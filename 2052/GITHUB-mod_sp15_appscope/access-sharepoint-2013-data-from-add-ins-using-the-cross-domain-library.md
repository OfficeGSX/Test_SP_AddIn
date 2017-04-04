---
title: 使用跨域库从外接程序访问 SharePoint 2013 数据
ms.prod: SHAREPOINT
ms.assetid: bc37ff5c-1285-40af-98ae-01286696242d
---



# 使用跨域库从外接程序访问 SharePoint 2013 数据
了解如何在 SharePoint 2013 中使用跨域库从外接程序访问 SharePoint 网站中的数据。
在构建 SharePoint 外接程序时，您通常必须合并来自各种源的数据。但出于 [安全目的](http://msdn.microsoft.com/library/cc709423.aspx)设置了一些防止同时与多个域通信的阻止机制。大多数浏览器中都实现了这些安全机制，加大跨域完成客户端调用的难度，或使其无法完成。
  
    
    

图 1 显示了跨域阻止的请求。
**图 1. 跨域阻止的请求**

  
    
    
用户在请求外接程序域 (1) 的页面时，客户端通信仅与该域绑定。您的外接程序可以仅向同一域中的其他资源从页面发起客户端调用。然而，外接程序通常需要来自其他域的资源（如 SharePoint 域）来实现方案。在您页面的代码中，您可以尝试向被浏览器阻止的 SharePoint 域 (2) 发出一个请求。通常可以看到 **禁止访问** 错误。此错误不表示您不具有访问请求资源的权限，而最有可能的是，您甚至无法向提及的资源发出请求。您在使用跨域库时，外接程序中的网页可以访问外接程序域和 SharePoint 域中的数据。跨域库是 SharePoint 网站（可在远程外接程序中引用）上托管的 JavaScript 文件 (SP.RequestExecutor.js) 形式的客户端替代项。利用跨域库，您可以通过代理与远程外接程序页中的多个域交互。如果您想让外接程序代码在客户端，而不是服务器中运行，或者 SharePoint 与您的远程基础结构之间存在连接障碍（如防火墙），则这是一个不错的选择。您可以访问主机 Web 中的数据：例如，无论您的外接程序如何，您都可以访问最终用户与之交互的列表。或者，您也可以访问外接程序 Web 中的数据，例如专门为外接程序设置的列表。外接程序还可以访问其他网站集和网站，只要外接程序具有租户级权限，且使用外接程序目录作为批量安装进行了部署。
> **注释**
> 本主题中， **外接程序域** 指托管了外接程序页面的域。可以是提供商托管的外接程序页面中的远程 Web 应用程序域，但外接程序页面还可以在外接程序 Web 中的 SharePoint 之上，并向主机 Web 域发起调用。在后一种方案中，外接程序域是外接程序 Web 的域。
  
    
    

本文中的主要示例显示如何构建读取外接程序 Web 上的数据的外接程序，并将其显示在网页中。 [后续步骤](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Next)部分显示构建于主要示例之上的更多方案。
## 使用本文中的示例的先决条件
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

若要按照本文中的示例操作，您需要具备以下各项：
  
    
    

-  [Visual Studio 2012](https://www.microsoft.com/zh-cn/download/details.aspx?id=30682)
    
  
-  [Microsoft Visual Studio 2012 的 Office 开发工具](https://msdn.microsoft.com/zh-cn/office/aa905340.aspx)
    
  
- 一个 SharePoint 2013 开发环境（内部部署方案要求应用程序隔离）
    
  

## 使用跨域库读取外接程序 Web 上的数据
<a name="SP15Accessdatafromremoteapp_Codeexample"> </a>

本示例中，SharePoint 外部托管的简单页面使用代表性状态传输 (REST) 终结点读取 SharePoint 网站（外接程序 Web）中的数据。由于跨域库需要外接程序 Web，因此从此方案开始是合理的。
  
    
    
若要读取外接程序 Web 数据，必须执行下列操作：
  
    
    

1. 创建一个 SharePoint 外接程序和多个 Web 项目。
    
  
2. 在外接程序 Web 中创建列表项。此步骤也会确保在用户部署外接程序时，也创建了外接程序 Web。
    
  
3. 创建一个使用跨域库读取列表项的外接程序页面。
    
  
图 2 说明了显示外接程序 Web 上的数据的网页。
  
    
    

**图 2. 显示外接程序 Web 上的数据的网页**

  
    
    

  
    
    
![跨域读取项目示例结果屏幕](images/CrossDomainReadItemsResult.png)
  
    
    

### 创建一个 SharePoint 外接程序和多个 Web 项目。


1. 以管理员身份打开 Visual Studio 2008。（为此，请右键单击"启动"菜单上的 Visual Studio 2008 图标，然后选择"以管理员身份运行"。）
    
  
2. 使用"SharePoint 2013 外接程序"模板创建一个新项目。
    
    Visual Studio 2008 中"SharePoint 2013 外接程序"模板的位置在"模板" **>** "Visual C#"、"Office SharePoint" **>** "外接程序"下。
    
  
3. 提供您要用于调试的 SharePoint 网站 URL。
    
  
4. 选择"提供程序托管"作为您的外接程序的托管选项。
    
    > **注释**
      > 您还可以使用 SharePoint 承载的外接程序中的跨域库。然而，在 SharePoint 承载的外接程序中，外接程序页面已存在于外接程序 Web 中，在这种情况下，就无需使用跨域库读取列表项。对于读取主机 Web 中的数据的 SharePoint 承载的外接程序示例，请参阅 [在 SharePoint 托管的外接程序中使用跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)或参阅本文后面的 [访问主机 Web 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)。 

### 在外接程序 Web 中创建列表项


1. 右键单击"解决方案资源管理器"中的 SharePoint 外接程序项目。选择"添加" **>**"添加新项…"
    
  
2. 选择"Visual C# 项" **>** "Office/SharePoint" **>** "列表"。将列表名称设置为 **Announcements** 。
    
  
3. 双击"Announcements" **>** "Elements.xml"。粘贴以下 XML 节点作为 **ListInstance** 元素的子元素。
    
 ```
  
<Data>
    <Rows>
        <Row>
            <Field Name="Title">Lorem ipsum 1</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
        <Row>
            <Field Name="Title">Lorem ipsum 2</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
    </Rows>
</Data>
 ```


### 添加使用跨域库的新页面


1. 在"解决方案资源管理器"的 Web 项目中，双击"Default.aspx"文件。
    
  
2. 复制以下代码并将其粘贴到 Default.aspx 文件中。该代码将执行以下任务：
    
  - 从 Microsoft CDN 加载 jQuery 库。
    
  
  - 提供结果的占位符。
    
  
  - 从查询字符串中提取外接程序 Web URL。
    
  
  - 使用 jQuery 中的 **getScript** 函数加载跨域库 JavaScript。
    
    此函数将加载所需资源，然后加载指定的函数，以确保跨域库已加载并可供后续代码使用。
    
  
  - 实例化 **RequestExecutor** 对象。默认情况下，RequestExecutor 使用外接程序 Web 作为上下文网站。
    
    > **注释**
      > 您可以使用 **AppContextSite** 终结点 (REST) 或对象 (JSOM) 将上下文网站更改为其他不同于外接程序 Web 的网站。若要了解有关 AppContextSite 的详细信息，请参阅本文后面的 [访问主机 Web 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)。 
  - 向列表项终结点发起 REST 调用。
    
  
  - 处理成功完成操作，并在网页中显示列表项。
    
  
  - 处理错误，并在网页中显示错误消息。
    
  

 ```
  
<html>
    <head>
        <title>Cross-domain sample</title>
    </head>
    <body>
        <!-- This is the placeholder for the announcements -->
        <div id="renderAnnouncements"></div>
        <script 
            type="text/javascript" 
            src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
        </script>
        <script type="text/javascript">
          var hostweburl;
          var appweburl;

          // Load the required SharePoint libraries
          $(document).ready(function () {
            //Get the URI decoded URLs.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );

            // resources are in URLs in the form:
            // web_url/_layouts/15/resource
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js files and continue to the successHandler
            $.getScript(scriptbase + "SP.RequestExecutor.js", execCrossDomainRequest);
          });

          // Function to prepare and issue the request to get
          //  SharePoint data
          function execCrossDomainRequest() {
            // executor: The RequestExecutor object
            // Initialize the RequestExecutor with the add-in web URL.
            var executor = new SP.RequestExecutor(appweburl);

            // Issue the call against the add-in web.
            // To get the title using REST we can hit the endpoint:
            //      appweburl/_api/web/lists/getbytitle('listname')/items
            // The response formats the data in the JSON format.
            // The functions successHandler and errorHandler attend the
            //      sucess and error events respectively.
            executor.executeAsync(
                {
                  url:
                      appweburl +
                      "/_api/web/lists/getbytitle('Announcements')/items",
                  method: "GET",
                  headers: { "Accept": "application/json; odata=verbose" },
                  success: successHandler,
                  error: errorHandler
                }
            );
          }

          // Function to handle the success event.
          // Prints the data to the page.
          function successHandler(data) {
            var jsonObject = JSON.parse(data.body);
            var announcementsHTML = "";

            var results = jsonObject.d.results;
            for (var i = 0; i < results.length; i++) {
              announcementsHTML = announcementsHTML +
                  "<p><h1>" + results[i].Title +
                  "</h1>" + results[i].Body +
                  "</p><hr>";
            }

            document.getElementById("renderAnnouncements").innerHTML =
                announcementsHTML;
          }

          // Function to handle the error event.
          // Prints the error message to the page.
          function errorHandler(data, errorCode, errorMessage) {
            document.getElementById("renderAnnouncements").innerText =
                "Could not complete cross-domain call: " + errorMessage;
          }

          // Function to retrieve a query string value.
          // For production purposes you may want to use
          //  a library to handle the query string.
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
    </body>
</html>
 ```


### 生成并运行解决方案


1. 按 F5 键。
    
    > **注释**
      > 按 F5 时，Visual Studio 将生成解决方案、部署外接程序并打开外接程序的权限页。 
2. 选择"信任它"按钮。
    
  
3. 选择"网站内容"页上的外接程序图标。
    
  
如果您更喜欢可下载的代码示例，可以从代码库获取。 **代码示例：使用采用  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain) 或 [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain) 的跨域库获取列表项** 。
  
    
    

**表 2. 问题及解决方案**


|**如果您看到：**|**请尝试：**|
|:-----|:-----|
|错误消息：很抱歉，访问您的网站时出现问题。  <br/> 有一个按钮可以修复此错误，但无法纠正该问题。  <br/> |您可能在 Internet Explorer 中遇到安全区域的已知问题，请参阅 [跨 SharePoint 外接程序中的不同 Internet Explorer 安全区域使用跨域库](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)。  <br/> |
|错误消息：您的浏览器不支持所需功能。请确保您使用的是 IE 8 或更高版本或其他现代浏览器。请确保 ""X-UA-Compatible"" META 标记设置为 ""IE=8"" 或更高版本。  <br/> |跨域库需要 **IE8** 或更高版本的文档模式。在某些情况下，文档模式默认设置为 **IE7** 。您可以使用 Internet Explorer 开发人员工具来确定并更改页面的文档模式。有关详细信息，请参阅 [定义文档兼容性](http://msdn.microsoft.com/zh-cn/library/cc288325.aspx)。  <br/> |
|错误消息："类型"未定义。  <br/> 此外，您的外接程序使用 JavaScript 对象模型 (JSOM)。  <br/> |JSOM 使用 Microsoft Ajax library 的 **Type.registerNamespace** 方法注册 **SP** 命名空间。使用以下代码将引用从页面添加到 Microsoft Ajax library。 <br/>```HTML<script type="text/javascript"  src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js"></script>```|
   

## 后续步骤
<a name="SP15Accessdatafromremoteapp_Next"> </a>

本文介绍如何使用未托管在 SharePoint 上的外接程序页面查询 REST 终结点以读取外接程序 Web 数据。您还可以浏览以下有关跨域库的方案和详细信息。
  
    
    

### 使用 JSOM 读取外接程序 Web 数据
<a name="SP15Accessdatafromremoteapp_JSOM"> </a>

根据您的喜好，您可能想要使用 JSOM 而不是 REST 查询外接程序 Web 数据。您必须完成其他任务才能与 JSOM 一起使用跨域库：
  
    
    

- 参考外接程序页面中的 SharePoint JSOM。
    
  
- 初始化 **ProxyWebRequestExecutorFactory** 对象并将其设置为上下文对象的工厂。
    
  
- 访问 SharePoint 对象以读取列表数据。
    
  
- 加载上下文中的对象并执行查询。
    
  
有关介绍如何执行任务的代码示例，请参阅  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)。有关如何使用 JSOM 的详细信息，请参阅 [使用 SharePoint 外接程序中的 JavaScript 对象模型 (JSOM)](http://blogs.msdn.com/b/officeapps/archive/2012/09/04/using-the-javascript-object-model-jsom-in-apps-for-sharepoint.aspx)。
  
    
    

### 访问主机 Web 数据
<a name="SP15Accessdatafromremoteapp_Hostweb"> </a>

本页面中的示例显示了如何读取外接程序 Web 数据。将此作为开始示例会收效良好，因为跨域库最初使用外接程序作为上下文网站。但是，您想要访问主机 Web 数据有多种方案。需要执行一些任务，才能访问主机 Web 数据：
  
    
    

- 将主机 Web 设置为跨域库的上下文网站。
    
  
- 为外接程序提供相应的权限。
    
  
您可以使用 **AppContextSite** 终结点 (REST) 或对象 (JSOM) 更改上下文网站。以下示例显示了如何使用 REST 终结点更改上下文网站：
  
    
    


```

executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/title?@target='" +
            hostweburl + "'",
        method: "GET",
        headers: { "Accept": "application/json; odata=verbose" },
        success: successHandler,
        error: errorHandler
    }
);```

以下代码示例显示了如何使用 JSOM 更改上下文网站：
  
    
    


```

context = new SP.ClientContext(appweburl);
factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
context.set_webRequestExecutorFactory(factory);
appContextSite = new SP.AppContextSite(context, hostweburl);

this.web = appContextSite.get_web();
context.load(this.web);```

默认情况下，您的外接程序具有访问外接程序 Web 的权限，但不具有访问主机 Web 的权限。以下示例显示了声明读取主机 Web 数据的权限请求的清单部分：
  
    
    


```XML

<AppPermissionRequests>
    <AppPermissionRequest 
        Scope="http://sharepoint/content/sitecollection/web" 
        Right="Read" />
</AppPermissionRequests>```

请确保在外接程序 Web 上创建一个资源（如空页面或列表）以设置外接程序 Web，这需要使用跨域库。
  
    
    

### 跨网站集访问数据
<a name="SP15Accessdatafromremoteapp_TenantScope"> </a>

通过使用跨域库，您可以以同一租户跨网站集访问数据。以下是跨网站集访问数据需要完成的一些任务：
  
    
    

- 添加一个权限请求以访问租户数据。
    
  
- 在代码中，将上下文网站切换为您想要查询的网站集。
    
  
- 将外接程序添加到外接程序目录。
    
  
- 将外接程序作为租户范围外接程序部署到网站。有关如何以租户范围外接程序部署的示例，请参阅 [在租户范围外接程序中使用跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e) 代码示例的说明。
    
  
您的外接程序还需要访问租户数据的权限。以下示例显示了声明读取租户数据的权限请求的清单部分：
  
    
    


```XML

<AppPermissionRequests>
  <AppPermissionRequest 
    Scope="http://sharepoint/content/tenant" 
    Right="Read" />
</AppPermissionRequests>```

若要在代码中切换上下文网站，请使用 **AppContextSite** 终结点 (REST) 或对象 (JSOM)，如 [访问主机 Web 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)部分。以下是 REST 终结点的提醒：/_api/SP.AppContextSite(@target)/web/title?@target='weburl'，以及如何实例化 JSOM 中的对象的示例： `appContextSite = new SP.AppContextSite(context, weburl);`。
  
    
    
作为开发人员，您只能部署外接程序目录中的租户范围外接程序。您可以将外接程序目录设置为您的本地或 SharePoint Online 环境。将外接程序上载到外接程序目录如同将文件上载到文档库中一样简单易行。请参阅 [将自定义外接程序添加到外接程序目录网站](http://office.microsoft.com/zh-cn/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)获取详细说明。
  
    
    
您可以将外接程序目录中的外接程序部署到租户中的一个或多个网站。由于您的外接程序具有访问租户数据的权限，您只需要部署到一个网站，就可以访问整个租户的数据。请参阅 [部署自定义外接程序](http://office.microsoft.com/zh-cn/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)获取有关如何部署外接程序目录中的外接程序的说明。
  
    
    
若要下载说明如何跨网站集访问数据的代码示例，请参阅 [在租户范围外接程序中使用跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)。
  
    
    

### 跨不同安全区域发起调用
<a name="SP15Accessdatafromremoteapp_IEZones"> </a>

跨域库使用托管在外接程序页面上的 **IFrame** 中的代理页面启用通信。当外接程序页和 SharePoint 网站位于不同的安全区域时，无法发送授权 Cookie。如果没有授权 Cookie，则 IFrame 将尝试加载代理页，它将重定向至 SharePoint 登录页。由于安全原因，SharePoint 登录页不能包含在 IFrame 中。在这些方案中，库无法加载代理页，并且无法与 SharePoint 通信。
  
    
    
然而，有一个针对这些场景的解决方案，那就是 **apphost 模式** ，此模式将外接程序页面打包到外接程序 Web 托管的一个页面中。在使用跨域库的外接程序中使用 apphost 模式是一个很好的想法，即使没有明确的安全范围也是如此。有关详细信息，请参阅 [跨 SharePoint 外接程序中的不同 Internet Explorer 安全区域使用跨域库](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)。
  
    
    

### 访问 SharePoint 承载的外接程序中其他远程主机数据
<a name="SP15Accessdatafromremoteapp_SPhosted"> </a>

默认情况下，只要具有相应的权限，就允许 SharePoint 承载的外接程序向主机 Web 发起跨域调用。但是，SharePoint 承载的外接程序也可以在其 **AppPrincipal** 的 **AllowedRemoteHostUrl** 属性中指定一个远程主机。这将使您可以有效地从外接程序 Web 和其他主机发起跨域调用。
  
    
    
若要下载使用跨域库的 SharePoint 承载的外接程序，请参阅 [代码示例：在 SharePoint 托管的外接程序中使用跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)。
  
    
    

## 其他资源
<a name="SP15Accessdatafromremoteapp_Addresources"> </a>


-  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)
    
  
-  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)
    
  
-  [代码示例：使用跨域库获取主机 Web 标题 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Get-the-0ec36bb6)
    
  
-  [代码示例：使用跨域库获取主机 Web 标题 (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)
    
  
-  [代码示例：在 SharePoint 托管的外接程序中使用跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)
    
  
-  [代码示例：在租户范围外接程序中使用跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)
    
  
-  [代码示例：使用部件版式控制和跨域库 (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)
    
  
-  [代码示例：使用部件版式控制和跨域库 (JSOM)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)
    
  
-  [代码示例：使用自定义操作和跨域库订购书籍](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)
    
  
-  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [跨 SharePoint 外接程序中的不同 Internet Explorer 安全区域使用跨域库](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  
-  [为 SharePoint 2013 中的跨域库创建自定义代理页](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [使用 SharePoint 2013 中的 Web 代理查询远程服务](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)
    
  
-  [设置 SharePoint 加载项的本地开发环境](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
