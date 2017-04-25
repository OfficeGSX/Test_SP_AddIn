---
title: SharePoint 外接程序的三个授权系统
ms.prod: SHAREPOINT
ms.assetid: 623fdab7-856e-4a89-9f5d-748a2ba1ef2e
---


# SharePoint 外接程序的三个授权系统
了解 SharePoint 外接程序可用于获取对 SharePoint 资源的授权的三个系统。
在 SharePoint 中，SharePoint 外接程序是一个身份主体，就像用户一样，必须通过身份验证并得到授权才能使用 SharePoint 资源。外接程序可以使用三种授权系统。它们不会相互排斥。





## 了解这三个授权系统以及何时使用它们
<a name="UnderstandThreeSystems"> </a>






- **低信任**：提供程序托管的 SharePoint 外接程序可以注册到 Microsoft Azure 访问控制服务 (ACS)，它会向外接程序发出访问令牌，允许外接程序访问外接程序安装所在的 SharePoint 租户或服务器场中的资源。Azure ACS 是 OAuth 2.0 Framework"流"中的受信任令牌颁发者，其中包括 SharePoint 和外接程序的远程组件。使用此系统的外接程序可以在 Office 商店中出售。低信任系统主要用于其远程组件托管在云中的外接程序。

    有关创建使用低信任系统的 SharePoint 外接程序的详细信息，请参阅 SDK 节点 [创建使用低信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)。

    > **注释**
      > 安装外接程序的客户必须具有 Office 365 帐户。向外接程序授予 Azure ACS 访问权限时需要此帐户。但是客户不需要将此帐户用于任何其他目的，在服务器场上完成一些简单的配置任务后，外接程序即可安装到本地 SharePoint 场。 
- **高信任**：提供程序承载的外接程序可以使用数字证书，与 SharePoint 建立信任。高信任系统主要用于其远程组件在本地承载的外接程序。外接程序可以安装到未连接到 Internet 的 SharePoint 场。外接程序不能安装到 SharePoint Online，也不能在 Office 商店中出售。

    有关创建使用高信任系统的 SharePoint 外接程序的详细信息，请参阅 SDK 节点 [创建使用高信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)。


- **跨域库**：当外接程序的业务逻辑位于 JavaScript 中时，您有两种使用 SharePoint 跨域库的选项，即在低信任系统和高信任系统所在位置使用，或者作为一种补充使用。该库还用于外接程序具有云托管的组件，但客户的公司防火墙使其很难使用低信任系统的情况。用户的浏览器阻止来自其他域的脚本，但库封装一个安全的系统以绕过此限制。使用该库的外接程序可以在 Office 商店中出售，且可以安装到 SharePoint Online 或本地 SharePoint。

    有关创建使用跨域库的 SharePoint 外接程序的详细信息，请参阅 SDK 节点 [创建使用跨域库的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)、博客文章 [解决 SharePoint 外接程序中的跨域问题](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)。



## 关于 OAuth 2.0 Framework 及其 SharePoint 实施的背景信息
<a name="UnderstandThreeSystems"> </a>

三个授权系统中有两个使用 OAuth 2.0 Framework。OAuth 2.0 是一种 **开放式授权框架** 。OAuth 使用一种标准方法从桌面、设备和 Web 应用程序中启用安全授权。 OAuth 使用户可以批准应用程序代表其执行操作，而无需共享其用户名和密码。例如，它 **使用户可以在一个网站上与另一个网站共享** 其私有资源或数据（联系人列表、文档、照片、视频等）， **without requiring the user to provide his or her credentials**（通常为用户名和密码）到其他网站。



OAuth 使用户能够提供由指定服务提供程序（如 SharePoint）承载的数据的访问令牌。每个令牌向特定资源提供程序（如 SharePoint 网站）提供在指定时间内（例如 12 小时）对特定资源（例如 SharePoint 文档库中的文档）的访问权限。这样用户可以向第三方 Web 或桌面应用程序授予对存储在另一个资源或服务提供程序（例如 SharePoint）中的信息的访问权限，而无需共享用户名和密码以及存储在提供程序中的 *所有*  数据。



SharePoint 使用 **OAuth 2.0** Framework **，后者使用令牌传递"流"** 执行以下操作：




- 通过 SharePoint 外接程序授权请求以访问 SharePoint 资源。


- 对 Office 商店、外接程序目录或开发人员租户中的外接程序进行身份验证。


有关 OAuth 和 OAuth 术语的详细信息与背景，请参阅  [OAuth.net](http://oauth.net/) 和 [Web 授权协议 (oauth)](http://datatracker.ietf.org/doc/active/)。总而言之，有一个托管受保护资源的资源服务器、资源的所有者、需要访问资源的客户端应用程序，以及在收到资源所有者指示时颁发资源访问令牌的授权服务器。正式的 OAuth 文档倾向于假定这样一种场景，即每当客户端应用程序运行时，都有一个资源所有者授予对客户端应用程序中的资源的访问权限。SharePoint 实施会考虑到 SharePoint 资源（如列表）在多个用户之间共享的情况。此外，在 SharePoint 实施中，SharePoint 外接程序在安装时被授予访问权限，而不是在每次运行时；它可以由安装并向其授予访问权限的人员以外的用户运行。（如果外接程序未安装到 SharePoint 上，但会访问 SharePoint 资源（因此它们在扩展意义上只是"SharePoint 外接程序"），运行外接程序的用户无需在每次外接程序运行时都授予权限。）



因此，在 SharePoint 实施中，OAuth 角色由下列组件扮演：




- SharePoint 外接程序的远程组件扮演客户端应用程序的角色。


- SharePoint 用户扮演资源所有者的角色。


- SharePoint 扮演资源服务器的角色。


- 使用 [低信任授权系统](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)时，Azure ACS 扮演授权服务器的角色。使用 [高信任系统](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)时，外接程序本身（以及数字证书）将成为授权服务器。



### 访问令牌不是登录令牌
<a name="FileName_uniquekeyword3"> </a>

如上所述，要访问资源，外接程序必须从 OAuth 授权服务器请求访问令牌，此服务器之前被资源所有者指定为受信任的安全令牌颁发者 (STS)。相反，WS-Federation STS 和安全声明标记语言 (SAML) 被动登录 STS 主要用于发放登录令牌。在 SharePoint 中，OAuth STS 不用于发放登录令牌，即它们不用作身份提供程序。因此，您将不会发现用户登录页面、管理中心内的"身份验证提供程序"部分或 SharePoint 内的人员选择器中列出了 OAuth STS。



SharePoint 管理员可以使用 Windows PowerShell 命令启用或禁用 OAuth STS，方式与它们在 SharePoint 2010 中启用或禁用受信任登录提供程序一样。




