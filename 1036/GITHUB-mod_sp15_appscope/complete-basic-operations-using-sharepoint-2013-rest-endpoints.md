---
title: Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013
Découvrez comment effectuer des opérations CRUD (create, read, update et delete ; créer, lire, mettre à jour et supprimer) de base à l'aide de l'interface REST SharePoint 2013.
## Développement avec l'interface REST et les API clientes SharePoint
<a name="ClientAPIs"> </a>

Vous pouvez effectuer des opérations basiques de création, lecture, mise à jour et suppression à l'aide de l'interface REST (Representational State Transfer) fournie par SharePoint 2013. L'interface REST expose toutes les entités SharePoint et les opérations disponibles dans les autres API clientes SharePoint SharePoint. Un des avantages de l'utilisation de REST réside dans le fait que vous n'avez pas à ajouter de références aux assemblys clients ou bibliothèques SharePoint 2013. Vous effectuez des demande HTTP aux points de terminaison adéquats pour récupérer ou mettre à jour les entités SharePoint, telles que les sites web, les listes et les éléments de liste. Pour obtenir une présentation de l'interface REST SharePoint 2013 et de son architecture, voir  [Familiarisation avec le service REST SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md).



Les pages  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md) et [Utilisation de dossiers et de fichiers à l'aide de l'interface REST](working-with-folders-and-files-with-rest.md) fournissent des informations détaillées sur la façon de travailler avec des entités SharePoint principales. Voir [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations) pour obtenir un exemple montrant comment effectuer plusieurs de ces opérations dans une application web ASP.NET écrite en C#.



Pour plus d'informations sur les ensembles d'API disponibles sur la plate-forme SharePoint 2013, voir  [Choisir l'ensemble d'API approprié dans SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx). Pour plus d'informations sur l'utilisation des autres API clientes, voir  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) et [Créer des applications Windows Phone accédant à SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx).




## Opérations HTTP dans les services REST SharePoint 2013
<a name="HTTPOps"> </a>

Les points de terminaison dans le service REST SharePoint 2013 correspondent aux types et aux membres des modèles objet client SharePoint. À l'aide de demandes HTTP, vous pouvez utiliser ces points de terminaison REST pour effectuer des opérations CRUD typiques ( **Create**, **Read**, **Update** et **Delete**) sur des entités SharePoint, telles que des listes et des sites.



En général, les points de terminaison qui représentent des opérations **Read** correspondent à des commandes HTTP **GET**. Les points de terminaison qui représentent des opérations de mise à jour correspondent à des commandes HTTP **POST**, et ceux qui représentent des opérations de mise à jour ou d'insertion correspondent à des commandes HTTP **PUT**.



Dans SharePoint 2013, les commandes **POST** permettent de créer des entités, telles que des listes et des sites. Le service REST SharePoint 2013 prend en charge l'envoi de commandes **POST** incluant des définitions d'objet à des points de terminaison représentant des collections. Par exemple, vous pouvez envoyer une commande **POST** incluant une nouvelle définition d'objet de liste au format ATOM à l'URL suivante pour créer une liste SharePoint :



 `http://<site url>/_api/web/lists`



Pour les opérations **POST**, toutes les propriétés qui ne sont pas obligatoires sont définies sur leur valeur par défaut. Si vous tentez de définir une propriété en lecture seule dans le cadre d'une opération **POST**, le service renvoie une exception.



Les opérations **PUT** et **MERGE** permettent de mettre à jour des objets SharePoint existants. Les points de terminaison de service qui représentent une opération **set** sur une propriété d'objet prennent en charge à la fois les demandes **PUT** et **MERGE**. Pour les demandes **MERGE**, la définition des propriétés est facultative ; toute propriété que vous ne définissez pas de manière explicite conserve sa valeur actuelle. Cependant, pour les commandes **PUT**, toutes les propriétés que vous ne définissez pas de manière explicite sont configurées sur leur valeur par défaut. De plus, si vous ne spécifiez pas toutes les propriétés obligatoires pour les mises à jour d'objet lors de l'utilisation des commandes HTTP **PUT**, le service REST renvoie une exception.



Utilisez la commande HTTP **DELETE** sur l'URL d'un point de terminaison spécifique pour supprimer l'objet SharePoint représenté par ce point de terminaison. Dans le cas des objets recyclables, tels que les listes, les fichiers et les éléments de liste, une opération **Recycle** est réalisée.




## Lire des données à l'aide de l'interface REST SharePoint 2013
<a name="ReadingData"> </a>

Pour utiliser les fonctionnalités REST intégrées dans SharePoint 2013, vous devez construire une demande HTTP REST, à l'aide de la norme OData, qui correspond à l'API du modèle d'objet client à utiliser. Chaque entité SharePoint est exposée à un point de terminaison sur le site SharePoint 2013 que vous ciblez, et ses métadonnées sont représentées au format XML ou JSON. Vous pouvez effectuer les demandes HTTP dans n'importe quel langage, notamment JavaScript et C#.



Pour lire des informations à partir d'un point de terminaison REST, vous devez connaître l'URL du point de terminaison et la représentation OData de l'entité SharePoint qui est exposée à ce point de terminaison. Par exemple, pour récupérer toutes les listes sur d'un site SharePoint spécifique, vous devez envoyer une demande **GET** à `http://<site url>/_api/web/lists`. Vous pouvez accéder à cette URL dans votre navigateur et afficher le code XML qui est renvoyé. Lorsque vous effectuez la demande dans du code, vous pouvez spécifier si vous voulez recevoir la représentation OData des listes au format XML ou JSON.



Le code C# suivant montre comment effectuer cette demande **GET** qui renvoie une représentation JSON de toutes les listes d'un site à l'aide de JQuery. Cet exemple de code suppose également que vous disposez d'un jeton d'accès OAuth valide stocké dans la variable **accessToken**. Vous n'avez pas besoin du jeton d'accès si vous effectuez cet appel au sein d'un site web de complément, comme vous le feriez au sein d'un complément hébergé sur SharePoint. Vous ne pouvez pas obtenir de jeton d'accès à partir du code en cours d'exécution sur un client navigateur. Vous devez obtenir le jeton d'accès à partir du code en cours d'exécution sur un serveur. Les rubriques  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) et [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md) expliquent comment obtenir un jeton d'accès.





```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

Cette demande est légèrement différente si vous écrivez votre complément en JavaScript à l'aide de la bibliothèque inter-domaines SharePoint 2013. Dans ce cas, il n'est pas nécessaire de fournir un jeton d'accès. Le code ci-après montre l'aspect de cette demande si vous utilisez la bibliothèque inter-domaines et si vous voulez recevoir la représentation OData des listes au format XML, et non JSON. (Étant donné que le format par défaut est Atom, vous n'avez pas besoin d'inclure d'en-tête **Accept**.) Pour plus d'informations sur l'utilisation de la bibliothèque inter-domaines, voir  [Accéder à des données SharePoint 2013 à partir de compléments à l'aide de la bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).





```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);```

Le code de l'exemple ci-après montre comment demander une représentation JSON de toutes les listes d'un site à l'aide de C#. Il suppose que vous disposez d'un jeton d'accès OAuth stocké dans la variable  `accessToken`.





```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();
```


### Obtention des propriétés qui ne sont pas renvoyées avec la ressource
<a name="NavigationProperties"> </a>

De nombreuses valeurs de propriété sont renvoyées lorsque vous récupérez une ressource. Toutefois, pour certaines propriétés, vous devez envoyer une demande **GET** directement au point de terminaison de la propriété. Il s'agit généralement des propriétés qui représentent des entités SharePoint.



L'exemple suivant montre comment obtenir une propriété en ajoutant le nom de la propriété au point de terminaison de la ressource. L'exemple obtient la valeur de la propriété **Author** d'une ressource **File**



http:// _\<site url\>_/_api/web/getfilebyserverrelativeurl('/ _\<folder name\>_/ _\<file name\>_')/author



Pour obtenir les résultats au format JSON, ajoutez un en-tête **Accept** défini sur `"application/json;odata=verbose"`.




## Écrire des données à l'aide de l'interface REST
<a name="WritingData"> </a>

Vous pouvez créer et mettre à jour des entités SharePoint en construisant des demandes HTTP REST pour les points de terminaison adéquats, comme vous le feriez lors de la lecture de données. Dans ce cas, vous utilisez toutefois une demande **POST**. Lorsque vous mettez à jour des entités, vous passez également une méthode **PUT** ou **MERGE** de demande HTTP en ajoutant un de ces termes aux en-têtes de votre demande comme valeur de la clé **X-HTTP-Method**. La méthode **MERGE** met uniquement à jour les propriétés de l'entité spécifiée, alors que la méthode **PUT** remplace l'entité existante par une nouvelle que vous fournissez dans le corps de la méthode **POST**. Utilisez la méthode **DELETE** pour supprimer l'entité. Lorsque vous créez ou mettez à jour une entité, vous devez fournir une représentation OData de l'entité à créer ou modifier dans le corps de la demande HTTP.



Lors de la création, de la mise à jour et de la suppression d'entités SharePoint, il est important de prendre en compte le fait que si vous n'utilisez pas OAuth pour autoriser vos demandes, ces opérations requièrent la transmission de la valeur de chiffrement de la demande du serveur comme valeur d'en-tête **X-RequestDigest**. Vous pouvez récupérer cette valeur en envoyant une demande **POST** avec un corps vide à `http://<site url>/_api/contextinfo`, puis en extrayant la valeur du nœud  `d:FormDigestValue` dans le code XML que renvoie le point de terminaison **contextinfo**. L'exemple ci-après illustre l'envoi d'une demande HTTP au point de terminaison **contextinfo** en C#.





```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

Si vous utilisez le flux d'authentification et d'autorisation décrit dans  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md), vous n'avez pas à inclure la valeur de chiffrement de demande dans vos demandes.



Si vous utilisez la bibliothèque inter-domaine JavaScript, SP.RequestExecutor gère pour vous la réception et l'envoi de la valeur de condensé de formulaire.



Si vous créez un Complément SharePoint hébergé par SharePoint, il n'est pas nécessaire d'effectuer une demande HTTP distincte pour récupérer la valeur de condensé. Vous pouvez récupérer cette valeur dans le code JavaScript à partir d'une page SharePoint (si celle-ci utilise la page maître par défaut), comme illustré dans l'exemple ci-après, qui utilise JQuery et crée une liste.





```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});

```

L'exemple ci-dessous montre comment mettre à jour la liste créée dans l'exemple précédent. L'exemple change le titre de la liste, utilise JQuery et suppose que vous effectuez cette opération dans un complément hébergé sur SharePoint.





```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});
```

La valeur de la clé **IF-MATCH** dans les en-têtes de la requête est l'emplacement où vous spécifiez la valeur **etag** d'une liste ou d'un élément de liste. Cette valeur spécifique s'applique uniquement aux listes et aux éléments de liste. Elle permet d'éviter des problèmes d'accès concurrentiel lors de la mise à jour de ces entités. L'exemple précédent utilise un astérisque (*) pour cette valeur. Vous pouvez utiliser cette valeur lorsqu'il n'y a aucun risque de rencontrer des problèmes d'accès concurrentiel. Sinon, vous devez récupérer la valeur **etag**, une liste ou un élément de liste en effectuant une requête **GET** qui récupère cette entité. Les en-têtes de réponse de la réponse HTTP obtenue transmettent l'etag comme valeur de la clé **ETag**. Cette valeur est également incluse dans les métadonnées de l'entité. L'exemple ci-après montre la balise ouvrante  `<entry>` du nœud XML qui contient les informations de liste. La propriété **m:etag** comporte la valeur **etag**.





```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">```


## Création d'un site à l'aide d'une requête REST
<a name="bk_CreateSite"> </a>

L'exemple suivant montre comment créer un site en JavaScript.



```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});```


## Différences des requêtes REST en fonction de l'environnement
<a name="bk_HowRequestsDiffer"> </a>

La création et l'envoi d'une requête HTTP peuvent varier en fonction de la langue, de la bibliothèque et du type de complément, de sorte que vous devez souvent modifier un ou plusieurs composants de requête lorsque vous traduisez une requête d'un environnement à un autre. Par exemple, les requêtes jQuery AJAX utilisent les paramètres **data** et **type** pour spécifier le corps et le type de la requête, mais les requêtes de bibliothèque inter-domaines utilisent les paramètres **body** et **method** pour spécifier ces valeurs.



Les sections suivantes décrivent les autres différences courantes entre les environnements.




### Le mode de réception et d'envoi de la valeur de condensé de formulaire dépend du complément.
<a name="FormDigest"> </a>

Lorsque vous envoyez une demande POST, celle-ci doit inclure la valeur de condensé de formulaire dans l'en-tête **X-RequestDigest**. Cependant, la façon dont vous obtenez et envoyez la valeur diffère selon le complément :




- Dans les compléments hébergés sur SharePoint, il vous suffit de transmettre l'en-tête suivant : 



 "X-RequestDigest": $("#__REQUESTDIGEST").val()


- Les compléments hébergés dans le cloud qui utilisent OAuth récupèrent d'abord la valeur de condensé de formulaire en envoyant une demande au point de terminaison **contextinfo**, puis l'ajoutent aux demandes, comme indiqué dans  [Écrire des données à l'aide de l'interface REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData).


- Dans les compléments hébergés dans le cloud qui utilisent la bibliothèque inter-domaines JavaScript, vous n'avez pas besoin de préciser la valeur de condensé de formulaire. Par défaut, SP.RequestExecutor gère ceci automatiquement. (Il gère également la valeur de longueur du contenu.)



### Les compléments qui utilisent OAuth doivent passer des jetons d'accès dans les requêtes
<a name="OAuthTokens"> </a>

Les compléments hébergés dans le cloud utilisent soit OAuth, soit la bibliothèque inter-domaines pour autoriser l'accès aux données SharePoint. Les composants de compléments dont le code s'exécute sur un serveur web distant doivent utiliser OAuth pour autoriser l'accès aux données SharePoint. Dans ce cas, vous devez inclure un en-tête **Authorization** pour envoyer le jeton d'accès. Voir [Lire des données à l'aide de l'interface REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData) pour obtenir un exemple d'ajout d'un en-tête d'autorisation pour un objet **HTTPWebRequest**.




> **REMARQUE**
> Les composants de compléments hébergés dans le cloud écrits en JavaScript doivent utiliser l'objet **SP.RequestExecutor** dans la bibliothèque inter-domaines pour accéder aux données SharePoint. Pour les requêtes de bibliothèque inter-domaines, il n'est pas nécessaire d'inclure un jeton d'accès.




Pour en savoir plus sur les jetons d'accès OAuth et sur la façon de les obtenir, voir  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) et [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md).




### Dans les requêtes inter-domaines, les URI de point de terminaison utilisent SP.AppContextSite pour modifier le contexte
<a name="AppContextSite"> </a>

Les demandes sont envoyées au point de terminaison de ressource spécifié dans la propriété **url** de la requête. Les URI de point de terminaison utilisent le format suivant :



 _<URL du site>_/_api/ _<contexte>_/ _<ressource>_ (exemple : https://contoso.com/_api/web/lists)



Les requêtes de bibliothèque inter-domaines utilisent ce format lorsqu'elles accèdent à des données sur le site web de complément ; celui-ci correspond au contexte par défaut pour les requêtes de bibliothèque inter-domaines. Cependant, pour accéder aux données sur le site web hôte ou sur une autre collection de sites, les requêtes doivent initialiser le site web hôte ou une autre collection de sites en tant que contexte. Pour ce faire, elles utilisent le point de terminaison **SP.AppContextSite** dans l'URI, comme indiqué dans le tableau 1. Les exemples d'URI dans le tableau 1 utilisent l'alias **@target** pour envoyer l'URL cible dans la chaîne de requête, car l'URL contient un caractère spécial (« : »).




> **REMARQUE**
> Une instance de site web de complément est requise pour qu'un complément hébergé dans le cloud puisse accéder aux données SharePoint lors de l'utilisation de la bibliothèque inter-domaines. 





**Tableau 1. Utilisation du point de terminaison SP.AppContextSite pour modifier le contexte de la requête**


|**Type de complément**|**Scénario d'accès aux données inter-domaines**|**Exemple d'URI de point de terminaison**|
|:-----|:-----|:-----|
|Hébergée dans le nuage  <br/> |Composant de complément JavaScript accédant aux données de site web hôte à l'aide de la bibliothèque inter-domaines  <br/> | _<URL du site web d'application>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<URL du site web hôte>_'  <br/> |
|Hébergée dans le nuage  <br/> |Composant de complément JavaScript accédant aux données dans une collection de sites autre que le site web hôte à l'aide de la bibliothèque inter-domaines (complément délimité de locataire uniquement)  <br/> | _<URL du site web d'application>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<URL du site cible>_'  <br/> |
|Hébergée par SharePoint  <br/> |Composant de site web de complément accédant aux données dans une autre collection de sites (compléments délimités de locataire uniquement)  <br/> | _<URL du site web d'application>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<URL du site cible>_'  <br/> |
 

> **REMARQUE**
> Les scénarios d'accès aux données inter-domaines nécessitent également les autorisations de complément appropriées. Pour plus d'informations, voir  [Accéder aux données à partir du site web hôte](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) et [Accéder aux données sur plusieurs collections de sites](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope). 




Les Compléments SharePoint peuvent obtenir l'URL du site web de complément et l'URL du site web hôte à partir de la chaîne de requête de la page de complément, comme le montre l'exemple de code suivant. Cet exemple montre également comment référencer la bibliothèque inter-domaines, qui est définie dans le fichier SP.RequestExecutor.js sur le site web hôte. Cet exemple part du principe que votre complément est lancé à partir de SharePoint. Voir  [Flux OAuth de code d'authentification pour les compléments SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md) pour obtenir des conseils sur la manière de configurer correctement votre contexte SharePoint lorsque votre complément n'est pas lancé à partir de SharePoint.





```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions```


## Propriétés utilisées dans les requêtes REST
<a name="bk_requestElements"> </a>

Le tableau 2 montre les propriétés couramment utilisées dans les requêtes HTTP pour le service REST SharePoint.




**Tableau 2. Utilisation des propriétés de requête REST dans les requêtes HTTP**


|**Propriétés**|**Nécessaire pour**|**Description**|
|:-----|:-----|:-----|
|**url** <br/> |Toutes les requêtes  <br/> |URL du point de terminaison de ressource REST. Exemple :  `http://<site url>/_api/web/lists` <br/> |
|**method** (ou **type**)  <br/> |Toutes les requêtes  <br/> |Méthode de requête HTTP : **GET** pour les opérations de lecture et **POST** pour les opérations d'écriture. Les requêtes **POST** permettent d'effectuer des opérations de mise à jour ou de suppression en indiquant un verbe **DELETE**, **MERGE** ou **PUT** dans l'en-tête **X-HTTP-Method**.  <br/> |
|**body** (ou **data**)  <br/> |Requêtes **POST** qui envoient des données dans le corps de la requête <br/> |Corps de la requête POST. Envoie des données (comme des types complexes) qui ne peuvent pas être envoyées dans l'URI de point de terminaison. Utilisé avec l'en-tête **content-length**.  <br/> |
|En-tête **Authentication** <br/> |Compléments distants qui utilisent OAuth pour authentifier les utilisateurs. Ne s'applique pas lorsque vous utilisez JavaScript ou la bibliothèque inter-domaines.  <br/> |Envoie le jeton d'accès OAuth (obtenu à partir d'un serveur de jeton sécurisé du service de contrôle d'accès de Microsoft) utilisé afin d'authentifier l'utilisateur pour la requête. Exemple :  `"Authorization": "Bearer " + accessToken`, où  `accessToken` représente la variable qui stocke le jeton. Les jetons doivent être récupérés à l'aide de code côté serveur. <br/> |
|En-tête **X-RequestDigest** <br/> |Requêtes **POST** (à l'exception des demandes SP.RequestExecutor) <br/> |Les compléments distants utilisant OAuth peuvent obtenir la valeur de chiffrement de formulaire à partir du point de terminaison  `http://<site url>/_api/contextinfo`. Les compléments hébergés sur SharePoint peuvent obtenir la valeur à partir du contrôle de page **#__REQUESTDIGEST**, si celui-ci est disponible sur la page SharePoint. Voir  [Écrire des données à l'aide de l'interface REST](#WritingData).  <br/> |
|En-tête **accept** <br/> |Requêtes qui renvoient les métadonnées SharePoint  <br/> |Spécifie le format des données de réponse du serveur. Le format par défaut est  `application/atom+xml`. Exemple :  `"accept":"application/json;odata=verbose"`.  <br/> |
|En-tête **content-type** <br/> |Requêtes **POST** qui envoient des données dans le corps de la requête <br/> |Spécifie le format des données que le client envoie au serveur. Le format par défaut est  `application/atom+xml`. Exemple :  `"content-type":"application/json;odata=verbose"`.  <br/> |
|En-tête **content-length** <br/> |Requêtes **POST** qui envoient les données dans le corps de demande (à l'exception des demandes SP.RequestExecutor ) <br/> |Spécifie la longueur du contenu. Exemple :  `"content-length":requestBody.length`.  <br/> |
|En-tête **IF-MATCH** <br/> |Requêtes **POST** pour les opérations **DELETE**, **MERGE** ou **PUT**, principalement pour la modification de listes et de bibliothèques.  <br/> |Permet de vérifier que l'objet en cours de modification n'a pas été modifié lors sa dernière récupération, ou permet d'indiquer le remplacement de toute modification, comme illustré dans l'exemple suivant :  `"IF-MATCH":"*"`.  <br/> |
|En-tête **X-HTTP-Method** <br/> |Requêtes **POST** pour les opérations **DELETE**, **MERGE** ou **PUT** <br/> |Utilisé pour indiquer que la demande effectue une opération de mise à jour ou de suppression. Exemple :  `"X-HTTP-Method":"PUT"`.  <br/> |
|**binaryStringRequestBody** <br/> |Demandes **POST** SP.RequestExecutor qui envoient les données binaires dans le corps <br/> |Spécifie si le corps de la demande est une chaîne binaire. **Boolean**.  <br/> |
|**binaryStringResponseBody** <br/> |Demandes SP.RequestExecutor qui renvoient des données binaires  <br/> |Spécifie si la réponse est une chaîne binaire. **Boolean**.  <br/> |
 

## Prise en charge du traitement par lots
<a name="batch"> </a>

Le service REST SharePoint Online (et SharePoint sur site 2016 et ultérieur) prend en charge la combinaison de plusieurs requêtes en un seul appel au service à l'aide de l'option de requête  `$batch` OData. Pour plus de détails et des liens vers des exemples de code, reportez-vous à [Effectuer des requêtes de lot avec les API REST](make-batch-requests-with-the-rest-apis.md).




## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Utilisation d'une liste et de ses éléments avec REST](working-with-lists-and-list-items-with-rest.md)


-  [Utilisation de dossiers et de fichiers à l'aide de l'interface REST](working-with-folders-and-files-with-rest.md)


-  [Exemples, points de terminaison et API REST pour SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013 : effectuer des opérations de base d'accès aux données sur des fichiers et des dossiers à l'aide de REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [Procédure : effectuer des opérations de base avec du code de bibliothèque JavaScript dans SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [Développer des compléments pour SharePoint](develop-sharepoint-add-ins.md)


-  [Accès aux données sécurisé et modèles d'objet client pour les compléments SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Utiliser des données externes dans SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)


-  [Protocole Open Data](http://www.odata.org/)


-  [OData : format JSON (JavaScript Object Notation)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)


-  [Définir des autorisations personnalisées sur une liste à l'aide de l'interface REST](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)







