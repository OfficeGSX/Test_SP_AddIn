---
title: Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: 1b992485-6efe-4ea4-a18c-221689b0b66f
---


# Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins
Richten Sie eine Entwicklungsumgebung ein, und erstellen Sie Ihr erstes von SharePoint gehostetes SharePoint-Add-In
Von SharePoint gehostete Add-Ins sind eine der zwei Haupttypen von SharePoint-Add-Ins. Einen schnellen Überblick über SharePoint-Add-Ins und die zwei verschiedenen Typen finden Sie unter  [SharePoint-Add-Ins](sharepoint-add-ins.md). Hier ist eine Zusammenfassung zu von SharePoint gehosteten Add-Ins:
  
    
    


- Sie enthalten SharePoint-Listen, Webparts, Workflows, benutzerdefinierte Seiten und andere Komponenten, die auf einer Unterwebsite mit der Bezeichnung Add-In-Web der SharePoint-Website installiert sind, wo das Add-In installiert ist.
    
  
- Der einzige verfügbare Code ist JavaScript auf benutzerdefinierten SharePoint-Seiten.
    
  

 [![Schritt 1: Einrichten der Entwicklungsumgebung](images/6d3bbe0a-399e-4747-9e1a-01d42954ce32.png)
  
    
    
](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#Setup) [![Schritt 2: Erstellen des App-Projekts](images/d69871f6-c503-463b-bf96-4b6d7306c313.png)
  
    
    
](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#Create) [![Schritt 3: Codieren Ihrer App](images/e5f8a9a2-e5fb-42d1-b19a-300178c626fb.png)
  
    
    
](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#Code)
  
    
    


## Einrichten der Entwicklungsumgebung
<a name="Setup"> </a>

Es gibt verschiedene Möglichkeiten zum Einrichten einer Entwicklungsumgebung für SharePoint-Add-Ins. In diesem Abschnitt wird die einfachste Möglichkeit erläutert. Alternativen finden Sie unter  [Zusätzliche Ressourcen](#bk_addresources).
  
    
    

### Abrufen der Tools


- Wenn Sie **Visual Studio** 2013 oder höher noch nicht installiert haben, installieren Sie es mit den Anweisungen unter [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx). Wir empfehlen die Verwendung der  [neuesten Version aus dem Microsoft Download Center](https://www.visualstudio.com/downloads/download-visual-studio-vs).
    
  
- Visual Studio enthält die **Microsoft Office-Entwicklertools für Visual Studio**, aber manchmal wird eine Version der Tools zwischen Updates von Visual Studio veröffentlicht. Um sicherzustellen, dass Sie die neueste Version der Tools verwenden, führen Sie das [Installationsprogramm für Office Developer Tools für Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) oder das [Installationsprogramm für Office Developer Tools für Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015) aus.
    
  

### Registrieren für eine Website für Office 365-Entwickler
<a name="o365_signup"> </a>


> [!HINWEIS]
>  Möglicherweise haben Sie bereits Zugriff auf eine Website für Office 365-Entwickler.> **Sind Sie MSDN-Abonnent?** Visual Studio Ultimate und Visual Studio Premium mit MSDN-Abonnenten erhalten als Bonus ein einjähriges Office 365 Developer-Abonnement. [Lösen Sie Ihren Bonus heute ein.](https://login.live.com/login.srf?wa=wsignin1.0&amp;rpsnv=12&amp;ct=1402926170&amp;rver=6.0.5276.0&amp;wp=MCMBI&amp;wlcxt=msdn%24msdn%24msdn&amp;wreply=https%3a%2f%2fmsdn.microsoft.com%2fsubscriptions%2fmanage%2fdefault.aspx&amp;lc=1033&amp;id=254354&amp;mkt=de-DE)> **Besitzen Sie einen der folgenden Office 365 Abonnementpläne?**> **Falls ja, kann ein Administrator des Office 365-Abonnements eine Website für Entwickler** über das [Office 365 Admin Center](https://portal.microsoftonline.com/admin/default.aspx) erstellen. Weitere Informationen finden Sie unter [Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement](create-a-developer-site-on-an-existing-office-365-subscription.md). 
  
    
    

Es gibt drei Wege zu einem Office 365-Plan.
  
    
    

- Registrieren Sie sich über das Office 365-Entwicklerprogramm für ein kostenloses einjähriges Office 365-Entwicklerkonto.  [Informieren Sie sich ausführlicher](http://dev.office.com/devprogram), oder wechseln Sie direkt zum  [Registrierungsformular](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170). Nach der Registrierung für das Entwicklerprogramm erhalten Sie eine E-Mail mit einem Link, über den Sie sich für das Entwicklerkonto registrieren können. Beachten Sie die nachstehenden Anweisungen.
    
  
- Beginnen Sie mit einer  [kostenlosen 30-Tage-Testversion](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK) mit einer Benutzerlizenz.
    
  
- Erwerben Sie ein  [Office 365 Developer-Abonnement](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK).
    
  

> [!TIPP]
> Öffnen Sie diese Links in einem anderen Fenster oder einer anderen Registerkarte, damit die nachfolgenden Anweisungen übersichtlich bleiben. 
  
    
    


**Abb. 1. Domänenname der Office 365-Entwicklerwebsite**

  
    
    

  
    
    
![Seite 2 des Registrierungsformulars für das Office 365-Konto](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. Die erste Seite (nicht abgebildet) des Registrierungsformulars ist selbsterklärend. Geben Sie einfach die erforderlichen Informationen zu Ihrer Person an, und wählen Sie **Weiter** aus.
    
  
2. Geben Sie auf der zweiten Seite, die in Abbildung 1 gezeigt ist, eine Benutzer-ID für den Administrator des Abonnements an.
    
  
3. Erstellen Sie eine Unterdomäne von **.onmicrosoft.com**, zum Beispiel contoso.onmicrosoft.com.
    
    Nach der Registrierung müssen Sie die resultierenden Anmeldeinformationen (im Format  _UserID_@ _IhreDomäne_.onmicrosoft.com) benutzen, um sich auf Ihrer Office 365-Portalwebsite anzumelden, auf der Sie Ihr Konto verwalten. Ihre SharePoint Online-Entwicklerwebsite wird unter Ihrer neuen Domäne **http:// _IhreDomäne_.sharepoint.com** bereitgestellt.
    
  
4. Wählen Sie **Weiter** aus, und füllen Sie die letzte Seite des Formulars aus. Wenn Sie eine Telefonnummer bereitstellen, um Ihren Bestätigungscode zu erhalten, können Sie eine Mobil- oder Festnetznummer, aber *keine*  VoIP-Nummer (Voice over Internet Protocol) benutzen.
    
  

    
> [!HINWEIS]
> Wenn Sie beim Versuch, sich bei einem Entwicklerkonto anzumelden, bei einem anderen Microsoft-Konto angemeldet sind, wird möglicherweise die folgende Nachricht angezeigt: „Die eingegebene Benutzer-ID hat leider nicht funktioniert. Sie ist anscheinend nicht gültig. Geben Sie die Benutzer-ID ein, die Ihnen von Ihrem Unternehmen zugewiesen wurde. Ihre Benutzer-ID hat in der Regel das Format  *someone@example.com*  oder *someone@example.onmicrosoft.com*  ."> Wenn diese Nachricht angezeigt wird, melden Sie sich vom Microsoft-Konto ab, und versuchen Sie es erneut. Sollte die Nachricht weiterhin angezeigt werden, löschen Sie den Browsercache, oder wechseln Sie zu **InPrivate-Browsen** und füllen Sie das Formular aus.
  
    
    

Nachdem Sie die Registrierung abgeschlossen haben, wird in Ihrem Browser die Office 365-Installationsseite geöffnet. Wählen Sie das Admin-Symbol aus, um die Admin Center-Seite zu öffnen.
  
    
    

**Abb. 2. Office 365 Admin Center-Seite**

  
    
    

  
    
    
![Screenshot mit dem Office 365 Admin Center](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. Warten Sie, bis der Einrichtungsprozess für Ihre Website für Entwickler abgeschlossen ist. Nach Abschluss der Bereitstellung aktualisieren Sie die Admin Center-Seite im Browser.
    
  
2. Wählen Sie dann links oben den Link **Add-Ins erstellen** aus, um Ihre Website für Entwickler zu öffnen. Sie sollten eine Website wie in Abbildung 3 dargestellt sehen. Mit der Liste **Add-Ins im Test** auf der Seite wird bestätigt, dass die Website mit der Entwicklerwebsitevorlage von SharePoint erstellt wurde. Wird stattdessen eine Teamwebsite angezeigt, warten Sie einige Minuten und starten dann Ihre Website erneut.
    
  
3. Notieren Sie die URL der Website. Diese wird verwendet, wenn Sie SharePoint-Add-Ins-Projekte in Visual Studio erstellen.
    
  

**Abb. 3: Die Startseite Ihrer Entwicklerwebsite mit der Liste der Add-Ins im Test**

  
    
    

  
    
    
![Screenshot, auf dem die Entwicklerwebsite-Startseite angezeigt ist](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## Erstellen des Add-In-Projekts
<a name="Create"> </a>


1. Starten Sie Visual Studio mit der Option **Als Administrator ausführen**.
    
  
2. Wählen Sie **Datei** > **Neu** > **Neues Projekt**.
    
  
3. Erweitern Sie im Dialogfeld **Neues Projekt** den Knoten **Visual C#**, dann den Knoten **Office/SharePoint**, und wählen Sie den Knoten **Add-Ins** > **Add-In für SharePoint** aus.
    
  
4. Nennen Sie das Projekt EmployeeOrientation, und wählen Sie dann **OK**.
    
  
5. Stellen Sie im ersten Dialogfeld **Add-In für SharePoint-Einstellungen angeben** die vollständige URL der SharePoint-Website bereit, die Sie verwenden möchten, um das Add-In zu debuggen. Dabei handelt es sich um die URL der Website für Entwickler. (Verwenden Sie HTTPS und nicht HTTP in der URL). Wählen Sie unter **Wie soll Ihr Add-In für SharePoint gehostet werden** die Option **Von SharePoint gehostet** aus. Wählen Sie **Fertig stellen** aus.
    
  
6. Sie werden möglicherweise aufgefordert, sich bei Ihrer Website für Entwickler anzumelden. Wenn dies der Fall ist, verwenden Sie die Administratoranmeldeinformationen des entsprechenden Abonnements.
    
  
7. Nachdem das Projekt erstellt wurde, öffnen Sie die Datei **/Pages/Default.aspx** aus dem Stammverzeichnis des Projekts. Unter anderem lädt diese generierte Datei eines oder beide der zwei Skripts, die auf SharePoint: sp.runtime.js und sp.js gehostet werden. Das Markup zum Laden dieser Dateien befindet sich im **Content**-Steuerelement im oberen Bereich der Datei mit der ID **PlaceHolderAdditionalPageHead**. Das Markup variiert in Abhängigkeit von der **Microsoft Office-Entwicklertools für Visual Studio** -Version, die Sie verwenden. Diese Reihe von Lernprogrammen erfordert, dass beide Dateien geladen werden und sie mit normalen HTML **<script>**-Tags und nicht mit **<SharePoint:ScriptLink>**-Tags geladen werden. Stellen Sie sicher, dass die folgenden Zeilen im **PlaceHolderAdditionalPageHead**-Steuerelement  *über*  der Zeile `<meta name="WebPartPageExpansion" content="full" />` enthalten sind:
    
  ```
  
<script type="text/javascript" src="/_layouts/15/sp.runtime.js"></script>
<script type="text/javascript" src="/_layouts/15/sp.js"></script> 

  ```


    Durchsuchen Sie anschließend die Datei nach einem anderen Markup, das auch eine oder die andere dieser Dateien lädt, und entfernen Sie das redundante Markup. Speichern und schließen Sie die Datei.
    
  

## Codieren Ihres Add-Ins
<a name="Code"> </a>

Für das erste von SharePoint gehostete SharePoint-Add-In ist die klassische SharePoint-Erweiterung enthalten: eine benutzerdefinierte Liste und Listeninstanz.
  
    
    

1. Öffnen Sie im **Projektmappen-Explorer** die Datei „AppManifest.xml".
    
  
2. Fügen Sie beim Öffnen des Manifest-Designers ein Leerzeichen zwischen den Wörtern im Feld **Title** hinzu, sodass dortEmployee Orientation steht. (Ändern Sie das Feld **Name** *nicht*  .)
    
  
3. Speichern und schließen Sie die Datei.
    
  
4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neuer Ordner** aus. Nennen Sie den OrdnerListen.
    
  
5. Klicken Sie mit der rechten Maustaste auf den neuen Ordner, und wählen Sie **Hinzufügen** > **Neues Element** aus. Das Dialogfeld **Neues Element hinzufügen** wird mit dem Knoten **Office/SharePoint** geöffnet.
    
  
6. Wählen Sie **Liste** aus. Geben Sie ihr den NamenNewEmployeeOrientation, und wählen Sie dann **Hinzufügen** aus.
    
  
7. Lassen Sie auf der Seite **Listeneinstellungen auswählen** im **Assistenten zum Anpassen von SharePoint** den Listenanzeigenamen auf dem Standardwert **NewEmployeeOrientation**, wählen Sie das Optionsfeld **Anpassbare Listenvorlage und Listeninstanz erstellen** und anschließend **Standard (benutzerdefinierte Liste)** in der Dropdownliste aus. Klicken Sie dann auf **Fertig stellen**.
    
  
8. Der Assistent erstellt eine **NewEmployeeOrientation**-Listenvorlage mit einer untergeordneten Listeninstanz mit der Bezeichnung **NewEmployeeOrientationInstance**. Möglicherweise wird ein Listen-Designer geöffnet. Dieser wird in einem späteren Schritt verwendet.
    
  
9. Erweitern Sie den Knoten **NewEmployeeOrientationInstance** im **Projektmappen-Explorer**, sofern dies noch nicht geschehen ist, damit Sie die Datei „elements.xml", die ein untergeordnetes Element der Liste  *Instanz*  ist, klar von der Datei „elements.xml" unterscheiden können, die ein untergeordnetes Element der Liste *Vorlage*  ist.
    
   **Listenknoten im Projektmappen-Explorer**

  

     ![Listenordner mit der untergeordneten Vorlage "NewEmployeeOrientation", die wiederum über drei untergeordnete Elemente verfügt: eine NewEmployeeOrientationInstance, die Datei "elements.xml" und die Datei "schema.xml". Die Instanz selbst ist ein untergeordnetes Element mit dem Namen "elements.xml".](images/10e5d116-d24b-4a44-bfff-cfbf2f971b1e.PNG)
  

    
    
  
10. Öffnen Sie das untergeordnete Element „elements.xml" der Listenvorlage **NewEmployeeOrientation**.
    
  
11. Fügen Sie dem Attribut **DisplayName** (nicht dem Attribut **Name**) Leerzeichen hinzu, damit es besser aussieht: „New Employee Orientation".
    
  
12. Legen Sie das Attribut **Description** auf„Orientation information about new employees." fest.
    
  
13. Lassen Sie alle anderen Attribute auf dem Standardwert, speichern Sie die Datei und schließen Sie sie.
    
  
14. Wenn der Listen-Designer nicht geöffnet wurde, wählen Sie den Knoten **NewEmployeeOrientation** im **Projektmappen-Explorer** aus.
    
  
15. Öffnen Sie die Registerkarte **Liste** des Designers. Diese Registerkarte wird verwendet, um bestimmte Werte für die Liste *Instanz*  und nicht für die Liste *Vorlage*  festzulegen, aber sie enthält einige Standardwerte, die sie aus der Vorlage übernommen hat.
    
  
16. Ändern Sie die Werte auf dieser Registerkarte wie folgt:
    
  - **Titel**: Neue Mitarbeiter in Seattle
    
  
  - **Listen-URL**: Listen/NewEmployeesInSeattle
    
  
  - **Beschreibung**: Die neuen Mitarbeiter in Seattle.
    
  

    Lassen Sie die Kontrollkästchen auf ihrer Standardeinstellung, speichern Sie die Datei und schließen Sie den Designer.
    
  
17. Die Listeninstanz hat möglicherweise ihren alten Namen im **Projektmappen-Explorer** beibehalten. Wenn dies der Fall ist, öffnen Sie das Kontextmenü für **NewEmployeeOrientationInstance**, wählen **Umbenennen** aus und ändern den Namen inNewEmployeesInSeattle.
    
  
18. Öffnen Sie die Datei schema.xml.
    
  
19. Ersetzen Sie im Element **View**, dessen **BaseViewID**-Wert „0" ist, das vorhandene Element **ViewFields** durch das folgende Markup. (Verwenden Sie genau diese GUID für das **FieldRef**-Element mit dem Namen  `Title`.)
    
     *Zeilenumbrüche können an ungeraden Stellen in dieser automatisch generierten schema.xml-Datei vorkommen. Stellen Sie sicher, dass Sie die übereinstimmenden Start- und Endtags für das **ViewFields**-Element gefunden haben. Fügen Sie Zeilenumbrüche hinzu, um die Lesbarkeit zu verbessern.* 
    


  ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Employee" />
 </ViewFields>
  ```

20. Ersetzen Sie dann in der Datei „schema.xml" im Element **View**, dessen **BaseViewID**-Wert „1" ist, das vorhandene Element **ViewFields** durch das folgende Markup. (Verwenden Sie genau diese GUID für das **FieldRef**-Element mit dem Namen  `LinkTitle`.)
    
  ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
</ViewFields>
  ```

21. Speichern und schließen Sie die Datei schema.xml.
    
  
22. Öffnen Sie die Datei „elements.xml", die ein untergeordnetes Element der Liste  *Instanz* **NewEmployeesInSeattle** ist (nicht die Datei „elements.xml", die ein untergeordnetes Element der Liste *Vorlage* **NewEmployeeOrientation**) ist.
    
  
23. Füllen Sie in dieser Datei die Liste mit einigen Ausgangsdaten. Hierzu fügen Sie folgendes **Data**-Elementmarkup als untergeordnetes Element des **ListInstance**-Elements hinzu. 
    
  ```
  
<Data>
  <Rows>
    <Row>
      <Field Name="Title">Tom Higginbotham</Field>
    </Row>
    <Row>
      <Field Name="Title">Satomi Hayakawa</Field>
    </Row>
    <Row>
      <Field Name="Title">Cassi Hicks</Field>
    </Row>
    <Row>
      <Field Name="Title">Lertchai Treetawatchaiwong</Field>
    </Row>
  </Rows>
</Data>
  ```

24. Speichern und schließen Sie die Datei.
    
  
25. Doppelklicken Sie im **Projektmappen-Explorer** auf **Feature1**, um den Feature-Designer zu öffnen. Legen Sie im Designer den **Titel** aufOrientierungskomponenten für neue Mitarbeiter und die **Beschreibung** aufListen und andere Komponenten, die zur Orientierung neuer Mitarbeiter im Unternehmen dienen fest. Speichern Sie die Datei, und schließen Sie den Designer.
    
  
26. Wenn **Feature1** im **Projektmappen-Explorer** nicht automatisch umbenannt wurde, öffnen Sie das zugehörige Kontextmenü, wählen **Umbenennen** aus und benennen die Option inNewEmployeeOrientationComponents um.
    
  
27. Öffnen Sie die Datei „Default.aspx".
    
  
28. Suchen Sie das ASP.NET **Content**-Element mit der ID **PlaceHolderPageTitleInTitleArea**. Ersetzen Sie die standardmäßige Zeichenfolge „Seitentitel" durch „Neue Mitarbeiter nach Standort".
    
  
29. Suchen Sie das ASP.NET **Content**-Element mit der ID **PlaceHolderMain**.  *Ersetzen*  Sie seinen Inhalt mit dem folgenden Markup. ` _spPageContextInfo` ist ein JavaScript-Objekt, das SharePoint automatisch auf der Seite enthält. Die entsprechende `webAbsoluteUrl`-Eigenschaft gibt die URL des Add-In-Web zurück.
    
  ```XML
  
<p><asp:HyperLink runat="server"
    NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Lists/NewEmployeesInSeattle/AllItems.aspx';" 
    Text="New Employees in Seattle" /></p>

  ```


## Ausführen des Add-Ins und Testen der Liste
<a name="Code"> </a>


  
    
    

1. Verwenden Sie die F5-TASTE, um Ihr Add-In bereitzustellen und auszuführen. Visual Studio führt eine temporäre Installation des Add-Ins auf Ihrer SharePoint-Testwebsite durch und führt das Add-In sofort aus. (Weitere Informationen dazu, wie Endbenutzer ein installiertes SharePoint-Add-In ausführen, finden Sie unter  [Nächste Schritte](#Nextsteps).)
    
  
2. Wenn die Standardseite des Add-Ins geöffnet wird, wählen Sie den Link für **Neue Mitarbeiter in Seattle** aus, um die benutzerdefinierte Listeninstanz zu öffnen.
    
   **Standardseite und die Seite mit der Listenansicht**

  

     ![Die Standardseite des Add-Ins mit dem Titel "Neue Mitarbeiter nach Standort" wird angezeigt. Es gibt einen Link mit der Bezeichnung "Neue Mitarbeiter in Seattle". Ein Pfeil von diesem Link zeigt auf die Listenansichtsseite für die Liste. Diese hat den Titel "Neue Mitarbeiter in Seattle" und weist die folgende Liste auf.](images/9dc5cefe-083a-4807-bee6-473001f23db9.png)
  

    
    
  
3. Fügen Sie der Liste Elemente hinzu, und löschen Sie Elemente aus der Liste.
    
  
4. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.
    
  
5. Da Sie mit diesem Add-In und dieser Visual Studio-Projektmappe in anderen Artikeln arbeiten werden, hat es sich bewährt, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.
    
  

## 
<a name="Nextsteps"> </a>

Bisher gibt es nur wenige Informationen zur Orientierung in der Liste. Wir werden in späteren Artikeln dieser Reihe einige Informationen hinzufügen. Legen wir aber zunächst eine kurze Pause bezüglich der Codierung ein, um etwas über die Bereitstellung von SharePoint-Add-Ins in  [Bereitstellung und Installation eines von SharePoint gehosteten Add-Ins für SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md) zu erfahren.
  
    
    

