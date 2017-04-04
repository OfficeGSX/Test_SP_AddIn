---
title: SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫
ms.prod: SHAREPOINT
ms.assetid: f3f87cdf-5cbf-47c9-9ce1-1ab65cd598de
---


# SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫
了解如何使用SharePoint的跨網域文件庫來存取資料的遠端服務建立自訂的 proxy] 頁面上的 SharePoint 網頁。
當您在建置SharePoint Add-ins時，您通常必須併入各種來源的資料。不過，基於安全性考量，有封鎖機制，以防止多個網域與一次的通訊。
  
    
    

您可以使用的跨網域文件庫如果要提供自訂 proxy] 頁面上必需遠端的增益集基礎結構中的存取在遠端增益集中的資料。為開發人員，您負責實作自訂的 proxy] 頁面上，且如何處理自訂邏輯，例如遠端增益集所驗證機制的跨網域文件庫與自訂 proxy 頁面如果您想使用用戶端層級進行通訊。
## 此範例使用本文中的先決條件
<a name="SP15Createcustomproxypage_Prereq"> </a>

您必須在開發環境 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)所述。
  
    
    

### 了解之前SharePoint Add-ins搭配使用自訂的 proxy] 頁面上的核心概念

下表列出一些實用的文章可協助您了解之SharePoint Add-ins跨網域案例的相關概念。
  
    
    

**表 1。使用自訂的 proxy] 頁面上的核心概念**


|**文章標題**|**描述**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |了解新增益集模型中的全部SharePoint可讓您建立增益集，亦即使用者的小型、 易於使用的解決方案。 <br/> |
| [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> |了解資料SharePoint Add-ins存取選項。本主題提供高階的替代項目必須可以使用在增益集中的資料時選擇的指導。 <br/> |
| [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |了解主機 web 及增益集 web 之間的差異。深入了解哪些SharePoint元件還包含在SharePoint Add-in、 哪些元件部署到主機網站、 哪些元件部署到增益集 web 及增益集 web 隔離網域中的部署方式。 <br/> |
| [用戶端的跨網域安全性](http://msdn.microsoft.com/en-us/library/cc709423%28v=vs.85%29.aspx) <br/> |探索跨網域威脅跨原點要求的使用情況下及安全性原則和衡量適用於開發人員增強從瀏覽器中執行的 web 應用程式的跨網域存取的風險。 <br/> |
   

## 程式碼範例： 存取跨網域文件庫使用自訂的 proxy] 頁面上的遠端資料
<a name="SP15Createcustomproxypage_Codeexample"> </a>

若要從遠端服務讀取資料，您必須執行下列動作：
  
    
    

1. 建立SharePoint Add-in專案。
    
  
2. 修改允許從遠端的增益集通訊的增益集資訊清單。
    
  
3. Web 專案中建立自訂的 proxy 頁面和內容頁面。
    
  
4. 建立使用SharePoint Add-in專案中的跨網域文件庫的頁面。
    
  

### 若要建立 SharePoint 增益集專案


1. 系統管理員身分開啟Visual Studio 。(若要這樣做，以滑鼠右鍵按一下 [ **開始**] 功能表上的 [ Visual Studio ] 圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立提供者主控SharePoint Add-in [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)所述，並命名ProxyPageApp。
    
  

### 若要編輯的增益集資訊清單檔案


1. 在 **方案總管**] 中以滑鼠右鍵按一下 [ **AppManifest.xml**的檔案，並選擇 [ **檢視程式碼**。
    
  
2. 以下列取代整個 **AppPrincipal**元素。
    
  ```XML
  
<AppPrincipal>
    <Internal AllowedRemoteHostUrl="~remoteAppUrl"/>
</AppPrincipal>
  ```


    > **注意事項**
      > **AllowedRemoteHostUrl**屬性用來指定遠端網域。 **~remoteAppUrl**解析為遠端增益集 (英文) URL。如需憑證的詳細資訊，請參閱 [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)。

### 若要建立自訂的 proxy] 頁面


1. 已建立Visual Studio解決方案之後，以滑鼠右鍵按一下 [web 應用程式專案 (非SharePoint Add-in專案) 並選擇 [ **新增]**新增新的 Web 表單 > **新增項目**> **Web** > **Web 表單**。名稱表單Proxy.aspx。
    
  
2. Proxy.aspx 檔案並中取代整個 html 元素且其具有下列 HTML 程式碼的子項。讓上面的 html 元素的所有標記都保持原狀。HTML 程式碼包含標記和JavaScript執行下列工作：
    
  - 提供跨網域文件庫JavaScript檔案版面配置區。
    
  
  - 擷取的查閱者增益集 web URL。
    
  
  - 動態跨網域文件庫JavaScript檔案載入版面配置區。
    
  
  - 提供 **RequestExecutorMessageProcessor**物件的設定。
    
  
  - 初始化 **RequestExecutorMessageProcessor**物件。
    
  

  ```HTML
  
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <meta http-equiv="X-UA-Compatible" content="IE=8" /> 
    <title>Custom Proxy Host Page</title>
    <script 
        src="http://ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js" 
        type="text/javascript">
    </script>
    <script 
        type="text/javascript" 
        src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
    </script>

    <!-- Script to load the cross-domain library js file -->
    <script type="text/javascript">
        var hostweburl;

        $(document).ready(function(){
            //Get the URI decoded host web URL.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );

            // The cross-domain js file is in a URL in the form:
            // host_web_url/_layouts/15/SP.RequestExecutor.js
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js file 
            $.getScript(scriptbase + "SP.RequestExecutor.js", initCustomProxy);
        });

        //Function to initialize the custom proxy page
        //  must set the appropriate settings and implement
        //  proper authentication mechanism
        function initCustomProxy() {
            var settings =
            {
                originAuthorityValidator: function (messageOriginAuthority) {
                    // This page must implement the authentication for the
                    //   remote add-in.
                       // You should validate if messageOriginAuthority is
                       //  an approved domain to receive calls from.
                    return true;
                }
            };
            SP.RequestExecutorMessageProcessor.init(settings);
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
<body>
    
</body>
</html>


  ```


    > **重要**
      > 在實際執行SharePoint Add-in，您必須提供授權邏輯並設定 **originAuthorityValidator**物件中傳回適當的值。

### 若要建立的內容] 頁面


1. 以滑鼠右鍵按一下 [ **方案總管**中的 web 應用程式專案，然後選擇 [ **新增]**新增新的 Web 表單 > **新增項目**> **Web** > **Web 表單**。名稱表單Content.aspx.
    
  
2. 下列程式碼複製並貼入 **Page_Load**方法中的程式碼後置檔案中。程式碼會執行下列工作：
    
  - 將輸出 **content-type**設為 **text/plain**。
    
  
  - 寫入輸出緩衝區中的內容。
    
  
  - 結束的連線。
    
  

  ```cs
  
string content;
content = "Just some text.";
Response.ContentType="text/plain";
Response.Write(content);
Response.End();

  ```


### 建立 SharePoint 網頁所使用的跨網域文件庫


1. 以滑鼠右鍵按一下SharePoint Add-in專案，並選擇 [ **新增]** > **新增項目**> **Office/SharePoint** > **模組**。
    
  
2. 命名模組頁面，然後選擇 [ **新增]**。
    
  
3. 以滑鼠右鍵按一下 [ **頁面**] 資料夾並選擇 [ **新增]** > **新增項目**> **Office/SharePoint** > **] 頁面**。
    
  
4. 命名頁面Home.aspx ，然後選擇 [ **新增]**。
    
  
5. 開啟 **Home.aspx**頁面如果它不會自動開啟。
    
  
6. 下列程式碼複製並將其貼在 **PlaceHolderMain**內容標籤中。
    
  ```
  
<!-- The page dynamically loads the cross-domain library's
    js file, rescript acts as the placeholder. -->
<script 
    type="text/javascript"
    id="rescript"
    src="../_layouts/15/SP.RequestExecutor.js">
</script>
    Data from the remote domain: <span id="TextData"></span>

    <!-- Main script to retrieve the host web's title -->
    <script type="text/javascript">
    (function () {
        var executor;
        var hostweburl;
        var remotedomain;

        remotedomain = "<your_remote_add-in_domain>";

        //Get the URI decoded host web URL.
        hostweburl =
            decodeURIComponent(
                getQueryStringParameter("SPHostUrl")
        );

        // Initialize the RequestExecutor with the custom proxy URL.
        executor = new SP.RequestExecutor(remotedomain);
        executor.iFrameSourceUrl = "Proxy.aspx?SPHostUrl=" + hostweburl;

        // Issue the call against the remote endpoint.
        // The response formats the data in plain text.
        // The functions successHandler and errorHandler attend the
        //      sucess and error events respectively.
        executor.executeAsync(
            {
                url:
                    remotedomain + "Content.aspx",
                method: "GET",
                headers: { "Accept": "text/plain" },
                success: successHandler,
                error: errorHandler
            }
        );
    })();

    // Function to handle the success event.
    // Prints the data to the placeholder.
    function successHandler(data) {
        document.getElementById("TextData").innerText =
            data.body;
    }

    // Function to handle the error event.
    // Prints the error message to the page.
    function errorHandler(data, errorCode, errorMessage) {
        document.getElementById("TextData").innerText =
            "Could not complete cross-domain call: " + errorMessage;
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

  ```

7. 在上面的程式碼的您貼上，尋找列 `remotedomain = "<your_remote_add-in_domain>";`，並以 web 應用程式時執行的增益集 F5 Visual Studio中所使用的"localhost"URL 取代預留位置 _<your_remote_add-in_domain>_ 。若要尋找此值，請選取 [ **方案總管**中的 web 應用程式專案。 **URL**屬性會在 [ **內容**] 窗格中。使用整個值，包括通訊協定、 連接埠，並結束正斜線和;例如 「 http://localhost:45072。 」
    
  
8. 儲存後關閉檔案。
    
  
9. 開啟 appmanifest.xml 檔案，並將 **起始畫面**的值設為 **ProxyPageApp/Pages/Home.aspx**。
    
  

### 若要建置和執行解決方案


1. 請確定SharePoint Add-in專案已設為啟動專案。
    
  
2. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio建置解決方案、 增益集、 部署和增益集的權限] 頁面會隨即開啟。
3. 選擇 [ **信任它**] 按鈕。
    
    [首頁] 頁面上將會開啟並看起來應該類似如下。可能需要幾秒鐘的片語"只是一些文字"看起來因為會從遠端網域的 Content.aspx 頁面正在擷取。
    

   **在 SharePoint 網頁中的遠端服務的資料**

  

     ![SharePoint page with data from a remote service](images/CustomProxy_result.jpg)
  

  

  

**表 2。解決方案的疑難排解**


|**問題**|**解決方法**|
|:-----|:-----|
|Visual Studio無法開啟瀏覽器之後按 F5 鍵。 <br/> |將SharePoint Add-in專案設為啟動專案。 <br/> |
|**SP 未定義** 處理的例外狀況。 <br/> |請確定您可以存取 SP.在瀏覽器視窗中的 RequestExecutor.js 檔案。 <br/> |
   

## 後續步驟
<a name="SP15Createcustomproxypage_Nextsteps"> </a>

本文示範如何使用自訂的 proxy] 頁面上SharePoint的跨網域文件庫的存取遠端資料。為下一個步驟中，您可了解其他資料SharePoint Add-ins中可用的存取選項。若要深入了解，請參閱下列各項：
  
    
    

-  [程式碼範例： 使用 proxy] 頁面上的跨網域文件庫中取得資料](http://code.msdn.microsoft.com/SharePoint-2013-Get-data-10039ff1)
    
  
-  [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [查詢使用 SharePoint 2013 中的 web proxy 遠端服務](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)
    
  

## 其他資源
<a name="SP15Createcustomproxypage_Addresources"> </a>


-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [在 SharePoint 其餘邀請中使用 OData 查詢作業](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [資料儲存在 SharePoint 增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data)
    
  

