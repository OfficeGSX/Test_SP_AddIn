---
title: Enregistrer des compléments dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: be41a5dc-2af9-4fd9-bf4e-ad6dfa849524
---



# Enregistrer des compléments dans SharePoint 2013
Inscrivez vos Compléments SharePoint dans Azure ACS à l'aide de Visual Studio, du Mon tableau de bord vendeur ou d'une page AppRegNew.aspx, puis extrayez les informations d'inscription.
Pour que les composants distants d'un Complément SharePoint hébergé par un fournisseur interagissent avec SharePoint à l'aide d'OAuth, le complément doit tout d'abord être inscrit auprès du service basé sur le cloud  [Azure ACS](https://msdn.microsoft.com/fr-fr/library/azure/gg429788.aspx) et du service de gestion des applications SharePoint basé sur le cloud. (Le nom « service de gestion des applications » vient du fait que les Compléments SharePoint étaient à l'origine appelés « applications pour SharePoint ».)
  
    
    


> [!REMARQUE]
> Cela n'est pas obligatoire pour les compléments hébergés par SharePoint. 
  
    
    


Pour inscrire votre complément auprès de Azure ACS, indiquez les informations suivantes :
  
    
    


- Le GUID du complément, appelé ID client.
    
  
- Le mot de passe du complément, appelé clé secrète client.
    
  
- Le nom d'affichage du complément qui apparaît sur la page de consentement lorsque l'utilisateur est invité à approuver le complément.
    
  
- L'URL du domaine dans lequel le complément distant est hébergé.
    
  
- L'URL de redirection.
    
  
Une fois que vous avez inscrit votre complément, celui-ci possède une identité de complément et devient un  *principal de sécurité*  , appeléprincipal de complément. Par la suite, une fois le complément installé, les administrateurs SharePoint peuvent récupérer les informations relatives à ce principal de complément en particulier.Lorsqu'un utilisateur attribue initialement à un complément des autorisations d'accès aux ressources SharePoint (soit lors de l'installation, soit lors de son exécution, en fonction de la conception de l'application), SharePoint récupère des informations sur le complément à partir d'Azure ACS. SharePoint stocke ensuite ces informations dans la base de données du service de gestion des applications de la location ou de la batterie de serveurs SharePoint, à l'exception de la clé secrète client, qui est uniquement stockée dans Azure ACS. SharePoint n'a jamais connaissance de la clé secrète du complément. Le service de base de données de contenu et les autres composants, tels que le service de profil utilisateur, peuvent directement obtenir le nom d'affichage d'un complément et d'autres informations de base auprès du service partagé de gestion des applications. Pour en savoir plus, voir la section  [Récupération des informations d'inscription du complément et des informations du principal de complément ](register-sharepoint-add-ins-2013.md#Retrieve) dans cet article.
> [!REMARQUE]
> Cet article suppose que vous connaissez déjà les concepts et principes fondamentaux sur lesquels se fonde OAuth 2.0 Framework. Pour plus d'informations, voir  [OAuth.net](http://oauth.net/) et [Protocole d'autorisation web (oauth)](http://datatracker.ietf.org/doc/active/). 
  
    
    


## Inscription de votre Complément SharePoint auprès d'Azure ACS

Vous pouvez inscrire votre complément pour obtenir son identité de trois façons différentes. La méthode que vous choisissez dépend de votre avancement dans le cycle de développement du complément, de l'architecture de votre complément et de son mode de commercialisation prévu.
  
    
    


|**Méthode d'inscription**|**Détails**|
|:-----|:-----|
|Utilisez Visual Studio et Outils de développement Microsoft Office pour Visual Studio pour créer une identité de complément temporaire.  <br/> |L'Assistant Outils de développement Office pour Visual Studio inscrit temporairement votre complément auprès du service ACS et du service de gestion d'applications de votre site web de test SharePoint. Lorsque vous exécutez le complément à partir de Visual Studio (F5), cette identité est utilisée. Les outils insèrent également l'ID client et la clé secrète client dans les fichiers web.config et AppManifest.xml.  <br/> Lorsque vous êtes prêt à publier votre complément, vous pouvez utiliser l'Assistant de publication Visual Studio pour accéder à Mon tableau de bord vendeur et effectuer l'inscription. Si vous ne commercialisez pas votre Complément SharePoint dans l'Office Store, utilisez la page AppRegNew.aspx pour inscrire l'application. (Les étapes exactes sont décrites ci-dessous.)  <br/> > [!REMARQUE]> Si votre complément demande l'autorisation d'accéder aux ressources SharePoint dynamiquement au moment de l'exécution plutôt que lors de l'installation, vous ne pouvez pas utiliser Visual Studio pour créer des identités de complément.           |
|Inscrivez l'application par le biais de Mon tableau de bord vendeur.  <br/> |Si vous envisagez d'utiliser votre complément dans plusieurs batteries de serveurs ou clients SharePoint, passez par Mon tableau de bord vendeur pour inscrire votre complément, que vous souhaitiez ou non le commercialiser dans le Office Store ou le rendre disponible via le catalogue de compléments. Lorsque vous l'inscrivez dans le Mon tableau de bord vendeur, vous pouvez concevoir votre complément avec une architecture à plusieurs clients sans nécessiter que les administrateurs de clients ou de batteries de serveurs s'inscrivent séparément. En outre, si vous prévoyez de publier votre complément dans le Office Store, vous devez l'inscrire via Mon tableau de bord vendeur. Vous n'avez pas à utiliser le magasin pour publier un complément déjà inscrit auprès de Mon tableau de bord vendeur.  <br/> Pour plus d'informations, voir  [Création ou mise à jour d'ID et de clés secrètes client dans le service Mon tableau de bord vendeur](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx).  <br/> |
|Utilisez la page AppRegNew.aspx.  <br/> |Utilisez le formulaire AppRegNew pour inscrire votre Complément SharePoint si vous envisagez d'utiliser le complément uniquement dans un client ou dans une batterie de serveurs. Par exemple, si vous créez des compléments pour une organisation et que vous allez les distribuer via le catalogue de compléments de l'organisation, vous pouvez utiliser la page AppRegNew.aspx d'un site web dans un client ou une batterie de serveurs pour inscrire le complément.  <br/> Vous ne pouvez pas publier un complément inscrit via la page AppRegNew.aspx dans le Office Store. Pour les compléments publiés dans le Office Store, vous devez obtenir une identité à partir du Mon tableau de bord vendeur.  <br/> |
   

### Inscrire une application via la page AppRegNew.aspx


1.  Accédez à `http://` *<SiteWebSharePoint>*  `/_layouts/15/AppRegNew.aspx` sur le client ou la batterie de serveurs.
    
   **Formulaire de page AppRegNew**

  

     ![Formulaire sur la page App Reg New avec des zones de texte pour l'ID client, la clé secrète client, le titre, le domaine d'application et l'URL de redirection. Les boutons « générés » se trouvent à côté des deux premiers. Les boutons Créer et Annuler se trouvent dans le coin.](images/9a38d876-2189-418c-9314-ae493a4cab61.PNG)
  

  

  
2. Renseignez les champs de formulaire suivants :
    
  - **ID du complément**: également appelé ID client. Il s'agit d'un GUID qui peut être généré (si vous choisissez **Générer**) ou collé dans AppRegNew.aspx. La valeur doit être unique pour chaque complément et  *doit être en minuscules*  .
    
  
  - **Secret du complément**: également appelé clé secrète client. Il s'agit d'une chaîne opaque. Elle est générée sur la page AppRegNew.aspx à l'aide du bouton **Générer**. Vous trouverez ci-après un exemple de secret de complément : **xvVpG0AgVIJfch6ldu4dLUlcZyysmGqBRbpFDu6AfJw=**.
    
    > [!IMPORTANTE]
      > Les clés secrètes de complément ont une durée de validité. Si vous inscrivez le complément auprès de Mon tableau de bord vendeur, vous pouvez définir sa date d'expiration jusqu'à 3 ans plus tard. Dans le tableau de bord, vous pouvez également ajouter de nouvelles clés secrètes lorsque les anciennes arrivent à expiration. La nouvelle clé secrète sera activée dans toutes les instances du complément. Si vous inscrivez votre complément à l'aide d'AppRegNew.aspx, la clé secrète expire un an plus tard. Pour en savoir plus, voir  [Remplacement d'une clé secrète client arrivant à expiration dans un complément pour SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md). 
  - **Titre**: titre convivial (par exemple,Complément d'impression de photos Contoso). Les utilisateurs sont invités à accorder ou refuser des autorisations lorsque le complément en demande. Ce titre s'affiche en tant que nom du complément à l'invite de consentement. 
    
  
  - **Domaine de complément**: nom d'hôte du composant distant du Complément SharePoint. Si l'application distante n'utilise pas le port 443, le domaine de complément doit également inclure le numéro de port. Le domaine de complément doit correspondre aux liaisons URL que vous utilisez pour votre application web. N'incluez pas de protocole (« https: ») ni de caractères « / » dans cette valeur. Si votre hôte d'application web a recours à un alias CNAME DNS, utilisez-le. Voici quelques exemples :
    
  - www.contoso.com:3333
    
  
  - www.fabrikam.com
    
  
  - **URI de redirection**: point de terminaison dans votre service ou application distante, auquel le service ACS envoie un code d'authentification. En principe, les Compléments SharePoint n'utilisent pas cette valeur. L'URI de redirection est requis pour les applications web lancées en dehors de SharePoint et qui utilisent le [flux de code d'authentification](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) pour obtenir l'accès aux données SharePoint. L'URI de redirection est ignoré pour les véritables Compléments SharePoint (lancés à partir de SharePoint et utilisant le [flux de jeton de contexte](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)). L'URI de redirection est généralement la même page, méthode du contrôleur ou méthode de service web qui a demandé le code d'authentification au service ACS, mais il peut s'agir d'un autre point de terminaison. Le point de terminaison doit avoir une logique qui obtient le code d'autorisation à partir de la réponse HTTP envoyée par le service ACS, puis utilise ce code pour demander un jeton d'accès et d'actualisation. Pour plus d'informations, voir  [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md). Vous devez saisir une valeur valide dans le formulaire, même pour les véritables Compléments SharePoint, bien qu'elle ne soit pas utilisée.
    
    La valeur doit être une URL de point de terminaison complète, avec le protocole  *HTTPS obligatoirement*  . Exemples :
    
  - https://www.contoso.com/Default.aspx
    
  
  - https://www.fabrikam.com/RedirectAccept.aspx
    
  
  - https://www.northwindtraders.com/home/index
    
  
  - https://adventureworks.com/vacationdata.svc
    
  
3. Cliquez sur **Créer** dans le formulaire. La page est rechargée et affiche une confirmation des valeurs que vous avez saisies. Notez bien ces valeurs dans un format facile à copier et coller. En effet, vous allez devoir les saisir dans les fichiers web.config et AppManifest.xml ou dans l'Assistant **Publication** de Visual Studio.
    
  
Quelle que soit la manière dont vous inscrivez votre Complément SharePoint, lorsque vous êtes prêt à déployer le complément dans un environnement intermédiaire ou de production, vous devez  [Saisie des valeurs d'inscription dans les fichiers web.config et AppManifest.xml](#EditConfigFiles). Si vous utilisez Visual Studio, les Outils de développement Microsoft Office pour Visual Studio effectuent cette configuration pour vous.
  
    
    

## Saisie des valeurs d'inscription dans les fichiers web.config et AppManifest.xml
<a name="EditConfigFiles"> </a>

Avant d'inclure le Complément SharePoint dans un package et de déployer ses composants distants, entrez une partie des valeurs d'inscription dans les fichiers AppManifest.xml et web.config.
  
    
    

> [!CONSEIL]
> Si vous publiez votre Complément SharePoint à l'aide de l'Assistant Publication de Visual Studio, Visual Studio vous invite à entrer un ID client et une clé secrète client au cours du processus de publication, puis insère ces informations aux emplacements appropriés à votre place. 
  
    
    


1. Dans le fichier web.config de votre projet Visual Studio, entrez la valeur d'ID du complément pour la valeur de **ClientId** (elle remplace la valeur temporaire entrée par les outils).
    
    > [!IMPORTANTE]
      > Toutes les lettres du GUID de l'ID client doivent être en minuscules. 

    Voici un exemple :
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
   .  .  .
</appSettings>
  ```

2. Entrez la valeur de la clé secrète du complément pour la valeur de **ClientSecret** (elle remplace la valeur temporaire entrée par les outils).
    
    L'exemple suivant illustre l'utilisation des valeurs dans le fichier Web.config d'une application web.
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
  <add key="ClientSecret" value="l0z/8TzWN0yQBzMBSEZtYts2Vt3Eo/oE3rfCdPaogKQ= " />
</appSettings>
  ```

3. Dans le fichier AppManifest.xml de votre projet Visual Studio, entrez la valeur de l'ID du complément pour la valeur de **ClientId**,  *en minuscules*  .
    
    > [!REMARQUE]
      > Le manifeste du complément n'est pas applicable aux applications web qui demandent l'autorisation d'accéder aux ressources SharePoint à la volée. Il ne s'agit pas vraiment de « Compléments SharePoint ». Ils ne sont pas installés sur SharePoint et n'ont pas de manifeste de complément. Pour en savoir plus, voir  [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md). 

    L'exemple suivant illustre l'utilisation de la valeur **ClientId** dans le fichier AppManifest.xml.
    


  ```XML
  
<AppPrincipal>
  <RemoteWebApplication ClientId="a044e184-7de2-4d05-aacf-52118008c44e "/>
</AppPrincipal>
  ```

4. Les Outils de développement Office pour Visual Studio utilisent le jeton  `~remoteAppUrl` dans l'élément **StartPage**. (Par exemple :  `<StartPage>~remoteAppUrl/Pages/Default.aspx?{StandardTokens}</StartPage>`). Ce jeton résout l'URL du composant distant si vous utilisez l'Assistant **Publication** dans Visual Studio. Si vous n'utilisez pas l'Assistant (ou si vous l'utilisez, mais publiez le composant distant dans Azure), vous devez remplacer manuellement le jeton par la valeur **Domaine de complément** que vous avez utilisée lors de l'inscription du complément. Il doit s'agir *exactement*  de la même valeur, y compris le numéro de port, le cas échéant, sauf que vous y incluez également le protocole HTTPS. Vous trouverez un exemple ci-dessous.
    
  ```XML
  
<StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
  ```

5. Vous pouvez utiliser la même valeur pour l'élément **Title** dans le fichier AppManifest.xml que celle que vous avez utilisée pour le champ **Titre** dans AppRegNew.aspx. La valeur de l'élément **Title** est le nom du complément que les utilisateurs peuvent voir sur le complément après son installation. Le fait que le complément porte un nom différent dans la boîte de dialogue de consentement et dans l'interface utilisateur SharePoint peut perturber les utilisateurs.
    
    L'exemple suivant illustre ces valeurs dans le manifeste du complément.
    


  ```XML
  <Properties>
  <Title>Contoso photo printing app</Title>
  <StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
</Properties>
  ```


## Utilisation de l'URL de redirection dans un complément qui demande des autorisations à la volée
<a name="UseRedirectUrl"> </a>

Si votre application web est lancée en dehors de SharePoint (et n'est donc pas un véritable Complément SharePoint), elle doit être conçue pour demander des autorisations à SharePoint lors de l'exécution. Son code doit utiliser l'URI de redirection, ainsi que d'autres informations, pour obtenir un jeton d'accès du service ACS. Recherchez l'endroit où cet URI est défini et utilisez  *exactement*  la même valeur que celle indiquée dans le champ **URI de redirection** sur la page AppRegNew.aspx ou dans le Mon tableau de bord vendeur. Il peut s'agir d'un fichier de code ou d'un fichier de configuration.
  
    
    

## Récupération des informations d'inscription du complément et des informations du principal de complément
<a name="Retrieve"> </a>

Vous pouvez récupérer les informations d'inscription du complément et les informations du principal de complément pour les compléments que vous avez installés ou inscrits sur SharePoint. 
  
    
    
Pour rechercher les informations d'inscription d'un complément que vous avez inscrit, accédez à  `http://` *<SiteWebSharePoint>*  `/_layouts/15/AppInv.aspx`.
  
    
    
Pour effectuer une recherche, vous devez mémoriser l'ID client (également appelé ID du complément) utilisé pour inscrire le complément. La recherche renvoie les informations suivantes pour un ID client spécifique :
  
    
    

- Titre
    
  
- Domaine de complément
    
  
- URL de redirection (il s'agit de la même chose que l'URI de redirection.)
    
  
La recherche ne renvoie pas la valeur de la clé secrète du complément.
  
    
    
Pour afficher la liste des principaux de complément inscrits, accédez à l'adresse :
  
    
    
 `http://` *<SiteWebSharePoint>*  `/_layouts/15/AppPrincipals.aspx`
  
    
    

## Ressources supplémentaires
<a name="AR"> </a>


-  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Trois systèmes d'autorisation pour les compléments SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
