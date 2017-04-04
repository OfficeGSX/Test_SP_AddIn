---
title: 使用 SharePoint 2013 其餘端點完成基本作業
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# 使用 SharePoint 2013 其餘端點完成基本作業
瞭解如何執行基本建立、 讀取、 更新及刪除 (CRUD) 作業SharePoint 2013其餘介面。
## 開發使用SharePoint用戶端 Api 及其餘
<a name="ClientAPIs"> </a>

您可以執行基本建立、 讀取、 更新及刪除 (CRUD) 作業使用SharePoint 2013所提供的代表性狀態轉接 （剩餘） 介面。其餘介面公開的所有SharePoint項目和所提供的其他SharePoint用戶端 Api 的作業。使用其餘的其中一個優點是，您不需要新增任何SharePoint 2013文件庫或用戶端的組件的參照。不過，您對 HTTP 要求適當的端點來擷取或更新SharePoint項目，例如網站、 清單及清單項目。請參閱 [若要了解 SharePoint 2013 REST 服務取得](get-to-know-the-sharepoint-2013-rest-service.md)SharePoint 2013其餘介面和其架構的完整簡介。
  
    
    
 [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md) ，並 [使用資料夾與其餘的檔案](working-with-folders-and-files-with-rest.md)詳細說明如何使用核心SharePoint項目。說明如何執行這些作業環境中撰寫 C# ASP.NET web 應用程式的範例，請參閱 [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations) 。
  
    
    
如需可用SharePoint 2013平台上的 api 集的詳細資訊，請參閱 [選擇 [設定 SharePoint 2013 中的右 API](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。瞭解如何使用用戶端 Api 的資訊，請參閱 [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)， [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)，並 [建置存取 SharePoint 2013 的 Windows Phone 應用程式](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)。
  
    
    

## HTTP SharePoint 2013其餘服務中的作業
<a name="HTTPOps"> </a>

SharePoint 2013其餘服務中的端點會對應到類型與SharePoint用戶端物件模型中的成員。藉由使用 HTTP 要求，您可以使用這些其餘端點執行一般 CRUD （ **Create**、 **Read**、 **Update**及 **Delete**） 作業SharePoint項目，例如清單和網站。
  
    
    
一般而言，結束點代表 **Read**作業地圖 HTTP **GET**命令。代表更新作業的結束點對應到 HTTP **POST**命令，然後結束點代表更新或插入 HTTP **PUT**命令的操作對應。
  
    
    
在SharePoint 2013，使用 **POST**建立項目，例如清單和網站。SharePoint 2013其餘服務支援包含要代表集合的結束點的物件定義的傳送 **POST**命令。例如，您可能會傳送 **POST**命令 ATOM 下列 url，以建立SharePoint清單中包含新清單物件定義 ︰
  
    
    
 `http://<site url>/_api/web/lists`
  
    
    
 **POST**作業，不是必要的任何屬性設定為預設值。如果您嘗試將 [唯讀] 屬性設定為 **POST**作業的一部分，服務會傳回例外狀況。
  
    
    
若要更新現有的SharePoint物件使用 **PUT**和 **MERGE**作業。代表物件屬性 **set**作業的任何服務端點支援 **PUT**要求和 **MERGE**要求。 **MERGE**要求，若要設定屬性是選擇性;您不明確設定的任何內容保留目前的屬性。不過， **PUT**命令，您不明確設定的任何屬性已設為預設的屬性。此外，如果您不指定所有必要的屬性物件更新中使用 HTTP **PUT**命令時，其餘服務會傳回例外狀況。
  
    
    
使用 [HTTP **DELETE**命令針對特定的端點 URL 若要刪除端點表示SharePoint物件。若是可回收的物件，例如清單、 檔案和清單項目，如此 **Recycle**作業。
  
    
    

## 讀取SharePoint 2013其餘介面的資料
<a name="ReadingData"> </a>

若要使用內建SharePoint 2013其餘功能，您建構 RESTful HTTP 邀請中，使用 OData 標準，其會對應到您想要使用的用戶端物件模型 API。每個SharePoint實體會在您的目標，它的中繼資料中和是 XML 或 JSON 格式的SharePoint 2013網站上的端點公開顯示。您可以在任何語言，包括但不是限於JavaScript及 C# 進行 HTTP 要求。
  
    
    
若要從其餘的端點讀取資訊，您必須知道兩個端點的 URL 及SharePoint實體在該端點公開的 OData 表示。比方說，如果您要擷取所有的清單中的特定SharePoint網站，您必須進行 `http://<site url>/_api/web/lists`的 **GET**要求。您可以在瀏覽器中瀏覽至 [此 URL，並查看 XML 傳回。當您要求的程式碼中，您可以指定是否要在 XML 或 JSON 收到清單的 OData 表示。
  
    
    
下列 C# 程式碼將示範如何進行此 **GET**要求的使用 JQuery 傳回的所有網站的清單 JSON 表示。也會假設您有一個有效的 OAuth 存取權杖 **accessToken**變數中所儲存的。您不需要存取權杖如果您從這個呼叫內增益集網頁一樣SharePoint-裝載您無法從瀏覽器用戶端執行的程式碼來取得存取權杖的增益集筆記。您必須從伺服器執行的程式碼，以取得存取權杖。 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)與 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)會說明如何取得存取權杖。
  
    
    



```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

如果您是在JavaScript撰寫增益集，但使用SharePoint 2013跨網域文件庫，這項要求看起來稍有不同。在此情況下，您不需要提供存取權杖。下列程式碼會示範如何此要求想看起來，如果您使用跨網域文件庫，並想將接收清單的 OData 表示為 XML，而不是 JSON。（Atom 是預設的回應格式，因為您沒有要包含在 **Accept**標頭。）請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)如需有關使用跨網域文件庫。
  
    
    



```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);
```

下列範例中的程式碼會顯示如何使用 C# 要求在網站的清單中的所有的 JSON 表示法。假設您有您儲存在 `accessToken`變數 OAuth 存取權杖。
  
    
    



```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();

```


### 取得無法傳回與該資源的屬性
<a name="NavigationProperties"> </a>

多個屬性值傳回時擷取的資源，但您必須針對某些屬性， **GET**要求直接傳送到屬性結束點。這是一般表示SharePoint實體的屬性。
  
    
    
下列範例會示範如何取得屬性，藉由附加到資源端點的 [屬性名稱。此範例會從 **File**資源取得 **Author**屬性的值。
  
    
    
http:// _<site url>_/_api/web/getfilebyserverrelativeurl('/ _<folder name>_/ _<file name>_')/author
  
    
    
若要取得結果 JSON 格式，包括設定為 [  `"application/json;odata=verbose"` **Accept**頁首。
  
    
    

## 使用其餘介面來撰寫資料
<a name="WritingData"> </a>

您可以建立並更新SharePoint實體建構 RESTful HTTP 要求適當的端點，就像您平常您正在讀取的資料。主要的差異，不過，是您使用的 **POST**要求。當您要更新的實體時，也會傳遞 **PUT**或 **MERGE** HTTP 要求方法這些字詞的其中一個為 **X-HTTP-Method**索引鍵的值新增至您要求的標題。 **MERGE**方法，更新您指定，而 **PUT**方法會取代現有的實體使用新的 **POST**本文中所提供的實體屬性。若要刪除的實體使用 **DELETE**方法。當您建立或更新的實體時，您必須提供您要建立或變更您的 HTTP 邀請內文中的實體 OData 表示。
  
    
    
另一個重要考量時建立、 更新和刪除SharePoint實體，如果您不使用 OAuth 授權您的要求，這些作業需要伺服器的要求表單摘要值為 **X-RequestDigest**標頭的值。您可以進行 `http://<site url>/_api/contextinfo`具有空白的本文的 **POST**要求並擷取 `d:FormDigestValue`中節點的 XML **contextinfo**端點傳回的值，以擷取此值。下列範例會顯示在 C# **contextinfo**端點 HTTP 要求。
  
    
    



```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

如果您使用 [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)所述的驗證及授權流程，您不需要在邀請中包含的要求摘要。
  
    
    
如果您使用的JavaScript跨網域文件庫，SP.RequestExecutor 處理取得，並為您傳送表單摘要值。
  
    
    
如果您要建立SharePoint-裝載SharePoint Add-in，您不需要個別 HTTP 要求擷取表單摘要值。不過，您可以從SharePoint （如果網頁使用預設主版頁面） 頁面，擷取JavaScript程式碼中的值如下圖所示，在下列範例中，使用 JQuery，並建立清單。
  
    
    



```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});


```

下列範例會示範如何更新在上一個範例中所建立的清單。在變更標題的清單、 使用 JQuery，假設您在進行這項作業中SharePoint-裝載增益集。
  
    
    



```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});

```

 **IF-MATCH**機碼要求的標頭中的值是您指定的清單或清單項目 **etag**值的位置。此特定值僅適用於清單及清單項目，它可能是可協助您避免並行問題，當您更新這些項目。上一個範例針對此值，請使用星號 （*），然後每當您沒有安裝任何原因擔心並行問題時，您可以使用的值。否則，您應該執行擷取實體的 **GET**要求取得 **etag**值或清單或清單項目。回應標頭的 HTTP 回應的結果會將 etag 傳遞為 **ETag**索引鍵的值。此值也會包含在實體中繼資料。下列範例所示開啟 `<entry>`標記包含清單資訊的 XML 節點。 **m:etag**屬性包含 **etag**值。
  
    
    



```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">
```


## 建立與其他的網站
<a name="bk_CreateSite"> </a>

下列範例會示範如何建立JavaScript的網站。
  
    
    

```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});
```


## 由環境的其餘部分要求有何不同
<a name="bk_HowRequestsDiffer"> </a>

建立和傳送 HTTP 要求會因語言、 文件庫和增益集類型，因此您通常需要變更一或多個要求元件，當您要翻譯另一個環境要求到另一個。例如，jQuery AJAX 要求使用 **data** ，並指定邀請本文並類型，但跨網域文件庫要求 **type**參數使用 **body**和 **method**參數來指定這些值。
  
    
    
下列各節說明環境的其他常見的差異。
  
    
    

### 取得及傳送表單摘要值方式取決於增益集
<a name="FormDigest"> </a>

當您傳送的文章要求時，要求 **X-RequestDigest**標頭中必須包含表單摘要值。不過，您取得的方式傳送值相差增益集 ︰
  
    
    

- 在SharePoint-裝載增益集，您就可以將傳遞下列標頭 ︰ 
  
    
    
 「 X RequestDigest 」: $("#__REQUESTDIGEST").val()
    
  
- 在雲端裝載的增益集使用 OAuth 先將要求傳送給 **contextinfo**端點，擷取表單摘要值，然後將其新增至邀請，如下所示 [其餘介面來撰寫資料](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData)。
    
  
- 在雲端裝載的增益集使用JavaScript跨網域文件庫，您不需要指定表單摘要值。根據預設，SP.RequestExecutor 自動這會為您處理。（也處理內容長度值。）
    
  

### 使用 OAuth 的增益集必須經過要求存取權杖
<a name="OAuthTokens"> </a>

雲端裝載增益集使用 OAuth 或跨網域文件庫授權SharePoint資料的存取權。在 [遠端網頁伺服器上執行的程式碼的增益集元件必須使用 OAuth 授權SharePoint資料的存取權。在此情況下，您必須包含要傳送的存取權杖 **Authorization**標頭。如需 **HTTPWebRequest**物件新增授權標頭的範例，請參閱 [SharePoint 2013 其餘介面的讀取資料](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData)。
  
    
    

> [!注意事項]
> 雲端裝載增益集元件撰寫的JavaScript必須使用 **SP.RequestExecutor**物件中的跨網域文件庫來存取SharePoint資料。包含存取權杖，不需要跨網域文件庫要求。
  
    
    

若要進一步瞭解 OAuth 存取權杖，以及如何取得這些 」 的資訊，請參閱 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)與 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。
  
    
    

### 在跨網域邀請中的端點 Uri SP.使用若要變更內容的 AppContextSite
<a name="AppContextSite"> </a>

邀請也會傳送至資源端點要求的 **url**屬性中指定。結束點 Uri 使用下列格式 ︰
  
    
    
 _<site url>_/_api/ _<context>_/ _<resource>_ （例如，https://contoso.com/_api/web/lists）
  
    
    
跨網域文件庫要求存取網路上的資料增益集，這是預設內容的跨網域文件庫要求時，使用此格式。但若要存取其他網站集合或主機網站上的資料，邀請需要初始化主機網頁或其他網站集合做為內容。若要這麼做，請使用 **SP.AppContextSite**端點中 URI，如下所示資料表 1。範例 Uri 資料表 1 中使用 **@target**別名傳送目標 URL 查詢字串中，是因為 URL 中包含特殊字元 (': 」)。
  
    
    

> [!注意事項]
> 增益集網站執行個體的雲端裝載增益集存取SharePoint資料時所使用的跨網域文件庫。
  
    
    


**資料表 1。使用 SP.若要變更要求的內容 AppContextSite 端點**


|**增益集類型**|**跨網域資料存取的案例**|**範例端點 URI**|
|:-----|:-----|:-----|
|雲端裝載 <br/> |JavaScript增益集元件，存取主機 web 資料使用的跨網域文件庫 <br/> | _<app web url>_/_api SP./AppContextSite(@target)/web/lists? @ 目標 =' _<host web url>_' <br/> |
|雲端裝載 <br/> |JavaScript增益集元件，使用跨網域文件庫 （租用戶範圍增益集只） 來存取主機網站以外的網站集合中的資料 <br/> | _<app web url>_/_api SP./AppContextSite(@target)/web/title? @ 目標 =' _<target site url>_' <br/> |
|SharePoint-裝載於 <br/> |增益集 web 元件存取其他網站集合 （租用戶範圍增益集只） 中的資料 <br/> | _<app web url>_/_api SP./AppContextSite(@target)/web/title? @ 目標 =' _<target site url>_' <br/> |
   

> [!注意事項]
> 跨網域資料存取案例也需要適當的增益集權限。如需詳細資訊，請參閱 [從主機 web Access 資料](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)和 [Access 資料跨網站集合](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope)。
  
    
    

SharePoint Add-ins可以取得的增益集的 web URL 及主機網址從查詢字串中的 [增益集] 頁面，如下列範例所示。範例也會顯示如何參考 SP.中定義的跨網域文件庫RequestExecutor.js 主機網站上的檔案。假設增益集從SharePoint啟動。如需從SharePoint不會啟動增益集時，正確地設定您SharePoint的內容，請參閱 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。
  
    
    



```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions
```


## 用於其餘要求的內容
<a name="bk_requestElements"> </a>

資料表 2 的會顯示在 HTTP 要求SharePoint其餘服務的常用的屬性。
  
    
    

**資料表 2。在 [HTTP 要求中使用其他要求屬性**


|**屬性**|**需要時**|**說明**|
|:-----|:-----|:-----|
|**url** <br/> |所有的要求 <br/> |其他資源端點的 URL。範例 ︰  `http://<site url>/_api/web/lists` <br/> |
|**method**（或 **type**） <br/> |所有的要求 <br/> |HTTP 要求方法 ︰ **GET**讀取作業及 **POST**的寫入作業。 **POST**要求可以執行更新，或指定 **DELETE**、 **MERGE**或 **PUT**動詞 **X-HTTP-Method**標頭中，刪除作業。 <br/> |
|**body**（或 **data**） <br/> |傳送邀請內文中的資料的 **POST**要求 <br/> |文章邀請內文。傳送無法傳送端點 URI 中的資料 （例如複雜類型）。使用 **內容長度**標題。 <br/> |
|**驗證**標頭 <br/> |遠端的增益集來驗證使用者是 OAuth。使用JavaScript或跨網域文件庫時不適用。 <br/> |傳送 OAuth 存取權杖 （從 Microsoft Access 控制項服務 (ACS) 安全的權杖伺服器取得），用來要求使用者進行驗證。範例 ︰  `"Authorization": "Bearer " + accessToken`，其中 `accessToken`代表儲存權杖的變數。權杖必須使用伺服器端程式碼來擷取。 <br/> |
|**X-RequestDigest**標頭 <br/> |（除了屬於 SP. **POST**要求要求 RequestExecutor） <br/> |遠端的增益集使用 OAuth 可以從 `http://<site url>/_api/contextinfo`端點取得表單摘要值。SharePoint-則可使用SharePoint網頁上裝載的增益集從 **#__REQUESTDIGEST**分頁控制項取得的值。請參閱 [使用其餘介面來撰寫資料](#WritingData)。 <br/> |
|**accept**標頭 <br/> |傳回SharePoint中繼資料的要求 <br/> |指定從伺服器的回應資料的格式。預設格式為 `application/atom+xml`。範例 ︰  `"accept":"application/json;odata=verbose"` <br/> |
|**content-type**標頭 <br/> |傳送邀請內文中的資料的 **POST**要求 <br/> |指定之資料的用戶端傳送至伺服器的格式。預設格式為 `application/atom+xml`。範例 ︰  `"content-type":"application/json;odata=verbose"` <br/> |
|**content-length**標頭 <br/> |傳送資料 （除了屬於 SP.邀請內文中的 **POST**要求要求 RequestExecutor） <br/> |指定內容的長度。範例 ︰  `"content-length":requestBody.length` <br/> |
|**如果**標頭 <br/> |**POST**要求 **DELETE**、 **MERGE**或 **PUT**作業，主要的變更清單和文件庫。 <br/> |提供驗證，變更的物件尚未變更自上次擷取的方式。或者，讓您指定要覆寫任何變更，如下列範例所示 ︰  `"IF-MATCH":"*"` <br/> |
|**X-HTTP-Method**標頭 <br/> |**POST**要求 **DELETE**、 **MERGE**或 **PUT**作業 <br/> |用來指定要求執行更新或刪除作業。範例 ︰  `"X-HTTP-Method":"PUT"` <br/> |
|**binaryStringRequestBody** <br/> |SP.傳送本文中的二進位資料的 RequestExecutor **POST**要求 <br/> |指定邀請本文的二進位字串。 **Boolean**。 <br/> |
|**binaryStringResponseBody** <br/> |SP.傳回二進位資料的 RequestExecutor 要求 <br/> |指定回覆的二進位字串。 **Boolean**。 <br/> |
   

## 批次工作支援
<a name="batch"> </a>

SharePoint Online （與內部部署SharePoint 2016年及更新版本） 其餘服務支援合併成單一呼叫服務的多個要求，請使用 OData  `$batch`查詢選項。 如需詳細資料與程式碼範例的連結，請參閱 [請使用 REST Api 的批次要求](make-batch-requests-with-the-rest-apis.md)。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [使用資料夾與其餘的檔案](working-with-folders-and-files-with-rest.md)
    
  
-  [REST API 參考和範例](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013 ︰ 使用其餘執行基本資料存取作業檔案和資料夾上的](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [開放式資料通訊協定](http://www.odata.org/)
    
  
-  [OData: JavaScript 物件標記法 (JSON) 格式](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  
-  [使用 REST 介面在清單上設定自訂權限](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)
    
  

  
    
    

