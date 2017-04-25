---
title: URL-Zeichenfolgen und Tokens in Add-Ins für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# URL-Zeichenfolgen und Tokens in Add-Ins für SharePoint
In diesem Artikel erfahren Sie, welche URL-Token in SharePoint-Add-Ins zur Verfügung stehen.
> **WICHTIG**
> Allgemeine Informationen zur Erstellung von URLs in SharePoint 2013 und zur Verwendung von Token in diesen URLs finden Sie unter  [URLs und Token in SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx). In diesem Thema werden die in SharePoint-Add-Ins verfügbaren Token beschrieben. 





## 
<a name="URLtokens"> </a>

SharePoint 2013 unterstützt die in der folgenden Tabelle aufgeführten Token in SharePoint-Add-Ins.



Die Token in den Tabellen dieses Abschnitts können in einer Vielzahl von Situationen bei der Entwicklung von SharePoint-Add-Ins in URLs verwendet werden, so beispielsweise in benutzerdefinierten Aktionen und in Links auf benutzerdefinierten Seiten. In einigen Kontexten können einige dieser URLs nicht verwendet werden. Drei der wichtigsten Orte, an denen nur eine eingeschränkte Liste der Token verwendet werden kann, sind die Startseite eines Add-Ins, eine benutzerdefinierte Aktion im Hostweb und die  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) -Eigenschaft eines Add-In-Webparts. Diese werden in separaten Spalten aufgeführt. Es handelt sich dabei * **jedoch nicht um eine erschöpfende Liste der Orte, an denen Token verwendet werden können.*** 



Die Spalte **StartPage** gibt an, ob das Token im **StartPage**-Element eines Add-In-Manifests verwendet werden kann. Die Spalte **Benutzerdefinierte Aktion** gibt an, ob das Token in der URL einer benutzerdefinierten Aktion in einem Hostweb verwendet werden kann. Die Spalte **Add-In-Webpart** gibt an, ob das Token in der [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) -Eigenschaft des Add-In-Webparts verwendet werden kann.




**Token, die am Anfang einer URL in einer SharePoint-Add-In verwendet werden können**


|**Token**|**Wird aufgelöst in**|**StartPage**|**Benutzerdefinierte Aktion**|**Add-In-Webpart**|**Hinweise**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl  <br/> |Die URL des Add-In-Webs einer SharePoint-Add-In.  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> |Dieses Token sollte nur außerhalb von Add-In-Webs verwendet werden. Im Add-In-Web verwenden Sie **~site** als URL des Add-In-Webs. <br/> |
|~controlTemplates  <br/> |Die URL des virtuellen Ordners **ControlTemplates** der aktuellen Website. <br/> |Nein  <br/> |Nein  <br/> |Nein  <br/> ||
|~hostUrl  <br/> |Die URL des Hostweb  <br/> |Nein  <br/> |Nein  <br/> |Ja  <br/> ||
|~hostLogoUrl  <br/> |Die URL des Logos des Hostweb  <br/> |Nein  <br/> |Nein  <br/> |Nein  <br/> ||
|~layouts  <br/> |Die URL des virtuellen Ordners **Layouts** für die aktuelle Website. <br/> |Nein  <br/> |Nein  <br/> |Nein  <br/> ||
|~remoteAppUrl  <br/> |Die URL einer Remotewebanwendung in einer SharePoint-Add-In.  <br/> |Ja  <br/> |"Ja" im Hostweb, aber "Nein" im Add-In-Webpart  <br/> |Ja  <br/> |Wenn Sie Microsoft Office-Entwicklertools für Visual Studionicht zum Entwickeln Ihres Add-Ins für SharePoint verwenden, können Sie **~remoteAppUrl** nicht in dem **StartPage**-URL verwenden. Wenn Sie jedoch Visual Studio und die Tools verwenden, können Sie dieses Token für jedes von einem Anbieter gehostete Add-In verwenden. Es wird aufgelöst, wenn das Add-In von Visual Studio gepackt wird. Bei dieser Art der Nutzung handelt es sich dann eher um ein Visual Studio-Token als um ein SharePoint-Token. Es kann außerhalb des Add-In-Manifests verwendet werden, auch wenn Sie Microsoft Office-Entwicklertools für Visual Studio nicht verwenden.  <br/> |
|~site  <br/> |Die URL der aktuellen Website.  <br/> |Nein  <br/> |Nein  <br/> |Ja  <br/> ||
|~sitecollection  <br/> |Die URL der übergeordneten Websitesammlung der aktuellen Website.  <br/> |Nein  <br/> |Nein  <br/> |Ja  <br/> ||
 
Sofern nicht anders angegeben, kann keines der Token in der nächsten Tabelle im  *Pfad*  -Teil des [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) -Eigenschaftenwerts des Add-In-Webparts verwendet werden. Die Spalte **Add-In-Webpart** bezieht sich auf deren Verwendung im *Abfragezeichenfolge*  -Teil des Werts.




**Token, die in einer URL verwendet werden können**


|**Token**|**Wird aufgelöst in**|**StartPage**|**Benutzerdefinierte Aktion**|**Add-In-Webpart**|**Hinweise**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken}  <br/> |Das OAuth-Kontexttoken für das Add-In  <br/> |Nein  <br/> |Nein  <br/> |Nein  <br/> ||
|{AppWebUrl}  <br/> |Die URL des Add-In-Webparts in einer SharePoint-Add-In  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> |Dieses Token sollte nur außerhalb des Add-In-Webparts verwendet werden. Innerhalb des Add-In-Webparts verwenden Sie **{Site}** als URL des Add-In-Webparts. <br/> |
|{ClientTag}  <br/> |Die Clientcache-Kontrollnummer (Clienttag) für die aktuelle Website  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> ||
|{HostLogoUrl}  <br/> |Das Logo für das Hostweb einer SharePoint-Add-In.  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> ||
|{HostTitle}  <br/> |Der Titel des Hostwebs einer SharePoint-Add-In.  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> ||
|{HostUrl}  <br/> |Die URL des Hostwebs einer SharePoint-Add-In.  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> ||
|{ItemId}  <br/> |Die ID eines Elements in einer Liste oder Bibliothek (eine ganze Zahl)  <br/> |Nein  <br/> |Ja  <br/> |Nein  <br/> ||
|{ItemUrl}  <br/> |Die URL des Elements, an dem eine Aktion ausgeführt wird.  <br/> |Nein  <br/> |Ja  <br/> |Nein  <br/> ||
|{Language}  <br/> |Die aktuelle Sprach-/Kultureinstellung des Hostwebs einer SharePoint-Add-In.  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> ||
|{ListId}  <br/> |Die ID der aktuellen Liste (eine GUID).  <br/> |Nein  <br/> |Ja  <br/> |Nein  <br/> ||
|{ProductNumber}  <br/> |Die vollständige Buildversionsnummer der SharePoint-Farm  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> |Beispielwert: "15.0.4433.1011"  <br/> |
|{RecurrenceId}  <br/> |Der Wiederholungsindex eines sich wiederholenden Ereignisses.  <br/> |Nein  <br/> |Ja  <br/> |Nein  <br/> |In Kontextmenüs von Listenelementen wird die Verwendung dieses Tokens nicht unterstützt.  <br/> |
|{RemoteAppUrl}  <br/> |Die URL einer Remotewebanwendung in einer SharePoint-Add-In.  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> ||
|{Site}  <br/> |Die URL der aktuellen Website.  <br/> |Nein  <br/> |Ja  <br/> |Ja  <br/> ||
|{SiteCollection}  <br/> |Die URL der übergeordneten Website der aktuellen Website.  <br/> |Nein  <br/> |Ja  <br/> |Ja  <br/> ||
|{SiteUrl}  <br/> |Die URL der aktuellen Website.  <br/> |Nein  <br/> |Ja  <br/> |Nein  <br/> ||
|{Source}  <br/> |Die URL der HTTP-Anforderung  <br/> |Nein  <br/> |Ja  <br/> |Nein  <br/> ||
|{StandardTokens}  <br/> |Weitere Informationen finden Sie in der "Anmerkungen".  <br/> |Ja  <br/> |Ja  <br/> |Ja  <br/> |Dieses Token ist die Kombination von fünf anderen Token. Es wird anfänglich in  `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}` aufgelöst. Dann wird jedes dieser Token aufgelöst. Wenn kein Add-In-Webpart vorhanden ist, ist `&amp;SPAppWebUrl={AppWebUrl}` nicht enthalten. <br/> |
 

## Zusätzliche Ressourcen
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [Erweiterte Extranetunterstützung](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)


-  [Abrufen von Verweisen auf Websites, Webanwendungen und andere Schlüsselobjekte](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)


-  [Arbeiten mit Listenobjekten und Auflistungen](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)


-  [Verwenden des Objektmodells für grundlegende Aufgaben](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)



