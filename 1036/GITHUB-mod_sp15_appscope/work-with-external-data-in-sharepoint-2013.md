---
title: Utiliser des données externes dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# Utiliser des données externes dans SharePoint 2013
Trouvez des ressources et des conseils pour accéder aux données externes et les manipuler avec JavaScript sur des pages de Compléments SharePoint.
## Utiliser JavaScript dans des Compléments SharePoint
<a name="SP15Workdata_Working"> </a>

Dans vos Compléments SharePoint, vous devrez fréquemment récupérer et manipuler des données qui sont exposées par un service ou une application web distante à partir d'une page ou d'un composant SharePoint. Étant donné que le code personnalisé n'est pas autorisé sur les serveurs SharePoint, votre complément doit utiliser JavaScript dans ce but. Le modèle de complément SharePoint offre plusieurs options pour accéder aux services et aux données distants.
  
    
    

### Utiliser la bibliothèque JavaScript inter-domaine de SharePoint pour accéder aux données externes

Vous pouvez utiliser la bibliothèque inter-domaine pour accéder aux données dans votre application web à distance si vous fournissez une page de proxy personnalisée hébergée dans l'infrastructure à distance. En tant que développeur, vous êtes responsable de la mise en œuvre de la page de proxy personnalisée, et vous devez vous charger de la logique personnalisée, telle que le mécanisme d'authentification vers l'application à distance, le cas échéant. Utilisez la bibliothèque inter-domaine pour que la communication entre la source de données à distance et la page SharePoint se produise au niveau du client.
  
    
    
Pour plus d'informations sur cette utilisation de la bibliothèque, voir  [Créer une page de proxy personnalisée pour la bibliothèque inter-domaines dans SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

> [!REMARQUE]
> La bibliothèque inter-domaine SharePoint peut également être utilisée dans l'autre sens. Autrement dit, sur les pages web à distance, JavaScript peut l'utiliser pour accéder aux données à partir de SharePoint. Pour plus d'informations sur cette utilisation de la bibliothèque, voir  [Création de compléments pour SharePoint qui utilisent la bibliothèque inter-domaine](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md). 
  
    
    


### Utiliser le proxy web SharePoint pour accéder aux données externes

Vous pouvez utiliser le proxy web qui est exposé dans le modèle d'objet client JavaScript pour accéder aux données à distance. (Le proxy est également disponible dans le modèle d'objet côté client (CSOM) .NET, mais vous ne pouvez pas utiliser ce modèle d'objet dans le code exécuté sur les serveurs SharePoint.) Lorsque vous utilisez le proxy web, vous émettez la première requête vers SharePoint. À son tour, SharePoint demande les données au point terminal spécifié et transfère la réponse vers votre page. Utilisez le proxy web lorsque vous souhaitez que la communication entre la source de données à distance et la page SharePoint ait lieu au niveau du serveur.
  
    
    
Pour plus d'informations sur l'utilisation du proxy, voir  [Interroger un service distant à l'aide du proxy web](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).
  
    
    

## Ressources supplémentaires
<a name="SP15Workdata_AddRes"> </a>


-  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  

