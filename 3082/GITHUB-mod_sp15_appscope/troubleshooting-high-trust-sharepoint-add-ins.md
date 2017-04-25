---
title: Solucionar problemas de aplicaciones de elevada confianza para SharePoint
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# Solucionar problemas de aplicaciones de elevada confianza para SharePoint
Obtenga ayuda para solucionar problemas de desarrollo de Complementos de SharePoint de elevada confianza.
En este artículo se describe la herramienta Fiddler y se proporciona orientación para resolver algunos problemas específicos.





## Usar la herramienta Fiddler

La  [herramienta Fiddler](http://www.telerik.com/fiddler) gratuita se puede usar para capturar las solicitudes HTTP enviadas por el componente remoto de su complemento a SharePoint. Hay una [extensión gratuita para la herramienta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automáticamente los tokens de acceso en las solicitudes.



Después de instalar Fiddler en el servidor de aplicaciones web, agregue el siguiente marcado a su archivo web.config para que las solicitudes procedentes de su aplicación web remota vayan por este proxy. De esta manera, puede capturar un seguimiento de Fiddler y ver la respuesta completa de SharePoint cuando obtenga un error.




> **NOTA**
> Asegúrese de quitar este marcado si no está ejecutando Fiddler. Si no quita el marcado, el complemento no podrá hacer solicitudes de HTTP. 






```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>
```

Una vez instalado Fiddler, puede comprobar también los encabezados de respuesta de SharePoint, que incluirán un GUID de solicitud. Este GUID de solicitud es un identificador de correlación y puede buscar en los registros para encontrar errores de registro asociados con esa solicitud.




## Error 401 No autorizado
<a name="UnauthorizedException"> </a>

Hay varias causas para un error **401 Unauthorized** cuando el complemento de elevada confianza accede por primera vez a SharePoint. Si usa el modelo de objetos de cliente (CSOM), el error tiene un aspecto como éste:



```cs

[WebException: The remote server returned an error: (401) Unauthorized.]
   System.Net.HttpWebRequest.GetResponse() +8515936
   Microsoft.SharePoint.Client.SPWebRequestExecutor.Execute() +178
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQueryToServer(ChunkStringBuilder sb) +1427
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQuery() +270
   Microsoft.SharePoint.Client.ClientRuntimeContext.ExecuteQuery() +146
   Microsoft.SharePoint.Client.ClientContext.ExecuteQuery() +666
   S2STestWeb.Default.Page_Load(Object sender, EventArgs e) in c:\\MyFiles\\HightrustTest\\HightrustTestWeb\\Default.aspx.cs:28
   System.Web.UI.Control.LoadRecursive() +71
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178```

Si usa el archivo TokenHelper y la identidad de Windows, el código que desencadena la excepción tiene el siguiente aspecto:





```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();```

El primer paso para solucionar el problema es usar el depurador de Visual Studio para comprobar que el token de acceso y el objeto **ClientContext** se hayan creado correctamente. En caso afirmativo, investigue las siguientes posibilidades:



 **Posibles problemas y su solución:**




- No se ha creado un perfil de usuario para el usuario que quiere acceder a la aplicación web remota. Cree el perfil de usuario.


- Su complemento no tiene permisos en los recursos a los que está intentando acceder. Abra el Shell de administración de SharePoint y ejecute el siguiente cmdlet de Windows PowerShell. La variable  `$web` es el sitio web de SharePoint al que está intentando acceder y `$appPrincipal` es el identificador del complemento. Para más información, vea [Set-SPAppPrincipalPermission](http://technet.microsoft.com/es-es/library/jj219714%28v=office.15%29.aspx).

 ```

Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl
 ```

- La aplicación web acepta solicitudes anónimas. Esto significa que no hay una verdadera identidad de usuario en el token de acceso. Asegúrese de que el acceso anónimo se ha deshabilitado en IIS para el directorio raíz de la aplicación web remota. También puede comprobar esto si depura la aplicación web remota y comprueba el valor de **Request.LogonUserIdentity** en el archivo .aspx.cs (o .vb) predeterminado para asegurarse de que no se trate de un usuario anónimo.


- No se agregó el certificado digital al almacén de certificados de confianza. Asegúrese de seguir los procedimientos descritos en  [Empaquetar y publicar complementos de gran confianza para SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md).



## Errores varios de SSL y de autorización relacionados con el dominio
<a name="DomainRelatedErrors"> </a>

Si los nombres de dominio en los archivos de configuración y en los formularios de registro no coinciden, podría impedir la autorización. Los siguientes cuatro valores tienen que ser exactamente los mismos:




- El **Dominio del complemento** que se especifica cuando se registra la Complemento de SharePoint en AppRegNew.aspx.


- El dominio con el cual se registra el certificado de seguridad de la aplicación web remota.


- La parte del dominio del valor de **StartPage** en el archivo AppManifest.xml.


- La parte del dominio de las direcciones URL de los receptores de eventos especificados en el archivo AppManifest.xml.


En relación con este punto, tenga en cuenta lo siguiente:




- Si el componente remoto de su Complemento de SharePoint usa un puerto que no sea 443, debe incluir ese puerto explícitamente como parte del dominio en las cuatro partes; por ejemplo,  `MarketingServer:3333`. (Debe usar el protocolo HTTPS, cuyo puerto predeterminado es 443).


- El domino se tiene que codificar de forma rígida en el valor **StartPage** (y las direcciones URL de los receptores de eventos) del archivo AppManifest.xml antes de empaquetar el complemento. Si usa el Asistente para **publicación** en Visual Studio para empaquetar el complemento, se le pedirá el dominio y Office Developer Tools para Visual Studio lo insertará en el valor de **StartPage** automáticamente (en lugar del token `~remoteWebUrl` que se usa durante la depuración). Pero si no usa el Asistente para **publicación**, debe reemplazar manualmente el token por el dominio (y protocolo); por ejemplo  `https://MarketingServer` o `https://MarketingServer:3333`.



## Error de tiempo de ejecución que indica que no hay ningún certificado con ese número de serie
<a name="DomainRelatedErrors"> </a>

Si está seguro de que tiene el número de serie del certificado correcto en el archivo web.config y puede ver el certificado en el **almacén de certificados de Windows**, entonces puede haber un carácter adicional oculto en el número de serie en el archivo web.config. Esto sucederá si el número de serie se copia y pega desde **Microsoft Management Console**. Elimine el valor de número de serie completo desde el archivo web.config y vuelva a escribirlo *manualmente*  .




