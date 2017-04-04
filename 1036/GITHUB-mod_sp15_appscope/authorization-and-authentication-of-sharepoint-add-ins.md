---
title: Autorisation et authentification des compléments dans SharePoint
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# Autorisation et authentification des compléments dans SharePoint
Obtenez une vue d'ensemble de l'authentification et de l'autorisation dans SharePoint, ce qui permet d'autoriser les demandes d'accès aux ressources SharePoint émises par un Complément SharePoint.
**Regardez une vidéo sur l'identité du complément.**

  
    
    

  
    
    
![Vidéos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## Authentification des compléments dans SharePoint
<a name="AuthN"> </a>

Lorsqu'un  *utilisateur*  se connecte à SharePoint, son jeton de sécurité est validé. Le jeton est émis par un fournisseur d'identité. SharePoint prend en charge plusieurs types d'authentification d'utilisateur. Pour en savoir plus, voir [Authentification, autorisation et sécurité dans SharePoint 2013](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx).
  
    
    
Les Compléments SharePoint sont également des principaux de sécurité qui doivent être authentifiés et autorisés. Il existe plusieurs manières d'authentifier et d'autoriser les compléments. Pour en savoir plus, voir  [Trois systèmes d'autorisation pour les compléments SharePoint](three-authorization-systems-for-sharepoint-add-ins.md). 
  
    
    

## Stratégies d'autorisation : stratégie utilisateur uniquement, stratégie utilisateur + complément, stratégie complément uniquement
<a name="AuthZ"> </a>

Le processus d'autorisation vérifie qu'un sujet authentifié (un complément, un utilisateur ou les deux) possède l'autorisation d'effectuer certaines opérations ou d'accéder à des ressources spécifiques (une liste ou un dossier de documents SharePoint, par exemple).
  
    
    
SharePoint utilise trois types de stratégies d'autorisation. La stratégie utilisateur uniquement requiert uniquement que l'appel à SharePoint contienne une identité d'utilisateur authentifiée. Avec la stratégiecomplément uniquement, l'appel doit seulement inclure une identité de complément authentifiée. Enfin, la stratégie utilisateur + complément nécessite que l'appel contienne les deux types d'identités authentifiées. Lorsqu'un utilisateur accède aux ressources SharePoint par l'interface utilisateur SharePoint plutôt que par un complément, SharePoint emploie la stratégie utilisateur uniquement. En revanche, pour les appels provenant d'un Complément SharePoint, SharePoint utilise toujours la stratégie complément uniquement ou la stratégie utilisateur + complément. Le Complément SharePoint détermine quelle stratégie est utilisée par le type de jeton d'accès inclus dans sa demande à SharePoint. Si une demande utilisateur + complément est effectuée, SharePoint exige que le complément et l'utilisateur disposent tous les deux d'une autorisation pour accéder à la ressource à laquelle le complément accède. Dans le cas d'une demande complément uniquement, SharePoint exige que le complément dispose de l'autorisation pour accéder à la ressource, sans se soucier de l'utilisateur. (Un Complément SharePoint peut effectuer des demandes complément uniquement si l'autorisation nécessaire lui a été préalablement attribuée, ce qui se fait généralement lors de son installation.)
  
    
    
Pour plus d'informations sur les stratégies d'autorisation et sur leur mode de fonctionnement, voir  [Types de stratégie d'autorisation des compléments dans SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Autorisations de complément et étendues des demandes d'autorisation de complément
<a name="Permissions"> </a>

Le développeur d'un Complément SharePoint doit spécifier, dans le fichier manifeste du complément, les autorisations dont a besoin un complément pour accéder aux ressources SharePoint en dehors du site web du complément. (Le complément reçoit automatiquement l'autorisation de contrôle total pour l'ensemble du site web du complément.) Lorsque le complément est conçu pour être lancé depuis SharePoint, son infrastructure d'installation demande à l'utilisateur qui l'installe d'accorder ou de refuser les autorisations nécessaires. Une fois les autorisations accordées, les utilisateurs du site web peuvent utiliser le complément sans avoir à accorder à nouveau les autorisations nécessaires. Toutefois, lorsque le complément est conçu pour être lancé en dehors de SharePoint, c'est-à-dire lorsqu'il n'est pas installé sur SharePoint, SharePoint invite l'utilisateur qui exécute le complément à accorder les autorisations nécessaires chaque fois que le complément est exécuté. Par exemple, les compléments exécutés sur les appareils mobiles et les Compléments Office peuvent accéder à SharePoint, mais n'y sont pas installés.
  
    
    
Seul le propriétaire d'un site web peut installer un Complément SharePoint sur un site web SharePoint (sauf si un rôle personnalisé a été créé avec les droits d'installation du complément). Un utilisateur ne peut accorder à un complément que les autorisations dont il dispose lui-même. Ainsi, un utilisateur ne peut pas installer un complément qui nécessite des autorisations dont il ne dispose pas. De même, il ne peut pas exécuter un complément lancé en externe qui demande des autorisations qu'il ne possède pas. En revanche, lorsqu'un Complément SharePoint est installé sur SharePoint, il peut demander l'autorisation d'effectuer des appels d'application uniquement. Un complément autorisé peut accéder à SharePoint par des moyens que l'utilisateur qui l'exécute n'est pas autorisé à utiliser.
  
    
    
Les autorisations des compléments peuvent également être révoquées ou accordées par les administrateurs client SharePoint Online ou les administrateurs de batterie SharePoint.
  
    
    
Dans le fichier manifeste du complément, un Complément SharePoint spécifie les autorisations dont il a besoin pour fonctionner correctement. Les demandes d'autorisation indiquent à la fois les droits dont a besoin un complément et l'étendue de ces droits. Les étendues indiquent à quel niveau de la hiérarchie SharePoint la demande d'autorisation s'applique. SharePoint prend en charge quatre étendues de contenu : location, collection de sites, site web et liste. Il existe également des étendues spéciales permettant d'effectuer des requêtes de recherche ou d'accéder à des données de taxonomie, ainsi qu'à des fonctionnalités sociales, Microsoft Business Connectivity Services (BCS) et Project Server 2013. Pour plus d'informations, voir  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## À quel moment OAuth est-il utilisé ?
<a name="FileName_uniquekeyword4"> </a>

Vous savez peut-être déjà qu'OAuth 2.0 joue un rôle important dans les processus d'authentification et d'autorisation des Compléments SharePoint. C'est effectivement le cas, mais pas nécessairement pour le processus d'autorisation de chaque Complément SharePoint. Si vous envisagez de créer un Complément SharePoint qui s'exécute dans une application web distante et communique avec SharePoint à l'aide du code côté serveur, vous devrez utiliser OAuth. Si l'application web distante ne se trouve pas en local, vous utiliserez le  [système d'autorisation à faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) dans lequel Azure ACS est l'émetteur de jeton d'accès. Si l'application se trouve en local, vous utiliserez généralement le [système à haut niveau de fiabilité](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) dans lequel le complément lui-même et un certificat numérique sont les émetteurs de jetons d'accès.
  
    
    
Vous n'utiliserez pas OAuth pour appeler à partir de JavaScript sur une page dans le site web du complément ou à partir d'une page web distante utilisant la  [bibliothèque inter-domaine](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md). Pour obtenir plus d'informations sur la bibliothèque inter-domaine, voir  [Création de compléments pour SharePoint qui utilisent la bibliothèque inter-domaine](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md).
  
    
    

## Ressources supplémentaires
<a name="Filename_AdditionalResources"> </a>


-  [Trois systèmes d'autorisation pour les compléments SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Compléments](sharepoint-add-ins.md)
    
  

