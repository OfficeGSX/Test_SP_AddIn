---
title: Hochladen einer Datei mit der REST-API und jQuery
ms.prod: SHAREPOINT
ms.assetid: b48a9cb8-131a-4bd4-af4c-a98017ee2e5a
---


# Hochladen einer Datei mit der REST-API und jQuery
Erfahren Sie, wie Sie eine lokale Datei mit der REST-API und jQuery AJAX-Anforderungen in einen SharePoint-Ordner hochladen.
Die Codebeispiele in diesem Artikel verwenden die REST-Schnittstelle und jQuery AJAX-Anforderungen, um eine lokale Datei zur **Dokument**bibliothek hinzuzufügen und ändern dann die Eigenschaften des Listenelements, dass die hochgeladene Datei repräsentiert.
  
    
    

Dieser Prozess verwendet die folgenden allgemeinen Schritte:
1. Konvertieren Sie die lokale Datei mit der **FileReader**-API in einen Arraypuffer, dafür ist Unterstützung von HTML5 erforderlich. Die **jQuery(document).ready**-Funktion überprüft den Browser auf FileReader API-Unterstützung.
    
  
2. Fügen Sie die Datei mithilfe der **Add**-Methode in der Dateisammlung des Ordners zum Ordner **Freigegebene Elemente** hinzu. Der Arraypuffer wird im Text der POST-Anforderung übergeben.
    
    Diese Beispiele verwenden den **getfolderbyserverrelativeurl**-Endpunkt, um die Dateisammlung zu erreichen, Sie können aber auch einen Listenendpunkt verwenden (Beispiel:  `…/_api/web/lists/getbytitle('<list title>')/rootfolder/files/add`).
    
  
3. Rufen Sie das Listenelement, das der hochgeladenen Datei entspricht, mithilfe der **ListItemAllFields**-Eigenschaft der hochgeladenen Datei ab.
    
  
4. Ändern Sie den Anzeigenamen und den Titel des Listenelements mit einer MERGE-Anforderung.
    
  

## Ausführen von Codebeispielen
<a name="RunTheExamples"> </a>

Beide Codebeispiele in diesem Artikel verwenden die REST-API und jQuery AJAX-Anforderungen, um eine Datei in den Ordner **Freigegebene Elemente** hochzuladen und dann die Listenelementeigenschaften zu ändern. Das erste Beispiel verwendet **SP.AppContextSite**, um SharePoint-domänenübergreifende Aufrufe auszuführen, z. B. ein in SharePoint gehostetes Add-In beim Hochladen von Dateien in das Hostweb. Das zweite Beispiel führt Aufrufe auf der gleichen Domäne aus, z. B. ein in SharePoint gehostetes Add-In beim Hochladen von Dateien in das Add-In-Web oder eine Lösung, die beim Hochladen der Dateien auf dem Server ausgeführt wird.
  
    
    

> **HINWEIS**
> In JavaScript geschriebene vom Anbieter gehostete Add-Ins müssen die domänenübergreifende SP.RequestExecutor-Bibliothek verwenden, um Anforderungen an eine SharePoint-Domäne zu senden. Ein Beispiel dazu finden Sie unter  [Hochladen einer Datei mithilfe der domänenübergreifenden Bibliothek](2c3d2545-1cd7-497e-b535-12199d8edfbb.md#bk_FileCollectionAdd). 
  
    
    

Um die Beispiele in diesem Artikel zu verwenden, benötigen Sie Folgendes:
  
    
    

- SharePoint Server 2013 oder SharePoint Online
    
  
- **Write**-Berechtigungen für die **Dokument**bibliothek für den Benutzer, der den Code ausführt. Wenn Sie ein SharePoint-Add-In entwickeln, können Sie die **Write**-Add-In-Berechtigungen im Bereich **List** angeben
    
  
- Browserunterstützung für die **FileReader**-API (HTML5)
    
  
- Ein Verweis zur jQuery-Bibliothek library in Ihrem Seitenmarkup. Beispiel:
    
  ```HTML
  
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js" type="text/javascript"></script>
  ```

- Die folgenden Steuerelemente in Ihrem Seitenmarkup.
    
  ```HTML
  <input id="getFile" type="file"/><br />
<input id="displayName" type="text" value="Enter a unique name" /><br />
<input id="addFileButton" type="button" value="Upload" onclick="uploadFile()"/>
  ```


## Codebeispiel 1: Laden Sie eine SharePoint-domänenübergreifende Datei mit der REST-API und jQuery hoch
<a name="RunTheExamples"> </a>

Das folgende Codebeispiel verwendet die SharePoint-REST API und jQuery AJAX-Anforderungen, um eine Datei in die **Dokument**bibliothek hochzuladen und die Eigenschaften des Listenelements zu ändern, das die Datei repräsentiert. Der Kontext für dieses Beispiel ist ein in SharePoint gehostetes Add-In, das eine Datei in einen Ordner im Hostweb hochlädt.
  
    
    
Um dieses Beispiel verwenden zu können, müssen Sie  [diese Anforderungen](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples) erfüllen.
  
    
    



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


## Codebeispiel 2: Laden Sie eine Datei mit der REST-API und jQuery auf die gleiche Domäne hoch
<a name="UploadFile"> </a>

Das folgende Codebeispiel verwendet die SharePoint-REST-API und jQuery AJAX-Anforderungen, um eine Datei in die **Dokument**bibliothek hochzuladen und die Eigenschaften des Listenelements zu ändern, das die Datei repräsentiert. Der Kontext für dieses Beispiel ist eine Lösung, die auf dem Server ausgeführt wird. Der Code wäre in einem in SharePoint gehosteten Add-In ähnlich, das Dateien in das Add-In-Web hochlädt.
  
    
    
Bevor Sie dieses Beispiel ausführen können, müssen Sie  [diese Anforderungen](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples) erfüllen.
  
    
    



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


## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Einführung in den SharePoint 2013 REST-Dienst](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Arbeiten mit Ordnern und Dateien mit REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint 2013 - REST-API, Endpunkte und Beispiele](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

