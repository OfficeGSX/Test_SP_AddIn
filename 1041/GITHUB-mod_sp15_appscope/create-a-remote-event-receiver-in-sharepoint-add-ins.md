---
title: SharePoint アドインでリモート イベント レシーバーを作成する
ms.prod: SHAREPOINT
ms.assetid: 628c6103-52f9-4d85-9464-4a6862b36640
---


# SharePoint アドインでリモート イベント レシーバーを作成する
SharePoint アドイン でリスト イベントとリスト項目イベントを処理するリモート イベント レシーバー (RER) を作成します。
## 前提条件
<a name="SP15appevent_prereq"> </a>

事前にプロバイダー ホスト型 SharePoint アドインについて理解していること、少なくとも「Hello World」レベル以上のものを開発した経験があると助けになります。また、「 [SharePoint アドインのイベントの処理](handle-events-in-sharepoint-add-ins.md)」について精通している必要もあります。 




## リモート イベント レシーバーの作成
<a name="MakeRER"> </a>

この記事では、アドイン Web 内でカスタム リストの ItemAdded イベントを処理するリモート イベント レシーバー (RER) を追加して SharePoint アドイン を拡張する方法を示しています。この RER は宣言型マークアップを使用してアドイン Web に登録されています。RER は *ホスト Web*  にプログラムを使用して登録されています。そのためのコード サンプルについては、「 [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)」をご覧ください。



RER は SOAP Web サービスにする必要があります。継続して用いられている例では、Windows Communication Foundation (WCF) サービスとして実装しています。ただし、基本的には RER を Microsoft 以外のスタックに実装することが可能です。



この記事の内容に沿って自分でコードを入力する場合は、「 [SharePoint-Add-in-CSOM-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-CSOM-BasicDataOperations)」のサンプルをダウンロードしてから、Visual Studio で開きます。




> **メモ**
> このサンプルは、Office Developer Tools for Visual Studio によって生成された TokenHelper.cs ファイルを使用しています。このサンプルを作成する時点では最新バージョンでしたが、この記事をお読みになる時点では最新バージョンではない場合もあります。それでも、初めて RER を作成するときにこのサンプルはとても役立ちます。ただし、さらに理解を深めたい場合には、以下の「次の手順」セクションに記されているサンプルを参照してください。そちらの方が最新のものである可能性が高いです。 





### リモート イベント レシーバーを登録するには


1. Visual Studio で SharePoint アドイン プロジェクトを開きます。 


2. **ソリューション エクスプローラー**で、アドイン プロジェクトのノードを選択します。


3. メニュー バーで、[ **プロジェクト**]、[ **新しい項目の追加**] の順にクリックします。


4. [ **インストールされたテンプレート**] ウィンドウで、[ **Office/ SharePoint**] ノードをクリックします。


5. [ **テンプレート**] ウィンドウで、[ **リモート イベント レシーバー**] テンプレートをクリックします。


6. **[名前]** ボックスは既定の名前 (RemoteEventReceiver1) のままにし、 **[追加]** をクリックします。


7. [ **使用するイベント レシーバーの種類**] リストで、[ **リスト項目イベント**] をクリックします。


8. [ **イベント ソースとなる項目**] リストで、[ **カスタム リスト**] をクリックします。

    この記事の例では、カスタム ジェネリック リストを使用しています。ただし、RER は [ **お知らせ**] や [ **連絡先**] などの標準の SharePoint リストで発生するイベントも処理できます。


9. [ **次のイベントを処理**] リストで、[ **項目が追加されています**] を選択し、[ **完了**] ボタンをクリックします。

    Web サービスが、指定したリモート イベントを処理する Web アプリケーションに追加されます。リモート イベント レシーバーは SharePoint アドイン に追加され、リスト項目イベントはレシーバーの Elements.xml ファイル (アドイン Web 機能に含まれます) で参照されます。



### リストを作成するには


1. **ソリューション エクスプローラー**で、アドイン プロジェクトのノードを選択します。


2. メニュー バーで、[ **プロジェクト**]、[ **新しい項目の追加**] の順にクリックします。


3. [ **インストールされたテンプレート**] ウィンドウで、[ **Office/ SharePoint**] ノードをクリックします。


4. [ **テンプレート**] ウィンドウで、[ **リスト**] テンプレートをクリックします。


5. [ **名前**] ボックスは既定の名前 (List1) のままにし、[ **追加**] ボタンをクリックします。


6. [ **既存のリスト テンプレートに基づくリスト インスタンスを作成**] オプション ボタンをクリックし、リストの [ **カスタム リスト**] をクリックしたら、[ **完了**] ボタンをクリックします。



### リモート イベント レシーバーに機能を追加するには


1. SharePoint テスト ファームが Visual Studio が実行されているのと同じコンピューター上にない場合 (または SharePoint Online テナンシーを SharePoint テスト サイトとして使用している場合) には、Microsoft Azure サービス バスを使用してデバッグを行うようにプロジェクトを構成します。詳しくは、「 [SharePoint アドインでのリモート イベント レシーバーのデバッグとトラブルシューティング](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)」を参照してください。 


2. リモート イベント レシーバーのサービスのコード ファイル (つまり RemoteEventReceiver1.svc.cs) で、内容を次のコードに置き換えます。

    このコードでは、次のタスクを実行します。

  - 有効なクライアント コンテキスト オブジェクトを取得します。 


  - **EventLog** という名前のリストが存在しない場合は、発生するリモート イベントの名前が含まれるリストを作成します。


  - 日時のタイム スタンプを含むイベントについてのエントリをリストに追加します。



    > **メモ**
      > この記事が書かれた時点では、レシーバーが作成されると Office Developer Tools for Visual Studio によって、必要なアセンブリすべてへの参照が追加されましたが、以降のバージョンのツールではこうした処理が実行されない場合があります。コンパイラ エラーが発生する場合、欠落している参照を追加できます。たとえば、System.ServiceModel または System.ComponentModel.DataAnnotations への参照を追加することが必要になる場合があります。 



 ```cs

using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Text;
using Microsoft.SharePoint.Client;
using Microsoft.SharePoint.Client.EventReceivers;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.ServiceModel.Channels;


namespace BasicDataOperationsWeb.Services
{
    public class RemoteEventReceiver1 : IRemoteEventService
    {
        public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {
            // When a "before" event occurs (such as ItemAdding), call the event 
            // receiver code.
            ListRemoteEventReceiver(properties);
            return new SPRemoteEventResult();
        }

        public void ProcessOneWayEvent(SPRemoteEventProperties properties)
        {
            // When an "after" event occurs (such as ItemAdded), call the event 
            // receiver code.        
        }

        public static void ListRemoteEventReceiver(SPRemoteEventProperties properties)
        {
            string logListTitle = "EventLog";

            // Return if the event is from the EventLog list itself. Otherwise, it may go into
            // an infinite loop.
            if (string.Equals(properties.ItemEventProperties.ListTitle, logListTitle, 
                  StringComparison.OrdinalIgnoreCase))
                return;

            // Get the token from the request header.
            HttpRequestMessageProperty requestProperty = 
                  (HttpRequestMessageProperty)OperationContext
                   .Current.IncomingMessageProperties[HttpRequestMessageProperty.Name];
            string contextTokenString = requestProperty.Headers["X-SP-ContextToken"];

            // If there is a valid token, continue.
            if (contextTokenString != null)
            {
                SharePointContextToken contextToken =
                    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                         requestProperty.Headers[HttpRequestHeader.Host]);

                Uri sharepointUrl = new Uri(properties.ItemEventProperties.WebUrl);
                string accessToken = TokenHelper.GetAccessToken(contextToken, 
                                                      sharepointUrl.Authority).AccessToken;
                bool exists = false;

                // Retrieve the log list "EventLog" and add the name of the event that occurred
                // to it with a date/time stamp.
                using (ClientContext clientContext = 
                     TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), 
                                                                                                         accessToken))
                {
                    clientContext.Load(clientContext.Web);
                    clientContext.ExecuteQuery();
                    List logList = clientContext.Web.Lists.GetByTitle(logListTitle);

                    try
                    {
                        clientContext.Load(logList);
                        clientContext.ExecuteQuery();
                        exists = true;
                    }

                    catch (Microsoft.SharePoint.Client.ServerUnauthorizedAccessException)
                    {
                        // If the user doesn't have permissions to access the server that's 
                        // running SharePoint, return.
                        return;
                    }

                    catch (Microsoft.SharePoint.Client.ServerException)
                    {
                        // If an error occurs on the server that's running SharePoint, return.
                        exists = false;
                    }

                    // Create a log list called "EventLog" if it doesn't already exist.
                    if (!exists)
                    {
                        ListCreationInformation listInfo = new ListCreationInformation();
                        listInfo.Title = logListTitle;
                        // Create a generic custom list.
                        listInfo.TemplateType = 100;
                        clientContext.Web.Lists.Add(listInfo);
                        clientContext.Web.Context.ExecuteQuery();
                    }

                    // Add the event entry to the EventLog list.
                    string itemTitle = "Event: " + properties.EventType.ToString() + 
                          " occurred on: " + 
                          DateTime.Now.ToString(" yyyy/MM/dd/HH:mm:ss:fffffff");
                    ListCollection lists = clientContext.Web.Lists;
                    List selectedList = lists.GetByTitle(logListTitle);
                    clientContext.Load<ListCollection>(lists);
                    clientContext.Load<List>(selectedList);
                    ListItemCreationInformation listItemCreationInfo = 
                          new ListItemCreationInformation();
                    var listItem = selectedList.AddItem(listItemCreationInfo);
                    listItem["Title"] = itemTitle;
                    listItem.Update();
                    clientContext.ExecuteQuery();
                }
            }
        }
    }
}
 ```

3. Home.aspx.cs で、 `SPHostUrl` のすべてのインスタンスを `SPAppWebUrl` に変更します。

    たとえば、 `sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);` は `sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);` に変更します。



## イベント ハンドラーの実行とテスト
<a name="RunAndTest"> </a>

以下の手順で、ハンドラーをテストします。




1. **F5** キーを押してプロジェクトを実行します。


2. アドインを信頼するようにというプロンプトが表示されたら、それに従います。

    SharePoint アドインが実行され、使用可能なリストの表が表示されます。これには、 **List1** が含まれます。


3. **List1** の ID を選択します。

    その ID は [ **リスト項目を取得**] ボックスにコピーされます。


4. [ **リスト項目を取得**] ボタンをクリックします。

    項目を含まない **List1** が表示されます。


5. [ **項目の追加**] ボックスで「最初の項目」と指定し、[ **項目の追加**] ボタンをクリックします。

    [ **最初の項目**] という名前のリスト項目が **List1** に追加され、これによってリモート イベント レシーバーが起動し、EventLog リストにエントリが追加されます。


6. [ **リストの更新**] ボタンをクリックし、リストの表に戻ります。

    表には、 **EventLog** という名前の新しいリストが表示されます。


7. **EventLog** の **ListID** GUID 値を選択し、[ **リスト項目を取得**] ボタンをクリックします。

    **List1** に項目を追加したときに発生した **Handle ItemAdding** イベントのエントリを含む **EventLog** の表が表示されます。



## Visual Studio を使用したイベント ハンドラーの追加または削除
<a name="Handle"> </a>


1. **ソリューション エクスプローラー**で、リモート イベント レシーバーのプロジェクト ノードを選択します。


2. [ **プロパティ**] ウィンドウで、処理するイベントのプロパティを **True** に設定します。

    たとえば、ユーザーがリスト項目を追加したときに応答するには、 **Handle ItemAdding** プロパティの値を **True** に設定します。そのイベントを処理しないようにするには、そのプロパティの値を **False** に設定します。


   **図 1. Visual Studio の SharePoint リモート イベント**



!\[Visual Studio での SharePoint リモート イベント](images/SP_VS_Properties_Window_RemoteEvents.PNG)





3. イベントを追加した場合は、前のイベントで追加したのと同様に、コード ファイルにイベント処理コードを追加します。

    別の種類のイベントを処理するには、別のリモート イベント レシーバーを SharePoint アドインに追加します。たとえば、あるリモート イベント レシーバーでリスト項目イベントを処理する場合、そのレシーバーに別のリスト項目イベントを追加できます。ただし、リスト イベントを処理するには、別のリモート イベント レシーバーを追加する必要があります。 



## 運用環境のリモート レシーバー イベントの URL とホストの制限
<a name="Handle"> </a>

リモート イベント レシーバーが SharePoint サーバーとしても使用されていないクラウドまたはオンプレミス サーバーでホストされる可能性があります。運用環境のレシーバー URL で特定のポートを指定することはできません。つまり、HTTPS のポート 443 (推奨) と HTTP のポート 80 のいずれかを使用する必要があります。HTTPS を使用し、レシーバー サービスがオンプレミスでホストされ、アドインは Microsoft SharePoint Online 上にある場合には、ホスト サーバーには、証明機関で公的に信頼されている証明書が必要です (自己署名証明書が有効になるのは、アドインがオンプレミスの SharePoint ファームにある場合のみです)。




## 次の手順
<a name="Handle"> </a>

以下のコード サンプルを使用して、RER に関する理解を深めてください。




-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)


-  [OfficeDev/PnP/Samples/Provisioning.ReR](
https://github.com/OfficeDev/PnP/tree/master/Samples/Provisioning.ReR)


-  [OfficeDev/PnP/Scenarios/ECM.AutoTagging](https://github.com/OfficeDev/PnP/tree/master/Samples/ECM.AutoTagging)



## その他の技術情報
<a name="Additional"> </a>


-  [SharePoint アドインのイベントの処理](handle-events-in-sharepoint-add-ins.md)


-  [SharePoint アドインでのリモート イベント レシーバーのデバッグとトラブルシューティング](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)


-  [リモート イベント レシーバーに関してよく寄せられる質問](handle-events-in-sharepoint-add-ins.md#RERFAQ)



