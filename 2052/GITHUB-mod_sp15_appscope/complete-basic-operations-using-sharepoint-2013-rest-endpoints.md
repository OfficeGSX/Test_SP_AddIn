---
title: 使用 SharePoint 2013 REST 终结点完成基本操作
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# 使用 SharePoint 2013 REST 终结点完成基本操作
了解如何使用 SharePoint 2013 REST 接口执行基本的创建、读取、更新和删除 (CRUD) 操作。
## 使用 SharePoint 客户端 API 和 REST 进行开发
<a name="ClientAPIs"> </a>

您可以通过使用 SharePoint 2013 提供的表示性状态传输 (REST) 界面执行基本的创建、读取、更新和删除 (CRUD) 操作。REST 界面公开所有的 SharePoint 实体和在其他 SharePoint 客户端 API 中可用的操作。使用 REST 的优点之一是您不必添加对任何 SharePoint 2013 库或客户端程序集的引用。相反，您向适当的终结点发出 HTTP 请求来检索或更新SharePoint 实体，如网站、列表和列表项。请参阅 [了解 SharePoint 2013 REST 服务](get-to-know-the-sharepoint-2013-rest-service.md)，它全面地介绍了SharePoint 2013 REST 界面及其体系结构。



 [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md) 和 [使用 REST 处理文件夹和文件](working-with-folders-and-files-with-rest.md)更加详细地说明了如何使用核心的 SharePoint 实体。请参阅  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)，它通过示例向您演示如何在使用 C# 编写的 ASP.NET Web 应用程序的上下文中执行这些操作。



有关 SharePoint 2013 平台上可用的 API 集的更多详细信息，请参阅 [在 SharePoint 2013 中选择正确的 API 集](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。有关如何使用其他客户端 API 的信息，请参阅 [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)、 [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)和 [构建访问 SharePoint 2013 的 Windows Phone 应用程序](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)。




## SharePoint 2013 REST 服务中的 HTTP 操作
<a name="HTTPOps"> </a>

SharePoint 2013 REST 服务中的终结点对应于 SharePoint 客户端对象模型中的类型和成员。通过使用 HTTP 请求，您可以使用这些 REST 终结点对 SharePoint 实体（例如列表和网站）执行典型的 CRUD（ **Create**、 **Read**、 **Update** 和 **Delete**）操作。



通常，表示 **Read** 的端点映射到 HTTP **GET** 命令。表示更新操作的端点映射到 HTTP **POST** 命令，而表示更新或插入操作的端点则映射到 HTTP **PUT** 命令。



在 SharePoint 2013 中，使用 **POST** 可以创建注入列表和网站的工件。SharePoint 2013 REST 服务支持将包括对象定义的 **POST** 命令发送到表示集合的终结点。例如，您可以将 ATOM 中包括新列表定义的 **POST** 命令发送到以下 URL，以创建 SharePoint 列表：



 `http://<site url>/_api/web/lists`



对于 **POST** 操作，任何不需要的属性将设置为其默认值。如果您尝试在 **POST** 操作过程中设置只读属性，则服务将返回异常。



使用 **PUT**、PATCH 和 **MERGE** 操作可以更新现有 SharePoint 对象。任何表示对象属性 **set** 操作的服务终结点均支持 **PUT** 请求和 **MERGE** 请求。对于 **MERGE** 请求，设置属性是可选的；任何未显式设置的属性将保留其当前属性。但是，对于 **PUT** 命令，任何未显式设置的属性将设置为其默认属性。此外，如果您在使用 HTTP **PUT** 命令时未在对象更新中指定所有必需的属性，则 REST 服务将返回异常。



对某个特定终结点 URL 使用 HTTP **DELETE** 命令可删除该终结点表示的 SharePoint 对象。对于可循环的对象（如列表、文件和列表项），这将导致 **Recycle** 操作。




## 使用 SharePoint 2013 REST 界面读取数据
<a name="ReadingData"> </a>

若要使用 SharePoint 2013 内置的 REST 功能，请您使用 OData 标准（它对应于您要使用的客户端对象模型 API）构建 REST 样式的 HTTP 请求。每个 SharePoint 实体都在您的目标 SharePoint 2013 网站上的终结点上公开，其元数据由 XML 或 JSON 格式表示。您可以用任何一种语言（包括但不限于 JavaScript 和 C#）发出 HTTP 请求。



要从 REST 终结点读取信息，您必须知道该终结点的 URL 和在该终结点上公开的 SharePoint 实体的 OData 表示。例如，若要检索特定 SharePoint 网站中的所有列表，应向  `http://<site url>/_api/web/lists` 发出 **GET** 请求。您可以在浏览器中导航到该 URL 并查看返回的 XML。当您在代码中发出请求时，可以指定接收 XML 还是 JSON 形式的列表的 OData 表示。



以下 C# 代码代码演示了如何发出 **GET** 请求，该请求通过使用 JQuery 返回站点的所有列表的 JSON 表示。它还假定您有一个有效的 OAuth 访问令牌，存储在 **accessToken** 变量中。如果您从外接程序 Web 内部进行此调用（就像在 SharePoint 托管的外接程序中一样），则不需要访问令牌。请注意，您无法从浏览器客户端上运行的代码中获取访问令牌。访问令牌必须从服务器上运行的代码中获取。 [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)和  [SharePoint 外接程序的身份验证代码 OAuth 流](authorization-code-oauth-flow-for-sharepoint-add-ins.md)说明了如何获取访问令牌。






```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

如果您使用 JavaScript 编写外接程序但使用 SharePoint 2013 跨域库，此请求看起来会有点不同。在这种情况下，您不需要提供访问令牌。以下代码演示如果您使用跨域库并要以 XML（而不是 JSON）格式接收列表的 OData 表示，此请求会是怎样的。（由于 Atom 是默认的响应格式，您无需包含 **Accept** 标头。）请参阅 [使用跨域库从外接程序访问 SharePoint 2013 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)，了解有关使用跨域库的详细信息。






```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);
```

以下示例中的代码显示了如何使用 C# 请求网站中所有列表的 JSON 表示。它假定您有一个存储在  `accessToken` 变量中的 OAuth 访问令牌。






```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();

```


### 获取没有返回资源的属性
<a name="NavigationProperties"> </a>

当您检索资源时将返回多个属性值，但对于某些属性，您必须直接向属性终结点发送 **GET** 请求。这是显示 SharePoint 实体的属性的典型行为。



以下示例显示了如何通过将属性名称附加到资源端点来获取属性。示例从 **File** 资源获取 **Author** 属性的值。



http:// _<网站 url>_/_api/web/getfilebyserverrelativeurl('/ _<文件夹名称>_/ _<文件名称>_')/作者



要获取 JSON 格式的结果，请加入一个设置为  `"application/json;odata=verbose"` 的 **Accept** 标头。




## 通过使用 REST 界面写入数据
<a name="WritingData"> </a>

您可以通过构建发送到适当终结点的 REST 样式的 HTTP 请求来创建和更新 SharePoint 实体，就像您阅读数据一样。但是，一个关键的区别是您使用 **POST** 请求。当您更新实体时，您还通过将一个 **PUT** 或 **MERGE** HTTP 请求方法作为 **X-HTTP-Method** 键的值添加到您的请求标头，来传递该 HTTP 请求方法。 **MERGE** 方法仅更新您指定的实体的属性，而 **PUT** 方法会将现有实体替换为您在 **POST** 的正文中提供的一个新实体。使用 **DELETE** 方法可删除实体。当您创建或更新实体时，您必须在 HTTP 请求的正文中提供您要创建或更改的实体的 OData 表示。



创建、更新和删除 SharePoint 实体时的另一个重要考虑是，如果您不是使用 OAuth 对请求进行授权，则这些操作需要将服务器的请求窗体摘要值作为 **X-RequestDigest** 标头的值。您可以通过以下方式检索此值：向 `http://<site url>/_api/contextinfo` 发送具有空正文的 **POST** 请求，在 **contextinfo** 终结点返回的 XML 中提取 `d:FormDigestValue` 节点的值。以下示例演示了向 C# 中的 **contextinfo** 终结点发出的 HTTP 请求。






```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

如果您使用  [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)中所述的身份验证和授权流，则无需将请求摘要包含在请求中。



如果您使用 JavaScript 跨域库，SP.RequestExecutor 将负责获取窗体摘要值并将其发送给您。



如果您创建一个 SharePoint 承载的 SharePoint 外接程序，您不必发出单独的 HTTP 请求来检索窗体摘要值。相反，您可以检索 SharePoint 页面（若该页面使用默认母版页）的 JavaScript 代码中的值，如以下示例所示，其中使用 JQuery 并创建一个列表。






```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});


```

以下示例演示如何更新在前面的示例中创建的列表。该示例更改列表的标题、使用 JQuery，并假定您在 SharePoint 托管的外接程序中执行此操作。






```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});

```

在请求标头中的 **IF-MATCH** 键的值中，您指定列表或列表项的 **etag** 值。此特定值仅适用于列表和列表项，它旨在帮助您避免在更新这些实体时出现并发问题。前面的示例对此值使用星号 (*)，只要您没有任何理由担心并发问题，您就可以使用此值。否则，您应通过执行一个 **GET** 请求（用于检索实体）来获取列表或列表项的 **etag** 值。由此产生的 HTTP 响应的响应标头将作为 **ETag** 键的值传递。此值也包含在实体元数据中。以下示例显示了包含列表信息的 XML 节点的开始 `<entry>` 标记。 **m:etag** 属性包含 **etag** 值。






```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">
```


## 使用 REST 创建网站
<a name="bk_CreateSite"> </a>

以下示例演示如何在 JavaScript 中创建网站。




```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});
```


## REST 请求如何因环境而异
<a name="bk_HowRequestsDiffer"> </a>

构建和发送 HTTP 请求可能会因语言、库和外接程序类型而不同，因此在将请求从一个环境转换到另一个环境时，经常需要更改一个或多个请求组件。例如，jQuery AJAX 请求使用 **data** 和 **type** 参数指定请求正文和类型，但跨域库请求使用 **body** 和 **method** 参数指定这些值。



以下各节介绍各环境之间的其他常见差异。




### 您获取和发送窗体摘要值的方式取决于外接程序
<a name="FormDigest"> </a>

发送 POST 请求时，请求必须在 **X-RequestDigest** 标头中加入窗体摘要值。但是，您获取和发送值的方式因外接程序而异：




- 在 SharePoint 托管的外接程序中，您可以仅传递以下标头：



 "X-RequestDigest": $("#__REQUESTDIGEST").val()


- 在使用 OAuth 的云托管的外接程序，首先通过向 **contextinfo** 终结点发送请求来检索窗体摘要值，然后将其添加到请求，如 [使用 REST 界面写入数据](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData)中所述。


- 在使用 JavaScript 跨域库的云托管的外接程序中，您无需指定窗体摘要值。默认情况下，SP.RequestExecutor 将自动处理此操作。（它还会处理内容长度值。）



### 使用 OAuth 的外接程序必须在请求中传递访问令牌
<a name="OAuthTokens"> </a>

云托管的外接程序使用 OAuth 或跨域库来授予访问 SharePoint 数据的权限。如果外接程序组件包含运行于远程 Web 服务器上的代码，则必须使用 OAuth 授予访问 SharePoint 数据的权限。在这种情况下，需要添加 **Authorization** 标头以发送访问令牌。请参阅 [使用 SharePoint 2013 REST 接口读取数据](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData)获取将授权标头添加到 **HTTPWebRequest** 对象的示例。




> **注释**
> 在 JavaScript 中写入的云托管的外接程序组件必须使用跨域库中的 **SP.RequestExecutor** 对象访问 SharePoint 数据。跨域库请求无需包含访问令牌。




有关 OAuth 访问令牌和如何获取这些访问令牌的详细信息，请参阅  [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)和 [SharePoint 外接程序的身份验证代码 OAuth 流](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。




### 跨域请求中的终结点 URI 使用 SP.AppContextSite 更改上下文
<a name="AppContextSite"> </a>

将请求发送到请求的 **url** 属性中指定的资源终结点。终结点 URI 使用以下格式：



 _<网站 URL>_/_api/ _<上下文>_/ _<资源>_（示例，https://contoso.com/_api/web/lists）



跨域库请求在访问外接程序 Web 上的数据时使用此格式，这是跨域库请求的默认上下文。但是，如果要访问主机 Web 或其他网站集中的数据，该请求需要初始化主机 Web 或其他网站集作为上下文。为此，它们在 URI 中使用 **SP.AppContextSite** 终结点，如表 1 中所示。表 1 中的示例 URI 使用 **@target** 别名在查询字符串中发送目标 URL，因为此 URL 包含一个特殊字符 (':')。




> **注释**
> 云托管的外接程序需要外接程序 Web 实例，才能在使用跨域库时访问 SharePoint 数据。 





**表 1. 使用 SP.AppContextSite 终结点更改请求的上下文**


|**外接程序类型**|**跨域数据访问方案**|**示例终结点 URI**|
|:-----|:-----|:-----|
|云承载  <br/> |JavaScript 外接程序组件通过使用跨域库访问主机 Web 数据  <br/> | _<应用程序 Web URL>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<主机 Web URL>_'  <br/> |
|云承载  <br/> |JavaScript 外接程序组件通过使用跨域库访问网站集中的数据，而不是主机 Web 中的数据（仅租户范围的外接程序）  <br/> | _<应用程序 Web URL>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<目标网站 URL>_'  <br/> |
|SharePoint 承载  <br/> |外接程序 Web 组件访问其他网站集中的数据（仅租户范围的外接程序）  <br/> | _<应用程序 Web URL>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<目标网站 URL>_'  <br/> |
 

> **注释**
> 跨域库数据访问方案还需要相应的外接程序权限。有关详细信息，请参阅 [访问主机 Web 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)和 [访问各网站集的数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope)。 




SharePoint 外接程序可以从外接程序页面的查询字符串中获取外接程序 Web URL 和主机 Web URL，如以下代码示例中所示。此示例还显示了如何引用主机 Web 上 SP.RequestExecutor.js 文件中定义的跨域库。示例假设从 SharePoint 启动外接程序。请参阅 [SharePoint 外接程序的身份验证代码 OAuth 流](authorization-code-oauth-flow-for-sharepoint-add-ins.md)获取有关外接程序不是从 SharePoint 启动时正确设置 SharePoint 上下文的指南。






```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions
```


## REST 请求中使用的属性
<a name="bk_requestElements"> </a>

表 2 显示了在 SharePoint REST 服务的 HTTP 请求中常用的属性。




**表 2. 在 HTTP 请求中使用 REST 请求属性的情况**


|**属性**|**何时需要使用**|**说明**|
|:-----|:-----|:-----|
|**url** <br/> |所有请求  <br/> |REST 资源端点的 URL。示例： `http://<site url>/_api/web/lists` <br/> |
|**method**（或 **type**）  <br/> |所有请求  <br/> |HTTP 请求方法： **GET** （针对读取操作）和 **POST**（针对写入操作）。 **POST** 请求可以通过指定 **DELETE** 标头中的 **MERGE**、 **PUT** 或 **X-HTTP-Method** 动词执行更新或删除操作。 <br/> |
|**body**（或 **data**）  <br/> |在请求正文中发送数据的 **POST** 请求 <br/> |POST 请求的正文。发送无法在端点 URI 中发送的数据（例如复杂类型）。用于 **content-length** 标头。 <br/> |
|**Authentication** 标头 <br/> |使用 OAuth 验证用户的远程外接程序。使用 JavaScript 或跨域库时不适用。  <br/> |发送 OAuth 访问令牌（从 Microsoft 访问控制服务 (ACS) 安全令牌服务器获得），该令牌用于针对请求验证用户。示例： `"Authorization": "Bearer " + accessToken`，其中  `accessToken` 表示存储令牌的变量。令牌必须通过服务器端代码检索。 <br/> |
|**X-RequestDigest** 标头 <br/> |**POST** 请求（SP.RequestExecutor 请求除外） <br/> |使用 OAuth 的远程外接程序可以从  `http://<site url>/_api/contextinfo` 终结点获取窗体摘要值。SharePoint 托管的外接程序可从 **#__REQUESTDIGEST** 页面控件获取在 SharePoint 页面上的可用值。请参阅 [通过使用 REST 界面写入数据](#WritingData)。  <br/> |
|**accept** 标头 <br/> |返回 SharePoint 元数据的请求  <br/> |为来自服务器的响应数据指定格式。默认格式为  `application/atom+xml`。示例： `"accept":"application/json;odata=verbose"` <br/> |
|**content-type** 标头 <br/> |在请求正文中发送数据的 **POST** 请求 <br/> |为客户端发送到服务器的数据指定格式。默认格式为  `application/atom+xml`。示例： `"content-type":"application/json;odata=verbose"` <br/> |
|**content-length** 标头 <br/> |在请求正文中发送数据的 **POST** 请求（SP.RequestExecutor 请求除外） <br/> |指定内容的长度。示例： `"content-length":requestBody.length` <br/> |
|**IF-MATCH** 标头 <br/> |**POST**、 **DELETE** 或 **MERGE** 操作的 **PUT** 请求，主要用于更改列表和库。 <br/> |提供一种用于验证正在更改的对象自上次检索后尚未更改的方法。或者，允许您指定覆盖任何更改，如以下示例所示： `"IF-MATCH":"*"` <br/> |
|**X-HTTP-Method** 标头 <br/> |**POST**、 **DELETE** 或 **MERGE** 操作的 **PUT** 请求 <br/> |用于指定请求执行了一项更新或删除操作。示例：  `"X-HTTP-Method":"PUT"` <br/> |
|**binaryStringRequestBody** <br/> |在正文中发送二进制数据的 SP.RequestExecutor **POST** 请求 <br/> |指定请求正文是否为二进制字符串。 **Boolean**。  <br/> |
|**binaryStringResponseBody** <br/> |返回二进制数据的 SP.RequestExecutor 请求  <br/> |指定响应是否为二进制字符串。 **Boolean**。  <br/> |
 

## 批处理作业支持
<a name="batch"> </a>

SharePoint Online（和本地 SharePoint 2016 及更高版本）REST 服务支持使用 OData  `$batch` 查询选项，将多个请求合并到对该服务的单个调用中。有关详细信息和代码示例的链接，请参阅 [使用 REST API 发出批处理请求](make-batch-requests-with-the-rest-apis.md)。




## 其他资源
<a name="bk_addresources"> </a>


-  [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md)


-  [使用 REST 处理文件夹和文件](working-with-folders-and-files-with-rest.md)


-  [SharePoint 2013 REST API、终结点和示例](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013：使用 REST 对文件和文件夹执行基本的数据访问操作](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)


-  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md)


-  [打开数据协议](http://www.odata.org/)


-  [OData：JavaScript 对象表示法 (JSON) 格式](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)


-  [使用 REST 接口设置列表的自定义权限](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)







