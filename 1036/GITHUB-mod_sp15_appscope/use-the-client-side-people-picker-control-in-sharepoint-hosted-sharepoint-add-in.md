---
title: Utiliser le contrôle Sélecteur de personnes côté client dans des compléments hébergés par SharePoint
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# Utiliser le contrôle Sélecteur de personnes côté client dans des compléments hébergés par SharePoint
Découvrez comment utiliser le contrôle Sélecteur de personnes côté client dans les Compléments SharePoint hébergés par SharePoint.
> **IMPORTANTE**
> Cette rubrique présuppose que vous savez créer un Complément SharePoint hébergé par SharePoint. Pour apprendre à en créer un, commencez à la rubrique  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md). 





## Qu'est-ce que le contrôle Sélecteur de personnes côté client dans SharePoint 2013 ?
<a name="bk_whatIs"> </a>

Ce contrôle permet aux utilisateurs de rapidement rechercher et sélectionner des comptes d'utilisateurs valides afin de trouver des personnes, des groupes et des revendications dans leur organisation. Le sélecteur est un contrôle HTML et JavaScript pris en charge par plusieurs navigateurs. L'ajout du sélecteur à votre complément est simple : dans votre balisage, ajoutez un élément conteneur pour le contrôle et des références pour le contrôle et ses dépendances. Ensuite dans votre script, appelez la fonction globale **SPClientPeoplePicker_InitStandaloneControlWrapper** pour afficher et initialiser le sélecteur.



Le sélecteur est représenté par l'objet **SPClientPeoplePicker**, lequel fournit des méthodes que les autres contrôles côté client peuvent utiliser pour obtenir des informations à partir du sélecteur ou pour effectuer d'autres opérations. Vous pouvez utiliser les propriétés **SPClientPeoplePicker** pour configurer le sélecteur avec des paramètres propres au contrôle, comme l'autorisation de plusieurs utilisateurs ou la spécification d'options de mise en cache. Le sélecteur utilise également des paramètres de configuration d'application web tels que des paramètres ou des forêts ciblées Services de domaine Active Directory. Les paramètres de configuration d'application web sont automatiquement sélectionnés (à partir de la propriété **SPWebApplication.PeoplePickerSettings**).



Le sélecteur comporte les composants suivants :




- une zone de texte pour entrer des noms d'utilisateurs, de groupes et de revendications,


- un contrôle d'étendue qui présente les noms des utilisateurs, groupes et revendications résolus,


- un élément **div** masqué qui remplit automatiquement une zone déroulante à l'aide des résultats de requête correspondants,


- un contrôle de remplissage automatique.



> **REMARQUE**
> Le sélecteur et ses fonctionnalités sont définis dans les fichiers de script **clientforms.js**, **clientpeoplepicker.js** et **autofill.js**, lesquels se trouvent dans le dossier %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\TEMPLATE\\LAYOUTS sur le serveur. 





## Conditions préalables à la configuration de votre environnement de développement afin d'utiliser le contrôle Sélecteur de personnes côté client dans un Complément SharePoint 2013
<a name="bk_prereqs"> </a>

Cet article suppose que vous créez le Complément SharePoint à l'aide des Outils de développement Office 365 « Napa » sur un site du développeur Office 365. Si vous utilisez cet environnement de développement, vous remplissez déjà les conditions préalables.




> **REMARQUE**
> Voir  [Configurer un environnement de développement pour les compléments pour SharePoint dans Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) pour découvrir comment s'inscrire afin d'obtenir un site du développeur et commencer à utiliser les Outils de développement Office 365 « Napa ».




Si vous n'utilisez pas les Outils de développement Office 365 « Napa » sur un site du développeur, vous avez besoin de ce qui suit :




- SharePoint 2013 avec au moins un utilisateur cible,


- Visual Studio 2012 ou Visual Studio 2013


- Outils de développement Office pour Visual Studio 2013



> **REMARQUE**
> Pour obtenir des instructions sur la manière de configurer un environnement de développement adapté à vos besoins, voir  [Commencer à créer des applications pour Office et SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 




La procédure qui suit décrit les principales étapes qui permettent d'ajouter le sélecteur à votre complément, puis d'obtenir ses informations utilisateur à partir d'un autre contrôle côté client. Voir  [Exemple de code : utilisation du sélecteur de personnes côté client](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example) pour obtenir le code correspondant.



Vous pouvez utiliser le contrôle Sélecteur de personnes côté client dans les Compléments SharePoint hébergés sur SharePoint, mais pas dans les compléments hébergés par un fournisseur. Pour obtenir un exemple illustrant l'implémentation d'un contrôle Sélecteur de personnes dans un complément hébergé par un fournisseur, téléchargez les  [exemples de modèles pour complément Office](http://officeams.codeplex.com).




## Utilisation d'un contrôle Sélecteur de personnes côté client dans un complément hébergé par SharePoint
<a name="bk_steps"> </a>


### Dans le balisage de votre page


1. Ajoutez des références aux dépendances de script du contrôle Sélecteur de personnes côté client.


2. Ajoutez les balises HTML de l'interface utilisateur de la page. Dans cet exemple, le complément définit deux éléments **div**: un pour l'affichage du sélecteur et un autre pour l'interface utilisateur (un bouton qui appelle le script d'interrogation du sélecteur et les éléments qui affichent les informations utilisateur).



### Dans votre script


1. Créez un dictionnaire JSON à utiliser en tant que schéma pour stocker les propriétés propres au sélecteur, comme **AllowMultipleValues** et **MaximumEntitySuggestions**.


2. Appelez la fonction globale **SPClientPeoplePicker_InitStandaloneControlWrapper**.


3. Obtenez l'objet sélecteur de la page.


4. Interrogez le sélecteur. Dans cet exemple, le complément appelle la méthode **GetAllUserInfo** pour obtenir toutes les informations utilisateur des utilisateurs résolus et la méthode **GetAllUserKeys** pour obtenir uniquement les clés des utilisateurs résolus.


5. Obtenez l'ID de l'utilisateur en utilisant le modèle objet JavaScript. L'ID de l'utilisateur n'est pas inclus dans les informations qui sont renvoyées par le sélecteur, le complément appelle donc la méthode **SP.Web.ensureUser** et obtient l'ID de l'objet **SP.User** renvoyé.


L'affichage, l'initialisation et d'autres fonctionnalités du sélecteur sont gérés par le serveur, notamment la recherche et la résolution des entrées utilisateur auprès des fournisseurs d'authentification SharePoint.




## Exemple de code : utilisation du sélecteur de personnes côté client dans un complément hébergé par SharePoint
<a name="bk_example"> </a>

Les exemples de code HTML et JavaScript suivants permettent d'ajouter un contrôle Sélecteur de personnes côté client à un complément hébergé par SharePoint.



Le premier exemple illustre le balisage de page lié aux balises **PlaceHolderMain** **asp:Content** dans la page Default.aspx. Ce code référence les dépendances de script du sélecteur, attribue un ID unique à l'élément DOM dans lequel le sélecteur sera affiché, puis définit l'interface utilisateur du complément.





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
</asp:Content>```


> **REMARQUE**
> Selon votre environnement, vous pouvez ne pas avoir à référencer explicitement toutes ces dépendances. 




L'exemple suivant illustre le script du fichier App.js. Ce script permet d'initialiser et d'afficher le sélecteur, de l'interroger afin d'obtenir des informations utilisateur et d'obtenir l'ID de l'utilisateur à l'aide du modèle objet JavaScript.





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
}```


## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Créer des composants d'expérience utilisateur dans SharePoint 2013](create-ux-components-in-sharepoint-2013.md)


-  [Vue d'ensemble du sélecteur de personnes et du fournisseur de revendications (SharePoint 2013)](http://technet.microsoft.com/fr-fr/library/gg602078.aspx)


-  [Configurer le sélecteur de personnes dans SharePoint 2013](http://technet.microsoft.com/fr-fr/library/gg602075.aspx)



