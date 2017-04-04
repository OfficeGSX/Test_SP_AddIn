---
title: Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines
ms.prod: SHAREPOINT
ms.assetid: bc37ff5c-1285-40af-98ae-01286696242d
---



# Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines
Découvrez comment accéder aux données d'un site web SharePoint à partir de votre complément à l'aide de la bibliothèque inter-domaines dans SharePoint 2013.
Quand vous créez des Compléments SharePoint, vous devez généralement incorporer des données en provenance de différentes sources. Toutefois, pour des  [raisons de sécurité](http://msdn.microsoft.com/library/cc709423.aspx), des mécanismes de blocage empêchant la communication avec plusieurs domaines à la fois sont mis en place. Ces mécanismes de sécurité sont présents dans la plupart des navigateurs, ce qui rend difficile voire impossible d'effectuer des appels côté client sur plusieurs domaines.
  
    
    

La figure 1 illustre une requête bloquée sur plusieurs domaines.
**Figure 1. Requête bloquée sur plusieurs domaines**

  
    
    
Lorsqu'un utilisateur demande une page du domaine de votre complément (1), la communication côté client est uniquement liée à ce domaine. Votre complément peut émettre des appels côté client à partir de la page uniquement vers d'autres ressources dans le même domaine. Toutefois, les compléments nécessitent généralement des ressources d'autres domaines, tels que le domaine SharePoint, pour accomplir leurs scénarios. Dans le code de votre page, vous pouvez essayer d'émettre une requête vers le domaine SharePoint (2) qui est bloqué par le navigateur. Généralement, une erreur **Accès refusé** s'affiche. Cela ne signifie pas que vous ne disposez pas des autorisations aux ressources demandées mais, plus probablement, que vous ne pouvez même pas émettre une requête vers les ressources mentionnées.Lorsque vous utilisez la bibliothèque inter-domaines, les pages web de votre complément peuvent accéder aux données du domaine de votre complément et du domaine SharePoint. La bibliothèque inter-domaines est une alternative côté client ayant la forme d'un fichier JavaScript (SP.RequestExecutor.js) hébergé sur le site web SharePoint que vous pouvez référencer dans votre complément distant. La bibliothèque inter-domaines vous permet d'interagir avec plusieurs domaines dans la page de votre complément distant via un proxy. C'est une bonne option si vous préférez que le code de votre complément s'exécute dans le client plutôt que sur le serveur, et s'il existe des barrières de connectivité telles que des pare-feu, entre SharePoint et votre infrastructure distante. Vous pouvez accéder aux données dans le site web hôte (par exemple, vous pouvez accéder à des listes avec lesquelles les utilisateurs finaux interagissent indépendamment de votre complément). Vous pouvez également accéder aux données sur le site web du complément, telles que des listes spécifiquement fournies pour votre complément. Les compléments peuvent aussi accéder à d'autres collections de sites et sites web tant que le complément dispose d'autorisations d'étendue client et est déployé en tant qu'installation par lots à l'aide du catalogue de compléments.
> [!REMARQUE]
> Dans cette rubrique, **domaine de votre complément** fait référence au domaine qui héberge les pages du complément. Il peut s'agir du domaine d'une application web distante dans une application hébergée par un fournisseur, mais les pages du complément peuvent également se trouver sur SharePoint dans le site web du complément et effectuer des appels vers le domaine du site web hôte. Dans ce dernier scénario, le domaine du complément est le domaine du site web du complément.
  
    
    

L'exemple principal de cet article montre comment créer un complément qui lit les données sur le site web du complément et les affiche dans une page web. La section  [Étapes suivantes](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Next) présente plusieurs scénarios qui s'appuient sur l'exemple principal.
## Conditions préalables à l'utilisation des exemples de cet article
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Pour suivre les exemples de cet article, vous devez disposer des éléments suivants :
  
    
    

-  [Visual Studio 2012](https://www.microsoft.com/fr-fr/download/details.aspx?id=30682)
    
  
-  [Outils de développement Microsoft Office pour Visual Studio 2012](https://msdn.microsoft.com/fr-fr/office/aa905340.aspx)
    
  
- Un environnement de développement SharePoint 2013 (isolation de l'application requise pour les scénarios locaux)
    
  

## Lire des données sur le site web de complément à l'aide de la bibliothèque inter-domaines
<a name="SP15Accessdatafromremoteapp_Codeexample"> </a>

Dans cet exemple, on utilise une page simple hébergée à l'extérieur de SharePoint utilisant un point de terminaison Representational State Transfer (REST) pour lire des données dans un site web SharePoint (le site web du complément). Puisque la bibliothèque inter-domaines nécessite un site web de complément, il est logique de commencer par ce scénario.
  
    
    
Pour lire les données à partir du site web du complément, vous devez effectuer les opérations suivantes :
  
    
    

1. Créer un Complément SharePoint et des projets web.
    
  
2. Créer des éléments de liste dans le site web du complément. Cette étape garantit également qu'un site web de complément est créé lorsque les utilisateurs déploient le complément.
    
  
3. Créer une page de complément qui utilise la bibliothèque inter-domaines pour lire les éléments de liste.
    
  
La figure 2 illustre une page web qui affiche les données sur le site web du complément.
  
    
    

**Figure 2. Page web qui affiche les données sur le site web du complément**

  
    
    

  
    
    
![Exemple d'écran de résultats d'éléments en lecture inter-domaines](images/CrossDomainReadItemsResult.png)
  
    
    

### Créer un Complément SharePoint et des projets web


1. Ouvrez Visual Studio 2012 en tant qu'administrateur. (Pour cela, cliquez avec le bouton droit sur l'icône Visual Studio 2012 dans le menu **Démarrer** et choisissez **Exécuter en tant qu'administrateur**.)
    
  
2. Créez un projet à l'aide du modèle **Complément pour SharePoint 2013**.
    
    Le modèle **Complément pour SharePoint 2013** dans Visual Studio 2012 se trouve sous **Modèles** **>** **Visual C#**, **Office/SharePoint** **>** **Compléments**.
    
  
3. Fournissez l'URL du site web SharePoint que vous souhaitez utiliser pour le débogage.
    
  
4. Sélectionnez **Hébergement par le fournisseur** comme option d'hébergement pour votre complément.
    
    > [!REMARQUE]
      > Vous pouvez également utiliser la bibliothèque inter-domaines dans une Application hébergée par SharePoint. Cependant, dans une Application hébergée par SharePoint, la page du complément se trouve déjà dans le site web du complément, et, dans ce cas, elle n'a pas besoin que la bibliothèque inter-domaines lise les éléments de liste. Pour obtenir un exemple d'Application hébergée par SharePoint qui lit des données dans le site web hôte, voir la page relative à l' [utilisation de la bibliothèque inter-domaines dans un complément hébergé par SharePoint (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814) ou la section [Accéder aux données à partir du site web hôte](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) plus loin dans cet article.

### Créer des éléments de liste sur le site web du complément


1. Cliquez avec le bouton droit sur le projet de Complément SharePoint dans l' **Explorateur de solutions**. Cliquez sur **Ajouter** **>** **Nouvel élément...**.
    
  
2. Sélectionnez **Éléments Visual C#** **>** **Office/SharePoint** **>** **Liste**. Définissez le nom de votre liste sur **Annonces**.
    
  
3. Double-cliquez sur **Annonces** **>** **Elements.xml**. Collez les nœuds XML suivants en tant qu'enfants de l'élément **ListInstance**.
    
  ```
  
<Data>
    <Rows>
        <Row>
            <Field Name="Title">Lorem ipsum 1</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
        <Row>
            <Field Name="Title">Lorem ipsum 2</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
    </Rows>
</Data>
  ```


### Pour ajouter une nouvelle page qui utilise la bibliothèque inter-domaines


1. Double-cliquez sur le fichier **Default.aspx** dans le projet web dans l' **Explorateur de solutions**.
    
  
2. Copiez le code ci-dessous et collez-le dans le fichier Default.aspx. Ce code effectue les tâches suivantes :
    
  - Charge la bibliothèque jQuery à partir du réseau de distribution de contenu Microsoft.
    
  
  - Fournit un espace réservé pour le résultat.
    
  
  - Extrait l'URL du site web du complément de la chaîne de requête.
    
  
  - Charge le code JavaScript de la bibliothèque inter-domaines à l'aide de la fonction **getScript** dans jQuery.
    
    Cette fonction charge les ressources nécessaires, puis continue vers la fonction spécifiée, en veillant à ce que la bibliothèque inter-domaines soit chargée et disponible pour être utilisée par le code suivant.
    
  
  - Instancie l'objet **RequestExecutor**. Par défaut, RequestExecutor utilise le site web du complément comme site de contexte.
    
    > [!REMARQUE]
      > Vous pouvez remplacer le site de contexte par des sites autres que le site web du complément en utilisant le point de terminaison (REST) ou l'objet (JSOM) **AppContextSite**. Pour en savoir plus sur AppContextSite, voir la section  [Accéder aux données à partir du site web hôte](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) plus loin dans cet article.
  - Émet un appel REST à destination du point de terminaison des éléments de liste.
    
  
  - Gère la réussite de l'opération, en affichant les éléments de liste sur la page web.
    
  
  - Gère les erreurs, en affichant le message d'erreur sur la page web.
    
  

  ```
  
<html>
    <head>
        <title>Cross-domain sample</title>
    </head>
    <body>
        <!-- This is the placeholder for the announcements -->
        <div id="renderAnnouncements"></div>
        <script 
            type="text/javascript" 
            src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
        </script>
        <script type="text/javascript">
          var hostweburl;
          var appweburl;

          // Load the required SharePoint libraries
          $(document).ready(function () {
            //Get the URI decoded URLs.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );

            // resources are in URLs in the form:
            // web_url/_layouts/15/resource
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js files and continue to the successHandler
            $.getScript(scriptbase + "SP.RequestExecutor.js", execCrossDomainRequest);
          });

          // Function to prepare and issue the request to get
          //  SharePoint data
          function execCrossDomainRequest() {
            // executor: The RequestExecutor object
            // Initialize the RequestExecutor with the add-in web URL.
            var executor = new SP.RequestExecutor(appweburl);

            // Issue the call against the add-in web.
            // To get the title using REST we can hit the endpoint:
            //      appweburl/_api/web/lists/getbytitle('listname')/items
            // The response formats the data in the JSON format.
            // The functions successHandler and errorHandler attend the
            //      sucess and error events respectively.
            executor.executeAsync(
                {
                  url:
                      appweburl +
                      "/_api/web/lists/getbytitle('Announcements')/items",
                  method: "GET",
                  headers: { "Accept": "application/json; odata=verbose" },
                  success: successHandler,
                  error: errorHandler
                }
            );
          }

          // Function to handle the success event.
          // Prints the data to the page.
          function successHandler(data) {
            var jsonObject = JSON.parse(data.body);
            var announcementsHTML = "";

            var results = jsonObject.d.results;
            for (var i = 0; i < results.length; i++) {
              announcementsHTML = announcementsHTML +
                  "<p><h1>" + results[i].Title +
                  "</h1>" + results[i].Body +
                  "</p><hr>";
            }

            document.getElementById("renderAnnouncements").innerHTML =
                announcementsHTML;
          }

          // Function to handle the error event.
          // Prints the error message to the page.
          function errorHandler(data, errorCode, errorMessage) {
            document.getElementById("renderAnnouncements").innerText =
                "Could not complete cross-domain call: " + errorMessage;
          }

          // Function to retrieve a query string value.
          // For production purposes you may want to use
          //  a library to handle the query string.
          function getQueryStringParameter(paramToRetrieve) {
            var params =
                document.URL.split("?")[1].split("&amp;");
            var strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
              var singleParam = params[i].split("=");
              if (singleParam[0] == paramToRetrieve)
                return singleParam[1];
            }
          }
        </script>
    </body>
</html>
  ```


### Pour générer et exécuter la solution


1. Appuyez sur la touche F5.
    
    > [!REMARQUE]
      > Lorsque vous appuyez sur F5, Visual Studio génère la solution, déploie le complément et ouvre la page des autorisations pour le complément. 
2. Cliquez sur le bouton **Trust It** (Faire confiance).
    
  
3. Cliquez sur l'icône du complément de la page **Contenu du site**.
    
  
Si vous préférez des exemples de code téléchargeables, vous pouvez obtenir cet exemple à partir de la bibliothèque de code. **Exemple de code : obtenir des éléments de liste à l'aide de la bibliothèque inter-domaines** à l'aide de [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain) ou [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain).
  
    
    

**Tableau 2. Dépannage de la solution**


|**Si vous voyez ceci...**|**Essayez ceci...**|
|:-----|:-----|
|Message d'erreur : désolé, nous rencontrons des difficultés lors de l'accès à votre site.  <br/> Il existe également un bouton pour corriger l'erreur, mais il ne résout pas le problème.  <br/> |Le problème que vous rencontrez est peut-être connu et lié aux zones de sécurité dans Internet Explorer, consultez  [Utiliser la bibliothèque inter-domaines dans différentes zones de sécurité Internet Explorer dans les compléments pour SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md).  <br/> |
|Message d'erreur : les fonctionnalités requises ne sont pas prises en charge par votre navigateur. Veillez à utiliser IE 8 ou supérieur, ou un autre navigateur moderne. Assurez-vous que la balise meta « X-UA-Compatible » est définie sur « IE=8 » ou sur une valeur supérieure.  <br/> |La bibliothèque inter-domaines nécessite un mode de document **IE8** ou supérieur. Dans certains scénarios, le mode de document est défini sur **IE7** par défaut. Vous pouvez utiliser les outils de développement d'Internet Explorer pour déterminer et modifier le mode de document de votre page. Pour plus d'informations, voir [Définition de la compatibilité des documents](http://msdn.microsoft.com/library/cc288325.aspx).  <br/> |
|Message d'erreur : le « type » n'est pas défini.  <br/> De plus, votre complément utilise le modèle objet JavaScript (JSOM).  <br/> |Le JSOM utilise la méthode **Type.registerNamespace** dans la bibliothèque Microsoft Ajax pour enregistrer l'espace de noms **SP**. Utilisez le code suivant pour ajouter une référence à la bibliothèque Microsoft Ajax à partir de votre page : <br/> ```HTML<script type="text/javascript"  src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js"></script>```|
   

## Étapes suivantes
<a name="SP15Accessdatafromremoteapp_Next"> </a>

Cet article indique comment demander à un point de terminaison REST de lire les données à partir du site web du complément en utilisant une page du complément qui n'est pas hébergée sur SharePoint. Vous pouvez également explorer les scénarios et les détails suivants à propos de la bibliothèque inter-domaines.
  
    
    

### Utiliser le JSOM pour lire des données à partir du site web du complément
<a name="SP15Accessdatafromremoteapp_JSOM"> </a>

Selon vos préférences, vous pouvez utiliser le JSOM au lieu de REST pour interroger les données à partir du site web du complément. Vous devez effectuer des tâches supplémentaires pour utiliser la bibliothèque inter-domaines avec JSOM :
  
    
    

- Référencer le JSOM SharePoint dans la page de votre complément.
    
  
- Initialiser l'objet **ProxyWebRequestExecutorFactory** et le définir en tant que fabrique de l'objet de contexte.
    
  
- Accéder aux objets SharePoint pour lire les données de la liste.
    
  
- Charger les objets dans le contexte et exécuter la requête.
    
  
Pour obtenir un exemple de code qui indique comment exécuter les tâches, voir  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain). Pour plus d'informations sur la manière d'utiliser le JSOM, consultez l'article relatif à l' [utilisation du modèle objet JavaScript (JSOM) dans les compléments SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/09/04/using-the-javascript-object-model-jsom-in-apps-for-sharepoint.aspx).
  
    
    

### Accéder aux données à partir du site web hôte
<a name="SP15Accessdatafromremoteapp_Hostweb"> </a>

L'exemple de cette page indique comment lire les données à partir du site web du complément. Il s'agit d'un bon premier exemple car la bibliothèque inter-domaines utilise d'abord le complément en tant que site de contexte. Cependant, il existe de nombreux scénarios pour lesquels vous souhaiterez accéder aux données sur le site web hôte. Quelques tâches sont requises pour accéder aux données sur le site web hôte :
  
    
    

- Définir le site web hôte en tant que site de contexte pour la bibliothèque inter-domaines.
    
  
- Fournir les autorisations appropriées pour accéder au complément.
    
  
Vous pouvez modifier le site de contexte en utilisant le point de terminaison (REST) ou l'objet (JSOM) **AppContextSite**. L'exemple suivant montre comment changer le site de contexte, à l'aide du point de terminaison REST :
  
    
    



```

executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/title?@target='" +
            hostweburl + "'",
        method: "GET",
        headers: { "Accept": "application/json; odata=verbose" },
        success: successHandler,
        error: errorHandler
    }
);
```

L'exemple de code suivant montre comment modifier le site de contexte à l'aide du JSOM :
  
    
    



```

context = new SP.ClientContext(appweburl);
factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
context.set_webRequestExecutorFactory(factory);
appContextSite = new SP.AppContextSite(context, hostweburl);

this.web = appContextSite.get_web();
context.load(this.web);
```

Par défaut, votre complément dispose des autorisations pour accéder au site web du complément, mais pas au site web hôte. L'exemple suivant montre une section de manifeste déclarant une demande d'autorisation pour lire les données du site web hôte :
  
    
    



```XML

<AppPermissionRequests>
    <AppPermissionRequest 
        Scope="http://sharepoint/content/sitecollection/web" 
        Right="Read" />
</AppPermissionRequests>
```

Veillez à créer une ressource sur le site web du complément (comme une page ou une liste vide) pour forcer la mise à disposition du site web du complément, laquelle est nécessaire pour utiliser la bibliothèque inter-domaines.
  
    
    

### Accéder aux données sur plusieurs collections de sites
<a name="SP15Accessdatafromremoteapp_TenantScope"> </a>

Avec la bibliothèque inter-domaines, vous pouvez accéder aux données de plusieurs collections de sites dans le même client. Vous devez effectuer certaines tâches pour accéder aux données de plusieurs collections de sites :
  
    
    

- Ajouter une demande d'autorisation pour accéder aux données dans le client.
    
  
- Dans votre code, basculez le site de contexte sur les collections de sites que vous souhaitez interroger.
    
  
- Ajouter le complément au catalogue de compléments.
    
  
- Déployer le complément en tant que complément d'étendue client sur un site web. Pour obtenir un exemple de la façon de déployer un complément d'étendue client, consultez la description de l'exemple de code sur la page relative à l' [utilisation de la bibliothèque inter-domaines dans une application d'étendue client (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e).
    
  
Votre complément doit également disposer d'autorisations pour accéder aux données du client. L'exemple suivant montre une section de manifeste déclarant une demande d'autorisation pour lire les données du client :
  
    
    



```XML

<AppPermissionRequests>
  <AppPermissionRequest 
    Scope="http://sharepoint/content/tenant" 
    Right="Read" />
</AppPermissionRequests>
```

Pour changer le site de contexte dans votre code, utilisez l'objet (JSOM) ou le point de terminaison **AppContextSite** (REST), comme dans la section [Accéder aux données à partir du site web hôte](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb). Voici à quoi ressemble le point de terminaison REST : /_api/SP.AppContextSite(@target)/web/title?@target='weburl', et un exemple de la procédure d'instantiation de l'objet dans JSOM :  `appContextSite = new SP.AppContextSite(context, weburl);`.
  
    
    
En tant que développeur, vous ne pouvez déployer que des compléments d'étendue client à partir du catalogue de compléments. Vous pouvez fournir un catalogue de compléments dans vos environnements sur site ou SharePoint Online. Le téléchargement de votre complément dans le catalogue de compléments est aussi simple que le téléchargement d'un fichier dans une bibliothèque de documents. Pour obtenir des instructions détaillées, voir  [Ajouter des compléments personnalisés au site de catalogue de compléments](http://office.microsoft.com/fr-fr/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).
  
    
    
À partir du catalogue de compléments, vous pouvez déployer le complément sur un ou plusieurs sites web dans le client. Puisque votre complément dispose des autorisations pour accéder aux données dans le client, vous n'avez besoin de déployer qu'un seul site web pour accéder aux données sur tout le client. Pour obtenir des instructions détaillées sur le déploiement d'un complément à partir du catalogue de compléments, voir  [Déployer un complément personnalisé](http://office.microsoft.com/fr-fr/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).
  
    
    
Pour télécharger un exemple de code qui montre comment accéder aux données dans plusieurs collections de sites, voir la page relative à l' [utilisation de la bibliothèque inter-domaines dans un complément d'étendue client (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e).
  
    
    

### Émettre des appels dans plusieurs zones de sécurité
<a name="SP15Accessdatafromremoteapp_IEZones"> </a>

La bibliothèque inter-domaines utilise une page proxy hébergée dans un **IFrame** sur la page du complément pour permettre la communication. Lorsque la page du complément et le site web SharePoint se trouvent dans des zones de sécurité différentes, les cookies d'autorisation ne peuvent pas être envoyés. En l'absence de cookies d'autorisation, si l'IFrame essaye de charger la page proxy, il est redirigé vers la page de connexion SharePoint. La page de connexion SharePoint ne peut pas se trouver dans un IFrame pour des raisons de sécurité. Dans ces scénarios, la bibliothèque ne peut pas charger la page proxy et la communication avec SharePoint n'est pas possible.
  
    
    
Cependant, il existe une solution pour ces scénarios. La solution est le **modèle apphost**, qui consiste à insérer les pages du complément dans une page hébergée dans le site web du complément. L'utilisation du modèle apphost est recommandée dans les compléments qui utilisent la bibliothèque inter-domaines, même s'il n'existe aucune frontière de sécurité évidente. Pour plus d'informations, voir [Utiliser la bibliothèque inter-domaines dans différentes zones de sécurité Internet Explorer dans les compléments pour SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md).
  
    
    

### Accéder aux données à partir d'un hôte distant supplémentaire dans une Application hébergée par SharePoint
<a name="SP15Accessdatafromremoteapp_SPhosted"> </a>

Par défaut, une Application hébergée par SharePoint est autorisée à émettre des appels inter-domaines vers le site web hôte, à condition qu'elle dispose des autorisations appropriées. Cependant, une Application hébergée par SharePoint peut également spécifier un hôte distant dans l'attribut **AllowedRemoteHostUrl** de son **AppPrincipal**. Cela vous permet effectivement d'émettre des appels inter-domaines à partir du site web du complément et d'un autre hôte, où qu'il soit.
  
    
    
Pour télécharger un exemple d'Application hébergée par SharePoint qui utilise la bibliothèque inter-domaines, voir la page  [Exemple de code : utiliser la bibliothèque inter-domaines dans un complément hébergé par SharePoint (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814).
  
    
    

## Ressources supplémentaires
<a name="SP15Accessdatafromremoteapp_Addresources"> </a>


-  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)
    
  
-  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)
    
  
-  [Exemple de code : obtenir le titre du site web hôte à l'aide de la bibliothèque inter-domaines (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Get-the-0ec36bb6)
    
  
-  [Exemple de code : obtenir le titre du site web hôte à l'aide de la bibliothèque inter-domaines (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)
    
  
-  [Exemple de code : utiliser la bibliothèque inter-domaines dans un complément hébergé par SharePoint (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)
    
  
-  [Exemple de code : utiliser la bibliothèque inter-domaines dans un complément d'étendue client (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)
    
  
-  [Exemple de code : utiliser le contrôle Chrome et la bibliothèque inter-domaines (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)
    
  
-  [Exemple de code : utiliser le contrôle Chrome et la bibliothèque inter-domaines (JSOM)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)
    
  
-  [Exemple de code : utilisation des actions personnalisées et de la bibliothèque à domaines multiples pour commander des livres](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)
    
  
-  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Utiliser la bibliothèque inter-domaines dans différentes zones de sécurité Internet Explorer dans les compléments pour SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  
-  [Créer une page de proxy personnalisée pour la bibliothèque inter-domaines dans SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [Interroger un service distant à l'aide du proxy web](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)
    
  
-  [Configurer un environnement de développement local pour les compléments pour SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
