---
title: 使用 Visual Studio 發佈 SharePoint 增益集
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# 使用 Visual Studio 發佈 SharePoint 增益集
瞭解如何使用Microsoft Visual Studio 2013或Visual Studio 2012年發佈您SharePoint Add-in 。如果增益集有相關聯的 web 應用程式，請將其部署第一次。所有SharePoint Add-ins，與您封裝SharePoint Add-in然後再將它發佈。您也可以選擇可以提交增益集要包含在Office 市集上的選擇。
## 必要條件
<a name="Prereq"> </a>


  
    
    

-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284  )
    
    -或者-
    
     [Visual Studio 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30682)然後Office Developer Tools for Visual Studio。若要下載的工具，請參閱 [下載頁面](http://go.microsoft.com/fwlink/?LinkId=234393)上的 「 工具 」。(新的 「 發佈 」 管理員不Visual Studio 2012年或更舊版本中可用。)
    
  
- Microsoft SharePoint 2013
    
  

## 使用Visual Studio 2013發佈
<a name="VS2013"> </a>

如果您提供者主控SharePoint Add-in web 應用程式，請先為其部署檔案。然後，所有SharePoint Add-ins，先封裝SharePoint Add-in ，並將它發佈。
  
    
    

> [!重要]
> 若要確保您的 SharePoint 用戶端識別碼和用戶端私人值取得 web 專案，可讓您存取 SharePoint 資料，請從 **增益集的發佈**頁面將SharePoint Add-in專案發佈的網站內容與發佈。您可以存取此頁面開啟SharePoint Add-in，不 web 應用程式的捷徑功能表的快顯功能表，然後選擇 [ **發佈**] 命令。
  
    
    


### 步驟 1: 部署 web 應用程式

您SharePoint Add-in通常都有相關聯的主機 web 應用程式所需的網頁伺服器部署。如需有關如何使用 [發佈] 精靈的詳細資訊，請參閱 [如何: 部署 Visual Studio 中使用上按一下 [發佈 Web 專案](http://msdn.microsoft.com/library/dd465337.aspx)。
  
    
    

### 若要開啟 [發佈您的增益集頁面


- 在 **方案總管]**中，開啟SharePoint Add-in專案的快顯功能表，然後選擇 **發佈**。
    
    出現 **增益集的發佈**頁面。
    
  

### 若要選取或建立設定檔


- 在 **目前的設定檔**] 清單中，選擇設定檔匯入，或選擇 [ **<...新增 >**建立設定檔]。
    
    發佈設定檔會指定要部署 web 應用程式的伺服器所需的登入伺服器、 部署 (如果適用的話)、 資料庫及其他部署選項的認證。您可以建立不同發佈符合您需求的設定檔。例如，您可能會建立一個設定檔，以進行測試和發佈的另一個設定檔。
    
    如果您選擇 **<...新增 >**， **建立發佈的設定檔**精靈] 隨即出現。您可以使用此精靈來發佈的設定檔匯入的網站主機服務提供者，例如Azure，或建立設定檔，然後手動新增您的伺服器名稱、 認證和其他設定。如果您建立新的設定檔，而不要匯入現有的設定檔，您需要提供用戶端識別碼] 和 [用戶端私人值 [的指導方針註冊 SharePoint 增益集 2013年](http://msdn.microsoft.com/library/jj687469.aspx)中所述和 [如何: 建立用戶端識別碼和 Microsoft 銷售者儀表板中的機密](http://msdn.microsoft.com/library/office/jj220036.aspx)。
    
    如果您打算要送出到Office 市集您SharePoint Add-in ，請務必使用用戶端識別碼，並在銷售者儀表板中建立的用戶端私人值。您可以使用用戶端識別碼與您在開發階段中，使用 appregnew.aspx 頁面產生的用戶端私人值，但您送出 Office 市集的增益集必須使用用戶端識別碼] 和 [從銷售者儀表板取得的用戶端密碼。此外，應該Azure網站上建立發佈的設定檔，然後將Visual Studio，而不是在 **建立發佈的設定檔**精靈] 中建立的設定檔匯入。當您建立設定檔中Azure時，所有的 [ **連線**] 索引標籤上的設定中所提供您Visual Studio。如需有關如何匯入或建立發佈的設定檔的詳細資訊，請參閱 [建立發佈的設定檔](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile)。
    
    > [!秘訣]
      > 如果您無法直接發佈 web 內容，您可以建立 web 部署套件的系統管理員為您部署至 web]。若要建立部署套件，建立新的設定檔，選擇 [ **連線**] 索引標籤，然後選擇 **網頁部署套件**的 **發佈方式**] 清單中。

### 若要部署 web 應用程式專案


1. 在 **增益集的發佈**頁面，選擇 [ **部署 web 專案**] 按鈕。
    
    **發佈網站**] 對話方塊隨即出現。
    
  
2. 在 [ **連線**及 **設定**] 索引標籤上填寫任何遺漏的值。
    
    若要變更您SharePoint Add-in檔案發佈的方式，或如果增益集會使用外部資料庫，請選擇 [ **設定**] 索引標籤。在中看到 「 設定設定] 索引標籤 」 區段 [如何: 部署 Visual Studio 中使用上按一下 [發佈 Web 專案](http://msdn.microsoft.com/library/dd465337.aspx)。
    
  
3. 若要檢閱的項目會變更的項目部署 web 應用程式時，選擇 [ **預覽**] 索引標籤上的 [ **開始預覽**] 按鈕。
    
  
4. 選擇要部署 web 應用程式專案的 [ **發佈**] 按鈕。
    
  

### 步驟 2: 封裝的增益集


1. 在 **增益集的發佈**頁面，選擇 [ **套件增益集**] 按鈕。
    
    **發佈 Office 和 SharePoint 增益集**精靈隨即出現。
    
  
2. 在 [ **您的網站裝載於何處?**文字] 方塊中，輸入主控內容檔案SharePoint Add-in您網站的 URL。
    
    您必須指定 「 https 」 前置詞的開頭的地址。請參閱 [為什麼我的增益集需要是 SSL 安全?](http://msdn.microsoft.com/library/jj591603#bk_q7)。
    
    > [!注意事項]
      > Azure網站會自動提供 https 端點。如果發佈增益集Office 市集網站上或Office 市集，https 首碼必須開始的地址。不過，如果您內部網站發佈增益集，您可以使用 http 前置字元。

    在 [ **增益集的用戶端 ID 是什麼?** ] 文字方塊中，輸入發佈的設定檔中的用戶端識別碼應該已經會顯示。
    
    如果您已使用的用戶端識別碼的版面配置區的值，重點前，您必須立即新增實際的用戶端識別碼。這項資訊內嵌在.app 套件，並讓您的網站內容與 live 網站上的 SharePoint 通訊。
    
  
3. 選擇 [ **完成**] 按鈕。
    
    Visual Studio會產生所需的發佈您SharePoint Add-in ，然後開啟 [發佈輸出資料夾的檔案。若要瞭解如何安裝增益集，請參閱 [安裝及管理 SharePoint 增益集 2013年](http://technet.microsoft.com/library/fp161232.aspx)。
    
  

### 步驟 3: 發佈您的SharePoint Add-inOffice 市集上

如果您想要送出到Office 市集您SharePoint Add-in ，請執行下列程序。
  
    
    

1. 在 **增益集的發佈**頁面，選擇 [ **瀏覽銷售者儀表板**] 按鈕，然後登入您的 Microsoft 銷售者儀表板帳戶。
    
    請參閱 [送出到 Office 市集的 Office 與 SharePoint 增益集和 Office 365 web 應用程式](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx)。
    
  
2. 選擇 [ **新增新的應用程式**，填入資訊，，再提交增益集以Office 市集。如需詳細資訊，請參閱 [若要送出到 Office 市集的 Office 與 SharePoint 增益集和 Office 365 應用程式使用賣方儀表板](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)。
    
  

## 使用Visual Studio 2012年發佈
<a name="VS2012"> </a>

當您準備好要封裝您SharePoint Add-in時，開啟 [ **發佈 Office 增益集**] 精靈，備妥SharePoint Add-in您發佈的檔案。
  
    
    

### 步驟 1: 封裝SharePoint Add-in


1. 在 **方案總管]**中，開啟SharePoint Add-in專案的快顯功能表，然後選擇 **發佈**。
    
    **發佈 Office 增益集**精靈] 隨即出現。您要封裝的SharePoint Add-in類型會決定精靈中的頁面。如果增益集就會 SharePoint 裝載，會出現 [僅 **摘要**頁面。如果增益集就會提供者主控，也會出現 **設定檔**] 和 [ **裝載**頁面。
    
  
2. 如果您SharePoint Add-in提供者主控，指定發佈的設定檔名稱中 **您想要發佈的設定檔?** ] 清單中，，然後選擇 [ **下一步**] 按鈕。
    
    發佈的設定檔儲存您在 **裝載**頁面中輸入的資訊。
    
  
3. 在 [ **您的網站裝載於何處?** ] 清單中，指定會裝載您SharePoint Add-in的 web 應用程式的 URL。
    
  
4. 在下方的方塊中 **什麼是增益集的身分識別?**與指定的用戶端識別碼與用戶端私人的增益集，然後選擇 [ **下一步**] 按鈕。
    
    請參閱 [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)。
    
  
5. 所有的SharePoint Add-ins類型，請選取 [ **開啟後成功封裝的輸出資料夾**核取方塊，如果尚未選取，，然後選擇 [ **完成**] 按鈕。
    
    Visual Studio會產生所有發佈SharePoint Add-in您所需的檔案。您可以在您的專案輸出資料夾 (例如，  `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`) 的 `app.Publish`資料夾中找到這些檔案。該資料夾 (如果您SharePoint Add-in是以雲端為基礎)，將包含您SharePoint Add-in的.app 檔案和 web 應用程式的多個檔案。所有SharePoint Add-ins都包含是發佈SharePoint Add-in增益集的資訊清單的.app 檔案。提供者主控SharePoint Add-ins也包含發佈主 web 應用程式的檔案。
    
  

### 步驟 2: 發佈 web 應用程式

如果您SharePoint Add-in提供者主控，則通常必須發佈的網頁伺服器所需的相關聯的主機 web 應用程式。Visual Studio產生部署套件和指令碼，以協助您執行這項工作。
  
    
    
部署套件的 web 應用程式專案檔案中包含壓縮 (.zip)  `app.publish`資料夾中。除了.zip 檔，  `app.publish`資料夾會包含下列檔案:
  
    
    


|**File**|**說明**|
|:-----|:-----|
| _ProjectName_。 deploy.cmd <br/> |此檔案是叫用 Web 部署，讓您更輕鬆地可以安裝在命令提示字元套件的命令列批次檔案。 <br/> |
| _ProjectName_。SetParameters.xml <br/> |這個檔案包含當您使用 deploy.cmd 檔案來安裝套件 Web 部署傳遞的參數。Visual Studio封裝設定會決定為指定之每個參數的預設值。如果您想要安裝的多個伺服器的 web 應用程式，並使用不同的設定為每個伺服器的例如，您可以變更這些值。 <br/> |
| _ProjectName_。SourceManifest.xml <br/> |這個檔案包含的Visual Studio傳遞到 Web 部署，且該網站的部署使用建立網頁套件的設定。Web 部署需要此檔案僅建立套件。安裝套件時，無法使用這個檔案。 <br/> |
   
如需逐步指引，請參閱 [如何: 安裝部署套件使用使用 Visual Studio 建立檔案 deploy.cmd](http://go.microsoft.com/fwlink/?LinkID=254954)
  
    
    

### 步驟 3: 發佈您SharePoint Add-in
<a name="Publish"> </a>

若要發佈您SharePoint Add-in，檔案上傳增益集資訊清單 (.app) 的增益集Office 市集、 Office 增益集目錄、 SharePoint、 檔案共用，或 Exchange 目錄。增益集的增益集資訊清單位於 [  `app.publish` ] 資料夾，例如 `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`。如需有關如何發佈您SharePoint Add-in的詳細資訊，請參閱 [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)。
  
    
    

## 其他資源
<a name="Additional"> </a>


-  [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)
    
  
-  [發佈 Office 增益集](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)
    
  

