

# Options de stockage des données dans les compléments pour SharePoint
Découvrez les diverses façons dont une Complément SharePoint peut stocker des données.
 * **S'applique à :*** 





|||
|:-----|:-----|
|**Dans cet article**          [Options de stockage de données structurées](#StructuredData)           [Options de stockage de données non structurées](#UnStructuredData)           [Paramètres de complément et autres options de stockage de métadonnées](#AppMetadata)           [Options d'accès aux données](#DataAccess)           [Ressources supplémentaires](#AddtionalResources)||
 

## Options de stockage de données structurées
<a name="StructuredData"> </a>

Une Complément SharePoint peut utiliser pratiquement tout type de stockage de données structurées, à la fois à l'intérieur et à l'extérieur de SharePoint 2013 et sur des plateformes Microsoft et non Microsoft. Voici quelques emplacement dans lesquels vous pouvez stocker des données structurées pour une Complément SharePoint :




- Listes SharePoint sur un site web de complément


- SQL Azure


- Sources de données externes connectées à SharePoint avec Microsoft Business Connectivity Services (BCS)


- Service de nuage non Microsoft


- Une base de données sur votre propre serveur



> **CONSEIL**
> Vous devrez probablement mettre à niveau votre Complément SharePoint à un moment donné. Lorsqu'une Complément SharePoint inclut des composants SharePoint sur un site web de complément, le processus de mise à niveau procède à une copie complète du site web de complément. Pour cette raison, la présence de longues listes SharePoint sur le site web de complément allonge le processus de mise à niveau et entraîne une utilisation intensive du processeur sur le serveur de base de données de contenu. Vous devez éviter de placer des « données volumineuses » dans les listes SharePoint sur le site web de complément. 





## Options de stockage de données non structurées
<a name="UnStructuredData"> </a>

Les documents, images, vidéos, fichiers audio et autres types de données non structurées qui sont produits ou utilisés par une Complément SharePoint peuvent être stockés à l'intérieur ou à l'extérieur de SharePoint. Les bibliothèques de documents sont un bon choix pour des documents et sont consultables par le biais d'une recherche SharePoint. Une bibliothèque d'éléments de site est souvent un choix approprié pour les fichiers multimédias. 



Les autres options incluent le stockage de données blob sur votre compte Microsoft Azure ou sur vos propres serveurs. Vous pouvez également stocker des fichiers sur certaines plateformes non Microsoft ou sur certains services basés sur le nuage.




## Paramètres de complément et autres options de stockage de métadonnées
<a name="AppMetadata"> </a>

Les métadonnées pour une Complément SharePoint, telles que les préférences des utilisateurs, les informations d'emplacement et d'autres paramètres, peuvent être stockées à plusieurs endroits. Une liste SharePoint masquée peut parfois être une bonne solution. Vous pouvez également utiliser le conteneur de propriétés du site web de complément. Une autre possibilité, pour un complément hébergé par un fournisseur, consiste à utiliser le stockage de table Microsoft Azure. 




## Options d'accès aux données
<a name="DataAccess"> </a>

Vos options pour l'accès aux données dépendent bien entendu de vos possibilités de stockage. L'accès aux données et la façon de les consulter sont décrits en détails dans plusieurs autres articles. Pour plus d'informations, voir  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).




## Ressources supplémentaires
<a name="AddtionalResources"> </a>


-  [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)





    