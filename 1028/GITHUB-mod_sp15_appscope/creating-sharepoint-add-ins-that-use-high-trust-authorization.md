---
title: 建立 SharePoint 增益集使用高信任授權
ms.prod: SHAREPOINT
ms.assetid: 135bf6be-b118-4d2c-8d40-27f5060b06f3
---


# 建立 SharePoint 增益集使用高信任授權
了解高信任SharePoint Add-ins、 其使用數位憑證 SharePoint 與遠端存取 SharePoint 元件之間建立信任。
 **提供:**
  
    
    
Steve Peschka，Microsoft Corporation 的
  
    
    
著 Siew-moi Khor Microsoft Corporation
  
    
    


**觀看有關建立高信任 SharePoint 增益集的影片。**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## 高信任SharePoint Add-ins的概觀
<a name="Overview"> </a>

高信任層級增益集是提供者主控SharePoint Add-in安裝至內部部署 SharePoint 伺服器陣列。安裝至Microsoft SharePoint Online或透過Office Store行銷。高信任層級增益集，而不是內容權杖使用憑證來建立信任。
  
    
    

> **注意事項**
> 本主題可協助您了解SharePoint Add-ins高信任授權系統。如需建立及部署高信任層級增益集的實用資訊，請參閱下列主題：>  [建立高信任 SharePoint 增益集](create-high-trust-sharepoint-add-ins.md)>  [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)
  
    
    

在SharePoint 2013、 security token service (STS) 會提供存取 token 的伺服器對伺服器驗證。存取其他應用程式服務，例如Exchange 2013、 Lync 2013、 和SharePoint Add-insSTS 啟用暫時存取權杖。伺服器陣列管理員使用Windows PowerShell指令程式和憑證來建立 SharePoint 及其他應用程式或增益集 (英文) 之間的信任。使用每個憑證必須信任由SharePoint 2013 `New-SPTrustedRootAuthority`指令程式。也每個憑證必須為使用 `New-SPTrustedSecurityTokenIssuer`指令程式權杖簽發者登錄與 SharePoint。
  
    
    

> **注意事項**
> STS 無法供使用者驗證。如此，您將不會看到列在 [使用者登入] 頁面的 [ **驗證提供者**] 區段中 [人員選擇] 在SharePoint 2013或管理中心] 中的 STS。
  
    
    


## 兩種類型的 token 發行者
<a name="TwoKindsOfIssuers"> </a>

高信任SharePoint Add-in的遠端 web 應用程式繫結至數位憑證。(如需如何這麼做，請參閱連結對上方的兩個主題)。憑證是遠端 web 應用程式用來簽署它包含 SharePoint 其要求的存取 token。Web 應用程式與私密金鑰的憑證簽署權杖與 SharePoint 使用公開金鑰憑證進行驗證。此憑證具有之前 SharePoint 將信任該問題的 token 登錄為受信任權杖發行者。有兩種類型的 token 發行者: 某些只能發行用於特定SharePoint Add-in; 權杖其他呼叫信任經紀人，可以發出用於多個SharePoint Add-ins權杖。
  
    
    
依實用性、 SharePoint 伺服器陣列管理員會決定哪一種權杖簽發者建立的參數與他們 `New-SPTrustedSecurityTokenIssuer`指令程式搭配使用的參數值。若要建立為信任 broker token 簽署者，新增 `-IsTrustBroker`切換至命令列及使用非增益集用戶端識別碼的唯一值 `-Name`參數。以下是已編輯的範例。
  
    
    



```

New-SPTrustedSecurityTokenIssuer -IsTrustBroker -RegisteredIssuerName "<full_token_issuer_name> " --other parameters omitted--
```

若要建立非 broker token 發行者，不會使用 `-IsTrustBroker`參數。有一部其他差異。 `-RegisteredIssuerName`參數的值一定是以分隔的兩個 Guid 的形式"@"字元 ；@ _GUID_ _GUID_。在右側 GUID 一律是 SharePoint 伺服器陣列 (或) 網站訂閱的驗證領域識別碼。在左側的 GUID 一律是 token 簽署者特定識別碼。它是一個隨機 GUID 時所建立的 *信任 broker*  token 發行者。但時建立非 broker token 簽署者時，特定簽發者 GUID 必須是同一個 GUID 作為SharePoint Add-in的用戶端識別碼。此參數提供發行者的名稱。它也會告知 SharePoint 哪些SharePoint Add-in是唯一的憑證可核發 token。以下是部分範例：
  
    
    



```
$fullIssuerIdentifier = "<client_ID_of_SP_app> " + "@" + "<realm_GUID> "
New-SPTrustedSecurityTokenIssuer -RegisteredIssuerName $fullIssuerIdentifier --other parameters omitted--
```

通常使用 `New-SPTrustedSecurityTokenIssuer` cmdlet 所執行的其他工作來設定 SharePoint 的高信任層級增益集的指令碼中。如需這類指令碼及完成的範例 `New-SPTrustedSecurityTokenIssuer` cmdlet 的詳細資訊，請參閱 [SharePoint 2013 高信任層級設定指令碼](high-trust-configuration-scripts-for-sharepoint-2013.md)。
  
    
    

## 決定使用一個憑證或許多的高信任SharePoint Add-ins之間
<a name="Deciding"> </a>

SharePoint 和網路管理員已有兩種基本的策略從中取得和管理高信任SharePoint Add-ins憑證以便使用時：
  
    
    

- 使用多個SharePoint Add-ins相同的憑證。
    
  
- 每個SharePoint Add-in可用於不同的憑證。
    
  
本章節將討論每個策略的優缺點。
  
    
    
對多個SharePoint Add-ins使用相同的憑證的優點是系統管理員具有較少信任和管理憑證。此方法中的缺點是當您要中斷與特定SharePoint Add-in信任之的情況發生時，系統管理員可以自動換行信任的唯一方式是移除 token 簽署者或身分的根授權單位的憑證。但移除憑證也會中斷所有其他SharePoint Add-ins使用相同的憑證，使其所有停止運作的信任。
  
    
    
同樣地，若要取得仍可信賴SharePoint Add-ins工作系統管理員會需要建立 `New-SPTrustedSecurityTokenIssuer`物件 *使用新的憑證*  ，包含 `-IsTrustBroker`旗標。然後將新的憑證必須註冊的主控任何值得信任增益集的每部伺服器與每個這些增益集必須繫結至新的憑證。
  
    
    
使用增益集的每一個憑證的優點是它容易中斷信任關係的特定增益集，因為系統管理員會中斷信任之憑證的一個增益集時仍值得信任增益集所使用的憑證不受影響。在這項策略的缺點是系統管理員具有以取得更多憑證與 SharePoint 必須設定為使用每個，可藉使用可重複使用的指令碼 [SharePoint 2013 高信任層級設定指令碼](high-trust-configuration-scripts-for-sharepoint-2013.md)所示。
  
    
    

## 憑證已在 SharePoint 中的根授權單位
<a name="RootAuthorities"> </a>

簡短提及頂端的本文，SharePoint 伺服器陣列管理員必須進行中的高信任層級增益集、 SharePoint 中的受信任的根授權單位，以及受信任權杖發行者的遠端 web 應用程式的憑證。事實上，發出背後的 web 應用程式的憑證授權單位的憑證的階層時，請鏈結中的所有憑證必須都加入至 SharePoint 的清單中的信任的根授權單位。
  
    
    
每個憑證鏈結中的會新增至 SharePoint 的清單中的呼叫 `New-SPTrustedRootAuthority`指令程式的信任的根授權單位。例如，假設 web 應用程式的憑證是由 LAN 上企業網域憑證授權單位發行網域憑證和假設其憑證、 依次發出時獨立、 LAN 上的最上層的憑證授權單位。在此案例中，最上層的 CA、 中間的 CA 和 web 應用程式所有的憑證必須加入至受信任的根授權單位 SharePoint 的清單。下列Windows PowerShell程式碼會完成這個工作。
  
    
    



```

$rootCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<path_to_top-level_CA's_cer_file>")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $rootCA

$intermediateCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_intermediate_CA's_cer_file")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $intermediateCA

$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_web_application's_cer_file") 
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $certificate 

```

根目錄和中繼憑證應新增一次只在 SharePoint 伺服器陣列上。一般而言，在不同的指令碼可執行其他設定，例如 `New-SPTrustedSecurityTokenIssuer`呼叫新增 web 應用程式的憑證。如需範例，請參閱 [SharePoint 2013 高信任層級設定指令碼](high-trust-configuration-scripts-for-sharepoint-2013.md)。
  
    
    
如果 web 應用程式的憑證是自我簽署的增益集為時可能會大小寫正在開發和偵錯然後有無憑證鏈結，且僅 web 應用程式的憑證需要新增至根授權單位的清單。
  
    
    
如需詳細資訊，請參閱部落格文章 [根憑證鏈結不受信任的宣告式驗證與錯誤](http://blogs.technet.com/b/speschka/archive/2010/02/13/root-of-certificate-chain-not-trusted-error-with-claims-authentication.aspx)。(捲動過去關於匯出憑證從 Active Directory Federation Services (AD FS) 假設您在高信任層級增益集透過其他的一些方法，例如使用憑證授權單位所發出的商業憑證、 自我簽署的憑證或網域發出的憑證來建立您的憑證 ＞ 一節)。
  
    
    

## Web 應用程式需要知道的 token 發行者
<a name="AppIsTokenIssuer"> </a>

SharePoint Add-in的遠端 web 應用程式元件繫結至 IIS 中其憑證。這是足夠的傳統的用途的憑證： 安全地識別 web 應用程式及編碼的 HTTP 要求及回應。不過，在高信任SharePoint Add-in、 憑證有其他工作正在官方"發行者 」 的 web 應用程式傳送至 SharePoint 存取 token。針對此目的而、 web 應用程式具有知道時將憑證註冊為與 SharePoint 的 token 發行者所使用之憑證的發行者識別碼。Web 應用程式會從 [ **appSettings** ] 區段中的 web.config 檔案中，取得此識別碼是名為 **IssuerId**機碼。增益集開發人員如何設定此值，及如何憑證繫結至 IIS] 中的 web 應用程式的指示位於 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)。請注意是否客戶使用的每個高信任SharePoint Add-in的個別憑證的策略，然後 **ClientId**值也可作為 **IssuerId**值。這是沒有大小寫時多個增益集共用相同的憑證，因為每個SharePoint Add-in必須有其專屬唯一用戶端識別碼，但簽發者識別碼是 **SPTrustedSecurityTokenIssuer**物件的識別碼。
  
    
    
以下是高信任SharePoint Add-in **appSettings**一節的範例。在這個範例中，憑證共用由多個增益集，因此 **IssuerId**沒有 **ClientId**相同。請注意高信任SharePoint Add-in中有沒有 **ClientSecret**機碼。
  
    
    



```XML

<appSettings>
  <add key="ClientId" value="6569a7e8-3670-4669-91ae-ec6819ab461" />
  <add key="ClientSigningCertificatePath" value="C:\\MyCerts\\HighTrustCert.pfx" />
  <add key="ClientSigningCertificatePassword" value="3VeryComplexPa$$word82" />
  <add key="IssuerId" value="e9134021-0180-4b05-9e7e-0a9e5a524965" />
</appSettings>

```


> **安全性注意事項**
> 上述範例假設憑證儲存在檔案系統上。這是可接受進行開發、 偵錯。在實際執行高信任SharePoint Add-in、 憑證通常是儲存在 Windows 憑證存放區，並 **ClientSigningCertificatePath**和 **ClientSigningCertificatePassword**鍵通常以此 **ClientSigningCertificateSerialNumber**機碼。
  
    
    


## 高信任系統中的 IT 人員責任
<a name="ITPro"> </a>

開發人員必須了解應用程式的安全性需求如上述，但 IT 專業人員實作支援它所需的基礎結構。IT 專業人員必須規劃以下操作需求：
  
    
    

- 建立或購買一或多個要用於信任SharePoint Add-ins的憑證。
    
  
- 請確定憑證都安全地儲存在 web 應用程式伺服器。使用 Windows OS 時，這表示儲存在 Windows 憑證存放區中的憑證。
    
  
- 管理的開發人員會建置SharePoint Add-ins這些憑證的散佈。
    
  
- 保留其中要分散式每個憑證，這兩個增益集使用它與開發人員已接收到複本的追蹤。如果要撤銷的憑證，IT 人員必須處理每位開發人員排列的受管理的轉換至新的憑證。
    
  

## 其他資源
<a name="AR"> </a>


-  [建立高信任 SharePoint 增益集](create-high-trust-sharepoint-add-ins.md)
    
  
-  [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)
    
  
-  [疑難排解秘訣高信任的增益集在 SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)
    
  
-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  

