---
title: 以 SharePoint 主控 SharePoint 增益集新增自訂的用戶端轉譯
ms.prod: SHAREPOINT
ms.assetid: 6e0e530a-7d8b-48ab-8d0c-a878ddbf5be1
---


# 以 SharePoint 主控 SharePoint 增益集新增自訂的用戶端轉譯
自訂轉譯和驗證SharePoint Add-ins頁面中的控制項。
這是以一系列的基本知識文章的開發 SharePoint 主控SharePoint Add-ins八分。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將自訂欄新增至 SharePoint hostedSharePoint 增益集](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將自訂內容類型新增至 SharePoint hostedSharePoint 增益集](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將工作流程新增至 SharePoint 主控 SharePoint 增益集](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [以 SharePoint 主控 SharePoint 增益集新增自訂頁面與樣式](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> [!注意事項]
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforeClientRenderedControl.sln 檔案。
  
    
    

您可以使用一點的用戶端JavaScript **JSLink**屬性的控制項，例如 **SPField.JSLink**指派JavaScript檔案以自訂的網頁組件、 大部分類型的欄位 (欄) 及其他控制項，轉譯。您也可以新增用戶端驗證邏輯以這種方式。 本文中您要使用用戶端轉譯自訂欄位清單中的員工方向SharePoint Add-in的轉譯。
> [!注意事項]
> 如果使用者在其瀏覽器中停用JavaScript ，SharePoint 會改為使用伺服器端呈現和驗證。
  
    
    


> [!注意事項]
> 屬性不支援調查或事件 JSLink 列出。將 SharePoint 行事曆是事件] 清單中。
  
    
    


## 建立與註冊JavaScript


  
    
    

1. 在 **方案總管**] 中以滑鼠右鍵按一下 [ **指令碼**] 節點並選擇 [ **新增]** > **新增項目**> **網頁**。
    
  
2. 選擇 [ **JavaScript 檔案**，並命名OrientationStageRendering.js。
    
  
3. 欄位您自訂轉譯的自動發生所以新增至檔案載入具有下列程式碼時自動執行JavaScript匿名方法。
    
  ```
  
(function () {

})();
  ```

4. 本文中於此方法 (之間 {} 字元)，新增下列程式碼建立的轉譯覆寫內容、 中之內容的範本和欄位範本 JSON (Javascript 物件表示法) 物件。
    
  ```
  
var customRenderingOverride = {};
customRenderingOverride.Templates = {};
customRenderingOverride.Templates.Fields = {

}
  ```

5. 本文中的 `Fields` template 物件，新增下列 JSON。屬性名稱 `OrientationStage`識別具有自訂的轉譯] 欄位。屬性的值是另一個 JSON 物件。 `View`屬性識別的順序套用自訂的轉譯的頁面內容。在此例中，物件會告知 SharePoint 清單檢視上使用自訂的轉譯。(其他選項將新的、 可供編輯，和顯示表單。)  `renderOrientationStage`，屬性的值是自訂的轉譯方法所建立的後續步驟中的名稱。
    
  ```
  
"OrientationStage": { "View": renderOrientationStage }
  ```

6. 匿名方法必須執行動作的最後一項重點是告訴 SharePoint 的範本管理員有關轉譯覆寫。將下行新增至方法的內文結尾。
    
  ```
  SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
  ```


    此方法現在看起來應類似如下。
    


  ```
  (function () {
    var customRenderingOverride = {};
    customRenderingOverride.Templates = {};
    customRenderingOverride.Templates.Fields = {
        "OrientationStage": { "View": renderOrientationStage }
    }

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
})();
  ```

7. 檔案中新增下列方法。其設定之 **方向階段**欄值的色彩為紅色值為 「 未啟動"時和為綠色完成值是""。(  `ctx`物件是在方塊 SharePoint 指令碼所宣告的用戶端快顯物件)。
    
  ```
  
function renderOrientationStage(ctx) {
    var orientationStageValue = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];
    if (orientationStageValue == "Not Started")  {
        return "<span style='color:red'>" + orientationStageValue + "</span>"
    }
    else if (orientationStageValue == "Completed") {
        return "<span style='color:green'>" + orientationStageValue + "</span>"
    }
    else {
        return orientationStageValue;
    }
}
  ```

8. 在 [ **方案總管**中，依序展開 [ **網站欄**，然後 **OrientationStage**;然後開啟 [elements.xml 檔案。
    
  
9. 要告訴 SharePoint，以使用您的自訂JavaScript，將新的屬性、 **JSLink**，新增至 **Field**項目，然後將指派其值為下列 URL：  `~site/Scripts/OrientationStageRendering.js`。
    
    > [!注意事項]
      > **JSLink**屬性一定是檔案，不一種方法。沒有要告訴 SharePoint 哪一種方法來執行方法。這就是為什麼檔案包含一種方法可將會自動執行。

    **Field**元素的開始標籤現在看起來像下列。
    


  ```
  
<Field
       ID="{some_guid_here}"
       Name="OrientationStage"
       Title="OrientationStage"
       DisplayName="Orientation Stage"
       Description="The current orientation stage of the employee."
       Type="Choice"
       Required="TRUE"
       Group="Employee Orientation" 
       JSLink="~site/Scripts/OrientationStageRendering.js">
<!-- child elements and end tag omitted -->
  ```

10. 開啟 Default.aspx 頁面並新增下列程式碼做為已設定為 **PlaceHolderMain** **ContentPlaceHolderID** **asp:Content**元素的最後一個子。
    
  ```XML
  
<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Lists/NewEmployeesInSeattle/AllItems.aspx';"
    Text="List View Page for New Employees in Seattle" /></p>

  ```


## 執行及測試增益集


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。
    
  
2. 您已設定用戶端轉譯會影響轉譯只能在不在清單檢視網頁組件，我們放在首頁的 [清單檢視] 頁面上的欄位。這是因為網頁組件的預設會是伺服器端轉譯。有方法可以回復，但他們太進階這個簡單的範例。如此，如需用戶端轉譯的巨集指令，請按一下連結] 頁面 **中西雅圖的新員工的清單檢視頁面**底部。
    
  
3. [清單檢視] 頁面開啟時，將一些項目 **方向階段**值設為 **未啟動**與其他人設為 **[已完成**若要查看自訂色彩轉譯。
    
   **具有自訂的用戶端轉譯的清單**

  

     ![「西雅圖新進員工」清單中，「未啟動」的訓練階段值為紅色，「已完成」的值為綠色。其他值為黑色。](images/dc8e2b7d-1747-4b65-aab4-6fc93c6867d4.PNG)
  

  

  
4. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
5. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在此系列中下一篇文章 ＜ 您將新增自訂功能表項目及自訂按鈕的功能區中SharePoint Add-in:  [Create a custom ribbon button in the host web of a SharePoint Add-in](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md)。
  
    
    

