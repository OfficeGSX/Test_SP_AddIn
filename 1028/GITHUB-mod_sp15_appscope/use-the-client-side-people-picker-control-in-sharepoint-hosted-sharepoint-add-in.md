---
title: 使用用戶端的人員選擇 」 控制項中 SharePoint 主控 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# 使用用戶端的人員選擇 」 控制項中 SharePoint 主控 SharePoint 增益集
了解如何使用用戶端的人員選擇 」 控制項中SharePoint-主控SharePoint Add-ins。
> [!重要]
> 本主題假設您了解如何建立 SharePoint 主控的SharePoint Add-in。若要了解如何建立 web 應用程式，請參閱 [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)啟動。
  
    
    


## 什麼是SharePoint 2013中的用戶端的人員選擇 」 控制項？
<a name="bk_whatIs"> </a>

用戶端的人員選擇 」 控制項可讓使用者快速搜尋並選取 [有效的使用者帳戶的人員、 群組和其組織中的宣告。選擇是提供跨瀏覽器支援 HTML 和JavaScript控制項。選擇器新增到增益集很簡單： 您標記中新增控制項的容器元素和參照控制項和其相依性。然後您的指令碼中呼叫 **SPClientPeoplePicker_InitStandaloneControlWrapper**全域函數轉譯及初始化選擇]。
  
    
    
選擇被表示 **SPClientPeoplePicker**物件，提供其他用戶端控制項可以使用從選擇器中取得資訊或執行其他作業的方法。您可以使用 **SPClientPeoplePicker**屬性來設定選擇器與特定控制項設定，例如允許多個使用者或指定快取的選項。選擇也會使用如Active Directory Domain Services參數或目標樹系的 web 應用程式組態設定。Web 應用程式組態設定會挑選自動 (從 **SPWebApplication.PeoplePickerSettings**屬性)。
  
    
    
選擇器有下列元件：
  
    
    

- 若要輸入的使用者、 群組及宣告名稱輸入的文字方塊。
    
  
- Span 控制項的顯示名稱解析的使用者、 群組和宣告。
    
  
- Autofills 下拉式方塊與比對查詢結果隱藏的 **div**項目。
    
  
- 自動填滿控制項。
    
  

> [!注意事項]
> 選擇器和其功能的定義 **clientforms.js**、 **clientpeoplepicker.js**及 **autofill.js**指令碼檔案位於伺服器上的%ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\TEMPLATE\\LAYOUTS 資料夾中。
  
    
    


## SharePoint Add-in 2013年中使用的用戶端的人員選擇 」 控制項設定開發環境的必要條件
<a name="bk_prereqs"> </a>

本文假設您使用NapaOffice 365開發人員網站上建立SharePoint Add-in 。如果您使用此開發環境，您已經已符合先決條件。
  
    
    

> [!注意事項]
> 移至 [在 Office 365 上設定 SharePoint 增益集的開發環境](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md)以了解如何將開發人員網站註冊並開始使用Napa。
  
    
    

如果您不使用Napa開發人員網站上，您將需要下列項目：
  
    
    

- SharePoint 2013與至少一個目標使用者
    
  
- Visual Studio 2012或Visual Studio 2013
    
  
- Visual Studio 2013 Office 開發人員工具
    
  

> [!注意事項]
> 如需如何設定適合您需求的開發環境的指引，請參閱 [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx)。
  
    
    

下列步驟說明將選擇器新增至 [增益集以及然後從另一個用戶端控制項取得其使用者資訊的高階步驟。請參閱 [程式碼範例： 使用用戶端的人員選擇](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example)相對應的程式碼。
  
    
    
您可以使用用戶端的人員選擇 」 控制項中SharePoint-主控SharePoint Add-ins，但未提供者主控增益集。如需將示範如何實作人員選擇 」 控制項提供者主控增益集範例，下載 [Office 增益集模型範例](http://officeams.codeplex.com)。
  
    
    

## 使用用戶端的人員選擇 」 控制項中SharePoint-主控增益集 (英文)
<a name="bk_steps"> </a>


### 在頁面標記


1. 加入參考用戶端的人員選擇 」 控制項的指令碼相依性。
    
  
2. 新增頁面 UI 的 HTML 標籤。增益集在此範例會定義兩個 **div**元素： 一個選擇] 中呈現的另一個 ui： 叫用指令碼來查詢選擇及顯示使用者資訊的項目] 按鈕。
    
  

### 在您的指令碼


1. 建立 JSON 字典來作為儲存選擇特有的屬性，例如 **AllowMultipleValues**和 **MaximumEntitySuggestions**結構描述。
    
  
2. 呼叫 **SPClientPeoplePicker_InitStandaloneControlWrapper**全域函式。
    
  
3. 從網頁取得選擇物件。
    
  
4. 查詢選擇。增益集在此範例會呼叫 **GetAllUserInfo**方法來取得所有的使用者資訊進行解析並剛剛 **GetAllUserKeys**方法取得機碼的解析的使用者。
    
  
5. 透過使用JavaScript物件模型中取得的使用者識別碼。使用者識別碼不包含在傳回選擇器]，讓增益集呼叫 **SP.Web.ensureUser**方法，並取得從傳回的 **SP.User**物件的識別碼資訊。
    
  
呈現、 初始化和選擇的其他功能都是由伺服器，包括搜尋及解決針對SharePoint驗證提供者的使用者輸入處理。
  
    
    

## 程式碼範例： 使用用戶端的人員選擇中SharePoint-主控增益集
<a name="bk_example"> </a>

下列的 HTML 和JavaScript程式碼範例用戶端的人員選擇] 控制項新增至SharePoint-主控增益集。
  
    
    
第一個範例顯示 **PlaceHolderMain**頁面標記 **asp:Content**標記 Default.aspx 頁面中。這段程式碼會參照選擇指令碼相依性，讓其中選擇將轉譯，而且會定義增益集 UI 的 DOM 元素的唯一識別碼。
  
    
    



```HTML

<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
    <SharePoint:ScriptLink name="clienttemplates.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientforms.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientpeoplepicker.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="autofill.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.runtime.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.core.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <div id="peoplePickerDiv"></div>
    <div>
        <br/>
        <input type="button" value="Get User Info" onclick="getUserInfo()"></input>
        <br/>
        <h1>User info:</h1>
        <p id="resolvedUsers"></p>
        <h1>User keys:</h1>
        <p id="userKeys"></p> 
        <h1>User ID:</h1>
        <p id="userId"></p>
    </div>
</asp:Content>
```


> [!注意事項]
> 根據您的環境，您可能沒有明確地參照所有這些相依性。
  
    
    

下列範例會顯示從 App.js 檔案指令碼。此指令碼初始化和轉譯選擇、 查詢的使用者資訊、，然後使用JavaScript物件模型來取得使用者識別碼。
  
    
    



```

// Run your custom code when the DOM is ready.
$(document).ready(function () {

    // Specify the unique ID of the DOM element where the
    // picker will render.
    initializePeoplePicker('peoplePickerDiv');
});

// Render and initialize the client-side People Picker.
function initializePeoplePicker(peoplePickerElementId) {

    // Create a schema to store picker properties, and set the properties.
    var schema = {};
    schema['PrincipalAccountType'] = 'User,DL,SecGroup,SPGroup';
    schema['SearchPrincipalSource'] = 15;
    schema['ResolvePrincipalSource'] = 15;
    schema['AllowMultipleValues'] = true;
    schema['MaximumEntitySuggestions'] = 50;
    schema['Width'] = '280px';

    // Render and initialize the picker. 
    // Pass the ID of the DOM element that contains the picker, an array of initial
    // PickerEntity objects to set the picker value, and a schema that defines
    // picker properties.
    this.SPClientPeoplePicker_InitStandaloneControlWrapper(peoplePickerElementId, null, schema);
}

// Query the picker for user information.
function getUserInfo() {

    // Get the people picker object from the page.
    var peoplePicker = this.SPClientPeoplePicker.SPClientPeoplePickerDict.peoplePickerDiv_TopSpan;

    // Get information about all users.
    var users = peoplePicker.GetAllUserInfo();
    var userInfo = '';
    for (var i = 0; i < users.length; i++) {
        var user = users[i];
        for (var userProperty in user) { 
            userInfo += userProperty + ':  ' + user[userProperty] + '<br>';
        }
    }
    $('#resolvedUsers').html(userInfo);

    // Get user keys.
    var keys = peoplePicker.GetAllUserKeys();
    $('#userKeys').html(keys);

    // Get the first user's ID by using the login name.
    getUserId(users[0].Key);
}

// Get the user ID.
function getUserId(loginName) {
    var context = new SP.ClientContext.get_current();
    this.user = context.get_web().ensureUser(loginName);
    context.load(this.user);
    context.executeQueryAsync(
         Function.createDelegate(null, ensureUserSuccess), 
         Function.createDelegate(null, onFail)
    );
}

function ensureUserSuccess() {
    $('#userId').html(this.user.get_id());
}

function onFail(sender, args) {
    alert('Query failed. Error: ' + args.get_message());
}
```


## 其他資源
<a name="bk_addresources"> </a>


-  [在 SharePoint 2013 中建立 UX 元件](create-ux-components-in-sharepoint-2013.md)
    
  
-  [人員選擇與宣告提供者概觀 (SharePoint 2013)](http://technet.microsoft.com/library/gg602078.aspx)
    
  
-  [configure People Picker in SharePoint 2013](http://technet.microsoft.com/library/gg602075.aspx)
    
  

