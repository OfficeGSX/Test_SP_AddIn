---
title: SharePoint アドイン 2013 を登録する
ms.prod: SHAREPOINT
ms.assetid: be41a5dc-2af9-4fd9-bf4e-ad6dfa849524
---



# SharePoint アドイン 2013 を登録する
SharePoint アドイン を Azure ACS に登録するには、Visual Studio、販売者ダッシュボード、または AppRegNew.aspx ページを使用し、登録情報を取得します。
プロバイダー向けのホスト型 SharePoint アドインのリモート コンポーネントが OAuth を使用して SharePoint を操作するためには、アドインをまず  [Azure ACS](https://msdn.microsoft.com/ja-jp/library/azure/gg429788.aspx) クラウド ベース サービスに登録し、テナンシーまたはファームの SharePoint アドイン管理サービスに登録する必要があります (当初 SharePoint アドインは「SharePoint 用アプリ」と呼ばれていたため「アプリ管理サービス」と呼ばれます)。
  
    
    


> **メモ**
> これは、SharePoint ホスト型のアドインでは必要ありません。 
  
    
    


アドインを Azure ACS に登録するには、次の情報を指定します。
  
    
    


- アドインの GUID。クライアント ID と呼ばれます。
    
  
- アドインのパスワード。クライアント シークレットと呼ばれます。
    
  
- ユーザーがアドインを信頼するよう求められる同意ページで使用するアドインの表示名。
    
  
- リモート アドインをホストするドメインの URL。
    
  
- リダイレクト URL。
    
  
アドインを登録すると、アドイン ID が割り当てられ、アドインはアドイン プリンシパルと呼ばれる *セキュリティ プリンシパル*  になります。ユーザーがアドインをインストールすると、SharePoint 管理者はその特定のアドイン プリンシパルに関する情報を取得できます。SharePoint のリソースにアクセスするためのアクセス許可をユーザーが初めてアドインに付与すると (アドインの設計に応じてアドインのインストール時、または実行時に発生します)、SharePoint は Azure ACS からそのアドインに関する情報を取得します。SharePoint はこの情報を、SharePoint のテナンシーまたはファームのアプリ管理サービス データベースに格納します。クライアント シークレットは、Azure ACS にのみ格納されます。SharePoint がアドインのシークレットを認識することはありません。コンテンツ データベース サービスとその他のコンポーネント (ユーザー プロファイル サービスなど) は、アドインの表示名やその他の基本情報をアプリ管理共有サービスから直接取得できます。詳細については、この記事の「 [アドイン登録情報とアドイン プリンシパル情報を取得する ](register-sharepoint-add-ins-2013.md#Retrieve)」を参照してください。
> **メモ**
> この記事では、読者が OAuth 2.0 フレームワークの基礎となっている概念や原理を理解していることを前提としています。詳細については、 [OAuth.net](http://oauth.net/) および [Web 承認プロトコル (OAuth)](http://datatracker.ietf.org/doc/active/) を参照してください。
  
    
    


## SharePoint アドインを Azure ACS に登録する

アドインを登録するには、アドイン開発のライフサイクルのどこにいるか、アドインのアーキテクチャ、およびどのように市場投入する計画なのかに応じて、次の 3 つの方法の 1 つを使用します。
  
    
    


|**登録の方法**|**詳細説明**|
|:-----|:-----|
|Visual Studio と Microsoft Office Developer Tools for Visual Studio を使用して、一時的なアドイン ID を作成する。  <br/> |Office Developer Tools for Visual Studio ウィザードにより、アドインが SharePoint のテスト Web サイトの ACS とアプリ管理サービスに一時的に登録されます。アドインを Visual Studio から実行する (F5) ときに、この ID が使用されます。また、このツールにより、クライアント ID とシークレットが web.config ファイルと AppManifest.xml ファイルの該当箇所にも挿入されます。  <br/> アドインを発行する準備ができたら、Visual Studio 発行ウィザードを使用して販売者ダッシュボードに移動し、アドインを登録できます。SharePoint アドインを Office ストアに公開しない場合は、AppRegNew.aspx を使用してアドインを登録します。(手順は、この後を参照してください。)  <br/> > **メモ**> アドインが、アドインのインストール時ではなく、実行時に動的に SharePoint のリソースにアクセスする権限を要求する場合は、Visual Studio を使用してアドイン ID を作成することはできません。           |
|販売者ダッシュボード を使用してアドインを登録する。  <br/> |アドインを複数の SharePoint テナントまたはファームで使用する場合は、Office ストア でアドインを公開するか、アドイン カタログを通して利用できるようにするかに関係なく、販売者ダッシュボード を使用してアドインを登録してください。販売者ダッシュボード で登録すると、アドインにマルチテナントのアーキテクチャを持たせた設計ができるため、テナントまたはファームの管理者がアドインを個別に登録する必要がなくなります。また、アドインを Office ストア に公開しようと計画している場合も、販売者ダッシュボード を使用してアドインを登録する必要があります。販売者ダッシュボード に登録したアドインは、必ずしもストアを利用して公開する必要はありません。  <br/> 詳細については、「 [販売者ダッシュボードでクライアント ID とクライアント シークレットを作成または更新する](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)」を参照してください。  <br/> |
|AppRegNew.aspx ページを使用する。  <br/> |アドインを 1 つのテナントまたはファームでのみ使用する場合は、AppRegNew フォームを使用して SharePoint アドインを登録します。たとえば、単一の組織で使用するアドインを作成し、組織のアドイン カタログを使用してアドインを配布しようとしている場合は、テナンシーまたはファームの任意の Web サイトの AppRegNew.aspx ページを使用してアドインを登録できます。  <br/> AppRegNew.aspx を使用して登録したアドインは、Office ストア に公開することができません。Office ストア に公開する予定のアドインについては、販売者ダッシュボード から ID を取得する必要があります。  <br/> |
   

### AppRegNew.aspx を使用して登録するには


1.  テナンシーまたはファームの `http://` *<SharePointWebsite>*  `/_layouts/15/AppRegNew.aspx` に移動します。
    
   **AppRegNew ページのフォーム**

  

     ![AppRegNew ページ上のフォームに、クライアント ID、クライアント シークレット、職責、アプリ ドメイン、リダイレクト URI の入力ボックスが表示されています。最初の 2 つの入力ボックスの横には [Generate] (生成) というボタンがあります。フォームの隅に [Create] (作成)、[Cancel] (キャンセル) ボタンがあります。](images/9a38d876-2189-418c-9314-ae493a4cab61.PNG)
  

  

  
2. フォームのフィールドに従って値を入力します。
    
  - **アドイン ID**: クライアント ID とも呼ばれる GUID です。[ **生成**] を選択して生成するか、AppRegNew.aspx に貼り付けることができます。値はアドインごとに一意である必要があり、 *小文字にする必要があります*  。
    
  
  - **アドイン シークレット**: クライアント シークレットとも呼ばれる、意味のない文字列です。[ **生成**] ボタンを使用して AppRegNew.aspx ページ上で生成します。アドイン シークレットの例を次に示します。 **xvVpG0AgVIJfch6ldu4dLUlcZyysmGqBRbpFDu6AfJw=** 。
    
    > **重要**
      > アドイン シークレットには有効期限があります。アドインを販売者ダッシュボード で登録する場合は、有効期限を最長で 3 年間まで設定できます。ダッシュボードでは、古いシークレットの有効期限の日付に近づいたときに、新しいシークレットを追加することもできます。新しいシークレットはアドインのすべてのインスタンスで有効になります。アドインを AppRegNew.aspx で登録する場合、シークレットは 1 年で有効期限が切れます。詳細については、「 [SharePoint アドインで期限が切れたクライアント シークレットを置換する方法](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)」を参照してください。 
  - **タイトル**: わかりやすいタイトル。たとえば、「Contoso 写真印刷アドイン」などです。ユーザーはアドインが要求しているアドインのアクセス許可を付与または拒否するように求められます。このタイトルは、同意を求めるメッセージでアドインの名前として表示されます。 
    
  
  - **アドイン ドメイン**: SharePoint アドインのリモート コンポーネントのホスト名。リモート アプリケーションがポート 443 を使用しない場合は、アドイン ドメインにポート番号を含める必要があります。アドイン ドメインは、Web アプリケーションで使用する URL バインドと一致する必要があります。この値にはプロトコル ("https:") や "/" の文字を含めないでください。Web アプリケーションのホストが DNS CNAME エイリアスを使用している場合は、そのエイリアスを使用します。次にいくつか例を示します。
    
  - www.contoso.com:3333
    
  
  - www.fabrikam.com
    
  
  - **リダイレクト URI:** ACS が認証コードを送信するリモート アプリケーションまたはサービスのエンドポイント。厳密に言えば、SharePoint アドインはこの値を使用しません。リダイレクト URI は、SharePoint の外部で起動し、 [認証コード フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)を使用して SharePoint データに対するアクセス許可を得る必要のある Web アプリケーションが必要とします。リダイレクト URI は、本当の意味での SharePoint アドインでは無視されます (SharePoint から起動され、 [コンテキスト トークン フロー](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)を使用します)。リダイレクト URI は通常、ACS から認証コード要求したのと同じページ、コントローラー メソッド、Web サービス メソッドですが、異なるエンドポイントにすることもできます。エンドポイントは、ACS によって送信される HTTP 応答から認証コードを取得し、そのコードを使用してアクセス トークンと更新トークンを要求するロジックを持っている必要があります。詳細については、「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。このフォームには、本当の意味での SharePoint アドインの場合でも、有効な値を入力する必要があります (ただし、その値は使用されません)。
    
    値は、プロトコル ( *HTTPS でなければならない*  ) を含む完全なエンドポイント URL にする必要があります。たとえば次のとおりです。
    
  - https://www.contoso.com/Default.aspx
    
  
  - https://www.fabrikam.com/RedirectAccept.aspx
    
  
  - https://www.northwindtraders.com/home/index
    
  
  - https://adventureworks.com/vacationdata.svc
    
  
3. フォームの [ **作成**] を選択します。ページが再読み込みされ、入力した値が確認のために表示されます。これらの値を、コピーして貼り付けやすい形式で記録してください。これらの値は、web.config ファイルや AppManifest.xml ファイルに入力するか、Visual Studio の [ **発行**] ウィザードで入力する必要があります。
    
  
SharePoint アドインを登録する方法には関係なく、アドインをステージングまたは運用に展開する準備ができたら、 [登録する値を web.config ファイルと AppManifest.xml ファイルに入力する](#EditConfigFiles)必要があります。ただし、Visual Studio では、Microsoft Office Developer Tools for Visual Studio によってこの構成が実行されます。
  
    
    

## 登録する値を web.config ファイルと AppManifest.xml ファイルに入力する
<a name="EditConfigFiles"> </a>

SharePoint アドインをパッケージ化してリモート コンポーネントを展開する前に、登録する値のいくつかを AppManifest.xml ファイルと web.config ファイルに入力します。
  
    
    

> **ヒント**
> Visual Studio の発行ウィザードを使用して、SharePoint アドインを発行する場合は、発行プロセスで、クライアント ID とクライアント シークレットを入力するように Visual Studio から要求されます。入力した情報は、自動的に正しい場所に入力されします。 
  
    
    


1. Visual Studio プロジェクトの Web.config ファイルに、アドイン ID 値を **ClientId** 値として入力します (ツールによって入力された一時的な値を置き換えます)。
    
    > **重要**
      > クライアント ID の GUID の文字はすべて小文字にする必要があります。 

    以下に例を示します。
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
   .  .  .
</appSettings>
  ```

2. アドイン シークレットの値を **ClientSecret** 値として入力します (ツールによって入力された一時的な値を置き換えます)。
    
    これらの値が Web アプリケーションの Web.config ファイルで使用される例を次に示します。
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
  <add key="ClientSecret" value="l0z/8TzWN0yQBzMBSEZtYts2Vt3Eo/oE3rfCdPaogKQ= " />
</appSettings>
  ```

3. Visual Studio プロジェクトの AppManifest.xml ファイルに、アドイン ID 値を **ClientId** 値として *小文字で*  入力します。
    
    > **メモ**
      > アドイン マニフェストは、SharePoint のリソースに対するアクセス許可を実行時に要求する Web アプリケーションには適用されません。このようなアプリケーションは、本当の意味での "SharePoint アドイン" ではありません。SharePoint にはインストールされず、アドイン マニフェストを持っていません。詳細については、「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。 

    AppManifest.xml ファイルで **ClientId** 値を使用する例を次に示します。
    


  ```XML
  
<AppPrincipal>
  <RemoteWebApplication ClientId="a044e184-7de2-4d05-aacf-52118008c44e "/>
</AppPrincipal>
  ```

4. Office Developer Tools for Visual Studio は **StartPage** の要素でトークン `~remoteAppUrl` を使用します。( `<StartPage>~remoteAppUrl/Pages/Default.aspx?{StandardTokens}</StartPage>` など。) このトークンは、Visual Studio の [ **発行**] ウィザードで使用する場合、リモート コンポーネントの URL に解決されます。ウィザードを使用しない場合 (または、ウィザードを使用しても、リモート コンポーネントを Azure に発行している場合) には、このトークンをアドインの登録時に使用した [ **アドイン ドメイン**] の値に手動で置き換える必要があります。これは、HTTPS プロトコルも含める以外は、ポート番号がある場合はそれも含めて、 *完全に*  同じ値でなければなりません。以下に例を示します。
    
  ```XML
  
<StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
  ```

5. AppRegNew.aspx の [ **タイトル**] フィールドに使用したのと同じ値を、AppManifest.xml ファイルの **Title** 要素で使用することを検討します。 **Title** 要素の値は、アドインのインストール後に、ユーザーにアドインの名前として表示されます。同意ダイアログに表示される名前と、SharePoint の UI とで名前が異なると、アドインのユーザーが混乱する恐れがあります。
    
    これらの値をアドイン マニフェストで指定する例を次に示します。
    


  ```XML
  <Properties>
  <Title>Contoso photo printing app</Title>
  <StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
</Properties>
  ```


## 実行時にアクセス許可を求めるアドインでリダイレクト URL を使用する
<a name="UseRedirectUrl"> </a>

Web アプリケーションが SharePoint の外部から起動する場合 (つまり、本当の意味での SharePoint アドインではない場合)、そのアドインは、実行時に SharePoint からアクセス許可を求めるように設計する必要があります。アドインには、リダイレクト URI やその他の情報を使用して ACS からアクセス トークンを取得するためのコードが必要です。この URI が設定されている場所を探し、AppRegNew.aspx または 販売者ダッシュボード の [ **リダイレクト先の URI**] フィールドに使用したのと *まったく同じ*  値を使用します。これは、コード ファイルか構成ファイルの中にあります。
  
    
    

## アドイン登録情報とアドイン プリンシパル情報を取得する
<a name="Retrieve"> </a>

SharePoint でインストールまたは登録したアドインのアドイン登録情報とアドイン プリンシパル情報を取得できます。 
  
    
    
自分が登録したアドインの登録情報を検索するには、 `http://` *<SharePointWebsite>*  `/_layouts/15/AppInv.aspx` に移動します。
  
    
    
この情報を検索するには、アドインの登録に使用したクライアント ID (アドイン ID とも呼ばれる) を覚えておく必要があります。この検索により、特定のクライアント ID について次の情報が返されます。
  
    
    

- タイトル
    
  
- アドイン ドメイン
    
  
- リダイレクト URL (これはリダイレクト URI と同じ)
    
  
この検索では、アドイン シークレットの値は返されません。
  
    
    
登録されたアドイン プリンシパルの一覧を取得するには、次に移動します。
  
    
    
 `http://` *<SharePointWebsite>*  `/_layouts/15/AppPrincipals.aspx`
  
    
    

## その他の技術情報
<a name="AR"> </a>


-  [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの 3 つの承認システム](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
