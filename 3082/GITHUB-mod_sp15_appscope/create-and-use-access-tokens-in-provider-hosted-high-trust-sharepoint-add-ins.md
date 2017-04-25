---
title: Crear y usar tokens de acceso en complementos de SharePoint de elevada confianza hospedados por el proveedor
ms.prod: SHAREPOINT
ms.assetid: cb3264d4-41a6-498f-a408-75b077566051
---


# Crear y usar tokens de acceso en complementos de SharePoint de elevada confianza hospedados por el proveedor
Conozca el rol de los tokens de acceso en los complementos de elevada confianza para SharePoint y cómo su código trabaja con ellos.
> **IMPORTANTE**
> **Este artículo está enteramente dedicado al uso de los tokens de acceso en el sistema de autorización de elevada confianza, no en el sistema de ACS.** Para más información sobre cómo usar tokens de seguridad en el sistema de ACS, vea [Administrar tokens de seguridad en complementos de confianza baja hospedados por el proveedor para SharePoint](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md). 




Los **Complementos de SharePoint que usan el  [sistema de autorización de elevada confianza](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) para obtener acceso a SharePoint tienen que pasar un token de acceso** (con el formato de [token web JSON](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)) a SharePoint con cada solicitud de creación, lectura, actualización o eliminación (CRUD). SharePoint valida el token y atiende la solicitud. En este artículo se proporciona información sobre cómo el código crea y pasa el token de acceso.
En el sistema de autorización de elevada confianza, **el componente remoto de su Complemento de SharePoint crea el token de acceso**. Si el componente remoto usa código administrado para el código del lado del servidor, la mayor parte del trabajo de codificación para crear los tokens se hace automáticamente en los archivos SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb) que se incluyen en Office Developer Tools para Visual Studio. Como los clientes de un Complemento de SharePoint de elevada confianza tienen una implementación local de SharePoint, probablemente no tendrán problemas en usar ASP.NET, IIS y Windows Server como pila de hospedaje para el componente remoto. Debe considerar la posibilidad de usar esta pila porque los dos archivos generados le ahorrarán mucho trabajo de codificación y pruebas. Si el componente remoto debe usar un lenguaje que no sea .NET, y tanto el componente remoto como la granja de servidores de SharePoint están conectados a Internet, debe considerar la posibilidad de usar el [sistema de autorización de confianza baja](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) en lugar de la elevada confianza. En el sistema Servicio de control de acceso (ACS) de Microsoft Azure, toda la construcción de los tokens la realiza ACS, por lo que también se ahorra mucho trabajo. El resto de este artículo está dedicado a proporcionar orientación a los desarrolladores que crean Complementos de SharePoint con componentes remotos que no son .NET y que usan el sistema de autorización de elevada confianza. También proporciona información valiosa para depurar Complementos de SharePoint basados en .NET que usan el sistema de elevada confianza.





## Descripción de la administración de los tokens de acceso
<a name="AccessTokens"> </a>


> **NOTA**
>  Al leer este artículo, especialmente acerca de las tareas que su código debe realizar, recuerde que si usa código administrado, Microsoft Office Developer Tools para Visual Studio agregan a todos los proyectos de Complemento de SharePoint dos archivos de código generado, SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb) que hacen la mayor parte de estas tareas automáticamente. El código de administración de tokens de su aplicación suele consistir en unas cuantas llamadas a las clases en estos archivos. El objetivo de este tema es ayudar a los desarrolladores que no usan código administrado (y a los que tratan de solucionar problemas de tokens).>  Encontrará vínculos a bibliotecas de OAuth para diferentes lenguajes y plataformas aquí:>  [OAuth 2.0](http://oauth.net/2/) Vaya a **Bibliotecas de cliente**.>  En [github](https://github.com/), busque "OAuth 2" y "token web JSON" (sin comillas). 




Lo **principal que su código tiene que hacer** es:




1. **Cree un token de acceso.** Las tareas secundarias para crear este token varían según si la aplicación web remota realiza llamadassolo de complemento a SharePoint, llamadas deusuario+complemento o ambas. (Para más información sobre los dos tipos de llamadas, vea [Tipos de directivas de autorización de complementos en SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)). 

    Si el complemento realiza llamadas de usuario+complemento, la creación del token de acceso incluye las siguientes tareas secundarias:

1. Cree un token de actor que identifique el Complemento de SharePoint e indique a SharePoint que delegue la autenticación y autorización de usuarios a su complemento, y la codifique en base 64. Encontrará información sobre las notificaciones y la estructura del token de actor en la tabla 2 siguiente. Encontrará información sobre cómo codificar y firmar el token más adelante en [Codificar y firmar tokens](#EncodingTokens).


2. Firmar el token de actor con las credenciales de un certificado x509 que un administrador de la granja de servidores de SharePoint haya configurado como de confianza para SharePoint.


3. Incluir el token de actor en el token de acceso.


4. Agregar otras notificaciones necesarias al token de acceso. Encontrará información sobre las notificaciones y la estructura del token en la tabla 1 siguiente.


5. Codificar el token de acceso en Base 64. Encontrará información sobre cómo codificar y firmar el token en  [Codificar y firmar tokens](#EncodingTokens) más adelante.



    Si el complemento realiza llamadas solo de complemento, su código solo necesita hacer las dos primeras de estas tareas secundarias. El token de actor sirve como token de acceso.

    Si el complemento hace algunas llamadas de usuario+complemento y algunas llamadas de solo complemento, debe crear un token de actor simple para las llamadas de solo complemento y un token de acceso anidado mayor para las llamadas de usuario+complemento. No se puede usar el mismo token para ambas.


2. **Incluir el token de acceso en todas las solicitudes HTTP a SharePoint.** El token se agrega como un encabezado de **Authorization** a la solicitud. El valor del encabezado es la palabra "Bearer" seguida de un espacio y del token de acceso codificado en Base 64.


3. Opcionalmente, **almacenar en caché el token de acceso** para reutilizarlo en siguientes solicitudes.


Estas tareas deben realizarse con código del lado del servidor. Si usa código administrado, el código de muestra de algunas de estas tareas está en los archivos SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb) que Microsoft Office Developer Tools para Visual Studio genera.




### Almacenar en caché el token de acceso
<a name="CacheAccessToken"> </a>

Después de crear un token, se puede reutilizar en llamadas posteriores a SharePoint hasta que expire. Según la arquitectura del componente remoto y de la plataforma de hospedaje, hay varias **maneras de almacenar en caché el token de acceso** en el servidor.




- En estado de sesión


- En estado de aplicación


- En el  [Servicio de almacenamiento en caché de Windows Server AppFabric](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx).


- En una base de datos


- En un sistema  [memcached](http://www.memcached.org/)


Si el almacenamiento en caché se comparte entre diferentes sesiones de usuario, como la caché de la aplicación, asegúrese de usar una clave de caché que sea única para la sesión. Si la caché se comparte entre varias aplicaciones, el código también debe **relativizar la clave de caché** para esa variable. También es posible que su complemento acceda a diferentes granjas de servidores de SharePoint. Necesitará tokens de acceso diferenciados para cada una de ellas de modo que, en ese escenario, su clave de caché debería relativizarse para la granja de servidores. Además, quizás necesite claves de caché según uno o varios identificadores de usuario, identificadores de aplicación y dominio o dominio kerberos de SharePoint. Considere la posibilidad de usar un sistema de claves de caché similar al de los Complementos de SharePoint que utilizan el sistema de autorización de baja confianza. Para más información, vea [Descripción de la clave de caché](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey). Básicamente, se concatenan uno o varios de estos tres identificadores (y opcionalmente se aplica un algoritmo hash al resultado para obtener una cadena más corta) para que actúen como clave de caché. 




### Controlar los tokens de acceso expirados
<a name="CacheAccessToken"> </a>

 **El token de acceso tiene un tiempo de expiración** que el código puede establecer en cualquier valor que usted quiera. Si su complemento construye un nuevo token de acceso para cada solicitud, el token solo tiene que durar lo suficiente para que SharePoint pueda validarlo (no más de unos segundos, a menos que la red LAN del cliente esté saturada). Podría establecer la expiración en años, pero incluso en el escenario "todo local" para el que se diseñan los complementos de elevada confianza, existe el riesgo de que se roben los tokens de acceso. Por lo tanto, considere establecer la expiración en no más de unas horas. (Si trabaja con código administrado, la muestra de creación de tokens del archivo TokenHelper.cs [o .vb] establece la expiración en 12 horas).



Si la sesión de un usuario con su Complemento de SharePoint dura más que la duración del token de acceso almacenado en caché, la primera solicitud a SharePoint después de que expire el token producirá un error **401 Unauthorized**. Su código tiene que administrar esta respuesta. También puede comprobar el tiempo de expiración del token de acceso antes de usarlo. **Su código debe responder a un token de acceso expirado creando un nuevo token de acceso** y repitiendo la solicitud errónea.




## Descripción de la estructura de los tokens de acceso
<a name="Structure"> </a>

El siguiente es un **ejemplo de un token de acceso generado por un Complemento de SharePoint** de elevada confianza; concretamente, este token lo generó el código de muestra del archivo TokenHelper.cs (o .vb) que forma parte de la plantilla de proyecto de Complemento de SharePoint creada por Office Developer Tools para Visual Studio en Visual Studio 2013 Update 2. El token se ha decodificado y se han agregado espacios en blanco para facilitar su lectura. Los tokens de acceso usados en el sistema de elevada confianza cumplen el [[MS-SPS2SAUTH]: OAuth 2.0 Authentication Protocol: SharePoint Profile](http://msdn.microsoft.com/library/05f9759b-0fa4-45ff-bd4b-0d7d254e7010.aspx), también llamado protocolo de servidor a servidor o S2S. Esta información se proporciona para ayudar a los desarrolladores que usan código administrado a depurar Complementos de SharePoint de elevada confianza, y a proporcionar a los desarrolladores que usan otros lenguajes orientación sobre cómo construir los tokens.



 **Este token de acceso se genera si el complemento realiza una llamada a SharePoint mediante la  [directiva de usuario+complemento](add-in-authorization-policy-types-in-sharepoint-2013.md).** Si el complemento usa la [directiva de solo complemento](add-in-authorization-policy-types-in-sharepoint-2013.md) y realiza una llamada de solo complemento a SharePoint, el token de actor (que es un token secundario dentro del token de acceso del usuario+complemento, descrito a continuación), se convierte en el token de acceso (y no hay un token principal).




> **NOTA**
>  Tenga en cuenta que los tokens de acceso de elevada confianza que su código crea son diferentes de los que Azure ACS crea cuando se usa el sistema de autorización de confianza baja:>  La notificación **alg** en el encabezado es "none" porque el token de acceso de una llamada a usuario+complemento desde un complemento de elevada confianza no está firmado.>  La dirección URL del complemento en el valor **aud** de este ejemplo es un servidor local, que es lo normal para el sistema de elevada confianza.>  No hay ninguna notificación **identityprovider**, pero hay una **nii** (emisor de identidad de nombre) con el mismo tipo de valores que los tokens de acceso de la notificación **identityprovider** usados en el sistema de autorización de confianza baja. (Para obtener más información sobre este valor cuando el proveedor de identidad está basado en SAML, vea las publicaciones en el blog de Steve Peschka [Seguridad en complementos de SharePoint: parte 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx) y [Usar complementos de SharePoint con sitios SAML y FBA en SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)). >  No hay ninguna notificación **actor**, pero hay una notificación **actortoken** que contiene un token interno codificado en base 64 con una duración de 12 horas.




El encabezado tiene dos propiedades. "typ" es el tipo de token. El código de la aplicación web remota debe establecer siempre este valor en "JWT". "alg" es el algoritmo usado para firmar el token. Como el token externo en una llamada a usuario+complemento desde un complemento de elevada confianza no está firmado, establezca este valor en "none". En la tabla 1 encontrará información sobre los valores en el cuerpo del token de acceso de elevada confianza.





```

{"typ":"JWT", "alg":"none"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"s-1-5-21-2127521184-1604012920-1887927527-2963467",
 "nii":"urn:office:idp:activedirectory",
 "actortoken":"6sMZhbw … [remainder of long base 64 string omitted] … "
}
```

La tabla siguiente proporciona orientación sobre las **propiedades que su código debe incluir en el token de acceso y qué valores establecer para ellas**. Si usa código administrado, los archivos SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb) crean los tokens automáticamente. Por ejemplo, su código realiza una sola llamada al método **SharePointContext.CreateUserClientContextForSPHost**. A su vez, este llama a los métodos de la clase **TokenHelper** que construyen el token de acceso que después se incluye en todas las llamadas que se realizan a SharePoint mediante el objeto de contexto de cliente de SharePoint que **SharePointContext.CreateUserClientContextForSPHost** devuelve.




**Tabla 1: Notificaciones de tokens de acceso emitidos por ACS**


|**Notificación**|**Descripción**|**Valor correspondiente en el ejemplo de token de acceso**|
|:-----|:-----|:-----|
| `aud` <br/> |Audiencia, que indica la entidad a la que está dirigida el token. El formato es  _identificador de entidad de audiencia_/ _nombre de dominio completo de SharePoint_@ _dominio kerberos de SharePoint_.  <br/> La entidad de audiencia para un Complemento de SharePoint siempre es 00000003-0000-0ff1-ce00-000000000000.  <br/> Como los Complementos de SharePoint de elevada confianza suelen usarse en escenarios totalmente locales, el domino de dominio completo de SharePoint suele ser un nombre de servidor.  <br/> El  _dominio kerberos de SharePoint_ es el GUID de la granja de servidores local de SharePoint para acceder a la cual se usa el token de acceso (o el GUID del inquilino, si la granja se ha configurado para inquilinos). <br/> Para buscar el dominio kerberos de SharePoint, ejecute el cmdlet **Get-SPAuthenticationRealm** de PowerShell en el servidor de SharePoint. Después, úselo directamente en el código o guárdelo en un archivo de configuración donde su código pueda leerlo, por ejemplo, la sección app.Settings de un archivo web.config. Su código también puede detectar automáticamente el dominio kerberos de SharePoint, enviando un desafío de autenticación a SharePoint. Vea el método `GetRealmFromTargetUrl` en el archivo TokenHelper.cs (o .vb) para obtener un ejemplo de cómo se hace esto en código administrado. <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |Emisor. Representa la entidad de seguridad que creó el token. El formato es  _GUID del emisor_@ _GUID de dominio kerberos de SharePoint_.  <br/> En el sistema de elevada confianza, el emisor es el propio complemento, por lo que se usa el identificador de cliente del Complemento de SharePoint como GUID de emisor.  *Todas las letras del identificador de emisor deben estar en minúsculas.*  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |No antes de. Representa la hora a la que el token  *comienza*  a ser válido, expresada en segundos contando desde la medianoche del 1 de enero de 1970. Su código debe establecer su valor en el momento en que se crea el token. <br/> |1403212820  <br/> |
| `exp` <br/> |Abreviatura de "expiración". Representa la hora a la que expira el token, en segundos desde la medianoche del 1 de enero de 1970.  <br/> |1403256020  <br/> |
| `nameid` <br/> |Un identificador único para el usuario para el que se emitió el token. El formato varía en función del proveedor de identidad. En este ejemplo, el proveedor es Active Directory.  <br/> |s-1-5-21-2127521184-1604012920-1887927527-2963467  <br/> |
| `nii` <br/> |Emisor del identificador de nombre. El nombre único del proveedor de identidad tal y como se registró con la autoridad de asignación de números de Internet (IANA).  <br/> Para un Complemento de SharePoint de elevada confianza, normalmente es un proveedor de identidad local, como Active Directory en este ejemplo.  <br/> |urn:office:idp:activedirectory  <br/> |
| `actortoken` <br/> |Token JWT codificado en base 64 que identifica el Complemento de SharePoint e indica a SharePoint que confíe en el complemento independientemente del usuario que esté ejecutándolo.  <br/> |Vea más adelante.  <br/> |
 
 **A continuación se muestra el **actortoken** decodificado.** El pequeño objeto de encabezado Notación de objetos de JavaScript (JSON) en la parte superior contiene los metadatos sobre el token, incluido el tipo de token y el algoritmo que se usa para firmarlo. La propiedad **x5t** del encabezado es un resumen de la huella del certificado x.509 que oficialmente es el emisor del token. Para construir este valor, el código hace lo siguiente:




1. Obtiene la versión de matriz de bytes (no cadena) de la huella del certificado. Este es un resumen SHA-1 del certificado. (En código administrado, se puede hacer con el método  [GetCertHash()](http://msdn2.microsoft.com/ES-ES/library/4f9acc3f). Necesitará algo equivalente en el lenguaje que esté usando).


2. Codifique la matriz de bytes con codificación de URL en base 64.


3. Establezca el valor de la propiedad **x5t** en el resumen codificado.


En la tabla 2 se describen las notificaciones que su código debe incluir en el cuerpo del token y los valores de las mismas.





```

{"typ":"JWT","alg":"RS256","x5t":"7MjK99QvkVdwz6UrKldx8AG7ydM"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "trustedfordelegation":"true"
}
```


> **NOTA**
> Si el complemento de elevada confianza usa la  [directiva solo de complemento](add-in-authorization-policy-types-in-sharepoint-2013.md) y realiza una llamada solo de complemento a SharePoint, el token mostrado aquí es en realidad el token de acceso. No hay ningún token externo. Además, no hay ninguna notificación **trustedfordelegation**, porque los permisos del usuario son irrelevantes en una llamada solo de complemento. 





**Tabla 2: Notificaciones de token de actor emitidas por el certificado**


|**Notificación**|**Descripción**|**Valor correspondiente en el ejemplo de token de acceso**|
|:-----|:-----|:-----|
| `aud` <br/> |Igual que en el token de acceso primario.  <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |El mismo significado que en el token de acceso primario, pero el GUID del emisor no es el identificador de cliente de la aplicación web. Es el GUID del certificado. Aunque el código de la aplicación construye el token de actor, el certificado se considera el emisor del token de actor.  <br/> Tenga en cuenta que, en este ejemplo, el GUID del emisor es una cadena GUID fácil de recordar que el administrador de la granja de servidores usó cuando registró el certificado x.509 como emisor de tokens de confianza en SharePoint. Esto es habitual cuando se usa el mismo certificado como emisor de tokens de actor para todas los Complementos de SharePoint de elevada confianza en la granja de servidores. Un administrador también puede elegir tener distintos certificados para cada Complemento de SharePoint. En ese caso, usaría GUID generados aleatoriamente para los certificados.  *Todas las letras del GUID del emisor deben estar en minúsculas.*  <br/> |11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |El mismo significado que en el token de acceso primario.  <br/> |El mismo valor que el token de acceso primario.  <br/> |
| `exp` <br/> |El mismo significado que en el token de acceso primario.  <br/> |El mismo valor que el token de acceso primario.  <br/> |
| `nameid` <br/> |Un identificador único para el Complemento de SharePoint, porque es el "actor" en el sistema de elevada confianza. El formato es  _id._cliente_@ _dominio_kerberos_SharePoint_ <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `trustedfordelegation` <br/> |Valor booleano que especifica si SharePoint debe confiar en el Complemento de SharePoint para autenticar y autorizar el usuario. Normalmente es TRUE en el sistema de elevada confianza.  <br/> No incluya esta notificación en una llamada solo de complemento en el sistema de elevada confianza.  <br/> |true  <br/> |
 

## Codificar y firmar tokens
<a name="EncodeTokens"> </a>

 **Después de que el código haya agregado todas las propiedades y valores a los objetos JSON del encabezado y el cuerpo, tiene que codificarlo, combinarlo en un JWT y firmarlo.** Estos son los pasos.




1. Codifique el encabezado con codificación de URL en base 64.


2. Codifique la carga con codificación de URL en base 64.


3. Concatene el cuerpo codificado después del encabezado codificado con un carácter "." entre ellos. Este es el JWT.


4. Cree una firma SHA256 usando el JWT y la clave privada del certificado.


5. Codifique la firma con codificación de URL en base 64.


6. Anexe la firma al final del JWT, con un carácter "." entre ellos. 


Para un token de actor que se usa con una llamada solo de complemento, el token de actor actúa como token de acceso. No hay un token externo. Para un token de acceso que se usa con una llamada de usuario+complemento, se siguen los pasos anteriores para construir un token de actor que, después, se inserta en el cuerpo de un token de acceso como valor de la propiedad **actortoken**. Después, el token de acceso completo se codifica y se construye con los tres primeros pasos anteriores, pero no se firma, por lo que los demás pasos no se usan para el token externo.




## Solucionar problemas de tokens de acceso
<a name="Trouble"> </a>

 [Fiddler](http://www.telerik.com/fiddler) es una herramienta gratuita que puede usarse para capturar las solicitudes HTTP enviadas por el componente remoto del complemento a SharePoint. Hay una [extensión gratuita para la herramienta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automáticamente los tokens en las solicitudes.




## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Creación de complementos para SharePoint que usan el sistema de autorización de elevada confianza](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)


-  [Seguridad en complementos de SharePoint: parte 7](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-7.aspx), por Steve Peschka


-  [Seguridad en complementos de SharePoint: parte 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx), por Steve Peschka


-  [Usar complementos de SharePoint con sitios SAML y FBA en SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx), por Steve Peschka


-  [Complementos de SharePoint de elevada confianza en plataformas que no son de Microsoft](http://blogs.msdn.com/b/kaevans/archive/2014/07/14/high-trust-sharepoint-apps-on-non-microsoft-platforms.aspx), por Kirk Evan


-  [OAuth 2.0](http://oauth.net/2/)



