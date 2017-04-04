---
title: Télécharger un fichier en utilisant l'API REST et jQuery
ms.prod: SHAREPOINT
ms.assetid: b48a9cb8-131a-4bd4-af4c-a98017ee2e5a
---


# Télécharger un fichier en utilisant l'API REST et jQuery
Découvrez comment télécharger un fichier local vers un dossier SharePoint à l'aide de l'API REST et des requêtes AJAX jQuery.
Les exemples de code figurant dans cet article utilisent l'interface REST et les requêtes AJAX jQuery pour ajouter un fichier local à la bibliothèque **Documents**, puis modifient les propriétés de l'élément de liste qui représente le fichier téléchargé.
  
    
    

Ce processus suit les étapes de haut niveau ci-dessous :
1. Conversion du fichier local en un tampon de tableau à l'aide de l'API **FileReader**, qui nécessite la prise en charge du format HTML5. La fonction **jQuery(document).ready** vérifie la prise en charge de l'API FileReader dans le navigateur.
    
  
2. Ajout du fichier au dossier **Documents partagés** à l'aide de la méthode **Add** sur la collection de fichiers du dossier. Le tampon de tableau est transmis dans le corps de la demande POST.
    
    Ces exemples utilisent le point de terminaison **getfolderbyserverrelativeurl** pour atteindre la collection de fichiers, mais vous pouvez également utiliser un point de terminaison de liste (par exemple, `…/_api/web/lists/getbytitle('<list title>')/rootfolder/files/add`).
    
  
3. Obtention de l'élément de liste correspondant au fichier téléchargé à l'aide de la propriété **ListItemAllFields** du fichier téléchargé.
    
  
4. Modification du nom d'affichage et du titre de l'élément de liste à l'aide d'une demande MERGE.
    
  

## Exécution des exemples de code
<a name="RunTheExamples"> </a>

Les deux exemples de code figurant dans cet article utilisent l'API REST et des requêtes AJAX jQuery pour télécharger un fichier vers le dossier **Documents partagés**, puis modifier des propriétés d'élément de liste. Le premier exemple utilise **SP.AppContextSite** pour établir des appels entre domaines SharePoint, comme le ferait un complément hébergé par SharePoint lors du téléchargement de fichiers vers le site web hôte. Le deuxième exemple établit des appels dans le même domaine, comme le ferait un complément hébergé par SharePoint lors du téléchargement de fichiers vers le site web de complément, ou comme le ferait une solution exécutée sur le serveur lors du téléchargement de fichiers.
  
    
    

> [!REMARQUE]
> Les compléments en JavaScript hébergés par un fournisseur doivent utiliser la bibliothèque inter-domaines SP.RequestExecutor pour envoyer des demandes vers un domaine SharePoint. Pour obtenir un exemple, voir comment  [télécharger un fichier à l'aide de la bibliothèque inter-domaines](2c3d2545-1cd7-497e-b535-12199d8edfbb.md#bk_FileCollectionAdd). 
  
    
    

Pour utiliser les exemples de cet article, vous avez besoin des éléments suivants :
  
    
    

- SharePoint Server 2013 ou SharePoint Online
    
  
- Les autorisations **Write** sur la bibliothèque **Documents** pour l'utilisateur qui exécute le code. Si vous développez une Complément SharePoint, vous pouvez définir l'étendue des autorisations de complément **Write** sur **List**
    
  
- La prise en charge de l'API **FileReader** (HTML5) par le navigateur
    
  
- Une référence à la bibliothèque jQuery dans le balisage de votre page. Par exemple :
    
  ```HTML
  
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js" type="text/javascript"></script>
  ```

- Les commandes suivantes dans le balisage de votre page.
    
  ```HTML
  <input id="getFile" type="file"/><br />
<input id="displayName" type="text" value="Enter a unique name" /><br />
<input id="addFileButton" type="button" value="Upload" onclick="uploadFile()"/>
  ```


## Exemple de code 1 : télécharger un fichier entre domaines SharePoint à l'aide de l'API REST et de jQuery
<a name="RunTheExamples"> </a>

L'exemple de code suivant utilise l'API REST SharePoint et les requêtes AJAX jQuery pour télécharger un fichier vers la bibliothèque **Documents** et modifier les propriétés de l'élément de liste qui représente le fichier. Cet exemple entre dans le contexte d'un complément hébergé par SharePoint qui télécharge un fichier vers un dossier sur le site web hôte.
  
    
    
Vous devez respecter  [ces exigences](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples) pour pouvoir utiliser cet exemple.
  
    
    



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


## Exemple de code 2 : télécharger un fichier dans le même domaine à l'aide de l'API REST et de jQuery
<a name="UploadFile"> </a>

L'exemple de code suivant utilise l'API REST SharePoint et les requêtes AJAX jQuery pour télécharger un fichier vers la bibliothèque **Documents** et modifier les propriétés de l'élément de liste qui représente le fichier. Cet exemple entre dans le contexte d'une solution exécutée sur le serveur. Le code serait semblable dans un complément hébergé par SharePoint qui télécharge des fichiers vers le site web de complément.
  
    
    
Vous devez respecter  [ces exigences](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples) pour pouvoir exécuter cet exemple.
  
    
    



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


## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Familiarisation avec le service REST SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Utilisation de dossiers et de fichiers à l'aide de l'interface REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Exemples, points de terminaison et API REST pour SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

