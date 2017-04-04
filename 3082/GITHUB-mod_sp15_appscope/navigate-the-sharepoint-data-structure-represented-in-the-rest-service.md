---
title: Navegar por la estructura de datos de SharePoint representada en el servicio REST
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# Navegar por la estructura de datos de SharePoint representada en el servicio REST
Aprenda a iniciar desde un extremo de REST para un determinado elemento de SharePoint, y a navegar hasta elementos relacionados y acceder a ellos, como pueden ser sitios primarios o la estructura de biblioteca en la que reside dicho elemento. 
## Navegue desde una dirección URL dada para alcanzar otro recurso de SharePoint

Cuando está trabajando con el servicio REST de SharePoint, a menudo comenzará sabiendo la dirección URL de un elemento de SharePoint específico, pero desea acceder a elementos relacionados, como la estructura de carpeta o de biblioteca donde reside el elemento. Por ejemplo, suponga que crea un complemento en la que el usuario escribe una dirección URL de un documento en una biblioteca de SharePoint. A continuación, el complemento deberá desglosar esa dirección URL para averiguar la dirección URL del sitio de SharePoint real. Una vez conseguido, la aplicación puede realizar más solicitudes desde el sitio en nombre del usuario, como crear, actualizar o eliminar elementos o recursos relacionados. 
  
    
    
Para ello, el complemento necesita consultar la siguiente información a SharePoint:
  
    
    

- Las URL relativas al servidor del sitio y la colección de sitios que contienen el recurso
    
  
- Una síntesis de formulario para que pueda hacer solicitudes que modifiquen el estado del recurso, como **POST**, **PUT**, **MERGE** y **DELETE**
    
  
El proceso básico:
  
    
    

1. Use el operador  `/contextinfo` con la dirección URL dada para acceder a las direcciones del sitio y de la colección de sitios, y la síntesis de formulario. Use el operador `/contextinfo` en el siguiente formato:
    
     `http://server/web/doclib/forms/_api/contextinfo`
    
    Para aumentar la seguridad contra los intentos de scripting entre sitios, el operador  `/contextinfo` solo acepta solicitudes **POST**.
    
  
2. Use las  [Propiedades del objeto SPContextWebInformation](#bk_props) que devuelve el operador `/contextinfo` para acceder a recursos adicionales si así lo desea.
    
  
 **Probar**
  
    
    

1. Comenzar con una URL dada para un elemento de SharePoint dado. Por ejemplo:
    
     `http://site/web/doclib/myDocument.docx`
    
  
2. Quite el nombre del recurso específico del final de la dirección URL, de forma que la dirección de URL apunte a una biblioteca de documentos, una carpeta o una lista. En este caso:
    
     `http://site/web/doclib/`
    
  
3. Anexe el puntero del servicio REST y el operador  `/contextinfo` a la dirección URL:
    
     `http://site/web/doclib/_api/contextinfo`
    
  
4. Lea la síntesis de formulario y las propiedades **webFullUrl** de la respuesta.
    
  
5. Anexe el puntero del servicio REST  `_api` a la dirección URL de la web.
    
  
6. Use la dirección URL resultante y la síntesis de formulario para realizar solicitudes a otros recursos que necesite.
    
  
No es necesario pasar la síntesis de formulario si está haciendo solicitudes **GET** o solicitudes con un token OAuth validado.
  
    
    

## Navegación por sitios primarios y secundarios
<a name="bk_sites"> </a>

Cuando se navega por la estructura de un sitio con el modelo de objetos del servidor de SharePoint, se usan las propiedades  [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) y [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) para acceder a objetos que representan sitios primarios y secundarios.
  
    
    
Los recursos de REST correspondientes,  `web/parentweb` y `web/webs`, no devuelven objetos que representan sitios. Esto se debe a que el servicio REST cumple con los estándares de OData, y la devolución de representaciones de sitios completos haría dichas solicitudes ineficientes. En su lugar, devuelven un  [Objeto WebInfo ](#bk_webinfo) que contiene las propiedades escalares del sitio, pero sin los conjuntos de entidades relacionados como las colecciones o las colecciones de campos.
  
    
    
Para navegar a un sitio primario o secundario específico, construya la dirección URL de REST adecuada a dicho sitio mediante la propiedad **Id** o **Title**. Desde ahí, podrá acceder a los conjuntos de entidades relacionadas con el sitio.
  
    
    

## Navegación por la estructura de carpetas
<a name="bk_folders"> </a>

El servicio REST de SharePoint no permite recorrer la jerarquía de carpetas de un sitio a través de la construcción de la URL. En su lugar, use el equivalente de REST del método  [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) . Por ejemplo:
  
    
    
 *Navegación no compatible con el servicio REST:* 
  
    
    
 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`
  
    
    
Navegación compatible con el servicio REST: 
  
    
    
 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`.
  
    
    

## Propiedades del objeto SPContextWebInformation
<a name="bk_props"> </a>



|**Propiedad SPContextWebInformation**|**Descripción**|
|:-----|:-----|
|**webFullUrl** <br/> |Obtiene la URL relativa al servidor del sitio más próximo.  <br/> |
|**siteFullUrl** <br/> |Obtiene la URL relativa al servidor de la raíz de la colección a la que pertenece el sitio.  <br/> Si el sitio web más cercano es la raíz de una colección de sitios, el valor de la propiedad **webFullUrl** es igual al de la propiedad **siteFullUrl**.  <br/> |
|**formDigestValue** <br/> |Obtiene la síntesis de formulario de la solicitud del servidor.  <br/> |
|**LibraryVersion** <br/> |Obtiene la versión actual de la biblioteca REST.  <br/> |
|**SupportedSchemaVersions** <br/> |Obtiene las versiones admitidas del esquema de la biblioteca REST/CSOM.  <br/> |
   

## Objeto WebInfo
<a name="bk_webinfo"> </a>



|**Propiedad WebInfo**|**Descripción**|
|:-----|:-----|
|**Created** <br/> |Obtiene un valor que especifica cuándo se creó el sitio.  <br/> |
|**Description** <br/> |Obtiene o establece la descripción del sitio.  <br/> |
|**Id** <br/> |Obtiene un valor que especifica el identificador del sitio.  <br/> |
|**Language** <br/> |Obtiene un valor que especifica el identificador de configuración regional (LCID) del idioma usado en el sitio.  <br/> |
|**LastItemModifiedDate** <br/> |Obtiene un valor que especifica cuándo se modificó por última vez un elemento en el sitio.  <br/> |
|**Title** <br/> |Obtiene o establece el título del sitio.  <br/> |
|**WebTemplateId** <br/> |Obtiene el identificador de la plantilla del sitio.  <br/> |
   

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Introducción al servicio REST para SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabajar con carpetas y archivos con REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Determinar los URI de extremo de REST de SharePoint](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Usar operaciones de consulta de OData en solicitudes REST de SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [API de REST, extremos y ejemplos de SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Sincronizar elementos de SharePoint con el servicio REST](synchronize-sharepoint-items-using-the-rest-service.md)
    
  
-  [Usar valores de ETag a través del servicio REST para obtener control de versiones en documentos y elementos de lista](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

