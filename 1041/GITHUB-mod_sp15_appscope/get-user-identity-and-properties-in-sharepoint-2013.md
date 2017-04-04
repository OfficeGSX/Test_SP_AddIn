---
title: SharePoint 2013 でユーザー ID とプロパティを取得する
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# SharePoint 2013 でユーザー ID とプロパティを取得する
SharePoint 2013 でユーザー ID とユーザー情報を取得します。
ユーザー ID とユーザー情報を取得するには、取得する目的の情報によってさまざまな方法があります。この記事では、これを行ういくつかの方法について説明します。
  
    
    


## ユーザー ID とプロパティを取得するための前提条件
<a name="Prereq"> </a>


- 「 [SharePoint アドインのオンプレミスの開発環境をセットアップする](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)」に説明されているように、アドイン開発用の環境を整えます。
    
  
- Visual Studio をインストールします。
    
  
- 最新バージョンの  [Office Developer Tools for Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) または [Office Developer Tools for Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015) をインストールします。
    
  

> [!メモ]
> ニーズに合った開発環境をセットアップする方法については、「 [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)」を参照してください。 
  
    
    


### ユーザー ID とプロパティを取得するために知っておく必要がある中心概念

下の表に、SharePoint アドインの作成に関係する概念を理解するために役立つ記事を示します。
  
    
    


|**記事**|**説明**|
|:-----|:-----|
| [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md) <br/> |SharePoint 2013を操作するための SharePoint アドインのアクセス許可について説明します。アドインのアクセス許可の種類、アクセス許可要求範囲、およびアクセス許可の管理が含まれます。この記事では、アドインのアクセス許可権限とユーザー権限の違いについても説明します。  <br/> |
| [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |クラウド ホスト型アドインの OAuth 認証と承認フローについて説明します。  <br/> |
| [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |プロバイダーでホストされる基本的な SharePoint アドインを Office Developer Tools for Visual Studio 2012を使用して作成する方法、SharePoint CSOM を使用して Microsoft SharePoint 2013 サイトと対話する方法、および SharePoint アドインに OAuth を実装する方法について説明します。  <br/> |
   

## 現在の Web サイトのユーザー ID を取得する
<a name="WebsiteUserID"> </a>

Web サイトの現在のユーザーの ID を取得する最も簡単な方法は、 **Web** オブジェクトを使用する方法です。プロジェクトで TokenHelper.cs ファイルを使用して、現在の Web サイトのユーザー ID を取得できます。使用するコード スニペットは次のとおりです。
  
    
    

```cs

ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);
 
 
            //Load the properties for the Web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();
 
            //Get the site name.
            siteName = web.Title;
 
            //Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

```


- Office 365 を使用している場合、取得するログイン名は  `i:0#.f|membership|adam@contoso.com` のようになります。
    
  
- Microsoft SharePoint 2013を社内で使用していて、ユーザーが NTLM を使用して標準ユーザーとしてログインしている場合、取得するログイン名は  `i:0#.w|contoso\\adam` のようになります。
    
  
- SharePoint 2013を社内で使用していて、ユーザーがファーム アカウントを使用してログインしている場合、取得するログイン名は  `SHAREPOINT\\System` です。
    
  

## ResolvePrincipal メソッドを使用してユーザー ID を取得する
<a name="ResolvePrincipal"> </a>

ユーザーのログイン名を取得するもう 1 つの方法を次に示します。ユーザーの電子メール アドレスまたは表示名がわかっている場合は、 [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) メソッドを使用してユーザーのログイン名を取得できます。
  
    
    

> [!メモ]
> API は  [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/ja-jp/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx) アセンブリの Microsoft.SharePoint.Client.Utilities 名前空間にあります。
  
    
    

次のサンプル コードに、ユーザー ログイン情報を取得する方法を示します。
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

 **Person.LoginName** 値がログイン情報を示します。
  
    
    

## ユーザー ID とプロファイル プロパティを取得する
<a name="Profile"> </a>

ユーザーの ID とプロパティを取得するときは、OAuth トークンとソーシャル フィーチャー API を使用できます。最初に OAuth トークンを取得し、そのトークンをクライアント コンテキストに設定します。次のサンプル コードに、ユーザー プロファイル プロパティを取得する方法を示します。
  
    
    

```cs

ClientContext clientContext = new ClientContext(<sharepointurl>);
clientContext.AuthenticationMode = ClientAuthenticationMode.Anonymous;
clientContext.FormDigestHandlingEnabled = false;
clientContext.ExecutingWebRequest +=
delegate(object oSender, WebRequestEventArgs webRequestEventArgs)
{                      
    webRequestEventArgs.WebRequestExecutor.RequestHeaders["Authorization"] =
        "Bearer " + accessToken;
};

```

次に、 [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) API を使用して、アドインを使用中のユーザーのプロパティを取得します。
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

コードを動作させるための条件:
  
    
    

- SharePoint 2013にユーザー用のユーザー プロファイル共有サービスが構成され、同期されている必要があります。
    
  
- ソーシャル フィーチャーに対する次のアクセス許可範囲をアドイン マニフェストに追加する必要があります。
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

これらの API は Microsoft.SharePoint.Client.UserProfiles.dll にあります。
  
    
    
ユーザー プロファイル ストアへのアクセス方法を示すもう 1 つのサンプル コード スニペットを次に示します。
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## その他の技術情報
<a name="AdditionalResources"> </a>


-  [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint アドイン](sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのオンプレミスの開発環境をセットアップする](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

