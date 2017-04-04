---
title: 查詢使用 SharePoint 2013 中的 web proxy 遠端服務
ms.prod: SHAREPOINT
ms.assetid: 16913e6d-4fc6-4c5e-84a4-6c2688703798
---



# 查詢使用 SharePoint 2013 中的 web proxy 遠端服務
了解如何從架設在SharePoint 2013所使用的 web proxy] 頁面存取遠端網域中的資料。
當您在建置SharePoint Add-ins時，您通常必須併入各種來源的資料。基於安全性考量，有封鎖機制，以防止跨網域的通訊。當您使用的 web proxy 時，在增益集中網頁可存取遠端網域及 SharePoint 網域中的資料。
  
    
    

身為開發人員，您可以使用用戶端 api (英文)，例如JavaScript和.NET 用戶端物件模型中公開的 web proxy。當您使用的 web proxy 時，您會發出 sharepoint 初始要求。接著，SharePoint 會要求指定的結束點的資料，將您] 頁面上的回應。當您想在伺服器層級進行通訊時使用的 web proxy。如需詳細資訊，請參閱 [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)。
**SharePoint Web Proxy 是您的程式碼與外部資料來源之間的中介人**

  
    
    

  
    
    
![「程式碼」、「SharePoint Web Proxy」及「資料來源」符號顯示資料要求通過 Web Proxy。](images/3fbdcfae-6af9-452b-9a07-48575de7e86a.png)
  
    
    

  
    
    

  
    
    

## 此範例使用本文中的先決條件
<a name="SP15Queryremoteservice_Prereq"> </a>

若要遵循此範例中的步驟，您需要下列項目：
  
    
    

-  [Visual Studio 2015 and the latest Microsoft Office Developer Tools](https://www.visualstudio.com/features/office-tools-vs.aspx)
    
  
- SharePoint 2013開發環境 (增益集隔離所需的內部部署案例)
    
  

### 若要使用的 web proxy 前需要瞭解的核心概念

下表列出一些實用的文章可協助您了解內SharePoint Add-ins跨網域案例的相關概念。
  
    
    

**表 1。Web proxy 的核心概念**


|**文章標題**|**描述**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |了解新增益集模型中的全部SharePoint 2013可讓您建立增益集，亦即使用者的小型、 易於使用的解決方案。 <br/> |
| [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> |了解資料SharePoint Add-ins存取選項。本文提供高階的替代項目必須可以使用在增益集中的資料時選擇的指導。 <br/> |
| [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |了解主機 web 及增益集 web 之間的差異。深入了解哪些SharePoint 2013元件還包含在SharePoint Add-in、 哪些元件部署到主機網站、 哪些元件部署到增益集 web 及增益集 web 隔離網域中的部署方式。 <br/> |
| [用戶端的跨網域安全性](http://msdn.microsoft.com/en-us/library/cc709423%28v=vs.85%29.aspx) <br/> |探索跨網域威脅使用情況下，跨原點要求的安全性原則和衡量適用於開發人員增強從瀏覽器中執行的 web 應用程式的跨網域存取的風險。 <br/> |
   

## 程式碼範例： 存取遠端服務使用的 web proxy 中的資料
<a name="SP15Queryremoteservice_Codeexample"> </a>

若要從遠端服務讀取資料，您必須執行下列動作：
  
    
    

1. 建立SharePoint Add-in專案。
    
  
2. 修改要查詢之遠端服務使用的 web proxy 的 **Default.aspx**頁面。
    
  
3. 修改的增益集資訊清單以允許遠端網域的通訊。
    
  
圖 1 顯示的 SharePoint 網頁瀏覽器視窗之遠端服務的資料。
  
    
    

**圖 1。從遠端服務資料的 SharePoint 網頁**

  
    
    

  
    
    
![SharePoint page with data from the remote service](images/WebProxy_result.png)
  
    
    

### 若要建立 SharePoint 增益集專案


1. 系統管理員身分開啟 2015年。(若要這樣做，以滑鼠右鍵按一下 [ **開始**] 功能表中的 2015年圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立新專案使用 **SharePoint 增益集**範本
    
    圖 2 顯示 2015、 **範本**、 **Visual C#**、 **Office/SharePoint**、 **Office 增益集**下 **SharePoint 增益集**範本的位置。
    

   **圖 2。Visual Studio 範本 SharePoint 增益集**

  

     ![App for SharePoint 2013 Visual Studio template](images/AppForSharePointVSTemplate.PNG)
  

  

  
3. 提供您想要用於偵錯之 SharePoint 網站的 URL。
    
  
4. 選取 **SharePoint 主控**做為增益集的主控選項。
    
  

### 若要修改的 Default.aspx 頁面來使用的 web proxy JavaScript物件模型


1. 按兩下 [ **頁面**] 資料夾中 **的 Default.aspx**頁面。
    
  
2. 複製下列標記並將其貼在 **PlaceHolderMain**內容標籤] 頁面中。標記會執行下列工作：
    
  - 提供遠端資料版面配置區。
    
  
  - 參照 SharePoint JavaScript檔案。
    
  
  - 準備 **WebRequestInfo**物件的要求。
    
  
  - 準備要JavaScript Object Notation (JSON)格式指定回應的要求 **Accept**標頭。
    
  
  - 發出呼叫的遠端端點。
    
  
  - 處理成功完成，呈現在 SharePoint 網頁中的遠端資料。
    
  
  - 處理呈現 SharePoint 網頁中的錯誤訊息的任何錯誤。
    
  

  ```
  
Categories from the Northwind database exposed as an OData service:
    
<!-- Placeholder for the remote content -->
<span id="categories"></span>

<!-- Add references to the JavaScript libraries. -->
<script 
    type="text/javascript" 
    src="../_layouts/15/SP.Runtime.js">
</script>
<script 
    type="text/javascript" 
    src="../_layouts/15/SP.js">
</script>
<script type="text/javascript">
(function () {
    "use strict";

    // Prepare the request to an OData source
    // using the GET verb.
    var context = SP.ClientContext.get_current();
    var request = new SP.WebRequestInfo();
    request.set_url(
        "http://services.odata.org/Northwind/Northwind.svc/Categories"
        );
    request.set_method("GET");

    // We need the response formatted as JSON.
    request.set_headers({ "Accept": "application/json;odata=verbose" });
    var response = SP.WebProxy.invoke(context, request);

    // Let users know that there is some
    // processing going on.
    document.getElementById("categories").innerHTML =
                "<P>Loading categories...</P>";

    // Set the event handlers and invoke the request.
    context.executeQueryAsync(successHandler, errorHandler);

    // Event handler for the success event.
    // Get the totalResults node in the response.
    // Render the value in the placeholder.
    function successHandler() {

        // Check for status code == 200
        // Some other status codes, such as 302 redirect
        // do not trigger the errorHandler. 
        if (response.get_statusCode() == 200) {
            var categories;
            var output;

            // Load the OData source from the response.
            categories = JSON.parse(response.get_body());

            // Extract the CategoryName and Description
            // from each result in the response.
            // Build the output as a list.
            output = "<UL>";
            for (var i = 0; i < categories.d.results.length; i++) {
                var categoryName;
                var description;
                categoryName = categories.d.results[i].CategoryName;
                description = categories.d.results[i].Description;
                output += "<LI>" + categoryName + ":&amp;nbsp;" +
                    description + "</LI>";
            }
            output += "</UL>";

            document.getElementById("categories").innerHTML = output;
        }
        else {
            var errordesc;

            errordesc = "<P>Status code: " +
                response.get_statusCode() + "<br/>";
            errordesc += response.get_body();
            document.getElementById("categories").innerHTML = errordesc;
        }
    }

    // Event handler for the error event.
    // Render the response body in the placeholder.
    // The body includes the error message.
    function errorHandler() {
        document.getElementById("categories").innerHTML =
            response.get_body();
    }
})();
</script>
  ```


### (選用)若要修改的 Default.aspx 頁面來使用的 web proxy REST 端點


1. 按兩下 [ **頁面**] 資料夾中 **的 Default.aspx**頁面。
    
  
2. 複製下列標記並將其貼在 **PlaceHolderMain**內容標籤] 頁面中。標記會執行下列工作：
    
  - 提供遠端資料版面配置區。
    
  
  - 參照的 jQuery 文件庫。
    
  
  - 準備 **SP.WebRequest.Invoke**端點的要求。
    
  
  - 準備本文的 **SP.WebrequestInfo**物件的要求。物件會包含指定回應JavaScript Object Notation (JSON)格式 **Accept**標頭。
    
  
  - 發出呼叫的遠端端點。
    
  
  - 處理成功完成，呈現在 SharePoint 網頁中的遠端資料。
    
  
  - 處理呈現 SharePoint 網頁中的錯誤訊息的任何錯誤。
    
  

  ```
  
Categories from the Northwind database exposed as an OData service:
    
<!-- Placeholder for the remote content -->
<span id="categories"></span>

<script 
    type="text/javascript" 
    src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.min.js">
</script>

<script type="text/javascript">
(function () {
    "use strict";

    // The Northwind categories endpoint.
    var url =
        "http://services.odata.org/Northwind/Northwind.svc/Categories";

    // Let users know that there is some
    // processing going on.
    document.getElementById("categories").innerHTML =
                "<P>Loading categories...</P>";

    // Issue a POST request to the SP.WebProxy.Invoke endpoint.
    // The body has the information to issue a GET request
    // to the Northwind service.
    $.ajax({
        url: "../_api/SP.WebProxy.invoke",
        type: "POST",
        data: JSON.stringify(
            {
                "requestInfo": {
                    "__metadata": { "type": "SP.WebRequestInfo" },
                    "Url": url,
                    "Method": "GET",
                    "Headers": {
                        "results": [{
                            "__metadata": { "type": "SP.KeyValue" },
                            "Key": "Accept",
                            "Value": "application/json;odata=verbose",
                            "ValueType": "Edm.String"
                        }]
                    }
                }
            }),
        headers: {
            "Accept": "application/json;odata=verbose",
            "Content-Type": "application/json;odata=verbose",
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: successHandler,
        error: errorHandler
    });

    // Event handler for the success event.
    // Get the totalResults node in the response.
    // Render the value in the placeholder.
    function successHandler(data) {
        // Check for status code == 200
        // Some other status codes, such as 302 redirect,
        // do not trigger the errorHandler. 
        if (data.d.Invoke.StatusCode == 200) {
            var categories;
            var output;

            // Load the OData source from the response.
            categories = JSON.parse(data.d.Invoke.Body);

            // Extract the CategoryName and Description
            // from each result in the response.
            // Build the output as a list
            output = "<UL>";
            for (var i = 0; i < categories.d.results.length; i++) {
                var categoryName;
                var description;
                categoryName = categories.d.results[i].CategoryName;
                description = categories.d.results[i].Description;
                output += "<LI>" + categoryName + ":&amp;nbsp;" +
                    description + "</LI>";
            }
            output += "</UL>";

            document.getElementById("categories").innerHTML = output;
        }
        else {
            var errordesc;

            errordesc = "<P>Status code: " +
                data.d.Invoke.StatusCode + "<br/>";
            errordesc += response.get_body();
            document.getElementById("categories").innerHTML = errordesc;
        }
    }

    // Event handler for the error event.
    // Render the response body in the placeholder.
    // The 2nd argument includes the error message.
    function errorHandler() {
        document.getElementById("categories").innerHTML =
            arguments[2];
    }
})();
</script>

  ```


### 若要編輯的增益集資訊清單檔案


1. 在 **方案總管**] 中開啟 **AppManifest.xml**檔案的捷徑功能表並選擇 [ **檢視程式碼**。
    
  
2. 將複製的下列 **RemoteEndPoints**定義的子 **App**節點。
    
  ```XML
  
<RemoteEndpoints>
    <RemoteEndpoint Url=" http://services.odata.org" />
</RemoteEndpoints>
  ```


    **RemoteEndpoint**元素用來指定遠端網域。Web proxy 驗證發行給遠端網域的要求會宣告在增益集資訊清單中。您可以在 **RemoteEndpoints**元素中建立最多 20 個項目。僅限授權組件會被視為; `http://domain:port`及 `http://domain:port/website`會視為相同端點。您可以使用只是一個 **RemoteEndpoint** 定義發行到相同網域內的許多不同端點的通話。
    
  

### 若要建置和執行解決方案


1. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio建置解決方案、 增益集、 部署和增益集的權限] 頁面會隨即開啟。
2. 選擇 [ **信任它**] 按鈕。
    
  
3. 按一下 [網站內容] 頁面中的 [增益集] 圖示。
    
    圖 3 是遠端資料之 SharePoint 網頁中。
    

   **圖 3。在 SharePoint 網頁中的遠端資料**

  

     ![SharePoint page with data from the remote service](images/WebProxy_result.png)
  

  

  

**表 2。解決方案的疑難排解**


|**問題**|**解決方法**|
|:-----|:-----|
|Visual Studio 無法開啟瀏覽器之後按 F5 鍵。 <br/> |將SharePoint Add-in專案設為啟動專案。 <br/> |
|不支援配置連接埠組合。 <br/> |通話結構描述連接埠組合必須落在下列準則： <br/> |**配置**|**通訊埠**|
|:-----|:-----|
|http <br/> |80 <br/> |
|https <br/> |443 <br/> |
|http 或 https <br/> |7000 10000 <br/> |
   

> **重要**
> 輸出的連接埠會受限於主機防火牆可用性。特別是只有 http 80 和 https 443 上可用SharePoint Online。
  
    
    

|
|**SP 未定義** 處理的例外狀況。 <br/> |請確定您就可以存取 SP.在瀏覽器視窗中的 RequestExecutor.js 檔案。 <br/> 如果您使用您的本機伺服器作為您的開發環境，您必須停用 IIS 回送檢查。從Windows PowerShell命令提示字元中執行下列命令。 <br/> ```New-ItemProperty HKLM:\\System\\CurrentControlSet\\Control\\Lsa -Name "DisableLoopbackCheck" -value "1" -PropertyType dword```> **注意**> 停用 IIS 回送] 核取不建議在生產環境中。          |
|從遠端端點回應大小超過設定的限制。 <br/> |不得大於 200 KB 的 web proxy 要求的回應大小。 <br/> |
   

## 後續步驟
<a name="SP15Queryremoteservice_Next"> </a>

本文示範如何從 SharePoint 網頁讀取遠端服務中的資料。為下一個步驟中，您可了解其他資料存取SharePoint Add-ins中可用的選項。若要深入了解，請參閱下列各項：
  
    
    

-  [程式碼範例： 取得資料從遠端服務使用的 web proxy](http://code.msdn.microsoft.com/SharePoint-2013-Get-data-705bdcd5)
    
  
-  [SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [操作方法： 存取外部資料與 SharePoint 2013 的 REST](http://msdn.microsoft.com/library/0663cc8c-a736-434d-9858-6ce12ce7f748%28Office.15%29.aspx)
    
  

## 其他資源
<a name="SP15Queryremoteservice_Addresources"> </a>


-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [在 SharePoint 其餘邀請中使用 OData 查詢作業](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [資料儲存在 SharePoint 增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data)
    
  
