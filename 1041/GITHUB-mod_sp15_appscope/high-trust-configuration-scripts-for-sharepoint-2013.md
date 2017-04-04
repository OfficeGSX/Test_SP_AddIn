---
title: SharePoint 2013 の高信頼設定スクリプト
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# SharePoint 2013 の高信頼設定スクリプト
高信頼の SharePoint アドインを使用するために Microsoft SharePoint 2013 ファームを設定するカスタマイズ可能な Windows PowerShell スクリプトを取得します。
## スクリプトの使用方法
<a name="Usage"> </a>

以下のスクリプトは、Microsoft SharePoint 2013 のステージング ファームまたは運用ファームのアクセス トークンの信頼できる発行元として、1 つ以上の X.509 デジタル証明書を指定する場合に使用されます。(SharePoint アドイン開発環境に適したスクリプトについては、「 [SharePoint の高信頼性アドインを作成する](create-high-trust-sharepoint-add-ins.md)」を参照してください。) 証明書の取得と保存には非常に多くの方法があるため、すべての SharePoint ファームで機能するスクリプト セットというものは存在しません。このため、以下の点に注意してください。
  
    
    

- スクリプトは、ファーム内の任意の SharePoint サーバーの SharePoint 管理シェルで実行されることを意図しています。
    
  
- これらのスクリプトは、カスタマイズが必要な可能性があるドラフトとして考えてください。
    
  
- これらは信頼度の高い SharePoint アドインを発行するプロセス全体の一部として使用されます。トピック「 [高信頼認証システムを使用する SharePoint アドインを作成する](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)」、「 [高信頼 SharePoint アドインをパッケージ化して発行する](package-and-publish-high-trust-sharepoint-add-ins.md)」およびこれらに記載されている前提条件を理解しているユーザーのみが使用してください。
    
  
- また、アドインの顧客の証明書ポリシーを理解しているユーザーが、必要に応じて確認およびカスタマイズしてください。
    
  
- **HighTrustConfig-ForSharedCertificate.ps1** と **HighTrustConfig-ForSingleApp.ps1** の 2 つの主要なスクリプトは、管理者がアドインのリモート Web アプリケーション コンポーネントの証明書を取得しており、SharePoint サーバー上の次の IIS アプリケーション プールのユーザー アカウントが読み取り権限を持つフォルダーに、管理者が (秘密キーを含まない) .cer バージョンの証明書を一時的に保存していることを想定しています。
    
  - **SecurityTokenServiceApplicationPool**
    
  
  - テスト用の SharePoint Web サイトの親 SharePoint Web アプリケーションをホストする IIS Web サイトを提供するアドイン プール。 **SharePoint - 80** IIS Web サイトでは、プールは **OServerPortalAppPool** という名前です。
    
  

    アプリケーション プールが使用しているユーザー アカウントを検索するには、SharePoint サーバーで IIS マネージャーを開き、 [ **接続**] ペインの [ **アプリケーション プール**] をダブルクリックします。[ **アプリケーション プール**] リストの [ **ID**] 列が開き、アプリケーション プールのユーザーが表示されます。
    
  
- また、2 つの主要なスクリプトの手順は、リモート Web アプリケーションをホストするサーバーの IIS に 証明書がインストールされていることも想定しています。手順については、「 [証明書によるリモート Web サーバーの構成](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote)」を参照してください。
    
  
スクリプトごとの利用に関する特定の注意は、後続のセクションを参照してください。
  
    
    

## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

高信頼 SharePoint アドインのリモート Web アプリケーション コンポーネントの証明書は、商用の証明機関 (CA) またはドメインの CA のいずれかから取得します。どちらの場合でも、この証明書はルート CA (商用またはドメイン) を起点として、それぞれがその上位を信頼する証明書チェーンの最も低いリンクになります。SharePoint 2013 は、チェーンの *すべて*  の証明書が、SharePoint の信頼できるルート証明機関のリストに追加されていることを要求します。以下のスクリプトは、 *最も低いものを除き*  、チェーンの各証明書をリストに追加するために使用できます。チェーンの最も低い証明書はリモート Web アプリに直接バインドされる証明書ですが、後のセクションで説明する主要なスクリプトの 1 つにより、ルート証明機関に追加されます。
  
    
    
スクリプトのパラメーターは、次のとおりです。
  
    
    


|**パラメーター**|**値**|
|:-----|:-----|
|-CertPath  <br/> |証明書の完全なパスとファイル名 (.cer ファイル)。  <br/> |
|-CertName  <br/> |証明書の名前。名前を見つけるには、証明書のプロパティを表示します。  <br/> |
   
たとえば、Web アプリケーションの証明書が中間 (別名エンタープライズ) CA サーバーによって発行され、証明書が、ルート (別名スタンドアロン) CA サーバーによって発行される一般的なシナリオでは、スクリプトは、2 つの CA サーバーの証明書ごとに 1 回ずつ実行する必要があります。このことを以下に示します。
  
    
    



```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"

```

顧客の高信頼 SharePoint アドインがすべて同じ証明書チェーンからのものである標準的なケースの場合、このスクリプトをもう一度使用することはありません。
  
    
    
スクリプトのコードは以下のとおりです。テキスト エディターや PowerShell エディター (IPowerShell ISE) に貼り付けて、AddSPRootAuthority.ps1 として保存します。
  
    
    

> [!メモ]
> ファイルは UTF-8 ではなく ANSI 形式で保存する必要があります。PowerShell で ANSI 以外の形式のファイルを実行すると、構文エラーが表示される場合があります。メモ帳および PowerShell エディターでは、既定で ANSI 形式で保存されます。その他のエディターを使用してファイルを保存するときは、必ず ANSI 形式で保存します。 
  
    
    




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

このスクリプトの主なタスクは、複数の高信頼 SharePoint アドインのリモート Web アプリケーション コンポーネントの共有証明書を、ルート証明機関および信頼できるアクセス トークン発行元の両方として SharePoint に登録することです。このスクリプトは、顧客がそれぞれの高信頼 SharePoint アドインと共に個別の証明書を使用している場合は使用されません。そのようなシナリオの場合は、以下の「 [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript)」を参照してください。すべてのアドインが同じ証明書を使用している場合、このスクリプトは 1 回のみ実行します。アドインのセットがほかのセットとは異なる証明書を使用している場合、このスクリプトはセットごとに 1 回実行します。
  
    
    
次の表に、スクリプトのパラメーターとスイッチを示します。スクリプトをカスタマイズする場合に、この表への変更が必要になることがあります。
  
    
    


|**パラメーター**|**値**|
|:-----|:-----|
|-CertPath (必須)  <br/> |共有される *.cer ファイルの完全なパス。  <br/> |
|-CertName (必須)  <br/> |共有される証明書の名前。 名前を探すには、リモート Web アプリケーションをホストするリモート Web サーバーの IIS を開きます。 _サーバー名_ノードを強調表示して、 [ **証明書**] をダブルクリックします。証明書が名前順で一覧表示されます。  <br/> |
|-TokenIssuerFriendlyName (オプション)  <br/> |トークン発行元の、最長 50 文字の一意でわかりやすい名前。これは、トークン発行元と共に問題をデバッグする際に役立ちます。名前は一意にする必要があります。GUID を含めれば一意性は保証されますが、GUID は 50 文字のうちの 32 文字までを使用するので、GUID を Base 64 文字列に変換することを考慮してください。そうすれば、22 文字にまで減らせます。このパラメーターを使用しないと、スクリプトは「High-Trust Add-ins  _<発行元 GUID の base64 バージョン>_」という名前を使用します。  <br/> |
   
このスクリプトの実行例を、次に示します。
  
    
    
 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`
  
    
    

> [!重要]
> トークン発行元としての証明書の登録は、即座には有効になりません。すべての SharePoint サーバーが新しいトークン発行元を認識するまでに 24 時間程かかることがあります。SharePoint ユーザーに影響を与えずに実行できる場合には、すべての SharePoint サーバーで iisreset を実行すると、発行元がすぐに認識されます。 
  
    
    

テキスト エディター、または PowerShell エディターで新しいファイルを作成して、テキスト ファイルに以下をコピーし、HighTrustConfig-ForSharedCertificate.ps1 として保存します。UTF-8 ではなく、ANSI 形式を使用します。
  
    
    



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


> [!ヒント]
>  `New-SPTrustedRootAuthority` 行が正常に実行された後でスクリプトがエラーをスローする場合は、オブジェクトを削除するまでスクリプトを再実行することはできません。次のコマンドレットを使用します。ここで、 `-Identity` パラメーターの値は、スクリプトの `-CertName` で使用した値と同じです。>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`> トークン発行元を何らかの理由で削除する必要がある場合は、次のコマンドレットを使用します。 >  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`>  `-TokenIssuerFriendlyName` パラメーターが使用されている場合は、 `-Identity` にそれと同じ値を使用します。 `-TokenIssuerFriendlyName` パラメーターが使用されていない場合、ランダム GUID が発行元の名前の一部になります。 `-Identity` に必要な値を取得するには、次のコマンドレットを実行します。次に、生成される TokenIssuers.txt ファイルを開き、名前が「High-Trust Add-ins _<発行元 GUID の base64 バージョン>_」である発行元を探します。 `-Identity` でこの名前全体を使用します。>  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`
  
    
    


## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

このスクリプトの主なタスクは、高信頼 SharePoint アドインのリモート Web アプリの証明書を、ルート証明機関と信頼できるアクセス トークン発行元の両方として SharePoint に登録することです。このスクリプトは、顧客が複数の SharePoint アドインに単一の証明書を使用しているときは使用されません。 このシナリオの詳細については、前述の「 [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript)」を参照してください。このスクリプトは、高信頼 SharePoint アドインのそれぞれで実行されます。
  
    
    
次の表に、スクリプトのパラメーターとスイッチについて説明します。スクリプトをカスタマイズする場合に、この表への変更が必要になることがあります。
  
    
    


|**パラメーター**|**値**|
|:-----|:-----|
|-CertPath (必須)  <br/> |共有される *.cer ファイルの完全なパス。  <br/> |
|-CertName (必須)  <br/> |共有される証明書の名前。 名前を探すには、リモート Web アプリをホストするリモート Web サーバーの IIS を開きます。 _サーバー名_ノードを強調表示して、 [ **証明書**] をダブルクリックします。証明書が名前順で一覧表示されます。  <br/> |
|-SPAppClientID (必須)  <br/> |SharePoint アドインを appregnew.aspx で登録したときに使用したクライアント ID (GUID)。これは、トークン発行元の発行元 ID として使用されます。スクリプトは、発行元 ID の要件である小文字が使用されていることを確認します。  <br/> |
|-TokenIssuerFriendlyName (オプション)  <br/> |トークン発行元の、最長 50 文字の一意でわかりやすい名前。これは、トークン発行元と共に問題をデバッグしている場合に役立ちます。名前は一意にする必要があります。SharePoint アドインの名前の使用を検討してください。このパラメーターを使用しない場合、スクリプトは名前に  `-SPAppClientID` の値を使用します。 <br/> |
   
スクリプトの呼び出し例を、次に示します。
  
    
    
 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`
  
    
    

> [!重要]
> トークン発行元としての証明書の登録は、即座には有効になりません。すべての SharePoint サーバーが新しいトークン発行者を認識するまでに、24 時間程かかることがあります。SharePoint ユーザーに影響を与えずに実行できる場合は、すべての SharePoint サーバーで iisreset を実行すると、発行者がすぐに認識されます。 
  
    
    

テキスト エディター、または PowerShell エディターで新しいファイルを作成して、テキスト ファイルに以下をコピーし、HighTrustConfig-ForSingleApp.ps1 として保存します。UTF-8 ではなく、ANSI 形式を使用します。
  
    
    



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


> [!ヒント]
> 前のセクションの終わりのヒントがここでも適用されますが、HighTrustConfig-ForSingleApp.ps1 で  `-TokenIssuerFriendlyName` パラメーターが使用されていない場合は、 `Remove-SPTrustedSecurityTokenIssuer` コマンドレットの `-Identity` パラメーターでクライアント ID を使用してください。
  
    
    


## サイト サブスクリプションに必要な変更
<a name="SiteSubscriptions"> </a>

サイト サブスクリプションはテナンシーと呼ばれることもありますが、SharePoint ファームのサイト コレクションのサブセットです。サイト サブスクリプションは、通常ホストされる SharePoint ファーム上に作成されます。高信頼 SharePoint アドイン の顧客が、サイト サブスクリプション用に設定されたファームにアドインをインストールしようとしている場合、使用しているのが 2 つの HighTrustConfig-*.ps1 スクリプトのいずれの場合でも、そのスクリプトを変更する必要があります。サイト サブスクリプションは、それぞれ独自の領域 ID を持っていますが、スクリプトの  `$realm = Get-SPAuthenticationRealm` 行は、常にファームの領域を返します。トークン発行元から発行されたアクセス トークンは、完全な発行元 ID の "@" 文字の後に指定される領域についてのみ SharePoint によって有効とみなされます。アドインをインストールする Web サイトの正しい領域を取得するには、スクリプトの `Get-SPAuthenticationRealm` 呼び出しで `-ServiceContext` パラメーターを使用する必要があります。そうすると、サービス コンテキスト オブジェクトが、ターゲット Web サイトの親サイト コレクションから作成されます。次に例を示しますが、 `$WebURL` は、「http://marketing.contoso.com/sites/northteam/july」など、SharePoint Web サイトの完全な URL を指定した文字列変数です。このコードにより、指定された Web サイトだけでなく、同じサイト サブスクリプション内のすべての Web サイトで高信頼アドインを実行できるようになります。
  
    
    

```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc
```

スクリプトの変更を完了するには、追加の必須パラメーター  `$WebURL` を、次のようにファイルの一番上にあるパラメーター リストに追加します。
  
    
    



```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)
```

新しいパラメーターに先行するパラメーターの後に、必ずコンマを追加してください。また、一番上の行の「Usage」の例を次のように拡張して、新しいパラメーターを考慮に入れてください。 `-WebURL <full URL where SP add-in will be installed>`
  
    
    
SharePoint アドインがすべてのサイト サブスクリプションで信頼されるようにするには、 `Get-SPFarm` コマンドレットを使用してファームの参照を取得し、 **SiteSubscriptions** プロパティを反復します。各 **SPSiteSubscription** オブジェクトを `Get-SPServiceContext` コマンドレットに `-SiteSubscription` パラメーターの値として渡します。次に例を示します。
  
    
    



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


