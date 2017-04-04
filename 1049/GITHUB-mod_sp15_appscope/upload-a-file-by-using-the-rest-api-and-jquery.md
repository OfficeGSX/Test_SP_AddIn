---
title: Отправка файла с помощью API REST и jQuery
ms.prod: SHAREPOINT
ms.assetid: b48a9cb8-131a-4bd4-af4c-a98017ee2e5a
---


# Отправка файла с помощью API REST и jQuery
Узнайте, как загрузить локальный файл в папку SharePoint с помощью API REST и запросов jQuery AJAX.
В примерах кода в этой статье интерфейс REST и запросы jQuery AJAX используются для добавления локального файла в библиотеку **Документы** и изменения свойств элемента списка, который представляет загруженный файл.
  
    
    

Этот процесс состоит из следующих высокоуровневых шагов.
1. Преобразование локального файла в буфер массива с помощью **FileReader** API, для чего необходима поддержка HTML5. Функция **jQuery(document).ready** проверяет поддержку FileReader API в браузере.
    
  
2. Добавление файла в папку **Общие документы** с помощью метода **Add** в коллекции файлов папки. Буфер массива передается в тексте запроса POST.
    
    В этих примерах для доступа к коллекции файлов используется конечная точка **getfolderbyserverrelativeurl**, но также можно воспользоваться конечной точкой списка (например,  `…/_api/web/lists/getbytitle('<list title>')/rootfolder/files/add`).
    
  
3. Получение элемента списка, соответствующего загруженному файлу, с помощью свойства **ListItemAllFields** файла.
    
  
4. Изменение отображаемого имени и название элемента списка с помощью запроса на MERGE.
    
  

## Запуск примеров кода
<a name="RunTheExamples"> </a>

В обоих примерах кода в этой статье API REST и запросы jQuery AJAX используются для загрузки файла в папку **Общие документы** и изменения свойств элемента списка. В первом примере **SP.AppContextSite** используется для вызовов в доменах SharePoint, как надстройка с размещением в SharePoint для отправки файлов на хост-сайт. Второй пример осуществляет вызовы в одном домене, как надстройка с размещением в SharePoint для отправки файлов в надстройку или Интернет или как решение, работающее на сервере, при отправке файлов.
  
    
    

> **Примечание**
> Надстройки с размещением у поставщика, написанные на JavaScript, должны использовать междоменную библиотеку SP.RequestExecutor для отправки запросов в домен SharePoint. Пример, см. в разделе  [Загрузка файла с помощью междоменной библиотеки](2c3d2545-1cd7-497e-b535-12199d8edfbb.md#bk_FileCollectionAdd). 
  
    
    

Для использования примеров, описанных в этой статье, вам необходимо следующее:
  
    
    

- SharePoint Server 2013 или SharePoint Online.
    
  
- Разрешения **Write** для библиотеки **Документы** для пользователя, выполняющего код. Если вы создаете Надстройка SharePoint, можно указать разрешения надстройки **Write** в области **List**.
    
  
- Браузер, поддерживающий **FileReader** API (HTML5).
    
  
- Ссылка на библиотеку jQuery в разметке страницы. Например:
    ```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js" type="text/javascript"></script>```

- Следующие элементы управления в разметке страницы.
    ```HTML
<input id="getFile" type="file"/><br />
<input id="displayName" type="text" value="Enter a unique name" /><br />
<input id="addFileButton" type="button" value="Upload" onclick="uploadFile()"/>```


## Пример кода 1. Отправка файлов в доменах SharePoint с помощью API REST и jQuery
<a name="RunTheExamples"> </a>

Следующий пример кода использует API REST SharePoint и запросов jQuery AJAX для загрузки файла в библиотеку **Документы** и изменения свойств элемента списка, представляющего файл. Контекст этого примера  размещенное в SharePoint надстройку, которая загружает файл в папку на хост-сервере.
  
    
    
Для использования этого примера необходимо выполнение  [этих требований](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples).
  
    
    


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
}```


## Пример кода 2. Отправка файлов в одном домене с помощью API REST и jQuery
<a name="UploadFile"> </a>

Следующий пример кода использует API REST SharePoint и запросов jQuery AJAX для загрузки файла в библиотеку **Документы** и изменения свойств элемента списка, представляющего файл. Контекст этого примера  решение, работающее на сервере. Код аналогичен коду надстройки с размещением в SharePoint, которое загружает файл на сайт надстройки.
  
    
    
Для использования этого примера необходимо выполнение  [этих требований](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples).
  
    
    


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
}```


## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Знакомство со службой REST для SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Работа с папками и файлами в службе REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint 2013 REST API, конечные точки и примеры](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Обращение к данным SharePoint 2013 из надстроек с помощью междоменной библиотеки](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

