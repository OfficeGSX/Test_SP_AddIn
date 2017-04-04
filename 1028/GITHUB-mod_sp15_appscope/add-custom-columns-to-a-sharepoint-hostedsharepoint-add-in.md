---
title: 將自訂欄新增至 SharePoint hostedSharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 0fbc9a8b-d652-44a8-b821-578afd8f33dc
---


# 將自訂欄新增至 SharePoint hostedSharePoint 增益集
了解如何將自訂欄納入SharePoint Add-ins。
這是一系列的開發 SharePoint 主控SharePoint Add-ins基本知識文章中的第三個。您應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)並放在這一系列文章：
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforeColumns.sln 檔案。
  
    
    

本文中收到若要將一些網站欄新增至員工方向SharePoint Add-in編碼。
## 建立自訂欄類型


  
    
    

1. 在 **方案總管**] 中以滑鼠右鍵按一下專案並選擇 [ **新增]** > **新資料夾**。名稱的網站欄的資料夾。
    
  
2. 以滑鼠右鍵按一下新資料夾並選擇 [ **新增]** > **新增項目**。[ **新增項目**] 對話方塊隨即開啟 [ **Office/SharePoint** ] 節點。
    
  
3. 選擇 [ **網站欄**、 為其指定名稱部門、 並選擇 [ **新增]**。
    
  
4. 在 elements.xml 檔案並將新的網站欄中編輯 **Field**元素使它具有屬性及值顯示在下列範例中，但是 *您應該 **變更 GUID*** **ID**的屬性值為其， *因此請小心如果您使用複製及貼上*  產生該Visual Studio 。
    
  ```
  
<Field ID="{generated GUID}"
       Name="Division" 
       Title="Division" 
       DisplayName="Division" 
       Description="The division of the company where the employee works." 
       Group="Employee Orientation" 
       Type="Text" 
       Required ="FALSE">
</Field>
  ```

5. 將另一個 **網站欄**新增至名為OrientationStage的相同資料夾中。
    
  
6. 在 elements.xml 檔案並將新的網站欄的編輯 **Field**元素已定義的屬性和值顯示在下列範例中，不同之處在於您不應該變更 **ID**屬性的 GUID 值為其產生的Visual Studio 。
    
  ```
  
<Field ID="{generated GUID}"
       Name="OrientationStage" 
       Title="OrientationStage"
       DisplayName="Orientation Stage" 
       Group="Employee Orientation" 
       Description="The current orientation stage of the employee." 
       Type="Choice"
       Required ="TRUE">
</Field>
  ```

7. 因為這是選擇欄位，您必須指定可能的選項和應該出現的順序在下拉式清單中當使用者已選擇。與因為它是必要的欄位，您必須指定為預設值。將下列子標記新增至 **Field**元素。
    
  ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Tour of building</CHOICE>
      <CHOICE>HR paperwork</CHOICE>
      <CHOICE>Corporate network access</CHOICE>
      <CHOICE>Completed</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Tour of building</MAPPING>
      <MAPPING Value="3">HR paperwork</MAPPING>
      <MAPPING Value="4">Corp network access</MAPPING>
      <MAPPING Value="5">Completed</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```

8. 儲存所有檔案。
    
  

## 增益集執行及測試欄


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。
    
  
2. 增益集的預設頁面開啟時，選擇 **新員工 Seattle**連結以開啟 [自訂清單執行個體。
    
  
3. 開啟清單 **設定**] 頁面上，並將兩個資料行新增至其進行這些步驟。
    
1. 按一下 [註標] 按鈕， **· · ·**、 清單、 正上方，然後選擇 [ **建立檢視**。
    
  
2. **檢視類型**] 頁面隨即開啟，具有連結階層結構 **設定 > 檢視類型**靠近頂端。按一下 [ **設定**階層連結。
    
   **若要開啟 [清單設定] 頁面上的步驟**

  

     ![在步驟 1 中反白顯示圖說文字按鈕和 [建立檢視] 項目的 [西雅圖新進員工] 清單。然後將箭頭指向反白顯示 [設定] 階層連結的 [建立檢視] 頁面。](images/6c119cae-adf8-42ff-9890-f3aa1e11719d.png)
  

    
    
  
3. 在 [ **設定**] 頁面上開啟左側的 **從現有的網站欄新增]**連結有關中間向下] 頁面。
    
   **[清單設定] 頁面**

  

     ![反白顯示 [從網站欄新增欄] 連結的清單執行個體設定頁面。](images/a8698b77-b9d2-40f6-89f6-ccc3c6e06073.png)
  

    
    
  
4. 在 [ **新增網站欄的資料行**] 頁面上選擇 [上 **選取 [從下列網站欄]**下拉式清單中的 **員工方向**。
    
   **從網站欄] 頁面上新增期**

  

     ![SharePoint 欄選取控制項，而標題為 [選取網站欄] 的下拉式清單中已選取 [員工訓練]。](images/3b33c622-c52a-45fd-8ea1-d7f307539753.png)
  

    
    
  
5. 將 [ **部門**] 和 [ **OrientationStage**欄新增至 [ **新增欄**] 方塊中。
    
  
6. 選擇 **[確定]**回到 [ **設定**] 頁面，然後按一下 [頁面頂端附近 **西雅圖的新員工**階層連結。
    
  
4. 新欄現在是清單上。將新項目新增至清單。編輯表單上的 **方向階段**欄位將已經具備尚未啟動的預設值。(因為他們所建立之前的欄位是在清單上現有的項目將會是在此欄位空白)。
    
   **新欄的清單**

  

     ![具有新 [部門] 和 [訓練階段] 欄的清單。](images/d4e17424-c06b-4635-aab8-4912cee5fe35.png)
  

    
    
  
5. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
6. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

您真的不想讓使用者手動將自訂欄新增至清單，因此，在此系列中的下一篇文章 ＜ 您將建立自訂內容類型，包括自訂欄且自動產生關聯之新員工清單範本具有:  [將自訂內容類型新增至 SharePoint hostedSharePoint 增益集](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)。
  
    
    

