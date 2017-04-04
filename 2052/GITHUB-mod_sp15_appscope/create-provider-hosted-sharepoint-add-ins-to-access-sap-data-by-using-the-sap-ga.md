---
title: 创建提供程序托管的 SharePoint 外接程序以通过 SAP Gateway for Microsoft 访问 SAP 数据
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# 创建提供程序托管的 SharePoint 外接程序以通过 SAP Gateway for Microsoft 访问 SAP 数据
了解如何构建可以访问 SAP 数据的 SharePoint 外接程序。
您可以通过使用适用于 .NET 的 SAP Gateway for Microsoft 和 Azure AD 身份验证库，创建读取和写入 SAP 数据并选择性读取和写入 SharePoint 数据的 SharePoint 外接程序。本文提供了设计 SharePoint 外接程序以获取 SAP 授权访问权限的过程。 
  
    
    


## 开始之前

下面是执行本文中过程的先决条件：
  
    
    

- **Office 365 开发人员网站** 在与 Microsoft Azure Active Directory 订阅相关的 Office 365 域中。请参阅 [在 Office 365 上设置 SharePoint 加载项的开发环境](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md)或 [在现有 Office 365 订阅中创建开发人员网站](create-a-developer-site-on-an-existing-office-365-subscription.md)。
    
  
- **Visual Studio 2013 Update 2** 或更高版本，您可从 [欢迎使用 Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx) 中获取。
    
  
- **Visual Studio Microsoft Office 开发人员工具**。Visual Studio 2013 Update 2 或更高版本中包含的版本。
    
  
- **SAP Gateway for Microsoft** 在 Microsoft Azure 中部署和配置。请参阅 [SAP Gateway for Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635) 文档。
    
  
- **Microsoft Azure** 中的组织帐户。请参阅 [使用 Common Consent Framework 集成 Office 365 API 预览版](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount)。
    
    > [!注释]
      > 创建帐户后，登录到您的 Office 365 帐户 (login.microsoftonline.com) 以更改临时密码。 
- **SAP OData 终结点**及其中的示例数据。请参阅  [SAP Gateway for Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635) 文档。
    
  
- **基本熟悉 Azure AD。**请参阅  [Azure AD 入门](http://msdn.microsoft.com/library/azure/dn655157.aspx)。
    
  
- **基本熟悉创建 SharePoint 外接程序。**请参阅 [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)。
    
  
- **基本熟悉 Azure AD 中的 OAuth 2.0**。请参阅  [Azure AD 中的 OAuth 2.0](http://msdn.microsoft.com/library/azure/dn645545.aspx) 及其子主题。
    
  
 **代码示例：** [SharePoint 2013：在 SharePoint 外接程序中使用 SAP Gateway to Microsoft](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)
  
    
    

## 了解 SAP Gateway for Microsoft 和 SharePoint 的身份验证和授权
<a name="AuthOverview"> </a>

Azure AD 中的 OAuth 2.0 使应用程序可以访问 Microsoft Azure 托管的多个资源，SAP Gateway for Microsoft 就是其中之一。在 OAuth 2.0 中，除用户以外，应用程序也是安全主体。除了用户以外（有时是取代用户），应用程序主体需要对受保护资源进行身份验证和授权。该过程涉及 OAuth"流"，其中应用程序（可以是 SharePoint 外接程序）获取 Microsoft Azure 托管的所有服务和应用程序都接受的访问令牌（和刷新令牌），这些服务和应用程序配置为将 Azure AD 作为 OAuth 2.0 授权服务器。该过程与提供程序托管 SharePoint 外接程序的远程组件获取 SharePoint 授权的方式非常相似，如 [创建使用低信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)及其子文章中所述。但是，ACS 授权系统使用 Microsoft Azure 访问控制服务 (ACS) 作为受信任的令牌颁发者，而非 Azure AD。
  
    
    

> [!提示]
> 如果您的 SharePoint 外接程序除了访问 SAP Gateway for Microsoft 之外还访问 SharePoint，它将需要使用 *两个*  系统：使用 Azure AD 获取 SAP Gateway for Microsoft 的访问令牌，使用 ACS 授权系统获取 SharePoint 的访问令牌。这两个源的令牌不能相互交换。有关详细信息，请参阅 [可以选择将 SharePoint 访问权限添加到 ASP.NET 应用程序](#SharePoint)。 
  
    
    

有关 Azure AD 中的 OAuth 2.0 使用的 OAuth 工作流的详细说明和示意图，请参阅 [授权代码授予流](http://msdn.microsoft.com/library/azure/dn645542.aspx)。（有关访问 SharePoint 的工作流的类似说明和图示，请参阅 [参阅上下文令牌流中的步骤](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps)。）
  
    
    

## 创建 SharePoint 外接程序
<a name="AuthOverview"> </a>


### 创建 Visual Studio 解决方案


1. 执行下列步骤，在 Visual Studio 中创建 **SharePoint 外接程序** 项目。（本文下面的示例假定您使用的是 C#；但您也可以在新项目模板的"Visual Basic"部分启动 SharePoint 外接程序项目。）
    
1. 在"新建 SharePoint 外接程序"向导中，对项目命名并单击"确定"。对于下面的示例，使用 SAP2SharePoint。
    
  
2. 将您的 Office 365 开发人员网站 的域 URL（包括最后的正斜杠）指定为调试网站；例如 https://<O365_domain>.sharepoint.com/。将"提供程序托管"指定为外接程序类型。单击"下一步"。
    
  
3. 选择项目类型。对于下面的示例，选择"ASP.NET Web Forms 应用程序"。（您也可以选择为访问 SAP Gateway for Microsoft 的 ASP.NET MVC 应用程序。）单击"下一步"。
    
  
4. 选择 Azure ACS 作为身份验证系统。（如果您的 SharePoint 外接程序访问 SharePoint，它将使用此系统。当它访问 SAP Gateway for Microsoft 时，则不使用此系统。）单击"完成"。
    
  
2. 创建项目后，将提示您登录到 Office 365 帐户。使用帐户管理员的凭据，例如 Bob@<O365_domain>.onmicrosoft.com。
    
  
3. Visual Studio 解决方案中有两种项目：SharePoint 外接程序项目和 ASP.NET Web Forms 项目。执行下列步骤，将 **Active Directory 身份验证库** (ADAL) 包添加到 ASP.NET 项目：
    
1. 在下面的示例中，在名为"SAP2SharePointWeb"的 ASP.NET 项目中右键单击"References"文件夹，选择"管理 NuGet 包"。 
    
  
2. 在打开的对话框中，选择左侧的"在线"。在搜索框中输入 Microsoft.IdentityModel.Clients.ActiveDirectory。
    
  
3. 当 ADAL 库出现在搜索结果中时，单击下方的"安装"按钮，在收到提示时接受许可证。
    
  
4. 执行下列步骤，将 Json.net 包添加到 ASP.NET 项目：
    
1. 在搜索框中输入 Json.net。如果这样生成的提示太多，尝试搜索 Newtonsoft.json。
    
  
2. 当"Json.net"出现在搜索结果中时，单击下方的"安装"按钮。
    
  
5. 单击"关闭"。
    
  

### 在 Azure AD 中注册您的 Web 应用程序


1. 使用 Azure 管理员帐户登录到  [Azure 管理门户](https://manage.windowsazure.com)。
    
    > [!注释]
      > 出于安全考虑，我们建议您在开发外接程序时使用管理员帐户。 
2. 在左侧选择"Active Directory"。
    
  
3. 单击您的目录。 
    
  
4. 选择"应用程序"（位于顶部导航栏中）。
    
  
5. 在屏幕底部的工具栏上选择"添加"。
    
  
6. 在打开的对话框中，选择"添加我的组织正在开发的应用程序"。
    
  
7. 在"添加应用程序"对话框中，为应用程序指定一个名称。在下面的示例中，请使用 ContosoAutomobileCollection。 
    
  
8. 选择"Web 应用程序和/或 Web API"作为应用程序类型，然后单击向右箭头按钮。
    
  
9. 在对话框的第二页，使用 Visual Studio 解决方案中 ASP.NET 项目的 SSL 调试 URL 作为"登录 URL"。您可以执行下列步骤来查找 URL。 ** *（您最初需要使用调试 URL 注册外接程序，以便您可以运行 Visual Studio 调试程序 (F5)。当您的外接程序准备暂存时，您需使用它的暂存 Azure 网站 URL 重新注册。 [修改外接程序并将其暂存到 Azure 和 Office 365](#Stage)。）* **
    
1. 在"解决方案资源管理器"中突出显示 ASP.NET 项目。 
    
  
2. 在"属性"窗口中，复制"SSL URL"属性的值。例如，https://localhost:44300/。
    
  
3. 将其粘贴到"添加应用程序"对话框中的"登录 URL"。
    
  
10. 对于"应用程序 ID URI"，为应用程序指定一个唯一的 URI，如附加在 SSL URL 结尾的应用程序名称；例如 https://localhost:44300/ContosoAutomobileCollection。
    
  
11. 单击复选标记按钮。应用程序的 Azure 仪表板将打开，并显示一条成功消息。
    
  
12. 在页面顶部选择"配置"。
    
  
13. 滚动到"客户端 ID"并进行复制。稍后您将需要此 ID。
    
  
14. 在"密钥"部分，创建一个密钥。单击页面底部的"保存"，密钥将显示。复制密钥，稍后您将需要此密钥。
    
  
15. 滚动到"其他应用程序的权限"并选择您的 SAP Gateway for Microsoft 服务应用程序。 
    
  
16. 打开"委派权限"下拉列表，并启用您的 SharePoint 外接程序将需要的 SAP Gateway for Microsoft 服务的权限复选框。
    
  
17. 单击屏幕底部的"保存"。
    
  

### 将应用程序配置为与 Azure AD 通信


1. 在 Visual Studio 中，在 ASP.NET 项目中打开 web.config 文件。
    
  
2. 在  `<appSettings>` 部分，Visual Studio Office 开发人员工具 已添加 SharePoint 外接程序的 **ClientID** 和 **ClientSecret** 元素。（如果应用程序访问 SharePoint，这两个元素将用于 Azure ACS 授权系统。在下面的示例中，您可以将它们忽略，但不能删除。在提供程序托管的 SharePoint 外接程序中需要这两个元素，即使外接程序不访问 SharePoint 数据。每当您在 Visual Studio 中按 F5 时，它们的值都会更改。）将以下两个元素添加到此部分。应用程序使用这两个元素来向 Azure AD 进行身份验证。（请记住，在基于 OAuth 的身份验证和授权系统中，应用程序和用户都是安全主体。）
    
  ```
  
<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
  ```

3. 插入在之前的过程中从 Azure AD 目录保存的客户端 ID，作为 **ida:ClientID** 密钥的值。将大小写和标点保持为与您复制时一样，注意不要在值的开头或结尾包含空格字符。对于 **ida:ClientKey** 密钥，使用从目录保存的 *密钥*  。同样，请小心不要插入空格字符或以任何方式更改值。 `<appSettings>` 部分现在应该如下所示。（ **ClientId** 值可能具有 GUID 或空字符串。）
    
  ```
  
<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
  ```


    > [!注释]
      > Azure AD 通过您用于注册应用程序的"localhost"URL 来识别您的应用程序。客户端 ID 和客户端密钥与该标识相关。当您准备好将应用程序暂存到 Azure 网站，时，您需要使用新的 URL 重新注册。 
4. 仍在 **appSettings** 部分，添加 **Authority** 密钥，将其值设置为您的组织帐户的 Office 365 域 ( *some_domain*  .onmicrosoft.com)。在下面的示例中，组织帐户为 Bob@<O365_domain>.onmicrosoft.com，因此标识为 `<O365_domain>.onmicrosoft.com`。 
    
  ```
  
<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  ```

5. 仍在 **appSettings** 部分，添加 **AppRedirectUrl** 密钥，将其值设置为在 ASP.NET 外接程序从 Azure AD 获取授权代码后，用户浏览器应重定向到的页面。通常情况下，这是调用 Azure AD 时用户位于的页面。在下面的示例中，使用 SSL URL 值及其所附加的"/Pages/Default.aspx"，如下所示。（这是您需为暂存更改的另一个值。）
    
  ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  ```

6. 仍在 **appSettings** 部分，添加 **ResourceUrl** 密钥，将其值设置为 SAP Gateway for Microsoft 的 APP ID URI（ *不是*  您的 ASP.NET 应用程序的 APP ID URI）。从 SAP Gateway for Microsoft 管理员那获取此值。示例如下。
    
  ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
  ```


     `<appSettings>` 部分现在应该如下所示：
    


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

7. 保存并关闭 web.config 文件。
    
    > [!提示]
      > 当您运行 Visual Studio 调试程序 (F5) 时，请勿将 web.config 文件保持打开状态。每次您按 F5 时，Visual Studio Office 开发人员工具 都会更改 **ClientId** 值（不是 **ida:ClientID**）。如果 web.config 文件处于打开状态，则您会收到重新加载此文件的提示并做出响应，然后才能执行调试。 

### 添加帮助程序类以对 Azure AD 进行身份验证


1. 右键单击 ASP.NET 项目并使用 Visual Studio 项目添加过程，将新类文件添加到名为 AADAuthHelper.cs 的项目。
    
  
2. 向文件中添加以下 **using** 语句：
    
  ```
  
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

  ```

3. 将访问关键字从 **public** 更改为 **internal** 并将 **static** 关键字添加到类声明。
    
  ```
  
internal static class AADAuthHelper
  ```

4. 将下列字段添加到类。这些字段存储您的 ASP.NET 应用程序用于从 AAD 获取访问令牌的信息。
    
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

5. 将以下属性添加到类。该属性保存到 Azure AD 登录屏幕的 URL。
    
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

6. 将下列属性添加到类。这些属性缓存访问令牌和刷新令牌并检查它们的有效性。
    
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

7. 将下列方法添加到类。这些方法用于检查授权代码的有效性，并使用身份验证代码或刷新令牌从 Azure AD 获取访问令牌。
    
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

8. 将以下方法添加到类。此方法从后台的 ASP.NET 代码调用以获取有效的访问令牌，然后执行调用，通过 SAP Gateway for Microsoft 获取 SAP 数据。
    
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


> [!提示]
> AADAuthHelper 类仅进行最少的错误的处理。要实现生产质量的可靠 SharePoint 外接程序，请增加更多错误处理，如此 MSDN 节点中所述： [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx)。 
  
    
    


### 创建数据模型类


1. 创建一个或多个类，对您的外接程序从 SAP 获取的数据进行建模。在下面的示例中，只有一个数据模型类。 右键单击 ASP.NET 项目并使用 Visual Studio 项目添加过程，将新类文件添加到名为 Automobile.cs 的项目。
    
  
2. 将以下代码添加到类的正文：
    
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


### 添加后台代码，以通过 SAP Gateway for Microsoft 从 SAP 获取数据


1. 打开 Default.aspx.cs 文件，并添加以下 **using** 语句。
    
  ```
  
using System.Net;
using Newtonsoft.Json.Linq;
  ```

2. 将 **const** 声明添加到 Default 类，其值为外接程序将访问的 SAP OData 终结点的基 URL。下面是一个示例：
    
  ```
  
private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
  ```

3. Visual Studio Office 开发人员工具 已添加 **Page_PreInit** 方法和 **Page_Load** 方法。将 **Page_Load** 方法中的代码注释掉，并注释掉整个 **Page_Init** 方法。此代码未在此示例中使用。（如果您的 SharePoint 外接程序将要访问 SharePoint，您应还原此代码。请参阅 [可以选择将 SharePoint 访问权限添加到 ASP.NET 应用程序](#SharePoint)。）
    
  
4. 将以下行添加到 **Page_Load** 方法最前面。这将简化调试过程，因为您的 ASP.NET 应用程序使用 SSL (HTTPS) 与 SAP Gateway for Microsoft 进行通信；但是，您的"localhost:port"服务器未配置为信任 SAP Gateway for Microsoft 的证书。如果没有此代码行，在 Default.aspx 打开之前，您将收到证书无效的警告。某些浏览器允许您单击跳过此错误，但是有些浏览器不会允许您始终打开 Default.aspx。
    
  ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
  ```


    > [!重要信息]
      > 当您准备好将 ASP.NET 应用程序部署到暂存环境之后，删除此行。请参阅 [修改外接程序并将其暂存到 Azure 和 Office 365](#Stage)。 
5. 将以下代码添加到 **Page_Load** 方法。您传递到 `GetSAPData` 方法的字符串是 OData 查询。
    
  ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

  ```

6. 将以下方法添加到 Default 类。此方法首先确保访问令牌缓存具有从 Azure AD 获取的有效访问令牌。然后创建包含访问令牌的 HTTP **GET** 请求并将其发送到 SAP OData 终结点。结果是作为 JSON 对象返回，已转换为 .NET **List** 对象。项目的三个属性用于绑定到 **DataListView** 的阵列。
    
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


### 创建用户界面


1. 打开 Default.aspx 文件，并将以下标记添加到页面的 **form**：
    
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

2. 可以选择通过 SharePoint  [Chrome 控件](use-the-client-chrome-control-in-sharepoint-add-ins.md)和 [主机 SharePoint 网站的样式表](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)为网页设置 SharePoint 页面外观。
    
  

### 在 Visual Studio 中按 F5 测试外接程序


1. 在 Visual Studio 中按 F5。
    
  
2. 第一次使用 F5 时，可能会提示您登录到您使用的 开发人员网站。使用网站管理员凭据。在下面的示例中为 Bob@<O365_domain>.onmicrosoft.com。
    
  
3. 第一次使用 F5 时，将提示您授予对外接程序的权限。单击"信任它"。
    
  
4. 获取访问令牌时的短暂延迟后，Default.aspx 页面将打开。确认 SAP 数据显示。
    
  

## 可以选择将 SharePoint 访问权限添加到 ASP.NET 应用程序
<a name="SharePoint"> </a>

当然，您的 SharePoint 外接程序不一定要仅将 SAP 数据显示在从 SharePoint 启动的网页中。它还可以创建、读取、更新和删除 (CRUD) SharePoint 数据。您的后台代码可以使用 SharePoint 客户端对象模型 (CSOM) 或 SharePoint 的 REST API 执行此操作。CSOM 部署为一对程序集，Visual Studio Office 开发人员工具 会将其自动包含在 ASP.NET 项目中并将其设置为 Visual Studio 中的"Copy Local"，因此它们可以包含在 ASP.NET 应用程序包中。有关使用 CSOM 的信息，请首先阅读 [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)。有关使用 REST API 的信息，请首先阅读 [了解和使用 SharePoint 2013 REST 接口](http://msdn.microsoft.com/zh-cn/magazine/dn198245.aspx)。 
  
    
    
不管您使用 CSOM 还是 REST API 访问 SharePoint，您的 ASP.NET 应用程序必须获取 SharePoint 的访问令牌，就像必须获取 SAP Gateway for Microsoft 的访问令牌一样。请参阅上面的 [了解 SAP Gateway for Microsoft 和 SharePoint 的身份验证和授权](#AuthOverview)。下面的过程提供了关于如何执行此操作的基本指导，但是我们建议您首先阅读下列文章：
  
    
    

-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序的三个授权系统](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [创建使用低信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
-  [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  

1. 打开 Default.aspx.cs 文件并取消对 **Page_PreInit** 方法的注释。另外取消对 Visual Studio Office 开发人员工具 添加到 **Page_Load** 方法的代码的注释。
    
  
2. 如果您的 SharePoint 外接程序要访问 SharePoint 数据，则当外接程序在 SharePoint 中启动时，您必须将已执行 POST 操作的 SharePoint 上下文令牌缓存到 Default.aspx 页面。这是为了确保当浏览器按照 Azure AD 身份验证重定向时，SharePoint 上下文令牌不会丢失。（您具有缓存此上下文方式的多个选项。）Visual Studio Office 开发人员工具 将 SharePointContext.cs 文件添加到执行大部分工作的 ASP.NET 项目。要使用会话缓存，您只需在" `if (!IsPostBack)`"块内，在引出 SAP Gateway for Microsoft 的代码 *前面*  添加以下代码：
    
  ```
  
if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
  ```

3. SharePointContext.cs 文件调用 Visual Studio Office 开发人员工具 添加到项目中的另一个文件：TokenHelper.cs。该文件提供获取和使用 SharePoint 的访问令牌所需的大部分代码。但是，它不提供续订已过期访问令牌或已过期的刷新令牌的任何代码。它也不包含任何令牌缓存代码。要实现生产质量 SharePoint 外接程序，您需要添加此类代码。例如前一步中的缓存逻辑。您的代码还应该缓存访问令牌并重复使用，直至其过期。当访问令牌过期时，您的代码应使用刷新令牌获取新的访问令牌。
    
  
4. 使用 CSOM 或 REST 添加对 SharePoint 的数据调用。以下示例是修改 Visual Studio Office 开发人员工具 添加到 **Page_Load** 方法的 CSOM 代码。在本示例中，代码已移至单独的方法中，并通过检索已缓存的上下文令牌来启动。
    
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

5. 添加 UI 元素以呈现 SharePoint 数据。下面显示了在前一方法中引用的 HTML 控件：
    
  ```
  
<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
  ```


> [!注释]
> 调试 SharePoint 外接程序时，每次您在 Visual Studio 中按 F5 时，Visual Studio Office 开发人员工具 都会将其在 Azure ACS 中重新注册。当您暂存 SharePoint 外接程序时，您必须对其进行长期注册。请参阅 [修改外接程序并将其暂存到 Azure 和 Office 365](#Stage)部分。 
  
    
    


## 修改外接程序并将其暂存到 Azure 和 Office 365
<a name="Stage"> </a>

当您在 Visual Studio 中使用 F5 调试完 SharePoint 外接程序之后，您需要将 ASP.NET 应用程序部署到实际的 Azure 网站。
  
    
    

### 创建 Azure 网站


1. 在 Microsoft Azure 门户中，在左侧导航栏上打开"网站"。
    
  
2. 在页面底部单击"新建"，在"新建"对话框中选择"网站"|"快速创建"。
    
  
3. 输入域名并单击"创建网站"。复制新网站的 URL。它的格式将为  `my_domain.azurewebsites.net`。
    
  

### 修改应用程序中的代码和标记


1. 在 Visual Studio 中，从 Default.aspx.cs 文件中删除行  `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;`。
    
  
2. 打开 ASP.NET 项目的 web.config 文件，将 **appSettings** 部分中 **AppRedirectUrl** 密钥值的域部分更改为 Azure 网站 的域。例如，将 `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` 更改为 `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`。
    
  
3. 在 SharePoint 外接程序项目中右键单击 AppManifest.xml 文件并选择"查看代码"。
    
  
4. 在 **StartPage** 值中，将字符串 `~remoteAppUrl` 替换为 Azure 网站 的完整域（包括协议）；例如 `https://my_domain.azurewebsites.net`。完整的 **StartPage** 值现在应该如下所示： `https://my_domain.azurewebsites.net/Pages/Default.aspx`。（通常情况下， **StartPage** 值与 web.config 文件中 **AppRedirectUrl** 密钥的值完全相同。）
    
  

### 修改 AAD 注册并在 ACS 中注册外接程序


1. 使用 Azure 管理员帐户登录到  [Azure 管理门户](https://manage.windowsazure.com)。
    
  
2. 在左侧选择"Active Directory"。
    
  
3. 单击您的目录。
    
  
4. 选择"应用程序"（位于顶部导航栏中）。
    
  
5. 打开您创建的应用程序。在下面的示例中，为"ContosoAutomobileCollection"。
    
  
6. 对于下面每个值，将值的"localhost: *port*  "部分更改为新 Azure 网站 的域：
    
  - **登录 URL**
    
  
  - **APP ID URI**
    
  
  - **回复 URL**
    
  

    例如，如果"APP ID URI"为"https://localhost:44304/ContosoAutomobileCollection"，将其更改为"https://<my_domain>.azurewebsites.net/ContosoAutomobileCollection"。
    
  
7. 单击屏幕底部的"保存"。
    
  
8. 在 Azure ACS 中注册外接程序。即使外接程序不访问 SharePoint，不使用 ACS 中的令牌，也必须执行此操作，这是因为此过程也会在 Office 365 订阅的外接程序管理服务中注册外接程序，这是必要条件。（称为"外接程序管理服务"是因为 SharePoint 外接程序最初称为"SharePoint 相关应用程序"）您可在 Office 365 订阅中任何 SharePoint 网站的 AppRegNew.aspx 页面上执行注册。有关详细信息，请参阅 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)。作为此过程的一部分，您需获取新的客户端 ID 和客户端机密。将这些值插入 **ClientId**（不是 **ida:ClientID**）和 **ClientSecret** 密钥的 web.config。
    
    > [!警告]
      > 进行此更改，不论您出于何种原因按 F5，Visual Studio Office 开发人员工具 将覆盖一个或两个值。因此，您应该记录在 AppRegNew.aspx 中获取的值，并始终在发布 ASP.NET 应用程序之前确认 web.config 中的值正确无误。 

### 将 ASP.NET 应用程序发布到 Azure 并将外接程序安装到 SharePoint


1. 有几种方法可将 ASP.NET 应用程序发布到 Azure 网站。有关详细信息，请参阅 [如何部署 Azure 网站](http://azure.microsoft.com/zh-cn/documentation/articles/web-sites-deploy/)。
    
  
2. 在 Visual Studio 中，右键单击 SharePoint 外接程序项目并选择"数据包"。在打开的"发布外接程序"页上，单击"打包外接程序"。文件资源管理器将打开到包含外接程序包的文件夹。
    
  
3. 以全局管理员身份登录到 Office 365，导航到组织外接程序目录网站集。（如果不存在，请进行创建。请参阅 [使用外接程序目录使自定义业务外接程序对 SharePoint Online 环境可用](http://office.microsoft.com/zh-cn/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)。）
    
  
4. 将外接程序包上载到外接程序目录。
    
  
5. 导航到订阅中任何网站的"网站内容"页面，并单击"添加外接程序"。
    
  
6. 在"您的外接程序"页上，滚动到"您可以添加的外接程序"部分，并单击您的外接程序的图标。
    
  
7. 安装外接程序后，在"网站内容"页上单击它的图标启动外接程序。
    
  
有关安装 SharePoint 外接程序的详细信息，请参阅 [部署和安装SharePoint 外接程序：方法和选项](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)。
## 将外接程序部署到生产环境
<a name="Stage"> </a>

执行完所有测试后，即可将外接程序部署到生产环境。这可能需要进行一些变更。
  
    
    

1. 如果 ASP.NET 应用程序的生产域与暂存域不同，您将需要更改 web.config 中的 **AppRedirectUrl** 值和 AppManifest.xml 文件中的 **StartPage** 值，并重新打包 SharePoint 外接程序。请参阅上面的过程 **修改应用程序中的代码和标记** 。
    
  
2. 域中的更改也要求您编辑 AAD 中的外接程序注册。请参阅上面的过程 **修改 AAD 注册并在 ACS 中注册外接程序** 。
    
  
3. 域中的更改也要求您在 ACS（以及订阅的外接程序管理服务）中重新注册外接程序，如相同过程中所述。（无法 *编辑*  ACS 中的外接程序注册。）但是，无需在 AppRegNew.aspx 页面上生成新的客户端 ID 或客户端机密。您可以将原始值从 web.config 的 **ClientId**（不是 **ida:ClientID**）和 **ClientSecret** 密钥复制到 AppRegNew 表中。 *如果您不生成新的客户端 ID 或客户端机密，请务必将新值复制到 web.config 中的密钥。* 
    
  

