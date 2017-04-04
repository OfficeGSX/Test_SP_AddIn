---
title: 同步處理 SharePoint 項目的使用 REST 服務
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# 同步處理 SharePoint 項目的使用 REST 服務
了解如何使用 SharePoint REST 服務一部分 **GetListItemChangesSinceToken**資源同步處理 SharePoint 與增益集或服務之間的項目。
## 同步處理 SharePoint 項目的使用 GetListItemChangesSinceToken 資源

如果您想要同步處理 SharePoint 與增益集或服務之間的項目，您可以使用 **GetListItemChangesSinceToken**資源來達成。 **GetListItemChangesSinceToken**、 組件的 SharePoint REST 服務，會對應至 [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) web 服務呼叫。
  
    
    
執行 SP.包含 [SP.ChangeLogItemQuery 物件屬性](#bk_props)要求內文中的物件。
  
    
    
要求傳回 ADO **rowset** XML 包括對應至指定的查詢比對任何清單項目變更的資料列。如需有關這些屬性包括屬性資料結構、 CAML 元素描述及傳回值，請參閱 [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) 。
  
    
    

||
|:-----|
|**範例要求** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**範例會張貼內容** <br/> |
|
```XML

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
   

## SP.ChangeLogItemQuery 物件屬性
<a name="bk_props"> </a>


****


|**屬性**|**描述**|
|:-----|:-----|
|**ListName** <br/> |字串包含標題或清單的 GUID。查詢的使用者資訊資料表、 時字串會包含使用者資訊。使用 GUID 結果中較佳的效能。 <br/> |
|**ViewName** <br/> |由 _query_、  _viewFields_及 _rowLimit_參數表示包含檢視中，會決定要使用的預設檢視屬性的檢視 GUID 的字串。若未提供此引數，會假設為預設檢視。如果提供、  _query_、  _viewFields_或 _rowLimit_參數的值會覆寫檢視中的對等設定。例如，如果 _viewFields_參數所指定的檢視有 100 個資料列的列數限制，但 _rowLimit_參數包含的值為 1000年，若有 1000 列會傳回以回應。 <br/> |
|**Query** <br/> |含有會決定傳回記錄的查詢的 [查詢](http://msdn.microsoft.com/en-us/library/ms471093.aspx)元素及哪些順序。 <br/> |
|**QueryOptions** <br/> |在包含 [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) 物件的各種屬性的不同節點的下列表單 XML 片段。 <br/> |
|**ChangeToken** <br/> |字串，其中包含要求的變更 token。這個字串中所用的格式的說明，請參閱 [Overview of 變更記錄](http://msdn.microsoft.com/en-us/library/bb417456.aspx)。如果會傳遞 null，則會傳回清單中的所有項目。 <br/> |
|**Contains** <br/> |定義自訂篩選查詢 [包含](http://msdn.microsoft.com/en-us/library/ms196501.aspx)項目。 <br/> |
   

## 其他資源
<a name="bk_addresources"> </a>


-  [若要了解 SharePoint 2013 REST 服務取得](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [使用資料夾與其餘的檔案](working-with-folders-and-files-with-rest.md)
    
  
-  [瀏覽表示 REST 服務中的 SharePoint 資料結構](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [決定 SharePoint REST 服務端點 Uri](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [在 SharePoint 其餘邀請中使用 OData 查詢作業](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [REST API 參考和範例](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [使用 ETag 值，透過其他服務，來取得文件清單項目版本設定](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

