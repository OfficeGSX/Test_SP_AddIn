---
title: Utilisation d'une liste et de ses éléments avec REST
ms.prod: SHAREPOINT
ms.assetid: 956f3a09-bddc-4154-9cba-6143c914d60f
---


# Utilisation d'une liste et de ses éléments avec REST
Découvrez comment effectuer des opérations CRUD (create, read, update et delete ; créer, lire, mettre à jour et supprimer) de base sur les listes et les éléments de liste à l'aide de l'interface REST SharePoint 2013.
> **CONSEIL**
> Le service REST SharePoint Online (et SharePoint sur site 2016 et ultérieur) prend en charge la combinaison de plusieurs requêtes en un seul appel au service à l'aide de l'option de requête  `$batch` OData. Pour plus de détails et des liens vers des exemples de code, reportez-vous à [Effectuer des requêtes de lot avec les API REST](make-batch-requests-with-the-rest-apis.md). 





## Configuration requise

Cette rubrique suppose que vous avez déjà parcouru les rubriques  [Familiarisation avec le service REST SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md) et [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). Elle ne fournit aucun extrait de code.




## Récupérer des listes et des propriétés de liste à l'aide de l'interface REST
<a name="RetrieveLists"> </a>

L'exemple ci-après montre comment **récupérer** une liste spécifique lorsque vous connaissez le GUID de celle-ci.



```

url: http://site url/_api/web/lists(guid'list GUID'),
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```


> **REMARQUE**
> Utilisez  `application/json;odata=verbose` dans l'en-tête `accept` si vous voulez obtenir la réponse en JSON. Employez `application/atom+xml` dans l'en-tête `accept` si vous voulez obtenir la réponse au format Atom.




L'exemple ci-après montre comment **récupérer** une liste spécifique lorsque vous connaissez le titre de celle-ci.





```

url: http://site url/_api/web/lists/GetByTitle('Test')
method: GET
Headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

Le code XML ci-après donne un exemple des propriétés de liste qui sont renvoyées lorsque vous demandez le type de contenu XML.





```XML

<content type="application/xml">
  <m:properties>
  <d:AllowContentTypes m:type="Edm.Boolean">true</d:AllowContentTypes> 
  <d:BaseTemplate m:type="Edm.Int32">100</d:BaseTemplate> 
  <d:BaseType m:type="Edm.Int32">0</d:BaseType> 
  <d:ContentTypesEnabled m:type="Edm.Boolean">false</d:ContentTypesEnabled> 
  <d:Created m:type="Edm.DateTime">2012-06-26T23:15:58Z</d:Created> 
  <d:DefaultContentApprovalWorkflowId m:type="Edm.Guid">00000000-0000-0000-0000-000000000000</d:DefaultContentApprovalWorkflowId> 
  <d:Description>A list created by Project Based Retention used to store Project Policy Items.</d:Description> 
  <d:Direction>none</d:Direction> 
  <d:DocumentTemplateUrl m:null="true" /> 
  <d:DraftVersionVisibility m:type="Edm.Int32">0</d:DraftVersionVisibility> 
  <d:EnableAttachments m:type="Edm.Boolean">true</d:EnableAttachments> 
  <d:EnableFolderCreation m:type="Edm.Boolean">false</d:EnableFolderCreation> 
  <d:EnableMinorVersions m:type="Edm.Boolean">false</d:EnableMinorVersions> 
  <d:EnableModeration m:type="Edm.Boolean">false</d:EnableModeration> 
  <d:EnableVersioning m:type="Edm.Boolean">false</d:EnableVersioning> 
  <d:EntityTypeName>ProjectPolicyItemList</d:EntityTypeName> 
  <d:ForceCheckout m:type="Edm.Boolean">false</d:ForceCheckout> 
  <d:HasExternalDataSource m:type="Edm.Boolean">false</d:HasExternalDataSource> 
  <d:Hidden m:type="Edm.Boolean">true</d:Hidden> 
  <d:Id m:type="Edm.Guid">74de3ff3-029c-42f9-bd2a-1e9463def69d</d:Id> 
  <d:ImageUrl>/_layouts/15/images/itgen.gif</d:ImageUrl> 
  <d:IrmEnabled m:type="Edm.Boolean">false</d:IrmEnabled> 
  <d:IrmExpire m:type="Edm.Boolean">false</d:IrmExpire> 
  <d:IrmReject m:type="Edm.Boolean">false</d:IrmReject> 
  <d:IsApplicationList m:type="Edm.Boolean">false</d:IsApplicationList> 
  <d:IsCatalog m:type="Edm.Boolean">false</d:IsCatalog> 
  <d:IsPrivate m:type="Edm.Boolean">false</d:IsPrivate> 
  <d:ItemCount m:type="Edm.Int32">0</d:ItemCount> 
  <d:LastItemDeletedDate m:type="Edm.DateTime">2012-06-26T23:15:58Z</d:LastItemDeletedDate> 
  <d:LastItemModifiedDate m:type="Edm.DateTime">2012-06-26T23:15:59Z</d:LastItemModifiedDate> 
  <d:ListItemEntityTypeFullName>SP.Data.ProjectPolicyItemListItem</d:ListItemEntityTypeFullName> 
  <d:MultipleDataList m:type="Edm.Boolean">false</d:MultipleDataList> 
  <d:NoCrawl m:type="Edm.Boolean">true</d:NoCrawl> 
  <d:ParentWebUrl>/</d:ParentWebUrl> 
  <d:ServerTemplateCanCreateFolders m:type="Edm.Boolean">true</d:ServerTemplateCanCreateFolders> 
  <d:TemplateFeatureId m:type="Edm.Guid">00bfea71-de22-43b2-a848-c05709900100</d:TemplateFeatureId> 
  <d:Title>Project Policy Item List</d:Title> 
  </m:properties>
  </content>```


> **REMARQUE**
> La propriété **ListItemEntityTypeFullName** ( **SP.Data.ProjectPolicyItemListItem** dans l'exemple précédent) est particulièrement importante lorsque vous voulez créer et mettre à jour des éléments de liste. Cette valeur doit être transmise à la propriété **type** dans les métadonnées transmises dans le corps de la requête HTTP lors de la création et la mise à jour d'éléments de liste.





## Utilisation de listes à l'aide de l'interface REST
<a name="WorkLists"> </a>

L'exemple ci-après montre comment **créer** une liste.



```

url: http://site url/_api/web/lists
method: POST
body: { '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true, 'BaseTemplate': 100,
 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple ci-après montre comment **mettre à jour** une liste à l'aide de la méthode **MERGE**.





```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
body: { '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH": etag or "*"
    X-HTTP-Method: MERGE,
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple suivant montre comment **créer** un **champ personnalisé** pour une liste.





```

Url: url: http://site url/_api/web/lists(guid'list GUID')/Fields
Method:POST
Body: { '__metadata': { 'type': 'SP.Field' }, 'Title': 'field title', 'FieldTypeKind': FieldType value,'Required': 'true/false', 'EnforceUniqueValues': 'true/false','StaticName': 'field name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple ci-après montre comment **supprimer** une liste.





```

url: http://site url/_api/web/lists(guid'list GUID')
method: POST
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method: DELETE
```


## Utilisation d'éléments de liste à l'aide de l'interface REST
<a name="ListItems"> </a>

L'exemple ci-après montre comment **récupérer** tous les éléments d'une liste.




> **REMARQUE**
> L'option de requête OData $skip ne fonctionne pas pour l'interrogation d'éléments de liste. Dans de nombreuses situations, vous pouvez utiliser l'option  [$skiptoken](http://msdn.microsoft.com/library/4dda9434-c2c5-4577-8e01-7bf9e822d90a.aspx) à la place.




```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

L'exemple ci-après montre comment **récupérer** un élément de liste spécifique.





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

Le code XML ci-après donne un exemple des propriétés d'élément de liste qui sont renvoyées lorsque vous demandez le type de contenu XML.





```XML

<content type="application/xml">
<m:properties> 
<d:FileSystemObjectType m:type="Edm.Int32">0</d:FileSystemObjectType>
<d:Id m:type="Edm.Int32">1</d:Id>
<d:ID m:type="Edm.Int32">1</d:ID>
<d:ContentTypeId>0x010049564F321A0F0543BA8C6303316C8C0F</d:ContentTypeId>
<d:Title>an item</d:Title>
<d:Modified m:type="Edm.DateTime">2012-07-24T22:47:26Z</d:Modified>
<d:Created m:type="Edm.DateTime">2012-07-24T22:47:26Z</d:Created>
<d:AuthorId m:type="Edm.Int32">11</d:AuthorId>
<d:EditorId m:type="Edm.Int32">11</d:EditorId>
<d:OData__UIVersionString>1.0</d:OData__UIVersionString>
<d:Attachments m:type="Edm.Boolean">false</d:Attachments>
<d:GUID m:type="Edm.Guid">eb6850c5-9a30-4636-b282-234eda8b1057</d:GUID>
</m:properties>
</content>```

L'exemple ci-après montre comment **créer** un élément de liste.




> **REMARQUE**
> Pour effectuer cette opération, vous devez connaître la propriété **ListItemEntityTypeFullName** de la liste et la transmettre comme valeur de **type** dans le corps de la requête HTTP.






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'Test'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple ci-après montre comment **mettre à jour** un élément de liste.




> **REMARQUE**
> Pour effectuer cette opération, vous devez connaître la propriété **ListItemEntityTypeFullName** de la liste et la transmettre comme valeur de **type** dans le corps de la requête HTTP.






```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
body: { '__metadata': { 'type': 'SP.Data.TestListItem' }, 'Title': 'TestUpdated'}
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"MERGE",
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple ci-après montre comment **supprimer** un élément de liste.





```

url: http://site url/_api/web/lists/GetByTitle('Test')/items(item id)
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"
```


## Utilisation de valeurs ETag pour déterminer la version des documents et des éléments de liste
<a name="Etag"> </a>

Le service REST SharePoint, qui suit la  [norme OData](http://www.odata.org/developers/protocols/operations), utilise les  [ETags HTML pour le contrôle de simultanéité](http://www.odata.org/developers/protocols/operations#ConcurrencycontrolandETags) des listes et éléments de listes SharePoint. Pour vérifier la version d'un élément lorsque vous effectuez une requête **PUT**, **MERGE** ou **DELETE**, spécifiez une valeur **ETag** dans l'en-tête de requête HTTP **If-Match**.



Si la valeur **ETag** spécifiée dans votre requête ne correspond pas à la valeur **ETag** du document ou de l'élément de liste sur le serveur, le service REST renvoie une exception 412, pour la spécification OData.




- Pour forcer le remplacement d'un élément indépendamment de sa version, définissez la valeur **ETag** sur **"*"**.


- Si vous ne spécifiez aucune valeur **ETag**, SharePoint remplace l'élément sans tenir compte de sa version.


Dans SharePoint, les ETags s'appliquent uniquement aux listes et aux éléments de liste SharePoint.




## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Utilisation de dossiers et de fichiers à l'aide de l'interface REST](working-with-folders-and-files-with-rest.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013 : Effectuer des opérations d'accès aux données de base sur des fichiers et des dossiers à l'aide de l'interface REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [Effectuer des appels REST en C# et JavaScript pour SharePoint 2013](http://www.microsoft.com/resources/msdn/fr-fr/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=4e4cc094-ff69-405b-852f-2ac7c41293c5)


-  [Effectuer des appels REST en C# et JavaScript pour SharePoint 2013](http://www.microsoft.com/resources/msdn/fr-fr/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)


-  [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)


-  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Utiliser des données externes dans SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)


-  [Open Data Protocol](http://www.odata.org/)


-  [OData : Format JSON (JavaScript Object Notation)](http://www.odata.org/documentation/odata-version-2-0/json-format/)







