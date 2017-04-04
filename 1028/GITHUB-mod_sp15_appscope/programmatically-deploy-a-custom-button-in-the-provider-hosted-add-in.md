---
title: 以程式設計方式部署中的增益集的自訂按鈕
ms.prod: SHAREPOINT
ms.assetid: 4d75d113-8dc8-4026-a297-c47b6d4d7008
---


# 以程式設計方式部署中的增益集的自訂按鈕
了解如何以程式設計方式在同一個提供者主控SharePoint Add-in自訂清單中註冊的自訂功能區按鈕。
這是以一系列的基本知識文章的開發提供者主控SharePoint Add-ins二十九。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [包含自訂按鈕中的增益集](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [取得 SharePoint 物件模型的快速概觀](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [將 SharePoint 相關寫入作業新增至 [增益集]](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [Include an add-in part in the provider-hosted add-in](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  
-  [處理增益集事件中的增益集](handle-add-in-events-in-the-provider-hosted-add-in.md)
    
  
-  [將初次執行邏輯加入至增益集](add-first-run-logic-to-the-provider-hosted-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關提供者主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)在存放庫並開啟 BeforeProgrammaticButton.sln 檔案。
  
    
    

本文中您將了解如何將包含自訂功能區按鈕的SharePoint Add-in清單中的功能區取得] 按鈕時要以程式設計方式部署非常相同增益集本身。
## 重新將自訂按鈕新增至專案


> **注意事項**
> 啟動專案的設定中Visual Studio往往每當解決方案重新開啟後還原為預設值。一律 updatefromfile 此系列文章中的範例方案後立即執行這些步驟：> 以滑鼠右鍵按一下頂端的 [ **方案總管中**的 [解決方案] 節點並選取 [ **設定啟動專案**。> 請確定所有三個專案設定以 **啟動**[ **動作**] 欄中。
  
    
    

在前一篇文章您從專案中移除自訂的 **AddEmployeeToCorpDB**功能區按鈕。將其新增回使用下列步驟執行。
  
    
    

1. 在 **方案總管**] 中按下小型上方的 [ **方案總管]**工具列上的 [ **顯示所有檔案**] 按鈕。
    
     ![在 [顯示所有檔案] 按鈕周圍繪製方塊的 [方案總管] 工具列。](images/f6b035f5-1aa7-452a-8f59-9dd44b062d06.PNG)
  

  

  
2. 在 **ChainStore**專案中，以滑鼠右鍵按一下 **AddEmployeeToCorpDB**並選取 [ **專案中的加入**。
    
  
3. 一次按下 [ **顯示所有檔案**] 按鈕。
    
  
4. 在 **ChainStore**專案中，依序展開 [ **AddEmployeeToCorpDB**然後再開啟 elements.xml 檔案。
    
  

## 了解兩難和其解決方案

在 elements.xml 檔案中， **RegistrationId**元素之屬性的 **CustomAction**識別其功能區新增] 按鈕的清單：  `{$ListId:Lists/Local Employees;}`。這正常運作正常運作時清單有已經新增至主機網頁以手動方式。但是，現在我們要部署中初次執行邏輯以程式設計方式的清單、 清單不存在當 SharePoint 安裝的增益集及嘗試部署] 按鈕。增益集的安裝會擲回例外狀況和失敗。
  
    
    
部署中安裝事件處理常式，而不是初次執行邏輯清單將不會解決兩難因為 SharePoint 部署自訂 descriptively 所定義的元件，例如自訂按鈕 (及 **進行順序**增益集組件)、 *之前*  執行自訂的處理常式中，因此將不會存在於 SharePoint 嘗試部署] 按鈕時的清單。
  
    
    
完全以程式設計方式建立自訂按鈕並不實用的太進階討論這裡的原因。幸運地是，不需要。有較輕鬆的方式分號以程式設計方式建立自訂按鈕並將它指派給自訂清單。基本步驟如下：
  
    
    

1. 專案中保留 descriptively 已定義的按鈕，但將其指派的功能區中的某個項目一律存在於 SharePoint 網站，而不是以程式設計方式部署具有相同的增益集的清單。
    
  
2. 在初次執行的邏輯，以程式設計方式建立清單之後，以程式設計方式新增未定義的] 按鈕的功能區中的清單。
    
  
3. 初始化新的按鈕的屬性與原始] 按鈕的值。此時有相同的兩個按鈕。第二個它們被指派給 **本機的員工**清單的功能區。
    
  
4. 以程式設計方式刪除原始的] 按鈕。
    
  

## 以程式設計方式登錄自訂按鈕

下列程序示範如何實作這項策略。
  
    
    

1. 在 **ChainStore**專案中，依序展開 [ **AddEmployeeToCorpDB**和再開啟 elements.xml 檔案中，並變更 **CustomAction**元素的 **RegistrationId**屬性的值為"100"。這是類型的清單的識別碼。即使有無執行個體在網站上這類型的清單、 清單 *類型*  是在每個 SharePoint 網站上。此屬性現在看起來應類似如下。
    
  ```XML
  
RegistrationId="100"
  ```

2. SharePointComponentDeployer.cs 檔案中新增下列行 `DeployChainStoreComponentsToHostWeb`方法，會呼叫 `CreateLocalEmployeesList`行下方。您會在下一個步驟中建立此方法。
    
  ```cs
  ChangeCustomActionRegistration();
  ```

3.  `SharePointComponentDeployer`類別新增下列方法。請注意下列有關這段程式碼：
    
  - 因為自訂動作 ；也就是自訂按鈕 ；已註冊使用功能區中的清單 *類型*  ，對整個網站範圍設定並自訂動作的網站集合中。讓程式碼會擷取其從該集合。
    
  
  -  `action.Name`的值是來自 **ID**元素之屬性的 **CustomAction**中 **AddEmployeeToCorpDB**element.xml 檔案中。
    
    > **重要**
      > **必須變更 `action.Name`值以符合您的 elements.xml 檔案中的值以下的程式碼中。** 名稱的 GUID 部分將會不同。請注意，"。"字元之間的 GUID 與其餘的名稱。以下是行的範例中。>  `where action.Name == "4a926a42-3577-4e02-9d06-fef78586b1bc.AddEmployeeToCorpDB"`

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

4.  `TODO8`取代下列程式碼。
    
    請注意當您要撤銷增益集、 增益集所建立的元件不會移除。初次執行邏輯執行、 將清單 **UserCustomActions**集合中的自訂動作及它不會撤銷件下一次之後會按 F5。若要避免混淆，此程式碼 `listActions.Clear();`的最後一行會清空集合。
    


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

5.  `TODO9`取代為下行，如此會在未定義的自訂動作新增至 **本機的員工**清單。
    
  ```cs
  
var listScopedEmployeeAction = listActions.Add();
  ```

6.  `TODO10`取代下列程式碼。請注意下列有關這段程式碼：
    
  - 它會將指派 web 範圍的按鈕 (即已部署具有描述性標記) 的屬性值給範圍清單] 按鈕的對應屬性讓範圍位於相同以外的兩個按鈕。
    
  
  - **Sequence**屬性指定] 按鈕會出現在其] 區域中的功能區的相對順序。在此例中按鈕是在 [ **動作**] 區段中的功能區的 [ **項目**] 索引標籤上。在描述性標記此值已設為 10001 即不夠高以確定它會顯示之後 (亦即右邊) 本身的 SharePoint 會放在功能區的 [ **動作**] 區段中的任何在方塊] 按鈕。
    
  

  ```cs
  listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
listScopedEmployeeAction.Update();
  ```

7.  `TODO11`取代為下行，由其刪除原始的 descriptively 定義] 按鈕。如果我們沒有這一行，使用"100"的清單範本的網站上的每個清單會在其上有自訂按鈕。由於 **本機的員工**清單與密切相關按鈕的功能，它會對有意義沒有有任何其他清單上的按鈕。而且、 這一行中，而] 按鈕就會出現 *兩次*  在 **本機員工**] 清單中，因為該清單使用範本"100"。
    
  ```cs
  
webScopedEmployeeAction.DeleteObject();
  ```


    整個方法現在看起來應類似如下 (除了應該取代預留位置的 GUID)。
    


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


## 要求的主機網站的完全控制

由於增益集現在會新增新增和刪除網站範圍設定的自訂動作，我們需要呈報增益集的要求管理為 Full Control 權限。請遵循下列步驟。
  
    
    

1. 在 **方案總管**] 中開啟 AppManifest.xml 檔案 **ChainStore**專案中。
    
  
2. 開啟保留 **範圍**值在 **網頁**，但 **權限**] 欄位中的 [ **權限**] 索引標籤中，從下拉式清單選取 **完全控制**向下。
    
  
3. 儲存該文件。
    
  

## 增益集執行及測試按鈕部署


  
    
    

1. 開啟 Hong 特別行政區存放區的網站的 [ **網站內容**] 頁面上 *並移除 **本機的員工**清單!* 
    
    > **注意事項**
      > 增益集在Visual Studio後退回，不會移除讓您需要手動刪除的每當您要測試其所建立的程式碼增益集] 中所建立的清單。
2. 使用 F5 鍵以部署及執行您的增益集Visual Studio主控 IIS Express 的遠端 web 應用程式和主控 SQL 資料庫的 SQL Express。它也讓您測試 SharePoint 網站上的 [增益集的暫存安裝，並立即執行增益集。系統提示您授與權限的增益集才能開始] 頁面隨即開啟。
    
  
3. 增益集開始頁面會隨即開啟，請選取 **回站台**連結上的 chrome 控制項頂端。
    
  
4. 瀏覽至 [ **網站內容**] 頁面上。因為您初次執行的邏輯將其新增有無 **本機的員工**清單。
    
    > **注意事項**
      > 若清單並非有或具有初次執行的程式碼不執行其他表示，則可能的 **承租人**表格不被 csdeviceupdateconfiguration 空的狀態時按 F5。最常見的原因是 **ChainCorporateDB**專案已不再設定為Visual Studio啟動專案。請參閱本文針對如何修正此問題頂端附近附註。也請確定您已設定要重新 [設定Visual Studio重建每個偵錯工作階段的公司資料庫](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md#Rebuild)中所述的資料庫。
5. 開啟清單並新增項目。
    
  
6. 在清單檢視中，選取的項目並開啟 [功能區上的 [ **項目**] 索引標籤。在功能區是 **新增至公司 DB** ] 按鈕。
    
  
7. 按一下按鈕與員工加入公司資料庫並 **新增至公司 DB**欄位變更為 [ **是]**。
    
  
8. 瀏覽至 [ **網站內容**」 頁面並選取 [ **新增增益集**。
    
  
9. 新增新的 **自訂清單**。依預設會 「 一般 」 類型。(一般為清單類型 100)。建立清單之後，請開啟 [功能區上的 [ **項目**] 索引標籤。請注意 **新增至公司 DB** ] 按鈕是 *不*  在功能區。這因為您的程式碼刪除 web 範圍] 按鈕。
    
  
10. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
11. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

事件清單及清單項目上的也可以在 SharePoint 中有自訂處理常式。了解如何建立 web 應用程式及部署在您初次執行中的邏輯下一篇文章:  [處理提供者主控增益集的清單項目事件](handle-list-item-events-in-the-provider-hosted-add-in.md)
  
    
    

