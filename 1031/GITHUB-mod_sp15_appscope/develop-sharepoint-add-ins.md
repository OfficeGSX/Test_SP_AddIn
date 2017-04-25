---
title: Entwickeln von Add-Ins für SharePoint
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# Entwickeln von Add-Ins für SharePoint
Hier finden Sie eingehende Artikel und Ressourcen, die es Ihnen erleichtern sollen, Ihre SharePoint-Add-Ins mit erweiterten Funktionen auszustatten.
> **HINWEIS**
> TIn diesem Artikel wird davon ausgegangen, dass Sie mit dem Artikel  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den Einstiegsmaterialien, auf die dieser verweist, vertraut sind.




Unter **Entwicklung** finden Sie folgende Ressourcen, in denen die verschiedenen Möglichkeiten in einer SharePoint-Add-In veranschaulicht werden:
- Ausführliche Übersichten


- Gewusst-wie-Artikel


- Codeausschnitte


Sie finden dort Artikel zu folgenden Themen: 
- Durchführen von Erstellungs-, Lese-, Aktualisierungs- und Löschoperationen (CRUD-Operationen) für Listen


- Erstellen von REST-Abfragen und Interaktion mit den neuen APIs


- Konfigurieren von OAuth für Sicherheit


SharePoint verfügt über Funktionen für soziale Netzwerke für Unternehmen, wie Aktivitätsfeeds und Benutzerprofile, sowie über Funktionen für das Enterprise Content Management, LOB-Interoperabilitätsfunktionen und Funktionen zum Entwurf von Websites, durch die sich durch Ihre Add-Ins wirklich aus der Masse herausheben können. Weitere Informationen hierzu finden Sie unter  [Hinzufügen von SharePoint 2013-Funktionen](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx).Da der Code das Wichtigste ist, sehen Sie sich im Dev Center das Menü "Beispiele" an. Es enthält Hyperlinks zu unseren Codebeispielen für Add-Ins. Sobald Sie Ihre Entwicklungsumgebung eingerichtet haben, sollten Sie sich einige unserer Beispiele ansehen. Nutzen Sie die Community-Funktion, mit der Sie ein Codebeispiel anfordern können, falls Sie in unseren Beispielen nicht das finden, was Sie suchen. Wir nutzen diese Anforderungen zusammen mit anderen Rückmeldungen für unsere laufenden Aktualisierungen des Inhalts und der Beispiele. Lassen Sie uns also bitte wissen, wenn Sie sich etwas wünschen!
## Erste Schritte mit SharePoint-Add-Ins-Ressourcen
<a name="bk_gettingstarted"> </a>

Wenn Sie Neuling in der Entwicklung mit SharePoint-Add-Ins sind, sehen Sie sich  [SharePoint-Add-Ins](sharepoint-add-ins.md) an. Diese Seite enthält Verweise auf wichtige Artikel, die Sie schnell mit verschiedenen Arten von SharePoint-Add-Ins vertraut machen. Bevor Sie beginnen, anspruchsvollere Projekte mit SharePoint-Add-Ins entwickeln, sollten Sie sich darüber im Klaren sein, welche Art von Add-Ins Sie erstellen möchten, welche Technologien Sie einbeziehen möchten und welche Hostingoptionen Sie verwenden möchten.




### Grundlegende Aufgaben und Ressourcen für die Entwicklung von SharePoint-Add-Ins mit dem Clientobjektmodell, dem JavaScript-Objektmodell und REST-Endpunkten in SharePoint 2013
<a name="bk_essentials"> </a>

Gleichgültig, welche Art von SharePoint-Add-In Sie erstellen werden, das Add-In interagiert immer auf irgendeine Weise mit einer SharePoint 2013-Website. Die Artikel in Tabelle 1 beschreiben, wie viele der wichtigsten Arten von Add-Ins mithilfe der drei Schnittstellen, die für SharePoint-Add-Ins zur Verfügung stehen, mit SharePoint-Sites zusammenarbeiten. Diese Schnittstellen sind das Clientobjektmodell, das JavaScript-Objektmodell und REST-Endpunkte.




**Tabelle 1. Grundlegende Vorgänge mit dem SharePoint 2013-Clientobjektmodell, dem JavaScript-Objektmodell und der REST-Schnittstelle**


|**Thema**|**Beschreibung**|
|:-----|:-----|
| [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |Hier wird erklärt, wie häufige Vorgänge mit C# und dem Clientobjektmodell ausgeführt werden.  <br/> |
| [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |Hier wird erklärt, wie häufige Vorgänge unter Verwendung des JavaScript-Objektmodells ausgeführt werden.  <br/> |
| [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |Hier wird erklärt, wie häufige Vorgänge mit der REST-Schnittstelle ausgeführt werden.  <br/> |
 

## Grundlegende Konzepte der Entwicklung von SharePoint-Add-Ins
<a name="bk_fundamentals"> </a>

Neben den grundlegenden Vorgängen sollten Sie auch die grundlegenden Konzepte des Add-In-Entwicklungsmodells von SharePoint 2013 verstehen. Jede Art von SharePoint-Add-In enthält eine Add-In-Manifestdatei und wird in ein Add-In-Paket integriert, das Sie auf einer SharePoint 2013-Website bereitstellen. Bei der Entwicklung jeder Art von Add-In müssen Sie außerdem verschiedene Aspekte bezüglich der Authentifizierung und Autorisierung, des Datenzugriffs und der Benutzerfreundlichkeit berücksichtigen. Die Artikel in Tabelle 2 machen Sie mit diesen Themen vertraut und erklären die Implikationen, die diese für jede Art von haben.




**Tabelle 2. Grundlegende Konzepte für die Arbeit mit SharePoint-Add-Ins**


|**Thema**|**Beschreibung**|
|:-----|:-----|
| [Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |In diesem Artikel werden die Grundkonzepte in Bezug auf den Erwerb der notwendigen Berechtigungen für die Arbeit mit SharePoint 2013-Ressourcen erläutert.  <br/> |
| [Hinweise zur App-Manifeststruktur und zum Paket eines SharePoint-Add-Ins](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |In diesem Artikel wird erklärt, welche Funktion Add-In-Manifeste erfüllen und wie Add-In-Pakete erstellt werden.  <br/> |
| [Erstellen von UX-Komponenten in SharePoint 2013](create-ux-components-in-sharepoint-2013.md) <br/> |In diesem Artikel wird beschrieben, wie Sie SharePoint-Add-Ins benutzerfreundlich gestalten können.  <br/> |
| [Arbeiten mit externen Daten in SharePoint 2013](work-with-external-data-in-sharepoint-2013.md) <br/> |In diesem Artikel werden die Datenzugriffsoptionen und -techniken erklärt, die für die verschiedenen Arten von SharePoint-Add-Ins zur Verfügung stehen.  <br/> |
| [Lizenzieren von Office- und SharePoint-Add-Ins](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |In diesem Artikel wird das Add-In-Lizenzframework für Office- und SharePoint-Add-Ins erläutert.  <br/> |
 

## Zusammenfassung: Erstellen anspruchsvoller Add-Ins für SharePoint durch die Integration von Funktionen
<a name="bk_integrate"> </a>

Wenn Sie mit den Funktionen und Features von SharePoint-Add-Ins vertraut sind, können Sie komplexere Add-Ins erstellen, indem Sie die Funktionen und Elemente so kombinieren, dass Ihre Anforderungen erfüllt werden. Die Artikel in Tabelle 3 zeigen, wie Funktionen integriert und SharePoint-Add-Ins mit einem größeren Funktionsumfang erstellt werden.




**Tabelle 3. Fortgeschrittene Konzepte in SharePoint-Add-Ins**


|**Thema**|**Beschreibung**|
|:-----|:-----|
| [Gewusst wie: Erstellen eines von einem Anbieter gehosteten Add-Ins, das eine benutzerdefinierte SharePoint-Liste und einen benutzerdefinierten Inhaltstyp enthält](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |In diesem Artikel wird erklärt, wie Sie SharePoint-Add-Ins erstellen, die in der Cloud gehostet werden und benutzerdefinierte SharePoint-Listen und Inhaltstypen enthalten.  <br/> |
 

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Tools und Umgebungen für die Entwicklung von Add-Ins für SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)


-  [Entwerfen von SharePoint-Add-Ins](design-sharepoint-add-ins.md)


-  [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md)


-  [Add-In für SharePoint-Beispielpaket](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)



