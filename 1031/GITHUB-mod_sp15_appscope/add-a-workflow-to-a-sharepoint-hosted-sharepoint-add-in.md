---
title: Hinzufügen eines Workflows zu einem von SharePoint gehosteten Add-In für SharePoint
ms.prod: SHAREPOINT
ms.assetid: b33a61d7-5bc9-4720-97a5-8475d9a0d7c5
---


# Hinzufügen eines Workflows zu einem von SharePoint gehosteten Add-In für SharePoint
Erfahren Sie, wie Sie einen Workflow in ein SharePoint-Add-In einschließen.
Dies ist der sechste einer Reihe von Artikeln über die Grundlagen der Entwicklung von SharePoint gehosteter SharePoint-Add-Ins. Machen Sie sich zunächst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den vorherigen Artikeln dieser Reihe vertraut:





-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Bereitstellung und Installation eines von SharePoint gehosteten Add-Ins für SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Hinzufügen von benutzerdefinierten Spalten zu einem von SharePoint gehosteten Add-In für SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Hinzufügen eines benutzerdefinierten Inhaltstyps zu einem von SharePoint gehosteten Add-In für SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Hinzufügen eines Webparts zu einer Seite in einem von SharePoint gehosteten Add-In für SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)



> **HINWEIS**
> Wenn Sie diese Reihe zu von SharePoint gehosteten Add-Ins durchgearbeitet haben, haben Sie eine Visual Studio-Lösung, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_SP-Hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) herunterladen und die Datei „BeforeWorkflow.sln" öffnen.




In diesem Artikel fügen Sie einen Workflow zur SharePoint-Add-In „Employee Orientation" hinzu, der die Personalabteilung (HR) benachrichtigt, dass ein neuer Mitarbeiter bereit ist, die Personalpapiere auszufüllen.
## Hinzufügen eines Workflows zu einem Add-In






1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** und **Neuer Ordner** aus. Nennen Sie den OrdnerWorkflows.


2. Klicken Sie mit der rechten Maustaste auf den neuen Ordner, und wählen Sie **Hinzufügen** und **Neues Element** aus. Das Dialogfeld **Neues Element hinzufügen** wird mit dem Knoten **Office/SharePoint** geöffnet.


3. Wählen Sie **Workflow** aus, und geben Sie dem Workflow den NamenHR_Intake. Wenn Sie aufgefordert werden, den Workflowtyp auszuwählen, wählen Sie **Listenworkflow** und dann **Weiter** aus.


4. Aktivieren Sie auf der nächsten Seite des Assistenten die Option **Ja, Zuordnung durchführen...**aus, und legen Sie dann die Dropdownfelder auf die folgenden Werte fest:

  - **Die Bibliothek oder Liste zum Verknüpfen mit Ihrem Workflow**

    Neue Mitarbeiter in Seattle


  - **Die Verlaufsliste...**

    <Neu erstellen>


  - **Die Aufgabenliste...**

    <Neu erstellen>



    Klicken Sie auf **Weiter**.


5. Aktivieren Sie auf der letzten Seite des Assistenten die Option zum automatischen Starten des Workflows  *nur*  , wenn ein Element *geändert*  wird.


6. Wählen Sie **Fertig stellen** aus.

    Von den Office-Entwicklertools für Visual Studio wird dann Folgendes ausgeführt:

  - Erstellen eines HR_Intake-Workflows im Ordner **Workflows** mit einer untergeordneten Workflow.xaml-Datei, die im Workflow-Designer geöffnet ist


  - Erstellen einer **WorkflowTaskList**-Listeninstanz, in der Aufgaben, die Teil des Workflows sind, erstellt und aktualisiert werden


  - Erstellen einer **WorkflowHistoryList**-Listeninstanz, die ein Protokoll der verschiedenen stattfindenden Schritte bei jeder Ausführung des Workflows ist


7. Ziehen Sie die zwei neuen Listeninstanzen in den Ordner **Listen**.



## Entwerfen des Workflows

Der Workflow sendet eine E-Mail, um einen Mitarbeiter der Personalabteilung zu benachrichtigen, dass der neue Mitarbeiter die Einführungsphase der **Gebäudeführung** abgeschlossen hat und zum Ausfüllen der Aufnahmepersonalpapiere bereit ist. Jede Änderung in einem vorhandenen Element auf der Liste derNeue Mitarbeiter in Seattle löst den Workflow aus, aber der Workflow führt keine Aktionen durch, bis das FeldOrientationStage des Listenelements aufHR paperwork festgelegt wird. Dann wird eine E-Mail an einen Mitarbeiter der Personalabteilung gesendet und eine Aufgabe für diesen Mitarbeiter zur **WorkflowTaskList** hinzugefügt.




> **HINWEIS**
> An verschiedenen Punkten während des Entwerfens Ihres Workflows wird ein blaues Rautensymbol mit einem Ausrufezeichen 



![Ein kleines blaues Rautensymbol mit einem weißen Ausrufezeichen darin.](images/f7b82a70-80fe-4e7e-a0f5-5a78cd12b367.PNG)



für ein oder mehrere Elemente im Workflow-Designer angezeigt. Diese weisen auf temporäre Fehler hin. (Bewegen Sie den Cursor über das Symbol, um eine kurze Meldung anzuzeigen, oder suchen Sie in der **Fehlerliste** von Visual Studio nach Details.) Hierbei handelt es sich um Nebeneffekte der Unvollständigkeit des Workflows. Diese sollten nicht mehr vorhanden sein, wenn Sie das Verfahren abgeschlossen haben.





1. Öffnen Sie den Bereich **Toolbox** in Visual Studio, erweitern Sie den Knoten **SP - Liste**, und ziehen Sie dann **LookupSPListItem** in die **Sequenz** im Designer.


2. Wählen Sie **LookupSPListItem** aus, um die Eigenschaften im Visual Studio-Bereich **Eigenschaften** anzuzeigen. Legen Sie die Eigenschaften auf die folgenden Werte fest:

  - **ItemID:** (aktuelles Element)


  - **ListID:** (aktuelle Liste)


  - **DisplayName:** LookupCurrentNewEmployee



    Der Bereich **Eigenschaften** sollte jetzt wie folgt aussehen:


   **Eigenschaftenbereich von LookupSPListItem**



!\[Eigenschaftenbereich der Workflowaktivität "Listenelement suchen", wobei die Eigenschaften "ItemID", "ListID" und "DisplayName" festgelegt sind.](images/60f3302e-ca9c-45be-b785-0c9f636181da.PNG)


    Klicken Sie auf eine beliebige Stelle außerhalb des Bereichs, um die Änderungen zu speichern. Die Designeroberfläche sollte jetzt wie folgt aussehen.


   **Sequenz im Workflow-Designer**



!\[Workflow-Designer mit dem Feld "Sequenz" und einer darin enthaltenen Aktivität mit dem Namen "Aktuellen neuen Mitarbeiter suchen".](images/c8fbf801-e8e4-444a-9d2e-c14e29f537de.PNG)





3. Klicken Sie auf den Link **Eigenschaften abrufen** in der (neu umbenannten) AktivitätLookupCurrentNewEmployee im Designer. Dadurch wird eine **GetDynamicValueProperties**-Aktivität zur Sequenz hinzugefügt.


4. Klicken Sie auf den Text **Definieren...** in der Aktivität **GetDynamicValueProperties**. Damit wird das Dialogfeld **Eigenschaften** geöffnet.


5. Legen Sie den **Entitätstyp** auf **Listenelement von** _Name_der_Listeninstanz_ fest, wobei _Name_der_Listeninstanz_Neue Mitarbeiter in Seattle ist.


6. Klicken Sie in der Spalte **Pfad** auf die oberste Zelle, und wählen Sie dannOrientationStage aus der Dropdownliste aus.


7. Klicken Sie auf die Zelle darunter, und wählen Sie dann **Title (Title)** aus der Dropdownliste aus.


8. Klicken Sie auf **Variablen auffüllen**. Damit werden die Variablen namensOrientationStage und **Title** erstellt, und jeder wird der Wert der entsprechenden Felder im aktuellen Element der Liste derNeue Mitarbeiter in Seattle zugewiesen. Das Dialogfeld **Eigenschaften** sollte nun wie folgt aussehen:

   **Dialogfeld „Eigenschaften" der Workflowaktivität**



!\[Das Dialogfeld "Eigenschaften" für die Aktivität "Dynamische Werte abrufen", wobei der Entitätstyp auf Elemente der Liste "Neue Mitarbeiter" festgelegt ist, und Variablen mit dem Namen "Title" und "OrientationStage", die Feldern mit dem gleichen Namen zugewiesen sind.](images/36a841e7-ce1b-444c-9bfe-7cdc56399ec1.PNG)





9. Wählen Sie **OK** aus. Die Designeroberfläche sollte jetzt wie folgt aussehen:

   **Workflow-Designer**



!\[Der Workflow-Designer mit zwei Aktivitäten: "Listenelement suchen" und "Dynamische Werte abrufen".](images/cd8eb456-d883-491a-b171-38c1b9f64018.PNG)





10. Öffnen Sie den Bereich **Toolbox** in Visual Studio, erweitern Sie den Knoten **Ablaufsteuerung**, und ziehen Sie dann **Wenn** in den unteren Bereich von **Sequenz** unterhalb von **GetDynamicValueProperties**.


11. Geben Sie in das Feld **Bedingung** von **Wenn**OrientationStage=="HR paperwork" ein.


12. Öffnen Sie den Bereich **Toolbox** in Visual Studio, erweitern Sie den Knoten **SP - Dienstprogramme**, und ziehen Sie dann **E-Mail** in das Feld **Dann** der **Wenn**-Aktivität.


13. Wählen Sie die Aktivität **E-Mail** aus. Legen Sie im Bereich **Eigenschaften** die Werte für die Eigenschaften des Textkörpers, des Betreffs und der Option „An" fest. Wählen Sie in jedem Fall die Popupschaltfläche **...** für die Eigenschaft aus, und verwenden Sie den sich öffnenden **Ausdrucks-Editor**, um den Wert der Eigenschaft wie in der folgenden Tabelle festzulegen. Da es sich dabei um C# Zeichenfolgenausdrücke handelt, verwenden Sie die Anführungszeichen exakt wie gezeigt. Der  `Title` ist hier eine Variable, die Sie zuvor zum Feld **Title** des Listenelements zugewiesen haben (das den Namen des Mitarbeiters enthält).

  - **Textkörper:** `Title + " is waiting in the lobby to fill out benefits and employment forms."`


  - **Betreff:** `Title + " is ready for HR paperwork"`


  - **An:** `new System.Collections.ObjectModel.Collection<string>() {"your_O365_email"}`

    Ersetzen Sie den Platzhalter  *your_O365_email*  durch die Identität, mit der Sie sich bei Ihrem Office 365-Entwicklerkonto anmelden, z. B. *alias*  @ *O365domain*  .sharepoint.com. Da es sich hier um eine C#-Zeichenfolge handelt, muss diese in Anführungszeichen eingeschlossen sein.


14. Öffnen Sie den Bereich **Toolbox** in Visual Studio, erweitern Sie den Knoten **Runtime**, und ziehen Sie dann **TerminateWorkflow** in das Feld **Else** der **Wenn**-Aktivität.


15. Wählen Sie die Aktivität **TerminateWorkflow** aus, und legen Sie im Bereich **Eigenschaften** den **Grund** auf Folgendes fest, *einschließlich der Anführungszeichen*  :"Not at HR paperwork stage.". Der Designer sollte jetzt wie folgt aussehen:

   **Workflow-Designer, wenn der Workflow abgeschlossen ist**



!\[Der Workflow-Designer mit Aktivitäten für "Listenelement suchen", "Dynamischen Wert abrufen" und einer "Wenn-Dann-Sonst"-Struktur. E-Mail ist die Aktivität im Teil "Dann" und "Workflow beenden" ist die Aktivität für "Sonst".](images/c1230e3b-d149-413c-aa66-d258250a4512.PNG)






## Ausführen und Testen des Add-Ins






1. Verwenden Sie die F5-TASTE, um das Add-In bereitzustellen und auszuführen. Visual Studio führt eine temporäre Installation des Add-Ins in Ihrer SharePoint-Website durch und führt das Add-In sofort aus. Die Konsole **Diensttesthost** des Workflow-Managers wird ebenfalls geöffnet.


2. Wenn die Standardseite des Add-Ins geöffnet wird, öffnen Sie eins der Elemente zum Bearbeiten, und legen Sie den Wert von OrientationStage aufHR paperwork fest.

    In der Konsole **Diensttesthost** wird der Hinweis angezeigt, dass der Workflow gestartet wurde. Kurz danach wird der Hinweis angezeigt, dass der Workflow abgeschlossen ist. Im Folgenden sehen Sie ein Beispiel:


   **Konsole „Diensttesthost"**



!\[Das Workflowfenster "Test Service Host" mit einer Zeile, die besagt, dass der Workflow gestartet wurde, gefolgt von einer Zeile, die besagt, dass der Workflow beendet wurde. Die GUID der Workflowinstanz befindet sich am Anfang jeder Zeile.](images/2422936d-7ef6-4c90-a03f-30053fbb9743.PNG)





    > **HINWEIS**
      > Wenn die Konsole **Diensttesthost** nicht geöffnet wird, müssen Sie möglicherweise das Workflow-Debugging aktivieren. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Eigenschaften** aus. Öffnen Sie im Bereich **Eigenschaften** die Registerkarte **SharePoint**, und aktivieren Sie das Kontrollkästchen **Workflow-Debugging aktivieren**. 
3. Navigieren Sie zum E-Mail-Postfach (Outlook) Ihres Office 365-Entwicklerkontos. Dort finden Sie eine E-Mail mit dem Betreff „ *Employee*  is ready for HR paperwork", wobei *Employee*  der Name des Mitarbeiters ist, deren Element Sie bearbeitet haben. Im Textkörper der E-Mail steht: „ *Employee*  is waiting in the lobby to fill out benefits and employment forms." Im Folgenden sehen Sie ein Beispiel:

   **Vom Workflow gesendete E-Mail**



!\[Eine E-Mail-Nachricht in Outlook aus dem Workflow mit dem Betreff "Cassie Hicks ist bereit für die Schreibarbeiten der Personalabteilung" und dem Nachrichtentext "Cassie Hicks wartet in der Lobby, um Formulare für Zusatzleistungen und Dienstverhältnisse auszufüllen."](images/7b1d8f47-9c34-441e-af6a-3af4a8c65533.PNG)





    > **TIPP**
      > Wenn der Workflow gestartet, aber niemals abgeschlossen und die E-Mail nicht gesendet wird, versuchen Sie, die Debugsitzung zu beenden, und drücken Sie einige Male erneut F5, bevor Sie davon ausgehen, dass etwas in Ihrem Code nicht in Ordnung ist. Manchmal liegt das Problem bei SharePoint Online. > Wenn weiterhin Probleme auftreten, versuchen Sie, einen Inhaltstyp namens **ListFieldsContentType**, falls dieser noch nicht vorhanden ist, zum Abschnitt **ContentTypes** der schema.xml-Datei hinzuzufügen. Das folgende ist ein Beispiel für das Markup.>  `<ContentType ID="0x0100781dd48170b94fdc9706313c82b3d04c" Name="ListFieldsContentType" Hidden="TRUE">`



 `</ContentType>`> Kopieren Sie dann den gesamten Abschnitt **FieldRefs** des Inhaltstyps **NewEmployee** in diesen neuen Inhaltstyp.> Speichern Sie das Projekt, ziehen Sie es zurück, und drücken Sie erneut F5. 
4. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version des Add-Ins zurück und installiert die neueste.


5. Da Sie mit diesem Add-In und Visual Studio-Lösung in anderen Artikeln arbeiten werden, ist es eine bewährte Methode, das Add-In ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.



## 
<a name="Nextsteps"> </a>

Im nächsten Artikel dieser Reihe fügen Sie eine benutzerdefinierte Seite und Formatvorlage zur SharePoint-Add-In hinzu:  [Hinzufügen einer benutzerdefinierten Seite und Formatvorlage zu einem von SharePoint gehosteten Add-In für SharePoint](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md).




