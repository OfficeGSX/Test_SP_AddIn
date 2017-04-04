---
title: 建立 SharePoint 增益集，可供匿名使用者
ms.prod: SHAREPOINT
ms.assetid: e858fce6-7a8f-4b1e-b053-64dc75345801
---


# 建立 SharePoint 增益集，可供匿名使用者
了解如何建立可供匿名使用者公開Microsoft SharePoint 2013網站上使用的SharePoint Add-ins 。
> [!重要]
> 儘 *內部*  SharePoint 2013本文中提及，我們假設已安裝 Service Pack 1 for SharePoint 2013 。
  
    
    


## 建立可匿名存取SharePoint Add-ins的必要條件

可能的 SharePoint 裝載和提供者主控SharePoint Add-ins匿名存取。依據類型您可以建立、 檢閱下列必要條件的組:
  
    
    

-  [](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#SP15SPhostedapps_bk_prereqs)
    
  
-  [](get-started-creating-provider-hosted-sharepoint-add-ins.md#SP15createselfhostapp_bk_prereq)
    
  
網站集合也必須設定為匿名存取測試SharePoint 2013安裝中。如果您有Office 365 開發人員網站，是已經使用特殊的公用網站的網站定義的公用網站集合與它相關聯。(如Microsoft SharePoint Online中的公用網站的使用的相關詳細資訊，請參閱 [說明 Office 365 的公用網站](http://office.microsoft.com/en-gb/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1))。無法使用的內部部署SharePoint 2013安裝該網站定義。讓內部部署測試安裝時，您將需要：
  
    
    

- SharePoint web 應用程式設定為允許匿名存取。但此組態必須在安裝您要測試SharePoint Add-in之後發生。
    
  
- 網站集合內的 web 應用程式本身已設定匿名存取。但此組態必須在安裝您要測試SharePoint Add-in之後發生。
    
  
- 如果增益集是 SharePoint 主控及存取 SharePoint 清單中，會針對匿名存取的網站集合中的清單。
    
  
 [設定 SharePoint web 應用程式和網站集合與清單的匿名存取](#Configuring)] 區段中的這些工作的指示說明。
  
    
    

## 使用限制的 SharePoint 增益集匿名使用者

當您設計您SharePoint Add-in，請考慮下列數據：
  
    
    

- 如果可從 SharePoint 啟動SharePoint Add-in ，它必須包含自訂動作或增益集組件，或必須從頁面上的自訂連結內容啟動。這是因為匿名使用者無法啟動增益集由其並排顯示。需要使用不是匿名使用者存取的特殊 [應用程式] 頁面上](http://msdn.microsoft.com/library/685c8e01-b163-4b5e-888c-421d9ecbb25e%28Office.15%29.aspx)的增益集已從磚啟動機制。另一個作法是讓增益集也從外部 SharePoint、 已在此情況下使用增益集僅在授權原則。
    
  
- 匿名使用者只可啟動SharePoint Add-ins已安裝的其他人。這是因為匿名使用者無法開啟 [ **新增增益集**] 頁面。
    
  
- 當登入的使用者瀏覽至網站的匿名存取] **系統帳戶** 的使用者的身分識別變更已設定內部部署 SharePoint web 應用程式中。此 identity 無法安裝SharePoint Add-ins。由於匿名使用者也無法安裝的增益集，這表示建議有空可以安裝SharePoint Add-ins時的 web 應用程式設定匿名存取。據以、 SharePoint Add-ins應該是之前安裝到 SharePoint web 應用程式中之網站的 web 應用程式設定匿名存取。如果稍後安裝需要其他增益集、 web 應用程式必須暫時變更停用匿名存取，因此可以登入的使用者所安裝之增益集。
    
  
- 若要啟用 SharePoint 搜尋 REST api (英文) 的內部部署 SharePoint 網站中，您可以設定某些查詢 XML。如需詳細資訊，請參閱 [啟用匿名搜尋 REST 查詢](http://msdn.microsoft.com/library/office/jj163876.aspx#bk_AnonymousREST)。
    
  
- 因此必須從遠端部署自訂資料或主機 web 搜尋索引器不會編目增益集在 web 上的資料。這適用於所有SharePoint Add-ins，但是我們記下其以下因為增益集的適用對象的匿名存取通常需要搜尋功能。
    
  

## 建立提供者主控增益集可匿名存取
<a name="Cloud-hosted"> </a>

進行的提供者主控增益集匿名使用者存取是只是設定成增益集僅在授權原則。使用此原則時，SharePoint 不甚至是包含使用者內容，讓它不會有影響使用者是匿名。只是必要的增益集主體租用戶系統管理員所安裝的增益集所授與它需要主機 web-與任何它需要父網站集合或父租用-任何權限。您要求增益集資訊清單中的主機網站的權限就像任何增益集一樣。
  
    
    

> [!注意事項]
> 匿名使用者存取 SharePoint 網站時，通常允許 HTTP 存取，而不是 HTTPS。新增-僅在原則用於在此案例中的增益集時，有潛在的安全性問題。如需詳細資訊及減輕這些方法，請參閱 [每開發人員必須了解有關 SharePoint 增益集、 CSOM、 和匿名的發佈網站](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx)。
  
    
    

若要使用增益集僅在原則增益集設計需要兩件事：
  
    
    

- 您必須新增 `AllowAppOnlyPolicy="true"` **AppPermissionRequests**元素中的增益集資訊清單。
    
  
- 新增-僅在存取權杖從 OAuth 驗證伺服器必須詢問您的程式碼。使用 managed 程式碼時，是否有 Api 特別針對此目的而Microsoft Office Developer Tools for Visual Studio所產生的程式碼檔案中建立: SharePointContext.cs (或.vb) 和 TokenHelper.cs (或.vb)。
    
  
如需新增-僅在原則 (使用程式碼片段 (英文)) 的詳細資訊，增益集權限及增益集資訊清單請參閱下列主題：
  
    
    

-  [SharePoint 2013 中的增益集授權原則類型](add-in-authorization-policy-types-in-sharepoint-2013.md)
    
  
-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

## 建立 SharePoint 主控增益集可匿名存取
<a name="SP-hosted"> </a>

建立 SharePoint 主控增益集可執行匿名使用者不需要任何特殊的技術。您建立您可以建立任何 SharePoint 主控增益集的方式相同。如需詳細資訊，請參閱 [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)和 [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)。
  
    
    
什麼是真正重要讓 SharePoint 主控增益集可由匿名存取的是SharePoint Online網站集合已由承租人管理員為允許匿名存取。沒有您身為開發人員的 SharePoint 裝載增益集可以進行此設定是從增益集、、 遠端增益集事件接收器，還是 SharePoint 增益集安裝基礎結構與方法。您可以包含自訂清單或文件庫增益集，但是Collaborative Application Markup Language (CAML)不提供任何方法來預先匿名存取，因此它必須安裝的增益集之後進行設定。
  
    
    
如果行銷您的增益集透過Office Store、 潛在客戶的重要部分會尋找增益集最有用的匿名使用者存取時，您應該考慮提及您增益集的描述此組態需求。請考慮也包括增益集的 「 支援] 頁面上 **設定 SharePoint Online 網站集合的匿名存取** 下方的程序的版本。
  
    
    
如果增益集使用 SharePoint 的 JavaScript 物件模型 (JSOM)，則不是非常重要的其中一個組態需求。JSOM Api 非常小部分是預設匿名使用者存取的。所有其他人需要使用者具有 **使用遠端介面** 權限和匿名使用者沒有此權限。這項需求具有可以關閉在父網站集合或父 SharePoint web 應用程式增益集安裝的網站 [設定 SharePoint web 應用程式和網站集合與清單的匿名存取](#Configuring)中所述。
  
    
    

> [!注意事項]
> 關閉使用者有 **使用遠端介面** 權限需求有資訊隱私權的隱含意義。如需詳細資訊，請參閱 [每開發人員必須了解有關 SharePoint 增益集、 CSOM、 和匿名的發佈網站](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx)。
  
    
    


## SharePoint 主控的增益集匿名使用者的限制
<a name="SP-hosted"> </a>

我們將會與您誠實： SharePoint 主控增益集必須注意的匿名使用者的一些重要倒是：
  
    
    

- 您不可以讓清單或文件庫上SharePoint Online匿名使用者存取。據以、 JSOM Api 互動的清單或文件庫上SharePoint Online匿名使用者無法運作。
    
  
- SharePoint 主控增益集安裝至 *內部部署*  SharePoint 網站可以利用所有 JSOM api (英文)。不過，web 應用程式、 網站集合及清單/文件庫必須 *以手動方式*  設定為匿名存取及 **使用遠端介面** 權限需求，必須停用。如果增益集至增益集 web 安裝自訂清單/文件庫使用者負責以手動方式設定這些匿名存取的增益集] 安裝之後。
    
  
- 由於增益集在 web 上的資料不會編目的搜尋索引器和因為您沒有辦法自訂清單或文件庫安裝到主機網站，而且 SharePoint 主控的增益集不能有遠端元件或事件接收器中的 SharePoint 裝載增益集的自訂資料不是可供搜尋。
    
  

## 設定 SharePoint web 應用程式和網站集合與清單的匿名存取
<a name="Configuring"> </a>

如果您測試 SharePoint 安裝為內部部署，您必須執行下列前兩個程序以測試是否可供匿名使用者使用您SharePoint Add-in 。安裝您SharePoint Add-in客戶也必須執行這些程序父網站集合與SharePoint Add-in您已安裝任何網站的 web 應用程式。
  
    
    

> [!重要]
> 請儘可能您應該安裝SharePoint Add-in至網站 *之前先*  執行前兩個程序。當會在已設定匿名存取 web 應用程式增益集無法安裝至內部部署 SharePoint web 應用程式中的任何網站。如果已設定匿名存取 web 應用程式，您必須暫時反向若要安裝的增益集的設定。
  
    
    


### 若要設定匿名存取的上層 web 應用程式


1. 在 [ **管理中心**] 中選擇 [ **應用程式管理**]，然後按一下 [ **管理 Web 應用程式**]。
    
  
2. (選用)如果您不想要允許任何現有的 SharePoint web 應用程式的匿名存取、 建立新的 web 應用程式。(雖然 web 應用程式建立表單可讓您允許匿名存取， *請勿使用此選項*  的選項。匿名存取應該不會啟用之前安裝 *之後*  SharePoint Add-in 。)如需詳細資訊，請參閱 [Create a web application in SharePoint 2013](http://msdn.microsoft.com/library/121c8d83-a508-4437-978b-303096aa59df.aspx)。
    
  
3. 在 [web 應用程式] 清單選取一個要匿名使用者存取並選取功能區上的 [ **驗證提供者**。
    
  
4. 在開啟的註標，選擇要啟用匿名存取的區域。一般而言，這是 **網際網路**或 **預設值**。 **編輯驗證**表單隨即開啟。
    
  
5. 核取 [ **啟用匿名存取**] 方塊中，如果不已啟用。這會設定可以關閉特定網站集合的預設值，但如果您不要啟用此 web 應用程式，就無法啟用它的任何網站集合。
    
  
6. (選用)取消核取 [ **需要使用遠端介面權限**] 方塊中。這樣可以讓程式碼和匿名使用者來呼叫 SharePoint 的用戶端物件模型在每個網站集合的內容中執行的指令碼。您不能重新啟用任何網站集合的需求。離開] 方塊中檢查表示預設匿名使用者將無法存取用戶端物件模型，但是您可以停用需求 (讓這些存取) 的與特定網站集合。
    
    > [!注意事項]
      > 開發SharePoint Add-ins匿名使用者的內容，此設定僅為有意義的 SharePoint 主控新增寫提供者主控SharePoint Add-ins的設計用匿名使用者，請使用讓使用者的權限不相關的技術。如需此的詳細資訊，請參閱 [建立提供者主控增益集可匿名存取](#Cloud-hosted)上述一節。
7. 選擇 [ **儲存**] 以關閉表單。
    
  
8. 仍反白顯示 web 應用程式的情況下，選取 [功能區上的 **匿名原則**。
    
  
9. **匿名存取限制**的表單中選擇 [區域] 並確定已啟用 [ **無**] 選項按鈕。如果您要測試只需要SharePoint Add-in讀取權限的 SharePoint 資料、 啟用 **拒絕寫入**改用。
    
    > [!注意事項]
      > 這是另一個設定的開發SharePoint Add-ins匿名使用者的內容中才有意義的 SharePoint 主控的增益集。
10. 如果您在步驟 2 中建立新的 web 應用程式，您必須在其中建立網站集合。
    
  

### 設定匿名存取的內部網站集合


1. 在網站集合的 web 應用程式中的 [首頁] 頁面上選取齒輪圖示及 [ **網站設定**。
    
  
2. 在 [ **使用者與權限**] 區段中的 [ **網站設定**] 頁面上，選取 [ **網站權限**。
    
  
3. 功能區上選擇 [ **匿名存取**]。
    
  
4. **匿名存取**表單上選擇 [ **整個網站**] 或 [ **清單和文件庫**] 的存取層級根據您要測試需求的增益集。(無論您選擇哪個選項，匿名使用者無法存取的清單或文件庫以 SharePoint 主控增益集 (英文) 除非文件庫清單分別設定為所有匿名存取。下面的程序說明如何執行這項作業。
    
  
5.  *如果您要測試以存取 SharePoint 用戶端物件模型、 需求的增益集確定 **需要使用遠端介面權限**] 方塊會檢查。*  如果灰色] 核取方塊，然後需求已關閉在 web 應用程式層級具有相同效果] 方塊中所未核取。(上一個程序所述，開發SharePoint Add-ins為匿名使用者的內容中的此設定是僅有意義的 SharePoint 主控應用程式)。
    
  

> [!重要]
> 僅限SharePoint Online中的公用網站上執行下列程序。(如Microsoft SharePoint Online中的公用網站的使用的相關詳細資訊，請參閱 [說明 Office 365 的公用網站](http://office.microsoft.com/en-gb/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1))。
  
    
    


### 設定匿名存取SharePoint Online網站集合


1. 以Office 365租用戶系統管理員身分登入。
    
  
2. 在網站集合的首頁上，選擇 [ **讓網站線上**接近頁面右上角。此動作也會關閉 **使用遠端介面權限** 需求。
    
  
如果您開發 SharePoint 主控增益集和其存取 SharePoint 清單，您必須也執行下列程序中測試網站集合。使用您的增益集的客戶需要進行此作業，以及任何已安裝的增益集所在的網站。沒有方法的增益集可以透過程式設計方式或 descriptively 設定清單的匿名存取。增益集可以安裝自訂清單、 但沒有方法來預先設定這類清單的匿名存取]。設定必須安裝的增益集之後仍手動會完成。如果您開發提供者主控SharePoint Add-in匿名使用者，則不需要此程序。
  
    
    

> [!注意事項]
> 此程序無法執行SharePoint Online網站集合中，因此 SharePoint 主控增益集的安裝至SharePoint Online與對象為使用匿名使用者無法存取清單或文件庫。
  
    
    


### 若要設定的清單或文件庫的匿名存取


1. 瀏覽至清單或文件庫的存取您要測試SharePoint Add-in並開啟 [ **清單設定**] 或 [ **文件庫設定**。SharePoint Online 網站，您必須租用戶系統管理員身分登入。
    
  
2. 選擇 **清單/程式庫的權限**。
    
  
3. 在 [開啟] 頁面上 [ **權限**] 索引標籤上選擇 [ **停止繼承權限**，然後選擇 **[確定]**上的確認提示。
    
  
4. 選擇 **權限**] 索引標籤上的 [ **匿名存取**]。
    
  
5. 會開啟 **匿名存取**表單上選取 [全部的權限的SharePoint Add-in的使用者要求。您可以授與權限檢視、 新增、 編輯和刪除項目。您不能授與完全控制，因此匿名使用者無法變更結構描述的清單或變更清單設定。
    
  

## 其他資源
<a name="bk_addresources"> </a>


-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  

