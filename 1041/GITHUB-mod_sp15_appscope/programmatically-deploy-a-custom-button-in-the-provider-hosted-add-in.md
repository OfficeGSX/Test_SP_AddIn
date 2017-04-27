---
title: プロバイダー向けのホスト型アドインでカスタム ボタンをプログラムで展開する
ms.prod: SHAREPOINT
ms.assetid: 4d75d113-8dc8-4026-a297-c47b6d4d7008
---


# プロバイダー向けのホスト型アドインでカスタム ボタンをプログラムで展開する
カスタム リボン ボタンを同じプロバイダー ホスト型 SharePoint アドイン のカスタム リストにプログラムで登録する方法について説明します。
これは、プロバイダー ホスト型 SharePoint アドイン の開発の基本に関する記事のシリーズの 9 番目です。 [SharePoint アドイン](sharepoint-add-ins.md) とこのシリーズの前の記事をよく理解しておいてください。





-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [プロバイダー向けのホスト型アドインに SharePoint の外観を付与する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [プロバイダー向けのホスト型アドインにカスタム ボタンを含める](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [SharePoint オブジェクト モデルの概要を簡単に説明する](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [プロバイダー向けのホスト型アドインに SharePoint の書き込み操作を追加する](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [プロバイダー向けのホスト型アドインにアドインの一部を含める](include-an-add-in-part-in-the-provider-hosted-add-in.md)


-  [プロバイダー向けのホスト型アドインでアドイン イベントを処理する](handle-add-in-events-in-the-provider-hosted-add-in.md)


-  [プロバイダー向けのホスト型アドインに初回実行時のロジックを追加する](add-first-run-logic-to-the-provider-hosted-add-in.md)



> **メモ**
> プロバイダー ホスト型アドインに関するこのシリーズを続けて学習している場合は、このトピックを続行するために利用できる Visual Studio ソリューションがあります。でリポジトリをダウンロードして、BeforeContentType.sln ファイルを開くこともできます。 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) でリポジトリをダウンロードして、BeforeProgrammaticButton.sln ファイルを開くこともできます。




この記事では、ボタンを取得するリボンのリスト自体が全く同じアドインでプログラムで展開されている場合に、SharePoint アドイン にカスタム リボン ボタンを含める方法について説明します。
## プロジェクトにカスタム ボタンを再度追加する


> **メモ**
>  Visual Studio スタートアップ プロジェクトの設定は、ソリューションを再び開くと既定値に戻ることがよくあります。この記事のシリーズのサンプル ソリューションを再度開く場合は、すぐに次の手順を実行してください。> **ソリューション エクスプローラー**の上部にあるソリューション ノードを右クリックして、[ **スタートアップ プロジェクト**] を選択します。 >  [ **操作**] 列で、3 つのプロジェクトすべてが [ **開始**] に設定されていることを確認します。 




前の記事では、カスタム **AddEmployeeToCorpDB** リボン ボタンをプロジェクトから削除しました。以下の手順を使用して再度追加します。




1. **ソリューション エクスプローラー**で、 **ソリューション エクスプローラー**の上部の小さなツールバーにある [ **すべてのファイルを表示**] ボタンを押します。

![[すべてのファイルの表示] ボタンに枠がついたソリューション エクスプローラーのツール バー。](images/f6b035f5-1aa7-452a-8f59-9dd44b062d06.PNG)





2. [ **ChainStore**] プロジェクトで、[ **AddEmployeeToCorpDB**] を右クリックして、[ **プロジェクトに含める**] を選択します。


3. [ **すべてのファイルを表示**] ボタンをもう一度押します。


4. [ **ChainStore**] プロジェクトで、[ **AddEmployeeToCorpDB**] を展開してから、elements.xml ファイルを開きます。 



## ジレンマとその解決方法について理解する

elements.xml ファイルで、 **CustomAction** 要素の **RegistrationId** 属性は、リボンにボタンが追加されたリスト を識別します ( `{$ListId:Lists/Local Employees;}`)。リストは既にホスト Web に手動で追加されていた場合は正しく動作しました。しかし今回は初回実行時のロジックでプログラムによりリストを展開するので、SharePoint がアドインをインストールしボタンを展開しようとしてもリストがまだ存在していません。それで、アドインのインストール時に例外がスローされ失敗します。



初回実行時のロジックではなく、イベント ハンドラーのインストールでリストを展開しても、ジレンマは解決されません。これは、SharePoint が、カスタム ハンドラーを実行する *前に*  、記述的に定義されたカスタム コンポーネント (カスタム ボタン (および [ **注文**] アドイン パート) など) を展開するためです。このため、SharePoint がボタンを展開しようとするときにリストは存在しないことになります。



完全にプログラムでカスタム ボタンを作成するのは非常に高度であるため、ここで説明するのは実際的ではありません。幸いにも、その必要はありません。カスタム ボタンの作成とカスタム リストへの割り当てのかなりの部分をプログラムで行う比較的簡単な方法が存在します。基本的な手順を次に示します。




1. 記述的に定義されたボタンをプロジェクトに保存します。ただし、同じアドインを使用してプログラムで展開されるリストではなく、SharePoint サイトに常に存在する何らかのリボンに割り当てます。 


2. 初回実行時のロジックで、リストをプログラムで作成した後、未定義のボタンをリストのリボンにプログラムで追加します。


3. 元のボタンの値で新しいボタンのプロパティを初期化します。この時点では、同じボタンが 2 つあることになります。2 番目のボタンは、[ **ローカル従業員**] リストのリボンに割り当てられます。


4. プログラムで元のボタンを削除します。



## プログラムでカスタム ボタンを登録する

次の手順は、この戦略を実装する方法を示しています。




1. [ **ChainStore**] プロジェクトで、[ **AddEmployeeToCorpDB**] を展開してから、elements.xml ファイルを開き、 **CustomAction** 要素の **RegistrationId** の属性の値を 「100」 に変更します。これは、リスト型の ID です。Web サイトにこの型のリストのインスタンスがない場合でも、リスト *型*  はすべての SharePoint Web サイトに存在します。属性は、次のようになります。

 ```XML

RegistrationId="100"
 ```

2. ファイル SharePointComponentDeployer.cs で、以下の行を  `DeployChainStoreComponentsToHostWeb` メソッドの、 `CreateLocalEmployeesList` を呼び出す行のすぐ下に追加します。次の手順でこのメソッドを作成します。

 ```cs
  ChangeCustomActionRegistration();
 ```

3. 以下のメソッドを  `SharePointComponentDeployer` クラスに追加します。このコードについては以下の点に注目してください。

  - カスタム アクション (つまり、カスタム ボタン) はリスト *型*  のリボンに登録されているため、スコープは Web サイト全体であり、カスタム アクションの Web サイトのコレクション内に存在します。このため、コードを実行すると、カスタム アクションはそのコレクションから取得されます。


  -  `action.Name` の値は、[ **AddEmployeeToCorpDB**] の element.xml ファイル内の **CustomAction** 要素の **ID** 属性に基づいています。

    > **重要**
      > **次のコード内の  `action.Name` 値を変更して、elements.xml ファイル内の値と一致させる必要があります。** 名前の GUID の部分は異なります。名前の GUID と残りの部分の間には "." 文字があることに注意してください。行の例を次に示します。>  `where action.Name == "4a926a42-3577-4e02-9d06-fef78586b1bc.AddEmployeeToCorpDB"`

 ```cs
  private static void ChangeCustomActionRegistration()
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
         var query = from action in clientContext.Web.UserCustomActions
                     where action.Name == "{button_GUID} .AddEmployeeToCorpDB"
                     select action;
          IEnumerable<UserCustomAction> matchingActions = clientContext.LoadQuery(query);	   
	         clientContext.ExecuteQuery();
	
          UserCustomAction webScopedEmployeeAction = matchingActions.Single();

         // TODO8: Get a reference to the (empty) collection of custom actions 
         // that are registered with the custom list.

         // TODO9: Add a blank custom action to the list's collection.

         // TODO10: Copy property values from the descriptively deployed
         // custom action to the new custom action

        // TODO11: Delete the original custom action.     

          clientContext.ExecuteQuery();
    }
}
 ```

4.  `TODO8` を以下のコードに置き換えます。

    アドインを取り消しても、アドインによって作成されたコンポーネントは削除されないことに注意してください。初回実行時のロジックが実行されると、カスタム アクションがリストの **UserCustomActions** コレクションに作成され、次に F5 キーを押しても取り消されません。混乱を避けるため、このコードの最後の行 `listActions.Clear();` によってコレクションを空にします。



 ```cs

var queryForList = from list in clientContext.Web.Lists
                   where list.Title == "Local Employees"
                   select list;
IEnumerable<List> matchingLists = clientContext.LoadQuery(queryForList);
clientContext.ExecuteQuery();

List employeeList = matchingLists.First();
var listActions = employeeList.UserCustomActions;
clientContext.Load(listActions);
listActions.Clear();
 ```

5.  `TODO9` を次の行に置き換えます。これにより、未定義のカスタム アクションが [ **ローカル従業員**] の一覧に追加されます。

 ```cs

var listScopedEmployeeAction = listActions.Add();
 ```

6.  `TODO10` を以下のコードに置き換えます。このコードについては以下の点に注目してください。

  - このコードは、Web スコープ ボタン (記述されているマークアップで展開されている) のプロパティ値を、リスト スコープのボタンの対応するプロパティに割り当てます。このため、2 つのボタンは、スコープ以外は同じものです。


  - **Sequence** プロパティは、ボタンがリボンの領域に表示される相対的な順序を指定します。ここでは、ボタンは リボンの [ **アイテム**] タブの [ **アクション**] セクションにあります。記述されているマークアップでは、この値は 10001 に設定されています。これはかなり大きな値なので、リボンの [ **アクション**] セクションに SharePoint 自体がもともと配置するボックス内ボタンの後 (つまり右側) にこのボタンが表示されます。



 ```cs
  listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
listScopedEmployeeAction.Update();
 ```

7.  `TODO11` を次の行に置き換えます。これにより、記述して定義されている元のボタンが削除されます。この行がないと、リスト テンプレート「100」を使用する Web サイト上のすべてのリストにカスタム ボタンが存在することになります。このボタンの機能は [ **ローカル従業員**] リストと密接に関連しているため、このボタンが他のリストに存在していても意味がありません。また、この行がないと、ボタンは [ **ローカル従業員**] リストに * 2 つ*  表示されます。これは、そのリストでテンプレート「100」が使用されているためです。

 ```cs

webScopedEmployeeAction.DeleteObject();
 ```


    メソッド全体は次のようになります (プレースホルダーの部分が GUID になっていることを除く)。



 ```cs
  private static void ChangeCustomActionRegistration()
{
    using (var clientContext = SPContext.CreateUserClientContextForSPHost())
    {
         var query = from action in clientContext.Web.UserCustomActions
                     where action.Name == "{button_GUID} .AddEmployeeToCorpDB"
                     select action;
          IEnumerable<UserCustomAction> matchingActions = clientContext.LoadQuery(query);	   
	         clientContext.ExecuteQuery();
	
          UserCustomAction webScopedEmployeeAction = matchingActions.Single();

         var queryForList = from list in clientContext.Web.Lists
                            where list.Title == "Local Employees"
                            select list;
         IEnumerable<List> matchingLists = clientContext.LoadQuery(queryForList);
         clientContext.ExecuteQuery();

        List employeeList = matchingLists.First();
        var listActions = employeeList.UserCustomActions;
        clientContext.Load(listActions);
        listActions.Clear();

        var listScopedEmployeeAction = listActions.Add();

        listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
        listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
        listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
        listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
        listScopedEmployeeAction.Update();

        webScopedEmployeeAction.DeleteObject();     

        clientContext.ExecuteQuery();
    }
}
 ```


## ホスト Web のフル コントロールを要求する

アドインを使用して Web スコープのカスタム アクションを追加および削除しようとしているため、アドインが要求するアクセス許可を [管理] から [フル コントロール] に昇格させる必要があります。次の手順に従います。




1. [ **ソリューション エクスプローラー**] で、[ **ChainStore**] プロジェクトの AppManifest.xml ファイルを開きます。 


2. [ **アクセス許可**] タブを開きます。[ **スコープ**] 値は [ **Web**] のままにしますが、[ **アクセス許可**] フィールドで、ドロップダウンから [ **フル コントロール**] を選択します。


3. ファイルを保存します。



## アドインを実行し、ボタンの展開をテストする






1. 香港ストアの Web サイトの [ **サイト コンテンツ**] ページを開き、 *[ **ローカル従業員**] リストを削除します。* 

    > **メモ**
      > Visual Studio でアドインを取り消しても、アドインで作成されたリストは削除されないため、リストを作成するコードをテストする場合は手動でリストを削除する必要があります。 
2. F5 キーを使用して、アドインを展開して実行します。Visual Studio は、IIS Express でリモート Web アプリケーションをホストし、SQL Express で SQL データベースをホストします。これにより、テスト SharePoint サイト上のアドインの一時的なインストールも実行され、アドインもすぐに実行されます。開始ページが表示される前に、アドインへアクセス許可を付与するように求められます。


3. アドインの開始ページが開いたら、上部のクロム コントロールにある [ **サイトに戻る**] リンクを選択します。 


4. [ **サイト コンテンツ**] ページに移動します。[ **ローカル従業員**] リストは、初回実行時のロジックで追加されるため、既に存在します。

    > **メモ**
      > リストが存在しないか、初回実行コードが実行されていないことを示す他の兆候がある場合、F5 キーを押したときに [ **テナント**] テーブルが空の状態に戻っていない可能性があります。最も一般的な原因は、[ **ChainCorporateDB**] プロジェクトが Visual Studio でスタートアップ プロジェクトとして設定されなくなったことです。この問題を解決する方法については、この記事の上部にある注記を参照してください。また、「 [デバッグ セッションごとに企業データベースを再構築するように Visual Studio を構成する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md#Rebuild)」で説明されているように、データベースを再構築するよう構成済みであることを確認してください。 
5. リストを開き、アイテムを追加します。


6. リスト ビューでアイテムを選択して、リボンの [ **アイテム**] タブを開きます。[ **企業 DB へ追加**] ボタンはリボンに表示されています。


7. ボタンをクリックすると、従業員が企業のデータベースに追加され、[ **企業 DB に追加**] フィールドが [ **はい**] に変更されます。


8. [ **サイト コンテンツ**] ページに戻り、[ **アドインの追加**] を選択します。


9. 新しい [ **カスタム リスト**] を追加します。既定の型は「標準」になります。(「標準」はリスト型 100 です。) リストの作成後に、リボンの [ **アイテム**] タブを開きます。[ **企業 DB に追加**] ボタンはリボン上には *ない*  ことに注意してください。これは、コードによって Web スコープ ボタンが削除されているためです。


10. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は以前のバージョンのアドインを取り消し、最新のアドインをインストールします。


11. このアドインおよび他の記事の Visual Studio ソリューションを操作し、それが終了したら前回のアドインを取り消すとよいでしょう。 **ソリューション エクスプローラー**のプロジェクトを右クリックして、[ **取り消し**] を選択します。



## 
<a name="Nextsteps"> </a>

 リストおよびリスト アイテムのイベントには、SharePoint のカスタム ハンドラーもあります。次の記事では、イベントを作成し、初回実行ロジックでイベントを展開する方法について説明します。 [プロバイダー向けのホスト型アドインでリスト項目一覧を処理する](handle-list-item-events-in-the-provider-hosted-add-in.md)




