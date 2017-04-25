---
title: Mettre à jour des composants distants dans les compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# Mettre à jour des composants distants dans les compléments pour SharePoint
Découvrez comment mettre à jour les bases de données et applications web distantes dans un Complément SharePoint.
## Conditions requises pour mettre à jour les composants distants d'un complément SharePoint
<a name="Prerequistes"> </a>

Être familiarisé avec la rubrique  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md), ainsi qu'avec les composants requis et concepts fondamentaux qui y sont listés.




## Mettre à jour les composants distants
<a name="UpdateRemote"> </a>

Seuls des conseils très généraux peuvent être fournis pour la mise à jour des composants distants en raison des grandes différences entre les plateformes et les systèmes clients. Les deux sections suivantes fournissent quelques directives.




### Mettre à jour les composants distants dans un complément hébergé par un fournisseur
<a name="UpdateProviderHosted"> </a>

Pour un Complément SharePoint hébergé par un fournisseur, vous mettez à jour les composants distants en utilisant les meilleures pratiques de mise à jour de la plateforme sur laquelle les composants sont hébergés. De la même manière que les composants distants d'un complément hébergé par un fournisseur sont installés séparément du Complément SharePoint lui-même, ils sont également mis à jour séparément. Voici quelques éléments à prendre en compte :




- Les composants distants mis à jour devraient continuer à fonctionner avec toutes les versions antérieures du Complément SharePoint.


- Si vous avez mis en place un système d'isolation de client pour vos composants distants, gardez à l'esprit que plusieurs clients pourraient utiliser différentes versions de votre complément et qu'un client spécifique pourrait même disposer de différentes versions installées sur plusieurs sites web SharePoint.


Pour un Complément SharePoint hébergé par un fournisseur qui utilise Base de données SQL Microsoft Azure ou SQL Server, il existe plusieurs méthodes pour mettre à jour la base de données. Pour commencer, voir  [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx)..




## Étapes suivantes
<a name="Next"> </a>

Retournez à  [Étapes principales de mise à jour d'un complément](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) ou consultez directement l'un des articles suivants pour découvrir comment mettre à jour le prochain composant majeur de votre Complément SharePoint.




-  [Mettre à jour des composants de compléments web dans SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)


-  [Mettre à jour des composants web hôte dans SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)


-  [Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)



## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md)


-  [Processus de mise à jour des compléments pour SharePoint](sharepoint-add-ins-update-process.md)



