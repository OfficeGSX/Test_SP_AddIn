---
title: 將 SharePoint 相關寫入作業新增至 [增益集]
ms.prod: SHAREPOINT
ms.assetid: c3d11afd-098e-4cf9-941e-cca6db28d732
---


# 將 SharePoint 相關寫入作業新增至 [增益集]
了解如何將資料寫入 SharePoint 的提供者主控SharePoint Add-in。
這是 fifthin 一系列的基本知識文章的開發提供者主控SharePoint Add-ins。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [包含自訂按鈕中的增益集](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [取得 SharePoint 物件模型的快速概觀](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  

> [!注意事項]
> 如果您有已使用透過這一系列有關提供者主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)在存放庫並開啟 BeforeSharePointWriteOps.sln 檔案。
  
    
    

本文中收到來編碼新增一些資料寫入鏈結存放區SharePoint Add-in的功能。
## 變更 SharePoint 清單項目上的欄值

我們的增益集已從 Hong 特別行政區存放區的 **本機的員工**清單將員工加入公司資料庫的自訂功能區按鈕。但使用者可以手動將 **公司 DB 已新增**欄位的值變更為 [是]，請記得。我們新增程式碼來自動執行的動作。
  
    
    

> [!注意事項]
> 啟動專案的設定中Visual Studio往往每當解決方案重新開啟後還原為預設值。一律 updatefromfile 此系列文章中的範例方案後立即執行這些步驟：> 以滑鼠右鍵按一下頂端的 [ **方案總管中**的 [解決方案] 節點並選取 [ **設定啟動專案**。> 請確定所有三個專案設定以 **啟動**[ **動作**] 欄中。
  
    
    


1. 在 **方案總管**] 中開啟 EmployeeAdder.cs 檔案。
    
  
2. 將下行新增至 **Page_Load**方法之間的 `AddLocalEmployeeToCorpDB`通話和 **Response.Redirect**通話。您會在下一個步驟中建立 `SetLocalEmployeeSyncStatus`方法。
    
  ```cs
  
// Write to SharePoint
SetLocalEmployeeSyncStatus();
  ```

3.  `EmployeeAdder`類別新增下列新方法。請注意下列有關這段程式碼：
    
  - **已新增至公司 DB**欄位的內部名稱是奇怪。內部的欄位名稱不能包含空格，讓使用者建立的欄位時的顯示名稱、 SharePoint 空格會將字串"_x0020_"的每個空間時加以設定的內部名稱。這會"新增至員工 DB"到"Added_x0020_to_x0020_Corporate_x0020_DB"。內部名稱不能超過 32 個字元，因此名稱截斷至剛"Added_x0020_to_x0020_Corporate_x"。
    
  
  - 雖然 **已新增至公司 DB** ] 欄會呼叫 SharePoint 使用者介面中的"是/否 」 欄位，它是真正布林值，因此不"Yes"其值設為 **true**、。
    
  
  - **ListItem**類別之 **Update**方法必須呼叫認可變更至 SharePoint 的內容資料庫。它是一個一般，但不是太萬用，規則會儲存在 SharePoint 資料庫物件的屬性值變更後，您必須呼叫物件的 **Update**方法。
    
  

  ```cs
  
private void SetLocalEmployeeSyncStatus()
{
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        ListItem selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        selectedLocalEmployee["Added_x0020_to_x0020_Corporate_x"] = true;
        selectedLocalEmployee.Update();
        clientContext.ExecuteQuery();
    }
}
  ```


## 要求寫入至主機 web 清單的權限

由於增益集會立即寫入至清單，以及讀取它，我們需要呈報增益集的要求讀取/寫入權限。請遵循下列步驟。
  
    
    

1. 在 **方案總管**] 中開啟 AppManifest.xml 檔案 **ChainStore**專案中。
    
  
2. 開啟 [ **權限**] 索引標籤並在 [ **權限**] 欄位中選取 **寫入**從下拉式清單向下。
    
  
3. 儲存該文件。
    
  

## 增益集執行及測試] 按鈕


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio主控 IIS Express 的遠端 web 應用程式和主控 SQL 資料庫的 SQL Express。它也讓您測試 SharePoint 網站上的 [增益集的暫存安裝，並立即執行增益集。系統提示您授與權限的增益集才能開始] 頁面隨即開啟。
    
  
2. 在 [權限的表單上從清單中選擇 **本機員工**] 和 [ **信任它**。
    
  
3. 在 「 開始 」 頁面增益集開啟時，按一下 [ **返回網站**上方的 chrome 控制項上。
    
  
4. 從網站的首頁上瀏覽至 [ **網站內容 |本機員工**。清單檢視] 頁面隨即開啟。
    
  
5. 如果 **沒有**與清單 **新增至公司 DB** ] 欄中有沒有員工，新增至清單、 員工及 *不檢查 **新增公司的資料庫**] 核取方塊。* 
    
  
6. 在功能區上，開啟 [ **項目**] 索引標籤。在索引標籤的 [ **動作**] 區段中，是自訂按鈕 **新增至公司 DB**。
    
  
7. 選取清單中 **沒有已新增至公司 DB ] 欄中**的員工。
    
  
8. 按 [ **新增至公司 DB** ] 按鈕。 * **必須先選取項目!*** 
    
  
9. [] 頁面上將似乎重新載入因為 EmployeeAdder] 頁面上的 **Page_Load**方法會重新導向給它。員工的 **公司 DB 已新增**欄位的值已變更為 [ **是]**。
    
    > [!注意事項]
      > 項目會防止使用者手動變更值 **新增至公司 DB**讓清單及公司的資料庫不一致的方式？Nothing 沒有可用。您將這一系列更新文章中取得此問題的解決方案。
10. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
11. 以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 在主機網站上建立新的自訂清單

下一個到鏈結存放區增益集就是建立新的項目在清單中，而不只變更現有的項目中的欄位。尤其是新的順序撥打公司層級、 時項目會自動建立提醒本機員工得以預期物料的 SharePoint 清單中。清單會呼叫 **預期出貨**並使用下列步驟所建立。在此系列中更新文章，您將了解如何以程式設計方式新增自訂清單的主機網站，但現在您將手動新增這一個。
  
    
    

1. 從 Fabrikam Hong 特別行政區存放區的首頁上，瀏覽至 [ **網站內容 | 將增益集 |自訂清單**。
    
  
2. 在 [ **新增自訂清單**] 對話方塊中指定預期出貨做為名稱並按下 **建立**。
    
  
3. 在 [ **網站內容**] 頁面開啟的 **預期出貨**清單。
    
  
4. 開啟在功能區上的 [ **清單**] 索引標籤，並再按一下 [ **清單設定**] 按鈕。
    
  
5. 在 [ **清單設定**] 頁面的 [ **欄**] 區段中，按一下 [ **標題**] 直欄。
    
  
6. **編輯資料行**格式為 **資料行名稱**的從產品; 的標題變更並再按一下 [ **確定]**。
    
  
7. 按一下 [ **設定**] 頁面上的 [ **建立欄**]。
    
  
8. 在此系列的前一篇文章，您學會如何建立自訂欄的清單。如 **預期般出貨**] 清單中，新增四個欄，使用下表中的值。保留所有其他設定的預設值。
    

|**資料行名稱**|**類型**|**Required?**|**預設值**|
|:-----|:-----|:-----|:-----|
|供應商 <br/> |**單行文字** <br/> |不需要 <br/> |無 <br/> |
|數量 <br/> |**數字** <br/> |必要 <br/> |1 <br/> |
|抵達 <br/> |**是 / 否** <br/> |不需要 <br/> |否 <br/> |
|新增至 [詳細目錄 <br/> |**是 / 否** <br/> |不需要 <br/> |否 <br/> |
   
9. 在 [清單設定] 頁面上建立欄之後，按一下 [以開啟 [ **網站內容**] 頁面上的 **[網站內容**]。開啟 **預期出貨**清單。
    
  
10. 按一下 [ **新增項目**。項目建立表單外觀完全下列、 包括兩個 asterisks 會指出必要的欄位。：
    
     ![預計出貨清單的項目建立表單。內含 [產品]、[供應商]、[數量]、[已到貨]、[已新增至庫存] 的欄位。 [產品] 和 [數量] 標題旁會顯示星號，[數量] 預設值為一。](images/e552b5c9-8baa-4e53-9295-4d85a79d7734.PNG)
  

  

  
11. 我們不想要手動建立此清單上的 [項目，所以按一下 [ **取消]**。
    
  

## 將項目插入至 SharePoint 清單

現在您可以新增函數來建立 **預期出貨**清單中的項目時 Hong 特別行政區存放區的順序放在公司的層級增益集。
  
    
    

1. 在 **方案總管**] 中開啟 OrderForm.aspx.cs 檔案。
    
  
2. 將針對 **Microsoft.SharePoint.Client** **using**陳述式新增至檔案頂端。
    
  
3. 在 `btnCreateOrder_Click`方法中，新增下列行下方 `CreateOrder`呼叫。您將在下一個步驟中建立 CreateExpectedShipment 方法。
    
  ```cs
  
CreateExpectedShipment(txtBoxSupplier.Text, txtBoxItemName.Text, quantity);
  ```

4.  `OrderForm`類別新增下列方法。請注意下列有關這段程式碼：
    
  - **ListItem**物件不會建立具有建構函式。這是基於效能考量。 **ListItem**物件有許多屬性 (預設值)。如果使用建構函式，則會在 **ExecuteQuery**方法將傳送至伺服器 XML 訊息中包含整個物件。 **ListItemCreationInformation**物件是只包含伺服器需要建立 **ListItem**物件的最小非預設值的輕量型物件。它可能會出現的行以建立 **ListItem**物件，但這一行只會將新增一些 XML 標記訊息會傳送到伺服器的重新叫用。在伺服器上那里建立 **ListItem** 物件。
    
  
  - 就不需要讓沒有呼叫 **ClientContext.Load**方法將移回至用戶端的 **ListItem**物件。
    
  
  - 程式碼不需要明確設定 **Arrived**或 **已新增至庫存**fields 因為它們"No"的預設值是我們想要。
    
  

  ```
  private void CreateExpectedShipment(string supplier, string product, UInt16 quantity)
{
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List expectedShipmentsList = clientContext.Web.Lists.GetByTitle("Expected Shipments");
        ListItemCreationInformation itemCreateInfo = new ListItemCreationInformation();
        ListItem newItem = expectedShipmentsList.AddItem(itemCreateInfo);
        newItem["Title"] = product;
        newItem["Supplier"] = supplier;
        newItem["Quantity"] = quantity;
        newItem.Update();
        clientContext.ExecuteQuery();
    }
}
  ```


## 檢查已刪除的元件

使用 SharePoint 清單的清單中擁有者權限的任何人都可以刪除清單。與如果增益集的清單部署到主機網站的主機網站的網站擁有者可以將其刪除。可能會發生如果擁有者決定不要沒有清單所提供的功能。(它可以還原 SharePoint 資源回收筒中擁有者變更其注意如果。)
  
    
    
 `CreateExpectedShipment`方法取決於 **預期出貨**清單存在。假設網站擁有者決定要刪除的清單。稍後當順序加入與增益集 **，訂購表單**，  `CreateExpectedShipment`會呼叫並將會擲回例外狀況的訊息會指出在 SharePoint 網站上已無 **預期出貨**清單。
  
    
    
您可能會想要檢查 nullity  `expectedShipmentsList`之前，先不要與它的方法。當使用 CSOM 時，您可以 *不*  進行這項檢查以簡單結構如下：
  
    
    
 `if (expectedShipmentsList != null) { ... }`
  
    
    
而您需要使用呼叫 **ConditionalScope**特殊 CSOM 類別。此原因會連線到 CSOM 的批次作業系統、 提及先前在這一系列文章中。(請參閱 [用戶端執行階段以及批次處理](get-a-quick-overview-of-the-sharepoint-object-model.md#CSOMBatching))。 **ConditionalScope**和批次處理系統的進階主題，這快速入門一系列的範圍內的但您應該完成這一系列教學課程之後會看到這些 MSDN 的文件。
  
    
    
檢查清單是否存在的替代方式： 而不是使用 **GetByTitle**方法來取得清單參照，您可以檢查查看的網站 」 清單中的清單"程式碼如下所示是否具有指定名稱的清單。
  
    
    



```cs

var query = from list in clientContext.Web.Lists
             where list.Title == "Expected Shipments" 
             select list; 
IEnumerable<List> matchingLists = clientContext.LoadQuery(query); 
clientContext.ExecuteQuery(); 
if (matchingLists.Count() != 0) 
{ 
    List expectedShipmentsList = matchingLists.Single(); 
    // Do something with the list. 
}
clientContext.ExecuteQuery(); 
```

前述的程式碼能夠讓您以避免 **ConditionalScope**類別的複雜性的優點及我們使用完全這段程式碼其他地方此系列文章中。但有太缺點： 這段程式碼需要額外呼叫的 **ExecuteQuery**察覺取得您想要檢查 **if**陳述式中的值。如果我們使用這項技術 `CreateExpectedShipment`中檢查存在] 清單中，然後方法會有兩個通話的 **ExecuteQuery**每一種提出 HTTP 要求從遠端網頁伺服器至 SharePoint。這些要求屬於最耗時任何 CSOM 方法，所以通常是很好的作法它們降到最低。
  
    
    
我們將會保留 `CreateExpectedShipment`為是，但是在實際執行增益集，您需要考量您的程式碼移至如果刪除它所參考的元件運作的方式。以程式設計方式從資源回收筒還原清單是一個選項，但可會干擾刻意決定要刪除清單的使用者。您也應該考量執行所有可防止例外可能是最佳選擇。從 SharePoint 例外狀況會向使用者發出警示的刪除清單中的已中斷的增益集] 中的組件這是某個項目可能不知道刪除它的人員。使用者可以然後決定是否要從資源回收筒還原清單或無法再運作的增益集功能的一部分而執行。
  
    
    

## 管理網站的要求權限

重新叫用的增益集的要求讀取或寫入權限] 清單中，以範圍 SharePoint 提示使用者信任的增益集和對話方塊包含] 下拉式清單會在使用者所在選取清單中之增益集可以存取時。可以選取 [只有一份清單。但鏈結存放區增益集立即寫入至兩個不同的清單。若要存取多個清單、 增益集必須要求範圍之網頁的權限。請遵循下列步驟：
  
    
    

1. 在 **方案總管**] 中開啟 AppManifest.xml 檔案 **ChainStore**專案中。
    
  
2. 開啟 [ **權限**] 索引標籤並在 [ **範圍**] 欄位中選取 **網頁**從下拉式清單向下。
    
  
3. 在 [ **權限**] 欄位中選取 **寫入**從下拉式清單向下。
    
  
4. 儲存該文件。
    
  

## 增益集執行及測試項目建立


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio主控 IIS Express 的遠端 web 應用程式和主控 SQL 資料庫的 SQL Express。它也讓您測試 SharePoint 網站上的 [增益集的暫存安裝，並立即執行增益集。系統提示您授與權限的增益集才能開始] 頁面隨即開啟。
    
  
2. 在 「 開始 」 頁面增益集開啟時，按一下 [在頁面底部的 [ **訂單**] 連結。
    
  
3. 在表單中輸入一些值並按下 **進行順序**。
    
  
4. 使用瀏覽器的 [上一頁] 按鈕來瀏覽至 [開始] 頁面，然後按一下 [ **返回網站**上的 chrome 控制項頂端。
    
  
5. 從 Hong 特別行政區首頁儲存、 瀏覽至 [ **網站內容**和開啟 **預期出貨**清單。在 [對應至 [順序] 清單現在有項目。下列螢幕擷取畫面是範例。
    
     ![含有單一項目的預計出貨清單。其中 [產品] 和 [供應商] 欄位都有名稱。[數量] 欄位會有數字。[是]/[否] 這兩個欄位皆設為「否」。](images/e4285084-d31e-4e79-a469-ddebbc7dfb18.PNG)
  

  

  
6. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
7. 以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

您將了解如何為 SharePoint 頁面的網頁組件表面遠端訂購表單中下一篇文章 ＜:  [Include an add-in part in the provider-hosted add-in](include-an-add-in-part-in-the-provider-hosted-add-in.md)
  
    
    

