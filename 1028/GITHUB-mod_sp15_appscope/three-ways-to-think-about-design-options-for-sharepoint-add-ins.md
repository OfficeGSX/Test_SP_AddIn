---
title: 若要考慮的三種方式設計選項的 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# 若要考慮的三種方式設計選項的 SharePoint 增益集
取得可用來搭配SharePoint Add-ins設計及架構選項的概觀。您應該先熟悉文章 [SharePoint Add-ins](sharepoint-add-ins.md)。
## 概觀 (英文) 與第一個決策
<a name="Overview"> </a>

本文會查看SharePoint Add-ins架構選擇三種不同的方式。首先，了解最重要的設計選擇; 類別第二個;檢視應用程式層; 方面的增益集架構與第三您看到一組您需要進行設計選擇時要考慮的因素。
  
    
    
但進行的第一個決策 SharePoint 延伸模組是否應該是SharePoint Add-in或傳統 SharePoint farm solution或沙箱化解決方案。不可以從用戶端存取 SharePoint 物件模型與自訂 SharePoint 管理和安全性、 主要連線的某些部分。僅限自訂 SharePoint 伺服器上執行的程式碼能予以存取，而SharePoint Add-in中不允許自訂伺服器端程式碼。(一組豐富的用戶端物件模型以及 REST/OData 服務讓可以SharePoint Add-ins執行幾乎任何端使用者導向 SharePoint 延伸模組。)如需決定之間傳統解決方案及增益集的詳細資訊，請參閱 [與 SharePoint 解決方案比較 SharePoint 增益集](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)。也很有幫助這個決策是 [選擇 [設定 SharePoint 2013 中的右 API](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)。
  
    
    

## 在設計的 SharePoint 增益集的主要元素
<a name="MajorCategoriesOfChoices"> </a>

有三個主要類別需要設計SharePoint Add-in時進行的選擇。應用程式的設計成一律涉及取捨;選擇您將一個類別的可能會限制在另一個選項。不是每個可能的組合選擇是合適。
  
    
    

- **主控:** SharePoint Add-ins可以有效地分成兩種主要類型根據其已部署及主控。
    
  - **提供者主控**增益集有其主要資料儲存區和商務邏輯部署及主控您的  開發人員--SharePoint server] 或 [您提供雲端帳戶的外部。您負責強制執行之各種購買增益集的客戶帳戶之間的隔離。這類增益集可以太有 SharePoint 元件。這些都架設在客戶的 SharePoint 伺服器陣列中。此類型的增益集提供其他類別的設計選擇最大的彈性。它也可讓您能夠使用非 Microsoft 平台的外部資料、 邏輯和 web 使用者介面 (UI)。(內的提供者主控增益集的類別，您也需要區分增益集的 SharePoint 伺服器陣列相同的公司防火牆內有其遠端元件以及該防火牆外有其遠端元件。授權系統這兩種案例的不同，它會依次可以讓您用以存取 SharePoint 資料的程式設計語言差異。。 {3})
    
  
  - **SharePoint 主控**的增益集完全包含SharePoint 2013元件，例如清單、 內容類型、 工作流程與網頁組件。沒有外部元件。如需可以SharePoint Add-ins中包含的 SharePoint 元件類型的詳細資訊，請參閱 [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)。
    
  

    如需SharePoint Add-ins代管選項的詳細資訊，請參閱 [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)。
    
  
- **連線：** SharePoint 2013支援三種安全建立/讀取/更新/刪除 (CRUD) 資料的存取權。
    
  - 外部 web 應用程式增益集使用 OAuth 通訊協定來存取 SharePoint 資料。如需詳細資訊，請參閱 [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)。
    
  
  - JavaScript可以存取使用啟用安全的跨網域指令碼特殊JavaScript函式庫，內相同租用 SharePoint 增益集網頁中的資料及其他網站上的資料。如需詳細資訊，請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
    
  
  - SharePoint Add-in也可以存取外部資料透過Business Connectivity Services (BCS)或 web 服務 proxy。如需詳細資訊，請參閱 [Business Connectivity Services in SharePoint 2013](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx)和 [查詢使用 SharePoint 2013 中的 web proxy 遠端服務](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)。
    
  

    資料儲存區與SharePoint Add-ins中的存取的詳細資訊，請參閱 [資料儲存在 SharePoint 增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data)、  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)及 [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md)。
    
  
- **UI：**有三種方法可以出現在 SharePoint 中SharePoint Add-in : 在最低限度下，所有增益集所呈現完整的網頁中。(選用) 增益集可以也會呈現透過增益集組件，以及透過功能表項目] 或 [功能區] 按鈕。如需詳細資訊，請參閱 [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)。
    
    > **注意事項**
      > 多個網站集合至客戶可以安裝SharePoint Add-ins ，租用中或網站的網站為基礎。先前稱為租用戶範圍增益集。如果您想讓租用戶範圍選項客戶，您可能會包含自訂功能區按鈕或增益集組件。如需詳細資訊，請參閱 [租用和部署範圍的 SharePoint 增益集](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。

## 架構的層
<a name="Tiers"> </a>

考量您的增益集架構選項的另一種方式是設想為具有三個邏輯各層的增益集： 使用者介面、 商務邏輯和資料存取權。每個圖層有多個實作選項 ；同樣地，選擇進行一層限制其他人的選項。下表說明一些選項和其用途、 增益集的遠端元件和 SharePoint 元件。
  
    
    

**在主控提供者增益集的遠端元件： 每一層的選項**


|**層**|**選項**|**適用於**|
|:-----|:-----|:-----|
|使用者介面 <br/> |在ASP.NETASP.NET頁面表單或裝載於Microsoft Azure網頁的角色的 MVC 應用程式。 <br/> |運用ASP.NET開發人員的技術。 <br/> |
||HTML 5] 頁面上使用JavaScript。 <br/> |豐富型使用者介面。 <br/> |
||PHP 或其他種類的裝載於非 Microsoft 雲端服務的網頁。 <br/> |將非 Microsoft 應用程式整合至 SharePoint。 <br/> |
||Silverlight在 Windows Phone 應用程式。 <br/> |行動裝置存取 SharePoint 資料和整合地理位置的資料和推入通知。 <br/> |
|商務邏輯 <br/> |用戶端JavaScript。 <br/> |UI 邏輯及淺商務邏輯。 <br/> 透過JavaScript用戶端物件模型存取 SharePoint 資料。 <br/> |
||Microsoft Azure工作者角色。 <br/> |需要大量佔用處理器的功能。 <br/> 透過.NET Framework用戶端物件模型存取 SharePoint 資料。 <br/> |
||遠端 web 服務。 <br/> |需要大量佔用處理器的功能。 <br/> 透過.NET Framework用戶端物件模型存取 SharePoint 資料。 <br/> |
|資料 <br/> |SQL Azure. <br/> |全功能的關聯式資料。 <br/> |
||Microsoft Azure表格儲存體。 <br/> |應用程式設定和其他中繼資料。 <br/> |
||Microsoft Azure blob 存放區。 <br/> |大型檔案的儲存空間。 <br/> |
||非 Microsoft 雲端服務。 <br/> |運用現有的資料來源為基礎的非 Microsoft 平台。 <br/> |
||開發人員自己的伺服器上的資料庫。 <br/> |提供者主控和開發人員控制項的租用隔離。 <br/> |
   

**SharePoint 元件： 每一層的選項**


|**層**|**選項**|**適用於**|
|:-----|:-----|:-----|
|使用者介面 <br/> |自訂增益集網頁上SharePoint 2013清單和文件庫的檢視。 <br/> |最大化整合 SharePoint 外觀與行為。 <br/> |
||架設在網頁組件 (或 < 物件 > 標記中) 的Silverlight應用程式增益集網頁上。 <br/> |運用現有的 Silverlight 開發經驗。 <br/> 豐富型使用者介面。 <br/> |
|商務邏輯 <br/> |SharePoint 工作流程。 <br/> |實作商務程序。 <br/> |
||用戶端JavaScript與 SharePoint 跨網域文件庫的地方自動執行。 <br/> |在增益集 web 存取 SharePoint 資料。 <br/> 在租用中之其他網站的存取資料。 <br/> |
||遠端事件處理常式。 <br/> |處理 CRUD 事件的 SharePoint 清單和文件庫使用外部架設邏輯。 <br/> |
|資料 <br/> |SharePoint 2013清單和文件庫透過Collaborative Application Markup Language (CAML)或 LINQ，查詢其中一個 SharePoint 用戶端物件模型的查詢。 <br/> |運用現有的 SharePoint 和.NET Framework開發經驗。 <br/> |
||SharePoint 2013清單和文件庫透過 SharePoint REST/OData web 服務查詢。 <br/> |從非 Microsoft 平台存取 SharePoint 資料。 <br/> 運用現有的 OData 查詢經驗。 <br/> |
||BCS 模型。 <br/> |將 SharePoint 中的外部資料呈現為 SharePoint 清單。 <br/> |
   

## 讓您的設計決策時要考慮的因素
<a name="DecisionFactors"> </a>

SharePoint Add-in模型可讓設計簡單的決策樹即無法再太可能性。以下是一些建構SharePoint Add-in的架構時所應考量的最重要因素。
  
    
    

- 最重要的是當然，是您想要提供給客戶的功能  使用案例。例如，如果增益集包括需要大量佔用處理器的功能，例如視訊檔案轉換成另一個視訊格式，，就是在其中一部伺服器或Microsoft Azure工作者角色上建立裝載提供者增益集處理完畢的引數。
    
  
- 因為SharePoint Add-in、 提供者主控增益集的一種需要您 (或您的客戶) 來裝載非 SharePoint 元件和強制執行租用戶隔離，您需要考量您擁有的硬體和 IT 人員若要這樣做 (或您的目標的客戶是否執行)。
    
  
- 您要採用哪個客戶十分重要的考量。如果將係用於所有增益集 (也就是您有任何外部客戶) 或都使用某一個客戶、 提供者主控的增益集已明顯更容易實作及維護比有外部客戶時或多個客戶將使用中之增益集。如果您想要公開買賣增益集，您也應該考量是否會上市以擁有SharePoint Online帳戶或出其自己的 SharePoint 伺服器陣列，或兩者的企業。
    
  
- 您也應該考量您現有的技能或開發人員的技術。例如，如果您是有經驗的ASP.NET開發人員的就是以建立遠端 web 應用程式並將 SharePoint 清單資料呈現在ASP.NET ] 頁面上的點。換句話說，如果您是有經驗的 SharePoint 開發人員，那就是以自訂 SharePoint 清單與網站] 頁面上，使用 JavaScript 執行處理程序的點。
    
  

## 其他資源
<a name="AdditionalResources"> </a>


-  [設計 SharePoint 增益集](design-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

