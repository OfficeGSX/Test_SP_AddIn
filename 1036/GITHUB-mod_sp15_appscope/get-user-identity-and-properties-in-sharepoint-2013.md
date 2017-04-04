---
title: Obtenir les propriétés et l'identité de l'utilisateur dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# Obtenir les propriétés et l'identité de l'utilisateur dans SharePoint 2013
Récupérer l'identité et les informations utilisateur dans SharePoint 2013.
Vous pouvez récupérer l'identité et les informations utilisateur de différentes manières, en fonction du type d'informations que vous souhaitez récupérer. Cet article vous indique plusieurs manières de procéder.
  
    
    


## Composants requis pour récupérer l'identité et les propriétés utilisateur
<a name="Prereq"> </a>


- Préparer votre environnement de développement de complément, comme décrit dans  [Configurer un environnement de développement local pour les compléments pour SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
    
  
- Installez Visual Studio.
    
  
- Installez la version la plus récente des  [outils de développement Office pour Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) ou des [outils de développement Office pour Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015).
    
  

> [!REMARQUE]
> Pour obtenir des instructions sur la manière de configurer un environnement de développement adapté à vos besoins, voir  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md). 
  
    
    


### Concepts fondamentaux pour récupérer l'identité et les propriétés utilisateur

Le tableau suivant répertorie des articles utiles pour vous aider à comprendre les concepts nécessaires pour créer des Compléments SharePoint.
  
    
    


|**Article**|**Description**|
|:-----|:-----|
| [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) <br/> |Apprenez-en davantage sur les autorisations de complément nécessaires pour travailler avec SharePoint 2013, y compris les types d'autorisation de complément, les étendues de demande d'autorisation et la gestion des autorisations. Cet article aborde également les différences dans les droits d'autorisation de complément et les droits utilisateur.  <br/> |
| [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |Apprenez-en davantage sur le flux d'autorisation et d'authentification OAuth pour les compléments basés sur le Cloud.  <br/> |
| [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |Découvrez comment créer une Complément SharePoint basique hébergée par un fournisseur avec les Outils de développement Office pour Visual Studio 2012, comment interagir avec les sites Microsoft SharePoint 2013 en utilisant le CSOM SharePoint et comment implémenter OAuth dans une Complément SharePoint.  <br/> |
   

## Récupérer l'identité utilisateur du site web actuel.
<a name="WebsiteUserID"> </a>

La manière la plus facile de récupérer l'identité de l'utilisateur actuel du site web est de passer par l'objet **Web**. Le fichier TokenHelper.cs de votre projet vous permet d'obtenir l'identité utilisateur du site web actuel en utilisant l'extrait de code suivant.
  
    
    

```cs

ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);
 
 
            //Load the properties for the Web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();
 
            //Get the site name.
            siteName = web.Title;
 
            //Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

```


- Si vous utilisez Office 365, le nom de connexion que vous recevez est similaire à  `i:0#.f|membership|adam@contoso.com`.
    
  
- Si vous utilisez Microsoft SharePoint 2013 au niveau local et que l'utilisateur est connecté en tant qu'utilisateur normal utilisant NTLM, le nom de connexion que vous recevez est similaire à  `i:0#.w|contoso\\adam`.
    
  
- Si vous utilisez SharePoint 2013 au niveau local et que l'utilisateur est connecté en utilisant un compte de batterie, le nom de connexion que vous recevez est  `SHAREPOINT\\System`.
    
  

## Récupérer l'identité utilisateur en utilisant la méthode ResolvePrincipal
<a name="ResolvePrincipal"> </a>

Vous pouvez utiliser la méthode suivante pour récupérer le nom de connexion de l'utilisateur. Si vous disposez de l'adresse de messagerie ou du nom complet de l'utilisateur, vous pouvez utiliser la méthode  [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) pour obtenir le nom de connexion de l'utilisateur.
  
    
    

> [!REMARQUE]
> Les API se trouvent dans l'espace de noms Microsoft.SharePoint.Client.Utilities dans l'assembly  [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/fr-fr/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx). 
  
    
    

Voici un exemple de code pour vous montrer comment obtenir les informations de connexion utilisateur.
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

La valeur **Person.LoginName** indique les informations de connexion.
  
    
    

## Récupérez les propriétés de profil et d'identité utilisateur.
<a name="Profile"> </a>

Si vous souhaitez récupérer l'identité et les propriétés utilisateur, vous pouvez utiliser le jeton OAuth et les fonctionnalités de mise en réseau API. Obtenez d'abord le jeton OAuth, puis affectez-le au contexte client. L'exemple de code suivant indique comment recevoir les propriétés du profil d'utilisateur.
  
    
    

```cs

ClientContext clientContext = new ClientContext(<sharepointurl>);
clientContext.AuthenticationMode = ClientAuthenticationMode.Anonymous;
clientContext.FormDigestHandlingEnabled = false;
clientContext.ExecutingWebRequest +=
delegate(object oSender, WebRequestEventArgs webRequestEventArgs)
{                      
    webRequestEventArgs.WebRequestExecutor.RequestHeaders["Authorization"] =
        "Bearer " + accessToken;
};

```

Ensuite, utilisez l'API  [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) pour recevoir les propriétés de l'utilisateur qui utilise le complément.
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

Pour que le code fonctionne :
  
    
    

- Les services partagés profil utilisateur doivent être configurés et synchronisés sur SharePoint 2013 pour les utilisateurs.
    
  
- Vous devez ajouter l'étendue d'autorisation pour les fonctionnalités de mise en réseau dans le manifeste de complément :
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

Les API se trouvent dans Microsoft.SharePoint.Client.UserProfiles.dll.
  
    
    
Voici un autre exemple d'extrait de code indiquant comment accéder au magasin de profils utilisateur.
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## Ressources supplémentaires
<a name="AdditionalResources"> </a>


-  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Compléments](sharepoint-add-ins.md)
    
  
-  [Configurer un environnement de développement local pour les compléments pour SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

