---
title: 发布 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# 发布 SharePoint 外接程序
查找文章和资源以帮助您发布您的 SharePoint 外接程序。
## 开始发布外接程序
<a name="bk_gettingstarted"> </a>

您已完成开发您的 SharePoint 外接程序 最后一步是使让用户可以使用该外接程序。您可以通过将外接程序发布到两个位置之一来实现此目的：




- **公共 Office 商店。** 将您的外接程序发布到 Office 商店可公开提供外接程序，因此，任何 SharePoint 部署的用户都可以获得它。


- **内部组织的外接程序目录。** 将您的外接程序发布到内部组织的外接程序目录中（在您的 SharePoint 部署上托管)，使能够访问该 SharePoint 部署的用户都可以使用您的外接程序。


有关如何使用 Visual Studio 2008 打包您的外接程序以供发布的信息，请参阅 [使用 Visual Studio 发布 SharePoint 外接程序](publish-sharepoint-add-ins-by-using-visual-studio.md)。




### 发布到 Office 商店

若要发布外接程序到 Office 商店，您必须首先 [作为 Microsoft 开发人员进行注册](https://sellerdashboard.microsoft.com/Registration)。 



当您将外接程序上载到 Office 商店发布时，Microsoft 会执行一系列验证检查，以确保您的外接程序符合外接程序内容和行为准则。例如，它会检查外接程序清单标记是否有效且完整，并验证外接程序中的 SharePoint 解决方案包（.wsp 文件）是否包含不允许的元素，或 SharePoint 功能的范围是否比 Web 更广。还会检查包中是否有不允许的内容。如果外接程序包通过所有测试，会将其打包到一个文件并由 Microsoft 签署。



当您上载您的外接程序以在 Office 商店上发布时，您可以选择要提供给下载它的用户的许可证的条款。使用此外接程序许可证来决定：




- 您提供外接程序的方式为免费、试用还是购买。


- 您的外接程序是否按用户或网站来提供。


SharePoint 不强制实施外接程序使用的许可条款  它提供了一个许可框架，允许您在外接程序中包含代码逻辑来强制实施您选择的许可限制。例如，您可以在外接程序中包含代码逻辑，使有付费许可证的用户能够访问外接程序的某些功能，而拥有试用许可证的用户则不能。有关详细信息，请参阅  [许可 Office 和 SharePoint 加载项](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)。




### 发布到外接程序目录

如果您创建 SharePoint 外接程序以供您自己的公司或特定企业客户端使用，而不是面向一般公众，则您可能要将外接程序发布到在 SharePoint 上托管的内部外接程序目录。专用外接程序目录是 SharePoint 2013 Web 应用程序（或 SharePoint Online 租赁）中的一个专用网站集合，用于托管 SharePoint 外接程序和 Office 外接程序的文档库。将目录放在自己的站点集合中更便于 Web 应用程序管理员或租户管理员来限制对该目录的权限。



将 SharePoint 外接程序上载到企业外接程序目录与将任何文件上载到 SharePoint 文档库一样容易。您需要填写弹出窗体，在其中提供外接程序包的本地 URL 和其他信息，例如外接程序的名称。将外接程序上载到外接程序目录后，会对其进行类似的检查，未通过检查的外接程序将标记为无效或在该目录中禁用。




## 决定 SharePoint 外接程序的发布位置
<a name="bk_decide"> </a>

下表对发布到 Office 商店和发布到外接程序目录进行了比较，并列出了决定外接程序的发布位置时要考虑的问题。我们建议您首先决定计划发布外接程序的位置，然后进行设计和开发；在某些情况下（如许可），外接程序的发布位置会影响您的外接程序的开发与设计。




**表 1. 外接程序发布位置的注意事项**


|**Office 商店**|**外接程序目录**|
|:-----|:-----|
|外接程序公开可用。  <br/> |对此 SharePoint 部署具有访问权限的用户可使用外接程序  <br/> |
|许可框架可用。  <br/> |许可框架不可用。  <br/> |
|外接程序包由 Microsoft 进行技术和内容政策验证。  <br/> |在上载外接程序时由 SharePoint 执行外接程序包验证。  <br/> |
|您必须向 Microsoft 卖家面板注册才能上载外接程序。  <br/> |无需向 Microsoft 注册。  <br/> |
 

## 其他资源
<a name="bk_addresources"> </a>


-  [在卖方仪表板中创建或更新客户端 ID 和密码](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)


-  [使用"卖家面板"将 Office 和 SharePoint 外接程序和 Office 365 应用提交到 Office 应用商店](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)


-  [提交到 Office 应用商店的应用和外接程序的验证策略（版本 2.0）](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)


-  [开始构建 Office 和 SharePoint 相关应用程序](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)


-  [许可 Office 和 SharePoint 加载项](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)


-  [部署和安装SharePoint 外接程序：方法和选项](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [SharePoint 外接程序的租赁和部署范围](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)


-  [使用 Visual Studio 发布 SharePoint 外接程序](publish-sharepoint-add-ins-by-using-visual-studio.md)


-  [发布 Office 外接程序商店](http://social.msdn.microsoft.com/Forums/zh-cn/officestore)



