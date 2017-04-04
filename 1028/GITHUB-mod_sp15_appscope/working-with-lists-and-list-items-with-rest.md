---
title: 使用清單及清單項目與 REST
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# 使用清單及清單項目與 REST
了解如何執行基本建立、 讀取、 更新及刪除清單和清單項目與SharePoint 2013 REST 介面 (CRUD) 作業。
> **秘訣**
> SharePoint Online (及內部SharePoint 2016年及更新版本) REST 服務可支援合併成單一呼叫服務的多個要求使用 OData  `$batch`查詢選項。 詳細資料與程式碼範例的連結，請參閱 [請使用 REST Api 的批次要求](make-batch-requests-with-the-rest-apis.md)。
  
    
    


## 必要條件

本主題假設您已熟悉的各項主題 [若要了解 SharePoint 2013 REST 服務取得](get-to-know-the-sharepoint-2013-rest-service.md)和 [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)。它不提供在程式碼片段 (英文)。
  
    
    

## 擷取清單及清單與其餘的屬性
<a name="RetrieveLists"> </a>

下列範例會示範如何 **擷取** 特定清單如果您知道其 GUID。
  
    
    

```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```


> **注意事項**
> 如果您想在 JSON 回應，  `accept`標頭中使用 `application/json;odata=verbose` 。如果您想 Atom 格式回應， `accept`標頭中使用 `application/atom+xml` 。
  
    
    

下列範例會示範如何 **擷取** 特定清單如果您知道其標題。
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列 XML 顯示您要求的 XML 內容類型時不會傳回清單屬性的範例。
  
    
    



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


> **注意事項**
> **ListItemEntityTypeFullName**屬性 ( **SP.Data.ProjectPolicyItemListItem**上述範例中)，請務必特別是如果您想要建立並更新清單項目。此值必須傳遞 **type**屬性中每當您建立及更新清單項目內文的 HTTP 要求傳遞的中繼資料。
  
    
    


## 使用 REST 使用清單
<a name="WorkLists"> </a>

下列範例顯示如何 **建立** 清單。
  
    
    

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

下列範例顯示如何 **更新** 使用 **MERGE**方法的清單。
  
    
    



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

下列範例顯示如何 **建立** **自訂欄位** 的清單。
  
    
    



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

下列範例顯示如何 **刪除** 清單。
  
    
    



```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE

```


## 使用使用 REST 的清單項目
<a name="ListItems"> </a>

下列範例顯示如何 **擷取** 所有的清單項目。
  
    
    

> **注意事項**
> OData $skip 查詢選項不適用於查詢清單項目時。5 情況，您可以使用 [  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx) ] 選項。
  
    
    


```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列範例會示範如何 **擷取** 特定清單項目。
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

下列 XML 顯示會傳回您要求的 XML 內容類型的清單項目屬性的範例。
  
    
    



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

下列範例顯示如何 **建立** 清單項目。
  
    
    

> **注意事項**
> 若要執行這項作業，您必須知道清單 **ListItemEntityTypeFullName**屬性及傳遞的 **type** HTTP 要求內文中的值。
  
    
    




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

下列範例顯示如何 **更新** 清單項目。
  
    
    

> **注意事項**
> 若要執行這項作業，您必須知道清單 **ListItemEntityTypeFullName**屬性及傳遞的 **type** HTTP 要求內文中的值。
  
    
    




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

下列範例顯示如何 **刪除** 清單項目。
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## 使用 ETag 值來決定文件和清單項目版本設定
<a name="Etag"> </a>

遵循 [OData 標準](http://www.odata.org/developers/protocols/operations)、 SharePoint REST 服務，會使用 [HTML Etag 並行控制項](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags)的 SharePoint 清單及清單項目。若要檢查的項目版本上執行 **PUT**、 **MERGE**或 **DELETE**要求時，請指定 **ETag** **If-Match** HTTP 要求標頭中。
  
    
    
如果您在您要求中指定 **ETag**不符合伺服器上的文件或清單項目 **ETag** 、 REST 服務會傳回 412 例外狀況，每個 OData 規格。
  
    
    

- 若要強制覆寫不論版本的項目，請將 **ETag**值設 **"*"**。
    
  
- 如果未指定 **ETag**，SharePoint 會覆寫不論版本的項目。
    
  
在 SharePoint、 Etag 僅適用於 SharePoint 清單及清單項目。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [使用資料夾與其餘的檔案](working-with-folders-and-files-with-rest.md)
    
  
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
    
  
-  [OData: JavaScript 物件 Notation (JSON) 格式](http://www.odata.org/documentation/odata-version-2-0/json-format/)
    
  

  
    
    

