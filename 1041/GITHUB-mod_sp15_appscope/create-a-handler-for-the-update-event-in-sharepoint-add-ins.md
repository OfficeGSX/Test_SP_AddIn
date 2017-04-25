---
title: SharePoint アドインで更新イベント用のハンドラーを作成する
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# SharePoint アドインで更新イベント用のハンドラーを作成する
SharePoint アドインの更新イベント用のハンドラーを作成して使用します。
## アドイン更新イベント用ハンドラー作成の前提条件
<a name="Prerequisites"> </a>

「 [アドイン イベントを処理する](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)」と「 [SharePoint アドインを更新する](update-sharepoint-add-ins.md)」、およびその中で示されている前提条件と主要な概念を理解している必要があります。




## UpgradedEventEndpoint レシーバーを作成する
<a name="UpgradedEventEndpoint"> </a>


> **メモ**
> **バージョン番号付けシステム:** 一貫性を保つため、このトピックではアドインのバージョン番号は 1.0.0.0、2.0.0.0、3.0.0.0 などであるものとします。ただし、ロジックとガイダンスは、番号付けシステムに関係なく適用されます。




カスタム更新ロジックの場合、アドイン更新イベントを処理する SharePoint リモート イベント レシーバーを作成できます。この技法は注意深く使用してください。他の方法では行うことができない更新手順に対してのみ使用してください。また、「 [アドイン イベントを処理する](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)」に記されているガイダンスは、アドイン インストール イベントおよびアドイン アンインストール イベントと同じようにアドイン更新イベントにも適用されます。ガイダンスには、以下の点も含まれます。




- ハンドラーは、30 秒以内に完了してキャンセルまたは続行のステータスを SharePoint に返す必要があります。返さない場合、SharePoint はハンドラーを再び呼び出します。再呼び出しは 3 回まで行われます。


- ハンドラーがキャンセル ステータスを返すと、SharePoint により 3 回まで再試行が行われます。 


- 通常は、ハンドラーにロールバックと「既に実行」ロジックを含める必要があります。


 **UpgradedEventEndpoint** ハンドラーが役に立つシナリオの 1 つに、計算フィールドをリモート データベースに追加する場合があります。City 列を追加し、その値は既に存在する Postal Code 列から計算されるものと仮定します。 **UpgradedEventEndpoint** ハンドラーのコードでは、データベースの既存の項目を反復処理し、Postal Code フィールドの値と、郵便番号を都市にマップする外部データ ソースに基づいて、新しい City フィールドの値を設定できます。データベース スキーマ自体に対する変更は、データベース プラットフォームのベスト プラクティスを使用して、 **UpgradedEventEndpoint** ハンドラーの外側で行うのが最善です。



アドイン イベント用のハンドラーを作成する方法の詳細については、「 [SharePoint アドインのイベントの処理](handle-events-in-sharepoint-add-ins.md)」および「 [SharePoint アドインでアドイン イベント レシーバーを作成する](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)」を参照してください。以下の手順では、アドイン イベント レシーバー アイテムを Visual Studio の SharePoint アドイン プロジェクトに追加するものとします。 




### 初めてアドイン更新イベントを処理するには


1. AppEventReceiver.svc.cs ファイルを開き、ハンドラーを呼び出したイベントが更新イベントかどうかをテストする条件構造を  [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) メソッドに追加します。更新コードをこの構造の内部に追加します。コードで SharePoint にアクセスする必要がある場合は、SharePoint マネージ コード クライアント オブジェクト モデル (CSOM) または Representational State Transfer (REST) インターフェイスを使用できます。メソッド内での条件構造の場所は、メソッドの他のコードがどのように構造化されているかにより異なります。通常は、アドインのインストール イベントとアンインストール イベントをテストするのと似た条件構造になります。 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) に渡される [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) オブジェクトの特定のプロパティまたはサブプロパティで **null** 値または他の無効な値をテストする条件構造の内部になる場合があります。また、 **try** ブロックの中になることもあります。構造の例を次に示します。 _properties_ オブジェクトは [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) オブジェクトです。

 ```cs

if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

 ```

2. ハンドラー内で CSOM を使用するには、 **TokenHelper.CreateAppEventClientContext** メソッドを呼び出して [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) オブジェクトを取得する **using** ブロックを (条件ブロックの中に) 追加します。アドイン Web にアクセスするには、2 番目のパラメーターに **true** を指定します。ホスト Web にアクセスするには、 **false** を指定します。両方にアクセスする必要がある場合は、2 つの異なるクライアント コンテキスト オブジェクトが必要です。


3. ハンドラーで SharePoint 以外のコンポーネントにアクセスする必要がある場合は、そのコードをクライアント コンテキスト ブロックの外側に配置します。コードは次のような構造になります。

 ```cs

if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, true))
    {
        // CSOM code that accesses the add-in web
    }
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, false))
    {
        // CSOM code that accesses the host web
    }
    // Other update code
}

 ```

4. REST インターフェイスを使用するには、コードで **TokenHelper** クラスの他のメソッドを使用してアクセス トークンを取得します。その後、SharePoint に対して行う要求にこのトークンを組み込みます。詳細については、「 [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)」を参照してください。コードは次のような構造になります。

 ```cs

if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
    if (contextTokenString != null)
    {
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                                                                                                                  Request.Url.Authority);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                                   .AccessToken;

       // REST code that accesses SharePoint
    }
    // Other update code
}

 ```

5. SharePoint にアクセスするには、REST コードでホスト Web かアドイン Web またはその両方の URL もわかっている必要があります。これらの URL はどちらも、 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) メソッドに渡される [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) オブジェクトのサブプロパティです。次のコードではその取得方法を示します。

 ```cs

Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
 ```

アドインの 2 回目 (または 3 回目以降) の更新を行う場合、一部の更新ロジックが同じアドイン インスタンスに対して複数回実行されないようにすることが必要な場合があります。以下の手順ではその方法を示します。




### 2 回目以降の更新でのアドイン更新イベントを処理するには


1. AppEventReceiver.svc.cs ファイルを開き、1 回目の更新 (1.0.0.0 から 2.0.0.0) での更新アクションを実装したコードを探します。これを、以前の更新コードと呼ぶことにします (このコードは、通常、クライアント コンテキストまたはアクセス トークンを取得する承認コードの後にあります)。今は 2 回目の更新を行っており (2.0.0.0 から 3.0.0.0)、通常、以前の更新コードの中には、同じアドイン インスタンスに対して 2 回以上実行されないようにする必要がありますが、2.0.0.0 に更新されなかったアドイン インスタンスでは実行しなければならないアクションが存在します (この場合、インスタンスは 1.0.0.0 から 3.0.0.0 に更新されます)。


2. 前のバージョンのアドイン インスタンスを調べて、構造内のコードがこのアドイン インスタンスに対して前に実行されていない場合にのみ実行する条件構造の中に、前の更新コードをラップします。前のバージョンのインスタンスは、 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) オブジェクトの [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) サブプロパティに格納されています。


3. 新しい更新ロジック (2.0.0.0 から 3.0.0.0 への更新用) を、この構造に追加します。次に例を示します。

 ```cs

Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

 ```

4. 以降の更新ごとに、上記の手順を繰り返します。3.0.0.0 から 4.0.0.0 への更新の場合、コードは次のような構造になります。

 ```cs

Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 is here.
}

Version ver3OOO = new Version("3.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver3OOO)
{
    // Code to update from 2.0.0.0 to 3.0.0.0 (previous update code) is here.
}
// Code to update from 3.0.0.0 to 4.0.0.0 is here.

 ```


> **重要**
> **UpgradedEventEndpoint** ハンドラーでアドインにコンポーネントを追加する場合は、新規インストールでもそのコンポーネントをアドインに含める必要があるので、同じコードを **InstalledEventEndpoint** ハンドラーにも追加してください。また、コンポーネントを削除するための、アドインの [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) を追加する (または修正する) 必要があります。ほとんどの場合、 **InstalledEventEndpoint** によって追加または変更されたすべてのものを、 **UninstallingEventEndpoint** で元に戻すか削除する必要があります。例外として、アドインを削除済みデータ バックアップからアンインストールした後でも役に立つデータは削除しないようにする必要があります (アドインによって作成される Web サイト (アドイン Web 以外) は、データとみなす必要があります)。





## ハンドラーにロールバック ロジックを追加する
<a name="AddRollbackLogic"> </a>

アドインの更新中にエラーが発生した場合、SharePoint 2013 インフラストラクチャは更新プロセスを停止し、アドイン インスタンスおよびそのすべてのコンポーネントを前のバージョンのアドイン インスタンスにロールバックします。ただし、ユーザーの **UpgradedEventEndpoint** ハンドラーのロジックで行われたことはインフラストラクチャにはわからないので、元に戻すことができない場合があります。 **UpgradedEventEndpoint** ハンドラーの実行中にスローされたハンドルされない例外は、ほぼ確実に、アドイン更新プロセス全体をロールバックする必要があることを示しますが、 **UpgradedEventEndpoint** のコードで行われたことを元に戻す方法がインフラストラクチャにはわからないので行われません。このため、 **UpgradedEventEndpoint** のコードでは次のようにする必要があります。




1. すべての例外をキャッチします。


2. カスタム ロールバック コードに分岐し、その時点までに行われたことを元に戻します。


3. アドイン更新全体をロールバックする必要があることを、SharePoint 2013 インフラストラクチャに通知します。


4. エラー メッセージをインフラストラクチャに渡します。


 **UpgradedEventEndpoint** が行うすべてのことを、ハンドラーの内部で明示的に元に戻す必要はありません。アドイン Web はインフラストラクチャによってロールバックされるので、コードではアドイン Web に対する変更を元に戻す必要はありません。ただし通常は、ホスト Web または SharePoint アドインの外部にある他のコンポーネントに対して行われた変更は、 **UpgradedEventEndpoint** ハンドラーで元に戻す必要があります。



2 回目以降の更新の場合、例外ハンドラーのロジックでは、更新されているアドイン インスタンスが必ずしもすぐ前のバージョンではないことを考慮する必要があります。すぐ前のバージョンではない場合は、更新コードの複数のブロックを元に戻すことが必要になる場合があります。このため、前のバージョンの番号に基づく条件ロジックが必要です。次に示すのは、バージョン 4.0.0.0 への更新に対するロールバック ロジックの例です。





```cs

catch (Exception e)
{ 
    // Rollback of the 3.0.0.0 to 4.0.0.0 update logic goes here.

    Version ver3OOO = new Version("3.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver3OOO)
    {
        // Rollback of the 2.0.0.0 to 3.0.0.0 update logic goes here.
    }

    Version ver2OOO = new Version("2.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver2OOO)
    {
        // Rollback of the 1.0.0.0 to 2.0.0.0 update logic goes here.
    }
}```

エラー処理では、最後に **ProcessEvent** メソッドが SharePoint 2013 の更新インフラストラクチャに返す [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) オブジェクトに、エラー メッセージとキャンセル ステータスを割り当てることが必要です。次に例を示します。このコードでは、 _result_ は、 **ProcessEvent** メソッドの最初で宣言されている **SPRemoteEventResult** オブジェクトです。





```cs

catch (Exception e)
{ 
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}```


> **重要**
> **SPRemoteEventServiceStatus.CancelWithError** (または **SPRemoteEventServiceStatus.CancelNoError**) を  [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) プロパティに割り当てることが重要です。このプロパティが、更新をロールバックするようインフラストラクチャに通知します。ただし、SharePoint は更新のロールバックを行う前に 3 回ハンドラーを試行します。





### ハンドラー委任ストラテジの使用

ハンドラー委任ストラテジ (「 [アドイン イベントを処理する](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)」をご覧ください) を **UpdatedEventHandler** でも使用できます。ロールバックと「既に実行」ロジックがリモート コンポーネント上にバンドルされて実行されるだけでなく、バージョン管理ロジックも備わっています。また、複数のリモート システムの更新には通常は使用できないという、ストラテジの制限も当てはまります。




## 次の手順
<a name="Next"> </a>

「 [アドイン更新の主要な手順](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)」に戻るか、次のいずれかの記事に直接進んで SharePoint アドインの次の主要コンポーネントの更新方法を学習してください。




-  [SharePoint 2013 でアドイン Web コンポーネントを更新する](update-add-in-web-components-in-sharepoint-2013.md)


-  [SharePoint 2013 でホスト Web コンポーネントを更新する](update-host-web-components-in-sharepoint-2013.md)


-  [SharePoint アドインのリモート コンポーネントを更新する](update-remote-components-in-sharepoint-add-ins.md)



## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint アドインを更新する](update-sharepoint-add-ins.md)


-  [UpgradedEventEndpoint 要素 (PropertiesDefinition コントロール (SharePoint アドイン マニフェスト)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)


-  [SharePoint アドインのイベントの処理](handle-events-in-sharepoint-add-ins.md)


-  [SharePoint アドインでアドイン イベント レシーバーを作成する](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)



