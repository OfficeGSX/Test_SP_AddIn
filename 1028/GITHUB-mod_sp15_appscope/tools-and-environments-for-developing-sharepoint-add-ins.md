---
title: 工具及開發 SharePoint 的增益集的環境
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# 工具及開發 SharePoint 的增益集的環境
了解可用於建立SharePoint Add-ins開發環境的選項。
有兩個SharePoint Add-ins的開發環境的基本模式：
  
    
    


- **測試及偵錯 SharePoint 網站是Office 365訂閱以SharePoint Online網站中。** 通常Visual Studio已安裝到本機電腦，但雲端式Visual Studio也是一個選項。
    
  
- **測試及偵錯 SharePoint 網站是已在內部部署、 一部伺服器的 SharePoint 伺服器陣列。** Visual Studio安裝在同一部電腦上。
    
  

請考慮下列事項：
  
    
    


- 可以部署至其中一個SharePoint Online或內部部署 SharePoint 伺服器陣列的環境類型不論您使用幾乎任何增益集建立。 一般規則是增益集無法部署至SharePoint Online也不能被開發與它。範例： 增益集需要 [完全控制 」 權限](add-in-permissions-in-sharepoint-2013.md)及增益集使用 [高信任授權系統](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)。
    
  
- 您可以開發和 SharePoint 主控提供者主控SharePoint Add-ins，在您使用哪些環境的型別無關。
    
  
- 您可以讓內部部署和SharePoint Online測試網站。
    
  
- 考慮過一切、 兩個選項是同樣也請容易設定。
    
  
 **若要建立SharePoint Online環境** 使用可用的開發、 SharePoint Online訂閱查看 [建立現有的 Office 365 訂閱開發人員網站](create-a-developer-site-on-an-existing-office-365-subscription.md)。 **若要建立的內部部署環境** ，請參閱 [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)。
> [!注意事項]
> 本主題僅並與開發SharePoint Add-ins的環境。如果您打算開發伺服器陣列解決方案，請參閱 [設定 SharePoint 2013 的一般開發環境](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx)。如果您想要執行這兩種類型的開發、 開頭後面的文章，並會看到 [  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)的其他步驟您需要開發SharePoint Add-ins。
  
    
    


## 其他工具資訊


-  [使用 Napa Office 365 開發工具建立基本 SharePoint 主控增益集 (英文)](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)
    
  
-  [在 Visual Studio 中建立 SharePoint 增益集](create-sharepoint-add-ins-in-visual-studio.md)
    
  

## 其他資源
<a name="bk_addresources"> </a>


-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  

