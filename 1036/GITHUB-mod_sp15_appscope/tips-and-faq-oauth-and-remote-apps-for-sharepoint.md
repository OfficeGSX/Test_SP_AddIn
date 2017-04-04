

# Conseils et forum aux questions : OAuth et les applications pour SharePoint distantes
Cette rubrique est maintenant obsolète. Consultez les liens ci-dessous pour déterminer où le contenu a été déplacé. 
 * **S'applique à :*** 
  
    
    


||
|:-----|
|**Dans cet article** <BR/> [Récupération des informations d'application](#AppInfo) <BR/> [Fichier AppManifest.xml](#Manifest) <BR/> [Fichier Web.config](#Webconfig) <BR/> [URL et protocole SSL (Secure Sockets Layer)](#URL) <BR/> [Page de redirection d'application ](#Redirect) <BR/> [URI de redirection](#RedirectURI) <BR/> [Jetons OAuth](#Tokens) <BR/> [Autorisations et étendues des demandes d'autorisation](#Perm) <BR/> [Types de stratégie d'autorisation OAuth](#Policy) <BR/> [Débogage](#Debugging) <BR/> [Applications à haut niveau de fiabilité (applications de serveur à serveur) sur site](#S2S) <BR/> [Autres problèmes associés à OAuth](#Misc) <BR/> [Fonctionnalités sociales](#Social) <BR/> [Autres ressources](#AR)|
   

## Récupération des informations d'application
<a name="AppInfo"> </a>

Ce contenu a été déplacé vers  [Récupération des informations d'inscription du complément et des informations du principal de complément ](register-sharepoint-add-ins-2013.md#Retrieve).
  
    
    

## Fichier AppManifest.xml
<a name="Manifest"> </a>

Ce contenu a été déplacé vers :
  
    
    

-  [Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Référence du schéma de manifestes d'applications pour SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  

### Quelle est l'URL dans l'élément <StartPage> ?

Ce contenu a été déplacé vers : 
  
    
    


  
    
    
>  [Explorer la structure du manifeste d'application et le package d'un complément pour SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

  
    
    
>  [Page d'accueil, élément](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)
    
  

### Quels sont les attributs et éléments du fichier AppManifest.xml ?

Ce contenu a été déplacé vers  [Référence du schéma de manifestes d'applications pour SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
  
    
    

## Fichier Web.config
<a name="Webconfig"> </a>

Ce contenu a été déplacé vers  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md), en particulier la section  [Saisie des valeurs d'inscription dans les fichiers web.config et AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

### Quels sont les paramètres d'application du fichier Web.config ?

Ce contenu a été déplacé vers  [Saisie des valeurs d'inscription dans les fichiers web.config et AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

## URL et protocole SSL (Secure Sockets Layer)
<a name="URL"> </a>

Ce contenu a été déplacé vers : 
  
    
    

-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications de service ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Mon application web rencontre des problèmes de communication avec SharePoint. Quels éléments dois-je vérifier ?

Ce contenu a été déplacé vers : 
  
    
    

-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications de service ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Quelles URL doivent être codées en dur dans mon application pour pointer vers le serveur de cloud ?

Ce contenu a été déplacé vers : 
  
    
    

-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications de service ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Dois-je inscrire l'alias CNAME ou l'URL sous-jacente actuelle qui héberge l'application ?

Ce contenu a été déplacé vers : 
  
    
    

-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Erreurs d'autorisation diverses liées au domaine et à SSL dans les applications de service ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Le message d'erreur suivant s'affiche : « La connexion sous-jacente a été fermée : impossible d'établir une relation de confiance pour le canal sécurisé SSL/TLS ». Que dois-je faire ?

Ce contenu a été déplacé vers  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed).
  
    
    

## Page de redirection d'application
<a name="Redirect"> </a>

Ce contenu a été déplacé vers  [Obtenir un nouveau jeton de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### À quoi sert la page de redirection de l'application ?

Ce contenu a été déplacé vers  [Obtenir un nouveau jeton de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Comment dois-je utiliser la page de redirection de l'application pour obtenir le jeton de contexte ?

Ce contenu a été déplacé vers  [Obtenir un nouveau jeton de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Comment dois-je utiliser la page appredirect dans l'URL ?

Ce contenu a été déplacé vers  [Obtenir un nouveau jeton de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

## URI de redirection
<a name="RedirectURI"> </a>

Ce contenu a été déplacé vers  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md)
  
    
    

### Dois-je utiliser une URI de redirection ?

Ce contenu a été déplacé vers  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md)
  
    
    

## Jetons OAuth
<a name="Tokens"> </a>

Ce contenu a été déplacé vers :
  
    
    

-  [Gestion des jetons de sécurité dans les compléments SharePoint à faible niveau de fiabilité hébergés par le fournisseur](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)
    
  
-  [Création et utilisation de jetons d'accès dans les compléments à haut niveau de fiabilité hébergés par un fournisseur pour SharePoint](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
    
  

### Qu'est-ce qu'un jeton de contexte ?

Ce contenu a été déplacé vers  [Comprendre la structure et la gestion des jetons de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).
  
    
    

### Qu'est-ce qu'un jeton d'accès ?

Ce contenu a été déplacé vers  [Comprendre la gestion des jetons d'accès](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens).
  
    
    

### Qu'est-ce qu'un jeton d'actualisation ?

Ce contenu a été déplacé vers  [Comprendre la gestion et la mise en cache des jetons d'actualisation](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

### La chaîne de jeton de contexte doit-elle être stockée dans un cookie afin de pouvoir être utilisée pour d'autres demandes de page directement à partir de l'application hébergée par un fournisseur ?

Ce contenu a été déplacé vers  [Mettre en cache le jeton de contexte ou certains de ses éléments](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).
  
    
    

### De quels éléments est composée la valeur de clé de cache ? Comment est-elle unique ?

Ce contenu a été déplacé vers  [Comprendre la clé de cache](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey).
  
    
    

### Comment puis-je récupérer le jeton de contexte ?

Ce contenu a été déplacé vers  [Comprendre la structure et la gestion des jetons de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).
  
    
    

### Quelles informations sont contenues dans le jeton de contexte ?

Ce contenu a été déplacé vers  [Exemple de jeton de contexte](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken).
  
    
    

### Quelles informations un jeton d'accès contient-il ?

Ce contenu a été déplacé vers :
  
    
    

-  [Exemples de jetons d'accès dans le système d'autorisation ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)
    
  
-  [Exemple de jeton d'accès dans le système d'autorisation des applications à haut niveau de fiabilité](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)
    
  

### Comment dois-je calculer l'heure et la date exactes à partir de la valeur de nbf et exp ?

Ce contenu a été déplacé vers  [Utiliser les valeurs de temps JWT](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes).
  
    
    

### Je veux protéger le fichier svc de mes applications des utilisateurs qui ne proviennent pas de SharePoint. Je vérifie la légitimité de l'utilisateur au point d'entrée de l'application (en créant ClientContext), mais le service WCF ne peut être appelé par personne. Dois-je créer ClientContext à partir d'un jeton de contexte à chaque appel de méthode svc ?

Ce contenu a été déplacé vers  [Utiliser le jeton de contexte pour limiter l'accès aux seuls utilisateurs de SharePoint](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess).
  
    
    

### Puis-je conserver la valeur AppContext (obtenue à partir d'une requête POST SharePoint) comme champ d'entrée masqué dans la page ?

Ce contenu a été déplacé vers  [Mettre en cache le jeton de contexte ou certains de ses éléments](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).
  
    
    

### Quelle est la durée de validité d'un jeton d'actualisation ?

Ce contenu a été déplacé vers  [Comprendre la gestion et la mise en cache des jetons d'actualisation](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

### Je stocke le jeton d'accès et l'URL hôte dans des cookies pour qu'ils puissent être utilisés dans d'autres demandes de page. Mais l'utilisateur a fait une pause et le jeton d'accès a expiré. Que dois-je faire ?

Ce contenu a été déplacé vers :
  
    
    

-  [Comprendre la gestion des jetons d'accès](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)
    
  
-  [Comprendre la gestion et la mise en cache des jetons d'actualisation](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)
    
  

### Dans quel cas un jeton d'actualisation ancien qui n'a pas expiré et qui est toujours valide doit-il être ignoré et remplacé par un nouveau ?

Ce contenu a été déplacé vers  [Comprendre la gestion et la mise en cache des jetons d'actualisation](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

## Autorisations et étendues des demandes d'autorisation
<a name="Perm"> </a>

Ce contenu est accessible dans  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
  
    
    

### Quelles sont les étendues des demandes d'autorisation et les autorisations disponibles pour les listes, le contenu de bibliothèque et d'autres fonctionnalités ?

Ce contenu a été déplacé dans  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
  
    
    

## Types de stratégie d'autorisation OAuth
<a name="Policy"> </a>

Ce contenu est accessible dans  [Types de stratégie d'autorisation des compléments dans SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)
  
    
    

### Quelle est la différence entre la stratégie de l'application uniquement et la stratégie de l'application + utilisateur ?

Ce contenu est accessible dans  [Types de stratégie d'autorisation des compléments dans SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)
  
    
    

### Est-il possible d'accorder ou de refuser l'autorisation de lancer une application ?

Ce contenu est accessible dans  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
  
    
    

## Débogage
<a name="Debugging"> </a>

Ce contenu a été déplacé vers : 
  
    
    

-  [Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Résolution des problèmes relatifs aux compléments à faible niveau de fiabilité pour SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Utilisation de Fiddler

Ce contenu a été déplacé vers :
  
    
    

-  [Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Résolution des problèmes relatifs aux compléments à faible niveau de fiabilité pour SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

## Applications à haut niveau de fiabilité (applications de serveur à serveur) sur site
<a name="S2S"> </a>

Ce contenu a été déplacé vers  [Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md)
  
    
    

### Lorsque j'exécute une application à haut niveau de fiabilité, une erreur 401 non autorisée s'affiche. Que dois-je faire ?

Ce contenu a été déplacé vers  [Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md)
  
    
    

### Comment puis-je obtenir un jeton de contexte pour une application à haut niveau de fiabilité ?

Ce contenu a été déplacé vers  [Création et utilisation de jetons d'accès dans les compléments à haut niveau de fiabilité hébergés par un fournisseur pour SharePoint](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
  
    
    

## Autres problèmes associés à OAuth
<a name="Misc"> </a>

Ce contenu a été déplacé vers :
  
    
    

-  [Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Résolution des problèmes relatifs aux compléments à faible niveau de fiabilité pour SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Lorsque je tente de lire un fichier à l'aide de la méthode HTTP DAV, une erreur s'affiche. Que dois-je faire ?

Ce contenu a été déplacé vers  [Résolution des problèmes relatifs aux compléments à faible niveau de fiabilité pour SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
  
    
    

### Est-il possible de transférer l'autorisation OAuth vers d'autres composants dans des domaines différents ou de configurer OAuth pour plusieurs URI ?

Oui. Voir  [Transmettre le jeton d'accès aux systèmes principaux](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend).
  
    
    

### La valeur de principal SharePoint 2013 est-elle constante ?

Oui. Voir  [Exemples de jetons d'accès dans le système d'autorisation ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (notamment le tableau 1).
  
    
    

### L'ID et le secret d'application sont-ils constants dans tous les clients pour une application donnée ?

Oui, si l'application est inscrite dans le Mon tableau de bord vendeur ; mais si elle est inscrite séparément dans chaque location ou batterie de serveurs SharePoint, elle peut avoir des ID et des secrets distincts pour chacun de ces éléments. Dans ce cas, plusieurs applications sont visibles dans Microsoft Azure Access Control Service (ACS). Voir  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Les domaines sont-ils uniques ?

Oui. Voir  [Exemples de jetons d'accès dans le système d'autorisation ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (notamment le tableau 1) et [Utiliser le jeton de contexte pour obtenir un jeton d'accès](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken).
  
    
    

### Comment puis-je désactiver l'exigence du protocole HTTPS pour OAuth pendant le développement ?

Ce contenu a déplacé vers  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement).
  
    
    

## Fonctionnalités sociales
<a name="Social"> </a>

Ce contenu a été déplacé vers :
  
    
    

1.  [Fonctions sociales et de collaboration dans SharePoint 2013](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)
    
  
2.  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  

### Comment puis-je récupérer l'identité et les propriétés d'un utilisateur ?

Voir  [Obtenir les propriétés et l'identité de l'utilisateur dans SharePoint 2013](get-user-identity-and-properties-in-sharepoint-2013.md).
  
    
    

### Quelles sont l'utilisation des différentes fonctionnalités sociales et les étendues des demandes d'autorisation ?

Ce contenu est accessible dans  [Mise en route du développement avec les fonctions sociales dans SharePoint 2013](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms).
  
    
    

### Comment puis-je récupérer les propriétés de profil utilisateur des personnes qui me suivent ?

Ce contenu a été déplacé vers  [Procédure : Récupérer les propriétés de profil utilisateur à l'aide du modèle objet client .NET dans SharePoint 2013](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp).
  
    
    