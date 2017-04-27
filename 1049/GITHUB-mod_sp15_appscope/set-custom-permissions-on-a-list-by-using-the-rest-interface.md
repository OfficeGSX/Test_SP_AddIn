---
title: Установка настраиваемых разрешений для списка с помощью интерфейса REST
ms.prod: MULTIPLEPRODUCTS
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# Установка настраиваемых разрешений для списка с помощью интерфейса REST
Сведения о том, как определять точные настраиваемые разрешения для списка SharePoint с помощью интерфейса REST и JavaScript.
Сайты, списки и элементы списков SharePoint это типы **SecurableObject**. По умолчанию защищаемый объект наследует разрешения своего родителя. Чтобы задать настраиваемые разрешения для объекта, необходимо нарушить его наследование, чтобы он перестал наследовать разрешения от родительского объекта, а затем определить новые разрешения, добавив или удалив назначения ролей.





> **Примечание**
> Ссылки на статьи о настройке детальных разрешений см. в разделе  [Дополнительные ресурсы](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources). 





Пример кода в этой статье задает настраиваемые разрешения для списка, а затем меняет разрешения группы для него. В примере интерфейс REST используется в следующих целях.





- Чтобы получить идентификатор целевой группы. Пример использует его, чтобы получить текущие привязки ролей для группы в списке и чтобы добавить новую роль в список.


- Получить идентификатор определения роли, задающего новые разрешения для группы. Этот идентификатор используется для добавления новой роли в список. Пример применяет существующее определение для новой роли, но при необходимости вы можете создать новое определение.


- Чтобы нарушить наследование роли для списка, используя метод  `BreakRoleInheritance`. Пример нарушает наследование роли, но сохраняет текущий набор ролей. (Или же можно не копировать назначения ролей, а добавить текущего пользователя на уровень разрешений "Управление".)


- Чтобы удалить текущее назначение ролей группы в списке, отправив запрос DELETE конечной точки назначения роли. (Если вы решили не копировать назначения ролей, пропустите этот шаг.)


- Чтобы добавить назначение роли для группы в списке, используя метод  `AddRoleAssignment`, который привязывает группу к определению роли и добавляет роль в список.



## Необходимые условия для использования примеров в этой статье
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Для использования примеров, описанных в этой статье, вам необходимо следующее:




- Среда разработки SharePoint 2013 (для установки приложения внутри организации потребуется изоляция).


- Visual Studio 2012 или Visual Studio 2013 с Инструменты разработчика Office для Visual Studio 2012 или 



Napa (только для SharePoint Online).


Вам также потребуется задать разрешения **Full Control** надстройки в области **Web**. Только пользователи с достаточными разрешениями для изменения разрешений списка (например, владельцы сайта) могут запускать эту надстройку.




## Пример. Установка настраиваемых разрешений для списка с помощью интерфейса REST
<a name="bk_example1"> </a>

В следующих примерах представлено содержимое файла App.js в надстройке с размещением в SharePoint. Первый пример использует междоменную библиотеку JavaScript для создания и отправки HTTP-запросов. Второй пример используется запросы jQuery AJAX.



Прежде чем выполнять код, замените значения заполнителей на фактические. Если вы используете другой язык или другую среду, необходимо добавить или изменить некоторые компоненты запросов. Дополнительные сведения см. в разделе  [Отличия запросов REST в разных средах](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer).



 **Пример 1. Запросы междоменной библиотеки**





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

 **Пример 2. Запросы jQuery AJAX**





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


## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Знакомство со службой REST для SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)


-  [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md)


- Ресурсы REST:

     [Ресурс GroupCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_GroupCollection)

     [Ресурс Group](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_Group)

     [Ресурс RoleAssignmentCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignmentCollection)

     [Ресурс RoleAssignment](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignment)

     [Ресурс RoleDefinitionCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinitionCollection)

     [Ресурс RoleDefinition](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinition)


- Статьи TechNet:

     [Справочник по детальным разрешениям для SharePoint Server 2013](http://technet.microsoft.com/ru-ru/library/dn169567.aspx)

     [Рекомендации по использованию детальных разрешений в SharePoint Server 2013](http://technet.microsoft.com/ru-ru/library/gg128955.aspx)

     [Разрешения пользователей и уровни разрешений в SharePoint 2013](http://technet.microsoft.com/ru-ru/library/cc721640.aspx)



