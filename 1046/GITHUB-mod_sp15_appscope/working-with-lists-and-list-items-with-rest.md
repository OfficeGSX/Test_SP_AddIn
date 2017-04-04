---
title: Trabalhar com listas e itens de listas com REST
ms.prod: SHAREPOINT
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# Trabalhar com listas e itens de listas com REST
Saiba como realizar operações básicas de criação, gravação, atualização e exclusão (CRUD) em listas e itens de lista com a com a interface REST do SharePoint 2013.
> **DICA**
> O serviço REST do SharePoint Online (e a versão local do SharePoint 2016 e posterior) dá suporte à combinação de várias solicitações em uma única chamada para o serviço usando a opção de consulta  `$batch` do OData. Para obter mais detalhes e links para exemplos de códigos, confira [Fazem solicitações de lote com as APIs REST](make-batch-requests-with-the-rest-apis.md). 
  
    
    


## Pré-requisitos

Este tópico pressupõe que você já está familiarizado com os tópicos  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md) e [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). Ele não fornece trechos de código.
  
    
    

## Recuperando listas e propriedades da lista com o REST
<a name="RetrieveLists"> </a>

A exemplo a seguir mostra como **recuperar** uma lista específica, caso você tenha o GUID.
  
    
    

```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```


> **OBSERVAçãO**
> Use  `application/json;odata=verbose` no cabeçalho `accept` se quiser a resposta em JSON. Use `application/atom+xml` no cabeçalho `accept` se quiser a resposta no formato Atom.
  
    
    

O exemplo a seguir mostra como **recuperar** uma lista específica, caso você tenha o seu título.
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

O XML a seguir mostra um exemplo das propriedades de lista que são retornadas quando você solicita o tipo de conteúdo de XML.
  
    
    



```XML

<content type="application/xml">
  <m:properties>
  <d:AllowContentTypes m:type="Edm.Boolean">true</d:AllowContentTypes> 
  <d:BaseTemplate m:type="Edm.Int32">100</d:BaseTemplate> 
  <d:BaseType m:type="Edm.Int32">0</d:BaseType> 
  <d:ContentTypesEnabled m:type="Edm.Boolean">false</d:ContentTypesEnabled> 
  <d:Created m:type="Edm.DateTime">2012-06-26T23:15:58Z</d:Created> 
  <d:DefaultContentApprovalWorkflowId m:type="Edm.Guid">00000000-0000-0000-0000-000000000000</d:DefaultContentApprovalWorkflowId> 
  <d:Description>A list created by Project Based Retention used to store Project Policy Items.</d:Description> 
  <d:Direction>none</d:Direction> 
  <d:DocumentTemplateUrl m:null="true" /> 
  <d:DraftVersionVisibility m:type="Edm.Int32">0</d:DraftVersionVisibility> 
  <d:EnableAttachments m:type="Edm.Boolean">true</d:EnableAttachments> 
  <d:EnableFolderCreation m:type="Edm.Boolean">false</d:EnableFolderCreation> 
  <d:EnableMinorVersions m:type="Edm.Boolean">false</d:EnableMinorVersions> 
  <d:EnableModeration m:type="Edm.Boolean">false</d:EnableModeration> 
  <d:EnableVersioning m:type="Edm.Boolean">false</d:EnableVersioning> 
  <d:EntityTypeName>ProjectPolicyItemList</d:EntityTypeName> 
  <d:ForceCheckout m:type="Edm.Boolean">false</d:ForceCheckout> 
  <d:HasExternalDataSource m:type="Edm.Boolean">false</d:HasExternalDataSource> 
  <d:Hidden m:type="Edm.Boolean">true</d:Hidden> 
  <d:Id m:type="Edm.Guid">74de3ff3-029c-42f9-bd2a-1e9463def69d</d:Id> 
  <d:ImageUrl>/_layouts/15/images/itgen.gif</d:ImageUrl> 
  <d:IrmEnabled m:type="Edm.Boolean">false</d:IrmEnabled> 
  <d:IrmExpire m:type="Edm.Boolean">false</d:IrmExpire> 
  <d:IrmReject m:type="Edm.Boolean">false</d:IrmReject> 
  <d:IsApplicationList m:type="Edm.Boolean">false</d:IsApplicationList> 
  <d:IsCatalog m:type="Edm.Boolean">false</d:IsCatalog> 
  <d:IsPrivate m:type="Edm.Boolean">false</d:IsPrivate> 
  <d:ItemCount m:type="Edm.Int32">0</d:ItemCount> 
  <d:LastItemDeletedDate m:type="Edm.DateTime">2012-06-26T23:15:58Z</d:LastItemDeletedDate> 
  <d:LastItemModifiedDate m:type="Edm.DateTime">2012-06-26T23:15:59Z</d:LastItemModifiedDate> 
  <d:ListItemEntityTypeFullName>SP.Data.ProjectPolicyItemListItem</d:ListItemEntityTypeFullName> 
  <d:MultipleDataList m:type="Edm.Boolean">false</d:MultipleDataList> 
  <d:NoCrawl m:type="Edm.Boolean">true</d:NoCrawl> 
  <d:ParentWebUrl>/</d:ParentWebUrl> 
  <d:ServerTemplateCanCreateFolders m:type="Edm.Boolean">true</d:ServerTemplateCanCreateFolders> 
  <d:TemplateFeatureId m:type="Edm.Guid">00bfea71-de22-43b2-a848-c05709900100</d:TemplateFeatureId> 
  <d:Title>Project Policy Item List</d:Title> 
  </m:properties>
  </content>
```


> **OBSERVAçãO**
> A **ListItemEntityTypeFullName** propriedade ( **SP.Data.ProjectPolicyItemListItem**, no exemplo anterior) é especialmente importante se você quiser criar e atualizar itens de lista. Este valor deve ser passado como a propriedade **type** nos metadados passados no corpo da solicitação HTTP sempre que você criar e atualizar itens de lista.
  
    
    


## Trabalhar com listas usando o REST
<a name="WorkLists"> </a>

O exemplo a seguir mostra como **criar** uma lista.
  
    
    

```

url: http://site url/_api/web/lists
method: POST
body: { '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true, 'BaseTemplate': 100,
 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

O exemplo a seguir mostra como **atualizar** uma lista usando o método **MERGE**.
  
    
    



```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
body: { '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH": etag or "*"
    X-HTTP-Method: MERGE,
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

O exemplo a seguir mostra como **criar** um **campo personalizado** para uma lista.
  
    
    



```

Url: url: http://site url/_api/web/lists(guid'list GUID')/Fields
Method:POST
Body: { '__metadata': { 'type': 'SP.Field' }, 'Title': 'field title', 'FieldTypeKind': FieldType value,'Required': 'true/false', 'EnforceUniqueValues': 'true/false','StaticName': 'field name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

O exemplo a seguir mostra como **excluir** uma lista.
  
    
    



```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE

```


## Trabalhar com itens de lista usando o REST
<a name="ListItems"> </a>

O exemplo a seguir mostra como **recuperar** todos os itens de uma lista.
  
    
    

> **OBSERVAçãO**
> A opção de consulta OData $skip não funciona ao consultar itens de lista. Em situações de obrigação, é possível usar a opção  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx). 
  
    
    


```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

O exemplo a seguir mostra como **recuperar** um item específico de lista.
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

O XML a seguir mostra um exemplo das propriedades do item de lista que são retornadas quando você solicita o tipo de conteúdo XML.
  
    
    



```XML

<content type="application/xml">
<m:properties> 
<d:FileSystemObjectType m:type="Edm.Int32">0</d:FileSystemObjectType>
<d:Id m:type="Edm.Int32">1</d:Id>
<d:ID m:type="Edm.Int32">1</d:ID>
<d:ContentTypeId>0x010049564F321A0F0543BA8C6303316C8C0F</d:ContentTypeId>
<d:Title>an item</d:Title>
<d:Modified m:type="Edm.DateTime">2012-07-24T22:47:26Z</d:Modified>
<d:Created m:type="Edm.DateTime">2012-07-24T22:47:26Z</d:Created>
<d:AuthorId m:type="Edm.Int32">11</d:AuthorId>
<d:EditorId m:type="Edm.Int32">11</d:EditorId>
<d:OData__UIVersionString>1.0</d:OData__UIVersionString>
<d:Attachments m:type="Edm.Boolean">false</d:Attachments>
<d:GUID m:type="Edm.Guid">eb6850c5-9a30-4636-b282-234eda8b1057</d:GUID>
</m:properties>
</content>
```

O exemplo a seguir mostra como **criar** um item de lista.
  
    
    

> **OBSERVAçãO**
> Para executar esta operação, você precisa conhecer a propriedade **ListItemEntityTypeFullName** da lista e passá-la como o valor de **type** no corpo de solicitação do HTTP.
  
    
    




```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'Test'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

O exemplo a seguir mostra como **atualizar** um item de lista.
  
    
    

> **OBSERVAçãO**
> Para executar esta operação, você precisa conhecer a propriedade **ListItemEntityTypeFullName** da lista e passá-la como o valor de **type** no corpo de solicitação do HTTP.
  
    
    




```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'TestUpdated'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"MERGE",
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

O exemplo a seguir mostra como **excluir** um item de lista.
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## Usando valores ETag para determinar as versões do documento e do item de lista
<a name="Etag"> </a>

O serviço REST do SharePoint, que segue o  [padrão OData](http://www.odata.org/developers/protocols/operations), usa  [ETags HTML para controle de simultaneidade](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags) de listas do SharePoint e itens de lista. Para verificar a versão de um item ao executar uma solicitação **PUT**, **MERGE**, **DELETE**, especifique um **ETag** no cabeçalho de solicitação HTTP **If-Match**.
  
    
    
Se o **ETag** especificado na solicitação não corresponder ao **ETag** do documento ou item de lista no servidor, o serviço REST retornará uma exceção 412, de acordo com a especificação de OData.
  
    
    

- Para forçar uma substituição do item independentemente da versão, defina o valor **ETag** para **"*"**.
    
  
- Se você não especificar um **ETag**, o SharePoint substitui o item independentemente da versão.
    
  
No SharePoint, as ETags se aplicam apenas a listas do SharePoint e itens de lista.
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: Realizar operações básicas de acesso a dados em arquivos e pastas usando REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Fazendo chamadas REST com C# e JavaScript para o SharePoint 2013](http://www.microsoft.com/resources/msdn/pt-br/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=4e4cc094-ff69-405b-852f-2ac7c41293c5)
    
  
-  [Fazendo chamadas REST com C# e JavaScript para a demonstração do SharePoint 2013](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)
    
  
-  [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Protocolo Open Data](http://www.odata.org/)
    
  
-  [OData: Formato JavaScript Object Notation (JSON)](http://www.odata.org/documentation/odata-version-2-0/json-format/)
    
  

  
    
    

