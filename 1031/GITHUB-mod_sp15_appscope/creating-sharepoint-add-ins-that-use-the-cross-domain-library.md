---
title: Erstellen von SharePoint-Add-Ins, die die domänenübergreifende Bibliothek verwenden
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# Erstellen von SharePoint-Add-Ins, die die domänenübergreifende Bibliothek verwenden
Informationen zur SharePoint domänenübergreifenden JavaScript-Bibliothek.
Es gibt einige Szenarien, in denen weder das Autorisierungssystem mit niedriger Vertrauensebene noch das besonders vertrauenswürdige Autorisierungssystem von einem SharePoint-Add-In verwendet werden kann oder sie keine gute Wahl als einzige Methode des Add-Ins für die Autorisierung von SharePoint-Ressourcen sind. Beispiele:
  
    
    


- Die Remotekomponenten des SharePoint-Add-Ins sind nicht lokal vorhanden, eine Unternehmensfirewall blockiert jedoch die Server-zu-Server-Kommunikation zwischen SharePoint und ACS, sodass das System mit niedriger Vertrauensebene nicht verwendet werden kann.
    
  
- Das SharePoint-Add-In ist als einseitige Webanwendung ausgelegt, die für Datenvorgänge mit SharePoint clientseitiges JavaScript verwendet.
    
  
- Das SharePoint-Add-In basiert hauptsächlich auf Server-zu-Server-Aufrufen, um auf SharePoint-Daten zuzugreifen (und ist vom System mit niedriger Vertrauensebene oder vom besonders vertrauenswürdigen System autorisiert), es muss jedoch durch einige JavaScript-Aufrufe ergänzt werden. Eine zum Großteil aus Grafiken bestehende Seite kann beispielsweise JavaScript verwenden, um kleinere Aktualisierungen an den angezeigten Daten vorzunehmen, ohne die komplette Seite neu laden zu müssen.
    
  

 [Aus Sicherheitsgründen](http://msdn.microsoft.com/de-de/library%28d=robot%29/cc709423(d=robot,l=de-de,v=vs.85).aspx) können Browser jedoch kein JavaScript verwenden, der auf einer Domäne gehostet wird, um auf Ressourcen in einer anderen Domäne zuzugreifen, es ist deshalb ein spezielles Verfahren erforderlich, damit Remote-JavaScript auf SharePoint-Ressourcen zugreifen kann. Mit der SharePoint domänenübergreifenden JavaScript-Bibliothek wird die Verwendung des Verfahrens durch Ihre Remotewebanwendung erleichtert.
  
    
    


> [!HINWEIS]
> Die domänenübergreifende Bibliothek wird außerdem verwendet, damit auf die Daten in umgekehrter Richtung zugegriffen werden kann; d. h. JavaScript kann auf einer SharePoint-Seite verwendet werden, um auf Daten in einer Remotedomäne zuzugreifen. Weitere Informationen dazu finden Sie unter  [Zugreifen auf Remotedaten von einer SharePoint-Seite](#ReverseDirection). 
  
    
    


## So funktioniert die Architektur der domänenübergreifenden Bibliothek

Die SharePoint domänenübergreifende Bibliothek ist in der Datei SP.RequestExecutor.js enthalten, die sich in dem virtuellen Ordner "/_layouts/15/" jeder SharePoint-Website befindet. Die Skripts in dieser Datei umfassen eine sichere, bekannte Methode zur Überwindung der Einschränkung des Browsers beim domänenübergreifenden Skripting: Ein iFrame kann mit der  `window.postMessage()`-Funktion mit der übergeordneten Seite kommunizieren, selbst wenn sich die Seite im iFrame in einer anderen Domäne befindet. Datenanforderungen und -antworten werden also mit Aufrufen an  `postMessage()` über die Domänengrenze hinweg übergeben.
  
    
    

> [!VORSICHT]
> Die Funktion  `postMessage()` funktioniert nur mit Browsern, die HTML 5 unterstützen, SharePoint-Add-Ins mit der domänenübergreifenden Bibliothek funktionieren also nicht mit älteren Browsern.
  
    
    

Bei SharePoint wird die domänenübergreifende Bibliothek auf einer Seite der Remotewebanwendung geladen, auf der sie einen verborgenen iFrame erstellt, der eine spezielle Proxyseite von der SharePoint-Domäne hostet. Die Proxyseite ist bereits auf jeder SharePoint-Website vorhanden. Die Bibliothek wird zum Erstellen eines JavaScript Object Notation (JSON)-Objekts verwendet, dass alle benötigten Informationen zum Ausführen eines CRUD-Aufrufs an die REST APIs der SharePoint enthält. Das JSON-Objekt wird mit  `postMessage()` an die Proxyseite übergeben. Auf der Proxyseite, auf der die Bibliothek ebenfalls geladen wird, wird das JSON-Objekt als REST-Aufruf an SharePoint analysiert und wiederhergestellt. Da sich die Proxyseite in der SharePoint-Domäne befindet,lässt der Browser den Aufruf zu.
  
    
    
Die Remotekomponenten des SharePoint-Add-Ins benötigen natürlich dennoch autorisierten Zugriff auf die SharePoint-Ressourcen. Dazu gibt es zwei Möglichkeiten:
  
    
    

- Legen Sie den Prinzipaltyp des Add-Ins auf **RemoteWebApplication** (der Standard für vom Anbieter gehostete Add-Ins) im Add-In-Manifest fest. Wenn das Add-In mit ACS registriert wird, schließt die Registrierung die Domäne der Remotewebanwendung ein. SharePoint vertraut mit ACS registrierten Domänen, auch wenn es in diesem Szenario keine der Tokenübergabeabläufe verwendet, die Teil des serverseitigen Systems mit niedriger Vertrauensebene sind. Ausführliche Informationen zum Registrieren von Add-Ins finden Sie unter [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md). 
    
  
- Bei einem in SharePoint gehosteten Add-In können Sie für den Prinzipaltyp des Add-Ins den Standardwert **Internal** übernehmen. Legen Sie anschließend für das **AllowedRemoteHostUrl**-Attribut des **Internal**-Elements die URL der Remotewebanwendung fest, siehe folgendes Beispiel.
    
  ```
  
<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>
  ```


> [!HINWEIS]
> Wenn Sie die zweite Option verwenden (ein **Internal**-Add-In-Prinzipal), können Sie nur JavaScript und die domänenübergreifende Bibliothek zum Zugreifen auf SharePoint verwenden. Das SharePoint-Clientobjektmodell ist für **Internal**SharePoint-Add-Ins gesperrt, Sie können also kein duales Autorisierungssystem nutzen, das die domänenübergreifende Bibliothek und Systeme mit niedriger Vertrauensebene oder besonders vertrauenswürdige Systeme verwendet. 
  
    
    

Details zur Verwendung der Bibliothek finden Sie unter  [Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Zugreifen auf Remotedaten von einer SharePoint-Seite
<a name="ReverseDirection"> </a>

Die SharePoint domänenübergreifende Bibliothek kann ebenfalls in die umgekehrte Richtung verwendet werden; d. h., JavaScript auf einer SharePoint-Seite kann die Bibliothek verwenden, um Daten von den Remotekomponenten des Add-Ins abzurufen. Dazu müssen Sie die domänenübergreifende Architektur umkehren: Erstellen Sie eine Proxyseite in der Remotewebanwendung. Die Bibliothek wird von einer SharePoint-Seite aufgerufen, auf der sie einen iFrame erstellt, um die Proxyseite zu hosten. Details zur Verwendung der Bibliothek auf diese Weise finden Sie unter  [Erstellen einer benutzerdefinierten Proxyseite für die domänenübergreifende Bibliothek in SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

## Inhalt dieses Abschnitts
<a name="ReverseDirection"> </a>


-  [Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [Arbeiten mit der domänenübergreifenden Bibliothek in verschiedenen Internet Explorer-Sicherheitszonen in Add-Ins für SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  

## Zusätzliche Ressourcen
<a name="ReverseDirection"> </a>


-  [Lösen von domänenübergreifenden Problemen in SharePoint-Add-Ins](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)
    
  

