---
title: 建立及使用存取權杖中提供者主控高信任 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: cb3264d4-41a6-498f-a408-75b077566051
---


# 建立及使用存取權杖中提供者主控高信任 SharePoint 增益集
了解高信任 SharePoint 增益集與您的程式碼與它們的運作方式的存取權杖的角色。
> [!重要]
> **本文是完全相關的存取權杖使用高信任授權系統都不 ACS system。** 安全性權杖 ACS 系統中之使用者的相關資訊，請參閱 [處理安全性權杖的提供者主控低信任層級 SharePoint 增益集](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)。
  
    
    

 **SharePoint Add-ins用來存取SharePoint [高信任授權系統](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)已傳遞存取權杖** (以 [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)格式) 至每個建立SharePoint讀取、 更新或刪除 (CRUD) 要求。SharePoint驗證權杖，並提供服務要求。本文提供如何您的程式碼會建立及通過存取 token 相關資訊。
在 **您SharePoint Add-in遠端元件會建立存取權杖** 的高信任授權系統。如果遠端元件會使用其伺服器端程式碼的 managed 程式碼，大部分的編碼適用於建立權杖中 SharePointContext.cs (或.vb) 完成您和 TokenHelper.cs (或.vb) Office Developer Tools for Visual Studio中所包含的檔案。由於高信任SharePoint Add-in的客戶有內部部署SharePoint，他們是可能不 averse 使用 ASP.NET、 IIS 和 Windows 伺服器主控堆疊做為遠端元件。您應該考慮使用此堆疊，因為這兩個產生的檔案會儲存您的編碼及測試人力許多。若遠端元件必須使用非.NET 語言，並連線至網際網路的遠端元件及SharePoint伺服器陣列，則應考慮使用 [低信任授權單位系統](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)，而不高信任層級。Microsoft Azure Access Control Service (ACS)系統中所有的 token 的情況下即可 ACS，因此也一命許多的人力。本文的其餘部分的主要目的非.NET 遠端元件以建立SharePoint Add-ins和使用高信任授權單位系統的開發人員提供指引。它也可以提供適用於偵錯一些重要資訊。NET 型SharePoint Add-ins使用高信任系統。
  
    
    


## 了解存取權杖的處理
<a name="AccessTokens"> </a>


> [!注意事項]
> 閱讀本文時請記住，特別是關於您的程式碼必須執行的工作]，如果您使用 managed 程式碼， Microsoft Office Developer Tools for Visual Studio將新增至每個SharePoint Add-in專案的兩個產生程式碼檔案、 SharePointContext.cs (或.vb) 和 TokenHelper.cs (或.vb) 可讓您執行大部分的這些工作。您的應用程式權杖通常處理程式碼包含少數撥給這些檔案中的類別。本主題中的詳細資料是以協助開發人員未使用 managed 程式碼 (和可協助使用者可以使用的疑難排解問題權杖)。> 在許多語言和平台的 OAuth 文件庫的連結都是：>  [2.0 OAuth](http://oauth.net/2/)捲動到 **用戶端文件庫** 。> 您可以找到更多搜尋 [github](https://github.com/)的"OAuth 2" 和"JSON web 語彙基元"(不含引號)。
  
    
    

主要的 **程式碼所需的事項** 不要如下：
  
    
    

1. **建立存取權杖。** 建立此 token 的任務而異的遠端 web 應用程式是否會新增-僅在呼叫SharePoint、使用者 + 增益集的呼叫，或兩者。(如通話的兩種類型的相關資訊，請參閱 [SharePoint 2013 中的增益集授權原則類型](add-in-authorization-policy-types-in-sharepoint-2013.md))。
    
    如果增益集產生使用者 + 增益集的通話，然後建立存取權杖包含下列子任務：
    
1. 建立執行者權杖識別SharePoint Add-in且會告知SharePoint委派使用者驗證及授權增益集，並編碼的基底 64 編碼。在下表 2 的詳細宣告及執行者語彙基元的結構。編碼及簽署憑證的詳細資料都 [編碼和簽署權杖](#EncodingTokens)下。
    
  
2. 登入認證 x509 憑證SharePoint伺服器陣列管理員已設定為信任的SharePoint為執行者權杖。
    
  
3. 動作項目權杖納入存取權杖。
    
  
4. 新增其他必要的宣告來存取權杖。下表 1 中所宣告及結構語彙基元的詳細資訊。
    
  
5. 編碼與 base 64 存取權杖。編碼及簽署憑證的詳細資料都 [編碼和簽署權杖](#EncodingTokens)下。
    
  

    如果增益集產生新增-僅在通話，然後您的程式碼才需執行動作之第一個兩個這些子任務。動作項目權杖做為存取權杖。
    
    如果增益集產生一些使用者 + 增益集呼叫和有些新增-僅在呼叫，它必須先建立簡單的動作項目新增-僅在通話的權杖和使用者 + 增益集呼叫的較大的巢狀式存取權杖。相同的存取權杖後無法用於兩者。
    
  
2. **SharePoint每個 HTTP 要求中包含存取權杖。** 要求權杖新增為 **Authorization**標頭。標頭的值是單字"承載"，後面接著空格，後面接著基底 64 編碼存取權杖。
    
  
3. (選用) **快取存取權杖** 供重複使用在後續的要求。
    
  
使用伺服器端程式碼必須完成這些工作。如果這些工作的一些為 SharePointContext.cs (或.vb) 而 TokenHelper.cs (或.vb) 檔案的使用 managed 程式碼、 範例程式碼，產生Microsoft Office Developer Tools for Visual Studio 。
  
    
    

### 快取存取權杖
<a name="CacheAccessToken"> </a>

建立權杖之後，它可重複使用在稍後撥給SharePoint直到過期。依據遠端元件的架構和主控平台，有數種 **方式來存取權杖快取** 的伺服器上。
  
    
    

- 在工作階段狀態
    
  
- 在應用程式狀態
    
  
- 在 [Windows Server AppFabric 快取](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx)。
    
  
- 在資料庫中
    
  
-  [Memcached](http://www.memcached.org/)系統中
    
  
如果由不同的使用者工作階段，例如的應用程式快取共用快取儲存然後務必使用快取索引鍵是唯一加入工作階段。如果透過多種應用程式共用快取，您的程式碼也必須 **relativize 快取機碼** ，以及該變數。它也有可能增益集存取不同SharePoint伺服器陣列。需要不同的存取權杖的每個，所以在該案例中快取金鑰者需要 relativize 伺服器陣列。完全您可能需要快取根據一或多個使用者識別碼、 應用程式識別碼和SharePoint網域或領域的機碼。請考慮使用類似SharePoint Add-ins使用低信任層級授權系統所使用的快取系統。如需詳細資訊，請參閱 [了解快取機碼](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey)。基本上，您將串連一或多個下列三個識別碼 (及 (選用) 雜湊成較短的字串結果) 做為快取機碼。
  
    
    

### 處理過期的存取權杖
<a name="CacheAccessToken"> </a>

您要 **存取權杖有到期時間** 您的程式碼可以設為任何值。如果增益集建構新存取權杖的每個要求，然後每個語彙基元只有存留時間足以至來驗證SharePoint、 不得超過幾秒鐘除非客戶的 LAN 通常已經堵塞。您無法將到期時間設定為 years 未來，但即使在其高信任層級增益集的設計用"所有內部"案例中，有一些危險的被竊存取 token。所以您應該考慮設定到期日不超過幾個小時。(如果您使用 managed 程式碼、 TokenHelper.cs [或.vb] 檔案中的範例權杖建立程式碼會設定到期日 12 小時。)
  
    
    
如果您SharePoint Add-in使用者的工作階段持續的快取的存取 token，然後將第一個要求為SharePoint週期多於權杖的到期時間之後會導致 **401 Unauthorized**錯誤。您的程式碼有來處理此回應。或者，它可以測試存取權杖的到期時間才能加以使用。 **您的程式碼應該回應藉由建立新的存取權杖到期的存取權杖** 及重複失敗的要求。
  
    
    

## 了解存取權杖的結構
<a name="Structure"> </a>

以下是 **高信任SharePoint Add-in所產生的存取權杖的範例**;尤其是由屬於SharePoint Add-in專案範本建立的Office Developer Tools for Visual Studio中Visual Studio 2013年更新 2 TokenHelper.cs (或.vb) 檔案中的範例程式碼產生此 token。已解碼權杖和可讀性之已新增空格。用於高信任系統的存取 token 是符合 [[MS-SPS2SAUTH]: OAuth 2.0 Authentication Protocol: SharePoint Profile](http://msdn.microsoft.com/library/05f9759b-0fa4-45ff-bd4b-0d7d254e7010.aspx)，也稱為伺服器對伺服器或 S2S 通訊協定。協助開發人員使用 managed 程式碼偵錯高信任SharePoint Add-ins，並提供一些指南使用如何建構語彙基元的相關的其他語言的開發人員提供的此資訊。
  
    
    
 **如果增益集撥打SharePoint使用 [使用者 + 增益集原則](add-in-authorization-policy-types-in-sharepoint-2013.md)來產生此存取權杖。** 如果使用 [增益集僅在原則](add-in-authorization-policy-types-in-sharepoint-2013.md)中之增益集，而且它進行新增-僅在呼叫SharePoint，然後執行者權杖 (也就是子權杖內使用者 + 增益集存取權杖如下所述)，會成為存取權杖 (且有無父權杖)。
  
    
    

> [!注意事項]
> 請注意您的程式碼會建立高信任存取 token 不同時要使用低信任層級驗證系統建立Azure ACS ：> 因為未簽署的存取權杖中使用者 + 來自高信任層級增益集的增益集呼叫 **alg**宣告標頭中的為"none"。> 增益集 URL **aud**值在此範例中的為正常的高信任系統在內部部署伺服器。> 沒有 **identityprovider**宣告，但沒有 **nii** (名稱識別簽發者) 具有相同種類的值為 low 信任授權系統中所用的 **identityprovider**宣告存取 token。(如需此值時 SAML 型身分識別提供者，請參閱 [安全性中 SharePoint 增益集組件 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)及 [使用 SharePoint 增益集使用 SAML 與 FBA Sites in SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)的 Steve Peschka 部落格文章。> 沒有 **actor**宣告，但沒有包含基底 64 編碼 inner 權杖使用 12 小時壽命 **actortoken**宣告。
  
    
    

標頭有兩個屬性。"輸入"是權杖的類型。遠端 web 應用程式中的程式碼應該一律將此值設"JWT"。"alg"是用來登入 token 的演算法。由於未簽署中使用者 + 來自高信任層級增益集的增益集呼叫的外圍 token，將此值為"none"。請參閱表格 1 的內文部分中的值的相關資訊的高信任存取 token。
  
    
    



```

{"typ":"JWT", "alg":"none"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"s-1-5-21-2127521184-1604012920-1887927527-2963467",
 "nii":"urn:office:idp:activedirectory",
 "actortoken":"6sMZhbw … [remainder of long base 64 string omitted] … "
}

```

下表提供一些指導為何 **您的程式碼應該包含在存取權杖和新值來設定這些屬性** 。如果您使用 managed 程式碼、 SharePointContext.cs (或.vb) 和 TokenHelper.cs (或.vb) 檔案為您建立的 token。例如，您的程式碼讓單一呼叫 **SharePointContext.CreateUserClientContextForSPHost**方法。依次 **TokenHelper**類別中建構則包含每個通話中其存取權杖的呼叫方法對SharePoint會傳回由 **SharePointContext.CreateUserClientContextForSPHost**SharePoint用戶端快顯物件。
  
    
    

**表格 1： 應用程式發出的存取權杖宣告**


|**宣告**|**描述**|**範例存取權杖中對應值**|
|:-----|:-----|:-----|
| `aud` <br/> |簡短的 「 對象 」，表示對象與語彙基元的主體。格式是 _audience principal ID_/ _fully qualified SharePoint domain_@ _SharePoint realm_。 <br/> SharePoint Add-in的主要對象一定是 00000003-0000-0ff1-ce00-000000000000。 <br/> 由於高信任SharePoint Add-ins通常都使用完全內部部署案例中，完整的 SharePoint 網域名稱通常是只是伺服器名稱。 <br/>  _SharePoint realm_是存取權杖用來存取] (或如果伺服器陣列已設定租用的租用的 GUID) 的內部部署SharePoint伺服器陣列的 GUID。 <br/> 在 SharePoint 伺服器上執行 PowerShell **Get-SPAuthenticationRealm**指令程式來尋找 SharePoint 領域。然後使用您的程式碼中直接或儲存在其中您的程式碼可以讀取，例如應用程式設定檔。設定] 區段中的 web.config 檔案。或者，您的程式碼以動態方式可以探索 SharePoint 領域於執行階段時傳送至SharePoint的驗證挑戰。請參閱 `GetRealmFromTargetUrl`方法 TokenHelper.cs (或.vb) 檔案中如的方式作法是在 managed 程式碼範例。 <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `iss` <br/> |簡短的"簽發者 」。它所代表之主體的建立權杖。格式為 _Issuer GUID_@ _SharePoint realm GUID_。 <br/> 在高信任系統本身的增益集是簽發者，讓用戶端識別碼SharePoint Add-in通常用於簽發者 GUID。 *簽發者識別碼的所有字母必須都為小寫。*  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `nbf` <br/> |簡短的"不是之前"。它所代表的時間權杖 *啟動*  正在有效午夜，從 1970 年 1 月 1 日起的秒數。您的程式碼應該設定為 [建立權杖可用。 <br/> |1403212820 <br/> |
| `exp` <br/> |簡短的"到期日"。它表示權杖到期，以秒為單位的午夜，從 1970 年 1 月 1 日的時間。 <br/> |1403256020 <br/> |
| `nameid` <br/> |其發出的權杖之使用者的唯一識別碼。格式是根據身分識別提供者而異。在這個範例中，提供者是 Active Directory。 <br/> |s-1-5-21-2127521184-1604012920-1887927527-2963467 <br/> |
| `nii` <br/> |簡短的 「 名稱識別碼簽發者 」。當登錄與網際網路指派號碼授權單位 (IANA) 的身分識別提供者的唯一名稱。 <br/> 高信任SharePoint Add-in，為通常是內部身分識別提供者，如本範例所示的 Active Directory 等。 <br/> |urn: office: idp:activedirectory <br/> |
| `actortoken` <br/> |基底 64 編碼 JWT 權杖識別SharePoint Add-in且會告知SharePoint設為信任的增益集不論哪些使用者執行增益集。 <br/> |請參閱下方。 <br/> |
   
 **下列顯示解碼的 **actortoken**。** 頂端的小型JavaScript Object Notation (JSON)標頭物件包含的 token，包括的語彙基元和用以簽署它的演算法類型相關的中繼資料。標頭的 **x5t**屬性是權杖的做為正式發行者的 x.509 憑證的指紋從摘要。若要建構這個值，您的程式碼會執行下列動作：
  
    
    

1. 取得的憑證指紋的位元組陣列 (不 string) 版本。這是憑證的 sha-1 摘要。(在 managed 程式碼、 達成此目的是使用 [GetCertHash()](http://msdn2.microsoft.com/ZH-TW/library/4f9acc3f)方法。您將需要相等所使用的語言中的某個項目)。
    
  
2. 使用基底 64 URL 編碼編碼的位元組陣列。
    
  
3. **x5t**屬性的值設為編碼的摘要。
    
  
表 2 說明您的程式碼必須包含權杖以及設定其值的內文中的宣告。
  
    
    



```

{"typ":"JWT","alg":"RS256","x5t":"7MjK99QvkVdwz6UrKldx8AG7ydM"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "trustedfordelegation":"true"
}

```


> [!注意事項]
> 如果使用 [增益集僅在原則](add-in-authorization-policy-types-in-sharepoint-2013.md)高信任層級增益集，而且它進行SharePoint新增-僅在呼叫，如下所示的 token 是實際存取權杖。沒有外圍權杖。此外，沒有任何 **trustedfordelegation**宣告，因為使用者的權限已新增-僅在通話的無關。
  
    
    


**表 2: 憑證發行 actortoken 宣告**


|**宣告**|**描述**|**範例存取權杖中對應值**|
|:-----|:-----|:-----|
| `aud` <br/> |父存取權杖相同。 <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `iss` <br/> |意義相同父存取 token，但簽發者 GUID 不是 web 應用程式的用戶端識別碼。它是憑證的 GUID。雖然應用程式中的程式碼建構執行者權杖、 憑證會被視為動作項目權杖的發行者。 <br/> 請注意在此範例中的發行者 GUID 容易記住的 GUID 字串時她為受信任權杖發行者SharePoint中註冊的 x.509 憑證使用伺服器陣列管理員。這是常見時相同的憑證則用為執行者 token 簽署者伺服器陣列上所有的高信任SharePoint Add-ins 。系統管理員也可以選擇不同的每個SharePoint Add-in的憑證。在此情況下，其可以使用不同隨機產生的 Guid 憑證。 *簽發者 GUID 中的所有字母必須都為小寫。*  <br/> |11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `nbf` <br/> |如父存取權杖所示的相同意義。 <br/> |父存取權杖值相同。 <br/> |
| `exp` <br/> |如父存取權杖所示的相同意義。 <br/> |父存取權杖值相同。 <br/> |
| `nameid` <br/> |唯一識別碼SharePoint Add-in，因為它是高信任系統中的"動作項目的"。格式是 _client_ID_@ _SharePoint_realm_ <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `trustedfordelegation` <br/> |布林值會指定SharePoint是否應信任SharePoint Add-in來驗證及授權使用者。這是高信任系統中通常則為 true。 <br/> 不包括此宣告新增-僅在呼叫高信任系統中。 <br/> |true <br/> |
   

## 編碼和登入 token
<a name="EncodeTokens"> </a>

 **您的程式碼已新增所有屬性及值標頭和本文 JSON 物件之後已編碼它們、 合併它們到 JWT 和簽署人。** 下列是步驟。
  
    
    

1. 使用基底 64 URL 編碼編碼標頭。
    
  
2. 使用基底 64 URL 編碼編碼承載。
    
  
3. 將編碼的本文串連之後使用的編碼標頭"。"傳來這些字元。這是 JWT。
    
  
4. 建立使用 JWT 與憑證的私密金鑰 SHA256 簽章。
    
  
5. 使用基底 64 URL 編碼編碼簽章。
    
  
6. 具有附加至 JWT 結尾的簽章"。"兩者之間的字元。
    
  
搭配新增-僅在來電動作項目 token 的動作項目權杖用作存取權杖。沒有外圍權杖。用於使用者 + 增益集呼叫存取 token，如上述步驟可用來建構動作項目語彙基元的 **actortoken**屬性的值為然後插入至本文的存取權杖。然後編碼及建構而來，與上述的前三個步驟的完整存取 token，但其未簽署，因此的其餘步驟不會用於外圍權杖。
  
    
    

## 疑難排解存取權杖
<a name="Trouble"> </a>

免費的 [Fiddler 工具](http://www.telerik.com/fiddler)可用來擷取遠端元件的增益集所傳送至SharePoint的 HTTP 要求。沒有自動解碼語彙基元要求中的 [免費延伸至工具](https://github.com/andrewconnell/SPOAuthFiddlerExt) 。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [建立 SharePoint 增益集使用高信任授權](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)
    
  
- Steve Peschka 的 [安全性中 SharePoint 之增益集組件 7](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-7.aspx)
    
  
- Steve Peschka 的 [安全性中 SharePoint 之增益集組件 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)
    
  
- Steve Pescka 的 [使用 SharePoint 增益集使用 SAML 與 FBA Sites in SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)
    
  
- 吳 Evan 的 [高信任 SharePoint 增益集在非-Microsoft 平台](http://blogs.msdn.com/b/kaevans/archive/2014/07/14/high-trust-sharepoint-apps-on-non-microsoft-platforms.aspx)
    
  
-  [2.0 OAuth](http://oauth.net/2/)
    
  

