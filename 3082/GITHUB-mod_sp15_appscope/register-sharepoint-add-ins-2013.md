---
title: Registrar complementos de SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: be41a5dc-2af9-4fd9-bf4e-ad6dfa849524
---



# Registrar complementos de SharePoint 2013
Registre sus Complementos de SharePoint en Azure ACS mediante Visual Studio, el Panel de vendedores o una página AppRegNew.aspx, y recupere la información de registro.
Para que los componentes remotos de un Complemento de SharePoint hospedado por el proveedor puedan interactuar con SharePoint mediante OAuth, el complemento debe primero registrarse con el servicio basado en la nube  [Azure ACS](https://msdn.microsoft.com/es-es/library/azure/gg429788.aspx) y el Servicio de administración de complementos de SharePoint del inquilino o la granja de servidores. (Se llama "Servicio de administración de aplicaciones" porque Complementos de SharePoint se denominaba en un principio "aplicaciones para SharePoint").
  
    
    


> **NOTA**
> Esto no es necesario para los complementos hospedados en SharePoint. 
  
    
    


Para registrar el complemento con Azure ACS, especifique la siguiente información:
  
    
    


- Un GUID para el complemento, llamado identificador de cliente.
    
  
- Una contraseña para el complemento, llamada secreto de cliente.
    
  
- El nombre para mostrar del complemento que se usa en la página de consentimiento cuando se pregunta al usuario si confía en el complemento.
    
  
- La dirección URL del dominio donde el complemento remoto está hospedado.
    
  
- Una URL de redireccionamiento.
    
  
Después de registrar el complemento, este tiene una identidad de complemento y es una  *entidad de seguridad*  , denominadaentidad de seguridad del complemento. Cuando instale el complemento, los administradores de SharePoint podrán recuperar información sobre esta entidad de seguridad de complemento concreta.La primera vez que un usuario concede permisos a un complemento para que acceda a los recursos de SharePoint (lo que puede suceder al instalarlo o en tiempo de ejecución, según el diseño del mismo), SharePoint consigue información sobre el complemento de Azure ACS. Después, SharePoint almacena esta información en el Servicio de administración de aplicaciones del inquilino o de la granja de servidores de SharePoint. El secreto de cliente solo se almacena en Azure ACS. SharePoint no conoce nunca el secreto de un complemento. El servicio de bases de datos de contenido y otros componentes, como el servicio de perfiles de usuario, pueden obtener el nombre para mostrar del complemento y otra información básica sobre el complemento directamente del servicio compartido de administración de aplicaciones. Para obtener más información, vea  [Recuperación de información sobre el registro del complemento y la entidad de seguridad del complemento ](register-sharepoint-add-ins-2013.md#Retrieve) en este artículo.
> **NOTA**
> En este artículo se presupone que el lector conoce los conceptos y principios básicos relativos a OAuth 2.0 Framework. Para más información, vea  [OAuth.net](http://oauth.net/) y el [Protocolo de autorización web (oauth)](http://datatracker.ietf.org/doc/active/). 
  
    
    


## Registrar el Complemento de SharePoint en Azure ACS

Puede registrar el complemento eligiendo una de tres opciones, según en qué etapa de desarrollo se encuentra, según la arquitectura de su complemento, y según cómo tiene pensado comercializarlo.
  
    
    


|**Método de registro**|**Más información**|
|:-----|:-----|
|Usar Visual Studio y las Microsoft Office Developer Tools para Visual Studio para crear una identidad de complemento temporal.  <br/> |El asistente de Office Developer Tools para Visual Studio crea automáticamente un registro temporal para su complemento con ACS y el Servicio de administración de aplicaciones de su sitio web de prueba de SharePoint. Cuando ejecute el complemento desde Visual Studio (F5), se usará esta identidad. Las herramientas también insertan el identificador y el secreto de cliente en los archivos web.config y AppManifest.xml.  <br/> Cuando esté listo para publicar su complemento, puede usar el asistente para publicación de Visual Studio que le llevará al Panel de vendedores, donde puede registrarlo. Si no va a comercializar el Complemento de SharePoint en la Tienda Office, use AppRegNew.aspx para registrarlo. (A continuación se muestran los pasos exactos).  <br/> > **NOTA**> Si su complemento pide permiso para acceder a los recursos de SharePoint de forma dinámica durante el tiempo de ejecución en lugar de durante la instalación del complemento, no puede usar Visual Studio para crear identidades de complementos.           |
|Registrar el complemento a través del Panel de vendedores.  <br/> |Si va a usar el complemento en más de una granja de servidores o inquilino de SharePoint, use el Panel de vendedores para registrar el complemento, independientemente de si pretende comercializarlo en la Tienda Office o hacerlo disponible mediante el catálogo de complementos. Cuando lo registre en el Panel de vendedores, puede diseñar el complemento con una arquitectura multiinquilino sin necesidad de que los administradores de inquilino o granja lo registren por separado. Además, si tiene pensado publicar el complemento en la Tienda Office, tiene que usar el Panel de vendedores para registrar el complemento, pero no tiene que usar la tienda para publicar un complemento que se ha registrado en el Panel de vendedores.  <br/> Para más información, vea  [Crear o actualizar identificadores y claves secretas de cliente en el Panel de vendedores](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx).  <br/> |
|Usar la página AppRegNew.aspx.  <br/> |Use el formulario AppRegNew para registrar el Complemento de SharePoint si va a usar el complemento en un solo inquilino o granja de servidores. Por ejemplo, si va a crear complementos para una sola organización y va a usarlos y distribuirlos a través del catálogo de complementos corporativo, puede usar la página AppRegNew.aspx en cualquier sitio web de un inquilino o granja de servidores para registrar el complemento.  <br/> Un complemento registrado con AppRegNew.aspx no se puede publicar en la Tienda Office. Para complementos que se publican en la Tienda Office, debe obtener una identidad del Panel de vendedores.  <br/> |
   

### Para el registro mediante AppRegNew.aspx


1.  Vaya a `http://` *<SharePointWebsite>*  `/_layouts/15/AppRegNew.aspx` en el inquilino o granja de servidores.
    
   **Formulario de la página AppRegNew**

  

     ![El formulario en la página  de nuevo registro de aplicación con casillas de identificador de cliente, secreto de cliente, título, dominio de aplicación y dirección URL de redireccionamiento. Los botones para generar están junto a las dos primeras. En la esquina están los botones para crear y cancelar.](images/9a38d876-2189-418c-9314-ae493a4cab61.PNG)
  

  

  
2. Especifique los valores de los campos de formulario siguientes.
    
  - **Identificador de complemento**: también conocido como identificador de cliente, se trata de un GUID que se puede generar (al elegir **Generar**) o pegar en AppRegNew.aspx. El valor debe ser único para cada complemento y  *debe estar en minúsculas*  .
    
  
  - **Secreto de complemento**: también conocido como secreto de cliente, una cadena opaca. Se genera en la página AppRegNew.aspx con el botón **Generar**. El siguiente es un ejemplo de un secreto de complemento: **xvVpG0AgVIJfch6ldu4dLUlcZyysmGqBRbpFDu6AfJw=**.
    
    > **IMPORTANTE**
      > Los secretos de complemento expiran. Si registra el complemento en el Panel de vendedores, puede establecer una expiración de hasta 3 años. En el panel también puede agregar nuevos secretos cuando los antiguos alcanzan su fecha de expiración. El nuevo secreto se habilitará en todas las instancias del complemento. Si registra el complemento con AppRegNew.aspx, el secreto expira en 1 año. Para más información, vea  [Reemplazar un secreto de cliente a punto de expirar en un complemento para SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md). 
  - **Título**: un título descriptivo, por ejemplo,Complemento para imprimir fotos de Contoso. Se pedirá a los usuarios que confirmen o denieguen al complemento los permisos que este está solicitando. Este título aparece como el nombre del complemento en el mensaje de consentimiento. 
    
  
  - **Dominio del complemento**: el nombre de host del componente remoto del Complemento de SharePoint. Si la aplicación remota no usa el puerto 443, el dominio del complemento debe incluir también el número de puerto. El dominio del complemento debe hacer coincidir los enlaces URL que usted usa para la aplicación web. No incluya ningún protocolo ("https:") ni caracteres "/" en este valor. Si su host de aplicación web usa un alias CNAME de DNS, use el alias. Estos son algunos ejemplos:
    
  - www.contoso.com:3333
    
  
  - www.fabrikam.com
    
  
  - **URI de redireccionamiento:** el extremo de la aplicación o servicio remotos al que ACS envía un código de autenticación. En términos estrictos, los Complementos de SharePoint no usan este valor. El URI de redireccionamiento se necesita para las aplicaciones web que se inician fuera de SharePoint y que usan el [flujo de código de autenticación](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) para obtener acceso autorizado a los datos de SharePoint. Se ignora el URI de redireccionamiento para los verdaderos Complementos de SharePoint (que se inician desde SharePoint y que usan el [flujo de tokens de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)). El URI de redireccionamiento suele ser la misma página, el método de controlador o el método de servicio web que solicitó el código de autenticación de ACS, pero puede ser un extremo diferente. El extremo debe tener una lógica que obtenga el código de autorización de la respuesta HTTP que ACS envía y, después, usa ese código para solicitar un token de acceso y de actualización. Para más información, vea  [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md). El formulario requiere que especifique un valor válido incluso para Complementos de SharePoint verdaderos, aunque no se use.
    
    El valor debe ser una dirección URL de extremo completa que incluya el protocolo  *que debe ser HTTPS*  . Por ejemplo:
    
  - https://www.contoso.com/Default.aspx
    
  
  - https://www.fabrikam.com/RedirectAccept.aspx
    
  
  - https://www.northwindtraders.com/home/index
    
  
  - https://adventureworks.com/vacationdata.svc
    
  
3. Elija **Crear** en el formulario. La página se volverá a cargar y mostrará una confirmación de los valores especificados. Realice un registro de estos valores que sea fácil de copiar y pegar. Tendrá que insertar los valores en los archivos web.config y AppManifest.xml o en el asistente **Publicador** de Visual Studio.
    
  
Independientemente de cómo registre el Complemento de SharePoint, cuando esté listo para implementarlo en una instalación provisional o para la producción, necesitará  [Insertar los valores de registro en los archivos web.config y AppManifest.xml](#EditConfigFiles). Si usa Visual Studio, Microsoft Office Developer Tools para Visual Studio realizarán esta configuración por usted.
  
    
    

## Insertar los valores de registro en los archivos web.config y AppManifest.xml
<a name="EditConfigFiles"> </a>

Antes de empaquetar el Complemento de SharePoint y antes de implementar sus componentes remotos, inserte algunos de los valores de registro en los archivos AppManifest.xml y web.config.
  
    
    

> **SUGERENCIA**
> Si publica el Complemento de SharePoint mediante el asistente publicador de Visual Studio, Visual Studio le pedirá el identificador de cliente y el secreto de cliente durante el proceso de publicación, y pondrá la información en el sitio adecuado. 
  
    
    


1. En el archivo Web.config del proyecto de Visual Studio, escriba el valor del identificador de complemento como el valor de **ClientId** (reemplazando el valor temporal que las herramientas especificaron).
    
    > **IMPORTANTE**
      > Todas las letras del GUID del identificador de cliente deben estar en minúsculas. 

    A continuación se muestra un ejemplo.
    


 ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
   .  .  .
</appSettings>
 ```

2. Escriba el valor del secreto de complemento como valor de **ClientSecret** (reemplazando el valor temporal que las herramientas especificaron).
    
    El siguiente es un ejemplo de cómo se usan los valores en el archivo Web.config de una aplicación web.
    


 ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
  <add key="ClientSecret" value="l0z/8TzWN0yQBzMBSEZtYts2Vt3Eo/oE3rfCdPaogKQ= " />
</appSettings>
 ```

3. En el archivo AppManifest.xml del proyecto de Visual Studio, escriba el valor del identificador de complemento como valor de **ClientId**,  *con minúsculas*  .
    
    > **NOTA**
      > El manifiesto del complemento no se aplica a aplicaciones web que piden permiso para acceder a los recursos de SharePoint sobre la marcha. Estos no son realmente "Complementos de SharePoint". No se instalan en SharePoint y no tienen un manifiesto de complemento. Para más información, vea  [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md). 

    El siguiente ejemplo muestra cómo se usa el valor **ClientId** en el archivo AppManifest.xml.
    


 ```XML
  
<AppPrincipal>
  <RemoteWebApplication ClientId="a044e184-7de2-4d05-aacf-52118008c44e "/>
</AppPrincipal>
 ```

4. Office Developer Tools para Visual Studio usa el token  `~remoteAppUrl` en el elemento **StartPage**. (Por ejemplo,  `<StartPage>~remoteAppUrl/Pages/Default.aspx?{StandardTokens}</StartPage>`). Este token se resuelve en la dirección URL del componente remoto si usa el asistente **Publicador** de Visual Studio. Si no usa el asistente (o si lo usa pero está publicando el componente remoto en Azure), tiene que reemplazar manualmente el token por el valor del **Dominio del complemento** que usó al registrar el complemento. Debe ser *exactamente*  el mismo valor, incluido el número de puerto, si lo hubiera; la excepción es que se incluye también el protocolo HTTPS. A continuación se muestra un ejemplo.
    
 ```XML
  
<StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
 ```

5. Considere la posibilidad de usar para el elemento **Title** que hay en el archivo AppManifest.xml el mismo valor que usó para el campo **Título** en AppRegNew.aspx. El valor del elemento **Title** es el nombre del complemento que los usuarios ven cuando se este instala. Podría resultar confuso para los usuarios que el complemento tenga un nombre diferente en el cuadro de diálogo de consentimiento y en la interfaz de usuario de SharePoint.
    
    El siguiente ejemplo muestra estos valores en el manifiesto del complemento.
    


 ```XML
  <Properties>
  <Title>Contoso photo printing app</Title>
  <StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
</Properties>
 ```


## Uso de la dirección URL de redireccionamiento en un complemento que pide permisos sobre la marcha
<a name="UseRedirectUrl"> </a>

Si su aplicación web se inicia desde fuera de SharePoint (y por lo tanto, no es un verdadero Complemento de SharePoint), entonces tiene que estar diseñada para pedir permisos desde SharePoint en tiempo de ejecución. Debe contener código que use el URI de redireccionamiento, además de otra información, para obtener un token de acceso de ACS. Encuentre el lugar donde se establece este URI y use el valor  *exacto*  que utilizó en el campo **URI de redireccionamiento** en AppRegNew.aspx o en el Panel de vendedores. Puede estar en un archivo de código o en un archivo de configuración.
  
    
    

## Recuperación de información sobre el registro del complemento y la entidad de seguridad del complemento
<a name="Retrieve"> </a>

Puede recuperar información de registro del complemento y de la entidad de seguridad del complemento de los complementos que haya instalado o registrado en SharePoint. 
  
    
    
Para consultar la información de registro de un complemento que haya registrado, vaya a  `http://` *<SharePointWebsite>*  `/_layouts/15/AppInv.aspx`.
  
    
    
Para hacer una búsqueda, debe recordar el identificador de cliente (también denominado identificador de complemento) que usó para registrar el complemento. La búsqueda devuelve la siguiente información sobre un identificador de cliente determinado:
  
    
    

- Título
    
  
- Dominio del complemento
    
  
- Dirección URL de redireccionamiento (es la misma que el URI de redireccionamiento)
    
  
La búsqueda no devuelve el valor del secreto del complemento.
  
    
    
Para ver una lista de entidades de seguridad del complemento, vaya a:
  
    
    
 `http://` *<SharePointWebsite>*  `/_layouts/15/AppPrincipals.aspx`
  
    
    

## Recursos adicionales
<a name="AR"> </a>


-  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Tres sistemas de autorización para complementos de SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
