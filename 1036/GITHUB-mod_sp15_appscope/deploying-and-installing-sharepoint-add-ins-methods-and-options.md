---
title: Déploiement et installation de compléments pour SharePoint  méthodes et options
ms.prod: SHAREPOINT
ms.assetid: d15a74a7-3c10-485a-9885-7ef11aaa0d90
---


# Déploiement et installation de compléments pour SharePoint : méthodes et options
Découvrez les méthodes de publication, d'installation et de désinstallation d'un Complément SharePoint.
## Publication sur le Office Store ou sur le catalogue de compléments d'une organisation
<a name="MarketOrCatalog"> </a>

Vous pouvez télécharger votre Complément SharePoint sur le Office Store public ou sur le catalogue de compléments d'une organisation. Un catalogue de compléments privé est une collection de sites dédiée dans une application web SharePoint 2013 (ou une location SharePoint Online) qui héberge des bibliothèques de documents pour les Compléments SharePoint et les Compléments Office. Pour les administrateurs d'applications web ou les administrateurs locataires, il sera plus simple de limiter les autorisations d'accès au catalogue en plaçant celui-ci sur leur propre collection de données. 



Si le complément est téléchargé sur le Office Store, Microsoft exécute des contrôles de validation sur celui-ci. Par exemple, il vérifie si le balisage du manifeste du complément est valide et complet, et il vérifie qu'aucun des packages de solution SharePoint (fichiers .wsp) inclus ne comporte d'éléments non autorisés ni de fonctionnalités d'étendue plus large que **Web**. Le package est également inspecté pour vérifier qu'il ne comporte pas de contenu répréhensible. Si le complément satisfait à tous ces tests, le package de compléments est encapsulé dans un fichier et signé par Microsoft. 




> **REMARQUE**
> Lorsque vous développez votre complément et le déployez avec Outils de développement Microsoft Office pour Visual Studio, le complément est installé directement dans le site SharePoint de test cible. Comme il ne passe pas par Office Store, le contrôle de validation décrit ci-dessus ne se produit pas. 




Il est aussi simple de télécharger un Complément SharePoint sur un catalogue de compléments d'une organisation que de télécharger un fichier sur une bibliothèque de documents SharePoint Foundation. Remplissez un formulaire contextuel dans lequel vous fournirez l'URL locale du package de compléments et d'autres informations, telles que le nom du complément. Lorsque le complément est téléchargé sur le catalogue de compléments d'une organisation, des vérifications semblables se déroulent et les compléments qui ne satisfont pas à ces vérifications sont marqués comme « non valide » ou « désactivé » dans le catalogue. 



Les administrateurs locataires et les administrateurs d'application web SharePoint 2013 peuvent acquérir des Compléments SharePoint sur le Office Store. Pour ouvrir le Office Store, l'administrateur sélectionne **Ajouter un complément** dans la page **Contenu du site**, puis sélectionne **Banque SharePoint** dans la page **Vos compléments**. Ceci ouvre une page **Magasin SharePoint** que les administrateurs peuvent utiliser pour découvrir les Compléments SharePoint que proposent les fournisseurs et en savoir plus à leur sujet. (Ils peuvent également le faire sur office.com.) Les compléments nécessitant un composant préalable qui n'est pas installé sur la location ou l'application web de l'administrateur apparaissent grisés et sont indisponibles dans le magasin de compléments. Par exemple, si un complément qui nécessite des services Search et que ce dernier n'est pas installé, le complément apparaît grisé. Les administrateurs peuvent trier, filtrer et parcourir la liste de compléments, lire des informations et consulter les commentaires sur ceux-ci, et acheter des licences pour un complément.



Lorsqu'un administrateur décide d'acheter une licence, il doit accepter les termes et conditions d'achat ainsi que les autorisations dont doit disposer le complément pour pouvoir s'exécuter (accès en lecture aux listes ou accès en contrôle total à la collection de sites). 



Si une ou plusieurs licences de complément sont achetées, elles sont téléchargées sur la location ou l'application web. Le complément n'est pas automatiquement téléchargé et installé lorsque la licence est achetée, même si les administrateurs peuvent combiner l'installation avec un achat de licences.



Les utilisateurs installent les compléments à partir de la page **Vos compléments**. Cette page dispose des listes issues de la fusion des éléments suivants :




- Compléments SharePoint du catalogue de compléments de l'organisation (ou du locataire) de l'application web.


- Compléments SharePoint du Office Store pour lequel l'organisation ou le locataire possède déjà une licence de site ou une licence qui a été attribuée à l'utilisateur.


Tous les compléments immédiatement installables par l'utilisateur sont répertoriés et seuls ces compléments le sont, à quelques exceptions près. Les utilisateurs peuvent filtrer les compléments contenus dans la page afin d'inclure uniquement les compléments du catalogue de compléments de l'organisation. Lorsqu'un complément est installé, il apparaît dans la liste des compléments dans la page **Contenu du site** du site web sur lequel il est installé.




## Installation de compléments SharePoint
<a name="Installing"> </a>

Les propriétaires de sites web peuvent installer des Compléments SharePoint à partir de la page **Vos compléments**, tel que décrit précédemment dans cette rubrique. L'installation crée une instance du complément. Pour plus d'informations sur l'installation de compléments, voir  [Ajouter des compléments SharePoint à un site SharePoint 2013](https://technet.microsoft.com/fr-fr/library/fp161231.aspx). 




> **REMARQUE**
> La perte temporaire de la connexion réseau peut parfois bloquer l'installation. Si celle-ci échoue pour une raison ou une autre, son infrastructure effectuera trois tentatives. Si ces dernières échouent, un indicateur d'échec apparaît sur l'interface utilisateur. Les utilisateurs pourront effectuer ultérieurement une nouvelle tentative d'installation. 





## Désinstallation de compléments SharePoint
<a name="Uninstalling"> </a>

Les propriétaires de sites web peuvent désinstaller une instance d'un Complément SharePoint par le biais de l'interface utilisateur SharePoint. La désinstallation des instances de Compléments SharePoint est complète, à savoir que tous les composants installés par le complément sont désinstallés. 



En revanche, les composants utilisés par un complément mais installés séparément ne sont pas supprimés. Supposons par exemple qu'un complément possède une page web distante dotée de boutons qui créent des listes sur le site web hôte. La désinstallation supprime la vignette du complément de la page **Contenu du site**, ce qui rend la page distante effectivement inaccessible ou inutilisable par les utilisateurs, mais ne supprime pas les listes qui ont été créées avec le complément. SharePoint ne mémorise pas quelles listes du site web hôte ont été créées par le complément et quelles lignes ont été créées par les utilisateurs dans l'interface de SharePoint, et ne peut donc pas supprimer les listes qui ont été créées avec le complément. Ce comportement est généralement souhaitable, car les listes peuvent contenir des données toujours utiles pour l'utilisateur, même après la suppression du complément qui les a créées.



Si le Complément SharePoint comporte un site web de complément, celui-ci est supprimé. Cette désinstallation est plus complète que la désactivation systématique des fonctionnalités et l'inversion du déploiement du fichier .wsp interne du complément.




> **REMARQUE**
> Quand un utilisateur supprime un complément, celui-ci est déplacé vers la Corbeille premier niveau. Sa suppression de cet emplacement le fait passer à la Corbeille second niveau. Sa suppression de la Corbeille second niveau le désinstalle complètement et il ne peut pas être restauré. 




Les autorisations du complément sont également révoquées lorsqu'il est supprimé (recyclé), conformément à ces règles :




- Les autorisations relatives au web sont toujours révoquées.


- S'il n'existe aucune autre instance du complément dans la collection de sites, les autorisations relatives aux collections de sites sont également révoquées.


- S'il n'existe aucune autre instance du complément dans l'abonnement au site (location), les autorisations de client sont également révoquées.


Si vous êtes inscrit dans le manifeste du complément, le service web  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) s'exécute au début du processus de désinstallation (qui survient lorsque le complément est supprimé de la Corbeille second niveau). Il est recommandé de vous doter d'un service web [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) si vous disposez déjà d'un service web [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) et de concevoir le service [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) en tant qu'outil qui inverse toutes les actions effectuées par le service [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx). Pour plus d'informations, voir  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md).




## Ressources supplémentaires
<a name="SP15deployinstallapps_addlresources"> </a>


-  [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md)


-  [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [Processus de mise à jour des compléments pour SharePoint](sharepoint-add-ins-update-process.md)



