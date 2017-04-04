---
title: Designrichtlinien für die Benutzerfreundlichkeit von Add-Ins für SharePoint
ms.prod: SHAREPOINT
ms.assetid: a4a8f53c-27d7-43dc-b6db-aa7b1f1c7d45
---


# Designrichtlinien für die Benutzerfreundlichkeit von Add-Ins für SharePoint
Hier erhalten Sie Informationen über allgemeine, auf das Benutzungserlebnis bzw. die User Experience (UX) ausgerichtete Entwurfsrichtlinien für Add-Ins in SharePoint 2013, darunter die Wahl des Chrom, die Verwendung des CSS, das Verwalten von Benutzerlizenzen und andere Entwurfsaufgaben.
Add-Ins sind ein neues Konzept für SharePoint 2013 und ermöglichen Endbenutzern, ihren Websites neue Funktionen hinzuzufügen und dabei die Zuverlässigkeit der SharePoint-Site weiterhin zu gewährleisten. Zum Erstellen eines guten Add-Ins gehört nicht nur, interessante Funktionen bereitzustellen (obwohl dies ebenfalls wichtig ist), sondern auch sicherzustellen, dass das Add-In gut aussieht und sich nahtlos in die Website, auf der es installiert wird, einfügt.
  
    
    


## Wahl des Chrom für Ihr Add-In
<a name="UXGuide_AppChrome"> </a>

Beim Erstellen eines Add-Ins müssen Sie als erstes entscheiden, wie ausgeprägt das Branding Ihrer Seiten sein soll und wo diese gehostet werden sollen. Auf der Grundlage dieser Optionen liegt es dann auf der Hand, welche Technologie Sie für den Betrieb Ihres Chrom verwenden:
  
    
    

- **In SharePoint gehostete ASPX-Seiten:** Verwenden Sie die Add-In-Vorlage.
    
  
- **In SharePoint gehostete HTML-Seiten oder beliebige Seiten außerhalb von SharePoint:** Verwenden Sie das Chromsteuerelement.
    
  
- **Seiten mit benutzerdefiniertem Branding:** Verwenden Sie ein eigenes Chrom.
    
  

### Verwenden der Add-In-Vorlage für in SharePoint gehostete Seiten
<a name="UXGuide_AppTemplate"> </a>

Die Add-In-Vorlage kann nur für in SharePoint gehostete ASPX-Seiten verwendet werden. Die Vorlage enthält die **app.master**-Masterseite (die für ein Add-In geeignetes Chrom enthält und auf das Design der Hostwebsite abgestimmt ist), und verbirgt SharePoint-Funktionen, die entweder nicht funktionieren würden oder innerhalb einer Add-In-Web nicht sinnvoll sind. Abbildung 1 zeigt eine in SharePoint gehostete Seite, die die Add-In-Vorlage verwendet.
  
    
    

**Abbildung 1. In SharePoint gehostete Seite, die die Add-In-Vorlage verwendet**

  
    
    

  
    
    
![Eine in SharePoint gehostete Seite, die die App-Vorlage verwendet](images/AppUXGuidelines_AppTemplate.png)
  
    
    
Die Add-In-Vorlage ist die Standardvorlage in Visual Studio, wenn Sie eine Add-In-Website und Seiten innerhalb dieser Website erstellen.
  
    
    

### Verwenden des Chromsteuerelements in SharePoint-Add-Ins
<a name="UXGuide_ChromeControl"> </a>

Wenn Sie keine in SharePoint gehosteten ASPX-Seiten erstellen, Ihr Add-In sich jedoch trotzdem problemlos in die Hostwebsite, von der aus es verwendet wird, einbinden lassen soll, ist das Chromsteuerelement die richtige Wahl. Abbildung 2 zeigt das Chromsteuerelement.
  
    
    

**Abbildung 2. Chromsteuerelement auf einer Webseite**

  
    
    

  
    
    
![Chromsteuerelement auf einer Webseite](images/AppUXGuidelines_ChromeControl.png)
  
    
    

  
    
    

**Video anschauen: Chromsteuerelement in SharePoint 2013**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

  
    
    

### So verwenden Sie das Chromsteuerelement


1. Fügen Sie der Steuerelementbibliothek einen Verweis hinzu. Hierzu stehen Ihnen zwei Möglichkeiten zur Verfügung:
    
  - Verweisen Sie im Stamm des Layouts-Ordners auf die Bibliothek, wie im folgenden Beispiel dargestellt.
    
  ```
  
<script
    type="text/javascript" 
    src="http://{server URL}/_layouts/15/sp.ui.controls.js">
</script>
  ```

  - Kopieren Sie die Bibliothek auf Ihre eigene Website und verweisen Sie dort auf sie.
    
    > **VORSICHT**
      > Wenn Sie sich für diese Alternative entscheiden, kann Ihr Add-In keine Aktualisierungen des Steuerelements nutzen 
2. Fügen Sie das DOM-Platzhalterelement an der Position ein, an der das Steuerelement gerendert werden soll, wie in diesem Beispiel dargestellt.
    
  ```
  
<div id='chromeControlContainer'></div>
  ```

3. Instanziieren Sie das Steuerelement.
    
  ```
  function addchromecontrol(){
    var options = {};
    options.siteTitle ="{host site title}";
    options.siteUrl = "{host URL}";
    options.appHelpPageUrl = "{help page URL}";
    options.appIconUrl = "{app icon URL}";
    options.appTitle = "add-in Title";
    nav = new SP.UI.Controls.Navigation("chromeControlContainer", options);
    nav.setVisible(true);
}
  ```

4. (Optional) Wenn Sie keinen Titelbereich auf Ihrer Seite möchten, können Sie ihn entfernen, indem Sie den folgenden JavaScript-Code ausführen.
    
  ```
  
nav.setBottomHeaderVisible(false);
  ```

Das Chromsteuerelement bietet zwei optionale Add-In-Symbole: Eines in der oberen Navigationsleiste und eines im Titelbereich. Das Add-In-Symbol in der oberen Navigationsleiste umfasst 24 x 24 Pixel (px), das Symbol im Titelbereich hat die gleiche Größe wie SharePoint-Website-Symbole - max. 64 px auf 180 px. Es wird empfohlen, ein auf einem weißen, schwarzen, grauen, hellen und gedeckten Hintergrund getestetes PNG-Bild zu verwenden, da Benutzer und Administratoren das Websitedesign ändern können. Weitere Informationen über die Verwendung des Chromsteuerelements finden Sie unter  [Verwenden des Client-Chromsteuerelements in Add-Ins für SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md).
  
    
    

### Erstellen einer benutzerdefinierten Branding-Benutzeroberfläche in SharePoint-Add-Ins
<a name="UXGuide_CustomUI"> </a>

Wenn Sie Ihr eigenes Branding im Add-In verwenden möchten, anstatt sie am Design der Hostwebsite auszurichten und in die SharePoint-Site einzupassen, in der Ihr Add-In installiert ist, müssen Sie das Chromsteuerelement vollständig neu erstellen. Sie sollten jedoch noch einen Link „Zurück zur Website" in der linken oberen Ecke der Seite (oben rechts bei Sprachen, die von rechts nach links gelesen werden) vorsehen, über den der Benutzer zurück zu der Website gelangen kann, auf der das Add-In installiert ist.
  
    
    

## Verwenden des CSS der Hostwebsite in SharePoint-Add-Ins
<a name="UXGuide_CSS"> </a>

Indem Sie die gleichen Formate wie für die Hostwebsite verwenden, können Sie sicherstellen, dass Ihre Add-Ins mit der SharePoint-Website konsistent bleiben, aus der sie stammen. Die tatsächlichen Formate können sich basierend auf dem Design der Website ändern. Indem Sie jedoch auf die CSS-Datei der Hostwebsite verweisen, können Sie sicher sein, dass Ihr Add-In sich unabhängig von seinem Installationsort einfügt.
  
    
    
Um die CSS-Formatvorlagen von der Hostwebsite abzurufen, müssen Sie auf ihre CSS-Datei verweisen. Dazu gibt es verschiedene Möglichkeiten.
  
    
    

### So verweisen Sie auf die CSS-Datei der Hostwebsite


1. Wenn Sie die Add-In-Vorlage oder das Add-In-Chromsteuerelement verwenden, wird dies automatisch für Sie erledigt.
    
  
2. Wenn Sie sich innerhalb der Add-In-Website befinden, können Sie die Steuerelemente **CssRegistration** und **CssLink** verwenden, um auf die CSS-Datei zu verweisen, indem Sie den folgenden Code entweder auf der Masterseite oder der ASPX-Seite einfügen:
    
  ```HTML
  <SharePoint:CssRegistration runat="server" name="default" />
<SharePoint:CssLink runat="server />

  ```

3. Sie können ein <link>-Element verwenden, um auf die CSS-Datei zu verweisen, indem Sie eine URL aus der URL der Hostwebsite erstellen, wie in diesem Beispiel dargestellt.
    
  ```HTML
  
<link rel="stylesheet" href="{host web URL}/_layouts/15/defaultcss.ashx" />
  ```


    Wenn Sie diese Vorgehensweise verwenden, müssen Sie JavaScript auf der Seite ausführen, um die URL der Hostwebsite aus der Abfragezeichenfolge abzurufen. Dann können Sie die URL der Hostwebsite in das **link**-Element einfügen, bevor Sie das Element in das DOM der Seite schreiben.
    
  
Als erstes sollten Sie beim Formatieren Ihres Add-Ins möglichst viel semantischen HTML-Code verwenden, d. h. **H1**, **H2**, **H3** usw. für die verschiedenen Überschriften und Eingabetags für Schaltflächen. Sie sollten außerdem versuchen, in möglichst großem Umfang SharePoint-Kernformatvorlagen zu verwenden, damit bei einer Änderung des Designs der Hostwebsite Ihr Add-In diese Änderungen nahtlos und automatisch übernimmt. Die folgenden Tabellen zeigen die Verwendung von Formatvorlagen beim Standarddesign.
  
    
    

**Tabelle 1. Textkörperformatvorlagen**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![ms-textXLarge](images/AppUXGuidelines_ms-textxlarge.png)|Besonders großer Textkörper  <br/> |.ms-textXLarge  <br/> |
|![ms-textlarge](images/AppUXGuidelines_ms-textlarge.png)|Großer Textkörper  <br/> |.ms-textLarge  <br/> |
|![Text](images/AppUXGuidelines_body.png)|Normaler Textkörper  <br/> |Wird automatisch geerbt  <br/> |
|![ms-textsmall](images/AppUXGuidelines_ms-textsmall.png)|Kleiner Textkörper  <br/> |.ms-textSmall  <br/> |
|![ms-metadata](images/AppUXGuidelines_ms-metadata.png)|Metadatentext  <br/> |.ms-metadata  <br/> |
   

**Tabelle 2. Titel- und Überschriftenformatvorlagen**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![ms-core-pagetitle](images/AppUXGuidelines_ms-core-pagetitle.png)|Haupttitel auf der Seite  <br/> |.ms-core-pageTitle  <br/> |
|![h1](images/AppUXGuidelines_h1.png)|Titel für Dialogfelder, Formulare, Blogs und Diskussionsbeiträge. Ein alternativer „primärer" Titel für spezielle Inhaltstypen oder Add-Ins, die die ganze Seite einnehmen und sich von einer regulären Wiki- oder Webparts-Seite unterscheiden sollen.  <br/> |H1  <br/> |
|![h2](images/AppUXGuidelines_h2.png)|Sekundäre Überschrift im Verhältnis zu H1. Zum Beispiel verwendet Communitys H1 Accent für den Titel eines Beitrags und H2 Accent für die beste "Antwort" auf den Beitrag.  <br/> |H2  <br/> |
|![h3](images/AppUXGuidelines_h3.png)|In der Regel eine Unterüberschrift unter H2.  <br/> |H3  <br/> |
|![h4](images/AppUXGuidelines_h4.png)|Unterüberschriften unter H3.  <br/> |H4  <br/> |
|![ms-webpart-titletext](images/AppUXGuidelines_ms-webpart-titletext.png)|Titel des Haupt- bzw. primären Webparts auf einer Seite oder für Hauptabschnittsüberschriften.  <br/> |.ms-webpart-titleText  <br/> |
|![ms-dlg-heading](images/AppUXGuidelines_ms-dlg-heading.png)|Titel für Überschriften in Dialogfeldern oder Legenden.  <br/> |.ms-dlg-heading  <br/> |
   

**Tabelle 3. Formatvorlagen für die Navigation**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![QuickLaunchHeading](images/AppUXGuidelines_QuickLaunchHeading.png)|Überschrift der linken Navigationsleiste.  <br/> |.ms-core-listMenu-verticalBox > .ms-core-listMenu-root > li > .ms-core-listMenu-item  <br/> |
|![QuickLaunchLink](images/AppUXGuidelines_QuickLaunchLink.png)|Link in der linken Navigationsleiste.  <br/> |.ms-core-listMenu-verticalBox  <br/> |
|![QuickLaunchSelected](images/AppUXGuidelines_QuickLaunchSelected.png)|Ausgewähltes Element in der linken Navigationsleiste.  <br/> |.ms-core-listMenu-verticalBox + .ms-accentText  <br/> |
|![TopNav](images/AppUXGuidelines_TopNav.png)|Element in der oberen Navigationsleiste.  <br/> ||
|![TopNavSelected](images/AppUXGuidelines_TopNavSelected.png)|Ausgewähltes Element in der oberen Navigationsleiste.  <br/> ||
   

**Tabelle 4. Formatvorlagen für Befehle**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![ms-commandlink](images/AppUXGuidelines_ms-commandlink.png)|Links für primäre Aktionen, die der Benutzer innerhalb eines bestimmten Containers oder einer Seite durchführen soll. Wird z. B. verwendet, um die Befehle unter einer Legende zu formatieren. Zeigt immer die gleiche Farbe für besuchte und nicht besuchte Befehle an.  <br/> |.ms-commandLink  <br/> |
|![ms-secondarycommandlink](images/AppUXGuidelines_ms-secondarycommandlink.png)|Wird auch zum Formatieren von Aktions-Links verwendet, jedoch für Aktionen, die für den Inhalt sekundär sind. Diese Formatvorlage wird für sekundäre Aktionen verwendet, damit sie nicht die Aufmerksamkeit vom Inhalt ablenken.  <br/> |.ms-secondaryCommandLink  <br/> |
|![ms-calloutlink](images/AppUXGuidelines_ms-calloutlink.png)|Links in der Legende.  <br/> |.ms-calloutLink  <br/> |
   

**Tabelle 5. Formatvorlagen für Modifzierer**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![ms-accenttext](images/AppUXGuidelines_ms-accenttext.png)|Hilfsklasse, die die Akzentfarbe aus dem aktuellen Design für Text bereitstellt.  <br/> |.ms-accentText  <br/> |
|![Hyperlink](images/AppUXGuidelines_hyperlink.png)|Links im Inhalt sollten vom Hyperlinkformat und -verhalten erben. Das Hyperlinkformat wendet eine Farbe und einen Hover-Effekt an, um anzugeben, dass es sich um einen Link und nicht um normalen Text handelt.  <br/> |Durch Verwenden von <a> geerbt.  <br/> |
|![ms-error](images/AppUXGuidelines_ms-error.png)|Fehlermeldungen in Formularen.  <br/> |.ms-error  <br/> |
|![ms-soften](images/AppUXGuidelines_ms-soften.png)|Hilfsklasse, die ein schattiertes Grau für Text bereitstellt, der weniger hervorgehoben sein soll wie der normale Textkörper.  <br/> |.ms-soften  <br/> |
|![ms-disabled](images/AppUXGuidelines_ms-disabled.png)|Hilfsklasse, welche die "deaktivierte" Farbe auf Text anwendet, der zur Kennzeichnung von deaktivierten Stati verwendet wird.  <br/> |.ms-disabled  <br/> |
|![ms-uppercase](images/AppUXGuidelines_ms-uppercase.png)|Hilfsklasse, die den Text vollständig in Großbuchstaben umwandelt.  <br/> |.ms-uppercase  <br/> |
|![ms-helper](images/AppUXGuidelines_ms-helper.png)|Hilfsklasse zum Formatieren von Text wie Formulare.  <br/> |.ms-helper  <br/> |
|![hr](images/AppUXGuidelines_hr.png)|Gestrichelte Trennlinie, die verwendet wird, um Abschnitte im Schnellstartbereich und in Menüs zu trennen.  <br/> |HR  <br/> |
   

**Tabelle 6. Formatvorlagen für Part-Benutzeroberflächen**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![Dateien ziehen](images/AppUXGuidelines_dragfiles.png)|Inline-Haupttext am Anfang eines Parts.  <br/> |.ms-textXLarge + .ms-soften  <br/> |
|![ms-herocommandlink](images/AppUXGuidelines_ms-herocommandlink.png)|Befehle in der obersten Zeile eines Parts; pro Part sollten maximal ein oder zwei enthalten sein.  <br/> |.ms-heroCommandLink  <br/> |
|![ms-attractmode](images/AppUXGuidelines_ms-attractmode.png)|Text, der angezeigt wird, um dem Benutzer einen Anreiz zur Interaktion mit dem Part zu geben, wenn dieser keine Daten enthält.  <br/> |.ms-attractMode  <br/> |
|![ms-emptymode](images/AppUXGuidelines_ms-emptymode.png)|Text, der für den Benutzer angezeigt wird, wenn keine Daten verfügbar sind.  <br/> |.ms-emptyMode  <br/> |
|![mspivotlink](images/AppUXGuidelines_mspivotlink.png)|Ansicht-Steuerelemente, wie z. B. ein Pivot-Element.  <br/> |.ms-pivot-link  <br/> |
|![ms-listlink](images/AppUXGuidelines_ms-listlink.png)|Listenelemente, die auch Links sind.  <br/> |.ms-listLink  <br/> |
   

**Tabelle 7. Hintergrund- und Rahmenformatvorlagen**


|**Beispiel**|**Verwendung für**|**Formatvorlage**|
|:-----|:-----|:-----|
|![ms-emphasis](images/AppUXGuidelines_ms-emphasis.png)|Zum Formatieren eines Rechtecks, das auf der Seite stark hervorgehoben werden soll.  <br/> |.ms-emphasis  <br/> |
|![ms-emphasisborder](images/AppUXGuidelines_ms-emphasisborder.png)|Rahmen zum Hervorheben eines Elements.  <br/> |.ms-emphasisBorder  <br/> |
|![ms-subtleemphasis](images/AppUXGuidelines_ms-subtleemphasis.png)|Eine schwächere Hervorhebung eines Elements.  <br/> |.ms-subtleEmphasis  <br/> |
|![ms-subtleemphasiscommand](images/AppUXGuidelines_ms-subtleemphasiscommand.png)|Befehle in einem Element, mit ms-subtleEmphasis formatiert.  <br/> |.ms-subtleEmphasisCommand  <br/> |
|![ms-subtleemphasiscommand-disabled](images/AppUXGuidelines_ms-subtleemphasiscommand-disabled.png)|Deaktivierter Befehl in einem Element, mit ms-subtleEmphasis formatiert.  <br/> |.ms-subtleEmphasisCommand-disabled  <br/> |
|![ms-sidenav](images/AppUXGuidelines_ms-sidenav.png)|Seitliche Navigationselemente.  <br/> |.ms-sideNav  <br/> |
|![ms-sidenav-selected](images/AppUXGuidelines_ms-sidenav-selected.png)|Zum Formatieren des ausgewählten seitlichen Navigationselements.  <br/> |.ms-sideNav-selected  <br/> |
|![ms-lines](images/AppUXGuidelines_ms-lines.png)|Zum Hervorheben eines Elements durch einen Rahmen.  <br/> |.ms-lines  <br/> |
|![ms-subtlelines](images/AppUXGuidelines_ms-subtlelines.png)|Zum Hervorheben eines Elements durch einen feinen Rahmen.  <br/> |.ms-subtleLines  <br/> |
|![ms-stronglines](images/AppUXGuidelines_ms-stronglines.png)|Zum Hervorheben eines Elements durch einen starken oder farbigen Rahmen.  <br/> |.ms-strongLines  <br/> |
|![ms-disabledlines](images/AppUXGuidelines_ms-disabledlines.png)|Zum Hervorheben eines deaktivierten Elements durch einen Rahmen.  <br/> |.ms-disabledLines  <br/> |
|![ms-accentlines](images/AppUXGuidelines_ms-accentlines.png)|Zum Hervorheben eines Elements durch einen Akzentuierungsrahmen.  <br/> |.ms-accentLines  <br/> |
|![ms-popupborder](images/AppUXGuidelines_ms-popupborder.png)|Zum Begrenzen eines Popupfensters.  <br/> |.ms-popupBorder  <br/> |
|![ms-bgoverlay](images/AppUXGuidelines_ms-bgoverlay.png)|Zum Anwenden einer Überlagerung auf das Hintergrundelement.  <br/> |.ms-bgOverlay  <br/> |
|![bgdisabled](images/AppUXGuidelines_bgdisabled.png)|Um den Hintergrund eines Elements deaktiviert erscheinen zu lassen.  <br/> |.ms-bgDisabled  <br/> |
|![ms-bgheader](images/AppUXGuidelines_ms-bgheader.png)|Zum Anwenden der Kopfzeilen-Hintergrundfarbe.  <br/> |.ms-bgHeader  <br/> |
|![ms-bgfooter](images/AppUXGuidelines_ms-bgfooter.png)|Zum Anwenden der Fußzeilen-Hintergrundfarbe.  <br/> |.ms-bgFooter  <br/> |
|![md-bghoverable normal](images/AppUXGuidelines_md-bghoverable-normal.png)|Elemente, die farblich hervorgehoben werden sollen, wenn mit dem Mauszeiger darauf gezeigt wird. Das Beispiel zeigt das Element, wenn nicht mit dem Mauszeiger darauf gezeigt wird.  <br/> |.ms-bgHoverable  <br/> |
|![ms-bghoverable-onhover](images/AppUXGuidelines_ms-bghoverable-onhover.png)|Elemente, die farblich hervorgehoben werden sollen, wenn mit dem Mauszeiger darauf gezeigt wird. Das Beispiel zeigt das Element, wenn mit dem Mauszeiger darauf gezeigt wird.  <br/> |.ms-bgHoverable  <br/> |
|![ms-bgselected](images/AppUXGuidelines_ms-bgselected.png)|Zum Anzeigen der Auswahl eines Elements.  <br/> |.ms-bgSelected  <br/> |
|![ms-topbar](images/AppUXGuidelines_ms-topbar.png)|Elemente in der obersten Leiste der Seite.  <br/> |.ms-topBar  <br/> |
   
Weitere Informationen finden Sie unter  [Verwenden des Stylesheets einer SharePoint-Website in Add-Ins für SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md).
  
    
    

## Einheitliches Formatieren von gemeinsamen Elementen in SharePoint-Add-Ins
<a name="UXGuide_Styling"> </a>

Damit Benutzer sich Fertigkeiten aneignen können, die sowohl in SharePoint als auch in Add-Ins anwendbar sind, sollten Sie gemeinsame Elemente konsistent formatieren.
  
    
    

### Interne Navigation

Um die Navigation innerhalb Ihres Add-Ins zu ermöglichen, stehen hauptsächlich zwei Konzepte zur Verfügung: Linksnavigation und Topnavigation. Für welche Option Sie sich entscheiden, hängt in gewissem Maße auch vom weiteren Inhalt in Ihres Add-Ins ab. In der Regel ist Linksnavigation die richtige Wahl, insbesondere wenn Sie zwischen verschiedenen Listen umschalten oder der Fokus Ihres Add-Ins eine Master-Detail-Ansicht ist. Wenn andererseits Ihre Navigation hauptsächlich zwischen Ansichten wechselt, die als verschiedene Ansichten einer Liste betrachtet werden können, könnten Sie statt dessen auch Topnavigation wählen.
  
    
    
Sowohl die Linksnavigation als auch die Topnavigation verfügen über Objektmodelldarstellungen, die korrekt formatiert werden, wenn sie in SharePoint festgelegt werden. Außerhalb von SharePoint-Seiten ist etwas mehr Aufwand erforderlich, um das Markup für die Top- oder die Linksnavigation selbst zu erstellen und dann die entsprechenden CSS-Klasses für die korrekte Formatierung hinzuzufügen.
  
    
    

### Symbolleisten

In vielen Fällen verfügen Sie über eine kleine Anzahl von Befehlen, die Sie für Ihre Benutzer schnell verfügbar machen möchten. Wenn Sie das Menüband bereits auf Ihrer Seite verwenden, empfiehlt es sich, diese Befehle an logischen Positionen dem vorhandenen Menüband hinzuzufügen. Falls Sie jedoch auf der Seite noch über kein Menüband verfügen, ist es wahrscheinlich nicht sinnvoll, nur wegen ein paar Befehlen ein Menüband hinzuzufügen. In diesem Fall wird empfohlen, eine Symbolleiste hinzuzufügen, die auf das Element kontextbezogen ist, für das die Befehle gelten sollen. Verwenden Sie dazu Glyphe, mit ms-commandLink formatierten Text oder beides, um Ihre Befehle in der Symbolleiste dazustellen, welche die gleiche Hintergrundfarbe haben sollte wie die restliche Seite.
  
    
    

### Listen

Listen sind eine gängige Möglichkeit zur Darstellung von Daten für Benutzer. Wenn Ihr Add-In SharePoint-Seiten verwendet, können Sie das Listenansicht-Webpart verwenden, um Daten für Benutzer darzustellen und über die damit verbundenen Formatierungs- und Interaktionsmöglichkeiten zu verfügen. Wenn sich Ihre Seiten jedoch an anderer Stelle befinden oder Sie die Interaktion von Benutzern mit Ihrer Liste stärker steuern möchten, sollten Sie die Listenformatierung in SharePoint imitieren, während Sie Ihre eigenen Rendering- und Interaktionsmöglichkeiten bereitstellen. Nachfolgend sind einige Formatierungsprobleme aufgeführt, die Sie bei der Verwendung von Listen in Ihrem Add-In berücksichtigen sollten:
  
    
    

- **Ansichten:** Wenn Sie mehrere Ansichten in einer einzelnen Liste darstellen, sollten Sie am Anfang der Liste ein Pivot-Element verwenden, wie dies bei regulären SharePoint-Listen der Fall ist. Verwenden Sie jedoch niemals Pivot-Elemente für die Darstellung von Master-Detail-Daten.
    
  
- **Filter:** Wenn Sie einen Filter für eine bestehende Liste oder eine Master-Detail-Anordnung bereitstellen, verwenden Sie eine Randleiste, die bündig mit der linken Seite des Inhaltsbereichs abschließt und eine Breite von mindestens 300 Pixel hat. Kopieren Sie außerdem die SharePoint-Auswahlformatierung, um für den Benutzer anzugeben, welche Filter oder Elemente ausgewählt wurden.
    
  
- **Formulare:** Wenn ein Benutzer ein einzelnes Element anzeigt oder bearbeitet, sollten Sie entweder die integrierten SharePoint-Formulare verwenden oder ihre Formatierung imitieren, um eine konsistente Nutzungserfahrung zu vermitteln.
    
  

### Formulare, Dialogfelder und Legenden

Es gibt drei verschiedene Möglichkeiten, um für Benutzer Informationen über ein Objekt bereitzustellen oder um eine Benutzeroberfläche (UI) für die Benutzereingabe zu bieten: Ganzseitige Formulare, Dialogfelder und Legenden. Welche Option Sie wählen, hängt von der Benutzerintention sowie davon ab, wie viele Informationen angezeigt oder abgefragt werden sollen.
  
    
    

- **Ganzseitige Formulare:** Dies ist die beste Option, wenn Benutzer mehrere unterschiedliche Informationen eingeben sollen, oder wenn viele strukturierte Informationen gleichzeitig für sie angezeigt werden sollen. Ganzseitige Formulare sind auch in Szenarien am sinnvollsten, in denen komplexere Interaktionsmodelle erforderlich sind , wie z. B. das Menüband. In diesem Fall verweisen Sie den Benutzer bei Bedarf auf die Formularseite. Stellen Sie sicher, dass Benutzer über eine einfache Möglichkeit zum Speichern oder Verwerfen ihrer Änderungen verfügen, entweder mithilfe von Schaltflächen oder mit dem Menüband. In sehr langen Formularen, in denen Bildläufe durchgeführt werden müssen, empfiehlt es sich, die Optionen **Speichern** und **Abbrechen** sowohl am oberen als auch am unteren Rand des Formulars zu positionieren.
    
  
- **Dialogfelder:** Dies sind modale Benutzeroberflächen-Container, die in der Regel verwendet werden, um weitere Informationen oder Aktionen auf kontextbezogene Weise anzuzeigen. Sie werden auch für kürzere Formulare oder Benutzereingaben verwendet. In der Regel sollte die in einem Dialogfeld gehostete Benutzeroberfläche einfach und für eine kleinere Rendering-Oberfläche gut geeignet sein. Für längere Formulare oder komplexere Interaktionsmodelle, wie z. B. das Menüband, empfiehlt sich dagegen die Verwendung ganzseitiger Formulare.
    
  
- **Legenden:** Diese geben wichtige kontextbezogene Informationen und Aktionen zu einem bestimmten Element an. Legenden werden in der Regel verwendet, um dem Benutzer in einer kleinen Benutzeroberfläche weitere Informationen oder Aktionen zu einem Element anzugeben. Wenn Bildlaufleisten oder Benutzereingaben erforderlich sind, ist die Legende möglicherweise keine gute Wahl.
    
  

### Animation

Animation kann zwar zu einer lebendigeren und interessanteren Benutzeroberfläche führen, Sie sollten jedoch eine übermäßige Verwendung in Ihrer Benutzeroberfläche vermeiden. Gut gemachte Animation wird vom Benutzer kaum bewusst wahrgenommen, sondern vermittelt ihm den Eindruck einer schnelleren, leistungsfähigeren Benutzeroberfläche. Sie sollten bei der Anwendung von Animation unbedingt Konzepte wie Bewegung und Trägheit berücksichtigen und eine Benutzeroberfläche bereitstellen, die natürlich und ansprechend wirkt. Es wird nachdrücklich davon abgeraten, übertriebenen Animationen zu verwenden, wie z. B. übermäßiges Prellen oder Federn oder das Umherfliegen von Objekten auf dem Bildschirm bei der kleinsten Benutzeraktion. Objekte sollten generell auf dem direkten Weg zu ihrem Ziel gelangen und beanspruchen häufig nur die ersten oder letzten zehn Prozent der tatsächlichen Änderung durch die Animation, mit der dem Benutzer der Eindruck einer Bewegung vermittelt werden soll.
  
    
    

### Registerkarten und Pivot-Elemente

Das Menüband ist der einzige Ort in SharePoint, an dem Sie Registerkarten verwenden sollten. An jedem anderen Ort in SharePoint sollten Sie mithilfe von Pivot-Elementen das Konzept der Änderung des Inhaltsbereichs ausdrücken.
  
    
    

## Häufig gestellte Fragen zu Office UI Fabric mit SharePoint-Add-Ins
<a name="Fabric"> </a>

Verwenden Sie diese häufig gestellten Fragen, um zu verstehen, wie Sie Office UI Fabric verwenden und Ihre SharePoint-Add-In wie das restliche Office 365 aussehen und sich verhalten lassen.
  
    
    
 **1. Was ist Office UI Fabric?**
  
    
    
Office UI Fabric ist ein reaktionsfähiges, mobilorientiertes Front-End-Framework, mit dem Sie Webbenutzeroberflächen über die Office-Entwurfssprache erstellen können. Das Tool wird mit einem Schriftartensatz und CSS-Klassen implementiert, die Benutzeroberflächenkomponenten, Symbole, Animation und die offizielle Office-Farbpalette bereitstellen. Ausführliche Informationen finden Sie unter  [Office UI Fabric](https://github.com/OfficeDev/Office-UI-Fabric).
  
    
    
 **2. Kann ich Office UI Fabric in meinen SharePoint-Add-Ins verwenden?**
  
    
    
Ja. Ihre Add-In-Seiten können auf dieselbe Weise auf Office UI Fabric-Dateien verweisen, wie auf andere CSS-Frameworks wie Bootstrap verwiesen wird.
  
    
    
 **3. Wann sollte ich Office UI Fabric mit SharePoint-Add-Ins verwenden?**
  
    
    
Verwenden Sie das Tool, wenn Sie Ihrem Add-In das Aussehen und Verhalten von Office 365 geben möchten. Es ist eine Alternative zur Verwendung der CSS-Datei des SharePoint-Hostwebs.
  
    
    
 **4. Wie kann Office UI Fabric in SharePoint-Add-Ins verwendet werden?**
  
    
    
Fügen Sie einfach die Office UI Fabric-Dateien zu Ihrem Entwicklungsprojekt hinzu, und schließen Sie einen Verweis zur fabric.css-Bibliothek in Ihre HTML- oder ASPX-Seite ein. Ausführliche Informationen finden Sie unter  [Erste Schritte](https://github.com/OfficeDev/Office-UI-Fabric#get-started).
  
    
    
 **5. Wie können Office UI Fabric-Komponenten in SharePoint-Add-Ins verwendet werden?**
  
    
    
Fügen Sie einfach einen Verweis auf die fabric.components.css-Bibliothek zu Ihrer HTML- oder ASPX-Seite hinzu. Ausführliche Informationen finden Sie unter  [Erste Schritte](https://github.com/OfficeDev/Office-UI-Fabric/blob/master/ghdocs/GETTINGSTARTED.md).
  
    
    
 **6. Kann ich Office UI Fabric zusammen mit einer Hostweb-CSS-Datei eines SharePoint-Add-Ins verwenden?**
  
    
    
Derzeit raten wir davon ab, Office UI Fabric mit Hostweb-CSS zu mischen. Damit sollen Konflikte bei Klassennamen und Formatvorlagen vermieden werden.
  
    
    
 **7. Unterstützt Office UI Fabric SharePoint-Designs?**
  
    
    
Nein. Office UI Fabric bietet keine Unterstützung für SharePoint-Designs. Das Anwenden von Office UI Fabric-Designs führt jedoch nicht zu Konflikten mit SharePoint-Designs.
  
    
    

## Erweitern der SharePoint-Benutzeroberfläche in Add-Ins
<a name="UXGuide_Extending"> </a>

In SharePoint kann die bestehende Benutzeroberfläche durch Add-Ins erweitert werden. Dadurch können Sie Ihr Add-In dort bereitstellen, wo Benutzer es benötigen. Sie können die Benutzeroberfläche der Hostwebsite mit den folgenden Methoden erweitern:
  
    
    

- **Add-In-Parts:** Ermöglichen Ihnen, ein **iframe**-Element bereitzustellen, das Inhalt Ihres Add-Ins enthält.
    
  
- **Benutzerdefinierte Aktionen:** Sie können das Menüband oder das Kontextmenü durch benutzerdefinierte Aktionen erweitern. Benutzerdefinierte Aktionen machen Ihr Add-In in Listenelementen oder Dokumenten verfügbar, oder überall dort, wo das Menüband angezeigt wird.
    
  

### Hinzufügen von Add-In-Parts zur Hostwebsite

Parts bieten für Ihr Add-In eine Möglichkeit, Informationen oder einen kleinen Interaktionspunkt in der Hostwebsite, auf der das Add-In installiert ist, anzuzeigen. Endbenutzer können diese Parts in ihre Seiten integrieren, indem Sie das Webpartframework in SharePoint verwenden. Abbildung 3 zeigt das Part Tag-Cloud als Beispiel für ein Part.
  
    
    

**Abbildung 3. Das Part Tag-Cloud**

  
    
    

  
    
    
![Das Part Tag-Cloud](images/AppUXGuidelines_part.png)
  
    
    
Der Titel des Parts in Abbildung 3 ist **Tag-Cloud aus Add-In UX Design**. Die Tag-Cloud selbst wird vom Add-In-Inhalt bereitgestellt. Sie ist in einem **iframe**-Element gehostet und von der Hostingseite vollständig isoliert. Da der Add-In-Inhalt die CSS-Datei der Hostwebsite verwendet, fügt sie sich nahtlos in die Hostseite ein.
  
    
    
Einige Benutzeroberflächenarten eignen sich gut für die Bereitstellung durch Part-Benutzeroberflächen. Zum Beispiel möchten Sie vielleicht eine Gruppe von Tastenkombinationen in verschiedenen Bereichen Ihres Add-Ins bereitstellen, oder sogar einen einzelnen Startpunkt, den Benutzer in andere Seiten integrieren können. Eine weitere Möglichkeit wäre, einen kleinen Teilsatz der Daten im Add-In oder die neuesten Änderungen beliebiger Elemente anzuzeigen. Vielleicht möchten Sie eine kleine interaktive Zone bereitstellen, die die Durchführung von schnellen Aktionen mit dem Add-In ermöglicht, ohne dass es dazu geöffnet werden muss. Welchen Part-Typ Sie wählen, wird von den Szenarien bestimmt, die Ihr Add-In unterstützt. Bedenken Sie immer, dass nicht für alle Add-Ins Parts erforderlich sind. Stellen Sie Parts nur bereit, wenn sie für die Gesamtwirkung sinnvoll sind.
  
    
    
Die Seite, die Sie innerhalb des Parts anzeigen, wird in einem **iframe**-Element gehostet. Stellen Sie daher sicher, dass dies dem von Ihnen geschriebenen JavaScript bekannt ist und es auf Objekte, wie z. B. das Fensterobjekt, zugreifen kann. Selbst wenn der Rest Ihres Add-Ins durch ausgeprägtes Branding gekennzeichnet ist, sollten Sie in Betracht ziehen, für Ihr Part die Formate der Hostwebsite zu übernehmen. Denn dieses wird in die Seiten der Hostwebsite integriert und sieht weder harmonisch noch ansprechend aus, wenn es nicht dazu passt. Um die Formate der Hostwebsite zu übernehmen, müssen Sie den Link zur CSS-Standarddatei manuell erstellen. Weitere Informationen finden Sie unter  [Vorgehensweise: Verweisen auf die CSS-Datei der Hostwebsite](sharepoint-add-ins-ux-design-guidelines.md#UXGuide_CSSHowto) in diesem Artikel. Auf der Seite darf sich auch kein Chromsteuerelement befinden, denn es wird in eine Seite integriert, die bereits über ein Chromsteuerelement verfügt.
  
    
    
Da die Seite in einem **iframe**-Element über verschiedene Domänen hinweg einwandfrei funktionieren soll, müssen Sie sicherstellen, dass Sie nicht "Same-Origin-Only" für X-Frame-Optionen dieser Seite angeben. SharePoint-Seiten geben standardmäßig an, dass sie sich in einem **iframe**-Element innerhalb einer Domäne befinden müssen. Für Seiten, die in SharePoint gehostet werden, müssen Sie daher für die Seiten, die Sie in Parts anzeigen möchten, dieses Verhalten deaktivieren. Dazu fügen Sie an einer beliebigen Stelle auf der Seite das Webpart **AllowFraming** hinzu, wie im folgenden Beispiel dargestellt.
  
    
    



```

<WebPartPages:AllowFraming ID="AllowFraming1" runat="server" />
```

Da Sie nicht erzwingen können, in welche Domänen Ihre Seiten per iFrame gelangen, sind die von Ihnen in Add-In-Parts gehosteten Seiten anfällig für Sicherheitsangriffe durch Clickjacking, Seiten können sich in einem iFrame auf einer schädlichen Seite befinden, und Benutzer könnten dazu verleitet werden, auf Schaltflächen zu klicken und Aktionen durchzuführen, derer sie sich nicht bewusst sind. Wenn Sie Ihre Seite planen, sollten Sie dies berücksichtigen und sicherstellen, dass Sie auf der Seite für das Part keine Funktion exponieren, die gefährlich wäre, wenn sie auf einer schädlichen Seite angezeigt wird.
  
    
    
Obwohl Benutzer manuell eine andere Größe für Ihr Part einstellen können, können Sie in der Part-Definition eine bestimmte Größe für das Part festlegen. Außerdem können Sie über **postmessages** anfordern, dass die Größe Ihres Parts dynamisch angepasst wird. Als Standard wird empfohlen, für das Part eine Größe in Schritten von 30 px (z. B. 150 px oder 210 px zu wählen. Wenn dann Parts verschiedener Add-Ins gemischt auf einer Seite vorhanden sind, kann der Benutzer trotzdem den Eindruck vermittelt bekommen, dass jedes dieser Parts speziell für diesen Ort erstellt wurde. Wenn Ihr Part eine Kachel der Erste Schritte-Funktion imitieren soll, muss es eine Höhe und Breite von 150 px haben. Wenn das Part in einer Spalte angezeigt werden soll, um Details zu zeigen, sollte es eine Breite von 300 px haben.
  
    
    
Wenn Ihr Part dynamischen Inhalt anzeigt, empfiehlt es sich, eine Größenanpassung anzufordern, um das Einbetten von Bildlaufleisten auf einer Seite zu reduzieren. Das folgende Beispiel zeigt, wie Sie **postmessages** verwenden, um die Größe des Parts anzupassen:
  
    
    



```
window.parent.postMessage('<message senderId={your ID}>resize(120, 300)</message>', {hostweburl});
```

Im obigen Beispiel wird der **senderId**-Wert in der Abfragezeichenfolge der Seite automatisch durch den Add-In-Webpart-Code festgelegt, wenn die Seite gerendert wird. Ihre Seite liest dann einfach den **SenderId**-Wert aus der Abfragezeichenfolge und verwendet ihn beim Anfordern einer Größenanpassung. Sie können die Hostweb-URL aus der Abfragezeichenfolge abrufen, indem Sie in der Definition des Add-In-Webparts die Token **StandardTokens** oder **HostUrl** an das **Src** -Attribut anfügen.
  
    
    
Um ein Part für die Hostwebsite anzugeben, müssen Sie ein Client-Webpart in der Feature-Datei im Add-In-Paket angeben (nicht die Feature-Datei im WSP-Paket). Sie können ein Part erstellen, das vom Endbenutzer konfigurierbar ist, z. B. durch Angabe einer Postleitzahl. Das folgende Markup spezifiziert ein Add-In-Part, und das Element **Properties** ist optional:
  
    
    



```XML
<ClientWebPart
    Name="Sample Add-in Part" 
    DefaultWidth="600" 
    DefaultHeight="300" 
    Title="Sample Add-in Part" 
    Description="This is a sample part with properties.">
    <Content Type="html" Src="~appWebUrl/Pages/Part.aspx?Property1=_prop1_&amp;amp;Property2=_prop2_&amp;amp;Property3=_prop3_&amp;amp;Property4=_prop4_" />
    <Properties>
        <Property 
            Name="prop1" 
            Type="string" 
            WebBrowsable="true" 
            WebDisplayName="First Property" 
            WebDescription="Description 1" 
            WebCategory="Custom Properties" 
            DefaultValue="String Property" 
            RequiresDesignerPermission="true" />
        <Property 
            Name="prop2" 
            Type="boolean" 
            WebBrowsable="true" 
            WebDisplayName="Second Property" 
            WebDescription="Description 2" 
            WebCategory="Custom Properties" 
            DefaultValue="TRUE" 
            RequiresDesignerPermission="true" />
        <Property 
            Name="prop3" 
            Type="int" 
            WebBrowsable="true" 
            WebDisplayName="Third Property" 
            WebDescription="Description 3" 
            WebCategory="Custom Properties" 
            DefaultValue="1" 
            RequiresDesignerPermission="true" />
        <Property 
            Name="prop4" 
            Type="enum" 
            WebBrowsable="true" 
            WebDisplayName="Fourth Property" 
            WebDescription="Description 4" 
            WebCategory="Custom Properties" 
            DefaultValue="one" 
            RequiresDesignerPermission="true" >
            <EnumItems>
                <EnumItem Value="one" WebDisplayName="One" />
                <EnumItem Value="two" WebDisplayName="Two" />
                <EnumItem Value="three" WebDisplayName="Three" />
            </EnumItems>
        </Property>
    </Properties>
</ClientWebPart>
```

In Ihrem **ClientWebPart**-Element können Sie Folgendes angeben:
  
    
    

- **Name:** Ein interner Name, der zum Identifizieren des Add-Ins verwendet wird. Der Name muss eindeutig sein.
    
  
- **DefaultWidth/DefaultHeight:** Die Standardgröße des Webparts. Bei Bedarf können Sie die Größe der Seite innerhalb des Parts anpassen.
    
  
- **Title:** Der Name, der für Endbenutzer angezeigt wird, wenn diese Ihr Part mit dem Feature zum Hinzufügen von Webparts zu einer Seite hinzufügen.
    
  
- **Description:** Die Beschreibung, die für Endbenutzer angezeigt wird, wenn diese Ihr Part mit dem Feature zum Hinzufügen von Webparts zu einer Seite hinzufügen.
    
  
Sie können Part-Eigenschaften vom Typ **string**, **enum**, **int** und **Boolean** angeben. Sie können die **toolpart**-Kategorie angeben, in der Ihre Eigenschaften angezeigt werden sollen, indem Sie das Attribut **WebCategory** verwenden. Die Attribute für das **Property**-Element, die Sie angeben müssen, lauten wie folgt:
  
    
    

- **Name:** Der Name, der verwendet wird, um diese Eigenschaft an einen Token in der Abfragezeichenfolge anzupassen, der ersetzt werden soll.
    
  
- **WebDisplayName:** Der Name, der im Toolpart verwendet wird.
    
  
- **WebCategory:** Das Toolpart in dem Toolbereich, dem diese Eigenschaft hinzugefügt werden soll.
    
  
- **Type:** Der Eingabedatentyp, der vom Benutzer erwartet wird. Der Typ kann **string**, **enum**, **int** oder **Boolean** sein.
    
  
- **DefaultValue:** Der Standardwert für Ihre Eigenschaft.
    
  
Wenn das Part der Seite hinzugefügt wird, werden alle Zeichenfolgen in der Abfragezeichenfolge, die mit dem Muster _propertyName_ übereinstimmen, automatisch durch den Wert der Eigenschaft mit diesem Namen in der Webpartinstanz ersetzt, oder dem Standardnamen, wenn der Benutzer ihn nicht festgelegt hat. Anschließend führen Sie Code innerhalb der Seite aus, um die Abfragezeichenfolge zu parsen und die Eigenschaften zu extrahieren, um sie beim Rendering und bei Interaktionen auf Ihrer Seite zu verwenden.
  
    
    
Sie können auch veranlassen, dass die Webpart-ID in der Abfragezeichenfolge gesendet wird, indem Sie mithilfe der Zeichenfolge _wpid_ darstellen, wo in der Abfragezeichenfolge sie ersetzt werden soll. Dies kann bei der Unterscheidung verschiedener Part-Instanzen hilfreich sein, wenn Sie Informationen über Benutzerauswahlen oder -interaktionen auf einer Pro-Instanz-Basis speichern möchten. Weitere Informationen finden Sie unter  [Erstellen von Add-In-Webparts zur Installation mit Ihrem SharePoint-Add-In](create-add-in-parts-to-install-with-your-sharepoint-add-in.md).
  
    
    

### Hinzufügen von benutzerdefinierten Aktionen zur Hostwebsite

Wenn Sie über Funktionen verfügen, die sinnvoll im Kontext von Listenelementen oder Dokumenten oder auf bestimmten Menübandregisterkarten in der Hostwebsite angezeigt werden könnten, können Sie sie dem Kontextmenü oder dem Menüband mithilfe von benutzerdefinierten Aktionen hinzufügen. Um benutzerdefinierte Aktionen auf der Hostwebsite anzuzeigen, müssen Sie sie in der gleichen Art von loser Feature-Datei im Add-In-Paket definieren, wie die Datei, die **ClientWebPart**-Definitionen enthält.
  
    
    

**Abbildung 4. Eine benutzerdefinierte Aktion im Kontextmenü**

  
    
    

  
    
    
![Eine benutzerdefinierte Aktion im Kontextmenü](images/AppUXGuidelines_ECBcustomaction.png)
  
    
    
Der Code für benutzerdefinierte Aktionen, die in der Hostwebsite angezeigt werden, ist gleich wie in früheren Versionen von SharePoint, jedoch mit folgenden Einschränkungen:
  
    
    

- Das **Location**-Attribut muss entweder **CommandUI.Ribbon** oder **EditControlBlock** sein.
    
  
- **CustomAction** darf kein JavaScript enthalten:
    
  - Alle **UrlActions** oder **CommandActions** müssen eine URL sein, zu der navigiert werden kann. Die URL kann zusätzlich zu den App-spezifischen Token mit normalen Token für benutzerdefinierten Aktionen parametrisiert werden.
    
  
  - **EnabledScript** ist bei Menübandanpassungen nicht zulässig.
    
  
Normalerweise wird ein Benutzer bei Auswahl einer benutzerdefinierten Aktion zu der URL navigiert, die Sie mit den Token angegeben haben, die basierend auf der Benutzerauswahl aufgelöst wurden. In einigen Fällen soll der Benutzer jedoch auf der Seite bleiben, z. B. für schnelle Aktionen an einem bestimmten Dokument. Wenn Ihre benutzerdefinierte Aktion ein Dialogfeld öffnen soll anstatt Navigation zu ermöglichen, fügen Sie dem **CustomAction**-Element die folgenden Attribute hinzu.
  
    
    



```

HostWebDialog="TRUE"
HostWebDialogHeight="500" 
HostWebDialogWidth="500"
```

Das Attribut **HostWebDialogHeight** und das Attribut **HostWebDialogWidth** sind optional. Wenn die Attribute nicht angegeben werden, wird die Standardgröße für ein Dialogfeld in SharePoint verwendet. Sie sollten jedoch generell die Größe Ihres Dialogfelds angeben, damit es passend aussieht und nicht mit Bildlaufleisten für den Benutzerangezeigt wird.
  
    
    
Das Dialogfeld enthält immer eine Schaltfläche **Schließen** im Dialogfeld-Chrom. Sie können auf Ihrer Seite auch Schaltflächen hinzufügen, die das Dialogfeld schließen und der Ursprungsseite mitteilen, ob sie aktualisiert werden muss. Wenn Sie einen Schritt durchgeführt haben, der sich auf die Ansicht auswirken könnte, die der Benutzer anzeigt (z. B. das Aktualisieren von Eigenschaften in einem Dokument), sollten Sie die Seite aktualisieren. Wenn Sie dagegen keine Aktualisierung vorgenommen haben, sondern nur eine "Abbrechen"-Aktion durchgeführt oder eine Datei an ein Archiv gesendet haben, ohne Eigenschaften zu aktualisieren, können Sie der Seite mitteilen, dass keine Aktualisierung erforderlich ist. Die folgenden Beispiele zeigen, wie Sie POST-Meldungen senden können, um das Dialogfeld zu schließen.
  
    
    



```

window.parent.postMessage('CloseCustomActionDialogRefresh', '*');
window.parent.postMessage('CloseCustomActionDialogNoRefresh', '*');
```

Je nachdem, ob Sie **CloseCustomActionDialogRefresh** oder **CloseCustomActionDialogNoRefresh** verwenden, wird das Dialogfeld geschlossen und aktualisiert die zugehörige Seite oder nicht.
  
    
    
Sie können dem Menüband der Hostwebsite von Ihrem Add-In aus keine benutzerdefinierte Registerkarte hinzufügen. Sie können nur benutzerdefinierte Gruppen oder einzelne Steuerelemente hinzufügen. Überschreiben Sie keines der standardmäßigen Steuerelemente im SharePoint-Menüband. Ihre Steuerelemente und die SharePoint-Steuerelemente sollten sich nebeneinander befinden.
  
    
    
Wenn einige Ihrer Steuerelemente in einem Zusammenhang miteinander stehen oder der Benutzer sie in Zusammenhang mit der Verwendung Ihres Add-Ins empfindet, sollten Sie diese in einer eigenen benutzerdefinierten Gruppe zusammenfassen, damit der Benutzer sie leichter finden kann. Wenn dagegen die von Ihnen hinzugefügten Funktionen mit großer Wahrscheinlichkeit vom Benutzer als Teil der Gesamtwirkung seiner Website betrachtet, sollten Sie versuchen, das betreffende Steuerelement an einer logischen Position in den bestehenden Menübandpositionen einzufügen. Weitere Informationen finden Sie unter  [Gewusst wie: Erstellen benutzerdefinierter Aktionen zur Bereitstellung mit Add-Ins für SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

## Bereitstellen einer Einstellungsseite für die Add-In-Konfiguration
<a name="UXGuide_Settings"> </a>

In vielen Fällen ist sinnvoll, Ihr Add-In über Konfigurationsinformationen verfügen zu lassen, die der Benutzer ändern kann, und diese Informationen über eine Einstellungsseite bereitzustellen. Idealerweise wählen Sie vernünftige Standardwerte für diese Einstellungen, und Benutzer können dann zur Einstellungsbenutzeroberfläche wechseln, wenn sie diese Standardwerte ändern müssen. In einigen Fällen erfordert das Add-In die Angabe bestimmter Informationen oder die Auswahl von Optionen, ehe es funktioniert. Wenn für Ihr Add-In Informationen erforderlich sind, bevor es funktionieren kann, sollten Sie eine benutzerfreundliche Möglichkeit vorsehen, mit der der Benutzer zur Einstellungsseite geleitet wird, um die Konfiguration zu aktualisieren.
  
    
    
Fügen Sie ggf. die URL der Einstellungsseite im rechten oberen Menü dem Add-In hinzu, damit Benutzer sie problemlos finden können. Wenn Ihr Add-In über eine Erste Schritte-Funktion oder andere Einstellungen verfügt, können Sie diese ebenfalls hinzufügen. Weitere Informationen finden Sie unter  [Verwenden des Client-Chromsteuerelements in Add-Ins für SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md).
  
    
    
Sie sollten auch berücksichtigen, dass der Benutzer, der Ihr Add-In besucht, möglicherweise nicht in der Lage ist, es zu konfigurieren. Auch Ihre Benutzeroberfläche sollte nicht voraussetzen, dass der aktuelle Benutzer die Konfiguration durchführen kann. Ihr Add-In sollte den Benutzer führen und ihm ermöglichen, die richtige Person zu finden, wenn er die Konfiguration nicht selbst vornehmen kann.
  
    
    

## Verwalten von Benutzerlizenzen in Add-Ins
<a name="UXGuide_License"> </a>

Wenn Ihr Add-In nicht kostenlos ist, sollten Sie ein gutes Gleichgewicht finden zwischen den Features, die im Testmodus bzw. im unlizenzierten Modus verfügbar oder eingeschränkt sind, und der kostenpflichtigen Version.
  
    
    
Wenn Sie eine zeitlich begrenzte Testversion bereitstellen, sollte sie im Testzeitraum gleich wie die kostenpflichtige Version funktionieren. Ermöglichen Sie Benutzern einen realistischen Eindruck davon, was sie bekommen, wenn sie für das Add-In bezahlen. Wenn Sie im Testzeitraum Einschränkungen vorsehen, geben Sie klare Informationen, wie der Benutzer diese aufheben kann, wenn er bezahlt. Bei zeitlich unbegrenzten Testversionen sollten Sie so viele Funktionen freigeben, wie Ihrer Meinung nach erforderlich sind, damit der Benutzer den Wert Ihres Add-Ins gut einschätzen kann. Stellen Sie klar heraus, welche zusätzlichen Vorteile es bringen würde, für das Add-In zu bezahlen.
  
    
    
Wenn Benutzer Ihr Add-In zum ersten Mal sehen, besitzen sie möglicherweise keine Lizenz dafür. Zum Beispiel könnte ein Benutzer Ihr Add-In einer Team-Website hinzufügen, jedoch vergessen, die anderen Benutzer zu lizenzieren. Andere Benutzer der Team-Website würden dann Ihr Add-In ohne Lizenz nutzen, bis der Lizenzmanager die Situation behebt. Stellen Sie sicher, dass Benutzer einen guten Eindruck erhalten, denn dann steigt die Wahrscheinlichkeit, dass eine Lizenz verlangt oder gekauft wird. Es empfiehlt sich, Benutzern grundsätzlich die Anzeige und Navigation der Daten in Ihrem Add-In zu ermöglichen. Stellen Sie heraus, dass eine Lizenz mehr Features bedeutet, weisen Sie jedoch nicht mehr als einmal pro Sitzung darauf hin.
  
    
    
Wenn der Hauptnutzen Ihres Add-Ins im Anzeigen von Daten besteht (und sie diese nicht kostenlos weitergeben möchten), sollten Sie ein begrenztes Subset der Daten verfügbar machen oder die Daten ohne Interaktivität anzeigen. Hindern Sie Benutzer ohne Lizenz nicht daran, Ihr Add-In anzuzeigen. Unlizensierte Benutzer sollten einen Eindruck davon erhalten, welchen Nutzen Ihr Add-In ihnen bringen kann. Dadurch wird die Wahrscheinlichkeit größer, dass sie es kaufen.
  
    
    

### Förderung des Erwerbs von Lizenzen

Wenn unlizenzierte Benutzer bzw. Benutzer der Testversion Ihr Add-In verwenden, sollten Sie sie zum Erwerb einer Volllizenz ermutigen:
  
    
    

- Durch eine Statusleiste oben auf der Seite, die den Lizenzstatus angibt.
    
  
- Im Kontext, wenn ein Benutzer versucht, auf Inhalte oder Funktionen zuzugreifen, für die eine Lizenz erforderlich ist.
    
  
Achten Sie sorgfältig darauf, zweite Lizenzwarnungen nicht übermäßig einzusetzen. Es ist für den Benutzer eine bessere Erfahrung, wenn Sie die Statusmeldung der obersten Ebene verwenden und alle unlizenzierten Funktionen deaktivieren, und den Benutzer nicht unangenehm überraschen, weil er einen Schritt nicht durchführen kann. In beiden Fällen sollte Ihre Meldung verbindlich und ermutigend sein und nicht unfreundlich. Sehen Sie einen Link zur Storefront-Add-In-Detailseite vor, auf der der Benutzer eine Lizenz erwerben kann.
  
    
    

### Statusleiste für die Lizenzierung

SharePoint verfügt über eine integrierte Statusleiste, die Sie auf SharePoint-Seiten verwenden können, indem Sie die JavaScript-API aufrufen. Sie können auch das Format der integrierten Statusleiste kopieren. Verwenden Sie gelbe "Warnfarbe" zusammen mit einer für die Benutzersituation geeigneten, wie beispielsweise:
  
    
    

- Für Benutzer einer **zeitlich unbegrenzten Testversion**: Dies ist eine Testversion von _<App-Name>_. Hier können Sie die Vollversion erwerben und  _<bezahlte Funktionen>_ freischalten.
    
  
- Für Benutzer einer **zeitlich begrenzten Testversion, die noch nicht abgelaufen ist**: In _<Zeitraum, ausgedrückt durch eine leicht verständliche Zahlenangabe wie "3 Tage", nicht "73:42:12">_ läuft diese Testversion von _<App-Name>_ ab. Hier können Sie die Vollversion erwerben, um den ganzen Funktionsumfang nutzen zu können.
    
  
- Für Benutzer einer **zeitliche begrenzten Testversion, die abgelaufen ist**: Leider ist der Testzeitraum für diese Testversion von _<App-Name>_ abgelaufen. Hier können Sie die Vollversion erwerben und den gesamten Funktionsumfang nutzen.
    
  
- Für Benutzer **ohne Lizenz**: Leider besitzen Sie keine Lizenz für _<App-Name>_. Hier können Sie die Vollversion erwerben und  _<bezahlte Funktionen>_ aktivieren.
    
  

## Weitere Entwurfsaspekte für SharePoint-Add-Ins
<a name="UXGuide_Other"> </a>

Zusätzlich zu den bereits behandelten Informationen sollten Sie noch folgende Punkte berücksichtigen, wenn Sie Ihr Add-In für SharePoint erstellen.
  
    
    

### Speichern von notwendigen Informationen in Cookies

Ihr Add-In benötigt sehr viele Informationen für die Interaktion mit SharePoint, z. B. die URL der Hostwebsite oder die POST-Meldung mit SharePoint-Anmeldeinformationen. Wenn Informationen in einem Client-Cookie gespeichert werden, muss Ihr Add-In nicht immer wieder diese Informationen erneut von SharePoint anfordern, sodass sich für den Endbenutzer eine angenehmere effizientere Nutzungserfahrung ergibt.
  
    
    

### Anfordern eines neuen OAuth-Tokens

Wenn Ihr Add-In über keine Anmeldeinformationen verfügt, können Sie ein neues Token anfordern. Leiten Sie dazu den Benutzer mit Ihrer Add-In-ID und der URL, die er aufrufen möchte, zur Umleitungsseite. Die URL muss sich unter der Domäne der Umleitungs-URL befinden, die für die von Ihnen verwendete OAuth-App-ID registriert ist. Die folgende URL bietet ein Beispiel für die Vorgehensweise zum Umleiten Ihrer Add-In-Benutzer. (Platzhalter stehen in Klammern.)
  
    
    
 `{hostWebURL}/_layouts/15/appredirect.aspx?client_id={OAuth_app_ID}&amp;redirect_uri={redirectUrl}`
  
    
    

### Überprüfen des schreibgeschützten Modus auf SharePoint-Websites

Aufgrund von Aktualisierungen oder Wartungsarbeiten an der Website kann sich SharePoint manchmal im schreibgeschützten Modus befinden, wenn der Benutzer auf Ihr Add-In zugreift. Wenn Sie die Bearbeitung von SharePoint-Daten durch den Benutzer zulassen möchten, müssen Sie sicherstellen, dass Sie dem Benutzer keine Änderungen erlauben, die nicht auf den Server zurückgespeichert werden können. Deaktivieren Sie die Bearbeitungsbenutzeroberfläche im schreibgeschützten Modus. Um zu überprüfen, ob sich die Website im schreibgeschützten Modus befindet, können Sie diese API aufrufen:
  
    
    
 `{hostWebUrl}/_api/site/ReadOnly`
  
    
    

## Weitere Informationsquellen
<a name="bk_addresources"> </a>


-  [UX-Design für SharePoint-Add-Ins](ux-design-for-sharepoint-add-ins.md)
    
  
-  [Erstellen von UX-Komponenten in SharePoint 2013](create-ux-components-in-sharepoint-2013.md)
    
  
-  [Verwenden des Stylesheets einer SharePoint-Website in Add-Ins für SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)
    
  
-  [Verwenden des Client-Chromsteuerelements in Add-Ins für SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [Erstellen von Add-In-Webparts zur Installation mit Ihrem SharePoint-Add-In](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  
-  [Gewusst wie: Erstellen benutzerdefinierter Aktionen zur Bereitstellung mit Add-Ins für SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)
    
  
-  [Anpassen einer Listenansicht in Add-Ins für SharePoint durch clientseitiges Rendering](customize-a-list-view-in-sharepoint-add-ins-using-client-side-rendering.md)
    
  

  
    
    

