---
title: Vorgehensweise Erstellen von Add-Ins für SharePoint, die von anonymen Benutzern verwendet werden können
ms.prod: SHAREPOINT
ms.assetid: e858fce6-7a8f-4b1e-b053-64dc75345801
---


# Vorgehensweise: Erstellen von Add-Ins für SharePoint, die von anonymen Benutzern verwendet werden können
Erfahren Sie, wie Sie SharePoint-Add-Ins erstellen, die von anonymen Benutzern auf öffentlich zugänglichen Microsoft SharePoint 2013-Websites verwendet werden können.
> [!WICHTIG]
> Wenn in diesem Artikel der Begriff  *lokal*  SharePoint 2013 verwendet wird, wird davon ausgegangen, dass Service Pack 1 für SharePoint 2013 installiert wurde.
  
    
    


## Voraussetzungen für die Erstellung anonym zugänglicher SharePoint-Add-Ins

Anonymer Zugriff ist für in SharePoint gehostete und vom Anbieter gehostete SharePoint-Add-Ins möglich. Je nach Art der App, die Sie erstellen, sollten Sie einen der folgenden Sätze von Voraussetzungen prüfen:
  
    
    

-  [](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#SP15SPhostedapps_bk_prereqs)
    
  
-  [](get-started-creating-provider-hosted-sharepoint-add-ins.md#SP15createselfhostapp_bk_prereq)
    
  
Sie benötigen in Ihrer SharePoint 2013-Testinstallation eine Websitesammlung, die für anonymen Zugriff konfiguriert ist. Wenn Sie eine Website für Office 365-Entwickler haben, ist damit bereits eine öffentliche Websitesammlung verknüpft, die eine spezielle "Public Website"-Websitedefinition verwendet. (Weitere Informationen zur Verwendung von öffentlichen Websites in Microsoft SharePoint Online finden Sie unter  [Hilfe zu öffentlichen Websites für Office 365](http://office.microsoft.com/de-de/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1).) Diese Websitedefinition ist für lokale SharePoint 2013-Installationen nicht verfügbar. Wenn Ihre Testinstallation lokal ist, benötigen Sie Folgendes:
  
    
    

- Eine SharePoint-Webanwendung, die so konfiguriert ist, dass sie anonymen Zugriff zulässt. Diese Konfiguration muss aber erfolgen, nachdem die SharePoint-Add-In installiert wurde, die Sie testen.
    
  
- Eine Websitesammlung innerhalb der Webanwendung, die selbst für anonymen Zugriff konfiguriert ist. Diese Konfiguration muss aber erfolgen, nachdem die SharePoint-Add-In installiert wurde, die Sie testen.
    
  
- Wenn Ihr Add-In in SharePoint gehostet wird und auf eine SharePoint-Liste zugreift: eine Liste in der Websitesammlung, die für anonymen Zugriff konfiguriert ist.
    
  
Anleitungen für diese Aufgaben sind im Abschnitt  [Konfigurieren einer SharePoint-Webanwendung und einer Websitesammlung und Liste für anonymen Zugriff](#Configuring) zu finden.
  
    
    

## Einschränkungen bei der Verwendung von Add-Ins für SharePoint durch anonyme Benutzer

Berücksichtigen Sie die folgenden Fakten, wenn Sie Ihre SharePoint-Add-In entwerfen:
  
    
    

- Wenn die SharePoint-Add-In in SharePoint gestartet wird, muss sie eine benutzerdefinierte Aktion oder ein Add-In-Webpart enthalten oder über einen benutzerdefinierten Link auf einer Seite gestartet werden. Der Grund dafür ist, dass Benutzer das Add-In nicht über die entsprechende Kachel starten können. Der Mechanismus, durch den Add-Ins über eine Kachel gestartet werden, erfordert den Einsatz einer speziellen  [Anwendungsseite](http://msdn.microsoft.com/library/685c8e01-b163-4b5e-888c-421d9ecbb25e%28Office.15%29.aspx), die für anonyme Benutzer nicht zugänglich ist. Eine weitere Möglichkeit ist, den Start des Add-Ins von außerhalb von SharePoint zu ermöglichen. In diesem Fall muss es die Nur-Add-In-Autorisierungsrichtlinie verwenden.
    
  
- Ein anonymer Benutzer kann nur SharePoint-Add-Ins starten, die von anderen installiert wurden. Der Grund dafür ist, dass anonyme Benutzer die Seite **Add-In hinzufügen** nicht öffnen können.
    
  
- Wenn ein angemeldeter Benutzer zu einer Website in einer lokalen SharePoint-Webanwendung navigiert, die für anonymen Zugriff konfiguriert wurde, wird die Identität des Benutzers in **Systemkonto** geändert. Diese Identität kann SharePoint-Add-Ins nicht installieren. Da anonyme Benutzer Add-Ins auch nicht installieren können, bedeutet dies, dass niemand SharePoint-Add-Ins installieren kann, wenn die Webanwendung für anonymen Zugriff konfiguriert ist. Entsprechend sollten SharePoint-Add-Ins auf Websites in der SharePoint-Webanwendung installiert werden, bevor die Webanwendung für anonymen Zugriff konfiguriert wird. Wenn später weitere Add-Ins installiert werden müssen, muss die Webanwendung vorübergehend so geändert werden, dass anonymer Zugriff deaktiviert wird, sodass die Add-Ins von einem angemeldeten Benutzer installiert werden können.
    
  
- Um die REST-APIs für die SharePoint-Suchfunktion in einer lokalen SharePoint-Website zu aktivieren, können Sie Abfrage-XML konfigurieren. Nähere Informationen dazu finden Sie unter  [Aktivieren von anonymen Such-REST-Abfragen](http://msdn.microsoft.com/library/office/jj163876.aspx#bk_AnonymousREST).
    
  
- Daten im Add-In-Web werden von den Suchindexern nicht durchforstet, sodass benutzerdefinierte Daten remote oder im Hostweb bereitgestellt werden müssen. Dies gilt für alle SharePoint-Add-Ins, wird aber hier erwähnt, da auf anonymen Zugriff ausgelegte Add-Ins häufig eine Suchfunktion erfordern.
    
  

## Erstellen von anbietergehosteten Add-Ins, die anonym zugänglich sind
<a name="Cloud-hosted"> </a>

Um ein vom Anbieter gehostetes Add-In für anonyme Benutzer zugänglich zu machen, muss es lediglich so konfiguriert werden, dass es die Nur-Add-In-Autorisierungsrichtlinie verwendet. Wenn diese Richtlinie verwendet wird, fügt SharePoint nicht einmal Benutzerkontext ein, sodass es keine Rolle spielt, dass der Benutzer anonym ist. Es ist nur wichtig, dass dem Add-In-Prinzipal vom Mandantenadministrator, der das Add-In installiert, alle Berechtigungen gewährt werden, die er für das Hostweb benötigt, sowie alle Berechtigungen, die er für die übergeordnete Websitesammlung oder den übergeordneten Mandanten benötigt. Sie fordern Berechtigungen für das Hostweb im Add-In-Manifest an, genau so, wie Sie dies bei jedem anderen Add-In tun würden.
  
    
    

> [!HINWEIS]
> Wenn die SharePoint-Website für anonyme Benutzer zugänglich ist, erlaubt sie anstatt HTTPS-Zugriff normalerweise HTTP-Zugriff. Es können Sicherheitsprobleme auftreten, wenn in diesem Szenario die Nur-Add-In-Richtlinie für Add-Ins verwendet wird. Nähere Informationen dazu sowie eine Methode zur Behebung der Probleme finden Sie unter  [Was jeder Entwickler über SharePoint-Add-Ins, CSOM und anonyme Veröffentlichungswebsites wissen muss](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx). 
  
    
    

Um ein Add-In so zu entwerfen, dass es die Nur-Add-In-Richtlinie verwendet, sind zwei Dinge erforderlich:
  
    
    

- Sie müssen  `AllowAppOnlyPolicy="true"` zum **AppPermissionRequests**-Element im Add-In-Manifest hinzufügen.
    
  
- Ihr Code muss vom OAuth-Autorisierungsserver einen Nur-Add-In-Zugriffstoken anfordern. Wenn Sie mit verwaltetem Code arbeiten, gibt es in den Codedateien, die von Microsoft Office-Entwicklertools für Visual Studio generiert werden, APIs, die speziell zu diesem Zweck erstellt wurden: "SharePointContext.cs" (oder ".vb") und "TokenHelper.cs" (oder ".vb").
    
  
Nähere Informationen zur Nur-Add-In-Richtlinie (mit Codeausschnitten), zu Add-In-Berechtigungen und zum Add-In-Manifest finden Sie in diesen Themen:
  
    
    

-  [Add-In-Autorisierungsrichtlinientypen in SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)
    
  
-  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Hinweise zur App-Manifeststruktur und zum Paket eines SharePoint-Add-Ins](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

## Erstellen von in SharePoint gehosteten Add-Ins, die anonym zugänglich sind
<a name="SP-hosted"> </a>

Um ein in SharePoint gehostetes Add-In zu erstellen, das von anonymen Benutzern ausgeführt werden kann, sind keine speziellen Techniken erforderlich. Es wird genauso erstellt wie jedes andere in SharePoint gehostete Add-In. Nähere Informationen finden Sie unter  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md) und [Ausführen grundlegender Vorgänge unter Verwendung von JavaScript-Bibliothekscode in SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md).
  
    
    
Was wirklich wichtig ist, um ein in SharePoint gehostetes Add-In für anonyme Benutzer verwendbar zu machen, ist, dass ein Mandantenadministrator die SharePoint Online-Websitesammlung so konfiguriert, dass sie anonymen Zugriff zulässt. Es ist Ihnen als Entwickler unmöglich, diese Konfiguration über das Add-In, einen Remote-Add-In-Ereignisempfänger oder die SharePoint-Add-In-Installationsinfrastruktur vorzunehmen. Sie können in das Add-In eine benutzerdefinierte Liste oder Bibliothek einschließen, aber Collaborative Application Markup Language (CAML) bietet keine Möglichkeit, es vorab für anonymen Zugriff zu konfigurieren. Daher muss dieser nach der Installation des Add-Ins konfiguriert werden.
  
    
    
Wenn Sie Ihr Add-In über den Office Store vertreiben und ein erheblicher Anteil Ihrer möglichen Kunden es besonders hilfreich fänden, wenn es für anonyme Benutzer zugänglich wäre, sollten Sie erwägen, diese Konfigurationsanforderung in Ihrer Add-In-Beschreibung zu erwähnen. Erwägen Sie außerdem, unten auf der Supportseite Ihres Add-Ins eine Version des Verfahrens **So konfigurieren Sie eine SharePoint Online-Websitesammlung für anonymen Zugriff** einzufügen.
  
    
    
Wenn Ihr Add-In das JavaScript-Objektmodell (JSOM) von SharePoint verwendet, gibt es nur eine sehr wichtige Konfigurationsanforderung. Nur ein sehr kleiner Teil der JSOM-APIs ist standardmäßig für anonyme Benutzer zugänglich. Alle anderen erfordern, dass ein Benutzer die Berechtigung **Remoteschnittstellen verwenden** besitzt, und anonyme Benutzer besitzen diese Berechtigung nicht. Diese Anforderung muss in der übergeordneten Websitesammlung oder übergeordneten SharePoint-Webanwendung der Website deaktiviert werden, in der das Add-In installiert ist. Eine Beschreibung dazu finden Sie unter [Konfigurieren einer SharePoint-Webanwendung und einer Websitesammlung und Liste für anonymen Zugriff](#Configuring).
  
    
    

> [!HINWEIS]
> Wenn Sie die Anforderung, dass Benutzer die Berechtigung **Remoteschnittstellen verwenden** besitzen, deaktivieren, hat dies Auswirkungen auf den Datenschutz. Nähere Informationen finden Sie unter [Was jeder Entwickler über SharePoint-AApps, CSOM und anonyme Veröffentlichungswebsites wissen muss](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx). 
  
    
    


## e>Einschränkungen von in SharePoint gehosteten Add-Ins für anonyme Benutz
<a name="SP-hosted"> </a>

a>Wir sind ehrlich mit Ihnen: In SharePoint gehostete Add-Ins für anonyme Benutzer weisen einige erhebliche Einschränkungen auf, die Sie kennen sollte
  
    
    

- Es ist nicht möglich, eine Liste oder Bibliothek in einer SharePoint Online für anonyme Benutzer zugänglich zu machen. Entsprechen können JSOM-APIs, die Interaktionen mit Listen oder Bibliotheken erfordern, in SharePoint Online nicht von anonymen Benutzern verwendet werden.
    
  
- a>Ein in SharePoint gehostetes Add-In, das auf einer  *lokalen*  SharePoint-Website installiert ist, kann alle JSOM-APIs verwenden. Die Webanwendung, die Websitesammlung sowie Listen oder Bibliotheken müssen allerdings *manuell*  für anonymen Zugriff konfiguriert werden, und die Berechtigungsanforderung **Remoteschnittstellen verwenden** muss deaktiviert werden. Wenn das Add-In im Add-In-Web benutzerdefinierte Listen oder Bibliotheken installiert, ist es Aufgabe der Benutzer, diese nach der Add-In-Installation manuell für anonymen Zugriff zu konfiguriere
    
  
- a>Da Daten im Add-In-Web nicht von den Suchindexern durchforstet werden und es keine Möglichkeit gibt, benutzerdefinierte Listen oder Bibliotheken im Hostweb zu installieren, und da in SharePoint gehostete Add-Ins keine Remotekomponenten oder Ereignisempfänger enthalten können, sind benutzerdefinierte Daten in einem in SharePoint gehosteten Add-In nicht durchsuchba
    
  

## Konfigurieren einer SharePoint-Webanwendung und einer Websitesammlung und Liste für anonymen Zugriff
<a name="Configuring"> </a>

Wenn Ihre SharePoint-Testinstallation lokal ist, müssen Sie die ersten beiden unten stehenden Verfahren ausführen, um zu testen, ob Ihre SharePoint-Add-In von anonymen Benutzern verwendet werden kann. Die Kunden, die Ihre SharePoint-Add-In installieren, müssen diese Verfahren auch für die übergeordnete Websitesammlung und die Webanwendung jeder Website durchführen, auf der Ihre SharePoint-Add-In installiert ist.
  
    
    

> [!WICHTIG]
> a>Wenn möglich, sollten Sie das SharePoint-Add-In auf einer Website installieren,  *bevor*  Sie die ersten beiden Verfahren ausführen. Add-Ins können nicht auf jede Website in einer lokalen SharePoint-Webanwendung installiert werden, wenn die Webanwendung für anonymen Zugriff installiert wurde. Wenn die Webanwendung bereits für anonymen Zugriff installiert wurde, müssen Sie die Einstellung vorübergehend deaktivieren, um das Add-In zu installiere
  
    
    


### So konfigurieren Sie die übergeordnete Webanwendung für anonymen Zugriff


1. Wählen Sie in der **Zentraladministration** **Anwendungsverwaltung** und dann **Webanwendungen verwalten** aus.
    
  
2. (Optional) Wenn Sie für keine der vorhandenen SharePoint-Webanwendungen anonymen Zugriff erlauben möchten, erstellen Sie eine neue Webanwendung. (Das Webanwendungs-Erstellformular bietet Ihnen zwar die Option, anonymen Zugriff zuzulassen.  *Diese Option sollten Sie allerdings nicht verwenden*  . Anonymer Zugriff sollte erst aktiviert werden, *nachdem*  die SharePoint-Add-In installiert wurde.) Nähere Informationen finden Sie unter [Create a web application in SharePoint 2013](http://msdn.microsoft.com/library/121c8d83-a508-4437-978b-303096aa59df.aspx).
    
  
3. Wählen Sie in der Liste der Webanwendungen eine aus, die für anonyme Benutzer zugänglich sein soll, und wählen Sie im Menüband **Authentifizierungsanbieter** aus.
    
  
4. Wählen Sie im daraufhin angezeigten Popup die Zone aus, für die anonymer Zugriff aktiviert werden soll. Normalerweise ist dies **Internet** oder **Standard**. Das Formular **Authentifizierung bearbeiten** wird geöffnet.
    
  
5. Aktivieren Sie das Kontrollkästchen **Anonymen Zugriff aktivieren**, falls es nicht bereits aktiviert ist. Damit wird eine Standardeinstellung festgelegt, die für bestimmte Websitesammlungen deaktiviert werden kann. Wenn Sie diese allerdings für die Webanwendung nicht aktivieren, können Sie sie für keine Websitesammlung aktivieren.
    
  
6. a>(Optional) Deaktivieren Sie das Kontrollkästchen **Berechtigung 'Remoteschnittstellen verwenden' verlangen**. Dadurch wird Code und Skript, der bzw. das im Kontext eines anonymen Benutzers ausgeführt wird, erlaubt, in jeder Websitesammlung Aufrufe an das Clientobjektmodell von SharePoint zu richten. Sie können die Anforderung für keine Websitesammlung erneut aktivieren. Wenn Sie das Kontrollkästchen aktiviert lassen, bedeutet dies, dass anonyme Benutzer standardmäßig nicht auf die Clientobjektmodelle zugreifen können, aber Sie können die Anforderung für bestimmte Websitesammlungen deaktivieren (und ihnen den Zugriff erlauben
    
    > [!HINWEIS]
      > Im Zusammenhang mit der Entwicklung von SharePoint-Add-Ins für anonyme Benutzer ist diese Einstellung nur für in SharePoint gehostete Apps von Bedeutung. Vom Anbieter gehostete SharePoint-Add-Ins, die auf anonyme Benutzer ausgelegt sind, verwenden eine Technik, welche die Berechtigungen des Benutzers irrelvant machen. Weitere Informationen hierzu finden Sie oben im Abschnitt  [Erstellen von anbietergehosteten Add-Ins, die anonym zugänglich sind](#Cloud-hosted)
7. a>Wählen Sie zum Schließen des Formulars **Speichern** au
    
  
8. Wählen Sie im Menüband **Richtlinie für anonymen Zugriff** aus, während die Webanwendung markiert ist.
    
  
9. Wählen Sie im Formular **Einschränkungen für anonymen Zugriff** die Zone aus, und achten Sie darauf, dass das Optionsfeld **Keine** aktiviert ist. Wenn die SharePoint-Add-In, die Sie testen, nur Leserechte für SharePoint-Daten benötigt, aktivieren Sie stattdessen **Schreiben verweigern**.
    
    > [!HINWEIS]
      > a>Dies ist eine weitere Einstellung, die im Zusammenhang mit der Entwicklung von SharePoint-Add-Ins für anonyme Benutzer nur für in SharePoint gehosteten Add-Ins von Bedeutung is 
10. Wenn Sie in Schritt 2 eine neue Webanwendung erstellt haben, müssen Sie darin eine Websitesammlung erstellen.
    
  

### So konfigurieren Sie eine lokale Websitesammlung für anonymen Zugriff


1. Wählen Sie auf der Startseite einer Websitesammlung in der Webanwendung das Zahnradsymbol und dann **Websiteeinstellungen** aus.
    
  
2. Wählen Sie im Abschnitt **Benutzer und Berechtigungen** der Seite **Websiteeinstellungen** die Option **Websiteberechtigungen** aus.
    
  
3. Wählen Sie auf dem Menüband **Anonymer Zugriff** aus.
    
  
4. a>Wählen Sie im Formular **Anonymer Zugriff** **Gesamte Website** oder **Listen und Bibliotheken** aus, je nachdem, welche Zugriffsebene das Add-In benötigt, das Sie testen. (Unabhängig davon, welche Option Sie auswählen, können anonyme Benutzer nur dann auf eine Liste oder Bibliothek mit einem in SharePoint gehosteten Add-In zugreifen, wenn die Liste oder Bibliothek einzeln für anonymen Zugriff konfiguriert ist. Das unten stehende Verfahren beschreibt, wie Sie dabei vorgehe
    
  
5. a>Wenn das Add-In, das Sie testen, auf das SharePoint-Clientobjektmodell zugreifen muss, achten Sie darauf, dass das Kontrollkästchen **Berechtigung 'Remoteschnittstellen verwenden' verlangen** *nicht*  aktiviert ist. Wenn das Kontrollkästchen ausgegraut ist, wurde die Anforderung auf der Webanwendungsebene deaktiviert, was den gleichen Effekt hat wie das deaktivierte Kontrollkästchen. (Wie im vorherigen Verfahren erwähnt, ist diese Einstellung im Zusammenfassung mit der Entwicklung von SharePoint-Add-Ins für anonyme Benutzer nur für in SharePoint gehostete Add-Ins relevant
    
  

> [!WICHTIG]
> Das folgende Verfahren kann nur auf einer öffentlichen Website in SharePoint Online ausgeführt werden. (Weitere Informationen zur Verwendung von öffentlichen Websites in Microsoft SharePoint Online finden Sie unter  [Hilfe zu öffentlichen Websites für Office 365](http://office.microsoft.com/de-de/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1).) 
  
    
    


### So konfigurieren Sie eine SharePoint Online-Websitesammlung für anonymen Zugriff


1. Melden Sie sich bei Office 365 als Mandantenadministrator an.
    
  
2. Wählen Sie auf der Startseite der Websitesammlung nahe der oberen rechten Ecke der Seite **WEBSITE ONLINE STELLEN** aus. Mit dieser Aktion wird die Anforderung **Berechtigung 'Remoteschnittstellen verwenden' verlangen** deaktiviert.
    
  
a>Wenn Sie ein in SharePoint gehostetes Add-In entwickeln und es auf eine SharePoint-Liste zugreift, müssen Sie in Ihrer Testwebsitesammlung auch das folgende Verfahren ausführen. Auch Kunden, die Ihr Add-In verwenden, müssen dieses Verfahren auf jeder Website durchführen, auf der das Add-In installiert ist. Einem Add-In ist es nicht möglich, programmgesteuert oder deskriptiv eine Liste für anonymen Zugriff zu konfigurieren. Add-Ins können benutzerdefinierte Listen installieren, diese Listen aber nicht vorab für anonymen Zugriff konfigurieren. Die Konfiguration muss manuell erfolgen, nachdem das Add-In installiert wurde. Wenn Sie ein vom Anbieter gehostetes SharePoint-Add-In für anonyme Benutzer entwickeln, ist dieses Verfahren nicht erforderlic
  
    
    

> [!HINWEIS]
> a>Dieses Verfahren kann nicht in einer SharePoint Online-Websitesammlung ausgeführt werden. Daher können in SharePoint gehostete Add-Ins, die in SharePoint Online installiert sind und von anonymen Benutzern verwendet werden sollen, keine Listen oder Bibliotheken aufrufe 
  
    
    


### So konfigurieren Sie eine Liste oder Bibliothek für anonymen Zugriff


1. Navigieren Sie zu einer Liste oder Bibliothek, auf welche die SharePoint-Add-In zugreift, die Sie testen, und öffnen Sie die **Listeneinstellungen** oder **Bibliothekseinstellungen**. Bei einer SharePoint Online-Website müssen Sie als Mandantenadministrator angemeldet sein.
    
  
2. Wählen Sie **Berechtigungen für diese Liste/Bibliothek** aus.
    
  
3. Wählen Sie auf der Seite, die daraufhin geöffnet wird, auf der Registerkarte **Berechtigungen** die Option **Berechtigungsvererbung beenden** aus, und wählen Sie dann **OK** aus, wenn Sie zur Bestätigung aufgefordert werden.
    
  
4. Wählen Sie auf der Registerkarte **Berechtigung** die Option **Anonymer Zugriff** aus.
    
  
5. Wählen Sie im Formular **Anonymer Zugriff**, das daraufhin geöffnet wird, alle Berechtigungen aus, welche die Benutzer der SharePoint-Add-In benötigen. Sie können Berechtigungen zum Anzeigen, Bearbeiten, Hinzufügen und Löschen von Elementen gewähren. Sie können keinen Vollzugriff gewähren. Somit können anonyme Benutzer das Schema der Liste oder Listeneinstellungen nicht ändern.
    
  

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Entwickeln von Add-Ins für SharePoint](develop-sharepoint-add-ins.md)
    
  

