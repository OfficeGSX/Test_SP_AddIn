---
title: 创建使用跨域库的 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# 创建使用跨域库的 SharePoint 外接程序
了解 SharePoint 跨域 JavaScript 库。
在某些情况下，SharePoint 外接程序不能使用低信任或高信任授权系统，或者它们不是作为外接程序获取 SharePoint 资源授权的唯一方式的最佳选择。示例：





- SharePoint 外接程序的远程组件不在本地环境中，但公司防火墙会阻止 SharePoint 与 ACS 之间的服务器到服务器通信，从而阻止使用低信任系统。


- SharePoint 外接程序设计为单页 Web 应用程序，它依赖于客户端 JavaScript 来执行 SharePoint 的数据操作。


- SharePoint 外接程序主要依赖于服务器到服务器调用来访问 SharePoint 数据（并由低信任或高信任系统授权），但需使用一些 JavaScript 调用进行补充。例如，图形密集页面可以使用 JavaScript 对显示的数据进行一些次要更新，而无需重新加载整个页面。



但是， [为安全起见](http://msdn.microsoft.com/zh-cn/library/cc709423%28v=vs.85%29.aspx)，浏览器不允许承载在一个域中的 JavaScript 访问另一个域中的资源，因此需要特殊技术以允许远程 JavaScript 访问 SharePoint 资源。SharePoint 跨域 JavaScript 库使您的远程 Web 应用程序可以轻松使用此技术。





> **注释**
> 跨域库还可用于允许反向访问数据，即允许 SharePoint 页面上的 JavaScript 访问远程域中的数据。有关详细信息，请参阅 [从 SharePoint 页面访问远程数据](#ReverseDirection)。 





## 了解跨域库的体系结构

SharePoint 跨域库包含在文件 SP.RequestExecutor.js 中，该文件位于每个 SharePoint 网站的 /_layouts/15/ virtual 文件夹中。此文件中的脚本封装了安全的已知技术，用以克服浏览器的跨域脚本限制：iFrame 可以通过  `window.postMessage()` 函数与其父页面通信，即使 iFrame 中的页面位于其他域。因此数据请求和响应通过调用 `postMessage()`，跨越域界线传递。




> **警告**
>  `postMessage()` 函数仅可在支持 HTML 5 的浏览器上运行，因此使用跨域库的 SharePoint 外接程序将不能在旧浏览器上运行。




对于 SharePoint，跨域库加载在远程 Web 应用程序的页面上，它在此页面创建从 SharePoint 域承载特殊代理页面的隐藏 iFrame。代理页面已存在于每个 SharePoint 网站中。库用于创建包含向 SharePoint 的 REST API 发出 CRUD 调用所需的所有信息的 JavaScript 对象表示法 (JSON) 对象。JSON 对象使用  `postMessage()` 传递到代理页面。在同时加载库的代理页面上，将解析 JSON 对象并将其重新构建为对 SharePoint 的 REST 调用。由于代理页面位于 SharePoint 域，浏览器将允许此调用。



当然，SharePoint 外接程序的远程组件仍然必须具有 SharePoint 资源的授权访问权限。可使用以下两种方法执行此操作：




- 在外接程序清单中，将外接程序主体类型设置为 **RemoteWebApplication**（提供程序托管的外接程序的默认设置）。当外接程序在 ACS 中注册时，注册包括远程 Web 应用程序的域。在此方案中，SharePoint 信任在 ACS 中注册的域，即使它不是，使用属于服务器端低信任系统的任一令牌传递流。有关注册外接程序的详细信息，请参阅 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)。


- 在 SharePoint 托管的外接程序中，您可以将外接程序主体类型保留设置为默认值，即 **Internal**。然后将 **Internal** 元素的 **AllowedRemoteHostUrl** 属性设置为远程 Web 应用程序的 URL，如以下示例中所示。

  ```

<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>
  ```


> **注释**
> 如果您使用第二个选项（ **Internal** 外接程序主体），您可以使用仅 JavaScript 和跨域库访问 SharePoint。SharePoint 客户端对象模型对 **Internal**SharePoint 外接程序为冻结状态，因此您不能具有同时使用跨域库和低信任或高信任系统的双授权系统。 




有关如何使用库的详细信息，请参阅 [使用跨域库从外接程序访问 SharePoint 2013 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。




## 从 SharePoint 页面访问远程数据
<a name="ReverseDirection"> </a>

SharePoint 跨域库可用于反方向；即 SharePoint 页面上的 JavaScript 可以使用库从外接程序的远程组件获取数据。为此，您需还原跨域体系结构：在远程 Web 应用程序中创建代理页面。从创建托管代理页面的 iFrame 的 SharePoint 页面调用库。有关如何以这种方式使用库的详细信息，请参阅 [为 SharePoint 2013 中的跨域库创建自定义代理页](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)。




## 本节内容
<a name="ReverseDirection"> </a>


-  [使用跨域库从外接程序访问 SharePoint 2013 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)


-  [跨 SharePoint 外接程序中的不同 Internet Explorer 安全区域使用跨域库](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)



## 其他资源
<a name="ReverseDirection"> </a>


-  [解决 SharePoint 外接程序中的跨域问题](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)



