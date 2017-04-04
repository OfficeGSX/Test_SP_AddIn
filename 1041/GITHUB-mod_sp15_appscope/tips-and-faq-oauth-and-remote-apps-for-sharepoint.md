

# ヒントとよく寄せられる質問: SharePoint の OAuth とリモート アプリ
このトピックは古くなりました。以下のリンクを参照して、コンテンツの現在の所在を確認してください。 





||
|:-----|
|**この記事の内容** <BR/> [アプリ情報の取得](#AppInfo) <BR/> [AppManifest.xml ファイル](#Manifest) <BR/> [Web.config ファイル](#Webconfig) <BR/> [URL と Secure Sockets Layer (SSL)](#URL) <BR/> [アプリのリダイレクト ページ ](#Redirect) <BR/> [リダイレクト URI](#RedirectURI) <BR/> [OAuth トークン](#Tokens) <BR/> [アクセス許可およびアクセス許可の要求スコープ](#Perm) <BR/> [OAuth 承認ポリシーの種類](#Policy) <BR/> [デバッグ](#Debugging) <BR/> [社内の高信頼アプリ (サーバー間アプリ)](#S2S) <BR/> [OAuth に関連するその他の問題](#Misc) <BR/> [ソーシャル機能](#Social) <BR/> [その他の技術情報](#AR)|
   

## アプリ情報の取得
<a name="AppInfo"> </a>

このコンテンツは、「 [アドイン登録情報とアドイン プリンシパル情報を取得する ](register-sharepoint-add-ins-2013.md#Retrieve)」に移動されました。
  
    
    

## AppManifest.xml ファイル
<a name="Manifest"> </a>

このコンテンツは以下の個所に移動されました。
  
    
    

-  [SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [SharePoint 用アプリのマニフェストのスキーマ リファレンス](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  

### <StartPage> 要素に含まれる URL は何ですか。

このコンテンツは以下の個所に移動されました。 
  
    
    


  
    
    
>  [SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

  
    
    
>  [StartPage 要素](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)
    
  

### AppManifest.xml ファイルの要素と属性はどのようなものですか。

このコンテンツは、「 [SharePoint 用アプリのマニフェストのスキーマ リファレンス](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)」に移動されました。
  
    
    

## Web.config ファイル
<a name="Webconfig"> </a>

このコンテンツは、「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」、特に「 [登録する値を web.config ファイルと AppManifest.xml ファイルに入力する](register-sharepoint-add-ins-2013.md#EditConfigFiles)」セクションに移動されました。
  
    
    

### Web.config ファイルのアプリ設定はどのようなものですか。

このコンテンツは、「 [登録する値を web.config ファイルと AppManifest.xml ファイルに入力する](register-sharepoint-add-ins-2013.md#EditConfigFiles)」に移動されました。
  
    
    

## URL と Secure Sockets Layer (SSL)
<a name="URL"> </a>

このコンテンツは以下の個所に移動されました。 
  
    
    

-  [高信頼アプリでの各種の SSL およびドメインに関連するエラー](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [ACS アプリでの各種の SSL およびドメインに関連するエラー](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### 使用している Web アプリケーションで、SharePoint への通信に問題があります。何をチェックすればいいですか。

このコンテンツは以下の個所に移動されました。 
  
    
    

-  [高信頼アプリでの各種の SSL およびドメインに関連するエラー](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [ACS アプリでの各種の SSL およびドメインに関連するエラー](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### クラウド サーバーをポイントするために、どのような URL をアプリにハードコードする必要がありますか。

このコンテンツは以下の個所に移動されました。 
  
    
    

-  [高信頼アプリでの各種の SSL およびドメインに関連するエラー](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [ACS アプリでの各種の SSL およびドメインに関連するエラー](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### CNAME エイリアス、またはアプリをホストする実際の基本となる URL を登録する必要がありますか。

このコンテンツは以下の個所に移動されました。 
  
    
    

-  [高信頼アプリでの各種の SSL およびドメインに関連するエラー](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [ACS アプリでの各種の SSL およびドメインに関連するエラー](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### "基礎になる接続が閉じられました: SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。" というエラーが表示されます。どうすればいいですか。

このコンテンツは、「 [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed)」に移動されました。
  
    
    

## アプリのリダイレクト ページ
<a name="Redirect"> </a>

このコンテンツは、「 [新しいコンテキスト トークンを取得する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)」に移動されました。
  
    
    

### どのような場合にアプリ リダイレクト ページを使用するのですか。

このコンテンツは、「 [新しいコンテキスト トークンを取得する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)」に移動されました。
  
    
    

### アプリ リダイレクト ページを使用してコンテキスト トークンを取得する方法を教えてください。

このコンテンツは、「 [新しいコンテキスト トークンを取得する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)」に移動されました。
  
    
    

### appredirect ページは URL でどのように使用するのですか。

このコンテンツは、「 [新しいコンテキスト トークンを取得する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)」に移動されました。
  
    
    

## リダイレクト URI
<a name="RedirectURI"> </a>

このコンテンツは、「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」に移動されました。
  
    
    

### リダイレクト URI は必要ですか。

このコンテンツは、「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」に移動されました。
  
    
    

## OAuth トークン
<a name="Tokens"> </a>

このコンテンツは以下の個所に移動されました。
  
    
    

-  [SharePoint のプロバイダー向けのホスト型低信頼アドインでセキュリティ トークンを処理する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)
    
  
-  [SharePoint のプロバイダー向けのホスト型高信頼アドインでアクセス トークンを作成して使用する](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
    
  

### コンテキスト トークンとは何ですか。

このコンテンツは、「 [コンテキスト トークンの構造と処理について理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens)」に移動されました。
  
    
    

### アクセス トークンとは何ですか。

このコンテンツは、「 [アクセス トークンの処理について理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)」に移動されました。
  
    
    

### リフレッシュ トークンとは何ですか。

このコンテンツは、「 [更新トークンの処理とキャッシュについて理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)」に移動されました。
  
    
    

### 他のページ要求についてもプロバイダーでホストされるアプリから直接使用できるように、コンテキスト トークン文字列が cookie に保存されますか。

このコンテンツは、「 [コンテキスト トークンまたはその一部をキャッシュする](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken)」に移動されました。
  
    
    

### キャッシュ キー値はどのように構成されていますか。どのような点で一意ですか。

このコンテンツは、「 [キャッシュ キーについて理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey)」に移動されました。
  
    
    

### コンテキスト トークンを取得する方法を教えてください。

このコンテンツは、「 [コンテキスト トークンの構造と処理について理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens)」に移動されました。
  
    
    

### コンテキスト トークンにはどのような情報が含まれますか。

このコンテンツは、「 [コンテキスト トークンの実例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken)」に移動されました。
  
    
    

### アクセス トークンにはどのような情報が含まれますか。

このコンテンツは以下の個所に移動されました。
  
    
    

-  [ACS 承認システムでのアクセス トークンの例を参照する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)
    
  
-  [高信頼承認システムでのアクセス トークンの例を参照する](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)
    
  

### nbf および exp の値からどのようにして正確な時刻と日付を計算するのですか。

このコンテンツは、「 [JWT 時刻値の取り扱い](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes)」に移動されました。
  
    
    

### SharePoint 以外のユーザーから、アプリの svc を保護したいと考えています。アプリのエントリ ポイントで (ClientContext を作成することによって) ユーザーの正当性をチェックしていますが、使用している WCF サービスは誰でも呼び出すことができます。svc メソッドを呼び出すたびにコンテキスト トークンから ClientContext を作成する必要がありますか。

このコンテンツは、「 [コンテキスト トークンを使用してアクセスを SharePoint ユーザーに限定する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess)」に移動されました。
  
    
    

### SharePoint POST 要求から取得された AppContext を非表示の入力フィールドとしてページに保持することはできますか。

このコンテンツは、「 [コンテキスト トークンまたはその一部をキャッシュする](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken)」に移動されました。
  
    
    

### リフレッシュ トークンが有効な期間はどれぐらいですか。

このコンテンツは、「 [更新トークンの処理とキャッシュについて理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)」に移動されました。
  
    
    

### 他のページ要求で使用できるように、アクセス トークンとホスト URL を cookie に保存しています。しかし、ユーザーが休憩を取っている間に、アクセス トークンが期限切れになっていました。どうすればいいですか。

このコンテンツは以下の個所に移動されました。
  
    
    

-  [アクセス トークンの処理について理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)
    
  
-  [更新トークンの処理とキャッシュについて理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)
    
  

### 期限切れではなくまだ有効な古いリフレッシュ トークンを破棄して新しいリフレッシュ トークンを使用するのは、どのような場合ですか。

このコンテンツは、「 [更新トークンの処理とキャッシュについて理解する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)」に移動されました。
  
    
    

## アクセス許可およびアクセス許可の要求スコープ
<a name="Perm"> </a>

このコンテンツは「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」に記載されています。
  
    
    

### リスト、ライブラリ コンテンツ、その他の機能に関するアクセス許可の要求スコープと使用可能な権限について教えてください。

現在、このコンテンツは「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」に記載されています。
  
    
    

## OAuth 承認ポリシーの種類
<a name="Policy"> </a>

このコンテンツは「 [SharePoint 2013 のアドイン承認ポリシーの種類](add-in-authorization-policy-types-in-sharepoint-2013.md)」に記載されています。
  
    
    

### app-only ポリシーと user + app ポリシーの違いについて教えてください。

このコンテンツは「 [SharePoint 2013 のアドイン承認ポリシーの種類](add-in-authorization-policy-types-in-sharepoint-2013.md)」に記載されています。
  
    
    

### アプリを起動する権限を付与または拒否する方法はありますか。

このコンテンツは「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」に記載されています。
  
    
    

## デバッグ
<a name="Debugging"> </a>

このコンテンツは以下の個所に移動されました。 
  
    
    

-  [SharePoint の高信頼アドインのトラブルシューティング](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 の低信頼アドインのトラブルシューティング](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Fiddler の使用

このコンテンツは以下の個所に移動されました。
  
    
    

-  [SharePoint の高信頼アドインのトラブルシューティング](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 の低信頼アドインのトラブルシューティング](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

## 社内の高信頼アプリ (サーバー間アプリ)
<a name="S2S"> </a>

このコンテンツは、「 [SharePoint の高信頼アドインのトラブルシューティング](troubleshooting-high-trust-sharepoint-add-ins.md)」に移動されました。
  
    
    

### 高信頼アプリを実行すると、401 認証エラーが表示されます。どうすればいいですか。

このコンテンツは、「 [SharePoint の高信頼アドインのトラブルシューティング](troubleshooting-high-trust-sharepoint-add-ins.md)」に移動されました。
  
    
    

### 高信頼アプリからコンテキスト トークンを取得する方法を教えてください。

このコンテンツは、「 [SharePoint のプロバイダー向けのホスト型高信頼アドインでアクセス トークンを作成して使用する](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)」に移動されました。
  
    
    

## OAuth に関連するその他の問題
<a name="Misc"> </a>

このコンテンツは以下の個所に移動されました。
  
    
    

-  [SharePoint の高信頼アドインのトラブルシューティング](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 の低信頼アドインのトラブルシューティング](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### HTTP DAV メソッドを使用してファイルを読み取ろうとすると、エラーが発生します。どうすればいいですか。

このコンテンツは、「 [SharePoint 2013 の低信頼アドインのトラブルシューティング](44ead3b9-75c3-4f68-b908-0af6197f1143.md)」に移動されました。
  
    
    

### OAuth 承認を、異なるドメインにある他のコンポーネントに転送する方法、または複数の URI に対して OAuth を構成する方法はありますか。

はい。「 [アクセス トークンをバックエンド システムに転送する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend)」を参照してください。
  
    
    

### SharePoint 2013 プリンシパルの値は一定ですか。

はい。「 [ACS 承認システムでのアクセス トークンの例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)」 (特に表 1) を参照してください。
  
    
    

### アプリ ID とアプリ シークレットは、特定のアプリのすべてのテナントにわたって一定ですか。

はい。アプリが 販売者ダッシュボード で登録され、各 SharePoint テナンシーまたはファームでも別個に登録されている場合、それぞれ別個の ID とシークレットを持っている可能性があります。そのような場合、アプリは Microsoft Azure アクセス制御サービス (ACS) からは複数のアプリのように見えます。「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」を参照してください。
  
    
    

### 領域は一意ですか。

はい。「 [ACS 承認システムでのアクセス トークンの例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)」 (特に表 1) と「 [コンテキスト トークンを使用してアクセス トークンを取得する](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken)」を参照してください。
  
    
    

### 開発中に OAuth への HTTPS 要求を無効にする方法を教えてください。

このコンテンツは、「 [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement)」に移動されました。
  
    
    

## ソーシャル機能
<a name="Social"> </a>

このコンテンツは以下の個所に移動されました。
  
    
    

1.  [SharePoint 2013 のソーシャルおよびコラボレーション機能](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)
    
  
2.  [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)
    
  

### ユーザーの ID とプロパティを取得する方法を教えてください。

「 [SharePoint 2013 でユーザー ID とプロパティを取得する](get-user-identity-and-properties-in-sharepoint-2013.md)」を参照してください。
  
    
    

### さまざまなソーシャル機能およびアクセス許可の要求スコープの使用方法について教えてください。

このコンテンツは、「 [SharePoint 2013 でのソーシャル機能の開発の概要](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms)」に記載されています。
  
    
    

### 自分をフォローしている人のユーザー プロファイルのプロパティを取得する方法を教えてください。

このコンテンツは、「 [[方法] SharePoint 2013 の .NET クライアント オブジェクト モデルを使用してユーザー プロファイル プロパティを取得する](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp)」に移動されました。
  
    
    