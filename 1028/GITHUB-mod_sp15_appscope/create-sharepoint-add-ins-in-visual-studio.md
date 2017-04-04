---
title: 在 Visual Studio 中建立 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: f1b9c858-907c-4558-b671-3b488ece40a0
---



# 在 Visual Studio 中建立 SharePoint 增益集
了解如何使用範本專案和專案項目中Visual Studio擬定SharePoint Add-ins 。
您可以使用新範本的專案和專案項目在 **vsnv**擬定SharePoint Add-ins 。
  
    
    


## 專案範本
<a name="SP15Projecttemplates_templates"> </a>

當您使用的專案範本中Visual Studio時，它會建立包含專案項目方案及專案類型所需要的檔案。如果您展開 **Office/SharePoint**節點，然後選擇 [ **增益集**] 節點下的專案範本就會出現在 [ **新增專案**] 對話方塊中。如需 **SharePoint 解決方案**節點下的專案範本資訊，請參閱 ＜  [SharePoint 專案和專案項目範本](http://go.microsoft.com/fwlink/?LinkId=255306)。
  
    
    

### Office Add-in

建立 Office 應用程式，例如 Excel 或 Outlook 區內裝載的網頁。Office 增益集提供其他內容及 Outlook 項目或文件中的功能。如需詳細資訊，請參閱 [Office 增益集平台概觀](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx)。
  
    
    

### SharePoint Add-in

建立依據您在精靈中指定的資訊SharePoint Add-in 。此資訊包含下列資料。
  
    
    

- 增益集的名稱。
    
  
- 適用於偵錯增益集使用本機或遠端 SharePoint 網站。
    
  
- 增益集，您想要建立的類型： 提供者主控或 SharePoint 主控。
    
  
如需詳細資訊，請參閱 [SharePoint Add-ins](sharepoint-add-ins.md)。
  
    
    

### 雲端商務增益集 (英文)

使用Visual Studio的 **雲端商務增益集**範本，您可以建立的 SharePoint 裝載增益集在其行動裝置使用者可以檢視、 新增、 並使用現代、 觸控導向電話與平板電腦等裝置更新遠端位置的資料。如需詳細資訊，請參閱 [建立雲端商務增益集](create-cloud-business-add-ins.md)。
  
    
    

## 專案項目範本
<a name="SP15Projecttemplates_items"> </a>

在您建立的 SharePoint 解決方案之後，您可以使用 [ **Office/SharePoint** ] 節點下的 [ **新增項目**] 對話方塊中會出現下列範本專案項目新增給它。
  
    
    

### Office Add-in

將Office 增益集新增至您SharePoint Add-in。您可以新增的工作窗格增益集、 內容增益集，或郵件增益集。如需詳細資訊，請參閱 [Office 增益集平台概觀](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx)。
  
    
    

### 用戶端網頁組件 (由 Web 主機)

將用戶端網頁組件新增至您SharePoint Add-in。新增用戶端網頁組件，您可以在主機網站的頁面上顯示增益集。此範本包含單一的 Elements.xml 檔案，其屬性定義的用戶端網頁組件的下列項目。
  
    
    


|**內容名稱**|**描述**|
|:-----|:-----|
|ClientWebPart <br/> |會指定名稱、 標題、 描述與用戶端網頁組件的尺寸。 <br/> |
|內容 <br/> |定義轉譯內部用戶端網頁組件頁面的位置。此元素有兩個屬性：  `Type`和 `Src`。 `Type`會指定您建立，例如 HTML 的網頁組件的類型。 `Src`定義內的用戶端網頁組件將會轉譯頁面的位置。範本使用圖樣 _ _PropertyName__ 例如 `Src="~addinWebUrl/Pages/ClientWebPart1.aspx?Property1=_property1_"`參照上的查詢字串的屬性 <br/> 如需詳細資訊，請參閱 [建立增益集組件安裝與您 SharePoint 的增益集](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)。 <br/> |
   

### 內容類型

將內容類型新增至您SharePoint Add-in，類似於舊版 SharePoint 中所使用的內容類型。內容類型是一組的中繼資料、 工作流程與 SharePoint 清單或文件庫中的項目類別的行為。例如，項目是一種清單內容類型。其他類型的清單內容包括宣告、 連絡人及工作，以及它們繼承的項目內容類型。連絡人的內容類型包含 **名字**、 **姓氏**和 **工作職稱**] 之類的欄。
  
    
    
當您將內容類型新增至您SharePoint Add-in時，您會指定新的內容類型繼承的基底內容類型。例如，它可繼承公告、 連絡人、 文件或項目的內容類型。您然後可以使用 **內容類型**設計工具來設定的內容類型和其其他屬性，例如其名稱和其描述資料行。您選擇的值會新增至 `ContentType`和 `FieldRef`元素在 Elements.xml 檔案中。如需詳細資訊，請參閱 [Building Block: SharePoint 2010 Content Types](http://msdn.microsoft.com/library/277dfc42-d9a8-4fae-9ae1-0d202b14674f%28Office.15%29.aspx)。
  
    
    

### 空白項目

將專案項目之空元素新增至您SharePoint Add-in。這個專案項目包含單一檔案、 Elements.xml，用來定義元素的屬性。您通常會使用空的元素定義其Visual Studio不提供範本的項目。
  
    
    

### List

會新增兩個專案項目至您SharePoint Add-in: 清單定義和清單的執行個體。當您將清單新增至 [增益集時，您可以指定要名稱清單中的項目以及是否要建立的空白的清單或根據現有的清單類型的清單。您也可以指定是否可以自訂清單。然後您可以使用 **清單設計工具**設定的欄和檢視的清單及其他屬性，例如清單的名稱和描述。如需清單內容的詳細資訊，請參閱 [ListTemplate 元素 (清單範本)](http://msdn.microsoft.com/library/e565ead9-adcb-4a90-97e3-04850719420a%28Office.15%29.aspx)和 [ListInstance 元素 (清單執行個體)](http://msdn.microsoft.com/library/cfefe8e5-2656-4d71-bb4e-5f991a800598%28Office.15%29.aspx)。
  
    
    

### 功能表項目的自訂動作

將透過將巨集指令新增至清單功能表延伸其主機網站的使用者介面專案項目。功能表的自訂動作包含 Elements.xml 檔案中，讓您用來定義動作的屬性。如需詳細資訊，請參閱 [建立部署與 SharePoint 增益集的自訂動作](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)。
  
    
    

### 豪華色系

將模組專案項目新增至您SharePoint Add-in。模組基本上是您可以使用其他檔案時所要包含在部署您SharePoint Add-in的容器。若要新增的檔案，您必須在 [ **方案總管**] 模組底下的專案複製它。檔案參照會自動新增至模組的 Elements.xml 檔案並參照指定的路徑與新檔案的 URL。您可以刪除已包含模組的因為它只例如已包含目的 Sample.txt 檔案。
  
    
    

### 遠端事件接收器

新增專案項目的遠端事件接收器SharePoint Add-in您及您的解決方案至 web 應用程式專案如果這類專案已不存在。Web 應用程式包含與您SharePoint Add-in的遠端事件接收器具有相關聯的 web 服務。Web 服務包含清單、 清單項目或網頁項目事件發生在SharePoint Add-in時，其程式碼會執行 Visual Basic 或 Visual C# 程式碼檔案。如果 web 應用程式具有關聯SharePoint Add-in、 和 web 服務新增至該應用程式。如需詳細資訊，請參閱 [處理事件中 SharePoint 增益集](handle-events-in-sharepoint-add-ins.md)。
  
    
    

### Ribbon custom action

新增延伸其主機網站的使用者介面新增動作至功能區的專案項目。功能區自訂動作包含 Elements.xml 檔案中，定義動作的屬性。如需詳細資訊，請參閱 [建立部署與 SharePoint 增益集的自訂動作](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)。
  
    
    

### 搜尋設定

將可讓您從 SharePoint 網站都已匯出的自訂搜尋組態設定匯入專案項目。
  
    
    

### 網站欄

將專案項目網站欄新增至您SharePoint Add-in。網站欄包含定義的 [網站] 欄中，包括下列資料 `Field`屬性 Elements.xml 檔案。
  
    
    


|**內容名稱**|**描述**|
|:-----|:-----|
|識別碼 <br/> |網站欄的唯一 GUID 值。 <br/> |
|名稱 <br/> |用來參照網站欄的唯一名稱。 <br/> |
|DisplayName <br/> |在 UI 中顯示的易記名稱。 <br/> |
|類型 <br/> |**SPFieldType**，例如布林值、 查閱或文字為基礎的網站欄資料類型。 <br/> |
|必要 <br/> |如果 [] 欄中，屬性設為 **True**;否則，屬性是設定為 **False**。 <br/> |
|群組 <br/> |會指定對其指派 [網站] 欄的群組名稱。此屬性的預設值為 **自訂的網站欄**。 <br/> |
   
如需詳細資訊，請參閱 [Building Block: Columns and Field Types](http://msdn.microsoft.com/library/58548ade-e439-4931-82a2-fa29bd5afdb7%28Office.15%29.aspx)。
  
    
    

### 工作流程

加入您SharePoint Add-inMicrosoft Azure工作流程專案項目。如需詳細資訊，請參閱 [在 SharePoint 2013 中的工作流程](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx)。新增這種類型的項目之後，您會指定工作流程以及它是否為清單或網站的工作流程的名稱。名稱建議清單工作流程僅適用於清單和網站工作流程僅適用於 SharePoint 網站。當您建立工作流程時，您也指定是否要自動建立關聯的工作流程清單和文件庫如果要哪些。您新增每個關聯中，它的檔案新增至工作流程專案。工作流程包含下列檔案。
  
    
    


|**檔案名稱**|**描述**|
|:-----|:-----|
|Elements.xml <br/> |指定工作流程及它所包含檔案，例如 workflow.xaml 檔案及關聯的檔案，以及每個檔案，例如及其 URL、 本身類型，以及其路徑的屬性的設定。針對每個檔案新增至工作流程專案，對應的區段新增至工作流程的 Elements.xml 檔案。 在清單中的工作流程關聯檔案需要] 清單中，讓他們有清單語彙基元的參照。在網站工作流程的 GUID 被新增網站。 <br/> > [!注意]> 由於Visual Studio維護 Elements.xml 檔案中的項目，因此建議針對變更其除非您已熟悉之變更的影響。          |
|Workflow.xaml <br/> |代表 [工作流程設計工具。在此檔案中，您將動作新增至工作流程並設定其程式碼和屬性。 <br/> |
|WorkflowStartAssociation <br/> |手動啟動 SharePoint 工作流程。此檔案加入至工作流程專案如果您在 [工作流程精靈] 中選取 **使用者手動啟動工作流程**] 核取方塊。 <br/> |
|ItemAddedAssociation <br/> |如果其中有無使用者建立網站或清單 (依工作流程類型) 中的項目時自動啟動工作流程。此檔案加入至工作流程專案如果您在工作流程精靈選取 **工作流程啟動自動時新增的項目**] 核取方塊。 <br/> |
|ItemUpdatedAssociation <br/> |如果出現在使用者變更網站或清單 (依工作流程類型) 中的項目時自動啟動工作流程。此檔案加入至工作流程專案如果您在工作流程精靈選取 **工作流程啟動自動時變更的項目**] 核取方塊。 <br/> |
|WorkflowHistoryList <br/> |代表如果您在 [工作流程精靈] 建立工作流程的歷程記錄清單會新增至工作流程專案檔案。 <br/> |
|WorkflowTaskList <br/> |代表已新增至工作流程專案如果您在 [工作流程精靈] 建立工作流程工作清單的檔案。 <br/> |
   

### 工作流程的自訂活動

將工作流程的自訂活動的專案項目新增至您SharePoint Add-in。新增工作流程的自訂活動，您可以建立以 SharePoint Designer 2013 中的自訂動作可以再匯入的其他工作流程動作。工作流程的自訂活動會包含 Elements.xml 檔案中，會定義動作的屬性，並將.xaml 檔案的工作流程設計工具。如需詳細資訊，請參閱 [在 SharePoint 2013 中的工作流程](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx)。
  
    
    

## 其他資源
<a name="SP15Projecttemplates_addlresources"> </a>


-  [工具及開發 SharePoint 的增益集的環境](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
