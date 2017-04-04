---
title: Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013
Découvrez comment écrire du code pour effectuer des opérations de base au moyen du modèle objet client (CSOM) SharePoint 2013 .NET Framework.
## API clientes SharePoint
<a name="ClientAPIs"> </a>

Vous pouvez utiliser le modèle objet client (CSOM) SharePoint pour extraire, mettre à jour et gérer des données dans SharePoint 2013. SharePoint 2013 rend le modèle objet disponible sous plusieurs formes.
  
    
    

- Assemblys redistribuables .NET Framework
    
  
- Bibliothèque JavaScript
    
  
- Points de terminaison REST/OData
    
  
- Assemblys Windows Phone
    
  
- Assemblys redistribuables Silverlight
    
  
Pour plus d'informations sur les jeux d'API disponibles sur la plateforme SharePoint 2013, voir  [Choisir l'ensemble d'API approprié dans SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
Cet article explique comment effectuer des opérations de base au moyen du modèle objet .NET Framework, qui est disponible comme package redistribuable dans le Centre de téléchargement Microsoft. Recherchez « SharePoint Server 2013 Client Components SDK » ou « SharePoint Online Client Components SDK ». Pour plus d'informations sur l'utilisation des autres API clientes, voir  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md),  [Créer des applications Windows Phone accédant à SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx) et [Utilisation du modèle objet Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx) dans le Kit de développement logiciel (SDK) SharePoint 2010.
  
    
    

## Opérations de base avec le modèle objet client SharePoint .NET
<a name="BasicOps_SPCSOMOps"> </a>

Les sections suivantes décrivent les tâches que vous pouvez effectuer par programmation et elles incluent des exemples de code C# pour illustrer les opérations du modèle objet client.
  
    
    
Lorsque vous créez un projet **Complément pour SharePoint 2013** dans Visual Studio 2012, les références aux assemblies .NET Framework, à **Microsoft.SharePoint.Client.Runtime.dll** et à **Microsoft.SharePoint.Client.dll**, sont automatiquement ajoutées au projet. Pour d'autres types de projets, tels que les applications ou consoles d'applications .NET Framework, vous devez ajouter ces références. Les fichiers se trouvent sur n'importe quel serveur SharePoint 2013 dans %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI.
  
    
    
Tous ces exemples partent du principe que le code se trouve dans un fichier code-behind pour une page web Microsoft ASP.NET. Vous devez ajouter l'instruction **using** suivante au fichier de code.
  
    
    


```

using Microsoft.SharePoint.Client;```

Sauf mention contraire, vous pouvez partir de l'hypothèse que chacun de ces exemples se trouve dans une méthode sans paramètre qui est définie dans la classe de la page. En outre,  `label1`,  `label2`, et ainsi de suite, sont les noms des objets  [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) sur la page.
  
    
    

> **REMARQUE**
> Lorsque vous créez une Complément SharePoint hébergée par un fournisseur avec une application web ASP.NET et que vous ajoutez une référence à un assembly dans le projet d'application web dans Visual Studio, définissez la propriété **Copie locale** de l'assembly sur **True**, sauf si vous savez que l'assembly est déjà installé sur le serveur web ou que vous pouvez vous assurer qu'il sera installé avant le déploiement de votre complément. .NET Framework est installé sur les rôles web Microsoft Azure et les Sites web Azure. Toutefois, les assemblys clients SharePoint 2013 et les différentes extensions de code managé et instances Microsoft Foundation ne sont pas installés. Les Outils de développement Office pour Visual Studio 2012 ajoutent automatiquement les références à certains assemblys fréquemment utilisés dans les compléments pour SharePoint et définissent la propriété **Copie locale**. 
  
    
    


## Tâches du site web SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Les exemples suivants indiquent comment utiliser le modèle objet client .NET Framework pour effectuer des tâches en lien avec le site web.
  
    
    

### Récupérer les propriétés d'un site web

Récupérez le titre d'un site web SharePoint.
  
    
    
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


### Récupérer uniquement les propriétés sélectionnées d'un site web

Parfois, le client n'est intéressé que par quelques propriétés d'un objet. Le modèle objet client SharePoint .NET Framework ne vous oblige pas à avoir toutes les propriétés de l'objet sur un serveur ; vous pouvez utiliser des méthodes anonymes, qui peuvent être des expressions lambda, pour demander spécifiquement les noms de propriétés. La bibliothèque cliente demandera uniquement ces propriétés sur le serveur, et le serveur enverra uniquement ces propriétés au client. Cette technique limite les transferts de données inutiles entre le client et le serveur. Elle s'avère également utile lorsque l'utilisateur n'a pas d'autorisation pour une ou plusieurs des autres propriétés inutilisées d'un objet. Remarquez que vous devrez ajouter une instruction **using** pour [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    
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


> **REMARQUE**
> Si vous essayez d'accéder à d'autres propriétés, le code génère une exception parce que les autres propriétés ne sont pas disponibles. 
  
    
    


### Modifier les propriétés du site web

L'exemple ci-dessous montre comment accéder en écriture aux propriétés du site web.
  
    
    
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


### Créer un nouveau site web SharePoint

L'exemple suivant illustre la création d'un nouveau site SharePoint en tant que sous-site du site web actuel. Utilisez la classe  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) pour créer un nouveau site web. Il vous faudra également ajouter des instructions **using** pour [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) et [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) .
  
    
    
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


## Tâches de liste SharePoint
<a name="BasicOps_SPListTasks"> </a>

Les exemples suivants indiquent comment utiliser le modèle objet client .NET Framework pour effectuer des tâches en lien avec la liste.
  
    
    

### Récupérer toutes les listes SharePoint sur un site web

L'exemple ci-dessous récupère toutes les listes SharePoint d'un site web SharePoint. Pour compiler ce code, il vous faudra ajouter une instruction **using** pour [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    
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


> **REMARQUE**
> Le cas échéant, vous pouvez utiliser la méthode **LoadQuery** pour stocker la valeur de retour dans une autre collection, au lieu d'utiliser la propriété **web.Lists**. Vous devrez également ajouter des instructions **using** pour [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) et [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Ajoutez également un alias à l'instruction using pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) pour pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`. 
  
    
    

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


### Créer et mettre à jour une liste SharePoint

Cet exemple crée une liste SharePoint et la met à jour au moyen de la classe  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) .
  
    
    
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


### Supprimer une liste SharePoint

Cet exemple supprime une liste SharePoint.
  
    
    
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


### Ajouter un champ à une liste SharePoint

L'exemple suivant ajoute un champ à une liste SharePoint. Ajoutez un alias à l'instruction using pour l'espace de noms  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) pour pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **REMARQUE**
> L'exemple utilise **context.CastTo** pour effectuer une conversion de type (transtypage). Avant d'exécuter la requête, la bibliothèque cliente ne connaît pas le type réel de l'objet renvoyé « field » et **SharePoint.Field** est le seul type possible. Si vous connaissez le type réel, vous pouvez utiliser la méthode **ClientContext.CastTo<RealType>** pour effectuer une conversion de type de l'objet.
  
    
    

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


## Tâches d'éléments de liste SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Les exemples suivants illustrent l'utilisation du modèle objet client .NET Framework pour effectuer des tâches qui sont liées aux éléments de liste.
  
    
    

### Récupérer les éléments d'une liste SharePoint

L'exemple suivant récupère les éléments d'une liste SharePoint. Vous devrez également ajouter une instruction **using** pour [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) .
  
    
    

> **REMARQUE**
> Vous pouvez utiliser la propriété  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) pour limiter davantage les éléments qui sont renvoyés à ceux figurant dans un dossier spécifique.
  
    
    

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


### Créer un nouvel élément de liste

Cet exemple crée un nouvel élément de liste SharePoint au moyen de la classe  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) .
  
    
    
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


### Mettre à jour un élément de liste

Cet exemple met à jour un élément de liste SharePoint.
  
    
    
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


### Supprimer un élément de liste

Cet exemple supprime un élément de liste SharePoint.
  
    
    
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


## Tâches de champ SharePoint
<a name="BasicOps_SPFieldTasks"> </a>

Les exemples suivants indiquent comment utiliser le modèle objet client .NET Framework SharePoint pour effectuer des tâches en lien avec le champ.
  
    
    

### Récupérer tous les champs d'une liste

L'exemple suivant récupère tous les champs d'une liste SharePoint. Vous aurez également besoin d'ajouter un alias à l'instruction **using** pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) de façon à pouvoir faire référence à ses classes de façon non ambiguë ; par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    
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


### Récupérer un champ spécifique dans la liste

Si vous voulez récupérer des informations relatives à un champ spécifique, utilisez la méthode **Fields.GetByInternalNameOrTitle**. Le type de retour de cette méthode correspond à **Field**. Avant l'exécution de la requête, le client ne connaît pas le type d'objet et la syntaxe C# n'est pas disponible pour son transtypage en type dérivé. Par conséquent, utilisez la méthode **ClientContext.CastTo** pour effectuer son transtypage, ce qui indique à la bibliothèque cliente de recréer un objet. Vous devrez également ajouter une instruction **using** pour [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) . Vous devrez également ajouter un alias à l'instruction **using** pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) pour pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **REMARQUE**
> La méthode  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) utilisée dans cet exemple est une méthode distante. Elle n'utilise pas les données de la collection cliente même si la collection cliente contient déjà des données.
  
    
    

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


## Tâches d'utilisateur SharePoint
<a name="BasicOps_SPSecurityTasks"> </a>

Vous pouvez utiliser le modèle objet client SharePoint .NET Framework pour gérer des utilisateurs, des groupes et la sécurité utilisateur SharePoint.
  
    
    

### Ajouter un utilisateur à un groupe SharePoint

Cet exemple ajoute un utilisateur et des informations relatives à celui-ci dans un groupe SharePoint intitulé Members.
  
    
    
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


### Récupérer tous les utilisateurs d'un groupe SharePoint

Cet exemple récupère les informations relatives à tous les utilisateurs dans un groupe SharePoint intitulé Members.
  
    
    
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


### Créer un rôle

Cet exemple crée un rôle disposant d'autorisations de création et de gestion des alertes.
  
    
    
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


### Ajouter un utilisateur à un rôle

Cet exemple ajoute un utilisateur à un rôle.
  
    
    
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


## Règles et pratiques recommandées pour l'utilisation du modèle objet client SharePoint .NET
<a name="Best"> </a>

Ces exemples illustrent certaines pratiques recommandées et conditions requises importantes auxquelles vous devez vous conformer lorsque vous utilisez le modèle objet client SharePoint .NET Framework.
  
    
    

### Appeler ClientContext.ExecuteQuery avant d'accéder à des propriétés de valeurs

Le modèle objet client SharePoint .NET Framework vous oblige à utiliser un modèle de programmation semblable à SQL : déclarez ce que vous voulez et exécutez la requête avant d'accéder aux données. Par exemple, le code suivant qui essaie d'afficher le titre du site web SharePoint va générer une exception.
  
    
    
```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  
```

Ce code échoue parce que le code du modèle objet client SharePoint .NET Framework doit :
  
    
    

1. Générer une requête SQL ou une procédure stockée ad hoc.
    
  
2. Exécuter la requête SQL.
    
  
3. Lire les résultats à partir du SQL.
    
  
Dans le modèle objet client SharePoint .NET Framework, lorsque vous appelez une méthode, vous générez une requête. Les requêtes s'accumulent et ne sont envoyées au serveur que lorsque  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) est appelé. L'exemple suivant présente le code nécessaire pour afficher le titre du site web. Il vous faudra également ajouter une instruction **using** pour [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Ajoutez également un alias à l'instruction **using** pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) de façon à pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   
```

Les différences résident au niveau de l'ajout des lignes suivantes :
  
    
    


```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
```

La première ligne crée une requête pour la propriété **Title** du site web. La deuxième ligne exécute la requête.
  
    
    

### Ne pas utiliser d'objets de valeurs renvoyés à partir de méthodes ou propriétés dans la même requête

Lorsqu'un objet de valeur est renvoyé à partir d'une méthode ou d'une propriété, vous ne pouvez utiliser cet objet qu'après avoir exécuté la requête. Par exemple, le code suivant essaye de créer une liste SharePoint présentant le même titre que le site web parent, mais il génère une exception.
  
    
    
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

Une exception est générée parce que la propriété n'est pas disponible avant d'avoir exécuté la requête. Dans SQL, vous déclareriez une variable locale pour contenir la valeur de  `web.Title` et utiliseriez la variable locale pour la création d'un site web. Dans la bibliothèque cliente, vous ne pouvez pas créer une variable locale. Vous devez fractionner les fonctionnalités en deux requêtes séparées comme illustré dans l'exemple suivant. Il vous faudra également ajouter une instruction **using** pour [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Ajoutez également un alias à l'instruction using pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) de façon à pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    


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

La différence réside dans les trois lignes suivantes :
  
    
    


```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 
```


### Utilisation de méthodes ou de propriétés renvoyant les objets clients dans un autre appel de méthode au sein de la même requête

Contrairement à un objet valeur, un objet client peut être utilisé dans un autre appel de méthode au sein de la même requête.
  
    
    
Dans une communication à distance .NET, l'objet valeur est une classe ou un struct qui est marshalé par valeur, tandis que l'objet client est une classe ou un struct qui est marshalé par référence. Par exemple, **ListItem** est un objet client, tandis que **UrlFieldValue** et les autres valeurs de champ sont des objets de valeur.
  
    
    
Dans la bibliothèque cliente, l'objet serveur correspondant présente l'attribut  `[ClientCallable(ValueObject = true)]`. Ces valeurs pourraient avoir uniquement des propriétés et aucune méthode. Les types primitifs, tels que les chaînes et les entiers (int), sont traités comme des objets valeur. Toutes les valeurs sont marshalées entre le client et le serveur. La valeur par défaut de **ValueObject** est **false**.
  
    
    
L'équivalent de l'objet valeur est l'objet client. Si l'objet serveur correspondant présente l'attribut **[ClientCallable(ValueObject = false)]**, l'objet est un objet client. Pour les objets clients, nous effectuons le suivi de la façon dont l'objet est créé ; il s'agit de **ObjectPath** dans la mise en œuvre de la bibliothèque cliente. Par exemple, si nous avons le code suivant :
  
    
    


```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 
```

Nous savons que la liste est créée de la façon suivante :
  
    
    

- Obtention de la propriété **Web** à partir du contexte.
    
  
- Obtention de la propriété **Lists** à partir du résultat ci-dessus.
    
  
- Appel de la méthode **GetByTitle** avec le paramètre _Announcements_ à partir du résultat ci-dessus.
    
  
Lorsque le modèle objet client SharePoint .NET Framework transmet ces informations au serveur, vous pouvez recréer l'objet sur le serveur. Dans la bibliothèque cliente, vous pouvez effectuer le suivi de l' **ObjectPath** que l'objet client a créé. Étant donné que vous savez la façon dont l'objet est créé, vous pourriez utiliser l'objet comme paramètre pour appeler d'autres méthodes au sein de la même requête.
  
    
    

### Regrouper l'extraction de données sur le même objet pour améliorer les performances

Lors de la lecture de plusieurs fragments de données à partir du même objet, vous pouvez essayer d'accéder à toutes les données au moyen d'une requête unique ; c'est-à-dire un appel unique à la méthode **Load<T>(T, [])**. Le code suivant illustre deux façons de récupérer le titre et la description d'un site web ainsi que la description de la liste **Announcements**. Pour compiler ce code, vous devez ajouter une instruction **using** pour [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . En outre, ajoutez un alias à l'instruction ( **statement**) using pour l'espace de noms  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) de façon à pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    
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

Ces méthodes ne présentent pas la même efficacité. Dans **Method1**, le code permettant d'extraire le titre et la description du site web est regroupé. Dans **Method2**, le code permettant d'extraire le titre et la description du site web est séparé par d'autres actions. Cela signifie que **Method2** déclenchera deux requêtes séparées sur le même objet de site web et qu'il y aura deux jeux de résultats pour le même site web. Étant donné que la bibliothèque cliente essaye de renvoyer des données cohérentes, le second jeu de résultats inclura à la fois le titre et la description. Le code précédent pourrait être représenté comme suit.
  
    
    


```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 
```


### Spécifier les propriétés des objets que vous voulez renvoyer

Dans le modèle objet serveur SharePoint, si vous obtenez un objet **SPWeb**, vous pouvez inspecter toutes ses propriétés. Dans SQL, pour obtenir toutes les colonnes d'une table, vous pouvez exécuter :
  
    
    
```

SELECT * FROM Webs ```

Dans la bibliothèque cliente, ni **Load<T>**, ni aucune autre méthode ne renvoie toutes les propriétés, de sorte que vous devez explicitement spécifier ce que vous voulez. Par exemple, le code suivant récupère l'objet de site web sans spécifier les propriétés à renvoyer. Il essaye ensuite de lire deux propriétés et l'une d'elles ne figure pas parmi les propriétés qui sont automatiquement renvoyées par **Load**. Ce code génère une exception.
  
    
    


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

Pour que le code soit compilé correctement, mettez-le à jour comme ci-dessous. Pour compiler ce code, vous devez ajouter une instruction **using** pour [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . En outre, ajoutez un alias à l'instruction **using** pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) de façon à pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    


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


### Utilisez l'étendue conditionnelle pour tester la présence de conditions préalables avant de charger des données

Pour exécuter du code de façon conditionnelle, définissez une étendue conditionnelle au moyen d'un objet  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) . Par exemple, récupérez la propriété de liste lorsque la liste n'est pas nulle. Il vous faudra également ajouter des instructions **using** pour [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) et [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . En outre, ajoutez un alias à l'instruction **using** pour l'espace de noms [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) de façon à pouvoir faire référence à ses classes de façon non ambiguë. Par exemple, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **REMARQUE**
> L'appel de méthodes et la définition de propriétés au sein d'une étendue conditionnelle ne sont pas autorisées, étant donné que la bibliothèque cliente n'effectue pas le suivi des effets secondaires liés aux appels de méthodes et aux définitions de propriétés. Veillez à utiliser uniquement **Load** au sein de l'étendue conditionnelle.
  
    
    

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


### Utiliser une étendue de gestion des exceptions pour intercepter des exceptions

Cet exemple illustre la création et l'utilisation d'une étendue de gestion des exceptions au moyen d'un objet  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) . Le scénario vise à mettre à jour la description d'une liste et également à activer la création d'un dossier. Il est possible que la liste n'existe pas.
  
    
    
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


## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Créer des sites pour SharePoint](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

