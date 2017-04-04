---
title: Création de compléments pour SharePoint qui utilisent l'autorisation de faible niveau de fiabilité
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# Création de compléments pour SharePoint qui utilisent l'autorisation de faible niveau de fiabilité
Découvrez le système d'autorisation à faible niveau de fiabilité pour les Compléments SharePoint.
Les composants distants dans un Complément SharePoint (ou application externe) peuvent obtenir une autorisation pour les ressources SharePoint en transmettant un jeton d'accès à SharePoint avec chaque requête HTTP. Les composants distants obtiennent ce jeton d'accès à partir d'un compte Microsoft Azure Access Control Service (ACS) qui est associé à la location Office 365 du client. Azure ACS agit comme serveur d'autorisation dans une transaction  [OAuth 2.0](http://oauth.net/), appelée flux, avec SharePoint en tant que serveur de ressource et avec les composants distants en tant que client. Pour en savoir plus sur les spécifications de protocoles connexes, voir  [Protocole d'autorisation web (oauth)](http://datatracker.ietf.org/doc/active/#oauth). 
  
    
    

Les Compléments SharePoint hébergés par un fournisseur qui utilisent le système d'autorisation à faible niveau de fiabilité peuvent être vendus dans l'Office Store et installés surMicrosoft SharePoint Online ou sur une batterie de serveurs SharePoint en local qui a été configurée pour utiliser la location Office 365 du client afin d'établir une connexion sécurisée à Azure ACS. Le client doit avoir une location Office 365 pour installer des Compléments SharePoint qui utilisent le système à faible niveau de fiabilité. Cependant, il n'est pas nécessaire que le client utilise la location à d'autres fins. Pour obtenir des instructions sur la liaison d'une batterie de serveurs en local à une location Office 365, voir  [Utiliser un site SharePoint Office 365 pour autoriser des compléments hébergés par un fournisseur sur un site SharePoint local](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md).
## Inscription auprès de Azure ACS
<a name="Registration"> </a>

Pour utiliser le système à faible niveau de fiabilité, le Complément SharePoint doit d'abord être inscrit auprès d'Azure ACS et auprès du service de gestion des applications de la batterie de serveurs SharePoint ou de la location SharePoint Online. (Le « service de gestion des applications » se nomme ainsi car l'ancien nom des Compléments SharePoint est « applications pour SharePoint ».) Pour les compléments vendus via l'Office Store, l'inscription auprès d'ACS est réalisée dans le service Mon tableau de bord vendeur et l'inscription au service est effectuée lors de l'installation du complément. Pour les compléments distribués dans le catalogue de compléments de l'organisation, l'inscription auprès d'ACS et du service est effectuée sur la page \\_Layouts\\15\\AppRegNew.aspx de toute location ou batterie de serveurs SharePoint sur laquelle le complément est installé. Les applications externes ou autres que SharePoint qui accèdent à SharePoint doivent également être inscrites. Cette catégorie comprend les Compléments Office, les applications du Windows Store, les applications web et les applications de périphériques telles que celles des smartphones.
  
    
    

> [!REMARQUE]
> L'inscription requiert que l'application ait un domaine Internet. Tout domaine existant peut être utilisé à cette fin, mais vous ne pouvez pas être certain à 100 % qu'un domaine que vous ne possédez pas existera toujours. C'est pourquoi une application web devrait faire partie d'une application de périphérique native, même si le composant d'application web sert uniquement à permettre l'inscription. Pour un exemple de code avancé conçu de cette façon, voir  [Fournir des sites en lots avec le modèle du complément](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6). 
  
    
    

Pour plus d'informations sur l'inscription, voir  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Expiration de la clé secrète du complément

La clé secrète du complément doit être remplacée tous les 12 mois. Pour plus d'informations, voir  [Remplacement d'une clé secrète client arrivant à expiration dans un complément pour SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md).
  
    
    

## Stratégies d'autorisation
<a name="Policies"> </a>

Un Complément SharePoint peut être conçu pour utiliser l'une des deux stratégies d'autorisation suivantes :
  
    
    

- **Stratégie utilisateur + complément :** les compléments qui utilisent cette stratégie peuvent effectuer uniquement des actions pour lesquelles le complément et l'utilisateur disposent d'une autorisation. Il s'agit de la stratégie par défaut, sauf si le développeur prend des mesures spécifiques permettant d'utiliser l'autre stratégie.
    
  
- **Stratégie application uniquement :** les compléments qui utilisent cette stratégie peuvent effectuer toutes les actions pour lesquelles elles disposent d'une autorisation, même si l'utilisateur n'en a pas. Le développeur doit demander à ce que cette stratégie soit utilisée dans le fichier manifeste du complément. Cette demande doit alors être approuvée par l'utilisateur qui installe le complément. Cette stratégie est autorisée uniquement pour les Compléments SharePoint hébergés par un fournisseur.
    
  
Pour plus d'informations sur les stratégies d'autorisation, voir  [Types de stratégie d'autorisation des compléments dans SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Deux flux d'exécution OAuth
<a name="Flows"> </a>

À chaque exécution d'un Complément SharePoint hébergé dans le cloud ou d'une application externe qui accède à SharePoint, un flux (série d'interactions entre le complément, SharePoint 2013, ACS et parfois l'utilisateur final) se produit. Le résultat du flux est le suivant : SharePoint reçoit un jeton d'accès inclus avec chaque demande de création, mise à jour, suppression (opérations CRUD) que l'application envoie à SharePoint.
  
    
    
SharePoint utilise principalement deux flux OAuth : un pour les Compléments SharePoint hébergés par le nuage, l'autre (appelé « à la volée ») pour les applications d'autres plateformes qui accèdent aux données SharePoint.
  
    
    

- **Flux de jeton de contexte :** le composant distant du Complément SharePoint utilise les points de terminaison REST ou du modèle objet client (CSOM) SharePoint pour effectuer des appels vers SharePoint. SharePoint demande un jeton de contexte à ACS pour l'envoyer au serveur distant. Le serveur distant utilise alors ce jeton de contexte pour demander un jeton d'accès à ACS. Le serveur distant utilise ensuite le jeton d'accès pour répondre à SharePoint. Pour plus d'informations sur ce flux, voir [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).
    
  
- **Flux de code d'autorisation :** les autorisations nécessaires sont accordées à un Complément SharePoint pour les ressources SharePoint lors de son installation. Cependant, les applications qui ne sont pas installées sur SharePoint doivent demander des autorisations « à la volée », c'est-à-dire chaque fois qu'elles s'exécutent. Il n'y a pas de jeton de contexte SharePoint dans ce flux. Au lieu de cela, lors de l'exécution de compléments et de tentatives d'accès à SharePoint, SharePoint invite l'utilisateur à accorder les autorisations que l'application demande. Lorsque l'utilisateur accorde les autorisations, SharePoint obtient un code d'autorisation d'ACS qui est transmis à l'application. L'application utilise alors ce code pour obtenir un jeton d'accès auprès d'ACS, qu'elle peut ensuite utiliser pour communiquer avec SharePoint. Pour plus d'informations sur ce flux, voir [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
    
  

## Résolution des problèmes relatifs aux Compléments SharePoint de faible niveau de fiabilité
<a name="Trouble"> </a>

Cet article fournit des conseils généraux en matière de résolution de problèmes, ainsi que des informations sur certaines questions précises au sujet des Compléments SharePoint qui utilisent le système d'autorisation à faible niveau de fiabilité.
  
    
    

### Utilisation de l'outil Fiddler

L' [outil gratuit Fiddler](http://www.telerik.com/fiddler) peut être utilisé pour capturer les demandes HTTP envoyées par le composant distant de votre complément à SharePoint. Il existe une [extension gratuite de l'outil ](https://github.com/andrewconnell/SPOAuthFiddlerExt) qui décode automatiquement les jetons d'accès dans les demandes.
  
    
    

### Désactivation de l'exigence HTTPS pour OAuth lors du développement
<a name="TurnOffHTTPRequirement"> </a>

OAuth exige SharePoint pour exécuter HTTPS (non seulement votre service, mais aussi SharePoint). Cette exigence peut s'avérer gênante au moment du développement du complément. Par exemple, il est possible que vous receviez un message 403 (accès interdit) en essayant d'appeler SharePoint lors du débogage du complément, car la prise en charge de SSL ne se situe plus dans « localhost », où votre complément est en cours d'exécution.
  
    
    
Vous pouvez désactiver l'exigence HTTPS lors du développement à l'aide des cmdlets Windows PowerShell suivantes :
  
    
    



```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()

```

Pour réactiver l'exigence HTTPS ultérieurement, utilisez les cmdlets Windows PowerShell suivantes :
  
    
    



```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()

```


### Erreurs d'autorisation diverses liées au domaine et à SSL
<a name="DomainRelatedErrors"> </a>

Si les noms de domaine dans les fichiers de configuration et les formulaires d'inscription ne correspondent pas, l'autorisation peut être bloquée. Les quatre valeurs suivantes doivent être identiques :
  
    
    

- Le **domaine de complément** qui est spécifié lorsque le Complément SharePoint est inscrit auprès de AppRegNew.aspx ou du service Mon tableau de bord vendeur.
    
  
- Le domaine dans lequel le certificat de sécurité de l'application web distante est inscrit.
    
  
- La partie de domaine de la valeur **StartPage** dans le fichier AppManifest.xml.
    
  
- La partie de domaine des URL de tous les récepteurs d'événements spécifiés dans le fichier AppManifest.xml.
    
  
Concernant ce point, veillez à prendre en compte les éléments suivants :
  
    
    

- Si le composant distant de votre Complément SharePoint utilise un port autre que le port 443, vous devez inclure explicitement le port en tant que partie du domaine dans les quatre endroits. Par exemple,  `contoso.com:3333`. (Vous devez utiliser le protocole HTTPS pour lequel le port par défaut est le port 443.)
    
  
- Si vous créez un alias CNAME DNS pour votre application web distante, utilisez l'alias CNAME pour la valeur de domaine aux quatre emplacements. Par exemple, si votre application est hébergée sur contoso.cloudapp.net et que vous lui créez un CNAME sur contososoftware.com, utilisez contososoftware.com comme domaine.
    
  
- Le domaine doit être codé en dur dans la valeur **StartPage** (et toutes les URL de récepteur d'événements) du fichier AppManifest.xml avant que le complément soit inclus dans un package. Si vous utilisez l'Assistant **Publier** dans Visual Studio pour inclure le complément dans un package, le domaine vous sera demandé et les Outils de développement Office pour Visual Studio l'inséreront dans la valeur **StartPage** pour vous (à la place du jeton `~remoteWebUrl` qui est utilisé pendant le débogage). Par contre, si vous n'utilisez pas l'Assistant **Publier**, ou si vous l'utilisez alors que votre application web distante est déployée dans Azure, vous devez remplacer manuellement le jeton par le domaine (et le protocole), par exemple  `https://contososoftware.com` ou `https://MyCloudVM:3333`.
    
  

### Message d'erreur : « La connexion sous-jacente a été fermée : impossible d'établir une relation de confiance pour le canal sécurisé SSL/TLS. »
<a name="ErrorConnectionClosed"> </a>

Cette erreur révèle un problème d'établissement de liaison SSL et non un problème OAuth. Assurez-vous que le certificat que vous utilisez est approuvé par SharePoint et que ce certificat correspond au nom du point de terminaison.
  
    
    

### Erreurs pouvant survenir en utilisant la méthode DAV HTTP pour lire des fichiers à partir de SharePoint
<a name="ErrorConnectionClosed"> </a>

La méthode DAV HTTP ne fonctionne pas avec OAuth. Si vous utilisez le modèle objet client SharePoint (CSOM), utilisez le code suivant pour lire un fichier.
  
    
    

```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();

```


## Dans cette section
<a name="Trouble"> </a>

 [Utiliser un site SharePoint Office 365 pour autoriser des compléments hébergés par un fournisseur sur un site SharePoint local](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)
  
    
    
 [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Remplacement d'une clé secrète client arrivant à expiration dans un complément pour SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)
  
    
    

## Ressources supplémentaires
<a name="FileName_AdditionalResources"> </a>


-  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md)
    
  

