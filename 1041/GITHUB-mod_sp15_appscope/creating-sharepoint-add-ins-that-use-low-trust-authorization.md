---
title: 低信頼承認を使用する SharePoint アドインの作成
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# 低信頼承認を使用する SharePoint アドインの作成
SharePoint アドイン の低信頼承認システムについて説明します。
SharePoint アドイン のリモート コンポーネント (または外部アプリケーション) は、各 HTTP 要求を使用してアクセス トークンを SharePoint に渡すことにより、SharePoint リソースへの承認を取得します。リモート コンポーネントは顧客の Office 365 テナンシーに関連付けられた Microsoft Azure アクセス制御サービス (ACS) アカウントからアクセス トークンを取得します。Azure ACS は、リソース サーバーとしての SharePoint、クライアントとしてのリモート コンポーネントと共に、フローと呼ばれる  [OAuth 2.0](http://oauth.net/) トランザクションの承認サーバーとして機能します。関連するプロトコルの仕様については、「 [Web 承認プロトコル (oauth)](http://datatracker.ietf.org/doc/active/#oauth)」を参照してください。 
  
    
    

低信頼承認システムを使用するプロバイダー向けのホスト型 SharePoint アドイン は、Office ストア で販売し、顧客の Office 365 テナンシーを使用して Azure ACS との信頼を確立するよう構成された、Microsoft SharePoint Online またはオンプレミス SharePoint ファームのいずれかにインストールすることができます。低信頼システムを使用する SharePoint アドイン をインストールするには、顧客が Office 365 テナンシーを持っている必要があります。ただし、顧客は他の目的でこのテナンシーを使用する必要はありません。オンプレミスのファームを Office 365 テナンシーにリンクする手順については、「 [Office 365 SharePoint サイトを使用してオンプレミスの SharePoint サイトのプロバイダー向けのホスト型アドインに権限を付与する](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)」を参照してください。
## Azure ACS への登録
<a name="Registration"> </a>

低信頼承認システムを使用するには、SharePoint アドイン を最初に Azure ACS に登録してから、SharePoint ファームまたは SharePoint Online テナンシーのアプリ管理サービスに登録する必要があります (当初 SharePoint アドイン は「SharePoint 用アプリ」と呼ばれていたため「アプリ管理サービス」と呼ばれます)。Office ストア を介して販売するアドインの場合、ACS への登録は 販売者ダッシュボード で実行し、サービスへの登録は、アドインがインストールされるときに実行されます。組織のアドイン カタログで配布するアドインの場合、ACS とサービスの両方への登録は、アドインがインストールされる SharePoint テナンシーまたはファームの \\_Layouts\\15\\AppRegNew.aspx ページで実行します。SharePoint にアクセスする外部の非 SharePoint アプリケーションも登録する必要があります。このカテゴリには、Office アドイン、Windows ストア アプリ、Web アプリケーション、およびスマートフォン アプリなどのデバイス アプリが含まれます。
  
    
    

> **メモ**
> 登録するには、アプリケーションがインターネット ドメインを持っている必要があります。任意の既存のドメインをこの目的に使用できますが、自分で所有していないドメインが今後もずっと存在するとは言い切れません。このため、ネイティブ デバイス アプリケーションの一部に Web アプリケーションを含める必要があります。この場合、その Web アプリケーション コンポーネントは、登録を有効にする以外の役割を持っていなくても構いません。この方法で設計された詳細なコード サンプルについては、「 [アドイン モデルを使用したバッチでのサイトのプロビジョニング](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6)」を参照してください。 
  
    
    

登録の詳細については、「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」を参照してください。
  
    
    

### アドイン シークレットの有効期限

アドイン シークレットは、12 か月ごとに交換する必要があります。詳細については、「 [SharePoint アドインで期限が切れたクライアント シークレットを置換する方法](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)」を参照してください。
  
    
    

## 承認ポリシー
<a name="Policies"> </a>

SharePoint アドインは、2 つの承認ポリシーのいずれかを使用するように設計できます。
  
    
    

- **ユーザー + アプリ ポリシー:** このポリシーを使用するアドインは、アドインとユーザーの両方がアクセス許可を持っている操作のみを実行できます。これは、開発者が他のポリシーを使用する特定の手順を踏まない限り使用される、既定のポリシーです。
    
  
- **アプリ用ポリシー:** このポリシーを使用するアドインは、ユーザーがアクセス許可を持たない操作でも、アドインがアクセス許可を持っていればその操作を実行できます。開発者は、アドインのアドイン マニフェストでこのポリシーの使用を要求する必要があります。この要求は、アドインをインストールするユーザーによって承認される必要があります。このポリシーは、プロバイダー向けのホスト型 SharePoint アドインでのみ使用できます。
    
  
承認ポリシーの詳細については、「 [SharePoint 2013 のアドイン承認ポリシーの種類](add-in-authorization-policy-types-in-sharepoint-2013.md)」を参照してください。
  
    
    

## 2 つの OAuth のランタイム フロー
<a name="Flows"> </a>

SharePoint にアクセスしているクラウド ホスト型 SharePoint アドイン または外部アプリケーションが実行されると、そのたびにフロー、つまりアドイン、SharePoint 2013、ACS、および場合によってはエンド ユーザーの間の一連のやりとりが発生します。フローの最終結果は、アプリケーションが SharePoint に対して行う、作成、更新、削除 (CRUD) の各要求に含まれるアクセス トークンを SharePoint が受信することです。
  
    
    
SharePoint によって使用される主要な OAuth フローが 2 つあります。1 つはクラウド ホスト型 SharePoint アドインです。もう 1 つは、SharePoint データにアクセスするほかのプラットフォームのアプリケーションのための、「オン ザ フライ」と呼ばれるものです。
  
    
    

- **コンテキスト トークン フロー:**SharePoint アドイン のリモート コンポーネントは、SharePoint クライアント オブジェクト モデル (CSOM) または REST エンドポイントを使用して、SharePoint への呼び出しを行います。SharePoint は、リモート サーバーに送信できる ACS からのコンテキスト トークンを要求します。リモート サーバーはコンテキスト トークンを使用して、ACS からのアクセス トークンを要求します。リモート サーバーはアクセス トークンを使用して、SharePoint へ逆方向に通信します。このフローの詳細については、「 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。
    
  
- **認証コード フロー:**SharePoint アドイン には、インストール時に SharePoint のリソースに対して必要なアクセス許可が付与されます。ただし、SharePoint にインストールされていないアプリケーションは、「オン ザ フライ」で、つまり実行時に毎回アクセス許可を求める必要があります。このフローには SharePoint のコンテキスト トークンは含まれません。その代わりに、アドインが実行されて SharePoint にアクセスしようとした時点で、要求しているアプリケーションにアクセス許可を付与するようにというメッセージが SharePoint からユーザーに表示されます。ユーザーがアクセス許可を付与すると、SharePoint はアプリケーションに渡す承認コードを ACS から取得します。アプリケーションはコードを使用して ACS からアクセス トークンを取得し、それを使用して SharePoint と通信します。このフローの詳細については、「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。
    
  

## 低信頼 SharePoint アドイン のトラブルシューティング
<a name="Trouble"> </a>

ここでは、トラブルシューティングに関する一般的なガイダンスと、低信頼の承認システムを使用する SharePoint アドイン に関する具体的な事項をいくつか説明します。
  
    
    

### Fiddler ツールを使用する

無料の  [Fiddler ツール](http://www.telerik.com/fiddler) を使用して、アドインのリモート コンポーネントから SharePoint に送信される HTTP 要求をキャプチャできます。このツールには、要求に含まれるアクセス トークンを自動的にデコードする [無料の拡張機能](https://github.com/andrewconnell/SPOAuthFiddlerExt) があります。
  
    
    

### 開発中は OAuth の HTTPS 要件をオフにする
<a name="TurnOffHTTPRequirement"> </a>

OAuth では SharePoint が HTTPS を実行する必要があります (サービスだけでなく SharePoint も)。これは、アドインの開発時に妨げになる場合があります。たとえば、アドインのデバッグ時に SharePoint を呼び出そうとすると、アドインが実行される場所である "localhost" に SSL サポートがないため、403 (禁止) メッセージを受け取ることがあります。
  
    
    
開発中は、次の Windows PowerShell コマンドレットを使用して、HTTPS 要件をオフにできます。
  
    
    


```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()
```

後で HTTPS 要件をオンに戻すには、次の Windows PowerShell コマンドレットを使用します。
  
    
    


```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()
```


### その他の SSL およびドメイン関連の承認エラー
<a name="DomainRelatedErrors"> </a>

構成ファイルと登録フォームのドメイン名の不一致により、承認されない場合があります。次の 4 つの値が完全に同一でなければなりません。
  
    
    

- SharePoint アドイン を AppRegNew.aspx または 販売者ダッシュボード のいずれかに登録する際に指定する [ **アドイン ドメイン**]。
    
  
- リモート Web アプリケーションのセキュリティ証明書が登録されているドメイン。
    
  
- AppManifest.xml ファイルの **StartPage** 値のドメイン部分。
    
  
- AppManifest.xml で指定されたイベント レシーバーの URL のドメイン部分。
    
  
この点に関連して、次のことに注意してください。
  
    
    

- SharePoint アドイン のリモート コンポーネントが 443 以外のポートを使用している場合、4 つの場所のすべてでドメインの一部として明示的にポート (たとえば、 `contoso.com:3333`) を含める必要があります。(HTTPS プロトコルを使用する必要があり、その場合の既定のポートは 443 です。)
    
  
- リモート Web アプリケーションに DNS CNAME エイリアスを作成する場合は、4 か所すべてでドメイン値に CNAME エイリアスを使用します。たとえば、アプリケーションが contoso.cloudapp.net でホストされていて、これに contososoftware.com という CNAME を作成する場合は、contososoftware.com をドメインとして使用します。
    
  
- アドインをパッケージ化する前に、AppManifest.xml ファイルの **StartPage** 値 (およびすべてのイベント レシーバー URL) にドメインをハードコーディングする必要があります。Visual Studio の [ **発行**] ウィザードを使用してアドインをパッケージ化する場合は、ドメインを入力するよう求めるプロンプトが表示され、Office Developer Tools for Visual Studio により、そのドメインが (デバッグ中に使用される  `~remoteWebUrl` トークンの代わりに) **StartPage** 値に挿入されます。しかし、[ **発行**] ウィザードを使用しない場合、またはウィザードを使用するもののリモート Web アプリケーションが Azure に展開される場合は、手動でトークンをドメイン (およびプロトコル) に置き換える必要があります (例:  `https://contososoftware.com` または `https://MyCloudVM:3333`)。
    
  

### エラー "基礎になる接続が閉じられました: SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。"
<a name="ErrorConnectionClosed"> </a>

このエラーは OAuth の問題ではなく、SSL ハンドシェイクの問題です。使用している証明書が SharePoint によって信頼されていること、また証明書がエンドポイント名と一致していることを確認してください。
  
    
    

### HTTP DAV メソッドを使用してファイルを SharePoint から読み込む際のエラー
<a name="ErrorConnectionClosed"> </a>

HTTP DAV は OAuth では動作しません。SharePoint のクライアント オブジェクト モデル (CSOM) を使用している場合は、以下のコードを使用してファイルを読み込みます。
  
    
    
```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();
```


## このセクションの内容
<a name="Trouble"> </a>

 [Office 365 SharePoint サイトを使用してオンプレミスの SharePoint サイトのプロバイダー向けのホスト型アドインに権限を付与する](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)
  
    
    
 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [SharePoint アドインで期限が切れたクライアント シークレットを置換する方法](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)
  
    
    

## その他の技術情報
<a name="FileName_AdditionalResources"> </a>


-  [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)
    
  

