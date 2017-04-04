---
title: Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013
Hier erhalten Sie einen Überblick über die Authentifizierung und Autorisierung in SharePoint, die verwendet werden, um Anforderungen eines SharePoint-Add-Ins für den Zugriff auf SharePoint-Ressourcen zu autorisieren.
**Sehen Sie sich ein Video über Add-In-Identität an.**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## Add-In-Authentifizierung in SharePoint
<a name="AuthN"> </a>

Wenn sich ein  *Benutzer*  bei SharePoint anmeldet, wird das Sicherheitstoken des Benutzers überprüft. Das Token wird von einem Identitätsanbieter ausgestellt. SharePoint unterstützt mehrere Arten der Benutzerauthentifizierung. Weitere Informationen finden Sie unter [Authentifizierung, Autorisierung und Sicherheit in SharePoint 2013](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx).
  
    
    
SharePoint-Add-Ins sind außerdem Sicherheitsprinzipale, die authentifiziert und autorisiert werden müssen. Add-Ins können auf verschiedene Weise authentifiziert und autorisiert werden. Weitere Informationen finden Sie unter  [Drei Autorisierungssysteme für SharePoint-Add-Ins](three-authorization-systems-for-sharepoint-add-ins.md). 
  
    
    

## Autorisierungsrichtlinien: Nur-Benutzer-Richtlinie, Benutzer-und Add-In-Richtlinie oder Nur-Add-In-Richtlinie
<a name="AuthZ"> </a>

Die Autorisierung überprüft, ob ein authentifizierter Antragsteller (ein Add-In oder ein Benutzer oder beide) berechtigt ist, bestimmte Vorgänge durchzuführen oder auf bestimmte Ressourcen zuzugreifen (z. B. eine Liste oder einen SharePoint-Dokumentordner).
  
    
    
SharePoint verwendet drei Arten von Autorisierungsrichtlinien. Die Nur-Benutzer-Richtlinie erfordert, dass nur der Aufruf an SharePoint eine authentifizierte Benutzeridentität enthält. Die Nur-Add-In-Richtlinie erfordert, dass der Aufruf nur eine authentifizierte Add-In-Identität enthält. Die Benutzer-und-Add-In-Richtlinie erfordert, dass der Aufruf beide Arten von authentifizierten Identitäten enthält. Wenn ein Benutzer auf SharePoint-Ressourcen über die SharePoint-Benutzeroberfläche statt über ein Add-In zugreift, verwendet SharePoint die Nur-Benutzer-Richtlinie. Bei Aufrufen von einem SharePoint-Add-In verwendet SharePoint jedoch immer entweder die Nur-Add-In- oder Benutzer-und-Add-In-Richtlinie. Das SharePoint-Add-In bestimmt anhand des Zugriffstokens, das es in seine Anforderung an SharePoint einschließt, welche Richtlinie verwendet wird. Wenn eine Benutzer-und-Add-In-Anforderung gestellt wird, verlangt SharePoint, dass sowohl das Add-In als auch der Benutzer über die Berechtigung für die Ressource verfügen, auf die das Add-In zugreift. Im Fall einer Nur-Add-In-Anforderung verlangt SharePoint, dass das Add-In über die Berechtigung für die Ressource verfügt, es spielt aber keine Rolle, ob der Benutzer über die Berechtigung verfügt oder nicht. (Ein SharePoint-Add-In kann Nur-Add-In-Anforderungen nur durchführen, wenn es zuvor die Berechtigung dafür erhalten hat, normalerweise bei der Installation.)
  
    
    
Weitere Informationen über Autorisierungsrichtlinien und ihre Funktionsweise finden Sie unter  [Add-In-Autorisierungsrichtlinientypen in SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Add-In-Berechtigungen und Add-In-Berechtigungsanforderungsbereiche
<a name="Permissions"> </a>

Der Entwickler eines SharePoint-Add-Ins muss über die Add-In-Manifestdatei die Berechtigungen angeben, die ein Add-In für SharePoint-Ressourcen außerhalb des Add-In-Webs benötigt. (Das Add-In erhält automatisch die Vollzugriffsberechtigung auf das gesamte Add-In-Web). Wenn das Add-In darauf ausgelegt ist, direkt aus SharePoint gestartet zu werden, fordert die Installationsinfrastruktur des Add-Ins den Benutzer, der das Add-In installiert hat, dazu auf, dem Add-In die benötigten Berechtigungen zu gewähren oder zu verweigern. Nachdem die Berechtigungen gewährt wurden, können Benutzer der Website das Add-In verwenden, ohne ihm die Berechtigungen erneut zu gewähren. Wenn das Add-In jedoch darauf ausgelegt ist, außerhalb von SharePoint gestartet zu werden, das heißt, das Add-In ist nicht in SharePoint installiert, dann fordert SharePoint den Benutzer, der das Add-In ausführt, dazu auf, die erforderlichen Berechtigungen bei jeder Ausführung des Add-Ins zu gewähren. Add-Ins auf mobilen Geräten und Office-Add-Ins sind Beispiele für Add-Ins, die auf SharePoint zugreifen können, aber nicht darin installiert sind.
  
    
    
Nur ein Websitebesitzer kann ein SharePoint-Add-In auf einer SharePoint-Website installieren (sofern keine benutzerdefinierte Rolle erstellt wurde, die über Add-In-Installationsrechte verfügt). Ein Benutzer kann einem Add-In nur die Berechtigungen erteilen, über die er selbst verfügt. Ein Benutzer kann also kein Add-In installieren, das Berechtigungen erfordert, über die der Benutzer nicht verfügt. Auf ähnliche Weise kann ein Benutzer kein Add-In ausführen und extern starten, das Berechtigungen benötigt, über die der Benutzer nicht verfügt. Wenn ein SharePoint-Add-In jedoch in SharePoint installiert ist, kann es die Berechtigung anfordern, Nur-Add-In-Aufrufe durchzuführen. Ein Add-In, das über diese Berechtigung verfügt, kann auf eine Art und Weise auf SharePoint zugreifen, für die der Benutzer keine Berechtigung hat.
  
    
    
Zudem können SharePoint Online-Mandantenadministratoren oder SharePoint-Farmadministratoren dem Add-In Berechtigungen erteilen oder entziehen.
  
    
    
In der Add-In-Manifestdatei gibt ein SharePoint-Add-In die Berechtigungen an, die es für eine ordnungsgemäße Funktionsweise benötigt. In den Berechtigungsanforderungen sind sowohl die Rechte angegeben, die ein Add-In benötigt, als auch der Bereich, in dem die Rechte benötigt werden. Bereiche geben an, an welcher Stelle in der SharePoint-Hierarchie eine Berechtigungsanforderung angewendet wird. SharePoint unterstützt vier unterschiedliche Inhaltsbereiche: Mandant, Websitesammlung, Website und Liste. Außerdem gibt es spezielle Bereiche zum Ausführen von Suchabfragen, Zugreifen auf Taxonomiedaten, Features für soziale Netzwerke, Microsoft Business Connectivity Services (BCS)-Features und Project Server 2013-Features. Weitere Informationen zu Add-In-Berechtigungen finden Sie unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Wann ist die Verwendung von OAuth erforderlich?
<a name="FileName_uniquekeyword4"> </a>

Möglicherweise haben Sie schon gehört, dass OAuth 2.0 eine wichtige Rolle bei der Authentifizierung und Autorisierung von SharePoint-Add-Ins spielt. Das ist tatsächlich der Fall, aber nicht unbedingt als Teil der Autorisierungsfunktion für jedes SharePoint-Add-In. Wenn Sie die Erstellung eines SharePoint-Add-Ins planen, das in einer Remotewebanwendung ausgeführt wird und über serverseitigen Code zurück an SharePoint kommuniziert, müssen Sie OAuth verwenden. Wenn die Remotewebanwendung nicht lokal vorhanden ist, würden Sie das  [Autorisierungssystem mit niedriger Vertrauensebene](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) verwenden, bei dem Azure ACS der Aussteller des Zugriffstokens ist. Bei einer lokalen Version würden Sie normalerweise das [besonders vertrauenswürdige System](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) verwenden, bei dem das Zugriffstoken vom Add-In selbst und einem digitalen Zertifikat ausgestellt werden.
  
    
    
Sie würden OAuth nicht verwenden, um einen Aufruf von JavaScript auf einer Seite im Add-In selbst oder von einer Remotewebseite über  [die domänenübergreifende Bibliothek](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md) durchzuführen. Weitere Informationen zur domänenübergreifenden Bibliothek finden Sie unter [Erstellen von SharePoint-Add-Ins, die die domänenübergreifende Bibliothek verwenden](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md).
  
    
    

## Weitere Informationsquellen
<a name="Filename_AdditionalResources"> </a>


-  [Drei Autorisierungssysteme für SharePoint-Add-Ins](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [SharePoint-Add-Ins](sharepoint-add-ins.md)
    
  

