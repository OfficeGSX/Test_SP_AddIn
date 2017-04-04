---
title: 將自訂內容類型新增至 SharePoint hostedSharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: b52e5622-bf87-4bb1-a99a-ac1389de6651
---


# 將自訂內容類型新增至 SharePoint hostedSharePoint 增益集
了解如何在SharePoint Add-ins包含自訂內容類型。
這是一系列的開發 SharePoint 主控SharePoint Add-ins基本知識文章中的第四。您應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中的其他文章:
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將自訂欄新增至 SharePoint hostedSharePoint 增益集](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  

> [!注意事項]
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforeContentType.sln 檔案。
  
    
    

本文中您可以將自訂內容類型新增至員工方向SharePoint Add-in。
## 建立自訂內容類型


  
    
    

1. 在 **方案總管**] 中以滑鼠右鍵按一下專案並選擇 [ **新增]** > **新資料夾**。名稱資料夾內容類型。
    
  
2. 以滑鼠右鍵按一下新資料夾並選擇 [ **新增]** > **新增項目**。[ **新增項目**] 對話方塊隨即開啟 [ **Office/SharePoint** ] 節點。.
    
  
3. 選擇 [ **內容類型**指定其名稱NewEmployee、，然後選擇 [ **新增]**。當系統提示精靈所選取的基底的內容類型，請選擇 [ **項目**，然後選擇 [ **完成時間**。
    
  
4. 如果內容類型設計工具不會自動開啟，請在 **方案總管中**開啟選擇 **NewEmployee**內容類型。
    
  
5. 在設計工具中開啟 [ **內容類型**] 索引標籤，並填滿] 文字方塊中，如下所示：
    
  - **內容類型名稱**： NewEmployee
    
  
  - **描述**：代表新員工。
    
  
  - **群組名稱**：員工方向
    
  
6. 確認該 *none*  ] 核取方塊] 索引標籤上的選取。預設可能會選取 **繼承自上層內容類型的資料行**] 核取方塊。 *請確定已清除人。*  [] 索引標籤現在應該看起來如下：
    
   **內容類型] 索引標籤**

  

     ![內容類型設計工具，顯示 "NewEmployee" 做為類型名稱、[代表新員工] 做為描述以及 [員工訓練] 做為群組。](images/8a9768f4-315d-45c0-88d7-687dbf84495c.PNG)
  

    
    
  
7. 在設計工具中開啟 [ **欄**] 索引標籤。
    
  
8. 在方格中，選擇 [開啟] 下拉式清單的欄，[ **按一下這裡以新增欄**並新增 [ **部門**] 欄中。在下拉式清單中列出依其顯示名稱： **部門**。 不要 **方向階段**欄相同。(如果未列出，您可能未啟動正確Visual Studio解決方案。開始使用 BeforeContentType.sln)。當您正在完成格線看起來應該類似如下：
    
   **資料行] 索引標籤**

  

     ![格線中列有 [員工]、[部門] 和 [訓練階段] 之內容類型設計工具的 [欄] 索引標籤。](images/835e78b3-a073-45b2-b4ee-3f9be9d88495.PNG)
  

    
    
  
9. 儲存檔案並關閉設計程式]。
    
  
10. 下一個步驟需要直接運作中的內容類型的原始 XML，所以在 **方案總管**] 中選擇 **NewEmployee**內容類型的 elements.xml 檔案子系。
    
  
11. 您已新增兩個資料行的檔案中已經有 **FieldRef**元素。將 **FieldRef**元素的兩個內建SharePoint欄新增為等項目已有兩個。以下是針對元素的標記。 *您必須使用這些相同的 Guid 識別碼屬性，因為這些是以固定識別碼的內建欄位類型。*  新增這些 *以上*  兩者 **FieldRef**元素的自訂網站欄。
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Employee" />
  ```


    請注意我們已授與這些欄位的自訂的顯示名稱： **員工**。
    
  
12. 儲存後關閉檔案。
    
  
13. 依序展開 [ **方案總管**中的 [ **清單**] 節點，並選擇 **NewEmployeeOrientation**開啟清單類型設計工具。
    
  
14. 在設計工具中，開啟 [ **欄**] 索引標籤，然後選擇 [ **內容類型**] 按鈕。
    
  
15. 在 [ **內容類型的設定**] 對話方塊中，新增 **NewEmployee**內容類型。
    
  
16. 選擇 **NewEmployee**內容類型清單中的 [類型]，然後選擇 [ **設成預設值**] 按鈕。
    
  
17. 選擇 **項目的**內容類型、 以滑鼠右鍵按一下左邊的內容類型名稱，看起來的小箭頭，然後選擇 **刪除**。
    
  
18. 使得 **NewEmployee**都只內容類型列出， **資料夾**內容類型，重複上述步驟。[] 對話方塊現在看起來應類似如下：
    
   **內容類型設定] 對話方塊**

  

     ![只列出單一內容類型 (名為 NewEmployee) 的 [內容類型設定] 對話方塊。](images/b90699f4-40de-4f50-ad47-3e8773d0eb92.PNG)
  

    
    
  
19. 選擇 **[確定]**以關閉對話方塊，然後儲存並關閉檔案。
    
  
20. 開啟 schema.xml 檔案。
    
  
21. 尋找 **Fields**元素。應該包含三個 **Field**元素: **Title**、部門、 及OrientationStage。(這些項目可能會在此產生的檔案中的單一行上。若是如此，其使用分隔分行符號。)
    
  
22. 檔案保持在開啟狀態並在 **方案總管**] 中展開 [ **網站欄**資料夾及 [部門] 節點，然後再開啟 elements.xml 檔案的部門。部門的 **Field**元素中 schema.xml 應該完全重複中部門elements.xml **Field**元素。 如果不完全相符， **Field**元素複製網站欄 elements.xml 檔案並將其貼取代 schema.xml 檔案中的不相符的 **Field**元素。然後關閉 element.xml 檔案。
    
  
23. 開啟OrientationStageelements.xml 檔案。Here 太，必須有 **Field**中的元素OrientationStage，包括所有子元素，例如 **CHOICES**和 **MAPPINGS**元素的兩個檔案完全相符。如果沒有，在 elements.xml 檔案中複製 **Field**並將其貼取代 schema.xml 檔案中的不相符的 **Field**元素。然後關閉 element.xml 檔案。
    
  
24. 仍在 schema.xml 檔案並將其 **BaseViewID**值為"1"的 **View**元素中尋找下層 **ViewFields**元素，然後新增下列兩個 **FieldRef**元素做為它的子系。 他們已經可能有，遺漏 **ID**屬性，但。若是如此，新增的 ID 屬性。
    
  ```
  
<FieldRef Name="Division" ID="{GUID from the Field element}" />
<FieldRef Name="OrientationStage" ID="{GUID from the Field element}" />

  ```

25. 兩個版面配置區 **ID**屬性值取代為從 **NewEmployee** schema.xml 檔案中的舊版 **ContentType**元素中的對應 **Field**元素的 Guid。請記得臉部在大括號"{}"。
    
    "1" **View** **ViewFields**行會類似。(您 Guid 可能是不同)。
    


  ```
  
<ViewFields>
   <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
   <FieldRef Name="Division" ID="{509d2d67-9a96-4596-9b3b-58449cdcc6ff}" />
   <FieldRef Name="OrientationStage" ID="{38a3b54c-acf3-4ddf-b748-55c7c28d4cc2}" />        
</ViewFields>
  ```

26. 仍在 schema.xml 檔案中，找出其 **BaseViewID**值是"0" **View**元素。尋找其具有 **ViewFields**項目。
    
  
27. 複製 [ **ViewFields** ] 區段中的全部從透過 **ViewFields** ] 區段中的檢視"0"的檢視"1"。兩個檢視現在應該有相同 **ViewFields**章節。
    
  
28. 儲存並關閉 schema.xml 檔案。
    
  
29. 在 [ **清單**] 資料夾中，展開 [ **NewEmployeeOrientation**節點和子清單執行個體 **NewEmployeesInSeattle**。您應該能夠清楚地看到和知道執行個體 elements.xml 範本 elements.xml。開啟一個執行個體。
    
  
30. 第一個 **Row**元素，以新增兩個 **Field**元素 **Row**元素如下所示。
    
  ```
  
<Row>
  <Field Name="Title">Tom Higginbotham</Field>
  <Field Name="Division">Manufacturing</Field>
  <Field Name="OrientationStage">Tour of building</Field>
</Row>
   
  ```

31. 儲存後關閉檔案。
    
  

## 執行及測試增益集


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。
    
  
2. 增益集的預設頁面開啟時，選擇 **新員工 Seattle**連結以開啟 [自訂清單執行個體。
    
  
3. [清單] 頁面隨即開啟及 [部門] 和 [ OrientationStage欄位於。 您不需要使用者將其新增手動因為它們都是清單內容類型的一部分。最上層項目具有您新增的資料。
    
   **西雅圖清單中的新員工**

  

     ![已有 [部門] 和 [訓練階段] 欄的 [西雅圖新進員工] 清單。](images/b654af45-663e-425c-b7c7-b8b5524cb316.PNG)
  

    
    
  
4. 嘗試新增至清單的新項目和編輯現有的項目。
    
  
5. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
6. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在此系列中下一篇文章 ＜ 將將在清單檢視網頁組件新增至SharePoint Add-in的預設頁面:  [將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)。
  
    
    

