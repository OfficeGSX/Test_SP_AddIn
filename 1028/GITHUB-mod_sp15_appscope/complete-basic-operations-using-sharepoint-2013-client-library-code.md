---
title: 使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# 使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業
深入了解如何撰寫程式碼以使用 SharePoint 2013 .NET Framework 用戶端物件模型 (CSOM) 執行基本作業。
## SharePoint 用戶端 API
<a name="ClientAPIs"> </a>

您可以使用 SharePoint 用戶端物件模型 (CSOM) 以擷取、更新及管理 SharePoint 2013 中的資料。SharePoint 2013 提供數種使用 CSOM 的格式。
  
    
    

- .NET Framework 可轉散發的組件
    
  
- JavaScript 程式庫
    
  
- REST/OData 端點
    
  
- Windows Phone 組件
    
  
- Silverlight 可轉散發的組件
    
  
如需 SharePoint 2013 平台上可用之 API 集合的詳細資訊，請參閱 [選擇 [設定 SharePoint 2013 中的右 API](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。
  
    
    
本文顯示如何使用 .NET Framework 物件模型執行基本作業，該物件模型可以做為 Microsoft 下載中心的可轉散發的套件。搜尋 "SharePoint Server 2013 Client Components SDK" 或 "SharePoint Online Client Components SDK"。如需如何使用其他用戶端 API 的詳細資訊，請參閱 SharePoint 2010 SDK 中的 [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)、 [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)、 [建置存取 SharePoint 2013 的 Windows Phone 應用程式](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)，以及 [Using the Silverlight Object Model](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)。
  
    
    

## 使用 SharePoint .NET 用戶端物件模型的基本作業
<a name="BasicOps_SPCSOMOps"> </a>

下列章節說明您可以用程式設計的方式完成的工作，包含示範 CSOM 作業的 C# 程式碼範例。
  
    
    
當您在 Visual Studio 2012 中建立 **Add-in for SharePoint 2013** 專案時，.NET Framework 組件、 **Microsoft.SharePoint.Client.Runtime.dll** 及 **Microsoft.SharePoint.Client.dll** 的參考會自動新增至專案。對於其他類型的專案，例如 .NET Framework 應用程式或主控台應用程式，您應該新增這些參考。檔案位於 %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI 的任何 SharePoint 2013 伺服器。
  
    
    
這些範例假設程式碼對於 Microsoft ASP.NET 網頁是在程式碼後置檔案中。您必須將下列 **using** 陳述式新增至程式碼檔案。
  
    
    



```

using Microsoft.SharePoint.Client;
```

除非已有指定，否則您可以假設這些範例都是在無參數方法中，該方法是在頁面的類別中定義。另外， `label1`、 `label2` 等等是頁面上 [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) 物件的名稱。
  
    
    

> **注意事項**
> 當您製作由提供者裝載 SharePoint Add-in 與 ASP.NET Web 應用程式，並且將組件的參考新增至 Visual Studio 中的 Web 應用程式專案時，請將組件的 **Copy Local** 屬性設為 **True**，除非您知道該組件已安裝在網頁伺服器上，或者您可以確定在您部署增益集之前已安裝。.NET Framework 安裝在 Microsoft Azure Web 角色和 Azure 網站上。但是 SharePoint 2013 用戶端組件和各種 Microsoft 受管理的程式碼擴充和基礎則未安裝。Visual Studio 2012 Office 開發人員工具 會自動新增 SharePoint 增益集通用之部份組件的參考，並且設定 **Copy Local** 屬性。
  
    
    


## SharePoint 網站工作
<a name="BasicOps_SPWebTasks"> </a>

這些範例顯示如何使用 .NET Framework CSOM 以完成網站相關工作。
  
    
    

### 擷取網站的屬性

擷取 SharePoint 網站的標題。
  
    
    

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


### 擷取選取的網站屬性

有時候，用戶端只對物件的少數屬性有興趣。SharePoint .NET Framework CSOM 不需要您從伺服器上的物件取得所有屬性，您可以使用匿名方法，可以是 Lambda 運算式，特別要求屬性名稱。用戶端程式庫會在伺服器上查詢這些屬性，而伺服器只會傳送這些屬性給用戶端。此技術會減少用戶端與伺服器之間不必要的資料傳輸。當使用者對於物件上其他、未使用的一或多個屬性沒有權限時，這個技術也相當有用。請注意，您需要為  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。
  
    
    

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


> **注意事項**
> 如果您嘗試存取其他屬性，程式碼會擲回例外，因為沒有其他屬性可用。 
  
    
    


### 寫入網站的屬性

此範例顯示如何寫入網站的屬性。
  
    
    

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


### 建立新的 SharePoint 網站

此範例顯示如何建立新的 SharePoint 網站做為目前網站的子網站。使用  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) 類別以建立新的網站。您也必須為 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 和 [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) 新增 **using** 陳述式。
  
    
    

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


## SharePoint 清單工作
<a name="BasicOps_SPListTasks"> </a>

這些範例顯示如何使用 .NET Framework CSOM 以完成清單相關工作。
  
    
    

### 擷取網頁中所有 SharePoint 清單

此範例擷取 SharePoint 網站中的所有 SharePoint 清單。若要編譯此程式碼，您需要為  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。
  
    
    

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


> **注意事項**
> 或者，您可以使用 **LoadQuery** 方法以在其他集合中儲存傳回值，而不是使用 **web.Lists** 屬性。您也必須為 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 和 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。另外，為 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 using 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。 
  
    
    


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


### 建立及更新 SharePoint 清單

此範例使用  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) 類別建立 SharePoint 清單，以及更新清單。
  
    
    

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


### 刪除 SharePoint 清單

此範例刪除 SharePoint 清單。
  
    
    

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


### 將欄位新增到 SharePoint 清單

此範例會將欄位新增至 SharePoint 清單。為  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 using 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

> **注意事項**
> 此範例使用 **context.CastTo** 進行轉換。執行查詢之前，用戶端程式庫不知道傳回物件「欄位」的實際類型，且 **SharePoint.Field** 是唯一可能的類型。如果您知道實際類型，可以使用 **ClientContext.CastTo<RealType>** 方法來轉換物件。
  
    
    


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


## SharePoint 清單項目工作
<a name="BasicOps_SPListItemTasks"> </a>

這些範例示範如何使用 .NET Framework CSOM 以完成清單項目相關的工作。
  
    
    

### 從 SharePoint 清單擷取項目

此範例擷取 SharePoint 清單中的項目。您也必須為  [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) 新增 **using** 陳述式。
  
    
    

> **注意事項**
> 您可以使用  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) 屬性，進一步限制傳回指定資料夾的項目。
  
    
    


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


### 建立新的清單項目

此範例使用  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) 類別建立新的 SharePoint 清單項目。
  
    
    

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


### 更新清單項目

此範例更新 SharePoint 清單項目。
  
    
    

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


### 刪除清單項目

此範例刪除 SharePoint 清單項目。
  
    
    

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


## SharePoint 欄位工作
<a name="BasicOps_SPFieldTasks"> </a>

這些範例顯示如何使用 SharePoint .NET Framework CSOM 以完成欄位相關工作。
  
    
    

### 擷取清單中的所有欄位

此範例擷取 SharePoint 清單中的所有欄位，您必須為  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 **using** 的別名，以便明確地參考其類別，例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

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


### 從清單中擷取特定的欄位

如果您要擷取特定欄位的資訊，請使用 **Fields.GetByInternalNameOrTitle** 方法。此方法的傳回類型是 **Field**。在執行查詢之前，用戶端不知道物件的類型，且 C# 語法無法用於將其轉換為衍生的類型。因此，請使用 **ClientContext.CastTo** 方法進行轉換，該方法會指示用戶端程式庫重新建立物件。您也必須為 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 新增 **using** 陳述式。同時必須為 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 **using** 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

> **注意事項**
> 此範例中所使用的  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) 方法是遠端方法。即使已填入用戶端集合，它也不會使用來自用戶端集合的資料。
  
    
    


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


## SharePoint 使用者工作
<a name="BasicOps_SPSecurityTasks"> </a>

您可以使用 SharePoint .NET Framework CSOM 以管理 SharePoint 使用者、群組及使用者安全性。
  
    
    

### 將使用者新增至 SharePoint 群組

此範例將使用者和部份使用者資訊新增至名為「成員」的 SharePoint 群組。
  
    
    

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


### 擷取 SharePoint 群組中的所有使用者

此範例擷取名為「成員」之 SharePoint 群組的所有使用者資訊。
  
    
    

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


### 建立角色

此範例建立角色，該角色具有建立和管理警示權限。
  
    
    

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


### 將使用者新增至角色

此範例將使用者新增至角色。
  
    
    

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


## 使用 SharePoint .NET 用戶端物件模型的規則和最佳做法
<a name="Best"> </a>

這些範例說明當您使用 SharePoint .NET Framework CSOM 時，應該遵循的部份重要最佳做法和需求。
  
    
    

### 存取任何值屬性之前呼叫 ClientContext.ExecuteQuery

SharePoint .NET Framework CSOM 需要您使用類似 SQL 的程式設計模式：在您存取資料之前，宣告您想要的項目並且執行查詢。例如，下列程式碼，嘗試顯示 SharePoint 網站的標題，將會擲回例外。
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  

```

此程式碼失敗，因為 SharePoint .NET Framework CSOM 程式碼必須：
  
    
    

1. 建立臨機操作 SQL 查詢或預存程序。
    
  
2. 執行 SQL 查詢。
    
  
3. 從 SQL 讀取結果。
    
  
在 SharePoint .NET Framework CSOM 中，當您呼叫方法時會建立查詢。查詢會累積，並且在呼叫  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) 之前不會被傳送至伺服器。下列範例顯示若要顯示網站標題所需的程式碼。您也必須為 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。另外，也必須為 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 **using** 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   

```

差異在於新增這些行：
  
    
    



```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 

```

第一行建立網頁 **Title** 屬性的查詢。第二行執行查詢。
  
    
    

### 不使用相同查詢中從方法或屬性傳回的值物件

從方法或屬性傳回值物件時，您無法使用該物件，除非您已執行查詢。例如，下列程式碼會嘗試建立具有相同標題的 SharePoint 清單做為上層網站，但是會擲回例外。
  
    
    

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

會擲回例外，因為在您執行查詢之前，無法使用屬性。在 SQL 中，您宣告本機變數以保留  `web.Title` 的值，並且將本機變數用於網頁建立。在用戶端程式庫中，您無法建立本機變數。您必須將功能分割為兩個個別的查詢，如以下範例所示。您也必須為 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。另外，也必須為 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 using 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    



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

差異在於下列三行：
  
    
    



```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 

```


### 使用在相同查詢的其他方法中傳回用戶端物件的方法或屬性

與值物件不同，用戶端物件可以用於相同查詢中的其他方法呼叫。
  
    
    
在 .NET 遠端處理中，值物件是由值封送處理的類別或結構，而用戶端物件是由參考封送處理的類別或結構。例如， **ListItem** 是用戶端物件，而 **UrlFieldValue** 和其他欄位值是值物件。
  
    
    
在用戶端程式庫中，對應的伺服器物件具有  `[ClientCallable(ValueObject = true)]` 屬性。這些值只能有屬性，不能有方法。基本類型，例如字串和 int，會被視為值物件。所有值都是在用戶端與伺服器之間進行封送處理。 **ValueObject** 的預設值為 **false**。
  
    
    
與值物件相對的是用戶端物件。如果對應的伺服器物件具有 **[ClientCallable(ValueObject = false)]** 屬性，則物件為用戶端物件。若是用戶端物件，我們會追蹤物件如何建立，這在用戶端程式庫實作中稱為 **ObjectPath**。例如，如果有如下的程式碼：
  
    
    



```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 

```

我們知道清單的建立者為：
  
    
    

- 從內容取得 **Web** 屬性。
    
  
- 從上述結果取得 **Lists** 屬性。
    
  
- 根據上述結果使用  _Announcements_ 參數叫用 **GetByTitle** 方法。
    
  
當 SharePoint .NET Framework CSOM 將此資訊傳遞給伺服器時，您可以在伺服器上重新建立物件。在用戶端程式庫中，您可以追蹤建立用戶端物件的 **ObjectPath**。因為您知道物件如何建立，所以可以使用物件做為參數以在相同查詢中叫用其他方法。
  
    
    

### 同時在相同物件上進行群組資料擷取以改進效能

從相同物件讀取多個片段的資料時，您應該嘗試在單一查詢中取得所有資料，也就是對 **Load<T>(T, [])** 方法的單一呼叫。下列程式碼顯示兩個方式以擷取網站的標題和說明以及「宣告」清單的說明。若要編譯此程式碼，您必須為 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。另外，也必須為 using [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 using **statement** 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

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

其效率並不相同。在 **Method1** 中，用來擷取網頁標題和說明的程式碼是群組在一起。在 **Method2** 中，用來擷取網頁標題和說明的程式碼是由其他動作分隔，這表示 **Method2** 在相同的網頁物件上將會觸發兩個個別的查詢，對於相同的網頁將會有兩個結果集。因為用戶端程式庫嘗試傳回一致的資料，第二個結果集將會包含標題和說明。您可以思考先前的程式碼如下。
  
    
    



```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 

```


### 指定您要傳回物件的哪些屬性

在 SharePoint 伺服器物件模型中，如果您取得 **SPWeb** 物件，則可以檢查其所有屬性。在 SQL 中，若要取得表格的所有欄位，您可以執行：
  
    
    

```

SELECT * FROM Webs 
```

在用戶端程式庫中， **Load<T>** 或任何其他方法皆不會傳回所有屬性，因此您必須明確指定您想要的項目。例如，下列程式碼會擷取網站物件而不指定要傳回哪些屬性。然後，該程式碼會嘗試讀取兩個屬性，其中一個屬性不在 **Load** 自動傳回的屬性之中。此程式碼會擲回例外。
  
    
    



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

若要讓程式碼成功編譯，請將其更新如下。若要編譯此程式碼，您必須為  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。另外，也必須為 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 **using** 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    



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


### 使用條件範圍以在載入資料之前測試前置條件

若要有條件地執行程式碼，則設定條件範圍，方法是使用  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) 物件。例如，當清單非 null 時擷取清單屬性。您也必須為 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 和 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 新增 **using** 陳述式。另外，為 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空間新增 **using** 陳述式的別名，以便明確地參考其類別。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

> **注意事項**
> 不允許在條件範圍內呼叫方法和設定屬性，因為用戶端程式庫不會追蹤方法呼叫和屬性設定的副作用。您應該僅在條件範圍內使用 **Load**。 
  
    
    


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


### 使用例外處理範圍來攔截例外

此範例顯示如何透過  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) 物件，建立及使用例外處理範圍。本案例是要更新清單的說明，同時啟用資料夾建立。清單有可能並不存在。
  
    
    

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


## 其他資源
<a name="bk_addresources"> </a>


-  [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [建立 SharePoint 網站](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

