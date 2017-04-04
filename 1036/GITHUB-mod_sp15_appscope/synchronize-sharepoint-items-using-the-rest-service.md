---
title: Synchroniser les éléments SharePoint à l'aide du service REST
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# Synchroniser les éléments SharePoint à l'aide du service REST
Découvrez comment synchroniser des éléments entre SharePoint et vos compléments ou services à l'aide de la ressource **GetListItemChangesSinceToken**, dans le service REST SharePoint.
## Synchronisation des éléments SharePoint à l'aide de la ressource GetListItemChangesSinceToken

Si vous souhaitez synchroniser des éléments entre SharePoint et vos compléments ou services, vous pouvez utiliser la ressource **GetListItemChangesSinceToken**. La ressource **GetListItemChangesSinceToken**, comprise dans le service REST SharePoint, correspond à l'appel de service web  [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    
Exécutez une requête **POST** dont le corps inclut un objet [Propriétés de l'objet SP.ChangeLogItemQuery](#bk_props).
  
    
    
La requête renvoie un XML **rowset** ADO qui contient des lignes renvoyant à chaque modification d'élément de liste correspondant à la requête spécifiée. Pour plus d'informations sur ces propriétés, notamment sur les structures de données de propriété, les descriptions d'éléments CAML et les valeurs de retour, voir [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    

||
|:-----|
|**Exemple de requête** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**Exemple de corps POST** <br/> |
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
   

## Propriétés de l'objet SP.ChangeLogItemQuery
<a name="bk_props"> </a>


****


|**Propriété**|**Description**|
|:-----|:-----|
|**ListName** <br/> |Chaîne qui contient le titre ou le GUID de la liste. Lorsque vous interrogez la table UserInfo, la chaîne contient UserInfo. L'utilisation du GUID améliore les performances.  <br/> |
|**ViewName** <br/> |Chaîne qui contient le GUID de l'affichage, déterminant l'affichage à utiliser pour les attributs d'affichage par défaut, représentés par les paramètres  _query_,  _viewFields_ et _rowLimit_. Si cet argument n'est pas fourni, l'affichage par défaut est utilisé. S'il est fourni, la valeur du paramètre  _query_,  _viewFields_ ou _rowLimit_ remplace le paramètre équivalent dans l'affichage. Par exemple, si l'affichage spécifié par le paramètre _viewFields_ a un nombre limite de lignes défini sur 100, mais que le paramètre _rowLimit_ contient une valeur de 1 000, la réponse renverra 1 000 lignes. <br/> |
|**Query** <br/> |Élément  [Query](http://msdn.microsoft.com/fr-fr/library/ms471093.aspx) contenant la requête qui détermine quels enregistrements sont renvoyés et dans quel ordre. <br/> |
|**QueryOptions** <br/> |Fragment XML au format suivant, contenant des nœuds séparés pour les différentes propriétés de l'objet  [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) . <br/> |
|**ChangeToken** <br/> |Chaîne qui contient le jeton de modification de la requête. Pour obtenir une description du format utilisé dans la chaîne, voir  [Vue d'ensemble du journal des modifications](http://msdn.microsoft.com/fr-fr/library/bb417456.aspx). Si la valeur null est transmise, tous les éléments de la liste sont renvoyés.  <br/> |
|**Contains** <br/> |Élément  [Contains](http://msdn.microsoft.com/fr-fr/library/ms196501.aspx) définissant le filtrage personnalisé pour la requête. <br/> |
   

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Familiarisation avec le service REST SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Utilisation de dossiers et de fichiers à l'aide de l'interface REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Naviguer dans la structure de données SharePoint représentée dans le service REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [Déterminer les URI de point de terminaison du service REST SharePoint](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Utiliser les opérations de requête OData dans les demandes REST SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [Exemples, points de terminaison et API REST pour SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [Utiliser des valeurs ETag avec le service REST pour le contrôle de version des éléments de liste](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

