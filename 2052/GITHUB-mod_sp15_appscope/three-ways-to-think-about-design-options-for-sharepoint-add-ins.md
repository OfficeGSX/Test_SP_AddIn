---
title: 考虑 SharePoint 外接程序设计选项的三种方法
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# 考虑 SharePoint 外接程序设计选项的三种方法
大致了解可用于 SharePoint 外接程序的设计和体系结构选项。您应该先熟悉一下文章 [SharePoint 外接程序](sharepoint-add-ins.md)。
## 概述和第一个决策
<a name="Overview"> </a>

本文以三种不同的方式介绍了 SharePoint 外接程序的体系结构选项。第一种，了解设计选项最重要的类别；第二种，在应用程序层方面观察外接程序体系结构；第三种，了解在制定设计决策时需要考虑的一系列因素。



但是，第一个要做出的决定是 SharePoint 扩展应为 SharePoint 外接程序还是经典的 SharePoint 服务器场解决方案或沙盒解决方案。SharePoint 对象模型的某些部件（主要与自定义 SharePoint 管理和安全性有关）无法从客户端访问。只有在 SharePoint 服务器上运行的自定义代码可以访问这些部件，且 SharePoint 外接程序中不允许使用自定义服务器端代码。（利用大量客户端对象模型和一项 REST/OData 服务，SharePoint 外接程序几乎可以实现任何面向最终用户的 SharePoint 扩展。）有关在经典解决方案和外接程序之间做出决定的详细信息，请参阅 [SharePoint 加载项与 SharePoint 解决方案比较](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)。 [在 SharePoint 2013 中选择正确的 API 集](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)对做此决定也有帮助。




## SharePoint 外接程序的设计中的关键因素
<a name="MajorCategoriesOfChoices"> </a>

在设计 SharePoint 外接程序后，需要做出三个主要类别的选择。像往常一样，应用程序设计包括权衡；您在一个类别中作出的选择可能限制您在另一个类别中的选择。并非所有可能的选项组合都可行。




- **承载：** 可根据 SharePoint 外接程序的部署和承载方式将其分为两个主要类型，这很有用。

  - **提供程序托管的**外接程序让其主要数据存储和业务逻辑由您（开发人员）从 SharePoint 外部部署和托管在服务器或您提供云帐户中。您负责强制实现购买您的外接程序的各个客户的帐户之间的隔离。此类外接程序也可能包含 SharePoint 组件。这些组件在客户的 SharePoint 场中托管。此类型的外接程序为您在其他设计选择的类别上提供了最大的灵活性。它还支持您对外部数据、逻辑和 Web 用户接口 (UI) 使用非 Microsoft 平台。（在提供程序托管的外接程序中，您还需要区分其远程组件位于与 SharePoint 场相同的企业防火墙中的外接程序以及其远程组件位于该防火墙之外的外接程序。这两种情况下的授权系统是不同的，这反过来会使您用来访问 SharePoint 数据的编程语言也不同。）


  - **SharePoint 托管的**外接程序完全由 SharePoint 2013 组件（如列表、内容类型、工作流和 Web 部件）组成，它没有外部组件。有关可包含在 SharePoint 外接程序中的 SharePoint 组件的类型的详细信息，请参阅 [SharePoint 2013 中的主机 Web、外接程序 Web 和 SharePoint 组件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)。



    有关 SharePoint 外接程序的托管选项的更多详细信息，请参阅 [为开发和托管 SharePoint 外接程序选择模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)。


- **连接性：** SharePoint 2013 支持三种类型的对数据的安全 create/read/update/delete (CRUD) 访问。

  - 外接程序中的外部 Web 应用程序使用 OAuth 协议访问 SharePoint 数据。有关详细信息，请参阅 [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)。


  - JavaScript 可使用支持安全的跨域脚本的特殊 JavaScript 库访问 SharePoint 外接程序 Web 中的数据和相同租赁中的其他网站上的数据。有关详细信息，请参阅 [使用跨域库从外接程序访问 SharePoint 2013 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。


  - SharePoint 外接程序还可通过 Business Connectivity Services (BCS) 或 Web 服务代理访问外部数据。有关详细信息，请参阅 [SharePoint 2013 中的 Business Connectivity Services](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx)和 [使用 SharePoint 2013 中的 Web 代理查询远程服务](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)。



    有关 SharePoint 外接程序中的数据存储和访问的详细信息，请参阅 [SharePoint 外接程序中的数据存储选项](3034f03c-2d5a-46de-9cb8-2c101ff194fa.md)、 [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)和 [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md)。


- **UI：** 可通过三种方式在 SharePoint 中显示 SharePoint 外接程序：至少，所有外接程序都会显示在一个完整的网页中。或者，也可以通过外接程序部件显示外接程序，以及通过菜单项或功能区按钮显示外接程序。有关详细信息，请参阅 [SharePoint 外接程序的 UX 设计](ux-design-for-sharepoint-add-ins.md)。

    > **注释**
    > SharePoint 外接程序可由客户安装到一个租赁中的多个网站集中，或由客户安装在每个网站上。前者称为租户范围外接程序。如果希望客户具有租户范围选项，您就无法包含自定义功能区按钮或外接程序部件。有关详细信息，请参阅 [SharePoint 外接程序的租赁和部署范围](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。 

## 体系结构层
<a name="Tiers"> </a>

考虑外接程序体系结构选项的另一个方法是将外接程序视为具有三个逻辑层：UI、业务逻辑和数据访问。每一层都有多个实现选项；再次重申，对某一层做出的选择将限制对其他层的选择。下表介绍了外接程序的远程组件和 SharePoint 组件的一部分选项及其用途。




**提供程序托管的外接程序中的远程组件：针对每一层的选项**


|**层**|**选项**|**适用情况**|
|:-----|:-----|:-----|
|UI  <br/> |ASP.NET 窗体中的 ASP.NET 页或在 Microsoft Azure Web 角色中承载的 MVC 应用程序。  <br/> |充分利用 ASP.NET 开发人员的技能。  <br/> |
||使用 JavaScript 的 HTML 5 网页。  <br/> |富用户界面。  <br/> |
||PHP 或可在非 Microsoft 云服务中承载的其他类型的网页。  <br/> |将非 Microsoft 应用程序集成到 SharePoint 中。  <br/> |
||Windows Phone 外接程序中的 Silverlight。  <br/> |移动访问 SharePoint 数据并集成地理位置数据和推送通知。  <br/> |
|业务逻辑  <br/> |客户端 JavaScript。  <br/> |UI 逻辑和轻型业务逻辑。  <br/> 通过 JavaScript 客户端对象模型访问 SharePoint 数据。  <br/> |
||Microsoft Azure 辅助角色。  <br/> |需要大量处理器资源的功能。  <br/> 通过 .NET Framework 客户端对象模型访问 SharePoint 数据。  <br/> |
||远程 Web 服务。  <br/> |需要大量处理器资源的功能。  <br/> 通过 .NET Framework 客户端对象模型访问 SharePoint 数据。  <br/> |
|数据  <br/> |SQL Azure。  <br/> |功能齐全的关系数据。  <br/> |
||Microsoft Azure 表存储。  <br/> |应用程序设置和其他元数据。  <br/> |
||Microsoft Azure BLOB 存储。  <br/> |大型文件的存储。  <br/> |
||非 Microsoft 云服务。  <br/> |利用基于非 Microsoft 平台的现有数据源。  <br/> |
||开发人员自己的服务器上的数据库。  <br/> |提供程序承载和租赁隔离的开发人员控制。  <br/> |
 

**SharePoint 组件：针对每一层的选项**


|**层**|**选项**|**适用情况**|
|:-----|:-----|:-----|
|UI  <br/> |外接程序网页上的 SharePoint 2013 列表和库的自定义视图。  <br/> |与 SharePoint 外观和行为进行最大程度的集成。  <br/> |
||在外接程序网页上的 Web 部件中（或在 <object> 标记中）托管的 Silverlight 应用程序。  <br/> |利用现有 Silverlight 开发体验。  <br/> 富用户界面。  <br/> |
|业务逻辑  <br/> |SharePoint 工作流。  <br/> |实现业务流程。  <br/> |
||SharePoint 跨域库附带的客户端 JavaScript。  <br/> |在外接程序 Web 中访问 SharePoint 数据。  <br/> 访问租赁中的其他网站中的数据。  <br/> |
||远程事件处理程序。  <br/> |处理外部数据源中的 CRUD 事件。  <br/> |
|数据  <br/> |通过协作应用程序标记语言 (CAML) 或 LINQ（使用 SharePoint 客户端对象模型之一的查询）查询到的 SharePoint 2013 列表和库。  <br/> |利用现有 SharePoint 和 .NET Framework 开发体验。  <br/> |
||通过 SharePoint REST/OData Web 服务查询的 SharePoint 2013 列表和库。  <br/> |从非 Microsoft 平台访问 SharePoint 数据。  <br/> 利用现有 OData 查询体验。  <br/> |
||BCS 模型。  <br/> |以 SharePoint 列表的形式在 SharePoint 中显示外部数据。  <br/> |
 

## 做出设计决定时要考虑的因素
<a name="DecisionFactors"> </a>

SharePoint 外接程序模型使单个决策树无法实现的很多功能都成为了现实。下面是在构造 SharePoint 外接程序的体系结构时要考虑的一些最重要的因素。




- 当然，最重要的一个因素是您要提供给客户的功能，即用例。例如，如果您的外接程序包括占用大量处理器资源的功能（如将视频文件转换为其他视频格式），这就会成为您创建一个提供程序托管的外接程序（其中的处理将会在一个服务器或某个 Microsoft Azure 辅助角色上完成）的理由。


- 由于提供程序托管的外接程序（一种 SharePoint 外接程序）要求您（或您的客户）托管非 SharePoint 组件并强制实现租户隔离，因此您需要考虑是否让硬件和 IT 员工执行此操作（或是否让您的目标客户执行此操作）。


- 您的目标客户是哪些也是一个重要的考虑事项。如果您的所有外接程序都是在内部使用的（也就是说，您没有外部客户），那么提供程序托管的外接程序的实现和维护将比您有外部客户时容易得多。如果您打算公开出售外接程序，则还应考虑是将外接程序销售给具有 SharePoint Online 帐户的企业，销售给具有其自己的 SharePoint 场的企业，还是具有这两者的企业。


- 您还应考虑您的现有技能或您的开发员工的技能。例如，如果您是一个有经验的 ASP.NET 开发人员，则可以考虑创建远程 Web 应用程序以及在 ASP.NET 页上显示 Sharepoint 列表数据。另一方面，如果您是一个有经验的 SharePoint 开发人员，则可以考虑将自定义 SharePoint 列表和网站页与 JavaScript 结合使用来执行处理。



## 其他资源
<a name="AdditionalResources"> </a>


-  [设计 SharePoint 外接程序](design-sharepoint-add-ins.md)


-  [SharePoint 外接程序体系结构的重要方面和开发前景](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)



