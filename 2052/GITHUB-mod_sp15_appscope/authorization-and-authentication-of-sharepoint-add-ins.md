---
title: SharePoint 外接程序的授权和身份验证
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# SharePoint 外接程序的授权和身份验证
大致了解 SharePoint 中的身份验证和授权，这用于授权 SharePoint 外接程序提出的访问 SharePoint 资源的请求。
**观看关于外接程序标识的视频。**








![视频](images/mod_icon_video.png)












## SharePoint 中的外接程序身份验证
<a name="AuthN"> </a>

当 *用户*  登录到 SharePoint 时，说明用户的安全令牌已经过验证。令牌由标识提供程序颁发。SharePoint 支持多种类型的用户身份验证。有关详细信息，请参阅 [SharePoint 2013 中的身份验证、授权和安全性](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx)。



SharePoint 外接程序也是需要进行身份验证和授权的安全主体。可以通过若干种不同的方法对外接程序进行身份验证和授权。有关详细信息，请参阅  [SharePoint 外接程序的三个授权系统](three-authorization-systems-for-sharepoint-add-ins.md)。




## 授权策略：仅用户策略、用户+外接程序策略或仅外接程序策略
<a name="AuthZ"> </a>

授权过程验证经过身份验证的使用者（外接程序或用户或两者）是否有权执行某些操作或访问特定资源（例如列表或 SharePoint 文档文件夹）。



SharePoint 使用三种类型的授权策略。仅用户策略要求对 SharePoint 的调用仅包含已通过身份验证的用户标识。仅外接程序策略要求调用仅包含已通过身份验证的外接程序标识。用户+外接程序策略要求调用包含两种类型的已通过身份验证的标识。当用户通过 SharePoint UI（而非外接程序）访问 SharePoint 资源时，SharePoint 使用仅用户策略。但是，对于从 SharePoint 外接程序发出的调用，SharePoint 始终使用仅外接程序或用户+外接程序策略。SharePoint 外接程序确定访问令牌类型使用哪种策略，该访问令牌类型包含在其对 SharePoint 的请求中。如果发出用户+外接程序请求，SharePoint 将要求外接程序和用户都具有对外接程序所访问资源的权限。如果是仅外接程序请求，SharePoint 要求外接程序具有对资源的权限，但用户是否具有权限则不重要。（仅当 SharePoint 外接程序被事先授予执行此操作的权限时，才能发出仅外接程序请求；通常是在其已安装的情况下。）



有关授权策略以及策略工作方式的详细信息，请参阅 [SharePoint 2013 中的外接程序授权策略类型](add-in-authorization-policy-types-in-sharepoint-2013.md)。




## 外接程序权限和外接程序权限请求作用域
<a name="Permissions"> </a>

SharePoint 外接程序开发人员必须通过外接程序清单文件，指定外接程序对外接程序 Web 外部的 SharePoint 资源所需的权限。（外接程序自动拥有整个外接程序 Web 的完全控制权限。）当外接程序设计为从 SharePoint 内部启动时，外接程序安装基础结构将提示安装外接程序的用户授予或拒绝所需的权限。授予权限后，网站用户即可使用外接程序，无需重新授予权限。但是，当外接程序设计为从 SharePoint 外部启动，即未在 SharePoint 上安装时，则 SharePoint 将提示执行外接程序的用户在每次外接程序运行时授予所需的权限。移动设备上的外接程序和 Office 外接程序是可以访问 SharePoint 的外接程序示例，但未在其上安装。



只有网站所有者可以在 SharePoint 网站上安装 SharePoint 外接程序（除非已创建具有外接程序安装权限的自定义角色）。用户只能向外接程序授予他/她自己所具有的权限。因此用户无法安装需要用户不具备的权限的外接程序。同样，用户无法运行和从外部启动需要用户不具备的权限的外接程序。但是，当 SharePoint 外接程序已在 SharePoint 上安装时，它可以请求发出仅外接程序调用的权限，具有该权限的外接程序可以通过运行外接程序的用户无权执行此操作的方式访问 SharePoint。



外接程序可能还具有 SharePoint Online 租户管理员或 SharePoint 场管理员调用或授予的权限。



在外接程序清单文件中，SharePoint 外接程序指定它正确运行所需的权限。权限请求指定外接程序需要的权限以及它们需要权限的作用域。作用域指定了 SharePoint 层次结构中应用权限请求的位置。SharePoint 支持四种不同的内容作用域：租户、网站集、网站和列表。还有一些适用于执行搜索查询、访问分类数据、社交功能、Microsoft Business Connectivity Services (BCS) 功能和 Project Server 2013 功能的特殊作用域。有关详细信息，请参阅  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)。




## 何时使用 OAuth？
<a name="FileName_uniquekeyword4"> </a>

您可能已听说，OAuth 2.0 在 SharePoint 外接程序的身份验证和授权过程中扮演着重要角色。的确如此，但这并不是每个 SharePoint 外接程序的授权过程的必要组成部分。如果计划生成使用服务器端代码在远程 Web 应用程序中运行并向 SharePoint 回信的 SharePoint 外接程序，则将需要使用 OAuth。如果远程 Web 应用程序不在本地，您可使用 Azure ACS 作为访问令牌颁发者的 [低信任授权系统](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)。如果是本地部署，则您通常将使用外接程序本身或数字证书充当访问令牌颁发者的 [高信任系统](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)。



您不应该使用 OAuth 从外接程序 Web 本身的页面上的 JavaScript 或从使用 [跨域库](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)的远程网页中进行调用。有关跨域库的详细信息，请参阅 [创建使用跨域库的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)。




## 其他资源
<a name="Filename_AdditionalResources"> </a>


-  [SharePoint 外接程序的三个授权系统](three-authorization-systems-for-sharepoint-add-ins.md)


-  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)


-  [SharePoint 外接程序](sharepoint-add-ins.md)



