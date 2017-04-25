---
title: REST インターフェイスを使用してリストにカスタム アクセス許可を設定する
ms.prod: SHAREPOINT
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# REST インターフェイスを使用してリストにカスタム アクセス許可を設定する
REST インターフェイスと JavaScript を使って SharePoint リストにカスタムの詳細に設定されたアクセス許可を定義する方法について説明します。
SharePoint のサイト、リスト、およびリスト項目は、 **SecurableObject** の種類です。既定では、セキュリティ設定が可能なオブジェクトは、親のアクセス許可を継承します。オブジェクトのカスタムのアクセス許可を設定するには、継承を停止して、親からのアクセス許可の継承を停止し、ロールの割り当てを追加または削除して、新しいアクセス許可を定義します。





> **メモ**
> 詳細に設定されたアクセス許可の設定の記事のリンクについては、「 [その他の技術情報](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources)」を参照してください。 





この記事のコードの例は、リストのカスタム アクセス許可を設定して、グループのアクセス許可を変更します。例では REST インターフェイスを使用して、次の処理を行います。





- ターゲット グループの ID を取得します。例では、グループ ID を使用して、リストのグループの現在のロールのバインドを取得し、新しいロールをリストに追加しています。


- グループの新しいアクセス許可を定義するロール定義の ID を取得します。この ID は新しいロールをリストに追加するために使用します。この例では、新しいロールに対して既存のロール定義を使用していますが、オプションで新しいロール定義を作成することもできます。


-  `BreakRoleInheritance` を使用して、リストのロールの継承を解除します。 例ではロールの継承を解除していますが、現在のロールのセットは保持します。(または、ロールの割り当てをコピーせずに、現在のユーザーをアクセス許可レベルの管理に追加する方法も選択できます。)


- DELETE 要求をロールの割り当てエンドポイントに送信することで、リストに対するグループの現在のロール割り当てを削除します。(ロールの割り当てをコピーしないことを選択した場合は、この手順をスキップします。)


-  `AddRoleAssignment` メソッドを使用してグループのロールの割り当てをリストに追加します。これにより、グループがロール定義にバインドされ、ロールがリストに追加されます。



## この記事の例を使用するための前提条件
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

この記事の例を使用するには、次のものが必要です。




- SharePoint 2013 開発環境 (オンプレミス シナリオではアプリの分離が必要)


- Office Developer Tools for Visual Studio 2012 を含む Visual Studio 2012 または Visual Studio 2013、あるいは



Napa (SharePoint Online のみ)


 **Web** スコープで **Full Control** アドインのアクセス許可を設定することも必要です。リストのアクセス許可を変更するのに十分なアクセス許可を持っているユーザー (リストの所有者など) だけが、このアドインを実行できます。




## 例: REST インターフェイスを使用してリストにカスタム アクセス許可を設定する
<a name="bk_example1"> </a>

次の例に、SharePoint ホスト型アドインの App.js ファイルの内容を示します。最初の例は JavaScript クロス ドメイン ライブラリを使用して HTTP 要求を作成して送信します。2 番目の例は、jQuery AJAX 要求を使用します。



コードを実行する前に、プレースホルダーの値を実際の値に置き換えます。別の言語や環境を使用している場合、いくつかの要求コンポーネントを追加または変更する必要があります。詳細については、「 [環境によって異なる REST 要求](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer)」を参照してください。



 **例1: クロス ドメイン ライブラリの要求**





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
}```

 **例 2: jQuery AJAX の要求**





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
}```


## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 REST サービスの概要](get-to-know-the-sharepoint-2013-rest-service.md)


-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)


- REST リソース:

     [GroupCollection リソース](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_GroupCollection)

     [Group リソース](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_Group)

     [RoleAssignmentCollection リソース](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignmentCollection)

     [RoleAssignment リソース](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignment)

     [RoleDefinitionCollection リソース](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinitionCollection)

     [RoleDefinition リソース](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinition)


- TechNet の記事:

     [SharePoint Server 2013 の詳細に設定されたアクセス許可の参照](http://technet.microsoft.com/ja-jp/library/dn169567.aspx)

     [SharePoint Server 2013 で詳細に設定された権限の使用のベスト プラクティス](http://technet.microsoft.com/ja-jp/library/gg128955.aspx)

     [SharePoint 2013 のユーザー権限とアクセス許可レベル](http://technet.microsoft.com/ja-jp/library/cc721640.aspx)



