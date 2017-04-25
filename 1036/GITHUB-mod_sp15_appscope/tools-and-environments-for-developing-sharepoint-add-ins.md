---
title: Outils et environnement de développement de compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# Outils et environnement de développement de compléments pour SharePoint
Découvrez les options qui s'offrent à vous pour créer un environnement de développement de Compléments SharePoint.
Il existe deux modèles de base pour les environnements de développement de Compléments SharePoint :





- **Le site web de test et de débogage SharePoint est un site web SharePoint Online dans un abonnement Office 365.** Généralement, Visual Studio est installé sur un ordinateur local, mais il est également possible d'établir un environnement Visual Studio en nuage.


- **Le site web SharePoint de test et de débogage se trouve sur une batterie SharePoint d'un seul serveur sur site.** Généralement, Visual Studio est installé sur le même ordinateur.



Tenez compte des éléments suivants :





- Presque tous les compléments que vous créez peuvent être déployés dans des batteries SharePoint sur site ou SharePoint Online, quel que soit le type utilisé par l'environnement. De manière générale, les applications qui ne peuvent pas être déployées sur SharePoint Online ne peuvent pas non plus être développées avec. Il s'agit par exemple des applications qui requièrent des  [autorisations Contrôle total](add-in-permissions-in-sharepoint-2013.md) et des applications qui utilisent le [système d'autorisation à haut niveau de fiabilité](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).


- Vous pouvez développer des Compléments SharePoint hébergés par SharePoint et hébergés par un fournisseur, quel que soit le type d'environnement que vous utilisez.


- Vous pouvez avoir à la fois des sites de test sur site et SharePoint Online.


- Tout compte fait, les deux options sont aussi faciles à configurer l'une que l'autre.


 **Pour créer l'environnementSharePoint Online** à l'aide d'un abonnement SharePoint Online que vous pouvez utiliser pour le développement, voir [Créer un Site du développeur dans un abonnement Office 365 existant](create-a-developer-site-on-an-existing-office-365-subscription.md). **Pour créer l'environnement local**, voir [Configurer un environnement de développement local pour les compléments pour SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
> **REMARQUE**
> Cette rubrique ne concerne que les environnements de développement de Compléments SharePoint. Si vous envisagez de développer des solutions de batterie de serveurs, voir  [Configurer un environnement de développement général pour SharePoint 2013](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx). Si vous envisagez d'utiliser les deux types de développement, commencez par l'article qui précède, puis consultez  [Configurer un environnement de développement local pour les compléments pour SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md) pour connaître les étapes supplémentaires nécessaires pour développer des Compléments SharePoint.





## Autres informations sur les outils


-  [Créer un complément de base hébergé par SharePoint à l'aide des outils de développement Office 365 « Napa »](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)


-  [Création de compléments pour SharePoint et d'éléments de projet à partir de modèles](create-sharepoint-add-ins-in-visual-studio.md)



## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Compléments](sharepoint-add-ins.md)



