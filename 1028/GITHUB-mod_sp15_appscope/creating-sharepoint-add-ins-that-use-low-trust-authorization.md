---
title: 建立 SharePoint 增益集使用低信任層級授權
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# 建立 SharePoint 增益集使用低信任層級授權
了解SharePoint Add-ins低信任層級授權系統。
SharePoint Add-in (或外部應用程式) 中的遠端元件可以傳遞給SharePoint與每個 HTTP 要求的存取權杖入侵SharePoint資源的授權。遠端元件與客戶的Office 365租用相關聯的Microsoft Azure Access Control Service (ACS)帳戶取得存取 token。Azure ACS做為 [OAuth 2.0](http://oauth.net/)交易時，呼叫流程、 SharePoint為資源伺服器與遠端元件做為用戶端的授權伺服器。相關的通訊協定規格，請參閱 [Web 授權通訊協定 (oauth)](http://datatracker.ietf.org/doc/active/#oauth)。
  
    
    

提供者主控SharePoint Add-ins使用低信任層級授權系統可在Office Store售出及Microsoft SharePoint Online或已設定以用於客戶的Office 365租用建立信任關係Azure ACS內部SharePoint伺服器陣列上安裝。客戶必須要有安裝使用低信任層級系統的SharePoint Add-insOffice 365租用。不過，不需要任何其他用途使用租用客戶的。如需連結至Office 365租用的內部部署伺服器陣列的指示，請參閱 [若要授權提供者主控增益集內部部署 SharePoint 網站上使用 Office 365 SharePoint 網站](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)。
## 註冊以Azure ACS
<a name="Registration"> </a>

若要使用低信任層級系統， Azure ACS以及SharePoint伺服器陣列或SharePoint Online租用的 App Management Service 前必須註冊SharePoint Add-in 。(它是可以呼叫"App Management Service"因為SharePoint Add-ins原本 」 稱為 「 SharePoint 應用程式 」)。針對透過Office Store售出的增益集、 登錄至 ACS 執行賣方儀表板且註冊與服務執行時安裝增益集。分散式目錄增益集的組織中的增益集，如註冊 ACS 及服務被執行的任何SharePoint租用或伺服器陣列中之增益集所在安裝 \\_Layouts\\15\\AppRegNew.aspx] 頁面上。外部、 非SharePoint、 存取SharePoint、 應用程式也需要註冊。此類別包含Office 增益集、 Windows 市集應用程式、 web 應用程式，以及裝置等 smartphone 應用程式的應用程式。
  
    
    

> [!注意事項]
> 登錄要求的應用程式沒有網際網路網域。任何現有的網域可用於此目的，但您不能是 100%特定的一定會存在任何不屬於您的網域，讓 web 應用程式必須為原生裝置應用程式的一部分即使 web 應用程式元件播放比若要啟用註冊沒有其他角色。如需設計以這種方式進階的程式碼範例，請參閱 [使用增益集模型的批次中佈建網站](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6)。
  
    
    

如需登錄的詳細資訊，請參閱 [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md)。
  
    
    

### 增益集秘密到期日

增益集密碼必須取代每隔 12 個月。如需詳細資訊，請參閱 [取代過期的用戶端私人 SharePoint 增益集](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)。
  
    
    

## 授權原則
<a name="Policies"> </a>

SharePoint Add-in可以使用兩個授權原則的設計：
  
    
    

- **使用者 + 增益集原則:**增益集使用此原則的只能執行增益集和使用者有權限執行的動作。這是開發人員需要使用其他的特定步驟會使用預設原則。
    
  
- **僅限應用程式的原則：**增益集使用此原則的可執行它具有權限執行動作，即使使用者與權限的巨集指令沒有任何動作。開發人員必須要求此原則可用增益集資訊清單的增益集。要求必須安裝的增益集之使用者所核准。此原則所允許的只提供者主控SharePoint Add-ins。
    
  
如需授權原則的詳細資訊，請參閱 [SharePoint 2013 中的增益集授權原則類型](add-in-authorization-policy-types-in-sharepoint-2013.md)。
  
    
    

## 兩個 OAuth runtime 流程
<a name="Flows"> </a>

雲端託管SharePoint Add-in或存取SharePoint的外部應用程式執行時，流程-每次發生一系列的增益集、 SharePoint 2013、 ACS、 及有時使用者-之間的互動。最終結果是流程的該SharePoint接收存取權杖包含的每個建立、 更新、 刪除 (CRUD) 要求的應用程式對SharePoint。
  
    
    
有兩個主要的 OAuth 流程SharePoint所使用。一個是雲端託管SharePoint Add-ins。其他，呼叫 」 在 「 立即 」 是存取SharePoint資料其他平台上的應用程式。
  
    
    

- **內容權杖流程:**SharePoint Add-in遠端元件會使用來呼叫SharePointSharePoint用戶端物件模型 (CSOM) 或 REST 端點。SharePoint 會從它可以傳送至遠端伺服器的 ACS 要求內容權杖。遠端伺服器從 ACS 要求存取權杖使用內容權杖。遠端伺服器使用存取權杖將 SharePoint 備份與對話。如需此流程的詳細資訊，請參閱 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)。
    
  
- **Authenticaton 程式碼流程:**SharePoint Add-in被授與安裝時需要SharePoint資源的權限。SharePoint未安裝的應用程式必須"「 立即 」 根據要求的權限，但也就是每次時執行。此流程中有無SharePoint內容權杖。而是當增益集執行，並嘗試存取SharePoint、 SharePoint會提示使用者授與該要求的應用程式的權限。當使用者授與權限時、 SharePoint會從它會傳遞至應用程式的 ACS 取得授權的程式碼。應用程式使用程式碼從它可讓然後用來與對話SharePointACS 取得存取 token。如需此流程的詳細資訊，請參閱 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。
    
  

## 疑難排解低信任層級SharePoint Add-ins
<a name="Trouble"> </a>

本文提供一些疑難排解的一般指引和SharePoint Add-ins使用低信任層級驗證系統的一些特定問題的相關資訊。
  
    
    

### 使用 Fiddler 工具

免費的 [Fiddler 工具](http://www.telerik.com/fiddler)可用來擷取遠端元件的增益集所傳送至SharePoint的 HTTP 要求。沒有自動解碼要求中的存取 token [免費延伸至工具](https://github.com/andrewconnell/SPOAuthFiddlerExt) 。
  
    
    

### 關閉期間開發 OAuth HTTPS 需求
<a name="TurnOffHTTPRequirement"> </a>

OAuth 要求SharePoint執行 HTTPS (不只服務，但SharePoint太)。這可以取得離開時所開發的增益集。例如，您可能會取得 403 (禁止) 訊息嘗試撥打電話至 SharePoint 在偵錯時的增益集因為 SSL 支援不是出現在"localhost"的增益集執行時。
  
    
    
您可以關閉期間使用的下列Windows PowerShell cmdlet 開發的 HTTPS 需求。
  
    
    



```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()

```

若要重新打開 HTTPS 需求之後，請使用以下Windows PowerShell cmdlet。
  
    
    



```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()

```


### 其他 SSL 和網域相關的驗證錯誤
<a name="DomainRelatedErrors"> </a>

設定檔及註冊表單中的網域名稱不符可以防止授權。下列四個值必須是完全相同：
  
    
    

- SharePoint Add-in AppRegNew.aspx 或賣方儀表板上的註冊時指定為 **增益集的網域**。
    
  
- 登錄程式的遠端 web 應用程式的安全性憑證的網域。
    
  
- **StartPage**值 AppManifest.xml 檔案中的網域部分。
    
  
- 任何 AppManifest.xml 中指定的事件接收器的 Url 網域部分。
    
  
與此點，請注意下列各項：
  
    
    

- 若您SharePoint Add-in遠端元件會使用任何的連接埠 443，您必須明確中所有的四個地方; 加入網域的一部分的連接埠例如，  `contoso.com:3333`。(您必須使用 HTTPS 通訊協定的預設連接埠為 443)。
    
  
- 如果您建立的 DNS CNAME 別名遠端 web 應用程式，可用於 CNAME 別名中所有的四個地方的網域值。例如，如果您的應用程式架設在 contoso.cloudapp.net 並為其建立的 contososoftware.com CNAME、 作為 contososoftware.com 網域。
    
  
- 網域必須能夠 AppManifest.xml 檔案 **StartPage**值 (與任何事件接收器 Url) 中的硬式編碼之前封裝增益集。如果您使用 [ **發佈**精靈Visual Studio封裝在一起的增益集，將會提示您輸入網域和Office Developer Tools for Visual Studio將它插入 **StartPage**值供您 (取代偵錯時使用的 `~remoteWebUrl` token。但如果您不使用 [ **發佈**精靈] 或即使您但遠端 web 應用程式部署至 Azure，您必須手動取代權杖的網域 (及通訊協定);例如 `https://contososoftware.com`或 `https://MyCloudVM:3333`。
    
  

### 錯誤 「 基礎連接已關閉: 無法建立 SSL/TLS 安全通道的信任關係。 」
<a name="ErrorConnectionClosed"> </a>

這個錯誤是 SSL 信號交換問題，未 OAuth 問題。確定您使用的憑證受信任由SharePoint，且該憑證與相符的端點名稱。
  
    
    

### 使用 HTTP DAV 方法從 SharePoint 讀取檔案的錯誤
<a name="ErrorConnectionClosed"> </a>

HTTP DAV 不適用於 OAuth。如果您使用SharePoint用戶端物件模型 (CSOM)，請使用下列程式碼將檔案讀取。
  
    
    

```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();

```


## 在本章節中
<a name="Trouble"> </a>

 [若要授權提供者主控增益集內部部署 SharePoint 網站上使用 Office 365 SharePoint 網站](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)
  
    
    
 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [取代過期的用戶端私人 SharePoint 增益集](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)
  
    
    

## 其他資源
<a name="FileName_AdditionalResources"> </a>


-  [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md)
    
  

