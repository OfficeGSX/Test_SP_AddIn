---
title: Hinzufügen von benutzerdefinierten Spalten zu einem von SharePoint gehosteten Add-In für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 0fbc9a8b-d652-44a8-b821-578afd8f33dc
---


# Hinzufügen von benutzerdefinierten Spalten zu einem von SharePoint gehosteten Add-In für SharePoint
Erfahren Sie, wie Sie benutzerdefinierte Spalten in SharePoint-Add-Ins einschließen.
Dies ist der dritte einer Reihe von Artikeln über die Grundlagen der Entwicklung von in SharePoint gehosteten SharePoint-Add-Ins. Machen Sie sich zunächst mit  [SharePoint-Add-Ins](sharepoint-add-ins.md) und den vorherigen Artikeln dieser Reihe vertraut:





-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Bereitstellung und Installation eines von SharePoint gehosteten Add-Ins für SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)



> **HINWEIS**
> Wenn Sie diese Reihe zu von SharePoint gehosteten Apps durchgearbeitet haben, haben Sie eine Visual Studio Lösung, die Sie verwenden können, um mit diesem Thema fortzufahren. Sie können außerdem das Repository unter  [SharePoint_SP-Hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) herunterladen und die Datei „BeforeColumns.sln" öffnen.




In diesem Artikel kehren wir zum Codieren zurück, indem wir einige Websitespalten zum SharePoint-Add-In „Employee Orientation" hinzufügen.
## Erstellen von benutzerdefinierten Spaltentypen






1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** und **Neuer Ordner** aus. Nennen Sie den OrdnerWebsitespalten.


2. Klicken Sie mit der rechten Maustaste auf den neuen Ordner, und wählen Sie **Hinzufügen** und **Neues Element** aus. Das Dialogfeld **Neues Element hinzufügen** wird mit dem Knoten **Office/SharePoint** geöffnet.


3. Wählen Sie **Websitespalte** aus, geben Sie ihr den NamenDivision, und wählen Sie **Hinzufügen** aus.


4. Bearbeiten Sie in der Datei „elements.xml" für die neue Websitespalte das Element **Field** so, dass es die im folgenden Beispiel gezeigten Attribute und Werte hat, mit der Ausnahme, dass Sie den Wert *der GUID*  für das Attribut **ID** **nicht** von dem Wert ändern, den Visual Studio dafür generiert hat. *Seien Sie also vorsichtig, wenn Sie Kopieren und Einfügen verwenden*  .

  ```

<Field ID="{generated GUID}"
       Name="Division" 
       Title="Division" 
       DisplayName="Division" 
       Description="The division of the company where the employee works." 
       Group="Employee Orientation" 
       Type="Text" 
       Required ="FALSE">
</Field>
  ```

5. Fügen Sie eine weitere **Websitespalte** im selben Ordner mit dem NamenOrientationStage hinzu.


6. Bearbeiten Sie in der Datei elements.xml für die neue Websitespalte das Element **Field** so, dass es die im folgenden Beispiel dargestellten Attribute und Werte aufweist, mit der Ausnahme, dass der Wert der GUID für das Attribut **ID**, den Visual Studio dafür generiert hat, nicht geändert wird.

  ```

<Field ID="{generated GUID}"
       Name="OrientationStage" 
       Title="OrientationStage"
       DisplayName="Orientation Stage" 
       Group="Employee Orientation" 
       Description="The current orientation stage of the employee." 
       Type="Choice"
       Required ="TRUE">
</Field>
  ```

7. Da es sich um ein Auswahlfeld handelt, müssen Sie die Auswahlmöglichkeiten und die Reihenfolge angeben, in der diese in der Dropdownliste angezeigt werden sollen, wenn ein Benutzer eine Auswahl vornimmt. Und da es ein Pflichtfeld ist, müssen Sie einen Standardwert angeben. Fügen Sie das folgende untergeordnete Markup zum Element **Field** hinzu.

  ```

<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Tour of building</CHOICE>
      <CHOICE>HR paperwork</CHOICE>
      <CHOICE>Corporate network access</CHOICE>
      <CHOICE>Completed</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Tour of building</MAPPING>
      <MAPPING Value="3">HR paperwork</MAPPING>
      <MAPPING Value="4">Corp network access</MAPPING>
      <MAPPING Value="5">Completed</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```

8. Speichern Sie alle Dateien.



## Ausführen der App und Testen der Spalten






1. Verwenden Sie die F5-TASTE, um Ihre App bereitzustellen und auszuführen. Visual Studio führt eine temporäre Installation der App auf Ihrer SharePoint-Testwebsite durch und führt die App sofort aus. 


2. Wenn die Standardseite der App geöffnet wird, wählen Sie den Link **Neue Mitarbeiter in Seattle** aus, um die benutzerdefinierte Listeninstanz zu öffnen.


3. Öffnen Sie die Seite **Einstellungen** der Liste, und fügen Sie die zwei Spalten mit den folgenden Schritten hinzu.

a. Klicken Sie auf die Popupschaltfläche **· · ·** direkt über der Liste, und wählen Sie dann **Ansicht erstellen** aus.
b. Die Seite **Ansichtstyp** wird mit der Breadcrumb-Struktur **Einstellungen und Ansichtstyp** im oberen Bereich geöffnet. Klicken Sie auf den Breadcrumb **Einstellungen**.

   **Schritte zum Öffnen der Seite „Listeneinstellungen"**



![Liste "Neue Mitarbeiter in Seattle", wobei die Beschriftungsschaltfläche und das Element "Ansicht erstellen" als erster Schritt markiert sind. Dann über den Pfeil zur Seite "Ansicht erstellen", wobei das Breadcrumb "Einstellungen" markiert ist.](images/6c119cae-adf8-42ff-9890-f3aa1e11719d.png)





c. Öffnen Sie auf der Seite **Einstellungen** den Link **Aus vorhandenen Websitespalten hinzufügen** etwa in der Mitte der Seite links.

   **Seite „Listeneinstellungen"**



![Die Seite mit Listeninstanzeinstellungen, auf der der Link für "Spalten aus Websitespalten hinzufügen" hervorgehoben ist.](images/a8698b77-b9d2-40f6-89f6-ccc3c6e06073.png)





d. Wählen Sie auf der Seite **Spalten aus Websitespalten hinzufügen** in der Dropdownliste **Websitespalten auswählen aus** die Option **Employee Orientation** aus.

   **Hinzufügen von Spalten von der Seite „Websitespalten"**



![Das Steuerelement für die SharePoint-Spaltenauswahl, wobei "Mitarbeiterorientierung" in der Dropdownliste mit der Bezeichnung "Websitespalten auswählen" ausgewählt ist.](images/3b33c622-c52a-45fd-8ea1-d7f307539753.png)

e. Fügen Sie die Spalten **Division** und **OrientationStage** zum Feld **Hinzuzufügende Spalten** hinzu.
f. Wählen Sie **OK** aus, um zur Seite **Einstellungen** zurückzukehren, und klicken Sie dann auf den Breadcrumb **Neue Mitarbeiter in Seattle** im oberen Bereich der Seite.


4. Die neuen Spalten befinden sich jetzt in der Liste. Fügen Sie der Liste ein neues Element hinzu. Auf dem Bearbeitungsformular enthält das Feld **OrientationStage** bereits den StandardwertNicht gestartet. (Die vorhandenen Elemente in diesem Feld sind leer, da sie erstellt wurden, bevor sich das Feld in der Liste befand.)

   **Die Liste mit neuen Spalten**



![Die Liste mit den neuen Spalten "Abteilung" und "Orientierungsphase".](images/d4e17424-c06b-4635-aab8-4912cee5fe35.png)





5. Schließen Sie zum Beenden der Debugsitzung das Browserfenster, oder beenden Sie das Debuggen in Visual Studio. Jedes Mal, wenn Sie F5 drücken, zieht Visual Studio die vorherige Version der App zurück und installiert die neueste.


6. Da Sie mit dieser App und Visual Studio-Lösung in anderen Artikeln arbeiten werden, ist es eine bewährte Methode, die App ein letztes Mal zurückzuziehen, wenn Sie Ihre Arbeit daran für eine Weile abgeschlossen haben. Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Zurückziehen** aus.



## 
<a name="Nextsteps"> </a>

Da Sie nicht wirklich möchten, dass Ihre Benutzer die benutzerdefinierten Spalten manuell zur Liste hinzufügen müssen, erstellen Sie im nächsten Artikel dieser Reihe einen benutzerdefinierten Inhaltstyp, der die benutzerdefinierten Spalten enthält und automatisch mit der Listenvorlage für neue Mitarbeiter verknüpft wird:  [Hinzufügen eines benutzerdefinierten Inhaltstyps zu einem von SharePoint gehosteten Add-In für SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md). 




