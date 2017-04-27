---
title: Besonders vertrauenswürdige Konfigurationsskripts für SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# Besonders vertrauenswürdige Konfigurationsskripts für SharePoint 2013
Anpassbare Windows PowerShell-Skripts, die eine Microsoft SharePoint 2013-Farm so konfiguriert, dass sie eine besonders vertrauenswürdige SharePoint-Add-In verwendet.
## Informationen zur Verwendung von Skripts
<a name="Usage"> </a>

Die unten aufgeführten Skripts werden verwendet, um ein oder mehrere digitale X.509-Zertifikate als Aussteller von Zugriffstoken in einer Staging- oder ProduktionsMicrosoft SharePoint 2013-Farm zu definieren. (Ein geeigneteres Skript für eine SharePoint-Add-Ins-Entwicklungsumgebung finden Sie unter  [Erstellen besonders vertrauenswürdiger Add-Ins für SharePoint 2013](create-high-trust-sharepoint-add-ins.md).) Es gibt keine einzelne Skriptgruppe, die für jede SharePoint-Farm funktioniert, da es zu viele Optionen gibt, wie das Zertifikat bezogen und gespeichert wird. Beachten Sie deshalb Folgendes:




- Die Skripts sind zum Ausführen in SharePoint-Verwaltungsshell auf einem SharePoint-Server in der Farm vorgesehen.


- Diese Skripts sollten als Entwürfe betrachtet werden, die möglicherweise angepasst werden müssen.


- Sie werden als Teil des gesamten Veröffentlichungsvorgangs einer besonders vertrauenswürdigen SharePoint-Add-In verwendet. Sie sollten nur von jemandem verwendet werden, der mit den Themen  [Erstellen von Add-Ins für SharePoint, die eine Autorisierung mit hoher Vertrauenswürdigkeit verwenden](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) und [Packen und Veröffentlichen besonders vertrauenswürdiger Add-Ins für SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md) und den darin aufgeführten Voraussetzungen vertraut ist.


- Sie sollten außerdem von jemandem geprüft und angepasst werden, der mit den Kundenzertifikatsrichtlinien des Add-Ins vertraut ist.


- Bei den zwei Hauptskripts unten, **HighTrustConfig-ForSharedCertificate.ps1** und **HighTrustConfig-ForSingleApp.ps1**, wird davon ausgegangen, dass der Administrator ein Zertifikat für die Remote-Webanwendungskomponente des Add-Ins oder der Add-Ins abgerufen hat und eine CER-Version des Zertifikats (das nicht den privaten Schlüssel enthält) in einem Ordner gespeichert hat, zu dem die Benutzerkonten für folgende IIS-Anwendungspools auf den SharePoint-Servern Lesezugriff besitzen:

  - **SecurityTokenServiceApplicationPool**


  - Der Add-In-Pool, der der IIS-Website dient, die die übergeordnete SharePoint-Webanwendung für Ihre Test-SharePoint-Website hostet. Für die **SharePoint - 80** IIS-Website, wird der Pool als **OServerPortalAppPool** bezeichnet.



    Um das Benutzerkonto zu finden, das ein Anwendungspool verwendet, öffnen Sie den IIS-Manager auf einem SharePoint-Server und klicken Sie im Bereich **Verbindungen** doppelt auf **Anwendungspools**. Die Spalte **Identität** in der Liste **Anwendungspools** zeigt die Benutzer für die Anwendungspools an.


- In den Anweisungen zu den zwei Hauptskripts wird außerdem davon ausgegangen, dass die Zertifikate im IIS auf den Servern installiert sind, die die Remoteanwendungen hosten. Die Anweisungen finden Sie unter  [Konfigurieren des Remotewebservers mit dem Zertifikat](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote).


Verwendungshinweise zu den Skripts finden Sie in den weiter unten aufgeführten Abschnitten.




## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

Das Zertifikat der Remotewebanwendungskomponente der besonders vertrauenswürdigen SharePoint-Add-In wird von einer kommerziellen Zertifizierungsstelle oder einer Zertifizierungsstelle der Domäne abgerufen. In beiden Fällen ist das Zertifikat das niedrigste Bindeglied einer Kette von Zertifikaten, dabei vertraut jedes dem darüberliegenden Zertifikat, da aus einer Stamm-Zertifizierungsstelle (kommerziell oder der Domäne) stammt. SharePoint 2013 verlangt, dass  *alle*  Zertifikate in der Kette zur SharePoint-Liste der vertrauenswürdigen Stamm-Zertifizierungsstellen hinzugefügt werden. Das unten aufgeführte Skript kann verwendet werden, um jedes der Zertifikate in der Kette *abgesehen vom niedrigsten*  zur Liste hinzuzufügen. Das niedrigste Zertifikat in der Kette, das direkt an die Remotewebanwendung gebunden ist, wird zu den Stamm-Zertifizierungsstellen in einem der Hauptskripts hinzugefügt, die in Abschnitten weiter unten beschrieben werden.



Die Parameter für das Skript lauten wie folgt:





|**Parameter**|**Value**|
|:-----|:-----|
|-CertPath  <br/> |Der vollständige Pfad und der Dateiname des Zertifikats (die CER-Datei).  <br/> |
|-CertName  <br/> |Der Name des Zertifikats. Um den Namen zu finden, müssen Sie die Eigenschaften des Zertifikats anzeigen.  <br/> |
 
In dem gängigen Szenario, in dem das Zertifikat der Webanwendung von einem Zertifizierungsstellenserver auf mittlerer Ebene (auch als „Enterprise" bezeichnet) ausgestellt wird und das Zertifikat beispielsweise wiederum von einem Stammzertifizierungsserver (auch als „eigenständig" bezeichnet) ausgestellt wird, sollte das Skript je einmal für die Zertifikate und die beiden Zertifizierungsstellenserver ausgeführt werden. Dies wird im folgenden Absatz verdeutlicht:






```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"

```

Wenn alle Zertifikate der besonders vertrauenswürdigen SharePoint-Add-Ins des Kunden aus der gleichen Zertifikatskette stammen, wie es in der Regel der Fall wäre, wird dieses Skript nicht erneut verwendet.



Im Folgenden ist der Code für dieses Skript aufgeführt. Fügen Sie ihn einfach in einen Text-Editor oder den PowerShell-Editor (IPowerShell ISE) ein, und speichern Sie ihn als „AddSPRootAuthority.ps1".




> **HINWEIS**
> Die Datei muss im ANSI-Format, nicht im UTF-8-Format, gespeichert werden. PowerShell gibt möglicherweise Syntaxfehler aus, wenn eine Datei in einem anderen Format als ANSI ausgeführt wird. Der Editor und der PowerShell-Editor speichern die Datei standardmäßig im ANSI-Format. Wenn Sie zum Speichern der Datei einen anderen Editor verwenden, achten Sie darauf, die Datei im ANSI-Format zu speichern. 







```

param(
    [Parameter(Mandatory)][String] $CertName = $(throw "Usage: AddSPRootAuthority.ps1 -CertPath <full path to .cer file> -CertName <name of certificate>"),
    [Parameter(Mandatory)][String] $CertPath
)
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Make the certificate a trusted root authority in SharePoint
$cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)
New-SPTrustedRootAuthority -Name $CertName -Certificate $cert

```


## HighTrustConfig-ForSharedCertificate.ps1
<a name="MultipleAppScript"> </a>

Die primären Aufgabe dieses Skript ist das Registrieren des freigegebenen Zertifikats der Remotewebanwendungskomponenten in mehreren besonders vertrauenswürdigen SharePoint-Add-Ins mit SharePoint als Stammzertifizierungsstelle und vertrauenswürdiger Aussteller eines Zugriffstoken. Dieses Skript wird nicht verwendet, wenn der Kunde separate Zertifikate für jede besonders vertrauenswürdige SharePoint-Add-In verwendet. Weitere Informationen zu diesem Szenario finden Sie unter  [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript) weiter unten. Wenn alle Add-Ins das gleiche Zertifikat verwenden, wird dieses Skript nur einmal ausgeführt. Wenn einige Add-In-Gruppen ein anderes Zertifikat aus anderen Gruppen verwenden, wird diese Skript einmal für jede Gruppe ausgeführt.



In der folgenden Tabelle werden die Parameter und Schalter für das Skript erläutert. Durch Anpassungen des Skripts sind möglicherweise Änderungen an dieser Tabelle erforderlich.





|**Parameter**|**Value**|
|:-----|:-----|
|-CertPath (erforderlich)  <br/> |Der vollständige Pfad zur freigegebenen CER-Datei.  <br/> |
|-CertName (erforderlich)  <br/> |Der Name des freigegebenen Zertifikats. Um den Namen zu finden, müssen Sie IIS auf dem Remotewebserver öffnen, der die Remotewebanwendung hostet. Markieren Sie den Knoten  _Servername_ und klicken Sie doppelt auf **Zertifikate**. Das Zertifikat wird mit dem Namen aufgelistet.  <br/> |
|-TokenIssuerFriendlyName (optional)  <br/> |Ein eindeutiger Anzeigename für den Tokenaussteller mit bis zu 50 Zeichen. Dies kann beim Debuggen von Problemen mit Tokenausstellern hilfreich sein. Der Name muss eindeutig sein. Dies könnte mit einer GUID sichergestellt werden, aber eine GUID belegt 32 der 50 Zeichen. Sie können die Konvertierung einer GUID in eine Base-64-Zeichenfolge in Betracht ziehen, die auf 22 Zeichen reduziert werden kann. Wenn dieser Parameter nicht verwendet wird, verwendet das Skript den Namen „Besonders vertrauenswürdige Add-Ins  _<Base-64-Version der Aussteller-GUID>_".  <br/> |
 
Im Folgenden finden Sie ein Beispiel zum Ausführen dieses Skripts.



 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`




> **WICHTIG**
> Die Registrierung des Zertifikats als Tokenaussteller wird nicht sofort wirksam. Es kann bis zu 24 Stunden dauern, bis alle SharePoint-Server den neuen Tokenaussteller erkannt haben. Durch Ausführen von "iisreset" auf allen SharePoint-Servern wird der Aussteller sofort erkannt, wenn dies möglich ist, ohne die SharePoint-Benutzer zu stören. 




Beginnen Sie eine neue Datei in einem Text-Editor oder dem PowerShell-Editor und kopieren Sie das Folgende in eine Textdatei und speichern Sie sie als HighTrustConfig-ForSharedCertificate.ps1. Verwenden Sie dazu das ANSI-Format, nicht UTF-8.






```

param(
    [Parameter(Mandatory)][String] $CertPath = $(throw "Usage: HighTrustConfig-ForSharedCertificate.ps1 -CertPath <full path to .cer file> -CertName <name of certificate> [-TokenIssuerFriendlyName <friendly name>]"),
    [Parameter(Mandatory)][String] $CertName,
    [Parameter()][String] $TokenIssuerFriendlyName
) 
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Ensure friendly name is short enough
if ($TokenIssuerFriendlyName.Length -gt 50)
{
    throw "-TokenIssuerFriendlyName must be unique name of no more than 50 characters."
} 

# Get the certificate
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)

# Make the certificate a trusted root authority in SharePoint
New-SPTrustedRootAuthority -Name $CertName -Certificate $certificate 

# Get the GUID of the authentication realm
$realm = Get-SPAuthenticationRealm

# Generate a unique specific issuer ID
$specificIssuerId = [System.Guid]::NewGuid().ToString()

# Create full issuer ID in the required format
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

# Create issuer name
if ($TokenIssuerFriendlyName.Length -ne 0)
{
    $tokenIssuerName = $TokenIssuerFriendlyName
}
else
{
    $tokenIssuerName = "High-Trust Add-ins " + [System.Convert]::ToBase64String($specificIssuerId.ToByteArray()).TrimEnd('=') 
}

# Register the token issuer
New-SPTrustedSecurityTokenIssuer -Name $tokenIssuerName -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier -IsTrustBroker

# Output the specific issuer ID to a file in the same folder as this script. The file should be given to the developer of the high-trust SharePoint Add-in.
$specificIssuerId | select * | Out-File -FilePath "SecureTokenIssuerID.txt"
```


> **TIPP**
> Wenn das Skript einen Fehler ausgibt, nachdem die  `New-SPTrustedRootAuthority`-Zeile erfolgreich ausgeführt wurde, kann das Skript nicht erneut ausgeführt werden, bis das Objekt entfernt wurde. Verwenden Sie folgendes Cmdlet an der Stelle, an der der Wert des  `-Identity`-Parameters dem des verwendeten  `-CertName`-Parameters im Skript entspricht. <br/>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`<br/> Wenn der Tokenaussteller entfernt werden muss, verwenden Sie folgendes Cmdlet: <br/>  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`<br/> Wenn der  `-TokenIssuerFriendlyName`-Parameter verwendet wurde, verwenden Sie für  `-Identity` denselben Wert. Wenn der `-TokenIssuerFriendlyName`-Parameter nicht verwendet wurde, ist eine zufällige GUID Teil des Ausstellernamens. Zum Abrufen des für  `-Identity` erforderlichen Werts führen Sie folgendes Cmdlet aus. Öffnen Sie anschließend die erzeugte TokenIssuers.txt-Datei, und suchen Sie den Aussteller mit der Bezeichnung „Besonders vertrauenswürdige Add-Ins _<Base-64-Version der Aussteller-GUID>_". Verwenden Sie diesen vollständigen Namen für  `-Identity`. <br/>  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`





## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

Die primäre Aufgabe dieses Skripts ist das Registrieren des Zertifikats der Remotewebanwendung in einer besonders vertrauenswürdigen SharePoint-Add-In mit SharePoint als Stammautorisierungsstelle und vertrauenswürdigen Zugriffstokenaussteller. Dieses Skript wird nicht verwendet, wenn der Kunde für mehrere SharePoint-Add-Ins ein einzelnes Zertifikat verwendet. Weitere Informationen zu diesem Szenario finden Sie unter  [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript) weiter oben. Dieses Skript wird für jede besonders vertrauenswürdige SharePoint-Add-In ausgeführt.



In der folgenden Tabelle werden die Parameter und Schalter für das Skript erläutert. Durch Anpassungen des Skripts sind möglicherweise Änderungen an dieser Tabelle erforderlich.





|**Parameter**|**Value**|
|:-----|:-----|
|-CertPath (erforderlich)  <br/> |Der vollständige Pfad zur freigegebenen CER-Datei.  <br/> |
|-CertName (erforderlich)  <br/> |Der Name des freigegebenen Zertifikats. Um den Namen zu finden, öffnen Sie IIS auf dem Remotewebserver, der die Remotewebanwendung hostet. Markieren Sie den Knoten  _Servername_ und klicken Sie doppelt auf **Zertifikate**. Das Zertifikat wird mit dem Namen aufgelistet.  <br/> |
|-SPAppClientID (erforderlich)  <br/> |Die Client-ID (eine GUID), die beim Registrieren der SharePoint-Add-In auf appregnew.aspx verwendet wurde. Sie wird als Aussteller-ID für den Tokenaussteller verwendet. Das Skript stellt sicher, dass sie kleingeschrieben wird, da dies eine Anforderung an die Aussteller-IDs ist.  <br/> |
|-TokenIssuerFriendlyName (optional)  <br/> |Ein eindeutiger Anzeigename für den Tokenaussteller mit bis zu 50 Zeichen. Dies kann beim Debuggen von Problemen mit Tokenausstellern hilfreich sein. Der Name muss eindeutig sein. Erwägen Sie die Verwendung des Namens der SharePoint-Add-In. Wenn dieser Parameter nicht verwendet wird, verwendet das Skript den Wert der  `-SPAppClientID` als Namen. <br/> |
 
Im Folgenden ist ein Beispiel eines Aufrufs des Skripts dargestellt:



 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`




> **WICHTIG**
> Die Registrierung des Zertifikats als Tokenaussteller wird nicht sofort wirksam. Es kann bis zu 24 Stunden dauern, bis alle SharePoint-Server den neuen Tokenaussteller erkannt haben. Durch Ausführen von "iisreset" auf allen SharePoint-Servern wird der Aussteller sofort erkannt, wenn dies möglich ist, ohne die SharePoint-Benutzer zu stören. 




Beginnen Sie eine neue Datei in einem Text-Editor oder dem PowerShell-Editor und kopieren Sie das Folgende in eine Textdatei und speichern Sie sie als HighTrustConfig-ForSharedCertificate.ps1. Verwenden Sie dazu das ANSI-Format, nicht UTF-8.






```

param(
    [Parameter(Mandatory)][String] $CertPath = $(throw "Usage: HighTrustConfig-ForSingleApp.ps1 -CertPath <full path to .cer file> -CertName <name of certificate> [-SPAppClientID <client ID of SharePoint add-in>] [-TokenIssuerFriendlyName <friendly name>]"),
    [Parameter(Mandatory)][String] $CertName,
    [Parameter(Mandatory)][String] $SPAppClientID,
    [Parameter()][String] $TokenIssuerFriendlyName
) 
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Ensure friendly name is short enough
if ($TokenIssuerFriendlyName.Length -gt 50)
{
    throw "-TokenIssuerFriendlyName must be unique name of no more than 50 characters."
} 

# Get the certificate
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)

# Make the certificate a trusted root authority in SharePoint
New-SPTrustedRootAuthority -Name $CertName -Certificate $certificate 

# Get the GUID of the authentication realm
$realm = Get-SPAuthenticationRealm

# Must use the client ID as the specific issuer ID. Must be lower-case!
$specificIssuerId = New-Object System.String($SPAppClientID).ToLower()

# Create full issuer ID in the required format
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

# Create issuer name
if ($TokenIssuerFriendlyName.Length -ne 0)
{
    $tokenIssuerName = $TokenIssuerFriendlyName
}
else
{
    $tokenIssuerName = $specificIssuerId 
}

# Register the token issuer
New-SPTrustedSecurityTokenIssuer -Name $tokenIssuerName -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier

```


> **TIPP**
> Die Tipps am Ende des vorherigen Abschnitts gelten auch hier, verwenden Sie jedoch für den Parameter  `-Identity` des Cmdlets `Remove-SPTrustedSecurityTokenIssuer` die Client-ID, wenn der Parameter `-TokenIssuerFriendlyName` nicht mit HighTrustConfig-ForSingleApp.ps1. verwendet wurde.





## Erforderliche Änderungen für Website-Abonnements
<a name="SiteSubscriptions"> </a>

Ein Websiteabonnement, manchmal auch als Mandant bezeichnet, ist eine Untergruppe von Websitesammlungen in einer SharePoint-Farm. Websiteabonnements werden in der Regel in gehosteten SharePoint-Farmen erstellt. Wenn der Kunde dem besonders vertrauenswürdigen SharePoint-Add-In das Add-In in einer Farm installieren möchte, die für Websiteabonnements konfiguriert wurde, muss eins der beiden verwendeten HighTrustConfig-*.ps1-Skripts geändert werden. Jedes Websiteabonnement hat seine eigene Bereichs-ID, die Zeile `$realm = Get-SPAuthenticationRealm` in den Skripts gibt jedoch immer den Bereich der Farm zurück. Die von einem Tokenaussteller ausgegebenen Zugriffstoken werden von SharePoint nur für den Bereich als gültig angesehen, der in der vollständigen Aussteller-ID nach dem „@"-Zeichen angegeben ist. Um den korrekten Bereich für die Website abzurufen, in dem das Add-In installiert wird, muss das Skript den `-ServiceContext`-Parameter in dem Aufruf an  `Get-SPAuthenticationRealm` verwenden. Das Dienstkontextobjekt wird wiederum aus der übergeordneten Websitesammlung der Zielwebsite erstellt. Es folgt ein Beispiel, bei dem `$WebURL` eine Zeichenfolgenvariable mit der vollständigen URL einer SharePoint-Website darstellt, z. B. „http://marketing.contoso.com/sites/northteam/july." Mit diesem Code kann das besonders vertrauenswürdige Add-In ausgeführt werden, nicht nur auf der angegeben Website, sondern auf jeder Website innerhalb des gleichen Websiteabonnements.




```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc
```

Um die Änderung des Skripts abzuschließen, fügen Sie oben in der Datei einen zusätzlichen erforderlichen Parameter  `$WebURL` wie folgt zur Parameterliste hinzu:






```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)
```

Achten Sie darauf, nach dem Parameter ein Komma zu setzen, der vor dem neuen steht. Und erweitern Sie das Verwendungsbeispiel in der oberen Zeile, um den neuen Parameter folgendermaßen zu berücksichtigen:  `-WebURL <full URL where SP add-in will be installed>`.



Damit die SharePoint-Add-In bei jedem Websiteabonnement vertrauenswürdig ist, müssen Sie mit dem  `Get-SPFarm`-Cmdlet einen Verweis zur Farm abrufen, und anschließend die **SiteSubscriptions**-Eigenschaft durchlaufen. Übergeben Sie jedes **SPSiteSubscription**-Objekt als Wert des  `-SiteSubscription`-Parameters an das  `Get-SPServiceContext`-Cmdlet. Im Folgenden finden Sie dazu ein Beispiel.






```

$Farm = Get-SPFarm
foreach ($ss in $Farm.SiteSubscriptions)
{
    $sc = Get-SPServiceContext -SiteSubscription $ss
    $realm = Get-SPAuthenticationRealm -ServiceContext $sc

    # All of the lines from the draft script below the call to 
    # Get-SPAuthenticationRealm are inserted here inside the loop.
}
# end of script
```


