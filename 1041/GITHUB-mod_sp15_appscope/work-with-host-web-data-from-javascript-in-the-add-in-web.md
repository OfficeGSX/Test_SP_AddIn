---
title: アドイン Web で JavaScript からのホスト Web データを使って作業する
ms.prod: SHAREPOINT
ms.assetid: 08eb049f-eaf8-478d-9af6-9565e8bb24f2
---


# アドイン Web で JavaScript からのホスト Web データを使って作業する
SharePoint JavaScript オブジェクト モデル (JSOM) を使用して、アドイン Web のページ上の JavaScript からホスト Web にある SharePoint データを処理します。
これは、SharePoint ホスト型の SharePoint アドインの開発の基本に関する記事のシリーズの 11 番目です。 [SharePoint アドイン](sharepoint-add-ins.md)と、次に示すこのシリーズの前の記事をよく理解しておいてください。
  
    
    


-  [SharePoint ホスト型の SharePoint アドインの作成を始める](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [SharePoint 用の SharePoint ホスト型アドインを展開してインストールする](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [SharePoint 用の SharePoint ホスト型アドインにカスタム列を追加する](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [SharePoint ホスト型 SharePoint アドインにカスタム コンテンツ タイプを追加する](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [SharePoint ホスト型 SharePoint アドイン内のページに Web パーツを追加する](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [SharePoint ホスト型 SharePoint アドインにワークフローを追加する](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [SharePoint ホスト型 SharePoint アドインにカスタム ページとスタイルを追加する](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [SharePoint ホスト型 SharePoint アドインにカスタムのクライアント側レンダリングを追加する](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [SharePoint アドインのホスト Web にカスタムのリボンのボタンを作成する](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md)
    
  
-  [SharePoint データで動作するように SharePoint JavaScript API を使用する](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md)
    
  

> **メモ**
> SharePoint ホスト型アドインに関するこのシリーズを学習している場合は、このトピックを続行するために利用できる Visual Studio ソリューションがあります。また、 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) でリポジトリをダウンロードして、BeforeHostWebData.sln ファイルを開くこともできます。
  
    
    

既定で、SharePoint の設計上、JavaScript のアドインはファーム上の他の SharePoint Web サイトにあるデータにアクセスできません。そのため、許可されていないアドインのスクリプトは機密データにアクセスできません。しかし、アドインがホスト Web、またはホスト Web と同じサイト コレクション内のある他の Web サイトにアクセスする必要がある場合も少なくありません。アドインがこのシナリオに対応できるようにするためには、次の 2 つの部分があります。
- アドインのアドイン マニフェスト ファイルでホスト Web に対するアクセス許可を要求する。アドインをインストールするユーザーに対して、このアクセス許可を付与することを求めるダイアログが表示されます。付与しないとユーザーはアドインをインストールできません。
    
  
- ホスト Web に対して JSOM 呼び出しを行うために **SP.ClientContext** オブジェクトを使用するのではなく、 **SP.AppContextSite** オブジェクトを使用する。このオブジェクトを使用すると、アドインはアドイン Web 以外の Web サイト (ただし、同じサイト コレクション内の Web サイトのみ) のコンテキスト オブジェクトを取得できます。(SharePoint Online サブスクリプション [またはオンプレミスの SharePoint Web アプリケーション] 内の任意の Web サイトにアクセスする方法がありますが、これは高度な内容となります)。
    
  
 この記事では JSOM を使用して、まだ開始されていないオリエンテーションを見つけて、それをホスト Web の予定表でスケジュールします。
## ホスト Web 予定表の準備

ホスト Web (開発者向けテスト Web サイト) を開き、「新入社員オリエンテーションのスケジュール」という名前の予定表があること、およびその予定表に「Orient Cassie Hicks」という単一のイベントがあることを確認します。ない場合には、次の手順を実行します。
  
    
    

1. サイトのホーム ページで、 **[サイト コンテンツ]** > **[アドインの追加]** > **[予定表]** の順に選びます。
    
  
2. **[予定表の追加]** ダイアログの **[名前]**に、新入社員オリエンテーションのスケジュールと入力し、 **[作成]** を選びます。
    
  
3. 予定表が開いたら、いずれかの日付にカーソルを合わせて **[追加]** リンクが表示されるまで待機し、その後 **[追加]** をクリックします。
    
  
4. **[新入社員オリエンテーションのスケジュール - 新しいアイテム]** ダイアログの **[タイトル]** に、Orient Cassi Hicks と入力します。他のフィールドは既定のままとし、 **[保存]** をクリックします。
    
    予定表は、次のようになります。
    

   **カスタム予定表**

  

!\[6 月 1 日付の「Orient Cassie Hicks」というアイテムがある「新入社員オリエンテーションのスケジュール」という名前の予定表](images/d2066862-41c1-424d-9bfb-b6c5342bcf2c.PNG)
  

  

  

## JavaScript とその呼び出し用ボタンの作成


1. **ソリューション エクスプローラー**の **[スクリプト]** ノードで Add-in.js ファイルを開きます。
    
  
2.  `completedItems` の宣言の下に、以下の宣言を追加します。
    
  -  `notStartedItems`。 **[シアトルの新入社員]** リストにある、 **[オリエンテーション ステージ]** が **[未開始]** のアイテムを参照します。
    
  
  -  `calendarList`。ホスト Web で作成した予定表を参照します。
    
  
  -  `scheduledItems`。予定表にあるアイテムのコレクションを参照します。
    
  

 ```
  
var notStartedItems;
var calendarList;
var scheduledItems;
 ```

3. SharePoint アドインが実行されると、SharePoint はスタート ページを呼び出して、そのスタート ページ URL にいくつかのクエリ パラメーターを追加します。その 1 つが  `SPHostUrl` で、当然のことながら、これはホスト Web の URL です。アドインではホスト Web データに対する呼び出しを行うためにこの情報が必要となるため、Add-in.js ファイルの上部付近の `scheduledItems` の変数宣言の下に、次の行を追加します。このコードの以下の点にご注目ください。
    
  -  `getQueryStringParameter` は、次の手順で作成するユーティリティ関数です。
    
  
  -  `decodeUriComponent` は標準の JavaScript 関数で、SharePoint がクエリ パラメーターに対して実行する URI エンコード処理を元に戻します。たとえば、エンコードされたスラッシュ "%2F" は "/" に戻されます。
    
  

 ```
  
var hostWebURL = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
 ```

4. このファイルの下部に次のコードを追加します。この関数は、クエリ パラメーターの読み取りに使用できます。 
    
 ```
  // Utility functions

function getQueryStringParameter(paramToRetrieve) {
     var params = document.URL.split("?")[1].split("&amp;");
     var strParams = "";
     for (var i = 0; i < params.length; i = i + 1) {
         var singleParam = params[i].split("=");
         if (singleParam[0] == paramToRetrieve) {
             return singleParam[1];
        }
     }
 }
 ```

5. Add-in.js ファイルの失敗コールバック セクションより上のどこかに次の関数を追加します。このコードの以下の点にご注目ください。
    
  - これは、 **[完了]** アイテムを取得するリストのクエリ メソッドとほぼ同一です。異なるのは、 **[完了]** アイテムではなく **[未開始]** のアイテムを取得する点です。このスクリプトでは簡略化のために、オリエンテーションが既に **[未開始]** ステージを過ぎている場合にはスケジュールに入っているはずと想定するため、注目するのは **[未開始]** アイテムのみです。
    
  
  -  後述の手順では、 **executeQueryAsync** 呼び出しで 2 つのコールバック メソッドを作成します。
    
  

 ```
  
function ensureOrientationScheduling() {

    var camlQuery = new SP.CamlQuery();
    camlQuery.set_viewXml(
        '<View><Query><Where><Eq>' +
            '<FieldRef Name=\\'OrientationStage\\'/><Value Type=\\'Choice\\'>Not started</Value>' +
        '</Eq></Where></Query></View>');
    notStartedItems = employeeList.getItems(camlQuery);
  
    clientContext.load(notStartedItems);
    clientContext.executeQueryAsync(getScheduledOrientations, onGetNotStartedItemsFail);
    return false;
}
 ```

6. Add-in.js ファイルの前述の関数のすぐ下に次の関数を追加します。この関数は  `hostWebContext` オブジェクトを使用してクエリ対象リストを識別します。
    
    > **メモ**
      > CAML クエリに追加されるクエリ マークアップはないことにご注目ください。クエリ オブジェクト内に実際のクエリがないと、 *すべて*  のリスト アイテムが取得されることになります。リストが非常に大きい場合は、許容できないほどの長時間に渡ってサーバーに対する要求が実行される可能性があります。その場合は、必要な処理を実行する別の方法を探すことができます。しかし、非常に小さなリスト (予定表リストもほとんどの場合、小さなリストです) を使用するこのサンプル ソリューションでは、リスト全体を取得してクライアント上で反復処理すれば、実際には、サーバーへの呼び出し、つまり **executeQueryAsync** の呼び出し回数を最小限に抑えることができます。

 ```
  
function getScheduledOrientations() {

    var hostWebContext = new SP.AppContextSite(clientContext, hostWebURL);
    calendarList = hostWebContext.get_web().get_lists().getByTitle('Employee Orientation Schedule');

    var camlQuery = new SP.CamlQuery();
    scheduledItems = calendarList.getItems(camlQuery);

    clientContext.load(scheduledItems);
    clientContext.executeQueryAsync(scheduleAsNeeded, onGetScheduledItemsFail);
}
 ```

7. 以下の関数をファイルに追加します。このコードの以下の点にご注目ください。
    
  - このメソッドは、 **[シアトルの新入社員]** リストの **[未開始]** アイテムのタイトル (その社員の名前) が、 **[新入社員オリエンテーションのスケジュール]** 予定表のイベント タイトルに含まれているかどうかをチェックします。簡略化のため、予定表内のすべてのエントリはイベント タイトルに社員の氏名を指定して作成されるものと想定しています。
    
  
  - 予定表に既に記載されているどのイベントも **[未開始]** アイテムに合致しない場合、スクリプトは **[未開始]** アイテムの予定表アイテムを作成します。
    
  
  - JSOM は、SharePoint サーバーに送信するペイロードのサイズを最小化するために、 **SPListItem** オブジェクトではなく軽量の **ListItemCreationInformation** オブジェクトを使用します。
    
  
  - 新しい予定表イベントの 2 つの DateTime フィールドは、この記事が作成された月、つまり  `2015-06` に日付設定されています。 *現在の年と月の日付に変更すれば、アイテムを探して予定表を後方にスクロールせずに済みます。* 
    
  
  - スケジュールされていない **[未開始]** アイテムが検出されると、最初のアイテムが月の 10 日にスケジュールされます。スケジュールされていないアイテムがさらにある場合はそれぞれ 10 日以降にスケジュールされます。簡略化のため、その数はそれほど多くなく、"32" などのあり得ない日付になることはないと想定しています。
    
  
  - このコードの大部分は標準の JavaScript です。SharePoint JSON を使用する行には、コメントが付いています。
    
  

 ```
  
function scheduleAsNeeded() {

    var unscheduledItems = false;
    var dayOfMonth = '10';

    var listItemEnumerator = notStartedItems.getEnumerator();

    while (listItemEnumerator.moveNext()) {
        var alreadyScheduled = false;
        var notStartedItem = listItemEnumerator.get_current();

        var calendarEventEnumerator = scheduledItems.getEnumerator();
        while (calendarEventEnumerator.moveNext()) {
            var scheduledEvent = calendarEventEnumerator.get_current();

             // The SP.ListItem.get_item('field_name ') method gets the value of the specified field.
            if (scheduledEvent.get_item('Title').indexOf(notStartedItem.get_item('Title')) > -1) {
                alreadyScheduled = true;
                break;
            }
        }
        if (alreadyScheduled === false) {

             // SP.ListItemCreationInformation holds the information the SharePoint server needs to
             // create a list item
            var calendarItem = new SP.ListItemCreationInformation();

             // The some_list .additem method tells the server which list to add 
             // the item to.
            var itemToCreate = calendarList.addItem(calendarItem);

             // The some_item .set_item method sets the value of the specified field.
            itemToCreate.set_item('Title', 'Orient ' + notStartedItem.get_item('Title'));

             // The EventDate and EndDate are the start and stop times of an event.
            itemToCreate.set_item('EventDate', '2015-06-' + dayOfMonth + 'T21:00:00Z');
            itemToCreate.set_item('EndDate', '2015-06-' + dayOfMonth + 'T23:00:00Z');
            dayOfMonth++;

             // The update method tells the server to commit the changes to the SharePoint database.
            itemToCreate.update();
            unscheduledItems = true;
        }
    }
    if (unscheduledItems) {
        calendarList.update();
        clientContext.executeQueryAsync(onScheduleItemsSuccess, onScheduleItemsFail);
    }
}
 ```

8. 次の成功ハンドラーを追加します。これは、以前にスケジュールされていなかったアイテムが予定表に追加されると呼び出されます。
    
 ```
  
function onScheduleItemsSuccess() {
    alert('There was one or more unscheduled orientations and they have been added to the '
              + 'Employee Orientation Schedule calendar.');
}
 ```

9. ファイルの失敗コールバック セクションに次の失敗関数を追加します。
    
 ```
  
function onGetNotStartedItemsFail(sender, args) {
    alert('Unable to get the not-started items. Error:' 
        + args.get_message() + '\\n' + args.get_stackTrace());
}

function onGetScheduledItemsFail(sender, args) {
    alert('Unable to get scheduled items from host web. Error:' 
        + args.get_message() + '\\n' + args.get_stackTrace());
}

function onScheduleItemsFail(sender, args) {
    alert('Unable to schedule items on host web calendar. Error:' 
        + args.get_message() + '\\n' + args.get_stackTrace());
}
 ```

10. default.aspx ファイルを開き、ID **PlaceHolderMain** の **asp:Content** 要素を見つけます。
    
  
11. 次のマークアップを  `purgeCompletedItems` ボタンのすぐ下に追加します。
    
 ```HTML
  
<p><asp:Button runat="server" OnClientClick="return ensureOrientationScheduling()"
  ID="ensureorientationschedulingbutton" Text="Ensure all items are on the Calendar" /></p>
 ```

12. Visual Studio でこのプロジェクトをリビルドします。
    
  
13. アドインのテスト中に、リスト アイテムの **[オリエンテーション ステージ]** を[未開始] に手動で設定する必要を最小限に抑えるため、リスト インスタンス **NewEmployeesInSeattle** の elements.xml ファイル (リスト テンプレート **NewEmployeeOrientation** の elements.xml ではない) を開き、最低 3 つの **Row** 要素 (その中に *Cassie Hicks の行を含める*  ) の [オリエンテーション ステージ] 値が[未開始] 値になるようにします。これは既定値なので、3 つ (あるいはそれ以上) の行に `OrientationStage` の **Field** 要素を含めないことが最も簡単な方法です。
    
    **Rows** 要素の様子の一例を次に示します。
    


 ```
  
<Rows>
  <Row>
    <Field Name="Title">Tom Higginbotham</Field>
    <Field Name="Division">Manufacturing</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Satomi Hayakawa</Field>
  </Row>
  <Row>
    <Field Name="Title">Cassi Hicks</Field>
  </Row>
  <Row>
    <Field Name="Title">Lertchai Treetawatchaiwong</Field>
  </Row>
</Rows>
 ```


## アドインが必要とするホスト Web へのアクセス許可の指定

アドインにはそれ自体のアドイン Web に対するフル コントロールのアクセス許可が自動的に設定されるため、ここまでの部分では必要なアクセス許可を指定する必要がありませんでした。しかし、ホスト Web のデータと対話するには特にアクセス許可を要求しなければなりません。新入社員オリエンテーション アドインには、ホスト Web の予定表にアイテムを追加するためのアクセス許可が必要です。 
  
    
    

1. **ソリューション エクスプローラー**で appmanifest.xml ファイルを開きます。 
    
  
2. マニフェスト デザイナーで **[アクセス許可]** タブを開きます。
    
  
3. **[範囲]** 列の一番上の行にあるドロップダウンで、 **[リスト]** を選びます。
    
  
4. **[アクセス許可]** 列で **[管理]** を選びます。
    
  
5. **[プロパティ]** 列が空白のままの場合、このアドインは、ホスト Web におけるすべてのリストに対する書き込みアクセス許可を要求します。アドインには、必要なアクセス許可のみを付与することをお勧めします。アドイン マニフェストではアクセス許可を特定のリスト インスタンスに制限する方法はありませんが、特定の基本リスト テンプレートを基に作成されたリスト インスタンスのみにアドインを制限することはできます。予定表の基となっているリスト テンプレートは、数値 ID 106 の **Events** です。
    
    同じ行の **[プロパティ]** セルをクリックして、セルに **[編集]** ボタンを表示します。アクセス許可リストは次のようになります。
    

   **[編集] ボタンが表示されている [アクセス許可] リスト**

  

!\[Visual Studio アドイン マニフェスト デザイナーの [アクセス許可] タブに記されているアクセス許可の一覧。[プロパティ] 列のセルに [編集] ボタンが示されています。](images/03780b79-aca8-44d1-b0bf-d80833d08627.PNG)
  

  

  
6. **[編集]** を選んで、 **[プロパティ]** ダイアログを開きます。
    
  
7. **[名前]** をBaseTemplateID に、 **[値]** を106 にそれぞれ設定します。この段階でダイアログは次のようになります。
    
   **アクセス許可リストの [プロパティ] ダイアログ**

  

!\[Visual Studio におけるアクセス許可一覧のための [プロパティ] ダイアログ。プロパティ名は「Base List ID」に、値は「106」に設定されています。](images/13773fdd-5606-4f35-b8d5-14aad54cffb7.PNG)
  

    **[OK]** を選びます。 **[アクセス許可]** タブは、次のようになります。
    

   **Visual Studio のアドイン マニフェスト デザイナーの [アクセス許可] タブ**

  

!\[Visual Studio のアドイン マニフェスト デザイナーの　[アクセス許可] タブ。[管理] アクセス許可があり、ベース タイプが 106 のリストを表示するアドインが示されています。](images/14d5a820-ab44-4d12-98de-6672884bf344.PNG)
  

  

  

## アドインの実行とテスト


  
    
    

1. この記事の前述のとおりにホスト Web 予定表が準備されていることを確認します。「Orient Cassi Hicks」という単一のイベントがあるはずです。
    
  
2. デバッグ時に Visual Studio が使用するブラウザーでポップアップを有効にします。
    
  
3. F5 キーを使用して、アドインを展開して実行します。Visual Studio が、テスト用 SharePoint サイトにアドインを一時的にインストールして、すぐにアドインを実行します。 
    
  
4. アクセス許可の同意確認フォームが開いたら、必要なアクセス許可をアドインに付与できます。ページ上にはドロップダウン リストが表示され、ホスト Web 上のすべての予定表から選べます。 **[新入社員オリエンテーションのスケジュール]** を選び、 **[信頼する]** を選びます。
    
   **SharePoint アドインの同意確認プロンプト**

  

!\[アドインが必要とするアクセス許可の概要が示された SharePoint アドイン同意確認プロンプトと、[信頼する] または [キャンセル] のボタン。](images/99209248-8927-4fc2-abfc-53d530376516.PNG)
  

  

  
5. アドインがスタート ページが完全に読み込まれてから、 **[アイテムがスケジュール設定されていることを確認する]** ボタンを選びます。
    
   **新しいボタンが表示されている [新入社員オリエンテーション] ホームページ**

  

!\[[アイテムがスケジュール設定されていることを確認する] というラベルの新しいボタンが追加された [新入社員オリエンテーション] ホームページ。](images/72b78f79-78c0-41db-90d5-8f67e1c17b0e.PNG)
  

  

  
6. いずれかの失敗コールバック関数が実行されると、コールバック関数によって作成されたエラー メッセージ通知が表示されます。それ以外の場合には最終的な成功コールバックによって作成された成功メッセージ **「スケジュールされていない 1 つ以上のオリエンテーションがあり、それらは [新入社員オリエンテーションのスケジュール] 予定表に追加されました。」**が表示されます。
    
  
7. ホスト Web 上の **[新入社員オリエンテーションのスケジュール]** 予定表に移動します。たとえば、開発者向けサイトのホーム ページに至る階層リンクを選び、 **[サイト コンテンツ]** を選びます。その後、 **[新入社員オリエンテーションのスケジュール]** タイル ( **[新入社員オリエンテーション]** タイルではない) を選びます。
    
    予定表は次のようになります。このスクリプトは Cassi Hicks のイベントが既に存在することを検出したため、Cassi Hicks の 2 番目のイベントは作成しませんでしたが、オリエンテーションが **[未開始]** 状態の他の 2 人の社員にイベントを作成しました。また、オリエンテーションが **[未開始]** 状態を過ぎた社員に対してはイベントは作成しませんでした。
    

   **2 つの新しいイベントが追加された後の予定表**

  

!\[対象月の 10 日と 11 日に 2 人の社員のオリエンテーションを行うための新しいイベントが追加された [新入社員オリエンテーション] 予定表](images/f8037509-4bf1-4c69-a673-ee6fe0f0dcb7.PNG)
  

  

  
8.  * **[アイテムがスケジュール設定されていることを確認する]** をもう一度押す前に、予定表から 2 つの新しいイベントを確実に削除してください。* 
    
  
9. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は以前のバージョンのアドインを取り消し、最新のアドインをインストールします。
    
  
10. このアドインおよび他の記事の Visual Studio ソリューションを操作し、それが終了したら前回のアドインを取り消すとよいでしょう。 **[ソリューション エクスプローラー]** のプロジェクトを右クリックして、 **[取り消し]** を選びます。
    
  

## 
<a name="Nextsteps"> </a>

SharePoint ホスト型 SharePoint アドイン の高度な作業に進みます。 
  
    
    

-  [SharePoint アドインの設計](design-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの発行](publish-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインを開発するためのツールと環境](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  

