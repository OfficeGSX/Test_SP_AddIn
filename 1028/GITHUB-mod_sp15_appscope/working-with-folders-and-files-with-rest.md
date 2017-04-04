---
title: 使用資料夾與其餘的檔案
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# 使用資料夾與其餘的檔案
了解如何執行基本建立、 讀取、 更新及刪除資料夾和檔案與SharePoint 2013 REST 介面 (CRUD) 作業。
> [!秘訣]
> SharePoint Online (及內部SharePoint 2016年及更新版本) REST 服務可支援合併成單一呼叫服務的多個要求使用 OData  `$batch`查詢選項。 詳細資料與程式碼範例的連結，請參閱 [請使用 REST Api 的批次要求](make-batch-requests-with-the-rest-apis.md)。
  
    
    


## 使用 REST 使用資料夾
<a name="Folders"> </a>

您可以擷取的資料夾內的文件庫時您知道其 URL。例如，您可以在下列範例中使用之端點擷取共享文件庫之根的資料夾。
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列 XML 顯示當您要求的 XML 內容類型時所傳回之資料夾屬性的範例。
  
    
    



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

下列範例顯示如何 **建立** 資料夾。
  
    
    



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

下列範例顯示如何 **更新** 使用 **MERGE**方法的資料夾。
  
    
    



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

下列範例顯示如何 **刪除** 資料夾。
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## 使用 REST 使用的檔案
<a name="Files"> </a>

下列範例顯示如何 **擷取** 所有資料夾中的檔案。
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列範例顯示如何 **擷取** 特定的檔案。
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

您也可以 **擷取** 檔案時您知道其 URL，如下列範例所示。
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

下列範例顯示如何 **建立** 檔案並將其新增至資料夾。
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body
```

下列範例顯示如何 **更新** 使用 **放入** 方法的檔案。
  
    
    

> [!注意事項]
> **放入** 是唯一的方法可用來更新檔案。不允許 **合併** 方法。
  
    
    




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

如果您想要更新的檔案的中繼資料，您必須建構達到為清單項目之檔案的端點。因為每個資料夾也是一個清單，而且每個檔案也是清單項目您可以這麼做。建構端點看起來像這樣：  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`。 [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)說明如何更新清單項目的中繼資料。
  
    
    
若要以確保的任何人變更它在更新之前先取出檔案。在您更新後應也可能會想要檢查檔案回，讓其他人可以使用它。下列範例會顯示您如何 **檢查取出的檔案** 。
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

下列範例會顯示您如何 **檢查中的檔案** 。
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

下列範例顯示如何 **刪除** 檔案。
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"

```


## 使用 REST 使用大型檔案
<a name="LargeFiles"> </a>

當您要上傳大於 1.5 mb (MB) 的二進位檔案時、 REST 介面是唯一的選擇。請參閱 [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)的程式碼範例會示範如何上傳小於 1.5 MB 使用SharePoint 2013 Javascript 物件模型的二進位檔案。您可以建立使用 REST 的二進位檔案的大小上限為 2 gb。下列範例顯示如何 **建立** 大型的二進位檔案。
  
    
    

> [!注意]
> 這個方法將會使用僅 Internet Explorer 10 和其他瀏覽器的最新版本。
  
    
    


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

下列程式碼範例示範如何建立使用此 REST 端點與跨網域文件庫的檔案。
  
    
    



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


## 使用附加至清單項目使用 REST 的檔案
<a name="FileAttachments"> </a>

下列範例顯示如何 **擷取** 所有檔案附加到清單項目。
  
    
    

```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列範例顯示如何 **擷取** 檔案附加到清單項目。
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列範例顯示如何 **建立** 清單項目檔案附件。
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body
```

下列範例顯示如何 **更新** 檔案附件的清單項目使用 **放入** 方法。
  
    
    

> [!注意事項]
> **放入** 是唯一的方法可用來更新檔案。不允許 **合併** 方法。
  
    
    




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


## 其他資源
<a name="bk_addresources"> </a>


-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [檔案及資料夾的 REST API 參考資料](http://msdn.microsoft.com/library/2c3d2545-1cd7-497e-b535-12199d8edfbb%28Office.15%29.aspx)
    
  
-  [使用 REST API 和 jQuery 上傳檔案](upload-a-file-by-using-the-rest-api-and-jquery.md)
    
  
-  [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013： 使用 REST 執行基本的資料存取作業的檔案及資料夾](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [以 C# 及 JavaScript 為 SharePoint 2013 進行的其餘呼叫](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=4e4cc094-ff69-405b-852f-2ac7c41293c5)
    
  
-  [使用 C# 及 JavaScript 為 SharePoint 2013 示範版來呼叫進行 REST](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)
    
  
-  [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [開啟資料通訊協定](http://www.odata.org/)
    
  
-  [OData: JavaScript 物件 Notation (JSON) 格式](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  

  
    
    

