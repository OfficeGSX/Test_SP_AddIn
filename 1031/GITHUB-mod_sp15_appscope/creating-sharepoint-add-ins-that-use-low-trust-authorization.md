---
title: Erstellen von SharePoint-Add-Ins, die die Autorisierung mit niedriger Vertrauensebene verwenden
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# Erstellen von SharePoint-Add-Ins, die die Autorisierung mit niedriger Vertrauensebene verwenden
Erfahren Sie mehr über das Autorisierungssystem mit niedriger Vertrauensebene für SharePoint-Add-Ins.
Remotekomponenten in einem SharePoint-Add-In (oder einer externen Anwendung) können die Autorisierung für SharePoint-Ressourcen erteilen, indem mit jeder HTTP-Anforderung ein Zugriffstoken an SharePoint weitergegeben wird. Die Remotekomponenten rufen das Zugriffstoken aus einem Microsoft Azure Access Control Service (ACS)-Konto ab, das dem Office 365-Mandanten zugeordnet ist. Azure ACS dient als Autorisierungsserver in einer  [OAuth 2.0](http://oauth.net/)-Transaktion, die einen Ablauf aufruft, dabei dient SharePoint als Ressourcenserver und die Remotekomponenten als der Client. Entsprechende Protokollspezifikationen finden Sie unter [Web-Autorisierungsprotokoll (oauth)](http://datatracker.ietf.org/doc/active/#oauth). 
  
    
    

Vom Anbieter gehostete SharePoint-Add-Ins, die das Autorisierungssystem mit niedriger Vertrauensebene verwenden, können im Office Store verkauft und in Microsoft SharePoint Online oder einer lokalen SharePoint-Farm installiert werden, die so konfiguriert ist, dass sie zum Einrichten einer Vertrauensstellung mit Azure ACS den Office 365-Mandanten des Kunden verwendet. Der Kunde muss einen Office 365-Mandanten besitzen, um SharePoint-Add-Ins zu installieren, die das System mit niedriger Vertrauensstellung verwenden. Der Kunde benötigt den Mandanten jedoch zu keinem anderen Zweck. Anweisungen zum Verknüpfen einer lokalen Farm mit einem Office 365-Mandanten finden Sie unter  [Verwenden einer Office 365 SharePoint-Website, um vom Anbieter gehostete Add-Ins auf einer lokalen SharePoint-Website zu autorisieren](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md).
## Registrierung auf Azure ACS
<a name="Registration"> </a>

Zum Verwenden des Systems mit niedriger Vertrauensstellung muss das SharePoint-Add-In zunächst bei Azure ACS und dem App-Verwaltungsdienst der SharePoint-Farm oder des SharePoint Online-Mandanten registriert werden. (Er wird "App-Verwaltungsdienst" genannt, da SharePoint-Add-Ins ursprünglich "Apps für SharePoint" genannt wurden). Bei Add-Ins, die über den Office Store verkauft werden, erfolgt die Registrierung bei ACS im Verkäuferdashboard, und die Registrierung bei dem Dienst wird bei der Installation des Add-Ins durchgeführt. Bei Add-Ins, die im Add-In-Katalog der Organisation verteilt werden, erfolgt die Registrierung bei ACS und dem Dienst auf der \\_Layouts\\15\\AppRegNew.aspx-Seite eines SharePoint-Mandanten oder einer Farm, auf der das Add-In installiert werden soll. Externe, Nicht-SharePoint-Anwendungen, die auf SharePoint zugreifen, müssen ebenfalls registriert werden. Diese Kategorie umfasst Office-Add-Ins, Windows Store-Apps, Webanwendungen und Geräte-Apps, wie z. B. Smartphone-Apps.
  
    
    

> **HINWEIS**
> Für die Registrierung muss die Anwendung über eine Internetdomäne verfügen. Zu diesem Zweck kann jede vorhandene Domäne verwendet werden, Sie können jedoch nicht zu 100 % sicher sein, dass eine Domäne, die Sie nicht besitzen, auch existiert, deshalb muss eine Webanwendung Teil einer systemeigenen Geräteanwendung sein, selbst wenn die Webanwendungskomponente nur zum Aktivieren der Registrierung dient. Weitere Informationen finden Sie in einem so konzipierten Codebeispiel unter  [Bereitstellen von Websites in Batches mithilfe des Add-In-Modells](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6). 
  
    
    

Weitere Informationen zur Registrierung finden Sie unter  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Ablauf von geheimen Add-In-Schlüsseln

Der geheime Add-In-Schlüssel muss alle 12 Monate ausgetauscht werden. Details dazu finden Sie unter  [Austauschen eines ablaufenden geheimen Clientschlüssels in einem Add-In für SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md).
  
    
    

## Autorisierungsrichtlinien
<a name="Policies"> </a>

Ein SharePoint-Add-In kann so konzipiert werden, dass es eine von zwei Autorisierungsrichtlinien verwendet:
  
    
    

- **Benutzer- und Add-In-Richtlinie:** Add-Ins, die diese Richtlinie verwenden, können nur Aktionen ausführen, für die sowohl das Add-In als auch der Benutzer Berechtigungen besitzen. Dies ist die standardmäßig verwendete Richtlinie, sofern der Entwickler keine bestimmten Schritte zur Verwendung der anderen Richtlinie unternimmt.
    
  
- **Nur-Add-In-Richtlinie:** Add-Ins, die diese Richtlinie verwenden, können jede Aktion ausführen, für die sie eine Berechtigung besitzen, selbst wenn der Benutzer die Berechtigung für die Aktion nicht besitzt. Der Entwickler muss eine Anforderung senden, damit diese Richtlinie im Add-In-Manifest des Add-Ins verwendet wird. Die Anforderung muss von dem Benutzer genehmigt werden, der das Add-In installiert. Diese Richtlinie ist nur für vom Anbieter gehostete SharePoint-Add-Ins zulässig.
    
  
Weitere Informationen über Autorisierungsrichtlinien finden Sie unter  [Add-In-Autorisierungsrichtlinientypen in SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Zwei OAuth-Laufzeitabläufe
<a name="Flows"> </a>

Bei jedem Ausführen eines in der Cloud gehosteten SharePoint-Add-In oder einer externen Anwendung, die auf SharePoint zugreift, tritt ein Ablauf - eine Reihe von Interaktionen zwischen dem Add-In, SharePoint 2013, ACS und manchmal dem Endbenutzer - auf. Das Endergebnis des Ablaufs besteht darin, dass SharePoint ein bei Erstell-, Aktualisierungs-, Löschanforderungen enthaltenes Zugriffstoken erhält, die die Anwendung an SharePoint senden.
  
    
    
SharePoint verwendet zwei große OAuth-Abläufe. Einer ist für in der Cloud gehostete SharePoint-Add-Ins. Der andere „fließende" ist für Anwendungen auf anderen Plattformen, die auf SharePoint-Daten zugreifen.
  
    
    

- **Ablauf mit Kontexttoken:** Die Remotekomponente der SharePoint-Add-In verwendet das SharePoint-Clientobjektmodell (CSOM) oder REST-Endpunkte, um Aufrufe an SharePoint auszuführen. SharePoint fordert ein Kontexttoken von ACS an, das es an den Remoteserver senden kann. Der Remoteserver verwendet das Kontexttoken, um ein Zugriffstoken bei ACS anzufordern. Der Remoteserver verwendet das Zugriffstoken anschließend, um mit SharePoint zu kommunizieren. Details zu diesem Ablauf finden Sie unter [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).
    
  
- **Ablauf mit Authentifizierungscode:** Ein SharePoint-Add-In erhält die benötigten Berechtigungen für den Zugriff auf SharePoint-Ressourcen, wenn es installiert wird. Anwendungen, die nicht in SharePoint installiert sind, müssen jedoch „spontan" Berechtigungen anfordern, d. h., jedes Mal, wenn sie ausgeführt werden. In diesem Ablauf ist kein SharePoint-Kontexttoken vorhanden. Wenn das Add-In ausgeführt wird und versucht, auf SharePoint zuzugreifen, fordert SharePoint den Benutzer stattdessen dazu auf, Berechtigungen für die Anwendung zu gewähren, die es anfordert. Wenn der Benutzer die Berechtigungen gewährt, ruft SharePoint einen Autorisierungscode von ACS ab, der an die Anwendung weitergegeben wird. Die Anwendung verwendet den Code, um ein Zugriffstoken von ACS abzurufen, das anschließend für die Kommunikation mit SharePoint verwendet werden kann. Details zu diesem Ablauf finden Sie unter [OAuth-Ablauf mit Authentifizierungscode für SharePoint-Add-Ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
    
  

## Problembehandlung bei SharePoint-Add-Ins mit niedriger Vertrauensebene
<a name="Trouble"> </a>

In diesem Artikel finden Sie einige allgemeine Anleitungen zur Problembehandlung und Informationen zu einigen speziellen Problemen mit SharePoint-Add-Ins, die das Autorisierungssystem mit niedriger Vertrauensebene verwenden.
  
    
    

### Verwenden des Fiddler-Tools

Sie können das kostenlose  [Fiddler-Tool](http://www.telerik.com/fiddler) verwenden, um die HTTP-Anforderungen zu erfassen, die von der Remotekomponente Ihres Add-Ins an SharePoint gesendet werden. Es gibt eine [kostenlose Erweiterung des Tools](https://github.com/andrewconnell/SPOAuthFiddlerExt), das die Zugriffstoken in den Anforderungen automatisch decodiert.
  
    
    

### Deaktivieren der HTTPS-Anforderung für OAuth während der Entwicklung
<a name="TurnOffHTTPRequirement"> </a>

OAuth erfordert, dass SharePoint HTTPS ausführt (nicht nur Ihr Dienst, sondern auch SharePoint). Dies kann bei der Entwicklung des Add-Ins hinderlich sein. Sie erhalten zum Beispiel eine 403-Fehlermeldung (unzulässig), wenn Sie versuchen, beim Debuggen des Add-Ins einen Aufruf an SharePoint zu richten, da der „localhost", auf dem das Add-In ausgeführt wird, keine SSL-Unterstützung bietet.
  
    
    
Sie können die HTTPS-Anforderung während der Entwicklung mithilfe der folgenden Windows PowerShell-Cmdlets deaktivieren.
  
    
    


```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()
```

Um die HTTPS-Anforderung später wieder zu aktivieren, verwenden Sie die folgenden Windows PowerShell-Cmdlets.
  
    
    


```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()
```


### Verschiedene SSL- und domänenrelevante Autorisierungsfehler
<a name="DomainRelatedErrors"> </a>

Die fehlende Übereinstimmung von Domänennamen in Konfigurationsdateien und Registrierungsformularen kann die Autorisierung verhindern. Die folgenden vier Werte müssen exakt gleich sein:
  
    
    

- Die **Add-In-Domäne**, die bei der SharePoint-Add-In-Registrierung auf „AppRegNew.aspx" oder im Verkäuferdashboard angegeben wird.
    
  
- Die Domäne, unter der das Sicherheitszertifikat der Remotewebanwendung registriert ist.
    
  
- Der Domänenteil des Werts **StartPage** in der Datei „AppManifest.xml".
    
  
- Der Domänenteil der URL aller Ereignisempfänger, die in AppManifest.xml angegeben sind.
    
  
Beachten Sie im Zusammenhang mit diesem Punkt Folgendes:
  
    
    

- Wenn die Remotekomponente Ihres SharePoint-Add-In einen anderen Port als 443 verwendet, müssen Sie den Port ausdrücklich als Teil der Domäne an allen vier Stellen einschließen, beispielsweise  `contoso.com:3333`. (Sie müssen das HTTPS-Protokoll verwenden, für das der Standardport 443 ist.)
    
  
- Wenn Sie einen DNS CNAME-Alias für Ihre Remotewebanwendung erstellen, verwenden Sie an allen vier Stellen den CNAME-Alias für den Domänenwert. Wenn Ihre Anwendung beispielsweise unter contoso.cloudapp.net gehostet wird und Sie den CNAME contososoftware.com dafür erstellen, verwenden Sie contososoftware.com als Domäne.
    
  
- Die Domäne muss im **StartPage**-Wert (und allen Ereignisempfänger-URLs) der Datei „AppManifest.xml" hartcodiert sein, bevor das Add-In gepackt wird. Wenn Sie den Assistenten **Veröffentlichen** in Visual Studio zum Packen des Add-Ins verwenden, werden Sie nach der Domäne gefragt. Office-Entwicklertools für Visual Studio fügt die Domäne für Sie in den **StartPage**-Wert ein (an der Stelle des  `~remoteWebUrl`-Tokens, das während des Debuggens verwendet wird). Wenn Sie den Assistenten **Veröffentlichen** nicht verwenden, oder auch dann, wenn Sie ihn verwenden, Ihre Remotewebanwendung jedoch in Azure bereitgestellt wird, müssen Sie das Token manuell durch die Domäne (und das Protokoll) ersetzen, z. B `https://contososoftware.com` oder `https://MyCloudVM:3333`.
    
  

### Fehler „Die zugrunde liegende Verbindung wurde geschlossen: Für den geschützten SSL/TLS-Kanal konnte keine Vertrauensstellung hergestellt werden."
<a name="ErrorConnectionClosed"> </a>

Dieser Fehler ist ein SSL Handshake-, kein OAuth-Problem. Stellen Sie sicher, dass das von Ihnen verwendete Zertifikat für SharePoint vertrauenswürdig ist und das Zertifikat mit dem Endpunktnamen übereinstimmt.
  
    
    

### Fehler beim Verwenden der HTTP DAV-Methode zum Lesen von Dateien aus SharePoint
<a name="ErrorConnectionClosed"> </a>

HTTP DAV funktioniert nicht zusammen mit OAuth. Wenn Sie das SharePoint-Clientobjektmodell (CSOM) nutzen, verwenden Sie zum Lesen einer Datei den folgenden Code.
  
    
    
```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();
```


## Inhalt dieses Abschnitts
<a name="Trouble"> </a>

 [Verwenden einer Office 365 SharePoint-Website, um vom Anbieter gehostete Add-Ins auf einer lokalen SharePoint-Website zu autorisieren](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)
  
    
    
 [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [OAuth-Ablauf mit Authentifizierungscode für SharePoint-Add-Ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Austauschen eines ablaufenden geheimen Clientschlüssels in einem Add-In für SharePoint](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)
  
    
    

## Zusätzliche Ressourcen
<a name="FileName_AdditionalResources"> </a>


-  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md)
    
  

