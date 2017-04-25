---
title: Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013
Aprenda a escribir código para ejecutar operaciones básicas con el modelo de objetos de cliente JavaScript en SharePoint 2013.
> **NOTA**
> Para ver un Complemento de SharePoint de muestra del nivel "Hola a todos" que usa la biblioteca JavaScript, consulte  [Usar las API de JavaScript de SharePoint para trabajar con datos de SharePoint](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md). 





## API de cliente de SharePoint 2013
<a name="ClientAPIs"> </a>

Puede usar el modelo de objetos de cliente de SharePoint para recuperar, actualizar y administrar datos en SharePoint 2013. SharePoint hace que el modelo de objetos esté disponible en varias formas.




- Ensamblados redistribuibles de .NET Framework


- Biblioteca de JavaScript


- Extremos REST/OData


- Ensamblados de Windows Phone


- Ensamblados redistribuibles de Silverlight


Para más información sobre los conjuntos de API disponibles para SharePoint 2013, vea  [Elegir el conjunto de API correcto en SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).



Este artículo muestra cómo realizar operaciones básicas con el modelo de objetos JavaScript. Puede agregar una referencia al modelo de objetos con las etiquetas <script> de HTML. Para más información sobre cómo utilizar las otras API de cliente, vea lo siguiente:




-  [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Creación de aplicaciones de Windows Phone con acceso a SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)


-  [Uso del modelo de objetos Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)



## Ejecutar tareas básicas en SharePoint 2013 con el modelo de objetos de cliente JavaScript
<a name="BasicOps_SPJSOMOps"> </a>

Las secciones siguientes describen las tareas que puede completar mediante programación e incluyen ejemplos de código JavaScript que demuestran las operaciones.



Al crear un complemento hospedado en la nube, puede agregar una referencia al modelo de objetos con etiquetas <script> de HTML. Recomendamos hacer referencia a la web de host porque puede que la web de complemento no exista en todos los escenarios de los complementos hospedados en la nube. Puede recuperar la dirección URL de la web de host desde el parámetro de la cadena de consulta  _SPHostUrl_ si usa el token **{StandardTokens}**. También puede usar su propio parámetro de la cadena de consulta si usa el token **{HostUrl}**. Cuando tenga la dirección URL de la web de host, use código JavaScript para crear dinámicamente la referencia al modelo de objetos.



En el ejemplo de código siguiente se ejecutan estas tareas para agregar una referencia al modelo de objetos JavaScript:




- Hace referencia a la biblioteca AJAX desde la Red de entrega de contenido (CDN) de Microsoft.


- Hace referencia a la biblioteca jQuery desde la CDN de Microsoft.


- Extrae la dirección URL de la web de host desde la cadena de la consulta.


- Sube los archivos SP.Runtime.js y SP.js con la función **getScript** en jQuery. Después de subir los archivos, el programa tiene acceso al modelo de objetos JavaScript para SharePoint.


- Sigue el flujo en la función **execOperation**.




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

Al crear un complemento hospedado en SharePoint, puede agregar una referencia al modelo de objetos con etiquetas <script> de HTML. La web de complemento de un complemento hospedado en SharePoint le permite usar rutas de acceso relativas para hacer referencia a los archivos necesarios para usar el modelo de objetos JavaScript:



El marcado siguiente ejecuta estas tareas para agregar una referencia al modelo de objetos JavaScript:




- Hace referencia a la biblioteca AJAX desde la CDN de Microsoft.


- Hace referencia al archivo SP.Runtime.js con una dirección URL relativa a la web de complemento.


- Hace referencia al archivo SP.js con una dirección URL relativa a la web de complemento.




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


## Tareas de sitio web de SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Para trabajar con sitios web con JavaScript, empiece con el constructor **ClientContext(serverRelativeUrl)** y pase una dirección URL o URI para devolver un contexto de solicitud específico.




### Recuperar las propiedades de un sitio web

Use la propiedad web de la clase **ClientContext** para especificar las propiedades del objeto de sitio web que se encuentra en la dirección URL del contexto especificado. Después de cargar el objeto de sitio web con el método **load(clientObject)** y, posteriormente, llamar a **executeQueryAsync(succeededCallback, failedCallback)**, obtendrá acceso a todas las propiedades de ese sitio web. En el siguiente ejemplo se muestra el título y la descripción del sitio web especificado, aunque el resto de las propiedades devueltas de forma predeterminada se ponen a disposición después de cargar el objeto de sitio web y ejecutar la consulta.



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


### Recuperar solo las propiedades seleccionadas de un sitio web

Para reducir la transferencia de datos innecesaria entre cliente y servidor, es posible que desee devolver solo las propiedades especificadas del objeto de sitio web, en lugar de todas sus propiedades. En este caso, use la sintaxis de la expresión lambda o consulta LINQ con el método **load(clientObject)** para especificar las propiedades que se van a devolver desde el servidor. En el siguiente ejemplo, solo se ponen a disposición el título y la fecha de creación del objeto de sitio web después de llamar a **executeQueryAsync(succeededCallback, failedCallback)**.



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


> **NOTA**
> Si trata de obtener acceso a otras propiedades, el código produce una excepción porque las otras propiedades no están disponibles. 





### Escribir en las propiedades de un sitio web

Para modificar un sitio web, se deben establecer las propiedades y llamar al método **update()**, de manera similar al modo en que funciona el modelo de objetos de servidor. Sin embargo, en el modelo de objetos de cliente, debe llamar a **executeQueryAsync(succeededCallback, failedCallback)** para solicitar el procesamiento por lotes para todos los comandos que se especifiquen. En el siguiente ejemplo se cambia el título y la descripción de un sitio web especificado.



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


## Tareas de lista de SharePoint
<a name="BasicOps_SPListTasks"> </a>

Trabajar con objetos de lista con JavaScript es parecido a trabajar con objetos de sitio web. Empiece por usar el constructor **ClientContext(serverRelativeUrl)** y pasar una dirección URL o URI para devolver un contexto de consulta específico. Después puede usar la propiedad **lists** de la clase **Web** para obtener la colección de listas del sitio web.




### Recuperar todas las propiedades de todas las listas de un sitio web

Para devolver todas las listas de un sitio web, cargue la colección de listas con el método **load(clientObject)** y luego llame a **executeQueryAsync(succeededCallback, failedCallback)**. En el siguiente ejemplo se muestra la dirección URL del sitio web, y la fecha y hora, en que se creó la lista.



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


### Recuperar solo propiedades específicas de listas

En el ejemplo anterior se devuelven todas las propiedades de las listas de un sitio web. Para reducir la transferencia de datos innecesarios entre cliente y servidor, puede usar expresiones de consultas LINQ para especificar qué propiedades devolver. En JavaScript, se especifica **Include** como parte de la cadena de la consulta que se pasa al método **load(clientObject)** para especificar qué propiedades devolver. En el siguiente ejemplo se usa este enfoque para devolver solo el título y el identificador de cada una de las listas de la colección.



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


### Almacenar listas devueltas en una colección

Como se ve en el ejemplo siguiente, puede usar el método **loadQuery(clientObjectCollection, exp)** en lugar del método **load(clientObject)** para almacenar el valor devuelto en otra colección en lugar de almacenarlo en la propiedad de las listas.



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


### Aplicar filtros a la recuperación de listas

Como se muestra en el ejemplo siguiente, puede anidar instrucciones **Include** en una consulta JavaScript, para devolver metadatos de listas y campos. En el ejemplo se devuelven todos los campos de todas las listas dentro de un sitio web, y se muestra el título y el nombre interno de todos los campos cuyo nombre interno contiene la cadena "name".



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


## Crear, actualizar y eliminar listas
<a name="BasicOps_SPListCRUD"> </a>

La creación, la actualización y la eliminación de listas por medio del modelo de objetos de cliente funcionan de manera similar al modo en que se llevan a cabo estas tareas mediante el modelo de objetos de cliente .NET. No obstante, las operaciones de cliente no se completarán hasta que se llame a la función **executeQueryAsync(succeededCallback, failedCallback)**.




### Crear y actualizar una lista

Parar crear un objeto List con JavaScript, use el objeto **ListCreationInformation** para definir sus propiedades y luego pase este objeto a la función **add(parameters)** del objeto **ListCollection**. En el siguiente ejemplo se crea una lista de anuncios.



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

Si necesita actualizar la lista una vez creada, puede establecer las propiedades de la lista y llamar a la función **update()** antes de llamar a **executeQueryAsync(succeededCallback, failedCallback)**, como se muestra en las siguientes modificaciones del ejemplo anterior.





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


### Agregar un campo a una lista

Use **add(field)** o la función **addFieldAsXml(schemaXml, addToDefaultView, options)** del objeto **FieldCollection** para agregar un campo a la colección de campos de una lista. En el siguiente ejemplo se crea un campo y, posteriormente, se actualiza antes de llamar a **executeQueryAsync(succeededCallback, failedCallback)**.



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


### Eliminar una lista

Para eliminar una lista, llame a la función **deleteObject()** del objeto List, como se muestra en el siguiente ejemplo.



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


## Crear, actualizar y eliminar carpetas
<a name="BasicOps_FolderTasks"> </a>

Puede manipular carpetas para organizar el contenido usando el modelo de objetos JavaScript. En las secciones siguientes se enseña a realizar operaciones básicas con las carpetas.




### Crear una carpeta en una biblioteca de documentos

Para crear una carpeta, debe usar un objeto **ListItemCreationInformation**, establecer el tipo de objeto subyacente en **SP.FileSystemObjectType.folder** y pasarlo como parámetro a la función **addItem(parameters)** del objeto **List**. Establezca las propiedades en el objeto de elemento de lista que este método devuelve y luego llame a la función **update()**, como se ve en el ejemplo siguiente.



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


### Actualizar una carpeta en una biblioteca de documentos

Para actualizar el nombre de una carpeta, puede escribir en la propiedad **FileLeafRef** y llamar a la función **update()** para que los cambios surtan efecto cuando llame al método **executeQueryAsync**.



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


### Eliminar una carpeta de una biblioteca de documentos

Para eliminar una carpeta, llame a la función **deleteObject()** en el objeto. El ejemplo siguiente usa el método **getFolderByServerRelativeUrl** para recuperar la carpeta de la biblioteca de documentos y luego elimina el elemento.



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


## Crear, leer, actualizar y eliminar archivos
<a name="BasicOps_FileTasks"> </a>

Puede manipular archivos usando el modelo de objetos JavaScript. En las secciones siguientes se enseña a realizar operaciones básicas con los archivos.




> **NOTA**
> Solo puede trabajar con archivos de hasta 1,5 MB usando el modelo de objetos JavaScript. Para cargar archivos más grandes, use REST (transferencia de estado representacional). Para más información, vea  [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles). 





### Crear un archivo en una biblioteca de documentos

Para crear un archivo, debe usar un objeto **FileCreationInformation**, definir el atributo de la dirección URL y anexar contenido como una matriz codificada base64 de bytes, como se ve en este ejemplo.



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


### Leer un archivo de una biblioteca de documentos

Para leer el contenido de un archivo, debe realizar una operación **GET** en la dirección URL del archivo, como se ve en el ejemplo siguiente.



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


### Actualizar un archivo de una biblioteca de documentos

Para actualizar el contenido de un archivo, puede usar un objeto **FileCreationInformation** y definir el atributo de sobrescritura en true usando el método **set_overwrite()**, como se ve en este ejemplo.



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


### Eliminar un archivo de una biblioteca de documentos

Para eliminar un archivo, llame a la función **deleteObject()** en el objeto. El ejemplo siguiente usa el método **getFileByServerRelativeUrl** para recuperar el archivo de la biblioteca de documentos y luego elimina el elemento.



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


## Tareas de elementos de lista de SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Para devolver elementos de una lista con JavaScript, use la función **getItemById(id)** para devolver un solo elemento o use la función **getItems(query)** para devolver varios elementos. Luego use la función **load(clientObject)** para alcanzar los objetos de elemento de lista que representan los elementos.




### Recuperar elementos de una lista

La función **getItems(query)** permite definir una consulta Lenguaje de marcado de aplicaciones de colaboración (CAML) que especifique qué elementos se van a devolver. Puede pasar un objeto **CamlQuery** no definido para devolver todos los elementos de la lista, o bien usar la función **set_viewXml** para definir una consulta CAML y devolver los elementos que cumplan con criterios específicos. En el siguiente ejemplo se muestran los valores de columna Identificador, Título y Cuerpo de los primeros 100 elementos de la lista Anuncios, empezando por los elementos de lista cuyo identificador de colección es mayor que 10.



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


### Usar el método Include para obtener acceso a propiedades de objetos ListItem

Hay cuatro propiedades de objetos **ListItem** que no están disponibles de forma predeterminada al devolver elementos de lista: **displayName**, **effectiveBasePermissions**, **hasUniqueRoleAssignments** y **roleAssignments**. El ejemplo anterior devuelve una excepción **PropertyOrFieldNotInitializedException** si trata de obtener acceso a una de estas propiedades. Para obtener acceso a una de estas propiedades, use el método **Include** como parte de la cadena de la consulta, como se muestra en el siguiente ejemplo.




> **NOTA**
> Al usar LINQ para crear consultas respecto del modelo de objetos de cliente, se utiliza  [LINQ to Objects](http://msdn.microsoft.com/es-es/library/bb397919.aspx), no el  [proveedor LINQ to SharePoint](http://msdn.microsoft.com/es-es/library/ee535491.aspx), que solo se puede usar cuando se escribe código respecto al modelo de objetos de servidor. 




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

Dado que en este ejemplo se usa **Include**, solo las propiedades especificadas estarán disponibles después de la ejecución de la consulta. Por lo tanto, recibirá una excepción **PropertyOrFieldNotInitializedException** si trata de obtener acceso a otras propiedades además de las que se especificaron. Además, recibirá este error si trata de usar propiedades como **get_contentType** o **get_parentList** para obtener acceso a las propiedades de los objetos que contienen.




### Restricciones en la recuperación de elementos

El método **loadQuery(clientObjectCollection, exp)** del modelo de objetos JavaScript de SharePoint Foundation 2010 no admite los métodos y operadores de LINQ que usa el modelo de objetos administrado.




## Crear, actualizar y eliminar elementos de lista
<a name="BasicOps_SPListItemCRUD"> </a>

La creación, actualización o eliminación de elementos de lista mediante el modelo de objetos de cliente funciona de forma similar a la realización de tareas mediante el modelo de objetos de servidor. Se crea un objeto de elemento de lista, se establecen sus propiedades y, a continuación, se actualiza el objeto. Para modificar o eliminar un objeto de elemento de lista, use la función **getById(id)** del objeto **ListItemCollection** para devolver el objeto y, a continuación, establezca las propiedades y llame a la actualización del objeto que devuelve este método o llame al propio método del objeto para eliminarlo. A diferencia del modelo de objetos de servidor, cada una de estas operaciones en el modelo de objetos de cliente debe concluir con una llamada a **to executeQueryAsync(succeededCallback, failedCallback)** para realizar los cambios en el servidor.




### Crear un elemento de lista

Para crear elementos de lista, debe crear un objeto **ListItemCreationInformation**, establecer sus propiedades y pasarlo como parámetro a la función **addItem(parameters)** del objeto **List**. Establezca las propiedades en el objeto de elemento de lista que este método devuelve y luego llame a la función **update()**, como se ve en el ejemplo siguiente.



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


### Actualizar un elemento de lista

Para establecer la mayoría de las propiedades del elemento de lista, se puede usar un indizador de columna para realizar una asignación y llamar a la función **update()** para que los cambios se realicen al llamar a **executeQueryAsync(succeededCallback, failedCallback)**. En el ejemplo siguiente se establece el título del tercer elemento de la lista de anuncios.



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


### Eliminar un elemento de lista

Para eliminar un elemento de lista, llame a la función **deleteObject()** del objeto. En el siguiente ejemplo se usa la función **getItemById(id)** para devolver el segundo elemento de la lista y luego se elimina el elemento. SharePoint mantiene los identificadores de enteros de los elementos dentro de las colecciones aunque se hayan eliminado. Por ejemplo, el segundo elemento de una lista podría no tener el 2 como identificador. Si se llama a la función **deleteObject()** para un elemento que no existe, se devuelve la excepción **ServerException**.



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

Por ejemplo, si desea recuperar el nuevo recuento de elementos que resulta de una operación de eliminación, incluya una llamada al método update() para actualizar la lista. Además, debe cargar el objeto de lista en sí o la propiedad **itemCount** en el objeto de lista antes de ejecutar la consulta. Si desea recuperar un recuento de inicio y finalización de los elementos de lista, debe ejecutar dos consultas y devolver el recuento de elemento dos veces, tal como se muestra en la modificación siguiente del ejemplo anterior.





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


## Acceder a objetos en la web de host
<a name="BasicOps_AccessHostweb"> </a>

Al desarrollar el complemento, puede ser necesario acceder a la web de host para interactuar con sus elementos. Use el objeto **AppContextSite** para hacer referencia a la web de host o a otros sitios de SharePoint, como se ve en el ejemplo siguiente. Puede consultar el ejemplo de código completo en [Obtener el título de la web de host usando la biblioteca entre dominios (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d).



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

En el ejemplo anterior se usó la biblioteca entre dominios de SharePoint 2013 para acceder a la web de host. Para más información, vea  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).




## Recursos adicionales
<a name="BasicOps_AddRes"> </a>


-  [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)


-  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Trabajar con datos externos en SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)



