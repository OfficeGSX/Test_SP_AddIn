---
title: 导航 REST 服务中展示的 SharePoint 数据结构
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# 导航 REST 服务中展示的 SharePoint 数据结构
了解对于给定 SharePoint 项目如何从 REST 端点开始，然后导航到并访问相关项目，例如该项目所在的父网站或库结构。
## 从给定的 URL 导航，以访问其他 SharePoint 资源

使用 SharePoint REST 服务时，通常开始时就知道指定 SharePoint 项目的 URL，但是希望访问相关项目，例如项目驻留的文件夹或库结构。例如，假设您创建了一个外接程序，用户在其中的 SharePoint 库中输入了文档 URL。然后，外接程序必须分解该 URL，得出实际的 SharePoint 网站 URL。完成此操作后，外接程序便可以代表用户从该网站发出更多请求，例如创建、更新或删除相关项目或资源。



为此，您的外接程序需要查询 SharePoint，获取以下信息：




- 包含该资源的站点和网站集的服务器相对 URL


- 窗体摘要（它让您能够执行更改该资源状态的请求，如 **POST**、 **PUT**、 **MERGE** 和 **DELETE**）


基本过程：




1. 使用  `/contextinfo` 运算符和给定 URL 访问网站和网站集地址，以及窗体摘要。通过以下格式使用 `/contextinfo` 运算符：

     `http://server/web/doclib/forms/_api/contextinfo`

    为了增加跨网站的执行脚本操作尝试的安全性， `/contextinfo` 运算符只接受 **POST** 请求。


2. 使用  `/contextinfo` 运算符返回的 [SPContextWebInformation 对象属性](#bk_props)根据需要访问其他资源。


 **试用**




1. 从给定 SharePoint 项目的 URL 开始。例如：

     `http://site/web/doclib/myDocument.docx`


2. 从 URL 的结尾处删除指定资源的名称，以便 URL 指向文档库、文件夹或列表。在本示例中：

     `http://site/web/doclib/`


3. 将 REST 服务指针和  `/contextinfo` 运算符追加到该 URL：

     `http://site/web/doclib/_api/contextinfo`


4. 从该响应中读取窗体摘要和 **webFullUrl** 属性。


5. 将 REST 服务指针  `_api` 追加到 Web URL


6. 使用产生的 URL 和窗体摘要请求其他所需资源。


如果发出 **GET** 请求或使用已验证的 OAuth 令牌发出请求，则不需要传递窗体摘要。




## 导航父网站和子网站
<a name="bk_sites"> </a>

使用 SharePoint 服务器对象模型导航网站结构时，使用  [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) 和 [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) 属性访问表示父网站和子网站的对象。



对应的 REST 资源 `web/parentweb` 和 `web/webs`不返回表示网站的对象。这是因为 REST 服务符合 OData 标准，并且，返回完整网站表示会降低此类请求的效率。相反，它们返回  [WebInfo 对象](#bk_webinfo)，该对象包含网站的标量属性，但是不包含相关实体集，例如 Like 集或字段集。



若要导航到特定父网站或子网站，请使用 **Id** 或 **Title** 属性构造该网站对应的 REST URL。从那里，您可以访问该网站的相关实体集。




## 导航文件夹结构
<a name="bk_folders"> </a>

SharePoint REST 服务不支持通过 URL 构造遍历站点的文件夹层次结构。代替使用  [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) 方法的等效的 REST。例如：



 *REST 服务不支持的导航：* 



 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`



由 REST 服务支持的导航：



 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`。




## SPContextWebInformation 对象属性
<a name="bk_props"> </a>



|**SPContextWebInformation 属性**|**说明**|
|:-----|:-----|
|**webFullUrl** <br/> |获取最近站点的相对于服务器的 URL  <br/> |
|**siteFullUrl** <br/> |获取将该站点包含其中的网站集的根的相对于服务器的 URL  <br/> 如果最近的 Web 是某网站集的根，那么 **webFullUrl** 属性的值等于 **siteFullUrl** 属性的值。 <br/> |
|**formDigestValue** <br/> |获取服务器的请求窗体摘要。  <br/> |
|**LibraryVersion** <br/> |获取 REST 库的当前版本。  <br/> |
|**SupportedSchemaVersions** <br/> |获取所支持的 REST/CSOM 库的架构的版本。  <br/> |
 

## WebInfo 对象
<a name="bk_webinfo"> </a>



|**WebInfo 属性**|**说明**|
|:-----|:-----|
|**Created** <br/> |获取指定何时创建该站点的值。  <br/> |
|**Description** <br/> |获取或设置该站点的说明。  <br/> |
|**Id** <br/> |获取指定站点标识符的值。  <br/> |
|**Language** <br/> |获取指定该站点所使用语言的区域设置 ID (LCID) 的值。  <br/> |
|**LastItemModifiedDate** <br/> |获取在该站点最后修改项的时间的值。  <br/> |
|**Title** <br/> |获取或设置该站点的标题。  <br/> |
|**WebTemplateId** <br/> |获取站点模板的标识符。  <br/> |
 

## 其他资源
<a name="bk_addresources"> </a>


-  [了解 SharePoint 2013 REST 服务](get-to-know-the-sharepoint-2013-rest-service.md)


-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md)


-  [使用 REST 处理文件夹和文件](working-with-folders-and-files-with-rest.md)


-  [确定 SharePoint REST 服务端点 URI](determine-sharepoint-rest-service-endpoint-uris.md)


-  [在 SharePoint REST 请求中使用 OData 查询操作](use-odata-query-operations-in-sharepoint-rest-requests.md)


-  [SharePoint 2013 REST API、终结点和示例](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [使用 REST 服务同步 SharePoint 项](synchronize-sharepoint-items-using-the-rest-service.md)


-  [使用 ETag 值通过 REST 服务获取文档列表项版本控制](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)







