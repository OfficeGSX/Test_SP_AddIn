---
title: Erstellen eines Remoteereignisempfängers in Add-Ins für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 628c6103-52f9-4d85-9464-4a6862b36640
---


# Erstellen eines Remoteereignisempfängers in Add-Ins für SharePoint
Erstellen eines Remoteereignisempfängers (RER), der Listen- und Listenelementereignisse in einer SharePoint-Add-In verarbeitet.
## Voraussetzungen
<a name="SP15appevent_prereq"> </a>

Sie sollten die Grundlagen vom Anbieter gehosteter SharePoint-Add-Ins verstehen und bereits ein paar entwickelt haben, die wenigstens geringfügig über die Komplexität von "Hello World" hinausgehen. Ferner sollten Sie mit dem  [Behandeln von Ereignissen in SharePoint-Add-Ins](handle-events-in-sharepoint-add-ins.md) vertraut sein.
  
    
    

## Erstellen eines Remoteereignisempfängers
<a name="MakeRER"> </a>

In diesem Artikel wird gezeigt, wie Sie eine SharePoint-Add-In durch Hinzufügen eines Remoteereignisempfängers (RER) erweitern, der das ItemAdded-Ereignis für eine benutzerdefinierte Liste im Add-In-Web verarbeitet. Der RER ist im Add-In-Web mit deklarativem Markup registriert. RERs werden programmgesteuert beim  *Hostweb*  registriert. Ein Codebeispiel dazu finden Sie unter [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers).
  
    
    
Ein RER muss ein SOAP-Webdienst sein. Im fortlaufenden Beispiel wird dieser als WCF-Dienst (Windows Communication Foundation) implementiert. Es ist jedoch im Prinzip möglich, einen RER auf einem nicht von Microsoft stammenden Stapel zu implementieren.
  
    
    
Um diesem Artikel zu folgen und den Code selbst einzugeben, laden Sie das Beispiel von der Website  [SharePoint-Add-in-CSOM-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-CSOM-BasicDataOperations) herunter, und öffnen Sie das Beispiel anschließend in Visual Studio.
  
    
    

> **HINWEIS**
> Dieses Beispiel verwendet eine TokenHelper.cs-Datei, die mithilfe von Office-Entwicklertools für Visual Studio generiert wurde. Bei Erstellung des Beispiels war dies die aktuelle Version, aber wenn Sie diesen Artikel lesen, ist dies möglicherweise nicht die aktuellste Version. Das Beispiel ist weiterhin hervorragend für das Erstellen Ihrer ersten RER geeignet. Aber wenn Sie bereit sind, darüber hinaus zu gehen, sollten Sie sich die Beispiele im Abschnitt „Nächste Schritte" ansehen. Diese sind wahrscheinlich auf dem neuesten Stand. 
  
    
    


### So registrieren Sie einen Remoteereignisempfänger


1. Öffnen Sie das SharePoint-Add-In-Projekt in Visual Studio. 
    
  
2.  Wählen Sie im **Projektmappen-Explorer** den Knoten des Add-In-Projekts aus.
    
  
3. Wählen Sie in der Menüleiste **Projekt**, **Neues Element hinzufügen**.
    
  
4. Wählen Sie im Bereich **Installierte Vorlagen** den Knoten **Office/SharePoint** aus.
    
  
5. Wählen Sie im Bereich **Vorlagen** die Vorlage **Remoteereignisempfänger** aus.
    
  
6. Lassen Sie den Standardnamen im Feld **Name** unverändert (RemoteEventReceiver1), und wählen Sie dann **Hinzufügen** aus.
    
  
7. Wählen Sie in der Liste **Welchen Typ soll der Ereignisempfänger aufweisen?** die Option **Listenelementereignisse** aus.
    
  
8. Wählen Sie in der Liste **Welches Element soll als Ereignisquelle dienen?** die Option **Benutzerdefinierte Liste** aus.
    
    Im fortlaufenden Beispiel wird eine benutzerdefinierte generische Liste verwendet. Aber ein RER kann auch Ereignisse behandeln, die in SharePoint-Standardlisten auftreten, beispielsweise **Ankündigungen** oder **Kontakte**.
    
  
9. Wählen Sie in der Liste **Die folgenden Ereignisse behandeln** die Option **Ein Element wird hinzugefügt** aus, und klicken Sie anschließend auf **Fertig stellen**.
    
    Der Webanwendung wird ein Webdienst zur Behandlung des von Ihnen angegebenen Remoteereignisses hinzugefügt. Der SharePoint-Add-In wird ein Remoteereignisempfänger hinzugefügt, und auf das Listenelementereignis wird in der Datei "Elements.xml" des Empfängers verwiesen, die wiederum im Add-In-Web-Feature enthalten ist.
    
  

### So erstellen Sie die Liste


1. Wählen Sie im **Projektmappen-Explorer** den Knoten des Add-In-Projekts aus.
    
  
2. Wählen Sie in der Menüleiste **Projekt**, **Neues Element hinzufügen**.
    
  
3. Wählen Sie im Bereich **Installierte Vorlagen** den Knoten **Office SharePoint** aus.
    
  
4. Wählen Sie im Bereich **Vorlagen** die Vorlage **Liste** aus.
    
  
5. Lassen Sie den Standardnamen im Feld **Name** unverändert (List1), und wählen Sie dann **Hinzufügen** aus.
    
  
6. Aktivieren Sie das Optionsfeld **Listeninstanz basierend auf einer bestehenden Listenvorlage erstellen**, wählen Sie in der Liste **Benutzerdefinierte Liste** aus, und klicken Sie auf **Fertig stellen**.
    
  

### So fügen Sie dem Remoteereignisempfänger Funktionen hinzu


1. Wenn sich die SharePoint-Testfarm nicht auf dem Computer mit Visual Studio befindet (oder Sie einen SharePoint Online-Mandanten als SharePoint-Testwebsite verwenden), konfigurieren Sie das Projekt für das Debuggen mit dem Microsoft Azure Service Bus. Weitere Informationen finden Sie unter  [Debugging und Problembehandlung eines Remoteereignisempfängers in einem Add-In für SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). 
    
  
2. Ersetzen Sie den Inhalt der Codedatei für den Dienst des Remoteereignisempfängers (d. h. **RemoteEventReceiver1.svc.cs**) durch den folgenden Code.
    
    Dieser Code führt die folgenden Aufgaben aus.
    
  - Er ruft einen gültigen Clientkontextobjekt ab. 
    
  
  - Wenn nicht bereits eine Liste mit dem Namen **EventLog** vorhanden ist, erstellt er eine für die Namen der auftretenden Remoteereignisse.
    
  
  - Er fügt der Liste einen Eintrag für das Ereignis hinzu, einschließlich Uhrzeit- und Datumsstempel.
    
  

    > **HINWEIS**
      > Zu dem Zeitpunkt, zu dem dieser Artikel geschrieben wurde, wurden von Office-Entwicklertools für Visual Studio alle Verweise auf alle benötigten Assemblys hinzugefügt, als der Empfänger erstellt wurde. In höheren Versionen des Tools ist dies möglicherweise nicht der Fall. Wenn Compilerfehler auftreten, fügen Sie einfach die fehlenden Verweise hinzu. Möglicherweise müssen Sie Verweise auf System.ServiceModel oder System.ComponentModel.DataAnnotations hinzufügen. 



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

3. Ändern Sie in **Home.aspx.cs** alle Instanzen von `SPHostUrl` in `SPAppWebUrl`.
    
    Beispielsweise muss  `sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);` in `sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);` geändert werden.
    
  

## Ausführen und Testen des Ereignishandlers
<a name="RunAndTest"> </a>

Testen Sie den Handler mit dem folgenden Verfahren.
  
    
    

1. Drücken Sie **F5**, um das Projekt auszuführen.
    
  
2. Vertrauen Sie dem Add-In, wenn Sie dazu aufgefordert werden.
    
    Ihre SharePoint-Add-In wird ausgeführt, und eine Tabelle mit verfügbaren Listen wird angezeigt und enthält **List1**.
    
  
3. Wählen Sie die ID von **List1** aus.
    
    Die ID wird in das Feld **Listenelemente abrufen** kopiert.
    
  
4. Klicken Sie auf die Schaltfläche **Listenelemente abrufen**.
    
    **List1** wird ohne Elemente angezeigt.
    
  
5. Geben Sie im Feld **Element hinzufügen** den WertErstes Element ein, und klicken Sie anschließend auf **Element hinzufügen**.
    
    Ein Listeneintrag mit dem Namen **Erstes Element** wird der Liste **List1** hinzugefügt, was dazu führt, dass der Remoteereignisempfänger ausgelöst wird und einen Eintrag im Ereignisprotokoll ("EventLog") hinzufügt.
    
  
6. Klicken Sie auf die Schaltfläche **Liste aktualisieren**, um zur Tabelle mit den Listen zurückzukehren.
    
    In der Tabelle wird eine neue Liste mit dem Namen **EventLog** angezeigt.
    
  
7. Wählen Sie den GUID-Wert **ListID** für **EventLog** aus, und klicken Sie anschließend auf die Schaltfläche **Listenelemente abrufen**.
    
    Eine Tabelle für **EventLog** wird mit einem Eintrag für das Ereignis **Handle ItemAdding** angezeigt, das aufgetreten ist, als Sie das Element der Liste **List1** hinzugefügt haben.
    
  

## Hinzufügen oder Entfernen von Ereignishandlern mit Visual Studio
<a name="Handle"> </a>


1. Wählen Sie im **Projektmappen-Explorer** den Projektknoten für den Remoteereignisempfänger aus.
    
  
2. Legen Sie im Bereich **Eigenschaften** die Eigenschaften von Ereignissen, die Sie behandeln möchten, auf **True** fest.
    
    Wenn Sie beispielsweise darauf reagieren möchten, wenn ein Benutzer ein Listenelement hinzufügt, dann legen Sie die **Handle ItemAdding**-Eigenschaft auf **True** fest. Wenn Sie dieses Ereignis nicht behandeln möchten, dann legen Sie diese Eigenschaft auf **False** fest.
    

   **Abbildung 1: SharePoint-Remoteereignisse in Visual Studio**

  

!\[SharePoint-Remoteereignisse in Visual Studio](images/SP_VS_Properties_Window_RemoteEvents.PNG)
  

  

  
3. Wenn Sie ein Ereignis hinzugefügt haben, dann fügen Sie den Ereignishandlercode so wie bei den vorherigen Ereignissen der Codedatei für den Webdienst hinzu.
    
    Zur Behandlung eines anderen Ereignistyps fügen Sie der SharePoint-Add-In einen weiteren Remoteereignisempfänger hinzu. Wenn ein Remoteereignisempfänger z. B. Listenelementereignisse behandelt, dann können sie ihm ein weiteres Listenelementereignis hinzufügen. Zur Behandlung von Listenereignissen müssen Sie jedoch einen weiteren Remoteereignisempfänger hinzufügen. 
    
  

## URL- und Hosteinschränkungen für produktionsferne Ereignisempfänger
<a name="Handle"> </a>

Der Remoteereignisempfänger kann in der Cloud oder auf einem lokalen Server gehostet werden, der nicht auch als SharePoint-Server verwendet wird. Die URL eines Produktionsempfängers kann keinen bestimmten Port angeben. Dies bedeutet, dass Sie entweder Port 443 für HTTPS (Empfehlung) oder Port 80 für HTTP verwenden müssen. Wenn Sie HTTPS verwenden und der Empfängerdienst lokal gehostet wird, das Add-In sich jedoch auf Microsoft SharePoint Online befindet, muss der Hostserver über ein öffentlich vertrauenswürdiges Zertifikat von einer Zertifizierungsstelle verfügen. (Ein selbst signiertes Zertifikat funktioniert nur, wenn sich das Add-In in einer lokalen SharePoint-Farm befindet.)
  
    
    

## Nächste Schritte
<a name="Handle"> </a>

Verwenden Sie die folgenden Codebeispiele, um Ihr Verständnis von RERs zu vertiefen:
  
    
    

-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)
    
  
-  [OfficeDev/PnP/Samples/Provisioning.ReR](
https://github.com/OfficeDev/PnP/tree/master/Samples/Provisioning.ReR)
    
  
-  [OfficeDev/PnP/Scenarios/ECM.AutoTagging](https://github.com/OfficeDev/PnP/tree/master/Samples/ECM.AutoTagging)
    
  

## Zusätzliche Ressourcen
<a name="Additional"> </a>


-  [Behandeln von Ereignissen in SharePoint-Add-Ins](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Debugging und Problembehandlung eines Remoteereignisempfängers in einem Add-In für SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)
    
  
-  [FAQ für Remote-Ereignisempfänger](handle-events-in-sharepoint-add-ins.md#RERFAQ)
    
  

