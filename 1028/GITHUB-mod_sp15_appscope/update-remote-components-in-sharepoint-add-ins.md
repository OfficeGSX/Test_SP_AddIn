---
title: 更新遠端元件的 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# 更新遠端元件的 SharePoint 增益集
更新的遠端 web 應用程式和SharePoint Add-in的資料庫。
## 更新的 SharePoint 增益集的遠端元件的必要條件
<a name="Prerequistes"> </a>

先熟悉 [更新 SharePoint 增益集](update-sharepoint-add-ins.md)的先決條件和中其所列的核心概念。
  
    
    

## 更新遠端元件
<a name="UpdateRemote"> </a>

因為平台和租用系統中的整體差異更新遠端元件，即可提供大致上來說、 僅限非常一般建議。下列兩個章節提供一些指引。
  
    
    

### 更新遠端元件提供者主控增益集
<a name="UpdateProviderHosted"> </a>

提供者主控SharePoint Add-in，您會更新使用更新的作法平台上所主控之元件的遠端元件。就如同從SharePoint Add-in本身安裝會分別安裝提供者主控增益集的遠端元件，這些也會更新分開。考慮一些點：
  
    
    

- 更新的遠端元件應繼續處理與所有舊版SharePoint Add-in。
    
  
- 如果您在遠端元件的實作租用隔離系統，請記住不同的租用戶可能會使用多個版本的增益集，並在特定的承租人甚至可能必須安裝在各種 SharePoint 網站上的不同版本。
    
  
對於提供者主控SharePoint Add-in使用Microsoft Azure SQL Database或SQL Server，有多種方法來更新資料庫。若要開始，請參閱 [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx)。
  
    
    

## 後續步驟
<a name="Next"> </a>

返回 [在增益集更新的主要步驟](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接移至其中的下列文章，以了解如何更新您SharePoint Add-in的下一個主要元件。
  
    
    

-  [更新 SharePoint 2013 中的增益集 web 元件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [更新 SharePoint 2013 中的主機 web 元件](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [建立更新事件處理常式中 SharePoint 增益集](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  

## 其他資源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 增益集](update-sharepoint-add-ins.md)
    
  
-  [SharePoint 增益集更新程序](sharepoint-add-ins-update-process.md)
    
  

