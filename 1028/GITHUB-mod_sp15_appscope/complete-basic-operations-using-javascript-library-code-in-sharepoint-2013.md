---
title: 使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# 使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業
深入了解如何使用 SharePoint 2013 中的 JavaScript 用戶端物件模型來撰寫程式碼，以執行基本作業。
> **注意事項**
> 如需使用 JavaScript 程式庫的 "Hello World" 層級範例 SharePoint Add-in，請參閱  [使用 SharePoint JavaScript Api 來處理 SharePoint 資料](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md)。 
  
    
    


## SharePoint 2013 用戶端 API
<a name="ClientAPIs"> </a>

您可以使用 SharePoint 用戶端物件模型以擷取、更新及管理 SharePoint 2013 中的資料。SharePoint 提供數種使用物件模型的格式。
  
    
    

- .NET Framework 可轉散發的組件
    
  
- JavaScript 程式庫
    
  
- REST/OData 端點
    
  
- Windows Phone 組件
    
  
- Silverlight 可轉散發的組件
    
  
如需 SharePoint 2013 可用的 API 集合相關資訊，請參閱  [選擇 [設定 SharePoint 2013 中的右 API](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。
  
    
    
本文章顯示如何使用 JavaScript 物件模型，以執行基本作業。您可以使用 HTML <script> 標籤，來新增物件模型的參考。如需如何使用其他用戶端 API 的詳細資訊，請參閱下列項目：
  
    
    

-  [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [建置存取 SharePoint 2013 的 Windows Phone 應用程式](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)
    
  
-  [使用 Silverlight 物件模型](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)
    
  

## 使用 JavaScript 用戶端物件模型，以執行 SharePoint 2013 中的基本工作
<a name="BasicOps_SPJSOMOps"> </a>

下列章節說明您可以用程式設計的方式完成的工作，包含示範作業的 JavaScript 程式碼範例。
  
    
    
當您建立雲端託管的增益集時，您可以使用 HTML <script> 標籤，來新增物件模型的參考。我們建議您參考主機 Web，因為可能不是雲端託管的增益集中每種情況下，都會有增益集 Web。如果使用 **{StandardTokens}** 權杖，您可以從 _SPHostUrl_ 查詢字串參數擷取主機 Web URL。如果使用 **{HostUrl}** 權杖，您也可以使用自訂所定義的查詢字串參數。主應用程式的 web URL 之後，您必須使用 JavaScript 程式碼來動態建立物件模型的參考。
  
    
    
下列程式碼範例會執行這些工作，來加入 JavaScript 物件模型參考︰
  
    
    

- 從 Microsoft 內容傳遞網路 (CDN) 參考 AJAX Library。
    
  
- 從 Microsoft CDN 參考 jQuery Library。
    
  
- 從查詢字串中擷取主機 Web 的 URL。
    
  
- 使用 jQuery 中的 **getScript** 函數，載入 SP.Runtime.js 和 SP.js 檔案。在載入檔案之後，您的程式會有權存取 SharePoint 的 JavaScript 物件模型。
    
  
- 在 **execOperation** 函數中繼續流程。
    
  



```

<script
    src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js" 
    type="text/javascript">
</script>
<script
    type="text/javascript"
    src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
</script>
<script type="text/javascript">
    var hostweburl;

    // Load the required SharePoint libraries.
    $(document).ready(function () {

        // Get the URI decoded URLs.
        hostweburl =
            decodeURIComponent(
                getQueryStringParameter("SPHostUrl")
        );

        // The js files are in a URL in the form:
        // web_url/_layouts/15/resource_file
        var scriptbase = hostweburl + "/_layouts/15/";

        // Load the js files and continue to
        // the execOperation function.
        $.getScript(scriptbase + "SP.Runtime.js",
            function () {
                $.getScript(scriptbase + "SP.js", execOperation);
            }
        );
    });

    // Function to execute basic operations.
    function execOperation() {

        // Continue your program flow here.

    }

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

```

當您建立 SharePoint 託管的增益集時，您可以使用 HTML <script> 標籤，來新增物件模型的參考。SharePoint 託管的增益集中，增益集 Web 讓您可使用相對路徑來參考所需的檔案，以使用 JavaScript 物件模型。
  
    
    
下列標記會執行這些工作，來加入 JavaScript 物件模型參考︰
  
    
    

- 從 Microsoft CDN 參考 AJAX library。
    
  
- 使用增益集 Web 的相對 URL，來參考 SP.Runtime.js 檔案。
    
  
- 使用增益集 Web 的相對 URL，來參考 SP.js 檔案。
    
  



```

<script
    src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js" 
    type="text/javascript">
</script>
<script 
    type="text/javascript" 
    src="/_layouts/15/sp.runtime.js">
</script>
<script 
    type="text/javascript" 
    src="/_layouts/15/sp.js">
</script>
<script type="text/javascript">

    // Continue your program flow here.

</script>
```


## SharePoint 網站工作
<a name="BasicOps_SPWebTasks"> </a>

若要使用 JavaScript 來處理網站，請先使用 **ClientContext(serverRelativeUrl)** 建構函式，並傳遞 URL 或 URI 以傳回特定的要求內容。
  
    
    

### 擷取網站的屬性

使用 **ClientContext** 類別的 Web 屬性，來指定位於指定內容 URL 的網站物件屬性。在透過 **load(clientObject)** 方法載入網站物件，並呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 之後，您會取得該網站的所有屬性的存取。下列範例會顯示標題和指定的網站的描述；雖然在您載入網站物件，並執行查詢之後，就可以使用所有依預設會傳回的其他屬性。
  
    
    

```

function retrieveWebSite(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    this.oWebsite = clientContext.get_web();

    clientContext.load(this.oWebsite);

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded(sender, args) {
    alert('Title: ' + this.oWebsite.get_title() + 
        ' Description: ' + this.oWebsite.get_description());
}
    
function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 擷取選取的網站屬性

若要減少用戶端與伺服器之間不必要的資料傳輸，您可能會想要只傳回網站物件的指定屬性，而不是其所有的屬性。在這種情況下，使用與 LINQ 查詢或 Lambda 運算式語法搭配 **load(clientObject)** 方法，以指定要從伺服器傳回的屬性。在下列範例中，呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 之後，只有標題和網站物件的建立日期變成可用。
  
    
    

```

function retrieveWebSiteProperties(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    this.oWebsite = clientContext.get_web();

    clientContext.load(this.oWebsite, 'Title', 'Created');

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded(sender, args) {
    alert('Title: ' + this.oWebsite.get_title() + 
        ' Created: ' + this.oWebsite.get_created());
}
    
function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


> **注意事項**
> 如果您嘗試存取其他屬性，程式碼會擲回例外，因為沒有其他屬性可用。 
  
    
    


### 寫入網站的屬性

若要修改網站，設定其屬性並呼叫 **update()** 方法，與伺服器物件模型運作的方式相似。然而，在用戶端物件模型中，您必須呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 以要求批次處理您指定的所有命令。下列範例會變更標題和指定網站的描述。
  
    
    

```

function updateWebSite(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    this.oWebsite = clientContext.get_web();

    this.oWebsite.set_title('Updated Web Site');
    this.oWebsite.set_description('This is an updated Web site.');
    this.oWebsite.update();

    clientContext.load(this.oWebsite, 'Title', 'Description');

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded(sender, args) {
    alert('Title: ' + this.oWebsite.get_title() + 
        ' Description: ' + this.oWebsite.get_description());
}
    
function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


## SharePoint 清單工作
<a name="BasicOps_SPListTasks"> </a>

使用 JavaScript 來處理清單物件，與處理網站物件相似。開始時，請使用 **ClientContext(serverRelativeUrl)** 建構函式並傳遞 URL 或 URI，以傳回特定的要求內容。然後您可以使用 **Web** 類別的 **lists** 屬性，來取得在網站中的清單集合。
  
    
    

### 擷取網站中所有清單的所有屬性

若要傳回網站的所有清單，請透過 **load(clientObject)** 方法載入清單集合，然後再呼叫 **executeQueryAsync(succeededCallback, failedCallback)**。下列範例會顯示網站的 URL，以及清單建立的日期和時間。
  
    
    

```

function retrieveAllListProperties(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oWebsite = clientContext.get_web();
    this.collList = oWebsite.get_lists();
    clientContext.load(collList);

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    var listInfo = '';
    var listEnumerator = collList.getEnumerator();

    while (listEnumerator.moveNext()) {
        var oList = listEnumerator.get_current();
        listInfo += 'Title: ' + oList.get_title() + ' Created: ' + 
            oList.get_created().toString() + '\\n';
    }
    alert(listInfo);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 只擷取清單的指定屬性

先前的範例會傳回網站中清單的所有屬性。若要減少用戶端與伺服器之間不必要的資料傳輸，您可以使用 LINQ 查詢運算式，指定要傳回的屬性。在 JavaScript 中，您指定 **Include** 做為查詢字串的一部分，傳遞至 **load(clientObject)** 方法，以指定要傳回的屬性。下列範例會使用這種方法，只傳回集合中每個清單的標題和識別碼。
  
    
    

```

function retrieveSpecificListProperties(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oWebsite = clientContext.get_web();
    this.collList = oWebsite.get_lists();

    clientContext.load(collList, 'Include(Title, Id)');
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    var listInfo = '';
    var listEnumerator = collList.getEnumerator();

    while (listEnumerator.moveNext()) {
        var oList = listEnumerator.get_current();
        listInfo += 'Title: ' + oList.get_title() + 
            ' ID: ' + oList.get_id().toString() + '\\n';
    }
    alert(listInfo);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}

```


### 儲存在集合中所擷取的清單

如下列範例所示，您可以使用 **loadQuery(clientObjectCollection, exp)** 方法，而不是 **load(clientObject)** 方法，來將傳回值儲存在另一個集合中，而不是將其儲存在清單屬性中。
  
    
    

```

function retrieveSpecificListPropertiesToCollection(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oWebsite = clientContext.get_web();
    var collList = oWebsite.get_lists();

    this.listInfoCollection = clientContext.loadQuery(collList, 'Include(Title, Id)');
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    var listInfo = '';

    for (var i = 0; i < this.listInfoCollection.length; i++) {
        var oList = this.listInfoCollection[i];
        listInfo += 'Title: ' + oList.get_title() + 
            ' ID: ' + oList.get_id().toString();
    }
    alert(listInfo.toString());
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 套用篩選條件至清單擷取

如下列範例所示，您可以在 JavaScript 查詢中使用巢狀 **Include** 陳述式，來傳回清單及其欄位的中繼資料。範例會傳回網站內所有清單的所有欄位，並顯示所有包含字串「名稱」的欄位標題以及內部名稱。
  
    
    

```

function retrieveAllListsAllFields(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oWebsite = clientContext.get_web();
    var collList = oWebsite.get_lists();

    this.listInfoArray = clientContext.loadQuery(collList, 
        'Include(Title,Fields.Include(Title,InternalName))');

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this._onQueryFailed)
    );
}

function onQuerySucceeded() {
    var listInfo = '';

    for (var i = 0; i < this.listInfoArray.length; i++) {
        var oList = this.listInfoArray[i];
        var collField = oList.get_fields();
        var fieldEnumerator = collField.getEnumerator();
            
        while (fieldEnumerator.moveNext()) {
            var oField = fieldEnumerator.get_current();
            var regEx = new RegExp('name', 'ig');
            
            if (regEx.test(oField.get_internalName())) {
                listInfo += '\\nList: ' + oList.get_title() + 
                    '\\n\\tField Title: ' + oField.get_title() + 
                    '\\n\\tField Name: ' + oField.get_internalName();
            }
        }
    }
    alert(listInfo);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}

```


## 建立、更新和刪除清單
<a name="BasicOps_SPListCRUD"> </a>

透過用戶端物件模型來建立、更新和刪除清單的運作方式，與使用.NET 用戶端物件模型來執行這些工作相似；只是到您呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 函數為止，用戶端作業會無法完成。
  
    
    

### 建立並更新清單

若要使用 JavaScript 來建立清單物件，請使用 **ListCreationInformation** 物件來定義其屬性，然後將此物件傳遞至 **ListCollection** 物件的 **add(parameters)** 函數。下列範例會建立新的宣告清單。
  
    
    

```

function createList(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oWebsite = clientContext.get_web();
    
    var listCreationInfo = new SP.ListCreationInformation();
    listCreationInfo.set_title('My Announcements List');
    listCreationInfo.set_templateType(SP.ListTemplateType.announcements);

    this.oList = oWebsite.get_lists().add(listCreationInfo);

    clientContext.load(oList);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    var result = oList.get_title() + ' created.';
    alert(result);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```

如果您在建立之後需要更新清單，在呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 之前，您可以設定清單內容並呼叫 **update()** 函數，如先前範例中的下列修改所示。
  
    
    



```

.
.
.
.
this.oList = oWebsite.get_lists().add(listCreationInfo);

oList.set_description('New Announcements List');
oList.update();

clientContext.load(oList);
clientContext.executeQueryAsync(
    Function.createDelegate(this, this.onQuerySucceeded), 
    Function.createDelegate(this, this.onQueryFailed)
);
```


### 將欄位新增到清單

使用 **FieldCollection** 物件的 **add(field)** 或 **addFieldAsXml(schemaXml, addToDefaultView, options)** 函數，將欄位新增到清單的欄位集合。下列範例會建立欄位，並在呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 之前將其更新。
  
    
    

```

function addFieldToList(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);

    var oList = clientContext.get_web().get_lists().getByTitle('Announcements');
    this.oField = oList.get_fields().addFieldAsXml(
        '<Field DisplayName=\\'MyField\\' Type=\\'Number\\' />', 
        true, 
        SP.AddFieldOptions.defaultValue
    );

    var fieldNumber = clientContext.castTo(oField,SP.FieldNumber);
    fieldNumber.set_maximumValue(100);
    fieldNumber.set_minimumValue(35);
    fieldNumber.update();

    clientContext.load(oField);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    var result = oField.get_title() + ' added.';
    alert(result);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 刪除清單

若要刪除清單，請呼叫清單物件的 **deleteObject()** 函數，如下列範例所示。
  
    
    

```

function deleteList(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oWebsite = clientContext.get_web();
    this.listTitle = 'My Announcements List';

    this.oList = oWebsite.get_lists().getByTitle(listTitle);
    oList.deleteObject();

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    var result = listTitle + ' deleted.';
    alert(result);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


## 建立、更新和刪除資料夾
<a name="BasicOps_FolderTasks"> </a>

您可以使用 JavaScript 物件模型來管理資料夾，以組織您的內容。下列章節說明要如何在資料夾上執行基本作業。
  
    
    

### 在文件庫中建立資料夾

若要建立資料夾，請使用 **ListItemCreationInformation** 物件，將基礎物件類型設定為 **SP.FileSystemObjectType.folder**，並將其當做參數傳遞至 **List** 物件的 **addItem(parameters)** 函數。在此方法傳回的清單項目物件上設定屬性，然後呼叫 **update()** 函數，如下列範例所示。
  
    
    

```

function createFolder(resultpanel) {
    var clientContext;
    var oWebsite;
    var oList;
    var itemCreateInfo;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();
    oList = oWebsite.get_lists().getByTitle("Shared Documents");

    itemCreateInfo = new SP.ListItemCreationInformation();
    itemCreateInfo.set_underlyingObjectType(SP.FileSystemObjectType.folder);
    itemCreateInfo.set_leafName("My new folder!");
    this.oListItem = oList.addItem(itemCreateInfo);
    this.oListItem.set_item("Title", "My new folder!");
    this.oListItem.update();

    clientContext.load(this.oListItem);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, successHandler),
        Function.createDelegate(this, errorHandler)
    );

    function successHandler() {
        resultpanel.innerHTML = "Go to the " +
            "<a href='../Lists/Shared Documents'>document library</a> " +
            "to see your new folder.";
    }

    function errorHandler() {
        resultpanel.innerHTML =
            "Request failed: " + arguments[1].get_message();
    }
}
```


### 更新文件庫中的資料夾

若要更新資料夾名稱，您可以撰寫 **FileLeafRef** 屬性並呼叫 **update()** 函數，如此當您呼叫 **executeQueryAsync** 方法時，變更才會生效。
  
    
    

```

function updateFolder(resultpanel) {
    var clientContext;
    var oWebsite;
    var oList;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();
    oList = oWebsite.get_lists().getByTitle("Shared Documents");

    this.oListItem = oList.getItemById(1);
    this.oListItem.set_item("FileLeafRef", "My updated folder");
    this.oListItem.update();

    clientContext.load(this.oListItem);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, successHandler),
        Function.createDelegate(this, errorHandler)
    );

    function successHandler() {
        resultpanel.innerHTML = "Go to the " +
            "<a href='../Lists/Shared Documents'>document library</a> " +
            "to see your updated folder.";
    }

    function errorHandler() {
        resultpanel.innerHTML = "Request failed: " + arguments[1].get_message();
    }
}
```


### 刪除文件庫中的資料夾

若要刪除資料夾，請呼叫物件上的 **deleteObject()** 函數。下列範例會使用 **getFolderByServerRelativeUrl** 方法，來從文件庫擷取資料夾，然後刪除項目。
  
    
    

```

function deleteFolder(resultpanel) {
    var clientContext;
    var oWebsite;
    var folderUrl;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();

    clientContext.load(oWebsite);
    clientContext.executeQueryAsync(function () {
        folderUrl = oWebsite.get_serverRelativeUrl() + "/Lists/Shared Documents/Folder1";
        this.folderToDelete = oWebsite.getFolderByServerRelativeUrl(folderUrl);
        this.folderToDelete.deleteObject();

        clientContext.executeQueryAsync(
            Function.createDelegate(this, successHandler),
            Function.createDelegate(this, errorHandler)
        );
    }, errorHandler);

    function successHandler() {
        resultpanel.innerHTML = "Go to the " +
            "<a href='../Lists/Shared Documents'>document library</a> " +
            "to make sure the folder is no longer there.";
    }

    function errorHandler() {
        resultpanel.innerHTML = "Request failed: " + arguments[1].get_message();
    }
}
```


## 建立、讀取、更新和刪除檔案
<a name="BasicOps_FileTasks"> </a>

您可以使用 JavaScript 物件模型來管理檔案。下列章節說明如何在檔案上執行基本作業。
  
    
    

> **注意事項**
> 您只可以使用 JavaScript 物件模型，處理多達 1.5 MB 的檔案。若要上載較大的檔案，請使用 REST (具象狀態傳輸)。如需詳細資訊，請參閱 [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles)。 
  
    
    


### 在文件庫中建立檔案

若要建立檔案，請使用 **FileCreationInformation** 物件設定 URL 屬性，並以 Base64 編碼的位元組陣列來附加內容，如本範例所示。
  
    
    

```

function createFile(resultpanel) {
    var clientContext;
    var oWebsite;
    var oList;
    var fileCreateInfo;
    var fileContent;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();
    oList = oWebsite.get_lists().getByTitle("Shared Documents");

    fileCreateInfo = new SP.FileCreationInformation();
    fileCreateInfo.set_url("my new file.txt");
    fileCreateInfo.set_content(new SP.Base64EncodedByteArray());
    fileContent = "The content of my new file";

    for (var i = 0; i < fileContent.length; i++) {
        
        fileCreateInfo.get_content().append(fileContent.charCodeAt(i));
    }

    this.newFile = oList.get_rootFolder().get_files().add(fileCreateInfo);

    clientContext.load(this.newFile);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, successHandler),
        Function.createDelegate(this, errorHandler)
    );

    function successHandler() {
        resultpanel.innerHTML =
            "Go to the " +
            "<a href='../Lists/Shared Documents'>document library</a> " +
            "to see your new file.";
    }

    function errorHandler() {
        resultpanel.innerHTML = "Request failed: " + arguments[1].get_message();
    }
}
```


### 讀取文件庫中的檔案

若要讀取檔案的內容，請在檔案的 URL 上執行 **GET** 作業，如下列範例所示。
  
    
    

```

function readFile(resultpanel) {
    var clientContext;
    var oWebsite;
    var fileUrl;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();

    clientContext.load(oWebsite);
    clientContext.executeQueryAsync(function () {
        fileUrl = oWebsite.get_serverRelativeUrl() +
            "/Lists/Shared Documents/TextFile1.txt";
        $.ajax({
            url: fileUrl,
            type: "GET"
        })
            .done(Function.createDelegate(this, successHandler))
            .error(Function.createDelegate(this, errorHandler));
    }, errorHandler);

    function successHandler(data) {
        resultpanel.innerHTML =
            "The content of file \\"TextFile1.txt\\": " + data
    }

    function errorHandler() {
        resultpanel.innerHTML =
            "Request failed: " + arguments[2];
    }
}
```


### 更新文件庫中的檔案

若要更新檔案的內容，您可以使用 **FileCreationInformation** 物件，並使用 **set_overwrite()** 方法，將覆寫屬性設定為 True，如本範例所示。
  
    
    

```

function updateFile(resultpanel) {
    var clientContext;
    var oWebsite;
    var oList;
    var fileCreateInfo;
    var fileContent;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();
    oList = oWebsite.get_lists().getByTitle("Shared Documents");

    fileCreateInfo = new SP.FileCreationInformation();
    fileCreateInfo.set_url("TextFile1.txt");
    fileCreateInfo.set_content(new SP.Base64EncodedByteArray());
    fileCreateInfo.set_overwrite(true);
    fileContent = "The updated content of my file";

    for (var i = 0; i < fileContent.length; i++) {

        fileCreateInfo.get_content().append(fileContent.charCodeAt(i));
    }

    this.existingFile = oList.get_rootFolder().get_files().add(fileCreateInfo);

    clientContext.load(this.existingFile);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, successHandler),
        Function.createDelegate(this, errorHandler)
    );

    function successHandler() {
        resultpanel.innerHTML =
            "Go to the " +
            "<a href='../Lists/Shared Documents'>document library</a> " +
            "to see the updated \\"TextFile1.txt\\" file.";
    }

    function errorHandler() {
        resultpanel.innerHTML =
            "Request failed: " + arguments[1].get_message();
    }
}
```


### 刪除文件庫中的檔案

若要刪除檔案，請呼叫物件上的 **deleteObject()** 函數。下列範例會使用 **getFileByServerRelativeUrl** 方法，來從文件庫擷取檔案，然後刪除項目。
  
    
    

```

function deleteFile(resultpanel) {
    var clientContext;
    var oWebsite;
    var fileUrl;

    clientContext = new SP.ClientContext.get_current();
    oWebsite = clientContext.get_web();

    clientContext.load(oWebsite);
    clientContext.executeQueryAsync(function () {
        fileUrl = oWebsite.get_serverRelativeUrl() +
            "/Lists/Shared Documents/TextFile1.txt";
        this.fileToDelete = oWebsite.getFileByServerRelativeUrl(fileUrl);
        this.fileToDelete.deleteObject();

        clientContext.executeQueryAsync(
            Function.createDelegate(this, successHandler),
            Function.createDelegate(this, errorHandler)
        );
    }, errorHandler);

    function successHandler() {
        resultpanel.innerHTML =
            "Go to the " +
            "<a href='../Lists/Shared Documents'>document library</a> " +
            "to confirm that the \\"TextFile1.txt\\" file has been deleted.";
    }

    function errorHandler() {
        resultpanel.innerHTML = "Request failed: " + arguments[1].get_message();
    }
}
```


## SharePoint 清單項目工作
<a name="BasicOps_SPListItemTasks"> </a>

若要使用 JavaScript 從清單中傳回項目，請使用 **getItemById(id)** 函數來傳回單一項目，或使用 **getItems(query)** 函數來傳回多個項目。然後使用 **load(clientObject)** 函數，來獲得代表項目的清單項目物件。
  
    
    

### 從清單中擷取項目

 **getItems(query)** 函數讓您可定義 Collaborative Application Markup Language (CAML) 查詢，該查詢會指定要傳回的項目。您可以傳遞未定義的 **CamlQuery** 物件，來從清單中傳回所有的項目，或使用 **set_viewXml** 函數來定義 CAML 查詢，並傳回符合特定準則的項目。下列範例除了標題和本文資料欄值，還會顯示宣告清單中前 100 個項目的識別碼，以集合識別碼值大於 10 的清單項目開頭。
  
    
    

```

function retrieveListItems(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oList = clientContext.get_web().get_lists().getByTitle('Announcements');
        
    var camlQuery = new SP.CamlQuery();
    camlQuery.set_viewXml(
        '<View><Query><Where><Geq><FieldRef Name=\\'ID\\'/>' + 
        '<Value Type=\\'Number\\'>1</Value></Geq></Where></Query>' + 
        '<RowLimit>10</RowLimit></View>'
    );
    this.collListItem = oList.getItems(camlQuery);
        
    clientContext.load(collListItem);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    ); 
}

function onQuerySucceeded(sender, args) {
    var listItemInfo = '';
    var listItemEnumerator = collListItem.getEnumerator();
        
    while (listItemEnumerator.moveNext()) {
        var oListItem = listItemEnumerator.get_current();
        listItemInfo += '\\nID: ' + oListItem.get_id() + 
            '\\nTitle: ' + oListItem.get_item('Title') + 
            '\\nBody: ' + oListItem.get_item('Body');
    }

    alert(listItemInfo.toString());
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 使用 Include 方法來存取 ListItem 物件的屬性

當您傳回清單項目時，依預設 **ListItem** 物件的四個屬性不可用 - **displayName**、 **effectiveBasePermissions**、 **hasUniqueRoleAssignments** 和 **roleAssignments**。如果您嘗試存取這些屬性的其中一個，先前的範例會傳回 **PropertyOrFieldNotInitializedException**。若要存取這些屬性，請將 **Include** 方法當作查詢字串的一部分使用，如下列範例所示。
  
    
    

> **注意事項**
> 當您使用 LINQ 來針對用戶端物件模型建立查詢時，會使用  [LINQ to Objects](http://msdn.microsoft.com/zh-tw/library/bb397919)，而不是  [LINQ to SharePoint provider](http://msdn.microsoft.com/zh-tw/library/ee535491)，後者只有當您撰寫針對伺服器物件模型的程式碼時，才可以使用。 
  
    
    


```

function retrieveListItemsInclude(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oList = clientContext.get_web().get_lists().getByTitle('Announcements');

    var camlQuery = new SP.CamlQuery();
    camlQuery.set_viewXml('<View><RowLimit>100</RowLimit></View>');
    this.collListItem = oList.getItems(camlQuery);

    clientContext.load(
        collListItem, 
        'Include(Id, DisplayName, HasUniqueRoleAssignments)'
    );
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded(sender, args) {
    var listItemInfo = '';
    var listItemEnumerator = collListItem.getEnumerator();
        
    while (listItemEnumerator.moveNext()) {
        var oListItem = listItemEnumerator.get_current();
        listItemInfo += '\\nID: ' + oListItem.get_id() + 
            '\\nDisplay name: ' + oListItem.get_displayName() + 
            '\\nUnique role assignments: ' + 
            oListItem.get_hasUniqueRoleAssignments();
    }

    alert(listItemInfo.toString());
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}

```

因為這個範例會使用 **Include**，在查詢執行之後，只有指定的屬性可用。因此，如果您嘗試存取除了指定的屬性以外的其他屬性，您會收到 **PropertyOrFieldNotInitializedException**。此外，如果您嘗試使用例如 **get_contentType** 或 **get_parentList** 函數來存取包含物件的屬性，您會收到這個錯誤訊息。
  
    
    

### 擷取項目的限制

SharePoint Foundation 2010 中 JavaScript 物件模型的 **loadQuery(clientObjectCollection, exp)** 方法，不支援 LINQ 方法和所管理的物件模型使用的運算子。
  
    
    

## 建立、更新和刪除清單項目
<a name="BasicOps_SPListItemCRUD"> </a>

透過用戶端物件模型來建立、更新和刪除清單項目的運作方式，與透過伺服器物件模型來執行這些工作相似。建立清單項目物件、設定其屬性，然後更新物件。若要修改或刪除清單項目物件，請使用 **ListItemCollection** 物件的 **getById(id)** 函數以傳回物件，然後在此方法傳回的物件上設定屬性並呼叫更新；或呼叫物件本身的方法來刪除。與伺服器物件模型不同，每個用戶端物件模型中的這些作業，都必須以呼叫 **to executeQueryAsync(succeededCallback, failedCallback)** 結束，變更才會在伺服器上生效。
  
    
    

### 建立清單項目

若要建立清單項目，請建立 **ListItemCreationInformation** 物件、設定其屬性，並將其當做參數傳遞至 **List** 物件的 **addItem(parameters)** 函數。在此方法傳回的清單項目物件上設定屬性，然後呼叫 **update()** 函數，如下列範例所示。
  
    
    

```

function createListItem(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oList = clientContext.get_web().get_lists().getByTitle('Announcements');
        
    var itemCreateInfo = new SP.ListItemCreationInformation();
    this.oListItem = oList.addItem(itemCreateInfo);
    oListItem.set_item('Title', 'My New Item!');
    oListItem.set_item('Body', 'Hello World!');
    oListItem.update();

    clientContext.load(oListItem);
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    alert('Item created: ' + oListItem.get_id());
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 更新清單項目

若要設定大部分的清單項目內容，您可以使用資料欄索引子來進行指派並呼叫 **update()** 函數，因此當您呼叫 **executeQueryAsync(succeededCallback, failedCallback)** 時，變更將會生效。下列範例會設定宣告清單中，第三個項目的標題。
  
    
    

```

function updateListItem(siteUrl) {
    var clientContext = new SP.ClientContext(siteUrl);
    var oList = clientContext.get_web().get_lists().getByTitle('Announcements');

    this.oListItem = oList.getItemById(3);
    oListItem.set_item('Title', 'My Updated Title');
    oListItem.update();

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    alert('Item updated!');
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


### 刪除清單項目

若要刪除清單項目，請呼叫物件上的 **deleteObject()** 函數。下列範例會使用 **getItemById(id)** 函數，來從清單中傳回第二個項目，然後刪除項目。SharePoint 會維護在集合內的整數識別碼，即使已經將其刪除。例如，因此在清單中的第二個項目，可能會沒有 2 作為其識別項。如果呼叫 **deleteObject()** 函數所針對的項目不存在，便會傳回 **ServerException**。
  
    
    

```

function deleteListItem(siteUrl) {
    this.itemId = 2;
    var clientContext = new SP.ClientContext(siteUrl);
    var oList = clientContext.get_web().get_lists().getByTitle('Announcements');
    this.oListItem = oList.getItemById(itemId);
    oListItem.deleteObject();

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.onQuerySucceeded), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function onQuerySucceeded() {
    alert('Item deleted: ' + itemId);
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```

例如，如果您想要擷取由於刪除作業而產生的新項目計數，請將呼叫加入 update() 方法，以重新整理清單。此外，在執行查詢前，您必須載入清單物件本身，或清單物件上的 **itemCount** 屬性。如果您想要擷取清單項目的開始和結束計數，您必須執行兩個查詢，並傳回項目計數兩次，如上一個範例中的下列修改所示。
  
    
    



```

function deleteListItemDisplayCount(siteUrl) {
    this.clientContext = new SP.ClientContext(siteUrl);
    this.oList = clientContext.get_web().get_lists().getByTitle('Announcements');
    clientContext.load(oList);

    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.deleteItem), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function deleteItem() {
    this.itemId = 58;
    this.startCount = oList.get_itemCount();
    this.oListItem = oList.getItemById(itemId);
    oListItem.deleteObject();

    oList.update();
    clientContext.load(oList);
        
    clientContext.executeQueryAsync(
        Function.createDelegate(this, this.displayCount), 
        Function.createDelegate(this, this.onQueryFailed)
    );
}

function displayCount() {
    var endCount = oList.get_itemCount();
    var listItemInfo = 'Item deleted: ' + itemId + 
        '\\nStart Count: ' +  startCount + 
        ' End Count: ' + endCount;
        
    alert(listItemInfo)
}

function onQueryFailed(sender, args) {
    alert('Request failed. ' + args.get_message() + 
        '\\n' + args.get_stackTrace());
}
```


## 存取主機 Web 中的物件
<a name="BasicOps_AccessHostweb"> </a>

在開發增益集時，您可能會需要存取主機 Web，以便與其中的項目互動。使用 **AppContextSite** 物件來參照主機 Web 或其他的 SharePoint 網站，如下列範例中所示。如需完整的程式碼範例，請參閱 [使用跨網域程式庫 (JSOM)，以取得主機 Web 標題](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)。
  
    
    

```

function execCrossDomainRequest(appweburl, hostweburl) {
    // context: The ClientContext object provides access to
    //      the web and lists objects.
    // factory: Initialize the factory object with the
    //      add-in web URL.
    var context;
    var factory;
    var appContextSite;

    context = new SP.ClientContext(appweburl);
    factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
    context.set_webRequestExecutorFactory(factory);
    appContextSite = new SP.AppContextSite(context, hostweburl);

    this.web = appContextSite.get_web();
    context.load(this.web);

    // Execute the query with all the previous 
    //  options and parameters.
    context.executeQueryAsync(
        Function.createDelegate(this, successHandler), 
        Function.createDelegate(this, errorHandler)
    );

    // Function to handle the success event.
    // Prints the host web's title to the page.
    function successHandler() {
        alert(this.web.get_title());
    }

    // Function to handle the error event.
    // Prints the error message to the page.
    function errorHandler(data, errorCode, errorMessage) {
        alert("Could not complete cross-domain call: " + errorMessage);
    }
}
```

先前的範例使用跨網域程式庫 SharePoint 2013，來存取主機 Web。如需詳細資訊，請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
  
    
    

## 其他資源
<a name="BasicOps_AddRes"> </a>


-  [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md)
    
  

