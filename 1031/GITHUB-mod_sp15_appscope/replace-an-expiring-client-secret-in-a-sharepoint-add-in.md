---
title: Austauschen eines ablaufenden geheimen Clientschlüssels in einem Add-In für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# Austauschen eines ablaufenden geheimen Clientschlüssels in einem Add-In für SharePoint
Erfahren Sie, wie Sie einen neuen Clientschlüssel für eine SharePoint-Add-In hinzufügen können, die bei AppRegNew.aspx registriert ist.
Clientschlüssel für SharePoint-Add-Ins, die mithilfe der AppRegNew.aspx-Seite registriert wurden, laufen nach einem Jahr ab. In diesem Artikel wird erläutert, wie Sie einen neuen Schlüssel für das Add-In hinzufügen, und wie Sie einen neuen Clientschlüssel erstellen können, der für drei Jahre gültig ist.
  
    
    


> **HINWEIS**
> In diesem Artikel geht es um SharePoint-Add-Ins, die über einen Organisationskatalog verteilt und über die Seite AppRegNew.aspx registriert werden. Wenn das Add-In über das Verkäuferdashboard registriert ist, finden Sie weitere Informationen unter  [Erstellen oder Aktualisieren von Client-IDs und geheimen Clientschlüsseln im Verkäuferdashboard](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update). 
  
    
    


## Voraussetzungen zum Aktualisieren eines geheimen Clientschlüssels

Stellen Sie vor Beginn Folgendes sicher:
  
    
    

-  [Der Microsoft Online Services-Anmeldeassistent](http://www.microsoft.com/download/details.aspx?id=39267) ist auf dem Entwicklungscomputer installiert.
    
  
- Das Microsoft Online Services PowerShell-Modul ( [32-Bit](http://go.microsoft.com/fwlink/p/?linkid=236298);  [64-Bit](http://go.microsoft.com/fwlink/p/?linkid=236297)) wird auf dem Entwicklungscomputer installiert.
    
  
- Sie sind der Mandantenadministrator für den Office 365-Mandanten (oder ein Farmadministrator in der Farm), auf dem das Add-In mit der AppRegNew.aspx-Seite registriert wurde.
    
  

## Ermitteln der Ablaufdaten des auf dem Office 365-Mandanten installierten SharePoint-Add-Inss


  
    
    

1. Öffnen Sie Windows PowerShell und führen Sie folgendes Cmdlet aus:
    
 ```
  
Connect-MsolService

 ```

2. Geben Sie bei der Anmeldeaufforderung die Mandantenadministrator-Anmeldeinformationen (oder Farmadministrator) für die Office 365-Mandantschaft oder die Farm ein, auf der das Add-In mit der AppRegNew.aspx-Seite registriert wurde.
    
  
3. Generieren Sie einen Bericht mit den folgenden Zeilen, in denen das jeweilige Add-In und das Datum, an dem ihr geheimer Schlüssel abläuft, aufgeführt sind. Beachten Sie zu diesem Code Folgendes:
    
  - Zunächst werden eigene Anwendungen von Microsoft und Add-Ins, die noch entwickelt werden, herausgefiltert.
    
  
  - Aus dem Rest werden Nicht-SharePoint-Add.Ins und Add-Ins, die asymmetrische Schlüssel verwenden, z. B. Workflows, herausgefiltert.
    
  

 ```
  
$applist = Get-MsolServicePrincipal -all  |Where-Object -FilterScript { ($_.DisplayName -notlike "*Microsoft*") -and ($_.DisplayName -notlike "autohost*") -and  ($_.ServicePrincipalNames -notlike "*localhost*") }

foreach ($appentry in $applist)
{
    $principalId = $appentry.AppPrincipalId
    $principalName = $appentry.DisplayName
    
    Get-MsolServicePrincipalCredential -AppPrincipalId $principalId -ReturnKeyValues $false | Where-Object { ($_.Type -ne "Other") -and ($_.Type -ne "Asymmetric") }
    
     $date = get-date
     Write-Host "$principalName;$principalId;$appentry.KeyId;$appentry.type;$date;$appentry.Usage"

}  > c:\\temp\\appsec.txt
 ```

4. Öffnen Sie die Datei C:\\temp\\appsec.txt, um den Bericht anzuzeigen. Lassen Sie das Windows PowerShell-Fenster für die nächste Prozedur geöffnet, wenn sich das Ablaufdatum für einen der geheimen Schlüssel nähert.
    
  

## Generieren eines neuen geheimen Schlüssels


  
    
    

1. Erstellen Sie eine Client-ID-Variable mit folgender Zeile, und verwenden Sie dafür die Client-ID des SharePoint-Add-Ins als Parameter.
    
 ```
  
$clientId = 'client id of the add-in'

 ```

2. Generieren Sie mit den folgenden Zeilen einen neuen geheimen Clientschlüssel:
    
 ```
  
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Sign -Value $newClientSecret
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Verify -Value $newClientSecret
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Password -Usage Verify -Value $newClientSecret
$newClientSecret
 ```

3. Der neue geheime Clientschlüssel wird in der Windows PowerShell -Konsole angezeigt. Kopieren Sie ihn in eine Textdatei. Sie benötigen ihn in der nächsten Prozedur.
    
  

> **TIPP**
> Standardmäßig ist der geheime Schlüssel des Add-Ins für ein Jahr gültig. Sie können diesen Zeitraum verkürzen oder verlängern (bis auf maximal drei Jahre), indem Sie den Parameter **-EndDate** für die drei Aufrufe des Cmdlets **New-MsolServicePrincipalCredential** verwenden. Der Wert des Parameters muss ein [DateTime](http://msdn2.microsoft.com/DE-DE/library/03ybds8y)-Objekt sein, das nicht mehr als drei Jahre nach **DateTime.Now** liegt.
  
    
    


## Aktualisieren der Remote-Webanwendung in Visual Studio zum Verwenden des neuen geheimen Schlüssels


> **WICHTIG**
> Wenn Ihr Add-In ursprünglich mit einer Vorabversion von Microsoft Office-Entwicklertools für Visual Studio erstellt wurde, enthält es möglicherweise eine veraltete Version der Datei TokenHelper.cs (oder TokenHelper.vb). Wenn die Datei die Zeichenfolge „secondaryClientSecret" nicht enthält, ist sie veraltet und muss ausgetauscht werden, bevor Sie die Webanwendung durch einen neuen geheimen Schlüssel aktualisieren können. Um eine Kopie einer endgültigen Version der Datei abzurufen, benötigen Sie Visual Studio 2012 oder höher. Erstellen Sie ein neues SharePoint-Add-In-Projekt in Visual Studio. Kopieren Sie die TokenHelper-Datei in das Webanwendungsprojekt Ihres SharePoint-Add-Ins. 
  
    
    


1. Öffnen Sie das SharePoint-Add-In-Projekt in Visual Studio, und öffnen Sie die Datei web.config für das Webanwendungsprojekt. Im Abschnitt **appSettings** finden Sie Schlüssel für die Client-ID und den geheimen Clientschlüssel. Beispiel:
    
 ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

 ```

2. Ändern Sie den Namen des **ClientSecret**-Schlüssels in "SecondaryClientSecret" wie im folgenden Beispiel:
    
 ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
 ```

3. Fügen Sie einen neuen **ClientSecret**-Schlüssel hinzu und übergeben Sie den neuen geheimen Clientschlüssel. Ihr Markup sollte wie folgt aussehen:
    
 ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
 ```

4. Wenn Sie zu einer neuen "TokenHelper"-Datei gewechselt haben, müssen Sie das Projekt neu erstellen.
    
  
5. Veröffentlichen Sie die Webanwendung erneut.
    
  

## Erstellen eines Client-Schlüssels, der drei Jahre gültig ist

Für abgelaufene Clientschlüssel müssen Sie zunächst alle abgelaufenen Schlüssel für eine bestimmte **ClientId** löschen. Sie erstellen einen neuen mit MSOPowerShell, warten mindestens 24 Stunden, und testen die App mit der neuen **ClientId** und **ClientSecret** dem neuen Schlüssel.
  
    
    

1. Stellen Sie eine Verbindung mit dem Mandanten-Admininistratorbenutzer mit dem unten aufgeführten Markup mithilfe von SharePoint 2013 Windows PowerShell her.
    
 ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

 ```

2. Abrufen von **ServicePrincipals** und Schlüsseln. Durch das Drucken von **$keys** werden drei Datensätze zurückgegeben. Ersetzen Sie die einzelnen **KeyId** unter *KeyId1*  , *KeyId2*  und *KeyId3*  . Außerdem wird Ihnen das **EndDate** der einzelnen Schlüssel angezeigt. Vergewissern Sie sich, dass dort Ihre Schlüssel angezeigt werden.
    
    **Hinweis:** Die **ClientId** muss mit Ihrer abgelaufenen **ClientId** übereinstimmen. Es wird empfohlen, alle abgelaufenen und nicht abgelaufenen Schlüssel für diese **ClientId** zu löschen.
    


 ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

 ```

3. Generieren eines neuen **ClientSecret** für diese **ClientID**. Es verwendet die gleichen **ClientId**,wie in dem vorstehenden Schritt eingerichtet. Der neue **ClientSecret** ist für 3 Jahre gültig.
    
 ```
  
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$dtStart = [System.DateTime]::Now
$dtEnd = $dtStart.AddYears(3)
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Sign -Value $newClientSecret -StartDate $dtStart  -EndDate $dtEnd
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Verify -Value $newClientSecret   -StartDate $dtStart  -EndDate $dtEnd
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Password -Usage Verify -Value $newClientSecret   -StartDate $dtStart  -EndDate $dtEnd
$newClientSecret

 ```

4. Kopieren Sie die Ausgabe des **$newClientSecret**.
    
  
5. Ersetzen Sie die **Web.config** durch diese **ClientId** und **ClientSecret**. Sie benötigen nicht die **SecondaryClientSecret** -App-Einstellungen.
    
  
6. Warten Sie mindestens 24 Stunden mit dem Auffüllen des **ClientSecret** in SharePoint Office (SPO).
    
  

## Siehe auch


#### Weitere Ressourcen


  
    
    
 [Vom Anbieter gehostete fällt bei SPO aus](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
