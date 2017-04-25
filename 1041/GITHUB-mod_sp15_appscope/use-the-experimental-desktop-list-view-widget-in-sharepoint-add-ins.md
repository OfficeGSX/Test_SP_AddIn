---
title: SharePoint アドインでデスクトップ リスト ビューの実験用ウィジェットを使用する
ms.prod: SHAREPOINT
ms.assetid: b1e26543-9d03-4759-80bf-e6ce729a1b19
---


# SharePoint アドインでデスクトップ リスト ビューの実験用ウィジェットを使用する
ページが SharePoint でホストされていない場合でも、任意の Web ページでデスクトップ リスト ビュー ウィジェットを使用する方法について説明します。さまざまなアドインでリスト ビュー ウィジェットを使って、SharePoint サイトでホストされているリスト内のデータを表示してみてください。
> **メモ**
> Office Web Widgets - Experimental は、研究およびフィードバックを得る目的でのみ提供されています。運用シナリオでは使用しません。Office Web ウィジェットは、今後のリリースでその動作が大幅に変更される可能性があります。「 [Office Web Widgets - Experimental のライセンス条項](office-web-widgetsexperimental-license-terms.md)」を参照し、把握してください。 




このリスト ビュー ウィジェットは、通常のリスト ビュー ウィジェットと同様に SharePoint リスト内のデータを表示できますが、必ずしも SharePoint でホストされているとは限らないアドインや Web サイトで使用できます。
**図 1. デスクトップ リスト ビュー ウィジェットによるリスト内のデータの表示**








![ページ上の [Desktop List View] 実験用コントロール](images/DesktopListView_basic.png)












## 概要

SharePoint リストのビューを指定して、データの表示に使用できます。リスト ビュー ウィジェットでは、ビューで指定された順序で列およびアイテムが表示されます。



リスト ビュー ウィジェットは、クロスドメイン ライブラリを使用してリストのデータを取得します。このため、クライアント レベルで通信が行われます。




> **注意**
> デスクトップ リスト ビュー ウィジェットでは、ネイティブ リスト ビューのすべてのシナリオが有効になっているわけではありません。 




以下のシナリオは、ウィジェットの現在のバージョンでは有効にされていません。




- クロス ドメイン ライブラリでネイティブにサポートされていない認証方式の使用。


- SharePoint のリストまたはライブラリ以外のデータ ソースの使用。


- ウィジェットのデータ バインド。


- ユーザー タッチフレンドリなビュー。


- ユーザーによるインライン編集。


- プレゼンス情報の表示。


- カスタム レンダリング テンプレートの提供。


- 社内のシナリオ。ウィジェットは現在、SharePoint Online でのみ動作します。



## この記事の手順を実行するための前提条件

この記事に示されている手順を実行するには、次のものが必要です。




- Visual Studio 2013


- NuGet パッケージ マネージャー。詳細については、「 [NuGet をインストールする](http://go.microsoft.com/fwlink/?LinkId=271465)」を参照してください。


- SharePoint 2013 開発環境 (オンプレミスのシナリオでは、アプリの分離が必要)。 


- Office Web Widgets - Experimental の NuGet パッケージ。NuGet パッケージのインストールの詳細については、「 [ダイアログを使用して NuGet パッケージを管理する](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog)」を参照してください。また、 [NuGet ギャラリー ページ](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)も参照してください。



## プロバイダーでホストされる SharePoint アドインでデスクトップ リスト ビュー ウィジェットを使用する

この例では、SharePoint の外部でホストされるシンプルなページでデスクトップ リスト ビュー ウィジェットを宣言します。



リスト ビュー ウィジェットを使用するには、以下の手順を実行する必要があります。




- SharePoint アドインと Web プロジェクトを作成します。


- アドイン Web 上にリストを作成します。この手順により、アドインの展開時にアドイン Web が作成されるようになります。

    > **メモ**
      > クロスドメイン ライブラリを使用するには、アドイン Web が存在する必要があります。リスト ビュー ウィジェットは、クロスドメイン ライブラリを使用して SharePoint と通信します。 
- HTML マークアップを使用してリスト ビュー ウィジェットを宣言するアドイン ページを作成します。



### SharePoint アドインと Web プロジェクトを作成するには


1. 管理者として Visual Studio 2013 を開きます ([ **スタート**] メニューの Visual Studio 2013 アイコンを選択し、[ **管理者として実行**] を選択します)。


2. [ **SharePoint アドイン 2013**] テンプレートを使用して新しいプロジェクトを作成します。SharePoint アドイン 2013 テンプレートは、[ **テンプレート**] > [ **Visual C#**]、[ **Office/SharePoint**] > [ **アドイン**] にあります。


3. デバッグに使用する SharePoint Web サイトの URL を入力します。


4. アドインのホスティング オプションとして [ **プロバイダー向けのホスト型**] を選択します。

    > **メモ**
      > デスクトップ リスト ビュー ウィジェットは、他のホスティング オプションでも使用できます。また、Office アドインや独自の Web サイトでも使用できます。 
5. Web アプリケーション プロジェクトの種類として [ **ASP.NET Web フォーム アプリケーション**] を選択します。


6. 認証オプションとして [ **Windows Azure アクセス制御サービス**] を選択します。



### アドイン Web 上にリストを作成するには


1. **ソリューション エクスプローラー**で SharePoint アドイン プロジェクトを選択します。[ **追加**] > [ **新しいアイテム…**] を選択します。


2. [ **Visual C# アイテム**] > [ **Office/SharePoint**] > [ **リスト**] を選択します。[ **名前**] テキスト ボックスに、「 **お知らせ** 」と入力します。[ **追加**] を選択します。


3. [ **既存のリスト テンプレートに基づくリスト インスタンスを作成**] を選択します。[ **お知らせ**] テンプレートを選択します。[ **完了**] を選択します。



### デスクトップ リスト ビュー ウィジェットを使用する新しいページを追加するには


1. **ソリューション エクスプローラー**で Web プロジェクトの [ **ページ**] フォルダーを選択します。


2. 以下のコードをコピーし、プロジェクトの **ASPX** ファイルに貼り付けます。このコードは以下のタスクを実行します。

  - 必要な Office ライブラリおよびリソースへの参照を追加します。


  - リスト ビュー ウィジェットのプレースホルダーを提供します。


  - コントロールのランタイムを初期化します。


  - Office コントロール ランタイムの **renderAll** メソッドを実行します。



 ```

<!DOCTYPE html>
<html>
<head>
    <!-- IE9 or superior -->
    <meta http-equiv="x-ua-compatible" content="IE=10">
    <title>Desktop List View HTML Markup</title>

    <!-- Controls Specific CSS File -->
    <link rel="stylesheet" type="text/css" href="/Scripts/Office.Controls.css" />

    <!-- Ajax, jQuery, and utils -->
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js.js">
    </script>
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js">
    </script>
    <script type="text/javascript">

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

    <!-- Cross-Domain Library and Office controls runtime -->
    <script type="text/javascript">
        //Register namespace and variables used through the sample
        Type.registerNamespace("Office.Samples.ListViewBasic");
        //Retrieve context tokens from the querystring
        Office.Samples.ListViewBasic.appWebUrl =
            decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));
        Office.Samples.ListViewBasic.hostWebUrl =
            decodeURIComponent(getQueryStringParameter("SPHostUrl"));
        Office.Samples.ListViewBasic.ctag =
            decodeURIComponent(getQueryStringParameter("SPClientTag"));

        //Pattern to dynamically load JSOM and the cross-domain library
        var scriptbase =
            Office.Samples.ListViewBasic.hostWebUrl + "/_layouts/15/";

        //Get the cross-domain library
        $.getScript(scriptbase + "SP.RequestExecutor.js", 
            //Get the Office controls runtime and 
            //  continue to the createControl function
            function () {
                $.getScript("../Scripts/Office.Controls.js", createControl);
            }
        );
    </script>

    <!-- List View -->
    <script 
        src="../Scripts/Office.Controls.ListView.debug.js" 
        type="text/javascript">
    </script>

    <!-- SharePoint CSS -->
    <script type="text/javascript">
        document.addEventListener("DOMContentLoaded", function () {
            // The resource files are in a URL in the form:
            // web_url/_layouts/15/Resource.ashx
            var scriptbase =
                Office.Samples.ListViewBasic.appWebUrl + "/_layouts/15/";

            // Dynamically create the invisible iframe.
            var blankiframe;
            var blankurl;
            var body;
            blankurl =
                Office.Samples.ListViewBasic.appWebUrl + "/Pages/blank.html";
            blankiframe = document.createElement("iframe");
            blankiframe.setAttribute("src", blankurl);
            blankiframe.setAttribute("style", "display: none");
            body = document.getElementsByTagName("body");
            body[0].appendChild(blankiframe);

            // Dynamically create the link element.
            var dclink;
            var head;
            dclink = document.createElement("link");
            dclink.setAttribute("rel", "stylesheet");
            dclink.setAttribute("href",
                                scriptbase +
                                "defaultcss.ashx?ctag=" +
                                Office.Samples.ListViewBasic.ctag
                                );
            head = document.getElementsByTagName("head");
            head[0].appendChild(dclink);
        }, false);
    </script>
</head>
<body>
    Basic List View sample (HTML markup declaration):
    <div id="ListViewDiv"
         data-office-control="Office.Controls.ListView"
         data-office-options='{"listUrl" : getListUrl()}'>
    </div>

    <script type="text/javascript">
        function createControl() {
            //Initialize Controls Runtime
            Office.Controls.Runtime.initialize({
                sharePointHostUrl: Office.Samples.ListViewBasic.hostWebUrl,
                appWebUrl: Office.Samples.ListViewBasic.appWebUrl
            });

            //Render the widget, this must be executed after the
            //placeholder DOM is loaded
            Office.Controls.Runtime.renderAll();
        }

        function getListUrl() {
            return Office.Samples.ListViewBasic.appWebUrl +
                    "/_api/web/lists/getbytitle('Announcements')";
        }
    </script>
</body>
</html>
 ```


> **メモ**
> 上記のコード例では、ホスト Web とアドイン Web の URL を明示的に指定して Office コントロール ランタイムを初期化しています。しかし、アドイン Web とホスト Web の URL が **SPAppWebUrl** と **SPHostUrl** クエリ文字列パラメーターで指定されている場合、空のオブジェクトを渡して、これらのパラメータを自動的に取得できます。 **SPAppWebUrl** と **SPHostUrl** パラメータは、 **{StandardTokens}** トークンの使用時にクエリ文字列に組み込まれます。




以下の例は、初期化メソッドに空のオブジェクトを渡す方法を示しています。





```

// Initialize with an empty object and the code
// will attempt to get the tokens from the
// query string directly.
Office.Controls.Runtime.initialize({});```


### ソリューションを構築して実行するには


1. F5 キーを押します。

    > **メモ**
      > F5 キーを押すと、Visual Studio がソリューションを構築して、アドインを展開し、アドインのアクセス許可ページを開きます。 
2. [ **信頼する**] ボタンをクリックします。


3. [ **サイト コンテンツ**] ページの [アドイン] アイコンを選択します。


このサンプルをコード ギャラリーからダウンロードすることもできます。「 [アドインで実験的なデスクトップ リスト ビュー ウィジェットを使用する](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)」のコード サンプルを参照してください。




## 

この記事では、HTML を使ってデスクトップ リスト ビュー ウィジェットをアドインで使用する方法を説明しています。以下のシナリオを参照して、ウィジェットの詳細を確認することもできます。




### JavaScript を使用してデスクトップ リスト ビュー ウィジェットを宣言する

設定によっては、HTML ではなく JavaScript を使用してウィジェットを宣言することもできます。その場合は、以下のマークアップをウィジェットのプレースホルダーとして使用できます。



```HTML

<div id="ListViewDiv"></div>```

以下の JavaScript コードを使用してリスト ビューをインスタンス化します。





```
new Office.Controls.ListView(
    document.getElementById("ListViewDiv"), {
        listUrl: Office.Samples.ListViewBasic.appWebUrl + "/_api/web/lists/getbytitle('Announcements')"
    });```

タスクの実行方法を示すコード サンプルについては、「 [アドインで実験的なデスクトップ リスト ビュー ウィジェットを使用する](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)」コード サンプルの **JSSimple.html** ページを参照してください。




### データを表示するビューを指定する

SharePoint リストの既存のビューを指定すると、リスト ビュー ウィジェットでそのビュー指定を使用してデータを表示できます。



HTML マークアップを使用してウィジェットを宣言する場合は、以下の構文を使用してビューを指定します。





```

<div id="ListViewDiv"
        data-office-control="Office.Controls.ListView"
        data-office-options="{listUrl: 'list URL',
                            viewID: 'GUID'
                            }">
</div> 
```

JavaScript を使用してウィジェットを宣言する場合は、以下の構文を使用してビューを指定します。





```

new Office.Controls.ListView(
    document.getElementById("ListViewDiv"), {
        listUrl: "list URL",
        viewID: "GUID"
    });```


## まとめ

実験的なデスクトップ リスト ビュー ウィジェットを使用して、SharePoint リストのデータを表示できます。ウィジェットは、読み取り専用モードでデータを表示します。アイデアやコメントを  [Office デベロッパー プラットフォーム UserVoice サイト](http://officespdev.uservoice.com/)にお寄せください。




## その他の技術情報
<a name="bk_addresources"> </a>


-  [Office Web Widgets - Experimental の概要](office-web-widgetsexperimental-overview.md)


-  [Office Web Widgets - Experimental のライセンス条項](office-web-widgetsexperimental-license-terms.md)


-  [Office Web Widgets - Experimental の NuGet ギャラリー ページ](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)


-  [コード サンプル: アドインで実験的なデスクトップ リスト ビュー ウィジェットを使用する](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)


-  [SharePoint アドインでデスクトップ リスト ビューの実験用ウィジェットを使用する](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md)



