---
title: 註冊 sharepoint2013 增益集
ms.prod: SHAREPOINT
ms.assetid: be41a5dc-2af9-4fd9-bf4e-ad6dfa849524
---



# 註冊 sharepoint2013 增益集
在Azure ACS登錄SharePoint Add-ins您可以使用Visual Studio、 賣方儀表板或 AppRegNew.aspx] 頁面上，以及擷取註冊的資訊。
提供者主控SharePoint Add-inSharePoint使用 OAuth 與互動之遠端元件、 增益集必須先註冊 [Azure ACS](https://msdn.microsoft.com/en-us/library/azure/gg429788.aspx)雲端架構服務與SharePoint租用或伺服器陣列的應用程式管理服務。(它是可以呼叫"App Management Service"因為SharePoint Add-ins原本 」 稱為 「 SharePoint 應用程式 」)。
  
    
    


> [!注意事項]
> 這不是必要的 SharePoint 主控的增益集。
  
    
    


增益集登錄與Azure ACS，您可以指定下列資訊：
  
    
    


- 增益集、 GUID 呼叫用戶端識別碼。
    
  
- 增益集] 中的密碼呼叫用戶端密碼。
    
  
- 增益集用在同意] 頁面上會提示使用者設為信任的增益集的顯示名稱。
    
  
- 遠端的增益集由主控所在網域的 URL。
    
  
- 重新導向的 URL。
    
  
您註冊您的增益集後，已增益集的 identity，並為 *安全性主體*  ，稱為 [增益集主體。當您安裝增益集時， SharePoint系統管理員可以擷取該特定的增益集主體的相關資訊。當使用者第一次授與存取SharePoint資源 (可以發生在安裝或是 runtime，視應用程式的設計而定) 增益集的權限時、 SharePoint會從Azure ACS取得增益集的相關資訊。SharePoint然後將此資訊儲存在SharePoint租用或伺服器陣列的 App Management Service 資料庫中。用戶端密碼為只能與Azure ACS一起儲存。SharePoint永遠不知道增益集的密碼。內容資料庫服務及其他元件，例如使用者設定檔服務可以取得顯示名稱和其他的基本資訊直接從應用程式管理的增益集共用服務。如需詳細資訊，請參閱本文中的 [擷取註冊增益集及增益集主要資訊](register-sharepoint-add-ins-2013.md#Retrieve)。
> [!注意事項]
> 本文假設您已熟悉的基本概念和背後 OAuth 2.0 架構原則。如需詳細資訊，請參閱 [OAuth.net](http://oauth.net/)和 [Web 的驗證通訊協定 (oauth)](http://datatracker.ietf.org/doc/active/)。
  
    
    


## 在Azure ACS中註冊您SharePoint Add-in

您可以根據您的增益集開發生命週期中的位置、 您的增益集，及如何規劃上市它的架構的三種方式之一註冊您的增益集。
  
    
    


|**註冊方法**|**詳細資料**|
|:-----|:-----|
|使用Visual Studio及Microsoft Office Developer Tools for Visual Studio建立暫存的增益集識別。 <br/> |Office Developer Tools for Visual Studio精靈建立暫存的註冊增益集與 ACS 開頭且您SharePoint測試網站的應用程式管理服務。當您從Visual Studio (F5) 執行增益集時，使用此 identity。工具也會將用戶端識別碼和密碼插入 web.config 和 AppManifest.xml 檔案中。 <br/> 當您準備好發佈增益集時，您可以使用Visual Studio發佈精靈] 以移至賣方儀表板以將其登錄。如果您不行銷您SharePoint Add-inOffice Store中可用於 AppRegNew.aspx 將其登錄。(確切步驟是下方)。 <br/> > [!注意事項]> 如果增益集要求 」 權限存取SharePoint資源動態在執行階段，而不是在增益集的安裝，您無法使用Visual Studio建立增益集的識別身分。          |
|註冊增益集透過賣方儀表板。 <br/> |如果您正在使用您的增益集在一個以上的SharePoint承租人或伺服器陣列中，使用賣方儀表板註冊您的增益集，不論您將上市Office Store中還是以供透過增益集類別目錄。當您在賣方儀表板中註冊時，您可以設計增益集的 multitenant 架構而不需要另外登錄的租用戶或伺服器陣列管理員。此外，如果您打算發佈Office Store中的 [增益集，您必須使用賣方儀表板註冊增益集。您不需要使用增益集已註冊的賣方儀表板發佈的存放區。 <br/> 如需詳細資訊，請參閱 [建立或更新用戶端識別碼和賣方儀表板中的機密資料](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)。 <br/> |
|使用 [AppRegNew.aspx] 頁面。 <br/> |如果您要使用增益集只能在一個承租人或伺服器陣列中註冊您SharePoint Add-in使用 AppRegNew 表單。例如，如果您建立增益集的單一組織與您要將其散佈透過目錄增益集的組織，您可用於租用或伺服器陣列中的任何網站的 [AppRegNew.aspx] 頁面上註冊增益集。 <br/> 您無法發佈至Office Store使用 AppRegNew.aspx 登錄增益集。增益集的發佈至Office Store，您必須從賣方儀表板取得身分識別。 <br/> |
   

### 若要使用 AppRegNew.aspx 註冊


1. 瀏覽至 [  `http://` *< SharePointWebsite >*  `/_layouts/15/AppRegNew.aspx`租用或伺服器陣列上。
    
   **AppRegNew] 頁面上的表單**

  

     ![[App Reg New] 頁面上的表單有用戶端識別碼、用戶端密碼、標題、應用程式網域和重新導向 URL 幾個方塊。前兩個方塊旁邊有「產生」按鈕。下角有「建立」和「取消」按鈕。](images/9a38d876-2189-418c-9314-ae493a4cab61.PNG)
  

  

  
2. 輸入追蹤表單欄位的值：
    
  - **增益集識別碼** 也稱為 「 用戶端識別碼 (如果您選擇 **產生**) 可產生的 GUID 或貼入 AppRegNew.aspx。此值必須是唯一的每個增益集和 *必須是小寫*  。
    
  
  - **增益集密碼** -也稱為用戶端密碼、 不透明的字串。它會產生 AppRegNew.aspx] 頁面上使用 [ **產生**] 按鈕。以下是範例增益集的機密: **xvVpG0AgVIJfch6ldu4dLUlcZyysmGqBRbpFDu6AfJw =** 。
    
    > [!重要]
      > 增益集機密資料過期。如果您註冊增益集在賣方儀表板，您可以設定多達三年的到期時間。在儀表板，您也可以新增新的機密資料舊的達到其到期日期時。在所有執行個體中之增益集將會啟用新密碼。如果您與 AppRegNew.aspx 登錄的增益集、 密碼過期中一年以上。如需詳細資訊，請參閱 [取代過期的用戶端私人 SharePoint 增益集](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)。
  - **標題** 使用者易記標題 ；例如，Contoso 相片列印增益集。 授與或拒絕的增益集所要求的增益集的權限會提示使用者。此標題顯示為同意提示字元上的增益集的名稱。
    
  
  - **增益集網域** - SharePoint Add-in遠端元件的主機名稱。如果遠端應用程式未使用的連接埠 443，增益集的網域必須也包含連接埠號碼。增益集的網域必須符合用於 web 應用程式的 URL 繫結。不包含通訊協定 ("https:") 或"/"字元的這個值。如果您的 web 應用程式主機要使用的 DNS CNAME 別名，請使用別名。一些範例：
    
  - www.contoso.com:3333
    
  
  - www.fabrikam.com
    
  
  - **重新導向 URI:** -遠端應用程式或服務的 ACS 傳送驗證程式碼中的端點。嚴格來說SharePoint Add-ins不使用這個值。重新導向 URI 是必要的 web 應用程式的已啟動外部SharePoint而且可用於取得授權的存取 SharePoint 資料 [驗證程式碼流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)。(這會從SharePoint啟動及使用 [快顯 Token 流程](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows))，則為 true SharePoint Add-ins會忽略重新導向 URI。重新導向 URI 通常是同一頁上、 控制站方法、 web 服務方法要求驗證碼從 ACS，但它可以為不同的端點。端點必須從 HTTP 回應傳送的 ACS，然後使用該程式碼以要求存取及重新整理 token 取得授權的程式碼的邏輯。如需詳細資訊，請參閱 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。表單需要您輸入有效的值甚至是的則為 true SharePoint Add-ins，但它不會使用。
    
    值必須包括 *這必須是 HTTPS*  通訊協定的完整端點 URL。例如：
    
  - https://www.contoso.com/Default.aspx
    
  
  - https://www.fabrikam.com/RedirectAccept.aspx
    
  
  - https://www.northwindtraders.com/home/index
    
  
  - https://adventureworks.com/vacationdata.svc
    
  
3. 選擇 [ **建立**的表單上。頁面會重新載入並顯示一則確認您輸入的值。會複製並貼至 eay 表單中進行這些值的記錄。您必須在 web.config 和 AppManifest.xml 檔案或Visual Studio **發佈**精靈] 中輸入值。
    
  
不管的註冊您SharePoint Add-in、，當您準備好的增益集部署至臨時或實際執行，您需要 [在 web.config 和 AppManifest.xml 檔案中輸入的登錄值](#EditConfigFiles)。如果您使用 Visual Studio， Microsoft Office Developer Tools for Visual Studio不要為您此設定。
  
    
    

## 在 web.config 和 AppManifest.xml 檔案中輸入的登錄值
<a name="EditConfigFiles"> </a>

封裝SharePoint Add-in前和部署其遠端元件之前，請輸入登錄值的一些 AppManifest.xml 和 web.config 檔案中。
  
    
    

> [!秘訣]
> 如果您使用Visual Studio發佈您SharePoint Add-in發佈精靈、 Visual Studio會提示您輸入用戶端識別碼和用戶端密碼發佈程序、 期間和它會將資訊正確的地方置於您。
  
    
    


1. 在 Web.config 檔案中Visual Studio專案中，輸入增益集識別碼值為 **ClientId**值 (取代工具輸入暫存值)。
    
    > [!重要]
      > 在用戶端識別碼 GUID 的所有字母必須都是小寫。

    以下為範例：
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
   .  .  .
</appSettings>
  ```

2. 輸入增益集秘密值做為 **ClientSecret**值 (取代工具輸入暫存值)。
    
    以下是如何將值使用的 web 應用程式的 Web.config 檔案中的範例。
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
  <add key="ClientSecret" value="l0z/8TzWN0yQBzMBSEZtYts2Vt3Eo/oE3rfCdPaogKQ= " />
</appSettings>
  ```

3. 在 AppManifest.xml 檔案中Visual Studio專案中，輸入增益集識別碼值作為 **ClientId**值， *與小寫字母*  。
    
    > [!注意事項]
      > 增益集資訊清單不適用於 web 應用程式的要求即時存取SharePoint資源的權限。這些是不真正"SharePoint Add-ins"。它們都不會安裝在SharePoint並不需要增益集資訊清單。如需詳細資訊，請參閱 [驗證程式碼 OAuth 流程 for SharePoint 增益集](authorization-code-oauth-flow-for-sharepoint-add-ins.md)。

    下列範例顯示如何 **ClientId**值會用於 AppManifest.xml 檔案。
    


  ```XML
  
<AppPrincipal>
  <RemoteWebApplication ClientId="a044e184-7de2-4d05-aacf-52118008c44e "/>
</AppPrincipal>
  ```

4. Office Developer Tools for Visual Studio使用 token  `~remoteAppUrl` **StartPage**元素中。(例如 `<StartPage>~remoteAppUrl/Pages/Default.aspx?{StandardTokens}</StartPage>`。)如果您使用 [ **發佈**精靈] 中Visual Studio遠端元件的 url 會解析此 token。如果您不要使用精靈 (或者如果您執行動作，但您所發佈的遠端元件至 Azure)，您必須手動將權杖取代登錄的增益集時所用的 **增益集的網域**值。其必須 *完全*  相同的值，包括連接埠號碼，如果有的話，除了包含 HTTPS 通訊協定。以下是範例。
    
  ```XML
  
<StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
  ```

5. 請考慮使用 **Title**檔中的元素 AppManifest.xml AppRegNew.aspx 中的 [ **標題**] 欄位所用的相同值。 **Title**元素值是增益集的名稱使用者會看到安裝後。您可能比已定義SharePoint UI 中有不同的名稱同意] 對話方塊中的增益集使用者造成混淆。
    
    下列範例會顯示這些值增益集資訊清單中。
    


  ```XML
  <Properties>
  <Title>Contoso photo printing app</Title>
  <StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
</Properties>
  ```


## 使用重新導向 URL 中要求的權限在即時增益集
<a name="UseRedirectUrl"> </a>

如果您的 web 應用程式啟動從外部的 SharePoint (而且，因此，不是 true SharePoint Add-in)，其具有設計詢問SharePoint於執行階段時將權限。它具有已從 ACS 取得存取權杖使用重新導向 URI、 以及其他資訊的程式碼。尋找其中此 URI 設定及使用 **重新導向 URI** ] 欄位中 AppRegNew.aspx 或賣方儀表板中所用的 *實際*  值的位置。這可能是在程式碼檔案或設定檔。
  
    
    

## 擷取註冊增益集及增益集主要資訊
<a name="Retrieve"> </a>

您可以擷取增益集登錄資訊及增益集主體的資訊之增益集已安裝或登錄SharePoint上。
  
    
    
若要尋找已註冊增益集的登錄資訊，請移至 `http://` *< SharePointWebsite >*  `/_layouts/15/AppInv.aspx`。
  
    
    
若要執行查閱，您必須記住註冊增益集所用的用戶端識別碼 (也稱為增益集 ID)。Lookup 函數傳回的特定用戶端識別碼的下列資訊：
  
    
    

- 標題
    
  
- 增益集的網域
    
  
- 重新導向的 URL (這是重新導向 URI 相同)。
    
  
查閱不會傳回增益集秘密值。
  
    
    
若要查看已登錄的增益集主體的清單，請前往：
  
    
    
 `http://` *<SharePointWebsite>*  `/_layouts/15/AppPrincipals.aspx`
  
    
    

## 其他資源
<a name="AR"> </a>


-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [三個授權系統的 SharePoint 增益集](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
