---
title: Trabalhando com pastas e arquivos com REST
ms.prod: SHAREPOINT
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# Trabalhando com pastas e arquivos com REST
Saiba como realizar básica criar, ler, atualizar e excluir operações CRUD () em pastas e arquivos com a interface REST do SharePoint 2013.
> **DICA**
> SharePoint Online (e no local SharePoint 2016 e posterior) combinando várias solicitações em uma única chamada para o serviço usando a opção de consulta OData  `$batch` oferece suporte a serviço REST. Para obter detalhes e links para exemplos de código, consulte [Fazem solicitações de lote com as APIs REST](make-batch-requests-with-the-rest-apis.md).
  
    
    


## Trabalhando com pastas usando REST
<a name="Folders"> </a>

É possível recuperar uma pasta dentro de uma biblioteca de documentos quando souber seu URL. Por exemplo, é possível recuperar a pasta raiz da sua biblioteca de documentos compartilhados usando o ponto de extremidade no exemplo a seguir.
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

O XML a seguir mostra um exemplo das propriedades de pasta que serão retornados quando você solicitar o tipo de conteúdo de XML.
  
    
    



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

O exemplo a seguir mostra como **criar** uma pasta.
  
    
    



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

O exemplo a seguir mostra como **Atualizar** uma pasta usando o método **MERGE**.
  
    
    



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

O exemplo a seguir mostra como **Excluir** uma pasta.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## Trabalhando com arquivos usando REST
<a name="Files"> </a>

O exemplo a seguir mostra como **recuperar** todos os arquivos em uma pasta.
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

O exemplo a seguir mostra como **recuperar** um arquivo específico.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

Você também pode **recuperar** um arquivo quando souber seu URL, como no exemplo a seguir.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

O exemplo a seguir mostra como **criar** um arquivo e adicioná-lo para uma pasta.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body
```

O exemplo a seguir mostra como **Atualizar** um arquivo usando o método **PUT**.
  
    
    

> **OBSERVAçãO**
> **Colocar** é o único método que você pode usar para atualizar um arquivo. O método **MERGE** não é permitido.
  
    
    




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

Se você deseja atualizar os metadados de um arquivo, você terá que construir um ponto de extremidade que atinge o arquivo como um item de lista. Você pode fazer isso, porque cada pasta também é uma lista, e cada arquivo também é um item de lista. Construir um ponto de extremidade que tem esta aparência:  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`.  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md) explica como atualizar os metadados do item de lista.
  
    
    
Convém fazer check-out de um arquivo para certificar-se de que ninguém o altera antes de você atualizá-lo. Após a atualização, você deve também pode deseja verificar o arquivo novamente para que outras pessoas possam trabalhar com ele. O exemplo a seguir mostra como **Verificar um arquivo check-out**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

O exemplo a seguir mostra como **Verificar um arquivo**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

O exemplo a seguir mostra como **Excluir** um arquivo.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"

```


## Trabalhando com arquivos grandes usando REST
<a name="LargeFiles"> </a>

Quando você precisa carregar um arquivo binário que for maior do que 1,5 MB (Megabytes), a interface REST é a única opção. Consulte  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) para obter um exemplo de código que mostra como carregar um arquivo binário que for menor do que 1,5 MB usando o modelo de objeto Javascript SharePoint 2013. O tamanho máximo de um arquivo binário que você pode criar com o REST é 2 gigabytes (GB). O exemplo a seguir mostra como **criar** um grande arquivo binário.
  
    
    

> **CUIDADO**
> Essa abordagem funcionará apenas com o Internet Explorer 10 e as versões mais recentes de outros navegadores.
  
    
    


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

O exemplo de código a seguir mostra como criar um arquivo usando esse ponto de extremidade do REST e a biblioteca de domínio cruzado.
  
    
    



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


## Trabalhando com arquivos anexados aos itens de lista usando REST
<a name="FileAttachments"> </a>

O exemplo a seguir mostra como **recuperar** todos os arquivos que estejam anexados a um item de lista.
  
    
    

```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

A exemplo a seguir mostra como **recuperar** um arquivo que é anexado a um item de lista.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

O exemplo a seguir mostra como **criar** um anexo de arquivo a um item de lista.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body
```

O exemplo a seguir mostra como **Atualizar** um anexo de arquivo a uma lista de itens usando o método **PUT**.
  
    
    

> **OBSERVAçãO**
> **Colocar** é o único método que você pode usar para atualizar um arquivo. O método **MERGE** não é permitido.
  
    
    




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


## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Referência de API REST de arquivos e pastas](http://msdn.microsoft.com/library/2c3d2545-1cd7-497e-b535-12199d8edfbb%28Office.15%29.aspx)
    
  
-  [Carregar um arquivo usando a API REST e jQuery](upload-a-file-by-using-the-rest-api-and-jquery.md)
    
  
-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: executar operações de acesso de dados básicos em arquivos e pastas usando o REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Chamadas fazendo REST com c# e JavaScript para SharePoint 2013](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=4e4cc094-ff69-405b-852f-2ac7c41293c5)
    
  
-  [Chamadas fazendo REST com c# e JavaScript para a demonstração do SharePoint 2013](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)
    
  
-  [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Protocolo Open Data](http://www.odata.org/)
    
  
-  [OData: objeto JavaScript Notation (JSON) formato](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  

  
    
    

