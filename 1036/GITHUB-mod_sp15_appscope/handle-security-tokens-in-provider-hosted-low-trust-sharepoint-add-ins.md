---
title: Gestion des jetons de sécurité dans les compléments SharePoint à faible niveau de fiabilité hébergés par le fournisseur
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# Gestion des jetons de sécurité dans les compléments SharePoint à faible niveau de fiabilité hébergés par le fournisseur
Apprenez-en plus sur les jetons de contexte, d'accès et d'actualisation qui sont utilisés pour l'autorisation par les Compléments SharePoint à faible niveau de fiabilité hébergés par un fournisseur et découvrez comment les intégrer dans votre code.
> **IMPORTANTE**
> **Cet article est entièrement consacré à l'utilisation de jetons de sécurité dans le système d'autorisation à faible niveau de fiabilité et non dans le système à haut niveau de fiabilité.** Pour plus d'informations sur l'utilisation de jetons dans le système à haut niveau de fiabilité, voir [Création et utilisation de jetons d'accès dans les compléments à haut niveau de fiabilité hébergés par un fournisseur pour SharePoint](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md). 




 **Les Compléments SharePoint qui utilisent le  [système d'autorisation à faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) pour accéder à des données SharePoint participent à unflux OAuth qui implique le transfert de jetons de sécurité (dans un format [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)) entre SharePoint, Microsoft Azure Access Control Service (ACS), les composants distants du Complément SharePoint et, dans certains cas, le navigateur de l'utilisateur.** Il existe différents flux en fonction de la conception du complément, mais tous impliquent au moins les deux types de jetons suivants :
- **Jeton d'accès :** inclus dans chaque demande de création, lecture, mise à jour ou suppression (CRUD) provenant des composants distants du complément et transmise à SharePoint. SharePoint valide le jeton et répond à la demande.


- **Jeton d'actualisation :** utilisé pour obtenir un premier jeton d'accès dans le [flux de jeton de contexte](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) et pour remplacer les jetons d'accès arrivés à expiration à la fois dans le flux de jeton de contexte et le [flux de code d'autorisation](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) du système d'autorisation à faible niveau de fiabilité.


Selon le flux OAuth qu'utilise le complément, l'un ou l'autre des éléments suivants fait également partie du processus :
- **Jeton de contexte :** utilisé dans le flux de jeton de contexte pour fournir au composant distant un jeton d'actualisation et des informations qui lui sont nécessaires pour effectuer une demande de jeton d'accès auprès d'Azure ACS.


- **Code d'autorisation :** il ne s'agit pas d'un jeton, mais d'un code d'autorisation propre à chaque paire d'utilisateur et d'application. Il est utilisé dans le flux de code d'autorisation pour obtenir un premier jeton d'accès et un jeton d'actualisation.



## Comprendre la gestion des jetons d'accès
<a name="AccessTokens"> </a>

Dans le système d'autorisation à faible niveau de fiabilité, les jetons d'accès sont créés par Azure ACS et envoyés au composant distant de votre Complément SharePoint. (Lorsque cet article a été rédigé, les jetons d'accès émis par les services ACS pour SharePoint avaient une durée de vie de 12 heures, mais cela a pu changer.) Les principales **opérations devant être réalisées par le code de votre Complément SharePoint** sont les suivantes :




- **Demander un jeton d'accès** auprès d'Azure ACS. Selon le flux OAuth en place, le complément utilise soit un code d'autorisation, soit les informations qu'elle extrait à partir d'un jeton de contexte pour effectuer la demande.


- **Intégrer le jeton d'accès à chaque requête HTTP envoyée à SharePoint.** Le jeton est ajouté à la demande sous la forme d'un en-tête **Authorization**. La valeur de l'en-tête est le mot « Bearer », suivi par un espace et par le jeton d'accès encodé en base 64.


- **Mettre en cache le jeton d'accès** pour le réutiliser lors de demandes ultérieures (facultatif mais recommandé).


- Transmettre le jeton d'accès aux systèmes principaux afin qu'ils puissent accéder directement à SharePoint (facultatif).


Ces tâches doivent être effectuées à l'aide d'un code côté serveur. Si vous utilisez un code managé, il existe un exemple de code pour certaines de ces tâches dans les fichiers SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb) qui font partie des Outils de développement Microsoft Office pour Visual Studio. Pour un exemple de code PHP qui exécute certaines de ces tâches, voir  [Comprendre et utiliser l'interface REST de SharePoint 2013](http://msdn.microsoft.com/fr-fr/magazine/dn198245.aspx).




### Mettre en cache le jeton d'accès
<a name="CacheAccessToken"> </a>

Selon l'architecture et la plateforme d'hébergement de votre Complément SharePoint, il existe plusieurs **moyens de mettre en cache le jeton d'accès** sur le serveur.




- Dans l'état de session


- Dans l'état de l'application


- Dans les  [fonctionnalités de mise en cache de Windows Server AppFabric](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx) ou leur équivalent sur un système d'exploitation autre que Microsoft


- Dans le  [service de mise en cache de Microsoft Azure](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx) ou son équivalent sur un service cloud autre que Microsoft


- Dans une base de données


- Dans un système  [memcached](http://www.memcached.org/)



> **REMARQUE**
> Dans la plupart des cas, vous ne pourrez pas utiliser de termes aussi simples que « AccessToken » comme clé de mise en cache, car votre complément doit garder les jetons pour les différents utilisateurs et conserver la distinction batteries/locations SharePoint. Si votre complément utilise le  [flux de jeton de contexte](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), il existe une clé **CacheKey** spéciale fournie par SharePoint, qui peut être utilisée pour distinguer les jetons mis en cache. Cette section décrit les problèmes connus et les solutions à adopter lorsque votre application n'utilise pas le flux de jeton de contexte.




La mise en cache du jeton d'accès dans l'état de session fonctionne dans la plupart des cas. Si l'application web distante accède à d'autres services qui utilisent OAuth (en plus de SharePoint) et qu'elle met en cache les différents jetons d'accès dans l'état de session, veillez à utiliser des clés de cache distinctes pour les jetons. Par exemple, au lieu de « AccessToken », utilisez « SharePoint_AccessToken », « Facebook_AccessToken », « SAP_Gateway_AccessToken », etc. (Si vous n'utilisez pas l'état de session ou une autre méthode de mise en cache qui sépare automatiquement le cache de chaque utilisateur, vous devez également relativiser vos clés utilisateurs.)



Si l'application accède à plusieurs locations en ligne ou batteries SharePoint, vous pouvez utiliser le domaine SharePoint dans le cadre de la clé de mise en cache principale de l'application (« SharePoint_ *<mydomain>*  .sharepoint.com_AccessToken ») ou utiliser le domaine de la batterie/location (« SharePoint_ *<realmGUID>*  _AccessToken »), les deux pouvant être lus à partir du jeton d'accès. (Votre code doit inverser le codage en base 64 du jeton afin de le lire. Pour le code managé, le fichier TokenHelper.cs, ou .vb, comporte des exemples de code qui utilise les classes des espaces de noms **Microsoft.IdentityModel.S2S.Tokens** et **System.IdentityModel.Tokens**.) Il existe une autre option disponible lorsque l'application utilise le flux de jeton de contexte comme décrit dans le paragraphe suivant.



Dans certains cas, votre application doit mettre en cache le jeton d'accès à un endroit disponible pour l'application entre les sessions ou après la fin de l'une d'elles. Par exemple, l'application peut être conçue pour permettre aux utilisateurs de programmer des actions à effectuer après sa fermeture. Si ces actions incluent l'accès à SharePoint, le complément doit récupérer le jeton d'accès. Dans ce cas, **votre application doit conserver la distinction entre les jetons d'accès des différents utilisateurs**. Si vous utilisez le flux de jeton de contexte, une chaîne de clé de cache est fournie dans le jeton de contexte que SharePoint transmet au composant distant de votre Complément SharePoint lorsqu'il est lancé. Pour plus d'informations sur cette **clé de cache spéciale** et sur son utilisation, voir [Comprendre la clé de cache](#CacheKey). Vous pouvez également utiliser cette chaîne pour le scénario décrit dans le paragraphe précédent. Le système de planification a la possibilité de stocker les données de configuration dont il a besoin, comme le moment où le travail prévu doit être exécuté et les tâches à effectuer. Utilisez ce stockage pour conserver la clé de cache pour le jeton d'accès.



Si le cache intersession que vous utilisez est également partagé par plusieurs applications, les clés de cache doivent être relativisées par rapport aux applications ainsi qu'aux utilisateurs et aux domaines SharePoint. La clé de cache qui est fournie dans le jeton de contexte est propre aux applications ainsi qu'aux utilisateurs et au domaine SharePoint.



 **Si votre application utilise le flux de code d'autorisation**, il n'existe pas de jeton de contexte et, par conséquent, il n'existe pas de clé de cache spéciale. Dans ce cas, **vous devez créer votre propre système de clés pour les données mises en cache**, qui sont relativisées par rapport à un ou plusieurs des éléments suivants, en fonction des conflits potentiels de noms pouvant survenir selon l'utilisation de votre application : l'utilisateur, le domaine SharePoint et l'application. Vous pouvez utiliser les revendications à l'intérieur du jeton d'accès à cette fin ; par exemple, **nameId** et **aud** (voir les tableaux ci-dessous). Votre code peut tout simplement concaténer les chaînes ou les utiliser comme valeurs initiales pour créer un hachage unique qui peut servir de clé de cache.



Enfin, si votre application réalise à la fois des appels complément uniquement et des appels utilisateur + complément à SharePoint, elle disposera de deux jetons d'accès différents. Ainsi, vous aurez besoin de clés de cache distinctes. Une fois que vous avez choisi une clé de cache de base, ajoutez-y seulement « _add-in-only » ou « _add-in+user ».




> **ATTENTION**
> **Stocker le jeton d'accès dans un cookie n'est pas considéré comme une pratique sûre.** Il est généralement conseillé d'éviter que le jeton d'accès ne passe par le navigateur.





### Transmettre le jeton d'accès aux systèmes principaux
<a name="ForwardTokenToBackend"> </a>

Un Complément SharePoint peut avoir des serveurs principaux qui ne sont pas hébergés dans le même domaine que l'application web distante. Quand un serveur principal doit effectuer des opérations CRUD sur SharePoint, le complément s'exécute plus rapidement si ces opérations peuvent être transmises directement du système principal vers SharePoint. Heureusement, le domaine n'est important que lorsque votre application obtient un jeton d'accès des services ACS. Après avoir obtenu le jeton, elle peut le transmettre aux services principaux et appeler SharePoint en l'utilisant. Le code de ces systèmes doit pouvoir gérer les jetons d'accès arrivés à expiration et envoyer une demande de nouveau jeton à l'application web parente qui est enregistrée auprès des services ACS. Voir  [Gérer les jetons d'accès arrivés à expiration](#Expired). Cette technique doit être utilisée uniquement pour les serveurs principaux de votre application et non pour les services web externes. Aussi, si vous utilisez cette technique, pensez à modifier la conception des services principaux pour qu'ils utilisent des appels complément uniquement lorsque cela est possible.




### Gérer les jetons d'accès arrivés à expiration
<a name="Expired"> </a>

Un jeton d'accès expire au bout de quelques heures (douze heures au moment de la rédaction de cet article, mais cela a pu changer). Si l'application accède à SharePoint après l'expiration du jeton d'accès, la première demande à SharePoint suivant l'expiration provoque une erreur **401 Unauthorized**. Votre code doit pouvoir gérer cette réponse. Sinon, le code peut tester le délai d'expiration du jeton d'accès avant de l'utiliser. **Votre code utilise le jeton d'actualisation pour obtenir un autre jeton d'accès à partir des services ACS.** Dans le flux de jeton de contexte, le jeton d'actualisation est inclus dans le jeton de contexte que votre complément reçoit de SharePoint au début de sa première session avec SharePoint. Dans le flux de code d'autorisation, il est transmis à l'application en même temps que le premier jeton d'accès. Votre code doit le mettre en cache afin qu'il soit disponible en cas de besoin. Le jeton d'actualisation a une durée de vie de quelques mois et peut être conservé dans un cookie ou dans le stockage côté serveur. Pour plus d'informations, voir [Comprendre la gestion et la mise en cache des jetons d'actualisation](#RefreshToken).




### Exemples de jetons d'accès
<a name="ExampleAccessTokens"> </a>

Cette section décrit des jetons d'accès à l'aide d'exemples et montre à quel point ils varient en fonction de la stratégie d'autorisation qui est utilisée.




#### Jetons d'accès pour le système d'autorisation à faible niveau de fiabilité

 **Stratégie utilisateur + complément**



Vous trouverez ci-dessous un **exemple décodé d'un jeton d'accès utilisateur + complément généré par les services ACS**, conçu pour être utilisé lors des appels vers SharePoint à l'aide de la [stratégie utilisateur + complément](add-in-authorization-policy-types-in-sharepoint-2013.md). Des espaces ont été ajoutés pour une meilleure lisibilité. Le jeton respecte le protocole  [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1). L'objet JavaScript Object Notation (JSON) du jeton est appelé l'ensemble de revendications. Pour plus de détails concernant l'ensemble de revendications, voir le tableau 1. Veuillez noter que toutes les valeurs doivent être en minuscules. (Les jetons d'accès utilisateur + complément sont les mêmes dans le  [flux de jeton de contexte](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) et le [flux de code d'autorisation](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows).)





```

{
 "aud": "00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss": "00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf": 1377549246,
 "exp": 1377592446,
 "nameid": "2303000085ff9abc",
 "actor": "964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73",
 "identityprovider": "urn:federation:microsoftonline"
}```


**Tableau 1 : Revendications contenues dans le jeton d'accès utilisateur + complément généré par les services ACS**


|**Revendication**|**Description**|**Valeur correspondante dans l'exemple de jeton d'accès**|
|:-----|:-----|:-----|
| `aud` <br/> |Abréviation du mot « audience ». Représente le principal auquel est destiné le jeton. Le format est  _identificateur du principal d'audience_/ _domaine SharePoint complet_@ _domaine SharePoint_, où  _identificateur du principal d'audience_ est un identificateur de principal de sécurité permanent pour SharePoint. (Voir [Constantes des principaux d'application de produit Microsoft](http://msdn.microsoft.com/fr-fr/library/hh629982%28v=office.12%29.aspx).)  <br/>  _domaine SharePoint_ est le GUID de la location SharePoint Online ou de la batterie SharePoint locale, utilisée par le jeton d'accès. Ce GUID sert d'identificateur de domaine pour l'émetteur du jeton, dans ce cas Azure ACS. <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |Abréviation du mot « issuer » (émetteur). Représente le principal qui a créé le jeton. Le format est  _GUID d'émetteur_@ _GUID du domaine SharePoint_.  <br/> Dans le système d'autorisation à faible niveau de fiabilité, l'émetteur est Azure ACS et son GUID est **00000001-0000-0000-c000-000000000000**. <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |Abréviation des mots « not before » (pas avant). Représente l'heure à laquelle le jeton  *commence*  à être valide, en secondes depuis le 1er janvier 1970 à minuit. Par défaut, cette valeur est définie sur le moment auquel le jeton a été créé. Pour plus d'informations, voir [Utiliser les valeurs de temps JWT](#JWTtimes).  <br/> |1377549246  <br/> |
| `exp` <br/> |Abréviation du mot « expiration ». Représente l'heure d'expiration du jeton. Par défaut, il s'agit de 12 heures après l'heure indiquée par la valeur **nbf**. Pour plus d'informations, voir [Utiliser les valeurs de temps JWT](#JWTtimes).  <br/> |1377592446  <br/> |
| `nameid` <br/> |Identificateur unique de l'utilisateur pour lequel le jeton est émis. Le format varie selon le fournisseur d'identité. Dans cet exemple, le fournisseur est Microsoft Online, mais s'il s'agissait d'Active Directory, l'ID serait semblable à « s-1-5-21-2127521184-1604012920-1887927527-415149 ».  <br/> |2303000085ff9abc  <br/> |
| `actor` <br/> |Principal qui cherche à avoir accès à la batterie ou à la location SharePoint. Le format est  _ID client de l'application_@ _domaine SharePoint_.  <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `identityprovider` <br/> |Nom unique du fournisseur d'identité tel qu'inscrit auprès de l'organisme IANA (Internet Assigned Numbers Authority).  <br/> Pour un complément installé sur SharePoint Online, la valeur est généralement la même que dans cet exemple.  <br/> Pour un complément installé dans une batterie locale, il s'agit généralement d'un fournisseur d'identité local, tel que « urn:office:idp:activedirectory ».  <br/> |urn:federation:microsoftonline  <br/> |
 
 **Stratégie complément uniquement**



Vous trouverez ci-dessous un **exemple décodé d'un jeton d'accès complément uniquement généré par les services ACS**, conçu pour être utilisé lors des appels vers SharePoint à l'aide de la [stratégie complément uniquement](add-in-authorization-policy-types-in-sharepoint-2013.md). Des espaces ont été ajoutés pour une meilleure lisibilité. Le jeton respecte le protocole  [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1). Pour plus de détails concernant l'ensemble de revendications, voir le tableau 2. (La stratégie complément uniquement n'est pas disponible pour les applications qui utilisent le  [flux de code d'autorisation](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), car elles ne disposent pas d'un fichier manifeste de complément et, par conséquent, ne peuvent pas demander l'autorisation d'utiliser des appels complément uniquement.)





```

{
 "aud":"00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":1403304705,
 "exp":1403347905,
 "nameid":"c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73",
 "sub":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "oid":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "trustedfordelegation":"false",
 "identityprovider":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73"
}```


**Tableau 2 : Revendications contenues dans le jeton d'accès complément uniquement généré par les services ACS**


|**Revendication**|**Description**|**Valeur correspondante dans l'exemple de jeton d'accès**|
|:-----|:-----|:-----|
| `aud` <br/> |Identique au jeton utilisateur + complément ci-dessus.  <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |Identique au jeton utilisateur + complément ci-dessus.  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |Identique au jeton utilisateur + complément ci-dessus.  <br/> |1403304705  <br/> |
| `exp` <br/> |Identique au jeton utilisateur + complément ci-dessus.  <br/> |1403347905  <br/> |
| `nameid` <br/> |Identificateur unique du complément, à la place de l'utilisateur, puisque l'identité de l'utilisateur n'a pas d'importance dans le cadre de la stratégie complément uniquement. Le format est  _ID client_@ _domaine SharePoint_.  <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `sub` <br/> |Abréviation du mot « subject » (sujet). Représente le sujet du jeton, qui est le principal cherchant à accéder à SharePoint ; dans ce cas, il s'agit de l'application web distante. L'identificateur d'objet est utilisé pour la valeur. Voir la revendication **oid** ci-dessous. <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `oid` <br/> |Abréviation des mots « object ID » (identificateur d'objet). Représente l'identificateur d'objet dans Azure Active Directory pour l'application web distante. Dans un jeton d'accès complément uniquement, le sujet et l'identificateur d'objet ont la même valeur.  <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `trustedfordelegation` <br/> |Valeur booléenne indiquant si SharePoint doit faire confiance au Complément SharePoint pour authentifier et autoriser l'utilisateur. Cette valeur est définie sur false dans les appels complément uniquement, car l'identité de l'utilisateur n'a pas d'importance.  <br/> |false  <br/> |
| `identityprovider` <br/> |Nom unique du fournisseur d'identité. Comme l'identité fournie est celle du complément plutôt que celle de l'utilisateur, les services ACS sont le fournisseur d'identité. Le format est  _GUID des services ACS_@ _domaine SharePoint_.  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
 

## Comprendre la structure et la gestion des jetons de contexte
<a name="ContextTokens"> </a>

Un jeton de contexte est utilisé uniquement dans le  [flux de jeton de contexte](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) du système d'autorisation à faible niveau de fiabilité. **Lorsque le Complément SharePoint est lancée dans SharePoint, SharePoint demande à Azure ACS de créer un jeton de contexte que SharePoint transmet ensuite au composant distant du Complément SharePoint.**. Le jeton est transmis comme un formulaire de paramètre masqué, appelé **SPAppToken**, dans une demande de SharePoint pour la page d'accueil du composant distant. Le jeton est signé avec une clé secrète client connue seulement des services ACS et du Complément SharePoint. Le **jeton de contexte comprend un jeton d'actualisation que le complément utilise**, ainsi que d'autres informations provenant du jeton de contexte, **pour demander un jeton d'accès** auprès des services ACS. (Lorsque cet article a été rédigé, les jetons de contexte émis par les services ACS pour SharePoint avaient une durée de vie de 12 heures, mais cela a pu changer.) Les **tâches principales devant être effectuées par le code du Complément SharePoint** sont les suivantes :




- Utiliser la clé secrète client du complément pour **valider le jeton de contexte**.


- **Mettre en cache le jeton de contexte** ou extraire, et mettre en cache séparément, le jeton d'actualisation et certains autres éléments inclus dans celui-ci.


- Utiliser le jeton d'actualisation et d'autres informations pour **demander un jeton d'accès** auprès des services ACS (qui sont eux-mêmes mis en cache ensuite).


- **Mettre en cache la clé CacheKey** du jeton de contexte.



> **IMPORTANTE**
> Les deux premières tâches doivent être effectuées avant que l'utilisateur navigue vers une autre page ou actualise la page, sinon, le jeton est perdu. Par exemple, dans une application Web Forms ASP.NET, incluez ces tâches dans la méthode **Page_Load** (dans un bloc de code conditionnel qui n'est exécuté que lorsque la demande n'est pas une publication). Dans une application ASP.NET MVC, incluez ces tâches dans la méthode du contrôleur par défaut.




Si vous utilisez du code managé, un exemple de code pour certaines de ces tâches se trouve dans les fichiers SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb) inclus dans les Outils de développement Microsoft Office pour Visual Studio. Vous devez seulement envoyer des appels simples aux membres de la classe TokenHelper. Par exemple, votre code peut effectuer la première tâche avec la seule ligne de code suivante :





```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);```

Pour obtenir un exemple illustrant comment effectuer certaines de ces tâches avec du code PHP, voir  [SharePoint 2013 : Effectuer des opérations dans la bibliothèque de documents SharePoint à partir d'un site PHP](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef).




### Mettre en cache le jeton de contexte ou certains de ses éléments
<a name="CacheContextToken"> </a>

Vous pouvez **mettre en cache** la totalité du jeton de contexte, ou seulement le jeton d'actualisation et certains autres éléments inclus dans celui-ci que votre code utilise pour obtenir des jetons d'accès, **aussi bien dans un stockage côté serveur que côté client**. Par souci de simplicité, cet article suppose que vous mettez en cache le jeton de contexte dans son ensemble.




> **IMPORTANTE**
> Nous vous rappelons que vous ne devez pas utiliser pas la mise en cache côté client pour le jeton d' *accès*  . Son utilisation est sûre uniquement pour le jeton de contexte.




Vous disposez des mêmes  [options de mise en cache côté serveur](#CacheAccessToken) que celles répertoriées ci-dessus pour le jeton d'accès. Les options côté client comprennent un cookie et un champ de formulaire masqué sur une page HTML. Une autre option consiste à stocker le jeton de contexte dans le cache de session, mais pensez à stocker la clé **CacheKey** obtenue sur le client.



Si votre application accède à SharePoint après la fin d'une session, ni la mise en cache de la session ni la mise en cache côté client ne sont possibles, car le jeton d'actualisation doit être disponible pour l'application au cas où le jeton d'accès d'origine ait expiré lors de l'exécution du travail post-session. Dans ce cas, vous devez utiliser un cache (intersession) durable qui soit partagé par plusieurs utilisateurs, domaines et/ou applications SharePoint. Ainsi, votre code doit utiliser les clés de cache qui distinguent l'utilisateur, le domaine SharePoint et/ou l'application, comme expliqué ci-dessus dans  [Mettre en cache le jeton d'accès](#CacheAccessToken). Vous pouvez **utiliser la clé de cache spéciale** qui se trouve à l'intérieur du jeton de contexte à cet effet de la même manière que vous l'utiliseriez pour le jeton d'accès. (Voir [Comprendre la clé de cache](#CacheKey) ci-dessous.)




#### Comprendre la clé de cache
<a name="CacheKey"> </a>

 **La clé de cache est une chaîne opaque qui est propre à la combinaison formée par l'utilisateur, l'émetteur de nom d'utilisateur, le complément et la batterie SharePoint ou le client SharePoint Online.** Avant le chiffrement, elle a la forme suivante, où _Realm_ est le GUID de la batterie SharePoint locale ou de la location SharePoint Online.



 _UserNameId_ + "," + _UserNameIdIssuer_ + "," + _ApplicationId_ + "," + _Realm_



La clé de cache ne contient pas d'informations sur l'URL du site. Chaque client SharePoint Online (ou batterie SharePoint locale) a un domaine unique. Par conséquent, la clé de cache est propre à chaque combinaison formée par le nom d'utilisateur, l'émetteur de nom d'utilisateur, l'application et la batterie. Dans le jeton de contexte de l'exemple ci-dessous, la valeur de la clé de cache chiffrée est :



 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`



Étant donné que votre application peut mettre en cache plusieurs éléments, tels que le jeton d'accès et le jeton de contexte dans le même cache avec la même clé de cache, **il convient d'envisager d'utiliser la clé de cache comme un radical** et d'ajouter au début ou à la fin de ce dernier une chaîne spécifique, telle que « AccessToken » ou « ContextToken », selon les besoins, pour former une clé de cache complète. **Une autre option consiste à créer une classe** avec les propriétés des différents éléments que vous voulez mettre en cache, puis de mettre en cache un objet de ce type. Une **troisième option**, si vous **utilisez une base de données** en tant que cache, consiste à utiliser un tableau avec une colonne CacheKey et des colonnes supplémentaires pour les éléments mis en cache (AccessToken, ContextToken, etc.).



Bien entendu, votre application n'est pas tenue d'utiliser le même cache pour tous les éléments. La pratique courante consiste à mettre en cache le jeton d'accès dans le cache de session, le jeton de contexte (ou le jeton d'actualisation qu'il contient) dans une base de données et la clé CacheKey dans un cookie.




### Utiliser le jeton de contexte pour obtenir un jeton d'accès
<a name="UseContextTokenToGetAccessToken"> </a>

 **Pour obtenir un jeton d'accès, votre application envoie une demande directement aux services ACS.** La demande comprend trois éléments d'information qui sont extraits du jeton de contexte (en plus d'autres informations) :




- Le jeton d'actualisation


- Le GUID du principal d'application de SharePoint


- Le GUID du domaine de la batterie SharePoint ou de la location SharePoint Online à laquelle le complément souhaite accéder


Le fichier TokenHelper.cs (ou .vb) contient du code qui crée cette demande. Pour un exemple de code PHP, voir  [SharePoint 2013 : Effectuer des opérations dans la bibliothèque de documents SharePoint à partir d'un site PHP](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454) .



L'application peut obtenir le domaine de la batterie ou de la location SharePoint à l'exécution, plutôt que de l'analyser à partir du jeton de contexte. Si vous utilisez du code managé, il existe une méthode  `TokenHelper.GetRealmFromTargetUrl` qui permet d'obtenir le domaine. Veillez à mettre en cache la valeur afin que votre code ne réalise pas un autre appel réseau pour l'obtenir à nouveau.




### Obtenir un nouveau jeton de contexte
<a name="GetNewContextToken"> </a>

 **Si vous avez besoin d'un nouveau jeton de contexte**, généralement parce que le jeton d'actualisation (qui est contenu dans le jeton de contexte) a expiré, **votre code peut en obtenir un nouveau en redirigeant le navigateur vers une page spéciale sur tous les sites web SharePoint**, AppRedirect.aspx. Deux paramètres de la requête doivent être joints à l'URL de cette page :




-  `client_id` : ID client de votre Complément SharePoint.


-  `redirect_uri` : URI vers lequel vous souhaitez que le navigateur soit redirigé après l'obtention du nouveau jeton de contexte. SharePoint PUBLIERA le jeton de contexte sur cet URI. Généralement, il s'agit de la même page, la méthode du contrôleur ou une méthode de service web qui a demandé le nouveau jeton de contexte. La valeur doit être codée au format URL.


La structure de l'URL doit être la suivante :





```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>```

L'exemple ci-dessous montre comment effectuer une demande dans ASP.NET en utilisant le fichier TokenHelper :





```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));```


### Exemple de jeton de contexte
<a name="ExampleContextToken"> </a>

Vous trouverez ci-dessous un exemple de jeton de contexte. Le petit objet JavaScript Object Notation (JSON), situé dans la partie supérieure, contient les métadonnées relatives au jeton. Les propriétés sont les mêmes que dans les jetons d'accès (voir ci-dessus). La valeur de la propriété **alg** correspond au nom de l'algorithme qui est utilisé pour générer la signature que les services ACS ajoutent au jeton. Pour plus de détails sur les propriétés de charge utile du jeton, voir le tableau 3. Notez que toutes les valeurs doivent être en minuscules. (Des espaces ont été ajoutés pour une meilleure lisibilité.)



```
{"typ":"JWT","alg":"HS256"}
.
{
 "aud":"a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":"1335822895",
 "exp":"1335866095",
 "appctxsender":"00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "appctx":"{
            \\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\",
            \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"
           }",
 "refreshtoken":"IAAAAC1Lv5w0OrcFAmJx0xk6aaBdhgsw3VPnPzNEDAWypTHtCYytZ2/dBBUKj+HLK8YB3IUCUfDxYpAque
NHKtgs4rYJJ5AegQpNMOJR1yYK8ngivQx0oetj7aSPuGVb+k6at6G0Kx5LZ5vhxkAq8iUSwu8p4L2cvNMzDF1mDKfMivqxgrIZkr2nbf9as0SJFL6VG5hZnDE4HKq
xJnejSW3umatKM4fsfY1MClVCxrkXb2EQ8H/TmwaJc388YW063GEVUS/3BTSgSIRBKQUmXJuJ6BZY7WTm84LaGrx3mIjnUTM/jnqPoPG55JbCC9sS/MeGNPtzPPCDg
6Vv7dVhQ1Dq5Y3fQ65e9LpJ580jCgzYYvpIFT+Wx5V+17mjY2T8wug04K2ts87Znsr+GfFCorf7NS/lj5HjoxRAQ2tva/8dwguSLwxcUwi/Q9MbpR0NNtlpwVazqi9O
hJ4Df7gVhUDdJ0Dtc6aFCPbl5ZLDDRs42xK2", 
 "isbrowserhostedapp": "true"
}```

Les revendications **aud**, **iss**, **nbf** et **exp** sont identiques à celles décrites ci-dessus pour le jeton d'accès. Les revendications **appctxsender**, **appctx**, **CacheKey**, **SecurityTokenServiceUri**, **refreshtoken** et **isbrowserhostedapp** sont décrites dans le tableau suivant.




**Tableau 3 : Revendications et informations contenues dans un jeton de contexte**


|****Revendication****|****Description****|****Valeur correspondante dans l'exemple de jeton de contexte****|
|:-----|:-----|:-----|
|aud  <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|iss  <br/> ||00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|nbf  <br/> ||1335822895  <br/> |
|exp  <br/> ||1335866095  <br/> |
|appctxsender  <br/> |Abréviation des mots « application context sender » (expéditeur du contexte d'application). Représente l'application qui a envoyé le jeton de contexte au Complément SharePoint.  <br/> Le format est  _GUID du principal_@ _domaine SharePoint_, où  _GUID du principal_ représente l'ID constant du principal de l'application, à savoir SharePoint, Exchange 2013, Lync 2013 ou Workflow. <br/> |00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|appctx  <br/> |Abréviation des mots « add-in context » (contexte de complément). Il s'agit d'un objet JSON contenant les éléments **CacheKey** et **SecurityTokenServiceURI**. <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"  <br/> |
|CacheKey  <br/> |Valeur unique qui peut servir de clé à tout cache structuré au format clé/valeur pour stocker et récupérer le jeton de contexte. Cette valeur peut également être utilisée en tant que valeur d'une colonne de clé dans une ligne de base de données.  <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=  <br/> |
|SecurityTokenServiceURI  <br/> |URI du service d'émission de jeton.  <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2  <br/> |
|refreshtoken  <br/> |Jeton d'actualisation du complément.  <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6…  <br/> |
|isbrowserhostedapp  <br/> |Champ **Boolean** qui indique si la demande au complément qui contient le jeton de contexte émane d'un navigateur (true) ou d'un récepteur d'événements distant (false). <br/> |true  <br/> |
 

### Utiliser le jeton de contexte pour limiter l'accès aux seuls utilisateurs de SharePoint
<a name="UseContextTokenToLimitAccess"> </a>

Si vous souhaitez limiter l'accès à votre composant distant, comme un service WCF, aux utilisateurs SharePoint, votre code peut simplement valider le jeton de contexte dans la requête HTTP. (Si vous utilisez du code managé, vous pouvez simplement appeler **TokenHelper.ReadAndValidateContextToken()**.) Votre code peut vérifier que la revendication actor du jeton commence par **00000003-0000-0ff1-ce00-000000000000**, si vous voulez vous assurer qu'il s'agit de SharePoint (et non, par exemple, de Exchange 2013, Lync 2013 ou de Workflow). Si vous voulez effectuer une validation supplémentaire qui nécessite un rappel vers SharePoint, par exemple en limitant l'accès aux utilisateurs avec un certain rôle dans SharePoint, vous pouvez mettre en cache le fait que vous avez effectué cette validation pour un utilisateur en particulier (en utilisant la clé **CacheKey** du jeton de contexte), afin de n'avoir à effectuer cette opération qu'une seule fois.




## Comprendre la gestion et la mise en cache des jetons d'actualisation
<a name="RefreshTokens"> </a>

 **Un jeton d'actualisation est inclus dans le jeton de contexte que SharePoint publie sur votre application web quand elle est lancée.** Le jeton d'actualisation est un jeton chiffré que votre Complément SharePoint ne peut pas déchiffrer. **Votre code l'utilise**, avec d'autres informations, **pour obtenir un nouveau jeton d'accès lorsque le jeton d'accès actuel arrive à expiration**. Il est également utilisé pour obtenir le *premier*  jeton d'accès dans le [flux de jeton de contexte](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows). (Lorsque cet article a été rédigé, les jetons d'actualisation émis par les services ACS pour SharePoint avaient une durée de vie de 6 mois, mais cela a pu changer.)



Étant donné qu'un jeton d'accès a une durée de vie de quelques heures (actuellement 12) et qu'un utilisateur final en obtient un nouveau chaque fois qu'il lance votre Complément SharePoint depuis SharePoint, vous avez besoin du jeton d'actualisation uniquement dans les cas suivants :




- Les utilisateurs ouvrent des sessions longues au cours desquelles le complément effectue des appels vers SharePoint durant de nombreuses heures (actuellement plus de 12) après avoir été lancée.


- La conception du complément permet aux utilisateurs de programmer le complément afin qu'il accède à SharePoint quelque temps après la fin de la session.


Dans les deux cas ci-dessus, **votre application doit mettre en cache le jeton d'actualisation** et, dans le second cas, vous devez utiliser un cache côté serveur qui soit durable pendant les sessions. Vos options de mise en cache sont les mêmes que pour le [jeton d'accès](#CacheAccessToken) et, dans le flux de jeton de contexte, vous pouvez utiliser [la clé de cache dans le jeton de contexte](#CacheKey). (Dans le flux de jeton de contexte, en règle générale, vous mettez en cache simplement le jeton de contexte. Il contient le jeton d'actualisation et les autres informations nécessaires à l'obtention d'un nouveau jeton dֺ'accès. Dans le  [flux de code d'autorisation](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), il n'existe aucun jeton de contexte. Par conséquent, vous mettez en cache le jeton d'actualisation lui-même.)



Si vous mettez en cache le jeton d'actualisation dans un stockage persistant pendant les sessions d'un utilisateur spécifique, veillez à le remplacer par le jeton d'actualisation le plus récent. Dans le flux de code d'autorisation, comme dans le flux hébergé sur le cloud, l'utilisateur obtient un nouveau jeton d'actualisation à chaque fois qu'il lance le complément.



Lorsque le jeton d'actualisation arrive à expiration, toute demande de nouveau jeton d'accès auprès des services ACS se traduira par une erreur **401 Unauthorized**. Votre complément doit répondre à cette erreur en obtenant un nouveau jeton d'actualisation et en l'utilisant pour obtenir un nouveau jeton d'accès. (Étant donné que le jeton d'actualisation est chiffré, votre code ne peut pas vérifier sa date d'expiration avant de l'utiliser.) Dans le flux de jeton de contexte, votre complément reçoit un nouveau jeton d'actualisation en  [obtenant un nouveau jeton de contexte](#GetNewContextToken). Dans le flux de code d'autorisation, votre complément reçoit un nouveau jeton d'actualisation en redémarrant le flux. Plus précisément, votre code doit répondre à l'erreur en redirigeant l'utilisateur vers la page OAuthAuthorize.aspx de SharePoint, comme expliqué dans  [Comprendre le flux OAuth pour les compléments qui demandent des autorisations à la volée](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow).




## Comprendre la gestion des codes d'autorisation
<a name="Authcodes"> </a>

 **Dans le flux de code d'autorisation, le processus d'autorisation commence par un code d'autorisation émis par les services ACS, à la demande de SharePoint et que SharePoint transmet ensuite à l'application distante** comme un paramètre de requête. Le code d'autorisation est propre à chaque paire d'utilisateur et d'application distante. (Lorsque cet article a été rédigé, les codes d'autorisation émis par les services ACS pour SharePoint avaient une durée de vie de 5 minutes, mais cela a pu changer.) La logique de **votre application doit obtenir le code d'autorisation du paramètre de requête et l'utiliser dans une demande de jeton d'accès auprès des services ACS**. Si vous utilisez du code managé, un exemple de code pour créer le jeton se trouve dans le fichier TokenHelper.cs (et .vb). Un exemple de code pour lire le paramètre de requête se trouve sur la page [Obtenir un exemple de code-behind d'une page accédant à SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default). Les services ACS invalident le code d'autorisation immédiatement après l'émission du jeton d'accès, de sorte qu'il ne peut être utilisé qu'une seule fois et qu'il est inutile de le mettre en cache.




## Utiliser les valeurs de temps JWT
<a name="JWTtimes"> </a>

Les revendications **nbf** et **exp** sont au format indiqué par la [spécification JWT](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl). Elles sont exprimées en nombre de secondes écoulées depuis le 1er janvier 1970. Dans le langage C#, vous pouvez convertir ces valeurs à l'aide du code suivant, où  _jWTTimeStamp_ représente la valeur du jeton, comme 1335822895.



```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);
```


## Résolution des problèmes relatifs à la gestion des jetons
<a name="Troubleshooting"> </a>

L' [outil gratuit Fiddler](http://www.telerik.com/fiddler) permet de capturer les requêtes HTTP envoyées par le composant distant de votre complément à SharePoint. Il existe une [extension gratuite de cet outil](https://github.com/andrewconnell/SPOAuthFiddlerExt) qui décode automatiquement les jetons dans les requêtes.




## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Création de compléments pour SharePoint qui utilisent l'autorisation de faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)


- Pour obtenir des exemples de code utilisant du code managé et TokenHelper, voir  [SharePoint 2013 : création d'un complément distant Hello World à l'aide du modèle CSOM](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf) et [Pack d'exemples des compléments pour SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)


-  Pour obtenir un exemple de code utilisant des appels REST depuis un complément PHP, voir la rubrique [SharePoint 2013 relative à la réalisation d'opérations dans la bibliothèque de documents SharePoint à partir d'un site PHP](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)



