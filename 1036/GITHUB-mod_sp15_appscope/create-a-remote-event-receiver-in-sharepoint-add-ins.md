---
title: Créer un récepteur d'événements distant dans des compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 628c6103-52f9-4d85-9464-4a6862b36640
---


# Créer un récepteur d'événements distant dans des compléments pour SharePoint
Créez un récepteur d'événements distant qui gère les événements de liste et d'élément de liste dans un Complément SharePoint.
## Conditions requises
<a name="SP15appevent_prereq"> </a>

Il est utile d'avoir d'abord des connaissances sur les Compléments SharePoint hébergés par un fournisseur, et d'avoir une expérience de développement allant au-delà du niveau « Hello World ». Vous devez aussi savoir comment  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md). 
  
    
    

## Créer un récepteur d'événement distant
<a name="MakeRER"> </a>

Cet article décrit comment étendre un Complément SharePoint en ajoutant un récepteur d'événements distant qui gère l'événement ItemAdded pour une liste personnalisée dans le site web de complément. Le récepteur d'événements distant est enregistré avec le site web de complément à l'aide du balisage déclaratif. Les récepteurs d'événements distants sont enregistrés avec le  *site web hôte*  par programme. Pour consulter un exemple de code qui exécute cette tâche, voir [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers).
  
    
    
Un récepteur d'événement distant doit être un service web SOAP. L'exemple en cours l'implémente comme un service Windows Communication Foundation (WCF), mais il est en principe possible d'implémenter un récepteur d'événement distant sur une pile non Microsoft.
  
    
    
Pour suivre cet article et entrer le code vous-même, téléchargez l'exemple  [SharePoint-Add-in-CSOM-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-CSOM-BasicDataOperations), puis ouvrez l'exemple dans Visual Studio.
  
    
    

> **REMARQUE**
> Cet exemple utilise un fichier TokenHelper.cs généré par Outils de développement Office pour Visual Studio. Il s'agissait de la version en cours lors de la création de l'exemple, mais il existe peut-être maintenant une version plus récente. L'exemple est tout de même utile pour créer votre premier récepteur d'événement distant. Cependant, lorsque vous serez prêt à aller plus loin, consultez les exemples répertoriés dans la section Étapes suivantes ci-dessous, car ils sont mis à jour plus régulièrement. 
  
    
    


### Pour enregistrer un récepteur d'événement distant


1. Ouvrez le projet d'Complément SharePoint dans Visual Studio. 
    
  
2.  Dans l' **Explorateur de solutions**, cliquez sur le nœud du projet de complément.
    
  
3. Dans la barre de menus, choisissez **Projet**, **Ajouter un nouvel élément**.
    
  
4. Dans le volet **Modèles installés**, sélectionnez le nœud **Office/ SharePoint**.
    
  
5. Dans le volet **Modèles**, sélectionnez le modèle **Récepteur d'événements distants**.
    
  
6. Dans la zone **Nom**, conservez le nom par défaut (RemoteEventReceiver1), puis cliquez sur le bouton **Ajouter**.
    
  
7. Dans la liste **Quel type de récepteur d'événements voulez-vous ?**, sélectionnez **Liste des événements d'élément**.
    
  
8. Dans la liste **Quel élément doit être la source d'événement ?**, sélectionnez **Liste personnalisée**.
    
    L'exemple en cours utilise une liste générique personnalisée. Mais un récepteur d'événement distant peut également gérer les événements qui se produisent dans des listes SharePoint standard comme **Annonces** ou **Contacts**.
    
  
9. Dans la liste **Gérer les événements suivants**, sélectionnez **Un élément est en cours d'ajout**, puis cliquez sur le bouton **Terminer**.
    
    Un service web est ajouté à l'application web pour gérer l'événement à distance que vous avez spécifié. Un récepteur d'événements distant est ajouté au Complément SharePoint et l'événement d'élément de liste est référencé dans le fichier Elements.xml du récepteur, qui est lui-même contenu dans la fonctionnalité de site web de complément.
    
  

### Pour créer la liste


1. Dans l' **Explorateur de solutions**, sélectionnez le nœud du projet de complément.
    
  
2. Dans la barre de menus, sélectionnez **Projet**, puis **Ajouter un nouvel élément**.
    
  
3. Dans le volet **Modèles installés**, sélectionnez le nœud **Office SharePoint**.
    
  
4. Dans le volet **Modèles**, sélectionnez le modèle **Liste**.
    
  
5. Dans la zone **Nom**, conservez le nom par défaut (List1), puis cliquez sur le bouton **Ajouter**.
    
  
6. Cliquez sur la case d'option **Créer une instance de liste basée sur un modèle de liste existant**, sélectionnez **Liste personnalisée** dans la liste, puis cliquez sur le bouton **Terminer**.
    
  

### Pour ajouter des fonctionnalités au récepteur d'événement distant


1. Si votre batterie SharePoint de test ne se trouve pas sur l'ordinateur qui exécute Visual Studio (ou si vous utilisez un client SharePoint Online comme site SharePoint de test), configurez le projet pour débogage à l'aide du bus des services Microsoft Azure. Pour plus d'informations, consultez la rubrique  [Déboguer et dépanner un récepteur d'événement distant dans un complément pour SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). 
    
  
2. Dans le fichier de code du service du récepteur d'événement distant (à savoir, RemoteEventReceiver1.svc.cs), remplacez le contenu par le code suivant.
    
    Ce code effectue les tâches ci-après.
    
  - Il obtient un objet de contexte client valide. 
    
  
  - S'il n'existe pas de liste appelée **EventLog**, créez-la afin qu'elle contienne le nom des événements distants qui se produisent.
    
  
  - Il ajoute une entrée à la liste pour l'événement, notamment un cachet de date et d'heure.
    
  

    > **REMARQUE**
      > Au moment où cet article a été écrit, les Outils de développement Office pour Visual Studio ajoutent des références à toutes les assemblys nécessaires lorsque le récepteur est créé, mais il est possible que les versions ultérieures des outils ne le permettent pas. Si vous obtenez des erreurs de compilation, ajoutez simplement les références manquantes ; par exemple, vous devrez peut-être ajouter des références à System.ServiceModel ou System.ComponentModel.DataAnnotations. 



 ```cs
  
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Text;
using Microsoft.SharePoint.Client;
using Microsoft.SharePoint.Client.EventReceivers;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.ServiceModel.Channels;


namespace BasicDataOperationsWeb.Services
{
    public class RemoteEventReceiver1 : IRemoteEventService
    {
        public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {
            // When a "before" event occurs (such as ItemAdding), call the event 
            // receiver code.
            ListRemoteEventReceiver(properties);
            return new SPRemoteEventResult();
        }

        public void ProcessOneWayEvent(SPRemoteEventProperties properties)
        {
            // When an "after" event occurs (such as ItemAdded), call the event 
            // receiver code.            
        }

        public static void ListRemoteEventReceiver(SPRemoteEventProperties properties)
        {
            string logListTitle = "EventLog";

            // Return if the event is from the EventLog list itself. Otherwise, it may go into
            // an infinite loop.
            if (string.Equals(properties.ItemEventProperties.ListTitle, logListTitle, 
                  StringComparison.OrdinalIgnoreCase))
                return;

            // Get the token from the request header.
            HttpRequestMessageProperty requestProperty = 
                  (HttpRequestMessageProperty)OperationContext
                   .Current.IncomingMessageProperties[HttpRequestMessageProperty.Name];
            string contextTokenString = requestProperty.Headers["X-SP-ContextToken"];

            // If there is a valid token, continue.
            if (contextTokenString != null)
            {
                SharePointContextToken contextToken =
                    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                         requestProperty.Headers[HttpRequestHeader.Host]);

                Uri sharepointUrl = new Uri(properties.ItemEventProperties.WebUrl);
                string accessToken = TokenHelper.GetAccessToken(contextToken, 
                                                      sharepointUrl.Authority).AccessToken;
                bool exists = false;

                // Retrieve the log list "EventLog" and add the name of the event that occurred
                // to it with a date/time stamp.
                using (ClientContext clientContext = 
                     TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), 
                                                                                                         accessToken))
                {
                    clientContext.Load(clientContext.Web);
                    clientContext.ExecuteQuery();
                    List logList = clientContext.Web.Lists.GetByTitle(logListTitle);

                    try
                    {
                        clientContext.Load(logList);
                        clientContext.ExecuteQuery();
                        exists = true;
                    }

                    catch (Microsoft.SharePoint.Client.ServerUnauthorizedAccessException)
                    {
                        // If the user doesn't have permissions to access the server that's 
                        // running SharePoint, return.
                        return;
                    }

                    catch (Microsoft.SharePoint.Client.ServerException)
                    {
                        // If an error occurs on the server that's running SharePoint, return.
                        exists = false;
                    }

                    // Create a log list called "EventLog" if it doesn't already exist.
                    if (!exists)
                    {
                        ListCreationInformation listInfo = new ListCreationInformation();
                        listInfo.Title = logListTitle;
                        // Create a generic custom list.
                        listInfo.TemplateType = 100;
                        clientContext.Web.Lists.Add(listInfo);
                        clientContext.Web.Context.ExecuteQuery();
                    }

                    // Add the event entry to the EventLog list.
                    string itemTitle = "Event: " + properties.EventType.ToString() + 
                          " occurred on: " + 
                          DateTime.Now.ToString(" yyyy/MM/dd/HH:mm:ss:fffffff");
                    ListCollection lists = clientContext.Web.Lists;
                    List selectedList = lists.GetByTitle(logListTitle);
                    clientContext.Load<ListCollection>(lists);
                    clientContext.Load<List>(selectedList);
                    ListItemCreationInformation listItemCreationInfo = 
                          new ListItemCreationInformation();
                    var listItem = selectedList.AddItem(listItemCreationInfo);
                    listItem["Title"] = itemTitle;
                    listItem.Update();
                    clientContext.ExecuteQuery();
                }
            }
        }
    }
}
 ```

3. Dans le fichier Home.aspx.cs, remplacez toutes les instances de  `SPHostUrl` par `SPAppWebUrl`.
    
    Par exemple,  `sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);` doit être remplacé par `sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);`. 
    
  

## Exécuter et tester le gestionnaire d'événement
<a name="RunAndTest"> </a>

Testez votre gestionnaire avec la procédure suivante.
  
    
    

1. Appuyez sur la touche **F5** pour exécuter le projet.
    
  
2. Approuvez le complément lorsque vous êtes invité à le faire.
    
    Votre Complément SharePoint s'exécute. Un tableau contenant les listes disponibles s'affiche en incluant **List1**.
    
  
3. Sélectionnez l'ID de **List1**.
    
    Cet ID est copié dans la zone **Récupérer les éléments de liste**.
    
  
4. Cliquez sur le bouton **Récupérer les éléments de liste**.
    
    **List1** apparaît sans contenir d'élément.
    
  
5. Dans la zone **Ajouter un élément**, indiquez First Item (Premier élément), puis cliquez sur le bouton **Ajouter un élément**.
    
    Un élément de liste appelé **First Item** est ajouté à **List1**, ce qui déclenche le récepteur d'événements distants et ajoute une entrée à la liste EventLog.
    
  
6. Cliquez sur le bouton **Actualiser les listes** pour retourner dans le tableau des listes.
    
    Dans le tableau, une nouvelle liste appelée **EventLog** apparaît.
    
  
7. Sélectionnez la valeur de GUID **ListID** pour **EventLog**, puis cliquez sur le bouton **Récupérer les éléments de liste**.
    
    Un tableau pour **EventLog** apparaît. Il contient une entrée pour l'événement **Handle ItemAdding** qui s'est produit lorsque vous avez ajouté l'élément à **List1**.
    
  

## Ajouter ou supprimer des gestionnaires d'événement à l'aide de Visual Studio
<a name="Handle"> </a>


1. Dans l' **Explorateur de solutions**, choisissez le nœud de projet du récepteur d'événements distants.
    
  
2. Dans le volet **Propriétés**, définissez sur **True** les propriétés des événements à gérer.
    
    Par exemple, si vous voulez répondre chaque fois qu'un utilisateur ajoute un élément de liste, définissez la valeur de la propriété **Handle ItemAdding** sur **True**. Si vous ne voulez pas gérer cet événement, définissez la valeur de cette propriété sur **False**.
    

   **Figure 1. Événements distants SharePoint dans Visual Studio**

  

!\[Événements distants SharePoint dans Visual Studio](images/SP_VS_Properties_Window_RemoteEvents.PNG)
  

  

  
3. Si vous avez ajouté un événement, ajoutez le code de gestion des événements au fichier de code pour le service web comme vous l'avez fait avec les événements précédents.
    
    Pour gérer un type différent d'événement, ajoutez un autre récepteur d'événements distant à l'Complément SharePoint. Par exemple, si un récepteur d'événements distants gère des événements d'élément de liste, vous pouvez ajouter un autre événement d'élément de liste à celui-ci. Mais, vous devez ajouter un autre récepteur d'événements distants si vous voulez gérer des événements de liste. 
    
  

## URL et restrictions d'hébergement pour les récepteurs d'événements distants de production
<a name="Handle"> </a>

Le récepteur d'événements distant peut être hébergé dans le cloud ou dans un serveur sur site qui n'est pas également utilisé en tant que serveur SharePoint. L'URL d'un récepteur de production ne peut pas spécifier un port particulier. Cela signifie que vous devez utiliser le port 443 pour HTTPS, ce que nous recommandons, ou le port 80 pour HTTP. Si vous utilisez HTTPS et que le service de récepteur est hébergé sur site, mais que le complément est sur Microsoft SharePoint Online, le serveur d'hébergement doit avoir un certificat approuvé publiquement, émanant d'une autorité de certification. (Un certificat auto-signé ne fonctionne que si le complément est dans une batterie de serveurs SharePoint sur site.)
  
    
    

## Étapes suivantes
<a name="Handle"> </a>

Les exemples de code suivants vous permettront d'approfondir vos connaissances en matière de récepteur d'événements distants :
  
    
    

-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)
    
  
-  [OfficeDev/PnP/Samples/Provisioning.ReR](
https://github.com/OfficeDev/PnP/tree/master/Samples/Provisioning.ReR)
    
  
-  [OfficeDev/PnP/Scenarios/ECM.AutoTagging](https://github.com/OfficeDev/PnP/tree/master/Samples/ECM.AutoTagging)
    
  

## Ressources supplémentaires
<a name="Additional"> </a>


-  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Déboguer et dépanner un récepteur d'événement distant dans un complément pour SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)
    
  
-  [Questions les plus fréquentes sur les récepteurs d'événements distants](handle-events-in-sharepoint-add-ins.md#RERFAQ)
    
  

