---
title: Erstellen und Verwenden von Zugriffstoken in vom Anbieter gehostete besonders vertrauenswürdige SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: cb3264d4-41a6-498f-a408-75b077566051
---


# Erstellen und Verwenden von Zugriffstoken in vom Anbieter gehostete besonders vertrauenswürdige SharePoint-Add-Ins
Erfahren Sie mehr über die Rolle von Zugriffstoken in besonders vertrauenswürdigen Add-Ins für SharePoint und wir Ihr Code mit diesen zusammenarbeitet.
> **WICHTIG**
> **In diesem Artikel geht es nur um die Verwendung von Zugriffstokens im besonders vertrauenswürdigen Autorisierungssystem, und nicht um das ACS-System.** Informationen zur Verwendung von Sicherheitstokens im ACS-System finden Sie unter [Handhabung von Sicherheitstoken in vom Anbieter gehosteten Add-Ins für SharePoint mit niedriger Vertrauensebene](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md). 
  
    
    

 **SharePoint-Add-Ins, die das  [besonders vertrauenswürdige Autorisierungssystem](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) verwenden, um auf SharePoint zuzugreifen, müssen ein Zugriffstoken** (im [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)-Format) mit jeder CRUD-Anforderung (Erstellen, Lesen, Aktualisieren oder Löschen) an SharePoint übergeben. SharePoint validiert das Token und verarbeitet die Anforderung. In diesem Artikel finden Sie Informationen dazu, wie der Code das Zugriffstoken erstellt und übergibt.
Im besonders vertrauenswürdigen Autorisierungssystem erstellt **die Remotekomponente Ihres SharePoint-Add-In das Zugriffstoken**. Wenn die Remotekomponente verwalteten Code für ihren serverseitigen Code verwendet, wird der größte Teil der Codierungsarbeit für das Erstellen der Token in den Dateien SharePointContext.cs (oder .vb) und TokenHelper.cs (oder .vb) für Sie durchgeführt, die in Office-Entwicklertools für Visual Studio enthalten sind. Da der Kunde für ein besonders vertrauenswürdiges SharePoint-Add-In über eine lokale SharePoint-Version verfügt, ist er wahrscheinlich nicht abgeneigt, ASP.NET, IIS und Windows Server als Hosting-Stapel für die Remotekomponente zu verwenden. Sie sollten die Verwendung dieses Stapels in Betracht ziehen, da die zwei generierten Dateien Ihnen viel Codierungs- und Testarbeit abnehmen. Wenn Sie für die Remotekomponente eine Nicht-.NET-Sprache verwenden müssen und sowohl die Remotekomponente als auch die SharePoint-Farm mit dem Internet verbunden sind, sollten Sie die Verwendung des [Autorisierungssystem mit niedriger Vertrauensebene](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) anstelle des besonders vertrauenswürdigen Autorisierungssystems in Betracht ziehen. Im Microsoft Azure Access Control Service (ACS) erfolgt die gesamte Tokenkonstruktion über ACS, sodass Ihnen ebenfalls eine Menge Arbeit erspart wird. Im restlichen Teil des Artikels finden Sie hauptsächlich Anweisungen für Entwickler, die SharePoint-Add-Ins mit Nicht-.NET-Remotekomponenten erstellen und das besonders vertrauenswürdige Autorisierungssystem verwenden. Sie finden außerdem einige nützliche Informationen für das Debugging von .NET-basierten SharePoint-Add-Ins, die das besonders vertrauenswürdige System verwenden.
  
    
    


## Übersicht über die Handhabung von Zugriffstoken
<a name="AccessTokens"> </a>


> **HINWEIS**
>  Denken Sie bei der Lektüre dieses Artikels, insbesondere in Bezug auf Aufgaben, die Ihr Code durchführen muss, daran, dass bei der Verwendung von verwaltetem Code die Microsoft Office-Entwicklertools für Visual Studio jedem SharePoint-Add-In-Projekt zwei generierte Codedateien, SharePointContext.cs (oder .vb) und TokenHelper.cs (oder .vb) hinzufügen, die den größten Teil der Arbeit für Sie übernehmen. Der Tokenverarbeitungscode Ihrer Anwendung besteht für gewöhnlich aus nur wenigen Aufrufen an die Klassen in diesen Dateien. Die ausführlichen Informationen in diesem Thema helfen Entwicklern, die keinen verwalteten Code verwenden (bei der Behandlung von Problemen mit Tokens).>  Links zu OAuth-Bibliotheken für viele Sprachen und Plattformen finden Sie unter:>  [OAuth 2.0](http://oauth.net/2/) Blättern Sie zu **Clientbibliotheken**.>  Weitere Informationen finden Sie, indem Sie [github](https://github.com/) nach "OAuth 2" und "JSON Web Token" (ohne Anführungszeichen) durchsuchen.
  
    
    

Die folgenden wichtigen **Dinge muss Ihr Code übernehmen**:
  
    
    

1. **Erstellen eines Zugriffstokens.** Die Teilaufgaben für das Erstellen dieses Tokens sind unterschiedlich, je nachdem, ob die RemotewebanwendungNur-Add-In-Aufrufe an SharePoint, Benutzer-und-Add-In-Aufrufe oder beides durchführt. (Informationen zu den zwei Arten von Aufrufen finden Sie unter  [Add-In-Autorisierungsrichtlinientypen in SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).) 
    
    Wenn das Add-In Benutzer-und-Add-In-Aufrufe durchführt, umfasst das Erstellen des Zugriffstokens die folgenden Teilaufgaben:
    
1. Erstellen eines Actor-Tokens, das das SharePoint-Add-In identifiziert und SharePoint mitteilt, die Benutzerauthentifizierung und -autorisierung an das Add-In zu delegieren und es in der Base64-Codierung zu codieren. Ausführliche Informationen zu den Ansprüchen und der Struktur des Actor-Tokens finden Sie in Tabelle 2 unten. Details zum Codieren und Signieren des Tokens finden Sie unter  [Codieren und Signieren von Token](#EncodingTokens) weiter unten.
    
  
2. Signieren Sie das Actor-Token mit Anmeldeinformationen aus einem x509-Zertifikat, das ein SharePoint-Farmadministrator so konfiguriert hat, dass SharePoint ihm vertraut.
    
  
3. Schließen Sie das Actor-Token in das Zugriffstoken ein.
    
  
4. Fügen Sie dem Zugriffstoken weitere erforderliche Ansprüche hinzu. Ausführliche Informationen zu den Ansprüchen und der Struktur des Tokens finden Sie in Tabelle 1 unten.
    
  
5. Codieren Sie das Zugriffstoken mit Base64. Details zum Codieren und Signieren des Tokens finden Sie unter  [Codieren und Signieren von Token](#EncodingTokens) weiter unten.
    
  

    Wenn das Add-In Nur-Add-In-Aufrufe durchführt, muss Ihr Code nur die ersten zwei dieser Teilaufgaben ausführen. Das Actor-Token dient als Zugriffstoken.
    
    Wenn das Add-In einige Benutzer-und-Add-In-Aufrufe und einige Nur-Add-In-Aufrufe durchführt, muss sie ein einfaches Actor-Token für die Nur-Add-In-Aufrufe und das größere, verschachtelte Zugriffstoken für die Benutzer-und-App-Aufrufe erstellen. Es können nicht dieselben Zugriffstoken für beide Arten von Aufrufen verwendet werden.
    
  
2. **Schließen Sie das Zugriffstoken in jede HTTP-Anforderung an SharePoint ein.** Das Token wird als ein **Authorization**-Header an die Anforderung angehängt. Der Wert des Headers ist das Wort "Bearer", gefolgt von einem Leerzeichen, gefolgt von dem Base64-codierten Zugriffstoken.
    
  
3. Optional können Sie **das Zugriffstoken zwischenspeichern**, damit es in nachfolgenden Anforderungen erneut verwendet werden kann.
    
  
Diese Aufgaben müssen mit serverseitigem Code durchgeführt werden. Wenn Sie verwalteten Code verwenden, befindet sich Beispielcode für einige dieser Aufgaben in den Dateien SharePointContext.cs (oder .vb) und TokenHelper.cs (oder .vb), die von den Microsoft Office-Entwicklertools für Visual Studio generiert werden.
  
    
    

### Zwischenspeichern des Zugriffstokens
<a name="CacheAccessToken"> </a>

Nach der Erstellung eines Tokens kann dieses in späteren Aufrufen an SharePoint erneut verwendet werden, bis es abläuft. Je nach Architektur der Remotekomponente und der Hosting-Plattform gibt es verschiedene **Wege zum Zwischenspeichern des Zugriffstokens** auf dem Server.
  
    
    

- Im Sitzungsstatus
    
  
- Im Anwendungsstatus
    
  
- Im  [Windows Server AppFabric-Cachedienst](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx).
    
  
- In einer Datenbank
    
  
- In einem  [memcached](http://www.memcached.org/)-System
    
  
Wenn der Cachespeicher, beispielsweise der Anwendungscache, von verschiedenen Benutzersitzungen gemeinsam verwendet wird, müssen Sie sicherstellen, dass Sie einen Cacheschlüssel verwenden, der für die Sitzung eindeutig ist. Wenn der Cache von mehreren Anwendungen gemeinsam verwendet wird, muss Ihr Code zudem auch für diese Variable **den Cacheschlüssel relativieren**. Es ist außerdem möglich, dass das Add-In auf verschiedene SharePoint-Farmen zugreift. Sie benötigen unterschiedliche Token für jede davon, müssten in diesem Szenario also den Cacheschlüssel für die Farm relativieren. Insgesamt benötigen Sie möglicherweise Cacheschlüssel, die auf einem oder mehreren Elementen aus Benutzer-ID, Anwendungs-ID und SharePoint-Domäne oder -Bereich basieren. Ziehen Sie in Betracht, ein ähnliches Cacheschlüsselsystem wie das von SharePoint-Add-Ins zu benutzen, die das Autorisierungssystem mit niedriger Vertrauensebene verwenden. Ausführliche Informationen finden Sie unter [Überblick über den Cacheschlüssel](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey). Im Wesentlichen würden Sie eine oder mehrere dieser drei IDs verknüpfen (und optional einen Hash mit einer kürzere Zeichenfolge daraus erstellen) und dann als Cacheschlüssel verwenden. 
  
    
    

### Handhabung abgelaufener Zugriffstoken
<a name="CacheAccessToken"> </a>

 **Das Zugriffstoken hat einen Ablaufzeitpunkt**, den Ihr Code auf einen beliebigen Wert festlegen kann. Wenn Ihr Add-In ein neues Zugriffstoken für jede Anforderung erstellt, muss jedes Token nur lange genug gültig sein, um von SharePoint validiert zu werden, das heißt, nicht mehr als einige Sekunden, solange das LAN des Kunden nicht für gewöhnlich überlastet ist. Sie könnten den Ablaufzeitpunkt auch auf mehrere Jahre in der Zukunft festlegen, aber selbst im „komplett lokalen" Szenario, für das besonders vertrauenswürdige Add-Ins ausgelegt sind, besteht die Gefahr, dass Zugriffstoken gestohlen werden. Deshalb sollten Sie den Ablaufzeitpunkt nicht auf mehr als einige Stunden festlegen. (Wenn Sie mit verwaltetem Code arbeiten, legt der Beispielcode für die Tokenerstellung in der Datei TokenHelper.cs [oder .vb] die Ablauffrist auf 12 Stunden fest.)
  
    
    
Wenn die Sitzung eines Benutzers mit dem SharePoint-Add-In länger als die Lebensspanne des zwischengespeicherten Zugriffstokens dauert, führt die erste Anforderung an SharePoint nach Ablauf des Tokens zu einem **401 Unauthorized**-Fehler. Ihr Code muss diese Antwort verarbeiten. Alternativ kann er den Ablaufzeitpunkt des Zugriffstokens testen, bevor es verwendet wird. **Ihr Code sollte auf ein abgelaufenes Zugriffstoken mit der Erstellung eines neuen Zugriffstokens reagieren** und die fehlgeschlagene Anforderung wiederholen.
  
    
    

## Verstehen der Struktur von Zugriffstoken
<a name="Structure"> </a>

Im Folgenden finden Sie ein **Beispiel für ein Zugriffstoken, das von einem besonders vertrauenswürdigen SharePoint-Add-In** erstellt wurde. Genauer gesagt wurde dieses Token von dem Beispielcode in der Datei TokenHelper.cs (oder .vb) erstellt, die Teil der SharePoint-Add-In-Projektvorlage ist, die von den Office-Entwicklertools für Visual Studio in Visual Studio 2013 Update 2 erstellt wurde. Das Token wurde codiert und zur besseren Lesbarkeit wurden Leerzeichen eingefügt. Die im besonders vertrauenswürdigen System verwendeten Token sind konform mit dem [[MS-SPS2SAUTH]: OAuth 2.0 Authentication Protocol: SharePoint Profile](http://msdn.microsoft.com/library/05f9759b-0fa4-45ff-bd4b-0d7d254e7010.aspx), das auch als Server-zu-Server- oder S2S-Protokoll bezeichnet wird. Diese Informationen werden bereitgestellt, um Entwickler, die verwalteten Code verwenden, beim Debugging von besonders vertrauenswürdigen SharePoint-Add-Ins zu unterstützen, und Entwicklern, die andere Sprachen verwenden, Anleitungen zum Konstruieren der Token zur Verfügung zu stellen.
  
    
    
 **Dieses Zugriffstoken wird generiert, wenn das Add-In einen Aufruf an SharePoint unter Verwendung der  [Benutzer-und-Add-In-Richtlinie](add-in-authorization-policy-types-in-sharepoint-2013.md) durchführt.** Wenn das Add-In die [Nur-Add-In-Richtlinie](add-in-authorization-policy-types-in-sharepoint-2013.md) verwendet und einen Nur-Add-In-Aufruf an SharePoint durchführt, wird das Actor-Token (das ein untergeordnetes Token innerhalb eines Benutzer-und-Add-In-Zugriffstokens ist und weiter unten beschrieben wird) zum Zugriffstoken (und es gibt kein übergeordnetes Token).
  
    
    

> **HINWEIS**
>  Beachten Sie, dass sich die von Ihrem Code erstellten besonders vertrauenswürdigen Zugriffstoken von den von Azure ACS erstellten Token unterscheiden, wenn das Autorisierungssystem mit niedriger Vertrauensebene verwendet wird:>  Der **alg**-Anspruch im Header ist „none", da das Zugriffstoken in einem Benutzer-und-Add-In-Aufruf von einem besonders vertrauenswürdigen Add-In nicht signiert ist. >  Die Add-In-URL im Wert **aud** in diesem Beispiel befindet sich auf einem lokalen Server, was für das besonders vertrauenswürdige System normal ist.>  Es gibt keinen **identityprovider**-Anspruch, aber einen **nii** (Namensidentitätsaussteller) mit derselben Art von Werten wie im **identityprovider**-Anspruch, den Zugriffstoken im Autorisierungssytem mit niedriger Vertrauensebene verwenden. (Informationen zu diesem Wert bei einem SAML-basierten Identitätsanbieter finden Sie in den Blog-Posts von Steve Peschka:  [Sicherheit in SharePoint-Add-Ins - Teil 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx) und [Verwenden von SharePoint-Add-Ins mit SAML- und FBA-Websites in SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx). >  Es gibt keinen **actor**-Anspruch, aber einen **actortoken**-Anspruch, der ein Base64-codiertes inneres Token mit einer Gültigkeitsdauer von 12 Stunden enthält. 
  
    
    

Der Header hat zwei Eigenschaften. „typ" ist der Tokentyp. Der Code in der Remotewebanwendung sollte diesen Wert immer auf „JWT" festlegen. „alg" ist der Algorithmus, der für das Signieren des Tokens verwendet wird. Da das äußere Token in einem Benutzer-und-Add-In-Aufruf von einem besonders vertrauenswürdigen Add-In nicht signiert wird, legen Sie diesen Wert auf „none" fest. Informationen zu den Werten im Hauptteil des besonders vertrauenswürdigen Zugriffstokens finden Sie in Tabelle 1.
  
    
    



```

{"typ":"JWT", "alg":"none"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"s-1-5-21-2127521184-1604012920-1887927527-2963467",
 "nii":"urn:office:idp:activedirectory",
 "actortoken":"6sMZhbw … [remainder of long base 64 string omitted] … "
}

```

In der folgenden Tabelle finden Sie einige Anweisung dazu, welche **Eigenschaften Ihr Code in das Zugriffstoken einschließen sollte und welche Werte für diese Eigenschaften festgelegt werden sollten**. Wenn Sie verwalteten Code verwenden, erstellen die Dateien SharePointContext.cs (oder .vb) und TokenHelper.cs (oder .vb) die Token für Sie. Nehmen wir beispielsweise an, Ihr Code führt einen einzigen Aufruf an die **SharePointContext.CreateUserClientContextForSPHost**-Methode durch. Diese wiederum ruft Methoden in der **TokenHelper**-Klasse auf, die das Zugriffstoken konstruieren, das dann in jeden Aufruf an SharePoint durch das SharePoint-Clientkontextobjekt eingefügt wird, das von **SharePointContext.CreateUserClientContextForSPHost** zurückgegeben wird.
  
    
    

**Tabelle 1: Von der App ausgestellte Zugriffstokenansprüche**


|**Anspruch**|**Beschreibung**|**Entsprechender Wert im Beispielzugriffstoken**|
|:-----|:-----|:-----|
| `aud` <br/> |Abkürzung für "audience", das ist der Prinzipal, für den das Token vorgesehen ist. Das Format ist  _Zielprinzipal-ID_/ _vollqualifizierte SharePoint-Domäne_@ _SharePoint-Bereich_.  <br/> Der Zielprinzipal für ein SharePoint-Add-In ist immer 00000003-0000-0ff1-ce00-000000000000.  <br/> Da besonders vertrauenswürdige SharePoint-Add-Ins normalerweise in einem komplett lokalen Szenario verwendet werden, ist der vollqualifizierte SharePoint-Domänenname oft einfach nur ein Servername.  <br/> Der  _SharePoint-Bereich_ ist die GUID der lokalen SharePoint-Farm, auf die mit dem Zugriffstoken zugegriffen werden soll (oder mit der GUID des Mandanten, wenn die Farm für Mandanten konfiguriert wurde). <br/> Sie finden den SharePoint-Bereich, indem Sie das PowerShell- **Get-SPAuthenticationRealm**-Cmdlet auf dem SharePoint-Server ausführen. Verwenden Sie es dann direkt in Ihrem Code oder speichern Sie es in einer Konfigurationsdatei, aus der Ihr Code es lesen kann, beispielsweise im app.Settings-Abschnitt einer web.config-Datei. Alternativ kann Ihr Code den SharePoint-Bereich dynamisch während der Laufzeit ermitteln, indem er eine Authentifizierungsaufforderung an SharePoint sendet. Ein Beispiel dafür, wie dies in verwaltetem Code durchgeführt wird, finden Sie in der  `GetRealmFromTargetUrl`-Methode in der Datei TokenHelper.cs (oder .vb).  <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |Abkürzung für "issuer". Sie stellt den Prinzipal dar, der das Token erstellt hat. Das Format ist  _Aussteller-GUID_@ _SharePoint-Bereichs-GUID_.  <br/> Im besonders vertrauenswürdigen System ist das Add-In selbst der Aussteller, sodass normalerweise die Client-ID des SharePoint-Add-In als Aussteller-GUID verwendet wird.  *Alle Buchstaben in der Aussteller-ID müssen kleingeschrieben sein.*  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |Abkürzung für "not before" (nicht vor). Sie steht für den Zeitpunkt in Sekunden ab dem 1. Januar 1970 um Mitternacht, ab dem der Token  *beginnt*  , gültig zu werden. Ihr Code sollte diesen Wert auf den Moment festlegen, in dem das Token erstellt wird. <br/> |1403212820  <br/> |
| `exp` <br/> |Abkürzung für "expiration". Sie steht für den Zeitpunkt, an dem das Token abläuft, in Sekunden nach dem 1. Januar 1970 um Mitternacht.  <br/> |1403256020  <br/> |
| `nameid` <br/> |Ein eindeutiger Bezeichner für den Benutzer, für den das Token ausgestellt wurde. Das Format ist je nach Identitätsanbieter unterschiedlich. In diesem Beispiel ist der Anbieter Active Directory.  <br/> |s-1-5-21-2127521184-1604012920-1887927527-2963467  <br/> |
| `nii` <br/> |Abkürzung für "name identifier issuer". Der eindeutige Name des Identitätsanbieters wie er bei der Internet Assigned Numbers Authority (IANA) registriert ist.  <br/> Bei einem besonders vertrauenswürdigen SharePoint-Add-In ist es normalerweise ein lokaler Identitätsanbieter wie Active Directory in diesem Beispiel.  <br/> |urn:office:idp:activedirectory  <br/> |
| `actortoken` <br/> |Ein Base64-codiertes JWT-Token, das das SharePoint-Add-In identifiziert und SharePoint mitteilt, dem Add-In zu vertrauen, unabhängig davon, welcher Benutzer das Add-In ausführt.  <br/> |Siehe unten.  <br/> |
   
 **Im Folgenden ist das decodierte **actortoken** gezeigt.** Das kleine JavaScript Object Notation (JSON)-Headerobjekt im oberen Bereich enthält Metadaten zum Token, einschließlich Tokentyp und Algorithmus, mit dem es signiert wird. Die Eigenschaft **x5t** des Headers ist ein Digest, der aus dem Fingerabdruck des x.509-Zertifikats erstellt wird, das der offizielle Aussteller des Tokens ist. Ihr Code geht zum Konstruieren dieses Werts wie folgt vor:
  
    
    

1. Abrufen der Bytearrayversion (nicht der Zeichenfolge) des Fingerabdrucks des Zertifikats. Dies ist ein SHA-1-Digest des Zertifikats. (In verwaltetem Code kann dies mit der Methode  [GetCertHash()](http://msdn2.microsoft.com/DE-DE/library/4f9acc3f) durchgeführt werden. Sie benötigen etwas Äquivalentes in der Sprache, die Sie verwenden.)
    
  
2. Codieren des Bytearrays mit Base64-URL-Codierung.
    
  
3. Legen Sie den Wert der Eigenschaft **x5t** auf den codierten Digest fest.
    
  
In Tabelle 2 sind die Ansprüche beschrieben, die Ihr Code in den Hauptteil des Tokens einfügen muss, sowie die Werte, die dafür festgelegt werden sollten.
  
    
    



```

{"typ":"JWT","alg":"RS256","x5t":"7MjK99QvkVdwz6UrKldx8AG7ydM"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "trustedfordelegation":"true"
}

```


> **HINWEIS**
> Wenn das besonders vertrauenswürdige Add-In die  [Nur-Add-In-Richtlinie](add-in-authorization-policy-types-in-sharepoint-2013.md) verwendet und einen Nur-Add-In-Aufruf an SharePoint durchführt, ist das hier gezeigte Token tatsächlich das Zugriffstoken. Es gibt kein äußeres Token. Darüber hinaus gibt es keinen **trustedfordelegation**-Anspruch, da die Berechtigungen des Benutzers für einen Nur-Add-In-Aufruf irrelevant sind. 
  
    
    


**Tabelle 2: Vom Zertifikat ausgestellte Actor-Tokenansprüche**


|**Anspruch**|**Beschreibung**|**Entsprechender Wert im Beispielzugriffstoken**|
|:-----|:-----|:-----|
| `aud` <br/> |Wie im übergeordneten Zugriffstoken.  <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `iss` <br/> |Dieselbe Bedeutung wie im übergeordneten Zugriffstoken, aber die Aussteller-GUID ist nicht die Client-ID der Webanwendung, sondern die GUID des Zertifikats. Obwohl Code in der Anwendung das Actor-Token konstruiert, wird das Zertifikat als Aussteller des Actor-Tokens betrachtet.  <br/> Beachten Sie, dass die Aussteller-GUID in diesem Beispiel eine leicht zu merkende GUID-Zeichenfolge ist, die der Farmadministrator verwendet hat, als er das x.509-Zertifikat als vertrauenswürdigen Tokenherausgeber in SharePoint registriert hat. Dies ist üblich, wenn dasselbe Zertifikat als Actor-Tokenherausgeber für alle besonders vertrauenswürdigen SharePoint-Add-Ins in der Farm verwendet wird. Ein Administrator kann sich auch für verschiedene Zertifikate für jedes SharePoint-Add-In entscheiden. In diesem Fall würde er verschiedene, zufällig generierte GUIDs für die Zertifikate verwenden.  *Alle Buchstaben in der Aussteller-GUID müssen kleingeschrieben sein.*  <br/> |11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `nbf` <br/> |Dieselbe Bedeutung wie im übergeordneten Zugriffstoken.  <br/> |Derselbe Wert wie im übergeordneten Zugriffstoken.  <br/> |
| `exp` <br/> |Dieselbe Bedeutung wie im übergeordneten Zugriffstoken.  <br/> |Derselbe Wert wie im übergeordneten Zugriffstoken.  <br/> |
| `nameid` <br/> |Ein eindeutiger Bezeichner für das SharePoint-Add-In, da es der "actor" im besonders vertrauenswürdigen System ist. Das Format ist  _Client_ID_@ _SharePoint_Bereich_ <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2  <br/> |
| `trustedfordelegation` <br/> |Ein boolescher Wert, der angibt, ob SharePoint dem SharePoint-Add-In vertrauen sollte, dass sie den Benutzer authentifiziert und autorisiert. Im besonders vertrauenswürdigen System ist der Wert normalerweise "true".  <br/> Schließen Sie diesen Anspruch nicht in einen Nur-Add-In-Aufruf im besonders vertrauenswürdigen System ein.  <br/> |true  <br/> |
   

## Codieren und Signieren von Token
<a name="EncodeTokens"> </a>

 **Nachdem Ihr Code alle Eigenschaften und Werte zu Header- und Hauptteil-JSON-Objekten hinzugefügt hat, muss er sie codieren, in ein JWT kombinieren und signieren.** Die einzelnen Schritte sind im Folgenden aufgeführt.
  
    
    

1. Codieren Sie den Header mit der Base64-URL-Codierung.
    
  
2. Codieren Sie die Nutzlast mit der Base64-URL-Codierung.
    
  
3. Verknüpfen Sie den codierten Hauptteil nach dem codierten Header mit einem "."-Zeichen dazwischen. Dies ist das JWT.
    
  
4. Erstellen Sie eine SHA256-Signatur mit dem JWT und dem privaten Schlüssel des Zertifikats.
    
  
5. Codieren Sie die Signatur mit der Base64-URL-Codierung.
    
  
6. Hängen Sie die Signatur an der Ende des JWT an, mit einem "."-Zeichen dazwischen. 
    
  
Für ein Actor-Token, das mit einem Nur-Add-In-Aufruf verwendet wird, dient das Actor-Token als Zugriffstoken. Es gibt kein äußeres Token. Für ein Zugriffstoken, das mit einem Benutzer-und-Add-In-Aufruf verwendet wird, werden die vorstehenden Schritte verwendet, um ein Actor-Token zu konstruieren, dass dann in den Hauptteil eines Zugriffstokens als Wert für die Eigenschaft **actortoken** eingefügt wird. Das vollständige Zugriffstoken wird dann mit den ersten drei Schritten oben codiert und konstruiert, aber nicht signiert, sodass die verbleibenden Schritte für das äußere Token nicht verwendet werden.
  
    
    

## Problembehandlung bei Zugriffstoken
<a name="Trouble"> </a>

Das kostenlose  [Fiddler-Tool](http://www.telerik.com/fiddler) kann verwendet werden, um die HTTP-Anforderungen zu erfassen, die von der Remotekomponente Ihres Add-Ins an SharePoint gesendet werden. Es gibt eine [kostenlose Erweiterung für das Tool](https://github.com/andrewconnell/SPOAuthFiddlerExt), die automatisch die Token in den Anforderungen dekodiert.
  
    
    

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Erstellen von Add-Ins für SharePoint, die eine Autorisierung mit hoher Vertrauenswürdigkeit verwenden](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)
    
  
- Steve Peschka  [Sicherheit in SharePoint-Add-Ins - Teil 7](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-7.aspx)
    
  
- Steve Peschka  [Sicherheit in SharePoint-Add-Ins - Teil 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)
    
  
- Steve Pescka  [Verwenden von SharePoint-Add-Ins mit SAML- und FBA-Websites in SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)
    
  
- Kirk Evan  [Besonders vertrauenswürdige SharePoint-Add-Ins auf Nicht-Microsoft-Plattformen](http://blogs.msdn.com/b/kaevans/archive/2014/07/14/high-trust-sharepoint-apps-on-non-microsoft-platforms.aspx)
    
  
-  [OAuth 2.0](http://oauth.net/2/)
    
  

