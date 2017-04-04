---
title: Créer des compléments pour SharePoint dont peuvent se servir des utilisateurs anonymes
ms.prod: SHAREPOINT
ms.assetid: e858fce6-7a8f-4b1e-b053-64dc75345801
---


# Créer des compléments pour SharePoint dont peuvent se servir des utilisateurs anonymes
Découvrez comment créer des Compléments SharePoint pouvant être utilisés par des utilisateurs anonymes sur des sites web Microsoft SharePoint 2013 publics.
> **IMPORTANTE**
> Dans cette article, lorsque nous faisons référence à SharePoint 2013 *sur site*  , nous partons du principe que le Service Pack 1 pour SharePoint 2013 a été installé.
  
    
    


## Conditions préalables à la création de Compléments SharePoint accessibles anonymement

L'accès anonyme est possible pour les Compléments SharePoint hébergés par SharePoint et par un fournisseur. En fonction du type d'application que vous créez, consultez l'un des ensembles de conditions préalables suivants :
  
    
    

-  [](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#SP15SPhostedapps_bk_prereqs)
    
  
-  [](get-started-creating-provider-hosted-sharepoint-add-ins.md#SP15createselfhostapp_bk_prereq)
    
  
Dans l'installation de test SharePoint 2013 vous aurez également besoin d'une collection de sites configurée pour l'accès anonyme. Si vous disposez d'un Site du développeur Office 365, celui-ci est déjà associé à une collection de sites publique qui utilise une définition spéciale de site web public. (Pour plus d'informations sur l'utilisation des sites web publics dans Microsoft SharePoint Online, voir  [Aide sur le site web public pour Office 365](http://office.microsoft.com/fr-fr/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1).) Cette définition de site n'est pas disponible pour les installations SharePoint 2013 sur site. Par conséquent, si vous disposez d'une installation de test sur site, vous avez besoin des éléments suivants :
  
    
    

- Une application web SharePoint configurée pour autoriser l'accès anonyme. Toutefois, cette configuration doit être effectuée une fois que vous avez installé le Complément SharePoint à tester.
    
  
- Une collection de sites dans l'application web, elle-même configurée pour l'accès anonyme. Toutefois, cette configuration doit être effectuée une fois que vous avez installé le Complément SharePoint à tester.
    
  
- Une liste (dans la collection de sites) configurée pour l'accès anonyme, si votre complément est hébergé par SharePoint et qu'il accède à une liste SharePoint.
    
  
Vous trouverez des instructions relatives à ces tâches dans la section  [Configuration d'une application hébergée par SharePoint, d'une collection de sites et d'une liste pour l'accès anonyme](#Configuring).
  
    
    

## Limitations sur l'utilisation des compléments SharePoint par des utilisateurs anonymes

Tenez compte des points suivants lorsque vous concevez votre Complément SharePoint :
  
    
    

- Si le Complément SharePoint est lancé à partir de SharePoint, il doit inclure une action personnalisée ou un composant de complément, ou il doit être lancé à partir d'un lien personnalisé sur une page. Cela est dû au fait que les utilisateurs anonymes ne peuvent pas lancer le complément à partir de sa vignette. Le mécanisme permettant de lancer les compléments à partir de leur vignette requiert l'utilisation d'une  [page d'application](http://msdn.microsoft.com/library/685c8e01-b163-4b5e-888c-421d9ecbb25e%28Office.15%29.aspx) qui n'est pas accessible aux utilisateurs anonymes. Vous pouvez également configurer le lancement du complément en dehors de SharePoint, auquel cas il devra utiliser la stratégie d'autorisation decomplément uniquement.
    
  
- Les utilisateurs anonymes peuvent uniquement lancer des Compléments SharePoint qui ont été installés par d'autres utilisateurs. Cela est dû au fait qu'ils ne peuvent ouvrir la page **Ajouter un complément**.
    
  
- Lorsqu'un utilisateur connecté accède à un site web dans une application web SharePoint sur site configurée pour l'accès anonyme, son identité devient **Compte système**. Cette identité ne peut pas installer de Compléments SharePoint. Comme les utilisateurs anonymes ne peuvent pas installer de compléments, personne ne peut installer de Compléments SharePoint lorsque l'application web est configurée pour l'accès anonyme. Par conséquent, les Compléments SharePoint doivent être installés sur des sites web de l'application web SharePoint avant que l'application web ne soit configurée pour l'accès anonyme. S'il faut installer d'autres compléments ultérieurement, l'application web doit être modifiée temporairement pour désactiver l'accès anonyme, permettant ainsi à un utilisateur connecté d'installer les compléments.
    
  
- Pour activer l'API REST de recherche de SharePoint sur un site web SharePoint sur site, vous devez configurer un code XML de requête. Pour plus de détails, voir  [Activation des requêtes de recherche REST anonymes](http://msdn.microsoft.com/library/office/jj163876.aspx#bk_AnonymousREST).
    
  
- Les données sur le complément web ne sont pas analysées par les indexeurs de recherche. De ce fait, des données personnalisées doivent être déployées à distance ou dans le web hôte. Cela s'applique à tous les Compléments SharePoint, mais nous abordons ce sujet dans cet article car les compléments destinés à l'accès anonyme requièrent souvent la fonctionnalité de recherche.
    
  

## Création de compléments hébergés par un fournisseur accessibles anonymement
<a name="Cloud-hosted"> </a>

Pour rendre un complément hébergé par un fournisseur accessible aux utilisateurs anonymes, il suffit de le configurer afin qu'il utilise la stratégie d'autorisation de complément uniquement. Avec cette stratégie, SharePoint n'inclut même pas de contexte utilisateur. De ce fait, peu importe que l'utilisateur soit anonyme. Il est simplement indispensable que le principal du complément dispose de toutes les autorisations nécessaires pour le web hôte (et la collection de sites parent ou la location parent). Celles-ci lui sont accordées par l'administrateur de clients qui installe le complément. La demande d'autorisations pour le web hôte s'effectue dans le manifeste de complément, de la même façon que pour tous les compléments.
  
    
    

> **REMARQUE**
> Si le site SharePoint est accessible aux utilisateurs anonymes, il autorise généralement l'accès HTTP, plutôt que HTTPS. Dans ce scénario, il est possible de rencontrer des problèmes de sécurité lorsque la stratégie de complément uniquement est utilisée pour les compléments. Pour obtenir plus de détails et connaître la méthode permettant d'atténuer ces problèmes, voir l'article relatif à  [ce que tout développeur doit savoir à propos des compléments SharePoint, du modèle CSOM (Client-side Server Object Model) et des sites de publication anonymes](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx). 
  
    
    

La conception d'un complément en vue d'utiliser la stratégie de complément uniquement implique les opérations suivantes :
  
    
    

- Vous devez ajouter  `AllowAppOnlyPolicy="true"` à l'élément **AppPermissionRequests** dans le manifeste de complément.
    
  
- Votre code doit solliciter un jeton d'accès de complément uniquement auprès du serveur d'autorisation OAuth. Si vous utilisez du code managé, il existe des API spécialement conçues dans ce but dans les fichiers de code générés par Outils de développement Microsoft Office pour Visual Studio SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb).
    
  
Pour plus de détails sur la stratégie de complément uniquement (avec des extraits de code), les autorisations de compléments et le manifeste du complément, consultez les rubriques suivantes :
  
    
    

-  [Types de stratégie d'autorisation des compléments dans SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)
    
  
-  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

## Création de compléments hébergés par SharePoint accessibles anonymement
<a name="SP-hosted"> </a>

Aucune technique particulière n'est requise pour créer un complément hébergé par SharePoint pouvant être exécuté par des utilisateurs anonymes. Vous procédez de la même façon que pour tout complément hébergé par SharePoint. Pour plus de détails, voir  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md) et [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md).
  
    
    
Pour qu'un complément hébergé par SharePoint soit utilisable avec un accès anonyme, il est primordial que la collection de sites SharePoint Online soit configurée par un administrateur de clients de sorte à autoriser l'accès anonyme. En tant que développeur d'un complément hébergé par SharePoint, vous ne pouvez pas effectuer cette configuration à partir du complément, d'un récepteur d'événements de complément distant ou à l'aide de l'infrastructure d'installation du complément SharePoint. Vous pouvez inclure une liste ou une bibliothèque personnalisée dans le complément, mais un CAML (Collaborative Application Markup Language) ne permet pas de préconfigurer l'accès anonyme. Par conséquent, ce dernier doit être configuré une fois le complément installé.
  
    
    
Si vous commercialisez votre complément sur le Office Store, et que la majorité de vos clients potentiels considèrent le complément plus utile s'il est accessible aux utilisateurs anonymes, vous devriez envisager de mentionner cette configuration requise dans la description de votre complément. En outre, vous pouvez inclure une version de la procédure permettant de **configurer une collection de sites SharePoint Online pour l'accès anonyme** (ci-dessous) sur la page de support de votre complément.
  
    
    
Si votre complément utilise le modèle d'objet JavaScript (JSOM) de SharePoint, il existe une configuration requise. Seule une infime partie des API JSOM est accessible par défaut aux utilisateurs anonymes. Toutes les autres requièrent l'autorisation **Utiliser les interfaces distantes**, dont ne disposent pas les utilisateurs anonymes. Cette configuration requise doit être désactivée dans la collection de sites parent ou l'application web SharePoint parent du site web où le complément est installé, comme décrit à la section [Configuration d'une application hébergée par SharePoint, d'une collection de sites et d'une liste pour l'accès anonyme](#Configuring).
  
    
    

> **REMARQUE**
> La désactivation de cette configuration requise, selon laquelle les utilisateurs doivent disposer de l'autorisation **Utiliser les interfaces distantes**, a une incidence sur la confidentialité des informations. Pour plus de détails, voir [What Every Developer Needs to Know About SharePoint Add-ins, CSOM, and Anonymous Publishing Sites](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx). 
  
    
    


## Limitations des compléments hébergés par SharePoint pour les utilisateurs anonymes
<a name="SP-hosted"> </a>

À dire vrai, il existe des limitations non négligeables à l'utilisation des compléments hébergés par SharePoint pour les utilisateurs anonymes, et que vous devez connaître :
  
    
    

- Il est impossible de rendre une liste ou une bibliothèque accessible aux utilisateurs anonymes sur SharePoint Online. Par conséquent, les API JSOM qui impliquent une interaction avec des listes ou des bibliothèques ne fonctionnent pas sur SharePoint Online pour les utilisateurs anonymes.
    
  
- Un complément hébergé par SharePoint installé sur un site web SharePoint  *local*  peut utiliser la totalité des API JSOM. Toutefois, l'application web, la collection de sites et les listes/bibliothèques doivent être configurées *manuellement*  pour l'accès anonyme, et la configuration requise relative à l'autorisation **Utiliser les interfaces distances** doit être désactivée. Si le complément installe des listes/bibliothèques personnalisées sur le complément web, les utilisateurs doivent y configurer manuellement l'accès anonyme une fois le complément installé.
    
  
- Puisque les données du web de complément ne sont pas analysées par les indexeurs de recherche, qu'il est impossible d'installer des listes ou des bibliothèques personnalisées sur le web hôte et que les compléments hébergés par SharePoint ne peuvent avoir de récepteurs d'événements ni de composants distants, les données personnalisées d'un complément hébergé par SharePoint ne peuvent pas faire l'objet d'une recherche.
    
  

## Configuration d'une application hébergée par SharePoint, d'une collection de sites et d'une liste pour l'accès anonyme
<a name="Configuring"> </a>

Si l'installation de test SharePoint est sur site, vous devez effectuer les deux premières procédures ci-dessous pour vérifier si les utilisateurs anonymes peuvent se servir de votre Complément SharePoint. Les clients qui installent votre Complément SharePoint doivent également le faire pour la collection de sites parent et l'application web de tout site web où votre Complément SharePoint est installé.
  
    
    

> **IMPORTANTE**
> Essayez d'installer le Complément SharePoint sur un site web  *avant*  d'effectuer les deux premières procédures. Lorsque l'application web a été configurée pour l'accès anonyme, il est alors impossible d'installer des compléments sur les sites web dans une application web SharePoint sur ​site. Si l'application web a déjà été configurée pour l'accès anonyme, vous devez annuler ce paramètre temporairement pour installer le complément.
  
    
    


### Pour configurer l'application web parent en vue de l'accès anonyme


1. Dans **Administration centrale**, sélectionnez **Gestion des applications**, puis **Gérer les applications web**.
    
  
2. (Facultatif) Si vous ne souhaitez autoriser l'accès anonyme à aucune des applications web SharePoint existantes, créez autre une application web. (Bien que le formulaire de création d'applications web vous permette d'autoriser l'accès anonyme,  *n'utilisez pas cette option*  . L'accès anonyme ne doit être activé qu' *après*  l'installation du Complément SharePoint.) Pour plus de détails, voir [Create a web application in SharePoint 2013](http://msdn.microsoft.com/library/121c8d83-a508-4437-978b-303096aa59df.aspx).
    
  
3. Dans la liste des applications web, choisissez celle que vous souhaitez rendre accessible aux utilisateurs anonymes, puis sélectionnez **Fournisseurs d'authentification** sur le ruban.
    
  
4. Dans la légende qui apparaît, choisissez la zone pour laquelle l'accès anonyme doit être activé. Généralement, il s'agit d' **Internet** ou de **Par défaut**. Le formulaire **Modifier l'authentification** s'affiche.
    
  
5. Cochez la case à cocher **Activer l'accès anonyme**, si cela n'est pas déjà fait. Cela définit une valeur par défaut qui peut être désactivée pour les collections de sites spécifiques. Toutefois, si vous ne l'activez pas pour l'application web, vous ne pouvez l'activer pour aucune des collections de sites.
    
  
6. (Facultatif) Désactivez la case à cocher **Exiger l'autorisation Utiliser les interfaces distantes**. Cela permet au code et au script exécutés dans le contexte d'un utilisateur anonyme d'effectuer des appels au modèle d'objet client de SharePoint sur ​​chaque collection de sites. Vous ne pouvez réactiver la configuration requise pour aucune collection de sites. Si la case reste cochée, cela signifie que, par défaut, les utilisateurs anonymes ne peuvent pas accéder aux modèles d'objet client. Toutefois, vous pouvez désactiver la configuration requise (et leur autoriser l'accès) pour des collections de sites spécifiques.
    
    > **REMARQUE**
      > Dans le cadre du développement des Compléments SharePoint pour les utilisateurs anonymes, ce paramètre n'est significatif que pour les compléments hébergés par SharePoint. Les Compléments SharePoint hébergés par le fournisseur qui sont conçus pour les utilisateurs anonymes ont recours à une technique qui rend les autorisations de l'utilisateur non pertinentes. Pour plus d'informations à ce sujet, consultez la section  [Création de compléments hébergés par un fournisseur accessibles anonymement](#Cloud-hosted) ci-dessus.
7. Sélectionnez **Enregistrer** pour fermer le formulaire.
    
  
8. Veillez à ce que l'application web soit toujours mise en surbrillance, puis sélectionnez **Stratégie anonyme** sur le ruban.
    
  
9. Dans le formulaire de **Restrictions d'accès anonyme**, sélectionnez la zone et assurez-vous que la case d'option **Aucune** est activée. Si le Complément SharePoint que vous testez ne requiert que des droits de lecture pour les données SharePoint, activez l'option **Refuser l'écriture** à la place.
    
    > **REMARQUE**
      > Il s'agit d'un autre paramètre qui, dans le cadre du développement de Compléments SharePoint pour les utilisateurs anonymes, n'a d'importance que pour les compléments hébergés par SharePoint. 
10. Si vous avez créé une application web à l'étape 2, vous devez créer une collection de sites dans cette application.
    
  

### Pour configurer une collection de sites sur site en vue de l'accès anonyme


1. Sur la page d'accueil d'une collection de sites de l'application web, cliquez sur l'icône d'engrenage, puis sélectionnez **Paramètres du site**.
    
  
2. Dans la section **Utilisateurs et autorisations** de la page **Paramètres du site**, sélectionnez **Autorisations de site**.
    
  
3. Choisissez **Accès anonyme** sur le ruban.
    
  
4. Dans le formulaire **Accès anonyme**, choisissez **Tout le site web** ou **Listes et bibliothèques**, selon le niveau d'accès requis par le complément que vous testez. (Indépendamment de l'option choisie, les utilisateurs anonymes ne peuvent pas accéder à une liste ou à une bibliothèque avec un complément hébergé par SharePoint, à moins que la liste ou la bibliothèque ne soit configurée individuellement pour tous les accès anonymes). Vous trouverez ci-dessous la procédure à suivre.
    
  
5. Si le complément que vous testez requiert un accès au modèle d'objet client SharePoint, assurez-vous que la case **Exiger l'autorisation Utiliser les interfaces distantes** n'est *pas*  cochée. Si la case à cocher est grisée, la configuration requise a été désactivée au niveau de l'application web. L'effet est le même que si la case à cocher était désactivée. (Comme indiqué dans la procédure précédente, dans le cadre du développement de Compléments SharePoint pour les utilisateurs anonymes, ce paramètre n'a d'importance que pour les applications hébergées par SharePoint.)
    
  

> **IMPORTANTE**
> La procédure suivante ne peut être effectuée sur un site web public dans SharePoint Online. (Pour plus d'informations sur l'utilisation de sites web publics dans Microsoft SharePoint Online, voir l' [Aide sur les sites web public pour Office 365](http://office.microsoft.com/fr-fr/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1).) 
  
    
    


### Pour configurer une collection de sites SharePoint Online en vue de l'accès anonyme


1. Connectez-vous à Office 365 en tant qu'administrateur de clients.
    
  
2. Sur la page d'accueil de la collection de sites, sélectionnez **Publier le site web en ligne**, dans le coin supérieur droit de la page. Cette action désactive également la configuration requise relative à l'autorisation **Utiliser les interfaces distantes**.
    
  
Si vous développez un complément hébergé par SharePoint et qui accède à une liste SharePoint, vous devez également effectuer la procédure suivante dans votre collection de sites de test. Les clients qui utilisent votre complément doivent faire la même chose sur tous les sites web où le complément est installé. Un complément ne peut pas configurer une liste pour l'accès anonyme via un programme ou de façon descriptive. Les compléments peuvent installer des listes personnalisées, mais il est impossible de préconfigurer ces listes pour l'accès anonyme. Une fois le complément installé, la configuration doit encore être faite manuellement. Si vous développez un Complément SharePoint hébergé par un fournisseur pour l'accès anonyme, cette procédure n'est pas obligatoire.
  
    
    

> **REMARQUE**
> Cette procédure ne peut pas être effectuée dans une collection de sites SharePoint Online. Par conséquent, les compléments hébergés par SharePoint installés sur SharePoint Online et destinés à des utilisateurs anonymes n'ont pas accès aux listes ni aux bibliothèques. 
  
    
    


### Pour configurer une liste ou une bibliothèque pour l'accès anonyme


1. Accédez à une liste ou à une bibliothèque à laquelle le Complément SharePoint testé a accès, puis sélectionnez **Paramètres de liste** ou **Paramètres de la bibliothèque**. Pour un site SharePoint Online, vous devez être connecté en tant qu'administrateur de clients.
    
  
2. Sélectionnez **Autorisations de cette liste/bibliothèque**.
    
  
3. Sur la page qui apparaît, sélectionnez **Arrêter l'héritage des autorisations** sous l'onglet **Autorisations**, puis répondez **OK** à la demande de confirmation.
    
  
4. Sélectionnez **Accès anonyme** sous l'onglet **Autorisation**.
    
  
5. Sur le formulaire **Accès anonyme** qui apparaît, sélectionnez toutes les autorisations requises par les utilisateurs du Complément SharePoint. Vous pouvez autoriser l'affichage, la modification, l'ajout et la suppression d'éléments. Vous ne pouvez pas autoriser le contrôle total. Par conséquent, les utilisateurs anonymes ne peuvent modifier ni le schéma de la liste, ni les paramètres de liste.
    
  

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)
    
  

