---
title: Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement
ms.prod: SHAREPOINT
ms.assetid: 2ec857d5-dc6f-4cf6-ba45-adc845ef2a25
---


# Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement
Eine Website für Office 365-Entwickler erleichtert Ihnen die Einrichtung und den Einstieg in das Erstellen, Testen und Bereitstellen Ihrer Office- und SharePoint-Add-Ins. Viele Office 365 Business-, Enterprise-, Education- und Government-Abonnements enthalten eine Websitevorlage, die Sie zum Erstellen einer Website für Entwickler verwenden können.
 **Vorbereitungen**
  
    
    


- **Stellen Sie sicher, dass Sie ein Office 365-Abonnement besitzen, das eine Entwicklerwebsite unterstützt.** Wenn Sie eins der folgenden Office 365 Abonnementpläne besitzen, können Sie in Ihrem vorhandenen Abonnement eine Website für Entwickler erstellen:
    

  
    
    
> Office 365 Midsize Business
    
  

  
    
    
> Office 365 Enterprise E1, E3, E4, E5 oder K1
    
  

  
    
    
> Office 365 Education A2, A3 oder A4
    
  

  
    
    
> Office 365 Government G1, G3, G4 oder K1
    
  
- **Wenn Sie ein Office 365 Small Business-Abonnement besitzen,** unterstützt es nur eine einzelne Websitesammlung, sie können damit also keine Entwickler-Websitesammlung erstellen. Weitere Informationen über Office 365-Pläne für Ihr Unternehmen finden Sie unter [SharePoint Online: Softwarelimits und -beschränkungen](http://office.microsoft.com/de-de/office365-sharepoint-online-enterprise-help/sharepoint-online-software-boundaries-and-limits-HA102694293.aspx).
    
  
- Weitere Informationen zu Office 365 Enterprise-Angeboten finden Sie unter  [Pläne und Preise](http://products.office.com/de-de/business/office-365-enterprise-e1-business-software).
    
  

## Erstellen einer Entwicklerwebsite
<a name="bk_createdevsite"> </a>


1. Melden Sie sich bei Office 365 als globaler oder SharePoint Online-Administrator an.
    
    **Sie müssen als globaler oder SharePoint Online-Administrator angemeldet sein, um neue Websitesammlungen** wie z. B. eine Website für Entwickler zu erstellen. Nur Administratoren werden nach dem Anmelden an Office 365 Administratoroptionen angezeigt. Wenn Sie kein Administrator sind, müssen Sie einen Administrator in Ihrem Unternehmen kontaktieren, der für Sie eine der folgenden Aufgaben ausführen muss:
    
  - Ihnen Administratorrechte gewähren, damit Sie die Website für Entwickler selbst erstellen können.
    
  
  - Die Website für Entwickler für Sie erstellen und Sie als Administrator für die Websitesammlung angeben.
    
  
2. Klicken Sie auf die Schaltfläche „App-Startfeld" ganz links auf der Navigationsleiste am oberen Bildschirmrand.
    
  
3. Klicken Sie auf die Kachel **Admin**.
    
  
4. Erweitern Sie in der Navigationsstruktur links den Eintrag **Admin**, und wählen Sie **SharePoint** aus.
    
  
5. Klicken Sie im **SharePoint Admin Center** auf der Registerkarte **Websitesammlungen** auf **Neue und dann auf Private Websitesammlung**.
    
!\[Neue Websitesammlungsoption des SharePoint Admin Center](images/SPAdminCenter_newSiteCollection.png)
  

  

  
6. Geben Sie im Dialogfeld **Neue Websitesammlung** Informationen zu Ihrer Entwicklerwebsite an.
    
    **Klicken Sie auf das folgende Bild, um Schritt für Schritt durch die Optionen im Dialogfeld „Neue Websitesammlung" zu gehen. Weitere Hinweise finden Sie in der Tabelle unter dem Bild.**
     [![Click to zoom into new site collection options](images/SPAdminCenter_newSiteCollection_options_ZoomIt.gif)
  
    
    
](http://go.microsoft.com/fwlink/?LinkId=400960)
|||
|:-----|:-----|
|**Title** <br/> |Der Name, den Sie Ihrer Entwicklerwebsite geben möchten.  <br/> |
|Die Liste **Öffentliche Websiteadresse** <br/> |Ein Domänenname und ein URL-Pfad - entweder **/sites/** oder **/teams/** - und geben Sie dann einen URL-Namen für die Websitesammlung ein. <br/> |
|Die Liste **Sprache auswählen** im Abschnitt **Vorlagenauswahl** <br/> |Eine Primärsprache für Ihre Entwicklerwebsite.  <br/> **Stellen Sie sicher, dass Sie die gewünschte Sprache für Ihre Entwicklerwebsite auswählen, da diese nach der Auswahl nicht mehr geändert werden kann.** <br/> Das Auswählen einer Sprache für Ihre Entwicklerwebsite hat keine Auswirkungen auf die Sprachen, die Sie in Ihren Add-Ins für Office und SharePoint verfügbar machen.  <br/> Sie können für Ihre Websites die mehrsprachige SharePoint-Oberfläche aktivieren, doch die Primärsprache der Websitesammlung wählen Sie hier aus.  <br/> |
|Der Abschnitt **Vorlagenauswahl** auf der Registerkarte **Zusammenarbeit** unter **Vorlage auswählen** <br/> |Klicken Sie auf **Entwicklerwebsite.** <br/> |
|**Zeitzone** <br/> |Die zum Gebietsschema Ihrer Enwicklerwebsite gehörige Zeitzone.  <br/> |
|**Administrator** <br/> |Der Benutzername des Websitesammlungsadministrators  <br/> |
|**Speicherkontingent** <br/> |Die Anzahl der Megabytes (MB) ein, die Sie dieser Websitesammlung zuordnen möchten.  <br/> |
|**Serverressourcenkontingent** <br/> |Die Größe der Ressourcen, die Sie der Websitesammlung zuordnen möchten.  <br/> Dieser Wert ist eine Kombination von Leistungsmetriken (wie Prozessorzeit und unbehandelten Ausnahmen), die zu Code in Sandkastenlösungen gehören. Wenn der Wert ein Tageskontingent überschreitet, wird der Sandkasten für diese Websitesammlung deaktiviert.  <br/> |
   
7. Klicken Sie auf **OK**.
    
    Die URL der neuen Entwicklerwebsite wird in der Liste **Websitesammlungen** angezeigt. Wenn die Erstellung der Website abgeschlossen ist, können Sie zur URL navigieren, um Ihre Entwicklerwebsite zu öffnen.
    
!\[Neue Websitesammlungsbereitstellung](images/SPAdminCenter_newSiteCollection_provisioning.png)
  

  

  

## Weitere Ressourcen
<a name="bk_addresources"> </a>


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)
    
  
-  [Erstellen oder Löschen einer Websitesammlung](http://office.microsoft.com/de-de/office365-sharepoint-online-enterprise-help/create-or-delete-a-site-collection-HA102772354.aspx?CTT=1)
    
  

