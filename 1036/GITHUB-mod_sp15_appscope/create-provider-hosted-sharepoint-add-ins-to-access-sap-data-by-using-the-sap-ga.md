---
title: Créer des compléments hébergés par un fournisseur pour SharePoint afin d'accéder aux données SAP via la passerelle SAP pour Microsoft
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# Créer des compléments hébergés par un fournisseur pour SharePoint afin d'accéder aux données SAP via la passerelle SAP pour Microsoft
Découvrez comment créer un Complément SharePoint pouvant accéder aux données SAP.
Vous pouvez créer un Complément SharePoint qui lit et écrit les données SAP et éventuellement les données SharePoint en utilisant la Passerelle SAP pour Microsoft et la Bibliothèque d'authentification Azure AD pour .NET. Cet article décrit les procédures pour concevoir un Complément SharePoint afin qu'il puisse accéder à SAP. 
  
    
    


## Avant de commencer

Les éléments suivants sont des conditions préalables aux procédures décrites dans cet article :
  
    
    

- **Un Site du développeur Office 365** dans un domaine Office 365 qui est associé à un abonnement Microsoft Azure Active Directory. Voir [Configurer un environnement de développement pour les compléments pour SharePoint dans Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) ou [Créer un Site du développeur dans un abonnement Office 365 existant](create-a-developer-site-on-an-existing-office-365-subscription.md).
    
  
- **Visual Studio 2013 Mise à jour 2** ou une version ultérieure, que vous pouvez télécharger en consultant l'article [Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx).
    
  
- Les **Outils de développement Microsoft Office pour Visual Studio** dans leur version incluse dans Visual Studio 2013 Mise à jour 2 ou version ultérieure.
    
  
- **La Passerelle SAP pour Microsoft** est déployée et configurée dans Microsoft Azure. Voir la documentation relative à la [Passerelle SAP pour Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Un compte d'organisation sur Microsoft Azure**. Voir  [Ajouter manuellement de commun accord dans votre application Office 365 Preview API](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount).
    
    > **REMARQUE**
      > Connectez-vous avec votre compte Office 365 (login.microsoftonline.com) pour changer le mot de passe temporaire après la création du compte. 
- **Un point de terminaison OData SAP** contenant des exemples de données. Pour en savoir plus, voir la page relative à la [Passerelle SAP pour Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Quelques connaissances de base d'Azure AD.** Voir la page [Présentation d'Azure AD](http://msdn.microsoft.com/library/azure/dn655157.aspx).
    
  
- **Quelques connaissances de base sur la création de Compléments SharePoint.** Voir la page [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md).
    
  
- **Quelques connaissances de base d'OAuth 2.0 dans Azure AD**. Voir  [OAuth 2.0 dans Azure AD](http://msdn.microsoft.com/library/azure/dn645545.aspx) et ses rubriques enfants.
    
  
 **Exemple de code :** [SharePoint 2013 : utiliser la passerelle SAP pour Microsoft dans un complément SharePoint](http://code.msdn.microsoft.com/SharePoint-2013-Utilisation-la-0931abce)
  
    
    

## Comprendre l'authentification et l'autorisation à la Passerelle SAP pour Microsoft et à SharePoint
<a name="AuthOverview"> </a>

OAuth 2.0 dans Azure AD permet aux applications d'accéder à de multiples ressources hébergées par Microsoft Azure, et la Passerelle SAP pour Microsoft est l'une d'entre elles. Avec OAuth 2.0, les applications et les utilisateurs sont des principaux de sécurité. Les principaux d'application doivent s'authentifier et ont besoin d'une autorisation pour accéder aux ressources protégées en plus des (et parfois à la place des) utilisateurs. Le processus implique un « flux » OAuth dans lequel l'application, qui peut être un Complément SharePoint, obtient un jeton d'accès (et un jeton d'actualisation) qui est accepté par tous les services et applications hébergés par Microsoft Azure qui sont configurés pour utiliser Azure AD en tant que serveur d'autorisation OAuth 2.0. Le processus est semblable au processus d'obtention de l'autorisation à accéder à SharePoint par les composants distants d'un Complément SharePoint hébergé par un fournisseur, tel que décrit dans l'article  [Création de compléments pour SharePoint qui utilisent l'autorisation de faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) et ses articles enfants. Cependant, le système d'autorisation ACS utilise Microsoft Azure Access Control Service (ACS) comme émetteur de jeton approuvé plutôt qu'Azure AD.
  
    
    

> **CONSEIL**
> Si votre Complément SharePoint accède à SharePoint en plus d'accéder à la Passerelle SAP pour Microsoft, il devra utiliser  *les deux*  systèmes : Azure AD pour obtenir un jeton d'accès à la Passerelle SAP pour Microsoft et le système d'autorisation ACS pour obtenir un jeton d'accès à SharePoint. Les jetons provenant des deux sources ne sont pas interchangeables. Pour en savoir plus, voir la section [Ajout de l'accès à SharePoint à l'application ASP.NET (facultatif)](#SharePoint). 
  
    
    

Pour obtenir une description détaillée et un diagramme du flux OAuth utilisé par OAuth 2.0 dans Azure AD, voir la page  [Flux d'octroi d'un code d'autorisation](http://msdn.microsoft.com/library/azure/dn645542.aspx). (Pour obtenir une description similaire et un diagramme du flux pour accéder à SharePoint, voir la section  [Étapes du flux de jeton de contexte](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps).)
  
    
    

## Création du Complément SharePoint
<a name="AuthOverview"> </a>


### Création de la solution Visual Studio


1. Créez un projet de **Complément SharePoint** dans Visual Studio en suivant les étapes ci-dessous. (L'exemple de cet article suppose que vous utilisez le langage C#, mais vous pouvez également commencer un projet de Complément SharePoint dans la section **Visual Basic** des modèles de nouveaux projets.)
    
1. Dans l'Assistant **Nouveau complément SharePoint**, nommez le projet et cliquez sur **OK**. Pour l'exemple actuel, utilisez le nom SAP2SharePoint.
    
  
2. Indiquez en tant que site de débogage l'URL de domaine de votre Site du développeur Office 365 (ajoutez un slash à la fin). Par exemple, https://<domaine_O365>.sharepoint.com/. Précisez **Hébergement par le fournisseur** comme type de complément et cliquez sur **Suivant**.
    
  
3. Choisissez un type de projet. Pour l'exemple actuel, choisissez **Application Web Forms ASP.NET**. (Vous pouvez aussi créer des applications ASP.NET MVC qui ont accès à la Passerelle SAP pour Microsoft.) Cliquez sur **Suivant**.
    
  
4. Choisissez Azure ACS comme système d'authentification. (Votre Complément SharePoint utilisera ce système s'il accède à SharePoint, mais pas s'il accède à la Passerelle SAP pour Microsoft.) Cliquez sur **Terminer**.
    
  
2. Une fois le projet créé, vous êtes invité à vous connecter au compte Office 365. Utilisez les informations d'identification d'un administrateur de compte, par exemple Bob@<domaine_O365>.onmicrosoft.com.
    
  
3. Il existe deux projets dans la solution Visual Studio : le projet de Complément SharePoint approprié et un projet Application Web Forms ASP.NET. Ajoutez le package de **bibliothèque d'authentification Active Directory** (ADAL) au projet ASP.NET en suivant les étapes ci-dessous :
    
1. Cliquez avec le bouton droit sur le dossier **Références** dans le projet ASP.NET (nommé **SAP2SharePointWeb** dans l'exemple de cet article) et sélectionnez **Gérer les packages NuGet**. 
    
  
2. Dans la boîte de dialogue qui apparaît, sélectionnez **En ligne** sur la gauche. SaisissezMicrosoft.IdentityModel.Clients.ActiveDirectory dans la zone de recherche.
    
  
3. Lorsque la bibliothèque ADAL apparaît dans les résultats de la recherche, cliquez sur le bouton **Installer**, en regard du résultat, et acceptez la licence lorsque vous y êtes invité.
    
  
4. Ajoutez le package Json.net au projet ASP.NET en suivant les étapes ci-dessous :
    
1. Entrez Json.net dans la zone de recherche. Si vous obtenez trop de résultats, essayez de rechercherNewtonsoft.json.
    
  
2. Lorsque Json.net apparaît dans les résultats de la recherche, cliquez sur le bouton **Installer**, en regard du résultat.
    
  
5. Cliquez sur **Fermer**.
    
  

### Inscription de votre application web auprès d'Azure AD


1. Connectez-vous au  [portail de gestion Azure](https://manage.windowsazure.com) avec votre compte Administrateur Azure.
    
    > **REMARQUE**
      > Pour des raisons de sécurité, nous vous recommandons de ne pas utiliser un compte Administrateur lors du développement de compléments. 
2. Choisissez **Active Directory** sur la gauche.
    
  
3. Cliquez sur votre répertoire. 
    
  
4. Sélectionnez **APPLICATIONS** (sur la barre de navigation supérieure).
    
  
5. Sélectionnez **Ajouter** dans la barre d'outils en bas de l'écran.
    
  
6. Dans la boîte de dialogue qui apparaît, choisissez **Ajouter une application développée par mon organisation**.
    
  
7. Dans la boîte de dialogue **Ajouter une application**, nommez l'application. Pour l'exemple de cet article, utilisez le nom ContosoAutomobileCollection. 
    
  
8. Choisissez **Application Web et/ou API Web** comme type d'application, puis cliquez sur la flèche droite.
    
  
9. Dans la deuxième page de la boîte de dialogue, utilisez l'URL de débogage SSL du projet ASP.NET dans la solution Visual Studio en tant qu' **URL de connexion**. Pour trouver l'URL, suivez les étapes ci-après. ** *(Vous devez d'abord inscrire le complément avec l'URL de débogage de sorte que vous puissiez exécuter le débogueur Visual Studio (F5). Lorsque votre complément est prêt à être préparé, inscrivez-la à nouveau avec son URL de Site web Azure de préparation.  [Modification du complément et préparation de celui-ci sur Azure et Office 365](#Stage).)* **
    
1. Mettez en surbrillance le projet ASP.NET dans l' **Explorateur de solutions**. 
    
  
2. Dans la fenêtre **Propriétés**, copiez la valeur de la propriété **URL SSL**. Par exemple, https://localhost:44300/.
    
  
3. Collez-la dans le champ **URL de connexion** dans la boîte de dialogue **Ajouter une application**.
    
  
10. Pour l' **URI ID d'application**, n'attribuez à l'application qu'un seul URI, comme le nom de l'application ajouté à la fin de l'URL SSL. Par exemple, https://localhost:44300/ContosoAutomobileCollection.
    
  
11. Cliquez sur le bouton représentant une coche. Le tableau de bord Azure pour l'application s'ouvre et affiche un message indiquant que l'opération a réussi.
    
  
12. Choisissez **CONFIGURER** en haut de la page.
    
  
13. Faites défiler jusqu'à l' **ID client** et copiez-le. Vous en aurez besoin pour une procédure ultérieure.
    
  
14. Dans la section **Clés**, créez une clé. Elle n'apparaîtra pas immédiatement. Cliquez sur **ENREGISTRER** en bas de la page et la clé apparaît. Copiez-la, vous en aurez besoin pour une procédure ultérieure.
    
  
15. Faites défiler jusqu'à **Autorisations pour d'autres applications** et sélectionnez votre application de service de la Passerelle SAP pour Microsoft.
    
  
16. Ouvrez la liste déroulante **Autorisations déléguées** et activez les cases pour les autorisations d'accès au service de la Passerelle SAP pour Microsoft qui seront nécessaires à votre Complément SharePoint.
    
  
17. Cliquez sur **ENREGISTRER** en bas de l'écran.
    
  

### Configuration de l'application pour qu'elle communique avec Azure AD


1. Dans Visual Studio, ouvrez le fichier web.config dans le projet ASP.NET.
    
  
2. Dans la section  `<appSettings>`, les Outils de développement Office pour Visual Studio ont ajouté des éléments pour **ClientID** et **ClientSecret** pour le Complément SharePoint. (Ils sont utilisés dans le système d'autorisation Azure ACS si l'application ASP.NET accède à SharePoint. Vous pouvez les ignorer pour l'exemple de cet article, mais ne les supprimez pas. Ils sont nécessaires pour les Compléments SharePoint hébergés par un fournisseur, même si le complément n'accède pas aux données SharePoint. Leurs valeurs changent à chaque fois que vous appuyez sur la touche F5 dans Visual Studio.) Ajoutez les deux éléments suivants à la section. Ils sont utilisés par l'application pour s'authentifier auprès d'Azure AD. (N'oubliez pas que les applications et les utilisateurs sont des principaux de sécurité dans les systèmes d'authentification et d'autorisation basés sur OAuth.)
    
  ```
  
<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
  ```

3. Utilisez l'ID client que vous avez enregistré depuis votre répertoire Azure AD lors de la procédure précédente comme la valeur de la clé **ida:ClientID**. Conservez les majuscules, les minuscules et la ponctuation exactement comme vous les avez copiées et veillez à ne pas inclure d'espace au début ou à la fin de la valeur. Pour la clé **ida:ClientKey**, utilisez la  *clé*  que vous avez enregistrée depuis le répertoire. Encore une fois, veillez à ne pas introduire d'espace et à ne pas modifier la valeur. La section `<appSettings>` devrait maintenant ressembler à ce qui suit. (Il est possible que la valeur **ClientId** affiche un GUID ou une chaîne vide.)
    
  ```
  
<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
  ```


    > **REMARQUE**
      > Votre application est reconnue par Azure AD grâce à l'URL « localhost » que vous avez utilisée pour l'inscrire. L'ID client et la clé client sont associés à cette identité. Lorsque vous êtes prêt à préparer votre application sur un Site web Azure, inscrivez-la à nouveau avec une autre URL. 
4. Toujours dans la section **appSettings**, ajoutez une clé **Authority** et définissez sa valeur sur le domaine Office 365 ( *domaine*  .onmicrosoft.com) de votre compte d'entreprise. Dans l'exemple de cet article, le compte d'entreprise est Bob@<domaine_O365>.onmicrosoft.com, l'autorité est donc `<O365_domain>.onmicrosoft.com`. 
    
  ```
  
<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  ```

5. Toujours dans la section **appSettings**, ajoutez une clé **AppRedirectUrl** et définissez sa valeur sur la page vers laquelle le navigateur de l'utilisateur doit être redirigé après que le complément ASP.NET a obtenu un code d'autorisation de la part d'Azure AD. Il s'agit habituellement de la page sur laquelle l'utilisateur se trouvait lorsque l'appel à Azure AD a été effectué. Dans l'exemple de cet article, utilisez la valeur de l'URL SSL en ajoutant « /Pages/Default.aspx » à la fin, comme indiqué ci-dessous. (Ceci est une autre valeur que vous changerez pour la préparation.)
    
  ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  ```

6. Toujours dans la section **appSettings**, ajoutez une clé **ResourceUrl** et définissez sa valeur sur l'URI ID d'application de la Passerelle SAP pour Microsoft (et *non*  sur l'URI ID d'application de votre application ASP.NET). Cette valeur peut vous être fournie par l'administrateur de la Passerelle SAP pour Microsoft. Voici un exemple.
    
  ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
  ```


    La section  `<appSettings>` devrait maintenant ressembler à ceci :
    


  ```
  <appSettings>
  <add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
  <add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
</appSettings>
  ```

7. Enregistrez et fermez le fichier web.config.
    
    > **CONSEIL**
      > Ne laissez pas le fichier web.config ouvert lorsque vous exécutez le débogueur Visual Studio (F5). Les Outils de développement Office pour Visual Studio changent la valeur **ClientId** (mais pas la valeur **ida:ClientID**) chaque fois que vous appuyez sur la touche F5. Vous devez alors répondre à un message vous invitant à recharger le fichier web.config, s'il est ouvert, avant l'exécution du débogage. 

### Ajout d'une classe d'assistance pour s'authentifier sur Azure AD


1. Cliquez avec le bouton droit sur le projet ASP.NET et utilisez le processus d'ajout d'élément Visual Studio pour ajouter un nouveau fichier de classe au projet nommé AADAuthHelper.cs.
    
  
2. Ajoutez les instructions **using** suivantes au fichier.
    
  ```
  
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

  ```

3. Modifiez le mot clé d'accès en le passant de **public** à **internal** et ajoutez le mot clé **static** à la déclaration de classe.
    
  ```
  
internal static class AADAuthHelper
  ```

4. Ajoutez les champs ci-dessous à la classe. Ces champs stockent des informations que votre application ASP.NET utilise pour obtenir des jetons d'accès auprès d'AAD.
    
  ```
  private static readonly string _authority = ConfigurationManager.AppSettings["Authority"];
private static readonly string _appRedirectUrl = ConfigurationManager.AppSettings["AppRedirectUrl"];
private static readonly string _resourceUrl = ConfigurationManager.AppSettings["ResourceUrl"];     
private static readonly string _clientId = ConfigurationManager.AppSettings["ida:ClientID"];        
private static readonly ClientCredential _clientCredential = new ClientCredential(
                           ConfigurationManager.AppSettings["ida:ClientID"],
                           ConfigurationManager.AppSettings["ida:ClientKey"]);

private static readonly AuthenticationContext _authenticationContext = 
            new AuthenticationContext("https://login.windows.net/common/" + 
                                      ConfigurationManager.AppSettings["Authority"]);
  ```

5. Ajoutez la propriété ci-dessous à la classe. Cette propriété contient l'URL de l'écran de connexion d'Azure AD.
    
  ```
  
private static string AuthorizeUrl
{
    get
    {
        return string.Format("https://login.windows.net/{0}/oauth2/authorize?response_type=code&amp;redirect_uri={1}&amp;client_id={2}&amp;state={3}",
            _authority,
            _appRedirectUrl,
            _clientId,
            Guid.NewGuid().ToString());
    }
}

  ```

6. Ajoutez les propriétés ci-dessous à la classe. Celles-ci mettent en cache les jetons d'accès et d'actualisation et vérifient leur validité.
    
  ```
  
public static Tuple<string, DateTimeOffset> AccessToken
{
    get {
return HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] 
       as Tuple<string, DateTimeOffset>;
    }

    set { HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] = value; }
}

private static bool IsAccessTokenValid
{
   get 
   { 
       return AccessToken != null &amp;&amp;
       !string.IsNullOrEmpty(AccessToken.Item1) &amp;&amp;
       AccessToken.Item2 > DateTimeOffset.UtcNow;
   }
}

private static string RefreshToken
{
    get { return HttpContext.Current.Session["RefreshToken" + _resourceUrl] as string; }
    set { HttpContext.Current.Session["RefreshToken-" + _resourceUrl] = value; }
}

private static bool IsRefreshTokenValid
{
    get { return !string.IsNullOrEmpty(RefreshToken); }
}

  ```

7. Ajoutez les méthodes ci-dessous à la classe. Ces méthodes sont utilisées pour vérifier la validité du code d'autorisation et pour obtenir un jeton d'accès auprès d'Azure AD en utilisant soit un code d'authentification, soit un jeton d'actualisation.
    
  ```
  
private static bool IsAuthorizationCodeNotNull(string authCode)
{
    return !string.IsNullOrEmpty(authCode);
}

private static Tuple<Tuple<string,DateTimeOffset>,string> AcquireTokensUsingAuthCode(string authCode)
{
    var authResult = _authenticationContext.AcquireTokenByAuthorizationCode(
                authCode,
                new Uri(_appRedirectUrl),
                _clientCredential,
                _resourceUrl);

    return new Tuple<Tuple<string, DateTimeOffset>, string>(
                new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn), 
                authResult.RefreshToken);
}

private static Tuple<string, DateTimeOffset> RenewAccessTokenUsingRefreshToken()
{
    var authResult = _authenticationContext.AcquireTokenByRefreshToken(
                         RefreshToken,
                         _clientCredential.OwnerId,
                         _clientCredential,
                         _resourceUrl);

    return new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);
}

  ```

8. Ajoutez la méthode ci-dessous à la classe. Elle est appelée par le code-behind ASP.NET pour obtenir un jeton d'accès valide avant d'effectuer un appel pour obtenir les données SAP via la Passerelle SAP pour Microsoft.
    
  ```
  
internal static void EnsureValidAccessToken(Page page)
{
    if (IsAccessTokenValid) 
    {
        return;
    }
    else if (IsRefreshTokenValid) 
    {
        AccessToken = RenewAccessTokenUsingRefreshToken();
        return;
    }
    else if (IsAuthorizationCodeNotNull(page.Request.QueryString["code"]))
    {
        Tuple<Tuple<string, DateTimeOffset>, string> tokens = null;
        try
        {
            tokens = AcquireTokensUsingAuthCode(page.Request.QueryString["code"]);
        }
        catch 
        {
            page.Response.Redirect(AuthorizeUrl);
        }
        AccessToken = tokens.Item1;
        RefreshToken = tokens.Item2;
        return;
    }
    else
    {
        page.Response.Redirect(AuthorizeUrl);
    }
}
  ```


> **CONSEIL**
> La classe AADAuthHelper n'assure qu'une gestion minimale des erreurs. Pour obtenir un Complément SharePoint résistant, de qualité de production, appliquez un niveau supérieur de gestion des erreurs en suivant les indications décrites dans ce nœud MSDN :  [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx). 
  
    
    


### Création des classes de modèle de données


1. Créez une ou plusieurs classes pour modéliser les données que votre complément reçoit de la part de SAP. Dans l'exemple de cet article, il n'existe qu'une seule classe de modèle de données. Cliquez avec le bouton droit sur le projet ASP.NET et utilisez le processus d'ajout d'élément Visual Studio pour ajouter un nouveau fichier de classe au projet nommé Automobile.cs.
    
  
2. Ajoutez le code suivant dans le corps de la classe :
    
  ```
  
public string Price;
public string Brand;
public string Model;
public int Year;
public string Engine;
public int MaxPower;
public string BodyStyle;
public string Transmission;
  ```


### Ajout de code-behind pour obtenir des données de la part de SAP via la Passerelle SAP pour Microsoft


1. Ouvrez le fichier Default.aspx.cs, puis ajoutez-y les instructions **using** ci-dessous.
    
  ```
  
using System.Net;
using Newtonsoft.Json.Linq;
  ```

2. Ajoutez une déclaration **const** à la classe Default dont la valeur est l'URL de base du point de terminaison OData SAP à laquelle le complément accédera. En voici un exemple :
    
  ```
  
private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
  ```

3. Les Outils de développement Office pour Visual Studio ont ajouté une méthode **Page_PreInit** et une méthode **Page_Load**. Commentez le code qui se trouve à l'intérieur de la méthode **Page_Load**, puis commentez l'ensemble de la méthode **Page_Init**. Ce code n'est pas utilisé dans l'exemple actuel. (Si votre Complément SharePoint doit accéder à SharePoint, restaurez ce code. Voir la rubrique  [Ajout de l'accès à SharePoint à l'application ASP.NET (facultatif)](#SharePoint).)
    
  
4. Ajoutez la ligne suivante au début de la méthode **Page_Load**. Cela facilitera le processus de débogage, car votre application ASP.NET communique avec la Passerelle SAP pour Microsoft à l'aide du protocole SSL (HTTPS), mais votre serveur « localhost:port » n'est pas configuré pour approuver le certificat de la Passerelle SAP pour Microsoft. Sans cette ligne de code, vous obtiendrez un avertissement vous informant que le certificat est invalide avant l'ouverture de la page Default.aspx. Certains navigateurs vous permettent de cliquer après cette erreur, mais certains ne vous laisseront pas ouvrir la page Default.aspx.
    
  ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
  ```


    > **IMPORTANTE**
      > Supprimez cette ligne lorsque vous êtes prêt à déployer l'application ASP.NET en phase de préparation. Voir la section  [Modification du complément et préparation de celui-ci sur Azure et Office 365](#Stage). 
5. Ajoutez le code suivant à la méthode **Page_Load**. La chaîne que vous transmettez à la méthode  `GetSAPData` est une requête OData.
    
  ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

  ```

6. Ajoutez la méthode suivante à la classe Default. Cette méthode vérifie d'abord que la mise en cache du jeton d'accès possède un jeton d'accès valide qui a été obtenu à partir d'Azure AD. Il crée ensuite une demande HTTP **GET** dans laquelle le jeton d'accès est inclus puis l'envoie au point de terminaison OData SAP. Le résultat est renvoyé comme un objet JSON qui est converti en un objet de **List**.NET. Trois propriétés des éléments sont utilisées dans un tableau qui est lié à la **DataListView**.
    
  ```
  
private void GetSAPData(string oDataQuery)
{
    AADAuthHelper.EnsureValidAccessToken(this);

    using (WebClient client = new WebClient())
    {
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.Authorization] = "Bearer " + AADAuthHelper.AccessToken.Item1;
        var jsonString = client.DownloadString(SAP_ODATA_URL + oDataQuery);
        var jsonValue = JObject.Parse(jsonString)["d"]["results"];
        var dataCol = jsonValue.ToObject<List<Automobile>>();

        var dataList = dataCol.Select((item) => {
            return item.Brand + " " + item.Model + " " + item.Price;
            }).ToArray();

        DataListView.DataSource = dataList;
        DataListView.DataBind();
    }
}

  ```


### Création de l'interface utilisateur


1. Ouvrez le fichier Default.aspx et ajoutez le balisage suivant à l'élément **form** de la page :
    
  ```
  
<div>
  <h3>Data from SAP via SAP Gateway for Microsoft</h3>

  <asp:ListView runat="server" ID="DataListView">
    <ItemTemplate>
      <tr runat="server">
        <td runat="server">
          <asp:Label ID="DataLabel" runat="server"
            Text="<%# Container.DataItem.ToString()%>" /><br />
        </td>
      </tr>
    </ItemTemplate>
  </asp:ListView>
</div>
  ```

2. Vous pouvez éventuellement donner à la page web l'« apparence » d'une page SharePoint à l'aide du  [contrôle Chrome](use-the-client-chrome-control-in-sharepoint-add-ins.md)SharePoint et de la  [feuille de style de site web hôte SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md).
    
  

### Test du complément avec F5 dans Visual Studio


1. Dans Visual Studio, appuyez sur la touche F5.
    
  
2. Lorsque vous utilisez la touche F5 pour la première fois, il est possible que vous soyez invité à vous connecter au Site du développeur que vous êtes en train d'utiliser. Utilisez les informations d'identification d'administrateur du site. Dans l'exemple de cet article, nous utiliserons Bob@<domaine_O365>.onmicrosoft.com.
    
  
3. Lorsque vous utilisez la touche F5 pour la première fois, vous êtes invité à accorder des autorisations au complément. Cliquez sur **Approuver**.
    
  
4. Après un bref délai au cours duquel le jeton d'accès est obtenu, la page Default.aspx s'ouvre. Vérifiez que les données SAP s'affichent.
    
  

## Ajout de l'accès à SharePoint à l'application ASP.NET (facultatif)
<a name="SharePoint"> </a>

Bien entendu, votre Complément SharePoint n'est pas tenu d'exposer seulement des données SAP sur une page web lancée depuis SharePoint. Il peut également créer, lire, mettre à jour et supprimer (opérations CRUD) des données SharePoint. Votre code-behind peut le faire en utilisant soit le modèle objet client (CSOM) SharePoint, soit les API REST de SharePoint. Le modèle CSOM est déployé comme une paire d'assemblies automatiquement inclus au projet ASP.NET par les Outils de développement Office pour Visual Studio et définis sur **Copie locale** dans Visual Studio afin d'être inclus dans le package de l'application ASP.NET. Pour en savoir plus sur l'utilisation du modèle CSOM, commencez par consulter la rubrique [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md). Pour en savoir plus sur l'utilisation des API REST, commencez par consulter la rubrique  [Comprendre et utiliser l'interface REST de SharePoint 2013](http://msdn.microsoft.com/fr-fr/magazine/dn198245.aspx). 
  
    
    
Quoi qu'il en soit, que vous utilisiez le modèle CSOM ou les API REST pour accéder à SharePoint, votre application ASP.NET doit obtenir un jeton d'accès à SharePoint, comme pour la Passerelle SAP pour Microsoft. Voir la rubrique  [Comprendre l'authentification et l'autorisation à la Passerelle SAP pour Microsoft et à SharePoint](#AuthOverview) ci-dessus. La procédure suivante fournit quelques conseils de base sur la façon d'effectuer cette opération, mais nous vous recommandons de lire d'abord les articles suivants :
  
    
    

-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Trois systèmes d'autorisation pour les compléments SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Création de compléments pour SharePoint qui utilisent l'autorisation de faible niveau de fiabilité](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
-  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  

1. Ouvrez le fichier Default.aspx.cs et supprimez le commentaire de code de la méthode **Page_PreInit**. Supprimez également le commentaire de code que les Outils de développement Office pour Visual Studio ont ajouté à la méthode **Page_Load**.
    
  
2. Si votre Complément SharePoint doit accéder aux données SharePoint, vous devez mettre en cache le jeton de contexte SharePoint qui est envoyé via une requête POST à la page Default.aspx lorsque le complément est lancé dans SharePoint. Il s'agit de s'assurer que le jeton de contexte SharePoint ne se perd pas lorsque le navigateur est redirigé après l'authentification Azure AD. (Il existe plusieurs façons de mettre en cache ce jeton de contexte.) Les Outils de développement Office pour Visual Studio ajoutent un fichier SharePointContext.cs au projet ASP.NET qui fait l'essentiel du travail. Pour utiliser le cache de sessions, il vous suffit d'ajouter le code suivant à l'intérieur du bloc «  `if (!IsPostBack)` » *avant*  le code qui appelle la Passerelle SAP pour Microsoft :
    
  ```
  
if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
  ```

3. Le fichier SharePointContext.cs appelle un autre fichier que les Outils de développement Office pour Visual Studio ont ajouté au projet : TokenHelper.cs. Ce fichier fournit la plupart du code nécessaire pour obtenir et utiliser les jetons d'accès à SharePoint. Cependant, il ne fournit aucun code pour renouveler un jeton d'accès ou d'actualisation expiré. Il ne contient pas non plus de code de mise en cache de jeton. Pour obtenir un Complément SharePoint de qualité de production, vous devez ajouter ce code. La logique de mise en cache de l'étape précédente est un exemple. Votre code devrait également mettre en cache le jeton d'accès et le réutiliser jusqu'à son expiration. Lorsque le jeton d'accès expire, votre code doit utiliser le jeton d'actualisation pour obtenir un nouveau jeton d'accès.
    
  
4. Ajoutez les appels de données à SharePoint à l'aide du modèle CSOM ou de REST. L'exemple suivant est une modification du code CSOM que les Outils de développement Office pour Visual Studio ajoutent à la méthode **Page_Load**. Dans cet exemple, le code a été déplacé dans une méthode distincte et commence par récupérer le jeton de contexte mis en cache.
    
  ```
  
private void GetSharePointTitle()
{
    var spContext = HttpContext.Current.Session["SharePointContext"] as SharePointContext;
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        clientContext.Load(clientContext.Web, web => web.Title);
        clientContext.ExecuteQuery();
        SharePointTitle.Text = "SharePoint web site title is: " + clientContext.Web.Title;
    }
}
  ```

5. Ajoutez des éléments de l'interface utilisateur pour afficher les données SharePoint. L'encadré suivant montre le contrôle HTML auquel il est fait référence dans la méthode précédente :
    
  ```
  
<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
  ```


> **REMARQUE**
> Pendant que vous déboguez le Complément SharePoint, les Outils de développement Office pour Visual Studio l'inscrivent à nouveau auprès d'Azure ACS chaque fois que vous appuyez sur F5 dans Visual Studio. Lorsque vous préparez le Complément SharePoint, vous devez lui attribuer une inscription sur le long terme. Voir la section  [Modification du complément et préparation de celui-ci sur Azure et Office 365](#Stage). 
  
    
    


## Modification du complément et préparation de celui-ci sur Azure et Office 365
<a name="Stage"> </a>

Lorsque vous avez terminé le débogage du Complément SharePoint à l'aide de la touche F5 dans Visual Studio, vous devez déployer l'application ASP.NET dans un Site web Azure réel.
  
    
    

### Création du site web Azure


1. Sur le portail Microsoft Azure, ouvrez **SITES WEB** dans la barre de navigation de gauche.
    
  
2. Cliquez sur **NOUVEAU** en bas de la page, puis, dans la boîte de dialogue **NOUVEAU**, sélectionnez **SITE WEB** |**CRÉATION RAPIDE**.
    
  
3. Entrez un nom de domaine, puis cliquez sur **CRÉER UN SITE WEB**. Copiez l'URL du nouveau site. Elle ressemblera à ceci :  `my_domain.azurewebsites.net`.
    
  

### Modification du code et du balisage dans l'application


1. Dans Visual Studio, supprimez la ligne  `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;` du fichier Default.aspx.cs.
    
  
2. Ouvrez le fichier web.config du projet ASP.NET et remplacez la valeur de la partie de domaine de la valeur de la clé **AppRedirectUrl** de la section **appSettings** par le domaine du Site web Azure. Par exemple, remplacez `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` par `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`.
    
  
3. Cliquez avec le bouton droit sur le fichier AppManifest.xml dans le projet de Complément SharePoint et sélectionnez **Afficher le code**.
    
  
4. Dans la valeur **StartPage**, remplacez la chaîne  `~remoteAppUrl` par le domaine complet du Site web Azure, y compris le protocole. Par exemple : `https://my_domain.azurewebsites.net`. Maintenant, la valeur **StartPage** entière doit se présenter comme suit : `https://my_domain.azurewebsites.net/Pages/Default.aspx`. (Généralement, la valeur **StartPage** est identique à la valeur de la clé **AppRedirectUrl** dans le fichier web.config.)
    
  

### Modification de l'inscription AAD et inscription du complément auprès d'ACS


1. Connectez-vous au  [portail de gestion Azure](https://manage.windowsazure.com) avec votre compte Administrateur Azure.
    
  
2. Choisissez **Active Directory** sur la gauche.
    
  
3. Cliquez sur votre répertoire.
    
  
4. Sélectionnez **APPLICATIONS** (sur la barre de navigation supérieure).
    
  
5. Ouvrez l'application que vous avez créée. Dans l'exemple de cet article, il s'agit de **ContosoAutomobileCollection**.
    
  
6. Pour chacune des valeurs suivantes, remplacez la partie « localhost: *port*  » de la valeur par le domaine de votre nouveau Site web Azure :
    
  - **URL DE CONNEXION**
    
  
  - **URI ID D'APPLICATION**
    
  
  - **URL DE RÉPONSE**
    
  

    Par exemple, si l' **URI ID D'APPLICATION** est **https://localhost:44304/ContosoAutomobileCollection**, remplacez-la par **https://<mon_domaine>.azurewebsites.net/ContosoAutomobileCollection**.
    
  
7. Cliquez sur **ENREGISTRER** en bas de l'écran.
    
  
8. Inscrivez le complément auprès d'Azure ACS. Vous devez procéder à cette inscription même si le complément n'accédera pas à SharePoint et même s'il ne sera pas amené à utiliser des jetons provenant du service ACS, car le même processus inscrit également le complément auprès du service de gestion des compléments de l'abonnement Office 365, et ceci est obligatoire. (Le nom « service de gestion des applications » vient du fait que les Compléments SharePoint étaient à l'origine appelés « applications pour SharePoint ».) Inscrivez-le sur la page AppRegNew.aspx d'un site web SharePoint dans l'abonnement Office 365. Pour en savoir plus, voir  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md). Dans le cadre de ce processus, vous obtiendrez un nouvel ID client et une nouvelle clé secrète client. Insérez ces valeurs dans le fichier web.config pour obtenir les clés **ClientId** (et non **ida:ClientID**) et **ClientSecret**.
    
    > **ATTENTION**
      > Si vous appuyez sur la touche F5 après avoir fait ce changement, les Outils de développement Office pour Visual Studio écraseront une des valeurs ou les deux. Pour cette raison, vous devez conserver une trace des valeurs obtenues avec AppRegNew.aspx et toujours vérifier que les valeurs dans le fichier web.config sont correctes juste avant de publier l'application ASP.NET. 

### Publication du complément ASP.NET sur Azure et installation du complément sur SharePoint


1. Il existe plusieurs façons de publier une application ASP.NET sur un Site web Azure. Pour en savoir plus, voir  [Déploiement d'un site web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-deploy/).
    
  
2. Dans Visual Studio, cliquez avec le bouton droit sur le projet de complément SharePoint et sélectionnez **Package**. Sur la page **Publier votre complément** qui s'ouvre, cliquez sur **Empaqueter le complément**. L'explorateur de fichiers s'ouvre et affiche le dossier contenant le package de complément.
    
  
3. Connectez-vous à Office 365 en tant qu'administrateur général, puis accédez à la collection de sites du catalogue de compléments de l'organisation. (S'il n'en existe aucune, créez-en une. Voir la page  [Utiliser le catalogue de compléments pour mettre à disposition des compléments d'entreprise personnalisés pour votre environnement SharePoint Online](http://office.microsoft.com/fr-fr/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).)
    
  
4. Téléchargez le package de complément vers le catalogue de compléments.
    
  
5. Accédez à la page **Contenu du site** d'un site web de l'abonnement et cliquez sur **Ajouter un complément**.
    
  
6. Sur la page **Vos compléments**, faites défiler jusqu'à la section **Compléments que vous pouvez ajouter** et cliquez sur l'icône de votre complément.
    
  
7. Après avoir installé le complément, cliquez sur son icône sur la page **Contenu du site** pour lancer le complément.
    
  
Pour plus d'informations sur l'installation des Compléments SharePoint, voir  [Déploiement et installation de compléments pour SharePoint : méthodes et options](deploying-and-installing-sharepoint-add-ins-methods-and-options.md).
## Déploiement du complément dans un environnement de production
<a name="Stage"> </a>

Après avoir effectué tous les tests, vous pouvez déployer le complément dans un environnement de production. Pour ce faire, quelques modifications peuvent être nécessaires.
  
    
    

1. Si le domaine de production de l'application ASP.NET est différent du domaine de préparation, vous devez changer la valeur **AppRedirectUrl** dans le fichier web.config et la valeur **StartPage** dans le fichier AppManifest.xml, puis repackager le Complément SharePoint. Voir la procédure **Modification du code et du balisage dans l'application** ci-dessus.
    
  
2. Pour modifier le domaine, vous devez également modifier l'inscription AAD des compléments. Voir la procédure **Modification de l'inscription AAD et inscription du complément auprès d'ACS** ci-dessus.
    
  
3. Pour modifier le domaine, vous devez également inscrire à nouveau le complément auprès du service ACS (et du service de gestion des compléments de l'abonnement) comme indiqué dans la même procédure. (Il est impossible de  *modifier*  l'inscription d'un complément auprès du service ACS). Toutefois, il n'est pas nécessaire de générer un nouvel ID client ou une nouvelle clé secrète client sur la page AppRegNew.aspx. Vous pouvez copier les valeurs d'origine des clés **ClientId** (et non de **ida:ClientID**) et **ClientSecret** du fichier web.config dans le formulaire AppRegNew. *Si vous en générez des nouveaux, veillez à copier les nouvelles valeurs dans les clés du fichier web.config.* 
    
  

