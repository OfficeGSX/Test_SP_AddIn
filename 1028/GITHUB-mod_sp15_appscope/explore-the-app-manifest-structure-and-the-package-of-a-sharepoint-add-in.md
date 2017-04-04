---
title: 探索應用程式資訊清單結構和套件的 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# 探索應用程式資訊清單結構和套件的 SharePoint 增益集
了解增益集套件結構和SharePoint Add-in的資訊清單檔案。
## SharePoint 套件結構的增益集
<a name="Package"> </a>

SharePoint Add-in套件為可遵守 [開放封裝慣例 (OPC)](http://msdn.microsoft.com/en-us/magazine/cc163372.aspx)並已".app"副檔名的檔案。套件包含下列項目：
  
    
    

- **增益集資訊清單：**這是名為 appmanifest.xml 的必要的檔案。它會告訴SharePoint 2013增益集，例如其標題和執行所需要的權限的一些重要屬性。此檔案的內容的相關詳細資訊，請參閱 [SharePoint 資訊清單檔案的增益集](#AppManifest)。
    
  
- **SharePoint 方案封裝:**(選用) 增益集可能包括包含增益集 web 元件的 SharePoint 方案套件 (.wsp 檔)。這些元件可能包括頁面、 清單執行個體、 檢視、 文件、 **Web**-範圍功能及其他SharePoint 2013元件。(如需可以SharePoint Add-in中包含哪些 SharePoint 元件的詳細資訊，請參閱 [類型的 SharePoint 元件可在 SharePoint 增益集](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps))。.Wsp 檔案可能也會包含Office 增益集。.Wsp 檔案的元件部署至增益集的網站。包含 SharePoint 方案套件的增益集套件的範例，請參閱 [建立提供者主控增益集包含的自訂 SharePoint 清單與內容類型](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md)。
    
  
- **主控 web 功能與自訂動作或增益集組件：**部署至增益集 web SharePoint 2013元件，除了SharePoint Add-in也可以部署一個以上的自訂動作 (快顯功能表項目或功能區擴充功能) 到主機網站。這被藉由包括增益集套件中不是這樣的套件.wsp 檔案內和的部署會移至主機 web 元件的功能。此 「 寬鬆"功能稱為主機 web 功能。增益集組件會部署到主機網站相同的方式。主機 web 功能是由標準SharePoint 2013 feature.xml 檔案以及一或多個相關聯的 elements.xml 檔案所組成。Elements.xml 檔案自訂動作，例如，包含自訂動作的 **CustomAction**標記。它也可以包含增益集組件的標記。僅限這兩種元件可以在主機網站功能。這些主控的 web 功能不會分項增益集資訊清單中。不過，他們"組件 」 中 OPC 有意義且沒有明確 OPC 關係的增益集資訊清單和每個這些檔案。如需包含主機 web 功能的增益集套件的範例，請參閱 [建立部署與 SharePoint 增益集的自訂動作](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)。
    
    > [!注意事項]
      > 租用戶系統管理員可以選擇要安裝多個網站SharePoint Add-in批次。增益集已安裝以這種方式是說有 **Tenant**範圍。如果增益集尚未安裝批次，而每個網站分開安裝它具有 **Web**範圍。若主機 web 功能包括功能區擴充功能或增益集組件、 未部署至主機 web 如果增益集是批次安裝，因此唯一的快顯功能表項目時部署的租用戶範圍增益集增益集範圍不應混淆功能範圍。功能範圍決定之部署的功能中的項目。各種可能性是 **Farm**、 **WebApplication**、 **Site** (也就是網站集合) 及 **Web**。僅限 **Web**選項是SharePoint Add-ins (兩者裝載 web 功能和功能內的增益集套件.wsp) 中允許的功能。增益集範圍是指的增益集已安裝的範圍。各種可能性是 **Web**，在增益集的大小寫具有一或多個網站的網站，並 **Tenant**，在其中增益集的大小寫已安裝網站的所有或部分子集客戶的租用中的批次安裝。如需 **Tenant**與 **Web**範圍的詳細資訊，請參閱 [租用和部署範圍的 SharePoint 增益集](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。
- **當地語系化的資源檔案 (.resx)：**這些是針對當地語系化的增益集資訊清單包含增益集的層面標題和主機的層面 web 功能增益集套件中。(增益集套件的每個自己套件，例如.wsp 檔案、 Azure 網站與add-in資訊清單內的個別部分可以完全相同他們就是有問題的項目不屬SharePoint Add-in一部分套用自己當地語系化程序)。例如包括.resx 檔案的主機 web 功能的增益集套件中，請參閱 [找出 SharePoint 增益集](localize-sharepoint-add-ins.md)。
    
  
- **Office 增益集資訊清單：** (選用) 可能會有一或多個Office 增益集資訊清單的每個封裝Office 增益集。只有當增益集移至要上傳至SharePoint 2013公司增益集目錄、 不公用市集此組件可以包含在增益集套件。請參閱 [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)如需詳細資訊。
    
  

## SharePoint 資訊清單檔案的增益集
<a name="AppManifest"> </a>

每個SharePoint Add-in包含 appmanifest.xml 檔案。Appmanifest.xml 會告知 SharePoint 它必須了解增益集和定義增益集最重要的屬性。以下是一些資訊清單中指定的項目：
  
    
    

- 內部名稱、 產品識別碼及版本的增益集。
    
  
- [開始] 頁面即會開啟時啟動增益集] 頁面的 URL。這可以是在增益集 web 頁面、 雲端式頁面或 ISV 網頁伺服器上的頁面。
    
    > [!注意事項]
      > 在某些情況下，可能會有可 **StartPage**元素中指定的檔案類型的限制。如需詳細資訊，請參閱 [首頁元素 (PropertiesDefinition complexType) (SharePoint 增益集資訊清單)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)。> 當您會結合 **StartPage**值中的多個查詢參數時，您必須使用而不是" `&amp;`"或分號分隔的編碼的 &amp;" `&amp;amp;`"一起附加它們。
- 增益集的其他屬性。這些包括標題和支援的增益集 (兩者都是必要的)] 中的處理後續安裝的服務 Url 的地區設定升級後前解除安裝事件及建立增益集網頁時所使用的網站範本。
    
  
- 如需權限要求增益集必須增益集網路外部的 SharePoint 資源。
    
  
- 為了驗證與授權、 增益集主體的識別碼。它是本主體的權限。這不是必要的 SharePoint 裝載增益集。
    
  
- 先決條件，如果有的話，必須有人能夠讓增益集安裝增益集清單。例如，某些功能可能需要安裝及啟動，與特定服務可能需要授權和安裝。
    
  

> [!注意事項]
> 增益集資訊清單檔案是在增益集套件的唯一必要項目，但不是所有的前一個清單中的項目所必要的組件的檔案。
  
    
    

如需詳細資訊的增益集清單標記，請參閱節點 [資訊清單的 SharePoint 增益集的結構描述參考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)。本主題不取代者的資訊在該節點包含必要的元素與屬性的相關資訊。此外，請注意 SharePoint 增益集資訊清單有不同的結構描述從Office 增益集資訊清單。您可以在 [Office 增益集的結構描述參考資訊清單 (v1.1 (英文))](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx)尋找後面的相關資訊。
  
    
    
以下是 appmanifest.xml 檔案的範例。請注意在此範例中，[開始] 頁面上的增益集在遠端伺服器不是 SharePoint 網站上的頁面上的 ASP.NET 頁面。頁面的 URL 包含會傳遞至遠端 web 應用程式的主機網站的 URL 的查詢字串。字串"{HostUrl}"部分是解析時啟動增益集的 token。增益集要求中的主機網站的所有清單的寫入權限。必須授與此權限的增益集主體是遠端 web 應用程式。
  
    
    
您必須使用 **SupportedLocales**或 **SupportedLanguages**元素增益集資訊清單中。 **SupportedLanguages**是用以 **SupportedLocales**為主的方式所取代。 **SupportedLanguages**元素會繼續運作版本，即使之後，但應避免使用它。如需這些元素的詳細資訊請參閱 [SupportedLocales 元素 (PropertiesDefinition complexType) (SharePoint 增益集資訊清單)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx)和 [SupportedLanguages 元素 (PropertiesDefinition complexType) (SharePoint 增益集資訊清單)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx)。
  
    
    

> [!注意事項]
> **AppPermissionRequest**元素 **Scope**屬性值的結構類似 Uri，但他們實際常值字串。在下列範例中的範例 **Scope**值沒有部分是預留位置。如需權限的詳細資訊，請參閱 [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)。
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### 增益集資訊清單中的 URL token

SharePoint 2013提供數種可使用於 **StartPage**元素及增益集及增益集的元件中其他地方代表增益集執行之前沒有已知的資訊的 token。SharePoint 2013基礎結構會解析下列 token。一些可用於開頭的 URL 及其他人可用於 URL 如查詢參數的值。下列 token 與數個其他也可用以各種SharePoint 2013開發內容。相關的所有 token，其中用的詳細資訊，請參閱 [URL 字串及權杖中 SharePoint 增益集](url-strings-and-tokens-in-sharepoint-add-ins.md)。一般SharePoint 2013中其他權杖及 Url 的相關資訊，請參閱 [Url 及 SharePoint 2013 中的權杖](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)。
  
    
    

> [!注意事項]
> **Scope**元素之屬性的 **AppPermissionRequest**中不使用這些權杖。
  
    
    


## 其他資源
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [資訊清單的 SharePoint 增益集的結構描述參考](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [開放式封裝慣例 (OPC)](http://msdn.microsoft.com/en-us/magazine/cc163372.aspx)
    
  
-  [資料層應用程式的連線 (DAC)](http://msdn.microsoft.com/en-us/library/ee210546)
    
  
-  [Web 部署 2.0](http://www.iis.net/download/WebDeploy)
    
  

