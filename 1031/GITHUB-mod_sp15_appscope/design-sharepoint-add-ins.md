---
title: Entwerfen von SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# Entwerfen von SharePoint-Add-Ins
In diesem Artikel erhalten Sie einen Überblick über die in SharePoint-Add-Ins verfügbaren Entwicklungs- und Architekturoptionen und erfahren, wie Sie die richtigen Entscheidungen treffen, um die Entwicklung Ihrer Add-Ins in SharePoint 2013 zu erleichtern.
Angenommen, Sie haben eine großartige Idee für ein Add-In. In diesem Abschnitt führen wir Sie durch die erforderlichen Entwurfsentscheidungen und stellen bewährte Methoden für die Erstellung Ihres Add-Ins bereit. Was macht zum Beispiel eine gute Benutzeroberfläche aus? Welche Add-In-"Formen" sind verfügbar? Nach welchen Kriterien sollten diese ausgewählt werden? Welche Optionen stehen für den Datenzugriff zur Verfügung? 





## Erste Schritte beim Entwurf von SharePoint-Add-Ins
<a name="SP15Design_Startdesigning"> </a>

Da das Cloud-Add-In-Modell in SharePoint 2013 so viele Designoptionen ermöglicht, können SharePoint-Add-Ins die unterschiedlichsten Formen und Größen haben. Dieser Abschnitt liefert nützliche Tipps für einige der wichtigsten Entscheidungen, die Sie bei der Planung und beim Entwurf der Architektur und der Benutzerfreundlichkeit Ihres Add-Ins treffen müssen. Dazu zählen das Hosting Ihres Add-Ins, der effiziente und sichere Zugriff auf Daten und die Benutzung.



Eine Übersicht über die Design- und Architekturoptionen für SharePoint-Add-Ins finden Sie unter  [Drei Ansätze, um Entwurfsentscheidungen für Add-Ins für SharePoint zu treffen](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md). Worum es sich bei den SharePoint-Add-Ins handelt, erfahren Sie unter  [SharePoint-Add-Ins](sharepoint-add-ins.md).




## Auswählen des richtigen Hostingmodells für Ihr Add-In
<a name="SP15Design_Hostingmodel"> </a>

SharePoint-Add-Ins unterstützen mehrere Hostingoptionen. Sie können Ihren eigenen Webstapel wählen, Microsoft mit der Bereitstellung von Microsoft Azure und SQL Azure beauftragen oder das Add-In auf SharePoint hosten. In Tabelle 1 ist ein Artikel aufgeführt, der Ihnen bei der Auswahl des richtigen Hostingmodells für Ihr Add-In hilft.




**Tabelle 1: Anleitung zur Auswahl des richtigen Hostingmodells für SharePoint-Add-Ins**


|**Artikel**|**Beschreibung**|
|:-----|:-----|
| [Auswählen von Mustern für die Entwicklung und das Hosting Ihres Add-Ins für SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |In diesem Artikel erfahren Sie mehr über die verschiedenen Verfahren zum Hosten der Komponenten von SharePoint-Add-Ins.  <br/> |
 

## Auswählen der richtigen Datenzugriffstechnologie für Ihr Add-In
<a name="SP15Design_Dataaccess"> </a>

Sie müssen sicherstellen, dass Ihr Add-In effizient und sicher auf Daten zugreift. Für den Zugriff auf SharePoint und die Arbeit mit Daten in Ihrem Add-In stehen verschiedene Datenzugriffstechnologien zur Verfügung. In Tabelle 2 ist ein Artikel aufgeführt, in dem Sie erfahren, welche Optionen es gibt und wie Sie die richtige für Ihr Add-In auswählen. 




**Tabelle 2: Anleitung zur Auswahl der richtigen Datenzugriffstechnologien zur Verwendung in SharePoint-Add-Ins**


|**Artikel**|**Beschreibung**|
|:-----|:-----|
| [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> | In diesem Artikel erfahren Sie, welche Datenzugriffsoptionen Sie bei der Erstellung von SharePoint-Add-Ins haben. Erläutert werden auch die Datenkonnektivitätsoptionen für eingehende und ausgehende Daten sowie die APIs, die für den Zugriff auf SharePoint von Ihrem Add-In aus zur Verfügung stehen. <br/> |
 

## Entwickeln der UX für Ihr Add-In
<a name="SP15Design_UX"> </a>

Bei der Entwicklung eines Add-Ins sollte es Ihnen vor allem darauf ankommen, dass das Add-In so benutzerfreundlich ist, dass Benutzer die vorgesehenen Funktionen ausführen können. In Tabelle 3 ist ein Artikel aufgeführt, der erläutert, wie Sie erstklassige Add-Ins entwickeln, die bewährten Anforderungen an die Benutzerfreundlichkeit genügen und deren Gestalt und Funktionsweise sich an SharePoint 2013 anlehnt.




**Tabelle 3: Anleitung zur Sicherstellung erstklassiger Benutzerfreundlichkeit für SharePoint-Add-Ins**


|**Artikel**|**Beschreibung**|
|:-----|:-----|
| [UX-Design für SharePoint-Add-Ins](ux-design-for-sharepoint-add-ins.md) <br/> |In diesem Artikel erfahren Sie, welche Optionen Sie hinsichtlich der Benutzerfreundlichkeit haben, wenn Sie SharePoint-Add-Ins entwickeln.  <br/> |
 

## Entwerfen im Hinblick auf Updates
<a name="Upgrade"> </a>

Irgendwann möchten Sie ein Update Ihres Add-Ins erstellen und in den Office Store oder den Add-In-Katalog eines Unternehmens hochladen. Dies ist erheblich einfacher, wenn Sie spätere Updates des Add-Ins bereits beim Entwurf der ersten Version berücksichtigen. Daher sollten Sie die folgenden Artikel zu einem frühen Zeitpunkt in der Entwurfsphase lesen:  [Aktualisierungsverfahren für Add-Ins für SharePoint](sharepoint-add-ins-update-process.md) und [Aktualisieren von Add-Ins für SharePoint](update-sharepoint-add-ins.md). 




## Nächste Schritte: Entwickeln und Veröffentlichen Ihres Add-Ins
<a name="SP15Design_Next"> </a>

Haben Sie ein solides Konzept für Ihr Add-In? Dann können Sie direkt mit der Erstellung des Add-Ins und seiner Veröffentlichung loslegen. Die in Tabelle 4 aufgeführten Artikel helfen Ihnen dabei.




**Tabelle 4: Anleitungen zur Entwicklung und Veröffentlichung von SharePoint-Add-Ins**


|**Artikel**|**Beschreibung**|
|:-----|:-----|
| [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md) <br/> |Beschreibt die weiterführenden Konzepte und Funktionen des Add-In-Modells  <br/> |
| [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md) <br/> |Beschreibt die Vorgehensweise und die Anforderungen für die Veröffentlichung von SharePoint-Add-Ins  <br/> |
 

## Zusätzliche Ressourcen
<a name="SP15Design_AddRes"> </a>


-  [SharePoint-Add-Ins-Beispielpaket](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)


-  [SharePoint-Entwicklung neu betrachtet](http://msdn.microsoft.com/de-de/office/apps/dn133840)


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)


-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)


-  [Blog für Add-Ins](http://blogs.msdn.com/b/spoffapps)



