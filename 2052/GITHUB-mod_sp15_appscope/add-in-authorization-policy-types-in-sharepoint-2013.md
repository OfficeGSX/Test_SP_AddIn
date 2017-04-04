---
title: SharePoint 2013 中的外接程序授权策略类型
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# SharePoint 2013 中的外接程序授权策略类型
了解 SharePoint 中的外接程序的不同授权策略：仅外接程序策略、用户 + 外接程序策略和仅用户策略。本文还提供了使用仅外接程序策略的指南。
在阅读本文之前，您应该先熟悉文章  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)和 [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)。
  
    
    


## 了解外接程序授权策略类型的概述
<a name="Overview"> </a>

SharePoint 提供了三种类型的授权策略：
  
    
    

- **仅用户策略** - 当使用仅用户策略时，SharePoint 仅检查用户的权限。当用户直接访问资源而不使用外接程序时（例如，当用户第一次打开 SharePoint 网站的主页或从 PowerShell 访问 SharePoint API 时），SharePoint 将使用该策略。
    
    
    
  
- **用户 + 外接程序策略** - 当使用用户 + 外接程序策略时，SharePoint 会同时检查用户和外接程序主体的权限。仅在当前用户和外接程序都具有执行有问题的操作的权限时，授权检查才会成功。
    
    例如，当 SharePoint 外接程序需要获得对 SharePoint 上的用户资源的访问权限时需要用到此策略。（SharePoint 外接程序的远程组件中的代码应设计为可对 SharePoint 执行用户 + 外接程序调用。
    
    
    
  
- **仅外接程序策略** - 当使用仅外接程序策略时，SharePoint 仅检查外接程序主体的权限。仅在当前外接程序具有执行有问题的操作的权限时，授权检查才会成功，而与当前用户的权限（如果有）无关。
    
    费用审批外接程序是针对使用此策略而设计的外接程序一个示例。外接程序让不能批准费用的用户能够批准低于某个特定数目的费用。请参阅以下方案，了解详细信息。 
    
    
    
    > **注释**
      > 某些 API 需要用户上下文，并且您无法使用仅外接程序策略执行它。这包括很多用于与 Project Server 2013 交互的 API 和用于执行搜索查询的 API。 

### 请参阅使用仅外接程序策略的外接程序的一个示例方案
<a name="Scenario"> </a>

假定有一个名为 Adam 的 Contoso 销售经理购买了使用仅外接程序策略的费用提交外接程序。当 Adam 选择购买该外接程序时，系统提示他允许外接程序提升用户权限；即，允许外接程序对 SharePoint 执行仅外接程序调用。Adam 为外接程序授予了所请求的权限。然后，他购买了足够多的外接程序许可证，供所有 Contoso 销售人员使用，并且他将此外接程序安装在了销售团队的 SharePoint 网站中。
  
    
    
很快，销售人员就能使用新的费用提交外接程序提交费用报告。销售人员通常不能批准自己的费用报告，但如果使用外接程序就可以，因为 Adam 将外接程序设置为可以自动批准 50 美元以下报告，所以外接程序就具有了让销售人员自行批准费用提交的功能。外接程序会自动为他分配审批 50 美元或以上的报告任务。这可以通过为 SharePoint 外接程序提供对含已批准费用的 SharePoint 列表的写入权限来实现。但是，在用户之中，仅人力资源经理具有对该列表的写入权限。如果费用少于 50 美元，外接程序中的代码将通过对 SharePoint 执行仅外接程序调用将费用添加到该列表。由于不会检查用户的权限，50 美元以下的任何用户的提交都会被自动添加到已批准的费用列表中，即使用户对该列表没有写入权限也是如此。
  
    
    

  
    
    

### 了解外接程序如何获得使用仅外接程序策略的权限
<a name="Approve"> </a>

为了能够对 SharePoint 执行仅外接程序的调用，您的外接程序必须请求使用仅外接程序策略的权限。应在外接程序清单中提出此请求。为此，可将 **AllowAppOnlyPolicy** 属性添加到 **AppPermissionRequests** 元素并将它设置为 **true**，如以下标记中所示：
  
    
    

```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>
```


> **注释**
> SharePoint 外接程序 以前称为"SharePoint 相关应用程序"。若要保持向后兼容性，该应用部件清单 (manifest) 架构未做更改，因此字符串"app"会显示在可能的元素和属性名称中。 
  
    
    

系统将提示用户安装外接程序批准此请求。如果外接程序要求使用租户范围内的权限，那么仅租户管理员可以授予使用仅外接程序策略权限，因此只有租户管理员可以安装外接程序。如果外接程序不要求使用较网站集范围更大的任何权限，那么网站集管理员就可以安装外接程序。有关权限的作用域的详细信息，请参阅  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)。
  
    
    

### 了解外接程序如何进行仅外接程序的调用
<a name="AppOnlyCalls"> </a>

对 SharePoint 执行仅外接程序调用与用户 + 外接程序调用之间的差异是包含在调用中访问令牌的类型。以下代码显示如何获取托管代码中的用户 + 外接程序和仅外接程序的访问令牌。TokenHelper.cs（或.vb）文件中为您提供了详细的编码，介绍了 Visual Studio Office 开发人员工具 自动添加到 Visual Studio 中的 项目 。
  
    
    

```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+add-in access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...
    
      //Get add-in-only access token.
       string addinOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}
```


> **注释**
> 不进行 OAuth 身份验证调用的外接程序（例如，仅包含在外接程序 Web 中运行的 JavaScript 的外接程序）无法使用仅外接程序策略。它们可以请求该权限，但无法利用该权限，因为这样做需要传递仅外接程序 OAuth 令牌。只有具有在 SharePoint 外部运行的 Web 应用程序的外接程序能创建和传递仅外接程序令牌。 
  
    
    

一般而言，需要存在当前用户才能进行调用。对于仅外接程序策略，SharePoint 将创建一个 SHAREPOINT\\APP，这与现有的 SHAREPOINT\\SYSTEM 用户类似。所有仅外接程序请求均由 SHAREPOINT\\APP 提出。无法通过基于用户的身份验证以 SHAREPOINT\\APP 形式进行验证。
  
    
    

### 获取使用仅外接程序策略的指南
<a name="GuidelinesFor"> </a>

因为仅外接程序调用可有效地提升用户特权，您应保守地创建外接程序，以请求为其提供权限。调用仅在如下情况下应使用仅外接程序策略：
  
    
    

- 外接程序需要将其权限提升得高于用户以进行特定调用；例如，在外接程序评估的条件下审批费用报告。
    
  
- 外接程序未代表任何用户；例如，SharePoint 文档库上执行夜间维护任务的外接程序。
    
  

## 其他资源
<a name="AR"> </a>


-  [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [SharePoint 外接程序的上下文令牌 OAuth 流](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 外接程序](sharepoint-add-ins.md)
    
  
-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

