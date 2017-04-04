---
title: 包含自訂按鈕中的增益集
ms.prod: SHAREPOINT
ms.assetid: 58932389-0100-47ee-9d33-1b4321d3f462
---


# 包含自訂按鈕中的增益集
了解如何將包含自訂功能區按鈕的提供者主控SharePoint Add-in。
這是一系列的開發提供者主控SharePoint Add-ins基本知識文章中的第三個。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關提供者主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)在存放庫並開啟 BeforeRibbonButton.sln 檔案。
  
    
    

SharePoint Add-in可以包含自訂動作，這是 SharePoint 字詞的自訂功能表項目或功能區按鈕。本文中您將了解如何建立自訂按鈕與 SharePoint 清單同步處理將遠端資料庫。
## 在主機網站上建立的自訂清單

要記錄的本機存放區員工特定清單的功能區上分組自訂按鈕。在此系列中更新文章，您將了解如何以程式設計方式新增自訂清單的主機網站，但現在您將會新增一個手動。
  
    
    

1. 從 Fabrikam Hong 特別行政區存放區的首頁上，瀏覽至 [ **網站內容 | 將增益集 |自訂清單**。
    
  
2. 在 [ **新增自訂清單**] 對話方塊中指定本機員工做為名稱並按下 **建立**。
    
  
3. 在 [ **網站內容**] 頁面開啟 **本機的員工**清單。
    
  
4. 開啟在功能區上的 [ **清單**] 索引標籤，並再按一下 [ **清單設定**] 按鈕。
    
  
5. 在 [ **清單設定**] 頁面的 [ **欄**] 區段中，按一下 [ **標題**] 直欄。
    
  
6. 在 **[編輯] 欄**的表單中變更 **資料行名稱**從標題為名稱；並再按一下 [ **確定]**。
    
  
7. 按一下 [ **設定**] 頁面上的 [ **建立欄**]。
    
  
8. **建立欄**的表單中執行下列動作：
    
1. 輸入已新增至公司 DB做為 **資料行名稱**。
    
  
2. 若要設定的類型 **是/否 (] 核取方塊)**。
    
  
3. 設定的 **預設值**為 [ **否]**。
    
  
4. 按 **[確定]**。您會前往回 [ **設定**] 頁面。
    
  
9. 按一下 [ **網站內容]**以開啟 [ **網站內容**] 頁面。新清單磚有。開啟它。
    
  
10. 按一下 [ **新增項目**及建立項目表單上輸入名稱，但不要 *檢查 **新增公司的資料庫*** 。然後按一下 [ **儲存**]。[] 清單中看起來應該類似如下：
    
     ![具有單一項目的當地員工清單。名稱為 Brayden Sawtell。「已新增至公司 DB」欄位的值為「否」。](images/a3371859-e42f-49ea-8f17-48d8a248b075.PNG)
  

  

  

## 新增自訂按鈕

此區段中，您可以包含中增益集將會部署至清單的功能區按鈕的標記。當使用者醒目提示的員工清單，然後按一下 [] 按鈕時，該員工的名稱會新增至公司的資料庫與員工的 [ **新增至公司 DB** ] 欄位會交換從 [否] 為 [是]。
  
    
    

1.  *如果開啟Visual Studio ，則必須將其關閉*  並重新開啟鏈結儲存解決方案使Visual Studio可以探索新的清單。(系統管理員身分執行Visual Studio )。
    
    > **注意事項**
      > 啟動專案的設定中Visual Studio往往每當解決方案重新開啟後還原為預設值。一律 updatefromfile 此系列文章中的範例方案後立即執行這些步驟：> 以滑鼠右鍵按一下頂端的 [ **方案總管中**的 [解決方案] 節點並選取 [ **設定啟動專案**。> 請確定所有三個專案設定以 **啟動**[ **動作**] 欄中。
2. 以滑鼠右鍵按一下 [ **方案總管**中的 **ChainStore**專案並選擇 **新增 |新項目**。
    
  
3. 在 [ **新增項目**] 對話方塊中選取 **功能區自訂動作**、 為其指定名稱AddEmployeeToCorpDB、，然後按一下 [ **新增]**。
    
  
4. 會開啟對話方塊詢問三個問題。授與下列的答案：
    

|**問題**|**授與此回覆：**|
|:-----|:-----|
|**您要在其中公開的自訂動作？** <br/> |主機 Web <br/> |
|**其中的自訂動作的範圍侷限於？** <br/> |清單執行個體 <br/> |
|**哪個特定項目的自訂動作的範圍侷限於？** <br/> |本機名員工 <br/> |
   
5. 按一下 [ **下一步**，並取得三個多個問題：
    

|**問題**|**授與此回覆：**|
|:-----|:-----|
|**控制項位於何處？** <br/> |Ribbon.ListItem.Actions <br/> |
|**什麼是 [按鈕] 控制項的標籤文字？** <br/> |新增至公司 DB <br/> |
|**[按鈕] 控制項會其中巡覽至？** <br/> |ChainStoreWeb\\Pages\\EmployeeAdder.aspx(這是其程式碼後置移至該員工將新增至資料庫] 頁面上)。 <br/> |
   
6. 按一下 [完成]。
    
    定義自訂動作的 elements.xml 檔案會新增至專案並開啟。大致上，您可以將此檔案視為黑色] 方塊中，並不需要進行任何變更之前的這一系列更新文章中。現在請注意僅下列事項：
    
  - **Location**元素之屬性的 **CommandUIDefinition**具有值 `Ribbon.ListItem.Actions.Controls_children`。第二部分 `ListItem`，這會識別出] 按鈕會被放置 (但，可能不] 索引標籤的確切的顯示名稱) 的功能區上索引標籤和第三組件 `Actions`，是要放置按鈕的功能區] 區段的名稱。
    
  
  - **CommandAction**元素之屬性的 **CommandUIHandler**開頭為預留位置 `~remoteAppUrl`。這將會取代遠端 web 應用程式的 URL 部署] 按鈕時。
    
  
  - 幾個查詢參數已新增至使用中括號"{}"的版面配置區值 **CommandAction**值。這些預留位置以外都已解決在執行階段。請注意其中一個清單項目之前她按 [自訂] 按鈕的功能區上所選取之使用者的識別碼。
    
  
7. 在 **ChainStoreWeb**專案中，開啟 **Pages/EmployeeAdder.aspx**檔案。請注意它不會有任何使用者介面。增益集將要為 web 服務的一種可以使用此頁面。這可能是由於 ASP.NET **System.Web.UI.Page**類別會實作 **System.Web.IHttpHandler**而且 ** Page_Load**事件，要求] 頁面上時自動執行。
    
  
8. 開啟的程式碼後置檔案 **Pages/EmployeeAdder.aspx.cs**。遠端資料庫 `AddLocalEmployeeToCorpDB`、 將該員工的方法是已經存在。它會使用 **SharePointContext**物件取得增益集使用為其租用戶鑑別器的主機網頁的 URL。所以的最先 **Page_Load**方法只需要為初始化此物件。物件是建立及快取的工作階段在 「 開始 」 頁面增益集載入時，可以新增下列程式碼 **Page_Load**方法。( **SharePointContext**物件建立增益集解決方案時，會產生Office Developer Tools for Visual Studio SharePointContext.cs 檔案中定義)。
    
  ```cs
  
spContext = Session["SPContext"] as SharePointContext;
  ```

9.  `AddLocalEmployeeToCorpDB`方法需要該員工的名稱做為參數，所以將下行新增至 **Page_Load**方法。您將會在後續步驟中建立 `GetLocalEmployeeName`方法。
    
  ```cs
  // Read from SharePoint
string employeeName = GetLocalEmployeeName();
  ```

10. 下面這一行中，新增 `AddLocalEmployeeToCorpDB`方法的呼叫。
    
  ```cs
  
// Write to remote database
AddLocalEmployeeToCorpDB(employeeName);
  ```

11. 新增 **using**陳述式的命名空間 `Microsoft.SharePoint.Client`檔案。( Office Developer Tools for Visual Studio包括 Microsoft.SharePoint.Client 組件中 **ChainStoreWeb**專案時所建立)。
    
  
12. 現在 `EmployeeAdder`類別新增下列方法。SharePoint.NET 用戶端物件模型 (CSOM) 所記載的 MSDN 上的其他地方的詳細資訊，我們建議您探索完成與此系列文章時。本文中，記下 **ListItem**類別代表 SharePoint 清單中的項目並可以使用"indexer"語法參照的項目中的欄位值。此外，請注意程式碼指的是欄位為"Title"即使您變更的欄位名稱為"Name"。這是因為欄位會一律在參照程式碼依其 *內部*  名稱不是其顯示名稱。欄位的內部名稱時的欄位建立且可以永遠不會變更設定。您完成 `TODO1`在後續步驟。
    
  ```cs
  
private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    // TODO1: Initialize the localEmployee object by getting  
    // the item from SharePoint.
 
    return localEmployee["Title"].ToString();
}
  ```

13. 它可以擷取 SharePoint 之前我們的程式碼將需要的清單項目 ID。下列宣告的 `EmployeeAdder`類別新增行下方 `spContext`物件的宣告。
    
  ```cs
  
private int listItemID;
  ```

14. 現在請將下列方法新增至 `EmployeeAdder`類別，取得清單項目識別碼從查詢參數。
    
  ```cs
  private int GetListItemIDFromQueryParameter()
{
    int result;
    Int32.TryParse(Request.QueryString["SPListItemId"], out result);
    return result;
}
  ```

15. 若要初始化 `listItemID`變數，請將下行新增初始化 `spContext`變數的行下方 **Page_Load**方法。
    
  ```cs
  
listItemID = GetListItemIDFromQueryParameter();
  ```

16. 在 `GetLocalEmployeeName`中，以 `TODO1`取代下列程式碼。時間正在剛視為這段程式碼黑色方塊時我們專注於快速自訂按鈕工作。我們將深入瞭解在這一系列是有關 SharePoint 用戶端物件模型的所有後續文章中的這段程式碼。
    
  ```cs
  using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
    localEmployee = localEmployeesList.GetItemById(listItemID);
    clientContext.Load(localEmployee);
    clientContext.ExecuteQuery();
}

  ```


    整個方法現在看起來應類似如下。
    


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

17. EmployeeAdder] 頁面上應實際上並不是呈現，所以將以下內容新增為 **Page_Load**方法中的最後一行。這會將瀏覽器重新導向回 **本機的員工**清單的清單檢視頁面。
    
  ```cs
  
// Go back to the Local Employees page
Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);

  ```


    整個 **Page_Load**方法現在看起來應類似如下。
    


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


## 要求權限以讀取 「 主機 web 清單

視為已，SharePoint 會提示您安裝時授與主機 web 增益集權限。您有已重新安裝的增益集每次按 F5。到目前為止，增益集有只需要最少的權限，但 `GetLocalEmployeeName`方法需要權限以讀取主機網站的清單。增益集使用其增益集資訊清單告訴 SharePoint 需要哪些權限。請遵循下列步驟。
  
    
    

1. 在 **方案總管**] 中開啟 AppManifest.xml 檔案 **ChainStore**專案中。(該檔案被呼叫 Vappmanifest 因為增益集用來呼叫"apps")。資訊清單的設計工具會隨即開啟。
    
  
2. 開啟 [ **權限**] 索引標籤和 [ **範圍**] 欄中，按一下 [空白儲存格然後從下拉式清單選取 **清單中**向下。
    
  
3. 在 [ **權限**] 欄位中，從下拉式選取 **讀取**向下。
    
  
4. **屬性**欄位請保留空白並儲存檔案。[ **權限**] 索引標籤看起來應該類似如下：
    
     ![增益集資訊清單設計工具的 [權限] 索引標籤顯示了清單範圍及要被讀取的權限。](images/8dd2a25f-103a-42af-aa88-c8ec796315db.PNG)
  

  

  

## 增益集執行及測試] 按鈕


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio主控 IIS Express 的遠端 web 應用程式和主控 SQL 資料庫的 SQL Express。它也讓您測試 SharePoint 網站上的 [增益集的暫存安裝，並立即執行增益集。系統提示您授與權限的增益集才能開始] 頁面隨即開啟。這次提示具有下拉式方塊您選取 [應用程式需要閱讀如下列螢幕擷取畫面所示的清單。
    
     ![包含「當地員工」清單的 SharePoint 增益集權限提示，該清單是從標示為「讓其讀取清單中的項目」的下拉式清單中所選取的](images/84e8b42c-4800-4947-acbd-21c6f096f4ea.PNG)
  

  

  
2. 從清單中選擇 **本機員工**] 和 [ **信任它**。
    
  
3. 在 「 開始 」 頁面增益集開啟時，按一下 [ **返回網站**上方的 chrome 控制項上。
    
  
4. 從網站的首頁上瀏覽至 [ **網站內容 |本機員工**。清單檢視] 頁面隨即開啟。
    
  
5. 新增至清單的幾個員工。 *不檢查 **新增公司的資料庫**] 核取方塊。* 
    
  
6. 在功能區上，開啟 [ **項目**] 索引標籤。在索引標籤的 [ **動作**] 區段中，是自訂按鈕 **新增至公司 DB**。
    
  
7. 選取清單項目。頁面與功能區看起來應該類似如下：
    
     ![當地員工清單。一個項目已反白顯示。在其上方是功能區，且動作區段中有名為「新增至公司 DB」的按鈕。](images/797a5ceb-7291-4b62-8075-2bb6a1b8e8a1.PNG)
  

  

  
8. 按 [ **新增至公司 DB** ] 按鈕。 * **必須先選取項目!*** 
    
  
9. [] 頁面上將似乎重新載入因為 EmployeeAdder] 頁面上的 **Page_Load**方法會重新導向給它。
    
  
10. 使用瀏覽器的 [上一頁] 按鈕兩次回到增益集起始頁面。
    
  
11. 按一下 [ **顯示員工**及的員工清單會填入您新增員工。看起來應該類似如下：
    
     ![在增益集開始頁面上所列的公司員工清單，顯示了在先前步驟中被選取的相同員工。](images/4a300a4e-f479-4f63-b536-6315c5d9ba4d.PNG)
  

  

  
12. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
13. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在下一篇文章 ＜ 我們將看從編碼的 SharePoint 用戶端物件模型概觀簡短符號:  [取得 SharePoint 物件模型的快速概觀](get-a-quick-overview-of-the-sharepoint-object-model.md)。
  
    
    

