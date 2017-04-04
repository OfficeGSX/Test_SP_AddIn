---
title: 更新 SharePoint 2013 中的增益集 web 元件
ms.prod: SHAREPOINT
ms.assetid: 6da6ac25-53b7-4dd2-8637-a86e7ca1f3ff
---


# 更新 SharePoint 2013 中的增益集 web 元件
更新頁面、 清單、 內容類型及SharePoint Add-in中的其他增益集 web 元件。
## 更新增益集 web 元件的必要條件
<a name="Prerequisites"> </a>

先熟悉 [更新 SharePoint 增益集](update-sharepoint-add-ins.md)的先決條件和它包含的核心概念。
  
    
    
本主題假設您已開發和測試增益集的最新版本中所述 [建立及偵錯新版好像它是全新的增益集](update-sharepoint-add-ins.md#DebugFirst)。
  
    
    

## 在增益集 web 更新 SharePoint 元件
<a name="UpdatingAppWeb"> </a>

所有 SharePoint 元件部署至增益集 web **Web**中包含-範圍增益集套件中的功能。對於的原因而更新這些元件是更新一或多項功能的專家。此程序未變更自SharePoint 2010和所記載的 [How to: Add Elements to an Existing Feature](http://msdn.microsoft.com/library/b007f419-e0d6-4e3a-a3ae-b8e448656d02%28Office.15%29.aspx)SharePoint 2010 SDK中。其他 [Upgrading Features](http://msdn.microsoft.com/library/e917f709-6491-4d50-adbe-2ab8f35da990%28Office.15%29.aspx)節點中的文章可能會有所助益也，但考慮增益集必須不包含在 SharePoint 伺服器上的自訂程式碼讓功能升級SharePoint 2010中的某些部分不相關更新增益集。例如，您無法使用 [CustomUpgradeAction](http://msdn.microsoft.com/library/16a2182e-80aa-4184-8071-8f717ee5c572%28Office.15%29.aspx)元素當您升級SharePoint Add-in的功能。
  
    
    

### 可以以及無法以宣告完成

以 SharePoint 主控增益集，您僅可使用的 XML 標記來更新增益集和上可如何以宣告變更增益集更新中有一些限制。提供者主控增益集，您可以實作執行不能以宣告完成的事項 [UpdatedEventEndpoint 處理常式](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)。
  
    
    
將元件新增至 [增益集很簡單。是要包含的增益集合格的任何元件也可以新增中更新。(如需元件可以為增益集的詳細資訊，請參閱 [類型的 SharePoint 元件可在 SharePoint 增益集](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps))。但是當您想要修改現有的元件以宣告，請考慮下列時間點。
  
    
    

- 無法在任何情況下都其初始部署之後變更清單或內容類型] 欄位 (欄) 的資料類型。特別是，請勿變更增益集更新 ( *不甚至是以程式設計方式*  )] 欄位的資料類型。或者，您可以新增新的欄位。如果增益集包含自訂項目建立、 編輯或檢視表單;請務必在這些表單進行對應的變更。例如，新增新欄位的 UI 並移除舊的 UI。(提供者主控增益集，您可以以程式設計方式將資料從舊的欄位移至新然後再刪除舊。)
    
  
- 無法更新標記中刪除清單、 清單執行個體、 內容類型或欄位。
    
  
- 無法從增益集的 web 更新標記中刪除檔案。不過，您可以變更任何檔案的內容。
    
  
- **CustomUpgradeAction**和 **MapFile**元素時，無法使用更新SharePoint Add-in，雖然可能會出現在Visual Studio intellisense 可用。
    
  

### 第一次更新增益集 web

本節中的程序說明如何新增或更新內容類型、 清單、 檔案及增益集 web 中的其他 SharePoint 元件。簡化了程序，我們假設所有元件都都在增益集的 web，單一功能的一部分，但增益集 web 可以有多個功能和您可以更新一個以上相同的更新。
  
    
    
Microsoft Office Developer Tools for Visual Studio會導向到建立新的增益集和工具的預設行為有時並不是最佳時您要更新增益集。若要取得更多控制權程序，您應該開始藉由停用功能設計者使用下列程序，以便您可以直接編輯 XML 的原始功能。
  
    
    

### 若要編輯功能 XML


1. 在 **方案總管**] 中開啟 _{FeatureName}_.features 檔案。Feature designer 中開啟。
    
  
2. 開啟 [ **清單**] 索引標籤，並依序展開 [ **編輯選項**。
    
  
3. 選擇 [ **覆寫產生 XML 及編輯 XML 編輯器中的資訊清單**。
    
  
4. 選擇 **[是]**以停用在設計工具提示。
    
  
5. 在開啟檢視中，選擇 [ **編輯 XML 編輯器中的資訊清單**。 _{FeatureName}_中。會開啟 Template.xml 檔。
    
   **開啟功能 XML 編輯器**

  

     ![Steps to open the Feature XML editor](images/UpdateAppOpenFeatureXML.png)
  

  

  

> **注意**
> 請勿加上"<!--->"  _{FeatureName}_.features 檔案的註解。註解不受支援的升級的基礎結構和註解的檔案中升級將會失敗。使用本文標記範例中只能以指出您您標記應移出。
  
    
    

使用下列步驟來更新增益集 web 功能。
  
    
    

### 若要更新的增益集 web 功能第一次


1. 遞增 **Version**元素之屬性的 [功能](http://msdn.microsoft.com/library/265cd648-1a7e-410f-a1d7-0da8c64b4006%28Office.15%29.aspx)，如果Office Developer Tools for Visual Studio沒有不尚未這麼做時遞增增益集資訊清單中的版本號碼。(工具不這麼做在每個案例中，因此您必須確認。)您應該使用您的增益集使用的相同版本號碼。您甚至應考慮引發功能版本時所要更新的增益集的其他元件，但不是增益集網頁本身的功能。(這在 [後續的增益集網路的更新](#SubsequentUpgrades)] 區段中討論)  [VersionRange](http://msdn.microsoft.com/library/cd715e38-6ec3-43b2-8007-6d0ed8865d91%28Office.15%29.aspx)元素的邏輯會比較容易管理的增益集版本和功能版本一定是相同時。
    
  
2. 不要刪除任何項目在 [ElementManifests](http://msdn.microsoft.com/library/d8d4db7e-2bc2-40c6-958b-d5683bdee87a%28Office.15%29.aspx) ] 區段中的檔案。不屬於刪除此區段。
    
  
3. 如果它們不是已經存在，請在檔案中新增下列元素：
    
  - **Feature**元素中 [UpgradeActions](http://msdn.microsoft.com/library/5af24ac1-a290-454d-b32b-bc7f7a4634f0%28Office.15%29.aspx)子元素。執行 *不* **ReceiverAssembly**或 **ReceiverClass**屬性新增至項目。當您要更新SharePoint Add-in，這些會有未使用。(這些屬性參照到SharePoint Add-ins不支援的自訂組件。如果您的增益集包括自訂組件，SharePoint 會安裝的增益集)。
    
  
  - **UpgradedActions**元素中 **VersionRange**子元素。Do 將 **BeginVersion**或 **EndVersion**屬性新增至項目。這些提供任何用途的增益集正在更新時第一次。他們使用討論的 [後續的增益集網路的更新](#SubsequentUpgrades)] 區段中。
    
  
  - **VersionRange**元素中 [ApplyElementManifests](http://msdn.microsoft.com/library/c087a0c3-1e27-4034-b4da-e025991454d6%28Office.15%29.aspx)子元素。
    
  

    此時檔案應該類似下列的範例。
    
    > **重要**
      > Office Developer Tools for Visual Studio可能已經新增上述標記並從 **ElementManifests** ] 區段中的某些項目複製到 **ApplyElementManifests** ] 區段中為圖例。 *刪除這些。*  雖然您最後可能有些放在稍後步驟後，它是更輕鬆地和比較安全空 **ApplyElementManifests**一節的開頭。重複的項目尚未變更的元件可以有錯誤的後果，包括還延長更新程序足夠的時間延展並失敗。



  ```XML
  
<Feature <!-- Some attributes omitted -->
               Version="2.0.0.0">
  <ElementManifests>
    <!-- ElementManifest elements omitted -->
  </ElementManifests>
  <UpgradeActions>
   <VersionRange>
     <ApplyElementManifests>
       
     </ApplyElementManifests>
   </VersionRange>
  </UpgradeActions>
</Feature>
  ```


### 若要將元件新增至 [增益集


1. 完全一樣如果您已建立新的SharePoint Add-in專案，請將任何新元件新增至功能。
    
  
2. 當您新增未在舊版本的增益集，例如對先前沒有清單、 增益集新增清單中的類型元件Office Developer Tools for Visual Studio會將 elements.xml 檔案新增至專案。這是個元件的元素資訊清單。您應該將新的版本號碼的增益集新增至這個檔案 (例如 elements.2.0.0.0.xml)。這可以是有用的疑難排解。請務必例如中 csproj 檔案和 XML 的功能變更據以進行在 **方案總管中**以確保參照至檔案的變更。
    
  
3. 針對每個新的元素資訊清單、 新增 [ElementManifest](http://msdn.microsoft.com/library/5a6a2865-5d31-45a2-a402-6da6e0f5567a%28Office.15%29.aspx)元素的子 **ElementManifests**及功能 xml **ApplyElementManifests**元素。(完全相同 **ElementManifest**元素在這兩個位置。) **Location**元素之屬性的應指向 elements.2.0.0.0.xml 檔案的相對路徑。例如，如果您已新增名為 MyCustomList 清單、 **ElementManifest**元素看起來下列。
    
  ```XML
  
<ElementManifest Location="MyCustomList\\elements.2.0.0.0.xml" />
  ```

4. 某些元件將檔案新增至專案。當您新增清單 ； 例如，建立一個 schema.xml 檔案並新增] 頁面上，建立] 頁面上的檔案。針對每一個這類檔案新增 [ElementFile](http://msdn.microsoft.com/library/bd43638e-8f18-4a0d-b122-1c055f97aa71%28Office.15%29.aspx)元素的子 **ElementManifests**元素。(請勿不將其新增至 **ApplyElementManifests**元素。) **Location**屬性應指向檔案的相對路徑。例如，如果您已新增清單、 schema.xml **ElementFile**元素看起來下列。
    
  ```XML
  <ElementFile Location="MyCustomList\\Schema.xml" />
  ```

5. 當您新增其他項目已經在舊版本的增益集的類型時， Office Developer Tools for Visual Studio可能會將參照新增至新的項目至而不是建立一個新的現有元素資訊清單。例如，是在 **方案總管中**的 [ **頁面**] 節點上按一下滑鼠右鍵，然後瀏覽整份 **新增標準的方式將頁面新增到增益集的 web |新項目 |頁面 | 新增**。 Office Developer Tools for Visual Studio會將新的 **File**元素新增至現有元素資訊清單檔案 (通常稱為 elements.xml) **Pages** 」 模組而不是建立新的元素資訊清單。
    
    這是不取捨。最佳做法是時可能、 編輯任何現有的元素資訊清單檔案更新增益集時避免 (亦即 any 元素資訊清單從舊版的增益集)。 一般而言，新項目應在新元素資訊清單檔案 (這兩個參照功能 XML **ApplyElementManifests**元素中)。(這種做法例外是一些說明稍後。)例如，若要新增新的頁面，採取下列步驟：
    
1. 建立新的模組名為Pages.2.0.0.0
    
  
2. 從其Office Developer Tools for Visual Studio新增自動移除 sample.txt 檔案。
    
  
3. 重新命名 elements.2.0.0.0.xml 在新模組中的元素資訊清單。
    
  
4. 以滑鼠右鍵按一下 **Pages.2.0.0.0**模組，然後瀏覽 **新增 |新項目 |頁面 |新增**。 建立新的頁面和參照，而不是 **頁面** **Pages.2.0.0.0**的元素資訊清單中。
    
  
5. 確認有新頁面的功能 XML **ElementManifests**元素中 **ElementsFile**元素，並確定是 **ElementManifest**元素的 **ElementManifests**和 **ApplyElementManifests**各節中的 elements.2.0.0.0.xml 檔案。
    
  

    在任何情況下Office Developer Tools for Visual Studio已經變更現有的元素資訊清單、 是手動建立新的 elements.2.0.0.0.xml 並移動已加入至標記的另一個作法舊清單至新的一個。(您可以將放在舊相同的 **方案總管]**節點中新增一個如果您想。)
    
  
6. 如果您將欄位新增至內容類型的功能，將 [AddContentTypeField](http://msdn.microsoft.com/library/cb04a3ac-f41a-4ffe-aaa1-d4bf3fb6347d%28Office.15%29.aspx)元素新增至 [ **VersionRange** ] 區段中。請務必將正確的值指派給 **ContentTypeId**和 **FieldId**屬性。(選用) 使用 **PushDown**屬性來指定新的欄位是否應該新增至任何衍生的內容類型。以下是範例。
    
  ```XML
  <VersionRange>
  <AddContentTypeField 
    ContentTypeId="0x0101000728167cd9c94899925ba69c4af6743e"
    FieldId="{CCDD361F-A3FB-40D8-A272-3A3C858F4116}"
    PushDown="TRUE" />
  <!-- Other child elements of VersionRange -->
</VersionRange>
  ```


### 若要修改現有的增益集的元件


1. 如果您已變更元素資訊清單檔案，例如，Default.aspx 檔案中所參照的檔案您沒有所有變更檔案的 **ElementFile**元素。但是您需要告知取代舊版本的檔案與新的更新基礎結構。您執行這項作業將模組的 **ElementManifest**元素新增至 [ **ApplyElementManifests** ] 區段中。由於中已經有這類元素 **ElementManifests** ] 區段中，只複製 (不移動) 以 **ApplyElementManifests**有時] 選項，但這只是建議您在資訊清單中所參照的每個檔案已變更。一般的作法是您不應該使用其本身的複本取代不變的檔案。這可以在某些情況下有損壞的效果。例如，如果已允許來自訂它的使用者設定] 頁面上，並取代其可能導致要移除的自訂。(如果您有變更] 頁面上，然後您必須接受此結果，但不想要加上客戶此不便 pointlessly)。
    
    若要確保只有在模組中的已變更的檔案所取代，建立只會參照變更的檔案模組的第二個元素資訊清單並套用 **ApplyElementManifests**中的第二個清單依照下列步驟。
    
1. 以滑鼠右鍵按一下 [ **方案總管**中的模組節點，並新增名為 elements.2.0.0.0.xml XML 檔案 (不是頁面)。
    
  
2. 在 [ **方案總管中**使其屬性窗格可見並將 **Deployment Type**屬性變更為 **ElementManifest**選取新的檔案。這是重要確定Office Developer Tools for Visual Studio控點檔案正確。
    
  
3. 將原始的資訊清單的內容複製到新的其中一個，並再刪除新的資訊清單會對應至檔案 **已變更** 的所有 [檔案](http://msdn.microsoft.com/library/c270e4ce-8110-4da7-b0e7-c223604bfce7%28Office.15%29.aspx)元素。
    
  
4. 將 **ElementManifest**元素新增至新的資訊清單檔案例如在此範例會參照 **ApplyElementManifests** ] 區段。
    
  ```XML
  
<ElementManifest Location="Pages\\elements.2.0.0.0.xml" />
  ```


    > **注意事項**
      > 不要刪除原始的資訊清單。功能 XML 會使用這兩個舊的和新的錯誤。> 不要複製任何 **ElementFile**元素從 **ElementManifests**區段 **ApplyElementManifests**小節即使已變更為 **ElementFile**中所參照的檔案。
2. 開啟所參照的 **ApplyElementManifests** ] 區段中的每個元素資訊清單檔案，並確保任何 [檔案](http://msdn.microsoft.com/library/c270e4ce-8110-4da7-b0e7-c223604bfce7%28Office.15%29.aspx)元素具有 **ReplaceContents**屬性和它設為 **TRUE**。以下是範例。Office Developer Tools for Visual Studio可能已完成這已經，不過您應該驗證它。這樣即使的元素資訊清單從舊版的增益集。這是一個很少的方式在其中的編輯現有的元素資訊清單檔案很好的作法。
    
  ```XML
  <Module Name="Pages">
  <File Path="Pages\\Default.aspx" Url="Pages/Default.aspx" ReplaceContent="TRUE" />
</Module>
  ```

3. 頁面可以內嵌在其 [在 [增益集在 web 上網頁中包含網頁組件](include-a-web-part-in-a-webpage-on-the-add-in-web.md)所述的網頁組件。如果變更網頁組件對網頁 (或變更的網頁組件屬性)，則沒有額外的步驟： 您必須將下列標記新增至頁面以防止SharePoint新增放入頁面的 [網頁組件的第二個複本。標記應新增至具有識別碼 `PlaceHolderAdditionalPageHead` **asp:Content**項目。( Office Developer Tools for Visual Studio可能都已經新增它時先建立] 頁面上，不過您應該驗證它有。)
    
  ```XML
  
<meta name="WebPartPageExpansion" content="full" />
  ```


    > **注意事項**
      > 如果頁面已設定為允許使用者自訂它，然後此標記的效果端移除這些自訂項目。使用者必須重複執行它們。> 如果網頁組件新增至 [在 [增益集在 web 上網頁中包含網頁組件](include-a-web-part-in-a-webpage-on-the-add-in-web.md)，然後的網頁組件的標記中的指引是元素資訊清單] 頁面上下列項目，所以變更網頁組件屬性是您不應編輯元素資訊清單檔案的增益集更新一部分的一般規則的例外狀況。
4. 若要變更頁面或者，也可以選擇使用重新導向至新的頁面使用下列步驟。
    
1. 建立新的頁面並設定其更新標記 **新增元件至增益集** 上述程序中所述。
    
  
2. 開啟 「 舊 」 頁面，並移除具有識別碼 `PlaceHolderAdditionalPageHead` **asp:Content**元素的所有標記。
    
  
3. 新增下列標記 **asp:Content**元素，並再 _{RelativePathToNewPageFile}_取代為新的路徑和檔案名稱。此指令碼會將瀏覽器重新導向至新的頁面及包含查詢參數。它也會保持原有的頁面不在瀏覽器歷程記錄。
    
  ```
  <script type="text/javascript">
        var queryString = window.location.search.substring(1);
        window.location.replace("{RelativePathToNewPageFile}" + "?" + queryString);
</script>
  ```

4. 刪除頁面上的任何其他 **asp:Content**元素。
    
  
5. 如果您要取代的頁面為增益集的 [開始] 頁面上，變更 **StartPage**元素中的增益集資訊清單以指向新的頁面。
    
  
5. 如果增益集的增益集 web 包含 **CustomAction**或 **ClientWebPart**，而且您修改該更新的一部分，您必須修改元素資訊清單由於這是這些元件定義的位置。(這是您應該不編輯從舊版的增益集的元素資訊清單的增益集在更新時的一般做法例外)。您也必須複製 (不移動) **ElementManifest**元素 **ElementManifests**區段 **ApplyElementManifests**小節。
    
  

#### 功能 XML 範例的第一次升級增益集

以下是完成 _{FeatureName}_的範例。增益集的第一次更新的 Template.xml 檔。已更新增益集在此範例中包含修改的 Default.aspx 檔案 `Pages\\Elements.xml`檔案中所參照的和它部署三個新的 jQuery 檔案、  `Scripts\\Elements.xml`檔案中參考的每一個成員。請注意所有 **ElementFile**s 移 **ElementManifests** ] 區段中，記下 `<ElementManifest Location="Pages\\Elements.xml" />`已複製方式 (不會移動) 從 **ElementManifests** ] 區段中 **ApplyElementManifests**小節。
  
    
    

```XML

<Feature xmlns="http://schemas.microsoft.com/sharepoint/" Title="MyApp Feature1"
      Description="SharePoint Add-in Feature" Id="85d309a8-107e-4a7d-b3a2-51341d3b11ff" 
      Scope="Web" Version="2.0.0.0">
  <ElementManifests>
    <ElementFile Location="Pages\\Default.aspx" />
    <ElementManifest Location="Pages\\Elements.xml" />
    <ElementFile Location="Content\\App.css" />
    <ElementManifest Location="Content\\Elements.xml" />
    <ElementFile Location="Images\\AppIcon.png" />
    <ElementManifest Location="Images\\Elements.xml" />
    <ElementFile Location="Scripts\\jquery-3.0.0.intellisense.js" />
    <ElementFile Location="Scripts\\jquery-3.0.0.js" />
    <ElementFile Location="Scripts\\jquery-3.0.0.min.js" />
  </ElementManifests> 
  <UpgradeActions>
      <VersionRange>      
        <ApplyElementManifests>
          <ElementManifest Location="Pages\\Elements.xml" />
          <ElementManifest Location="Scripts\\elements.2.0.0.0.xml" />
        </ApplyElementManifests>
      </VersionRange>
  </UpgradeActions>
</Feature>

```


### 後續的增益集網路的更新
<a name="SubsequentUpgrades"> </a>

當您更新SharePoint Add-in的第二個 (或第三和等等) 的時間時，您必須考慮的客戶的一些可能不進行先前的更新。如果使用者回應 「 更新是可用的"提示後最新的更新部署至組織的增益集目錄或Office Store，可能會透過單一更新程序中的多個版本更新其執行個體中之增益集。大致上，這是完全什麼應執行: 您想更新為最新版本的增益集的每個舊版。但不是一定要每個 [update] 動作的增益集 web 再度發生的每個執行個體中之增益集的功能。有一些應該不會發生多次指定增益集執行個體的更新動作。例如，如果您將欄位新增至一個更新中的內容類型時，您不想再新增 [下次更新該欄位。下列程序示範如何使用 update 動作發生根據即將更新之功能的版本控制 **VersionRange**元素。
  
    
    

### 若要變更增益集 web 功能更新版本的更新


1. 開啟 _FeatureName_。Template.xml 檔案進行編輯 **編輯功能 XML** 稍早在本文中的程序所述和遞增 **Version**元素之屬性的 [功能](http://msdn.microsoft.com/library/265cd648-1a7e-410f-a1d7-0da8c64b4006%28Office.15%29.aspx)。您應該使用相同的版本號碼時所使用的增益集的功能。
    
    基於延續範例，我們假設您先前已更新增益集從 1.0.0.0 版版本 2.0.0.0，並立即更新其版本 3.0.0.0。所以設 3.0.0.0 **Version**屬性。
    
  
2. 新增新 **VersionRange**元素底下所有現有的 **VersionRange**元素。執行 *不*  將 **BeginVersion**或 **EndVersion**屬性新增至這個項目。
    
  
3. 若要計算您此功能的更新版本中所做的變更本文稍早的 **增益集 web 功能第一次更新** 程序中所述會填入 **VersionRange**元素。每當 **ApplyElementManifests** ] 區段中的程序參照，視為這是您剛新增; **VersionRange**元素的子系 **ApplyElementManifests**元素參照也就是 *最低*  一個功能 XML 檔案中。
    
  
4. 移至 [上一個 **VersionRange**元素  您上次您新增一個更新增益集 (從 2.0.0.0 延續範例中的 1.0.0.0)  和 **EndVersion**屬性新增至其中。要在此 **VersionRange**的升級動作套用至任何版本的增益集的他們已經尚未被套用 (1.0.0.0 版)，但是您不想讓它們可以重新套用至以交易已套用 (版本 2.0.0.0) 的版本。 **EndVersion**值是 *互斥*  ，讓您將它設為您執行動作 *不*  想要升級動作套用的最低版本。在繼續範例中，您設定它以 2.0.0.0。您的檔案應該類似下列。
    
  ```XML
  
<Feature <!-- Some attributes omitted -->
               Version="3.0.0.0">
  <ElementManifests>
    <!-- ElementManifest elements omitted -->
  </ElementManifests>
  <UpgradeActions>
    <VersionRange EndVersion="2.0.0.0">
      <!--  Child elements for upgrade from 1.0.0.0 to 2.0.0.0 go here. -->
    </VersionRange>
   <VersionRange>
      <!--  Child elements for upgrade from 2.0.0.0 to 3.0.0.0 go here. -->
   </VersionRange>
  </UpgradeActions>
</Feature>
  ```


    每當您升級功能，請遵循相同的圖樣。新增新 **VersionRange**最新的更新動作。然後將 **EndVersion**元素新增至 *舊*  的 **VersionRange**項目並將它設為先前的版本號碼。在繼續範例中，將檔案應當下列更新從 3.0.0.0 4.0.0.0。
    


  ```XML
  
<Feature <!-- Some attributes omitted -->
               Version="4.0.0.0">
  <ElementManifests>
    <!-- Child elements omitted -->
  </ElementManifests>
  <UpgradeActions>
    <VersionRange EndVersion="2.0.0.0">
       <!-- Child elements for upgrade from 1.0.0.0 to 2.0.0.0 go here. -->
    </VersionRange>
    <VersionRange EndVersion="3.0.0.0">
       <!-- Child elements for upgrade from 2.0.0.0 to 3.0.0.0 go here. -->
    </VersionRange>
    <VersionRange>
       <!-- Child elements for upgrade from 3.0.0.0 to 4.0.0.0 go here. -->
    </VersionRange>
  </UpgradeActions>
</Feature>
  ```


    請注意的最新的 **VersionRange**元素有無 **BeginVersion**或 **EndVersion**屬性。這可確保的升級移到此 **VersionRange**元素的動作會套用至所有舊版的功能，這是您要因為所有最新變更參照此 **VersionRange**，而且不已經發生的任何功能的執行個體。
    
    請注意 **BeginVersion**屬性不適用於任何 **VersionRange**s。這是因為 **BeginVersion**屬性的預設值為 0.0.0.0，而您想因為您想要套用至每個執行個體以前版本 **EndVersion**屬性中指定的增益集的所有升級動作的值。
    
    > **重要**
      > **VersionRange**元素會決定僅功能之哪個版本升級會套用至。它不會判斷哪個版本的增益集取得有可用的更新通知  通知觸發只能由增益集版本號碼。要用於組織的增益集目錄或Office Store、 增益集] 中的每個已安裝執行個體的增益集的新版本的 24 小時內的版本，不管已有可用的更新通知會出現在其並排顯示在 [ **網站內容**] 頁面上。> **VersionRange**不會影響新升級的功能或剛更新的增益集的新版本號碼。這些兩個號碼永遠會變更為最新的版本號碼，不論此功能已在升級前何種版本範圍。這可避免使用 **BeginVersion**屬性的另一個很好的理由。 **BeginVersion**屬性可用來封鎖從屬於發生在某些增益集執行個體上一些升級動作。但它不能封鎖的功能或從要乘以次方的最新版本的增益集 (英文) 版本。因此 **BeginVersion**屬性使用無法建立增益集的兩個執行個體可能有相同的增益集版本號碼及相同增益集 web 功能版本號碼，但其增益集的 web 中有不同元件的狀況。

## 確認部署增益集 web 元件
<a name="VerifyDeployAppWebComp"> </a>

請遵循下列步驟來確認增益集 web 功能的部署和其元件。
  
    
    

### 若要確認的增益集網站佈建


1. 開啟 [ **網站設定**] 頁面上的主機網站。在 [ **網站集合管理**] 區段中選擇的 **網站階層**連結。
    
  
2. 在 **網站階層**] 頁面上，您會看見增益集 web 及其 URL 依列出。不要啟動它。而複製的 URL，並使用 URL 中的其餘步驟。
    
  
3. 瀏覽至 [  _URL_of_app_web_/_layouts/15/ManageFeatures.aspx 並開啟 [ **網站功能**] 頁面，確認此功能是依字母順序排列的功能清單的成員且其狀態為 **作用中**。
    
  
4. 如果增益集 web 功能包含自訂網站欄，請開啟 _URL_of_app_web_/_layouts/15/mngfield.aspx 並在開啟 [ **網站欄**] 頁面，確認 [列出新的自訂網站欄。
    
  
5. 如果增益集 web 功能包含任何自訂內容類型，請開啟 _URL_of_app_web_/_layouts/15/mngctype.aspx 並在開啟 [ **網站內容類型**] 頁面，確認新的內容類型已列出。
    
  
6. 每個自訂的內容類型及每個要新增一欄的內容類型，選擇 [內容類型的連結。在開啟 [ **網站內容類型**] 頁面，確認內容類型具有應有的網站欄。
    
  
7. 如果增益集 web 功能包含任何清單執行個體、 開啟 _URL_of_app_web_/_layouts/15/mcontent.aspx 及，開啟 [ **網站文件庫及清單**] 頁面上，確認有 **自訂"name_of_list"**連結的每個自訂清單執行個體。
    
  
8. 針對每個這些自訂清單執行個體中，選擇 [ **自訂"name_of_list"** ] 連結，並確認 [清單設定] 頁面上清單有預期的內容類型和欄。
    
    > **注意事項**
      > 如果頁面上有無 **內容類型**] 區段中，您必須啟用 [管理內容類型。選擇 [ **進階設定**] 連結，在 [進階設定] 頁面上啟用 [管理內容類型，然後選擇 **[確定]**。回到上一個] 索引標籤和現在有 **內容類型**] 區段中的清單。
9. 頁面頂端附近是清單的 **網頁地址**。如果您清單執行個體定義中包含範例項目，請複製地址並將它貼到您的瀏覽器的位址列，然後瀏覽至清單。確認清單中具有您所建立的範例項目。
    
  

## 後續步驟
<a name="Next"> </a>

返回 [在增益集更新的主要步驟](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接移至其中的下列文章，以了解如何更新您SharePoint Add-in的下一個主要元件。
  
    
    

-  [更新 SharePoint 2013 中的主機 web 元件](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [建立更新事件處理常式中 SharePoint 增益集](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [更新遠端元件的 SharePoint 增益集](update-remote-components-in-sharepoint-add-ins.md)
    
  

## 其他資源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 增益集](update-sharepoint-add-ins.md)
    
  
- 在Microsoft SharePoint 2010 Software Development Kit (SDK) [How to: Add Elements to an Existing Feature](http://msdn.microsoft.com/library/b007f419-e0d6-4e3a-a3ae-b8e448656d02%28Office.15%29.aspx) 。
    
  
- 在Microsoft SharePoint 2010 Software Development Kit (SDK) [Upgrading Features](http://msdn.microsoft.com/library/e917f709-6491-4d50-adbe-2ab8f35da990%28Office.15%29.aspx) 。
    
  

