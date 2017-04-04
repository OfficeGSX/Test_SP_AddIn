---
title: Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013
Сведения о том, как писать код для выполнения базовых операций с помощью клиентской объектной модели JavaScript в SharePoint 2013.
> [!Примечание]
> Пример создания Надстройка SharePoint "Hello World", использующей библиотеку JavaScript, см. в статье  [Использование API JavaScript для SharePoint для работы с данными SharePoint](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md). 
  
    
    


## Клиентские API-интерфейсы SharePoint 2013
<a name="ClientAPIs"> </a>

Вы можете использовать клиентскую объектную модель SharePoint для извлечения, обновления данных и управления ими в SharePoint 2013. SharePoint предоставляет объектные модели в нескольких формах.
  
    
    

- Распространяемые сборки .NET Framework
    
  
- Библиотека JavaScript
    
  
- конечные точки REST и OData;
    
  
- Сборки Windows Phone
    
  
- Распространяемые сборки Silverlight
    
  
Для получения дополнительной информации о наборах API-интерфейсов, доступных для SharePoint 2013, см.  [Выбор правильного набора API в SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
В этой статье описано, каким образом следует выполнять базовые операции с помощью объектной модели JavaScript. Вы можете добавить ссылку на объектную модель, используя HTML-теги <script>. Дополнительные сведения об использовании других клиентских API-интерфейсов см. в разделе
  
    
    

-  [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Построение приложений Windows Phone, обращающихся к SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)
    
  
-  [Использование объектной модели Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)
    
  

## Выполнение базовых задач в SharePoint 2013 с использованием клиентской объектной модели JavaScript
<a name="BasicOps_SPJSOMOps"> </a>

В следующих разделах описаны задачи, которые можно выполнять программно, и они включают примеры кода JavaScript, демонстрирующие выполнение этих операций.
  
    
    
При создании надстройки с размещением в облаке можно добавить ссылку на объектную модель с помощью HTML-тегов <script>. Мы рекомендуем создать ссылку на хост-сайт, так как сайт надстройки может существовать не в каждом сценарии работы с надстройками, размещаемыми в облаке. Вы можете извлечь URL-адрес хост-сайта из параметра строки запроса  _SPHostUrl_, если вы используете маркер **{StandardTokens}**. Вы также можете использовать пользовательский параметр строки запроса, если вы применяете маркер **{HostUrl}**. После получения URL-адреса хост-сайта вы должны использовать код JavaScript для динамичного создания ссылки на объектную модель.
  
    
    
В следующем примере кода выполняются следующие задачи для добавления ссылки на объектную модель JavaScript:
  
    
    

- Создание ссылки на библиотеку AJAX из сети Microsoft Content Delivery Network (CDN).
    
  
- Создание ссылки на библиотеку jQuery из сети Microsoft CDN.
    
  
- Извлечение URL-адреса хост-сайта из строки запроса.
    
  
- Загрузка файлов SP.Runtime.js и SP.js с помощью функции **getScript** в jQuery. После загрузки файлов программа получает доступ к объектной модели JavaScript для SharePoint.
    
  
- Продолжение рабочего процесса функции **execOperation**.
    
  



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

При создании надстройки с размещением в SharePoint можно добавить ссылку на объектную модель с помощью HTML-тегов <script>. Сайт надстройки для SharePoint позволяет использовать относительные пути для ссылок на нужные файлы, используемые в объектной модели JavaScript.
  
    
    
Следующая разметка выполняет указанные далее задачи для добавления ссылки на объектную модель JavaScript:
  
    
    

- Создание ссылки на библиотеку AJAX из сети Microsoft CDN.
    
  
- Создание ссылки на файл SP.Runtime.js с использованием относительного URL-адреса сайта надстройки.
    
  
- Создание ссылки на файл SP.js с использованием относительного URL-адреса сайта надстройки.
    
  



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


## Задачи, связанные с веб-сайтом SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Для работы с веб-сайтами с использованием JavaScript начните с использования конструктора **ClientContext(serverRelativeUrl)** и передачи URL-адреса или URI-кода для возврата определенного контекста запроса.
  
    
    

### Получите свойства веб-сайта

Используйте веб-свойства класса **ClientContext** для указания свойств объекта веб-сайта, которые размещены по указанному URL-адресу контекста. После загрузки объекта веб-сайта с помощью метода **load(clientObject)** и вызова **executeQueryAsync(succeededCallback, failedCallback)** можно получить доступ ко всем свойствам этого веб-сайта. В следующем примере отображается заголовок и описание определенного веб-сайта, хотя все другие возвращаемые свойства по умолчанию становятся доступными после загрузки объекта веб-сайта и выполнения запроса.
  
    
    

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


### Получение только выбранных свойств веб-сайта

Для снижения объема передаваемых данных между клиентом и сервером можно получать только указанные свойства объекта веб-сайта, а не все свойства. В этом случае используйте запрос LINQ или синтаксис лямбда-выражений с методом **load(clientObject)** для определения того, какие свойства следует получать от сервера. В следующем примере после вызова запроса **executeQueryAsync(succeededCallback, failedCallback)** доступны только заголовок и дата создания объекта веб-сайта.
  
    
    

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


> [!Примечание]
> Если вы пытаетесь получить доступ к другим свойствам, код создает исключение, потому что остальные свойства недоступны. 
  
    
    


### Запись в свойства веб-сайта

Для изменения веб-сайта следует задать его свойства и вызвать метод **update()** аналогично применению серверной объектной модели. Однако в клиентской объектной модели следует вызвать **executeQueryAsync(succeededCallback, failedCallback)** для запроса пакетной обработки всех указанных команд. В следующем примере изменяется заголовок и описание указанного веб-сайта.
  
    
    

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


## Задачи по работе со списками SharePoint
<a name="BasicOps_SPListTasks"> </a>

Работа с объектами списков с использованием JavaScript похожа на работу с объектами веб-сайтов. Начинайте работу с использования конструктора **ClientContext(serverRelativeUrl)** и передачи URL-адреса или URI-кода для возврата определенного контекста запроса. После этого можно использовать свойство **lists** класса **Web** для получения коллекции списков на веб-сайте.
  
    
    

### Извлечение всех свойств всех списков на веб-сайте

Для возврата всех списков веб-сайта загрузите коллекцию списков с помощью метода **load(clientObject)**, а затем вызовите **executeQueryAsync(succeededCallback, failedCallback)**. В следующем примере представлен URL-адрес веб-сайта, а также дата и время создания списка.
  
    
    

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


### Извлечение только заданных свойств списков

В предыдущем примере выполнялся возврат всех свойств списков на веб-сайте. Для уменьшения ненужных данных, передаваемых между клиентом и сервером, можно использовать выражения запросов LINQ, чтобы указать возвращаемые свойства. В JavaScript следует указать **Include** как часть строки запроса, который передается в метод **load(clientObject)** для указания возвращаемых свойств. В следующем примере этот подход используется для возврата только заголовка и идентификатора каждого из списков в коллекции.
  
    
    

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


### Хранение полученных списков в коллекции

Как показывает следующий пример, вы можете использовать метод **loadQuery(clientObjectCollection, exp)** вместо метода **load(clientObject)** для хранения возвращаемого значения в другой коллекции вместо его сохранения в свойстве списков.
  
    
    

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


### Применение фильтров к извлечению списков

Как показано в следующем примере, операторы **Include** можно вкладывать в запрос JavaScript, чтобы возвратить метаданные как для списка, так и для его полей. В примере возвращаются все поля из всех списков на веб-сайте, а также отображаются заголовок и внутреннее имя всех полей, внутреннее имя которых содержит строку "name".
  
    
    

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


## Создание, обновление и удаление списков
<a name="BasicOps_SPListCRUD"> </a>

Создание, обновление и удаление списков с использованием клиентской объектной модели аналогично выполнению этих операций с помощью серверной объектной модели .NET, хотя клиентские операции не будут завершены до вызова функции **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    

### Создание и обновление списка

Для создания объекта списка с помощью JavaScript, используйте объект **ListCreationInformation**, чтобы определить его свойства, и передайте объект функции **add(parameters)** объекта **ListCollection**. В следующем примере создается новый список объявлений.
  
    
    

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

Если нужно обновить список после его создания, можно задать свойства списка и вызвать функцию **update()** перед вызовом **executeQueryAsync(succeededCallback, failedCallback)**, как показано далее в измененном примере.
  
    
    



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


### Добавление поля в список

Используйте функцию **add(field)** или **addFieldAsXml(schemaXml, addToDefaultView, options)** объекта **FieldCollection** для добавления поля в коллекцию полей списка. В следующем примере создается поле, которое затем обновляется перед вызовом **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    

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


### Удаление списка

Чтобы удалить список, вызовите функцию **deleteObject()** объекта списка, как показано в примере ниже.
  
    
    

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


## Создание, обновление и удаление папок
<a name="BasicOps_FolderTasks"> </a>

Вы можете работать с папками, упорядочивая контент, с помощью объектной модели JavaScript. В следующих разделах рассказывается об основных операциях с папками.
  
    
    

### Создание папки в библиотеке документов

Чтобы создать папку, нужно использовать объект **ListItemCreationInformation**, задав базовый тип как **SP.FileSystemObjectType.folder**, и передать его в параметре функции **addItem(parameters)** объекту **List**. Задайте свойства объекта элемента списка, возвращенного методом, и вызовите функцию **update()**, как показано в следующем примере.
  
    
    

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


### Изменение папки в библиотеке документов

Чтобы изменить имя папки, вы можете записать его в свойство **FileLeafRef** и вызвать функцию **update()**, чтобы изменения вступили в силу при вызове метода **executeQueryAsync**.
  
    
    

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


### Удаление папки в библиотеке документов

Чтобы удалить папку, следует вызвать функцию **deleteObject()** объекта. В следующем примере метод **getFolderByServerRelativeUrl** используется для извлечения папки из библиотеки документов и удаления элемента.
  
    
    

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


## Создание, чтение, обновление и удаление файлов
<a name="BasicOps_FileTasks"> </a>

Вы можете работать с файлами с помощью объектной модели JavaScript. В следующих разделах рассказывается об основных операциях с файлами.
  
    
    

> [!Примечание]
> С помощью объектной модели JavaScript можно работать только с файлами размером до 1,5 МБ. Чтобы отправлять более крупные файлы, используйте REST. Дополнительные сведения см. в разделе  [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles). 
  
    
    


### Создание файла в библиотеке документов

Для создания файлов используйте объект **FileCreationInformation**, задав атрибут URL и добавив контент как байтовый массив в кодировке Base64, как показано в следующем примере.
  
    
    

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


### Чтение файла в библиотеке документов

Для чтения контента файла используйте операцию **GET** с URL-адресом файла, как показано в следующем примере.
  
    
    

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


### Изменение файла в библиотеке документов

Чтобы изменить контент файла, используйте объект **FileCreationInformation**, задав атрибут перезаписи равным "true" с помощью метода **set_overwrite()**, как показано в следующем примере.
  
    
    

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


### Удаление файла в библиотеке документов

Чтобы удалить файл, следует вызвать функцию **deleteObject()** объекта. В следующем примере метод **getFileByServerRelativeUrl** используется для извлечения файла из библиотеки документов и удаления элемента.
  
    
    

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


## Задачи, связанные с элементами списков SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Для получения элементов из списка с помощью JavaScript используйте функцию **getItemById(id)** для возврата одного элемента или функцию **getItems(query)** для извлечения нескольких элементов. Затем используется функция **load(clientObject)** для получения объектов, представляющих элементы списка.
  
    
    

### Получение элементов из списка

Функция **getItems(query)** позволяет задавать запрос Язык CAML, который определяет возвращаемые элементы. Вы можете передать неопределенный объект **CamlQuery** для возврата всех элементов из списка или использовать функцию **set_viewXml** для определения запроса CAML и возврата элементов, которые отвечают определенным критериям. В следующем примере отображается идентификатор (помимо значений столбцов Title и Body) первых 100 элементов списка объявлений, начиная с элементов списка, идентификатор коллекции которых превышает 10.
  
    
    

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


### Используйте метод Include для доступа к свойствам объектов ListItem

Четыре свойства объектов **ListItem** недоступны по умолчанию при возврате элементов списка: **displayName**, **effectiveBasePermissions**, **hasUniqueRoleAssignments** и **roleAssignments**. В предыдущем примере, если попытаться получить доступ к одному из этих свойств, возвращается исключение **PropertyOrFieldNotInitializedException**. Для доступа к этим свойствам используйте метод **Include** как часть строки запроса, как показано в следующем примере.
  
    
    

> [!Примечание]
> При создании запросов для клиентской объектной модели с помощью LINQ применяется поставщик  [LINQ to Objects](http://msdn.microsoft.com/library/bb397919.aspx), а не  [LINQ to SharePoint](http://msdn.microsoft.com/library/ee535491.aspx), который можно использовать только при написании кода для серверной 
  
    
    


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

Так как в этом примере используется **Include**, после выполнения запроса доступны только указанные свойства. Поэтому возвращается исключение **PropertyOrFieldNotInitializedException** при попытке доступа к другим свойствам помимо указанных. Кроме того, эта ошибка возникает, если вы пытаетесь использовать такие функции как **get_contentType** или **get_parentList** с целью доступа к объектам, которые они содержат.
  
    
    

### Ограничения получения объектов

Метод **loadQuery(clientObjectCollection, exp)** объектной модели JavaScript в SharePoint Foundation 2010 не поддерживает методы и операторы LINQ, которые используются в управляемой объектной модели.
  
    
    

## Создание, обновление и удаление элементов списков
<a name="BasicOps_SPListItemCRUD"> </a>

Создание, обновление и удаление элементов списка с помощью клиентской объектной модели работает аналогично выполнению этих задач с помощью серверной объектной модели. Можно создать объект элемента списка, установить его свойства, а затем обновить этот объект. Чтобы изменить или удалить объект "list item", необходимо вернуть этот объект с помощью функции **getById(id)** объекта **ListItemCollection**, а затем либо установить свойства и вызвать обновление в объекте, возвращенном методом, либо вызвать собственный метод объекта для удаления. В отличие от серверной объектной модели, каждая из этих операций в клиентской объектной модели должна завершаться вызовом метода **to executeQueryAsync(succeededCallback, failedCallback)**, чтобы изменения вступили в силу на сервере
  
    
    

### Создание элемента списка

Чтобы создать элементы списка, следует создать объект **ListItemCreationInformation**, установить его свойства и передать его как параметр в функцию **addItem(parameters)** объекта **List**. Затем устанавливаются свойства элемента списка, который возвращает этот метод, и вызывается функция **update()**, как показано в следующем примере.
  
    
    

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


### Обновление элемента списка

Для установки большинства свойств элемента списка можно с помощью индексатора столбца создать назначение, а затем вызвать функцию **update()**, чтобы изменения вступили в силу при вызове **executeQueryAsync(succeededCallback, failedCallback)**. В следующем примере устанавливается заголовок третьего элемента списка объявлений.
  
    
    

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


### Удаление элемента списка

Чтобы удалить элемент списка, следует вызвать функцию **deleteObject()** объекта. В следующем примере используется функция **getItemById(id)** для возврата второго элемента списка, а затем выполняется удаление элемента. SharePoint обслуживает целочисленные идентификаторы элементов в коллекциях, даже если эти элементы удаляются. Поэтому, например, второй элемент списка должен иметь идентификатор, отличный от 2. **ServerException** возвращается, если функция **deleteObject()** вызывается для несуществующего элемента.
  
    
    

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

Если, например, требуется получить новое количество элементов, появившееся в результате операции удаления, следует включить вызов метода update(), чтобы обновить список. Кроме того, перед выполнением запроса необходимо загрузить либо сам объект "list", либо его свойство **itemCount**. Если требуется получить как исходное, так и итоговое количество элементов списка, то необходимо выполнить два запроса и получить число элементов дважды, как показано в следующем варианте предыдущего примера.
  
    
    



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


## Доступ к объектам на хост-сайте
<a name="BasicOps_AccessHostweb"> </a>

При разработке надстройки может потребоваться доступ к хост-сайту для работы с элементами на нем. Используйте объект **AppContextSite** для ссылки на хост-сайт или другие сайты SharePoint, как показано в следующем примере. Полный пример кода см. в разделе [Получение названия хост-сайта с помощью междоменной библиотеки (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d).
  
    
    

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

В предыдущем примере для доступа к хост-сайту используется междоменная библиотека в SharePoint 2013. Дополнительные сведения см. в разделе  [Обращение к данным SharePoint 2013 из надстроек с помощью междоменной библиотеки](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Дополнительные ресурсы
<a name="BasicOps_AddRes"> </a>


-  [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Разработка надстроек для SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Безопасный доступ к данным и клиентские объектные модели для надстроек SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Работа с внешними данными в SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  

