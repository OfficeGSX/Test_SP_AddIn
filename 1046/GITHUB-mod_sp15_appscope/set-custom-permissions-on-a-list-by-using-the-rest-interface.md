---
title: Definir permissões personalizadas em uma lista, usando a interface REST
ms.prod: SHAREPOINT
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# Definir permissões personalizadas em uma lista, usando a interface REST
Saiba como definir permissões refinadas personalizadas em uma lista de SharePoint usando a interface REST e JavaScript.
SharePoint sites, listas e itens de lista são tipos de **SecurableObject**. Por padrão, um objeto protegível herda as permissões de seu pai. Para definir permissões personalizadas para um objeto, você precisará interromper a herança de seu para que ele pára herdando permissões de seu pai e então definir novas permissões adicionando ou removendo atribuições de função.
  
    
    


> [!OBSERVAçãO]
> Consulte os  [recursos adicionais](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources) para obter links para artigos sobre a configuração das permissões refinadas.
  
    
    


O exemplo de código neste artigo define permissões personalizadas em uma lista e altera as permissões de um grupo a ele. O exemplo utiliza a interface REST para:
  
    
    


- Obter a identificação do grupo de destino. O exemplo usa a ID de grupo para obter as associações role atual para o grupo na lista e adicionar a nova função à lista.
    
  
- Obter a identificação da definição de função que define as novas permissões para o grupo. A ID é usada para adicionar a nova função à lista. Este exemplo usa uma definição de função existente para a nova função, mas opcionalmente, você pode criar uma nova definição de função.
    
  
- Interromper a herança de função na lista usando o método  `BreakRoleInheritance` . O exemplo quebras de herança de função, mas mantém o conjunto atual de funções. (Como alternativa, você pode escolher não para copiar quaisquer atribuições de função e para adicionar o usuário atual para o nível de permissão de gerenciar.)
    
  
- Remova a atribuição de função atual do grupo na lista, enviando uma solicitação de exclusão para o ponto de extremidade de atribuição de função. (Se você optar por não copiar quaisquer atribuições de função, você faria ignorar esta etapa.)
    
  
- Adicione uma atribuição de função para o grupo na lista usando o método  `AddRoleAssignment` , que vincula o grupo à definição de função e adiciona a função à lista.
    
  

## Pré-requisitos para usar o exemplo neste artigo
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Para usar o exemplo neste artigo, você precisará de:
  
    
    

- Um ambiente de desenvolvimento SharePoint 2013 (isolamento de aplicativo necessário para os cenários de local)
    
  
- Visual Studio 2012 ou Visual Studio 2013 com Office Developer Tools para Visual Studio 2012, ou
  
    
    
Napa (SharePoint Online somente)
    
  
Você também precisará definir permissões do suplemento de **Full Control** no escopo do **Web**. Somente os usuários que têm permissões suficientes para alterar permissões de lista (por exemplo, os proprietários de site) podem executar este suplemento.
  
    
    

## Exemplo: Definir permissões personalizadas em uma lista, usando a interface REST
<a name="bk_example1"> </a>

Os exemplos a seguir representam o conteúdo do arquivo App.js em um SharePoint-hospedado suplemento. O primeiro exemplo usa a biblioteca de entre domínios de JavaScript para criar e enviar solicitações HTTP. O segundo exemplo usa o jQuery AJAX solicitações.
  
    
    
Antes de executar o código, substitua os valores de espaço reservado com valores reais. Se você estiver usando um idioma diferente ou um ambiente, você precisará adicionar ou alterar alguns componentes de solicitação. Consulte  [solicitações como REST diferem pelo ambiente](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer) para obter mais informações.
  
    
    
 **Exemplo 1: As solicitações de biblioteca entre domínios**
  
    
    



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

 **Exemplo 2: jQuery AJAX solicitações**
  
    
    



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


## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
- Recursos REST:
    
     [Recurso GroupCollection](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_GroupCollection)
    
     [Recursos de grupo](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_Group)
    
     [Recurso de RoleAssignmentCollection](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleAssignmentCollection)
    
     [Recurso de RoleAssignment](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleAssignment)
    
     [Recurso de RoleDefinitionCollection](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleDefinitionCollection)
    
     [Recursos RoleDefinition](http://msdn.microsoft.com/library/c5e49290-78d4-4167-b4de-da32376bbf90%28Office.15%29.aspx#bk_RoleDefinition)
    
  
- Artigos da TechNet
    
     [Referência de permissão refinadas para o SharePoint Server 2013](http://technet.microsoft.com/en-us/library/dn169567.aspx)
    
     [Práticas recomendadas para a utilização das permissões refinadas no SharePoint Server 2013](http://technet.microsoft.com/en-us/library/gg128955.aspx)
    
     [Permissões de usuário e níveis de permissão no SharePoint 2013](http://technet.microsoft.com/en-us/library/cc721640.aspx)
    
  

