---
title: Créer des composants d'expérience utilisateur dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: bfdd0a58-2cc5-4805-ac89-4bd2fe6f3b09
---


# Créer des composants d'expérience utilisateur dans SharePoint 2013
Découvrez comment créer des composants UX pour vos compléments dans SharePoint 2013.
## Création de composants UX dans des Compléments SharePoint
<a name="SP15CreateUX_Creating"> </a>

Le modèle de complément SharePoint propose de nombreux composants et mécanismes UX vous aidant à créer une formidable expérience utilisateur dans des Compléments SharePoint. L'expérience utilisateur dans le modèle de complément est également suffisamment flexible pour vous permettre d'utiliser les techniques et les plateformes les mieux adaptées aux besoins des utilisateurs finaux. Le Tableau 1 répertorie les ressources vous permettant d'apprendre à créer et à utiliser les composants UX dans les compléments.
  
    
    

**Tableau 1. Ressources et instructions pour créer des composants UX dans des Compléments SharePoint**


|**Article**|**Description**|
|:-----|:-----|
| [Utilisation d'une feuille de style de site web SharePoint dans les compléments pour SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) <br/> |Vous pouvez référencer la feuille de style d'un site web SharePoint dans votre Complément SharePoint et l'utiliser pour donner un style à vos pages web en utilisant la feuille de style dans SharePoint 2013. En outre, si quelqu'un modifie la feuille de style ou le thème du site web SharePoint, vous pouvez adopter le nouvel ensemble de styles dans votre complément sans modifier la référence de la feuille de style dans celui-ci.  <br/> |
| [Utiliser le contrôle de chrome client dans les compléments pour SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md) <br/> |Le contrôle Chrome dans SharePoint 2013 vous permet d'utiliser les styles d'en-tête d'un site SharePoint spécifique dans votre complément sans avoir à inscrire dans le Registre une bibliothèque de serveur ou utiliser une technologie ou un outil spécifique. Pour utiliser cette fonctionnalité, vous devez inscrire dans le Registre une bibliothèque JavaScript SharePoint par le biais d'une balise <script> standard. Vous pouvez fournir un espace réservé au moyen d'un élément HTML **div** et personnaliser davantage le contrôle au moyen des options disponibles. Le contrôle hérite de son aspect à partir du site web SharePoint spécifié. <br/> |
| [Créer des composants de complément à installer avec votre complément pour SharePoint](create-add-in-parts-to-install-with-your-sharepoint-add-in.md) <br/> |Les composants de complément vous permettent de montrer votre expérience d'utilisateur de complément dans le site web hôte. Un composant de complément affiche le contenu de votre complément à l'aide d'un **IFrame**. Les utilisateurs finaux peuvent personnaliser l'expérience en utilisant les propriétés personnalisées que vous pouvez fournir pour votre composant de complément. La page web du complément reçoit les valeurs de propriété personnalisée par l'intermédiaire de paramètres dans la chaîne de requête.  <br/> |
| [Créer des actions personnalisées à déployer avec les compléments pour SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md) <br/> |Lorsque vous créez un Complément SharePoint, les actions personnalisées vous permettent d'interagir avec les listes et le ruban sur le site web hôte. Une action personnalisée peut être déployée sur le site web hôte quand les utilisateurs finaux installent votre complément. Les actions personnalisées peuvent ouvrir une page web distante et transférer des informations par l'intermédiaire de la chaîne de requête. Deux types d'actions personnalisées sont disponibles pour les compléments : Ruban et Bloc de contrôle d'édition.  <br/> |
| [Personnaliser le mode Liste dans les compléments pour SharePoint à l'aide du rendu côté client](customize-a-list-view-in-sharepoint-add-ins-using-client-side-rendering.md) <br/> |Le rendu côté client propose un mécanisme qui vous permet de produire votre propre destination pour un jeu de contrôles hébergés sur une page SharePoint. Vous pouvez également utiliser des technologies connues, telles que le HTML et JavaScript, afin de définir la logique de rendu des affichages de liste SharePoint. Avec le rendu côté client, vous pouvez spécifier vos propres ressources JavaScript et les héberger dans les options de stockage de données disponibles pour vos compléments, par exemple dans une bibliothèque de documents.  <br/> |
| [Utiliser le contrôle Sélecteur de personnes côté client dans des compléments hébergés par SharePoint](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md) <br/> |Découvrez comment utiliser le contrôle Sélecteur de personnes côté client dans les Compléments SharePoint. Ce contrôle permet aux utilisateurs de rapidement rechercher et sélectionner des comptes d'utilisateurs valides afin de trouver des personnes, des groupes et des revendications dans leur organisation. Le sélecteur est un contrôle HTML et JavaScript est pris en charge par plusieurs navigateurs.  <br/> |
   

## Étapes suivantes : utilisation de données dans des Compléments SharePoint
<a name="SP15CreateUX_Next"> </a>

Avez-vous fini de créer une UX performante pour votre complément ? Incorporez les données avec les mécanismes disponibles dans le modèle de complément SharePoint. Pour plus d'informations, voir  [Utiliser des données externes dans SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).
  
    
    

## Ressources supplémentaires
<a name="SP15CreateUX_AddRes"> </a>


-  [Compléments](sharepoint-add-ins.md)
    
  
-  [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md)
    
  
-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  

