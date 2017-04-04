---
title: Abrufen von Benutzeridentität und Eigenschaften in SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# Abrufen von Benutzeridentität und Eigenschaften in SharePoint 2013
Abrufen der Benutzeridentität und Benutzerinformationen in SharePoint 2013
Je nachdem, welche Informationen Sie abrufen möchten, können Benutzeridentität und Benutzerinformationen auf verschiedene Weise abgerufen werden. In diesem Artikel werden einige Möglichkeiten gezeigt.
  
    
    


## Voraussetzungen für das Abrufen von Benutzeridentität und -informationen
<a name="Prereq"> </a>


- Vorbereitung der Entwicklungsumgebung, wie in  [Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md) beschrieben.
    
  
- Installieren von Visual Studio.
    
  
- Installieren Sie die neueste Version von  [Office Developer Tools für Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) oder [Office Developer Tools für Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015)
    
  

> [!HINWEIS]
> Anleitungen zum Einrichten einer Entwicklungsumgebung, die Ihren Anforderungen entspricht, finden Sie unter  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md). 
  
    
    


### Wichtige Kernkonzepte für das Abrufen von Benutzeridentität und -eigenschaften

Die folgende Tabelle enthält eine Liste einiger Artikel, die zum Verständnis der für die Erstellung von SharePoint-Add-Ins relevanten Konzepte hilfreich sein können.
  
    
    


|**Artikel**|**Beschreibung**|
|:-----|:-----|
| [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) <br/> |Dieser Artikel enthält Informationen zu Add-In-Berechtigungen in SharePoint 2013, zu Typen von Add-In-Berechtigungen, Berechtigungsanforderungsbereichen und der Verwaltung von Berechtigungen. Zudem werden in diesem Artikel die Unterschiede zwischen Add-In-Berechtigungsrechten und Benutzerrechten besprochen.  <br/> |
| [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |Hier lernen Sie die OAuth-Authentifizierung und den Autorisierungsablauf für in der Cloud gehostete Add-Ins kennen.  <br/> |
| [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |In diesem Artikel erfahren Sie, wie Sie eine grundlegende von einem Anbieter gehostete SharePoint-Add-In mit Office Developer Tools für Visual Studio 2012 erstellen, wie Sie unter Verwendung des SharePoint-CSOM mit Microsoft SharePoint 2013-Sites interagieren und wie Sie OAuth in einer SharePoint-Add-In implementieren.  <br/> |
   

## Abrufen der Identität des aktuellen Websitebenutzers
<a name="WebsiteUserID"> </a>

Die einfachste Möglichkeit, die Identität des aktuellen Benutzers einer Website abzurufen, bietet das **Web**-Objekt. Wenn Ihr Projekt die Datei **TokenHelper.cs** enthält, können Sie mithilfe des folgenden Codeausschnitts die Identität des aktuellen Websitebenutzers abrufen.
  
    
    

```cs

ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);
 
 
            //Load the properties for the Web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();
 
            //Get the site name.
            siteName = web.Title;
 
            //Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

```


- Wenn Sie mit Office 365 arbeiten, erhalten Sie einen Anmeldenamen, der so ähnlich aussieht wie  `i:0#.f|membership|adam@contoso.com`.
    
  
- Wenn Sie Microsoft SharePoint 2013 lokal einsetzen und sich der Benutzer über NTLM als normaler Benutzer angemeldet hat, dann erhalten Sie einen Anmeldenamen wie  `i:0#.w|contoso\\adam`.
    
  
- Wenn Sie SharePoint 2013 lokal verwenden der Benutzer ein Farmkonto verwendet, dann erhalten Sie einen Anmeldenamen wie  `SHAREPOINT\\System`.
    
  

## Abrufen der Benutzeridentität mithilfe der ResolvePrincipal-Methode
<a name="ResolvePrincipal"> </a>

Nachfolgend wird eine andere Möglichkeit zum Abrufen des Anmeldenamens des Benutzers beschrieben. Wenn Sie die E-Mail-Adresse oder den Anzeigenamen des Benutzers kennen, können Sie mit der  [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) -Methode den Anmeldenamen des Benutzers abrufen.
  
    
    

> [!HINWEIS]
> Die APIs befinden sich im Namespace **Microsoft.SharePoint.Client.Utilities** in der Assembly [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/de-de/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx). 
  
    
    

Im folgenden Beispielcode wird gezeigt, wie Sie die Anmeldeinformationen des Benutzers erhalten.
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

Der **Person.LoginName**-Wert enthält die Anmeldeinformationen
  
    
    

## Abrufen der Benutzeridentität und der Profileigenschaften
<a name="Profile"> </a>

Wenn Sie die Identität und die Eigenschaften des Benutzers abrufen möchten, können Sie das OAuth-Token und APIs für soziale Funktionen verwenden. Rufen Sie zuerst das OAuth-Token ab, und legen Sie es auf den Clientkontext fest. Im folgenden Beispielcode wird gezeigt, wie Benutzerprofileigenschaften abgerufen werden.
  
    
    

```cs

ClientContext clientContext = new ClientContext(<sharepointurl>);
clientContext.AuthenticationMode = ClientAuthenticationMode.Anonymous;
clientContext.FormDigestHandlingEnabled = false;
clientContext.ExecutingWebRequest +=
delegate(object oSender, WebRequestEventArgs webRequestEventArgs)
{                      
    webRequestEventArgs.WebRequestExecutor.RequestHeaders["Authorization"] =
        "Bearer " + accessToken;
};

```

Verwenden Sie dann die  [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) -API, um die Eigenschaften des Benutzers abzurufen, der das Add-In verwendet.
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

Voraussetzungen für die fehlerfreie Ausführung des Codes:
  
    
    

- Der gemeinsame Benutzerprofildienst muss in SharePoint 2013 für die Benutzer konfiguriert und synchronisiert werden.
    
  
- Sie müssen dem Add-In-Manifest den folgenden Berechtigungsbereich für soziale Funktionen hinzufügen:
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

Die APIs befinden sich in der Datei **Microsoft.SharePoint.Client.UserProfiles.dll**.
  
    
    
Es folgt ein weiterer Codeausschnitt, der zeigt, wie auf den Benutzerprofilspeicher zugegriffen wird.
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## Zusätzliche Ressourcen
<a name="AdditionalResources"> </a>


-  [Add-In-Berechtigungen in SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint-Add-Ins](sharepoint-add-ins.md)
    
  
-  [Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

