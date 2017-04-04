---
title: Festlegen von benutzerdefinierten Berechtigungen in einer Liste mit der REST-Schnittstelle
ms.prod: MULTIPLEPRODUCTS
ms.assetid: d5fcc26c-4e44-404b-aacf-e9351af8cc7d
---


# Festlegen von benutzerdefinierten Berechtigungen in einer Liste mit der REST-Schnittstelle
Informationen zum Definieren von benutzerdefinierten, abgestimmten Berechtigungen in einer SharePoint-Liste mit der REST-Schnittstelle und JavaScript.
SharePoint-Websites, Listen und Listenelemente sind **SecurableObject**-Typen. Standardmäßig erbt ein sicherungsfähiges Objekt die Berechtigung des übergeordneten Objekts. Um benutzerdefinierte Berechtigungen für ein Objekt festzulegen, müssen Sie die Vererbung abbrechen, sodass die Berechtigungen nicht mehr vom übergeordneten Objekt vererbt werden, definieren Sie anschließend neue Berechtigungen, indem Sie Rollenzuweisungen hinzufügen oder entfernen.
  
    
    


> [!HINWEIS]
> Unter  [Zusätzliche Ressourcen](set-custom-permissions-on-a-list-by-using-the-rest-interface.md#bk_addresources) finden Sie Links zu Artikeln zum Festlegen abgestimmter Berechtigungen.
  
    
    


In dem Codebeispiel in diesem Artikel werden benutzerdefinierte Berechtigungen in einer Liste zugewiesen, anschließend werden die Gruppenberechtigungen für die Liste geändert. In dem Beispiel wird die REST-Schnittstelle für folgende Vorgänge verwendet:
  
    
    


- Zum Abrufen der ID der Zielgruppe. In dem Beispiel wird die Gruppen-ID verwendet, um die aktuellen Rollenbindungen für die Gruppe in der Liste abzurufen, und um der Liste die neue Rolle hinzuzufügen.
    
  
- Zum Abrufen der ID der Rollendefinition, die die neuen Berechtigungen für die Gruppe definiert. Die ID wird verwendet, um der Liste die neue Rolle hinzuzufügen. In diesem Beispiel wird die vorhandene Rollendefinition für die neue Rolle verwendet, Sie können optional jedoch auch eine neue Rollendefinition erstellen.
    
  
- Zum Abbrechen der Rollenvererbung in der Liste mithilfe der  `BreakRoleInheritance`-Methode. In dem Beispiel wird die Rollenvererbung abgebrochen, die aktuelle Rollengruppe wird jedoch beibehalten. (Alternativ können Sie auswählen, die Rollenzuweisungen nicht zu kopieren und den aktuellen Benutzer zu der Verwaltungsberechtigungsebene hinzuzufügen).
    
  
- Entfernen Sie die aktuelle Rollenzuweisung der Gruppe in der Liste, indem Sie eine DELETE-Anforderung an den Rollenzuweisungsendpunkt senden. (Wenn Sie vorsehen, keine Rollenzuweisungen zu kopieren, können Sie diesen Schritt überspringen).
    
  
- Fügen Sie eine Rollenzuweisung für die Gruppe in der Liste hinzu, indem Sie die  `AddRoleAssignment`-Methode verwenden, die die Gruppe an die Rollendefinition bindet und die Rolle zur Liste hinzufügt.
    
  

## Voraussetzungen zum Verwenden des Beispiels in diesem Artikel
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Um das Beispiel in diesem Artikel zu verwenden, benötigen Sie Folgendes:
  
    
    

- Eine SharePoint 2013-Entwicklungsumgebung (für lokale Szenarios ist die App-Isolation erforderlich)
    
  
- Visual Studio 2012 oder Visual Studio 2013 mit Office Developer Tools für Visual Studio 2012 oder
  
    
    
Napa (nur SharePoint Online)
    
  
Sie müssen außerdem **Full Control**-Add-In-Berechtigungen im **Web**-Bereich festlegen. Dieses Add-In können nur Benutzer ausführen, die über ausreichend Berechtigungen zum Ändern von Listenberechtigungen (z. B. Websitebesitzer) verfügen.
  
    
    

## Beispiel: Festlegen von benutzerdefinierten Berechtigungen in einer Liste mithilfe der REST-Schnittstelle
<a name="bk_example1"> </a>

In den folgenden Beispielen wird der Inhalt der App.js-Datei eines in SharePoint gehosteten Add-Ins dargestellt. Im ersten Beispiel wird die JavaScript domänenübergreifende Bibliothek zum Erstellen und Senden von HTTP-Anforderungen verwendet. Im zweiten Beispiel werden jQuery AJAX-Anforderungen verwendet.
  
    
    
Bevor Sie den Code ausführen, müssen Sie die Platzhalterwerte durch tatsächliche Werte ersetzen. Wenn Sie eine andere Sprache oder Umgebung verwenden, müssen Sie einige Anforderungskomponenten hinzufügen oder ändern. Weitere Informationen dazu finden Sie unter  [So unterscheiden sich REST-Anforderungen je nach Umgebung](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#bk_HowRequestsDiffer).
  
    
    
 **Beispiel 1: domänenübergreifende Anforderungen**
  
    
    



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

 **Beispiel 2: jQuery AJAX-Anforderungen**
  
    
    



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


## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Einführung in den SharePoint 2013 REST-Dienst](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md)
    
  
- REST-Ressourcen:
    
     [GroupCollection-Ressource](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_GroupCollection)
    
     [Group-Ressource](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_Group)
    
     [RoleAssignmentCollection-Ressource](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignmentCollection)
    
     [RoleAssignment-Ressource](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleAssignment)
    
     [RoleDefinitionCollection-Ressource](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinitionCollection)
    
     [RoleDefinition-Ressource](c5e49290-78d4-4167-b4de-da32376bbf90.md#bk_RoleDefinition)
    
  
- TechNet-Artikel:
    
     [Abgestimmte Berechtigungsreferenz für SharePoint Server 2013](http://technet.microsoft.com/de-de/library/dn169567.aspx)
    
     [Bewährte Methoden zum Verwenden von abgestimmten Berechtigungen in SharePoint Server 2013](http://technet.microsoft.com/de-de/library/gg128955.aspx)
    
     [Benutzerberechtigungen und Berechtigungsstufen in SharePoint 2013](http://technet.microsoft.com/de-de/library/cc721640.aspx)
    
  

