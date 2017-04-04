---
title: Arbeiten mit externen Daten in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# Arbeiten mit externen Daten in SharePoint 2013
Hier finden Sie Ressourcen und Anleitungen für den Zugriff auf und die Bearbeitung von Daten mit JavaScript auf Seiten in SharePoint-Add-Ins.
## Verwenden von JavaScript in SharePoint-Add-Ins
<a name="SP15Workdata_Working"> </a>

In Ihren SharePoint-Add-Ins müssen Sie häufig Daten abrufen und bearbeiten, die über eine Remote-Webanwendung oder einen Dienst einer SharePoint-Seite oder Komponente verfügbar gemacht werden. Da benutzerdefinierter Corde auf den SharePoint-Servern nicht zulässig ist, muss Ihr Add-In zu diesem Zweck JavaScript verwenden. Das Modell für SharePoint-Add-Ins stellt mehrere Optionen für den Zugriff auf Remotedaten und -dienste bereit.
  
    
    

### Verwenden Sie die SharePoint domänenübergreifende JavaScript-Bibliothek, um auf externe Daten zuzugreifen

Sie können mit der domänenübergreifenden Bibliothek auf Daten in Ihrer Remote-Webanwendung zugreifen, wenn Sie eine benutzerdefinierte Proxyseite bereitstellen, die in der Remoteinfrastruktur gehostet wird. Als Entwickler sind Sie für die Implementierung der benutzerdefinierten Proxyseite zuständig und Sie müssen mit der benutzerdefinierten Logik umgehen, wie z. B. dem Authentifizierungsmechanismus, falls einer vorhanden ist, zu der Remoteanwendung. Verwenden Sie die domänenübergreifende Bibliothek, wenn die Kommunikation zwischen der Remotedatenquelle und der SharePoint-Seite auf Clientebene erfolgen soll.
  
    
    
Details zur Verwendung der Bibliothek auf diese Weise finden Sie unter  [Erstellen einer benutzerdefinierten Proxyseite für die domänenübergreifende Bibliothek in SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

> **HINWEIS**
> Die domänenübergreifende Bibliothek SharePoint kann auch in die andere Richtung verwendet werden; d. h. JavaScript auf Remotewebseiten kann sie verwenden, um auf Daten aus SharePoint zuzugreifen. Weitere Informationen zu dieser Verwendung der Bibliothek finden Sie unter  [Erstellen von SharePoint-Add-Ins, die die domänenübergreifende Bibliothek verwenden](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md). 
  
    
    


### Verwenden Sie den SharePoint-Webproxy, um auf externe Daten zuzugreifen

Sie können den Webproxy verwenden, der in dem JavaScript-Clientobjektmodell für den Zugriff auf Remotedaten verfügbar gemacht wird. (Der Proxy ist auch in dem clientseitigen .NET-Objektmodell (CSOM) verfügbar, Sie können das Objektmodell jedoch nicht in Code verwenden, der auf den SharePoint-Servern ausgeführt wird). Wenn Sie den Webproxy verwenden, senden Sie die ursprüngliche Anforderung an SharePoint. SharePoint fordert wiederum die Daten des angegebenen Endpunkts an und gibt die Antwort zurück an Ihre Seite. Verwenden Sie den Webproxy, wenn die Kommunikation zwischen der Remotedatenquelle und der SharePoint-Seite auf Serverebene erfolgen soll.
  
    
    
Details zum Verwenden des Proxys finden Sie unter  [Abfragen eines Remotediensts mithilfe des Webproxys in SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).
  
    
    

## Zusätzliche Ressourcen
<a name="SP15Workdata_AddRes"> </a>


-  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)
    
  

