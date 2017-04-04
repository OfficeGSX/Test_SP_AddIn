---
title: Définir des autorisations personnalisées sur une liste à l'aide de l'interface REST
ms.prod: SHAREPOINT
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# Définir des autorisations personnalisées sur une liste à l'aide de l'interface REST
Découvrez comment définir des autorisations affinées et personnalisées sur une liste SharePoint à l'aide de l'interface REST et de JavaScript.
Les sites, les listes et les éléments de liste SharePoint sont des types d' **SecurableObject**. Par défaut, un objet sécurisable hérite des autorisations de son parent. Pour définir des autorisations personnalisées d'un objet, vous devez rompre son héritage afin qu'il n'hérite plus des autorisations de son parent, puis définir de nouvelles autorisations en ajoutant ou supprimant des attributions de rôles.
  
    
    


> [!REMARQUE]
> Pour obtenir des liens vers des articles sur la définition des autorisations affinées, voir  [Ressources supplémentaires](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources). 
  
    
    


L'exemple de code présenté dans cet article définit des autorisations personnalisées sur une liste, puis les applique à un groupe. L'exemple utilise l'interface REST pour effectuer les actions suivantes :
  
    
    


- Obtenir l'ID du groupe cible. L'exemple utilise l'ID de groupe pour obtenir les liaisons de rôle actuelles du groupe sur la liste et pour ajouter le nouveau rôle à la liste.
    
  
- Obtenir l'ID de la définition de rôle qui régit les nouvelles autorisations pour le groupe. L'ID est utilisé pour ajouter le nouveau rôle à la liste. Cet exemple utilise une définition de rôle existante pour le nouveau rôle, mais vous pouvez éventuellement créer une définition de rôle.
    
  
- Interrompre l'héritage de rôle sur la liste à l'aide de la méthode  `BreakRoleInheritance`. L'exemple rompt l'héritage de rôle, mais maintient l'ensemble actuel des rôles. (Sinon, vous pouvez choisir de ne pas copier les attributions de rôles et d'ajouter l'utilisateur actuel au niveau de l'autorisation de gestion.)
    
  
- Supprimer l'attribution de rôles actuelle du groupe sur la liste en envoyant une requête DELETE au point de terminaison d'attribution de rôles. (Si vous choisissez de ne pas copier les attributions de rôles, ignorez cette étape.)
    
  
- CCH Ajouter une attribution de rôle pour le groupe à la liste à l'aide de la méthode  `AddRoleAssignment`, qui lie le groupe à la définition des rôles et ajoute le rôle à la liste.
    
  

## Conditions requises pour utiliser l'exemple de cet article
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Pour utiliser les exemples de cet article, vous avez besoin des éléments suivants :
  
    
    

- Un environnement de développement SharePoint 2013 (isolation de l'application requise pour les scénarios locaux)
    
  
- Visual Studio 2012 ou Visual Studio 2013 avec Outils de développement Office pour Visual Studio 2012, ou 
  
    
    
Outils de développement Office 365 « Napa » (SharePoint Online uniquement)
    
  
Vous aurez également besoin de définir les autorisations de complément **Full Control** pour l'étendue **Web**. Seuls les utilisateurs disposant des autorisations nécessaires pour modifier les autorisations de liste (tels que les propriétaires de sites) peuvent exécuter ce complément.
  
    
    

## Exemple : définir des autorisations personnalisées sur une liste à l'aide de l'interface REST
<a name="bk_example1"> </a>

Les exemples suivants représentent le contenu du fichier App.js dans un complément hébergé par SharePoint. Le premier exemple utilise la bibliothèque inter-domaine JavaScript pour créer et envoyer des requêtes HTTP. Le deuxième exemple utilise des requêtes AJAX jQuery.
  
    
    
Avant d'exécuter le code, remplacez les valeurs d'espace réservé par des valeurs réelles. Si vous utilisez une langue ou un environnement différent, vous devez ajouter ou modifier certains composants de la requête. Pour plus d'informations, voir  [Différences des requêtes REST en fonction de l'environnement](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer).
  
    
    
 **Exemple 1 : requêtes de bibliothèque inter-domaine**
  
    
    



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

 **Exemple 2 : requête jQuery AJAX**
  
    
    



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


## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Familiarisation avec le service REST SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md)
    
  
- Ressources REST :
    
     [Ressource GroupCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_GroupCollection)
    
     [Ressource Group](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_Group)
    
     [Ressource RoleAssignmentCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignmentCollection)
    
     [Ressource RoleAssignment](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignment)
    
     [Ressource RoleDefinitionCollection](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinitionCollection)
    
     [Ressource RoleDefinition](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinition)
    
  
- Articles TechNet :
    
     [Référence pour les autorisations affinées pour SharePoint Server 2013](http://technet.microsoft.com/fr-fr/library/dn169567.aspx)
    
     [Meilleures pratiques pour l'utilisation des autorisations affinées dans SharePoint Server 2013](http://technet.microsoft.com/fr-fr/library/gg128955.aspx)
    
     [Autorisations utilisateur et niveaux d'autorisation dans SharePoint 2013](http://technet.microsoft.com/fr-fr/library/cc721640.aspx)
    
  

