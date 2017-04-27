---
title: クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする
ms.prod: SHAREPOINT
ms.assetid: bc37ff5c-1285-40af-98ae-01286696242d
---



# クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする
SharePoint 2013 でクロス ドメイン ライブラリを使用して、アドインから SharePoint Web サイト内のデータにアクセスする方法を説明します。
SharePoint アドインを作成する場合、通常は、さまざまなソースのデータを組み込む必要があります。ただし、 [セキュリティのため](http://msdn.microsoft.com/library/cc709423.aspx)、一度に複数のドメインと通信することを防止するブロック メカニズムが存在します。ほとんどのブラウザーにこうしたセキュリティ メカニズムが実装されており、ドメイン間でクライアント側呼び出しを行うのが困難または不可能になっています。




図 1 にドメイン間でブロックされた要求を示します。

**図 1. ドメイン間でブロックされた要求**




ユーザーがアドインのドメインからページを要求した場合 (1)、クライアント側通信はそのドメインにのみ関連付けられます。アドインがページからクライアント側呼び出しを発行できるのは、同じドメイン内の他のリソースに対してのみです。しかし、アドインでシナリオを実行するには通常、SharePoint ドメインなど他のドメインのリソースが必要です。ページ内のコードで、SharePoint ドメインに対して要求を発行しようとすると (2)、ブラウザーによってブロックされます。通常、[ **アクセスが拒否されました** ] というエラーが表示されます。このエラーは、要求したリソースに対するアクセス許可がないことを示すものではなく、多くの場合、そのリソースに対する要求を発行することすらできないことを示しています。クロスドメイン ライブラリを使用すると、アドインに含まれる Web ページから、アドインのドメインと SharePoint ドメインのデータにアクセスできます。クロスドメイン ライブラリは、リモート アドインで参照できる、SharePoint Web サイトでホストされている JavaScript ファイル (SP.RequestExecutor.js) 形式のクライアント側代替手段です。クロスドメイン ライブラリを使用することで、プロキシを介してリモート アドイン ページ内の複数のドメインとやり取りできます。これは、サーバーではなくクライアントでアドインのコードを実行する場合と、SharePoint とリモート インフラストラクチャの間にファイアウォールなどの接続の障害物がある場合に適したオプションです。ホスト Web 上のデータ、たとえばアドインの有無に関係なくエンド ユーザーがやり取りするリストにアクセスできます。または、アドイン向けにプロビジョニングされたリストなど、アドイン Web のデータへのアクセスもできます。さらに、アドインにテナントをスコープとしたアクセス許可があり、アドイン カタログを使用してバッチ インストールとして展開されていれば、アドインは他のサイト コレクションおよび Web サイトにもアクセスできます。

> **メモ**
> このトピックで、 **アドインのドメイン** とはアドイン ページをホストするドメインを指します。プロバイダー向けのホスト型アドイン内のリモート Web アプリケーションのドメインなどを指定できますが、アドイン ページをアドイン Web 内の SharePoint に配置して、ホスト Web ドメインを呼び出すこともできます。後者のシナリオでは、アドインのドメインとはアドイン Web のドメインです。




この記事で取り上げる主な例は、アドイン Web 上のデータを読み取り、それを Web ページに表示するアドインを作成する方法を示すものです。「 [次の手順](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Next)」セクションでは、その例を基盤として構築されるいくつかのシナリオを示します。
## この記事の手順を行う上での前提条件
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

この記事に示されている例を行うには、次のものが必要です。




-  [Visual Studio 2012](https://www.microsoft.com/ja-jp/download/details.aspx?id=30682)


-  [Microsoft Office Developer Tools for Visual Studio 2012](https://msdn.microsoft.com/ja-jp/office/aa905340.aspx)


- SharePoint 2013開発環境 (社内のシナリオでは、アプリの分離が必要)



## クロスドメイン ライブラリを使用したアドイン Web 上のデータの読み取り
<a name="SP15Accessdatafromremoteapp_Codeexample"> </a>

この例では、SharePoint の外部でホストされている単純なページがあり、Representational State Transfer (REST) エンドポイントを使用して SharePoint Web サイト (アドイン Web) 内のデータを読み取っています。クロスドメイン ライブラリではアドイン Web が必要であるため、このシナリオで開始するのは良い方法です。



アドイン Web からデータを読み取るには、次のことを実行する必要があります。




1. SharePoint アドインと Web プロジェクトを作成します。


2. アドイン Web でリスト アイテムを作成します。この手順により、アドインの展開時にアドイン Web が作成されるようになります。


3. クロスドメイン ライブラリを使用してリスト アイテムを読み取るアドイン ページを作成します。


図 2 に、アドイン Web 上のデータを表示する Web ページを示します。




**図 2. アドイン Web 上のデータを表示する Web ページ**








![クロス ドメイン読み取り項目のサンプル結果画面](images/CrossDomainReadItemsResult.png)




### SharePoint アドイン プロジェクトと Web プロジェクトの作成


1. 管理者として Visual Studio 2012 を開きます (これを行うには、[ **スタート**] メニューの Visual Studio 2012 アイコンを右クリックし、[ **管理者として実行**] をクリックします)。


2. [ **SharePoint 2013 用アドイン**] テンプレートを使用して新しいプロジェクトを作成します。

    Visual Studio 2012 の [ **SharePoint 2013 用アドイン**] テンプレートは、 [ **テンプレート**] **>** [ **Visual C#**]、[ **Office SharePoint**] **>** [ **アドイン**] にあります。


3. デバッグに使用する SharePoint Web サイトの URL を入力します。


4. アドインのホスティング オプションとして [ **プロバイダー向けのホスト型**] を選択します。

    > **メモ**
      > SharePoint ホスト型アドイン でもクロスドメイン ライブラリを使用できます。ただし、SharePoint ホスト型アドイン では、アドイン ページはすでにアドイン Web 内にあり、このような場合、クロスドメイン ライブラリはリスト アイテムを読み取る必要がありません。ホスト Web 内のデータを読み取る SharePoint ホスト型アドイン サンプルについては、「 [SharePoint 2013: SharePoint でホストされるアドインでクロスドメイン ライブラリを使用する (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)」またはこの記事の後半にある「 [ホスト Web からのデータ アクセス](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)」をご覧ください。 

### アドイン Web でのリスト アイテムの作成


1. **ソリューション エクスプローラー** で SharePoint アドイン プロジェクトを右クリックし、[ **追加**] **>** [ **新しいアイテム…**] を選択します。


2. [ **Visual C# アイテム**] **>** [ **Office/SharePoint**] **>** [ **リスト**] を選択します。リストの名前を **お知らせ** に設定します。


3. [ **お知らせ**] **>** [ **Elements.xml**] をダブルクリックします。次の XML ノードを **ListInstance** 要素の子として貼り付けます。

 ```

<Data>
    <Rows>
        <Row>
            <Field Name="Title">Lorem ipsum 1</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
        <Row>
            <Field Name="Title">Lorem ipsum 2</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
    </Rows>
</Data>
 ```


### クロスドメイン ライブラリを使用する新しいページを追加するには


1. **ソリューション エクスプローラー**で Web プロジェクトの **Default.aspx** をダブルクリックします。


2. 以下のコードをコピーし、Default.aspx ファイルに貼り付けます。このコードは次のタスクを実行します。

  - Microsoft CDN から jQuery ライブラリを読み込みます。


  - 結果のプレースホルダーを提供します。


  - クエリ文字列からアドイン Web の URL を抽出します。


  - jQuery の **getScript** 関数を使用して、クロスドメイン ライブラリ JavaScript を読み込みます。

    関数によって必要なリソースが読み込まれ、指定された関数の実行が続行されます。これにより、クロスドメイン ライブラリが読み込まれ、後続のコードで利用できるようになります。


  - **RequestExecutor** オブジェクトのインスタンスを作成します。既定では、RequestExecutor はアドイン Web をコンテキスト サイトとして使用します。

    > **メモ**
      > **AppContextSite** エンドポイント (REST) またはオブジェクト (JSOM) を使用して、コンテキスト サイトをアドイン Web とは異なる他のサイトに変更できます。AppContextSite について詳しくは、この記事の後半にある「 [ホスト Web からのデータ アクセス](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)」をご覧ください。 
  - リスト アイテム エンドポイントへの REST 呼び出しを発行します。


  - 成功した実行を処理し、Web ページにリスト アイテムを表示します。


  - エラーを処理し、リモート Web ページにエラー メッセージを表示します。



 ```

<html>
    <head>
        <title>Cross-domain sample</title>
    </head>
    <body>
        <!-- This is the placeholder for the announcements -->
        <div id="renderAnnouncements"></div>
        <script 
            type="text/javascript" 
            src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
        </script>
        <script type="text/javascript">
          var hostweburl;
          var appweburl;

          // Load the required SharePoint libraries
          $(document).ready(function () {
            //Get the URI decoded URLs.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );

            // resources are in URLs in the form:
            // web_url/_layouts/15/resource
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js files and continue to the successHandler
            $.getScript(scriptbase + "SP.RequestExecutor.js", execCrossDomainRequest);
          });

          // Function to prepare and issue the request to get
          //  SharePoint data
          function execCrossDomainRequest() {
            // executor: The RequestExecutor object
            // Initialize the RequestExecutor with the add-in web URL.
            var executor = new SP.RequestExecutor(appweburl);

            // Issue the call against the add-in web.
            // To get the title using REST we can hit the endpoint:
            //      appweburl/_api/web/lists/getbytitle('listname')/items
            // The response formats the data in the JSON format.
            // The functions successHandler and errorHandler attend the
            //      sucess and error events respectively.
            executor.executeAsync(
                {
                  url:
                      appweburl +
                      "/_api/web/lists/getbytitle('Announcements')/items",
                  method: "GET",
                  headers: { "Accept": "application/json; odata=verbose" },
                  success: successHandler,
                  error: errorHandler
                }
            );
          }

          // Function to handle the success event.
          // Prints the data to the page.
          function successHandler(data) {
            var jsonObject = JSON.parse(data.body);
            var announcementsHTML = "";

            var results = jsonObject.d.results;
            for (var i = 0; i < results.length; i++) {
              announcementsHTML = announcementsHTML +
                  "<p><h1>" + results[i].Title +
                  "</h1>" + results[i].Body +
                  "</p><hr>";
            }

            document.getElementById("renderAnnouncements").innerHTML =
                announcementsHTML;
          }

          // Function to handle the error event.
          // Prints the error message to the page.
          function errorHandler(data, errorCode, errorMessage) {
            document.getElementById("renderAnnouncements").innerText =
                "Could not complete cross-domain call: " + errorMessage;
          }

          // Function to retrieve a query string value.
          // For production purposes you may want to use
          //  a library to handle the query string.
          function getQueryStringParameter(paramToRetrieve) {
            var params =
                document.URL.split("?")[1].split("&amp;");
            var strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
              var singleParam = params[i].split("=");
              if (singleParam[0] == paramToRetrieve)
                return singleParam[1];
            }
          }
        </script>
    </body>
</html>
 ```


### ソリューションを構築して実行するには


1. F5 キーを押します。

    > **メモ**
      > F5 キーを押すと、Visual Studio によってソリューションが構築され、アドインが展開され、アドインのアクセス許可ページが表示されます。 
2. [ **信頼する**] ボタンをクリックします。


3. [ **サイト コンテンツ**] ページの [アドイン] アイコンを選択します。


ダウンロード可能なコード サンプルを使用する場合は、コード ギャラリーから取得できます。 [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain) または [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain) を用いて、 **クロスドメイン ライブラリを使用してリスト アイテムを取得する場合のコード サンプルをお使いください** 。




**表 2. ソリューションのトラブルシューティング**


|**表示されるメッセージ**|**対策**|
|:-----|:-----|
|エラー メッセージ: 申し訳ございません。サイトへのアクセス中に問題が発生しました。  <br/> エラーを修正するボタンもありますが、問題は解決されません。  <br/> |Internet Explorer のセキュリティ ゾーンに関する既知の問題がヒットする可能性があります。「 [SharePoint アドインで Internet Explorer の異なるセキュリティ ゾーンを横断してクロスドメイン ライブラリを操作する](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)」をご覧ください。  <br/> |
|エラー メッセージ: 必要な機能がお使いのブラウザーでサポートされていません。IE 8 以上または他の最新ブラウザーを使用していることを確認してください。'X-UA-Compatible' メタ タグが 'IE=8' 以上に設定されていることを確認してください。  <br/> |クロスドメイン ライブラリには **IE8** 以上のドキュメント モードが必要です。シナリオによっては、ドキュメント モードが既定で **IE7** に設定されている場合があります。Internet Explorer 開発者ツールを使用して、ページのドキュメント モードを決定および変更できます。詳細については、「 [ドキュメント互換性の定義](http://msdn.microsoft.com/library/cc288325.aspx)」を参照してください。  <br/> |
|エラー メッセージ: 'Type' は定義されていません。  <br/> さらに、アドインで JavaScript Object Model (JSOM) を使用しています。  <br/> |JSOM は Microsoft Ajax ライブラリの **Type.registerNamespace** メソッドを使用して **SP** 名前空間を登録します。次のコードを使用して、お使いのページから Microsoft Ajax ライブラリにリファレンスを追加してください。 <br/>```HTML<script type="text/javascript"  src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js"></script>```|
 

## 次の手順
<a name="SP15Accessdatafromremoteapp_Next"> </a>

この記事では、REST エンドポイントにクエリして、SharePoint でホストされていないアドイン ページを使用してアドイン Web からデータを読み取る方法を説明します。次のシナリオを参照して、クロスドメイン ライブラリの詳細を確認することもできます。




### JSOM を使用してアドイン Web からデータを読み取る
<a name="SP15Accessdatafromremoteapp_JSOM"> </a>

設定によっては、REST ではなく JSOM を使用してアドイン Web からデータのクエリを実行することもできます。JSOM でクロスドメイン ライブラリを使用するには、追加タスクを実行する必要があります。




- アドイン ページで SharePoint JSOM を参照します。


- **ProxyWebRequestExecutorFactory** オブジェクトを初期化し、コンテキスト オブジェクトのファクトリとして設定します。


- SharePoint オブジェクトにアクセスして、リストからデータを読み取ります。


- コンテキストでオブジェクトを読み込んで、クエリを実行します。


タスクの実行方法を示すコード サンプルについては、「 [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)」をご覧ください。JSOM の使用方法について詳しくは、「 [SharePoint アドインで JavaScript オブジェクト モデル (JSOM) を使用する](http://blogs.msdn.com/b/officeapps/archive/2012/09/04/using-the-javascript-object-model-jsom-in-apps-for-sharepoint.aspx)」をご覧ください。




### ホスト Web からのデータ アクセス
<a name="SP15Accessdatafromremoteapp_Hostweb"> </a>

このページに示されている例では、アドイン Web からデータを読み取る方法を示します。クロスドメイン ライブラリでは最初にアドインをコンテキスト サイトとして使用するため、これを最初の例に用いることにします。ただし、ホスト Web 上のデータにアクセスするシナリオも多数存在します。ホスト Web 上のデータにアクセスするには、いくつかのタスクが必要です。




- ホスト Web をクロスドメイン ライブラリのコンテキスト サイトとして設定します。


- アドインに適切なアクセス許可を付与します。


 **AppContextSite** エンドポイント (REST) またはオブジェクト (JSOM) を使用して、コンテキスト サイトを変更できます。次の例で、REST エンドポイントを使用してコンテキスト サイトを変更する方法を示します。





```

executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/title?@target='" +
            hostweburl + "'",
        method: "GET",
        headers: { "Accept": "application/json; odata=verbose" },
        success: successHandler,
        error: errorHandler
    }
);```

次のコード例で、JSOM を使用してコンテキスト サイトを変更する方法を示します。





```

context = new SP.ClientContext(appweburl);
factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
context.set_webRequestExecutorFactory(factory);
appContextSite = new SP.AppContextSite(context, hostweburl);

this.web = appContextSite.get_web();
context.load(this.web);```

既定では、アドインにはアクセス許可が付与されますが、ホスト Web にはアクセス許可が付与されません。次の例は、ホスト Web からデータを読み取るためのアクセス許可要求を宣言するマニフェスト セクションを示しています。





```XML

<AppPermissionRequests>
    <AppPermissionRequest 
        Scope="http://sharepoint/content/sitecollection/web" 
        Right="Read" />
</AppPermissionRequests>```

必ずアドイン Web で (空白のページまたはリストのような) リソースを作成して、クロスドメイン ライブラリを使用するために必要な、アドイン Web のプロビジョニングを行います。




### サイト コレクション間でのデータ アクセス
<a name="SP15Accessdatafromremoteapp_TenantScope"> </a>

クロスドメイン ライブラリでは、同じテナント内のサイト コレクション間でデータにアクセスできます。サイト コレクション間でデータにアクセスするには、いくつかのタスクを実行する必要があります。




- テナント内のデータにアクセスするためのアクセス許可要求を追加します。


- コードで、クエリするサイト コレクションにコンテキスト サイトを切り替えます。


- アドインをアドイン カタログに追加します。


- アドインをテナントをスコープとするアドインとして Web サイトに展開します。たとえば、テナントをスコープとするアドインとして展開する方法については、「 [SharePoint 2013: テナントをスコープとするアドインでクロスドメイン ライブラリを使用する (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)」のコード サンプルに関する説明をご覧ください。


アドインには、テナントからデータにアクセスするためのアクセス許可も必要です。次の例は、テナントからデータを読み取るためのアクセス許可要求を宣言するマニフェスト セクションを示しています。





```XML

<AppPermissionRequests>
  <AppPermissionRequest 
    Scope="http://sharepoint/content/tenant" 
    Right="Read" />
</AppPermissionRequests>```

コードでコンテキスト サイトを切り替えるには、「 [ホスト Web からのデータ アクセス](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)」セクションと同様に、 **AppContextSite** エンドポイント (REST) またはオブジェクト (JSOM) を使用します。REST エンドポイントとは、/_api/SP.AppContextSite(@target)/web/title?@target='weburl' で、JSOM でオブジェクトをインスタンス化する方法の例は次のとおりです: `appContextSite = new SP.AppContextSite(context, weburl);`。



開発者はアドイン カタログからのみテナントをスコープとするアドインを展開できます。アドイン カタログはオンプレミス環境または SharePoint Online 環境にプロビジョニングできます。アドインをアドイン カタログにアップロードするのは、ファイルをドキュメント ライブラリにアップロードするのと同じぐらい簡単です。手順について詳しくは、「 [アドイン カタログ サイトにカスタム アドインを追加する](http://office.microsoft.com/ja-jp/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)」をご覧ください。



アドイン カタログから、アドインをテナント内の 1 つ以上の Web サイトに展開できます。アドインにはテナント内のデータにアクセスするアクセス許可があるため、1 つの Web サイトに展開するだけでテナント全体のデータにアクセスできます。アドイン カタログからアドインを展開する方法については、「 [カスタム アドインを展開する](http://office.microsoft.com/ja-jp/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)」をご覧ください。



サイト コレクション間でデータにアクセスする方法を示すコード サンプルをダウンロードするには、「 [SharePoint 2013: テナントをスコープとするアドインでクロスドメイン ライブラリを使用する (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)」をご覧ください。




### 異なるセキュリティ ゾーン間での呼び出しの発行
<a name="SP15Accessdatafromremoteapp_IEZones"> </a>

クロスドメイン ライブラリでは、アドイン ページの **IFrame** でホストされているプロキシ ページを使用して通信を可能にします。アドイン ページと SharePoint Web サイトが異なるセキュリティ ゾーンにある場合、認証 Cookie の送信ができません。認証 Cookie がない場合、IFrame がプロキシ ページを読み込もうとすると、SharePoint サインイン ページにリダイレクトされます。セキュリティ上の理由により IFrame に SharePoint サインイン ページを含めることはできません。これらのシナリオでは、ライブラリはプロキシ ページを読み込むことができず、SharePoint との通信はできません。



ただし、これらのシナリオには解決策があります。解決策は、アドイン ページをアドイン Web でホストされているページでラップする **アプリホスト パターン** です。明らかなセキュリティ境界がない場合でも、クロスドメイン ライブラリを使用するアドインではアプリホスト パターンを使用することをお勧めします。詳しくは、「 [SharePoint アドインで Internet Explorer の異なるセキュリティ ゾーンを横断してクロスドメイン ライブラリを操作する](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)」をご覧ください。




### SharePoint ホスト型アドインのその他のリモート ホストからのデータ アクセス
<a name="SP15Accessdatafromremoteapp_SPhosted"> </a>

既定では、適切なアクセス許可があれば、SharePoint ホスト型アドイン はホスト Web へのクロスドメイン呼び出しを発行できます。ただし、SharePoint ホスト型アドイン は **AppPrincipal** の **AllowedRemoteHostUrl** 属性でリモート ホストを指定することもできます。これにより、アドイン Web やほかの場所にある別のホストからもクロスドメイン呼び出しを発行できます。



クロスドメイン ライブラリを使用する SharePoint ホスト型アドインのサンプルをダウンロードする場合は、「 [SharePoint 2013: SharePoint でホストされるアドインでクロスドメイン ライブラリを使用する (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)」をご覧ください。




## その他の技術情報
<a name="SP15Accessdatafromremoteapp_Addresources"> </a>


-  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)


-  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)


-  [コード サンプル: クロスドメイン ライブラリ (REST) を使用してホスト Web タイトルを取得する](http://code.msdn.microsoft.com/SharePoint-2013-Get-the-0ec36bb6)


-  [コード サンプル: クロスドメイン ライブラリ (JSOM) を使用してホスト Web タイトルを取得する](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)


-  [コード サンプル: SharePoint でホストされるアドインでクロスドメイン ライブラリを使用する (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)


-  [コード サンプル: テナントをスコープとするアドインでクロスドメイン ライブラリを使用する (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)


-  [コード サンプル: クロム コントロールとクロス ドメイン ライブラリ (REST) を使用する](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)


-  [コード サンプル: クロム コントロールとクロス ドメイン ライブラリ (JSOM) を使用する](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)


-  [コード サンプル: カスタム アクションとクロスドメイン ライブラリを使用して書籍をオーダーする](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)


-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [SharePoint アドインで Internet Explorer の異なるセキュリティ ゾーンを横断してクロスドメイン ライブラリを操作する](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)


-  [SharePoint 2013 のクロスドメイン ライブラリのカスタム プロキシ ページを作成する](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)


-  [SharePoint 2013 で Web プロキシを使用してリモート サービスのクエリを実行する](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)


-  [SharePoint アドインのオンプレミスの開発環境をセットアップする](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)


