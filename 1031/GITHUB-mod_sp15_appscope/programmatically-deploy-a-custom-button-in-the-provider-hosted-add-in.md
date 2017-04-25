---
title: Programmgesteuerte Bereitstellung einer benutzerdefinierten Schaltfläche im vom Anbieter gehosteten Add-In
ms.prod: SHAREPOINT
ms.assetid: 4d75d113-8dc8-4026-a297-c47b6d4d7008
---


# Programmgesteuerte Bereitstellung einer benutzerdefinierten Schaltfläche im vom Anbieter gehosteten Add-In
Erfahren Sie, wie Sie programmgesteuert eine benutzerdefinierte Menübandschaltfläche mit einer benutzerdefinierten Liste im selben vom Anbieter gehosteten SharePoint-Add-In registrieren.
Dies ist der neunte einer Reihe von Artikeln über die Grundlagen der Entwicklung von vom Anbieter gehosteten SharePoint-Add-Ins. Machen Sie sich zunächst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den vorherigen Artikeln dieser Reihe vertraut:
  
    
    


-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Erteilen des Aussehens und Verhaltens von SharePoint für Ihr vom Anbieter gehostetes Add-In](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [Einfügen einer benutzerdefinierten Schaltfläche in das vom Anbieter gehostete Add-In](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [Schnelle Übersicht über das SharePoint-Objektmodell](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [Hinzufügen von SharePoint-Schreibvorgängen zum vom Anbieter gehosteten Add-In](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [Einfügen eines Add-In-Webparts in das vom Anbieter gehostete Add-In](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  
-  [Behandeln von Add-In-Ereignissen im vom Anbieter gehosteten Add-In](handle-add-in-events-in-the-provider-hosted-add-in.md)
    
  
-  [Hinzufügen der Logik für die erste Ausführung zum vom Anbieter gehosteten Add-In](add-first-run-logic-to-the-provider-hosted-add-in.md)
    
  

> **HINWEIS**
> Wenn Sie diese Reihe zu vom Anbieter gehosteten Add-Ins durchgearbeitet haben, haben Sie eine Visual Studio-Projektmappe, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) herunterladen und die Datei „BeforeProgrammaticButton.sln" öffnen.
  
    
    

In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte Menübandschaltfläche in ein SharePoint-Add-In einfügen, wenn die Liste, deren Menüband die Schaltfläche erhält, selbst programmgesteuert im selben Add-In bereitgestellt wird.
## Erneutes Hinzufügen der benutzerdefinierten Schaltfläche zum Projekt


> **HINWEIS**
>  Die Einstellungen für Startprojekte in Visual Studio werden normalerweise auf die Standardwerte zurückgesetzt, wann immer die Projektmappe erneut geöffnet wird. Führen Sie die folgenden Schritte immer unmittelbar nach dem erneuten Öffnen der Beispielprojektmappe in dieser Artikelreihe durch:>  Klicken Sie mit der rechten Maustaste oben im **Projektmappen-Explorer** auf den Projektmappenknoten, und wählen Sie **Startprojekte festlegen** aus.>  Stellen Sie sicher, dass alle drei Projekte in der Spalte **Aktion** auf **Start** festgelegt sind.
  
    
    

Im vorherigen Artikel haben Sie die benutzerdefinierte **AddEmployeeToCorpDB**-Menübandschaltfläche aus dem Projekt entfernt. Fügen Sie diese mit der folgenden Schritten unten wieder hinzu.
  
    
    

1. Klicken Sie im **Projektmappen-Explorer** auf die Schaltfläche **Alle Dateien anzeigen** auf der kleinen Symbolleiste am oberen Rand des **Projektmappen-Explorers**.
    
!\[Die Projektmappen-Explorer-Symbolleiste mit einem Feld um die Schaltfläche „Alle Dateien anzeigen'.](images/f6b035f5-1aa7-452a-8f59-9dd44b062d06.PNG)
  

  

  
2. Klicken Sie im Projekt **ChainStore** mit der rechten Maustaste auf **AddEmployeeToCorpDB**, und wählen Sie **Zu Projekt hinzufügen** aus.
    
  
3. Klicken Sie erneut auf die Schaltfläche **Alle Dateien anzeigen**.
    
  
4. Erweitern Sie im Projekt **ChainStore** **AddEmployeeToCorpDB**, und öffnen Sie die Datei „elements.xml". 
    
  

## Verstehen eines Problems und seiner Lösung

In der Datei „elements.xml" identifiziert das Attribut **RegistrationId** des Elements **CustomAction** die Liste, auf deren Menüband die Schaltfläche hinzugefügt wird: `{$ListId:Lists/Local Employees;}`. Das hat einwandfrei funktioniert, als die Liste bereits manuell zum Hostweb hinzugefügt wurde. Aber jetzt, da die Liste programmgesteuert in der zuerst ausgeführten Logik bereitgestellt wird, ist die Liste nicht vorhanden, wenn SharePoint das Add-In installiert und versucht, die Schaltfläche bereitzustellen. Bei der Installation des Add-Ins wird eine Ausnahme ausgelöst, und es treten Fehler auf.
  
    
    
Das Bereitstellen der Liste im Ereignishandler der Installation statt in der zuerst ausgeführten Logik wird das Problem nicht lösen, da SharePoint benutzerdefinierte , deskriptiv definierte Komponenten wie die benutzerdefinierte Schaltfläche (und das Add-In-Webpart **Bestellung aufgeben**) bereitstellt,  *bevor*  der benutzerdefinierte Handler ausgeführt wird, sodass die Liste nicht vorhanden ist, wenn SharePoint versucht, die Schaltfläche bereitzustellen.
  
    
    
Das vollständig programmgesteuerte Erstellen einer benutzerdefinierten Schaltfläche ist aus Gründen nicht praktikabel, die zu fortgeschritten sind, um sie hier zu erläutern. Zum Glück ist das nicht erforderlich. Es gibt eine relativ problemlose Möglichkeit, eine benutzerdefinierte Schaltfläche halbprogrammgesteuert zu erstellen und sie einer benutzerdefinierten Liste zuzuweisen. Im Folgenden werden die grundlegenden Schritte dargestellt:
  
    
    

1. Behalten Sie die deskriptiv definierte Schaltfläche im Projekt bei, aber weisen Sie diese dem Menüband von etwas zu, das immer auf SharePoint-Websites vorhanden ist, statt einer Liste,die programmgesteuert mit demselben Add-In bereitgestellt wird. 
    
  
2. Fügen Sie in der zuerst ausgeführten Logik nach dem programmgesteuerten Erstellen der Liste programmgesteuert eine nicht definierten Schaltfläche zum Menüband der Liste hinzu.
    
  
3. Initialisieren Sie die Eigenschaften der neuen Schaltfläche mit den Werten der ursprünglichen Schaltfläche. An diesem Punkt gibt es zwei identische Schaltflächen. Die zweite wird dem Menüband der Liste **Lokale Mitarbeiter** zugeordnet.
    
  
4. Löschen Sie die ursprüngliche Schaltfläche programmgesteuert.
    
  

## Programmgesteuertes Registrieren der benutzerdefinierten Schaltfläche

Das folgende Verfahren veranschaulicht, wie diese Strategie implementiert wird.
  
    
    

1. Erweitern Sie im **ChainStore**-Projekt **AddEmployeeToCorpDB**, öffnen Sie die Datei „elements.xml", und ändern Sie dann den Wert des Attributs **RegistrationId** des Elements **CustomAction** auf 100. Dies ist die ID eines Listentyps. Auch wenn keine Instanzen dieses Typs von Listen auf der Website vorhanden sind, befindet sich die Liste *Typ*  auf jeder SharePoint-Website. Das Attribut sollte jetzt wie folgt aussehen.
    
  ```XML
  
RegistrationId="100"
  ```

2. Fügen Sie in der Datei „SharePointComponentDeployer.cs" die folgende Zeile zur Methode  `DeployChainStoreComponentsToHostWeb` direkt unter der Zeile hinzu, die `CreateLocalEmployeesList` aufruft. Sie erstellen diese Methode im nächsten Schritt.
    
  ```cs
  ChangeCustomActionRegistration();
  ```

3. Fügen Sie der Klasse  `SharePointComponentDeployer` die folgende Methode hinzu. Beachten Sie Folgendes bei diesem Code:
    
  - Da die benutzerdefinierte Aktion, das heißt, die benutzerdefinierte Schaltfläche, beim Menüband einer Liste  *Typ*  registriert wurde, wird sie der gesamten Website zugeordnet und befindet sich in der Sammlung der benutzerdefinierten Aktionen der Website. Daher ruft der Code sie aus dieser Sammlung ab.
    
  
  - Der Wert für  `action.Name` stammt aus dem Attribut **ID** des Elements **CustomAction** in der Datei „element.xml" in **AddEmployeeToCorpDB**.
    
    > **WICHTIG**
      > **Sie müssen den Wert  `action.Name` im Code unten ändern, damit er mit dem Wert in der Datei „elements.xml" übereinstimmt.** Der GUID-Teil des Namens weicht ab. Beachten Sie, dass ein „."-Zeichen zwischen der GUID und dem Rest des Namens vorhanden ist. Der folgende Code ist ein Beispiel der Zeile.>  `where action.Name == "4a926a42-3577-4e02-9d06-fef78586b1bc.AddEmployeeToCorpDB"`

  ```cs
  private static void ChangeCustomActionRegistration()
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
         var query = from action in clientContext.Web.UserCustomActions
                     where action.Name == "{button_GUID} .AddEmployeeToCorpDB"
                     select action;
          IEnumerable<UserCustomAction> matchingActions = clientContext.LoadQuery(query);	       
	         clientContext.ExecuteQuery();
	
          UserCustomAction webScopedEmployeeAction = matchingActions.Single();

         // TODO8: Get a reference to the (empty) collection of custom actions 
         // that are registered with the custom list.

         // TODO9: Add a blank custom action to the list's collection.

         // TODO10: Copy property values from the descriptively deployed
         // custom action to the new custom action

        // TODO11: Delete the original custom action.         

          clientContext.ExecuteQuery();
    }
}
  ```

4. Ersetzen Sie  `TODO8` durch den folgenden Code.
    
    Beachten Sie, dass Komponenten, die das Add-In erstellt beim Zurückziehen eines Add-Ins nicht entfernt werden. Nach der Ausführung Ihrer zuerst ausgeführten Logik ist eine benutzerdefinierte Aktion in der **UserCustomActions**-Auflistung der Liste enthalten, und sie wird nicht zurückgezogen, wenn Sie beim nächsten Mal F5 drücken. Um Verwirrung zu vermeiden, leert die letzte Zeile in diesem Code,  `listActions.Clear();`, die Auflistung. 
    


  ```cs
  
var queryForList = from list in clientContext.Web.Lists
                   where list.Title == "Local Employees"
                   select list;
IEnumerable<List> matchingLists = clientContext.LoadQuery(queryForList);
clientContext.ExecuteQuery();

List employeeList = matchingLists.First();
var listActions = employeeList.UserCustomActions;
clientContext.Load(listActions);
listActions.Clear();
  ```

5. Ersetzen Sie  `TODO9` mit der folgenden Zeile, die eine nicht definierte benutzerdefinierte Aktion zur Liste **Lokale Mitarbeiter** hinzufügt.
    
  ```cs
  
var listScopedEmployeeAction = listActions.Add();
  ```

6. Ersetzen Sie  `TODO10` durch den folgenden Code. Beachten Sie Folgendes bei diesem Code:
    
  - Er weist die Eigenschaftswerte der Schaltfläche für den Webbereich (die mit beschreibendem Markup bereitgestellt wurde) den entsprechenden Eigenschaften der Schaltfläche für den Listenbereich zu, damit die beiden Schaltflächen außer im Bereich identisch sind.
    
  
  - Die Eigenschaft **Sequence** gibt die relative Reihenfolge an, in der die Schaltfläche in ihrem Bereich des Menübands angezeigt wird. In diesem Fall befindet sich die Schaltfläche im Abschnitt **Aktionen** Teil der Registerkarte **Elemente** im Menüband. Im beschreibenden Markup war dieser Wert auf 10001 festgelegt, was hoch genug ist, um sicherzustellen, dass die Schaltfläche nach (d. h. rechts neben) den internen Schaltflächen angezeigt wird, die SharePoint selbst in den Abschnitt **Aktionen** im Menüband einfügt.
    
  

  ```cs
  listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
listScopedEmployeeAction.Update();
  ```

7. Ersetzen Sie  `TODO11` durch die folgende Zeile, die die ursprüngliche, deskriptiv definierte Schaltfläche löscht. Wenn diese Zeile nicht vorhanden wäre, würde jede Liste auf der Website, die die Listenvorlage „100" verwendet, die benutzerdefinierte Schaltfläche enthalten. Da die Funktionalität der Schaltfläche eng verbunden ist mit der Liste **Lokale Mitarbeiter**, würde es keinen Sinn machen, die Schaltfläche auf einer anderen Liste zu haben. Außerdem würde die Schaltfläche ohne diese Zeile  *zweimal*  in der Liste **Lokale Mitarbeiter** angezeigt werden, die die Liste die Vorlage „100" verwendet.
    
  ```cs
  
webScopedEmployeeAction.DeleteObject();
  ```


    Die gesamte Methode sollte jetzt wie folgt aussehen (mit Ausnahme einer GUID anstelle des Platzhalters).
    


  ```cs
  private static void ChangeCustomActionRegistration()
{
    using (var clientContext = SPContext.CreateUserClientContextForSPHost())
    {
         var query = from action in clientContext.Web.UserCustomActions
                     where action.Name == "{button_GUID} .AddEmployeeToCorpDB"
                     select action;
          IEnumerable<UserCustomAction> matchingActions = clientContext.LoadQuery(query);	       
	         clientContext.ExecuteQuery();
	
          UserCustomAction webScopedEmployeeAction = matchingActions.Single();

         var queryForList = from list in clientContext.Web.Lists
                            where list.Title == "Local Employees"
                            select list;
         IEnumerable<List> matchingLists = clientContext.LoadQuery(queryForList);
         clientContext.ExecuteQuery();

        List employeeList = matchingLists.First();
        var listActions = employeeList.UserCustomActions;
        clientContext.Load(listActions);
        listActions.Clear();

        var listScopedEmployeeAction = listActions.Add();

        listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
        listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
        listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
        listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
        listScopedEmployeeAction.Update();

        webScopedEmployeeAction.DeleteObject();         

        clientContext.ExecuteQuery();
    }
}
  ```


## Anfordern der vollständigen Kontrolle über das Hostweb

Da das Add-In jetzt benutzerdefinierte Aktionen mit Webbereich hinzufügt und löscht, müssen die Berechtigungen, die das Add-In anfordert, von Verwalten auf Vollzugriff eskaliert werden. Gehen Sie folgendermaßen vor.
  
    
    

1. Öffnen Sie im **Projektmappen-Explorer** die Datei AppManifest.xml im **ChainStore**-Projekt. 
    
  
2. Öffnen Sie die Registerkarte **Berechtigungen**. Behalten Sie für den Wert **Bereich** **Web** bei, wählen Sie aber im Feld **Berechtigung** die Option **Vollzugriff** aus der Dropdownliste aus.
    
  
3. Speichern Sie die Datei.
    
  

## Ausführen des Add-Ins und Testen der Bereitstellung der Schaltfläche


  
    
    

1. Öffnen Sie die Seite **Websiteinhalte** der Website des Hongkong-Stores, *und entfernen Sie die Liste **Lokale Mitarbeiter**!* 
    
    > **HINWEIS**
      > Durch das Zurückziehen eines Add-Ins in Visual Studio werden vom Add-In erstellte Listen nicht entfernt, daher müssen Sie sie jedes Mal manuell löschen, wenn Sie Code testen, der sie erstellt. 
2. Verwenden Sie die F5-TASTE, um Ihr Add-In bereitzustellen und auszuführen. Visual Studio hostet die Remotewebanwendung in IIS Express und die SQL-Datenbank in SQL Express. Außerdem wird eine temporäre Installation des Add-Ins auf Ihrer SharePoint-Testwebsite durchgeführt, und das Add-In wird sofort ausgeführt. Sie werden aufgefordert, Berechtigungen für das Add-In zu erteilen, bevor die Startseite geöffnet wird.
    
  
3. Wenn die Add-In-Startseite geöffnet wird, wählen Sie den Link **Zurück zur Website** auf dem Chromesteuerelement im oberen Bereich aus.
    
  
4. Navigieren Sie zur Seite **Websiteinhalte**. Die Liste **Lokale Mitarbeiter** ist vorhanden, da Ihre zuerst ausgeführte Logik sie hinzugefügt hat.
    
    > **HINWEIS**
      > Wenn die Liste nicht vorhanden ist oder Sie andere Anzeichen haben, dass der zuerst ausgeführte Code nicht ausgeführt wird, wird möglicherweise die Tabelle **Mandanten** nicht in einen leeren Zustand zurückgesetzt, wenn Sie F5 drücken. Die häufigste Ursache hierfür ist, dass das Projekt **ChainCorporateDB** nicht mehr als Startprojekt in Visual Studio festgelegt ist. Im oberen Bereich dieses Artikels finden Sie Informationen, wie Sie dies beheben. Stellen Sie außerdem sicher, dass Sie die Datenbank so konfiguriert haben, dass sie wie unter [Konfigurieren von Visual Studio zum erneuten Erstellen der Unternehmensdatenbank bei jeder Debugsitzung](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md#Rebuild) beschrieben neu erstellt wird.
5. Öffnen Sie die Liste, und fügen Sie ein Element hinzu.
    
  
6. Wählen Sie in der Listenansicht das Element aus, und öffnen Sie die Registerkarte **Elemente** im Menüband. Die Schaltfläche **Zu Unternehmens-DB hinzufügen** wird im Menüband angezeigt.
    
  
7. Klicken Sie auf die Schaltfläche. Der Mitarbeiter wird der Unternehmensdatenbank hinzugefügt und das Feld **Zu Unternehmens-DB hinzugefügt** wird in **Ja** geändert.
    
  
8. Navigieren Sie zurück zur Seite **Websiteinhalte**, und wählen Sie **Add-In hinzufügen** aus.
    
  
9. Fügen Sie eine neue **benutzerdefinierte Liste** hinzu. Standardmäßig ist der Typ „generisch". (Generisch ist Listentyp 100.) Öffnen Sie nach dem Erstellen der Liste die Registerkarte **Elemente** im Menüband. Beachten Sie, dass die Schaltfläche **Zu Unternehmens-DB hinzufügen** *nicht*  im Menüband angezeigt wird, da Ihr Code die Schaltfläche mit Webbereich gelöscht hat.
    
  
10. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.
    
  
11. Da Sie mit diesem Add-In und dieser Visual Studio-Projektmappe in anderen Artikeln arbeiten werden, hat es sich bewährt, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.
    
  

## 
<a name="Nextsteps"> </a>

 Ereignisse für Listen und Listenelemente können in SharePoint auch benutzerdefinierte Handler umfassen. In nächsten Artikel erfahren Sie, wie Sie einen erstellen und in Ihrer zuerst ausgeführten Logik bereitstellen: [Behandeln von Listenelementereignissen im vom Anbieter gehosteten Add-In](handle-list-item-events-in-the-provider-hosted-add-in.md)
  
    
    

