---
title: Synchronisieren von SharePoint-Elementen mit dem REST-Dienst
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# Synchronisieren von SharePoint-Elementen mit dem REST-Dienst
In diesem Artikel erfahren Sie, wie Sie Elemente zwischen SharePoint und Ihren Add-Ins oder Diensten mithilfe der Ressource **GetListItemChangesSinceToken** synchronisieren, die Teil des SharePoint REST-Dienstes ist.
## Synchronisieren von SharePoint-Elementen mit der Ressource "GetListItemChangesSinceToken"

Wenn Sie Elemente zwischen SharePoint und Ihren Add-Ins oder Diensten synchronisieren wollen, können Sie dazu die Ressource **GetListItemChangesSinceToken** verwenden. **GetListItemChangesSinceToken** ist Teil des SharePoint REST-Dienst und entspricht dem Webdienstaufruf [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .



Führen Sie eine **POST**-Anforderung durch, die im Textkörper ein  [Objekteigenschaften von SP.ChangeLogItemQuery](#bk_props)-Objekt enthält.



Die Anforderung gibt eine ADO- **rowset**-XML-Datei zurück, die Zeilen enthält, die jedem Listenelement entsprechen, das zur jeweiligen Anfrage passt. Weitere Informationen zu diesen Eigenschaften, einschließlich Eingenschaftsdatenstrukturen, CAML-Elementbeschreibungen und Rückgabewerten, finden Sie unter  [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .




||
|:-----|
|**Beispielanforderung** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**Beispiel für einen POST-Textkörper** <br/> |
|
```XML

{ 'd' : {
  'query': { 
    '__metadata': { 'type': 'SP.ChangeLogItemQuery'}, 
    'ViewName': '', 
    'Query': '<Where>
      <Contains>
         <FieldRef Name="Title" />
         <Value Type='Text'>Te</Value>
      </Contains></Where>',
    'QueryOptions': '<QueryOptions>
      <IncludeMandatoryColumns>FALSE</IncludeMandatoryColumns>
      <DateInUtc>False</DateInUtc>
      <IncludePermissions>TRUE</IncludePermissions>
      <IncludeAttachmentUrls>FALSE</IncludeAttachmentUrls>
      <Folder>Shared Documents/Test1</Folder></QueryOptions>', 
    'ChangeToken':'1;3;eee4c6d5-f88a-42c4-8ce1-685122984870;634397182229400000;3710', 
    'Contains':'<Contains>
      <FieldRef Name="Title"/>
      <Value Type="Text">Testing</Value></Contains>' } 
  } 
}

```

|
 

## Objekteigenschaften von SP.ChangeLogItemQuery
<a name="bk_props"> </a>


****


|**Eigenschaft**|**Beschreibung**|
|:-----|:-----|
|**ListName** <br/> |Eine Zeichenfolge, die entweder Titel oder GUID der Liste enthält. Bei Abruf der Tabelle "UserInfo" enthält die Zeichenfolge UserInfo. Die Verwendung der GUID führt zu einer besseren Leistung.  <br/> |
|**ViewName** <br/> |Eine Zeichenfolge, die die GUID für die Ansicht enthält. Diese bestimmt die Ansicht, die für die Standardansichtsattribute verwendet wird, die durch die Parameter  _query_,  _viewFields_ und _rowLimit_ repräsentiert werden. Wird dieses Argument nicht angegeben, findet die Standardansicht Anwendung. Wird es angegeben, überschreibt der Wert des Parameters _query_,  _viewFields_ oder _rowLimit_ die entsprechende Einstellung in der Ansicht. Wird die Ansicht beispielsweise durch den Parameter _viewFields_ mit einer maximalen Zeilenanzahl von 100 angeben, während der Parameter _rowLimit_ einen Wert von 1000 enthält, werden in der Antwort 1000 Zeilen zurückgegeben. <br/> |
|**Query** <br/> |Ein  [Query](http://msdn.microsoft.com/de-de/library/ms471093.aspx)-Element, das die Abfrage enthält, die bestimmt, welche Datensätze in welcher Reihenfolge zurückgegeben werden.  <br/> |
|**QueryOptions** <br/> |Ein XML-Fragment in der folgenden Form, das separate Knoten für die verschienden Eingeschaften des  [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) -Objekts enthält. <br/> |
|**ChangeToken** <br/> |Eine Zeichenfolge, die den Änderungstoken für die Anforderung enthält. Eine Beschreibung des für diese Zeichenfolge verwendeten Formats finden Sie unter  [Änderungsprotokoll (Übersicht)](http://msdn.microsoft.com/de-de/library/bb417456.aspx).Wenn NULL übergeben wird, werden alle Elemente in der Liste zurückgegeben.  <br/> |
|**Contains** <br/> |n  [Contains](http://msdn.microsoft.com/de-de/library/ms196501.aspx)-Element, das die benutzerdefinierte Filterung für die Abfrage definiert.  <br/> |
 

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Einführung in den SharePoint 2013 REST-Dienst](get-to-know-the-sharepoint-2013-rest-service.md)


-  [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-REST-Endpunkten](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Arbeiten mit Listen und Listenelementen unter Verwendung von REST](working-with-lists-and-list-items-with-rest.md)


-  [Arbeiten mit Ordnern und Dateien mit REST](working-with-folders-and-files-with-rest.md)


-  [Navigieren durch die im REST-Dienst dargestellte SharePoint-Datenstruktur](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)


-  [Ermitteln von URIs von SharePoint-REST-Dienstendpunkten](determine-sharepoint-rest-service-endpoint-uris.md)


-  [Verwenden von OData-Abfragevorgängen in SharePoint REST-Anforderungen](use-odata-query-operations-in-sharepoint-rest-requests.md)


-  [SharePoint 2013 - REST-API, Endpunkte und Beispiele](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [Verwenden von ETag-Werten über den REST-Dienst, um die Version von Dokumentlistenelementen zu bestimmen](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)







