---
title: 取得 SharePoint 物件模型的快速概觀
ms.prod: SHAREPOINT
ms.assetid: 6b8b55f8-9370-43a0-af8d-e07d1028a075
---


# 取得 SharePoint 物件模型的快速概觀
取得 SharePoint 物件模型的主要類別的一些快速介紹。
這是一系列的開發提供者主控SharePoint Add-ins基本知識文章中的第四。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [包含自訂按鈕中的增益集](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關提供者主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials)在存放庫並開啟 BeforeSharePointWriteOps.sln 檔案。
  
    
    

本文中您將需要從編碼若要取得的 SharePoint 用戶端物件模型 (CSOM) 的快速概觀簡短符號。此模型是大型且制訂在 MSDN 中具有參考主題"how to"s、 和程式碼範例。在本文中，我們可以只提供冰山的秘訣的秘訣。但即使極短的簡介會有多少您會看到的程式碼中進行這一系列許多較少神秘。
## 內容階層

下表顯示內容的階層中 SharePoint and 的 CSOM 類別代表它們。每個這些實體有下方該類型的子項。
  
    
    

|
|
|**Entity**|**類別**|**備註**|
|:-----|:-----|:-----|
|SharePoint 內部部署伺服器陣列或SharePoint Online訂閱 (也稱為租用戶) <br/> ||沒有僅限於以程式設計方式存取 CSOM 在此層級。例如沒有任何伺服器陣列或訂閱或租用戶類別。(無法用於增益集的 SharePoint 的伺服器端物件模型，可讓這些實體以程式設計方式存取)。 <br/> |
|網站集合 <br/> |**Site** <br/> |會群組在一起的主要系統管理原因以及門牌 SharePoint 元件，例如加上品牌的主版頁面、 或自訂安全性群組，可套用到所有子網站的網站集合。所有網站都屬於某些網站集合。 <br/> |
|網站上的群組。 <br/> |**Web** <br/> |頁面和 SharePoint 元件的一組。可以有 subwebsites。 <br/> |
|清單 <br/> |**List** <br/> |文件庫和其他類型的檔案庫也包含在此層級。 <br/> 文件庫是一種特殊的清單中的每一列包含附加的文件及其他欄是文件，例如 author、 當它上次編輯，且有它取出的相關資料。 <br/> |
|清單項目 <br/> |**ListItem** <br/> |清單中的列  即清單項目  與特定資料列的欄位中的值。也有類型。請參閱下一列。 <br/> |
|清單項目 <br/> |**Content Type** <br/> |清單項目類型。這些是以 **ContentType**類別來表示。每個基本上是一組資料行及中繼資料。最簡單是內建 **Item**內容類型。其他所有內容類型衍生自 **Item**。SharePoint 包含許多內建內容類型，例如事件和宣告。 <br/> |
|column <br/> |**Field** <br/> |**Field**物件包含相關的基礎資料型別資訊不僅方式的格式化並呈現在表單，例如建立、 顯示、 及編輯特定清單項目表單上的相關資訊。 <br/> |
   

  
    
    
您可以程式設計方式建立自訂清單、 內容類型、 資料行類型及清單項目。
  
    
    
除了內容、 CSOM 可以讓您存取使用者、 群組、 角色與權限、 分類法、 搜尋和更多。
  
    
    

## 用戶端執行階段以及批次處理
<a name="CSOMBatching"> </a>

CSOM 使用批次處理系統。Managed 程式碼區塊會轉換成 XML 並傳送到單一的 HTTP 要求的伺服器。針對每個命令中，撥打的電話對應伺服器物件模型，並伺服器傳回給用戶端回應中 JavaScript Object Notation (JSON) 格式。
  
    
    
在用戶端上的 SharePoint 程式碼開始，以擷取代表目前的要求內容，包括 SharePoint 網站 (和其父網站集合) 的身分識別的用戶端快顯物件，您可以透過此上下文中取得 CSOM 物件的存取權。以下是您一次及一次將會看到的基本結構。請注意下列有關這段程式碼：
  
    
    

-  `spContext`物件的類型 **SharePointContext**和中定義Office Developer Tools for Visual Studio所產生的 SharePointContext.cs (或.vb) 檔案。可修改此檔案，但它不是通常需要這麼做。大部分SharePoint Add-in專案、 這個檔案，及也工具所產生的 TokenHelper.cs (或.vb) 檔案，有效地運作為副檔名 CSOM 本身擷取。
    
  
-  `clientContext`物件是 CSOM 類型 **ClientContext**。
    
  
- **ExecuteQuery**方法可將 CRUD 作業中的程式碼和它傳送至 SharePoint server 的 XML 訊息。那里它會轉譯成相等的伺服器端物件模型程式碼並執行。
    
  



```cs

using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    // CRUD operation or query code goes here.

    clientContext.ExecuteQuery();
}
```

有此模式在這一系列下, 面所顯示之 `GetLocalEmployeeName`方法中先前文章中的範例。請注意下列有關此方法：
  
    
    

- 若要取得清單及清單項目物件的參照出現 **using**區塊中的前兩行。但實際上時的 SharePoint 用戶端執行階段中執行這些行，他們只是會轉譯成 XML 格式。 **ExecuteQuery**方法傳送之伺服器的 XML。
    
  
- **Load**方法將額外的某個項目新增至郵件： 它會告知伺服器以傳送到用戶端所指定的物件。 **ExecuteQuery**方法會收到此物件 (如 JSON) 以及使用它來初始化用戶端 `selectedLocalEmployee`變數。後續的用戶端程式碼和再參照該 **ListItem**物件以及其成員。如您所見下, 一行會參照項目的"Title"欄位的值。這一行會有一個例外狀況如果 **Load**方法有不呼叫因為物件不會真的在用戶端上存在直到其載入。
    
  



```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
```


## 
<a name="Nextsteps"> </a>

我們在下一篇文章 ＜ 得到來編碼和了解如何將資料寫入至 SharePoint:  [將 SharePoint 相關寫入作業新增至 [增益集]](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
  
    
    

