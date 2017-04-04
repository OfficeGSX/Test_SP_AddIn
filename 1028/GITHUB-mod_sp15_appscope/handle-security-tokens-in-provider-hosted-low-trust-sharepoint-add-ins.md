---
title: 處理安全性權杖的提供者主控低信任層級 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# 處理安全性權杖的提供者主控低信任層級 SharePoint 增益集
了解之內容的存取，並重新整理低信任層級、 提供者主控SharePoint Add-ins及如何使用您的程式碼中所使用的授權的 token。
> [!重要]
> **本文是完全相關的安全性權杖使用低信任層級授權系統都不高信任系統。** 如需使用的 token 高信任系統中的資訊，請參閱 [建立及使用存取權杖中提供者主控高信任 SharePoint 增益集](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)。
  
    
    

 **SharePoint Add-ins來SharePoint資料存取使用 [低信任層級驗證系統](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)的參與涉及SharePoint、 Microsoft Azure Access Control Service (ACS)、 SharePoint Add-in、 遠端元件之間的安全性權杖 (格式為 [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/) ) 通過 OAuth流程中與在某些情況下，使用者的瀏覽器。** 所有這些至少包含下列兩種類型的權杖但有不同的流程根據增益集] 中的設計：
- **存取權杖:**包含在每個建立、 讀取、 更新或刪除SharePoint遠端元件的增益集 (CRUD) 要求。SharePoint驗證權杖，並提供服務要求。
    
  
- **重新整理權杖:**用來取得第一個存取權杖中 [快顯 Token 的流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)，以及取代在快顯 Token 的流程和低信任層級授權系統 [授權碼流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)的到期存取權杖。
    
  
根據哪些 OAuth 使用增益集的流程、 一或下列其他也是程序的一部分：
- **內容權杖:**使用中的快顯 Token 的流程來重新整理權杖以及需要從Azure ACS要求存取權杖的資訊提供遠端元件。
    
  
- **Authorizaton 程式碼：**不是權杖，但授權碼唯一的每一對使用者和應用程式。它會用於取得第一個的存取權杖，並重新整理權杖的授權碼流程。
    
  

## 了解存取權杖的處理
<a name="AccessTokens"> </a>

低信任層級授權系統中的存取 token 是Azure ACS所建立及傳送給您SharePoint Add-in遠端元件。(當本文撰寫時，用於SharePoint ACS 發出存取權杖有 12 個小時壽命但是，無法變更)。主要 **您SharePoint Add-in中的程式碼必須執行的動作** 如下：
  
    
    

- **要求存取權杖** 從Azure ACS。根據使用哪些 OAuth 流程、 增益集使用授權程式碼或其擷取內容權杖進行要求的資訊。
    
  
- **納入SharePoint每個 HTTP 要求的 token。** 要求權杖新增為 **Authorization**標頭。標頭的值是單字"承載"，後面接著空格，後面接著基底 64 編碼的存取權杖。
    
  
- 選擇性 (但建議) **快取存取權杖** 供重複使用在後續的要求。
    
  
- (選用) 轉寄要備份的存取 token，讓他們可以直接存取SharePoint結束系統。
    
  
使用伺服器端程式碼必須完成這些工作。如果您使用 managed 程式碼、 某些這些工作的範例程式碼是在 SharePointContext.cs (或.vb) 和 TokenHelper.cs (或.vb) 檔案的屬於Microsoft Office Developer Tools for Visual Studio。執行這些工作的 PHP 程式碼範例，請參閱 [瞭解並使用 SharePoint 2013 REST 介面](http://msdn.microsoft.com/en-us/magazine/dn198245.aspx)。
  
    
    

### 快取存取權杖
<a name="CacheAccessToken"> </a>

依據您SharePoint Add-in架構和主控平台，有數種 **方式來存取權杖快取** 的伺服器上。
  
    
    

- 在工作階段狀態
    
  
- 在應用程式狀態
    
  
- 在 [Windows Server AppFabric 快取](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx)或其等值的非-Microsoft OS。
    
  
-  [Microsoft Azure 快取服務](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx)或其對等資格非 Microsoft 雲端服務中。
    
  
- 在資料庫中
    
  
-  [Memcached](http://www.memcached.org/)系統中
    
  

> [!注意事項]
> 在大多數情況下，您將不能夠使用"AccessToken"為快取機碼一樣簡單的字詞，因為增益集必須保留不同的使用者和 SharePoint 伺服器陣列租用的權杖不同。如果增益集使用 [快顯 Token 流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)、 沒有可用來區分快取的語彙基元的 SharePoint 所提供的特殊 **CacheKey** 。本節說明問題有哪些以及您的應用程式未使用的快顯 Token 流程時執行的項目。
  
    
    

在工作階段狀態的存取權杖快取是細緻對於大部分的情況。如果遠端 web 應用程式會存取使用 OAuth (除了SharePoint) 其他服務及它快取的工作階段狀態的各種存取 token，務必使用不同的快取金鑰進行權杖;例如，而不是"AccessToken"使用"SharePoint_AccessToken"、"Facebook_AccessToken"、"SAP_Gateway_AccessToken"等。.(如果您不使用工作階段狀態或一些其他快取自動分隔每個使用者的快取，您就需要也 relativize 您機碼的使用者)。
  
    
    
如果應用程式存取多個SharePoint伺服器陣列或 online 租用，您可以使用SharePoint網域做為應用程式的主要快取機碼的一部分 ("SharePoint_ *< mydomain >*  。 sharepoint.com_AccessToken") 或使用伺服器陣列/租用戶領域 ("SharePoint_ *< realmGUID >*  _AccessToken")，這兩種可以讀取存取權杖。(您的程式碼必須回復讀取它的 token 的 Base 64 編碼方式。在 managed 程式碼、 TokenHelper.cs，或者.vb 檔案具有程式碼範例針對此目的而使用從 **Microsoft.IdentityModel.S2S.Tokens**和 **System.IdentityModel.Tokens**命名空間的類別。)使用時，另一個作法可用應用程式是快顯 Token 流程的下一個段落中所述。
  
    
    
以下是在其中某些案例中的需要快取存取您的應用程式 token 亦即是用於應用程式不同工作階段或在工作階段結束之後。例如，將應用程式可能設計可讓使用者排程後使用者已關閉的應用程式進行的動作。如果這些動作加上存取SharePoint，然後增益集必須以擷取存取權杖。在這種情況下， **您的應用程式必須保留的不同的不同使用者的存取 token** 。如果您使用快顯 Token 的流程，達到這個目的 SharePoint 傳遞至您SharePoint Add-in遠端元件時啟動增益集內容權杖中提供的快取金鑰的字串。如需此 **特殊的快取金鑰** 及如何使用它的詳細資訊，請參閱 [了解快取機碼](#CacheKey)。您也可以使用此字串至上一個段落中所述的案例。排程系統會有一些方法儲存其所需要的組態資料，例如排定的工作時就會執行與它應該執行的動作。使用此存放區來保留存取權杖快取金鑰。
  
    
    
如果您使用的跨工作階段快取也多個應用程式共用、 快取金鑰必須將，以及應用程式來的使用者和 SharePoint 領域 relativized。提供內容權杖快取機碼是唯一的應用程式的使用者和 SharePoint 領域。
  
    
    
 **如果您的應用程式使用的授權碼流程** ，則沒有任何內容權杖與因此，不特別進行快取機碼。在該情況下， **您需要建立的機碼的快取資料系統** 的一或多個下列根據何種可能的名稱衝突 relativized 可能會發生授與您的應用程式使用案例： 使用者、 SharePoint的領域和應用程式。您可以使用內部存取權杖宣告達到這個目的;例如 **nameId**和 **aud** (請參閱下表)。您的程式碼只可以串連字串或使用這些種子做為建立唯一的雜湊可作為快取機碼。
  
    
    
最後，如果您的應用程式會同時新增-僅在與使用者 + 增益集呼叫SharePoint，則它會有兩種不同的存取權杖。如此，您將需要不同的快取機碼。一旦您已決定基本快取機碼值，剛附加"_ 增加中-僅限"或"_ 增加-以 + 使用者"給它。
  
    
    

> [!注意]
> **不安全的作法是儲存在 cookie 中存取權杖。** 通常是很好的作法若要避免擁有透過瀏覽器存取 token 複雜。
  
    
    


### 轉寄至後端系統的存取權杖
<a name="ForwardTokenToBackend"> </a>

SharePoint Add-in可能會有未架設在相同網域的遠端 web 應用程式的後端伺服器。當後端伺服器必須執行上SharePointCRUD 作業時、 增益集執行速度如果這些作業可以直接從後端系統移至 [ SharePoint。幸運地是，網域務必僅當您的應用程式會取得存取 token 從 ACS。它具有權杖之後，它可將它轉接至後端服務及他們可以使用它來呼叫SharePoint 。這些系統中的程式碼將需要處理過期的存取權杖並將新的一部後的要求傳送到實際登錄與 ACS 的上層 web 應用程式。請參閱 [處理過期的存取權杖](#Expired)。這項技術僅用於應用程式本身的後端伺服器，未提供給外部 web 服務。此外，如果您使用這項技術，請考慮設計若要使用增益集僅在呼叫盡可能的後端服務。
  
    
    

### 處理過期的存取權杖
<a name="Expired"> </a>

存取權杖會在 (十二個小時到撰寫本文時，但該可以變更) 的幾個小時後到期。如果應用程式仍存取SharePoint存取權杖到期後SharePoint到期後的第一個要求導致 **401 Unauthorized**錯誤。您的程式碼有來處理此回應。另外，程式碼可以測試存取權杖的到期時間才能加以使用。 **您的程式碼使用的重新整理權杖從 ACS 取得另一個存取權杖。** 在快顯 Token 的流程，重新整理權杖隨附於增益集接收來自SharePoint開頭SharePoint其第一個工作階段的內容權杖。在的授權碼流程，它會傳遞至應用程式與第一個存取權杖。您的程式碼必須快取其因此時所需可用。重新整理權杖會持續幾個月及可保存在 cookie 中或在伺服器端存放區中。如需詳細資訊，請參閱 [了解處理及快取的重新整理 token](#RefreshToken)。
  
    
    

### 請參閱存取權杖的範例
<a name="ExampleAccessTokens"> </a>

本節範例，說明存取權杖，並顯示根據所用的授權原則如何與不同。
  
    
    

#### 請參閱低信任層級驗證系統的存取權杖

 **使用者 + 增益集 (英文) 原則**
  
    
    
以下是解碼 **ACS 所產生的範例中的使用者 + 增益集存取權杖** 使用電話SharePoint使用 [使用者 + 增益集 (英文) 原則](add-in-authorization-policy-types-in-sharepoint-2013.md)。可讀性之已新增空格。權杖遵守 [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1)通訊協定。JavaScript Object Notation (JSON)物件權杖中的稱為宣告組，請參閱表格 1 如需詳細資訊中宣告的屬性設定。請注意所有的值必須是小寫。(使用者 + 增益集存取權杖是在 [快顯 Token 的流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)和 [授權的程式碼流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)相同)。
  
    
    



```

{
 "aud": "00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss": "00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf": 1377549246,
 "exp": 1377592446,
 "nameid": "2303000085ff9abc",
 "actor": "964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73",
 "identityprovider": "urn:federation:microsoftonline"
}
```


**表 1: ACS 核發給使用者 + 增益集存取 token 的宣告**


|**宣告**|**描述**|**範例存取權杖中對應值**|
|:-----|:-----|:-----|
| `aud` <br/> |簡短的 「 對象 」，表示對象與語彙基元的主體。格式是 _audience principal ID_/ _SharePoint domain_@ _SharePoint realm_、  _audience principal ID_所在的 SharePoint 永久安全性主體識別碼。(請參閱 [Microsoft 產品應用程式主體常數](http://msdn.microsoft.com/en-us/library/hh629982%28v=office.12%29.aspx))。 <br/> 。 _SharePoint realm_是SharePoint Online租用或內部SharePoint伺服器陣列使用的存取權杖的 GUID 來存取。這個 GUID 做為Azure ACS此案例中的權杖發行者的領域識別碼。 <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `iss` <br/> |簡短的"簽發者 」。它所代表之主體的建立權杖。格式為 _Issuer GUID_@ _SharePoint realm GUID_。 <br/> 在低信任層級授權系統發行者會是Azure ACS和它的 GUID 是 **00000001-0000-0000-c000-000000000000** 。 <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `nbf` <br/> |簡短的"不是之前"。它所代表的時間權杖 *啟動*  正在有效午夜，從 1970 年 1 月 1 日起的秒數。根據預設，其設置為可用建立權杖。如需詳細資訊，請參閱 [使用 JWT 時間值](#JWTtimes)。 <br/> |1377549246 <br/> |
| `exp` <br/> |簡短的"到期日"。它所代表的權杖到期的時間。根據預設，這是 **nbf** 時間後的 12 個小時。如需詳細資訊，請參閱 [使用 JWT 時間值](#JWTtimes)。 <br/> |1377592446 <br/> |
| `nameid` <br/> |其發出的權杖之使用者的唯一識別碼。格式是根據身分識別提供者而異。在這個範例中，提供者是 Microsoft Online 但如果它是 Active Directory、 識別碼看起來"s-1-5-21-2127521184-1604012920-1887927527-415149"。 <br/> |2303000085ff9abc <br/> |
| `actor` <br/> |搜尋項目SharePoint伺服器陣列或租用的存取權之主體。它具有 @ _SharePoint realm_表單 _Client ID of Application_。 <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `identityprovider` <br/> |當登錄與網際網路指派號碼授權單位 (IANA) 的身分識別提供者的唯一名稱。 <br/> 增益集安裝至SharePoint Online、 值通常是此範例相同。 <br/> 增益集已安裝至內部部署伺服器陣列，是說過通常是內部身分識別提供者，例如"urn: office: idp:activedirectory"。 <br/> |microsoftonline: 同盟 urn: <br/> |
   
 **新增在-僅限原則**
  
    
    
以下是解碼 **ACS 所產生的增益集僅在存取權杖的範例** 使用電話SharePoint使用 [新增的-僅限原則](add-in-authorization-policy-types-in-sharepoint-2013.md)。可讀性之已新增空格。權杖遵守 [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1)通訊協定。請參閱表 2 如需詳細資訊中宣告的屬性設定。(新增-僅在原則不適用於使用 [授權碼流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)，因為他們沒有增益集資訊清單檔案，因此，無法要求權限以使用增益集僅在通話的應用程式。)
  
    
    



```

{
 "aud":"00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":1403304705,
 "exp":1403347905,
 "nameid":"c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73",
 "sub":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "oid":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "trustedfordelegation":"false",
 "identityprovider":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73"
}
```


**表 2: ACS 發出新增-僅在存取權杖宣告**


|**宣告**|**描述**|**範例存取權杖中對應值**|
|:-----|:-----|:-----|
| `aud` <br/> |與使用者 + 增益集權杖上述相同。 <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `iss` <br/> |與使用者 + 增益集權杖上述相同。 <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `nbf` <br/> |與使用者 + 增益集權杖上述相同。 <br/> |1403304705 <br/> |
| `exp` <br/> |與使用者 + 增益集權杖上述相同。 <br/> |1403347905 <br/> |
| `nameid` <br/> |增益集，而不是使用者，因為使用者的身分識別不它們與增益集僅在原則的唯一識別碼。格式為 _client ID_@ _SharePoint realm_。 <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `sub` <br/> |簡短的"subject"。是權杖，即希望SharePoint; 存取之主體的主旨在此例中的遠端 web 應用程式。物件識別碼用於值。請參閱 **oid**宣告下方。 <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce <br/> |
| `oid` <br/> |[物件識別碼] 的短。它的遠端 web 應用程式是在 Azure Active Directory 中的物件識別碼。在新增-僅在存取權杖、 主旨及物件識別碼是相同的值。 <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce <br/> |
| `trustedfordelegation` <br/> |布林值會指定SharePoint是否應信任SharePoint Add-in來驗證及授權使用者。因為不重要的使用者身分識別是新增-僅在通話中為 false。 <br/> |false <br/> |
| `identityprovider` <br/> |身分識別提供者的唯一名稱。由於它是增益集，而不是使用者所提供之身分識別、 ACS 是身分識別提供者。格式為 _ACS GUID_@ _SharePoint realm_。 <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
   

## 了解結構及內容權杖的處理
<a name="ContextTokens"> </a>

內容權杖只能用於低信任層級授權系統 [內容權杖的流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)。 **SharePointSharePoint Add-in啟動時在SharePoint，要求該Azure ACS建立內容權杖該SharePoint再登入到SharePoint Add-in遠端元件的傳遞。** 權杖傳遞做為邀請中呼叫 **SPAppToken**SharePoint從遠端元件的 [開始] 頁面上的隱藏的表單參數。使用用戶端秘密已知只以 ACS 開頭且SharePoint Add-in簽署權杖。 **內容權杖包含增益集使用的重新整理 token** ，以及從快顯 token， **要求存取權杖** 從 ACS 的其他資訊。(當本文撰寫時，用於SharePoint ACS 發出內容權杖有 12 個小時壽命但是，無法變更)。 **主要工作SharePoint Add-in中的程式碼** 如下所示：
  
    
    

- 使用用戶端機密的增益集來 **驗證內容權杖** 。
    
  
- **快取內容權杖** 或解壓縮、 和分開快取、 重新整理權杖及某些其他項目從內它。
    
  
- 使用的重新整理 token 及其他資訊 **要求存取權杖** 從 ACS (這是本身然後 「 快取)。
    
  
- 從內容權杖 **快取 CacheKey** 。
    
  

> [!重要]
> 使用者瀏覽至另一個頁面或重新整理] 頁面上或權杖會遺失前必須發生前兩個工作。例如，ASP.NET web 表單應用程式中，請考慮這些工作 **Page_Load**方法 (在設定格式化的條件的程式碼區塊只能在要求不是回傳時才會執行)。在 ASP.NET MVC 應用程式，請考慮這些工作的預設控制站方法。
  
    
    

如果您使用 managed 程式碼、 某些這些工作的範例程式碼是在 SharePointContext.cs (或.vb) 和 TokenHelper.cs (或.vb) 檔案的都包含在Microsoft Office Developer Tools for Visual Studio。您只需要簡單撥打 TokenHelper 類別的成員。例如，您的程式碼的用途具有下一行程式碼的第一個工作：
  
    
    



```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);
```

如何執行這些工作與 PHP 的範例，請參閱範例 [SharePoint 2013： 從 PHP 網站執行在 SharePoint 文件庫上的作業](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)。
  
    
    

### 快取的內容權杖或它的組件
<a name="CacheContextToken"> </a>

您可以 **快取** 整個內容權杖，或只重新整理語彙基元和其他某些內它是您的程式碼使用來取得存取 token， **在伺服器端或用戶端的存放區中** 的項目。簡化了程序，本文會假設快取內容權杖做為單位。
  
    
    

> [!重要]
> 我們提醒您一次因為它是非常重要： 請勿使用用戶端的 *存取*  權杖快取。它會使用僅適用於內容權杖的安全。
  
    
    

您必須同一個 [伺服器端快取選項](#CacheAccessToken)的如以上所列的存取權杖。用戶端選項包括 cookie 和 HTML 網頁上的隱藏的表單欄位。另一個作法是在工作階段快取中儲存內容權杖但儲存 **CacheKey** 內加以取得從用戶端上。
  
    
    
如果您的應用程式存取 SharePoint 結束工作階段後，然後未加入工作階段快取和用戶端快取是一個選項，因為以防原始的存取權杖已到期後的工作階段工作執行時重新整理權杖必須提供應用程式。在此案例中，您必須由多個使用者及/或 SharePoint 領域和/或應用程式共用的長期 (跨工作階段) 快取。如此，您的程式碼必須使用區分使用者、 SharePoint 領域和/或應用程式所述上方 [快取存取權杖](#CacheAccessToken)快取機碼。您可以 **使用特殊的快取機碼** 的內容權杖內達到這個目的就如同用於存取權杖。(請參閱 [了解快取機碼](#CacheKey)下)。
  
    
    

#### 了解快取機碼
<a name="CacheKey"> </a>

 **快取機碼是唯一的使用者、 使用者名稱簽發者、 增益集及SharePoint伺服器陣列或SharePoint Online租用戶組合不透明字串。** 已加密之前，它會具有下列格式，其中 _Realm_是內部SharePoint伺服器陣列或SharePoint Online租用的 GUID。
  
    
    
 _UserNameId_ +"、"+ _UserNameIdIssuer_ +"、"+ _ApplicationId_ +"、"+ _Realm_
  
    
    
快取機碼不包含網站 URL 資訊。每個SharePoint Online租用戶 (或內部SharePoint伺服器陣列) 具有唯一的領域，因此，快取索引鍵是唯一的使用者名稱、 使用者名稱簽發者、 應用程式及伺服器陣列的每個組合。在範例內容權杖下，加密的快取機碼值是：
  
    
    
 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`
  
    
    
由於您的應用程式可能會快取多個項目，例如存取權杖及內容權杖中具有相同的快取金鑰、 **考慮使用 「 快取金鑰為主體** 及任一附加相同的快取或附加為特定字串，例如"AccessToken"或"ContextToken"以該表單的完整的快取機碼所需的前面。 **另一個作法是建立一個類別** 的各種事項您想將快取，然後快取該類型的物件的屬性。 **第三個選項** 中，如果您為快取 **使用的資料庫** 是使用表格含有 CacheKey 欄及其他欄的快取的項目 (AccessToken、 ContextToken)。
  
    
    
您的應用程式沒有相同的快取用於它快取、 當然的每個項目。常見模式是快取工作階段快取、 內容權杖 (或從其在資料庫中，內部的重新整理語彙基元) 中存取權杖及 CacheKey 在 cookie 中。
  
    
    

### 若要取得存取權杖使用內容權杖
<a name="UseContextTokenToGetAccessToken"> </a>

 **以取得存取 token，您的應用程式將要求傳送至 ACS 直接。** 要求包含資訊的擷取自內容權杖 (和其他資訊) 的三個部分：
  
    
    

- 重新整理權杖
    
  
- 應用程式主體 GUID SharePoint。
    
  
- 領域 GUID 的增益集希望存取SharePoint伺服器陣列或SharePoint Online租用。
    
  
TokenHelper.cs (或.vb) 檔案具有建立此要求的程式碼。執行此動作的 PHP 程式碼範例，請參閱 [SharePoint 2013： 從 PHP 網站執行在 SharePoint 文件庫上的作業](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454)。
  
    
    
應用程式可以取得SharePoint租用或執行階段時的伺服器陣列的領域另一個到剖析從內容權杖。如果您使用 managed 程式碼，有 `TokenHelper.GetRealmFromTargetUrl`方法來取得領域。請務必使您的程式碼不會讓另一個網路呼叫再次取得快取的值。
  
    
    

### 取得新的內容權杖
<a name="GetNewContextToken"> </a>

 **如果您需要新的內容權杖** ，通常是因為重新整理權杖 (這包含在快顯權杖) 已過期， **您的程式碼可以取得一個新的重新導向至每個SharePoint網站中的特殊頁面在瀏覽器** -AppRedirect.aspx。兩個查詢參數已經附加至此頁面的 URL：
  
    
    

-  `client_id`: SharePoint Add-in您的用戶端識別碼。
    
  
-  `redirect_uri`: 您要在瀏覽器重新導向取得新的內容權杖之後的 URI。SharePoint會張貼內容權杖此 uri。一般而言，這是同一頁上、 控制站方法或 web 服務方法要求新的內容權杖。此值必須是 URL 編碼。
    
  
下列範例顯示 URL 的結構:
  
    
    



```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>
```

以下是範例進行中使用 TokenHelper 檔案的 ASP.NET 要求：
  
    
    



```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));
```


### 查看內容權杖的範例
<a name="ExampleContextToken"> </a>

以下是範例內容權杖。在頂端的小型JavaScript Object Notation (JSON)物件包含語彙基元的相關的中繼資料。這些屬性的存取權杖 (請參閱上述) 相同。 **Alg** 屬性的值是用來產生 ACS 附加至權杖的簽章演算法的名稱。請參閱如需詳細資訊的 token 承載中的屬性表 3。請注意所有的值必須是小寫。(可讀性的已新增空格)。
  
    
    

```
{"typ":"JWT","alg":"HS256"}
.
{
 "aud":"a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":"1335822895",
 "exp":"1335866095",
 "appctxsender":"00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "appctx":"{
            \\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\",
            \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"
           }",
 "refreshtoken":"IAAAAC1Lv5w0OrcFAmJx0xk6aaBdhgsw3VPnPzNEDAWypTHtCYytZ2/dBBUKj+HLK8YB3IUCUfDxYpAque
NHKtgs4rYJJ5AegQpNMOJR1yYK8ngivQx0oetj7aSPuGVb+k6at6G0Kx5LZ5vhxkAq8iUSwu8p4L2cvNMzDF1mDKfMivqxgrIZkr2nbf9as0SJFL6VG5hZnDE4HKq
xJnejSW3umatKM4fsfY1MClVCxrkXb2EQ8H/TmwaJc388YW063GEVUS/3BTSgSIRBKQUmXJuJ6BZY7WTm84LaGrx3mIjnUTM/jnqPoPG55JbCC9sS/MeGNPtzPPCDg
6Vv7dVhQ1Dq5Y3fQ65e9LpJ580jCgzYYvpIFT+Wx5V+17mjY2T8wug04K2ts87Znsr+GfFCorf7NS/lj5HjoxRAQ2tva/8dwguSLwxcUwi/Q9MbpR0NNtlpwVazqi9O
hJ4Df7gVhUDdJ0Dtc6aFCPbl5ZLDDRs42xK2", 
 "isbrowserhostedapp": "true"
}
```

 **Aud** 、 **iss** 、 **nbf** 、 及 **exp** 宣告確實會存取權杖相同如前文所述。下表說明 **appctxsender** 、 **appctx** 、 **CacheKey** 、 **SecurityTokenServiceUri** 、 **refreshtoken** 及 **isbrowserhostedapp** 宣告。
  
    
    

**表 3。內容權杖宣告及資訊**


|****宣告****|****描述****|****範例內容權杖中對應值****|
|:-----|:-----|:-----|
|AUD <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
|iss <br/> ||00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
|nbf <br/> ||1335822895 <br/> |
|EXP <br/> ||1335866095 <br/> |
|appctxsender <br/> |簡短的 「 應用程式內容寄件者 」。它所代表之應用程式的內容權杖傳送至SharePoint Add-in。 <br/> 它具有 @ _SharePoint realm_、 表單 _GUID of principal_ _GUID of principal_所在之應用程式主體; 常數識別碼SharePoint、 Exchange 2013、 Lync 2013或工作流程。 <br/> |00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
|appctx <br/> |簡短的 「 增益集內容 」。是包含 **CacheKey** 及 **SecurityTokenServiceURI** JSON 物件。 <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\" <br/> |
|CacheKey <br/> |可以作為在任何索引鍵的唯一值索引鍵/值結構化的快取儲存及擷取內容權杖。它也無法使用中資料庫的資料列的索引鍵資料欄的值。 <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw= <br/> |
|SecurityTokenServiceURI <br/> |發出服務權杖的 URI。 <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2 <br/> |
|refreshtoken <br/> |重新整理語彙基元的增益集。 <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6… <br/> |
|isbrowserhostedapp <br/> |會指定是否傳送至增益集包含內容權杖要求即將從瀏覽器 (true) 或從遠端事件接收器 (false) **Boolean** ] 欄位。 <br/> |true <br/> |
   

### 使用內容權杖來限制只有 SharePoint 使用者存取
<a name="UseContextTokenToLimitAccess"> </a>

如果您想要限制存取您的遠端元件，例如SharePoint使用者 WCF 服務您的程式碼可以只需驗證 HTTP 要求內容的權杖。(如果您使用 managed 程式碼，您可以只是呼叫 **TokenHelper.ReadAndValidateContextToken()**)。您的程式碼可以驗證動作項目宣告的權杖啟動與 **00000003-0000-0ff1-ce00-000000000000** ，如果您想要確定它是SharePoint (和未，例如Exchange 2013、 Lync 2013或工作流程)。如果您想要做其他驗證需要 SharePoint，例如限制使用中SharePoint、 特定角色的使用者存取呼叫可以快取您完成此驗證特定使用者 (使用內容權杖 **CacheKey**) 事實使其此只有一次。
  
    
    

## 了解處理與快取的重新整理 token
<a name="RefreshTokens"> </a>

 **重新整理權杖隨附內容權杖該SharePoint張貼至 web 應用程式啟動時。** 重新整理權杖是您SharePoint Add-in無法解密加密的 token。 **您的程式碼會使用它** ，以及 **以取得新的存取 token 目前的存取權杖到期時** 的其他資訊。它也用來取得 *第一次*  存取權杖 [快顯 Token 流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中。(當本文是書寫、 ACS 發出時重新整理語彙基元的SharePoint有 6 個月、 壽命但是，無法變更)。
  
    
    
由於存取權杖持續小時數 (目前 12) 和使用者取得一份新每次他啟動SharePoint從您SharePoint Add-in 、 您只需要重新整理權杖這些情況：
  
    
    

- 使用者有時間執行的工作階段與您的增益集在其中增益集時呼叫SharePoint時數 (目前超過 12) 則可啟動之後。
    
  
- 增益集設計可讓使用者排程增益集在工作階段結束之後存取SharePoint功能。
    
  
 **案例需要增益集來快取的重新整理 token** ，及第二個案例需要不同工作階段是永久性的伺服器端快取。快取的選項是這些 [存取 token](#CacheAccessToken)的相同，在快顯 Token 的流程，您可以使用 [的內容權杖快取機碼](#CacheKey)。(在快顯 Token 的流程，您通常只是快取內容權杖。包含您要取得新的存取權杖的重新整理 token 和其他資訊。 [授權碼流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中有無內容權杖，因此快取的重新整理 token 本身。)
  
    
    
如果您快取與增益集的特定使用者的工作階段會套用到儲存區中的重新整理 token，請務必取代的最新的重新整理 token。在同時雲端託管和授權的程式碼流程、 使用者取得新的重新整理 token 每個時間一次啟動增益集。
  
    
    
如果已過期的重新整理 token，針對新的存取權杖 ACS 要求會導致 **401 Unauthorized**錯誤。增益集應該回應這項錯誤取得新的重新整理權杖，並使用以取得新的存取 token。(由於加密的重新整理 token，您的程式碼無法檢查才能使用其到期日。)在快顯 Token 的流程、 增益集取得新的重新整理 token 來 [取得新的內容權杖](#GetNewContextToken)。 中的授權碼流程、 增益集取得新的重新整理 token 來重新啟動流程。主要是針對您的程式碼應該回應錯誤的 [了解 OAuth 流程增益集的要求即時的權限](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow)所述要將使用者重新導向至SharePoint OAuthAuthorize.aspx 頁面。
  
    
    

## 了解處理授權代碼
<a name="Authcodes"> </a>

 **中的授權碼流程，授權程序開頭為 ACS 問題，要求SharePoint、 哪些SharePoint則傳遞給遠端應用程式授權碼** 做為查詢參數。授權程式碼是以每對使用者和遠端應用程式的唯一的。(當本文撰寫時、 SharePoint的 ACS 發行授權碼有 5 分鐘的壽命但是，無法變更)。在 **您的應用程式必須取得查詢參數的授權程式碼和使用它來存取權杖的 ACS 要求中** 邏輯。如果您使用 managed 程式碼，建立語彙基元的範例程式碼是 TokenHelper.cs (和.vb) 檔案中。讀取查詢參數的程式碼範例會在 [取得存取SharePoint頁面背後的程式碼範例](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default)。ACS 無效的授權碼後面發出存取 token，讓它一次只能使用及沒有在快取它的點。
  
    
    

## 使用 JWT 時間值
<a name="JWTtimes"> </a>

 **nbf**和 **exp**宣告處於 [JWT 規格](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl)所指定的格式。他們是撰寫成 1970 年 1 月 1 日起的秒數。以 C# 您可以將這些值使用下列程式碼、  _jWTTimeStamp_所在的 token，例如 1335822895 的值。
  
    
    

```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);

```


## 疑難排解 token 處理
<a name="Troubleshooting"> </a>

免費的 [Fiddler 工具](http://www.telerik.com/fiddler)可用來擷取遠端元件的增益集所傳送至SharePoint的 HTTP 要求。沒有自動解碼語彙基元要求中的 [免費延伸至工具](https://github.com/andrewconnell/SPOAuthFiddlerExt) 。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [建立 SharePoint 增益集使用低信任層級授權](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
- 使用 managed 程式碼和 TokenHelper 的程式碼範例，請參閱 [SharePoint 2013: Hello World 遠端增益集使用 CSOM](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf)與 [SharePoint 增益集範例 pack](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
- 使用 REST 的程式碼範例會從 PHP 增益集呼叫:  [SharePoint 2013： 從 PHP 網站執行在 SharePoint 文件庫上的作業](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)
    
  

