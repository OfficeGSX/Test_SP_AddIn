---
title: Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013
Узнайте, как создавать код для выполнения основных операций с клиентской объектной моделью SharePoint 2013 .NET Framework (CSOM).
## Клиентские интерфейсы API SharePoint
<a name="ClientAPIs"> </a>

С помощью клиентской объектной модели SharePoint (CSOM) вы можете получать и изменять данные в SharePoint 2013, а также управлять ими. В SharePoint 2013 доступ к CSOM осуществляется несколькими путями:
  
    
    

- распространяемые сборки .NET Framework;
    
  
- библиотека JavaScript;
    
  
- конечные точки REST и OData;
    
  
- Сборки Windows Phone
    
  
- распространяемые сборки Silverlight.
    
  
Подробнее о наборах API, доступных на платформе SharePoint 2013, см. в разделе  [Выбор правильного набора API в SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
В этой статье описывается выполнение базовых операций с помощью объектной модели .NET Framework, доступной в Центре загрузки Майкрософт в качестве распространяемого пакета. Найти его можно по поисковым фразам "пакет SDK для клиентских компонентов SharePoint Server 2013" или "пакет SDK для клиентских компонентов SharePoint Online". Сведения об использовании других клиентских интерфейсов API см. в статьях  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md),  [Построение приложений Windows Phone, обращающихся к SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx) и [Использование объектной модели Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx) в Пакет SDK для SharePoint 2010.
  
    
    

## Основные операции с клиентской объектной моделью .NET в SharePoint
<a name="BasicOps_SPCSOMOps"> </a>

В следующих разделах описываются задачи, которые вы можете выполнять программным путем. Они включают примеры кода на языке C#, в которых демонстрируются операции CSOM.
  
    
    
Когда вы создаете проект **надстройки для SharePoint 2013** в Visual Studio 2012, в него автоматически добавляются ссылки на сборки .NET Framework, **Microsoft.SharePoint.Client.Runtime.dll** и **Microsoft.SharePoint.Client.dll**. Для других типов проектов, например приложений .NET Framework или консольных приложений, эти ссылки необходимо добавлять вручную. Файлы находятся на любом сервере SharePoint 2013 в папке %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI.
  
    
    
Во всех этих примерах предполагается, что код содержится в файле кода программной части для веб-страницы Microsoft ASP.NET. Вам нужно добавить в файл кода следующую инструкцию **using**.
  
    
    


```

using Microsoft.SharePoint.Client;```

Если не указано иное, предполагается, что каждый из этих примеров содержится в методе без параметров, определенном в классе страницы. Кроме того,  `label1`,  `label2` и так далее  это имена объектов [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) на странице.
  
    
    

> **Примечание**
> При создании размещаемого у поставщика Надстройка SharePoint с помощью веб-приложения ASP.NET и добавлении ссылки на сборку проекта веб-приложения в Visual Studio присвойте свойству сборки **Копировать локально** значение **True**. Это не относится к случаям, когда сборка уже установлена на веб-сервере или вы знаете, что она установлена до того, как была развернута надстройка. Платформа .NET Framework устанавливается в веб-ролях Microsoft Azure и Веб-сайты Azure, но не устанавливаются сборки клиента SharePoint 2013 и различные расширения управляемого кода и платформы Microsoft. Инструменты разработчика Office для Visual Studio 2012 автоматически добавляет ссылки на некоторые сборки, часто используемые в надстройках SharePoint, и задает свойство **Копировать локально**. 
  
    
    


## Задачи, связанные с веб-сайтами SharePoint
<a name="BasicOps_SPWebTasks"> </a>

В этих примерах показано, как использовать модель CSOM .NET Framework для выполнения задач, связанных с веб-сайтами.
  
    
    

### Получение свойств веб-сайта

Следующий код служит для получения названия веб-сайта SharePoint.
  
    
    
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


### Получение определенных свойств веб-сайта

Иногда клиенту требуются только некоторые свойства объекта. Модель CSOM .NET Framework в SharePoint позволяет получать только некоторые свойства объекта на сервере. Вы можете использовать анонимные методы, которые могут представлять собой лямбда-выражения, для запроса свойств с определенными именами. Клиентская библиотека запросит с сервера только эти свойства, и сервер отправит только их клиенту. Таким образом можно избежать передачи ненужных данных между клиентом и сервером. Это также полезно в том случае, если у пользователя нет разрешения на доступ к одному или нескольким другим ненужным свойствам объекта. Обратите внимание на то, что вам потребуется добавить инструкцию **using** для пространства имен [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    
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


> **Примечание**
> Если вы попытаетесь обратиться к другим свойствам, код создаст исключение, поскольку они недоступны. 
  
    
    


### Запись значений для свойств веб-сайта

В этом примере показано, как записать значения для свойств веб-сайта.
  
    
    
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


### Создание веб-сайта SharePoint

В этом примере показано, как создать дочерний сайт SharePoint текущего веб-сайта. Для создания веб-сайта используйте класс  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) . Вам также потребуется добавить инструкции **using** для пространств имен [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) и [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) .
  
    
    
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


## Задачи, связанные со списками SharePoint
<a name="BasicOps_SPListTasks"> </a>

В этих примерах показано, как использовать модель CSOM .NET Framework для выполнения задач, связанных со списками.
  
    
    

### Получение всех списков SharePoint на сайте

В этом примере извлекаются все списки SharePoint, имеющиеся на веб-сайте SharePoint. Для выполнения этого кода вам потребуется добавить инструкцию **using** для пространства имен [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    
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


> **Примечание**
> Кроме того, вы можете сохранить возвращенное значение в другой коллекции с помощью метода **LoadQuery**, вместо того чтобы использовать свойство **web.Lists**. Вам также потребуется добавить инструкции **using** для пространств имен [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) и [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Помимо этого, добавьте псевдоним в инструкцию using для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`. 
  
    
    

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


### Создание и обновление списка SharePoint

В этом примере список SharePoint создается и обновляется с помощью класса  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) .
  
    
    
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


### Удаление списка SharePoint

В этом примере удаляется список SharePoint.
  
    
    
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


### Добавление поля в список SharePoint

В этом примере в список SharePoint добавляется поле. Добавьте псевдоним в инструкцию using для пространства имен  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **Примечание**
> В этом примере для приведения типа используется метод **context.CastTo**. Перед выполнением запроса клиентская библиотека не знает тип возвращаемого объекта, и единственным возможным типом является **SharePoint.Field**. Если тип объекта известен, вы можете воспользоваться методом **ClientContext.CastTo<RealType>**, чтобы привести его. 
  
    
    

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


## Задачи, связанные с элементами списков SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

В этих примерах демонстрируется использование модели CSOM .NET Framework для выполнения задач, связанных с элементами списков.
  
    
    

### Получение элементов из списка SharePoint

В этом примере извлекаются элементы из списка SharePoint. Вам также потребуется добавить инструкцию **using** для пространства имен [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) .
  
    
    

> **Примечание**
> С помощью свойства  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) вы можете ограничить возвращаемые элементы только теми, которые находятся в указанной папке.
  
    
    

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


### Создание элемента списка

В этом примере создается элемент списка SharePoint с помощью класса  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) .
  
    
    
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


### Обновление элемента списка

В этом примере обновляется элемент списка SharePoint.
  
    
    
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


### Удаление элемента списка

В этом примере удаляется элемент списка SharePoint.
  
    
    
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


## Задачи, связанные с полями SharePoint
<a name="BasicOps_SPFieldTasks"> </a>

В этих примерах показано, как использовать модель CSOM .NET Framework в SharePoint для выполнения задач, связанных с полями.
  
    
    

### Получение всех полей в списке

В этом примере извлекаются все поля, имеющиеся в списке SharePoint. Вам также потребуется добавить псевдоним в инструкцию **using** для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    
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


### Получение определенного поля из списка

Если вы хотите получить сведения об определенном поле, воспользуйтесь методом **Fields.GetByInternalNameOrTitle**. Он возвращает объект типа **Field**. Перед выполнением запроса клиент не знает тип объекта, а в C# нет синтаксической конструкции для приведения его к производному типу. Поэтому для его приведения используйте метод **ClientContext.CastTo**, который указывает клиентской библиотеке на то, что объект нужно создать повторно. Вам также потребуется добавить инструкцию **using** для пространства имен [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) . Кроме того, вам нужно добавить псевдоним в инструкцию **using** для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **Примечание**
> Метод  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) , используемый в этом примере, является удаленным. Он не использует данные из клиентской коллекции, даже если она уже заполнена.
  
    
    

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


## Задачи, связанные с пользователями SharePoint
<a name="BasicOps_SPSecurityTasks"> </a>

Вы можете использовать модель CSOM .NET Framework в SharePoint для управления пользователями и группами SharePoint, а также безопасностью пользователей.
  
    
    

### Добавление пользователя в группу SharePoint

В этом примере в группу SharePoint с именем Members добавляется пользователь и некоторые сведения о нем.
  
    
    
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


### Получение всех пользователей из группы SharePoint

В этом примере извлекаются сведения о всех пользователях в группе SharePoint с именем Members.
  
    
    
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


### Создание роли

В этом примере создается роль, имеющая разрешения на создание оповещений и управление ими.
  
    
    
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


### Добавление пользователя к роли

В этом примере к роли добавляется пользователь.
  
    
    
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


## Правила и рекомендации по клиентской объектной модели .NET в SharePoint
<a name="Best"> </a>

В этих примерах демонстрируются некоторые важные рекомендации и требования, которые следует соблюдать при использовании модели CSOM .NET Framework в SharePoint.
  
    
    

### Вызов метода ClientContext.ExecuteQuery перед доступом к свойствам значения

Модель CSOM .NET Framework в SharePoint требует использования шаблона программирования наподобие SQL: необходимо объявить запрос и выполнить его перед доступом к данным. Например, следующий код, в котором выполняется попытка отобразить название веб-сайта SharePoint, создаст исключение.
  
    
    
```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  
```

Сбой произойдет по той причине, что в коде CSOM .NET Framework в SharePoint необходимо выполнить следующие действия:
  
    
    

1. создать прямой запрос SQL или хранимую процедуру;
    
  
2. выполнить запрос SQL;
    
  
3. прочитать результаты из SQL.
    
  
В модели CSOM .NET Framework в SharePoint при вызове метода вы создаете запрос. Запросы накапливаются и не отправляются на сервер, пока не будет вызван метод  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) . В следующем примере показан код, необходимый для отображения названия веб-сайта. Вам также потребуется добавить инструкцию **using** для пространства имен [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Кроме того, добавьте псевдоним в инструкцию **using** для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    


```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   
```

Отличие в том, что добавлены следующие строки.
  
    
    


```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
```

В первой строке создается запрос для свойства **Title** сайта. Во второй строке выполняется запрос.
  
    
    

### Не используйте объекты значений, возвращенные методами или свойствами, в том же запросе

Когда метод или свойство возвращает объект значения, вы не можете использовать этот объект, пока запрос не будет выполнен. Например, в следующем коде выполняется попытка создать список SharePoint с тем же названием, что и у родительского веб-сайта, однако создается исключение.
  
    
    
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

Исключение создается по той причине, что свойство недоступно, пока запрос не будет выполнен. В SQL можно объявить локальную переменную для хранения значения  `web.Title` и использовать ее для создания сайта. В клиентской библиотеке создать локальную переменную нельзя. Вам придется реализовать эту функцию в двух отдельных запросах, как показано в следующем примере. Вам также потребуется добавить инструкцию **using** для пространства имен [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Кроме того, добавьте псевдоним в инструкцию using для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    


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

Отличие заключается в следующих трех строках.
  
    
    


```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 
```


### Использование методов или свойств, возвращающих клиентские объекты, в вызове другого метода в том же запросе

В отличие от объекта значения, клиентский объект можно использовать в вызове другого метода в том же запросе.
  
    
    
В удаленной среде .NET объект значения представляет собой класс или структуру, маршалируемую по значению, а клиентский объект  класс или структуру, маршалируемую по ссылке. Например, **ListItem**  это клиентский объект, а **UrlFieldValue** и другие значения полей  это объекты значений.
  
    
    
В клиентской библиотеке соответствующий объект сервера имеет атрибут  `[ClientCallable(ValueObject = true)]`. Эти значения могут иметь только свойства, но не иметь методов. Примитивные типы, такие как строки и целочисленные значения, обрабатываются как объекты значений. Все значения маршалируются между клиентом и сервером. Значение **ValueObject** по умолчанию  **false**.
  
    
    
Альтернативой объекту значения является клиентский объект. Если соответствующий серверный объект имеет атрибут **[ClientCallable(ValueObject = false)]**, объект является клиентским. Процесс создания клиентских объектов отслеживается. В реализации клиентской библиотеки для этого используется класс **ObjectPath**. Например, имеется следующий код.
  
    
    


```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 
```

Мы знаем, что список создается следующим образом.
  
    
    

- Свойство **Web** извлекается из контекста.
    
  
- Из полученного выше свойства извлекается свойство **Lists**.
    
  
- Для полученного выше свойства вызывается метод **GetByTitle** с параметром _Announcements_.
    
  
Когда модель CSOM .NET Framework в SharePoint передает эти сведения на сервер, вы можете повторно создать объект на нем. В клиентской библиотеке вы можете отслеживать процесс создания клиентского объекта с помощью **ObjectPath**. Так как вы знаете, как был создан объект, вы можете использовать его как параметр для вызова других методов в том же запросе.
  
    
    

### Получение групповых данных из одного объекта для повышения производительности

Если необходимо получить различные данные из одного объекта, следует попытаться сделать это в одном запросе, то есть использовать один вызов метода **Load<T>(T, [])**. В следующем коде показаны два способа получения названия и описания веб-сайта, а также описания списка **Announcements**. Чтобы скомпилировать этот код, необходимо добавить инструкцию **using** для пространства имен [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Кроме того, добавьте псевдоним в инструкцию using для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    
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

Эти способы различаются по эффективности. В методе **Method1** код для извлечения названия и описания веб-сайта сгруппирован вместе. В методе **Method2** он разделен другими действиями. Это означает, что метод **Method2** вызывает два отдельных запроса к одному и тому же объекту сайта, и для него будет получено два набора результатов. Поскольку клиентская библиотека стремится возвращать согласованные данные, второй набор результатов будет включать как название, так и описание. Предыдущий код можно представить следующим образом.
  
    
    


```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 
```


### Указание свойств объектов, которые необходимо вернуть

В серверной объектной модели SharePoint при получении объекта **SPWeb** вы можете просмотреть все его свойства. В SQL для получения всех столбцов таблицы можно выполнить следующий запрос.
  
    
    
```

SELECT * FROM Webs ```

В клиентской библиотеке ни метод **Load<T>**, ни какой-либо другой метод не возвращает всех свойств, поэтому необходимо явным образом указать требуемые данные. Например, следующий код получает объект веб-сайта, но возвращаемые свойства не указаны. Далее он пытается прочесть два свойства, однако одно из них не относится с свойствам, которые возвращаются методом **Load** автоматически. Создается исключение.
  
    
    


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

Чтобы успешно скомпилировать код, обновите его следующим образом. Для компиляции кода необходимо добавить инструкцию **using** для пространства имен [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Кроме того, добавьте псевдоним в инструкцию **using** для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    


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


### Использование условной области для проверки предварительных условий перед загрузкой данных

Для выполнения кода по условию задайте условную область с помощью объекта  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) . Например, так можно извлечь свойство списка, если список не равен null. Вам также потребуется добавить инструкции **using** для пространств имен [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) и [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Кроме этого, добавьте псевдоним в инструкцию **using** для пространства имен [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , чтобы получить возможность ссылаться на его классы однозначно. Пример: `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> **Примечание**
> Вызывать методы и задавать свойства внутри условной области запрещено, так как клиентская библиотека не отслеживает побочные эффекты этих действий. Внутри условной области следует использовать только метод **Load**. 
  
    
    

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


### Использование области обработки исключений для перехвата исключений

В этом примере показано, как создать и использовать область обработки исключений с помощью объекта  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) . Код должен изменить описание списка и создать папку. Существует вероятность, что список не существует.
  
    
    
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


## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Разработка надстроек для SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Создание сайтов для SharePoint](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

