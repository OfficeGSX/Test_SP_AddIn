---
title: 开发 SharePoint 外接程序
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# 开发 SharePoint 外接程序
查找可帮助您将高级功能构建到 SharePoint 外接程序的深度文章和资源。
> **注释**
> 本文假设您熟悉文章  [SharePoint 外接程序](sharepoint-add-ins.md)以及文章所链接到的入门材料。 




在"开发"下方，我们提供以下内容帮助解释您可在 SharePoint 外接程序中执行的所有不同操作:
- 深层概述


- 操作方法文章


- 代码段


您将找到与以下内容相关的文章：
- 对列表执行创建、读取、更新和删除 (CRUD) 操作


- 如何生成 REST 查询并与新的 API 进行交互


- 如何配置以及何时配置 OAuth 以实现安全性


SharePoint 具有企业社交功能（如活动源和用户配置文件）以及企业内容管理功能、业务线 (LOB) 互操作性功能和网站设计功能，这些功能可以使您的外接程序与众不同。在 [添加 SharePoint 2013 功能](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx)中了解有关这些功能的详细信息。代码是关键，因此，请了解一下开发人员中心中的"示例"菜单。它是指向外接程序的代码示例的直接链接。在您设置开发环境后，您应检查其中的几个示例。如果您对我们的代码示例不感兴趣，请使用社区功能请求一个代码示例。我们将接受这些请求以及其他文档反馈，并将其融入我们对内容和示例的持续更新中。因此，请告诉我们您希望看到的内容！
## SharePoint 外接程序资源入门
<a name="bk_gettingstarted"> </a>

如果您要开始开发 SharePoint 外接程序，首先请阅读  [SharePoint 外接程序](sharepoint-add-ins.md)。该页将您指向可使您获得对不同类型的 SharePoint 外接程序的快速认知的重要文章。在进行更高级的 SharePoint 外接程序开发前，从要构建的外接程序种类、将要包含的技术以及将使用的托管选项开始是一个不错的主意。




### 使用客户端对象模型、JavaScript 对象模型和 REST 终结点在 SharePoint 2013 中开发 SharePoint 外接程序的基本任务和资源
<a name="bk_essentials"> </a>

不管您决定构建哪种类型的 SharePoint 外接程序，您的外接程序将始终通过某种方式与 SharePoint 2013 网站交互。表 1 中的文章介绍了如何使用可供您在 SharePoint 外接程序中使用的下列三个接口对 SharePoint 网站执行许多非常重要的工作：客户端对象模型、JavaScript 对象模型和 REST 终结点。




**表 1. 使用 SharePoint 2013 客户端对象模型、JavaScript 对象模型和 REST 接口执行的基本操作**


|**主题**|**说明**|
|:-----|:-----|
| [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |解释如何使用 C# 和客户端对象模型执行常见操作。  <br/> |
| [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |解释如何使用 JavaScript 对象模型执行常见操作。  <br/> |
| [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |解释如何使用 REST 接口执行常见操作。  <br/> |
 

## 了解开发 SharePoint 外接程序的基本概念
<a name="bk_fundamentals"> </a>

除了了解基本操作之外，您应了解 SharePoint 2013 外接程序开发模型的基本概念。每种 SharePoint 外接程序均包含一个外接程序清单文件并且内置在部署到 SharePoint 2013 网站的外接程序包中。当您开发任何类型的外接程序时，必须考虑与身份验证和授权、数据访问以及可用性相关的一系列问题。表 2 中的文章使您熟悉这些问题并解释了其针对您要创建的各种 的实现。




**表 2. 开发 SharePoint 外接程序的基本概念**


|**主题**|**说明**|
|:-----|:-----|
| [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |指导您熟悉与获取开发 SharePoint 2013 资源所需的权限相关的核心概念。  <br/> |
| [了解 SharePoint 外接程序的应用程序清单结构和包](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |解释外接程序清单的工作原理以及外接程序包的构建方式。  <br/> |
| [在 SharePoint 2013 中创建 UX 组件](create-ux-components-in-sharepoint-2013.md) <br/> |研究您可在 SharePoint 外接程序中构建丰富用户体验的各种方式。  <br/> |
| [在 SharePoint 2013 中处理外部数据](work-with-external-data-in-sharepoint-2013.md) <br/> |解释各种 SharePoint 外接程序中可用的数据访问选项和技术。  <br/> |
| [许可 Office 和 SharePoint 加载项](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |指导您熟悉 Office 和 SharePoint 外接程序的外接程序许可框架。  <br/> |
 

## 汇总：使用集成功能构建高级 SharePoint 外接程序
<a name="bk_integrate"> </a>

如果您熟悉 SharePoint 外接程序的各种功能和特征，则可通过满足您的要求的各种方式将所有功能放置在一起来开始构建更复杂的外接程序。表 3 中的文章介绍如何集成功能和创建功能更全面的 SharePoint 外接程序。




**表 3. SharePoint 外接程序中的高级概念**


|**主题**|**说明**|
|:-----|:-----|
| [创建包括自定义 SharePoint 列表和内容类型的提供商托管的外接程序](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |解释如何创建承载在云中并且包含自定义 SharePoint 列表和内容类型的 SharePoint 外接程序。  <br/> |
 

## 其他资源
<a name="bk_addresources"> </a>


-  [SharePoint 外接程序开发工具和环境](tools-and-environments-for-developing-sharepoint-add-ins.md)


-  [设计 SharePoint 外接程序](design-sharepoint-add-ins.md)


-  [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)


-  [SharePoint 外接程序示例包](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)



