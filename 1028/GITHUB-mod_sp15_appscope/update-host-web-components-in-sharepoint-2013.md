---
title: 更新 SharePoint 2013 中的主機 web 元件
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# 更新 SharePoint 2013 中的主機 web 元件
更新增益集組件及自訂動作的主機網站中的SharePoint Add-in。
## 更新主機 web 元件的必要條件
<a name="Prerequisites"> </a>

先熟悉 [更新 SharePoint 增益集](update-sharepoint-add-ins.md)的先決條件和中其所列的核心概念。
  
    
    

## 更新主機 web 元件
<a name="UpdateHostWeb"> </a>

增益集可以在具有描述性的標記中SharePoint Add-in主機網頁上安裝元件的兩種：自訂動作和增益集組件。更新這些元件是在主機 web 比在增益集 web 更容易。您不需要任何更新語意。剛新增/變更自訂動作和增益集組件。當SharePoint Add-in ，也會更新 SharePoint 一律適用於任何新的元素資訊清單檔案並重新套用最新的版本與任何已變更的項目資訊清單檔案。沒有傷害中重新套用; 完成例如，自訂動作的功能區按鈕會新增多次的功能區中。
  
    
    
當您更新增益集組件時，SharePoint 會取代舊的版本與網頁組件庫中的新版本。請務必更新增益集組件時變更 **ClientWebPart**物件的 **Name**屬性。如此可確保增益集更新時，SharePoint 會移除在舊版本的增益集組件 (這是不會再應用程式的一部分) 它已新增的所有頁面。使用者需要重新新增至頁面的 [新的版本。
  
    
    
如果保留不變的 **Name**屬性，在舊版本停留在其中已新增、 頁面讓可能性的使用者將會注意可用增益集組件的新版本。此外，當增益集組件加入至其他頁面，即會新增新版本讓SharePoint Add-in相同版本會有不同增益集組件不同頁面上。
  
    
    
您可以部署其他種類的主控 web 元件以程式設計方式使用您的增益集資訊清單與 [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx)元素中登錄的遠端事件接收器。您應該使用 [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)接收器更新 **InstalledEventEndpoint**接收器原本已部署的元件。 [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)接收器 [建立更新事件處理常式中 SharePoint 增益集](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)所述。
  
    
    

## 後續步驟
<a name="Next"> </a>

移至 [ [在增益集更新的主要步驟](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接移至其中的下列文章，以了解如何更新您SharePoint Add-in的下一個主要元件。
  
    
    

-  [更新 SharePoint 2013 中的增益集 web 元件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [建立更新事件處理常式中 SharePoint 增益集](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [更新遠端元件的 SharePoint 增益集](update-remote-components-in-sharepoint-add-ins.md)
    
  

## 其他資源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 增益集](update-sharepoint-add-ins.md)
    
  
-  [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

