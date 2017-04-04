---
title: 將初次執行邏輯加入至增益集
ms.prod: SHAREPOINT
ms.assetid: 649c06b9-3612-439a-acb3-041e5f5f01f3
---


# 將初次執行邏輯加入至增益集
了解如何將包含"先執行 「 提供者主控SharePoint Add-in中的程式碼。
這是以一系列的基本知識文章的開發提供者主控SharePoint Add-ins八分。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [包含自訂按鈕中的增益集](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [取得 SharePoint 物件模型的快速概觀](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [將 SharePoint 相關寫入作業新增至 [增益集]](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [Include an add-in part in the provider-hosted add-in](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  
-  [處理增益集事件中的增益集](handle-add-in-events-in-the-provider-hosted-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關提供者主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)在存放庫並開啟 BeforeFirstRunLogic.sln 檔案。
  
    
    

本文中您可以新增程式碼來檢查目前的增益集正在執行個體的第一次鏈結存放區SharePoint Add-in中的 [開始] 頁面上。如果是第一次，您的程式碼會將部署 **本機的員工**清單及自訂功能區] 按鈕。
## 建立部署 SharePoint 元件的基本類別


  
    
    

> **注意事項**
> 啟動專案的設定中Visual Studio往往每當解決方案重新開啟後還原為預設值。一律 updatefromfile 此系列文章中的範例方案後立即執行這些步驟：> 以滑鼠右鍵按一下頂端的 [ **方案總管中**的 [解決方案] 節點並選取 [ **設定啟動專案**。> 請確定所有三個專案設定以 **啟動**[ **動作**] 欄中。
  
    
    


1. 在 **方案總管**中 **ChainStoreWeb**專案中的 **公用**資料夾上按一下滑鼠右鍵並選取 **新增 |現有的項目**。
    
  
2. 會開啟 **檔案總管]**中瀏覽至 [解決方案] 資料夾中，[ **ChainStoreWeb** ] 資料夾中，然後再開啟 [ **公用**資料夾。
    
  
3. 選取 SharePointComponentDeployer.cs 並按 [ **新增]**。
    
  
4. 開啟檔案 SharePointComponentDeployer.cs。它具有靜態類別和兩個靜態的方法，取得並設定增益集版本公司資料庫的 **租用戶** 資料表中。我們將不會討論這些方法因為這一系列文章不得教導 ASP.NET 或 SQL Server Azure 程式設計 (英文)。
    
  
5. 將下列 **using**陳述式新增至檔案頂端。
    
  ```
  
using System.Web;
using System.Linq;
using System.Collections.Generic;
using Microsoft.SharePoint.Client;
  ```

6. 在 `SharePointComponentDeployer`類別的最頂端，新增下列兩個靜態欄位。兩者都將增益集開始頁面之 **Page_Load**方法中初始化。您在後續步驟中新增該程式碼。第一個功能變數會保留要對 SharePoint CRUD 作業所需的 **SharePointContext**物件。第二個保存的主機網站上安裝的增益集的版本號碼。此值將最初是不同的安裝處理常式登錄租用戶時加以記錄公司的 **承租人** 表格中的預設值 ( **0000.0000.0000.0000** )。例如，增益集的第一個版本會 **1.0.0.0** 。
    
  ```cs
  
internal static SharePointContext sPContext;
internal static Version localVersion;
  ```

7. 建立要保留的增益集目前記錄公司的 **承租人** 表格中的版本的下列靜態屬性。它會使用兩種方法讓已在 [取得並設定此值的檔案。
    
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

8. 現在建立下列 `IsDeployed`屬性。請注意下列有關這段程式碼：
    
  - 在 「 開始 」 頁面增益集的 **Page_Load**方法會使用此屬性的值來決定增益集執行第一次。 **false**值訊號的增益集未執行之前在目前的主機網站，所以其元件必須部署。
    
  
  - 準則是在 **承租人** 表格中註冊的版本號碼低於實際安裝的版本。第一次的增益集執行，它會降低。您在後續步驟中撰寫程式碼版本 **承租人** 將表格設為相同的版本為實際上已安裝，因此 `IsDeployed`增益集執行時一次將會傳回 **true**和部署邏輯將不會執行一次。
    
  

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

9.  `SharePointComponentDeployer`類別新增下列方法。請注意方法會執行最後一個項目更新公司資料庫 ( **0000.0000.0000.0000** ) 來比對主機網路 ( **1.0.0.0** ) 上的增益集的實際版本中已登錄的租用戶版本。您要完成此方法在後續步驟。
    
  ```cs
  
internal static void DeployChainStoreComponentsToHostWeb(HttpRequest request)
{
    // TODO4: Deployment code goes here.

    RemoteTenantVersion = localVersion;
}
  ```


> **注意事項**
> 您可能會想知道現在為何增益集使用的版本號碼和"小於"測試來判定簡單的答覆 [是/否問號： 為第一次執行增益集？我們剛也可能有 **承租人** 表格已設為"尚未執行"中的安裝處理常式並再變更為 「 已執行一次"由初次執行邏輯之後 SharePoint 元件部署中的簡單字串欄位。> 鏈結存放區 」 增益集、 簡單的測試會運作。不過，它通常是很好的作法使用版本號碼。這是因為實際執行增益集是可能是更新就地未來;也就被更新已安裝後。當該時間而言時，您的增益集邏輯會需要機密以上的兩種可能性not 尚未執行並已-執行一次。例如，假設您想要新增主機網站在升級至 2.0.0.0 1.0.0.0 版其他清單。"更新之後，先執行「 邏輯或中更新事件處理常式，您可以這麼做。無論如何，您部署的邏輯必須部署新的元件，但是它也需要避免嘗試重新部署在舊版的增益集已部署的元件。已部署的元件，1.0.0.0 版但的第一個執行-之後-更新邏輯尚未執行就信號 1.0.0.0 版本號碼。
  
    
    


## 新增的基本啟動邏輯


  
    
    

1. SharePoint 主機 web 需要告知遠端 web 應用程式版本都已安裝的增益集。我們將使用的查詢參數來執行這項作業。 **ChainStore** project 中開啟 AppManifest.xml 檔案。在設計您會看到版面配置區 **{StandardTokens}** **查詢字串**] 方塊中的值。新增字串"&amp; SPAddInVersion = 1.0.0.0"結尾。資訊清單 designer 看起來應該類似如下。 *通知您傳遞查詢字串中的版本號碼必須符合設計工具] 的 [ **版本**] 方塊中的值。*  (如果您曾經更新增益集，其中一個任務是以引發這兩個值，並保持相同)。
    
     ![資訊清單設計工具的 [一般] 索引標籤。[版本] 方塊的值為一零零零。[查詢] 字串方塊寫著  "{StandardTokens}&amp;SPAddInVersion=1.0.0.0"](images/db71c411-10c5-43d8-bb5e-3388d2f6f7bc.PNG)
  

  

  
2. 開啟 CorporateDataViewer.aspx.cs 檔案並新增下列程式碼 **Page_Load**方法，初始化 `spContext`物件的行下方。請注意下列有關這段程式碼：
    
  - 它會從靜態 `SharePointComponentDeployer`類別中設定兩個靜態欄位。它會 **SharePointContext**物件傳遞因為 `SharePointComponentDeployer`中的程式碼會呼叫至 SharePoint]，而且它會使用您新增至設定 `localVersion`屬性的查詢參數。
    
  
  - 它會沒有任何動作 `IsDeployed`則為 true ； 如果也就是說，如果已經有執行 「 先執行 「 邏輯。否則它會呼叫的部署方法並傳遞的 ASP.NET 要求物件。
    
  

  ```cs
  
SharePointComponentDeployer.sPContext = spContext;
SharePointComponentDeployer.localVersion = new Version(Request.QueryString["SPAddInVersion"]);

if (!SharePointComponentDeployer.IsDeployed)
{
    SharePointComponentDeployer.DeployChainStoreComponentsToHostWeb(Request);
}
  ```


## 以程式設計方式部署 SharePoint 清單


  
    
    

1. 在 SharePointComponentDeployer.cs 檔案並將 `TODO4`取代為下行。您在下一個步驟中建立此方法。
    
  ```cs
  
CreateLocalEmployeesList();
  ```

2.  `SharePointComponentDeployer`類別新增下列方法。請注意下列有關這段程式碼：
    
  - 它具有兩個通話的 **ExecuteQuery**。第一個會需要以判斷是否已存在於清單。第二個會建立清單的工作。
    
  
  - 類似 **ClientContext.Load** **ClientContext.LoadQuery**方法是不同之處在於它會傳回而不是將引進實體，例如清單、 用戶端到下列舉查詢的結果。
    
  

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

3.  `TODO5`取代下列程式碼。請注意下列有關這段程式碼：
    
  - **ListCreationInformation**類別會類似於您在舊版的這一系列文章中看到的 **ListItemCreationInformation**類別。它是較適合用來將資訊傳送至 SharePoint 比完整 **List**類別的 web 應用程式的輕量型類別。
    
  
  - 有許多類型的清單範本，例如工作類型的 「 如何 」 清單和行事曆的事件類型。 **本機的員工**清單根據最簡單: 泛型類型。
    
  
  - **ListCreationInformation.Url**屬性到主機網站包含的清單中 *相對*  URL。藉由指定"清單/LocalEmployees"，程式碼會設定清單的完整 URL 為 https:// *{SharePointDomain}*  /hongkong/_layouts/15/start.aspx#/Lists/Local%20Employees。
    
  

  ```cs
  
ListCreationInformation listInfo = new ListCreationInformation();
listInfo.Title = "Local Employees";
listInfo.TemplateType = (int)ListTemplateType.GenericList;
listInfo.Url = "Lists/Local Employees";
List localEmployeesList = clientContext.Web.Lists.Add(listInfo);
  ```

4. 取代下列程式碼從"Title"到"Name""Title"欄位 (欄) 將公用名稱變更為 `TODO6` 。這是什麼您沒有在 [ **清單設定**] 頁面上手動建立清單時。
    
  ```cs
  
Field field = localEmployeesList.Fields.GetByInternalNameOrTitle("Title");
field.Title = "Name";
field.Update();
  ```

5. 您也手動建立名為 **公司 DB 已新增**的欄位。若要執行的以程式設計方式新增以 `TODO7`取代下列程式碼。請注意下列有關這段程式碼：
    
  - 使用 XML blob 指定索引鍵欄位的屬性。這是舊版 SharePoint 的架構的: 網站、 清單、 欄位、 內容類型及大部分其他種類的 SharePoint 元件會定義為 XML。在此例中我們需要指定顯示名稱、 資料類型及欄位的預設值。
    
  
  - 第二個參數會決定欄位是否出現在清單中的預設檢視。我們正在設定為 **true**。
    
  
  - 第三個參數可用來判斷欄位新增至何種內容類型。傳遞給 **DefaultValue**表示只會新增至清單的預設內容類型。
    
  

  ```cs
  
localEmployeesList.Fields.AddFieldAsXml("<Field DisplayName='Added to Corporate DB'"
                                         +"Type='Boolean'>"
                                         + "<Default>FALSE</Default></Field>",
                                         true,
                                         AddFieldOptions.DefaultValue);
  ```

6. 重新叫用 **公司 DB 已新增**為 **否**(也就是，false) 根據預設，但在增益集自訂功能區按鈕將其設定為 **Yes**之後員工加入公司的資料庫。此 system 只能用於最佳如果使用者不能手動變更欄位的值。若要確保他們不，隱藏欄位建立與編輯 **本機的員工**清單上的項目表單中。其執行方法是新增兩個更多的屬性到第一個參數，如下列所示。
    
  ```cs
  
localEmployeesList.Fields.AddFieldAsXml("<Field DisplayName='Added to Corporate DB'"
                                         + " Type='Boolean'"  
                                         + " ShowInEditForm='FALSE' "
                                         + " ShowInNewForm='FALSE'>"
                                         + "<Default>FALSE</Default></Field>",
                                         true,
                                         AddFieldOptions.DefaultValue);
  ```


    整個 `CreateLocalEmployeesList`現在看起來應類似如下。
    


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


## 從專案中暫時移除自訂按鈕

我們會在後續文章中討論的技術考量，我們建立自訂按鈕不能被安裝不需要修改其正在置於以程式設計方式部署清單的功能區時。我們將會移除它暫時專案，使我們可以測試我們初次執行邏輯。我們將將其移回下一篇文章。
  
    
    
在 **方案總管**]、 [在 **ChainStore**專案中，[ **AddEmployeeToCorpDB** ] 節點上按一下滑鼠右鍵並選取 **排除專案**。
  
    
    

## 要求的權限管理主機網路上的清單

由於增益集是現在將清單新增至主機網頁，而不只項目至現有的清單中，我們需要呈報增益集的要求寫入至管理的權限。請遵循下列步驟。
  
    
    

1. 在 **方案總管**] 中開啟 AppManifest.xml 檔案 **ChainStore**專案中。
    
  
2. 開啟 [ **權限**] 索引標籤與在網頁，但在 [ **權限**] 欄位保留 **範圍**值、 選取 [從下拉式清單的 [ **管理**] 下。
    
  
3. 儲存該文件。
    
  

## 增益集執行及測試的"第一次執行"邏輯


  
    
    

1. 開啟 Hong 特別行政區存放區的網站的 [ **網站內容**] 頁面上 *並移除 **本機的員工**清單!* 
    
  
2. 使用 F5 鍵以部署及執行您的增益集Visual Studio主控 IIS Express 的遠端 web 應用程式和主控 SQL 資料庫的 SQL Express。它也讓您測試 SharePoint 網站上的 [增益集的暫存安裝，並立即執行增益集。系統提示您授與權限之前其開始] 頁面隨即開啟的增益集。
    
  
3. 增益集開始頁面會隨即開啟，請選取 **回站台**連結上的 chrome 控制項頂端。
    
  
4. 瀏覽至 [ **網站內容**] 頁面上。因為您初次執行的邏輯將其新增有無 **本機的員工**清單。
    
    > **注意事項**
      > 若清單並非有或具有初次執行的程式碼不執行其他表示，則可能的 **承租人**表格不被 csdeviceupdateconfiguration 空的狀態時按 F5。最常見的原因是 **ChainCorporateDB**專案已不再設定為Visual Studio啟動專案。請參閱本文針對如何修正此問題頂端附近附註。也請確定您已設定要重新 [設定Visual Studio重建每個偵錯工作階段的公司資料庫](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md#Rebuild)中所述的資料庫。
5. 開啟清單並新增項目。請注意新項目表單上 **新增至公司 DB**欄位是不再存在，因此無法手動設定它。這是的編輯項目表單，則為 true。
    
     ![適用於當地員工清單的新項目表單。表單上不再顯示「已新增至公司 DB」欄位。僅顯示「確定」與「取消」的名稱欄位與按鈕。](images/3fdc6752-4184-4928-9423-0bc7c0206c62.PNG)
  

  

  
6. 若要瀏覽至 [增益集開始頁面使用瀏覽器的 [上一頁] 按鈕。
    
  
7. 按齒輪圖示上的 chrome 控制項上方，然後選取 [ **帳戶設定**。
    
  
8. 在 [ **帳戶**] 頁面上按下 [ **顯示增益集版本**] 按鈕。版本，因此顯示為 **1.0.0.0** 初次執行邏輯變更它。
    
     ![[帳戶設定] 頁面與 1.0.0.0 的版本號碼。](images/4c6d82a7-7c40-4190-b7e3-1337275e1e60.PNG)
  

  

  
9. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
10. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

下一篇文章 ＜ 在您將了如何取得自訂按鈕 **本機員工**功能區回復成增益集現在要以程式設計方式部署清單:  [以程式設計方式部署中的增益集的自訂按鈕](programmatically-deploy-a-custom-button-in-the-provider-hosted-add-in.md)
  
    
    

