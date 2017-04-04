---
title: Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: ae96572b-8f06-4fd3-854f-fc312f7f2d88
---


# Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint
Découvrez les aspects de l'architecture des Compléments SharePoint et du modèle de complément SharePoint, notamment les options d'hébergement du complément, les options de l'interface utilisateur, le système de déploiement, le système de sécurité et le cycle de vie. Cet article complète les informations de l'article  [Compléments](sharepoint-add-ins.md).
## Options d'hébergement des compléments pour SharePoint
<a name="SPAppModelArch_SPCenteredVsCloudCentered"> </a>

Le modèle de complément SharePoint 2013 propose les solutions d'hébergement suivantes pour les composants d'un Complément SharePoint : 
  
    
    

- **Hébergés par un fournisseur :** compléments incluant au moins un composant distant et pouvant également inclure des composants SharePoint. Les autres composants sont déployés par votre logique sur votre matériel ou dans votre compte cloud. Il est aussi possible de les déployer sur le matériel ou sur le compte cloud du client à l'aide des instructions et des programmes d'installation que vous fournissez.
    
  
- **Hébergés sur SharePoint :** compléments comprenant uniquement les composants SharePoint et la logique qui fonctionne sur le client.
    
  
Pour obtenir des informations plus détaillées sur les options d'hébergement et des instructions pour faire votre choix, voir  [Choisir les modèles de développement et l'hébergement d'un complément pour SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).
  
    
    

## Sites web de complément, sites web hôtes, Fonctionnalités et composants SharePoint dans les compléments
<a name="SPComponents"> </a>

Le site web où un Complément SharePoint est installé est appelé le site web hôte. Cependant, les composants importants du Complément SharePoint, qu'ils soient des composants SharePoint ou externes, ne sont pas déployés sur le site web hôte. Les composants externes sont déployés sur les serveurs externes ou sur des comptes sur le nuage. Les composants SharePoint sont déployés sur un site web spécial qui dispose de son propre domaine. Ce site est appelé le site web de complément.. Seul un ensemble limité d'éléments de l'interface utilisateur donnant aux utilisateurs accès aux autres composants du complément sont déployés sur le site web hôte. Ces composants de l'interface utilisateur sur le site web hôte sont déployés en tant que composants d'une fonctionnalité du site web hôte, une Fonctionnalité flexible dans le package de complément et non dans un fichier .wsp. Les composants déployés sur le site web de complément sont toujours dans les Fonctionnalités dans un fichier .wsp. Les deux types de Fonctionnalités doivent avoir une étendue **Web**. Aucune autre étendue n'est possible pour les Fonctionnalités dans les Compléments SharePoint.
  
    
    
En règle générale, tout composant SharePoint qui ne comprend pas de code personnalisé fonctionnant sur les serveurs SharePoint peut être inclus dans un Complément SharePoint (et être déployé sur le site web de complément). Cependant, il existe des exceptions et des nuances concernant la manière et l'emplacement de déploiement des composants. Pour plus d'informations sur ces nuances et sur les sites web hôtes, les sites web de complément isolés et les Fonctionnalités des compléments, voir  [Héberger des sites web, des sites web de complément et des composants SharePoint dans SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).
  
    
    

## Accéder au complément à partir de l'interface utilisateur
<a name="AccessingApp"> </a>

Quand un Complément SharePoint est installé sur un site web, le complément est répertorié sur la page **Contenu du site** du site web hôte. Les utilisateurs peuvent démarrer le complément à partir de cette page. Ouvert de cette manière, le complément fonctionne en mode plein écran.
  
    
    
Une autre manière d'exposer un Complément SharePoint est par l'intermédiaire d'un composant de complément, un type de composant WebPart représenté par la classe **ClientWebPart**. Ce type de composant WebPart est essentiellement un wrapper d'IFrame qui héberge une page du complément. Dans le cas le plus simple, la seule propriété importante du composant WebPart est une URL qui pointe vers la page. Néanmoins, les composants WebPart peuvent disposer de propriétés personnalisées que les utilisateurs peuvent définir dans le composant Tool Part. Ces propriétés pourraient par exemple servir à définir des informations contextuelles, telles que le code postal de l'utilisateur. Pour inclure un tel composant de complément dans votre complément, vous créez une Fonctionnalité de site web hôte dans le complément et ajoutez un balisage de composant WebPart déclaratif. Comme tout autre composant WebPart, il apparaît dans l'interface utilisateur SharePoint 2013 à partir de laquelle les utilisateurs ajoutent des composants WebPart. Vous pouvez disposer de plusieurs composants de complément déployés avec votre complément si vous avez besoin de davantage de variabilité. Par exemple, un complément météo peut disposer d'un composant de complément indiquant la météo actuelle et d'un second composant de complément indiquant les prévisions météorologiques hebdomadaires. Les deux composants disposent de tailles et de fonctionnalités différentes.
  
    
    

> **REMARQUE**
> Vous pouvez également déployer les composants de complément sur le site web de complément. Pour implémenter cela, le balisage du composant WebPart sera un composant de fonctionnalité dans un fichier .wsp dans le package de complément, pas dans la fonctionnalité du site web hôte. 
  
    
    

Nous vous recommandons d'essayer de donner à vos compléments une apparence SharePoint autant que possible, bien que cela ne soit pas obligatoire et parfois pas la meilleure solution. Pour plus d'informations sur les instructions concernant l'expérience utilisateur, voir  [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md). 
  
    
    
Il existe par exemple un page maître spéciale appelée app.master. Cette page est optimisée pour être utilisée par les pages des compléments. La page app.master fait partie d'une nouvelle définition de site incluse dans SharePoint 2013. 
  
    
    
Un autre outil que vous pouvez utiliser pour que vos compléments conservent une convivialité cohérente avec SharePoint est le contrôle de chrome fourni avec SharePoint 2013. Ce contrôle vous permet d'ajouter la zone d'en-tête de navigation SharePoint à vos pages de complément, y compris les pages hébergées de façon externe. Pour plus d'informations sur la conception de l'expérience utilisateur dans les Compléments SharePoint, voir  [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md). Pour plus d'informations sur le contrôle de chrome, voir  [Utiliser le contrôle de chrome client dans les compléments pour SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md).
  
    
    

## Structure du package de complément
<a name="SPAppModelArch_Package"> </a>

Un package de Complément SharePoint est un fichier disposant d'une extension « .app » et qui est conforme aux  [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/fr-fr/magazine/cc163372.aspx). Vous pouvez ouvrir le fichier en ajoutant « .zip » en tant qu'extension supplémentaire du nom de fichier, puis l'ouvrir dans Windows Explorer.) Il contient un manifeste de complément spécifiant certaines propriétés du complément et des instructions pour l'infrastructure d'installation SharePoint. Pour plus d'informations sur le manifeste et le package de complément, voir  [Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md).
  
    
    

## Autorisations et authentification pour les compléments SharePoint
<a name="SPAppModelArch_Running"> </a>

SharePoint 2013 présente un nouveau système d'autorisation et de sécurité pour les compléments.
  
    
    

### Autorisations de complément
<a name="AppPermissions"> </a>

Les Compléments SharePoint disposent d'autorisations, tout comme les utilisateurs et les groupes. Un complément peut posséder un ensemble d'autorisations différentes de celles dont dispose l'utilisateur qui exécute le complément. 
  
    
    
Dans le fichier manifeste du complément, vous devez demander les autorisations qu'un complément doit exécuter. L'utilisateur qui ajoute le complément doit accorder ces requêtes et l'utilisateur ne peut accorder que les autorisations dont il dispose en tant qu'utilisateur. Afin de simplifier la gestion des autorisations pour les utilisateurs et les développeurs, toutes les autorisations demandées doivent être accordées, ou aucune. Le principal de complément dispose toujours du contrôle total sur le site web de complément, ainsi il doit seulement demander des autorisations aux ressources SharePoint dans le site web hôte ou d'autres emplacements hors du site web de complément.
  
    
    
Pour plus d'informations sur les autorisations de complément, voir  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Délégation et autorisation sélectives
<a name="SelectiveAuthorization"> </a>

Les utilisateurs lançant un complément et les propriétaires de ressources accordant une autorisation de complément pour accéder à une ressource ne doivent pas fournir leurs informations d'identification ou un mot de passe au complément. En revanche, SharePoint 2013 permet aux utilisateurs et aux propriétaires de ressources de n'accorder que les autorisations spécifiques demandées par le complément. Cela est rendu possible par l'utilisation par SharePoint 2013 du protocole de transaction  [OAuth 2.0](http://tools.ietf.org/html/draft-ietf-oauth-v2-22). Pour plus d'informations sur OAuth dans SharePoint 2013, voir  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### Accès inter-domaines
<a name="SelectiveAuthorization"> </a>

Un Complément SharePoint incluant une application web distante qui utilise JavaScript pour sa logique d'accès aux données peut utiliser une bibliothèque inter-domaines JavaScript pour obtenir l'accès aux données SharePoint dans la location d'installation du complément. Pour plus d'informations, voir  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Cycle de vie d'un complément
<a name="SPAppModelArch_Lifecycle"> </a>

Le cycle de vie d'un Complément SharePoint inclut la publication, l'installation, la mise à niveau et la désinstallation. Pour plus d'informations sur ces sujets, voir  [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md),  [Déploiement et installation de compléments pour SharePoint : méthodes et options](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) et [Processus de mise à jour des compléments pour SharePoint](sharepoint-add-ins-update-process.md). Veuillez noter qu'il existe un mécanisme permettant aux administrateurs locataires d'installer par lot un Complément SharePoint sur plusieurs sites web. Pour plus d'informations, voir  [Locations et étendues de déploiement des compléments pour SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md).
  
    
    

## Stockage des données dans les compléments SharePoint
<a name="Data"> </a>

Les Compléments SharePoint peuvent créer tout type de données et y accéder, y compris les données structurées, les documents et les fichiers multimédias. Ces données peuvent être stockées dans SharePoint ou à un emplacement externe. 
  
    
    

### Options de stockage de données structurées
<a name="StructuredData"> </a>

Un Complément SharePoint peut utiliser presque tout type de stockage de données structurées, à l'intérieur ou à l'extérieur de SharePoint 2013, ainsi que sur les plates-formes Microsoft et non-Microsoft. Voici  *quelques*  emplacements dans lesquels vous pouvez stocker des données structurées pour un Complément SharePoint :
  
    
    

- Listes SharePoint dans un site web de complément
    
  
- SQL Azure
    
  
- Sources de données externes connectées à SharePoint avec Microsoft Business Connectivity Services (BCS)
    
  
- Service de nuage non Microsoft
    
  
- Une base de données sur votre propre serveur
    
  

> **CONSEIL**
> Vous devrez probablement mettre à niveau votre Complément SharePoint à un moment donné. Lorsqu'un Complément SharePoint inclut des composants SharePoint sur un site web de complément, le processus de mise à niveau procède à une copie complète du site web de complément. Pour cette raison, la présence de longues listes SharePoint sur le site web de complément allonge le processus de mise à niveau et entraîne une utilisation intensive du processeur sur le serveur de base de données de contenu. Vous devez éviter de placer des « données volumineuses » dans les listes SharePoint sur le site web de complément. 
  
    
    


### Options de stockage de données non structurées
<a name="UnStructuredData"> </a>

Les documents, images, vidéos, fichiers audio et autres types de données non structurées qui sont produits ou utilisés par un Complément SharePoint peuvent être stockés à l'intérieur ou à l'extérieur de SharePoint. Les bibliothèques de documents sont un bon choix pour des documents et sont consultables par le biais d'une recherche SharePoint. Une bibliothèque d'éléments de site est souvent un choix approprié pour les fichiers multimédias. 
  
    
    
Les autres options incluent le stockage de données blob sur votre compte Microsoft Azure ou sur vos propres serveurs. Vous pouvez également stocker des fichiers sur certaines plateformes non Microsoft ou sur certains services basés sur le nuage.
  
    
    

### Paramètres de complément et autres options de stockage de métadonnées
<a name="AppMetadata"> </a>

Les métadonnées pour un Complément SharePoint, telles que les préférences des utilisateurs, les informations d'emplacement et d'autres paramètres, peuvent être stockées à plusieurs endroits. Une liste SharePoint masquée peut parfois être une bonne solution. Vous pouvez également utiliser le conteneur de propriétés du site web du complément. Une autre possibilité, pour un complément hébergé par un fournisseur, consiste à utiliser le stockage de table Microsoft Azure. 
  
    
    

### Options d'accès aux données sécurisées
<a name="DataAccess"> </a>

Vos options en matière d'accès aux données sécurisées dépendent bien entendu de vos possibilités de stockage. L'accès aux données et la façon de les consulter sont décrits en détails dans plusieurs autres articles. Pour plus d'informations, voir  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).
  
    
    

## Gérer des compléments
<a name="SPAppModelArch_Managing"> </a>

Les administrateurs de collection de sites et les administrateurs locataires peuvent surveiller les compléments et modifier les ressources qui leur sont attribuées. De plus, le personnel Microsoft du magasin de compléments peut signaler les compléments et les désactiver.
  
    
    
Pour plus d'informations sur la gestion des compléments, voir  [Installer et gérer des compléments pour](http://msdn.microsoft.com/fr-fr/library/fp161232.aspx) sur TechNet.
  
    
    

### Surveiller des compléments
<a name="SPAppModelArch_Monitoring"> </a>

SharePoint 2013 propose la surveillance de l'intégrité des compléments et publie ces informations sur l'interface utilisateur à destination des propriétaires de sites web, des administrateurs clients et des administrateurs de batterie. La plupart de la documentation du système de surveillance se trouve sur TechNet. Par exemple  [Surveiller les compléments pour SharePoint Server 2013](http://technet.microsoft.com/library/3adafdd2-f276-4a9d-8a74-e06b8916bbc2). Cette section ne représente qu'une brève introduction afin d'expliquer comment les compléments que vous vendez sont surveillés.
  
    
    
Certains types de données sont signalés par application et d'autres, par instance d'application. Les éléments principaux signalés par la structure de surveillance sont les suivants :
  
    
    

- Utilisation du complément, par exemple, le nombre d'installations (création d'une nouvelle instance). 
    
  
- Consommation des ressources du serveur de chaque instance de complément.
    
  
- Installation, mise à niveau et erreurs d'exécution de chaque instance de complément.
    
  
- Un indicateur d'intégrité global pour chaque instance de complément de couleur verte, jaune et rouge.
    
  
Si le complément comprend des composants Site web Azure, la structure de surveillance interroge également Microsoft Azure toutes les heures pour obtenir des données d'erreurs et signale les erreurs critiques et les données de quotas de stockage dans l'interface utilisateur SharePoint 2013. Les erreurs Base de données SQL Microsoft Azure ne sont pas signalées.
  
    
    
Les informations fournies par la structure de surveillance permettent aux administrateurs de déterminer si leur budget d'achat de compléments est dépensé de manière judicieuse, s'ils doivent déployer davantage de ressources sur les compléments et s'ils doivent désactiver un complément qui ne fonctionne pas correctement.
  
    
    

## Inscription de dépendances de complément
<a name="RegisterDependency"> </a>

Si votre Complément SharePoint dépend d'une fonctionnalité SharePoint qui n'est pas disponible et ne peut être mise à disposition sur le site web de complément, il ne fonctionnera pas bien et vos clients s'en plaindront. Vous pouvez veiller à ce que votre complément ne soit pas installé lorsque les services et fonctionnalités requis ne sont pas disponibles en inscrivant les dépendances du complément dans le manifeste du complément. L'infrastructure d'installation des Compléments SharePoint vérifiera cette condition préalable et bloquera l'installation de votre complément si aucun d'eux n'est disponible.
  
    
    
Pour des services (par exemple, Excel, Access ou Visio Services), l'infrastructure vérifiera que le service est installé et fonctionne sous licence.
  
    
    
Pour des fonctionnalités telles que la liste des tâches, l'infrastructure vérifiera que la fonctionnalité est déployée et est :
  
    
    
-- activée dans l'étendue **Farm**, **WebApplication** ou **Site** (collection de sites)
  
    
    
or
  
    
    
-- activable avec l'étendue **Web** dans le complément web qui est créé au moment de l'installation du complément.
  
    
    

> **REMARQUE**
> L'infrastructure d'installation du complément activera automatiquement ces fonctionnalités dans le site web de complément dès sa création. 
  
    
    

Les sections qui suivent précisent les détails nécessaires pour enregistrer la configuration requise.
  
    
    

### Enregistrement implicite des dépendances avec des demandes d'autorisation
<a name="PermAsPreq"> </a>

Lorsque votre complément a besoin d'accéder à des composants SharePoint en dehors du site web de complément, il doit demander l'autorisation d'accès à ces ressources dans la section **AppPermissionRequests** du manifeste du complément. Ces demandes d'autorisation font également office d'enregistrements préalables puisque SharePoint déduira à partir de ces autorisations que votre complément exige que certaines fonctionnalités SharePoint soient disponibles. Dans de nombreux cas, SharePoint peut déduire que toutes les fonctionnalités indispensables à votre complément, ainsi que les sections restantes de cette rubrique, ne sont pas nécessaires. Néanmoins, des enregistrements de dépendances redondants ne sont pas inutiles.
  
    
    

### Inscription explicite des dépendances avec des éléments AppPrerequisites
<a name="Explicit"> </a>

Lorsque votre complément affiche une dépendance qui n'est pas implicitement définie par ses demandes d'autorisation, vous devez enregistrer chaque dépendance avec un élément **AppPrerequisite** dans le manifeste du complément. Il y a trois attributs dans cet élément ; **Type**, **ID** et **MinimumVersion** (facultatif).
  
    
    
Il existe trois valeurs potentiellement requises pour l'attribut **Type**: `Feature`,  `Capablility` et `AutoProvisioning`. Une valeur « Feature » requise est simplement une fonctionnalité SharePoint que vous devez déployer et activer sur le site web de complément ou une étendue plus large qui inclut le site web de complément. Une valeur Capability (capacité) est un ensemble de fonctionnalités et de services qui doivent être disponibles sur le site web de complément. (La valeur  `AutoProvisioning` est abordée dans la section suivante.)
  
    
    
L'attribut facultatif **MinimumVersion** spécifie la version la plus faible de la fonctionnalité ou de la capacité qu'exige votre complément. Les valeurs des attributs adoptent la forme n.n.n.n (par exemple, `15.0.0.0`).
  
    
    
L'attribut **ID** précise quelle fonctionnalité ou capacité est requise. Si l'attribut **Type** a la valeur `Feature`, l'attribut **ID** correspond au GUID entre crochets et séparé par des tirets de la fonctionnalité (par exemple, `{151D22D9-95A8-4904-A0A3-22E4DB85D1E0}`). Si l'attribut **Type** a la valeur `Capability`, l'attribut **ID** désigne le GUID de la capacité. Les capacités sont répertoriées ci-dessous. Pour retrouver le GUID d'une capacité, voir [L'élément AppPrerequisite (AppPrerequisiteCollection complexType) (manifeste de l'application SharePoint)](http://msdn.microsoft.com/library/791be402-981f-519e-fcde-f24cc3cb4139%28Office.15%29.aspx).
  
    
    

- Access Services 2010
    
  
- Access Services
    
  
- Service web de métadonnées gérées
    
  
- Services PowerPoint
    
  
- Services Banque d'informations sécurisés
    
  
- Service de traduction automatique
    
  
- Service de profil utilisateur
    
  
- Service Graphiques Visio
    
  
- Service Gestion du travail
    
  
- Duet
    
  
- Flux de travail
    
  
- Recherche
    
  
- EDU
    
  
Voici un exemple de balisage brut **AppPrerequisites** qui enregistre la capacité Flux de travail. Si vous utilisez Visual Studio, vous devez modifier le manifeste du complément dans un outil de conception.
  
    
    


```

<AppPrerequisites>
  <AppPrerequisite Type="Capability" ID="{CDD8F991-B459-4512-8048-03D5A03FF27E}" MinimumVersion="15.0.0.0" />
</ AppPrerequisites>```


## Dans cette section
<a name="RegisterDependency"> </a>


-  [Choisir les modèles de développement et l'hébergement d'un complément pour SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)
    
  
-  [Héberger des sites web, des sites web de complément et des composants SharePoint dans SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

## Ressources supplémentaires
<a name="SPAppModelArch_AdditionalResources"> </a>


-  [Compléments](sharepoint-add-ins.md)
    
  
-  [Comparaison des compléments pour SharePoint et des solutions SharePoint](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)
    
  
-  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md)
    
  
-  [IFrame](http://www.w3schools.com/tags/tag_iframe.asp)
    
  
-  [Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Déploiement et installation de compléments pour SharePoint : méthodes et options](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Processus de mise à jour des compléments pour SharePoint](sharepoint-add-ins-update-process.md) .
    
  
-  [Locations et étendues de déploiement des compléments pour SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  

