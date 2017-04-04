---
title: Trois systèmes d'autorisation pour les compléments SharePoint
ms.prod: SHAREPOINT
ms.assetid: 623fdab7-856e-4a89-9f5d-748a2ba1ef2e
---


# Trois systèmes d'autorisation pour les compléments SharePoint
Découvrez les trois systèmes que les Compléments SharePoint peuvent utiliser afin d'obtenir une autorisation pour les ressources SharePoint.
Dans SharePoint, un Complément SharePoint est une identité de principal, comme un utilisateur, et doit être authentifié et autorisé à employer les ressources SharePoint. Il existe trois systèmes d'autorisation qui peuvent être utilisés par un complément. Ils ne s'excluent pas mutuellement.
  
    
    


## Comprendre les trois systèmes d'autorisation et savoir quand les utiliser
<a name="UnderstandThreeSystems"> </a>


  
    
    

- **Faible niveau de fiabilité**: un Complément SharePoint hébergé par un fournisseur peut s'inscrire auprès de Microsoft Azure Access Control Service (ACS), qui émet un jeton d'accès pour le complément. Ce jeton permet à ce dernier d'accéder aux ressources dans la location ou la batterie de serveurs SharePoint sur laquelle le complément est installé. Azure ACS est l'émetteur de jeton approuvé dans un « flux » d'infrastructure OAuth 2.0 qui comprend SharePoint et les composants distants du composant. Les composants qui utilisent ce système peuvent être vendus dans l'Office Store. Le système à faible niveau de fiabilité est principalement destiné aux compléments dont les composants distants sont hébergés dans le nuage.
    
    Pour plus d'informations sur la création d'un Complément SharePoint qui utilise le système à faible niveau de fiabilité, voir le nœud SDK  [Création de compléments pour SharePoint qui utilisent l'autorisation de faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md).
    
    > **REMARQUE**
      > Le client qui installe le complément doit avoir un compte Office 365. Ceci est nécessaire pour autoriser le complément à accéder à Azure ACS. Toutefois, le client ne doit pas utiliser ce compte dans un autre but, et le complément peut être installé sur une batterie de serveurs SharePoint en local après certaines tâches de configuration basiques. 
- **Haut niveau de fiabilité**: un complément hébergé par un fournisseur peut établir une connexion sécurisée à SharePoint avec des certificats numériques. Le système à haut niveau de fiabilité est principalement destiné à des compléments dont les composants distants sont hébergés en local. Le complément peut être installé sur une batterie de serveurs SharePoint qui n'est pas connectée à Internet. Le complément ne peut pas être installé sur SharePoint Online ni vendu dans l'Office Store.
    
    Pour plus d'informations sur la création d'un Complément SharePoint qui utilise le système à haut niveau de fiabilité, voir le nœud SDK  [Création de compléments pour SharePoint qui utilisent l'autorisation à haut niveau de fiabilité](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).
    
  
- **Bibliothèque inter-domaine**: lorsque la logique métier du complément est en JavaScript, vous avez la possibilité d'utiliser la bibliothèque inter-domaine SharePoint à la place ou en complément du système à faible niveau de fiabilité et du système à haut niveau de fiabilité. La bibliothèque est également conçue pour les situations dans lesquelles le complément possède des composants hébergés dans le nuage, et que le pare-feu de l'entreprise du client complique l'utilisation du système à faible niveau de fiabilité. Le navigateur de l'utilisateur bloque les scripts provenant d'autres domaines, mais la bibliothèque encapsule un système sécurisé pour contourner cette restriction. Les compléments qui utilisent la bibliothèque peuvent être vendus dans l'Office Store et installés sur SharePoint Online ou sur SharePoint en local.
    
    Pour plus d'informations sur la création d'un Complément SharePoint qui utilise la bibliothèque inter-domaine, voir le nœud SDK  [Création de compléments pour SharePoint qui utilisent la bibliothèque inter-domaine](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md), le billet de blog  [Résolution des problèmes inter-domaines dans les compléments SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx).
    
  

## Informations générales sur l'infrastructure d'OAuth 2.0 et sur son implémentation SharePoint
<a name="UnderstandThreeSystems"> </a>

Deux sur les trois systèmes d'autorisation utilisent l'infrastructure OAuth 2.0, qui est une **infrastructure ouverte d'autorisation**. OAuth permet d'accorder des autorisations sécurisées depuis un ordinateur de bureau, des périphériques et des applications web de manière standardisée. OAuth permet à son utilisateur d'autoriser une application à agir en son nom sans partager son nom d'utilisateur et son mot de passe. Par exemple, il **permet à l'utilisateur de partager** ses ressources ou ses données privées (listes de contacts, documents, photos, vidéos, etc.) stockées sur un seul site avec un autre site, **without requiring the user to provide his or her credentials** (généralement un nom d'utilisateur et un mot de passe) avec l'autre site.
  
    
    
OAuth permet aux utilisateurs de fournir des jetons d'accès aux données hébergées par un certain fournisseur de services (par exemple, SharePoint). Chaque jeton donne accès à un fournisseur de ressources particulier (comme un site web SharePoint) pour des ressources spécifiques (documents d'une bibliothèque SharePoint, par exemple) et pour une durée déterminée (12 heures, par exemple). Cela permet aux utilisateurs d'accorder à une application de bureau ou web tierce un accès aux informations stockées chez un autre fournisseur de services ou ressources (SharePoint, dans cas), sans fournir leur nom ni leur mot de passe, et sans partager  *toutes*  les données qu'ils possèdent avec le fournisseur.
  
    
    
SharePoint utilise l'infrastructure **OAuth 2.0** et les **« flux » de transmission de jeton** pour :
  
    
    

- Autoriser les demandes émises par un Complément SharePoint pour accéder aux ressources SharePoint.
    
  
- Authentifier les compléments dans l'Office Store, dans un catalogue de compléments ou chez un client développeur.
    
  
Pour en savoir plus sur OAuth et sa terminologie, voir le site  [OAuth.net](http://oauth.net/) et le site relatif au [protocole d'autorisation web (oauth)](http://datatracker.ietf.org/doc/active/). En bref, il s'agit d'un serveur de ressources qui héberge une ressource protégée, un propriétaire de la ressource, une application client qui cherche à avoir accès à la ressource et un serveur d'autorisation qui émet des jetons d'accès à la ressource lorsque le propriétaire de la ressource le demande. La documentation officielle d'OAuth a tendance à partir du principe qu'un seul propriétaire de ressource donne accès à la ressource à partir de l'application cliente chaque fois que l'application cliente est exécutée. Elle suppose également que la personne qui utilise l'application cliente est le propriétaire de la ressource. L'implémentation SharePoint tient compte du fait qu'une ressource SharePoint, telle qu'une liste, est partagée entre plusieurs utilisateurs. En outre, dans l'implémentation SharePoint, l'accès est accordé au Complément SharePoint lorsqu'il est installé, et pas à chaque fois qu'il est exécuté. De plus, il peut être exécuté par des utilisateurs autres que la personne qui l'a installé et qui lui a accordé l'accès. (Dans le cas des compléments qui ne sont pas installés sur SharePoint, mais qui ont accès aux ressources SharePoint (ce sont donc uniquement des « Compléments SharePoint » au sens large), l'utilisateur exécutant le complément doit accorder des autorisations à chaque fois que le complément est exécuté.)
  
    
    
Ainsi, dans l'implémentation SharePoint, les rôles OAuth sont répartis comme suit :
  
    
    

- Le composant distant d'un Complément SharePoint joue le rôle de l'application cliente.
    
  
- Les utilisateurs de SharePoint jouent le rôle de propriétaire de ressource.
    
  
- SharePoint joue le rôle du serveur de ressources.
    
  
- Azure ACS joue le rôle de serveur d'autorisation lorsque le  [système d'autorisation à faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) est utilisé. Lorsque le [système à haut niveau de fiabilité ](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) est utilisé, le complément lui-même (avec un certificat numérique) devient le serveur d'autorisation.
    
  

### Les jetons d'accès ne sont pas des jetons de connexion
<a name="FileName_uniquekeyword3"> </a>

Comme décrit ci-dessus, pour accéder aux ressources, un complément doit demander un jeton d'accès à un serveur d'autorisation OAuth qui a été précédemment désigné comme émetteur de jeton de sécurité fiable (STS) par le propriétaire de la ressource. Par opposition, le STS WS-Federation et le STS de connexion passive Security Assertion Markup Language (SAML) sont essentiellement conçus pour émettre des jetons de connexion. Dans SharePoint, un STS OAuth ne sert pas à émettre des jetons de connexion, ce qui signifie qu'ils ne sont pas utilisés comme fournisseurs d'identité. Par conséquent, aucun STS OAuth ne figurera sur la page de connexion de l'utilisateur, dans la section **Fournisseur d'authentification** de l'administration centrale, ou dans le sélecteur de personnes de SharePoint.
  
    
    
Les administrateurs SharePoint peuvent utiliser les commandes Windows PowerShell pour activer ou désactiver un STS OAuth, de la même manière qu'ils peuvent activer ou désactiver les fournisseurs de connexions approuvés dans SharePoint 2010. 
  
    
    

