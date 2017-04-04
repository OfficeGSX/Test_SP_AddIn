---
title: 重要方面之 SharePoint 增益集架構設計和開發入門
ms.prod: SHAREPOINT
ms.assetid: ae96572b-8f06-4fd3-854f-fc312f7f2d88
---


# 重要方面之 SharePoint 增益集架構設計和開發入門
了解方面SharePoint Add-ins以及SharePoint 增益集相關的模型，包括增益集主控選項、 使用者介面選項、 部署系統、 安全性系統及生命週期的架構。本文補充文章 [SharePoint Add-ins](sharepoint-add-ins.md)中的資訊。
## SharePoint 增益集主控選項
<a name="SPAppModelArch_SPCenteredVsCloudCentered"> </a>

SharePoint 2013增益集模型提供下列方法來裝載SharePoint Add-in的元件：
  
    
    

- **提供者主控:**增益集包含至少一個遠端元件及可能還會包括 SharePoint 元件。非 SharePoint 元件會透過您的硬體或雲端的帳戶，在您邏輯部署或部署在客戶的硬體或雲端帳戶使用安裝程式與您所提供的指示。
    
  
- **SharePoint 主控:**增益集包括只有 SharePoint 元件與用戶端執行的邏輯。
    
  
如需代管選項及如何選擇兩者之間的一些指引的詳細資訊，請參閱 [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)。
  
    
    

## 增益集 web、 主機 web、 功能及 SharePoint 中的增益集的元件
<a name="SPComponents"> </a>

要安裝SharePoint Add-in網站會呼叫主機網頁。不過， SharePoint Add-in、 的重要部分其是否為 SharePoint 元件或外部元件未部署到主機網站。外部的組件部署至外部伺服器或雲端的帳戶。SharePoint 元件與自己的網域的特殊網站部署。這會呼叫增益集網頁。只有一組有限的讓使用者能夠存取增益集的 UI 元素的其他元件部署到主機網站。在主機 web 這些使用者介面元件部署為 「 主機 web 功能的一部分--會寬鬆增益集套件中而不是.wsp 檔案內的功能。部署至增益集 web 元件永遠都會以.wsp 檔案內的功能。這兩種類型的功能必須具備 **Web**範圍。沒有其他範圍是在SharePoint Add-ins可能的功能。
  
    
    
依照通則，不包括在 SharePoint 伺服器執行的自訂程式碼的任何 SharePoint 元件還包含在SharePoint Add-in (和部署至增益集 web)。有，不過，某些例外日期] 及 [一些細微至方法和位置所部署的元件。如需有關這些細微及主機 web 的隔離增益集的 web 和增益集功能請參閱 [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)。
  
    
    

## 從使用者介面存取增益集
<a name="AccessingApp"> </a>

網站上已安裝SharePoint Add-in 、 增益集會列在 **[網站內容**] 頁面上的主機網站。使用者可以從該頁面啟動增益集。當開啟以這種方式，增益集以全螢幕模式執行。
  
    
    
SharePoint Add-in可以呈現的另一種方式是透過增益集組件、 網頁組件 **ClientWebPart**類別所表示的類型。這種網頁組件是基本上會裝載的增益集] 頁面的 IFrame 包裝函式。在最簡單的情況下，網頁組件僅重要屬性是指向頁面的 URL。但網頁組件可以有使用者可以設定工具組件中的自訂屬性。這類屬性無法使用，例如設定如使用者的郵遞區號或郵遞區號的內容資訊。在您的增益集包含這類增益集組件，您的增益集] 和 [加入宣告式的網頁組件標記建立主機 web 功能。像其他網頁組件] 中顯示的使用者新增網頁組件SharePoint 2013 UI。您可以有多個增益集組件如果您需要更多的變異增益集的部署。例如，天氣增益集可以擁有增益集組件會顯示目前的天氣及第二個增益集組件會顯示每週預測。兩個部分可以有不同的大小和功能。
  
    
    

> **注意事項**
> 您也可以部署增益集組件至增益集的網站。若要實施此作業，網頁組件的標記會是.wsp 檔案的增益集套件而非中的主機網站功能內功能的一部分。
  
    
    

我們建議您嘗試授與您的增益集的 SharePoint 外觀尺寸，但不是必要項目且不一定是最佳選擇。如需使用者經驗方針的詳細資訊，請參閱 [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)。
  
    
    
有，例如呼叫 app.master 特殊主版頁面。此頁面是最適合使用之頁面的增益集。App.master 頁面是新的網站定義所含的SharePoint 2013的一部分。
  
    
    
您可以使用可協助您的增益集維持一致的外觀與風格與 SharePoint 是隨附SharePoint 2013的 chrome 控制項另一項工具。此控制項可讓您將 SharePoint 導覽頁首區域新增至您增益集的頁面，包括從外部主控的頁面。如需在SharePoint Add-insUX 設計的詳細資訊，請參閱 [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)。如需 chrome 控制項的詳細資訊，請參閱 [使用用戶端的 chrome 控制項中 SharePoint 增益集](use-the-client-chrome-control-in-sharepoint-add-ins.md)。
  
    
    

## 增益集套件結構
<a name="SPAppModelArch_Package"> </a>

SharePoint Add-in套件為可遵守 [開放封裝慣例 (OPC)](http://msdn.microsoft.com/en-us/magazine/cc163372.aspx)並已".app"副檔名的檔案。(您可以新增額外的副檔名上檔案名稱為".zip"並再開啟 Windows 檔案總管中開啟檔案)。包含增益集資訊清單指定的增益集和指示 SharePoint 安裝基礎結構的特定內容。如需增益集資訊清單和套件的詳細資訊，請參閱 [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)。
  
    
    

## 權限、 驗證及授權 SharePoint 增益集
<a name="SPAppModelArch_Running"> </a>

SharePoint 2013介紹新增益集的權限及安全性系統。
  
    
    

### 增益集的權限
<a name="AppPermissions"> </a>

SharePoint Add-ins剛才為使用者與群組具有權限。這可讓增益集有一組不同的使用者執行增益集的權限的權限。
  
    
    
您必須要求、 增益集資訊清單檔案、 增益集所需執行的權限。增益集新增的使用者必須授與這些要求和使用者只可授與他可為使用者的權限。授與必須針對所有要求的權限或無這些簡化管理使用者與開發人員的權限。(增益集主體一律有增益集 web 的 「 完全控制 」 權限讓它只需要要求主機 web 或增益集網路外的其他位置中的 SharePoint 資源的權限)。
  
    
    
如需增益集的權限的詳細資訊，請參閱 [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)。
  
    
    

### 選擇性委派及授權
<a name="SelectiveAuthorization"> </a>

啟動增益集] 中的使用者和資源擁有者所授與增益集存取權限的資源，都需要其認證或密碼提供增益集。而是SharePoint 2013可讓使用者與授與的特定權限的增益集要求的資源擁有者。何謂這可能是由SharePoint 2013交易通訊協定 [OAuth 2.0](http://tools.ietf.org/html/draft-ietf-oauth-v2-22)使用。在SharePoint 2013OAuth 的相關資訊，請參閱 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)。
  
    
    

### 跨網域存取
<a name="SelectiveAuthorization"> </a>

包含其資料存取邏輯可以使用JavaScript使用JavaScript的遠端 web 應用程式SharePoint Add-in跨網域文件庫內的增益集已安裝租用取得授權的存取 SharePoint 資料。如需詳細資訊，請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
  
    
    

## 增益集生命週期
<a name="SPAppModelArch_Lifecycle"> </a>

技術支援週期的SharePoint Add-in包含發佈、 安裝、 升級及解除安裝。如需這些主題的詳細資訊，請參閱 [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)、  [部署及安裝 SharePoint 增益集： 方法與選項](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)和 [SharePoint 增益集更新程序](sharepoint-add-ins-update-process.md)。請注意有哪些租用戶的系統管理員可以批次的機制安裝SharePoint Add-in到多個網站。如需詳細資訊，請參閱 [租用和部署範圍的 SharePoint 增益集](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。
  
    
    

## 資料儲存在 SharePoint 增益集
<a name="Data"> </a>

SharePoint Add-ins可以建立並存取任何種類的資料，包括結構化的資料、 文件及處理多媒體檔案。此資料可以儲存在 SharePoint 中或在外部的位置。
  
    
    

### 結構化的資料存放區選項
<a name="StructuredData"> </a>

SharePoint Add-in可以使用幾乎任何種類的結構化的資料存放區、 內部和不在SharePoint 2013和 Microsoft 和非 Microsoft 平台上。以下是 *一些*  您可以在其中儲存SharePoint Add-in的結構化的資料的位置：
  
    
    

- 增益集網頁中的 SharePoint 清單
    
  
- SQL Azure
    
  
- 外部資料來源與Microsoft Business Connectivity Services (BCS)連線至 SharePoint
    
  
- 非 Microsoft 雲端服務
    
  
- 在您自己的伺服器上的資料庫
    
  

> **秘訣**
> 您可能會升級在某些您SharePoint Add-in 。時SharePoint Add-in包含增益集網頁上的 SharePoint 元件，升級程序會建立增益集 web 的完整複本。基於此增益集在 web 上非常大型的 SharePoint 清單密集讓耗時的升級程序和處理器內容資料庫伺服器上。您應避免放在 SharePoint 清單中的 「 大資料 」 增益集在 web 上。
  
    
    


### 非結構化的資料存放區選項
<a name="UnStructuredData"> </a>

文件、 影像、 影片、 音訊檔及其他種類的已產生或SharePoint Add-in所使用的非結構化資料可以儲存或 SharePoint 外部。文件庫是不錯的選擇文件和透過 SharePoint 搜尋可搜尋。網站資產庫通常是不錯的選擇多媒體檔案。
  
    
    
其他選項包括 blob 存放區中Microsoft Azure帳戶，或您自己的伺服器上。您也可以將檔案儲存在某些非 Microsoft 平台或雲端服務。
  
    
    

### 增益集設定和其他中繼資料存放區選項
<a name="AppMetadata"> </a>

有幾個地方可以儲存的SharePoint Add-in，例如使用者喜好設定、 位置資訊及其他設定的中繼資料。隱藏的 SharePoint 清單有時是不錯的選擇。您也可以使用的屬性包的增益集 web。提供者主控增益集的另一個作法是使用Microsoft Azure表格儲存體。
  
    
    

### 安全的資料存取選項
<a name="DataAccess"> </a>

安全的資料存取選項當然，取決於您選擇的存放區。資料存取和搜尋所述的數個其他文章中的詳細資訊。如需詳細資訊，請參閱 [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)。
  
    
    

## 管理增益集
<a name="SPAppModelArch_Managing"> </a>

網站集合管理員和租用系統管理員可以監視增益集及變更配置給他們的資源。此外， Microsoft人員增益集存放區可以標幟增益集和停用它們。
  
    
    
如需管理增益集的詳細資訊，請參閱 [安裝及管理 SharePoint 增益集](http://msdn.microsoft.com/en-us/library/733647a3-a5d3-475b-967d-3bb627c2a0c2) TechNet 上。
  
    
    

### 監視增益集
<a name="SPAppModelArch_Monitoring"> </a>

SharePoint 2013提供狀況監控的增益集，並提供這項資訊在 UI 中給網站擁有者、 承租人管理員及伺服器陣列管理員。大部分的文件的監控系統是 technet;例如 [監視 SharePoint 增益集](http://technet.microsoft.com/library/3adafdd2-f276-4a9d-8a74-e06b8916bbc2)時。本節是剛快速介紹說明增益集，您買賣受監視的方式。
  
    
    
某些類型的資料會回報每個應用程式及其他類型的報告每個 - 應用程式執行個體。監控架構報告的主要項目如下所示：
  
    
    

- 使用增益集，例如它已安裝 (建立的新執行個體) 的次數。
    
  
- 每個增益集執行個體的伺服器資源使用率。
    
  
- 安裝、 升級及每個增益集執行個體的執行階段錯誤。
    
  
- 每個增益集的執行個體綠色、 黃色和紅色整體健康狀態指示器。
    
  
如果增益集包括Azure 網站元件、 監控架構也會輪詢Microsoft Azure每小時的錯誤資料和報告嚴重錯誤其SharePoint 2013 UI 中的儲存配額資料。不會報告Microsoft Azure SQL Database錯誤。
  
    
    
監控架構所提供的資訊可讓系統管理員可以決定是否其增益集購買預算正在地所花費的、 已有部署多個資源至增益集]，以及是否有停用增益集無法正常運作。
  
    
    

## 註冊增益集相依性
<a name="RegisterDependency"> </a>

如果您SharePoint Add-in取決於不提供且不能可使用增益集從網頁、 則無法正常運作，將抱怨您的客戶的 SharePoint 功能。您可以確保您的增益集安裝的是不出必要的服務和功能都無法使用所登錄的增益集的相依性資訊增益集清單中。這些必要條件檢查SharePoint Add-ins安裝基礎結構和它會封鎖安裝的您增益集 (英文) 如果這些即無法使用。
  
    
    
Services、 Excel、 存取或 Visio services 之類的基礎結構會驗證服務已安裝並授權。
  
    
    
功能、 工作清單，例如基礎結構會確認部署的功能和下列任一項：
  
    
    
-啟動範圍 **Farm**、 **WebApplication**或 **Site** (網站集合)
  
    
    
或
  
    
    
-activatible **Web**範圍、 增益集在 web 上安裝的增益集時所建立。
  
    
    

> **注意事項**
> 建立後，增益集安裝基礎結構會自動啟動增益集在 web 上的這類功能。
  
    
    

下列各節提供您需要註冊您的必要條件的詳細資料。
  
    
    

### 隱含權限要求中註冊相依性
<a name="PermAsPreq"> </a>

當您的增益集需要存取增益集網路外部的 SharePoint 元件，它必須要求增益集資訊清單的 [ **AppPermissionRequests** ] 區段中的這些資源的權限。這些權限要求也會做必要的註冊因為 SharePoint 將推斷從權限您的增益集要求增益集需要特定的 SharePoint 功能可供使用。在許多情況下，SharePoint 可以推斷的增益集需要且不需要此主題的其餘小節的所有功能。不過，設為備援的相依性註冊沒有有害。
  
    
    

### 明確地使用 AppPrerequisites 登錄相依性
<a name="Explicit"> </a>

當您的增益集有不以其權限要求隱含的相依性時，您註冊每個相依性與 **AppPrerequisite**元素中的增益集資訊清單。此元素 ； 中有三個屬性 **Type**、 **ID**，以及 (選擇性) **MinimumVersion**。
  
    
    
有三種可能的必要值的 **Type**; `Feature`、  `Capablility`、 和 `AutoProvisioning`。功能必要條件是只是 SharePoint 的功能，必須部署和啟動增益集 web 或較大的範圍包含增益集網頁上。功能是一組相關的功能和必須能夠使用增益集在 web 上的服務。( `AutoProvisioning`一節中討論)。
  
    
    
選用 **MinimumVersion**指定的功能或功能增益集所需的最低版本。屬性值為的表單為 n.n.n.n;例如 `15.0.0.0`。
  
    
    
 **ID**指定所需的功能。如果 `Feature` **Type** ， **ID**是以方括弧括起、 斷字的 GUID 功能 ；例如 `{151D22D9-95A8-4904-A0A3-22E4DB85D1E0}`。如果 `Capability` **Type**即 **ID**為功能的 GUID。功能會如下所示。若要取得尋找功能的 GUID 請參閱 [AppPrerequisite 元素 (AppPrerequisiteCollection complexType) (SharePoint 增益集資訊清單)](http://msdn.microsoft.com/library/791be402-981f-519e-fcde-f24cc3cb4139%28Office.15%29.aspx)。
  
    
    

- Access Services 2010
    
  
- Access Services
    
  
- Managed Metadata Web Service
    
  
- PowerPoint 服務
    
  
- Secure Store Services
    
  
- Machine Translation Service
    
  
- 使用者設定檔服務
    
  
- Visio Graphics Service
    
  
- 工作管理服務
    
  
- DUET
    
  
- 工作流程
    
  
- 搜尋
    
  
- EDU
    
  
以下是註冊的工作流程功能的原始 **AppPrerequisites**標記的範例。如果您使用Visual Studio，您可以編輯設計工具中的增益集資訊清單。
  
    
    



```

<AppPrerequisites>
  <AppPrerequisite Type="Capability" ID="{CDD8F991-B459-4512-8048-03D5A03FF27E}" MinimumVersion="15.0.0.0" />
</ AppPrerequisites>
```


## 在本章節中
<a name="RegisterDependency"> </a>


-  [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)
    
  
-  [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

## 其他資源
<a name="SPAppModelArch_AdditionalResources"> </a>


-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  
-  [與 SharePoint 解決方案比較 SharePoint 增益集](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)
    
  
-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [IFrame](http://www.w3schools.com/tags/tag_iframe.asp)
    
  
-  [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [部署及安裝 SharePoint 增益集： 方法與選項](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [SharePoint 增益集更新程序](sharepoint-add-ins-update-process.md) .
    
  
-  [租用和部署範圍的 SharePoint 增益集](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  

