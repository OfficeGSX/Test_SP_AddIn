---
title: Utilisation de dossiers et de fichiers à l'aide de l'interface REST
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# Utilisation de dossiers et de fichiers à l'aide de l'interface REST
Découvrez comment effectuer des opérations CRUD (create, read, update et delete ; créer, lire, mettre à jour et supprimer) de base sur les dossiers et les fichiers à l'aide de l'interface REST SharePoint 2013.
> **CONSEIL**
> Le service REST SharePoint Online (et SharePoint sur site 2016 et ultérieur) prend en charge la combinaison de plusieurs requêtes en un seul appel au service à l'aide de l'option de requête  `$batch` OData. Pour plus de détails et des liens vers des exemples de code, reportez-vous à [Effectuer des requêtes de lot avec les API REST](make-batch-requests-with-the-rest-apis.md). 
  
    
    


## Utilisation de dossiers à l'aide de l'interface REST
<a name="Folders"> </a>

Vous pouvez récupérer un dossier au sein d'une bibliothèque de documents lorsque vous connaissez l'URL de celui-ci. Par exemple, vous pouvez récupérer le dossier racine de la bibliothèque de documents partagés à l'aide du point de terminaison de l'exemple ci-après.
  
    
    
```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

Le code XML ci-après donne un exemple des propriétés de dossier qui sont renvoyées lorsque vous demandez le type de contenu XML.
  
    
    


```XML

<content type="application/xml">
<m:properties>
<d:ItemCount m:type="Edm.Int32">0</d:ItemCount>
<d:Name>Shared Documents</d:Name>
<d:ServerRelativeUrl>/Shared Documents</d:ServerRelativeUrl>
<d:WelcomePage/>
</m:properties>
</content>```

L'exemple ci-après montre comment **créer** un dossier.
  
    
    


```

url: http://site url/_api/web/folders
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'ServerRelativeUrl': '/document library relative url/folder name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple ci-après montre comment **mettre à jour** un dossier à l'aide de la méthode **MERGE**.
  
    
    


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
    content-length:length of post body```

L'exemple ci-après montre comment **supprimer** un dossier.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"
```


## Utilisation de fichiers à l'aide de l'interface REST
<a name="Files"> </a>

L'exemple ci-après montre comment **récupérer** tous les fichiers d'un dossier.
  
    
    
```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

L'exemple ci-après montre comment **récupérer** un fichier spécifique.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken```

Vous pouvez également **récupérer** un fichier lorsque vous connaissez l'URL de celui-ci, comme illustré dans l'exemple ci-après.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken```

L'exemple ci-après montre comment **créer** un fichier et l'ajouter à un dossier.
  
    
    


```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body```

L'exemple ci-après montre comment **mettre à jour** un fichier à l'aide de la méthode **PUT**.
  
    
    

> **REMARQUE**
> **PUT** est la seule méthode permettant de mettre à jour un fichier. La méthode **MERGE** n'est pas autorisée.
  
    
    



```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: POST
body: "Contents of file."
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    X-HTTP-Method:"PUT"
    content-length:length of post body```

Pour mettre à jour les métadonnées d'un fichier, vous devez créer un point de terminaison qui renvoie au fichier comme un élément de liste. Cette opération est possible dans la mesure où chaque dossier est également une liste et chaque fichier est un élément de liste. Créez un point de terminaison semblable à celui-ci :  `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)`. La rubrique  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md) explique comment mettre à jour les métadonnées d'un élément de liste.
  
    
    
Vous souhaiterez peut-être extraire un fichier pour vérifier qu'aucun utilisateur ne l'a modifié avant de le mettre à jour. Une fois le fichier mis à jour, vous voudrez peut-être aussi l'archiver pour que les autres utilisateurs puissent l'utiliser. L'exemple ci-après montre comment **extraire un fichier**.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value```

L'exemple ci-après montre comment **archiver un fichier**.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value```

L'exemple ci-après montre comment **supprimer** un fichier.
  
    
    


```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"
```


## Utilisation de fichiers volumineux à l'aide de l'interface REST
<a name="LargeFiles"> </a>

Pour télécharger un fichier binaire dont la taille est supérieure à 1,5 mégaoctet (Mo), l'interface REST constitue la seule option. Pour obtenir un exemple de code qui montre comment télécharger un fichier binaire dont la taille est inférieure à 1,5 Mo à l'aide du modèle objet Javascript de SharePoint 2013, reportez-vous à  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md). La taille maximale d'un fichier binaire que vous pouvez créer à l'aide de REST est de 2 gigaoctets (Go). L'exemple suivant montre comment **créer** un fichier binaire volumineux.
  
    
    

> **ATTENTION**
> Cette approche ne fonctionne qu'avec Internet Explorer 10 et les versions les plus récentes des autres navigateurs. 
  
    
    

```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/Add(url='file name', overwrite=true)
method: POST
body: contents of binary file
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

L'exemple de code suivant montre comment créer un fichier à l'aide de ce point de terminaison REST et de la bibliothèque inter-domaines.
  
    
    


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


## Utilisation de fichiers joints à des éléments de liste à l'aide de l'interface REST
<a name="FileAttachments"> </a>

L'exemple ci-après montre comment **récupérer** l'ensemble des fichiers joints à un élément de liste.
  
    
    
```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

L'exemple ci-après montre comment **récupérer** un fichier joint à un élément de liste.
  
    
    


```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

L'exemple ci-après montre comment **créer** un fichier joint à un élément de liste.
  
    
    


```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body```

L'exemple ci-après montre comment **mettre à jour** un fichier joint à un élément de liste à l'aide de la méthode **PUT**.
  
    
    

> **REMARQUE**
> **PUT** est la seule méthode permettant de mettre à jour un fichier. La méthode **MERGE** n'est pas autorisée.
  
    
    



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: POST
body: "Contents of file."
headers:
    Authorization: "Bearer " + accessToken
    "X-HTTP-Method":"PUT"
    X-RequestDigest: form digest value
    content-length:length of post body```


## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Références API REST pour les fichiers et les dossiers](2c3d2545-1cd7-497e-b535-12199d8edfbb.md)
    
  
-  [Télécharger un fichier en utilisant l'API REST et jQuery](upload-a-file-by-using-the-rest-api-and-jquery.md)
    
  
-  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md)
    
  
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
    
  
-  [OData : Format JSON (JavaScript Object Notation)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  

  
    
    

