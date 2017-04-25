---
title: Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013
In diesem Artikel erfahren Sie, wie Sie Code zum Ausführen grundlegender Vorgänge unter Verwendung des JavaScript-Clientobjektmodells in SharePoint 2013 schreiben.
> **HINWEIS**
> Eine Beispiel-SharePoint-Add-In mit der Komplexität von „Hello World", das die JavaScript-Bibliothek verwendet, finden Sie unter  [Verwenden von SharePoint-JavaScript-APIs zum Arbeiten mit SharePoint-Daten](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md). 





## SharePoint 2013 Client-APIs
<a name="ClientAPIs"> </a>

Sie können das SharePoint-Clientobjektmodell zum Abrufen, Aktualisieren und Verwalten von Daten in SharePoint 2013 verwenden. SharePoint macht das Objektmodell in verschiedener Form verfügbar.




- Weitervertreibbare Assemblys für .NET Framework


- JavaScript-Bibliothek


- REST-/OData-Endpunkte


- Windows Phone-Assemblys


- Weitervertreibbare Assemblys für Silverlight


Weitere Information zu den APIs, die für SharePoint 2013 verfügbar sind, finden Sie unter  [Auswählen des richtigen API-Satzes in SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).



In diesem Artikel wird erklärt, wie Sie mithilfe des JavaScript-Objektmodells grundlegende Aufgaben ausführen. Sie können mit den HTML-<script>-Tags einen Verweis auf das Objektmodell hinzufügen. Informationen zur Verwendung anderer Client-APIs finden Sie unter folgenden Themen:




-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Erstellen von Windows Phone-Apps, die auf SharePoint 2013 zugreifen](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)


-  [Verwenden des Silverlight-Objektmodells](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)



## Ausführen grundlegender Aufgaben in SharePoint 2013 unter Verwendung des JavaScript-Clientobjektmodells
<a name="BasicOps_SPJSOMOps"> </a>

In den folgenden Abschnitten werden Aufgaben beschrieben, die Sie programmgesteuert ausführen können. Diese Abschnitte enthalten JavaScript-Codebeispiele zur Veranschaulichung der Vorgänge.



Beim Erstellen eines in der Cloud gehosteten Add-Ins können Sie dem Objektmodell mithilfe der HTML-<script>-Tags einen Verweis hinzufügen. Es wird empfohlen, einen Verweis auf das Hostweb einzufügen, da das Add-In-Web möglicherweise nicht in allen Szenarien von in der Cloud gehosteten Add-Ins verfügbar ist. Sie können die URL des Hostwebs aus dem  _SPHostUrl_-Abfragezeichenfolgenparameter abrufen, wenn Sie das Token **{StandardTokens}** verwenden. Zudem können Sie den benutzerdefinierten Abfragezeichenfolgenparameter verwenden, wenn Sie das **{HostUrl}**-Token verwenden. Nachdem Sie die URL des Hostwebs ermittelt haben, müssen Sie den Verweis auf das Objektmodell mit JavaScript-Code dynamisch erstellen.



Im folgenden Codebeispiel werden die folgenden Aufgaben ausgeführt, um einen Verweis auf das JavaScript-Objektmodell hinzuzufügen:




- Es wird auf die AJAX-Bibliothek aus dem Microsoft Netzwerk für die Inhaltsübermittlung (CDN) verwiesen.


- Es wird auf die jQuery-Bibliothek aus dem Microsoft CDN verwiesen.


- Die URL des Hostwebs wird aus der Abfragezeichenfolge extrahiert.


- Die Dateien "SP.Runtime.js" und "SP.js" werden mithilfe der **getScript**-Funktion in jQuery geladen. Nachdem die Dateien geladen wurden, hat das Programm Zugriff auf das JavaScript-Objektmodell für SharePoint.


- Der Programmablauf wird in der **execOperation**-Funktion fortgesetzt.





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

Beim Erstellen eines in SharePoint gehosteten Add-Ins können Sie mithilfe der HTML-Tags <script> einen Verweis auf das Objektmodell hinzufügen. Mithilfe des Add-In-Webs in einem von SharePoint gehosteten Add-In können Sie relative Pfade verwenden, um auf die erforderlichen Dateien zur Verwendung des JavaScript-Objektmodells zu verweisen.



Im folgenden Markup werden diese Aufgaben ausgeführt, um einen Verweis auf das JavaScript-Objektmodell hinzuzufügen:




- Es wird auf die AJAX-Bibliothek aus dem Microsoft CDN verwiesen.


- Über eine auf das Add-In-Web bezogene URL wird auf die Datei SP.Runtime.js verwiesen.


- Über eine auf das Add-In-Web bezogene URL wird auf die Datei SP.js verwiesen.





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


## SharePoint-Websiteaufgaben
<a name="BasicOps_SPWebTasks"> </a>

Wenn Sie Websites mithilfe von JavaScript bearbeiten möchten, verwenden Sie zuerst den **ClientContext(serverRelativeUrl)**-Konstruktor und übergeben eine URL oder einen URI, um einen bestimmten Anforderungskontext zurückzugeben.




### Abrufen der Eigenschaften einer Website

Verwenden Sie die web-Eigenschaft der **ClientContext**-Klasse, um die Eigenschaften des Website-Objekts abzurufen, das sich an der angegebenen Kontext-URL befindet. Nachdem das Website-Objekt über die **load(clientObject)**-Methode geladen und anschließend **executeQueryAsync(succeededCallback, failedCallback)** aufgerufen wurde, können Sie Zugriff auf alle Eigenschaften dieser Website erhalten. Im folgenden Beispiel werden Titel und Beschreibung der angegebenen Website angezeigt, obwohl alle anderen standardmäßig zurückgegebenen Eigenschaften verfügbar werden, nachdem das Websiteobjekt geladen und die Abfrage ausgeführt wurde.




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


### Abrufen nur ausgewählter Eigenschaften einer Website

Sie können die unnötige Übertragung von Daten zwischen Client und Server reduzieren, indem Sie nicht alle, sondern nur angegebene Eigenschaften des Websiteobjekts zurückgeben. In diesem Fall verwenden Sie LINQ-Abfragen oder die Lambda-Ausdruckssyntax mit der **load(clientObject)**-Methode, um anzugeben, welche Eigenschaften vom Server zurückgegeben werden sollen. Im folgenden Beispiel werden nur der Titel und das Erstellungsdatum des Websiteobjekts verfügbar, nachdem **executeQueryAsync(succeededCallback, failedCallback)** aufgerufen wurde.




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


> **HINWEIS**
> Beim Versuch, auf andere Eigenschaften zuzugreifen, wird eine Ausnahme ausgelöst, weil keine anderen Eigenschaften verfügbar sind. 





### Festlegen der Eigenschaften einer Website

Zum Ändern einer Website legen Sie deren Eigenschaften fest und rufen ähnlich wie im Serverobjektmodell die **update()**-Methode auf. Im Clientobjektmodell müssen Sie jedoch **executeQueryAsync(succeededCallback, failedCallback)** aufrufen, um die Batchverarbeitung aller angegebenen Befehle anzufordern. Im folgenden Beispiel werden der Titel und die Beschreibung einer angegebenen Website geändert.




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


## SharePoint-Listenaufgaben
<a name="BasicOps_SPListTasks"> </a>

Das Arbeiten mit Listenobjekten in JavaScript ähnelt dem Arbeiten mit Websiteobjekten. Beginnen Sie, indem Sie den **ClientContext(serverRelativeUrl)**-Konstruktor verwenden und eine URL oder einen URI übergeben, um einen bestimmten Anforderungskontext zurückzugeben. Sie können mithilfe der **lists**-Eigenschaft der **Web**-Klasse die Auflistung der Listen einer Website abrufen.




### Abrufen aller Eigenschaften aller Listen einer Website

Zum Zurückgeben aller Listen einer Website laden Sie die Listenauflistung über die **load(clientObject)**-Methode, und rufen Sie dann **executeQueryAsync(succeededCallback, failedCallback)** auf. Im folgenden Beispiel werden die URL der Website sowie das Datum und die Uhrzeit der Erstellung der Liste angezeigt.




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


### Abrufen der angegebenen Eigenschaften von Listen

Im vorhergehenden Beispiel wurden alle Eigenschaften der Listen einer Website zurückgegeben. Um unnötige Datenübertragungen zwischen Client und Server zu vermeiden, können Sie mit LINQ-Abfrageausdrücken angeben, welche Eigenschaften zurückgegeben werden sollen. In JavaScript geben Sie mit **Include** innerhalb der Abfragezeichenfolge, die der **load(clientObject)**-Methode übergeben wird, die Eigenschaften an, die zurückgegeben werden sollen. Im folgenden Beispiel werden auf diese Weise nur der Titel und die ID der einzelnen Listen der Auflistung zurückgegeben.




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


### Speichern abgerufener Listen in einer Auflistung

Wie im folgenden Beispiel dargestellt, können Sie die **loadQuery(clientObjectCollection, exp)**-Methode anstelle der **load(clientObject)**-Methode verwenden, um den Rückgabewert in einer anderen Auflistung anstatt in der entsprechenden Listeneigenschaft zu speichern.




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


### Anwenden von Filtern auf den Listenabruf

Wie das folgende Beispiel zeigt, können Sie **Include**-Anweisungen in einer JavaScript-Abfrage schachteln, um Metadaten sowohl für eine Liste als auch für deren Felder zurückzugeben. In diesem Beispiel werden alle Felder aus allen Listen einer Website zurückgegeben und der Titel und der interne Name aller Felder angezeigt, deren interner Name die Zeichenfolge "name" enthält.




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


## Erstellen, Aktualisieren und Löschen von Listen
<a name="BasicOps_SPListCRUD"> </a>

Zum Erstellen, Aktualisieren und Löschen von Listen über das Clientobjektmodell gehen Sie ähnlich vor wie beim Ausführen dieser Aufgaben über das .NET-Clientobjektmodell. Allerdings werden die Clientvorgänge erst abgeschlossen, wenn die **executeQueryAsync(succeededCallback, failedCallback)**-Funktion aufgerufen wird.




### Erstellen und Aktualisieren von Listen

Zum Erstellen eines Listenobjekts mit JavaScript definieren Sie unter Verwendung des **ListCreationInformation**-Objekts dessen Eigenschaften. Übergeben Sie dann dieses Objekt der **add(parameters)**-Funktion des **ListCollection**-Objekts. Im folgenden Beispiel wird eine neue Liste mit dem Titel "My Announcements List" erstellt.




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

Wenn Sie eine Liste nach ihrer Erstellung aktualisieren müssen, können Sie die entsprechenden Listeneigenschaften festlegen und die **update()**-Funktion vor dem Aufruf von **executeQueryAsync(succeededCallback, failedCallback)** aufrufen. Die folgenden Änderungen am vorhergehenden Beispiel zeigen dieses Vorgehen.






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


### Hinzufügen von Feldern zu einer Liste

Verwenden Sie die **add(field)**-Funktion oder die **addFieldAsXml(schemaXml, addToDefaultView, options)**-Funktion des **FieldCollection**-Objekts, um der Feldauflistung einer Liste ein Feld hinzuzufügen. Im folgenden Beispiel wird ein Feld erstellt, das dann vor dem Aufruf von **executeQueryAsync(succeededCallback, failedCallback)** aktualisiert wird.




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


### Löschen einer Liste

Zum Löschen einer Liste rufen Sie die **deleteObject()**-Funktion des Listenobjekts auf, wie im folgenden Beispiel dargestellt.




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


## Erstellen, Aktualisieren und Löschen von Ordnern
<a name="BasicOps_FolderTasks"> </a>

Sie können Ordner für die Organisation Ihrer Inhalte anpassen, indem Sie das JavaScript-Objektmodell verwenden. In den folgenden Abschnitten ist beschrieben, wie Sie grundlegende Schritte für Ordner ausführen.




### Erstellen eines Ordners in einer Dokumentbibliothek

Zum Erstellen eines Ordners verwenden Sie ein **ListItemCreationInformation**-Objekt, legen den zugrunde liegenden Objekttyp auf **SP.FileSystemObjectType.folder** fest und übergeben diesen als Parameter an die **addItem(parameters)**-Funktion des **List**-Objekts. Legen Sie Eigenschaften für das von dieser Methode zurückgegebene Listenelementobjekt fest, und rufen Sie dann die **update()**-Funktion auf. Dies ist im folgenden Beispiel dargestellt.




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


### Aktualisieren eines Ordners in einer Dokumentbibliothek

Zum Aktualisieren des Ordnernamens können Sie in die **FileLeafRef**-Eigenschaft schreiben und die **update()**-Funktion aufrufen, damit Änderungen wirksam werden, wenn Sie die **executeQueryAsync**-Methode aufrufen.




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


### Löschen eines Ordners in einer Dokumentbibliothek

Zum Löschen eines Ordners rufen Sie die **deleteObject()**-Funktion für das Objekt auf. Im folgenden Beispiel wird die **getFolderByServerRelativeUrl**-Methode verwendet, um den Ordner aus der Dokumentbibliothek abzurufen, und anschließend wird das Element gelöscht.




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


## Erstellen, Lesen, Aktualisieren und Löschen von Ordnern
<a name="BasicOps_FileTasks"> </a>

Sie können Dateien mithilfe des JavaScript-Objektmodells anpassen. In den folgenden Abschnitten ist beschrieben, wie Sie grundlegende Schritte für Dateien ausführen.




> **HINWEIS**
> Sie können nur Dateien mit einer Größe von bis zu 1,5 MB verwenden, wenn Sie das JavaScript-Objektmodell nutzen. Um größere Dateien hochzuladen, müssen Sie REST (Representational State Transfer) nutzen. Weitere Informationen finden Sie unter  [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles). 





### Erstellen einer Datei in einer Dokumentbibliothek

Zum Erstellen von Dateien verwenden Sie ein **FileCreationInformation**-Objekt, legen das URL-Attribut fest und fügen Inhalte als base64-codiertes Bytearray an. Dies ist im folgenden Beispiel dargestellt.




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


### Lesen einer Datei in einer Dokumentbibliothek

Zum Lesen des Inhalts einer Datei führen Sie einen **GET**-Vorgang für die URL der Datei durch. Dies ist im folgenden Beispiel dargestellt.




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


### Aktualisieren einer Datei in einer Dokumentbibliothek

Zum Aktualisieren des Inhalts der Datei können Sie ein **FileCreationInformation**-Objekt verwenden und das overwrite-Attribut auf "true" festlegen, indem Sie die **set_overwrite()**-Methode verwenden. Dies ist in diesem Beispiel dargestellt.




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


### Löschen einer Datei in einer Dokumentbibliothek

Zum Löschen einer Datei rufen Sie die **deleteObject()**-Funktion für das Objekt auf. Im folgenden Beispiel wird die **getFileByServerRelativeUrl**-Methode verwendet, um die Datei aus der Dokumentbibliothek abzurufen, und anschließend wird das Element gelöscht.




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


## SharePoint-Listenelementaufgaben
<a name="BasicOps_SPListItemTasks"> </a>

Um unter Verwendung von JavaScript Elemente einer Liste zurückzugeben, verwenden Sie die **getItemById(id)**-Funktion zum Zurückgeben eines einzelnen Elements oder die **getItems(query)**-Funktion zum Zurückgeben mehrerer Elemente. Mit der **load(clientObject)**-Funktion können Sie dann Listenelementobjekte abrufen, die diese Elemente darstellen.




### Abrufen von Listenelementen

Mit der **getItems(query)**-Funktion können Sie eine Collaborative Application Markup Language (CAML)-Abfrage definieren, die angibt, welche Elemente zurückgegeben werden sollen. Sie können ein undefiniertes **CamlQuery**-Objekt übergeben, um alle Elemente der Liste zurückzugeben, oder mit der **set_viewXml**-Funktion eine CAML-Abfrage definieren und nur Elemente zurückgeben, die bestimmte Kriterien erfüllen. Im folgenden Beispiel werden neben den Werten der Spalten "Title" und "Body" die IDs der ersten 100 Elemente der Liste "Announcements" angezeigt, wobei mit den Listenelementen begonnen wird, deren ID größer als 10 ist.




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


### Zugreifen auf Eigenschafen von ListItem-Objekten mit der Include-Methode

Vier Eigenschaften von **ListItem**-Objekten sind standardmäßig nicht verfügbar, wenn Listenelemente zurückgegeben werden: **displayName**, **effectiveBasePermissions**, **hasUniqueRoleAssignments** und **roleAssignments**. Im vorhergehenden Beispiel wird eine Ausnahme vom Typ **PropertyOrFieldNotInitializedException** zurückgegeben, wenn Sie auf eine dieser Eigenschaften zuzugreifen versuchen. Um auf diese Eigenschaften zuzugreifen, geben Sie die **Include**-Methode in der Abfragezeichenfolge an, wie im folgenden Beispiel gezeigt.




> **HINWEIS**
> Wenn Sie mit LINQ das Clientobjektmodell abfragen, verwenden Sie  [LINQ to Objects](http://msdn.microsoft.com/de-de/library/bb397919.aspx), nicht den  [LINQ to SharePoint-Anbieter](http://msdn.microsoft.com/de-de/library/ee535491.aspx), der nur in Code zum Abfragen des Serverobjektmodells verwendet werden kann. 





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

Weil in diesem Beispiel die **Include**-Methode verwendet wird, sind nach der Ausführung der Abfrage nur die angegebenen Eigenschaften verfügbar. Daher wird eine **PropertyOrFieldNotInitializedException**-Ausnahme ausgelöst, wenn Sie versuchen, auf andere als die genannten Eigenschaften zuzugreifen. Zudem erhalten Sie diesen Fehler, wenn Sie mit Funktionen wie **get_contentType** oder **get_parentList** auf Eigenschaften von übergeordneten Objekten zuzugreifen versuchen.




### Beschränkungen beim Abrufen von Elementen

Die **loadQuery(clientObjectCollection, exp)**-Methode des JavaScript-Objektmodells in SharePoint Foundation 2010 unterstützt die LINQ-Methoden und -Operatoren nicht, die im verwalteten Objektmodell verwendet werden.




## Erstellen, Aktualisieren und Löschen von Listenelementen
<a name="BasicOps_SPListItemCRUD"> </a>

Das Erstellen, Aktualisieren oder Löschen von Listenelementen über das Clientobjektmodell funktioniert ähnlich wie das Ausführen dieser Aufgaben über das Serverobjektmodell. Sie erstellen ein Listenelementobjekt, legen seine Eigenschaften fest und aktualisieren dann das Objekt. Verwenden Sie zum Ändern oder Löschen eines Listenelementobjekts die **getById(id)**-Methode der **ListItemCollection**-Klasse, um das Objekt zurückzugeben, und legen Sie dann entweder Eigenschaften fest und rufen Sie die Aktualisierung des Objekts auf, das von dieser Methode zurückgegeben wird, oder rufen Sie die objekteigene Methode zum Löschen auf. Anders als beim Serverobjektmodell muss jeder dieser Vorgänge im Clientobjektmodell mit einem Aufruf von **to executeQueryAsync(succeededCallback, failedCallback)** beendet werden, damit die Änderungen auf dem Server übernommen werden.




### Erstellen eines Listenelements

Zum Erstellen von Listenelementen erstellen Sie ein **ListItemCreationInformation**-Objekt, legen dessen Eigenschaften fest und übergeben das Objekt als Parameter der **addItem(parameters)**-Funktion des **List**-Objekts. Legen Sie Eigenschaften für das von dieser Methode zurückgegebene Listenelementobjekt fest, und rufen Sie dann **update()** auf. Dies ist im folgenden Beispiel dargestellt.




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


### Aktualisieren eines Listenelements

Zum Festlegen der meisten Listenelementeigenschaften können Sie mithilfe eines Spaltenindexers eine Zuweisung definieren und die **update()**-Funktion aufrufen, sodass Änderungen übernommen werden, wenn Sie **executeQueryAsync(succeededCallback, failedCallback)** aufrufen. Im folgenden Beispiel wird der Titel des dritten Elements der Liste "Announcements" festgelegt.




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


### Löschen eines Listenelements

Rufen Sie zum Löschen eines Listenelements die **deleteObject()**-Methode für das Objekt auf. Im folgenden Beispiel wird die **getItemById(id)**-Methode verwendet, um das zweite Element aus der Liste zurückzugeben. Anschließend wird das Element gelöscht. SharePoint behält die ganzzahligen IDs der Elemente innerhalb von Auflistungen selbst dann bei, wenn die Elemente gelöscht wurden. Daher kann das zweite Element einer Liste unter Umständen eine andere ID als 2 besitzen. Es wird eine Ausnahme vom Typ **ServerException** zurückgegeben, wenn die **deleteObject()**-Methode für ein nicht vorhandenes Element aufgerufen wird.




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

Wenn Sie z. B. die neue Elementanzahl abrufen möchten, die das Ergebnis eines Löschvorgangs ist, schließen Sie einen Aufruf der update()-Methode ein, um die Liste zu aktualisieren. Zudem müssen Sie entweder das Listenobjekt oder die **itemCount**-Eigenschaft für das Listenobjekt aufrufen, bevor Sie die Abfrage ausführen. Wenn Sie die Anzahl der Listenelemente am Anfang und am Ende abrufen möchten, müssen Sie zwei Abfragen ausführen und die Elementanzahl zweimal zurückgeben, wie es in der folgenden Änderung des vorherigen Beispiels gezeigt wird.






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


## Zugreifen auf Objekte im Hostweb
<a name="BasicOps_AccessHostweb"> </a>

Beim Entwickeln des Add-Ins müssen Sie möglicherweise auf das Hostweb zugreifen, um mit den darin enthaltenen Elementen zu interagieren. Verwenden Sie das **AppContextSite**-Objekt, um auf das Hostweb oder andere SharePoint-Websites zu verweisen. Dies ist im folgenden Beispiel dargestellt. Ein vollständiges Codebeispiel finden Sie unter  [Abrufen des Hostwebtitels mithilfe der domänenübergreifenden Bibliothek (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d).




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

Im vorherigen Beispiel wird die domänenübergreifende Bibliothek in SharePoint 2013 zum Zugreifen auf das Hostweb verwendet. Weitere Informationen finden Sie unter  [Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).




## Weitere Ressourcen
<a name="BasicOps_AddRes"> </a>


-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)


-  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Arbeiten mit externen Daten in SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)



