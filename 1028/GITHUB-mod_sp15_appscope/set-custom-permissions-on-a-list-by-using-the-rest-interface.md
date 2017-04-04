---
title: 使用 REST 介面在清單上設定自訂權限
ms.prod: MULTIPLEPRODUCTS
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# 使用 REST 介面在清單上設定自訂權限
了解如何使用 REST 介面和JavaScriptSharePoint清單上定義自訂、 微調權限。
SharePoint網站、 清單及清單項目是 **SecurableObject**類型。根據預設，安全的物件會繼承其父項的權限。若要設定自訂權限的物件，您需要中斷其繼承使它會停止繼承其父系的權限和再定義新的權限新增或移除角色指派。
  
    
    


> **注意事項**
> 請參閱設定微調權限的相關文章連結 [的其他資源](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources)。
  
    
    


本文中的程式碼範例會設定自訂權限] 清單中，並再變更群組的權限給它。此範例使用 REST 介面：
  
    
    


- 取得目標群組的識別碼。範例來取得目前群組的角色繫結清單和新的角色新增至清單，使用群組識別碼。
    
  
- 取得角色定義會定義新的權限群組的識別碼。ID 用來將新的角色新增至清單。本範例會使用現有的角色定義新的角色，但是您可以選擇性地建立新的角色定義。
    
  
- 會自動換行角色繼承清單上使用 `BreakRoleInheritance`方法。範例會中斷角色繼承但目前角色的集合。(或者，您可以選擇不將複製任何角色指派和將目前的使用者新增至管理權限等級。)
    
  
- 刪除要求傳送至角色指派端點來移除清單上的群組目前角色指派。(如果您選擇不要複製任何角色指派，您會略過此步驟。)
    
  
- 使用 `AddRoleAssignment`方法，由其繫結至角色定義的群組並新增至清單的角色新增至清單的群組的角色指派。
    
  

## 此範例使用本文中的先決條件
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

若要使用本文中的範例，您將需要：
  
    
    

- SharePoint 2013開發環境 (隔離應用程式所需的內部部署案例)
    
  
- Visual Studio 2012或Visual Studio 2013與Visual Studio 2012 Office 開發人員工具，或
  
    
    
Napa (僅限SharePoint Online )
    
  
您也需要在 **Web**範圍內設定 **Full Control**增益集的權限。僅限具有足夠的權限變更 (例如網站擁有者) 清單權限的使用者可以執行此增益集。
  
    
    

## 範例： 使用 REST 介面在清單上設定自訂權限
<a name="bk_example1"> </a>

下列範例代表SharePointApp.js 檔案內容-主控增益集。第一個範例使用JavaScript跨網域文件庫來建置並傳送 HTTP 要求。第二個範例使用 jQuery AJAX 要求。
  
    
    
執行的程式碼之前，請將預留位置值取代實際值。如果您使用不同語言或環境，您需要新增或變更某些要求元件。如需詳細資訊，請參閱 [如何 REST 要求不同的環境](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer)。
  
    
    
 **範例 1： 跨網域文件庫要求**
  
    
    



```

'use strict';

// Change placeholder values before you run this code.
var listTitle = 'List 1';
var groupName = 'Group A';
var targetRoleDefinitionName = 'Contribute';
var appweburl;
var hostweburl;
var executor;
var groupId;
var targetRoleDefinitionId;

$(document).ready( function() {

    //Get the URI decoded URLs.
    hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
    appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

    // Load the cross-domain library file and continue to the custom code.
    var scriptbase = hostweburl + "/_layouts/15/";
    $.getScript(scriptbase + "SP.RequestExecutor.js", getTargetGroupId);
});

// Get the ID of the target group.
function getTargetGroupId() {
    executor = new SP.RequestExecutor(appweburl);
    var endpointUri = appweburl + "/_api/SP.AppContextSite(@target)/web/sitegroups/getbyname('";
    endpointUri += groupName + "')/id" + "?@target='" + hostweburl + "'";

    executor.executeAsync({
        url: endpointUri,
        method: 'GET',
        headers: { 'accept':'application/json;odata=verbose' },
        success: function(responseData) {
            var jsonObject = JSON.parse(responseData.body);
            groupId = jsonObject.d.Id;
            getTargetRoleDefinitionId();
        },
        error: errorHandler
   });
}

// Get the ID of the role definition that defines the permissions
// you want to assign to the group.
function getTargetRoleDefinitionId() {
    var endpointUri = appweburl + "/_api/SP.AppContextSite(@target)/web/roledefinitions/getbyname('";
    endpointUri += targetRoleDefinitionName + "')/id" + "?@target='" + hostweburl + "'";

    executor.executeAsync({
        url: endpointUri,
        method: 'GET',
        headers: { 'accept':'application/json;odata=verbose' },
        success: function(responseData) {
            var jsonObject = JSON.parse(responseData.body)
            targetRoleDefinitionId = jsonObject.d.Id;
            breakRoleInheritanceOfList();
        },
        error: errorHandler
    });
}

// Break role inheritance on the list.
function breakRoleInheritanceOfList() {
    var endpointUri = appweburl + "/_api/SP.AppContextSite(@target)/web/lists/getbytitle('";
    endpointUri += listTitle + "')/breakroleinheritance(true)?@target='" + hostweburl + "'";

    executor.executeAsync({
        url: endpointUri,
        method: 'POST',
        headers: { 'X-RequestDigest':$('#__REQUESTDIGEST').val() },
        success: deleteCurrentRoleForGroup,
        error: errorHandler
    });
}

// Remove the current role assignment for the group on the list.
function deleteCurrentRoleForGroup() {
    var endpointUri = appweburl + "/_api/SP.AppContextSite(@target)/web/lists/getbytitle('";
    endpointUri += listTitle + "')/roleassignments/getbyprincipalid('" + groupId + "')?@target='" + hostweburl + "'";

    executor.executeAsync({
        url: endpointUri,
        method: 'POST',
        headers: { 
            'X-RequestDigest':$('#__REQUESTDIGEST').val(),
            'X-HTTP-Method':'DELETE'
        },
        success: setNewPermissionsForGroup,
        error: errorHandler
    });
}

// Add the new role assignment for the group on the list.
function setNewPermissionsForGroup() {
    var endpointUri = appweburl + "/_api/SP.AppContextSite(@target)/web/lists/getbytitle('";
    endpointUri += listTitle + "')/roleassignments/addroleassignment(principalid=" + groupId;
    endpointUri += ",roledefid=" + targetRoleDefinitionId + ")?@target='" + hostweburl + "'";

    executor.executeAsync({
        url: endpointUri,
        method: 'POST',
        headers: { 'X-RequestDigest':$('#__REQUESTDIGEST').val() },
        success: successHandler,
        error: errorHandler
    });
}

// Get parameters from the query string.
// For production purposes you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
    var params = document.URL.split("?")[1].split("&amp;");
    for (var i = 0; i < params.length; i = i + 1) {
        var singleParam = params[i].split("=");
        if (singleParam[0] == paramToRetrieve) return singleParam[1];
    }
}

function successHandler() {
    alert('Request succeeded.');
} 

function errorHandler(xhr, ajaxOptions, thrownError) {
    alert('Request failed: ' + xhr.status + '\\n' + thrownError + '\\n' + xhr.responseText);
}
```

 **範例 2： jQuery AJAX 要求**
  
    
    



```

// Change placeholder values before you run this code.
var siteUrl = 'http://server/site';
var listTitle = 'List 1';
var groupName = 'Group A';
var targetRoleDefinitionName = 'Contribute';
var groupId;
var targetRoleDefinitionId;

$(document).ready( function() {
    getTargetGroupId();
});

// Get the ID of the target group.
function getTargetGroupId() {
    $.ajax({
        url: siteUrl + '/_api/web/sitegroups/getbyname(\\'' + groupName + '\\')/id',
        type: 'GET',
        headers: { 'accept':'application/json;odata=verbose' },
        success: function(responseData) {
            groupId = responseData.d.Id;
            getTargetRoleDefinitionId();
        },
        error: errorHandler
   });
}

// Get the ID of the role definition that defines the permissions
// you want to assign to the group.
function getTargetRoleDefinitionId() {
    $.ajax({
        url: siteUrl + '/_api/web/roledefinitions/getbyname(\\''
            + targetRoleDefinitionName + '\\')/id',
        type: 'GET',
        headers: { 'accept':'application/json;odata=verbose' },
        success: function(responseData) {
            targetRoleDefinitionId = responseData.d.Id;
            breakRoleInheritanceOfList();
        },
        error: errorHandler
    });
}

// Break role inheritance on the list.
function breakRoleInheritanceOfList() {
    $.ajax({
        url: siteUrl + '/_api/web/lists/getbytitle(\\'' + listTitle
            + '\\')/breakroleinheritance(true)',
        type: 'POST',
        headers: { 'X-RequestDigest':$('#__REQUESTDIGEST').val() },
        success: deleteCurrentRoleForGroup,
        error: errorHandler
    });
}

// Remove the current role assignment for the group on the list.
function deleteCurrentRoleForGroup() {
    $.ajax({
        url: siteUrl + '/_api/web/lists/getbytitle(\\'' + listTitle
            + '\\')/roleassignments/getbyprincipalid(' + groupId + ')',
        type: 'POST',
        headers: { 
            'X-RequestDigest':$('#__REQUESTDIGEST').val(),
            'X-HTTP-Method':'DELETE'
        },
        success: setNewPermissionsForGroup,
        error: errorHandler
    });
}

// Add the new role assignment for the group on the list.
function setNewPermissionsForGroup() {
    $.ajax({
        url: siteUrl + '/_api/web/lists/getbytitle(\\'' + listTitle
            + '\\')/roleassignments/addroleassignment(principalid='
            + groupId + ',roledefid=' + targetRoleDefinitionId + ')',
        type: 'POST',
        headers: { 'X-RequestDigest':$('#__REQUESTDIGEST').val() },
        success: successHandler,
        error: errorHandler
    });
}

function successHandler() {
    alert('Request succeeded.');
} 

function errorHandler(xhr, ajaxOptions, thrownError) {
    alert('Request failed: ' + xhr.status + '\\n' + thrownError + '\\n' + xhr.responseText);
}
```


## 其他資源
<a name="bk_addresources"> </a>


-  [若要了解 SharePoint 2013 REST 服務取得](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)
    
  
- 其他資源：
    
     [GroupCollection 資源](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_GroupCollection)
    
     [群組資源](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_Group)
    
     [RoleAssignmentCollection 資源](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleAssignmentCollection)
    
     [RoleAssignment 資源](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleAssignment)
    
     [RoleDefinitionCollection 資源](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleDefinitionCollection)
    
     [RoleDefinition 資源](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleDefinition)
    
  
- TechNet 文章
    
     [SharePoint Server 2013 的微調權限參考 (英文)](http://technet.microsoft.com/en-us/library/dn169567.aspx)
    
     [使用 SharePoint Server 2013 中的微調權限的最佳作法](http://technet.microsoft.com/en-us/library/gg128955.aspx)
    
     [使用者權限與 SharePoint 2013 中的權限層級](http://technet.microsoft.com/en-us/library/cc721640.aspx)
    
  

