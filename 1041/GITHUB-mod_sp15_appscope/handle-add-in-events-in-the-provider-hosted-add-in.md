---
title: プロバイダー向けのホスト型アドインでアドイン イベントを処理する
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# プロバイダー向けのホスト型アドインでアドイン イベントを処理する
プロバイダー ホスト型の SharePoint アドインのインストールをカスタマイズする方法について説明します。
これは、プロバイダー ホスト型 SharePoint アドインの開発の基本に関する記事のシリーズの 7 番目です。  [SharePoint アドイン](sharepoint-add-ins.md)およびこのシリーズの前の記事についてまず理解しておく必要があります。
  
    
    


-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の外観を付与する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [プロバイダー向けのホスト型アドインにカスタム ボタンを含める](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [SharePoint オブジェクト モデルの概要を簡単に説明する](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の書き込み操作を追加する](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインにアドインの一部を含める](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  

> **メモ**
> プロバイダー ホスト型アドインに関するこのシリーズを続けて学習している場合は、このトピックの学習に使用できる Visual Studio ソリューションがあります。また、 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) でリポジトリをダウンロードして、BeforeAdd-inEventHandlers ファイルを開くこともできます。
  
    
    

この記事では、アドイン イベントと呼ばれる、一種の SharePoint 内のイベントの処理をカスタマイズします。具体的には、アドインのインストールとアンインストールのイベント用のハンドラーを作成します。カスタム処理を取得できるリストとリスト アイテム イベントもあります。このシリーズの以降の記事ではこれらについて学習します。これらのイベントはすべて、SharePoint でトリガーされますが、各イベントを処理するカスタム コードは、リモート Web アプリケーション内にあります。SharePoint イベントにハンドラーの URL を登録することによって、カスタム ハンドラーを呼び出すように SharePoint を構成します。
## SharePoint コンポーネントをプログラマチックに展開する 2 つの場所

チェーン ストア アドインで [ **ローカル従業員**] と [ **予想される出荷**] のリストを自動的に作成および展開します。アドインは、カスタム リストなどの SharePoint コンポーネントをいつでも展開できます。ただし、アドインがカスタム リストなどの特定のコンポーネントに依存する場合、そのコンポーネントは、アドインでの処理を開始 *する前に*  実際に展開されている必要があります。こうした重要なコンポーネント用のカスタムの展開ロジックを配置できる次の 2 つの場所があります。
  
    
    

- アドインのインストール イベントのハンドラー内。
    
  
- アドインが初めて SharePoint で起動されたときに実行される「初回実行」ロジック内。
    
  
特定のアドインにとってどちらが最適かを決定するというのは複雑な話になります。この記事では、いくつかの比較点のみを扱います。
  
    
    

- カスタム インストール ハンドラーは、30 秒以内に完了する必要があります。初回実行ロジックには時間の制限はありません。
    
  
- アドインのインストール時に問題が発生した場合、SharePoint はインストールの一部として行った処理すべてをロールバックします。カスタム インストール ハンドラーは、SharePoint がアドインをインストールするためのすべての作業を行った *後*  に実行されるため、カスタム ハンドラーはこのシステムに参加できます。たとえば、カスタム ロジックが例外をスローしたときに、アドインのインストール全体をロールバックするように SharePoint に通知できます。ただし、初回実行ロジックで問題が生じた場合は、アドインはインストールされたままになりますが、適切に動作しない可能性があります。
    
  
- アドインのインストールをロールバックする必要がある場合、SharePoint はインストールを停止せず、すぐにインストールを再試行します。インストールは最大 4 回試行されます (各試行では 30 秒の制限が適用されます)。再試行のたびに、カスタム インストール ハンドラーは *初めから*  再実行されます。ハンドラーは、ロールバック前にインストールできたリストがあっても、再試行時にその同じリストをもう一度インストールしようとします。これが発生しないようにするには、その操作が既に実行されていないかどうかを最初にチェックしない限りコンポーネントの展開をはじめとするいずれの操作も実行しないように、インストール ハンドラー内にコードを書き込む必要があります。こうすると、インストール ハンドラーのロジックは初回実行ロジックよりも複雑になります。初回実行ロジックは (コーディングで指定した場合を除き) 再試行されないからです。さらに、コンポーネントが既に展開されているかどうかをチェックするのには、インターネット経由でのリモート ハンドラーから SharePoint への呼び出しのための時間が必要になります。次いで、(まだ展開されていないことがわかった場合には) コンポーネントを実際に展開するために 2 番目の呼び出しが必要になります。
    
  
チェーン ストア アドインでは、これらの戦略が組み合わされています。この記事では、企業データベース内のテナントとしてホスト Web を登録し、アドインがホスト Web で実行済みかどうかを示す信号を設定するインストール ハンドラーを作成します。このシリーズのこれより後の記事で、初回実行ロジックをアドイン スタート ページの **Page_Load** メソッドに配置します。このロジックは、2 つのカスタム リストを展開し、他のいくつかの操作も実行します。
  
    
    

## イベント レシーバーのデバッグのためのソリューションを構成する
<a name="RERDebug"> </a>

イベント レシーバーのデバッグには、Azure サービス バスを使用する必要があります。「 [SharePoint アドインでのリモート イベント レシーバーのデバッグとトラブルシューティング](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)」の指示に従ってください。SharePoint Online Web サイトをテスト サイトとして使用している場合は、必ずリモート テスト サイト用の手順を実行してください。このシリーズの残りの部分は、デバッグが正常に構成されていることを前提としています。 
  
    
    

## インストール ハンドラーを作成する
<a name="RERDebug"> </a>


  
    
    

> **メモ**
>  Visual Studio 内のスタートアップ プロジェクトの設定では、ソリューションが開かれるたびに、既定値に戻ってしまう傾向があります。この一連の記事のサンプル ソリューションを再度開く場合は、その後ですぐに次の手順を実行してください。>  [ **ソリューション エクスプローラー**] の上部にあるソリューション ノードを右クリックして、[ **スタートアップ プロジェクトを設定**] を選択します。 >  [ **操作**] 列で、3 つのプロジェクトすべてが [ **開始**] に設定されていることを確認します。 
  
    
    


1. [ **ソリューション エクスプローラー**] で、[ **ChainStore**] プロジェクトを選択すると、プロパティが Visual Studio の [ **プロパティ**] ウィンドウに表示されます。
    
  
2. [ **アドインのハンドルがインストールされました**] (これは、まだ [ **アプリのハンドルがインストールされました**] になっている可能性もあります) の値を [ **True**] に設定します。これは次の 2 つの作業を行います。
    
  - [ **サービス**] という名前のフォルダーが [ **ChainStoreWeb**] プロジェクト ([ **ChainStore**] プロジェクトではない) に作成され、それに 2 つのファイル、すなわち、AppEventReceiver.svc ファイルとそのコードビハインド ファイルである AppEventReceiver.svc.cs ファイルが追加されます。(これらのファイル名は "App" という文字列で始まりますが、これはアドインが以前「アプリ」と呼ばれていたためです。 *これらのファイル名は変更しないでください。*  Office Developer Tools for Visual Studio は、ファイルの名前がこのままになっていると想定しています。)
    
  
  - ハンドラーの URL はアドイン マニフェストに登録されます。マニフェストのこの部分は、マニフェスト デザイナーでは表示されません。これを表示するには、AppManifest.xml ファイルを右クリックし、[ **コードの表示**] を選択します。次のような [ **プロパティ**] 要素の新しい子があります。このマークアップは、アドインのインストールに関連した独自の作業をすべて完了したときに **ProcessEvent** メソッドを呼び出すように SharePoint に通知します。カスタム ハンドラーは、インストールの最後の部分として実行されます。文字列 `~remoteAppUrl` はプレースホルダーであり、Office Developer Tools for Visual Studio によってサービス ホストの URL に置き換えられます。デバッグをしている場合は、これは Azure サービス バスの URL になります。運用環境に展開するためのパッケージを作成している場合は、これは運用環境の URL になります。
    
  ```XML
  
<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
  ```

3. AppEventReceiver.svc.cs ファイルを開きます。
    
  
4. Office Developer Tools for Visual Studio により、 **ProcessEvent** メソッドのサンプル実装が作成されました。このメソッドのすべての実装は、 **SPRemoteEventResult** オブジェクトを初期化することによって開始され、そのオブジェクトを SharePoint に返すことによって終了します。このオブジェクトは、主に、カスタム処理ロジックが失敗したのでイベントをロールバックするかどうかを SharePoint に通知します。また、 **ClientContext** オブジェクトを作成する **using** ブロックも、これらのツールによってこのメソッドに含められている可能性があります。チェーン ストア アドイン内のカスタム ハンドラーは SharePoint にコールバックしないため、このブロックは削除してください。このメソッドは、次のようになります。
    
  ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
  ```

5. イベント レシーバーは、次の 3 つのアドイン イベントによって呼び出される可能性があるため、次の **switch** 構造を、 **ProcessEvent** メソッド内の `result` オブジェクトを作成する行と取得する行の間に追加します。イベント名には、"App" という文字列が含まれが、これは、アドインが以前「アプリ」と呼ばれていたためです。
    
  ```cs
  
switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.         
         
        break;
}
  ```

6. インストール ロジックは、SQL ストアド プロシージャを呼び出して、香港のストアをテナントとしてリモート Web アプリケーションに登録します。重要なこととして、この処理が失敗した場合、ハンドラーがアドインのインストールをロールバックするよう SharePoint に通知するようにする必要があるので、次の **try/catch** ブロックを `TODO2` の代わりに追加します。このコードについては、以下の点に注意してください。
    
  -  `tenantName` オブジェクトと `CreateTenant` メソッドは、後の手順で作成します。
    
  
  - **SPRemoteEventResult** オブジェクトの **Status** プロパティは、次の 3 つの値を指定できます。 **Continue** (既定)、 **CancelNoError**、および **CancelWithError**。後者の 2 つのいずれかが、イベントをロールバックするように SharePoint に通知します。
    
  

  ```cs
  
try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

7. サンプルのテナントの識別子であるホスト Web の URL は、SharePoint が **SPRemoteEventProperties** パラメーターでレシーバーに渡す情報の一部です。 **ProcessEvent** メソッドの、 **SPRemoteEventResult** オブジェクトの初期化のすぐ下の行に次の行を追加します。
    
  ```cs
  
string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
  ```

8. ここで、コードは **AppEventProperties.HostWebFullUrl** プロパティの多少変わった特徴を処理する必要があります。その他のほとんどのコンテキストでは、SharePoint にホスト Web URL の末尾に "/" の終了文字が含まれるため、サンプル コードのロジックはこの文字が記載されていることを想定しています。しかし、SharePoint では、ホスト Web がサイト コレクションのルート Web である場合にのみ、この文字が **HostWebFullUrl** 値の末尾に追加されます。当社の香港の Web サイトはサイト コレクション内のサブ Web であるため、この文字を追加して、同じ名前の文字列がサンプル全体で使用されるようにする必要があります。 `tenantName` オブジェクトの初期化の下に次のコードを追加します。
    
  ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
  ```

9. 次の **using** ステートメントをファイルの先頭に追加します。
    
  ```
  
using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
  ```

10. 以下のメソッドを  `AppEventReceiver` クラスに追加します。このシリーズの記事の目的は、SQL Server/Azure プログラミングではなく、SharePoint アドイン プログラミングについて学習することであるため、これについては詳しく説明しません。
    
    このメソッドは、 **テナント** という名前のデータベース テーブルに行を作成します。 **名前** 列に加えて、テーブルには既定値の列が0000.0000.0000.0000 に設定された **バージョン** 列も含まれています。このシリーズの以降の記事では、この値を参照する初回実行ロジックを作成し、アドインが既にホスト Web にインストールされているするかどうかを判別します。バージョンが0000.0000.0000.0000 である場合、コードは [ **ローカル従業員**] と [ **予想される出荷**] リストを展開してから、バージョン番号を上げます。
    


  ```cs
  
private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
  ```


## アンインストール ハンドラーを作成する
<a name="RERDebug"> </a>

通常は、インストール済みイベントを処理するときに、常にアンインストール イベントを処理することをお勧めします。基本的な考え方は、アンインストール ハンドラーをインストール ハンドラーが展開した内容を削除またはリサイクルするということです。ただし、多くの例外があるため、実際にはアドインのユース ケースを理解する必要があります。たとえば、アドインを使用して展開されているリスト、およびアドインを使用して入力されているリストには、アドイン自体をアンインストールした後でも値が含まれる場合があります。この場合、アンインストール イベント ハンドラーのリストはアンインストールしません。 
  
    
    
ご想像どおり、ユーザーが [ **サイト コンテンツ**] ページからアドインを削除しても、アンインストール イベントは実行されません。これを行うには、Web サイトのごみ箱にアドインを移動する必要があります。ユーザーはアドインを復元できますが、復元しても、インストール イベント ハンドラーは再実行されません。このため、インストール イベント ハンドラーを使用して展開されたものは、アドインを復元してもそのまま存在します。SharePoint コンポーネントは、ごみ箱から第 2 ステージのごみ箱に移動できます。アンインストール イベントが発生するのは、アドインが第 2 ステージから削除されたときのみです。ユーザーがそれを実行すると、アドインは復元不能になります。このため、香港ストアのテナントをその時点で企業のデータベースから削除するようにします。
  
    
    

1. [ **アドインのハンドルをアンインストール中**] の値を [ **True**] に設定します (これは、引き続き [ **アプリのハンドルをアンインストール中**] と呼ばれている可能性があります)。これにより、先ほどインストール ハンドラーを登録したのと同じように、AppManifest.xml ファイルにハンドラーが登録されます。ファイルを参照すると、全く同じ URL が表示されます。Office Developer Tools for Visual Studio は、同じ *.svc ファイルが使用されることを前提としています。このサンプルで以上の処理を行います。これは一般的なプラクティスです。 
    
  
2. AppEventReceiver.svc.cs ファイル内の  `TODO3` の代わりに次のコードを追加します。このコードについては、以下の点に注意してください。
    
  -  `DeleteTenant` メソッドは、次の手順で追加されます。
    
  
  - アドインのアンインストールをロールバックすると、アドインは第 2 ステージのごみ箱に入ったままになり、元に復元することができます。
    
  

  ```cs
  
try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

3. 以下のメソッドを  `AppEventReceiver` クラスに追加します。
    
  ```cs
  
private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();                
    }//dispose conn and cmd
}
  ```


> **メモ**
> このシリーズの前の記事では、F5 キーを押すたびに、企業のデータベースを再構築するようにプロジェクトを構成していました。これにより、[ **テナント**] テーブルは空になります。 
  
    
    


## アドインを実行し、インストール ハンドラーをテストする
<a name="RERDebug"> </a>


1. F5 キーを使用して、アドインを展開し実行します。Visual Studio は IIS Express でリモート Web アプリケーションをホストし、SQL Express で SQL データベースをホストします。さらに、テスト用の SharePoint サイト上にアドインを一時的にインストールし、インストール イベント ハンドラーを実行します。すると、アドインはすぐに実行されます。スタート ページが表示される前に、アドインにアクセス許可を付与するように求められます。 
    
  
2. アドインの開始ページが開くと、上部にあるクロム コントロールの歯車アイコンをキーを押して、[ **アカウント設定**] を選択します。
    
  
3. [ **アカウント**] ページで、[ **アドインのバージョンを表示する**] ボタンをクリックします。バージョンが 0000.0000.0000.0000 と表示されます。
    
     ![見出しが [アカウントの設定] のアカウント ページです。[アドインのバージョンを表示する] という名前のボタンと、その下に「登録されたバージョン: ゼロ ゼロ ゼロ ゼロ ドット ゼロ ゼロ ゼロ ゼロ ドット ゼロ ゼロ ゼロ ゼロ ドット ゼロ ゼロ ゼロ ゼロ」と書かれたテキストの行があります。](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)
  

  

  
4. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は以前のバージョンのアドインを取り消し、最新のアドインをインストールします。
    
  
5. このアドインおよび他の記事の Visual Studio ソリューションを操作し、それが終了したら前回のアドインを取り消すとよいでしょう。[ **ソリューション エクスプローラー**] のプロジェクトを右クリックして、[ **取り消し**] を選択します。
    
  

## 
<a name="Nextsteps"> </a>

 シリーズの次の記事では、初回実行ロジックを [ **ローカル従業員**] リストとカスタム リボンのボタンをプログラムで展開するアドインに追加します。 [プロバイダー向けのホスト型アドインに初回実行時のロジックを追加する](add-first-run-logic-to-the-provider-hosted-add-in.md)
  
    
    

