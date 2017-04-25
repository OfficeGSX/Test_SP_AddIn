---
title: Establecer permisos personalizados en una lista usando la interfaz REST
ms.prod: SHAREPOINT
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# Establecer permisos personalizados en una lista usando la interfaz REST
Aprenda a definir permisos específicos personalizados en una lista de SharePoint con la interfaz REST y JavaScript.
Los sitios, las listas y los elementos de lista de SharePoint son tipos de **SecurableObject**. De forma predeterminada, un objeto protegible hereda los permisos de su elemento primario. Para establecer permisos personalizados en un objeto, tiene que romper su herencia para que deje de heredar permisos del elemento primario y luego definir permisos nuevos agregando o eliminando asignaciones de roles.





> **NOTA**
> En  [Recursos adicionales](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources) encontrará vínculos a artículos sobre la configuración de permisos específicos.





El ejemplo de código de este artículo establece permisos personalizados en una lista y luego cambia los permisos que un grupo tiene en ella. En el ejemplo se usa la interfaz REST para:





- Obtener el id. del grupo de destino. En el ejemplo se usa el id. de grupo para obtener los enlaces de rol actuales para el grupo que hay en la lista y agregar el nuevo rol a la lista.


- Obtener el id. de la definición de rol que define los nuevos permisos para el grupo. El id. se usa para agregar el nuevo rol a la lista. En este ejemplo se usa una definición de rol existente para el nuevo rol, pero también puede crear una nueva definición de rol.


- Interrumpir la herencia de roles en la lista usando el método  `BreakRoleInheritance`. En el ejemplo se rompe la herencia de rol, pero se mantiene el conjunto actual de roles. (Como alternativa, puede elegir no copiar las asignaciones de roles y agregar el usuario actual al nivel de permisos Administrar).


- Quitar la asignación de rol actual del grupo que hay en la lista enviando una solicitud de eliminación (DELETE) al extremo de la asignación de rol. (Si no decide copiar las asignaciones de rol, omita este paso).


- Agregar una asignación de rol para el grupo en la lista usando el método  `AddRoleAssignment`, que enlaza el grupo a la definición de rol y agrega el rol a la lista.



## Requisitos previos para usar los ejemplos de este artículo
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Para usar el ejemplo de este artículo, necesitará:




- Un entorno de desarrollo de SharePoint 2013 (hay que aislar la aplicación en los escenarios locales)


- Visual Studio 2012 o Visual Studio 2013 con Office Developer Tools para Visual Studio 2012, o 



Napa (solo SharePoint Online)


También tendrá que establecer los permisos del complemento **Full Control** en el ámbito **Web**. Solo los usuarios que tengan permisos suficientes para cambiar los permisos de la lista (como los propietarios de sitios) pueden ejecutar este complemento.




## Ejemplo: establecer permisos personalizados en una lista usando la interfaz REST
<a name="bk_example1"> </a>

En los ejemplos siguientes se representa el contenido del archivo App.js de un complemento hospedado en SharePoint. El primer ejemplo usa la biblioteca entre dominios de JavaScript para compilar y enviar solicitudes HTTP. El segundo ejemplo usa las solicitudes jQuery AJAX.



Antes de ejecutar el código, reemplace los valores de marcador de posición por los valores reales. Si usa un lenguaje o entorno distintos, tiene que agregar o cambiar algunos componentes de la solicitud. Vea  [Cómo difieren las solicitudes REST por entorno](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer) para más información.



 **Ejemplo 1: solicitudes de bibliotecas entre dominios**





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

 **Ejemplo 2: solicitudes jQuery AJAX**





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


## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Introducción al servicio REST para SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)


-  [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md)


- Recursos de REST:

     [Recurso GroupCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_GroupCollection)

     [Recurso de grupo](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_Group)

     [Recurso RoleAssignmentCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignmentCollection)

     [Recurso RoleAssignment](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignment)

     [Recurso RoleDefinitionCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinitionCollection)

     [Recurso RoleDefinition](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinition)


- Artículos de TechNet:

     [Referencia sobre la personalización avanzada de permisos para SharePoint Server 2013](http://technet.microsoft.com/es-es/library/dn169567.aspx)

     [Procedimientos recomendados para usar la personalización avanzada de permisos en SharePoint Server 2013](http://technet.microsoft.com/es-es/library/gg128955.aspx)

     [Permisos de usuario y niveles de permisos en SharePoint 2013](http://technet.microsoft.com/es-es/library/cc721640.aspx)



