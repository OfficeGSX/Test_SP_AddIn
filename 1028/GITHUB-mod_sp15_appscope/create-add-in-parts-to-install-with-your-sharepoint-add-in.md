---
title: 建立增益集組件安裝與您 SharePoint 的增益集
ms.prod: SHAREPOINT
ms.assetid: a2664289-6c56-4cb1-987a-22367fad55eb
---


# 建立增益集組件安裝與您 SharePoint 的增益集
了解如何建立可在網頁組件庫中的主機網站時，安裝您SharePoint Add-inSharePoint 2013增益集組件。
使用增益集組件，即可顯示您的增益集使用者體驗中SharePoint網站頁面的右邊。增益集組件會顯示網頁 (這通常是互動式表單或資料的動態顯示) 您使用指定的 (也稱為框架) **IFrame**中 [主機 web](http://msdn.microsoft.com/library/fp179925.aspx)一頁。 如需增益集組件的詳細背景，請參閱下列文章：
  
    
    


-  [從使用者介面存取增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#AccessingApp)
    
  
-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [將 SharePoint 使用者介面擴充增益集](sharepoint-add-ins-ux-design-guidelines.md#UXGuide_Extending)
    
  

圖 1 顯示的增益集組件內容SharePoint頁面中的顯示方式。
  
    
    


**圖 1。增益集組件內容顯示在 SharePoint 頁面**

  
    
    

  
    
    
![App part content displayed in a SharePoint page](images/AppParts_ConceptualArchitecture.png)
  
    
    
 **ClientWebPart** 類別會實作增益集組件很類似所有網頁組件可在網頁組件庫中之後使用者會安裝SharePoint Add-in包含它。您的使用者可以進一步自訂增益集組件使用您所提供的屬性。(請參閱圖 2 下方的增益集組件中的可設定屬性的範例)。本文中的範例會使用網頁必需的遠端伺服器上，不在SharePoint，作為 [內容] 頁面。請記住您也可以使用SharePoint頁面以主控 [其他常見案例增益集組件](#SP15Createappparts_Nextsteps)] 區段中稍後本文所述的增益集組件內容。
## 此範例使用本文中的先決條件
<a name="SP15Createappparts_Prereq"> </a>

若要遵循此範例中的步驟，您需要下列項目：
  
    
    

- Visual Studio
    
  
- Microsoft Office Developer Tools for Visual Studio
    
  
- SharePoint開發環境。如果您需要設定開發環境的說明，請參閱 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)。
    
  

  
    
    

## 建立增益集組件的主機網站上安裝
<a name="SP15Createappparts_Codeexample"> </a>

有幾項工作才能建立及增益集組件安裝到主機網站：
  
    
    

1. 建立SharePoint Add-in和遠端 web 專案。
    
  
2. 新增增益集組件內容的表單。
    
  
3. 將增益集組件新增至SharePoint Add-in專案。
    
  
完成工作後，您的增益集組件應該看起來類似圖 2 的增益集組件處於編輯模式時。我們可以看到 (1) 增益集的內容顯示在SharePoint ] 索引標籤和 (2) 的增益集組件的自訂屬性。
  
    
    

**圖 2。SharePoint] 頁面上裝載的基本增益集組件**

  
    
    

  
    
    
![Web part page hosting a basic app part](images/BasicAppPart_result.png)
  
    
    

### 新增表單之增益集組件內容


1. 建立提供者主控SharePoint Add-in [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)、 所述，但TestAppPart為專案的名稱。
    
  
2. 已建立Visual Studio解決方案之後，以滑鼠右鍵按一下 [web 應用程式專案 (非SharePoint Add-in專案) 並選擇 [ **新增]**新增新的 Web 表單 > **新增項目**> **Web** > **Web 表單**。名稱表單AppPartContent.aspx。
    
  
3. AppPartContent.aspx 檔案並中取代整個 html 元素且其具有下列 HTML 程式碼的子項。讓上面的 html 元素的所有標記都保持原狀。HTML 程式碼包含JavaScript執行下列工作：
    
  - 從查詢字串中抽選屬性預設值
    
  
  - 轉譯的屬性值
    
  

    請注意程式碼預期查詢字串中有一些參數。讓網頁可以使用這些增益集組件會提供其透過查詢字串的自訂屬性。下一個任務說明如何將宣告的自訂屬性及如何使其能增益集] 網頁。
    


  ```HTML
  
<html>
    <body>
        <div id="content">
            <!-- Placeholders for properties -->
            String property: <span id="strProp"></span><br />
            Integer property: <span id="intProp"></span><br />
            Boolean property: <span id="boolProp"></span><br />
            Enumeration property: <span id="enumProp"></span><br />
        </div>

    <!-- Main JavaScript function, controls the rendering
         logic based on the custom property values -->
    <script lang="javascript">
        "use strict";

        var params = document.URL.split("?")[1].split("&amp;");
        var strProp;
        var intProp;
        var boolProp;
        var enumProp;

        // Extracts the property values from the query string.
        for (var i = 0; i < params.length; i = i + 1) {
            var param = params[i].split("=");
            if (param[0] == "strProp")
                strProp = decodeURIComponent(param[1]);
            else if (param[0] == "intProp")
                intProp = parseInt(param[1]);
            else if (param[0] == "boolProp")
                boolProp = (param[1] == "true");
            else if (param[0] == "enumProp")
                enumProp = decodeURIComponent(param[1]);
        }

        document.getElementById("strProp").innerText = strProp;
        document.getElementById("intProp").innerText = intProp;
        document.getElementById("boolProp").innerText = boolProp;
        document.getElementById("enumProp").innerText = enumProp;
    </script>
    </body>
</html>
  ```

4. 儲存後關閉檔案。
    
  

### 將增益集組件新增至 SharePoint 增益集專案


1. 以滑鼠右鍵按一下SharePoint Add-in專案 (非 web 應用程式專案)，並再選擇 [ **新增]** > **新增項目**> **Office/SharePoint** > **用戶端網頁組件 (由 Web 主機)**。 (「 用戶端網頁組件 」 是另一個名稱為"增益集組件 」)。
    
  
2. 名稱的組件基本增益集組件。
    
  
3. 在 [ **指定用戶端網頁組件頁面**] 對話方塊中，選擇 [ **選取或輸入現有的 web 頁面的 URL**。在下拉式清單中選擇 [ **TestAppWebPart/AppPartContent.aspx** ] 頁面。(您是否已進行您選擇之後，頁面 URL 可能會出現在方塊中具有"TestAppWebPart"取代為 **~ remoteAppUrl** 、 **{StandardTokens}**的查詢參數新增具有。)
    
  
4. 選擇 [ **完成**]。
    
  
5. 以滑鼠右鍵按一下 [在 **方案總管**] 中的 **基本增益集組件**並選擇 [ **屬性**]。
    
  
6. 在 [ **內容**] 窗格中，選取 [ **自訂屬性**，選擇 [註標(...) 按鈕。
    
  
7. 若要將四個自訂屬性加入至增益集組件使用 **自訂屬性**] 對話方塊。您必須設定的每一個四個自訂屬性的五個屬性。下表 1 列出的屬性名稱和值。建立使用下列程序的屬性。
    
1. 選擇 [新增]。
    
  
2. 在 [屬性] 清單中選取的第一個屬性表 1: **DefaultValue**。
    
  
3. 將此值，例如字串預設值。
    
  
4. 選取下一個屬性 **名稱**，並將其值，例如strProp。
    
  
5. 繼續使用 **Type**、 **WebCategory**及 **WebDisplayName**屬性。
    
  
6. 選擇 [ **新增]**並重複此程序的所有表格 1 的四個資料列。 不要 *關閉對話方塊*  。
    
   **表 1。增益集組件的自訂屬性的屬性**


|**DefaultValue**|**名稱**|**類型**|**WebCategory**|**WebDisplayName**|
|:-----|:-----|:-----|:-----|:-----|
|字串預設值 <br/> |strProp <br/> |字串 <br/> |基本的增益集組件類別 <br/> |將類型字串的屬性 <br/> |
|0 <br/> |intProp <br/> |int <br/> |基本的增益集組件類別 <br/> |整數類型之屬性 <br/> |
|false <br/> |boolProp <br/> |boolean <br/> |基本的增益集組件類別 <br/> |屬性輸入布林值 <br/> |
|1st <br/> |enumProp <br/> |列舉 <br/> |基本的增益集組件類別 <br/> |將屬性的類型列舉 <br/> |
   

    此時對話方塊看起來應該類似如下：
    

   **ClientWebPart 的自訂屬性] 對話方塊**

  

     ![[用戶端網頁組件自訂內容] 對話方塊左側列出 4 個內容，且每個內容的右邊設有 5 個屬性。](images/6a0f0a56-3184-490a-af19-b51b7545671d.PNG)
  

  

  
8. 選取 **enumProp** 屬性、 選取 **EnumItems**屬性，然後選擇註標(...) 按鈕。
    
  
9. 使用 **ClientWebPartEnumItem 集合編輯器**新增三個項目。您必須設定每三個的兩個屬性。表格 2 中列出的屬性名稱和值。建立使用下列程序的屬性。
    
1. 選擇 [新增]。
    
  
2. 在 [屬性] 清單中選取表格 2 的第一個屬性： **值**。
    
  
3. 設定屬性，例如，第 1的值。
    
  
4. 選取下一個屬性 **WebDisplayName**，並將其值，例如第一個選項。
    
  
5. 選擇 [ **新增]**並重複此程序之表格 2 的所有列。
    
   **表 2。列舉項目之 enumProp 屬性**


|**值**|**WebDisplayName**|
|:-----|:-----|
|1st <br/> |第一個選項 <br/> |
|2nd <br/> |第二個選項 <br/> |
|3rd <br/> |第三個選項 <br/> |
   

    完成時] 對話方塊應如下所示：
    

   **ClientWebPartEnumItem 集合編輯器**

  

     ![用戶端網頁組件列舉項目集合編輯器有 3 個列出的項目，且每個項目均有值屬性和網頁顯示名稱屬性。](images/1b21d968-bd58-4f30-a019-84df6181b81a.PNG)
  

  

  
6. 選擇 **[確定]**以關閉 [] 對話方塊，然後選擇 [ **確定**] 以關閉 [ **自訂屬性**] 對話方塊。
    
  
10. Visual Studio會產生下列 XML 程式碼在 elements.xml 檔案中的增益集組件 (分行符號以利檢視)。請注意， **ClientWebPart**元素的 **Title**屬性設為"Basic 增益集組件標題 」 和描述會設為"基本增益集組件描述"。從第一筆、 刪除單字"Title"並將第二個取代基本增益集組件。
    
  ```XML
  
<?xml version="1.0" encoding="UTF-8"?>
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
    <ClientWebPart
        Name="Basic add-in part"
        Title="Basic add-in part Title"
        Description="Basic add-in part Description" >
        
        <!--  The properties are passed through the query string 
                using the following notation: _propertyName_
                in the Src property of the Content element.  
          -->
        <Content
            Src="~remoteAppUrl/AppPartContent.aspx?strProp=_strProp_&amp;amp;intProp=_intProp_&amp;amp;boolProp=_boolProp_&amp;amp;enumProp=_enumProp_"
            Type="html"/>
        <Properties>
            <Property
                Name="strProp"
                Type="string"
                RequiresDesignerPermission="true"
                DefaultValue="String default value"
                WebCategory="Basic add-in part category"
                WebDisplayName="A property of type string.">
            </Property>
            <Property
                Name="intProp"
                Type="int"
                RequiresDesignerPermission="true"
                DefaultValue="0"
                WebCategory="Basic add-in part category"
                WebDisplayName="A property of type integer.">
            </Property>
            <Property
                Name="boolProp"
                Type="boolean"
                RequiresDesignerPermission="true"
                DefaultValue="false"
                WebCategory="Basic add-in part category"
                WebDisplayName="A property of type boolean.">
            </Property>
            <Property
                Name="enumProp"
                Type="enum"
                RequiresDesignerPermission="true"
                DefaultValue="1st"
                WebCategory="Basic add-in part category"
                WebDisplayName="A property of type enum.">
                <EnumItems>
                    <EnumItem WebDisplayName="First option" Value="1st"/>
                    <EnumItem WebDisplayName="Second option" Value="2nd"/>
                    <EnumItem WebDisplayName="Third option" Value="3rd"/>
                </EnumItems>
            </Property>
        </Properties>
    </ClientWebPart>
</Elements>               

  ```


### 設為主機網站首頁上的增益集開始頁面


1. 繼續範例SharePoint Add-in都不會有任何增益集的 web 和其遠端 web 應用程式存在只是為了主控表單。沒有任何完整頁面、 此增益集的沈浸式經驗。讓增益集的 [開始] 頁面上應設為 [首頁] 頁面上的主機網站。
    
    若要開始，在 **方案總管**中選取SharePoint Add-in專案 (非 web 應用程式專案) 並複製 **網站 URL**屬性，包括通訊協定 (例如 **https://contoso.sharepoint.com**) 至剪貼簿的值。
    
  
2. 開啟的增益集資訊清單，並再貼到 [ **開始] 頁面**] 方塊中的 [URL。
    
  
3. (選用) 您可以從 web 應用程式專案中，刪除 Default.aspx 頁面由於不適用於SharePoint Add-in。
    
  

### 建立及測試解決方案


1. 按 F5 鍵。
    
    > [!注意事項]
      > 當您按 F5 時、 Visual Studio建置解決方案、 安裝增益集，並開啟的增益集的 [權限] 頁面。
2. 選擇 [ **信任它**] 按鈕。
    
  
3. 從增益集組件庫中新增 **基本增益集組件**。如需詳細指示，請參閱 [新增增益集組件至頁面](https://support.office.com/article/Add-an-App-Part-to-a-page-6f06c0b7-44b8-4c69-b4ad-85197eee8d78)。
    
    當增益集的主機網站上安裝時， **基本的增益集組件**增益集組件庫中是可用。它應該許多看來如圖 3。
    

   **圖 3。增益集組件增益集組件庫中**

  

     ![Basic app part in the web part gallery](images/BasicAppPart_gallery.jpg)
  

  

  
4. 新增增益集組件後，選擇 [向下箭號標題右邊的 **基本增益集組件**、 標題，然後選擇 [ **編輯網頁組件**。
    
    您應該會看到類似圖 1 上述的編輯模式中的增益集組件。
    
  
5. 開啟 **基本增益集組件類別**，並變更之部分屬性值。
    
  
6. 按一下 **[確定]**儲存變更並確認在 「 增益集組件中已變更屬性。
    
  
7. 如果結束偵錯工作階段，您將不會使用 F5 一次此專案上一段時間，它是最佳作法以確保測試增益集組件已從您的首頁撤銷SharePoint Add-in一的最後一次。以滑鼠右鍵按一下SharePoint Add-in專案並選擇 **Retract**。
    
  

## 疑難排解
<a name="SP15Createappparts_Codeexample"> </a>


**表 3。解決方案的疑難排解**


|**問題**|**解決方法**|
|:-----|:-----|
|增益集組件不會顯示任何內容。增益集組件會顯示下列錯誤： **瀏覽至網頁已取消** 。因為在瀏覽器已封鎖內容] 頁面上發生此錯誤。 <br/> |啟用混合的內容。此程序可能會根據您所使用的瀏覽器不同： <br/> Internet Explorer 9 和 10 請在頁面底部顯示下列訊息： **安全的內容會顯示** 。選擇要顯示的增益集組件內容的 [ **顯示所有內容**。 <br/> Internet Explorer 8 會顯示下列訊息對話方塊： **您想要檢視僅限已安全地傳遞的網頁內容吗？** 選擇 [ **否**] 以顯示的增益集組件內容]。 <br/> 或者，您可以啟用混合您正在處理之網際網路區域中的內容。適用於大部分的開發人員網際網路區域是 **近端內部網路** 。如果這不是您的大小寫、 替代 **近端內部網路**的您正在處理的網際網路區域。 <br/> 在 Internet Explorer 中，選擇 [ **工具]** > **網際網路選項**。 <br/> 在 [ **網際網路選項**] 對話方塊的 [ **安全性**] 索引標籤上選擇 [ **近端內部網路**，，然後選擇 [ **自訂層級**] 按鈕。 <br/> 在 [ **安全性設定**] 對話方塊中，啟用 [ **其他**] 區段中的 [ **顯示混合式的內容**。 <br/> |
   

## 其他常見案例增益集組件
<a name="SP15Createappparts_Nextsteps"> </a>

本文說明如何使用遠端網頁作為 [內容] 頁面上的自訂屬性建立基本增益集組件。您也可以探索下列案例和相關增益集組件的詳細資訊。
  
    
    

### SharePoint 頁面作為 [內容] 頁面

在大多數情況下，網頁無法顯示在框架中若將 **X 框架選項** HTTP 標頭傳送回應。根據預設， SharePoint頁面包含 **X 框架選項** 標頭。如果您使用增益集在 web 上主控SharePoint網頁，您可能會執行到下列的錯誤 (如圖 4 所示)： **無法在框架中顯示此內容**
  
    
    

**圖 4。增益集組件無法在框架中顯示其內容**

  
    
    

  
    
    
![App part that can't display its content in a frame](images/AppParts_IFrameError.png)
  
    
    
請注意的特定情況下都很容易" [ClickJacking](http://blogs.msdn.com/b/ieinternals/archive/2010/03/30/combating-clickjacking-with-x-frame-options.aspx)"攻擊時網頁會顯示在框架中。謹慎評估您的增益集組件分析藍本以確保沒有任何 **ClickJacking** 攻擊的風險。
  
    
    
如果您主控增益集在 web 上的頁面不容易 ClickJacking 攻擊，您可以使用 **AllowFraming**網頁組件抑制您的頁面回應的 **X 框架選項** 標頭。下列程式碼範例會示範如何使用 SharePoint 」 頁面上的 **AllowFraming**網頁組件。將此標記複製到主控增益集在 web 的頁面。將它放在頁面中的第一個 **asp:content**元素的正上方。它不應該是任何其他元素的子項。
  
    
    



```XML

<WebPartPages:AllowFraming ID="AllowFraming1" runat="server" />
```

您可以下載示範如何使用SharePoint頁面作為 [內容] 頁面上的 [增益集組件的程式碼範例](http://code.msdn.microsoft.com/SharePoint-2013-Display-be8dac16) 。
  
    
    

### 調整大小的增益集組件

如果您使用增益集組件中的動態內容，內容可能會變更其寬度和高度。動態內容的性質，因為它可能不符合圖文框中。您也可以使用太多空間。動態內容可能很難在您的增益集組件宣告中指定固定的大小。不過，您可以調整大小以配合內容的寬度和高度的圖文框。
  
    
    
您可以使用從您的內容網頁的張貼訊息至指定的圖文框的大小。下列JavaScript範例將示範如何傳送調整大小主控的增益集組件圖文框的張貼訊息。 一般而言，您必須這JavaScript方法在您呼叫從頁面JavaScript檔案中。例如，頁面無法有使用者指定的增益集組件] 視窗大小的控制項。自訂方法會再從 **onchange**處理常式呼叫的控制項。如需完整的範例，請參閱 [程式碼範例： 調整增益集組件動態中 SharePoint 增益集](http://code.msdn.microsoft.com/officeapps/SharePoint-2013-Resize-app-594acc88)。
  
    
    



```
window.parent.postMessage("<message senderId={SenderId}>resize(120, 300)</message>", {hostweburl});
```

在上述範例中， **senderId**值將會在頁面上的查詢字串自動進行設定的增益集組件程式碼轉譯頁面。您] 頁面上就只需要讀取 **SenderId**值登出的查詢字串及要求調整時使用它。您可以主機網頁 URL 的查詢字串來擷取附加 **StandardTokens**或 **HostUrl**權杖中增益集組件定義的 **Src** 屬性。您可以下載 [調整增益集組件的程式碼範例](http://code.msdn.microsoft.com/officeapps/SharePoint-2013-Resize-app-594acc88)查看增益集組件動態調整大小。
  
    
    

### 在您的增益集組件內容中使用 SharePoint 樣式表

由於增益集組件常駐SharePoint ] 頁面上，您可能要增益集組件內容外觀像是很的頁面部分。若要達到類似的外觀與風格的其中一個方法是使用相同樣式類別做為裝載的增益集組件的SharePoint頁面。您可以提供SharePoint網站的樣式表至增益集組件新增增益集 web **defaultcss.ashx** 檔案的參照。
  
    
    
您可以看到 [使用 SharePoint 網站的樣式表中 SharePoint 增益集](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)如需說明如何參照中您SharePoint Add-ins **defaultcss.ashx** 檔案。您也可以下載 [coffeemaker 程式碼範例](http://code.msdn.microsoft.com/office/SharePoint-2013-App-part-9d83703c)查看增益集組件參照樣式表。
  
    
    

### 偵測增益集組件處於編輯模式時

使用者可以編輯的增益集組件來變更它的屬性。例如，使用者可能會想要變更的其中一個增益集組件的 **外觀**] 或 [ **版面配置**屬性。(請參閱上述的圖 2)。如果增益集組件是在編輯模式中，您可能會想要修改呈現邏輯或避免發生的一些不需要處理。 例如，考慮增益集呼叫的組件後端資料庫時重新載入 [主機] 頁面。變更增益集組件屬性值在編輯模式會導致重新載入頁面，但是您可能不想要在此情況下觸發的網路呼叫。您可以使用 **_editMode_** token 偵測使用者如果正在編輯您的增益集組件。
  
    
    
若要使用的 **_editMode_** token，將新增至 **Src**元素之屬性的 **Content**增益集組件宣告中的查詢字串參數。
  
    
    



```XML
<Content Src="content_page_url&amp;amp;editmode=_editMode_">
```

 **_editMode_**權杖可讓您決定增益集組件是否處於編輯模式的內容] 頁面。如果增益集組件是在編輯模式中 **_editMode_**權杖會解析為 1。否則請權杖會解析為 0。
  
    
    

## 其他資源
<a name="SP15Createappparts_AddResources"> </a>


-  [程式碼範例： 使用增益集組件的主機網站中顯示遠端增益集內容](http://code.msdn.microsoft.com/SharePoint-2013-Display-03c28286)
    
  
-  [程式碼範例： 使用增益集組件的主機網站中顯示的增益集 web 內容](http://code.msdn.microsoft.com/SharePoint-2013-Display-be8dac16)
    
  
-  [程式碼範例： 調整增益集組件動態中 SharePoint 增益集](http://code.msdn.microsoft.com/officeapps/SharePoint-2013-Resize-app-594acc88)
    
  
-  [程式碼範例： 使用 coffeemaker 增益集組件顯示遠端網頁內容](http://code.msdn.microsoft.com/SharePoint-2013-App-part-9d83703c)
    
  
-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 增益集 UX 設計的指導方針](sharepoint-add-ins-ux-design-guidelines.md)
    
  
-  [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

