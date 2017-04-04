---
title: Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 5a69c9e3-73bf-4ed5-bc19-182056bdb394
---


# Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013
Aprenda a escribir código para ejecutar operaciones básicas con el modelo de objetos de cliente (CSOM) .NET Framework de SharePoint 2013.
## API de cliente de SharePoint
<a name="ClientAPIs"> </a>

Puede usar el modelo de objetos de cliente (CSOM) de SharePoint para recuperar, actualizar y administrar datos en SharePoint 2013. SharePoint 2013 hace que el CSOM esté disponible en varias formas.
  
    
    

- Ensamblados redistribuibles de .NET Framework
    
  
- Biblioteca de JavaScript
    
  
- Extremos REST/OData
    
  
- Ensamblados de Windows Phone
    
  
- Ensamblados redistribuibles de Silverlight
    
  
Para obtener más detalles sobre los conjuntos de API disponibles en la plataforma SharePoint 2013, vea  [Elegir el conjunto de API correcto en SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    
En este artículo se muestra cómo realizar operaciones básicas con el modelo de objetos de .NET Framework, que está disponible como un paquete redistribuible en el Centro de descarga de Microsoft. Busque "SDK de componentes de cliente de SharePoint Server 2013" o "SDK de componentes de cliente de SharePoint Online". Para obtener más información sobre cómo usar otras API de cliente, consulte  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md),  [Creación de aplicaciones de Windows Phone con acceso a SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx) y [Uso del modelo de objetos Silverlight](http://msdn.microsoft.com/library/cea7829d-f360-4052-8b76-91d90bcefd2a%28Office.15%29.aspx) en el SDK de SharePoint 2010.
  
    
    

## Operaciones básicas con el modelo de objetos de cliente .NET de SharePoint
<a name="BasicOps_SPCSOMOps"> </a>

Las secciones siguientes describen las tareas que puede completar mediante programación e incluyen ejemplos de código C# que demuestran las operaciones CSOM.
  
    
    
Al crear un proyecto de **Complemento para SharePoint 2013** en Visual Studio 2012, se agregan referencias a los ensamblados .NET Framework, **Microsoft.SharePoint.Client.Runtime.dll** y **Microsoft.SharePoint.Client.dll** automáticamente al proyecto. En otros tipos de proyectos, como aplicaciones .NET Framework o aplicaciones de consola, debe agregar estas referencias. Los archivos se encuentran en cualquier servidor de SharePoint 2013 en %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ISAPI.
  
    
    
En todos estos ejemplos se supone que el código está en un archivo de código subyacente para una página web Microsoft ASP.NET. Debe agregar la siguiente instrucción **using** al archivo de código.
  
    
    



```

using Microsoft.SharePoint.Client;
```

Excepto donde se especifique lo contrario, puede suponer que cada uno de estos ejemplos está en un método sin parámetros que se define en la clase de la página. Además,  `label1`,  `label2`, etc. son los nombres de objetos  [Label](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Label.aspx) en la página.
  
    
    

> [!NOTA]
> Cuando haga un Complemento de SharePoint hospedado por el proveedor con una aplicación web ASP.NET y agregue una referencia a un ensamblado en el proyecto de aplicación web en Visual Studio, establezca la propiedad **Copia local** del ensamblado en **True**, salvo que sepa que el ensamblado ya está instalado en el servidor web o pueda asegurarse de que se instale antes de implementar el complemento. La aplicación .NET Framework está instalada en los roles web de Microsoft Azure y los Sitios web de Azure. Pero no están instalados los ensamblados de cliente de SharePoint 2013 ni las distintas fundaciones ni extensiones de código administrado de Microsoft. Office Developer Tools para Visual Studio 2012 agrega automáticamente referencias a algunos ensamblados que se usan con frecuencia en complementos para SharePoint y configura la propiedad **Copia local**. 
  
    
    


## Tareas de sitio web de SharePoint
<a name="BasicOps_SPWebTasks"> </a>

Estos ejemplos muestran cómo usar el CSOM de .NET Framework para completar las tareas relacionadas con el sitio web.
  
    
    

### Recuperar las propiedades de un sitio web

Recuperar el título de un sitio web de SharePoint
  
    
    

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


### Recuperar solo las propiedades seleccionadas de un sitio web

A veces, el cliente solo está interesado en algunas propiedades de un objeto. El CSOM .NET Framework de SharePoint no necesita que obtenga todas las propiedades del objeto de un servidor. Puede usar métodos anónimos, que pueden ser expresiones lambda, para solicitar específicamente los nombres de propiedades. La biblioteca de cliente solo consultará esas propiedades del servidor y el servidor solo enviará las propiedades al cliente. Esta técnica reduce la transferencia de datos innecesarios entre el cliente y el servidor. También es útil cuando el usuario no tiene permiso para uno o más de las otras propiedades sin usar en un objeto. Tenga en cuenta que necesitará agregar una instrucción **using** a [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    

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


> [!NOTA]
> Si trata de obtener acceso a otras propiedades, el código produce una excepción porque las otras propiedades no están disponibles. 
  
    
    


### Escribir en las propiedades de un sitio web

En este ejemplo se muestra cómo escribir en las propiedades de un sitio web.
  
    
    

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


### Crear un sitio web de SharePoint

En este ejemplo se muestra cómo crear un sitio de SharePoint como un subsitio del sitio web actual. Use la clase  [WebCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.WebCreationInformation.aspx) para crear un sitio web. También tendrá que agregar las instrucciones **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) y [System.Text](https://msdn.microsoft.com/library/System.Text.aspx) .
  
    
    

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


## Tareas de lista de SharePoint
<a name="BasicOps_SPListTasks"> </a>

Estos ejemplos muestran cómo usar el CSOM de .NET Framework para completar las tareas relacionadas con listas.
  
    
    

### Recuperar todas las listas de SharePoint en una web

En este ejemplo se recuperan todas las listas de SharePoint en un sitio de SharePoint. Para compilar este código tendrá que agregar una instrucción **using** para [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) .
  
    
    

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


> [!NOTA]
> Como alternativa, puede usar el método **LoadQuery**para almacenar el valor devuelto en otra colección, en vez de utilizar la propiedad **web.Lists**. También tendrá que agregar las instrucciones **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) y [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Además, agregue un alias a la instrucción que se usa para el espacio de nombres de [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`. 
  
    
    


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


### Crear y actualizar una lista de SharePoint

En este ejemplo se crea una lista de SharePoint y se actualiza con la clase  [ListCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListCreationInformation.aspx) .
  
    
    

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


### Eliminar una lista de SharePoint

En este ejemplo se elimina una lista de SharePoint.
  
    
    

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


### Agregar un campo a una lista de SharePoint

En este ejemplo se agrega un campo a una lista de SharePoint. Agregue un alias a la instrucción que se usa para el espacio de nombres  [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> [!NOTA]
> El ejemplo usa **context.CastTo** para la conversión. Antes de ejecutar la consulta, la biblioteca de cliente no conoce el tipo real del objeto devuelto "campo" y **SharePoint.Field** es el único tipo posible. Si conoce el tipo real, puede usar el método **ClientContext.CastTo<RealType>** para convertir el objeto.
  
    
    


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


## Tareas de elementos de lista de SharePoint
<a name="BasicOps_SPListItemTasks"> </a>

Estos ejemplos demuestran cómo usar el CSOM de .NET Framework para completar tareas relacionadas con elementos de lista.
  
    
    

### Recuperar de elementos de una lista de SharePoint

En este ejemplo se recuperan los elementos de una lista de SharePoint. También tendrá que agregar una instrucción **using** para [Microsoft.SharePoint.Client.QueryExpression](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.QueryExpression.aspx) .
  
    
    

> [!NOTA]
> Puede usar la propiedad  [FolderServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.CamlQuery.FolderServerRelativeUrl.aspx) para restringir aún más los elementos que se devuelven a los que se encuentran en una carpeta determinada.
  
    
    


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


### Crear un elemento de lista

En este ejemplo se crea un elemento de lista de SharePoint con  [ListItemCreationInformation](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ListItemCreationInformation.aspx) .
  
    
    

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


### Actualizar un elemento de lista

En este ejemplo se actualiza un elemento de lista de SharePoint.
  
    
    

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


### Eliminar un elemento de lista

En este ejemplo se elimina un elemento de lista de SharePoint.
  
    
    

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


## Tareas de campos de SharePoint
<a name="BasicOps_SPFieldTasks"> </a>

Estos ejemplos muestran cómo usar el CSOM de .NET Framework de SharePoint para completar las tareas relacionadas con campos.
  
    
    

### Recuperar todos los campos de una lista

En este ejemplo se recuperan los campos de una lista de SharePoint. Además, agregue un alias a la instrucción **using** para el espacio de nombres de [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

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


### Recuperar un campo específico de la lista

Si desea recuperar información sobre un campo específico, use el método **Fields.GetByInternalNameOrTitle**. El tipo devuelto por este método es **Field**. Antes de ejecutar la consulta, el cliente no conoce el tipo de objeto y la sintaxis de C# no está disponible para convertirlo al tipo derivado. Por lo tanto, use el método **ClientContext.CastTo** para convertirlo, que indica a la biblioteca de cliente que tiene que volver a crear recrear un objeto. También tendrá que agregar una instrucción **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) . Además tendrá que agregar un alias a la instrucción **using** para el espacio de nombres de [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> [!NOTA]
> El método  [GetByInternalNameOrTitle](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.FieldCollection.GetByInternalNameOrTitle.aspx) usado en este ejemplo es un método remoto. No utiliza los datos de la colección del cliente incluso si ya está llena.
  
    
    


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


## Tareas de usuarios de SharePoint
<a name="BasicOps_SPSecurityTasks"> </a>

Puede usar el CSOM de .NET Framework de SharePoint para administrar usuarios, grupos y seguridad de usuarios de SharePoint.
  
    
    

### Agregar un usuario a un grupo de SharePoint

En este ejemplo se agrega un usuario y cierta información de usuario a un grupo de SharePoint denominado Miembros.
  
    
    

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


### Recuperar todos los usuarios de un grupo de SharePoint

En este ejemplo se recupera información de todos los usuarios de un grupo de SharePoint denominado Miembros.
  
    
    

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


### Crear un rol

En este ejemplo se crea un rol que tiene que crear y administrar permisos de alertas.
  
    
    

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


### Agregar un usuario a un rol

En este ejemplo se agrega un usuario a un rol.
  
    
    

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


## Reglas y procedimientos recomendados para el modelo de objetos de cliente de .NET de SharePoint
<a name="Best"> </a>

Estos ejemplos ilustran algunos requisitos y procedimientos recomendados importantes que debe cumplir al usar el CSOM de .NET Framework de SharePoint.
  
    
    

### Llamar a ClientContext.ExecuteQuery antes de obtener acceso a cualquier propiedad de valor

El CSOM de .NET Framework de SharePoint necesita que use un patrón de programación similar a SQL: declarar lo que desea y ejecutar la consulta antes de obtener acceso a los datos. Por ejemplo, el siguiente código trata de mostrar el título del sitio de SharePoint, pero generará una excepción.
  
    
    

```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 
label1.Text = web.Title;  

```

Este código produce errores porque el código CSOM de .NET Framework de SharePoint debe:
  
    
    

1. Crear un procedimiento almacenado o una consulta SQL ad hoc.
    
  
2. Ejecutar la consulta SQL.
    
  
3. Leer resultados desde SQL.
    
  
En CSOM de .NET Framework de SharePoint, cuando llama a un método, genera una consulta. Las consultas se acumulan y no se envían al servidor hasta que se llama a  [ExecuteQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.ExecuteQuery.aspx) . En el ejemplo siguiente se muestra el código que es necesario para mostrar el título del sitio web. También tendrá que agregar una instrucción **using** para [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Además, agregue un alias a la instrucción **using** para el espacio de nombres de [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    



```cs

// Starting with ClientContext, the constructor requires a URL to the
// server running SharePoint. 
ClientContext context = new ClientContext("http://SiteUrl"); 

Web web = context.Web; 

context.Load(web, w => w.Title); 

context.ExecuteQuery(); 

label1.Text = web.Title;   

```

Las diferencias son la incorporación de estas líneas:
  
    
    



```

context.Load(web, w => w.Title);
context.ExecuteQuery(); 

```

La primera línea crea una consulta para la propiedad **Title** de la web. La segunda línea ejecuta la consulta.
  
    
    

### No usar objetos de valor devueltos por los métodos o propiedades en la misma consulta

Cuando se devuelve un objeto de valor de una propiedad o método, se puede usar que objeto hasta después de haber ejecutado la consulta. Por ejemplo, el siguiente código trata de crear una lista de SharePoint que tiene el mismo título que el sitio web primario, pero generará una excepción.
  
    
    

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

Se genera una excepción porque la propiedad no está disponible antes de ejecutar la consulta. En SQL, declararía una variable local para retener el valor de  `web.Title` y usar la variable local para la creación web. En la biblioteca de cliente, puede crear una variable local. Tiene que dividir las funciones en dos consultas independientes como se muestra en el ejemplo siguiente. También necesitará agregar una instrucción **using** para [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Además, agregue un alias a la instrucción que se usa para el espacio de nombres de [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    



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

La diferencia está en las tres líneas siguientes:
  
    
    



```cs

context.Load(web, w => w.Title);
context.ExecuteQuery(); 
...
context.ExecuteQuery(); 

```


### Usar métodos o propiedades que devuelven objetos de cliente en la llamada a otro método en la misma consulta

A diferencia de un objeto de valor, un objeto de cliente se puede usar en otra llamada de método en la misma consulta.
  
    
    
En.NET en remoto, el objeto de valor es una clase o estructura cuyas referencias se calculan por valor, mientras que un objeto de cliente es una clase o estructura cuyas referencias se calculan por referencia. Por ejemplo, **ListItem** es un objeto de cliente, mientras que **UrlFieldValue** y otros valores de campo son objetos de valor.
  
    
    
En la biblioteca de cliente, el objeto de servidor correspondiente tiene el atributo  `[ClientCallable(ValueObject = true)]`. Esos valores podrían tener solo propiedades y no métodos. Los tipos primitivos, como las cadenas y enteros, se tratan como objetos de valor. Todos los valores se calculan como referencias entre el cliente y el servidor. El valor predeterminado de **ValueObject** es **false**.
  
    
    
La contraparte del objeto de valor es el objeto de cliente. Si el objeto de servidor correspondiente tiene el atributo **[ClientCallable(ValueObject = false)]**, el objeto es un objeto de cliente. En objetos de cliente, se hace un seguimiento de cómo se crea el objeto; esto se denomina **ObjectPath** en la implementación de la biblioteca de cliente. Por ejemplo, si se tiene código como el siguiente:
  
    
    



```cs

ClientContext context = new ClientContext("http://SiteUrl");
Web web = context.Web; 
SP.List list = web.Lists.GetByTitle("Announcements"); 

```

Se sabe que la lista se crea al:
  
    
    

- Obtener la propiedad **Web** del contexto actual.
    
  
- Obtener la propiedad **Lists** del resultado anterior.
    
  
- Invocar el método **GetByTitle** con el parámetro _Announcements_ desde el resultado anterior.
    
  
Cuando el CSOM de .NET Framework de SharePoint pasa esta información al servidor, puede volver a crear el objeto en el servidor. En la biblioteca de cliente, puede mantener un seguimiento de **ObjectPath** que crea el objeto de cliente. Ya que sabe cómo se crea el objeto, puede usarlo como parámetro para otros métodos dentro de la misma consulta.
  
    
    

### Recuperar el grupo de datos en el mismo objeto para mejorar el rendimiento

Al leer múltiples fragmentos de datos desde el objeto mismo, debe tratar de obtener todo en una sola consulta; es decir, con una sola llamada al método **Load<T>(T, [])**. El código siguiente muestra dos formas de recuperar el título y la descripción de un sitio web y la descripción de la lista de **Anuncios**. Para compilar este código tiene que agregar una instrucción **using** para [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Además, agregue un alias a la **statement** que se usa para el espacio de nombres [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    

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

Estos no son igualmente eficientes. En **Method1**, el código para recuperar el título y la descripción de la web se agrupa juntos. En **Method2**, el código para recuperar el título y la descripción de la web están separados por otras acciones. Esto significa que **Method2** activará dos consultas separadas en el mismo objeto de la web y habrá dos conjuntos de resultados para la misma web. Ya que la biblioteca de cliente trata de devolver datos coherentes, el segundo conjunto de resultados incluirá el título y la descripción. Podría pensar que el código anterior es como el siguiente.
  
    
    



```

Method1:
SELECT Title, Description FROM Webs WHERE ... 
SELECT Description FROM Lists WHERE … 

Method2: 
SELECT Title FROM Webs WHERE … 
SELECT Description FROM Lists WHERE … 
SELECT Title, Description FROM Webs WHERE … 

```


### Especificar las propiedades de los objetos que desea devolver

En el modelo de objetos de servidor de SharePoint, si obtiene un objeto **SPWeb**, puede inspeccionar todas sus propiedades. En SQL, para obtener todas las columnas de una tabla puede ejecutar:
  
    
    

```

SELECT * FROM Webs 
```

En la biblioteca de cliente, ni **Load<T>** ni cualquier otro método devuelve todas las propiedades, por lo que tiene que especificar explícitamente lo que desee. Por ejemplo, el siguiente código recupera el objeto del sitio web sin especificar qué propiedades se van a devolver. Luego trata de leer dos propiedades y una de ellos no está entre las propiedades que **Load** devuelve automáticamente. Este código genera una excepción.
  
    
    



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

Para obtener el código para compilar correctamente, actualícelo a la siguiente versión. Para compilar este código tiene que agregar una instrucción **using** para [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Además, agregue un alias a la instrucción **using** para el espacio de nombres de [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo, `using SP = Microsoft.SharePoint.Client;`.
  
    
    



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


### Usar ámbito condicional para probar condiciones previas antes de cargar datos

Para ejecutar código de forma condicional, establezca un ámbito condicional con un objeto  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) . Por ejemplo, recupere la propiedad de la lista cuando la lista no sea válida. También necesitará agregar instrucciones **using** para [System.Collections.Generic](https://msdn.microsoft.com/library/System.Collections.Generic.aspx) y [System.Linq](https://msdn.microsoft.com/library/System.Linq.aspx) . Además, agregue un alias a la instrucción **using** para el nombre de espacio [Microsoft.SharePoint.Client](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.aspx) , así puede hacer referencia a sus clases de forma inequívoca. Por ejemplo `using SP = Microsoft.SharePoint.Client;`.
  
    
    

> [!NOTA]
> No se permite llamar al método y establecer las propiedades dentro de un ámbito condicional, porque la biblioteca de cliente no realiza el seguimiento de los efectos secundarios de las llamadas a métodos y valores de la propiedad. Dentro del ámbito condicional solo debe usar **Load**. 
  
    
    


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


### Usar un ámbito de control de excepciones para capturar excepciones

En este ejemplo se muestra cómo crear y usar un ámbito con un objeto de excepciones  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) . El escenario es actualizar la descripción de una lista y también habilitar la creación de la carpeta. Puede darse la posibilidad de que la lista no exista.
  
    
    

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


## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Crear sitios para SharePoint](http://msdn.microsoft.com/library/3b372a63-7cdf-462a-abb4-750e611e967d%28Office.15%29.aspx)
    
  

