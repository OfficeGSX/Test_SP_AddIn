---
title: Behandeln von Add-In-Ereignissen im vom Anbieter gehosteten Add-In
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# Behandeln von Add-In-Ereignissen im vom Anbieter gehosteten Add-In
Erfahren Sie, wie Sie die Installation eines vom Anbieter gehosteten SharePoint-Add-Ins anpassen.
Dies ist der siebte einer Reihe von Artikeln über die Grundlagen der Entwicklung von vom Anbieter gehosteten SharePoint-Add-Ins. Machen Sie sich zunächst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den vorherigen Artikeln dieser Reihe vertraut:





-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Erteilen des Aussehens und Verhaltens von SharePoint für Ihr vom Anbieter gehostetes Add-In](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [Einfügen einer benutzerdefinierten Schaltfläche in das vom Anbieter gehostete Add-In](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [Schnelle Übersicht über das SharePoint-Objektmodell](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [Hinzufügen von SharePoint-Schreibvorgängen zum vom Anbieter gehosteten Add-In](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [Einfügen eines Add-In-Webparts in das vom Anbieter gehostete Add-In](include-an-add-in-part-in-the-provider-hosted-add-in.md)



> **HINWEIS**
> Wenn Sie diese Reihe zu vom Anbieter gehosteten Add-Ins durchgearbeitet haben, haben Sie eine Visual Studio-Projektmappe, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) herunterladen und die Datei „BeforeAdd-inEventHandlers.sln" öffnen.




In diesem Artikel passen wir die Verarbeitung einer Art von Ereignis in SharePoint an, die als Add-In-Ereignisse bezeichnet wird. Insbesondere erstellen wir Ereignishandler für die Add-In-Installation und -Deinstallation. Es gibt außerdem Listen- und Listenelementereignisse, die eine benutzerdefinierte Verarbeitung abrufen können. Sie erfahren mehr über diese in einem späteren Artikel dieser Reihe. Alle diese Ereignisse werden in SharePoint ausgelöst, aber Ihr benutzerdefinierter Code, der jedes Ereignis verarbeitet, befindet sich in Ihrer Remotewebanwendung. Sie konfigurieren SharePoint zum Aufrufen Ihres benutzerdefinierten Handlers, indem Sie die URL für den Handler bei dem SharePoint-Ereignis registrieren.
## Zwei Orte für die programmgesteuerte Bereitstellung von SharePoint-Komponenten

Unser ChainStore-Add-In soll die Listen **Lokale Mitarbeiter** und **Erwartete Lieferungen** automatisch erstellen und bereitstellen. Ein Add-In kann SharePoint-Komponenten wie eine benutzerdefinierte Liste jederzeit bereitstellen. Aber wenn ein Add-In von einer bestimmten Komponente wie einer benutzerdefinierten Liste abhängt, sollte die Komponente bereitgestellt werden, *bevor*  Benutzer beginnen, mit dem Add-In zu arbeiten. Für solche wichtigen Komponenten gibt es zwei Orte, an denen sich die benutzerdefinierte Bereitstellungslogik befinden kann:




- In einem Handler für das Add-In-Installationsereignis


- In einer Logik für die erste Ausführung, die ausgeführt wird, wenn das Add-In zum ersten Mal in SharePoint gestartet wird


Die Entscheidung, welcher für ein bestimmtes Add-In am besten geeignet ist, ist ein weiterführendes Thema. In diesem Artikel können nur einige Vergleichspunkte erwähnt werden:




- Ein benutzerdefinierter Installationshandler muss in 30 Sekunden abgeschlossen sein. Es gibt keine Beschränkung für die Dauer der Ausführung der Logik für die erste Ausführung.


- Wenn es während der Add-In-Installation zu Zwischenfällen kommt, führt SharePoint ein Rollback aller Aktionen durch, die als Teil der Installation durchgeführt wurden. Ein benutzerdefinierter Installationshandler wird ausgeführt,  *nachdem*  SharePoint alles ausgeführt hat, was für die Installation des Add-Ins erforderlich ist, deshalb kann ein benutzerdefinierter Handler an diesem System teilnehmen. Wenn Ihre benutzerdefinierte Logik eine Ausnahme auslöst, können Sie z. B. SharePoint anweisen, ein Rollback der gesamten Add-In-Installation durchzuführen. Wenn ein Fehler in der benutzerdefinierten Logik für die erste Ausführung auftritt, bleibt das Add-In installiert und funktioniert vermutlich nicht ordnungsgemäß.


- SharePoint gibt nicht auf, wenn ein Rollback einer Add-In-Installation durchgeführt werden muss. Die Installation wird sofort erneut versucht. SharePoint führt bis zu 4 Versuche durch (wobei für jeden Versuch die 30-Sekunden-Zeitbegrenzung gilt). Bei jedem erneuten Versuch wird der benutzerdefinierte Installationshandler wieder ausgeführt,  *von Anfang an*  . Wenn der Handler es z. B. geschafft hat, vor dem Rollback eine Liste zu installieren, versucht er, beim erneuten dieselbe Liste erneut zu installieren. Um dies zu verhindern, muss der Code in einen Handler für die Installation so geschrieben werden, dass erst eine Aktionen (z. B. das Bereitstellen einer Komponente) durchgeführt wird, wenn überprüft wurde, ob diese Aktion bereits durchgeführt wurde. Dadurch wird die Logik eines Handlers für die Installation komplexer als die Logik für erste Ausführung, da letztere keinen erneuten Versuch durchführt (es sei denn, Sie codieren sie explizit dafür). Das Überprüfen, ob eine Komponente bereits bereitgestellt wurde, erfordert außerdem in der Regel einen zeitaufwändigen Aufruf über das Internet vom Remotehandler an SharePoint. Und dann ist ein zweiter Aufruf erforderlich, um die Komponente tatsächlich bereitzustellen (falls sich herausstellt, dass sie noch bereits bereitgestellt wurde).


Für das ChainStore-Add-In werden diese Strategien kombiniert. In diesem Artikel erstellen Sie einen Installationshandler, der das Hostweb als Mandanten in der Unternehmensdatenbank registiert, und legen dann ein Signal fest, das angibt, ob das Add-In schon im Hostweb ausgeführt wurde. In einem späteren Artikel dieser Reihe fügen Sie Logik für die erste Ausführung zur Methode **Page_Load** der Add-In-Startseite hinzu. Diese Logik stellt die zwei benutzerdefinierten Listen bereit und führt einige andere Aktionen aus.




## Konfigurieren der Projektmappe für das Debuggen des Ereignisempfängers
<a name="RERDebug"> </a>

Für das Debuggen von Ereignisempfängern muss der Azure-Dienstbus verwendet werden. Befolgen Sie die Anweisungen unter  [Debugging und Problembehandlung eines Remoteereignisempfängers in einem Add-In für SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). Da Sie eine SharePoint Online-Website als Testwebsite verwenden, sollten Sie die Anweisungen für einen Remotewebsitetest befolgen. Im restlichen Teil dieser Reihe wird davon ausgegangen, dass Sie das Debuggen erfolgreich konfiguriert haben. 




## Erstellen des Installationshandlers
<a name="RERDebug"> </a>






> **HINWEIS**
>  Die Einstellungen für Startprojekte in Visual Studio werden normalerweise auf die Standardwerte zurückgesetzt, wann immer die Projektmappe erneut geöffnet wird. Führen Sie die folgenden Schritte immer unmittelbar nach dem erneuten Öffnen der Beispielprojektmappe in dieser Artikelreihe durch:>  Klicken Sie mit der rechten Maustaste oben im **Projektmappen-Explorer** auf den Projektmappenknoten, und wählen Sie **Startprojekte festlegen** aus.>  Stellen Sie sicher, dass alle drei Projekte in der Spalte **Aktion** auf **Start** festgelegt sind.





1. Wählen Sie im **Projektmappen-Explorer** das **ChainStore**-Projekt aus, damit seine Eigenschaften im Bereich **Eigenschaften** von Visual Studio angezeigt werden.


2. Legen Sie den Wert von **Installiertes Add-In behandeln** auf **True** fest. (Möglicherweise heißt die Option immer noch **Installierte App behandeln**.) Damit werden zwei Aktionen ausgeführt:

  - Einen Ordner namens **Dienste** wird im **ChainStoreWeb**-Projekt (nicht im **ChainStore**-Projekt) erstellt, und ihm werden zwei Dateien hinzugefügt: eine AppEventReceiver.svc-Datei und ihre CodeBehind-Datei AppEventReceiver.svc.cs. (Die Dateinamen beginnen mit der Zeichenfolge „App", weil Add-Ins früher als Apps bezeichnet wurden.  *Benennen Sie diese Dateien nicht um.*  Die Office-Entwicklertools für Visual Studio gehen davon aus, dass die Dateien diese Namen behalten. )


  - Die Handler-URL ist im Add-In-Manifest registriert. Dieser Teil des Manifests ist nicht im Manifest-Designer sichtbar. Klicken Sie zum Anzeigen mit der rechten Maustaste in die Datei AppManifest.xml, und wählen Sie **Code anzeigen** aus. Es gibt ein neues untergeordnetes Element des Elements **Eigenschaften**, das wie folgt aussieht. Dieses Markup weist SharePoint an, die Methode **ProcessEvent** dieses Dienstes aufzurufen, nachdem alle eigenen Arbeiten im Zusammenhang mit der Installation des Add-Ins abgeschlossen wurden. Der benutzerdefinierte Handler wird zuletzt als Teil der Installation ausgeführt. Die Zeichenfolge `~remoteAppUrl` ist ein Platzhalter, dem die Office-Entwicklertools für Visual Studio durch die Diensthost-URL ersetzen. Beim Debuggen ist das eine Azure-Dienstbus-URL. Wenn Sie das Paket für die Bereitstellung an die Produktion erstellen, ist es die Produktions-URL.

  ```XML

<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
  ```

3. Öffnen Sie die Datei AppEventReceiver.svc.cs.


4. Sehen Sie, dass die Office-Entwicklertools für Visual Studio eine Beispielimplementierung der Methode **ProcessEvent** erstellt haben. Alle Implementierungen dieser Methode beginnen mit der Initialisierung eines **SPRemoteEventResult**-Objekts und enden durch das Zurückgeben dieses Objekts an SharePoint. Unter anderem weist dieses Objekt SharePoint an, ob das Ereignis zurückgesetzt werden soll, weil ein Fehler bei der benutzerdefinierten Verarbeitungslogik aufgetreten ist. Möglicherweise haben die Tools auch einen **using**-Block in diese Methode eingefügt, der ein **ClientContext**-Objekt erstellt. Der benutzerdefinierte Handler im ChainStore-Add-In führt keinen Rückruf an SharePoint durch und kann deshalb gelöscht werden. Die Methode sollte jetzt wie folgt aussehen.

  ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
  ```

5. Der Ereignisempfänger kann von jedem der drei möglichen Add-In-Ereignisse aufgerufen werden. Fügen Sie deshalb die folgende **switch**-Struktur zur Methode **ProcessEvent** zwischen den Zeilen hinzu, die das Objekt `result` erstellen und zurückgeben. Die Ereignisnamen enthalten die Zeichenfolge „App", da Add-Ins früher als Apps bezeichnet wurden.

  ```cs

switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.     
     
        break;
}
  ```

6. Unsere Installationslogik ruft eine in SQL gespeicherte Prozedur auf, um das Geschäft in Hongkong als Mandant in der Remotewebanwendung zu registrieren. Falls bei diesem Prozess ein Fehler auftritt, ist es sehr wichtig, dass der Handler SharePoint signalisiert, die Installation des Add-Ins zurückzusetzen. Fügen Sie deshalb die folgenden **try/catch**-Blöcke anstelle von  `TODO2` hinzu. Beachten Sie Folgendes bei diesem Code:

  - Sie erstellen das  `tenantName`-Objekt und die Methode  `CreateTenant` in einem späteren Schritt.


  - Die Eigenschaft **Status** des Objekts **SPRemoteEventResult** kann drei Werte aufweisen: **Continue** (Standard), **CancelNoError** und **CancelWithError**. Die beiden Letzteren weisen SharePoint an, ein Rollback des Ereignisses durchzuführen.



  ```cs

try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

7. Die Hostweb-URL, die im Beispiel der Mandantendiskriminator ist, ist Teil der Informationen, die SharePoint im Parameter **SPRemoteEventProperties** an den Empfänger übergibt. Fügen Sie die folgende Zeile zur Methode **ProcessEvent** in der Zeile hinzu, die sich unterhalb der Initialisierung des Objekts **SPRemoteEventResult** befindet.

  ```cs

string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
  ```

8. Jetzt muss unser Code mit einer kleinen Tücke der Eigenschaft **AppEventProperties.HostWebFullUrl** umgehen. In den meisten anderen Kontexten enthält SharePoint ein schließendes /-Zeichen am Ende der Hostweb-URL, sodass die Logik im Beispielcode annimmt, dass diese Zeichen vorhanden ist. Aber SharePoint fügt dieses Zeichen am Ende des Werts **HostWebFullUrl** hinzu, wenn, und nur wenn, das Hostweb die Stammwebsite einer Websitesammlung ist. Da unsere Hongkong-Website eine Unterwebsite in der Websitesammlung ist, müssen wir dieses Zeichen hinzufügen, um sicherzustellen, dass im gesamten Beispiel dieselbe Zeichenfolge für den Mandantennamen verwendet wird. Fügen Sie folgenden Code unterhalb der Initialisierung des Objekts `tenantName` hinzu.

  ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
  ```

9. Fügen Sie die folgenden **using**-Anweisungen an den Anfang der Datei hinzu.

  ```

using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
  ```

10. Fügen Sie die folgende Methode zur Klasse  `AppEventReceiver` hinzu. Dies wird nicht ausführlich behandelt, da der Zweck dieser Artikelreihe darin besteht, Kenntnisse zur SharePoint-Add-In-Programmierung zu vermitteln, nicht zur SQL Server-/Azure-Programmierung.

    Diese Methode erstellt eine Zeile in einer Datenbanktabelle namens **Mandanten**. Zusätzlich zur Spalte **Name** enthält die Tabelle auch die Spalte **Version** mit dem Standardwert0000.0000.0000.0000. In einem späteren Artikel dieser Reihe erstellen Sie die Logik für die erste Ausführung, die sich diesen Wert ansieht, um zu bestimmen, ob das Add-In bereits im Hostweb installiert wurde. Wenn die Version 0000.0000.0000.0000 ist, stellt Ihr Code die Listen **Lokale Mitarbeiter** und **Erwartete Lieferungen** bereit, und erhöht dann die Versionsnummer.



  ```cs

private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
  ```


## Erstellen des Handlers für die Deinstallation
<a name="RERDebug"> </a>

Normalerweise empfiehlt es sich, das Deinstallationsereignis zu verarbeiten, wann immer Sie das Installationsereignis verarbeiten. Die grundlegende Idee ist, dass der Deinstallationshandler Dinge löscht oder recycelt, die der Installationshandler bereitgestellt wird. Es gibt jedoch viele Ausnahmen, und deshalb sollten Sie die Anwendungsfälle Ihres Add-Ins wirklich verstehen. Beispielsweise hat eine Liste, die mit einem Add-In bereitgestellt und mit dem Add-In ausgefüllt wird, möglicherweise immer einen Wert, selbst nachdem das Add-In selbst deinstalliert wurde, was bedeuten würde, dass Sie die Liste nicht im Deinstallationshandler deinstallieren möchten. 



Das Deinstallationsereignis wird nicht wie zu erwarten ausgeführt, wenn ein Benutzer das Add-In von der Seite **Websiteinhalte** entfernt. Dadurch wird das Add-In nur in den Papierkorb der Website verschoben. Ein Benutzer kann es wiederherstellen, aber durch das Wiederherstellen wird der Ereignishandler für die Installation nicht erneut ausgeführt, und Sie möchten, dass alles, was mit dem Installationsereignishandler bereitgestellt wurde, weiterhin vorhanden ist, wenn das Add-In wiederhergestellt wird. SharePoint-Komponenten können aus dem Papierkorb an den endgültigen Papierkorb verschoben werden. Das Deinstallationsereignis wird nur durchgeführt, wenn ein Add-In aus dem endgültigen Papierkorb gelöscht wird. Und wenn ein Benutzer das ausführt, ist das Add-In sowieso nicht wiederherstellbar. Wir möchten also den Mandaten des Geschäfts in Hongkong an diesem Punkt aus der Unternehmensdatenbank entfernen.




1. Legen Sie den Wert von **Deinstallierendes Add-In behandeln** auf **True** fest. (Möglicherweise heißt die Option immer noch **Deinstallierende App behandeln**.) Dadurch wird der Handler ebenso in der Datei AppManifest.xml registriert wie Sie der Installationshandler. Wenn Sie die Datei betrachten, sehen Sie, dass die URL exakt identisch ist. Die Office-Entwicklertools für Visual Studio gehen davon aus, dass Sie dieselbe *.svc-Datei verwenden, so wie wir in diesem Beispiel. Das ist eine Standardmethode. 


2. Fügen Sie den folgenden Code anstelle von  `TODO3` in die AppEventReceiver.svc.cs-Datei ein. Beachten Sie Folgendes zu diesem Code:

  - Die Methode  `DeleteTenant` wird im nächsten Schritt hinzugefügt.


  - Ein Rollback der Deinstallation des Add-Ins würde das Add-In in den endgültigen Papierkorb übermitteln, aus dem es immer noch wiederhergestellt werden kann.



  ```cs

try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

3. Fügen Sie die folgende Methode zur Klasse  `AppEventReceiver` hinzu.

  ```cs

private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();            
    }//dispose conn and cmd
}
  ```


> **HINWEIS**
> In einem früheren Artikel dieser Reihe haben Sie das Projekt so konfiguriert, dass die Datenbank des Unternehmens jedes Mal neu erstellt wird, wenn Sie F5 drücken. Damit wird die Tabelle **Mandanten** geleert.





## Ausführen des Add-ins und Testen des Installationshandlers
<a name="RERDebug"> </a>


1. Verwenden Sie die F5-Taste, um das Bereitstellen und Ausführen von Add-Ins. Visual Studio hostet die Remotewebanwendung in IIS Express und SQL-Datenbank in eine SQL Express hostet. Es stellt auch eine temporäre Installation des Add-Ins auf Ihrer SharePoint-Website, wird der Ereignishandler für die Installation ausgeführt und sofort das Add-In ausgeführt wird. Sie werden aufgefordert, zum Erteilen von Berechtigungen für das Add-in, bevor sie beginnen geöffnet wird. 


2. Wenn das Add-in starten geöffnet wird, drücken Sie auf das Zahnradsymbol auf das Chrome-Steuerelement am Anfang, und wählen **Kontoeinstellungen**.


3. Klicken Sie auf der Seite **Konten** auf die Schaltfläche **Add-In-Version anzeigen**. Die Version wird als 0000.0000.0000.0000 angezeigt.

!\[The Accounts page with the heading "Account settings". A button named "Show Add-in Version" and below this a line of text reading "Registered version: zero zero zero zero dot zero zero zero zero dot zero zero zero zero dot zero zero zero zero".](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)





4. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.


5. Da Sie mit diesem Add-In und dieser Visual Studio-Projektmappe in anderen Artikeln arbeiten werden, hat es sich bewährt, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.



## 
<a name="Nextsteps"> </a>

 Im nächsten Artikel der Reihe fügen Sie Logik für die erste Ausführung zum Add-In hinzu, die programmgesteuert die Liste **Lokale Mitarbeiter** und die benutzerdefinierten Menübandschaltfläche bereitstellt: [Hinzufügen der Logik für die erste Ausführung zum vom Anbieter gehosteten Add-In](add-first-run-logic-to-the-provider-hosted-add-in.md)




