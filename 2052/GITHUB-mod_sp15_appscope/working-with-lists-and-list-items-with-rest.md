---
title: 使用 REST 处理列表和列表项
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# 使用 REST 处理列表和列表项
了解如何使用 SharePoint 2013 REST 界面对列表和列表项执行基本的创建、读取、更新和删除 (CRUD) 操作。
> **提示**
> SharePoint Online（和本地 SharePoint 2016 及更高版本）REST 服务支持使用 OData  `$batch` 查询选项，将多个请求合并到对该服务的单个调用中。有关详细信息和代码示例的链接，请参阅 [使用 REST API 发出批处理请求](make-batch-requests-with-the-rest-apis.md)。 





## 先决条件

本主题假定您已熟悉主题 [了解 SharePoint 2013 REST 服务](get-to-know-the-sharepoint-2013-rest-service.md)和 [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)。它不提供代码段。




## 使用 REST 检索列表和列表属性
<a name="RetrieveLists"> </a>

以下示例演示您在知道特定列表的 GUID 时如何 **检索** 该列表。




```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```


> **注释**
> 如果您需要 JSON 格式的响应，请在  `accept` 标头中使用 `application/json;odata=verbose`。如果您需要 Atom 格式的响应，请在  `accept` 标头中使用 `application/atom+xml`。 




以下示例演示您在知道特定列表的标题时如何 **检索** 该列表。






```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下 XML 显示了当您请求 XML 内容类型时返回的列表属性的示例。






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


> **注释**
> 如果您要创建和更新列表中的项， **ListItemEntityTypeFullName** 属性（前面示例中的 **SP.Data.ProjectPolicyItemListItem**）特别重要。每当您创建和更新列表项时，此值必须作为在您的 HTTP 请求的正文中传递的元数据中的 **type** 属性传递。





## 通过 REST 使用列表
<a name="WorkLists"> </a>

以下示例演示如何 **创建** 列表。




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

以下示例演示如何使用 **MERGE** 方法 **更新** 列表。






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

以下示例演示如何为列表 **创建** **自定义字段** 。






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

以下示例演示如何 **删除** 列表。






```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE

```


## 通过 REST 使用列表项
<a name="ListItems"> </a>

以下示例显示如何"检索"列表的所有项。




> **注释**
> 查询列表项时，OData $skip 查询选项不起作用。在很多情况下，您可以改用  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx) 选项。





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下示例显示如何"检索"特定列表项。






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

以下 XML 显示当您请求 XML 内容类型时返回的列表项属性的示例。






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

以下示例显示如何"创建"列表项。




> **注释**
> 若要执行此操作，您必须知道列表的 **ListItemEntityTypeFullName** 属性并将它作为 HTTP 请求正文中的 **type** 的值传递。







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

以下示例显示如何"更新"列表项。




> **注释**
> 若要执行此操作，您必须知道列表的 **ListItemEntityTypeFullName** 属性并将它作为 HTTP 请求正文中的 **type** 的值传递。







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

以下示例显示如何"删除"列表项。






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## 使用 ETag 值确定文档和列表项的版本控制
<a name="Etag"> </a>

SharePoint REST 服务遵循  [OData 标准](http://www.odata.org/developers/protocols/operations)，它使用  [HTML ETag 对 SharePoint 列表和列表项进行并发控制](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags)。若要在执行 **PUT**、 **MERGE** 或 **DELETE** 请求时检查项目的版本，请在 **If-Match** HTTP 请求标题中指定 **ETag**。



如果您在请求中指定的 **ETag** 与服务器上文档或列表项的 **ETag** 不匹配，根据 OData 规范，REST 服务将返回 412 异常。




- 若要不考虑版本强制覆盖该项，请将 **ETag** 值设置为 **"*"**。


- 如果您不指定 **ETag**，SharePoint 将不考虑版本覆盖该项。


在 SharePoint 中，ETag 只应用于 SharePoint 列表和列表项。




## 其他资源
<a name="bk_addresources"> </a>


-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [使用 REST 处理文件夹和文件](working-with-folders-and-files-with-rest.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013：使用 REST 对文件和文件夹执行基本的数据访问操作](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [对 SharePoint 2013 使用 C# 和 JavaScript 进行 REST 调用](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=4e4cc094-ff69-405b-852f-2ac7c41293c5)


-  [对 SharePoint 2013 演示版使用 C# 和 JavaScript 进行 REST 调用](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)


-  [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)


-  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md)


-  [打开数据协议](http://www.odata.org/)


-  [OData：JavaScript 对象表示法 (JSON) 格式](http://www.odata.org/documentation/odata-version-2-0/json-format/)







