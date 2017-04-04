---
title: 疑難排解高信任 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# 疑難排解高信任 SharePoint 增益集
取得某些說明與開發高信任SharePoint Add-ins的問題。
本文說明 Fiddler 工具並也會提供一些解決某些特定問題的指引。
  
    
    


## 使用 Fiddler 工具

免費的 [Fiddler 工具](http://www.telerik.com/fiddler)可用來擷取遠端元件的增益集所傳送至SharePoint的 HTTP 要求。沒有自動解碼要求中的存取 token [免費延伸至工具](https://github.com/andrewconnell/SPOAuthFiddlerExt) 。
  
    
    
Web 應用程式伺服器上安裝 Fiddler 之後，將下列標記新增至 web.config 檔案進行遠端 web 應用程式的要求透過此 proxy。如此一來，您可以擷取 Fiddler 追蹤並查看SharePoint完整回應，當您收到的錯誤。
  
    
    

> [!注意事項]
> 請確定您如果您未執行 Fiddler 移除此標記。如果您不移除標記、 增益集將不能夠進行 HTTP 要求。
  
    
    




```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>

```

Fiddler 安裝之後，您也可以檢查回應標頭從SharePoint，其中會包含在要求的 GUID。此要求的 GUID 是您可以查詢以尋找任何記錄檔中的相互關聯識別碼與該要求相關聯的錯誤記錄。
  
    
    

## 401 未經授權的錯誤
<a name="UnauthorizedException"> </a>

當高信任層級增益集第一次存取SharePoint一些事項造成 **401 Unauthorized**錯誤。如果您使用用戶端物件模型 (CSOM)，錯誤會外觀應類似如下：
  
    
    

```cs

[WebException: The remote server returned an error: (401) Unauthorized.]
   System.Net.HttpWebRequest.GetResponse() +8515936
   Microsoft.SharePoint.Client.SPWebRequestExecutor.Execute() +178
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQueryToServer(ChunkStringBuilder sb) +1427
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQuery() +270
   Microsoft.SharePoint.Client.ClientRuntimeContext.ExecuteQuery() +146
   Microsoft.SharePoint.Client.ClientContext.ExecuteQuery() +666
   S2STestWeb.Default.Page_Load(Object sender, EventArgs e) in c:\\MyFiles\\HightrustTest\\HightrustTestWeb\\Default.aspx.cs:28
   System.Web.UI.Control.LoadRecursive() +71
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178
```

如果您使用 TokenHelper 檔案和 Windows 身分識別，會觸發例外狀況的程式碼如下所示：
  
    
    



```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();
```

疑難排解問題您第一個步驟是使用Visual Studio偵錯工具來確認已順利建構存取權杖和 **ClientContext**物件。如果是，請先調查下列各種可能性：
  
    
    
 **可能的問題與解決方法：**
  
    
    

- 有無建立會存取的遠端 web 應用程式之使用者的使用者設定檔。建立使用者設定檔。
    
  
- 增益集沒有您嘗試存取之資源的權限。開啟SharePoint Management Shell並執行下列Windows PowerShell指令程式。變數 `$web`是您嘗試要取得的存取權和 `$appPrincipal`SharePoint網站) 是增益集識別碼。如需詳細資訊，請參閱 ＜  [Set-spappprincipalpermission ＞](http://technet.microsoft.com/en-us/library/jj219714%28v=office.15%29.aspx)。
    
  ```
  
Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl
  ```

- Web 應用程式為接受匿名要求。這表示存取權杖中沒有實際的使用者身分識別。請確定的匿名存取已停用 IIS 中的遠端 web 應用程式的根目錄。您也可以檢查此偵錯遠端 web 應用程式，並檢查 **Request.LogonUserIdentity** default.aspx.cs (或.vb) 檔案中，確認它不是匿名使用者的值。
    
  
- 您的數位憑證已新增至受信任的憑證存放區。請確定您已遵循 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)中的程序。
    
  

## 其他 SSL 和網域相關的驗證錯誤
<a name="DomainRelatedErrors"> </a>

設定檔及註冊表單中的網域名稱不符可以防止授權。下列四個值必須是完全相同：
  
    
    

- SharePoint Add-in AppRegNew.aspx 上的註冊時指定為 **增益集的網域**。
    
  
- 登錄程式的遠端 web 應用程式的安全性憑證的網域。
    
  
- **StartPage**值 AppManifest.xml 檔案中的網域部分。
    
  
- 任何 AppManifest.xml 中指定的事件接收器的 Url 網域部分。
    
  
與此點，請注意下列各項：
  
    
    

- 若您SharePoint Add-in遠端元件會使用任何的連接埠 443，您必須明確中所有的四個地方; 加入網域的一部分的連接埠例如，  `MarketingServer:3333`。(您必須使用 HTTPS 通訊協定的預設連接埠為 443)。
    
  
- 網域必須能夠 AppManifest.xml 檔案 **StartPage**值 (與任何事件接收器 Url) 中的硬式編碼之前封裝增益集。如果您使用 [ **發佈**精靈Visual Studio封裝在一起的增益集，將會提示您輸入網域和Office Developer Tools for Visual Studio將它插入 **StartPage**值供您 (取代偵錯時使用的 `~remoteWebUrl` token。但如果您不使用您必須手動取代權杖的網域 (和通訊協定); [ **發佈**精靈例如 `https://MarketingServer`或 `https://MarketingServer:3333`。
    
  

## 執行階段錯誤訊息沒有憑證的序號
<a name="DomainRelatedErrors"> </a>

如果您是確認您在 web.config 中具有正確的憑證序號，並且您可以查看 **Windows 憑證存放區** 中的憑證，然後可能有隱藏的額外字元序號在 web.config 中。這將會發生如果序號為 copy'n' 從 **Microsoft Management Console** 貼上。刪除整個序號值從 web.config 和 *手動*  重新輸入它。
  
    
    

