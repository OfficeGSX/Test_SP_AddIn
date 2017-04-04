---
title: Concevoir des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# Concevoir des compléments pour SharePoint
Ayez une vue d'ensemble des options de conception et d'architecture disponibles dans les Compléments SharePoint. Découvrez également comment prendre des décisions avisées pour simplifier le développement de votre complément dans SharePoint 2013.
Supposons que vous ayez une idée sensationnelle pour un complément. Dans cette section, nous vous aiderons à prendre les bonnes décisions au niveau de la conception et nous vous présenterons les pratiques recommandées pour créer votre complément. Par exemple, qu'est-ce qui caractérise une bonne interface utilisateur ? Quelles sont les « formes » de complément disponibles ? Quand dois-je utiliser l'une plutôt que l'autre ? Quels sont mes choix pour l'accès aux données ? 
  
    
    


## Commencer à concevoir des Compléments SharePoint
<a name="SP15Design_Startdesigning"> </a>

Comme le modèle de complément Cloud de SharePoint 2013 offre un grand nombre d'options de conception, les Compléments SharePoint peuvent se présenter sous différentes formes et tailles. Cette section contient des conseils utiles pour les décisions importantes que vous devez prendre lors de la planification et de la conception de l'architecture et de l'expérience utilisateur de votre complément (hébergement de votre complément, accès aux données efficace et en toute sécurité du complément et expérience utilisateur du complément, par exemple).
  
    
    
Pour obtenir une vue d'ensemble des options de conception et d'architecture disponibles pour les compléments SharePoint, voir  [Penser de trois manières différentes les options de conception des compléments pour SharePoint](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md). Pour obtenir une présentation des Compléments SharePoint, voir  [Compléments](sharepoint-add-ins.md).
  
    
    

## Choisir le modèle d'hébergement adéquat pour le complément
<a name="SP15Design_Hostingmodel"> </a>

Les Compléments SharePoint prennent en charge plusieurs options d'hébergement. Vous pouvez choisir votre propre pile web, demander à Microsoft de configurer Microsoft Azure et SQL Azure ou héberger le complément sur SharePoint. Le tableau 1 contient des ressources qui peuvent vous aider à choisir le modèle d'hébergement adéquat pour votre complément.
  
    
    

**Tableau 1. Ressources et conseils pour choisir le modèle d'hébergement adéquat pour les Compléments SharePoint**


|**Article**|**Description**|
|:-----|:-----|
| [Choisir les modèles de développement et l'hébergement d'un complément pour SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Découvrez les différentes méthodes d'hébergement pour les composants des Compléments SharePoint.  <br/> |
   

## Choisir les technologies d'accès aux données adéquates pour le complément
<a name="SP15Design_Dataaccess"> </a>

Vous devez faire en sorte que le complément accède aux données en toute sécurité et de manière efficace. Plusieurs technologies différentes d'accès aux données sont disponibles pour accéder à SharePoint et utiliser des données dans votre complément. Le tableau 2 répertorie des ressources qui vous permettent de découvrir les différentes options et de choisir celle qui convient à votre complément. 
  
    
    

**Tableau 2. Ressources et conseils pour choisir les technologies d'accès aux données adéquates pour les Compléments SharePoint**


|**Article**|**Description**|
|:-----|:-----|
| [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> | Découvrez les options d'accès aux données qui vous sont proposées lorsque vous créez des Compléments SharePoint, notamment les options de connexion de données pour les scénarios de données entrantes et sortantes et les API disponibles lorsque vous souhaitez accéder aux données SharePoint à partir de votre complément. <br/> |
   

## Concevoir l'expérience utilisateur de votre complément
<a name="SP15Design_UX"> </a>

Lorsque vous concevez votre complément, votre véritable objectif est de créer un environnement permettant aux utilisateurs de suivre les scénarios que vous avez prévu qu'ils suivent. Dans le tableau 3, découvrez les ressources et les conseils de conception dont vous avez besoin pour créer des compléments qui suivent les meilleures pratiques en matière de conception d'expérience utilisateur et qui ont l'apparence et le comportement familiers de SharePoint 2013.
  
    
    

**Tableau 3. Ressources et conseils pour concevoir l'expérience utilisateur des Compléments SharePoint**


|**Article**|**Description**|
|:-----|:-----|
| [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md) <br/> |Découvrez les options d'expérience utilisateur qui vous sont proposées lors de la création de Compléments SharePoint.  <br/> |
   

## Concevoir une application en pensant aux mises à jour
<a name="Upgrade"> </a>

Un jour, vous aurez peut-être besoin de mettre à jour votre complément et de le télécharger dans le Office Store ou dans le catalogue de compléments d'une organisation. Vous pouvez vous faciliter la tâche en pensant à la méthode à utiliser pour les mises à jour dès la conception de la première version de votre complément. Nous vous recommandons de lire les articles suivants lors de la phase de conception :  [Processus de mise à jour des compléments pour SharePoint](sharepoint-add-ins-update-process.md) et [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md). 
  
    
    

## Étapes suivantes : développer et publier votre complément
<a name="SP15Design_Next"> </a>

Vous disposez d'une conception fiable pour votre complément ? Vous êtes donc prêt à le générer et le publier. Les ressources contenues dans le tableau 4 peuvent vous aider à commencer.
  
    
    

**Tableau 4. Ressources et conseils pour développer et publier des Compléments SharePoint**


|**Article**|**Description**|
|:-----|:-----|
| [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md) <br/> |Présente les concepts avancés et les possibilités du modèle de complément.  <br/> |
| [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md) <br/> |Décrit le processus et les conditions requises pour publier des Compléments SharePoint.  <br/> |
   

## Ressources supplémentaires
<a name="SP15Design_AddRes"> </a>


-  [Pack d'exemples de compléments SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
-  [Réinventer le développement SharePoint](http://msdn.microsoft.com/fr-fr/office/apps/dn133840)
    
  
-  [Compléments](sharepoint-add-ins.md)
    
  
-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Blog pour les compléments](http://blogs.msdn.com/b/spoffapps)
    
  

