---
title: 部署及安裝 SharePoint 增益集： 方法與選項
ms.prod: SHAREPOINT
ms.assetid: d15a74a7-3c10-485a-9885-7ef11aaa0d90
---


# 部署及安裝 SharePoint 增益集： 方法與選項
了解發佈、 安裝及解除安裝SharePoint Add-in方法。
## 發佈到Office Store或組織的增益集型錄
<a name="MarketOrCatalog"> </a>

您可以上傳您SharePoint Add-in公用Office Store或組織的私人增益集目錄。私人的增益集目錄是主控文件庫SharePoint Add-ins和Office 增益集以專用的網站集合中SharePoint 2013 web 應用程式 (或SharePoint Online租用)。放入其本身的網站集合的目錄會限制目錄的權限的 web 應用程式管理員或租用戶系統管理員更容易。
  
    
    
如果增益集上傳到Office Store、 Microsoft執行其一些驗證檢查。例如，它會檢查增益集資訊清單標記是否有效且完成並驗證包括任何 SharePoint 方案套件 (.wsp 檔) 未加上不允許的元素或功能更廣泛比 **Web**範圍。不當內容也檢查套件的內容。如果增益集通過所有測試、 增益集套件會換行至檔案和簽署的Microsoft。
  
    
    

> [!注意事項]
> 當您正在您的增益集開發和部署搭配Microsoft Office Developer Tools for Visual Studio時、 增益集是直接安裝目標測試 SharePoint 網站中。由於未通過Office Store、 前文所述的驗證檢查不會發生。
  
    
    

將SharePoint Add-in上傳至組織的增益集目錄輕而易舉將任何檔案上傳至SharePoint Foundation文件庫。您填寫快顯表單您提供本機增益集套件和其他資訊，例如增益集的名稱的 URL。當增益集已上傳至組織的增益集目錄、 類似檢查進行而且沒有傳遞任何的增益集標示為無效或已停用目錄中。
  
    
    
承租人管理員及SharePoint 2013 web 應用程式管理員可以購買SharePoint Add-insOffice Store上。若要開啟Office Store，系統管理員選取 [ **網站內容**」 頁面上的 [ **新增增益集**，，然後選取 [ **您的增益集**] 頁面中的 [ **SharePoint 存放區**。如此會開啟系統管理員可以使用可探索，並了解SharePoint Add-ins廠商所提供的 **SharePoint 存放區**] 頁面。(他們可以也進行此作業 office.com。)增益集需要系統管理員的 web 應用程式或租用未安裝的必要條件會呈暗灰色及增益集存放區中已無法使用。例如，如果增益集需要搜尋 Services，不會安裝此增益集呈現暗灰色。系統管理員可以排序、 篩選和瀏覽的增益集清單、 閱讀增益集的相關資訊、 增益集檢閱 (英文)，請參閱 ＜ 和購買授權的增益集。
  
    
    
當系統管理員決定購買授權時，她必須接受條款和條件購買並同意至增益集必須具備以執行，例如 read access 至清單或完全控制存取權的網站集合的權限。
  
    
    
當已購買的增益集的一或多個授權時，授權下載至 web 應用程式或租用。增益集是不會自動下載並安裝時已購買授權，但系統管理員有組合授權購買與安裝選項。
  
    
    
使用者從 **您的增益集**] 頁面上安裝的增益集。此頁面已合併的下列清單：
  
    
    

- 從 web 應用程式 (或租用戶) 組織增益集目錄SharePoint Add-ins 。
    
  
- 從其組織或租用戶尚未擁有網站授權或其已指派給使用者的授權Office StoreSharePoint Add-ins 。
    
  
列出所有增益集的使用者便可以立即安裝，並列出使用者便可以立即安裝僅增益集有幾個例外。使用者可以當做篩選僅增益集包括組織的增益集目錄中的頁面中包含之增益集。增益集在安裝時，它會出現在網站的 [ **網站內容**] 頁面上的增益集清單夠安裝。
  
    
    

## 安裝 SharePoint 增益集
<a name="Installing"> </a>

網站擁有者從 **您的增益集**] 頁面上安裝SharePoint Add-ins本主題先前所述。安裝建立增益集的執行個體。如需安裝的增益集的詳細資訊，請參閱 [新增 SharePoint 增益集至 SharePoint 2013 網站](https://technet.microsoft.com/en-us/library/fp161231.aspx)。
  
    
    

> [!注意事項]
> 有時暫時遺失網路連線可以封鎖安裝。如果安裝任何原因而失敗的安裝基礎結構會重試三倍。如果不成功，失敗的指示會出現在 UI 中。使用者可以安裝稍後再試一次。
  
    
    


## 解除安裝 SharePoint 增益集
<a name="Uninstalling"> </a>

網站擁有人可以解除安裝 SharePoint 使用者介面透過SharePoint Add-in執行個體。解除安裝的執行個體SharePoint Add-ins是全新。這表示安裝的增益集的每個項目會解除安裝。
  
    
    
不過，增益集使用，但分開安裝從安裝的增益集] 中的元件不會移除。例如，假設增益集具有遠端網頁與主機網路建立清單的按鈕。解除安裝的增益集磚從 **網站內容**] 頁面上的接著會遠端頁面實際上無法存取或無法使用的使用者，但它不會移除已建立與增益集 SharePoint 清單的增益集 」 會移除不會保留與增益集所建立的主機網站上的清單與 SharePoint 使用者介面中的使用者建立哪些記錄讓它不能刪除與增益集所建立的清單。這是一般所需的行為因為清單可能會有即使之後的增益集建立清單移除仍留使用者很有用的資料。
  
    
    
如果SharePoint Add-in包含增益集網頁，請刪除增益集 web。此提供更乾淨比詢問停用功能解除安裝後再回復部署的增益集的內部.wsp 檔案。
  
    
    

> [!注意事項]
> 當使用者移除增益集時，它會移至第一階段資源回收筒。刪除從該處移至第二階段資源回收筒。如果它從第二階段資源回收筒中刪除，然後完全解除安裝並無法還原。
  
    
    

增益集的權限也會撤銷移除 (回收) 時這些規則：
  
    
    

- 一律會撤銷網站範圍設定的權限。
    
  
- 增益集在網站集合中的任何其他執行個體時，也會撤銷網站集合範圍設定的權限。
    
  
- 如果沒有任何其他執行個體 (租用) 的網站訂閱的增益集，是也會撤銷租用戶範圍設定的權限。
    
  
 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) web 服務，如有一個已登錄的增益集、 增益集資訊清單中執行之解除安裝程序 (的增益集已從第二階段資源回收筒時發生) 的開頭。它是如果您有 [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) web 服務具備 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) web 服務並設計反向完成 [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx)服務中的任何項目 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx)服務的最佳作法。 如需詳細資訊，請參閱 [處理事件中 SharePoint 增益集](handle-events-in-sharepoint-add-ins.md)。
  
    
    

## 其他資源
<a name="SP15deployinstallapps_addlresources"> </a>


-  [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [SharePoint 增益集更新程序](sharepoint-add-ins-update-process.md)
    
  

