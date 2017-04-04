---
title: Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode
Hier erfahren Sie, wie Sie Code zum Ausführen grundlegender Vorgänge mit dem SharePoint 2013 .NET Framework-Clientobjektmodell (CSOM) schreiben.
## SharePoint-Client-APIs
<a name="ClientAPIs"> </a>

Sie können das SharePoint-Clientobjektmodell (CSOM) zum Abrufen, Aktualisieren und Verwalten von Daten in SharePoint 2013 verwenden. SharePoint 2013 macht das CSOM in verschiedener Form verfügbar.
  
    
    

- Weitervertreibbare .NET Framework-Assemblys
    
  
- JavaScript-Bibliothek
    
  
- REST-/OData-Endpunkte
    
  
- Windows Phone-Assemblys
    
  
- Weitervertreibbare Silverlight-Assemblys
    
  
Weitere Informationen zu den Gruppen von APIs, die auf der SharePoint 2013-Plattform verfügbar sind, finden Sie unter  [Auswählen des richtigen API-Satzes in SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
In diesem Artikel wird beschrieben, wie Sie grundlegende Vorgänge mithilfe des .NET Framework-Objektmodells ausführen, das als weitervertreibbares Paket im Microsoft Download Center verfügbar ist. Suchen Sie nach „SDKs für SharePoint Server 2013-Clientkomponenten" oder „SKDs für SharePoint Online-Clientkomponenten". Informationen zur Verwendung der anderen Client-APIs finden Sie unter  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md),  [Erstellen von Windows Phone-Apps, die auf SharePoint 2013 zugreifen](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx) und [Verwenden des Silverlight-Objektmodells](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx) im SharePoint 2010 SDK.
  
    
    

## Grundlegende Vorgänge mit dem SharePoint .NET-Clientobjektmodell
<a name="BasicOps_SPCSOMOps"> </a>

In den folgenden Abschnitten werden Aufgaben beschrieben, die programmgesteuert ausgeführt werden können. Die Abschnitte enthalten C#-Codebeispiele, die CSOM-Vorgänge veranschaulichen.
  
    
    
Wenn Sie in Visual Studio 2012 ein **Add-In für SharePoint 2013**-Projekt erstellen, werden dem Projekt automatisch Verweise auf die .NET Framework-Assemblys **Microsoft.SharePoint.Client.Runtime.dll** und **Microsoft.SharePoint.Client.dll** hinzugefügt. Bei anderen Arten von Projekten, z. B. .NET Framework-Anwendungen oder Konsolenanwendungen, müssen Sie diese Verweise hinzufügen. Die Dateien befinden sich auf jedem SharePoint 2013-Server im Ordner %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI.
  
    
    
In allen Beispielen hier wird vorausgesetzt, dass der Code in einer CodeBehind-Datei für eine Microsoft ASP.NET-Webseite enthalten ist. Sie müssen der Codedatei die folgende **using**-Anweisung hinzufügen.
  
    
    



```

using Microsoft.SharePoint.Client;
```

Sofern nicht ausdrücklich etwas Anderes angegeben wird, kann davon ausgegangen werden, dass jedes dieser Beispiele in einer parameterlosen Methode enthalten ist, die in der Klasse der Seite definiert ist. Zudem gilt, dass  `label1`,  `label2` usw. die Namen von auf der Seite befindlichen [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) -Objekten sind.
  
    
    

> **HINWEIS**
> Wenn Sie ein vom Anbieter gehostetes SharePoint-Add-In mit einer ASP.NET-Webanwendung erstellen und einen Verweis zu einer Assembly des Webanwendungsprojekts in Visual Studio hinzufügen, müssen Sie die Eigenschaft **Lokal kopieren** der Assembly auf **True** festlegen, sofern Sie nicht sicher wissen, dass die Assembly bereits auf dem Web-Server installiert ist, oder Sie sicherstellen können, dass sie installiert wird, bevor Sie das Add-In bereitstellen. .NET Framework wird in Microsoft Azure-Webrollen und Azure-Websites installiert. Die SharePoint 2013-Client-Assemblys und die verschiedenen Erweiterungen durch verwalteten Code und Foundations von Microsoft werden nicht installiert. Office Developer Tools für Visual Studio 2012 fügt zu häufig in SharePoint-Add-Ins verwendeten Assemblys automatisch Verweise hinzu und legt die Eigenschaft **Lokal kopieren** fest.
  
    
    


## SharePoint-Websiteaufgaben
<a name="BasicOps_SPWebTasks"> </a>

In diesen Beispielen wird gezeigt, wie Sie mit dem .NET Framework-CSOM Aufgaben ausführen, die Websites betreffen.
  
    
    

### Abrufen der Eigenschaften einer Website

Rufen Sie den Titel einer SharePoint-Website ab.
  
    
    

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


### Abrufen ausgewählter Eigenschaften einer Website

Manchmal ist der Client nur an einigen Eigenschaften eines Objekts interessiert. Im SharePoint .NET Framework-CSOM ist es nicht erforderlich, alle Eigenschaften des Objekts auf dem Server abzurufen. Sie können mit anonymen Methoden, bei denen es sich um lambda-Ausdrücke handeln kann, bestimmte Eigenschaftennamen anfordern. Die Clientbibliothek fragt dann nur diese Eigenschaften vom Server ab, und der Server sendet nur diese Eigenschafen an den Client. Diese Technik bewirkt, dass weniger unnötige Daten zwischen Client und Server übermittelt werden. Sie ist auch hilfreich, wenn der Benutzer nicht berechtigt ist, auf eine oder mehrere der anderen, nicht verwendeten Eigenschaften des Objekts zuzugreifen. Beachten Sie, dass Sie eine **using**-Anweisung für  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen müssen.
  
    
    

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


> **HINWEIS**
> Beim Versuch, auf andere Eigenschaften zuzugreifen, wird eine Ausnahme ausgelöst, weil keine anderen Eigenschaften verfügbar sind. 
  
    
    


### Festlegen der Eigenschaften einer Website

In diesem Beispiel wird gezeigt, wie Sie Eigenschaften einer Website festlegen.
  
    
    

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


### Erstellen einer neuen SharePoint-Website

In diesem Beispiel wird gezeigt, wie Sie eine SharePoint-Website als untergeordnete Website der aktuellen Website erstellen. Verwenden Sie die  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) -Klasse zum Erstellen einer neuen Website. Zudem müssen Sie **using**-Anweisungen für  [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) und [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) hinzufügen.
  
    
    

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


## SharePoint-Listenaufgaben
<a name="BasicOps_SPListTasks"> </a>

In diesen Beispielen wird gezeigt, wie Sie unter Verwendung des .NET Framework-CSOM auf Listen bezogene Aufgaben ausführen.
  
    
    

### Abrufen aller SharePoint-Listen in einem Web

In diesem Beispiel werden alle SharePoint-Listen in einer SharePoint-Website abgerufen. Zum Kompilieren dieses Codes müssen Sie eine **using**-Anweisung für  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen.
  
    
    

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


> **HINWEIS**
> Stattdessen können Sie den Rückgabewert mithilfe der **LoadQuery**-Methode auch in einer anderen Auflistung statt der **web.Lists**-Eigenschaft speichern. Zudem müssen Sie **using**-Anweisungen für  [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) und [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen. Fügen Sie der using-Anweisung für den [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzu, damit Sie unzweideutig auf die Klassen dieses Namespace verweisen können. Beispiel: `using SP = Microsoft.SharePoint.Client;`. 
  
    
    


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


### Erstellen und Aktualisieren einer SharePoint-Liste

In diesem Beispiel wird eine SharePoint-Liste erstellt und mithilfe der  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) -Klasse aktualisiert.
  
    
    

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


### Löschen einer SharePoint-Liste

In diesem Beispiel wird eine SharePoint-Liste gelöscht.
  
    
    

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


### Hinzufügen eines Felds zu einer SharePoint-Liste

In diesem Beispiel wird ein Feld zu einer SharePoint-Liste hinzugefügt. Fügen Sie der using-Anweisung für den  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzu, damit Sie unzweideutig auf dessen Klassen verweisen können. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **HINWEIS**
> In diesem Beispiel wird mit **context.CastTo** eine Typumwandlung durchgeführt. Vor der Ausführung der Abfrage weiß die Clientbibliothek nicht, welchen Typ das zurückgegebene Objekt "field" eigentlich hat, und **SharePoint.Field** ist der einzig mögliche Typ. Wenn Sie den tatsächlichen Typ kennen, können Sie den Objekttyp mit der **ClientContext.CastTo<RealType>**-Methode umwandeln. 
  
    
    


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


## SharePoint-Listenelementaufgaben
<a name="BasicOps_SPListItemTasks"> </a>

In diesen Beispielen wird gezeigt, wie Sie das .NET Framework-CSOM zum Ausführen auf Listenelemente bezogener Aufgaben verwenden.
  
    
    

### Abrufen von Elementen aus einer SharePoint-Liste

In diesem Beispiel werden die Elemente einer SharePoint-Liste abgerufen. Zudem müssen Sie eine **using**-Anweisung für  [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) hinzufügen.
  
    
    

> **HINWEIS**
> Sie können die  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) -Eigenschaft verwenden, um die Elemente, die zurückgegeben werden, weiter auf die Elemente eines angegebenen Ordners zu beschränken.
  
    
    


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


### Erstellen eines neuen Listenelements

In diesem Beispiel wird mithilfe der  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) -Klasse ein neues SharePoint-Listenelement erstellt.
  
    
    

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


### Aktualisieren eines Listenelements

In diesem Beispiel wird ein SharePoint-Listenelement aktualisiert.
  
    
    

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


### Löschen eines Listenelements

In diesem Beispiel wird ein SharePoint-Listenelement gelöscht.
  
    
    

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


## SharePoint-Feldaufgaben
<a name="BasicOps_SPFieldTasks"> </a>

In diesen Beispielen wird gezeigt, wie Sie das SharePoint-.NET Framework-CSOM zum Ausführen auf Felder bezogener Aufgaben verwenden.
  
    
    

### Abrufen aller Felder einer Liste

In diesem Beispiel werden alle Felder aus einer SharePoint-Liste abgerufen. Sie müssen zudem der **using**-Anweisung für den  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzufügen, damit Sie unzweideutig auf dessen Klassen verweisen können; zum Beispiel `using SP = Microsoft.SharePoint.Client;`.
  
    
    

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


### Verweisen auf ein bestimmtes Feld der Liste

Wenn Sie Informationen aus einem bestimmten Feld abrufen möchten, verwenden Sie die **Fields.GetByInternalNameOrTitle**-Methode. Diese Methode hat den Rückgabetyp **Field**. Vor der Ausführung der Abfrage weiß der Client nicht, welchen Typ das Objekt hat, und die C#-Syntax ist nicht zur Umwandlung des Objekttyps in den abgeleiteten Typ verfügbar. Daher wandeln Sie den Typ mit der **ClientContext.CastTo**-Methode um, welche die Clientbibliothek anweist, das Objekt neu zu erstellen. Zudem müssen Sie eine **using**-Anweisung für  [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) hinzufügen. Außerdem müssen Sie der **using**-Anweisung für den  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzufügen, damit Sie eindeutig auf dessen Klassen verweisen können. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **HINWEIS**
> Die in diesem Beispiel verwendete  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) -Methode ist eine Remotemethode. Sie verwendet selbst dann nicht die Daten aus der Clientauflistung, wenn diese bereits Daten enthält.
  
    
    


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


## SharePoint-Benutzeraufgaben
<a name="BasicOps_SPSecurityTasks"> </a>

Sie können mithilfe des SharePoint .NET Framework-CSOM SharePoint-Benutzer, -Gruppen und -Benutzersicherheit verwalten.
  
    
    

### Hinzufügen eines Benutzers zu einer SharePoint-Gruppe

In diesem Beispiel werden ein Benutzer und einige Benutzerinformationen einer SharePoint-Gruppe namens "Members" hinzugefügt.
  
    
    

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


### Abrufen aller Benutzer einer SharePoint-Gruppe

In diesem Beispiel werden Informationen zu allen Benutzern einer SharePoint-Gruppe namens "Members" abgerufen.
  
    
    

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


### Erstellen einer Rolle

In diesem Beispiel wird eine Rolle erstellt, die über Berechtigungen zum Erstellen und Verwalten von Benachrichtigungen verfügt.
  
    
    

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


### Hinzufügen eines Benutzers zu einer Rolle

In diesem Beispiel wird ein Benutzer einer Rolle hinzugefügt.
  
    
    

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


## Regeln und bewährte Methoden für die Verwendung des SharePoint .NET-Clientobjektmodells
<a name="Best"> </a>

In diesen Beispielen werden einige wichtige bewährte Methoden und Anforderungen veranschaulicht, die Sie bei Verwendung des SharePoint .NET Framework-CSOM beachten müssen.
  
    
    

### Aufrufen von ClientContext.ExecuteQuery vor dem Zugriff auf Werteigenschaften

Im SharePoint .NET Framework-CSOM müssen Sie ein Programmiermuster im Stil von SQL verwenden: Sie deklarieren, was gewünscht ist, und führen die Abfrage aus, bevor Sie auf die Daten zugreifen. Beispielsweise löst der folgende Code, mit dem der Titel einer SharePoint-Website angezeigt werden soll, eine Ausnahme aus.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  

```

Dieser Code verursacht einen Fehler, weil SharePoint .NET Framework-CSOM-Code folgende Aktionen ausführen muss:
  
    
    

1. Entweder eine Ad-hoc-SQL-Abfrage oder eine gespeicherte Prozedur erstellen.
    
  
2. Die SQL-Abfrage ausführen.
    
  
3. Die Ergebnisse der SQL-Abfrage lesen.
    
  
Wenn Sie im SharePoint .NET Framework-CSOM eine Methode aufrufen, erstellen Sie eine Abfrage. Die Abfragen werden gesammelt und erst beim Aufruf von  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) an den Server gesendet. Das folgende Beispiel enthält den Code, der erforderlich ist, um den Titel der Website anzuzeigen. Zudem müssen Sie eine **using**-Anweisung für  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen. Fügen Sie außerdem der **using**-Anweisung für den  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzu, damit Verweise auf Klassen dieses Namespace eindeutig sind. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   

```

Der Unterschied besteht in der Hinzufügung der folgenden Zeilen:
  
    
    



```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 

```

In der ersten Zeile wird eine Abfrage für die **Title**-Eigenschaft der Website erstellt. In der zweiten Zeile wird die Abfrage ausgeführt.
  
    
    

### Von Methoden oder Eigenschaften zurückgegebene Wertobjekte dürfen nicht in derselben Abfrage verwendet werden

Wenn ein Wertobjekt von einer Methode oder Eigenschaft zurückgegeben wird, kann dieses Objekt erst verwendet werden, nachdem die Abfrage ausgeführt worden ist. Beispielsweise wird im folgenden Code versucht, eine SharePoint-Liste zu erstellen, die den gleichen Titel wie die übergeordnete Website hat. Der Code löst jedoch eine Ausnahme aus.
  
    
    

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

Es wird eine Ausnahme ausgelöst, weil die Eigenschaft vor der Ausführung der Abfrage nicht verfügbar ist. In SQL würden Sie eine lokale Variable erstellen, um den Wert für  `web.Title` zu speichern, und diese lokale Variable dann zur Weberstellung verwenden. Die Clientbibliothek bietet nicht die Möglichkeit zur Erstellung lokaler Variablen. Sie müssen die Funktionalität in zwei getrennte Abfragen aufteilen, wie im folgenden Beispiel gezeigt. Zudem müssen Sie eine **using**-Anweisung für  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen. Fügen Sie außerdem der using-Anweisung für den [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzu, damit Verweise auf Klassen dieses Namespace eindeutig sind. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    



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

Unterschiedlich sind die folgenden drei Zeilen:
  
    
    



```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 

```


### Verwenden von Methoden oder Eigenschaften, die Clientobjekte zurückgeben, in einem anderen Methodenaufruf derselben Abfrage

Im Gegensatz zu Wertobjekten können Clientobjekte in einem anderen Methodenaufruf derselben Abfrage verwendet werden.
  
    
    
In .NET Remoting ist das Wertobjekt eine Klasse oder eine Struktur, die nach dem Wert gemarshallt wird, während ein Clientobjekt eine Klasse oder Struktur darstellt, die nach dem Verweis gemarshallt wird. Beispielsweise ist **ListItem** ein Clientobjekt, und **UrlFieldValue** und andere Feldwerte sind dagegen Wertobjekte.
  
    
    
In der Clientbibliothek verfügt das entsprechende Serverobjekt über das  `[ClientCallable(ValueObject = true)]`-Attribut. Diese Werte können nur Eigenschaften und keine Methoden besitzen. Einfache Typen, wie beispielsweise Zeichenfolgen und Ganzzahlen, werden wie Wertobjekte behandelt. Alle Werte werden zwischen Client und Server gemarshallt. Der Standardwert von **ValueObject** lautet **false**.
  
    
    
Das Clientobjekt ist das Gegenstück zum Wertobjekt. Wenn das entsprechende Serverobjekt über das **[ClientCallable(ValueObject = false)]**-Attribut verfügt, ist das Objekt ein Clientobjekt. Bei Clientobjekten wird verfolgt, wie das Objekt erstellt wird; dies wird in der Clientbibliotheksimplementierung als **ObjectPath** bezeichnet. Wenn beispielsweise Code wie der Folgende vorliegt:
  
    
    



```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 

```

Wir wissen, dass die Liste wie folgt erstellt wird:
  
    
    

- Abrufen der **Web**-Eigenschaft aus dem Kontext.
    
  
- Abrufen der **Lists**-Eigenschaft aus dem obigen Ergebnis.
    
  
- Aufrufen der **GetByTitle**-Methode mit dem  _Announcements_-Parameter aus dem obigen Ergebnis.
    
  
Wenn das SharePoint .NET Framework-CSOM diese Informationen an den Server übergibt, können Sie das Objekt auf dem Server neu erstellen. In der Clientbibliothek können Sie den **ObjectPath** der Clientobjekterstellung verfolgen. Weil Sie wissen, wie das Objekt erstellt wird, können Sie das Objekt als Parameter zum Aufruf anderer Methoden innerhalb derselben Abfrage verwenden.
  
    
    

### Gruppieren des Abrufs von Daten vom selben Objekt zur Optimierung der Leistung

Wenn verschiedene Datenelemente aus demselben Objekt gelesen werden, sollten Sie versuchen, alle Daten in einer Abfrage zu erfassen, d. h. in einem Aufruf der **Load<T>(T, [])**-Methode. Im folgenden Code werden zwei Möglichkeiten gezeigt, wie der Titel und die Beschreibung einer Website und die Beschreibung der Liste **Announcements** abgerufen werden können. Zum Kompilieren dieses Codes müssen Sie eine **using**-Anweisung für  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen. Fügen Sie außerdem der using-Anweisung **statement** für den [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzu, damit Verweise auf Klassen dieses Namespace eindeutig sind. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

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

Diese Methoden sind nicht gleich effizient. In **Method1** wurde der Code zum Abrufen des Titels und der Beschreibung der Website zusammen gruppiert. In **Method2** wird der Code zum Abrufen des Titels und der Beschreibung der Website durch andere Aktionen unterbrochen. Daher löst **Method2** zwei getrennte Abfragen für dasselbe Webobjekt aus, und es werden zwei Ergebnismengen für dasselbe Web erzeugt. Weil die Clientbibliothek versucht, konsistente Daten zurückzugeben, enthält die zweite Ergebnismenge sowohl den Titel als auch die Beschreibung. Der obige Code lässt sich auch so darstellen.
  
    
    



```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 

```


### Angeben, welche Objekteigenschaften zurückgegeben werden sollen

Im SharePoint-Serverobjektmodell können alle Eigenschaften eines **SPWeb**-Objekts untersucht werden. Um in SQL alle Spalten einer Tabelle zu erhalten, können Sie folgende Anweisung ausführen:
  
    
    

```

SELECT * FROM Webs 
```

In der Clientbibliothek gibt weder **Load<T>** noch irgend eine andere Methode alle Eigenschaften zurück. Sie müssen daher explizit angeben, was zurückgegeben werden soll. Beispielsweise wird im folgenden Code das Websiteobjekt ohne Angabe der gewünschten Eigenschaften abgerufen. Dann wird versucht, zwei Eigenschaften zu lesen, und eine Eigenschaft ist nicht unter den Eigenschaften, die von **Load** automatisch zurückgegeben werden. Dieser Code löst eine Ausnahme aus.
  
    
    



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

Damit der Code erfolgreich kompiliert wird, müssen Sie ihn wie folgt aktualisieren. Sie müssen eine **using**-Anweisung für  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen. Fügen Sie außerdem der **using**-Anweisung für den  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzu, damit Verweise auf Klassen dieses Namespace eindeutig sind. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    



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


### Verwenden eines bedingten Bereichs zum Testen von Vorbedingungen vor dem Laden von Daten

Zur bedingten Ausführung von Code legen Sie unter Verwendung eines  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) -Objekts einen bedingten Bereich fest. Sie rufen beispielsweise die Listeneigenschaft ab, wenn die Liste nicht leer ist. Zudem müssen Sie **using**-Anweisungen für  [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) und [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) hinzufügen. Außerdem müssen Sie der **using**-Anweisung für den  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) -Namespace einen Alias hinzufügen, damit Sie eindeutig auf die Klassen dieses Namespace verweisen können. Beispiel: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **HINWEIS**
> Es ist nicht zulässig, in einem bedingten Bereich Methoden aufzurufen und Eigenschaften festzulegen, weil die Clientbibliothek die Nebenwirkungen der Methodenaufrufe und Eigenschafteneinstellungen nicht verfolgen kann. Im bedingten Bereich sollten Sie nur **Load** verwenden.
  
    
    


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


### Verwenden eines Ausnahmebehandlungsbereichs zum Abfangen von Ausnahmen

In diesem Beispiel wird gezeigt, wie Sie mit einem  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) -Objekt einen Ausnahmebehandlungsbereich erstellen und verwenden. In diesem Szenario soll die Beschreibung einer Liste aktualisiert und die Ordnererstellung ermöglicht werden. Die Liste ist möglicherweise nicht vorhanden.
  
    
    

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


## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Erstellen von Websites für SharePoint](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

