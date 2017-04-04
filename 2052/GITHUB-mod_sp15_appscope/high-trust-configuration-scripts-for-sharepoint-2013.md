---
title: SharePoint 2013 的高信任配置脚本
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# SharePoint 2013 的高信任配置脚本
获取用于配置 Microsoft SharePoint 2013 以使用高信任 SharePoint 外接程序的 Windows PowerShell 脚本。
## 如何使用脚本
<a name="Usage"> </a>

下面的脚本用于将一个或多个 X.509 数字证书指定为暂存或生产 Microsoft SharePoint 2013 场中受信任的访问令牌颁发者。（有关更适合 SharePoint 外接程序 开发环境的脚本，请参阅 [创建 SharePoint 高信任外接程序](create-high-trust-sharepoint-add-ins.md)。）没有哪个脚本集可以用于所有 SharePoint 场，因为有很多种获取和存储证书的不同方法。因此，请注意以下事项：
  
    
    

- 脚本的目的是在 SharePoint Management Shell 中或在场中的任何 SharePoint 服务器上运行。
    
  
- 应将这些脚本视为可能需要自定义的草稿。
    
  
- 脚本是发布高信任 SharePoint 外接程序的整体过程的一部分。脚本应仅由熟悉 [创建使用高信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)和 [打包和发布高度可信的 SharePoint 外接程序](package-and-publish-high-trust-sharepoint-add-ins.md)这两个主题以及其中所列前提条件的用户使用。
    
  
- 脚本还应由熟悉外接程序客户的证书策略的用户根据需要进行审查和自定义。
    
  
- 下面的两个主要脚本 **HighTrustConfig-ForSharedCertificate.ps1** 和 **HighTrustConfig-ForSingleApp.ps1** 假定管理员已获取外接程序的远程 Web 应用程序组件的证书，且管理员已在 SharePoint 服务器上下列 IIS 应用程序池的用户帐户具有"读取"权限的文件夹中临时存储证书的 .cer 版本（不包含私人密钥）：
    
  - "SecurityTokenServiceApplicationPool"
    
  
  - 可服务于 IIS 网站的外接程序池，该网站托管用于测试 SharePoint 网站的父 SharePoint Web 应用程序。对于"SharePoint - 80"IIS 网站，该池称为"OServerPortalAppPool"。
    
  

    要查找应用程序池当前使用的用户帐户，请在 SharePoint 服务器上打开 IIS 管理器，并在"连接"窗格中双击"应用程序池"。"应用程序池"列表中的"标识"列显示了应用程序池的用户。
    
  
- 两个主要脚本的说明还假定证书已安装到承载远程 Web 应用程序的服务器上的 IIS。有关说明，请参阅 [使用证书配置远程 Web 服务器](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote)。
    
  
请参阅以下各节中每个脚本的特定使用说明。
  
    
    

## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

高信任 SharePoint 外接程序的远程 Web 应用程序组件的证书从商业证书颁发机构 (CA) 或域 CA 获取。在两种情况中的任何一种情况下，证书是证书链中的最低链接，每个证书信任其上方的证书，使用根 CA（商业或域）发出。SharePoint 2013 要求将证书链中的 *所有*  证书添加到 SharePoint 的受信任根颁发机构列表中。下面的脚本可用于将证书链中 *除最低的证书*  以外的所有证书添加到列表中。证书链中的最低证书是直接绑定到远程 Web 应用程序的证书，已添加到后面章节中所述的主要脚本之一的根颁发机构。
  
    
    
脚本的参数如下所示：
  
    
    


|**参数**|**值**|
|:-----|:-----|
|-CertPath  <br/> |证书的完整路径和文件名（.cer 文件）。  <br/> |
|-CertName  <br/> |证书的名称。要查找名称，请查看证书的属性。  <br/> |
   
例如，在 Web 应用程序的证书由中间（也称为"企业"）CA 服务器颁发，其证书反过来由根（也称为"独立"）CA 服务器颁发的常用方案中，脚本应针对两个 CA 服务器的每个证书运行一次。下面演示了这一过程：
  
    
    



```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"

```

如果客户的高信任 SharePoint 外接程序 的所有证书来自同一证书链（这是典型的情况），此脚本将不会再使用。
  
    
    
下面是脚本的代码。只需将其粘贴到任何文本编辑器或 PowerShell 编辑器 (IPowerShell ISE) 并将其另存为 AddSPRootAuthority.ps1 即可。
  
    
    

> [!注释]
> 文件必须另存为 ANSI 格式，而非 UTF-8。PowerShell 在运行非 ANSI 格式的文件时，可能会报告语法错误。记事本和 PowerShell 编辑器会默认将其保存为 ANSI。如果使用其他任何编辑器保存文件，请确保将其保存为 ANSI。 
  
    
    




```

param(
    [Parameter(Mandatory)][String] $CertName = $(throw "Usage: AddSPRootAuthority.ps1 -CertPath <full path to .cer file> -CertName <name of certificate>"),
    [Parameter(Mandatory)][String] $CertPath
)
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Make the certificate a trusted root authority in SharePoint
$cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)
New-SPTrustedRootAuthority -Name $CertName -Certificate $cert

```


## HighTrustConfig-ForSharedCertificate.ps1
<a name="MultipleAppScript"> </a>

此脚本的主要任务是在 SharePoint 同时作为根颁发机构和受信任访问令牌颁发者的多个高信任 SharePoint 外接程序中，注册远程 Web 应用程序的共享证书。如果客户为每个高信任 SharePoint 外接程序使用单独的证书，则不使用此脚本。有关此方案，请参阅下面的  [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript)。如果所有外接程序使用同一个证书，此脚本将仅运行一次。如果某些外接程序集使用与其他集不同的证书，此脚本将为每个集运行一次。
  
    
    
下表介绍了脚本的参数和开关。对脚本进行自定义可能需要更改此表。
  
    
    


|**参数**|**值**|
|:-----|:-----|
|-CertPath（必需）  <br/> |共享 *.cer 文件的完整路径。  <br/> |
|-CertName（必需）  <br/> |共享证书的名称。要查找名称，在承载远程 Web 应用程序的远程 Web 服务器上打开 IIS。突出显示  _服务器名称_ 节点并双击"证书"。证书将按其名称列出。 <br/> |
|-TokenIssuerFriendlyName（可选）  <br/> |令牌颁发者的唯一友好名称，最多 50 个字符。如果您正在调试令牌颁发者的问题，这可能很有帮助。名称必须唯一。将 GUID 包括在内可以确保名称唯一，但 GUID 将占用 32 个字符（总共 50 个字符），因此请考虑将 GUID 转换为 Base 64 字符串，这将减少到 22 个字符。如果未使用此参数，脚本将使用名称"High-Trust Add-ins  _<颁发者 GUID 的 base64 版本>_"。  <br/> |
   
下面是运行此脚本的一个示例。
  
    
    
 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`
  
    
    

> [!重要信息]
> 将证书注册为令牌颁发者不会立即生效。可能需要 24 小时，才能使所有 SharePoint 服务器识别新的令牌颁发者。在所有 SharePoint 服务器上运行 iisreset 会使它们立即识别颁发者，前提是您可以在不影响 SharePoint 用户的情况下执行此操作。 
  
    
    

在文本编辑器或 PowerShell 编辑器中启动一个新文件，将以下内容复制到文本文件中，并将其另存为 HighTrustConfig-ForSharedCertificate.ps1。使用 ANSI 格式，而非 UTF-8。
  
    
    



```

param(
    [Parameter(Mandatory)][String] $CertPath = $(throw "Usage: HighTrustConfig-ForSharedCertificate.ps1 -CertPath <full path to .cer file> -CertName <name of certificate> [-TokenIssuerFriendlyName <friendly name>]"),
    [Parameter(Mandatory)][String] $CertName,
    [Parameter()][String] $TokenIssuerFriendlyName
) 
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Ensure friendly name is short enough
if ($TokenIssuerFriendlyName.Length -gt 50)
{
    throw "-TokenIssuerFriendlyName must be unique name of no more than 50 characters."
} 

# Get the certificate
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)

# Make the certificate a trusted root authority in SharePoint
New-SPTrustedRootAuthority -Name $CertName -Certificate $certificate 

# Get the GUID of the authentication realm
$realm = Get-SPAuthenticationRealm

# Generate a unique specific issuer ID
$specificIssuerId = [System.Guid]::NewGuid().ToString()

# Create full issuer ID in the required format
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

# Create issuer name
if ($TokenIssuerFriendlyName.Length -ne 0)
{
    $tokenIssuerName = $TokenIssuerFriendlyName
}
else
{
    $tokenIssuerName = "High-Trust Add-ins " + [System.Convert]::ToBase64String($specificIssuerId.ToByteArray()).TrimEnd('=') 
}

# Register the token issuer
New-SPTrustedSecurityTokenIssuer -Name $tokenIssuerName -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier -IsTrustBroker

# Output the specific issuer ID to a file in the same folder as this script. The file should be given to the developer of the high-trust SharePoint Add-in.
$specificIssuerId | select * | Out-File -FilePath "SecureTokenIssuerID.txt"
```


> [!提示]
> 如果脚本在  `New-SPTrustedRootAuthority` 行运行成功后抛出错误，则脚本无法重新运行，除非对象已删除。使用以下 cmdlet，其中 `-Identity` 参数的值与用于脚本中的 `-CertName` 参数的值相同。>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`> 如果因为某种原因需要移除令牌颁发者，请使用以下 cmdlet： >  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`> 如果使用了  `-TokenIssuerFriendlyName` 参数，请为 `-Identity` 使用相同的值。如果未使用 `-TokenIssuerFriendlyName` 参数，则随机 GUID 是颁发者名称的一部分。要获取 `-Identity` 所需的值，请运行以下 cmdlet。然后打开生成的 TokenIssuers.txt 文件并查找名称为"High-Trust Add-ins _<颁发者 GUID 的 base64 版本>_"的颁发者。为  `-Identity` 使用该完整名称。>  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`
  
    
    


## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

此脚本的主要任务是在 SharePoint 同时作为根颁发机构和受信任访问令牌颁发者的高信任 SharePoint 外接程序中，注册远程 Web 应用程序的证书。如果用户对多个 SharePoint 外接程序使用单个证书，则不使用此脚本。有关此方案，请参阅上面的  [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript)。将为每个高信任 SharePoint 外接程序运行此脚本。
  
    
    
下表介绍了脚本的参数和开关。对脚本进行自定义可能需要更改此表。
  
    
    


|**参数**|**值**|
|:-----|:-----|
|-CertPath（必需）  <br/> |共享 *.cer 文件的完整路径。  <br/> |
|-CertName（必需）  <br/> |共享证书的名称。要查找名称，在承载远程 Web 应用程序的远程 Web 服务器上打开 IIS。突出显示  _服务器名称_ 节点并双击"证书"。证书将按其名称列出。 <br/> |
|-SPAppClientID（必需）  <br/> |在 appregnew.aspx 上注册 SharePoint 外接程序时使用的客户端 ID (GUID)。此 ID 用作令牌颁发者的颁发者 ID。脚本确保其为小写，这是对颁发者 ID 的要求。  <br/> |
|-TokenIssuerFriendlyName（可选）  <br/> |令牌颁发者的唯一友好名称，最多 50 个字符。如果您正在调试令牌颁发者的问题，这可能很有帮助。名称必须唯一。考虑使用 SharePoint 外接程序的名称。如果未使用此参数，脚本将使用  `-SPAppClientID` 的值作为名称。 <br/> |
   
下面是调用此脚本的一个示例：
  
    
    
 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`
  
    
    

> [!重要信息]
> 将证书注册为令牌颁发者不会立即生效。可能需要 24 小时，才能使所有 SharePoint 服务器识别新的令牌颁发者。在所有 SharePoint 服务器上运行 iisreset 会使它们立即识别颁发者，前提是您可以在不影响 SharePoint 用户的情况下执行此操作。 
  
    
    

在文本编辑器或 PowerShell 编辑器中启动一个新文件，将以下内容复制到文本文件中，并将其另存为 HighTrustConfig-ForSingleApp.ps1。使用 ANSI 格式，而非 UTF-8。
  
    
    



```

param(
    [Parameter(Mandatory)][String] $CertPath = $(throw "Usage: HighTrustConfig-ForSingleApp.ps1 -CertPath <full path to .cer file> -CertName <name of certificate> [-SPAppClientID <client ID of SharePoint add-in>] [-TokenIssuerFriendlyName <friendly name>]"),
    [Parameter(Mandatory)][String] $CertName,
    [Parameter(Mandatory)][String] $SPAppClientID,
    [Parameter()][String] $TokenIssuerFriendlyName
) 
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Ensure friendly name is short enough
if ($TokenIssuerFriendlyName.Length -gt 50)
{
    throw "-TokenIssuerFriendlyName must be unique name of no more than 50 characters."
} 

# Get the certificate
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)

# Make the certificate a trusted root authority in SharePoint
New-SPTrustedRootAuthority -Name $CertName -Certificate $certificate 

# Get the GUID of the authentication realm
$realm = Get-SPAuthenticationRealm

# Must use the client ID as the specific issuer ID. Must be lower-case!
$specificIssuerId = New-Object System.String($SPAppClientID).ToLower()

# Create full issuer ID in the required format
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

# Create issuer name
if ($TokenIssuerFriendlyName.Length -ne 0)
{
    $tokenIssuerName = $TokenIssuerFriendlyName
}
else
{
    $tokenIssuerName = $specificIssuerId 
}

# Register the token issuer
New-SPTrustedSecurityTokenIssuer -Name $tokenIssuerName -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier

```


> [!提示]
> 上一节结尾的提示在此处同样适用，但对于  `Remove-SPTrustedSecurityTokenIssuer` cmdlet 的 `-Identity` 参数，请使用客户端 ID，前提是未在 HighTrustConfig-ForSingleApp.ps1 中使用 `-TokenIssuerFriendlyName` 参数。
  
    
    


## 站点订阅所需的修改
<a name="SiteSubscriptions"> </a>

站点订阅有时称为租户，它是指 SharePoint 服务器场上的网站集的子集。网站订阅通常在托管的 SharePoint 服务器场上创建。如果高信任 SharePoint 外接程序的客户希望在为站点订阅配置的服务器场上安装外接程序，则不论使用两个 HighTrustConfig-*.ps1 脚本中的哪一个，都需对其进行修改。每个站点订阅都有自己的领域 ID，但脚本中的行  `$realm = Get-SPAuthenticationRealm` 始终返回服务器场的领域。仅对完整颁发者 ID 中"@"字符后面指定的领域，SharePoint 才会将令牌颁发者颁发的访问令牌视为有效。要获取将安装外接程序的网站的正确领域，脚本在调用 `Get-SPAuthenticationRealm` 时必须使用 `-ServiceContext` 参数。反过来，服务上下文对象从目标网站的父网站集创建。下面是一个示例，其中 `$WebURL` 是具有 SharePoint 网站的完整 URL 的字符串变量，例如"http://marketing.contoso.com/sites/northteam/july"。此代码使高信任外接程序不仅可以在指定网站上运行，还可以在同一站点订阅中的每个网站上运行。
  
    
    

```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc
```

要完成脚本修改，请将另一个必需参数  `$WebURL` 添加到文件开头的参数列表中，如下所示：
  
    
    



```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)
```

在新参数前的参数后面务必添加一个逗号。扩展顶行的"使用"示例以考虑新参数，如下所示： `-WebURL <full URL where SP add-in will be installed>`。
  
    
    
要将 SharePoint 外接程序在每个站点订阅上设为可信任，请使用  `Get-SPFarm` cmdlet 获取对服务器场的引用，然后遍历其 **SiteSubscriptions** 属性。将每个 **SPSiteSubscription** 对象作为 `-SiteSubscription` 参数的值传递到 `Get-SPServiceContext` cmdlet。示例如下。
  
    
    



```

$Farm = Get-SPFarm
foreach ($ss in $Farm.SiteSubscriptions)
{
    $sc = Get-SPServiceContext -SiteSubscription $ss
    $realm = Get-SPAuthenticationRealm -ServiceContext $sc

    # All of the lines from the draft script below the call to 
    # Get-SPAuthenticationRealm are inserted here inside the loop.
}
# end of script
```


