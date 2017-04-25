---
title: Erstellen eines einfachen von SharePoint gehosteten Add-Ins mithilfe von Napa Office 365-Entwicklungstools
ms.prod: SHAREPOINT
ms.assetid: 3b47c97b-9e09-47b2-a65f-29b0f44e34bf
---


# Erstellen eines einfachen von SharePoint gehosteten Add-Ins mithilfe von Napa Office 365-Entwicklungstools
Erfahren Sie, wie Sie ein einfaches von SharePoint gehostetes SharePoint-Add-In mit Napa Office 365-Entwicklungstools erstellen.



![Schaltfläche "Ausführen"](images/Apps_NAPA_Run_Button.png)



 [Führen Sie jetzt dieses Beispiel aus!](http://go.microsoft.com/fwlink/?LinkId=313212)
Napa ist ein Tool, mit dem Sie von SharePoint gehostete SharePoint-Add-Ins erstellen können. Napa selbst wird als eine (vom Anbieter gehostete) SharePoint-Add-In implementiert, die auf SharePoint Online-Websites installiert werden kann, die mit der Vorlage **Entwicklerwebsite** erstellt werden. SharePoint-Entwicklerwebsites verfügen über eine Bibliothek namens **Add-Ins im Test** auf der Startseite. Anweisungen zum Erstellen einer Entwicklerwebsite und Installieren von Napa finden Sie weiter unten in diesem Artikel.





> **HINWEIS**
> Die Installation von Napa wird nicht für lokale SharePoint-Bereitstellungen unterstützt. 





Durch die Verwendung von Napa können Sie Ihr SharePoint-Add-Ins im Browser statt in Visual Studio erstellen. Sie können das Projekt bei komplexeren Szenarios jederzeit herunterladen und in Visual Studio öffnen.




Durch Befolgen der Schritte in diesem Artikel erfahren Sie, wie Sie ein einfaches in SharePoint gehostetes SharePoint-Add-In mithilfe von Napa erstellen. Das von Ihnen erstellte Add-In enhält Steuerelemente und Code für das Verwalten von Listen und Listenelementen.
> **HINWEIS**
> Sie können mit Napa nur von SharePoint gehostete, keine von Anbietern gehostete SharePoint-Add-Ins erstellen. Informationen zu den Unterschieden finden Sie unter  [SharePoint-Add-Ins](sharepoint-add-ins.md). > Sie können in Napa nicht die Add-In-Aktualisierungssemantik von SharePoint verwenden, die unter  [Aktualisieren von Add-In-Webkomponenten in SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md) beschrieben ist. Wenn Sie also eine in Napa erstellte App aktualisieren müssen, müssen Sie diese zuerst in Visual Studio importieren. Anweisungen dazu finden Sie später in diesem Artikel.> Sie können ein SharePoint-Add-In auch mit Visual Studio erstellen. Weitere Informationen finden Sie unter  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md). 





## Optionales Erwerben einer Website für Office 365-Entwickler
<a name="Prerequisites"> </a>

Wenn Sie noch kein SharePoint Online-Abonnement haben, das Sie für die Entwickung verwenden können, erwerben Sie eins mithilfe der Informationen in diesem Abschnitt. Fahren Sie andernfalls mit  [Installieren von Napa](#Overview) fort.




> **HINWEIS**
>  Möglicherweise haben Sie bereits Zugriff auf eine Website für Office 365-Entwickler.> **Sind Sie MSDN-Abonnent?** Visual Studio Ultimate und Visual Studio Premium mit MSDN-Abonnenten erhalten als Bonus ein einjähriges Office 365 Developer-Abonnement. [Lösen Sie Ihren Bonus heute ein.](https://login.live.com/login.srf?wa=wsignin1.0&amp;rpsnv=12&amp;ct=1402926170&amp;rver=6.0.5276.0&amp;wp=MCMBI&amp;wlcxt=msdn%24msdn%24msdn&amp;wreply=https%3a%2f%2fmsdn.microsoft.com%2fsubscriptions%2fmanage%2fdefault.aspx&amp;lc=1033&amp;id=254354&amp;mkt=de-DE)> **Besitzen Sie einen der folgenden Office 365 Abonnementpläne?**> **Falls ja, kann ein Administrator des Office 365-Abonnements eine Website für Entwickler** über das [Office 365 Admin Center](https://portal.microsoftonline.com/admin/default.aspx) erstellen. Weitere Informationen finden Sie unter [Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement](create-a-developer-site-on-an-existing-office-365-subscription.md). 




Es gibt zwei Wege zu einem Office 365-Plan.




- Beginnen Sie mit einer  [kostenlosen 30-Tage-Testversion](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK) mit einer Benutzerlizenz.


- Erwerben Sie ein  [Office 365 Developer-Abonnement](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK).



> **TIPP**
> Jeder dieser Links wird in einem anderen Fenster oder einer anderen Registerkarte geöffnet, damit die nachfolgenden Anweisungen übersichtlich bleiben. 





**Abb. 1. Domänenname der Office 365-Entwicklerwebsite**








![Seite 2 des Registrierungsformulars für das Office 365-Konto](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)












1. Die erste Seite (nicht abgebildet) des Registrierungsformulars ist selbsterklärend. Geben Sie einfach die erforderlichen Informationen zu Ihrer Person an, und wählen Sie **Weiter** aus.


2. Geben Sie auf der zweiten Seite, die in Abbildung 1 gezeigt ist, eine Benutzer-ID für den Administrator des Abonnements an.


3. Erstellen Sie eine Unterdomäne von **. onmicrosoft.com**.

    Nach der Registrierung müssen Sie die resultierenden Anmeldeinformationen (im Format  _UserID_@ _IhreDomäne_.onmicrosoft.com) benutzen, um sich auf Ihrer Office 365-Portalwebsite anzumelden, auf der Sie Ihr Konto verwalten. Ihre SharePoint Online-Entwicklerwebsite wird unter Ihrer neuen Domäne **http:// _IhreDomäne_.sharepoint.com** bereitgestellt.


4. Wählen Sie **Weiter** aus, und füllen Sie die letzte Seite des Formulars aus. Wenn Sie eine Telefonnummer bereitstellen, um Ihren Bestätigungscode zu erhalten, können Sie eine Mobil- oder Festnetznummer, aber *keine*  VoIP-Nummer (Voice over Internet Protocol) bereitstellen.




> **HINWEIS**
> Wenn Sie beim Versuch, sich bei einem Entwicklerkonto anzumelden, bei einem anderen Microsoft-Konto angemeldet sind, wird möglicherweise die folgende Nachricht angezeigt: „Die eingegebene Benutzer-ID hat leider nicht funktioniert. Sie ist anscheinend nicht gültig. Geben Sie die Benutzer-ID ein, die Ihnen von Ihrem Unternehmen zugewiesen wurde. Ihre Benutzer-ID hat in der Regel das Format  *someone@example.com*  oder *someone@example.onmicrosoft.com*  ."> Wenn diese Nachricht angezeigt wird, melden Sie sich vom Microsoft-Konto ab, und versuchen Sie es erneut. Sollte die Nachricht weiterhin angezeigt werden, löschen Sie den Browsercache, oder wechseln Sie zu **InPrivate-Browsen** und füllen Sie das Formular aus.




Nachdem Sie die Registrierung abgeschlossen haben, wird in Ihrem Browser die Office 365-Installationsseite geöffnet. Wählen Sie das Admin-Symbol aus, um die Admin Center-Seite zu öffnen.




**Abb. 2. Office 365 Admin Center-Seite**








![Screenshot mit dem Office 365 Admin Center](images/SP15_Office365AdminInset_border.png)








1. Warten Sie, bis der Bereitstellungsprozess für Ihre Website für Entwickler abgeschlossen ist. Nach Abschluss der Bereitstellung aktualisieren Sie die Admin Center-Seite im Browser.


2. Wählen Sie dann links oben den Link **Add-Ins erstellen** aus, um Ihre Website für Entwickler zu öffnen. Sie sollten eine Website wie in Abbildung 3 dargestellt sehen. Auf der Seite wird die Liste **Add-Ins im Test** angezeigt. Damit wird bestätigt, dass die Website mit der Entwicklerwebsitevorlage von SharePoint erstellt wurde. Wird stattdessen eine Teamwebsite angezeigt, warten Sie einige Minuten und starten dann Ihre Website erneut.


3. Notieren Sie die URL der Website. Diese wird verwendet, wenn Sie SharePoint-Add-Ins-Projekte in Visual Studio erstellen.



**Abb. 3: Die Startseite Ihrer Entwicklerwebsite mit der Liste der Add-Ins im Test**








![Screenshot, auf dem die Entwicklerwebsite-Startseite angezeigt ist](images/SP15_DeveloperSiteHome_border.png)












## Installieren von Napa
<a name="Overview"> </a>

Wenn Ihr -Abonnement ursprünglich nicht als eine Website für Office 365-Entwickler erstellt wurde, müssen Sie in der Verwaltungsbenutzeroberfläche des -Abonnements eine Entwicklerwebsite erstellen und dann Napa darin installieren. Anweisungen für das Erstellen der Website finden Sie unter  [Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement](create-a-developer-site-on-an-existing-office-365-subscription.md).



Öffnen Sie zum Installieren von Napa Ihre Entwicklerwebsite, und wählen Sie **Websiteinhalte** > **Add-In hinzufügen** > **SharePoint Store** aus. Suchen Sie im Store nachNapa, und installieren Sie das Add-In. (Wenn Sie eine Website für Office 365-Entwickler haben, wurde Napa möglicherweise bereits bei der Erstellung der Website installiert und wird auf der Seite **Websiteinhalte** angezeigt.)




## Erstellen eines SharePoint-Add-In-Projekts
<a name="Create"> </a>


1. Öffnen Sie das Napa-Add-In auf der Office 365-Seite.


2. Wählen Sie die Kachel **Neues Projekt hinzufügen** und dann die Kachel **Add-In für SharePoint** aus.


3. Nennen Sie das Projekt Test-Add-In für SharePoint, und klicken Sie dann auf die Schaltfläche **Erstellen**.

    Der Code-Editor wird mit der Standardwebsite geöffnet, die bereits etwas Beispielcode enthält, den Sie ohne weiteres Zutun ausführen können.



## Hinzufügen von Steuerelementen zur Homepage
<a name="AddControls1"> </a>

Fügen Sie der standardmäßigen Homepage im SharePoint-Add-In Steuerelemente zum Erstellen und Löschen einer generischen SharePoint-Liste und zum Abrufen der aktuellen Anzahl von Listen im Web des SharePoint-Add-Ins hinzu. Den Code für die Steuerelemente fügen Sie später hinzu.




### So fügen Sie der Homepage Steuerelemente hinzu


1. Wählen Sie links auf der Seite unter dem Ordner **Seiten** die Seite **Default.aspx** aus, falls diese nicht bereits ausgewählt ist.

    Die Webseite "Default.aspx" wird im Code-Editor angezeigt.


2. Fügen Sie im Abschnitt  `PlaceHolderMain` den folgenden Code unter dem vorhandenen HTML-Code hinzu:

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


    Mit dem HTML-Code werden die folgenden Steuerelemente erstellt:

  - Eine Schaltfläche, mit der die Anzahl von Listen im Web des SharePoint-Add-Ins abgerufen wird.


  - Eine Schaltfläche zum Erstellen einer generischen SharePoint-Liste und eine weitere Schaltfläche zum Löschen der Liste.


  - Eine Liste mit den Listen, die in dem Add-In verfügbar sind.



## Hinzufügen von Code zum Erstellen und Löschen von Listen
<a name="AddCode1"> </a>

In diesem Verfahren fügen Sie JavaScript-Code hinzu, damit Benutzer Listen im SharePoint-Add-In erstellen und löschen können.




### So fügen Sie Code zum Erstellen und Löschen von Listen hinzu


1. Wählen Sie den Ordner **Skripts** aus, und klicken Sie dann auf den Link **App.js**.

    Die standardmäßige JavaScript-Codedatei der Projektvorlage wird für die Bearbeitung geöffnet. Diese Datei enthält den Code, der in Ihrem SharePoint-Add-In verwendet wird. Sie können auch eine andere JS-Datei hinzufügen, in die Sie Code (anstatt in die vorhandene Datei) einfügen. Fügen Sie den Code in diesem Beispiel jedoch in die bereitgestellte Datei **App.js** ein.

    Im nächsten Schritt definieren Sie die Funktionen für die Steuerelemente, die Sie im vorherigen Verfahren erstellt haben.


|**Funktionsname**|**Beschreibung**|
|:-----|:-----|
| `getWebProperties()` <br/> |Verbunden mit dem **getListCount**-Steuerelement - ruft die Anzahl von Listen im Web ab.  <br/> |
| `createlist()` <br/> |Verbunden mit dem **createListButton**-Steuerelement - erstellt eine generische SharePoint-Liste.  <br/> |
| `deletelist()` <br/> |Verbunden mit dem **deletelistbutton**-Steuerelement - löscht die Liste, die Benutzer aus der Liste mit den verfügbaren Listen ausgewählt haben.  <br/> |
 

    Außerdem rufen Sie die Funktionen  `welcome()` und `displayLists()` auf. Dies wird weiter unten in dieser exemplarischen Vorgehensweise beschrieben.


2. Fügen Sie in der Datei **App.js** die Variablen `web`,  `lists` und `listItemcollection` zu den beiden Standardvariablen hinzu, und ändern Sie den Code in der Funktion `$(document).ready()` in das folgende Beispiel.

    > **HINWEIS**
      > Im Code werden gewellte Unterstreichungen angezeigt. Diese werden im Laufe der nächsten Schritte ausgeblendet. 

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


    Im nächsten Schritt fügen Sie JavaScript-Funktionen für die Definitionen hinzu. Jede Funktion im Code wird per Aufruf von  `executeQueryAsync()` ausgeführt. Bei diesem Aufruf wird die aktuelle ausstehende Anforderung asynchron auf dem Server ausgeführt, indem das clientseitige Objektmodell (CSOM) für SharePoint verwendet wird. Wenn eine Funktion asynchron ausgeführt wird, wird Ihr Skript weiter ausgeführt, ohne dass eine Antwort des Servers abgewartet wird. Jeder `executeQueryAsync()`-Aufruf umfasst zwei Ereignishandler. Ein Handler antwortet, wenn die Funktion erfolgreich ausgeführt wird, und der andere Handler antwortet, wenn die Funktion fehlschlägt. In dieser Tabelle sind die Hauptfunktionen beschrieben.


|**Funktionsname**|**Beschreibung**|
|:-----|:-----|
| `welcome()` <br/> |Ruft den aktuellen Webkontextverweis ab und verwendet diesen dann, um die aktuellen Benutzerinformationen in den Kontext einzubinden.  <br/> |
| `getWebProperties()` <br/> |Ruft die Listensammlung im aktuellen Web ab und gibt dann die Anzahl der Listen zurück.  <br/> |
| `displaylists()` <br/> |Ruft die aktuelle Listensammlung in diesem Web ab. Ist dies erfolgreich, wird mit dieser Funktion der Name der einzelnen Listen in der Sammlung der Liste mit den verfügbaren Listen hinzugefügt.  <br/> |
| `createlist()` <br/> |Erstellt eine generische SharePoint-Liste (Listenvorlagentyp **genericList**) und benennt diese mit dem Namen, den Benutzer im **createlistbox**-Steuerelement angeben. Sie können auch andere Arten von Listen erstellen. Weitere Informationen zu den Arten von Listen finden Sie unter  [SPListTemplateType Enumeration](http://go.microsoft.com/fwlink/?LinkId=256687).  <br/> |
| `deletelist()` <br/> |Löscht die Liste, die Benutzer aus der Liste mit den verfügbaren Listen ausgewählt haben.  <br/> |
 
3. Fügen Sie den folgenden Code nach der Funktion  `onGetUserNameFail()` in **App.js** ein.

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


## Führen Sie die App aus!
<a name="Run1"> </a>

Der erste Teil der Benutzeroberfläche und des Codes ist jetzt fertig. Sie sollten das Add-In also ausführen, um zu überprüfen, ob es funktioniert.




### So führen Sie das Add-In aus


1. Wählen Sie unten auf der Seite die Ausführungsschaltfläche (



![Schaltfläche "Ausführen"](images/Apps_NAPA_Run_Button.png)



) aus.

    Das Add-In wird verpackt, bereitgestellt und auf Ihrer Office 365 Developer Site installiert.

    Nach der Installation wird das SharePoint-Add-In gestartet. Falls das Add-In nicht automatisch gestartet wird, weil beispielsweise ein Popupblocker aktiviert ist, wählen Sie den Add-In-Link zum Starten des Add-Ins aus.


2. Wählen Sie den Link zum Starten des Add-Ins in einem neuen Fenster aus.

    Das Fenster für das SharePoint-Add-In wird angezeigt.


3. Klicken Sie auf die Schaltfläche **Get count of lists in web**.

    In einem Dialogfeld wird angegeben, dass das Web für das aktuelle SharePoint-Add-In zwei Listen enthält. (Standardmäßig sind im Web die Listen „Design Gallery" und „Gestaltungsvorlagenkatalog" enthalten.)


4. Geben Sie im Feld **List name here** den TextTestliste ein, und klicken Sie auf die Schaltfläche **Create Liste**.


5. Öffnen Sie die Liste **Lists**, um zu überprüfen, ob die neue Liste darin enthalten ist.


6. Klicken Sie erneut auf die Schaltfläche **Get count of lists in web**.

    Das Web enthält mit der eben erstellten Liste jetzt drei Listen.


7. Wählen Sie in der Liste **Lists** den Eintrag **Test List** aus, und klicken Sie auf die Schaltfläche **Delete Selected List**.

    Der Eintrag **Testliste** wird aus der Liste mit den verfügbaren Listen entfernt.


8. Schließen Sie nach Abschluss Ihrer Änderungen das Browserfenster, und klicken Sie dann im Fenster zum Starten des Add-Ins auf die Schaltfläche **Schließen**, um zum in Bearbeitung befindlichen Projekt zurückzukehren.



## Hinzufügen von Code und Steuerelementen zum Hinzufügen und Löschen von Listenelementen
<a name="AddControls2"> </a>

Da Benutzer jetzt Listen erstellen und löschen können, können Sie die folgenden Schritte ausführen, um Benutzern das Hinzufügen und Löschen von Listenelementen zu ermöglichen.




### So fügen Sie Code und Steuerelemente zum Hinzufügen und Löschen von Listenelementen hinzu


1. Wählen Sie die Datei "Default.aspx" zur Bearbeitung aus.


2. Fügen Sie unter dem  `selectlistbox`-Element den folgenden Code hinzu.

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


    Mit diesem Code wird ein Eingabefeld hinzugefügt, in dem Benutzer den Namen eines Elements, eine Schaltfläche zum Hinzufügen des Elements zur Liste und eine Schaltfläche zum Löschen des Elements aus der Liste angeben können.


3. Wählen Sie die Datei **App.js** zur Bearbeitung aus.


4. Fügen Sie in der  `$(document).ready()`-Funktion Definitionen für Funktionen hinzu, die aufgerufen werden, wenn Benutzer auf die Schaltflächen **Element erstellen** und **Ausgewähltes Element löschen** klicken. Fügen Sie außerdem einen jQuery-Ereignishandler für das Listenfeld **Listen** hinzu, um sicherzustellen, dass die Listenelemente aktualisiert werden, wenn Sie eine neue Liste auswählen.

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


    > **HINWEIS**
      > Werden die Listenelemente nicht angezeigt, wenn Sie das Add-In ausführen, stellen Sie sicher, dass die  `displayLists();`-Anweisung nach dem vorhergehenden Code kommt. 

    Im nächsten Schritt fügen Sie JavaScript-Funktionen für die neuen Definitionen und eine Unterstützungsfunktion ( `getItems()`) hinzu. In der folgenden Tabelle sind die Hauptfunktionen beschrieben.


|**Funktionsname**|**Beschreibung**|
|:-----|:-----|
| `createItem()` <br/> |Fügt der vom Benutzer ausgewählten Liste ein Element hinzu und benennt dieses Element mit dem Namen, den der Benutzer im Feld **Items** angibt. <br/> |
| `deleteItem()` <br/> |Löscht das vom Benutzer ausgewählte Element aus der Liste.  <br/> |
| `getItems()` <br/> |Ruft die Listensammlung (und die untergeordneten Elemente) der Liste ab, die vom Benutzer ausgewählt wurde.  <br/> |
 
5. Fügen Sie diesen Code am Ende der Datei **App.js** nach der Funktion `onDeleteListFail()` hinzu.

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


## Führen Sie das aktualisierte SharePoint-Add-In aus!
<a name="Run2"> </a>

Die gesamte Benutzeroberfläche und der gesamte Code ist jetzt fertig. Sie sollten das Add-In also ausführen, um zu überprüfen, ob sie funktioniert.




### So führen Sie das aktualisierte SharePoint-Add-In aus


1. Wählen Sie unten auf der Seite erneut die Schaltfläche **Ausführen**.


2. Geben Sie im Feld **List name here** den TextNew Test List ein, und klicken Sie auf die Schaltfläche **Create Liste**.

    Die neue Liste wird der Liste **Lists** hinzugefügt.


3. Wählen Sie in der Liste **Lists** die Option **New Test List** aus.


4. Geben Sie im Feld **Item name here** den TextItem 1 ein, und klicken Sie auf die Schaltfläche **Create Item**.

    Das neue Listenelement wird in der Liste **Items** angezeigt.


5. Wiederholen Sie den vorherigen Schritt, um Item 2 undItem 3 hinzuzufügen.


6. Wählen Sie in der Liste mit den Elementen die Option **Item 2** aus, und klicken Sie auf die Schaltfläche **Delete Selected Item**.

    **Item 2** wird aus der Liste mit den Elementen entfernt.


7. Schließen Sie das Browserfenster, wenn Sie fertig sind.



## Exportieren des Projekts in Visual Studio
<a name="NextSteps"> </a>

Öffnen Sie das Projekt in Visual Studio, indem Sie die Schaltfläche **In Visual Studio öffnen** auswählen, wie in Abbildung 3 dargestellt. Napa installiert automatisch die erforderlichen Tools und öffnet Ihr Projekt in Visual Studio.




**Abbildung 3: Schaltfläche "In Visual Studio öffnen"**








![Schaltfläche "In Visual Studio öffnen"](images/Apps_Napa_OpenInVS.png)












## Zusätzliche Ressourcen
<a name="Additional"> </a>


-  [Übersicht über die SharePoint 2013-Entwicklung](http://msdn.microsoft.com/library/f86e2695-4d7a-4fc5-bc23-689de96c4b06%28Office.15%29.aspx)


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)



