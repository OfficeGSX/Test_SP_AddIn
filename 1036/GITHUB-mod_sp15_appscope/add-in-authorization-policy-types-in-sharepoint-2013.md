---
title: Types de stratégie d'autorisation des compléments dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# Types de stratégie d'autorisation des compléments dans SharePoint 2013
Découvrez les différentes stratégies d'autorisation pour les compléments dans SharePoint : stratégie de complément uniquement, stratégie d'utilisateur et de complément, et stratégie d'utilisateur uniquement. Ce document contient également des instructions pour l'utilisation d'une stratégie de complément uniquement.
Avant de lire cet article, vous devez d'abord vous familiariser avec les articles  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) et [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).





## Obtenir une vue d'ensemble des types de stratégie d'autorisation des compléments
<a name="Overview"> </a>

SharePoint propose trois types de stratégies d'autorisation :




- **Stratégie d'utilisateur uniquement**: lorsque la stratégie d'utilisateur uniquement est utilisée, SharePoint vérifie uniquement les autorisations de l'utilisateur. SharePoint utilise cette stratégie lorsque l'utilisateur accède aux ressources directement sans utiliser de complément, par exemple lorsqu'un utilisateur ouvre pour la première fois la page d'accueil d'un site SharePoint ou accède pour la première fois aux API SharePoint à partir de PowerShell.




- **Stratégie d'utilisateur et de complément**: lorsque la stratégie d'utilisateur et de complément est utilisée, SharePoint vérifie à la fois les autorisations de l'utilisateur et du principal de complément. Les vérifications d'autorisation réussissent uniquement si l'utilisateur en cours et le complément disposent tous deux des autorisations nécessaires pour effectuer l'action en question.

    Par exemple, cette stratégie est utilisée quand un Complément SharePoint souhaite accéder aux ressources de l'utilisateur sur SharePoint. (Le code dans les composants distants du Complément SharePoint doit être conçu pour effectuer des appels d'utilisateur et de complément à SharePoint.)




- **Stratégie de complément uniquement**: lorsque la stratégie de complément uniquement est utilisée, SharePoint vérifie uniquement les autorisations du principal de complément. Les vérifications de l'utilisation réussissent uniquement si le complément en cours dispose des autorisations nécessaires pour effectuer l'action en question, indépendamment des autorisations de l'utilisateur actuel (s'il y en a un).

    Un complément d'approbation de notes de frais est un exemple de complément susceptible d'être conçu pour utiliser cette stratégie. Le complément permet aux utilisateurs qui autrement ne pourraient pas approuver de dépenses de le faire dans la limite d'un certain montant. Pour plus de détails, consultez le scénario ci-dessous. 



    > **REMARQUE**
      > Certaines API requièrent un contexte utilisateur et ne peuvent pas être exécutées avec une stratégie de complément uniquement. Elles comprennent notamment des API pour l'interaction avec Project Server 2013 et pour l'exécution de requêtes de recherche. 

### Voir un exemple de scénario de complément qui utilise la stratégie de complément uniquement
<a name="Scenario"> </a>

Supposons qu'un responsable des ventes chez Contoso, Adam, achète un complément de soumission de notes de frais qui utilise la stratégie de complément uniquement. Lorsqu'Adam décide d'acheter le complément, il est invité à l'autoriser à élever les autorisations des utilisateurs, c'est-à-dire permettre au complément d'effectuer des appels de complément uniquement à SharePoint. Adam accorde au complément les autorisations demandées. Ensuite, il achète un nombre suffisant de licences du complément pour tous les commerciaux chez Contoso, et il installe le complément sur le site web SharePoint de l'équipe de vente.



Rapidement, les commerciaux soumettent les notes de frais à l'aide du nouveau complément. En général, les commerciaux ne peuvent pas approuver leurs propres notes de frais mais le complément leur permet de le faire car Adam lui a accordé cette possibilité pour les dépenses inférieures à 50 €, car il a défini le complément de sorte qu'il approuve automatiquement les notes de frais inférieures à 50 €. Pour l'approbation des notes de frais d'un montant supérieur ou égal à 50 €, le complément lui affecte automatiquement une tâche. Cela pourrait être implémenté en donnant au Complément SharePoint une autorisation d'accès en écriture à une liste SharePoint de dépenses approuvées. Toutefois, parmi les utilisateurs, seuls les responsables des ressources humaines disposent de l'autorisation d'accès en écriture à la liste. Le code du complément est conçu pour ajouter la dépense à la liste en effectuant un appel de complément uniquement à SharePoint dès que la dépenses est inférieure à 50 €. Comme les autorisations de l'utilisateur ne sont pas vérifiées, les soumissions d'utilisateur inférieures à 50 € sont automatiquement ajoutées à la liste des dépenses approuvées, même si l'utilisateur ne dispose pas d'autorisation d'accès en écriture à la liste.








### Découvrir comment les compléments reçoivent l'autorisation d'utiliser la stratégie de complément uniquement
<a name="Approve"> </a>

Pour pouvoir effectuer des appels de complément uniquement à SharePoint, le complément doit demander l'autorisation d'utiliser la stratégie de complément uniquement. Cette demande est faite dans le manifeste de complément. Pour ce faire, ajoutez l'attribut **AllowAppOnlyPolicy** à l'élément **AppPermissionRequests** et affectez-lui la valeur **true**, tel qu'indiqué dans le balisage suivant :



```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>```


> **REMARQUE**
> L'ancien nom des Compléments SharePoint est « applications pour SharePoint ». Le schéma de manifeste d'application n'a pas été modifié afin de conserver la compatibilité descendante. De ce fait, la chaîne « app » est susceptible d'apparaître dans des noms d'élément et d'attribut. 




Un utilisateur installant le complément sera invité à approuver cette demande. Si le complément demande des autorisations d'étendue client, seul un administrateur client peut accorder l'utilisation de la stratégie de complément uniquement. De ce fait, seul un administrateur client peut installer le complément. Si le complément ne demande pas d'autorisation d'étendue plus élevée que la collection de sites, un administrateur de collection de sites peut installer le complément. Pour plus d'informations sur les étendues d'autorisation, voir  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).




### Découvrir comment les compléments effectuent des appels de complément uniquement
<a name="AppOnlyCalls"> </a>

La différence entre un appel de complément uniquement à SharePoint et un appel d'utilisateur et de complément réside dans le type de jeton d'accès qui est inclus dans l'appel. Le code suivant montre comment faire obtenir des jetons d'accès d'utilisateur et de complément, et de complément uniquement dans du code managé. Le codage détaillé est à votre disposition dans le fichier TokenHelper.cs (ou .vb) que les Outils de développement Office pour Visual Studio ajoutent automatiquement au projet d' dans Visual Studio.



```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+add-in access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...

      //Get add-in-only access token.
       string addinOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}```


> **REMARQUE**
> Les compléments qui ne font pas d'appels OAuth authentifiés (par exemple, les compléments JavaScript uniquement qui s'exécutent dans le service web de complément) ne peuvent pas utiliser la stratégie de complément uniquement. Elles peuvent demander l'autorisation, mais elles ne pourront pas en bénéficier car cela nécessite le passage d'un jeton OAuth de complément uniquement. Seuls les compléments qui ont des applications web qui s'exécutent hors de SharePoint peuvent créer et passer des jetons de complément uniquement. 




En général, un utilisateur actuel doit être présent pour passer un appel. Dans le cas d'une stratégie de complément uniquement, SharePoint crée un SHAREPOINT\\APP, similaire à l'utilisateur SHAREPOINT\\SYSTEM existant. Toutes les demandes de complément uniquement sont faites par SHAREPOINT\\APP. Il n'est pas possible de s'authentifier comme SHAREPOINT\\APP via une authentification utilisateur.




### Obtenir des recommandations sur l'utilisation de la stratégie de complément uniquement
<a name="GuidelinesFor"> </a>

Étant donné que les appels de complément uniquement élèvent effectivement les privilèges d'utilisateur, faites preuve de prudence lorsque vous créez des compléments qui demandent l'autorisation d'en effectuer. Les appels ne doivent utiliser la stratégie de complément que si :




- le complément doit élever ses autorisations au-dessus de celles de l'utilisateur pour un appel spécifique, par exemple pour approuver un rapport de note de frais d'après des conditions évaluées par le complément ;


- le complément n'agit au nom d'aucun utilisateur, par exemple un complément qui effectue des tâches de maintenance nocturne sur une bibliothèque de documents SharePoint.



## Ressources supplémentaires
<a name="AR"> </a>


-  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)


-  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)


-  [Flux OAuth de jeton de contexte pour les compléments SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)


-  [Compléments](sharepoint-add-ins.md)


-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)



