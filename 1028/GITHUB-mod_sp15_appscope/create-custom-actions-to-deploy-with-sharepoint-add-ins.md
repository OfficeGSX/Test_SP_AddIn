---
title: 建立部署與 SharePoint 增益集的自訂動作
ms.prod: SHAREPOINT
ms.assetid: bbd11f94-1798-453e-bbb0-e5eb0df8dc75
---


# 建立部署與 SharePoint 增益集的自訂動作
了解如何在SharePoint ，當您部署SharePoint Add-in部署到主機網站中建立自訂動作。
當您要建立SharePoint Add-in時、 自訂動作可讓您互動的清單和功能區中的主機網站。當使用者安裝的增益集自訂的自訂動作會部署到主機 web 動作可以開啟遠端網頁和傳遞到查詢字串的資訊。有兩種類型的自訂動作可用增益集：功能區和功能表項目的自訂動作。
  
    
    


## 此範例使用本文中的先決條件
<a name="SP15Createcustomactionsapps_Prereq"> </a>

您必須在開發環境 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)所述。
  
    
    

### 若要協助您了解自訂動作的核心概念

下表列出有用的文章可幫助您了解的概念和相關的自訂動作的案例中的步驟。
  
    
    

**表 1。自訂動作的核心概念**


|**文章**|**描述**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |了解新增益集模型中的全部SharePoint可讓您建立增益集，亦即使用者的小型、 易於使用的解決方案。 <br/> |
| [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md) <br/> |了解使用者經驗時要建置SharePoint Add-ins有的 (UX) 選項。 <br/> |
| [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |了解主機 web 及增益集 web 之間的差異。深入了解哪些SharePoint元件還包含在SharePoint Add-in、 哪些元件部署到主機網站、 哪些元件部署到增益集 web 及增益集 web 隔離網域中的部署方式。 <br/> |
   

## 程式碼範例： web 文件庫主應用程式中建立的自訂動作
<a name="SP15Createcustomactionsapps_Codeexample"> </a>

請遵循下列步驟來建立主機網頁文件庫中的自訂動作：
  
    
    

1. 建立SharePoint Add-in和遠端 web 專案。
    
  
2. 將自訂動作功能新增至SharePoint Add-in專案。
    
  
3. 為 web 專案新增增益集] 網頁。
    
  

### 若要建立SharePoint Add-in和遠端 web 專案


1. 系統管理員身分開啟Visual Studio 。(若要這樣做，以滑鼠右鍵按一下 [ **開始**] 功能表上的 [ Visual Studio ] 圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立提供者主控SharePoint Add-in [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)所述，並命名CustomActionsApp。
    
  

### 若要新增的自訂動作的增益集] 網頁


1. 已建立Visual Studio解決方案之後，以滑鼠右鍵按一下 [web 應用程式專案 (非SharePoint Add-in專案) 並選擇 [ **新增]**新增新的 Web 表單 > **新增項目**> **Web** > **Web 表單**。名稱表單CustomActionTarget.aspx。
    
  
2. CustomActionTarget.aspx 檔案並中取代整個 **html**元素和它會使用下列的 HTML 程式碼的子項。讓上方 **html**元素的所有標記都保持原狀。HTML 程式碼包含JavaScript執行下列工作：
    
  - 提供查詢字串參數版面配置區。
    
  
  - 從查詢字串中抽選參數。
    
  
  - 轉譯版面配置區中的參數。
    
  

    > **重要**
      > ItemURL 和項目識別碼權杖只取得傳遞時沒有選取項目。 在實際執行品質SharePoint Add-in、 程式碼必須處理的情況其中會選取任何項目。本範例會在程式碼會提醒使用者已選取任何項目。

  ```HTML
  
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>Custom action target</title>
</head>
<body>
    <h2>Query string parameters passed by the custom action:</h2>

    <!-- Placeholder for query string parameters -->
    <ul id="qsparams"/>

    <!-- Main JavaScript function, renders
         the query string parameters -->
    <script lang="javascript">
        var params = document.URL.split("?")[1].split("&amp;");
        var paramsHTML = "";
      
        // Extracts the parameters from the query string.
        // Parameters are URLencoded, decode for rendering
        // in page.
        for (var i = 0; i < params.length; i = i + 1) {
            params[i] = decodeURIComponent(params[i]);
            paramsHTML += "<li>" + params[i] + "</li>";
        }

         // Alert the user when no item has been selected.
         // (The SPListItemId is the 5th parameter.)
         if (params[5] === undefined) {
            paramsHTML += "<div> <h3> No item has been selected from the list.  Please select an item. </h3> </div> ";
         }

        // Render parameters in the placeholder.
        document.getElementById("qsparams").innerHTML =
            paramsHTML;
    </script>
</body>
</html>
  ```


### 將自訂功能表項目] 動作新增至SharePoint Add-in專案


1. 以滑鼠右鍵按一下SharePoint Add-in專案，並選擇 [ **新增]** > **新增項目**> **Office/SharePoint** > **功能表項目的自訂動作**。
    
  
2. 保留預設名稱，選擇 [ **新增]**。
    
  
3. **建立自訂動作] 功能表項目**精靈會要求您一系列的問題。授與下列表格中的答案：
    
   **表 2。功能表項目的自訂動作屬性**


|**屬性的問題**|**接聽**|
|:-----|:-----|
|您要在其中公開的自訂動作？ <br/> |選擇 **主機網頁**。 <br/> |
|其中的自訂動作的範圍侷限於？ <br/> |選擇 **清單範本**。 <br/> |
|哪個特定項目的自訂動作的範圍侷限於？ <br/> |選擇 [ **文件庫**。 <br/> |
|什麼是文字上的功能表項目？ <br/> |**「 我的自訂動作**的類型。 <br/> |
|自訂動作其中巡覽至？ <br/> |選擇 [ **CustomActionAppWeb\\CustomActionTarget.aspx** ] 頁面。 <br/> |
   
4. 選擇 [ **完成**]。
    
    Visual Studio在 elements.xml 檔案中的功能表項目的自訂動作的功能將會產生下列標記：
    


  ```XML
  
<?xml version="1.0" encoding="utf-8"?>
<Elements 
    xmlns="http://schemas.microsoft.com/sharepoint/">
    <!-- RegistrationId attribute is the list type id,
        in this case, a document library (id=101). -->
  <CustomAction 
      Id="65695319-4784-478e-8dcd-4e541cb1d682.CustomAction"
      RegistrationType="List"
      RegistrationId="101"
      Location="EditControlBlock"
      Sequence="10001"
      Title="Invoke custom action">
    <!-- 
    Update the Url below to the page you want the custom action to use.
    Start the URL with the token ~remoteAppUrl if the page is in the
    associated web project, use ~appWebUrl if page is in the add-in project.
    -->
    <UrlAction Url=
"~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={ItemId}&amp;amp;SPListId={ListId}" />
  </CustomAction>
</Elements>

  ```

5. 將下列查詢參數新增至結尾的 **UrlAction**元素的 **Url**屬性：
    
     `&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}&amp;amp;SPItemURL={ItemUrl}`
    
    **UrlAction**元素看起來應該類似如下：
    
     ` <UrlAction Url= "~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={ItemId}&amp;amp;SPListId={ListId}&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}&amp;amp;SPItemURL={ItemUrl}" />`
    
  

> **注意事項**
> 在這個範例中，遠端網頁在完整視窗開啟當使用者從功能表選取自訂動作。自訂功能表動作可以也開啟 [遠端網頁] 對話方塊中可以使用 **HostWebDialog**屬性。如需詳細資訊，請參閱 [SharePoint-增益集在-當地語系化](https://github.com/OfficeDev/SharePoint-Add-in-Localization)。
  
    
    


### 將功能區自訂動作新增至SharePoint Add-in專案


1. 以滑鼠右鍵按一下SharePoint Add-in專案，並選擇 [ **新增]** > **新增項目**> **Office/SharePoint** > **功能區自訂動作**。
    
  
2. 保留預設名稱，選擇 [ **新增]**。
    
  
3. **建立自訂動作的功能區**精靈] 會要求您一系列的問題。授與下列表格中的答案：
    
   **表 3。功能區自訂動作屬性**


|**屬性的問題**|**接聽**|
|:-----|:-----|
|您要在其中公開的自訂動作？ <br/> |選擇 **主機網頁**。 <br/> |
|其中的自訂動作的範圍侷限於？ <br/> |選擇 **清單範本**。 <br/> |
|哪個特定項目的自訂動作的範圍侷限於？ <br/> |選擇 [ **文件庫**。 <br/> |
|控制項位於何處？ <br/> |選擇 [ **Ribbon.Documents.Manage**。 <br/> |
|什麼是文字上的功能表項目？ <br/> |**「 我的自訂功能區按鈕**的類型。 <br/> |
|自訂動作其中巡覽至？ <br/> |選擇 [ **CustomActionAppWeb\\CustomActionTarget.aspx** ] 頁面。 <br/> |
   
4. Visual Studio在 elements.xml 檔案中的功能區自訂動作功能將會產生下列標記：
    
  ```XML
  
<?xml version="1.0" encoding="utf-8"?>
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <CustomAction Id="85691508-c076-4f43-93d4-96b4d5253a09.RibbonCustomAction1"
                RegistrationType="List"
                RegistrationId="101"
                Location="CommandUI.Ribbon"
                Sequence="10001"
                Title="Invoke &amp;apos;RibbonCustomAction1&amp;apos; action">
    <CommandUIExtension>
      <!-- 
      Update the UI definitions below with the controls and the command actions
      that you want to enable for the custom action.
      -->
      <CommandUIDefinitions>
        <CommandUIDefinition Location="Ribbon.Documents.Manage.Controls._children">
          <Button Id="Ribbon.Documents.Manage.RibbonCustomAction1Button"
                  Alt="My Custom Ribbon Button"
                  Sequence="100"
                  Command="Invoke_RibbonCustomAction1ButtonRequest"
                  LabelText="My Custom Ribbon Button"
                  TemplateAlias="o1"
                  Image32by32="_layouts/15/images/placeholder32x32.png"
                  Image16by16="_layouts/15/images/placeholder16x16.png" />
        </CommandUIDefinition>
      </CommandUIDefinitions>
      <CommandUIHandlers>
        <CommandUIHandler Command="Invoke_RibbonCustomAction1ButtonRequest"
                          CommandAction="~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={SelectedItemId}&amp;amp;SPListId={SelectedListId}"/>
      </CommandUIHandlers>
    </CommandUIExtension >
  </CustomAction>
</Elements> 

  ```

5. 將下列查詢參數新增至結尾的 **CommandUIHandler**元素的 **CommandAction**屬性：
    
     `&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}`
    
    **CommandUIHandler**元素看起來應該類似如下：
    
     ` <CommandUIHandler Command="Invoke_RibbonCustomAction1ButtonRequest" CommandAction="~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={SelectedItemId}&amp;amp;SPListId={SelectedListId}&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}" />`
    
    > **注意事項**
      > 功能區自訂動作使用 **SelectedListId**和 **SelectedItemId**。 **ListId**和 **ItemId**適用於只能與功能表項目的自訂動作。

### 設為主機網站首頁上的增益集開始頁面


1. 繼續範例SharePoint Add-in都不會有任何增益集的 web 和其遠端 web 應用程式存在只是為了主控表單。讓增益集的 [開始] 頁面上應設為 [首頁] 頁面上的主機網站。
    
    若要開始，在 **方案總管**中選取SharePoint Add-in專案 (非 web 應用程式專案) 並複製 **網站 URL**屬性，包括通訊協定 (例如 **https://contoso.sharepoint.com**) 至剪貼簿的值。
    
  
2. 開啟的增益集資訊清單，並再貼到 [ **開始] 頁面**] 方塊中的 [URL。
    
  
3. (選用) 您可以從 web 應用程式專案中，刪除 Default.aspx 頁面由於不適用於SharePoint Add-in。
    
  

### 若要建置和執行解決方案


1. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio建置解決方案、 增益集、 部署和增益集的權限] 頁面會隨即開啟。
2. 選擇 [ **信任它**] 按鈕。開發人員網站的預設頁面隨即開啟。
    
  
3. 瀏覽至任何文件庫中的主機網站。
    
   **啟動的自訂功能表動作**

  

     ![開啟文件圖說文字的文件庫、圖說文字開啟上的圖說文字按鈕功能表、及 [進階] 功能表開啟。](images/477cecf5-03ff-46ff-9c25-a5f9a86d43f4.png)
  

  

  
4. 選擇任何文件的註標] 按鈕 (...)。圖說文字線會隨即開啟。
    
  
5. 選擇在圖說文字線的圖說文字] 按鈕 (...)。
    
  
6. 選擇 [ **進階**]。
    
  
7. 選擇快顯功能表中的 **「 我的自訂功能表動作**。您應該會開啟遠端網頁會看到類似下列的訊息：
    
   **由自訂動作的參數與遠端網頁**

  

     ![Web page with parameters from a custom action](images/CustomActions_target.png)
  

  

  
8. 按一下您要傳回至文件庫的瀏覽器上的 [ **上一頁**] 按鈕。
    
   **啟動的自訂功能區動作**

  

     ![選取文件的文件庫、在功能區上開啟的 [檔案] 索引標籤，以及功能區上的自訂按鈕。](images/b315cb68-ff6a-4770-a1dc-738696ab71d2.png)
  

  

  
9. 選取任何文件。
    
  
10. 開啟 [功能區上的 [ **檔案**] 索引標籤。
    
  
11. 選擇 [ **我的自訂功能區] 按鈕**。您看到相同的遠端 web 頁面。
    
  

**表 4。解決方案的疑難排解**


|**問題**|**解決方法**|
|:-----|:-----|
|Visual Studio無法開啟瀏覽器之後按 F5 鍵。 <br/> |將SharePoint Add-in專案設為啟動專案。 <br/> |
|之後您按 F5 鍵Visual Studio中未解析 URL 中的 token。 <br/> |移至主機 web 中的 [ **網站內容**] 頁面上，按一下 [增益集的圖示。 <br/> |
   

## 後續步驟
<a name="SP15Createcustomactionsapps_Nextsteps"> </a>

本文示範如何在SharePoint Add-in中建立自訂動作。為下一個步驟中，您可了解可用於SharePoint Add-ins其他 UX 元件。若要深入了解，請參閱下列各項：
  
    
    

-  [程式碼範例： 開啟 [遠端增益集網頁使用 ECB 自訂動作](http://code.msdn.microsoft.com/SharePoint-2013-Open-e0ca1826)
    
  
-  [SharePoint-Add-in-Localization](https://github.com/OfficeDev/SharePoint-Add-in-Localization)
    
  
-  [程式碼範例： 使用自訂動作和順序書籍的跨網域文件庫](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)
    
  
-  [使用 SharePoint 網站的樣式表中 SharePoint 增益集](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)
    
  
-  [使用用戶端的 chrome 控制項中 SharePoint 增益集](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [建立增益集組件安裝與您 SharePoint 的增益集](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## 其他資源
<a name="SP15Createcustomactionsapps_AddResources"> </a>


-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 增益集 UX 設計的指導方針](sharepoint-add-ins-ux-design-guidelines.md)
    
  
-  [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

