---
title: Erstellen eines Add-In-Ereignisempfängers in SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: f40c910f-12a2-4caa-8e91-c7a61ae540db
---


# Erstellen eines Add-In-Ereignisempfängers in SharePoint-Add-Ins
Erstellen von Handlern für SharePoint-Add-In-Installations- und Deinstallationsereignisse in SharePoint-Add-Ins.
## Voraussetzungen
<a name="SP15appevent_prereq"> </a>

In diesem Artikel wird vorausgesetzt, dass Sie die Grundlagen vom Anbieter gehosteter SharePoint-Add-Ins verstehen und bereits Apps entwickelt haben, die wenigstens geringfügig über die Komplexität von "Hello World" hinausgehen. Ferner sollten Sie mit dem  [Behandeln von Ereignissen in SharePoint-Add-Ins](handle-events-in-sharepoint-add-ins.md) vertraut sein.
  
    
    

## Abrufen weiterer Codebeispiele
<a name="SP15appevent_prereq"> </a>

Wenn Sie das fortlaufende Beispiel in diesem Artikel durcharbeiten, verfügen Sie über ein fertiges Codebeispiel. Im Folgenden finden Sie weitere Beispiele. Sie folgen nicht alle der hier beschriebenen Architektur. Es kann mehr als eine gute Möglichkeit geben, einen Add-In-Ereignisempfänger zu entwerfen. Bedenken Sie auch, dass Microsoft möglicherweise mit der Zeit weitere Anleitungen bereitstellen wird. 
  
    
    

-  [OfficeDev/PnP/Samples/Core.AppEvents.HandlerDelegation](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents.HandlerDelegation) ist eine enge Übereinstimmung mit dem fortlaufenden Beispiel in diesem Artikel.
    
  
-  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents) zeigt, wie Sie dieselbe Aufgabe wie im vorstehenden Beispiel in Szenarien durchführen können, in denen die Handlerdelegierungsstrategie nicht verwendet werden kann.
    
  
-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)
    
  
-  [Erstellen eines vom Anbieter gehosteten Add-Ins mit angepasster Add-In-Installation](https://code.msdn.microsoft.com/SharePoint-2013-Create-a-f27752e0)
    
  

## Erstellen eines Ereignisempfängers für das installierte Add-In
<a name="SP15appevent_prereq"> </a>


1. Öffnen Sie in Visual Studio das Projekt für das vom Anbieter gehostete SharePoint-Add-In. (Wenn Sie einen Add-In-Ereignishandler für ein in SharePoint gehostetes Add-In hinzufügen, wird sie von den Office-Entwicklertools für Visual Studio in ein vom Anbieter gehostetes Add-In umgewandelt.)
    
  
2. Wählen Sie im **Projektmappen-Explorer** den Knoten für die SharePoint-Add-In aus.
    
  
3. Legen Sie im Fenster **Eigenschaften** den Wert von **Installiertes Add-In behandeln** auf **True** fest.
    
   **Abbildung 1: Add-In-Ereignisse im Fenster Eigenschaften**

  

     ![App-Ereignisse im Eigenschaftenfenster](images/SP_VS_Properties_Window_AppEvents.PNG)
  

    Von den Office-Entwicklertools für Visual Studio wird Folgendes ausgeführt:
    
  - Eine Datei **AppEventReceiver.svc** mit grundlegendem C#-Code (oder VB.NET-Code) wird hinzugefügt. Dies ist der Dienst, der das Add-In-Ereignis behandelt.
    
  
  - Der folgende Eintrag wird im Abschnitt **Eigenschaften** der Datei AppManifest.xml hinzugefügt: `<InstalledEventEndpoint>~remoteAppUrl/AppEventReceiver.svc</InstalledEventEndpoint>`. Dieser Eintrag registriert den Add-In-Ereignisempfänger in SharePoint. (Beachten Sie, dass die **~remoteAppUrl** die gleiche ist, die für die Remotewebanwendung in dem vom Anbieter gehosteten SharePoint-Add-In verwendet wurde. In den Office-Entwicklertools für Visual Studio wird davon ausgegangen, dass die Domäne der Webanwendung mit der des Ereignishandlers identisch ist. In den seltenen Fällen, wo dies nicht gilt, müssen Sie das Token **~remoteAppUrl** manuell durch die tatsächliche Domäne des Diensts ersetzen.)
    
  
  - Wenn das SharePoint-Add-In-Projekt nicht bereits ein Webprojekt aufweist, wird eines von den Office-Entwicklertools für Visual Studio erstellt. Die Tools stellen außerdem sicher, dass das Add-In-Manifest für ein vom Anbieter gehostetes Add-In konfiguriert ist. Außerdem fügen sie Seiten, Skripts, CSS-Dateien und andere Artefakte hinzu. (Wenn die einzige Remotekomponente, die Ihr Add-In benötigt, der Webdienst für die Ereignisbehandlung ist, können Sie diese aus dem Projekt löschen. Außerdem sollten Sie sicherstellen, dass das **StartPage**-Element im Add-In-Manifest nicht auf eine Seite verweist, die Sie gelöscht haben.)
    
  
4. Wenn sich die SharePoint-Testfarm nicht auf dem Computer mit Visual Studio befindet, konfigurieren Sie das Projekt zum Debuggen mit dem Microsoft Azure Service Bus. Weitere Informationen finden Sie unter  [Debugging und Problembehandlung eines Remoteereignisempfängers in einem Add-In für SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). 
    
  
5. Wenn die Datei **AppEventReceiver.svc** eine `ProcessOneWayEvent`-Methode enthält, sollte ihre Implementierung lediglich die Zeile  `throw new NotImplementedException();` umfassen, da diese Methode nicht in einem Add-In-Ereignishandler verwendet werden kann. *Add-In-Ereignishandler müssen ein Objekt zurückgeben, das SharePoint mitteilt, ob das Ereignis abgeschlossen oder zurückgesetzt werden soll. Die  `ProcessOneWayEvent`-Methode gibt jedoch nichts zurück.* 
    
  
6. Die Datei enthält eine  `ProcessEvent`-Methode, die etwa wie folgt aussieht. (Sie kann auch einen Codeblock enthalten, der veranschaulicht, wie ein Clientkontext abgerufen wird. Löschen Sie diesen, oder kommentieren Sie ihn aus.) 
    
    Beachten Sie die folgenden Aspekte in diesem Code:
    
  - Das  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) -Objekt wird an den Handlerwebdienst als SOAP-Nachricht gesendet, die Kontextinformationen von SharePoint enthält, beispielsweise eine [EventType](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.EventType.aspx) -Eigenschaft zur Identifizierung des Ereignisses.
    
  
  - Das vom Handler zurückgegebene  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) -Objekt enthält eine [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) -Eigenschaft mit den möglichen Werten [SPRemoteEventServiceStatus](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventServiceStatus.aspx) . **Continue**, **SPRemoteEventServiceStatus.CancelNoError** und **SPRemoteEventServiceStatus.CancelWithError**. Der Standardwert der **Status**-Eigenschaft ist **Continue** und weist SharePoint an, das Ereignis abzuschließen. Die anderen beiden Werte weisen SharePoint an, Folgendes zu tun:
    
  - Führen Sie den Handler maximal noch drei Mal aus.
    
  
  - Wenn weiterhin ein "Cancel"-Status zurückgegeben wird, wird das Ereignis abgebrochen, und alles, was als Teil des Ereignisses ausgeführt wurde, wird zurückgesetzt. 
    
  



  ```cs
  
public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();

    return result;
}
  ```

7. Fügen Sie direkt unter der Zeile mit der Deklaration der  `result`-Variable die folgende Verzweigungsstruktur hinzu, um das behandelte Ereignis zu identifizieren. 
    
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


    > **HINWEIS**
      > Falls Sie für die Ereignisse **AppInstalled**, **AppUpdated** und **AppInstalling** über Handler verfügen, wird für jedes eine eigene URL im Add-In-Manifest registriert. So *können*  Sie unterschiedliche Endpunkte dafür verwenden. Aber in diesem Artikel (und den Office-Entwicklertools für Visual Studio) wird angenommen, dass sie genau denselben Endpunkt haben. Deshalb muss der Code bestimmen, durch welches Ereignis er aufgerufen wurde.
8. Wie unter  [Einbeziehen von Rollbacklogik und "Bereits erledigt"-Logik in Add-In-Ereignishandler](handle-events-in-sharepoint-add-ins.md#Rollback) erläutert wurde, möchten Sie bei einem Fehler in der Installationslogik fast immer, dass die Installation des Add-Ins abgebrochen wird und alle Installationsaktionen von SharePoint zurückgesetzt werden. Außerdem sollen die Aktionen Ihres Handlers zurückgesetzt werden. Eine Möglichkeit dazu besteht darin, folgenden Code in der **case**-Struktur für das AppInstalled-Ereignis hinzuzufügen.
    
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


    > **HINWEIS**
      > Verschieben Sie Installationscode, der mehr als 30 Sekunden dauert, in das Add-In selbst. Sie können ihn zu "Erster Start"-Logik hinzufügen, die beim ersten Ausführen des Add-Ins ausgeführt wird. Das Add-In könnte eine Meldung im Sinne von "Das Add-In wird nun für Sie vorbereitet" anzeigen. Alternativ kann das Add-In den Benutzer zum Ausführen des Initialisierungscodes auffordern. > Ist "Erster Start"-Logik für Ihr Add-In nicht machbar, können Sie den Ereignishandler auch einen asynchronen Remoteprozess starten lassen und dann sofort ein  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) -Objekt mit dem **Status**-Wert **Continue** zurückgeben. Ein Schwachpunkt dieser Strategie ist, dass bei einem Fehler des Remoteprozesses keine Möglichkeit besteht, SharePoint zum Zurücksetzen der Add-In-Installation aufzufordern.
9. Wie unter  [Strategien für die Architektur von Add-In-Ereignishandlern](handle-events-in-sharepoint-add-ins.md#Strategies) erläutert, wird die Handlerdelegierungsstrategie bevorzugt, auch wenn sie nicht in jedem Szenario möglich ist. Im fortlaufenden Beispiel zeigen wir Ihnen, wie Sie die Handlerdelegierungsstrategie implementieren, wenn Sie eine Liste zum Hostweb hinzufügen. (Informationen zum Erstellen eines ähnlichen AppInstalled-Ereignishandlers, der nicht die Handlerdelegierungsstrategie verwendet, finden Sie im Beispiel [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents).)
    
    Im Folgenden finden Sie die neue Version des **case**-Blocks für AppInstalled. Beachten Sie, dass Initialisierungslogik, die für alle Ereignisse gilt, über dem **switch**-Block steht. Da die gleiche Liste, die installiert wird, im AppUninstalling-Ereignishandler entfernt wird, wird die Liste hier identifiziert.
    


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

10. Fügen Sie die Listenerstellungsmethode zur **AppEventReceiver**-Klasse als **private**-Methode mit folgendem Code hinzu. Beachten Sie, dass die  `TokenHelper`-Klasse eine besondere Methode aufweist, die für das Abrufen eines Clientkontexts für ein Add-In-Ereignis optimiert ist. Durch die Übergabe von **false** für den letzten Parameter wird sichergestellt, dass der Kontext für das Hostweb gilt.
    
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

11. Rollbacklogik besteht im Wesentlichen aus Ausnahmebehandlungslogik, und das SharePoint-CSOM (clientseitige Objektmodell) hat einen  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) , der es Ihrem Webdienst ermöglicht, die Ausnahmebehandlung an den SharePoint-Server zu delegieren. (Siehe auch [Gewusst wie: Verwenden des Ausnahmebehandlungsbereichs](http://msdn.microsoft.com/library/103619ef-1ba3-44e3-93e1-5e0685bc616e%28Office.15%29.aspx).) Fügen Sie den folgenden Code im **if**-Block des vorhergehenden Codeausschnitts hinzu.
    
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

12. Es gibt nur einen Aufruf von SharePoint ( **ExecuteQuery**) im vorherigen Codeausschnitt, aber leider bleibt es nicht bei dem einen. Jedes Objekt, auf das in unserem Ausnahmebereich verwiesen wird, muss zuerst in den Client geladen werden. Fügen Sie also den folgenden Code  *über*  dem Konstruktor für den **ExceptionHandlingScope** hinzu.
    
  ```cs
  
ListCollection allLists = clientContext.Web.Lists;
IEnumerable<List> matchingLists =
    clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
clientContext.ExecuteQuery();

var foundList = matchingLists.FirstOrDefault();
 List createdList = null;
  ```

13. Der Code zum Erstellen einer Hostwebliste gehört in den  [StartTry](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartTry.aspx) -Block, aber zuerst muss überprüft werden, ob die Liste bereits hinzugefügt wurde (wie unter [Einbeziehen von Rollbacklogik und "Bereits erledigt"-Logik in Add-In-Ereignishandler](handle-events-in-sharepoint-add-ins.md#Rollback) erläutert). If-then-else-Logik kann über die [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) -Klasse an den SharePoint-Server delegiert werden. (Siehe auch [Gewusst wie: Verwenden des bedingten Bereichs](http://msdn.microsoft.com/library/560112e9-c3ed-4b8f-9cd4-c8bc5d60d63c%28Office.15%29.aspx).) Fügen Sie den folgenden Code im **StartTry**-Block hinzu.
    
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

14. Der  [StartCatch](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartCatch.aspx) -Block sollte die Erstellung der Liste rückgängig machen, aber zuvor muss überprüft werden, ob die Liste erstellt wurde. Denn möglicherweise wurde eine Ausnahme im **StartTry**-Block ausgelöst, bevor die Erstellung der Liste abgeschlossen war. Fügen Sie den folgenden Code im **StartCatch**-Block hinzu.
    
  ```cs
  
ConditionalScope condScope = new ConditionalScope(clientContext,
        () => createdList.ServerObjectIsNull.Value != true, true);
using (condScope.StartScope())
{
    createdList.DeleteObject();
} 
  ```


    > **TIPP**
      > **PROBLEMBEHANDLUNG:** Zum Testen, ob der **StartCatch**-Block wie beabsichtigt betreten wird, brauchen Sie eine Möglichkeit, eine Laufzeitausnahme auf dem SharePoint-Server auszulösen. Die Verwendung von **throw** oder eine Division durch Null funktioniert nicht, da diese *clientseitige*  Ausnahmen auslösen, bevor die Clientlaufzeit den Code bündeln und an den Server senden kann (mit der **ExecuteQuery**-Methode). Fügen Sie stattdessen dem **StartTry**-Block die folgenden Zeilen hinzu. Die clientseitige Laufzeit akzeptiert dies, veranlasst jedoch wie gewünscht eine serverseitige Ausnahme. >  `List fakeList = clientContext.Web.Lists.GetByTitle("NoSuchList");`
  
    
    
 `clientContext.Load(fakeList);`

    Die gesamte TryCreateList-Methode sollte wie folgt aussehen. (Der  [StartFinally](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartFinally.aspx) -Block ist auch erforderlich, wenn er nicht verwendet wird.)
    


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


    > **TIPP**
      > **DEBUGGEN:** Unabhängig davon, ob Sie die Handlerdelegierungsstrategie verwenden, sollten Sie beim Durchlaufen des Codes mit dem Debugger bedenken, dass in jedem Szenario, in dem der Handler einen "Cancel"-Status zurückgibt, SharePoint den Handler erneut aufrufen wird, und das bis zu drei Mal mehr. Somit durchläuft der Debugger den Code bis zu vier Mal.

    > **TIPP**
      > **CODEARCHITEKTUR:** Da Sie Komponenten im Add-In-Web mit deklarativem Markup außerhalb des Handlers installieren können, möchten Sie in der Regel keine der 30 Sekunden, die der Handler zur Verfügung hat, für die Interaktion mit dem Add-In-Web verbrauchen. Wenn Sie es dennoch tun, denken Sie daran, dass Ihr Code ein separates [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) -Objekt für das Add-In-Web benötigt. Dies bedeutet, dass Add-In-Web und Hostweb verschiedene Komponenten sind, ebenso wie sie sich von einer SQL Server-Datenbank unterscheiden. Daher befindet sich eine Methode, die das Add-In-Web aufruft, im **try**-Block des **case**-Blocks für AppInstalled, genau so wie die TryCreateList-Methode im fortlaufenden Beispiel. Der Handler muss jedoch Aktionen im Add-In-Web  *nicht*  zurücksetzen. Wenn ein Fehler auftritt, muss er nur das Ereignis abbrechen, da SharePoint das gesamte Add-In-Web löscht, wenn das Ereignis abgebrochen wird.

## Erstellen eines Ereignisempfängers für das deinstallierende Add-In
<a name="SP15appevent_prereq"> </a>


1. Legen Sie die Eigenschaft **Deinstallierendes Add-In behandeln** des Projekts auf **True** fest. Von den Tools wird *keine*  weitere Webdienstdatei erstellt, falls eine vorhanden ist. Aber sie fügen dem Add-In-Manifest ein **UninstallingEventEndpoint**-Element hinzu. 
    
  
2. Code im **case**-Block für AppUninstalling sollte Artefakte des Add-Ins entfernen, die nicht mehr benötigt werden, nachdem das Add-In aus dem endgültigen Papierkorb entfernt wurde, wodurch das Ereignis ausgelöst wird. Allerdings müssen Sie die Komponenten möglichst "außer Kraft setzen", anstatt sie vollständig zu löschen. Der Grund ist, dass Sie sie wiederherstellen müssen, wenn das Deinstallationsereignis rückgängig gemacht werden muss. In diesem Fall befindet sich das Add-In noch im endgültigen Papierkorb, und ein Benutzer kann sie wiederherstellen und erneut verwenden. Das Neuerstellen einer gelöschten Komponente in der Rollbacklogik ist möglicherweise ausreichend, um das Add-In wieder funktionsfähig zu machen, aber Daten oder Konfigurationseinstellungen in der Komponente würden dann verloren gehen.
    
    Diese Strategie ist für SharePoint-Komponenten relativ einfach, da SharePoint über einen Papierkorb verfügt, aus dem Elemente wiederhergestellt werden können, und es CSOM-APIs für den Zugriff gibt. Spätere Schritte in diesem Verfahren zeigen, wie das geht. Für andere Plattformen können unterschiedliche Techniken erforderlich sein. Wenn Sie beispielsweise eine Zeile in einer SQL Server-Tabelle in Ihrem Add-In-Deinstallationshandler außer Kraft setzen möchten, kann eine gespeicherte T-SQL-Prozedur im Ereignishandler eine IsDeleted-Spalte zur Tabelle hinzufügen und für die Zeile auf **True** festlegen. Wenn in der Prozedur ein Fehler auftritt, setzt die Rollbacklogik den Wert auf **False** zurück. Wenn die Prozedur ohne Fehler abgeschlossen wird, kann sie kurz vor der Rückgabe eines Erfolgsflags einen Zeitgeberauftrag für die spätere Löschung der Zeile festlegen.
    
    Manchmal möchten Sie Daten, wie z. B. Listen, auch nach dem Löschen des Add-Ins behalten, aber als Beispiel in diesem Artikel wird im folgenden Deinstallationsereignishandler die Liste gelöscht, die mit dem Installationsereignishandler erstellt wurde.
    


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

3. Fügen Sie die Hilfsmethode für die Wiederverwendung der Liste hinzu. Beachten Sie Folgendes zu diesem Code:
    
  - Der Code verschiebt die Liste in den Papierkorb, anstatt sie dauerhaft zu löschen. Dies ermöglicht das Wiederherstellen der Liste und der zugehörigen Daten nach einem Fehler im Ereignis, was im **StartCatch**-Block geschieht. Wenn die Methode erfolgreich ist und das Ereignis abgeschlossen ist, wird das Add-In dauerhaft aus dem endgültigen Papierkorb gelöscht, aber die Liste befindet sich noch im Papierkorb. 
    
  
  - Der Code prüft das Vorhandensein der Liste, bevor sie wieder verwendet wird, da ein Benutzer sie bereits auf der SharePoint-Benutzeroberfläche wieder verwendet haben könnte. Auf ähnliche Weise überprüft der Rollbackcode das Vorhandensein der Liste im Papierkorb, bevor sie wiederhergestellt wird, da ein Benutzer sie möglicherweise bereits wiederhergestellt haben oder in den endgültigen Papierkorb verschoben haben könnte. 
    
  
  - Es gibt zwei bedingte Bereiche, die das Vorhandensein einer Liste testen, indem sie überprüfen, ob ein Verweis darauf **null** ist. Aber beide verfügen über einen inneren **if**-Block, der dasselbe Objekt ein zweites Mal auf null überprüft. Die äußeren Tests mit Blocks mit bedingten Bereichen werden auf dem Server ausgeführt, aber die inneren Tests auf null sind ebenfalls erforderlich. Denn die Client-Laufzeit durchläuft den Code Zeile für Zeile zum Erstellen der XML-Meldung, die die **ExecuteQuery**-Methode an den Server sendet. Wenn die Verweise auf die Objekte **foundList** und **recycledList** erreicht werden, löst die eine oder andere dieser Zeilen eine Nullverweisausnahme aus, sofern sie nicht innerhalb der inneren null-Überprüfungen eingeschlossen sind.
    
  

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


### So debuggen und testen Sie einen Ereignisempfänger für deinstallierende Add-Ins


1. Öffnen Sie alle folgenden Seiten in getrennten Fenstern oder Registerkarten:
    
  - **Websiteinhalte**
    
  
  - **Websiteeinstellungen - Papierkorb** (_layouts/15/AdminRecycleBin.aspx?ql=1)
    
  
  - **Papierkorb - Endgültiger Papierkorb** (_layouts/15/AdminRecycleBin.aspxView=2&amp;?ql=1)
    
  
2. Drücken Sie F5, und vertrauen Sie dem Add-In, wenn Sie dazu aufgefordert werden. Die Startseite des Add-Ins wird geöffnet. Wenn Sie nur den Deinstallationsereignishandler testen möchten, können Sie dieses Browserfenster schließen.  *Wenn Sie den Handler jedoch debuggen, lassen Sie es geöffnet. Beim Schließen wird die Debuggingsitzung beendet.* 
    
  
3. Aktualisieren Sie die Seite **Websiteinhalte**, und wenn das Add-In angezeigt wird, entfernen Sie sie.
    
  
4. Aktualisieren Sie die Seite **Websiteeinstellungen - Papierkorb**. Das Add-In wird als das erste Element angezeigt. Aktivieren Sie das Kontrollkästchen daneben, und klicken Sie auf **Auswahl löschen**.
    
  
5. Aktualisieren Sie die Seite **Papierkorb - Endgültiger Papierkorb**. Das Add-In wird als das erste Element angezeigt. Aktivieren Sie das Kontrollkästchen daneben, und klicken Sie auf **Auswahl löschen**. SharePoint wird sofort Ihren Ereignishandler für deinstallierende Add-Ins aufrufen.
    
  

## Erstellen eines Ereignisempfängers für das aktualisierte Add-In
<a name="SP15appevent_prereq"> </a>

Einzelheiten zum Erstellen eines Handlers für das aktualisierte Add-In finden Sie unter  [Erstellen eines Handlers für das Updateereignis in SharePoint-Add-Ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).
  
    
    

## URL- und Hostingeinschränkungen für Add-In-Ereignisempfänger in Produktion
<a name="SP15appevent_prereq"> </a>

Der Add-In-Ereignisempfänger kann in der Cloud oder auf einem lokalen Server gehostet werden, der nich gleichzeitig als SharePoint-Server verwendet wird. Die URL eines Produktionsempfängers kann keinen bestimmten Port angeben. Daher müssen Sie entweder Port 443 für HTTPS verwenden, was empfohlen wird, oder Port 80 für HTTP. Wenn Sie HTTPS verwenden und der Empfängerdienst lokal gehostet wird, sich das Add-In jedoch auf Microsoft SharePoint Online befindet, muss der Hostingserver über ein öffentlich vertrauenswürdiges Zertifikat einer Zertifikatstelle verfügen. (Ein selbstsigniertes Zertifikat kann nur verwendet werden, wens sich das Add-In in einer lokalen SharePoint-Farm befindet.)
  
    
    

## Zusätzliche Ressourcen
<a name="SP15appevent_addlresources"> </a>


-  [Behandeln von Ereignissen in SharePoint-Add-Ins](handle-events-in-sharepoint-add-ins.md)
    
  

