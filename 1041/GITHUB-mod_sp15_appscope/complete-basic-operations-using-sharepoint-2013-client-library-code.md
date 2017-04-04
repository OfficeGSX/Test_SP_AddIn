---
title: SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する
基本的な操作を実行するコードを SharePoint 2013 .NET Framework クライアント オブジェクト モデル (CSOM) を使用して記述する方法について説明します。
## SharePoint クライアント API
<a name="ClientAPIs"> </a>

SharePoint クライアント オブジェクト モデル (CSOM) を使用して、SharePoint 2013でデータを取得、更新、および管理できます。SharePoint 2013では、さまざまな形で CSOM を使用できます。
  
    
    

- .NET Framework 再頒布可能アセンブリ
    
  
- JavaScript ライブラリ
    
  
- REST/OData エンドポイント
    
  
- Windows Phone アセンブリ
    
  
- Silverlight 再頒布可能アセンブリ
    
  
SharePoint 2013 プラットフォームで利用できる API セットの詳細については、「 [SharePoint 2013 での適切な API セットの選択](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)」を参照してください。
  
    
    
この記事では, .NET Framework オブジェクト モデルを使用して基本的な操作を実行する方法について説明します。これは、Microsoft ダウンロード センターから再頒布可能パッケージとして入手できます。「SharePoint Server 2013 Client Components SDK」または「SharePoint Online Client Components SDK」を検索してください。他のクライアント API の使用方法の詳細については、SharePoint 2010 SDK の「 [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)」、「 [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)」、「 [SharePoint 2013 にアクセスする Windows Phone アプリの作成](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)」、および「 [Silverlight オブジェクト モデルを使用する](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx)」を参照してください。
  
    
    

## SharePoint .NET クライアント オブジェクト モデルを使用した基本的な操作
<a name="BasicOps_SPCSOMOps"> </a>

以降のセクションで、プログラムで完了できるタスクについて説明します。説明には、CSOM の操作を例示する C# コード例が含まれます。
  
    
    
Visual Studio 2012 で **SharePoint 2013 用アドイン** プロジェクトを作成すると, .NET Framework アセンブリ、 **Microsoft.SharePoint.Client.Runtime.dll**、および **Microsoft.SharePoint.Client.dll**への参照がプロジェクトに自動的に追加されます。他の種類のプロジェクト (.NET Framework アプリケーションやコンソール アプリケーションなど) に対しては、これらの参照を追加する必要があります。これらのファイルは、すべての SharePoint 2013 サーバーで %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI に配置されます。
  
    
    
すべての例では、Microsoft ASP.NET Web ページ用の分離ファイルにコードが保存されていることを前提としています。次の **using** ステートメントをコード ファイルに追加する必要があります。
  
    
    



```

using Microsoft.SharePoint.Client;
```

特に明記しない限り、これらの例は、ページのクラスに定義されるパラメーターなしメソッド内に記述するものとします。また、 `label1`、 `label2` などは、ページの [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) オブジェクトの名前です。
  
    
    

> [!メモ]
> ASP.NET Web アプリケーションでプロバイダー向けのホスト型 SharePoint アドインを作成しており、Visual Studio で Web アプリケーション プロジェクトにアセンブリへの参照を追加する場合、アセンブリが既に Web サーバーにインストールされていることがわかっているのでない限り、アセンブリの [ **Copy Local**] プロパティを [ **True**] に設定します。あるいは、アドインを展開する前に確実にインストールされるようにすることもできます。.NET Framework は Microsoft Azure Web ロールおよび Azure Web サイト にインストールされます。ただし、SharePoint 2013 クライアント アセンブリと各種の Microsoft マネージ コード拡張機能および基盤はインストールされません。Office Developer Tools for Visual Studio 2012 は、SharePoint アドインでよく使われる一部のアセンブリへの参照を自動的に追加し、[ **Copy Local**] プロパティを設定します。 
  
    
    


## SharePoint Web サイトのタスク
<a name="BasicOps_SPWebTasks"> </a>

以下の例は, .NET Framework CSOM を使用して Web サイトに関連するタスクを完了する方法を示しています。
  
    
    

### Web サイトのプロパティを取得する

SharePoint Web サイトのタイトルを取得します。
  
    
    

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


### Web サイトの選択したプロパティのみを取得する

クライアントの関心が、オブジェクトの一部のプロパティのみにある場合があります。SharePoint .NET Framework CSOM では、サーバー上のオブジェクトからすべてのプロパティを取得する必要はありません。ラムダ式を使用できる匿名メソッドを使用して、特定のプロパティ名を要求できます。クライアント ライブラリは、サーバーで要求されたプロパティのみのクエリを実行し、サーバーは要求されたプロパティのみをクライアントに送信します。このテクニックによって、クライアントとサーバー間の不要なデータ転送が減少します。また、オブジェクトのその他の未使用プロパティの 1 つ以上に対するアクセス許可がユーザーにない場合にも有用です。 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントを追加する必要があることに注意してください。
  
    
    

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


> [!メモ]
> 他のプロパティへのアクセスを試行した場合、他のプロパティは使用できないので、このコードは例外をスローします。 
  
    
    


### Web サイトのプロパティに書き込む

次の例は、Web サイトのプロパティに書き込む方法を示しています。
  
    
    

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


### 新しい SharePoint Web サイトを作成する

次の例は、新しい SharePoint サイトを、現在の Web サイトのサブサイトとして作成する方法を示しています。新しい Web サイトを作成するには、 [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) クラスを使用します。また、 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) および [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) 用の **using** ステートメントを追加する必要があります。
  
    
    

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


## SharePoint リスト タスク
<a name="BasicOps_SPListTasks"> </a>

以下の例は, .NET Framework CSOM を使用して、リストに関連するタスクを完了する方法を示しています。
  
    
    

### Web 内のすべての SharePoint リストを取得する

次の例では、SharePoint Web サイト内のすべての SharePoint リストを取得します。このコードをコンパイルするには、 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントを追加する必要があります。
  
    
    

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


> [!メモ]
> **web.Lists** プロパティではなく **LoadQuery** メソッドを使用して、戻り値を別のコレクションに格納することもできます。また、 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) および [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントを追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の using ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    


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


### SharePoint リストを作成および更新する

次の例では、 [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) クラスを使用して、SharePoint リストの作成と更新を行います。
  
    
    

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


### SharePoint リストを削除する

次の例では、SharePoint リストを削除します。
  
    
    

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


### SharePoint リストにフィールドを追加する

次の例では、SharePoint リストにフィールドを追加し、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 用の using ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    

> [!メモ]
> この例では、 **context.CastTo** を使用してキャストを行います。クライアント ライブラリは、クエリの実行前は、返されるオブジェクト "フィールド" の実際の型がわからないため、 **SharePoint.Field** が唯一の可能な型になります。実際の型がわかっている場合は、 **ClientContext.CastTo<RealType>** メソッドを使用してオブジェクトをキャストできます。
  
    
    


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


## SharePoint リスト アイテム タスク
<a name="BasicOps_SPListItemTasks"> </a>

以下の例は, .NET Framework CSOM を使用して、リスト アイテムに関連するタスクを完了する方法を示しています。
  
    
    

### SharePoint リストからアイテムを取得する

次の例では、SharePoint リスト内のアイテムを取得します。 [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) 用の **using** ステートメントも追加する必要があります。
  
    
    

> [!メモ]
>  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) プロパティを使用して、特定のフォルダーに返されるアイテムを制限できます。
  
    
    


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


### 新しいリスト アイテムを作成する

次の例では、 [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) クラスを使用して新しい SharePoint リスト アイテムを作成します。
  
    
    

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


### リスト アイテムを更新する

次の例では、SharePoint リスト アイテムを更新します。
  
    
    

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


### リスト アイテムを削除する

次の例では、SharePoint リスト アイテムを削除します。
  
    
    

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


## SharePoint フィールド タスク
<a name="BasicOps_SPFieldTasks"> </a>

以下の例は、SharePoint .NET Framework CSOM を使用して、フィールドに関連するタスクを完了する方法を示しています。
  
    
    

### リスト内のすべてのフィールドを取得する

次の例では、SharePoint リスト内のすべてのフィールドを取得します。 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の **using** ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにする必要があります。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    

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


### リストから特定のフィールドを取得する

特定のフィールドに関する情報を取得する場合は、 **Fields.GetByInternalNameOrTitle** メソッドを使用します。このメソッドの戻り値の型は **Field** です。クライアントは、クエリの実行前は返されるオブジェクトの型がわからないため、それを派生型にキャストするための C# 構文は使用できません。したがって、 **ClientContext.CastTo** メソッドを使用してキャストを行い、クライアント ライブラリにオブジェクトを再作成するように命令します。 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) 用の **using** ステートメントも追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の **using** ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにする必要があります。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    

> [!メモ]
> この例で使用されている  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) メソッドはリモート メソッドです。クライアント コレクションが既に入力されている場合でも、クライアント コレクションのデータを使用することはありません。
  
    
    


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


## SharePoint ユーザー タスク
<a name="BasicOps_SPSecurityTasks"> </a>

SharePoint .NET Framework CSOM を使用して、SharePoint ユーザー、グループ、およびユーザー セキュリティを管理できます。
  
    
    

### SharePoint グループにユーザーを追加する

次の例では、Members という名前の SharePoint グループに、ユーザーとユーザー情報を追加します。
  
    
    

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


### SharePoint グループ内のすべてのユーザーを取得する

次の例では、Members という名前の SharePoint グループからすべてのユーザーに関する情報を取得します。
  
    
    

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


### ロールを作成する

次の例では、アラートのアクセス許可を作成して管理するロールを作成します。
  
    
    

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


### ロールにユーザーを追加する

次の例では、ロールにユーザーを追加します。
  
    
    

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


## SharePoint .NET クライアント オブジェクト モデルを使用するための規則とベスト プラクティス
<a name="Best"> </a>

以下の例は、SharePoint .NET Framework CSOM を使用する際に従う必要がある重要なベスト プラクティスと要件を示しています。
  
    
    

### 値のプロパティにアクセスする前に ClientContext.ExecuteQuery を呼び出す

SharePoint .NET Framework CSOM では、SQL に似たプログラミング パターン (データにアクセスする前に、必要なものを宣言し、クエリを実行する) を使用する必要があります。たとえば、SharePoint Web サイトのタイトルの表示を試行する次のコードは、例外をスローします。
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  

```

このコードは失敗するのは、SharePoint .NET Framework CSOM コードが次の要件を満たしていないためです。
  
    
    

1. アドホック SQL クエリまたはストアド プロシージャをビルドする。
    
  
2. SQL クエリを実行する。
    
  
3. SQL から結果を読み込む。
    
  
SharePoint .NET Framework CSOM では、メソッドの呼び出し時にクエリがビルドされます。クエリは蓄積され、 [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) が呼び出されるまではサーバーに送信されません。次の例は、Web サイトのタイトルを表示するために必要なコードを示しています。 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントも追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の **using** ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   

```

違いは、以下の行の追加です。
  
    
    



```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 

```

1 行目は、Web の **Title** プロパティ用のクエリを作成します。2 行目は、クエリを実行します。
  
    
    

### 同じクエリ内のメソッドまたはプロパティから返された値オブジェクトは使用しない

メソッドまたはプロパティから値オブジェクトが返る場合は、クエリが実行されるまではそのオブジェクトを使用できません。たとえば、次のコードは、親 Web サイトと同じタイトルの SharePoint リストの作成を試行していますが、例外をスローします。
  
    
    

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

例外がスローされるのは、クエリの実行前は、プロパティを取得できないためです。SQL では、 `web.Title` の値を保持するためのローカル変数を宣言し、そのローカル変数を Web を作成するために使用します。クライアント ライブラリでは、ローカル変数は作成できません。次の例に示すように、機能を 2 つの独立したクエリに分割する必要があります。 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントも追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 用の using ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    



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

違いは、次の 3 行です。
  
    
    



```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 

```


### クライアント オブジェクトを返すメソッドまたはプロパティを、同じクエリ内の別のメソッド呼び出しで使用する

値オブジェクトとは異なり、クライアント オブジェクトは、同じクエリ内の別のメソッドの中で使用できます。
  
    
    
.NET リモート処理では、値オブジェクトは値によってマーシャリングされるクラスまたは構造体であり、クライアント オブジェクトは参照によってマーシャリングされるクラスまたは構造体です。たとえば、 **ListItem** はクライアント オブジェクトであり、 **UrlFieldValue** およびその他のフィールド値は値オブジェクトです。
  
    
    
クライアント ライブラリでは、対応するサーバー オブジェクトには  `[ClientCallable(ValueObject = true)]` 属性があります。これらの値にはプロパティのみがあり、メソッドがない可能性があります。strings や int などのプリミティブ型は、値オブジェクトとして処理されます。すべての値は、クライアントとサーバー間でマーシャリングされます。 **ValueObject** の既定値は **false** です。
  
    
    
値オブジェクトに対応するオブジェクトがクライアント オブジェクトです。対応するサーバー オブジェクトに **[ClientCallable(ValueObject = false)]** 属性がある場合、そのオブジェクトはクライアント オブジェクトです。クライアント オブジェクトの場合は、オブジェクトの作成方法が追跡されます。クライアント ライブラリの実装では、これを **ObjectPath** と呼びます。たとえば、次のようなコードがあるとします。
  
    
    



```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 

```

このリストは、以下の手順で作成されることがわかっています。
  
    
    

- コンテキストから **Web** プロパティを取得する。
    
  
- 上の結果から **Lists** プロパティを取得する。
    
  
- 上の結果から  _Announcements_ パラメーターを使用して **GetByTitle** メソッドを呼び出す。
    
  
SharePoint .NET Framework CSOM によってこの情報がサーバーに渡されたときに、サーバー上でオブジェクトを再作成できます。クライアント ライブラリでは、クライアント オブジェクトが作成される **ObjectPath** を追跡できます。オブジェクトの作成方法はわかっているので、オブジェクトをパラメーターとして使用して、同じクエリ内の別のメソッドを呼び出すことができます。
  
    
    

### 同じオブジェクトに関するデータの取得をグループ化してパフォーマンスを向上させる

同じオブジェクトから複数のデータを読み取る場合は、そのすべてを単一のクエリ ( **Load<T>(T, [])** メソッドを 1 回呼び出すこと) で取得するようにしてください。次のコードは、Web サイトのタイトルと説明、および **お知らせ**リストの説明を取得するための 2 つの方法を示しています。このコードをコンパイルするには、 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントを追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の using **statement** に対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    

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

これらの効率は同じではありません。 **Method1** では、Web サイトのタイトルと説明を取得するためのコードはグループ化されています。 **Method2** では、Web サイトのタイトルと説明を取得するためのコードは、他のアクションによって分離されています。つまり、 **Method2** では、同じ Web オブジェクトに関して 2 つの独立したクエリがトリガーされ、同じ Web に対する 2 つの結果セットが存在することになります。クライアント ライブラリは一貫性のあるデータを返そうとするため、2 つ目の結果セットにはタイトルと説明の両方が含まれます。前のコードは、次のように考えることができます。
  
    
    



```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 

```


### オブジェクトのどのプロパティを返すかを指定する

SharePoint サーバー オブジェクト モデルでは、 **SPWeb** オブジェクトを取得する場合は、そのプロパティをすべて検査できます。SQL では、テーブルのすべてのカラムを取得する場合は、次のコードを実行できます。
  
    
    

```

SELECT * FROM Webs 
```

クライアント ライブラリでは、 **Load<T>** も他のメソッドも、すべてのプロパティを返すことはないため、目的のプロパティを明示的に指定する必要があります。たとえば、次のコードでは、目的のプロパティの指定なしで、Web サイトのオブジェクトを取得しています。その後、2 つのプロパティを読み込もうとしていますが、そのうちの 1 つは **Load** によって自動的に返されるプロパティには含まれていません。このコードは例外をスローします。
  
    
    



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

このコードのコンパイルを成功させるには、コードを次のように更新します。このコードをコンパイルするには、 [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントを追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の **using** ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    



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


### データを読み込む前に条件範囲を使用して前提条件をテストする

コードを条件付きで実行するには、 [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) オブジェクトを使用して、条件範囲を設定します。たとえば、リストが null 以外の場合にリストのプロパティを取得します。 [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) および [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) 用の **using** ステートメントも追加する必要があります。さらに、 [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) 名前空間用の **using** ステートメントに対するエイリアスを追加して、そのクラスを明確に参照できるようにします。例: `using SP = Microsoft.SharePoint.Client;`
  
    
    

> [!メモ]
> 条件範囲内でメソッドの呼び出しとプロパティの設定を行うことは許可されていません。これは、クライアント ライブラリでは、メソッドの呼び出しとプロパティの設定の副次的影響を追跡しないためです。条件範囲の中では、 **Load** のみを使用する必要があります。
  
    
    


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


### 例外処理範囲を使用して例外をキャッチする

次の例は、 [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) オブジェクトを使用して例外処理範囲を作成および使用する方法を示します。このシナリオは、リストの説明を更新すると共に、フォルダーを作成できるようにすることです。リストは存在しない可能性があります。
  
    
    

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


## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint サイトの構築](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

