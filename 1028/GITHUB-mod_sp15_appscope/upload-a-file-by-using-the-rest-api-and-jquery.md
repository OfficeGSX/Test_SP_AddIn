---
title: 使用 REST API 和 jQuery 上傳檔案
ms.prod: SHAREPOINT
ms.assetid: b48a9cb8-131a-4bd4-af4c-a98017ee2e5a
---


# 使用 REST API 和 jQuery 上傳檔案
了解如何使用 REST API 和 jQuery AJAX 要求上傳本機檔案至 SharePoint 資料夾。
本文中的程式碼範例將使用 REST 介面和 jQuery AJAX 要求來新增本機檔案至文件庫，然後變更代表已上傳檔案的清單項目之屬性。
  
    
    

此程序使用下列高階步驟：
1. 使用 **FileReader** API 將本機檔案轉換成陣列緩衝區，需要有 HTML5 支援。 **jQuery(document).ready** 函數會檢查瀏覽器中是否有 FileReader API 支援。
    
  
2. 使用資料夾檔案集合的 **Add** 方法，新增檔案至 [共用文件] 資料夾。陣列緩衝區則在 POST 要求的內文中傳遞。
    
    這些範例使用 **getfolderbyserverrelativeurl** 端點來連線檔案集合，但您也可以使用清單端點 (例如： `…/_api/web/lists/getbytitle('<list title>')/rootfolder/files/add`)。
    
  
3. 使用已上傳檔案的 **ListItemAllFields** 屬性，取得對應至已上傳檔案的清單項目。
    
  
4. 使用 MERGE 要求，變更清單項目的顯示名稱和標題。
    
  

## 執行程式碼範例
<a name="RunTheExamples"> </a>

本文的兩個程式碼範例使用 REST API 和 jQuery AJAX 要求來上傳檔案至 [共用文件] 資料夾，然後變更清單項目屬性。第一個範例使用 **SP.AppContextSite** 跨 SharePoint 網域進行呼叫，與 SharePoint 主控的增益集在上載檔案至主機 Web 時的作法相同。第二個範例進行相同網域呼叫，與 SharePoint 主控的增益集在上載檔案至增益集 Web 時，或者於伺服器上執行之解決方案在上載檔案時的作法相同。
  
    
    

> **注意事項**
> 在 JavaScript 中撰寫的提供者主控增益集必須使用 SP.RequestExecutor 跨網域程式庫來傳送要求至 SharePoint 網域。如需範例，請參閱 [使用跨網域程式庫上傳檔案](http://msdn.microsoft.com/library/2c3d2545-1cd7-497e-b535-12199d8edfbb%28Office.15%29.aspx#bk_FileCollectionAdd)。 
  
    
    

若要使用本文中的範例，您將需要下列項目：
  
    
    

- SharePoint Server 2013 或 SharePoint Online
    
  
- 對執行程式碼的使用者提供文件庫的 **Write** 權限。如果您開發的是 SharePoint Add-in，您可以在 **List** 範圍指定 **Write** 增益集權限
    
  
- 瀏覽器支援 **FileReader** API (HTML5)
    
  
- 在頁面標記中參考 jQuery 程式庫。例如：
    
  ```HTML
  
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js" type="text/javascript"></script>
  ```

- 頁面標記中的下列控制項。
    
  ```HTML
  <input id="getFile" type="file"/><br />
<input id="displayName" type="text" value="Enter a unique name" /><br />
<input id="addFileButton" type="button" value="Upload" onclick="uploadFile()"/>
  ```


## 程式碼範例 1：使用 REST API 和 jQuery 跨 SharePoint 網域上傳檔案
<a name="RunTheExamples"> </a>

下列程式碼範例使用 SharePoint REST API 和 jQuery AJAX 要求來上傳檔案至文件庫，並變更代表檔案的清單項目屬性。此範例的內容是會上傳檔案至主機 Web 上資料夾的 SharePoint 主控增益集。
  
    
    
您必須符合 [這些需求 ](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples)，才能使用此範例。
  
    
    



```

'use strict';

var appWebUrl, hostWebUrl;

jQuery(document).ready(function () {

    // Check for FileReader API (HTML5) support.
    if (!window.FileReader) {
        alert('This browser does not support the FileReader API.');
    }

    // Get the add-in web and host web URLs.
    appWebUrl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));
    hostWebUrl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
});

// Upload the file.
// You can upload files up to 2 GB with the REST API.
function uploadFile() {

    // Define the folder path for this example.
    var serverRelativeUrlToFolder = '/shared documents';

    // Get test values from the file input and text input page controls.
    // The display name must be unique every time you run the example.
    var fileInput = jQuery('#getFile');
    var newName = jQuery('#displayName').val();

    // Initiate method calls using jQuery promises.
    // Get the local file as an array buffer.
    var getFile = getFileBuffer();
    getFile.done(function (arrayBuffer) {

        // Add the file to the SharePoint folder.
        var addFile = addFileToFolder(arrayBuffer);
        addFile.done(function (file, status, xhr) {

            // Get the list item that corresponds to the uploaded file.
            var getItem = getListItem(file.d.ListItemAllFields.__deferred.uri);
            getItem.done(function (listItem, status, xhr) {

                // Change the display name and title of the list item.
                var changeItem = updateListItem(listItem.d.__metadata);
                changeItem.done(function (data, status, xhr) {
                    alert('file uploaded and updated');
                });
                changeItem.fail(onError);
            });
            getItem.fail(onError);
        });
        addFile.fail(onError);
    });
    getFile.fail(onError);

    // Get the local file as an array buffer.
    function getFileBuffer() {
        var deferred = jQuery.Deferred();
        var reader = new FileReader();
        reader.onloadend = function (e) {
            deferred.resolve(e.target.result);
        }
        reader.onerror = function (e) {
            deferred.reject(e.target.error);
        }
        reader.readAsArrayBuffer(fileInput[0].files[0]);
        return deferred.promise();
    }

    // Add the file to the file collection in the Shared Documents folder.
    function addFileToFolder(arrayBuffer) {

        // Get the file name from the file input control on the page.
        var parts = fileInput[0].value.split('\\\\');
        var fileName = parts[parts.length - 1];

        // Construct the endpoint.
        var fileCollectionEndpoint = String.format(
            "{0}/_api/sp.appcontextsite(@target)/web/getfolderbyserverrelativeurl('{1}')/files" +
            "/add(overwrite=true, url='{2}')?@target='{3}'",
            appWebUrl, serverRelativeUrlToFolder, fileName, hostWebUrl);

        // Send the request and return the response.
        // This call returns the SharePoint file.
        return jQuery.ajax({
            url: fileCollectionEndpoint,
            type: "POST",
            data: arrayBuffer,
            processData: false,
            headers: {
                "accept": "application/json;odata=verbose",
                "X-RequestDigest": jQuery("#__REQUESTDIGEST").val(),
                "content-length": arrayBuffer.byteLength
            }
        });
    }

    // Get the list item that corresponds to the file by calling the file's ListItemAllFields property.
    function getListItem(fileListItemUri) {

        // Construct the endpoint.
        // The list item URI uses the host web, but the cross-domain call is sent to the
        // add-in web and specifies the host web as the context site.
        fileListItemUri = fileListItemUri.replace(hostWebUrl, '{0}');
        fileListItemUri = fileListItemUri.replace('_api/Web', '_api/sp.appcontextsite(@target)/web');
        
        var listItemAllFieldsEndpoint = String.format(fileListItemUri + "?@target='{1}'",
            appWebUrl, hostWebUrl);

        // Send the request and return the response.
        return jQuery.ajax({
            url: listItemAllFieldsEndpoint,
            type: "GET",
            headers: { "accept": "application/json;odata=verbose" }
        });
    }

    // Change the display name and title of the list item.
    function updateListItem(itemMetadata) {

        // Construct the endpoint.
        // Specify the host web as the context site.
        var listItemUri = itemMetadata.uri.replace('_api/Web', '_api/sp.appcontextsite(@target)/web');
        var listItemEndpoint = String.format(listItemUri + "?@target='{0}'", hostWebUrl);

        // Define the list item changes. Use the FileLeafRef property to change the display name. 
        // For simplicity, also use the name as the title.
        // The example gets the list item type from the item's metadata, but you can also get it from the
        // ListItemEntityTypeFullName property of the list.
        var body = String.format("{{'__metadata':{{'type':'{0}'}},'FileLeafRef':'{1}','Title':'{2}'}}",
            itemMetadata.type, newName, newName);

        // Send the request and return the promise.
        // This call does not return response content from the server.
        return jQuery.ajax({
            url: listItemEndpoint,
            type: "POST",
            data: body,
            headers: {
                "X-RequestDigest": jQuery("#__REQUESTDIGEST").val(),
                "content-type": "application/json;odata=verbose",
                "content-length": body.length,
                "IF-MATCH": itemMetadata.etag,
                "X-HTTP-Method": "MERGE"
            }
        });
    }
}

// Display error messages. 
function onError(error) {
    alert(error.responseText);
}

// Get parameters from the query string.
// For production purposes you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
    var params = document.URL.split("?")[1].split("&amp;");
    for (var i = 0; i < params.length; i = i + 1) {
        var singleParam = params[i].split("=");
        if (singleParam[0] == paramToRetrieve) return singleParam[1];
    }
}
```


## 程式碼範例 2：使用 REST API 和 jQuery 在相同網域中上傳檔案
<a name="UploadFile"> </a>

下列程式碼範例使用 SharePointREST API 和 jQuery AJAX 要求來上傳檔案至文件庫並變更代表檔案的清單項目屬性。此範例的內容是在伺服器執行的解決方案。程式碼會類似於將檔案上傳到增益集 Web 的 SharePoint 主控增益集。
  
    
    
您必須符合 [這些需求 ](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples)，才能執行此範例。
  
    
    



```

'use strict';

jQuery(document).ready(function () {

    // Check for FileReader API (HTML5) support.
    if (!window.FileReader) {
        alert('This browser does not support the FileReader API.');
    }
});

// Upload the file.
// You can upload files up to 2 GB with the REST API.
function uploadFile() {

    // Define the folder path for this example.
    var serverRelativeUrlToFolder = '/shared documents';

    // Get test values from the file input and text input page controls.
    var fileInput = jQuery('#getFile');
    var newName = jQuery('#displayName').val();

    // Get the server URL.
    var serverUrl = _spPageContextInfo.webAbsoluteUrl;

    // Initiate method calls using jQuery promises.
    // Get the local file as an array buffer.
    var getFile = getFileBuffer();
    getFile.done(function (arrayBuffer) {

        // Add the file to the SharePoint folder.
        var addFile = addFileToFolder(arrayBuffer);
        addFile.done(function (file, status, xhr) {

            // Get the list item that corresponds to the uploaded file.
            var getItem = getListItem(file.d.ListItemAllFields.__deferred.uri);
            getItem.done(function (listItem, status, xhr) {

                // Change the display name and title of the list item.
                var changeItem = updateListItem(listItem.d.__metadata);
                changeItem.done(function (data, status, xhr) {
                    alert('file uploaded and updated');
                });
                changeItem.fail(onError);
            });
            getItem.fail(onError);
        });
        addFile.fail(onError);
    });
    getFile.fail(onError);

    // Get the local file as an array buffer.
    function getFileBuffer() {
        var deferred = jQuery.Deferred();
        var reader = new FileReader();
        reader.onloadend = function (e) {
            deferred.resolve(e.target.result);
        }
        reader.onerror = function (e) {
            deferred.reject(e.target.error);
        }
        reader.readAsArrayBuffer(fileInput[0].files[0]);
        return deferred.promise();
    }

    // Add the file to the file collection in the Shared Documents folder.
    function addFileToFolder(arrayBuffer) {

        // Get the file name from the file input control on the page.
        var parts = fileInput[0].value.split('\\\\');
        var fileName = parts[parts.length - 1];

        // Construct the endpoint.
        var fileCollectionEndpoint = String.format(
                "{0}/_api/web/getfolderbyserverrelativeurl('{1}')/files" +
                "/add(overwrite=true, url='{2}')",
                serverUrl, serverRelativeUrlToFolder, fileName);

        // Send the request and return the response.
        // This call returns the SharePoint file.
        return jQuery.ajax({
            url: fileCollectionEndpoint,
            type: "POST",
            data: arrayBuffer,
            processData: false,
            headers: {
                "accept": "application/json;odata=verbose",
                "X-RequestDigest": jQuery("#__REQUESTDIGEST").val(),
                "content-length": arrayBuffer.byteLength
            }
        });
    }

    // Get the list item that corresponds to the file by calling the file's ListItemAllFields property.
    function getListItem(fileListItemUri) {

        // Send the request and return the response.
        return jQuery.ajax({
            url: fileListItemUri,
            type: "GET",
            headers: { "accept": "application/json;odata=verbose" }
        });
    }

    // Change the display name and title of the list item.
    function updateListItem(itemMetadata) {

        // Define the list item changes. Use the FileLeafRef property to change the display name. 
        // For simplicity, also use the name as the title. 
        // The example gets the list item type from the item's metadata, but you can also get it from the
        // ListItemEntityTypeFullName property of the list.
        var body = String.format("{{'__metadata':{{'type':'{0}'}},'FileLeafRef':'{1}','Title':'{2}'}}",
            itemMetadata.type, newName, newName);

        // Send the request and return the promise.
        // This call does not return response content from the server.
        return jQuery.ajax({
            url: itemMetadata.uri,
            type: "POST",
            data: body,
            headers: {
                "X-RequestDigest": jQuery("#__REQUESTDIGEST").val(),
                "content-type": "application/json;odata=verbose",
                "content-length": body.length,
                "IF-MATCH": itemMetadata.etag,
                "X-HTTP-Method": "MERGE"
            }
        });
    }
}

// Display error messages. 
function onError(error) {
    alert(error.responseText);
}
```


## 其他資源
<a name="bk_addresources"> </a>


-  [若要了解 SharePoint 2013 REST 服務取得](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [使用資料夾與其餘的檔案](working-with-folders-and-files-with-rest.md)
    
  
-  [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [REST API 參考和範例](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

