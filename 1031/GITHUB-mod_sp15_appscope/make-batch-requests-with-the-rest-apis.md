---
title: Erstellen von Batchanforderungen mit den REST-APIs
ms.prod: OFFICE365
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# Erstellen von Batchanforderungen mit den REST-APIs
In diesem Artikel erfahren Sie, wie Sie die Abfrageoption  `$batch` zusammen mit den REST/OData-APIs verwenden.
In diesem Artikel wird beschrieben, wie Sie Abfragen und Vorgänge für die REST/OData-API von Microsoft SharePoint Online (und die lokalen SharePoint 2016 und höher) und die  [Dateien- und Ordnerteilmenge](http://msdn.microsoft.com/de-de/office/office365/api/files-rest-operations) der Office 365-REST-APIs in Batches zusammenfassen. Mit diesem Verfahren können Sie die Leistung Ihres Add-Ins verbessern, indem viele Vorgänge in einer einzelnen Anforderung an den Server und einer einzelnen Antwort kombiniert werden.





## Zusammenfassung der $batch-Option

SharePoint Online (und die lokalen SharePoint 2016 und höher) und Office 365-APIs implementieren die OData-Abfrageoption  `$batch`. Sie können also auf  [die offizielle Dokumentation](http://www.odata.org/documentation/odata-version-3-0/batch-processing) zurückgreifen, um Details zu ihrer Verwendung zu erfahren. (Eine andere Möglichkeit ist Andrew Connells Blogbeitrag zu dem Thema unter [Teil 1 - Batchverarbeitung der SharePoint-REST-API](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests).) Im Folgenden werden nur die wichtigsten Punkte aufgeführt:




- Die Anforderungs-URL besteht aus der Stammdienst-URL und der Option  `$batch`. Beispiel:  `https://fabrikam.sharepoint.com/_api/$batch` oder `https://fabrikam.office365.com/api/v1.0/me/$batch`.


- Der MIME-Typ für den Hauptteil der HTTP-Anforderung ist  *multipart/mixed*  .


- Der Text der Anforderung ist in Bereiche unterteilt, die durch eine Trennzeichenfolge voneinander getrennt sind, die im Header der Anforderung angegeben ist.


- Jeder Teil des Textkörpers enthält ein eigenes HTTP-Verb und eine eigene REST-URL sowie bei Bedarf einen eigenen internen Textkörper.


- Ein Teil kann ein Lesevorgang (oder Funktionsaufruf) oder ein ChangeSet aus mindestens einem Schreibvorgang (oder Funktionsaufruf) sein. Ein ChangeSet ist selbst vom MIME-Typ  *multipart/mixed*  mit Unterteilen, die Einfügungs-, Aktualisierungs- oder Löschvorgänge enthalten.

    > **WICHTIG**
    > Zu diesem Zeitpunkt wird in SharePoint und Office 365-APIs keine "Alles oder nichts"-Funktionalität für ChangeSets mit mehreren Vorgängen unterstützt. Wenn in einem der untergeordneten Vorgänge ein Fehler auftritt, werden die anderen dennoch abgeschlossen und nicht zurückgesetzt. 

## Codebeispiele

Beispiele für Code, der die Abfrageoption  `$batch` für die REST/OData-APIs von SharePoint verwendet:




- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)


- **JavaScript:** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)



## Beispielanforderungen und -antworten

Das folgende Beispiel zeigt eine unformatierte HTTP-Anforderung, die zwei GET-Vorgänge zusammenfasst, die die Titel aller Elemente in zwei verschiedenen Listen abrufen.




```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Host: fabrikam.sharepoint.com
Content-Length: 527
Expect: 100-continue

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('Composed%20Looks')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('User%20Information%20List')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1--

```

Das folgende Beispiel zeigt den Textkörper einer unformatierten HTTP-Anforderung, die ein DELETE einer Liste und ein GET der SharePoint-Liste der Listen zusammenfasst.






```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Host: fabrikam.sharepoint.com
Content-Length: 647
Expect: 100-continue

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: multipart/mixed; boundary=changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d
Content-Type: application/http
Content-Transfer-Encoding: binary

DELETE https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('OldList') HTTP/1.1
If-Match: "1"

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d--
--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists HTTP/1.1

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e--
```


## Links zu nützlichen Bibliotheken

Es gibt OData-Bibliotheken, die OData-Batchverarbeitung für viele Sprachen unterstützen. Zwei Beispiele sind nachstehend aufgeführt. Eine vollständige Liste finden Sie unter  [OData-Bibliotheken](http://www.odata.org/libraries/).




-  [.NET OData-Bibliothek](http://msdn.microsoft.com/de-de/office/microsoft.data.odata%28v=vs.90%29). Beachten Sie vor allem die **ODataBatch***-Klassen.


-  [Datajs-Bibliothek](http://datajs.codeplex.com/documentation). Beachten Sie vor allem die  [Batch-Vorgänge](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch).



