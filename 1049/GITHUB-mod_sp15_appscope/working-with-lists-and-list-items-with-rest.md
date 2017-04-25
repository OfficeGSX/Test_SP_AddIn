---
title: Работа со списками и элементами списков в службе REST
ms.prod: SHAREPOINT
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# Работа со списками и элементами списков в службе REST
Узнайте, как выполнять основные операции по созданию, чтению, обновлению и удалению списков и элементов списков с помощью интерфейса SharePoint 2013 REST.
> **Совет**
> SharePoint Online REST (а также локальной версии SharePoint 2016 и последующих выпусков) поддерживает объединение нескольких запросов в одном вызове службы с помощью параметра запроса OData  `$batch`. Дополнительные сведения и ссылки на примеры кода см. в разделе  [Создание пакетного запроса с помощью интерфейсов REST API](make-batch-requests-with-the-rest-apis.md). 





## Необходимые условия

В этом разделе предполагается, что вы уже знакомы с разделами  [Знакомство со службой REST для SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md) и [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). Здесь фрагменты кода не предоставлены.




## Получение списков и свойств списков с помощью REST
<a name="RetrieveLists"> </a>

В следующем примере показывается, как **получить** определенный список, если вам известен его GUID.



```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```


> **Примечание**
> Используйте  `application/json;odata=verbose` в заголовке `accept`, если вы хотите получить ответ в формате JSON. Используйте  `application/atom+xml` в заголовке `accept`, если вы хотите получить ответ в формате Atom. 




В следующем примере показывается, как **получить** определенный список, если вам известно его название.





```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

В следующем XML показывается пример свойств списка, которые возвращаются при запросе типов контента XML.





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
  </content>```


> **Примечание**
> Свойство **ListItemEntityTypeFullName** ( **SP.Data.ProjectPolicyItemListItem** в предыдущем примере) особенно важно при создании и обновлении элементов списка. Это значение должно быть передано как свойство **type** в метаданные, которые вы передаете в тексте HTTP-запроса всякий раз при создании и обновлении элементов списка.





## Работа со списками при помощи REST
<a name="WorkLists"> </a>

В следующем примере показывается, как **создавать** список.



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
    content-length:length of post body```

В следующем примере показано, как **обновить** список, используя метод **MERGE**.





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
    content-length:length of post body```

В следующем примере показывается, как **создать** **настраиваемое поле** для списка.





```

Url: url: http://site url/_api/web/lists(guid'list GUID')/Fields
Method:POST
Body: { '__metadata': { 'type': 'SP.Field' }, 'Title': 'field title', 'FieldTypeKind': FieldType value,'Required': 'true/false', 'EnforceUniqueValues': 'true/false','StaticName': 'field name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

В следующем примере показывается, как **удалить** список.





```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE
```


## Работа с элементами списка при помощи REST
<a name="ListItems"> </a>

В следующем примере показывается, как **получить** все элементы списка.




> **Примечание**
> Параметр запроса OData $skip не работает при запросе элементов списка. Вместо него в большинстве случаев вы можете использовать параметр  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx). 




```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

В следующем примере показывается, как **получить** определенный элемент списка.





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

В следующем XML показывается пример свойств элементов списка, которые возвращаются при вашем запросе типа контента XML.





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
</content>```

В следующем примере показывается, как **создать** элемент списка.




> **Примечание**
> Для выполнения этой операции вам необходимо знать свойство **ListItemEntityTypeFullName** списка и передать его как значение **type** в тексте запроса HTTP.






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'Test'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

В следующем примере показывается, как **обновить** элемент списка.




> **Примечание**
> Для выполнения этой операции вам необходимо знать свойство **ListItemEntityTypeFullName** списка и передать его как значение **type** в тексте запроса HTTP.






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
    content-length:length of post body```

В следующем примере показывается, как **удалить** элемент списка.





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"
```


## Использование значений тегов ETag для определения управления версиями элементов документов и списков
<a name="Etag"> </a>

Служба SharePoint REST, которая следует  [стандарту OData](http://www.odata.org/developers/protocols/operations), использует  [теги HTML ETags для контролирования блокировок](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags) элементов списков и списков SharePoint. Чтобы проверить версию элемента во время выполнения вами запроса **PUT**, **MERGE** или **DELETE**, укажите **ETag** в заголовке запроса HTTP **If-Match**.



Если **ETag**, который вы указываете в вашем запросе, не соответствует **ETag** документа или элемента списка на сервере, служба REST возвращает исключение 412 с помощью спецификации OData.




- Чтобы принудительно переписать элемент независимо от версии, установите значение **ETag** на **"*"**.


- Если не указать **ETag**, SharePoint переписывает элемент независимо от версии.


В SharePoint теги ETags применяются только к спискам SharePoint и элементам списков.




## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Работа с папками и файлами в службе REST](working-with-folders-and-files-with-rest.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013: выполнение основных операций доступа к данным в файлах и папках при помощи REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [Выполнение вызовов REST при помощи C# и JavaScript для SharePoint 2013](http://www.microsoft.com/resources/msdn/ru-ru/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=4e4cc094-ff69-405b-852f-2ac7c41293c5)


-  [Выполнение вызовов REST при помощи C# и JavaScript для SharePoint 2013 (ролик) ](http://www.microsoft.com/resources/msdn/ru-ru/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)


-  [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [Разработка надстроек для SharePoint](develop-sharepoint-add-ins.md)


-  [Безопасный доступ к данным и клиентские объектные модели для надстроек SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Работа с внешними данными в SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)


-  [Open Data Protocol](http://www.odata.org/)


-  [OData: формат нотаций объектов JavaScript (JSON)](http://www.odata.org/documentation/odata-version-2-0/json-format/)







