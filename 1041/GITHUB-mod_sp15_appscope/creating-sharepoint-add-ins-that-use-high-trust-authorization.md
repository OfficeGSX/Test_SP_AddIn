---
title: 高信頼認証システムを使用する SharePoint アドインを作成する
ms.prod: SHAREPOINT
ms.assetid: 135bf6be-b118-4d2c-8d40-27f5060b06f3
---


# 高信頼認証システムを使用する SharePoint アドインを作成する
SharePoint と SharePoint にアクセスするリモート コンポーネントとの間でデジタル証明書を使用して信頼を確立する、高信頼 SharePoint アドイン について説明します。
 **提供元:**
  
    
    
Steve Peschka、Microsoft Corporation
  
    
    
Siew Moi Khor、Microsoft Corporation
  
    
    


**高信頼 SharePoint アドインの作成に関するビデオをご覧ください。**

  
    
    

  
    
    
![ビデオ](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## 高信頼 SharePoint アドイン の概要
<a name="Overview"> </a>

高信頼アドインは、社内 SharePoint ファームにインストールされるプロバイダー ホスト型の SharePoint アドイン です。このアドインを Microsoft SharePoint Online にインストールすることも、Office ストア を通して販売することもできません。高信頼アドインはコンテキスト トークンではなく証明書を使用して信頼を確立します。
  
    
    

> [!メモ]
>  このトピックは SharePoint アドイン に使用する高信頼認証システムを理解するのに役立ちます。高信頼アドインの作成および配置に関する実用的な情報については、以下のトピックを参照してください。>  [SharePoint の高信頼性アドインを作成する](create-high-trust-sharepoint-add-ins.md)>  [高信頼 SharePoint アドインをパッケージ化して発行する](package-and-publish-high-trust-sharepoint-add-ins.md)
  
    
    

SharePoint 2013 では、セキュリティ トークン サービス (STS) によってサーバー間の認証用のアクセス トークンが提供されます。STS は、Exchange 2013、Lync 2013、および SharePoint アドイン などの他のアプリケーション サービスにアクセスするための一時的なアクセス トークンを有効にします。ファーム管理者は、Windows PowerShell コマンドレットと証明書を使用して、SharePoint と他のアプリケーションまたはアドインとの間の信頼を確立します。 `New-SPTrustedRootAuthority` コマンドレットを使用して、使用する各証明書が SharePoint 2013 によって信頼されるようにする必要があります。また、 `New-SPTrustedSecurityTokenIssuer` コマンドレットを使用して、各証明書をトークン発行者として SharePoint に登録する必要もあります。
  
    
    

> [!メモ]
> STS はユーザー認証用のサービスではありません。したがって、サーバーの全体管理の [ **認証プロバイダー**] セクションや SharePoint 2013 の [ユーザー選択] ウィンドウのユーザー サインイン ページに、STS は表示されません。 
  
    
    


## 2 種類のトークン発行者
<a name="TwoKindsOfIssuers"> </a>

高信頼 SharePoint アドイン のリモート Web アプリケーションはデジタル証明書にバインドされます。(詳細は前述の 2 つのリンク先トピックを参照。) 証明書は、リモート Web アプリケーションによって、このアプリケーションから SharePoint へのすべての要求に含めるアクセス トークンに署名するために使用されます。Web アプリケーションは、証明書の秘密鍵を使用してトークンに署名し、SharePoint は証明書の公開鍵を使用して検証します。証明書が発行するトークンを SharePoint で信頼する前に、証明書を信頼されたトークン発行者として登録する必要があります。トークン発行者には、特定の SharePoint アドイン 用のトークンのみを発行できる発行者と、複数の SharePoint アドイン 用のトークンを発行できる信頼ブローカーの 2 種類があります。
  
    
    
実際には、SharePoint ファーム管理者がどのタイプのトークン発行者を作成するかを決定し、  `New-SPTrustedSecurityTokenIssuer` コマンドレットに使用するスイッチとパラメーター値を指定します。信頼ブローカーであるトークン発行者を作成するには、コマンド ラインに `-IsTrustBroker` スイッチを追加し、 `-Name` パラメーターにはアドインのクライアント ID 以外の固有値を使用します。入力例は次のとおりです。
  
    
    



```

New-SPTrustedSecurityTokenIssuer -IsTrustBroker -RegisteredIssuerName "<full_token_issuer_name> " --other parameters omitted--
```

ブローカーではないトークン発行者を作成するには、 `-IsTrustBroker` スイッチを使用しません。もう 1 つ違いがあります。 `-RegisteredIssuerName` パラメーターの値は常に 2 つの GUID を「@」文字で区切った形 ( _GUID_@ _GUID_) を取ります。右側の GUID は常に SharePoint ファーム (またはサイト サブスクリプション) の認証領域の ID です。左側の GUID は常にトークン発行者に固有の ID です。この ID は、 *信頼ブローカー*  であるトークン発行者の作成時にはランダム GUID です。しかし、ブローカーではないトークン発行者の作成時には、固有の発行者 GUID は SharePoint アドイン のクライアント ID として使用されるものと同一の GUID でなければなりません。このパラメーターは発行者の名前を指定するだけでなく、証明書がトークンを発行できる唯一の対象の SharePoint アドイン を SharePoint に通知します。次に示すのは、入力例の一部です。
  
    
    



```
$fullIssuerIdentifier = "<client_ID_of_SP_app> " + "@" + "<realm_GUID> "
New-SPTrustedSecurityTokenIssuer -RegisteredIssuerName $fullIssuerIdentifier --other parameters omitted--
```

通常、 `New-SPTrustedSecurityTokenIssuer` コマンドレットは、高信頼アドイン用に SharePoint を構成する他のタスクを実行するスクリプト内で使用します。そのスクリプトおよび `New-SPTrustedSecurityTokenIssuer` コマンドレットの完全な例の詳細については、 [SharePoint 2013 の高信頼設定スクリプト](high-trust-configuration-scripts-for-sharepoint-2013.md)を参照してください。
  
    
    

## 高信頼 SharePoint アドインに 1 つの証明書を使用するかまたは多数の証明書を使用するかを決定する
<a name="Deciding"> </a>

SharePoint およびネットワーク管理者には、高信頼 SharePoint アドイン で使用される証明書を入手および管理する次の 2 つの基本的な方法があります。
  
    
    

- 複数の SharePoint アドイン に同一の証明書を使用する。
    
  
- SharePoint アドイン ごとに別々の証明書を使用する。
    
  
このセクションでは各方法の長所と短所を説明します。
  
    
    
複数の SharePoint アドイン で同じ証明書を使用することの利点は、信頼および管理する証明書の数が少なくて済むことです。この方法の欠点は、特定の SharePoint アドインで信頼を信頼をなくす必要がある場合、トークン発行者またはルート証明機関として証明書を削除するしか方法がないことです。証明書を削除すると、同じ証明書を使用している他のすべての SharePoint アドインでも信頼が失われ、それらすべてのアプリが機能しなくなります。
  
    
    
引き続き信頼できる SharePoint アドインが再び動作するようにするには、 *新しい証明書を使用し*  、 `-IsTrustBroker` フラグを含めて `New-SPTrustedSecurityTokenIssuer` オブジェクトを作成する必要があります。その後、新しい証明書を信頼できるアドインをホストする各サーバーに登録し、それらのアドインをそれぞれ新しい証明書にバインドします。
  
    
    
アドインごとに 1 つの証明書を使用することの利点は、特定のアドインとの信頼を破棄するのが簡単なことです。特定のアドインの証明書との信頼を破棄することが必要な場合でも、引き続き信頼できるアドインが使用する証明書は影響を受けません。この方法の欠点は、取得する証明書の数が多く、それらの各々を使用するように SharePoint を構成する必要があることです。この構成は、 [SharePoint 2013 の高信頼設定スクリプト](high-trust-configuration-scripts-for-sharepoint-2013.md)に示されている再利用可能なスクリプトを使用して行えます。
  
    
    

## 証明書は SharePoint のルート証明機関
<a name="RootAuthorities"> </a>

この記事の最初で簡単に説明したように、SharePoint ファーム管理者は高信頼アドインのリモート Web アプリケーション、SharePoint 内の信頼済みルート証明機関、および信頼されたトークン発行者の証明書を作成する必要があります。実際、Web アプリケーションの背後に証明書発行機関の階層がある場合、そのチェーン内のすべての証明書は SharePoint の信頼済みルート証明機関のリストに追加される必要があります。
  
    
    
 `New-SPTrustedRootAuthority` コマンドレットを呼び出すことにより、チェーン内の各証明書は SharePoint の信頼済みルート証明機関のリストに追加されます。たとえば、Web アプリケーションの証明書が LAN 上のエンタープライズ ドメイン証明機関によって発行されたドメイン証明書であり、今度はその証明書が LAN 上にあるスタンドアロンの最高レベルの証明機関によって発行されたものであるとします。この場合、最高レベル CA、中間 CA、および Web アプリケーションのすべての証明書を SharePoint の信頼済みルート証明機関のリストに追加する必要があります。次の Windows PowerShell コードを実行して、この処理を行うことができます。
  
    
    



```

$rootCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<path_to_top-level_CA's_cer_file>")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $rootCA

$intermediateCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_intermediate_CA's_cer_file")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $intermediateCA

$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_web_application's_cer_file") 
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $certificate 

```

ルート証明書と中間証明書は SharePoint ファームに一度だけ追加します。通常は、Web アプリケーションの証明書は、 `New-SPTrustedSecurityTokenIssuer` への呼出など、他の構成も実行する別のスクリプトで追加します。例については、 [SharePoint 2013 の高信頼設定スクリプト](high-trust-configuration-scripts-for-sharepoint-2013.md)を参照してください。
  
    
    
アドインを開発およびデバッグ中の場合など、Web アプリケーションの証明書が自己署名入り証明書であるときは、証明書チェーンがないので、Web アプリケーションの証明書のみをルート証明機関のリストに追加する必要があります。
  
    
    
詳細については、ブログ投稿「 [クレーム認証での証明書チェーンのルートが信頼されていないエラー](http://blogs.technet.com/b/speschka/archive/2010/02/13/root-of-certificate-chain-not-trusted-error-with-claims-authentication.aspx)」を参照してください。(Active Directory フェデレーション サービス (AD FS) からの証明書のエクスポートのセクションの次までスクロールしてください。証明機関によって発行された商用証明書、自己署名入り証明書、またはドメインで発行された証明書を使用するなど、他の何らかの方法で高信頼アドイン用の証明書を作成した場合を想定しています。)
  
    
    

## トークン発行者であることを Web アプリケーションが認識することが必要
<a name="AppIsTokenIssuer"> </a>

SharePoint アドイン のリモート Web アプリケーション構成要素は、IIS でそれ自身の証明書にバインドされます。証明書の従来の目的、つまり Web アプリケーションの識別および HTTP 要求と応答のエンコードを安全に実行するには、この方法で十分です。しかし、高信頼 SharePoint アドインでは、証明書に、Web アプリケーションから SharePoint に送信されるアクセス トークンの公式の「発行元」になるという追加のタスクがあります。この目的のために、Web アプリケーションは、トークン発行者として証明書を SharePoint に登録するときに使用された証明書の発行者 ID を認識する必要があります。Web アプリケーションはこの ID を web.config ファイルの **appSettings** セクションから取得します。そのセクションには **IssuerId** という名前のキーがあります。アドイン開発者がこの値を設定する方法、および IIS で証明書を Web アプリケーションにバインドする方法の手順については、「 [高信頼 SharePoint アドインをパッケージ化して発行する](package-and-publish-high-trust-sharepoint-add-ins.md)」で説明されています。お客様が高信頼 SharePoint アドインごとに別個の証明書を使用する戦略を採用している場合、 **ClientId** 値も **IssuerId** 値として使用されることに注意してください。複数のアドインで同じ証明書を共有する場合は、SharePoint アドイン ごとにそれぞれ固有のクライアント ID があるので上記に該当せず、発行者 ID は **SPTrustedSecurityTokenIssuer** オブジェクトの ID になります。
  
    
    
以下は、高信頼 SharePoint アドイン用の **appSettings** セクションの例です。この例では、1 つの証明書が複数のアドインによって共有されているため、 **IssuerId** は **ClientId** と同じではありません。高信頼 SharePoint アドイン に **ClientSecret** キーがないことに注意してください。
  
    
    



```XML

<appSettings>
  <add key="ClientId" value="6569a7e8-3670-4669-91ae-ec6819ab461" />
  <add key="ClientSigningCertificatePath" value="C:\\MyCerts\\HighTrustCert.pfx" />
  <add key="ClientSigningCertificatePassword" value="3VeryComplexPa$$word82" />
  <add key="IssuerId" value="e9134021-0180-4b05-9e7e-0a9e5a524965" />
</appSettings>

```


> [!セキュリティ メモ]
> 上の例では、証明書がファイル システムに保存されていることを想定しています。これは、開発とデバッグで許容されます。運用環境の高信頼 SharePoint アドイン では、証明書は通常 Windows 証明書ストアに保存され、 **ClientSigningCertificatePath** と **ClientSigningCertificatePassword** の鍵は、通常、 **ClientSigningCertificateSerialNumber** の鍵に置き換えられます。
  
    
    


## 高信頼システムにおける IT スタッフの責任
<a name="ITPro"> </a>

開発者はこれまでに説明したアプリケーション セキュリティの要件を理解している必要がありますが、IT 担当者はそれをサポートするために必要なインフラストラクチャを実装します。IT 担当者は以下の運用要件を計画する必要があります。
  
    
    

- 信頼済み SharePoint アドインに使用する 1 つ以上の証明書を作成または購入します。
    
  
- 証明書が Web アプリケーション サーバーに安全に保存されていることを確認します。Windows OS が使用されている場合、これは、Windows 証明書ストアに証明書が保存されていることを意味します。
    
  
- SharePoint アドイン をビルドしている開発者への証明書の配布を管理します。
    
  
- 証明書を使用するアドインと、コピーを受け取った開発者の両方について、各証明書がどこに配布されたかを追跡します。証明書を失効させる必要がある場合は、IT スタッフは各開発者と協力して、新しい証明書への移行を調整する必要があります。
    
  

## その他の技術情報
<a name="AR"> </a>


-  [SharePoint の高信頼性アドインを作成する](create-high-trust-sharepoint-add-ins.md)
    
  
-  [高信頼 SharePoint アドインをパッケージ化して発行する](package-and-publish-high-trust-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 での高信頼アドインのトラブルシューティングのヒント](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)
    
  
-  [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  

