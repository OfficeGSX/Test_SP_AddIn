---
title: Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013
Aprenda a escrever o código para executar operações básicas com o modelo de objeto cliente (CSOM) SharePoint 2013 .NET Framework.
## APIs de cliente SharePoint
<a name="ClientAPIs"> </a>

Você pode usar o modelo de objeto cliente SharePoint (CSOM) para recuperar, atualizar e gerenciar dados no SharePoint 2013. O SharePoint 2013 disponibiliza o modelo de objeto de diversas formas. 
  
    
    

- Assemblies redistribuíveis do .NET Framework
    
  
- JavaScript biblioteca
    
  
- Pontos de extremidade REST/OData
    
  
- Assemblies do Windows Phone
    
  
- Assemblies redistribuíveis do Silverlight
    
  
Para saber mais sobre os conjuntos de APIs disponíveis para a plataforma SharePoint 2013, confira  [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
Este artigo mostra como executar operações básicas usando o modelo de objeto .NET Framework que está disponível como um pacote redistribuível no Centro de Download da Microsoft. Pesquise "SDK de componentes do cliente SharePoint Server 2013" ou "SDK online de componentes do cliente SharePoint". Para obter informações sobre como usar APIs de outro cliente, confira  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md),  [Crie aplicativos do Windows Phone que acessam o SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx) e [Using the Silverlight Object Model](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx) no SharePoint 2010 SDK.
  
    
    

## Operações básicas com o modelo de objeto cliente .NET do SharePoint
<a name="BasicOps_SPCSOMOps"> </a>

As seções a seguir descrevem tarefas que podem ser concluídas de maneira programática e que incluem exemplos de código do C#, que demonstram as operações CSOM.
  
    
    
Quando você cria um projeto de **Suplemento do SharePoint 2013** no Visual Studio 2012, as referências a assemblies .NET Framework, **Microsoft.SharePoint.Client.Runtime.dll** e **Microsoft.SharePoint.Client.dll** são automaticamente adicionadas ao projeto. Para outros tipos de projetos, como aplicativos ou aplicativos de console .NET Framework, é necessário adicionar essas referências. Os arquivos estão localizados em qualquer servidor SharePoint 2013 no ISAPI %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\.
  
    
    
Esses exemplos pressupõem que o código está em um arquivo code-behind de uma página da Web Microsoft ASP.NET. Você deve adicionar a seguinte instrução **using** ao arquivo de código.
  
    
    



```

using Microsoft.SharePoint.Client;
```

A menos que não tenha sido especificado, é necessário presumir que cada um desses exemplos é em um método sem parâmetros que foi definido na classe da página. Além disso,  `label1`,  `label2` e os demais são os nomes dos objetos [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) na página.
  
    
    

> [!OBSERVAçãO]
> Quando você estiver criando um provedor Suplemento do SharePoint hospedado com um aplicativo da Web ASP.NET e adicionar um assembly a um conjunto para o projeto do aplicativo da Web no Visual Studio, defina a propriedade **Copy Local** do assembly para **True**, a menos que você saiba que este já está instalado no servidor Web ou você possa garantir que ele está instalado antes de implantar o suplemento. O .NET Framework está instalado nas Microsoft Azure Funções da Web e em Azure Web Sites. Mas os assemblies cliente SharePoint 2013, bem como as diversas extensões de código e as fundações gerenciadas da Microsoft, não estão instalados. Office Developer Tools para Visual Studio 2012 adiciona automaticamente as referências para alguns assemblies usados com frequência nos Suplementos do SharePoint e define a propriedade **Cópia Local**. 
  
    
    


## Tarefas de site do SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Esses exemplos mostram como usar o CSOM .NET Framework para concluir tarefas relacionadas ao site.
  
    
    

### Recuperar as propriedades de um site

Recuperar o título de um site do SharePoint.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the 
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

// We want to retrieve the web's properties.
context.Load(web); 

// Execute the query to the server.
context.ExecuteQuery(); 

// Now, the web's properties are available and we could display 
// web properties, such as title. 
label1.Text = web.Title;

```


### Recuperar somente as propriedades selecionadas de um site

Às vezes, o cliente está interessado apenas em algumas propriedades do objeto. Com o CSOM .NET Framework do SharePoint, você não precisa armazenar todas as propriedades de objeto em um servidor. Você pode usar métodos anônimos, como expressões lambda, para solicitar nomes de propriedades. A biblioteca do cliente só realizará consultas para essas propriedades no servidor, e o servidor enviará somente essas propriedades para o cliente. Essa técnica reduz transferência desnecessária de dados entre o cliente e servidor. Além disso, também é útil quando o usuário não tem permissão para uma ou mais das propriedades não utilizadas em um objeto. Você também precisará adicionar uma instrução **using** para o [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

// We want to retrieve the web's title and description. 
context.Load(web, w => w.Title, w => w.Description); 

// Execute the query to server.
context.ExecuteQuery(); 

// Now, only the web's title and description are available. If you 
// try to print out other properties, the code will throw 
// an exception because other properties are not available. 
label1.Text = web.Title;
label1.Text = web. Description;

```


> [!OBSERVAçãO]
> Se você tentar acessar outras propriedades, o código lançará uma exceção porque não há outras propriedades disponíveis. 
  
    
    


### Gravar nas propriedades do site

Este exemplo mostra como realizar gravações nas propriedades do site.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

web.Title = "New Title"; 
web.Description = "New Description"; 

// Note that the web.Update() does not trigger a request to the server
// because the client library until ExecuteQuery() is called. 
web.Update(); 

// Execute the query to server.
context.ExecuteQuery(); 

```


### Criar um novo site do SharePoint

Este exemplo mostra como criar um novo site do SharePoint como um subsite do site atual. Use a classe  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) para criar um novo site. Você também precisará adicionar instruções **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) e [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) .
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

WebCreationInformation creation = new WebCreationInformation(); 
creation.Url = "web1"; 
creation.Title = "Hello web1"; 
Web newWeb = context.Web.Webs.Add(creation); 

// Retrieve the new web information. 
context.Load(newWeb, w => w.Title); 
context.ExecuteQuery(); 

label1.Text = newWeb.Title; 

```


## Tarefas de lista do SharePoint
<a name="BasicOps_SPListTasks"> </a>

Esses exemplos mostram como usar o CSOM .NET Framework para concluir tarefas da lista.
  
    
    

### Recuperar todas as listas do SharePoint em uma Web

Este exemplo recupera todas as listas do SharePoint em um site do mesmo. Para compilar este código,é necessário adicionar uma instrução **using** para o [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

// Retrieve all lists from the server. 
context.Load(web.Lists, 
             lists => lists.Include(list => list.Title, // For each list, retrieve Title and Id. 
                                    list => list.Id)); 

// Execute query. 
context.ExecuteQuery(); 

// Enumerate the web.Lists. 
foreach (List list in web.Lists) 
{ 
    label1.Text = label1.Text + ", " + list.Title; 
} 

```


> [!OBSERVAçãO]
> Se preferir, você pode usar o método **LoadQuery** para armazenar o valor de retorno em outro conjunto em vez de usar a propriedade **web.Lists**. Você também precisará adicionar instruções **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) e [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Além disso, é necessário adicionar um alias à instrução em uso para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`. 
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

// Retrieve all lists from the server, and put the return value in another 
// collection instead of the web.Lists. 
IEnumerable<SP.List> result = context.LoadQuery(web.Lists.Include( // For each list, retrieve Title and Id.
                                                                   list => list.Title, 
                                                                   list => list.Id)); 

// Execute query. 
context.ExecuteQuery(); 

// Enumerate the result. 
foreach (List list in web.Lists) 
{ 
    label1.Text = label1.Text + ", " + list.Title; 
} 

```


### Criar e atualizar uma lista do SharePoint

Este exemplo cria uma lista do SharePoint e a atualiza com a classe  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) .
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

ListCreationInformation creationInfo = new ListCreationInformation(); 
creationInfo.Title = "My List"; 
creationInfo.TemplateType = (int)ListTemplateType.Announcements; 
List list = web.Lists.Add(creationInfo); 
list.Description = "New Description"; 

list.Update(); 
context.ExecuteQuery(); 

```


### Excluir uma lista do SharePoint

Este exemplo exclui uma lista do SharePoint.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// The SharePoint web at the URL.
Web web = context.Web; 

List list = web.Lists.GetByTitle("My List"); 
list.DeleteObject(); 

context.ExecuteQuery();  

```


### Adicionar um campo a uma lista do SharePoint

Este exemplo adiciona um campo à lista do SharePoint. É necessário adicionar um alias à instrução em uso para o namespace  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> [!OBSERVAçãO]
> O exemplo usa **context.CastTo** para realizar uma conversão. Antes de executar a consulta, a biblioteca cliente não sabe o tipo real do "campo" do objeto retornado, e **SharePoint.Field** é o único tipo possível. Se você souber o tipo real, use o método **ClientContext.CastTo<RealType>** para converter o objeto.
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

SP.List list = context.Web.Lists.GetByTitle("Announcements"); 

SP.Field field = list.Fields.AddFieldAsXml("<Field DisplayName='MyField2' Type='Number' />", 
                                           true, 
                                           AddFieldOptions.DefaultValue); 
SP.FieldNumber fldNumber = context.CastTo<FieldNumber>(field); 
fldNumber.MaximumValue = 100; 
fldNumber.MinimumValue = 35; 
fldNumber.Update(); 

context.ExecuteQuery();  

```


## Tarefas de item de lista do SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Esses exemplos mostram como usar o CSOM .NET Framework para realizar tarefas relacionadas aos itens de lista.
  
    
    

### Recuperar itens de uma lista do SharePoint

Este exemplo recupera todos os itens de uma lista do SharePoint. Você também precisará adicionar uma instrução **using** para o [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) .
  
    
    

> [!OBSERVAçãO]
> Você pode usar a propriedade  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) para restringir ainda mais os itens que são retornados a eles em uma pasta especificada.
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// Assume the web has a list named "Announcements". 
List announcementsList = context.Web.Lists.GetByTitle("Announcements"); 

// This creates a CamlQuery that has a RowLimit of 100, and also specifies Scope="RecursiveAll" 
// so that it grabs all list items, regardless of the folder they are in. 
CamlQuery query = CamlQuery.CreateAllItemsQuery(100); 
ListItemCollection items = announcementsList.GetItems(query); 

// Retrieve all items in the ListItemCollection from List.GetItems(Query). 
context.Load(items); 
context.ExecuteQuery(); 
foreach (ListItem listItem in items) 
{ 
    // We have all the list item data. For example, Title. 
    label1.Text = label1.Text + ", " + listItem["Title"]; 
} 

```


### Criar um novo item de lista

Este exemplo cria uma nova lista do SharePoint e a atualiza com a classe  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) .
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// Assume that the web has a list named "Announcements". 
List announcementsList = context.Web.Lists.GetByTitle("Announcements"); 

// We are just creating a regular list item, so we don't need to 
// set any properties. If we wanted to create a new folder, for 
// example, we would have to set properties such as 
// UnderlyingObjectType to FileSystemObjectType.Folder. 
ListItemCreationInformation itemCreateInfo = new ListItemCreationInformation(); 
ListItem newItem = announcementsList.AddItem(itemCreateInfo); 
newItem["Title"] = "My New Item!"; 
newItem["Body"] = "Hello World!"; 
newItem.Update(); 

context.ExecuteQuery();  

```


### Atualizar um item de lista

Este exemplo atualiza um item de lista do SharePoint.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// Assume that the web has a list named "Announcements". 
List announcementsList = context.Web.Lists.GetByTitle("Announcements"); 

// Assume there is a list item with ID=1. 
ListItem listItem = announcementsList.GetItemById(1); 

// Write a new value to the Body field of the Announcement item.
listItem["Body"] = "This is my new value!!"; 
listItem.Update(); 

context.ExecuteQuery();  

```


### Excluir um item de lista

Este exemplo exclui um item de lista do SharePoint.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// Assume that the web has a list named "Announcements". 
List announcementsList = context.Web.Lists.GetByTitle("Announcements"); 

// Assume that there is a list item with ID=2. 
ListItem listItem = announcementsList.GetItemById(2); 
listItem.DeleteObject(); 

context.ExecuteQuery(); } 

```


## Tarefas de campo do SharePoint
<a name="BasicOps_SPFieldTasks"> </a>

Esses exemplos mostram como usar o CSOM .NET Framework do SharePoint para concluir tarefas de campo. 
  
    
    

### Recuperar todos os campos de uma lista

Este exemplo recupera todos os campos de uma lista do SharePoint. Você também precisará adicionar um alias à instrução **using** para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos; por exemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

SP.List list = context.Web.Lists.GetByTitle("Shared Documents"); 
context.Load(list.Fields); 

// We must call ExecuteQuery before enumerate list.Fields. 
context.ExecuteQuery(); 

foreach (SP.Field field in list.Fields) 
{ 
    label1.Text = label1.Text + ", " + field.InternalName;
} 

```


### Recuperar um campo específico da lista

Se você deseja recuperar informações sobre um campo específico, use o método **Fields.GetByInternalNameOrTitle**. O tipo de retorno desse método é **Field**. Antes que a consulta seja executada, o cliente não conhece o tipo do objeto e sintaxe C# não está disponível para ser direcionada ao tipo derivado. Portanto, use o método **ClientContext.CastTo** para convertê-la. Esse processo faz com que a biblioteca cliente recrie um objeto. Você também precisará adicionar uma instrução **using** para o [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) . Você também precisará adicionar um alias à instrução **using** para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> [!OBSERVAçãO]
> O método  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) usado neste exemplo é um método remoto. Ele não usa os dados do conjunto do cliente, mesmo se este já estiver preenchido.
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

SP.List list = context.Web.Lists.GetByTitle("Shared Documents"); 
SP.Field field = list.Fields.GetByInternalNameOrTitle("Title"); 
FieldText textField = context.CastTo<FieldText>(field); 
context.Load(textField); 
context.ExecuteQuery(); 

// Now, we can access the specific text field properties. 
label1.Text = textField.MaxLength; 

```


## Tarefas de usuário do SharePoint
<a name="BasicOps_SPSecurityTasks"> </a>

Você pode usar o CSOM .NET Framework do SharePoint para gerenciar usuários, grupos e a segurança do usuário do SharePoint. 
  
    
    

### Adicionar um usuário a um grupo do SharePoint

Este exemplo adiciona um usuário e algumas informações do usuário a um grupo do SharePoint denominado Membros.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

GroupCollection siteGroups = context.Web.SiteGroups; 

// Assume that there is a "Members" group, and the ID=5. 
Group membersGroup = siteGroups.GetById(5); 

// Let's set up the new user info. 
UserCreationInformation userCreationInfo = new UserCreationInformation(); 
userCreationInfo.Email = "user@domain.com"; 
userCreationInfo.LoginName = "domain\\\\user"; 
userCreationInfo.Title = "Mr User"; 

// Let's add the user to the group. 
User newUser = membersGroup.Users.Add(userCreationInfo); 

context.ExecuteQuery();  

```


### Recuperar todos os usuários de um grupo do SharePoint

Este exemplo obtém informações de todos os usuários de um grupo do SharePoint denominado Membros.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

GroupCollection siteGroups = context.Web.SiteGroups; 

// Assume that there is a "Members" group, and the ID=5. 
Group membersGroup = siteGroups.GetById(5); 
context.Load(membersGroup.Users); 
context.ExecuteQuery(); 

foreach (User member in membersGroup.Users) 
{ 
    // We have all the user info. For example, Title. 
    label1.Text = label1.Text + ", " + member.Title; 
}  

```


### Criar uma função

Este exemplo cria uma função que tem de criar e gerenciar permissões de alertas.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

BasePermissions perm = new BasePermissions(); 
perm.Set(PermissionKind.CreateAlerts); 
perm.Set(PermissionKind.ManageAlerts); 

RoleDefinitionCreationInformation creationInfo = new RoleDefinitionCreationInformation(); 
creationInfo.BasePermissions = perm; 
creationInfo.Description = "A role with create and manage alerts permission"; 
creationInfo.Name = "Alert Manager Role"; 
creationInfo.Order = 0; 
RoleDefinition rd = context.Web.RoleDefinitions.Add(creationInfo); 

context.ExecuteQuery();  

```


### Adicionar um usuário a uma função

Este exemplo adiciona um usuário a uma função.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

// Assume that we have a SiteUser with Login user. 
Principal user = context.Web.SiteUsers.GetByLoginName(@"domain\\user"); 

// Assume that we have a RoleDefinition named "Read". 
RoleDefinition readDef = context.Web.RoleDefinitions.GetByName("Read"); 
RoleDefinitionBindingCollection roleDefCollection = new RoleDefinitionBindingCollection(context); 
roleDefCollection.Add(readDef); 
RoleAssignment newRoleAssignment = context.Web.RoleAssignments.Add(user, roleDefCollection); 

context.ExecuteQuery();  

```


## Regras e práticas recomendadas para a utilização do modelo de objeto cliente .NET do SharePoint
<a name="Best"> </a>

Estes exemplos ilustram algumas práticas recomendadas importantes e requisitos que devem ser cumpridos para usar o CSOM .NET Framework do SharePoint.
  
    
    

### Chamar ClientContext.ExecuteQuery antes de acessar as propriedades de valor

O CSOM .NET Framework do SharePoint exige que você use um padrão de programação do tipo SQL: declarar o que você deseja e executar a consulta antes de acessar os dados. Por exemplo, o código a seguir tenta exibir o título do site do SharePoint e acaba criando uma exceção.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  

```

Esse código falha porque o código CSOM .NET Framework do SharePoint deve:
  
    
    

1. Crie uma consulta SQL ad hoc ou um procedimento armazenado.
    
  
2. Execute a consulta SQL.
    
  
3. Leia os resultados do SQL.
    
  
No CSOM .NET Framework do SharePoint, você cria uma consulta sempre que chama um método. As consultas são acumuladas e não são enviadas ao servidor até que  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) seja chamado. O exemplo a seguir mostra o código necessário para exibir o título do site. Você também precisará adicionar uma instrução **using** para [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Além disso, é necessário adicionar um alias à instrução **using** para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   

```

As diferenças são a adição das linhas a seguir:
  
    
    



```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 

```

A primeira linha cria uma consulta para a propriedade da Web **Title**. A segunda linha executa a consulta.
  
    
    

### Não use objetos de valor que tenham sido retornados de métodos ou propriedades da mesma consulta

Quando um objeto valor é retornado de um método ou propriedade, só será possível executá-lo depois da consulta. Por exemplo, o código a seguir tenta criar uma lista do SharePoint que tenha o mesmo título que o site pai, mas cria uma exceção. 
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
ListCreationInformation creationInfo = new ListCreationInformation(); 
creationInfo.Description = web.Title; 
creationInfo.Title = web.Title; 
List newList = web.Lists.Add(creationInfo);  

```

Essa exceção é criada porque a propriedade não está disponível até que uma consulta seja executada. No SQL, você poderia declarar uma variável local para armazenar o valor do  `web.Title` e usar a variável local para a criação da Web. Não é possível criar uma variável local na biblioteca do cliente. Você precisa dividir a funcionalidade em duas consultas separadas, conforme exibido no exemplo a seguir. Você também precisa adicionar uma instrução **using** para o [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Além disso, é necessário adicionar um alias à instrução em uso para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

ListCreationInformation creationInfo = new ListCreationInformation(); 
creationInfo.Description = web.Description; 
creationInfo.Title = web.Title; 
SP.List newList = web.Lists.Add(creationInfo); 

context.ExecuteQuery();  

```

A diferença está nas três linhas a seguir:
  
    
    



```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 

```


### Usando métodos ou propriedades que retornam objetos de cliente em outras chamadas de método na mesma consulta

Diferente de um objeto valor, um objeto cliente pode ser usado em outra chamada de método de uma mesma consulta. 
  
    
    
No .NET Remoting, o objeto valor é uma classe ou struct empacotada por valor, e o objeto cliente é uma classe ou struct empacotada por referência. Por exemplo, o **ListItem** é um objeto cliente, enquanto o **UrlFieldValue** e os outros valores de campo são objetos de valor.
  
    
    
Na biblioteca cliente, o objeto servidor correspondente tem o atributo  `[ClientCallable(ValueObject = true)]`. Esses valores podem ter apenas propriedades e nenhum método. Tipos primitivos, como cadeias de caracteres e integrações, são tratados como objetos de valor. Todos os valores são empacotados entre o cliente e servidor. O valor padrão do **ValueObject** é **false**. 
  
    
    
O representante do objeto valor é o objeto cliente. Se o objeto servidor correspondente apresentar o atributo **[ClientCallable(ValueObject = false)]**, o objeto será um objeto cliente. Para objetos de cliente, podemos manter o controle de como o objeto é criado; isso se chama **ObjectPath** na implementação da biblioteca cliente. Por exemplo, se temos um código como este:
  
    
    



```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 

```

Sabemos que a lista foi criada por:
  
    
    

- Obtendo a propriedade **Web** do contexto.
    
  
- Obtendo a propriedade **Lists** do resultado acima.
    
  
- Invocando o método **GetByTitle** com o parâmetro _Announcements_ do resultado acima.
    
  
Quando o CSOM .NET Framework do SharePoint passa essas informações para o servidor, é possível recriar o objeto no servidor. Na biblioteca do cliente, é possível acompanhar o **ObjectPath** no qual o objeto cliente é criado. Uma vez que você sabe como o objeto é criado, é possível usar o objeto como um parâmetro para chamar outros métodos de uma mesma consulta.
  
    
    

### Grupo de recuperação de dados no mesmo objeto para melhorar o desempenho

Ao ler várias partes de dados do mesmo objeto, você deve tentar juntar todas as informações em uma única consulta; ou seja, uma única chamada para o método **Load<T>(T, [])**. O código a seguir mostra duas maneiras de recuperar o título e a descrição de um site, bem como a descrição da lista **Comunicados**. Para compilar este código, você precisa adicionar uma instrução **using** para o [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Além disso, é necessário adicionar um alias ao **statement** em utilização para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`. 
  
    
    

```cs

static void Method1() 
{ 
    ClientContext context = new ClientContext("http://SiteUrl"); 
    Web web = context.Web; 
    SP.List list = web.Lists.GetByTitle("Announcements"); 
    context.Load(web, w => w.Title, w => w.Description); 
    context.Load(list, l => l.Description); 
    context.ExecuteQuery(); 
} 

static void Method2() 
{ 
    ClientContext context = new ClientContext("http://SiteUrl"); 
    Web web = context.Web; 
    SP.List list = web.Lists.GetByTitle("Announcements"); 
    context.Load(web, w => w.Title); 
    context.Load(list, l => l.Description); 
    context.Load(web, w => w.Description); 
    context.ExecuteQuery();  
} 

```

Eles não são igualmente eficientes. Em **Method1**, o código para recuperar o título e a descrição da Web está agrupado. Em **Method2**, o código para recuperar o título e a descrição da Web está separado por outras ações. Isso significa que **Method2** acionará duas consultas separadas no mesmo objeto da Web, e haverá dois conjuntos de resultados para a mesma Web. Como a biblioteca do cliente tenta retornar dados consistentes, o segundo conjunto de resultados incluirá o título e a descrição. Você pode pensar no código anterior da seguinte maneira.
  
    
    



```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 

```


### Especificar quais propriedades de objetos você deseja retornar

Se for possível recuperar um objeto **SPWeb** no modelo do objeto servidor SharePoint, você poderá inspecionar todas as suas propriedades. Para obter todas as colunas de uma tabela no SQL, você pode executar:
  
    
    

```

SELECT * FROM Webs 
```

Na biblioteca cliente, nem **Load<T>** nem outros métodos retornam todas as propriedades, de modo que é necessário especificar explicitamente o que você deseja. Por exemplo, o código a seguir recupera o objeto site da Web sem especificar as propriedades que precisam ser devolvidas. Em seguida, ele tenta ler duas propriedades, e uma delas não se encontra entre as propriedades que são automaticamente retornadas por **Load**. Este código gera uma exceção. 
  
    
    



```cs
// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
context.Load(web); 
context.ExecuteQuery(); 

Console.WriteLine(web.Title); 
Console.WriteLine(web.HasUniqueRoleAssignments);  

```

 Para que o código seja compilado com sucesso, é necessário atualizá-lo da maneira a seguir. Para compilar este código, você precisa adicionar uma instrução **using** para o [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Além disso, é necessário adicionar um alias à instrução **using** para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
context.Load(web); 
context.Load(web, web => web.HasUniqueRoleAssignments); 
context.ExecuteQuery(); 

Console.WriteLine(web.Title); 
Console.WriteLine(web.HasUniqueRoleAssignments);  

```


### Use o escopo condicional para testar pré-condições antes de carregar dados

Para executar códigos condicionalmente, use um objeto  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) para definir um escopo condicional. Por exemplo, recupere a propriedade de lista quando a lista não for nula. Você também precisará adicionar instruções **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) e [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Além disso, é necessário adicionar um alias à instrução **using** para o namespace [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , de modo que possa se referir às classes sem equívocos. Por exemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> [!OBSERVAçãO]
> O método de chamada e as propriedades de configuração presentes em um escopo condicional não são permitidas, porque a biblioteca do cliente não acompanha os efeitos colaterais das chamadas de método e as configurações de propriedade. Você deve usar somente o **Load** dentro do escopo condicional.
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

SP.List list = context.Web.GetCatalog(ListTemplateType.WebPartCatalog); 
BasePermissions perm = new BasePermissions(); 
perm.Set(PermissionKind.ManageLists); 

ConditionalScope scope = 
    new ConditionalScope(context, 
                         () => list.ServerObjectIsNull &amp;&amp; context.Web.DoesUserHavePermissions(perm).Value); 
using (scope.StartScope()) 
{ 
    context.Load(list, l => l.Title); 
} 
context.ExecuteQuery(); 

label1.Text = scope.TestResult.Value; 

if (scope.TestResult.Value) 
{ 
    label1.Text = list.Title; 
}  

```


### Use um escopo da manipulação de exceção para capturar exceções

Este exemplo mostra como criar e usar um escopo da manipulação de exceção com um objeto  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) . O cenário é atualizar a descrição de uma lista e ativar a criação da pasta. Há uma possibilidade de que a lista talvez não exista.
  
    
    

```cs


// Starting with ClientContext, the constructor requires a URL to the 
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

ExceptionHandlingScope scope = new ExceptionHandlingScope(context); 

using (scope.StartScope()) 
{ 
    using (scope.StartTry()) 
    { 
        List fooList = context.Web.Lists.GetByTitle("Sample"); 
        fooList.Description = "In Try Block"; 
        fooList.Update(); 
    } 
    using (scope.StartCatch()) 
    { 
        // Assume that if there's an exception, 
        // it can be only because there was no "Sample" list. 
        ListCreationInformation listCreateInfo = new ListCreationInformation(); 
        listCreateInfo.Title = "Sample"; 
        listCreateInfo.Description = "In Catch Block"; 
        listCreateInfo.TemplateType = (int)ListTemplateType.Announcements; 
        List fooList = context.Web.Lists.Add(listCreateInfo); 
    } 
    using (scope.StartFinally()) 
    { 
        List fooList = context.Web.Lists.GetByTitle("Sample"); 
        fooList.EnableFolderCreation = true; 
        fooList.Update(); 
    } 
} 

context.ExecuteQuery();  

```


## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Criar sites para o SharePoint](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

