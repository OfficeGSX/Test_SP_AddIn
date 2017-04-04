---
title: Синхронизация элементов SharePoint с помощью службы REST
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# Синхронизация элементов SharePoint с помощью службы REST
Узнайте, как синхронизировать элементы между SharePoint и вашими надстройками или службами с помощью ресурса **GetListItemChangesSinceToken**, являющегося частью службы SharePoint REST.
## Синхронизация элементов SharePoint с помощью ресурса GetListItemChangesSinceToken

Если вы хотите синхронизировать элементы между SharePoint и вашими надстройками или службами, вы можете использовать для этого ресурс **GetListItemChangesSinceToken**. **GetListItemChangesSinceToken**, являющийся частью службы SharePoint REST, соответствует вызову веб-службы  [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    
Выполните запрос **POST**, который содержит объект  [Свойства объекта SP.ChangeLogItemQuery](#bk_props) в тексте запроса.
  
    
    
Запрос возвращает ADO **rowset** XML, который включает строки, соответствующие любому изменению элемента списка, отвечающие требованиям указанного запроса. Для получения дополнительных сведений об этих свойствах, включая структуры данных свойства, описания элементов CAML и возвращаемые значения, см. [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    

||
|:-----|
|**Пример запроса** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**Пример текста POST** <br/> |
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
   

## Свойства объекта SP.ChangeLogItemQuery
<a name="bk_props"> </a>


****


|**Свойство**|**Описание**|
|:-----|:-----|
|**ListName** <br/> |Строка, которая содержит название или GUID для списка. При запросе таблицы UserInfo строка содержит UserInfo. Использование GUID приводит к повышению производительности.  <br/> |
|**ViewName** <br/> |Строка, которая содержит GUID для представления и которая определяет его для использования атрибутами представления по умолчанию, в роли которых выступают параметры  _query_,  _viewFields_ и _rowLimit_. Если этот аргумент не указан, то предполагается представление по умолчанию. Если он указан, значение параметра  _query_,  _viewFields_ или _rowLimit_ переопределяет аналогичный параметр в представлении. Например, если представление, указанное параметром _viewFields_, имеет максимальное число строк, равное 100, но параметр  _rowLimit_ содержит значение 1000, то в ответе возвращаются 1000 строк. <br/> |
|**Query** <br/> |Элемент  [Query](http://msdn.microsoft.com/ru-ru/library/ms471093.aspx), содержащий запрос, который определяет, какие записи возвращаются и в каком порядке.  <br/> |
|**QueryOptions** <br/> |Фрагмент XML в следующей форме, которая содержит отдельные узлы для различных свойств объекта  [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) . <br/> |
|**ChangeToken** <br/> |Строка, которая содержит маркер изменений для запроса. Описание формата, который используется в этой строке, см. здесь:  [Обзор журнала изменений ](http://msdn.microsoft.com/ru-ru/library/bb417456.aspx). Если передано значение NULL, все элементы из списка возвращаются.  <br/> |
|**Contains** <br/> |Элемент  [Contains](http://msdn.microsoft.com/ru-ru/library/ms196501.aspx), который определяет настраиваемую фильтрацию для запроса.  <br/> |
   

## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Знакомство со службой REST для SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Работа с папками и файлами в службе REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Навигация по структуре данных SharePoint, представленной в службе REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [Определение универсальных кодов ресурсов (URI) конечных точек службы SharePoint REST](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Использование операций запросов OData в запросах SharePoint REST](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [SharePoint 2013 REST API, конечные точки и примеры](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Использование значений ETag в службе REST для управления версиями элементов списков документов](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

