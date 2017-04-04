---
title: Administrar tokens de seguridad en complementos de confianza baja hospedados por el proveedor para SharePoint
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# Administrar tokens de seguridad en complementos de confianza baja hospedados por el proveedor para SharePoint
Obtenga información sobre el contexto, el acceso y los tokens de actualización que las Complementos de SharePoint hospedadas por el proveedor y de confianza baja usan para autorización, y cómo trabajar con ello en el código.
> [!IMPORTANTE]
> **Este artículo está enteramente dedicado al uso de los tokens de seguridad en el sistema de autorización de confianza baja, no en el sistema de confianza alta.** Para obtener información sobre cómo usar tokens en el sistema de confianza alta, vea [Crear y usar tokens de acceso en complementos de SharePoint de elevada confianza hospedados por el proveedor](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md). 
  
    
    

 **Las Complementos de SharePoint que el  [sistema de autorización de confianza baja](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) para obtener acceso a los datos de SharePoint participan en unflujo de OAuth que implica pasar tokens de seguridad (con el formato de [token web de JSON](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)) entre SharePoint, Servicio de control de acceso (ACS) de Microsoft Azure, los componentes remotos de la Complemento de SharePoint y, en algunos casos, el explorador del usuario.** Hay diferentes flujos según el diseño del complemento, pero todos ellos implican al menos dos tipos de tokens:
- **Token de acceso:** se incluye en todas las solicitudes para crear, leer, actualizar o eliminar (CRUD) de los componentes remotos del complemento a SharePoint. SharePoint valida el token y atiende la solicitud.
    
  
- **Token de actualización:** se usa para obtener un primer token de acceso en el [flujo de tokens de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), y para reemplazar los tokens de acceso que expiren tanto en el flujo de tokens de contexto como en el  [flujo de código de autorización](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) del sistema de autorización de confianza baja.
    
  
Según el flujo de OAuth donde se esté usando el complemento, uno de los dos siguientes también forma parte del proceso:
- **Token de contexto:** se usa, en el flujo de contexto, para proporcionar al componente remoto un token de actualización y la información que necesita para solicitar a Azure ACS un token de acceso.
    
  
- **Código de autorización:** no es un token sino un código de autorización, único para cada par de usuario y aplicación. Se usa en el flujo de código de autorización para obtener un primer token de acceso y un token de actualización.
    
  

## Descripción de la administración de los tokens de acceso
<a name="AccessTokens"> </a>

En el sistema de autorización de confianza baja, Azure ACS crea los tokens de acceso y los envía al componente remoto de su Complemento de SharePoint. (Cuando se escribió este artículo, los tokens de acceso emitidos por ACS para SharePoint tenían una duración de 12 horas, pero eso podría cambiar). Lo **principal que el código de su Complemento de SharePoint tiene que hacer** es:
  
    
    

- **Solicitar un token de acceso** a Azure ACS. Según el flujo de OAuth en el que se esté usando, el complemento usa un código de autorización o la información que extrae de un token de contexto para realizar la solicitud.
    
  
- **Incluir el token en todas las solicitudes HTTP a SharePoint.** El token se agrega como un encabezado de **Authorization** a la solicitud. El valor del encabezado es la palabra "Bearer" seguida de un espacio y del token de acceso codificado en base 64.
    
  
- Opcionalmente (pero recomendado), **almacenar en caché el token de acceso** para reutilizarlo en siguientes solicitudes.
    
  
- Opcionalmente, reenviar el token de acceso a los sistemas de backend para que puedan acceder directamente a SharePoint.
    
  
Estas tareas deben realizarse con código del lado del servidor. Si usa código administrado, el código de muestra de algunas de estas tareas está en los archivos SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb) que forman parte de Microsoft Office Developer Tools para Visual Studio. Para ver un ejemplo del código PHP que realiza alguna de estas tareas, vea  [Descripción y uso de la interfaz REST de SharePoint 2013](http://msdn.microsoft.com/es-es/magazine/dn198245.aspx).
  
    
    

### Almacenar en caché el token de acceso
<a name="CacheAccessToken"> </a>

Según la arquitectura de su Complemento de SharePoint y de la plataforma de hospedaje, hay varias **maneras de almacenar en caché el token de acceso** en el servidor.
  
    
    

- En estado de sesión
    
  
- En estado de aplicación
    
  
- En el  [Servicio de almacenamiento en caché de Windows Server AppFabric](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx) o su equivalente en un sistema operativo que no sea de Microsoft.
    
  
- En el  [servicio Caché de Microsoft Azure](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx) o su equivalente en un servicio de nube que no sea de Microsoft.
    
  
- En una base de datos
    
  
- En un sistema  [memcached](http://www.memcached.org/)
    
  

> [!NOTA]
> En la mayoría de los casos, no podrá usar términos tan simples como "AccessToken" como clave de almacenamiento en caché porque su complemento debe mantener diferenciados los tokens de los distintos usuarios y granjas o inquilinos de SharePoint. Si su complemento usa el  [flujo de tokens de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), hay una **CacheKey** que SharePoint proporciona y que se puede usar para distinguir los tokens almacenados en caché. En esta sección se explica cuáles son los problemas y qué hacer cuando su aplicación no usa el flujo de tokens de contexto.
  
    
    

Almacenar el token de acceso en estado de sesión funciona bien en la mayoría de los casos. Si la aplicación web remota está accediendo a otros servicios que usan OAuth (además de SharePoint) y almacena en caché los diversos tokens de acceso en estado de sesión, asegúrese de usar claves de caché distintas para los tokens; por ejemplo, en lugar de "AccessToken", use "SharePoint_AccessToken", "Facebook_AccessToken", "SAP_Gateway_AccessToken", etc. (Si no usa el estado de sesión u otro almacenamiento en caché que separe automáticamente la caché de cada usuario, también tiene que relativizar las claves para el usuario).
  
    
    
Si la aplicación accede a más de una granja de servidores o de un inquilino en línea de SharePoint, puede usar el dominio de SharePoint como parte de la clave primaria de almacenamiento en caché de la aplicación ("SharePoint_ *<mydomain>*  .sharepoint.com_AccessToken") o use el dominio kerberos de la granja o el inquilino ("SharePoint_ *<realmGUID>*  _AccessToken"); ambos pueden leerse desde el token de acceso. (Su código necesita revertir la codificación en base 64 del token para leerlo. En el código administrado, el archivo TokenHelper.cs, o .vb, contiene código de muestra para este fin que usa clases de los espacios de nombres **Microsoft.IdentityModel.S2S.Tokens** y **System.IdentityModel.Tokens**). Hay otra opción disponible cuando la aplicación usa el flujo de token de contexto, tal y como se describe en el párrafo siguiente.
  
    
    
En algunos escenarios, su aplicación tendrá que almacenar en caché el token de acceso en algún lugar que esté disponible para la aplicación entre sesiones o después de que una sesión finalice. Por ejemplo, la aplicación puede diseñarse para que los usuarios puedan programar acciones que se producirán después de que el usuario haya cerrado la aplicación. Si entre esas acciones se incluye acceder a SharePoint, el complemento tendrá que recuperar el token de acceso. En este tipo de escenario, **su aplicación debe mantener diferenciados los tokens de los diferentes usuarios**. Si usa el flujo de tokens de contexto, se proporciona una cadena de clave de caché para este fin en el token de contexto que SharePoint pasa al componente remoto de su Complemento de SharePoint cuando se lanza el complemento. Para obtener más información sobre esta **clave de caché especial** y cómo usarla, vea [Descripción de la clave de caché](#CacheKey). También puede usar esta cadena para el escenario descrito en el párrafo anterior. El sistema de programación tendrá algún medio para almacenar los datos de configuración que necesitará, por ejemplo, cuándo se ejecutará el trabajo programado y qué debe hacer. Use este almacenamiento para guardar la clave de caché para el token de acceso.
  
    
    
Si la caché entre sesiones que usa también está compartida entre varias aplicaciones, las claves de caché tendrá que relativizarse para las aplicaciones, los usuarios y los dominios kerberos de SharePoint. La clave de caché que se proporciona en el token de contexto es única para las aplicaciones así como para los usuarios y el dominio kerberos de SharePoint.
  
    
    
 **Si su aplicación usa el flujo de código de autorización**, entonces no hay ningún token de contexto y, por lo tanto, no se crea especialmente ninguna clave de caché. En ese escenario, **tendrá que crear su propio sistema de claves para los datos en caché** que se relativizarán para uno o varios de los siguientes, según los posibles conflictos de nombres que se puedan dar en función de los casos de uso de su aplicación: el usuario, el dominio kerberos de SharePoint y la aplicación. Puede usar las notificaciones dentro del token de acceso con este fin; por ejemplo, **nameId** y **aud** (vea las tablas siguientes). Su código simplemente concatenará las cadenas o las usará como fuentes para crear un hash único que pueda servidor como clave de caché.
  
    
    
Por último, si su aplicación realiza llamadas solo de complemento o de usuario y complemento a SharePoint, tendrá dos tokens de acceso diferentes. Por lo tanto, necesitará claves de caché diferentes. Cuando se haya decidido por una clave de caché básica, simplemente anexe "_add-in-only" o "_add-in+user" a esta.
  
    
    

> [!PRECAUCIóN]
> **No es un procedimiento seguro almacenar el token de acceso en una cookie.** Normalmente se recomienda evitar que el token de acceso pase por el explorador.
  
    
    


### Reenviar el token de acceso a los sistemas backend
<a name="ForwardTokenToBackend"> </a>

Una Complemento de SharePoint puede tener servidores backend que no se hospedan en el mismo dominio que la aplicación web remota. Cuando un servidor backend necesita realizar operaciones CRUD en SharePoint, el complemento es más rápido si estas operaciones pueden ir directamente desde el sistema backend a SharePoint. Por suerte, el dominio solo es importante cuando su aplicación obtiene un token de acceso de ACS. Cuando tiene el token, puede reenviarlo a los servicios de backend, que pueden usarlo para llamar a SharePoint. En estos sistemas, el código debe controlar los tokens de acceso expirados y enviar una solicitud para uno nuevo a la aplicación web principal que realmente está registrada con ACS. Vea  [Controlar los tokens de acceso expirados](#Expired). Esta técnica solo se debe usar para los servidores backend propios de su aplicación, no para los servicios web externos. Además, si usa esta técnica, considere la posibilidad de diseñar los servicios de backend para usar llamadas solo de complemento siempre que sea posible.
  
    
    

### Controlar los tokens de acceso expirados
<a name="Expired"> </a>

Un token de acceso expira después de unas horas (doce horas en el momento de escribir este artículo, pero eso puede cambiar). Si la aplicación sigue accediendo a SharePoint después de que expire el token de acceso, la primera solicitud para SharePoint después de la expiración producirá un error **401 Unauthorized**. Su código tiene que controlar esta respuesta. Otra opción es que el código compruebe el tiempo de expiración del token de acceso antes de que se use. **Su código usa el token de actualización para obtener otro token de acceso de ACS.** En el flujo de tokens de contexto, el token de actualización se incluye en el token de contexto que el complemento recibe de SharePoint al comenzar su primera sesión con SharePoint. En el flujo de código de autorización, se pasa a la aplicación junto con el primer token de acceso. El código debe almacenarlo en caché para que esté disponible cuando sea necesario. El token de actualización dura unos meses y puede conservarse en una cookie o en un almacenamiento en el lado del servidor. Para obtener más información, vea [Descripción del control y almacenamiento en caché de tokens de actualización](#RefreshToken).
  
    
    

### Ejemplos de tokens de acceso
<a name="ExampleAccessTokens"> </a>

En esta sección se describen, con ejemplos, los tokens de acceso y se muestra cómo varían según la directiva de autorización que se use.
  
    
    

#### Tokens de acceso para el sistema de autorización de confianza baja

 **Directiva usuario+complemento**
  
    
    
El siguiente es un **ejemplo descodificado de un token de acceso de usuario+complemento generado por ACS** para usarse con llamadas a SharePoint mediante la [directiva de usuario+complemento](add-in-authorization-policy-types-in-sharepoint-2013.md). Se han agregado espacios en blanco para facilitar su lectura. El token cumple el protocolo de  [token web JSON](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1). El objeto Notación de objetos de JavaScript (JSON) en el token se llama conjunto de notificaciones. Vea la tabla 1 para obtener más información sobre las propiedades en el conjunto de notificaciones. Tenga en cuenta que todos los valores deben estar en minúsculas (los tokens de acceso de usuario+complemento son los mismos en el  [flujo de tokens de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) y en el [flujo de código de autorización](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)).
  
    
    



```

{
 "aud": "00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss": "00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf": 1377549246,
 "exp": 1377592446,
 "nameid": "2303000085ff9abc",
 "actor": "964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73",
 "identityprovider": "urn:federation:microsoftonline"
}
```


**Tabla 1: Notificaciones de tokens de acceso de usuario+complemento emitidos por ACS**


|**Notificación**|**Descripción**|**Valor correspondiente en el ejemplo de token de acceso**|
|:-----|:-----|:-----|
| `aud` <br/> |Audiencia, que indica la entidad a la que está dirigida el token. El formato es  _id. de entidad de audiencia_/ _dominio de SharePoint_@ _dominio kerberos de SharePoint_, donde  _id. de entidad de audiencia_ es un identificador de entidad de seguridad permanente para SharePoint. (Vea [Constantes de entidad de seguridad de la aplicación de productos Microsoft](http://msdn.microsoft.com/es-es/library/hh629982%28v=office.12%29.aspx)).  <br/> . _dominio kerberos de SharePoint_ es el GUID del inquilino de SharePoint Online, o la granja de servidores local de SharePoint, para acceder a los cuales se usa el token de acceso. Este GUID funciona como el id. de dominio kerberos del emisor del token; en este caso Azure ACS. <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |Emisor. Representa la entidad de seguridad que creó el token. El formato es  _GUID del emisor_@ _GUID de dominio kerberos de SharePoint_.  <br/> En el sistema de autorización de confianza baja, el emisor es Azure ACS y su GUID es **00000001-0000-0000-c000-000000000000**. <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |No antes de. Representa la hora en la que el token  *empieza*  a ser válido, en segundos contados desde medianoche del 1 de enero de 1970. De forma predeterminada, se establece en el momento en el que se creó. Vea [Trabajar con valores de tiempo de JWT](#JWTtimes) para obtener más información. <br/> |1377549246  <br/> |
| `exp` <br/> |Expiración. Representa el momento del tiempo en que expira el token. De forma predetermina, es 12 horas después de la hora **nbf**. Vea [Trabajar con valores de tiempo de JWT](#JWTtimes) para obtener más información. <br/> |1377592446  <br/> |
| `nameid` <br/> |Un identificador único para el usuario para el que se emitió el token. El formato varía en función del proveedor de identidad. En este ejemplo, el proveedor es Microsoft Online, pero si fuera Active Directory, el identificador tendría el siguiente aspecto "s-1-5-21-2127521184-1604012920-1887927527-415149".  <br/> |2303000085ff9abc  <br/> |
| `actor` <br/> |Entidad de seguridad que busca acceder a la granja de servidores o al inquilino de SharePoint. Tiene el formato  _id. de cliente de la aplicación_@ _dominio kerberos de SharePoint_.  <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `identityprovider` <br/> |El nombre único del proveedor de identidad tal y como se registró con la autoridad de asignación de números de Internet (IANA).  <br/> Para un complemento que está instalado en SharePoint Online, el valor suele ser el mismo que en este ejemplo.  <br/> Para un complemento que está instalado en una granja de servidores local, normalmente será un proveedor de identidad local, como "urn:office:idp:activedirectory".  <br/> |urn:federation:microsoftonline  <br/> |
   
 **Directiva solo de complemento**
  
    
    
El siguiente es un **ejemplo decodificado de un token de acceso solo de complemento generado por ACS** que se usará para llamadas a SharePoint mediante la [directiva solo de complemento](add-in-authorization-policy-types-in-sharepoint-2013.md). Se han agregado espacios en blanco para facilitar su lectura. El token cumple el protocolo de  [token web JSON](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1). Vea la tabla 2 para obtener más información sobre las propiedades en el conjunto de notificaciones (la directiva solo de complemento no está disponible para aplicaciones que usan el  [flujo de código de autorización](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), porque no tienen un archivo de manifiesto del complemento y, por lo tanto, no solicitan permiso para usar llamadas solo de complemento).
  
    
    



```

{
 "aud":"00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":1403304705,
 "exp":1403347905,
 "nameid":"c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73",
 "sub":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "oid":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "trustedfordelegation":"false",
 "identityprovider":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73"
}
```


**Tabla 2: Notificaciones de tokens de acceso solo de complemento emitidos por ACS**


|**Notificación**|**Descripción**|**Valor correspondiente en el ejemplo de token de acceso**|
|:-----|:-----|:-----|
| `aud` <br/> |Igual que el token de usuario+complemento anterior.  <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |Igual que el token de usuario+complemento anterior.  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |Igual que el token de usuario+complemento anterior.  <br/> |1403304705  <br/> |
| `exp` <br/> |Igual que el token de usuario+complemento anterior.  <br/> |1403347905  <br/> |
| `nameid` <br/> |Identificador único para el complemento, en lugar del usuario, porque la identidad del usuario no importa en la directiva solo de complemento. El formato es  _id. de cliente_@ _dominio kerberos de SharePoint_.  <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `sub` <br/> |Sujeto. Sujeto del token, que es la entidad de seguridad que quiere acceder a SharePoint; en este caso, la aplicación web remota. El id. de objeto se usa para el valor. Vea la notificación **oid** más adelante. <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `oid` <br/> |Id. de objeto. Es el id. de objeto en Azure Active Directory para la aplicación web remota. En un token de acceso solo de complemento, el sujeto y el id. de objeto son el mismo valor.  <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `trustedfordelegation` <br/> |Valor booleano que especifica si SharePoint debe confiar en la Complemento de SharePoint para autenticar y autorizar el usuario. Es FALSE en llamadas solo de complemento porque la identidad del usuario no importa.  <br/> |false  <br/> |
| `identityprovider` <br/> |Nombre único del proveedor de identidad. Como se está proporcionando la identidad del complemento y no del usuario, el proveedor de identidad es ACS. El formato es  _GUID de ACS_@ _dominio kerberos de SharePoint_.  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
   

## Descripción de la estructura y control de los tokens de contexto
<a name="ContextTokens"> </a>

Los tokens de contexto se usan solo en el  [flujo de tokens de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) del sistema de autorización de confianza baja. **Cuando se inicia la Complemento de SharePoint en SharePoint, SharePoint solicita que Azure ACS cree un token de contexto que SharePoint pasa después al componente remoto de la Complemento de SharePoint.** El token se pasa como un parámetro de formulario oculto llamado **SPAppToken**, en una solicitud de SharePoint para la página de inicio del componente remoto. El token está firmado con un secreto de cliente que solo ACS y la Complemento de SharePoint conocen. El **token de contexto incluye un token de actualización que el complemento usa**, junto con otra información del token de contexto, **para solicitar un token de acceso** a ACS. (Cuando se escribió este artículo, los tokens de contexto emitidos por ACS para SharePoint tenían una duración de 12 horas, pero eso podría cambiar). Las **principales tareas del código en la Complemento de SharePoint** son las siguientes:
  
    
    

- Usar el secreto de cliente del complemento para **validar el token de contexto**.
    
  
- **Almacenar en caché el token de contexto** o extraer, y almacenar en caché por separado, el token de actualización y algunos otros elementos que contiene.
    
  
- Usar el token de actualización y otra información para **solicitar un token de acceso** de ACS (que después se almacena también en caché).
    
  
- **Almacenar en caché CacheKey** a partir del token de contexto.
    
  

> [!IMPORTANTE]
> Las dos primeras tareas deben realizarse antes de que el usuario navegue a otra página o de que actualice la página, o de que se pierda el token. Por ejemplo, en una aplicación de formularios Web Forms ASP.NET, considere la posibilidad de realizar estas tareas en el método **Page_Load** (en un bloque de código condicional que se ejecute solo cuando la solicitud no sea un postback). En una aplicación MVC ASP.NET, considere la posibilidad de realizar estas tareas en el método de controlador predeterminado.
  
    
    

Si usa código administrado, el código de muestra de algunas de estas tareas está en los archivos SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb) que se incluyen en Microsoft Office Developer Tools para Visual Studio. Solo tiene que hacer llamadas simples a los miembros de la clase TokenHelper. Por ejemplo, su código puede realizar la primera tarea con la siguiente línea de código:
  
    
    



```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);
```

Para ver un ejemplo de cómo realizar algunas de estas tareas con PHP, consulte el código de ejemplo de  [SharePoint 2013 para realizar operaciones en la biblioteca de documentos de SharePoint desde un sitio PHP](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef).
  
    
    

### Almacenar el token de contexto o partes de él
<a name="CacheContextToken"> </a>

Puede **almacenar en caché** todo el token de contexto o tan solo el token de actualización y algunos otros elementos que contiene y que su código usa para obtener tokens de acceso, **en un almacenamiento del lado del servidor o del lado del cliente**. Para simplificar, en este artículo se da por hecho que almacenará en caché el token de contexto como una unidad.
  
    
    

> [!IMPORTANTE]
> Se lo recordamos una vez más porque es realmente importante: no use almacenamiento en caché en el lado del cliente para el token de  *acceso*  . Solo es seguro de usar para el token de contexto.
  
    
    

Tiene las mismas  [opciones de almacenamiento en caché del lado del servidor](#CacheAccessToken) que las indicadas anteriormente para el token de acceso. Las opciones del lado del cliente incluyen una cookie y un campo de formulario oculto en una página HTML. Otra opción es almacenar el token de contexto en la memoria caché de la sesión y almacenar en el cliente el valor de **CacheKey** obtenido de él.
  
    
    
Si su aplicación accede a SharePoint después de que finalice una sesión, ni el almacenamiento en caché de sesión ni el almacenamiento en caché en el lado del cliente son opciones, porque el token de actualización debe estar disponible para la aplicación en caso de que el token de acceso original haya expirado cuando se ejecuta el trabajo posterior a la sesión. En este escenario, necesita una memoria caché duradera (entre sesiones) compartida entre varios usuarios, dominios kerberos de SharePoint o aplicaciones. Por lo tanto, su código tiene que usar claves de caché que distingan el usuario, el dominio kerberos de SharePoint o la aplicación, tal y como se explicó anteriormente en  [Almacenar en caché el token de acceso](#CacheAccessToken). Puede **usar la clave de caché especial** que está dentro del token de contexto con este fin, igual que lo usó para el token de acceso. (Vea [Descripción de la clave de caché](#CacheKey), a continuación). 
  
    
    

#### Descripción de la clave de caché
<a name="CacheKey"> </a>

 **La clave de caché es una cadena opaca única para la combinación de usuario, emisor del nombre de usuario, complemento y granja de servidores de SharePoint o inquilino de SharePoint Online.** Antes de cifrarla, tiene el formato siguiente, donde _DominioKerberos_ es el GUID de la granja de servidores local de SharePoint o el inquilino de SharePoint Online.
  
    
    
 _IdNombreDeUsuario_ + "," + _EmisorIdNombreDeUsuario_ + "," + _IdDeAplicación_ + "," + _DominioKerberos_
  
    
    
La clave de caché no contiene información sobre la dirección URL del sitio. Cada inquilino de SharePoint Online (o granja de servidores local de SharePoint) tiene un dominio kerberos único, por lo que la clave de caché es única para cada combinación de nombre de usuario, emisor del nombre de usuario, aplicación y granja de servidores. En el siguiente ejemplo de token de contexto, el valor de la clave de caché cifrada es:
  
    
    
 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`
  
    
    
Como su aplicación podría almacenar en caché varios elementos, como el token de acceso y el token de contexto en la misma memoria caché con la misma clave de caché, **considere la posibilidad de usar la clave de caché como un tronco** al que anexa o antepone una cadena específica, como "AccessToken" o "ContextToken", según sea necesario para formar una clave de caché completa. **Otra opción es crear una clase** con propiedades para las diferentes cosas que quiera almacenar en caché y, después, almacenar en caché un objeto de ese tipo. Una **tercera opción**, si **usa una base de datos** como memoria caché, es usar una tabla con una columna CacheKey y columnas adicionales para los elementos almacenados en caché (AccessToken, ContextToken, etc.).
  
    
    
Por supuesto, su aplicación no tiene que usar la misma memoria caché para todo lo que almacena en caché. Un modelo habitual es almacenar el token de acceso en la memoria caché de sesión, el token de contexto (o el token de actualización incluido en él) en una base de datos y el valor de CacheKey en una cookie.
  
    
    

### Usar el token de contexto para obtener un token de contexto
<a name="UseContextTokenToGetAccessToken"> </a>

 **Para obtener un token de contexto, su aplicación envía una solicitud directamente a ACS.** La solicitud incluye tres partes de información que se extraen del token de contexto (y otra información):
  
    
    

- El token de actualización
    
  
- El GUID de entidad de seguridad de la aplicación de SharePoint.
    
  
- El GUID de dominio kerberos de la granja de servidores de SharePoint o del inquilino de SharePoint Online a los que el complemento quiere acceder.
    
  
El archivo TokenHelper.cs (o .vb) contiene código que crea esta solicitud. Para ver un ejemplo de código PHP que hace esto, vea  [SharePoint 2013: realizar operaciones en la biblioteca de documentos de SharePoint desde un sitio PHP](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454).
  
    
    
La aplicación puede obtener el dominio kerberos del inquilino o de la granja de servidores de SharePoint en tiempo de ejecución como alternativa a tener que analizarlo en el token de contexto. Si usa código administrado, hay un método  `TokenHelper.GetRealmFromTargetUrl` para obtener el dominio kerberos. Asegúrese de almacenar en caché el valor para que su código no realice otra llamada de red para volver a obtenerlo.
  
    
    

### Obtener un nuevo token de contexto
<a name="GetNewContextToken"> </a>

 **Si necesita un nuevo token de contexto**, normalmente porque el token de actualización (que está incluido en los tokens de contexto) ha expirado, **su código puede obtener uno nuevo redireccionando el explorador a una página especial en todos los sitios web de SharePoint**: AppRedirect.aspx. Hay que adjuntar dos parámetros de consulta a la dirección URL de esta página:
  
    
    

-  `client_id`: id. de cliente de su Complemento de SharePoint.
    
  
-  `redirect_uri`: URI al que quiere redirigir el explorador después de obtener el nuevo token de contexto. SharePoint publicará el token de contexto en este URI. Suele ser la misma página, método de controlador o método de servicio web que solicitó el nuevo token de contexto. El valor debe estar codificado como URL.
    
  
A continuación se muestra la estructura de la dirección URL:
  
    
    



```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>
```

El siguiente es un ejemplo de cómo crear la solicitud en ASP.NET usando el archivo TokenHelper:
  
    
    



```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));
```


### Ejemplo de un token de contexto
<a name="ExampleContextToken"> </a>

A continuación se muestra un ejemplo de token de contexto. El objeto Notación de objetos de JavaScript (JSON) pequeño en la parte superior contiene metadatos acerca del token. Estas propiedades son las mismas que en los tokens de acceso (vea más arriba). El valor de la propiedad **alg** es el nombre del algoritmo que se usa para generar la firma que ACS anexa al token. Vea la tabla 3 para obtener información sobre las propiedades en la carga del token. Tenga en cuenta que todos los valores deben estar en minúsculas. Se han agregado espacios en blanco para facilitar su lectura.
  
    
    

```
{"typ":"JWT","alg":"HS256"}
.
{
 "aud":"a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":"1335822895",
 "exp":"1335866095",
 "appctxsender":"00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "appctx":"{
            \\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\",
            \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"
           }",
 "refreshtoken":"IAAAAC1Lv5w0OrcFAmJx0xk6aaBdhgsw3VPnPzNEDAWypTHtCYytZ2/dBBUKj+HLK8YB3IUCUfDxYpAque
NHKtgs4rYJJ5AegQpNMOJR1yYK8ngivQx0oetj7aSPuGVb+k6at6G0Kx5LZ5vhxkAq8iUSwu8p4L2cvNMzDF1mDKfMivqxgrIZkr2nbf9as0SJFL6VG5hZnDE4HKq
xJnejSW3umatKM4fsfY1MClVCxrkXb2EQ8H/TmwaJc388YW063GEVUS/3BTSgSIRBKQUmXJuJ6BZY7WTm84LaGrx3mIjnUTM/jnqPoPG55JbCC9sS/MeGNPtzPPCDg
6Vv7dVhQ1Dq5Y3fQ65e9LpJ580jCgzYYvpIFT+Wx5V+17mjY2T8wug04K2ts87Znsr+GfFCorf7NS/lj5HjoxRAQ2tva/8dwguSLwxcUwi/Q9MbpR0NNtlpwVazqi9O
hJ4Df7gVhUDdJ0Dtc6aFCPbl5ZLDDRs42xK2", 
 "isbrowserhostedapp": "true"
}
```

Las notificaciones **aud**, **iss**, **nbf** y **exp** son exactamente las mismas que en un token de acceso, tal y como se describió anteriormente. Las notificaciones **appctxsender**, **appctx**, **CacheKey**, **SecurityTokenServiceUri**, **refreshtoken** y **isbrowserhostedapp** se describen en la tabla siguiente.
  
    
    

**Tabla 3. Notificaciones e información de un token de contexto**


|****Notificación****|****Descripción****|****Valor correspondiente en ejemplo de token de contexto****|
|:-----|:-----|:-----|
|aud  <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|iss  <br/> ||00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|nbf  <br/> ||1335822895  <br/> |
|exp  <br/> ||1335866095  <br/> |
|appctxsender  <br/> |Remitente del contexto de aplicación. Representa la aplicación que envió el token de contexto a la Complemento de SharePoint.  <br/> Tiene el formato  _GUID de la entidad de seguridad_@ _dominio kerberos de SharePoint_, donde  _GUID de la entidad de seguridad_ es el id. de constante de la entidad de seguridad de la aplicación; puede ser SharePoint, Exchange 2013, Lync 2013 o Workflow. <br/> |00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|appctx  <br/> |Contexto del complemento. Es un objeto JSON que contiene los valores de **CacheKey** y **SecurityTokenServiceURI**. <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"  <br/> |
|CacheKey  <br/> |Valor único que se puede usar como clave en una memoria caché estructurada de claves y valores para almacenar y recuperar el token de contexto. También se podría usar como valor de una columna de clave en una fila de una base de datos.  <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=  <br/> |
|SecurityTokenServiceURI  <br/> |URI del servicio emisor de tokens.  <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2  <br/> |
|refreshtoken  <br/> |Token de actualización del complemento.  <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6…  <br/> |
|isbrowserhostedapp  <br/> |Campo **Boolean** que especifica si la solicitud al complemento que contiene el token de contexto procede de un explorador (true) o de un receptor de eventos remotos (false). <br/> |true  <br/> |
   

### Usar el token de contexto para limitar el acceso solo a usuarios de SharePoint
<a name="UseContextTokenToLimitAccess"> </a>

Si quiere limitar el acceso a su componente remoto, como un servicio WCF, a los usuarios de SharePoint, su código puede validar el token de contexto en la solicitud HTTP. (Si usa código administrado, puede llamar a **TokenHelper.ReadAndValidateContextToken()**). Su código puede comprobar que la notificación actor del token comienza por **00000003-0000-0ff1-ce00-000000000000**, si quiere asegurarse de que es SharePoint (y no Exchange 2013, Lync 2013 o Workflow, por ejemplo). Si quiere realizar una validación adicional que requiere una devolución de llamada a SharePoint, por ejemplo, para limitar el acceso a usuarios con un determinado rol en SharePoint, puede almacenar en caché el hecho de que ha realizado esta validación para un usuario determinado (usando el valor de **CacheKey** del token de contexto) para que solo tenga que hacerlo una vez.
  
    
    

## Descripción del control y almacenamiento en caché de tokens de actualización
<a name="RefreshTokens"> </a>

 **Los tokens de actualización se incluyen en el token de contexto que SharePoint publica en su aplicación web cuando se inicia.** El token de actualización es un token cifrado que su Complemento de SharePoint no puede descifrar. **Su código lo usa**, además de otra información, **para obtener un nuevo token de acceso cuando el token de acceso actual expira**. También se usa para obtener el *primer*  token de acceso del [flujo de tokens de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows). (Cuando se escribió este artículo, los tokens de actualización emitidos por ACS para SharePoint tenían una duración de 6 meses, pero eso podría cambiar).
  
    
    
Como un token de acceso dura horas (normalmente 12) y un usuario final obtiene uno nuevo cada vez que inicia su aplicación Complemento de SharePoint desde SharePoint, solo necesita el token de actualización en uno de estos escenarios:
  
    
    

- Los usuarios mantienen sesiones largas en su complemento en las que el complemento realiza llamadas a SharePoint muchas horas después de que se inicie (actualmente más de 12).
    
  
- El diseño del complemento permite a los usuarios programarlo para que acceda a SharePoint algún tiempo después de que finalice la sesión.
    
  
Ambos **escenarios requieren que su complemento almacene en caché el token de actualización**, y el segundo escenario requiere una memoria caché en el lado del servidor que dure de una sesión a otra. Las opciones de almacenamiento en caché son las mismas que para el [token de acceso](#CacheAccessToken) y, en el flujo de tokens de contexto, puede usar [la clave de caché en el token de contexto](#CacheKey). (En el flujo de tokens de contexto, normalmente solo se almacena en caché el token de contexto. Contiene el token de actualización y otra información que necesita para obtener el nuevo token de acceso. En el  [flujo de código de autorización](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), no hay ningún token de contexto, por lo que se almacena en caché el propio token de actualización).
  
    
    
Si está almacenando en caché el token de actualización en un almacenamiento que persiste entre las sesiones de su complemento de un usuario específico, asegúrese de reemplazarlo por el token de actualización más reciente. Tanto en el flujo hospedado en la nube como en el flujo de código de autorización, el usuario obtiene un nuevo token de actualización cada vez que inicia el complemento.
  
    
    
Si el token de actualización expira y se solicita a ACS un nuevo token de acceso, se producirá un error **401 Unauthorized**. Su complemento deberá responder a este error obteniendo un nuevo token de actualización y usarlo para obtener un nuevo token de acceso. (Como el token de actualización está cifrado, su código no puede comprobar la expiración antes de usarlo). En el flujo de tokens de contexto, su complemento obtiene un nuevo token de actualización  [obteniendo un nuevo token de contexto](#GetNewContextToken). En el flujo de código de autorización, su complemento obtiene un nuevo token de actualización reiniciando el flujo. Específicamente, su código debe responder al error redirigiendo al usuario a la página SharePoint OAuthAuthorize.aspx, tal y como se explica en  [Flujo de OAuth para complementos que piden permisos sobre la marcha](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow).
  
    
    

## Descripción de la administración de los códigos de autorización
<a name="Authcodes"> </a>

 **En el flujo de código de autorización, el proceso de autorización comienza con un código de autorización que ACS emite, a petición de SharePoint, y que SharePoint pasa después a la aplicación remota** como un parámetro de consulta. El código de autorización es único para cada par de usuario y aplicación remota. (Cuando se escribió este artículo, los códigos de autorización emitidos por ACS para SharePoint tenían una duración de 5 minutos, pero eso podría cambiar). La lógica de **su aplicación debe obtener el código de autorización del parámetro de consulta y usarlo en una solicitud de un token de acceso a ACS**. Si usa código administrado, el código de muestra para crear el token está en el archivo TokenHelper.cs (y .vb). El código de muestra para leer el parámetro de consulta está en [Muestra de código subyacente de una página que accede a SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default). ACS invalida el código de autorización inmediatamente después de emitir el token de acceso, por lo que solo se puede usar una vez y no hay manera de almacenarlo en caché.
  
    
    

## Trabajar con valores de tiempo de JWT
<a name="JWTtimes"> </a>

Las notificaciones **nbf** y **exp** tienen el formato establecido por la [especificación JWT](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl). Están escritos como el número de segundos desde el 1 de enero de 1970. En C#, puede traducir estos valores con el código siguiente, donde  _jWTTimeStamp_ es el valor del token, como 1335822895.
  
    
    

```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);

```


## Solucionar problemas de control de tokens
<a name="Troubleshooting"> </a>

La  [herramienta Fiddler](http://www.telerik.com/fiddler) gratuita se puede usar para capturar las solicitudes HTTP enviadas por el componente remoto de su complemento a SharePoint. Hay una [extensión gratuita para la herramienta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automáticamente los tokens en las solicitudes.
  
    
    

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Crear complementos de SharePoint que usen autorización de baja confianza](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
- Para obtener muestras de código que usan código administrado y TokenHelper, vea  [SharePoint 2013: complemento remoto Hello World con CSOM](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf) y [Paquete de muestra de complementos para SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
- Para ver un ejemplo de código que usa llamadas a REST desde un complemento PHP, consulte  [SharePoint 2013: realizar operaciones en la biblioteca de documentos de SharePoint desde un sitio PHP](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef).
    
  

