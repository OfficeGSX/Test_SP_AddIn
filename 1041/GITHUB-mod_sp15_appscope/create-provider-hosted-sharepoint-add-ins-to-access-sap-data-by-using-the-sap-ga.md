---
title: SAP Gateway for Microsoft を使用して SAP データにアクセスする SharePoint のプロバイダー向けのホスト型アドインを作成する
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# SAP Gateway for Microsoft を使用して SAP データにアクセスする SharePoint のプロバイダー向けのホスト型アドインを作成する
SAP データにアクセスできる SharePoint アドインを構築する方法について説明します。
SAP Gateway for Microsoft および Azure AD Authentication Library for .NET を使用すると、SAP データの読み書き、およびオプションで SharePoint データの読み書きを行う SharePoint アドインを作成できます。この記事では、SAP への承認されたアクセスを行う SharePoint アドインを設計する手順について説明します。 





## 開始の前に

この記事に挙げる手順の前提条件は以下のとおりです。




- Microsoft Azure Active Directory サブスクリプションに関連付けられた Office 365 ドメイン内の **Office 365 開発者向けサイト**。「 [Office 365 で SharePoint アドインの開発環境をセットアップする](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md)」と「 [既存の Office 365 サブスクリプション内で開発者向けサイトを作成する](create-a-developer-site-on-an-existing-office-365-subscription.md)」を参照してください。


- **Visual Studio 2013 更新プログラム 2** 以降。これは、「 [Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx)」から入手できます。


- **Microsoft Office Developer Tools for Visual Studio**。Visual Studio 2013 更新プログラム 2 以降に含まれるバージョンです。


- **SAP Gateway for Microsoft** を Microsoft Azure に展開および構成します。 [SAP Gateway for Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635) のドキュメントを参照してください。


- **Microsoft Azure 内の組織アカウント**。「 [Common Consent Framework を使用した Office 365 API プレビューの統合](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount)」を参照してください。

    > **メモ**
      > アカウントが作成されたら、Office 365 アカウント (login.microsoftonline.com) にログインして一時パスワードを変更します。 
- サンプル データがある **SAP OData エンドポイント**。 [SAP Gateway for Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635) のドキュメントを参照してください。


- **Azure AD の基本的な知識。**「 [Azure AD の概要](http://msdn.microsoft.com/library/azure/dn655157.aspx)」を参照してください。


- **SharePoint アドインの作成についての基本的な知識。**「 [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)」を参照してください。


- **Azure AD での OAuth 2.0 に関する基本的な知識**。「 [Azure AD での OAuth 2.0](http://msdn.microsoft.com/library/azure/dn645545.aspx)」およびその子トピックを参照してください。


 **コード サンプル:** [SharePoint 2013: SharePoint アドインで SAP Gateway to Microsoft を使用する](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)




## SAP Gateway for Microsoft および SharePoint への認証と承認について理解する
<a name="AuthOverview"> </a>

Azure AD で OAuth 2.0 を使用すると、Microsoft Azure によってホストされるアプリケーションは複数のリソースにアクセスできるようになります。SAP Gateway for Microsoft は、そのようなアプリケーションの 1 つです。OAuth 2.0 では、ユーザーに加えて、アプリケーションもセキュリティ プリンシパルです。アプリケーション プリンシパルでは、ユーザーに加えて (場合によってはユーザーの代わりに)、保護されたリソースに対する認証と承認が必要です。プロセスには OAuth "フロー" が含まれ、そこではアプリケーション (SharePoint アドインの場合もある) がアクセス トークン (およびリフレッシュ トークン) を取得します。このトークンは、OAuth 2.0 承認サーバーとして Azure AD を使用するよう構成された、Microsoft Azure によってホストされるすべてのサービスとアプリケーションによって受け入れられます。このプロセスは、プロバイダー ホスト型 SharePoint アドインのリモート コンポーネントが SharePoint への承認を受ける方法 (「 [低信頼承認を使用する SharePoint アドインの作成](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)」およびその副記事で説明されています) に非常に似ています。ただし、ACS 承認システムでは、信頼済みトークンの発行元として Azure AD ではなく Microsoft Azure アクセス制御サービス (ACS) が使用されます。




> **ヒント**
> SharePoint アドインが SAP Gateway for Microsoft だけでなく SharePoint にもアクセスする場合、SAP Gateway for Microsoft へのアクセス トークンを取得するための Azure AD と、SharePoint へのアクセス トークンを取得するための ACS 承認システムの *両方*  のシステムが必要になります。2 つのリソースのトークンは、互いに交換できるものではありません。詳細については、「 [SharePoint へのアクセス許可を ASP.NET アプリケーションに追加する (オプション)](#SharePoint)」を参照してください。 




Azure AD の OAuth 2.0 によって使用される OAuth フローの詳細な説明および図については、「 [認証コードの付与フロー](http://msdn.microsoft.com/library/azure/dn645542.aspx)」を参照してください。(SharePoint へのアクセスのフローに関する類似の説明と図については、「 [コンテキスト トークン フローの手順を確認する](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps)」を参照してください。)




## SharePoint アドインを作成する
<a name="AuthOverview"> </a>


### Visual Studio ソリューションを作成する


1. 以下の手順に従って、 **SharePoint アドイン**のプロジェクトを Visual Studio で作成します。(この記事の例では、C# を使用することを想定していますが、新規プロジェクト テンプレートの [ **Visual Basic**] セクションで SharePoint アドイン のプロジェクトを開始することもできます。) 

1. [ **新しい SharePoint アドイン**] ウィザードでプロジェクトに名前を付け、[ **OK**] をクリックします。このトピックの例では、「SAP2SharePoint」を使用します。


2. デバッグ サイトとして Office 365 開発者向けサイト のドメイン URL を指定します (末尾のスラッシュも含めます)。たとえば、「https://<O365_domain>.sharepoint.com/」と指定します。アドインの種類として [ **プロバイダー向けのホスト型**] を指定します。[ **次へ**] をクリックします。


3. プロジェクトの種類を選択します。このトピックの例では、[ **ASP.NET Web フォーム アプリケーション**] を選択します。(SAP Gateway for Microsoft にアクセスする ASP.NET MVC アプリケーションを作成することもできます。) [ **次へ**] をクリックします。


4. Azure ACS を認証システムとして選択します。(SharePoint アドインは、SharePoint にアクセスする場合、このシステムを使用します。SAP Gateway for Microsoft にアクセスする場合は、このシステムを使用しません。) [ **完了**] をクリックします。


2. プロジェクトが作成された後、Office 365 アカウントにログインするように求めるプロンプトが表示されます。アカウント管理者の資格情報を使用します (たとえば、「Bob@<O365_domain>.onmicrosoft.com」)。


3. Visual Studio ソリューションには、SharePoint アドインの固有のプロジェクトと ASP.NET Web フォーム プロジェクトの 2 つのプロジェクトがあります。以下の手順に従って、 **Active Directory Authentication Library** (ADAL) パッケージを ASP.NET プロジェクトに追加します。

1. ASP.NET プロジェクト (このトピックの例では [ **SAP2SharePointWeb**]) の [ **References**] フォルダーを右クリックして、[ **NuGet パッケージの管理**] を選択します。 


2. 開いたダイアログの左側にある [ **オンライン**] を選択します。検索ボックスに「Microsoft.IdentityModel.Clients.ActiveDirectory」と入力します。


3. 検索結果に ADAL ライブラリが表示されたら横にある [ **インストール**] ボタンをクリックします。プロンプトが表示されたらライセンスに同意します。


4. 以下の手順に従って、Json.net パッケージを ASP.NET プロジェクトに追加します。

1. 検索ボックスに「Json.net」と入力します。ヒット件数が多すぎる場合、「Newtonsoft.json」で検索してみます。


2. 検索結果に Json.net が表示されたら、横にある [ **インストール**] ボタンをクリックします。


5. [ **閉じる**] をクリックします。



### Web アプリケーションを Azure AD で登録する


1. Azure 管理者アカウントで  [Azure 管理ポータル](https://manage.windowsazure.com)にログインします。

    > **メモ**
      > セキュリティ上、アドインの開発時に管理者アカウントを使用することはお勧めしません。 
2. 左側の [ **Active Directory**] を選択します。


3. ディレクトリをクリックします。 


4. [ **アプリケーション**] (上部のナビゲーション バーにあります) を選択します。


5. 画面下部にあるツールバーで [ **追加**] を選択します。


6. 開いたダイアログで [ **組織が開発中のアプリケーションを追加**] を選択します。


7. [ **アプリケーションの追加**] ダイアログでアプリケーション名を指定します。このトピックの例では、「ContosoAutomobileCollection」を使用します。 


8. [ **Web アプリケーションと WEB API の一方または両方**] をアプリケーションの種類として選択し、右矢印ボタンをクリックします。


9. ダイアログの 2 番目のページで、[ **サインオン URL**] として Visual Studio ソリューションの ASP.NET プロジェクトから SSL デバッグ URL を使用します。以下の手順を使用して URL を見つけることができます。 ** *(Visual Studio デバッガー (F5) を実行できるように、まずアドインをデバッグ URL に登録する必要があります。アドインのステージングの準備ができたら、ステージング Azure Web サイト URL に再登録します。 [アドインを変更して Azure および Office 365 にステージングする](#Stage)。)* **

1. **ソリューション エクスプローラー**で ASP.NET プロジェクトを強調表示します。 


2. [ **プロパティ**] ウィンドウで [ **SSL URL**] プロパティの値をコピーします (たとえば、https://localhost:44300/)。


3. その値を [ **アプリケーションの追加**] ダイアログの [ **サインオン URL**] に貼り付けます。


10. [ **アプリケーション ID/URI**] では、アプリケーションに一意の URI を指定します。たとえば、https://localhost:44300/ContosoAutomobileCollection のように、SSL URL の末尾にアプリケーション名を追加します。


11. チェックマーク ボタンをクリックします。アプリケーションの Azure ダッシュボードが開き、成功したことを示すメッセージが表示されます。


12. ページ上部で [ **構成**] を選択します。


13. [ **クライアント ID**] までスクロールして、その値をコピーします。これは後続の手順で必要になります。


14. [ **キー**] セクションでキーを作成します。このキーは、最初は表示されません。ページ下部にある [ **保存**] をクリックすると、キーが表示されます。そのキーをコピーします。これは後続の手順で必要になります。


15. [ **他のアプリケーションに対するアクセス許可**] までスクロールし、SAP Gateway for Microsoft サービス アプリケーションを選択します。 


16. [ **デリゲートされたアクセス許可**] ドロップダウン リストを開き、SharePoint アドインが必要とする SAP Gateway for Microsoft サービスへのアクセス許可のボックスを有効にします。


17. 画面下部にある [ **保存**] をクリックします。



### Azure AD と通信するようアプリケーションを構成する


1. Visual Studio で ASP.NET プロジェクトの web.config ファイルを開きます。


2. Office Developer Tools for Visual Studio により、 `<appSettings>` セクションに SharePoint アドインの **ClientID** および **ClientSecret** の要素が既に追加されています。(これらの要素は、ASP.NET アプリケーションが SharePoint にアクセスする場合に Azure ACS 承認システムで使用されます。このトピックの例ではこれらを無視できますが、削除することはできません。これらの要素はプロバイダー ホスト型の SharePoint アドインに必要であり、アドインが SharePoint データにアクセスしない場合でもそのことに変わりはありません。Visual Studio で F5 キーを押すたびに、その値が変更されます。) 以下の 2 つの要素をセクションに追加します。これらは、アプリケーションにより、Azure AD に対する認証のために使用されます。(OAuth ベースの認証および承認システムでは、ユーザーと同様、アプリケーションもセキュリティ プリンシパルであることを思い出してください。)

 ```

<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
 ```

3. 前の手順で Azure AD ディレクトリから保存したクライアント ID を、 **ida:ClientID** キーの値として 挿入します。大文字小文字および句読点は、コピーしたままの状態にし、値の先頭および末尾にスペース文字を含めないように注意します。 **ida:ClientKey** キーには、ディレクトリから保存した *キー*  を使用します。ここでも、スペース文字を含めないように、また何も変更しないように注意します。これで、 `<appSettings>` セクションは、次のようになります。( **ClientId** 値は、GUID または空の文字列に設定されます。)

 ```

<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
 ```


    > **メモ**
      > アプリケーションは、登録用に使用した "localhost" URL によって Azure AD から認識されます。クライアント ID およびクライアント キーはその ID に関連付けられます。アプリケーションを Azure Web サイト にステージングする準備ができたら、そのアプリケーションを新しい URL に再登録します。 
4. **appSettings** セクションで、 **Authority** キーを追加し、その値を組織アカウントの Office 365 ドメイン ( *some_domain*  .onmicrosoft.com) に設定します。このトピックの例では、組織アカウントは "Bob@<O365_domain>.onmicrosoft.com" です。それで、Authority は `<O365_domain>.onmicrosoft.com` になります。

 ```

<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
 ```

5. **appSettings** セクションで、 **AppRedirectUrl** キーを追加し、その値を、ASP.NET アドインが認証コードを Azure AD から取得した後にユーザーのブラウザーがリダイレクトされるページに設定します。通常、これは、Azure AD への呼び出しが実行されたときにユーザーに表示されたページと同じページになります。このトピックの例では、以下のように SSL URL 値に "/Pages/Default.aspx" が付加された値が使用されます。(これは、ステージング用に変更するのとは別の値です。)

 ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
 ```

6. **appSettings** セクションで、 **ResourceUrl** キーを追加し、その値を SAP Gateway for Microsoft のアプリケーション ID/URI に設定します (ASP.NET アプリケーションのアプリケーション ID/URI *ではありません*  )。この値は SAP Gateway for Microsoft 管理者から入手します。次に例を示します。

 ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
 ```


     `<appSettings>` セクションは、次のようになります。



 ```
  <appSettings>
  <add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
  <add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
</appSettings>
 ```

7. web.config ファイルを保存して閉じます。

    > **ヒント**
      > Visual Studio デバッガー (F5) を実行するときに、web.config ファイルを開いたままにしないでください。F5 キーを押すたびに、Office Developer Tools for Visual Studio は、 **ClientId** 値 ( **ida:ClientID** ではありません) を変更します。ファイルが開いていると、デバッグを実行する前に、プロンプトに応答して web.config ファイルを再度読み込む必要が生じます。

### Azure AD への認証用のヘルパー クラスを追加する


1. ASP.NET プロジェクトを右クリックし、Visual Studio のアイテム追加プロセスを使用して AADAuthHelper.cs という名前の新規クラス ファイルをプロジェクトに追加します。


2. 以下の **using** ステートメントをファイルに追加します。

 ```

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

 ```

3. アクセス キーワードを **public** から **internal** に変更し、 **static** キーワードをクラス宣言に追加します。

 ```

internal static class AADAuthHelper
 ```

4. 次のフィールドをクラスに追加します。これらのフィールドは、AAD からアクセス トークンを取得するために ASP.NET アプリケーションが使用する情報を格納します。

 ```
  private static readonly string _authority = ConfigurationManager.AppSettings["Authority"];
private static readonly string _appRedirectUrl = ConfigurationManager.AppSettings["AppRedirectUrl"];
private static readonly string _resourceUrl = ConfigurationManager.AppSettings["ResourceUrl"]; 
private static readonly string _clientId = ConfigurationManager.AppSettings["ida:ClientID"];    
private static readonly ClientCredential _clientCredential = new ClientCredential(
                           ConfigurationManager.AppSettings["ida:ClientID"],
                           ConfigurationManager.AppSettings["ida:ClientKey"]);

private static readonly AuthenticationContext _authenticationContext = 
            new AuthenticationContext("https://login.windows.net/common/" + 
                                      ConfigurationManager.AppSettings["Authority"]);
 ```

5. 次のプロパティをクラスに追加します。このプロパティは、URL を Azure AD ログイン画面に保持します。

 ```

private static string AuthorizeUrl
{
    get
    {
        return string.Format("https://login.windows.net/{0}/oauth2/authorize?response_type=code&amp;redirect_uri={1}&amp;client_id={2}&amp;state={3}",
            _authority,
            _appRedirectUrl,
            _clientId,
            Guid.NewGuid().ToString());
    }
}

 ```

6. 次のプロパティをクラスに追加します。これらは、アクセス トークンおよびリフレッシュ トークンをキャッシュし、それらの有効性を確認します。

 ```

public static Tuple<string, DateTimeOffset> AccessToken
{
    get {
return HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] 
       as Tuple<string, DateTimeOffset>;
    }

    set { HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] = value; }
}

private static bool IsAccessTokenValid
{
   get 
   { 
       return AccessToken != null &amp;&amp;
       !string.IsNullOrEmpty(AccessToken.Item1) &amp;&amp;
       AccessToken.Item2 > DateTimeOffset.UtcNow;
   }
}

private static string RefreshToken
{
    get { return HttpContext.Current.Session["RefreshToken" + _resourceUrl] as string; }
    set { HttpContext.Current.Session["RefreshToken-" + _resourceUrl] = value; }
}

private static bool IsRefreshTokenValid
{
    get { return !string.IsNullOrEmpty(RefreshToken); }
}

 ```

7. 次のメソッドをクラスに追加します。これらは、認証コードの有効性を確認するため、また認証コードまたはリフレッシュ トークンのいずれかを使用してアクセス トークンを Azure AD から取得するために使用されます。

 ```

private static bool IsAuthorizationCodeNotNull(string authCode)
{
    return !string.IsNullOrEmpty(authCode);
}

private static Tuple<Tuple<string,DateTimeOffset>,string> AcquireTokensUsingAuthCode(string authCode)
{
    var authResult = _authenticationContext.AcquireTokenByAuthorizationCode(
                authCode,
                new Uri(_appRedirectUrl),
                _clientCredential,
                _resourceUrl);

    return new Tuple<Tuple<string, DateTimeOffset>, string>(
                new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn), 
                authResult.RefreshToken);
}

private static Tuple<string, DateTimeOffset> RenewAccessTokenUsingRefreshToken()
{
    var authResult = _authenticationContext.AcquireTokenByRefreshToken(
                         RefreshToken,
                         _clientCredential.OwnerId,
                         _clientCredential,
                         _resourceUrl);

    return new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);
}

 ```

8. 次のメソッドをクラスに追加します。これは、SAP Gateway for Microsoft を介して SAP データを取得する呼び出しを実行する前に、有効なアクセス トークンを取得するため、ASP.NET 分離コードから呼び出されます。

 ```

internal static void EnsureValidAccessToken(Page page)
{
    if (IsAccessTokenValid) 
    {
        return;
    }
    else if (IsRefreshTokenValid) 
    {
        AccessToken = RenewAccessTokenUsingRefreshToken();
        return;
    }
    else if (IsAuthorizationCodeNotNull(page.Request.QueryString["code"]))
    {
        Tuple<Tuple<string, DateTimeOffset>, string> tokens = null;
        try
        {
            tokens = AcquireTokensUsingAuthCode(page.Request.QueryString["code"]);
        }
        catch 
        {
            page.Response.Redirect(AuthorizeUrl);
        }
        AccessToken = tokens.Item1;
        RefreshToken = tokens.Item2;
        return;
    }
    else
    {
        page.Response.Redirect(AuthorizeUrl);
    }
}
 ```


> **ヒント**
> AADAuthHelper クラスには、最小限のエラー処理しかありません。運用環境で使用可能な品質を持つ、堅牢な SharePoint アドインを作成するには、「 [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx)」の MSDN ノードで説明されているように、エラー処理をさらに追加する必要があります。 





### データ モデル クラスを作成する


1. 1 つ以上のクラスを作成して、アドインが SAP から取得するデータをモデル化します。このトピックの例では、データ モデル クラスが 1 つだけ存在します。ASP.NET プロジェクトを右クリックし、Visual Studio のアイテム追加プロセスを使用して、Automobile.cs という名前の新規クラス ファイルをプロジェクトに追加します。


2. 次のコードをクラスの本文に追加します。

 ```

public string Price;
public string Brand;
public string Model;
public int Year;
public string Engine;
public int MaxPower;
public string BodyStyle;
public string Transmission;
 ```


### 分離コードを追加して、SAP Gateway for Microsoft を介して SAP からデータを取得する


1. Default.aspx.cs ファイルを開き、次の **using** ステートメントを追加します。

 ```

using System.Net;
using Newtonsoft.Json.Linq;
 ```

2. アドインがアクセスする SAP OData エンドポイントのベース URL を値として持つ **const** 宣言を Default クラスに追加します。次に例を示します。

 ```

private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
 ```

3. Office Developer Tools for Visual Studio により、 **Page_PreInit** メソッドおよび **Page_Load** メソッドが追加されています。 **Page_Load** メソッド内のコードをコメント アウトし、 **Page_Init** メソッド全体をコメント アウトします。このコードはこの例では使用されません。(SharePoint アドインが SharePoint にアクセスする場合に、このコードを復元します。「 [SharePoint へのアクセス許可を ASP.NET アプリケーションに追加する (オプション)](#SharePoint)」を参照してください。)


4. 以下の行を **Page_Load** メソッドの上部に追加します。これにより、ASP.NET アプリケーションは SSL (HTTPS) を使用して SAP Gateway for Microsoft と通信するので、デバッグのプロセスが簡単になります。しかし、"localhost:port" サーバーは SAP Gateway for Microsoft の資格情報を信頼するようには構成されていません。このコード行がないと、Default.aspx が開く前に、無効な資格情報であることを警告するメッセージが表示されます。一部のブラウザーではクリックしてこのエラーに対処できますが、他のブラウザーでは Default.aspx をまったく開くことができません。

 ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
 ```


    > **重要**
      > ASP.NET アプリケーションをステージングに展開する準備ができたら、この行を削除します。「 [アドインを変更して Azure および Office 365 にステージングする](#Stage)」を参照してください。 
5. 次のコードを **Page_Load** メソッドに追加します。 `GetSAPData` メソッドに渡す文字列は、OData クエリです。

 ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

 ```

6. 次のメソッドを Default クラスに追加します。このメソッドは、アクセス トークンのキャッシュに、Azure AD から取得した有効なアクセス トークンがあることを最初に確認します。それから、アクセス トークンを含む HTTP **GET** 要求を作成し、その要求を SAP OData エンドポイントに送信します。結果は JSON オブジェクトとして返され、これが .NET **List** オブジェクトに変換されます。アイテムの 3 つのプロパティが、 **DataListView** にバインドされた配列で使用されます。

 ```

private void GetSAPData(string oDataQuery)
{
    AADAuthHelper.EnsureValidAccessToken(this);

    using (WebClient client = new WebClient())
    {
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.Authorization] = "Bearer " + AADAuthHelper.AccessToken.Item1;
        var jsonString = client.DownloadString(SAP_ODATA_URL + oDataQuery);
        var jsonValue = JObject.Parse(jsonString)["d"]["results"];
        var dataCol = jsonValue.ToObject<List<Automobile>>();

        var dataList = dataCol.Select((item) => {
            return item.Brand + " " + item.Model + " " + item.Price;
            }).ToArray();

        DataListView.DataSource = dataList;
        DataListView.DataBind();
    }
}

 ```


### ユーザー インターフェイスを作成する


1. Default.aspx ファイルを開き、次のマークアップをページの **form**に追加します。

 ```

<div>
  <h3>Data from SAP via SAP Gateway for Microsoft</h3>

  <asp:ListView runat="server" ID="DataListView">
    <ItemTemplate>
      <tr runat="server">
        <td runat="server">
          <asp:Label ID="DataLabel" runat="server"
            Text="<%# Container.DataItem.ToString()%>" /><br />
        </td>
      </tr>
    </ItemTemplate>
  </asp:ListView>
</div>
 ```

2. オプションで、SharePoint  [クロム コントロール](use-the-client-chrome-control-in-sharepoint-add-ins.md)および [ホスト SharePoint Web サイトのスタイル シート](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)を使用して Web ページに SharePoint ページの "外観" を付与します。



### Visual Studio で F5 キーを使用してアドインをテストする


1. Visual Studio で F5 キーを押します。


2. 初めて F5 キーを使用するとき、使用する 開発者向けサイト にログインするように求めるプロンプトが表示される場合があります。サイト管理者の資格情報を使用します。このトピックの例では、Bob@<O365_domain>.onmicrosoft.com です。


3. 初めて F5 キーを使用するとき、アドインへのアクセス許可を付与するように求めるプロンプトが表示されます。[ **信頼する**] をクリックします。


4. アクセス トークンの取得中に、少し遅れて Default.aspx ページが開きます。SAP データが表示されていることを確認します。



## SharePoint へのアクセス許可を ASP.NET アプリケーションに追加する (オプション)
<a name="SharePoint"> </a>

当然、SharePoint アドインが行うことは、SharePoint から起動された Web ページに SAP データを表示することだけに限られません。SharePoint データの作成、読み込み、更新、削除 (CRUD) も実行できます。分離コードでは、SharePoint クライアント オブジェクト モデル (CSOM) または SharePoint の REST API のいずれかを使用してこれらを実行します。CSOM は、Office Developer Tools for Visual Studio によって ASP.NET プロジェクトに自動的に含められるアセンブリのペアとして展開されます。これは、Visual Studio で [ **ローカルにコピー**] に設定されて、ASP.NET アプリケーション パッケージに含められるようになります。CSOM の使用の詳細については、まず「 [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)」を参照してください。REST API の使用の詳細については、まず「 [SharePoint 2013 REST インターフェイスを理解して使用する](http://msdn.microsoft.com/ja-jp/magazine/dn198245.aspx)」を参照してください。 



SharePoint にアクセスするため CSOM を使用するか、REST API を使用するかにかかわりなく、ASP.NET アプリケーションは、SAP Gateway for Microsoft に対して行うように、SharePoint へのアクセス トークンも取得する必要があります。この記事の前のほうにある「 [SAP Gateway for Microsoft および SharePoint への認証と承認について理解する](#AuthOverview)」を参照してください。以下の手順では、この実行方法に関する基本的なガイダンスが提供されますが、まず以下の記事を読むことをお勧めします。




-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)


-  [SharePoint アドインの 3 つの承認システム](three-authorization-systems-for-sharepoint-add-ins.md)


-  [低信頼承認を使用する SharePoint アドインの作成](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)


-  [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)



1. Default.aspx.cs ファイルを開き、 **Page_PreInit** メソッドのコメントを解除します。また、Office Developer Tools for Visual Studio によって **Page_Load** メソッドに追加されたコードもコメント解除します。


2. SharePoint アドインが SharePoint データにアクセスする場合、アドインが SharePoint で起動されるときに Default.aspx ページに POST される SharePoint コンテキスト トークンをキャッシュする必要があります。これにより、ブラウザーが Azure AD 認証の後にリダイレクトされても、SharePoint コンテキスト トークンが失われないようにすることができます。(このコンテキストをキャッシュする方法には、いくつかのオプションがあります。) Office Developer Tools for Visual Studio は大部分の処理を実行する SharePointContext.cs ファイルを ASP.NET プロジェクトに追加します。セッション キャッシュを使用するには、" `if (!IsPostBack)`" ブロック内の SAP Gateway for Microsoft を呼び出すコードの *前*  に以下のコードを追加するだけです。

 ```

if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
 ```

3. SharePointContext.cs ファイルは、Office Developer Tools for Visual Studio がプロジェクトに追加したもう 1 つのファイル (TokenHelper.cs) を呼び出します。このファイルは、SharePoint のアクセス トークンを取得および使用するために必要な大部分のコードを提供します。ただし、有効期限が切れたアクセス トークンや有効期限の切れたリフレッシュ トークンを更新するコードは提供しません。また、トークンをキャッシュするコードも含まれていません。運用環境用の SharePoint アドインを作成するには、このようなコードを追加する必要があります。前述のステップのキャッシュ ロジックは、その一例です。また、アクセス トークンをキャッシュしたり、有効期限が切れるまで再使用したりするコードも含める必要があります。アクセス トークンの有効期限が切れたら、コードはリフレッシュ トークンを使用して新しいアクセス トークンを取得しなければなりません。


4. CSOM または REST を使用してデータ呼び出しを SharePoint に追加します。以下の例は、Office Developer Tools for Visual Studio が **Page_Load** メソッドに追加する CSOM コードを変更したものです。この例では、コードが独立したメソッドになっており、キャッシュされたコンテキスト トークンの取得によって開始されます。

 ```

private void GetSharePointTitle()
{
    var spContext = HttpContext.Current.Session["SharePointContext"] as SharePointContext;
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        clientContext.Load(clientContext.Web, web => web.Title);
        clientContext.ExecuteQuery();
        SharePointTitle.Text = "SharePoint web site title is: " + clientContext.Web.Title;
    }
}
 ```

5. SharePoint データを表示するための UI 要素を追加します。以下は、前述のメソッドで参照される HTML コントロールを示しています。

 ```

<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
 ```


> **メモ**
> SharePoint アドインをデバッグする際、Visual Studio で F5 キーを押すたびに、Office Developer Tools for Visual Studio は Azure ACS にアドインを再登録します。SharePoint アドインをステージングする場合、長期の登録を行う必要があります。セクション「 [アドインを変更して Azure および Office 365 にステージングする](#Stage)」を参照してください。 





## アドインを変更して Azure および Office 365 にステージングする
<a name="Stage"> </a>

Visual Studio で F5 キーを使用して SharePoint アドインのデバッグを終了したら、ASP.NET アプリケーションを実際の Azure Web サイトに展開する必要があります。




### Azure Web サイトを作成する


1. Microsoft Azure ポータルで、左側のナビゲーション バーで [ **Web サイト**] を開きます。


2. ページ下部にある [ **新規**] をクリックして、[ **新規**] ダイアログで [ **WEB サイト**] | [ **簡易作成**] を選択します。


3. ドメイン名を入力して、[ **Web サイトの作成**] をクリックします。新しいサイトの URL のコピーを作成します。これは、 `my_domain.azurewebsites.net` のような形式になります。



### アプリケーションでコードおよびマークアップを変更する


1. Visual Studio で、Default.aspx.cs ファイルから行 " `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;`" を削除します。


2. ASP.NET プロジェクトの web.config ファイルを開き、 **appSettings** セクションにある **AppRedirectUrl** キーの値のドメイン部分を Azure Web サイトのドメインに変更します。たとえば、" `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />`" を " `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`" に変更します。


3. SharePoint アドイン プロジェクトで AppManifest.xml ファイルを右クリックして、[ **コードの表示**] を選択します。


4. **StartPage** 値で、文字列 `~remoteAppUrl` を、プロトコルを含む Azure Web サイトの完全なドメインで置き換えます (たとえば、 `https://my_domain.azurewebsites.net`)。 **StartPage** 値全体は、" `https://my_domain.azurewebsites.net/Pages/Default.aspx`" となります。(通常、 **StartPage** 値は、web.config ファイルの **AppRedirectUrl** キーの値とまったく同じになります。)



### AAD 登録を変更してアドインを ACS に登録する


1.  [Azure 管理ポータル](https://manage.windowsazure.com)に Azure 管理者アカウントでログインします。


2. 左側の [ **Active Directory**] を選択します。


3. ディレクトリをクリックします。


4. [ **アプリケーション**] (上部のナビゲーション バーにあります) を選択します。


5. 作成したアプリケーションを開きます。このトピックの例では、" **ContosoAutomobileCollection**" です。


6. 以下のそれぞれの値について、値の "localhost: *port*  " 部分を新しい Azure Web サイトのドメインに変更します。

  - **サインオン URL**


  - **アプリケーション ID/URI**


  - **応答 URL**



    たとえば、[ **アプリケーション ID/URI**] が " **https://localhost:44304/ContosoAutomobileCollection**" の場合、" **https://<my_domain>.azurewebsites.net/ContosoAutomobileCollection**" に変更します。


7. 画面下部にある [ **保存**] をクリックします。


8. アドインを Azure ACS に登録します。アドインが SharePoint にアクセスせず、ACS からのトークンを使用しない場合であっても、この手順を実行する必要があります。それは、この同じプロセスで、アドインが Office 365 サブスクリプションのアドイン管理サービスにも登録されるためです。これは必須です (当初 SharePoint アドイン は「SharePoint 用アプリ」と呼ばれていたため、「アドイン管理サービス」と呼ばれます)。登録は Office 365 サブスクリプションの SharePoint Web サイトの AppRegNew.aspx ページで行います。詳細については、「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」を参照してください。このプロセスの一部として、新しいクライアント ID とクライアント シークレットを取得することになります。web.config で **ClientId** ( **ida:ClientID** ではない) と **ClientSecret** キーにこれらの値を挿入します。

    > **注意**
      > この変更を行った後に、何らかの理由で F5 キーを押した場合、Office Developer Tools for Visual Studio はこれらの値の一方または両方を上書きします。そのため、AppRegNew.aspx で取得した値の記録を保持しておき、ASP.NET アプリケーションを発行する直前に、web.config のその値が正しいことを常に確認する必要があります。 

### ASP.NET アプリケーションを Azure に発行してアドインを SharePoint にインストールする


1. ASP.NET アプリケーションを Azure Web サイトに発行する方法はいくつかあります。詳細については、「 [Azure の Web サイトのデプロイ方法](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-deploy/)」を参照してください。


2. Visual Studio では、SharePoint アドイン プロジェクトを右クリックして、[ **パッケージ**] を選択します。開いた [ **アドインを発行する**] ページで、[ **アドインをパッケージ化する**] をクリックします。アドイン パッケージが格納されたフォルダーが表示されたエクスプローラーが開きます。


3. Office 365 にグローバル管理者としてログインし、組織のアドイン カタログ サイト コレクションまで移動します。(1 つも存在しない場合は、作成します。「 [アドイン カタログを使ってカスタム ビジネス アドインを SharePoint Online 環境で利用できるようにする](http://office.microsoft.com/ja-jp/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)」を参照してください。)


4. アドイン パッケージをアドイン カタログにアップロードします。


5. サブスクリプション内の任意の Web サイトの [ **サイト コンテンツ**] ページに移動し、[ **アドインの追加**] をクリックします。


6. [ **自分のアドイン**] ページで、[ **追加できるアドイン**] セクションまでスクロールし、アドインのアイコンをクリックします。


7. アドインがインストールされたら、[ **サイト コンテンツ**] ページにあるそのアイコンをクリックしてアドインを起動します。


SharePoint アドインのインストールの詳細については、「 [SharePoint アドインの展開とインストール: 方法とオプション](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)」を参照してください。
## アドインを運用環境に展開する
<a name="Stage"> </a>

すべてのテストを終了したら、アドインを運用環境に展開できます。この手順を実行するには、いくつかの変更が必要になる場合があります。




1. ASP.NET アプリケーションの運用ドメインがステージング ドメインとは異なる場合、web.config の **AppRedirectUrl** 値と AppManifest.xml ファイルの **StartPage** 値を変更し、SharePoint アドインを再パッケージ化する必要があります。この記事の前のほうにある手順「 **アプリケーションでコードおよびマークアップを変更する** 」を参照してください。


2. ドメインを変更するには、AAD へのアドインの登録を編集する必要もあります。上記の手順「 **AAD 登録を変更してアドインを ACS に登録する** 」を参照してください。


3. 同じ手順で説明したとおり、ドメインを変更するには、ACS (およびサブスクリプションのアドイン管理サービス) へのアドインの再登録が必要です。(ACS へのアドインの登録を *編集*  する方法はありません。) ただし、新しいクライアント ID やクライアント シークレットを AppRegNew.aspx ページで生成する必要はありません。元の値を web.config の **ClientId** ( **ida:ClientID** ではない) および **ClientSecret** キーから AppRegNew フォームにコピーすることができます。 *新しいクライアント ID やクライアント シークレットを生成する場合、新しい値を web.config のキーに忘れずにコピーしてください。* 



