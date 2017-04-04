---
title: URL 字串及權杖中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# URL 字串及權杖中 SharePoint 增益集
了解哪些 URL token 都可供使用的SharePoint Add-ins。
> [!重要]
> 如需在SharePoint 2013和語彙基元這些 Url 中使用建構 Url 的一般資訊，請參閱 [Url 及 SharePoint 2013 中的權杖](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)。本主題說明SharePoint Add-ins中可用的 token。
  
    
    


## 
<a name="URLtokens"> </a>

SharePoint 2013支援用於SharePoint Add-ins下表中所列的 token。
  
    
    
本節的表格中的 token 可用在 Url 中各種不同的情況中開發SharePoint Add-ins，例如自訂動作及自訂頁面上的連結。在某些內容，這些語彙基元的一些無法使用。語彙基元的限制的清單可使用的最重要位置的其中三項是 [開始] 頁面上的增益集、 主機網頁、 自訂動作和增益集組件的 [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) 屬性。這些個別的資料行中呼叫 * **但這三種不的 places 權杖可使用的詳盡清單。*** 
  
    
    
[ **首頁** ] 欄中指定權杖可用之增益集資訊清單的 **StartPage**元素內。 **自訂動作** ] 欄會指定權杖是否可用於主機網頁上的自訂動作的 URL。[ **增益集組件** ] 欄中指定權杖可用增益集組件的 [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) 屬性中。
  
    
    

**可用的SharePoint Add-in的 URL 開頭的權杖**


|**Token**|**解析為**|**StartPage**|**自訂動作**|**增益集組件**|**備註**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl <br/> |SharePoint Add-in的增益集網頁的 URL。 <br/> |是 <br/> |是 <br/> |是 <br/> |僅外部增益集 web 應使用此 token。增益集 web 本身內使用 **~site**的增益集網站 url。 <br/> |
|~controlTemplates <br/> |ControlTemplates 虛擬資料夾目前網站的 URL。 <br/> |否 <br/> |否 <br/> |否 <br/> ||
|~hostUrl <br/> |主機網頁的 URL。 <br/> |否 <br/> |否 <br/> |是 <br/> ||
|~hostLogoUrl <br/> |標誌的主機網站的 URL。 <br/> |否 <br/> | [Microsoft InfoPath](http://msdn.microsoft.com/library/142d5d73-fac4-45a1-b742-846953943813.aspx#bkmk_InfoPath) <br/> |否 <br/> ||
|~ 版面配置 <br/> |目前的網站的版面配置虛擬資料夾的 URL。 <br/> |否 <br/> |否 <br/> |否 <br/> ||
|~remoteAppUrl <br/> |SharePoint Add-in的遠端 web 應用程式的 URL。 <br/> |是 <br/> |是，在主機 web，但不是在增益集 web。 <br/> |是 <br/> |如果您不使用Microsoft Office Developer Tools for Visual Studio開發您 SharePoint 的增益集，則無法使用 **~remoteAppUrl** **StartPage** URL 中。不過，當您使用Visual Studio及工具，您可以使用這個語彙基元的所有主控提供者增益集及 Visual Studio 增益集的封裝時解析。在這種用法，它是真正超過Visual Studio語彙基元的 SharePoint token。因此可以外的增益集資訊清單，即使您不使用Microsoft Office Developer Tools for Visual Studio。 <br/> |
|~ 網站 <br/> |目前網站的 URL。 <br/> |否 <br/> |否 <br/> |是 <br/> ||
|~ 網站集合 <br/> |父網站集合目前網站的 URL。 <br/> |否 <br/> |否 <br/> |是 <br/> ||
   
除了其中指定否則任一個語彙基元中下一個表格可用增益集組件的 [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) 屬性值的 *路徑*  部分。[ **增益集組件** ] 欄參照其使用值的 *查詢字串*  部分。
  
    
    

**可以使用內部 URL 的權杖**


|**Token**|**解析為**|**StartPage**|**自訂動作**|**增益集組件**|**備註**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken} <br/> |增益集的 OAuth 內容權杖。 <br/> |否 <br/> |否 <br/> |否 <br/> ||
|{AppWebUrl} <br/> |在SharePoint Add-in增益集網頁的 URL。 <br/> |是 <br/> |是 <br/> |是 <br/> |僅外部增益集 web 應使用此 token。增益集 web 本身內使用 **{Site}**的增益集網站 url。 <br/> |
|{ClientTag} <br/> |用戶端快取控制項數目 (用戶端標記) 的目前的網站。 <br/> |是 <br/> |是 <br/> |是 <br/> ||
|{HostLogoUrl} <br/> |SharePoint Add-in主機網頁的標誌。 <br/> |是 <br/> |是 <br/> |是 <br/> ||
|{HostTitle} <br/> |SharePoint Add-in主機網頁的標題。 <br/> |是 <br/> |是 <br/> |是 <br/> ||
|{HostUrl} <br/> |SharePoint Add-in主機網頁的 URL。 <br/> |是 <br/> |是 <br/> |是 <br/> ||
|{ItemId} <br/> |清單或文件庫 (整數) 中的項目識別碼。 <br/> |否 <br/> |是 <br/> |否 <br/> ||
|{ItemUrl} <br/> |正在起的項目 URL。 <br/> |否 <br/> |是 <br/> |否 <br/> ||
|語言 <br/> |目前語言/文化特性的SharePoint Add-in的主機網站。 <br/> |是 <br/> |是 <br/> |是 <br/> ||
|{ListId} <br/> |目前的清單 (GUID) 的識別碼。 <br/> |否 <br/> |是 <br/> |否 <br/> ||
|{ProductNumber} <br/> |完整版本的組建編號 SharePoint 伺服器陣列。 <br/> |是 <br/> |是 <br/> |是 <br/> |範例值為"15.0.4433.1011"。 <br/> |
|{RecurrenceId} <br/> |循環週期性事件的索引。 <br/> |否 <br/> |是 <br/> |否 <br/> |使用清單項目快顯功能表中不支援此 token。 <br/> |
|{RemoteAppUrl} <br/> |SharePoint Add-in的遠端 web 應用程式的 URL。 <br/> |是 <br/> |是 <br/> |是 <br/> ||
|網站 <br/> |目前網站的 URL。 <br/> |否 <br/> |是 <br/> |是 <br/> ||
|{SiteCollection} <br/> |目前網站的上層網站的 URL。 <br/> |否 <br/> |是 <br/> |是 <br/> ||
|{SiteUrl} <br/> |目前網站的 URL。 <br/> |否 <br/> |是 <br/> |否 <br/> ||
|Source <br/> |HTTP 要求的 URL。 <br/> |否 <br/> |是 <br/> |否 <br/> ||
|{StandardTokens} <br/> |請參閱 「 備註 」。 <br/> |是 <br/> |是 <br/> |是 <br/> |這將結合五個其他權杖。一開始會解析為 `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}`。然後這每一項語彙基元解析。如果沒有任何增益集網頁，部分 `&amp;SPAppWebUrl={AppWebUrl}`不存在。 <br/> |
   

## 其他資源
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [Advanced Extranet Support](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)
    
  
-  [Getting References to Sites, Web Applications, and other Key Objects](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)
    
  
-  [Working with List Objects and Collections](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)
    
  
-  [Sample Object Model Tasks](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)
    
  

