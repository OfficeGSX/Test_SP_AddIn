---
title: Veröffentlichen von SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# Veröffentlichen von SharePoint-Add-Ins
In diesem Thema geht es um Artikel und Ressourcen, die Ihnen bei der Veröffentlichung Ihrer SharePoint-Add-Ins helfen.
## Erste Schritte zur Veröffentlichung Ihrer Add-Ins
<a name="bk_gettingstarted"> </a>

Nachdem Sie Ihr SharePoint-Add-In fertig gestellt haben, besteht der letzte Schritt darin, das Add-In Ihren Benutzern verfügbar zu machen. Zu diesem Zweck können Sie das Add-In an zwei verschiedenen Orten veröffentlichen:
  
    
    

- **Im öffentlichen Office Store.** Wenn Sie Ihr Add-In im Office Store öffentlich anbieten, steht es allen Benutzern von SharePoint-Bereitstellungen zur Verfügung.
    
  
- **Im internen Add-In-Katalog einer Organisation.** Wenn Sie Ihre Add-Ins im internen Add-In-Katalog einer Organisation veröffentlichen, der in Ihrer SharePoint-Bereitstellung gehostet wird, machen Sie sie für Benutzer verfügbar, die auf diese SharePoint-Bereitstellung zugreifen können.
    
  
Informationen darüber, wie Sie Ihr Add-In zur Veröffentlichung mit Visual Studio 2012 verpacken, finden Sie unter  [Veröffentlichen von SharePoint-Add-Ins mithilfe von Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md).
  
    
    

### Veröffentlichen im Office Store

Zu Veröffentlichen eines Add-Ins im Office Store müssen Sie sich zunächst  [als Microsoft-Entwickler registrieren](https://sellerdashboard.microsoft.com/Registration). 
  
    
    
Wenn Sie ein Add-In zur Veröffentlichung in den Office Store hochladen, führt Microsoft eine Reihe von Überprüfungen durch, um sicherzustellen, dass Ihr Add-In den Add-In-Inhalts- und -verhaltensrichtlinien entspricht. Beispielsweise wird geprüft, ob das Add-In-Manifestmarkup gültig und vollständig ist, und sichergestellt, dass im Add-In enthaltene SharePoint-Lösungspakete (WSP-Dateien) keine unzulässigen Elemente oder SharePoint-Features mit einem Geltungsbereich enthalten, der über "Web" hinausgeht. Das Paket wird zudem auf unzulässigen Inhalt geprüft. Wenn das Add-In alle Tests besteht, wird es in eine Datei verpackt und von Microsoft signiert.
  
    
    
Wenn Sie Ihr Add-In zur Veröffentlichung in den Office Store hochladen, können Sie Lizenzbedingungen für Benutzer anbieten, die das Add-In herunterladen. Die Add-In-Lizenz definiert:
  
    
    

- ob Sie Ihr Add-In kostenlos, als Testversion oder zum Kauf anbieten;
    
  
- ob Ihr Add-In auf Pro-Benutzer- oder Websitebasis erworben werden kann.
    
  
SharePoint erzwingt keine Lizenzbedingungen für die Add-In-Verwendung, sondern bietet ein Lizenzierungsframework, das es Ihnen ermöglicht, Codelogik in Ihr Add-In einzubinden und die von Ihnen gewünschten Lizenzierungsbeschränkungen durchzusetzen. Beispielsweise können Sie Codelogik in Ihr Add-In aufnehmen, die Benutzern Zugriff auf bestimmte Add-In-Features gewährt, sofern sie über eine kostenpflichtige Lizenz und nicht nur eine Testlizenz verfügen. Weitere Informationen finden Sie unter  [Lizenzieren von Office- und SharePoint-Add-Ins](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx).
  
    
    

### Veröffentlichen in einem Add-In-Katalog

Wenn Sie SharePoint-Add-Ins für die Verwendung durch Ihr eigenes Unternehmen oder einen spezifischen Unternehmensclient und nicht die Allgemeinheit erstellen, möchten Sie Ihr Add-In wahrscheinlich in einem von SharePoint gehosteten internen Add-In-Katalog veröffentlichen. Ein privater Add-In-Katalog ist eine dedizierte Websitesammlung in einer SharePoint 2013-Webanwendung (oder einer SharePoint Online-Instanz), die als Host für Dokumentbibliotheken für SharePoint-Add-Ins und Office-Add-Ins dient. Wenn der Katalog in eine eigene Websitesammlung eingefügt wird, ist es für den Webanwendungsadministrator oder Mandantenadministrator einfacher, Zugriffsberechtigungen für den Katalog zu begrenzen.
  
    
    
Das Hochladen eines SharePoint-Add-In in einen unternehmenseigenen Add-In-Katalog ist genauso einfach wie das Hochladen irgendeiner Datei in eine SharePoint-Dokumentbibliothek. Sie brauchen nur die lokale URL des Add-In-Pakets und ein paar andere Informationen, wie den Namen des Add-Ins, in ein Popupformular einzugeben. Wenn Sie das Add-In in einen Add-In-Katalog hochladen, werden ähnliche Kontrollen durchgeführt, und Add-Ins, die die Prüfung nicht bestanden haben, werden im Katalog als ungültig markiert oder deaktiviert.
  
    
    

## Entscheiden, wo Ihre SharePoint-Add-In veröffentlicht werden soll
<a name="bk_decide"> </a>

In der folgenden Tabelle wird die Veröffentlichung im Office Store mit der Veröffentlichung in einem Add-In-Katalog verglichen. Zudem wird auf kritische Punkte hingewiesen, die bei der Entscheidung, wo ein Add-In veröffentlicht werden soll, zu berücksichtigen sind. Wir empfehlen, die Veröffentlichung des Add-Ins bereits vor dem Entwurf und der Entwicklung zu planen, da sich der Ort der Veröffentlichung in manchen Fällen auf den Entwurf und die Entwicklung des Add-Ins auswirkt.
  
    
    

**Tabelle 1: Überlegungen zum Ort der Veröffentlichung eines Add-Ins**


|**Office Store**|**Add-In-Katalog**|
|:-----|:-----|
|Das Add-In ist öffentlich zugänglich.  <br/> |Das Add-In steht Benutzern zur Verfügung, die auf diese SharePoint-Bereitstellung zugreifen können.  <br/> |
|Ein Lizenzierungsframework ist verfügbar.  <br/> |Es steht kein Lizenzierungsframework zur Nutzung zur Verfügung.  <br/> |
|Das Add-In-Paket wird von Microsoft auf die Einhaltung technischer und Inhaltsrichtlinien geprüft.  <br/> |Die Überprüfung des Add-In-Pakets wird beim Hochladen von SharePoint durchgeführt.  <br/> |
|Sie müssen beim Microsoft Seller Dashboard angemeldet sein, um Add-Ins hochladen zu können.  <br/> |Es ist keine Registrierung bei Microsoft erforderlich.  <br/> |
   

## Weitere Ressourcen
<a name="bk_addresources"> </a>


-  [Erstellen oder Aktualisieren von Client-IDs und geheimen Clientschlüsseln im Verkäuferdashboard](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)
    
  
-  [Verwenden des Verkäuferdashboards zum Übermitteln von Office- und SharePoint-Add-Ins und Office 365-Apps an den Office Store](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)
    
  
-  [Validierungsrichtlinien für an den Office Store übermittelte Apps und Add-Ins (Version 2.0)](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)
    
  
-  [Erste Schritte beim Erstellen von Apps für Office und SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)
    
  
-  [Lizenzieren von Office- und SharePoint-Add-Ins](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)
    
  
-  [Bereitstellen und Installieren von SharePoint-Add-Ins: Methoden und Optionen](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Mandantschaften und Bereitstellungsbereiche von Add-Ins für SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  
-  [Veröffentlichen von SharePoint-Add-Ins mithilfe von Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md)
    
  
-  [Veröffentlichen von Add-Ins für den Office Store](http://social.msdn.microsoft.com/Forums/de-de/officestore)
    
  

