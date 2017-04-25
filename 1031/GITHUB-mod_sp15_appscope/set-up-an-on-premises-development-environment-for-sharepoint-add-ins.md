---
title: Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: b0878c12-27c9-4eea-ae3b-7e79e5a8838d
---


# Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins
Hier erhalten Sie Informationen über das Einrichten einer Entwicklungsumgebung, die speziell für die Entwicklung von SharePoint-Add-Ins mit einer lokalen Installation von SharePoint geeignet ist.
## Installieren des Betriebssystems für Ihre Entwicklungsumgebung für SharePoint-Add-Ins
<a name="bk_installOS"> </a>

Die Anforderungen an eine Entwicklungsumgebung sind weniger streng und kostenintensiv als die Anforderungen an eine Produktionsumgebung. Mit den hier beschriebenen Richtlinien ist die Installation einer Produktionsumgebung nicht möglich. Unter  [Übersicht über die SharePoint 2013-Installation und -Konfiguration](http://technet.microsoft.com/de-de/library/ee667264%28office.15%29.aspx),  [Hardware- und Softwareanforderungen für SharePoint 2013](http://technet.microsoft.com/de-de/library/cc262485%28office.15%29.aspx) und [Konfigurieren einer Umgebung für SharePoint-Add-Ins](http://technet.microsoft.com/de-de/library/fp161236%28office.15%29.aspx) finden Sie Anleitungen zum Einrichten der Installation einer Produktionsumgebung von SharePoint.



In jeder Entwicklungsumgebung sollten Sie einen Computer mit einer x64-fähigen CPU und mindestens 16 Gigabyte (GB) RAM für die Installation und Ausführung von SharePoint verwenden (24 GB RAM empfohlen).



Je nach Ihren spezifischen Anforderungen und Ihrem Budget können Sie unter folgenden Optionen wählen:




- Installieren Sie SharePoint unter Windows Server 2008 R2 Service Pack 1 x64 oder Windows Server 2012.


- Verwenden Sie Microsoft Hyper-V, und installieren Sie SharePoint auf einem virtuellen Computer unter einem Gastbetriebssystem Windows Server 2008 R2 Service Pack 1 x64 oder Windows Server 2012. Eine Anleitung zum Einrichten eines virtuellen Microsoft Hyper-V-Computers für SharePoint finden Sie unter  [Verwenden bewährter Konfigurationsmethoden für virtuelle SharePoint 2013-Computer und die Hyper-V-Umgebung](http://technet.microsoft.com/de-de/library/ff621103%28v=office.15%29.aspx).



> **HINWEIS**
> Die Installation von SharePoint wird nur unter Windows Server 2008 R2 Service Pack 1 x64 oder Windows Server 2012 unterstützt. Wenn Sie SharePoint-Add-Ins für SharePoint unter Windows 7 oder Windows 8 entwickeln möchten, können Sie sich für eine Office 365 Developer Site registrieren und Add-Ins remote entwickeln. 





## Installieren der erforderlichen Komponenten für das Betriebssystem und SharePoint
<a name="bk_prereqsOS"> </a>


1. Führen Sie das PrerequisiteInstaller.exe-Tool aus, das in Ihren Installationsdateien enthalten ist.


2. Führen Sie das in den Installationsdateien enthaltene Setup.exe-Tool aus.


3. Akzeptieren Sie die Microsoft-Software-Lizenzbedingungen.


4. Wählen Sie auf der Seite **Gewünschte Installation auswählen** die Option **Eigenständig** aus.

   **Abbildung 1. Wahl des Installationstyps**



!\[SharePoint 2013-Installationsservertyp](images/SP15_app_ServerType.gif)





5. Wenn bei der Installation Fehler auftreten, überprüfen Sie die Protokolldatei. Sie finden die Protokolldatei, indem Sie ein Eingabeaufforderungsfenster öffnen und die folgenden Befehle an der Eingabeaufforderung eingeben. Beim Abschluss der Installation wird ebenfalls ein Link zur Protokolldatei angezeigt.

  ```

cd %temp%
dir /od *.log
  ```

6. Wenn die Installation abgeschlossen ist, werden Sie aufgefordert, den Konfigurations-Assistenten für SharePoint-Produkte und -Technologien zu starten.

    > **HINWEIS**
      > Bei der Ausführung des Konfigurations-Assistenten für SharePoint-Produkte und -Technologien können Fehler auftreten, wenn Sie einen an eine Domäne angebundenen Computer verwenden, der nicht mit einem Domänencontroller verbunden ist. Bei diesem Fehler müssen Sie entweder direkt oder über eine VPN-Verbindung (Virtuelles Privates Netzwerk) eine Verbindung mit einem Domänencontroller herstellen oder sich mit einem lokalen Konto anmelden, das über Administratorrechte für den Computer verfügt. 
7. Nach Abschluss des Konfigurations-Assistenten wird die neue Seite **Vorlagenauswahl** der neuen SharePoint-Website angezeigt. Wählen Sie auf dieser Seite die Vorlage **Developer Site** aus. Sie können SharePoint-Add-Ins nur aus Visual Studio für eine Developer Site bereitstellen.

   **Abbildung 2. Auswahl der Sitevorlagenseite**



!\[Websitevorlagenseite](images/SP15_appChooseSiteTemplate.gif)






## Konfigurieren von Diensten in SharePoint für die Verwendung in Server-zu-Server-Add-Ins
<a name="Servertoserver"> </a>

IIn diesem Schritt konfigurieren Sie Dienste in SharePoint für die Verwendung in Server-zu-Server-Add-Ins. Mit diesen Schritten wird sichergestellt, dass Sie mit Ihrer Installation besonders vertrauenswürdige vom Anbieter gehostete Add-Ins erstellen können. Weitere Informationen zur Erstellung dieser Art von Add-In finden Sie unter  [Erstellen besonders vertrauenswürdiger Add-Ins für SharePoint 2013](create-high-trust-sharepoint-add-ins.md).




1. Stellen Sie sicher, dass der App-Verwaltungsdienst und die Benutzerprofilanwendung konfiguriert sind. (Es heißt „App-Verwaltungsdienst", da SharePoint-Add-Ins ursprünglich „Apps für SharePoint" hießen). Führen Sie dazu folgende Schritte aus:

1. Wählen Sie in **Zentraladministration** unter **Anwendungsverwaltung** den Eintrag **Dienstanwendungen verwalten** aus.


2. Überzeugen Sie sich auf der Seite **Dienstanwendungen** davon, dass die folgenden Dienste gestartet wurden:

  - Benutzerprofildienst-Anwendung


  - App-Verwaltungsdienst


3. Wählen Sie unter **Anwendungsverwaltung** den Eintrag **Dienstanwendungen verwalten** aus.


4. Stellen Sie auf der Seite **Dienste auf dem Server** sicher, dass die folgenden Dienste gestartet wurden:

  - Benutzerprofildienst 


2. Stellen Sie sicher, dass mindestens ein Profil in der **Benutzerprofildienst-Anwendung** erstellt wird. Führen Sie dazu folgende Schritte aus:

1. Wählen Sie in **Zentraladministration** unter **Anwendungsverwaltung** den Eintrag **Dienstanwendungen verwalten** aus.


2. Wählen Sie dann **Benutzerprofildienst-Anwendung** aus.


3. Wählen Sie auf der Seite **Profildienst verwalten: Benutzerprofildienst-Anwendung** unter **Personen** den Eintrag **Benutzerprofile verwalten** aus.


4. Wählen Sie auf der Seite **Benutzerprofile verwalten** die Option **Neue Profile** aus.


5. Geben Sie auf der Seite **Benutzerprofil hinzufügen** Ihren Kontonamen und Ihre E-Mail-Adresse ein.


6. Wählen Sie **Speichern und schließen** aus.

    > **HINWEIS**
      > Wenn eine Meldung mit der Information angezeigt wird, dass das zu erstellende Profil bereits vorhanden ist, wählen Sie **Abbrechen und zurückgehen** aus.
7. Die Seite **Benutzerprofile verwalten**, die daraufhin wieder angezeigt wird, sollte den Eintrag **Gesamtanzahl der Profile: 1** enthalten.



## Installieren von Visual Studio und Office-Entwicklertools für Visual Studio
<a name="SP15Appdevonprem_bk_installVS"> </a>


- Wenn Sie **Visual Studio** 2013 oder höher noch nicht installiert haben, installieren Sie es mithilfe der Anweisungen unter [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx). Wir empfehlen die Verwendung der  [aktuellsten Version aus dem Microsoft Download Center](https://www.visualstudio.com/downloads/download-visual-studio-vs).


- Visual Studio umfasst **Microsoft Office-Entwicklertools für Visual Studio**, es gibt jedoch auch Fälle, in denen eine Version der Tools zwischen den Updates von Visual Studio veröffentlicht wird. Um sicherzustellen, dass Sie die aktuellste Version der Tools verwenden, führen Sie das [Installationsprogramm für Office Developer Tools für Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) oder das [Installationsprogramm für Office Developer Tools für Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015) aus.



### Ausführliche Protokollierung in Visual Studio

Führen Sie die folgenden Schritte aus, wenn Sie die ausführliche Protokollierung aktivieren möchten:




1. Öffnen Sie die Registrierung und navigieren Sie zu **HKEY_CURRENT_USER\\Software\\Microsoft\\VisualStudio\\ _nn.n_\\SharePointTools**, dabei steht _nn.n_ für die Version von Visual Studio, z. B. 12.0 oder 14.0.


2. Fügen Sie einen DWORD-Schlüssel mit dem Namen **EnableDiagnostics** hinzu.


3. Geben Sie dem Schlüssel den Wert **1**.


Der Registrierungspfad wird sich in kommenden Versionen von Visual Studio ändern.




## Konfigurieren einer isolierten Add-In-Domäne in SharePoint
<a name="SP15appdevonprem_bk_configure"> </a>

Lesen Sie zunächst  [Hostwebs, Add-In-Webs und die isolierte Domäne](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#IsolatedDomain), bevor Sie Vorgehensweisen dieses Abschnitts vornehmen.



Sie müssen auf der Test-SharePoint-Farm eine isolierte Domäne erstellen. Außerdem erfordert Ihre SharePoint-Installation eine allgemeine Platzhalter-Hostheaderdomäne, auf der sie in SharePoint gehostete Add-Ins Apps bereitstellen.



Für Entwicklungszwecke können Sie Ihre Hostdatei nach Bedarf ändern, um den Entwicklungscomputer zum Testen einer Instanz von SharePoint-Add-In weiterzuleiten. Visual Studio ändert Ihre Hostdatei automatisch, wenn Sie das Add-In erstellen und bereitstellen. 




> **HINWEIS**
> Für Produktionsfarmen müssen Sie eine DNS-Weiterleitungsstrategie innerhalb Ihres Intranets erstellen und die Firewall konfigurieren. Weitere Informationen zum Erstellen und Konfigurieren einer Produktionsumgebung für SharePoint-Add-Ins finden Sie unter  [Installieren und Verwalten von SharePoint-Add-Ins](http://technet.microsoft.com/de-de/library/fp161232%28v=office.15%29). 




Führen Sie die Schritte im folgenden Verfahren aus, um eine isolierte Add-In-Domäne zu erstellen.




> **HINWEIS**
> Führen Sie alle Schritte im folgenden Verfahren aus, während Sie als Farmadministrator angemeldet sind. Führen Sie die Eingabeaufforderung und die SharePoint-Verwaltungsshell als Administrator aus. 





### Erstellen einer isolierten Add-In-Domäne auf Ihrem Entwicklungscomputer


1. Stellen Sie sicher, dass der spadmin- und der sptimer-Dienst ausgeführt werden, indem Sie eine Eingabeaufforderung öffnen und die folgenden Befehle eingeben.

  ```

net start spadminv4
net start sptimerv4
  ```

2. Erstellen Sie die isolierte Add-In-Domäne, indem Sie die SharePoint-Verwaltungsshell als Administrator ausführen und den folgenden Befehl eingeben. Ersetzen Sie  _contosoaddins.com_ durch Ihre Add-In-Domäne. Dies sollte *keine*  Unterdomäne der Host-SharePoint-Domäne sein. Dadurch würden die Sicherheitsvorteile isolierter Add-In-Domänen weitgehend zunichte gemacht. Lautet die Hostdomäne zum Beispiel „contoso.com", sollten Sie nicht „addins.contoso.com" als Add-In-Domäne verwenden.

  ```

Set-SPAppDomain "contosoaddins.com"
  ```

3. Stellen Sie sicher, dass die Dienste SPSubscriptionSettingsService und AppManagementServiceInstance ausgeführt werden, indem Sie den folgenden Befehl in der SharePoint-Verwaltungsshell eingeben.

  ```
  Get-SPServiceInstance | where{$_.GetType().Name -eq "AppManagementServiceInstance" -or $_.GetType().Name -eq "SPSubscriptionSettingsServiceInstance"} | Start-SPServiceInstance
  ```

4. Überprüfen Sie, ob die Dienste SPSubscriptionSettingsService und AppManagementServiceInstance ausgeführt werden, indem Sie den folgenden Befehl in der SharePoint-Verwaltungsshell eingeben. Die Ausgabe gibt an, ob jeder Dienst online ist.

  ```
  Get-SPServiceInstance | where{$_.GetType().Name -eq "AppManagementServiceInstance" -or $_.GetType().Name -eq "SPSubscriptionSettingsServiceInstance"}
  ```

5. Sie müssen ein Konto angeben, unter dem die Dienstinstanzen SPSubscriptionService und AppManagementServiceInstance ausgeführt werden sollen. Dieses Konto muss ein SPManagedAccount sein. Sie können ein SPManagedAccount erstellen, indem Sie den folgenden Befehl in der SharePoint-Verwaltungsshell eingeben. (Sie werden zur Eingabe von Kontodomäne\\Benutzer und Kennwort aufgefordert.)

  ```
  $account = New-SPManagedAccount
  ```

6. Geben Sie ein Konto, einen Anwendungspool und Datenbankeinstellungen für die Dienste SPSubscriptionService und AppManagementServiceInstance an, indem Sie den folgenden Code in der SharePoint-Verwaltungsshell eingeben. Wenn Sie im vorherigen Schritt ein SPManagedAccount erstellt haben, verwenden Sie diesen Kontonamen hier.

  ```
  $account = Get-SPManagedAccount "domain\\user"
$appPoolSubSvc = New-SPServiceApplicationPool -Name SettingsServiceAppPool -Account $account
$appPoolAppSvc = New-SPServiceApplicationPool -Name AppServiceAppPool -Account $account
$appSubSvc = New-SPSubscriptionSettingsServiceApplication -ApplicationPool $appPoolSubSvc -Name SettingsServiceApp -DatabaseName SettingsServiceDB 
$proxySubSvc = New-SPSubscriptionSettingsServiceApplicationProxy -ServiceApplication $appSubSvc
$appAppSvc = New-SPAppManagementServiceApplication -ApplicationPool $appPoolAppSvc -Name AppServiceApp -DatabaseName AppServiceDB
$proxyAppSvc = New-SPAppManagementServiceApplicationProxy -ServiceApplication $appAppSvc

  ```

7. Geben Sie Ihr Add-In-Präfix an (siehe  [Hostwebs, Add-In-Webs und die isolierte Domäne](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#IsolatedDomain)), indem Sie folgenden Code in die SharePoint-Verwaltungsshell eingeben.

  ```

Set-SPAppSiteSubscriptionName -Name "add-in" -Confirm:$false
  ```

 **Führen Sie die Schritte in der folgenden Prozedur nur durch, wenn Sie einen Proxyserver verwenden.** Nachdem Sie Ihre isolierte Add-In-Domäne erstellt haben, führen Sie die in der folgenden Prozedur beschriebenen Schritte durch, um die Domäne zu Ihrer Umgehungsliste im Internet Explorer hinzuzufügen. Dadurch wird sichergestellt, dass Sie zu dieser Domäne navigieren können, nachdem Sie ein in SharePoint gehostetes Add-In oder ein vom Anbieter gehostetes Add-In bereitgestellt haben, das ein Add-In-Web umfasst.




### Hinzufügen der isolierten Add-In-Domäne zu Ihrer Umgehungsliste im Internet Explorer


1. Gehen Sie im Internet Explorer auf **Extras**.


2. Wählen Sie **Internetoptionen** aus.


3. Klicken Sie auf der Registerkarte **Verbindungen** auf die Schaltfläche **LAN-Einstellungen**.


4. Deaktivieren Sie das Kontrollkästchen **Automatische Suche der Einstellungen**.


5. Aktivieren Sie das Kontrollkästchen **Proxyserver für LAN verwenden**.


6. Klicken Sie auf die Schaltfläche **Erweitert** und fügen Sie dann*.IhreAddInDomäne.com der Liste **Ausnahmen** hinzu.


7. Klicken Sie auf **OK**.


8. Klicken Sie auf **OK**, um das Dialogfeld **Einstellungen für lokales Netzwerk** zu schließen.


9. Klicken Sie auf **OK**, um das Dialogfeld **Internetoptionen** zu schließen.


Unter  [Bereitstellen und Installieren von SharePoint-Add-Ins: Methoden und Optionen](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) finden Sie weitere Informationen über Ihre Optionen zum Bereitstellen Ihrer Add-Ins.




> **TIPP**
> Nachdem Sie in Ihrer Installation ein von SharePoint gehostetes Add-In bereitgestellt haben, werden Sie beim Starten des Add-Ins möglicherweise dazu aufgefordert, sich mit Ihren Anmeldeinformationen anzumelden. Sie müssen die Loopbackprüfung deaktivieren, um diese Aufforderungen zu vermeiden. Eine Anleitung zur Deaktivierung der Loopbackprüfung finden Sie unter  [Fehler 401.1 beim Aufrufen einer Website, die integrierte Authentifizierung verwendet und mit IIS 5.1 oder einer höheren Version gehostet wird](http://support.microsoft.com/kb/896861/de-de). 





## Weitere Informationsquellen
<a name="SP15SetupSPO365_bk_addlresources"> </a>


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)


-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)







