---
title: 在 SharePoint 2013 中创建 UX 组件
ms.prod: SHAREPOINT
ms.assetid: bfdd0a58-2cc5-4805-ac89-4bd2fe6f3b09
---


# 在 SharePoint 2013 中创建 UX 组件
了解如何在 SharePoint 2013 中为您的外接程序创建 UX 组件。
## 在 SharePoint 外接程序中创建 UX
<a name="SP15CreateUX_Creating"> </a>

SharePoint 外接程序的模型提供了许多 UX 组件和机制，以帮助您在 SharePoint 外接程序中感受卓越的用户体验。外接程序模型中的用户体验也非常灵活，足以允许您使用最适合最终用户需求的技术和平台。表 1 列出了一些资源，以帮助您了解如何在外接程序中创建和使用 UX 组件。




**表 1. 用于在 SharePoint 外接程序中创建 UX 组件的资源和指南**


|**文章**|**描述**|
|:-----|:-----|
| [在 SharePoint 外接程序中使用 SharePoint 网站的样式表](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) <br/> |可以在您的 SharePoint 外接程序中引用 SharePoint 网站的样式表，并通过使用 SharePoint 2013 中的样式表将其用于设置您网页的样式。此外，如果有人更改了 SharePoint 网站的样式表或主题，则可以在您的外接程序中采用一组新的样式，而无需修改您的外接程序中的样式表引用。  <br/> |
| [在 SharePoint 外接程序中使用客户端部件版式控制](use-the-client-chrome-control-in-sharepoint-add-ins.md) <br/> |利用 SharePoint 2013 的部件版式控制，您无需注册服务器库或使用特定技术或工具即可在外接程序中使用特定 SharePoint 网站的标题样式。若要使用此功能，您必须通过标准 <script> 标记注册 SharePoint JavaScript 库。您可使用 HTML **div** 元素提供占位符并使用可用选项进一步自定义控件。该控件继承指定的 SharePoint 网站的外观。 <br/> |
| [创建外接程序部件以安装 SharePoint 外接程序](create-add-in-parts-to-install-with-your-sharepoint-add-in.md) <br/> |利用外接程序部件，您可以直接在主机 Web 中表明您的外接程序用户体验。外接程序部件使用 **IFrame** 显示您的外接程序内容。最终用户可以使用可为外接程序部件提供的自定义属性来自定义体验。外接程序网页通过查询字符串中的参数接收自定义属性值。 <br/> |
| [创建自定义操作以部署 SharePoint 外接程序](create-custom-actions-to-deploy-with-sharepoint-add-ins.md) <br/> |在创建 SharePoint 外接程序时，自定义操作使您能够与主机 Web 中的列表及功能区交互。自定义操作会在最终用户安装外接程序时部署到主机 Web。自定义操作可以通过查询字符串打开远程网页并传送信息。外接程序可使用两种类型的自定义操作：功能区和编辑控件块。  <br/> |
| [使用客户端呈现在 SharePoint 外接程序中自定义列表视图](customize-a-list-view-in-sharepoint-add-ins-using-client-side-rendering.md) <br/> |客户端呈现提供了一种机制，您可以使用此机制为 SharePoint 页面中托管的一组控件生成您自己的输出。利用此机制，您可以使用诸如 HTML 和 JavaScript 之类的众所周知的技术来定义 SharePoint 列表视图的呈现逻辑。在客户端呈现中，您可以指定自己的 JavaScript 资源，并在诸如文档库之类的可用于外接程序的数据存储选项中托管它们。  <br/> |
| [在 SharePoint 托管的 SharePoint 外接程序中使用客户端人员选取器控件](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md) <br/> |了解如何在 SharePoint 外接程序中使用客户端"人员选取器"控件。利用客户端"人员选取器"控件，用户可以快速搜索和选择其组织中的人员、组和声明的有效用户帐户。该选取器是提供跨浏览器支持的 HTML 和 JavaScript 控件。  <br/> |
 

## 后续步骤：在 SharePoint 外接程序中处理数据
<a name="SP15CreateUX_Next"> </a>

是否已为外接程序设计了一个很棒的 UX？请将数据与 SharePoint 外接程序的模型中提供的机制相结合。有关详细信息，请参阅 [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md)。




## 其他资源
<a name="SP15CreateUX_AddRes"> </a>


-  [SharePoint 外接程序](sharepoint-add-ins.md)


-  [SharePoint 外接程序的 UX 设计](ux-design-for-sharepoint-add-ins.md)


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)



