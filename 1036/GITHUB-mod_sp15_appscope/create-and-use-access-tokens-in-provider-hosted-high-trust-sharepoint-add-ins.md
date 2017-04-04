---
title: Création et utilisation de jetons d'accès dans les compléments à haut niveau de fiabilité hébergés par un fournisseur pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: cb3264d4-41a6-498f-a408-75b077566051
---


# Création et utilisation de jetons d'accès dans les compléments à haut niveau de fiabilité hébergés par un fournisseur pour SharePoint
Découvrez le rôle des jetons d'accès dans les compléments SharePoint à haut niveau de fiabilité et le fonctionnement de votre code avec eux.
> [!IMPORTANTE]
> **Cet article est entièrement consacré à l'utilisation des jetons d'accès dans le système d'autorisation à haut niveau de fiabilité, et non dans le système ACS.** Pour en savoir plus sur l'utilisation des jetons de sécurité dans le système ACS, voir [Gestion des jetons de sécurité dans les compléments SharePoint à faible niveau de fiabilité hébergés par le fournisseur](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md). 
  
    
    

 **Les Compléments SharePoint qui utilisent le  [système d'autorisation à haut niveau de fiabilité](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) pour accéder à SharePoint doivent transmettre un jeton d'accès** (un [jeton web JSON](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)) à SharePoint lors de chaque demande de création, lecture, mise à jour ou suppression (CRUD). SharePoint valide le jeton et exécute la demande. Cet article fournit des informations sur la façon dont votre code crée et transmet le jeton d'accès.
Dans le système d'autorisation à haut niveau de fiabilité, **le composant distant de votre Complément SharePoint crée le jeton d'accès**. Si le composant distant utilise du code managé pour son code côté serveur, la plupart du travail de codage pour créer les jetons est effectué pour vous dans les fichiers SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb) qui sont inclus dans Outils de développement Office pour Visual Studio. Étant donné que le client pour un Complément SharePoint à haut niveau de fiabilité dispose de SharePoint en local, il n'est probablement pas contre le fait d'utiliser ASP.NET, IIS et Windows Server comme pile d'hébergement pour le composant distant. Nous vous recommandons d'utiliser cette pile, car les deux fichiers générés vous éviteront d'avoir à effectuer la plupart du codage et du travail de test. Si le composant distant doit utiliser un langage autre que .NET, et que le composant distant et la batterie de serveurs SharePoint sont connectés à Internet, vous devez envisager d'utiliser [le système d'autorisation à faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) plutôt que celui à haut niveau de fiabilité. Dans le système Microsoft Azure Access Control Service (ACS), tous les jetons sont créés par ACS, donc vous êtes également dispensé d'une grande quantité de travail. La suite de cet article vise essentiellement à fournir des conseils aux développeurs créant des Compléments SharePoint avec des composants distants dans un langage autre que .NET et en utilisant le système d'autorisation à haut niveau de fiabilité. Cet article peut également fournir des informations précieuses sur le débogage de Compléments SharePoint basés sur .NET qui utilisent le système à haut niveau de fiabilité.
  
    
    


## Comprendre la gestion des jetons d'accès
<a name="AccessTokens"> </a>


> [!REMARQUE]
>  Au cours de la lecture de cet article, en particulier dans les parties relatives aux tâches que votre code doit effectuer, n'oubliez pas que si vous utilisez le code managé, les Outils de développement Microsoft Office pour Visual Studio ajoutent à tous les projets de Complément SharePoint deux fichiers de code généré, SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb) qui accomplissent la plupart de ces tâches à votre place. Le code de gestion du jeton de votre application n'effectue généralement que quelques appels aux classes de ces fichiers. Les informations contenues dans cette rubrique sont destinées à aider les développeurs qui n'utilisent pas le code managé (et à aider ceux qui souhaitent résoudre des problèmes en relation avec les jetons).>  Pour plus de langues et de plateformes, accédez aux liens vers les bibliothèques OAuth en cliquant sur :>  [OAuth 2.0](http://oauth.net/2/) : faites défiler jusqu'à **Client Libraries** (bibliothèques clientes).>  Pour plus d'informations, effectuez une recherche sur [github](https://github.com/) pour « OAuth 2 » et « jeton web JSON » (sans les guillemets).
  
    
    

Les principales **actions que votre code doit** effectuer sont les suivantes :
  
    
    

1. **Créer un jeton d'accès.** Les sous-tâches pour la création de ce jeton varient selon que l'application web à distance effectue des appelscomplément uniquement à SharePoint, des appelsutilisateur + complément, ou les deux. (Pour en savoir plus sur les deux types d'appels, voir  [Types de stratégie d'autorisation des compléments dans SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).) 
    
    Si le complément effectue des appels utilisateur + complément, la création du jeton comprendra les sous-tâches suivantes :
    
1. Créer un jeton acteur qui identifie le Complément SharePoint et dit à SharePoint de déléguer l'authentification et l'autorisation de l'utilisateur à votre complément, et l'encoder en base 64. Vous trouverez des informations relatives aux revendications et à la structure du jeton acteur dans le tableau 2 ci-dessous, et des informations relatives à l'encodage et à la signature du jeton sont disponibles à la section [Encoder et signer des jetons](#EncodingTokens) ci-dessous.
    
  
2. Signer le jeton acteur avec des informations d'identification provenant d'un certificat x509 configuré par un administrateur de la batterie de serveurs SharePoint pour que SharePoint l'approuve.
    
  
3. Intégrer le jeton acteur dans le jeton d'accès.
    
  
4. Ajouter les autres revendications requises au jeton d'accès. Vous trouverez des informations sur les revendications et la structure du jeton dans le tableau 1 ci-dessous.
    
  
5. Encoder le jeton d'accès en base 64. Des informations relatives à l'encodage et à la signature du jeton sont disponibles à la section  [Encoder et signer des jetons](#EncodingTokens) ci-dessous.
    
  

    Si le complément effectue des appels complément uniquement, votre code doit seulement effectuer les deux premières sous-tâches. Le jeton acteur sert de jeton d'accès.
    
    Si le complément effectue des appels utilisateur + complément et des appels complément uniquement, il doit créer un jeton acteur simple pour les appels complément uniquement, et un jeton d'accès imbriqué plus étendu pour les appels utilisateur + complément. Un même jeton d'accès ne peut pas être utilisé pour les deux appels.
    
  
2. **Intégrer le jeton d'accès à chaque demande HTTP à SharePoint.** Le jeton est ajouté à la demande sous la forme d'un en-tête **Authorization**. La valeur de l'en-tête est le mot « Bearer », suivi par un espace et par le jeton d'accès encodé en base 64.
    
  
3. **Mettre en cache le jeton d'accès** pour le réutiliser lors de demandes ultérieures (facultatif).
    
  
Ces tâches doivent être effectuées à l'aide d'un code côté serveur. Si vous utilisez du code managé, un exemple de code pour certaines de ces tâches est disponible dans les fichiers SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb) générés par les Outils de développement Microsoft Office pour Visual Studio.
  
    
    

### Mettre en cache le jeton d'accès
<a name="CacheAccessToken"> </a>

Une fois qu'un jeton est créé, il peut être réutilisé pour des appels ultérieurs à SharePoint jusqu'à ce qu'il expire. Selon l'architecture et la plateforme d'hébergement du composant distant, il existe plusieurs **façons de mettre en cache le jeton d'accès** sur le serveur.
  
    
    

- Dans l'état de session
    
  
- Dans l'état de l'application
    
  
- Dans  [Mise en cache de Windows Server AppFabric](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx)
    
  
- Dans une base de données
    
  
- Dans un système  [memcached](http://www.memcached.org/)
    
  
Si le stockage du cache est partagé entre plusieurs sessions d'utilisateurs, comme le cache de l'application, assurez-vous que vous utilisez une clé de cache unique, propre à la session. Si le cache est partagé entre plusieurs applications, votre code doit également **relativiser la clé de cache** pour cette variable. Il est également possible que votre complément accède à plusieurs batteries de serveurs SharePoint. Vous aurez besoin de jetons d'accès différents pour chacune d'elles et, dans ce scénario, votre clé de cache devra relativiser pour la batterie de serveurs. Au total, vous aurez besoin de clés de cache basées sur un ou plusieurs identificateurs d'utilisateurs, ID d'application et domaine SharePoint. Pensez à utiliser un système de clé de cache semblable à celui utilisé par les Compléments SharePoint qui utilisent le système d'autorisation à faible niveau de fiabilité. Pour en savoir plus, voir la section [Comprendre la clé de cache](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey). Pour résumer, il vous suffit de concaténer au moins l'un de ces trois identifiants (et éventuellement procéder au hachage du résultat en une chaîne plus courte) pour obtenir une clé de cache. 
  
    
    

### Gérer les jetons d'accès arrivés à expiration
<a name="CacheAccessToken"> </a>

 **Le jeton d'accès possède une heure d'expiration** que vous pouvez définir sur n'importe quelle valeur dans votre code. Si votre complément crée un jeton d'accès à chaque demande, chaque jeton doit être valable assez longtemps pour être validé par SharePoint, pas plus de quelques secondes à moins que le réseau local du client ne soit bloqué. Vous pouvez définir la date d'expiration dans plusieurs années, mais même dans un scénario où tout est en local, pour lequel les compléments à haut niveau de fiabilité sont conçus, il existe un risque de vol des jetons d'accès. C'est pour cette raison que nous vous recommandons de définir l'expiration de votre jeton quelques heures tout au plus après sa création. (Si vous utilisez du code managé, l'exemple de code de création de jeton dans le fichier TokenHelper.cs [ou .vb] définit l'expiration 12 heures plus tard.)
  
    
    
Si la session d'un utilisateur utilisant votre Complément SharePoint dure plus longtemps que la durée de vie du jeton d'accès mis en cache, la première demande à SharePoint après l'expiration du jeton générera une erreur **401 Unauthorized**. Votre code doit gérer cette réponse. Sinon, il peut tester l'heure d'expiration du jeton d'accès avant de l'utiliser. **Votre code doit répondre à un jeton d'accès arrivé à expiration en créant un autre jeton d'accès**, puis en répétant la demande qui a échoué.
  
    
    

## Comprendre la structure des jetons d'accès
<a name="Structure"> </a>

L'exemple suivant illustre **un jeton d'accès généré par un Complément SharePoint à haut niveau de fiabilité**. Plus précisément, ce jeton a été généré par l'exemple de code du fichier TokenHelper.cs (ou .vb) qui fait partie du modèle de projet de Complément SharePoint créé par les Outils de développement Office pour Visual Studio dans Visual Studio 2013 Update 2. Le jeton a été décodé et un espace blanc a été ajouté pour en faciliter la lecture. Les jetons d'accès utilisés dans le système à haut niveau de fiabilité sont conformes à [[MS-SPS2SAUTH]: OAuth 2.0 Authentication Protocol: SharePoint Profile](http://msdn.microsoft.com/library/05f9759b-0fa4-45ff-bd4b-0d7d254e7010.aspx), aussi appelé protocole serveur à serveur ou S2S. Ces informations visent à aider les développeurs utilisant du code managé à déboguer des Compléments SharePoint à haut niveau de fiabilité et à fournir quelques conseils sur la création des jetons aux développeurs utilisant d'autres langages.
  
    
    
 **Ce jeton d'accès est généré si le complément établit un appel avec SharePoint en utilisant la  [stratégie utilisateur + complément](add-in-authorization-policy-types-in-sharepoint-2013.md).** Si le complément utilise la [stratégie complément uniquement](add-in-authorization-policy-types-in-sharepoint-2013.md) et établit un appel complément uniquement avec SharePoint, le jeton acteur (qui est un jeton enfant compris dans le jeton d'accès utilisateur + complément, et dont la description est disponible ci-dessous) devient le jeton d'accès (il n'existe pas de jeton parent).
  
    
    

> [!REMARQUE]
>  N'oubliez pas que les jetons d'accès à haut niveau de fiabilité créés par votre code sont différents de ceux créés par Azure ACS si le système d'autorisation à faible niveau de fiabilité est utilisé :>  La revendication **alg** dans l'en-tête est « none », car le jeton d'accès d'un appel utilisateur + complément provenant d'un complément à haut niveau de fiabilité n'est pas signé.>  Dans cet exemple, l'URL de complément dans la valeur **aud** est celle d'un serveur local, ce qui est normal pour le système à haut niveau de fiabilité.>  Il n'existe aucune revendication **identityprovider**, mais un émetteur d'identité de nom **nii** qui a des valeurs semblables à celles des jetons d'accès de revendication **identityprovider** utilisés dans le système d'autorisation à faible niveau de fiabilité. (Pour en savoir plus sur cette valeur si le fournisseur d'identité est basé sur SAML, voir les articles de blog de Steve Peschka intitulés [Sécurité dans les compléments SharePoint (partie 8)](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx) et [Utilisation des compléments SharePoint avec des sites SAML et FBA dans SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx). >  Il n'existe aucune revendication **actor**, mais il existe une revendication **actortoken** qui contient un jeton interne encodé en base 64 d'une durée de vie de 12 heures.
  
    
    

L'en-tête a deux propriétés. La propriété « typ » est le type de jeton. Le code contenu dans l'application web distante doit toujours définir cette valeur sur « JWT ». La propriété « alg » est l'algorithme utilisé pour signer le jeton. Étant donné que le jeton externe d'un appel utilisateur + complément provenant d'un complément à haut niveau de fiabilité n'est pas signé, définissez cette valeur sur « none ». Pour en savoir plus sur les valeurs du corps du jeton d'accès à haut niveau de fiabilité, voir le tableau 1.
  
    
    



```

{"typ":"JWT", "alg":"none"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"s-1-5-21-2127521184-1604012920-1887927527-2963467",
 "nii":"urn:office:idp:activedirectory",
 "actortoken":"6sMZhbw … [remainder of long base 64 string omitted] … "
}

```

Le tableau suivant fournit quelques indications sur les **propriétés que votre code doit inclure dans le jeton d'accès et les valeurs à définir**. Si vous utilisez du code managé, les fichiers SharePointContext.cs (ou .vb) et TokenHelper.cs (ou .vb) créent les jetons à votre place. Par exemple, imaginez que votre code n'effectue qu'un seul appel à la méthode **SharePointContext.CreateUserClientContextForSPHost**. Celle-ci appelle à son tour des méthodes dans la classe **TokenHelper** qui créent le jeton d'accès qui est ensuite intégré à tous les appels à SharePoint par l'objet de contexte client SharePoint qui est renvoyé par la méthode **SharePointContext.CreateUserClientContextForSPHost**.
  
    
    

**Tableau 1 : revendications des jetons d'accès émis par l'application**


|**Revendication**|**Description**|**Valeur correspondante dans l'exemple de jeton d'accès**|
|:-----|:-----|:-----|
| `aud` <br/> |Abréviation du mot « audience ». Il représente le principal auquel est destiné le jeton. Le format est  _identifiant du principal d'audience_/ _domaine SharePoint complet_@ _domaine SharePoint_.  <br/> Le principal d'audience pour un Complément SharePoint est toujours 00000003-0000-0ff1-ce00-000000000000.  <br/> Étant donné que les Compléments SharePoint à haut niveau de fiabilité sont généralement utilisés dans un scénario entièrement local, le nom de domaine SharePoint complet est souvent simplement un nom de serveur.  <br/> Le  _domaine SharePoint_ est le GUID de la batterie de serveurs SharePoint locale dont le jeton d'accès est utilisé pour y accéder (ou le GUID de la location, si la batterie a été configurée pour les locations). <br/> Trouvez le domaine SharePoint en exécutant la cmdlet PowerShell **Get-SPAuthenticationRealm** sur le serveur SharePoint. Utilisez-le ensuite directement dans votre code ou stockez-le dans un fichier de configuration que votre code peut lire, tel que la section app.Settings d'un fichier web.config. Sinon, votre code peut découvrir le domaine SharePoint de manière dynamique lors de son exécution en envoyant une demande d'authentification à SharePoint. Pour obtenir un exemple de cette procédure en code managé, voir la méthode `GetRealmFromTargetUrl` dans le fichier TokenHelper.cs (ou .vb). <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |Abréviation du mot « issuer » (émetteur). Il représente le principal qui a créé le jeton. Le format est  _GUID d'émetteur_@ _GUID du domaine SharePoint_.  <br/> Dans le système à haut niveau de fiabilité, le complément est l'émetteur, c'est-à-dire que l'ID client du Complément SharePoint est généralement utilisé pour le GUID d'émetteur.  *Toutes les lettres de l'ID de l'émetteur doivent être en minuscules.*  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |Abréviation des mots « not before » (pas avant). Il représente l'heure à laquelle le jeton  *commence*  à être valide, en secondes depuis le 1er janvier 1970 à minuit. Votre code doit le définir sur le moment où le jeton est créé. <br/> |1403212820  <br/> |
| `exp` <br/> |Abréviation du mot « expiration ». Il représente l'heure d'expiration du jeton, en secondes depuis le 1er janvier 1970 à minuit.  <br/> |1403256020  <br/> |
| `nameid` <br/> |Un identifiant unique pour l'utilisateur pour qui le jeton est émis. Le format varie selon le fournisseur d'identité. Dans cet exemple, le fournisseur est Active Directory.  <br/> |s-1-5-21-2127521184-1604012920-1887927527-2963467  <br/> |
| `nii` <br/> |Abréviation de « name identifier issuer » (émetteur d'identifiant de nom). Nom unique du fournisseur d'identité tel qu'inscrit auprès de l'organisme IANA (Internet Assigned Numbers Authority).  <br/> Pour un Complément SharePoint à haut niveau de fiabilité, il s'agit généralement d'un fournisseur d'identité local, tel qu'Active Directory dans cet exemple.  <br/> |urn:office:idp:activedirectory  <br/> |
| `actortoken` <br/> |Un jeton JWT encodé en base 64 qui identifie le Complément SharePoint et demande à SharePoint d'approuver le complément, quel que soit l'utilisateur qui exécute le complément.  <br/> |Voir ci-dessous.  <br/> |
   
 **L'exemple suivant montre un **actortoken** décodé.** Le petit objet d'en-tête JavaScript Object Notation (JSON) en haut contient des métadonnées sur le jeton, y compris le type de jeton et l'algorithme utilisé pour le signer. La propriété **x5t** de l'en-tête est un chiffrement effectué à partir de l'empreinte numérique du certificat x.509 qui est officiellement l'émetteur du jeton. Pour créer cette valeur, votre code suit les étapes ci-dessous :
  
    
    

1. Obtention de l'empreinte du certificat sous la forme d'un tableau d'octets (pas de chaînes). Il s'agit d'un chiffrement SHA-1 du certificat. (Il est possible de le faire en code managé à l'aide de la méthode  [GetCertHash()](http://msdn2.microsoft.com/FR-FR/library/4f9acc3f). Vous aurez besoin d'un équivalent dans le langage que vous utilisez.)
    
  
2. Encodage du tableau d'octets en URL base 64.
    
  
3. Définition de la valeur de la propriété **x5t** sur le chiffrement encodé.
    
  
Le tableau 2 décrit les revendications que votre code doit intégrer dans le corps du jeton et les valeurs à définir pour celles-ci.
  
    
    



```

{"typ":"JWT","alg":"RS256","x5t":"7MjK99QvkVdwz6UrKldx8AG7ydM"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "trustedfordelegation":"true"
}

```


> [!REMARQUE]
> Si le complément à haut niveau de fiabilité utilise la  [stratégie complément uniquement](add-in-authorization-policy-types-in-sharepoint-2013.md) et effectue un appel complément uniquement à SharePoint, le jeton indiqué ici sert de jeton d'accès. Il n'existe pas de jeton externe ni de réclamation **trustedfordelegation**, car les autorisations de l'utilisateur ne sont pas pertinentes pour un appel complément uniquement. 
  
    
    


**Tableau 2 : revendications actortoken émises par le certificat**


|**Revendication**|**Description**|**Valeur correspondante dans l'exemple de jeton d'accès**|
|:-----|:-----|:-----|
| `aud` <br/> |Même description que pour le jeton d'accès parent.  <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |Même signification que pour le jeton d'accès parent, mis à part que le GUID d'émetteur n'est pas l'ID client de l'application web, mais le GUID du certificat. Même si le code dans l'application crée le jeton acteur, le certificat est considéré comme l'émetteur du jeton acteur.  <br/> Notez que dans cet exemple, le GUID d'émetteur est une chaîne GUID facile à retenir qui a été utilisée par l'administrateur de la batterie de serveurs au moment de l'inscription du certificat x.509 en tant qu'émetteur de jeton approuvé dans SharePoint. Il s'agit d'un procédé courant lorsque le même certificat est utilisé comme émetteur de jeton acteur pour tous les Compléments SharePoint à haut niveau de fiabilité de la batterie. Un administrateur peut aussi faire le choix d'avoir des certificats distincts pour chaque Complément SharePoint. Dans ce cas, il utiliserait différents GUID générés de manière aléatoire pour les certificats.  *Toutes les lettres du GUID d'émetteur doivent être en minuscules.*  <br/> |11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |Même signification que pour le jeton d'accès parent.  <br/> |Même valeur que pour le jeton d'accès parent.  <br/> |
| `exp` <br/> |Même signification que pour le jeton d'accès parent.  <br/> |Même valeur que pour le jeton d'accès parent.  <br/> |
| `nameid` <br/> |Un identifiant unique pour le Complément SharePoint, car il s'agit de l'« acteur » du système à haut niveau de fiabilité. Le format est  _ID_client_@ _domaine_SharePoint_.  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `trustedfordelegation` <br/> |Une valeur booléenne indiquant si SharePoint doit approuver le Complément SharePoint pour authentifier et autoriser l'utilisateur. Ceci est généralement vrai dans le système à haut niveau de fiabilité.  <br/> N'intégrez pas cette revendication dans un appel complément uniquement dans le système à haut niveau de fiabilité.  <br/> |true  <br/> |
   

## Encoder et signer des jetons
<a name="EncodeTokens"> </a>

 **Après que votre code a ajouté toutes les propriétés et valeurs à l'en-tête et au corps des objets JSON, il doit les encoder, les combiner pour former un jeton web JSON (JWT) et signer ce dernier.** Voici les différentes étapes.
  
    
    

1. Encodez l'en-tête en URL base 64.
    
  
2. Encodez la charge utile en URL base 64.
    
  
3. Concaténez le corps encodé après l'en-tête encodé en insérant le caractère « . » entre les deux. Vous avez votre JWT.
    
  
4. Créez une signature SHA256 en utilisant le JWT et la clé privée du certificat.
    
  
5. Encodez la signature en URL base 64.
    
  
6. Ajoutez la signature à la fin du JWT en insérant le caractère « . » entre les deux. 
    
  
Si un jeton acteur est utilisé lors d'un appel complément uniquement, il joue également le rôle de jeton d'accès. Il n'existe pas de jeton externe. Si un jeton d'accès est utilisé lors d'un appel utilisateur + complément, les étapes précédentes sont utilisées pour créer un jeton acteur qui est ensuite inséré dans le corps d'un jeton d'accès en tant que valeur de la propriété **actortoken**. Le jeton d'accès complet est alors encodé et créé à l'aide des trois étapes susmentionnées, mais n'est pas signé. Les étapes restantes ne sont pas utilisées pour le jeton externe.
  
    
    

## Résoudre les problèmes relatifs aux jetons d'accès
<a name="Trouble"> </a>

L' [outil gratuit Fiddler](http://www.telerik.com/fiddler) peut être utilisé pour capturer les demandes HTTP envoyées par le composant distant de votre complément à SharePoint. Il existe une [extension gratuite de l'outil ](https://github.com/andrewconnell/SPOAuthFiddlerExt) qui décode automatiquement les jetons dans les demandes.
  
    
    

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Création de compléments pour SharePoint qui utilisent l'autorisation à haut niveau de fiabilité](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)
    
  
- Article de blog de Steve Peschka intitulé  [Sécurité dans les compléments SharePoint (partie 7)](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-7.aspx)
    
  
- Article de blog de Steve Peschka intitulé  [Sécurité dans les compléments SharePoint (partie 8)](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)
    
  
- Article de blog de Steve Peschka intitulé  [Utilisation des compléments SharePoint avec des sites SAML et FBA dans SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)
    
  
- Article de blog de Kirk Evan intitulé  [Compléments SharePoint à haut niveau de fiabilité sur les plateformes autres que Microsoft](http://blogs.msdn.com/b/kaevans/archive/2014/07/14/high-trust-sharepoint-apps-on-non-microsoft-platforms.aspx)
    
  
-  [OAuth 2.0](http://oauth.net/2/)
    
  

