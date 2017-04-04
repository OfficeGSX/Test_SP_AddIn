---
title: Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten
In diesem Artikel erfahren Sie, wie Sie grundlegende Erstellungs-, Lese-, Aktualisierungs- und Löschoperationen, auch als CRUD-Operationen (Create, Read, Update, Delete) bezeichnet, mit der SharePoint 2013-REST-Schnittstelle durchführen.
## Entwickeln mit SharePoint-Client-APIs und REST
<a name="ClientAPIs"> </a>

Sie können grundlegende Erstellungs-, Lese-, Aktualisierungs- und Löschoperationen (Create, Read, Update, Delete: CRUD) durchführen, indem Sie die von SharePoint 2013 bereitgestellte REST (Representational State Transfer)-Schnittstelle verwenden. Die REST-Schnittstelle macht alle SharePoint-Entitäten und -Operationen verfügbar, die in den anderen SharePoint-Client-APIs verfügbar sind. Ein Vorteil bei der Verwendung von REST besteht darin, dass Sie keine Verweise auf SharePoint 2013-Bibliotheken oder Client-Assemblys hinzufügen müssen. Statt dessen führen Sie HTTP-Anforderungen an die entsprechenden Endpunkte durch, um SharePoint-Entitäten wie Websites, Listen und Listenelemente abzurufen oder zu aktualisieren. Unter  [Einführung in den SharePoint 2013 REST-Dienst](get-to-know-the-sharepoint-2013-rest-service.md) finden Sie eine ausführliche Einführung zur SharePoint 2013-REST-Schnittstelle und ihrer Architektur.
  
    
    
Unter  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md) und [Arbeiten mit Ordnern und Dateien mit REST](working-with-folders-and-files-with-rest.md) wird die Verwendung von SharePoint-Kernentitäten ausführlich erläutert. Unter [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations) ist ein Beispiel enthalten, das Ihnen zeigt, wie Sie viele dieser Operationen im Kontext einer in C# geschriebenen ASP.NET-Webanwendung durchführen können.
  
    
    
Weitere Einzelheiten über die Gruppen von APIs, die auf der SharePoint 2013-Plattform verfügbar sind, finden Sie unter  [Auswählen des richtigen API-Satzes in SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx). Informationen über die Verwendung der anderen Client-APIs finden Sie unter  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) und [Erstellen von Windows Phone-Apps, die auf SharePoint 2013 zugreifen](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx).
  
    
    

## HTTP-Operationen in SharePoint 2013 REST-Diensten
<a name="HTTPOps"> </a>

Die Endpunkte im SharePoint 2013-REST-Dienst entsprechen den Typen und Mitgliedern in den SharePoint-Clientobjektmodellen. Mithilfe von HTTP-Anforderungen können Sie diese REST-Endpunkte verwenden, um typische CRUD-Operationen ( **Create**, **Read**, **Update** und **Delete**) für SharePoint-Entitäten wie Listen und Websites auszuführen.
  
    
    
In der Regel entsprechen Endpunkte, die **Read**-Operationen darstellen, HTTP- **GET**-Befehlen. Endpunkte, die Aktualisierungsoperationen darstellen, entsprechen HTTP- **POST**-Befehlen, und Endpunkte, die Aktualisierungs- oder Einfügeoperationen darstellen, entsprechen HTTP- **PUT**-Befehlen.
  
    
    
In SharePoint 2013 verwenden Sie **POST**, um Entitäten wie Listen und Websites zu erstellen. Der SharePoint 2013-REST-Dienst unterstützt das Senden von **POST**-Befehlen, die Objektdefinitionen enthalten, an Endpunkte, die Auflistungen darstellen. Sie können beispielsweise einen **POST**-Befehl, der eine neue Listenobjektdefinition in ATOM enthält, an die folgende URL senden, um eine SharePoint-Liste zu erstellen:
  
    
    
 `http://<site url>/_api/web/lists`
  
    
    
Alle nicht erforderlichen Eigenschaften in **POST**-Operationen werden auf ihre Standardwerte festgelegt. Wenn Sie eine schreibgeschützte Eigenschaft im Rahmen einer **POST**-Operation festzulegen versuchen, gibt der Dienst eine Ausnahme zurück.
  
    
    
Verwenden Sie die Operationen **PUT** und **MERGE**, um vorhandene SharePoint-Objekte zu aktualisieren. Jeder Dienstendpunkt, der eine **set**-Operation für eine Objekteigenschaft darstellt, unterstützt **PUT**-Anforderungen und **MERGE**-Anforderungen. Bei **MERGE**-Anforderungen ist das Festlegen von Eigenschaften optional. Alle Eigenschaften, die Sie nicht explizit festlegen, behalten ihre aktuelle Eigenschaft. Bei **PUT**-Befehlen werden jedoch alle Eigenschaften, die Sie nicht explizit festlegen, auf ihre Standardeigenschaften festgelegt. Darüber hinaus gibt der REST-Dienst eine Ausnahme zurück, wenn Sie bei Verwendung von HTTP- **PUT**-Befehlen nicht alle erforderlichen Eigenschaften in Objektaktualisierungen angeben.
  
    
    
Verwenden Sie den HTTP- **DELETE**-Befehl für die spezifische Endpunkt-URL, um das durch den Endpunkt dargestellte SharePoint-Objekt zu löschen. Bei wiederverwendbaren Objekten, wie Listen, Dateien und Listenelementen, führt dies zu einer **Recycle**-Operation.
  
    
    

## Lesen von Daten mit der SharePoint 2013 REST-Schnittstelle
<a name="ReadingData"> </a>

Um die in SharePoint 2013 integrierten REST-Funktionen zu nutzen, erstellen Sie eine RESTful-HTTP-Anforderung mit dem OData-Standard, die der Clientobjektmodell-API entspricht, die Sie verwenden möchten. Jede SharePoint-Entität wird an einem Endpunkt auf der SharePoint 2013-Website verfügbar gemacht, die Sie verwenden möchten, und ihre Metadaten werden entweder im XML- oder JSON-Format dargestellt. Sie können die HTTP-Anforderungen in jeder beliebigen Sprache durchführen, auch in JavaScript und C#.
  
    
    
Um Informationen aus einem REST-Endpunkt zu lesen, müssen Sie die URL des Endpunkts und die OData-Darstellung der SharePoint-Entität kennen, die an diesem Endpunkt verfügbar gemacht wird. Um beispielsweise alle Listen in einer bestimmten SharePoint-Website abzurufen, senden Sie eine **GET**-Anforderung an  `http://<site url>/_api/web/lists`. Sie können zu dieser URL in Ihrem Browser navigieren und das zurückgegebene XML anzeigen. Wenn Sie die Anforderung in Code durchführen, können Sie angeben, ob die OData-Darstellung der Listen im XML- oder JSON-Format erfolgen soll.
  
    
    
Der folgende C#-Code zeigt die Vorgehensweise zum Durchführen der **GET**-Anforderung, die eine JSON-Darstellung aller Listen einer Website mithilfe von JQuery zurückgibt. Dabei wird auch davon ausgegangen, dass Sie einen gültigen OAuth-Zugriffstoken installiert haben, der in der **accessToken**-Variablen gespeichert ist. Sie benötigen den Zugriffstoken nicht, wenn Sie den Aufruf, wie bei einem in SharePoint gehosteten Add-In, innerhalb einer Add-In-Website vornehmen. Beachten Sie, dass Sie den Zugriffstoken nicht aus Code abrufen können, der auf einem Browserclient ausgeführt wird. Sie müssen den Zugriffstoken aus Code abrufen, der auf einem Server ausgeführt wird. Unter  [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) und [OAuth-Ablauf mit Authentifizierungscode für SharePoint-Add-Ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md) wird erläutert, wie Sie einen Zugriffstoken abrufen können.
  
    
    



```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

Diese Anforderung würde etwas anders aussehen, wenn Sie Ihr Add-In in JavaScript schreiben, jedoch die domänenübergreifende SharePoint 2013-Bibliothek verwenden. In diesem Fall müssen Sie keinen Zugriffstoken zur Verfügung stellen. Der folgende Code zeigt, wie die Anforderung aussehen würde, wenn Sie die domänenübergreifende Bibliothek verwenden und die OData-Darstellung der Listen im XML-Format anstatt im JSON-Format erfolgen soll. (Da Atom das Standardantwortformat ist, müssen Sie keinen **Accept**-Header einfügen.) Weitere Informationen über die Verwendung der domänenübergreifenden Bibliothek finden Sie unter  [Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    



```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);
```

Der Code im folgenden Beispiel zeigt, wie Sie eine JSON-Darstellung aller Listen in einer Website anfordern, indem Sie C# verwenden. Das Beispiel geht davon aus, dass Sie über einen OAuth-Zugriffstoken verfügen, den Sie in der  `accessToken`-Variablen speichern.
  
    
    



```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();

```


### Abrufen von Eigenschaften, die nicht mit der Ressource zurückgegeben werden
<a name="NavigationProperties"> </a>

Viele Eigenschaftswerte werden zurückgegeben, wenn Sie eine Ressource abrufen. Bei einigen Eigenschaften müssen Sie allerdings eine **GET**-Anforderung direkt an den Eigenschaftsendpunkt senden. Dies gilt i. d. R. für Eigenschaften, die SharePoint-Entitäten darstellen.
  
    
    
Das folgende Beispiel zeigt, wie Sie eine Eigenschaft abrufen, indem Sie den Namen der Eigenschaft an den Ressourcenendpunkt anhängen. Im Beispiel wird der Wert der Eigenschaft **Author** von einer **File**-Ressource abgerufen.
  
    
    
http:// _<site url>_/_api/web/getfilebyserverrelativeurl('/ _<Ordnername >_/ _<Dateiname>_')/author
  
    
    
Um die Ergebnisse im JSON-Format zu erhalten, fügen Sie einen **Accept**-Header zu  `"application/json;odata=verbose"` hinzu.
  
    
    

## Schreiben von Daten unter Verwendung der REST-Schnittstelle
<a name="WritingData"> </a>

Sie können SharePoint-Entitäten erstellen und aktualisieren, indem Sie RESTful-HTTP-Anforderungen an die entsprechenden Endpunkte erstellen, genauso wie beim Lesen von Daten. Ein wesentlicher Unterschied dabei ist jedoch, dass Sie eine **POST**-Anforderung verwenden. Beim Aktualisieren von Entitäten übergeben Sie außerdem eine **PUT**- oder **MERGE**-HTTP-Anforderungsmethode, indem Sie einen dieser Begriffe den Headern Ihrer Anforderung als Wert des **X-HTTP-Method**-Schlüssels hinzufügen. Die **MERGE**-Methode aktualisiert nur die Eigenschaften der von Ihnen angegebenen Entität, während die **PUT**-Methode die bestehende Entität durch eine neue ersetzt, die Sie im Textkörper der **POST**-Anforderung angeben. Verwenden Sie die **DELETE**-Methode, um die Entität zu löschen. Beim Erstellen oder Aktualisieren einer Entität müssen Sie eine OData-Darstellung der Entität, die Sie erstellen oder ändern möchten, im Textkörper Ihrer HTTP-Anforderung bereitstellen.
  
    
    
Eine weitere wichtige Überlegung beim Erstellen, Aktualisieren und Löschen von SharePoint-Entitäten ist, dass diese Vorgänge auch den Anforderungsformular-Digestwert des Servers als Wert des **X-RequestDigest**-Headers erfordern, wenn Sie nicht OAuth verwenden, um Ihre Anforderungen zu autorisieren. Sie können diesen Wert abrufen, indem Sie eine **POST**-Anforderung mit leerem Textkörper an  `http://<site url>/_api/contextinfo` senden und den Wert des `d:FormDigestValue`-Knotens aus der XML-Datei extrahieren, das der **contextinfo**-Endpunkt zurückgibt. Das folgende Beispiel zeigt eine HTTP-Anforderung an den **contextinfo**-Endpunkt in C#.
  
    
    



```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

Wenn Sie den unter  [Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md) beschriebenen Authentifizierungs- und Autorisierungsablauf verwenden, müssen Sie den Anforderungsdigest nicht in Ihre Anforderungen einschließen.
  
    
    
Wenn Sie die domänenübergreifende JavaScript-Bibliothek verwenden, übernimmt SP.RequestExecutor das Abrufen und Senden des Formulardigestwerts für Sie.
  
    
    
Wenn Sie eine in SharePoint gehostete SharePoint-Add-In erstellen, müssen Sie keine separate HTTP-Anforderung durchführen, um den Formulardigestwert abzurufen. Stattdessen können Sie den Wert im JavaScript-Code aus dem SharePoint einer Seite (wenn die Seite die Standard-Masterseite verwendet) abrufen, siehe folgendes Beispiel, indem die JQuery verwendet und eine Liste erstellt wird.
  
    
    



```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});


```

Das folgende Beispiel zeigt, wie Sie die im vorherigen Beispiel erstellte Liste aktualisieren. In dem Beispiel wird der Titel der Liste geändert, JQuery wird verwendet, und es wird davon ausgegangen, dass Sie diese Operation in einem in SharePoint gehosteten Add-In durchführen.
  
    
    



```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});

```

Geben Sie im Wert des **IF-MATCH**-Schlüssels in den Anforderungs-Headern den **etag**-Wert einer Liste oder eines Listenelements an. Dieser bestimmte Wert gilt nur für Listen und Listenelemente und soll Ihnen nicht helfen, Parallelitätsprobleme beim Aktualisieren dieser Entitäten zu vermeiden. Im vorherigen Beispiel wird ein Sternchen (*) für diesen Wert verwendet. Sie können den Wert immer verwenden, wenn Sie sicher sind, dass keine Parallelitätsprobleme bestehen. Andernfalls sollten Sie den **etag**-Wert bzw. eine Liste oder ein Listenelement abrufen, indem Sie eine **GET**-Anforderung durchführen, die die Entität abruft. Die Antwortheader der resultierenden HTTP-Antwort übergeben das ETag als Wert des **ETag**-Schlüssels. Dieser Wert ist auch in den Entitätsmetadaten enthalten. Das folgende Beispiel zeigt das öffnende Tag  `<entry>` für den XML-Knoten, der die Listeninformationen enthält. Die Eigenschaft **m:etag** enthält den **etag**-Wert.
  
    
    



```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">
```


## Erstellen einer Website mit REST
<a name="bk_CreateSite"> </a>

Das folgende Beispiel zeigt, wie Sie eine Website in JavaScript erstellen können.
  
    
    

```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});
```


## So unterscheiden sich REST-Anforderungen je nach Umgebung
<a name="bk_HowRequestsDiffer"> </a>

Der Vorgang zum Erstellen und Senden einer HTTP-Anforderung kann je nach Sprache, Bibliothek und Add-In-Typ variieren, sodass Sie häufig mindestens eine Anforderungskomponente ändern müssen, wenn Sie eine Anforderung aus einer Umgebung in eine andere übertragen. jQuery AJAX-Anforderungen verwenden beispielsweise **data**- und **type**-Parameter, um den Anforderungstext und -typ anzugeben, während domänenübergreifende Bibliotheksanforderungen **body**- und **method**-Parameter zum Angeben dieser Werte verwenden.
  
    
    
In den folgenden Abschnitten werden weitere allgemeine Unterschiede zwischen Umgebungen beschrieben.
  
    
    

### Wie Sie den Formulardigestwert abrufen und senden, hängt von dem Add-In ab
<a name="FormDigest"> </a>

Wenn Sie eine POST-Anforderung senden, muss die Anforderung im **X-RequestDigest**-Header den Formulardigestwert enthalten. Wie Sie den Wert abrufen und senden, ist jedoch je nach Add-In unterschiedlich:
  
    
    

- In von SharePoint gehosteten Add-Ins können Sie einfach den folgenden Header übergeben: 
  
    
    
 "X-RequestDigest": $("#__REQUESTDIGEST").val()
    
  
- Rufen Sie in Cloud-gehosteten Add-Ins, die OAuth verwenden, zuerst den Formulardigestwert ab, indem sie eine Anforderung an den **contextinfo**-Endpunkt senden, und dann der Anforderung den Wert hinzufügen, wie unter  [Schreiben von Daten unter Verwendung der REST-Schnittstelle](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData) gezeigt.
    
  
- In Cloud-gehosteten Add-Ins, welche die domänenübergreifende JavaScript-Bibliothek verwenden, müssen Sie den Formulardigestwert nicht angeben. SP.RequestExecutor gibt diesen automatisch für Sie an (ebenso wie den Inhalt-Länge-Wert).
    
  

### Add-Ins, die OAuth verwenden, müssen Zugriffstoken in Anforderungen übergeben
<a name="OAuthTokens"> </a>

In der Cloud gehostete Add-Ins autorisieren den Zugriff auf SharePoint-Daten entweder mit OAuth oder mit der domänenübergreifenden Bibliothek. Add-In-Komponenten mit auf einem Remotewebserver ausgeführtem Code müssen OAuth verwenden, um den Zugriff auf SharePoint-Daten zu autorisieren. In diesem Fall müssen Sie einen **Authorization**-Header zum Senden des Zugriffstokens einbeziehen. Unter  [Lesen von Daten mit der SharePoint 2013 REST-Schnittstelle](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData) finden Sie ein Beispiel, in dem einem **HTTPWebRequest**-Objekt ein Autorisierungsheader hinzugefügt wird.
  
    
    

> [!HINWEIS]
> In der Cloud gehostete Add-In-Komponenten, die in JavaScript geschrieben wurden, müssen zum Zugreifen auf SharePoint-Daten das **SP.RequestExecutor**-Objekt in der domänenübergreifenden Bibliothek verwenden. Domänenübergreifende Bibliotheksanforderungen müssen kein Zugriffstoken enthalten. 
  
    
    

Weitere Informationen zu OAuth-Zugriffstoken und zum Abrufen dieser Token finden Sie unter  [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) und [OAuth-Ablauf mit Authentifizierungscode für SharePoint-Add-Ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### Endpunkt-URIs in domänenübergreifenden Bibliotheksanforderungen verwenden "SP.AppContextSite", um den Kontext zu ändern
<a name="AppContextSite"> </a>

Die Anforderungen werden an den in der **url**-Eigenschaft der Anforderung angegebenen Ressourcenendpunkt gesendet. Endpunkt-URIs verwenden das folgende Format:
  
    
    
 _<Website-URL>_/_api/ _<Kontext>_/ _<Ressource>_ (Beispiel: https://contoso.com/_api/web/lists)
  
    
    
Domänenübergreifende Bibliotheksanforderungen verwenden dieses Format für den Zugriff auf Daten im Add-In-Web. Dies ist der Standardkontext für domänenübergreifende Bibliotheksanforderungen. Um jedoch auf Daten im Hostweb oder in einer anderen Websitesammlung zugreifen zu können, müssen die Anforderungen das Hostweb oder die andere Websitesammlung als Kontext initialisieren. Hierzu wird wie in Tabelle 1 gezeigt der **SP.AppContextSite**-Endpunkt im URI verwendet. Die in Tabelle 1 aufgeführten Beispiel-URIs verwenden den **@target**-Alias zum Senden der Ziel-URL in der Abfragezeichenfolge, weil die URL ein Sonderzeichen (':') enthält.
  
    
    

> [!HINWEIS]
> Damit ein in der Cloud gehostetes Add-In beim Verwenden der domänenübergreifenden Bibliothek auf SharePoint-Daten zugreifen kann, ist eine Add-In-Webinstanz erforderlich. 
  
    
    


**Tabelle 1: Verwenden des "SP.AppContextSite"-Endpunkts zum Ändern des Kontexts der Anforderung**


|**Add-In-Typ**|**Szenario für den domänenübergreifenden Datenzugriff**|**Beispiel-Endpunkt-URI**|
|:-----|:-----|:-----|
|In der Cloud gehostet  <br/> |JavaScript-Add-In-Komponente, die mithilfe der domänenübergreifenden Bibliothek auf Hostwebdaten zugreift  <br/> | _<App-Web-URL>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<Hostweb-URL>_'  <br/> |
|In der Cloud gehostet  <br/> |JavaScript-Add-In-Komponente, die mithilfe der domänenübergreifenden Bibliothek auf Daten zugreift, die sich in einer anderen Websitesammlung als dem Hostweb befinden (gilt nur für Add-Ins mit Mandantenbereich)  <br/> | _<App-Web-URL>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<URL der Zielwebsite>_'  <br/> |
|Von SharePoint gehostet  <br/> |Add-In-Webkomponente, die auf Daten einer anderen Websitesammlung zugreift (gilt nur für Add-Ins mit Mandantenbereich)  <br/> | _<App-Web-URL>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<URL der Zielwebsite>_'  <br/> |
   

> [!HINWEIS]
> In Szenarien für den domänenübergreifenden Datenzugriff sind auch die entsprechenden Add-In-Berechtigungen erforderlich. Weitere Informationen finden Sie unter  [Zugreifen auf Daten in einem Hostweb](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) und [Zugreifen auf Daten in allen Websitesammlungen](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope). 
  
    
    

SharePoint-Add-Ins können die Add-In-Web-URL und die Hostweb-URL aus der Abfragezeichenfolge der Add-In-Seite abrufen. Dies wird im folgenden Codebeispiel gezeigt. Aus dem Beispiel geht außerdem hervor, wie auf die domänenübergreifende Bibliothek verwiesen wird, die im Hostweb in der Datei "SP.RequestExecutor.js" definiert ist. In dem Beispiel wird davon ausgegangen, dass Ihr Add-In über SharePoint gestartet wird. Unter  [OAuth-Ablauf mit Authentifizierungscode für SharePoint-Add-Ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md) finden Sie Anleitungen, wie Sie Ihren SharePoint-Kontext richtig festlegen, wenn das Add-In nicht über SharePoint gestartet wird.
  
    
    



```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions
```


## In REST-Anforderungen verwendete Eigenschaften
<a name="bk_requestElements"> </a>

In Tabelle 2 sind häufig in HTTP-Anforderungen für den SharePoint REST-Dienst verwendete Eigenschaften aufgeführt.
  
    
    

**Tabelle 2: Verwendung von REST-Anforderungseigenschaften in HTTP-Anforderungen**


|**Eigenschaften**|**Wann erforderlich**|**Beschreibung**|
|:-----|:-----|:-----|
|**url** <br/> |Alle Anforderungen  <br/> |Die URL eines REST-Ressourcenendpunkts. Beispiel:  `http://<site url>/_api/web/lists` <br/> |
|**method** (oder **type**)  <br/> |Alle Anforderungen  <br/> |Die HTTP-Anforderungsmethode: **GET** für Lesevorgänge und **POST** für Schreibvorgänge. **POST**-Anforderungen können Aktualisierungs- oder Löschvorgänge durchführen, wenn das Verb **DELETE**, **MERGE** oder **PUT** im **X-HTTP-Method**-Header angegeben wird.  <br/> |
|**body** (oder **data**)  <br/> |**POST**-Anforderungen, die Daten im Anforderungstextkörper senden  <br/> |Der Textkörper der POST-Anforderung. Sendet Daten (z. B. komplexe Typen), die nicht in einer Endpunkt-URI gesendet werden können. Wird mit dem **content-length**-Header verwendet.  <br/> |
|**Authentication**-Header  <br/> |Remote-Add-Ins verwenden OAuth, um Benutzer zu authentifizieren. Dies gilt nicht bei der Verwendung von JavaScript oder der domänenübergreifenden Bibliothek.  <br/> |Sendet den OAuth-Zugriffstoken (erhalten von einem sicheren Token-Server von Microsoft Access Control Service (ACS)), der zur Authentifizierung der Anforderung des Nutzers verwendet wird. Beispiel:  `"Authorization": "Bearer " + accessToken`, wobei  `accessToken` für die Variable steht, die den Token enthält. Token müssen mittels serverseitigem Code abgerufen werden. <br/> |
|**X-RequestDigest**-Header  <br/> |**POST**-Anforderungen (außer SP.RequestExecutor-Anforderungen)  <br/> |Remote-Add-Ins, die OAuth verwenden, können den Formulardigestwert vom Endpunkt  `http://<site url>/_api/contextinfo` abrufen. In SharePoint gehostete Add-Ins können den Wert über das **#__REQUESTDIGEST**-Seitensteuerelement abrufen, wenn es auf der SharePoint-Seite verfügbar ist. Weitere Informationen finden Sie unter  [Schreiben von Daten unter Verwendung der REST-Schnittstelle](#WritingData).  <br/> |
|**accept**-Header  <br/> |Anforderungen, die SharePoint-Metadaten zurückgeben  <br/> |Gibt das Format für Antwortdaten vom Server an. Das Standardformat ist  `application/atom+xml`. Beispiel:  `"accept":"application/json;odata=verbose"` <br/> |
|**content-type**-Header  <br/> |**POST**-Anforderungen, die Daten im Anforderungstextkörper senden  <br/> |Gibt das Format der Daten an, die der Client an den Server sendet. Das Standardformat ist  `application/atom+xml`. Beispiel:  `"content-type":"application/json;odata=verbose"` <br/> |
|**content-length**-Header  <br/> |**POST**-Anforderungen, die Daten im Anforderungstextkörper senden (außer SP.RequestExecutor-Anforderungen)  <br/> |Gibt die Länge des Inhalts an. Beispiel:  `"content-length":requestBody.length` <br/> |
|**IF-MATCH**-Header  <br/> |**POST** fordert **DELETE**-, **MERGE**- oder **PUT**-Vorgänge an, in erster Linie, um Listen und Bibliotheken zu ändern.  <br/> |Bietet eine Möglichkeit, zu überprüfen, dass das Objekt, das geändert wird, seit seinem letzten Abruf nicht geändert wurde. Sie können aber auch festlegen, dass alle Änderungen überschrieben werden, wie im folgenden Beispiel zu sehen:  `"IF-MATCH":"*"` <br/> |
|**X-HTTP-Method**-Header  <br/> |**POST** fordert **DELETE**-, **MERGE**- oder **PUT**-Vorgänge an  <br/> |Wird verwendet, um festzulegen, dass die Anforderung einen Aktualisierungs- oder Löschvorgang durchführt. Beispiel:  `"X-HTTP-Method":"PUT"` <br/> |
|**binaryStringRequestBody** <br/> |SP.RequestExecutor- **POST**-Anforderungen, die binäre Daten im Anforderungstextkörper senden  <br/> |Gibt an, ob der Anforderungstextkörper eine binäre Zeichenfolge ist. **Boolean**.  <br/> |
|**binaryStringResponseBody** <br/> |SP.RequestExecutor-Anforderungen, die binäre Daten zurückgeben  <br/> |Gibt an, ob die Antwort eine binäre Zeichenfolge ist. **Boolean**.  <br/> |
   

## Unterstützung für Batchaufträge
<a name="batch"> </a>

Der SharePoint Online-REST-Dienst unterstützt die Kombination mehrerer Anforderungen in einem einzelnen Dienstaufruf mithilfe der OData-Abfrageoption  `$batch`. Einzelheiten und Links zu Codebeispielen finden Sie unter  [Erstellen von Batchanforderungen mit den REST-APIs](make-batch-requests-with-the-rest-apis.md).
  
    
    

## Weitere Informationsquellen
<a name="bk_addresources"> </a>


-  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Arbeiten mit Ordnern und Dateien mit REST](working-with-folders-and-files-with-rest.md)
    
  
-  [SharePoint 2013 - REST-API, Endpunkte und Beispiele](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: Ausführen grundlegender Datenzugriffsvorgänge für Dateien und Ordner mithilfe von REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Arbeiten mit externen Daten in SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [OData (Open Data Protocol)](http://www.odata.org/)
    
  
-  [OData: JavaScript Object Notation (JSON)-Format](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  
-  [Festlegen von benutzerdefinierten Berechtigungen in einer Liste mit der REST-Schnittstelle](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)
    
  

  
    
    

