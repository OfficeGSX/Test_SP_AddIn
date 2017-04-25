---
title: プロバイダー向けのホスト型アドインでリスト項目一覧を処理する
ms.prod: SHAREPOINT
ms.assetid: 4534e0f5-61ef-4145-a63b-a9fa70f51391
---


# プロバイダー向けのホスト型アドインでリスト項目一覧を処理する
プロバイダー向けのホスト型 SharePoint アドインのリスト アイテム イベントを処理する方法について説明します。
これは、プロバイダー向けのホスト型 SharePoint アドインの開発の基本に関する記事のシリーズの 10 番目です。「 [SharePoint アドイン](sharepoint-add-ins.md)」とこのシリーズの前の記事を十分に理解している必要があります。
  
    
    


-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の外観を付与する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [プロバイダー向けのホスト型アドインにカスタム ボタンを含める](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [SharePoint オブジェクト モデルの概要を簡単に説明する](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [プロバイダー向けのホスト型アドインに SharePoint の書き込み操作を追加する](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインにアドインの一部を含める](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインでアドイン イベントを処理する](handle-add-in-events-in-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインに初回実行時のロジックを追加する](add-first-run-logic-to-the-provider-hosted-add-in.md)
    
  
-  [プロバイダー向けのホスト型アドインでカスタム ボタンをプログラムで展開する](programmatically-deploy-a-custom-button-in-the-provider-hosted-add-in.md)
    
  

> **メモ**
> プロバイダー向けのホスト型アドインに関するこのシリーズを通じて作業してきた場合は、このトピックを続行するために利用できる Visual Studio ソリューションがあります。また、「 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)」でリポジトリをダウンロードして、BeforeRER.sln ファイルを開くこともできます。 
  
    
    

このシリーズの前の記事で、注文があると、企業のデータベース内の **[注文]** テーブルに追加され、そのアイテムが **[出荷予定]** リストに自動的に追加されることを学びました。注文品がローカル ストアに届くと、ユーザーは **[配送済み]** 列を **[はい]** に設定します。アイテムのフィールド値を変更すると、アイテム更新イベントが作成され、カスタム ハンドラーを追加できます。この記事では、このリスト アイテム イベントのハンドラーを作成してから、SharePoint アドインの初回実行時ロジックでプログラムによってデプロイします。ハンドラーは、アイテムを企業データベース内の **[在庫]** テーブルに追加します。それから、 **[出荷予定]** リストの **[在庫に追加済み]** 列を **[はい]** に設定します。この 2 つ目のアイテム更新イベントにより、無限に一連のアイテム更新イベントが作動しないようにする方法についても学びます。
## [出荷予定] リストのプログラムによるデプロイ


> **メモ**
> Visual Studio のスタートアップ プロジェクトの設定は、ソリューションが再び開かれるたびに既定値に戻る傾向があります。このシリーズの記事では、サンプル ソリューションを再び開いた直後に必ずこのステップを実行してください。 
  
    
    


1. **ソリューション エクスプローラー**で、 **ChainStoreWeb** プロジェクト内の Utilities\\SharePointComponentDeployer.cs ファイルを開きます。 `SharePointComponentDeployer` クラスに以下のメソッドを追加します。このコードでこのシリーズの前の記事に記述されていなかった機能は導入されませんが、以下の点に注意してください。
    
  - **[数量]** フィールドの **Required** 属性が **TRUE** に設定されるので、このフィールドに必ず値を指定する必要があります。その後、既定値 を 1 に設定します。
    
  
  - [新しいアイテム] フォームの **[配送済み]** と **[在庫に追加済み]** のフィールドが非表示になります。
    
  
  - [アイテムの編集] フォーム上の **[在庫に追加済み]** フィールドも非表示にすることをお勧めします。アイテム更新イベント ハンドラーが初めて企業の **[在庫]** テーブルにアイテムを追加した場合だけこのフィールドを **[はい]** に変更する必要があるからです。後のステップで説明する技術的な理由から、プログラムによりアイテム更新イベント ハンドラーで [アイテムの編集] フォーム内のフィールドに書き込む場合、そのフィールドを表示する必要があります。
    
  

 ```cs
  
private static void CreateExpectedShipmentsList()
 {
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
        var query = from list in clientContext.Web.Lists
                    where list.Title == "Expected Shipments"
                    select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();
                   
        if (matchingLists.Count() == 0)
        {
                ListCreationInformation listInfo = new ListCreationInformation();
                listInfo.Title = "Expected Shipments";
                listInfo.TemplateType = (int)ListTemplateType.GenericList;
                listInfo.Url = "Lists/ExpectedShipments";
                List expectedShipmentsList = clientContext.Web.Lists.Add(listInfo);

                Field field = expectedShipmentsList.Fields.GetByInternalNameOrTitle("Title");
                field.Title = "Product";
                field.Update();

                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Supplier'" 
                                                            + " Type='Text' />", 
                                                            true,
                                                            AddFieldOptions.DefaultValue);
                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Quantity'" 
                                                            + " Type='Number'" 
                                                            + " Required='TRUE' >" 
                                                            + "<Default>1</Default></Field>",
                                                            true, 
                                                            AddFieldOptions.DefaultValue);
                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Arrived'" 
                                                           + " Type='Boolean'"
                                                           + " ShowInNewForm='FALSE'>"
                                                           + "<Default>FALSE</Default></Field>",
                                                            true, 
                                                            AddFieldOptions.DefaultValue);
                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Added to Inventory'" 
                                                            + " Type='Boolean'" 
                                                            + " ShowInNewForm='FALSE'>"
                                                            + "<Default>FALSE</Default></Field>", 
                                                            true, 
                                                            AddFieldOptions.DefaultValue);

                clientContext.ExecuteQuery();
        }
     }
 }
 ```

2.  `DeployChainStoreComponentsToHostWeb` メソッドで、次の行を `RemoteTenantVersion = localTenantVersion` 行のすぐ上に追加します。
    
 ```
  
CreateExpectedShipmentsList();
 ```


## リスト アイテム イベント レシーバーの作成


> **メモ**
> このシリーズの記事を通じて作業している場合は、既に開発環境をリモート イベント レシーバーのデバッグ用に構成しています。構成していない場合は、「 [イベント レシーバーのデバッグのためのソリューションを構成する](handle-add-in-events-in-the-provider-hosted-add-in.md#RERDebug)」を参照してからこのトピックを進めてください。 
  
    
    

Office Developer Tools for Visual Studio には **リモート イベント レシーバー** というアイテムが組み込まれていて、SharePoint アドイン ソリューションに追加できます。しかし、この記事が書かれた時点で、このプロジェクト アイテムは (このレシーバーが登録される) リストがアドイン Web 上にあると想定しているので、ツールではアドイン Web を作成して、そのアドイン Web 内に SharePoint 成果物を作成します。しかし、(後のステップでは) チェーン店のアドインのレシーバーをホスト Web 上の **[出荷予定]** リストに登録するので、このアドインにはアドイン Web は必要ありません (アドイン Web とホスト Web の違いについては、「 [SharePoint アドイン](sharepoint-add-ins.md)」をご覧ください)。
  
    
    

> **メモ**
> リストとリスト アイテム イベント レシーバーは、リモート イベント レシーバー (RER) と呼ばれます。なぜなら、そのコードが SharePoint から遠く離れており、SharePoint ファーム外のクラウド内かオンプレミスのサーバー内にあるからです。しかし、このレシーバーをトリガーするイベントは SharePoint 内にあります。
  
    
    


1. **ソリューション エクスプローラー**で、 **ChainStoreWeb** プロジェクト内の **サービス** フォルダーを右クリックして、 **[追加] | [WCF サービス]** を選択します。
    
  
2. 入力を要求するメッセージが表示されたら、サービスに RemoteEventReceiver1 という名前を指定して、 **[OK]** を押します。
    
  
3. ツールにより、インターフェイス ファイル (*.svc ファイル) と分離コード ファイルが作成されます。インターフェイス ファイル IRemoteEventReceiver1.cs は必要ないので削除します (ツールでこのファイルが自動的に開かれる可能性があるので、その場合は閉じて削除します)。
    
    > **メモ**
      > このシリーズの前の記事で、インストールしたイベントとアンインストールするイベントのアドイン イベント レシーバーを作成したときに、Office Developer Tools for Visual Studio がその URL をアプリ マニフェスト ファイルに追加しました。リストおよびリスト アイテム イベント レシーバーは、アプリ マニフェスト内に登録されません。その代わり、プログラムによって (プロバイダー向けのホスト型アドイン内に) 登録されます。後のステップで実行します。 
4. 分離コード ファイル RemoteEventReceiver1.svc.cs を開きます。そのコンテンツ全体を次のコードに置き換えます。このコードの以下の点に注目してください。
    
  - インターフェイス  `IRemoteEventService` は、 **Microsoft.SharePoint.Client.EventReceivers** 名前空間内に定義されています。
    
  
  - チェーン店アドインで処理される "before" イベントはありませんが、 **ProcessEvent** メソッドは `IRemoteEventService` インターフェイスで必要です。
    
  

 ```cs
  using System;
using System.Collections.Generic;
using Microsoft.SharePoint.Client;
using Microsoft.SharePoint.Client.EventReceivers;
using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;

namespace ChainStoreWeb.Services
{
    public class RemoteEventReceiver1 : IRemoteEventService
    {
        /// <summary>
        /// Handles events that occur before an action occurs, 
        /// such as when a user is adding or deleting a list item.
        /// </summary>
        /// <param name="properties">Holds information about the remote event.</param>
        /// <returns>Holds information returned from the remote event.</returns>
        public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {
            throw new NotImplementedException();
        }

        /// <summary>
        /// Handles events that occur after an action occurs, 
        /// such as after a user adds an item to a list or deletes an item from a list.
        /// </summary>
        /// <param name="properties">Holds information about the remote event.</param>
        public void ProcessOneWayEvent(SPRemoteEventProperties properties)
        {

        }
    }
}
 ```

5. 次のコードを  `ProcessOneWayEvent` メソッドに追加します。このサンプルが処理するイベントは **ItemUpdated** イベントだけなので、これまで単純な **if** 構造を **switch** の代わりに使用してきたことに留意してください。しかし、通常イベント レシーバーは複数のイベントを処理するので、ここでは SharePoint アドイン開発者がイベント ハンドラーで最もよく使用するパターンを確認します。
    
 ```cs
  
switch (properties.EventType)
{
    case SPRemoteEventType.ItemUpdated:

        // TODO12: Handle the item updated event.
                    
        break;
}  
 ```

6.  `TODO12` を次のコードに置き換えます。ここでも、SharePoint イベント レシーバー内の一般的なパターンを確認するために、単純な **if** 構造を使用する代わりに **switch** 構造を使用しています。
    
 ```cs
  
switch (properties.ItemEventProperties.ListTitle)
{
    case "Expected Shipments":

        // TODO13: Handle the arrival of a shipment.

        break;
}
 ```

7. 品物の配送に応答するコードは、次の 2 つの処理が必要です。
    
  - ストアに届いたアイテムを、企業の在庫に追加します。
    
  
  - **[出荷予定]** リスト上の **[在庫に追加済み]** フィールドを **[はい]** に設定します。しかし、この操作はアイテムが在庫に追加された場合のみ必要です。
    
  

     `TODO13` の代わりに次のコードを追加 します。後のステップで、 `TryUpdateInventory` と `RecordInventoryUpdateLocally` の 2 つのメソッドを作成します。
    


 ```cs
  
bool updateComplete = TryUpdateInventory(properties);
if (updateComplete)
{
    RecordInventoryUpdateLocally(properties);
}
 ```


     `ProcessOneWayEvent` メソッドは次のようになります。
    


 ```cs
  
public void ProcessOneWayEvent(SPRemoteEventProperties properties)
{
    switch (properties.EventType)
    {
        case SPRemoteEventType.ItemUpdated:

            switch (properties.ItemEventProperties.ListTitle)
            {
                case "Expected Shipments":
                    bool updateComplete = UpdateInventory(properties);
                    if (updateComplete)
                    {
                        RecordInventoryUpdateLocally(properties);
                    }
                    break;
            }
            break;
    }          
}
 ```

8.  `RemoteEventReceiver1` クラスに次のメソッドを追加します。
    
 ```cs
  
private bool TryUpdateInventory(SPRemoteEventProperties properties)
{
    bool successFlag = false;

        // TODO14: Test whether the list item is changing because the product has arrived
        // or for some other reason. If the former, add it to the inventory and set the success flag
        // to true.     

    return successFlag;
}
 ```

9. **[出荷予定]** リストには 5 つの列がありますが、ハンドラーがアイテムに対するほとんどの種類の更新に応答することは望ましくありません。たとえば、ユーザーが業者の名前のつづりを訂正すると、アイテム更新イベントがトリガーされますが、ハンドラーは応答すべきではありません。ハンドラーが応答する必要があるのは、 **[配送済み]** フィールドが **[はい]** に設定された直後だけです。
    
    テストが必要な別の条件もあります。 **[配送済み]** が **[はい]** に設定され、アイテムの製品が在庫に追加された (さらに **[在庫に追加済み]** が **[はい]** に設定された) とします。しかし、その後ユーザーが誤って出荷の **[配送済み]** フィールドを変更して **[いいえ]** に戻してから、誤りを修正するためもう一度 **[配送済み]** に設定したとします。誤った操作と修正操作の両方で、アイテム更新イベントがトリガーされます。ハンドラーは **[配送済み]** が **[はい]** の場合のみ応答するので、誤った操作には応答しませんが、 **[配送済み]** を **[はい]** に戻した修正操作には応答します。つまり、同じ製品と数量が在庫に 2 回追加されることになります。このような理由で、ハンドラーは **[在庫に追加済み]** の値が **[いいえ]** の場合にのみ応答する必要があります。
    
    したがってハンドラーは、ユーザーがアイテムを更新した直後のこれらのフィールドの値を把握する必要があります。 **SPRemoteEventProperties** オブジェクトには **ItemEventProperties** プロパティがあります。同様に、インデックス付きの **AfterProperties** プロパティがあり、このプロパティには更新されたアイテム内のフィールドの値が保持されます。次のコードは、これらのプロパティを使って、ハンドラーが応答する必要があるかどうかをテストします。 `TODO14` の代わりにこのコードを使用してください。
    


 ```cs
  
var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);
var addedToInventory = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Added_x0020_to_x0020_Inventory"]);

if (arrived &amp;&amp; !addedToInventory)
{

    // TODO15: Add the item to inventory

    successFlag = true;
}
 ```

10.  `TODO15` を次のコードに置き換えます。これは主に SQL と ASP.NET のプログラミングなので詳しくは説明していませんが、以下の点に注意してください。
    
  - **ItemEventProperties.WebUrl** プロパティを使用してテナント名を取得します。テナント名は、ホスト Web の URL です。
    
  
  - もう一度 **AfterProperties** を使用して、製品の名前と数量の値を取得します。
    
  
  - 表示名が ( `CreateExpectedShipmentsList` メソッドで) "Product" に変更されても、製品名フィールドは "Title" として参照されます。これは、フィールドが常に内部名によって参照されるからです。
    
  

 ```cs
  
using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
using (SqlCommand cmd = conn.CreateCommand())
{
    conn.Open();
    cmd.CommandText = "UpdateInventory";
    cmd.CommandType = CommandType.StoredProcedure;
    SqlParameter tenant = cmd.Parameters.Add("@Tenant", SqlDbType.NVarChar);
    tenant.Value = properties.ItemEventProperties.WebUrl + "/";
    SqlParameter product = cmd.Parameters.Add("@ItemName", SqlDbType.NVarChar, 50);
    product.Value = properties.ItemEventProperties.AfterProperties["Title"]; // not "Product"
    SqlParameter quantity = cmd.Parameters.Add("@Quantity", SqlDbType.SmallInt);
    quantity.Value = Convert.ToUInt16(properties.ItemEventProperties.AfterProperties["Quantity"]);
    cmd.ExecuteNonQuery();
}
 ```


     `TryUpdateInventory` メソッドをまだ終了していませんが、この時点で次のようになります。
    


 ```cs
  
private bool TryUpdateInventory(SPRemoteEventProperties properties)
{
    bool successFlag = false;

    var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);
    var addedToInventory = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Added_x0020_to_x0020_Inventory"]);

    if (arrived &amp;&amp; !addedToInventory)
    {
        using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
        using (SqlCommand cmd = conn.CreateCommand())
        {
            conn.Open();
            cmd.CommandText = "UpdateInventory";
            cmd.CommandType = CommandType.StoredProcedure;
            SqlParameter tenant = cmd.Parameters.Add("@Tenant", SqlDbType.NVarChar);
            tenant.Value = properties.ItemEventProperties.WebUrl + "/";
            SqlParameter product = cmd.Parameters.Add("@ItemName", SqlDbType.NVarChar, 50);
            product.Value = properties.ItemEventProperties.AfterProperties["Title"]; // not "Product"
            SqlParameter quantity = cmd.Parameters.Add("@Quantity", SqlDbType.SmallInt);
            quantity.Value = Convert.ToUInt16(properties.ItemEventProperties.AfterProperties["Quantity"]);
            cmd.ExecuteNonQuery();
        }            
        successFlag = true;
    }  
    return successFlag;
}
 ```

11.  `TryUpdateInventory` メソッドが **true** を返すと、ハンドラーは **[在庫に追加済み]** フィールドを **[はい]** に設定して、 **[出荷予定]** リスト内の同じアイテムを更新する (まだ作成されていない) メソッドを呼び出します。このメソッド自体はアイテム更新イベントなので、ハンドラーをもう一度呼び出します (現在 **[在庫に追加済み]** フィールドは **[はい]** で、、ハンドラーが同じ出荷を 2 回在庫に追加できなくなっていますが、それでもハンドラーが呼び出されます)。
    
    しかし、SharePoint の動作は、プログラムによる更新でアイテム更新イベントをトリガーする場合とは多少違います。 *SharePoint は、更新により変更されたフィールドのみを **AfterProperties** に組み込みます。*  したがって、変更されたのは **[在庫に追加済み]** フィールドだけなので、 **[配送済み]** フィールドは存在しません。この行
    
     `var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);`
    
     は、 **KeyNotFoundException** をスローします。
    
    この問題を解決する方法は複数あります。このサンプルでは、例外をキャッチし、 **catch** ブロックを使用して、確実に `successFlag` が **false** に設定されるようにします。こうすると、アイテムが 3 回更新されることはありません。
    
    メソッド内の最初の行  `bool successFlag = false;` と最後の行 `return successFlag;` の間にあるものをすべて **try** ブロックに組み込みます。
    
  
12. 次の **catch** ブロックを、 **try** ブロックのすぐ下に追加します。
    
 ```cs
  
catch (KeyNotFoundException)
{
    successFlag = false;
}
 ```


    > **メモ**
      > [アイテムの編集] フォームで **[在庫に追加済み]** フィールドを表示したままにしておく必要がある別の理由は **KeyNotFoundException** です。SharePoint は、[アイテムの編集] フォームで非表示になっているフィールドを **AfterProperties** に組み込みません。

    メソッド全体は次のようになります。
    


 ```cs
  
private bool TryUpdateInventory(SPRemoteEventProperties properties)
{
    bool successFlag = false;
    
    try 
    {
        var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);
        var addedToInventory = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Added_x0020_to_x0020_Inventory"]);

        if (arrived &amp;&amp; !addedToInventory)
        {
            using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
            using (SqlCommand cmd = conn.CreateCommand())
            {
                conn.Open();
                cmd.CommandText = "UpdateInventory";
                cmd.CommandType = CommandType.StoredProcedure;
                SqlParameter tenant = cmd.Parameters.Add("@Tenant", SqlDbType.NVarChar);
                tenant.Value = properties.ItemEventProperties.WebUrl + "/";
                SqlParameter product = cmd.Parameters.Add("@ItemName", SqlDbType.NVarChar, 50);
                product.Value = properties.ItemEventProperties.AfterProperties["Title"]; // not "Product"
                SqlParameter quantity = cmd.Parameters.Add("@Quantity", SqlDbType.SmallInt);
                quantity.Value = Convert.ToUInt16(properties.ItemEventProperties.AfterProperties["Quantity"]);
                cmd.ExecuteNonQuery();
            }            
            successFlag = true;
        }  
    }
    catch (KeyNotFoundException)
    {
        successFlag = false;
    }
    return successFlag;
}
 ```

13.  `RemoteEventReceiver1` クラスに次のメソッドを追加します。このコードのパターンはこのシリーズの前の記事から使い続けているものです。しかし、1 つの違いに注目してください。このコードは、EmployeeAdder ページなどのページから SharePoint へ呼び出されるコード内で使用した **SharePointContext.CreateUserClientContextForSPHost** メソッドではなく **TokenHelper.CreateRemoteEventReceiverClientContext** メソッドを呼び出して **ClientContext** オブジェクトを取得します。 **ClientContext** オブジェクトを取得するさまざまなメソッドがある主な理由は、SharePoint がこの種のオブジェクトの作成に必要な情報をイベント レシーバーに渡す方法とページに渡す方法が違うからです。イベント レシーバーの場合は、 **SPRemoteEventProperties** オブジェクトを渡しますが、ページの場合は、アドイン ページを起動する要求の本文でコンテキスト トークンと呼ばれる特別なフィールドを渡します。
    
 ```cs
  
private void RecordInventoryUpdateLocally(SPRemoteEventProperties properties)
{
    using (ClientContext clientContext = TokenHelper.CreateRemoteEventReceiverClientContext(properties))
    {
        List expectedShipmentslist = clientContext.Web.Lists.GetByTitle(properties.ItemEventProperties.ListTitle);
        ListItem arrivedItem = expectedShipmentslist.GetItemById(properties.ItemEventProperties.ListItemId);
        arrivedItem["Added_x0020_to_x0020_Inventory"] = true;
        arrivedItem.Update();
        clientContext.ExecuteQuery();
    }
}
 ```

14. レシーバーのコード ファイルを保存して閉じます。
    
  

## レシーバーの登録

最後のタスクは、 **[出荷予定]** リストのアイテムが更新されるたびに、あるカスタム レシーバーを SharePoint で呼び出すように、SharePoint に指示することです。
  
    
    

1. >SharePointContentDeployer.cs ファイルを開き、次の行を  `DeployChainStoreComponentsToHostWeb` メソッドの **[出荷予定]** リストを作成する行のすぐ下に追加します。次のステップでこのメソッドを追加します。アドインのスタート ページで `DeployChainStoreComponentsToHostWeb` メソッドに渡した **HttpRequest** オブジェクトをこのメソッドに渡すことに注意してください。
    
 ```cs
  
RegisterExpectedShipmentsEventHandler(request);
 ```

2.  `SharePointComponentDeployer` クラスに以下のメソッドを追加します。
    
 ```cs
  private static void RegisterExpectedShipmentsEventHandler(HttpRequest request)
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())    
    {
        var query = from list in clientContext.Web.Lists
                    where list.Title == "Expected Shipments"
                    select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();

        List expectedShipmentsList = matchingLists.Single();

        // TODO16: Add the event receiver to the list's collection of event receivers.       

        clientContext.ExecuteQuery();
    }
}
 ```

3.  `TODO16` を次の行に置き換えます。リストとリスト アイテムと同様に、イベント レシーバーには軽量の ***CreationInformation** クラスがあることに注意してください。
    
 ```cs
  
EventReceiverDefinitionCreationInformation receiver = new EventReceiverDefinitionCreationInformation();
receiver.ReceiverName = "ExpectedShipmentsItemUpdated";
receiver.EventType = EventReceiverType.ItemUpdated;

 // TODO17: Set the URL of the receiver.

expectedShipmentsList.EventReceivers.Add(receiver);

 ```

4. この時点で、SharePoint にイベント レシーバーの URL を知らせる必要があります。運用環境では、リモート ページと同じドメインになり、パスは /Services/RemoteEventReceiver1.svc です。ハンドラーはアドインのスタート ページからの初回実行時ロジックに登録されるので、このドメインは、ページを呼び出した要求に関する **HttpRequest** オブジェクトのホスト ヘッダー内にあります。コードはこのオブジェクトをページから `DeployChainStoreComponentsToHostWeb` メソッドに渡し、このメソッド自体はオブジェクトを `RegisterExpectedShipmentsEventHandler` メソッドに渡しました。したがって、次のコードでレシーバーの URL を設定できます。
    
     `receiver.ReceiverUrl = "https://" + request.Headers["Host"] + "/Services/RemoteEventReceiver1.svc";`
    
    残念ながら、Visual Studio からアドインをデバッグする際にはこの URL は機能しません。レシーバーはデバッグ時には、リモート ページのホスト場所の localhost の URL ではなく、Azure Service Bus でホストされます。デバッグするかどうかに応じて別個のレシーバーの URL を設定する必要があるので、 `TODO17` を C# コンパイラ ディレクティブを使用する以下の構造に置き換えます。デバッグ モードでは、レシーバーの URL は web.config 設定から読み取られることに注意してください。 *後のステップでこの設定を作成します。* 
    


 ```cs
  
#if DEBUG
                    receiver.ReceiverUrl = WebConfigurationManager.AppSettings["RERdebuggingServiceBusUrl"].ToString();
#else
                    receiver.ReceiverUrl = "https://" + request.Headers["Host"] + "/Services/RemoteEventReceiver1.svc"; 
#endif

 ```


     `RegisterExpectedShipmentsEventHandler` メソッド全体は次のようになります。
    


 ```cs
  
private static void RegisterExpectedShipmentsEventHandler(HttpRequest request)
{    
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
        var query = from list in clientContext.Web.Lists
		            where list.Title == "Expected Shipments"
		            select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();

        List expectedShipmentsList = matchingLists.Single();

        EventReceiverDefinitionCreationInformation receiver = new EventReceiverDefinitionCreationInformation();
        receiver.ReceiverName = "ExpectedShipmentsItemUpdated";
        receiver.EventType = EventReceiverType.ItemUpdated;

#if DEBUG
        receiver.ReceiverUrl = WebConfigurationManager.AppSettings["RERdebuggingServiceBusUrl"].ToString();
#else
        receiver.ReceiverUrl = "https://" + request.Headers["Host"] + "/Services/RemoteEventReceiver1.svc"; 
#endif
        expectedShipmentsList.EventReceivers.Add(receiver);
        clientContext.ExecuteQuery();
    }
}
 ```

5. 次の **using** ステートメントをファイルの一番上に追加します。
    
 ```cs
  
using System.Web.Configuration;
 ```

6. アドインがデバッグされる場合に限り  `DEBUG` が true になることを確認するには、次の Sub プロシージャを実行します。
    
1. **ソリューション エクスプローラー**で、 **ChainStoreWeb** プロジェクトを右クリックして、 **[プロパティ]** を選択します。
    
  
2. **[プロパティ]** の **[ビルド]** タブを開いてから、上部の **[構成]** ドロップダウンから **[デバッグ]** を選択します。
    
  
3. **[DEBUG 定数の定義]** チェック ボックスがオンになっていることを確認 します (通常は既定でオンになっています)。次のスクリーン ショットは、適切な設定を示しています。
    
!\[Visual Studio の [プロパティ] タブ内の [ビルド] サブタブです。[構成] ドロップ ダウンは [デバッグ] に設定されています。[DEBUG 定数の定義] チェック ボックスがオンになっています。](images/4f81174f-d875-4a9e-bff4-adea0f176f00.PNG)
  

  

  
4. **[構成]**ドロップダウンを **[リリース]** に変更してから、 **[DEBUG 定数の定義]** チェック ボックスがオンになって ** *いない* ** ことを確認します (通常は既定でオンになっています)。次のスクリーン ショットは、適切な設定を示しています。
    
!\[[プロパティ] タブ内の [ビルド] サブタブ。[構成] ドロップ ダウンでは [リリース] が選択されています。[DEBUG 定数の定義] チェック ボックスはチェックされていません。](images/7fd942de-a324-4f70-a750-f5304c993832.PNG)
  

  

  
5. 変更を加えた場合は、保存してから **[プロパティ]** タブを閉じます。
    
  
7. web.config ファイルを開き、 **appSettings** 要素の子として次のマークアップを追加します。次のセクションで、この設定の値を取得します。
    
 ```XML
  <add key="RERdebuggingServiceBusUrl" value="" />
 ```


## デバッグ用のレシーバー URL の取得

アドインのイベントとリスト アイテム イベント レシーバーは WCF (Windows Communication Service) サービスです。すべての WCF サービスは独自のエンドポイントを認識し、 **System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri** オブジェクトを含む複数の場所に格納します。デバッグ時に、アドイン レシーバーは Azure Service Bus エンドポイントでホストされます。このエンドポイントはリスト アイテム レシーバーのエンドポイントとほぼ同じですが、アドイン エンドポイントの URL は末尾が "AppEventReceiver.svc" であるのに対して、リスト アイテム レシーバーの URL は末尾が "RemoteEventReceiver1.svc" です。したがって、アドイン レシーバーのエンドポイントの URL を取得し、末尾に多少の変更を加えてから、web.config の **RERdebuggingServiceBusUrl** 設定の値として使用できます。
  
    
    

1. **ChainStoreWeb** プロジェクトの **[サービス]** フォルダーで、AppEventReceiver.svc.cs を開きます。
    
  
2. **ProcessEvent** メソッド内の最初の行として以下を追加します。
    
 ```cs
  string debugEndpoint = System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri.ToString(); 
 ```

3. メソッドの次の行にブレークポイントを追加します。
    
  
4. F5 キーを押してアドインをデバッグします。web.config が開いており、F5 キーを押すたびに Office Developer Tools for Visual Studio により設定が変更されるので、再読み込みを求めるメッセージが表示されます。 **[はい]** を選択します。
    
  
5. ブレークポイントにヒットしたら、 `debugEndpoint` 変数の上にカーソルを置きます。Visual Studio データのヒントが表示されたら、下矢印をクリックし、 **[テキスト ビジュアライザー]** を選択します。
    
!\[Azure Service Bus URL が表示されている Visual Studio テキスト ビジュアライザー。](images/494cf01e-3e17-4092-b239-9312ac4ab258.PNG)
  

  

  
6. ビジュアライザーから文字列の値をコピーし、任意の場所に貼り付けます。
    
  
7. ビジュアライザーを閉じてから、Visual Studio でデバッグを停止します。
    
  
8. このプロシージャの 2 番目のステップで追加した行を削除するかコメントにしてから、ブレークポイントも削除します。
    
  
9. コピーした文字列で、末尾の "AppEventReceiver.svc" を "RemoteEventReceiver1.svc" に置き換えます。
    
  
10. 変更した URL をコピーして、web.config ファイル内に **RERdebuggingServiceBusUrl** キーの値として貼り付けます。
    
  

> **メモ**
>  リモート イベント レシーバーを運用環境での実行時からデバッグする場合、サービス バスの URL を手動でコピーして、(変更したバージョンの) URL を web.config 内に貼り付ける以外にも、別の URL を使用する必要に対処する方法があります。> **System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri** の値を SharePoint の任意の場所かリモート データベース内にプログラムによって保存してから、初回実行時のコードでこの値を読み取って `receiver.ReceiverUrl` プロパティに割り当てることもできます。>  リスト アイテム イベント レシーバーを、アドインをインストールしたイベント ハンドラーの一部として登録することもできます。それからどこにも保存せずに、プログラムによって **System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri** を読み取り、変更して、 `receiver.ReceiverUrl` に割り当てることもできます。この戦略では、アドインをインストールしたイベント ハンドラーで **[出荷予定]** リストも作成する必要があります。このリストが存在していないと、ハンドラーを登録できないからです (アドイン イベント レシーバーとリスト アイテム イベント レシーバーを組み合わせて単一のレシーバーにできる (つまり .svc ファイルと svc.cs ファイルが同じ) ことにも注意してください。この場合、 `receiver.ReceiverUrl` の値として URL を使用する前にその URL を変更する必要はありません)。
  
    
    


## アドインの実行とリスト アイテム レシーバーのテスト


  
    
    

1. 香港店の Web サイトの **[サイト コンテンツ]** ページを開き、 * **[出荷予定]** リストがある場合は削除します。* 
    
  
2. F5 キーを使用し、アドインをデプロイして実行します。Visual Studio は、IIS Express ではリモート Web アプリケーションをホストし、SQL Express では SQL データベースをホストします。また、テスト用の SharePoint サイト上でアドインの一時インストールを行い、そのアドインを直ちに実行します。スタート ページが表示される前に、アドインへのアクセス許可を付与するように求めるメッセージが表示されます。
    
  
3. アドインのスタート ページが開いたら、上部のクロム コントロール上の **[サイトに戻る]** ボタンをクリックします。
    
  
4. 香港店のホーム ページから、 **[サイト コンテンツ]** ページに移動し、 **[出荷予定]** リストを開きます。
    
  
5. アイテムを新しいアイテムのフォーム上で作成します。フォーム上に **[配送済み]** フィールドと **[在庫に追加済み]** フィールドは表示されないことに注意してください。
    
  
6. アイテムを作成したら、もう一度開いて編集します。 **[配送済み]** チェックボックスをオンにして、アイテムを保存します。こうすると、アイテム更新イベントがトリガーされます。アイテムは在庫に追加され、 **[在庫に追加済み]** フィールドの値は **[はい]** に変更されます ( **[在庫に追加済み]** への変更を表示するには、ページを最新の状態に更新することが必要な場合があります)。
    
  
7. ブラウザーの [戻る] ボタンを使用して、チェーン店アドインのスタート ページに戻ってから、 **[在庫の表示]** ボタンを押します。こうすると、 **[配送済み]** としてマークを付けたアイテムがリストに表示されます。
    
  
8. **[出荷予定]** リストに移動し、 *製品名と業者の名前は全く同じで*  、数量は異なる別のアイテムを追加します。
    
  
9. アイテムを作成したら、もう一度開いて編集します。 **[配送済み]** の値を **[はい]** に変更して、アイテムを保存します。
    
  
10. ブラウザーの [戻る] ボタンを使用して、チェーン店アドインのスタート ページに戻ってから、 **[在庫の表示]** ボタンを押します。依然として製品名と業者のアイテムは 1 つだけですが、数量は **[出荷予定]** リストの 2 つのアイテムの合計になります。
    
  
11. デバッグ セッションを終了するには、ブラウザー ウィンドウを閉じるか、Visual Studio でデバッグを停止します。F5 を押すたびに、Visual Studio は前のバージョンのアドインを取り消し、最新のアドインをインストールします。
    
  
12. 他の記事でもこのアドインと Visual Studio ソリューションを処理するので、前回の処理が終了してからしばらく経過したらアドインを取り消すことをお勧めします。 **ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[取り消し]** を選択します。
    
  

## 
<a name="Nextsteps"> </a>

SharePoint サイトにアドインを発行する方法については、「 [SharePoint アドインの展開とインストール: 方法とオプション](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)」をご覧ください。または、これらの MSDN のノードを使用して SharePoint アドイン開発の上級処理に進んでください。
  
    
    

-  [SharePoint アドインの設計](design-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの発行](publish-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインを開発するためのツールと環境](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  

