---
title: 了解 SharePoint 外接程序的应用程序清单结构和包
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# 了解 SharePoint 外接程序的应用程序清单结构和包
了解 SharePoint 外接程序的外接程序包结构和清单文件。
## SharePoint 相关外接程序包结构
<a name="Package"> </a>

SharePoint 外接程序包是文件扩展名为".app"且使用 [开放数据包约定 (OPC)](http://msdn.microsoft.com/zh-cn/magazine/cc163372.aspx) 编译的文件。此包包含下列项：
  
    
    

- **外接程序清单：**这是名为 appmanifest.xml 的必需文件。它告知 SharePoint 2013 有关外接程序的一些重要属性信息，如外接程序标题和外接程序运行所需的权限。有关此文件内容的详细信息，请参阅  [SharePoint 相关外接程序清单文件](#AppManifest)。
    
  
- **SharePoint 解决方案包：**（可选）外接程序可能包含一个 SharePoint 解决方案包（.wsp 文件），其中包含外接程序 Web 的组件。这些组件可能包含页、列表实例、视图、文档、 **Web** 范围的功能和其他 SharePoint 2013 组件。（有关 SharePoint 外接程序中可包含的 SharePoint 组件的详细信息，请参阅 [可位于 SharePoint 外接程序中的 SharePoint 组件的类型](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps)。）.wsp 文件还可能包含 Office 外接程序。.wsp 文件中的组件将部署到外接程序 Web 中。有关包含 SharePoint 解决方案包的外接程序包的示例，请参阅 [创建包括自定义 SharePoint 列表和内容类型的提供商托管的外接程序](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md)。
    
  
- **包含自定义操作或外接程序部件的主机 Web 功能：**除了部署到外接程序 Web 的 SharePoint 2013 组件之外，SharePoint 外接程序还可将一个或多个自定义操作（快捷菜单项或功能区扩展）部署到主机 Web。这是通过将位于程序包的 .wsp 文件之外和部署将转到主机 Web 的组件的功能包含在外接程序包中来实现的。此"稀疏"功能称为主机 Web 功能。将通过同一方式将外接程序部件部署到主机 Web 中。主机 Web 功能包含标准 SharePoint 2013 feature.xml 文件和一个或多个关联的 elements.xml 文件。例如，自定义操作的 elements.xml 文件包含此自定义操作的 **CustomAction** 标记。它还可包含外接程序部件的标记。仅这两种组件可位于主机 Web 功能中。这些主机 Web 功能未在外接程序清单中详细记录。但是，它们是 OPC 感知中的"部件"，并且外接程序清单和其中每个文件之间存在显式 OPC 关系。有关包含主机 Web 功能的外接程序包的示例，请参阅 [创建自定义操作以部署 SharePoint 外接程序](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)。
    
    > **注释**
      > 租户管理员可选择将 SharePoint 外接程序批安装到多个网站。已通过此方式安装的外接程序的范围可能为 **Tenant**。如果未批安装此外接程序，而是安装到单独的网站，则外接程序的范围可能为 **Web**。如果主机 Web 功能包含功能区扩展或外接程序部件，则不会在批安装外接程序时将其部署到主机 Web，以便仅部署具有租户范围的外接程序的快捷菜单项。不要将外接程序范围与功能范围混淆。功能范围确定功能中元素的部署位置。可能的位置为 **Farm**、 **WebApplication**、 **Site**（即，网站集）和 **Web**。SharePoint 外接程序中的功能（主机 Web 功能和外接程序包的 .wsp 中的功能）仅允许使用 **Web** 选项。外接程序范围引用在其中安装外接程序的范围。可能的位置为 **Web**（其中已将外接程序点对点安装到一个或多个网站）和 **Tenant**（其中已将外接程序批安装到客户租赁的网站的所有或部分子集）。有关 **Tenant** 和 **Web** 范围的详细信息，请参阅 [SharePoint 外接程序的租赁和部署范围](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。 
- **本地化资源文件 (.resx)：**这些文件适用于本地化包含外接程序标题的外接程序清单的各个方面和外接程序包中的主机 Web 功能的各个方面。（位于其自己的包内的外接程序包的各个部分，如 .wsp 文件、Azure 网站包和外接程序清单，其中每个都有自己的本地化过程，这些过程将在相关项不是 SharePoint 外接程序的一部分而这些包是时完全应用。）有关包含 .resx 文件的主机 Web 功能的外接程序包的示例，请参阅 [本地化 SharePoint 外接程序](localize-sharepoint-add-ins.md)。
    
  
- **Office 外接程序清单：**（可选）可能存在一个或多个 Office 外接程序清单，其中每个包对应一个 Office 外接程序。此部分仅当外接程序将上载到 SharePoint 2013 企业外接程序目录而不是公共市场时才包含在外接程序包中。有关详细信息，请参阅 [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)。
    
  

## SharePoint 相关外接程序清单文件
<a name="AppManifest"> </a>

每个 SharePoint 外接程序都包含一个 appmanifest.xml 文件。appmanifest.xml 告知 SharePoint 其必须知道的有关外接程序的内容并且定义外接程序最重要的属性。以下是在清单中指定的一些项：
  
    
    

- 外接程序的内部名称、产品 ID 和版本。
    
  
- 起始页的 URL，该页是在启动外接程序时打开的页。这可以是外接程序 Web 中的页、基于云的页或 ISV 的 Web 服务器上的页。
    
    > **注释**
      > 在某些情况下，可能对可以在 **StartPage** 元素中指定哪种类型的文件有限制。有关详细信息，请参阅 [起始页元素 (PropertiesDefinition 复杂类型) (SharePoint 接清单)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)。 > 将多个查询参数组合到 **StartPage** 值中时，您必须使用编码的与号" `&amp;amp;`"而不是" `&amp;`"或分号将其追加在一起。 
- 外接程序的其他属性。这些属性包括标题和外接程序支持的区域设置（这二者是必需的）、处理安装后、升级后和卸载前事件的服务的 URL 以及创建外接程序 Web 时要使用的 Web 模板。
    
  
- 外接程序对外接程序 Web 之外的 SharePoint 资源所需权限的请求。
    
  
- 标识，供外接程序主体的身份验证和授权之用。它是被授予权限的主体。Sharepoint 托管的外接程序无需此标识。
    
  
- 必须可供外接程序使用才能安装外接程序的必备组件（如果有）的列表。例如，可能需要安装并激活某些功能，并且可能需要许可并安装某些服务。
    
  

> **注释**
> 外接程序清单文件是外接程序包中唯一必需的项，但之前列表中的项并不都是文件的必需部分。 
  
    
    

有关外接程序清单标记的详细信息，请参阅节点 [对于清单的 SharePoint 加载架构参考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)。本主题不是该节点中信息的替代信息，该节点包括有关必需元素和属性的信息。另外，请注意 SharePoint 外接程序清单具有不同于 Office 外接程序清单的架构。您可以在  [Office 外接程序清单的架构参考 (v1.1)](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx)中查找有关后者的信息。
  
    
    
以下是 appmanifest.xml 文件的示例。请注意，在此示例中，外接程序的起始页是位于远程服务器上的 ASP.NET 页，而不是 SharePoint 网站上的页。此页的 URL 包含一个查询字符串，该字符串将主机 Web 的 URL 传递到远程 Web 应用程序。该字符串的"{HostUrl}"部分是启动外接程序时解析的令牌。外接程序将请求对主机 Web 中所有列表的写入权限。必须为其授予此权限的外接程序主体是远程 Web 应用程序。
  
    
    
您必须在外接程序清单中使用 **SupportedLocales** 或 **SupportedLanguages** 元素。将弃用 **SupportedLanguages** 以支持 **SupportedLocales**。甚至在发布后， **SupportedLanguages** 元素将继续可用，但您应避免使用它。有关这些元素的详细信息，请参阅 [SupportedLocales 元素 (PropertiesDefinition 复杂类型) (SharePoint 接清单)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx)和  [SupportedLanguages 元素 (PropertiesDefinition 复杂类型) (SharePoint 接清单)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx)。 。
  
    
    

> **注释**
> **AppPermissionRequest** 元素的 **Scope** 属性的值的结构类似 URI，但它们实际是文本字符串。以下示例中的示例 **Scope** 值中没有任何部分是占位符。有关权限的详细信息，请参阅 [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)。 
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### 外接程序清单中的 URL 标记

SharePoint 2013 提供了若干可在外接程序的 **StartPage** 元素和其他位置以及外接程序的组件中使用的标记，用于表示外接程序运行前不清楚的信息。SharePoint 2013 基础结构将解析这些标记。部分用于 URL 的开头，而其他的在 URL 中使用，如用于查询参数的值。这些标记和若干其他标记还可在各种 SharePoint 2013 开发上下文中使用。有关所有标记以及可使用它们的位置的详细信息，请参阅 [SharePoint 外接程序中的 URL 字符串和标记](url-strings-and-tokens-in-sharepoint-add-ins.md)。有关 SharePoint 2013 中的其他标记和 URL 的常规信息，请参阅  [SharePoint 2013 中的 URL 和标记](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)。
  
    
    

> **注释**
> 这些标记不会在 **AppPermissionRequest** 元素的 **Scope** 属性中使用。
  
    
    


## 其他资源
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序体系结构的重要方面和开发前景](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [对于清单的 SharePoint 加载架构参考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [开放打包约定 (OPC)](http://msdn.microsoft.com/zh-cn/magazine/cc163372.aspx)
    
  
-  [数据层应用程序连接 (DAC)](http://msdn.microsoft.com/zh-cn/library/ee210546.aspx)
    
  
-  [Web Deploy 2.0](http://www.iis.net/downloads/microsoft/web-deploy)
    
  

