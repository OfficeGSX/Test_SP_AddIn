---
title: Работа с папками и файлами в службе REST
ms.prod: SHAREPOINT
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# Работа с папками и файлами в службе REST
Узнайте, как выполнять базовые операции создания, чтения, обновления и удаления (CRUD) для папок и файлов с помощью REST-интерфейса SharePoint 2013.
> **Совет**
> Служба REST SharePoint Online (а также локальной версии SharePoint 2016 и последующих выпусков) поддерживает объединение нескольких запросов в одном вызове службы с помощью параметра запроса OData  `$batch`. Дополнительные сведения и ссылки на примеры кода см. в разделе  [Создание пакетного запроса с помощью интерфейсов REST API](make-batch-requests-with-the-rest-apis.md). 
  
    
    


## Работа с папками с помощью REST
<a name="Folders"> </a>

Вы можете получить папку внутри библиотеки документов, если вы знаете ее URL-адрес. Например, вы можете получить корневую папку библиотеки совместно используемых документов с помощью конечной точки, как показано в примере ниже.
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

В следующем коде XML показан пример свойств папки, который вы получаете при запросе XML-типа контента.
  
    
    



```XML

<content type="application/xml">
<m:properties>
<d:ItemCount m:type="Edm.Int32">0</d:ItemCount>
<d:Name>Shared Documents</d:Name>
<d:ServerRelativeUrl>/Shared Documents</d:ServerRelativeUrl>
<d:WelcomePage/>
</m:properties>
</content>
```

В следующем примере показано, как **создать** папку.
  
    
    



```

url: http://site url/_api/web/folders
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'ServerRelativeUrl': '/document library relative url/folder name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

В следующем примере показано, как **обновить** папку, используя метод **MERGE**.
  
    
    



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
    content-length:length of post body
```

В следующем примере показано, как **удалить** папку.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## Работа с файлами с помощью REST
<a name="Files"> </a>

В следующем примере показано, как **получить** все файлы в папке.
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

В следующем примере показано, как **получить** конкретный файл.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

Вы также можете **получить** файл, если вы знаете его URL-адрес, как показано в примере ниже.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

В следующем примере показано, как **создать** файл и добавить его в папку.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body
```

В следующем примере показано, как **обновить** файл, используя метод **PUT**.
  
    
    

> **Примечание**
> **PUT** является единственным методом, который можно использовать для обновления файла. Метод **MERGE** не разрешен.
  
    
    




```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: POST
body: "Contents of file."
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    X-HTTP-Method:"PUT"
    content-length:length of post body
```

Чтобы обновить метаданные файла, понадобится создать конечную точку, которая будет получать доступ к файлу как к элементу списка. Это возможно благодаря тому, что каждая папка также является списком, а каждый файл также является элементом списка. Создайте следующую конечную точку:  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`. В статье  [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md) объясняется, как обновить метаданные элемента списка.
  
    
    
Вы можете проверить файл на выходе, чтобы убедиться, что с ним не произошло никаких изменений, прежде чем обновлять его. После обновления вы также можете проверить файл на входе, чтобы с ним могли работать другие пользователи. В следующем примере показано, как **проверить файл на выходе**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

В следующем примере показано, как **проверить файл на входе**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

В следующем примере показано, как **удалить** файл.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"

```


## Работа с большими файлами с помощью REST
<a name="LargeFiles"> </a>

Если вам нужно отправить двоичный файл размером свыше 1,5 МБ, интерфейс REST  единственный доступный вариант. См. в разделе  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) пример кода, отправляющего двоичный файл меньше 1,5 МБ с помощью объектной модели SharePoint 2013 Javascript. Максимальный размер двоичного файла, который можно создать с помощью REST,  2 ГБ. В следующем примере показано, как **создать** крупный двоичный файл.
  
    
    

> **Внимание!**
> Этот способ работает только в Internet Explorer 10 и последних версиях других браузеров. 
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/Add(url='file name', overwrite=true)
method: POST
body: contents of binary file
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

В следующем примере кода показано, как создать файл с помощью конечной точки REST и междоменной библиотеки.
  
    
    



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


## Работа с файлами, подключенными к элементам списка с помощью REST
<a name="FileAttachments"> </a>

В следующем примере показано, как **извлечь** все файлы, подключенные к элементу списка.
  
    
    

```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

В следующем примере показано, как **извлечь** файл, подключенный к элементу списка.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

В следующем примере показано, как **создать** подключение файла к элементу списка.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body
```

В следующем примере показано, как **обновить** подключение файла к элементу списка с помощью метода **PUT**.
  
    
    

> **Примечание**
> **PUT** является единственным методом, который можно использовать для обновления файла. Метод **MERGE** не разрешен.
  
    
    




```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: POST
body: "Contents of file."
headers:
    Authorization: "Bearer " + accessToken
    "X-HTTP-Method":"PUT"
    X-RequestDigest: form digest value
    content-length:length of post body
```


## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Справочные материалы по интерфейсу API службы REST для файлов и папок](2c3d2545-1cd7-497e-b535-12199d8edfbb.md)
    
  
-  [Отправка файла с помощью API REST и jQuery](upload-a-file-by-using-the-rest-api-and-jquery.md)
    
  
-  [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: выполнение основных операций доступа к данным в файлах и папках с помощью REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Создание REST-запросов в C # и JavaScript для SharePoint 2013](http://www.microsoft.com/resources/msdn/ru-ru/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=4e4cc094-ff69-405b-852f-2ac7c41293c5)
    
  
-  [Создание REST-запросов в C# и JavaScript для SharePoint 2013 demo](http://www.microsoft.com/resources/msdn/ru-ru/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)
    
  
-  [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Разработка надстроек для SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Безопасный доступ к данным и клиентские объектные модели для надстроек SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Работа с внешними данными в SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Open Data Protocol](http://www.odata.org/)
    
  
-  [OData: формат нотации объектов JavaScript (JSON)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  

  
    
    

