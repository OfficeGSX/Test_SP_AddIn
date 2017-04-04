---
title: Cadenas de dirección URL y tokens en complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# Cadenas de dirección URL y tokens en complementos para SharePoint
Obtenga información sobre qué tokens para direcciones URL están disponibles para su uso en Complementos de SharePoint.
> [!IMPORTANTE]
> Para obtener información general sobre la construcción de direcciones URL en SharePoint 2013 y sobre el uso de los tokens en estas direcciones, consulte  [Formularios de cadenas de direcciones URL](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx). En este tema se describen los tokens que se encuentran disponibles en Complementos de SharePoint. 
  
    
    


## 
<a name="URLtokens"> </a>

SharePoint 2013 admite los tokens que se enumeran en las tablas siguientes para su uso en Complementos de SharePoint.
  
    
    
Los tokens que aparecen en las tablas de esta sección se pueden usar en direcciones URL en una gran variedad de situaciones en el desarrollo de Complementos de SharePoint, como en Acciones personalizadas y en vínculos a páginas personalizadas, si bien, en algunos contextos, no se pueden utilizar algunos de estos tokens. Tres de los lugares más importantes donde solo se puede utilizar una lista de tokens restringida son la página de inicio de un complemento, una acción personalizada de la web de host y la propiedad  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) de un elemento de complemento. Estos tres se indican en columnas separadas, * **pero no son una lista exhaustiva de lugares donde se pueden utilizar los tokens.*** 
  
    
    
La columna **StartPage** especifica si el token se puede utilizar en el elemento **StartPage** de un manifiesto del complemento. La columna **Acción personalizada** especifica si el token se puede utilizar en la dirección URL de una acción personalizada en una web de host. La columna **Elemento de complemento** especifica si el token se puede utilizar en la propiedad [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) del elemento de complemento.
  
    
    

**Los tokens se pueden usar al inicio de una dirección URL en una Complemento de SharePoint.**


|**Token**|**Resuelve a**|**StartPage**|**Acción personalizada**|**Elemento de complemento**|**Comentarios**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl  <br/> |Dirección URL del sitio web de complemento de una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> |Este token se debe usar únicamente fuera de los sitios web de complemento. Use **~site** dentro del sitio web de complemento para la dirección URL del sitio web de complemento. <br/> |
|~controlTemplates  <br/> |Dirección URL de la carpeta virtual ControlTemplates para el sitio web actual.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|~hostUrl  <br/> |Dirección URL de la web de host.  <br/> |No  <br/> |No  <br/> |Sí  <br/> ||
|~hostLogoUrl  <br/> |Dirección URL del logotipo de la web de host.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|~layouts  <br/> |Dirección URL de la carpeta virtual Layouts para el sitio web actual.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|~remoteAppUrl  <br/> |Dirección URL de una aplicación web remota en una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí, en la web de host, pero no en la web de complemento.  <br/> |Sí  <br/> |Si no está usando Microsoft Office Developer Tools para Visual Studio para desarrollar el complemento de SharePoint, no puede usar **~remoteAppUrl** en la dirección URL **StartPage**. Sin embargo, cuando use Visual Studio y las herramientas, podrá usar este token para cualquier complemento que se hospede en un proveedor y se resuelve cuando Visual Studio empaqueta el complemento. En este uso, se trata más de un token de Visual Studio que de un token de SharePoint. Se puede usar fuera del manifiesto del complemento aunque no use Microsoft Office Developer Tools para Visual Studio.  <br/> |
|~site  <br/> |Dirección URL del sitio web actual.  <br/> |No  <br/> |No  <br/> |Sí  <br/> ||
|~sitecollection  <br/> |Dirección URL de la colección del sitio primario del sitio web actual.  <br/> |No  <br/> |No  <br/> |Sí  <br/> ||
   
Excepto cuando se indique lo contrario, ninguno de los tokens de la siguiente tabla se pueden utilizar en la porción  *path*  del valor de la propiedad [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) del elemento de complemento. La columna **Elemento de complemento** hace referencia al uso en la porción *cadena de consulta*  del valor.
  
    
    

**Tokens que se pueden usar dentro de una dirección URL**


|**Token**|**Resuelve a**|**StartPage**|**Acción personalizada**|**Elemento de complemento**|**Comentarios**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken}  <br/> |Token de contexto OAuth para el complemento.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|{AppWebUrl}  <br/> |Dirección URL del sitio web de complemento de una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> |Este token se debe usar únicamente fuera de los sitios web de complemento. Use **{Site}** dentro del sitio web de complemento para la dirección URL del sitio web de complemento. <br/> |
|{ClientTag}  <br/> |Número de control de la caché de cliente (etiqueta del cliente) para el sitio web actual.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> ||
|{HostLogoUrl}  <br/> |Logotipo para el sitio web host de una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> ||
|{HostTitle}  <br/> |Título del sitio web host de una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> ||
|{HostUrl}  <br/> |Dirección URL del sitio web host de una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> ||
|{ItemId}  <br/> |Identificador de un elemento en una lista o biblioteca (entero).  <br/> |No  <br/> |Sí  <br/> |No  <br/> ||
|{ItemUrl}  <br/> |Dirección URL de un elemento sobre el que se realiza una acción.  <br/> |No  <br/> |Sí  <br/> |No  <br/> ||
|{Language}  <br/> |Idioma o referencia cultural actual de un sitio web host de una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> ||
|{ListId}  <br/> |Identificador de la lista actual (un GUID).  <br/> |No  <br/> |Sí  <br/> |No  <br/> ||
|{ProductNumber}  <br/> |Número completo de la versión de compilación de la granja de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> |Un valor de ejemplo es "15.0.4433.1011".  <br/> |
|{RecurrenceId}  <br/> |Índice de recurrencia de un evento recurrente.  <br/> |No  <br/> |Sí  <br/> |No  <br/> |Este token no se admite para usarlo en los menús contextuales de los elementos de lista.  <br/> |
|{RemoteAppUrl}  <br/> |Dirección URL de una aplicación web remota en una Complemento de SharePoint.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> ||
|{Site}  <br/> |Dirección URL del sitio web actual.  <br/> |No  <br/> |Sí  <br/> |Sí  <br/> ||
|{SiteCollection}  <br/> |Dirección URL del sitio primario del sitio web actual.  <br/> |No  <br/> |Sí  <br/> |Sí  <br/> ||
|{SiteUrl}  <br/> |Dirección URL del sitio web actual.  <br/> |No  <br/> |Sí  <br/> |No  <br/> ||
|{Source}  <br/> |Dirección URL de la solicitud HTTP.  <br/> |No  <br/> |Sí  <br/> |No  <br/> ||
|{StandardTokens}  <br/> |Consulte Comentarios.  <br/> |Sí  <br/> |Sí  <br/> |Sí  <br/> |Este token combina otros cinco. Inicialmente resuelve a  `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}`. Posteriormente, cada uno de estos tokens realiza la resolución. Si no hay una web de complemento, la parte  `&amp;SPAppWebUrl={AppWebUrl}` no estará presente. <br/> |
   

## Recursos adicionales
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [Compatibilidad avanzada con extranet](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)
    
  
-  [Obtención de referencias a sitios, aplicaciones web y otros objetos clave](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)
    
  
-  [Trabajar con objetos de listas y colecciones](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)
    
  
-  [Tareas de ejemplo con modelos de objetos](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)
    
  

