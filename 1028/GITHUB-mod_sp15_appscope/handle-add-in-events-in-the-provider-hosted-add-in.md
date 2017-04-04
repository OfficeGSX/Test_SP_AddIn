---
title: 處理增益集事件中的增益集
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# 處理增益集事件中的增益集
了解如何自訂安裝的提供者主控SharePoint Add-in。
這是以一系列的基本知識文章的開發提供者主控SharePoint Add-ins二十七。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [包含自訂按鈕中的增益集](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [取得 SharePoint 物件模型的快速概觀](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [將 SharePoint 相關寫入作業新增至 [增益集]](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [Include an add-in part in the provider-hosted add-in](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關提供者主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)在存放庫並開啟 BeforeAdd inEventHandlers.sln 檔案。
  
    
    

在本文中，我們將自訂一種 SharePoint，稱為 「增益集事件中的事件的處理。特別是，我們將建立的增益集的安裝與解除安裝事件處理常式。也有清單及清單項目事件，可以取得自訂處理。您將了解這些在此系列中更新文章。觸發這些事件的所有的 sharepoint，但您處理每個事件的自訂程式碼是在遠端 web 應用程式中。您設定 SharePoint，以呼叫您的自訂處理常式登錄與 SharePoint 事件處理常式的 URL。
## 若要以程式設計方式部署 SharePoint 元件的兩個位置

我們想我們鏈結存放區增益集建立及部署 **本機員工**及 **預期出貨**自動列出。增益集可以部署 SharePoint 元件，例如自訂清單中，任何時候。但當增益集取決於特定元件，例如自訂清單，然後元件真正應該是部署 *之前*  使用者開始使用 [增益集。這類環元件，有兩個位置的自訂部署邏輯可以移出：
  
    
    

- 在 [增益集安裝事件處理常式。
    
  
- 執行第一次的"第一次執行"邏輯中的增益集可啟動 SharePoint 中。
    
  
決定最適合於指定的增益集是進階的主題。在本文中，我們可以只提及幾個點的比較：
  
    
    

- 自訂安裝處理常式已完成 30 秒。有可能需要長初次執行邏輯不受限制。
    
  
- 如果發生任何錯誤增益集在安裝期間，SharePoint 會回復它已完成安裝的一部分的所有項目。自訂安裝處理常式會執行 *後*  SharePoint 已完成每個項目及其移至執行安裝的增益集，讓自訂處理常式可參與此系統。例如，如果您的自訂邏輯會擲回例外狀況，您可以分清回復整個增益集安裝的 SharePoint。如果以免出現任何狀況中初次執行的自訂邏輯，但增益集保留原狀安裝和可能就無法正常運作。
    
  
- 如果您將回復增益集安裝有 SharePoint 不會放棄。它會立即再試一次安裝。它會最多 4 個嘗試 (30 秒的時間限制適用於每一次嘗試。)它重試，每次在自訂安裝處理常式會執行一次， *從頭開始*  。如果安裝，假設清單，再回復、 受管理的處理常式它將會嘗試在重試一次安裝相同的清單。若要防止發生這種情況，安裝處理常式中的程式碼有要寫入，讓它不會採取任何動作 (例如，部署元件) 除非如果第一個檢查，請參閱是否已執行該巨集指令。這會使安裝處理常式的邏輯比初次執行邏輯更複雜自初次執行邏輯將不會重試 (除非您特別編寫程式碼來這麼做)。而且，檢查是否元件已經部署時通常需要耗時通話透過網際網路上的遠端處理常式至 SharePoint。然後第二個通話部署所需實際元件 (如果它會開啟不適用於已部署)。
    
  
鏈結存放區 」 增益集，我們將合併這些策略。在本文中，您將建立將做為公司資料庫中的租用戶登錄的主機網站並再設定指定是否增益集有已執行尚未主機網路上的訊號安裝處理常式。在此系列中更新文章，您將會放入增益集開始] 頁面上的 **Page_Load**方法中的第一個隨選即用邏輯。此邏輯會部署在兩個自訂清單及執行其他一些太。
  
    
    

## 設定偵錯的事件接收器的解決方案
<a name="RERDebug"> </a>

事件接收器的偵錯需要使用 Azure 服務匯流排。遵循在 [偵錯及疑難排解中的 SharePoint 增益集的遠端事件接收器](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)的指示。由於您使用SharePoint Online網站做為測試網站，可確定 」 執行出遠端測試網站的指示。這一系列的其餘部分將會假設您已設定成功偵錯。
  
    
    

## 建立安裝處理常式
<a name="RERDebug"> </a>


  
    
    

> **注意事項**
> 啟動專案的設定中Visual Studio往往每當解決方案重新開啟後還原為預設值。一律 updatefromfile 此系列文章中的範例方案後立即執行這些步驟：> 以滑鼠右鍵按一下頂端的 [ **方案總管中**的 [解決方案] 節點並選取 [ **設定啟動專案**。> 請確定所有三個專案設定以 **啟動**[ **動作**] 欄中。
  
    
    


1. 在 **方案總管**] 中選取的 **ChainStore**專案，因此及其屬性都會出現在 [ **內容**] 窗格的Visual Studio。
    
  
2. 將 **控點增益集安裝**的值設定為 **True**。(它可能仍會呼叫 **處理安裝應用程式**)。這會執行兩件事：
    
  - 在 **ChainStoreWeb**專案 (非 **ChainStore**專案) 中建立一個名為 **服務**資料夾和兩個檔案新增至其: AppEventReceiver.svc 檔案以及其程式碼後置 AppEventReceiver.svc.cs 檔案。(的檔案名稱開頭字串"App"，因為增益集用來呼叫"apps"。 *不重新命名這些檔案。*  Office Developer Tools for Visual Studio假設檔案將會保留這些名稱。)
    
  
  - 處理常式 URL 已登錄的增益集資訊清單中。資訊清單的設計工具中看不到此部分的資訊清單。若要查看它，以滑鼠右鍵按一下 AppManifest.xml 檔案並選取 **檢視程式碼**。有新的子系如下所示的 **內容**項目。此標記會告知 SharePoint 完成執行所有自己安裝增益集與相關的工作時呼叫此項服務的 **ProcessEvent**方法。自訂的處理常式會執行安裝的一部分的最後項目。字串 `~remoteAppUrl`是Office Developer Tools for Visual Studio會以服務主應用程式 URL 取代預留位置。當您偵錯時，它會為 Azure 服務匯流排 URL。當您建立的套件部署至實際執行環境時，它會是實際執行 URL。
    
  ```XML
  
<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
  ```

3. 開啟 AppEventReceiver.svc.cs 檔案。
    
  
4. 您會看見Office Developer Tools for Visual Studio已建立範例實作的 **ProcessEvent**方法。此方法的所有實作都首先初始化 **SPRemoteEventResult**物件和其所有結束於 sharepoint 傳回該物件。除此之外，此物件會告知 SharePoint 它應該回復事件因為自訂處理邏輯失敗。工具可能也有包含 **using**封鎖此建立 **ClientContext**物件的方法。鏈結存放區增益集的自訂處理常式無法移至回撥入 SharePoint，所以刪除 [此區塊。此方法現在看起來應類似如下。
    
  ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
  ```

5. 事件接收器可能是由三種可能增益集事件的任何呼叫，所以新增下列 **switch**結構來建立並傳回 `result`物件的那幾行傳來的 **ProcessEvent**方法。事件名稱備增益集用來呼叫"apps"字串的"App"它們。
    
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

6. 我們安裝邏輯將要呼叫 SQL 預存程序為遠端 web 應用程式中的租用戶登錄的 Hong 特別行政區存放區。它是非常重要的是如果此程序失敗，處理常式信號 SharePoint 回復安裝的增益集，因此區塊中新增下列 **try/catch** `TODO2`取代。請注意下列有關這段程式碼：
    
  - 您會在後續步驟中建立 `tenantName`物件與 `CreateTenant`方法。
    
  
  - **SPRemoteEventResult**物件的 **Status**屬性可以有三種可能的值： **Continue** (預設值)、 **CancelNoError**，以及 **CancelWithError**。後面兩個任一告訴 SharePoint 回復事件。
    
  

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

7. 主機網頁 URL，也就是樣本的租用戶鑑別器，屬於 SharePoint 會傳遞至受話方 **SPRemoteEventProperties**參數中的資訊。將下行新增至 **SPRemoteEventResult**物件的起始行下方所列的 **ProcessEvent**方法。
    
  ```cs
  
string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
  ```

8. 現在我們的程式碼有一點突變 **AppEventProperties.HostWebFullUrl**屬性的處理。SharePoint 中其他大部分的內容，包括結束結尾處的主機網頁 URL，因此我們的範例程式碼的邏輯假設此字元是出現的"/"字元。但 SharePoint 會新增此字元結尾處的 **HostWebFullUrl**值如果，且只有、 主機 web 是網站集合的根網站。由於我們 Hong 特別行政區網站是網站集合中的子網站，我們需要新增整個此範例會使用此字元以確保相同租用戶名稱字串。新增下列程式碼 `tenantName`物件的初始設定。
    
  ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
  ```

9. 將下列 **using**陳述式新增至檔案頂端。
    
  ```
  
using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
  ```

10.  `AppEventReceiver`類別新增下列方法。我們不討論這詳細因為這一系列文章的目的是要教導 SharePoint 增益集程式設計 (英文)、 不 SQL Server Azure 程式設計 (英文)。
    
    此方法會移至稱為 **租用戶** 的資料庫資料表中建立的資料列。[ **名稱** ] 欄中，除了表格也有 **版本** ] 欄預設值設為0000.0000.0000.0000。在此系列中更新文章，您將建立會查看此值以決定增益集已安裝在主機網頁上的第一個隨選即用邏輯。如果0000.0000.0000.0000版本，您的程式碼會部署 **本機員工**和 **預期出貨**清單和則引發的版本號碼。
    


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


## 建立解除安裝處理常式
<a name="RERDebug"> </a>

它會處理 uninstalling 事件每當您用來處理已安裝的事件通常是很好的作法。基本概念是解除安裝的處理常式會刪除或回收已安裝的處理常式部署的事項。有，不過，許多例外狀況，讓您真的需要瞭解增益集的使用情況。例如，已部署與增益集及填入 [增益集清單可能仍有即使增益集本身解除安裝後在此情況下有用要解除安裝的清單中的 ＜ uninstalling 的事件處理常式的值。
  
    
    
不執行解除安裝事件，如您所預期，當使用者從 **網站內容**] 頁面上移除增益集。這麼只會移動增益集至網站資源回收筒。使用者無法還原，但是還原不會不重新執行安裝偶數的處理常式中，因此您想想要部署已安裝的事件處理常式仍存在於如果還原增益集的任何項目。SharePoint 元件可從資源回收筒移動到第二階段資源回收筒。它是僅限增益集從刪除時 uninstalling 事件發生的情況，第二階段與當使用者執行動作的、 增益集是 unrestorable 繼續，因此我們想要在那個時候移除從公司資料庫 Hong 特別行政區存放區的租用。
  
    
    

1. **解除安裝控點增益集**的值設 **為 True**。(它可能仍會呼叫 **處理應用程式解除安裝**)。如此即會註冊處理常式 AppManifest.xml 檔案中就如同您稍早註冊安裝處理常式。如果您查看檔案，您會看見他們具有完全相同的 URL。Office Developer Tools for Visual Studio假設您已使用相同的 *.svc 檔案。我們執行的動作，在此範例中，及是標準的作法。
    
  
2. 以 `TODO3`取代下列程式碼檔案中新增 AppEventReceiver.svc.cs。請注意下列有關這段程式碼：
    
  - 下一個步驟中新增 `DeleteTenant`方法。
    
  
  - 回復解除安裝增益集會保留在第二階段資源回收筒，從中它可能仍會還原。
    
  

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

3.  `AppEventReceiver`類別新增下列方法。
    
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


> **注意事項**
> 在舊版這一系列的文章，您設定每次您按 F5 重建公司資料庫的專案。這會清空 **Tenants**表格。
  
    
    


## 增益集執行及測試安裝處理常式
<a name="RERDebug"> </a>


1. 使用 F5 鍵以部署及執行您的增益集Visual Studio主控 IIS Express 的遠端 web 應用程式和主控 SQL 資料庫的 SQL Express。它也讓您測試 SharePoint 網站上的 [增益集的暫存安裝，執行安裝的事件處理常式，並立即執行增益集。系統提示您授與權限的增益集才能開始] 頁面隨即開啟。
    
  
2. 在 「 開始 」 頁面增益集開啟時，在上方，然後選取 [ **帳戶設定**的 chrome 控制項上按齒輪圖示。
    
  
3. 在 [ **帳戶**] 頁面上按下 [ **顯示增益集版本**] 按鈕。版本，因此顯示為0000.0000.0000.0000。
    
     ![內含標題「帳戶設定」的 [帳戶] 頁面。一個名稱為「顯示增益集版本」的按鈕，以及其下方的一行文字寫著「註冊版本：零零零零點零零零零點零零零零點零零零零」。](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)
  

  

  
4. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
5. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

數列下一篇文章 ＜ 中您要新增初次執行邏輯至增益集以程式設計方式將部署 **本機的員工**清單及自訂功能區按鈕:  [將初次執行邏輯加入至增益集](add-first-run-logic-to-the-provider-hosted-add-in.md)
  
    
    

