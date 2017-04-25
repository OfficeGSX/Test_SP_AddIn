---
title: 对高信任的 SharePoint 外接程序进行故障排除
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# 对高信任的 SharePoint 外接程序进行故障排除
获取有关开发高信任的 SharePoint 外接程序时遇到的问题的相关帮助。
本文介绍了 Fiddler 工具，还提供了解决某些特定问题的指导。





## 使用 Fiddler 工具

免费的  [Fiddler 工具](http://www.telerik.com/fiddler)可用于捕获您的外接程序的远程组件发送到 SharePoint 的 HTTP 请求。有一个 [工具的免费扩展](https://github.com/andrewconnell/SPOAuthFiddlerExt)，可对请求中的访问令牌自动解码。



在 Web 应用程序服务器上安装 Fiddler 后，将以下标记添加到您的 web.config 文件中，以使远程 Web 应用程序提出的请求经过此代理。这样，您就可以在收到错误时捕获 Fiddler 痕迹以及查看 SharePoint 的完整响应。




> **注释**
> 如果未运行 Fiddler，请确保删除此标记。如果未删除此标记，则您的外接程序将无法提出 HTTP 请求。 







```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>

```

安装 Fiddler 之后，您也可以检查 SharePoint 的响应头，这些响应头将包含请求 GUID。此请求 GUID 是一个相关 ID，您可以在日志中查找此 ID 以查找与该请求关联的任何日志错误。




## 401 未授权错误
<a name="UnauthorizedException"> </a>

当高信任外接程序首次访问 SharePoint 时，有多种因素可能导致 **401 Unauthorized** 错误。如果您使用客户端对象模型 (CSOM)，错误将如下所示：




```cs

[WebException: The remote server returned an error: (401) Unauthorized.]
   System.Net.HttpWebRequest.GetResponse() +8515936
   Microsoft.SharePoint.Client.SPWebRequestExecutor.Execute() +178
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQueryToServer(ChunkStringBuilder sb) +1427
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQuery() +270
   Microsoft.SharePoint.Client.ClientRuntimeContext.ExecuteQuery() +146
   Microsoft.SharePoint.Client.ClientContext.ExecuteQuery() +666
   S2STestWeb.Default.Page_Load(Object sender, EventArgs e) in c:\\MyFiles\\HightrustTest\\HightrustTestWeb\\Default.aspx.cs:28
   System.Web.UI.Control.LoadRecursive() +71
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178
```

如果您使用 TokenHelper 文件和 Windows 标识，触发异常的代码将如下所示：






```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();
```

对问题进行故障排除的第一步是使用 Visual Studio 调试程序确认访问令牌和 **ClientContext** 对象构建成功。如果成功，请研究以下可能性：



 **可能的问题和解决方案：**




- 没有为访问远程 Web 应用程序创建用户配置文件。请创建用户配置文件。


- 您的外接程序对您正在尝试访问的资源没有权限。打开 SharePoint Management Shell 并运行以下 Windows PowerShell cmdlet。变量  `$web` 为您尝试获取访问权限的 SharePoint 网站， `$appPrincipal` 为外接程序 ID。有关详细信息，请参阅 [Set-SPAppPrincipalPermission](http://technet.microsoft.com/zh-cn/library/jj219714%28v=office.15%29.aspx)。

  ```

Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl
  ```

- 您的 Web 应用程序将接受匿名请求。这意味着访问令牌中没有真实的用户标识。请确保在 IIS 中已经对远程 Web 应用程序的根目录禁用了匿名访问。您也可以通过以下操作对其进行检查：调试远程 Web 应用程序并在 default.aspx.cs（或 .vb）文件中检查 **Request.LogonUserIdentity** 的值以确保它不是匿名用户。


- 您的数字证书未添加到受信任的证书存储中。请务必遵循 [打包和发布高度可信的 SharePoint 外接程序](package-and-publish-high-trust-sharepoint-add-ins.md)中的程序。



## 其他 SSL 及和域有关的授权错误
<a name="DomainRelatedErrors"> </a>

配置文件和注册表单中的域名不匹配可能导致无法授权。下面四个值必须完全相同：




- "外接程序域"，当 SharePoint 外接程序在 AppRegNew.aspx 上注册时指定。


- 远程 Web 应用程序的安全证书在其中注册的域。


- AppManifest.xml 文件中 **StartPage** 值的域部分。


- AppManifest.xml 中指定的任何事件接收器的 URL 的域部分。


关于这一点，请注意：




- 如果 SharePoint 外接程序的远程组件使用除 443 以外的任何端口，您必须将端口明确包含为所有位置的域部分；例如，  `MarketingServer:3333`。（您必须使用默认端口为 443 的 HTTPS 协议。）


- 需在 AppManifest.xml 文件的 **StartPage** 值（以及任何事件接收器 URL）中将域进行硬编码，然后才能将外接程序打包。如果您使用 Visual Studio 中的"发布"向导打包外接程序，将提示您输入域，Visual Studio Office 开发人员工具 会为您将域插入到 **StartPage** 值（取代在调试过程中使用的 `~remoteWebUrl` 令牌。但是，如果您不使用"发布"向导，您必须使用域（和协议）手动替换令牌；例如 `https://MarketingServer` 或 `https://MarketingServer:3333`。



## 显示没有包含该序列号的证书的运行时错误
<a name="DomainRelatedErrors"> </a>

如果您确信 web.config 中具有正确的证书序列号，您可以在 **Windows 证书存储** 中进行查看，web.config 中的序列号可能存在隐藏的额外字符。如果从 **Microsoft 管理控制台** 复制并粘贴序列号，可能发生此问题。从 web.config 中删除完整的序列号值并 *手动*  重新键入。




