---
title: SharePoint 外接程序中的 URL 字符串和标记
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# SharePoint 外接程序中的 URL 字符串和标记
了解可在 SharePoint 外接程序中使用的 URL 标记。
> **重要信息**
> 有关在 SharePoint 2013 中构造 URL 和在这些 URL 中使用标记的常规信息，请参阅 [SharePoint 2013 中的 URL 和标记](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)。本主题介绍 SharePoint 外接程序中可用的标记。 





## 
<a name="URLtokens"> </a>

SharePoint 2013 支持在 SharePoint 外接程序中使用下表中列出的标记。



在 SharePoint 外接程序开发的各种不同情景下，可在 URL 中使用本节表格中的标记，这些情景包括自定义操作和自定义页面上的链接。在某些上下文中，其中一些标记不可使用。只可使用有限标记的三个最重要位置是外接程序的起始页、主机 Web 的自定义操作和外接程序部件的  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) 属性。这三个位置在单独的列中标注， * **但这三者不构成可以使用标记的位置的详尽列表。*** 



 **StartPage** 列将指定是否可以在外接程序清单的 **StartPage** 元素中使用此标记。 **自定义操作** 列将指定是否可以在托管 Web 的自定义操作的 URL 中使用此标记。 **外接程序部件** 列将指定是否可以在外接程序部件的 [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) 属性中使用此标记。




**可在 SharePoint 外接程序的 URL 的开头使用的标记**


|**标记**|**解析为**|**StartPage**|**自定义操作**|**外接程序部件**|**备注**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl  <br/> |SharePoint 外接程序的外接程序 Web 的 URL。  <br/> |是  <br/> |是  <br/> |是  <br/> |此标记应仅在外接程序 Web 的外部使用。在外接程序 Web 本身中，请对外接程序 Web 的 URL 使用 **~site**。  <br/> |
|~controlTemplates  <br/> |当前网站的 ControlTemplates 虚拟文件夹的 URL。  <br/> |否  <br/> |否  <br/> |否  <br/> ||
|~hostUrl  <br/> |主机 Web 的 URL。  <br/> |否  <br/> |否  <br/> |是  <br/> ||
|~hostLogoUrl  <br/> |主机 Web 徽标的 URL。  <br/> |否  <br/> |否  <br/> |否  <br/> ||
|~layouts  <br/> |当前网站的 Layouts 虚拟文件夹的 URL。  <br/> |否  <br/> |否  <br/> |否  <br/> ||
|~remoteAppUrl  <br/> |SharePoint 外接程序的远程 Web 应用程序的 URL。  <br/> |是  <br/> |在主机 Web 中为是，但在外接程序 Web 中为否。  <br/> |是  <br/> |如果您不使用 Visual Studio Microsoft Office 开发人员工具来开发 SharePoint 外接程序，您仅可以在 **StartPage** URL 中使用 **~remoteAppUrl**。但是，如果您使用 Visual Studio 和工具，您可以对任意提供程序托管的外接程序使用该标记，当 Visual Studio 打包该外接程序时，它将被解析。在这种使用情况下，它实际上更像一个 Visual Studio 标记，而非 SharePoint 标记。它可在外接程序清单外使用，即使您没有使用 Visual Studio Microsoft Office 开发人员工具。  <br/> |
|~site  <br/> |当前网站的 URL。  <br/> |否  <br/> |否  <br/> |是  <br/> ||
|~sitecollection  <br/> |当前网站的父网站集的 URL。  <br/> |否  <br/> |否  <br/> |是  <br/> ||
 
除非另有规定，否则下个表格中的任何标记都不可在外接程序部件的  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) 属性值的 *路径*  部分使用。 **外接程序部件** 列是指这些标记在该值的 *查询字符串*  部分的使用。




**可在 URL 内使用的标记**


|**标记**|**解析为**|**StartPage**|**自定义操作**|**外接程序部件**|**备注**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken}  <br/> |外接程序的 OAuth 上下文标记。  <br/> |否  <br/> |否  <br/> |否  <br/> ||
|{AppWebUrl}  <br/> |SharePoint 外接程序的外接程序 Web 的 URL。  <br/> |是  <br/> |是  <br/> |是  <br/> |此标记应仅在外接程序 Web 的外部使用。在外接程序 Web 中，请对外接程序 Web 的 URL 使用 **{Site}**。  <br/> |
|{ClientTag}  <br/> |当前网站的客户端缓存控制编号（客户端标记）。  <br/> |是  <br/> |是  <br/> |是  <br/> ||
|{HostLogoUrl}  <br/> |SharePoint 外接程序的主机 Web 的徽标。  <br/> |是  <br/> |是  <br/> |是  <br/> ||
|{HostTitle}  <br/> |SharePoint 外接程序的主机 Web 的标题。  <br/> |是  <br/> |是  <br/> |是  <br/> ||
|{HostUrl}  <br/> |SharePoint 外接程序的主机 Web 的 URL。  <br/> |是  <br/> |是  <br/> |是  <br/> ||
|{ItemId}  <br/> |列表或库中的项目的 ID（整数）。  <br/> |否  <br/> |是  <br/> |否  <br/> ||
|{ItemUrl}  <br/> |正在处理的项的 URL。  <br/> |否  <br/> |是  <br/> |否  <br/> ||
|{Language}  <br/> |SharePoint 外接程序的主机 Web 的当前语言/区域性。  <br/> |是  <br/> |是  <br/> |是  <br/> ||
|{ListId}  <br/> |当前列表的 ID（一个 GUID）。  <br/> |否  <br/> |是  <br/> |否  <br/> ||
|{ProductNumber}  <br/> |SharePoint 服务器场的完整构造版本编号。  <br/> |是  <br/> |是  <br/> |是  <br/> |示例值为"15.0.4433.1011"。  <br/> |
|{RecurrenceId}  <br/> |定期事件的定期索引。  <br/> |否  <br/> |是  <br/> |否  <br/> |不支持将该标记用于列表项的上下文菜单中。  <br/> |
|{RemoteAppUrl}  <br/> |SharePoint 外接程序的远程 Web 应用程序的 URL。  <br/> |是  <br/> |是  <br/> |是  <br/> ||
|{Site}  <br/> |当前网站的 URL。  <br/> |否  <br/> |是  <br/> |是  <br/> ||
|{SiteCollection}  <br/> |当前网站的父网站的 URL。  <br/> |否  <br/> |是  <br/> |是  <br/> ||
|{SiteUrl}  <br/> |当前网站的 URL。  <br/> |否  <br/> |是  <br/> |否  <br/> ||
|{Source}  <br/> |HTTP 请求 URL。  <br/> |否  <br/> |是  <br/> |否  <br/> ||
|{StandardTokens}  <br/> |请参阅备注。  <br/> |是  <br/> |是  <br/> |是  <br/> |此标记组合了五个其他标记。它最初解析为  `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}`。然后解析其中的每一个标记。如果没有外接程序 Web，则将不显示  `&amp;SPAppWebUrl={AppWebUrl}` 部分。 <br/> |
 

## 其他资源
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [高级 Extranet 支持](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)


-  [获取对网站、Web 应用程序和其他关键对象的引用](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)


-  [使用列表对象和集合](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)


-  [示例对象模型任务](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)



