---
title: 使用實驗的人員選擇 widget 中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: edc60550-67d2-4230-8e27-06a328c0d1f1
---


# 使用實驗的人員選擇 widget 中 SharePoint 增益集
即使頁面不架設在SharePoint了解如何使用人員選擇 widget 在任何網頁。用於您的增益集的人員選擇 widget 可協助使用者尋找並選取 [人員與群組。
> **注意**
> Office Web Widget - Experimental 只提供進行研究 (英文) 和意見反應。不在實際執行案例中使用。Office Web Widget 行為可能會大幅在未來的版本中變更。閱讀並檢閱 [Office Web Widget 實驗的授權條款](office-web-widgetsexperimental-license-terms.md)。
  
    
    

您可以使用增益集在實驗的人員選擇 widget 可協助您的使用者尋找並選取 [人員與群組租用戶中。使用者就可以開始在文字方塊中輸入並 widget 擷取其名稱或電子郵件符合文字的人員。
**圖 1。人員選擇器 widget 解決查詢**

  
    
    

  
    
    
![頁面上的 [人員選擇] 實驗控制項](images/PeoplePicker_basic.png)
  
    
    

  
    
    

  
    
    
增益集可存取選取的人員閱讀 widget **selectedItems** 屬性。SelectedItems 屬性是代表人員或群組的物件陣列。下表顯示可用使用者物件的屬性。

|**屬性**|**描述**|
|:-----|:-----|
|**Department** <br/> |代表使用者或群組的部門。 <br/> |
|**displayName** <br/> |代表使用者或群組的顯示名稱。 <br/> |
|**電子郵件** <br/> |代表使用者或群組的電子郵件地址。 <br/> |
|**isResolved** <br/> |會指出是否 widget 已成功解析 widget 針對使用者或群組的租用戶中的文字。 <br/> |
|**jobTitle** <br/> |代表使用者的職稱。 <br/> |
|**loginName** <br/> |代表使用者或群組的登入名稱。 <br/> |
|**手機** <br/> |代表使用者或群組的行動電話號碼。 <br/> |
|**principalId** <br/> |代表使用者或群組的主體識別碼。 <br/> |
|**principalType** <br/> |指出項目是否使用者或群組。已定義的值是 1 時的使用者、 4 如果它是群組。 <br/> |
|**sipAddress** <br/> |代表使用者或群組的 sip 位址。 <br/> |
|**文字** <br/> |代表文字標題的使用者或群組名稱。 <br/> |
   
人員選擇 widget 具有快取的最近使用過 (MRU) 項目。快取儲存 widget 解析的五個最新項目。
## 此範例使用本文中的先決條件

若要使用本文中的範例，您需要下列項目：
  
    
    

- Visual Studio 2013.
    
  
- NuGet 封裝管理員。如需詳細資訊，請參閱 [安裝 NuGet](http://go.microsoft.com/fwlink/?LinkId=271465)。
    
  
- SharePoint 2013開發環境 (隔離應用程式所需的內部部署案例)。
    
  
- Office Web Widget-實驗 NuGet 套件。如需如何安裝 NuGet 套件的詳細資訊，請參閱 [使用對話方塊管理 NuGet 套件](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog)。您也可以瀏覽 [NuGet 圖庫] 頁面上](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)。
    
  

## 在 [提供者主控SharePoint Add-in中使用人員選擇 widget

在這個範例中，有主控外部SharePoint宣告使用標記人員選擇 widget 簡單] 頁面。若要保留的事項簡單，本範例會不會宣告的任何選項，但您可以看到 [  [NextSteps](use-the-experimental-people-picker-widget-in-sharepoint-add-ins.md#NextSteps) ] 區段中的選項與範例。
  
    
    
若要使用人員選擇 widget，您必須執行下列動作：
  
    
    

- 建立SharePoint Add-in和 web 的專案。
    
  
- 建立增益集在 web 上的模組。此步驟可確保使用者部署的增益集時建立的增益集網頁。
    
    > **注意事項**
      > 跨網域文件庫要求增益集 web 存在。人員選擇 widget 會與SharePoint通訊所使用的跨網域文件庫。
- 建立增益集] 頁面上的宣告使用標記的人員選擇 widget 執行個體。
    
  

### 若要建立SharePoint Add-in和 web 的專案


1. 系統管理員身分開啟 Visual Studio 2013。(若要這樣做，選擇 [ **開始**] 功能表上的 Visual Studio 2013 圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立新專案使用SharePoint Add-in 2013年範本。 **SharePoint Add-in 2013年** 範本位於 [ **範本**] 下 > **Visual C#** **Office /SharePoint**> **增益集**。
    
  
3. 提供您想要用於偵錯SharePoint網站 URL。
    
  
4. 選取 [ **提供者主控**為增益集的主控選項。
    
    > **注意事項**
      > 您也可以使用人員選擇 widget 搭配其他主控選項或甚至是 Office 增益集或您自己的網站。
5. 選取 [ **ASP.NET Web 表單應用程式**的 web 應用程式專案類型。
    
  
6. 選取 [ **Windows Azure Access Control Service** ] 為 [驗證] 選項。
    
  

### 若要建立增益集在 web 上的模組


1. 選擇 [ **方案總管**中的 [ SharePoint Add-in專案]。選擇 [ **新增**> **新增項目**
    
  
2. 選擇 [ **Visual C# 的項目**> **Office /SharePoint**> **模組**。提供在模組的名稱。
    
    > **注意事項**
      > 如果您正在建立SharePoint 裝載的增益集，您不需要建立額外的模組。

### 若要新增使用人員選擇 widget 的新頁面


1. 選擇 [ **頁面**] 資料夾中的 **方案總管**中的 web 專案。
    
  
2. 下列程式碼複製並貼入 **ASPX** 檔案中專案中。程式碼會執行下列工作：
    
  - 新增所需的 Office 文件庫及資源的參照。
    
  
  - 初始化控制項執行階段。
    
  
  - 執行 Office 控制項執行階段的 **renderAll**方法。
    
  
  - 宣告人員選擇 widget 的預留位置。
    
  

  ```
  
<!DOCTYPE html>
<html>
<head>
    <!-- IE9 or superior -->
    <meta http-equiv="X-UA-Compatible" content="IE=9" >
    <title>People Picker HTML Markup</title>

    <!-- Widgets Specific CSS File -->
    <link 
        rel="stylesheet" 
        type="text/css" 
        href="../Scripts/Office.Controls.css" 
    />

    <!-- Ajax, jQuery, and utils --> 
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js.js">
    </script>
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js">
    </script>
    <script type="text/javascript">
        // Function to retrieve a query string value.
        // For production purposes you may want to use
        //  a library to handle the query string.
        function getQueryStringParameter(paramToRetrieve) {
            var params =
                document.URL.split("?")[1].split("&amp;");
            var strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
                var singleParam = params[i].split("=");
                if (singleParam[0] == paramToRetrieve)
                    return singleParam[1];
            }
        }
    </script>

    <!-- Cross-Domain Library and Office controls runtime -->
    <script type="text/javascript">
        //Register namespace and variables used through the sample
        Type.registerNamespace("Office.Samples.PeoplePickerBasic");
        //Retrieve context tokens from the querystring
        Office.Samples.PeoplePickerBasic.appWebUrl =
            decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));
        Office.Samples.PeoplePickerBasic.hostWebUrl =
            decodeURIComponent(getQueryStringParameter("SPHostUrl"));

        //Pattern to dynamically load JSOM and and the cross-domain library
        var scriptbase =
            Office.Samples.PeoplePickerBasic.hostWebUrl + "/_layouts/15/";

        //Get the cross-domain library
        $.getScript(scriptbase + "SP.RequestExecutor.js",
            //Get the Office controls runtime and 
            //  continue to the createControl function
            function () {
                $.getScript("../Scripts/Office.Controls.js", createControl)
            }
        );
    </script>

    <!--People Picker -->
    <script 
        src="../Scripts/Office.Controls.PeoplePicker.js" 
        type="text/javascript">
    </script>
</head>
<body>
Basic People Picker sample (HTML markup declaration):
<div 
        id="PeoplePickerDiv" 
        data-office-control="Office.Controls.PeoplePicker">
</div>

<script type="text/javascript">
    function createControl() {
        //Initialize Controls Runtime
        Office.Controls.Runtime.initialize({
            sharePointHostUrl: Office.Samples.PeoplePickerBasic.hostWebUrl,
            appWebUrl: Office.Samples.PeoplePickerBasic.appWebUrl
        });

        //Render the widget, this must be executed after the
        //placeholder DOM is loaded
        Office.Controls.Runtime.renderAll();
    }
</script>
</body>
</html>

  ```


> **注意事項**
> 在上面的程式碼範例明確指定要初始化的 Office 控制項執行階段的主機網頁伺服器和增益集 web Url。不過，如果增益集網頁和主機網頁 Url 指定 **SPAppWebUrl** 及 **SPHostUrl** 查詢字串參數中，分別;您可以傳遞空物件並將程式碼會嘗試自動取得的參數。當您使用 **{StandardTokens}** 權杖 **SPAppWebUrl** 與 **SPHostUrl** 參數並包含查詢字串。
  
    
    

下列範例會示範如何傳遞至 initialize 方法的空物件：
  
    
    



```

// Initialize with an empty object and the code
// will attempt to get the tokens from the
// query string directly.
Office.Controls.Runtime.initialize({});
```


### 若要建置和執行解決方案


1. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio 建置解決方案、 增益集、 部署與開啟的增益集的 [權限] 頁面。
2. 選擇 [ **信任它**] 按鈕。
    
  
3. 選擇 [ **網站內容**] 頁面上的 [增益集] 圖示。
    
  
您也可以下載此範例從程式碼組件庫，請參閱 [使用增益集在人員選擇實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)程式碼範例。
  
    
    

## 
<a name="NextSteps"> </a>

本文說明如何使用人員選擇 widget 在增益集中使用 HTML。您也可以探索下列案例和相關 widget 的詳細資訊。
  
    
    

### 使用JavaScript宣告人員選擇 widget

您偏好您可能會想要用於JavaScript而不是 HTML 宣告 widget。如果這是您可以使用下列標記作為版面配置區 widget 大小寫。
  
    
    

```HTML

<div id="PeoplePickerDiv"></div>
```

使用下列JavaScript程式碼產生 「 人員選擇 」。
  
    
    



```
new Office.Controls.PeoplePicker(
    document.getElementById("PeoplePickerDiv"), {});
```

程式碼範例示範如何執行工作，請參閱 [使用增益集在人員選擇實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)程式碼範例中的 [ **JSSimple.html** ] 頁面。
  
    
    

### 指定 widget 選項

您可以指定選項 widget widget 宣告中使用 **資料 office 選項** 屬性。下列的 HTML 示範如何指定 PeoplePicker widget 選項。
  
    
    

```HTML

<div id="PeoplePickerDiv"
        data-office-control="Office.Controls.PeoplePicker"
        data-office-options='{
        "allowMultipleSelections" : true,
        "onChange" : handleChange,
        "placeholder" : "Check the count message, it changes when you add names..."
    }'>
</div>
```

下列程式碼示範如何宣告使用JavaScriptPeoplePicker widget 時指定選項。
  
    
    



```

new Office.Controls.PeoplePicker(
    document.getElementById("PeoplePickerDiv"), {
        allowMultipleSelections: true,
        placeholder: "Check the count message, it changes when you add names...",
        onChange: function (ctrl) {
            document.getElementById("count").textContent = 
ctrl.selectedItems.length.toString();
        }
    });
```

您也可以指定 **onChange** 、 **onAdded** ，以及 **onRemoved** 事件的事件處理常式。請注意上面的程式碼中，onChange 事件的事件處理常式接收單一參數 **ctrl 鍵** ，這是 widget 的參照。
  
    
    
如需指定選項的範例，請參閱 **MarkupOptions.html** 和 **JSOptions.html** 頁面中 [使用增益集在人員選擇實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)程式碼範例。
  
    
    

### 擷取的人員或 widget 中所選取的群組

若要擷取在 widget 人員您必須執行下列工作：
  
    
    

- 要取得 widget 的參考。
    
  
- 存取 widget **selectedItems** 屬性。
    
  
您可以取得使用下列語法 widget 的參照。
  
    
    



```

var pplPicker = document.getElementById("PeoplePickerDiv")._officeControl;
```

您也可以在時產生 widget 儲存參考 (英文)。
  
    
    



```
var pplPicker = new Office.Controls.PeoplePicker(
                        document.getElementById("PeoplePickerDiv"), {});
```

 **SelectedItems** 屬性是代表人員或群組的物件陣列。人員或群組的 selectedItems 陣列可以是解析或無法解析，其中您可以檢查 **isResolved** 屬性中。下列範例會示範如何存取陣列中的項目 *i*  及使用的個人或群組名稱。
  
    
    



```

var principal = pplPicker.selectedItems[i];
$("#msg").text(principal.text + " is selected in the control.");
```

如何從 widget 擷取所選的人員或群組的範例，請參閱 **demo.html** ] 頁面上的 [Office Web Widget 實驗示範](http://code.msdn.microsoft.com/SharePoint-2013-Office-Web-6d44aa9e)程式碼範例。
  
    
    

### 自訂 widget 的樣式

身為開發人員，您可能會想要自訂的外觀與風格 widget。下圖會顯示 HTML 階層中 widget 轉換之後。
  
    
    

**圖 2。在 [人員選擇 widget HTML 階層**

  
    
    

  
    
    
![[人員選擇] 控制項中的 HTML 階層](images/PeoplePicker_HTMLHierarchy.png)
  
    
    
Widget 定義開頭 **office peoplepicker** ，您可以找到並中 **Office.Controls.css** 樣式表自訂加上的許多類別。
  
    
    

## 總結
<a name="NextSteps"> </a>

您可以使用實驗的人員選擇 widget 以選取 [在您的租用戶中的 [人員與群組、 增益集便可以使用您的使用者所選的主體。請提供意見與 [Office 開發人員平台 UserVoice 網站](http://officespdev.uservoice.com/)中的註解。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [Office Web Widget 實驗性的概觀](office-web-widgetsexperimental-overview.md)
    
  
-  [Office Web Widget 實驗的授權條款](office-web-widgetsexperimental-license-terms.md)
    
  
-  [Office Web Widget 實驗 NuGet 圖庫] 頁面上](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)
    
  
-  [程式碼範例： 在增益集中使用人員選擇實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)。
    
  
-  [使用實驗桌面清單檢視 widget 中 SharePoint 增益集](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md) .
    
  

