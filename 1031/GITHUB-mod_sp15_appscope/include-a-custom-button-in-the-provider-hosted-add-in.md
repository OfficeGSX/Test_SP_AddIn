---
title: Einfügen einer benutzerdefinierten Schaltfläche in das vom Anbieter gehostete Add-In
ms.prod: SHAREPOINT
ms.assetid: 58932389-0100-47ee-9d33-1b4321d3f462
---


# Einfügen einer benutzerdefinierten Schaltfläche in das vom Anbieter gehostete Add-In
Erfahren Sie, wie Sie eine benutzerdefinierte Menübandschaltfläche in ein vom Anbieter gehostetes SharePoint-Add-In einfügen.
Dies ist der dritte einer Reihe von Artikeln über die Grundlagen der Entwicklung von vom Anbieter gehosteten SharePoint-Add-Ins. Machen Sie sich zunächst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den vorherigen Artikeln dieser Reihe vertraut:





-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Erteilen des Aussehens und Verhaltens von SharePoint für Ihr vom Anbieter gehostetes Add-In](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)



> **HINWEIS**
> Wenn Sie diese Reihe zu vom Anbieter gehosteten Add-Ins durchgearbeitet haben, haben Sie eine Visual Studio-Projektmappe, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) herunterladen und die Datei „BeforeRibbonButton.sln" öffnen.




Ein SharePoint-Add-In kann benutzerdefinierte Aktionen enthalten, was der SharePoint-Ausdruck für benutzerdefinierte Menüelemente oder Menübandschaltflächen ist. In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte Schaltfläche erstellen, die eine SharePoint-Liste mit einer Remotedatenbank synchronisiert.
## Erstellen einer benutzerdefinierten Liste auf der Hostwebsite

Die benutzerdefinierte Schaltfläche wird sich auf dem Menüband einer bestimmte Liste befinden, in der die Mitarbeiter eines lokalen Geschäfts aufgezeichnet werden. In einem späteren Artikel dieser Reihe erfahren Sie, wie Sie eine benutzerdefinierte Liste programmgesteuert zu einer Hostwebsite hinzufügen, aber für den Moment werden Sie diese manuell hinzufügen. 




1. Navigieren Sie auf der Startseite des Fabrikam-Geschäfts in Hongkong zu **Websiteinhalte | Add-In hinzufügen | Benutzerdefinierte Liste**. 


2. Geben Sie im Dialogfeld **Benutzerdefinierte Liste**Lokale Mitarbeiter als Name an, und klicken Sie auf **Erstellen**. 


3. Öffnen Sie auf der Seite **Websiteinhalte** die Liste **Lokale Mitarbeiter**.


4. Öffnen Sie die Registerkarte **Liste** auf dem Menüband, und klicken Sie dann auf die Schaltfläche **Listeneinstellungen**.


5. Klicken Sie im Abschnitt **Spalten** der Seite **Listeneinstellungen** auf die Spalte **Titel**. 


6. Ändern Sie im Formular **Spalte bearbeiten** den **Spaltenname** von „Titel" inName, und klicken Sie dann auf **OK**.


7. Klicken Sie auf der Seite **Einstellungen** auf **Spalte erstellen**.


8. Gehen Sie im Formular **Spalte erstellen** folgendermaßen vor:

1. Geben Sie Zu Unternehmens-DB hinzugefügt als **Spaltenname** ein.


2. Legen Sie den Typ auf **Ja/Nein (Kontrollkästchen)** fest.


3. Legen Sie den **Standardwert** auf **Nein** fest.


4. Klicken Sie auf **OK**. Sie gelangen zurück zur Seite **Einstellungen**.


9. Klicken Sie auf **Websiteinhalte**, um die Seite **Websiteinhalte** zu öffnen. Die Kachel für die neue Liste ist vorhanden. Öffnen Sie sie.


10. Klicken Sie auf **Neues Element**, und geben Sie auf dem Formular zum Erstellen eines Element einen Namen ein, aber aktivieren Sie  *nicht* **Zu Unternehmens-DB hinzugefügt**. Klicken Sie dann auf **Speichern**. Die Liste sollte etwa wie folgt aussehen:

!\[Die Liste der lokalen Mitarbeiter mit einem einzelnen Element. Der Name ist Brayden Sawtell. Der Wert der Spalte "Zu Unternehmens-DB hinzugefügt" ist "Nein".](images/a3371859-e42f-49ea-8f17-48d8a248b075.PNG)






## Hinzufügen der benutzerdefinierten Schaltfläche

In diesem Abschnitt fügen Sie ein Markup in das Add-In ein, das eine Schaltfläche auf dem Menüband der Liste bereitstellt. Wenn ein Benutzer einen Mitarbeiter in der Liste markiert und auf die Schaltfläche klickt, wird der Name des Mitarbeiters zur Unternehmensdatenbank hinzugefügt das Feld **Zu Unternehmens-DB hinzugefügt** wechselt von „Nein" auf „Ja".




1.  *Wenn Visual Studio geöffnet ist, müssen Sie es schließen*  und die Projektmappe ChainStore erneut öffen, damit Visual Studio Ihre neue Liste erkennen kann. (Führen Sie Visual Studio als Administrator aus.)

    > **HINWEIS**
      >  Die Einstellungen für Startprojekte in Visual Studio werden normalerweise auf die Standardwerte zurückgesetzt, wann immer die Projektmappe erneut geöffnet wird. Führen Sie die folgenden Schritte immer unmittelbar nach dem erneuten Öffnen der Beispielprojektmappe in dieser Artikelreihe durch:>  Klicken Sie mit der rechten Maustaste oben im **Projektmappen-Explorer** auf den Projektmappenknoten, und wählen Sie **Startprojekte festlegen** aus.>  Stellen Sie sicher, dass alle drei Projekte in der Spalte **Aktion** auf **Start** festgelegt sind.
2. Klicken Sie mit der rechten Maustaste auf das Projekt **ChainStore** im **Projektmappen-Explorer**, und wählen Sie **Hinzufügen | Neues Element** aus.


3. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Benutzerdefinierte Menübandaktion** aus, nennen Sie sieAddEmployeeToCorpDB, und klicken Sie dann auf **Hinzufügen**.


4. Im daraufhin geöffneten Dialogfeld werden drei Fragen gestellt. Geben Sie die folgenden Antworten:


|**Frage**|**Geben Sie die folgende Antwort:**|
|:-----|:-----|
|**Wo soll die benutzerdefinierte Aktion zur Verfügung gestellt werden?** <br/> |Hostweb <br/> |
|**Zu welchem Bereich ist die benutzerdefinierte Aktion zugeordnet?** <br/> |Listeninstanz <br/> |
|**Welchem bestimmten Element ist die benutzerdefinierte Aktion zugeordnet?** <br/> |Lokale Mitarbeiter <br/> |
 
5. Klicken Sie auf **Weiter**, und Sie erhalten drei weitere Fragen:


|**Frage**|**Geben Sie die folgende Antwort:**|
|:-----|:-----|
|**Wo befindet sich das Steuerelement?** <br/> |Ribbon.ListItem.Actions <br/> |
|**Was ist der Beschriftungstext für das Steuerelement?** <br/> |Zu Unternehmens-DB hinzufügen <br/> |
|**Wohin wird mit dem Steuerelement navigiert?** <br/> |ChainStoreWeb\\Pages\\EmployeeAdder.aspx (Dies ist eine Seite, deren zugrunde liegender Code den Mitarbeiter zur Datenbank hinzufügt.) <br/> |
 
6. Klicken Sie auf **Fertig stellen**.

    Eine elements.xml-Datei, die die benutzerdefinierte Aktion definiert, wird dem Projekt hinzugefügt und geöffnet. Größtenteils können Sie diese Datei als schwarzes Feld behandeln und müssen Sie bis zu einem späteren Artikel dieser Reihe keine Änderungen darin vornehmen. Jetzt beachten Sie nur Folgendes:

  - Das Attribut **Location** des Elements **CommandUIDefinition** hat den Wert `Ribbon.ListItem.Actions.Controls_children`. Der zweite Teil,  `ListItem`, identifiziert die Registerkarte des Menübands, in der die Schaltfläche abgelegt wird (ist aber möglicherweise nicht der genaue Namen der Registerkarte), und der dritte Teil,  `Actions`, ist der Name des Abschnitts des Menübands, in den die Schaltfläche eingefügt wird.


  - Das Attribut **CommandAction** des Elements **CommandUIHandler** beginnt mit dem Platzhalter `~remoteAppUrl`. Dieser wird bei der Bereitstellung der Schaltfläche ersetzt durch die URL der Remotewebanwendung.


  - Einige Abfrageparameter wurden zum Wert **CommandAction** mit Platzhalterwerten in geschweiften Klammern „{}" hinzugefügt. Diese Platzhalter werden zur Laufzeit aufgelöst. Beachten Sie, dass einer davon die ID des Listenelements ist, das vom Benutzer ausgewählt wird, bevor er auf die benutzerdefinierte Schaltfläche im Menüband klickt.


7. Öffnen Sie im **ChainStoreWeb**-Projekt die Datei **Pages/EmployeeAdder.aspx**. Beachten Sie, dass keine Benutzeroberfläche vorhanden ist. Das Add-In wird diese Seite als eine Art Webdienst verwenden. Dies ist möglich, da die ASP.NET- **System.Web.UI.Page**-Klasse **System.Web.IHttpHandler** implementiert und das ** Page_Load**-Ereignis automatisch ausgeführt wird, wenn die Seite angefordert wird.


8. Öffnen Sie die CodeBehind-Datei **Pages/EmployeeAdder.aspx.cs**. Die Methode, die den Mitarbeiter zur Remotedatenbank hinzufügt,  `AddLocalEmployeeToCorpDB`, ist bereits vorhanden. Sie verwendet das Objekt **SharePointContext**, um die URL des Hostwebs abzurufen, das das Add-In als Mandantendiskriminator verwendet. Die Methode **Page_Load** muss also zuerst dieses Objekt initialisieren. Das Objekt wird erstellt und in der Sitzung zwischengespeichert, wenn die Startseite des Add-Ins geladen wird. Fügen Sie also den folgenden Code zur Methode **Page_Load** hinzu. (Das Objekt **SharePointContext** ist in der SharePointContext.cs-Datei definiert, die von den Office-Entwicklertools für Visual Studio generiert wird, wenn die Add-In-Projektmappe erstellt wird.)

  ```cs

spContext = Session["SPContext"] as SharePointContext;
  ```

9. Da die Methode  `AddLocalEmployeeToCorpDB` den Namen des Mitarbeiters als Parameter akzeptiert, fügen Sie die folgende Zeile zur Methode **Page_Load** hinzu. Sie erstellen die Methode `GetLocalEmployeeName` in einem späteren Schritt.

  ```cs
  // Read from SharePoint
string employeeName = GetLocalEmployeeName();
  ```

10. Fügen Sie unterhalb dieser Zeile den Anruf an die Methode  `AddLocalEmployeeToCorpDB` hinzu.

  ```cs

// Write to remote database
AddLocalEmployeeToCorpDB(employeeName);
  ```

11. Fügen Sie eine **using**-Anweisung zur Datei für den Namespace  `Microsoft.SharePoint.Client` hinzu. (Die Office-Entwicklertools für Visual Studio enthielten die Microsoft.SharePoint.Client-Assembly im **ChainStoreWeb**-Projekt, als dieses erstellt wurde.)


12. Fügen Sie jetzt die folgende Methode zur Klasse  `EmployeeAdder` hinzu. Das SharePoint-.NET-CSOM (clientseitiges Objektmodell) ist im Detail an anderer Stelle auf MSDN dokumentiert, und wir empfehlen Ihnen, dieses zu erkunen, wenn Sie mit dieser Artikelreihe fertig sind. Beachten Sie für diesen Artikel, dass die Klasse **ListItem** ein Element in einer SharePoint-Liste darstellt und der Wert eines Felds in dem Element mit der „Indexer"-Syntax referenziert werden kann. Beachten Sie außerdem, dass der Code auf das Feld als „Titel" verweist, obwohl Sie den Feldnamen in „Name" geändert haben. Der Grund hierfür ist, dass in Code auf Felder immer mit ihrem *internen*  Namen verwiesen wird, nicht mit ihrem Anzeigenamen. Der interne Name eines Felds wird festgelegt, wenn das Feld erstellt wird, und kann nicht geändert werden. Sie schließen `TODO1` in einem späteren Schritt ab.

  ```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    // TODO1: Initialize the localEmployee object by getting
    // the item from SharePoint.
 
    return localEmployee["Title"].ToString();
}
  ```

13. Unser Code benötigt die ID des Listenelements, bevor er dieses von SharePoint abrufen kann. Fügen Sie die folgende Deklaration zur Klasse  `EmployeeAdder` direkt unterhalb der Deklaration für das Objekt `spContext` hinzu.

  ```cs

private int listItemID;
  ```

14. Fügen Sie jetzt die folgende Methode zur Klasse  `EmployeeAdder` hinzu, um die Listenelement-ID aus dem Abfrageparameter abzurufen.

  ```cs
  private int GetListItemIDFromQueryParameter()
{
    int result;
    Int32.TryParse(Request.QueryString["SPListItemId"], out result);
    return result;
}
  ```

15. Fügen Sie zum Initialisieren der Variable  `listItemID` die folgende Zeile zur Methode **Page_Load** direkt unterhalb der Zeile hinzu, die die Variable `spContext` initialisiert.

  ```cs

listItemID = GetListItemIDFromQueryParameter();
  ```

16. Ersetzen Sie in  `GetLocalEmployeeName` `TODO1` durch den folgenden Code. Behandeln sie den Code für den Moment einfach als schwarzes Feld, während wir uns darauf konzentrieren, dass die benutzerdefinierte Schaltfläche funktioniert. Sie erfahren mehr über diesen Code im nächsten Artikel dieser Reihe, in dem es um das clientseitige SharePoint-Objektmodell geht.

  ```cs
  using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
    localEmployee = localEmployeesList.GetItemById(listItemID);
    clientContext.Load(localEmployee);
    clientContext.ExecuteQuery();
}

  ```


    Die gesamte Methode sollte jetzt wie folgt aussehen.



  ```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
  ```

17. Da die Seite EmployeeAdder nicht tatsächlich gerendert werden sollte, fügen Sie Folgendes als letzte Zeile in der Methode **Page_Load** hinzu. Damit wird der Browser zurück zur Listenansichtseite für die Liste **Lokale Mitarbeiter** gebracht.

  ```cs

// Go back to the Local Employees page
Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);

  ```


    Die gesamte **Page_Load**-Methode sollte jetzt wie folgt aussehen.



  ```cs

protected void Page_Load(object sender, EventArgs e)
{
    spContext = Session["SPContext"] as SharePointContext;
    listItemID = GetListItemIDFromQueryParameter();

    // Read from SharePoint
    string employeeName = GetLocalEmployeeName();

    // Write to remote database
    AddLocalEmployeeToCorpDB(employeeName);

    // Go back to the preceding page
    Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);
}
  ```


## Anfordern der Berechtigung zum Lesen der Liste

Wie Sie gesehen haben, werden Sie von SharePoint aufgefordert, die Add-In-Berechtigungen für das Hostweb zu gewähren, bei es installiert wird. Sie haben das Add-In jedes Mal erneut installiert, wenn Sie F5 gedrückt haben. Bisher hat das Add-In nur minimale Berechtigungen benötigt, aber die Methode  `GetLocalEmployeeName` erfordert die Berechtigung zum Lesen der Listen auf der Hostwebsite. Das Add-In verwendet sein Add-In-Manifest, um SharePoint anzuweisen, welche Berechtigungen es benötigt. Gehen Sie folgendermaßen vor.




1. Öffnen Sie im **Projektmappen-Explorer** die Datei AppManifest.xml im **ChainStore**-Projekt. (Die Datei heißt AppManifest, da Add-Ins früher als „Apps" bezeichnet wurden.) Der Manifest-Designer wird geöffnet.


2. Öffnen Sie die Registerkarte **Berechtigungen**, und klicken Sie auf die leere Zelle unter der Spalte **Bereich**. Wählen Sie dann **Liste** aus der Dropdownliste aus.


3. Wählen Sie im Feld **Berechtigung** die Option **Lesen** aus der Dropdownliste aus.


4. Lassen Sie die das Feld **Eigenschaften** leer, und speichern Sie die Datei. Die Registerkarte **Berechtigung** sollte etwa wie folgt aussehen:

!\[Die Registerkarte "Berechtigungen" im Add-In-Manifest-Designer zeigt den Bereich "Liste" und die Berechtigung "Lesen".](images/8dd2a25f-103a-42af-aa88-c8ec796315db.PNG)






## Ausführen des Add-Ins und Testen der Schaltfläche






1. Verwenden Sie die F5-TASTE, um Ihr Add-In bereitzustellen und auszuführen. Visual Studio hostet die Remotewebanwendung in IIS Express und die SQL-Datenbank in SQL Express. Außerdem wird eine temporäre Installation des Add-Ins auf Ihrer SharePoint-Testwebsite durchgeführt, und das Add-In wird sofort ausgeführt. Sie werden aufgefordert, Berechtigungen für das Add-In zu erteilen, bevor die Startseite geöffnet wird. Diesmal umfasst die Aufforderung eine Dropdownliste, aus der Sie die Liste auswählen, die das Add-In lesen muss, wie im folgenden Screenshot dargestellt. 

!\[Die SharePoint-Add-In-Berechtigungsaufforderung; die Liste "Lokale Mitarbeiter" ist in der Dropdownliste "Darf Elemente in der Liste lesen" ausgewählt.](images/84e8b42c-4800-4947-acbd-21c6f096f4ea.PNG)





2. Wählen Sie **Lokale Mitarbeiter** aus der Liste aus, und klicken Sie dann auf **Vertrauen**.


3. Wenn die Add-in-Startseite geöffnet wird, klicken Sie auf **Zurück zur Website** im Chromesteuerelement im oberen Bereich.


4. Navigieren Sie auf der Startseite der Website zu **Websiteinhalte | Lokale Mitarbeiter**. Die Seite mit der Listenansicht wird geöffnet.


5. Fügen Sie einige Mitarbeiter zur Liste hinzu.  *Aktivieren Sie nicht das Kontrollkästchen **Zu Unternehmens-DB hinzugefügt**.* 


6. Öffnen Sie auf dem Menüband die Registerkarte **Elemente**. Im Abschnitt **Aktionen** der Registerkarte wird die benutzerdefinierte Schaltfläche **Zu Unternehmens-DB hinzufügen** angezeigt.


7. Wählen Sie ein Element in der Liste aus. Die Seite und das Menüband sollten etwa wie folgt aussehen:

!\[Die Lister der lokalen Mitarbeiter. Ein Element ist hervorgehoben. Darüber befindet sich das Menüband, und der Abschnitt "Aktionen" enthält eine Schaltfläche mit dem Namen "Zu Unternehmens-DB hinzufügen".](images/797a5ceb-7291-4b62-8075-2bb6a1b8e8a1.PNG)





8. Klicken Sie auf die Schaltfläche **Zu Unternehmens-DB hinzufügen**.  * **Sie müssen zuerst ein Element auswähl!*** 


9. Da Seite scheint neu geladen zu werden, da die Methode **Page_Load** der Seiteb EmployeeAdder zur Seite umleitet.


10. Klicken Sie im Browser zweimal auf die Schaltfläche „Zurück", um zur Add-In-Startseite zurückzukehren. 


11. Klicken Sie auf **Mitarbeiter anzeigen**, und die Liste der Mitarbeiter wird mit dem Mitarbeiter, den Sie hinzugefügt haben, aufgefüllt. Sie sollte etwa wie folgt aussehen:

!\[Die Liste der Unternehmensmitarbeiter auf der Startseite des Add-Ins mit Anzeige des Mitarbeiters, der in einem früheren Schritt ausgewählt wurde](images/4a300a4e-f479-4f63-b536-6315c5d9ba4d.PNG)





12. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.


13. Da Sie mit diesem Add-In und dieser Visual Studio-Projektmappe in anderen Artikeln arbeiten werden, hat es sich bewährt, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.



## 
<a name="Nextsteps"> </a>

 Im nächsten Artikel unterbrechen wir die Programmierung kurz und stellen eine Übersicht über das clientseitige SharePoint-Objektmodell bereit: [Schnelle Übersicht über das SharePoint-Objektmodell](get-a-quick-overview-of-the-sharepoint-object-model.md).




