---
title: 開發 SharePoint 的增益集
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# 開發 SharePoint 的增益集
尋找深入的文章與資源以協助您在SharePoint Add-ins中建置進階的功能。
> **注意事項**
> 本文假設您熟悉文章 [SharePoint Add-ins](sharepoint-add-ins.md)和快速入門它連結至資料。
  
    
    

下 **Develop** ，我們有下列 cmdlet 以協助說明您可以進行SharePoint Add-in中的所有其他工作：
- 深入的概觀
    
  
- 用法文章
    
  
- 程式碼片段 (英文)
    
  
將有關文章：
- 執行建立、 讀取、 更新及刪除清單上的 (CRUD) 作業
    
  
- 如何建置 REST 查詢及與新的 Api 互動
    
  
- 如何及何時安全性設定 OAuth
    
  
SharePoint 企業社交功能，例如活動摘要，而且使用者設定檔，以及企業內容管理功能、 企業營運 (LOB) 互通性功能及真正可讓您的增益集的網站設計功能突出。深入了解這些 [新增 SharePoint 2013 功能](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx)中。與、 程式碼是索引鍵，那麼仔細 「 範例 」 功能表開發人員中心中。它是我們的程式碼範例增益集的直接連結。一旦您已設定開發環境，您應該取出我們的範例是一些。善用社群功能，可讓您如果我們沒有一個您想要查看要求的程式碼範例。我們採用這些要求，以及其他文件的意見反應及用於我們連續更新的內容及範例。因此請讓我們知道您想要看到的內容!
## 開始使用SharePoint Add-ins資源
<a name="bk_gettingstarted"> </a>

如果您只開發SharePoint Add-ins快速入門、 先看看 [SharePoint Add-ins](sharepoint-add-ins.md)。該頁面會指向您要讓您快速熟悉SharePoint Add-ins的不同類型的主要文章。再進行更進階的SharePoint Add-ins開發類型，您應開始使用好的增益集，您想要建立、 技術會想要加上，與您會想要使用的主控選項類型。
  
    
    

### 基本工作及開發SharePoint Add-insSharePoint 2013中使用的用戶端物件模型、 JavaScript物件模型，以及 REST 端點的資源
<a name="bk_essentials"> </a>

不論您決定建立何種SharePoint Add-in 、 增益集將會一律互動一些方式與SharePoint 2013站台。表格 1 中的文章說明如何使用三個介面可供您使用SharePoint Add-ins您在執行許多種最重要的工作與 SharePoint 網站： 用戶端物件模型、 JavaScript物件模型和 REST 端點。
  
    
    

**表 1。基本作業SharePoint 2013用戶端物件模型、 JavaScript物件模型，及 REST 介面**


|**主題**|**描述**|
|:-----|:-----|
| [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |說明如何執行一般作業使用 C# 與用戶端物件模型。 <br/> |
| [使用 SharePoint 2013 中的 JavaScript 程式庫程式碼完成基本作業](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |說明如何執行一般作業使用JavaScript物件模型。 <br/> |
| [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |說明如何使用 REST 介面執行一般作業。 <br/> |
   

## 了解與SharePoint Add-ins開發的基本概念
<a name="bk_fundamentals"> </a>

除了了解基本作業，您應該了解SharePoint 2013增益集開發模型的基本概念。每種SharePoint Add-in包含增益集資訊清單檔案與內建於您部署至SharePoint 2013站台的增益集套件。當您開發任何種類的增益集必須考慮某個範圍的驗證與授權、 資料存取和可用性相關的問題。表格 2 中的文章您熟悉這些問題，並說明其想要建立的任何種類的含意。
  
    
    

**表 2。使用SharePoint Add-ins的基本概念**


|**主題**|**描述**|
|:-----|:-----|
| [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |引導您完成以取得使用SharePoint 2013資源的必要權限的相關的核心概念。 <br/> |
| [探索應用程式資訊清單結構和套件的 SharePoint 增益集](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |說明如何增益集資訊清單之內建工時及如何增益集的套件。 <br/> |
| [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md) <br/> |我們將在其中您可以建置豐富的使用者經驗SharePoint Add-ins的方式。 <br/> |
| [使用 SharePoint 2013 中的外部資料](work-with-external-data-in-sharepoint-2013.md) <br/> |說明的資料存取選項及不同種類的SharePoint Add-ins中可用的技術。 <br/> |
| [授權您的 Office 和 SharePoint 增益集](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |引導您完成Office 與 SharePoint 增益集的增益集授權架構。 <br/> |
   

## 一起放入部分： 建立進階 SharePoint 增益集整合功能
<a name="bk_integrate"> </a>

當您熟悉的功能和SharePoint Add-ins功能時，便可以開始建立較複雜的增益集所放在所有部分一起以符合您需求的方式。表 3 中的文章將示範如何整合功能，並建立更完整地精選的SharePoint Add-ins。
  
    
    

**表 3。進階的SharePoint Add-ins概念**


|**主題**|**描述**|
|:-----|:-----|
| [建立提供者主控增益集包含的自訂 SharePoint 清單與內容類型](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |說明如何建立SharePoint Add-ins的裝載在雲端中並包括自訂 SharePoint 清單及內容類型。 <br/> |
   

## 其他資源
<a name="bk_addresources"> </a>


-  [工具及開發 SharePoint 的增益集的環境](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
-  [設計 SharePoint 增益集](design-sharepoint-add-ins.md)
    
  
-  [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)
    
  
-  [SharePoint 增益集範例套件](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  

