---
title: 取得在 SharePoint 2013 中的使用者身分識別與屬性
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# 取得在 SharePoint 2013 中的使用者身分識別與屬性
擷取使用者身分識別與SharePoint 2013中的使用者資訊。
有不同的方式來擷取使用者身分識別和資訊，請根據您想要擷取何種資訊。本文說明一些您可以完成之工作的方式。
  
    
    


## 擷取使用者身分識別與內容的必要條件
<a name="Prereq"> </a>


- 準備您的增益集開發環境 [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)中所述。
    
  
- 安裝 Visual Studio
    
  
- 安裝最新版本的 [Office 開發人員工具的 Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013)或 [Office 開發人員工具的 Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015)。
    
  

> [!注意事項]
> 如需如何以符合您需求的開發環境的指引，請參閱 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)。
  
    
    


### 了解使用者身分識別與內容擷取的核心概念

下表列出一些實用的文章可協助您了解建立SharePoint Add-ins所需的概念。
  
    
    


|**文章**|**描述**|
|:-----|:-----|
| [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md) <br/> |了解 SharePoint 增益集的權限使用SharePoint 2013、 包括類型的增益集的權限、 權限要求範圍，以及管理的權限。本文也將告訴您在增益集權限和使用者權限的差異。 <br/> |
| [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |了解雲端託管增益集的 OAuth 驗證和授權流程。 <br/> |
| [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |了解如何使用Visual Studio 2012 Office 開發人員工具建立基本提供者主控SharePoint Add-in 、 如何使用 SharePoint CSOM、 Microsoft SharePoint 2013網站與互動以及如何在SharePoint Add-in中實作 OAuth。 <br/> |
   

## 擷取目前的網站使用者身分識別
<a name="WebsiteUserID"> </a>

若要擷取之網站的目前使用者的身分識別最簡單的方法是透過 **Web**物件。使用中專案的 TokenHelper.cs 檔案，您可以取得目前使用下列程式碼片段的網站使用者身分識別。
  
    
    

```cs

ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);
 
 
            //Load the properties for the Web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();
 
            //Get the site name.
            siteName = web.Title;
 
            //Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

```


- 如果您使用Office 365，您收到登入名稱為類似 `i:0#.f|membership|adam@contoso.com`。
    
  
- 如果您使用Microsoft SharePoint 2013內部使用者使用 NTLM 的一般使用者身分登入，您收到登入名稱是類似 `i:0#.w|contoso\\adam`。
    
  
- 如果您使用SharePoint 2013內部及使用者登入使用伺服器陣列帳戶，您收到登入名稱為 `SHAREPOINT\\System`。
    
  

## 擷取使用者身分識別使用的 ResolvePrincipal 方法
<a name="ResolvePrincipal"> </a>

以下是另一種方式來擷取使用者的登入名稱。如果您有使用者的電子郵件地址或顯示名稱時，您可以使用 [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) 方法來取得使用者的登入名稱。
  
    
    

> [!注意事項]
> Api 處於 [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx)組件中的 Microsoft.SharePoint.Client.Utilities 命名空間。
  
    
    

以下是範例程式碼示範如何取得使用者登入資訊。
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

 **Person.LoginName**值授與登入資訊。
  
    
    

## 擷取使用者身分識別與設定檔屬性
<a name="Profile"> </a>

如果您想要擷取使用者的身分識別及屬性，您可以使用 OAuth 權杖及社交功能的 Api。先取得 OAuth 權杖並再將它設為用戶端內容。下列範例程式碼示範如何取得使用者設定檔屬性。
  
    
    

```cs

ClientContext clientContext = new ClientContext(<sharepointurl>);
clientContext.AuthenticationMode = ClientAuthenticationMode.Anonymous;
clientContext.FormDigestHandlingEnabled = false;
clientContext.ExecutingWebRequest +=
delegate(object oSender, WebRequestEventArgs webRequestEventArgs)
{                      
    webRequestEventArgs.WebRequestExecutor.RequestHeaders["Authorization"] =
        "Bearer " + accessToken;
};

```

然後使用 [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) API 來取得使用中之增益集之使用者的屬性。
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

如要搭配使用的程式碼：
  
    
    

- 應設定且上SharePoint 2013使用者同步處理使用者設定檔共用服務。
    
  
- 您必須新增下列權限範圍社交功能的增益集資訊清單：
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

Api 處於 Microsoft.SharePoint.Client.UserProfiles.dll。
  
    
    
以下是另一個程式碼片段範例示範如何存取使用者設定檔儲存。
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## 其他資源
<a name="AdditionalResources"> </a>


-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  
-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

