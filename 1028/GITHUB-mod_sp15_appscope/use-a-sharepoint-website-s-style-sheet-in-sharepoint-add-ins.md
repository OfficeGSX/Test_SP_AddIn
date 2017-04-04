---
title: 使用 SharePoint 網站的樣式表中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 25d84ac5-d2b3-40c7-962d-1408aacf14ed
---


# 使用 SharePoint 網站的樣式表中 SharePoint 增益集
了解如何使用SharePoint Add-inSharePoint網站的樣式表。
您可以在您SharePoint Add-in參照樣式表的 SharePoint 網站及用它來樣式使用樣式表SharePoint中您的網頁。此外，如果某人變更 SharePoint 網站的樣式表或佈景主題，您可以採用一組新的樣式在增益集中而不修改在增益集中的樣式表參考 (英文)。
  
    
    


> **重要**
> 如果您的網頁使用的 chrome 控制項或增益集主版頁面、 樣式已可供您使用，且您不需要使用本文中的程序以手動方式參照樣式表。
  
    
    


## 此範例使用本文中的先決條件
<a name="SP15Usestylesheetcontrol_Prereq"> </a>

您必須在開發環境 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)所述。
  
    
    

### 了解SharePoint Add-in中使用 SharePoint 樣式表之前的核心概念

下表列出可協助您了解使用 SharePoint 樣式表案例的相關概念的實用文章。
  
    
    

**表 1。使用樣式表之前的核心概念**


|**文章標題**|**描述**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |了解新增益集模型中的全部SharePoint可讓您建立增益集，亦即使用者的小型、 易於使用的解決方案。 <br/> |
| [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md) <br/> |了解使用者經驗 (UX) 選項以及您必須建立SharePoint Add-ins時的替代方法。 <br/> |
| [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |了解主機 web 及增益集 web 之間的差異。深入了解哪些SharePoint元件還包含在SharePoint Add-in、 哪些元件部署到主機網站、 哪些元件部署到增益集 web 及增益集 web 隔離網域中的部署方式。 <br/> |
   

## 程式碼範例： 使用 SharePoint 網站的樣式表中SharePoint Add-in
<a name="SP15Usestylesheetcontrol_Example"> </a>

這個程式碼範例會示範如何使用 SharePoint 網站的樣式表。這可讓您以符合外觀及操作 SharePoint 主機 web 頁面的遠端 web 應用程式頁面。
  
    
    
若要使用的樣式表SharePoint Add-in中，執行下列步驟：
  
    
    

1. 建立提供者主控SharePoint Add-in.
    
  
2. 強制增益集 web 佈建所建立的空白頁面。
    
  
3. 將網頁新增至 web 專案，並參照樣式表。
    
  
4. 編輯增益集資訊清單中的項目。
    
  
圖 1 顯示 SharePoint 網頁所使用的樣式表。
  
    
    

**圖 1。使用樣式表的網頁**

  
    
    

  
    
    
![A web page using the stylesheet control](images/StylesheetControl_result.png)
  
    
    

### 若要建立的 SharePoint 增益集和遠端 web 專案


1. 系統管理員身分開啟Visual Studio 。(若要這樣做，以滑鼠右鍵按一下 [ **開始**] 功能表上的 [ Visual Studio ] 圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立提供者主控SharePoint Add-in [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)所述並將其命名StylesheetAdd 中。
    
  

### 若要強制所建立的空白頁面增益集 web 佈建


1. 以滑鼠右鍵按一下SharePoint Add-in專案，並新增新的模組。
    
  
2. 以滑鼠右鍵按一下新的模組，並新增新的項目。
    
  
3. 在 [ **Visual C# 的項目**、 **Web**、 選擇 **HTML 網頁**。重新命名 **blank.html** ] 頁面。
    
  
4. 刪除 blank.html 的內容。
    
  

### 若要新增網頁會參考 web 專案中的樣式表


1. 以滑鼠右鍵按一下 [web 專案，並新增新的 Web 表單。重新命名 Web 表單至 **StyleConsumer.aspx** 。
    
  
2. 取代下列程式碼 Web 表單.aspx 檔案的內容。程式碼會執行下列工作：
    
  - 會從增益集網路中不可見的 IFrame 載入 blank.html 頁面。
    
  
  - 會從增益集 web 載入 defaultcss.ashx 檔案。
    
  
  - 使用可用的樣式。
    
  

  ```
  
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="StyleConsumer.aspx.cs" Inherits="StylesheetAppWeb.StyleConsumer" %>

<!DOCTYPE html>
<html>
<head>
    <title>Add-in using stylesheet</title>
</head>
<body>

    <!-- The main page title -->
    <h1 class="ms-core-pageTitle">Stylesheet add-in</h1>

    <!-- Some subtitle -->
    <h1 class="ms-accentText">For people</h1>

    <!-- Subtitle comments -->
    <h2 class="ms-accentText">who care about the style in their add-ins</h2>
    <p></p>
    <div>
        <h2 class="ms-webpart-titleText">Get started with style in your add-in... </h2>
        <a class="ms-commandLink" href="#">some command</a>
        <br />
        This sample shows you how to use some of the classes defined in the SharePoint website's style sheet.
    </div>

    <!-- Script to load SharePoint resources
        and load the blank.html page in
        the invisible iframe
        -->
    <script type="text/javascript">
        "use strict";
        var appweburl;

        (function () {
            var ctag;

            // Get the URI decoded add-in web URL.
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );
            // Get the ctag from the SPClientTag token.
            ctag =
                decodeURIComponent(
                    getQueryStringParameter("SPClientTag")
            );

            // The resource files are in a URL in the form:
            // web_url/_layouts/15/Resource.ashx
            var scriptbase = appweburl + "/_layouts/15/";

            // Dynamically create the invisible iframe.
            var blankiframe;
            var blankurl;
            var body;
            blankurl = appweburl + "/Pages/blank.html";
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
            dclink.setAttribute("href", scriptbase + "defaultcss.ashx?ctag=" + ctag);
            head = document.getElementsByTagName("head");
            head[0].appendChild(dclink);
        })();

        // Function to retrieve a query string value.
        // For production purposes you may want to use
        //  a library to handle the query string.
        function getQueryStringParameter(paramToRetrieve) {
            var params;
            var strParams;

            params = document.URL.split("?")[1].split("&amp;");
            strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
                var singleParam = params[i].split("=");
                if (singleParam[0] == paramToRetrieve)
                    return singleParam[1];
            }
        }
    </script>
</body>
</html>

  ```


    在某些情況下，使用者有之前您] 頁面上將能夠下載 CSS 和圖像的樣式設定為 SharePoint 驗證。連結標籤不會自動驗證尚無已登入的使用者。請考慮從增益集網路中強制執行之前將連結的 CSS 檔案至使用者的驗證您網頁載入頁面資源。在這個範例中，看不見 IFrame 中載入 blank.html 頁面。
    
  

### 若要編輯的增益集資訊清單中的開始頁面元素


1. 按兩下 [ **方案總管**中的 **AppManifest.xml**檔案。
    
  
2. 按一下 [ **開始] 頁面上**按下拉式] 功能表選擇 [使用樣式表網頁]。
    
  

### 若要建置和執行解決方案


1. 請確定SharePoint Add-in專案已設為啟動專案。
    
  
2. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio建置解決方案、 增益集、 部署和增益集的權限] 頁面會隨即開啟。
3. 選擇 [ **信任它**] 按鈕。
    
  
4. 按一下 [ **StylesheetBasic**增益集] 圖示。
    
  
5. 圖 2 顯示 [結果] 網頁中使用 SharePoint 樣式。
    
   **圖 2。使用頁面中的樣式表**

  

     ![Stylesheet control used in a web page](images/StylesheetControl_result2.png)
  

  

  
6. 您也可以移至的主機網站，並變更佈景主題。然後重新載入增益集] 網頁以使用新的樣式。
    
  

**表 2。解決方案的疑難排解**


|**問題**|**解決方法**|
|:-----|:-----|
|Visual Studio無法開啟瀏覽器之後按 F5 鍵。 <br/> |將SharePoint Add-in專案設為啟動專案。 <br/> |
|[憑證錯誤。 <br/> |將 web 專案 **已啟用 SSL** 屬性設定為 false。在SharePoint Add-in專案，將 **Web 專案** 屬性設定為None，然後再屬性設定回 web 專案的名稱。 <br/> |
   

## 後續步驟
<a name="SP15Usestylesheetcontrol_Nextsteps"> </a>

本文示範如何使用SharePoint Add-in樣式表。為下一個步驟中，您可了解可用於SharePoint Add-ins其他 UX 元件。若要深入了解，請參閱下列各項：
  
    
    

-  [程式碼範例： 使用 SharePoint 樣式表增益集](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-7a8684e2)
    
  
-  [使用用戶端的 chrome 控制項中 SharePoint 增益集](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [建立部署與 SharePoint 增益集的自訂動作](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)
    
  
-  [建立增益集組件安裝與您 SharePoint 的增益集](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## 其他資源
<a name="SP15Usestylesheetcontrol_Addresources"> </a>


-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 增益集 UX 設計的指導方針](sharepoint-add-ins-ux-design-guidelines.md)
    
  
-  [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

