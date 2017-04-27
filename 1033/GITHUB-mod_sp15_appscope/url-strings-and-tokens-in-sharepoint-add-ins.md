---
title: URL strings and tokens in SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# URL strings and tokens in SharePoint Add-ins
Learn which URL tokens are available for use in SharePoint Add-ins.
 





> **IMPORTANT**
> For general information about constructing URLs in SharePoint 2013 and the use of tokens in those URLs, see  [URLs and tokens in SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx). This topic describes the tokens that are available in SharePoint Add-ins. 





## 
<a name="URLtokens"> </a>

SharePoint 2013 supports the tokens listed in the following tables for use in SharePoint Add-ins.



The tokens in the tables of this section can be used in URLs in a wide variety of situations in development of SharePoint Add-ins, such as in Custom Actions and in links on custom pages. In some contexts, some of these tokens cannot be used. Three of the most important places where only a restricted list of tokens can be used are the start page of an add-in, a custom action on the host web, and the  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) property of an add-in part. These are called out in separate columns, * **but these three are not an exhaustive list of places where tokens can be used.*** 



The **StartPage** column specifies whether the token can be used in the **StartPage** element of an add-in manifest. The **Custom Action** column specifies whether the token can be used in the URL of a custom action on a host web. The **Add-in Part** column specifies whether the token can be used in the [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) property of the add-in part.




**Tokens that can be used at the beginning of a URL in a SharePoint Add-in**


|**Token**|**Resolves to**|**StartPage**|**Custom Action**|**Add-in Part**|**Remarks**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl  <br/> |The URL of the add-in web of a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> |This token should be used only outside an add-in web. Within the add-in web itself, use **~site** for the URL of the add-in web. <br/> |
|~controlTemplates  <br/> |The URL of the ControlTemplates virtual folder for the current website.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|~hostUrl  <br/> |The URL of the host web.  <br/> |No  <br/> |No  <br/> |Yes  <br/> ||
|~hostLogoUrl  <br/> |The URL of the logo of the host web.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|~layouts  <br/> |The URL of the Layouts virtual folder for the current website.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|~remoteAppUrl  <br/> |The URL of a remote web application in a SharePoint Add-in.  <br/> |Yes  <br/> |Yes, in the host web, but No in the add-in web.  <br/> |Yes  <br/> |If you are not using Microsoft Office Developer Tools for Visual Studio to develop your SharePoint Add-in, you cannot use **~remoteAppUrl** in the **StartPage** URL. However, when you are using Visual Studio and the tools, you can use this token for any provider-hosted add-in and it is resolved when Visual Studio packages the add-in. In this usage, it is really more of a Visual Studio token than a SharePoint token. It can be used outside the add-in manifest, even when you are not using Microsoft Office Developer Tools for Visual Studio. <br/> |
|~site  <br/> |The URL of the current website.  <br/> |No  <br/> |No  <br/> |Yes  <br/> ||
|~sitecollection  <br/> |The URL of the parent site collection of the current website.  <br/> |No  <br/> |No  <br/> |Yes  <br/> ||
 
Except where indicated otherwise, none of the tokens in the next table can be used in the  *path*  portion of the [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) property value of the add-in part. The **Add-in Part** column refers to their use in the *query string*  portion of the value.




**Tokens that can be used inside a URL**


|**Token**|**Resolves to**|**StartPage**|**Custom Action**|**Add-in Part**|**Remarks**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken}  <br/> |The OAuth context token for the add-in.  <br/> |No  <br/> |No  <br/> |No  <br/> ||
|{AppWebUrl}  <br/> |The URL of the add-in web in a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> |This token should be used only outside an add-in web. Within the add-in web itself, use **{Site}** for the URL of the add-in web. <br/> |
|{ClientTag}  <br/> |The client cache control number (client tag) for the current website.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> ||
|{HostLogoUrl}  <br/> |The logo for the host web of a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> ||
|{HostTitle}  <br/> |The title of the host web of a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> ||
|{HostUrl}  <br/> |The URL of the host web of a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> ||
|{ItemId}  <br/> |The ID of an item in a list or library (an integer).  <br/> |No  <br/> |Yes  <br/> |No  <br/> ||
|{ItemUrl}  <br/> |The URL of the item being acted upon.  <br/> |No  <br/> |Yes  <br/> |No  <br/> ||
|{Language}  <br/> |The current language/culture of the host web of a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> ||
|{ListId}  <br/> |The ID of the current list (a GUID).  <br/> |No  <br/> |Yes  <br/> |No  <br/> ||
|{ProductNumber}  <br/> |The full build version number of the SharePoint farm.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> |An example value is "15.0.4433.1011".  <br/> |
|{RecurrenceId}  <br/> |The recurrence index of a recurring event.  <br/> |No  <br/> |Yes  <br/> |No  <br/> |This token is not supported for use in the context menus of list items.  <br/> |
|{RemoteAppUrl}  <br/> |The URL of a remote web application in a SharePoint Add-in.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> ||
|{Site}  <br/> |The URL of the current website.  <br/> |No  <br/> |Yes  <br/> |Yes  <br/> ||
|{SiteCollection}  <br/> |The URL of the parent site of the current website.  <br/> |No  <br/> |Yes  <br/> |Yes  <br/> ||
|{SiteUrl}  <br/> |The URL of the current website.  <br/> |No  <br/> |Yes  <br/> |No  <br/> ||
|{Source}  <br/> |The HTTP Request URL.  <br/> |No  <br/> |Yes  <br/> |No  <br/> ||
|{StandardTokens}  <br/> |See Remarks.  <br/> |Yes  <br/> |Yes  <br/> |Yes  <br/> |This combines five other tokens. It initially resolves to  `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}`. Then each of these tokens resolves. If there is no add-in web, the portion  `&amp;SPAppWebUrl={AppWebUrl}` is not present. <br/> |
 

## Additional resources
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [Advanced Extranet Support](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)


-  [Getting References to Sites, Web Applications, and other Key Objects](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)


-  [Working with List Objects and Collections](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)


-  [Sample Object Model Tasks](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)



