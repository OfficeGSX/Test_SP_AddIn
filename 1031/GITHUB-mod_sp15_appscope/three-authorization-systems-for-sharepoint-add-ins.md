---
title: Drei Autorisierungssysteme für SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: 623fdab7-856e-4a89-9f5d-748a2ba1ef2e
---


# Drei Autorisierungssysteme für SharePoint-Add-Ins
Erfahren Sie mehr über die drei Systeme, die SharePoint-Add-Ins verwenden kann, um die Autorisierung zu SharePoint-Ressourcen zu erhalten.
In SharePoint ist eine SharePoint-Add-In genau wie ein Benutzer ein Identitätsprinzipal, der für die Verwendung von SharePoint-Ressourcen authentifiziert und autorisiert werden muss. Eine App kann drei Authorisierungssysteme verwenden. Sie schließen sich nicht gegenseitig aus.





## So funktionieren die drei Authorisierungssysteme und dafür können sie verwendet werden
<a name="UnderstandThreeSystems"> </a>






- **Niedrige Vertrauensebene**: Eine vom Anbieter gehostete SharePoint-Add-In kann in Microsoft Azure Access Control Service (ACS) registriert werden. Dann wird ein Zugriffstoken für die App ausgestellt, mit dem die App auf die Ressourcen in der SharePoint-Mandantschaft oder -Farm zugreifen kann, in der die App installiert ist. Azure ACS ist der vertrauenswürdige Tokenherausgeber in einem OAuth 2.0 Framework-"Ablauf", zu dem SharePoint und die Remotekomponenten der App gehören. Apps, die dieses System verwenden, können im Office Store verkauft werden. Das System mit niedriger Vertrauensebene ist in erster Linie für Apps gedacht, deren Remotekomponenten in der Cloud gehostet sind.

    Weitere Informationen zum Erstellen einer SharePoint-Add-In, die das System mit niedriger Vertauensebene verwendet, finden Sie im SDK-Knoten  [Erstellen von SharePoint-Add-Ins, die die Autorisierung mit niedriger Vertrauensebene verwenden](creating-sharepoint-add-ins-that-use-low-trust-authorization.md).

    > **HINWEIS**
    > Der Kunde, der die App installiert, muss über ein Office 365-Konto verfügen. Dieses ist erforderlich, damit die App Zugriff auf Azure ACS hat. Der Kunde benötigt dieses Konto jedoch für keinen anderen Zweck und die App kann in einer lokalen SharePoint-Farm installiert werden, nachdem einige einfache Konfigurationsaufgaben auf der Farm durchgeführt wurden. 

- **Besonders vertrauenswürdig**: Eine vom Anbieter gehostete App kann eine Vertrauensstellung mit SharePoint einrichten, indem sie digitale Zertifikate verwendet. Das besonders vertrauenswürdige System ist primär für Apps vorgesehen, deren Remotekomponenten lokal gehostet werden. Die App kann in einer SharePoint-Farm installiert werden, die nicht mit dem Internet verbunden ist. Die App kann nicht auf SharePoint Online installiert oder im Office Store verkauft werden.

    Weitere Informationen zum Erstellen einer SharePoint-Add-In die das besonders vertrauenswürdige System verwendet, finden Sie im SDK-Knoten  [Erstellen von Add-Ins für SharePoint, die eine Autorisierung mit hoher Vertrauenswürdigkeit verwenden](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).


- **Domänenübergreifende Bibliothek**: Wenn sich die Geschäftslogik der App in JavaScript befindet, können Sie die domänenübergreifende SharePoint- Bibliothek anstelle der oder als Ergänzung zum System mit niedriger Vertrauensebene oder dem besonders vertrauenswürdigen System verwenden. Die Bibliothek ist außerdem für Szenarien vorgesehen, in denen die App in der Cloud gehostete Komponenten aufweist, die Unternehmensfirewall des Kunden jedoch die Verwendung des Systems mit niedriger Vertrauensebene erschwert. Der Browser des Benutzers sperrt Skripts von anderen Domänen, die Bibliothek kapselt jedoch ein sicheres System, um diese Einschränkung zu umgehen. Apps, die die Bibliothek verwenden, können im Office Store verkauft und auf SharePoint Online oder lokal in SharePoint installiert werden.

    Weitere Informationen zum Erstellen einer SharePoint-Add-In, die die domänenübergreifende Bibliothek verwendet, finden Sie im SDK-Knoten  [Erstellen von SharePoint-Add-Ins, die die domänenübergreifende Bibliothek verwenden](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md), dem Blogbeitrag  [Lösen von domänenübergreifenden Problemen in Apps für SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx).



## Hintergrundinformationen zum OAuth 2.0-Framework und dessen SharePoint-Implementierung
<a name="UnderstandThreeSystems"> </a>

Zwei der drei Autorisierungssysteme verwenden das OAuth 2.0-Framework. OAuth 2.0 ist ein **offenes Framework für die Autorisierung**. OAuth ermöglicht eine sichere Autorisierung von Desktopcomputern, Geräten und Webanwendungen auf eine Standardweise. Mit OAuth kann ein Benutzer eine Anwendung in seinem Namen agieren lassen, ohne seinen Benutzernamen und sein Kennwort weitergeben zu müssen. Beispielsweise ermöglicht OAuth **einem Benutzer die Weitergabe** von privaten Ressourcen (Kontaktlisten, Dokumenten, Fotos, Video usw.) von einer Website an eine andere, **without requiring the user to provide his or her credentials** (normalerweise Benutzername und Kennwort) an die andere Website bereitstellen muss.



Mit OAuth können Benutzer Zugriffstoken für Daten bereitstellen, die bei einem bestimmten Dienstanbieter gehostet sind (wie SharePoint). Jedes Token gewährt Zugriff auf einen bestimmten Ressourcenanbieter (zum Beispiel eine SharePoint-Website) oder auf bestimmte Ressourcen (z. B. Dokumente in einer SharePoint-Dokumentbibliothek) für einen definierten Zeitraum (z. B. 12 Stunden). Damit kann ein Benutzer einer Drittanbieterwebsite oder einer Desktopanwendung Zugriff auf Informationen gewähren, die bei einer anderen Ressource oder einem anderen Dienstanbieter (wie SharePoint) gespeichert sind, ohne seinen Benutzernamen und sein Kennwort und ohne  *alle*  bei diesem Anbieter gespeicherten Daten weiterzugeben.



SharePoint verwendet die **OAuth 2.0** -Framework **-Tokenübergabe-"Abläufe"** für die folgenden Zwecke:




- Autorisieren von Anforderungen einer SharePoint-Add-In für den Zugriff auf SharePoint-Ressourcen


- Authentifizieren von Apps im Office Store, einem App-Katalog oder einem Entwicklermandanten


Weitere Informationen und Hintergrundwissen zu OAuth und OAuth-Terminologie finden Sie unter  [OAuth.net](http://oauth.net/) und [Web Authorization Protocol (oauth)](http://datatracker.ietf.org/doc/active/). Insgesamt gibt es einen Ressourcenserver, der eine geschützte Ressource hostet, einen Eigentümer einer Ressource, eine Clientanwendung, die auf die Ressource zugreifen möchte, und einen Autorisierungsserver, der Zugriffstoken auf den Ressource ausstellt, wenn er vom Ressourceneigentümer dazu angewiesen wird. In der offiziellen OAuth-Dokumentation wird meist von einem Szenario ausgegangen, bei dem es einen einzigen Ressourceneigentümer gibt, der jedes Mal, wenn die Clientanwendung ausgeführt wird, Zugriff auf die Ressource von der Clientanwendung gewährt. Es wird außerdem davon ausgegangen, dass die Person, die die Clientanwendung verwendet, der Ressourceneigentümer ist. Die SharePoint-Implementierung berücksichtigt die Tatsache, dass eine SharePoint-Ressource, beispielsweise eine Liste, von mehreren Benutzer gemeinsam verwendet wird. In der SharePoint-Implementierung wird der SharePoint-Add-In außerdem der Zugriff gewährt, wenn sie installiert wird, nicht bei jeder Ausführung. Sie kann zudem von anderen Benutzern ausgeführt werden als der Person, die sie installiert und ihr den Zugriff gewährt hat. (Im Fall von Apps, die nicht in SharePoint installiert sind, aber auf SharePoint-Ressourcen zugreifen (und damit nur im erweiterten Sinn "SharePoint-Add-Ins" sind), muss der Benutzer, der die App ausführt, bei jeder Ausführung der App Berechtigungen gewähren.)



In der SharePoint-Implementierung werden also die OAuth-Rollen von den folgenden Komponenten übernommen:




- Die Remotekomponente einer SharePoint-Add-In übernimmt die Rolle der Clientanwendung.


- SharePoint-Benutzer übernehmen die Rolle des Ressourceneigentümers.


- SharePoint übernimmt die Rolle des Ressourcenservers.


- Azure ACS übernimmt die Rolle des Autorisierungsservers, wenn  [das Autorisierungssystem mit niedriger Vertrauensebene](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) verwendet wird. Wenn das [besonders vertrauenswürdige System](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) verwendet wird, wird die App selbst (zusammen mit einem digitalen Zertifikat) zum Autorisierungsserver.



### Zugriffstoken sind keine Anmeldetoken
<a name="FileName_uniquekeyword3"> </a>

Wie oben beschrieben, muss eine App für den Zugriff auf Ressourcen ein Zugriffstoken von einem OAuth-Autorisierungsserver anfordern, der zuvor vom Ressourceneigentümer zu einem vertrauenswürdigen Sicherheitstokenaussteller (STS) designiert wurde. Im Gegensatz dazu sind die WS-Federation-STS und die passiven Anmelde-Security Assertion Markup Language (SAML)-STS vorrangig dazu gedacht, Anmeldetoken auszugeben. In SharePoint wird ein OAuth-STS nicht für das Ausstellen von Anmeldetoken verwendet, das heißt, er wird nicht als Identitätsanbieter verwendet. Deshalb werden Sie keinen OAuth-STS auf der Benutzeranmeldeseite, dem Abschnitt **Authentifizierungsanbieter** in der zentralen Verwaltung oder der Personenauswahl in SharePoint aufgelistet sehen.



SharePoint-Administratoren können Windows PowerShell-Befehle verwenden, um einen OAuth-STS zu aktivieren oder zu deaktivieren, ähnlich wie das Aktivieren oder Deaktivieren von vertrauenswürdigen Anmeldeanbietern in SharePoint 2010. 




