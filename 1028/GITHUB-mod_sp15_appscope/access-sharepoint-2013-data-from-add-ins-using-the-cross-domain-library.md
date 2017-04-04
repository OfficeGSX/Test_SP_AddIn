---
title: 存取 SharePoint 2013 資料增益集使用跨網域文件庫
ms.prod: SHAREPOINT
ms.assetid: bc37ff5c-1285-40af-98ae-01286696242d
---



# 存取 SharePoint 2013 資料增益集使用跨網域文件庫
瞭解如何使用SharePoint 2013跨網域文件庫，從 [增益集存取SharePoint網站中的資料。
當您SharePoint Add-ins時，您通常只有併入來自各種來源的資料。但的 [安全性考量](http://msdn.microsoft.com/library/cc709423.aspx)，會封鎖避免一次使用一個以上的網域通訊的機制。在大部分的瀏覽器，讓難以或無法完成各網域的用戶端通話中實作這些安全機制。
  
    
    

圖 1 跨網域顯示的 [封鎖的要求。
**圖 1。在網域的 [封鎖的要求**

  
    
    
當使用者要求頁面從您增益集的網域 (1) 時，用戶端通訊結合只有該網域。增益集可以發行只以相同的網域中的其他資源的用戶端來電] 頁面。不過，增益集通常需要從其他網域，例如SharePoint網域，以滿足其案例的資源。在您的網頁程式碼，您可以嘗試發出要求，在瀏覽器會封鎖SharePoint網域 (2)。通常，您會看到 「 **拒絕存取** 」 錯誤。您沒有權限要求的資源但很可能是您無法甚至發出要求，所述的資源，並不會暗示錯誤。當您使用的跨網域文件庫時，在 [增益集網頁可以存取您增益集的網域與SharePoint網域中的資料。跨網域文件庫 (SP.是用戶端替代的JavaScript檔案格式RequestExecutor.js) 的裝載於SharePoint網站，您可以參照遠端增益集。跨網域文件庫，可讓您與多個網域，在 [遠端增益集] 頁面透過 proxy 互動。如果您喜歡在伺服器上，而不是在用戶端上執行增益集程式碼，並且連線障礙，例如SharePoint與遠端基礎結構之間的防火牆，如果是很好的選項。您可以存取主機網頁版中的資料，例如，您可以存取使用者互動的清單，無論增益集。或您可以存取增益集網頁版中的資料，例如清單特別佈建您增益集的增益集也可以存取其他網站集合和網站，只要增益集擁有 [租用戶範圍的權限及部署為使用增益集類別目錄的批次安裝。
> [!注意事項]
> 在本主題中， **新增的網域** 是指裝載增益集頁面的網域。這可能是該網域的遠端代管提供者，但增益集的頁面中的應用程式也能夠在SharePoint增益集 web 以及撥打主機 web 網域。在第二個案例中，新增的網域是增益集網站的網域。
  
    
    

本文中的主要範例會示範如何建立增益集的讀取增益集在網頁上的資料，並顯示在網頁。[ [後續步驟](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Next)] 區段會顯示更多建立主範例上方的案例。
## 必要條件使用本文中的範例
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

若要依照本文中的範例，您需要下列項目:
  
    
    

-  [Visual Studio 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30682)
    
  
-  [Microsoft Office for Visual Studio 2012 的開發人員工具](https://msdn.microsoft.com/en-us/office/aa905340.aspx)
    
  
- SharePoint 2013開發環境 (內部部署案例所需的應用程式隔離)
    
  

## 閱讀增益集網路使用的跨網域文件庫上的資料
<a name="SP15Accessdatafromremoteapp_Codeexample"> </a>

在此範例中，有SharePoint讀取SharePoint網站 (增益集網站) 中的資料使用代表性狀態轉接 (剩餘) 端點的外部裝載的簡單頁面。跨網域文件庫需要增益集網頁，因為這麼做，啟動與這種情況。
  
    
    
若要閱讀的增益集的資料，您必須執行下列動作:
  
    
    

1. 建立SharePoint Add-in和 web 專案。
    
  
2. 在 [增益集網頁版中建立清單項目。此步驟也可確保使用者部署增益集時，會建立增益集網頁。
    
  
3. 建立使用跨網域文件庫讀取清單項目增益集頁面。
    
  
圖 2 顯示網頁的增益集在網頁上顯示的資料。
  
    
    

**圖 2。增益集在網頁顯示的資料的網頁**

  
    
    

  
    
    
![Cross domain read items sample result screen](images/CrossDomainReadItemsResult.png)
  
    
    

### 建立SharePoint Add-in和 web 專案


1. 以系統管理員身分開啟Visual Studio 2012 。(以執行此動作，以滑鼠右鍵按一下 [ **開始**] 功能表上的 [ Visual Studio 2012 ] 圖示，並選擇 [ **以系統管理員身分執行**]。)
    
  
2. 建立新的專案使用 **增益集 SharePoint 2013** ] 範本。
    
    **增益集 SharePoint 2013**中的範本Visual Studio 2012位於 **範本** **>** **Visual C#**， **Office SharePoint** **>** **增益集**] 底下。
    
  
3. 提供您想要使用的偵錯的 SharePoint 網站 URL。
    
  
4. 選取 [ **提供者主控**為增益集的主機服務選項。
    
    > [!注意事項]
      > 您也可以使用SharePoint 裝載的增益集跨網域文件庫。不過，在SharePoint 裝載的增益集增益集] 頁面已在增益集網站，它不需要跨網域文件庫的大小寫的讀取清單項目。如SharePoint 裝載的增益集樣本讀取主機網頁版中的資料，請參閱 [使用跨網域文件庫中的 SharePoint 裝載增益集 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)或請參閱 [從主 web access 資料](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)本文稍後的。

### 在 [增益集網站上建立清單項目


1. 以滑鼠右鍵按一下SharePoint Add-in專案 **總管**] 中。選擇 [ **新增** **>** **新項目**
    
  
2. 選擇 **Visual C# 項目** **>** **Office/SharePoint** **>** **清單**。若要 **宣告** 設定清單的名稱。
    
  
3. 按兩下 [ **宣告** **>** **Elements.xml**。貼上下列的 XML 節點 **ListInstance** 項目的子項。
    
  ```
  
<Data>
    <Rows>
        <Row>
            <Field Name="Title">Lorem ipsum 1</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
        <Row>
            <Field Name="Title">Lorem ipsum 2</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
    </Rows>
</Data>
  ```


### 若要新增頁面使用的跨網域文件庫


1. 按兩下 **Default.aspx** web 專案 **總管**] 中。
    
  
2. 複製下列程式碼，並將其貼 Default.aspx 檔案。程式碼會執行下列工作:
    
  - 從 Microsoft CDN 載入 jQuery 文件庫。
    
  
  - 提供版面配置區的結果。
    
  
  - 從查詢字串中擷取的增益集的 web URL。
    
  
  - 載入跨網域文件庫JavaScript jQuery 中使用 **getScript**函數。
    
    函數載入所需的資源，然後再繼續指定的函數，確保跨網域文件庫的載入，並可供使用後續的程式碼。
    
  
  - 產生 **RequestExecutor**物件。根據預設，RequestExecutor 使用增益集網頁內容網站。
    
    > [!注意事項]
      > 您可以變更不同於增益集網站其他網站的內容網站使用 **AppContextSite**結束點 (剩餘) 或物件 (JSOM)。若要進一步瞭解 AppContextSite，請參閱本文稍後的 [從主 web access 資料](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)。
  - 問題清單項目結束點的其餘部分呼叫。
    
  
  - 處理順利完成，在網頁上顯示的清單項目。
    
  
  - 處理網頁上顯示錯誤訊息的錯誤。
    
  

  ```
  
<html>
    <head>
        <title>Cross-domain sample</title>
    </head>
    <body>
        <!-- This is the placeholder for the announcements -->
        <div id="renderAnnouncements"></div>
        <script 
            type="text/javascript" 
            src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
        </script>
        <script type="text/javascript">
          var hostweburl;
          var appweburl;

          // Load the required SharePoint libraries
          $(document).ready(function () {
            //Get the URI decoded URLs.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );

            // resources are in URLs in the form:
            // web_url/_layouts/15/resource
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js files and continue to the successHandler
            $.getScript(scriptbase + "SP.RequestExecutor.js", execCrossDomainRequest);
          });

          // Function to prepare and issue the request to get
          //  SharePoint data
          function execCrossDomainRequest() {
            // executor: The RequestExecutor object
            // Initialize the RequestExecutor with the add-in web URL.
            var executor = new SP.RequestExecutor(appweburl);

            // Issue the call against the add-in web.
            // To get the title using REST we can hit the endpoint:
            //      appweburl/_api/web/lists/getbytitle('listname')/items
            // The response formats the data in the JSON format.
            // The functions successHandler and errorHandler attend the
            //      sucess and error events respectively.
            executor.executeAsync(
                {
                  url:
                      appweburl +
                      "/_api/web/lists/getbytitle('Announcements')/items",
                  method: "GET",
                  headers: { "Accept": "application/json; odata=verbose" },
                  success: successHandler,
                  error: errorHandler
                }
            );
          }

          // Function to handle the success event.
          // Prints the data to the page.
          function successHandler(data) {
            var jsonObject = JSON.parse(data.body);
            var announcementsHTML = "";

            var results = jsonObject.d.results;
            for (var i = 0; i < results.length; i++) {
              announcementsHTML = announcementsHTML +
                  "<p><h1>" + results[i].Title +
                  "</h1>" + results[i].Body +
                  "</p><hr>";
            }

            document.getElementById("renderAnnouncements").innerHTML =
                announcementsHTML;
          }

          // Function to handle the error event.
          // Prints the error message to the page.
          function errorHandler(data, errorCode, errorMessage) {
            document.getElementById("renderAnnouncements").innerText =
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
    </body>
</html>
  ```


### 若要建立並執行方案


1. 按 F5 鍵。
    
    > [!注意事項]
      > 當您按 F5 時， Visual Studio建置解決方案部署增益集與增益集的權限頁面隨即會開啟。
2. 選擇 [ **允許存取**] 按鈕。
    
  
3. 選擇 [ **網站內容**] 頁面上的 [增益集] 圖示。
    
  
如果您偏好可下載的程式碼範例，您可以取得此程式碼圖庫中的項目。 **程式碼範例: 使用跨網域文件庫，以取得清單項目** 使用 [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)或 [SharePoint-新增-中-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)。
  
    
    

**資料表 2。疑難排解解決方案**


|**如果您看到...**|**然後嘗試...**|
|:-----|:-----|
|錯誤訊息: 很抱歉，我們有一些無法存取您的網站。 <br/> 也有按鈕，以修正錯誤，但它不會修正問題。 <br/> |您也可以按已知的問題與 Internet Explorer 中的安全性區域，請參閱 [跨不同的 Internet Explorer 安全性區域中 SharePoint 增益集使用的跨網域文件庫](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)。 <br/> |
|錯誤訊息: 您的瀏覽器不支援必要的功能。請確定您使用的 IE 8 或上方，或其他現代瀏覽器。請確定的 「 X UA-相容 ' 中繼標籤設定為 'IE = 8' 或上方。 <br/> |跨網域文件庫需要 **IE8** 或以上的文件模式。在某些情況下，文件模式是設為 **IE7** 預設。您可以使用 Internet Explorer 開發人員工具來決定，以及變更頁面的文件模式。如需詳細資訊，請參閱 [定義文件相容性](http://msdn.microsoft.com/library/cc288325.aspx)。 <br/> |
|錯誤訊息: 「 類型 」 未定義。 <br/> 此外，如果增益集使用JavaScript物件模型 (JSOM)。 <br/> |JSOM Microsoft Ajax 文件庫中使用 **Type.registerNamespace**方法，註冊 **預存程序** 命名空間。使用下列程式碼新增至 Microsoft Ajax 文件庫的參照，從您的頁面: <br/> ```HTML<script type="text/javascript"  src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js"></script>```|
   

## 後續步驟
<a name="SP15Accessdatafromremoteapp_Next"> </a>

本文將示範如何查詢增益集網站讀取資料SharePoint上使用的不裝載的增益集頁面的其餘部分端點。您也可以瀏覽下列案例與跨網域文件庫的詳細資料。
  
    
    

### 使用 JSOM 資料的增益集
<a name="SP15Accessdatafromremoteapp_JSOM"> </a>

根據您的喜好設定，您可能會想要的增益集查詢資料使用 JSOM，而不是其餘。您必須完成其他工作，若要使用 JSOM 跨網域文件庫:
  
    
    

- 參考SharePoint JSOM 在 [增益集] 頁面。
    
  
- 初始化 **ProxyWebRequestExecutorFactory**的物件，然後將其設為內容物件的工廠。
    
  
- 存取SharePoint物件讀取清單中的資料。
    
  
- 載入內容中的物件，然後執行查詢。
    
  
顯示如何執行工作的程式碼範例，請參閱 [SharePoint-新增-中-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)。如需有關如何使用 JSOM 的詳細資訊，請參閱 [使用 JavaScript 物件模型 (JSOM) 在 SharePoint 增益集](http://blogs.msdn.com/b/officeapps/archive/2012/09/04/using-the-javascript-object-model-jsom-in-apps-for-sharepoint.aspx)。
  
    
    

### 從主 web access 資料
<a name="SP15Accessdatafromremoteapp_Hostweb"> </a>

此頁面中的範例會示範如何讀取增益集網頁版中的資料。此運作正常啟動範例的因為跨網域文件庫最初使用增益集內容網站。然而，有許多情況下，您要存取主機網站上的資料的位置。有幾項工作所需存取主機網站上的資料:
  
    
    

- 設定主機 web 內容網站跨網域文件庫。
    
  
- 提供適當的權限增益集。
    
  
您可以變更的內容網站使用 **AppContextSite**結束點 (剩餘) 或物件 (JSOM)。下列範例會示範如何變更內容的網站其餘結束點:
  
    
    



```

executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/title?@target='" +
            hostweburl + "'",
        method: "GET",
        headers: { "Accept": "application/json; odata=verbose" },
        success: successHandler,
        error: errorHandler
    }
);
```

下列範例會示範如何變更 JSOM 內容網站:
  
    
    



```

context = new SP.ClientContext(appweburl);
factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
context.set_webRequestExecutorFactory(factory);
appContextSite = new SP.AppContextSite(context, hostweburl);

this.web = appContextSite.get_web();
context.load(this.web);
```

根據預設，增益集有增益集網站，但無法以主機網站的權限。下列範例顯示宣告讀取主機網頁版中的資料的權限要求的資訊清單區段:
  
    
    



```XML

<AppPermissionRequests>
    <AppPermissionRequest 
        Scope="http://sharepoint/content/sitecollection/web" 
        Right="Read" />
</AppPermissionRequests>
```

請確定 (例如的空白頁面或清單) 增益集在網頁上建立的資源，若要強制佈建增益集網站，這樣才能使用跨網域文件庫。
  
    
    

### 跨網站集合的 access 資料
<a name="SP15Accessdatafromremoteapp_TenantScope"> </a>

與跨網域文件庫中，您可以在相同的租用戶的網站集合存取資料。有幾項工作，您必須完成整個網站集合存取資料:
  
    
    

- 新增的權限要求存取租用戶中的資料。
    
  
- 在程式碼，切換至您想要查詢的網站集合的內容網站。
    
  
- 增益集新增到增益集目錄]。
    
  
- 為租用戶範圍增益集網站部署增益集。如需如何部署 as 租用戶範圍增益集的範例，請參閱 [使用跨網域文件庫的租用戶範圍增益集 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)程式碼範例的描述。
    
  
增益集也必須權限來存取資料的租用戶。下列範例顯示宣告讀取資料的租用戶的權限要求的資訊清單區段:
  
    
    



```XML

<AppPermissionRequests>
  <AppPermissionRequest 
    Scope="http://sharepoint/content/tenant" 
    Right="Read" />
</AppPermissionRequests>
```

若要切換程式碼中的內容網站，請使用 **AppContextSite**端點 (剩餘) 或物件 (JSOM)，就像 [從主 web access 資料](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)] 區段中。以下是其他端點的提醒: /_api/SP.AppContextSite(@target)/web/title? @ 目標 = 'weburl'，及如何產生中 JSOM 物件範例:  `appContextSite = new SP.AppContextSite(context, weburl);`。
  
    
    
為開發人員]，您可以只部署租用戶範圍增益集的增益集類別目錄。您可以提供您的內部部署或SharePoint Online增益集目錄環境。上傳的增益集增益集類別目錄是非常簡單上, 傳檔案至文件庫。如需詳細說明，請參閱 [新增自訂的增益集增益集類別目錄網站](http://office.microsoft.com/en-us/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx) 。
  
    
    
從增益集目錄可以部署增益集，租用戶中的一或多個網站。因為增益集有權限存取租用戶中的資料，您僅有一個網站來存取資料整個承租人部署。如需如何部署增益集增益集類別目錄中的指示，請參閱 [部署自訂的增益集](http://office.microsoft.com/en-us/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx) 。
  
    
    
若要下載的程式碼範例示範如何在網站集合存取資料，請參閱 [使用跨網域文件庫的租用戶範圍增益集 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)。
  
    
    

### 發行不同的安全性區域之間的通話
<a name="SP15Accessdatafromremoteapp_IEZones"> </a>

跨網域文件庫使用的裝載在進行通訊的 [增益集] 頁面上 **IFrame** proxy 頁面。當增益集頁面和 SharePoint 網站不同的安全性區域中，無法傳送授權 cookie。如果不有任何授權 cookie IFrame 嘗試載入 proxy] 頁面上，會重新導向至 SharePoint 登入頁面。SharePoint 登入頁面無法內的 IFrame 基於安全性理由。在下列情況下，文件庫無法載入 proxy] 頁面上，並不能與 SharePoint 的通訊。
  
    
    
不過，就是這些案例的解決方案。解決方法是 **apphost 圖樣** ，其中包含在 [文繞圖在裝載於增益集網站頁面中的 [增益集] 頁面。最好使用 apphost 模式中使用跨網域文件庫的增益集，即使沒有明顯的安全性界限。如需詳細資訊，請參閱 [跨不同的 Internet Explorer 安全性區域中 SharePoint 增益集使用的跨網域文件庫](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)。
  
    
    

### 從其他遠端主機SharePoint 裝載的增益集中存取資料
<a name="SP15Accessdatafromremoteapp_SPhosted"> </a>

根據預設， SharePoint 裝載的增益集允許議題跨網域的來電至] 主機網站，前提是必須適當的權限。不過， SharePoint 裝載的增益集也可以指定遠端主機其 **AppPrincipal** **AllowedRemoteHostUrl**屬性中。此有效可讓您發出跨網域呼叫的增益集，並從另一個主機其他位置。
  
    
    
若要下載的SharePoint 裝載的增益集使用跨網域文件庫的範例，請參閱 [程式碼範例: 使用跨網域文件庫中的 SharePoint 裝載增益集 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)。
  
    
    

## 其他資源
<a name="SP15Accessdatafromremoteapp_Addresources"> </a>


-  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)
    
  
-  [SharePoint-新增-中-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)
    
  
-  [程式碼範例: 使用跨網域文件庫 (REST) web 標題取得主機](http://code.msdn.microsoft.com/SharePoint-2013-Get-the-0ec36bb6)
    
  
-  [程式碼範例: 取得主機網頁標題使用跨網域文件庫 (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)
    
  
-  [程式碼範例: 使用跨網域文件庫中的 SharePoint 裝載增益集 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)
    
  
-  [程式碼範例: 使用跨網域文件庫的租用戶範圍增益集 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)
    
  
-  [程式碼範例: 使用 chrome 的控制項與跨網域文件庫 (剩餘)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)
    
  
-  [程式碼範例: 使用 chrome 的控制項與跨網域文件庫 (JSOM)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)
    
  
-  [程式碼範例: 使用自訂動作] 和 [訂單書籍跨網域文件庫](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)
    
  
-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [跨不同的 Internet Explorer 安全性區域中 SharePoint 增益集使用的跨網域文件庫](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  
-  [SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [查詢使用 SharePoint 2013 中的 web proxy 遠端服務](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)
    
  
-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
