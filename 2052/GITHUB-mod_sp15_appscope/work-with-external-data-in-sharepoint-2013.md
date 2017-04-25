---
title: 在 SharePoint 2013 中处理外部数据
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# 在 SharePoint 2013 中处理外部数据
查找在 SharePoint 外接程序中使用页面上的 JavaScript 访问和处理外部数据的资源和指南。
## 在 SharePoint 外接程序中使用 JavaScript
<a name="SP15Workdata_Working"> </a>

在 SharePoint 外接程序中，您必须频繁地从 SharePoint 页面或组件中检索和处理远程 Web 应用程序或服务公开的数据。由于在 SharePoint 服务器上不允许自定义代码，您的外接程序必须使用 JavaScript 执行此操作。SharePoint 外接程序的模型提供多种访问远程数据和服务的选项。




### 使用 SharePoint 跨域 JavaScript 库访问外部数据

如果您提供了在远程基础结构中承载的自定义代理页，则可以使用跨域库访问远程 Web 应用程序中的数据。作为开发人员，您要负责实现自定义代码页，且必须处理自定义逻辑（如针对远程应用程序的身份验证机制，如果有）。如果您希望远程数据源与 SharePoint 页面之间的通信在客户端级别进行，则应使用跨域库。



有关如何以这种方式使用库的详细信息，请参阅 [为 SharePoint 2013 中的跨域库创建自定义代理页](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)。




> **注释**
> SharePoint 跨域库也可以用于其他方向；即，远程网页上的 JavaScript 可以使用它访问 SharePoint 中的数据。有关库的此用途的详细信息，请参阅 [创建使用跨域库的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)。 





### 使用 SharePoint Web 代理访问外部数据

您可以使用在 JavaScript 客户端对象模型中公开的 Web 代理来访问远程数据。（代理在 .NET 客户端对象模型 (CSOM) 中也提供，但您无法在运行在 SharePoint 服务器上的代码中使用该对象模型。）在您使用 Web 代理时，将向 SharePoint 发出初始请求。然后，SharePoint 会请求将数据传递到指定终结点并将响应转发回您的页面。如果您希望远程数据源与 SharePoint 页面之间的通信在服务器级别进行，则应使用 Web 代理。



有关如何使用代理的详细信息，请参阅 [使用 SharePoint 2013 中的 Web 代理查询远程服务](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)。




## 其他资源
<a name="SP15Workdata_AddRes"> </a>


-  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)



