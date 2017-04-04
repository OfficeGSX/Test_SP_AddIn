---
title: 發佈 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# 發佈 SharePoint 增益集
尋找資源以協助您發佈您SharePoint Add-ins及文章。
## 開始使用發佈您的增益集
<a name="bk_gettingstarted"> </a>

您已完成開發您SharePoint Add-in 的最後一個步驟將該增益集提供給您的使用者。您可以為其中一個兩個地方發佈增益集：
  
    
    

- **公用Office Store。**Office Store讓增益集公開，使其可取得使用者的任何 SharePoint 部署至發佈您的增益集。
    
  
- **目錄增益集內部的組織。**內部的組織增益集目錄、 裝載於您的 SharePoint 部署，以讓使用者擁有存取權的 SharePoint 部署可供發佈您的增益集。
    
  
如需如何使用Visual Studio 2012封裝您的增益集的出版物，請參閱 [使用 Visual Studio 發佈 SharePoint 增益集](publish-sharepoint-add-ins-by-using-visual-studio.md)資訊。
  
    
    

### 發佈到Office Store

若要發佈增益集Office Store必須第一個 [登錄為 Microsoft 開發人員](https://sellerdashboard.microsoft.com/Registration)。
  
    
    
當您上載增益集Office Store出版物時，Microsoft 會執行一系列的驗證檢查以確保您的增益集依循的增益集的內容和行為準則。例如，它會檢查增益集資訊清單標記是否有效且完成並驗證的任何都包含在增益集的 SharePoint 方案套件 (.wsp 檔案) 不包含不允許的元素或 SharePoint 功能會比 Web 更廣泛的範圍。封裝 (英文) 也檢查不當的內容。如果增益集套件通過所有測試，已換行至檔案，並由 Microsoft 簽署。
  
    
    
當您上傳您的增益集在Office Store出版物時，您可以選擇您想要提供使用者他們下載時的授權條款。使用此增益集授權來決定：
  
    
    

- 是否您的增益集免費、 試用、 或購買提供。
    
  
- 是否增益集可以取得每個使用者或站台的基礎。
    
  
SharePoint 未強制增益集使用狀況的授權條款  提供可讓您在增益集來強制執行您選擇列傳授權限制包括程式碼邏輯的授權架構。例如，您可以包含程式碼邏輯您增益集可讓使用者是否具有付費的授權，但不是如果有試用授權存取特定的增益集功能。如需詳細資訊，請參閱 [授權您的 Office 和 SharePoint 增益集](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)。
  
    
    

### 發佈到增益集類別目錄

如果您正在建立您自己的公司使用或特定公司用戶端，而不是一般大眾， SharePoint Add-ins將可能想要發佈增益集內部增益集目錄架設在 SharePoint 上。私人的增益集目錄是主控文件庫SharePoint Add-ins和Office 增益集以專用的網站集合中SharePoint 2013 web 應用程式 (或SharePoint Online租用)。放入其本身的網站集合的目錄會限制目錄的權限的 web 應用程式管理員或租用戶系統管理員更容易。
  
    
    
將SharePoint Add-in上傳至公司的增益集目錄輕而易舉將任何檔案上傳至 SharePoint 文件庫。您填寫快顯表單您提供本機增益集套件和其他資訊，例如增益集的名稱的 URL。當增益集上載至增益集目錄時，有如下檢查並沒有傳遞任何的增益集標示為無效或已停用目錄中。
  
    
    

## 決定要發佈您SharePoint Add-in
<a name="bk_decide"> </a>

發佈至Office Store或增益集 catalog 及清單的功能比較問題時要考慮下表提供決定要發佈您的增益集。我們建議您決定要之前設計及開發它 ； 發佈增益集在某些情況下，例如授權、 其中您發佈增益集將會影響設計及開發的增益集。
  
    
    

**表 1。要發佈增益集的考量**


|**Office Store**|**增益集目錄**|
|:-----|:-----|
|增益集公開使用。 <br/> |增益集可供此 SharePoint 部署的存取權的使用者 <br/> |
|可用的授權架構。 <br/> |無法使用授權的架構。 <br/> |
|增益集套件驗證 microsoft 技術和內容嚴格該原則。 <br/> |當增益集已上傳 SharePoint 所執行的增益集套件驗證。 <br/> |
|您必須被註冊 Microsoft 賣方儀表板上傳的增益集。 <br/> |與 Microsoft 所需未註冊。 <br/> |
   

## 其他資源
<a name="bk_addresources"> </a>


-  [建立或更新用戶端識別碼和賣方儀表板中的機密資料](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)
    
  
-  [若要送出到 Office 市集的 Office 與 SharePoint 增益集和 Office 365 應用程式使用賣方儀表板](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)
    
  
-  [驗證原則的應用程式和增益集的送出到 Office 市集 （2.1 版）](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)
    
  
-  [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx)
    
  
-  [授權您的 Office 和 SharePoint 增益集](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)
    
  
-  [部署及安裝 SharePoint 增益集： 方法與選項](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [租用和部署範圍的 SharePoint 增益集](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  
-  [使用 Visual Studio 發佈 SharePoint 增益集](publish-sharepoint-add-ins-by-using-visual-studio.md)
    
  
-  [發佈 Office 增益集存放區](http://social.msdn.microsoft.com/Forums/en-US/officestore)
    
  

