---
title: Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint
En savoir plus sur la structure du package du complément et sur le fichier manifeste pour un Complément SharePoint.
## Structure du package du complément pour SharePoint
<a name="Package"> </a>

Un package de Complément SharePoint est un fichier doté d'une extension « .app » et qui est conforme aux conventions  [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/fr-fr/magazine/cc163372.aspx). Le package contient les éléments suivants :
  
    
    

- **Fichier manifeste du complément :** il s'agit d'un fichier requis nommé appmanifest.xml. Il indique à SharePoint 2013 certaines propriétés importantes du complément, telles que son titre et les autorisations qu'elle doit exécuter. Pour plus d'informations sur le contenu de ce fichier, voir [Fichier manifeste Complément pour SharePoint](#AppManifest).
    
  
- **Packages de solutions SharePoint :** en option, le complément peut inclure un package de solutions SharePoint (fichier .wsp) contenant les composants du site web de complément. Ces composants peuvent inclure des pages, des instances de liste, des vues, des documents, des fonctionnalités d'étendue **Web**, ainsi que d'autres composants SharePoint 2013. (Pour plus d'informations sur les types de composants SharePoint pouvant être inclus dans un Complément SharePoint, voir  [Types de composants SharePoint pouvant se trouver dans un complément SharePoint](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).) Le fichier .wsp peut également contenir des Compléments Office. Les composants d'un fichier .wsp sont déployés sur le site web de complément. Pour obtenir un exemple d'un package de complément incluant un package de solution SharePoint, voir  [Créer un complément hébergé par un fournisseur comportant un type personnalisé de liste et de contenu SharePoint](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md).
    
  
- **Fonctionnalités du site web hôte avec les actions personnalisées ou les composants de complément :** Outre les composants SharePoint 2013 déployés sur le site web de complément, un Complément SharePoint peut également déployer une ou plusieurs actions personnalisées (éléments de menu contextuel ou extensions du ruban) sur le site web hôte. Pour ce faire, il faut inclure dans le package du complément une fonctionnalité non comprise dans le fichier .wsp du package et qui déploie les composants qui iront sur le site web hôte. Cette fonctionnalité « souple » est appelée fonctionnalité du site web hôte. Les composants de complément sont déployés sur le site web hôte de la même manière. La fonctionnalité du site web hôte consiste en un fichier feature.xml SharePoint 2013 standard et d'un ou de plusieurs fichiers associés elements.xml. Par exemple, un fichier elements.xml pour une action personnalisée contient un balisage **CustomAction** pour l'action personnalisée. Il peut également inclure le balisage pour les composants de complément. Seuls ces deux types de composants peuvent figurer dans la fonctionnalité du site web hôte. Ces fonctionnalités du site web hôte ne sont pas détaillées dans le manifeste du complément. Cependant, elles représentent des « composants » dans le sens OPC et il existe une relation OPC explicite entre le manifeste du complément et chacun de ces fichiers. Pour obtenir un exemple de package de complément contenant une fonctionnalité du site web hôte, voir [Créer des actions personnalisées à déployer avec les compléments pour SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
    
    > **REMARQUE**
      > Les administrateurs locataires ont la possibilité d'effectuer une installation par lot d'un Complément SharePoint sur plusieurs sites web. Un complément installé de cette manière est dit avoir une étendue **Tenant**. Si le complément n'a pas été installé en lots, mais séparément sur chaque site, il a une étendue **Web**. Si la fonctionnalité du site web hôte inclut les extensions de ruban ou les composants du complément, elles ne sont pas déployées sur les sites web hôtes si le complément est installé par lot, donc seuls les éléments de menu contextuels sont déployés avec les compléments à étendue locataire. L'étendue du complément ne doit pas être confondue avec l'étendue de la fonctionnalité. L'étendue de la fonctionnalité détermine où les éléments sont déployés dans une fonctionnalité. Les possibilités sont **Farm**, **WebApplication**, **Site** (soit collection de sites) et **Web**. Seule l'option **Web** est permise pour les fonctionnalités dans les Compléments SharePoint (les fonctionnalités du site web hôte et les fonctionnalités comprises dans le fichier .wsp dans un package de complément). L'étendue du complément se rapporte à l'étendue de l'installation d'un complément. Les possibilités sont **Web**, auquel cas le complément a été installé sur un ou plusieurs sites web site par site et **Tenant**, auquel cas le complément a été installé par lot sur tout ou partie des sous-ensembles de sites web du locataire d'un client. Pour plus d'informations sur les étendues **Tenant** et **Web**, voir  [Locations et étendues de déploiement des compléments pour SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md). 
- **Fichiers de ressources de localisation (.resx) :** ils servent à localiser les aspects d'un manifeste de complément, y compris le titre de l'application et les aspects des fonctionnalités du site web hôte dans le package du complément. (Les composants individuels du package du complément se trouvant dans leur propre package, comme les fichiers .wsp, les packages Sites web Azure et les manifestes de complément, ont chacun leurs propres processus de localisation qui sont appliqués exactement de la même manière que si les éléments en question ne faisaient pas partie d'un Complément SharePoint.) Pour obtenir un exemple de package de complément comprenant des fichiers .resx pour une fonctionnalité du site web hôte, voir [Localiser les compléments pour SharePoint](localize-sharepoint-add-ins.md).
    
  
- Manifestes de **Compléments Office :** facultativement, il peut exister un ou plusieurs manifestes de Compléments Office comprenant chacun un Complément Office. Ce composant peut être inclus dans le package du complément uniquement si le complément est téléchargé vers un catalogue de compléments d'entreprise SharePoint 2013 et pas sur le marché. Voir [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md) pour plus d'informations.
    
  

## Fichier manifeste Complément pour SharePoint
<a name="AppManifest"> </a>

Chaque Complément SharePoint comprend un fichier appmanifest.xml. Ce fichier donne à SharePoint les informations nécessaires sur le complément et en définit les propriétés les plus importantes. Voici plusieurs des éléments indiqués dans le manifeste :
  
    
    

- Le nom interne, l'ID de produit et la version du complément.
    
  
- L'URL de la page d'accueil, c'est-à-dire de la page qui s'affiche au lancement du complément. Cela peut être une page du site web de complément, une page basée sur le cloud ou une page située sur un serveur web ISV.
    
    > **REMARQUE**
      > Dans certains cas, il peut y avoir des restrictions sur le type de fichier pouvant être spécifié dans l'élément **StartPage**. Pour plus d'informations, voir  [L'élément StartPage (PropertiesDefinition, complexType) (manifeste SharePoint Add-in)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx). > Quand vous associez plusieurs paramètres de requête dans la valeur **StartPage**, vous devez utiliser l'esperluette encodée «  `&amp;amp;` » plutôt que « `&amp;` » ou un point-virgule pour les ajouter ensemble.
- D'autres propriétés du complément. Il s'agit du titre et des paramètres régionaux pris en charge par le complément (les deux sont obligatoires), des URL des services traitant les événements avant l'installation, après la mise à niveau et avant la désinstallation, ainsi que du modèle web à utiliser quand le site web de complément est créé.
    
  
- Les demandes d'autorisation dont le complément a besoin pour les ressources SharePoint hors du site web de complément.
    
  
- Une identification, à des fins d'authentification et d'autorisation, du principal de complément. Il s'agit du principal qui reçoit les autorisations. Cela n'est pas obligatoire pour les compléments hébergés sur SharePoint.
    
  
- Une liste des conditions préalables, le cas échéant, devant être disponibles pour le complément afin d'installer celui-ci. Par exemple, il peut être nécessaire d'installer et d'activer certaines fonctionnalités, et de concéder sous licence et d'installer certains services.
    
  

> **REMARQUE**
> Le fichier manifeste du complément est le seul élément obligatoire dans le package du complément, mais tous les éléments de la liste précédente ne sont pas des composants obligatoires du fichier. 
  
    
    

Pour obtenir des informations détaillées sur le balisage du manifeste du complément, voir le nœud  [Référence du schéma de manifestes d'applications pour SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx). La présente rubrique n'est pas destinée à remplacer les informations contenues dans ce nœud, notamment les informations relatives aux éléments et attributs requis. De plus, notez que les manifestes de compléments SharePoint ont un schéma différent des manifestes de Complément Office. Des informations relatives à ces dernières sont disponibles dans la rubrique  [Informations de référence sur le schéma des manifestes des applications pour Office (version 1.1)](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx).
  
    
    
Ce qui suit est un exemple de fichier appmanifest.xml. Veuillez noter que, dans cet exemple, la page de démarrage du complément est une page ASP.NET se trouvant sur un serveur distant, et non une page sur le site SharePoint. L'URL de la page inclut une chaîne de requête passant l'URL ou le site web hôte à l'application web distante. Le composant « {HostUrl} » de la chaîne est un jeton qui est résolu au lancement du complément. Le complément demande l'autorisation d'accès en écriture à toutes les listes du site web hôte. Le principal de complément devant obtenir cette autorisation est l'application web distante.
  
    
    
Vous devez utiliser l'élément **SupportedLocales** ou **SupportedLanguages** dans le manifeste de votre complément. **SupportedLanguages** est déconseillé au profit de **SupportedLocales**. L'élément **SupportedLanguages** continuera de fonctionner même après la publication, mais il est préférable d'éviter de l'utiliser. Pour plus d'informations sur ces éléments, voir [Élément SupportedLocales (PropertiesDefinition, complexType) (manifeste SharePoint Add-in)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx) et [Élément SupportedLanguages (PropertiesDefinition, complexType) (manifeste SharePoint Add-in)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx)
  
    
    

> **REMARQUE**
> Les valeurs de l'attribut de l' **Scope** de l'élément **AppPermissionRequest** sont structurées comme des URL, mais sont en réalité des chaînes littérales. Aucun composant de la valeur **Scope** exemple de l'exemple suivant n'est un espace réservé. Pour plus d'informations sur les autorisations, voir [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md). 
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### Jetons d'URL dans le manifeste du complément

SharePoint 2013 fournit plusieurs jetons pouvant être utilisés dans l'élément **StartPage** et d'autres emplacements dans les compléments et les composants des compléments pour représenter des informations inconnues jusqu'à ce que le complément soit exécuté. L'infrastructure SharePoint 2013 résout ces jetons. Certains sont utilisés au début de l'URL et d'autres peuvent être utilisés au sein d'une URL, par exemple la valeur d'un paramètre de requête. Ces jetons ainsi que plusieurs autres peuvent également être utilisés dans des contextes de développement SharePoint 2013 variés. Pour des informations détaillées sur tous les jetons et où ils peuvent être utilisés, voir [Chaînes URL et jetons dans les compléments pour SharePoint](url-strings-and-tokens-in-sharepoint-add-ins.md). Pour des informations générales sur les autres jetons et les URL dans SharePoint 2013, voir  [URL et jetons dans SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx).
  
    
    

> **REMARQUE**
> Ces jetons ne sont pas utilisés dans l'attribut **Scope** d'un élément **AppPermissionRequest**. 
  
    
    


## Ressources supplémentaires
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Référence du schéma de manifestes d'applications pour SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/fr-fr/magazine/cc163372.aspx)
    
  
-  [Connexion application couche Données (DAC)](http://msdn.microsoft.com/fr-fr/library/ee210546)
    
  
-  [Web Deploy 2.0](http://www.iis.net/downloads/microsoft/web-deploy)
    
  

