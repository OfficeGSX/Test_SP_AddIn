---
title: Trabajar con listas y elementos de lista con REST
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# Trabajar con listas y elementos de lista con REST
Aprenda a realizar operaciones básicas de creación, lectura, actualización y eliminación (CRUD) en listas y elementos de listas con la interfaz REST de SharePoint 2013.
> **SUGERENCIA**
> El servicio REST de SharePoint Online (y SharePoint 2016 local o versiones posteriores) admite la combinación de varias solicitudes en una sola llamada al servicio mediante el uso de la opción de consulta  `$batch` de OData. Para obtener información detallada y vínculos a los ejemplos de código, vea [Realizar solicitudes de lote con las API de REST](make-batch-requests-with-the-rest-apis.md). 





## Requisitos previos

Este tema presupone que ya está familiarizado con los temas  [Introducción al servicio REST para SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md) y [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). No se proporcionan fragmentos de código.




## Recuperar listas y propiedades de listas con REST
<a name="RetrieveLists"> </a>

En el siguiente ejemplo se muestra cómo **recuperar** una lista específica si sabe su GUID.



```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```


> **NOTA**
> Use  `application/json;odata=verbose` en el encabezado `accept` si desea que la respuesta se dé en JSON. Use `application/atom+xml` en el encabezado `accept` si desea que la respuesta se dé en formato Atom.




En el siguiente ejemplo se muestra cómo **recuperar** una lista específica si sabe su título.





```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

El XML siguiente muestra un ejemplo de las propiedades de lista que se devuelven cuando solicita el tipo de contenido XML.





```XML

<content type="application/xml">
  <m:properties>
  <d:AllowContentTypes m:type="Edm.Boolean">true</d:AllowContentTypes> 
  <d:BaseTemplate m:type="Edm.Int32">100</d:BaseTemplate> 
  <d:BaseType m:type="Edm.Int32">0</d:BaseType> 
  <d:ContentTypesEnabled m:type="Edm.Boolean">false</d:ContentTypesEnabled> 
  <d:Created m:type="Edm.DateTime">2012-06-26T23:15:58Z</d:Created> 
  <d:DefaultContentApprovalWorkflowId m:type="Edm.Guid">00000000-0000-0000-0000-000000000000</d:DefaultContentApprovalWorkflowId> 
  <d:Description>A list created by Project Based Retention used to store Project Policy Items.</d:Description> 
  <d:Direction>none</d:Direction> 
  <d:DocumentTemplateUrl m:null="true" /> 
  <d:DraftVersionVisibility m:type="Edm.Int32">0</d:DraftVersionVisibility> 
  <d:EnableAttachments m:type="Edm.Boolean">true</d:EnableAttachments> 
  <d:EnableFolderCreation m:type="Edm.Boolean">false</d:EnableFolderCreation> 
  <d:EnableMinorVersions m:type="Edm.Boolean">false</d:EnableMinorVersions> 
  <d:EnableModeration m:type="Edm.Boolean">false</d:EnableModeration> 
  <d:EnableVersioning m:type="Edm.Boolean">false</d:EnableVersioning> 
  <d:EntityTypeName>ProjectPolicyItemList</d:EntityTypeName> 
  <d:ForceCheckout m:type="Edm.Boolean">false</d:ForceCheckout> 
  <d:HasExternalDataSource m:type="Edm.Boolean">false</d:HasExternalDataSource> 
  <d:Hidden m:type="Edm.Boolean">true</d:Hidden> 
  <d:Id m:type="Edm.Guid">74de3ff3-029c-42f9-bd2a-1e9463def69d</d:Id> 
  <d:ImageUrl>/_layouts/15/images/itgen.gif</d:ImageUrl> 
  <d:IrmEnabled m:type="Edm.Boolean">false</d:IrmEnabled> 
  <d:IrmExpire m:type="Edm.Boolean">false</d:IrmExpire> 
  <d:IrmReject m:type="Edm.Boolean">false</d:IrmReject> 
  <d:IsApplicationList m:type="Edm.Boolean">false</d:IsApplicationList> 
  <d:IsCatalog m:type="Edm.Boolean">false</d:IsCatalog> 
  <d:IsPrivate m:type="Edm.Boolean">false</d:IsPrivate> 
  <d:ItemCount m:type="Edm.Int32">0</d:ItemCount> 
  <d:LastItemDeletedDate m:type="Edm.DateTime">2012-06-26T23:15:58Z</d:LastItemDeletedDate> 
  <d:LastItemModifiedDate m:type="Edm.DateTime">2012-06-26T23:15:59Z</d:LastItemModifiedDate> 
  <d:ListItemEntityTypeFullName>SP.Data.ProjectPolicyItemListItem</d:ListItemEntityTypeFullName> 
  <d:MultipleDataList m:type="Edm.Boolean">false</d:MultipleDataList> 
  <d:NoCrawl m:type="Edm.Boolean">true</d:NoCrawl> 
  <d:ParentWebUrl>/</d:ParentWebUrl> 
  <d:ServerTemplateCanCreateFolders m:type="Edm.Boolean">true</d:ServerTemplateCanCreateFolders> 
  <d:TemplateFeatureId m:type="Edm.Guid">00bfea71-de22-43b2-a848-c05709900100</d:TemplateFeatureId> 
  <d:Title>Project Policy Item List</d:Title> 
  </m:properties>
  </content>```


> **NOTA**
> La propiedad **ListItemEntityTypeFullName** ( **SP.Data.ProjectPolicyItemListItem** en el ejemplo anterior) es especialmente importante si desea crear y actualizar elementos de lista. Siempre que se creen y actualicen elementos de lista, este valor se debe transmitir como propiedad **type** en los metadatos que aparezcan en el cuerpo de la solicitud HTTP.





## Trabajar con listas usando REST
<a name="WorkLists"> </a>

En el siguiente ejemplo se muestra cómo **crear** una lista.



```

url: http://site url/_api/web/lists
method: POST
body: { '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true, 'BaseTemplate': 100,
 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **actualizar** una lista usando el método **MERGE**.





```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
body: { '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH": etag or "*"
    X-HTTP-Method: MERGE,
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el ejemplo siguiente se muestra cómo **crear** un **campo personalizado** para una lista.





```

Url: url: http://site url/_api/web/lists(guid'list GUID')/Fields
Method:POST
Body: { '__metadata': { 'type': 'SP.Field' }, 'Title': 'field title', 'FieldTypeKind': FieldType value,'Required': 'true/false', 'EnforceUniqueValues': 'true/false','StaticName': 'field name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **eliminar** una lista.





```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE
```


## Trabajar con elementos de lista usando REST
<a name="ListItems"> </a>

En el ejemplo siguiente se muestra cómo **recuperar** todos los elementos de una lista.




> **NOTA**
> La opción de consulta $skip de OData no funciona al consultar elementos de lista. En muchas situaciones, puede usar la opción  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx). 




```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

En el siguiente ejemplo se muestra cómo **recuperar** un elemento de lista específico.





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

El XML siguiente muestra un ejemplo de las propiedades de elemento de lista que se devuelven cuando solicita el tipo de contenido XML.





```XML

<content type="application/xml">
<m:properties> 
<d:FileSystemObjectType m:type="Edm.Int32">0</d:FileSystemObjectType>
<d:Id m:type="Edm.Int32">1</d:Id>
<d:ID m:type="Edm.Int32">1</d:ID>
<d:ContentTypeId>0x010049564F321A0F0543BA8C6303316C8C0F</d:ContentTypeId>
<d:Title>an item</d:Title>
<d:Modified m:type="Edm.DateTime">2012-07-24T22:47:26Z</d:Modified>
<d:Created m:type="Edm.DateTime">2012-07-24T22:47:26Z</d:Created>
<d:AuthorId m:type="Edm.Int32">11</d:AuthorId>
<d:EditorId m:type="Edm.Int32">11</d:EditorId>
<d:OData__UIVersionString>1.0</d:OData__UIVersionString>
<d:Attachments m:type="Edm.Boolean">false</d:Attachments>
<d:GUID m:type="Edm.Guid">eb6850c5-9a30-4636-b282-234eda8b1057</d:GUID>
</m:properties>
</content>```

En el siguiente ejemplo se muestra cómo **crear** un elemento de lista.




> **NOTA**
> Para ejecutar esta operación, debe saber la propiedad **ListItemEntityTypeFullName** de la lista y pasarla como valor de **type** en el cuerpo de la solicitud HTTP.






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'Test'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **actualizar** un elemento de lista.




> **NOTA**
> Para ejecutar esta operación, debe saber la propiedad **ListItemEntityTypeFullName** de la lista y pasarla como valor de **type** en el cuerpo de la solicitud HTTP.






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'TestUpdated'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"MERGE",
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **eliminar** un elemento de lista.





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"
```


## Uso de valores ETag para el control de versiones en documentos y elementos de lista
<a name="Etag"> </a>

El servicio REST de SharePoint, que cumple el  [estándar OData](http://www.odata.org/developers/protocols/operations), usa  [Etags HTML para el control de simultaneidad](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags) de las listas y los elementos de lista de SharePoint. Para comprobar la versión de un elemento al efectuar una solicitud **PUT**, **MERGE** o **DELETE**, especifique una **ETag** en el encabezado de la solicitud HTTP **If-Match**.



Si la **ETag** especificada en la solicitud no coincide con la **ETag** del documento o con el elemento de lista del servidor, el servicio REST devolverá una excepción 412, de acuerdo con la especificación OData.




- Para forzar una sobrescritura del elemento independientemente de la versión, establezca el valor de la **ETag** en **"*"**.


- Si no especifica ninguna **ETag**, SharePoint sobrescribirá el elemento independientemente de la versión.


En SharePoint, las ETags solo se aplican a las listas y a los elementos de lista de SharePoint.




## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Trabajar con carpetas y archivos con REST](working-with-folders-and-files-with-rest.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013: Realizar operaciones básicas de acceso a datos en archivos y carpetas usando REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [Realizar llamadas REST con C# y JavaScript para SharePoint 2013](http://www.microsoft.com/resources/msdn/es-es/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=4e4cc094-ff69-405b-852f-2ac7c41293c5)


-  [Demostración para realizar llamadas REST con C# y JavaScript para SharePoint 2013](http://www.microsoft.com/resources/msdn/es-es/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)


-  [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)


-  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Trabajar con datos externos en SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)


-  [Protocolo Open Data](http://www.odata.org/)


-  [OData: Formato de notación de objetos JavaScript (JSON)](http://www.odata.org/documentation/odata-version-2-0/json-format/)







