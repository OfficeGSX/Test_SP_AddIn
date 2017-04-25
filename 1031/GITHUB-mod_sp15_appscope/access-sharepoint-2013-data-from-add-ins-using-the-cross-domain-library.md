---
title: Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek
ms.prod: SHAREPOINT
ms.assetid: bc37ff5c-1285-40af-98ae-01286696242d
---



# Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek
Erfahren Sie mehr über das Zugreifen auf Daten einer SharePoint-Website von einem Add-In mithilfe der domänenübergreifenden Bibliothek in SharePoint 2013.
Beim Erstellen von SharePoint-Add-Ins müssen Sie in der Regel Daten aus verschiedenen Quellen einbinden. Aus [Sicherheitsgründen](http://msdn.microsoft.com/library/cc709423.aspx) gibt es jedoch Blockierungsmechanismen, die die Kommunikation mit mehr als einer Domäne zur gleichen Zeit verhindern. Diese Sicherheitsmechanismen sind in den meisten Browsern implementiert und unterbinden nahezu clientseitige domänenübergreifende Aufrufe.
  
    
    

Abbildung 1 zeigt eine blockierte domänenübergreifende Anforderung.
**Abbildung 1. Blockierte domänenübergreifende Anforderung**

  
    
    
Wenn ein Benutzer eine Webseite aus Ihrer Add-In-Domäne (1) anfordert, ist die clientseitige Kommunikation nur an diese Domäne gebunden. Ihr Add-In kann clientseitige Aufrufe von der Webseite nur zu anderen Ressourcen in derselben Domäne machen. Add-Ins fordern normalerweise aber Ressourcen von anderen Domänen an, wie der SharePoint-Domäne, um ihre Szenarien zu erfüllen. Im Code auf Ihrer Webseite können Sie versuchsweise eine Anforderung an die SharePoint-Domäne (2) senden, die vom Browser blockiert wird. Normalerweise sehen Sie die Fehlermeldung **Zugriff verweigert**. Der Fehler impliziert aber nicht, dass Sie keine Berechtigungen für die angeforderten Ressourcen haben, sondern Sie können in der Regel nur keine Anforderung an die genannten Ressourcen senden.Wenn Sie die domänenübergreifende Bibliothek verwenden, können die Webseiten in Ihrem Add-In auf Daten in der Add-In-Domäne und in der SharePoint-Domäne zugreifen. Die domänenübergreifende Bibliothek ist eine clientseitige Alternative in Form einer auf der SharePoint-Website gehosteten JavaScript-Datei (SP.RequestExecutor.js), auf die Sie in Ihrem Remote-Add-In verweisen können. Die domänenübergreifende Bibliothek ermöglicht Ihnen, über einen Proxy auf der Remote-Add-In-Seite mit mehreren Domänen zu interagieren. Diese Option ist geeignet, wenn Sie den Add-In-Code auf dem Client statt auf dem Server ausführen möchten oder wenn Konnektivitätsbarrieren, z. B. Firewalls, zwischen SharePoint und der Remoteinfrastruktur bestehen. Sie können auf Daten im Hostweb zugreifen - beispielsweise können Sie auf Listen zugreifen, mit denen Endbenutzer unabhängig von Ihrem Add-In interagieren. Oder Sie können auf Daten im Add-In zugreifen, wie Listen, die speziell für Ihr Add-In bereitgestellt wurden. Add-Ins mit Mandantenbereich können zudem auf andere Websitesammlungen und Websites zugreifen, sofern das Add-In über die erforderlichen Berechtigungen verfügt und als Batchinstallation mittels des Add-In-Katalogs bereitgestellt wurde.
> **HINWEIS**
> In diesem Thema bezieht sich **Add-In-Domäne** auf die Domäne, die die Add-In-Seiten hostet. Dies kann die Domäne einer Remote-Webanwendung in einem Add-In, die vom Anbieter gehostet wird, sein, oder Add-In-Seiten können sich auch in SharePoint im Add-In-Web befinden und Aufrufe an die Hostwebdomäne machen. In diesem Fall ist die Add-In-Domäne die Domäne des Add-In-Webs.
  
    
    

Das Hauptbeispiel in diesem Artikel zeigt Ihnen, wie Sie ein Add-In erstellen können, die Daten im Add-In-Web liest und diese in einer Webseite anzeigt. Der Abschnitt  [Nächste Schritte](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Next) zeigt weitere Szenarien an, die auf dem Hauptbeispiel aufbauen.
## Voraussetzungen für die Verwendung der Beispiele in diesem Artikel
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Um diese Beispiele auszuführen, benötigen Sie Folgendes:
  
    
    

-  [Visual Studio 2012](https://www.microsoft.com/de-de/download/details.aspx?id=30682)
    
  
-  [Microsoft Office Developer Tools für Visual Studio 2012](https://msdn.microsoft.com/de-de/office/aa905340.aspx)
    
  
- Eine SharePoint 2013-Entwicklungsumgebung (App-Isolierung für lokale Szenarien erforderlich)
    
  

## Lesen von Daten im Add-In-Web unter Verwendung der domänenübergreifenden Bibliothek
<a name="SP15Accessdatafromremoteapp_Codeexample"> </a>

In diesem Beispiel wurde eine einfache Webseite außerhalb von SharePoint gehostet, die einen Representational State Transfer- (REST-)Endpunkt benötigt, um Daten einer SharePoint-Website (Add-In-Web) zu lesen. Da die domänenübergreifende Bibliothek ein Add-In-Web erfordert, sollten Sie mit diesem Szenario beginnen.
  
    
    
Gehen Sie wie folgt vor, um Daten im Add-In-Web zu lesen:
  
    
    

1. Erstellen Sie ein SharePoint-Add-In-Projekt und Webprojekte.
    
  
2. Erstellen Sie Listenelemente im Add-In-Web. Mit diesem Schritt stellen Sie auch sicher, dass ein Add-In-Web erstellt wird, wenn Benutzer das Add-In bereitstellen.
    
  
3. Erstellen Sie eine Add-In-Seite, die mithilfe der domänenübergreifenden Bibliothek die Listenelemente liest.
    
  
Abbildung 2 zeigt eine Webseite, die Daten im Add-In-Web anzeigt.
  
    
    

**Abbildung 2. Webseite, die Daten im Add-In-Web anzeigt**

  
    
    

  
    
    
![Beispielergebnisbildschirm für domänenübergreifende Leseelemente](images/CrossDomainReadItemsResult.png)
  
    
    

### Erstellen Sie ein SharePoint-Add-In-Projekt und Webprojekte.


1. Öffnen Sie Visual Studio 2012 als Administrator. (Klicken Sie hierzu im Menü **Start** mit der rechten Maustaste auf das Symbol Visual Studio 2012, und wählen Sie **Als Administrator ausführen** aus.)
    
  
2. Erstellen Sie ein neues Projekt unter Verwendung der Vorlage **Add-In für SharePoint 2013**.
    
    Die Vorlage **Add-In für SharePoint 2013** in Visual Studio 2012 befindet sich unter **Vorlagen** **>** **Visual C#**, **Office SharePoint** **>** **Add-Ins**.
    
  
3. Geben Sie die URL der SharePoint-Website an, die Sie für das Debugging verwenden möchten.
    
  
4. Wählen Sie **Vom Anbieter gehostet** als Hostingoption für Ihr Add-In.
    
    > **HINWEIS**
      > Sie können die domänenübergreifende Bibliothek auch in einem Von SharePoint gehostetes Add-In verwenden. In einem Von SharePoint gehostetes Add-In befindet sich die Add-In-Seite jedoch bereits im Add-In-Web. In diesem Fall benötigt sie nicht die domänenübergreifende Bibliothek zum Lesen von Listenelementen. Weitere Informationen zu einem Von SharePoint gehostetes Add-In-Beispiel, das Daten im Hostweb lesen kann, finden Sie unter  [Verwenden der domänenübergreifenden Bibliothek in einem von SharePoint gehosteten Add-In (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814) oder unter [Zugreifen auf Daten in einem Hostweb](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) später in diesem Artikel.

### Erstellen von Listenelementen im Add-In-Web


1. Klicken Sie im **Projektmappen-Explorer** auf das SharePoint-Add-In-Projekt. Wählen Sie **Hinzufügen** **>** **Neues Element…**.
    
  
2. Wählen Sie **Visual C# Items** **>** **Office/SharePoint** **>** **Liste**. Geben Sie Ihrer Liste den Namen **Ankündigungen**.
    
  
3. Doppelklicken Sie auf **Ankündigungen** **>** **Elements.xml**. Fügen Sie die folgenden XML-Knoten als untergeordnete Objekte des **ListInstance** -Elements ein.
    
  ```
  
<Data>
    <Rows>
        <Row>
            <Field Name="Title">Lorem ipsum 1</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
        <Row>
            <Field Name="Title">Lorem ipsum 2</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
    </Rows>
</Data>
  ```


### So fügen Sie eine neue Webseite hinzu, die die domänenübergreifende Bibliothek verwendet


1. Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei **Default.aspx**.
    
  
2. Kopieren Sie den folgenden Code, und fügen Sie ihn in die die Datei "Default.aspx" ein. Der Code führt die folgenden Aufgaben aus:
    
  - Laden der jQuery-Bibliothek aus dem Microsoft CDN.
    
  
  - Stellt einen Platzhalter für das Ergebnis bereit.
    
  
  - Extrahiert die Add-In-Web-URL aus der Abfragezeichenfolge.
    
  
  - Lädt das JavaScript für die domänenübergreifende Bibliothek mithilfe der **getScript**-Funktion in jQuery.
    
    Die Funktion lädt die erforderlichen Ressourcen und geht dann zur angegebenen Funktion über. Sie stellt sicher, dass die domänenübergreifende Bibliothek geladen und verfügbar für den nachfolgenden Code ist.
    
  
  - Instantiiert das **RequestExecutor**-Objekt. Standardmäßig verwendet RequestExecutor das Add-In-Web als Kontextwebsite.
    
    > **HINWEIS**
      > Sie können die Kontextwebsite durch andere Websites, die sich von der Add-In-Web unterscheiden, mithilfe des **AppContextSite**-Endpunkts (REST) oder -Objekts (JSOM) ersetzen. Weitere Informationen zu AppContextSite finden Sie in  [Zugreifen auf Daten in einem Hostweb](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) weiter unten in diesem Artikel.
  - Gibt einen REST-Aufruf an den Listenelementendpunkt aus.
    
  
  - Sorgt für einen erfolgreichen Abschluss und zeigt die Listenelemente auf der Webseite an.
    
  
  - Behandelt Fehler und zeigt die Fehlermeldung auf der Webseite an.
    
  

  ```
  
<html>
    <head>
        <title>Cross-domain sample</title>
    </head>
    <body>
        <!-- This is the placeholder for the announcements -->
        <div id="renderAnnouncements"></div>
        <script 
            type="text/javascript" 
            src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
        </script>
        <script type="text/javascript">
          var hostweburl;
          var appweburl;

          // Load the required SharePoint libraries
          $(document).ready(function () {
            //Get the URI decoded URLs.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );

            // resources are in URLs in the form:
            // web_url/_layouts/15/resource
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js files and continue to the successHandler
            $.getScript(scriptbase + "SP.RequestExecutor.js", execCrossDomainRequest);
          });

          // Function to prepare and issue the request to get
          //  SharePoint data
          function execCrossDomainRequest() {
            // executor: The RequestExecutor object
            // Initialize the RequestExecutor with the add-in web URL.
            var executor = new SP.RequestExecutor(appweburl);

            // Issue the call against the add-in web.
            // To get the title using REST we can hit the endpoint:
            //      appweburl/_api/web/lists/getbytitle('listname')/items
            // The response formats the data in the JSON format.
            // The functions successHandler and errorHandler attend the
            //      sucess and error events respectively.
            executor.executeAsync(
                {
                  url:
                      appweburl +
                      "/_api/web/lists/getbytitle('Announcements')/items",
                  method: "GET",
                  headers: { "Accept": "application/json; odata=verbose" },
                  success: successHandler,
                  error: errorHandler
                }
            );
          }

          // Function to handle the success event.
          // Prints the data to the page.
          function successHandler(data) {
            var jsonObject = JSON.parse(data.body);
            var announcementsHTML = "";

            var results = jsonObject.d.results;
            for (var i = 0; i < results.length; i++) {
              announcementsHTML = announcementsHTML +
                  "<p><h1>" + results[i].Title +
                  "</h1>" + results[i].Body +
                  "</p><hr>";
            }

            document.getElementById("renderAnnouncements").innerHTML =
                announcementsHTML;
          }

          // Function to handle the error event.
          // Prints the error message to the page.
          function errorHandler(data, errorCode, errorMessage) {
            document.getElementById("renderAnnouncements").innerText =
                "Could not complete cross-domain call: " + errorMessage;
          }

          // Function to retrieve a query string value.
          // For production purposes you may want to use
          //  a library to handle the query string.
          function getQueryStringParameter(paramToRetrieve) {
            var params =
                document.URL.split("?")[1].split("&amp;");
            var strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
              var singleParam = params[i].split("=");
              if (singleParam[0] == paramToRetrieve)
                return singleParam[1];
            }
          }
        </script>
    </body>
</html>
  ```


### So erstellen Sie die Lösung und führen sie aus


1. Drücken Sie F5.
    
    > **HINWEIS**
      > Wenn Sie F5 drücken, erstellt Visual Studio die Lösung, stellt die App bereit und öffnet die Berechtigungsseite für die App. 
2. Klicken Sie auf die Schaltfläche **Vertrauen**.
    
  
3. Wählen Sie auf der Seite **Websiteinhalte** das Add-In-Symbol.
    
  
Falls Sie herunterladbare Codebeispiel bevorzugen, rufen Sie diese aus der Code Gallery ab. **Codebeispiel: Abrufen von Listenelementen mithilfe der domänenübergreifenden Bibliothek** mit [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain) oder [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain).
  
    
    

**Tabelle 2: Problembehandlung für die Lösung**


|**Wird diese Fehlermeldung angezeigt...**|**Versuchen Sie Folgendes....**|
|:-----|:-----|
|Fehlermeldung: Zugriff auf Ihre Website nicht möglich.  <br/> Es gibt zwar eine Schaltfläche zur Fehlerbehebung, aber sie löst nicht das Problem.  <br/> |Möglicherweise haben Sie ein bekanntes Problem mit Sicherheitszonen in Internet Explorer. Weitere Informationen dazu finden Sie unter  [Arbeiten mit der domänenübergreifenden Bibliothek in verschiedenen Internet Explorer-Sicherheitszonen in Add-Ins für SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md).  <br/> |
|Fehlermeldung: Die erforderlichen Funktionen werden von Ihrem Browser nicht unterstützt. Stellen Sie bitte sicher, dass Sie IE8 oder höher bzw. einen anderen modernen Browser verwenden. Stellen Sie bitte ferner sicher, dass das Metatag "X-UA-Compatible" auf "IE = 8" oder höher festgelegt ist.  <br/> |Die domänenübergreifende Bibliothek erfordert den Dokumentmodus **IE8** oder höher. In einigen Fällen ist der Dokumentmodus standardmäßig auf **IE7** festgelegt. Sie können die Internet Explorer-Entwicklertools verwenden, um den Dokumentmodus der Seite zu ermitteln bzw. zu ändern. Weitere Informationen finden Sie unter [Definieren der Dokumentkompatibilität](http://msdn.microsoft.com/de-de/library/cc288325.aspx).  <br/> |
|Fehlermeldung: "Typ" wurde nicht definiert.  <br/> Ihr Add-In verwendet auch das JavaScript-Objektmodell (JSOM).  <br/> |Das JSOM verwendet die **Type.registerNamespace**-Methode in der Microsoft Ajax-Bibliothek, um den **SP** -Namespace zu registrieren. Verwenden Sie den folgenden Code, um einen Verweis von Ihrer Webseite auf die Microsoft Ajax-Bibliothek zu erstellen: <br/> ```HTML<script type="text/javascript"  src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js"></script>```|
   

## Nächste Schritte
<a name="SP15Accessdatafromremoteapp_Next"> </a>

Dieser Artikel zeigt, wie Sie eine Anforderungen an einen REST-Endpunkten senden, um Daten im Add-In-Web über eine Add-In-Seite zu lesen, die nicht auf SharePoint gehostet ist. Sie können auch die folgenden Szenarien untersuchen und Details zur domänenübergreifenden Bibliothek anzeigen.
  
    
    

### Verwenden des JSOM zum Lesen von Daten im Add-In-Web:
<a name="SP15Accessdatafromremoteapp_JSOM"> </a>

Sie möchten vielleicht lieber das JSOM anstelle von REST verwenden, um Daten aus dem Add-In-Web anzufordern. Sie müssen zusätzliche Tasks ausführen, um die domänenübergreifende Bibliothek zusammen mit JSOM verwenden zu können:
  
    
    

- Verweisen Sie auf SharePoint JSOM auf Ihrer Add-In-Seite.
    
  
- Initialisieren Sie das **ProxyWebRequestExecutorFactory**-Objekt, und legen Sie es als die Factory des Kontextobjekts fest.
    
  
- Greifen Sie auf die SharePoint-Objekte zu, um Daten in der Liste zu lesen.
    
  
- Laden Sie die Objekte in den Kontext, und führen Sie die Abfrage aus.
    
  
Ein Codebeispiel, das zeigt, wie Sie die Tasks ausführen, finden Sie in  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain) Weitere Informationen zur Verwendung von JSOM finden Sie in [Verwenden des JavaScript-Objektmodells (JSOM) in Add-Ins für SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/09/04/using-the-javascript-object-model-jsom-in-apps-for-sharepoint.aspx).
  
    
    

### Zugreifen auf Daten in einem Hostweb
<a name="SP15Accessdatafromremoteapp_Hostweb"> </a>

Das Beispiel auf dieser Seite zeigt, wie Daten im Add-In-Web gelesen werden. Dieses Beispiel ist ein guter Einstieg, da die domänenübergreifende Bibliothek anfangs das Add-In als Kontextwebsite verwendet. Aber es mag viele andere Szenarien geben, in denen Sie auf Daten im Hostweb zugreifen möchten. Dafür sind einige zusätzliche Tasks erforderlich:
  
    
    

- Legen Sie das Hostweb als Kontextwebsite für die domänenübergreifende Bibliothek fest.
    
  
- Stellen Sie ausreichende Berechtigungen für das Add-In bereit.
    
  
Sie können die Kontextwebsite über den **AppContextSite**-Endpunkt (REST) oder das Objekt (JSOM) ändern. Das folgende Beispiel zeigen Ihnen, wie Sie die Kontextwebsite mithilfe des REST-Endpunkts ändern:
  
    
    



```

executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/title?@target='" +
            hostweburl + "'",
        method: "GET",
        headers: { "Accept": "application/json; odata=verbose" },
        success: successHandler,
        error: errorHandler
    }
);
```

Das folgende Codebeispiel zeigt, wie die Kontextwebsite mit JSOM geändert wird:
  
    
    



```

context = new SP.ClientContext(appweburl);
factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
context.set_webRequestExecutorFactory(factory);
appContextSite = new SP.AppContextSite(context, hostweburl);

this.web = appContextSite.get_web();
context.load(this.web);
```

Ihr Add-In hat standardmäßig Berechtigungen für das Add-In-Web, aber nicht für das Hostweb. Das folgende Beispiel zeigt einen Manifestabschnitt, der eine Berechtigungsanforderung zum Lesen von Daten aus dem Hostweb deklariert:
  
    
    



```XML

<AppPermissionRequests>
    <AppPermissionRequest 
        Scope="http://sharepoint/content/sitecollection/web" 
        Right="Read" />
</AppPermissionRequests>
```

Stellen Sie sicher, dass Sie eine Ressource im Add-In-Web erstellen (beispielsweise eine leere Seite oder Liste), um die Bereitstellung des Add-In-Webs zu erzwingen, die für eine Verwendung der domänenübergreifenden Bibliothek erforderlich ist.
  
    
    

### Zugreifen auf Daten in allen Websitesammlungen
<a name="SP15Accessdatafromremoteapp_TenantScope"> </a>

Dank der domänenübergreifenden Bibliothek können Sie auf Daten in allen Websitesammlungen desselben Mandanten zugreifen. Sie müssen aber einige Tasks ausführen, um websitesammlungsübergreifende auf Daten zugreifen zu können:
  
    
    

- Fügen Sie eine Berechtigungsanforderung hinzu, um auf Daten im Mandanten zuzugreifen.
    
  
- Wechseln Sie in Ihrem Code von der Kontextwebsite zu den Websitesammlungen, die Sie abfragen möchten.
    
  
- Fügen Sie das Add-In zum Add-In-Katalog hinzu.
    
  
- Stellen Sie das Add-In als mandantenbereichsbezogenes Add-In auf einer Website bereit. Ein Beispiel, wie Sie ein mandantenbereichsbezogenes Add-In bereitstellen, finden Sie in der Beschreibung des Codebeispiels  [Verwenden der domänenübergreifenden Bibliothek in einem mandantenbereichsbezogenen Add-In (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e).
    
  
Ihr Add-In benötigt auch Zugriffsberechtigungen für Daten aus dem Mandanten. Das folgende Beispiel zeigt einen Manifestabschnitt, der eine Berechtigungsanforderung zum Lesen von Daten aus dem Mandanten deklariert:
  
    
    



```XML

<AppPermissionRequests>
  <AppPermissionRequest 
    Scope="http://sharepoint/content/tenant" 
    Right="Read" />
</AppPermissionRequests>
```

Wenn Sie die Kontextwebsite in Ihrem Code wechseln möchten, verwenden Sie den **AppContextSite**-Endpunkt (REST) oder das Objekt (JSOM), genau so wie im Abschnitt  [Zugreifen auf Daten in einem Hostweb](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) beschrieben. Hier finden Sie zur Erinnerung Informationen zum REST-Endpunkt:/_api/SP.AppContextSite(@target)/web/title?@target='weburl' und ein Beispiel zum Instanziieren des Objekts in JSOM: `appContextSite = new SP.AppContextSite(context, weburl);`.
  
    
    
Als Entwickler können Sie nur mandantenbereichsbezogene Add-Ins aus dem Add-In-Katalog bereitstellen. Den Add-In-Katalog können Sie lokal oder in SharePoint Online-Umgebungen bereitstellen. Das Hochladen Ihres Add-Ins in den Add-In-Katalog ist so einfach wie das Hochladen einer Datei in einer Dokumentbibliothek. Weitere Anweisungen finden Sie unter  [Hinzufügen von benutzerdefinierten Add-Ins zur Add-In-Katalogwebsite](http://office.microsoft.com/de-de/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).
  
    
    
Aus dem Add-In-Katalog können Sie das Add-In auf einer oder mehreren Websites im Mandanten bereitstellen. Da Ihr Add-In über Berechtigungen zum Zugriff auf Daten im Mandanten verfügt, müssen Sie es nur auf einer Website bereitstellen, um Datenzugriff im gesamten Mandanten zu haben. Weitere Anweisungen, wie Sie ein Add-In aus dem Add-In-Katalog bereitstellen, finden Sie unter  [Bereitstellen von benutzerdefinierten Add-Ins](http://office.microsoft.com/de-de/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).
  
    
    
Wenn Sie einen Beispielcode herunterladen möchten, der den Zugriff auf Daten in allen Websitesammlungen zeigt, navigieren Sie zu  [Verwenden der domänenübergreifenden Bibliothek in einem mandantenbereichsbezogenen Add-In (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e).
  
    
    

### Senden von Aufrufen in unterschiedlichen Sicherheitszonen
<a name="SP15Accessdatafromremoteapp_IEZones"> </a>

Die domänenübergreifende Bibliothek verwendet eine Proxyseite, die auf einem **IFrame** auf der Add-In-Seite gehostet ist, um Kommunikation zu ermöglichen. Wenn sich die Add-In-Seite und die SharePoint-Website in verschiedenen Sicherheitszonen befinden, können keine Autorisierungscookies gesendet werden. Wenn keine Autorisierungscookies vorhanden sind und IFrame die Proxyseite zu laden versucht, wird dieser zur SharePoint-Anmeldeseite weitergeleitet. Aus Sicherheitsgründen kann die SharePoint-Anmeldeseite nicht in einem IFrame enthalten sein. In diesen Szenarien kann die Bibliothek die Proxyseite nicht laden, und es ist keine Kommunikation mit SharePoint möglich.
  
    
    
Es gibt aber eine Lösung für diesen Fall. Die Lösung ist das **apphost-Muster**, das darin besteht, die Add-In-Seiten in einer Seite zu verpacken, die im Add-In-Web gehostet ist. Das apphost-Muster eignet sich gut in Add-Ins, die die domänenübergreifende Bibliothek verwenden, selbst wenn keine offensichtlichen Sicherheitsgrenzen vorhanden sind. Weitere Informationen finden Sie unter [Arbeiten mit der domänenübergreifenden Bibliothek in verschiedenen Internet Explorer-Sicherheitszonen in Add-Ins für SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md).
  
    
    

### Zugreifen auf Daten über einen zusätzlichen Remotehost in einer Von SharePoint gehostetes Add-In
<a name="SP15Accessdatafromremoteapp_SPhosted"> </a>

Standardmäßig ist ein Von SharePoint gehostetes Add-In zulässig, um domänenübergreifende Aufrufe an das Hostweb zu senden, vorausgesetzt sie verfügt über die entsprechenden Berechtigungen. Ein Von SharePoint gehostetes Add-In kann aber auch einen Remotehost im **AllowedRemoteHostUrl**-Attribut ihres **AppPrincipal** angeben. Damit können Sie domänenübergreifende Aufrufe aus dem Add-In-Web und von beliebigen anderen Hosts senden.
  
    
    
Wenn Sie ein Codebeispiel eines Von SharePoint gehostetes Add-In herunterladen möchten, das die domänenübergreifende Bibliothek verwendet, navigieren Sie zu  [Codebeispiel: Verwenden der domänenübergreifenden Bibliothek in einem von SharePoint gehosteten Add-In (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814).
  
    
    

## Zusätzliche Ressourcen
<a name="SP15Accessdatafromremoteapp_Addresources"> </a>


-  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)
    
  
-  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)
    
  
-  [Codebeispiel: Abrufen des Hostwebtitels mithilfe der domänenübergreifenden Bibliothek (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Get-the-0ec36bb6)
    
  
-  [Codebeispiel: Abrufen des Hostwebtitels mithilfe der domänenübergreifenden Bibliothek (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)
    
  
-  [Codebeispiel: Verwenden der domänenübergreifenden Bibliothek in einem von SharePoint gehosteten Add-In (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)
    
  
-  [Codebeispiel: Verwenden der domänenübergreifenden Bibliothek in einem mandantenbereichsbezogenen Add-In (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)
    
  
-  [Codebeispiel: Verwenden des Chromsteuerelements und der domänenübergreifenden Bibliothek (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)
    
  
-  [Codebeispiel: Verwenden des Chromsteuerelements und der domänenübergreifenden Bibliothek (JSOM)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)
    
  
-  [Codebeispiel: Verwenden von benutzerdefinierten Aktionen und der domänenübergreifenden Bibliothek zum Bestellen von Büchern](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)
    
  
-  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Arbeiten mit der domänenübergreifenden Bibliothek in verschiedenen Internet Explorer-Sicherheitszonen in Add-Ins für SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  
-  [Erstellen einer benutzerdefinierten Proxyseite für die domänenübergreifende Bibliothek in SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [Abfragen eines Remotediensts mithilfe des Webproxys in SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)
    
  
-  [Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
