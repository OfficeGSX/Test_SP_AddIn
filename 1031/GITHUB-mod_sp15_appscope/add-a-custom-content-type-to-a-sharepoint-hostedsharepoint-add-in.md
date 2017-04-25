---
title: Hinzufügen eines benutzerdefinierten Inhaltstyps zu einem von SharePoint gehosteten Add-In für SharePoint
ms.prod: SHAREPOINT
ms.assetid: b52e5622-bf87-4bb1-a99a-ac1389de6651
---


# Hinzufügen eines benutzerdefinierten Inhaltstyps zu einem von SharePoint gehosteten Add-In für SharePoint
Erfahren Sie, wie Sie benutzerdefinierte Inhaltstypen in ein SharePoint-Add-Ins einschließen.
Dies ist der vierte einer Reihe von Artikeln über die Grundlagen der Entwicklung von SharePoint gehosteter SharePoint-Add-Ins. Machen Sie sich zunächst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den anderen Artikeln dieser Reihe vertraut:
  
    
    


-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Bereitstellung und Installation eines von SharePoint gehosteten Add-Ins für SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Hinzufügen von benutzerdefinierten Spalten zu einem von SharePoint gehosteten Add-In für SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  

> **HINWEIS**
> Wenn Sie diese Reihe zu von SharePoint gehosteten Add-Ins durchgearbeitet haben, haben Sie eine Visual Studio-Lösung, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_SP-Hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) herunterladen und die Datei „BeforeContentType.sln" öffnen.
  
    
    

In diesem Artikel fügen Sie einen benutzerdefinierten Inhaltstyp zum SharePoint-Add-In „Orientierung für Mitarbeiter" hinzu.
## Erstellen des benutzerdefinierten Inhaltstyps


  
    
    

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** und **Neuer Ordner** aus. Nennen Sie den OrdnerInhaltstypen.
    
  
2. Klicken Sie mit der rechten Maustaste auf den neuen Ordner, und wählen Sie **Hinzufügen** und **Neues Element** aus. Das Dialogfeld **Neues Element hinzufügen** wird mit dem Knoten **Office/SharePoint** geöffnet.
    
  
3. Wählen Sie **Inhaltstyp** aus, geben Sie ihm den NamenNewEmployee, und wählen Sie **Hinzufügen** aus. Wenn Sie vom Assistenten dazu aufgefordert werden, den Basisinhaltstyp auszuwählen, wählen Sie **Element** und dann **Fertig stellen** aus.
    
  
4. Wenn der Inhaltstyp-Designer nicht automatisch geöffnet wird, wählen Sie den Inhaltstyp **NewEmployee** im **Projektmappen-Explorer** aus, um ihn zu öffnen.
    
  
5. Öffnen Sie die Registerkarte **Inhaltstyp** im Designer, und füllen Sie die Textfelder wie folgt aus:
    
  - **Inhaltstypname**: NewEmployee
    
  
  - **Beschreibung**: Stellt einen neuen Mitarbeiter dar.
    
  
  - **Gruppenname**: Orientierung für Mitarbeiter
    
  
6. Stellen Sie sicher, dass  *keines*  der Kontrollkästchen auf der Registerkarte aktiviert ist. Das Kontrollkästchen für **Erbt die Spalten vom übergeordneten Inhaltstyp** ist möglicherweise standardmäßig aktiviert. *Deaktivieren Sie es unbedingt.*  Die Registerkarte sollte jetzt wie folgt aussehen:
    
   **Registerkarte „Inhaltstyp"**

  

!\[Der Inhaltstyp-Designer, in dem "NewEmployee" als Typname "Stellt einen neuen Mitarbeiter dar" als Beschreibung und "Mitarbeiterorientierung" als Gruppe angezeigt wird.](images/8a9768f4-315d-45c0-88d7-687dbf84495c.PNG)
  

    
    
  
7. Öffnen Sie die Registerkarte **Spalten** im Designer.
    
  
8. Wählen Sie im Raster **Klicken Sie hier, um eine Spalte hinzuzufügen** aus, um eine Dropdownliste der Spalten zu öffnen, und fügen Sie die Spalte **Abteilung** hinzu. Sie ist in der Dropdownliste mit ihrem Anzeigenamen aufgeführt: **Abteilung**. Wiederholen Sie den Vorgang für die Spalte **Orientierungsphase**. (Wenn diese nicht aufgeführt sind, haben Sie möglicherweise nicht mit der richtigen Visual Studio-Lösung begonnen. Beginnen Sie mit BeforeContentType.sln.) Wenn Sie fertig sind, sollte das Raster wie folgt aussehen:
    
   **Registerkarte „Spalten"**

  

!\[Die Registerkarte "Spalten" des Inhaltstyp-Designers, wobei "Mitarbeiter", "Abteilung" und "Orientierungsphase" im Raster aufgeführt ist.](images/835e78b3-a073-45b2-b4ee-3f9be9d88495.PNG)
  

    
    
  
9. Speichern Sie die Datei, und schließen Sie den Designer.
    
  
10. Der nächste Schritt erfordert, dass Sie direkt im unformatierten XML des Inhaltstyps arbeiten. Wählen Sie daher im **Projektmappen-Explorer** die dem Inhaltstyp **NewEmployee** untergeordnete Datei elements.xml aus.
    
  
11. Die Datei enthält bereits **FieldRef**-Elemente für die beiden Spalten, die Sie hinzugefügt haben. Fügen Sie **FieldRef**-Elemente für zwei integrierte SharePoint-Spalten als übergeordnete Elemente der beiden bereits vorhandenen hinzu. Nachfolgend dargestellt ist das Markup für die Elemente.  *Sie müssen diese GUIDs für die ID-Attribute verwenden, da diese integrierte Feldtypen mit festen IDs sind.*  Fügen Sie sie *über*  den beiden **FieldRef**-Elementen für die benutzerdefinierten Websitespalten hinzu.
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Employee" />
  ```


    Beachten Sie, dass wir diesen Feldern einen benutzerdefinierten Anzeigenamen gegeben haben: **Employee**.
    
  
12. Speichern und schließen Sie die Datei.
    
  
13. Erweitern Sie den Knoten **Listen** im **Projektmappen-Explorer**, und wählen Sie **NewEmployeeOrientation** aus, um den Listentyp-Designer zu öffnen.
    
  
14. Öffnen Sie die Registerkarte **Spalten** im Designer, und klicken Sie dann auf die Schaltfläche **Inhaltstypen**.
    
  
15. Fügen Sie im Dialogfeld **Einstellungen für Inhaltstypen** den Inhaltstyp **NewEmployee** hinzu.
    
  
16. Wählen Sie in der Liste der Typen den Inhaltstyp **NewEmployee** aus, und klicken Sie auf die Schaltfläche **Als Standard festlegen**.
    
  
17. Wählen Sie den Inhaltstyp **Element** aus, klicken Sie mit der rechten Maustaste auf die kleine Pfeilspitze, die links vom Inhaltstypnamen angezeigt wird, und klicken Sie dann auf **Löschen**.
    
  
18. Wiederholen Sie den vorherigen Schritt für den Inhaltstyp **Ordner**, sodass **NewEmployee** als einziger Inhaltstyp angezeigt wird. Das Dialogfeld sollte jetzt wie folgt aussehen:
    
   **Dialogfeld „Einstellungen für Inhaltstypen"**

  

!\[Das Dialogfeld "Einstellungen" des Inhaltstyps, in dem nur ein einziger Inhaltstyp mit dem Namen "NewEmployee" aufgeführt ist.](images/b90699f4-40de-4f50-ad47-3e8773d0eb92.PNG)
  

    
    
  
19.  Wählen Sie **OK** aus, um das Dialogfeld zu schließen, und speichern und schließen Sie die Datei.
    
  
20. Öffnen Sie die Datei schema.xml.
    
  
21. Suchen Sie nach dem Element **Fields**. Es sollte über drei **Field**-Elemente verfügen: **Title**, Abteilung undOrientationStage. (Diese Elemente befinden sich in der generierten Daten möglicherweise in einer Zeile. Wenn das der Fall ist, trennen Sie sie durch Zeilenumbrüche.)
    
  
22. Lassen Sie die Datei geöffnet, und erweitern Sie im **Projektmappen-Explorer** den Ordner **Websitespalten** und den KnotenAbteilung, und öffnen Sie dann die Datei „elements.xml" für Abteilung. Das Element **Field** fürAbteilung in schema.xml sollte exakt mit dem Element **Field** fürAbteilung in elements.xml übereinstimmen. Wenn keine genaue Übereinstimmung vorhanden ist, kopieren Sie das Element **Field** aus der elements.xml-Datei der Websitespalte, und fügen Sie sie anstelle des nicht übereinstimmenden **Field**-Elements in der schema.xml-Datei ein. Schließen Sie dann die elements.xml-Datei.
    
  
23. Öffnen Sie die Datei elements.xml für OrientationStage. Auch hier muss eine exakte Übereinstimmung der **Field**-Elemente in den zwei Dateien für OrientationStage vorhanden sein, einschließlich aller untergeordneten Elemente wie die Elemente **CHOICES** und **MAPPINGS**. Wenn keine Übereinstimmung vorhanden ist, kopieren Sie das Element **Field** in der elements.xml-Datei, und fügen Sie es anstelle des nicht übereinstimmenden **Field**-Elements in der schema.xml-Datei ein. Schließen Sie die elements.xml-Datei.
    
  
24. Während Sie sich weiterhin in der Datei „schema.xml" befinden, suchen Sie im Element **View**, dessen **BaseViewID**-Wert gleich 1 ist, nach dem untergeordneten **ViewFields**-Element, und fügen Sie ihm dann die folgenden zwei **FieldRef**-Elemente als untergeordnete Elemente hinzu. Möglicherweise sind sie bereits vorhanden, jedoch fehlt ein **ID**-Attribut. Wenn dies der Fall ist, fügen Sie das ID-Attribut hinzu.
    
  ```
  
<FieldRef Name="Division" ID="{GUID from the Field element}" />
<FieldRef Name="OrientationStage" ID="{GUID from the Field element}" />

  ```

25. Ersetzen Sie die zwei Platzhalter- **ID**-Attributwerte durch die GUIDs aus den entsprechenden **Field**-Elementen im Element **ContentType** für **NewEmployee**, das sich zuvor in der Datei „schema.xml" befand. Vergessen Sie nicht die umschließenden Klammern „{}". 
    
    **ViewFields** für **View** „1" sollte wie folgt aussehen. (Ihre GUIDs unterscheiden sich möglicherweise.)
    


  ```
  
<ViewFields>
   <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
   <FieldRef Name="Division" ID="{509d2d67-9a96-4596-9b3b-58449cdcc6ff}" />
   <FieldRef Name="OrientationStage" ID="{38a3b54c-acf3-4ddf-b748-55c7c28d4cc2}" />        
</ViewFields>
  ```

26. Während Sie sich weiterhin in der Datei „schema.xml" befinden, suchen Sie im Element **View**, dessen **BaseViewID**-Wert gleich 0 ist, nach dem enthaltenen **ViewFields**-Element.
    
  
27. Kopieren Sie den gesamten Abschnitt **ViewFields** aus Ansicht „1" über den Abschnitt **ViewFields** von Ansicht „0". Die beiden Ansichten sollten jetzt über identische **ViewFields**-Abschnitte verfügen.
    
  
28. Speichern und schließen Sie die Datei schema.xml.
    
  
29. Erweitern Sie im Ordner **Listen** den Node **NewEmployeeOrientation** und seine untergeordneten Listeninstanz **NewEmployeesInSeattle**. Sie sollten die elements.xml für die Vorlage klar von der elements.xml für die Instanz unterscheiden können. Öffnen Sie die Datei für die Instanz. 
    
  
30. Fügen Sie zwei **Field**-Elemente zum ersten **Row**-Element hinzu, sodass das Element **Row** wie folgt aussieht.
    
  ```
  
<Row>
  <Field Name="Title">Tom Higginbotham</Field>
  <Field Name="Division">Manufacturing</Field>
  <Field Name="OrientationStage">Tour of building</Field>
</Row>
   
  ```

31. Speichern und schließen Sie die Datei.
    
  

## Ausführen und Testen des Add-Ins


  
    
    

1. Verwenden Sie die F5-TASTE, um Ihr Add-In bereitzustellen und auszuführen. Visual Studio führt eine temporäre Installation des Add-Ins auf Ihrer SharePoint-Testwebsite durch und führt das Add-In sofort aus. 
    
  
2. Wenn die Standardseite des Add-Ins geöffnet wird, wählen Sie den Link für **Neue Mitarbeiter in Seattle** aus, um die benutzerdefinierte Listeninstanz zu öffnen.
    
  
3. Die Listenseite wird mit den Spalten Abteilung undOrientationStage geöffnet. Es ist nicht erforderlich, dass ein Benutzer diese manuell hinzufügt, da sie Teil der Inhaltstyp der Liste sind. Das oberste Element verfügt über die Daten, die Sie hinzugefügt haben.
    
   **Liste „New Employees in Seattle"**

  

!\[Die Liste "Neue Mitarbeiter in Seattle", in der die Spalten "Abteilung" und "Orientierungsphase" bereits vorhanden sind.](images/b654af45-663e-425c-b7c7-b8b5524cb316.PNG)
  

    
    
  
4. Versuchen Sie, der Liste neue Elemente hinzuzufügen und vorhandene Elemente zu bearbeiten.
    
  
5. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.
    
  
6. Da Sie mit diesem Add-In und dieser Visual Studio-Lösung in anderen Artikeln arbeiten werden, hat es sich bewähr, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.
    
  

## 
<a name="Nextsteps"> </a>

Im nächsten Artikel dieser Reihe fügen Sie ein Listenansicht-Webpart zur Standardseite des SharePoint-Add-Ins hinzu:  [Hinzufügen eines Webparts zu einer Seite in einem von SharePoint gehosteten Add-In für SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md).
  
    
    

