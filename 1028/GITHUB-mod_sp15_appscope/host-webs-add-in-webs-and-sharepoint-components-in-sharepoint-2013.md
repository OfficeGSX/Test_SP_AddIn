---
title: 主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件
ms.prod: SHAREPOINT
ms.assetid: b791cdf5-8aa2-47fa-bc4c-aee437354759
---


# 主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件
了解主機 web 與增益集 web 之間的差別。也找出哪些SharePoint 2013元件還包含在SharePoint Add-in、 其部署至主機網頁、 其部署至增益集 web 及增益集 web 隔離網域中的部署方式。
## 主機 web、 增益集 web 及隔離的網域
<a name="IsolatedDomain"> </a>

增益集包括 SharePoint 元件會安裝在網站上，它會列在從中啟動 [ **網站內容**] 頁面。雖然某些其他事項可以選擇性地新增，例如自訂動作或增益集組件清單，也就是啟動點的增益集，是唯一需要新增至網站。如需這些選項的資訊，請參閱 [從使用者介面存取增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#AccessingApp)。這些 UI 項目之外的SharePoint Add-in元件和內容，例如清單、 內容類型、 工作流程與頁面、 部署至特殊的隔離網域以不同的網站。此事實主要是從使用者隱藏的。特殊網站的增益集是 *部署*  會呼叫增益集網頁。要增益集已 *安裝*  的網站會呼叫主機網頁。雖然增益集 web 有其專屬隔離的網域，其位於主機 web 相同網站集合中。(此規則的一項例外狀況為增益集安裝的租用戶範圍。在該情況下，增益集 web 是網站集合的公司增益集目錄中)。
  
    
    
圖 1 顯示兩個SharePoint Add-ins安裝主機網頁。增益集 1 具有遠端元件，但沒有 SharePoint 元件，讓它具有沒有增益集 (英文) 網站。增益集 2 沒有遠端元件，但有兩個 SharePoint 清單及工作流程。這些已部署至隔離的子網站。( SharePoint Add-in可以有遠端和 SharePoint 主控的元件，但未增益集在此圖表中有兩)。
  
    
    

**圖 1： 主機網頁與主控提供者增益集和 SharePoint 主控增益集 (英文)**

  
    
    

  
    
    
![Host web, app web, and their components.](images/HostWebAndAppWeb.png)
  
    
    
例如，假設下列 url 的主機網站上已安裝增益集] 中與 SharePoint 元件超過剛可以部署至主機 web UI 項目：
  
    
    
 `https://www.fabrikam.com/sites/Marketing`
  
    
    
SharePoint Add-in會部署至新建立的網站具有類似如下的 URL：
  
    
    
 `http://add-in-bdf2016ea7dacb.fabrikamadd-ins.com/sites/Marketing/Scheduler`
  
    
    
請注意此 URL 結構如下：
  
    
    
 `https://` _Add-in_Prefix_ `-` _Add-in_ID_ `.` _Add-in_Base_Domain_ `/` _Domain_Relative_URL_of_Host_Web_ `/` _Add-in_Name_
  
    
    
版面配置區的定義如下：
  
    
    

-  _Add-in_Prefix_是在管理中心的伺服器陣列管理員所設定的任何字串。預設值為"default"。系統管理員已在此範例會變更此選項以"增益集。 」
    
  
-  _Add-in_ID_是內部產生時安裝增益集的十六進位數字。
    
  
-  _Add-in_Base_Domain_為伺服器陣列管理員在管理中心內或SharePoint Management Shell所設定的任何字串。這應該 *不*  是設為子網域的 SharePoint web 應用程式或增益集隔離的目的是主要失敗。在這個範例中，管理員已移除"www。 然後"增益集 」 加入公司名稱。讓 `fabrikamadd-ins.com`為增益集基底網域。
    
  
-  _Domain_Relative_URL_of_Host_Web_是相對 URL 的上層主機網站，在此案例的 `sites/Marketing`。
    
  
-  _Add-in_Name_是 **Name**元素之屬性的 **App** appmanifest.xml 檔案中的值。
    
  
為什麼要選擇 SharePoint 元件部署到增益集的兩個主要原因有 web，而不是主機網頁。兩者都是與安全性相關。
  
    
    

- **增益集的權限的強制執行:**在SharePoint 增益集相關的模型、 增益集有其專屬的身分識別與已定義均不一定是相同的使用者執行增益集的權限的權限。當增益集已安裝並授與所安裝增益集] 中的人員只要人員具有所有的增益集要求的權限要求這些增益集的權限。(如果已安裝的增益集的使用者不具有所要求的增益集的所有權限，使用者無法安裝增益集。)由提供每個增益集自己的網域， SharePoint 2013可靠識別增益集所做的要求及驗證的增益集的權限。如需增益集的權限的詳細資訊，請參閱 [增益集的權限](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#AppPermissions)。
    
  
- **跨網域指令碼安全性：**現代瀏覽器支援"相同來源原則 」 就JavaScript方法呼叫。透過部署每個SharePoint Add-in自己的網域，SharePoint 會利用瀏覽器的相同來源原則，以確保該JavaScriptSharePoint Add-in中無法執行任何JavaScript從任何其他網域，包括安裝的網域中，從使用者的觀點增益集。
    
    SharePoint 也會提供一種安全地克服之原則的限制。總而言之，這可讓SharePoint Add-in遠端元件至查詢的資料從一般父租用的主機和增益集 web 中的任何網站。如需詳細資訊，請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
    
  

## 類型的 SharePoint 元件可在 SharePoint 增益集
<a name="TypesOfSPComponentsInApps"> </a>

一般而言， SharePoint Add-in可以包含一或多個下列清單中的元件。有某些例外，這些元件必須部署在 **Web**-範圍內的 SharePoint 方案套件 (.wsp) 檔案的功能：
  
    
    

> **注意事項**
>  *在本文稍後的 [部署 SharePoint 元件的警告](#SpecialCases)] 區段中的更詳細地討論標示星號 (*) 的元件。
  
    
    


- 功能 ( **Web**-僅限範圍)
    
  
- 自訂動作 (包括快顯功能表項目及功能區自訂) *
    
  
- 遠端事件接收器 *
    
  
- 標記參照網頁組件，包括增益集組件所包含的 SharePoint (但未自訂網頁組件) *
    
  
- SharePoint 頁面所使用的自訂階層式樣式表 (CSS) 檔案
    
  
- SharePoint 頁面所使用的自訂JavaScript檔案
    
  
- 模組 (集的檔案)
    
  
- Pages
    
  
- 清單範本
    
  
- 清單與文件庫的執行個體
    
  
- 自訂清單表單
    
  
- 自訂清單檢視
    
  
- 自訂內容類型
    
  
- (的內建 SharePoint 的欄位類型) 的欄位
    
  
- Microsoft Business Connectivity Services (BCS)模型 ( **Web**-僅限範圍)、 模型，為基礎的外部內容類型及外部清單的使用內容類型 *
    
  
- 工作流程 *
    
  
- 屬性袋
    
  
- 網頁範本 (但不是網站定義) *
    
  
沒有其他種類的 SharePoint 元件可以部署在SharePoint Add-in。如需功能還包含在SharePoint Add-in限制的相關詳細資訊，請參閱 [與 SharePoint 解決方案比較 SharePoint 增益集](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)。
  
    
    

## 部署 SharePoint 元件的警告
<a name="SpecialCases"> </a>

以下是一些警告及詳細資訊與相關的特定類型的增益集的 SharePoint 元件的部署：
  
    
    

- **自訂動作:**除了增益集網頁中加入自訂動作，您可以將其新增至的主機網站。若要新增的增益集網站自訂動作，您納入它 **Web**-將包括您加入至增益集的網站的任何其他元件一樣範圍內.wsp 檔案的功能。若要將自訂動作新增到主機網站，您可以包含 (即使在外部根據增益集) **CustomAction**標記中為增益集套件中但超出任何.wsp 檔案的功能。在 「 寬鬆 」 功能的元件適用於主機 web 不增益集 web 因此這種類型的功能稱為主機 web 功能。
    
  
- **網頁組件：**一種網頁組件、 增益集組件，可以部署在增益集，並增益集組件可以移至 [增益集 web 或主機網頁。所有其他類型的網頁組件可以參照在增益集，但尚未部署由其。如果增益集組件部署至主機網站，應該包含在主機網站功能。
    
  
- **的遠端事件接收器:**這些是SharePoint 2013的新功能。它們與傳統 SharePoint 事件接收器類似，除了在雲端中執行的程式碼。這些不提供 SharePoint 主控增益集。
    
  
- **工作流程：**SharePoint 2013中的工作流程使用Microsoft Azure-主控新SharePoint 2013功能的工作流程執行階段。使用 SharePoint 主控的工作流程執行階段的編碼工作流程無法併入SharePoint Add-in。允許僅限宣告式工作流程或使用較新的執行階段的工作流程。
    
  
- **Microsoft Business Connectivity Services (BCS)模型、 外部內容類型及外部清單：**Business Data Connectivity (BDC) service模型通常有是寬度大於網站集合的範圍。不過，當Business Data Connectivity (BDC) service模型部署增益集，其範圍僅限於增益集 web。當Business Data Connectivity (BDC) service模型包含在增益集時，則不會儲存Business Data Connectivity (BDC) service共用的服務存放區中。而是它會儲存為增益集 web 中的檔案。
    
  
- **網頁範本：**在大多數情況下，您需要產生新內建的網站定義設定 **APP#0**，這最適合增益集 web 增益集網頁。(如需它的詳細資訊，請參閱 [從使用者介面存取增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#AccessingApp))。SharePoint 2013會自動使用 **APP#0**時的增益集套件不包括 [WebTemplate](http://msdn.microsoft.com/library/ff4ba91a-cc5f-47ff-9101-a7651f452185%28Office.15%29.aspx)元素。
    
    您也可以定義增益集 web 的自訂網站類型。有執行這兩個主要步驟：
    
  - 自訂 [WebTemplate 元素 (網站範本)](http://msdn.microsoft.com/library/ff4ba91a-cc5f-47ff-9101-a7651f452185%28Office.15%29.aspx)、 onet.xml 檔案與可能的其他相關的檔案中包含的增益集 web 功能在增益集部署內的增益集套件.wsp 檔案的網頁範圍功能中的網站範本像平常一樣。
    
  
  - 新增至增益集資訊清單的 [WebTemplate 元素 (PropertiesDefinition complexType) (SharePoint 增益集資訊清單)](http://msdn.microsoft.com/library/62302903-e97a-a9a3-a64e-13176a7c4e1e%28Office.15%29.aspx) **Properties**元素的子並將其 **Id**屬性設定為 GUID 增益集功能和 [WebTemplate 元素 (網站範本)](http://msdn.microsoft.com/library/ff4ba91a-cc5f-47ff-9101-a7651f452185%28Office.15%29.aspx) **Name**屬性值。請注意 GUID 必須大寫字和換行中括號"{}"和"#"字元的隔 GUID 與範本的名稱。以下為範例：
    
  ```XML
  
<WebTemplate Id="{81dd4ae5-873b-4759-9838-4ad9c3dd2952}#NewSiteType" />
  ```


    > **注意事項**
      > 增益集資訊清單的新 [WebTemplate](http://msdn.microsoft.com/library/62302903-e97a-a9a3-a64e-13176a7c4e1e%28Office.15%29.aspx)元素不 [WebTemplate](http://msdn.microsoft.com/library/ff4ba91a-cc5f-47ff-9101-a7651f452185%28Office.15%29.aspx)元素可以包含在功能中的相同標記。 **WebTemplate**元素可以包含的功能定義一種網站，但增益集資訊清單 **WebTemplate**元素只會識別何種類型的站台能夠使用。如SharePoint Add-in的增益集資訊清單的相關詳細資訊，請參閱 [增益集套件結構](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#SPAppModelArch_Package)。

    > **注意**
      > 請勿 **WebTemplate**元素中的增益集資訊清單來指定任何的內建的 SharePoint 網站定義設定為增益集 web 網站類型。我們不支援使用的內建的網站定義設定 **APP#0**、 以外的任何增益集 web。

    如需網站定義設定及網站範本的詳細資訊，請參閱 [Working with Site Templates and Definitions](http://msdn.microsoft.com/library/1edf6d4d-eddb-4cb5-9034-ed394e8a3e01%28Office.15%29.aspx)。
    
  

## 其他資源
<a name="SP15hostedwebs_bk_addlresources"> </a>


-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [與 SharePoint 解決方案比較 SharePoint 增益集](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)
    
  

  
    
    

