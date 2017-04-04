---
title: Bereitstellen und Installieren von SharePoint-Add-Ins Methoden und Optionen
ms.prod: SHAREPOINT
ms.assetid: d15a74a7-3c10-485a-9885-7ef11aaa0d90
---


# Bereitstellen und Installieren von SharePoint-Add-Ins: Methoden und Optionen
Erfahren Sie mehr über die Methoden zum Veröffentlichen, installieren und Deinstallieren eines SharePoint-Add-In.
## Veröffentlichen im Office Store oder im Add-In-Katalog eines Unternehmens
<a name="MarketOrCatalog"> </a>

Sie können Ihr SharePoint-Add-In entweder in den öffentlichen Office Store oder in den privaten Add-In-Katalog eines Unternehmens hochladen. Ein privater Add-In-Katalog ist eine dedizierte Websitesammlung in einer SharePoint 2013-Webanwendung (oder einer SharePoint Online-Mandanteneinheit), in der Dokumentbibliotheken für SharePoint-Add-Ins und Office-Add-Ins gehostet werden. Wenn der Katalog in eine eigene Websitesammlung gestellt wird, ist es für den Webanwendungsadministrator oder den Mandantenadministrator einfacher, die Berechtigungen für den Katalog einzuschränken. 
  
    
    
Wenn das Add-In in den Office Store hochgeladen wird, führt Microsoft Validierungsprüfungen aus. Es überprüft beispielsweise, ob das Add-In-Manifest-Markup gültig und vollständig ist, und stellt sicher, dass hinzugefügte SharePoint-Lösungspakte (WSP-Dateien) keine unzulässigen Elemente oder Features mit einem breiteren Bereich als **Web** enthalten. Der Paketinhalt wird außerdem auf unzulässige Inhalte untersucht. Wenn das Add-In alle Prüfungen besteht, wird das Add-In-Paket in eine Datei verpackt und von Microsoft signiert.
  
    
    

> [!HINWEIS]
> Wenn Sie Ihr Add-In mit Microsoft Office-Entwicklertools für Visual Studio entwickeln und bereitstellen, wird das Add-In direkt auf der SharePoint-Zieltestwebsite installiert. Da es nicht über den Office Store übergeben wird, findet die oben beschriebene Überprüfung nicht statt. 
  
    
    

Ein SharePoint-Add-In in den Add-In-Katalog eines Unternehmens zu laden ist so einfach, wie eine Datei in eine SharePoint Foundation-Dokumentbibliothek zu laden. Sie füllen ein Popup-Formular aus, in dem Sie die lokale URL des Add-In-Pakets und weitere Informationen angeben, wie z. B. den Namen des Add-Ins. Wenn das Add-In in den Add-In-Katalog eines Unternehmens geladen wird, werden ähnliche Überprüfungen durchgeführt. Dabei werden Add-Ins, die die Prüfungen nicht bestehen, als ungültig gekennzeichnet oder im Katalog deaktiviert. 
  
    
    
Mandantenadministratoren und SharePoint 2013-Webanwendungsadministratoren können SharePoint-Add-Ins im Office Store kaufen. Zum Öffnen des Office Store wählt der Administrator die Option **Add-In hinzufügen** auf der Seite **Websiteinhalt** und dann **SharePoint Store** auf der Seite **Eigene Add-Ins** aus. Damit wird die Seite **SharePoint Store** geöffnet, über die der Administrator mehr über SharePoint-Add-Ins erfahren kann, die von Anbietern angeboten werden. (Dies ist auch auf office.com möglich.) Add-Ins, die eine Voraussetzung erfordern, die auf der Anwendungswebsite oder Mandanteneinheit des Administrators nicht installiert ist, werden abgeblendet angezeigt und sind im Add-In-Store nicht verfügbar. Wenn ein Add-In beispielsweise Suche-Dienste erfordert und diese nicht installiert sind, wird das Add-In abgeblendet angezeigt. Administratoren können die Liste der Add-Ins sortieren, filtern und durchsuchen, Informationen über Add-Ins lesen, Add-In-Rezensionen anzeigen und Lizenzen für ein Add-In erwerben.
  
    
    
Wenn ein Administrator eine Lizenz erwerben möchte, muss er die Kaufbestimmungen akzeptieren und den für das Add-In erforderlichen Berechtigungen zustimmen, wie z. B. Lesezugriff auf Listen oder Vollzugriff auf die Websitesammlung. 
  
    
    
Wenn eine oder mehrere Lizenzen für ein Add-In erworben werden, werden die Lizenzen in die Webanwendung oder Mandanteneinheit heruntergeladen. Das Add-In wird beim Lizenzerwerb nicht automatisch heruntergeladen und installiert, obwohl der Administrator die Option hat, die Installation mit dem Lizenzerwerb zu kombinieren.
  
    
    
Benutzer installieren Apps von der Seite **Your Add-Ins (Eigene Add-Ins)**. Diese Seite enthält eine zusammengeführte Liste mit folgenden Komponenten:
  
    
    

- SharePoint-Add-Ins aus dem Unternehmens-Add-In-Katalog der Webanwendung (oder des Mandanten).
    
  
- SharePoint-Add-Ins aus dem Office Store, für die das Unternehmen oder der Mandant bereits eine Website-Lizenz oder eine dem Benutzer zugewiesene Lizenz besitzt.
    
  
Alle Add-Ins, die der Benutzer sofort installieren kann, sind ausgeführt, und mit einigen wenigen Ausnahmen werden nur Add-Ins aufgeführt, die der Benutzer sofort installieren kann. Benutzer können die auf der Seite enthaltenen Add-Ins filtern, um dem Unternehmens-Add-In-Katalog nur Add-Ins hinzuzufügen. Wenn ein Add-In installiert ist, wird es in der Liste der Add-Ins auf der Seite **Websiteinhalt** der Website angezeigt, auf der es installiert ist.
  
    
    

## Installieren von SharePoint-Add-Ins
<a name="Installing"> </a>

Websitebesitzer installieren SharePoint-Add-Ins von der Seite **Eigene Add-Ins**, wie weiter oben in diesem Thema beschrieben. Die Installation erstellt eine Instanz des Add-Ins. Weitere Informationen über das Installieren von Add-Ins finden Sie unter  [Hinzufügen von SharePoint-Add-Ins zu einer SharePoint 2013-Website](https://technet.microsoft.com/de-de/library/fp161231.aspx). 
  
    
    

> [!HINWEIS]
> Manchmal kann ein vorübergehender Verlust der Netzwerkverbindung die Installation blockieren. Wenn die Installation aus irgendeinem Grund fehlschlägt, führt die Installationsinfrastruktur drei Wiederholungsversuche durch. Wenn sie nicht erfolgreich sind, wird dies auf der Benutzeroberfläche angezeigt. Benutzer können die Installation zu einem späteren Zeitpunkt erneut versuchen. 
  
    
    


## Deinstallieren von SharePoint-Add-Ins
<a name="Uninstalling"> </a>

Websitebesitzer können eine Instanz eines SharePoint-Add-In über die SharePoint-Benutzeroberfläche deinstallieren. Die Deinstallation einer Instanz eines SharePoint-Add-Ins ist sauber. Das bedeutet, dass alle vom Add-In installierten Komponenten deinstalliert werden. 
  
    
    
Komponenten jedoch, die von einem Add-In verwendet, die aber separat von der Installation des Add-Ins installiert sind, werden nicht entfernt. Nehmen Sie beispielsweise an, ein Add-In verfügt über eine Remotewebseite mit Schaltflächen, die Listen im Hostweb erstellen. Durch die Deinstallation des Add-Ins wird die Kachel des Add-Ins von der Seite **Websiteinhalt** entfernt, wodurch wiederum die Remoteseite im Wesentlichen für Endbenutzer nicht zugänglich oder nicht benutzbar wird, die Listen, die mit dem Add-In erstellt wurden, werden jedoch nicht entfernt. SharePoint bewahrt keine Aufzeichnung auf, welche Listen im Hostweb mit dem Add-In und welche von Benutzern in der SharePoint-Benutzeroberfläche erstellt wurden, deshalb können mit dem Add-In erstellte Listen nicht gelöscht werden. Im Allgemeinen ist dieses Verhalten wünschenswert, da die Listen möglicherweise Daten enthalten, die für Benutzer auch nach Entfernen des Add-Ins, das die Listen erstellt hat, nützlich bleiben können.
  
    
    
Wenn das SharePoint-Add-In ein Add-In-Web umfasst, wird das Add-In-Web gelöscht. Dies ermöglicht eine sauberere Deinstallation als die systematische Deaktivierung von Funktionen und das Rückgängigmachen der Bereitstellung der Internen WSP-Datei des Add-Ins.
  
    
    

> [!HINWEIS]
> Wenn ein Benutzer ein Add-In entfernt, wird es in die erste Phase des Papierkorbs verschoben. Wird es dort gelöscht, wird es in die zweite Phase des Papierkorbs verschoben. Wird es aus der zweiten Phase des Papierkorbs gelöscht, dann wird das Add-In vollständig deinstalliert und kann nicht wiederhergestellt werden. 
  
    
    

Die Berechtigungen des Add-Ins werden ebenfalls abgerufen, wenn es entfernt (recycelt) wird, entsprechend der folgenden Regeln:
  
    
    

- Webgültigkeitsberechtigungen werden immer abgerufen.
    
  
- Falls es keine weitere Instanz des Add-Ins in der Websitesammlung gibt, werden die Berechtigungen für die Websitesammlung ebenfalls abgerufen.
    
  
- Falls es keine weitere Instanz des Add-Ins im Website-Abonnement (Mandatschaft) gibt, werden die Mandantenberechtigungen ebenfalls abgerufen.
    
  
Der Webdienst  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx), falls er im App-Manifest des Add-Ins registeriert ist, wird zu Beginn des Deinstallationsvorgangs ausgeführt (tritt auf, wenn das Add-In aus dem endgültigen Papierkorb entfernt wird). Sie sollten den Webdienst  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) installiert haben, wenn Sie gleichzeitig auch [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) installiert haben. Sie sollten auch den Dienst [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) entwerfen, um eventuell Schritte im [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx)-Dienst rückgängig machen zu können. Weitere Informationen finden Sie unter  [Behandeln von Ereignissen in SharePoint-Add-Ins](handle-events-in-sharepoint-add-ins.md).
  
    
    

## Weitere Ressourcen
<a name="SP15deployinstallapps_addlresources"> </a>


-  [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md)
    
  
-  [Wichtige Aspekte der Architektur und Entwicklungslandschaft von Add-Ins für SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Aktualisierungsverfahren für Add-Ins für SharePoint](sharepoint-add-ins-update-process.md)
    
  

