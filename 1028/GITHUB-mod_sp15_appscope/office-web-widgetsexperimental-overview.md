---
title: Office Web Widget 實驗性的概觀
ms.prod: SHAREPOINT
ms.assetid: 6ce01956-6bda-45bf-9b4a-cffc0687a913
---



# Office Web Widget 實驗性的概觀
了解 Office Web Widget - 您可以使用Office 增益集、 SharePoint Add-ins，與網站中的 Experimental。
> [!注意]
> Office Web Widget - Experimental 只提供進行研究 (英文) 和意見反應。不在實際執行案例中使用。Office Web Widget 行為可能會大幅在未來的版本中變更。閱讀並檢閱 [Office Web Widget 實驗的授權條款](office-web-widgetsexperimental-license-terms.md)。
  
    
    

用戶端控制項，例如 Office Web Widget - Experimental，可大幅減少建立增益集所需的時間量並同時，增加之增益集的品質。這必須為 true，我們並且確定已符合特定準則 widget：
- 必須要使用的任何網頁、 設計 widget，即使頁面不架設在SharePoint。
    
  
- Widget 作用中的 Office 控制項執行階段。這可讓我們提供一組通用的需求和一致的語法使用 widget。
    
  
- 溝通SharePoint widget 使用的跨網域文件庫。Widget 不需要特定的伺服器端平台或技術上的相依性。您可以使用 widget 無論您選擇的伺服器技術。
    
  
- Widget 必須共存的頁面中的其他項目。加入到頁面 widget 的不應該修改其他元素。
    
  
- 播放好與現有的架構。我們想要確定您仍然可以使用的工具和技術，可用來。
    
  

**圖 1。增益集使用 Office Web Widget-實驗**

  
    
    

  
    
    
![Office Web Widget - 實驗示範](images/OfficeWebWidgetsOverview_demo.png)
  
    
    
您可以使用 widget 安裝 **Office Web Widget - Experimental** NuGet 套件從Visual Studio如需詳細資訊，請參閱 [使用對話方塊管理 NuGet 套件](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog)。您也可以瀏覽 [NuGet 圖庫] 頁面上](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)。您的意見反應和註解幫我們決定何種 widget 提供。如您所見圖 1，(1) 的人員選擇器與 (2) 桌面清單檢視 widget 可供您嘗試與試驗。請保持在 [Office 開發人員平台 UserVoice 網站](http://officespdev.uservoice.com/)您也可以參閱 widget 在 [Office Web Widget 實驗示範](http://code.msdn.microsoft.com/SharePoint-2013-Office-Web-6d44aa9e)程式碼範例中的動作。
## 人員選擇器 widget

您可以使用增益集在實驗的人員選擇 widget 可協助您的使用者尋找並選取 [人員與群組租用戶中。使用者就可以開始在文字方塊中輸入並 widget 擷取其名稱或電子郵件符合文字的人員。
  
    
    

**圖 2。人員選擇器 widget 解決查詢**

  
    
    

  
    
    
![頁面上的 [人員選擇] 實驗控制項](images/PeoplePicker_basic.png)
  
    
    
您可以宣告的 HTML 標記或以程式設計方式使用JavaScriptwidget。不論執行哪項中使用的 **div** 元素為預留位置以 widget。您也可以設定屬性 」 及 「 人員選擇 widget 的事件處理常式。下表顯示可用的屬性及事件中人員選擇 widget。
  
    
    


|**屬性/事件**|**類型**|**描述**|
|:-----|:-----|:-----|
|**objectType** <br/> |JSON 物件 (字串清單) <br/> |Widget 可解決的項目類型。選項： <br/> User <br/> Group <br/> 預設只有使用者。 <br/> |
|**allowMultipleSelections** <br/> |布林值 <br/> |True/False。若為 False，widget 應該允許次選取一個項目。          預設值 = False。 <br/> |
|**rootGroupName** <br/> |字串 <br/> |若提供，widget 會限制在此群組中的項目選取項目。          Widget 若未提供將會查詢整個租用的物件。 <br/> |
|**selectedItems** <br/> |JSON 陣列 <br/> |選取的項目清單。每個項目將會傳回代表使用者或群組的物件。 <br/> |
|**onAdded** <br/> |功能 <br/> |新物件加入至選取範圍時就會引發的事件。處理常式函數接收新增的物件。 <br/> |
|**onRemoved** <br/> |功能 <br/> |從選取範圍中移除的新物件時就會引發的事件。處理常式函數接收移除物件。 <br/> |
|**onChange** <br/> |功能 <br/> |[新增或移除物件觸發此事件。不含參數傳遞至處理常式函數。 <br/> |
|**validationErrors** <br/> |陣列 <br/> |可能的驗證錯誤的陣列： <br/> 空白 <br/> unresolvedItem <br/> tooManyItems <br/> |
|**autoShowValidationMessage** <br/> |布林值 <br/> |True = 顯示          False = ' t 顯示的 Don <br/> |
|**hasErrors** <br/> |布林值 <br/> |True = 1 或多個驗證錯誤          False = 沒有任何驗證錯誤 <br/> |
|**錯誤** <br/> |陣列 <br/> |可能的驗證錯誤的陣列： <br/> 空白 <br/> unresolvedItem <br/> tooManyItems <br/> |
|**displayErrors** <br/> |布林值 <br/> |True = 顯示錯誤          False = Don t 顯示錯誤 <br/> |
   
人員選擇 widget CSS 類別定義中 **Office.Controls.css** 樣式表。您可以覆寫類別並樣式 widget 增益集。
  
    
    
如需詳細資訊，請參閱 [使用實驗的人員選擇 widget 中 SharePoint 增益集](use-the-experimental-people-picker-widget-in-sharepoint-add-ins.md)及 [使用增益集在人員選擇實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)程式碼範例。
  
    
    

## 桌面的清單檢視 widget

您的使用者可以獲益清單檢視 widget 並顯示就像是一般清單檢視 widget，清單中的資料但您可以用於您的增益集不一定是架設在SharePoint。
  
    
    

**圖 3。在清單中顯示資料的桌面清單檢視 widget**

  
    
    

  
    
    
![頁面上的桌面清單檢視實驗控制項](images/DesktopListView_basic.png)
  
    
    
您可以指定現有清單上的檢視、 widget 轉譯的順序出現在檢視中的欄位。
  
    
    

    
> [!注意事項]
> 此刻、 桌面清單檢視 widget 只顯示資料。它並不提供編輯功能。
  
    
    

您可以使用 **div** 元素的 widget 提供版面配置區。您可以透過程式設計方式或以宣告使用 widget。
  
    
    
您也可以設定的屬性或桌面清單檢視 widget 的事件處理常式。下表會顯示在清單檢視中的桌面 widget 中可用的屬性及事件。
  
    
    


|**屬性/事件**|**類型**|**描述**|
|:-----|:-----|:-----|
|**listUrl** <br/> |URL <br/> |繪製從項目清單檢視中的 URL。它可以是相對 URL 在此情況下它會假設位於增益集 web 本身或絕對 URL。 <br/> |
|**viewName** <br/> |字串 <br/> |若要顯示的檢視名稱。這是以程式設計方式檢視 (不其顯示名稱) 的名稱。 <br/> |
|**onItemSelected** <br/> |功能 <br/> |在清單中選取項目時就會引發的事件。 <br/> |
|**onItemAdded** <br/> |功能 <br/> |新項目新增至清單時就會引發的事件。 <br/> |
|**onItemRemoved** <br/> |功能 <br/> |從清單中移除項目時就會引發的事件。 <br/> |
|**selectedItems** <br/> |陣列 <br/> |JSON 格式的選取項目清單。 <br/> |
   
Widget 需要SharePoint網站樣式表。您可以直接參照SharePoint樣式表或使用 chrome widget。如需樣式表的詳細資訊，請參閱 [使用 SharePoint 網站的樣式表中 SharePoint 增益集](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)和 [使用用戶端的 chrome 控制項中 SharePoint 增益集](use-the-client-chrome-control-in-sharepoint-add-ins.md)。
  
    
    
要在巨集指令中的清單檢視 widget，請參閱 [使用增益集在清單檢視中的桌面實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)程式碼範例。另請參閱 [使用實驗桌面清單檢視 widget 中 SharePoint 增益集](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md)。
  
    
    

## 結論

Widget 可協助加快開發程序並減少的成本和時間上市之您新增寫 Office Web Widget - Experimental 提供您可以使用您對非實際執行增益集的 widget。您的意見反應和註解的歡迎使用 [Office 開發人員平台 UserVoice 網站](http://officespdev.uservoice.com/)中。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [Office Web Widget 實驗的授權條款](office-web-widgetsexperimental-license-terms.md)
    
  
-  [Office Web Widget 實驗 NuGet 圖庫] 頁面上](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)
    
  
-  [使用實驗的人員選擇 widget 中 SharePoint 增益集](use-the-experimental-people-picker-widget-in-sharepoint-add-ins.md)
    
  
-  [程式碼範例： Office Web Widget 實驗示範](http://code.msdn.microsoft.com/SharePoint-2013-Office-Web-6d44aa9e)
    
  
-  [使用實驗桌面清單檢視 widget 中 SharePoint 增益集](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md)
    
  
-  [程式碼範例： 在增益集中使用人員選擇實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)
    
  
-  [程式碼範例： 使用增益集清單檢視中的桌面實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)
    
  
