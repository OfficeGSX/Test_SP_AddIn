

# Tipps und FAQ: OAuth und Remote-Apps für SharePoint
Dieses Thema ist jetzt veraltet. Unter den Links unten finden Sie Informationen dazu, wo sich die Inhalte jetzt befinden. 
 * **Gilt für:*** 
  
    
    


||
|:-----|
|**In diesem Artikel**          [Abrufen von App-Informationen](#AppInfo)           [Die Datei "AppManifest.xml"](#Manifest)           [Die Datei "Web.config"](#Webconfig)           [URLs und SSL (Secure Sockets Layer)](#URL)           [Die App-Umleitungsseite ](#Redirect)           [Umleitungs-URIs](#RedirectURI)           [OAuth-Token](#Tokens)           [Berechtigungen und Berechtigungsanforderungsbereiche](#Perm)           [OAuth-Autorisierungsrichtlinientypen](#Policy)           [Debuggen](#Debugging)           [Besonders vertrauenswürdige lokale Apps (Server-zu-Server-Apps)](#S2S)           [Weitere Fragen in Zusammenhang mit OAuth](#Misc)           [Social Features](#Social)           [Zusätzliche Ressourcen](#AR)|
   

## Abrufen von App-Informationen
<a name="AppInfo"> </a>

Dieser Inhalt wurde zu  [Abrufen von Informationen zur Add-In-Registrierung und zum Add-In-Prinzipal ](register-sharepoint-add-ins-2013.md#Retrieve) verschoben.
  
    
    

## Die Datei "AppManifest.xml"
<a name="Manifest"> </a>

Dieser Inhalt wurde verschoben zu:
  
    
    

-  [Hinweise zur App-Manifeststruktur und zum Paket eines SharePoint-Add-Ins](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Schemareferenz für Manifeste von Apps für SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  

### Wie lautet die URL im Element <StartPage>?

Dieser Inhalt wurde verschoben zu: 
  
    
    


  
    
    
>  [Hinweise zur App-Manifeststruktur und zum Paket eines SharePoint-Add-Ins](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

  
    
    
>  [StartPage-Element](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)
    
  

### Welche Elemente und Attribute hat die Datei AppManifest.xml?

Dieser Inhalt wurde zu  [Schemareferenz für Manifeste von Apps für SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx) verschoben.
  
    
    

## Die Datei "Web.config"
<a name="Webconfig"> </a>

Dieser Inhalt wurde zu  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md) verschoben, insbesondere der Abschnitt [Eingeben der Registrierungswerte in die Dateien web.config und AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

### Welches sind die App-Einstellungen der Datei Web.config?

Dieser Inhalt wurde zu  [Eingeben der Registrierungswerte in die Dateien web.config und AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles) verschoben.
  
    
    

## URLs und SSL (Secure Sockets Layer)
<a name="URL"> </a>

Dieser Inhalt wurde verschoben zu: 
  
    
    

-  [Verschiedene SSL- und domänenbezogene Fehler in besonders vertrauenswürdigen Apps](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Verschiedene SSL- und domänenbezogene Fehler in ACS-Apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Meine Webanwendung hat Probleme beim Rückrufen an SharePoint. Was soll ich überprüfen?

Dieser Inhalt wurde verschoben zu: 
  
    
    

-  [Verschiedene SSL- und domänenbezogene Fehler in besonders vertrauenswürdigen Apps](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Verschiedene SSL- und domänenbezogene Fehler in ACS-Apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Welche URLs soll ich als Hardcode in meine App eingeben, um auf meinen Cloud-Server zu verweisen?

Dieser Inhalt wurde verschoben zu: 
  
    
    

-  [Verschiedene SSL- und domänenbezogene Fehler in besonders vertrauenswürdigen Apps](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Verschiedene SSL- und domänenbezogene Fehler in ACS-Apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Soll ich den CNAME-Alias registrieren oder die tatsächliche, zugrunde liegende URL, die die App hostet?

Dieser Inhalt wurde verschoben zu: 
  
    
    

-  [Verschiedene SSL- und domänenbezogene Fehler in besonders vertrauenswürdigen Apps](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Verschiedene SSL- und domänenbezogene Fehler in ACS-Apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Ich erhalte die Fehlermeldung "Die zugrunde liegende Verbindung wurde geschlossen: Für den geschützten SSL/TLS-Kanal konnte keine Vertrauensstellung hergestellt werden." Was soll ich tun?

Dieser Inhalt wurde zu  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed) verschoben.
  
    
    

## Die App-Umleitungsseite
<a name="Redirect"> </a>

Dieser Inhalt wurde zu  [Abrufen eines neuen Kontexttokens](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken) verschoben.
  
    
    

### Wofür wird die App-Umleitungsseite verwendet?

Dieser Inhalt wurde zu  [Abrufen eines neuen Kontexttokens](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken) verschoben.
  
    
    

### Wie verwende ich eine App-Umleitungsseite zum Abrufen des Kontext-Tokens?

Dieser Inhalt wurde zu  [Abrufen eines neuen Kontexttokens](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken) verschoben.
  
    
    

### Wie verwende ich die appredirect-Seite in der URL?

Dieser Inhalt wurde zu  [Abrufen eines neuen Kontexttokens](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken) verschoben.
  
    
    

## Umleitungs-URIs
<a name="RedirectURI"> </a>

Dieser Inhalt wurde zu  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md) verschoben.
  
    
    

### Brauche ich eine Umleitungs-URI?

Dieser Inhalt wurde zu  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md) verschoben.
  
    
    

## OAuth-Token
<a name="Tokens"> </a>

Dieser Inhalt wurde verschoben zu:
  
    
    

-  [Handhabung von Sicherheitstoken in vom Anbieter gehosteten Add-Ins für SharePoint mit niedriger Vertrauensebene](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)
    
  
-  [Erstellen und Verwenden von Zugriffstoken in vom Anbieter gehostete besonders vertrauenswürdige SharePoint-Add-Ins](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
    
  

### Was ist ein Kontext-Token?

Dieser Inhalt wurde zu  [Überblick über die Struktur und Handhabung von Kontexttoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens) verschoben.
  
    
    

### Was ist ein Zugriffstoken?

Dieser Inhalt wurde zu  [Übersicht über die Handhabung von Zugriffstoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens) verschoben.
  
    
    

### Was ist ein Aktualisierungstoken?

Dieser Inhalt wurde zu  [Überblick über die Handhabung und das Zwischenspeichern von Aktualisierungstoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens) verschoben.
  
    
    

### Soll die Kontext-Token-Zeichenfolge in einem Cookie gespeichert werden, damit sie für andere Seitenanforderungen direkt aus der anbietergehosteten App verwendet werden kann?

Dieser Inhalt wurde zu  [Zwischenspeichern des Kontexttokens oder Teilen davon](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken) verschoben.
  
    
    

### Woraus setzt sich der Cache-Schlüsselwert zusammen? Inwiefern ist er eindeutig?

Dieser Inhalt wurde zu  [Überblick über den Cacheschlüssel](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey) verschoben.
  
    
    

### Wie rufe ich den Kontext-Token ab?

Dieser Inhalt wurde zu  [Überblick über die Struktur und Handhabung von Kontexttoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens) verschoben.
  
    
    

### Welche Informationen enthält der Kontext-Token?

Dieser Inhalt wurde zu  [Beispiel für ein Kontexttoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken) verschoben.
  
    
    

### Welche Informationen enthält ein Zugriffstoken?

Dieser Inhalt wurde verschoben zu:
  
    
    

-  [Beispiele für Zugriffstoken im ACS-Autorisierungssystem](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)
    
  
-  [Beispiel für ein Zugriffstoken im besonders vertrauenswürdigen Autorisierungssystem](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)
    
  

### Wie berechne ich die genaue Uhrzeit und das Datum aus dem Wert von nbf und exp?

Dieser Inhalt wurde zu  [Arbeiten mit JWT-Zeitwerten](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes) verschoben.
  
    
    

### Ich möchte den SVC für meine Apps vor Benutzern außerhalb von SharePoint schützen. Ich überprüfe die Rechtmäßigkeit des Benutzers am Eingangspunkt der App (durch die Erstellung von ClientContext), aber mein WCF-Dienst kann von jedem beliebigen Benutzer aufgerufen werden. Soll ich bei jedem SVC-Methodenaufruf ClientContext aus einem Kontext-Token erstellen?

Dieser Inhalt wurde zu  [Verwenden des Kontexttokens, um den Zugriff nur auf SharePoint-Benutzer einzuschränken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess) verschoben.
  
    
    

### Kann ich AppContext (mit einer SharePoint-POST-Anforderung abgerufen) als verdecktes Eingabefeld auf der Seite behalten?

Dieser Inhalt wurde zu  [Zwischenspeichern des Kontexttokens oder Teilen davon](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken) verschoben.
  
    
    

### Wie lange ist ein Aktualisierungstoken gültig?

Dieser Inhalt wurde zu  [Überblick über die Handhabung und das Zwischenspeichern von Aktualisierungstoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens) verschoben.
  
    
    

### Ich speichere den Zugriffstoken und die Host-URL in Cookies, sodass sie in anderen Seitenanforderungen verwendet werden können. Der Benutzer hat jedoch eine Pause gemacht und der Zugriffstoken ist abgelaufen. Was soll ich tun?

Dieser Inhalt wurde verschoben zu:
  
    
    

-  [Übersicht über die Handhabung von Zugriffstoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)
    
  
-  [Überblick über die Handhabung und das Zwischenspeichern von Aktualisierungstoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)
    
  

### In welchem Szenario sollte ich einen alten, noch nicht abgelaufenen Aktualisierungstoken, der noch gültig ist, verwerfen und einen neuen verwenden?

Dieser Inhalt wurde zu  [Überblick über die Handhabung und das Zwischenspeichern von Aktualisierungstoken](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens) verschoben.
  
    
    

## Berechtigungen und Berechtigungsanforderungsbereiche
<a name="Perm"> </a>

Dieser Inhalt befindet sich unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Welches sind die Berechtigungsanforderungsbereiche und verfügbaren Rechte für Liste, Bibliotheksinhalt und andere Features?

Dieser Inhalt befindet sich jetzt unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## OAuth-Autorisierungsrichtlinientypen
<a name="Policy"> </a>

Dieser Inhalt befindet sich unter  [Add-In-Autorisierungsrichtlinientypen in SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### Worin besteht der Unterschied zwischen der Richtlinie nur für Apps und der Richtlinie für Benutzer und Apps?

Dieser Inhalt befindet sich unter  [Add-In-Autorisierungsrichtlinientypen in SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### Gibt es eine Möglichkeit, das Recht zum Starten einer App zu erteilen oder zu verweigern?

Dieser Inhalt befindet sich unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Debuggen
<a name="Debugging"> </a>

Dieser Inhalt wurde verschoben zu: 
  
    
    

-  [Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Problembehandlung bei Add-Ins für SharePoint 2013 mit niedriger Vertrauensebene](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Verwenden von Fiddler

Dieser Inhalt wurde verschoben zu:
  
    
    

-  [Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Problembehandlung bei Add-Ins für SharePoint 2013 mit niedriger Vertrauensebene](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

## Besonders vertrauenswürdige lokale Apps (Server-zu-Server-Apps)
<a name="S2S"> </a>

Dieser Inhalt wurde zu  [Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins](troubleshooting-high-trust-sharepoint-add-ins.md) verschoben.
  
    
    

### Ich erhalte beim Ausführen einer hochzuverlässigen App den Fehler "401 (Nicht autorisiert)". Was soll ich tun?

Dieser Inhalt wurde zu  [Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins](troubleshooting-high-trust-sharepoint-add-ins.md) verschoben.
  
    
    

### Wie erhalte ich einen Kontext-Token für eine hochzuverlässige App?

Dieser Inhalt wurde zu  [Erstellen und Verwenden von Zugriffstoken in vom Anbieter gehostete besonders vertrauenswürdige SharePoint-Add-Ins](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md) verschoben.
  
    
    

## Weitere Fragen in Zusammenhang mit OAuth
<a name="Misc"> </a>

Dieser Inhalt wurde verschoben zu:
  
    
    

-  [Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Problembehandlung bei Add-Ins für SharePoint 2013 mit niedriger Vertrauensebene](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Wenn ich versuche, eine Datei mit der HTTP DAV-Methode zu lesen, erhalte ich einen Fehler. Was soll ich tun?

Dieser Inhalt wurde zu  [Problembehandlung bei Add-Ins für SharePoint 2013 mit niedriger Vertrauensebene](44ead3b9-75c3-4f68-b908-0af6197f1143.md) verschoben.
  
    
    

### Gibt es eine Möglichkeit, die OAuth-Autorisierung an andere Komponenten in anderen Domänen weiterzuleiten oder OAuth für mehrere URIs zu konfigurieren?

Ja. Weitere Informationen finden Sie unter  [Weiterleiten des Zugriffstokens an Back-End-Systeme](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend).
  
    
    

### Ist der SharePoint 2013-Prinzipalwert konstant?

Ja. Weitere Informationen finden Sie unter  [Beispiele für Zugriffstoken im ACS-Autorisierungssystem](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (insbesondere in Tabelle 1).
  
    
    

### Sind die App-ID und der geheime App-Schlüssel für eine bestimmte App mandantenübergreifend konstant?

Ja, wenn die App im Verkäuferdashboard registriert ist. Wenn sie jedoch separat bei allen SharePoint-Mandanten oder -Farmen registriert ist, können für jede unterschiedliche IDs und geheime Schlüssel vorhanden sein. In diesem Fall wirkt die App für Microsoft Azure Access Control Service (ACS) wie mehrere Apps. Weitere Informationen finden Sie unter  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Sind Bereiche eindeutig?

Ja. Weitere Informationen finden Sie unter  [Beispiele für Zugriffstoken im ACS-Autorisierungssystem](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (insbesondere Tabelle 1) und [Verwenden des Kontexttokens zum Abrufen eines Zugriffstokens](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken).
  
    
    

### Wie deaktiviere ich die HTTPS-Anforderung für OAuth während der Entwicklung?

Dieser Inhalt wurde zu  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement) verschoben.
  
    
    

## Social Features
<a name="Social"> </a>

Dieser Inhalt wurde verschoben zu:
  
    
    

1.  [Soziale Funktionen und Zusammenarbeit in SharePoint 2013](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)
    
  
2.  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  

### Wie rufe ich die Identität und Eigenschaften eines Benutzers ab?

Weitere Informationen hierzu finden Sie unter  [Abrufen von Benutzeridentität und Eigenschaften in SharePoint 2013](get-user-identity-and-properties-in-sharepoint-2013.md).
  
    
    

### Wofür werden die verschiedenen Social Features und Berechtigungsanforderungsbereiche verwendet?

Dieser Inhalt befindet sich unter  [Erste Schritte bei der Entwicklung mit thematischen Features in SharePoint 2013](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms).
  
    
    

### Wie erhalte ich die Benutzerprofileigenschaften von Personen, die mir folgen?

Dieser Inhalt wurde zu  [Vorgehensweise: Abrufen von Benutzerprofileigenschaften mithilfe des .NET-Clientobjektmodells in SharePoint 2013](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp) verschoben.
  
    
    