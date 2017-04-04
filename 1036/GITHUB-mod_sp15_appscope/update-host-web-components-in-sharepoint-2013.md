---
title: Mettre à jour des composants web hôte dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# Mettre à jour des composants web hôte dans SharePoint 2013
Découvrez comment mettre à jour des composants de complément et des actions personnalisées sur le site web hôte d'un Complément SharePoint.
## Conditions requises pour la mise à jour des composants web hôtes
<a name="Prerequisites"> </a>

Être familiarisé avec la rubrique  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md), ainsi qu'avec les composants requis et concepts fondamentaux qui y sont listés.
  
    
    

## Mettre à jour les composants web hôtes
<a name="UpdateHostWeb"> </a>

Votre complément peut installer deux types de composants sur un site web hôte avec un balisage descriptif dans le Complément SharePoint : des actions personnalisées et descomposants de complément. La mise à jour de ces composants est plus facile sur le site web hôte que sur le site web de complément. Vous n'avez pas besoin de sémantique de mise à jour. Il suffit d'ajouter ou de modifier les actions personnalisées et les composants de complément. Lorsque le Complément SharePoint est mis à jour, SharePoint applique toujours les nouveaux fichiers manifestes d'élément et applique à nouveau les fichiers manifestes d'élément ayant été modifiés avec la version la plus récente. Les réapplications ne perturberont pas l'application. Par exemple, un bouton du ruban pour une action personnalisée ne sera pas ajouté plusieurs fois.
  
    
    
Lorsque vous mettez à jour un composant de complément, SharePoint remplace l'ancienne version par la nouvelle dans la galerie de composants WebPart. N'oubliez pas de changer la propriété **Name** de l'objet **ClientWebPart** lorsque vous mettez à jour un composant de complément. Ceci garantit que, lorsque le complément est mis à jour, SharePoint supprime l'ancienne version du composant de complément (qui ne fait plus partie de l'application) de toutes les pages auxquelles elle avait été ajoutée. Les utilisateurs devront ajouter de nouveau la nouvelle version aux pages.
  
    
    
Si vous laissez la propriété **Name** inchangée, l'ancienne version reste sur les pages où elle a été ajoutée, ce qui risque d'empêcher les utilisateurs de voir qu'une nouvelle version du composant de complément est disponible. De plus, lorsque le composant de complément est ajouté à d'autres pages, c'est sa nouvelle version qui est ajoutée ; la même version d'un Complément SharePoint risque donc de comporter des composants de complément différents selon les pages.
  
    
    
Vous pouvez déployer d'autres types de composants web hôtes par programmation, en inscrivant un récepteur d'événements distants dans le manifeste de complément avec un élément  [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx). Vous devez utiliser un récepteur  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) pour mettre à jour les composants initialement déployés avec un récepteur **InstalledEventEndpoint**. Les récepteurs  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) sont décrits dans [Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).
  
    
    

## Étapes suivantes
<a name="Next"> </a>

Accédez à  [Étapes principales de mise à jour d'un complément](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) ou consultez directement l'un des articles suivants pour découvrir comment mettre à jour le prochain composant majeur de votre Complément SharePoint.
  
    
    

-  [Mettre à jour des composants de compléments web dans SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [Mettre à jour des composants distants dans les compléments pour SharePoint](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Héberger des sites web, des sites web de complément et des composants SharePoint dans SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

