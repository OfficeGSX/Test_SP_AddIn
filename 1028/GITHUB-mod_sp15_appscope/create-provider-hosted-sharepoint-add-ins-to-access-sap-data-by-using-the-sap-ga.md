---
title: 使用 SAP 閘道的 Microsoft 建立提供者主控 SharePoint 增益集來存取 SAP 資料
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# 使用 SAP 閘道的 Microsoft 建立提供者主控 SharePoint 增益集來存取 SAP 資料
了解如何建立可以存取 SAP 資料SharePoint Add-in 。
您可以建立SharePoint Add-in的讀取及寫入 SAP 資料以及 (選擇性) 讀取與寫入SharePoint資料使用SAP Gateway for Microsoft與 Azure AD 驗證文件庫 for.NET。本文提供如何設計SharePoint Add-in取得授權的存取 SAP 的程序。
  
    
    


## 開始之前

以下是本文中的程序的必要條件：
  
    
    

- **Office 365 開發人員網站**關聯Microsoft Azure Active Directory訂閱Office 365網域中。請參閱 [在 Office 365 上設定 SharePoint 增益集的開發環境](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md)或 [建立現有的 Office 365 訂閱開發人員網站](create-a-developer-site-on-an-existing-office-365-subscription.md)。
    
  
- **Visual Studio 2013年更新 2**或更新版本，其中您可以從 [ [歡迎使用 Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx)取得。
    
  
- **Microsoft Office Developer Tools for Visual Studio**。包含在更新 2 Visual Studio 2013年或更新版本的版本。
    
  
- **SAP Gateway for Microsoft**已部署及設定Microsoft Azure中。請參閱 [Microsoft SAP 閘道](http://go.microsoft.com/fwlink/?LinkId=507635)文件。
    
  
- **在Microsoft Azure組織帳戶**。請參閱 [手動新增至您的 Office 365 Api 預覽應用程式的 [一般同意](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount)。
    
    > **注意事項**
      > Office 365 帳戶 (login.microsoftonline.com) 來建立帳戶之後變更暫時密碼登入。
- 將含有範例資料中的 **SAP OData 端點**。請參閱 [Microsoft SAP 閘道](http://go.microsoft.com/fwlink/?LinkId=507635)文件。
    
  
- **基本非常熟悉Azure AD。**請參閱 ＜  [Getting started with Azure AD](http://msdn.microsoft.com/library/azure/dn655157.aspx)。
    
  
- **建立SharePoint Add-ins基本熟悉。**請參閱 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)。
    
  
- **基本熟悉 OAuth 2.0 的 Azure AD**。請參閱 [在 Azure AD 的 OAuth 2.0](http://msdn.microsoft.com/library/azure/dn645545.aspx)和其子主題。
    
  
 **程式碼範例：** [SharePoint 2013： 在 SharePoint 增益集使用 Microsoft SAP 閘道](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)
  
    
    

## 了解驗證和授權SAP Gateway for Microsoft及SharePoint
<a name="AuthOverview"> </a>

在 Azure AD 的 OAuth 2.0 可讓應用程式以存取Microsoft Azure所主控的多個資源和SAP Gateway for Microsoft是其中一個它們。使用 OAuth 2.0 應用程式，以及使用者會有安全性主體。應用程式主體需要驗證和授權受保護的資源得以至 (及有時而不是) 的使用者。程序需要 OAuth"流程"的應用程式可以SharePoint Add-in，會取得存取 token (及重新整理權杖) 的會接受所有Microsoft Azure-託管服務及設定成使用Azure AD為 OAuth 2.0 授權伺服器的應用程式。程序是非常類似的提供者主控SharePoint Add-in遠端元件取得授權SharePoint [建立 SharePoint 增益集使用低信任層級授權](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)和其子文章中所述的方式。不過，ACS 授權系統會使用Microsoft Azure Access Control Service (ACS)做為受信任權杖發行者而不是Azure AD。
  
    
    

> **秘訣**
> 如果您SharePoint Add-in存取SharePoint除了存取SAP Gateway for Microsoft，則它將需要使用 *兩個*  系統： Azure AD取得存取 token SAP Gateway for Microsoft及取得存取 token 至SharePointACS 授權系統。從兩個來源的 token 不可以互換。如需詳細資訊，請參閱 [選擇性地新增 SharePoint 存取權的 ASP.NET 應用程式](#SharePoint)。
  
    
    

詳細的說明與使用 OAuth 2.0 的Azure ADOAuth 流程圖表中，請參閱 [授權碼授與流程](http://msdn.microsoft.com/library/azure/dn645542.aspx)。(類似的描述，和存取SharePoint、 流程圖表，請參閱 [請參閱內容權杖流程中的步驟](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps))。
  
    
    

## 建立SharePoint Add-in
<a name="AuthOverview"> </a>


### 建立Visual Studio方案


1. 在Visual Studio使用下列步驟建立 **SharePoint Add-in**專案。(在本文中的繼續範例假設您使用 C# 中 ； 但您可以啟動SharePoint Add-in專案的 **Visual Basic** ] 區段中的新專案範本)。
    
1. 在 [ **新 SharePoint 增益集**精靈] 為專案的名稱並按一下 [ **確定]**。持續的範例中，使用SAP2SharePoint。
    
  
2. 指定的網域 URL (包括最後的正斜線) 您Office 365 開發人員網站的偵錯網站;例如， https://<O365_domain>.sharepoint.com/。指定 **提供者主控**做為增益集類型。按一下 [ **下一步**]。
    
  
3. 選擇 [專案類型]。針對持續的範例中，選擇 [ **ASP.NET Web 表單應用程式**]。(您也可以進行 ASP.NET MVC 存取SAP Gateway for Microsoft應用程式。)按一下 [ **下一步**]。
    
  
4. 選擇Azure ACS做為驗證系統。(如果它存取SharePointSharePoint Add-in您將會使用這個系統。它不使用這個系統時加以存取SAP Gateway for Microsoft。)按一下 [ **完成**]。
    
  
2. 建立專案之後，系統會提示您Office 365帳戶登入。使用系統帳戶 ； 的認證例如Bob @< O365_domain >。 onmicrosoft.com。
    
  
3. Visual Studio解決方案; 中有兩個專案SharePoint Add-in正確的專案和 ASP.NET web 表單的專案。將 **Active Directory 驗證文件庫** (ADAL) 套件新增至 ASP.NET 專案進行這些步驟：
    
1. (延續範例中一個名為 **SAP2SharePointWeb** ) ASP.NET 專案中的 [ **參考**] 資料夾上按一下滑鼠右鍵並選取 [ **管理 NuGet 套件**。
    
  
2. 在 [開啟] 對話方塊中，選取 [ **線上**左方。在 [搜尋] 方塊中輸入Microsoft.IdentityModel.Clients.ActiveDirectory 。
    
  
3. 當 ADAL 文件庫會出現在搜尋結果中時，按一下 [ **安裝**] 按鈕旁，並接受授權出現提示時。
    
  
4. 將 Json.net 套件新增至 ASP.NET 專案進行這些步驟：
    
1. 在 [搜尋] 方塊中輸入Json.net 。如果這會產生太多是落在圖形，請嘗試在Newtonsoft.json搜尋。
    
  
2. 當 Json.net 出現在搜尋結果中時，按一下 [它旁邊的 [ **安裝**] 按鈕。
    
  
5. 按一下 **[關閉]**。
    
  

### Azure AD中註冊您的 web 應用程式


1. 登入與 Azure 系統管理員帳戶 [Azure 管理入口網站](https://manage.windowsazure.com) 。
    
    > **注意事項**
      > 基於安全性考量，建議針對開發增益集時使用系統管理員帳戶。
2. 選擇 [ **Active Directory** ] 左側。
    
  
3. 按一下 [在您的目錄。
    
  
4. 選擇 [ **應用程式**(上方導覽列上)。
    
  
5. 在螢幕底部工具列上選擇 [ **新增**]。
    
  
6. 在 [開啟] 對話方塊中，選擇 [ **新增應用程式開發我的組織**。
    
  
7. 在 [ **新增應用程式**] 對話方塊提供應用程式的名稱。持續的範例中，使用ContosoAutomobileCollection。
    
  
8. 選擇應用程式類型] 中，為 **Web 應用程式和/或網頁 API** ，然後按一下右邊的箭號] 按鈕。
    
  
9. 在 [] 對話方塊中的第二頁，使用來自Visual Studio解決方案為 **登 ON URL**中的 ASP.NET 專案偵錯 URL 的 SSL。您可以找到依照下列步驟的 URL。 ** *(您需要登錄的增益集最初與偵錯的 URL，讓您可以執行Visual Studio偵錯工具 (F5)。當增益集已準備就緒臨時，您將會重新將其登錄及其臨時Azure 網站 URL。 [修改的增益集和階段 Azure 及 Office 365](#Stage)。)* **
    
1. 反白顯示 **方案總管**中的 ASP.NET 專案。
    
  
2. 在 [ **屬性**] 視窗中，將複製 **SSL URL**屬性的值。例如， https://localhost:44300 /。
    
  
3. 將它貼到 **登 ON URL**在 [ **新增應用程式**] 對話方塊。
    
  
10. 針對 **應用程式識別碼 URI**給應用程式的唯一 URI，例如附加至 SSL URL; 結尾的應用程式名稱例如https://localhost:44300/ContosoAutomobileCollection。
    
  
11. 按一下 [核取記號表示] 按鈕。應用程式的 Azure 儀表板會開啟內含的成功訊息。
    
  
12. 選擇 [請在頁面上方的 [ **設定**]。
    
  
13. 捲動至 **用戶端識別碼**並使其複本。您將需要它的後續的程序。
    
  
14. 在 [ **機碼**] 區段中建立機碼。它將不會顯示上一開始。按一下頁面底部的 [ **儲存**及索引鍵會是可見。使其複本。您將需要它的後續的程序。
    
  
15. 捲動至 **其他應用程式的權限**和選取SAP Gateway for Microsoft服務應用程式。
    
  
16. 開啟 **委派權限**] 下拉式清單，並讓您SharePoint Add-in所需要的SAP Gateway for Microsoft服務的權限的方塊。
    
  
17. 按一下 [在螢幕底部的 [ **儲存**]。
    
  

### 設定要與Azure AD通訊的應用程式


1. 在Visual Studio，開啟 ASP.NET 專案中的 web.config 檔案。
    
  
2. [  `<appSettings>` ] 區段中Office Developer Tools for Visual Studio已新增 **ClientID**和 **ClientSecret**SharePoint Add-in的元素。(這些將用以Azure ACS授權系統中的 ASP.NET 應用程式存取SharePoint如果。您可以加以略過的持續的範例中，但不要刪除。他們所需提供者主控SharePoint Add-ins即使增益集無法存取SharePoint資料。其值會變更每次您在Visual Studio按 F5)。將下列兩種元素新增至 [] 區段中。這些可用來驗證Azure AD應用程式。(請記住應用程式，當使用者處於安全性主體 OAuth 型驗證與授權系統)。
    
  ```
  
<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
  ```

3. 插入 **ida:ClientID**索引鍵的值儲存從Azure AD directory 在先前程序中的用戶端識別碼。保留的大小寫和標點符號您複製如同一樣，然後請小心不要將包含空格字元開頭或結尾的值。用於 **ida:ClientKey**主要您儲存目錄的 *索引鍵*  。同樣地，請小心不要將引進任何空白字元或變更任何方法中的值。 `<appSettings>`區段現在看起來應類似如下的某個項目。( **ClientId**值可能會有的 GUID 或空字串)。
    
  ```
  
<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
  ```


    > **注意事項**
      > 您的應用程式是由您用來將其登錄的"localhost"URL 已知Azure AD 。用戶端識別碼和用戶端金鑰相關聯的身分識別。當您準備好階段Azure 網站繼續應用程式時，將會重新將其註冊新的 url。
4. 仍處於 **appSettings** ] 區段中，新增 **Authority**機碼並設定其值為Office 365網域 ( *some_domain*  。 onmicrosoft.com) 您的組織帳戶。在繼續範例中，組織的帳戶是 Bob @< O365_domain >。 onmicrosoft.com，所以授權單位是 `<O365_domain>.onmicrosoft.com`。
    
  ```
  
<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  ```

5. 仍處於 **appSettings** ] 區段中，新增 **AppRedirectUrl**金鑰並將其值設為使用者的瀏覽器在 ASP.NET 增益集已從Azure AD取得授權的程式碼後重新導向至頁面。通常，這是第時進行的呼叫Azure AD使用者已在同一頁。在繼續範例中，使用 SSL URL 值搭配"/ /pages/default.aspx"附加至其如下所示。(這是您要變更的臨時的另一個值)。
    
  ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  ```

6. 仍 **appSettings** ] 區段中新增 **ResourceUrl**金鑰並將其值設為應用程式識別碼的 URI SAP Gateway for Microsoft (在 ASP.NET 應用程式的應用程式識別碼 URI)。從SAP Gateway for Microsoft系統管理員取得這個值。以下是範例。
    
  ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
  ```


     `<appSettings>`區段現在看起來應類似如下：
    


  ```
  <appSettings>
  <add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
  <add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
</appSettings>
  ```

7. 儲存並關閉 Web.config 檔案。
    
    > **秘訣**
      > 請勿將 web.config 檔案開啟時執行Visual Studio偵錯工具 (F5)。Office Developer Tools for Visual Studio值變更為 **ClientId** (不 **ida:ClientID**) 每次按 F5。這需要您回應提示重新載入 web.config 檔案中，如果它開啟，才可執行偵錯。

### 新增至 Azure AD 驗證輔助類別


1. 以滑鼠右鍵按一下 [ASP.NET 專案並使用Visual Studio項目新增程序將新的類別檔案新增至名為 AADAuthHelper.cs 專案。
    
  
2. 檔案中新增下列 **using**陳述式。
    
  ```
  
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

  ```

3. 變更存取關鍵字 **public** **internal**並將 **static**關鍵字新增至類別宣告。
    
  ```
  
internal static class AADAuthHelper
  ```

4. 將下列欄位新增至類別。這些欄位會儲存在 ASP.NET 應用程式用來從 AAD 取得存取 token 的資訊。
    
  ```
  private static readonly string _authority = ConfigurationManager.AppSettings["Authority"];
private static readonly string _appRedirectUrl = ConfigurationManager.AppSettings["AppRedirectUrl"];
private static readonly string _resourceUrl = ConfigurationManager.AppSettings["ResourceUrl"];     
private static readonly string _clientId = ConfigurationManager.AppSettings["ida:ClientID"];        
private static readonly ClientCredential _clientCredential = new ClientCredential(
                           ConfigurationManager.AppSettings["ida:ClientID"],
                           ConfigurationManager.AppSettings["ida:ClientKey"]);

private static readonly AuthenticationContext _authenticationContext = 
            new AuthenticationContext("https://login.windows.net/common/" + 
                                      ConfigurationManager.AppSettings["Authority"]);
  ```

5. 將下列屬性新增至類別。此屬性包含Azure AD登入畫面上的 URL。
    
  ```
  
private static string AuthorizeUrl
{
    get
    {
        return string.Format("https://login.windows.net/{0}/oauth2/authorize?response_type=code&amp;redirect_uri={1}&amp;client_id={2}&amp;state={3}",
            _authority,
            _appRedirectUrl,
            _clientId,
            Guid.NewGuid().ToString());
    }
}

  ```

6. 將下列屬性新增至類別。這些快取的存取並重新整理 token，並檢查其有效性。
    
  ```
  
public static Tuple<string, DateTimeOffset> AccessToken
{
    get {
return HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] 
       as Tuple<string, DateTimeOffset>;
    }

    set { HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] = value; }
}

private static bool IsAccessTokenValid
{
   get 
   { 
       return AccessToken != null &amp;&amp;
       !string.IsNullOrEmpty(AccessToken.Item1) &amp;&amp;
       AccessToken.Item2 > DateTimeOffset.UtcNow;
   }
}

private static string RefreshToken
{
    get { return HttpContext.Current.Session["RefreshToken" + _resourceUrl] as string; }
    set { HttpContext.Current.Session["RefreshToken-" + _resourceUrl] = value; }
}

private static bool IsRefreshTokenValid
{
    get { return !string.IsNullOrEmpty(RefreshToken); }
}

  ```

7. 類別新增下列方法。這些將用以以檢查授權程式碼的有效性和使用驗證程式碼或重新整理權杖從Azure AD取得存取 token。
    
  ```
  
private static bool IsAuthorizationCodeNotNull(string authCode)
{
    return !string.IsNullOrEmpty(authCode);
}

private static Tuple<Tuple<string,DateTimeOffset>,string> AcquireTokensUsingAuthCode(string authCode)
{
    var authResult = _authenticationContext.AcquireTokenByAuthorizationCode(
                authCode,
                new Uri(_appRedirectUrl),
                _clientCredential,
                _resourceUrl);

    return new Tuple<Tuple<string, DateTimeOffset>, string>(
                new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn), 
                authResult.RefreshToken);
}

private static Tuple<string, DateTimeOffset> RenewAccessTokenUsingRefreshToken()
{
    var authResult = _authenticationContext.AcquireTokenByRefreshToken(
                         RefreshToken,
                         _clientCredential.OwnerId,
                         _clientCredential,
                         _resourceUrl);

    return new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);
}

  ```

8. 類別新增下列方法。它會呼叫從 ASP.NET 程式碼後置撥打的電話取得透過SAP Gateway for Microsoft的 SAP 資料之前取得有效存取權杖。
    
  ```
  
internal static void EnsureValidAccessToken(Page page)
{
    if (IsAccessTokenValid) 
    {
        return;
    }
    else if (IsRefreshTokenValid) 
    {
        AccessToken = RenewAccessTokenUsingRefreshToken();
        return;
    }
    else if (IsAuthorizationCodeNotNull(page.Request.QueryString["code"]))
    {
        Tuple<Tuple<string, DateTimeOffset>, string> tokens = null;
        try
        {
            tokens = AcquireTokensUsingAuthCode(page.Request.QueryString["code"]);
        }
        catch 
        {
            page.Response.Redirect(AuthorizeUrl);
        }
        AccessToken = tokens.Item1;
        RefreshToken = tokens.Item2;
        return;
    }
    else
    {
        page.Response.Redirect(AuthorizeUrl);
    }
}
  ```


> **秘訣**
> AADAuthHelper 類別具有僅限最少的錯誤處理。穩固、 實際執行品質SharePoint Add-in，如新增更多的錯誤處理此 MSDN 節點中所述：  [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx)。
  
    
    


### 建立資料模型的類別


1. 建立一或多個類別來建立增益集取得來自 SAP 資料的模型。在繼續範例中，有只是一個資料模型的類別。以滑鼠右鍵按一下 [ASP.NET 專案並使用Visual Studio項目新增程序將新的類別檔案新增至名為 Automobile.cs 專案。
    
  
2. 將下列程式碼新增至類別的本文：
    
  ```
  
public string Price;
public string Brand;
public string Model;
public int Year;
public string Engine;
public int MaxPower;
public string BodyStyle;
public string Transmission;
  ```


### 若要從 SAP 取得資料透過SAP Gateway for Microsoft新增程式碼後置


1. 開啟 Default.aspx.cs 檔案並新增下列 **using**陳述式。
    
  ```
  
using System.Net;
using Newtonsoft.Json.Linq;
  ```

2. 將 **const**宣告新增至其值為基底 URL 將會存取增益集的 SAP OData 端點的預設類別。以下為範例：
    
  ```
  
private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
  ```

3. Office Developer Tools for Visual Studio新增 **Page_PreInit**方法 」 和 「 **Page_Load**方法。註解的程式碼內的 **Page_Load**方法和註解的整個 **Page_Init**方法。這段程式碼不在此範例會使用。(如果您SharePoint Add-in存取SharePoint，然後還原這段程式碼。請參閱 [選擇性地新增 SharePoint 存取權的 ASP.NET 應用程式](#SharePoint))。
    
  
4. 將下行新增至 **Page_Load**方法的頂端。這會簡化偵錯 ASP.NET 應用程式通訊的SAP Gateway for Microsoft使用 SSL (HTTPS) ； 因為程的序但是"localhost:port"伺服器未設定為信任之憑證的SAP Gateway for Microsoft。這一行程式碼中，而您將取得無效的憑證警告前 Default.aspx 會開啟。某些瀏覽器可讓您按一下過去的這項錯誤，但部分將不會讓您完全開啟 Default.aspx。
    
  ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
  ```


    > **重要**
      > 當您準備好要部署至臨時 ASP.NET 應用程式刪除此列。請參閱 [修改的增益集和階段 Azure 及 Office 365](#Stage)。
5. 將下列程式碼新增至 **Page_Load**方法。傳遞至 `GetSAPData`方法的字串是 OData 查詢。
    
  ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

  ```

6. 將下列方法新增至預設類別。此方法先確保存取權杖快取中會有已從Azure AD取得有有效的存取權杖。接著會建立 HTTP **GET**包含存取權杖，並傳送給 SAP OData 端點的要求。結果會以 JSON 物件轉換成.NET **List**物件傳回。三個項目屬性用於繫結至 **DataListView**陣列中。
    
  ```
  
private void GetSAPData(string oDataQuery)
{
    AADAuthHelper.EnsureValidAccessToken(this);

    using (WebClient client = new WebClient())
    {
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.Authorization] = "Bearer " + AADAuthHelper.AccessToken.Item1;
        var jsonString = client.DownloadString(SAP_ODATA_URL + oDataQuery);
        var jsonValue = JObject.Parse(jsonString)["d"]["results"];
        var dataCol = jsonValue.ToObject<List<Automobile>>();

        var dataList = dataCol.Select((item) => {
            return item.Brand + " " + item.Model + " " + item.Price;
            }).ToArray();

        DataListView.DataSource = dataList;
        DataListView.DataBind();
    }
}

  ```


### 建立使用者介面


1. 開啟 Default.aspx 檔案並將下列標記新增至頁面 **form** ：
    
  ```
  
<div>
  <h3>Data from SAP via SAP Gateway for Microsoft</h3>

  <asp:ListView runat="server" ID="DataListView">
    <ItemTemplate>
      <tr runat="server">
        <td runat="server">
          <asp:Label ID="DataLabel" runat="server"
            Text="<%# Container.DataItem.ToString()%>" /><br />
        </td>
      </tr>
    </ItemTemplate>
  </asp:ListView>
</div>
  ```

2. (選用) 授與網頁"外觀 'n' 覺得" SharePoint ] 頁面中填入SharePoint  [的 Chrome 控制項](use-the-client-chrome-control-in-sharepoint-add-ins.md)和 [主機 SharePoint 網站的樣式表](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)。
    
  

### 測試增益集與 F5 的Visual Studio


1. 在Visual Studio按 F5。
    
  
2. 第一次使用 F5，可能會提示您登入您使用開發人員網站 。使用網站管理員認證。在繼續範例中，會比較快Bob @< O365_domain >。 onmicrosoft.com。
    
  
3. 您使用 F5、 第一次系統提示您按一下增益集來授與權限 **信任它**。
    
  
4. 簡短的延遲時間之後時要取得存取 token，Default.aspx 頁面隨即開啟。確認 SAP 資料隨即顯示。
    
  

## 選擇性地新增 SharePoint 存取權的 ASP.NET 應用程式
<a name="SharePoint"> </a>

當然，您SharePoint Add-in沒有公開只從SharePoint啟動網頁中的 SAP 資料。它也可以建立、 讀取、 更新和刪除 (CRUD) SharePoint資料。您的程式碼後置可以執行這項作業使用SharePoint用戶端物件模型 (CSOM) 或SharePointREST Api。CSOM 部署為兩個Office Developer Tools for Visual Studio自動包含在 ASP.NET 專案並使其包含在 ASP.NET 應用程式套件中Visual Studio設為 **複製到本機**的組件。如需使用 CSOM，開始使用 [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md)。如需使用 REST Api，開始使用 [了解及使用 SharePoint 2013 REST 介面](http://msdn.microsoft.com/en-us/magazine/dn198245.aspx)。
  
    
    
無論如何，您是否使用 CSOM 或 REST Api 來存取SharePoint、 ASP.NET 應用程式必須取得SharePoint，來存取權杖以SAP Gateway for Microsoft一樣。請參閱 [了解驗證和授權SAP Gateway for Microsoft及SharePoint](#AuthOverview)以上。下面的程序提供有關如何達成此目的，某些基本指引，但建議您先閱讀下列文章：
  
    
    

-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [三個授權系統的 SharePoint 增益集](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [建立 SharePoint 增益集使用低信任層級授權](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
-  [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  

1. 開啟 Default.aspx.cs 檔案並取消註解 **Page_PreInit**方法。也取消註解Office Developer Tools for Visual Studio新增至 **Page_Load**方法的程式碼。
    
  
2. 如果您SharePoint Add-in將要存取SharePoint資料，您就必須快取時的增益集可啟動 SharePoint 中會張貼至 Default.aspx 頁面上的 SharePoint 內容權杖。這是確定下列Azure AD驗證重新導向瀏覽器時不遺失 SharePoint 內容權杖。(您有數種選項如何快取此內容)。Office Developer Tools for Visual Studio SharePointContext.cs 將檔案新增至 ASP.NET 專案可執行大部分的工作。若要使用的工作階段快取，您只是新增下列程式碼內的" `if (!IsPostBack)`"block *之前*  呼叫的程式碼至SAP Gateway for Microsoft：
    
  ```
  
if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
  ```

3. SharePointContext.cs 檔案進行Office Developer Tools for Visual Studio新增至專案的另一個檔案的通話： TokenHelper.cs。此檔案提供充分運用取得並使用SharePoint存取 token 所需的程式碼。不過，它不提供任何程式碼的更新過期的存取權杖或過期的重新整理 token。也不會包含任何權杖快取的程式碼。實際執行品質SharePoint Add-in，您需要新增這類程式碼。在上述步驟中的快取邏輯是範例。您的程式碼也應快取存取 token，直到過期重複使用。當存取權杖到期後時，您的程式碼應使用的重新整理權杖以取得新的存取 token。
    
  
4. 新增SharePoint使用 CSOM 或 REST 資料來電。下列範例會修改該Office Developer Tools for Visual Studio新增至 **Page_Load**方法 CSOM 程式碼。在這個範例中，將程式碼已移至不同的方法和其一開始會擷取快取的內容權杖。
    
  ```
  
private void GetSharePointTitle()
{
    var spContext = HttpContext.Current.Session["SharePointContext"] as SharePointContext;
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        clientContext.Load(clientContext.Web, web => web.Title);
        clientContext.ExecuteQuery();
        SharePointTitle.Text = "SharePoint web site title is: " + clientContext.Web.Title;
    }
}
  ```

5. 新增轉譯SharePoint資料的 UI 元素。下列顯示上述方法所參照的 HTML 控制項：
    
  ```
  
<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
  ```


> **注意事項**
> 當您正在偵錯SharePoint Add-in時、 Office Developer Tools for Visual Studio重新將其登錄Azure ACS按 F5 鍵Visual Studio在每次。當分段SharePoint Add-in時，必須將其命名長期註冊。請參閱] 區段中 [修改的增益集和階段 Azure 及 Office 365](#Stage)。
  
    
    


## 修改的增益集和階段 Azure 及 Office 365
<a name="Stage"> </a>

當您完成偵錯Visual Studio中使用 F5 SharePoint Add-in時，您需要部署至實際Azure 網站ASP.NET 應用程式。
  
    
    

### 建立 Azure 的網站


1. Microsoft Azure入口網站中的左的導覽列上開啟 [ **網站**]。
    
  
2. 按一下 [ **新增]**頁面底部和在 **新增**] 對話方塊選取 **網站**|**快速建立**。
    
  
3. 輸入網域名稱並按一下 [ **建立網站**。複製的新網站的 URL。它會有表單 `my_domain.azurewebsites.net`。
    
  

### 修改程式碼和應用程式中的標記


1. 在Visual Studio，移除線條 `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;` Default.aspx.cs 檔案中。
    
  
2. 開啟 ASP.NET 專案的 web.config 檔並 **AppRedirectUrl**機碼的 **appSettings** ] 區段中的值的網域部分變更為Azure 網站的網域。例如，變更 `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />` `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` 。
    
  
3. 以滑鼠右鍵按一下SharePoint Add-in專案中的 AppManifest.xml 檔案，並選取 [ **檢視程式碼**。
    
  
4. 在 [ **StartPage**值取代為字串 `~remoteAppUrl`包括通訊協定; Azure 網站的完整網域例如 `https://my_domain.azurewebsites.net`。整個 **StartPage**值現在應該:  `https://my_domain.azurewebsites.net/Pages/Default.aspx`。(通常是、 **StartPage**值為完全 **AppRedirectUrl**機碼的 web.config 檔案中的值相同)。
    
  

### 修改 AAD 註冊並向 ACS 登錄的增益集


1. 將 [Azure 管理入口網站](https://manage.windowsazure.com)登入與 Azure 系統管理員帳戶。
    
  
2. 選擇 [ **Active Directory** ] 左側。
    
  
3. 按一下 [在您的目錄。
    
  
4. 選擇 [ **應用程式**(上方導覽列上)。
    
  
5. 開啟您建立的應用程式。在繼續範例中，它是 **ContosoAutomobileCollection**。
    
  
6. 下列值的每個變更"localhost: *連接埠*  " Azure 網站您新網域值的一部分：
    
  - **登 ON URL**
    
  
  - **應用程式識別碼 URI**
    
  
  - **回覆 URL**
    
  

    例如，如果 **https://localhost:44304/ContosoAutomobileCollection** **應用程式識別碼 URI** ，將其變更為 **https://<my_domain>.azurewebsites.net/ContosoAutomobileCollection**。
    
  
7. 按一下 [在螢幕底部的 [ **儲存**]。
    
  
8. 向Azure ACS登錄的增益集。這必須是完成即使增益集不會存取SharePoint並不會使用權杖從 ACS，因為相同的程序也會註冊增益集與增益集管理服務的Office 365訂閱，這是需求。(它是可以呼叫 「 增益集管理服務 」 因為SharePoint Add-ins原本 」 稱為 「 SharePoint 應用程式 」)。您可以執行註冊Office 365訂閱中任何SharePoint網站 AppRegNew.aspx 頁上。如需詳細資訊，請參閱 [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md)。此程序的一部分您將取得新的用戶端識別碼和用戶端密碼。在 web.config 中 **ClientId** (不 **ida:ClientID**) 和 **ClientSecret**機碼插入這些值。
    
    > **注意**
      > 如果有任何原因您按 F5 進行這項變更之後， Office Developer Tools for Visual Studio會覆寫一或兩個值。該原因，您應該保留使用 AppRegNew.aspx 取得值的記錄，並一律確認 [web.config 中的值是正確之前發行的 ASP.NET 應用程式。

### 發佈至 Azure ASP.NET 應用程式及 sharepoint 安裝的增益集


1. 有數種方式來發佈至Azure 網站ASP.NET 應用程式。如需詳細資訊，請參閱 ＜ [如何部署 Azure 網站](http://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/)。
    
  
2. 在Visual Studio，以滑鼠右鍵按一下 [SharePoint 增益集專案並選取 **套件**。在開啟的 **增益集的發佈**頁面上，按一下 [ **增益集的套件**。檔案總管開啟包含增益集套件的資料夾。
    
  
3. 以Office 365以全域管理員身分登入及瀏覽至 [組織增益集目錄網站集合。(如果有不一，建立資料夾。請參閱 [進行自訂商務增益集可用於 SharePoint Online 環境中之增益集使用目錄](http://office.microsoft.com/en-us/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx))。
    
  
4. 將增益集套件上傳至增益集類別目錄。
    
  
5. 瀏覽至任何網站訂閱中的 [ **網站內容**] 頁面上，按一下 [ **新增增益集**。
    
  
6. 在 **您的增益集**] 頁面上，捲動至 [ **增益集可以新增**] 區段中，按一下 [增益集的圖示。
    
  
7. 已安裝的增益集之後，按一下它的圖示上啟動增益集的 [ **網站內容**] 頁面。
    
  
如需安裝SharePoint Add-ins的詳細資訊，請參閱 [部署及安裝 SharePoint 增益集： 方法與選項](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)。
## 增益集部署至實際執行環境
<a name="Stage"> </a>

當您完成所有測試您可以部署增益集在生產環境中。這可能需要一些變更。
  
    
    

1. 如果 ASP.NET 應用程式的實際執行網域是與臨時網域不同，您必須變更 web.config **AppRedirectUrl**值與 **StartPage**檔案中的值 AppManifest.xml，以及重新封裝為SharePoint Add-in。請參閱 **修改程式碼和應用程式中的標記** 以上的程序。
    
  
2. 網域中的變更還需要您編輯 AAD 與增益集登錄。請參閱 **修改 AAD 註冊及登錄的增益集與 ACS** 以上的程序。
    
  
3. 網域中的變更還需要重新登錄的增益集與 ACS (和訂閱的增益集管理服務) 相同的程序所述。(沒有方法來 *編輯*  增益集登錄與 ACS。)不過，不需要以產生新的用戶端識別碼或 AppRegNew.aspx] 頁面上的用戶端密碼。您可以從 **ClientId** (不 **ida:ClientID**) 和 **ClientSecret**機碼 web.config 的原始值複製到 AppRegNew 表單。 *如果您不要產生新的務必將新的值複製到 web.config 中的機碼。* 
    
  

