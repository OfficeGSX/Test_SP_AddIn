

# Datenspeicheroptionen in Add-Ins for SharePoint
Erfahren Sie mehr über die verschiedenen Möglichkeiten der Datenspeicherung mit einer SharePoint-Add-In.
 * **Gilt für:*** 





|||
|:-----|:-----|
|**In diesem Artikel**          [Speicheroptionen für strukturierte Daten](#StructuredData)           [Speicheroptionen für unstrukturierte Daten](#UnStructuredData)           [Add-In-Einstellungen und andere Metadaten-Speicheroptionen](#AppMetadata)           [Datenzugriffsoptionen](#DataAccess)           [Zusätzliche Ressourcen](#AddtionalResources)||
 

## Speicheroptionen für strukturierte Daten
<a name="StructuredData"> </a>

Eine SharePoint-Add-In kann fast jede Art von strukturiertem Datenspeicher sowohl innerhalb als auch außerhalb von SharePoint 2013 auf Microsoft-Plattformen und anderen Plattformen verwenden. Nachfolgend werden einige Speicherorte aufgeführt, an denen strukturierte Daten für eine SharePoint-Add-In gespeichert werden können:




- SharePoint-Listen in einer Add-In-Website


- SQL Azure


- Externe Datenquellen, die über Microsoft Business Connectivity Services (BCS) mit SharePoint verbunden sind


- Ein Cloud-Service von einem anderen Anbieter als Microsoft


- Eine Datenbank auf Ihrem eigenen Server



> **TIPP**
> Sie werden Ihre SharePoint-Add-In vermutlich irgendwann aktualisieren. Wenn eine SharePoint-Add-In SharePoint-Komponenten in einem Add-In-Web enthält, wird beim Updatevorgang eine vollständige Kopie des Add-In-Web erstellt. Daher machen sehr große SharePoint-Listen im App-Web den Upgradevorgang zeitaufwendig und belasten den Prozessor auf dem Inhaltsdatenbankserver. Sie sollten vermeiden, SharePoint-Listen im App-Web mit "Big Data" aufzufüllen. 





## Speicheroptionen für unstrukturierte Daten
<a name="UnStructuredData"> </a>

Dokumente, Bilder, Videos, Audiodateien und andere Arten unstrukturierter Daten, die von einer SharePoint-Add-In erzeugt werden, können in oder außerhalb von SharePoint gespeichert werden. Dokumentbibliotheken sind eine gute Wahl für Dokumente und können mit der SharePoint-Suchfunktion durchsucht werden. Eine Website-Objektbibliothek ist häufig für Multimediadateien eine gute Wahl. 



Weitere Optionen sind Blob-Storage in Ihrem Microsoft Azure-Konto oder auf Ihren eigenen Servern. Dateien können auch in einigen Plattformen anderer Anbieter als Microsoft oder Cloud-Diensten gespeichert werden.




## Add-In-Einstellungen und andere Metadaten-Speicheroptionen
<a name="AppMetadata"> </a>

Metadaten für eine SharePoint-Add-In, z. B. Benutzereinstellungen, Standortinformationen und andere Einstellungen können an verschiedenen Stellen gespeichert werden. Manchmal ist eine verborgene SharePoint-Liste eine gute Wahl. Sie können auch den Eigenschaftenbehälter der Add-In-Website verwenden. Eine weitere Option für von einem Anbieter gehostete Add-Ins ist der Microsoft Azure-Tabellenspeicher. 




## Datenzugriffsoptionen
<a name="DataAccess"> </a>

Welche Datenzugriffsoptionen verfügbar sind, hängt natürlich vom gewählten Speicher ab. Datenzugriff und -suche werden in verschiedenen anderen Artikeln besprochen. Weitere Informationen finden Sie unter  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).




## Zusätzliche Ressourcen
<a name="AddtionalResources"> </a>


-  [Wichtige Aspekte der Architektur und Entwicklungslandschaft von Add-Ins für SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [Sicherer Datenzugriff und Clientobjektmodelle für SharePoint-Add-Ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Zugreifen auf SharePoint 2013-Daten über Add-Ins mithilfe der domänenübergreifenden Bibliothek](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)





    