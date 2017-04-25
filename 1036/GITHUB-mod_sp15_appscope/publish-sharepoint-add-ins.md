---
title: Publier des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# Publier des compléments pour SharePoint
Recherchez des articles et des ressources sur la publication des Compléments SharePoint.
## Publier les compléments
<a name="bk_gettingstarted"> </a>

Vous avez terminé de développer vos Complément SharePoint ; il ne vous reste plus qu'à les rendre accessibles aux utilisateurs. Pour ce faire, vous pouvez les publier sur l'un des deux emplacements suivants :




- **Office Store public.** Publiez votre complément sur l'Office Store pour le rendre disponible publiquement afin qu'il puisse être acheté par les utilisateurs d'un déploiement SharePoint.


- **Catalogue de compléments d'organisation interne.** Publiez vos compléments dans un catalogue de compléments d'organisation interne, hébergé sur votre déploiement SharePoint, pour les rendre accessibles aux utilisateurs disposant d'un accès à ce déploiement SharePoint.


Pour plus d'informations sur la création d'un package de complément en vue d'une publication à l'aide de Visual Studio 2012, voir  [Publier des compléments pour SharePoint à l'aide de Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md).




### Publication sur Office Store

Pour publier un complément sur l'Office Store, vous devez d'abord vous  [inscrire en tant que développeur Microsoft](https://sellerdashboard.microsoft.com/Registration). 



Lorsque vous téléchargez un complément sur l'Office Store en vue de sa publication, Microsoft effectue une série de vérifications pour contrôler qu'il respecte les instructions de comportement et de contenu. Par exemple, Microsoft vérifie que le code du manifeste du complément est valide et complet, et s'assure que les packages de solution SharePoint (fichiers .wsp) inclus dans le complément ne contiennent pas d'éléments non autorisés ou de fonctionnalités SharePoint dont l'étendue s'étend au-delà du web. Le package est également inspecté pour déterminer s'il contient du contenu répréhensible. Si le package du complément réussit tous les tests, il est encapsulé dans un fichier et signé par Microsoft.



Lorsque vous téléchargez un complément en vue de sa publication sur l'Office Store, vous pouvez sélectionner les termes de la licence que vous voulez accorder aux utilisateurs qui la téléchargent. Cette licence de complément indique :




- si le complément est proposé gratuitement, sous forme de version d'évaluation ou s'il doit être acheté ;


- si le complément peut être acquis par utilisateur ou par site.


SharePoint n'applique pas les termes de licence à l'utilisation du complément. Il propose une structure de licence qui permet d'inclure une logique de code dans votre complément pour appliquer les restrictions de licence de votre choix. Par exemple, vous pouvez inclure une logique de code dans votre complément qui permet aux utilisateurs d'accéder à certaines fonctionnalités s'ils disposent d'une licence payante (fonctionnalités auxquelles ils n'ont pas accès avec une licence d'évaluation). Pour plus d'informations, voir  [Gérer les licences de compléments pour Office et SharePoint](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx).




### Publication dans un catalogue de compléments

Si vous créez des Compléments SharePoint pour votre propre société ou pour un client spécifique (et non pour un public général), vous souhaiterez les publier dans un catalogue de compléments interne hébergé sur SharePoint. Un catalogue de compléments privé correspond à une collection de sites dédiée dans une application web SharePoint 2013 (ou une location SharePoint Online) qui héberge des bibliothèques de documents pour les Compléments SharePoint et les Compléments Office. Placer le catalogue dans sa propre collection de sites permet à l'administrateur des applications web ou à l'administrateur de limiter facilement les autorisations sur le catalogue.



Télécharger un Complément SharePoint dans un catalogue de compléments d'entreprise est aussi simple que de télécharger n'importe quel fichier dans une bibliothèque de documents SharePoint. Vous devez remplir un formulaire contextuel dans lequel vous indiquez l'URL locale du package du complément et d'autres informations comme le nom du complément. Lorsque vous téléchargez le complément dans un catalogue de compléments, il est également vérifié. S'il ne réussit pas les tests, il est marqué comme non valide ou désactivé dans le catalogue.




## Choisir l'emplacement de publication du Complément SharePoint
<a name="bk_decide"> </a>

Le tableau ci-après compare une publication sur l'Office Store à une publication dans un catalogue de compléments. Il répertorie également les points à prendre en compte lors du choix de l'emplacement de publication du complément. Il est recommandé de choisir l'emplacement de publication d'un complément avant de le concevoir et de le développer. Dans certains cas (les licences, par exemple), l'emplacement de publication a un impact sur la conception et le développement d'un complément.




**Tableau 1. Points à prendre en compte lors du choix de l'emplacement de publication d'un complément**


|**Office Store**|**Catalogue de compléments**|
|:-----|:-----|
|Le complément est disponible publiquement.  <br/> |Le complément est accessible aux utilisateurs ayant un accès à ce déploiement SharePoint.  <br/> |
|Structure de licence disponible.  <br/> |Structure de licence non disponible à des fins d'utilisation.  <br/> |
|Le package du complément est vérifié par Microsoft pour contrôler qu'il est conforme aux stratégies techniques et de contenu.  <br/> |Une vérification du package du complément est effectuée par SharePoint lorsque le complément est téléchargé.  <br/> |
|Vous devez être inscrit à Mon tableau de bord vendeur Microsoft pour télécharger des compléments.  <br/> |Aucune inscription auprès de Microsoft n'est requise.  <br/> |
 

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Création ou mise à jour d'ID et de clés secrètes client dans le service Mon tableau de bord vendeur](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)


-  [Soumission des compléments SharePoint et Office, ainsi que des applications Office 365 à l'Office Store dans le tableau de bord vendeur](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)


-  [Stratégies de validation pour les applications et les compléments envoyés à l'Office Store (version 2.0)](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)


-  [Commencer à créer des applications pour Office et SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)


-  [Gérer les licences de compléments pour Office et SharePoint](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)


-  [Déploiement et installation de compléments pour SharePoint : méthodes et options](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [Locations et étendues de déploiement des compléments pour SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)


-  [Publier des compléments pour SharePoint à l'aide de Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md)


-  [Publication de compléments pour l'Office Store](http://social.msdn.microsoft.com/Forums/fr-fr/officestore)



