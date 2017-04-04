---
title: 更新 SharePoint 外接程序中的远程组件
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# 更新 SharePoint 外接程序中的远程组件
更新 SharePoint 外接程序的远程 Web 应用程序和数据库。
## 更新 SharePoint 外接程序远程组件的先决条件
<a name="Prerequistes"> </a>

了解 [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)和先决条件，以及其中列出的核心概念。
  
    
    

## 更新远程组件
<a name="UpdateRemote"> </a>

大多数情况下，对于更新远程组件只能提供非常通用的建议，因为各平台和租赁系统之间存在很大差异。以下两部分提供了一些指导。
  
    
    

### 更新提供程序承载的外接程序中的远程组件
<a name="UpdateProviderHosted"> </a>

对于提供程序托管的 SharePoint 外接程序，您可以使用远程组件托管平台的最佳更新做法来更新该远程组件。提供程序托管的外接程序的远程组件与 SharePoint 外接程序本身是分别安装的，同样，它们也分别进行更新。需要考虑以下几点：
  
    
    

- 更新的远程组件应继续与 SharePoint 外接程序所有的早期版本兼容。
    
  
- 如果对远程组件实施了租赁隔离系统，请牢记不同的租户可能使用该外接程序的不同版本，甚至某特定租户还可能在多个 SharePoint 网站上安装了多个版本。
    
  
对于使用 Microsoft Azure SQL 数据库 或 SQL Server 的提供商托管的 SharePoint 外接程序，有多种更新数据库的方法。若要开始，请参阅 [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx)。
  
    
    

## 后续步骤
<a name="Next"> </a>

返回到 [更新外接程序的主要步骤](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接转到以下文章之一，以了解如何更新 SharePoint 外接程序的下一主要组件。
  
    
    

-  [更新 SharePoint 2013 中的外接程序 Web 组件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [更新 SharePoint 2013 中的主机 Web 组件](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [在 SharePoint 外接程序中创建更新事件的处理程序](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  

## 其他资源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序更新过程](sharepoint-add-ins-update-process.md)
    
  

