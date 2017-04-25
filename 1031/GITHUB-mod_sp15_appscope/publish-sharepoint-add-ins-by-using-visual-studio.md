---
title: Veröffentlichen von SharePoint-Add-Ins mithilfe von Visual Studio
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# Veröffentlichen von SharePoint-Add-Ins mithilfe von Visual Studio
Erfahren Sie, wie Sie Ihre SharePoint-Add-In mithilfe von Microsoft Visual Studio 2013 oder Visual Studio 2012 veröffentlichen. Wenn mit dem Add-In eine Webanwendung verknüpft ist, stellen Sie diese zuerst bereit. Wie bei allen SharePoint-Add-Ins packen Sie dann die SharePoint-Add-In und veröffentlichen sie anschließend. Sie können Ihr Add-In auch optional zur Aufnahme in den Office Store übermitteln.
## Voraussetzungen
<a name="Prereq"> </a>






-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284)

    -Oder-

   [Visual Studio 2012](https://www.microsoft.com/de-de/download/details.aspx?id=30682) und Office-Entwicklertools für Visual Studio. Informationen zum Herunterladen dieser Tools finden Sie auf der [Downloadseite](http://go.microsoft.com/fwlink/?LinkId=234393) im Abschnitt „Tools". (Der neue Publish Manager ist in Visual Studio 2012 oder früheren Versionen nicht verfügbar).


- Microsoft SharePoint 2013



## Veröffentlichen mithilfe von Visual Studio 2013
<a name="VS2013"> </a>

Wenn Ihre von einem Anbieter gehostete SharePoint-Add-In über eine Webanwendung verfügt, stellen Sie zuerst die Dateien für diese bereit. Wie bei allen SharePoint-Add-Ins packen Sie dann die SharePoint-Add-In und veröffentlichen sie anschließend.




> **WICHTIG**
> Um sicherzustellen, dass Ihre SharePoint-Client-ID und Ihr geheimer Clientschlüssel mit Ihrem Webprojekt veröffentlicht werden, sodass Ihre Webinhalte auf SharePoint-Daten zugreifen können, veröffentlichen Sie Ihr SharePoint-Add-In-Projekt von der Seite **Add-In veröffentlichen**. Sie können diese Seite aufrufen, indem Sie das Kontextmenü der SharePoint-Add-In - nicht das Kontextmenü der Webapp - aufrufen, und dann den Befehl **Veröffentlichen** auswählen.





### Schritt 1: Stellen Sie die Webanwendung bereit

Ihre SharePoint-Add-In ist in der Regel mit einer Hostwebanwendung verknüpft, die Sie auf einem Webserver bereitstellen müssen. Weitere Informationen zur Verwendung des Assistenten zur Veröffentlichung von Webinhalten finden Sie unter  [Vorgehensweise: Bereitstellen eines Webanwendungsprojekts mit der One-Click-Veröffentlichung in Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).




### So öffnen Sie die Seite "Add-In veröffentlichen"


- Öffnen Sie im **Projektmappen-Explorer** das Kontextmenü für das SharePoint-Add-In-Projekt, und wählen Sie dann **Veröffentlichen**.

    Die Seite **Add-In veröffentlichen** wird angezeigt.



### So wählen oder erstellen Sie ein Profil


- Wählen Sie in der Liste **Aktuelles Profil** ein Profil aus, das importiert werden soll, oder wählen Sie **<Neu …>** aus, um ein Profil zu erstellen.

    Ein Veröffentlichungsprofil gibt den Server an, auf dem die Web App bereitgestellt wird, sowie die Anmeldedaten, die zur Anmeldung am Server erforderlich sind, die Datenbank, die bereitgestellt werden soll, (falls zutreffend) und weitere Bereitstellungsoptionen. Sie können entsprechend Ihren Anforderungen unterschiedliche Veröffentlichungsprofile erstellen. Sie können z. B. ein Profil zum Testen und ein weiteres Profil zum Veröffentlichen erstellen.

    Wenn Sie **<Neu …>** auswählen, wird der Assistent **Veröffentlichungsprofil erstellen** angezeigt. Sie können diesen Assistenten verwenden, um ein Veröffentlichungsprofil von einem Webhosting-Anbieter wie Azure zu importieren, oder um ein Profil zu erstellen, und dann manuell den Servernamen, die Anmeldedaten und andere Einstellungen hinzuzufügen. Wenn Sie kein vorhandenes Profil importiert, sondern ein neues Profil erstellt haben, müssen Sie die Werte für die Client-ID und den geheimen Clientschlüssel angeben, wie in [Richtlinien für das Registrieren von Add-Ins für SharePoint 2013](http://msdn.microsoft.com/library/jj687469.aspx) und [Gewusst wie: Erstellen von Client-IDs und geheimen Clientschlüsseln im Microsoft Seller Dashboard](http://msdn.microsoft.com/library/office/jj220036.aspx) beschrieben.

    Wenn Sie Ihre SharePoint-Add-In an den Office Store übermitteln möchten, müssen Sie für die Client-ID und den geheimen Clientschlüssel Werte verwenden, die im Verkäuferdashboard erstellt werden. Sie können für Client-IDs und geheime Clientschlüssel Werte verwenden, die Sie in der Entwicklungsphase mithilfe der Seite "appregnew.aspx" erstellt haben. Add-Ins, die Sie an den Office Store übermitteln, müssen allerdings Client-IDs und geheime Clientschlüssel verwenden, die Sie vom Verkäuferdashboard erhalten. Außerdem sollten Sie das Veröffentlichungsprofil auf Ihrer Azure-Website erstellen und es dann in Visual Studio importieren, anstatt ein Profil im Assistenten **Veröffentlichungsprofil erstellen** zu erstellen. Wenn Sie ein Profil in Azure erstellen, werden alle Einstellungen auf der Registerkarte **Verbindung**für Sie in Visual Studio bereitgestellt. Weitere Informationen zum Importieren oder Erstellen eines Veröffentlichungsprofils finden Sie unter  [Erstellen eines Veröffentlichungsprofils](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile).

    > **TIPP**
    > Wenn Sie Webinhalte nicht direkt veröffentlichen können, können Sie ein Webbereitstellungspaket erstellen, das ein Administrator für Sie im Web bereitstellen kann. Um ein Webbereitstellungspaket zu erstellen, erstellen Sie ein neues Profil, wählen Sie die Registerkarte **Verbindung** und dann in der Liste **Veröffentlichungsmethode** die Option **Webbereitstellungspaket** aus.

### So stellen Sie Ihr Web-App-Projekt bereit


1. Wählen Sie auf der Seite **Add-In veröffentlichen** die Schaltfläche **Webprojekt bereitstellen** aus.

    Das Dialogfeld **Webinhalte veröffentlichen** wird angezeigt.


2. Geben Sie auf den Registerkarten **Verbindung** und **Einstellungen** alle fehlenden Werte ein.

    Wenn Sie ändern möchten, wie die Dateien für Ihre SharePoint-Add-In veröffentlicht werden, oder ob das Add-In eine externe Datenbank verwendet, wählen Sie die Registerkarte **Einstellungen** aus. Siehe hierzu den Abschnitt "Configuring the Settings Tab" in [How to: Deploy a Web Project using On-Click Publishing in Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).


3. Um zu prüfen, welche Elemente geändert werden, wenn die Web App veröffentlicht wird, wählen Sie auf der Registerkarte **Vorschau** die Schaltfläche **Vorschau starten** aus.


4. Wählen Sie die Schaltfläche **Veröffentlichen** aus, um das Web-App-Projekt bereitzustellen.



### Schritt 2: Packen Sie das Add-In


1. Wählen Sie auf der Seite **Add-In veröffentlichen** die Schaltfläche **Package the add-in** (Add-In packen) aus.

    Der Assistent **Add-Ins für Office und SharePoint veröffentlichen** wird angezeigt.


2. Geben Sie im Textfeld **Wo wird Ihre Website gehostet?** die URL der Website ein, welche die Inhaltsdateien Ihrer SharePoint-Add-In hosten wird.

    Sie müssen eine Adresse angeben, die mit dem Präfix "https" beginnt. Siehe hierzu  [Warum müssen meine Add-Ins SSL-gesichert sein?](http://msdn.microsoft.com/library/jj591603#bk_q7).

    > **HINWEIS**
    > Azure-Websites bieten automatisch einen https-Endpunkt. Wenn Sie Ihr Add-In auf einer Office Store-Website oder im Office Store veröffentlichen, muss die Adresse mit einem https-Präfix beginnen. Wenn Sie das Add-In allerdings auf einer lokalen Website veröffentlichen, können Sie ein http-Präfix verwenden. 

    Im Textfeld **Wie lautet die Client-ID des Add-Ins?** sollte bereits die Client-ID angezeigt werden, die Sie im Veröffentlichungsprofil eingegeben haben.

    Wenn Sie für diese Client-ID bisher einen Platzhalterwert verwendet haben, müssen Sie nun eine richtige Client-ID hinzufügen. Diese Informationen werden in das .app-Paket eingebettet und ermöglichen die Kommunikation zwischen Ihren Webinhalten und SharePoint auf der Live-Website.


3. Klicken Sie auf die Schaltfläche **Fertig stellen**.

    Visual Studio generiert die erforderlichen Dateien, um Ihre SharePoint-Add-In zu veröffentlichen, und öffnet dann den Ordner mit der Veröffentlichungsausgabe. Weitere Informationen zur Installation des Add-Ins finden Sie unter  [Installieren und Verwalten von Add-Ins für SharePoint 2013](http://technet.microsoft.com/library/fp161232.aspx).



### Schritt 3: Veröffentlichen Sie Ihre SharePoint-Add-In im Office Store

Gehen Sie folgendermaßen vor, wenn Sie Ihre SharePoint-Add-In an den Office Store übermitteln möchten.




1. Wählen Sie auf der Seite **Add-In veröffentlichen** die Schaltfläche **Verkäuferdashboard besuchen** aus, und melden Sie sich dann bei Ihrem Microsoft-Verkäuferdashboard an.

    Siehe  [Veröffentlichen von Office- und SharePoint-Add-Ins und Office 365 Web Apps im Office Store](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx).


2. Wählen Sie **Neue App hinzufügen** aus, tragen Sie die Informationen ein, und senden Sie das Add-In dann an den Office Store. Weitere Informationen finden Sie unter [Verwenden des Verkäuferdashboards zum Übermitteln von Office- und SharePoint-Add-Ins und Office 365-Apps an den Office Store](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx).



## Veröffentlichen mithilfe von Visual Studio 2012
<a name="VS2012"> </a>

Wenn Sie soweit sind, dass Sie die SharePoint-Add-In als Paket veröffentlichen können, öffnen Sie den Assistenten zum **Veröffentlichen von Office-Add-Ins**, der die Dateien in Ihrer SharePoint-Add-In für die Veröffentlichung vorbereitet.




### Schritt 1: Stellen Sie die Dateien einer SharePoint-Add-In als Paket zusammen


1. Öffnen Sie im **Projektmappen-Explorer** das Kontextmenü für das SharePoint-Add-In-Projekt, und wählen Sie dann **Veröffentlichen**.

    Der Assistent zum **Veröffentlichen von Office-Add-Ins** wird angezeigt. Welche Seiten im Assistenten angezeigt werden, hängt vom Typ der als Paket zu veröffentlichenden SharePoint-Add-In ab. Wenn das Add-In in SharePoint gehostet wird, wird nur die Seite **Zusammenfassung** angezeigt. Soll das Add-In von einem Provider gehostet werden, dann werden auch die Seiten **Profil** und **Hosting** angezeigt.


2. Wenn Ihre SharePoint-Add-In von einem Anbieter gehostet wird, geben Sie in der Liste zur **Angabe des zu veröffentlichenden Profils** den Namen eines Veröffentlichungsprofils an, und wählen Sie dann die Schaltfläche **Weiter**.

    Im Veröffentlichungsprofil werden die Informationen gespeichert, die Sie auf der Seite **Hosting** eingeben.


3. Geben Sie in der Liste zur **Angabe des Websitehosts** die URL der Webanwendung an, die als Host der SharePoint-Add-In fungieren wird.


4. Geben Sie in den Feldern zur **Angabe der Identität des Add-Ins** die Client-ID und das Clientgeheimnis des Add-Ins ein, und wählen Sie dann die Schaltfläche **Weiter**.

    Siehe hierzu  [Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md).


5. Aktivieren Sie für alle Typen von SharePoint-Add-Ins das Kontrollkästchen **Ausgabeordner nach dem Verpacken öffnen**, sofern es nicht bereits aktiviert ist, und wählen Sie dann die Schaltfläche **Fertig stellen**.

    Visual Studio generiert alle Dateien, die zum Veröffentlichen der SharePoint-Add-In erforderlich sind. Sie finden diese Dateien im Ordner  `app.Publish` im Projektausgabeordner (beispielsweise `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`). Dieser Ordner enthält eine APP-Datei für die SharePoint-Add-In und mehrere Dateien für die Webanwendung (wenn die SharePoint-Add-In cloudbasiert ist). Alle SharePoint-Add-Ins verfügen über eine APP-Datei, die das Add-In-Manifest zum Veröffentlichen der SharePoint-Add-In enthält. Vom Anbieter gehostete SharePoint-Add-Ins enthalten auch Dateien zum Veröffentlichen der Hostwebanwendung.



### Schritt 2: Veröffentlichen Sie die Webanwendung

Wenn die SharePoint-Add-In von einem Anbieter gehostet wird, müssen Sie die verknüpfte Webanwendung auf einem Webserver veröffentlichen. Visual Studio erzeugt ein Bereitstellungspaket und ein Skript, das Ihnen die Ausführung dieser Aufgabe erleichtern soll.



Das Bereitstellungspaket des Webanwendungsprojekts befindet sich in einer komprimierten Datei (.ZIP) im Ordner  `app.publish`. Neben dieser ZIP-Datei enthält der Ordner  `app.publish` folgende Dateien:





|**Datei**|**Beschreibung**|
|:-----|:-----|
| _ProjectName_.deploy.cmd|Dies ist eine Befehlszeilen-Batchdatei, die Web Deploy aufruft, um die Installation des Pakets über die Befehlszeile zu erleichtern.|
| _ProjectName_.SetParameters.xml|Diese Datei enthält Parameter, die Web Deploy übergeben werden, wenn Sie die App mithilfe der Datei "deploy.cmd" installieren. Die Paketeinstellungen von Visual Studio bestimmen die Standardwerte für die einzelnen Parameter. Sie können diese Werte beispielsweise ändern, wenn Sie die Webanwendung auf mehreren Servern bereitstellen und für jeden Server andere Einstellungen verwenden möchten.|
| _ProjectName_.SourceManifest.xml|Diese Datei enthält Einstellungen, die Visual Studio an Web Deploy übergibt und die von Web Deploy zum Erstellen des Webpakets verwendet werden. Web Deploy benötigt diese Datei nur zum Erstellen des Pakets. Diese Datei wird nicht zur Installation des Pakets verwendet.|
 
Eine Schritt-für-Schritt-Anleitung finden Sie unter  [Gewusst wie: Installieren eines Bereitstellungspakets mit der von Visual Studio erstellten Datei „deploy.cmd"](http://go.microsoft.com/fwlink/?LinkID=254954)




### Schritt 3: Veröffentlichen Sie Ihre SharePoint-Add-In
<a name="Publish"> </a>

Zum Veröffentlichen der SharePoint-Add-In laden Sie die Add-In-Manifestdatei (.APP) des Add-Ins in den Office Store, den Katalog für Add-Ins für Office, SharePoint, eine Dateifreigabe oder einen Exchange-Katalog hoch. Das Add-In-Manifest Ihres Add-Ins befindet sich im Ordner  `app.publish`, z. B.  `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`. Weitere Informationen zum Veröffentlichen Ihrer SharePoint-Add-In finden Sie unter  [Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md).




## Weitere Ressourcen
<a name="Additional"> </a>


-  [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md)


-  [Veröffentlichen Ihres Office-Add-ins](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)



