

# 秘訣與常見問題集： OAuth 和 sharepoint 的遠端應用程式
本主題現在已過時。請參閱下列連結，從而了內容其中是現在位於。
 * **適用於:*** 
  
    
    


||
|:-----|
|**本文內容**          [擷取應用程式資訊](#AppInfo)           [AppManifest.xml 檔案](#Manifest)           [Web.config 檔案](#Webconfig)           [Url 及安全通訊端階層 (SSL)](#URL)           [應用程式重新導向頁面](#Redirect)           [重新導向 Uri](#RedirectURI)           [OAuth 權杖](#Tokens)           [權限與權限要求範圍](#Perm)           [OAuth 驗證原則類型](#Policy)           [偵錯](#Debugging)           [高信任度應用程式 (伺服器對伺服器應用程式) 內部部署](#S2S)           [OAuth 相關的其他問題](#Misc)           [社交功能](#Social)           [的其他資源](#AR)|
   

## 擷取應用程式資訊
<a name="AppInfo"> </a>

此內容已移至 [擷取註冊增益集及增益集主要資訊](register-sharepoint-add-ins-2013.md#Retrieve)。
  
    
    

## AppManifest.xml 檔案
<a name="Manifest"> </a>

此內容已移至：
  
    
    

-  [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [SharePoint 的結構描述參考 (英文) 的資訊清單的應用程式](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  

### < 首頁 > 元素中的 URL 是什麼？

此內容已移至：
  
    
    


  
    
    
>  [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

  
    
    
>  [首頁元素](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)
    
  

### 元素與屬性的 AppManifest.xml 檔案有哪些？

此內容已移至 ＜  [SharePoint 相關應用程式資訊清單的結構描述參考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
  
    
    

## Web.config 檔案
<a name="Webconfig"> </a>

This content has been moved to  [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md), especially the section  [在 web.config 和 AppManifest.xml 檔案中輸入的登錄值](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

### Web.config 檔案中的應用程式設定有哪些？

此內容已移至 [在 web.config 和 AppManifest.xml 檔案中輸入的登錄值](register-sharepoint-add-ins-2013.md#EditConfigFiles)。
  
    
    

## Url 及安全通訊端階層 (SSL)
<a name="URL"> </a>

此內容已移至：
  
    
    

-  [Miscellanous SSL 和高信任度應用程式中的網域相關的錯誤](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Miscellanous SSL 和 ACS 應用程式中的網域相關的錯誤](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### 「 我的 web 應用程式具有 SharePoint 回通訊問題。我應該檢查什麼？

此內容已移至：
  
    
    

-  [Miscellanous SSL 和高信任度應用程式中的網域相關的錯誤](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Miscellanous SSL 和 ACS 應用程式中的網域相關的錯誤](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### 項目 Url 應該我硬式編碼成 「 我的應用程式，以指向我雲端伺服器？

此內容已移至：
  
    
    

-  [Miscellanous SSL 和高信任度應用程式中的網域相關的錯誤](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Miscellanous SSL 和 ACS 應用程式中的網域相關的錯誤](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### 應註冊的 CNAME 別名或主控應用程式的實際基礎 URL 吗？

此內容已移至：
  
    
    

-  [Miscellanous SSL 和高信任度應用程式中的網域相關的錯誤](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Miscellanous SSL 和 ACS 應用程式中的網域相關的錯誤](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### 我收到錯誤"基礎連接已關閉： 無法建立 SSL/TLS 安全通道的信任關係。 」我該怎麼辦？

本內容已經移動至 [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed)。
  
    
    

## 應用程式重新導向頁面
<a name="Redirect"> </a>

此內容已移至 [取得新的內容權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。
  
    
    

### 什麼我應該使用的應用程式重新導向頁面？

此內容已移至 [取得新的內容權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。
  
    
    

### 如何取得內容權杖使用 app 重新導向頁面？

此內容已移至 [取得新的內容權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。
  
    
    

### 如何在 URL 中使用 appredirect 頁面？

此內容已移至 [取得新的內容權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。
  
    
    

## 重新導向 Uri
<a name="RedirectURI"> </a>

This content has been moved to  [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md).
  
    
    

### 是否需要重新導向 URI 吗？

This content has been moved to  [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md).
  
    
    

## OAuth 權杖
<a name="Tokens"> </a>

此內容已移至：
  
    
    

-  [處理安全性權杖的提供者主控低信任層級 SharePoint 增益集](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)
    
  
-  [建立及使用存取權杖中提供者主控高信任 SharePoint 增益集](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
    
  

### 何謂內容權杖？

此內容已移至 [了解結構及內容權杖的處理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens)。
  
    
    

### 存取權杖為何？

此內容已移至 [了解存取權杖的處理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)。
  
    
    

### 重新整理權杖為何？

此內容已移至 [了解處理與快取的重新整理 token](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)。
  
    
    

### 應內容權杖字串會儲存在 cookie 中，以便用於其他頁面要求，直接從提供者主控應用程式嗎？

此內容已移至 [快取的內容權杖或它的組件](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken)。
  
    
    

### 什麼是快取機碼值由組成？如何為其唯一？

此內容已移至 [了解快取機碼](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey)。
  
    
    

### 如何擷取內容權杖呢？

此內容已移至 [了解結構及內容權杖的處理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens)。
  
    
    

### 內容權杖包含哪些資訊？

此內容已移至 [查看內容權杖的範例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken)。
  
    
    

### 存取權杖包含哪些資訊？

此內容已移至：
  
    
    

-  [請參閱 ACS 驗證系統的存取權杖的範例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)
    
  
-  [請參閱高信任授權系統中的存取權杖的範例](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)
    
  

### 如何計算實際的時間和日期值的 nbf 和 exp？

此內容已移至 [使用 JWT 時間值](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes)。
  
    
    

### 我要防止使用者不是從 SharePoint 「 我的應用程式服務。我檢查使用者的有效性來在應用程式的進入點 (由建立 ClientContext)，但由任何人都可以呼叫我 WCF 服務。應建立 ClientContext 上每個服務方法呼叫內容權杖從嗎？

此內容已移至 [使用內容權杖來限制只有 SharePoint 使用者存取](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess)。
  
    
    

### 是否有需要，可以保留 AppContext (取自 SharePoint POST 要求) 為輸入的隱藏欄位，在頁面吗？

此內容已移至 [快取的內容權杖或它的組件](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken)。
  
    
    

### 多久是有效的重新整理 token？

此內容已移至 [了解處理與快取的重新整理 token](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)。
  
    
    

### 我儲存存取權杖，並在 cookie 主機 URL，讓他們可以用對其他頁面要求。但使用者所需的符號及存取權杖到期。我該怎麼辦？

此內容已移至：
  
    
    

-  [了解存取權杖的處理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)
    
  
-  [了解處理與快取的重新整理 token](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)
    
  

### 哪些案例中應我捨棄是否仍然有效舊會過期的重新整理權杖，並使用一份新？

此內容已移至 [了解處理與快取的重新整理 token](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)。
  
    
    

## 權限與權限要求範圍
<a name="Perm"> </a>

This content is in  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### 權限要求範圍與可用的權限的清單、 文件庫的內容，以及其他功能有哪些？

This content is now in  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## OAuth 驗證原則類型
<a name="Policy"> </a>

This content is in  [SharePoint 2013 中的增益集授權原則類型](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### 僅限應用程式的原則及使用者 + 應用程式原則之間的差異為何？

This content is in  [SharePoint 2013 中的增益集授權原則類型](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### 是否有授與或拒絕啟動應用程式的權限的方式吗？

This content is in  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## 偵錯
<a name="Debugging"> </a>

此內容已移至：
  
    
    

-  [疑難排解高信任 SharePoint 增益集](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [疑難排解低信任層級增益集的 SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### 使用 Fiddler

此內容已移至：
  
    
    

-  [疑難排解高信任 SharePoint 增益集](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [疑難排解低信任層級增益集的 SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

## 高信任度應用程式 (伺服器對伺服器應用程式) 內部部署
<a name="S2S"> </a>

This content has been moved to  [疑難排解高信任 SharePoint 增益集](troubleshooting-high-trust-sharepoint-add-ins.md)
  
    
    

### 執行高信任應用程式時收到 401 未經授權的錯誤。我該怎麼辦？

This content has been moved to  [疑難排解高信任 SharePoint 增益集](troubleshooting-high-trust-sharepoint-add-ins.md).
  
    
    

### 如何取得內容權杖高信任應用程式？

This content has been moved to  [建立及使用存取權杖中提供者主控高信任 SharePoint 增益集](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md).
  
    
    

## OAuth 相關的其他問題
<a name="Misc"> </a>

此內容已移至：
  
    
    

-  [疑難排解高信任 SharePoint 增益集](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [疑難排解低信任層級增益集的 SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### 當嘗試使用 HTTP DAV 方法將檔案讀取，我收到錯誤。我該怎麼辦？

This content has moved to  [疑難排解低信任層級增益集的 SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md).
  
    
    

### 是否有一種方式轉寄到不同網域中的其他元件的 OAuth 驗證，或設定多個 Uri 的 OAuth 吗？

[是]。請參閱 ＜ [轉寄至後端系統的存取權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend)。
  
    
    

### 是SharePoint 2013主體值常數吗？

[是]。請參閱 ＜ [請參閱範例 ACS 授權系統中的存取權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)(特別是資料表 1)。
  
    
    

### 是應用程式識別碼和應用程式秘密常數跨所有承租人特定應用程式吗？

Yes, if the app is registered in the 賣方儀表板; but if it is separately registered with each SharePoint tenancy or farm, it can have distinct IDs and secrets for each. In that case, it appears to be multiple apps to Microsoft Azure Access Control Service (ACS). See  [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md).
  
    
    

### 領域是唯一的嗎？

[是]。請參閱 ＜ [請參閱範例 ACS 授權系統中的存取權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)(特別是資料表 1) ＞ 與 ＜  [若要取得存取權杖使用內容權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken)。
  
    
    

### 如何關閉期間開發 OAuth HTTPS 需求？

本內容已經移動， [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement)。
  
    
    

## 社交功能
<a name="Social"> </a>

本內容已經移動至：
  
    
    

1.  [SharePoint 2013 的社交和共同作業功能](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)
    
  
2.  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  

### 如何擷取使用者的身分識別與屬性呢？

See  [取得在 SharePoint 2013 中的使用者身分識別與屬性](get-user-identity-and-properties-in-sharepoint-2013.md).
  
    
    

### 什麼是不同的社交功能和權限要求範圍的使用狀況？

此內容已在 [SharePoint 2013 開始使用社交功能開發](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms)。
  
    
    

### 如何取得使用者的我的人員設定檔屬性？

本內容已經移動至 [操作方法： 在 SharePoint 2013 中使用.NET 用戶端物件模型來擷取使用者設定檔屬性](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp)。
  
    
    