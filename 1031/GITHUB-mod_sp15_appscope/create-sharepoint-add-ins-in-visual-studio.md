---
title: Erstellen von SharePoint-Add-Ins in Visual Studio
ms.prod: SHAREPOINT
ms.assetid: f1b9c858-907c-4558-b671-3b488ece40a0
---



# Erstellen von SharePoint-Add-Ins in Visual Studio
In diesem Artikel erfahren Sie, wie Sie SharePoint-Add-Ins mithilfe von Vorlagen für Projekte und Projektelemente in Visual Studio entwickeln.
Sie können SharePoint-Add-Ins mithilfe neuer Vorlagen für Projekte und Projektelemente in **vsnv** erstellen.
  
    
    


## Projektvorlagen
<a name="SP15Projecttemplates_templates"> </a>

Wenn Sie eine Projektvorlage in Visual Studio verwenden, erstellt diese eine Projektmappe mit den Projektelementen und -dateien, die für den Projekttyp erforderlich sind. Die folgenden Projektvorlagen werden im Dialogfeld **Neues Projekt** angezeigt, wenn Sie den Knoten **Office/SharePoint** und dann den Knoten **Add-Ins** auswählen. Informationen über die Projektvorlagen unter dem Knoten **SharePoint-Lösungen** finden Sie im Artikel über [SharePoint-Projekt- und -Projektelementvorlagen](http://go.microsoft.com/fwlink/?LinkId=255306). 
  
    
    

### Office-Add-In

Erstellt eine Webseite, die in einer Office-Anwendung wie Excel oder Outlook gehostet wird. Eine Office-Add-In liefert zusätzliche Inhalte und Funktionen in einem Dokument oder Outlook-Element. Weitere Informationen finden Sie unter  [Office-Add-Ins-Plattformübersicht](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### SharePoint-Add-In

Erstellt ein SharePoint-Add-In basierend auf den Informationen, die Sie in einem Assistenten eingeben. Zu diesen Informationen gehören folgende Angaben.
  
    
    

- Der Name des Add-Ins
    
  
- Die lokale SharePoint-Website oder die SharePoint-Remotewebsite für das Debugging Ihres Add-Ins.
    
  
- Die Art des Add-Ins, das Sie erstellen möchten: automatisch gehostet, von einem Provider gehostet oder von SharePoint gehostet. 
    
  
Weitere Informationen finden Sie unter  [SharePoint-Add-Ins](sharepoint-add-ins.md).
  
    
    

### Cloud-Business-Add-In

Mithilfe der Vorlage **Cloud-Business-Add-In** in Visual Studio können Sie ein in SharePoint gehostetes Add-In erstellen, in dem mobile Benutzer an einem Remotestandort mithilfe moderner Geräte wie Smartphones und Tablet-PCs mit Toucheingabe Daten anzeigen, hinzufügen und aktualisieren können. Weitere Informationen finden Sie unter [Erstellen von Cloud-Geschäfts-Add-Ins](create-cloud-business-add-ins.md).
  
    
    

## Projektelementvorlagen
<a name="SP15Projecttemplates_items"> </a>

Nach Erstellung einer SharePoint-Lösung können Sie Projektelemente anhand der folgenden Vorlagen hinzufügen, die im Dialogfeld **Neues Element hinzufügen** unter dem Knoten **Office/SharePoint** angezeigt werden.
  
    
    

### Office-Add-In

Fügt Ihrem SharePoint-Add-In ein Office-Add-In hinzu. Sie können ein Aufgabenbereichs-, ein Inhalts- oder ein Mail-Add-In erstellen. Weitere Informationen finden Sie unter  [Office-Add-Ins-Plattformübersicht](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### Clientwebpart (Hostweb)

Fügt Ihrem SharePoint-Add-In ein Clientwebpart hinzu. Durch Hinzufügen eines Clientwebparts können Sie Add-Ins auf den Seiten einer Hostwebsite anzeigen. Diese Vorlage enthält eine einzelne Datei vom Typ "Elements.xml", deren Eigenschaften die folgenden Elemente des Clientwebparts definieren:
  
    
    


|**Eigenschaftenname**|**Beschreibung**|
|:-----|:-----|
|ClientWebPart  <br/> |Gibt den Namen, den Titel, die Beschreibung und die Dimensionen des Clientwebparts an.  <br/> |
|Content  <br/> |Definiert den Speicherort der Seite, die innerhalb des Clientwebparts gerendert wird. Das Element verfügt über zwei Eigenschaften:  `Type` und `Src`.  `Type` gibt den Typ des Webparts an, das Sie erstellen, beispielsweise HTML. `Src` definiert den Speicherort der Seite, die innerhalb des Clientwebparts gerendert wird. Die Vorlage verweist unter Verwendung des Schemas _ _Eigenschaftsname__ auf Eigenschaften in der Abfragezeichenfolge. Beispiel:  `Src="~addinWebUrl/Pages/ClientWebPart1.aspx?Property1=_property1_"` <br/> Weitere Informationen finden Sie unter  [Erstellen von Add-In-Webparts zur Installation mit Ihrem SharePoint-Add-In](create-add-in-parts-to-install-with-your-sharepoint-add-in.md).  <br/> |
   

### Inhaltstyp

Fügt Ihrer SharePoint-Add-In einen Inhaltstyp hinzu, ähnlich wie bei Inhaltstypen, die in früheren Version von SharePoint verwendet wurden. Ein Inhaltstyp ist ein Satz Metadaten, Workflows und Verhaltensweisen für eine Kategorie von Elementen in einer SharePoint-Liste oder -Bibliothek. Beispielsweise ist ein Element ein Listeninhaltstyp. Andere Listeninhaltstypen sind z. B. Ankündigungen, Kontakte und Aufgaben, und sie erben vom Inhaltstyp Element. Der Inhaltstyp Kontakt enthält Spalten wie **Vorname**, **Nachname** und **Position**.
  
    
    
Wenn Sie Ihrer SharePoint-Add-In einen Inhaltstyp hinzufügen, geben Sie den Basisinhaltstyp an, von dem der neue Inhaltstyp erben soll. Eine Vererbung ist beispielsweise von einer Ankündigung, einem Kontakt, einem Dokument oder von einem Element möglich. Danach konfigurieren Sie mit dem Inhaltstypdesignerdie Spalten für den Inhaltstyp und dessen Eigenschaften (wie Name und Beschreibung). Die von Ihnen gewählten Werte werden den Elementen  `ContentType` und `FieldRef` in der Datei "Elements.xml" hinzugefügt. Weitere Informationen finden Sie unter [Baustein: Inhaltstypen](http://msdn.microsoft.com/library/277dfc42-d9a8-4fae-9ae1-0d202b14674f%28Office.15%29.aspx).
  
    
    

### Leeres Element

Fügt Ihrer SharePoint-Add-In ein Projektelement für ein leeres Element hinzu. Dieses Projektelement enthält eine einzelne Datei (ELEMENTS.XML), in der Sie die Eigenschaften des Elements festlegen. Ein leeres Element wird in der Regel zur Definition eines Elements verwendet, für das Visual Studio keine Vorlage bereitstellt.
  
    
    

### Liste

Fügt Ihrem SharePoint-Add-In zwei Projektelemente hinzu: eine Listendefinition und eine Instanz der Liste. Wenn Sie Ihrem Add-In eine Liste hinzufügen, geben Sie an, wie die Liste genannt werden soll und ob eine leere Liste oder eine auf einem bestehenden Listentyp basierende Liste erstellt werden soll. Zudem geben Sie an, ob die Liste angepasst werden kann. Danach konfigurieren Sie mit dem **Listendesigner** die Spalten und Ansichten für die Liste sowie weitere Eigenschaften, wie den Namen und die Beschreibung der Liste. Weitere Informationen über Listeneigenschaften finden Sie unter [ListTemplate-Element (Listenvorlage)](http://msdn.microsoft.com/library/e565ead9-adcb-4a90-97e3-04850719420a%28Office.15%29.aspx) und [ListInstance-Element (Listeninstanz)](http://msdn.microsoft.com/library/cfefe8e5-2656-4d71-bb4e-5f991a800598%28Office.15%29.aspx).
  
    
    

### Benutzerdefinierte Menüelementaktion

Fügt ein Projektelement hinzu, das die Benutzeroberfläche der entsprechenden Hostwebsite erweitert, indem einem Listenmenü eine Aktion hinzugefügt wird. Die benutzerdefinierte Menüaktion umfasst eine Datei vom Typ "Elements.xml", in der die Eigenschaften der Aktion definiert werden. Weitere Informationen finden Sie unter  [Gewusst wie: Erstellen benutzerdefinierter Aktionen zur Bereitstellung mit Add-Ins für SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Modul

Fügt Ihrer SharePoint-Add-In ein Modul-Projektelement hinzu. Module sind einfach ausgedrückt Behälter, mit denen Sie bei der Bereitstellung Ihrer SharePoint-Add-In weitere Dateien einschließen können. Um eine Datei hinzuzufügen, kopieren Sie sie unter dem Modul im **Projektmappen-Explorer** in das Projekt. Ein Verweis auf die Datei wird der Datei ELEMENTS. XML für das Modul automatisch hinzugefügt. Der Verweis enthält den Pfad und die URL der neuen Datei. Die Datei SAMPLE.TXT für das Modul können Sie löschen, da sie lediglich als Beispiel dient.
  
    
    

### Remoteereignisempfänger

Fügt Ihrer SharePoint-Add-In ein Projektelement für einen Remoteereignisempfänger und Ihrer Lösung ein Webanwendungsprojekt hinzu, sofern noch kein solches Projekt vorhanden ist. Die Webanwendung enthält einen Webdienst, der dem Remoteereignisempfänger in Ihrer SharePoint-Add-In zugeordnet ist. Der Webdienst enthält eine Visual Basic- oder Visual C#-Codedatei, deren Code ausgeführt wird, wenn in der SharePoint-Add-In ein Listen-, ein Listenelement- oder ein Webelementereignis auftritt. Wenn eine Webanwendung vorhanden ist, wird sie der SharePoint-Add-In zugeordnet, und der Webdienst wird der Anwendung hinzugefügt. Weitere Informationen finden Sie unter  [Behandeln von Ereignissen in SharePoint-Add-Ins](handle-events-in-sharepoint-add-ins.md).
  
    
    

### Benutzerdefinierte Menübandaktion

Fügt ein Projektelement hinzu, das die Benutzeroberfläche der entsprechenden Hostwebsite erweitert, indem einem Menüband eine Aktion hinzugefügt wird. Die benutzerdefinierte Menübandaktion umfasst eine Datei vom Typ "Elements.xml", in der die Eigenschaften der Aktion definiert werden. Weitere Informationen finden Sie unter  [Gewusst wie: Erstellen benutzerdefinierter Aktionen zur Bereitstellung mit Add-Ins für SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Suchkonfiguration

Fügt ein Projektelement hinzu, das Ihnen das Importieren benutzerdefinierter Suchkonfigurationseinstellungen ermöglicht, die von einer SharePoint-Website exportiert wurden.
  
    
    

### Websitespalte

Fügt Ihrer SharePoint-Add-In ein Projektelement für eine Websitespalte hinzu. Die Websitespalte enthält eine Datei ELEMENTS.XML, die die  `Field`-Eigenschaften der Websitespalte definiert, einschließlich der folgenden Daten.
  
    
    


|**Eigenschaftenname**|**Beschreibung**|
|:-----|:-----|
|ID  <br/> |Ein eindeutiger GUID-Wert für die Websitespalte.  <br/> |
|Name  <br/> |Ein eindeutiger Name, der als Verweis auf die Websitespalte dient.  <br/> |
|DisplayName  <br/> |Ein auf der Benutzeroberfläche angezeigter Anzeigename.  <br/> |
|Type  <br/> |Der Datentyp der Websitespalte, der auf **SPFieldType** basiert, z. B. Boolesch, Nachschlagedaten oder Text. <br/> |
|Required  <br/> |Wenn die Spalte erforderlich ist, wird die Eigenschaft auf **True** festgelegt; anderenfalls wird die Eigenschaft auf **False** festgelegt. <br/> |
|Group  <br/> |Gibt den Namen der Gruppe an, der die Websitespalte zugeordnet ist. Der Standardwert für diese Eigenschaft lautet **Spalten benutzerdef. Website**.  <br/> |
   
Weitere Informationen finden Sie unter  [Baustein: Spalten und Feldtypen](http://msdn.microsoft.com/library/58548ade-e439-4931-82a2-fa29bd5afdb7%28Office.15%29.aspx).
  
    
    

### Workflow

Fügt Ihrer SharePoint-Add-In ein Projektelement für einen Microsoft Azure-Workflow hinzu. Weitere Informationen finden Sie unter  [Workflows in SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx). Wenn Sie diese Art von Element hinzufügen, geben Sie einen Namen für den Workflow an und definieren ihn als Listen- oder Website-Workflow. Ein Listenworkflow funktioniert nur mit einer Liste und ein Website-Workflow nur mit der SharePoint-Website. Bei der Erstellung des Workflows legen Sie zudem fest, ob dem Workflow automatisch Listen und Bibliotheken zugeordnet werden sollen, und wenn ja, welche. Für jede Zuordnung, die Sie hinzufügen, wird dem Workflowprojekt eine Datei hinzugefügt. Ein Workflow enthält die folgenden Dateien.
  
    
    


|**Dateiname**|**Beschreibung**|
|:-----|:-----|
|ELEMENTS.XML  <br/> |Gibt die Konfiguration des Workflows und die darin enthaltenen Dateien an, wie die Datei WORKFLOW.XAML und Zuordnungsdateien, sowie die Eigenschaften jeder Datei, wie URL, Typ und Pfad. Für jede Datei, die dem Workflowprojekt hinzugefügt wird, wird in der Datei ELEMENTS.XML für den Workflow ein entsprechender Bereich erstellt. Zuordnungsdateien in Listenworkflows erfordern eine Liste, damit sie über einen Verweis auf das Listentoken verfügen. In einem Website-Workflow wird eine GUID für die Website hinzugefügt.  <br/> > [!VORSICHT]> Da Visual Studio die Elemente in der Datei ELEMENTS.XML beibehält, raten wir von einer Änderung der Datei ab, es sei denn, Sie kennen die Auswirkungen der Änderungen genau.           |
|WORKFLOW.XAML  <br/> |Der Designer für den Workflow. In dieser Datei können Sie dem Workflow Aktionen hinzufügen und deren Code und Eigenschaften festlegen.  <br/> |
|WorkflowStartAssociation  <br/> |Startet den Workflow manuell in SharePoint. Diese Datei wird dem Workflowprojekt hinzugefügt, wenn Sie das Kontrollkästchen **Benutzer startet den Workflow manuell** im Workflow-Assistenten aktivieren. <br/> |
|ItemAddedAssociation  <br/> |Startet den Workflow manuell, sofern einer vorhanden ist, wenn ein Benutzer ein Element auf der Website oder in der Liste erstellt (je nach Workflowtyp). Diese Datei wird dem Workflowprojekt hinzugefügt, wenn Sie das Kontrollkästchen **Der Workflow wird automatisch gestartet, wenn ein Element hinzugefügt wird** im Workflow-Assistenten aktivieren. <br/> |
|ItemUpdatedAssociation  <br/> |Startet den Workflow automatisch, sofern einer vorhanden ist, wenn ein Benutzer ein Element auf der Website oder in der Liste ändert (je nach Workflowtyp). Diese Datei wird dem Workflowprojekt hinzugefügt, wenn Sie das Kontrollkästchen **Der Workflow wird automatisch gestartet, wenn ein Element geändert wird** im Workflow-Assistenten aktivieren. <br/> |
|WorkflowHistoryList  <br/> |Die Datei, die dem Workflowprojekt hinzugefügt wird, wenn eine Verlaufsliste für den Workflow im Workflow-Assistenten erstellt werden soll.  <br/> |
|WorkflowTaskList  <br/> |Die Datei, die dem Workflowprojekt hinzugefügt wird, wenn eine Aufgabenliste für den Workflow im Workflow-Assistenten erstellt werden soll.  <br/> |
   

### Benutzerdefinierte Workflowaktivität

Fügt Ihrer SharePoint-Add-In ein Projektelement für eine benutzerdefinierte Workflowaktivität hinzu. Durch Hinzufügen einer benutzerdefinierten Workflowaktivität können Sie zusätzliche Workflowaktionen erstellen, die dann als benutzerdefinierte Aktionen in SharePoint Designer 2013 importiert werden können. Die benutzerdefinierte Workflowaktivität umfasst eine Datei vom Typ "Elements.xml", in der die Eigenschaften der Aktion definiert werden, sowie eine XAML-Datei für den Workflow-Designer. Weitere Informationen finden Sie unter  [Workflows in SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx).
  
    
    

## Weitere Ressourcen
<a name="SP15Projecttemplates_addlresources"> </a>


-  [Tools und Umgebungen für die Entwicklung von Add-Ins für SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
