---
title: Sincronizar elementos de SharePoint con el servicio REST
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# Sincronizar elementos de SharePoint con el servicio REST
Aprenda cómo sincronizar elementos entre SharePoint y sus complementos o servicios usando el recurso **GetListItemChangesSinceToken**, parte del servicio REST de SharePoint.
## Sincronización de elementos de SharePoint mediante el recurso GetListItemChangesSinceToken

Si desea sincronizar elementos entre SharePoint y sus complementos o servicios, puede hacerlo con el recurso **GetListItemChangesSinceToken**. El recurso **GetListItemChangesSinceToken**, que forma parte del servicio REST de SharePoint, se corresponde con la llamada al servicio web  [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    
Realice una solicitud **POST** que incluya un objeto [Propiedades del objeto SP.ChangeLogItemQuery](#bk_props) en el cuerpo de la solicitud.
  
    
    
La solicitud devuelve el XML **rowset** de ADO, que incluye las filas correspondientes a cualquier cambio de elemento de lista que coincida con la consulta especificada. Para más información sobre estas propiedades, incluidas las estructuras de datos de las propiedades, descripciones de elementos CAML y valores de devolución, vea [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    

||
|:-----|
|**Ejemplo de solicitud** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**Ejemplo de cuerpo de POST** <br/> |
|```XML

{ 'd' : {
  'query': { 
    '__metadata': { 'type': 'SP.ChangeLogItemQuery'}, 
    'ViewName': '', 
    'Query': '<Where>
      <Contains>
         <FieldRef Name="Title" />
         <Value Type='Text'>Te</Value>
      </Contains></Where>',
    'QueryOptions': '<QueryOptions>
      <IncludeMandatoryColumns>FALSE</IncludeMandatoryColumns>
      <DateInUtc>False</DateInUtc>
      <IncludePermissions>TRUE</IncludePermissions>
      <IncludeAttachmentUrls>FALSE</IncludeAttachmentUrls>
      <Folder>Shared Documents/Test1</Folder></QueryOptions>', 
    'ChangeToken':'1;3;eee4c6d5-f88a-42c4-8ce1-685122984870;634397182229400000;3710', 
    'Contains':'<Contains>
      <FieldRef Name="Title"/>
      <Value Type="Text">Testing</Value></Contains>' } 
  } 
}
```

|
   

## Propiedades del objeto SP.ChangeLogItemQuery
<a name="bk_props"> </a>


****


|**Propiedad**|**Descripción**|
|:-----|:-----|
|**ListName** <br/> |Cadena que contiene el título o el GUID de la lista. Al consultar la tabla UserInfo, la cadena contiene UserInfo. Si se usa el GUID, se obtiene un mejor rendimiento.  <br/> |
|**ViewName** <br/> |Cadena que contiene el GUID de la vista, que determina la vista que se usará para los atributos de vista predeterminada, representados por los parámetros  _query_,  _viewFields_ y _rowLimit_. Si no se proporciona este argumento, se supone que es la vista predeterminada. Si se proporciona, el valor del parámetro  _query_,  _viewFields_ o _rowLimit_ invalida la configuración equivalente en la vista. Por ejemplo, si la vista especificada por el parámetro _viewFields_ tiene un límite de 100 filas, pero el parámetro _rowLimit_ contiene un valor de 1.000, entonces se devuelven 1.000 filas en la respuesta. <br/> |
|**Query** <br/> |Elemento  [Query](http://msdn.microsoft.com/es-es/library/ms471093.aspx) que contiene la consulta que determina qué registros se devuelven y en qué orden. <br/> |
|**QueryOptions** <br/> |Fragmento XML en el formulario siguiente que contiene nodos independientes para las diferentes propiedades del objeto  [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) . <br/> |
|**ChangeToken** <br/> |Cadena que contiene el token de cambio de la solicitud. Para obtener una descripción del formato usado en esta cadena, vea  [Información general acerca del registro de cambios](http://msdn.microsoft.com/es-es/library/bb417456.aspx). Si se transfiere un valor nulo, se devuelven todos los elementos de la lista.  <br/> |
|**Contains** <br/> |Elemento  [Contains](http://msdn.microsoft.com/es-es/library/ms196501.aspx) que define un filtro personalizado para la consulta. <br/> |
   

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Introducción al servicio REST para SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabajar con carpetas y archivos con REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Navegar por la estructura de datos de SharePoint representada en el servicio REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [Determinar los URI de extremo de REST de SharePoint](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Usar operaciones de consulta de OData en solicitudes REST de SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [API de REST, extremos y ejemplos de SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Usar valores de ETag a través del servicio REST para obtener control de versiones en documentos y elementos de lista](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

