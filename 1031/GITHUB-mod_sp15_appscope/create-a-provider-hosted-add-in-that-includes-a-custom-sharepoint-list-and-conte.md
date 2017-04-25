---
title: Gewusst wie Erstellen eines von einem Anbieter gehosteten Add-Ins, das eine benutzerdefinierte SharePoint-Liste und einen benutzerdefinierten Inhaltstyp enthält
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# Gewusst wie: Erstellen eines von einem Anbieter gehosteten Add-Ins, das eine benutzerdefinierte SharePoint-Liste und einen benutzerdefinierten Inhaltstyp enthält
Erstellen Sie ein SharePoint-Add-In, das eine in der Cloud gehostete Webanwendung mit benutzerdefinierten, in SharePoint gehosteten Listenvorlagen, Listeninstanzen und benutzerdefinierten Inhaltstypen unter Verwendung der Office Developer Tools für Visual Studio 2012 kombiniert. Hier erfahren Sie, wie Sie mit SharePoint 2013-Add-In-Websites interagieren, indem Sie den REST/OData-Webdienst verwenden, und wie Sie OAuth in einem SharePoint-Add-In implementieren.
Die meisten klassischen SharePoint-Komponenten, wie benutzerdefinierte Inhaltstypen, benutzerdefinierte Listendefinitionen und Workflows, können einem in der Cloud gehosteten SharePoint-Add-In hinzugefügt werden. Das einfache Beispiel in diesem Artikel enthält Folgendes:





- Add-In-Website mit

  - einigen benutzerdefinierten Websitespalten


  - einem benutzerdefinierten Inhaltstyp, der die benutzerdefinierten Spalten verwendet


  - einer benutzerdefinierten Listenvorlage, die den benutzerdefinierten Inhaltstyp verwendet


  - einer Listeninstanz auf Grundlage der benutzerdefinierten Listendefinition


- Eine ASP.NET-Webanwendung, die Daten aus der Listeninstanz liest



## Voraussetzungen zum Erstellen dieses Add-Ins für SharePoint
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/de-de/download/details.aspx?id=30682) oder höher.


-  [Office Developer Tools](https://msdn.microsoft.com/de-de/office/aa905340.aspx)


- Eine Installation von SharePoint 2013 zum Testen und Debuggen

  - Diese Komponenten können sich auf dem selben Computer befinden, den Sie als Entwicklungscomputer verwenden. Sie können die Entwicklung jedoch auch mit einer Remoteinstallation von SharePoint 2013 durchführen. Wenn Sie mit einer Remoteinstallation arbeiten, müssen Sie das Clientobjektmodell so installieren, dass es für das Clientobjektmodell auf der Zielinstallation verteilbar ist. Es ist als verteilbares Paket im Microsoft Download Center verfügbar. Suchen Sie nach "SDKs für SharePoint Server 2013-Clientkomponenen" oder "SKDs für SharePoint Online-Clientkomponenten".


  - Die SharePoint-Testwebsite muss aus der Websitedefinition der **Entwicklerwebsite** (die Sie in der Zentraladministration erstellen können) erstellt werden.


  - Ihre Remote-Webanwendung kommuniziert mit der Add-In-Website entweder über JavaScript und die domänenübergreifende  [-Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md) oder über [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md). Wenn OAuth verwendet wird, wie im fortlaufenden Beispiel in diesem Artikel, muss die SharePoint 2013-Installation für die Verwendung von OAuth konfiguriert sein.



> **HINWEIS**
> Anleitungen zum Einrichten einer Entwicklungsumgebung, die Ihren Anforderungen entspricht, finden Sie unter  [Erste Schritte beim Erstellen von Apps für Office und SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 





### Wichtige Kernkonzepte für die Erstellung eines Add-Ins

Bevor Sie Ihr erstes Add-In erstellen, sollten Sie grundsätzlich wissen, was SharePoint-Add-Ins sind, und die Unterschiede zwischen in SharePoint gehosteten und in der Cloud gehosteten SharePoint-Add-Ins kennen. Die Artikel in Tabelle 1 vermitteln Ihnen dieses Wissen.




**Tabelle 1. Kernkonzepte für die Erstellung eines Add-Ins**


|**Artikeltitel**|**Beschreibung**|
|:-----|:-----|
| [SharePoint-Add-Ins](sharepoint-add-ins.md) <br/> |Hier finden Sie Informationen über das neue Add-In-Modell in SharePoint 2013, das es Ihnen ermöglicht, Add-Ins als kompakte, einfach zu verwendende Lösungen für Endbenutzer zu erstellen.  <br/> |
| [Wichtige Aspekte der Architektur und Entwicklungslandschaft von Add-Ins für SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |Hier finden Sie Informationen über Aspekte der Architektur von SharePoint-Add-Ins und das Modell für SharePoint-Add-Ins, einschließlich Add-In-Hostingoptionen, Benutzeroberflächenoptionen, Bereitstellungssystem, Sicherheitssystem und Lebenszyklus.  <br/> |
| [Auswählen von Mustern für die Entwicklung und das Hosting Ihres Add-Ins für SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Hier finden Sie Informationen über die verschiedenen Möglichkeiten zum Hosten von SharePoint-Add-Ins.  <br/> |
 

## Entwickeln des Add-Ins für SharePoint
<a name="Develop"> </a>

Mit den Schritten in diesem Abschnitt erstellen Sie auf Ihrem Entwicklungscomputer ein SharePoint-Add-In, das eine Add-In-Website mit SharePoint-Komponenten und eine Remote-Webanwendung umfasst.




### So richten Sie die Visual Studio 2012-Lösung und ihre Elemente ein


1. Erstellen Sie in Visual Studio 2012 ein **Add-In für SharePoint 2013**-Projekt im Knoten **Office SharePoint | Add-Ins** (entweder unter **C#** oder **Visual Basic**) in der Vorlagenstruktur des Assistenten **Neues Projekt**. Wählen Sie die Hostingoption **Anbietergehostet** aus. Im fortlaufenden Beispiel dieses Artikels wird C# als Sprache verwendet, undLocalTheater ist der Projektname.


2. Wählen Sie **Fertig stellen** im Assistenten aus.


3. Öffnen Sie im Manifestdesigner die Datei AppManifest.xml. Für das **Title**-Element wird der Projektname als Standardwert angezeigt. Ersetzen Sie ihn durch einen griffigeren Namen, denn dies ist der Name des Add-Ins, den der Benutzer in der Benutzeroberfläche sieht.


4. Geben Sie für **Name** einen Namen für das Add-In ein. Es handelt sich um einen internen Namen, der nur ASCII-Zeichen und keine Leerzeichen enthalten darf, wie z. B.LocalTheater.


5. Öffnen Sie die Datei Web.config im Webanwendungsprojekt, und fügen Sie das Element  `<customErrors mode="Off"/>` dem Element **system.web** hinzu.


6. Stellen Sie sicher, dass im Webanwendungsprojekt Verweise auf die folgenden Assemblys vorhanden sind. (Falls in Ihrer Edition von Visual Studio 2012 die Verweise nicht automatisch hinzugefügt wurden, fügen Sie sie jetzt hinzu.)

  - **Microsoft.IdentityModel.dll** Diese Assembly wird im globalen Assemblycache mit Windows Identity Foundation (WIF) installiert. Da es sich um eine .NET Framework 3.5-Assembly handelt, wird sie standardmäßig aus dem **Framework** des Dialogfelds **Verweis hinzufügen** herausgefiltert. Sie können einen Verweis auf sie hinzufügen, indem Sie direkt zum Verzeichnis `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` Ihres Entwicklungscomputers navigieren.


  - **Microsoft.IdentityModel.Extensions.dll** Sie können einen Verweis auf diese Assemby hinzufügen, indem Sie direkt zum Ordner `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` Ihres Entwicklungscomputers hinzufügen.


  - **System.IdentityModel.dll** Diese Assembly ist Teil von .NET Framework 4 und wird auf dem Knoten **Assemblies | Framework** des Dialogfelds **Verweis hinzufügen** angezeigt.


7. Wenn Ihre Remote-Webanwendung auf Informationen in der Hostwebsite sowie das Add-In-Web zugreift, müssen Sie der Datei „AppManifest.xml" ein **AppPermissionRequests**-Element mit einem oder mehreren untergeordneten **AppPermissionRequest**-Elementen hinzufügen. (Die Webanwendung im fortlaufenden Beispiel dieses Artikels greift nur auf die Add-In-Website zu. Add-In-Prinzipale verfügen automatisch über alle erforderlichen Berechtigungen für die Add-In-Website, sodass die Datei „AppManifest.xml" in dem Beispiel kein **AppPermissionRequests**-Element umfasst). Weitere Informationen über Add-In-Berechtigungsanforderungen und wie sie hinzugefügt werden, finden Sie unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).



### So fügen Sie SharePoint-Komponenten hinzu


1. Zum Hinzufügen von SharePoint-Komponenten zu einem Add-In gehen Sie gleich vor wie beim Hinzufügen von Komponenten zu einem klassischen Farmlösung. Es kann jedoch nicht jede Art von SharePoint-Komponente einem SharePoint-Add-In hinzugefügt werden. Die Zwecke, denen diese Komponenten dienen, werden in SharePoint-Add-Ins auf andere Weise erreicht. Ausführlichere Informationen darüber, welche Arten von SharePoint-Komponenten einem SharePoint-Add-In hinzugefügt werden können und wie sie einem Projekt hinzugefügt werden, finden Sie unter  [Typen von SharePoint-Komponenten, die in einem SharePoint-Add-In enthalten sein können](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).

    Verwenden Sie für das fortlaufende Beispiel die folgenden Verfahren. Diese geben Beispiele für die Verwendung von Visual Studio 2012 zum Hinzufügen von benutzerdefinierten Spalten, Inhaltstypen, Listenvorlagen und Listeninstanzen zu einem SharePoint-Add-In.

### So erstellen Sie benutzerdefinierte Spaltentypen


1. Fügen Sie im **Projektmappen-Explorer** ein SharePoint-Element **Websitespalte** zum SharePoint-Add-In-Projekt mit dem NamenActor hinzu.


2. Bearbeiten Sie in der Datei elements.xml für die neue Websitespalte das Element **Field** so, dass es die im folgenden Beispiel dargestellten Attribute und Werte aufweist, mit der Ausnahme, dass der Wert der GUID für das **ID**Attribut, den Visual Studio 2012 dafür generiert hat, nicht geändert wird. Vergessen Sie nicht die umschließenden Klammern "{}".

  ```

<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
  ```

3. Fügen Sie eine weitere **Websitespalte** zum Projekt mit dem NamenCastingStatus hinzu.


4. Bearbeiten Sie in der Datei elements.xml für die neue Websitespalte das Element **Field** so, dass es die im folgenden Beispiel dargestellten Attribute und Werte aufweist, mit der Ausnahme, dass der Wert der GUID für das Attribut **ID**, den Visual Studio 2012 dafür generiert hat, nicht geändert wird.

  ```

<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
  ```

5. Weil es sich um ein Auswahlfeld handelt, müssen Sie die Auswahlmöglichkeiten, die Reihenfolge ihrer Anzeige in der Dropdownliste, wenn ein Benutzer eine Auswahl trifft, sowie die Standardauswahl angeben. Fügen Sie dem Element **Field** das folgende untergeordnete Markup hinzu.

  ```

<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```


### So erstellen Sie den benutzerdefinierten Inhaltstyp


1. Fügen Sie im **Projektmappen-Explorer** ein SharePoint-Element **Inhaltstyp** zum SharePoint-Add-In-Projekt mit dem NamenActingRole hinzu. Wenn Sie vom Assistenten dazu aufgefordert werden, den Basisinhaltstyp auszuwählen, klicken Sie auf **Element** und dann auf **Fertig stellen**.


2. Wenn der Inhaltstypdesigner nicht automatisch geöffnet wird, wählen Sie den Inhaltstyp **ActingRole** im **Projektmappen-Explorer** aus, um ihn zu öffnen.


3. Öffnen Sie die Registerkarte **Inhaltstyp** im Designer, und füllen Sie die Textfelder wie folgt aus:

  - **Inhaltstypname**: ActingRole


  - **Beschreibung**: Stellt eine Rolle in einem Stück oder Film dar.


  - **Gruppenname**: Theater and Movies


4. Stellen Sie sicher, dass  *keines*  der Kontrollkästchen auf der Registerkarte aktiviert ist. Das Kontrollkästchen für **Erbt die Spalten vom übergeordneten Inhaltstyp** ist möglicherweise standardmäßig aktiviert. *Deaktivieren Sie es unbedingt.* 


5. Öffnen Sie die Registerkarte **Spalten** im Designer.


6. Verwenden Sie das Raster, um dem Inhaltstyp die zwei Websitespalten hinzuzufügen. Sie sind in der Dropdownliste nach ihren Anzeigenamen aufgeführt: **Actor/Actress** und **CastingStatus**. (Wenn sie nicht aufgeführt sind, haben Sie möglicherweise das Projekt nicht mehr gespeichert, nachdem Sie die benutzerdefinierten Websitespalten hinzufügt haben. Wählen Sie **Speichern** aus.)


7. Speichern Sie die Datei und schließen Sie den Designer.


8. Der nächste Schritt erfordert, dass Sie direkt im unformatierten XML des Inhaltstyps arbeiten. Wählen Sie daher im **Projektmappen-Explorer** die dem Inhaltstyp **ActingRole** untergeordnete Datei elements.xml aus.


9. Die Datei enthält bereits **FieldRef**-Elemente für die beiden Spalten, die Sie hinzugefügt haben. Fügen Sie **FieldRef**-Elemente für zwei integrierte SharePoint 2013-Spalten als übergeordnete Elemente der beiden bereits vorhandenen hinzu. Nachfolgend dargestellt ist das Markup für die Elemente.  *Sie müssen diese GUIDs für die ID-Attribute verwenden, da diese integrierte Feldtypen mit festen IDs sind.*  Fügen Sie sie *über*  den beiden **FieldRef**-Elementen für die benutzerdefinierten Websitespalten hinzu.

  ```

<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  ```


    Beachten Sie, dass wir diesen Feldern einen benutzerdefinierten Anzeigenamen zugewiesen haben: **Character**, im Sinne eines Charakters in einem Stück oder Film.



### So erstellen Sie die benutzerdefinierte Listenvorlage und eine Listeninstanz


1. Fügen Sie ein SharePoint-Element **Liste** zum SharePoint-Add-In-Projekt mit dem NamenCharactersInShow hinzu. Lassen Sie auf der Seite **Listeneinstellungen auswählen** im **Assistenten zum Anpassen von SharePoint** den Listenanzeigenamen auf dem Standardwert **CharactersInShow**, aktivieren Sie das Optionsfeld **Anpassbare Liste erstellen basierend auf:** und wählen Sie in der Dropdownliste **Standard (leer)** aus. Klicken Sie dann auf **Fertig stellen**.


2. Wenn Sie den Assistenten abschließen, wird eine Listenvorlage **CharactersInShow** mit einer untergeordneten Listeninstanz namens **CharactersInShowInstance** erstellt. Möglicherweise wurde standardmäßig ein Listen-Designer geöffnet. Er wird in einem späteren Schritt verwendet.


3. Öffnen Sie die untergeordnete Datei elements.xml der Listenvorlage **CharactersInShow** ( *nicht*  die untergeordnete Datei elements.xml von **CharactersInShowInstance**).


4. Fügen Sie dem Attribut **DisplayName** Leerzeichen hinzu, damit es besser aussieht:"Characters In Show".


5. Legen Sie das Attribut **Description** auf"Characters in a play or movie" fest.


6. Lassen Sie alle anderen Attribute auf dem Standardwert, speichern Sie die Datei und schließen Sie sie.


7. Wenn der Listen-Designer nicht geöffnet wurde, wählen Sie den Knoten **CharactersInShow** im **Projektmappen-Explorer** aus.


8. Öffnen Sie die Registerkarte **Spalten** im Designer und klicken Sie dann auf die Schaltfläche **Inhaltstypen**.


9. Fügen Sie im Dialogfeld **Einstellungen für Inhaltstypen** den Inhaltstyp **ActingRole** hinzu.


10. Wählen Sie in der Liste der Typen den Inhaltstyp **ActingRole** aus und klicken Sie auf die Schaltfläche **Als Standard festlegen**.

    Wählen Sie den Inhaltstyp **Element** aus, klicken Sie mit der rechten Maustaste auf die kleine Pfeilspitze, die links vom Inhaltstypnamen angezeigt wird, und klicken Sie dann auf **Löschen**.


11. Wiederholen Sie den vorherigen Schritt für den Inhaltstyp **Ordner**, sodass **ActingRole** als einziger Inhaltstyp angezeigt wird. Klicken Sie auf **OK**, um das Dialogfeld zu schließen.


12. In der Liste der Spalten sind jetzt drei Spalten vorhanden. Wählen Sie **Titel** aus, klicken Sie mit der rechten Maustaste auf die kleine Pfeilspitze, die links vom Inhaltstypnamen angezeigt wird, und klicken Sie dann auf **Löschen**. Jetzt sollten nur zwei Spalten angezeigt werden, **Actor/Actress** und **Casting Status**.


13. Öffnen Sie die Registerkarte **Liste** des Designers. Diese Registerkarte wird verwendet,um bestimmte Werte für die Liste *Instanz*  , nicht für die Liste *Vorlage*  festzulegen.


14. Ändern Sie die Werte auf dieser Registerkarte wie folgt:

  - **Titel**: Characters in Hamlet


  - **Listen-URL**: Lists/CharactersInHamlet


  - **Beschreibung**: Die Charaktere in Hamlet und Casting-Informationen.



    Lassen Sie die Kontrollkästchen auf ihrer Standardeinstellung, speichern Sie die Datei und schließen Sie den Designer.


15. Die Listeninstanz hat möglicherweise ihren alten Namen im **Projektmappen-Explorer** beibehalten. Wenn dies der Fall ist, öffnen Sie das Kontextmenü für **CharactersInShowInstance**, wählen **Umbenennen** aus und ändern den Namen inCharactersInHamlet.


16. Öffnen Sie die Datei schema.xml.


17. In der Datei können zwei **ContentType**-Elemente vorhanden sein, eines mit dem **Name**-Attributwert von ActingRole und eines mit dem Namen **ListFieldsContentType**. Nur das mit dem Namen ActingRole ist relevant, löschen Sie daher alle anderen **ContentType**-Elemente.

    > **HINWEIS**
      > Möglicherweise befindet sich kein Zeilenumbruch zwischen den **ContentType**-Elementen, sodass es zunächst aussieht, als sei nur ein Element vorhanden. Scrollen Sie nach rechts und suchen Sie sorgfältig nach weiteren Elementen. 
18. Für das Element **Fields** müssten zwei **Field**-Elemente vorhanden sein (die in einer Zeile stehen, wenn sich zwischen ihnen kein Zeilenumbruch befindet). Eines sollte das Element **Field** in der Datei elements.xml der Websitespalte **Actor** genau duplizieren, das andere sollte das Element **Field** in der Datei elements.xml der Websitespalte **CastingStatus** genau duplizieren. Wenn einschließlich aller untergeordneten Elemente (wie z. B. die Elemente **CHOICES** und **MAPPINGS**) keine genaue Übereinstimmung vorhanden ist, kopieren Sie das Element **Field** in der Datei elements.xml der Websitespalte und fügen es anstelle des nicht übereinstimmenden Elements **Field** in die Datei schema.xml ein.


19. Ersetzen Sie dann in der Datei schema.xml im Element **View**, dessen BaseViewID-Wert "0" ist, das vorhandene Element **ViewFields** durch das folgende Markup. (Verwenden Sie genau diese GUID für das **FieldRef**-Element mit dem Namen  `LinkTitle`.)

  ```

<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
  ```

20. Ersetzen Sie die beiden fehlenden ID-Attributwerte durch die GUIDs in den Dateien elements.xml der entsprechenden Websitespalten. Vergessen Sie nicht die umschließenden Klammern "{}".


21. Ersetzen Sie dann in der Datei schema.xml im Element **View**, dessen BaseViewID-Wert "1" ist, das vorhandene Element **ViewFields** durch das folgende Markup. (Verwenden Sie genau diese GUID für das **FieldRef**-Element mit dem Namen  `LinkTitle`.)

  ```

<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
  ```

22. Kopieren Sie die beiden **FieldRef**-Elemente für  `Actor` und `CastingStatus`, die Sie in der vorherigen Ansicht diesem **ViewFields**-Element als gleichgeordnete Elemente von  `LinkTitle` **FieldRef** hinzugefügt haben.


23. Speichern und schließen Sie die Datei schema.xml.


24. Öffnen Sie die Datei elements.xml, die ein untergeordnetes Element der Listeninstanz **CharactersInHamlet** ist.


25. Geben Sie in die Liste einige Anfangsdaten ein, indem Sie das folgende Markup als untergeordnetes Element des **ListInstance**-Elements hinzufügen.

  ```

<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
  ```

2. Wählen Sie im **Projektmappen-Explorer** die Option **Feature1** aus, um den Feature-Designer zu öffnen. Im Designer legen Sie für den **Titel**Theater and Movie Data Components und für die **Beschreibung**Websitespalten, Inhaltstypen und Listeninstanzen für Daten zu Theater und Film fest. Speichern Sie die Datei und schließen Sie den Designer.


3. Wenn **Feature1** im **Projektmappen-Explorer** nicht umbenannt wurde, öffnen Sie das zugehörige Kontextmenü, wählen **Umbenennen** aus und benennen die Option inTheaterAndMovieDataComponents um.



### So codieren Sie das Remote-Webanwendungsprojekt


- Entwickeln Sie die Webanwendung wie jede andere Webanwendung für Ihren bevorzugten Plattformstapel. Bei einem Microsoft-Stapel können Sie entweder den REST/OData-Webdienst oder eines der Clientobjektmodelle in SharePoint 2013 verwenden. Bei einem Nicht-Microsoft-Stapel können Sie die REST/OData-Endpunkte in SharePoint 2013 verwenden, um Erstellungs-, Lese-, Aktualisierungs- und Löschoperationen (CRUD: Create, Read, Update and Delete) in der Add-In-Website durchzuführen.

    > **HINWEIS**
      > Wenn Sie dem Webanwendungsprojekt in Visual Studio einen Verweis auf eine Assembly hinzufügen, legen Sie die Eigenschaft **Lokale Kopie** der Assembly auf **True** fest, es sei denn, Sie wissen, dass die Assembly bereits auf dem Webserver installiert ist, oder Sie können sicherstellen, dass sie installiert wird, bevor Sie Ihr Add-In bereitstellen. .NET Framework wird auf Microsoft Azure-Webrollen und Azure-Websites installiert. Die SharePoint 2013-Clientassemblys und die verschiedenen verwalteten Codeerweiterungen und Foundations von Microsoft werden jedoch nicht installiert. Office Developer Tools für Visual Studio 2012 fügt automatisch Verweise auf einige häufig in SharePoint-Add-Ins verwendete Assemblys hinzu und legt die Eigenschaft **Lokale Kopie** fest.

    In dem fortlaufenden Beispiel entwickeln Sie eine ASP.NET-Webanwendung. Führen Sie die folgenden Schritte aus.

1. Öffnen Sie die Datei Default.aspx und ersetzen Sie das Textelement der Datei durch das folgende Markup. Das Markup fügt eine Schaltfläche **Get the Cast (Besetzung abrufen)** hinzu, welche, wenn sie ausgewählt wird, die Liste **Characters in Hamlet** in der Add-In-Website liest und deren Daten in einem [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) -Steuerelement darstellt, das erst nach dem Drücken der Schaltfläche angezeigt wird.

  ```HTML

<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
  ```

2. Öffnen Sie die Datei Default.aspx.cs und fügen Sie die folgenden **using**-Anweisungen hinzu.

  ```cs

using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
  ```


    Die letzte dieser Anweisungen bezieht sich auf den Namespace, der in der Datei TokenHelper.cs deklariert ist.


3. Fügen Sie der **Default**-Klasse die folgenden Felder hinzu.

  ```cs

SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
  ```

4. Ersetzen Sie die **Page_Load**-Methode durch den folgenden Code, der die **TokenHelper**-Klasse verwendet, um Token von dem OAuth-kompatiblen sicheren Token-Server abzurufen. Der Zugriffstoken wird dann in der Eigenschaft  [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) der Schaltfläche zum späteren Abrufen durch den Click-Ereignishandler der Schaltfläche gespeichert.

  ```cs

protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
  ```

5. Fügen Sie der **Default**-Klasse den folgenden Ereignishandler hinzu. Der Handler beginnt, den in der Eigenschaft  [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) der Schaltfläche gespeicherten Zugriffstoken abzurufen.

  ```cs

protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
  ```

6. Der Handler muss die geänderte Add-In-Web-URL auf Postbacks neu beziehen. Fügen Sie daher den folgenden Code hinzu.

  ```cs

if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
  ```

7. Fügen Sie die folgende Zeile hinzu, die einen der SharePoint 2013-REST/OData-Endpunkte verwendet, um Listendaten abzurufen. In diesem Beispiel liest der Code die Liste **Characters in Hamlet**, die für die Add-In-Website bereitgestellt wird. Die APIs für diesen Dienst machen es einfach, in einer einzelnen Codezeile eine Liste auszuwählen und drei Felder in der Liste zum Zurückgeben festzulegen. Beachten Sie, dass Sie in der OData-URL den internen Namen der Felder (Spalten) und nicht den Anzeigenamen angeben müssen, damit der Code  `Title`,  `Actor` sowie `CastingStatus` verwendet und nicht `Character`,  `Actor/Actress` und `Casting Status.` Weitere Informationen über den REST/OData-Webdienst finden Sie unter [Verwenden von OData-Abfragevorgängen in SharePoint REST-Anforderungen](use-odata-query-operations-in-sharepoint-rest-requests.md).

  ```cs

// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
  ```

8. Fügen Sie den folgenden Code hinzu, der die Klassen  [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) und [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) des Namespace [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) verwendet, um die HTTP-Anforderungs- und Antwortobjekte zu erstellen.

  ```cs

// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
  ```

9. Fügen Sie den folgenden Code hinzu, um das ATOM-formatierte Antwort-XML zu analysieren. Er verwendet die Klassen des Namespace  [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) , um die zurückgegebenen Daten zu analysieren und eine [List<T>](http://msdn2.microsoft.com/DE-DE/library/6sh2ey19) der Elemente aus der SharePoint-Liste zu erstellen. (Sie könnten auch die Klassen des Namespace [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) verwenden.) Beachten Sie, dass in dem von SharePoint zurückgegebenen XML die untergeordneten Elemente des **entry**-Elements die Metadaten zu dem Listenelement enthalten. Die tatsächlichen Zeilendaten eines SharePoint-Listenelements sind zwei Schichten weiter unten im **properties**-Element geschachtelt. Aus diesem Grund wird die Erweiterungsmethode  [Elements<T>](http://msdn2.microsoft.com/DE-DE/library/bb348465) zweimal verwendet, um die höheren Ebenen herauszufiltern.

  ```cs

// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
  ```

10. Fügen Sie die folgende LINQ-Abfrage hinzu, um eine  [IEnumerable<T>](http://msdn2.microsoft.com/DE-DE/library/9eekhta0) Sammlung eines anonymen Typs zu erstellen, die nur die Eigenschaften besitzt, die Sie benötigen. Beachten Sie, dass obwohl der Code durch seinen internen `Title`-Namen auf das Elementfeld Titel verweisen muss, der Eigenschaftenname in dem anonymen Typ, dem der Wert zugewiesen wird, als  `Character` benannt werden kann. Ein Effekt davon ist, dass der besser geeignete Name **Character** auf der Seite angezeigt wird, wenn die Sammlung an ein Rastersteuerelement gebunden wird.

  ```cs

var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

  ```

11. Schließen Sie den Handler mit dem folgenden Code ab, um die Daten an ein  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) -Steuerelement auf der Seite zu binden. Die Spaltenüberschriften in dem Steuerelement werden standardmäßig als die Eigenschaftsnamen des anonymen Typs angezeigt: `Character`,  `Actor` und `CastingStatus`. Das  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) -Steuerelement besitzt Eigenschaften, die es Ihnen ermöglichen, die Überschriften der Namens- und Formatierungsspalten zu steuern, sodass sie **Actor/Actress** und **Casting Status** lauten können, um mit den Spaltenüberschriften in SharePoint übereinzustimmen. Diese Methoden werden der Einfachheit halber hier nicht beschrieben. (Sie könnten auch ein [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) -Steuerelement verwenden.)

  ```cs

GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

  ```

12. Speichern Sie alle Dateien.



### So testen und debuggen Sie das Add-In für SharePoint


1. Um das SharePoint-Add-In und seine Remote-Webanwendung zu testen, drücken Sie in Visual Studio 2012 die Taste F5. Die Webanwendung wird für IIS Express unter localhost bereitgestellt. Das SharePoint-Add-In wird auf der SharePoint-Zielwebsite installiert. (Im fortlaufenden Beispiel versucht die Remote-Anwendung  *nicht*  , mit der *Host*  -Website zu interagieren, und der Add-In-Prinzipal verfügt automatisch über Berechtigungen für die *Add-In*  -Website, daher werden Sie *nicht*  zum Erteilen von Berechtigungen aufgefordert.) Die Seite **Websiteinhalt** Ihrer SharePoint-Zielwebsite wird geöffnet und das dort aufgeführte neue Add-In wird angezeigt.


2. Wenn Sie das SharePoint-Add-In auswählen, wird die Remote-Webanwendung auf der Seite geöffnet, die Sie im **StartPage**-Element in der Datei AppManifest.xml festgelegt haben. Verwenden Sie die Webanwendung nach Bedarf, um zu überprüfen, ob sie funktioniert. Im fortlaufenden Beispiel in diesem Thema klicken Sie einfach auf die Schaltfläche. Dadurch wird eine Tabelle erstellt und mit der Liste **Characters in Hamlet** der Add-In-Website ausgefüllt.



## Veröffentlichen des Add-Ins für SharePoint
<a name="Publish"> </a>

Um Ihr SharePoint-Add-In zu veröffentlichen, laden Sie das Add-In-Paket in einen Unternehmens-Add-In-Katalog oder den Office Add-In-Store. Weitere Informationen finden Sie unter  [Veröffentlichen im Office Store oder im Add-In-Katalog eines Unternehmens](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog) und [Veröffentlichen von SharePoint-Add-Ins](publish-sharepoint-add-ins.md).




## Problembehandlung
<a name="Troubleshooting"> </a>

Wenn das Add-In nicht funktioniert, sollten Sie überlegen, ob nicht ein Fehler im CAML-Markup die Bereitstellung der SharePoint-Komponenten blockiert. Verwenden Sie zum Überprüfen der Bereitstellung ein ähnliches Verfahren wie das folgende, das auf dem fortlaufenden Beispiel basiert.




### So testen Sie die Bereitstellung der Add-In-Website


1. Öffnen Sie die Seite **Websiteeinstellungen** der Hostwebsite. Klicken Sie im Abschnitt **Websitesammlungsverwaltung** auf den Link **Websitehierarchie**.


2. Auf der Seite **Websitehierarchie** wird das Add-In nach seiner URL aufgeführt. Starten Sie es nicht. Kopieren Sie stattdessen die URL, und verwenden Sie die URL in den verbleibenden Schritten.


3. Greifen Sie auf " _URL_von_App-Web_/_layouts/15/ManageFeatures.aspx" zu, und überprüfen Sie auf der Seite **Websitefeatures**, die geöffnet wird, ob die **Theater and Movie Data Components** in der alphabetischen Liste der Features in Ihrem SharePoint-Add-In aufgeführt werden und ob ihr Status **Aktiv** ist.


4. Greifen Sie auf " _URL_von_App-Web_/_layouts/15/mngfield.aspx" zu, und überprüfen Sie auf der Seite **Websitespalten**, die geöffnet wird, ob eine Gruppe **Theater and Movies** in der Liste der Websitespalten vorhanden ist und ob sie Ihre neuen benutzerdefinierten Websitespalten, **Actor/Actress** und **Casting Status**, enthält.


5. Greifen Sie auf " _URL_von_App-Web_/_layouts/15/mngctype.aspx" zu, und überprüfen Sie auf der Seite **Websiteinhaltstypen**, die geöffnet wird, ob eine Gruppe **Theater and Movies** in der Liste der Inhaltstypen vorhanden ist und ob sie Ihren neuen Inhaltstyp **ActingRole** enthält.


6. Klicken Sie auf den Inhaltstyp **ActingRole**. Auf der Seite **Websiteinhaltstyp**, die geöffnet wird, und überprüfen Sie, ob der Inhaltstyp die zwei neuen Websitespaltentypen, **Actor/Actress** und **Casting Status** umfasst, und ob das Elementfeld Titel mit ihrem benutzerdefinierten Anzeigenamen benannt wurde: **Character**.


7. Greifen Sie auf " _URL_von_App-Web_/_layouts/15/mcontent.aspx" zu, und überprüfen Sie auf der Seite **Websitebibliotheken und -listen**, die geöffnet wird, ob ein Link **"Characters in Hamlet" anpassen** vorhanden ist.


8. Klicken Sie auf den Link **"Characters in Hamlet" anpassen** und überprüfen Sie auf der Seite der Listeneinstellungen, ob der einzige Inhaltstyp für diese Liste Ihr benutzerdefinierter Inhaltstyp **ActingRole** ist und ob Ihre zwei neuen Websitespalten, **Actor/Actress** und **Casting Status**, im Abschnitt **Spalten** aufgeführt werden. (Die Titelspalte wird möglicherweise mit ihrem internen Namen **Titel** anstatt mit dem von Ihnen angegebenen Anzeigenamen **Character** angezeigt.)

    > **HINWEIS**
      > Falls auf der Seite kein Abschnitt **Inhaltstypen** dieser Art vorhanden ist, müssen Sie die Verwaltung der Inhaltstypen aktivieren. Klicken Sie auf den Link **Erweiterte Einstellungen**, und aktivieren Sie auf der Seite **Erweiterte Einstellungen** die Verwaltung der Inhaltstypen. Klicken Sie anschließend auf **OK**. Die vorherige Seite wird wieder angezeigt, und es ist ein Abschnitt **Inhaltstypen** vorhanden.
9. Am oberen Seitenrand befindet sich die **Webadresse** der Liste. Kopieren Sie die Adresse, fügen Sie sie in die Adressleiste Ihres Browsers ein und navigieren Sie dann zu der Liste. Überprüfen Sie, ob in der Liste die von Ihnen erstellten Beispielelemente vorhanden sind. (Die Titelspalte kann mit ihrem internen Namen **Title** anstatt mit dem von Ihnen angegebenen Anzeigenamen **Character** angezeigt werden.)



## Nächste Schritte
<a name="NextSteps"> </a>

In diesem Artikel wurde beschrieben, wie ein einfaches hybrides SharePoint-Add-In mit einer Remote-Webanwendung erstellt wird. Die nächsten Schritte könnten sein:




- Hinzufügen von vollständiger CRUD-Funktionalität zum Add-In mit den REST/OData-Endpunkten oder einem der Clientobjektmodelle. Weitere Informationen finden Sie unter  [Verwenden von OData-Abfragevorgängen in SharePoint REST-Anforderungen](use-odata-query-operations-in-sharepoint-rest-requests.md) und [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md).


- Lokalisieren Ihres SharePoint-Add-In für andere Kulturen. Weitere Informationen finden Sie unter  [Lokalisieren von Add-Ins für SharePoint](localize-sharepoint-add-ins.md).


- Erstellen eines Windows Phone Companion-Add-Ins, das die Funktionalität der Remote-Webanwendung dupliziert. Weitere Informationen finden Sie unter  [Erstellen von mobilen Add-Ins für SharePoint 2013](http://msdn.microsoft.com/de-de/library/office/jj163228.aspx).



## Weitere Informationsquellen
<a name="SP15createcloud_bk_addlresources"> </a>


-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)



