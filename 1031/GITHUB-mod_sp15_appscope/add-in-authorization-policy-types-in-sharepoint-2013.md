---
title: Add-In-Autorisierungsrichtlinientypen in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# Add-In-Autorisierungsrichtlinientypen in SharePoint 2013
Erfahren Sie mehr über die verschiedenen Autorisierungsrichtlinien für Add-Ins in SharePoint: Nur-Add-In-Richtlinie, Benutzer-und-Add-In-Richtlinie und Nur-Benutzer-Richtlinie. Abschließend finden Sie Hinweise zur Verwendung der Nur-Add-In-Richtlinie.
Bevor Sie diesen Artikel lesen, sollten Sie sich mit den Artikeln  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) und [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) vertraut machen.
  
    
    


## Get an overview of add-in authorization policies types
<a name="Overview"> </a>

SharePoint bietet drei Typen von Autorisierungsrichtlinien. Die verwendete Autorisierungsrichtlinie wird durch die authentifizierten Identitäten im Aufruf bestimmt. Bei den authentifizierten Identitäten kann es sich um reine Benutzeridentitäten, Benutzer-und-App-Identitäten oder reine App-Identitäten handeln:
  
    
    

- **Nur-Benutzer-Richtlinie**: Die Nur-Benutzer-Richtlinie entspricht der Autorisierungsrichtlinie, die beispielsweise auf der Startseite einer SharePoint-Website oder beim Zugriff auf SharePoint-APIs über PowerShell beim erstmaligen Öffnen angewendet wurde. Wenn die Nur-Benutzer-Richtlinie verwendet wird, wird bei den Autorisierungsprüfungen nur die Benutzeridentität berücksichtigt. Diese Richtlinie wird beispielsweise verwendet, wenn der Benutzer direkt auf Ressourcen zugreift, ohne die App zu verwenden.
    
    
    
  
- **Benutzer-und-Add-In-Richtlinie**: Wenn die Add-In-Richtlinie verwendet wird, wird bei Autorisierungsprüfungen der Inhaltsdatenbank die Benutzer- und Add-In-Identität berücksichtigt. Insbesondere ist bei Verwendung dieser Richtlinie eine Autorisierungsprüfung nur erfolgreich, wenn der aktuelle Benutzer und das Add-In über ausreichende Berechtigungen für die fragliche Aktion verfügt.
    
    Diese Richtlinie wird beispielsweise verwendet, wenn eine SharePoint-Add-In auf die Ressourcen eines Benutzers in SharePoint zugreifen möchte. (Der Code in den Remotekomponenten der SharePoint-Add-In muss so konzipiert sein, dass Benutzer-und-Add-In-Aufrufe an SharePoint) erfolgen können.
    
    
    
  
- **Nur-Add-In-Richtlinie**: Wenn die Nur-Add-In-Richtlinie verwendet wird, wird bei der Autorisierungsprüfung nur die Add-In-Identität berücksichtigt. Insbesondere sind bei Verwendung dieser Richtlinie Autorisierungsprüfungen nur erfolgreich, wenn das aktuelle Add-In über ausreichende Berechtigungen verfügen, um die fragliche Aktion auszuführen (unabhängig von den Berechtigungen des aktuellen Benutzers).
    
    Ein Ausgabengenehmigungs-Add-In ist ein Beispiel eines Add-Ins, das für die Verwendung dieser Richtlinie konzipiert werden kann. Durch dieses Add-In können Benutzer, die ansonsten keine Ausgaben genehmigen können, Ausgaben unter einem bestimmten Betrag genehmigen. In dem Szenario unten finden Sie dazu weitere Details. 
    
    
    
    > **HINWEIS**
      > Bestimmte APIs benötigen einen Benutzerkontext und können mit einer Nur-Add-In-Richtlinie nicht ausgeführt werden. Hierzu gehören viele APIs für die Interaktion mit Project Server 2013 und für Suchabfragen. 

### Weitere Informationen finden Sie in einem Beispielszenario eines Add-Ins, das die Nur-Add-In-Richtlinie verwendet
<a name="Scenario"> </a>

Angenommen, ein Vertriebsmanager bei Contoso, Adam, kauft eine Kostenvorlage-App, die die Nur-Add-In-Richtlinie verwendet. Beim Kauf des Add-Ins wird Adam aufgefordert, dem Add-In zu erlauben, Benutzerberechtigungen zu erhöhen. Adam erteilt dem Add-In die angeforderte Berechtigung. Anschließend kauft er genügend Lizenzen für alle Contoso-Vertriebsmitarbeiter und installiert das Add-In auf der SharePoint-Website des Vertriebsteams.
  
    
    
Bald reichen die Vertriebsmitarbeiter Kostenberichte mit dem neuen Kostenvorlage-Tool (des von Adam erworbenen Kostenvorlage-Add-Ins) ein. Das Add-In verhindert, dass Vertriebsmitarbeiter ihre eigenen Kostenberichte genehmigen. Das Add-In kann sie jedoch genehmigen, da Adam ihr die Berechtigung zum Erhöhen von Benutzerberechtigungen erteilt hat. Adam richtet das Add-In so ein, dass Kostenvorlagen unter 50 € automatisch genehmigt werden und ihm bei Berichten über 50 € automatisch eine Aufgabe zugewiesen wird, diese zu genehmigen. Dies kann implementiert werden, indem das SharePoint-Add-In eine Schreibberechtigung für eine SharePoint-Liste mit genehmigten Ausgaben erhält. Aber unter den Benutzern haben nur Manager der Personalabteilung Schreibberechtigungen für die Liste. Der Code im Add-In wurde so entwickelt, dass ein Nur-Add-In-Aufruf an SharePoint erfolgt, wenn die Ausgabe unter 45 Euro liegt, damit sie der Liste hinzugefügt wird. Da die Berechtigungen des Benutzers nicht überprüft werden, werden alle Benutzerübertragungen unter 45 Euro automatisch zur Liste genehmigter Ausgaben hinzugefügt, selbst wenn der Benutzer nicht über eine Schreibberechtigung für die Liste verfügt.
  
    
    

  
    
    

### Erfahren Sie mehr darüber, wie Add-Ins die Genehmigung zur Verwendung der Nur-Add-In-Richtlinie erhalten
<a name="Approve"> </a>

Um Nur-Add-In-Aufrufe an SharePoint ausführen zu können, muss Ihr Add-In die Berechtigung zur Verwendung der Nur-Add-In-Richtlinie anfordern. Diese Anforderung erfolgt im Add-In-Manifest.Fügen Sie dazu das **AllowAppOnlyPolicy**-Attribut zum **AppPermissionRequests**-Element hinzu und legen Sie dafür **true** fest, wie im folgenden Markup dargestellt:
  
    
    

```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>
```


> **HINWEIS**
> SharePoint-Add-Ins wurden "Apps für SharePoint" genannt. Damit eine Abwärtskompatibilität gewährleistet werden kann, wurde das App-Manifestschema nicht geändert, sodass die Zeichenfolge "App" in Elementen und Attributnamen angezeigt wird. 
  
    
    

Ein Benutzer wird bei der Installation des Add-Ins dazu aufgefordert, diese Anforderung zu genehmigen. Wenn das Add-In nach den Mandanten-Berechtigungen fragt, kann nur ein Mandanten-Administrator die Verwendung der Nur-Add-In-Richtlinie genehmigen, also kann nur ein Mandanten-Administrator das Add-In installieren. Wenn das Add-In keine höheren Berechtigungen erfordert als die auf der Ebene der Websitesammlung fragt das Add-In nach keinen Berechtigungen dafür, anschließend kann ein Website-Administrator das Add-In installieren. Weitere Informationen zu Berechtigungsbereichen finden Sie unter  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Erfahren Sie, wie Sie mit Add-Ins Nur-Add-In-Aufrufe ausführen
<a name="AppOnlyCalls"> </a>

Der Unterschied zwischen einem Nur-Add-In-Aufruf an SharePoint und einem Benutzer-und-Add-In-Aufruf ist der Typ des Zugriffstokens, das im Aufruf enthalten ist. Im folgenden Code sind die Benutzer-und-Add-In- und Nur-Add-In-Zugriffstokens im verwalteten Code dargestellt. Die detaillierte Programmierung erfolgt in der Datei TokenHelper.cs (oder .vb), das die Office-Entwicklertools für Visual Studio automatisch zum -Projekt in Visual Studio hinzufügen.
  
    
    

```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+add-in access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...
    
      //Get add-in-only access token.
       string addinOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}
```


> **HINWEIS**
> Add-Ins, die keine mit OAuth authentifizierten Aufrufe ausgeben (z. B. Add-Ins, die nur aus im Add-In-Web ausgeführtem JavaScript bestehen) können die Nur-Add-In-Richtlinie nicht verwenden. Sie können die Berechtigung anfordern, können sie jedoch nicht nutzen, da hierfür die Übergabe eines Nur-Add-In-OAuth-Tokens erforderlich wäre. Nur Add-Ins mit außerhalb von SharePoint ausgeführten Webanwendungen können Nur-Add-In-Token erstellen und übergeben. 
  
    
    

Im Allgemeinen muss ein aktueller Benutzer vorhanden sein, damit ein Aufruf möglich ist. Bei der Nur-Add-In-Richtlinie wird ein Benutzer SHAREPOINT\\APP, ähnlich dem vorhandenen Benutzer SHAREPOINT\\SYSTEM erstellt. Alle Nur-Add-In-Anforderungen erfolgen über SHAREPOINT\\APP. Es gibt keine Möglichkeit der Authentifizierung als SHAREPOINT\\APP über benutzerbasierte Authentifizierung.
  
    
    

### Szenariobeispiel für Add-Ins mit der Nur-Add-In-Richtlinie
<a name="GuidelinesFor"> </a>

Da durch Nur-Add-In-Aufrufe die Benutzerrechte erhöht werden, sollten Sie hinsichtlich des Erstellens von Add-Ins, die Berechtigungen erfordern, eher zurückhaltend agieren. Aufrufe sollten die Nur-Add-In-Richtlinie nur in folgenden Fällen verwenden:
  
    
    

- Die App muss ihre Berechtigungen für einen bestimmten Aufruf über die Berechtigungen des Benutzers hinaus erhöhen (z. B., um einen Kostenbericht unter von der App ausgewerteten Bedingungen zu genehmigen).
    
  
- The add-in is not acting on behalf of any user; for example, an add-in that performs nightly maintenance tasks on a SharePoint document library.
    
  

## Weitere Ressourcen
<a name="AR"> </a>


-  [Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint-Add-Ins](sharepoint-add-ins.md)
    
  
-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

