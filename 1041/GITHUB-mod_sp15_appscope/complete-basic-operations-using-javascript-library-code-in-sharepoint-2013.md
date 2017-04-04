---
title: SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する
SharePoint 2013 で JavaScript クライアント オブジェクト モデルを使用して基本的な操作を実行するコードの記述方法について説明します。
> **メモ**
> JavaScript ライブラリを使用する "Hello World" レベルのサンプル SharePoint アドインについては、「 [SharePoint データで動作するように SharePoint JavaScript API を使用する](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md)」をご覧ください。 
  
    
    


## SharePoint 2013 のクライアント API
<a name="ClientAPIs"> </a>

SharePoint 2013 では、SharePoint クライアント オブジェクト モデルを使用して、データを取得、更新、および管理できます。SharePoint では、オブジェクト モデルをいくつかの形式で提供しています。
  
    
    

- .NET Framework 再頒布可能アセンブリ
    
  
- JavaScript ライブラリ
    
  
- REST/OData エンドポイント
    
  
- Windows Phone アセンブリ
    
  
- Silverlight 再頒布可能アセンブリ
    
  
SharePoint 2013 で使用可能な API セットの詳細については、「 [SharePoint 2013 での適切な API セットの選択](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)」を参照してください。
  
    
    
この記事では、JavaScript オブジェクト モデルを使用して基本的な操作を実行する方法を示します。HTML <script> タグを使用してオブジェクト モデルへの参照を追加できます。その他のクライアント API の使用方法については、以下の記事を参照してください。
  
    
    

-  [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [SharePoint 2013 にアクセスする Windows Phone アプリの作成](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)
    
  
-  [Silverlight オブジェクト モデルを使用する](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)
    
  

## SharePoint 2013 での JavaScript クライアント オブジェクト モデルを使用した基本的なタスクの実行
<a name="BasicOps_SPJSOMOps"> </a>

以降のセクションでは、プログラムによって実行できるタスクについて説明します。また、実際の操作を示す JavaScript のコード例も含まれています。
  
    
    
クラウドでホストされるアドインを作成する場合、HTML <script> タグを使用してオブジェクト モデルへの参照を追加できます。クラウドでホストされるアドインでは、すべてのシナリオでアドイン Web が存在するわけではないため、ホスト Web を参照することをお勧めします。 **{StandardTokens}** トークンを使用している場合、 _SPHostUrl_ クエリ文字列パラメーターからホスト Web の URL を取得できます。 **{HostUrl}** トークンを使用している場合は、カスタム定義されたクエリ文字列パラメーターを使用することもできます。ホスト Web の URL を取得したら、JavaScript コードを使用して、オブジェクト モデルへの参照を動的に作成する必要があります。
  
    
    
下記のコード例では、以下のタスクが実行され、JavaScript オブジェクト モデルへの参照が追加されます。
  
    
    

- Microsoft コンテンツ配信ネットワーク (CDN: Content Delivery Network) から AJAX ライブラリを参照します。
    
  
- Microsoft CDN から jQuery ライブラリを参照します。
    
  
- クエリ文字列からホスト Web の URL を抽出します。
    
  
- jQuery で **getScript** 関数を使用して SP.Runtime.js ファイルと SP.js ファイルを読み込みます。ファイルの読み込みが完了すると、プログラムから SharePoint の JavaScript オブジェクト モデルにアクセスできるようになります。
    
  
- **execOperation** 関数でフローを続行します。
    
  


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

SharePoint ホスト型アドインを作成する場合、HTML <script> タグを使用してオブジェクト モデルへの参照を追加できます。SharePoint によってホストされるアドインのアドイン Web を使用すると、JavaScript オブジェクト モデルを使用するために必要なファイルを相対パスで参照できるようになります。
  
    
    
下記のマークアップでは、次のタスクが実行され、JavaScript オブジェクト モデルへの参照が追加されます。
  
    
    

- Microsoft CDN から AJAX ライブラリを参照します。
    
  
- アドイン Web に対する相対 URL を使用して SP.Runtime.js ファイルを参照します。
    
  
- アドイン Web に対する相対 URL を使用して SP.js ファイルを参照します。
    
  


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

</script>```


## SharePoint Web サイトのタスク
<a name="BasicOps_SPWebTasks"> </a>

JavaScript を使用して Web サイトを操作するには、最初に **ClientContext(serverRelativeUrl)** コンストラクターを使用し、URL または URI を渡して特定の要求コンテキストを返します。
  
    
    

### Web サイトのプロパティを取得する

 **ClientContext** クラスの Web プロパティを使用して、指定されたコンテキスト URL に存在する Web サイト オブジェクトのプロパティを指定します。 **load(clientObject)** メソッドを使用して Web サイト オブジェクトを読み込み、 **executeQueryAsync(succeededCallback, failedCallback)** を呼び出すと、この Web サイトのすべてのプロパティにアクセスできるようになります。次の例では、指定した Web サイトのタイトルと説明が表示されますが、既定で返される他のすべてのプロパティが使用可能になるのは、Web サイト オブジェクトを読み込んでクエリを実行した後です。
  
    
    
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
}```


### Web サイトの選択したプロパティのみを取得する

クライアントとサーバーの間で不要なデータが転送されることを抑えるため、Web サイト オブジェクトのすべてのプロパティではなく、指定したプロパティのみを返すこともできます。この場合、LINQ クエリ、または **load(clientObject)** メソッドとラムダ式構文を使用して、サーバーから返すプロパティを指定します。次の例では、 **executeQueryAsync(succeededCallback, failedCallback)** を呼び出した後で、Web サイト オブジェクトのタイトルと作成日付のみが使用可能になります。
  
    
    
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
}```


> **メモ**
> 他のプロパティにアクセスしようとした場合、他のプロパティは使用できないため、コードで例外がスローされます。 
  
    
    


### Web サイトのプロパティに書き込む

Web サイトを変更するには、サイトのプロパティを設定し、 **update()** メソッドを呼び出します。この方法はサーバー オブジェクト モデルの機能の仕方に似ていますが、クライアント オブジェクト モデルでは、 **executeQueryAsync(succeededCallback, failedCallback)** を呼び出して、指定したすべてのコマンドのバッチ処理を要求する必要があります。次の例は、指定した Web サイトのタイトルと説明を変更します。
  
    
    
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
}```


## SharePoint リストのタスク
<a name="BasicOps_SPListTasks"> </a>

JavaScript を使用してリスト オブジェクトを操作する方法は、Web サイト オブジェクトの操作方法と似ています。まず、 **ClientContext(serverRelativeUrl)** コンストラクターを使用して URL または URI を渡し、特定の要求コンテキストを返します。次に、 **Web** クラスの **lists** プロパティを使用して、Web サイト内のリストのコレクションを取得できます。
  
    
    

### Web サイト内のすべてのリストのすべてのプロパティを取得する

Web サイトのすべてのリストを返すには、 **load(clientObject)** メソッドを使用してリスト コレクションを読み込んだ後に、 **executeQueryAsync(succeededCallback, failedCallback)** を呼び出します。次の例は、Web サイトの URL とリストの作成日時を表示します。
  
    
    
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
}```


### リストの特定のプロパティのみを表示する

前述の例では、Web サイト内のリストのすべてのプロパティが返されます。クライアントとサーバー間の不必要なデータ転送を削減するために、LINQ クエリ式を使用して、返すプロパティを指定できます。JavaScript では、 **load(clientObject)** メソッドに渡されるクエリ文字列の一部として、 **Include** を指定し、返すプロパティを指定します。次の例では、この方法を使用して、コレクション内の各リストのタイトルと ID のみが返されます。
  
    
    
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


### 取得したリストをコレクションに格納する

次の例に示すように、 **load(clientObject)** メソッドの代わりに **loadQuery(clientObjectCollection, exp)** メソッドを使用して、戻り値を lists プロパティに格納する代わりに別のコレクションに格納できます。
  
    
    
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
}```


### リストの取得にフィルターを適用する

次の例のように、JavaScript クエリ内の **Include** ステートメントをネストして、リストとそのフィールドの両方のメタデータを返すことができます。例では、Web サイト内のすべてのリストからすべてのフィールドが返され、内部名に文字列 "name" が含まれるすべてのフィールドのタイトルと内部名が表示されます。
  
    
    
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


## リストの作成、更新、削除
<a name="BasicOps_SPListCRUD"> </a>

クライアント オブジェクト モデルを使用して行うリストの作成、更新、および削除は, .NET クライアント オブジェクト モデルを使用してこれらの操作を行う場合と同じように動作します。ただし、クライアントの処理は、 **executeQueryAsync(succeededCallback, failedCallback)** 関数を呼び出すまで完了しません。
  
    
    

### リストを作成および更新する

JavaScript を使用してリスト オブジェクトを作成するには、 **ListCreationInformation** オブジェクトを使用してそのプロパティを定義し、そのオブジェクトを **ListCollection** オブジェクトの **add(parameters)** 関数に渡します。次の例では、新しいお知らせリストを作成しています。
  
    
    
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
}```

リストを作成した後でリストを更新する必要がある場合は、前の例に変更を加えた次の例に示すように、リスト プロパティを設定し、 **update()** 関数を呼び出してから **executeQueryAsync(succeededCallback, failedCallback)** を呼び出すことができます。
  
    
    


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
);```


### リストにフィールドを追加する

リストのフィールド コレクションにフィールドを追加するには、 **FieldCollection** オブジェクトの **add(field)** 関数または **addFieldAsXml(schemaXml, addToDefaultView, options)** 関数を使用します。次の例では、フィールドを作成し、更新してから、 **executeQueryAsync(succeededCallback, failedCallback)** を呼び出しています。
  
    
    
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
}```


### リストを削除する

リストを削除するには、リスト オブジェクトの **deleteObject()** 関数を呼び出します。以下に例を示します。
  
    
    
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
}```


## フォルダーの作成、更新、削除
<a name="BasicOps_FolderTasks"> </a>

JavaScript オブジェクト モデルを使用してフォルダーを操作し、内容を整理できます。以下のセクションでは、フォルダーに対する基本的な操作を実行する方法を示します。
  
    
    

### ドキュメント ライブラリにフォルダーを作成する

フォルダーを作成するには、 **ListItemCreationInformation** オブジェクトを使用して、基になるオブジェクト タイプを **SP.FileSystemObjectType.folder** に設定し、それをパラメーターとして **List** オブジェクトの **addItem(parameters)** 関数に渡します。次の例に示すように、このメソッドによって返されるリスト アイテム オブジェクトのプロパティを設定した後、 **update()** 関数を呼び出します。
  
    
    
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
}```


### ドキュメント ライブラリのフォルダーを更新する

フォルダー名を更新するには、 **FileLeafRef** プロパティに書き込んで、 **update()** 関数を呼び出し、 **executeQueryAsync** メソッドを呼び出したときに変更が有効になるようにします。
  
    
    
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
}```


### ドキュメント ライブラリのフォルダーを削除する

フォルダーを削除するには、オブジェクトの **deleteObject()** 関数を呼び出します。次の例では、 **getFolderByServerRelativeUrl** メソッドを使用してドキュメント ライブラリからフォルダーを取得し、アイテムを削除しています。
  
    
    
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
}```


## ファイルの作成、読み取り、更新、削除
<a name="BasicOps_FileTasks"> </a>

JavaScript オブジェクト モデルを使用してファイルを操作できます。以下のセクションでは、ファイルに対する基本的な操作を実行する方法を示します。
  
    
    

> **メモ**
> JavaScript オブジェクト モデルを使用して操作できるファイルのサイズは 1.5 MB までです。それより大きいファイルをアップロードするには、REST (Representational State Transfer) を使用します。詳細については、「 [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles)」を参照してください。 
  
    
    


### ドキュメント ライブラリにファイルを作成する

ファイルを作成するには、次の例に示すように、 **FileCreationInformation** オブジェクトを使用して、URL 属性を設定し、Base64 エンコードしたバイトの配列としてコンテンツを追加します。
  
    
    
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
}```


### ドキュメント ライブラリのファイルの読み取り

ファイルのコンテンツを読み取るには、次の例に示すように、ファイルの URL に対して **GET** 操作を実行します。
  
    
    
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
}```


### ドキュメント ライブラリのファイルの更新

ファイルのコンテンツを更新するには、次の例に示すように、 **FileCreationInformation** オブジェクトを使用し、 **set_overwrite()** メソッドを使用して上書き属性を true に設定します。
  
    
    
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
}```


### ドキュメント ライブラリのファイルを削除する

ファイルを削除するには、オブジェクトの **deleteObject()** 関数を呼び出します。次の例では、 **getFileByServerRelativeUrl** メソッドを使用してドキュメント ライブラリからファイルを取得し、アイテムを削除しています。
  
    
    
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
}```


## SharePoint リスト アイテムのタスク
<a name="BasicOps_SPListItemTasks"> </a>

JavaScript を使用してリストからアイテムを返すには、 **getItemById(id)** 関数を使用して単一のアイテムを返すか、 **getItems(query)** 関数を使用して複数のアイテムを返します。その後、 **load(clientObject)** 関数を使用して、アイテムを表すリスト アイテム オブジェクトを取得します。
  
    
    

### リストからアイテムを取得する

 **getItems(query)** 関数を使用すると、返されるアイテムを指定する Collaborative Application Markup Language (CAML) クエリを定義できます。未定義の **CamlQuery** オブジェクトを渡してリストからすべてのアイテムを返すことも、 **set_viewXml** 関数を使用して CAML クエリを定義し、特定の条件を満たすアイテムを返すこともできます。以下の例では、Title および Body 列の値に加えて、Announcements リストに含まれる、コレクション ID が 10 より大きい最初の 100 アイテムの ID を表示します。
  
    
    
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
}```


### Include メソッドを使用して ListItem オブジェクトのプロパティにアクセスする

リスト アイテムを返す場合、既定では、 **ListItem** オブジェクトの 4 つのプロパティ ( **displayName**、 **effectiveBasePermissions**、 **hasUniqueRoleAssignments**、および **roleAssignments**) を使用できません。前の例では、これらのプロパティの 1 つにアクセスを試みた場合、 **PropertyOrFieldNotInitializedException** が返されます。これらのプロパティにアクセスするには、次の例に示されているように、 **Include** メソッドをクエリ文字列の一部として使用します。
  
    
    

> **メモ**
> LINQ を使用してクライアント オブジェクト モデルに対するクエリを作成する場合は、サーバー オブジェクト モデルに対するコードを記述する場合にしか使用できない  [LINQ to SharePoint の使用](http://msdn.microsoft.com/ja-jp/library/ee535491.aspx)ではなく、 [LINQ to Objects](http://msdn.microsoft.com/ja-jp/library/bb397919.aspx) を使用します。
  
    
    

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

この例では **Include** を使用しているので、クエリを実行した後は指定したプロパティのみを使用できます。したがって、指定されているもの以外のプロパティにアクセスしようとすると、 **PropertyOrFieldNotInitializedException** を受け取ります。また、 **get_contentType**、 **get_parentList** など、関数を使用して親オブジェクトのプロパティにアクセスしようとしても、このエラーを受け取ります。
  
    
    

### アイテムの取得に関する制限事項

SharePoint Foundation 2010 内の JavaScript オブジェクト モデルの **loadQuery(clientObjectCollection, exp)** メソッドでは、マネージ オブジェクト モデルで使用される LINQ メソッドと演算子がサポートされません。
  
    
    

## リスト アイテムの作成、更新、削除
<a name="BasicOps_SPListItemCRUD"> </a>

クライアント オブジェクト モデルでは、サーバー オブジェクト モデルとほぼ同じように、リスト アイテムを作成、更新、または削除できます。リスト アイテム オブジェクトを作成し、そのプロパティを設定して、オブジェクトを更新します。リスト アイテム オブジェクトを変更または削除するには、 **ListItemCollection** オブジェクトの **getById(id)** 関数を使用してオブジェクトを返します。次に、プロパティを設定してこのメソッドによって返されるオブジェクトの更新を呼び出すか、オブジェクト独自の削除メソッドを呼び出します。サーバー オブジェクト モデルと異なり、クライアント オブジェクト モデルでは、それぞれの操作の最後で **to executeQueryAsync(succeededCallback, failedCallback)** を呼び出して、サーバー上に変更を反映する必要があります。
  
    
    

### リスト アイテムを作成する

リスト アイテムを作成するには、 **ListItemCreationInformation** オブジェクトを作成し、プロパティを設定して、それをパラメーターとして **List** オブジェクトの **addItem(parameters)** 関数に渡します。次の例で示すように、このメソッドによって返されるリスト アイテム オブジェクトのプロパティを設定し、 **update()** 関数を呼び出します。
  
    
    
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
}```


### リスト アイテムを更新する

ほとんどの場合、リスト アイテム プロパティを設定するには、列インデクサーを使用して割り当てを作成し、 **executeQueryAsync(succeededCallback, failedCallback)** を呼び出すときに、 **update()** 関数を呼び出して変更を反映できます。次の例では、Announcements リストの 3 番目のアイテムのタイトルを設定しています。
  
    
    
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
}```


### リスト アイテムを削除する

リスト アイテムを削除するには、オブジェクトで **deleteObject()** 関数を呼び出します。次の例では、 **getItemById(id)** 関数を使用してリストの 2 番目のアイテムを返し、そのアイテムを削除しています。SharePoint では、コレクション内のアイテムを削除しても、そのアイテムの整数の ID が保持されます。したがって、たとえば、リスト内の 2 番目のアイテムの ID が 2 にならない場合があります。存在しないアイテムに対して **deleteObject()** 関数が呼び出された場合は、 **ServerException** が返されます。
  
    
    
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
}```

たとえば、削除操作によって更新された新しいアイテム数を取得する必要がある場合は、update() メソッドへの呼び出しを含めて、リストを更新します。また、クエリを実行する前に、リスト オブジェクト自体、またはリスト オブジェクト上の **itemCount** プロパティのどちらかを読み込む必要があります。リスト アイテムの開始数と終了数の両方を取得する場合は、前の例に次の変更を加えて、2 つのクエリを実行し、アイテム数を 2 回返す必要があります。
  
    
    


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
}```


## ホスト Web のオブジェクトへのアクセス
<a name="BasicOps_AccessHostweb"> </a>

アドインの開発中に、ホスト Web にアクセスしてその中のアイテムを操作することが必要になる場合があります。次の例に示すように、 **AppContextSite** オブジェクトを使用して、ホスト Web やその他の SharePoint サイトを参照します。完全なコード サンプルについては、「 [クロスドメイン ライブラリ (JSOM) を使用してホスト Web タイトルを取得する](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)」を参照してください。
  
    
    
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
}```

前の例では、SharePoint 2013 のクロスドメイン ライブラリを使用してホスト Web にアクセスしています。詳細については、「 [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)」を参照してください。
  
    
    

## その他の技術情報
<a name="BasicOps_AddRes"> </a>


-  [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md)
    
  

