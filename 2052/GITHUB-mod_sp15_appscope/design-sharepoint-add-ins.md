---
title: 设计 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# 设计 SharePoint 外接程序
大致了解 SharePoint 外接程序中可用的设计和体系结构选项，并学习如何做出正确的决策以便在 SharePoint 2013 中轻松开发您的外接程序。
假设您对于外接程序有绝佳的创意。在此部分，我们将指导您完成需要进行的设计决策，并提供生成外接程序的最佳实践。例如，良好的用户界面由什么组成？什么是可用的外接程序"形状"？使用时该如何选择？我有哪些数据访问选项？ 
  
    
    


## 开始设计 SharePoint 外接程序
<a name="SP15Design_Startdesigning"> </a>

由于 SharePoint 2013 中的云外接程序模型提供了很多设计选项，SharePoint 外接程序可以有诸多类型和大小。本节提供的有用指导可以帮助您在计划和设计外接程序的体系结构和用户体验时做出最重要的决定，包括外接程序的托管方式、外接程序的效率和数据访问安全，以及用户体验。
  
    
    
有关可用于 SharePoint 外接程序的设计和体系结构选项的概述，请参阅 [考虑 SharePoint 外接程序设计选项的三种方法](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)。有关 SharePoint 外接程序的概述，请参阅  [SharePoint 外接程序](sharepoint-add-ins.md)。
  
    
    

## 为外接程序选择正确的托管模型
<a name="SP15Design_Hostingmodel"> </a>

SharePoint 外接程序支持多个托管选项。您可以选择自己的 Web 堆栈，让 Microsoft 提供 Microsoft Azure 和 SQL Azure，或在 SharePoint 上托管外接程序。表 1 包含可帮助您为外接程序选择正确的托管模型的资源。
  
    
    

**表 1. 为 SharePoint 外接程序选择正确的承载模型的资源和指导**


|**文章**|**说明**|
|:-----|:-----|
| [为开发和托管 SharePoint 外接程序选择模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |了解您可以承载 SharePoint 外接程序组件的不同方式。  <br/> |
   

## 为外接程序选择正确的数据访问技术
<a name="SP15Design_Dataaccess"> </a>

您必须确保您的外接程序能够高效、安全地访问数据。可使用各项数据访问技术来访问 SharePoint 和处理您的外接程序中的数据。表 2 提供的资源可帮助您了解各选项并选择最适合您的外接程序的选项。 
  
    
    

**表 2. 选择要在 SharePoint 外接程序中使用的数据访问技术的资源和指导**


|**文章**|**说明**|
|:-----|:-----|
| [SharePoint 外接程序的安全数据访问和客户端对象模型](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> | 了解在构建 SharePoint 外接程序时可用的数据访问选项，包括入站和出站数据方案的数据连接性选项，以及从您的外接程序访问 SharePoint 数据时可用的 API。 <br/> |
   

## 为外接程序设计 UX
<a name="SP15Design_UX"> </a>

设计您的外接程序时，您的真正目标应该是创造一种体验，使用户能够完成您打算让他们完成的方案。通过表 3 中提供的资源和设计指导，您可以构建出色的外接程序，使其符合用户体验设计的最佳做法且具有 SharePoint 2013 的熟悉的外观和行为。
  
    
    

**表 3. 为 SharePoint 外接程序设计出色的用户体验的资源和指导**


|**文章**|**说明**|
|:-----|:-----|
| [SharePoint 外接程序的 UX 设计](ux-design-for-sharepoint-add-ins.md) <br/> |了解在构建 SharePoint 外接程序时可用的用户体验选项。  <br/> |
   

## 设计时考虑更新
<a name="Upgrade"> </a>

某天您可能希望生成外接程序更新，并将它上载到 Office 商店或组织的外接程序目录中。如果您在设计第一个版本时即考虑更新外接程序的方式，该任务将更加容易完成。我们建议您在设计初期阅读下面的文章： [SharePoint 外接程序更新过程](sharepoint-add-ins-update-process.md)和 [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)。 
  
    
    

## 后续步骤：开发并发布外接程序
<a name="SP15Design_Next"> </a>

扎实地完成了外接程序设计了吗？准备好生成您的外接程序并将其发布。表 4 中提供的资源可以帮助您开始。
  
    
    

**表 4. 开发和发布 SharePoint 外接程序的资源和指导**


|**文章**|**说明**|
|:-----|:-----|
| [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md) <br/> |讨论外接程序模型的高级概念和功能。  <br/> |
| [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md) <br/> |描述发布 SharePoint 外接程序的过程和要求。  <br/> |
   

## 其他资源
<a name="SP15Design_AddRes"> </a>


-  [SharePoint 外接程序示例包](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
-  [重新构思 SharePoint 开发](http://msdn.microsoft.com/zh-cn/office/apps/dn133840)
    
  
-  [SharePoint 外接程序](sharepoint-add-ins.md)
    
  
-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)
    
  
-  [外接程序的博客](http://blogs.msdn.com/b/spoffapps)
    
  

