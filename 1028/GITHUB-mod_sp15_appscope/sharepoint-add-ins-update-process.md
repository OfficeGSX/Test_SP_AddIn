---
title: SharePoint 增益集更新程序
ms.prod: SHAREPOINT
ms.assetid: 3dba209d-cb98-4e5d-b4b2-fad31e667ca1
---


# SharePoint 增益集更新程序
了解更新 SharePoint 增益集的程序。
您必須更新 SharePoint 增益集如果您要新增的功能、 修正錯誤或進行安全性更新。增益集來更新SharePoint Add-in套件中部署中部署的第一版的增益集的方式相同。SharePoint 增益集更新程序可確保增益集的資料會保留任何原因而失敗的更新時。
  
    
    


> [!重要]
> 您無法變更使用更新系統的 *增益集類型*  。例如，就無法變更增益集從 SharePoint 主控的更新裝載提供者。若要變更的類型時，您需要 [從舊增益集新增一個移轉](#Major)。特別自 [在關閉自動裝載的增益集的預覽程式](http://blogs.office.com/2014/05/16/update-on-autohosted-apps-preview-program/)，您應該注意您無法提供者主控增益集來更新自動裝載增益集。您必須將轉換增益集 [將自動裝載 SharePoint 增益集轉換成提供者主控增益集 (英文)](convert-an-autohosted-sharepoint-add-in-to-a-provider-hosted-add-in.md)所述。
  
    
    


## 更新程序的 SharePoint 增益集
<a name="Minor"> </a>

更新，您可以使用相同的產品識別碼在增益集資訊清單中所用的原始版本。增益集資訊清單中的版本號碼應大於原始的增益集或最新更新的版本號碼。
  
    
    
24 小時內到組織的增益集目錄，以及將其上傳至Office Store、 日當週內上傳您更新之後相對值有可用的更新旁邊會出現在每個網站安裝所在的 **網站內容**] 頁面上的增益集清單。使用者可以按一下要更新的增益集圖 1 所示的連結。可用的更新也都會公開在租用戶管理 UI。
  
    
    

**圖 1 的增益集 SharePoint 升級程序**

  
    
    

  
    
    
![The UI steps for updating an app](images/UpdatingApp_AppTileUpdateNotice.png)
  
    
    

    
> [!秘訣]
> 當您開發更新時，您不想要等到 24 小時每次將新的版本上傳至您的測試 SharePoint 增益集目錄。如需如何立即更新增益集的資訊，請參閱 [增益集更新而不需要等候 24 小時](update-sharepoint-add-ins.md#ImmediateUpdateNotice)。> 根據預設，SharePoint 會檢查每隔 24 小時的更新已安裝的增益集。伺服器陣列管理員可以設定此為其他值使用下列SharePoint Management Shell命令，其中n是檢查之間的時數。>  `Set-SPInternalAppStateUpdateInterval -AppStateSyncHours n`> 如果值設為 0，然後會進行檢查，每次內建的計時器工作 **Internal 增益集狀態更新**執行其預設值為每小時。伺服器陣列管理員可以使用管理中心變更計時器工作的頻率或立即執行。
  
    
    

當使用者安裝更新至SharePoint Add-inSharePoint 2013會執行下列作業。完全此順序執行不一定是發生這些事件及有些可能會同時發生。此外，如果更新失敗，是完成回復。
  
    
    

- SharePoint 2013提示使用者核准要求的增益集的權限。
    
  
- SharePoint 2013讓增益集無法使用使用者暫時。
    
  
- 如果增益集包含 SharePoint 方案套件 (.wsp) 與解決方案套件的內容已變更以任何方法，SharePoint 會執行下列動作：
    
  - 讓增益集 web 的備份。(不過，在 SharePoint Online 和內部部署 SharePoint 2016 中和更新版本、 SharePoint 清單中的實際資料備份只有當更新為清單中的結構描述中所做的變更)。
    
  
  - 測試備份的更新。
    
  
  - 如果測試成功，這會更新原始的增益集網頁。請注意增益集套件中的新.wsp 檔案用來更新特性及增益集 web 中的其他項目。(功能結構描述更新組件已擴充SharePoint 2013中。)
    
  
- SharePoint 2013執行 **UpgradedEventEndpoint** web 服務，如有任何已登錄的增益集資訊清單中。
    
    > [!注意事項]
      > 如果增益集，提供者主控您的增益集的所有非 SharePoint 元件提供更新邏輯。大致上，就如同您這些元件從個別安裝的增益集的安裝更新分開的SharePoint Add-in本身，更新這些元件。但是可能會有一些使用者已更新SharePoint Add-in時應該只會發生的變更。此邏輯可繼續在 **UpgradedEventEndpoint** web 服務或增益集本身的 「 第一次更新之後執行"邏輯。
- SharePoint 2013增益集和其元件可讓一次。
    
  

    
> [!注意事項]
> 如果要變更任何] 清單中的增益集網站的結構描述，然後清單備份以及增益集網頁的其餘部分。這可以在許多的清單中的資料是否需要一些時間。如果更新程序無法完成在 1 小時，它會停止並回復更新。
  
    
    


## 從舊的增益集移轉至新的其中一個
<a name="Major"> </a>

在某些情況下可能會想要產生的全新增益集來取代舊，而不是更新原始的其中一個。新的增益集可以有相同的易記名稱舊，但它必須授與新的產品識別碼增益集資訊清單，並將出現在公用Office Store及 SharePoint 網站的 [ **新增增益集**] 頁面上為不同的項目從原始版本。
  
    
    

> [!注意事項]
> 組織的增益集目錄中的項目被辨別依 *檔案名稱*  的增益集套件，不產品識別碼或增益集的名稱。如果新的增益集有舊相同的套件檔案名稱、 它要取代舊增益集資料庫目錄中，而舊的增益集將不會再出現在 [ **新增增益集**] 頁面上。如果您啟用版本設定的增益集套件當您將其上傳至目錄時，在舊版本 (這是舊的應用程式) 的檔案是仍然可以使用此項目歷程記錄中。您可以下載舊的增益集套件或還原為它，但沒有方法有兩個舊的和新增益集以不同的項目在目錄中或在 [ **新增增益集**] 頁面上除非有不同的檔案名稱。
  
    
    

在某些情況下，您可能需要將資料移轉。例如，新的增益集可能會使用Microsoft Azure SQL Database具有不同的結構描述從舊的增益集。或新的增益集可能使用不同的資料儲存機制 ；例如，外部資料庫，而不是 SharePoint 清單。您必須提供資料移轉程式碼。
  
    
    
如果您舊的資料是兩者的遠端事件處理常式可存取，您可以在 **InstalledEventEndpoint**中實作移轉邏輯或者 web 服務的新增益集、 如果新的增益集可以存取舊的資料，您可以將移轉邏輯第一次執行的程式碼中的使用者啟動新的增益集。如果舊的資料無法存取的遠端處理常式或新的增益集，您可以建立舊增益集，將資料匯出功能和功能的使用者介面的更新。使用者會先更新舊增益集，然後用它來將資料匯出至其中的新的增益集可以存取它的位置。您加入的功能和使用者介面匯入新增益集的資料。
  
    
    
在這當中，您可以重複使用的外部資料來源、 計算元件或其他外部元件中的新增益集所使用舊的增益集考量事項，但該SharePoint Add-in解除安裝時, SharePoint 2013基礎結構會解除安裝它所安裝的所有項目。據以，其通常是以取決於只有其安裝的元件或已不會安裝SharePoint 2013基礎結構的外部元件SharePoint Add-in很好的作法。
  
    
    

> [!注意事項]
> 我們建議您實作 **InstalledEventEndpoint**或安裝元件 **UpgradedEventEndpoint** ，如果您應該也實作解除安裝這些相同元件 **UninstallingEventEndpoint** 。這麼做讓符合設計準則的增益集應該是自給自足及完全解除安裝。不過，應該不會刪除仍很有用的使用者的增益集已解除安裝後的資料。建立增益集、 增益集 web 以外的網站通常是先考慮資料。
  
    
    

如果舊的和新增益集每個包含增益集網頁，請考慮安裝您新的增益集時建立的新的增益集網頁。此原因，您不應使用更新相關的 XML 標記SharePoint 2013功能結構描述中。因為您不會更新現有的 SharePoint 元件 ； 這類標記無法運作您要取代舊的增益集與一份新。
  
    
    

## 其他資源
<a name="SP15appupgrade_addlresources"> </a>


-  [更新 SharePoint 增益集](update-sharepoint-add-ins.md)
    
  
-  [更新 SharePoint 2013 中的增益集 web 元件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [更新 SharePoint 2013 中的主機 web 元件](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [建立更新事件處理常式中 SharePoint 增益集](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [更新遠端元件的 SharePoint 增益集](update-remote-components-in-sharepoint-add-ins.md)
    
  
-  [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [部署及安裝 SharePoint 增益集： 方法與選項](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  

