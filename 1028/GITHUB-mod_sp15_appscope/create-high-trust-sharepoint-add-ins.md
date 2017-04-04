---
title: 建立高信任 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 33294041-48ae-4ee3-846c-acb57b5409cb
---


# 建立高信任 SharePoint 增益集
了解如何建立高信任SharePoint Add-in。高信任層級增益集使用數位憑證的遠端 web 應用程式和SharePoint 2013之間建立信任。高信任層級增益集只能以安裝在內部部署 SharePoint，不適用於Microsoft SharePoint Online、 和主要對象搭配內部部署，而不是雲端式 web 應用程式。
## 建立高信任層級增益集的必要條件
<a name="Prereqs"> </a>

若要遵循本文中的程序，請確定您有下列：
  
    
    

- 在內部SharePoint 2013開發環境。請參閱 [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)的安裝指示。特別是，請確定您已完成的 [設定伺服器對伺服器增益集使用SharePoint中的服務](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md#Servertoserver)] 區段中的步驟。
    
  
- 建立提供者主控SharePoint Add-in體驗。請參閱 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)。
    
  
- Visual Studio安裝遠端或SharePoint 2013的安裝所在的電腦上。
    
  
- Microsoft Office Developer Tools for Visual Studio.
    
  
- 非常熟悉數位憑證  及其以及如何使用它們。
    
  
請閱讀下列文章以取得更了解SharePoint Add-ins和數位憑證。
  
    
    

**表 1。設定SharePoint 2013執行高信任層級增益集的核心概念**


|**文章標題**|**描述**|
|:-----|:-----|
| [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |了解如何建立基本提供者主控SharePoint Add-inOffice Developer Tools for Visual Studio，如何使用 SharePoint CSOM 與SharePoint 2013網站互動。 <br/> |
| [Digital Certificates](http://msdn.microsoft.com/library/e523b335-0156-4f47-b55c-b80495587c4f.aspx)和 [Working with Certificates](http://msdn.microsoft.com/library/6ffb8682-8f07-4a45-afbb-8d2487e9dbc3.aspx) <br/> |了解數位憑證背後的基本概念。 <br/> |
   

## 若要執行高信任層級增益集的簡介
<a name="Intro"> </a>

高信任層級增益集是提供者主控SharePoint Add-in用以數位憑證的遠端 web 應用程式與 SharePoint 之間建立信任。"高信任"不是 「 完全信任 」 相同。高信任層級增益集必須仍會要求增益集的權限。增益集會被視為"高信任"因為它是因為增益集是負責建立它會傳遞至 SharePoint 存取權杖的使用者部分使用增益集需要任何使用者身分識別信任。
  
    
    
高信任SharePoint Add-in被主要針對內部部署環境中使用。高信任層級增益集無法安裝至Microsoft SharePoint Online，且遠端元件通常是安裝在內部部署、 太、 公司防火牆內。即得出SharePoint Add-in執行個體特有為每個個別的公司。
  
    
    
高信任層級增益集，而不是內容權杖使用憑證來建立信任。(提供者主控增益集建立做為其信任 broker 需要修改做為高信任應用程式的Microsoft Azure Access Control Service (ACS) 。)高信任層級增益集需要一些設定 SharePoint 伺服器陣列及主控的遠端 web 應用程式之伺服器上。本主題會說明若要取得Visual Studio偵錯 ( **F5**) 工作所需的設定步驟。設定測試、 臨時、 或實際執行環境稍有不同，主題 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)所述。
  
    
    
在SharePoint 2013、 伺服器對伺服器 security token service (STS) 會提供存取 token 的伺服器對伺服器驗證。伺服器對伺服器 STS 讓存取SharePoint 2013Exchange 2013、 Lync 2013、 及增益集等其他應用程式服務的暫存存取權杖。在建立使用Windows PowerShell指令程式和憑證的應用程式服務 (例如 SharePoint 與遠端的增益集之間建立信任) 之間的信任關係。
  
    
    

> [!注意事項]
> 伺服器對伺服器 STS 無法供使用者驗證。因此，您將不會看到列在 [使用者登入] 頁面的 [ **驗證提供者**] 區段中 [人員選擇] 在SharePoint 2013或管理中心] 中的伺服器對伺服器 STS。
  
    
    

本文顯示如何建立高信任層級增益集，並提供安裝指示內Visual Studio執行按 **F5**。您將了解如何：
  
    
    

- 設定增益集使用為高信任層級增益集。
    
  
- 設定SharePoint 2013使用高信任層級增益集。
    
  
- 建立基本高信任層級增益集。
    
  

## 取得憑證或建立公用及私人測試憑證
<a name="Cert2"> </a>

您需要的 X.509 數位憑證的高信任層級增益集的遠端 web 應用程式。若要完整測試您SharePoint Add-in，您需要網域發出的憑證或憑證授權單位所發出的商業憑證。不過，偵錯的初始階段，您可以使用自我簽署的憑證。下列程序說明如何建立和使用 IIS 來匯出測試憑證。您將了解如何將自我簽署的憑證取代 [完成偵錯與發出的網域或商業憑證](#NewCertificate)下一節中的發行網域或商業憑證。
  
    
    
或者，您也可以使用 MakeCert 測試程式產生的 X.509 憑證。如需如何使用 MakeCert 的詳細資訊，請參閱 [簽署和檢查 Authenticode 的程式碼](http://msdn.microsoft.com/en-us/library/ms537364%28VS.85%29.aspx)。
  
    
    
您將會先建立測試.pfx 憑證檔案並再對應的測試.cer 檔案。.Pfx 憑證含有私密金鑰遠端 web 應用程式用來登入 SharePoint 與其通訊。.cer 包含 SharePoint 解密的郵件，確認其來自的遠端 web 應用程式驗證的遠端 web 應用程式具有存取權杖從 SharePoint 伺服器信任 token 簽署者所使用的公開金鑰。如需.pfx 和.cer 檔案的詳細資訊，請參閱 [軟體發佈者憑證](http://msdn.microsoft.com/en-us/library/windows/hardware/ff552299%28v=vs.85%29.aspx)
  
    
    

### 若要建立的自我簽署的測試.pfx 憑證檔案


1. 當您正在偵錯中Visual Studio高信任SharePoint Add-in時、 遠端 web 應用程式架設在 IIS Express Visual Studio安裝所在的電腦上。讓遠端 web 應用程式電腦都不會有您可以在其中建立憑證 IIS 管理員。此原因而您使用 IIS  *SharePoint*  測試伺服器上建立憑證。在 [IIS 管理員中，選取 _ServerName_節點左側的樹狀檢視中。
    
  
2. 選取 [ **伺服器憑證**] 圖示，如圖 1 所示。
    
   **圖 1。在 IIS 中的伺服器憑證選項**

  

     ![Server Certificates option in IIS](images/e38f9b7f-59a3-468c-bcde-a48272f1f217.gif)
  

  

  
3. 選取 [ **建立自我簽署憑證**] 連結的右側，連結從，圖 2 所示。
    
   **圖 2。建立自我簽署憑證連結**

  

     ![Create Self-Signed Cerificate link](images/3f0aae5a-e58b-4ec8-b67f-0024abfa2dab.gif)
  

  

  
4. 名稱的憑證HighTrustSampleCert，並再選擇 **[確定]**。
    
  
5. 以滑鼠右鍵按一下 [憑證]，並選取 [ **匯出**，如圖 3 所示。
    
   **圖 3。將匯出的測試憑證**

  

     ![Exporting a test certificate](images/997021de-c60c-46b0-961f-7e1e63c0f619.gif)
  

  

  
6. 在 Windows 中，或在命令列中，建立一個名為 C:\\Certs 資料夾。
    
  
7. 回在 IIS 管理員中，將檔案匯出至 C:\\Certs 並為其指定密碼。在這個範例中，密碼會受到 **密碼** 。
    
  
8. 如果您的測試 SharePoint 安裝不相同Visual Studio執行所在的電腦上，建立資料夾 C:\\Certs Visual Studio電腦上，並將 HighTrustSampleCert.pfx 檔案移至其。這是當您正在偵錯Visual Studio中的遠端 web 應用程式會執行的電腦。
    
  

### 若要建立對應的.cer 檔案


1. 在 SharePoint 伺服器上，請確定下列 IIS 增益集區的增益集區身分識別已閱讀 C:\\Certs 資料夾的權限：
    
  - **SecurityTokenServiceApplicationPool**
    
  
  - 增益集區中做為您的測試 SharePoint 網站的 SharePoint web 應用程式主控父項的 IIS 網站。 **SharePoint - 80** IIS 網站的集區會呼叫 **OServerPortalAppPool**。
    
  
2. 在 [IIS 管理員中，選取 _ServerName_節點左側的樹狀檢視中。
    
  
3. 按兩下 [伺服器憑證]。
    
  
4. 在 [ **伺服器憑證**] 檢視中，按兩下 **HighTrustSampleCert** 顯示憑證的詳細資訊。
    
  
5. 在 [ **詳細資料**] 索引標籤上選擇 [以啟動 **憑證匯出精靈**] 的 [ **複製到檔案**，然後選擇 [ **下一步**。
    
  
6. 使用預設值 [ **否，不要匯出私密金鑰**]，然後選擇 [ **下一步**。
    
  
7. 使用預設值。選擇 [ **下一步**]。
    
  
8. 選擇 [ **瀏覽**、 瀏覽至 C:\\Certs、 **HighTrustSampleCert** 、 的憑證名稱，然後選擇 **儲存**。憑證會儲存為.cer 檔。
    
  
9. 選擇 [下一步]。
    
  
10. 選擇 [ **完成**]。
    
  

## 設定SharePoint 2013使用憑證並設定信任的增益集
<a name="Configure2"> </a>

在本節中建立Windows PowerShell指令碼的目的Visual Studio中支援使用 **F5** 。它會 *不*  正確設定臨時或實際執行 SharePoint 安裝。如需設定的實際執行 SharePoint 使用的憑證，請參閱 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)指示。
  
    
    

> [!注意事項]
> 請仔細檢查您已完成 [設定伺服器對伺服器增益集使用SharePoint中的服務](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md#Servertoserver)(這列為本文的先決條件) 中的步驟。如果不是，您必須將其設定現在，您才可以繼續。
  
    
    


### 若要設定 SharePoint


1. 在文字編輯器或Windows PowerShell編輯器中，啟動新的檔案並新增下列幾行它來建立憑證物件：
    
  ```
  
$publicCertPath = "C:\\Certs\\HighTrustSampleCert.cer"
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($publicCertPath)

  ```

2. 新增下列行以確保 SharePoint 的來說憑證的根授權單位。
    
  ```
  
New-SPTrustedRootAuthority -Name "HighTrustSampleCert" -Certificate $certificate

  ```

3. 新增下列字行取得的驗證領域識別碼。
    
  ```
  
$realm = Get-SPAuthenticationRealm

  ```

4. 您的遠端 web 應用程式會使用存取權杖來取得 SharePoint 資料的存取權。必須由 SharePoint 信任 token 簽署者發出存取權杖。高信任SharePoint Add-in，則憑證的 token 簽署者。新增下列行來建構 SharePoint 需要格式簽發者 ID: ** _specific_issuer_GUID_@ _realm_GUID_** 。
    
  ```
  
$specificIssuerId = "11111111-1111-1111-1111-111111111111"
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

  ```


    > [!注意事項]
      > 因為在生產環境中每個憑證必須具備唯一簽發者 `$specificIssuerId`值必須是 GUID。不過，在此上下文中，其中您可以使用相同的憑證偵錯所有您高信任層級增益集，您可以硬式編碼值。如果有任何原因您使用不同的 GUID 透過在這裡使用， * **請務必以 GUID 的任何字母是小寫*** 。SharePoint 基礎結構目前需要憑證簽發者 Guid 小寫。
5. 新增下列行以登錄為受信任權杖發行者的憑證。 `-Name`參數必須是唯一的讓在實際執行設定中，為一般使用一部分的 GUID (或所有) 名稱，但在此上下文中，您可以使用的易記名稱。若要確保您可以使用相同的憑證所有高信任層級增益集的開發需要 `-IsTrustBroker`參數。 `iisreset`命令才可進行立即註冊您 token 簽署者。沒有，您可能必須等候時間為 24 小時的值要註冊新的簽署者。
    
  ```
  
New-SPTrustedSecurityTokenIssuer -Name "High Trust Sample Cert" -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier -IsTrustBroker
iisreset 

  ```

6. SharePoint 2013不正常接受自我簽署的憑證。當您使用自我簽署的憑證用於偵錯時，所以新增下列字行，以關閉 SharePoint 的一般需求 HTTPS 遠端 web 應用程式呼叫到 SharePoint 時使用。如果您沒有您會得到 **403 (禁止)**訊息時的遠端 web 應用程式呼叫 SharePoint 使用自我簽署的憑證。您將反向後續的程序此步驟。關閉 HTTPS 需求表示從 SharePoint 遠端 web 應用程式要求尚未加密，但憑證仍會當做存取權杖的信任發行者即高信任SharePoint Add-ins及其主要用途。
    
  ```
  
$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()

  ```

7. 名稱為 HighTrustConfig ForDebugOnly.ps1 儲存檔案。
    
  
8. 開啟 **SharePoint 管理命令介面**以系統管理員身分並執行之檔案的下一行：
    
  ```
  
./HighTrustConfig-ForDebugOnly.ps1
  ```


## 建立高信任SharePoint Add-in
<a name="Createapp2"> </a>

在本節中您了解如何建立使用Visual Studio高信任SharePoint Add-in 。
  
    
    

> [!注意事項]
>  [建立高信任層級增益集的必要條件](#Prereq)」 一節所述，本文會假設您知道如何建立提供者主控SharePoint Add-in。如需詳細資訊，請參閱 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)。
  
    
    


### 若要建立高信任SharePoint Add-in


1. 在Visual Studio、 選擇 [ **檔案**、 **新增**、 **專案**。
    
  
2. 在 [ **新增專案**精靈] 中展開 **Visual C#**或 **Visual Basic** ] 節點中，，然後展開 [ **Office/SharePoint** ] 節點。
    
  
3. 選擇 [ **增益集**]，然後按來建立 **SharePoint 的增益集**專案。
    
  
4. 專案HighTrustSampleApp的名稱。
    
  
5. 將專案儲存在您選擇的位置，然後選擇 **[確定]**。
    
  
6. 指定的 SharePoint 開發人員網站的完整 URL。例如， http://TestServer/sites/devsite/
    
  
7. 選取 [ **提供者主控**] 選項，然後選擇 [ **下一步**] 按鈕。
    
  
8. 如果系統提示您指定的 web 專案類型，如本主題延續範例選取 **ASP.NET Web 表單應用程式**，然後選擇 [ **下一步**] 按鈕。
    
  
9. 精靈的 [ **設定驗證設定**] 頁面隨即開啟。您新增到此表單的值將會自動新增至 web.config 檔案。下 **您希望增益集來驗證?**，選擇 [ **使用的憑證**。
    
  
10. 按一下 [ **瀏覽**] 按鈕 **憑證位置**] 方塊中，瀏覽至您建立 (C:\\Certs) 的自我簽署憑證 (.pfx 檔案) 的位置。此欄位的值應該是C:\\Certs\\HighTrustSampleCert.pfx的完整路徑。
    
  
11. 在 [ **密碼**] 方塊中輸入此憑證的密碼。在此例中是"password"。
    
  
12. 在 [ **簽發者識別碼**] 方塊中輸入簽發者識別碼 ( `11111111-1111-1111-1111-111111111111`)。
    
  
13. 選擇 [ **完成**]。有多少組態完畢時解決方案會隨即開啟。Visual Studio解決方案，另一個適用於SharePoint Add-in和其他ASP.NET web 應用程式中建立兩個專案。
    
  

### 若要執行及偵錯增益集


1. Office Developer Tools for Visual Studio自動產生 default.aspx 和 default.aspx.cs 檔案時建立ASP.NET專案。產生的程式碼會擷取 SharePoint 主機網頁的標題與列印的遠端 web 應用程式的預設頁面上。實際標記及這些檔案中的程式碼而異工具的版本。本主題中，您可以使用產生的 default.aspx 並不需要修改 default.aspx.cs 檔案。
    
  
2. 若要測試SharePoint Add-in和其遠端 web 應用程式，請按 **F5**Visual Studio中。Web 應用程式在 *localhost*  要部署至 IIS Express。SharePoint Add-in將會安裝至目標 SharePoint 網站。將會提示您 SharePoint 所授與權限SharePoint Add-in要求。一些舊版Office Developer Tools for Visual Studio會立即啟動增益集、 其他人將會開啟 [ **網站內容**] 頁面上的目標 SharePoint 網站和您會看見新增益集提列。
    
    如果未自動啟動增益集] 中啟動]。遠端 web 應用程式指定為 AppManifest.xml 檔案 (這是 Default.aspx) 中的 **[開始] 頁面**的頁面隨即開啟。增益集應該類似圖 4。
    

   **圖 4。範例增益集呼叫 SharePoint Server 並擷取 SharePoint 主機網頁的標題。**

  

     ![Sample app retrieving web title](images/SP15HighTrustLaunchNew.gif)
  

  

  

## 完成偵錯與發出的網域或商業憑證
<a name="NewCertificate"> </a>

您剛才Windows PowerShell指令碼關閉遠端 web 應用程式使用 HTTPS 通訊協定存取 SharePoint SharePoint 的一般需求。使用 HTTPS 關閉可能會造成您身為開發人員錯過特定問題時建立增益集就會發生在實際執行部署期間需要 HTTPS 的位置。據以，則不應考慮開發和偵錯階段完成直到您已具備測試憑證取代為網域發出或商業憑證，然後這與開啟的 HTTPS 需求的 [增益集。
  
    
    
當您取得新的憑證時，您需要將密碼新增至，如果它已經不會有一個。在實際執行環境中，您可以使用強式密碼，但適用於偵錯SharePoint Add-in，您可以使用的任何項目。您將需要兩個格式、 pfx 及 cer 中的憑證。如果它不 pfx 格式當您取得時，您可能需要將其轉換成 pfx 使用公用程式。當您有 pfx 檔時，您可以匯入到 IIS 並匯出 cer 檔案中的下列程序所述。
  
    
    

### 若要匯入新的憑證


1. 將.pfx 檔案置於 C:\\Certs SharePoint 伺服器上。基於本文的詳細資訊，我們假設檔案命名為 _MyCert_.pfx。您應該取代為"MyCert"中所有的這些指示實際名稱的憑證。
    
  
2. 在 IIS 管理員中，選取 [在左邊的樹狀檢視中的 [  _ServerName_ ] 節點。
    
  
3. 按兩下 [ **伺服器憑證**] 圖示。
    
  
4. 在右邊的 [ **動作**] 窗格中選取 [ **匯入**。
    
  
5. 在 [ **匯入憑證**] 對話方塊中，瀏覽至 C:\\Certs\\ _MyCert_.pfx，使用 [瀏覽] 按鈕，然後輸入憑證的密碼。
    
  
6. 請確定已啟用 [ **允許匯出此憑證**並按一下 [ **確定]**。
    
  
7. 在 [ **伺服器憑證**] 清單中憑證上按一下滑鼠右鍵，然後選取 **匯出**上方圖 3 所示。
    
  
8. 將檔案匯出至 C:\\Certs 並指定其密碼。
    
  
9. 如果您的測試 SharePoint 安裝不相同Visual Studio執行所在的電腦上，將 _MyCert_.pfx 檔案移至 C:\\Certs 資料夾Visual Studio電腦上。
    
  
10. 在 [ **伺服器憑證**] 檢視中，按兩下 _MyCert_顯示憑證的詳細資訊。
    
  
11. 在 [ **詳細資料**] 索引標籤上選擇 [以啟動 **憑證匯出精靈**] 的 [ **複製到檔案**，然後選擇 [ **下一步**。
    
  
12. 使用預設值 [ **否，不要匯出私密金鑰**]，然後選擇 [ **下一步**。
    
  
13. 使用預設值。選擇 [ **下一步**]。
    
  
14. 選擇 [ **瀏覽**、 瀏覽至 C:\\Certs、 憑證 _MyCert_名稱，然後選擇 **儲存**。憑證會儲存為.cer 檔。
    
  
15. 選擇 [下一步]。
    
  
16. 選擇 [ **完成**]。
    
  

### 若要設定SharePoint 2013以使用新的憑證


1. 開啟要編輯 HighTrustConfig ForDebugOnly.ps1file 並進行下列變更：
    
1. 取代 _MyCert_ `HighTrustSampleCert`在出現的位置這兩個位置。
    
  
2. 以特定簽發者識別碼 `11111111-1111-1111-1111-111111111111`、 取代 `22222222-2222-2222-2222-222222222222`。
    
  
3. 以"高信任範例 Cert"取代"My Cert"或一些其他適當的易記名稱。
    
  
4. 在線條 `$serviceConfig.AllowOAuthOverHttp = $true` `true`取代為 `false`。這會開啟後要使用 HTTPS 的需求。
    
  
2. 儲存該文件。
    
  
3. 開啟 **SharePoint 管理命令介面**以系統管理員身分並執行之檔案的下一行：
    
  ```
  ./HighTrustConfig-ForDebugOnly.ps1
  ```


### 若要重新設定之遠端 web 應用程式


1. 在Visual Studio，開啟 web.config 檔案的 web 應用程式專案並進行下列變更：
    
1.  `ClientSigningCertificatePath`機碼中取代 `C:\\Certs\\` _MyCert_ `.pfx` `C:\\Certs\\HighTrustSampleCert.pfx` 。
    
  
2.  `ClientSigningCertificatePassword`機碼值取代為憑證的實際的密碼。
    
  
3. 取代 `22222222-2222-2222-2222-222222222222` `IssuerId`機碼值。
    
  
2. 按 **F5**偵錯增益集。
    
  
當您完成高信任層級增益集，如需如何以指示的核取 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)開發封裝和發佈SharePoint Add-in這種。
  
    
    

## TokenHelper 和 SharePointContext 檔案怎麼辦？
<a name="TokenHelper"> </a>

Office Developer Tools for Visual Studio包含 TokenHelper.cs (或.vb) 檔案中的遠端 web 應用程式。工具的一些版本也包含 SharePointContext.cs (或.vb) 檔案。這些檔案中的程式碼會執行下列動作：
  
    
    

- 設定信任的憑證時網路呼叫.NET。
    
  
- 擷取指定的 **WindowsIdentity**物件代表的遠端 web 應用程式的私人憑證簽署和SharePoint 2013用來建立信任的伺服器對伺服器存取 token。
    
  
- 取得 SharePoint security token service (STS) 憑證。
    
  
- 在增益集使用低信任層級而不是高信任授權系統，這些檔案會有其他工作，例如處理 OAuth 權杖 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)所述的案例。該案例超出本文範圍。
    
  
如需 TokenHelper 和 SharePointContext 的詳細資訊，請參閱檔案中的註解。
  
    
    
高信任層級增益集，沒有任何內容權杖。內容權杖是專屬於使用低信任層級 authorizaton 的設定。不過，存取權杖則仍需要。如果您使用的高信任層級設定，您的 web 應用程式具有如同SharePoint未驗證使用者 (亦即增益集是負責建立存取權杖包括使用者與身分識別提供者的識別碼)。
  
    
    
當您正在偵錯中Visual Studio與 **F5**時、 Microsoft Office Developer Tools for Visual Studio要使用 Windows 驗證和兩個產生的程式碼檔案使用執行增益集之人員的 Windows 身分識別建立存取權杖。增益集發佈時，您需要設定之遠端 web 應用程式在 IIS 管理員中使用 Windows 驗證如果您想要使用不需要修改兩個產生的檔案。如果增益集將會不使用 Windows 驗證在實際執行環境，您必須自訂產生的程式碼檔案、 TokenHelper 和/或 SharePointContext，使用不同的驗證系統。如果您的遠端 web 應用程式中不會執行SharePoint Add-in的使用者身分識別存取 SharePoint，也會自訂這些檔案。最後，若遠端 web 應用程式 PHP、 node.js、 Java 或其他非 ASP.NET 平台，則您的程式碼必須以取得使用者的識別碼從列傳驗證系統使用，然後再新增的識別碼存取 token 到其結構。如需詳細資訊，請參閱 [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)、  [建立及使用存取權杖中提供者主控高信任 SharePoint 增益集](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)及 [使用 SharePoint 增益集使用 SAML 與 FBA Sites in SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [封裝並發佈高信任 SharePoint 增益集](package-and-publish-high-trust-sharepoint-add-ins.md)
    
  
-  [疑難排解秘訣高信任的增益集在 SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)
    
  
-  [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md)
    
  
-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [公告新 SharePointContext helper 在 sharepoint2013 增益集](http://blogs.msdn.com/b/officeapps/archive/2013/11/07/announcing-the-new-sharepointcontext-helper-in-apps-for-sharepoint-2013.aspx)
    
  
-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

