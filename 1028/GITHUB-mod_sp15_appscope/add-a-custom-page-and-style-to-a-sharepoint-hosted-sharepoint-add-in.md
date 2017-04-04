---
title: 以 SharePoint 主控 SharePoint 增益集新增自訂頁面與樣式
ms.prod: SHAREPOINT
ms.assetid: 91a20d79-318f-44ce-9877-3fa07d03aa09
---


# 以 SharePoint 主控 SharePoint 增益集新增自訂頁面與樣式
了解如何在SharePoint Add-ins中包含的自訂頁面與 CSS 檔案。
這是以一系列的基本知識文章的開發 SharePoint 主控SharePoint Add-ins二十七。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將自訂欄新增至 SharePoint hostedSharePoint 增益集](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將自訂內容類型新增至 SharePoint hostedSharePoint 增益集](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將工作流程新增至 SharePoint 主控 SharePoint 增益集](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforePage.sln 檔案。
  
    
    

本文中您可以將 「 說明 」 頁面新增至員工方向SharePoint Add-in並將其設定為使用自訂的 CSS 樣式表。
## 新增頁面


1. 在 [ **方案總管**中，[ **頁面**] 資料夾上按一下滑鼠右鍵並選擇 [ **新增**> **新增項目**。[ **新增項目**] 對話方塊隨即開啟 [ **Office/SharePoint** ] 節點。
    
  
2. 選擇 [ **頁面]**並指定其名稱Help.aspx。
    
  
3. 在檔案中，找到以下兩個 **asp:Content**元素並新增下列的第三個 **asp:Content**標記傳來它們。
    
  ```HTML
  
<asp:Content ContentPlaceHolderID="PlaceHolderPageTitleInTitleArea" runat="server">
    Help
</asp:Content> 
  ```

4. 尋找具有 **PlaceholderAdditionalPageHead**、 識別碼 **asp:Content**元素，並將下列標記。
    
  ```HTML
  
<link rel="Stylesheet" type="text/css" href="../Content/App.css" />
  ```

5. 具有 **PlaceHolderMain**、 識別碼 **asp:Content**元素中尋找及移除任何子元素它。
    
  
6. 將下列作為內容新增至相同的 **asp:Content**元素。
    
  ```HTML
  <H3>Having a problem with the add-in?</H3>
<p> Call the help line for Fabrikam Add-ins:</p>
<p>1-555-555-5555</p>
  ```

7. 儲存後關閉檔案。
    
  
8. 開啟 Default.aspx 檔案。
    
  
9. 尋找 **asp:Content**元素與 **PlaceHolderMain**、 識別碼，然後將下列標記新增至結尾。
    
  ```HTML
  
<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Pages/Help.aspx';"
    Text="Get help for the Employee Orientation add-in" /></p>

  ```

10. 儲存後關閉檔案。
    
  

## 將樣式類別新增至樣式表


  
    
    

1. 在 **方案總管**] 中開啟 [ **內容**] 資料夾中、 app.css 檔案與檔案中新增下列行。
    
  ```
  
p {color: green;}
  ```

2. 儲存後關閉檔案。
    
  

## 執行及測試增益集


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。
    
  
2. 增益集的預設頁面開啟時，按一下 [ **取得增益集的員工方向說明**連結以開啟 [ **說明**] 頁面。
    
    您的自訂頁面會隨即開啟並您放在 < p > 標記兩行都會綠色。
    

   **「 說明 」 頁面**

  

     ![標題為 [說明] 的 SharePoint 頁面。標頭行為黑色，後接兩個綠色文字行。](images/2df51ab0-5b24-4a37-8b6a-6e95dbb1aeaa.PNG)
  

    
    
  
3. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
4. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在此系列中下一篇文章 ＜ 您將新增自訂的用戶端轉譯至SharePoint Add-in清單欄：  [以 SharePoint 主控 SharePoint 增益集新增自訂的用戶端轉譯](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md)。
  
    
    

