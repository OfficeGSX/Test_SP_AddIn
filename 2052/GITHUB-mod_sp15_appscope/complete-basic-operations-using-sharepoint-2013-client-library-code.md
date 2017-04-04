---
title: 使用 SharePoint 2013 客户端库代码完成基本操作
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# 使用 SharePoint 2013 客户端库代码完成基本操作
了解如何编写代码以使用 SharePoint 2013 .NET Framework 客户端对象模型 (CSOM) 执行基本操作。
## SharePoint 客户端 API
<a name="ClientAPIs"> </a>

您可使用 SharePoint 客户端对象模型 (CSOM) 检索、更新和管理 SharePoint 2013 中的数据。SharePoint 2013 使 CSOM 在若干窗体中可用。
  
    
    

- .NET Framework 可再发行程序集
    
  
- JavaScript 库
    
  
- REST/OData 终结点
    
  
- Windows Phone 程序集
    
  
- Silverlight 可再发行程序集
    
  
有关 SharePoint 2013 平台上可用的 API 集的详细信息，请参阅 [在 SharePoint 2013 中选择正确的 API 集](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。
  
    
    
本文演示如何使用 .NET Framework 对象模型执行基本操作，此对象模型在 Microsoft 下载中心上作为可再发行包提供。搜索"SharePoint Server 2013 客户端组件 SDK"或"SharePoint Online 客户端组件 SDK"。有关如何使用其他客户端 API 的信息，请参阅 SharePoint 2010 SDK 中的 [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)、 [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)、 [构建访问 SharePoint 2013 的 Windows Phone 应用程序](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)和 [使用 Silverlight 对象模型](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)。
  
    
    

## 使用 SharePoint .NET 客户端对象模型执行的基本操作
<a name="BasicOps_SPCSOMOps"> </a>

下面各节介绍您可以编程方式完成的任务，这些内容包括演示 CSOM 操作的 C# 代码示例。
  
    
    
当您在 Visual Studio 2008 中创建"SharePoint 2013 加载项"项目时，对 .NET Framework 程序集、 **Microsoft.SharePoint.Client.Runtime.dll** 和 **Microsoft.SharePoint.Client.dll** 的引用都将自动添加到该项目中。对于其他类型的项目（如 .NET Framework 应用程序或控制台应用程序），您应添加这些引用。这些文件位于 %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI 处的任何 SharePoint 2013 服务器上。
  
    
    
所有这些示例均假定代码位于 Microsoft ASP.NET 网页的代码隐藏文件中。您必须将以下 **using** 语句添加到代码文件中。
  
    
    


```

using Microsoft.SharePoint.Client;```

除了指定的内容之外，您可假定这些示例中的每一个示例均位于在页面的类中定义的无参数方法中。此外， `label1`、 `label2` 等为页面上 [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) 对象的名称。
  
    
    

> **注释**
> 在您使用 ASP.NET Web 应用程序实现提供程序托管的 SharePoint 外接程序以及将对程序集的引用添加到 Visual Studio 中的 Web 应用程序项目时，将程序集的"Copy Local"属性设置为 **True**，除非您知道 Web 服务器上已经安装了程序集，或者可以确保程序集是在您部署外接程序之前安装的。Microsoft Azure Web 角色和 Azure 网站 上安装了 .NET Framework。但是未安装 SharePoint 2013 客户端程序集和和各种 Microsoft 托管代码扩展和基础。Visual Studio 2012 Office 开发人员工具 自动添加对 SharePoint 外接程序中某些常用的程序集的引用，并设置"Copy Local" 属性。 
  
    
    


## SharePoint 网站任务
<a name="BasicOps_SPWebTasks"> </a>

这些示例演示如何使用 .NET Framework CSOM 完成与网站相关的任务。
  
    
    

### 检索网站的属性

检索 SharePoint 网站的标题。
  
    
    
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


### 仅检索网站的选定属性

有时，客户端仅对某个对象的部分属性感兴趣。SharePoint .NET Framework CSOM 不要求您获取服务器上的对象的所有属性 - 您可使用匿名方法（其可以是 lambda 表达式）专门请求属性名称。客户端库将在服务器上查询这些属性，并且服务器仅将这些属性发送给客户端。此技术减少了客户端和服务器之间不必要的数据传输。这还在用户对对象上的一个或多个其他未使用的属性无权限时有用。请注意，您将需要为  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。
  
    
    
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


> **注释**
> 如果您尝试访问其他属性，则代码会因其他属性不可用而引发异常。 
  
    
    


### 写入网站的属性

此示例演示如何写入网站的属性。
  
    
    
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


### 创建新的 SharePoint 网站

此示例演示如何创建新的 SharePoint 网站作为当前网站的子网站。使用  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) 类创建新的网站。您还需要为 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 和 [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) 添加 **using** 语句。
  
    
    
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


## SharePoint 列表任务
<a name="BasicOps_SPListTasks"> </a>

这些示例演示如何使用 .NET Framework CSOM 完成与列表相关的任务。
  
    
    

### 检索 Web 中的所有 SharePoint 列表

此示例检索 SharePoint 网站中的所有 SharePoint 列表。若要编译此代码，您需要为  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。
  
    
    
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


> **注释**
> 另外，您可使用 **LoadQuery** 方法将返回值存储到另一个集合，而不是使用 **web.Lists** 属性。您还需要为 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 和 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。此外，向 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 using 语句添加一个别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。 
  
    
    

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


### 创建和更新 SharePoint 列表

此示例使用  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) 类创建 SharePoint 列表并更新它。
  
    
    
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


### 删除 SharePoint 列表

此示例将删除 SharePoint 列表。
  
    
    
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


### 向 SharePoint 列表添加域

此示例向 SharePoint 列表添加域。向  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 using 语句添加一个别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

> **注释**
> 此示例使用 **context.CastTo** 执行转换。执行查询之前，客户端库不知道返回对象"field"的实际类型以及 **SharePoint.Field** 是唯一可能的类型。如果您知道实际类型，则可使用 **ClientContext.CastTo<RealType>** 方法转换对象。
  
    
    

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


## SharePoint 列表项任务
<a name="BasicOps_SPListItemTasks"> </a>

这些示例演示如何使用 .NET Framework CSOM 完成与列表项相关的任务。
  
    
    

### 检索 SharePoint 列表中的项

此示例检索 SharePoint 列表中的项。您还需要为  [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) 添加 **using** 语句。
  
    
    

> **注释**
> 您可使用  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) 属性进一步限制返回到指定文件夹中的项。
  
    
    

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


### 创建新的列表项

此示例使用  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) 类创建新的 SharePoint 列表项。
  
    
    
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


### 更新列表项

此示例更新 SharePoint 列表项。
  
    
    
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


### 删除列表项

此示例删除 SharePoint 列表项。
  
    
    
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


## SharePoint 域任务
<a name="BasicOps_SPFieldTasks"> </a>

这些示例演示如何使用 SharePoint .NET Framework CSOM 完成与域相关的任务。
  
    
    

### 检索列表中的所有域

此示例检索 SharePoint 列表中的所有域。您还需要向  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 **using** 语句添加别名，以便您可清楚地引用其类；例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    
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


### 从列表中检索特定字段

如果要检索有关特定字段的信息，请使用 **Fields.GetByInternalNameOrTitle** 方法。此方法的返回类型为 **Field**。在执行查询之前，客户端不清楚对象的类型，并且 C# 语法不可用于将其转换为派生类型。因此，请使用 **ClientContext.CastTo** 方法转换它，其指导客户端库重新创建对象。您还需要为 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 添加 **using** 语句。您还需要为 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 **using** 语句添加别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

> **注释**
> 此示例中使用的  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) 方法是远程方法。它不使用客户端集合中的数据，即使已填充客户端操作也是如此。
  
    
    

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


## SharePoint 用户任务
<a name="BasicOps_SPSecurityTasks"> </a>

您可使用 SharePoint .NET Framework CSOM 管理 SharePoint 用户、组和用户安全。
  
    
    

### 向 SharePoint 组添加用户

此示例向名为"成员"的 SharePoint 组添加一名用户和一些用户信息。
  
    
    
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


### 检索 SharePoint 组中的所有用户

此示例检索有关名为"成员"的 SharePoint 组中的所有用户的信息。
  
    
    
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


### 创建角色

此示例创建具有创建和管理通知权限的角色。
  
    
    
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


### 向角色添加用户

此示例向角色添加用户。
  
    
    
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


## 使用 SharePoint .NET 客户端对象模型的规则和最佳实践
<a name="Best"> </a>

这些示例阐释您使用 SharePoint .NET Framework CSOM 时应遵循的一些重要最佳实践和要求。
  
    
    

### 在访问任何值属性之前调用 ClientContext.ExecuteQuery

SharePoint .NET Framework CSOM 要求您使用类似 SQL 的编程模式：声明您所需的内容并在访问数据之前执行查询。例如，以下代码尝试显示 SharePoint 网站的标题，这将引发异常。
  
    
    
```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  
```

此代码失败，因为 SharePoint .NET Framework CSOM 代码必须：
  
    
    

1. 生成临时 SQL 查询或存储过程。
    
  
2. 执行 SQL 查询。
    
  
3. 读取 SQL 的结果。
    
  
在 SharePoint .NET Framework CSOM 中，当您调用方法时，将生成查询。查询将累积在一起，但不会发送到服务器，直至调用  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) 。以下示例显示的是显示网站的标题所需的代码。您还需要为 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。此外，为 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 **using** 语句添加一个别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   
```

区别是以下这额外的两行：
  
    
    


```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
```

第一行创建对 Web 的 **Title** 属性的查询。第二行执行此查询。
  
    
    

### 请勿使用同一查询中的方法或属性返回的值对象

当方法或属性返回值对象时，不能使用此对象，直至执行查询之后。例如，以下代码尝试创建标题与父网站相同的 SharePoint 列表，但这将引发异常。
  
    
    
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

由于属性在执行查询之前不可用，因此将引发异常。在 SQL 中，将声明本地变量以包含  `web.Title` 的值并使用本地变量进行 Web 创建。在客户端库中，不能创建本地变量。您必须将功能拆分为下面示例中显示的两个单独的查询。您将需要为 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。此外，为 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 using 语句添加别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    


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

区别为下列三行：
  
    
    


```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 
```


### 在同一个查询的其他方法调用中使用返回客户端对象的方法或属性

与值对象不同，客户端对象可在相同查询的其他方法调用中使用。
  
    
    
在 .NET 远程处理中，值对象是值进行封送处理的类或结构，而客户端对象是引用进行封送处理的类或结构。例如， **ListItem** 是客户端对象，而 **UrlFieldValue** 和其他域值是值对象。
  
    
    
在客户端库中，对应的服务器对象具有  `[ClientCallable(ValueObject = true)]` 属性。这些值只能具有属性，不能包含方法。原始类型（如字符串和 int）被视为值对象。所有值在客户端和服务器之间进行封送处理。 **ValueObject** 的默认值为 **false**。
  
    
    
值对象对应客户端对象。如果对应服务器对象具有 **[ClientCallable(ValueObject = false)]** 属性，则此对象为客户端对象。对于客户端对象，我们保持对对象创建方式的跟踪；这在客户端库实现中称为 **ObjectPath**。例如，如果我们的代码类似于以下内容：
  
    
    


```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 
```

我们知道列表是通过下列方式创建的：
  
    
    

- 从上下文中获取 **Web** 属性。
    
  
- 从上面的结果中获取 **Lists** 属性。
    
  
- 使用上面结果中的  _Announcements_ 参数调用 **GetByTitle** 方法。
    
  
当 SharePoint .NET Framework CSOM 将此信息传递到服务器时，您可在服务器上重新创建对象。在客户端库中，可保持对创建客户端对象的 **ObjectPath** 的跟踪。因为您知道对象是如何创建的，因此可将对象用作参数来调用同一查询中的其他方法。
  
    
    

### 将同一对象上的数据检索分组在一起以改进性能

读取同一对象中的数据的多个部分时，您应尝试获取单个查询的所有内容；即，对 **Load<T>(T, [])** 方法的单个调用。以下代码演示用于检索网站标题和说明以及"通知"列表的说明的两种方式。若要编译此代码，您需要为 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。此外，为 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 using **statement** 添加别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    
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

效果不同。在 **Method1** 中，检索 Web 标题和说明的代码将分组在一起。在 **Method2** 中，检索 Web 标题和说明的代码将通过其他操作分开。这意味着， **Method2** 将触发对同一 Web 对象的两个不同的查询，并且同一 Web 将有两个结果集。由于客户端库尝试返回一致性数据，因此第二个结果集将包含标题和说明。您可将之前的代码视为以下代码。
  
    
    


```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 
```


### 指定要返回的对象的属性

在 SharePoint Server 对象模型中，如果获取 **SPWeb** 对象，则可检查其所有属性。在 SQL 中，若要获取表的所有列，您可运行：
  
    
    
```

SELECT * FROM Webs ```

在客户端库中， **Load<T>** 或任何其他方法都不会返回所有属性，因此您必须显式指定所需内容。例如，以下代码无需指定要返回的属性，将检索网站对象。之后它将尝试读取两个属性，其中一个属性不是 **Load** 自动返回的属性中的属性。此代码将引发异常。
  
    
    


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

若要成功获取要编译的代码，请将其更新为以下内容。若要编译此代码，需要为  [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。此外，为 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 **using** 语句添加别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    


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


### 使用条件范围在加载数据前测试先决条件

若要有条件地执行代码，请使用  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) 对象设置条件范围。例如，在列表不为 null 时检索列表属性。您还需要为 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 和 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 添加 **using** 语句。此外，为 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 命名空间的 **using** 语句添加别名，以便您可清楚地引用其类。例如， `using SP = Microsoft.SharePoint.Client;`。
  
    
    

> **注释**
> 不允许在条件范围内调用方法和设置属性，因为客户端库不会跟踪方法调用和属性设置的副作用。您只应使用条件范围内的 **Load**。 
  
    
    

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


### 使用异常处理范围捕获异常

此示例演示如何创建异常处理范围以及如何将其用于  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) 对象。此方案将更新列表的说明，还将启用文件夹创建。还有一种可能是列表不存在。
  
    
    
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


## 其他资源
<a name="bk_addresources"> </a>


-  [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)
    
  
-  [为 SharePoint 构建网站](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

