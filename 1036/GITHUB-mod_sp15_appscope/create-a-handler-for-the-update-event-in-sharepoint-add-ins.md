---
title: Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint
Découvrez comment créer et utiliser un gestionnaire d'événements de mise à jour d'un Complément SharePoint.
## Conditions préalables à la création d'un gestionnaire d'événements de mise à jour de complément
<a name="Prerequisites"> </a>

Avoir lu les articles relatifs à la  [Gestion des événements de complément](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) et [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md), et connaître les conditions préalables et les concepts fondamentaux qui y sont présentés.
  
    
    

## Créer un récepteur UpgradedEventEndpoint
<a name="UpgradedEventEndpoint"> </a>


> **REMARQUE**
> **Système de numérotation des versions :** par souci de cohérence, cet article suppose que les numéros de version de complément sont 1.0.0.0, 2.0.0.0, 3.0.0.0, et ainsi de suite. Cependant, la logique et ces conseils s'appliquent quel que soit votre système de numérotation.
  
    
    

Pour obtenir une logique de mise à jour personnalisée, vous pouvez créer un récepteur d'événement distant SharePoint qui gère l'événement de mise à jour de complément. Soyez prudent avec cette technique. Utilisez-la uniquement pour les étapes qui ne peuvent pas être effectuées d'une autre manière. En outre, les conseils contenus dans l'article relatif à la  [Gestion des événements de complément](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) s'appliquent à l'événement de mise à jour de complément, ainsi qu'aux événements d'installation et de désinstallation de complément. Cela implique les points suivants :
  
    
    

- Votre gestionnaire doit effectuer l'opération et renvoyer un statut d'annulation ou un statut de poursuite à SharePoint dans les 30 secondes. Dans le cas contraire, SharePoint appellera de nouveau le gestionnaire, jusqu'à trois fois.
    
  
- Si votre gestionnaire renvoie un état d'annulation, SharePoint effectuera jusqu'à trois nouvelles tentatives. 
    
  
- Généralement, vous devez inclure une logique de restauration et « d'exécution préalable » dans votre gestionnaire.
    
  
Un gestionnaire **UpgradedEventEndpoint** vous sera par exemple utile quand un champ calculé est ajouté à une base de données distante. Supposons qu'une colonne Ville est ajoutée et que sa valeur est calculée à partir d'une colonne Code postal existante. Le code intégré à votre gestionnaire **UpgradedEventEndpoint** peut alors parcourir les éléments existants de la base de données et remplir la valeur du nouveau champ Ville en fonction de la valeur du champ Code postal et d'une source de données externe qui associe les codes postaux aux villes. La modification du schéma de base de données lui-même est plus efficace lorsqu'elle est réalisée en dehors du gestionnaire **UpgradedEventEndpoint** en respectant les meilleures pratiques de la plateforme de base de données.
  
    
    
Pour plus de détails sur la façon de créer un gestionnaire d'événements de complément, voir  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md) et [Créer un récepteur d'événements de complément dans SharePoint](create-an-add-in-event-receiver-in-sharepoint-add-ins.md). La procédure suivante part du principe que vous avez ajouté l'élément de récepteur d'événements de complément à votre projet de Complément SharePoint dans Visual Studio. 
  
    
    

### Pour gérer l'événement de mise à jour de complément la première fois


1. Ouvrez le fichier AppEventReceiver.svc.cs et ajoutez une structure conditionnelle à la méthode  [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) , laquelle vérifie si l'événement qui a appelé le gestionnaire est l'événement de mise à jour. Votre code de mise à jour est placé dans cette structure. S'il doit accéder à SharePoint, vous pouvez utiliser le modèle objet client (CSOM) du code managé SharePoint ou l'interface REST (Representational State Transfer). L'emplacement de la structure conditionnelle dans la méthode dépend de la façon dont vous avez structuré le reste du code dans la méthode. Généralement, celle-ci se compose de deux structures conditionnelles similaires qui testent les événements d'installation et de désinstallation de complément. Elle peut se trouver au sein d'une structure conditionnelle qui contrôle certaines propriétés ou sous-propriétés de l'objet [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) transmis à la méthode [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) pour les valeurs **null** ou incorrectes. Elle peut également se trouver dans un bloc **try**. L'exemple ci-dessous illustre la structure. L'élément  _properties_ est un objet [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
 ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

 ```

2. Pour utiliser le modèle CSOM dans le gestionnaire, ajoutez (dans le bloc conditionnel) un bloc **using** qui obtient un objet [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) en appelant la méthode **TokenHelper.CreateAppEventClientContext**. Indiquez **true** pour le deuxième paramètre pour accéder au service web de complément. Indiquez **false** pour accéder au site web hôte. Si vous avez besoin d'accéder aux deux, vous devrez utiliser deux objets de contexte client différents.
    
  
3. Si le gestionnaire doit accéder à des composants qui ne sont pas des composants SharePoint, placez le code en dehors des blocs de contexte client. Votre code doit être structuré comme suit.
    
 ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, true))
    {
        // CSOM code that accesses the add-in web
    }
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, false))
    {
        // CSOM code that accesses the host web
    }
    // Other update code
}

 ```

4. Pour utiliser l'interface REST, votre code utilise d'autres méthodes de la classe **TokenHelper** pour obtenir un jeton d'accès, qui est alors inclus dans les requêtes qu'il envoie à SharePoint. Pour plus d'informations, voir [Effectuer des opérations de base à l'aide de terminaux REST SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). Votre code doit être structuré comme suit.
    
 ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
    if (contextTokenString != null)
    {
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                                                                                                                  Request.Url.Authority);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                                   .AccessToken;

       // REST code that accesses SharePoint
    }  
    // Other update code
}

 ```

5. Pour accéder à SharePoint, votre code REST doit aussi connaître l'URL du site web hôte, du site web de complément ou des deux. Ces URL sont toutes les deux des sous-propriétés de l'objet  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) transmis à la méthode [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) . Le code suivant montre comment les obtenir.
    
 ```cs
  
Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
 ```

Lorsque vous mettez à jour un complément pour la deuxième fois (ou la troisième, etc.), vous devez parfois vous assurer que certains éléments de votre logique ne sont pas exécutés plusieurs fois sur la même instance de complément. La procédure suivante vous montre comment faire.
  
    
    

### Pour gérer l'événement de mise à jour de complément les fois suivantes


1. Ouvrez le fichier AppEventReceiver.svc.cs et recherchez le code qui a servi à l'implémentation des actions de mise à jour lors de la première mise à jour (de 1.0.0.0. vers 2.0.0.0). Nous l'appellerons le code de mise à jour précédent. (Ce code est généralement situé après le code d'autorisation qui obtient le contexte client ou les jetons d'accès.) Quand vous effectuez une nouvelle mise à jour (de 2.0.0.0 vers 3.0.0.0), il arrive généralement que vous ne souhaitiez pas que certaines actions du code de mise à jour précédent soient exécutées sur la même instance de complément. En revanche, vous voulez que ce code soit exécuté sur une instance de complément qui n'a jamais été mise à jour vers 2.0.0.0 (auquel cas, l'instance est directement mise à jour de 1.0.0.0. vers 3.0.0.0).
    
  
2. Insérez le code de mise à jour précédent dans une structure conditionnelle qui teste la version précédente de l'instance de complément et n'exécute le code que si celui-ci n'a jamais été exécuté auparavant pour cette instance de complément. La version précédente de l'instance est stockée dans la sous-propriété  [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) de l'objet [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  
3. Ajoutez votre nouvelle logique de mise à jour (pour la mise à jour de 2.0.0.0 vers 3.0.0.0) en dessous de cette structure, comme dans l'exemple ci-dessous.
    
 ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

 ```

4. Pour chaque mise à jour ultérieure, répétez ces étapes. Pour la mise à jour de 3.0.0.0 vers 4.0.0.0, votre code doit avoir la structure suivante.
    
 ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 is here.
}

Version ver3OOO = new Version("3.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver3OOO)
{
    // Code to update from 2.0.0.0 to 3.0.0.0 (previous update code) is here.
}
// Code to update from 3.0.0.0 to 4.0.0.0 is here.

 ```


> **IMPORTANTE**
> Si vous ajoutez un composant à un complément dans un gestionnaire **UpgradedEventEndpoint**, n'oubliez pas d'ajouter le même code dans un gestionnaire **InstalledEventEndpoint**, car le composant doit également être inclus dans le complément lors d'une nouvelle installation. En outre, vous devez ajouter un élément  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) (ou le réviser) pour que le complément puisse supprimer le composant. Tout ce qui a été ajouté ou modifié par le gestionnaire **InstalledEventEndpoint** doit être annulé ou supprimé par le gestionnaire **UninstallingEventEndpoint**. La seule exception concerne les données qui resteront utiles après la suppression du complément de la corbeille secondaire et qui ne doivent pas être supprimées. (Les sites web créés par le complément, autres que le site web de complément, doivent être considérés comme des données.) 
  
    
    


## Ajouter la logique de restauration au gestionnaire
<a name="AddRollbackLogic"> </a>

Si une erreur se produit lors de la mise à jour d'un complément, l'infrastructure SharePoint 2013 arrête le processus de mise à jour et restaure la version précédente de l'instance de complément et de ses composants. Toutefois, l'infrastructure n'a aucun moyen de connaître les opérations effectuées par la logique de votre gestionnaire **UpgradedEventEndpoint** et ne peut donc pas toujours les annuler. Une exception non gérée, générée lors de l'exécution de votre gestionnaire **UpgradedEventEndpoint**, indique presque toujours que le processus entier de mise à jour du complément doit être annulé, mais il ne le sera pas, car l'infrastructure ne sait pas comment défaire certaines opérations effectuées par votre code **UpgradedEventEndpoint**. Pour cette raison, le code **UpgradedEventEndpoint** doit :
  
    
    

1. Intercepter toutes les exceptions.
    
  
2. Créer une branche vers un code de restauration personnalisé permettant d'annuler les opérations effectuées jusqu'à ce point.
    
  
3. Signaler à l'infrastructure SharePoint 2013 que l'intégralité de la mise à jour du complément doit être annulée.
    
  
4. Transmettre un message d'erreur à l'infrastructure.
    
  
Toutes les opérations réalisées par **UpgradedEventEndpoint** n'ont pas à être explicitement annulées dans le gestionnaire. Le site web de complément est restauré par l'infrastructure, de sorte que votre code n'a pas à annuler les modifications apportées au site web de complément. En revanche, le gestionnaire **UpgradedEventEndpoint** doit annuler les modifications apportées au site web hôte ou à d'autres composants extérieurs au Complément SharePoint.
  
    
    
Lors de la deuxième (ou de la troisième, etc.) mise à jour, votre logique de gestion des exceptions doit tenir compte du fait que la version de l'instance de complément en cours de mise à jour n'est pas nécessairement la version immédiatement précédente. Dans ce cas, il se peut que plusieurs blocs de code de mise à jour doivent être inversés. Pour cette raison, votre logique conditionnelle doit être basée sur le numéro de la version précédente. L'exemple suivant illustre une logique de restauration pour une mise à jour vers la version 4.0.0.0.
  
    
    


```cs

catch (Exception e)
{ 
    // Rollback of the 3.0.0.0 to 4.0.0.0 update logic goes here.

    Version ver3OOO = new Version("3.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver3OOO)
    {
        // Rollback of the 2.0.0.0 to 3.0.0.0 update logic goes here.
    }

    Version ver2OOO = new Version("2.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver2OOO)
    {
        // Rollback of the 1.0.0.0 to 2.0.0.0 update logic goes here.
    }
}```

Enfin, votre logique de gestion des erreurs doit affecter un message d'erreur et un statut d'annulation à l'objet  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) renvoyé par la méthode **ProcessEvent** à l'infrastructure de mise à jour de SharePoint 2013, comme dans l'exemple suivant. Dans ce code, l'élément _result_ est un objet **SPRemoteEventResult** qui a été déclaré plus tôt dans la méthode **ProcessEvent**.
  
    
    


```cs

catch (Exception e)
{     
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}```


> **IMPORTANTE**
> Il est essentiel d'affecter l'élément **SPRemoteEventServiceStatus.CancelWithError** (ou **SPRemoteEventServiceStatus.CancelNoError**) à la propriété  [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) . C'est cette propriété qui signale à l'infrastructure d'annuler la mise à jour. Cependant, SharePoint appellera votre gestionnaire trois fois avant d'annuler la mise à jour.
  
    
    


### Utiliser la stratégie de délégation de gestionnaire

La stratégie de délégation de gestionnaire est décrite dans l'article  [Gestion des événements de complément](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) et peut être également utilisée dans un gestionnaire **UpdatedEventHandler**. Non seulement votre logique de restauration et « d'exécution préalable » est contenue et exécutée sur le composant à distance, mais votre logique de contrôle de version également. Les limites de la stratégie s'appliquent ici aussi : vous ne pouvez généralement pas l'utiliser pour mettre à jour plusieurs systèmes distants.
  
    
    

## Étapes suivantes
<a name="Next"> </a>

Retournez à la section  [Étapes principales de mise à jour d'un complément](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) ou consultez directement l'un des articles suivants pour découvrir comment mettre à jour le prochain composant majeur de votre Complément SharePoint.
  
    
    

-  [Mettre à jour des composants de compléments web dans SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Mettre à jour des composants web hôte dans SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Mettre à jour des composants distants dans les compléments pour SharePoint](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Élément UpgradedEventEndpoint (PropertiesDefinition, complexType) (manifeste SharePoint Add-in)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)
    
  
-  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Créer un récepteur d'événements de complément dans SharePoint](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  

