---
title: 创建使用高信任授权的 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: 135bf6be-b118-4d2c-8d40-27f5060b06f3
---


# 创建使用高信任授权的 SharePoint 外接程序
了解高信任 SharePoint 外接程序，这些应用程序使用数字证书在 SharePoint 和访问 SharePoint 的远程组件之间建立信任。
 **提供者：**
  
    
    
Steve Peschka，Microsoft Corporation
  
    
    
Siew Moi Khor，Microsoft Corporation
  
    
    


**观看关于创建高信任 SharePoint 外接程序的视频。**

  
    
    

  
    
    
![视频](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## 高信任 SharePoint 外接程序概述
<a name="Overview"> </a>

高信任外接程序是提供程序托管的 SharePoint 外接程序，它安装在本地 SharePoint 场上。它无法安装到 Microsoft SharePoint Online 或通过 Office 商店推广。高信任外接程序使用证书而不是上下文令牌来建立信任。
  
    
    

> **注释**
>  本主题将帮助您了解 SharePoint 外接程序的高信任授权系统。有关创建和部署高信任外接程序的实用信息，请参阅下列主题：>  [创建 SharePoint 高信任外接程序](create-high-trust-sharepoint-add-ins.md)>  [打包和发布高度可信的 SharePoint 外接程序](package-and-publish-high-trust-sharepoint-add-ins.md)
  
    
    

在 SharePoint 2013 中，安全令牌服务 (STS) 提供了用于服务器到服务器身份验证的访问令牌。STS 使临时访问令牌可以访问其他应用程序服务，例如 Exchange 2013、Lync 2013 和 SharePoint 外接程序。场管理员可以使用 Windows PowerShell cmdlet 和证书在 SharePoint 和其他应用程序或外接程序之间建立信任。使用的每个证书必须使用  `New-SPTrustedRootAuthority` cmdlet 获得 SharePoint 2013 的信任。此外，每个证书都必须使用 `New-SPTrustedSecurityTokenIssuer` cmdlet 在 SharePoint 中注册为令牌颁发者。
  
    
    

> **注释**
> STS 不适用于用户身份验证。因此，在管理中心中用户登录页的"身份验证提供程序"部分，或 SharePoint 2013 的人员选取器中不会列出 STS。 
  
    
    


## 两类令牌颁发者
<a name="TwoKindsOfIssuers"> </a>

高信任 SharePoint 外接程序的远程 Web 应用程序绑定到数字证书。（有关如何完成此操作的信息，请参阅上面链接的两个主题。）远程 Web 应用程序使用证书签发其包含在对 SharePoint 的所有请求中的访问令牌。Web 应用程序使用证书的私钥签署令牌，SharePoint 使用证书的公钥对其进行验证。证书必须注册为信任令牌签发者，然后 SharePoint 才能信任其签发的令牌。有两类令牌签发者，某些签发者仅签发对特定 SharePoint 外接程序的令牌，其他签发者（称为信任代理）可以签发对多个 SharePoint 外接程序的令牌。
  
    
    
从实际来看，SharePoint 场管理员确定哪些类型的令牌签发者由它们在  `New-SPTrustedSecurityTokenIssuer` cmdlet 中使用的开关和参数值创建。要创建作为信任代理的令牌签发者，请将 `-IsTrustBroker` 开关添加到命令行并对 `-Name` 参数使用唯一值，而非外接程序的客户端 ID。下面是一个已编辑的示例。
  
    
    


```

New-SPTrustedSecurityTokenIssuer -IsTrustBroker -RegisteredIssuerName "<full_token_issuer_name> " --other parameters omitted--```

要创建非代理令牌签发者，请勿使用  `-IsTrustBroker` 开关。没有其他区别。 `-RegisteredIssuerName` 参数的值始终为"@"字符分开的两个 GUID 形式，即 _GUID_@ _GUID_。右侧的 GUID 始终为 SharePoint 场的身份验证领域（或站点订阅）的 ID。左侧的 GUID 始终为令牌签发者的特定 ID。创建为 *信任代理*  的令牌签发者时，这是一个随机 GUID。但当创建非代理令牌签发者时，特定签发者 GUID 必须为用作 SharePoint 外接程序 客户端 ID 的同一 GUID。此参数不仅为签发者提供了一个名称，还会告知 SharePoint 哪个 SharePoint 外接程序是证书可以向其签发令牌的唯一应用程序。下面是一个部分示例：
  
    
    


```
$fullIssuerIdentifier = "<client_ID_of_SP_app> " + "@" + "<realm_GUID> "
New-SPTrustedSecurityTokenIssuer -RegisteredIssuerName $fullIssuerIdentifier --other parameters omitted--```

通常， `New-SPTrustedSecurityTokenIssuer` cmdlet 用于执行其他任务以便为高信任外接程序配置 SharePoint 的脚本中。有关此类脚本和 `New-SPTrustedSecurityTokenIssuer` cmdlet 的完整示例的详细信息，请参阅 [SharePoint 2013 的高信任配置脚本](high-trust-configuration-scripts-for-sharepoint-2013.md)。
  
    
    

## 决定对高信任 SharePoint 外接程序使用一个还是多个证书
<a name="Deciding"> </a>

获取和管理供高信任 SharePoint 外接程序使用的证书时，SharePoint 和网络管理员具有两个可选的基本策略：
  
    
    

- 将同一证书用于多个 SharePoint 外接程序。
    
  
- 为每个 SharePoint 外接程序使用单独的证书。
    
  
本节讨论每个策略的优点和缺点。
  
    
    
将同一证书用于多个 SharePoint 外接程序的好处是管理员要信任和管理的证书较少。此方法的劣势是，当遇到要打破对某个特定 SharePoint 外接程序的信任的情况时，管理员打破信任的唯一方法是删除作为令牌颁发者或根证书颁发机构的证书。证书同时会打破对所有使用同一证书的其他SharePoint 外接程序的信任，这会导致它们停止工作。
  
    
    
要使仍然受信任的 SharePoint 外接程序再次运行，管理员将需要 *使用新证书*  创建 `New-SPTrustedSecurityTokenIssuer` 对象并包括 `-IsTrustBroker` 标记。然后必须在托管任何受信任的外接程序的每台服务器上注册新证书，每个外接程序必须绑定到新证书。
  
    
    
对每个外接程序使用一个证书的优势是可以更轻松地打破与特定外接程序的信任，因为当管理员打破与一个外接程序的证书的信任时，供仍然受信任的外接程序使用的证书不会受影响。此策略中的劣势是管理员需要获取多个证书，且 SharePoint 必须配置为使用每个证书，这可以通过一个可重用脚本实现，如  [SharePoint 2013 的高信任配置脚本](high-trust-configuration-scripts-for-sharepoint-2013.md)中所示。
  
    
    

## 证书是 SharePoint 中的根证书颁发机构
<a name="RootAuthorities"> </a>

如本文开头所述，SharePoint 场管理员必须将高信任外接程序中的远程 Web 应用程序的证书设为 SharePoint 中的信任根证书颁发机构以及信任令牌颁发者。事实上，当 Web 应用程序证书背后存在证书颁发机构的层次结构时，链中的所有证书都必须添加到 SharePoint 的信任根证书颁发机构列表。
  
    
    
链中的每个证书都必须通过调用  `New-SPTrustedRootAuthority` cmdlet 添加到 SharePoint 的信任根证书颁发列表中。例如，假定 Web 应用程序的证书是 LAN 上的企业域证书颁发机构颁发的域证书，并假定其证书由 LAN 上的独立顶级证书颁发机构颁发。在此方案中，顶级 CA、中间 CA 和 Web 应用程序的证书都必须添加到 SharePoint 的信任根证书颁发机构列表中。以下 Windows PowerShell 代码将实现此操作。
  
    
    


```

$rootCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<path_to_top-level_CA's_cer_file>")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $rootCA

$intermediateCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_intermediate_CA's_cer_file")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $intermediateCA

$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_web_application's_cer_file") 
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $certificate 
```

根证书和中间证书在 SharePoint 场上仅添加一次。通常，Web 应用程序的证书会添加到执行其他配置的单独脚本中，例如调用  `New-SPTrustedSecurityTokenIssuer`。有关示例，请参阅  [SharePoint 2013 的高信任配置脚本](high-trust-configuration-scripts-for-sharepoint-2013.md)。
  
    
    
正如开发和调试外接程序的情况一样，如果 Web 应用程序的证书是自签名证书，则没有证书链，且只有 Web 应用程序的证书需要添加到根证书颁发结构列表中。
  
    
    
有关详细信息，请参阅博客文章 [进行声明身份验证时根证书链不受信任错误](http://blogs.technet.com/b/speschka/archive/2010/02/13/root-of-certificate-chain-not-trusted-error-with-claims-authentication.aspx)。（滚动浏览关于从 Active Directory 联合身份验证服务 (AD FS) 导出证书的部分，假定您已通过某些其他方式为高信任外接程序创建证书；例如，通过使用证书颁发机构颁发的商业证书、自签名证书或域颁发的证书。）
  
    
    

## Web 应用程序需要知道这是令牌颁发者
<a name="AppIsTokenIssuer"> </a>

SharePoint 外接程序的远程 Web 应用程序组件已在 IIS 中绑定到其证书。这对于证书的传统用途已足够：安全识别 Web 应用程序和编码 HTTP 请求与响应。但是，在高信任 SharePoint 外接程序中，证书具有其他任务，即作为 Web 应用程序发送到 SharePoint 的访问令牌的正式"颁发者"。因此，Web 应用程序必须知道在 SharePoint 中将证书注册为令牌颁发者时所使用的证书的颁发者 ID。Web 应用程序从 web.config 文件的 **appSettings** 部分获取此 ID，其中有一个名为 **IssuerId** 的密钥。有关外接程序开发人员如何设置此值以及如何将证书绑定到 IIS 中的 Web 应用程序的说明，请参阅 [打包和发布高度可信的 SharePoint 外接程序](package-and-publish-high-trust-sharepoint-add-ins.md)。请注意，如果客户使用的策略是对每个高信任 SharePoint 外接程序使用单独的证书， **ClientId** 值还可用作 **IssuerId** 值。当多个外接程序共享同一个证书时，则不是这种情况，因为每个 SharePoint 外接程序 必须具有自己的唯一客户端 ID，但颁发者 ID 是 **SPTrustedSecurityTokenIssuer** 对象的标识符。
  
    
    
下面是高信任 SharePoint 外接程序的 **appSettings** 部分的一个示例。在此示例中，证书由多个外接程序共享，因此 **IssuerId** 与 **ClientId** 不同。请注意，高信任 SharePoint 外接程序中没有 **ClientSecret** 密钥。
  
    
    


```XML

<appSettings>
  <add key="ClientId" value="6569a7e8-3670-4669-91ae-ec6819ab461" />
  <add key="ClientSigningCertificatePath" value="C:\\MyCerts\\HighTrustCert.pfx" />
  <add key="ClientSigningCertificatePassword" value="3VeryComplexPa$$word82" />
  <add key="IssuerId" value="e9134021-0180-4b05-9e7e-0a9e5a524965" />
</appSettings>
```


> **安全注释**
> 前面的示例假定证书存储在文件系统上。这对于开发和调试是可接受的。在高信任的生产 SharePoint 外接程序中，证书通常存储在 Windows 证书存储中，并且 **ClientSigningCertificatePath** 和 **ClientSigningCertificatePassword** 键通常会替换为 **ClientSigningCertificateSerialNumber** 键。
  
    
    


## 高信任系统中的 IT 员工职责
<a name="ITPro"> </a>

如上所述，开发人员必须了解应用程序的安全要求，但是 IT 专业人员将实现为其提供支持所需的基础架构。IT 专业人员必须规划以下操作要求：
  
    
    

- 创建或购买一个或多个用于可信 SharePoint 外接程序的证书。
    
  
- 确保证书安全地存储在 Web 应用程序服务器上。使用 Windows 操作系统时，这意味着将证书存储在 Windows 证书存储中。
    
  
- 管理这些证书向构建 SharePoint 外接程序的开发人员的分发情况。
    
  
- 为使用该证书的外接程序和收到证书副本的开发人员跟踪每个证书的分发位置。如果必须撤销某个证书，IT 员工必须与每个开发人员合作，以安排到新证书的管理过渡。
    
  

## 其他资源
<a name="AR"> </a>


-  [创建 SharePoint 高信任外接程序](create-high-trust-sharepoint-add-ins.md)
    
  
-  [打包和发布高度可信的 SharePoint 外接程序](package-and-publish-high-trust-sharepoint-add-ins.md)
    
  
-  [有关 SharePoint 2013 中高信任外接程序的疑难解答提示](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)
    
  
-  [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  

