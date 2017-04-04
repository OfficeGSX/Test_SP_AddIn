---
title: Création de compléments pour SharePoint qui utilisent la bibliothèque inter-domaine
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# Création de compléments pour SharePoint qui utilisent la bibliothèque inter-domaine
Découvrez la bibliothèque JavaScript inter-domaine SharePoint.
Il existe quelques scénarios dans lesquels ni les systèmes d'autorisation de haut niveau de fiabilité ni ceux à faible niveau de fiabilité ne peuvent être utilisés par un Complément SharePoint, ou bien ce choix n'est pas approprié, car c'est le seul moyen pour le complément d'obtenir une autorisation pour les ressources SharePoint. Exemples :
  
    
    


- Les composants distants du Complément SharePoint ne sont pas locaux, mais un pare-feu d'entreprise bloque la communication de serveur à serveur entre SharePoint et ACS, empêchant ainsi l'utilisation du système d'autorisation à faible niveau de fiabilité.
    
  
- Le Complément SharePoint est conçu comme une application web à une seule page qui repose sur JavaScript côté client pour les opérations de données avec SharePoint.
    
  
- Le Complément SharePoint repose principalement sur les appels de serveur à serveur pour accéder aux données SharePoint (et est autorisé par les systèmes à faible niveau de fiabilité ou à haut niveau de fiabilité), mais il doit être complété par des appels JavaScript. Par exemple, une page riche en graphiques peut utiliser JavaScript pour effectuer des mises à jour mineures au niveau des données affichées sans avoir à recharger la page entière.
    
  

Toutefois,  [par sécurité](http://msdn.microsoft.com/fr-fr/library/cc709423%28vs.85%29.aspx), les navigateurs n'autorisent pas un JavaScript hébergé sur un domaine à accéder aux ressources sur un autre domaine. Une technique spéciale est donc requise pour autoriser le JavaScript à distance à accéder aux ressources SharePoint. La bibliothèque JavaScript inter-domaine SharePoint permet à votre application web à distance d'utiliser facilement cette technique.
  
    
    


> **REMARQUE**
> La bibliothèque inter-domaine est également utilisée pour autoriser l'accès aux données dans le sens inverse, autrement dit pour autoriser JavaScript sur une page SharePoint à accéder aux données dans un domaine à distance. Pour plus d'informations, voir  [Accès aux données à distance à partir d'une page SharePoint](#ReverseDirection). 
  
    
    


## Comprendre l'architecture de la bibliothèque inter-domaine

La bibliothèque inter-domaine SharePoint est contenue dans le fichier SP.RequestExecutor.js, qui se trouve dans le dossier virtuel /_layouts/15/ de chaque site web SharePoint. Les scripts de ce fichier encapsulent une technique bien connue et sécurisée pour surmonter la restriction du navigateur concernant la génération de scripts inter-domaines : un iFrame peut communiquer avec sa page parent via la fonction  `window.postMessage()`, même si le iFrame est dans un domaine différent. Ainsi, les requêtes de données et leurs réponses sont transmises au-delà des limites de domaines à l'aide d'appels vers  `postMessage()`.
  
    
    

> **ATTENTION**
> La fonction  `postMessage()` ne donne de résultats que sur les navigateurs prenant en charge HTML 5. Par conséquent, les Compléments SharePoint qui utilisent la bibliothèque inter-domaine ne fonctionnent pas sur les navigateurs plus anciens.
  
    
    

Pour SharePoint, la bibliothèque inter-domaine est chargée sur une page de l'application web à distance où elle crée un iFrame caché qui héberge une page proxy spéciale à partir du domaine SharePoint. La page proxy existe déjà sur tous les sites web SharePoint. La bibliothèque est utilisée pour créer un objet JavaScript Object Notation (JSON) qui contient toutes les informations nécessaires pour effectuer un appel CRUD vers l'API REST de SharePoint. L'objet JSON est transmis à la page proxy à l'aide de  `postMessage()`. Sur la page proxy, où la bibliothèque est également chargée, l'objet JSON est analysé et recréé en tant qu'appel REST vers SharePoint. Étant donné que la page proxy se trouve dans le domaine SharePoint, le navigateur autorise l'appel.
  
    
    
Évidemment, les composants distants du Complément SharePoint doivent encore disposer d'une autorisation d'accès aux ressources SharePoint. Pour ce faire, deux méthodes sont possibles :
  
    
    

- Définissez le type de principal de complément sur **RemoteWebApplication** (par défaut pour les applications hébergées par un fournisseur) dans le manifeste de complément. Une fois le complément inscrit auprès d'ACS, l'inscription inclut le domaine de l'application web distante. SharePoint approuve les domaines inscrits auprès d'ACS, même si, dans ce cas, il n'utilise aucun flux de transmission de jeton qui appartient au système à faible niveau de fiabilité côté serveur. Pour plus de détails sur l'inscription des compléments, voir [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md). 
    
  
- Dans un complément hébergé sur SharePoint, vous pouvez conserver la valeur par défaut du type de principal de complément, à savoir **Internal**. Définissez ensuite l'attribut **AllowedRemoteHostUrl** de l'élément **Internal** sur l'URL de l'application web à distance, comme dans l'exemple suivant.
    ```
<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>```


> **REMARQUE**
> Si vous optez pour la deuxième option (un principal de complément **Internal**), vous pouvez utiliser uniquement JavaScript et la bibliothèque inter-domaines pour accéder à SharePoint. Le modèle objet client SharePoint est bloqué pour les Compléments SharePoint **Internal**. Vous ne pouvez donc pas disposer d'un double système d'autorisation qui utilise à la fois la bibliothèque inter-domaines et le système à faible niveau de fiabilité ou le système à haut niveau de fiabilité. 
  
    
    

Pour plus d'informations sur l'utilisation de la bibliothèque, voir  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Accès aux données à distance à partir d'une page SharePoint
<a name="ReverseDirection"> </a>

La bibliothèque inter-domaines SharePoint peut également être utilisée dans le sens inverse ; autrement dit, JavaScript sur une page SharePoint peut utiliser la bibliothèque pour obtenir des données à partir des composants distants du complément. Pour ce faire, inversez l'architecture inter-domaine : vous créez une page proxy dans l'application web à distance. La bibliothèque est appelée à partir d'une page SharePoint où elle crée un iFrame pour héberger la page proxy. Pour plus d'informations sur une telle utilisation de la bibliothèque, voir  [Créer une page de proxy personnalisée pour la bibliothèque inter-domaines dans SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

## Dans cette section
<a name="ReverseDirection"> </a>


-  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [Utiliser la bibliothèque inter-domaines dans différentes zones de sécurité Internet Explorer dans les compléments pour SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  

## Ressources supplémentaires
<a name="ReverseDirection"> </a>


-  [Résolution des problèmes inter-domaines dans les compléments SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)
    
  

