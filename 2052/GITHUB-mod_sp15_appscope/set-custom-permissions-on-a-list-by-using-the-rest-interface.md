---
title: 使用 REST 接口设置列表的自定义权限
ms.prod: SHAREPOINT
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# 使用 REST 接口设置列表的自定义权限
了解如何使用 REST 接口和 JavaScript 定义自定义和细化 SharePoint 列表的权限。
SharePoint 网站、列表和列表项的类型为 **SecurableObject**。默认情况下，安全对象继承其父项的权限。要设置对象的自定义权限，您需要打断继承关系，使其停止从其父项继承权限，然后通过添加或删除角色分配定义新权限。
  
    
    


> [!注释]
> 有关设置细化权限的文章的链接，请参阅 [其他资源](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources)。 
  
    
    


本文中的代码示例设置列表的自定义权限，然后更改某个组对该列表的权限。本示例使用 REST 接口执行以下操作：
  
    
    


- 获取目标组的 ID。本示例使用 ID 获取列表中组的当前角色绑定，并将新角色添加到列表中。
    
  
- 获取定义组的新权限的角色定义的 ID。ID 用于将新角色添加到列表中。本示例使用新角色的现有角色定义，但您可以选择创建新的角色定义。
    
  
- 使用  `BreakRoleInheritance` 方法打断列表上的角色继承关系。本示例打断角色继承关系，但保留现有角色集。（您也可以选择不复制任何角色分配，并将当前角色添加到"管理"权限级别。）
    
  
- 通过向角色分配终结点发送 DELETE 请求，移除列表上组的当前角色分配。（如果您选择不复制任何角色分配，您可跳过此步骤。）
    
  
- 使用  `AddRoleAssignment` 方法，将组的角色分配添加到列表中，这会将组绑定到角色定义并将角色添加到列表中。
    
  

## 使用本文中示例的先决条件
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

若要使用本文中的示例，需具备以下各项：
  
    
    

- SharePoint 2013 开发环境（本地方案需要应用程序隔离）
    
  
- Visual Studio 2008 或具有 Visual Studio 2012 Office 开发人员工具 的 Visual Studio 2013，或者 
  
    
    
Napa（仅 SharePoint Online）
    
  
您还需要在 **Web** 作用域中设置 **Full Control**外接程序权限。只有具有更改列表权限的充分权限的用户（如网站所有者）才能运行此外接程序。
  
    
    

## 示例：使用 REST 接口设置列表的自定义权限
<a name="bk_example1"> </a>

以下示例显示 SharePoint 托管的外接程序中 App.js 文件的内容。第一个示例使用 JavaScript 跨域库构建和发送 HTTP 请求。第二个示例使用 jQuery AJAX 请求。
  
    
    
在运行代码之前，将占位符值替换实际值。如果您使用其他语言或环境，您将需要添加或更改一些请求组件。有关详细信息，请参阅  [REST 请求如何因环境而异](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer)。
  
    
    
 **示例 1：跨域库请求**
  
    
    



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

 **示例 2：jQuery AJAX 请求**
  
    
    



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


## 其他资源
<a name="bk_addresources"> </a>


-  [了解 SharePoint 2013 REST 服务](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [使用 REST 处理列表和列表项](working-with-lists-and-list-items-with-rest.md)
    
  
- REST 资源：
    
     [GroupCollection 资源](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_GroupCollection)
    
     [组资源](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_Group)
    
     [RoleAssignmentCollection 资源](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignmentCollection)
    
     [RoleAssignment 资源](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignment)
    
     [RoleDefinitionCollection 资源](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinitionCollection)
    
     [RoleDefinition 资源](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinition)
    
  
- TechNet 文章：
    
     [SharePoint Server 2013 的细化权限引用](http://technet.microsoft.com/zh-cn/library/dn169567.aspx)
    
     [在 SharePoint Server 2013 中使用细化权限的最佳实践](http://technet.microsoft.com/zh-cn/library/gg128955.aspx)
    
     [SharePoint 2013 中的用户权限和权限级别](http://technet.microsoft.com/zh-cn/library/cc721640.aspx)
    
  

