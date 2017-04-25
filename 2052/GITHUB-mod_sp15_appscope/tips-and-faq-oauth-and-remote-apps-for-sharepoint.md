

# 提示和常见问题：OAuth 和远程 SharePoint 相关应用程序
本主题已过时。请参阅下面的链接查找内容目前所在的位置。
 * **适用范围：*** 





||
|:-----|
|**本文内容**          [检索应用程序信息](#AppInfo)           [AppManifest.xml 文件](#Manifest)           [Web.config 文件](#Webconfig)           [URL 和安全套接字层 (SSL)](#URL)           [应用程序重定向页面](#Redirect)           [重定向 URI](#RedirectURI)           [OAuth 令牌](#Tokens)           [权限和权限请求作用域](#Perm)           [OAuth 授权策略类型](#Policy)           [调试](#Debugging)           [高度信任的本地应用程序（服务器对服务器应用程序）](#S2S)           [其他 OAuth 相关问题](#Misc)           [社会功能](#Social)           [其他资源](#AR)|
 

## 检索应用程序信息
<a name="AppInfo"> </a>

此内容已移至 [检索外接程序注册信息和外接程序主体信息](register-sharepoint-add-ins-2013.md#Retrieve)。




## AppManifest.xml 文件
<a name="Manifest"> </a>

此内容已移至：




-  [了解 SharePoint 外接程序的应用程序清单结构和包](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)


-  [SharePoint 相关应用程序清单的架构参考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)



### <StartPage> 元素中的 URL 是什么？

此内容已移至：








>  [了解 SharePoint 外接程序的应用程序清单结构和包](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)






>  [StartPage 元素](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)



### AppManifest.xml 文件的元素和属性是什么？

此内容已移至  [SharePoint 相关应用程序清单的架构参考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)




## Web.config 文件
<a name="Webconfig"> </a>

此内容已移至 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)，尤其是 [将注册值输入到 web.config 和 AppManifest.xml 文件中](register-sharepoint-add-ins-2013.md#EditConfigFiles)一节。




### Web.config 文件的应用程序设置是什么？

此内容已移至 [将注册值输入到 web.config 和 AppManifest.xml 文件中](register-sharepoint-add-ins-2013.md#EditConfigFiles)。




## URL 和安全套接字层 (SSL)
<a name="URL"> </a>

此内容已移至：




-  [高信任应用程序中与 SSL 和域相关的错误](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [ACS 应用程序中与 SSL 和域相关的错误](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### 我的 Web 应用程序向 SharePoint 回话时存在问题。我应该检查什么？

此内容已移至：




-  [高信任应用程序中与 SSL 和域相关的错误](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [ACS 应用程序中与 SSL 和域相关的错误](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### 我应该将哪些 URL 硬编码到应用程序中以指向我的云服务器？

此内容已移至：




-  [高信任应用程序中与 SSL 和域相关的错误](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [ACS 应用程序中与 SSL 和域相关的错误](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### 我是应该注册 CNAME 别名还是应该注册托管应用程序的实际基本 URL？

此内容已移至：




-  [高信任应用程序中与 SSL 和域相关的错误](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [ACS 应用程序中与 SSL 和域相关的错误](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### 我收到"基本连接已关闭：无法为 SSL/TLS 安全通道建立信任关系"错误。我应该怎样做？

此内容已移至 [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed)。




## 应用程序重定向页面
<a name="Redirect"> </a>

此内容已移至 [获取新的上下文令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。




### 为什么应该使用应用程序重定向页面？

此内容已移至 [获取新的上下文令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。




### 我如何使用应用程序重定向页面获取上下文令牌？

此内容已移至 [获取新的上下文令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。




### 如何在 URL 中使用应用程序重定向页面？

此内容已移至 [获取新的上下文令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。




## 重定向 URI
<a name="RedirectURI"> </a>

此内容已移至 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)。




### 是否需要重定向 URI？

此内容已移至 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)。




## OAuth 令牌
<a name="Tokens"> </a>

此内容已移至：




-  [在提供程序托管的低信任 SharePoint 外接程序中处理安全令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)


-  [在提供程序托管的高信任 SharePoint 外接程序中创建和使用访问令牌](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)



### 上下文令牌是什么？

此内容已移至 [了解上下文令牌的结构和处理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens)。




### 访问令牌是什么？

此内容已移至 [了解访问令牌的处理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)。




### 刷新令牌是什么？

此内容已移至 [了解刷新令牌的处理和缓存](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)。




### 是否应该将上下文令牌字符串存储到 Cookie 中，以便可以直接从提供程序承载的应用程序对其他页面请求使用该上下文令牌？

此内容已移至 [缓存上下文令牌或其中的一部分](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken)。




### 缓存密钥值由什么组成？它是如何具有唯一性的？

此内容已移至 [了解缓存密钥](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey)。




### 如何检索上下文令牌？

此内容已移至 [了解上下文令牌的结构和处理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens)。




### 上下文令牌包含什么信息？

此内容已移至 [参阅上下文令牌的示例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken)。




### 访问令牌包含什么信息？

此内容已移至：




-  [查看 ACS 授权系统中的访问令牌示例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)


-  [查看高信任授权系统中的访问令牌示例](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)



### 如何根据 nbf 和 exp 的值计算确切的时间和日期？

此内容已移至 [使用 JWT 时间值](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes)。




### 我想要保护我的应用程序中的 svc，不让非 SharePoint 用户访问。我在应用程序入口点（通过创建 ClientContext）检查了用户的合法性，但是任何人都可以调用我的 WCF 服务。我是否应该在每次调用 svc 方法时根据上下文令牌创建 ClientContext？

此内容已移至 [使用上下文令牌将访问权限限制为仅 SharePoint 用户](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess)。




### 是否可以在页面上将 AppContext（获取自 SharePoint POST 请求）保留为隐藏的输入字段？

此内容已移至 [缓存上下文令牌或其中的一部分](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken)。




### 刷新令牌有效期为多长时间？

此内容已移至 [了解刷新令牌的处理和缓存](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)。




### 我在 Cookie 中存储访问令牌和主机 URL，以便可以对其他页面请求使用它们。但是，用户休息了一会儿，而访问令牌过期了。我应该做什么？

此内容已移至：




-  [了解访问令牌的处理](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)


-  [了解刷新令牌的处理和缓存](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)



### 在哪种情况下应该放弃仍然有效的未到期的旧刷新令牌并使用新刷新令牌？

此内容已移至 [了解刷新令牌的处理和缓存](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)。




## 权限和权限请求作用域
<a name="Perm"> </a>

此内容现在位于  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)中。




### 列表、库内容以及其他功能的权限请求作用域和可用权限是什么？

此内容现在位于  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)中。




## OAuth 授权策略类型
<a name="Policy"> </a>

此内容现在位于 [SharePoint 2013 中的外接程序授权策略类型](add-in-authorization-policy-types-in-sharepoint-2013.md)中。




### 纯应用程序策略与用户 + 应用程序策略之间的区别是什么？

此内容现在位于 [SharePoint 2013 中的外接程序授权策略类型](add-in-authorization-policy-types-in-sharepoint-2013.md)中。




### 是否存在授予或拒绝应用程序启动权限的方法？

此内容现在位于  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)中。




## 调试
<a name="Debugging"> </a>

此内容已移至：




-  [对高信任的 SharePoint 外接程序进行故障排除](troubleshooting-high-trust-sharepoint-add-ins.md)


-  [对低信任的 SharePoint 2013 外接程序进行故障排除](44ead3b9-75c3-4f68-b908-0af6197f1143.md)



### 使用 Fiddler

此内容已移至：




-  [对高信任的 SharePoint 外接程序进行故障排除](troubleshooting-high-trust-sharepoint-add-ins.md)


-  [对低信任的 SharePoint 2013 外接程序进行故障排除](44ead3b9-75c3-4f68-b908-0af6197f1143.md)



## 高度信任的本地应用程序（服务器对服务器应用程序）
<a name="S2S"> </a>

此内容已移至 [对高信任的 SharePoint 外接程序进行故障排除](troubleshooting-high-trust-sharepoint-add-ins.md)。




### 我在运行高度信任的应用程序时收到 401 未授权错误。我应该怎么办？

此内容已移至 [对高信任的 SharePoint 外接程序进行故障排除](troubleshooting-high-trust-sharepoint-add-ins.md)。




### 如何获取高度信任的应用程序的上下文令牌？

此内容已移至 [在提供程序托管的高信任 SharePoint 外接程序中创建和使用访问令牌](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)。




## 其他 OAuth 相关问题
<a name="Misc"> </a>

此内容已移至：




-  [对高信任的 SharePoint 外接程序进行故障排除](troubleshooting-high-trust-sharepoint-add-ins.md)


-  [对低信任的 SharePoint 2013 外接程序进行故障排除](44ead3b9-75c3-4f68-b908-0af6197f1143.md)



### 尝试使用 HTTP DAV 方法读取文件时，我收到了一个错误。我应该怎么办？

此内容已移至 [对低信任的 SharePoint 2013 外接程序进行故障排除](44ead3b9-75c3-4f68-b908-0af6197f1143.md)。




### 是否存在一种将 OAuth 授权转给不同域中的其他组件的方法，或者是否存在为多个 URI 配置 OAuth 的方法？

是。请参阅 [将访问令牌转发到后端系统](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend)。




### SharePoint 2013 主体值是否恒定？

是。请参阅 [查看 ACS 授权系统中的访问令牌示例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)（尤其是表 1）。




### 应用程序 ID 和应用程序密钥在给定应用程序的所有租户中是否恒定？

是，如果应用程序已在卖家面板中注册；但是，如果它与每个 SharePoint 租户或服务器场分开注册，则可能分别具有不同的 ID 和密钥。这种情况下，看起来似乎是 Microsoft Azure 访问控制服务 (ACS) 的多个应用程序。请参阅 [注册 SharePoint 2013 外接程序](register-sharepoint-add-ins-2013.md)。




### 领域是否具有唯一性？

是。请参阅 [查看 ACS 授权系统中的访问令牌示例](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)（尤其是表 1）和 [使用上下文令牌获取访问令牌](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken)。




### 如何在开发过程中针对 OAuth 禁用 HTTPS 要求？

此内容已移至 [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement)。




## 社会功能
<a name="Social"> </a>

此内容已移至：




1.  [SharePoint 2013 中的社交和协作功能](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)


2.  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)



### 如何检索用户的标识和属性？

请参阅  [在 SharePoint 2013 中获取用户标识和属性](get-user-identity-and-properties-in-sharepoint-2013.md)。




### 不同的社会功能和权限请求作用域的用途是什么？

此内容现在位于 [使用 SharePoint 2013 中的社交功能开始进行开发](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms)中。




### 如何获取关注我的人员的用户配置文件属性？

此内容已移至 [如何：使用 SharePoint 2013 中的 .NET 客户端对象模型检索用户配置文件属性](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp)。


    