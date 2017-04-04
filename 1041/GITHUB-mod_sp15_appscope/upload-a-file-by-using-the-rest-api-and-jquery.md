---
title: REST API および jQuery を使用してファイルをアップロードする
ms.prod: SHAREPOINT
ms.assetid: b48a9cb8-131a-4bd4-af4c-a98017ee2e5a
---


# REST API および jQuery を使用してファイルをアップロードする
REST API および jQuery AJAX 要求を使用して、ローカル ファイルを SharePoint フォルダーにアップロードする方法について説明します。
この記事のコード例では、ローカル ファイルを **ドキュメント** ライブラリに追加してから、アップロードしたファイルを表すリスト項目を変更するのに、REST インターフェイスと jQuery AJAX 要求を使用します。
  
    
    

この処理は概ね次のような手順で行われます。
1. **FileReader** API (HTML5 のサポートが必要) を使用して、ローカル ファイルを配列バッファーに変換します。 **jQuery(document).ready** 関数により、ブラウザーで FileReader API がサポートされているかどうかを確認します。
    
  
2. フォルダーのファイル コレクションに **Add** メソッドを使用し、ファイルを **共有ドキュメント** フォルダーに追加します。配列バッファーは POST 要求の本文で渡されます。
    
    これらの例ではファイル コレクションに到達するために **getfolderbyserverrelativeurl** エンドポイントを使用しますが、リスト エンドポイント (例: `…/_api/web/lists/getbytitle('<list title>')/rootfolder/files/add`) も使用できます。
    
  
3. アップロードしたファイルの **ListItemAllFields** プロパティを使用して、アップロードしたファイルに対応するリスト項目を取得します。
    
  
4. MERGE 要求を使用して、リスト項目の表示名とタイトルを変更します。
    
  

## コード例の実行
<a name="RunTheExamples"> </a>

この記事の両方のコード例では、ファイルを **共有ドキュメント** フォルダーにアップロードしてリスト項目のプロパティを変更するのに REST API と jQuery AJAX 要求を使用します。最初の例は、SharePoint ホスト型アドインでファイルをホスト Web にアップロードする際に行うように、 **SP.AppContextSite** を使用して複数の SharePoint ドメインにまたがって呼び出しを行います。2 つ目の例では、SharePoint でホストされるアドインがファイルをアドイン Web にアップロードするときのように、または、サーバー上で実行されているソリューションがファイルをアップロードするときのように、同一ドメイン呼び出しを行います。
  
    
    

> **メモ**
> JavaScript で作成されたプロバイダー向けのホスト型アドインは、SP.RequestExecutor クロスドメイン ライブラリを使用して要求を SharePoint ドメインに送信する必要があります。例については、「 [クロスドメイン ライブラリを使用してファイルをアップロードする](2c3d2545-1cd7-497e-b535-12199d8edfbb.md#bk_FileCollectionAdd)」を参照してください。 
  
    
    

この記事の例を使用するには、次のものが必要です。
  
    
    

- SharePoint Server 2013 または SharePoint Online
    
  
- コードを実行するユーザーの、 **ドキュメント** ライブラリに対する **Write**権限。SharePoint アドイン を開発している場合は、 **List** スコープでアドイン **Write**権限を指定できます。
    
  
- **FileReader** API (HTML5) 対応のブラウザー
    
  
- ページ マークアップでの jQuery ライブラリの参照。次に例を示します。
    
  ```HTML
  
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js" type="text/javascript"></script>
  ```

- ページ マークアップでは次のように制御します。
    
  ```HTML
  <input id="getFile" type="file"/><br />
<input id="displayName" type="text" value="Enter a unique name" /><br />
<input id="addFileButton" type="button" value="Upload" onclick="uploadFile()"/>
  ```


## コード例 1: REST API と jQuery を使用して、ファイルを複数の SharePoint ドメインにまたがってアップロードする
<a name="RunTheExamples"> </a>

次のコード例では SharePoint REST API および jQuery AJAX 要求を使用してファイルを **ドキュメント** ライブラリにアップロードし、ファイルを表すリスト項目のプロパティを変更します。この例のコンテキストは、ファイルをホスト Web 上のフォルダーにアップロードする SharePoint でホスト型アドインのものです。
  
    
    
この例を使用するには、 [これらの要件](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples)を満たす必要があります。
  
    
    



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


## コード例 2: REST API と jQuery を使用して、ファイルを同一ドメインにアップロードする
<a name="UploadFile"> </a>

次のコード例では SharePoint REST API および jQuery AJAX 要求を使用してファイルを **ドキュメント** ライブラリにアップロードし、ファイルを表すリスト項目のプロパティを変更します。この例のコンテキストは、サーバー上で実行されているソリューションのものです。このコードは、ファイルをアドイン Web にアップロードする SharePoint ホスト型アドインのものと似ています。
  
    
    
この例を実行する前に、 [これらの要件](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples)を満たす必要があります。
  
    
    



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


## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 REST サービスの概要](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [REST を使用してフォルダーとファイルを操作する](working-with-folders-and-files-with-rest.md)
    
  
-  [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint 2013 REST API、エンドポイント、およびサンプル](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

