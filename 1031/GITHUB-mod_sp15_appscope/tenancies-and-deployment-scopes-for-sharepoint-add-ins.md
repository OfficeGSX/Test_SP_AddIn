---
title: Mandantschaften und Bereitstellungsbereiche von Add-Ins für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 1ceb3142-a7a5-453e-920f-5f953a79401a
---


# Mandantschaften und Bereitstellungsbereiche von Add-Ins für SharePoint
 Erfahren Sie mehr über das Konzept der Mandanten und die Unterschiede zwischen dem Bereitstellen von SharePoint-Add-Ins mit Mandantenbereich und solchen mit Webbereich.
## Mandanten und Add-In-Bereich
<a name="AppScope"> </a>

Einem SharePoint 2013-Mandanten ist ein Satz von Websitesammlungen in einer SharePoint-Farm oder in SharePoint Online zugeordnet. In SharePoint Online gehören die Websitesammlungen zu einem einzelnen Kundenkonto. In einer SharePoint-Farm kann es sich um alle Websitesammlungen in einer SharePoint-Webanwendung bzw. eine Teilmenge davon oder auch um einen Satz von Websitesammlungen aus mehreren Webanwendungen in der Farm handeln. Ein Mandant kann genau wie eine SharePoint-Webanwendung über einen SharePoint-Add-In-Add-In-Katalog verfügen.
  
    
    
Einem SharePoint-Add-In ist ein Add-In-Bereich zugewiesen. Die beiden möglichen Add-In-Bereiche sindWebbereich oderMandantenbereich. Der Unterschied stellt keine interne Eigenschaft des Add-Ins dar, und Sie als Entwickler können den Bereich Ihres Add-Ins nicht festlegen. Die Entscheidung wird von Mandantenadministratoren als Nebeneffekt der Installationsart des Add-Ins getroffen. Nachdem ein Add-In in den Add-In-Katalog eines Mandanten hochgeladen wurde, steht sie sofort zur Installation auf einzelnen Websites innerhalb des Mandanten zur Verfügung. Auf diese Weise installierte Add-Ins gelten im Webbereich. Mandantenadministratoren haben jedoch auch eine andere Möglichkeit. Sie können eine Batchinstallation des Add-Ins auf einer Untermenge von Websites des Mandanten vornehmen. Auf diese Weise installierte Add-Ins gelten im Mandantenbereich. Der Mandantenadministrator kann mithilfe einer Liste von verwalteten Pfaden, Websitevorlagen oder Websitesammlungen angeben, auf welchen Websites das Add-In installiert wird. Ein per Batch installiertes Add-In kann nur von einem Mandantenadministrator deinstalliert werden. Wenn der Mandantenadministrator das Add-In deinstalliert, wird es von allen Websites des Mandanten deinstalliert. Benutzer können ein per Batch installiertes Add-In nicht auf einzelnen Websites deinstallieren. Dasselbe gilt für die Aktualisierung von per Batch installierten Add-Ins: nur ein Mandantenadministrator kann dies tun, und das Add-In wird per Batchupdate auf allen Websites des Mandanten aktualisiert, auf denen es installiert ist.
  
    
    
Bei der Batchinstallation eines Add-Ins, das ein Add-In-Web enthält, wird nur ein einzelnes Add-In-Web erstellt und von allen Hostwebsites verwendet, auf denen das Add-In installiert wird. Das Add-In-Web befindet sich in der Websitesammlung des Unternehmenskatalogs.
  
    
    
Wenn im Mandanten neue Websitesammlungen erstellt werden, werden Add-Ins, die zuvor per Batch installiert wurden, automatisch in der neuen Websitesammlung installiert.
  
    
    

> [!HINWEIS]
> Der Add-In-Bereich sollte nicht mit dem Featurebereich verwechselt werden. Der Featurebereich bestimmt, wo die Elemente eines Features bereitgestellt werden. Die Möglichkeiten sind **Farm**, **WebApplication**, **Site** (d. h. Websitesammlung) und **Web**. Für Features in SharePoint-Add-Ins (Hostwebfeatures wie auch Features in einer WSP-Datei in einem Add-In-Paket) ist ausschließlich **Web** zulässig.> Der Add-In-Bereich sollte ebenfalls nicht mit den Add-In-Berechtigungsstufen verwechselt werden. SharePoint-Add-Ins können Berechtigungen für alle oder ausgewählte Teile von SharePoint-Inhalten auf der Listen-, Web-, Websitesammlungs- und Mandantenebene anfordern. Durch das Installieren eines Add-Ins mit Mandantenbereich erhält das Add-In weder zusätzliche Berechtigungen noch werden wesentliche Bedingungen des SharePoint-Sicherheitsmodells außer Kraft gesetzt. Weitere Informationen zu Add-In-Berechtigungen finden Sie unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md). 
  
    
    


## Einschränkungen für Add-Ins mit Mandantenbereich
<a name="Tenant"> </a>

Die folgenden Arten von Add-Ins können nicht per Batch installiert werden:
  
    
    

- Add-Ins, die eine benutzerdefinierte Aktion für das Menüband enthalten. (Als Menüelemente bereitgestellte benutzerdefinierte Aktionen sind zulässig.)
    
  
- Add-Ins, die ein Add-In-Webpart enthalten. 
    
  
Denken Sie außerdem daran, dass die Installation mit Mandantenbereich in der Office 365 Small Business Premium-Version von SharePoint Online nicht möglich ist.
  
    
    

## Installieren eines Add-Ins auf mehreren Websites eines Mandanten
<a name="Web"> </a>

Add-Ins aus dem Office Store oder aus einem Add-In-Katalog können von Mandantenadministratoren mit dem folgenden Verfahren auf mehreren Websites eines Mandanten installiert, deinstalliert und aktualisiert werden.
  
    
    

### So installieren Sie ein Add-In für SharePoint auf mehreren Websites


1. Navigieren Sie zur Seite **Websiteinhalte** der Website für den Unternehmenskatalog.
    
  
2. Wählen Sie **Add-In hinzufügen** aus, und installieren Sie das Add-In so, wie Sie bei jeder anderen SharePoint-Website vorgehen würden.
    
  
3. Nachdem das Add-In installiert wurde, zeigen Sie auf der Seite **Websiteinhalte** auf den Link zu dem Add-In. Ein " **...**"-Link wird angezeigt.
    
  
4. Wählen Sie den Link aus. Ein Popup wird angezeigt.
    
  
5. Wählen Sie im Menü den Befehl **Bereitstellung** aus.
    
  
6. Geben Sie auf der Benutzeroberfläche für die Bereitstellung die Websitesammlungen an, in denen das Add-In installiert werden soll. Sie können nach verwalteten Pfaden, Websitevorlagen oder URLs filtern.
    
  
7. Wählen Sie **OK** aus.
    
  

### So deinstallieren Sie ein per Batch installiertes Add-In für SharePoint


1. Navigieren Sie zur Seite **Websiteinhalte** der Website für den Unternehmenskatalog.
    
  
2. Zeigen Sie auf der Seite **Websiteinhalte** auf den Link zu dem Add-In. Ein " **...**"-Link wird angezeigt.
    
  
3. Wählen Sie den Link aus. Ein Popup wird angezeigt.
    
  
4. Wählen Sie im Popup **Entfernen** aus.
    
  

### So aktualisieren Sie ein per Batch installiertes Add-In für SharePoint


1. Navigieren Sie zur Seite **Websiteinhalte** der Website für den Unternehmenskatalog. Wenn für ein Add-In ein Update verfügbar ist, wird neben dem Add-In eine Meldung angezeigt. Es wird ein Link angezeigt, um das Add-In zu aktualisieren.
    
  
2. Klicken Sie auf den Link, um das Add-In zu aktualisieren.
    
  
3. Wenn Sie zur Genehmigung der Berechtigungsanforderungen des Add-Ins aufgefordert werden, wählen Sie **Vertrauen** aus.
    
  

## Zusätzliche Ressourcen
<a name="SP15tenancies_addlresources"> </a>


-  [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md)
    
  
-  [Wichtige Aspekte der Architektur und Entwicklungslandschaft von Add-Ins für SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Bereitstellen und Installieren von SharePoint-Add-Ins: Methoden und Optionen](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Aktualisierungsverfahren für Add-Ins für SharePoint](sharepoint-add-ins-update-process.md)
    
  

