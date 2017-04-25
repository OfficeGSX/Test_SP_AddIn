---
title: プロバイダー向けのホスト型アドインに初回実行時のロジックを追加する
ms.prod: SHAREPOINT
ms.assetid: 649c06b9-3612-439a-acb3-041e5f5f01f3
---


# プロバイダー向けのホスト型アドインに初回実行時のロジックを追加する
プロバイダー ホスト型 SharePoint アドイン に「初回実行」コードを含める方法を説明します。
これは、プロバイダー ホスト型 SharePoint アドイン の開発の基本に関する記事のシリーズの 8 番目です。 [SharePoint アドイン](sharepoint-add-ins.md) とこのシリーズの前の記事をよく理解しておいてください。
  
    
    


-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の外観を付与する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [プロバイダー向けのホスト型アドインにカスタム ボタンを含める](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [SharePoint オブジェクト モデルの概要を簡単に説明する](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の書き込み操作を追加する](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインにアドインの一部を含める](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインでアドイン イベントを処理する](handle-add-in-events-in-the-provider-hosted-add-in.md)
    
  

> **メモ**
> プロバイダー ホスト型アドインに関するこのシリーズを続けて学習している場合は、このトピックを続行するために利用できる Visual Studio ソリューションがあります。また、 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) でリポジトリをダウンロードして、BeforeFirstRunLogic.sln ファイルを開くこともできます。
  
    
    

この記事では、チェーンストア SharePoint アドイン の開始ページに、アドインの現在のインスタンスが初回実行中であるかどうかを確認するコードを追加します。初回である場合、コードは [ **ローカル従業員**] のリストとカスタムのリボン ボタンを配置します。
## SharePoint コンポーネントを配置するための基本クラスの作成


  
    
    

> **メモ**
>  Visual Studio スタートアップ プロジェクトの設定は、ソリューションを再び開くと既定値に戻ることがよくあります。この記事のシリーズのサンプル ソリューションを再度開く場合は、すぐに次の手順を実行してください。> **ソリューション エクスプローラー**の上部にあるソリューション ノードを右クリックして、[ **スタートアップ プロジェクト**] を選択します。 >  [ **操作**] 列で、3 つのプロジェクトすべてが [ **開始**] に設定されていることを確認します。 
  
    
    


1. **ソリューション エクスプローラー**の **ChainStoreWeb** プロジェクトで、 **ユーティリティ** フォルダーを右クリックして、[ **項目の追加 | 既存の項目**] を選択します。
    
  
2. **ファイル エクスプローラー**が開くので、ソリューションのフォルダーである **ChainStoreWeb** フォルダーに移動し、 **ユーティリティ** フォルダーを開きます。
    
  
3. SharePointComponentDeployer.cs を選択し、[ **追加**] を押します。
    
  
4. ファイル SharePointComponentDeployer.cs を開きます。企業データベースの **テナント** 表でアドインのバージョンを取得および設定する、1 つの静的クラスと 2 つの静的メソッドがあります。このシリーズの記事は、ASP.NET や SQL Server/Azure のプログラミングを説明することが目的ではないため、これらのメソッドについては説明しません。
    
  
5. 次の **using** ステートメントをファイルの先頭に追加します。
    
 ```
  
using System.Web;
using System.Linq;
using System.Collections.Generic;
using Microsoft.SharePoint.Client;
 ```

6.  `SharePointComponentDeployer` クラスの先頭に、次の 2 つの静的フィールドを追加します。これらはいずれも、アドインの開始ページの **Page_Load** メソッドで初期化されます。そのコードは後で追加します。最初のフィールドは、SharePoint で CRUD 操作を実行するのに必要な **SharePointContext** オブジェクトを保持します。2 つ目は、ホスト Web にインストールされたアドインのバージョン番号を保持します。この値は最初に、インストール ハンドラーがテナントを登録するときに企業の **テナント** テーブルに記録される 既定値 ( **0000.0000.0000.0000** ) とは異なる値に設定します。たとえば、最初のバージョンのアドインは **1.0.0.0** です。
    
 ```cs
  
internal static SharePointContext sPContext;
internal static Version localVersion;
 ```

7. 企業の **テナント** テーブルに現在記録されているアドインのバージョンを保持するために、次の静的プロパティを作成します。この値を取得および設定するために、既にファイルに入っている 2 つのメソッドを使用します。
    
 ```cs
  
internal static Version RemoteTenantVersion
{
    get
    {
        return GetTenantVersion();
    }
    set
    {
        SetTenantVersion(value);
    }
}
 ```

8. 次に、以下の  `IsDeployed` プロパティを作成します。このコードについて、以下の点に注意してください。
    
  - アドインの開始ページの **Page_Load** メソッドは、このプロパティの値を使用し、アドインが初回実行中であるかどうかを判断します。値が **false** であれば、現在のホスト Web 上でこれまでアドインが実行されていなかったので、コンポーネントを配置する必要があります。
    
  
  - **テナント** テーブルに登録されているバージョン番号が、実際にインストールされているバージョンよりも小さいかどうかが条件です。アドインの初回実行では小さくなります。後の手順で作成するコードは、実際にインストールされているものと同じバージョンに **テナント** テーブルのバージョンを設定するため、アドインをもう一度実行すると、 `IsDeployed` が **true** を返すので配置ロジックがもう一度実行されることはありません。
    
  

 ```cs
  
public static bool IsDeployed
{
    get
    {
        if (RemoteTenantVersion < localVersion)
            return false; 
        else
            return true; 
    }
}
 ```

9. 次のメソッドを  `SharePointComponentDeployer` クラスに追加します。メソッドの最後の処理は、企業のデータベースに登録されたテナントのバージョン ( **0000.0000.0000.0000** ) がホスト Web のアドインの実際のバージョン ( **1.0.0.0** ) に一致するよう更新することです。このメソッドは、後の手順で完成させます。
    
 ```cs
  
internal static void DeployChainStoreComponentsToHostWeb(HttpRequest request)
{
    // TODO4: Deployment code goes here.

    RemoteTenantVersion = localVersion;
}
 ```


> **メモ**
> 「アドインが初回実行であるか」という簡単な「はい/いいえ」の質問の答えを判断するために、なぜアドインがバージョン番号を使用して「未満」のテストを実行するのか不思議に思ったかもしれません。インストール ハンドラーで「未実行」に設定され、SharePoint コンポーネントが配置された後、初回実行ロジックで「1 回実行済み」に変更される、 **テナント** テーブルの単純な文字列フィールドを使用することもできます。> このチェーンストアのアドインでは、単純なテストが機能します。ただし、一般的にはバージョン番号を使用することをお勧めします。これは、運用アドインでは、将来、そのまま更新される、つまり、既にインストールされた後に更新される可能性があるためです。その場合、アドイン ロジックは未実行および1 回実行済みよりも詳細な区別が必要です。たとえば、1.0.0.0 から 2.0.0.0 のアップグレードでホスト Web にリストを追加するとします。これは、更新イベント ハンドラー、または「更新後の初回実行」ロジックで実行できます。いずれの方法でも、配置ロジックは新しいコンポーネントを配置する必要がありますが、以前のバージョンのアドインで配置されたコンポーネントを再配置しないようにする必要もあります。バージョン番号 1.0.0.0 は、バージョン 1.0.0.0 のコンポーネントが配置されているものの、「更新後の初回実行」ロジックがまだ行われていないことを示します。 
  
    
    


## 基本的なスタートアップロジックの追加


  
    
    

1. SharePoint ホスト Web は、インストールされているアドインがどのバージョンであるかをリモート Web アプリケーションに通知する必要があります。これを行うためにクエリ パラメーターを使用します。 **ChainStore** プロジェクトにある AppManifest.xml ファイルを開きます。デザイナーで、プレースホルダー **{StandardTokens}** が [ **クエリ文字列**] ボックスの値として表示されます。文字列 "&amp;SPAddInVersion=1.0.0.0" を最後に追加します。マニフェスト デザイナーは、次のようになります。 *クエリ文字列で渡すバージョン番号は、デザイナーの [ **バージョン**] ボックスの値と一致している必要があります。*  (アドインを更新する場合、これらの 2 つの値を上げて同じにしておくことが行うべきタスクの 1 つとなります。)
    
!\[マニフェスト デザイナーの [全般] タブです。バージョン ボックスの値は、1 0 0 0 です。クエリ文字列ボックスには、"{StandardTokens}&amp;SPAddInVersion=1.0.0.0" と記載されています。](images/db71c411-10c5-43d8-bb5e-3388d2f6f7bc.PNG)
  

  

  
2. CorporateDataViewer.aspx.cs ファイルを開き、 **Page_Load** メソッドの `spContext` オブジェクトを初期化する行の下に次のコードを追加します。このコードについて、以下の点に注意してください。
    
  - 最初に、静的  `SharePointComponentDeployer` クラスの 2 つの静的フィールドを設定します。 `SharePointComponentDeployer` のコードは SharePoint を呼び出しているため **SharePointContext** オブジェクトを渡し、 `localVersion` プロパティを設定するために追加したクエリ パラメーターを使用します。
    
  
  -  `IsDeployed` が true の場合、つまり、「初回実行」ロジックが既に実行されている場合は何もしません。そうでない場合、配置メソッドを呼び出し、ASP.NET の要求オブジェクトを渡します。
    
  

 ```cs
  
SharePointComponentDeployer.sPContext = spContext;
SharePointComponentDeployer.localVersion = new Version(Request.QueryString["SPAddInVersion"]);

if (!SharePointComponentDeployer.IsDeployed)
{
    SharePointComponentDeployer.DeployChainStoreComponentsToHostWeb(Request);
}
 ```


## SharePoint リストをプログラミングによって配置する


  
    
    

1. SharePointComponentDeployer.cs ファイルで、 `TODO4` を次の行で置き換えます。このメソッドは、次の手順で作成します。
    
 ```cs
  
CreateLocalEmployeesList();
 ```

2. 以下のメソッドを  `SharePointComponentDeployer` クラスに追加します。このコードについては以下の点に注目してください。
    
  - **ExecuteQuery** の 2 つの呼び出しがあります。1 つ目は、リストが既に存在するかどうかを判断するために必要です。2 つ目は、リストの作成作業を行います。
    
  
  - **ClientContext.LoadQuery** メソッドは **ClientContext.Load** と似ています。ただし、リストなどのエンティティをクライアントに渡す代わりに、クエリの列挙可能な結果を渡します。
    
  

 ```cs
  private static void CreateLocalEmployeesList()
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
        var query = from list in clientContext.Web.Lists
                    where list.Title == "Local Employees"
                    select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();

        if (matchingLists.Count() == 0)
        {
           // TODO5: Create the list 

           // TODO6: Rename the Title field on the list 

           // TODO7: Add "Added to Corporate DB" field to the list 

           clientContext.ExecuteQuery();
        }
    }
}
 ```

3.  `TODO5` を次のコードに置き換えます。このコードについて、以下の点に注意してください。
    
  - **ListCreationInformation** クラスは、このシリーズの前の記事にあった **ListItemCreationInformation** クラスと似ています。この軽量クラスは、Web アプリケーションの情報を SharePoint に送信するのに完全な **List** クラスよりも適しています。
    
  
  - "To Do" リストのためのタスク型や予定表のためのイベント型など、リスト テンプレートには多数の型があります。[ **ローカル従業員**] リストは、最も簡単なジェネリック型に基づきます。
    
  
  - **ListCreationInformation.Url** プロパティは、ホスト Web に *相対的な*  リストの URL を保持します。"Lists/LocalEmployees" を指定すれば、コードはリストの 絶対 URL を https:// *{SharePointDomain}*  /hongkong/_layouts/15/start.aspx#/Lists/Local%20Employees に設定します。
    
  

 ```cs
  
ListCreationInformation listInfo = new ListCreationInformation();
listInfo.Title = "Local Employees";
listInfo.TemplateType = (int)ListTemplateType.GenericList;
listInfo.Url = "Lists/Local Employees";
List localEmployeesList = clientContext.Web.Lists.Add(listInfo);
 ```

4.  `TODO6` を、"Title" フィールド (列) のパブリック名を "Title" から "Name" に変更する次のコードで置き換えます。これは、リストを手動で作成したときに、 **リストの設定**ページで行ったことです。
    
 ```cs
  
Field field = localEmployeesList.Fields.GetByInternalNameOrTitle("Title");
field.Title = "Name";
field.Update();
 ```

5. また、[ **会社の DB に追加済み**] という名前のフィールドを手動で作成しています。これをプログラムによって実行するには、 `TODO7` の代わりに次のコードを追加します。このコードについて、以下の点に注意してください。
    
  - フィールドの主なプロパティは、XML BLOB によって指定されます。これは、SharePoint のレガシ アーキテクチャです。Web サイト、リスト、フィールド、コンテンツ タイプ、その他のほとんどの SharePoint コンポーネントは、XML として定義されます。この場合、フィールドの表示名、データ型、および既定値を指定しています。
    
  
  - 2 つ目のパラメーターは、フィールドがリストの既定のビューで表示されるか判断します。これを **true** に設定しています。
    
  
  - 3 つめのパラメーターを使用して、フィールドが追加されるコンテンツ タイプが何か判断できます。 **DefaultValue** を渡すことで、リストの既定のコンテンツ タイプにのみ追加されます。
    
  

 ```cs
  
localEmployeesList.Fields.AddFieldAsXml("<Field DisplayName='Added to Corporate DB'"
                                         +"Type='Boolean'>"
                                         + "<Default>FALSE</Default></Field>",
                                         true,
                                         AddFieldOptions.DefaultValue);
 ```

6. 既定では、[ **会社の DB に追加済み**] が [ **いいえ**] (false) ですが、アドインのカスタムのリボン ボタンは、従業員を企業のデータベースに追加した後で、[ **はい**] に設定します。このシステムは、ユーザーがフィールドの値を手動で変更できない場合にのみ最適に機能します。確実に機能しないようにするには、[ **ローカル従業員**] リストでアイテムを作成および編集するためのフォームでフィールドを非表示にします。これは、次に示すように、最初のパラメーターに属性をあと 2 つ追加することで行います。
    
 ```cs
  
localEmployeesList.Fields.AddFieldAsXml("<Field DisplayName='Added to Corporate DB'"
                                         + " Type='Boolean'"  
                                         + " ShowInEditForm='FALSE' "
                                         + " ShowInNewForm='FALSE'>"
                                         + "<Default>FALSE</Default></Field>",
                                         true,
                                         AddFieldOptions.DefaultValue);
 ```


     `CreateLocalEmployeesList` 全体は、次のようになります。
    


 ```cs
  
private static void CreateLocalEmployeesList()
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
        var query = from list in clientContext.Web.Lists
                    where list.Title == "Local Employees"
                    select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();

        if (matchingLists.Count() == 0)
        {
            ListCreationInformation listInfo = new ListCreationInformation();
            listInfo.Title = "Local Employees";
            listInfo.TemplateType = (int)ListTemplateType.GenericList;
            listInfo.Url = "LocalEmployees";
            List localEmployeesList = clientContext.Web.Lists.Add(listInfo);

            Field field = localEmployeesList.Fields.GetByInternalNameOrTitle("Title");
            field.Title = "Name";
            field.Update();

            localEmployeesList.Fields.AddFieldAsXml("<Field DisplayName='Added to Corporate DB'" 
                                                    + " Type='Boolean'"  
                                                   + " ShowInEditForm='FALSE' "
                                                   + " ShowInNewForm='FALSE'>"
                                                   + "<Default>FALSE</Default></Field>",
                                                    true,
                                                    AddFieldOptions.DefaultValue);
            clientContext.ExecuteQuery();
        }
    }
}
 ```


## カスタム ボタンをプロジェクトから一時的に削除する

次の記事で説明する技術的な理由により、作成したカスタム ボタンは、プログラムにより配置されるリストのリボンに配置している場合、修正しないとインストールできません。プロジェクトからこれを一時的に削除すると、初回実行ロジックをテストできます。次の記事でこれを元に戻します。
  
    
    
 **ソリューション エクスプローラー**の **ChainStore** プロジェクトで、 **AddEmployeeToCorpDB** ノードを右クリックして、[ **プロジェクトから除外**] を選択します。
  
    
    

## ホスト Web でリストを管理するアクセス許可の要求

アドインは、アイテムを既存のリストに追加するだけでなく、リストをホスト Web に追加しているため、アドインが要求しているアクセス許可を書き込みから管理にエスカレートする必要があります。次の手順に従います。
  
    
    

1. **ソリューション エクスプローラー**で、 **ChainStore** プロジェクトの AppManifest.xml ファイルを開きます。
    
  
2. [ **アクセス許可**] タブを開き、Web の [ **スコープ**] の値はそのままにしますが、[ **アクセス許可**] フィールドで、ドロップダウンから [ **管理**] を選択します。
    
  
3. ファイルを保存します。
    
  

## アドインを実行して "初回実行" ロジックをテストする


  
    
    

1. 香港ストアの Web サイトの [ **サイト コンテンツ**] ページを開き、 *[ **ローカル従業員**] リストを削除します。* 
    
  
2. F5 キーを使用して、アドインを展開して実行します。Visual Studio は、IIS Express でリモート Web アプリケーションをホストし、SQL Express で SQL データベースをホストします。これにより、テスト SharePoint サイト上のアドインの一時的なインストールも実行され、アドインもすぐに実行されます。開始ページが表示される前に、アドインへアクセス許可を付与するように求められます。
    
  
3. アドインの開始ページが開いたら、上部のクロム コントロールにある [ **サイトに戻る**] リンクを選択します。 
    
  
4. [ **サイト コンテンツ**] ページに移動します。[ **ローカル従業員**] リストは、初回実行時のロジックで追加されるため、既に存在します。
    
    > **メモ**
      > リストが存在しないか、初回実行コードが実行されていないことを示す他の兆候がある場合、F5 キーを押したときに [ **テナント**] テーブルが空の状態に戻っていない可能性があります。最も一般的な原因は、[ **ChainCorporateDB**] プロジェクトが Visual Studio でスタートアップ プロジェクトとして設定されなくなったことです。この問題を解決する方法については、この記事の上部にある注記を参照してください。また、「 [デバッグ セッションごとに企業データベースを再構築するように Visual Studio を構成する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md#Rebuild)」で説明されているように、データベースを再構築するよう構成済みであることを確認してください。 
5. リストを開き、アイテムを追加します。新しいアイテムのフォームで、[ **会社の DB に追加済み**] フィールドが存在していないため、手動で設定できないことに注意してください。これは、アイテムの編集フォームにも同様に当てはまります。 
    
!\[現地の従業員の一覧の新しいアイテムのフォームです。[会社の DB に追加済み] フィールドはフォームからなくなりました。名前フィールドと、[OK] と [キャンセル] のボタンだけがあります。](images/3fdc6752-4184-4928-9423-0bc7c0206c62.PNG)
  

  

  
6. ブラウザーの戻るボタンを使用して、アドインの開始ページに移動します。
    
  
7. 上部にあるクロム コントロールの歯車アイコンを押し、[ **アカウント設定**] を選択します。
    
  
8. [ **アカウント**] ページで、[ **アドインのバージョンを表示する**] ボタンを押します。初回実行ロジックによって変更されたため、バージョンが **1.0.0.0** と表示されます。
    
!\[バージョン番号が 1.0.0.0 のアカウント設定ページ。](images/4c6d82a7-7c40-4190-b7e3-1337275e1e60.PNG)
  

  

  
9. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は以前のバージョンのアドインを取り消し、最新のアドインをインストールします。
    
  
10. このアドインおよび他の記事の Visual Studio ソリューションを操作し、それが終了したら前回のアドインを取り消すとよいでしょう。 **ソリューション エクスプローラー**のプロジェクトを右クリックして、[ **取り消し**] を選択します。
    
  

## 
<a name="Nextsteps"> </a>

 次の記事では、[ **ローカル従業員**] リボンのカスタム ボタンを、リストがプログラムによって配置されているアドインに戻す方法を説明します。 [プロバイダー向けのホスト型アドインでカスタム ボタンをプログラムで展開する](programmatically-deploy-a-custom-button-in-the-provider-hosted-add-in.md)
  
    
    

