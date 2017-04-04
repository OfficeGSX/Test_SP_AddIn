---
title: 自訂清單檢視中 SharePoint 增益集使用用戶端轉譯
ms.prod: SHAREPOINT
ms.assetid: 8d5cabb2-70d0-46a0-bfe0-9e21f8d67d86
---


# 自訂清單檢視中 SharePoint 增益集使用用戶端轉譯
了解如何使用用戶端轉譯技術SharePoint 2013中自訂 SharePoint 主控增益集的清單檢視。
在SharePoint 2013、用戶端轉譯會提供一種方式，為您產生自己的一組裝載於 SharePoint] 頁面上的控制項的輸出。它可讓您可以使用 HTML 和JavaScript、 已知技術可以定義 SharePoint 清單檢視呈現邏輯。使用用戶端轉譯，您可以指定JavaScript資源以及主控它們在資料存放區選項可在增益集，例如文件庫中。SharePoint 主控增益集包括只有 SharePoint 元件。SharePoint 主控增益集有其資源中的主機網站，呼叫增益集 web 隔離子網站。
  
    
    


## 此範例使用本文中的先決條件
<a name="SP15CSRlistview_Prereq"> </a>

若要遵循此範例中的步驟，您將需要下列項目：
  
    
    

-  [Visual Studio 2015 和最新的 Microsoft Office 開發人員工具](https://www.visualstudio.com/features/office-tools-vs)
    
  
- SharePoint 2013開發環境 (增益集隔離所需的內部部署案例)
    
  
如需如何設定開發環境適合您需求的指引，請參閱 [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx)。
  
    
    

### 若要協助您了解與用戶端轉譯的清單檢視自訂的核心概念

下表列出有用的文章可幫助您了解清單檢視的自訂案例相關的概念。
  
    
    

**表 1。在 [增益集的清單檢視自訂的核心概念**


|**文章標題**|**描述**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |了解新增益集模型中的全部Microsoft SharePoint 2013可讓您建立增益集，亦即使用者的小型、 易於使用的解決方案。 <br/> |
| [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md) <br/> |了解您有當您在建置SharePoint Add-insUX 選項。 <br/> |
| [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |了解主機 web 及增益集 web 之間的差異。深入了解哪些SharePoint 2013元件還包含在SharePoint Add-in、 哪些元件部署到主機網站、 哪些元件部署到增益集 web 及增益集 web 隔離網域中的部署方式。 <br/> |
   

## 程式碼範例： 使用用戶端轉譯自訂清單檢視
<a name="SP15CSRlistview_Codeexample"> </a>

若要自訂清單檢視中使用用戶端轉譯部署至增益集的網站，遵循下列步驟：
  
    
    

1. 建立SharePoint Add-in專案。
    
  
2. 使用自訂檢視建立新的清單定義。
    
  
3. 提供自訂的轉譯邏輯JavaScript檔案中。
    
  
圖 1 顯示的宣告清單的用戶端轉譯的檢視。
  
    
    

**圖 1。宣告清單的自訂檢視**

  
    
    

  
    
    
![Custom view of an announcements list](images/CSRListView_result.png)
  
    
    

### 若要建立SharePoint Add-in專案


1. 系統管理員身分開啟 Visual Studio 2015。(若要這樣做，以滑鼠右鍵按一下 [ **開始**] 功能表上的 **Visual Studio**圖示並選擇 [ **以系統管理員身分執行**])。
    
  
2. 建立新專案使用 **SharePoint 增益集**範本。
    
    圖 2 顯示在 Visual Studio 2015、 **範本**、 **Visual C#**、 **Office/SharePoint**、 **Office 增益集**下 **SharePoint 增益集**範本的位置。
    

   **圖 2。SharePoint 2013 Visual Studio 範本的增益集**

  

     ![App for SharePoint 2013 Visual Studio template](images/AppForSharePointVSTemplate.PNG)
  

  

  
3. 提供您想要用於偵錯之 SharePoint 網站的 URL。
    
  
4. 選取 **SharePoint 主控**做為增益集的主控選項。
    
  

### 若要建立新的清單定義


1. 以滑鼠右鍵按一下SharePoint Add-in專案，並新增新的 **清單**項目。建立可自訂宣告為基礎的清單。
    
  
2. 複製下列標記並將其貼 **Views**元素清單功能的 Schema.xml 檔中。標記會執行下列工作：
    
  - 會宣告名為可覆寫與 BaseViewID 新檢視 = 2。
    
  
  - 提供指引與增益集已佈建JavaScript檔案 **JSLink**元素的值。
    
    > **注意事項**
      > 屬性不支援調查或事件 JSLink 列出。將 SharePoint 行事曆是事件] 清單中。

  ```XML
  
<View BaseViewID="2"
      Name="8d2719f3-c3c3-415b-989d-33840d8e2ddb" 
      DisplayName="Overridable" 
      Type="HTML" 
      WebPartZoneID="Main" 
      SetupPath="pages\\viewpage.aspx" 
      Url="Overridable.aspx"
      DefaultView="TRUE">
  <ViewFields>
    <FieldRef Name="Title" />
  </ViewFields>
  <Query />
  <Toolbar Type="Standard" />
  <XslLink>main.xsl</XslLink>
  <JSLink Default="TRUE">~site/Scripts/CSRListView.js</JSLink>
</View>
  ```


### 若要提供JavaScript檔案中的自訂呈現邏輯


1. 以滑鼠右鍵按一下 [ **指令碼**] 資料夾中，並新增新的JavaScript檔案。CSRListView.js檔案名稱。
    
  
2. 下列程式碼複製並貼入 CSRListView.js 檔案中。程式碼會執行下列工作：
    
  - 提供 **PreRender**和 **PostRender**事件的事件處理常式。
    
  
  - 提供的頁首、 頁尾及項目範本組範本。
    
  
  - 登錄範本。
    
  

  ```
  
(function () {
    // Initialize the variable that stores the objects.
    var overrideCtx = {};
    overrideCtx.Templates = {};

    // Assign functions or plain html strings to the templateset objects:
    // header, footer and item.
    overrideCtx.Templates.Header = "<B><#=ctx.ListTitle#></B>" +
        "<hr><ul id='unorderedlist'>";

    // This template is assigned to the CustomItem function.
    overrideCtx.Templates.Item = customItem;
    overrideCtx.Templates.Footer = "</ul>";

    // Set the template to the:
    //  Custom list definition ID
    //  Base view ID
    overrideCtx.BaseViewID = 2;
    overrideCtx.ListTemplateType = 10057;

    // Assign a function to handle the
    // PreRender and PostRender events
    overrideCtx.OnPreRender = preRenderHandler;
    overrideCtx.OnPostRender = postRenderHandler;

    // Register the template overrides.
    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(overrideCtx);
})();

// This function builds the output for the item template.
// It uses the context object to access announcement data.
function customItem(ctx) {

    // Build a listitem entry for every announcement in the list.
    var ret = "<li>" + ctx.CurrentItem.Title + "</li>";
    return ret;
}

// The preRenderHandler attends the OnPreRender event
function preRenderHandler(ctx) {

    // Override the default title with user input.
    ctx.ListTitle = prompt("Type a title", ctx.ListTitle);
}

// The postRenderHandler attends the OnPostRender event
function postRenderHandler(ctx) {

    // You can manipulate the DOM in the postRender event
    var ulObj;
    var i, j;

    ulObj = document.getElementById("unorderedlist");
    
    // Reverse order the list.
    for (i = 1; i < ulObj.children.length; i++) {
        var x = ulObj.children[i];
        for (j = 1; j < ulObj.children.length; j++) {
            var y = ulObj.children[j];
            if(x.innerText<y.innerText){                  
                ulObj.insertBefore(y, x);
            }
        }
    }
}
  ```


### 若要建置和執行解決方案


1. 按 F5 鍵。
    
    > **注意事項**
      > 當您按 F5 時、 Visual Studio建置解決方案、 增益集、 部署和增益集的權限] 頁面會隨即開啟。
2. 選擇 [ **信任它**] 按鈕。
    
  
3. 在增益集 web 網域 (不主機 web 網域) 中輸入 _/Lists/<your_list_instance>_位址相對於您增益集的目錄移至您的自訂清單。新增一個或兩個宣告。在功能區上選擇 [ **可覆寫**] 檢視。
    
  

## 後續步驟
<a name="SP15CSRlistview_Nextsteps"> </a>

本文示範如何使用用戶端轉譯自訂SharePoint Add-in在清單檢視中。為下一個步驟中，您可了解其他 UX 元件可供SharePoint Add-ins。若要深入了解，請參閱下列各項：
  
    
    

-  [程式碼範例： 自訂清單檢視中的增益集使用用戶端轉譯](http://code.msdn.microsoft.com/SharePoint-2013-Customize-61761017)
    
  
-  [使用 SharePoint 網站的樣式表中 SharePoint 增益集](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)
    
  
-  [使用用戶端的 chrome 控制項中 SharePoint 增益集](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [建立部署與 SharePoint 增益集的自訂動作](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)
    
  
-  [建立增益集組件安裝與您 SharePoint 的增益集](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## 其他資源
<a name="SP15CSRlistview_AddResources"> </a>


-  [設定內部部署開發環境的 SharePoint 增益集](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [UX 設計的 SharePoint 增益集](ux-design-for-sharepoint-add-ins.md)
    
  
-  [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

