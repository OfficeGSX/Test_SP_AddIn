---
title: Chaînes URL et jetons dans les compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# Chaînes URL et jetons dans les compléments pour SharePoint
Découvrez quels jetons d'URL sont utilisables dans les Compléments SharePoint.
> **IMPORTANTE**
> Pour obtenir des informations générales sur la construction d'URL dans SharePoint 2013 et sur l'utilisation de jetons dans ces URL, voir  [URL et jetons dans SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx). Cette rubrique décrit les jetons disponibles dans les Compléments SharePoint. 
  
    
    


## 
<a name="URLtokens"> </a>

SharePoint 2013 prend en charge les jetons figurant dans les tableaux suivants pour une utilisation dans Compléments SharePoint.
  
    
    
Les jetons figurant dans les tableaux de cette section peuvent être utilisés dans les URL pour le développement des Compléments SharePoint dans la plupart des situations, par exemple dans les actions personnalisées et dans les liens de pages personnalisées. Dans certains cas, quelques-uns de ces jetons ne peuvent pas être utilisés. Les trois emplacements principaux pour lesquels seuls certains jetons peuvent être utilisés sont la page de démarrage d'un complément, une action personnalisée sur le site web hôte et la propriété  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) d'un composant de complément. Ces emplacements sont présentés dans des colonnes séparées, * **mais ils ne constituent pas une liste exhaustive des emplacements où les jetons peuvent être utilisés.*** 
  
    
    
La colonne **StartPage** indique si le jeton peut être utilisé dans l'élément **StartPage** d'un manifeste de complément. La colonne **Action personnalisée** indique si le jeton peut être utilisé dans l'URL d'une action personnalisée sur un site web hôte. Enfin, la colonne **Composant de complément** indique si le jeton peut être utilisé dans la propriété [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) du composant de complément.
  
    
    

**Jetons pouvant être utilisés au commencement d'une URL dans un Complément SharePoint**


|**Jeton**|**Est résolu en**|**StartPage**|**Action personnalisée**|**Composant de complément**|**Remarques**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl  <br/> |L'URL d'un site web de complément d'un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> |Ce jeton ne doit être utilisé qu'à l'extérieur d'un site web de complément. Au sein du site web de complément, utilisez **~site** pour l'URL du site web de complément. <br/> |
|~controlTemplates  <br/> |L'URL du dossier virtuel ControlTemplates pour le site web actuel.  <br/> |Non  <br/> |Non  <br/> |Non  <br/> ||
|~hostUrl  <br/> |URL du site Web hôte.  <br/> |Non  <br/> |Non  <br/> |Oui  <br/> ||
|~hostLogoUrl  <br/> |URL du logo du site Web hôte.  <br/> |Non  <br/> |Non  <br/> |Non  <br/> ||
|~layouts  <br/> |L'URL du dossier virtuel Dispositions pour le site web actuel.  <br/> |Non  <br/> |Non  <br/> |Non  <br/> ||
|~remoteAppUrl  <br/> |L'URL d'une application web à distance dans un Complément SharePoint.  <br/> |Oui  <br/> |Oui, dans le site web hôte. Non dans le site web de complément.  <br/> |Oui  <br/> |Si vous n'utilisez pas Outils de développement Microsoft Office pour Visual Studio pour développer votre complément SharePoint, vous ne pouvez pas utiliser **~remoteAppUrl** dans l'URL **StartPage**. Cependant, si vous utilisez Visual Studio et les outils de développement, vous pouvez utiliser ce jeton pour les compléments hébergés par un fournisseur. Celui-ci est résolu lorsque Visual Studio crée le package du complément. Il s'agit dans ce cas plutôt d'un jeton Visual Studio que d'un jeton SharePoint. Il peut être utilisé en dehors du manifeste de complément même lorsque vous n'utilisez pas Outils de développement Microsoft Office pour Visual Studio.  <br/> |
|~site  <br/> |L'URL du site web actuel.  <br/> |Non  <br/> |Non  <br/> |Oui  <br/> ||
|~sitecollection  <br/> |L'URL de la collection de sites parente du site web actuel.  <br/> |Non  <br/> |Non  <br/> |Oui  <br/> ||
   
Sauf mention contraire, aucun des jetons figurant dans le tableau suivant ne peut être utilisé dans la partie  *chemin d'accès*  de la valeur de propriété [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) du composant de complément. La colonne **Composant de complément** fait référence à l'utilisation des jetons dans la partie *chaîne de requête*  de la valeur.
  
    
    

**Jetons pouvant être utilisés dans une URL**


|**Jeton**|**Est résolu en**|**StartPage**|**Action personnalisée**|**Composant de complément**|**Remarques**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken}  <br/> |Jeton de contexte OAuth du complément.  <br/> |Non  <br/> |Non  <br/> |Non  <br/> ||
|{AppWebUrl}  <br/> |L'URL du site web de complément dans un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> |Ce jeton ne doit être utilisé qu'à l'extérieur d'un site web de complément. Au sein du site web de complément, utilisez **{Site}** pour l'URL du site web de complément. <br/> |
|{ClientTag}  <br/> |Numéro de contrôle de cache client (balise cliente) du site Web actuel.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> ||
|{HostLogoUrl}  <br/> |Le logo du site web hôte d'un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> ||
|{HostTitle}  <br/> |Le titre d'un site web hôte d'un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> ||
|{HostUrl}  <br/> |L'URL du site web hôte d'un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> ||
|{ItemId}  <br/> |ID d'un élément d'une liste ou d'une bibliothèque (un entier).  <br/> |Non  <br/> |Oui  <br/> |Non  <br/> ||
|{ItemUrl}  <br/> |L'URL de l'élément traité.  <br/> |Non  <br/> |Oui  <br/> |Non  <br/> ||
|{Language}  <br/> |La langue/culture actuelle du site web hôte d'un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> ||
|{ListId}  <br/> |L'ID de la liste actuelle (GUID).  <br/> |Non  <br/> |Oui  <br/> |Non  <br/> ||
|{ProductNumber}  <br/> |Numéro de version de build complet de la batterie de serveurs SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> |Exemple : « 15.0.4433.1011 ».  <br/> |
|{RecurrenceId}  <br/> |L'index de périodicité d'un événement périodique.  <br/> |Non  <br/> |Oui  <br/> |Non  <br/> |Ce jeton n'est pas pris en charge dans les menus contextuels des éléments de liste.  <br/> |
|{RemoteAppUrl}  <br/> |L'URL d'une application web à distance dans un Complément SharePoint.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> ||
|{Site}  <br/> |L'URL du site web actuel.  <br/> |Non  <br/> |Oui  <br/> |Oui  <br/> ||
|{SiteCollection}  <br/> |L'URL du site parent du site web actuel.  <br/> |Non  <br/> |Oui  <br/> |Oui  <br/> ||
|{SiteUrl}  <br/> |L'URL du site web actuel.  <br/> |Non  <br/> |Oui  <br/> |Non  <br/> ||
|{Source}  <br/> |URL de la requête HTTP.  <br/> |Non  <br/> |Oui  <br/> |Non  <br/> ||
|{StandardTokens}  <br/> |Voir les remarques.  <br/> |Oui  <br/> |Oui  <br/> |Oui  <br/> |Il combine cinq autres jetons. Il est initialement résolu en  `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}`. Ensuite, chacun de ces jetons est résolu. S'il n'y a pas de site web de complément, la partie  `&amp;SPAppWebUrl={AppWebUrl}` n'est pas présente. <br/> |
   

## Ressources supplémentaires
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [Support extranet avancé](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)
    
  
-  [Obtention de références aux sites, applications Web et autres objets clés](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)
    
  
-  [Utilisation des objets Liste et des collections](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)
    
  
-  [Exemples de tâches de modèle objet](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)
    
  

