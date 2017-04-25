---
title: Erstellen von UX-Komponenten in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: bfdd0a58-2cc5-4805-ac89-4bd2fe6f3b09
---


# Erstellen von UX-Komponenten in SharePoint 2013
In diesem Artikel erfahren Sie, wie Sie UX-Komponenten für Ihre Add-Ins in SharePoint 2013 erstellen.
## Erstellen von UX-Komponenten in SharePoint-Add-Ins
<a name="SP15CreateUX_Creating"> </a>

Das Modell für SharePoint-Add-Ins bietet viele UX-Komponenten und Mechanismen, die Ihnen ermöglichen, in SharePoint-Add-Ins ein optimales Nutzungserlebnis zu bieten. Das Nutzungserlebnis im Add-In-Modell ist außerdem so flexibel, dass Sie die Verfahren und Plattformen verwenden können, die sich am besten an die Anforderungen von Endbenutzern anpassen lassen. In Tabelle 1 sind Ressourcen aufgeführt, die Informationen zum Erstellen und Verwenden von UX-Komponenten in Add-Ins bieten.




**Tabelle 1. Ressourcen und Anleitungen zum Erstellen von UX-Komponenten in SharePoint-Add-Ins**


|**Artikel**|**Beschreibung**|
|:-----|:-----|
| [Verwenden des Stylesheets einer SharePoint-Website in Add-Ins für SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) <br/> |Sie können in Ihrem SharePoint-Add-In auf das Stylesheet einer SharePoint-Website verweisen und es zum Formatieren Ihrer Webseiten nutzen, indem Sie das Stylesheet in SharePoint 2013 verwenden. Wenn ein Benutzer das Stylesheet oder Design der SharePoint-Website ändert, können Sie außerdem die neue Gruppe von Formatvorlagen in Ihr Add-In übernehmen, ohne den Stylesheet-Verweis im Add-In ändern zu müssen.  <br/> |
| [Verwenden des Client-Chromsteuerelements in Add-Ins für SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md) <br/> |Dank des Chromsteuerelements in SharePoint 2013 können Sie den Kopfzeilenstil einer bestimmten SharePoint-Website in Ihrem Add-In verwenden, ohne eine Serverbibliothek registrieren zu müssen oder eine bestimmte Technologie bzw. ein bestimmtes Tool zu verwenden. Sie müssen dazu eine SharePoint-JavaScript-Bibliothek durch ein standardmäßiges <script>-Tag registrieren. Sie können einen Platzhalter bereitstellen, indem Sie ein HTML- **div**-Element verwenden und das Steuerelement mit den verfügbaren Optionen weiter anpassen. Das Steuerelement erhält sein Aussehen durch die angegebene SharePoint-Website.  <br/> |
| [Erstellen von Add-In-Webparts zur Installation mit Ihrem SharePoint-Add-In](create-add-in-parts-to-install-with-your-sharepoint-add-in.md) <br/> |Mit Add-In-Parts können Sie Ihr Add-In-Nutzungserlebnis direkt in der Hostwebsite anzeigen. Ein Add-In-Part zeigt Ihren Add-In-Inhalt mithilfe eines **IFrame** an. Endbenutzer können das Nutzungserlebnis individuell anpassen, indem sie die benutzerdefinierten Eigenschaften verwenden, die Sie für Ihr Add-In-Part bereitstellen. Die Add-In-Webseite erhält die benutzerdefinierten Eigenschaftswerte durch Parameter in der Abfragezeichenfolge. <br/> |
| [Gewusst wie: Erstellen benutzerdefinierter Aktionen zur Bereitstellung mit Add-Ins für SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md) <br/> |Bei der SharePoint-Add-In-Erstellung ermöglichen Ihnen benutzerdefinierte Aktionen die Interaktion mit Listen und dem Menüband in der Hostwebsite. Eine benutzerdefinierte Aktion wird für die Hostwebsite bereitgestellt, wenn Endbenutzer Ihr Add-In installieren. Benutzerdefinierte Aktionen können eine Remotewebseite öffnen und durch die Abfragezeichenfolge Informationen übergeben. Für Add-Ins sind zwei Typen von benutzerdefinierten Aktionen verfügbar: Menüband und ECB (Edit Control Block).  <br/> |
| [Anpassen einer Listenansicht in Add-Ins für SharePoint durch clientseitiges Rendering](customize-a-list-view-in-sharepoint-add-ins-using-client-side-rendering.md) <br/> |Durch clientseitiges Rendering wird ein Mechanismus verfügbar, mit dem Sie Ihre eigene Ausgabe für eine Gruppe von Steuerelementen, die in einer SharePoint-Seite gehostet sind, generieren können. Dieser Mechanismus ermöglicht Ihnen die Verwendung bekannter Technologien, wie HTML und JavaScript, um die Rendering-Logik von SharePoint-Listenansichten zu definieren. Beim clientseitigen Rendering können Sie Ihre eigenen JavaScript-Ressourcen angeben und sie in den für Ihre Add-Ins verfügbaren Datenspeicheroptionen, wie z. B. eine Dokumentbibliothek, hosten.  <br/> |
| [Verwenden des clientseitigen Steuerelements "Personenauswahl" in von SharePoint gehosteten Share Point-Add-Ins](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md) <br/> |Erfahren Sie, wie das clientseitige Steuerelement „Personenauswahl" in SharePoint-Add-Ins verwendet wird. Mit dem clientseitigen Personenauswahl-Steuerelement können Benutzer schnell nach gültigen Benutzerkonten von Personen, Gruppen und Ansprüchen in ihrer Organisation suchen und diese auswählen. Bei der Auswahl handelt es sich um ein HTML- und JavaScript-Steuerelement mit browserübergreifender Unterstützung.  <br/> |
 

## Nächste Schritte: Arbeiten mit Daten in SharePoint-Add-Ins
<a name="SP15CreateUX_Next"> </a>

Haben Sie die Entwicklung einer optimalen UX für Ihr Add-In abgeschlossen? Integrieren Sie Daten mit den Mechanismen, die Ihnen im Modell für SharePoint-Add-Ins zur Verfügung stehen. Weitere Informationen finden Sie unter  [Arbeiten mit externen Daten in SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).




## Weitere Informationsquellen
<a name="SP15CreateUX_AddRes"> </a>


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)


-  [UX-Design für SharePoint-Add-Ins](ux-design-for-sharepoint-add-ins.md)


-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)



