---
title: Aktualisieren von Remotekomponenten in Add-Ins für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# Aktualisieren von Remotekomponenten in Add-Ins für SharePoint
Aktualisieren Sie die Remotewebanwendungen und Datenbanken in einem SharePoint-Add-In.
## Voraussetzungen für das Aktualisieren der Remotekomponenten einer App für SharePoint
<a name="Prerequistes"> </a>

Sie sollten mit  [Aktualisieren von Add-Ins für SharePoint](update-sharepoint-add-ins.md) und den darin aufgeführten Voraussetzungen und Kernkonzepten vertraut sein.
  
    
    

## Aktualisieren von Remotekomponenten
<a name="UpdateRemote"> </a>

Aufgrund der großen Unterschiede bei Plattformen und Mandantensystemen können zur Aktualisierung der Remotekomponenten größtenteils nur sehr allgemeine Hinweise gegeben werden. Die folgenden zwei Abschnitte bieten eine Orientierung.
  
    
    

### Aktualisieren von Remotekomponenten in einer vom Anbieter gehosteten App
<a name="UpdateProviderHosted"> </a>

Bei einem vom Anbieter gehosteten SharePoint-Add-In aktualisieren Sie die Remotekomponenten mithilfe der für Updates bewährten Vorgehensweisen der Plattform, auf der die Komponenten gehostet werden. Ebenso wie die Remotekomponenten eines vom Anbieter gehosteten Add-Ins separat von der Installation des SharePoint-Add-Ins selbst installiert werden, werden sie auch separat aktualisiert. Hier einige Punkte, die berücksichtigt werden sollten:
  
    
    

- Die aktualisierten Remotekomponenten sollten weiterhin mit allen früheren Versionen des SharePoint-Add-Ins verwendet werden können.
    
  
- Wenn Sie für Ihre Remotekomponenten ein Mandantenisolationssystem implementiert haben, beachten Sie, dass unterschiedliche Mandanten möglicherweise mehrere Versionen Ihrer App verwenden. Außerdem können auf einem bestimmten Mandanten auf verschiedenen SharePoint-Websites sogar unterschiedliche Versionen installiert sein.
    
  
Für ein vom Anbieter gehostetes SharePoint-Add-In, das Microsoft Azure SQL-Datenbank oder SQL Server verwendet, gibt es mehrere Methoden zum Aktualisieren der Datenbank. Lesen Sie zum Einstieg  [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx).
  
    
    

## Nächste Schritte
<a name="Next"> </a>

Kehren Sie zu  [Wichtige Schritte beim Aktualisieren eines Add-Ins](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) zurück, oder rufen Sie direkt einen der folgenden Artikel auf, um zu erfahren, wie Sie die nächste Hauptkomponente Ihres SharePoint-Add-Ins aktualisieren.
  
    
    

-  [Aktualisieren von Add-In-Webkomponenten in SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Aktualisieren von Hostwebkomponenten in SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Erstellen eines Handlers für das Updateereignis in SharePoint-Add-Ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Aktualisieren von Add-Ins für SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Aktualisierungsverfahren für Add-Ins für SharePoint](sharepoint-add-ins-update-process.md)
    
  

