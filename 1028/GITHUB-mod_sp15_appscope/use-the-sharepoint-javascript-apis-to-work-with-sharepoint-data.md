---
title: 使用 SharePoint JavaScript Api 來處理 SharePoint 資料
ms.prod: SHAREPOINT
ms.assetid: 5c8767c2-317f-4bdb-8f4f-885d06da7feb
---


# 使用 SharePoint JavaScript Api 來處理 SharePoint 資料
使用 SharePoint JavaScript 物件模型處理JavaScript的 SharePoint 資料增益集 web 中的頁面。
這是以一系列的基本知識文章的開發 SharePoint 主控SharePoint Add-ins第 10 天。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將自訂欄新增至 SharePoint hostedSharePoint 增益集](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將自訂內容類型新增至 SharePoint hostedSharePoint 增益集](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將工作流程新增至 SharePoint 主控 SharePoint 增益集](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [以 SharePoint 主控 SharePoint 增益集新增自訂頁面與樣式](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [以 SharePoint 主控 SharePoint 增益集新增自訂的用戶端轉譯](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Create a custom ribbon button in the host web of a SharePoint Add-in](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md)
    
  

> [!注意事項]
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforeJSOM.sln 檔案。
  
    
    

即使 SharePoint 主控SharePoint Add-ins不能有伺服器端程式碼，您仍然可以與 SharePoint 元件的商務邏輯及執行階段互動 SharePoint 主控SharePoint Add-in使用JavaScript與 SharePoint JavaScript用戶端物件模型文件庫。（我們將會呼叫其 JSOM。請注意上結尾"M"。請勿將混淆這與 JSO **N** [JavaScript 物件標記法]。）本文中您可以使用JavaScript物件模型來找出並移除舊的項目從 **西雅圖的新員工**清單。
## 建立JavaScript與來叫用它的按鈕


1. 確認已完成下列步驟將此系列中第一個教學課程：
    
    專案的根目錄中開啟檔案 **/Pages/Default.aspx** 。除此之外，這個產生的檔案會載入一或兩個架設在 SharePoint 中的兩個指令碼： sp.runtime.js 和 sp.js。標記為載入這些檔案是具有識別碼 **PlaceHolderAdditionalPageHead**檔案頂端附近 **Content**控制項中。標記而異 **Microsoft Office Developer Tools for Visual Studio** 您所使用的版本。這一系列教學課程需要這兩個檔案會載入及它們使用一般的 HTML **<script>**標籤、 不 **<SharePoint:ScriptLink>**標籤載入。確定下列幾行 **PlaceHolderAdditionalPageHead**控制項 *正上方*  中線條 `<meta name="WebPartPageExpansion" content="full" />`：
    


  ```
  
<script type="text/javascript" src="/_layouts/15/sp.runtime.js"></script>
<script type="text/javascript" src="/_layouts/15/sp.js"></script> 

  ```


    再也會載入一個還是這些檔案的其他任何其他標記檔案中搜尋並移除重複標記。儲存並關閉檔案。
    
  
2. 在 [ **方案總管**中的 [ **指令碼**] 節點尚未可能有新增 in.js 檔案。如果沒有，但沒有 App.js，以滑鼠右鍵按一下 App.js 和新增 in.js 重新命名。如果沒有新增 in.js 或 App.js，建立一個使用下列步驟：
    
1. 以滑鼠右鍵按一下 [ **指令碼**] 節點並選擇 [ **新增]** > **新增項目**> **網頁**。
    
  
2. 選擇 [ **JavaScript 檔案**，並命名新增 in.js。
    
  
3. 開啟新增 in.js 並刪除其內容，如果沒有任何。
    
  
4. 檔案中新增下列幾行。請注意下列有關這段程式碼：
    
  -  `'use strict';`行確保如果您在不經意中JavaScript使用特定錯誤作法瀏覽器中的JavaScript執行階段將會傳回例外狀況。
    
  
  -  `clientContext`變數保留 **SP.ClientContext**物件參照的 SharePoint 網站。所有 JSOM 程式碼一都開始會建立，或是快速以這種類型的物件參考 （英文）。
    
  
  -  `employeeList`變數保留清單執行個體 **中西雅圖的新員工**的參照。
    
  
  -  `completedItems`變數保留從指令碼會刪除清單項目： **OrientationStage**欄位設定為 [ **已完成**的項目。
    
  

  ```
  
'use strict';

var clientContext = SP.ClientContext.get_current(); 
var employeeList = clientContext.get_web().get_lists().getByTitle('New Employees In Seattle'); 
var completedItems; 
  ```

5. 若要最小化用戶端瀏覽器與 SharePoint server 之間的郵件 JSOM 使用批次作業的系統。只有一個函數， **SP.ClientContext.executeQueryAsync**、 實際將郵件傳送給伺服器 （及接收回覆）。甚至傳來的 **executeQueryAsync**通話 JSOM Api 呼叫都是於並傳送至下一個階段 **executeQueryAsync**會呼叫批次中的伺服器。不過，不通常可以除非物件已帶 **executeQueryAsync**為上一個電話中的用戶端呼叫 JSOM 物件的方法。指令碼將要 **SP.ListItem.deleteObject**上呼叫方法的每個已完成的項目] 清單中，使它具有兩個撥打 **executeQueryAsync**;若要取得的完整的清單項目，然後批次的 **deleteObject**呼叫並將其傳送給伺服器執行第二個集合之一。
    
    先建立的方法，取得來自伺服器的清單項目。將下列程式碼新增至檔案。
    


  ```
  
function purgeCompletedItems() {

   var camlQuery = new SP.CamlQuery(); 
   camlQuery.set_viewXml( 
         '<View><Query><Where><Eq>' + 
           '<FieldRef Name=\\'OrientationStage\\'/><Value Type=\\'Choice\\'>Completed</Value>' + 
         '</Eq></Where></Query></View>'); 
     completedItems = employeeList.getItems(camlQuery); 
}
  ```

6. 這些行會傳送到伺服器並執行時，他們所建立的清單項目集合但指令碼必須將該集合至用戶端。這可以透過呼叫 **SP.ClientContext.load** 函數完成，所以將下行新增至結尾方法的方法。
    
  ```
  
clientContext.load(completedItems);
  ```

7. 新增 **executeQueryAsync**的通話。此方法有兩個參數，兩者都是回呼函式。第一個執行如果 server 成功執行批次中所有命令。第二個執行如果任何原因而失敗的伺服器。 您在稍後步驟中建立下列兩個函數。將下行新增至方法的結尾。
    
  ```
  clientContext.executeQueryAsync(deleteCompletedItems, onGetCompletedItemsFail);
  ```

8. 最後，新增下列行結尾的方法。藉由 **false**回到會呼叫此函數 ASP.NET 按鈕，我們會取消會重新載入] 頁面上的 ASP.NET 按鈕的預設行為。頁面的重新載入將會導致重新載入之增益集 in.js 檔案。接著，會重新初始化 `clientContext`物件。如果重新載入完成之間 **executeQueryAsync**傳送其要求的時間和 SharePoint server 回傳送回應的時間，然後原始 `clientContext`物件位於不再處理回應存在。此函數會使用 success 皆執行失敗回呼停止。（根據瀏覽器可能會有所不同完全行為）。
    
  ```
  return false;
  ```

9. 將下列函數 `deleteCompletedItems`、 新增至檔案。這是執行如果 `purgeCompletedItems`函數成功的函數。請注意下列有關這段程式碼：
    
  - **SP.ListItem.get_id**方法會傳回清單項目的識別碼。陣列中的每個項目是 **SP.ListItem**物件。
    
  
  - **SP.List.getItemById**方法會傳回 **SP.ListItem**物件與指定的識別碼。
    
  
  - **SP.ListItem.deleteObject**方法會標示的 **executeQueryAsync**電話時所要刪除的伺服器上的清單項目。
    
  
  - 清單項目已傳送的向下從伺服器陣列之前可以刪除集合中要複製。如果指令碼會呼叫 **deleteObject**方法直接在 **while**迴圈中每個項目， JavaScript會擲回錯誤抱怨列舉正在進行中時要變更之集合的長度。[] 錯誤訊息不逐字，則為 true，因為此項目不真正刪除從任何項目直到 **deleteObject**通話會於並傳送到伺服器，但 JSOM 的設計目的在於的模擬例外會擲回，就會發生 （其中程式碼不應該變更集合大小時正在列舉集合） 的伺服器上。不過，陣列有固定的大小，所以陣列中的項目上呼叫 **deleteObject**從清單中刪除項目，但不會變更陣列的大小。
    
  

  ```
  function deleteCompletedItems() {

    var itemArray = new Array();
    var listItemEnumerator = completedItems.getEnumerator();

    while (listItemEnumerator.moveNext()) {
        var item = listItemEnumerator.get_current();
        itemArray.push(item);
    }

    var i;
    for (i = 0; i < itemArray.length; i++) {
        employeeList.getItemById(itemArray[i].get_id()).deleteObject();
    }

    clientContext.executeQueryAsync(onDeleteCompletedItemsSuccess, onDeleteCompletedItemsFail);
}
  ```

10. 將下列函數 `onDeleteCompletedItemsSuccess`，新增至檔案。這是執行如果成功刪除已完成的項目 （或不完整的任何項目會在清單上） 的函數。第二條 `location.reload(true);`，會使要從伺服器重新載入頁面。這是讀者閱讀因為清單檢視網頁組件頁面上的仍會顯示完整的項目直到重新整理頁面。（新增 in.js 檔案重新載入，但因為它將不會中斷持續的 JavaScript 函數的方式達成不會造成問題。
    
  ```
  
function onDeleteCompletedItemsSuccess() {
    alert('Completed orientations have been deleted.');
    location.reload(true);
}
  ```

11. 將下列兩個回呼在失敗功能新增至檔案。
    
  ```
  
// Failure callbacks

function onGetCompletedItemsFail(sender, args) {
    alert('Unable to get completed items. Error:' + args.get_message() + '\\n' + args.get_stackTrace());
}

function onDeleteCompletedItemsFail(sender, args) {
    alert('Unable to delete completed items. Error:' + args.get_message() + '\\n' + args.get_stackTrace());
}
  ```

12. 開啟 default.aspx 檔案並找出具有識別碼 **PlaceHolderMain** **asp:Content**項目。
    
  
13. 新增下列標記之間 **WebPartPages:WebPartZone**元素與第一個的兩個 **asp:Hyperlink**元素。請注意 **OnClientClick**處理常式的值，而不是只 `purgeCompletedItems()` `return purgeCompletedItems()` 。此函數會傳回 `false`會告知 ASP.NET 無法重新載入頁面。
    
  ```HTML
  
<p><asp:Button runat="server" OnClientClick="return purgeCompletedItems()"
  ID="purgecompleteditemsbutton" Text="Purge Completed Items" /></p>
  ```

14. 重建 Visual Studio 中的專案。
    
  
15. 若要至最小化時的增益集測試手動設定為 [已完成的 **方向階段**的清單項目需要開啟清單執行個體 **NewEmployeesInSeattle** (不清單範本 **NewEmployeeOrientation**elements.xml) elements.xml 檔案並新增標記 `<Field Name="OrientationStage">Completed</Field>`做為最後一個子到一或多個 **Row**元素。
    
    以下是範例外觀 **Rows**元素為何。
    


  ```
  
<Rows>
  <Row>
    <Field Name="Title">Tom Higginbotham</Field>
    <Field Name="Division">Manufacturing</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Satomi Hayakawa</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Cassi Hicks</Field>
  </Row>
  <Row>
    <Field Name="Title">Lertchai Treetawatchaiwong</Field>
  </Row>
</Rows>
  ```


## 執行及測試增益集


  
    
    

1. 啟用快顯畫面的瀏覽器該Visual Studio時所使用您偵錯。
    
  
2. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。
    
  
3. 增益集的首頁隨即開啟並在 **[已完成**的 **方向階段**與清單上有一或多個項目。
    
   **列出前清除已完成的項目**

  

     ![「西雅圖新進員工」清單中「訓練階段」欄有兩個項目設為 [已完成] 。清單下方有標示為「清除已完成項目」的按鈕。](images/e5e4eef8-a218-4797-aabc-c52adbd2d96d.PNG)
  

  

  
4. 當已完全載入增益集的 [開始] 頁面上時，選擇 [ **清除已完成的項目**] 按鈕。如果作業成功 （您沒有收到任何失敗訊息），則會刪除所有 **完整**項目與您會看見表示 **已都刪除已完成方向**的快顯功能表訊息方塊。
    
  
5. 關閉快顯功能表及頁面會重新載入及 **已完成**的項目在清單檢視網頁組件已不再。。 {3}
    
   **之後清除已完成的項目清單**

  

     ![「西雅圖新進員工」清單比之前少兩個項目，且都未將「訓練階段」設為 [已完成]。](images/a0330fad-1473-4fde-9df2-8be0b37df1a1.PNG)
  

  

  
6. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
7. 您將會使用此增益集及Visual Studio解決方案其他篇文章和是一個增益集要撤銷個好習慣完成您的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管]**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在此系列中下一篇文章 ＜ 您將新增JavaScript增益集在 web 上的主機網路上的運作方式與 SharePoint 資料] 頁面上：  [Work with host web data from JavaScript in the add-in web](work-with-host-web-data-from-javascript-in-the-add-in-web.md)。
  
    
    

