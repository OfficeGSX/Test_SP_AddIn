---
title: Arbeiten mit Ordnern und Dateien mit REST
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# Arbeiten mit Ordnern und Dateien mit REST
Erfahren Sie, wie Sie grundlegende Erstellungs-, Lese-, Aktualisierungs- und Löschoperationen, auch als CRUD-Operationen (Create, Read, Update, Delete) bezeichnet, für Ordner und Dateien mit der SharePoint 2013-REST-Schnittstelle durchführen.
> **TIPP**
> Der SharePoint Online-REST-Dienst unterstützt die Kombination mehrerer Anforderungen in einem einzelnen Dienstaufruf mithilfe der OData-Abfrageoption  `$batch`. Einzelheiten und Links zu Codebeispielen finden Sie unter  [Erstellen von Batchanforderungen mit den REST-APIs](make-batch-requests-with-the-rest-apis.md). 
  
    
    


## Arbeiten mit Ordnern unter Verwendung von REST
<a name="Folders"> </a>

Sie können einen Ordner innerhalb einer Dokumentbibliothek abrufen, wenn Sie die URL des Ordners kennen. Zum Beispiel können Sie den Stammordner Ihrer Bibliothek für freigegebene Dokumente abrufen, indem Sie den Endpunkt im folgenden Beispiel verwenden.
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

Der folgende XML-Code zeigt ein Beispiel für die Ordnereigenschaften, die beim Anfordern des XML-Inhaltstyps zurückgegeben werden.
  
    
    



```XML

<content type="application/xml">
<m:properties>
<d:ItemCount m:type="Edm.Int32">0</d:ItemCount>
<d:Name>Shared Documents</d:Name>
<d:ServerRelativeUrl>/Shared Documents</d:ServerRelativeUrl>
<d:WelcomePage/>
</m:properties>
</content>
```

Das folgende Beispiel zeigt, wie Sie einen Ordner **erstellen**.
  
    
    



```

url: http://site url/_api/web/folders
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'ServerRelativeUrl': '/document library relative url/folder name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

Das folgende Beispiel zeigt, wie Sie einen Ordner **aktualisieren** und dazu die **MERGE**-Methode verwenden.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'Name': 'New name' }
Headers: 
     Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"MERGE",
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

Das folgende Beispiel zeigt, wie Sie einen Ordner **löschen**.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"

```


## Arbeiten mit Dateien unter Verwendung von REST
<a name="Files"> </a>

Das folgende Beispiel zeigt, wie Sie alle Dateien in einem Ordner **abrufen**.
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

Das folgende Beispiel zeigt, wie Sie eine bestimmte Datei **abrufen**.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

Sie können eine Datei auch **abrufen**, wenn Sie ihre URL kennen, wie im folgenden Beispiel.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
```

Das folgende Beispiel zeigt, wie Sie eine Datei **erstellen** und einem Ordner hinzufügen.
  
    
    



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body
```

Das folgende Beispiel zeigt, wie Sie eine Datei **aktualisieren** und dazu die **PUT** -Methode verwenden.
  
    
    

> **HINWEIS**
> **PUT** ist die einzige Methode, die Sie zum Aktualisieren einer Datei verwenden können. Die **MERGE** -Methode ist nicht zulässig.
  
    
    




```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: POST
body: "Contents of file."
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    X-HTTP-Method:"PUT"
    content-length:length of post body
```

Wenn Sie die Metadaten einer Datei aktualisieren möchten, müssen Sie einen Endpunkt erstellen, der die Datei als ein Listenelement erreicht. Dies ist möglich, weil jeder Ordner auch eine Liste und jede Datei auch ein Listenelement ist. Erstellen Sie einen Endpunkt, der folgendermaßen aussieht:  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`. In  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md) wird erklärt, wie Sie die Metadaten eines Listenelements aktualisieren.
  
    
    
Sie können eine Datei auschecken, um sicherzustellen, dass andere Benutzer sie nicht ändern, bevor Sie sie aktualisiert haben. Nachdem Sie die Datei aktualisiert haben, können Sie sie wieder zurückchecken, damit andere Benutzer mit ihr arbeiten können. Das folgende Beispiel zeigt, wie Sie **eine Datei auschecken**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

Das folgende Beispiel zeigt, wie Sie **eine Datei einchecken**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
```

Das folgende Beispiel zeigt, wie Sie eine Datei **löschen**.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"

```


## Arbeiten mit großen Dateien unter Verwendung von REST
<a name="LargeFiles"> </a>

Binärdateien mit einer Größe von über 1,5 Megabyte (MB) müssen über die REST-Schnittstelle hochgeladen werden. Unter  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) finden Sie ein Codebeispiel, in dem das Hochladen einer Binärdatei mit einer Größe von weniger als 1,5 MB unter Verwendung des Javascript-Objektmodells von SharePoint 2013veranschaulicht wird. Mit REST können Binärdateien mit einer Größe von bis zu 2 Gigabyte (GB) erstellt werden. Im folgenden Beispiel wird die Vorgehensweise zum **Erstellen** einer großen Binärdatei veranschaulicht.
  
    
    

> **VORSICHT**
> Diese Vorgehensweise funktioniert nur mit Internet Explorer 10 und den neuesten Versionen anderer Browser. 
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/Add(url='file name', overwrite=true)
method: POST
body: contents of binary file
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body
```

Das folgende Codebeispiel veranschaulicht die Vorgehensweise zum Erstellen einer Datei unter Verwendung dieses REST-Endpunkts und der domänenübergreifenden Bibliothek.
  
    
    



```

function uploadFileBinary() {
XDomainTestHelper.clearLog();
var ro;
if (document.getElementById("TxtViaUrl").value.length > 0) {
ro = new SP.RequestExecutor(document.getElementById("TxtWebUrl").value, document.getElementById("TxtViaUrl").value);
}
else {
ro = new SP.RequestExecutor(document.getElementById("TxtWebUrl").value);
}
var body = "";
for (var i = 0; i < 1000; i++) {
var ch = i % 256;
body = body + String.fromCharCode(ch);
}
var info = {
url: "_api/web/lists/getByTitle('Shared Documents')/RootFolder/Files/Add(url='a.dat', overwrite=true)",
method: "POST",
binaryStringRequestBody: true,
body: body,
success: success,
error: fail,
state: "Update"
};
ro.executeAsync(info);
}

```


## Arbeiten mit Dateien, die mit Listenelementen verknüpft sind, unter Verwendung von REST
<a name="FileAttachments"> </a>

Das folgende Beispiel zeigt, wie Sie alle Dateien **abrufen**, die mit einem Listenelement verknüpft sind.
  
    
    

```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

Das folgende Beispiel zeigt, wie Sie eine Datei **abrufen**, die mit einem Listenelement verknüpft ist.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"

```

Das folgende Beispiel zeigt, wie Sie eine Dateianlage für ein Listenelement **erstellen**.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body
```

Das folgende Beispiel zeigt, wie Sie eine Dateianlage für ein Listenelement **aktualisieren**, indem Sie die **PUT** -Methode verwenden.
  
    
    

> **HINWEIS**
> **PUT** ist die einzige Methode, die Sie zum Aktualisieren einer Datei verwenden können. Die **MERGE** -Methode ist nicht zulässig.
  
    
    




```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: POST
body: "Contents of file."
headers:
    Authorization: "Bearer " + accessToken
    "X-HTTP-Method":"PUT"
    X-RequestDigest: form digest value
    content-length:length of post body
```


## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Dateien und Ordner - REST API-Referenz](2c3d2545-1cd7-497e-b535-12199d8edfbb.md)
    
  
-  [Hochladen einer Datei mit der REST-API und jQuery](upload-a-file-by-using-the-rest-api-and-jquery.md)
    
  
-  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: Ausführen grundlegender Datenzugriffsvorgänge für Dateien und Ordner mithilfe von REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Durchführen von REST-Aufrufen mit C# und JavaScript für SharePoint 2013](http://www.microsoft.com/resources/msdn/de-de/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=4e4cc094-ff69-405b-852f-2ac7c41293c5)
    
  
-  [Durchführen von REST-Aufrufen mit C# und JavaScript für SharePoint 2013 Demo](http://www.microsoft.com/resources/msdn/de-de/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)
    
  
-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Arbeiten mit externen Daten in SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [OData (Open Data Protocol)](http://www.odata.org/)
    
  
-  [OData: JavaScript Object Notation (JSON)-Format](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  

  
    
    

