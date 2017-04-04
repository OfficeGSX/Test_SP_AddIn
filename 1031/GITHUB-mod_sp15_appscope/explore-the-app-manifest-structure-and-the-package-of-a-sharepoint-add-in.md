---
title: Hinweise zur App-Manifeststruktur und zum Paket eines SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# Hinweise zur App-Manifeststruktur und zum Paket eines SharePoint-Add-Ins
Dieser Artikel enthält Informationen zur Add-In-Paketstruktur und Manifestdatei für ein SharePoint-Add-In.
## Paketstruktur eines Add-Ins für SharePoint
<a name="Package"> </a>

Ein SharePoint-Add-In-Paket ist eine Datei mit der Erweiterung "APP", die dem Standard  [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/de-de/magazine/cc163372.aspx) entspricht. Das Paket enthält die folgenden Elemente:
  
    
    

- **Add-In-Manifest:** Dies ist eine erforderliche Datei mit dem Namen "appmanifest.xml". SharePoint 2013 entnimmt daraus Informationen zu einigen wichtigen Eigenschaften des Add-Ins, z. B. den Titel und die zur Ausführung erforderlichen Berechtigungen. Weitere Informationen zum Inhalt dieser Datei finden Sie unter [Manifestdatei für Add-Ins für SharePoint](#AppManifest).
    
  
- **SharePoint-Lösungspakete:** Optional kann das Add-In ein SharePoint-Lösungspaket (WSP-Datei) mit den Komponenten des Add-In-Webs enthalten. Diese Komponenten können Seiten, Listeninstanzen, Ansichten, Dokumente, Features mit **Web**-Bereich und andere SharePoint 2013-Komponenten umfassen. (Weitere Informationen dazu, welche SharePoint-Komponenten in ein SharePoint-Add-In aufgenommen werden können, finden Sie unter  [Typen von SharePoint-Komponenten, die in einem SharePoint-Add-In enthalten sein können](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).) Die WSP-Datei kann auch Office-Add-Ins enthalten. Die in der WSP-Datei enthaltenen Komponenten werden im Add-In-Web bereitgestellt. Ein Beispiel für ein Add-In-Paket, das ein SharePoint-Lösungspaket enthält, finden Sie unter  [Gewusst wie: Erstellen eines von einem Anbieter gehosteten Add-Ins, das eine benutzerdefinierte SharePoint-Liste und einen benutzerdefinierten Inhaltstyp enthält](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md).
    
  
- **Hostweb-Features mit benutzerdefinierten Aktionen oder Add-In-Parts:** Neben den SharePoint 2013-Komponenten, die im Add-In-Web bereitgestellt werden, kann ein SharePoint-Add-In auch eine oder mehrere benutzerdefinierte Aktionen (Kontextmenübefehle oder Menübanderweiterungen) im Hostweb bereitstellen. Hierzu wird ein Feature in das Add-In-Paket eingefügt, das nicht in der .WSP-Datei des Pakets enthalten ist und das die Komponenten bereitstellt, die für das Hostweb vorgesehen sind. Dieses "freie" Feature wird als Hostweb-Feature bezeichnet. Add-In-Parts werden auf die gleiche Weise im Hostweb bereitgestellt. Das Hostweb-Feature besteht aus der SharePoint 2013-Standarddatei "feature.xml" und mindestens einer zugehörigen "elements.xml"-Datei. Die "elements.xml"-Datei für eine benutzerdefinierte Aktion enthält beispielsweise das **CustomAction**-Markup für die benutzerdefinierte Aktion. Es kann auch Markup für Add-In-Parts enthalten. Nur diese beiden Komponenten können im Hostweb-Feature enthalten sein. Diese Hostweb-Features werden im Add-In-Manifest nicht einzeln aufgeführt. Es handelt sich jedoch im Sinn von OPC um "parts", und es besteht eine explizite OPC-Beziehung zwischen dem Add-In-Manifest und jeder dieser Dateien. Ein Beispiel für ein Add-In-Paket, das ein Hostweb-Feature enthält, finden Sie unter  [Gewusst wie: Erstellen benutzerdefinierter Aktionen zur Bereitstellung mit Add-Ins für SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
    
    > **HINWEIS**
      > Mandantenadministratoren haben die Möglichkeit, ein SharePoint-Add-In per Stapelverarbeitung auf mehreren Websites zu installieren. Ein Add-In, das auf diese Weise installiert worden ist, hat den Gültigkeitsbereich **Tenant**. Wenn das Add-In nicht per Stapelverarbeitung auf mehreren Websites, sondern stattdessen auf jeder Website getrennt installiert wurde, dann hat es den Gültigkeitsbereich **Web**Wenn das Hostweb-Feature Menüband-Erweiterungen oder Add-In-Webparts enthält, dann werden diese nicht in den Hostwebs bereitgestellt, wenn das Add-In per Stapelverarbeitung installiert wird. Daher werden nur Kontextmenüelemente bei Add-Ins mit dem Gültigkeitsbereich "Tenant". Der Add-In-Bereich darf nicht mit dem Feature-Bereich verwechselt werden. Der Feature-Bereich bestimmt, wo die in einem Feature enthaltenen Elemente bereitgestellt werden. Mögliche Bereiche sind **Farm**, **WebApplication**, **Site** (d. h. die Websitesammlung) und **Web**. Nur die **Web**-Option ist für Features in SharePoint-Add-Ins zulässig (sowohl Hostweb-Features als auch Features in einer WSP-Datei in einem Add-In-Paket). Der Add-In-Bereich bezeichnet den Bereich, in dem ein Add-In installiert worden ist. Mögliche Bereiche sind **Web**, wenn das Add-In parallel auf einem oder mehreren Websites installiert wurde, und **Tenant**, wenn das Add-In auf allen oder einer Teilmenge der Websites der Mandanteneinheit eines Kunden installiert wurde. Weitere Informationen zu den Bereichen **Tenant** und **Web** finden Sie unter [Mandantschaften und Bereitstellungsbereiche von Add-Ins für SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md). 
- **Lokalisierungsressourcendateien (.RESX):** Diese Dateien sind für die Lokalisierung verschiedener Aspekte des Add-In-Manifests vorgesehen, zu denen der Titel und Aspekte der Hostweb-Features im Add-In-Paket gehören. (Einzelne Teile des Add-In-Pakets, die sich in einem eigenen Paket befinden, wie WSP-Dateien, Azure-Websites-Pakete und Add-In-Manifeste, verfügen jeweils über eigene Lokalisierungsprozesse, die genauso ausgeführt werden wie bei Elementen, die nicht Teil eines SharePoint-Add-In sind.) Ein Beispiel zu einem Add-In-Paket, das die RESX-Dateien für ein Hostweb-Feature enthält, finden Sie unter [Lokalisieren von Add-Ins für SharePoint](localize-sharepoint-add-ins.md).
    
  
- **Office-Add-Ins Manifeste:** Optional können ein oder mehrere Office-Add-Ins-Manifeste vorhanden sein, die jeweils eine Paketdefinition für ein Office-Add-In enthalten. Dieser Teil kann nur dann in das Add-In-Paket aufgenommen werden, wenn das Add-In in einen unternehmenseigenen Add-In-Katalog für SharePoint 2013 und nicht in einen öffentlichen Add-In-Store hochgeladen wird. Weitere Informationen finden Sie unter [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md).
    
  

## Manifestdatei für Add-Ins für SharePoint
<a name="AppManifest"> </a>

Jedes SharePoint-Add-In enthält die Datei "appmanifest.xml". Mit der Datei "appmanifest.xml" werden SharePoint die erforderlichen Informationen zum Add-In mitgeteilt und die wichtigsten Eigenschaften des Add-Ins definiert. Nachfolgend werden einige Elemente aufgeführt, die im Manifest angegeben werden:
  
    
    

- Der interne Name, die Produkt-ID und die Version des Add-Ins.
    
  
- Die URL der Startseite. Die Startseite ist die Seite, die beim Start des Add-Ins geöffnet wird. Es kann sich dabei um eine Seite im Add-In-Web, eine cloudbasierte Seite oder eine Seite auf einem Webserver des unabhängigen Softwareherstellers handeln.
    
    > **HINWEIS**
      > Unter Umständen gelten Einschränkungen dafür, welche Art von Datei im **StartPage**-Element angegeben werden kann. Ausführliche Informationen finden Sie unter  [StartPage-Element (PropertiesDefinition ComplexType) (SharePoint-Add-in Manifest)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx). > Wenn Sie mehrere Abfrageparameter im **StartPage**-Wert kombinieren, müssen Sie diese Parameter durch Angabe des Codes für das kaufmännische Und-Zeichen " `&amp;amp;`" statt " `&amp;`" oder eines Semikolons aneinanderreihen. 
- Andere Eigenschaften des Add-Ins. Hierzu gehören der Titel und die Gebietsschemas, die von dem Add-In unterstützt werden (beides erforderlich), die URLs der Dienste, welche die Ereignisse "post-install", "post-upgrade" und "pre-uninstall" behandeln, sowie die Webvorlage, die zum Erstellen des Add-In-Webs verwendet werden soll.
    
  
- Anforderungen von Berechtigungen zum Zugriff auf SharePoint-Ressourcen außerhalb des Add-In-Webs.
    
  
- Eine zu Authentifizierungs- und Autorisierungszwecken zu verwendende Identifikation des Add-In-Prinzipals. Diesem Prinzipal werden die Berechtigungen gewährt. Bei einem in SharePoint gehosteten Add-In ist dies nicht erforderlich.
    
  
- Eine Liste der Voraussetzungen, falls vorhanden, die für das Add-In verfügbar sein müssen, damit das Add-In installiert werden kann. Beispielsweise kann es notwendig sein, bestimmte Features zu installieren und zu aktivieren und bestimmte Dienste zu lizenzieren und zu installieren.
    
  

> **HINWEIS**
> Lediglich die Add-In-Manifestdatei ist im Add-In-Paket erforderlich. Nicht alle oben genannten Elemente müssen in dieser Datei vorhanden sein. 
  
    
    

Ausführliche Informationen zum Markup für das Add-In-Manifest finden Sie unter dem Knoten  [Schemareferenz für Manifeste von apps für SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx). Dieses Thema ist kein Ersatz für die Informationen unter diesem Knoten, einschließlich der Informationen zu den erforderlichen Elementen und Attributen. Beachten Sie auch, dass SharePoint-Add-In-Manifeste gegenüber Office-Add-In-Manifesten ein anderes Schema aufweisen. Informationen zu Letzteren finden Sie unter  [Schemareferenz für Apps für Office-Manifeste (v1.1)](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx).
  
    
    
Es folgt ein Beispiel für die Datei "appmanifest.xml". Beachten Sie, dass in diesem Beispiel die Startseite des Add-Ins eine ASP.NET-Seite auf einem Remoteserver und keine Seite der SharePoint-Website ist. Die URL dieser Seite enthält eine Abfragezeichenfolge, mit der die URL des Hostwebs an die Remotewebanwendung übergeben wird. Der Teil "{HostUrl}" dieser Zeichenfolge ist ein Token, das beim Starten des Add-Ins aufgelöst wird. Das Add-In fordert eine Schreibberechtigung (write) für alle Listen im Hostweb an. Die Remotewebanwendung ist der Add-In-Prinzipal, dem diese Berechtigung gewährt werden muss.
  
    
    
Sie müssen in Ihrem Add-In-Manifest entweder das **SupportedLocales**- oder das **SupportedLanguages**-Element verwenden. **SupportedLanguages** gilt als hinfällig und wird durch **SupportedLocales** ersetzt. Das **SupportedLanguages**-Element funktioniert auch nach der Veröffentlichung, Sie sollten es jedoch nicht mehr verwenden. Weitere Informationen zu diesen Elementen finden Sie unter  [SupportedLocales-Element (PropertiesDefinition ComplexType) (SharePoint-Add-in Manifest)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx) und [SupportedLanguages-Element (PropertiesDefinition ComplexType) (SharePoint-Add-in Manifest)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx).
  
    
    

> **HINWEIS**
> Die Werte des **Scope**-Attributs des **AppPermissionRequest**-Elements sind wie URIs strukturiert, es handelt sich jedoch um Zeichenfolgenliterale. Im folgenden Beispiel ist kein Teil des Beispielwerts für **Scope** ein Platzhalter. Weitere Informationen zu Berechtigungen finden Sie unter [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md). 
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### URL-Token im Add-In-Manifest

SharePoint 2013 stellt verschiedene Token bereit, die im **StartPage**-Element und an anderen Stellen in Add-Ins und Komponenten zur Darstellung von Informationen verwendet werden können, die erst bekannt sind, wenn das Add-In ausgeführt wird. Die SharePoint 2013-Infrastruktur löst diese Token auf. Einige Token werden am Anfang der URL angegeben, und andere Token können innerhalb einer URL verwendet werden, z. B. der Wert eines Abfrageparameters. Diese und einige andere Token können auch in verschiedenen SharePoint 2013-Entwicklungskontexten verwendet werden. Ausführliche Informationen zu diesen Token und ihrer Verwendung finden Sie unter  [URL-Zeichenfolgen und Tokens in Add-Ins für SharePoint](url-strings-and-tokens-in-sharepoint-add-ins.md). Allgemeine Informationen zu anderen Token und URLs in SharePoint 2013 finden Sie unter  [URLs und Token in SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx).
  
    
    

> **HINWEIS**
> Diese Token werden nicht im **Scope**-Attribut des **AppPermissionRequest**-Elements verwendet. 
  
    
    


## Weitere Ressourcen
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Wichtige Aspekte der Architektur und Entwicklungslandschaft von Add-Ins für SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Schemareferenz für Manifeste von apps für SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/de-de/magazine/cc163372.aspx)
    
  
-  [Data-Tier Application Connection (DAC)](http://msdn.microsoft.com/de-de/library/ee210546.aspx)
    
  
-  [Web Deploy 2.0](http://www.iis.net/downloads/microsoft/web-deploy)
    
  

