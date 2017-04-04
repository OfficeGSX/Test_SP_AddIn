---
title: 使用 Napa Office 365 開發工具建立基本 SharePoint 主控增益集 (英文)
ms.prod: SHAREPOINT
ms.assetid: 3b47c97b-9e09-47b2-a65f-29b0f44e34bf
---


# 使用 Napa Office 365 開發工具建立基本 SharePoint 主控增益集 (英文)
了解如何使用Napa Office 365 開發工具建立基本 SharePoint 主控SharePoint Add-in 。
  
    
    
![Run button](images/Apps_NAPA_Run_Button.png)
  
    
    
 [現在執行這個範例!](http://go.microsoft.com/fwlink/?LinkId=313212)
Napa是可用來建立 SharePoint 主控SharePoint Add-ins的工具。Napa 是實作為 (提供者主控) SharePoint Add-in可以安裝在 **開發人員網站** 範本建立的SharePoint Online網站本身。SharePoint 開發人員網站具有 **增益集在測試中**稱為 [首頁] 頁面上的文件庫。在本文後面的指示說明建立開發人員網站及安裝 Napa。
  
    
    


> **注意事項**
> 我們不支援在內部部署 sharepoint 安裝 Napa。
  
    
    


藉由使用Napa，您可以建立您SharePoint Add-insVisual Studio中而不是瀏覽器內。在任何時候，您可以下載您的專案並開啟Visual Studio更進階案例中。
  
    
    

遵循本文章，您可以學習如何建立簡單 SharePoint 主控SharePoint Add-in使用Napa。增益集將會建立包含控制項和程式碼來管理清單、 清單項目。
> **注意事項**
> 您可以建立僅 SharePoint 主控SharePoint Add-ins與 Napa、 未裝載提供者。如需差異的資訊，請參閱 [SharePoint Add-ins](sharepoint-add-ins.md)。> 您無法使用 SharePoint 的增益集更新語意，其中 [更新 SharePoint 2013 中的增益集 web 元件](update-add-in-web-components-in-sharepoint-2013.md)、 Napa 中所述。因此如果您需要更新 Napa 中建立增益集，您先將其匯出至 Visual Studio。指示這麼做讓是在本文後面。> 您也可以使用Visual Studio建立SharePoint Add-in 。如需詳細資訊，請參閱 [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)。
  
    
    


## (選用) 取得Office 365 開發人員網站
<a name="Prerequisites"> </a>

如果您已經沒有可用的開發SharePoint Online訂閱，請使用此區段來取得其中一個。否則，請跳至 [Install NAPA](#Overview)。
  
    
    

> **注意事項**
> 您可能已有 Office 365 開發人員網站 的存取權：> **您是 MSDN 訂閱者嗎？** Visual Studio Ultimate 和 Visual Studio Premium 的 MSDN 訂閱者可以獲得 Office 365 Developer 訂閱的權益。 [立即兌換權益。](https://msdn.microsoft.com/subscriptions/manage/default.aspx)> **您有下列其中一個 Office 365 訂閱計劃嗎？**> **如果有，Office 365 訂閱的管理員可以使用  [Office 365 管理中心](https://portal.microsoftonline.com/admin/default.aspx) 建立 開發人員網站** 。如需詳細資訊，請參閱 [建立現有的 Office 365 訂閱開發人員網站](create-a-developer-site-on-an-existing-office-365-subscription.md)。
  
    
    

有兩種方法可以取得 Office 365 計劃。
  
    
    

- 利用一個使用者授權，開始使用 [免費 30 天試用](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK)。
    
  
- 購買  [Office 365 開發人員訂閱](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK)。
    
  

> **秘訣**
> 會在另一個視窗或索引標籤中開啟所有這些連結，以方便使用下列指示。
  
    
    


**圖 1。Office 365 開發人員網站網域名稱**

  
    
    

  
    
    
![Office 365 帳戶註冊表單的第 2 頁](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. 註冊表單的第一頁 (未顯示) 一目了然。只要提供所要求的您的個人資訊，然後選擇 [下一步]。
    
  
2. 在第二頁上 (圖 1 中所示)，指定訂閱管理員的使用者識別碼。
    
  
3. 建立 **.onmicrosoft.com** 的子網域。
    
    註冊之後，您必須使用所產生的認證 (格式為  _UserID_@ _yourdomain_.onmicrosoft.com) 登入您在其中管理帳戶的 Office 365 入口網站。SharePoint Online 開發人員網站會佈建於您的新網域中： **http:// _yourdomain_.sharepoint.com** 。
    
  
4. 選擇 [下一步]，並填寫表單的最後一頁。如果您選擇提供電話號碼來取得確認碼，則可以提供行動電話或地面通訊電話號碼，但「不」是 VoIP 號碼。
    
  

    
> **注意事項**
> 如果您嘗試註冊開發人員帳戶時已登入另一個 Microsoft 帳戶，可能會收到此訊息：「抱歉，您輸入的使用者識別碼無法運作。它看起來無效。請務必輸入組織指派給您的使用者識別碼。您的使用者識別碼通常看起來像是  *someone@example.com*  或 *someone@example.onmicrosoft.com*  。」> 如果您看到此訊息，請登出原本使用的 Microsoft 帳戶然後再試一次。如果仍然收到此訊息，請清除瀏覽器快取，或切換到 [InPrivate 瀏覽]，然後填寫表單。
  
    
    

完成註冊程序之後，您的瀏覽器會開啟 Office 365 安裝頁面。請選擇 [管理] 圖示，以開啟管理中心頁面。
  
    
    

**圖 2。Office 365 管理中心頁面**

  
    
    

  
    
    
![顯示 Office 365 系統管理中心的螢幕擷取畫面。](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. 您必須等待 開發人員網站 完成準備。準備完成之後，請在瀏覽器中重新整理 管理中心頁面。
    
  
2. 然後選擇頁面左上角的 [建置增益集] 連結來開啟 開發人員網站。您應該會看到與圖 3 中網站類似的網站。頁面上會有 [測試階段的增益集] 清單。這確認已使用 SharePoint 的開發人員網站範本建立網站。如果您是看到一般的小組網站，請稍候幾分鐘，再重新啟動網站。
    
  
3. 記下網站 URL。在 Visual Studio 中建立 SharePoint Add-ins 專案時會使用它。
    
  

**圖 3. 具有 [測試階段的增益集] 清單的開發人員網站首頁**

  
    
    

  
    
    
![顯示開發人員網站首頁的螢幕擷取畫面。](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## Install NAPA
<a name="Overview"> </a>

如果不為Office 365 開發人員網站最初建立訂閱，您必須建立訂閱管理 UI 中的開發人員網站並再安裝中的 [Napa。 [建立現有的 Office 365 訂閱開發人員網站](create-a-developer-site-on-an-existing-office-365-subscription.md)中的指示說明建立網站。
  
    
    
若要安裝 Napa，開啟開發人員網站，並選擇 [ **網站內容**> **增益集新增**> **SharePoint 存放區**。在 「 儲存 」、 搜尋Napa並安裝它。(如果您有Office 365 開發人員網站、 Napa 可能已安裝時建立網站並將會看到的 [ **網站內容**] 頁面上。)
  
    
    

## 建立SharePoint Add-in專案
<a name="Create"> </a>


1. 開啟Napa增益集在Office 365 ] 頁面上。
    
  
2. 選擇 [ **新增專案**] 磚，然後選擇 [ **增益集 sharepoint**並排顯示。
    
  
3. 測試 SharePoint 增益集，為專案的名稱，然後選擇 [ **建立**] 按鈕。
    
    程式碼編輯器會開啟並顯示預設網頁，其中已包含您可以執行而不需要任何其他動作的一些範例程式碼。
    
  

## 將控制項加入至首頁
<a name="AddControls1"> </a>

在SharePoint Add-in中，將控制項加入至預設的首頁建立及刪除一般 SharePoint 清單與SharePoint Add-inweb 中取得目前的清單數目。您將新增控制項之後的程式碼。
  
    
    

### 若要將控制項加入至首頁


1. 在頁面的 [ **頁面**] 資料夾下的左側，選擇 [ **Default.aspx**頁面如果它不已選取。
    
    Default.aspx] 網頁隨即顯示在程式碼編輯器。
    
  
2. [  `PlaceHolderMain` ] 區段中新增的現有 HTML 下這段程式碼
    
  ```HTML
  
<br />
<div>
    <button id="getListCount">Get count of lists in web</button>
</div>
<br />
<div id="starter">
    <input type="text" value="List name here" id="createlistbox"/><button id="createlistbutton">Create List</button>
    <p>
    Lists
    <br />
    <select id="selectlistbox" ></select><button id="deletelistbutton">Delete Selected List</button>
    </p>
</div>
  ```


    HTML 會建立這些控制項。
    
  - 取得在SharePoint Add-inweb 清單數目] 按鈕。
    
  
  - 建立泛用的 SharePoint 清單及刪除清單中的另一個] 按鈕的按鈕。
    
  
  - 可用增益集內的清單的清單。
    
  

## 新增程式碼來建立及刪除清單
<a name="AddCode1"> </a>

在此程序，您將新增一些 JavaScript 程式碼，讓使用者可以建立及刪除清單中SharePoint Add-in。
  
    
    

### 新增程式碼來建立及刪除清單


1. 選擇 [ **指令碼**] 資料夾，然後選擇 [ **App.js** ] 連結。
    
    預設 JavaScript 程式碼檔案中的專案範本隨即開啟供編輯。這個檔案包含您SharePoint Add-in中所使用的程式碼。您無法新增其他.js 檔案並將程式碼它而不是新增至現有的檔案。但是，此範例中，將其新增至 **App.js**檔案所提供的。
    
    在下一個步驟中，您將會定義您在前一程序中建立控制項的功能。
    

|**函數名稱**|**描述**|
|:-----|:-----|
| `getWebProperties()` <br/> |連線至 **getListCount**控制項  會擷取在 web 清單數目。 <br/> |
| `createlist()` <br/> |連線至 **createListButton**控制項  建立泛用的 SharePoint 清單。 <br/> |
| `deletelist()` <br/> |連線至 **deletelistbutton**控制項  刪除使用者選擇清單中的可用清單的清單。 <br/> |
   

    您也將會呼叫 `welcome()`和 `displayLists()`函數會稍後說明這個逐步解說。
    
  
2. 在 **App.js**檔案中，將 `web`、  `lists`及 `listItemcollection`變數新增兩個預設變數，並變更 `$(document).ready()`函數中的程式碼為下面範例。
    
    > **注意事項**
      > 錯誤不規則曲線會出現在這段程式碼。他們將在稍後步驟消失。

  ```
  
'use strict';

var context = SP.ClientContext.get_current();
var user = context.get_web().get_currentUser();
var web = context.get_web();
var lists = web.get_lists();
var listItemCollection;  // This variable is used later when you add list items.

(function () {

// This code runs when the DOM is ready and creates a context object which is 
// needed to use the SharePoint object model.
$(document).ready(function () {
    getUserName();
    $("#getListCount").click(function (event) {
        getWebProperties();
        event.preventDefault();
    });

    $("#createlistbutton").click(function (event) {
        createlist();
        event.preventDefault();
    });

    $("#deletelistbutton").click(function (event) {
        deletelist();
        event.preventDefault();
    });
        displayLists();
    });

  ```


    在下一個步驟中，您將新增 JavaScript 程式定義的函數。程式碼中的每個函數以呼叫 `executeQueryAsync()`、 其使用的用戶端物件模型 (CSOM) for SharePoint 的目前擱置的要求以非同步方式執行伺服器上執行。當函數以非同步方式執行時，指令碼會繼續執行而不需要等候伺服器回應。每次 `executeQueryAsync()`呼叫包含兩個事件處理常式。一個處理常式會回應如果函數執行成功，並在處理常式會回應如果此函數就會失敗。此表說明的主要功能。
    

|**函數名稱**|**描述**|
|:-----|:-----|
| `welcome()` <br/> |取得目前的 web 內容參照，並再使用它來將目前的使用者資訊放入內容。 <br/> |
| `getWebProperties()` <br/> |取得目前的網站中的清單及則會傳回清單數目。 <br/> |
| `displaylists()` <br/> |在此網站取得清單的目前的集合。如果成功，此函數會新增可用清單的清單集合中的每個清單的名稱。 <br/> |
| `createlist()` <br/> |建立泛用的 SharePoint 清單 (清單範本類型 **genericList**) 並為其使用者指定 **createlistbox**控制項中的名稱。您可以建立其他類型的清單。如需清單類型的詳細資訊，請參閱 [SPListTemplateType 列舉](http://go.microsoft.com/fwlink/?LinkId=256687)。 <br/> |
| `deletelist()` <br/> |刪除使用者選擇清單中的可用清單的清單。 <br/> |
   
3. 在 **App.js** `onGetUserNameFail()`函數之後新增下列程式碼。
    
  ```
  
function getWebProperties() {
        // Get the number of lists in the current web.
        context.load(lists);
        context.executeQueryAsync(onWebPropsSuccess, onWebPropsFail);
    }

    function onWebPropsSuccess(sender, args) {
        alert('Number of lists in web: ' + lists.get_count());
    }

    function onWebPropsFail(sender, args) {
        alert('Failed to get list. Error: ' + args.get_message());
    }

    function displayLists() {
        // Get the available SharePoint lists, and then set them into 
        // the context.
        lists = web.get_lists();
        context.load(lists);
        context.executeQueryAsync(onGetListsSuccess, onGetListsFail);
    }

    function onGetListsSuccess(sender, args) {
        // Success getting the lists. Set references to the list 
        // elements and the list of available lists.
        var listEnumerator = lists.getEnumerator();
        var selectListBox = document.getElementById("selectlistbox");
        if (selectListBox.hasChildNodes()) {
            while (selectListBox.childNodes.length >= 1) {
                selectListBox.removeChild(selectListBox.firstChild);
            }
        }
        // Traverse the elements of the collection, and load the name of    
        // each list into the dropdown list box.
        while (listEnumerator.moveNext()) {
            var selectOption = document.createElement("option");
            selectOption.value = listEnumerator.get_current().get_title();
            selectOption.innerHTML = listEnumerator.get_current().get_title();
            selectListBox.appendChild(selectOption);
        }
    }

    function onGetListsFail(sender, args) {
        // Lists couldn't be loaded - display error.
        alert('Failed to get list. Error: ' + args.get_message());
    }

function createlist() {
        // Create a generic SharePoint list with the name that the user specifies.
        var listCreationInfo = new SP.ListCreationInformation();
        var listTitle = document.getElementById("createlistbox").value;
        listCreationInfo.set_title(listTitle);
        listCreationInfo.set_templateType(SP.ListTemplateType.genericList);
        lists = web.get_lists();
        var newList = lists.add(listCreationInfo);
        context.load(newList);
        context.executeQueryAsync(onListCreationSuccess, onListCreationFail);
    }

    function onListCreationSuccess() {
        displayLists();
    }

    function onListCreationFail(sender, args) {
        alert('Failed to create the list. ' + args.get_message());
    }

    function deletelist() {
        // Delete the list that the user specifies.
        var selectListBox = document.getElementById("selectlistbox");
        var selectedListTitle = selectListBox.value;
        var selectedList = web.get_lists().getByTitle(selectedListTitle);
        selectedList.deleteObject();
        context.executeQueryAsync(onDeleteListSuccess, onDeleteListFail);
    }

    function onDeleteListSuccess() {
        displayLists();
    }

    function onDeleteListFail(sender, args) {
        alert('Failed to delete the list. ' + args.get_message());
    }
  ```


## 執行!
<a name="Run1"> </a>

使用者介面和程式碼的第一個部分已備妥、 所以繼續進行且執行增益集來驗證其是否運作。
  
    
    

### 若要執行的增益集


1. 在頁面的底端，選擇執行 (
  
    
    
![Run button](images/Apps_NAPA_Run_Button.png)
  
    
    
) 按鈕。
    
    增益集封裝、 部署，且已安裝 Office 365 開發人員網站上。
    
    安裝之後， SharePoint Add-in啟動。如果增益集不會自動啟動因為例如啟用快顯封鎖程式，請選擇 [增益集] 連結來啟動增益集。
    
  
2. 選擇 [ **按一下這裡以啟動增益集在新視窗中**的連結。
    
    針對SharePoint Add-in畫面隨即出現。
    
  
3. 選擇 [ **取得網頁中的清單計數**] 按鈕。
    
    在對話方塊指定為目前SharePoint Add-in網頁包含兩個清單。(網頁包含 Design Gallery 和主版頁面圖庫列出預設)。
    
  
4. 在 **以下清單名稱**] 方塊中輸入測試清單]，然後選擇 [ **建立清單**] 按鈕。
    
  
5. 開啟 **列出**清單，以確認新的清單會出現在其。
    
  
6. 再選擇 [ **取得計數網頁中的清單**] 按鈕。
    
    網頁現在包含三個清單，包括您剛才建立的清單。
    
  
7. [ **清單**] 清單中選擇 [ **測試] 清單**中，然後按 [ **刪除選取 [清單**] 按鈕。
    
    **測試清單**會從可用清單的清單中消失。
    
  
8. 完成後，請關閉瀏覽器視窗，並再回到您所編輯的專案的 **啟動增益集**視窗中選擇 [ **關閉**] 按鈕。
    
  

## 新增程式碼及控制項來新增及刪除清單項目
<a name="AddControls2"> </a>

使用者可以建立並刪除清單，您可以執行下列步驟，讓他們能夠新增及刪除清單項目。
  
    
    

### 若要新增程式碼和控制項新增及刪除清單項目


1. 選擇 [Default.aspx 檔案以進行編輯。
    
  
2.  `selectlistbox`元素底下，新增下列程式碼。
    
  ```XML
  
<p>
    Items
    <br />
    <input type="text" value="item name here" id="createitembox"/><button id="createitembutton">Create Item</button>
    </p>
    <p>
    <select id="selectitembox"></select> <button id="deleteitembutton">Delete Selected Item</button>
    </p>
  ```


    這段程式碼會新增使用者可以在其中指定的項目、] 按鈕新增至 [] 清單中的項目和按鈕以從清單中刪除項目名稱輸入的方塊。
    
  
3. 選擇 [ **App.js**檔案以進行編輯。
    
  
4.  `$(document).ready()`函數中新增使用者選擇 [ **建立項目**] 和 [ **刪除選取項目**] 按鈕時所呼叫的功能的定義。此外，新增 [ **清單**] 清單方塊以確保時選取新的清單，取得更新清單項目 jQuery 事件處理常式。
    
  ```
  
$("#createitembutton").click(function (event) {
            createitem();
            event.preventDefault();
        });

        $("#deleteitembutton").click(function (event) {
            deleteitem();
            event.preventDefault();
        });
    
        // Update the list items dropdown when a new list
        // is selected in the Lists dropdown.
        $("#selectlistbox").change(function (event) {
            getitems();
            event.preventDefault();
        });
  ```


    > **注意事項**
      > 如果當您執行增益集時不顯示清單項目，請務必 `displayLists();`陳述式而言之後前述的程式碼。

    在下一個步驟中，您將新增的新定義的 JavaScript 功能和支援函數 ( `getItems()`)。此表說明的主要功能所執行的動作。
    

|**函數名稱**|**描述**|
|:-----|:-----|
| `createItem()` <br/> |將項目至清單的使用者選擇，然後授與的項目中 **的項目**] 方塊中指定之使用者的名稱。 <br/> |
| `deleteItem()` <br/> |刪除使用者選擇 [從清單中的項目。 <br/> |
| `getItems()` <br/> |擷取使用者選擇清單中的項目 (及其子項) 的集合。 <br/> |
   
5. 加入 **App.js**、 底部的這段程式碼後 `onDeleteListFail()`函數。
    
  ```
  
function createitem() {
    // Retrieve the list that the user chose, and add an item to it.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedListTitle = selectListBox.value;
    var selectedList = web.get_lists().getByTitle(selectedListTitle);

    var listItemCreationInfo = new SP.ListItemCreationInformation();
    var newItem = selectedList.addItem(listItemCreationInfo);
    var listItemTitle = document.getElementById("createitembox").value;
    newItem.set_item('Title', listItemTitle);
    newItem.update();
    context.load(newItem);
    context.executeQueryAsync(onItemCreationSuccess, onItemCreationFail);
}

function onItemCreationSuccess() {
    // Refresh the list of items.
    getitems();
}

function onItemCreationFail(sender, args) {
    // The item couldn't be created - display an error message.
    alert('Failed to create the item. ' + args.get_message());
}

function deleteitem() {
    // Delete the item that the user chose.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedListTitle = selectListBox.value;
    var selectedList = web.get_lists().getByTitle(selectedListTitle);
    var selectItemBox = document.getElementById("selectitembox");
    var selectedItemID = selectItemBox.value;
    var selectedItem = selectedList.getItemById(selectedItemID);
    selectedItem.deleteObject();
    selectedList.update();
    context.load(selectedList);
    context.executeQueryAsync(onDeleteItemSuccess, onDeleteItemFail);
}

function onDeleteItemSuccess() {
    // Refresh the list of items.
    getitems();
}

function onDeleteItemFail(sender, args) {
    // The item couldn't be deleted - display an error message.
    alert('Failed to delete the item. ' + args.get_message());
}

function getitems() {
    // Using a CAML query, get the items in the list that the user chose, and 
    // set the context to the collection of list items.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedList = selectListBox.value;
    var selectedListTitle = web.get_lists().getByTitle(selectedList);  
    var camlQuery = new SP.CamlQuery();
    camlQuery.set_viewXml("<View><ViewFields>" +
        "<FieldRef Name='ID' />" +
        "<FieldRef Name='Title' />" +
        "</ViewFields></View>')");
    listItemCollection = selectedListTitle.getItems(camlQuery);
    context.load(listItemCollection, "Include(Title, ID)");
    context.executeQueryAsync(onGetItemsSuccess, onGetItemsFail);
}

function onGetItemsSuccess(sender, args) {
    // The list items were retrieved.
    // Show all child nodes.
    var listItemEnumerator = listItemCollection.getEnumerator();
    var selectItemBox = document.getElementById("selectitembox");
    if (selectItemBox.hasChildNodes()) {
        while (selectItemBox.childNodes.length >= 1) {
     selectItemBox.removeChild(selectItemBox.firstChild);
        }
    }
        while (listItemEnumerator.moveNext()) {
            var selectOption = document.createElement("option");
            selectOption.value = listItemEnumerator.get_current().get_item('ID');
            selectOption.innerHTML = listItemEnumerator.get_current().get_item('Title');
            selectItemBox.appendChild(selectOption);
        }
}

function onGetItemsFail(sender, args) {
    // The list items couldn't be retrieved - display an error message.
    alert('Failed to get items. Error: ' + args.get_message());
}
  ```


## 執行修訂的SharePoint Add-in!
<a name="Run2"> </a>

所有使用者介面和程式碼是備妥，所以繼續並執行增益集以確認其運作。
  
    
    

### 若要執行修訂的SharePoint Add-in


1. 在頁面的底端，請選擇 [ **執行**] 按鈕。
    
  
2. 在 **以下清單名稱**] 方塊中輸入新測試清單]，然後選擇 [ **建立清單**] 按鈕。
    
    新的清單會新增至 [ **清單**] 清單。
    
  
3. 在 [ **清單**] 清單中，選擇 [ **新的測試清單**。
    
  
4. 在 **以下項目名稱**] 方塊中輸入項目 1，，然後選擇 [ **建立項目**] 按鈕。
    
    新的清單項目會出現在 **項目**清單。
    
  
5. 重複上述步驟新增項目 2和3 項目。
    
  
6. 在清單中的項目，選擇 [ **項目 2**、，，然後選擇 [ **刪除選取項目**] 按鈕。
    
    從清單中的項目或隱藏 **項目 2** 。
    
  
7. 完成後，請關閉瀏覽器視窗。
    
  

## 匯出至 Visual Studio 專案
<a name="NextSteps"> </a>

開啟專案Visual Studio中選擇 [ **Visual Studio 中開啟**] 按鈕，如圖 3 所示。Napa自動安裝所需的工具和 Visual Studio 中開啟專案。
  
    
    

**圖 3。在 Visual Studio] 按鈕開啟**

  
    
    

  
    
    
![Open in Visual Studio button](images/Apps_Napa_OpenInVS.png)
  
    
    

  
    
    

  
    
    

## 其他資源
<a name="Additional"> </a>


-  [SharePoint 2013 開發概觀](http://msdn.microsoft.com/library/f86e2695-4d7a-4fc5-bc23-689de96c4b06%28Office.15%29.aspx)
    
  
-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  

