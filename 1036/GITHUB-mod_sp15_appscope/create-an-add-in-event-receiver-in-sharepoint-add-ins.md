---
title: Créer un récepteur d'événements de complément dans SharePoint
ms.prod: SHAREPOINT
ms.assetid: f40c910f-12a2-4caa-8e91-c7a61ae540db
---


# Créer un récepteur d'événements de complément dans SharePoint
Créez des gestionnaires pour les événements d'installation et de désinstallation de Complément SharePoint dans des Compléments SharePoint.
## Configuration requise
<a name="SP15appevent_prereq"> </a>

Cet article suppose que vous connaissez les Compléments SharePoint hébergés par un fournisseur, et que votre expérience en matière de développement va au-delà du niveau « Hello World ». Vous devriez également connaître le concept de  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md). 




## Obtenir d'autres exemples de code
<a name="SP15appevent_prereq"> </a>

Si vous continuez à travailler sur l'exemple de cet article, vous disposerez d'un exemple de code terminé. Voici d'autres exemples. Ils ne suivent pas tous l'architecture décrite dans cet article. Il existe plusieurs méthodes valables pour concevoir un récepteur d'événements de complément ; gardez à l'esprit que les conseils de Microsoft peuvent également évoluer au fil du temps. 




-  [OfficeDev/PnP/Samples/Core.AppEvents.HandlerDelegation](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents.HandlerDelegation) est très proche de l'exemple suivant de cet article.


-  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents) montre comment exécuter la même tâche que l'exemple précédent dans les scénarios où la stratégie de délégation de gestionnaire ne peut pas être utilisée.


-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)


-  [Créer un complément hébergé par un fournisseur qui permet de personnaliser l'installation du complément](https://code.msdn.microsoft.com/SharePoint-2013-Create-a-f27752e0)



## Ajouter un récepteur d'événement installé avec le complément
<a name="SP15appevent_prereq"> </a>


1. Dans Visual Studio, ouvrez le projet du Complément SharePoint hébergé par un fournisseur. (Si vous ajoutez un gestionnaire d'événement de complément à un complément hébergé par SharePoint, les Outils de développement Office pour Visual Studio le convertissent en application hébergée par un fournisseur.)


2. Dans l' **Explorateur de solutions**, choisissez le nœud du Complément SharePoint.


3. Dans la fenêtre **Propriétés**, définissez la valeur de **Gérer l'installation du complément** sur **True**. 

   **Figure 1. Événements de complément dans la fenêtre Propriétés**



!\[Événements d'application dans la fenêtre Propriétés](images/SP_VS_Properties_Window_AppEvents.PNG)


    Outils de développement Office pour Visual Studio effectuera l'action suivante :

  - Ajouter un fichier appelé AppEventReceiver.svc. contenant du code squelette C# (ou VB.NET). Il s'agit du service qui gérera l'événement de complément.


  - L'entrée suivante est ajoutée à la section **Propriétés** du fichier AppManifest.xml : `<InstalledEventEndpoint>~remoteAppUrl/AppEventReceiver.svc</InstalledEventEndpoint>`. Cette entrée enregistre le récepteur d'événement de complément pour SharePoint. (Notez que le jeton **~remoteAppUrl** est le même que celui utilisé pour l'application web distante dans le Complément SharePoint hébergé par un fournisseur. Les Outils de développement Office pour Visual Studio partent du principe que le domaine de l'application web et celui du gestionnaire d'événements sont les mêmes. Si jamais ce n'est pas le cas, vous devez remplacer manuellement le jeton **~remoteAppUrl** par le véritable domaine de votre service.)


  - Si le projet de Complément SharePoint ne dispose pas encore d'un projet web, les Outils de développement Office pour Visual Studio en créent un. Les outils garantissent également que le manifeste du complément est configuré pour un complément hébergé par un fournisseur. Ils ajouteront aussi des pages, scripts, fichiers CSS et autres artefacts. (Si le seul composant distant dont votre complément a besoin est le service web de gestion des événements, vous pouvez supprimer ceux-ci du projet. Vous devez également vous assurer que l'élément **StartPage** figurant dans le manifeste du complément ne pointe pas vers une page que vous avez supprimée.)


4. Si votre batterie de serveurs test SharePoint n'est pas installée sur l'ordinateur qui exécute Visual Studio, configurez le projet pour débogage à l'aide du bus des services Microsoft Azure. Pour plus d'informations, consultez la rubrique  [Déboguer et dépanner un récepteur d'événement distant dans un complément pour SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). 


5. S'il existe une méthode  `ProcessOneWayEvent` dans le fichier AppEventReceiver.svc, son implémentation doit simplement se faire avec la ligne `throw new NotImplementedException();`, car cette méthode ne peut être utilisée dans un gestionnaire d'événements de complément.  *Les gestionnaires d'événements de complément doivent renvoyer un objet qui indique à SharePoint s'il faut terminer ou restaurer l'événement, et la méthode  `ProcessOneWayEvent` ne renvoie rien.* 


6. Le fichier inclura une méthode  `ProcessEvent` qui ressemble à ce qui suit. (Ce pourrait être également un bloc de code illustrant comment obtenir un contexte client. Supprimez-le ou commentez-le.)

    Notez les points suivants concernant ce code :

  - L'objet  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) est envoyé à votre service web de gestionnaire en tant que message SOAP contenant des informations contextuelles issues de SharePoint, notamment une propriété [EventType](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.EventType.aspx) qui identifie l'événement.


  - L'objet  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) renvoyé par votre gestionnaire contient une propriété [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) dont les valeurs possibles sont [SPRemoteEventServiceStatus](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventServiceStatus.aspx) . **Continue**, **SPRemoteEventServiceStatus.CancelNoError** et **SPRemoteEventServiceStatus.CancelWithError**. La valeur par défaut de la propriété **Status** est **Continue**. Celle-ci indique à SharePoint de mettre fin à l'événement. Les deux autres valeurs donnent les instructions suivantes à SharePoint :

  - Réexécutez votre gestionnaire jusqu'à trois fois supplémentaires


  - Si vous obtenez toujours un état d'annulation, annulez l'événement et restaurez toutes les actions effectuées dans le cadre de l'événement. 





 ```cs

public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();

    return result;
}
 ```

7. Juste au-dessous de la ligne déclarant la variable  `result`, ajoutez la structure de basculement suivante pour identifier l'événement géré. 

 ```cs

switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:
        break;
    case SPRemoteEventType.AppUpgraded:
        break;
    case SPRemoteEventType.AppUninstalling:
        break;
}
 ```


    > **REMARQUE**
      > Si vous disposez de gestionnaires pour les événements **AppInstalled**, **AppUpdated** et **AppInstalling**, ceux-ci auront chacun leur propre URL enregistrée dans le manifeste du complément. Vous  *pouvez*  donc avoir différents points de terminaison pour eux. Toutefois, cet article (tout comme les Outils de développement Office pour Visual Studio) part du principe qu'ils possèdent exactement le même point de terminaison. C'est pourquoi le code doit déterminer quel événement l'a appelé.
8. Comme expliqué  [Inclure une logique de restauration et une logique « Déjà terminé » dans vos gestionnaires d'événements de complément](handle-events-in-sharepoint-add-ins.md#Rollback), si votre logique d'installation rencontre un problème, vous souhaitez dans la plupart des cas annuler l'installation du complément et faire en sorte que SharePoint revienne sur les étapes effectuées pendant cette installation. Vous voulez également annuler les actions de votre gestionnaire. Une méthode permettant d'atteindre ces objectifs consiste à ajouter le code suivant dans le **case** pour l'événement AppInstalled.

 ```cs

case SPRemoteEventType.AppInstalled:
  try
  {
      // Add-in installed event logic goes here.
  }
  catch (Exception e)
  {
      result.ErrorMessage = e.ErrorMessage;
      result.Status = SPRemoteEventServiceStatus.CancelWithError;

      // Rollback logic goes here.
  }
  break;
 ```


    > **REMARQUE**
      > Déplacez un code d'installation qui prend plus de 30 secondes dans le complément lui-même. Vous pouvez l'ajouter à la logique « de première exécution » qui s'exécute lors du premier lancement du complément. Le complément peut afficher un message indiquant que l'opération est en cours d'exécution et également inviter l'utilisateur à exécuter le code d'initialisation. > Si la logique « de première exécution » n'est pas réalisable pour votre complément, vous pouvez également laisser votre gestionnaire d'événement lancer un processus asynchrone distant, puis renvoyer immédiatement un objet  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) dont le **Status** possède la valeur **Continue**. Cette stratégie présente le défaut de n'avoir aucun moyen d'indiquer à SharePoint de restaurer l'installation du complément en cas d'échec du processus distant. 
9. Comme expliqué dans  [Stratégies d'architecture de gestionnaire d'événements de complément](handle-events-in-sharepoint-add-ins.md#Strategies), la stratégie de délégation de gestionnaire est privilégiée, mais elle n'est pas possible dans tous les scénarios. Dans l'exemple en cours, nous vous montrons comment implémenter la stratégie de délégation de gestionnaire lors de l'ajout d'une liste sur le site web hôte. (Pour plus d'informations sur la création d'un gestionnaire d'événements AppInstalled similaire qui n'utilise pas la stratégie de délégation de gestionnaire, voir l'exemple  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents).)

    Voici la nouvelle version du bloc **case** AppInstalled. Notez que la logique d'initialisation qui s'applique à tous les événements dépasse le bloc **switch**. Étant donné que la liste installée sera supprimée dans le gestionnaire AppUninstalling, la liste est identifiée ici.



 ```cs

SPRemoteEventResult result = new SPRemoteEventResult();
String listTitle = "MyList";

switch (properties.EventType)
{           
    case SPRemoteEventType.AppInstalled:
                
   try
   {
        string error = TryCreateList(listTitle, properties);
        if (error != String.Empty)
        {
            throw new Exception(error);        
        }
   }
    catch (Exception e)
   {
        // Tell SharePoint to cancel the event.
        result.ErrorMessage = e.Message;
        result.Status = SPRemoteEventServiceStatus.CancelWithError;           
    }
        break;
    case SPRemoteEventType.AppUpgraded:
       break;
    case SPRemoteEventType.AppUninstalling:
       break;
}                  
 ```

10. Ajoutez la méthode de création de la liste à la classe **AppEventReceiver** en tant que méthode **private** avec le code suivant. Notez que la classe `TokenHelper` dispose d'une méthode spéciale, optimisée afin d'obtenir un contexte client pour un événement de complément. La transmission de la valeur **false** pour le dernier paramètre garantit que le contexte est celui du site web hôte.

 ```cs

private string TryCreateList(String listTitle, SPRemoteEventProperties properties)
 {
    string errorMessage = String.Empty;      

    using (ClientContext clientContext =
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
        }
    }
    return errorMessage;
}

 ```

11. La logique de restauration est essentiellement une logique de gestion des exceptions et le modèle CSOM SharePoint (modèle objet client) dispose d'un élément  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) qui permet à votre service web de déléguer la gestion des exceptions au serveur SharePoint. (Consultez également la rubrique [Procédure : Utiliser l'étendue de gestion des exceptions](http://msdn.microsoft.com/library/103619ef-1ba3-44e3-93e1-5e0685bc616e%28Office.15%29.aspx).) Ajoutez le code suivant au bloc **if** dans l'extrait de code précédent.

 ```cs

ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext);

using (scope.StartScope()) 
{ 
    using (scope.StartTry()) 
    { 
    }     
    using (scope.StartCatch()) 
    {                             
    } 
    using (scope.StartFinally()) 
    { 
    } 
} 
 clientContext.ExecuteQuery();

if (scope.HasException)
{
    errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
        scope.ServerErrorTypeName, scope.ErrorMessage, 
        scope.ServerErrorDetails, scope.ServerErrorValue, 
        scope.ServerStackTrace, scope.ServerErrorCode);
}
 ```

12. L'extrait de code ci-dessus contient seulement un appel à SharePoint ( **ExecuteQuery**), mais nous ne pouvons malheureusement pas nous en contenter. Chaque objet référencé dans notre étendue des exceptions doit d'abord être chargé vers le client. Ajoutez le code suivant  *au-dessus*  du constructeur pour **ExceptionHandlingScope**.

 ```cs

ListCollection allLists = clientContext.Web.Lists;
IEnumerable<List> matchingLists =
    clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
clientContext.ExecuteQuery();

var foundList = matchingLists.FirstOrDefault();
 List createdList = null;
 ```

13. Le code permettant de créer une liste web hôte ira dans le bloc  [StartTry](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartTry.aspx) , mais le code doit d'abord vérifier si la liste a déjà été ajoutée (tel qu'expliqué dans la rubrique [Inclure une logique de restauration et une logique « Déjà terminé » dans vos gestionnaires d'événements de complément](handle-events-in-sharepoint-add-ins.md#Rollback)). Vous pouvez déléguer la logique if-then-else au serveur SharePoint à l'aide de la classe  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) . (Voir également [Procédure : Utilisation de l'étendue conditionnelle](http://msdn.microsoft.com/library/560112e9-c3ed-4b8f-9cd4-c8bc5d60d63c%28Office.15%29.aspx).) Ajoutez le code suivant dans le bloc **StartTry**.

 ```cs

ConditionalScope condScope = new ConditionalScope(clientContext,
        () => foundList.ServerObjectIsNull.Value == true, true);
using (condScope.StartScope())
{
    ListCreationInformation listInfo = new ListCreationInformation();
    listInfo.Title = listTitle;
    listInfo.TemplateType = (int)ListTemplateType.GenericList;
    listInfo.Url = listTitle;
    createdList = clientContext.Web.Lists.Add(listInfo);                            
}
 ```

14. Le bloc  [StartCatch](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartCatch.aspx) doit annuler la création de la liste, mais il doit d'abord vérifier que la liste a été créée. En effet, une exception pourrait avoir été déclenchée dans le bloc **StartTry** avant la fin de la création de la liste. Ajoutez le code suivant au bloc **StartCatch**.

 ```cs

ConditionalScope condScope = new ConditionalScope(clientContext,
        () => createdList.ServerObjectIsNull.Value != true, true);
using (condScope.StartScope())
{
    createdList.DeleteObject();
} 
 ```


    > **CONSEIL**
      > **CONSEIL DE RÉSOLUTION DES PROBLÈMES :** pour vérifier l'entrée adéquate de votre bloc **StartCatch**, vous avez besoin d'une méthode pour déclencher une exception d'exécution sur le serveur SharePoint. L'utilisation d'un **throw** ou la division par zéro ne fonctionneront pas, car elles déclenchent des exceptions *côté client*  avant même que l'exécution du client ne puisse envelopper le code et l'envoyer au serveur (à l'aide de la méthode **ExecuteQuery**). Ajoutez plutôt les lignes suivantes au bloc **StartTry**. L'exécution côté client accepte l'opération, mais cela cause une exception côté serveur, justement ce que vous recherchez. >  `List fakeList = clientContext.Web.Lists.GetByTitle("NoSuchList");`



 `clientContext.Load(fakeList);`

    La méthode TryCreateList complète devrait ressembler à ce qui suit. (Le bloc  [StartFinally](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartFinally.aspx) est requis même s'il n'est pas utilisé.)



 ```cs

private string TryCreateList(String listTitle, SPRemoteEventProperties properties)
{
    string errorMessage = String.Empty;

    using (ClientContext clientContext = 
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
            ListCollection allLists = clientContext.Web.Lists;
            IEnumerable<List> matchingLists = 
                clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
            clientContext.ExecuteQuery();
            var foundList = matchingLists.FirstOrDefault();
            List createdList = null;

            ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext); 
            using (scope.StartScope()) 
            { 
                using (scope.StartTry()) 
                { 
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                            () => foundList.ServerObjectIsNull.Value == true, true);
                    using (condScope.StartScope())
                    {
                        ListCreationInformation listInfo = new ListCreationInformation();
                        listInfo.Title = listTitle;
                        listInfo.TemplateType = (int)ListTemplateType.GenericList;
                        listInfo.Url = listTitle;
                        createdList = clientContext.Web.Lists.Add(listInfo);
                    }
                } 
            
                using (scope.StartCatch()) 
                { 
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                            () => createdList.ServerObjectIsNull.Value != true, true);
                    using (condScope.StartScope())
                    {
                        createdList.DeleteObject();
                    }
                } 

                using (scope.StartFinally()) 
                { 
                } 
            } 
            clientContext.ExecuteQuery();

            if (scope.HasException)
            {
                    errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
                    scope.ServerErrorTypeName, scope.ErrorMessage, 
                    scope.ServerErrorDetails, scope.ServerErrorValue, 
                    scope.ServerStackTrace, scope.ServerErrorCode);
            }
        }
    }
    return errorMessage;
}
 ```


    > **CONSEIL**
      > **DÉBOGAGE :** que vous utilisiez ou non la stratégie de délégation de gestionnaire, lorsque vous exécutez le code pas à pas avec le débogueur, gardez à l'esprit que, quel que soit le scénario dans lequel votre gestionnaire renvoie un état d'annulation, SharePoint appellera votre gestionnaire de nouveau, jusqu'à trois fois supplémentaires. Par conséquent, le débogueur parcourt le code jusqu'à quatre fois.

    > **CONSEIL**
      > **ARCHITECTURE DE CODE :** étant donné que vous pouvez installer des composants sur le site web du complément avec du code déclaratif à l'extérieur de votre gestionnaire, vous ne souhaitez généralement pas utiliser les 30 secondes dont votre gestionnaire dispose pour interagir avec le site web du complément. Toutefois, si vous le faites, gardez à l'esprit que votre code requiert un objet [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) distinct pour le site web du complément. Cela signifie que le site web du complément et le site web de l'hôte sont des composants différents, autant qu'une base de données SQL Server est différente de chacun d'entre eux. Une méthode appelant le site web du complément se trouve donc dans le bloc **try** du bloc **case** AppInstalled, tout comme la méthode TryCreateList dans l'exemple en cours. Cependant, votre gestionnaire n'a *pas*  besoin de restaurer les actions effectuées sur le site web du complément. S'il rencontre une erreur, il doit simplement annuler l'événement, car SharePoint supprimera la totalité du site web du complément si l'événement est annulé.

## Créer un récepteur d'événements de désinstallation de complément
<a name="SP15appevent_prereq"> </a>


1. Définissez la propriété **Gérer la désinstallation du complément** du projet sur la valeur **True**. Les outils ne créent  *pas*  de fichier de service web supplémentaire s'il en existe déjà un, mais ils ajoutent un élément **UninstallingEventEndpoint** au manifeste du complément.


2. Le code dans le bloc **case** AppUninstalling devrait supprimer les artefacts du complément qui ne sont pas nécessaires après que le complément a été supprimé de la corbeille secondaire, ce qui déclenche l'événement. Toutefois, lorsque c'est possible, vous devez « retirer » les composants plutôt que les supprimer totalement. Cela est dû au fait que vous devez les restaurer si l'événement de désinstallation doit être annulé. Si cela se produit, le complément est toujours dans la corbeille secondaire ; un utilisateur pourrait le restaurer et recommencer à l'utiliser. Le fait de simplement recréer un composant supprimé dans votre logique de restauration devrait suffire pour permettre au complément de fonctionner à nouveau, mais tout paramètre de données ou de configuration serait perdu.

    Cette stratégie est relativement facile pour les composants SharePoint, puisque SharePoint dispose d'une corbeille depuis laquelle des restaurations sont possibles, et il existe des API CSOM pour y accéder. Les étapes suivantes de cette procédure montrent la manière de procéder. Différentes techniques peuvent être nécessaires pour d'autres plateformes. Par exemple, si vous voulez retirer une ligne d'une table SQL Server dans votre gestionnaire de désinstallation de complément, une procédure T-SQL stockée dans le gestionnaire peut ajouter une colonne IsDeleted à la table et la définir sur la valeur **True** pour la ligne. Si la procédure rencontre une erreur, la logique de restauration réinitialise la valeur sur **False**. Si la procédure se termine sans erreur, elle peut définir un travail du minuteur pour supprimer la ligne ultérieurement, juste avant qu'elle ne renvoie un indicateur de réussite.

    Parfois, vous souhaitez conserver des données, telles que des listes, même après la suppression du complément. Cependant, à titre d'exemple pour cet article, voici un gestionnaire d'événements de désinstallation qui supprime la liste créée avec le gestionnaire d'événements installé.



 ```cs

case SPRemoteEventType.AppUninstalling:

try
{
    string error = TryRecycleList(listTitle, properties);
    if (error != String.Empty)
    {
        throw new Exception(error);
    }
}
catch (Exception e)
{
    // Tell SharePoint to cancel the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
break;
 ```

3. Ajoutez la méthode d'assistance pour le recyclage de la liste. Notez les points suivants concernant ce code :

  - Le code recycle la liste au lieu de la supprimer de façon permanente. Cela rend sa restauration possible, y compris celle de ses données, en cas d'échec de l'événement, ce en quoi consiste le bloc **StartCatch**. Ainsi, en cas de réussite de la méthode et une fois l'événement terminé, le complément est supprimé de façon permanente de la corbeille secondaire, mais la liste est toujours dans la corbeille primaire. 


  - Le code vérifie l'existence de la liste avant de la recycler, car un utilisateur pourrait l'avoir déjà recyclée dans l'interface SharePoint. De la même manière, le code de restauration vérifie l'existence de la liste dans la corbeille avant de la restaurer, car un utilisateur pourrait l'avoir déjà restaurée ou déplacée vers la corbeille secondaire. 


  - Il existe deux étendues conditionnelles qui testent l'existence d'une liste en vérifiant si une référence à celle-ci possède la valeur **null**. Cependant, toutes les deux disposent d'un bloc **if** interne qui teste la nullité du même objet une seconde fois. Les tests externes, avec des blocs d'étendues conditionnelles, s'exécutent sur le serveur, mais les tests internes de nullité sont également nécessaires. Cela est dû au fait que l'exécution du client parcourt le code ligne par ligne pour créer le message XML que la méthode **ExecuteQuery** enverra au serveur. Lorsque les références aux objets **foundList** et **recycledList** sont atteintes, l'une ou l'autre de ces lignes déclenchent une exception de référence Null, à moins qu'elles ne soient comprises dans les vérifications internes de nullité.



 ```cs

private string TryRecycleList(String listTitle, SPRemoteEventProperties properties)
{
    string errorMessage = String.Empty;

    using (ClientContext clientContext = 
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
            ListCollection allLists = clientContext.Web.Lists;
            IEnumerable<List> matchingLists = 
                clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
            RecycleBinItemCollection bin = clientContext.Web.RecycleBin;
            IEnumerable<RecycleBinItem> matchingRecycleBinItems = 
                clientContext.LoadQuery(bin.Where(item => item.Title == listTitle));    
            clientContext.ExecuteQuery();

            List foundList = matchingLists.FirstOrDefault();
            RecycleBinItem recycledList = matchingRecycleBinItems.FirstOrDefault();

            ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext);
            using (scope.StartScope())
            {
                using (scope.StartTry())
                {
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                        () => foundList.ServerObjectIsNull.Value == false, true);
                    using (condScope.StartScope())
                    {
                        if (foundList != null)
                        {
                            foundList.Recycle();
                        }
                    }
                }
                using (scope.StartCatch())
                {
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                         () => recycledList.ServerObjectIsNull.Value == false, true);
                    using (condScope.StartScope())
                    {
                        if (recycledList != null)
                        {
                            recycledList.Restore(); 
                        }
                    }
                }
                using (scope.StartFinally())
                {
                }
            }
            clientContext.ExecuteQuery();

            if (scope.HasException)
            {
                errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
                    scope.ServerErrorTypeName, scope.ErrorMessage, 
                    scope.ServerErrorDetails, scope.ServerErrorValue, 
                    scope.ServerStackTrace, scope.ServerErrorCode);
            }
        }
    }
    return errorMessage;
}
 ```


### Pour déboguer et tester un récepteur d'événements de désinstallation de complément


1. Ouvrez toutes les pages suivantes dans des fenêtres ou des onglets distincts :

  - **Contenu du site**


  - **Paramètres du site - Corbeille** (_layouts/15/AdminRecycleBin.aspx?ql=1)


  - **Corbeille - Corbeille secondaire** (_layouts/15/AdminRecycleBin.aspxView=2&amp;?ql=1)


2. Appuyez sur F5 et approuvez le complément lorsque vous y êtes invité. La page de démarrage du complément s'ouvre. Si vous envisagez seulement de tester le gestionnaire de désinstallation, vous pouvez fermer cette fenêtre de navigateur.  *En revanche, si vous déboguez le gestionnaire, laissez-la ouverte. La fermer mettra fin à la session de débogage.* 


3. Actualisez la page **Contenu du site** et, lorsque le complément s'affiche, supprimez-le.


4. Actualisez la page **Paramètres du site - Corbeille**. Le complément s'affiche comme premier élément. Cochez la case située à côté et cliquez sur **Supprimer la sélection**.


5. Actualisez la page **Corbeille - Corbeille secondaire**. Le complément s'affiche comme premier élément. Cochez la case située à côté et cliquez sur **Supprimer la sélection**. SharePoint appelle immédiatement votre gestionnaire de désinstallation de complément.



## Créer un récepteur d'événements de mise à jour de complément
<a name="SP15appevent_prereq"> </a>

Pour plus de détails sur la création d'un gestionnaire de mise à jour de complément, consultez  [Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).




## Restrictions d'URL et d'hébergement sur les récepteurs d'événements de complément de production
<a name="SP15appevent_prereq"> </a>

Le récepteur d'événements de complément peut être hébergé dans le Cloud ou dans un serveur sur site qui n'est pas également utilisé en tant que serveur SharePoint. L'URL d'un récepteur de production ne peut pas spécifier un port particulier. Cela signifie que vous devez utiliser le port 443 pour HTTPS, ce que nous recommandons, ou le port 80 pour HTTP. Si vous utilisez HTTPS et que le service de récepteur est hébergé sur site, mais que le complément est sur Microsoft SharePoint Online, le serveur d'hébergement doit avoir un certificat approuvé publiquement, émanant d'une autorité de certification. (Un certificat auto-signé ne fonctionne que si le complément est dans une batterie de serveurs SharePoint sur site.)




## Ressources supplémentaires
<a name="SP15appevent_addlresources"> </a>


-  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md)



