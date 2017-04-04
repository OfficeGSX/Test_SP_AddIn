---
title: Procédure  effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013
Découvrez comment écrire du code pour effectuer des opérations de base au moyen du modèle objet client JavaScript dans SharePoint 2013.
> **REMARQUE**
> Pour un exemple d'Complément SharePoint de niveau « Hello World » qui utilise la bibliothèque JavaScript, voir  [Utilisation des API JavaScript SharePoint pour exploiter les données SharePoint](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md). 
  
    
    


## API clientes SharePoint 2013
<a name="ClientAPIs"> </a>

Vous pouvez utiliser le modèle objet client SharePoint pour extraire, mettre à jour et gérer des données dans SharePoint 2013. SharePoint rend le modèle objet disponible sous plusieurs formes.
  
    
    

- Assemblys redistribuables .NET Framework
    
  
- Bibliothèque JavaScript
    
  
- Points de terminaison REST/OData
    
  
- Assemblys Windows Phone
    
  
- Assemblys redistribuables Silverlight
    
  
Pour plus d'informations sur les jeux d'API disponibles pour SharePoint 2013, voir  [Choisir l'ensemble d'API approprié dans SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
Cet article explique comment effectuer des opérations de base au moyen du modèle objet JavaScript. Vous pouvez ajouter une référence au modèle objet au moyen de balises HTML <script>. Pour plus d'informations sur l'utilisation des autres API clientes, voir les rubriques suivantes :
  
    
    

-  [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Créer des applications Windows Phone accédant à SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)
    
  
-  [Utilisation du modèle objet Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)
    
  

## Effectuer des tâches de base dans SharePoint 2013 au moyen du modèle objet client JavaScript
<a name="BasicOps_SPJSOMOps"> </a>

Les sections suivantes décrivent les tâches que vous pouvez effectuer par programmation et elles incluent des exemples de code JavaScript pour illustrer les opérations.
  
    
    
Lorsque vous créez un complément hébergé dans le nuage, vous pouvez ajouter une référence au modèle objet au moyen de balises HTML <script>. Nous vous recommandons de faire référence au site web hôte, car le site web de complément n'existe peut-être pas dans chaque scénario au sein des compléments hébergés dans le nuage. Vous pouvez extraire l'URL du site web hôte à partir du paramètre de chaîne de requête  _SPHostUrl_ si vous utilisez le jeton **{StandardTokens}**. Vous pouvez également utiliser le paramètre de chaîne de requête que vous avez personnellement défini si vous utilisez le jeton **{HostUrl}**. Une fois que vous disposez de l'URL du site web hôte, vous devez utiliser du code JavaScript pour créer de manière dynamique la référence au modèle objet.
  
    
    
L'exemple de code suivant effectue les tâches suivantes pour ajouter une référence au modèle objet JavaScript :
  
    
    

- Fait référence à la bibliothèque AJAX à partir du réseau de distribution de contenu Microsoft.
    
  
- Fait référence à la bibliothèque jQuery à partir du réseau de distribution de contenu Microsoft.
    
  
- Extrait l'URL du site web hôte à partir de la chaîne de requête.
    
  
- Charge les fichiers SP.Runtime.js et SP.js à l'aide de la fonction **getScript** dans jQuery. Après le chargement des fichiers, votre programme a accès au modèle objet JavaScript pour SharePoint.
    
  
- Poursuit le flux dans la fonction **execOperation**.
    
  


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

Lorsque vous créez un complément hébergé par SharePoint, vous pouvez ajouter une référence au modèle objet au moyen de balises HTML <script>. Le site web de complément dans un complément hébergé par SharePoint vous permet d'utiliser des chemins relatifs pour faire référence aux fichiers requis pour l'emploi du modèle objet JavaScript.
  
    
    
L'exemple de code suivant effectue les tâches suivantes pour ajouter une référence au modèle objet JavaScript :
  
    
    

- Fait référence à la bibliothèque AJAX à partir du réseau de distribution de contenu Microsoft.
    
  
- Fait référence au fichier SP.Runtime.js en utilisant une URL relative au site web de complément.
    
  
- Fait référence au fichier SP.js en utilisant une URL relative au site web de complément.
    
  


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


## Tâches du site web SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Pour utiliser des sites web au moyen de JavaScript, commencez par utiliser le constructeur **ClientContext(serverRelativeUrl)** et passez une URL ou URI pour renvoyer un contexte de requête spécifique.
  
    
    

### Récupérer les propriétés d'un site web

Utilisez la propriété web de la classe **ClientContext** pour spécifier les propriétés de l'objet de site web se trouvant au niveau de l'URL de contexte spécifiée. Après avoir chargé l'objet site web par le biais de la méthode **load(clientObject)**, puis appelé **executeQueryAsync(succeededCallback, failedCallback)**, vous obtenez un accès à toutes les propriétés de ce site web. L'exemple suivant affiche le titre et la description du site web spécifié, même si toutes les autres propriétés qui sont retournées deviennent disponibles après que vous avez chargé l'objet site web et exécuté la requête.
  
    
    
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


### Récupérer uniquement les propriétés sélectionnées d'un site web

Pour réduire les transferts inutiles de données entre le client et le serveur, vous souhaiterez peut-être retourner seulement les propriétés spécifiées de l'objet site web, et non pas toutes ses propriétés. Dans ce cas, utilisez une syntaxe d'expression de requête LINQ ou lambda avec la méthode **load(clientObject)** pour spécifier quelles propriétés il faut retourner depuis le serveur. Dans l'exemple suivant, seuls le titre et la date de création de l'objet site web deviennent disponibles après l'appel de **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    
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


> **REMARQUE**
> Si vous essayez d'accéder à d'autres propriétés, le code génère une exception parce que les autres propriétés ne sont pas disponibles. 
  
    
    


### Modifier les propriétés d'un site web

Pour modifier un site web, vous devez définir ses propriétés et appeler la méthode **update()** de la même manière dont le modèle objet serveur fonctionne. Toutefois, dans le modèle objet client, vous devez appeler **executeQueryAsync(succeededCallback, failedCallback)** pour demander le traitement par lots de toutes les commandes que vous spécifiez. L'exemple suivant change le titre et la description d'un site web spécifié.
  
    
    
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


## Tâches de liste SharePoint
<a name="BasicOps_SPListTasks"> </a>

L'utilisation d'objets de liste au moyen de JavaScript est similaire à l'utilisation des objets de site web. Commencez par utiliser le constructeur **ClientContext(serverRelativeUrl)** et passer une URL ou URI pour renvoyer une contexte de requête spécifique. Vous pouvez ensuite utiliser la propriété **lists** de la classe **Web** pour accéder à la collection de listes du site web.
  
    
    

### Récupérer toutes les propriétés de toutes les listes d'un site web

Pour retourner toutes les listes d'un site web, chargez la collection de listes via la méthode **load(clientObject)**, puis appelez **executeQueryAsync(succeededCallback, failedCallback)**. L'exemple suivant affiche l'URL du site web et la date et l'heure de création de la liste.
  
    
    
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


### Récupérer uniquement les propriétés spécifiées des listes

L'exemple précédent retourne toutes les propriétés des listes d'un site web. Pour réduire le transfert inutile de données entre le client et le serveur, vous pouvez utiliser des expressions de requête LINQ pour spécifier quelles propriétés retourner. Dans JavaScript, vous spécifiez **Include** comme élément de la chaîne de requête qui est transmise à la méthode **load(clientObject)** pour spécifier les propriétés à renvoyer. L'exemple suivant utilise cette méthode pour renvoyer uniquement le titre et l'ID de chaque liste dans la collection.
  
    
    
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


### Stocker des listes récupérées dans une collection

Comme le montre l'exemple qui suit, vous pouvez opter pour la méthode **loadQuery(clientObjectCollection, exp)** à la place de la méthode **load(clientObject)** pour stocker la valeur renvoyée dans une autre collection, au lieu de la stocker dans la propriété de listes.
  
    
    
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


### Appliquer des filtres à la récupération de liste

Comme indiqué dans l'exemple suivant, vous pouvez imbriquer des instructions **Include** dans une requête JavaScript pour retourner les métadonnées d'une liste et de ses champs. L'exemple retourne tous les champs de toutes les listes au sein d'un site web, et affiche le titre et le nom interne de tous les champs dont le nom interne contient la chaîne « name ».
  
    
    
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


## Créer, mettre à jour et supprimer des listes
<a name="BasicOps_SPListCRUD"> </a>

La création, la mise à jour et la suppression de listes via le modèle objet client fonctionne de façon similaire à la manière dont vous effectuez ces tâches à l'aide du modèle objet client .NET, bien que les opérations du client ne se terminent pas tant que vous n'avez pas appelé la fonction **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    

### Créer et mettre à jour une liste

Pour créer un objet de liste au moyen de JavaScript, utilisez l'objet **ListCreationInformation** pour définir ses propriétés, puis transmettez cet objet à la fonction **add(parameters)** de l'objet **ListCollection**. L'exemple suivant crée une nouvelle liste announcements.
  
    
    
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

Si vous devez mettre la liste à jour après la création de la liste, vous pouvez définir les propriétés de la liste et appeler la fonction **update()** avant d'appeler **executeQueryAsync(succeededCallback, failedCallback)**, comme le montrent les modifications suivantes apportées à l'exemple précédent.
  
    
    


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


### Ajouter un champ à une liste

Utilisez la fonction **add(field)** ou **addFieldAsXml(schemaXml, addToDefaultView, options)** de l'objet **FieldCollection** pour ajouter un champ à la collection de champs d'une liste. L'exemple suivant crée un champ, puis le met à jour avant d'appeler **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    
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


### Supprimer une liste

Pour supprimer une liste, appelez la fonction **deleteObject()** de l'objet liste, comme le montre l'exemple suivant.
  
    
    
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


## Créer, mettre à jour et supprimer des dossiers
<a name="BasicOps_FolderTasks"> </a>

Vous pouvez manipuler des dossiers pour organiser votre contenu à l'aide du modèle objet JavaScript. Les sections qui suivent vous montrent comment réaliser des opérations de base avec des dossiers.
  
    
    

### Créer un dossier dans une bibliothèque de documents

Pour créer un dossier, vous devez utiliser un objet **ListItemCreationInformation**, définir le type d'objet sous-jacent sur **SP.FileSystemObjectType.folder** et le transmettre comme paramètre à la fonction **addItem(parameters)** de l'objet **List**. Définissez les propriétés dans l'objet élément de liste renvoyé par cette méthode, puis appelez la fonction **update()** comme le montre l'exemple qui suit.
  
    
    
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


### Mettre à jour un dossier dans une bibliothèque de documents

Pour mettre à jour le nom d'un dossier, vous pouvez écrire dans la propriété **FileLeafRef** et appeler la fonction **update()** afin que les modifications soient validées au moment d'appeler la méthode **executeQueryAsync**.
  
    
    
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


### Supprimer un dossier dans une bibliothèque de documents

Pour supprimer un dossier, appelez la fonction **deleteObject()** dans l'objet. L'exemple qui suit fait appel à la méthode **getFolderByServerRelativeUrl** pour extraire le dossier de la bibliothèque de documents, puis supprime l'élément.
  
    
    
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


## Créer, lire, mettre à jour et supprimer des fichiers
<a name="BasicOps_FileTasks"> </a>

Vous pouvez manipuler des fichiers à l'aide du modèle objet JavaScript. Les sections suivantes expliquent comment effectuer des opérations de base avec des fichiers.
  
    
    

> **REMARQUE**
> Vous pouvez uniquement travailler avec des fichiers d'une taille maximale de 1,5 Mo à l'aide du modèle objet JavaScript. Pour charger des fichiers plus volumineux, utilisez l'interface REST (Representational State Transfer). Pour plus d'informations, voir  [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles). 
  
    
    


### Créer un fichier dans une bibliothèque de documents

Pour créer des fichiers, vous devez utiliser un objet **FileCreationInformation**, définir l'attribut URL et ajouter le contenu sous la forme d'un tableau d'octets codés en base64 comme dans l'exemple qui suit.
  
    
    
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


### Lire un fichier dans une bibliothèque de documents

Pour lire le contenu d'un fichier, vous devez exécuter une opération **GET** dans l'URL du fichier comme le montre l'exemple ci-dessous.
  
    
    
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


### Mettre à jour un fichier dans une bibliothèque de documents

Pour mettre à jour le contenu d'un fichier, vous pouvez utiliser un objet **FileCreationInformation** et définir l'attribut de remplacement sur True à l'aide de la méthode **set_overwrite()** comme dans l'exemple suivant.
  
    
    
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


### Supprimer un fichier dans une bibliothèque de documents

Pour supprimer un fichier, appelez la fonction **deleteObject()** dans l'objet. L'exemple suivant utilise la méthode **getFileByServerRelativeUrl** pour extraire le fichier de la bibliothèque de documents, puis supprime l'élément.
  
    
    
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


## Tâches d'éléments de liste SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Pour renvoyer les éléments d'une liste au moyen de JavaScript, utilisez la fonction **getItemById(id)** pour retourner un seul élément, ou bien la fonction **getItems(query)** pour retourner plusieurs éléments. Vous utilisez ensuite la fonction **load(clientObject)** pour accéder aux objets d'élément de liste représentant les éléments.
  
    
    

### Récupérer les éléments d'une liste

La fonction **getItems(query)** vous permet de définir une requête CAML (Collaborative Application Markup Language) qui spécifie les éléments à retourner. Vous transmettez un objet **CamlQuery** non défini pour retourner tous les éléments de la liste ou vous utilisez la fonction **set_viewXml** pour définir une requête CAML et renvoyer les éléments répondant à des critères spécifiques. L'exemple suivant affiche l'ID ainsi que les valeurs des colonnes Title et Body pour les 100 premiers éléments de la liste Announcements, en commençant par les éléments de liste dont l'ID dans la collection est supérieur à 10.
  
    
    
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


### Utiliser la méthode Include pour accéder aux propriétés des objets ListItem

Quatre propriétés des objets **ListItem** ne sont pas disponibles par défaut lorsque vous renvoyez des éléments de liste **displayName**, **effectiveBasePermissions**, **hasUniqueRoleAssignments** et **roleAssignments**. L'exemple précédent renvoie une erreur **PropertyOrFieldNotInitializedException** si vous essayez d'accéder à l'une de ces propriétés. Pour accéder à ces propriétés, utilisez la méthode **Include** comme élément de la chaîne de requête, comme illustré dans l'exemple suivant.
  
    
    

> **REMARQUE**
> Quand vous utilisez LINQ pour créer des requêtes sur la base du modèle objet client, vous utilisez  [LINQ to Objects](http://msdn.microsoft.com/fr-fr/library/bb397919), et non  [le fournisseur LINQ to SharePoint](http://msdn.microsoft.com/fr-fr/library/ee535491), qui peut seulement être utilisé quand vous écrivez du code sur la base du modèle objet serveur. 
  
    
    

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

Étant donné que cet exemple utilise une méthode **Include**, seules les propriétés spécifiées sont disponibles après l'exécution de la requête. Par conséquent, vous recevez une exception **PropertyOrFieldNotInitializedException** si vous essayez d'accéder à d'autres propriétés que celles qui ont été spécifiées. En outre, vous recevez cette erreur si vous essayez d'utiliser des fonctions telles que **get_contentType** ou **get_parentList** pour accéder aux propriétés des objets contenants.
  
    
    

### Restrictions sur la récupération d'éléments

La méthode **loadQuery(clientObjectCollection, exp)** du modèle objet JavaScript dans SharePoint Foundation 2010 ne prend pas en charge les méthodes et opérateurs LINQ qui sont utilisés par le modèle d'objet managé.
  
    
    

## Créer, mettre à jour et supprimer des éléments de liste
<a name="BasicOps_SPListItemCRUD"> </a>

La création, la mise à jour et la suppression d'éléments de liste via le modèle objet client fonctionnent comme l'exécution de ces tâches à l'aide du modèle objet serveur. Vous créez un objet élément de liste, définissez ses propriétés, puis mettez à jour l'objet. Pour modifier ou supprimer un objet élément de liste, utilisez la fonction **getById(id)** de l'objet **ListItemCollection** pour retourner l'objet, puis définissez les propriétés et appelez la mise à jour sur l'objet que cette méthode retourne, ou appelez la propre méthode de l'objet pour effectuer la suppression. Contrairement au modèle objet serveur, chacune de ces opérations dans le modèle objet client doit se conclure avec un appel à **to executeQueryAsync(succeededCallback, failedCallback)** pour que les modifications prennent effet sur le serveur.
  
    
    

### Créer un élément de liste

Pour créer des éléments de liste, vous devez créer un objet **ListItemCreationInformation**, définir ses propriétés et le transmettre en tant que paramètre à la fonction **addItem(parameters)** de l'objet **List**. Définissez les propriétés sur l'objet élément de liste que cette méthode renvoie, puis appelez la fonction **update()** comme le montre l'exemple qui suit.
  
    
    
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


### Mettre à jour un élément de liste

Pour définir les propriétés de la plupart des éléments de liste, vous pouvez utiliser un indexeur de colonnes pour effectuer une affectation, et appeler la fonction **update()** de façon que les modifications prennent effet quand vous appelez **executeQueryAsync(succeededCallback, failedCallback)**. L'exemple suivant définit le titre du troisième élément dans la liste Announcements.
  
    
    
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


### Supprimer un élément de liste

Pour supprimer un élément de liste, appelez la fonction **deleteObject()** sur l'objet. L'exemple suivant utilise la fonction **getItemById(id)** pour retourner le deuxième élément de liste, puis supprime l'élément. SharePoint conserve les ID d'entier dans les collections, même s'ils ont été supprimés. Ainsi, par exemple, le deuxième élément d'une liste ne peut pas avoir 2 comme identificateur. Une erreur **ServerException** est renvoyée si la fonction **deleteObject()** est appelée pour un élément qui n'existe pas.
  
    
    
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

Si vous voulez, par exemple, récupérer le nouveau nombre d'éléments résultant d'une opération de suppression, incluez un appel à la méthode update() pour actualiser la liste. De plus, vous devez charger soit l'objet de liste lui-même soit la propriété **itemCount** sur l'objet de liste avant d'exécuter la requête. Si vous souhaitez récupérer à la fois un nombre de début et un nombre de fin des éléments de liste, vous devez exécuter deux requêtes et retourner deux fois le nombre d'éléments, comme indiqué dans la modification suivante de l'exemple précédent.
  
    
    


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


## Objets Access sur le site web hôte
<a name="BasicOps_AccessHostweb"> </a>

Lors du développement de votre complément, vous pouvez avoir besoin d'accéder au site web hôte pour communiquer avec les éléments qu'il contient. Utilisez l'objet **AppContextSite** pour faire référence au site web hôte ou à d'autres sites SharePoint comme le montre l'exemple suivant. Pour bénéficier d'un exemple de code complet, voir [Extraire le titre du site web hôte à l'aide de la bibliothèque inter-domaines (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d).
  
    
    
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

L'exemple qui précède se sert de la bibliothèque inter-domaines dans SharePoint 2013 pour accéder au site web hôte. Pour plus d'informations, voir  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Ressources supplémentaires
<a name="BasicOps_AddRes"> </a>


-  [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Utiliser des données externes dans SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  

