---
title: 使用 REST 服务同步 SharePoint 项
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# 使用 REST 服务同步 SharePoint 项
了解如何使用 **GetListItemChangesSinceToken** 资源（SharePoint REST 服务的一部分）在 SharePoint 和您的外接程序或服务之间同步项目。
## 使用 GetListItemChangesSinceToken 资源同步 SharePoint 项目

如果要在 SharePoint 和您的外接程序或服务之间同步项目，可以使用 **GetListItemChangesSinceToken** 资源执行此操作。 **GetListItemChangesSinceToken** 是 SharePoint REST 服务的一部分，与 [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) Web 服务调用相对应。
  
    
    
执行请求正文中包含  [SP.ChangeLogItemQuery 对象属性](#bk_props) 对象的 **POST** 请求。
  
    
    
该请求返回了 ADO **rowset** XML，它包括了对应于任何与指定查询相匹配的列表项更改的行。有关这些属性的详细信息（包括属性数据结构、CAML 元素说明和返回值），请参阅 [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) 。
  
    
    

||
|:-----|
|**示例请求** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**示例 POST 正文** <br/> |
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
   

## SP.ChangeLogItemQuery 对象属性
<a name="bk_props"> </a>


****


|**属性**|**说明**|
|:-----|:-----|
|**ListName** <br/> |一个不包括列表的标题，也不包含其 GUID 的字符串。当查询 UserInfo 表时，该字符串包含 UserInfo。使用 GUID 将产生更好的性能。  <br/> |
|**ViewName** <br/> |一个包含该视图的 GUID 的字符串，这决定了将该视图用于  _query_、 _viewFields_ 和 _rowLimit_ 形参表示的默认视图属性。如果未提供此实参，则将假设默认视图。如果提供了此实参， _query_、 _viewFields_ 或 _rowLimit_ 形参将重写该视图中的等效设置。例如，如果 _viewFields_ 形参所指定的视图具有行限制（限制为 100 行），但是 _rowLimit_ 形参包含了为 1000 的值，然后在该响应中将返回 1,000 行。 <br/> |
|**Query** <br/> |包含决定返回哪些记录以及以怎样的顺序返回这些记录的查询的 [查询](http://msdn.microsoft.com/zh-cn/library/ms471093.aspx) 元素 <br/> |
|**QueryOptions** <br/> |以下表单中的 XML 段落（该表单包含了  [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) 对象的不同属性的独立节点） <br/> |
|**ChangeToken** <br/> |一个包含了该请求的更改令牌的字符串。有关用于此字符串中的格式的说明，请参与 [更改日志的概览](http://msdn.microsoft.com/zh-cn/library/bb417456.aspx)。如果传递 Null，则返回列表中所有的项。  <br/> |
|**Contains** <br/> |定义该查询的自定义筛选的  [包含](http://msdn.microsoft.com/zh-cn/library/ms196501.aspx) 元素。 <br/> |
   

## 其他资源
<a name="bk_addresources"> </a>


-  [了解 SharePoint 2013 REST 服务](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md)
    
  
-  [使用 REST 处理文件夹和文件](working-with-folders-and-files-with-rest.md)
    
  
-  [导航 REST 服务中展示的 SharePoint 数据结构](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [确定 SharePoint REST 服务端点 URI](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [在 SharePoint REST 请求中使用 OData 查询操作](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [SharePoint 2013 REST API、终结点和示例](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [使用 ETag 值通过 REST 服务获取文档列表项版本控制](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

