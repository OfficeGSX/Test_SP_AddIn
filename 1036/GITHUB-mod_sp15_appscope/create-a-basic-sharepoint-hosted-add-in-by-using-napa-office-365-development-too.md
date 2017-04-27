---
title: Créer un complément de base hébergé par SharePoint à l'aide des outils de développement Office 365 « Napa »
ms.prod: SHAREPOINT
ms.assetid: 3b47c97b-9e09-47b2-a65f-29b0f44e34bf
---


# Créer un complément de base hébergé par SharePoint à l'aide des outils de développement Office 365 « Napa »
Découvrez comment créer un Complément SharePoint de base hébergé par SharePoint à l'aide des Outils de développement Office 365 Napa.



![Bouton Exécuter](images/Apps_NAPA_Run_Button.png) [Exécuter cet exemple de code maintenant](http://go.microsoft.com/fwlink/?LinkId=313212)
Les outils Outils de développement Office 365 « Napa » peuvent être utilisés pour créer des Compléments SharePoint hébergés par SharePoint. Ils sont eux-mêmes configurés en tant que Complément SharePoint (hébergé par un fournisseur) qui peut être installé sur des sites web SharePoint Online créés avec le modèle **Site du développeur**. Les sites du développeur SharePoint disposent d'une bibliothèque appelée **Compléments en cours de test** sur la page d'accueil. Vous trouverez des instructions pour créer un Site du développeur et installer les outils Napa plus loin dans cet article.





> **REMARQUE**
> Nous ne prenons pas en charge l'installation de Napa sur SharePoint sur site. 





Grâce aux Outils de développement Office 365 « Napa », vous pouvez créer vos Compléments SharePoint au sein de votre navigateur plutôt que dans Visual Studio. À tout moment, il est possible de télécharger votre projet et de l'ouvrir dans Visual Studio pour des scénarios plus élaborés.




En suivant cet article, vous pouvez apprendre à créer un Complément SharePoint simple hébergé par SharePoint en utilisant les outils Outils de développement Office 365 « Napa ». Le complément que vous créerez inclut des commandes et le code de gestion des listes et des éléments de liste.
> **REMARQUE**
> Vous ne pouvez créer que des Compléments SharePoint hébergés par SharePoint avec les outils Napa. Pour plus d'informations sur les différences, voir  [Compléments](sharepoint-add-ins.md). <BR /><BR /> Vous ne pouvez pas utiliser la syntaxe de mise à jour de complément SharePoint qui est décrite à la rubrique  [Mettre à jour des composants de compléments web dans SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md) dans Napa. Par conséquent, si vous devez mettre à jour un complément créé dans les outils Napa, vous devez d'abord l'exporter dans Visual Studio. Vous trouverez la procédure à suivre pour cette opération plus loin dans cet article.<BR /><BR /> Vous pouvez également créer un Complément SharePoint à l'aide de Visual Studio. Pour plus d'informations, voir  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md). 





## Éventuellement, obtenir un Site du développeur Office 365
<a name="Prerequisites"> </a>

Si vous ne disposez pas déjà d'un abonnement SharePoint Online que vous pouvez utiliser pour le développement, utilisez cette section pour en obtenir un. Sinon, passez à  [Installation des outils Napa](#Overview).




> **REMARQUE**
>  Vous avez peut-être déjà accès à un Site du développeur Office 365 :<BR /><BR /> **Êtes-vous abonné à MSDN ?** Les abonnés Visual Studio Ultimate et Visual Studio Premium reçoivent également un abonnement à Office 365 Développeur. [Obtenez cet avantage aujourd'hui.](https://msdn.microsoft.com/subscriptions/manage/default.aspx)<BR /><BR /> **Disposez-vous de l'un des plans d'abonnement Office 365 suivants ?**<BR /> **Si oui, un administrateur de l'abonnement à Office 365 peut créer un Site du développeur** à l'aide du [centre d'administration Office 365](https://portal.microsoftonline.com/admin/default.aspx). Pour plus d'informations, voir  [Créer un Site du développeur dans un abonnement Office 365 existant](create-a-developer-site-on-an-existing-office-365-subscription.md). 




Deux options pour obtenir un plan Office 365 :




- Commencer avec un  [essai gratuit de 30 jours](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK) avec une licence utilisateur.


- Acheter un  [abonnement Office 365 Développeur](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK).



> **CONSEIL**
> Chacun de ces liens s'ouvre dans une autre fenêtre ou un nouvel onglet pour que vous puissiez toujours voir les instructions. 





**Figure 1. Nom de domaine d'un Site du développeur Office 365**








![Page 2 du formulaire d'inscription pour le compte Office 365](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)












1. La première page (non illustrée) du formulaire d'inscription est explicite. Indiquez simplement les informations demandées vous concernant et choisissez **Suivant**.


2. Sur la deuxième page (Figure 1), spécifiez l'identificateur d'utilisateur de l'administrateur de l'abonnement.


3. Créez un sous-domaine de **.onmicrosoft.com**.

    Après l'inscription, vous devez utiliser les informations d'identification obtenues (au format  _UserID_@ _votre_domaine_.onmicrosoft.com) pour vous connecter à votre portail Office 365, où vous pourrez administrer votre compte. Votre Site du développeur SharePoint Online est configuré sur votre nouveau domaine : **http:// _votre_domaine_.sharepoint.com**.


4. Choisissez **Suivant** et renseignez la page finale du formulaire. Si vous choisissez d'indiquer un numéro de téléphone pour obtenir un code de confirmation, vous pouvez indiquer un numéro de téléphone mobile ou fixe, mais *pas*  un numéro VoIP.




> **REMARQUE**
> Si vous êtes connecté à un autre compte Microsoft lorsque vous tentez de vous connecter à un compte de développeur, il est possible qu'un message semblable à celui-ci s'affiche : « Désolé, l'identifiant utilisateur que vous avez saisi ne fonctionne pas. Il semble ne pas être valide. Veillez à saisir l'identifiant utilisateur que votre organisation vous a affecté. Votre identifiant utilisateur ressemble généralement à  *nom@exemple.com*  ou *nom@exemple.onmicrosoft.com*  . »> Si ce message s'affiche, déconnectez-vous du compte Microsoft que vous utilisiez et réessayez. Si vous obtenez toujours ce message, effacez le cache de votre navigateur ou passez en mode **Navigation InPrivate**, puis remplissez le formulaire. 




Une fois que vous avez terminé le processus d'inscription, votre navigateur ouvre la page d'installation d'Office 365. Choisissez l'icône Administrateur pour ouvrir la page Centre d'administration.




**Figure 2. Page Centre d'administration Office 365**








![Capture d'écran illustrant le Centre d'administration Office 365.](images/SP15_Office365AdminInset_border.png)








1. Vous devrez attendre la fin de la configuration de votre Site du développeur. Une fois la configuration terminée, actualisez la page Centre d'administration dans votre navigateur.


2. Ensuite, cliquez sur le lien **Créer des compléments** dans le coin supérieur gauche de la page pour ouvrir votre Site du développeur. Le site ressemble à celui présenté dans la figure 3. Sur la page figure une liste **Compléments en cours de test**. Cela confirme que le site web a été créé avec le modèle Site du développeur de SharePoint. Si, à la place, un site d'équipe standard s'ouvre, patientez quelques minutes et lancez de nouveau votre site.


3. Notez l'URL du site. Elle est utilisée lorsque vous créez des projets de Compléments SharePoint dans Visual Studio.



**Figure 3. Page d'accueil de votre Site du développeur avec la liste Compléments en cours de test**








![Capture d'écran illustrant la page d'accueil du site du développeur.](images/SP15_DeveloperSiteHome_border.png)












## Installation des outils Napa
<a name="Overview"> </a>

Si votre abonnement n'a pas été créé à l'origine en tant que Site du développeur Office 365, vous devez créer un Site du développeur dans l'interface d'administration de l'abonnement , puis installer Napa dans ce site. Vous trouverez des instructions pour la création du site à la rubrique  [Créer un Site du développeur dans un abonnement Office 365 existant](create-a-developer-site-on-an-existing-office-365-subscription.md).



Pour installer les outils Napa, ouvrez votre Site du développeur et choisissez **Contenu du site** > **Ajouter un complément** > **SharePoint Store**. Dans le magasin, effectuez une recherche sur Napa et installez les outils. (Si vous avez un Site du développeur Office 365, les outils Napa ont peut-être déjà été installés lors de la création du site. Si c'est le cas, ils apparaîtront sur la page **Contenu du site**.)




## Créer un projet de Complément SharePoint
<a name="Create"> </a>


1. Ouvrez le complément Outils de développement Office 365 « Napa » dans la page Office 365.


2. Sélectionnez la vignette **Ajouter un nouveau projet**, puis **Complément SharePoint**.


3. Nommez le projet Complément SharePoint test, puis cliquez sur le bouton **Créer**.

    L'éditeur de code s'ouvre et affiche la page web par défaut dans laquelle figure déjà du code fourni à titre d'exemple que vous pouvez exécuter tel quel.



## Ajouter des contrôles à la page d'accueil
<a name="AddControls1"> </a>

Dans le Complément SharePoint, ajoutez des contrôles à la page d'accueil par défaut pour créer et supprimer une liste SharePoint générique et obtenir le nombre actuel de listes sur le site web du Complément SharePoint. Vous ajouterez du code pour les contrôles plus tard.




### Pour ajouter des contrôles à la page d'accueil


1. Dans la partie gauche de la page, sous le dossier **Pages**, choisissez la page **Default.aspx** si elle n'est pas déjà sélectionnée.

    La page web Default.aspx s'affiche dans l'éditeur de code.


2. Dans la section  `PlaceHolderMain`, ajoutez le code suivant sous le code HTML existant.

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


    HTML crée les contrôles suivants :

  - Un bouton chargé de recueillir le nombre de listes sur le site web du Complément SharePoint.


  - Un bouton pour créer une liste SharePoint générique et un autre pour la supprimer.


  - Une liste de listes disponibles dans le complément.



## Ajouter du code pour créer et supprimer des listes
<a name="AddCode1"> </a>

Dans cette procédure, vous allez ajouter du code JavaScript pour permettre aux utilisateurs de créer et de supprimer des listes dans le Complément SharePoint.




### Pour ajouter du code afin de créer et supprimer des listes


1. Choisissez le dossier **Scripts**, puis sélectionnez le lien **App.js**.

    Le fichier de code JavaScript par défaut issu du modèle de projet s'ouvre à des fins de modification. Ce fichier renferme le code qui est utilisé dans votre Complément SharePoint. Vous pouvez éventuellement ajouter un autre fichier .js et ajouter du code à ce fichier plutôt qu'au fichier existant. Néanmoins, pour cet exemple, ajoutez-le au fichier **App.js** qui est fourni.

    Dans l'étape suivante, vous allez définir les fonctions des contrôles que vous avez créés dans la procédure précédente.


|**Nom de la fonction**|**Description**|
|:-----|:-----|
| `getWebProperties()` <br/> |Connectée au contrôle **getListCount**. Elle extrait le nombre de listes sur le site web.  <br/> |
| `createlist()` <br/> |Connectée au contrôle **createListButton**. Elle crée une liste SharePoint générique.  <br/> |
| `deletelist()` <br/> |Connectée au contrôle **deletelistbutton**. Elle supprime la liste que l'utilisateur a choisie dans la liste des listes disponibles.  <br/> |
 

Vous allez également appeler les fonctions  `welcome()` et `displayLists()` décrites plus loin dans cette procédure pas à pas.


2. Dans le fichier **App.js**, ajoutez les variables  `web`,  `lists` et `listItemcollection` aux deux variables par défaut, puis modifiez le code dans la fonction `$(document).ready()` conformément à l'exemple suivant.

    > **REMARQUE**
      > Des tildes illustrant des erreurs apparaîtront dans ce code. Ils disparaîtront dans les étapes ultérieures. 

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


Dans l'étape suivante, vous allez ajouter des fonctions JavaScript pour les définitions. Chaque fonction dans le code est exécutée en appelant  `executeQueryAsync()` qui exécute la demande en attente de manière asynchrone sur le serveur au moyen du modèle objet côté client (CSOM) pour SharePoint. Lorsqu'une fonction est exécutée de manière asynchrone, l'exécution de votre script se poursuit sans attendre une réponse du serveur. Chaque appel de `executeQueryAsync()` comprend deux gestionnaires d'événements. Un gestionnaire répond si la fonction est exécutée avec succès ; l'autre répond en cas d'échec de la fonction. Le tableau qui suit décrit les principales fonctions.


|**Nom de la fonction**|**Description**|
|:-----|:-----|
| `welcome()` <br/> |Obtient la référence de contexte web actuelle, puis l'exploite pour définir les informations utilisateur dans le contexte.  <br/> |
| `getWebProperties()` <br/> |Extrait la collection de listes du site web actuel, puis renvoie le nombre de listes.  <br/> |
| `displaylists()` <br/> |Extrait la collection des listes actuellement disponibles dans ce site web. Si elle y parvient, cette même fonction ajoute le nom de chaque liste de la collection à la liste des listes disponibles.  <br/> |
| `createlist()` <br/> |Crée une liste SharePoint générique (type de modèle de liste **genericList**) et lui attribue le nom que l'utilisateur spécifie dans le contrôle **createlistbox**. Vous pouvez créer d'autres types de liste. Pour plus d'informations sur les types de liste, voir  [Énumération SPListTemplateType](http://go.microsoft.com/fwlink/?LinkId=256687).  <br/> |
| `deletelist()` <br/> |Supprime la liste que l'utilisateur a choisie dans la liste des listes disponibles.  <br/> |
 
3. Ajoutez le code suivant après la fonction  `onGetUserNameFail()` dans le fichier **App.js**.

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


## Exécuter l'application
<a name="Run1"> </a>

La première partie de l'interface utilisateur et du code est en place. Vous pouvez donc poursuivre et exécuter le complément pour vérifier s'il fonctionne.




### Pour exécuter le complément


1. Dans la partie inférieure de la page, choisissez le bouton d'exécution ( ![Bouton Exécuter](images/Apps_NAPA_Run_Button.png) ).

    Le complément est empaqueté, déployé et installé sur votre Site du développeur Office 365.

    Le Complément SharePoint démarre à l'issue de l'installation. S'il ne démarre pas automatiquement (par exemple à cause d'un bloqueur de fenêtres publicitaires), cliquez sur le lien du complément pour le démarrer.


2. Choisissez le lien **Cliquez ici pour lancer votre complément dans une nouvelle fenêtre**.

    L'écran du Complément SharePoint apparaît.


3. Choisissez le bouton qui permet d' **obtenir le nombre de listes sur le site web**.

    Une boîte de dialogue précise que le site web du Complément SharePoint actuel contient deux listes. (Par défaut, le site web est doté des listes Bibliothèque de présentations et Galerie de pages maîtres.)


4. Dans la zone **Nom de la liste**, entrez Liste test, puis choisissez le bouton **Créer une liste**.


5. Ouvrez la liste **Listes** pour vérifier que la nouvelle liste y apparaît.


6. Cliquez de nouveau sur le bouton qui permet d' **obtenir le nombre de listes sur le site web**.

    Le site web contient à présent trois listes, y compris celle que vous venez à peine de créer.


7. Dans la liste **Listes**, choisissez **Liste test**, puis choisissez le bouton **Supprimer la liste sélectionnée**.

    **Liste test** disparaît de la liste des listes disponibles.


8. Une fois terminé, fermez la fenêtre du navigateur, puis choisissez le bouton **Fermer** dans la fenêtre **Lancer le complément** pour revenir au projet que vous étiez en train de modifier.



## Ajouter du code et des contrôles pour créer et supprimer des éléments de liste
<a name="AddControls2"> </a>

Avec la possibilité pour les utilisateurs de créer et de supprimer des listes, vous pouvez exécuter les étapes suivantes pour leur permettre d'ajouter et de supprimer des éléments de liste.




### Pour ajouter du code et des contrôles afin de créer et supprimer des éléments de liste


1. Choisissez le fichier Default.aspx pour le modifier.


2. Sous l'élément  `selectlistbox`, ajoutez le code suivant.

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


Ce code ajoute une zone d'entrée dans laquelle les utilisateurs peuvent spécifier le nom d'un élément, un bouton pour ajouter l'élément à la liste, ainsi qu'un bouton permettant de supprimer l'élément de la liste.


3. Choisissez le fichier **App.js** pour le modifier.


4. Dans la fonction  `$(document).ready()`, ajoutez les définitions des fonctions qui sont appelées lorsque l'utilisateur choisit les boutons **Créer un élément** et **Supprimer l'élément sélectionné**. Ajoutez également un gestionnaire d'événements jQuery pour la zone de liste **Listes** afin de garantir que les éléments de liste sont mis à jour lorsque vous sélectionnez une nouvelle liste.

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


> **REMARQUE**
> Si les éléments de la liste ne s'affichent pas lorsque vous exécutez le complément, assurez-vous que l'instruction  `displayLists();` suit le code précédent.

Dans l'étape suivante, vous ajouterez des fonctions JavaScript pour les nouvelles définitions, ainsi qu'une fonction de prise en charge ( `getItems()`). Le tableau ci-dessous décrit la finalité des principales fonctions.


|**Nom de la fonction**|**Description**|
|:-----|:-----|
| `createItem()` <br/> |Ajoute un élément à la liste choisie par l'utilisateur et lui attribue le nom précisé par ce dernier dans la zone **Éléments**.  <br/> |
| `deleteItem()` <br/> |Supprime l'élément que choisit l'utilisateur dans la liste.  <br/> |
| `getItems()` <br/> |Extrait la collection des éléments (et ses enfants) dans la liste que choisit l'utilisateur.  <br/> |
 
5. Ajoutez ce code au bas du fichier **App.js**, après la fonction  `onDeleteListFail()`.

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


## Exécuter le Complément SharePoint mis à jour
<a name="Run2"> </a>

L'ensemble des éléments de l'interface utilisateur et du code sont en place. Vous pouvez donc poursuivre et exécuter le complément pour vous assurer qu'il fonctionne.




### Pour exécuter la version mise à jour du Complément SharePoint, procédez comme suit :


1. En bas de la page, choisissez de nouveau le bouton **Exécuter**.


2. Dans la zone **Nom de la liste**, entrez Nouvelle liste test, puis choisissez le bouton **Créer une liste**.

    La nouvelle liste est ajoutée à la liste **Listes**.


3. Dans la liste **Listes**, choisissez **Nouvelle liste test**.


4. Dans la zone **Nom de l'élément**, entrez Élément 1, puis choisissez le bouton **Créer un élément**.

    Le nouvel élément de liste apparaît dans la liste **Éléments**.


5. Répétez cette étape pour ajouter un Élément 2 et unÉlément 3.


6. Dans la liste des éléments, choisissez **Élément 2**, puis cliquez sur le bouton **Supprimer l'élément sélectionné**.

    **Élément 2** disparaît de la liste des éléments.


7. Une fois terminé, fermez la fenêtre du navigateur.



## Exporter le projet dans Visual Studio
<a name="NextSteps"> </a>

Ouvrir votre projet dans Visual Studio en cliquant sur le bouton **Ouvrir dans Visual Studio**, comme le montre la Figure 3. Les Outils de développement Office 365 « Napa » installent automatiquement les outils nécessaires et ouvrent votre projet dans Visual Studio.




**Figure 3. Bouton Ouvrir dans Visual Studio**








![Bouton Ouvrir dans Visual Studio](images/Apps_Napa_OpenInVS.png)












## Ressources supplémentaires
<a name="Additional"> </a>


-  [Vue d'ensemble du développement SharePoint 2013](http://msdn.microsoft.com/library/f86e2695-4d7a-4fc5-bc23-689de96c4b06%28Office.15%29.aspx)


-  [Compléments](sharepoint-add-ins.md)



