---
title: Carregar um arquivo usando a API REST e jQuery
ms.prod: SHAREPOINT
ms.assetid: b48a9cb8-131a-4bd4-af4c-a98017ee2e5a
---


# Carregar um arquivo usando a API REST e jQuery
Saiba como carregar um arquivo local em uma pasta SharePoint usando a API REST e solicitações AJAX jQuery.
Os exemplos de código deste artigo usam a interface REST e as solicitações AJAX jQuery para adicionar um arquivo local à biblioteca **Documentos** e então alteram as propriedades do item de lista que representa o arquivo carregado.
  
    
    

Este processo usa as seguintes etapas de alto nível:
1. Converter o arquivo local em um buffer de matriz usando a API **FileReader**, o que exige suporte do HTML5. A função **jQuery(document).ready** verifica o suporte à API FileReader no navegador.
    
  
2. Adicionar o arquivo à pasta **Documentos Compartilhados** usando o método **Add** no conjunto de arquivos da pasta. O buffer de matriz é passado no corpo da solicitação POST.
    
    Estes exemplos usam o ponto de extremidade **getfolderbyserverrelativeurl** para alcançar o conjunto de arquivos, mas você também pode usar um ponto de extremidade de lista (exemplo: `…/_api/web/lists/getbytitle('<list title>')/rootfolder/files/add`).
    
  
3. Obtenha o item de lista que corresponde ao arquivo carregado usando a propriedade **ListItemAllFields** do arquivo carregado.
    
  
4. Altere o nome de exibição e o título do item de lista usando uma solicitação MERGE.
    
  

## Executando os exemplos de código
<a name="RunTheExamples"> </a>

Os dois exemplos de código deste artigo usam a API REST e as solicitações AJAX jQuery para carregar um arquivo na pasta **Documentos Compartilhados** e alteram as propriedades do item de lista. O primeiro exemplo usa **SP.AppContextSite** para fazer chamadas em domínios do SharePoint, como um suplement hospedado pelo SharePoint faria ao carregar arquivos no host do site. O segundo exemplo faz chamadas de mesmo domínio, como um suplemento hospedado pelo SharePoint faria ao carregar arquivos no site do suplemento ou como uma solução em execução no servidor faria ao carregar arquivos.
  
    
    

> [!OBSERVAçãO]
> Os suplementos hospedados pelo provedor gravados no JavaScript devem usar a biblioteca SP.RequestExecutor entre domínios para enviar solicitações a um domínio do SharePoint. Para obter um exemplo, confira  [Carregar um arquivo usando a biblioteca de domínio cruzado](http://msdn.microsoft.com/library/2c3d2545-1cd7-497e-b535-12199d8edfbb%28Office.15%29.aspx#bk_FileCollectionAdd). 
  
    
    

Para usar os exemplos deste artigo, você precisará do seguinte:
  
    
    

- SharePoint Server 2013 ou SharePoint Online
    
  
- Permissões **Write** para a biblioteca **Documentos** para o usuário que estiver executando o código. Se você estiver desenvolvendo um Suplemento do SharePoint, pode especificar permissões **Write** de suplemento no escopo **List**
    
  
- Suporte do navegador para a API **FileReader** (HTML5)
    
  
- Uma referência para a biblioteca jQuery em sua marcação de página. Por exemplo:
    
  ```HTML
  
<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js" type="text/javascript"></script>
  ```

- Os controles a seguir em sua marcação de página.
    
  ```HTML
  <input id="getFile" type="file"/><br />
<input id="displayName" type="text" value="Enter a unique name" /><br />
<input id="addFileButton" type="button" value="Upload" onclick="uploadFile()"/>
  ```


## Exemplo de código 1: carregar um arquivo entre domínios do SharePoint usando a API REST e jQuery
<a name="RunTheExamples"> </a>

O exemplo de código a seguir usa a API REST e as solicitações AJAX jQuery para carregar um arquivo na biblioteca SharePoint **Documentos** e para alterar propriedades do item de lista que representa o arquivo. O contexto deste exemplo é um suplemento hospedado no SharePoint, que carrega um arquivo em uma pasta no site do host.
  
    
    
Você precisa atender a  [estes requisitos](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples) para usar este exemplo.
  
    
    



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


## Exemplo de código 2: carregar um arquivo no mesmo domínio usando a API REST e jQuery
<a name="UploadFile"> </a>

O exemplo de código a seguir usa a API REST e as solicitações AJAX jQuery do SharePoint para carregar um arquivo na biblioteca **Documentos** e para alterar propriedades da lista do item de lista que representa o arquivo. O contexto deste exemplo é uma solução em execução no servidor. O código seria semelhante em um suplemento hospedado pelo SharePoint que carrega arquivos no site do suplemento.
  
    
    
Você precisa atender a  [estes requisitos](upload-a-file-by-using-the-rest-api-and-jquery.md#RunTheExamples) antes de executar este exemplo.
  
    
    



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


## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Referência e amostras da API REST](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

