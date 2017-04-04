---
title: SharePoint 2013 高信任層級設定指令碼
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# SharePoint 2013 高信任層級設定指令碼
取得Microsoft SharePoint 2013伺服器陣列設定為使用高信任SharePoint Add-in的可自訂Windows PowerShell指令碼。
## 如何使用指令碼
<a name="Usage"> </a>

下列指令碼可用來指定為信任的發行者的存取 token 臨時或實際執行Microsoft SharePoint 2013伺服器陣列中的一或多個 X.509 數位憑證。(如更多適用於SharePoint Add-ins開發環境的指令碼，請參閱 [建立高信任 SharePoint 增益集](create-high-trust-sharepoint-add-ins.md))。因為有太多個方法可以取得憑證並儲存指令碼沒有單一一組可運作的每個 SharePoint 伺服器陣列。該原因，請注意下列：
  
    
    

- 指令碼原本用意是要SharePoint Management Shell中任何伺服器上執行 SharePoint 伺服器陣列中。
    
  
- 這些指令碼應視為可能需要自訂的草稿。
    
  
- 使用的發佈高信任SharePoint Add-in整體程序的一部分。他們只應由某人熟悉主題 [建立 SharePoint 增益集使用高信任授權](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)和 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)以及它們所列的先決條件。
    
  
- 他們應該也可以檢閱和自訂視所某人熟悉增益集客戶憑證原則。
    
  
- 兩個主要指令碼下， **HighTrustConfig ForSharedCertificate.ps1** 和 **HighTrustConfig ForSingleApp.ps1** ，假設管理員已取得遠端 web 應用程式元件的增益集的憑證或增益集和的系統管理員暫時已儲存的憑證 (不包含私密金鑰) 的.cer 版本的使用者帳戶下的 IIS 應用程式集區的 SharePoint 伺服器的資料夾中具有讀取權限：
    
  - **SecurityTokenServiceApplicationPool**
    
  
  - 增益集區中做為您的測試 SharePoint 網站的 SharePoint web 應用程式主控父項的 IIS 網站。 **SharePoint - 80** IIS 網站的集區會呼叫 **OServerPortalAppPool**。
    
  

    若要找出要使用的應用程式集區的使用者帳戶的 SharePoint 伺服器上開啟 IIS 管理員並按兩下 [ **連線**] 窗格中的 [ **應用程式集區**。會開啟 [ **應用程式集區**] 清單中的 [ **身分識別**] 欄顯示應用程式集區的使用者。
    
  
- 兩個主要的指令碼的指示也會假設憑證已裝載遠端 web 應用程式的伺服器上安裝到 IIS。指示，請參閱 [設定遠端網頁伺服器的憑證](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote)。
    
  
請參閱下列各節中的每個指令碼的特定流量附註。
  
    
    

## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

從商業憑證授權單位 (CA) 或網域的 CA 取得之憑證的高信任SharePoint Add-in的遠端 web 應用程式元件。不論執行哪項則憑證鏈結中的憑證，上方，每個信任的根 CA (商業或網域) 的來源最低的連結。SharePoint 2013需要的 *所有*  憑證鏈結中的加入至 SharePoint 的清單中的信任的根授權單位。下列指令碼可用來將每個憑證鏈結 *最低除了*  中新增至清單。直接繫結至的遠端 web 應用程式的憑證鏈結中的最低憑證新增至其中一個主要更新版本的各節所述的指令碼的根授權。
  
    
    
指令碼的參數如下所示：
  
    
    


|**參數**|**值**|
|:-----|:-----|
|-CertPath <br/> |完整路徑和檔名的憑證 (.cer 檔案)。 <br/> |
|-CertName <br/> |憑證的名稱。若要尋找名稱，檢視憑證的內容。 <br/> |
   
例如，常見的案例所在的 web 應用程式的憑證由中級 (也稱為 「 enterprise') CA 伺服器發行和其憑證，接著，由發行 (也稱為 「 獨立 」) 的根 CA 伺服器，指令碼應該在執行一次每兩個 CA 伺服器的憑證。下列範例顯示這:
  
    
    



```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"

```

如果所有憑證的客戶的高信任SharePoint Add-ins是來自相同的鏈結的憑證，通常是案例如下，然後此指令碼會永遠不使用一次。
  
    
    
以下是此指令碼的程式碼。只要將它貼到任何文字編輯器或 PowerShell 編輯器 (IPowerShell ISE) 並將其儲存為 AddSPRootAuthority.ps1。
  
    
    

> **注意事項**
> 檔案必須儲存為 ANSI 格式，不 utf-8。執行非 ANSI 格式的檔案時 PowerShell 可能會提供下列的語法錯誤。「 記事本 」 和 「 PowerShell 編輯器會使用預設儲存為 ANSI。如果您使用任何其他編輯器來儲存檔案，請確定您已將它儲存為 ANSI。
  
    
    




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

若要在多個高信任SharePoint Add-ins與 SharePoint 的根授權單位做以及信任的存取 token 簽署者登錄的遠端 web 應用程式元件共用的憑證是此指令碼的主要工作。如果客戶每個高信任SharePoint Add-in搭配使用不同的憑證未使用此指令碼。該案例中，請參閱 ＜  [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript)下。如果所有的增益集使用相同的憑證，然後此指令碼是只執行一次。如果增益集的一些設定是使用來自其他組不同的憑證，然後此指令碼會執行一次的每一組。
  
    
    
下表說明的參數和指令碼的切換參數。自訂指令碼可能會需要此表格的變更。
  
    
    


|**參數**|**值**|
|:-----|:-----|
|-CertPath (必要) <br/> |共用 *.cer 檔案的完整路徑。 <br/> |
|-CertName (必要) <br/> |共用憑證的名稱。若要尋找名稱，主控的遠端 web 應用程式的遠端 web 伺服器上開啟 [IIS]。反白顯示 _server name_節點並連按兩下 [ **憑證**。憑證會列出依其名稱。 <br/> |
|-TokenIssuerFriendlyName (選用) <br/> |最多 50 個字元的 token 簽署者唯一易記名稱。這可以是如果您使用 token 發行者偵錯問題很有幫助。名稱必須是唯一的。加上 GUID 會確保，但 GUID 使用 32 50 個字元的設定、 考慮轉換為 Base 64 字串，可降低為 22 個字元的 GUID。如果未使用此參數，指令碼會使用名稱"高信任層級增益集 _<base64 version of issuer GUID>_"。 <br/> |
   
以下是執行此指令碼的範例。
  
    
    
 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`
  
    
    

> **重要**
> 註冊為權杖發行者的憑證不是有效立即。可能需要只要之前的所有 SharePoint 伺服器都辨識新的權杖發行者的 24 小時。所有 SharePoint 伺服器上執行 iisreset 將會導致其立即辨識發行者，如果您可以執行的動作不干擾 SharePoint 使用者。
  
    
    

在文字編輯器或 PowerShell 編輯程式中啟動新的檔案並複製到文字檔的下列並將其儲存為 HighTrustConfig ForSharedCertificate.ps1。使用 ANSI 格式，不 utf-8。
  
    
    



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


> **秘訣**
> 如果 `New-SPTrustedRootAuthority`列有執行成功後指令碼會擲回錯誤，指令碼就不能重新之前已移除該物件的執行。使用下列 cmdlet，其中 `-Identity`參數的值相同是用於在指令碼的 `-CertName`參數。>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`> 如果 token 簽署者需要有任何原因移除，請使用下列 cmdlet：>  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`> 如果已使用 `-TokenIssuerFriendlyName`參數，然後使用相同的值的 `-Identity`。如果之前未使用 `-TokenIssuerFriendlyName`參數，然後一個隨機 GUID 屬於簽發者名稱。若要取得 `-Identity`所需的值，請執行下列 cmdlet。然後開啟會產生 TokenIssuers.txt 檔案並找出其名稱為"高信任層級增益集 _<base64 version of issuer GUID>_"的發行者。使用 `-Identity`該整個名稱。>  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`
  
    
    


## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

此指令碼的主要工作是在高信任SharePoint Add-in與 SharePoint 的根授權單位做以及信任的存取 token 簽署者中登錄的遠端 web 應用程式的憑證。如果客戶使用單一憑證的多個SharePoint Add-ins未使用此指令碼。該案例中，請參閱 ＜  [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript)上面。此指令碼執行的每個高信任SharePoint Add-in。
  
    
    
下表說明的參數和指令碼的切換參數。自訂指令碼可能會需要此表格的變更。
  
    
    


|**參數**|**值**|
|:-----|:-----|
|-CertPath (必要) <br/> |共用 *.cer 檔案的完整路徑。 <br/> |
|-CertName (必要) <br/> |共用憑證的名稱。若要尋找名稱，主控的遠端 web 應用程式的遠端 web 伺服器上開啟 [IIS]。反白顯示 _server name_節點並連按兩下 [ **憑證**。憑證會列出依其名稱。 <br/> |
|-SPAppClientID (必要) <br/> |時SharePoint Add-in已註冊 appregnew.aspx 上使用用戶端識別碼 (GUID)。這會做為簽發者 ID 的 token 簽署者。指令碼會確保它是小寫，這是必要項目簽發者識別碼。 <br/> |
|-TokenIssuerFriendlyName (選用) <br/> |最多 50 個字元的 token 簽署者唯一易記名稱。這可以是如果您使用 token 發行者偵錯問題很有幫助。名稱必須是唯一的。請考慮使用SharePoint Add-in的名稱。如果未使用此參數，指令碼會使用 `-SPAppClientID`的值做為名稱。 <br/> |
   
以下是範例呼叫指令碼：
  
    
    
 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`
  
    
    

> **重要**
> 註冊為權杖發行者的憑證不是有效立即。可能需要只要之前的所有 SharePoint 伺服器都辨識新的權杖發行者的 24 小時。所有 SharePoint 伺服器上執行 iisreset 將會導致其立即辨識發行者，如果您可以執行的動作不干擾 SharePoint 使用者。
  
    
    

在文字編輯器或 PowerShell 編輯程式中啟動新的檔案並複製到文字檔的下列並將其儲存為 HighTrustConfig ForSingleApp.ps1。使用 ANSI 格式，不 utf-8。
  
    
    



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


> **秘訣**
> 上一節結尾處的秘訣，以及在此處套用但 `-Identity`參數 `Remove-SPTrustedSecurityTokenIssuer`指令程式，請使用的用戶端識別碼如果 `-TokenIssuerFriendlyName`參數不會搭配 HighTrustConfig ForSingleApp.ps1。
  
    
    


## 修改所需的網站訂閱
<a name="SiteSubscriptions"> </a>

將網站訂閱，有時稱為租用中，是 SharePoint 伺服器陣列上的網站集合的子集。在主控的 SharePoint 伺服器陣列通常會建立網站訂閱。如果高信任SharePoint Add-in客戶想要在已設定網站訂閱的伺服器陣列上安裝的增益集，然後準的兩個 HighTrustConfig *.ps1 指令碼使用需要進行修改。每個網站訂閱有其專屬的領域識別碼，但指令碼中列 `$realm = Get-SPAuthenticationRealm`一定會傳回伺服器陣列的領域。Token 簽署者所發出的存取 token 僅限視為是有效 SharePoint 所指定後之領域的"@"字元的完整簽發者識別碼。若要取得的網站所在的增益集將要安裝正確的領域，指令碼有 `Get-SPAuthenticationRealm`的呼叫中使用 `-ServiceContext`參數。服務快顯、 依次建立物件的父網站集合中的目標網站。以下為範例，  `$WebURL`所在的 SharePoint 網站，例如"http://marketing.contoso.com/sites/northteam/july。 」 的完整 url 的字串變數這段程式碼可讓高信任層級增益集執行，而不只是在網站的指定，但在同一個網站訂閱內每個網站上。
  
    
    

```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc
```

若要完成的指令碼的修改，新增額外的必要的參數 `$WebURL`至參數清單頂端的檔案並將類似：
  
    
    



```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)
```

請務必反之新參數後新增逗號。展開 [事項與類似如下的新參數頂部線條上"使用"範例：  `-WebURL <full URL where SP add-in will be installed>`。
  
    
    
若要讓信任的每個網站訂閱在SharePoint Add-in ，取得 `Get-SPFarm`指令程式與伺服器陣列的參考，並再逐一查看其 **SiteSubscriptions**屬性。每個 **SPSiteSubscription**將物件傳遞至 `Get-SPServiceContext`指令程式為 `-SiteSubscription`參數的值。以下是範例。
  
    
    



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


