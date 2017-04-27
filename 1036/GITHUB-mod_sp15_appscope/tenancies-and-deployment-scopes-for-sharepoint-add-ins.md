---
title: Locations et étendues de déploiement des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 1ceb3142-a7a5-453e-920f-5f953a79401a
---


# Locations et étendues de déploiement des compléments pour SharePoint
 Découvrez le concept des locations et les différences entre le déploiement de Compléments SharePoint au niveau de l'étendue de la location et de l'étendue du site web.
## Locations et étendue de complément
<a name="AppScope"> </a>

Une location SharePoint 2013 est un ensemble de collections de sites dans une batterie de serveurs SharePoint ou dans SharePoint Online. Dans SharePoint Online, les collections de sites appartiennent à un seul compte client. Dans une batterie de serveurs SharePoint, les collections de sites peuvent correspondre à toutes les collections de sites dans une application web SharePoint ou un sous-ensemble de celles-ci, ou il peut s'agir d'un ensemble de collections de sites provenant de plusieurs applications web dans la batterie de serveurs. Une location peut avoir un catalogue de compléments de Complément SharePoint de la même façon qu'une application web SharePoint.



Un Complément SharePoint présente une étendue de complément. Les deux étendues de complément possibles sont l'étendue web ou l'étendue de location. La différence n'est pas une propriété intrinsèque du complément et ce n'est pas vous, le développeur, qui décidez de l'étendue de votre complément. La décision est prise par les administrateurs clients et découle de la façon dont le complément est installé. Une fois qu'un complément est téléchargé dans le catalogue de compléments d'une location, il est immédiatement disponible en vue d'une installation sur les sites web de la location, en procédant site par site. Les compléments qui sont installés de cette façon présentent une étendue web. Toutefois, les administrateurs clients disposent d'une autre option. Ils peuvent choisir d'installer le complément par lots sur un sous-ensemble de sites web au sein de la location. Les compléments qui sont installés de cette façon ont une étendue de location. L'administrateur client peut spécifier les sites web sur lesquels le complément est installé au moyen d'une liste de chemins d'accès gérés, d'une liste de modèles de site ou d'une liste de collections de sites. Un complément installé par lots ne peut être désinstallé que par un administrateur client. Quand ce dernier désinstalle le complément, il est désinstallé de tous les sites web de la location. Les utilisateurs ne peuvent pas désinstaller un complément installé par lots en procédant site par site. Ce principe s'applique également à la mise à jour des compléments installés par lots : elle ne peut être assurée que par un administrateur client et la mise à jour s'applique simultanément à tous les sites web de la location où le complément est installé.



Si un complément qui inclut un site web de complément est installé par lots, un seul site web de complément est créé et il est partagé par tous les sites web hôte sur lesquels le complément est installé. Le site web de complément se trouve dans la collection de sites du catalogue de complément de l'organisation.



Lorsque de nouvelles collections de sites sont créées dans la location, les compléments précédemment installés par lots sont automatiquement installés sur la nouvelle collection de sites.




> **REMARQUE**
> Il ne faut pas confondre l'étendue de complément et l'étendue de fonctionnalité. L'étendue de fonctionnalité détermine l'endroit où les éléments d'une fonctionnalité sont déployés. Les possibilités incluent **Farm**, **WebApplication**, **Site** (c'est-à-dire, la collection de sites) et **Web**. Seul **Web** est autorisé pour les fonctionnalités dans les Compléments SharePoint (à la fois les fonctionnalités de site web hôte et les fonctionnalités au sein d'un .wsp dans un package de complément).<BR /> L'étendue de complément ne doit pas non plus être confondue avec les niveaux d'autorisation de complément. Les Compléments SharePoint peuvent demander des autorisations pour l'ensemble ou des parties sélectionnées du contenu SharePoint aux niveaux de la liste, du site web, de la collection de sites et de la location. L'installation d'un complément avec une étendue de location ne lui confère pas des autorisations dont il ne disposerait pas en temps normal, et elle n'annule pas non plus les dispositions clés du modèle de sécurité SharePoint. Pour plus d'informations sur les autorisations de complément, voir  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md). 





## Limitations des compléments d'étendue de location
<a name="Tenant"> </a>

Les types suivants de complément ne peuvent pas être installés par lots :




- Les compléments qui contiennent une action personnalisée pour le ruban. (Les actions personnalisées qui sont déployées en tant qu'éléments de menu sont autorisées.)


- Les compléments qui contiennent un composant de complément. 


En outre, notez de nouveau que l'installation avec étendue de location n'est pas possible dans la version Office 365 Petite Entreprise Premium de SharePoint Online.




## Installer, désinstaller et mettre à jour un complément sur plusieurs sites web dans une location
<a name="Web"> </a>

Qu'un complément soit installé à partir de l'Office Store ou à partir d'un catalogue de compléments, les administrateurs clients peuvent l'installer sur plusieurs sites web dans une location, le désinstaller et le mettre à jour au moyen des procédures suivantes.




### Pour installer un complément SharePoint sur plusieurs sites web


1. Accédez à la page **Contenu du site** du site web du catalogue d'entreprise.


2. Sélectionnez **Ajouter un complément** et installez le complément de la même façon que pour n'importe quel autre site web SharePoint.


3. Une fois le complément installé, positionnez le pointeur sur le lien du complément dans la page **Contenu du site**. Un lien « **...** » apparaît.


4. Sélectionnez le lien. Une légende apparaît.


5. Sélectionnez **Déploiement** dans le menu.


6. Utilisez l'interface utilisateur de déploiement qui s'ouvre pour spécifier les collections de sites dans lesquelles vous voulez installer le complément. Vous pouvez appliquer un filtre sur la base des chemins d'accès gérés, des modèles de site ou des URL. Les filtres fonctionnent avec une relation logique de type « OR » : le complément est installé sur l'ensemble des collections de sites qui correspondent à un ou plusieurs critères de filtrage.


7. Sélectionnez **OK**.



### Pour désinstaller un complément SharePoint installé par lots


1. Accédez à la page **Contenu du site** du site web du catalogue d'entreprise.


2. Positionnez le pointeur sur le lien du complément dans la page **Contenu du site**. Un lien « **...** » apparaît.


3. Sélectionnez le lien. Une légende apparaît.


4. Sélectionnez **Supprimer** dans la légende.



### Pour mettre à jour un complément SharePoint installé par lots


1. Accédez à la page **Contenu du site** du site web du catalogue d'entreprise. Si une mise à jour est disponible pour un complément, un message apparaît à côté de ce dernier. Un lien permettant d'effectuer la mise à jour est également disponible.


2. Cliquez sur le lien pour mettre à jour le complément.


3. Lorsque vous êtes invité à approuver les demandes d'autorisation du complément, sélectionnez **Approuver**.



## Ressources supplémentaires
<a name="SP15tenancies_addlresources"> </a>


-  [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md)


-  [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [Déploiement et installation de compléments pour SharePoint : méthodes et options](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [Processus de mise à jour des compléments pour SharePoint](sharepoint-add-ins-update-process.md)



