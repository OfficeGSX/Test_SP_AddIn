---
title: 使用 REST 处理文件夹和文件
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# 使用 REST 处理文件夹和文件
了解如何使用 SharePoint 2013 REST 界面对文件夹和文件执行基本的创建、读取、更新和删除 (CRUD) 操作。
> **提示**
> SharePoint Online（和本地 SharePoint 2016 及更高版本）REST 服务支持使用 OData  `$batch` 查询选项，将多个请求合并到对该服务的单个调用中。有关详细信息和代码示例的链接，请参阅 [使用 REST API 发出批处理请求](make-batch-requests-with-the-rest-apis.md)。 





## 通过 REST 使用文件夹
<a name="Folders"> </a>

您可以检索已知其 URL 的文档库中的文件夹。例如，在以下示例中，您可以使用终结点检索您的"共享文档"库的根文件夹。




```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下 XML 显示了当您请求 XML 内容类型时返回的文件夹属性的示例。






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

以下示例演示如何 **创建** 文件夹。






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

以下示例演示如何使用 **MERGE** 方法 **更新** 文件夹。






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

以下示例演示如何 **删除** 文件夹。






```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## 通过 REST 使用文件
<a name="Files"> </a>

以下示例演示如何 **检索** 文件夹中的所有文件。




```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下示例演示如何 **检索** 特定文件。






```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

您还可以 **检索** 已知其 URL 的文件，如以下示例所示。






```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

以下示例演示如何 **创建** 文件并将其添加到文件夹中。






```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body
```

以下示例演示如何使用 **PUT** 方法 **更新** 文件。




> **注释**
> **PUT** 是可用于更新文件的唯一方法。不允许使用 **MERGE** 方法。







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

如果要更新文件的元数据，则需要构建到达文件的终结点作为列表项。可以执行此操作的原因在于每个文件夹也是一个列表，并且每个文件也是一个列表项。构建如下终结点：  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`。 [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md)说明了如何更新列表项的元数据。



您可能希望签出文件以确保在您更新之前没有人更改它。在您更新后，您还可能希望签回文件以便他人使用它。以下示例演示如何 **签出文件** 。






```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

以下示例演示如何 **签入文件** 。






```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

以下示例演示如何 **删除** 文件。






```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"

```


## 使用 REST 处理大文件
<a name="LargeFiles"> </a>

在您需要上载大于 1.5 MB 的二进制文件时，REST 接口是您的唯一选择。有关演示如何使用 SharePoint 2013 Javascript 对象模型上载小于 1.5 MB 的二进制文件的代码示例，请参阅 [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)。使用 REST 可创建的二进制文件的最大大小为 2 GB。下面的示例演示如何 **创建** 大二进制文件。




> **警告**
> 此方法将只适用于 Internet Explorer 10 和其他浏览器的最新版本。 





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

以下代码示例演示如何使用此 REST 端点和跨域库创建文件。






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


## 使用 REST 处理附加到列表项的文件
<a name="FileAttachments"> </a>

以下示例演示如何 **检索** 附加到列表项的所有文件。




```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下示例演示如何 **检索** 附加到列表项的文件。






```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下示例演示如何 **创建** 列表项的文件附件。






```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body
```

以下示例演示如何使用 **PUT** 方法 **更新** 列表项的文件附件。




> **注释**
> **PUT** 是可用于更新文件的唯一方法。不允许使用 **MERGE** 方法。







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


## 其他资源
<a name="bk_addresources"> </a>


-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [文件和文件夹 REST API 引用](2c3d2545-1cd7-497e-b535-12199d8edfbb.md)


-  [使用 REST API 和 jQuery 上载文件](upload-a-file-by-using-the-rest-api-and-jquery.md)


-  [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013：使用 REST 对文件和文件夹执行基本的数据访问操作](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [对 SharePoint 2013 使用 C# 和 JavaScript 进行 REST 调用](http://www.microsoft.com/resources/msdn/zh-cn/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=4e4cc094-ff69-405b-852f-2ac7c41293c5)


-  [对 SharePoint 2013 演示版使用 C# 和 JavaScript 进行 REST 调用](http://www.microsoft.com/resources/msdn/zh-cn/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)


-  [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)


-  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md)


-  [打开数据协议](http://www.odata.org/)


-  [OData：JavaScript 对象表示法 (JSON) 格式](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)







