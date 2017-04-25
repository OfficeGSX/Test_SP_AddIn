---
title: Einrichten einer Entwicklungsumgebung für SharePoint-Add-Ins in Office 365
ms.prod: MULTIPLEPRODUCTS
ms.assetid: b22ce52a-ae9e-4831-9b68-c9210af6dc54
---


# Einrichten einer Entwicklungsumgebung für SharePoint-Add-Ins in Office 365
Einrichten einer Entwicklungsumgebung für SharePoint-Add-Ins auf einer Office 365-Entwicklerwebsite.
Lesen Sie  [Tools und Umgebungen für die Entwicklung von Add-Ins für SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md), um Ihre Optionen zu verstehen, bevor Sie die Verfahren in diesem Artikel ausführen. Sie finden weitere Informationen unter  [SharePoint-Add-Ins](sharepoint-add-ins.md), wenn Sie nicht sicher sind, welche Arten von SharePoint-Add-Ins Sie erstellen möchten.
  
    
    


## Installieren von Visual Studio und Tools auf Ihrem Computer
<a name="devenv_vs"> </a>


- Wenn Sie **Visual Studio** 2013 oder höher noch nicht installiert haben, installieren Sie es mit den Anweisungen unter [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx). Wir empfehlen die Verwendung der  [neuesten Version aus dem Microsoft Download Center](https://www.visualstudio.com/downloads/download-visual-studio-vs).
    
  
- Visual Studio umfasst **Microsoft Office-Entwicklertools für Visual Studio**, es gibt jedoch auch Fälle, in denen eine Version der Tools zwischen den Updates von Visual Studio veröffentlicht wird. Um sicherzustellen, dass Sie die aktuellste Version der Tools verwenden, führen Sie das [Installationsprogramm für Office Developer Tools für Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) oder das [Installationsprogramm für Office Developer Tools für Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015) aus.
    
  

### Ausführliche Protokollierung in Visual Studio

Führen Sie die folgenden Schritte aus, wenn Sie die ausführliche Protokollierung aktivieren möchten:
  
    
    

1. Öffnen Sie die Registrierung, und navigieren Sie zu **HKEY_CURRENT_USER\\Software\\Microsoft\\VisualStudio\\ _nn.n_\\SharePointTools**, wobei _nn.n_ die Version von Visual Studio ist, z. B. 12.0 oder 14.0.
    
  
2. Fügen Sie einen DWORD-Schlüssel mit dem Namen **EnableDiagnostics** hinzu.
    
  
3. Geben Sie dem Schlüssel den Wert **1**.
    
  
Der Registrierungspfad wird sich in kommenden Versionen von Visual Studio ändern.
  
    
    

## Registrieren für eine Website für Office 365-Entwickler
<a name="o365_signup"> </a>


> **HINWEIS**
>  Möglicherweise haben Sie bereits Zugriff auf eine Website für Office 365-Entwickler.> **Sind Sie MSDN-Abonnent?** Visual Studio Enterprise mit MSDN-Abonnenten erhalten als Bonus ein einjähriges Office 365 Developer-Abonnement. [Lösen Sie Ihren Bonus heute ein.](https://login.live.com/login.srf?wa=wsignin1.0&amp;rpsnv=12&amp;ct=1402926170&amp;rver=6.0.5276.0&amp;wp=MCMBI&amp;wlcxt=msdn%24msdn%24msdn&amp;wreply=https%3a%2f%2fmsdn.microsoft.com%2fsubscriptions%2fmanage%2fdefault.aspx&amp;lc=1033&amp;id=254354&amp;mkt=de-DE)> **Besitzen Sie einen der folgenden Office 365 Abonnementpläne?**> **Falls ja, kann ein Administrator des Office 365-Abonnements eine Website für Entwickler** über das [Office 365 Admin Center](https://portal.microsoftonline.com/admin/default.aspx) erstellen. Weitere Informationen finden Sie unter [Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement](create-a-developer-site-on-an-existing-office-365-subscription.md). 
  
    
    

Es gibt drei Wege zu einem Office 365-Plan. 
  
    
    

- Registrieren Sie sich über das Office 365-Entwicklerprogramm für ein kostenloses einjähriges Office 365-Entwicklerkonto.  [Informieren Sie sich ausführlicher](http://dev.office.com/devprogram), oder wechseln Sie direkt zum  [Registrierungsformular](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170). Nach der Registrierung für das Entwicklerprogramm erhalten Sie eine E-Mail mit einem Link, über den Sie sich für das Entwicklerkonto registrieren können. Beachten Sie die nachstehenden Anweisungen.
    
  
- Beginnen Sie mit einer  [kostenlosen 30-Tage-Testversion](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK) mit einer Benutzerlizenz.
    
  
- Erwerben Sie ein  [Office 365 Developer-Abonnement](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK). 
    
  

> **TIPP**
> Öffnen Sie diese Links in einem anderen Fenster oder einer anderen Registerkarte, damit die nachfolgenden Anweisungen übersichtlich bleiben. 
  
    
    


**Abb. 1. Domänenname der Office 365-Entwicklerwebsite**

  
    
    

  
    
    
![Seite 2 des Registrierungsformulars für das Office 365-Konto](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. Die erste Seite (nicht abgebildet) des Registrierungsformulars ist selbsterklärend. Geben Sie einfach die erforderlichen Informationen zu Ihrer Person an, und wählen Sie **Weiter** aus.
    
  
2. Geben Sie auf der zweiten Seite, die in Abbildung 1 dargestellt ist, eine Benutzer-ID für den Administrator des Abonnements an.
    
  
3. Erstellen Sie eine Unterdomäne von **. onmicrosoft.com**. 
    
    Nach der Registrierung müssen Sie die resultierenden Anmeldeinformationen (im Format  _UserID_@ _IhreDomäne_.onmicrosoft.com) benutzen, um sich auf Ihrer Office 365-Portalwebsite anzumelden, auf der Sie Ihr Konto verwalten. Ihre SharePoint Online-Entwicklerwebsite wird unter Ihrer neuen Domäne **http:// _IhreDomäne_.sharepoint.com** bereitgestellt.
    
  
4. Wählen Sie **Weiter** aus, und füllen Sie die letzte Seite des Formulars aus. Wenn Sie eine Telefonnummer bereitstellen, um Ihren Bestätigungscode zu erhalten, können Sie eine Mobil- oder Festnetznummer, aber *keine*  VoIP-Nummer (Voice over Internet Protocol) bereitstellen.
    
  

    
> **HINWEIS**
> Wenn Sie beim Versuch, sich bei einem Entwicklerkonto anzumelden, bei einem anderen Microsoft-Konto angemeldet sind, wird möglicherweise die folgende Meldung angezeigt: „Die eingegebene Benutzer-ID hat leider nicht funktioniert. Sie ist anscheinend nicht gültig. Geben Sie die Benutzer-ID ein, die Ihnen von Ihrem Unternehmen zugewiesen wurde. Ihre Benutzer-ID hat in der Regel das Format  *jemand@beispiel.com*  oder *jemand@beispiel.onmicrosoft.com*  ."> Wenn diese Meldung angezeigt wird, melden Sie sich vom Microsoft-Konto ab, und versuchen Sie es erneut. Sollte die Meldung weiterhin angezeigt werden, löschen Sie den Browsercache, oder wechseln Sie zu **InPrivate-Browsen** und füllen Sie das Formular aus.
  
    
    

Nachdem Sie die Registrierung abgeschlossen haben, wird in Ihrem Browser die Office 365-Installationsseite geöffnet. Wählen Sie das Admin-Symbol aus, um die Admin Center-Seite zu öffnen.
  
    
    

**Abb. 2. Office 365 Admin Center-Seite**

  
    
    

  
    
    
![Screenshot mit dem Office 365 Admin Center](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. Warten Sie, bis der Bereitstellungsprozess für Ihre Website für Entwickler abgeschlossen ist. Nach Abschluss der Bereitstellung aktualisieren Sie die Admin Center-Seite im Browser.
    
  
2. Wählen Sie dann links oben den Link **Add-Ins erstellen** aus, um Ihre Website für Entwickler zu öffnen. Sie sollten eine Website wie in Abbildung 3 dargestellt sehen. Auf der Seite wird die Liste **Add-Ins im Test** angezeigt. Damit wird bestätigt, dass die Website mit der Entwicklerwebsitevorlage von SharePoint erstellt wurde. Wird stattdessen eine Teamwebsite angezeigt, warten Sie einige Minuten und starten dann Ihre Website erneut.
    
  
3. Notieren Sie die URL der Website. Diese wird verwendet, wenn Sie SharePoint-Add-Ins-Projekte in Visual Studio erstellen.
    
  

**Abb. 3: Die Startseite Ihrer Entwicklerwebsite mit der Liste der Add-Ins im Test**

  
    
    

  
    
    
![Screenshot, auf dem die Entwicklerwebsite-Startseite angezeigt ist](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## Weitere Ressourcen
<a name="SP15SetupSPO365_bk_addlresources"> </a>


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)
    
  
-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

  
    
    

