---
title: プロバイダー向けのホスト型アドインにカスタム ボタンを含める
ms.prod: SHAREPOINT
ms.assetid: 58932389-0100-47ee-9d33-1b4321d3f462
---


# プロバイダー向けのホスト型アドインにカスタム ボタンを含める
プロバイダー ホスト型 SharePoint アドイン にカスタム リボンのボタンを含める方法について説明します。
これは、プロバイダー ホスト型 SharePoint アドイン の開発の基本を扱っているシリーズ記事の 3 番目です。 [SharePoint アドイン](sharepoint-add-ins.md) およびこのシリーズの前の記事についてまず理解しておく必要があります。
  
    
    


-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の外観を付与する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  

> **メモ**
> プロバイダー ホスト型アドインに関するこのシリーズを続けて学習してきた方は、このトピックの続きで使用できる Visual Studio ソリューションをお持ちです。また、 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) でリポジトリをダウンロードして、BeforeRibbonButton.sln ファイルを開くこともできます。
  
    
    

SharePoint アドイン には、カスタム アクションを組み込むことができます。これは、カスタム メニュー項目またはリボン ボタンを指す SharePoint の用語です。この記事では、SharePoint のリストをリモート データベースと同期するカスタム ボタンを作成する方法について説明します。
## ホスト Web サイト上にカスタム リストを作成する

カスタム ボタンは、ローカル ストアの従業員を記録する特定のリストのリボン上に配置します。このシリーズの後半の記事では、プログラム的にホスト Web サイトにカスタム リストを追加する方法について説明します。 
  
    
    

1. Fabrikam Hong Kong ストアのホーム ページから、[ **サイト コンテンツ | アドインを追加 | カスタム リスト**] に移動します。 
    
  
2. [ **カスタム リストの追加**] ダイアログで、[ローカル従業員] の名前を指定して [ **作成**] を押します。 
    
  
3. [ **サイト コンテンツ**] ページで、[ **ローカル従業員**] のリストを開きます。
    
  
4. リボンの [ **リスト**] タブを開いてから、[ **リストの設定**] ボタンをクリックします。
    
  
5. [ **リストの設定**] ページの [ **列**] セクションで、[ **タイトル**] 列をクリックします。 
    
  
6. [ **列の編集**] フォームで、[ **列名**] を Title から「名前」に変更し、[ **OK**] をクリックします。
    
  
7. [ **設定値**] ページで、[ **列の作成**] をクリックします。
    
  
8. [ **列の作成**] フォームで、以下を実行します。
    
1. [ **列名**] に「企業 DB に追加」と入力します。
    
  
2. タイプを [ **はい / いいえ (チェック ボックス)**] に設定します。
    
  
3. [ **既定値**] を [ **いいえ**] に設定します。
    
  
4. [ **OK**] をクリックします。[ **設定値**] ページに戻ります。
    
  
9. [ **サイト コンテンツ**] をクリックして、[ **サイト コンテンツ**] ページを開きます。新しいリストのタイルがあるので、それを開きます。
    
  
10. [ **新しいアイテム**] をクリックして、アイテム作成フォームで名前を入力します。ただし、[ **企業 DB に追加**] はチェック *しないでください*  。[ **保存**] をクリックします。リストは、次のようになります。
    
!\[項目が 1 つの地元の従業員の一覧。名前は Brayden Sawtell です。"会社の DB に追加済み" 列の値は No です。](images/a3371859-e42f-49ea-8f17-48d8a248b075.PNG)
  

  

  

## カスタム ボタンの追加

このセクションでは、リストのリボンにボタンを配置するアドインにマークアップを含めます。ユーザーがリストにある従業員を強調表示してボタンをクリックすると、その従業員の名前が企業のデータベースに追加され、従業員の [ **企業 DB に追加**] フィールドが [いいえ] から [はい] に切り替わります。
  
    
    

1.  *Visual Studio が開いている場合は、一度閉じてから*  チェーン ストア ソリューションをもう一度開き、Visual Studio が新しいリストを検出できるようにする必要があります。(Visual Studio を管理者として実行します。)
    
    > **メモ**
      >  Visual Studio のスタートアップ プロジェクトの設定では、ソリューションが開かれるたびに、既定値に戻ってしまう傾向があります。このシリーズの記事のサンプル ソリューションを再度開いたら、すぐに次の手順を必ず実行してください。>  [ **ソリューション エクスプローラー**] の上位にあるソリューション ノードを右クリックして、[ **スタートアップ プロジェクトを設定**] を選択します。 >  [ **操作**] 列で、3 つのプロジェクトすべてが [ **開始**] に設定されていることを確認します。 
2. [ **ソリューション エクスプローラー**] の [ **ChainStore**] プロジェクトを右クリックして、[ **追加 | 新しいアイテム**] を選択します。 
    
  
3. [ **新しいアイテムの追加**] ダイアログ ボックスで、[ **リボン カスタム アクション**] を選択し、「AddEmployeeToCorpDB 」という名前を付けて、[ **追加**] をクリックします。
    
  
4. ダイアログ ボックスが開き、次の 3 つの質問がたずねられます。次のように回答してください。
    

|**質問**|**次のように回答します。**|
|:-----|:-----|
|**カスタム アクションをどこに公開しますか?** <br/> |ホスト Web <br/> |
|**カスタム アクションのスコープはどこに設定しますか?** <br/> |リスト インスタンス <br/> |
|**カスタム アクションのスコープを特定のどのアイテムに設定しますか?** <br/> |ローカルの従業員 <br/> |
   
5. [ **次へ**] をクリックすると、さらに 3 つの質問が表示されます。
    

|**質問**|**次のように回答します。**|
|:-----|:-----|
|**コントロールをどこに配置しますか?** <br/> |Ribbon.ListItem.Actions <br/> |
|**ボタン コントロールのラベルの文字列は何ですか?** <br/> |企業 DB に追加 <br/> |
|**ボタン コントロールはどこにナビゲートしますか?** <br/> |ChainStoreWeb\\Pages\\EmployeeAdder.aspx (これは、分離コードが従業員をデータベースに追加するページです。) <br/> |
   
6. [ **完了**] をクリックします。
    
    カスタム アクションを定義する elements.xml ファイルがプロジェクトに追加され、開かれました。ほとんどの場合、このファイルをブラック ボックスとして処理できます。このシリーズの後半の記事までは、変更を加える必要はありません。ここでは、次の点だけに注意してください。
    
  - **CommandUIDefinition** 要素の **Location** 属性には、値 `Ribbon.ListItem.Actions.Controls_children` があります。この 2 番目の部分 `ListItem` は、ボタンが配置されるリボンのタブ (ただし、タブの正確な表示名でない場合がある) を識別し、3 番目の部分 `Actions` は、ボタンが配置されるリボンのセクションの名前です。
    
  
  - **CommandUIHandler** 要素の **CommandAction** 属性は、プレースホルダー `~remoteAppUrl` で始まります。これは、ボタンが配置されたときにリモート Web アプリケーションの URL に置き換わります。
    
  
  - いくつかのクエリ パラメーターが、かっこ { } で囲まれたプレースホルダーの値で **CommandAction** 値に追加されています。これらのプレースホルダーは実行時に解決されます。リボンのカスタム ボタンを押す前に、これらのパラメーターの 1 つが、ユーザーによって選択されているリスト アイテムの ID になることに注意してください。
    
  
7. [ **ChainStoreWeb**] プロジェクトで、 **Pages/EmployeeAdder.aspx** ファイルを開きます。これには UI が含まれていないことに注意してください。アドインは一種の Web サービスとしてこのページを使用します。これが可能であるのは、ASP.NET **System.Web.UI.Page** クラスが **System.Web.IHttpHandler** を実装するため、また ** Page_Load** イベントはページが要求されたときに自動的に実行されるためです。
    
  
8. 分離コード ファイル **Pages/EmployeeAdder.aspx.cs** を開きます。リモート データベースに従業員を追加するメソッド `AddLocalEmployeeToCorpDB` が既にあります。これは **SharePointContext** オブジェクトを使用してホスト web の URL を取得し、アドインはそれをそのテナントの識別子として使用します。したがって、 **Page_Load** メソッドが最初に実行する必要があるのは、このオブジェクトの初期化です。アドインのスタート ページの読み込み時にオブジェクトは作成されて、セッションにキャッシュされるため、次のコードを **Page_Load** メソッドに追加してください。( **SharePointContext** オブジェクトは SharePointContext.cs ファイルで定義されています。このファイルは Office Developer Tools for Visual Studio がアドイン ソリューションの作成時に生成したものです)。
    
 ```cs
  
spContext = Session["SPContext"] as SharePointContext;
 ```

9.  `AddLocalEmployeeToCorpDB` メソッドは、従業員の名前をパラメーターと見なすため、次の行を **Page_Load** メソッドに追加してください。 `GetLocalEmployeeName` メソッドは後の手順で作成します。
    
 ```cs
  // Read from SharePoint
string employeeName = GetLocalEmployeeName();
 ```

10. この行で、 `AddLocalEmployeeToCorpDB` メソッドの呼び出しを追加します。
    
 ```cs
  
// Write to remote database
AddLocalEmployeeToCorpDB(employeeName);
 ```

11. 名前空間  `Microsoft.SharePoint.Client` のファイルに **using** ステートメントを追加します。(Office Developer Tools for Visual Studio は、Microsoft.SharePoint.Client アセンブリをその作成時に **ChainStoreWeb** プロジェクトに含めました。)
    
  
12. 続いて、次のメソッドを  `EmployeeAdder` クラスに追加します。SharePoint .NET クライアント側オブジェクト モデル (CSOM) については、MSDN の他の場所の詳細に説明されているため、このシリーズの記事を終えた後に調べてみることをお勧めします。この記事では、 **ListItem** クラスは SharePoint リスト内のアイテムを表すことと、アイテム内のフィールドの値は "indexer" 構文で参照できることに注意してください。また、フィールド名を「名前」に変更していても、コードはフィールドを「Title」として参照していることに注意してください。これはコード内では、フィールドは常に表示名ではなく *内部*  名で参照されるためです。フィールドの内部名は、フィールドの作成時に設定され、変更することはできません。 `TODO1` については、後の手順で説明します。
    
 ```cs
  
private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    // TODO1: Initialize the localEmployee object by getting  
    // the item from SharePoint.
 
    return localEmployee["Title"].ToString();
}
 ```

13. このコードを SharePoint から取得するには、まずリスト アイテムの ID が必要になります。次の宣言を  `spContext` オブジェクトの宣言のすぐ下にある、 `EmployeeAdder` クラスに追加します。
    
 ```cs
  
private int listItemID;
 ```

14. 次のメソッドを  `EmployeeAdder` クラスに追加し、クエリ パラメーターからリスト アイテムの ID を取得します。
    
 ```cs
  private int GetListItemIDFromQueryParameter()
{
    int result;
    Int32.TryParse(Request.QueryString["SPListItemId"], out result);
    return result;
}
 ```

15.  `listItemID` 変数を初期化するには、 `spContext` 変数を初期化する行のすぐ下にある **Page_Load** メソッドに次の行を追加します。
    
 ```cs
  
listItemID = GetListItemIDFromQueryParameter();
 ```

16.  `GetLocalEmployeeName` で、 `TODO1` を次のコードに置き換えます。しばらくの間、このコードを単なるブラック ボックスと見なし、カスタム ボタンの処理を続行します。このコードについては、SharePoint クライアント側オブジェクト モデルについて扱っている、このシリーズの次の記事で説明します。
    
 ```cs
  using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
    localEmployee = localEmployeesList.GetItemById(listItemID);
    clientContext.Load(localEmployee);
    clientContext.ExecuteQuery();
}

 ```


    メソッド全体は、次のようになります。
    


 ```cs
  
private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
 ```

17. EmployeeAdder ページは実際にはレンダリングしないため、以下を **Page_Load** メソッドの最後の行として追加します。これにより、ブラウザーは [ **ローカル従業員**] リストのリスト ビュー ページに戻ります。
    
 ```cs
  
// Go back to the Local Employees page
Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);

 ```


    **Page_Load** メソッド全体は次のようになります。
    


 ```cs
  
protected void Page_Load(object sender, EventArgs e)
{
    spContext = Session["SPContext"] as SharePointContext;
    listItemID = GetListItemIDFromQueryParameter();

    // Read from SharePoint
    string employeeName = GetLocalEmployeeName();

    // Write to remote database
    AddLocalEmployeeToCorpDB(employeeName);

    // Go back to the preceding page
    Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);
}
 ```


## ホスト Web リストの読み取りアクセス許可を要求する

これまで見てきたように、SharePoint では、アドインのインストール時に、そのアドインにホスト Web へのアクセス許可を付与するように求められます。F5 キーを押すたびにアドインが再インストールされてきました。ここまでは、アドインには必要最小限のアクセス許可が付与されていましたが、 `GetLocalEmployeeName` メソッドでは、ホスト Web サイトのリストを読み取るアクセス許可が必要になります。アドインは、アドイン マニフェストを使用して、必要なアクセス許可を SharePoint に通知します。次の手順に従ってください。
  
    
    

1. [ **ソリューション エクスプローラー**] で、[ **ChainStore**] プロジェクトの AppManifest.xml ファイルを開きます (アドインは以前「アプリ」と呼ばれていたため、このファイルは AppManifest と呼ばれます)。マニフェスト デザイナーが開きます。
    
  
2. [ **アクセス許可**] タブを開き、[ **スコープ**] 列の下の空のセルをクリックして、ドロップ ダウンから [ **リスト**] を選択します。
    
  
3. [ **アクセス許可**] フィールドで、ドロップダウンから [ **読み取り**] を選択します。
    
  
4. [ **プロパティ**] フィールドを空のままにし、ファイルを保存します。[ **アクセス許可**] タブは次のようになります。
    
!\[アドイン マニフェスト デザイナーの [アクセス権限] タブには、一覧の範囲と読み取るアクセス権限が表示されます。](images/8dd2a25f-103a-42af-aa88-c8ec796315db.PNG)
  

  

  

## アドインを実行してボタンをテストする


  
    
    

1. F5 キーを使用して、アドインを展開し、実行します。Visual Studio は、IIS Express でリモート Web アプリケーションをホストし、SQL Express で SQL データベースをホストします。また、テスト用の SharePoint サイト上にアドインを一時的にインストールし、アドインを即時で実行します。開始ページが表示される前に、アドインへのアクセス許可を付与するように求められます。ここでは、次のスクリーン ショットに示されているように、プロンプトにドロップダウンが表示され、アプリで読み取る必要があるリストを選択できます。 
    
!\[SharePoint アドインのアクセス許可は、"Let it read items in the list" というラベルの付いたドロップ ダウンリストで選ばれた「現地の従業員」という名前の一覧を表示します。](images/84e8b42c-4800-4947-acbd-21c6f096f4ea.PNG)
  

  

  
2. リストから [ **ローカル従業員**] を選択して、[ **信頼する**] をクリックします。
    
  
3. アドインのスタート ページが開いたら、上部のクロム コントロールにある [ **サイトに戻る**] をクリックします。
    
  
4. Web サイトのホーム ページから、[ **サイト コンテンツ | ローカルの従業員**] に移動します。リスト ビューのページが開きます。
    
  
5. 数名の従業員をリストに追加します。 *[ **企業 DB に追加**] チェック ボックスはオンにしないでください。* 
    
  
6. リボンで、[ **アイテム**] タブを開きます。タブの [ **アクション**] セクションに、カスタム ボタン [ **企業 DB に追加**] があります。
    
  
7. リストでアイテムを選択します。ページおよびリボンは、次のようになります。
    
!\[現地の従業員の一覧。1 つの項目が強調表示されています。その上にあるのはリボンで、"会社の DB に追加する" という名前のボタンは [アクション] セクションにあります。](images/797a5ceb-7291-4b62-8075-2bb6a1b8e8a1.PNG)
  

  

  
8. [ **企業 DB へ追加** ] ボタンをクリックします。 * **アイテムを最初に選択する必要があります。*** 
    
  
9. EmployeeAdder ページの **Page_Load** メソッドが元にリダイレクトするため、ページは再読み込みされているように見えます。
    
  
10. ブラウザーの [戻る] ボタンを 2 回使用して、アドインのスタート ページに戻ります。 
    
  
11. [ **従業員の表示**] をクリックすると、追加した従業員が従業員リストに入力されます。次のようになります。
    
!\[以前のステップで選ばれたのと同じ従業員を示す、アドインのスタート ページの会社の従業員の一覧。](images/4a300a4e-f479-4f63-b536-6315c5d9ba4d.PNG)
  

  

  
12. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は以前のバージョンのアドインを取り消し、最新のアドインをインストールします。
    
  
13. 別の記事でこのアドインと Visual Studio ソリューションを操作します。しばらく操作してそれが終了したら、前回のアドインを取り消すとよいでしょう。[ **ソリューション エクスプローラー**] でプロジェクトを右クリックして、[ **取り消し**] を選択します。
    
  

## 
<a name="Nextsteps"> </a>

 次の記事では、コーディングからは少し離れて、SharePoint クライアント側オブジェクト モデルの概要について説明します: [SharePoint オブジェクト モデルの概要を簡単に説明する](get-a-quick-overview-of-the-sharepoint-object-model.md)。
  
    
    

