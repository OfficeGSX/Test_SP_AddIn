---
title: 更新 SharePoint 2013 中的主机 Web 组件
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# 更新 SharePoint 2013 中的主机 Web 组件
在 SharePoint 外接程序的主机 Web 中更新外接程序部件和自定义操作。
## 更新主机 Web 组件的先决条件
<a name="Prerequisites"> </a>

了解 [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)和先决条件，以及其中列出的核心概念。
  
    
    

## 更新主机 Web 组件
<a name="UpdateHostWeb"> </a>

您的外接程序可以在 SharePoint 外接程序中有描述性标记的主机 Web 上安装两种组件：自定义操作和外接程序部件。在主机 Web 中更新这些组件比在外接程序 Web 中更加简单。不需要使用任何更新语义。只需添加/更改自定义操作和外接程序部件。更新 SharePoint 外接程序时，SharePoint 会始终使用最新版本应用任何新元素清单文件，并重新应用任何已更改的元素清单文件。重新应用过程中不会带来破坏；例如，自定义操作的功能区按钮不会多次添加到功能区。
  
    
    
更新外接程序部件时，SharePoint 用 Web 部件库中的新版本替换旧版本。更新外接程序部件时，请确保更改 **ClientWebPart** 对象的 **Name** 属性。这样可以确保在更新外接程序时，SharePoint 从添加了部件的所有页面中删除外接程序部件的旧版本（已不再是此应用的一部分）。用户需要将新版本重新添加到页面中。
  
    
    
如果保持 **Name** 属性不变，旧版本会在原来添加的页面中保留，这样用户便无法知道有外接程序部件的新版本可用。此外，当外接程序部件被添加到其他页面中时，添加的就是新版本，所以同一版本的 SharePoint 外接程序在不同页面中将拥有不同的外接程序部件。
  
    
    
可以使用在包含  [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) 元素的外接程序清单中注册的远程事件接收器，以编程方式部署其他类型的主机 Web 组件。应该使用 [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) 接收器更新最初使用 **InstalledEventEndpoint** 接收器部署的组件。在 [在 SharePoint 外接程序中创建更新事件的处理程序](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)中介绍了  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) 接收器。
  
    
    

## 后续步骤
<a name="Next"> </a>

转到 [更新外接程序的主要步骤](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接转到以下文章之一，以了解如何更新 SharePoint 外接程序的下一主要组件。
  
    
    

-  [更新 SharePoint 2013 中的外接程序 Web 组件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [在 SharePoint 外接程序中创建更新事件的处理程序](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [更新 SharePoint 外接程序中的远程组件](update-remote-components-in-sharepoint-add-ins.md)
    
  

## 其他资源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 中的主机 Web、外接程序 Web 和 SharePoint 组件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

