---
title: 获取 SharePoint 对象模型的快速概述
ms.prod: SHAREPOINT
ms.assetid: 6b8b55f8-9370-43a0-af8d-e07d1028a075
---


# 获取 SharePoint 对象模型的快速概述
获取对 SharePoint 对象模型中的某些主要类的快速简介。
这是关于开发 SharePoint 托管的 SharePoint 外接程序的基础知识系列文章中的第四篇文章。您应该首先熟悉  [SharePoint 外接程序](sharepoint-add-ins.md)以及本系列中之前的文章： 
  
    
    


-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [使提供程序托管的外接程序具有 SharePoint 的外观](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [在提供程序托管的外接程序中包含自定义按钮](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  

> **注释**
> 如果您阅读过关于提供程序托管的外接程序的系列文章，那么您应该具有 Visual Studio 解决方案，可以继续阅读本主题。您还可以从  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) 下载存储库并打开 BeforeSharePointWriteOps.sln 文件。
  
    
    

在本文中，我们将暂时停止讲述编码，而是简要介绍一下 SharePoint 客户端对象模型 (CSOM)。该模型非常大并在 MSDN 中进行了详细记录，包括参考主题、操作说明和代码示例。在本文中，我们仅提供了冰山一角。但即使是非常简短的介绍也能使您在本系列中看到的代码变得不那么神秘。 
## 内容层次结构

下表显示了 SharePoint 中的内容层次结构以及表示它们的 CSOM 类。每个实体下方都有类型的子级。
  
    
    

|
|
|**实体**|**类**|**注解**|
|:-----|:-----|:-----|
|SharePoint 内部部署服务器场或 SharePoint Online 订阅（也称为租户）  <br/> ||仅可对 CSOM 中的此级别进行有限的编程访问。例如，没有服务器场或订阅或租户类。（SharePoint 的服务器端对象模型不能用于外接程序中，它可启用对这些实体的编程访问。）  <br/> |
|网站集  <br/> |**Site** <br/> |主要出于管理原因分组在一起的网站集合，用于承载品牌母版页或自定义安全组等 SharePoint 组件，可应用于所有子网站。所有网站均属于某个网站集。  <br/> |
|网站  <br/> |**Web** <br/> |一组页面和 SharePoint 组件。可以有子网站。  <br/> |
|列表  <br/> |**List** <br/> |文档库和其他类型的库也属于此级别。  <br/> 文档库是一种特殊类型的列表，其中每行均包含一个附加文档，其他列则包含关于文档的数据，例如其作者、上次编辑时间以及签出者。  <br/> |
|列表项  <br/> |**ListItem** <br/> |列表中的一行即列表项，它在行的字段中具有特定值。也具有类型。请参阅下一行。  <br/> |
|列表项  <br/> |**Content Type** <br/> |列表项的类型。由 **ContentType** 类表示。每个列表项基本上都是一组列和元数据。最简单的是内置 **Item** 内容类型。所有其他内容类型均派生自 **Item**。SharePoint 包含许多内置的内容类型，如事件和通知。  <br/> |
|列  <br/> |**Field** <br/> |**Field** 对象不仅包括与基础数据类型有关的信息，还包括有关数据在表单上如何格式化和呈现的信息，例如用于创建、显示和编辑特定列表项的表单。 <br/> |
   

  
    
    
您可以编程方式创建自定义列表、内容类型、列类型和列表项。 
  
    
    
除了内容之外，CSOM 还允许您访问用户、组、角色和权限、分类、搜索等等。
  
    
    

## 客户端运行时和批处理
<a name="CSOMBatching"> </a>

CSOM 使用批处理系统。托管代码块将转换为 XML 并在单个 HTTP 请求中发送到服务器。对于每个命令，将进行相应的服务器对象模型调用，服务器将以 JavaScript 对象表示法 (JSON) 格式将响应返回到客户端。 
  
    
    
客户端上的 SharePoint 代码首先检索客户端上下文对象，该对象表示当前请求上下文，包括 SharePoint 网站（及其父网站集）的标识，通过此上下文，您可以获取对 CSOM 对象的访问权限。下面是基础结构，您可能需要反复查看。对于此代码，请注意以下事项。
  
    
    

-  `spContext` 对象的类型为 **SharePointContext**，它在 Visual Studio Office 开发人员工具 生成的 SharePointContext.cs（或 .vb）文件中定义。该文件可以修改，但不需要经常修改。对于大多数 SharePoint 外接程序 项目，此文件以及也由工具生成的 TokenHelper.cs（或 .vb）文件可作为 CSOM 本身的扩展有效运行。
    
  
-  `clientContext` 对象是 CSOM 类型 **ClientContext**。
    
  
- **ExecuteQuery** 方法将 SharePoint 服务器中的 CRUD 操作代码和发送到 SharePoint 服务器的 XML 消息捆绑在一起。它会转换为等效的服务器端对象模型代码并执行。
    
  



```cs

using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    // CRUD operation or query code goes here.

    clientContext.ExecuteQuery();
}
```

本系列的前一篇文章中有此模式的示例，如下面所示的  `GetLocalEmployeeName` 方法。对于此方法，请注意以下事项。
  
    
    

- **using** 块中的前两行似乎会获取对列表和列表项对象的引用。但实际上，当这些行在 SharePoint 客户端运行时中执行时，它们只会转换为 XML 格式。 **ExecuteQuery** 方法会将该 XML 发送到服务器。
    
  
- **Load** 方法将一些额外的内容添加到消息中：它告诉服务器将指定对象发送到客户端。 **ExecuteQuery** 方法接收此对象（作为 JSON）并使用它来初始化客户端 `selectedLocalEmployee` 变量。后续客户端代码将刷新该 **ListItem** 对象及其成员。正如您所看到的，下一行将引用项目的"Title"字段的值。如果由于对象实际上在客户端并不存在，导致 **Load** 方法尚未被调用，该行将抛出异常，直至对象加载。
    
  



```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
```


## 
<a name="Nextsteps"> </a>

 在下一篇文章中，我们将继续讲述编码并了解如何将数据写入 SharePoint： [将 SharePoint 写入操作添加到提供程序托管的外接程序](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
  
    
    

