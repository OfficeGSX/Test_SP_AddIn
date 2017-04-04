---
title: 三個授權系統的 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 623fdab7-856e-4a89-9f5d-748a2ba1ef2e
---


# 三個授權系統的 SharePoint 增益集
了解三個系統可以使用該SharePoint Add-ins取得SharePoint資源的授權。
在SharePoint、 SharePoint Add-in是主體就像是使用者身分識別和必須驗證及授權使用SharePoint資源。有三個授權系統可以使用增益集。而不是互斥的。
  
    
    


## 了解三個授權系統及使用時機
<a name="UnderstandThreeSystems"> </a>


  
    
    

- **低信任層級**： 提供者主控SharePoint Add-in可以Microsoft Azure Access Control Service (ACS)增益集可讓增益集存取資源SharePoint租用中對發出存取權杖中註冊或增益集安裝在伺服器陣列。Azure ACS OAuth 2.0 Framework"流程"，其中包含 SharePoint 中的受信任 token 簽署者，而且可以在Office Store售出增益集增益集使用此系統的遠端元件。低信任層級系統主要增益集之遠端元件都架設在雲端上執行。
    
    如需建立使用低信任層級系統SharePoint Add-in的詳細資訊，請參閱 SDK 節點 [建立 SharePoint 增益集使用低信任層級授權](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)。
    
    > [!注意事項]
      > 安裝的增益集的客戶必須具備Office 365帳戶。這會需要以Azure ACS來提供增益集的存取權。不過，在客戶不需要任何其他用途使用帳戶和增益集可以安裝至內部部署SharePoint的伺服器陣列的伺服器陣列上一些簡單的設定工作之後。
- **高信任層級**： 提供者主控增益集可以使用數位憑證建立與SharePoint信任。 高信任系統是主要用於供增益集之遠端元件都架設在內部部署。可安裝的增益集未連線至網際網路的SharePoint伺服器陣列。增益集無法安裝於SharePoint Online或售出Office Store中。
    
    如需建立使用高信任系統SharePoint Add-in的詳細資訊，請參閱 SDK 節點 [建立 SharePoint 增益集使用高信任授權](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)。
    
  
- **跨網域文件庫**： JavaScript商務邏輯增益集時，您可以選擇使用SharePoint跨網域文件庫中取代的或作為至低信任層級與高信任系統 supplement (英文)。文件庫也適用於案例其中增益集有雲端託管的元件，但客戶公司防火牆難使用低信任層級系統的。使用者瀏覽器封鎖來自其他網域中，指令碼，但文件庫封裝周圍這項限制的運作狀況的安全系統。增益集使用文件庫可在Office Store售出和可以安裝至SharePoint Online或內部SharePoint。
    
    如需建立SharePoint Add-in採用的跨網域文件庫的詳細資訊，請參閱 SDK 節點 [建立 SharePoint 增益集使用的跨網域文件庫](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)、 部落格文章 [解決跨網域問題中 SharePoint 增益集](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)。
    
  

## OAuth 2.0 架構和它的SharePoint實作的背景資訊
<a name="UnderstandThreeSystems"> </a>

兩個三個授權系統使用 OAuth 2.0 架構。OAuth 2.0 是 **開啟的授權的架構** 。OAuth 啟用安全的桌面、 裝置和 web 應用程式的授權的標準的方式。OAuth 可讓使用者核准，對其代理不含共用其的使用者名稱及密碼的應用程式。例如，它 **可讓使用者共用** 他的私人資源或上一個與另一個站台、 **without requiring the user to provide his or her credentials** (通常是使用者名稱和密碼) 至其他網站的網站 (連絡人清單、 文件、 圖片、 影片等等) 的資料。
  
    
    
OAuth 可讓使用者以提供裝載指定的服務提供者 (如SharePoint) 的資料存取權杖。每個語彙基元授與存取特定資源提供者 (例如SharePoint網站)，特定資源 (例如文件SharePoint文件庫中)，並已定義的持續期間 (例如，12 小時)。這可讓使用者授與協力廠商網頁或資訊儲存與另一個資源或服務供應商 (例如SharePoint)、 桌面應用程式存取及如何而不共用其的使用者名稱及密碼及共用 *所有*  他可與提供者的資料。
  
    
    
SharePoint使用 **OAuth 2.0** 架構 **使用權杖傳遞"流程"** 來：
  
    
    

- 依存取SharePoint資源SharePoint Add-in授權要求。
    
  
- 驗證Office 市集、 增益集目錄或開發人員租用戶中的增益集。
    
  
如需詳細資訊和 OAuth 相關的背景和 OAuth 術語，請參閱 [OAuth.net](http://oauth.net/)和 [Web 的驗證通訊協定 (oauth)](http://datatracker.ietf.org/doc/active/)。Sum、 只有資源伺服器的主控受保護的資源、 資源、 用戶端應用程式的搜尋項目資源的存取權及問題存取授權伺服器的擁有者語彙基元時指示，否則資源擁有者的資源。正式的 OAuth 文件與不同假設執行的案例中有授與存取權之資源的用戶端應用程式每次從用戶端應用程式的單一資源擁有者。它也會假設使用用戶端應用程式之人員的資源擁有者。SharePoint實作採用的多個使用者之間共用SharePoint資源] 清單中，例如事實的帳戶。此外，在SharePoint實作SharePoint Add-in會授與存取權] 安裝時，它會執行 ； 不每次與安裝並授與它存取的人員以外的使用者可以執行。(但如果是SharePoint，但 access SharePoint資源 (所以是只能在延伸的有意義的"SharePoint Add-ins") 上未安裝的增益集、 增益集執行的使用者沒有授與權限每次增益集執行。)
  
    
    
因此在SharePoint實作 OAuth 角色時會播放由下列元件：
  
    
    

- SharePoint Add-in遠端元件會播放的用戶端應用程式的角色。
    
  
- SharePoint使用者播放資源擁有者的角色。
    
  
- SharePoint播放資源伺服器的角色。
    
  
- Azure ACS會在使用 [低信任層級授權系統](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)時播放授權伺服器的角色。使用 [高信任系統](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)時，增益集本身 (搭配 [數位的憑證) 會成為授權伺服器。
    
  

### 不登入 token 存取權杖。
<a name="FileName_uniquekeyword3"> </a>

如上述，來存取的資源、 增益集必須從具有先前已指定為信任的安全性 token 發行者 (STS) 的資源擁有者 OAuth 驗證伺服器要求存取權杖。相較之下，主要是都是 WS-同盟 STS 及安全性聲明標記語言 (SAML) 被動登入 STS 發出登入 token。在SharePoint、 OAuth STS 不用於核發登入 token，亦即無法使用做為身分識別提供者。如此，您不會看到 OAuth STS 列在使用者登入頁面、 在管理中心的 [ **驗證提供者**] 區段或人員選擇] 在SharePoint。
  
    
    
SharePoint系統管理員可以使用Windows PowerShell命令啟用或停用 OAuth STS 類似方式可啟用或停用信任的登入提供者中SharePoint 2010。
  
    
    

