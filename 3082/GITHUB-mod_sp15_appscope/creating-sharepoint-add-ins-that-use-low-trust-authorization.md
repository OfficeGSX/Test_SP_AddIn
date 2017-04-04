---
title: Crear complementos de SharePoint que usen autorización de baja confianza
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# Crear complementos de SharePoint que usen autorización de baja confianza
Conozca el sistema de autorización de confianza baja para Complementos de SharePoint.
Los componentes remotos de un Complemento de SharePoint (o una aplicación externa) pueden obtener autorización para los recursos de SharePoint pasando un token de acceso a SharePoint con cada solicitud HTTP. Los componentes remotos consiguen el token de acceso de una cuenta de Servicio de control de acceso (ACS) de Microsoft Azure que está asociada con el inquilino de Office 365 del cliente. Azure ACS hace de servidor de autorización en una transacción de  [OAuth 2.0](http://oauth.net/), denominada flujo, con SharePoint como servidor de recursos y los componentes remotos como cliente. Para consultar las especificaciones de protocolo relacionadas, vea  [Protocolo de autorización web (oauth)](http://datatracker.ietf.org/doc/active/#oauth). 
  
    
    

Los Complementos de SharePoint hospedados por el proveedor que usan el sistema de autorización de baja confianza se pueden vender en la Tienda Office e instalar en Microsoft SharePoint Online o en una granja de servidores local de SharePoint que se haya configurado para usar el inquilino de Office 365 del cliente para establecer confianza con Azure ACS. El cliente debe tener un inquilino de Office 365 para instalar Complementos de SharePoint que usen el sistema de baja confianza. Sin embargo, no es necesario que el cliente use el inquilino para otros fines. Si quiere instrucciones sobre cómo vincular una granja de servidores local a un inquilino de Office 365, vea  [Usar un sitio de Office 365 SharePoint para autorizar complementos hospedados por el proveedor en un sitio de SharePoint local](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md).
## Registro con Azure ACS
<a name="Registration"> </a>

Para usar el sistema de baja confianza, el Complemento de SharePoint se debe registrar primero con Azure ACS y con el Servicio de administración de aplicaciones de la granja de servidores de SharePoint o el inquilino de SharePoint Online. (Se llama "Servicio de administración de aplicaciones" porque Complementos de SharePoint antes se llamaba "aplicaciones de SharePoint"). En el caso de los complementos que se venden en la Tienda Office, el registro en ACS se lleva a cabo en el Panel de vendedores y el registro con el servicio se realiza cuando se instala el complemento. En el caso de los complementos que se distribuyen en el catálogo de complementos de la organización, el registro en ACS y el servicio se lleva a cabo en la página \\_Layouts\\15\\AppRegNew.aspx de cualquier inquilino o granja de servidores de SharePoint donde se vaya a instalar el complemento. Las aplicaciones externas que no son de SharePoint y obtienen acceso a SharePoint también se deben registrar. Esta categoría incluye Complementos de Office, aplicaciones de la Tienda Windows, aplicaciones web y aplicaciones de dispositivos, como las aplicaciones de los smartphones.
  
    
    

> **NOTA**
> El registro requiere que la aplicación tenga un dominio de Internet. Para esto se puede usar cualquier dominio existente, pero no se puede estar seguro al 100 % de que los dominios que no son suyos existan. Por ello, una aplicación web tendría que formar parte de una aplicación de dispositivo nativa aunque el componente de la aplicación web no hiciese otra cosa aparte de permitir el registro. Puede ver un ejemplo de código avanzado diseñado de esta forma en  [Sitios de provisión por lotes con el modelo del complemento](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6). 
  
    
    

Para más información sobre el registro, vea  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Expiración del secreto de complemento

El secreto de complemento debe reemplazarse cada 12 meses. Para más detalles, vea  [Reemplazar un secreto de cliente a punto de expirar en un complemento para SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md).
  
    
    

## Directivas de autorización
<a name="Policies"> </a>

Un Complemento de SharePoint se puede diseñar para que use cualquiera de estas dos directivas de autorización:
  
    
    

- **Directiva usuario + complemento:** los complementos que usan esta directiva solo pueden hacer las acciones que el complemento y el usuario tienen permiso para llevar a cabo. Se trata de la directiva predeterminada que se usa a menos que el desarrollador haga algo para usar la otra.
    
  
- **Directiva solo de aplicación:** los complementos que usan esta directiva pueden hacer cualquier acción que tengan permiso para hacer aunque el usuario no pueda hacerla. El desarrollador debe pedir que esta directiva se use en el manifiesto del complemento. La solicitud la tiene que aprobar el usuario que instala el complemento. Esta directiva se permite solo para Complementos de SharePoint hospedados por el proveedor.
    
  
Para más información sobre las directivas de autorización, vea  [Tipos de directivas de autorización de complementos en SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Dos flujos en tiempo de ejecución OAuth
<a name="Flows"> </a>

Cada vez que se ejecuta un Complemento de SharePoint hospedado en la nube o una aplicación externa que obtiene acceso a SharePoint, se desencadena un flujo: una serie de interacciones entre el complemento, SharePoint 2013, ACS y, a veces, el usuario final. El resultado final del flujo es que SharePoint recibe un token de acceso incluido en todas las solicitudes de creación, actualización y eliminación (CRUD) que la aplicación hace a SharePoint.
  
    
    
Existen dos flujos principales de OAuth que usa SharePoint. Uno es para las Complementos de SharePoint hospedadas en la nube. El otro, denominado "sobre la marcha", es para las aplicaciones de otras plataformas que obtienen acceso a datos de SharePoint.
  
    
    

- **Flujo de tokens de contexto:** el componente remoto del Complemento de SharePoint usa el modelo de objetos de cliente (CSOM) de SharePoint o los extremos de REST para hacer llamadas a SharePoint. SharePoint solicita un token de contexto al ACS que puede enviar al servidor remoto. El servidor remoto usa el token de contexto para solicitar un token de acceso al ACS. Luego, el servidor remoto usa el token de acceso para responder a SharePoint. Para más información sobre este flujo, vea [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).
    
  
- **Flujo de código de autenticación:** los permisos que un Complemento de SharePoint necesita para los recursos de SharePoint se le conceden cuando se instala. En cambio, las aplicaciones que no están instaladas en SharePoint deben pedir permisos "sobre la marcha", es decir, cada vez que se ejecutan. En este flujo no hay token de contexto de SharePoint. Cuando el complemento se ejecuta y trata de obtener acceso a SharePoint, SharePoint le pide al usuario que conceda permisos a la aplicación que está solicitando. Cuando el usuario concede los permisos, SharePoint obtiene un código de autorización de ACS que pasa a la aplicación. La aplicación usa el código para obtener un token de acceso del ACS que se puede usar para comunicarse con SharePoint. Si quiere más detalles sobre este flujo, vea [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
    
  

## Solución de problemas de Complementos de SharePoint de baja confianza
<a name="Trouble"> </a>

Este artículo es una guía para la solución de algunos problemas generales y proporciona información sobre algunas cuestiones concretas relacionadas con Complementos de SharePoint que usan el sistema de autorización de baja confianza.
  
    
    

### Uso de la herramienta Fiddler

 [Fiddler](http://www.telerik.com/fiddler) es una herramienta gratuita que puede usarse para capturar las solicitudes HTTP enviadas por el componente remoto del complemento a SharePoint. Hay una [extensión gratuita de la herramienta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automáticamente los tokens de acceso de las solicitudes.
  
    
    

### Desactivación del requisito HTTPS para OAuth durante el desarrollo
<a name="TurnOffHTTPRequirement"> </a>

OAuth requiere que SharePoint ejecute HTTPS (no solo su servicio, sino también SharePoint). Esto puede obstaculizar el desarrollo del complemento. Por ejemplo, puede obtener un mensaje 403 (prohibido) al intentar realizar una llamada a SharePoint cuando esté depurando el complemento porque no hay ninguna compatibilidad con SSL presente en el "localhost" donde se está ejecutando el complemento.
  
    
    
Puede desactivar el requisito HTTPS durante el desarrollo por medio de los siguientes cmdlets de Windows PowerShell.
  
    
    


```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()
```

Para activar el requisito HTTPS posteriormente, use los siguientes cmdlets de Windows PowerShell.
  
    
    


```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()
```


### Varios errores de autorización de SSL y relativos al dominio
<a name="DomainRelatedErrors"> </a>

La no coincidencia de nombres de dominio en los archivos de configuración y los formularios de registro puede impedir la autorización. Los cuatro valores siguientes tienen que ser exactamente los mismos:
  
    
    

- El **dominio del complemento** que se especifica cuando el Complemento de SharePoint se registra tanto en AppRegNew.aspx como en el Panel de vendedores.
    
  
- El dominio bajo el cual se registra el certificado de seguridad de la aplicación web remota.
    
  
- La parte de dominio del valor de la **StartPage** en el archivo AppManifest.xml.
    
  
- La parte de dominio de las direcciones URL de todos los receptores de eventos especificados en el archivo AppManifest.xml.
    
  
En relación con este punto, tenga en cuenta lo siguiente:
  
    
    

- Si el componente remoto del Complemento de SharePoint está usando cualquier otro puerto que no sea 443, debe especificar explícitamente el puerto como parte del dominio en los cuatro lugares; por ejemplo,  `contoso.com:3333`. (Debe usar el protocolo HTTPS cuyo puerto predeterminado es 443).
    
  
- Si crea un alias DNS CNAME para su aplicación web remota, use el alias CNAME para el valor del dominio en los cuatro lugares. Por ejemplo, si la aplicación se hospeda en contoso.cloudapp.net y crea un CNAME contososoftware.com para esta, use contososoftware.com como dominio.
    
  
- El dominio debe necesariamente estar codificado de forma rígida en el valor de la **StartPage** (y en todas las direcciones URL de receptores de eventos) del archivo AppManifest.xml antes de empaquetar el complemento. Si usa el asistente **Publicador** en Visual Studio para empaquetar el complemento, se le solicitará el dominio y el Office Developer Tools para Visual Studio lo insertará en el valor de la **StartPage** por usted (en el lugar del token `~remoteWebUrl` que se ha usado durante la depuración. Sin embargo, si no usa el asistente **Publicador**, o si lo usa pero su aplicación web remota está implementada en Azure, debe reemplazar manualmente el token con el dominio (y protocolo); por ejemplo  `https://contososoftware.com` o `https://MyCloudVM:3333`.
    
  

### Error "La conexión subyacente se ha cerrado: no se pudo establecer una relación de confianza para el canal seguro de SSL/TLS"
<a name="ErrorConnectionClosed"> </a>

Este error es una cuestión de protocolo de enlace de SSL, no una cuestión de OAuth. Asegúrese de que el certificado que use es de confianza para SharePoint, y que el certificado coincide con el nombre del extremo.
  
    
    

### Errores al usar el método HTTP DAV para leer archivos desde SharePoint
<a name="ErrorConnectionClosed"> </a>

HTTP DAV no funciona con OAuth. Si usa el modelo de objetos cliente SharePoint (CSOM), use el siguiente código para leer un archivo.
  
    
    
```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();
```


## En esta sección
<a name="Trouble"> </a>

 [Usar un sitio de Office 365 SharePoint para autorizar complementos hospedados por el proveedor en un sitio de SharePoint local](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)
  
    
    
 [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Reemplazar un secreto de cliente a punto de expirar en un complemento para SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)
  
    
    

## Recursos adicionales
<a name="FileName_AdditionalResources"> </a>


-  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md)
    
  

