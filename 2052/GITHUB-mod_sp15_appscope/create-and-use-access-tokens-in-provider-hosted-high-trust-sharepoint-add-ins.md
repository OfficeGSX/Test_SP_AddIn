---
title: 在提供程序托管的高信任 SharePoint 外接程序中创建和使用访问令牌
ms.prod: SHAREPOINT
ms.assetid: cb3264d4-41a6-498f-a408-75b077566051
---


# 在提供程序托管的高信任 SharePoint 外接程序中创建和使用访问令牌
了解访问令牌在高信任 SharePoint 外接程序中的角色以及您的代码如何使用这些令牌。
> **重要信息**
> **本文内容完全与访问令牌在高信任授权系统（而不是 ACS 系统）中的使用相关。** 有关在 ACS 系统中使用安全令牌的信息，请参阅 [在提供程序托管的低信任 SharePoint 外接程序中处理安全令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)。 




 **使用 [高信任授权系统](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)获取 SharePoint 的访问权限的 SharePoint 外接程序必须在每个创建、读取、更新或删除 (CRUD) 请求中，将访问令牌** （以 [JSON Web 令牌](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)格式）传递到 SharePoint。 SharePoint 验证令牌并会请求提供服务。本文提供关于代码如何创建和传递访问令牌的信息。
在高信任授权系统中， **SharePoint 外接程序的远程组件可创建访问令牌** 。如果远程组件对服务器端代码使用托管代码，创建令牌的大部分编码工作将在包含在 Visual Studio Office 开发人员工具 的 SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb）文件中为您执行。高信任 SharePoint 外接程序客户具有本地 SharePoint，因此他们可能不愿意使用 ASP.NET、IIS 和 Windows Sever 作为远程组件的托管堆栈。您应该考虑使用此堆栈，因为生成的两个文件将为您节省大量的编码和测试时间。如果远程组件必须使用非 .NET 语言，且远程组件和 SharePoint 服务器场已连接到 Internet，您应考虑使用 [低信任授权系统](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)，而非高信任。在 Microsoft Azure 访问控制服务 (ACS) 系统中，所有令牌构建均由 ACS 完成，因此也可以为您节省大量劳力。本文其余部分的主要目的是为开发人员提供创建带有非 .NET 远程组件的 SharePoint 外接程序以及使用高信任授权系统的指导。本文还提供了关于调试使用高信任系统的基于 .NET 的 SharePoint 外接程序的有用信息。





## 了解访问令牌的处理
<a name="AccessTokens"> </a>


> **注释**
>  请记住，在阅读本文时，尤其是有关您的代码必须执行的任务部分，如果您使用的是托管代码，Visual Studio Microsoft Office 开发人员工具 会向每个 SharePoint 外接程序项目添加生成的两个代码文件，即 SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb），这两个文件将为您执行大部分任务。您的应用程序的令牌处理代码通常仅由对这两个文件中类的一些调用组成。本主题中的详细信息旨在帮助不使用托管代码的开发人员（以及正在对令牌进行故障排除的人员）。>  很多语言和平台的 OAuth 库的链接位于以下位置：>  [OAuth 2.0](http://oauth.net/2/) 滚动到"客户端库"。>  您可以通过在 [github](https://github.com/) 中搜索"OAuth 2"和"JSON web token"（不含引号）来查找更多信息。




 **您的代码需要执行的主要任务** 如下：




1. **创建访问令牌。** 根据远程 Web 应用程序是对 SharePoint 发出仅外接程序调用还是用户+外接程序调用还是同时发出两种调用，创建此令牌的子任务可能有所不同。（有关两种调用的详细信息，请参阅  [SharePoint 2013 中的外接程序授权策略类型](add-in-authorization-policy-types-in-sharepoint-2013.md)。）

    如果外接程序发出用户+外接程序调用，则创建访问令牌包含以下子任务：

1. 创建参与者令牌，用于标识 SharePoint 外接程序并通知 SharePoint 将用户身份验证和授权委派给您的外接程序，并使用 Base 64 编码对其进行编码。有关参与者令牌的声明和结构的详细信息，请参见下面的表 2。有关对令牌进行编码和签名的详细信息，请参阅下面的 [对令牌进行编码和签名](#EncodingTokens)。


2. 使用 x509 证书中的凭据对参与者令牌进行签名，SharePoint 服务器场管理员已将 SharePoint 配置为信任此证书。


3. 将参与者令牌包含在访问令牌中。


4. 将其他必要声明添加到访问令牌。有关令牌的声明和结构的详细信息，请参见下面的表 1。


5. 使用 Base 64 对访问令牌进行编码。有关对令牌进行编码和签名的详细信息，请参阅下面的 [对令牌进行编码和签名](#EncodingTokens)。



    如果外接程序发出仅外接程序调用，则您的代码仅需执行前两项子任务。参与者令牌充当访问令牌。

    如果外接程序发出一些用户+外接程序调用和一些仅外接程序调用，它必须为仅外接程序调用创建一个简单的参与者令牌，为用户+外接程序调用创建一个较大的嵌套式访问令牌。同一个访问令牌不能同时用于两者。


2. **在对 SharePoint的每个 HTTP 请求中包含访问令牌。** 令牌添加为请求的 **Authorization** 标头。标头的值为"Bearer"，后跟空格，然后是 Base 64 编码的基本访问令牌。


3. （可选） **缓存访问令牌** ，以便在后续请求中重复使用。


这些任务必须使用服务器端代码执行。如果您使用托管代码，某些任务的示例代码请参见 Visual Studio Microsoft Office 开发人员工具 生成的 SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb）文件。




### 缓存访问令牌
<a name="CacheAccessToken"> </a>

创建令牌后，它可以在对 SharePoint 的后续调用中重复使用，直至其过期。根据您的远程组件的体系结构和承载平台，有多种方法 **可以在服务器上缓存访问令牌** 。




- 在会话状态中


- 在应用程序状态中


- 在  [Windows Server AppFabric 缓存](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx)中。


- 在数据库中


- 在  [memcached](http://www.memcached.org/) 系统中


如果缓存存储由不同的用户会话共享（例如应用程序缓存），则务必使用对会话唯一的缓存密钥。如果缓存由多个应用程序共享，则代码还必须为该变量 **对缓存密钥进行相对化处理** 。您的外接程序还可能访问不同的 SharePoint 场。您需要为每个场提供不同的访问令牌，因此在这种情况下，您的缓存密钥需要针对场进行相对化处理。总而言之，您可能需要基于一个或多个用户 ID、应用程序 ID 和 SharePoint 域或领域的缓存密钥。考虑使用与利用低信任授权系统的 SharePoint 外接程序所使用的类似的缓存密钥系统。有关详细信息，请参阅 [了解缓存密钥](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey)。从根本上来说，您需将这三个 ID 中的一个或多个串联起来（可选，对结果执行哈希以形成一个较短的字符串）作为缓存密钥。




### 处理过期的访问令牌
<a name="CacheAccessToken"> </a>

 **访问令牌有过期时间** ，您的代码可将其设置为您需要的任何值。如果您的外接程序为每个请求都构建了一个新的访问令牌，则每个令牌的有效期仅需足够它通过 SharePoint 验证，甚至连多几秒都不需要，除非客户的 LAN 经常堵塞。以后您可以将过期时间设置为数年，但即使是在为其设计高信任外接程序的"全本地部署"方案中，访问令牌也有被窃取的危险。因此，您应该考虑将过期时间设置为几小时以内。（如果您使用托管代码，TokenHelper.cs（或 .vb）文件中的示例令牌创建代码会将过期时间设置为 12 小时。）



如果用户与您的 SharePoint 外接程序的会话持续时间超过缓存访问令牌的有效期，则令牌过期后对 SharePoint 的第一个请求将导致 **401 Unauthorized** 错误。您的代码必须处理此响应。或者，代码也可以在使用之前测试访问令牌的过期时间。 **您的代码应该通过创建新的访问令牌并重复失败的请求** ，来对过期的访问令牌做出响应。




## 了解访问令牌的结构
<a name="Structure"> </a>

下面是 **高信任 SharePoint 外接程序生成的访问令牌示例** ；具体地说，该令牌由 TokenHelper.cs（或 .vb）文件中的示例代码生成，该文件是 Visual Studio 2013 Update 2 中的 Visual Studio Office 开发人员工具 创建的 SharePoint 外接程序项目模板的一部分。该令牌已进行解码，并已添加空格以提高可读性。高信任系统中使用的访问令牌符合 [[MS-SPS2SAUTH]: OAuth 2.0 Authentication Protocol: SharePoint Profile](http://msdn.microsoft.com/library/05f9759b-0fa4-45ff-bd4b-0d7d254e7010.aspx)（也称为服务器到服务器或 S2S 协议）。该信息可帮助使用托管代码的开发人员调试高信任 SharePoint 外接程序，并为使用其他语言的开发人员提供关于构建令牌的指导。



 **如果外接程序使用 [用户+外接程序策略](add-in-authorization-policy-types-in-sharepoint-2013.md)调用 SharePoint，将生成此访问令牌。** 如果外接程序使用 [仅外接程序策略](add-in-authorization-policy-types-in-sharepoint-2013.md)并对 SharePoint 进行仅外接程序调用，参与者令牌（用户+外接程序访问令牌中的子令牌，如下所述）将成为访问令牌（没有父令牌）。




> **注释**
>  请注意，您的代码创建的高信任访问令牌与使用低信任授权系统时 Azure ACS 创建的令牌有所不同：>  标头中的 **alg** 声明为"none"，因为高信任外接程序发出的用户+外接程序调用中的访问令牌尚未签署。>  在本示例中， **aud** 值中的外接程序 URL 为本地服务器，这对高信任系统来说很正常。>  不提供 **identityprovider** 声明，但提供与低信任授权系统中所用 **identityprovider** 声明访问令牌相同类型的值的 **nii**（名称标识颁发者）。（有关当标识提供程序基于 SAML 时该值的信息，请参阅 Steve Peschka 的博客文章  [SharePoint 外接程序的安全 - 第 8 部分](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)和 [在 SharePoint 2013 中将 SharePoint 外接程序与 SAML 和 FBA 网站一起使用](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)。 >  不提供 **actor** 声明，但提供包含有效期为 12 小时的 Base 64 编码的内部令牌的 **actortoken** 声明。




标头中包含两个属性。"typ"是一种令牌类型。远程 Web 应用程序中的代码应始终将该值设置为"JWT"。"alg"是用于对令牌进行签名的算法。由于高信任外接程序发出的用户+外接程序调用中的外部令牌尚未签署，请将该值设置为"none"。有关高信任访问令牌正文部分中的值的信息，请参阅表 1。






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

下表说明了 **您的代码应该将哪些属性包含在访问令牌中，以及应该为这些属性设置什么值** 。如果您使用托管代码，SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb）文件将为您创建令牌。例如，您的令牌向 **SharePointContext.CreateUserClientContextForSPHost** 方法发出单个调用。该方法又反过来调用 **TokenHelper** 类中的方法，这些方法可构建 **SharePointContext.CreateUserClientContextForSPHost** 返回的 SharePoint 客户端上下文对象发出的每个 SharePoint 调用中包含的访问令牌。




**表 1：ACS 颁发的访问令牌声明**


|**声明**|**说明**|**示例访问令牌中相应的值**|
|:-----|:-----|:-----|
| `aud` <br/> |"audience"的简写，表示令牌的所适用的主体。格式为  _audience principal ID_/ _fully qualified SharePoint domain_@ _SharePoint realm_。  <br/> SharePoint 外接程序的访问群体主体始终为 00000003-0000-0ff1-ce00-000000000000。  <br/> 由于高信任 SharePoint 外接程序通常用于全本地方案，完全限定的 SharePoint 域名通常只是服务器名称。  <br/>  _SharePoint realm_ 为访问令牌被用于访问的内部部署 SharePoint 服务器场的 GUID（或者租户的 GUID，如果为租户配置了服务器场）。 <br/> 在 SharePoint 服务器上运行 PowerShell **Get-SPAuthenticationRealm** cmdlet，查找 SharePoint 域。然后直接在您的代码上使用该域或将其存储在您的代码可以读取的配置文件中，例如 web.config 文件的 app.Settings 部分。或者，您的代码可以在运行时动态发现 SharePoint 域，方法是向 SharePoint 发送身份验证质询。有关在托管代码中如何执行此操作的示例，请参阅 TokenHelper.cs（或 .vb）文件中的 `GetRealmFromTargetUrl` 方法。 <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |"issuer"的简写。它表示创建令牌的主体。格式为  _Issuer GUID_@ _SharePoint realm GUID_。  <br/> 在高信任系统中，外接程序本身就是颁发者，因此 SharePoint 外接程序的客户端 ID 通常用作颁发者 GUID。 *颁发者 ID 中的所有字母都必须为小写。*  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |"not before"的简写，表示令牌"开始" 生效的时间，按自 1970 年 1 月 1 日以来的秒数计算。您的代码应将该值设置为令牌创建的时刻。  <br/> |1403212820  <br/> |
| `exp` <br/> |"expiration"的简写，表示令牌的有效截止时间，自 1970 年 1 月 1 日午夜起计算的秒数。  <br/> |1403256020  <br/> |
| `nameid` <br/> |为其颁发令牌的用户的唯一标识符。其格式因标识提供程序而异。在本示例中，提供程序是 Active Directory。  <br/> |s-1-5-21-2127521184-1604012920-1887927527-2963467  <br/> |
| `nii` <br/> |"name identifier issuer"的简写。通过 Internet 编号分配机构 (IANA) 注册的标识提供者的唯一名称。  <br/> 对于高信任 SharePoint 外接程序应用程序来说，通常为本地标识提供程序，例如本示例中的 Active Directory。  <br/> |urn:office:idp:activedirectory  <br/> |
| `actortoken` <br/> |Base 64 编码的 JWT 令牌，可标识 SharePoint 外接程序并告知 SharePoint 信任外接程序，不论哪个用户在运行此外接程序。  <br/> |请参见下文。  <br/> |
 
 **下面显示了解码的 **actortoken**。** 顶部的小型 JavaScript 对象表示法 (JSON) 标头对象包含关于令牌的元数据，包括令牌类型以及用于签署令牌的算法。标头的 **x5t** 属性是 x.509 证书的缩略图发出的摘要，这是令牌的正式颁发者。要构建该值，您的代码需执行下列操作：




1. 获取证书的缩略图的字节数组（不是字符串）版本。这是证书的 SHA-1 摘要。（在托管代码中，这可以使用  [GetCertHash()](http://msdn2.microsoft.com/ZH-CN/library/4f9acc3f) 方法执行。您将需要您所用语言的等效方法。）


2. 使用 Base 64 URL 编码对字节数组进行编码。


3. 将 **x5t** 属性的值设置为编码摘要。


表 2 显示了您的代码必须包含在令牌正文中的声明以及要为其设置的值。






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


> **注释**
> 如果高信任外接程序使用 [仅外接程序策略](add-in-authorization-policy-types-in-sharepoint-2013.md)并且向 SharePoint 发出仅外接程序调用，则此处所述的令牌实际上是访问令牌。不提供外部令牌。此外，也不提供 **trustedfordelegation** 声明，因为用户的权限与仅外接程序调用无关。





**表 2：证书颁发的参与者令牌声明**


|**声明**|**说明**|**示例访问令牌中相应的值**|
|:-----|:-----|:-----|
| `aud` <br/> |与父访问令牌相同。  <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |这与在父访问令牌中的含义相同，但是颁发者 GUID 不是 Web 应用程序的客户端 ID。它是证书的 GUID。尽管应用程序中的代码会构建参与者令牌，证书将被视为参与者令牌的颁发者。  <br/> 请注意，本示例中的颁发者 GUID 是一个易于记住的 GUID 字符串，当服务器场管理员在 SharePoint 中将 x.509 证书注册为受信任令牌颁发者时，会使用此 GUID。当同一个证书在服务器场上的所有高信任 SharePoint 外接程序中用作参与者令牌时，这是普遍做法。管理员也可以选择为每个 SharePoint 外接程序使用不同的证书。在这种情况下，管理员应对证书使用随机生成的不同 GUID。 *颁发者 GUID 中的所有字母都必须为小写。*  <br/> |11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |与父访问令牌中的含义相同。  <br/> |与父访问令牌的值相同。  <br/> |
| `exp` <br/> |与父访问令牌中的含义相同。  <br/> |与父访问令牌的值相同。  <br/> |
| `nameid` <br/> |SharePoint 外接程序的唯一标识符，因为它是高信任系统中的"参与者"。格式为  _client_ID_@ _SharePoint_realm_ <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `trustedfordelegation` <br/> |指定 SharePoint 是否应该信任 SharePoint 外接程序来对用户进行身份验证和授权用户的布尔值。这在高信任系统中通常如此。  <br/> 请勿将该声明包含高信任系统中的仅外接程序调用中。  <br/> |True  <br/> |
 

## 对令牌进行编码和签名
<a name="EncodeTokens"> </a>

 **当您的代码将所有属性和值添加到标头和正文 JSON 对象后，必须对其进行编码，将其包含在 JWT 中并对其进行签名。** 步骤如下。




1. 使用 Base 64 URL 编码对标头进行编码。


2. 使用 Base 64 URL 编码对有效负载进行编码。


3. 将编码的正文串联在编码标头后面，两者之间有一个"."字符。这是 JWT。


4. 使用 JWT 和证书的私钥创建 SHA256 证书。


5. 使用 Base 64 URL 编码对签名进行编码。


6. 将签名附加到 JWT 结尾，两者之间有一个"."字符。


对于在仅外接程序调用中使用的参与者令牌，参与者令牌充当访问令牌。不提供外部令牌。对于用于用户+外接程序调用的访问令牌，上述步骤用于构建参与者令牌，该令牌稍后将作为 **actortoken** 属性的值插入到访问令牌的正文。然后使用上面的前三个步骤对完整访问令牌进行编码和构建，但未签名，因此其余步骤并不用于外部令牌。




## 对访问令牌进行故障排除
<a name="Trouble"> </a>

免费的  [Fiddler 工具](http://www.telerik.com/fiddler)可用于捕获您外接程序的远程组件发送到 SharePoint 的 HTTP 请求。提供一个 [工具的免费扩展](https://github.com/andrewconnell/SPOAuthFiddlerExt)，可对请求中的令牌自动解码。




## 其他资源
<a name="bk_addresources"> </a>


-  [创建使用高信任授权的 SharePoint 外接程序](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)


- Steve Peschka 的  [SharePoint 外接程序的安全 - 第 7 部分](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-7.aspx)


- Steve Peschka 的  [SharePoint 外接程序的安全 - 第 8 部分](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)


- Steve Pescka 的 [在 SharePoint 2013 中将 SharePoint 外接程序与 SAML 和 FBA 网站一起使用](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)


- Kirk Evan 的 [非 Microsoft 平台上的高信任 SharePoint 外接程序](http://blogs.msdn.com/b/kaevans/archive/2014/07/14/high-trust-sharepoint-apps-on-non-microsoft-platforms.aspx)


-  [OAuth 2.0](http://oauth.net/2/)



