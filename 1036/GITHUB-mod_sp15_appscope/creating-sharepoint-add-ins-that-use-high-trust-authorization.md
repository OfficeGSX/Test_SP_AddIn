---
title: Création de compléments pour SharePoint qui utilisent l'autorisation à haut niveau de fiabilité
ms.prod: SHAREPOINT
ms.assetid: 135bf6be-b118-4d2c-8d40-27f5060b06f3
---


# Création de compléments pour SharePoint qui utilisent l'autorisation à haut niveau de fiabilité
Découvrez les Compléments SharePoint à haut niveau de fiabilité, qui utilisent des certificats numériques pour établir la sécurité entre SharePoint et les composants distants qui accèdent à SharePoint.
 **Auteurs :**



Steve Peschka, Microsoft Corporation



Siew Moi Khor, Microsoft Corporation





**Regardez une vidéo sur la création de compléments SharePoint à haut niveau de fiabilité.**








![Vidéos](images/mod_icon_video.png)












## Vue d'ensemble des Compléments SharePoint à haut niveau de fiabilité
<a name="Overview"> </a>

Un complément à haut niveau de fiabilité est un Complément SharePoint hébergé par un fournisseur qui est installé sur une batterie de serveurs SharePoint. Il ne peut pas être installé sur Microsoft SharePoint Online ou commercialisé sur l'Office Store. Pour établir une connexion sécurisée, un complément à haut niveau de fiabilité utilise un certificat au lieu d'un jeton de contexte.




> **REMARQUE**
>  Cette rubrique vous aide à comprendre le système d'autorisation à haut niveau de fiabilité pour les Compléments SharePoint. Pour obtenir des informations pratiques sur la création et le déploiement de compléments à haut niveau de fiabilité, consultez les rubriques suivantes :>  [Créer des compléments à haut niveau de fiabilité pour SharePoint 2013](create-high-trust-sharepoint-add-ins.md)>  [Empaquetage et publication de compléments SharePoint à haut niveau de fiabilité](package-and-publish-high-trust-sharepoint-add-ins.md)




Dans SharePoint 2013, le service d'émission de jeton de sécurité (STS) fournit des jetons d'accès pour l'authentification de serveur à serveur. Le STS active des jetons d'accès temporaires permettant d'accéder à d'autres services d'application, tels que Exchange 2013, Lync 2013 et les Compléments SharePoint. Un administrateur de batterie établit une connexion sécurisée entre SharePoint et l'autre application ou complément au moyen des applets de commande Windows PowerShell et d'un certificat. Chaque certificat utilisé doit être approuvé par SharePoint 2013 à l'aide de l'applet de commande  `New-SPTrustedRootAuthority`. Par ailleurs, chaque certificat doit être inscrit auprès de SharePoint en tant qu'émetteur de jeton à l'aide de l'applet de commande  `New-SPTrustedSecurityTokenIssuer`.




> **REMARQUE**
> Le service STS n'est pas destiné à l'authentification des utilisateurs. C'est pour cette raison que ce service ne figure pas sur la page de connexion de l'utilisateur, dans la section **Fournisseur d'authentification** sur le site Administration centrale ou dans le sélecteur de personnes dans SharePoint 2013.





## Deux types d'émetteurs de jeton
<a name="TwoKindsOfIssuers"> </a>

L'application web distante d'un Complément SharePoint à haut niveau de fiabilité est liée à un certificat numérique. (Pour plus d'informations sur la façon de procéder, consultez les deux rubriques dont les liens se trouvent ci-dessus.) Le certificat est utilisé par l'application web distante afin de signer les jetons d'accès qu'elle inclut dans toutes ses demandes à SharePoint. L'application web signe le jeton avec la clé privée du certificat et SharePoint la valide avec la clé publique du certificat. Le certificat doit être inscrit auprès d'un émetteur de jeton approuvé avant que SharePoint n'approuve les jetons qu'il émet. Il existe deux types d'émetteur de jeton ; certains peuvent seulement émettre des jetons pour un Complément SharePoint particulier, et d'autres, appelés services Broker de sécurité, peuvent émettre des jetons pour plusieurs Compléments SharePoint.



En pratique, les administrateurs de batterie de serveurs SharePoint déterminent le type d'émetteur de jeton créé par les commutateurs et les valeurs des paramètres utilisées avec l'applet de commande `New-SPTrustedSecurityTokenIssuer`. Pour créer un émetteur de jeton qui agit en tant que service Broker de sécurité, ajoutez le commutateur  `-IsTrustBroker` à la ligne de commande et utilisez une valeur unique, autre que l'ID de client d'un complément, pour le paramètre `-Name`. Ce qui suit est un exemple de modification.





```

New-SPTrustedSecurityTokenIssuer -IsTrustBroker -RegisteredIssuerName "<full_token_issuer_name> " --other parameters omitted--```

Pour créer un émetteur de jeton non broker, le commutateur  `-IsTrustBroker` n'est pas utilisé. Il existe une autre différence. La valeur du paramètre `-RegisteredIssuerName` se présente toujours sous la forme de deux GUID séparés par le caractère « @ » : _GUID_@ _GUID_. Le GUID du côté droit est toujours l'ID du domaine d'authentification de la batterie de serveurs SharePoint (ou d'abonnement au site). Le GUID du côté gauche est toujours un ID propre à un émetteur de jeton. Il s'agit d'un GUID aléatoire lors de la création d'un émetteur de jeton de type  *service Broker de sécurité*  . Cependant, lorsqu'un émetteur de jeton non broker est créé, le GUID de l'émetteur spécifique doit être le même que celui utilisé en tant qu'ID de client du Complément SharePoint. Ce paramètre fournit un nom pour l'émetteur. Il indique également à SharePoint le seul Complément SharePoint pour lequel le certificat peut émettre des jetons. Ce qui suit est un exemple partiel :





```
$fullIssuerIdentifier = "<client_ID_of_SP_app> " + "@" + "<realm_GUID> "
New-SPTrustedSecurityTokenIssuer -RegisteredIssuerName $fullIssuerIdentifier --other parameters omitted--```

Généralement, l'applet de commande  `New-SPTrustedSecurityTokenIssuer` est utilisée dans un script qui effectue d'autres tâches pour configurer les compléments SharePoint à haut niveau de fiabilité. Pour plus d'informations sur ces scripts et des exemples complets de l'applet de commande `New-SPTrustedSecurityTokenIssuer`, voir  [Scripts de configuration à haut niveau de fiabilité pour SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).




## Décision entre l'utilisation d'un seul certificat ou de plusieurs certificats pour les Compléments SharePoint à haut niveau de fiabilité.
<a name="Deciding"> </a>

SharePoint et l'administrateur de réseau proposent deux stratégies lors de l'obtention et la gestion de certificats pour une utilisation par des Compléments SharePoint à haut niveau de fiabilité :




- Utiliser le même certificat pour plusieurs Compléments SharePoint.


- Utiliser un certificat distinct pour chaque Complément SharePoint.


Cet article aborde les avantages et les inconvénients de chaque stratégie.



L'avantage d'utiliser le même certificat pour plusieurs Compléments SharePoint tient au fait que l'administrateur a moins de certificats à approuver et à gérer. L'inconvénient de cette approche est que, lorsque vous voulez briser la relation d'approbation avec un Complément SharePoint, la seule façon de le faire pour l'administrateur est de retirer au certificat le rôle d'émetteur de jeton ou d'autorité racine. Toutefois, la suppression du certificat brise également la relation d'approbation avec tous les autres Compléments SharePoint qui utilisent le même certificat ; ils cessent alors de fonctionner.



Pour que les Compléments SharePoint toujours approuvés fonctionnent à nouveau, l'administrateur devra créer un objet  `New-SPTrustedSecurityTokenIssuer` *en utilisant un nouveau certificat*  et inclure l'indicateur `-IsTrustBroker`. Ensuite, il devra inscrire le nouveau certificat auprès de chaque serveur qui héberge l'un des compléments approuvés et chacun de ces compléments devra être associé au nouveau certificat.



L'avantage d'utiliser un certificat distinct pour chaque complément tient au fait qu'il est plus facile de briser la relation d'approbation avec un complément particulier, car les certificats utilisés par les compléments toujours approuvés ne sont pas concernés quand l'administrateur brise la relation d'approbation avec le certificat d'un complément. L'inconvénient de cette stratégie est que l'administrateur doit acquérir plusieurs certificats et que SharePoint doit être configuré pour utiliser chacun d'entre eux, ce qui peut être fait avec un script réutilisable comme indiqué dans  [Scripts de configuration à haut niveau de fiabilité pour SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).




## Les certificats sont des autorités racines dans SharePoint
<a name="RootAuthorities"> </a>

Comme brièvement mentionné au début de cet article, les administrateurs de batterie de serveurs SharePoint doivent faire du certificat (relatif à l'application web distante dans le complément à haut niveau de fiabilité) une autorité racine approuvée dans SharePoint, ainsi qu'un émetteur de jeton approuvé. En fait, lorsqu'une hiérarchie d'autorités de délivrance de certificat se trouve derrière le certificat de l'application web, tous les certificats de la chaîne doivent être ajoutés à la liste des autorités racines approuvées de SharePoint.



Chaque certificat de la chaîne est ajouté à la liste des autorités racines approuvées de SharePoint avec un appel de l'applet de commande  `New-SPTrustedRootAuthority`. Par exemple, supposons que le certificat de l'application web est un certificat de domaine délivré par une autorité de certification du domaine d'entreprise sur le réseau local, et supposons que son certificat, à son tour, a été publié par une autorité de certification autonome de niveau supérieur sur le réseau local. Dans ce cas, les certificats de l'autorité de certification de niveau supérieur, de l'autorité de certification intermédiaire, ainsi que de l'application web doivent tous être ajoutés à la liste des autorités racines approuvées de SharePoint. Le code Windows PowerShell suivant se charge d'accomplir cette tâche.





```

$rootCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<path_to_top-level_CA's_cer_file>")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $rootCA

$intermediateCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_intermediate_CA's_cer_file")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $intermediateCA

$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_web_application's_cer_file") 
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $certificate 
```

Les certificats racines et intermédiaires ne doivent être ajoutés qu'une seule fois sur une batterie de serveurs SharePoint. En règle générale, le certificat de l'application web est ajouté dans un script séparé qui procède également à d'autres configurations, comme l'appel de  `New-SPTrustedSecurityTokenIssuer`. Pour des exemples, voir  [Scripts de configuration à haut niveau de fiabilité pour SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).



Si le certificat de l'application web est auto-signé, comme cela pourrait être le cas lorsque le complément est en cours de développement et de débogage, il n'y a pas de chaîne de certificats et seul le certificat de l'application web doit être ajouté à la liste des autorités racines.



Pour plus d'informations, voir le billet de blog concernant l' [erreur liée à une racine de chaîne de certificats non approuvée avec l'authentification basée sur les revendications](http://blogs.technet.com/b/speschka/archive/2010/02/13/root-of-certificate-chain-not-trusted-error-with-claims-authentication.aspx). (Faites défiler la page jusqu'après la section sur l'exportation du certificat à partir d'Active Directory Federation Services (AD FS), à condition que vous ayez créé le certificat de vos compléments à niveau de fiabilité élevé par d'autres moyens, notamment en utilisant un certificat commercial émis par une autorité de certification, un certificat auto-signé ou un certificat émis par un domaine.)




## L'application web a besoin de savoir qu'il s'agit d'un émetteur de jeton
<a name="AppIsTokenIssuer"> </a>

Le composant d'application web distante du Complément SharePoint est lié à son certificat dans IIS. Cela suffit pour remplir les fonctions classiques des certificats : identifier l'application web en toute sécurité, et coder les demandes et les réponses HTTP. Cependant, dans un Complément SharePoint à haut niveau de fiabilité, le certificat a la tâche supplémentaire d'être l'« émetteur » officiel des jetons d'accès que l'application web envoie à SharePoint. À cet effet, l'application web doit connaître l'ID de l'émetteur du certificat qui est utilisé lors de l'inscription auprès de SharePoint du certificat en tant qu'émetteur de jeton. L'application web obtient cet ID dans la section **appSettings** du fichier web.config, où se trouve une clé nommée **IssuerId**. Des instructions sur la façon dont le développeur du complément définit cette valeur, ainsi que sur la manière dont le certificat est lié à l'application web dans IIS, figurent dans  [Empaquetage et publication de compléments SharePoint à haut niveau de fiabilité](package-and-publish-high-trust-sharepoint-add-ins.md). Notez que si le client opte pour la stratégie impliquant un certificat distinct pour chaque Complément SharePoint à haut niveau de fiabilité, la valeur **ClientId** est également utilisée en tant que valeur **IssuerId**. Ce n'est pas le cas lorsque plusieurs compléments partagent le même certificat, car chaque Complément SharePoint doit avoir un ID de client unique, mais l'ID de l'émetteur est l'identifiant d'un objet **SPTrustedSecurityTokenIssuer**.



Voici un exemple de section **appSettings** pour un Complément SharePoint à haut niveau de fiabilité. Dans cet exemple, plusieurs compléments partagent un certificat, de sorte que l'élément **IssuerId** est différent de l'élément **ClientId**. Notez qu'il n'y a pas de clé **ClientSecret** dans un Complément SharePoint à haut niveau de fiabilité.





```XML

<appSettings>
  <add key="ClientId" value="6569a7e8-3670-4669-91ae-ec6819ab461" />
  <add key="ClientSigningCertificatePath" value="C:\\MyCerts\\HighTrustCert.pfx" />
  <add key="ClientSigningCertificatePassword" value="3VeryComplexPa$$word82" />
  <add key="IssuerId" value="e9134021-0180-4b05-9e7e-0a9e5a524965" />
</appSettings>
```


> **REMARQUE DE SéCURITé**
> L'exemple qui précède suppose que le certificat est stocké dans le système de fichiers. Ceci est acceptable à des fins de développement et de débogage. Dans un Complément SharePoint à haut niveau de fiabilité de production, le certificat est généralement stocké dans le magasin de certificats Windows, et les clés **ClientSigningCertificatePath** et **ClientSigningCertificatePassword** sont généralement remplacées par une clé **ClientSigningCertificateSerialNumber**. 





## Responsabilités du personnel informatique dans le système à haut niveau de fiabilité
<a name="ITPro"> </a>

Les développeurs doivent comprendre les exigences relatives à la sécurité des applications décrites ci-dessus, mais ce sont les professionnels de l'informatique qui devront implémenter l'infrastructure nécessaire pour prendre en charge ces exigences. Ils doivent donc prévoir les éléments suivants :




- Créer ou acheter un ou plusieurs certificats qui seront utilisés pour les Compléments SharePoint approuvés.


- Veiller à ce que les certificats soient stockés de façon sécurisée sur les serveurs d'applications web. Avec le système d'exploitation Windows, il doivent être stockés dans le magasin de certificats Windows.


- Gérer la distribution de ces certificats aux développeurs qui créent des Compléments SharePoint.


- Suivre la distribution de chaque certificat, tant pour les compléments qui l'utilisent que pour les développeurs qui en ont reçu une copie. Si un certificat doit être révoqué, le personnel informatique doit contacter chaque développeur pour organiser une transition coordonnée vers un nouveau certificat.



## Ressources supplémentaires
<a name="AR"> </a>


-  [Créer des compléments à haut niveau de fiabilité pour SharePoint 2013](create-high-trust-sharepoint-add-ins.md)


-  [Empaquetage et publication de compléments SharePoint à haut niveau de fiabilité](package-and-publish-high-trust-sharepoint-add-ins.md)


-  [Conseils supplémentaires pour dépanner les compléments à haut niveau de fiabilité sur SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)


-  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)



