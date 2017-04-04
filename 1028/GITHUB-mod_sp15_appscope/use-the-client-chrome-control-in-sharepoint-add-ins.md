---
title: 使用用戶端的 chrome 控制項中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 7c2d0812-76e8-44c1-88bf-4a75eb6f82b1
---


# 使用用戶端的 chrome 控制項中 SharePoint 增益集
了解如何使用增益集SharePoint 2013chrome 控制項。
SharePoint 2013中的chrome 控制項可讓您使用增益集在特定的 SharePoint 網站的標題樣式設定而不需要註冊的伺服器文件庫或使用特定的技術或工具。若要使用這項功能，您必須註冊透過標準 < 指令碼 > 標記 SharePoint JavaScript文件庫。您可以使用 HTML **div**元素提供版面配置區及進一步自訂控制項使用可用的選項。控制項繼承指定的 SharePoint 網站及其外觀。
  
    
    


## 此範例使用本文中的先決條件
<a name="SP15Usechromecontrol_Prereq"> </a>

若要遵循此範例中的步驟，您需要下列項目：
  
    
    

- Visual Studio 2015
    
  
- SharePoint 2013開發環境 (增益集隔離所需的內部部署案例)
    
  
如需如何設定開發環境適合您需求的指引，請參閱 [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx)。
  
    
    

### 若要使用的 chrome 控制項前需要瞭解的核心概念

下表列出可協助您了解使用的 chrome 控制項案例的相關概念的實用文章。
  
    
    

**表 1。使用的 chrome 控制項的核心概念**


|**文章標題**|**描述**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |了解新增益集模型中的全部SharePoint 2013可讓您建立增益集，亦即使用者的小型、 易於使用的解決方案。 <br/> |
| [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md) <br/> |了解使用者經驗 (UX) 選項以及您必須建立SharePoint Add-ins時的替代方法。 <br/> |
| [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |了解主機 web 與增益集 web 之間的差別。深入了解哪些SharePoint 2013元件還包含在SharePoint Add-in、 哪些元件部署到主機網站、 哪些元件部署到增益集 web 及增益集 web 隔離網域中的部署方式。 <br/> |
   

## 程式碼範例： 使用在雲端託管增益集的 chrome 控制項
<a name="SP15Usechromecontrol_Codeexample"> </a>

雲端託管增益集包含至少一個遠端元件。如需詳細資訊，請參閱 [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)。若要使用的 chrome 控制項您雲端託管增益集，請遵循下列步驟：
  
    
    

1. 建立SharePoint Add-in和遠端 web 專案。
    
  
2. 傳送的查詢字串中的預設設定選項。
    
  
3. 將網頁新增至 web 專案。
    
  
圖 1 顯示的 chrome 控制項與遠端網頁。
  
    
    

**圖 1。遠端網頁組件區塊控制項**

  
    
    

  
    
    
![A remote web page with the chrome control](images/ChromeControl_result.png)
  
    
    

### 若要建立SharePoint Add-in和遠端 web 專案


1. 系統管理員身分開啟 Visual Studio 2015。(若要這樣做，以滑鼠右鍵按一下 [ **開始**] 功能表上的 Visual Studio 2015 圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立新專案使用 **SharePoint 增益集**範本。
    
    圖 2 顯示在 Visual Studio 2015、 **範本**、 **Visual C#**、 **Office/SharePoint**、 **Office 增益集**下 **SharePoint 增益集**範本的位置。
    

   **圖 2。Visual Studio 範本 SharePoint 增益集**

  

     ![App for SharePoint 2013 Visual Studio template](images/AppForSharePointVSTemplate.PNG)
  

  

  
3. 提供您想要用於偵錯之 SharePoint 網站的 URL。
    
  
4. 選取 [ **提供者主控**為增益集的主控選項。SharePoint 主控的程式碼範例，請參閱 ＜  [SharePoint-增益集在-JSOM-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-BasicDataOperations)。
    
    在精靈完成之後，您應該在 **方案總管中**的格式類似於圖 3 有結構。
    

   **圖 3。在方案總管中的 SharePoint 專案的增益集**

  

     ![App for SharePoint projects in Solution Explorer](images/AppVSTemplateSolutionExplorer.jpg)
  

  

  

### 查詢字串中傳送預設設定選項


1. 資訊清單編輯器中開啟 Appmanifest.xml 檔案。
    
  
2. 將 **{StandardTokens}**權杖和其他 _SPHostTitle_參數新增至查詢字串。圖 4 顯示含有已設定的查詢字串參數的資訊清單編輯器。
    
   **圖 4。Chrome 控制項的查詢字串參數的資訊清單編輯器**

  

     ![Manifest editor with query string parameters](images/ChromeControl_manifest.PNG)
  

    Chrome 控制項自動接受下列值的查詢字串：
    
  - **SPHostUrl**
    
  
  - **SPHostTitle**
    
  
  - **SPAppWebUrl**
    
  
  - **SPLanguage**
    
  

    **{StandardTokens}**包括 **SPHostUrl**和 **SPAppWebUrl**。
    
  

### 將 web 專案中使用的 chrome 控制項的頁面


1. 以滑鼠右鍵按一下 [web 專案，並新增新的 Web 表單。
    
  
2. 複製下列標記，並將其貼在 ASPX 頁面。標記會執行下列工作：
    
  - 從 Microsoft CDN (內容傳遞網路) 載入的 AJAX 文件庫。
    
  
  - 從 Microsoft CDN 載入 jQuery 文件庫。
    
  
  - 載入 SP.使用者介面。使用 jQuery 函數 **getScript**Controls.js 檔案。
    
  
  - 定義 **onCssLoaded**事件的回呼函數。
    
  
  - 準備的 chrome 控制項的選項。
    
  
  - 初始化的 chrome 控制項。
    
  

  ```HTML
  
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>Chrome control host page</title>
    <script 
        src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js" 
        type="text/javascript">
    </script>
    <script 
        type="text/javascript" 
        src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
    </script>      
    <script 
        type="text/javascript"
        src="ChromeLoader.js">
    </script>
<script type="text/javascript">
"use strict";

var hostweburl;

//load the SharePoint resources
$(document).ready(function () {
    //Get the URI decoded URL.
    hostweburl =
        decodeURIComponent(
            getQueryStringParameter("SPHostUrl")
    );

    // The SharePoint js files URL are in the form:
    // web_url/_layouts/15/resource
    var scriptbase = hostweburl + "/_layouts/15/";

    // Load the js file and continue to the 
    //   success handler
    $.getScript(scriptbase + "SP.UI.Controls.js", renderChrome)
});

// Callback for the onCssLoaded event defined
//  in the options object of the chrome control
function chromeLoaded() {
    // When the page has loaded the required
    //  resources for the chrome control,
    //  display the page body.
    $("body").show();
}

//Function to prepare the options and render the control
function renderChrome() {
    // The Help, Account and Contact pages receive the 
    //   same query string parameters as the main page
    var options = {
        "appIconUrl": "siteicon.png",
        "appTitle": "Chrome control add-in",
        "appHelpPageUrl": "Help.html?"
            + document.URL.split("?")[1],
        // The onCssLoaded event allows you to 
        //  specify a callback to execute when the
        //  chrome resources have been loaded.
        "onCssLoaded": "chromeLoaded()",
        "settingsLinks": [
            {
                "linkUrl": "Account.html?"
                    + document.URL.split("?")[1],
                "displayName": "Account settings"
            },
            {
                "linkUrl": "Contact.html?"
                    + document.URL.split("?")[1],
                "displayName": "Contact us"
            }
        ]
    };

    var nav = new SP.UI.Controls.Navigation(
                            "chrome_ctrl_placeholder",
                            options
                        );
    nav.setVisible(true);
}

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
</head>

<!-- The body is initally hidden. 
     The onCssLoaded callback allows you to 
     display the content after the required
     resources for the chrome control have
     been loaded.  -->
<body style="display: none">

    <!-- Chrome control placeholder -->
    <div id="chrome_ctrl_placeholder"></div>

    <!-- The chrome control also makes the SharePoint
          Website stylesheet available to your page -->
    <h1 class="ms-accentText">Main content</h1>
    <h2 class="ms-accentText">The chrome control</h2>
    <div id="MainContent">
        This is the page's main content. 
        You can use the links in the header to go to the help, 
        account or contact pages.
    </div>
</body>
</html>
  ```

3. 您也可以使用的 chrome 控制項的宣告式的方式。在下列程式碼範例中，HTML 標記宣告控制項而不使用JavaScript程式碼來設定及初始化控制項。下列的標記會執行下列工作：
    
  - 提供 SP.預留位置使用者介面。Controls.js JavaScript檔案。
    
  
  - 動態載入 SP.使用者介面。Controls.js 檔案
    
  
  - 提供的 chrome 控制項的版面配置區並指定選項內嵌具有 HTML 標記。
    
  

  ```HTML
  
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>Chrome control host page</title>
    <script 
        src="http://ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js" 
        type="text/javascript">
    </script>
    <script 
        type="text/javascript" 
        src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
    </script>      
    <script type="text/javascript">
    var hostweburl;

    // Load the SharePoint resources.
    $(document).ready(function () {

        // Get the URI decoded add-in web URL.
        hostweburl =
            decodeURIComponent(
                getQueryStringParameter("SPHostUrl")
        );

        // The SharePoint js files URL are in the form:
        // web_url/_layouts/15/resource.js
        var scriptbase = hostweburl + "/_layouts/15/";

        // Load the js file and continue to the 
        // success handler.
        $.getScript(scriptbase + "SP.UI.Controls.js")
    });

    // Function to retrieve a query string value.
    // For production purposes you may want to use
    // a library to handle the query string.
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
</head>
<body>

    <!-- Chrome control placeholder 
           Options are declared inline.  -->
    <div 
        id="chrome_ctrl_container"
        data-ms-control="SP.UI.Controls.Navigation"  
        data-ms-options=
            '{  
                "appHelpPageUrl" : "Help.html",
                "appIconUrl" : "siteIcon.png",
                "appTitle" : "Chrome control add-in",
                "settingsLinks" : [
                    {
                        "linkUrl" : "Account.html",
                        "displayName" : "Account settings"
                    },
                    {
                        "linkUrl" : "Contact.html",
                        "displayName" : "Contact us"
                    }
                ]
             }'>
    </div>
    
    <!-- The chrome control also makes the SharePoint
          Website style sheet available to your page. -->
    <h1 class="ms-accentText">Main content</h1>
    <h2 class="ms-accentText">The chrome control</h2>
    <div id="MainContent">
        This is the page's main content. 
        You can use the links in the header to go to the help, 
        account or contact pages.
    </div>
</body>
</html>
  ```


    SP.使用者介面。如果發現 **div**元素中 **data-ms-control="SP.UI.Controls.Navigation"**屬性 Controls.js 文件庫自動會轉譯控制項。
    
  

### 若要編輯的增益集資訊清單中的開始頁面元素


1. 按兩下 [ **方案總管**中的 **AppManifest.xml**檔案。
    
  
2. 按一下 [ **開始] 頁面上**按下拉式] 功能表選擇 [使用的 chrome 控制項之網頁。
    
  

### 若要建置和執行解決方案


1. 請確定SharePoint Add-in專案已設為啟動專案。
    
  
2. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio建置解決方案、 增益集、 部署和增益集的權限] 頁面會隨即開啟。
3. 選擇 [ **信任它**] 按鈕。
    
  
4. 按一下 [ **ChromeControlCloudhosted**增益集] 圖示。
    
  
5. 當您在網頁中使用的 chrome 控制項時，您也可以使用 SharePoint 網站樣式表，如圖 4 所示。
    
   **圖 5。使用中] 頁面上的 SharePoint 網站樣式表**

  

     ![SharePoint website stylesheet used in a page](images/ChromControl_stylesheet.png)
  

  

  

**表 2。解決方案的疑難排解**


|**問題**|**解決方法**|
|:-----|:-----|
|**SP 未定義** 處理的例外狀況。 <br/> |請確定您的瀏覽器載入 SP.使用者介面。Controls.js 檔案。 <br/> |
|Chrome 控制項無法正確顯示。 <br/> |Chrome 控制項只支援 Internet Explorer 8 和上層文件模式。請確定您的瀏覽器轉譯您在文件模式 Internet Explorer 8 或上層的頁面。 <br/> |
|[憑證錯誤。 <br/> |將 web 專案 **已啟用 SSL** 屬性設定為 false。在SharePoint Add-in專案，將 **Web 專案** 屬性設定為None，然後再屬性設定回 web 專案的名稱。 <br/> |
   

## 後續步驟
<a name="SP15Usechromecontrol_Nextsteps"> </a>

本文示範如何使用SharePoint Add-in的 chrome 控制項。為下一個步驟中，您可了解可用於SharePoint Add-ins其他 UX 元件。若要深入了解，請參閱下列各項：
  
    
    

-  [程式碼範例： 雲端託管增益集使用的 chrome 控制項](http://code.msdn.microsoft.com/SharePoint-2013-Work-with-089ecc6f)
    
  
-  [程式碼範例： 使用的 chrome 控制項與跨網域文件庫 (CSOM)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)
    
  
-  [程式碼範例： 使用的 chrome 控制項與跨網域文件庫 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)
    
  
-  [使用 SharePoint 網站的樣式表中 SharePoint 增益集](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)
    
  
-  [建立部署與 SharePoint 增益集的自訂動作](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)
    
  
-  [建立增益集組件安裝與您 SharePoint 的增益集](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## 其他資源
<a name="SP15Usechromecontrol_Addresources"> </a>


-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 增益集 UX 設計的指導方針](sharepoint-add-ins-ux-design-guidelines.md)
    
  
-  [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

