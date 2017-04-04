---
title: 在 SharePoint 2013 中获取用户标识和属性
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# 在 SharePoint 2013 中获取用户标识和属性
在 SharePoint 2013 中检索用户标识和用户信息。
可通过不同的方法检索用户标识和信息，具体取决于要检索的信息。本文将演示可用来完成该操作的一些方法。
  
    
    


## 检索用户标识和属性的先决条件
<a name="Prereq"> </a>


- 准备外接程序开发环境，如 [设置 SharePoint 加载项的本地开发环境](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)中所述。
    
  
- 安装 Visual Studio。
    
  
- 安装  [Visual Studio 2013 的 Office 开发工具](http://aka.ms/OfficeDevToolsForVS2013)的最新版本或  [Visual Studio 2015 的 Office 开发工具](http://aka.ms/OfficeDevToolsForVS2015)。
    
  

> **注释**
> 有关如何设置符合您的需求的开发环境的指南，请参阅 [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)。 
  
    
    


### 检索用户标识和属性要了解的核心概念

下表列出了一些有助于您理解创建 SharePoint 外接程序所涉及的概念的有用文章。
  
    
    


|**文章**|**说明**|
|:-----|:-----|
| [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md) <br/> |了解要用于 SharePoint 2013 的 SharePoint 外接程序权限，包括外接程序权限类型，权限请求范围和管理权限。本文还将讨论外接程序许可权限和用户权限的区别。  <br/> |
| [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |了解云托管的外接程序的 OAuth 身份验证和授权流。  <br/> |
| [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |了解如何使用 Visual Studio 2012 Office 开发人员工具创建提供程序承载的基本 SharePoint 外接程序，如何使用 SharePoint CSOM 与 Microsoft SharePoint 2013 网站交互，以及如何在 SharePoint 外接程序中实现 OAuth。  <br/> |
   

## 检索当前网站用户标识
<a name="WebsiteUserID"> </a>

检索网站的当前用户的标识的最简单的方法是通过 **Web** 对象。通过在项目中使用 TokenHelper.cs 文件，您可以使用以下代码段获取当前网站用户标识。
  
    
    

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


- 如果使用的是 Office 365，您获取的登录名将类似于  `i:0#.f|membership|adam@contoso.com`。
    
  
- 如果使用的是本地 Microsoft SharePoint 2013，并且用户使用 NTLM 以正常用户身份登录，您获取的登录名将类似于  `i:0#.w|contoso\\adam`。
    
  
- 如果使用的是本地 SharePoint 2013，并且用户使用场帐户登录，您获取的登录名将类似于  `SHAREPOINT\\System`。
    
  

## 使用 ResolvePrincipal 方法检索用户标识
<a name="ResolvePrincipal"> </a>

下面是检索用户的登录名的另一种方法。如果您具有用户的电子邮件地址或显示名称，则可以使用  [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) 方法获取用户的登录名。
  
    
    

> **注释**
> API 位于  [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/zh-cn/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx) 程序集中的 Microsoft.SharePoint.Client.Utilities 命名空间中。
  
    
    

下面是一个显示如何获取用户登录信息的示例代码。
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

 **Person.LoginName** 值提供了登录信息。
  
    
    

## 检索用户标识和配置文件属性
<a name="Profile"> </a>

如果要检索用户的标识和属性，则可以使用 OAuth 令牌和社会功能 API。首先，获取 OAuth 令牌并将其设置为客户端上下文。以下示例代码显示了如何获取用户配置文件属性。
  
    
    

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

然后，使用  [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) API 获取使用外接程序的用户的属性。
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

若要让代码正常运行：
  
    
    

- 应在 SharePoint 2013 上为用户配置和同步用户配置文件共享服务。
    
  
- 必须在外接程序清单中为社会功能添加以下权限范围：
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

API 位于 Microsoft.SharePoint.Client.UserProfiles.dll 中。
  
    
    
下面是显示如何访问用户配置文件存储的另一个示例代码段。
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## 其他资源
<a name="AdditionalResources"> </a>


-  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序](sharepoint-add-ins.md)
    
  
-  [设置 SharePoint 加载项的本地开发环境](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

