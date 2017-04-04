---
title: REST サービスを使用して SharePoint アイテムを同期させる
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# REST サービスを使用して SharePoint アイテムを同期させる
SharePoint REST サービスの一部である **GetListItemChangesSinceToken** リソースを使用して、SharePoint とアドインまたはサービス間でアイテムを同期させる方法を説明します。
## GetListItemChangesSinceToken リソースを使用して SharePoint アイテムを同期させる

SharePoint とアドインまたはサービス間でアイテムを同期させる場合、 **GetListItemChangesSinceToken** リソースを使用して行うことができます。SharePoint REST サービスの一部である **GetListItemChangesSinceToken** は、 [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) Web サービス呼び出しに対応します。
  
    
    
要求本文に  [SP.ChangeLogItemQuery オブジェクトのプロパティ](#bk_props) オブジェクトを含む **POST** 要求を実行します。
  
    
    
要求は、指定されたクエリに一致するリスト項目の変更に対応する行を含む ADO **rowset** XML を返します。プロパティのデータ構造、CAML 要素の説明、戻り値など、これらのプロパティの詳細については、「 [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) 」を参照してください。
  
    
    

||
|:-----|
|**要求の例** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**POST 本文の例** <br/> |
|```XML

{ 'd' : {
  'query': { 
    '__metadata': { 'type': 'SP.ChangeLogItemQuery'}, 
    'ViewName': '', 
    'Query': '<Where>
      <Contains>
         <FieldRef Name="Title" />
         <Value Type='Text'>Te</Value>
      </Contains></Where>',
    'QueryOptions': '<QueryOptions>
      <IncludeMandatoryColumns>FALSE</IncludeMandatoryColumns>
      <DateInUtc>False</DateInUtc>
      <IncludePermissions>TRUE</IncludePermissions>
      <IncludeAttachmentUrls>FALSE</IncludeAttachmentUrls>
      <Folder>Shared Documents/Test1</Folder></QueryOptions>', 
    'ChangeToken':'1;3;eee4c6d5-f88a-42c4-8ce1-685122984870;634397182229400000;3710', 
    'Contains':'<Contains>
      <FieldRef Name="Title"/>
      <Value Type="Text">Testing</Value></Contains>' } 
  } 
}
```

|
   

## SP.ChangeLogItemQuery オブジェクトのプロパティ
<a name="bk_props"> </a>


****


|**プロパティ**|**説明**|
|:-----|:-----|
|**ListName** <br/> |リストのタイトルまたは GUID のいずれかを含む文字列。UserInfo テーブルをクエリする場合、文字列には UserInfo が含まれる。GUID を使用するほうが処理速度が速い。  <br/> |
|**ViewName** <br/> |ビューの GUID を含む文字列。 _query_、 _viewFields_、または  _rowLimit_ パラメーターで表された既定のビュー属性で使用するビューを決定します。この引数を指定しない場合は、既定のビューが使用されます。指定した場合は、 _query_、 _viewFields_、または  _rowLimit_ パラメーターの値で、ビュー内の対応する設定が上書きされます。たとえば、 _viewFields_ パラメーターで指定されたビューに 100 行の行数制限があり、 _rowLimit_ パラメーターの値が 1000 に指定されている場合、応答には 1,000 行が返されます。 <br/> |
|**Query** <br/> | [Query](http://msdn.microsoft.com/ja-jp/library/ms471093.aspx) 要素。どのレコードをどの順序に並べて返すかを指定するクエリを含みます。 <br/> |
|**QueryOptions** <br/> | [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) オブジェクトのさまざまなプロパティの個別のノードを含む、次の形式の XML フラグメント。 <br/> |
|**ChangeToken** <br/> |要求の変更トークンを含む文字列。この文字列に使用される形式の詳細については、「 [変更ログの概要](http://msdn.microsoft.com/ja-jp/library/bb417456.aspx)」を参照してください。Null を渡すと、リスト内のすべてのアイテムが返されます。  <br/> |
|**Contains** <br/> | [Contains](http://msdn.microsoft.com/ja-jp/library/ms196501.aspx) 要素。クエリのカスタム フィルタリングを指定します。 <br/> |
   

## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 REST サービスの概要](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)
    
  
-  [REST を使用してフォルダーとファイルを操作する](working-with-folders-and-files-with-rest.md)
    
  
-  [REST サービスで表される SharePoint データ構造内を移動する](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [SharePoint REST サービス エンドポイント URI を決定する](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [SharePoint REST 要求で OData クエリ操作を使用する](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [SharePoint 2013 REST API、エンドポイント、およびサンプル](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [REST サービスで ETag を使用してドキュメント リスト アイテムのバージョンを取得する](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

