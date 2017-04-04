---
title: 將工作流程新增至 SharePoint 主控 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: b33a61d7-5bc9-4720-97a5-8475d9a0d7c5
---


# 將工作流程新增至 SharePoint 主控 SharePoint 增益集
了解如何在SharePoint Add-in中包含的工作流程。
這是一系列的開發 SharePoint 主控SharePoint Add-ins基本知識文章中的第六。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中舊的文章：
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將自訂欄新增至 SharePoint hostedSharePoint 增益集](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將自訂內容類型新增至 SharePoint hostedSharePoint 增益集](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> **注意事項**
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforeWorkflow.sln 檔案。
  
    
    

本文中您要新增工作流程通知新員工已準備好填寫 HR 書面文件的人力資源 (HR) 部門的員工方向SharePoint Add-in 。
## 將工作流程新增至 [增益集


  
    
    

1. 在 **方案總管**] 中以滑鼠右鍵按一下專案並選擇 [ **新增]** > **新資料夾**。名稱的工作流程的資料夾。
    
  
2. 以滑鼠右鍵按一下新資料夾並選擇 [ **新增]** > **新增項目**。[ **新增項目**] 對話方塊隨即開啟 [ **Office/SharePoint** ] 節點。
    
  
3. 選擇 **工作流程**並指定其名稱HR_Intake。當系統提示您選擇的工作流程類型，選擇 [ **清單工作流程**，並選擇 [ **下一步**。
    
  
4. 在精靈的 [下一步] 頁面上，啟用 [ **是，建立關聯...**則設為下列值的 [下拉式清單控制項及選項：
    
  - **文件庫或清單，以建立您使用的工作流程的關聯**
    
    西雅圖的新員工
    
  
  - **歷程記錄清單...**
    
    < 建立新 >
    
  
  - **[工作] 清單中...]**
    
    < 建立新 >
    
  

    按 [下一步]。
    
  
5. 在精靈的最後一頁，啟用 [ *僅限*  項目 *變更*  時自動啟動工作流程的選項]。
    
  
6. 選擇 [ **完成**]。
    
    Office Developer Tools for Visual Studio ，然後執行下列：
    
  - 建立 **工作流程**] 資料夾中的HR_Intake工作流程與工作流程設計工具中開啟子 Workflow.xaml 檔案。
    
  
  - 建立其中一部分的工作流程的工作會建立及更新的 **WorkflowTaskList**清單執行個體。
    
  
  - 建立是每次執行的工作流程中的各種步驟的記錄時所發生的 **WorkflowHistoryList**清單執行個體。
    
  
7. 將兩個新的清單執行個體拖曳到 [ **清單**] 資料夾。
    
  

## 設計工作流程

工作流程傳送電子郵件通知 HR staffer 新員工已完成的方向的 **導覽建置的**階段並準備填寫 HR 引入口書面文件。任何現有項目上西雅圖的新員工清單引動程序中的工作流程]，但工作流程不會執行任何除非方向階段項目的欄位清單設為HR 書面文件。 如果是，然後電子郵件傳送至 HR staffer 與該員工的工作會新增至 **WorkflowTaskList**。
  
    
    

> **注意事項**
> 在不同時間設計您的工作流程以驚嘆號中其
  
    
    
![其中含有白色驚嘆號的小藍色菱形圖形。](images/f7b82a70-80fe-4e7e-a0f5-5a78cd12b367.PNG)
  
    
    
、 藍色菱形符號時出現工作流程設計工具中的一或多個項目。這些報告暫時性錯誤。(將滑鼠游標停留在簡短的訊息，請參閱 ＜ 或外觀的詳細資訊的 Visual Studio **錯誤] 清單**中的符號)。這些是 incompleteness 的工作流程的副作用。應全部加以消失當您完成此程序。
  
    
    


1. 在Visual Studio中開啟 [ **工具箱**] 窗格、 **SP-清單**] 節點中，依序展開 [，然後 **LookupSPListItem**拖曳到設計工具中的 **順序**。
    
  
2. 使其屬性顯示在 Visual Studio **內容**] 窗格中選取 **LookupSPListItem** 。這些值來設定下列屬性：
    
  - **項目識別碼：**(目前項目)
    
  
  - **ListID:**(目前的清單)
    
  
  - **DisplayName:** LookupCurrentNewEmployee
    
  

    [ **內容**] 窗格現在應該看起來如下：
    

   **LookupSPListItem 的 [內容] 窗格**

  

     ![已設定 ItemID、ListID 和 DisplayName 屬性之 [查閱清單項目] 工作流程活動的 [內容] 窗格。](images/60f3302e-ca9c-45be-b785-0c9f636181da.PNG)
  

    按一下以儲存變更窗格外的任何地方和設計工具介面現在看起來應類似。
    

   **工作流程設計工具中的順序**

  

     ![工作流程設計工具，內含 [順序] 方塊，其內有名稱為 [查閱目前新進員工] 的活動。](images/c8fbf801-e8e4-444a-9d2e-c14e29f537de.PNG)
  

    
    
  
3. 按一下內 (新重新命名) LookupCurrentNewEmployee活動設計工具中 **取得的內容**連結。這會 **GetDynamicValueProperties**活動的順序。
    
  
4. 按一下 [ **定義...**文字 **GetDynamicValueProperties**活動。如此會開啟 [ **屬性**] 對話方塊。
    
  
5. **實體類型**設為 **清單項目** _list_instance_name_、  _list_instance_name_所在西雅圖的新員工。
    
  
6. 在 [ **路徑**] 欄中，按一下上方的儲存格，然後選擇方向階段從下拉式清單向下。
    
  
7. 按一下其下方儲存格，然後在向下的從下拉式清單選擇 **標題 (標題)** 。
    
  
8. 按一下 [ **填入變數**。這會建立名為OrientationStage和 **標題**變數並指派每個新員工西雅圖清單的目前項目中的對應欄位的值。[ **屬性**] 對話方塊現在應該看起來如下：
    
   **工作流程活動的 [內容] 對話方塊**

  

     ![[取得動態值] 活動的 [內容] 對話方塊，其中具有 [實體類型] 設為 [新員工] 清單的項目，以及名為 Title 和 OrientationStage 且指派給同名欄位的變數 。](images/36a841e7-ce1b-444c-9bfe-7cdc56399ec1.PNG)
  

  

  
9. 選擇 **[確定]**。設計工具介面現在應該看起來如下：
    
   **Workflow Designer**

  

     ![含有兩個活動的工作流程設計工具：[ 清單項目查閱] 和 [取得動態值]。](images/cd8eb456-d883-491a-b171-38c1b9f64018.PNG)
  

    
    
  
10. 開啟 [ **工具箱**] 窗格中Visual Studio、 **控制流程**] 節點中，依序展開 [，然後拖曳到 **如果**下方 **GetDynamicValueProperties** **順序**的底部。
    
  
11. **如果**[ **條件**] 方塊中輸入OrientationStage = ="HR 書面文件"。
    
  
12. 開啟 [ **工具箱**] 窗格中Visual Studio、 **SP-公用程式**] 節點中，依序展開 [，然後拖曳到 **然後**方塊 **如果**活動的 **電子郵件**。
    
  
13. 選取 [ **電子郵件**活動。在 [ **內容**] 窗格中，設定內文結尾的值、 主旨、 和 To 屬性。在每個案例中，選擇 [註標] 按鈕 **...**屬性並使用 **運算式編輯器**開啟設定如下列表格所示的屬性值。這些是 C# 的字串運算式，所以使用引號與顯示內容完全相同。 `Title`以下是您先前指派給清單項目 (保留該員工的名稱) 的 [ **標題**] 欄位的變數。
    
  - **本文:** `Title + " is waiting in the lobby to fill out benefits and employment forms."`
    
  
  - **Subject:** `Title + " is ready for HR paperwork"`
    
  
  - **至:** `new System.Collections.ObjectModel.Collection<string>() {"your_O365_email"}`
    
    取代預留位置，  *your_O365_email*  ，與您用來登入您的 Office 365 開發人員的身分識別帳戶，例如 *alias*  @ *O365domain*  。 sharepoint.com。此為 C# 字串必須是在引號。
    
  
14. 開啟 [ **工具箱**] 窗格中Visual Studio、 **Runtime** ] 節點中，依序展開 [，然後 **TerminateWorkflow**拖曳到 **如果**活動的 **Else**方塊。
    
  
15. 選取 [ **TerminateWorkflow**活動和在 [ **內容**] 窗格中，將 **原因**設為下列， *包括引號*  的:"不在 HR 書面文件的階段。"。在設計工具應尋找下列：
    
   **工作流程完成後的工作流程設計工具**

  

     ![具有 [清單項目查閱]、[取得動態值] 和 [If Then Else] 結構之活動的工作流程設計工具。電子郵件是 Then 組件中的活動，而 [終止工作流程] 是 Else 中的活動。](images/c1230e3b-d149-413c-aa66-d258250a4512.PNG)
  

  

  

## 執行及測試增益集


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。工作流程管理員的 **測試服務主機**主控台也會隨即開啟。
    
  
2. 增益集的預設頁面會隨即開啟，開啟其中一個項目進行編輯，然後將方向階段的值設為HR 書面文件。
    
    在 **測試服務主機**主控台中，這會顯示已經開始工作流程。不久後沒有已完成之工作流程指示。以下為範例：
    

   **服務測試主機主控台**

  

     ![工作流程 [測試服務主機] 視窗，內含一行指出已啟動工作流程，而後面一行則指出已完成。工作流程執行個體的 GUID 是在每行的開頭。](images/2422936d-7ef6-4c90-a03f-30053fbb9743.PNG)
  

    
    
    
    > **注意事項**
      > 如果無法開啟 **測試服務主機**主控台，您可能需要啟用偵錯工作流程。以滑鼠右鍵按一下 [ **方案總管**中的專案名稱並選擇 [ **屬性**]。開啟 [ **SharePoint** ] 索引標籤上 [ **內容**] 窗格和適用於 **偵錯啟用工作流程**] 核取方塊。
3. 瀏覽至電子郵件收件匣 (Outlook) 您的 Office 365 開發人員帳戶。沒有電子郵件主旨 *"Employee*  已準備就緒 HR 文。" *Employee*  所在的員工您編輯之項目的名稱。電子郵件的本文指出 *"員工*  填寫好處和工作表單大廳中等候"。以下為範例：
    
   **工作流程所傳送的電子郵件**

  

     ![Outlook 中主旨為 "Cassie Hicks is ready for HR paperwork" 且主體為 "Cassie Hicks is waiting in the lobby to fill out benefits and employement forms" 之工作流程的電子郵件訊息。](images/7b1d8f47-9c34-441e-af6a-3af4a8c65533.PNG)
  

    
    
    
    > **秘訣**
      > 如果工作流程開始但從未完成後，而不傳送的電子郵件、 試用結束偵錯工作階段並嘗試 F5 再次一些時間才能結束處有是在您的程式碼錯誤的某個項目。有時問題是SharePoint Online中。> 如果您仍然有問題，請嘗試新增內容類型呼叫 **ListFieldsContentType**，如果沒有一個已經、 schema.xml 檔案 **ContentTypes**章節。以下是標記的範例。>  `<ContentType ID="0x0100781dd48170b94fdc9706313c82b3d04c" Name="ListFieldsContentType" Hidden="TRUE">`
  
    
    
 `</ContentType>`> 然後將 [ **FieldRefs** ] 區段中的 **NewEmployee**內容類型的整個複製到這個新的內容類型。> 將專案儲存、 撤銷、 和再試一次 F5。
4. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
5. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在此系列中下一篇文章 ＜ 您將新增自訂頁面和樣式至SharePoint Add-in:  [以 SharePoint 主控 SharePoint 增益集新增自訂頁面與樣式](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)。
  
    
    

