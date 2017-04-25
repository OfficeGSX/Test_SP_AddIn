---
title: 使用 REST API 发出批处理请求
ms.prod: OFFICE365
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# 使用 REST API 发出批处理请求
了解如何将  `$batch` 查询选项与 REST/OData API 一起使用。
本文介绍如何对 Microsoft SharePoint Online（和本地 SharePoint 2016 及更高版本）的 REST/OData API 查询和操作以及 Office 365 REST API 的 [文件和文件夹子集](http://msdn.microsoft.com/zh-cn/office/office365/api/files-rest-operations)进行批处理。通过此技术，您可以将多项操作合并到发给服务器的一个请求以及一个响应中，从而改进外接程序的性能。





## $batch 选项的执行摘要

SharePoint Online（和本地 SharePoint 2016 及更高版本）和 Office 365 API 实现 OData  `$batch` 查询选项，因此您可以查看 [正式文档](http://www.odata.org/documentation/odata-version-3-0/batch-processing)了解有关其使用方式的详细信息。（另一个选项是查看 Andrew Connell 关于此主题的博客文章，从 [第 1 部分 - SharePoint REST API 批处理](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests)开始。）下面只是一些要点提醒：




- 请求 URL 由根服务 URL 和  `$batch` 选项组成，例如 `https://fabrikam.sharepoint.com/_api/$batch` 或 `https://fabrikam.office365.com/api/v1.0/me/$batch`。


- HTTP 请求正文是 MIME 类型  *multipart/mixed*  。


- 请求的正文分成几个部分，各个部分之间用请求标头中指定的边界字符串分开。


- 正文的每个部分都有自己的 HTTP 动词和 REST URL 及其自己的内部正文（如果适用）。


- 一个部分可为一个读取操作（或函数调用），也可以为一个或多个写入操作（或函数调用）的 ChangeSet。ChangeSet 本身为 MIME 类型  *multipart/mixed*  ，其各个部分包含插入、更新或删除操作。

    > **重要信息**
      > 目前，SharePoint 和 Office 365 API 对包含多个操作的 ChangeSets 不支持"全部或全无"功能。如果任一子操作失败，其他操作仍会完成，并且不会回滚。 

## 代码示例

针对 SharePoint REST/OData API 使用  `$batch` 查询选项的代码示例：




- **C#：** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)


- **JavaScript：** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)



## 示例请求和响应

下面是一个原始 HTTP 请求的示例，该请求对两个 GET 操作进行批处理，以检索两个不同列表中所有项目的标题。




```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Host: fabrikam.sharepoint.com
Content-Length: 527
Expect: 100-continue

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('Composed%20Looks')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('User%20Information%20List')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1--

```

下面是一个原始 HTTP 请求的正文示例，该请求对列表的 DELETE 和 SharePoint 列表的列表的 GET 进行批处理。






```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Host: fabrikam.sharepoint.com
Content-Length: 647
Expect: 100-continue

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: multipart/mixed; boundary=changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d
Content-Type: application/http
Content-Transfer-Encoding: binary

DELETE https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('OldList') HTTP/1.1
If-Match: "1"

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d--
--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists HTTP/1.1

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e--
```


## 有用库的链接

提供 OData 库，支持多种语言的 OData 批处理。下面是两个示例。有关更完整的列表，请参阅  [OData 库](http://www.odata.org/libraries/)。




-  [.NET OData 库](http://msdn.microsoft.com/zh-cn/office/microsoft.data.odata%28v=vs.90%29)。尤其请参阅 **ODataBatch*** 类。


-  [Datajs 库](http://datajs.codeplex.com/documentation)。尤其请参阅 [批处理操作](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch)。



