---
title: Penser de trois manières différentes les options de conception des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# Penser de trois manières différentes les options de conception des compléments pour SharePoint
Dans ce document, vous trouverez une présentation de la conception et des options d'architecture disponibles avec les Compléments SharePoint. Vous devez d'abord vous familiariser avec l'article  [Compléments](sharepoint-add-ins.md).
## Présentation et première décision
<a name="Overview"> </a>

Cet article étudie les choix d'architecture pour les Compléments SharePoint de trois manières différentes. Dans la première, vous apprendrez les catégories les plus importantes pour faire vos choix de conception ; dans la deuxième, vous découvrirez l'architecture de complément en termes de niveau d'application ; enfin, dans la troisième, vous découvrirez un ensemble de facteurs à prendre en considération en faisant vos choix de conception.



Mais la première décision à prendre réside dans le choix de votre extension SharePoint quoi doit être un Complément SharePoint ou une solution de batterie de serveurs SharePoint, ou encore une solution bac à sable. Certaines parties du modèle objet SharePoint, principalement connectées avec la personnalisation de l'administration et de la sécurité SharePoint, ne sont pas accessibles à partir des clients. Seul le code personnalisé exécuté sur le serveur SharePoint peut y accéder, et le code personnalisé côté serveur n'est pas autorisé dans un Complément SharePoint. (Un riche ensemble de modèles objets clients et un service REST/OData permettent aux Compléments SharePoint de créer n'importe quelle extension SharePoint orientée utilisateur final.) Pour plus d'informations sur la décision entre les solutions classiques et les compléments, voir  [Comparaison des compléments pour SharePoint et des solutions SharePoint](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx). Pour vous aider également à prendre cette décision, voir  [Choisir l'ensemble d'API approprié dans SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).




## Éléments clés dans la conception des compléments SharePoint
<a name="MajorCategoriesOfChoices"> </a>

Il existe trois catégories majeures de choix à faire lors de la conception d'un Complément SharePoint. Comme toujours, la conception d'un complément implique des compromis ; les choix que vous faites dans une catégorie peuvent limiter vos options dans une autre. Toutes les combinaisons de choix possibles ne sont pas nécessairement réalisables.




- **Hébergement :** les Compléments SharePoint se divisent en deux types majeurs en fonction de leur déploiement et de leur hébergement.

  -  Les compléments **hébergés par un fournisseur** nécessitent le déploiement et l'hébergement du stockage de données principal et de la logique de système par le développeur (vous), hors de SharePoint dans des serveurs ou sur un compte cloud que vous indiquez. Vous êtes responsable de l'application de l'isolation entre les comptes des divers clients qui achètent votre complément. De tels compléments peuvent avoir aussi des composants SharePoint. Ceux-ci sont hébergés dans la batterie de serveurs SharePoint du client. Ce type de complément vous offre un maximum de flexibilité dans les autres catégories de vos choix de conception. Il permet également d'utiliser des plateformes non Microsoft pour les données externes, la logique et l'interface utilisateur (IU) web. (Dans la catégorie des compléments hébergés par un fournisseur, vous devez également faire la distinction entre les compléments dont les composants à distance sont à l'intérieur du même pare-feu d'entreprise que la batterie de serveurs SharePoint et ceux dont les composants à distance sont situés hors du pare-feu. Les systèmes d'autorisation pour ces deux scénarios sont différents, ce qui, à son tour, crée une différence entre les langages de programmation utilisés pour accéder aux données SharePoint.)


  - **Les compléments hébergés par SharePoint** sont entièrement constitués de composants SharePoint 2013 tels que des listes, des types de contenu, des flux de travail et des composants WebPart. Ils ne comprennent aucun composant externe. Pour plus d'informations sur les différents types de composant SharePoint pouvant être inclus dans les Compléments SharePoint, voir [Héberger des sites web, des sites web de complément et des composants SharePoint dans SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).



    Pour plus d'informations sur les options d'hébergement des Compléments SharePoint, voir  [Choisir les modèles de développement et l'hébergement d'un complément pour SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).


- **Connectivité :** SharePoint 2013 prend en charge trois types d'accès sécurisé aux données pour la création/lecture/mise à jour/suppression (CRUD).

  - Les applications web externes dans un complément utilisent le protocole OAuth pour accéder aux données SharePoint. Pour plus d'informations, voir  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md).


  - JavaScript peut accéder aux données dans un site web de complément SharePoint, et aux données dans d'autres sites web dans la même location en utilisant une bibliothèque JavaScript spéciale qui permet les scripts sécurisés sur plusieurs domaines. Pour plus d'informations, voir  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).


  - Un Complément SharePoint peut également accéder aux données externes via Business Connectivity Services (BCS) ou un proxy de service web. Pour plus d'informations, voir  [Business Connectivity Services dans SharePoint 2013](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx) et [Interroger un service distant à l'aide du proxy web](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).



    Pour plus d'informations sur le stockage et l'accès des données dans les Compléments SharePoint, voir  [Stockage des données dans les compléments SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data),  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) et [Utiliser des données externes dans SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).


- **Interface utilisateur :** il existe trois manières d'exposer un Complément SharePoint dans SharePoint : tous les compléments sont au minimum exposés dans une page web complète. Ensuite, ils peuvent être exposés de manière facultative via un composant du complément, et via un élément de menu ou un bouton du ruban. Pour plus d'informations, voir [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md).

    > **REMARQUE**
      > Les Compléments SharePoint peuvent être installés par vos clients sur plusieurs collections de sites dans une location, ou en procédant site par site. Les premiers sont des compléments de locations. Si vous souhaitez que vos clients puissent utiliser l'option pour les locations, vous ne devez pas inclure de bouton de ruban personnalisé ou de composant de complément. Pour plus d'informations, voir  [Locations et étendues de déploiement des compléments pour SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md). 

## Niveau d'architecture
<a name="Tiers"> </a>

Il existe une autre manière d'appréhender les options d'architecture de votre complément, en le percevant avec trois niveaux logiques : l'interface utilisateur, la logique du système et l'accès aux données. Chaque couche comprend plusieurs options d'implémentation ; de nouveau, les choix effectués pour une couche limitent les options pour d'autres. Les tableaux suivants décrivent certaines de ces options et leur utilisation pour les composants à distance d'un complément et les composants SharePoint.




**Composants à distance dans les composants hébergés par un fournisseur : options pour chaque niveau**


|**Niveau**|**Options**|**S'applique à**|
|:-----|:-----|:-----|
|Interface utilisateur  <br/> |Pages ASP.NET dans un formulaire ASP.NET ou une application MVC hébergée dans un rôle Web Microsoft Azure.  <br/> |Optimisation des compétences du personnel de développement ASP.NET.  <br/> |
||HTML 5 page with JavaScript.  <br/> |Interface utilisateur riche.  <br/> |
||PHP ou autre type de page Web hébergée dans un service cloud non-Microsoft.  <br/> |Intégration d'applications non-Microsoft dans SharePoint.  <br/> |
||Silverlight dans une application Windows Phone.  <br/> |Accès mobile aux données SharePoint et intégration aux données de géolocalisation et notifications Push.  <br/> |
|Logique du système  <br/> |JavaScript côté client.  <br/> |Logique d'interface utilisateur et logique du système simple.  <br/> Accès aux données SharePoint via le modèle objet client JavaScript.  <br/> |
||Rôle de travail Microsoft Azure.  <br/> |Fonctionnalité d'utilisation intensive du processeur.  <br/> Accès aux données SharePoint via le modèle objet client .NET Framework.  <br/> |
||Service Web à distance.  <br/> |Fonctionnalité d'utilisation intensive du processeur.  <br/> Accès aux données SharePoint via le modèle objet client .NET Framework.  <br/> |
|Données  <br/> |SQL Azure.  <br/> |Données relationnelles complètes.  <br/> |
||Stockage de tableaux Microsoft Azure.  <br/> |Paramètres de l'application et autres métadonnées.  <br/> |
||Stockage BLOB Microsoft Azure.  <br/> |Stockage de fichiers volumineux.  <br/> |
||Service du cloud non-Microsoft.  <br/> |Exploitation des sources de données existantes basées sur les plateformes non-Microsoft.  <br/> |
||Base de données sur le propre serveur du développeur.  <br/> |Hébergement chez le fournisseur et contrôle par le développeur de l'isolation de la location.  <br/> |
 

**Composants SharePoint : options pour chaque niveau**


|**Niveau**|**Options**|**S'applique à**|
|:-----|:-----|:-----|
|Interface utilisateur  <br/> |Vues personnalisées des listes et des bibliothèques SharePoint 2013 sur les pages web de complément.  <br/> |Optimisation de l'intégration avec l'apparence visuelle et le comportement de SharePoint.  <br/> |
||Application Silverlight hébergée dans un composant WebPart (ou à l'intérieur de balises <object>) sur une page web de complément.  <br/> |Exploitation de l'expérience de développement existante de Silverlight.  <br/> Interface utilisateur riche.  <br/> |
|Logique du système  <br/> |Flux de travail SharePoint.  <br/> |Implémentation du processus de l'entreprise.  <br/> |
||JavaScript côté client avec la bibliothèque SharePoint multidomaine en supplément.  <br/> |Accès aux données SharePoint dans le site web de complément.  <br/> Accès aux données dans d'autres sites Web de la location.  <br/> |
||Gestionnaire d'événements à distance.  <br/> |Gestion des événements CRUD dans des listes SharePoint et des bibliothèques à l'aide d'une logique hébergée à l'extérieur.  <br/> |
|Données  <br/> |Listes et bibliothèques SharePoint 2013 requises via le CAML (Collaborative Application Markup Language), ou LINQ, requêtes avec un des modèles objets clients SharePoint.  <br/> |Exploitation de l'expérience de développement existante de SharePoint et .NET Framework.  <br/> |
||Listes et bibliothèques SharePoint 2013 questionnées via le service WebREST/OData de SharePoint.  <br/> |Accès aux données SharePoint à partir de plateformes non-Microsoft.  <br/> Exploitation de l'expérience de requête OData.  <br/> |
||Modèle BCS.  <br/> |Exposition des données externes dans SharePoint comme liste SharePoint.  <br/> |
 

## Facteurs à prendre en compte lors de vos décisions de conception
<a name="DecisionFactors"> </a>

Le modèle de Complément SharePoint permet tellement de possibilités de conceptions qu'un simple arbre de décision n'est pas possible. Les facteurs suivants sont parmi les plus importants à considérer lors de la création d'une architecture pour un Complément SharePoint.




- En particulier, bien sûr, vous devez tenir compte de la fonctionnalité que vous souhaitez mettre à la disposition des utilisateurs : ce sont les cas d'utilisation. Par exemple, si votre complément comprend des fonctions qui utilisent le processeur de manière intensive, telles que la conversion des fichiers vidéo dans un autre format, ce serait un argument pour la création d'un complément hébergé par un fournisseur dans lequel le traitement serait effectué sur l'un de vos serveurs ou sur un rôle de travail Microsoft Azure.


- Comme un type de Complément SharePoint (les compléments hébergés par un fournisseur) nécessite que vous (ou votre client) hébergiez les composants non-SharePoint et que vous mettiez en application l'isolation du locataire, vous devez évaluer si vous avez le matériel et le personnel informatique suffisants pour le faire (ou si vos clients ciblés les ont).


- Les clients que vous ciblez sont à prendre en considération de manière tout aussi importante. Si tous vos compléments doivent être utilisés en interne (c'est-à-dire, si vous n'avez pas de clients externes) ou utilisés par un seul client, il est bien plus aisé d'implémenter et de mettre à jour les compléments hébergés par un fournisseur que lorsque vous avez des clients externes ou que plusieurs clients sont susceptibles d'utiliser le complément. Si vous avez l'intention de vendre votre complément publiquement, vous devez également choisir si vous souhaitez le commercialiser à des entreprises qui ont des comptes SharePoint Online ou à celles qui ont leur propre batterie de serveurs SharePoint, ou les deux.


- Vous devez également prendre en compte vos compétences ou les compétences de votre personnel de développement. Par exemple, si vous êtes un développeur ASP.NET expérimenté, c'est un argument supplémentaire pour la création d'une application web à distance et l'exposition des données de liste SharePoint sur une page ASP.NET. Par contre, si vous êtes un développeur SharePoint expérimenté, c'est un argument en faveur de l'utilisation d'une liste SharePoint personnalisée et d'une page de site, avec JavaScript pour effectuer le traitement.



## Ressources supplémentaires
<a name="AdditionalResources"> </a>


-  [Concevoir des compléments pour SharePoint](design-sharepoint-add-ins.md)


-  [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)



