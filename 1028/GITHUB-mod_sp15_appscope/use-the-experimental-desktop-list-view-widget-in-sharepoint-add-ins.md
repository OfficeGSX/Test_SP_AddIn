---
title: 使用實驗桌面清單檢視 widget 中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: b1e26543-9d03-4759-80bf-e6ce729a1b19
---


# 使用實驗桌面清單檢視 widget 中 SharePoint 增益集
即使頁面不架設在SharePoint了解如何使用桌面清單檢視 widget 在任何網頁。用於您的增益集的清單檢視 widget 架設SharePoint網站中的清單中顯示資料。
> **注意事項**
> Office Web Widget - Experimental 只提供進行研究 (英文) 和意見反應。不在實際執行案例中使用。Office Web Widget 行為可能會大幅在未來的版本中變更。閱讀並檢閱 [Office Web Widget 實驗的授權條款](office-web-widgetsexperimental-license-terms.md)。
  
    
    

您可以使用清單檢視 widget 類似規則的清單檢視 widget、 SharePoint清單中顯示資料，但您可以使用它在您的增益集和不一定是架設在SharePoint中的網站。
**圖 1。桌面] 清單中顯示資料的清單檢視 widget**

  
    
    

  
    
    
![頁面上的桌面清單檢視實驗控制項](images/DesktopListView_basic.png)
  
    
    

  
    
    

  
    
    

## 簡介

您可以指定檢視的SharePoint清單中，您想要用於顯示資料。清單檢視 widget 會檢視所指定的順序顯示的欄和項目。
  
    
    
清單檢視 widget 取得清單資料使用的跨網域文件庫。基於此通訊發生在用戶端層級。
  
    
    

> **注意**
> 桌面清單檢視 widget 不會啟用原生清單檢視中的所有的案例。
  
    
    

在目前版本的 widget 尚未啟用下列案例：
  
    
    

- 使用 widget 上原本不支援的跨網域文件庫的驗證配置。
    
  
- 使用 widget SharePoint清單或文件庫以外的資料來源。
    
  
- 資料繫結 widget。
    
  
- 使用者種易於觸控檢視。
    
  
- 內嵌編輯的使用者。
    
  
- 顯示目前狀態資訊。
    
  
- 提供自訂的轉譯範本。
    
  
- 內部部署案例。此刻、 widget 僅適用於SharePoint Online。
    
  

## 此範例使用本文中的先決條件

若要遵循本文中的範例，您需要下列項目：
  
    
    

- Visual Studio 2013
    
  
- NuGet 封裝管理員。如需詳細資訊，請參閱 [安裝 NuGet](http://go.microsoft.com/fwlink/?LinkId=271465)。
    
  
- SharePoint 2013開發環境 (隔離應用程式所需的內部部署案例)。
    
  
- Office Web Widget-實驗 NuGet 套件。如需如何安裝 NuGet 套件的詳細資訊，請參閱 [使用對話方塊管理 NuGet 套件](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog)。您也可以瀏覽 [NuGet 圖庫] 頁面上](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)。
    
  

## 提供者主控SharePoint Add-in中使用清單檢視中的桌面 widget

在這個範例中，有主控以外的宣告清單檢視中的桌面 widget SharePoint簡單] 頁面。
  
    
    
若要使用的清單檢視 widget，您必須執行下列動作：
  
    
    

- 建立SharePoint Add-in和 web 的專案。
    
  
- 建立增益集在 web 上的清單。此步驟也可確保使用者部署的增益集時建立的增益集網頁。
    
    > **注意事項**
      > 跨網域文件庫要求增益集 web 存在。清單檢視 widget 會與SharePoint通訊所使用的跨網域文件庫。
- 建立增益集] 頁面上的宣告使用 HTML 標記的清單檢視 widget 執行個體。
    
  

### 若要建立SharePoint Add-in和 web 的專案


1. 系統管理員身分開啟 Visual Studio 2013。(若要這樣做，選擇 [ **開始**] 功能表上的 Visual Studio 2013 圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立新專案使用 **SharePoint Add-in 2013年**範本。SharePoint Add-in 2013年範本位於 [ **範本**] 下 > **Visual C#** **Office /SharePoint**> **增益集**。
    
  
3. 提供您想要用於偵錯SharePoint網站 URL。
    
  
4. 選取 [ **提供者主控**為增益集的主控選項。
    
    > **注意事項**
      > 您也可以使用桌面清單檢視 widget 搭配其他主控選項或甚至是 Office 增益集或您自己的網站。
5. 選取 [ **ASP.NET Web 表單應用程式**的 web 應用程式專案類型。
    
  
6. 選取 [ **Windows Azure Access Control Service** ] 為 [驗證] 選項。
    
  

### 若要建立增益集在 web 上的清單


1. 選擇 [ **方案總管**中的 [ SharePoint Add-in專案]。選擇 [ **新增**> **新增項目**
    
  
2. 選擇 [ **Visual C# 的項目**> **Office /SharePoint**> **清單**。在 [ **名稱**] 文字方塊中輸入 **宣告** 。選擇 [ **新增**]。
    
  
3. 選擇 [ **建立清單執行個體根據現有的清單範本**。選擇 [ **宣告**] 範本。選擇 [ **完成**]。
    
  

### 若要新增使用桌面清單檢視 widget 的新頁面


1. 選擇 [ **頁面**] 資料夾中的 **方案總管**中的 web 專案。
    
  
2. 下列程式碼複製並貼入 **ASPX** 檔案中專案中。程式碼會執行下列工作：
    
  - 新增所需的 Office 文件庫及資源的參照。
    
  
  - 提供清單檢視 widget 版面配置區。
    
  
  - 初始化控制項執行階段。
    
  
  - 執行 Office 控制項執行階段的 **renderAll**方法。
    
  

  ```
  
<!DOCTYPE html>
<html>
<head>
    <!-- IE9 or superior -->
    <meta http-equiv="x-ua-compatible" content="IE=10">
    <title>Desktop List View HTML Markup</title>

    <!-- Controls Specific CSS File -->
    <link rel="stylesheet" type="text/css" href="/Scripts/Office.Controls.css" />

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
        Type.registerNamespace("Office.Samples.ListViewBasic");
        //Retrieve context tokens from the querystring
        Office.Samples.ListViewBasic.appWebUrl =
            decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));
        Office.Samples.ListViewBasic.hostWebUrl =
            decodeURIComponent(getQueryStringParameter("SPHostUrl"));
        Office.Samples.ListViewBasic.ctag =
            decodeURIComponent(getQueryStringParameter("SPClientTag"));

        //Pattern to dynamically load JSOM and the cross-domain library
        var scriptbase =
            Office.Samples.ListViewBasic.hostWebUrl + "/_layouts/15/";

        //Get the cross-domain library
        $.getScript(scriptbase + "SP.RequestExecutor.js", 
            //Get the Office controls runtime and 
            //  continue to the createControl function
            function () {
                $.getScript("../Scripts/Office.Controls.js", createControl);
            }
        );
    </script>

    <!-- List View -->
    <script 
        src="../Scripts/Office.Controls.ListView.debug.js" 
        type="text/javascript">
    </script>

    <!-- SharePoint CSS -->
    <script type="text/javascript">
        document.addEventListener("DOMContentLoaded", function () {
            // The resource files are in a URL in the form:
            // web_url/_layouts/15/Resource.ashx
            var scriptbase =
                Office.Samples.ListViewBasic.appWebUrl + "/_layouts/15/";

            // Dynamically create the invisible iframe.
            var blankiframe;
            var blankurl;
            var body;
            blankurl =
                Office.Samples.ListViewBasic.appWebUrl + "/Pages/blank.html";
            blankiframe = document.createElement("iframe");
            blankiframe.setAttribute("src", blankurl);
            blankiframe.setAttribute("style", "display: none");
            body = document.getElementsByTagName("body");
            body[0].appendChild(blankiframe);

            // Dynamically create the link element.
            var dclink;
            var head;
            dclink = document.createElement("link");
            dclink.setAttribute("rel", "stylesheet");
            dclink.setAttribute("href",
                                scriptbase +
                                "defaultcss.ashx?ctag=" +
                                Office.Samples.ListViewBasic.ctag
                                );
            head = document.getElementsByTagName("head");
            head[0].appendChild(dclink);
        }, false);
    </script>
</head>
<body>
    Basic List View sample (HTML markup declaration):
    <div id="ListViewDiv"
         data-office-control="Office.Controls.ListView"
         data-office-options='{"listUrl" : getListUrl()}'>
    </div>

    <script type="text/javascript">
        function createControl() {
            //Initialize Controls Runtime
            Office.Controls.Runtime.initialize({
                sharePointHostUrl: Office.Samples.ListViewBasic.hostWebUrl,
                appWebUrl: Office.Samples.ListViewBasic.appWebUrl
            });

            //Render the widget, this must be executed after the
            //placeholder DOM is loaded
            Office.Controls.Runtime.renderAll();
        }

        function getListUrl() {
            return Office.Samples.ListViewBasic.appWebUrl +
                    "/_api/web/lists/getbytitle('Announcements')";
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
    
  
您也可以下載此範例從程式碼組件庫，請參閱 [使用增益集在清單檢視中的桌面實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)程式碼範例。
  
    
    

## 

本文說明如何使用桌面清單檢視 widget 在增益集中使用 HTML。您也可以探索下列案例和相關 widget 的詳細資訊。
  
    
    

### 使用JavaScript宣告桌面清單檢視 widget

您偏好您可能會想要用於JavaScript而不是 HTML 宣告 widget。如果這是您可以使用下列標記作為版面配置區 widget 大小寫。
  
    
    

```HTML

<div id="ListViewDiv"></div>
```

使用下列JavaScript程式碼產生清單檢視中。
  
    
    



```
new Office.Controls.ListView(
    document.getElementById("ListViewDiv"), {
        listUrl: Office.Samples.ListViewBasic.appWebUrl + "/_api/web/lists/getbytitle('Announcements')"
    });
```

如需範例顯示如何執行工作，請參閱 [使用增益集在清單檢視中的桌面實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)程式碼範例中的 [ **JSSimple.html** ] 頁面。
  
    
    

### 指定檢視以顯示資料

您可以指定存在的檢視中SharePoint清單、 清單檢視 widget 會顯示使用檢視規格的資料。
  
    
    
如果您使用 HTML 標記宣告 widget，您可以使用下列語法來指定一種檢視。
  
    
    



```

<div id="ListViewDiv"
        data-office-control="Office.Controls.ListView"
        data-office-options="{listUrl: 'list URL',
                            viewID: 'GUID'
                            }">
</div> 

```

如果您正在宣告使用JavaScriptwidget，請使用下列語法來指定一種檢視。
  
    
    



```

new Office.Controls.ListView(
    document.getElementById("ListViewDiv"), {
        listUrl: "list URL",
        viewID: "GUID"
    });
```


## 總結

您可以使用實驗桌面清單檢視 widget SharePoint清單中顯示資料。Widget 會以唯讀模式顯示資料。請提供意見和 [Office 開發人員平台 UserVoice 網站](http://officespdev.uservoice.com/)中的註解。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [Office Web Widget 實驗性的概觀](office-web-widgetsexperimental-overview.md)
    
  
-  [Office Web Widget 實驗的授權條款](office-web-widgetsexperimental-license-terms.md)
    
  
-  [Office Web Widget 實驗 NuGet 圖庫] 頁面上](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)
    
  
-  [程式碼範例： 使用增益集清單檢視中的桌面實驗 widget](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)
    
  
-  [使用實驗桌面清單檢視 widget 中 SharePoint 增益集](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md)
    
  

