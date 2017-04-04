---
title: 設計 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# 設計 SharePoint 增益集
取得位於SharePoint Add-ins，並了解如何讓SharePoint 2013中簡化的增益集開發的右決策的設計及架構選項的概觀。
假設您有殺人概念的增益集。在本節，我們將引導您進行您要進行編輯及提供最佳作法，以建立增益集的設計決策。例如，如何使良好的使用者介面？增益集 」 圖形"可用有哪些？何時應該使用一個而另一個不是？我可以使用哪些選項進行資料存取？
  
    
    


## 開始設計SharePoint Add-ins
<a name="SP15Design_Startdesigning"> </a>

因為雲端增益集模型中的全部SharePoint 2013讓太設計選擇變得可能、 SharePoint Add-ins可以跳中許多圖形和大小。本節包含一些您必須制定隨著的規劃和設計的架構與使用者經驗的增益集的最重要決定很有幫助指導  包括如何將裝載增益集、 增益集將如何有效率且安全地存取資料，以及自身的使用者經驗。
  
    
    
如需設計及架構可用選項與 SharePoint 增益集的概觀，請參閱 [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)。如需的哪些SharePoint Add-ins是概觀，請參閱 [SharePoint Add-ins](sharepoint-add-ins.md)。
  
    
    

## 選擇架設的增益集模型的權限
<a name="SP15Design_Hostingmodel"> </a>

SharePoint Add-ins支援多個主控的選項。您可以選擇您自己的 web 堆疊、 有 Microsoft 佈建Microsoft Azure及SQL Azure，或有架設在 SharePoint 上的增益集。表 1 包含可協助您選擇 [增益集的右裝載模型的資源。
  
    
    

**表 1。資源和指引選擇主控SharePoint Add-ins模型的權限**


|**文章**|**描述**|
|:-----|:-----|
| [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |了解您能託管的SharePoint Add-ins元件的不同方式。 <br/> |
   

## 選擇 [增益集的正確的資料存取技術
<a name="SP15Design_Dataaccess"> </a>

您必須確定該增益集存取資料有效率且安全地。各種資料存取技術可供存取 SharePoint，並在您的增益集使用資料表格 2 提供可協助您了解您的選項，並選擇最適合您的增益集的資源。
  
    
    

**表 2。資源和指引選擇SharePoint Add-ins中使用資料存取技術**


|**文章**|**Description**|
|:-----|:-----|
| [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> |了解建置SharePoint Add-ins，包括輸入和輸出資料案例的資料連線選項時，您有資料存取選項以及您想要從增益集存取 SharePoint 資料時所能使用的 Api。 <br/> |
   

## 設計 UX 增益集
<a name="SP15Design_UX"> </a>

當您設計增益集，實際的目標應該是嘗試建立讓使用者可以完成您想讓他們完成案例的體驗。表 3 中發現的資源與您需要建立更好增益集，請遵循使用者經驗設計的最佳作法且熟悉的外觀與行為的SharePoint 2013的設計指導。
  
    
    

**表 3。資源和指引來設計SharePoint Add-ins更好的使用者體驗**


|**文章**|**Description**|
|:-----|:-----|
| [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md) <br/> |了解您必須建立SharePoint Add-ins時的使用者經驗選項。 <br/> |
   

## 設計與記住的更新
<a name="Upgrade"> </a>

有時您可能會想要產生的增益集更新並將其上傳至Office Store或組織的增益集目錄。該任務會更加容易如果您認為有關如何您會更新增益集時所設計的第一個版本。我們建議您在設計階段初期閱讀下列文章：  [SharePoint 增益集更新程序](sharepoint-add-ins-update-process.md)和 [更新 SharePoint 增益集](update-sharepoint-add-ins.md)。
  
    
    

## 後續步驟： 開發及發佈增益集
<a name="SP15Design_Next"> </a>

具有增益集的實心設計吗？學會使用增益集建立並發佈。表 4 中所提供的資源可協助您開始。
  
    
    

**表 4。資源和指引開發及發佈SharePoint Add-ins**


|**文章**|**描述**|
|:-----|:-----|
| [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md) <br/> |討論進階的概念和增益集模型的功能。 <br/> |
| [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md) <br/> |說明的程序和發行SharePoint Add-ins的需求。 <br/> |
   

## 其他資源
<a name="SP15Design_AddRes"> </a>


-  [SharePoint 增益集範例套件](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
-  [重新構想 SharePoint 開發](http://msdn.microsoft.com/zh-tw/office/apps/dn133840)
    
  
-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [增益集的部落格](http://blogs.msdn.com/b/spoffapps)
    
  

