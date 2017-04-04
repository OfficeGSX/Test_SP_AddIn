---
title: Trabajar con carpetas y archivos con REST
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# Trabajar con carpetas y archivos con REST
Aprenda a realizar operaciones básicas de creación, lectura, actualización y eliminación (CRUD) en carpetas y archivos con la interfaz REST de SharePoint 2013.
> **SUGERENCIA**
> El servicio REST de SharePoint Online (y SharePoint 2016 local o versiones posteriores) admite la combinación de varias solicitudes en una sola llamada al servicio mediante el uso de la opción de consulta  `$batch` de OData. Para obtener información detallada y vínculos a los ejemplos de código, vea [Realizar solicitudes de lote con las API de REST](make-batch-requests-with-the-rest-apis.md). 
  
    
    


## Trabajar con carpetas usando REST
<a name="Folders"> </a>

Puede recuperar una carpeta de una biblioteca de documentos cuando sabe su dirección URL. Por ejemplo, puede recuperar la carpeta raíz de la biblioteca de documentos compartidos usando el extremo del ejemplo siguiente.
  
    
    
```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

El XML siguiente muestra un ejemplo de las propiedades de carpeta que se devuelven cuando solicita el tipo de contenido XML.
  
    
    


```XML

<content type="application/xml">
<m:properties>
<d:ItemCount m:type="Edm.Int32">0</d:ItemCount>
<d:Name>Shared Documents</d:Name>
<d:ServerRelativeUrl>/Shared Documents</d:ServerRelativeUrl>
<d:WelcomePage/>
</m:properties>
</content>```

En el siguiente ejemplo se muestra cómo **crear** una carpeta.
  
    
    


```

url: http://site url/_api/web/folders
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'ServerRelativeUrl': '/document library relative url/folder name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **actualizar** una carpeta usando el método **MERGE**.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'Name': 'New name' }
Headers: 
     Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"MERGE",
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **eliminar** una carpeta.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"
```


## Trabajar con archivos usando REST
<a name="Files"> </a>

En el siguiente ejemplo se muestra cómo **recuperar** todos los archivos de una carpeta.
  
    
    
```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

En el siguiente ejemplo se muestra cómo **recuperar** un archivo específico.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken```

También puede **recuperar** un archivo cuando sabe su URL, como en el caso siguiente.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken```

En el siguiente ejemplo se muestra cómo **crear** un archivo y agregarlo a una carpeta.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body```

En el siguiente ejemplo se muestra cómo **actualizar** un archivo usando el método **PUT**.
  
    
    

> **NOTA**
> **PUT** es el único método que puede usarse para actualizar un archivo; no se permite el método **MERGE**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: POST
body: "Contents of file."
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    X-HTTP-Method:"PUT"
    content-length:length of post body```

Si desea actualizar los metadatos de un archivo, deberá construir un extremo que alcance al archivo como un elemento de lista. Esto se puede hacer porque cada carpeta es también una lista, y cada archivo es también un elemento de lista. Construya un extremo que tenga el siguiente aspecto:  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`. En  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md) se explica cómo actualizar los metadatos de un elemento de lista.
  
    
    
Puede que desee desproteger un archivo para asegurarse de que nadie lo cambie antes de que lo actualice. Tras la actualización, también puede volver a proteger el archivo para que otros usuarios puedan usarlo. En el ejemplo siguiente se muestra cómo **desproteger un archivo**.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value```

En el ejemplo siguiente se muestra cómo **proteger un archivo**.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value```

En el siguiente ejemplo se muestra cómo **eliminar** un archivo.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"
```


## Trabajar con archivos grandes usando REST
<a name="LargeFiles"> </a>

Cuando necesita subir un archivo binario de más de 1,5 megabytes (MB), la interfaz de REST es su única opción. En  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) hay un ejemplo de código donde se ve cómo subir un archivo binario de menos de 1,5 MB usando el modelo de objetos JavaScript de SharePoint 2013. El tamaño máximo del archivo binario que se puede crear con REST es de 2 gigabytes (GB). En el siguiente ejemplo se ve cómo **crear** un archivo binario grande.
  
    
    

> **PRECAUCIóN**
> Este método solo funciona con Internet Explorer 10 y las últimas versiones de otros exploradores. 
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/Add(url='file name', overwrite=true)
method: POST
body: contents of binary file
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

En el siguiente ejemplo de código se muestra cómo crear un archivo usando este extremo de REST y la biblioteca entre dominios.
  
    
    


```

function uploadFileBinary() {
XDomainTestHelper.clearLog();
var ro;
if (document.getElementById("TxtViaUrl").value.length > 0) {
ro = new SP.RequestExecutor(document.getElementById("TxtWebUrl").value, document.getElementById("TxtViaUrl").value);
}
else {
ro = new SP.RequestExecutor(document.getElementById("TxtWebUrl").value);
}
var body = "";
for (var i = 0; i < 1000; i++) {
var ch = i % 256;
body = body + String.fromCharCode(ch);
}
var info = {
url: "_api/web/lists/getByTitle('Shared Documents')/RootFolder/Files/Add(url='a.dat', overwrite=true)",
method: "POST",
binaryStringRequestBody: true,
body: body,
success: success,
error: fail,
state: "Update"
};
ro.executeAsync(info);
}
```


## Trabajar con archivos adjuntos a elementos de lista usando REST
<a name="FileAttachments"> </a>

En el ejemplo siguiente se muestra cómo **recuperar** todos los archivos que están adjuntos a un elemento de lista.
  
    
    
```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

En el ejemplo siguiente se muestra cómo **recuperar** un archivo que está adjunto a un elemento de lista.
  
    
    


```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

En el ejemplo siguiente se muestra cómo **crear** un archivo adjunto a un elemento de lista.
  
    
    


```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body```

En el ejemplo siguiente se muestra cómo **actualizar** un archivo adjunto a un elemento de lista usando el método **PUT**.
  
    
    

> **NOTA**
> **PUT** es el único método que puede utilizarse para actualizar un archivo; no se permite el método **MERGE**.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: POST
body: "Contents of file."
headers:
    Authorization: "Bearer " + accessToken
    "X-HTTP-Method":"PUT"
    X-RequestDigest: form digest value
    content-length:length of post body```


## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Referencia de API de REST de archivos y carpetas](2c3d2545-1cd7-497e-b535-12199d8edfbb.md)
    
  
-  [Subir un archivo con la API de REST y jQuery](upload-a-file-by-using-the-rest-api-and-jquery.md)
    
  
-  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md)
    
  
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
    
  
-  [OData: Formato de notación de objetos JavaScript (JSON)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  

  
    
    

