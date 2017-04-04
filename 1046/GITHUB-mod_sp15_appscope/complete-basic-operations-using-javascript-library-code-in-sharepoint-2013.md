---
title: Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 29089af8-dbc0-49b7-a1a0-9e311f49c826
---


# Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013
Saiba como escrever código para executar operações básicas usando o modelo de objeto de cliente no JavaScriptSharePoint 2013.
> [!OBSERVAçãO]
> Para obter um modelo de nível "Hello World" Suplemento do SharePoint que usa a biblioteca JavaScript, confira  [Use as APIs do SharePoint JavaScript para trabalhar com dados do SharePoint](use-the-sharepoint-javascript-apis-to-work-with-sharepoint-data.md). 
  
    
    


## APIs de cliente do SharePoint 2013
<a name="ClientAPIs"> </a>

Você pode usar o modelo de objeto do cliente do SharePoint para recuperar, atualizar e gerenciar dados no SharePoint 2013. O SharePoint disponibiliza o modelo de objeto de diversas formas.
  
    
    

- Assemblies redistribuíveis do .NET Framework
    
  
- Biblioteca do JavaScript
    
  
- Pontos de extremidade REST/OData
    
  
- Assemblies do Windows Phone
    
  
- Assemblies redistribuíveis do Silverlight
    
  
Para saber mais sobre os conjuntos de APIs disponíveis para o SharePoint 2013, consulte  [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
Este artigo mostra como executar operações básicas usando o modelo de objeto do JavaScript. Você pode adicionar uma referência ao modelo de objeto usando marcas <script> HTML. Para saber mais sobre como usar as outras APIs de cliente, consulte o seguinte:
  
    
    

-  [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Crie aplicativos do Windows Phone que acessam o SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)
    
  
-  [Usando o modelo de objeto do Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)
    
  

## Executar tarefas básicas no SharePoint 2013 usando o modelo de objeto do cliente do JavaScript
<a name="BasicOps_SPJSOMOps"> </a>

As seções a seguir descrevem tarefas que você pode concluir programaticamente e incluem exemplos de código do JavaScript que demonstram as operações.
  
    
    
Quando você cria um suplemento hospedado na nuvem, pode adicionar uma referência ao modelo de objeto usando marcas <script> HTML. Recomendamos que você referencie o host da Web, uma vez que o suplemento da Web pode não existir em todos os cenários de suplementos hospedados na nuvem. Você pode recuperar a URL do host da Web do parâmetro de cadeia de caracteres de consulta  _SPHostUrl_, caso esteja usando o token **{StandardTokens}**. Você pode também usar seu parâmetro de cadeia de caracteres de consulta definido, caso esteja usando o token **{HostUrl}**. Depois de obter a URL do host da Web, você deve usar código do JavaScript para criar dinamicamente a referência ao modelo de objeto.
  
    
    
O exemplo de código a seguir executa essas tarefas para adicionar uma referência ao modelo de objeto do JavaScript:
  
    
    

- Referências à biblioteca AJAX da Rede de Distribuição de Conteúdo (CDN) da Microsoft.
    
  
- Referências à biblioteca jQuery da CDN da Microsoft.
    
  
- Extrai a URL do host da Web da cadeia de caracteres de consulta.
    
  
- Carrega os arquivos SP.Runtime.js e SP.js usando a função **getScript** no jQuery. Depois de carregar os arquivos, seu programa terá acesso ao modelo de objeto do JavaScript para o SharePoint.
    
  
- Continua o fluxo na função **execOperation**.
    
  



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

Quando você cria um suplemento hospedado pelo SharePoint, pode adicionar uma referência ao modelo de objeto usando marcas <script> HTML. O suplemento da Web em um suplemento hospedado pelo SharePoint permite o uso de caminhos relativos para fazer referência aos arquivos necessários para usar o modelo de objeto do JavaScript.
  
    
    
A marcação a seguir executa estas tarefas para adicionar uma referência ao modelo de objeto do JavaScript:
  
    
    

- Referencia a biblioteca AJAX da CDN da Microsoft.
    
  
- Referencia o arquivo SP.Runtime.js usando uma URL relativa ao suplemento da Web.
    
  
- Referencia o arquivo SP.js usando uma URL relativa ao suplemento da Web.
    
  



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


## Tarefas de site do SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Para trabalhar com sites usando o JavaScript, comece usando o construtor **ClientContext(serverRelativeUrl)** e passe uma URL ou URI para retornar um contexto de solicitação específico.
  
    
    

### Recuperar as propriedades de um site

Use a propriedade da Web da classe **ClientContext** para especificar as propriedades do objeto do site localizado em uma URL de contexto especificada. Depois de carregar o objeto de site por meio do método **load(clientObject)** e então chamar **executeQueryAsync(succeededCallback, failedCallback)**, você obterá acesso a todas as propriedades desse site. O exemplo a seguir exibe o título e a descrição do site especificado, embora todas as outras propriedades retornadas por padrão fiquem disponíveis depois que você carregar o objeto de site e executar a consulta.
  
    
    

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


### Recuperar somente as propriedades selecionadas de um site

Para reduzir a transferência de dados desnecessária entre cliente e servidor, talvez você queira retornar somente as propriedades especificadas do objeto de site, e não todas as propriedades. Nesse caso, use sintaxe de consulta LINQ ou de expressão lambda com o método **load(clientObject)** para especificar quais propriedades serão retornadas do servidor. No exemplo a seguir, somente o título e a data de criação do objeto de site ficarão disponíveis após a chamada a **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    

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


> [!OBSERVAçãO]
> Se você tentar acessar outras propriedades, o código lançará uma exceção porque não há outras propriedades disponíveis. 
  
    
    


### Gravar nas propriedades de um site

Para modificar um site, defina suas propriedades e chame o método **update()**, de maneira semelhante ao funcionamento do modelo de objeto do servidor. No entanto, no modelo de objeto do cliente, você deverá chamar **executeQueryAsync(succeededCallback, failedCallback)** para solicitar o processamento em lotes de todos os comandos especificados. O exemplo a seguir altera o título e a descrição de um site especificado.
  
    
    

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


## Tarefas de lista do SharePoint
<a name="BasicOps_SPListTasks"> </a>

Trabalhar com objetos de lista usando o JavaScript é semelhante a trabalhar com objetos de site. Comece usando o construtor **ClientContext(serverRelativeUrl)** e passando uma URL ou URI para retornar um contexto de solicitação específico. Você poderá então usar a propriedade **lists** da classe **Web** para obter a coleção de listas no site.
  
    
    

### Recuperar todas as propriedades de todas as listas em um site

Para retornar todas as listas de um site, carregue a coleção de listas por meio do método **load(clientObject)** e então chame **executeQueryAsync(succeededCallback, failedCallback)**. O exemplo a seguir exibe a URL do site e a data e hora em que a lista foi criada.
  
    
    

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


### Recuperar somente as propriedades de listas especificadas

O exemplo anterior retorna todas as propriedades das listas em um site. Para reduzir a transferência de dados desnecessária entre cliente e servidor, você poderá usar expressões de consulta LINQ para especificar quais propriedades serão retornadas. No JavaScript, especifique **Include** como parte da cadeia de caracteres de consulta passada para o método **load(clientObject)** para especificar quais propriedades serão retornadas. O exemplo a seguir usa essa abordagem para retornar somente o título e a ID de cada lista da coleção.
  
    
    

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


### Armazenar listas recuperadas em uma coleção

Como mostra o exemplo a seguir, você poderá usar o método **loadQuery(clientObjectCollection, exp)** em vez de usar o método **load(clientObject)** para armazenar o valor de retorno em outra coleção em vez de armazená-lo na propriedade de listas.
  
    
    

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


### Aplicar filtros a uma recuperação de lista

Como mostra o exemplo a seguir, você pode aninhar instruções **Include** em uma consulta JavaScript para retornar metadados para uma lista e seus campos. O exemplo retorna todos os campos de todas as listas em um site e exibe o título e o nome interno de todos os campos cujo nome interno contenha a cadeia de caracteres "nome".
  
    
    

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


## Criar, atualizar e excluir listas
<a name="BasicOps_SPListCRUD"> </a>

A criação, a atualização e a exclusão de listas por meio do modelo de objeto do cliente funciona de maneira semelhante a como você executa essas tarefas usando o modelo de objeto do cliente .NET, embora operações do cliente não sejam concluídas até você chamar a função **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    

### Criar e atualizar uma lista

Para criar um objeto de lista usando JavaScript, use o objeto **ListCreationInformation** para definir suas propriedades e então passe o objeto para a função **add(parameters)** do objeto **ListCollection**. O exemplo a seguir cria uma nova lista de anúncios.
  
    
    

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

Se precisar atualizar a lista depois de criá-la, você poderá definir propriedades da lista e chamar a lista **update()** antes de chamar **executeQueryAsync(succeededCallback, failedCallback)**, como mostrado nas modificações a seguir feitas no exemplo anterior.
  
    
    



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


### Adicionar um campo a uma lista

Use a função **add(field)** ou **addFieldAsXml(schemaXml, addToDefaultView, options)** do objeto **FieldCollection** para adicionar um campo à coleção de campos de uma lista. O exemplo a seguir cria um campo e então o atualiza antes de chamar **executeQueryAsync(succeededCallback, failedCallback)**.
  
    
    

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


### Excluir uma lista

Para excluir uma lista, chame a função **deleteObject()** do objeto de lista, como mostrado no exemplo a seguir.
  
    
    

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


## Criar, atualizar e excluir pastas
<a name="BasicOps_FolderTasks"> </a>

Você pode manipular pastas para organizar seu conteúdo usando o modelo de objeto do JavaScript. As seções a seguir mostram como executar operações básicas em pastas.
  
    
    

### Criar uma pasta em uma biblioteca de documentos

Para criar uma pasta, use um objeto **ListItemCreationInformation**, defina o tipo de objeto subjacente como **SP.FileSystemObjectType.folder** e passe-o como parâmetro para a função **addItem(parameters)** do objeto **List**. Defina propriedades no objeto de item de lista que esse método retorna e então chame a função **update()**, como mostrado no exemplo a seguir.
  
    
    

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


### Atualizar uma pasta em uma biblioteca de documentos

Para atualizar o nome da pasta, você poderá gravar na propriedade **FileLeafRef** e chame a função **update()** de forma que as alterações entrem em vigor quando o método **executeQueryAsync** for chamado.
  
    
    

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


### Excluir uma pasta em uma biblioteca de documentos

Para excluir uma pasta, chame a função **deleteObject()** no objeto. O exemplo a seguir usa o método **getFolderByServerRelativeUrl** para recuperar a pasta da biblioteca de documentos e então exclua o item.
  
    
    

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


## Criar, ler, atualizar e excluir arquivos
<a name="BasicOps_FileTasks"> </a>

Você pode manipular arquivos usando o modelo de objeto do JavaScript. As seções a seguir mostram como executar operações básicas em arquivos.
  
    
    

> [!OBSERVAçãO]
> Você só pode trabalhar com arquivos de até 1,5 MB usando o modelo de objeto do JavaScript. Para carregar arquivos maiores, use REST (Representational State Transfer). Para saber mais, consulte  [](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#LargeFiles). 
  
    
    


### Criar um arquivo em uma biblioteca de documentos

Para criar arquivos, use um objeto **FileCreationInformation**, defina o atributo de URL e anexe conteúdo como uma matriz de bytes codificada de base64, como mostrado neste exemplo.
  
    
    

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


### Ler um arquivo em uma biblioteca de documentos

Para ler o conteúdo de um arquivo, execute uma operação **GET** na URL do arquivo, como mostrado no exemplo a seguir.
  
    
    

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


### Atualizar um arquivo em uma biblioteca de documentos

Para atualizar o conteúdo do arquivo, você poderá usar um objeto **FileCreationInformation** e definir o atributo de substituição como verdadeiro usando o método **set_overwrite()** como mostrado neste exemplo.
  
    
    

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


### Excluir um arquivo de uma biblioteca de documentos

Para excluir um arquivo, chame a função **deleteObject()** no objeto. O exemplo a seguir usa o método **getFileByServerRelativeUrl** para recuperar o arquivo da biblioteca de documentos e então exclua o item.
  
    
    

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


## Tarefas de item de lista do SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Para retornar itens de uma lista usando JavaScript, use a função **getItemById(id)** para retornar um item único ou use a função **getItems(query)** para retornar vários itens. Use então a função **load(clientObject)** para alcançar objetos de item de lista que representam os itens.
  
    
    

### Recuperar itens de uma lista

A função **getItems(query)** permite que você defina uma consulta Collaborative Application Markup Language (CAML) que especifique quais itens serão retornados. Você pode passar um objeto **CamlQuery** indefinido para retornar todos os itens da lista ou usar a função **set_viewXml** para definir uma consulta CAML e retornar itens que atendam critérios específicos. O exemplo a seguir exibe a ID, além dos valores de coluna Título e Corpo dos 100 primeiros itens da lista Anúncios, começando com itens de lista cuja ID da coleção maiores do que 10.
  
    
    

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


### Usar o método Include para acessar propriedades de objetos ListItem

Quatro propriedades de objetos **ListItem** não estarão disponíveis por padrão quando você retornar itens de lista  **displayName**, **effectiveBasePermissions**, **hasUniqueRoleAssignments** e **roleAssignments**. O exemplo anterior retornará uma **PropertyOrFieldNotInitializedException** se você tentar acessar uma dessas propriedades. Para acessar essas propriedades, use o método **Include** como parte da cadeia de caracteres de consulta, como mostrado no exemplo a seguir.
  
    
    

> [!OBSERVAçãO]
> Quando você usa o LINQ para criar consultas no modelo de objeto do cliente, estará usando o  [LINQ para objetos](http://msdn.microsoft.com/library/bb397919) e não o [LINQ para provedor do SharePoint](http://msdn.microsoft.com/library/ee535491), que só poderá ser usado quando você escrever código no modelo de objeto do servidor. 
  
    
    


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

Como este exemplo usa um **Include**, somente as propriedades especificadas estarão disponíveis após a execução da consulta. Portanto, você receberá uma **PropertyOrFieldNotInitializedException** se tentar acessar outras propriedades além daquelas que foram especificadas. Além disso, você receberá esse erro se tentar usar funções como **get_contentType** ou **get_parentList** para acessar as propriedades de objetos contidos.
  
    
    

### Restrições na recuperação de itens

O método **loadQuery(clientObjectCollection, exp)** do modelo de objeto JavaScript no SharePoint Foundation 2010 não dá suporte a métodos e operadores LINQ usados pelo modelo de objeto gerenciado.
  
    
    

## Criar, atualizar e excluir itens de lista
<a name="BasicOps_SPListItemCRUD"> </a>

A criação, a atualização ou a exclusão de itens de lista por meio do modelo de objeto do cliente funciona de maneira semelhante a executar essas tarefas por meio do modelo de objeto do servidor. Você cria um objeto de item de lista, define suas propriedades e então atualize o objeto. Para modificar ou excluir um objeto de item de lista, use a função **getById(id)** do objeto **ListItemCollection** para retornar o objeto e então defina propriedades e chame a atualização no objeto que esse método retorna ou chame o próprio método do objeto para exclusão. Ao contrário do modelo de objeto do servidor, cada uma dessas operações no modelo de objeto do cliente deverá ser concluída com uma chamada **to executeQueryAsync(succeededCallback, failedCallback)** para que as alterações entrem em vigor no servidor.
  
    
    

### Criar um item de lista

Para criar itens de lista, crie um objeto **ListItemCreationInformation**, defina suas propriedades e passe-o como parâmetro para a função **addItem(parameters)** do objeto **List**. Defina propriedades no objeto de item de lista retornado por esse método e então chame a função **update()**, como mostrado no exemplo a seguir.
  
    
    

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


### Atualizar um item de lista

Para definir a maioria das propriedades do item de lista, você poderá usar um indexador de coluna para criar uma atribuição e chamar a função **update()**, de forma que as alterações entrem em vigor quando você chamar **executeQueryAsync(succeededCallback, failedCallback)**. O exemplo a seguir define o título do terceiro item na lista Anúncios.
  
    
    

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


### Excluir um item de lista

Para excluir um item de lista, chame a função **deleteObject()** no objeto. O exemplo a seguir usa a função **getItemById(id)** para retornar o segundo item da lista e então excluir o item. O SharePoint mantém as IDs de inteiro de itens em coleções, mesmo se eles tiverem sido excluídos. Dessa forma, por exemplo, o segundo item em uma lista poderá não ter 2 como seu identificador. Uma **ServerException** será retornada caso a função **deleteObject()**seja chamada para um item que não exista.
  
    
    

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

Se quiser recuperar, por exemplo, a nova contagem de itens que resulte de uma operação de exclusão, inclua uma chamada para o método update() para atualizar a lista. Além disso, você deverá carregar o próprio objeto de lista ou a propriedade **itemCount** no objeto de lista antes de executar a consulta. Se quiser recuperar uma contagem de itens de lista inicial ou final, você deverá executar duas consultas e retornar a contagem de itens duas vezes, como mostrado na modificação a seguir feita no exemplo anterior.
  
    
    



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


## Acessar objetos no host da Web
<a name="BasicOps_AccessHostweb"> </a>

Durante o desenvolvimento do seu suplemento, talvez seja necessário acessar o host da Web para interagir com itens nele. Use o objeto **AppContextSite** para referenciar o host da Web ou outros sites do SharePoint, como mostrado no exemplo a seguir. Para obter um exemplo de código completo, confira [Obter o título do host da Web usando a biblioteca de domínio cruzado (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d).
  
    
    

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

O exemplo anterior usa a biblioteca de domínio cruzado no SharePoint 2013 para acessar o host da Web. Para saber mais, confira  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Recursos adicionais
<a name="BasicOps_AddRes"> </a>


-  [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  

