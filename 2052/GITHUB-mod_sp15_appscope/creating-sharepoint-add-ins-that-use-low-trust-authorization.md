---
title: 创建使用低信任授权的 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# 创建使用低信任授权的 SharePoint 外接程序
了解适用于 SharePoint 外接程序的低信任授权系统。
SharePoint 外接程序（或外部应用程序）中的远程组件可以通过每个 HTTP 请求，将访问令牌传递到 SharePoint，来获取对 SharePoint 资源的授权。远程组件从与客户的 Office 365 租户相关的 Microsoft Azure 访问控制服务 (ACS) 帐户获取访问令牌。Azure ACS 在  [OAuth 2.0](http://oauth.net/) 事务中充当授权服务器，称为流，其中 SharePoint 充当资源服务器，远程组件充当客户端。有关相关的协议规范，请参阅  [Web 授权协议 (oauth)](http://datatracker.ietf.org/doc/active/#oauth)。




使用低信任授权系统的提供程序托管的 SharePoint 外接程序可以在 Office 商店中出售，并安装在 Microsoft SharePoint Online 或本地 SharePoint 场中，该服务器场已配置为使用客户的 Office 365 租户与 Azure ACS 建立信任。客户必须具有 Office 365 租户才能安装使用低信任系统的 SharePoint 外接程序。但是，客户无需使用租户用于任何其他目的。有关将本地场链接到 Office 365 租户的说明，请参阅 [使用 Office 365 SharePoint 网站在本地 SharePoint 网站中对提供程序托管的外接程序进行授权](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)。
## 注册到 Azure ACS
<a name="Registration"> </a>

要使用低信任系统，必须先将 SharePoint 外接程序注册到 Azure ACS 以及 SharePoint 场或 SharePoint Online 租户的应用程序管理服务。（称为"应用程序管理服务"是因为 SharePoint 外接程序最初称为"SharePoint 相关应用程序"）对于通过 Office 商店 销售的外接程序，注册到 ACS 的操作在卖家面板中执行，注册服务的操作在安装外接程序时执行。对于在组织外接程序目录中分发的外接程序，注册 ACS 和服务的操作在要安装外接程序的任何 SharePoint 租户或场的 \\_Layouts\\15\\AppRegNew.aspx 页上执行。访问 SharePoint 的外部非 SharePoint 应用程序也需要进行注册。该目录包含 Office 外接程序、Windows 应用商店应用、Web 应用程序以及智能手机应用程序等设备应用程序。




> **注释**
> 注册要求应用程序具有 Internet 域。任何现有域均可用于此目的，但您不能完全确定您未拥有的任何域将始终存在，因此 Web 应用程序需为本机设备应用程序的一部分，即使 Web 应用程序组件除启用注册外不扮演任何其他角色也是如此。有关使用这种方法设计的高级代码示例，请参阅 [使用外接程序模型分批预置网站](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6)。 




有关注册的详细信息，请参阅 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)。




### 外接程序密码过期

外接程序密码必须每 12 个月更换一次。有关详细信息，请参阅 [替换 SharePoint 外接程序中即将过期的客户端密码](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)。




## 授权策略
<a name="Policies"> </a>

SharePoint 外接程序可以设计为使用以下两种授权策略之一：




- **用户 + 外接程序策略：**使用此策略的外接程序只能执行外接程序和用户均有权限执行的操作。默认使用此策略，除非开发人员执行特定的步骤以使用其他策略。


- **仅应用程序的策略：**使用此策略的外接程序可以执行其有权限执行的任何操作，即使用户没有执行此操作的权限。开发人员必须请求此策略用于外接程序的外接程序清单。请求必须经过安装外接程序的用户批准。仅提供程序托管的 SharePoint 外接程序允许使用此策略。


有关授权策略的详细信息，请参阅  [SharePoint 2013 中的外接程序授权策略类型](add-in-authorization-policy-types-in-sharepoint-2013.md)。




## 两种 OAuth 运行时流
<a name="Flows"> </a>

每当访问 SharePoint 的云托管的 SharePoint 外接程序或外部应用程序运行时，将发生流，即外接程序、SharePoint 2013、ACS 和（有时）最终用户之间的一系列互动。流的最终结果是 SharePoint 接收访问令牌，令牌包含在应用程序向 SharePoint 发出的每个创建、更新、删除 (CRUD) 请求中。



有两个供 SharePoint 使用的主要 OAuth 流。一个用于云承载的 SharePoint 外接程序，另一个称为"临时"流，用于访问 SharePoint 数据的其他平台上的应用程序。




- **上下文令牌流**：SharePoint 外接程序的远程组件使用 SharePoint 客户端对象模型 (CSOM) 或 REST 终结点向 SharePoint 发出调用。SharePoint 从可以发送至远程服务器的 ACS 中请求上下文令牌。远程服务器使用上下文令牌从 ACS 请求访问令牌。然后远程服务器使用访问令牌返回 SharePoint。有关此流的详细信息，请参阅  [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)。


- **身份验证代码流：**SharePoint 外接程序在安装时被授予访问 SharePoint 资源所需的权限。但是未安装在 SharePoint 上的应用程序必须请求"临时"权限，即每次运行时请求的权限。此流中没有 SharePoint 上下文令牌。相反，当外接程序运行并尝试访问 SharePoint 时，SharePoint 将提示用户授予对所请求应用程序的权限。当用户授予权限后，SharePoint 从传递到应用程序的 ACS 中获取授权代码。应用程序使用代码从 ACS 获取访问代码，然后它可以与 SharePoint 通信。有关此流的详细信息，请参阅  [SharePoint 外接程序的身份验证代码 OAuth 流](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。



## 对低信任的 SharePoint 外接程序进行故障排除
<a name="Trouble"> </a>

本文提供了关于使用低信任授权系统的 SharePoint 外接程序中某些特定问题的常规故障排除指导和信息。




### 使用 Fiddler 工具

免费的  [Fiddler 工具](http://www.telerik.com/fiddler)可用于捕获您外接程序的远程组件发送到 SharePoint 的 HTTP 请求。提供一个 [工具的免费扩展](https://github.com/andrewconnell/SPOAuthFiddlerExt)，可对请求中的访问令牌自动解码。




### 开发期间关闭对 OAuth 的 HTTPS 要求
<a name="TurnOffHTTPRequirement"> </a>

OAuth 要求 SharePoint 运行 HTTPS（并不只是您的服务，还包括 SharePoint）。当您开发外接程序时，这可能会产生阻碍。例如，如果您在调试外接程序时尝试调用 SharePoint，您可能会收到 403（禁止）消息，这是因为您的外接程序运行所在的"localhost"中不支持 SSL。



您可以使用以下 Windows PowerShell cmdlet 在开发过程中禁用 HTTPS 要求。






```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()

```

若要稍后重新启用 HTTPS 要求，请使用以下 Windows PowerShell cmdlet。






```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()

```


### 其他 SSL 及和域有关的授权错误
<a name="DomainRelatedErrors"> </a>

配置文件和注册表单中的域名不匹配可能导致无法授权。下面四个值必须完全相同：




- "外接程序域"，当 SharePoint 外接程序在 AppRegNew.aspx 或卖家面板上注册时指定。


- 远程 Web 应用程序的安全证书在其中注册的域。


- AppManifest.xml 文件中 **StartPage** 值的域部分。


- AppManifest.xml 中指定的任何事件接收器的 URL 的域部分。


关于这一点，请注意：




- 如果 SharePoint 外接程序的远程组件使用除 443 以外的任何端口，您必须将端口明确包含为所有位置的域部分；例如，  `contoso.com:3333`。（您必须使用默认端口为 443 的 HTTPS 协议。）


- 如果您为远程 Web 应用程序创建 DNS CNAME 别名，请为所有位置的域值使用 CNAME 别名。例如，如果您的应用程序托管在 contoso.cloudapp.net 上，且您为其创建 contososoftware.com 的一个 CNAME，请使用 contososoftware.com 作为域。


- 需在 AppManifest.xml 文件的 **StartPage** 值（以及任何事件接收器 URL）中将域进行硬编码，然后才能将外接程序打包。如果您使用 Visual Studio 中的"发布"向导打包外接程序，将提示您输入域，Visual Studio Office 开发人员工具 会为您将域插入 **StartPage** 值（取代在调试过程中使用的 `~remoteWebUrl` 令牌。但是，如果您不使用"发布"向导，或者即使使用但您的远程 Web 应用程序部署到 Azure，您必须使用域和协议手动替换令牌；例如 `https://contososoftware.com` 或 `https://MyCloudVM:3333`。



### 错误"基本连接已关闭：无法为 SSL/TLS 安全通道建立信任关系"。
<a name="ErrorConnectionClosed"> </a>

此错误是 SSL 握手问题，不是 OAuth 问题。确保 SharePoint 信任您正在使用的证书，并且该证书与端点名称匹配。




### 使用 HTTP DAV 方法从 SharePoint 读取文件时出错
<a name="ErrorConnectionClosed"> </a>

HTTP DAV 与 OAuth 不兼容。如果您正在使用 SharePoint 客户端对象模型 (CSOM)，请使用以下代码读取文件。




```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();

```


## 本节内容
<a name="Trouble"> </a>

 [使用 Office 365 SharePoint 网站在本地 SharePoint 网站中对提供程序托管的外接程序进行授权](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)



 [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)



 [SharePoint 外接程序的身份验证代码 OAuth 流](authorization-code-oauth-flow-for-sharepoint-add-ins.md)



 [替换 SharePoint 外接程序中即将过期的客户端密码](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)




## 其他资源
<a name="FileName_AdditionalResources"> </a>


-  [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)



