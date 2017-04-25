---
title: Navigieren durch die im REST-Dienst dargestellte SharePoint-Datenstruktur
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# Navigieren durch die im REST-Dienst dargestellte SharePoint-Datenstruktur
Informationen zum Starten von einem REST-Endpunkt für einen gegebenen SharePoint-Element und Navigieren zu und Zugreifen auf dazugehörige Elemente, z. B. übergeordnete Standorte oder die Bibliotheksstruktur, in der sich das jeweilige Element befindet. 
## Navigieren von einer gegebenen URL zum Erreichen anderer SharePoint-Ressourcen

Wenn Sie mit dem SharePoint REST-Dienst arbeiten, kennen Sie häufig die URL eines bestimmten SharePoint-Elements, möchten aber auf dazugehörige Elemente zugreifen, z. B. den Ordner oder die Bibliotheksstruktur, in dem/der sich das jeweilige Element befindet. Angenommen, Sie erstellen ein Add-In, bei dem die Benutzer die URL eines Dokuments in eine SharePoint-Bibliothek eingeben. Ihr Add-In muss dann die URL aufgliedern, um die tatsächliche URL der SharePoint-Website zu bestimmen. Anschließend kann das Add-In im Auftrag des Benutzers weitere Anforderungen an die Website stellen, z. B. zum Erstellen, Aktualisieren oder Löschen verwandter Elemente oder Ressourcen. 



Hierzu muss Ihr Add-In in SharePoint folgende Informationen abfragen:




- Die serverrelative URLs der Website und Websitesammlung, die die Ressource enthält


- Einen Formulardigest zum Ermöglichen des Stellens von Anforderungen, die den Zustand der Ressource ändern, z. B. **POST**, **PUT**, **MERGE** und **DELETE**


Der Standardprozess:




1. Verwenden Sie den Operator  `/contextinfo` mit der angegebenen URL für den Zugriff auf die Website- und Websitesammlungsadressen sowie den Formulardigest. Wählen Sie für den Operator `/contextinfo` das folgende Format:

     `http://server/web/doclib/forms/_api/contextinfo`

    Zum Steigern des Schutzes vor websiteübergreifendem Skripting akzeptiert der Operator  `/contextinfo` nur **POST**-Anforderungen.


2. Verwenden Sie die Eigenschaften des Objekts  [SPContextWebInformation-Objekteigenschaften](#bk_props), die der  `/contextinfo`-Operator zurückgibt, um nach Wunsch auf weitere Ressourcen zuzugreifen. 


 **Probieren Sie es aus**




1. Starten Sie mit einer URL eines gegebenen SharePoint-Elements. Beispiel:

     `http://site/web/doclib/myDocument.docx`


2. Entfernen Sie den Namen der spezifischen Ressource vom Ende der URL, damit die URL auf eine Dokumentbibliothek, einen Ordner oder eine Liste verweist. In diesem Fall:

     `http://site/web/doclib/`


3. Fügen Sie den REST-Dienstzeiger und den Operator  `/contextinfo` an die URL an:

     `http://site/web/doclib/_api/contextinfo`


4. Lesen Sie den Formulardigest und die **webFullUrl**-Eigenschaften aus der Antwort.


5. Fügen Sie den REST-Dienstzeiger  `_api` an die Web-URL an.


6. Stellen Sie mithilfe der resultierenden URL und des Formulardigests Anforderungen für die benötigten Ressourcen.


Sie müssen den Formulardigest nicht übergeben, wenn Sie **GET**-Anforderungen stellen oder Anforderungen mithilfe eines validierten OAuth-Tokens stellen.




## Navigieren durch über- und untergeordnete Websites
<a name="bk_sites"> </a>

Wenn Sie mithilfe des SharePoint-Serverobjektmodells durch Ihre Website navigieren, verwenden Sie die Eigenschaften  [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) und [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) für den Zugriff auf Objekte, die über- und untergeordnete Websites darstellen.



Die entsprechenden REST-Ressourcen ( `web/parentweb` und `web/webs`) geben keine Objekte zurück, die Websites darstellen. Der Grund ist, dass der REST-Dienst OData-Standards befolgt und die Rückgabe vollständiger Websitedarstellungen solche Anforderungen ineffizient machen würde. Stattdessen wird ein  [WebInfo-Objekt ](#bk_webinfo) zurückgegeben, das die skalaren Eigenschaften der Website enthält, jedoch ohne dazugehörige Entitätssätze wie Sammlungen oder Feldsammlungen.



Konstruieren Sie zum Navigieren zu einer bestimmten über- oder untergeordneten Website die entsprechende REST-URL zur jeweiligen Website mithilfe der Eigenschaft **Id** oder **Title**. Anschließend können Sie auf die zu dieser Website gehörigen Entitätssätze zugreifen.




## Navigieren durch die Ordnerstruktur
<a name="bk_folders"> </a>

Der SharePoint REST-Dienst unterstützt nicht das Durchlaufen der Ordnerhierarchie einer Website mithilfe der URL-Erstellung. Verwenden Sie stattdessen die REST-Entsprechung der  [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) -Methode. Beispiel:



 *Von REST-Dienst nicht unterstützte Navigation:* 



 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`



Von REST-Dienst unterstützte Navigation: 



 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`.




## SPContextWebInformation-Objekteigenschaften
<a name="bk_props"> </a>



|**SPContextWebInformation-Eigenschaft**|**Beschreibung**|
|:-----|:-----|
|**webFullUrl** <br/> |Ruft die serverrelative URL der nächstgelegenen Website ab.  <br/> |
|**siteFullUrl** <br/> |Ruft die serverrelative URL der Stammwebsite in der Websitesammlung ab, in der sich die Website befindet.  <br/> Wenn die nächstgelegene Website der Stamm einer Websitesammlung ist, entspricht der Wert der **webFullUrl**-Eigenschaft dem der **siteFullUrl**-Eigenschaft.  <br/> |
|**formDigestValue** <br/> |Ruft den Anforderungsformulardigest des Servers ab.  <br/> |
|**LibraryVersion** <br/> |Ruft die aktuelle Version der REST-Bibliothek ab.  <br/> |
|**SupportedSchemaVersions** <br/> |Ruft die unterstützten Versionen des Schemas der REST/CSOM-Bibliothek ab.  <br/> |
 

## WebInfo-Objekt
<a name="bk_webinfo"> </a>



|**WebInfo-Eigenschaft**|**Beschreibung**|
|:-----|:-----|
|**Created** <br/> |Ruft einen Wert ab, der angibt, wann die Website erstellt wurde.  <br/> |
|**Description** <br/> |Dient zum Abrufen oder Festlegen der Beschreibung der Website.  <br/> |
|**Id** <br/> |Ruft einen Wert ab, der die Website-ID angibt.  <br/> |
|**Language** <br/> |Ruft einen Wert ab, der die Gebietsschema-ID für die auf der Website verwendete Sprache angibt.  <br/> |
|**LastItemModifiedDate** <br/> |Ruft einen Wert ab, der angibt, wann zuletzt ein Element auf der Website geändert wurde.  <br/> |
|**Title** <br/> |Dient zum Abrufen oder Festlegen des Titels der Website.  <br/> |
|**WebTemplateId** <br/> |Ruft den Bezeichner der Websitevorlage ab.  <br/> |
 

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Einführung in den SharePoint 2013 REST-Dienst](get-to-know-the-sharepoint-2013-rest-service.md)


-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md)


-  [Arbeiten mit Ordnern und Dateien mit REST](working-with-folders-and-files-with-rest.md)


-  [Ermitteln von URIs von SharePoint-REST-Dienstendpunkten](determine-sharepoint-rest-service-endpoint-uris.md)


-  [Verwenden von OData-Abfragevorgängen in SharePoint REST-Anforderungen](use-odata-query-operations-in-sharepoint-rest-requests.md)


-  [SharePoint 2013 - REST-API, Endpunkte und Beispiele](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [Synchronisieren von SharePoint-Elementen mit dem REST-Dienst](synchronize-sharepoint-items-using-the-rest-service.md)


-  [Verwenden von ETag-Werten über den REST-Dienst, um die Version von Dokumentlistenelementen zu bestimmen](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)







