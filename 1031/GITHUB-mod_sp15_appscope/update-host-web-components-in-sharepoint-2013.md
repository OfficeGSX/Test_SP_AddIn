---
title: Aktualisieren von Hostwebkomponenten in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# Aktualisieren von Hostwebkomponenten in SharePoint 2013
Aktualisieren Sie Add-In-Webparts und benutzerdefinierte Aktionen im Hostweb eines SharePoint-Add-Ins.
## Vorraussetzungen für das Aktualisieren der Hostwebkomponenten
<a name="Prerequisites"> </a>

Machen Sie sich vertraut mit  [Aktualisieren von Add-Ins für SharePoint](update-sharepoint-add-ins.md) und den darin aufgeführten Voraussetzungen und Kernkonzepten.




## Aktualisieren von Hostwebkomponenten
<a name="UpdateHostWeb"> </a>

Ihr Add-In kann zwei Arten von Komponenten auf einem Hostweb mit beschreibendem Markup im SharePoint-Add-In installieren: benutzerdefinierte Aktionen undAdd-In-Webparts. Das Aktualisieren dieser Komponenten ist im Hostweb einacher als im Add-In-Web. Sie müssen keine Semantik aktualisieren, fügen Sie einfach die benutzerdefinierten Aktionen und Add-In-Webparts hinzu bzw. ändern Sie diese. Wenn das SharePoint-Add-In aktualisiert wird, wendet SharePoint immer alle neuen Elementmanifestdateien an und wendet dann alle geänderten Elementmanifestdateien in ihrer aktuellen Version erneut an. Die erneute Anwendung verursacht keine Probleme; z. B wird eine Menübandschaltfläche für eine benutzerdefinierte Aktion nicht mehrfach zum Menüband hinzugefügt.



Wenn Sie ein App-Webpart aktualisieren, ersetzt SharePoint die alte Version durch die neue Version im Webpartkatalog. Achten Sie darauf, die Eigenschaft **Name** des **ClientWebPart**-Objekts zu ändern, wenn Sie ein App-Webpart aktualisieren. Dies gewährleistet beim Aktualisieren der App, dass SharePoint die alte Version des App-Webparts (diese ist nicht mehr Teil der App) von allen Seiten entfernt, zu denen sie hinzugefügt wurde. Die Benutzer müssen die neue Version erneut zu den Seiten hinzufügen. 



Wenn Sie die Eigenschaft **Name** unverändert lassen, bleibt die alte Version auf den Seiten, zu denen Sie hinzugefügt wurde. Dadurch ist es unwahrscheinlich, dass die Benutzer bemerken, dass eine neue Version des Add-In-Webparts verfügbar ist. Außerdem wird beim Hinzufügen des Add-In-Webparts zu einer Seite die neue Version verwendet. Dadurch hätte eine Version eines SharePoint-Add-Ins unterschiedliche Add-In-Webparts auf unterschiedlichen Seiten.



Sie können andere Hostwebkomponenten programmgesteuert bereitstellen, indem Sie einen Remoteereignisempfänger verwenden, den Sie im App-Manifest mit einem  [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx)-Element registrieren. Sie sollten einen  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)-Empfänger verwenden, um Komponenten zu aktualisieren, die ursprünglich mit einem **InstalledEventEndpoint**-Empfänger bereitgestellt wurden. Weitere Informationen zu  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)-Empfängern finden Sie unter  [Erstellen eines Handlers für das Updateereignis in SharePoint-Add-Ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).




## Nächste Schritte
<a name="Next"> </a>

Wechseln Sie zu  [Wichtige Schritte beim Aktualisieren eines Add-Ins](update-sharepoint-add-ins.md#MajorAppUpgradeSteps), oder rufen Sie direkt einen der folgenden Artikel auf, um zu erfahren, wie Sie die nächste Hauptkomponente Ihres SharePoint-Add-Ins aktualisieren.




-  [Aktualisieren von Add-In-Webkomponenten in SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)


-  [Erstellen eines Handlers für das Updateereignis in SharePoint-Add-Ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)


-  [Aktualisieren von Remotekomponenten in Add-Ins für SharePoint](update-remote-components-in-sharepoint-add-ins.md)



## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Aktualisieren von Add-Ins für SharePoint](update-sharepoint-add-ins.md)


-  [Hostwebsites, Add-In-Websites und SharePoint-Komponenten in SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)



