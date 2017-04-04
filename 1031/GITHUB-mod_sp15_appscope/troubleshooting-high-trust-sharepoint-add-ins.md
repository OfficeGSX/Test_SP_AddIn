---
title: Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# Fehlerbehebung bei besonders vertrauenswürdigen SharePoint-Add-Ins
Holen Sie sich Unterstützung bei Problemen mit der Entwicklung von besonders vertrauenswürdigen SharePoint-Add-Ins.
In diesem Artikel wird das Fiddler-Tool beschrieben, außerdem finden Sie Anweisungen für die Behebung einiger spezifischer Probleme.
  
    
    


## Verwenden des Fiddler-Tools

Das kostenlose  [Fiddler-Tool](http://www.telerik.com/fiddler) kann verwendet werden, um die HTTP-Anforderungen zu erfassen, die von der Remotekomponente Ihres Add-Ins an SharePoint gesendet werden. Es gibt eine [kostenlose Erweiterung für das Tool](https://github.com/andrewconnell/SPOAuthFiddlerExt), die automatisch die Zugriffstoken in den Anforderungen dekodiert.
  
    
    
Nachdem Sie Fiddler auf dem Webanwendungsserver installiert haben, fügen Sie das folgende Markup zur Datei web.config hinzu, damit Anforderungen von Ihrer Remoteweb-App über diesen Proxy gehen. Auf diese Weise können Sie eine Fiddler-Ablaufverfolgung erfassen und die vollständige Antwort von SharePoint anzeigen, wenn Sie eine Fehlermeldung erhalten.
  
    
    

> [!HINWEIS]
> Stellen Sie sicher, dass Sie dieses Markup entfernen, wenn Fiddler nicht ausgeführt wird. Wenn Sie das Markup nicht entfernen, kann Ihr Add-In keine HTTP-Anforderungen durchführen. 
  
    
    




```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>

```

Nachdem Fiddler installiert wurde, können Sie außerdem die Antwortheader von SharePoint überprüfen, die eine Anforderungs-GUID enthalten. Diese Anforderungs-GUID ist eine Korrelations-ID, die Sie in den Protokollen suchen können, um dieser Anforderung zugeordnete Protokollfehler zu finden.
  
    
    

## Fehler 401 (Nicht autorisiert)
<a name="UnauthorizedException"> </a>

Ein **401 Unauthorized**-Fehler kann durch verschiedene Dinge verursacht werden, wenn das besonders vertrauenswürdige Add-In erstmals auf SharePoint zugreift. Wenn Sie das clientseitige Objektmodell (CSOM) verwenden, sieht der Fehler in etwa wie folgt aus:
  
    
    

```cs

[WebException: The remote server returned an error: (401) Unauthorized.]
   System.Net.HttpWebRequest.GetResponse() +8515936
   Microsoft.SharePoint.Client.SPWebRequestExecutor.Execute() +178
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQueryToServer(ChunkStringBuilder sb) +1427
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQuery() +270
   Microsoft.SharePoint.Client.ClientRuntimeContext.ExecuteQuery() +146
   Microsoft.SharePoint.Client.ClientContext.ExecuteQuery() +666
   S2STestWeb.Default.Page_Load(Object sender, EventArgs e) in c:\\MyFiles\\HightrustTest\\HightrustTestWeb\\Default.aspx.cs:28
   System.Web.UI.Control.LoadRecursive() +71
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178
```

Wenn die TokenHelper-Datei und Windows-Identität verwenden, sieht der Code, der die Ausnahme auslöst, in etwa wie folgt aus:
  
    
    



```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();
```

Der erste Schritt für die Behebung des Problems besteht darin, mit dem Visual Studio-Debugger zu überprüfen, ob das Zugriffstoken und das **ClientContext** erfolgreich aufgebaut sind. Falls ja, untersuchen Sie die folgenden Möglichkeiten:
  
    
    
 **Mögliche Probleme und Lösungen:**
  
    
    

- Es wurde kein Benutzerprofil für den Benutzer erstellt, der auf die Remotewebanwendung zugreift. Erstellen Sie das Benutzerprofil.
    
  
- Ihr Add-In verfügt nicht über die Berechtigung für die Ressource, auf die Sie zugreifen möchten. Öffnen Sie die SharePoint-Verwaltungsshell, und führen Sie das folgende Windows PowerShell-Cmdlet aus. Die Variable  `$web` ist die SharePoint-Website, auf die Sie zugreifen möchten, und `$appPrincipal`) ist die Add-In-ID. Weitere Informationen finden Sie unter  [Set-SPAppPrincipalPermission](http://technet.microsoft.com/de-de/library/jj219714%28v=office.15%29.aspx).
    
  ```
  
Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl
  ```

- Ihre Webanwendung akzeptiert anonyme Anforderungen. Dies bedeutet, dass das Zugriffstoken keine echte Benutzeridentität enthält. Stellen Sie sicher, dass für das Stammverzeichnis Ihrer Remote-Webanwendung der anonyme Zugriff in IIS deaktiviert ist. Sie können dies auch überprüfen, indem Sie Ihre Remote-Webanwendung debuggen und den Wert von **Request.LogonUserIdentity** in der Datei default.aspx.cs (oder .vb) überprüfen, um sicherzustellen, dass es sich nicht um einen anonymen Benutzer handelt.
    
  
- Ihr digitales Zertifikat wurde dem vertrauenswürdigen Zertifikatspeicher nicht hinzugefügt. Stellen Sie sicher, dass Sie die Verfahren in  [Packen und Veröffentlichen besonders vertrauenswürdiger Add-Ins für SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md) befolgt haben.
    
  

## Verschiedene SSL- und domänenrelevante Autorisierungsfehler
<a name="DomainRelatedErrors"> </a>

Die fehlende Übereinstimmung von Domänennamen in Konfigurationsdateien und Registrierungsformularen kann die Autorisierung verhindern. Die folgenden vier Werte müssen exakt gleich sein:
  
    
    

- Die **Add-In-Domäne**, die angegeben wird, wenn die SharePoint-Add-In auf der Seite AppRegNew.aspx registriert wird
    
  
- Die Domäne, unter der das Sicherheitszertifikat der Remotewebanwendung registriert ist
    
  
- Der Domänenteil des Werts **StartPage** in der Datei AppManifest.xml
    
  
- Der Domänenteil der URL aller Ereignisempfänger, die in der AppManifest.xml angegeben sind
    
  
Beachten Sie in Verbindung mit diesem Punkt Folgendes:
  
    
    

- Wenn die Remotekomponente Ihrer SharePoint-Add-In einen anderen Port als 443 verwendet, müssen Sie den Port ausdrücklich als Teil der Domäne an allen vier Stellen einschließen, beispielsweise  `MarketingServer:3333`. (Sie müssen das HTTPS-Protokoll verwenden, für das der Standardport 443 ist.)
    
  
- Die Domäne muss in den Wert **StartPage** (und allen URLs von Ereignisempfängern) der Datei AppManifest.xml hartcodiert werden, bevor das Add-In verpackt wird. Wenn Sie den **Veröffentlichungs**-Assistenten in Visual Studio verwenden, um das Add-In zu verpacken, werden Sie aufgefordert, die Domäne anzugeben, und die Office-Entwicklertools für Visual Studio fügen diese für Sie in den Wert **StartPage** ein (anstelle des `~remoteWebUrl`-Tokens, das während des Debuggings verwendet wird). Wenn Sie den **Veröffentlichungs**-Assistenten jedoch nicht verwenden, müssen Sie das Token manuell durch die Domäne (und das Protokoll) ersetzen, z. B.  `https://MarketingServer` oder `https://MarketingServer:3333`.
    
  

## Laufzeitfehler, der darauf hinweist, dass kein Zertifikat mit der Seriennummer vorhanden ist
<a name="DomainRelatedErrors"> </a>

Wenn Sie sicher sind, dass Sie die richtige Zertifikatseriennummer in der Datei "web.config" angegeben haben, und Sie das Zertifikat im **Windows-Zertifikatspeicher** sehen, dann enthält die Seriennummer in "web.config" möglicherweise ein ausgeblendetes zusätzliches Zeichen. Dies geschieht, wenn die Seriennummer aus der **Microsoft Management Console** kopiert und eingefügt wird. Löschen Sie den gesamten Seriennummernwert aus der Datei "web.config", und geben Sie ihn *manuell*  erneut ein.
  
    
    

