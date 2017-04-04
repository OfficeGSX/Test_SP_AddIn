---
title: 使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# 使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作
了解如何编写代码以在 SharePoint 2013 中使用 JavaScript 客户端对象模型执行基本操作。
> **注释**
> 有关使用 JavaScript 库的"Hello World"级别示例 SharePoint 外接程序，请参阅 [使用 SharePoint JavaScript API 处理 SharePoint 数据](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md)。 
  
    
    


## SharePoint 2013 客户端 API
<a name="ClientAPIs"> </a>

您可以使用 SharePoint 客户端对象模型在 SharePoint 2013 中检索、更新和管理数据。SharePoint 按以下几种形式提供此对象模型。
  
    
    

- .NET Framework 可再发行程序集
    
  
- JavaScript 库
    
  
- REST/OData 终结点
    
  
- Windows Phone 程序集
    
  
- Silverlight 可再发行程序集
    
  
有关对 SharePoint 2013 可用的 API 集的详细信息，请参阅 [在 SharePoint 2013 中选择正确的 API 集](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。
  
    
    
本文说明如何使用 JavaScript 对象模型执行基本操作。您可以使用 HTML <script> 标记添加对此对象模型的引用。有关如何使用其他客户端 API 的信息，请参阅：
  
    
    

-  [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [构建访问 SharePoint 2013 的 Windows Phone 应用程序](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)
    
  
-  [使用 Silverlight 对象模型](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)
    
  

## 在 SharePoint 2013 中使用 JavaScript 客户端对象模型执行基本任务
<a name="BasicOps_SPJSOMOps"> </a>

以下几节说明了可以编程方式完成的任务，还包括演示这些操作的 JavaScript 代码示例。
  
    
    
在创建云托管的加载项时，您可以使用 HTML <script> 标记添加对此对象模型的引用。建议您引用主机 Web，因为并不是云托管的加载项中的每个方案中都包含加载项 Web。如果使用 **{StandardTokens}** 令牌，则可以从 _SPHostUrl_ 查询字符串参数检索主机 Web URL。如果使用 **{HostUrl}** 令牌，则还可以使用自定义的查询字符串参数。在获得主机 Web URL 后，您必须使用 JavaScript 代码动态创建对此对象模型的引用。
  
    
    
以下代码示例执行下列任务来添加对 JavaScript 对象模型的引用：
  
    
    

- 引用 Microsoft 内容交付网络 (CDN) 中的 AJAX 库。
    
  
- 引用 Microsoft CDN 中的 jQuery 库。
    
  
- 从查询字符串中提取主机 Web URL。
    
  
- 通过在 jQuery 中使用 **getScript** 函数加载 SP.Runtime.js 和 SP.js 文件。加载这些文件后，您的程序便具有对 SharePoint JavaScript 对象模型的访问权。
    
  
- 继续 **execOperation** 函数中的流。
    
  



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

在创建 SharePoint 托管的加载项时，您可以使用 HTML <script> 标记添加对此对象模型的引用。SharePoint 托管的加载项中的加载项 Web 允许您使用相对路径来引用使用 JavaScript 对象模型所需的文件。
  
    
    
以下标记执行下列任务来添加对 JavaScript 对象模型的引用：
  
    
    

- 引用 Microsoft CDN 中的 AJAX 库。
    
  
- 通过使用相对于加载项 Web 的 URL 引用 SP.Runtime.js 文件。
    
  
- 通过使用相对于加载项 Web 的 URL 引用 SP.js 文件。
    
  



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


## SharePoint 网站任务
<a name="BasicOps_SPWebTasks"> </a>

若要通过 JavaScript 使用网站，应首先使用 **ClientContext(serverRelativeUrl)** 构造函数并传递 URL 或 URI 以返回特定请求上下文。
  
    
    

### 检索网站的属性

使用 **ClientContext** 类的 web 属性指定位于指定上下文 URL 处的网站对象的属性。通过 **load(clientObject)** 方法加载网站对象，然后调用 **executeQueryAsync(succeededCallback, failedCallback)** 后，将会获取对该网站的所有属性的访问权限。下面的示例显示指定网站的标题和说明，尽管在加载网站对象和执行查询后返回的所有其他属性默认情况下都将变为可用。
  
    
    

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


### 仅检索网站的选定属性

若要减少客户端和服务器之间不必要的数据传输，可能需要仅返回网站对象的指定属性，而不是返回其所有属性。在这种情况下，可将 LINQ 查询或 lambda 表达式语法与 **load(clientObject)** 方法结合使用，以指定从服务器返回哪些属性。在下面的示例中，在调用 **executeQueryAsync(succeededCallback, failedCallback)** 之后，只有网站对象的标题和创建日期是可用的。
  
    
    

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


> **注释**
> 如果您尝试访问其他属性，则代码会因其他属性不可用而引发异常。 
  
    
    


### 对网站的属性进行写入

若要修改网站，可设置网站的属性并调用 **update()** 方法，这与服务器对象模型的工作方式相似。但在客户端对象模型中，必须调用 **executeQueryAsync(succeededCallback, failedCallback)** 来请求对您指定的所有命令进行批处理。下面的示例更改指定网站的标题和说明。
  
    
    

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


## SharePoint 列表任务
<a name="BasicOps_SPListTasks"> </a>

通过 JavaScript 使用列表对象与使用网站对象类似。首先使用 **ClientContext(serverRelativeUrl)** 构造函数并传递 URL 或 URI 以返回特定的请求上下文。然后，可以使用 **Web** 类的 **lists** 属性获取网站中列表的集合。
  
    
    

### 检索网站中所有列表的所有属性

若要返回网站的所有列表，可通过 **load(clientObject)** 方法加载列表集合，然后调用 **executeQueryAsync(succeededCallback, failedCallback)**。以下示例显示网站的 URL 以及创建列表的日期和时间。
  
    
    

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


### 只检索列表的指定属性

上面的示例返回网站中列表的所有属性。若要减少客户端和服务器之间不必要的数据传输，可以使用 LINQ 查询表达式指定要返回的属性。在 JavaScript 中，可以指定 **Include** 作为传递给 **load(clientObject)** 方法的查询字符串的一部分，以便指定要返回的属性。下面的示例使用该方法只返回集合中每个列表的标题和 ID。
  
    
    

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


### 在集合中存储检索到的列表

如以下示例所示，可以使用 **loadQuery(clientObjectCollection, exp)** 方法而不是 **load(clientObject)** 方法来在另一集合中存储返回值，而不是将其存储在 lists 属性中。
  
    
    

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


### 向列表检索应用筛选器

如以下示例所示，可以将 **Include** 语句嵌套在 JavaScript 查询中，以返回列表及其字段的元数据。该示例返回网站中所有列表的所有字段，并显示其内部名称包含字符串"name"的所有字段的标题和内部名称。
  
    
    

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


## 创建、更新和删除列表
<a name="BasicOps_SPListCRUD"> </a>

通过客户端对象模型创建、更新和删除列表与使用 .NET 客户端对象模型执行这些任务的方式类似，只是在调用 **executeQueryAsync(succeededCallback, failedCallback)** 函数之前，客户端操作不会完成。
  
    
    

### 创建和更新列表

若要使用 JavaScript 创建列表对象，请使用 **ListCreationInformation** 对象定义其属性，然后将该对象传递给 **ListCollection** 对象的 **add(parameters)** 函数。以下示例创建一个新公告列表。
  
    
    

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

如果在创建列表后需要更新该列表，可以在调用 **executeQueryAsync(succeededCallback, failedCallback)** 之前设置列表属性并调用 **update()** 函数，下面显示对上一示例所做的修改。
  
    
    



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


### 向列表中添加字段

使用 **FieldCollection** 对象的 **add(field)** 或 **addFieldAsXml(schemaXml, addToDefaultView, options)** 函数向列表的字段集合中添加字段。以下示例创建一个字段，然后在调用 **executeQueryAsync(succeededCallback, failedCallback)** 之前更新该字段。
  
    
    

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


### 删除列表

若要删除列表，请调用列表对象的 **deleteObject()** 函数，如下面的示例所示。
  
    
    

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


## 创建、更新和删除文件夹
<a name="BasicOps_FolderTasks"> </a>

可以使用 JavaScript 对象模型，通过操作文件夹来组织您的内容。以下各节说明如何对文件夹执行基本操作。
  
    
    

### 在文档库中创建文件夹

若要创建文件夹，请使用 **ListItemCreationInformation** 对象，将基础对象类型设置为 **SP.FileSystemObjectType.folder**，并将其作为参数传递给 **List** 对象的 **addItem(parameters)** 函数。设置此方法返回的列表项对象的属性，然后调用 **update()** 函数，如以下示例所示。
  
    
    

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


### 在文档库中更新文件夹

若要更新文件夹名称，可以写入 **FileLeafRef** 属性并调用 **update()** 函数，以使更改在您调用 **executeQueryAsync** 方法时生效。
  
    
    

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


### 从文档库中删除文件夹

若要删除某文件夹，请对对象调用 **deleteObject()** 函数。下面的示例使用 **getFolderByServerRelativeUrl** 方法从文档库中检索该文件夹，然后删除该项。
  
    
    

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


## 创建、读取、更新和删除文件
<a name="BasicOps_FileTasks"> </a>

可以使用 JavaScript 对象模型操作文件。以下各节说明如何对文件执行基本操作。
  
    
    

> **注释**
> 只能使用 JavaScript 对象模型处理最大 1.5 MB 的文件。若要上载更大文件，请使用 REST（代表性状态传输）。有关详细信息，请参阅 [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles)。 
  
    
    


### 在文档库中创建文件

若要创建文件，请使用 **FileCreationInformation** 对象，设置 URL 属性，并追加内容作为 base64 编码的字节数组，如以下示例所示。
  
    
    

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


### 在文档库中读取文件

若要读取某文件的内容，请对该文件的 URL 执行 **GET** 操作，如以下示例所示。
  
    
    

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


### 在文档库中更新文件

若要更新文件的内容，可以使用 **FileCreationInformation** 对象，并使用 **set_overwrite()** 方法将 overwrite 属性设置为 true，如以下示例所示。
  
    
    

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


### 从文档库中删除文件

若要删除某文件，请对对象调用 **deleteObject()** 函数。下面的示例使用 **getFileByServerRelativeUrl** 方法从文档库中检索该文件，然后删除该项。
  
    
    

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


## SharePoint 列表项任务
<a name="BasicOps_SPListItemTasks"> </a>

若要使用 JavaScript 返回列表项，可使用 **getItemById(id)** 函数返回单个项，也可使用 **getItems(query)** 函数返回多个项。然后使用 **load(clientObject)** 函数获取代表这些项的列表项对象。
  
    
    

### 从列表检索项

可以使用 **getItems(query)** 函数定义协作应用程序标记语言 (CAML) 查询，以指定要返回哪些项。您可以传递一个未定义的 **CamlQuery** 对象以返回列表中的所有项，也可以使用 **set_viewXml** 函数定义一个 CAML 查询并返回符合特定条件的项。以下示例显示 Announcements 列表中前 100 项的 ID 以及 Title 和 Body 列值，从集合 ID 大于 10 的列表项开始。
  
    
    

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


### 使用 Include 方法访问 ListItem 对象的属性

在返回列表项时， **ListItem** 对象的以下四个属性默认情况下不可用： **displayName**、 **effectiveBasePermissions**、 **hasUniqueRoleAssignments** 和 **roleAssignments**。如果您尝试访问其中一个属性，上面的示例将返回 **PropertyOrFieldNotInitializedException**。若要访问这些属性，请将 **Include** 方法用作查询字符串的一部分，如以下示例中所示。
  
    
    

> **注释**
> 当您使用 LINQ 针对客户端对象模型创建查询时，需使用  [LINQ to Objects](http://msdn.microsoft.com/library/bb397919) 而不是 [LINQ to SharePoint](http://msdn.microsoft.com/library/ee535491) 提供程序，只有在对服务器对象模型编写代码时，才能使用 LINQ to SharePoint 提供程序。
  
    
    


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

因为该示例使用 **Include**，所以在执行查询后，只有指定的属性可用。因此，如果您尝试访问其他未指定的属性，将会收到 **PropertyOrFieldNotInitializedException**。此外，如果您尝试使用 **get_contentType** 或 **get_parentList** 等函数访问包含对象的属性，也会收到该错误。
  
    
    

### 对检索项的限制

SharePoint Foundation 2010 中 JavaScript 对象模型的 **loadQuery(clientObjectCollection, exp)** 方法不支持托管对象模型使用的 LINQ 方法和操作。
  
    
    

## 创建、更新和删除列表项
<a name="BasicOps_SPListItemCRUD"> </a>

通过客户端对象模型对列表项执行创建、更新或删除操作与通过服务器对象模型执行这些操作的方式类似。您可以创建列表项对象，设置其属性，然后更新该对象。若要修改或删除列表项对象，可使用 **ListItemCollection** 对象的 **getById(id)** 函数返回该对象，然后对该方法返回的对象设置属性并调用更新，或者调用对象自己的删除方法。与服务器对象模型不同，只有调用 **to executeQueryAsync(succeededCallback, failedCallback)** 才能终止客户端对象模型中的这些操作，从而使所做更改在服务器上生效。
  
    
    

### 创建列表项

若要创建列表项，可以创建一个 **ListItemCreationInformation** 对象，设置其属性，并将其作为参数传递给 **List** 对象的 **addItem(parameters)** 函数。设置此方法返回的列表项对象的属性，然后调用 **update()** 函数，如以下示例所示。
  
    
    

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


### 更新列表项

若要设置大多数列表项属性，可以使用列索引器进行工作分配，然后调用 **update()** 函数，以使所做更改在调用 **executeQueryAsync(succeededCallback, failedCallback)** 时生效。下面的示例设置 Announcements 列表中第三项的标题。
  
    
    

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


### 删除列表项

若要删除列表项，请对该对象调用 **deleteObject()** 函数。下面的示例使用 **getItemById(id)** 函数返回列表中的第二项，然后删除该项。SharePoint 保留集合中项的整数 ID，即使这些项已被删除。这样一来，列表中的第二个项可能将整数 2 用作其标识符。如果对不存在的项调用 **deleteObject()** 函数，将返回 **ServerException**。
  
    
    

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

例如，若要检索执行删除操作而产生的新项计数，请包含对 update() 方法的调用以刷新列表。此外，还必须先加载列表对象本身或列表对象的 **itemCount** 属性，然后才能执行查询。若要检索列表项的开始计数和结束计数，则必须执行两次查询并返回项计数两次，下面显示对上一示例所做的修改。
  
    
    



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


## 访问主机 Web 中的对象
<a name="BasicOps_AccessHostweb"> </a>

在开发加载项时，您可能需要访问主机 Web 来与其中的项进行交互。使用 **AppContextSite** 对象可引用主机 Web 或其他 SharePoint 网站，如以下示例所示。有关完整代码示例，请参阅 [使用跨域库获取主机 Web 标题 (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)。
  
    
    

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

前面的示例使用 SharePoint 2013 中的跨域库来访问主机 Web。有关详细信息，请参阅 [使用跨域库从外接程序访问 SharePoint 2013 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
  
    
    

## 其他资源
<a name="BasicOps_AddRes"> </a>


-  [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md)
    
  

