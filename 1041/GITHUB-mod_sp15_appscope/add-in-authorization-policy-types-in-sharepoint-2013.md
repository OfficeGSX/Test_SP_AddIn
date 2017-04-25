---
title: SharePoint 2013 のアドイン承認ポリシーの種類
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# SharePoint 2013 のアドイン承認ポリシーの種類
SharePoint におけるアドインのさまざまな承認ポリシー (アドイン用ポリシー、ユーザー + アドイン ポリシー、ユーザー用ポリシー) について説明します。また、アドイン用ポリシーの使用ガイドラインも示します。
この記事を読む前に、最初に記事「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」および「 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)」を読んで理解してください。





## アドイン承認ポリシーの概要
<a name="Overview"> </a>

SharePoint には、3 種類の承認ポリシーがあります。




- **ユーザー用ポリシー** ― ユーザー用ポリシーが使用されている場合、SharePoint はユーザーのアクセス許可のみをチェックします。SharePoint がこのポリシーを使用するのは、ユーザーがアドインを使用せずにリソースに直接アクセスしている場合です。たとえば、ユーザーが初めて SharePoint Web サイトのホーム ページを開いたり、PowerShell から SharePoint API にアクセスしたりした場合です。




- **ユーザー + アドイン ポリシー**  ユーザー + アドイン ポリシーが使用されている場合、SharePoint はユーザーとアドイン プリンシパルの両方のアクセス許可をチェックします。承認チェックは、現在のユーザーとアドインの両方が対象のアクションを実行するためアクセス許可を持っている場合にのみ成功します。

    このポリシーが使われる例には、SharePoint 上のユーザーのリソースに SharePoint アドイン がアクセスするケースがあります。この SharePoint アドインのリモート コンポーネント内のコードは、SharePoint に対してユーザー + アドイン用呼び出しを行うように設計する必要があります。




- **アドイン用ポリシー**  アドイン用ポリシーが使用されている場合、SharePoint はアドイン プリンシパルのアクセス許可のみをチェックします。承認チェックは、現在のユーザー (もしある場合) のアクセス許可とは無関係に、現在のアドインが対象のアクションを実行するための十分なアクセス許可を持っている場合にのみ成功します。

    このポリシーを使用する設計になっているアドインの例は、経費承認アドインです。このアドインでは、通常は経費を承認できないユーザーが、特定の金額未満の経費なら承認できます。詳細は、この後のシナリオを参照してください。 



    > **メモ**
      > 一部の API ではユーザー コンテキストが必要であり、これらはアドイン用ポリシーでは実行できません。これらには、Project Server 2013 とやり取りするため、および検索クエリを実行するための多数の API が含まれます。 

### アドイン用ポリシーを使用するアドインのシナリオの例
<a name="Scenario"> </a>

Contoso のセールス マネージャー Adam が、アドイン用ポリシーを使用する経費提出アドインを購入するとします。アドインを購入する際に、Adam は、アドインに対してユーザー アクセス許可の昇格を許可するように、つまりアドインが SharePoint に対するアドイン用呼び出しの実行を許可するように求められます。Adam は要求されたアクセス許可をアドインに付与します。そして、彼は、Contoso セールス チームの全メンバー分のアドインのライセンスを購入し、アドインをセールス チームの SharePoint Web サイトにインストールします。



間もなく、セールス メンバーは新しい経費申請アドインを使用して経費レポートを提出するようになります。通常、セールス メンバーは自分自身の経費レポートを承認できませんが、このアドインを使用すれば承認できます。$50 未満の経費申請を自分自身で承認できるように Adam がアドインに許可を与え、$50 未満のレポートをアドインが自動的に承認するように設定したからです。$50 以上のレポートについては、アドインによって承認タスクが自動的に Adam に割り当てられます。これは、承認された経費の SharePoint リストに対する書き込みアクセス許可を SharePoint アドイン に付与することによって実装できます。しかし、このリストに対する書き込みアクセス許可を持っているのは、さまざまなユーザーの中で、人事マネージャーだけです。アドインのコードは、経費が $50 未満の場合は常に、SharePoint にアドイン用呼び出しを行って経費をリストに追加する設計になっています。ユーザーのアクセス許可が確認されることはないため、$50 未満の申請は、どのユーザーからのものであっても、そのユーザーがリストへの書き込みアクセス許可を持っていないとしても、承認された経費リストに自動的に追加されます。








### アドイン用ポリシーを使用するアクセス許可をアドインが取得する方法
<a name="Approve"> </a>

SharePoint に対してアドイン用呼び出しを行うには、アドインは、アドイン用ポリシーの使用許可を要求する必要があります。この要求は、アドイン マニフェスト内で実行されます。これを行うには、次のマークアップに示すとおり、 **AppPermissionRequests** 要素に **AllowAppOnlyPolicy** 属性を追加し、その属性を **true** に設定します。



```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>```


> **メモ**
> SharePoint アドイン は、かつては「SharePoint 用アプリ」と呼ばれていました。下位互換性を維持するため、アプリのマニフェスト スキーマは変更されませんでした。そのため、"app" という文字列が多くの要素名と属性名に現れています。 




このアドインをインストールするユーザーは、この要求を承認するように求められます。アドインがテナントのスコープでアクセス許可を要求する場合は、テナント管理者のみがアドイン用ポリシーの使用を許可できるため、そのアドインをインストールできるのはテナント管理者のみです。アドインの要求するアクセス許可のスコープがサイト コレクション以下であれば、サイト コレクション管理者がアドインをインストールできます。アクセス許可のスコープの詳細については、「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」を参照してください。




### アドインがアドイン用呼び出しを行う方法
<a name="AppOnlyCalls"> </a>

SharePoint に対するアドイン用呼び出しとユーザー + アドイン呼び出しの違いは、呼び出しに組み込まれるアクセス トークンの種類の違いです。次のコードは、マネージ コードの中でユーザー + アドイン用およびアドイン用のアクセス トークンを取得する方法を示しています。詳細なコーディングは、Visual Studio の プロジェクトに Office Developer Tools for Visual Studio によって自動的に追加される TokenHelper.cs (または .vb) ファイルの中に記述されます。



```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+add-in access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...

      //Get add-in-only access token.
       string addinOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}```


> **メモ**
> OAuth 認証された呼び出しを行わないアドイン (たとえば、アドイン Web で動作する単なる JavaScript であるアドイン) では、アドイン用ポリシーを使用できません。これらのアドインでは、アクセス許可を要求することはできますが、そのためにはアドイン用 OAuth トークンを渡す必要があるため、アクセス許可を利用できません。SharePoint の外部で動作する Web アプリケーションが含まれるアドインだけが、アドイン用トークンを作成して渡すことができます。 




通常は、呼び出しを行うには、現在のユーザーが存在している必要があります。アドイン用ポリシーの場合、既存の SHAREPOINT\\SYSTEM ユーザーに似た SHAREPOINT\\APP が SharePoint によって作成されます。すべてのアドイン用の要求は SHAREPOINT\\APP によって実行されます。ユーザーベースの認証を通じて SHAREPOINT\\APP として認証することはできません。




### アドイン用ポリシーを使用するためのガイダンス
<a name="GuidelinesFor"> </a>

アドイン用呼び出しは実質的にユーザーの特権を昇格するものであるため、アドイン用呼び出しを行うためのアクセス許可を要求するアドインを作成するかどうかは慎重に判断してください。呼び出しでアドイン用ポリシーを使用するのは、次の場合に限る必要があります。




- 特定の呼び出しについて、ユーザーのアクセス許可よりも高いアクセス許可までアドインを昇格する必要がある場合 (たとえば、アドインによって評価された条件で経費報告書を承認するため)。


- アドインが特定のユーザーの代わりとして機能しない場合。たとえば、SharePoint ドキュメント ライブラリの夜間保守作業を実行するアドイン。



## その他の技術情報
<a name="AR"> </a>


-  [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)


-  [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)


-  [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)


-  [SharePoint アドイン](sharepoint-add-ins.md)


-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)



