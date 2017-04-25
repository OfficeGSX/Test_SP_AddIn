---
title: SharePoint 外接程序体系结构的重要方面和开发前景
ms.prod: SHAREPOINT
ms.assetid: ae96572b-8f06-4fd3-854f-fc312f7f2d88
---


# SharePoint 外接程序体系结构的重要方面和开发前景
了解 SharePoint 外接程序和 SharePoint 外接程序的模型的体系结构的特征，包括加载项托管选项、用户界面选项、部署系统、安全系统和生命周期。本文补充了  [SharePoint 外接程序](sharepoint-add-ins.md)文章中的信息。
## SharePoint 加载项承载选项
<a name="SPAppModelArch_SPCenteredVsCloudCentered"> </a>

SharePoint 2013 加载项模型提供以下方法来托管 SharePoint 外接程序的组件：




- **提供程序承载：**加载项至少包括一个远程组件，并且还可能包括 SharePoint 托管的组件。非 SharePoint 组件按您的硬件或云帐户的逻辑进行部署，或者使用您提供的安装程序和说明在客户的硬件或云帐户上部署。


- **SharePoint 托管：**加载项只包括 SharePoint 组件以及在客户端上运行的逻辑。


有关托管选项的更多详细信息以及如何在它们之间进行选择的一些指导，请参阅  [为开发和托管 SharePoint 外接程序选择模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)。




## 加载项中的加载项 Web、主机 Web、功能和 SharePoint 组件
<a name="SPComponents"> </a>

SharePoint 外接程序所安装到的网站称为主机 Web。但是，SharePoint 外接程序的重要部件（无论是 SharePoint 组件还是外部组件）不会部署到主机 Web。外部部件会部署到外部服务器或云帐户。SharePoint 组件会部署到具有其自己的域的特殊网站。此网站称为加载项 Web。只有有限的一组 UI 元素（向用户提供加载项的其他组件的访问权限）会部署到主机 Web。主机 Web 中的这些 UI 组件会作为主机 Web 功能（即散装在加载项包中而不是在 .wsp 文件中的功能）的一部分进行部署。部署到加载项 Web 的组件始终位于 .wsp 文件内部的功能中。这两种功能都必须具有 **Web** 作用域。SharePoint 外接程序中的功能不可能具有其他作用域。



通常，任何不包含运行于 SharePoint 服务器上的自定义代码的 SharePoint 组件都可以包含在 SharePoint 外接程序中（并且可以部署到加载项 Web 中）。但是，组件的部署方式和位置有一些例外和细微差别。有关这些细微差别、主机 Web、独立的加载项 Web 以及加载项中的功能的详细信息，请参阅 [SharePoint 2013 中的主机 Web、外接程序 Web 和 SharePoint 组件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)。




## 从 UI 访问加载项
<a name="AccessingApp"> </a>

在网站上安装 SharePoint 外接程序后，会在主机 Web 的"网站内容"页面上列出该加载项。用户可以从该页面中启动此加载项。以这种方式打开时，该加载项将在全屏模式下运行。



可用于呈现 SharePoint 外接程序的另一种方法是使用加载项部件，即一种由 **ClientWebPart** 类表示的 Web 部件。这种 Web 部件本质上是托管加载项页面的 IFrame 的包装。在最简单的例子中，Web 部件的唯一重要属性是指向页面的 URL。但 Web 部件可具有用户能够在工具部件中设置的自定义属性。可以使用此类属性，例如用于设置用户的邮政编码之类的上下文信息。若要在加载项中包括此类加载项部件，您可以在加载项中创建一个主机 Web 功能，并添加声明性 Web 部件标记。像任何其他 Web 部件一样，它将出现在 SharePoint 2013 UI 中，用户可以从此 UI 中添加 Web 部件。如果需要更强的变化性，则可以随加载项一起部署多个加载项部件。例如，天气加载项可以具有一个显示当前天气的加载项部件，以及另一个显示每周天气预报的加载项部件。这两个部件可以具有不同的大小和功能。




> **注释**
> 您也可以将加载项部件部署到加载项 Web。若要执行此操作，Web 部件的标记应该是加载项包中 .wsp 文件内部的功能的一部分，而不在主机 Web 功能内。 




我们建议您尽可能尝试为您的加载项提供一个 SharePoint 外观，虽然这不是必须的，并且可能并不始终是最佳选择。有关用户体验指南的详细信息，请参阅  [SharePoint 外接程序的 UX 设计](ux-design-for-sharepoint-add-ins.md)。



例如，存在一个名为 app.master 的特殊母版页。此页面经过了优化以供加载项页面使用。此 app.master 页面是 SharePoint 2013 中所包括的新网站定义的一部分。



可用于帮助加载项与 SharePoint 保持一致外观的另一个工具是 SharePoint 2013 附带的版式控件。利用此控件，您可以将 SharePoint 导航标题区域添加到您的加载项页面中，包括外部托管的页面。有关 SharePoint 外接程序中的 UX 设计的详细信息，请参阅 [SharePoint 外接程序的 UX 设计](ux-design-for-sharepoint-add-ins.md)。有关版式控件的详细信息，请参阅 [在 SharePoint 外接程序中使用客户端部件版式控制](use-the-client-chrome-control-in-sharepoint-add-ins.md)。




## 加载项包结构
<a name="SPAppModelArch_Package"> </a>

SharePoint 外接程序包是一个具有".app"扩展名并且符合 [开放数据包约定 (OPC)](http://msdn.microsoft.com/zh-cn/magazine/cc163372.aspx) 的文件。（通过在文件名上添加".zip"作为额外的扩展名，然后在 Windows 资源管理器中打开此文件，您可以打开此文件。）它包含一个加载项清单，此清单指定加载项的某些属性以及针对 SharePoint 安装基础结构的说明。有关加载项清单和包的详细信息，请参阅 [了解 SharePoint 外接程序的应用程序清单结构和包](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)。




## SharePoint 加载项的权限、身份验证和授权
<a name="SPAppModelArch_Running"> </a>

SharePoint 2013 引入了新的加载项权限和安全系统。




### 加载项权限
<a name="AppPermissions"> </a>

SharePoint 外接程序具有权限，就像用户和组具有权限一样。这使加载项能够具有一组权限，这组权限不同于正在执行该加载项的用户的权限。



您必须在加载项清单文件中请求加载项运行所需的权限。添加加载项的用户必须准许这些请求，用户只能授予作为用户所具有的权限。授予必须针对所有请求的权限，或者不针对任何权限，以简化用户和开发人员的权限管理。（加载项主体始终具有对加载项 Web 的完全控制权限，因此，它只需要请求对主机 Web 中或加载项 Web 外部其他位置中的 SharePoint 资源的权限。）



有关加载项权限的详细信息，请参阅 [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)。




### 选择性委派和授权
<a name="SelectiveAuthorization"> </a>

正在启动加载项的用户以及正在授予加载项访问资源的权限的资源所有者都不需要向加载项提供其凭据或密码。相反 SharePoint 2013 使用户和资源所有者能够仅授予加载项请求的特定权限。SharePoint 2013 通过使用事务协议  [OAuth 2.0](http://tools.ietf.org/html/draft-ietf-oauth-v2-22) 使这成为可能。有关 SharePoint 2013 中的 OAuth 的详细信息，请参阅 [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)。




### 跨域访问
<a name="SelectiveAuthorization"> </a>

包括远程 Web 应用程序（将 JavaScript 用于其数据访问逻辑）的 SharePoint 外接程序可以使用 JavaScript 跨域库，以获取安装了加载项的租赁中的 SharePoint 数据的访问授权。有关详细信息，请参阅 [使用跨域库从外接程序访问 SharePoint 2013 数据](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。




## 加载项生命周期
<a name="SPAppModelArch_Lifecycle"> </a>

SharePoint 外接程序的生命周期包括发布、安装、升级和卸载。有关这些主题的详细信息，请参阅 [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)、 [部署和安装SharePoint 外接程序：方法和选项](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)以及  [SharePoint 外接程序更新过程](sharepoint-add-ins-update-process.md)。另外请注意，租户管理员可以使用某种机制将 SharePoint 外接程序成批安装到多个网站中。有关详细信息，请参阅  [SharePoint 外接程序的租赁和部署范围](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。




## SharePoint 加载项中的数据
<a name="Data"> </a>

SharePoint 外接程序可以创建和访问任何种类的数据，包括结构化数据、文档和多媒体文件。此数据可以存储在 SharePoint 或外部位置中。




### 结构化数据存储选项
<a name="StructuredData"> </a>

SharePoint 外接程序可以在 SharePoint 2013 的内部和外部以及 Microsoft 平台和非 Microsoft 平台上使用几乎所有类型的结构化数据存储。 *以下*  位置可用于存储 SharePoint 外接程序的结构化数据：




- 加载项 Web 中的 SharePoint 列表


- SQL Azure


- 通过 Microsoft Business Connectivity Services (BCS) 连接到 SharePoint 的外部数据源


- 非 Microsoft 云服务


- 您自己的服务器上的数据库



> **提示**
> 在某些情况下，您可能会升级 SharePoint 外接程序。当 SharePoint 外接程序包含加载项 Web 中的 SharePoint 组件时，升级过程会完整复制加载项 Web。因此，加载项 Web 中的大型 SharePoint 列表会让内容数据库服务器上的升级过程变得耗时并占用大量处理器资源。应该避免将"大数据"放到加载项 Web 上的 SharePoint 列表中。 





### 非结构化数据存储选项
<a name="UnStructuredData"> </a>

由 SharePoint 外接程序生成或使用的文档、图像、视频、音频文件和其他类型的非结构化数据可存储在 SharePoint 的内部或外部。文档库是文档的最佳选择，并且可以通过 SharePoint 搜索来搜索文档库。网站资产库通常是多媒体文件的最佳选择。



其他选项包括您的 Microsoft Azure 帐户中或您自己的服务器上的 Blob 存储。您还可以在一些非 Microsoft 平台上或云服务中存储文件。




### 加载项设置和其他元数据存储选项
<a name="AppMetadata"> </a>

SharePoint 外接程序的元数据（如用户首选项、位置信息和其他设置）可存储在多个位置。有时，隐藏的 SharePoint 列表是一个很好的选择。您还可以使用加载项 Web 的属性包。对于提供程序承载的加载项，另一个选项是使用 Microsoft Azure 表存储。




### 安全的数据访问选项
<a name="DataAccess"> </a>

当然，您的安全数据访问选项取决于您的存储选择。数据访问和搜索将在另外几篇文章中进行详细讨论。有关详细信息，请参阅  [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)。




## 管理加载项
<a name="SPAppModelArch_Managing"> </a>

网站集管理员和租户管理员可以监视加载项，以及更改分配给加载项的资源。此外，加载项存储的 Microsoft 人员可以标记加载项以及禁用加载项。



有关管理加载项的详细信息，请参阅 TechNet 上的 [安装和管理 SharePoint 加载项](http://msdn.microsoft.com/zh-cn/library/fp161232.aspx) 。




### 监视加载项
<a name="SPAppModelArch_Monitoring"> </a>

SharePoint 2013 提供加载项运行状况监视，并且可以在 UI 中向网站所有者、租户管理员和场管理员提供此信息。此监视系统的大部分文档位于 TechNet 上；例如 [监视 SharePoint 加载项](http://technet.microsoft.com/library/3adafdd2-f276-4a9d-8a74-e06b8916bbc2)。本部分只是一个快速简介，用于说明如何监视您销售的加载项。



某些种类的数据是按应用程序报告的，而其他种类是按应用程序实例报告的。监视框架报告的主要项目如下：




- 加载项的使用情况，如已安装应用程序的次数（创建新实例）。


- 每个加载项实例的服务器资源消耗。


- 每个加载项实例的安装、升级和运行时错误。


- 绿色、黄色和红色的每个加载项实例的整体运行状况指示器。


如果加载项包括 Azure 网站组件，则监视框架每小时还会轮询一次 Microsoft Azure，看其是否有错误数据，并且会在 SharePoint 2013 UI 中报告关键错误和存储配额数据。不报告 Microsoft Azure SQL 数据库 错误。



利用监视框架提供的信息，管理员可以确定其加载项采购预算是否精打细算，他们是否必须向加载项部署更多资源，以及他们是否必须禁用未正常工作的加载项。




## 注册加载项依赖项
<a name="RegisterDependency"> </a>

如果您的 SharePoint 外接程序依赖不可用且无法在加载项 Web 上提供的 SharePoint 功能，它将无法正常运行，并且您的客户将抱怨。通过在加载项清单中注册您的加载项的依赖项，您可以确保在未提供必备服务和功能的情况下，不会安装您的加载项。SharePoint 外接程序的安装基础结构将检查这些必备组件，如果其中任一组件不可用，它将阻止安装您的加载项。



对于 Excel、Access 或 Visio Services 等服务，该基础结构将验证是否已安装并许可相应服务。



对于任务列表等功能，该基础结构将验证是否已部署相应功能，并且：



-- 已在 **Farm**、 **WebApplication** 或 **Site**（网站集）范围内激活该功能



或



-- 可使用 **Web** 范围在安装加载项时所创建的加载项 Web 上激活该功能。




> **注释**
> 加载项安装基础结构将自动在所创建的加载项 Web 上激活此类功能。 




以下各节提供了注册必备组件所需的详细信息。




### 使用权限请求隐式注册依赖项
<a name="PermAsPreq"> </a>

当您的加载项需要访问加载项 Web 之外的 SharePoint 组件时，它必须在加载项清单的 **AppPermissionRequests** 部分请求访问这些资源的权限。这些权限请求还推动了必备组件注册，因为 SharePoint 将从您的加载项请求的权限推断加载项需要使用某些 SharePoint 功能。在许多情况下，SharePoint 可以推断您的加载项需要的所有功能，因此不必阅读本主题的其余各节。但是，额外的依赖项注册没有害处。




### 使用 AppPrerequistes 显式注册依赖项
<a name="Explicit"> </a>

当您的加载项具有未由其权限请求默示的依赖项时，请在加载项清单中使用 **AppPrerequisite** 元素注册每个依赖项。此元素具有三个属性： **Type**、 **ID** 和（可选） **MinimumVersion**。



 **Type** 具有三个可能的必备组件值： `Feature`、 `Capablility` 和 `AutoProvisioning`。Feature 必备组件仅是必须在加载项 Web 上或包括加载项 Web 的更广范围内部署并激活的 SharePoint 功能。Capability 是一组必须在加载项 Web 上提供的相关功能和服务。（将在下一节中讨论  `AutoProvisioning`。）



可选的 **MinimumVersion** 指定您的加载项需要的特性或功能的最低版本。此属性值采用 n.n.n.n 形式；例如 `15.0.0.0`。



 **ID** 指定所需特性或功能。如果 **Type** 为 `Feature`，则 **ID** 为功能的用括号括住且用连字符相连的 GUID；例如 `{151D22D9-95A8-4904-A0A3-22E4DB85D1E0}`。如果 **Type** 为 `Capability`，则 **ID** 为功能的 GUID。下面列出了所有功能。若要查找功能的 GUID，请参阅 [AppPrerequisite 元素 (AppPrerequisiteCollection 复杂类型) (SharePoint 接清单)](http://msdn.microsoft.com/library/791be402-981f-519e-fcde-f24cc3cb4139%28Office.15%29.aspx)。




- Access Services 2010


- Access Services


- Managed Metadata Web Service


- PowerPoint Services


- Secure Store Services


- Machine Translation Service


- User Profile Service


- Visio Graphics Service


- Work Management Service


- Duet


- Workflow


- Search


- EDU


下面是注册工作流功能的原始 **AppPrerequisites** 标记的示例。如果您使用的是 Visual Studio，可在设计器工具中编辑加载项清单。






```

<AppPrerequisites>
  <AppPrerequisite Type="Capability" ID="{CDD8F991-B459-4512-8048-03D5A03FF27E}" MinimumVersion="15.0.0.0" />
</ AppPrerequisites>
```


## 本节内容
<a name="RegisterDependency"> </a>


-  [为开发和托管 SharePoint 外接程序选择模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)


-  [SharePoint 2013 中的主机 Web、外接程序 Web 和 SharePoint 组件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)



## 其他资源
<a name="SPAppModelArch_AdditionalResources"> </a>


-  [SharePoint 外接程序](sharepoint-add-ins.md)


-  [SharePoint 加载项与 SharePoint 解决方案比较](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)


-  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)


-  [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)


-  [SharePoint 外接程序的 UX 设计](ux-design-for-sharepoint-add-ins.md)


-  [IFrame](http://www.w3schools.com/tags/tag_iframe.asp)


-  [了解 SharePoint 外接程序的应用程序清单结构和包](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)


-  [部署和安装SharePoint 外接程序：方法和选项](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [SharePoint 外接程序更新过程](sharepoint-add-ins-update-process.md) 。


-  [SharePoint 外接程序的租赁和部署范围](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)



