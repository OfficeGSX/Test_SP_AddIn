---
title: SharePoint ホスト型 SharePoint アドインにカスタム ページとスタイルを追加する
ms.prod: SHAREPOINT
ms.assetid: 91a20d79-318f-44ce-9877-3fa07d03aa09
---


# SharePoint ホスト型 SharePoint アドインにカスタム ページとスタイルを追加する
カスタム ページと CSS ファイルを SharePoint アドインに含める方法について説明します。
これは、SharePoint ホスト型の SharePoint アドインの開発の基本に関する記事のシリーズの 7 番目です。 [SharePoint アドイン](sharepoint-add-ins.md) とこのシリーズの前の記事をよく理解しておいてください。





-  [SharePoint ホスト型の SharePoint アドインの作成を始める](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [SharePoint 用の SharePoint ホスト型アドインを展開してインストールする](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)


-  [SharePoint 用の SharePoint ホスト型アドインにカスタム列を追加する](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [SharePoint ホスト型 SharePoint アドインにカスタム コンテンツ タイプを追加する](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [SharePoint ホスト型 SharePoint アドイン内のページに Web パーツを追加する](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)


-  [SharePoint ホスト型 SharePoint アドインにワークフローを追加する](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)



> **メモ**
> SharePoint ホスト型アドインに関するこのシリーズを学習している場合は、このトピックを続行するために利用できる Visual Studio ソリューションがあります。また、 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) でリポジトリをダウンロードして、BeforePage.sln ファイルを開くこともできます。




この記事では、新入社員オリエンテーション SharePoint アドインのヘルプ ページを追加して、カスタム CSS スタイル シートを使用するように構成します。 
## ページの追加


1. [ **ソリューション エクスプローラー**] で、[ **ページ**] フォルダーを右クリックして [ **追加**] > [ **新しい項目**] を選択します。[ **新しい項目の追加**] ダイアログで [ **Office/SharePoint**] の各ノードを開きます。


2. [ **ページ**] を選択し、Help.aspx という名前を付けます。


3. ファイルで 2 つの **asp:Content** 要素を探し、次の 3 番目の **asp:Content** マークアップをその間に追加します。

 ```HTML

<asp:Content ContentPlaceHolderID="PlaceHolderPageTitleInTitleArea" runat="server">
    Help
</asp:Content> 
 ```

4. ID が **PlaceholderAdditionalPageHead** の **asp:Content** 要素を探し、次のマークアップを追加します。

 ```HTML

<link rel="Stylesheet" type="text/css" href="../Content/App.css" />
 ```

5. ID が **PlaceHolderMain** の **asp:Content** 要素を探し、子要素があれば削除します。


6. 以下を同じ **asp:Content** 要素にコンテンツとして追加します。

 ```HTML
  <H3>Having a problem with the add-in?</H3>
<p> Call the help line for Fabrikam Add-ins:</p>
<p>1-555-555-5555</p>
 ```

7. ファイルを保存して閉じます。


8. Default.aspx ファイルを開きます。


9. ID が **PlaceHolderMain** の **asp:Content** 要素を探し、最後に次のマークアップを追加します。

 ```HTML

<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Pages/Help.aspx';"
    Text="Get help for the Employee Orientation add-in" /></p>

 ```

10. ファイルを保存して閉じます。



## スタイル クラスをスタイル シートに追加する






1. **ソリューション エクスプローラー**で、[ **コンテンツ**] フォルダーにある app.css ファイルを開き、次の行をファイルに追加します。

 ```

p {color: green;}
 ```

2. ファイルを保存して閉じます。



## アドインを実行してテストする






1. F5 キーを使用して、アドインを展開して実行します。Visual Studio が、テスト用 SharePoint サイトにアドインを一時的にインストールして、すぐにアドインを実行します。 


2. アドインの既定のページが開いたら、[ **新入社員オリエンテーションのアドインのヘルプを表示**] リンクをクリックして、[ **ヘルプ**] ページを開きます。 

    カスタム ページが開き、<p> タグ内に配置した 2 行が緑色になります。


   **[ヘルプ] ページ**



!\["ヘルプ" を含む SharePoint ページです。黒のヘッダー行があり、その後に緑色のテキスト行が 2 つあります。](images/2df51ab0-5b24-4a37-8b6a-6e95dbb1aeaa.PNG)





3. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は以前のバージョンのアドインを取り消し、最新のアドインをインストールします。


4. このアドインおよび他の記事の Visual Studio ソリューションを操作し、それが終了したら前回のアドインを取り消すとよいでしょう。[ **ソリューション エクスプローラー**] のプロジェクトを右クリックして、[ **取り消し**] を選択します。



## 
<a name="Nextsteps"> </a>

このシリーズの次の記事「 [SharePoint ホスト型 SharePoint アドインにカスタムのクライアント側レンダリングを追加する](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md)」では、カスタムのクライアント側レンダリングを SharePoint アドインのリスト列に追加します。




