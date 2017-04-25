---
title: Hinzufügen des benutzerdefinierten clientseitigen Renderings für ein von SharePoint-gehostetes SharePoint Add-In
ms.prod: SHAREPOINT
ms.assetid: 6e0e530a-7d8b-48ab-8d0c-a878ddbf5be1
---


# Hinzufügen des benutzerdefinierten clientseitigen Renderings für ein von SharePoint-gehostetes SharePoint Add-In
Erfahren Sie, wie Sie das Rendering und Überprüfen von Steuerelementen in SharePoint-Add-Ins-Seiten anpassen.
Dies ist der achte in einer Reihe von Artikeln über die Grundlagen der Entwicklung von SharePoint gehosteter SharePoint-Add-Ins. Sie sollten sich zuerst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den vorherigen Artikeln in dieser Reihe vertraut machen:





-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Bereitstellung und Installation eines von SharePoint gehosteten Add-Ins für SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Hinzufügen von benutzerdefinierten Spalten zu einem von SharePoint gehosteten Add-In für SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Hinzufügen eines benutzerdefinierten Inhaltstyps zu einem von SharePoint gehosteten Add-In für SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Hinzufügen eines Webparts zu einer Seite in einem von SharePoint gehosteten Add-In für SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Hinzufügen eines Workflows zu einem von SharePoint gehosteten Add-In für SharePoint](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Hinzufügen einer benutzerdefinierten Seite und Formatvorlage zu einem von SharePoint gehosteten Add-In für SharePoint](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)



> **HINWEIS**
> Wenn Sie diese Reihe zu von SharePoint gehosteten Add-Ins durchgearbeitet haben, haben Sie eine Visual Studio-Projektmappe, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_SP-Hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) herunterladen und die Datei „BeforeClientRenderedControl.sln" öffnen.




Sie können etwas clientseitiges JavaScript verwenden, um das Rendering von Webparts, der meisten Typen von Feldern (Spalten) und anderer Steuerelemente anzupassen, indem Sie eine JavaScript-Datei zur Eigenschaft **JSLink** des Steuerelements zuweisen, z. B. **SPField.JSLink**. Sie können auch clientseitige Validierungslogik auf diese Weise hinzufügen. In diesem Artikel passen Sie das Rendering eines Felds in einer Liste des SharePoint-Add-Ins für die Mitarbeitereinführung mithilfe von clientseitigem Rendering an.
> **HINWEIS**
> Wenn der Endbenutzer JavaScript in seinem Browser deaktiviert hat, greift SharePoint auf das serverseitige Rendering und Validieren zurück. 





> **HINWEIS**
> The JSLink property is not supported on Survey or Events lists. A SharePoint calendar is an Events list. 





## Erstellen und Registrieren des JavaScript






1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Knoten **Skripts**, und wählen Sie **Hinzufügen** > **Neues Element** > **Web** aus.


2. Wählen Sie **JavaScript-Datei**, und nennen Sie sie OrientationStageRendering.js.


3. Ihr benutzerdefiniertes Rendern des Felds sollte automatisch erfolgen. Fügen Sie daher eine anonyme Methode zum JavaScript hinzu, die automatisch ausgeführt wird, wenn die Datei mit dem folgenden Code geladen wird.

  ```
(function () {

})();
  ```

4. Fügen Sie im Textkörper dieser Methode (zwischen den {}-Zeichen) den folgenden Code zum Erstellen von JSON-Objekten (Javascript Object Notation) für den Renderingüberschreibungskontext, die Vorlagen in dem Kontext und die Vorlagen für die Felder hinzu.

  ```
var customRenderingOverride = {};
customRenderingOverride.Templates = {};
customRenderingOverride.Templates.Fields = {

}
  ```

5. Fügen Sie im Textkörper des  `Fields`-Vorlagenobjekts das folgende JSON-Objekt ein. Der Name der Eigenschaft  `OrientationStage` identifiziert das Feld, mit dem das Rendering angepasst wurde. Der Wert der Eigenschaft ist ein weiteres JSON-Objekt. Die Eigenschaft `View` gibt den Seitenkontext an, in dem das benutzerdefinierte Rendering angewendet wird. In diesem Fall teilt das Objekt SharePoint mit, das benutzerdefinierte Rendering auf Listenansichten zu verwenden. (Weitere Optionen sind die Formulare zum Bearbeiten, für neue Element und zum Anzeigen.) Der Wert der Eigenschaft `renderOrientationStage` ist der Name der benutzerdefiniertes Renderingmethode, die Sie in einem späteren Schritt erstellen.

  ```
"OrientationStage": { "View": renderOrientationStage }
  ```

6. Die letzte Aktion, die die anonyme Methode durchführen muss, besteht darin, den Vorlagen-Manager von SharePoint die Renderingüberschreibung zu informieren. Fügen Sie die folgende Zeile am Ende des Hauptteils der Methode hinzu.

  ```
  SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
  ```


    Die Methode sollte jetzt wie folgt aussehen.



  ```
  (function () {
    var customRenderingOverride = {};
    customRenderingOverride.Templates = {};
    customRenderingOverride.Templates.Fields = {
        "OrientationStage": { "View": renderOrientationStage }
    }

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
})();
  ```

7. Fügen Sie die folgende Methode zur Datei hinzu. Sie legt die Farbe des Spaltenwerts **Einführungsphase** auf Rot fest, wenn der Wert „Nicht gestartet" ist, und auf Grün, wenn der Wert „Abgeschlossen" ist. (Das `ctx`-Objekt ist ein Clientkontextobjekt, das vom SharePoint-Skript im Lieferumfang deklariert wird.)

  ```
function renderOrientationStage(ctx) {
    var orientationStageValue = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];
    if (orientationStageValue == "Not Started")  {
        return "<span style='color:red'>" + orientationStageValue + "</span>"
    }
    else if (orientationStageValue == "Completed") {
        return "<span style='color:green'>" + orientationStageValue + "</span>"
    }
    else {
        return orientationStageValue;
    }
}
  ```

8. Erweitern Sie im **Projektmappen-Explorer** **Websitespalten** und dann **Einführungsphase**, und öffnen Sie dann die Datei elements.xml.


9. Um SharePoint mitzuteilen, Ihr benutzerdefiniertes JavaScript zu verwenden, fügen Sie das neue Attribut **JSLink** zum Element **Field** hinzu, und weisen dann die folgende URL als Wert zu: `~site/Scripts/OrientationStageRendering.js`.

    > **HINWEIS**
      > Die Eigenschaft **JSLink** ist immer eine Datei, keine Methode. Es gibt keine Möglichkeit, SharePoint mitzuteilen, welche Methode ausgeführt werden soll. Aus diesem Grund enthält die Datei eine Methodet, die automatisch ausgeführt wird.

    Das Start-Tag für das Element **Field** sieht nun wie folgt aus.



  ```
<Field
       ID="{some_guid_here}"
       Name="OrientationStage"
       Title="OrientationStage"
       DisplayName="Orientation Stage"
       Description="The current orientation stage of the employee."
       Type="Choice"
       Required="TRUE"
       Group="Employee Orientation" 
       JSLink="~site/Scripts/OrientationStageRendering.js">
<!-- child elements and end tag omitted -->
  ```

10. Öffnen Sie die Seite Default.aspx, und fügen Sie den folgenden Code als letztes untergeordnetes Element des **asp:Content**-Elements hinzu, dessen **ContentPlaceHolderID** auf **PlaceHolderMain** festgelegt ist.

  ```XML
<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Lists/NewEmployeesInSeattle/AllItems.aspx';"
    Text="List View Page for New Employees in Seattle" /></p>
  ```


## Ausführen und Testen des Add-Ins






1. Verwenden Sie die F5-TASTE, um Ihr Add-In bereitzustellen und auszuführen. Visual Studio führt eine temporäre Installation des Add-Ins auf Ihrer SharePoint-Testwebsite durch und führt das Add-In sofort aus. 


2. Das clientseitige Rendering, das Sie konfiguriert haben, wirkt sich auf das Rendering des Felds nur in der Listenansichtsseite, aber nicht im Listenansicht-Webpart aus, das wir auf der Startseite abgelegt haben. Der Grund hierfür ist, dass das Webpart standardmäßig das serverseitige Rendering verwendet. Es gibt Möglichkeiten, dies umzukehren, aber diese zu fortgeschritten für dieses einfache Beispiel. Um also das clientseitige Rendering in Aktion zu sehen, klicken Sie auf den Link am unteren Rand der Seite **Listenansichtsseite für neue Mitarbeiter in Seattle**.


3. Wenn die Listenansichtsseite geöffnet wird, legen Sie den Wert **Einführungsphase** für einige Elemente auf **Nicht gestartet** und für andere auf **Abgeschlossen** fest, um das benutzerdefinierte Farbrendering anzuzeigen.

   **Liste mit benutzerdefiniertem clientseitigem Rendering**



![Liste "Neue Mitarbeiter in Seattle" mit den Orientierungsphasewerten "Nicht gestartet" in Rot und "Abgeschlossen" in Grün. Andere Werte in Schwarz.](images/dc8e2b7d-1747-4b65-aab4-6fc93c6867d4.PNG)





4. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.


5. Da Sie mit diesem Add-In und dieser Visual Studio-Projektmappe in anderen Artikeln arbeiten werden, hat es sich bewährt, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.



## 
<a name="Nextsteps"> </a>

Im nächsten Artikel dieser Reihe fügen Sie ein benutzerdefiniertes Menüelement und eine benutzerdefinierte Schaltfläche zum Menüband im SharePoint-Add-In hinzu:  [Erstellen einer benutzerdefinierten Menübandschaltfläche im Hostweb eines SharePoint-Add-Ins](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md).




