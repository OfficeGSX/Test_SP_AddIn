---
title: SharePoint の高信頼アドインのトラブルシューティング
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# SharePoint の高信頼アドインのトラブルシューティング
高信頼 SharePoint アドイン 開発の問題に関するいくつかのヘルプを表示する
この記事では、Fiddler ツールについて説明し、いくつかの特定の問題を解決するためのガイダンスも提供します。
  
    
    


## Fiddler ツールを使用する

無料の  [Fiddler ツール](http://www.telerik.com/fiddler)を使用して、アドインのリモート コンテンツから SharePoint に送信される HTTP 要求をキャプチャできます。要求のアクセス トークンを自動的にデコードする [ツールの無料の拡張機能](https://github.com/andrewconnell/SPOAuthFiddlerExt)があります。
  
    
    
Web アプリケーション サーバーに Fiddler をインストールしたら、次のマークアップを web.config ファイルに追加して、リモート Web アプリケーションからの要求がこのプロキシを経由するようにします。この方法で、Fiddler のトレースをキャプチャして、エラーが発生したときの SharePoint からの完全な応答を確認できます。
  
    
    

> **メモ**
> Fiddler を実行しないときは、忘れずにこのマークアップを削除してください。このマークアップを削除しないと、アドインが HTTP 要求を実行できません。 
  
    
    



```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>
```

Fiddler をインストールしたら、要求の GUID が含まれる SharePoint からの応答ヘッダーもチェックできます。この要求 GUID は関連付け ID であり、これをログ内で検索することで、その要求に関連するログ エラーがあれば見つけることができます。
  
    
    

## 401 認証エラー
<a name="UnauthorizedException"> </a>

高信頼アドインが最初に SharePoint にアクセスするとき、 **401 Unauthorized** エラーを引き起こす可能性があるものがいくつかあります。クライアント側オブジェクト モデル (CSOM) を使用している場合、エラーは次のようになります。
  
    
    
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
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178```

TokenHelper ファイルと Windows ID を使用している場合は、例外をトリガーするコードは次のようになります。
  
    
    


```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();```

この問題のトラブルシューティングを行うには、まず Visual Studio デバッガーを使用して、アクセス トークンと **ClientContext** オブジェクトが正常に構築されていることを確認します。正常に構築されている場合は、次の可能性を検討します。
  
    
    
 **考えられる問題と解決策:**
  
    
    

- リモート Web アプリケーションにアクセスしているユーザーのユーザー プロファイルが作成されていません。ユーザー プロファイルを作成します。
    
  
- アクセスしようとしているリソースに対するアクセス許可を、アドインが持っていません。SharePoint 管理シェル を開き、次の Windows PowerShell コマンドレットを実行します。変数  `$web` はアクセスしようとしている SharePoint の Web サイト、 `$appPrincipal` はアドイン ID です。詳細については、「 [Set-SPAppPrincipalPermission](http://technet.microsoft.com/ja-jp/library/jj219714%28v=office.15%29.aspx)」を参照してください。
    ```
  
Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl```

- ご使用の Web アプリケーションが匿名の要求を受け入れています。これは、アクセス トークンに実際のユーザー ID が含まれないことを意味します。IIS で、リモート Web アプリのルート ディレクトリに対して匿名アクセスが無効になっていることを確認します。これは、リモート Web アプリケーションをデバッグし、default.aspx.cs (または .vb) ファイルの **Request.LogonUserIdentity** 値を確認して、匿名ユーザーでないことを確認することでもチェックできます。
    
  
- デジタル証明書が、信頼された証明書ストアに追加されませんでした。 [高信頼 SharePoint アドインをパッケージ化して発行する](package-and-publish-high-trust-sharepoint-add-ins.md) の手順に従っていることを確認します。
    
  

## その他の SSL およびドメイン関連の認証エラー
<a name="DomainRelatedErrors"> </a>

設定ファイルと登録フォームのドメイン名の不一致により、認証できない場合があります。次の 4 つの値が完全に同一でなければなりません。
  
    
    

- SharePoint アドインが AppRegNew.aspx で登録されるときに指定された [ **アドイン ドメイン**]。
    
  
- リモート Web アプリケーションのセキュリティ証明書が登録されているドメイン。
    
  
- AppManifest.xml ファイルの **StartPage** 値のドメイン部分。
    
  
- AppManifest.xml で指定されたイベント レシーバーの URL のドメイン部分。
    
  
この点に関連して、次のことに注意してください。
  
    
    

- SharePoint アドイン のリモート コンポーネントが、443 以外のポートを使用している場合、4 つの場所のすべてでドメインの一部として明示的にポートを含める (たとえば、 `MarketingServer:3333`) 必要があります。(HTTPS プロトコルを使用する必要がありますが、この既定のポートは 443 です。)
    
  
- アドインをパッケージ化する前に、ドメインを AppManifest.xml ファイルの **StartPage** の値 (およびイベント レシーバーの URL) でハードコーディングする必要があります。Visual Studio で **発行**ウィザードを使用してアドインをパッケージ化する場合、ドメインを入力するよう促すメッセージが表示され、入力された値が Office Developer Tools for Visual Studio によって (デバッグ中に使用される  `~remoteWebUrl` トークンの代わりに) **StartPage** 値に挿入されます。ただし、 **発行**ウィザードを使用していない場合は、手動でトークンをドメイン (およびプロトコル) に置き換える必要があります。例えば、 `https://MarketingServer` や `https://MarketingServer:3333` です。
    
  

## 対象のシリアル番号の証明書がないことを示す実行時エラー
<a name="DomainRelatedErrors"> </a>

web.config に正しい証明書シリアル番号が含まれていて、 **Windows 証明書ストア** でその証明書を確認できる場合には、web.config のシリアル番号に非表示の余分な文字が存在する可能性があります。シリアル番号を **Microsoft 管理コンソール** からコピーして貼り付けると、この事態が生じます。web.config からシリアル番号値全体を削除し、 *手で*  再入力してください。
  
    
    

