---
title: Développer des compléments pour SharePoint
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# Développer des compléments pour SharePoint
Accédez à des articles et des ressources détaillés vous aidant à intégrer des fonctionnalités avancées dans vos Compléments SharePoint.
> **REMARQUE**
> Cet article suppose que vous connaissez déjà le contenu de l'article  [Compléments](sharepoint-add-ins.md) et le matériel de mise en route associé.
  
    
    

Sous **Développer**, se trouvent les éléments suivants qui permettent d'expliquer les différentes actions possibles dans un Complément SharePoint :
- Présentations approfondies
    
  
- Articles de procédure
    
  
- Extraits de code
    
  
Vous trouverez des articles concernant les aspects suivants : 
- Exécution d'opérations de création, lecture, mise à jour et suppression (CRUD) sur des listes
    
  
- Création de requêtes REST et interaction avec les nouvelles API
    
  
- Configuration OAuth pour la sécurité
    
  
SharePoint propose des fonctionnalités sociales d'entreprise telles que les flux d'activités et les profils utilisateur, de pair avec des fonctionnalités de gestion de contenu d'entreprise, des fonctionnalités d'interopérabilité métier et des fonctionnalités de conception de site web pouvant véritablement faire sortir du lot vos compléments. Pour plus d'informations, voir  [Ajout de fonctionnalités SharePoint 2013](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx).Par ailleurs, le code étant essentiel, examinez donc le menu « Exemples » dans le Centre de développement. Il s'agit d'un lien direct vers nos exemples de code pour les compléments. Dès que vous aurez configuré votre environnement de développement, jetez un œil à quelques-uns de nos exemples. Tirez profit d'une fonctionnalité de communauté qui vous permet de demander un exemple de code si nous n'en avons aucun à votre goût. Nous recueillons ces demandes, ainsi que les autres commentaires relatifs aux documents, et les exploitons dans le cadre des mises à jour régulières apportées au contenu et aux exemples. Par conséquent, n'hésitez pas à nous dire ce que vous voulez voir !
## Prise en main des ressources Compléments SharePoint
<a name="bk_gettingstarted"> </a>

Si vous êtes novice en matière de développement de Compléments SharePoint, jetez d'abord un coup d'œil à  [Compléments](sharepoint-add-ins.md). Cette page vous oriente vers des articles clés vous permettant de vous familiariser rapidement avec les différents types de Compléments SharePoint. Avant de mettre en œuvre des types plus avancés de développements avec les Compléments SharePoint, veillez à débuter avec une idée précise des types de compléments que vous voulez créer, des technologies que vous voulez inclure et des options d'hébergement que vous voudrez utiliser.
  
    
    

### Tâches et ressources essentielles pour le développement de Compléments SharePoint au moyen du modèle objet client, du modèle objet JavaScript et des points de terminaison REST dans SharePoint 2013
<a name="bk_essentials"> </a>

Quel que soit le type de Complément SharePoint que vous décidez de créer, votre complément interagira toujours d'une certaine façon avec un site SharePoint 2013. Les articles dans le tableau 1 décrivent comment effectuer plusieurs des types de tâche les plus importants dans des sites SharePoint en utilisant trois interfaces à votre disposition dans vos Compléments SharePoint : le modèle objet client, le modèle objet JavaScript et les points de terminaison REST.
  
    
    

**Tableau 1. Opérations de base avec le modèle objet client SharePoint 2013, le modèle objet JavaScript et l'interface REST**


|**Rubrique**|**Description**|
|:-----|:-----|
| [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |Explique comment effectuer des opérations courantes au moyen de C# et du modèle objet client.  <br/> |
| [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |Explique comment effectuer des opérations courantes au moyen du modèle objet JavaScript.  <br/> |
| [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |Explique comment effectuer des opérations courantes au moyen de l'interface REST.  <br/> |
   

## Découverte des concepts fondamentaux de développement avec les Compléments SharePoint
<a name="bk_fundamentals"> </a>

Outre les opérations de base, vous devez comprendre les concepts fondamentaux du modèle de développement de complément SharePoint 2013. Chaque type de Complément SharePoint contient un fichier de manifeste de complément et est intégré dans un package de complément que vous déployez sur un site SharePoint 2013. Et lorsque vous développez un type de complément, quel qu'il soit, vous devez prendre en considération une série de questions liés à l'authentification et à l'autorisation, à l'accès aux données et à l'utilisation. Les articles dans le tableau 2 vous présentent ces questions et expliquent leurs implications pour tout type de que vous voulez créer.
  
    
    

**Tableau 2. Concepts fondamentaux liés à l'utilisation des Compléments SharePoint**


|**Rubrique**|**Description**|
|:-----|:-----|
| [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |Vous guide tout au long des concepts clés liés à l'acquisition des droits nécessaires pour utiliser des ressources SharePoint 2013.  <br/> |
| [Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |Explique le fonctionnement des manifestes de complément et la création des packages de compléments.  <br/> |
| [Créer des composants d'expérience utilisateur dans SharePoint 2013](create-ux-components-in-sharepoint-2013.md) <br/> |Explore les façons dont vous pouvez créer une expérience utilisateur plus riche dans les Compléments SharePoint.  <br/> |
| [Utiliser des données externes dans SharePoint 2013](work-with-external-data-in-sharepoint-2013.md) <br/> |Explique les options et techniques d'accès aux données qui sont disponibles dans différents types de Compléments SharePoint.  <br/> |
| [Gérer les licences de compléments pour Office et SharePoint](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |Vous guide tout au long du cadre de licence de complément pour les Compléments Office et SharePoint.  <br/> |
   

## Assemblage du puzzle : création de compléments SharePoint avancés en intégrant des fonctionnalités
<a name="bk_integrate"> </a>

Lorsque vous avez fait connaissance avec les fonctionnalités des Compléments SharePoint, vous pouvez commencer à créer des compléments plus complexes en assemblant les pièces de la façon qui vous convient. Les articles dans le tableau 3 expliquent comment intégrer des fonctionnalités et créer des Compléments SharePoint plus complets.
  
    
    

**Tableau 3. Concepts avancés dans les Compléments SharePoint**


|**Rubrique**|**Description**|
|:-----|:-----|
| [Créer un complément hébergé par un fournisseur comportant un type personnalisé de liste et de contenu SharePoint](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |Explique comment créer des Compléments SharePoint qui sont hébergés dans le nuage et qui incluent des listes et des types de contenu SharePoint personnalisés.  <br/> |
   

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Outils et environnement de développement de compléments pour SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
-  [Concevoir des compléments pour SharePoint](design-sharepoint-add-ins.md)
    
  
-  [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Pack d'exemples de compléments SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  

