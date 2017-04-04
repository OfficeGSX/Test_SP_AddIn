---
title: REST を使用したリスト アイテムの操作
ms.prod: SHAREPOINT
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# REST を使用したリスト アイテムの操作
SharePoint 2013 REST インターフェイスでリストおよびリスト アイテムに対する基本的な作成、読み取り、更新、および削除 (CRUD) の操作を実行する方法について説明します。
> **ヒント**
> SharePoint Online (およびオンプレミスの SharePoint 2016 以降の) REST サービスは、OData  `$batch` クエリ オプションを使用して、複数の要求を組み合わせて 1 つのサービスへの呼び出しにすることをサポートしています。詳細およびコード サンプルへのリンクについては、「 [REST API によりバッチ要求を発行する](make-batch-requests-with-the-rest-apis.md)」を参照してください。 
  
    
    


## 前提条件

このトピックでは、読者が「 [SharePoint 2013 REST サービスの概要](get-to-know-the-sharepoint-2013-rest-service.md)」および「 [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)」の各トピックに既に習熟していることを想定しています。この記事にはコード スニペットが含まれていません。
  
    
    

## REST を使用してリストおよびリスト プロパティを取得する
<a name="RetrieveLists"> </a>

次の例は、GUID がわかっている場合に特定のリストを **取得** する方法を示しています。
  
    
    

```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```


> **メモ**
> JSON の応答が必要な場合は  `accept` ヘッダーの `application/json;odata=verbose` を使用します。Atom 形式の応答が必要な場合は `accept` ヘッダーの `application/atom+xml` を使用します。
  
    
    

次の例は、タイトルがわかっている場合に特定のリストを **取得** する方法を示しています。
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

次の XML は、XML コンテンツ タイプを要求したときに返されるリスト プロパティの例を示しています。
  
    
    



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


> **メモ**
> **ListItemEntityTypeFullName** プロパティ (前の例の **SP.Data.ProjectPolicyItemListItem**) は、リスト アイテムを作成して更新する場合に特に重要です。この値は、リスト アイテムを作成して更新するたびに HTTP 要求の本文に渡すメタデータの **type** プロパティとして渡す必要があります。
  
    
    


## REST を使用してリストを操作する
<a name="WorkLists"> </a>

次の例は、リストを **作成** する方法を示しています。
  
    
    

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

次の例は、 **MERGE** メソッドを使用してリストを **更新** する方法を示しています。
  
    
    



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

次の例は、リストの **カスタム フィールド** を **作成** する方法を示しています。
  
    
    



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

次の例は、リストを **削除** する方法を示しています。
  
    
    



```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE

```


## REST を使用してリスト アイテムを操作する
<a name="ListItems"> </a>

次の例は、リストのアイテムをすべて **取得** する方法を示しています。
  
    
    

> **メモ**
> リスト アイテムのクエリの場合、OData $skip クエリ オプションは機能しません。多くの場合、その代わりに  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx) オプションを使用することができます。
  
    
    


```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

次の例は、特定のリスト アイテムを **取得** する方法を示しています。
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

次の XML は、XML コンテンツ タイプを要求したときに返されるリスト アイテム プロパティの例を示しています。
  
    
    



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

次の例は、リスト アイテムを **作成** する方法を示しています。
  
    
    

> **メモ**
> この操作を実行するには、リストの **ListItemEntityTypeFullName** プロパティを知っていて、それを HTTP 要求本文の **type** の値として渡す必要があります。
  
    
    




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

次の例は、リスト アイテムを **更新** する方法を示しています。
  
    
    

> **メモ**
> この操作を実行するには、リストの **ListItemEntityTypeFullName** プロパティを知っていて、それを HTTP 要求本文の **type** の値として渡す必要があります。
  
    
    




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

次の例は、リスト アイテムを **削除** する方法を示しています。
  
    
    



```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## ETag 値を使用してドキュメントとリスト項目のバージョンを確認する
<a name="Etag"> </a>

 [OData 標準](http://www.odata.org/developers/protocols/operations)に従う SharePoint REST サービスは、SharePoint のリストおよびリスト項目の [同時実行制御に HTML ETag](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags) を使用します。 **PUT**、 **MERGE**、または **DELETE** 要求を実行する時に項目のバージョンを確認するには、 **If-Match** HTTP 要求ヘッダーに **ETag** を指定します。
  
    
    
要求に指定した **ETag** がサーバー上のドキュメントまたはリスト項目の **ETag** と一致しない場合、OData 仕様に従い、REST サービスによって 412 例外が返されます。
  
    
    

- バージョンの違いにかかわらず、項目を強制的に上書きするには、 **ETag** の値に **"*"** を設定します。
    
  
- **ETag** を指定しない場合、SharePoint は、バージョンの違いにかかわらず項目を上書きします。
    
  
SharePoint 内で、ETag は SharePoint リストとリスト項目にのみ適用されます。
  
    
    

## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [REST を使用してフォルダーとファイルを操作する](working-with-folders-and-files-with-rest.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: REST を使用してファイルおよびフォルダーに対する基本的なデータ操作を実行する](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [SharePoint 2013 で C# および JavaScript を使用して REST 呼び出しを実行する](http://www.microsoft.com/resources/msdn/ja-jp/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=4e4cc094-ff69-405b-852f-2ac7c41293c5)
    
  
-  [SharePoint 2013 で C# および JavaScript を使用して REST 呼び出しを実行するデモ](http://www.microsoft.com/resources/msdn/ja-jp/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)
    
  
-  [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Open Data Protocol](http://www.odata.org/)
    
  
-  [OData: JavaScript Object Notation (JSON) 形式](http://www.odata.org/documentation/odata-version-2-0/json-format/)
    
  

  
    
    

