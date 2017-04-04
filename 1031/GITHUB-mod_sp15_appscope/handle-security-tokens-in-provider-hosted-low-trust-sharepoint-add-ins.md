---
title: Handhabung von Sicherheitstoken in vom Anbieter gehosteten Add-Ins für SharePoint mit niedriger Vertrauensebene
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# Handhabung von Sicherheitstoken in vom Anbieter gehosteten Add-Ins für SharePoint mit niedriger Vertrauensebene
Erfahren Sie mehr über die Kontext-, Zugriffs- und Aktualisierungstoken, die für die Autorisierung der vom Anbieter gehosteten SharePoint-Add-Ins mit niedriger Vertrauensebene verwendet werden, und wie Sie mit diesen in Ihrem Code arbeiten können.
> [!WICHTIG]
> **In diesem Artikel geht es um die Verwendung von Sicherheitstoken im Autorisierungssystem mit niedriger Vertrauensebene, nicht um das besonders vertrauenswürdige System.** Informationen zur Verwendung von Token im besonders vertrauenswürdigen System finden Sie unter [Erstellen und Verwenden von Zugriffstoken in vom Anbieter gehostete besonders vertrauenswürdige SharePoint-Add-Ins](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md). 
  
    
    

 **SharePoint-Add-Ins, die das  [Autorisierungssystem mit niedriger Vertrauensebene](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) verwenden, um Zugriff auf SharePoint-Daten zu erhalten, nehmen an einem OAuth-Ablauf teil, der die Übergabe von Sicherheitstoken (im [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/)-Format) unter SharePoint, Microsoft Azure Access Control Service (ACS), den Remotekomponenten der SharePoint-Add-In und, in einigen Fällen, dem Browser des Benutzers beinhaltet.** Je nach Design des Add-Ins gibt es verschiedene Abläufe, aber beinhalten mindestens zwei der folgenden Tokentypen:
- **Zugriffstoken:** Enthalten in jeder CRUD-Anforderung (Erstellen, Lesen, Aktualisieren oder Löschen) von den Remotekomponenten des Add-Ins an SharePoint. SharePoint überprüft das Token und verarbeitet die Anforderung.
    
  
- **Aktualisierungstoken:** Verwendet zum Abrufen des ersten Zugriffstokens im [Kontexttokenablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) und zum Ersetzen ablaufender Zugriffstoken sowohl im Kontexttoken- als auch im [Autorisierungscodeablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) des Autorisierungssystems mit niedriger Vertrauensebene.
    
  
Je nachdem, welchen OAuth-Ablauf das Add-In verwendet, ist auch die eine oder die andere der folgenden Komponenten Teil des Prozesses:
- **Kontexttoken:** Im Kontexttokenablauf verwendet, um der Remotekomponente ein Aktualisierungstoken und Informationen bereitzustellen, die sie benötigt, um ein Zugriffstoken von Azure ACS anzufordern.
    
  
- **Autorisierungscode:** Kein Token, sondern Autorisierungscode, der für jedes Benutzer- und Anwendungskombination eindeutig ist. Er wird im Autorisierungscodeablauf verwendet, um ein erstes Zugriffstoken und ein Aktualisierungstoken zu erhalten.
    
  

## Übersicht über die Handhabung von Zugriffstoken
<a name="AccessTokens"> </a>

Im Autorisierungssystem mit niedriger Vertrauensebene werden die Zugriffstoken von Azure ACS erstellt und an die Remotekomponente Ihrer SharePoint-Add-In gesendet. (Zum Zeitpunkt, an dem dieser Artikel geschrieben wurde, hatten von ACS ausgestellte Zugriffstoken für SharePoint eine Lebensspanne von 12 Stunden, das kann sich aber geändert haben.) Die wichtigsten **Dinge, die der Code in Ihrer SharePoint-Add-In erledigen muss**, sind die folgenden:
  
    
    

- **Anfordern eines Zugriffstokens** von Azure ACS. Je nachdem, welcher OAuth-Ablauf verwendet wird, nutzt das Add-In entweder einen Autorisierungscode oder Informationen, die es aus einem Kontexttoken extrahiert, um die Anforderung zu erstellen.
    
  
- **Schließen Sie das Token in jede HTTP-Anforderung an SharePoint ein.** Das Token wird als ein **Authorization**-Header an die Anforderung angehängt. Der Wert des Headers ist das Wort "Bearer", gefolgt von einem Leerzeichen, gefolgt von dem Base64-codierten Zugriffstoken.
    
  
- Optional (aber empfohlen) können Sie **das Zugriffstoken zwischenspeichern**, damit es in nachfolgenden Anforderungen erneut verwendet werden kann.
    
  
- Optional können Sie das Zugriffstoken an Back-End-Systeme weiterleiten, damit diese direkt auf SharePoint zugreifen können.
    
  
Diese Aufgaben müssen mit serverseitigem Code durchgeführt werden. Wenn Sie verwalteten Code verwenden, befindet sich Beispielcode für einige dieser Aufgaben in den Dateien SharePointContext.cs (oder .vb) und TokenHelper.cs (oder .vb), die Teil von Microsoft Office-Entwicklertools für Visual Studio sind. Ein Beispiel für PHP-Code, der einige dieser Aufgaben ausführt, finden Sie unter  [Erläuterungen zur REST-Schnittstelle von SharePoint 2013 und zu ihrer Verwendung](http://msdn.microsoft.com/de-de/magazine/dn198245.aspx).
  
    
    

### Zwischenspeichern des Zugriffstokens
<a name="CacheAccessToken"> </a>

Je nach Architektur Ihrer SharePoint-Add-In und der Hosting-Plattform gibt es verschiedene **Wege zum Zwischenspeichern des Zugriffstokens** auf dem Server.
  
    
    

- Im Sitzungsstatus
    
  
- Im Anwendungsstatus
    
  
- Im  [Windows Server AppFabric-Cachedienst](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx) oder seiner Entsprechung in einem nicht von Microsoft stammenden Betriebssystem
    
  
- Im  [Microsoft Azure-Cachedienst](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx) oder seiner Entsprechung in einem nicht von Microsoft stammenden Clouddienst
    
  
- In einer Datenbank
    
  
- In einem  [memcached](http://www.memcached.org/)-System
    
  

> [!HINWEIS]
> In den meisten Szenarien können Sie nicht so einfache Begriffe wie "Zugriffstoken" als Cacheschlüssel verwenden, weil Ihr Add-In die Token für verschiedene Benutzer und SharePoint-Farms/-Mandanten voneinander unterscheiden können muss. Wenn Ihr Add-In den  [Kontexttokenablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) verwendet, wird ein spezieller **CacheKey** von SharePoint bereitgestellt, der verwendet werden kann, um zwischengespeicherte Token zu unterscheiden. In diesem Abschnitt wird erklärt, worin die Probleme liegen und was Sie tun können, wenn Ihre Anwendung nicht den Kontexttokenablauf verwendet.
  
    
    

Das Zwischenspeichern des Zugriffstokens in Sitzungszustand ist für die meisten Szenarien angemessen. Wenn die Remotewebanwendung auf andere Dienste zugreift, die OAuth (zusätzlich zu SharePoint) verwenden und sie die verschiedenen Zugriffstoken im Sitzungsstatus zwischenspeichert, müssen Sie sicherstellen, dass Sie eindeutige Cacheschlüssel für die Token verwenden. Verwenden Sie beispielsweise anstelle von "AccessToken" die Bezeichnungen "SharePoint_AccessToken", "Facebook_AccessToken", "SAP_Gateway_AccessToken" usw. (Wenn Sie keinen Sitzungsstatus oder eine andere Methode zum Zwischenspeichern verwenden, die den jeweiligen Cache aller Benutzer automatisch trennt, müssten Sie auch Ihre Schlüssel für Benutzer relativieren.)
  
    
    
Wenn die Anwendung auf mehrere SharePoint-Farmen oder -Onlinemandanten zugreift, können Sie die SharePoint-Domäne als Teil des primären Cacheschlüssels der Anwendung verwenden ("SharePoint_ *<mydomain>*  .sharepoint.com_AccessToken") oder den Bereich der Farm/des Mandanten benutzen ("SharePoint_ *<realmGUID>*  _AccessToken"), die beide vom Zugriffstoken gelesen werden können. (Ihr Code muss die Base64-Codierung des Tokens umkehren, um es lesen zu können. In verwaltetem Code verfügt die Datei TokenHelper.cs, oder .vb, über Beispielcode für diesen Zweck, der Klassen aus den Namespaces **Microsoft.IdentityModel.S2S.Tokens** und **System.IdentityModel.Tokens** verwendet.) Eine weitere Option steht zur Verfügung, wenn die Anwendung den Kontexttokenablauf wie im folgenden Absatz beschrieben verwendet.
  
    
    
In einigen Szenarien muss Ihre Anwendung das Zugriffstoken an einem Ort zwischenspeichern, der für die Anwendung über Sitzungen hinweg oder nach Beenden einer Sitzung verfügbar ist. Die Anwendung kann beispielsweise so entworfen sein, dass sie Benutzern ermöglicht, Aktionen zu planen, die durchgeführt werden, nachdem der Benutzer die Anwendung geschlossen hat. Wenn diese Aktionen den Zugriff auf SharePoint beinhalten, muss das Add-In das Zugriffstoken abrufen. In einem solchen Szenario **muss Ihre Anwendung die Zugriffstoken verschiedener Benutzer unterscheiden können**. Wenn Sie den Kontexttokenablauf verwenden, wird für diesen Zweck eine Cacheschlüsselzeichenfolge im Kontexttoken bereitgestellt, das SharePoint an die Remotekomponente Ihrer SharePoint-Add-In übergibt, wenn das Add-In gestartet wird. Weitere Informationen zu diesem **speziellen Cacheschlüssel** und seiner Verwendung finden Sie unter [Überblick über den Cacheschlüssel](#CacheKey). Sie können diese Zeichenfolge auch für das im vorherigen Abschnitt beschriebene Szenario verwenden. Das Planungssystem verfügt über eine Methode, um die erforderlichen Konfigurationsdaten zu speichern, beispielsweise dazu, wann die geplante Aktion ausgeführt wird und was sie bewirken soll. Verwenden Sie diesen Speicher, um den Cacheschlüssel für das Zugriffstoken abzulegen.
  
    
    
Wenn der von Ihnen verwendete sitzungsübergreifende Cache auch von mehreren Anwendungen gemeinsam verwendet wird, müssen die Cacheschlüssel für Anwendungen sowie für Benutzer und SharePoint-Bereiche relativiert werden. Der Cacheschlüssel, der im Kontexttoken bereitgestellt wird, ist für Anwendungen sowie für Benutzer und SharePoint-Bereiche eindeutig.
  
    
    
 **Wenn Ihre Anwendung den Autorisierungscodeablauf verwendet**, gibt es kein Kontexttoken und damit keinen speziell erstellten Cacheschlüssel. In diesem Fall **müssen Sie ein eigenes System mit Schlüsseln für zwischengespeicherte Daten erstellen**, die je nach potenziellen Namenskonflikten in den vorgesehenen Anwendungsfällen Ihrer Anwendung für eins oder mehrere der folgenden Elemente relativiert werden: den Benutzer, den SharePoint-Bereich und die Anwendung. Sie können für diesen Zweck die Ansprüche im Zugriffstoken verwenden, zum Beispiel **nameId** und **aud** (siehe Tabellen unten). Ihr Code kann einfach die Zeichenfolgen verknüpfen oder sie als Startwert verwenden, um einen eindeutigen Hashwert zu erstellen, der als Cacheschlüssel dienen kann.
  
    
    
Wenn Ihre Anwendung schließlich sowohl Aufrufe nur für das Add-In als auch für Benutzer und Add-In an SharePoint durchführt, hat sie zwei verschiedene Zugriffstoken. Also benötigen Sie unterschiedliche Cacheschlüssel. Wenn Sie sich für einen grundlegenden Cacheschlüssel entschieden haben, hängen Sie ihm einfach "_nurapp" oder "_app+benutzer" an.
  
    
    

> [!VORSICHT]
> **Das Speichern des Zugriffstokens in einem Cookie ist keine sichere Vorgehensweise.** Für gewöhnlich wird als eine bewährte Methode vermieden, das Zugriffstoken über den Browser zu übergeben.
  
    
    


### Weiterleiten des Zugriffstokens an Back-End-Systeme
<a name="ForwardTokenToBackend"> </a>

Ein SharePoint-Add-In hat möglicherweise Back-End-Server, die nicht in derselben Domäne gehostet sind wie die Remotewebanwendung. Wenn ein Back-End-Server CRUD-Vorgänge in SharePoint durchführen muss, zeigt das Add-In eine schnellere Leistung, wenn diese Vorgänge direkt vom Back-End-System zu SharePoint gehen können. Zum Glück ist die Domäne nur wichtig, wenn Ihre Anwendung ein Zugriffstoken von ACS erhält. Wenn sie das Token hat, kann sie es an die Back-End-Dienste weiterleiten und diese können damit SharePoint aufrufen. Code in diesen Systemen muss abgelaufene Zugriffstoken verarbeiten und eine Anforderung für ein neues Token zurück an die übergeordnete Webanwendung senden, die tatsächlich bei ACS registriert ist. Weitere Informationen finden Sie unter  [Handhabung abgelaufener Zugriffstoken](#Expired). Diese Technik sollte nur für die eigenen Back-End-Server Ihrer Anwendung, nicht für externe Webdienste verwendet werden. Außerdem sollten Sie bei Verwendung dieser Technik überlegen, ob Sie die Back-End-Services so entwerfen können, dass sie möglichst immer Nur-Add-In-Aufrufe verwenden.
  
    
    

### Handhabung abgelaufener Zugriffstoken
<a name="Expired"> </a>

Ein Zugriffstoken läuft nach einigen Stunden (zwölf zum Zeitpunkt, an dem der Artikel geschrieben wurde, aber das kann sich geändert haben) ab. Wenn die Anwendung immer noch auf SharePoint zugreift, nachdem das Zugriffstoken abgelaufen ist, führt die erste Anforderung an SharePoint nach dem Ablaufzeitpunkt zu einem **401 Unauthorized**-Fehler. Ihr Code muss diese Antwort verarbeiten. Alternativ kann der Code den Ablaufzeitpunkt des Zugriffstokens testen, bevor es verwendet wird. **Ihr Code verwendet das Aktualisierungstoken, um ein anderes Zugriffstoken von ACS abzurufen.** Im Kontexttokenablauf ist das Aktualisierungstoken im Kontexttoken enthalten, das Ihr Add-In zu Beginn der ersten Sitzung mit SharePoint von SharePoint erhält. Im Autorisierungscodeablauf wird es zusammen mit dem ersten Zugriffstoken an die Anwendung übergeben. Ihr Code muss es zwischenspeichern, damit es bei Bedarf verfügbar ist. Das Aktualisierungstoken ist für einige Monate gültig und kann in einem Cookie oder in serverseitigem Speicher beibehalten werden. Weitere Informationen finden Sie unter [Überblick über die Handhabung und das Zwischenspeichern von Aktualisierungstoken](#RefreshToken).
  
    
    

### Beispiele für Zugriffstoken
<a name="ExampleAccessTokens"> </a>

In diesem Abschnitt werden Zugriffstoken anhand von Beispielen beschrieben und Sie erfahren, inwiefern sie je nach verwendeter Autorisierungsrichtlinie unterschiedlich sind.
  
    
    

#### Zugriffstoken für das Autorisierungssystem mit niedriger Vertrauensebene

 **Benutzer-und-Add-In-Richtlinie**
  
    
    
Im Folgenden sehen Sie ein decodiertes **Beispiel für ein Benutzer-und-Add-In-Token, das von ACS** für Aufrufe an SharePoint unter Verwendung der [Benutzer-und-Add-In-Richtlinie](add-in-authorization-policy-types-in-sharepoint-2013.md) erstellt wurde. Zur besseren Lesbarkeit wurden Leerzeichen eingefügt. Das Token entspricht dem [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1)-Protokoll. Das JavaScript Object Notation (JSON)-Objekt im Token wird als Anspruchssatz bezeichnet. Ausführliche Informationen zu den Eigenschaften im Anspruchssatz finden Sie in Tabelle 1. Beachten Sie, dass alle Werte kleingeschrieben werden müssen. (Benutzer-und-Add-In-Zugriffscodes sind im [Kontexttokenablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) und im [Autorisierungscodeablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) identisch.)
  
    
    



```

{
 "aud": "00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss": "00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf": 1377549246,
 "exp": 1377592446,
 "nameid": "2303000085ff9abc",
 "actor": "964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73",
 "identityprovider": "urn:federation:microsoftonline"
}
```


**Tabelle 1: Von ACS ausgestellte Benutzer-und-Add-In-Tokenansprüche**


|**Anspruch**|**Beschreibung**|**Entsprechender Wert im Beispielzugriffstoken**|
|:-----|:-----|:-----|
| `aud` <br/> |Abkürzung für "audience", das ist der Prinzipal, für den das Token vorgesehen ist. Das Format ist  _Zielprinzipal-ID_/ _SharePoint-Domäne_@ _SharePoint-Bereich_, wobei  _Zielprinzipal-ID_ eine dauerhafte Sicherheitsprinzipal-ID für SharePoint ist. (Weitere Informationen finden Sie unter [Konstanten für Microsoft-Produktanwendungsprinzipale](http://msdn.microsoft.com/de-de/library/hh629982%28v=office.12%29.aspx).)  <br/> . _SharePoint-Bereich_ ist die GUID des SharePoint Online-Mandanten oder der lokalen SharePoint-Farm, auf die mit dem Zugriffstoken zugegriffen wird. Diese GUID fungiert als ID des Bereichs für den Tokenherausgeber, in diesem Fall Azure ACS. <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |Abkürzung für "issuer". Sie stellt den Prinzipal dar, der das Token erstellt hat. Das Format ist  _Aussteller-GUID_@ _SharePoint-Bereichs-GUID_.  <br/> Im Autorisierungssystem mit niedriger Vertrauensebene ist der Aussteller Azure ACS und seine GUID lautet **00000001-0000-0000-c000-000000000000**. <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |Abkürzung für "not before" (nicht vor). Sie steht für den Zeitpunkt in Sekunden ab dem 1. Januar 1970 um Mitternacht, ab dem das Token  *beginnt*  , gültig zu werden. Standardmäßig wird der Wert auf den Moment festgelegt, in dem das Token erstellt wird. Weitere Informationen finden Sie unter [Arbeiten mit JWT-Zeitwerten](#JWTtimes).  <br/> |1377549246  <br/> |
| `exp` <br/> |Abkürzung für "expiration", also den Zeitpunkt, an dem das Token abläuft. Standardmäßig ist das 12 Stunden nach dem **nbf** -Wert. Weitere Informationen finden Sie unter [Arbeiten mit JWT-Zeitwerten](#JWTtimes).  <br/> |1377592446  <br/> |
| `nameid` <br/> |Ein eindeutiger Bezeichner für den Benutzer, für den das Token ausgestellt wurde. Das Format variiert je nach Identitätsanbieter. In diesem Beispiel ist der Anbieter Microsoft Online, wäre es jedoch Active Directory, würde die ID folgendermaßen aussehen: "s-1-5-21-2127521184-1604012920-1887927527-415149".  <br/> |2303000085ff9abc  <br/> |
| `actor` <br/> |Der Prinzipal, der Zugriff auf die SharePoint-Farm oder den -Mandanten haben möchte. Er hat das Format  _Client-ID der Anwendung_@ _SharePoint-Bereich_.  <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `identityprovider` <br/> |Der eindeutige Name des Identitätsanbieters wie er bei der Internet Assigned Numbers Authority (IANA) registriert ist.  <br/> Für ein in SharePoint Online installiertes Add-In ist der Wert für gewöhnlich derselbe wie in diesem Beispiel.  <br/> Für ein Add-In, das in einer lokalen Farm installiert ist, wäre es üblicherweise ein lokaler Identitätsanbieter wie "urn:office:idp:activedirectory".  <br/> |urn:federation:microsoftonline  <br/> |
   
 **Nur-Add-In-Richtlinie**
  
    
    
Im Folgenden sehen Sie ein decodiertes **Beispiel für ein Nur-Add-In-Zugriffstoken, das von ACS** für Aufrufe an SharePoint unter Verwendung der [Nur-Add-In-Richtlinie](add-in-authorization-policy-types-in-sharepoint-2013.md) erstellt wurde. Zur besseren Lesbarkeit wurden Leerzeichen eingefügt. Das Token entspricht dem [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1)-Protokoll. Einzelheiten zu den Eigenschaften im Anspruchssatz finden Sie in Tabelle 2. (Die Nur-Add-In-Richtlinie ist nicht für Anwendungen verfügbar, die den  [Autorisierungscodeablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) verwenden, weil diese nicht über eine Add-In-Manifestdatei verfügen und deshalb keine Berechtigung für die Verwendung von Nur-Add-In-Aufrufen anfordern können.)
  
    
    



```

{
 "aud":"00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":1403304705,
 "exp":1403347905,
 "nameid":"c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73",
 "sub":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "oid":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "trustedfordelegation":"false",
 "identityprovider":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73"
}
```


**Tabelle 2: Von ACS ausgestellte Nur-Add-In-Tokenansprüche**


|**Anspruch**|**Beschreibung**|**Entsprechender Wert im Beispielzugriffstoken**|
|:-----|:-----|:-----|
| `aud` <br/> |Wie bei Benutzer-und-Add-In-Token oben.  <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `iss` <br/> |Wie bei Benutzer-und-Add-In-Token oben.  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `nbf` <br/> |Wie bei Benutzer-und-Add-In-Token oben.  <br/> |1403304705  <br/> |
| `exp` <br/> |Wie bei Benutzer-und-Add-In-Token oben.  <br/> |1403347905  <br/> |
| `nameid` <br/> |Ein eindeutiger Bezeichner für das Add-In, anstelle des Benutzers, da die Identität des Benutzers bei der Nur-Add-In-Richtlinie keine Rolle spielt. Das Format ist  _Client-ID_@ _SharePoint-Bereich_.  <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
| `sub` <br/> |Abkürzung für "subject", den Betreff des Tokens, das der Prinzipal ist, der auf SharePoint zugreifen möchte, in diesem Fall die Remotewebanwendung. Als Wert wird die Objekt-ID verwendet. Siehe den **oid**-Anspruch unten.  <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `oid` <br/> |Abkürzung für "object ID", also die Objekt-ID in Azure Active Directory für die Remotewebanwendung. In einem Nur-Add-In-Zugriffstoken sind die Betreff- und Objekt-ID ein identischer Wert.  <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce  <br/> |
| `trustedfordelegation` <br/> |Ein boolescher Wert, der angibt, ob SharePoint der SharePoint-Add-In vertrauen sollte, dass sie den Benutzer authentifiziert und autorisiert. In Nur-Add-In-Aufrufen ist der Wert auf "false" festgelegt, da die Benutzeridentität keine Rolle spielt.  <br/> |false  <br/> |
| `identityprovider` <br/> |Der eindeutige Name des Identitätsanbieters. Da es sich um das Add-In und nicht um einen Benutzer handelt, deren Identität bereitgestellt wird, ist ACS der Identitätsanbieter. Das Format ist  _ACS-GUID_@ _SharePoint-Bereich_.  <br/> |00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
   

## Überblick über die Struktur und Handhabung von Kontexttoken
<a name="ContextTokens"> </a>

Ein Kontexttoken wird nur im  [Kontexttokenablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) des Autorisierungssystems mit niedriger Vertrauensebene verwendet. **Wenn die SharePoint-Add-In in SharePoint gestartet wird, fordert SharePoint an, dass Azure ACS ein Kontexttoken erstellt, das SharePoint dann an die Remotekomponente der SharePoint-Add-In übergibt.** Das Token wird als ausgeblendeter Parameter namens **SPAppToken** in einer Anforderung von SharePoint für die Startseite der Remotekomponente übergeben. Das Token wird mit einem geheimen Clientschlüssel signiert, der nur ACS und der SharePoint-Add-In bekannt ist. Das **Kontexttoken enthält ein Aktualisierungstoken, das das Add-In**, zusammen mit anderen Informationen aus dem Kontexttoken, **verwendet, um ein Zugriffstoken** von ACS anzufordern. (Zum Zeitpunkt, an dem dieser Artikel geschrieben wurde, hatten von ACS ausgestellte Kontexttoken für SharePoint eine Lebensspanne von 12 Stunden, das kann sich aber geändert haben.) Die **Hauptaufgaben für den Code in der SharePoint-Add-In** sind die folgenden:
  
    
    

- Verwenden Sie den geheimen Clientschlüssel des Add-Ins, um **das Kontexttoken zu validieren**.
    
  
- **Speichern Sie das Kontexttoken** oder das extrahierte Element zwischen und speichern Sie das Aktualisierungstoken und bestimmte andere Elemente daraus separat zwischen.
    
  
- Verwenden Sie das Aktualisierungstoken und andere Informationen, um **ein Zugriffstoken** von ACS anzufordern (das dann selbst zwischengespeichert wird).
    
  
- **Speichern Sie den Cacheschlüssel** aus dem Kontexttoken zwischen.
    
  

> [!WICHTIG]
> Die ersten zwei Aufgaben müssen durchgeführt werden, bevor der Benutzer zu einer anderen Seite navigiert oder die Seite aktualisiert, da sonst das Token verloren ist. Ziehen Sie beispielsweise bei einer ASP.NET-Web Forms-Anwendung in Betracht, diese Aufgaben in der **Page_Load**-Methode durchzuführen (in einem Bedingungscodeblock, der nur ausgeführt wird, wenn die Anforderung kein Postback ist). Bei einer ASP.NET-MVC-Anwendung können Sie diese Aufgaben in der Standardcontrollermethode durchführen. 
  
    
    

Wenn Sie verwalteten Code verwenden, befindet sich Beispielcode für einige dieser Aufgaben in den Dateien SharePointContext.cs (oder .vb) und TokenHelper.cs (oder .vb), die in Microsoft Office-Entwicklertools für Visual Studio enthalten sind. Sie müssen nur einfache Aufrufe an die Mitglieder der TokenHelper-Klasse durchführen. Ihr Code kann die erste Aufgabe beispielsweise mit der folgenden einzelnen Codezeile durchführen:
  
    
    



```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);
```

Ein Beispiel, wie Sie einige dieser Aufgaben mit PHP erledigen können, finden im Beispiel  [SharePoint 2013: Durchführen von Vorgängen in der SharePoint-Dokumentbibliothek von einer PHP-Website](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef).
  
    
    

### Zwischenspeichern des Kontexttokens oder Teilen davon
<a name="CacheContextToken"> </a>

Sie können ganze Kontexttoken oder nur das Aktualisierungstoken und bestimmte andere darin enthaltene Elemente, die Ihr Code verwendet, um Zugriffstoken abzurufen **zwischenspeichern**, entweder **in serverseitigem oder in clientseitigem Speicher**. Zu Zwecken der Einfachheit wird in diesem Artikel davon ausgegangen, dass Sie das Kontexttoken als Einheit zwischenspeichern.
  
    
    

> [!WICHTIG]
> Wir erinnern Sie noch einmal, weil es wirklich wichtig ist: Verwenden Sie keinen clientseitigen Cache für das  *Zugriffstoken*  . Dieser ist nur für das Kontexttoken sicher.
  
    
    

Sie haben dieselben  [serverseitigen Cacheoptionen](#CacheAccessToken) wie oben für das Zugriffstoken aufgeführt. Zu den clientseitigen Optionen zählen ein Cookie und ein ausgeblendetes Formularfeld auf einer HTML-Seite. Eine weitere Option besteht darin, das Kontexttoken im Sitzungscache zu speichern, aber speichern Sie dann den daraus abgerufenen **Cacheschlüssel** auf dem Client.
  
    
    
Wenn Ihre Anwendung auf SharePoint zugreift, nachdem eine Sitzung beendet wurde, sind weder der Sitzungscache noch der clientseitige Cache eine Option, weil das Aktualisierungstoken für die Anwendung verfügbar sein muss, falls das Originalzugriffstoken abgelaufen ist, während die Aufgaben nach der Sitzung ausgeführt werden. In diesem Szenario benötigen Sie einen dauerhaften (sitzungsübergreifenden) Cache, der von mehreren Benutzern und/oder SharePoint-Bereichen und/oder Anwendungen gemeinsam verwendet wird. Ihr Code muss also Cacheschlüssel verwenden, die Benutzer, SharePoint-Bereich und/oder Anwendung unterscheiden, wie weiter oben unter  [Zwischenspeichern des Zugriffstokens](#CacheAccessToken) beschrieben. Sie können für diesen Zweck **den speziellen Cacheschlüssel verwenden**, der sich im Kontexttoken befindet, so wie Sie ihn für das Zugriffstoken verwendet haben. (Siehe [Überblick über den Cacheschlüssel](#CacheKey) weiter unten.)
  
    
    

#### Überblick über den Cacheschlüssel
<a name="CacheKey"> </a>

 **Der Cacheschlüssel ist eine opake Zeichenfolge, die für die Kombination aus Benutzer, Benutzernamenaussteller, Add-In und SharePoint-Farm oder SharePoint Online-Mandant eindeutig ist.** Bevor er verschlüsselt wird, hat er die folgende Form, wobei _Bereich_ die GUID der lokalen SharePoint-Farm oder des SharePoint Online-Mandanten ist.
  
    
    
 _UserNameId_ + "," + _UserNameIdIssuer_ + "," + _ApplicationId_ + "," + _Realm_
  
    
    
Der Cacheschlüssel enthält keine Website-URL-Informationen. Jeder SharePoint Online-Mandant (oder jede lokale SharePoint-Farm) hat einen eindeutigen Bereich, deshalb ist der Cacheschlüssel für jede Kombination aus Benutzername, Benutzernamenaussteller, Anwendung und Farm eindeutig. Im Beispielkontexttoken unten sieht der Wert für den verschlüsselten Cacheschlüssel wie folgt aus:
  
    
    
 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`
  
    
    
Da Ihre Anwendung möglicherweise mehrere Elemente zwischenspeichert, zum Beispiel sowohl das Zugriffstoken als auch das Kontexttoken im selben Cache mit demselben Cacheschlüssel, sollten Sie **in Betracht ziehen, den Cacheschlüssel als Stamm zu verwenden** und dann eine bestimmte Zeichenfolge wie "AccessToken" oder "ContextToken" nach Bedarf anzuhängen oder voranzustellen, um einen vollständigen Cacheschlüssel zu bilden. **Eine weitere Option besteht darin, eine Klasse zu erstellen**, die Eigenschaften für die verschiedenen Dinge enthält, die Sie zwischenspeichern möchten, und dann ein Objekt dieses Typs zwischenzuspeichern. Eine **dritte Option**, zumindest wenn Sie **eine Datenbank als Cache verwenden**, besteht darin, eine Tabelle mit einer Spalte für den Cacheschlüssel und weiteren Spalten für die zwischengespeicherten Elemente (Zugriffstoken, Kontexttoken usw.) zu verwenden.
  
    
    
Ihre Anwendung muss natürlich nicht denselben Cache für alles verwenden, was sie zwischenspeichert. Ein typisches Muster ist, das Zugriffstoken im Sitzungscache, das Kontexttoken (oder das daraus stammende Aktualisierungstoken) in einer Datenbank und den Cacheschlüssel in einem Cookie zwischenzuspeichern.
  
    
    

### Verwenden des Kontexttokens zum Abrufen eines Zugriffstokens
<a name="UseContextTokenToGetAccessToken"> </a>

 **Um ein Zugriffstoken zu erhalten, sendet Ihre Anwendung eine Anforderung direkt an ACS.** Die Anforderung enthält drei Informationen, die aus dem Kontexttoken (und anderen Informationen) extrahiert werden:
  
    
    

- Das Aktualisierungstoken
    
  
- Die Anwendungsprinzipal-GUID von SharePoint
    
  
- Die Bereichs-GUID der SharePoint-Farm oder des SharePoint Online-Mandanten, auf die bzw. den das Add-In zugreifen möchte
    
  
Die Datei TokenHelper.cs (oder .vb) enthält Code, der diese Anforderung erstellt. Einen Beispiel-PHP-Code für diese Aufgabe finden Sie unter  [SharePoint 2013: Durchführen von Vorgängen in der SharePoint-Dokumentbibliothek von einer PHP-Website](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454).
  
    
    
Die Anwendung kann den Bereich des SharePoint-Mandanten oder der Farm während der Laufzeit erhalten statt ihn aus dem Kontexttoken zu analysieren. Wenn Sie verwalteten Code verwenden, gibt es eine  `TokenHelper.GetRealmFromTargetUrl`-Methode, um den Bereich abzurufen. Stellen Sie sicher, dass Sie den Wert zwischenspeichern, damit Ihr Code nicht einen weiteren Netzwerkaufruf durchführt, um ihn erneut abzurufen.
  
    
    

### Abrufen eines neuen Kontexttokens
<a name="GetNewContextToken"> </a>

 **Wenn Sie ein neues Kontexttoken benötigen**, normalerweise weil das Aktualisierungstoken (das in Kontexttoken enthalten ist) abgelaufen ist **, kann Ihr Code ein neues Token abrufen, indem er den Browser auf eine spezielle Seite auf jeder SharePoint-Website** umleitet: AppRedirect.aspx. Sie müssen zwei Abfrageparameter an die URL dieser Seite anhängen:
  
    
    

-  `client_id`: Die Client-ID Ihrer SharePoint-Add-In.
    
  
-  `redirect_uri`: Der URI, an den der Browser umgeleitet werden soll, nachdem das neue Kontexttoken abgerufen wurde. SharePoint fügt das Kontexttoken in diesen URI ein. Normalerweise ist das dieselbe Seite, Contollermethode oder Webdienstmethode, die das neue Kontexttoken angefordert hat. Der Wert muss URL-codiert sein.
    
  
Im Folgenden ist die Struktur der URL dargestellt:
  
    
    



```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>
```

Im Folgenden finden Sie ein Beispiel für die Durchführung der Anforderung in ASP.NET, bei der die TokenHelper-Datei verwendet wird:
  
    
    



```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));
```


### Beispiel für ein Kontexttoken
<a name="ExampleContextToken"> </a>

Im Folgenden sehen Sie ein Beispiel für ein Kontexttoken. Das kleine JavaScript Object Notation (JSON)-Objekt im oberen Bereich enthält Metadaten zum Token. Die Eigenschaften sind dieselben wie bei Zugriffstoken (siehe oben). Der Wert der Eigenschaft **alg** ist der Name des Algorithmus, der für die Erzeugung der Signatur verwendet wird, die ACS an das Token anhängt. Details zu den Eigenschaften in der Nutzlast des Tokens finden Sie in Beispiel 3. Beachten Sie, dass alle Werte kleingeschrieben werden müssen. (Zur besseren Lesbarkeit wurden Leerzeichen eingefügt.)
  
    
    

```
{"typ":"JWT","alg":"HS256"}
.
{
 "aud":"a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":"1335822895",
 "exp":"1335866095",
 "appctxsender":"00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "appctx":"{
            \\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\",
            \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"
           }",
 "refreshtoken":"IAAAAC1Lv5w0OrcFAmJx0xk6aaBdhgsw3VPnPzNEDAWypTHtCYytZ2/dBBUKj+HLK8YB3IUCUfDxYpAque
NHKtgs4rYJJ5AegQpNMOJR1yYK8ngivQx0oetj7aSPuGVb+k6at6G0Kx5LZ5vhxkAq8iUSwu8p4L2cvNMzDF1mDKfMivqxgrIZkr2nbf9as0SJFL6VG5hZnDE4HKq
xJnejSW3umatKM4fsfY1MClVCxrkXb2EQ8H/TmwaJc388YW063GEVUS/3BTSgSIRBKQUmXJuJ6BZY7WTm84LaGrx3mIjnUTM/jnqPoPG55JbCC9sS/MeGNPtzPPCDg
6Vv7dVhQ1Dq5Y3fQ65e9LpJ580jCgzYYvpIFT+Wx5V+17mjY2T8wug04K2ts87Znsr+GfFCorf7NS/lj5HjoxRAQ2tva/8dwguSLwxcUwi/Q9MbpR0NNtlpwVazqi9O
hJ4Df7gVhUDdJ0Dtc6aFCPbl5ZLDDRs42xK2", 
 "isbrowserhostedapp": "true"
}
```

Die Ansprüche **aud**, **iss**, **nbf** und **exp** sind exakt dieselben wie ein einem Zugriffstoken und weiter oben erläutert. Die Ansprüche **appctxsender**, **appctx**, **CacheKey**, **SecurityTokenServiceUri**, **refreshtoken** und **isbrowserhostedapp** werden in der folgenden Tabelle beschrieben.
  
    
    

**Tabelle 3. Ansprüche und Informationen in einem Kontexttoken**


|****Claim****|****Beschreibung****|****Entsprechender Wert im Beispielkontexttoken****|
|:-----|:-----|:-----|
|aud  <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|iss  <br/> ||00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|nbf  <br/> ||1335822895  <br/> |
|exp  <br/> ||1335866095  <br/> |
|appctxsender  <br/> |Abkürzung für "application context sender" und steht für die Anwendung, die das Kontexttoken an die SharePoint-Add-In gesendet hat.  <br/> Der Wert hat das Format  _GUID des Prinzipals_@ _SharePoint-Bereich_, wobei  _GUID des Prinzipals_ die konstante ID des Anwendungsprinzipals ist, entweder SharePoint, Exchange 2013, Lync 2013 oder Workflow. <br/> |00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73  <br/> |
|appctx  <br/> |Abkürzung für "add-in context", ein JSON-Objekt, das **CacheKey** und **SecurityTokenServiceURI** enthält. <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"  <br/> |
|CacheKey  <br/> |Ein eindeutiger Wert, der als Schlüssel in jedem nach Schlüssel/Wert strukturierten Cache verwendet werden kann, um das Kontexttoken zu speichern und abzurufen. Er kann auch als Wert einer Schlüsselspalte in einer Zeile einer Datenbank verwendet werden.  <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=  <br/> |
|SecurityTokenServiceURI  <br/> |Der URI des tokenausstellenden Diensts.  <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2  <br/> |
|refreshtoken  <br/> |Das Aktualisierungstoken für das Add-In.  <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6…  <br/> |
|isbrowserhostedapp  <br/> |Ein **Boolean**-Feld, in dem angegeben wird, ob die Anforderung an das Add-In, das das Kontexttoken enthält, von einem Browser (true) oder von einem Remoteereignisempfänger (false) kommt  <br/> |true  <br/> |
   

### Verwenden des Kontexttokens, um den Zugriff nur auf SharePoint-Benutzer einzuschränken
<a name="UseContextTokenToLimitAccess"> </a>

Wenn Sie den Zugriff auf Ihre Remotekomponente wie einen WCF-Dienst auf SharePoint-Benutzer einschränken möchten, kann Ihr Code einfach das Kontexttoken in der HTTP-Anforderung validieren. (Wenn Sie verwalteten Code verwenden, können Sie einfach **TokenHelper.ReadAndValidateContextToken()** aufrufen.) Ihr Code kann überprüfen, ob der Akteuranspruch mit **00000003-0000-0ff1-ce00-000000000000** beginnt, wenn Sie sicherstellen möchten, dass es sich um SharePoint handelt (und z. B. nicht um Exchange 2013, Lync 2013 oder Workflow). Wenn Sie eine zusätzliche Validierung durchführen möchten, für die ein Rückruf an SharePoint erforderlich ist, beispielsweise um den Zugriff auf Benutzer mit einer bestimmten Rolle in SharePoint einzuschränken, können Sie die Tatsache, dass Sie diese Validierung für einen bestimmten Benutzer durchgeführt haben, zwischenspeichern (indem Sie den **CacheKey** des Kontexttokens verwenden), damit Sie dies nur einmal durchführen müssen.
  
    
    

## Überblick über die Handhabung und das Zwischenspeichern von Aktualisierungstoken
<a name="RefreshTokens"> </a>

 **Ein Aktualisierungstoken ist im Kontexttoken enthalten, das SharePoint an Ihre Webanwendung veröffentlicht, wenn sie gestartet wird.** Das Aktualisierungstoken ist ein verschlüsseltes Token, das Ihre SharePoint-Add-In nicht entschlüsseln kann. **Ihr Code verwendet es**, zusammen mit anderen Informationen, **um ein neues Zugriffstoken abzurufen, wenn das aktuelle Zugriffstoken abgelaufen ist.**. Es wird auch verwendet, um das *erste*  Zugriffstoken im [Kontexttokenablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) abzurufen. (Zum Zeitpunkt, an dem dieser Artikel geschrieben wurde, hatten von ACS ausgestellte Aktualisierungstoken für SharePoint eine Lebensspanne von 6 Monaten, das kann sich aber geändert haben.)
  
    
    
Da ein Zugriffstoken für Stunden gültig ist (derzeit 12) und ein Endbenutzer jedes Mal, wenn er Ihre SharePoint-Add-In von SharePoint startet, ein neues bekommt, brauchen Sie das Aktualisierungstoken nur in den folgenden Szenarien:
  
    
    

- Benutzer haben über lange Zeit laufende Sitzungen mit Ihrem Add-In, in denen das Add-In über viele Stunden (derzeit mehr als 12) nach ihrem Start Aufrufe an SharePoint durchführt.
    
  
- Das Design des Add-Ins ermöglicht Benutzern, einen Zugriff durch das Add-In auf SharePoint zu planen, der einige Zeit nach Beenden der Sitzung erfolgt.
    
  
Für beide **Szenarien muss Ihr Add-In das Aktualisierungstoken zwischenspeichern** und im zweiten Szenario ist ein serverseitiger Cache erforderlich, der über Sitzungen hinweg dauerhaft ist. Ihre Optionen zum Zwischenspeichern sind dieselben wie für das [Zugriffstoken](#CacheAccessToken) und im Kontexttokenablauf können Sie [den Cacheschlüssel im Kontexttoken verwenden](#CacheKey). (Im Kontexttokenablauf speichern Sie für gewöhnlich das Kontexttoken nur zwischen. Es enthält das Aktualisierungstoken und andere Informationen, die Sie benötigen, um ein neues Zugriffstoken zu erhalten. Im  [Autorisierungscodeablauf](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) gibt es kein Kontexttoken, deshalb speichern Sie das Aktualisierungstoken selbst zwischen.)
  
    
    
Wenn Sie das Aktualisierungstoken in einem Speicher zwischenspeichern, der über die Sitzungen eines bestimmten Benutzers mit Ihrem Add-In bestehen bleibt, achten Sie darauf, dass Sie es durch das neueste Aktualisierungstoken ersetzen. Sowohl im in der Cloud gehosteten als auch im Autorisierungscodeablauf erhält der Benutzer jedes Mal ein neues Aktualisierungstoken, wenn er das Add-In startet.
  
    
    
Wenn das Aktualisierungstoken abgelaufen ist, führt eine Anforderung an ACS für ein neues Zugriffstoken zu einem **401 Unauthorized**-Fehler. Ihr Add-In sollte auf diesen Fehler reagieren, indem es ein neues Aktualisierungstoken abruft und es verwendet, um ein neues Zugriffstoken zu erhalten. (Da das Aktualisierungstoken verschlüsselt ist, kann ihr Code den Ablaufzeitpunkt nicht überprüfen, bevor er es verwendet.) Im Kontexttokenablauf erhält Ihr Add-In ein neues Aktualisierungstoken, indem es  [ein neues Kontexttoken abruft](#GetNewContextToken). Im Autorisierungscodeablauf erhält Ihr Add-In ein neues Aktualisierungstoken, indem es den Ablauf neu startet. Ihr Code sollte spezifisch auf den Fehler reagieren, indem er den Benutzer zur SharePoint OAuthAuthorize.aspx-Seite umleitet, wie unter  [Informationen zum OAuth-Ablauf für Add-Ins, die dynamisch Berechtigungen anfordern](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow) beschrieben.
  
    
    

## Überblick über die Handhabung von Autorisierungscodes
<a name="Authcodes"> </a>

 **Im Autorisierungscodeablauf beginnt der Autorisierungsprozess mit einem Autorisierungscode, den ACS auf Anforderung von SharePoint ausstellt und den SharePoint dann** als Abfrageparameter an die Remoteanwendung übergibt. Der Autorisierungscode ist für jede Paarung aus Benutzer und Remoteanwendung eindeutig. (Zum Zeitpunkt, an dem dieser Artikel geschrieben wurde, hatten von ACS ausgestellte Autorisierungscodes für SharePoint eine Lebensspanne von 5 Minuten, das kann sich aber geändert haben.) Die Logik in **Ihrer Anwendung muss den Autorisierungscode aus dem Abfrageparameter abrufen und ihn in einer Anforderung an ACS für ein Zugriffstoken verwenden**. Wenn Sie verwalteten Code verwenden, finden Sie Beispielcode für das Erstellen des Tokens in der Datei TokenHelper.cs (und .vb). Beispielcode für das Lesen des Abfrageparameters finden Sie unter [Beispielcode für eine Seite, die auf SharePoint zugreift](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default). ACS macht den Autorisierungscode direkt nach der Ausgabe des Zugriffscodes ungültig, damit er nur einmal verwendet werden kann. Außerdem macht es keinen Sinn, ihn zwischenzuspeichern.
  
    
    

## Arbeiten mit JWT-Zeitwerten
<a name="JWTtimes"> </a>

Die Ansprüche **nbf** und **exp** haben das durch die [JWT-Spezifikation](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl) angegebene Format. Sie werden als Anzahl der Sekunden seit dem 1. Januar 1970 dargestellt. In C# können Sie diese Werte mit dem folgenden Code übersetzen, wobei _jWTTimeStamp_ der Wert aus dem Token ist, z. B. 1335822895.
  
    
    

```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);

```


## Problembehandlung bei der Tokenhandhabung
<a name="Troubleshooting"> </a>

Das kostenlose  [Fiddler-Tool](http://www.telerik.com/fiddler) kann verwendet werden, um die HTTP-Anforderungen zu erfassen, die von der Remotekomponente Ihres Add-Ins SharePoint gesendet werden. Es gibt eine [kostenlose Erweiterung für das Tool](https://github.com/andrewconnell/SPOAuthFiddlerExt), die automatisch die Token in den Anforderungen dekodiert.
  
    
    

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Erstellen von SharePoint-Add-Ins, die die Autorisierung mit niedriger Vertrauensebene verwenden](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
- Codebeispiele, die verwalteten Code und TokenHelper verwenden, finden Sie unter  [SharePoint 2013: Remote-Add-In "Hello World" mit CSOM](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf) und [SharePoint-Add-Ins - Paket mit Beispielen](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
- Einen Beispielcode, in dem REST-Aufrufe von einem PHP-Add-In verwendet werden, finden Sie unter:  [SharePoint 2013: Durchführen von Vorgängen in der SharePoint-Dokumentbibliothek von einer PHP-Website](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef)
    
  

