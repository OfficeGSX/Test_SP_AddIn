---
title: SharePoint のプロバイダー向けのホスト型低信頼アドインでセキュリティ トークンを処理する
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# SharePoint のプロバイダー向けのホスト型低信頼アドインでセキュリティ トークンを処理する
低信頼の承認に使用するコンテキスト トークン、アクセス トークン、および更新トークン、ならびにプロバイダー向けのホスト型 SharePoint アドイン、およびコード内でそれらを操作する方法について説明します。
> **重要**
> **この記事全体では低信頼の承認システムでのセキュリティ トークンの使用についてのみ説明し、高信頼システムについては取り上げません。** 高信頼システムでのトークンの使用については、「 [SharePoint のプロバイダー向けのホスト型高信頼アドインでアクセス トークンを作成して使用する](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)」を参照してください。 




 **SharePoint データにアクセスするために  [低信頼の承認システム](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) を使用する SharePoint アドイン は、SharePoint、Microsoft Azure アクセス制御サービス (ACS)、SharePoint アドインのリモート コンポーネント、および、場合によってはユーザーのブラウザーの間で ( [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/) 形式で) 受け渡しされるセキュリティ トークンが関係する OAuthフロー に参加します。** アドインの設計に応じてさまざまなフローがありますが、すべてのフローには少なくとも次の 2 種類のトークンが関係します。
- **アクセス トークン:** アドインのリモート コンポーネントから SharePoint への作成、読み取り、更新、削除 (CRUD) のそれぞれの要求に組み込まれます。SharePoint はトークンを検証し、要求に応じます。


- **更新トークン:** [コンテキスト トークン フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) で最初にアクセス トークンを取得するために使用します。また、低信頼の承認システムのコンテキスト トークン フローおよび [認証コード フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) の両方で、有効期限の切れたアクセス トークンを置き換えるために使用します。


アドインが使用している OAuth フローに応じて、次のいずれかもプロセスに含まれます。
- **コンテキスト トークン:** コンテキスト トークン フローで、リモート コンポーネントに更新トークンを提供し、Azure ACS からアクセス トークンを要求するために必要な情報を提供するために使用します。


- **認証コード:** 認証コードは、トークンではありませんが、ユーザーとアプリケーションのペアごとに一意です。認証コード フローで最初のアクセス トークンと更新トークンを取得するために使用します。



## アクセス トークンの処理について理解する
<a name="AccessTokens"> </a>

低信頼の承認システムでは、アクセス トークンは Azure ACS によって作成され、SharePoint アドインのリモート コンポーネントに送信されます。(この記事の執筆時点で、SharePoint に対して ACS が発行するアクセス トークンの有効期間は 12 時間ですが、これは変更される可能性があります。) **SharePoint アドイン内のコードで実行する必要のある処理** は、主として次のとおりです。




- Azure ACS から **アクセス トークンを要求します** 。使用している OAuth フローに応じて、アドインは認証コードか、コンテキスト トークンから抽出した情報のいずれかを使用して要求を行います。


- **SharePoint に対するすべての HTTP 要求にトークンを組み込みます** 。トークンは、要求に対する **Authorization** ヘッダーとして追加されます。ヘッダーの値は、"Bearer" という語、その後にスペース 1 つ、その後に Base 64 エンコードのアクセス トークンを続けたものです。


- 省略可能の (しかし推奨される) 処理として、後続の要求で再使用できるように **アクセス トークンをキャッシュします** 。


- 省略可能な処理として、アクセス トークンをバック エンド システムに転送して、SharePoint に直接アクセスできるようにします。


これらのタスクはサーバー側のコードで実行する必要があります。マネージ コードを使用している場合は、これらのタスクのいくつかに関するサンプル コードが、Microsoft Office Developer Tools for Visual Studio に付属の SharePointContext.cs (または .vb) および TokenHelper.cs (または .vb) ファイルに含まれています。これらのタスクのいくつかを実行する PHP コードの例については、「 [SharePoint 2013 REST インターフェイスを理解して使用する](http://msdn.microsoft.com/ja-jp/magazine/dn198245.aspx)」を参照してください。




### アクセス トークンをキャッシュする
<a name="CacheAccessToken"> </a>

SharePoint アドインのアーキテクチャとホストするプラットフォームに応じて、サーバー上で **アクセス トークンをキャッシュする方法** がいくつかあります。




- セッション状態に


- アプリケーション状態に


-  [Windows Server AppFabric キャッシュ](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx) または Microsoft 以外の OS でそれに相当するキャッシュに


-  [Microsoft Azure キャッシュ サービス](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx) または Microsoft 以外のクラウド サービスでそれに相当するキャッシュに


- データベースに


-  [memcached](http://www.memcached.org/) システムに



> **メモ**
> ほとんどのシナリオで、キャッシュ キーとして "AccessToken" などの単純な語句を使用することはできません。アドインでは、異なるユーザーや異なる SharePoint ファーム/テナンシーごとにトークンを区別して保持する必要があるからです。アドインで [コンテキスト トークン フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)を使用する場合は、SharePoint から提供される特別な **CacheKey** を使用してキャッシュ トークンを区別できます。このセクションでは、この問題の詳細と、アプリケーションでコンテキスト トークン フローを使用しない場合の対処方法について説明します。




セッション状態にアクセス トークンをキャッシュすることは、ほとんどのシナリオで問題になりません。リモート Web アプリケーションが OAuth を使用する他のサービスに (SharePoint に加えて) アクセスしており、さまざまなアクセス トークンをセッション状態にキャッシュしている場合には、トークンごとにキャッシュ キーを区別する必要があります。たとえば、"AccessToken" ではなく、"SharePoint_AccessToken"、"Facebook_AccessToken"、"SAP_Gateway_AccessToken" などを使用します。(セッション状態を使用していない場合、または各ユーザーのキャッシュを自動的に分離する他の種類のキャッシュを使用していない場合は、ユーザーごとにキーを区別する必要もあります。)



アプリケーションから複数の SharePoint ファームまたはオンライン テナンシーにアクセスする場合は、アプリケーションのプライマリ キャッシュ キーの一部として SharePoint ドメインを使用する ("SharePoint_ *<mydomain>*  .sharepoint.com_AccessToken") か、ファーム/テナンシーの領域を使用する ("SharePoint_ *<realmGUID>*  _AccessToken") ことができます。どちらの情報もアクセス トークンから読み取れます。(それには、読み取るトークンの Base 64 エンコードを逆変換する必要があります。マネージ コードの場合は、この目的に使用できるサンプル コードが TokenHelper.cs または .vb ファイルに含まれています。そのコードでは、 **Microsoft.IdentityModel.S2S.Tokens** および **System.IdentityModel.Tokens** の各名前空間に属するクラスを使用します。) アプリケーションがコンテキスト トークン フローを使用している場合に使用可能なもう 1 つの方法について、次の段落で説明します。



一部のシナリオでは、アプリケーションがセッションをまたいで、またはセッションの終了後に使用可能な場所にアクセス トークンをキャッシュする必要があります。たとえば、ユーザーがアプリケーションを閉じた後に、スケジュールされたアクションをユーザーが実行できるようにアプリケーションを設計する場合などです。そのアクションに SharePoint をアクセスする操作が含まれている場合は、アドインがアクセス トークンを取得できる必要があります。この種類のシナリオでは、 **アプリケーションは異なるユーザーのアクセス トークンを区別して保持する必要があります** 。コンテキスト トークン フローを使用している場合は、SharePoint アドインの起動時に SharePoint からアドインのリモート コンポーネントに渡されるコンテキスト トークンに入れてその目的のためのキャッシュ キー文字列が提供されます。この **特別なキャッシュ キー** の詳細とその使用方法については、「 [キャッシュ キーについて理解する](#CacheKey)」を参照してください。この文字列は、前の段落で説明したシナリオにも使用できます。スケジュール システムに作業の実行日時や内容などの構成データを格納する手段があれば、その保存スペースにアクセス トークンのキャッシュ キーを保持できます。



セッションをまたぐキャッシュを複数のアプリケーション間でも共有する場合は、キャッシュ キーを、ユーザーおよび SharePoint 領域についてだけでなくアプリケーションについても区別する必要があります。コンテキスト トークンに入れて提供されるキャッシュ キーは、ユーザーおよび SharePoint 領域についてだけでなくアプリケーションについても一意です。



 **アプリケーションで認証コード フローを使用している場合は** 、コンテキスト トークンがないため、特別に生成されるキャッシュ キーもありません。そのシナリオでは、 **キャッシュするデータに対してキーを生成する独自の体系を開発者が自分で作成する必要があります** 。このキーは、アプリケーションのユース ケースに基づいて名前の衝突が起きる状況を検討し、ユーザー、SharePoint 領域、およびアプリケーションのうち該当するものを区別できるように生成する必要があります。この目的のためには、アクセス トークン内のクレームを利用できます。たとえば、 **nameId** と **aud** を使用します (下記の表を参照)。作成するコードでは、これらの文字列を単に連結するか、これらの文字列をシードにして固有のハッシュを生成し、キャッシュ キーとして使用できます。



最後に、アプリケーションで SharePoint に対してアドインのみの呼び出しとユーザー + アドイン呼び出しの両方を使用する場合は、2 つの異なるアクセス トークンが必要になります。したがって、キャッシュ キーを区別する必要があります。この場合、基本のキャッシュ キーが決まっていれば、単に "_add-in-only" または "_add-in+user" を追加するだけで十分です。




> **注意**
> **アクセス トークンを Cookie に格納するのは安全ではありません。** 通常、アクセス トークンをブラウザー経由で受け渡しすることは避けるべきです。





### アクセス トークンをバックエンド システムに転送する
<a name="ForwardTokenToBackend"> </a>

SharePoint アドインでは、リモート Web アプリケーションと同じドメインでホストされていないバックエンド サーバーを持つことができます。バックエンド サーバーが SharePoint 上で CRUD 操作を実行する必要がある時は、バックエンド システムから直接 SharePoint を操作したほうがアドインの動作が速くなります。この場合、ドメインが問題になるのはアプリケーションが ACS からアクセス トークンを取得する時だけです。トークンを取得した後は、そのトークンをバックエンド サービスに転送すれば、バックエンド サービスはそのトークンを使用して SharePoint を呼び出すことができます。これらのシステムのコードでは、アクセス トークンの有効期限切れを処理する必要があり、実際に ACS に登録している親 Web アプリケーションに新しいアクセス トークンを要求する必要があります。「 [有効期限の切れたアクセス トークンの処理](#Expired)」を参照してください。この技法は、アプリケーション自体のバック エンドサーバーに限って使用し、外部 Web サービスに対しては使用しないでください。また、この技法を利用するときは、可能な場合にバックエンド サービスでアドインのみの呼び出しを使用することを検討してください。




### 有効期限の切れたアクセス トークンの処理
<a name="Expired"> </a>

アクセス トークンの有効期限は数時間で切れます (この記事の執筆時点では 12 時間ですが、変更される可能性があります)。アクセス トークンの有効期限が切れた後にもアプリケーションが SharePoint にアクセスすると、有効期限が切れた後に SharePoint が最初に要求を受け取った時点で **401 Unauthorized** エラーが発生します。コードでは、この応答を処理する必要があります。あるいは、アクセス トークンを使用する前にコードでその有効期限をテストすることもできます。 **ACS から別のアクセス トークンを取得するには、コードで更新トークンを使用してください** 。コンテキスト トークン フローの場合は、SharePoint との最初のセッションの開始時にアドインが SharePoint から受け取るコンテキスト トークンの中に更新トークンが含まれています。認証コード フローの場合、更新トークンは最初のアクセス トークンと一緒にアプリケーションに渡されます。必要なときに使用できるようにするため、更新トークンをキャッシュする必要があります。更新トークンの有効期限は数か月で、Cookie またはサーバー側ストレージの中に保持できます。詳細については、「 [更新トークンの処理とキャッシュについて理解する](#RefreshToken)」を参照してください。




### アクセス トークンの例
<a name="ExampleAccessTokens"> </a>

このセクションでは、アクセス トークンについて例を挙げて説明し、使用している承認ポリシーに応じてアクセス トークンがどのように異なるかを説明します。




#### 低信頼の承認システムのアクセス トークンについて

 **ユーザー + アドイン ポリシー**



次に示すのは、 [ユーザー + アドイン ポリシー](add-in-authorization-policy-types-in-sharepoint-2013.md) を使用した SharePoint の呼び出しで使用するために **ACS によって生成されるユーザー + アドイン呼び出しのアクセス トークンをデコードした例** です。読みやすくするために空白を追加してあります。このトークンは、 [JSON Web トークン](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1) プロトコルに準拠しています。トークン内の JavaScript Object Notation (JSON) オブジェクトは、クレーム セット と呼ばれます。クレーム セットのプロパティの詳細については、表 1 を参照してください。すべての値を小文字にする必要があることに注意してください。(ユーザー + アドイン呼び出しのアクセス トークンは、 [コンテキストトークン フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) と [認証コード フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) で同じです。)





```

{
 "aud": "00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss": "00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf": 1377549246,
 "exp": 1377592446,
 "nameid": "2303000085ff9abc",
 "actor": "964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73",
 "identityprovider": "urn:federation:microsoftonline"
}```


**表 1: ACS から発行されるユーザー + アドイン呼び出しのアクセス トークンのクレーム**


|**クレーム**|**説明**|**サンプルのアクセス トークンでの対応する値**|
|:-----|:-----|:-----|
| `aud` <br/> |"audience" の省略形で、トークンの対象として意図されるプリンシパルを意味します。形式は、 _audience principal ID_/ _SharePoint ドメイン_@ _SharePoint 領域_ で、このうち _audience principal ID_ は SharePoint の永続的なセキュリティ プリンシパル ID です。(「 [Microsoft 製品のアプリケーション プリンシパル定数](http://msdn.microsoft.com/ja-jp/library/hh629982%28v=office.12%29.aspx)」を参照してください。)  <br/>  _SharePoint 領域_ は、アクセス トークンを使用してアクセスされる SharePoint Online テナンシーまたは内部設置型の SharePoint ファームの GUID です。この GUID は、トークンの発行者 (この場合は Azure ACS) のために領域の ID として機能します。 <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |"issuer" の省略形。トークンを作成したプリンシパルを表します。形式は、 _発行者 GUID_@ _SharePoint 領域 GUID_ です。 <br/> 低信頼の承認システムでは、発行者は Azure ACS で、その GUID は **00000001-0000-0000-c000-000000000000** です。 <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |"not before" の省略形で、トークンが有効になった *開始*  日時 (1970 年 1 月 1 日午前 0 時からの秒数) を表します。既定では、トークンが作成された時点の日時です。詳細については、「 [JWT 時刻値の取り扱い](#JWTtimes)」を参照してください。  <br/> |1377549246  <br/> |
| `exp` <br/> |"expiration" の省略形で、トークンの有効期限が切れる日時を表します。既定では、 **nbf** の日時の 12 時間後です。詳細については、「 [JWT 時刻値の取り扱い](#JWTtimes)」を参照してください。  <br/> |1377592446  <br/> |
| `nameid` <br/> |トークンの発行対象ユーザーを表す一意識別子です。形式は ID プロバイダーに応じて異なります。この例ではプロバイダーは Microsoft Online です。プロバイダーが Active Directory であれば、ID は "s-1-5-21-2127521184-1604012920-1887927527-415149" のようになります。  <br/> |2303000085ff9abc  <br/> |
| `actor` <br/> |SharePoint ファームまたはテナンシーに対してアクセスを要求するプリンシパルで、 _アプリケーションのクライアント ID_@ _SharePoint 領域_ という形式です。 <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `identityprovider` <br/> |Internet Assigned Numbers Authority (IANA) に登録されている ID プロバイダーの一意の名前です。  <br/> SharePoint Online にインストールしたアドインでは、この値は通常この例と同じです。  <br/> オンプレミス ファームにインストールしたアドインでは、通常はオンプレミスの ID プロバイダーです ("urn:office:idp:activedirectory" など)。  <br/> |urn:federation:microsoftonline  <br/> |
 
 **アドインのみのポリシー**



次に示すのは、 [アドインのみのポリシー](add-in-authorization-policy-types-in-sharepoint-2013.md) を使用した SharePoint の呼び出しで使用するために **ACS によって生成されるアドインのみの呼び出しのアクセス トークンをデコードした例** です。読みやすくするために空白を追加してあります。このトークンは、 [JSON Web トークン](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1) プロトコルに準拠しています。クレーム セットのプロパティの詳細については、表 2 を参照してください。(アドインのみのポリシーは、 [認証コード フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)を使用するアプリケーションでは使用できません。その種のアプリケーションにはアドイン マニフェスト ファイルがないため、アドインのみのポリシー呼び出しのアクセス許可を要求できません。)





```

{
 "aud":"00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":1403304705,
 "exp":1403347905,
 "nameid":"c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73",
 "sub":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "oid":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "trustedfordelegation":"false",
 "identityprovider":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73"
}```


**表 2: ACS から発行されるアドインのみの呼び出しのアクセス トークンのクレーム**


|**クレーム**|**説明**|**サンプルのアクセス トークンでの対応する値**|
|:-----|:-----|:-----|
| `aud` <br/> |前述のユーザー + アドイン呼び出しのトークンと同じ。  <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |前述のユーザー + アドイン呼び出しのトークンと同じ。  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |前述のユーザー + アドイン呼び出しのトークンと同じ。  <br/> |1403304705  <br/> |
| `exp` <br/> |前述のユーザー + アドイン呼び出しのトークンと同じ。  <br/> |1403347905  <br/> |
| `nameid` <br/> |ユーザーではなくアドインの一意識別子 (アドインのみのポリシーではユーザーの ID は考慮に入れられないため)。形式は  _クライアントID_@ _SharePoint 領域_ です。 <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `sub` <br/> |"subject" の省略形で、トークンのサブジェクトです。これは、SharePoint へのアクセスを要求しているプリンシパルです (この場合は、リモート Web アプリケーション) 。この値には、オブジェクト ID が使用されます。後述する **oid** クレームを参照してください。 <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `oid` <br/> |"object ID" の省略形で、リモート Web アプリケーションの Azure Active Directory でのオブジェクト ID です。アドインのみの呼び出しのアクセス トークンでは、サブジェクトとオブジェクト ID は同じ値です。  <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `trustedfordelegation` <br/> |ユーザーを認証および承認するために SharePoint が SharePoint アドインを信頼してよいかどうかを示すブール値。アドインのみの呼び出しでは、ユーザー ID が考慮に入れられないため、この値は false です。  <br/> |false  <br/> |
| `identityprovider` <br/> |ID プロバイダーの一意の名前。ユーザーではなくアドインの ID が提供されるので、ACS が ID プロバイダーです。形式は、 _ACS GUID_@ _SharePoint 領域_ です。 <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
 

## コンテキスト トークンの構造と処理について理解する
<a name="ContextTokens"> </a>

コンテキスト トークンは、低信頼の承認システムの  [コンテキスト トークン フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) 内でのみ使用されます。 **SharePoint アドインが SharePoint 内で起動すると、SharePoint は Azure ACS にコンテキスト トークンを作成することを要求します。その後、SharePoint はそのコンテキスト トークンを SharePoint アドインのリモート コンポーネントに渡します。** トークンは、 **SPAppToken** という非表示のフォーム パラメーターとして SharePoint からリモート コンポーネントの開始ページに対する要求に入れて渡されます。トークンは、ACS と SharePoint アドインだけが知っているクライアント シークレットで署名されます。 **コンテキスト トークンには** 、ACS から **アクセス トークンを要求するためにアドインが使用する更新トークン** とその他の情報が組み込まれています。(この記事の執筆時点で、SharePoint に対して ACS が発行するコンテキスト トークンの有効期間は 12 時間ですが、これは変更される可能性があります。) **SharePoint アドイン内のコードが実行する主要なタスク** は次のとおりです。




- アドインのクライアント シークレットを使用して **コンテキスト トークンを検証する** 。


- **コンテキスト トークンをキャッシュする** 。またはコンテキスト トークンに含まれる更新トークンとその他の項目を抽出して別個にキャッシュする。


- 更新トークンとその他の情報を使用して ACS から **アクセス トークンを要求する** (その後、アクセス トークンそのものをキャッシュする)。


- コンテキスト トークンから **CacheKey をキャッシュする** 。



> **重要**
> 上記のうち最初の 2 つのタスクは、ユーザーが別のページにナビゲートする前、またはページが更新される前、またはトークンが失われる前に実行する必要があります。たとえば、ASP.NET Web フォーム アプリケーションであれば、これらのタスクを **Page_Load** メソッド内 (要求がポスト バックではない場合にのみ実行する条件付きコード ブロック内) で実行することを検討してください。ASP.NET MVC アプリケーションであれば、これらのタスクを既定のコントローラー メソッド内で実行することを検討してください。




マネージ コードを使用している場合は、これらのタスクのいくつかに関するサンプル コードが、Microsoft Office Developer Tools for Visual Studio に付属の SharePointContext.cs (または .vb) および TokenHelper.cs (または .vb) ファイルに含まれています。TokenHelper クラスのメンバーを単純に呼び出すだけで済みます。たとえば、次のような 1 行のコードで、上記のうち最初のタスクを実行することができます。





```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);```

これらのタスクのいくつかを PHP で実行する方法に関する例については、サンプル「 [SharePoint 2013: PHP サイトから SharePoint ドキュメント ライブラリ上で操作を実行する](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)」をご覧ください。




### コンテキスト トークンまたはその一部をキャッシュする
<a name="CacheContextToken"> </a>

 **キャッシュ** できるのは、コンテキスト トークン全体、またはアクセス トークンを取得するためにコードで使用するコンテキスト トークン内の更新トークンおよびその他の項目です。 **サーバー側とクライアント側のどちらにも格納できます** 。簡潔にするため、この記事ではコンテキスト トークンを一体としてキャッシュすることを想定して説明します。




> **重要**
> これは非常に重要な点なので繰り返します。 *アクセス*  トークンのためにクライアント側のキャッシュは使用しないでください。クライアント側のキャッシュは、コンテキスト トークンのために使用する場合に限って安全です。




 [サーバー側のキャッシュ オプション](#CacheAccessToken)としては、前述のアクセス トークンの場合と同じものを使用できます。クライアント側のオプションには、Cookie や HTML ページ上の非表示のフォーム フィールドがあります。別のオプションとしてセッション キャッシュにコンテキスト トークンを格納することもできますが、コンテキスト トークン内から取得した **CacheKey** はクライアント上に格納してください。



セッションが終わった後にアプリケーションが SharePoint にアクセスする場合は、セッション キャッシュやクライアント側キャッシュを利用できません。セッション後のタスクを実行するとき、元のアクセス トークンが有効期限切れになった場合に、アプリケーションが更新トークンを使用する必要があるからです。このシナリオでは、複数のユーザー、SharePoint 領域、アプリケーションで共有する持続的な (セッションをまたぐ) キャッシュが必要になります。そのため、コードでは、以前に「 [アクセス トークンをキャッシュする](#CacheAccessToken)」で説明したものと同じ、ユーザー/SharePoint 領域/アプリケーションを区別するキャッシュ キーを使用する必要があります。この目的のために、アクセス トークンの場合に使用したのと同じように、コンテキスト トークンに含まれる **特別なキャッシュキーを使用する** ことができます。(この後の「 [キャッシュ キーについて理解する](#CacheKey)」を参照してください。) 




#### キャッシュ キーについて理解する
<a name="CacheKey"> </a>

 **キャッシュ キーは、ユーザー、ユーザー名の発行者、アドイン、および SharePoint ファームまたは SharePoint Online テナントの組み合わせに対して一意の不明瞭な文字列です。** 暗号化する前のこの文字列の形式は次のとおりです ( _領域_ はオンプレミスの SharePoint ファームまたは SharePoint Online テナンシーの GUID)。



 _UserNameId_ + "," + _UserNameIdIssuer_ + "," + _ApplicationId_ + "," + _領域_



キャッシュ キーには、サイト URL 情報が含まれていません。各 SharePoint Online テナント (または内部設置型の SharePoint ファーム) の領域は一意であるため、キャッシュ キーはユーザー名、ユーザー名の発行者、アプリケーション、およびファームの各組み合わせに対して一意になります。後述するサンプルのコンテキスト トークンでは、暗号化したキャッシュ キー値は次のとおりです。



 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`



アプリケーションは複数のアイテムをキャッシュできるため、同じキャッシュ内に同じキャッシュ キーでアクセス トークンとコンテキスト トークンの両方が格納されることがあります。このため、 **キャッシュ キーを語幹として使用し** 、その前か後に "AccessToken" や "ContextToken" などの特定の文字列を付加して完全なキャッシュ キーを作成することを検討してください。 **別のオプションとしては、クラスを作成し** 、キャッシュする必要のあるさまざまな事項を表すプロパティを付け、その型のオブジェクトをキャッシュする方法があります。 **3 つ目のオプション** は、キャッシュとして **データベースを使用している** 場合に、CacheKey 列のあるテーブルを使用し、キャッシュするアイテム (AccessToken、ContextToken、その他) ごとに追加の列を使用する方法です。



もちろん、アプリケーションでは、キャッシュするすべてのアイテムに対して同じキャッシュを使用する必要はありません。一般には、アクセス トークンをセッション キャッシュに格納し、コンテキスト トークン (またはその中にある更新トークン) をデータベースに格納し、CacheKey を Cookie に格納するという方法がよく使用されます。




### コンテキスト トークンを使用してアクセス トークンを取得する
<a name="UseContextTokenToGetAccessToken"> </a>

 **アクセス トークンを取得するには、アプリケーションから直接 ACS に要求を送信します。** この要求には、コンテキスト トークンから抽出する次の 3 つの情報 (およびその他の情報) を含めます。




- 更新トークン。


- SharePoint のアプリケーション プリンシパル GUID。


- アドインが要求しているアクセス先の SharePoint ファームまたは SharePoint Online テナンシーの領域 のGUID。


TokenHelper.cs (または .vb) ファイルに、この要求を作成するコードが含まれています。この処理を行う PHP コードの例については、「 [SharePoint 2013: PHP サイトから SharePoint ドキュメント ライブラリ上で操作を実行する](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454)」を参照してください。



アプリケーションは、SharePoint テナンシーまたはファームの領域を、コンテキスト トークンを解析して取得する代わりに、実行時に取得できます。マネージ コードを使用している場合は、領域を取得するための  `TokenHelper.GetRealmFromTargetUrl` メソッドがあります。その値を再び取得するためにコードからもう一度ネットワーク呼び出しを行う必要が生じないようにするため、この値をキャッシュするようにしてください。




### 新しいコンテキスト トークンを取得する
<a name="GetNewContextToken"> </a>

 **新しいコンテキスト トークンが必要な場合** (通常は、コンテキスト トークンに含まれている更新トークンが有効期限切れになった場合) は、 **コードで新しいコンテキスト トークンを取得するためにすべての SharePoint Web サイトで特別なページ (AppRedirect.aspx) にブラウザーをリダイレクトする** ことができます。このページの URL に次の 2 つのクエリ パラメーターを付加する必要があります。




-  `client_id`: SharePoint アドインのクライアント ID。


-  `redirect_uri`: 新しいコンテキスト トークンを取得した後にブラウザーをリダイレクトする先の URI。SharePoint はこの URI にコンテキスト トークンを POST します。通常これは、新しいコンテキスト トークンを要求したのと、同じページ、コントローラー メソッド、または Web サービス メソッドです。値は URL エンコードする必要があります。


URL の構成は次のとおりです。





```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>```

次に示すのは、TokenHelper ファイルを使用する ASP.NET での要求の例です。





```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));```


### コンテキスト トークンの実例
<a name="ExampleContextToken"> </a>

次に、コンテキスト トークンの例を示します。先頭の小さな JavaScript Object Notation (JSON) オブジェクトには、トークンに関するメタデータが含まれています。これらのプロパティは、前述のアクセス トークンの場合と同じです。 **alg** プロパティの値は、ACS がトークンに付加する署名を生成するために使用されるアルゴリズムの名前です。トークンのペイロードに含まれるプロパティの詳細については、表 3 を参照してください。すべての値を小文字にする必要があることに注意してください。(読みやすくするために空白を追加しました。)



```
{"typ":"JWT","alg":"HS256"}
.
{
 "aud":"a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":"1335822895",
 "exp":"1335866095",
 "appctxsender":"00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "appctx":"{
            \\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\",
            \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"
           }",
 "refreshtoken":"IAAAAC1Lv5w0OrcFAmJx0xk6aaBdhgsw3VPnPzNEDAWypTHtCYytZ2/dBBUKj+HLK8YB3IUCUfDxYpAque
NHKtgs4rYJJ5AegQpNMOJR1yYK8ngivQx0oetj7aSPuGVb+k6at6G0Kx5LZ5vhxkAq8iUSwu8p4L2cvNMzDF1mDKfMivqxgrIZkr2nbf9as0SJFL6VG5hZnDE4HKq
xJnejSW3umatKM4fsfY1MClVCxrkXb2EQ8H/TmwaJc388YW063GEVUS/3BTSgSIRBKQUmXJuJ6BZY7WTm84LaGrx3mIjnUTM/jnqPoPG55JbCC9sS/MeGNPtzPPCDg
6Vv7dVhQ1Dq5Y3fQ65e9LpJ580jCgzYYvpIFT+Wx5V+17mjY2T8wug04K2ts87Znsr+GfFCorf7NS/lj5HjoxRAQ2tva/8dwguSLwxcUwi/Q9MbpR0NNtlpwVazqi9O
hJ4Df7gVhUDdJ0Dtc6aFCPbl5ZLDDRs42xK2", 
 "isbrowserhostedapp": "true"
}```

 **aud** 、 **iss** 、 **nbf** 、および **exp** クレームは、前述のアクセス トークンの場合とまったく同じです。 **appctxsender** 、 **appctx** 、 **CacheKey** 、 **SecurityTokenServiceUri** 、 **refreshtoken** 、および **isbrowserhostedapp** クレームについては、次の表で説明します。




**表 3. コンテキスト トークンのクレームと情報**


|****クレーム****|****説明****|****サンプル コンテキスト トークンでの対応する値****|
|:-----|:-----|:-----|
|aud  <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|iss  <br/> ||00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|nbf  <br/> ||1335822895  <br/> |
|exp  <br/> ||1335866095  <br/> |
|appctxsender  <br/> |"application context sender" の省略形。コンテキスト トークンを SharePoint アドインに送信したアプリケーションを表します。  <br/> 形式は  _プリンシパルの GUID_@ _SharePoint 領域_ です。ここで、 _プリンシパルの GUID_ はアプリケーション プリンシパルの定数 ID で、SharePoint、Exchange 2013、Lync 2013、または Workflow のいずれかです。 <br/> |00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|appctx  <br/> |"add-in context" の省略形。 **CacheKey** と **SecurityTokenServiceURI** を含む JSON オブジェクトです。 <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"  <br/> |
|CacheKey  <br/> |コンテキスト トークンを格納し、取得することができるキー/値の構造を持つ任意のキャッシュのキーとして使用できる一意の値。データベース行のキー列の値として使用することもできます。  <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=  <br/> |
|SecurityTokenServiceURI  <br/> |トークン発行サービスの URI。  <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2  <br/> |
|refreshtoken  <br/> |アドインの更新トークン。  <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6…  <br/> |
|isbrowserhostedapp  <br/> |**Boolean** 型のフィールドで、コンテキスト トークンを含むアドインに対する要求がブラウザー (true) またはリモート イベント レシーバー (false) のどちらから来たものかを示します。 <br/> |true  <br/> |
 

### コンテキスト トークンを使用してアクセスを SharePoint ユーザーに限定する
<a name="UseContextTokenToLimitAccess"> </a>

リモート コンポーネント (WCF サービスなど) に対するアクセスを SharePoint ユーザーに限定する場合は、HTTP 要求に含まれるコンテキスト トークンをコードで検証するだけで済みます。(マネージ コードを使用している場合は、 **TokenHelper.ReadAndValidateContextToken()** を呼び出すだけです。) SharePoint であること (そして、Exchange 2013、Lync 2013、Workflow でないこと) の確認は、トークンのアクター クレームの先頭が **00000003-0000-0ff1-ce00-000000000000** であることをコードで検証して行えます。SharePoint へのコール バックが必要な追加の検証を行う (たとえば、SharePoint 内で特定の役割を持つユーザーにアクセスを限定する) 場合は、特定のユーザーに対してこの検証を実行したというファクトを (コンテキスト トークンの **CacheKey** を使用して) キャッシュすることにより、この検証を 1 回だけで済ますことができます。




## 更新トークンの処理とキャッシュについて理解する
<a name="RefreshTokens"> </a>

 **更新トークンは、Web アプリケーションの起動時に SharePoint がアプリケーションに POST するコンテキスト トークンに組み込まれています。** 更新トークンは、SharePoint アドイン では暗号化を解除できない暗号化トークンです。 **コードでは、このトークン** と、その他の情報を一緒に **使用して、現在のアクセス トークンの有効期限が切れた場合に新しいアクセス トークンを取得します** 。また、このトークンは、 [コンテキスト トークン フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) で *最初の*  アクセス トークンを取得するためにも使用します。(この記事の執筆時点で、SharePoint に対して ACS が発行する更新トークンの有効期間は 6 か月ですが、これは変更される可能性があります。)



アクセス トークンの有効期限は何時間もあり (現在は 12 時間)、エンド ユーザーは SharePoint から SharePoint アドインを起動するたびに新しいアクセス トークンを取得するため、更新トークンが必要になるのは次のいずれかのシナリオだけです。




- ユーザーが長時間かかるセッションでアドインを使用しており、アドインを起動した何時間も後 (現在は 12 時間を超えたとき) にアドインが SharePoint を呼び出す場合。


- アドインの設計上、セッションの終了後しばらくたってから SharePoint にアクセスするようにユーザーがアドインにスケジュールを設定できる場合。


どちらのシナリオでも **アドインは更新トークンをキャッシュする必要があり** 、2 つ目のシナリオではセッションをまたいで持続するサーバー側のキャッシュが必要です。キャッシュのオプションは [アクセス トークン](#CacheAccessToken)の場合と同じで、コンテキスト トークン フローでは [コンテキスト トークンに含まれるキャッシュ キー](#CacheKey)を使用できます。(コンテキスト トークン フローでは、通常、コンテキスト トークンをキャッシュするだけです。コンテキスト トークンには、新しいアクセス トークンを取得するために必要な更新トークンとその他の情報が含まれています。 [認証コード フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) では、コンテキスト トークンがないため、更新トークンそのものをキャッシュします。)



更新トークンのキャッシュ先のストレージが、アドインを使用する特定のユーザーのセッションをまたいで持続するものである場合は、必ずキャッシュを最新の更新トークンで置き換えてください。クラウド ホスト型フローと認証コード フローのどちらの場合も、ユーザーはアドインを起動するたびに新しい更新トークンを取得します。



更新トークンの有効期限が切れている場合は、新しいアクセス トークンを ACS に要求したときに **401 Unauthorized** エラーが発生します。アドインでは、このエラーに応答して、新しい更新トークンを取得し、それを使用して新しいアクセス トークンを取得する必要があります。(更新トークンは暗号化されているため、実際に使用する前にコードで有効期限を確認することはできません。) コンテキスト トークン フローでは、アドインは [新しいコンテキスト トークンを取得する](#GetNewContextToken)ことにより新しい更新トークンを取得します。認証コード フローでは、アドインはフローを再始動することにより新しい更新トークンを取得します。具体的には、コードはユーザーを「 [実行時にアクセス許可を要求するアドインの OAuth のフローを理解する](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow)」で説明されている SharePoint OAuthAuthorize.aspx ページにリダイレクトすることによってエラーに応答する必要があります。




## 認証コードの処理について理解する
<a name="Authcodes"> </a>

 **認証コード フローの認証プロセスは、SharePoint からの要求により ACS が認証コードを発行するところから始まり、SharePoint は次にその認証コードをクエリ パラメーターとしてリモート アプリケーションに渡します** 。認証コードは、ユーザーとリモート アプリケーションのペアごとに一意です。(この記事の執筆時点で、SharePoint に対して ACS が発行する認証コードの有効期限は 5 分ですが、これは変更される可能性があります。) **アプリケーションのコードでは、クエリ パラメーターから認証コードを取得し、ACS にアクセス トークンを要求するときにその認証コードを使用する必要があります。** マネージ コードを使用している場合は、トークンを作成するためのサンプル コードが TokenHelper.cs (および .vb) ファイルに含まれています。クエリ パラメーターを読み取るサンプル コードは、「 [SharePoint にアクセスする、ページのコード ビハインドのサンプルを取得する](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default)」にあります。ACS はアクセス トークンを発行すると直ちに認証コードを無効にするため、認証コードは 1 回のみ使用できるもので、それをキャッシュしても無意味です。




## JWT 時刻値の取り扱い
<a name="JWTtimes"> </a>

 **nbf** および **exp** クレームは、 [JWT 仕様](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl) で規定された形式の値です。これは、1970 年 1 月 1 日からの秒数として記述されます。C# では次のコードによってこの値を変換できます ( _jWTTimeStamp_ はトークンから取り出した値で、たとえば 1335822895 です)。



```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);
```


## トークン処理のトラブルシューティング
<a name="Troubleshooting"> </a>

無料の  [Fiddler ツール](http://www.telerik.com/fiddler)を使用すると、アドインのリモート コンポーネントから SharePoint に送信される HTTP 要求をキャプチャすることができます。 [このツールの無料の機能拡張](https://github.com/andrewconnell/SPOAuthFiddlerExt)を使用すると、要求の中のトークンを自動的にデコードすることができます。




## その他の技術情報
<a name="bk_addresources"> </a>


-  [低信頼承認を使用する SharePoint アドインの作成](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)


- マネージ コードと TokenHelper を使用するコード サンプルについては、「 [SharePoint 2013: CSOM を使用する Hello World リモート アドイン](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf)」と「 [SharePoint アドインのサンプル パック](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)」を参照してください。


- PHP アドインからの REST 呼び出しを使用するコード サンプルについては、「 [SharePoint 2013: PHP サイトから SharePoint ドキュメント ライブラリ上で操作を実行する](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)」を参照してください。



