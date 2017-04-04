---
title: 在提供程序托管的低信任 SharePoint 外接程序中处理安全令牌
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# 在提供程序托管的低信任 SharePoint 外接程序中处理安全令牌
了解提供程序承载的低信任 SharePoint 外接程序用于授权的上下文、访问和刷新令牌，以及如何将这些令牌用于代码。
> [!重要信息]
> **本文完全关于安全令牌在低信任授权系统（而不是高信任系统）中的使用。** 有关令牌在高信任系统中的使用的信息，请参阅 [在提供程序托管的高信任 SharePoint 外接程序中创建和使用访问令牌](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)。 
  
    
    

 **使用 [低信任授权系统](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)获取 SharePoint 数据的访问权限的 SharePoint 外接程序会参与 OAuth 流，其中涉及在 SharePoint、Microsoft Azure 访问控制服务 (ACS) 和 SharePoint 外接程序的远程组件（有时是用户的浏览器）之间传递安全令牌（格式为  [JSON Web 令牌](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)）。** 有几种不同的工作流，具体取决于外接程序的设计，但所有工作流均至少涉及下列两种令牌：
- **访问令牌：**包含在外接程序的远程组件向 SharePoint 发出的每个创建、读取、更新或删除 (CRUD) 请求者中。SharePoint 将验证令牌并为请求提供服务。
    
  
- **刷新令牌：**用于获取 [上下文令牌流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中的优先访问令牌，并替换低信任授权系统的上下文令牌流和 [授权代码流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中的过期访问令牌。
    
  
根据外接程序所使用的 OAuth 流，此过程可能也包含下列两项内容之一：
- **上下文令牌：**用于上下文令牌流中，向远程组件提供刷新令牌以及从 Azure ACS 请求访问令牌所需的信息。
    
  
- **授权代码：**不是令牌，而是授权代码，对每个用户和应用程序对均具有唯一性。它用于授权代码流，以获取优先访问令牌和刷新令牌。
    
  

## 了解访问令牌的处理
<a name="AccessTokens"> </a>

在低信任授权系统中，访问令牌由 Azure ACS 创建并发送到 SharePoint 外接程序的远程组件。（在本文编写时，ACS 颁发的 SharePoint 访问令牌的有效期为 12 小时，这可能会变更。） **您的 SharePoint 外接程序中的代码需要执行的主要操作** 如下：
  
    
    

- **从 Azure ACS 请求访问令牌** 。根据所使用的 OAuth 流，外接程序使用授权代码或它从上下文令牌提取的信息来发出请求。
    
  
- **在对 SharePoint 的每个 HTTP 请求中包含令牌。** 令牌添加为请求的 **Authorization** 标头。标头的值为"Bearer"，后跟空格，然后是 Base 64 编码的基本访问令牌。
    
  
- **缓存访问令牌** ，以便在后续请求中重复使用，这是可选操作，但建议这样做。
    
  
- （可选）将访问令牌转发到后端系统，以便它们可以直接访问 SharePoint
    
  
这些任务必须通过服务器端代码执行。如果您使用托管代码，某些任务的示例代码请参见 Visual Studio Microsoft Office 开发人员工具中的 SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb）文件。有关执行部分任务的 PHP 代码示例，请参阅 [了解和使用 SharePoint 2013 REST 接口](http://msdn.microsoft.com/zh-cn/magazine/dn198245.aspx)。
  
    
    

### 缓存访问令牌
<a name="CacheAccessToken"> </a>

根据您的 SharePoint 外接程序的体系结构和承载平台，有多种方法 **可以在服务器上缓存访问令牌** 。
  
    
    

- 在会话状态中
    
  
- 在应用程序状态中
    
  
- 在  [Windows Server AppFabric 缓存](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx)或其相当的非 Microsoft 操作系统中。
    
  
- 在  [Microsoft Azure 缓存服务](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx)或其相当的非 Microsoft 云服务中。
    
  
- 在数据库中
    
  
- 在  [memcached](http://www.memcached.org/) 系统中
    
  

> [!注释]
> 在大多数情况下，您将无法使用"AccessToken"这样简单的术语作为缓存关键词，因为您的外接程序必须为不同的用户和 SharePoint 服务器场/租户保留令牌。如果您的外接程序使用 [上下文令牌流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)，则有 SharePoint 提供的特殊 **CacheKey** 可用于区分缓存令牌。本节介绍当您的应用程序不使用上下文令牌流时有哪些问题，以及如何处理这些问题。
  
    
    

在大多数情况下均可在会话状态下缓存访问令牌。如果远程 Web 应用程序访问使用 OAuth（除 SharePoint 以外）的其他服务，且在会话状态下缓存各种访问令牌，请务必对各个令牌使用不同的缓存密钥；例如，请勿使用"AccessToken"，而应使用"SharePoint_AccessToken"、"Facebook_AccessToken"、"SAP_Gateway_AccessToken"，等等。（如果您不使用会话状态或其他会自动分离每个用户的缓存的其他缓存，则您还需对用户的密钥进行相对化处理。）
  
    
    
如果应用程序访问多个 SharePoint 服务器或在线租户，您可以将 SharePoint 域用作应用程序的主要缓存密钥 ("SharePoint_ *<mydomain>*  .sharepoint.com_AccessToken") 的一部分，或者使用服务器和/租户的域 ("SharePoint_ *<realmGUID>*  _AccessToken")，两者均可从访问令牌读取。（您的代码需要还原令牌的 Base 64 编码才能进行读取。在托管代码中，TokenHelper.cs 或 .vb 文件具有用于此目的的示例代码，可使用 **Microsoft.IdentityModel.S2S.Tokens** 和 **System.IdentityModel.Tokens** 命名空间中的类。）当应用程序使用上下文令牌时，提供另一个选项，如下一段中所述。
  
    
    
在某些情况下，您的应用程序需要在跨会话或在会话结束之后对应用程序可用的某些位置缓存访问令牌。例如，应用程序可能设计为允许用户将操作计划为在用户关闭应用程序之后执行。如果这些操作包括访问 SharePoint，则外接程序将需要检索访问令牌。在这种情况下， **您的应用程序必须为不同用户保存不同的访问令牌** 。如果您使用上下文令牌流，将在上下文令牌中提供用于此目的的缓存密钥字符串，外接程序启动后，SharePoint 会将此上下文令牌传递到 SharePoint 外接程序的远程组件。有关此 **特殊缓存密钥** 以及如何使用它的详细信息，请参阅 [了解缓存密钥](#CacheKey)。您还可以将此字符串用于前一段中所述的情况。计划系统具有多种方式可存储它要使用的配置数据，例如何时执行计划工作以及应该做什么。使用此存储保存用于访问令牌的缓存密钥。
  
    
    
如果您使用的跨会话缓存也由多个应用程序共享，则必须将缓存密钥对应用程序、用户和 SharePoint 域进行相对化处理。在上下文令牌中提供的缓存密钥对应用程序、用户和 SharePoint 域具有唯一性。
  
    
    
 **如果您的应用程序使用授权代码流** ，则说明没有上下文令牌，因此没有特制的缓存密钥。在这种情况下， **您将需要创建自己的密钥系统以存储缓存数据** ，缓存数据已针对下面一项或多项进行相对化处理，具体取决于在应用程序的用例中可能发生什么潜在的命名冲突：用户、SharePoint 域和应用程序。您可以将访问令牌内的声明用于此目的；例如， **nameId** 和 **aud**（参见下列表格）。您的代码可以将字符串串联起来，或者将其作为种子来创建可作为缓存密钥的唯一哈希。
  
    
    
最后，如果您的应用程序同时对 SharePoint 进行仅外接程序和用户+外接程序调用，则有两种不同的访问令牌。因此，您需要不同的缓存密钥。当您决定使用某个基本缓存密钥后，只需向其附加"_add-in-only"或"_add-in+user"。
  
    
    

> [!警告]
> **将访问令牌存储在 Cookie 中并不是一种安全的做法。** 避免将访问令牌通过浏览器传递通常是一种好的做法。
  
    
    


### 将访问令牌转发到后端系统
<a name="ForwardTokenToBackend"> </a>

SharePoint 外接程序的一些后端服务器可能未托管在与远程 Web 应用程序相同的域中。当后端服务器需要在 SharePoint 上执行 CRUD 操作时，如果这些操作可以直接从后端系统进入 SharePoint，则外接程序可以更快的速度执行。幸运的是，仅当您的应用程序从 ACS 获取访问令牌时，域才重要。具有令牌后，可以将其转发到后端服务，它们可以通过使用 SharePoint 对其进行调用。这些系统中的代码将需要处理过期的访问令牌并将新访问令牌的请求发送回实际已在 ACS 中注册的父 Web 应用程序。请参阅 [处理过期的访问令牌](#Expired)。该技术应该仅用于您的应用程序自己的后端服务，不用于外部 Web 服务。此外，如果您使用此技术，请考虑将后端服务设计为在所有可能的情况下使用仅外接程序调用。
  
    
    

### 处理过期的访问令牌
<a name="Expired"> </a>

访问令牌在几小时后过期（在本文编写时为 12 小时，但这可能变更）。如果应用程序在访问令牌过期后仍访问 SharePoint，过期后对 SharePoint 的第一个请求将导致 **401 Unauthorized** 错误。您的代码必须处理此响应。或者，代码也可以在使用之前测试访问令牌的过期时间。 **您的代码使用刷新令牌从 ACS 获取另一个访问令牌。** 在上下文令牌流中，刷新令牌包含在启动 SharePoint 的第一个会话时，您的外接程序从 SharePoint 接收的上下文令牌中。在授权代码流中，它将与优先访问令牌一起传递到应用程序。您的代码必须将其缓存，使其在需要时可用。刷新令牌将持续数月，并且可在 Cookie 或服务器端存储中保存。有关详细信息，请参阅 [了解刷新令牌的处理和缓存](#RefreshToken)。
  
    
    

### 参阅访问令牌的示例
<a name="ExampleAccessTokens"> </a>

本节举例说明了访问令牌，并说明它们有何区别，具体取决于所使用的授权策略。
  
    
    

#### 请参阅低信任授权系统的访问令牌

 **用户+外接程序策略**
  
    
    
下面是 ** ACS 生成的用户+外接程序访问令牌的解码示例** ，可用于使用SharePoint [用户+外接程序策略](add-in-authorization-policy-types-in-sharepoint-2013.md)进行调用。已添加空格以确保可读性，令牌符合  [JSON Web 令牌](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1)协议。令牌中的 JavaScript 对象表示法 (JSON) 对象称为声明集。有关声明集中的属性的详细信息，请参阅表 1。请注意，所有值都必须为小写。（用户+外接程序访问令牌在 [上下文令牌流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)和 [授权代码流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中相同。）
  
    
    



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


**表 1：ACS 颁发的用户+外接程序访问令牌声明**


|**声明**|**说明**|**示例访问令牌中相应的值**|
|:-----|:-----|:-----|
| `aud` <br/> |"audience"的简写，表示令牌所适用的主体。格式为  _audience principal ID_/ _SharePoint domain_@ _SharePoint realm_，其中  _audience principal ID_ 是 SharePoint 的永久安全主体 ID。（请参阅 [Microsoft 产品应用程序主体常量](http://msdn.microsoft.com/zh-cn/library/hh629982%28v=office.12%29.aspx)。）  <br/> . _SharePoint realm_ 为访问令牌用于访问的 SharePoint Online 租户或内部部署 SharePoint 场的 GUID。此 GUID 充当令牌颁发者的域 ID，在本示例中 Azure ACS。 <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |"issuer"的简写。它表示创建令牌的主体。格式为  _Issuer GUID_@ _SharePoint realm GUID_。  <br/> 在低信任授权系统中，颁发者为 Azure ACS，其 GUID 为 **00000001-0000-0000-c000-000000000000** 。 <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |"not before"的简写。它表示令牌 *生效*  的时间，以自 1970 年 1 月 1 日的秒数为单位。默认情况下，该值设置为令牌创建的时间。有关详细信息，请参阅 [使用 JWT 时间值](#JWTtimes)。  <br/> |1377549246  <br/> |
| `exp` <br/> |"expiration"的简写。它表示令牌过期的时间。默认情况下，这为 **nbf** 时间后 12 小时。有关详细信息，请参阅 [使用 JWT 时间值](#JWTtimes)。  <br/> |1377592446  <br/> |
| `nameid` <br/> |是发放令牌所代表的用户的唯一标识符。格式根据标识提供者的不同而异。在此示例中，提供者是 Microsoft Online，但如果是 Active Directory，ID 将类似于"s-1-5-21-2127521184-1604012920-1887927527-415149"。  <br/> |2303000085ff9abc  <br/> |
| `actor` <br/> |需要 SharePoint 服务器场或租户的访问权限的主体。其格式为  _Client ID of Application_@ _SharePoint realm_。  <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `identityprovider` <br/> |通过 Internet 编号分配机构 (IANA) 注册的标识提供者的唯一名称。  <br/> 对于安装到 SharePoint Online 的外接程序，值通常与本示例中相同。  <br/> 对于安装到本地服务器场的外接程序，通常为本地标识提供程序，例如"urn:office:idp:activedirectory"。  <br/> |urn:federation:microsoftonline  <br/> |
   
 **仅外接程序策略**
  
    
    
下面 ** ACS 生成的仅外接程序访问令牌的解码示例** ，可用于使用 [仅外接程序策略](add-in-authorization-policy-types-in-sharepoint-2013.md)进行 SharePoint 调用。已添加空格以确保可读性，令牌符合  [JSON Web 令牌](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1)协议。有关声明集中的属性的详细信息，请参阅表 2。（仅外接程序策略不可用于使用 [授权代码流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)的应用程序，因为它们没有外接程序清单文件，因此无法请求使用仅外接程序调用的权限。）
  
    
    



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


**表 2：ACS 颁发的仅外接程序访问令牌声明**


|**声明**|**说明**|**示例访问令牌中相应的值**|
|:-----|:-----|:-----|
| `aud` <br/> |与上面的用户+外接程序令牌相同。  <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |与上面的用户+外接程序令牌相同。  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |与上面的用户+外接程序令牌相同。  <br/> |1403304705  <br/> |
| `exp` <br/> |与上面的用户+外接程序令牌相同。  <br/> |1403347905  <br/> |
| `nameid` <br/> |外接程序（而不是用户）的唯一标识符，因为用户的标识对于仅外接程序策略并不重要。格式为  _客户端 ID_@ _SharePoint realm_。  <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `sub` <br/> |"subject"的简写。它是令牌的主题，即需要 SharePoint 的访问权限的主体；在本示例中，为远程 Web 应用程序。对象 ID 用于此值。请参阅下面的 **oid** 声明。 <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `oid` <br/> |"对象 ID"的简写。它是远程 Web 应用程序的 Azure Active Directory 中的对象 ID。在仅外接程序访问令牌中，主题和对象 ID 为同一个值。  <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `trustedfordelegation` <br/> |指定 SharePoint 是否应该信任 SharePoint 外接程序来对用户进行身份验证和授权用户的布尔值。它在仅外接程序调用中为假，因为用户标识并不重要。  <br/> |FALSE  <br/> |
| `identityprovider` <br/> |标识提供程序的唯一名称。因为它是提供了标识的外接程序，而不是用户，则 ACS 是标识提供程序。其格式为  _ACS GUID_@ _SharePoint realm_。  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
   

## 了解上下文令牌的结构和处理
<a name="ContextTokens"> </a>

上下文令牌仅用于低信任授权系统的 [上下文令牌流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)。 **当 SharePoint 外接程序在 SharePoint 中启动时，SharePoint 请求 Azure ACS 创建上下文令牌，然后 SharePoint 会将其传递到 SharePoint 外接程序的远程组件。** 令牌在从 SharePoint 发出的对远程组件启动页面的请求中，作为隐藏的表单参数传递，称为 **SPAppToken**。令牌使用仅对 ACS 和 SharePoint 外接程序已知的客户端机密进行签名。 **上下文令牌包括外接程序使用的刷新令牌** ，以及上下文令牌中的其他信息， **以便从 ACS 请求访问令牌** 。（在本文编写时，ACS 颁发的 SharePoint 上下文令牌的有效期为 12 小时，这可能会变更。 **SharePoint 外接程序** 中的代码的主要任务如下：
  
    
    

- 使用外接程序的客户端机密 **验证上下文令牌** 。
    
  
- **缓存上下文令牌** 或提取并单独缓存刷新令牌及其中的其他特定项目。
    
  
- 使用刷新令牌和其他信息从 ACS **请求访问令牌** （然后将其缓存）。
    
  
- 从上下文令牌 **缓存 CacheKey** 。
    
  

> [!重要信息]
> 前两个任务必须在用户导航到其他页面或刷新页面或者在令牌丢失之前执行。例如，在 ASP.NET Web 表单应用程序中，考虑使用 **Page_Load** 方法执行这些任务（在仅当请求不是回发时运行的条件代码块中）。在 ASP.NET MVC 应用程序中，考虑使用默认的控制器方法执行这些任务。
  
    
    

如果您使用托管代码，某些任务的示例代码请参见 Visual Studio Microsoft Office 开发人员工具中的 SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb）文件。您只需对 TokenHelper 类的成员进行简单调用。例如，您的代码可以使用以下单行代码执行第一项任务：
  
    
    



```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);
```

有关如何使用 PHP 执行其中部分任务的示例，请参阅示例  [SharePoint 2013：从 PHP 网站对 SharePoint 文档库执行操作](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)。
  
    
    

### 缓存上下文令牌或其中的一部分
<a name="CacheContextToken"> </a>

您可以 **缓存** 整个上下文令牌，或者仅缓存您的代码用于获取访问令牌的刷新令牌及其中的其他特定项目（ **位于服务器端或客户端缓存中** ）。为简单起见，本文假定您将上下文令牌作为一个单元进行缓存。
  
    
    

> [!重要信息]
> 我们想再次提醒您，因为这一点的确非常重要：请勿对 *访问*  令牌使用客户端缓存。安全的做法是仅将其用于上下文令牌。
  
    
    

您的 [服务器端缓存选项](#CacheAccessToken)与上面为访问令牌列出的选项相同。HTML 页面上的客户端选项包括 Cookie 和隐藏的表单字段。另一个选项是将上下文令牌存储在会话缓存中，但是将从其内部获取的 **CacheKey** 缓存在客户端上。
  
    
    
如果您的应用程序在会话结束后访问 SharePoint，那么会话缓存和客户端缓存都不可选择，因为如果在执行会话后工作时原始访问令牌已过期，刷新令牌必须对应用程序可用。在这种情况下，您需要由多个用户和/或 SharePoint 域和/或应用程序共享的持久性（跨会话）缓存。因此，您的代码必须使用可区分用户、SharePoint 域和/或应用程序的缓存密钥，如上面的 [缓存访问令牌](#CacheAccessToken)中所述。您可以将上下文令牌内部的 **特殊缓存密钥** 用于此目的，就像对访问令牌一样。（请参阅下面的 [了解缓存密钥](#CacheKey)。） 
  
    
    

#### 了解缓存密钥
<a name="CacheKey"> </a>

 **缓存密钥是不透明字符串，对用户、用户名颁发者、外接程序和 SharePoint 服务器场或 SharePoint Online 租户组合具有唯一性。** 在加密之前，其格式如下，其中 _Realm_ 是本地 SharePoint 服务器场或 SharePoint Online 租户的 GUID。
  
    
    
 _UserNameId_ + "," + _UserNameIdIssuer_ + "," + _ApplicationId_ + "," + _Realm_
  
    
    
缓存密钥不包含网站 URL 信息。每个 SharePoint Online 租户（或内部部署 SharePoint 服务器场）具有唯一的域，因此缓存密钥对每个用户名、用户名颁发者、应用程序和服务器场组合具有唯一性。在下面的示例上下文令牌中，加密的缓存密钥值为：
  
    
    
 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`
  
    
    
由于您的应用程序可能会缓存多个项目，例如在具有相同缓存密钥的同一缓存中缓存访问令牌和上下文令牌， **请考虑将缓存密钥作为词干** ，并根据需要附加或预置特定的字符串，例如"AccessToken"或"ContextToken"，以形成完整的缓存密钥。 **另一个选项是为您想缓存的多项信息创建具有相应属性的类** ，然后缓存该类型的对象。如果您 **使用数据库** 作为缓存，则还有 **第三个选项** ，即将表与 CacheKey 列和其他列一起用于缓存项目（AccessToken、ContextToken 等）。
  
    
    
当然，您的应用程序不一定要为其缓存的所有内容使用相同缓存。常用模式是在会话缓存中缓存访问令牌，在数据库中缓存上下文令牌（或其内部的刷新令牌），在 Cookie 中缓存 CacheKey。
  
    
    

### 使用上下文令牌获取访问令牌
<a name="UseContextTokenToGetAccessToken"> </a>

 **为获取访问令牌，您的应用程序直接向 ACS 发送请求。** 请求包括从上下文令牌提取的三项信息（及其他信息）：
  
    
    

- 刷新令牌
    
  
- SharePoint 的应用程序主体 GUID。
    
  
- 外接程序希望访问的 SharePoint 服务器场或 SharePoint Online 租户的域 GUID。
    
  
TokenHelper.cs（或 .vb）文件具有创建此请求的代码。有关执行此操作的 PHP 代码示例，请参阅  [SharePoint 2013：从 PHP 网站对 SharePoint 文档库执行操作](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454)。
  
    
    
除了从上下文令牌解析域之外，应用程序也可以获取 SharePoint 租户或服务器场的域。如果您使用托管代码，则有一种  `TokenHelper.GetRealmFromTargetUrl` 方法获取域。请务必缓存值，确保您的代码不会发出另一个网络调用重复获取域。
  
    
    

### 获取新的上下文令牌
<a name="GetNewContextToken"> </a>

 **如果您需要新的上下文令牌** （通常是因为包含在上下文令牌中的刷新令牌已过期）， **您的应用程序可以获取一个新的，方法是在每个 SharePoint 网站将浏览器重定向到一个特殊的页面** -- AppRedirect.aspx。必须将两个查询参数附加到此页面的 URL：
  
    
    

-  `client_id`：SharePoint 外接程序的客户端 ID。
    
  
-  `redirect_uri`：获取新的上下文令牌后您希望浏览器重定向到的 URI。SharePoint 会将上下文令牌发布到此 URI。通常情况下，这是请求新的上下文令牌的相同页面、控制器方法或 Web 服务方法。该值必须为 URL 编码。
    
  
下面显示了 URL 的结构：
  
    
    



```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>
```

下面是在使用 TokenHelper 文件的 ASP.NET 中发出请求的示例：
  
    
    



```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));
```


### 参阅上下文令牌的示例
<a name="ExampleContextToken"> </a>

下面是一个上下文令牌示例。顶部的小型 JavaScript 对象表示法 (JSON) 对象包含关于令牌的元数据。这些属性与访问令牌中的属性相同（如上文所述）。 **alg** 属性的值为用于生成 ACS 附加到令牌的签名的算法名称。有关令牌有效负载中属性的详细信息，请参阅表 3。请注意，所有值都必须为小写。（已添加空格以确保可读性。）
  
    
    

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

 **aud** 、 **iss** 、 **nbf** 和 **exp** 声明与访问令牌中的声明完全相同，如上文所述。 **appctxsender** 、 **appctx** 、 **CacheKey** 、 **SecurityTokenServiceUri** 、 **refreshtoken** 和 **isbrowserhostedapp** 声明如下表中所述。
  
    
    

**表 3. 上下文令牌声明和信息**


|****声明****|****说明****|****示例上下文令牌中相应的值****|
|:-----|:-----|:-----|
|aud  <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|iss  <br/> ||00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|nbf  <br/> ||1335822895  <br/> |
|exp  <br/> ||1335866095  <br/> |
|appctxsender  <br/> |"application context sender"的简写。它表示将上下文令牌发送到 SharePoint 外接程序的应用程序。  <br/> 其格式为  _GUID of principal_@ _SharePoint realm_，其中  _GUID of principal_ 为应用程序主体的常量，即 SharePoint、Exchange 2013、Lync 2013 或工作流。 <br/> |00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|appctx  <br/> |"add-in context"的简写。它是包含 **CacheKey** 和 **SecurityTokenServiceURI** 的 JSON 对象。 <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"  <br/> |
|CacheKey  <br/> |可在任何键/值结构化缓存中用作键存储和检索上下文令牌的唯一值。它还可以在数据库行中用作键列的值。  <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=  <br/> |
|SecurityTokenServiceURI  <br/> |令牌颁发服务的 URI。  <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2  <br/> |
|refreshtoken  <br/> |外接程序的刷新令牌。  <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6…  <br/> |
|isbrowserhostedapp  <br/> |**Boolean** 字段指定对包含上下文令牌的外接程序的请求是来自浏览器 (true) 还是来自远程事件接收器 (false)。 <br/> |True  <br/> |
   

### 使用上下文令牌将访问权限限制为仅 SharePoint 用户
<a name="UseContextTokenToLimitAccess"> </a>

如果您想将对远程组件（例如 WCF 服务）的访问限制到 SharePoint 用户，您的代码只需验证 HTTP 请求中的上下文令牌。（如果您使用托管代码，您可以仅调用 **TokenHelper.ReadAndValidateContextToken()**。）如果想要确保它是 Lync 2013（例如，不是 SharePoint、Exchange 2013 或工作流），则您可以验证令牌的主角声明是否以 00000003-0000-0ff1-ce00-000000000000 开头。如果您想执行需要回调 SharePoint 的其他验证操作，例如将访问限制到在 SharePoint 中担任特定角色的用户），请记住，您已为特定用户执行此验证（通过使用上下文令牌的 **CacheKey**），因此您仅需执行一次此操作。
  
    
    

## 了解刷新令牌的处理和缓存
<a name="RefreshTokens"> </a>

 **刷新令牌包含在 SharePoint 在启动后发布到 Web 应用程序的上下文令牌中。** 刷新令牌是您的 SharePoint 外接程序无法解密的加密令牌。 **在当前访问令牌过期时，您的代码使用它** 及其他信息 **获取新的访问令牌** 。它还可用于在 [上下文令牌流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中获取 *有限*  访问令牌。（在本文编写时，ACS 颁发的 SharePoint 刷新令牌的有效期为 6 小时，这可能会变更。）
  
    
    
访问令牌可持续数小时（当前为 12 小时），且最终用户每次从 SharePoint 启动 SharePoint 外接程序时均会获取一个新的访问令牌，因此在下列情况之一，您只需要刷新令牌：
  
    
    

- 用户在外接程序中具有运行时间较长的会话，在这些会话中，外接程序在启动后调用 SharePoint 数小时（当前超过 12 小时）。
    
  
- 外接程序的设计允许用户将外接程序计划为在会话结束后偶尔访问 SharePoint。
    
  
两种 **方案均要求您的外接程序缓存刷新令牌** ，第二个方案要求服务器端缓存在会话之间具有持久性。您的缓存选项与 [访问令牌](#CacheAccessToken)的相同，在上下文令牌流中，您可以使用 [上下文令牌中的缓存密钥](#CacheKey)。（在上下文令牌流中，您通常只缓存上下文令牌。它包含您获取新的访问令牌所需的刷新令牌和其他信息。在 [授权代码流](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)中，没有上下文令牌，因此应缓存刷新令牌本身。）
  
    
    
如果您在跨外接程序的特定用户会话存在的存储中缓存刷新令牌，请务必将其替换为最新的刷新令牌。在云托管的流和授权代码流中，用户在每次启动外接程序时获取新的刷新令牌。
  
    
    
如果刷新令牌过期，向 ACS 发出新的访问令牌请求将导致 **401 Unauthorized** 错误。您的外接程序应该获取新的刷新令牌并使用它获取新的访问令牌，对此错误做出响应。（由于刷新令牌已加密，您的代码无法在使用之前检查其过期日期。）在上下文令牌流中，您的外接程序通过 [获取新的上下文令牌](#GetNewContextToken)来获取新的刷新令牌。在授权代码流中，您的外接程序通过重新启动流来获取新的刷新令牌。尤其需要注意的是，您的代码应该将用户重定向到 SharePoint OAuthAuthorize.aspx 页面，对此错误做出响应，如 [了解实时请求外接程序权限的 OAuth 流](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow)中所述。
  
    
    

## 了解授权代码的处理
<a name="Authcodes"> </a>

 **在授权代码流中，当请求 SharePoint 时，授权过程从 ACS 颁发的授权代码开始，然后 SharePoint 将其作为查询参数传递到远程应用程序** 。授权代码对每个用户和远程应用程序对均具有唯一性。（在本文编写时，ACS 颁发的 SharePoint 授权代码的有效期为 5 分钟，这可能会变更。应用程序中的逻辑 **必须从查询参数获取授权代码，并将其用于向 ACS 颁发的访问令牌请求中** 。如果您使用托管代码，创建令牌的示例代码请参见 TokenHelper.cs（和 .vb）文件。读取查询参数的示例代码请参见 [获取访问 SharePoint 的页面的后台示例代码](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default)。ACS 在颁发访问令牌后立即将授权代码设置为无效，因此只能使用一次，且缓存它没有任何意义。
  
    
    

## 使用 JWT 时间值
<a name="JWTtimes"> </a>

 **nbf** 和 **exp** 声明采用 [JWT 规范](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl)指定的格式。它们写入为自 1970 年 1 月 1 日以来的秒数。在 C# 中，您可以转换以下代码（ _jWTTimeStamp_ 是令牌中的值，如 1335822895）中的这些值。
  
    
    

```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);

```


## 对令牌处理进行故障排除
<a name="Troubleshooting"> </a>

免费的  [Fiddler 工具](http://www.telerik.com/fiddler)可用于捕获您的外接程序的远程组件发送到 SharePoint 的 HTTP 请求。有一个 [工具的免费扩展](https://github.com/andrewconnell/SPOAuthFiddlerExt)，可对请求中的令牌自动解码。
  
    
    

## 其他资源
<a name="bk_addresources"> </a>


-  [创建使用低信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
- 有关使用托管代码和 TokenHelper 的代码示例，请参阅  [SharePoint 2013：使用 CSOM 的 Hello World 的远程外接程序](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf)和  [SharePoint 外接程序示例包](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
- 有关从 PHP 外接程序使用 REST 调用的代码示例，请参阅  [SharePoint 2013：从 PHP 网站对 SharePoint 文档库执行操作](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)
    
  

