---
title: Naviguer dans la structure de données SharePoint représentée dans le service REST
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# Naviguer dans la structure de données SharePoint représentée dans le service REST
Découvrez comment démarrer à partir d'un point de terminaison REST pour un élément SharePoint donné et accéder aux éléments connexes, tels que les sites parents ou la structure de la bibliothèque dans laquelle cet élément réside. 
## Naviguer à partir d'une URL donnée pour atteindre les autres ressources SharePoint

Lorsque vous travaillez avec le service REST SharePoint, vous commencez souvent en connaissant l'URL d'un élément SharePoint spécifique, mais vous voulez aussi accéder aux éléments connexes, tels que le dossier ou la structure de bibliothèque où cet élément réside. Par exemple, supposons que vous créez un complément dans lequel l'utilisateur entre l'URL d'un document dans une bibliothèque SharePoint. Votre complément doit donc décomposer cette URL pour déterminer la véritable URL du site SharePoint. Ensuite, le complément peut effectuer plus de requêtes pour l'utilisateur à partir du site, comme créer, mettre à jour ou supprimer des ressources ou des éléments connexes. 



Pour ce faire, votre complément doit interroger SharePoint pour obtenir les informations suivantes :




- URL relatives au serveur du site et de la collection de sites contenant la ressource


- Chiffrement de formulaire vous permettant d'effectuer des requêtes modifiant l'état de la ressource, telles que **POST**, **PUT**, **MERGE** et **DELETE**


Processus de base :




1. Utilisez l'opérateur  `/contextinfo` avec l'URL donnée pour accéder au site et aux adresses de collection de sites, ainsi qu'au chiffrement de formulaire. Utilisez l'opérateur `/contextinfo` au format suivant :

     `http://server/web/doclib/forms/_api/contextinfo`

    Pour renforcer la sécurité contre les tentatives d'exécution de scripts de site à site, l'opérateur  `/contextinfo` n'accepte que les requêtes **POST**.


2. Utilisez les  [Propriétés de l'objet SPContextWebInformation](#bk_props) renvoyées par l'opérateur `/contextinfo` pour accéder aux ressources supplémentaires que vous désirez.


 **Essayer**




1. Commencez avec une URL menant à un élément SharePoint donné. Par exemple :

     `http://site/web/doclib/myDocument.docx`


2. Retirez le nom de la ressource spécifique de la fin de l'URL, de sorte que celle-ci pointe vers une bibliothèque de documents, un dossier ou une liste. Dans ce cas :

     `http://site/web/doclib/`


3. Ajoutez le pointeur de service REST et l'opérateur  `/contextinfo` à l'URL :

     `http://site/web/doclib/_api/contextinfo`


4. Consultez le chiffrement fourni pour le formulaire et les propriétés **webFullUrl** dans la réponse.


5. Ajoutez le pointeur de service REST  `_api` à l'URL web.


6. Utilisez l'URL qui résulte de ces opérations et le chiffrement de formulaire pour effectuer des requêtes concernant les autres ressources dont vous avez besoin.


Vous n'avez pas besoin de transmettre le chiffrement de formulaire pour les requêtes **GET** ou les requêtes effectuées à l'aide d'un jeton OAuth validé.




## Parcourir les sites parents et enfants
<a name="bk_sites"> </a>

Lorsque vous naviguez dans la structure de votre site à l'aide du modèle objet serveur SharePoint, les propriétés  [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) et [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) vous permettent d'accéder aux objets qui représentent les sites parents et enfants.



Les ressources REST correspondantes,  `web/parentweb` et `web/webs`, ne renvoient pas d'objets représentant des sites. Ceci est dû au fait que le service REST se conforme aux normes OData, et le fait de renvoyer des représentations de site complètes rendrait ces requêtes inefficaces. Au lieu de cela, elles renvoient un  [Objet WebInfo ](#bk_webinfo) qui contient les propriétés scalaires du site mais pas ses jeux d'entités connexes, comme les collections ou les collections de champs.



Pour accéder à un site parent ou enfant spécifique, reconstituez l'URL REST appropriée à l'aide de la propriété **Id** ou **Title**. À partir de là, vous pouvez accéder aux jeux d'entités connexes de ce site.




## Navigation dans la structure de dossiers
<a name="bk_folders"> </a>

Le service REST SharePoint ne prend pas en charge l'accès à la hiérarchie des dossiers d'un site via la génération d'URL. Utilisez plutôt l'équivalent REST de la méthode  [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) . Par exemple :



 *Navigation non prise en charge via le service REST :* 



 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`



Navigation prise en charge via le service REST : 



 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`.




## Propriétés de l'objet SPContextWebInformation
<a name="bk_props"> </a>



|**Propriété SPContextWebInformation**|**Description**|
|:-----|:-----|
|**webFullUrl** <br/> |Obtient l'URL relative au serveur du site le plus proche.  <br/> |
|**siteFullUrl** <br/> |Obtient l'URL relative au serveur de la racine de la collection de sites contenant le site.  <br/> Si le site web le plus proche est la racine d'une collection de sites, la valeur de la propriété **webFullUrl** est égale à celle de la propriété **siteFullUrl**.  <br/> |
|**formDigestValue** <br/> |Obtient le chiffrement de formulaire de requête du serveur.  <br/> |
|**LibraryVersion** <br/> |Obtient la version actuelle de la bibliothèque REST.  <br/> |
|**SupportedSchemaVersions** <br/> |Obtient les versions du schéma de la bibliothèque REST/CSOM qui sont prises en charge.  <br/> |
 

## Objet WebInfo
<a name="bk_webinfo"> </a>



|**Propriété WebInfo**|**Description**|
|:-----|:-----|
|**Created** <br/> |Obtient une valeur qui spécifie la date de création du site.  <br/> |
|**Description** <br/> |Obtient ou définit la description du site.  <br/> |
|**Id** <br/> |Obtient une valeur qui spécifie l'identificateur du site.  <br/> |
|**Language** <br/> |Obtient une valeur qui spécifie l'ID de paramètres régionaux de la langue utilisée sur le site.  <br/> |
|**LastItemModifiedDate** <br/> |Obtient une valeur qui indique la date de dernière modification d'un élément dans le site.  <br/> |
|**Title** <br/> |Obtient ou définit le titre du site.  <br/> |
|**WebTemplateId** <br/> |Obtient l'identificateur du modèle de site.  <br/> |
 

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Familiarisation avec le service REST SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)


-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md)


-  [Utilisation de dossiers et de fichiers à l'aide de l'interface REST](working-with-folders-and-files-with-rest.md)


-  [Déterminer les URI de point de terminaison du service REST SharePoint](determine-sharepoint-rest-service-endpoint-uris.md)


-  [Utiliser les opérations de requête OData dans les demandes REST SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)


-  [Exemples, points de terminaison et API REST pour SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [Synchroniser les éléments SharePoint à l'aide du service REST](synchronize-sharepoint-items-using-the-rest-service.md)


-  [Utiliser des valeurs ETag avec le service REST pour le contrôle de version des éléments de liste](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)







