---
title: Erstellen von vom Anbieter gehosteten SharePoint-Add-Ins für den Zugriff auf SAP-Daten über das SAP-Gateway für Microsoft
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# Erstellen von vom Anbieter gehosteten SharePoint-Add-Ins für den Zugriff auf SAP-Daten über das SAP-Gateway für Microsoft
Erfahren Sie, wie Sie ein SharePoint-Add-In erstellen, die auf SAP-Daten zugreifen kann.
Sie können ein SharePoint-Add-In erstellen, die SAP-Daten und optional SharePoint-Daten liest und schreibt, indem Sie SAP-Gateway für Microsoft und die Azure AD-Authentifizierungsbibliothek für .NET verwenden. In diesem Artikel finden Sie die Verfahrensweisen für das Entwerfen des SharePoint-Add-In für einen autorisierten Zugriff auf SAP. 
  
    
    


## Bevor Sie beginnen:

Im Folgenden sind die Voraussetzungen für die Verfahren in diesem Artikel aufgeführt:
  
    
    

- **Eine Website für Office 365-Entwickler** in einer Office 365-Domain, die mit einem Microsoft Azure Active Directory-Abonnement verknüpft ist. Weitere Informationen finden Sie unter [Einrichten einer Entwicklungsumgebung für SharePoint-Add-Ins in Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) oder [Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement](create-a-developer-site-on-an-existing-office-365-subscription.md).
    
  
- **Visual Studio 2013 Update 2** oder höher, das Sie über [Willkommen bei Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx) erwerben können.
    
  
- **Microsoft Office-Entwicklertools für Visual Studio**. Die Version, die in Update 2 von Visual Studio 2013 oder höher enthalten ist.
    
  
- **SAP-Gateway für Microsoft** wird in Microsoft Azure bereitgestellt und konfiguriert. Weitere Informationen finden Sie in der Dokumentation zu [SAP-Gateway für Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Ein Organisationskonto in Microsoft Azure**. Weitere Informationen finden Sie unter  [Ihre Office 365-APIs Preview app manuell gemeinsamen Einverständnisses hinzufügen](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount).
    
    > **HINWEIS**
      > Melden Sie sich bei Ihrem Office 365-Konto (login.microsoftonline.com) an, um das temporäre Kennwort zu ändern, nachdem das Konto erstellt ist. 
- **Ein SAP OData-Endpunkt** mit Beispieldaten. Weitere Informationen finden Sie in der Dokumentation zu [SAP-Gateway für Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Grundkenntnisse zu Azure AD.** Weitere Informationen finden Sie unter [Erste Schritt mit Azure AD](http://msdn.microsoft.com/library/azure/dn655157.aspx).
    
  
- **Grundkenntnisse in der Erstellung von SharePoint-Add-Ins.** Weitere Informationen finden Sie unter [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md).
    
  
- **Grundkenntnisse zu OAuth 2.0 in Azure AD**. Weitere Informationen finden Sie unter  [OAuth 2.0 in Azure AD](http://msdn.microsoft.com/library/azure/dn645545.aspx) und den untergeordneten Themen.
    
  
 **Codebeispiel:** [SharePoint 2013: Verwenden des SAP-Gateways zu Microsoft in einer App für SharePoint](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)
  
    
    

## Verstehen der Authentifizierung und Autorisierung beim SAP-Gateway für Microsoft und SharePoint
<a name="AuthOverview"> </a>

Mit OAuth 2.0 in Azure AD können Anwendungen auf mehrere Ressourcen zugreifen, die von Microsoft Azure gehostet werden, und SAP-Gateway für Microsoft ist eine davon. Mit OAuth 2.0 sind Anwendungen, zusätzlich zu Benutzern, Sicherheitsprinzipale. Für Anwendungsprinzipale ist eine Authentifizierung und Autorisierung bei geschützten Ressourcen zusätzlich zu (und manchmal anstelle von) Benutzern erforderlich. Der Prozess beinhaltet einen OAuth-"Ablauf", bei dem die Anwendung, die ein SharePoint-Add-In sein kann, ein Zugriffstoken (und Aktualisierungstoken) abruft, das von allen von Microsoft Azure gehosteten Diensten und Anwendungen akzeptiert wird, die für die Verwendung von Azure AD als OAuth 2.0-Autorisierungsserver konfiguriert sind. Der Prozess ähnelt der Art und Weise, wie die Remotekomponenten eines von einem Anbieter gehostetes SharePoint-Add-In eine Autorisierung für SharePoint erhalten, wie in  [Erstellen von SharePoint-Add-Ins, die die Autorisierung mit niedriger Vertrauensebene verwenden](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) und den untergeordneten Artikeln beschrieben. Das ACS-Autorisierungssystem verwendet jedoch Microsoft Azure Access Control Service (ACS) anstelle von Azure AD als vertrauenswürdigen Tokenherausgeber.
  
    
    

> **TIPP**
> Wenn Ihr SharePoint-Add-In auf SharePoint zusätzlich zu SAP-Gateway für Microsoft zugreift, muss sie  *beide*  Systeme verwenden: Azure AD, um ein Zugriffstoken für SAP-Gateway für Microsoft zu erhalten, und das ACS-Autorisierungssystem, um ein Zugriffstoken zu SharePoint zu erhalten. Die Token aus den zwei Quellen sind nicht austauschbar. Weitere Informationen finden Sie unter [Optionales Hinzufügen eines Zugriffs auf SharePoint zur ASP.NET-Anwendung](#SharePoint). 
  
    
    

Eine ausführliche Beschreibung und ein Diagramm zu dem in OAuth 2.0 verwendeten OAuth-Ablauf in Azure AD finden Sie unter  [Ablauf für die Gewährung eines Autorisierungscodes](http://msdn.microsoft.com/library/azure/dn645542.aspx). (Eine ähnliche Beschreibung und ein Diagramm für den Ablauf beim Zugriff auf SharePoint finden Sie unter  [Die einzelnen Schritte des Kontexttokenablaufs](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps).)
  
    
    

## Erstellen des SharePoint-Add-In
<a name="AuthOverview"> </a>


### Erstellen der Visual Studio-Lösung


1. Erstellen Sie ein **SharePoint-Add-In**-Projekt in Visual Studio mit den folgenden Schritten. (Für das fortlaufende Beispiel in diesem Artikel wird davon ausgegangen, die Sie C# verwenden. Sie können aber ein SharePoint-Add-InProjekt auch im Abschnitt **Visual Basic** der Vorlagen für neue Projekte starten.)
    
1. Geben Sie im Assistenten **Neue App für SharePoint** einen Namen für das Projekt ein, und klicken Sie auf **OK**. Verwenden Sie für das fortlaufende Beispiel den Namen SAP2SharePoint.
    
  
2. Geben Sie die Domänen-URL Ihrer Website für Office 365-Entwickler (einschließlich eines abschließenden Schrägstrichs) als Debugging-Website an, z. B. https://<O365_domain>.sharepoint.com/. Geben Sie **Von Anbieter gehostet** als App-Typ an. Klicken Sie auf **Weiter**.
    
  
3. Wählen Sie einen Projekttyp aus, für das fortlaufende Beispiel **ASP.NET-Web Forms-Anwendung**. (Sie können auch ASP.NET-MVC-Anwendungen erstellen, die auf SAP-Gateway für Microsoft zugreifen.) Klicken Sie auf **Weiter**.
    
  
4. Wählen Sie Azure ACS als Authentifizierungssystem aus. (Ihr SharePoint-Add-In verwendet dieses System, wenn es auf SharePoint zugreift. Sie verwendet das System nicht, wenn sie auf SAP-Gateway für Microsoft zugreift.) Klicken Sie auf **Fertig stellen**.
    
  
2. Nachdem das Projekt erstellt ist, werden Sie aufgefordert, sich beim Office 365-Konto anzumelden. Verwenden Sie die Anmeldeinformationen eines Kontoadministrators, z. B. Bob@<O365_domain>.onmicrosoft.com.
    
  
3. In der Visual Studio-Lösung gibt es zwei Projekte: das eigentliche SharePoint-Add-In-Projekt und ein ASP.NET-Web Forms-Projekt. Fügen Sie mit den folgenden Schritten das Paket der **Active Directory-Authentifizierungsbibliothek** (ADAL) zum ASP.NET-Projekt hinzu:
    
1. Klicken Sie mit der rechten Maustaste auf den Ordner **Referenzen** im ASP.NET-Projekt (mit dem Namen **SAP2SharePointWeb** im fortlaufenden Beispiel), und wählen Sie **NuGet-Pakete verwalten** aus.
    
  
2. Wählen Sie im daraufhin geöffneten Dialogfeld auf der linken Seite **Online** aus. Geben SieMicrosoft.IdentityModel.Clients.ActiveDirectory in das Suchfeld ein.
    
  
3. Wenn die ADAL-Bibliothek in den Suchergebnissen angezeigt wird, klicken Sie auf die Schaltfläche **Installieren** daneben, und akzeptieren Sie die Lizenz, wenn Sie dazu aufgefordert werden.
    
  
4. Fügen Sie das Paket Json.net mit den folgenden Schritten zum ASP.NET-Projekt hinzu:
    
1. Geben Sie Json.net in das Suchfeld ein. Wenn damit zu viele Treffer erzeugt werden, versuchen Sie, nachNewtonsoft.json zu suchen.
    
  
2. Wenn Json.net in den Suchergebnissen angezeigt wird, klicken Sie auf die Schaltfläche **Installieren** daneben.
    
  
5. Klicken Sie auf **Schließen**.
    
  

### Registrieren Ihrer Webanwendung bei Azure AD


1. Melden Sie sich im  [Azure-Verwaltungsportal](https://manage.windowsazure.com) mit Ihrem Azure-Administratorkonto an.
    
    > **HINWEIS**
      > Aus Sicherheitsgründen wird empfohlen, bei der Entwicklung von Apps kein Administratorkonto zu verwenden. 
2. Wählen Sie auf der linken Seite **Active Directory** aus.
    
  
3. Klicken Sie auf Ihr Verzeichnis. 
    
  
4. Wählen Sie **ANWENDUNGEN** (in der obersten Navigationsleiste) aus.
    
  
5. Wählen Sie in der Symbolleiste im unteren Bereich des Bildschirms **Hinzufügen**aus.
    
  
6. Wählen Sie im daraufhin geöffneten Dialogfeld **Eine Anwendung hinzufügen, die meine Organisation entwickelt**.
    
  
7. Geben Sie der Anwendung im Dialogfeld **ANWENDUNG HINZUFÜGEN** einen Namen. Verwenden Sie für das fortlaufende Beispiel den NamenContosoAutomobileCollection. 
    
  
8. Wählen Sie **Webanwendung und/oder Web-API** als Anwendungstyp aus, und klicken Sie dann auf den nach rechts zeigenden Pfeil.
    
  
9. Verwenden Sie auf der zweiten Seite des Dialogfelds die SSL-Debugging-URL aus dem ASP.NET-Projekt in der Visual Studio-Lösung als **ANMELDE-URL**. Sie finden die URL mit den folgenden Schritten. ** *(Sie müssen die App anfangs bei der Debugging-URL registrieren, damit Sie den Visual Studio-Debugger (F5) ausführen können. Wenn Ihre App für das Staging bereit ist, registrieren Sie sie erneut mit ihrer Azure-Website-Staging-URL.  [Ändern der App und Staging an Azure und Office 365](#Stage).)* **
    
1. Markieren Sie das ASP.NET-Projekt im **Projektmappen-Explorer**. 
    
  
2. Kopieren Sie im Fenster **Eigenschaften** den Wert der Eigenschaft **SSL-URL**. Ein Beispiel ist https://localhost:44300/.
    
  
3. Fügen Sie ihn bei der **ANMELDE-URL** im Dialogfeld **ANWENDUNG HINZUFÜGEN** ein.
    
  
10. Geben Sie der App unter **APP-ID-URI** einen eindeutigen URI wie den an das Ende der SSL-URL angehängten Anwendungsnamen, z. B.https://localhost:44300/ContosoAutomobileCollection.
    
  
11. Klicken Sie auf die Schaltfläche mit dem Häkchen. Das Azure-Dashboard für die Anwendung wird mit einer Erfolgsmeldung geöffnet.
    
  
12. Wählen Sie im oberen Bereich der Seite **KONFIGURIEREN** aus.
    
  
13. Blättern Sie zu **CLIENT-ID**, und erstellen Sie eine Kopie der ID. Diese benötigen Sie für ein späteres Verfahren.
    
  
14. Erstellen Sie im Abschnitt **Schlüssel** einen Schlüssel. Dieser wird anfangs nicht angezeigt. Klicken Sie im unteren Bereich der Seite auf **SPEICHERN**, damit der Schlüssel sichtbar wird. Erstellen Sie eine Kopie des Schlüssels. Diese benötigen Sie für ein späteres Verfahren.
    
  
15. Blättern Sie zu **Berechtigungen für andere Anwendungen**, und wählen Sie Ihre SAP-Gateway für Microsoft-Dienstanwendung aus. 
    
  
16. Öffnen Sie die Dropdownliste **Delegierte Berechtigungen**, und aktivieren Sie die Kontrollkästchen für die Berechtigungen für den SAP-Gateway für Microsoft-Dienst, die Ihr SharePoint-Add-In benötigt.
    
  
17. Klicken Sie unten auf dem Bildschirm auf **SPEICHERN**.
    
  

### Konfigurieren der Anwendung für die Kommunikation mit Azure AD


1. Öffnen Sie in Visual Studio die Datei web.config im ASP.NET-Projekt.
    
  
2. Im Abschnitt  `<appSettings>` verfügen die Office-Entwicklertools für Visual Studio über hinzugefügte Elemente für **ClientID** und **ClientSecret** des SharePoint-Add-In. (Diese werden im Azure ACS-Autorisierungssystem verwendet, wenn die ASP.NET-Anwendung auf SharePoint zugreift. Sie können diese für das fortlaufende Beispiel ignorieren, sollten sie aber nicht löschen. Sie sind in vom Anbieter gehosteten SharePoint-Add-Ins erforderlich, selbst wenn die App nicht auf SharePoint-Daten zugreift. Ihre Werte ändern sich jedes Mal, wenn Sie F5 in Visual Studio drücken.) Fügen Sie die folgenden zwei Elemente zum Abschnitt hinzu. Diese werden von der Anwendung für die Authentifizierung bei Azure AD verwendet. (Denken Sie daran, dass Anwendungen und Benutzer in OAuth-basierten Authentifizierungs- und Autorisierungssystemen Sicherheitsprinzipale sind.)
    
  ```
  
<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
  ```

3. Geben Sie die Client-ID, die Sie in einem früheren Schritt aus Ihrem Azure AD-Verzeichnis gespeichert haben, als den Wert des **ida:ClientID**-Schlüssels ein. Behalten Sie Groß-/Kleinschreibung und Satzzeichen exakt so bei, wie Sie sie kopiert haben, und achten Sie darauf, am Anfang oder Ende des Werts kein Leerzeichen einzufügen. Verwenden Sie als **ida:ClientKey**-Schlüssel den  *Schlüssel*  , den Sie aus dem Verzeichnis gespeichert haben. Auch hier sollten Sie darauf achten, keine Leerzeichen einzufügen oder den Wert auf irgendeine Weise zu ändern. Der Abschnitt `<appSettings>` sollte jetzt ungefähr wie das folgende Beispiel aussehen. (Der Wert **ClientId** enthält möglicherweise eine GUID oder eine leere Zeichenfolge.)
    
  ```
  
<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
  ```


    > **HINWEIS**
      > Azure AD kennt Ihre Anwendung über die "localhost"-URL, mit der Sie sie registriert haben. Die Client-ID und der Clientschlüssel sind mit dieser Identität verknüpft. Wenn Sie bereit sind, Ihre Anwendung an eine Azure-Website bereitzustellen, werden Sie sie mit einer neuen URL erneut registrieren. 
4. Fügen Sie weiterhin im Bereich **appSettings** einen **Authority**-Schlüssel hinzu, und legen Sie seinen Wert auf die Office 365-Domäne ( *some_domain*  .onmicrosoft.com) Ihres Organisationskontos fest. Im fortlaufenden Beispiel ist das Organisationskonto Bob@<O365_domain>.onmicrosoft.com, deshalb ist die Autorität `<O365_domain>.onmicrosoft.com`. 
    
  ```
  
<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  ```

5. Fügen Sie immer noch im Abschnitt **appSettings** einen **AppRedirectUrl**-Schlüssel hinzu, und legen Sie seinen Wert auf die Seite fest, auf die der Browser des Benutzers umgeleitet werden soll, nachdem die ASP.NET-App einen Autorisierungscode von Azure AD erhalten hat. Normalerweise ist dies dieselbe Seite, auf der sich der Benutzer befand, als der Aufruf an Azure AD stattfand. Im fortlaufenden Beispiel verwenden Sie den SSL-URL-Wert, dem Sie "/Pages/Default.aspx" anhängen, wie unten gezeigt. (Dies ist ein weiterer Wert, den Sie für das Staging verändern werden.)
    
  ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  ```

6. Fügen Sie weiterhin im Abschnitt **appSettings** einen **ResourceUrl**-Schlüssel hinzu, und legen Sie seinen Wert auf den APP-ID-URI von SAP-Gateway für Microsoft fest ( *nicht*  den APP-ID-URI Ihrer ASP.NET-Anwendung). Sie erhalten Sie diesen Wert vom SAP-Gateway für Microsoft-Administrator. Im Folgenden sehen Sie ein Beispiel.
    
  ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
  ```


    Der Abschnitt  `<appSettings>` sollte jetzt ungefähr wie folgt aussehen:
    


  ```
  <appSettings>
  <add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
  <add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
</appSettings>
  ```

7. Speichern und schließen Sie die web.config-Datei.
    
    > **TIPP**
      > Lassen Sie die web.config-Datei nicht geöffnet, während Sie den Visual Studio-Debugger (F5) ausführen. Die Office-Entwicklertools für Visual Studio ändern den **ClientId**-Wert (nicht die **ida:ClientID**) jedes Mal, wenn Sie F5 drücken. Damit müssen Sie auf eine Aufforderung zum erneuten Laden der web.config-Datei reagieren, wenn sie geöffnet ist, bevor das Debugging ausgeführt werden kann. 

### Hinzufügen einer Hilfsklasse für die Authentifizierung bei Azure AD


1. Klicken Sie mit der rechten Maustaste auf das ASP.NET-Projekt, und verwenden Sie den Visual Studio-Prozess zum Hinzufügen von Elementen, um eine neue Klassendatei zum Projekt mit dem Namen AADAuthHelper.cs hinzuzufügen.
    
  
2. Fügen Sie der Datei die folgenden **using**-Anweisungen hinzu.
    
  ```
  
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

  ```

3. Ändern Sie das Zugriffsschlüsselwort von **public** zu **internal**, und fügen Sie das **static**-Schlüsselwort zur Klassendeklaration hinzu.
    
  ```
  
internal static class AADAuthHelper
  ```

4. Fügen Sie der Klasse die folgenden Fehler hinzu. In diesen Feldern werden Informationen gespeichert, die Ihre ASP.NET-Anwendung verwendet, um Zugriffstoken von AAD zu erhalten.
    
  ```
  private static readonly string _authority = ConfigurationManager.AppSettings["Authority"];
private static readonly string _appRedirectUrl = ConfigurationManager.AppSettings["AppRedirectUrl"];
private static readonly string _resourceUrl = ConfigurationManager.AppSettings["ResourceUrl"];     
private static readonly string _clientId = ConfigurationManager.AppSettings["ida:ClientID"];        
private static readonly ClientCredential _clientCredential = new ClientCredential(
                           ConfigurationManager.AppSettings["ida:ClientID"],
                           ConfigurationManager.AppSettings["ida:ClientKey"]);

private static readonly AuthenticationContext _authenticationContext = 
            new AuthenticationContext("https://login.windows.net/common/" + 
                                      ConfigurationManager.AppSettings["Authority"]);
  ```

5. Fügen Sie der Klasse die folgende Eigenschaft hinzu. Diese Eigenschaft hält die URL zum Azure AD-Anmeldebildschirm.
    
  ```
  
private static string AuthorizeUrl
{
    get
    {
        return string.Format("https://login.windows.net/{0}/oauth2/authorize?response_type=code&amp;redirect_uri={1}&amp;client_id={2}&amp;state={3}",
            _authority,
            _appRedirectUrl,
            _clientId,
            Guid.NewGuid().ToString());
    }
}

  ```

6. Fügen Sie der Klasse die folgenden Eigenschaften hinzu. Diese speichern das Zugriffs- und Aktualisierungstoken zwischen und überprüfen ihre Gültigkeit.
    
  ```
  
public static Tuple<string, DateTimeOffset> AccessToken
{
    get {
return HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] 
       as Tuple<string, DateTimeOffset>;
    }

    set { HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] = value; }
}

private static bool IsAccessTokenValid
{
   get 
   { 
       return AccessToken != null &amp;&amp;
       !string.IsNullOrEmpty(AccessToken.Item1) &amp;&amp;
       AccessToken.Item2 > DateTimeOffset.UtcNow;
   }
}

private static string RefreshToken
{
    get { return HttpContext.Current.Session["RefreshToken" + _resourceUrl] as string; }
    set { HttpContext.Current.Session["RefreshToken-" + _resourceUrl] = value; }
}

private static bool IsRefreshTokenValid
{
    get { return !string.IsNullOrEmpty(RefreshToken); }
}

  ```

7. Fügen Sie der Klasse die folgenden Methoden hinzu. Diese werden verwendet, um die Gültigkeit des Autorisierungscodes zu überprüfen und ein Zugriffstoken von Azure AD zu erhalten, indem entweder ein Authentifizierungscode oder ein Aktualisierungstoken verwendet wird.
    
  ```
  
private static bool IsAuthorizationCodeNotNull(string authCode)
{
    return !string.IsNullOrEmpty(authCode);
}

private static Tuple<Tuple<string,DateTimeOffset>,string> AcquireTokensUsingAuthCode(string authCode)
{
    var authResult = _authenticationContext.AcquireTokenByAuthorizationCode(
                authCode,
                new Uri(_appRedirectUrl),
                _clientCredential,
                _resourceUrl);

    return new Tuple<Tuple<string, DateTimeOffset>, string>(
                new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn), 
                authResult.RefreshToken);
}

private static Tuple<string, DateTimeOffset> RenewAccessTokenUsingRefreshToken()
{
    var authResult = _authenticationContext.AcquireTokenByRefreshToken(
                         RefreshToken,
                         _clientCredential.OwnerId,
                         _clientCredential,
                         _resourceUrl);

    return new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);
}

  ```

8. Fügen Sie der Klasse die folgende Methode hinzu. Sie wird vom ASP.NET-Code dahinter aufgerufen, um ein gültiges Zugriffstoken zu erhalten, bevor ein Aufruf zum Abrufen von SAP-Daten über SAP-Gateway für Microsoft erfolgt.
    
  ```
  
internal static void EnsureValidAccessToken(Page page)
{
    if (IsAccessTokenValid) 
    {
        return;
    }
    else if (IsRefreshTokenValid) 
    {
        AccessToken = RenewAccessTokenUsingRefreshToken();
        return;
    }
    else if (IsAuthorizationCodeNotNull(page.Request.QueryString["code"]))
    {
        Tuple<Tuple<string, DateTimeOffset>, string> tokens = null;
        try
        {
            tokens = AcquireTokensUsingAuthCode(page.Request.QueryString["code"]);
        }
        catch 
        {
            page.Response.Redirect(AuthorizeUrl);
        }
        AccessToken = tokens.Item1;
        RefreshToken = tokens.Item2;
        return;
    }
    else
    {
        page.Response.Redirect(AuthorizeUrl);
    }
}
  ```


> **TIPP**
> Die AADAuthHelper-Klasse bietet nur eine minimale Fehlerbehandlung. Für einerobustes SharePoint-Add-In in Produktionsqualität fügen Sie mehr Fehlerbehandlung hinzu, wie im folgenden MSDN-Knoten beschrieben:  [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx). 
  
    
    


### Erstellen von Datenmodellklassen


1. Erstellen Sie eine oder mehrere Klassen, um die Daten zu modellieren, die Ihre App von SAP abruft. Im fortlaufenden Beispiel gibt es nur eine Datenmodellklasse. Klicken Sie mit der rechten Maustaste auf das ASP.NET-Projekt, und verwenden Sie den Visual Studio-Prozess zum Hinzufügen von Elementen, um eine neue Klassendatei zum Projekt mit dem Namen Automobile.cs hinzuzufügen.
    
  
2. Fügen Sie den folgenden Code zum Textteil der Klasse hinzu:
    
  ```
  
public string Price;
public string Brand;
public string Model;
public int Year;
public string Engine;
public int MaxPower;
public string BodyStyle;
public string Transmission;
  ```


### Hinzufügen von dahinter liegendem Code zum Abrufen von Daten von SAP über das SAP-Gateway für Microsoft


1. Öffnen Sie die Datei Default.aspx.cs, und fügen Sie die folgenden **using**-Anweisungen hinzu.
    
  ```
  
using System.Net;
using Newtonsoft.Json.Linq;
  ```

2. Fügen Sie eine **const**-Deklaration zur Standardklasse hinzu, deren Wert die Basis-URL des SAP OData-Endpunkts ist, auf den die App zugreift. Im Folgenden ist ein Beispiel dargestellt:
    
  ```
  
private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
  ```

3. Die Office-Entwicklertools für Visual Studio haben eine **Page_PreInit**-Methode und eine **Page_Load**-Methode hinzugefügt. Kommentieren Sie den Code in der **Page_Load**-Methode und die gesamte **Page_Init**-Methode aus. Dieser Code wird im Beispiel nicht verwendet. (Wenn Ihr SharePoint-Add-In auf SharePoint zugreifen wird, stellen Sie diesen Code wieder her. Informationen finden Sie unter  [Optionales Hinzufügen eines Zugriffs auf SharePoint zur ASP.NET-Anwendung](#SharePoint).)
    
  
4. Fügen Sie die folgende Zeile im oberen Bereich der **Page_Load**-Methode hinzu. Damit wird der Debugging-Prozess vereinfacht, da Ihre ASP.NET-Anwendung über SSL (HTTPS) mit SAP-Gateway für Microsoft kommuniziert. Ihr "localhost:port"-Server ist aber nicht so konfiguriert, dass er dem Zertifikat des SAP-Gateway für Microsoft vertraut. Mit dieser Codezeile würden Sie eine Warnung zu einem ungültigen Zertifikat erhalten, bevor Default.aspx geöffnet wird. In einigen Browsern können Sie diesen Fehler einfach wegklicken, aber in anderen können Sie Default.aspx gar nicht öffnen.
    
  ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
  ```


    > **WICHTIG**
      > Löschen Sie diese Zeile, wenn Sie bereit sind, die ASP.NET-Anwendung für das Staging bereitzustellen. Weitere Informationen finden Sie unter  [Ändern der App und Staging an Azure und Office 365](#Stage). 
5. Fügen Sie der **Page_Load**-Methode den folgenden Code hinzu. Die an die  `GetSAPData`-Methode übergebene Zeichenfolge ist eine OData-Abfrage.
    
  ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

  ```

6. Fügen Sie die folgende Methode zur Standardklasse hinzu. Diese Methode stellt zunächst sicher, dass der Cache für das Zugriffstoken ein gültiges Zugriffstoken enthält, das von Azure AD erhalten wurde. Dann erstellt sie eine HTTP **GET**-Anforderung, die das Zugriffstoken enthält, und sendet diese an den SAP OData-Endpunkt. Das Ergebnis wird als JSON-Objekt zurückgegeben, das in ein .NET **List**-Objekt konvertiert wird. Drei Eigenschaften der Elemente werden in einem Array verwendet, das an **DataListView** gebunden ist.
    
  ```
  
private void GetSAPData(string oDataQuery)
{
    AADAuthHelper.EnsureValidAccessToken(this);

    using (WebClient client = new WebClient())
    {
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.Authorization] = "Bearer " + AADAuthHelper.AccessToken.Item1;
        var jsonString = client.DownloadString(SAP_ODATA_URL + oDataQuery);
        var jsonValue = JObject.Parse(jsonString)["d"]["results"];
        var dataCol = jsonValue.ToObject<List<Automobile>>();

        var dataList = dataCol.Select((item) => {
            return item.Brand + " " + item.Model + " " + item.Price;
            }).ToArray();

        DataListView.DataSource = dataList;
        DataListView.DataBind();
    }
}

  ```


### Erstellen der Benutzeroberfläche


1. Öffnen Sie die Datei Default.aspx, und fügen Sie dem **form** auf der Seite das folgende Markup hinzu:
    
  ```
  
<div>
  <h3>Data from SAP via SAP Gateway for Microsoft</h3>

  <asp:ListView runat="server" ID="DataListView">
    <ItemTemplate>
      <tr runat="server">
        <td runat="server">
          <asp:Label ID="DataLabel" runat="server"
            Text="<%# Container.DataItem.ToString()%>" /><br />
        </td>
      </tr>
    </ItemTemplate>
  </asp:ListView>
</div>
  ```

2. Geben Sie der Webseite optional das Erscheinungsbild einer SharePoint-Seite, indem Sie das SharePoint [-Chromsteuerelement](use-the-client-chrome-control-in-sharepoint-add-ins.md) und [das Stylesheet der Host-SharePoint-Website](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) verwenden.
    
  

### Testen der App mit F5 in Visual Studio


1. Drücken Sie in Visual Studio F5.
    
  
2. Wenn Sie F5 zum ersten Mal verwenden, werden Sie möglicherweise aufgefordert, sich bei der Website für Entwickler anzumelden, die Sie verwenden. Benutzen Sie dafür die Anmeldeinformationen des Websiteadministrators. Im fortlaufenden Beispiel ist das Bob@<O365_domain>.onmicrosoft.com.
    
  
3. Wenn Sie F5 zum ersten Mal verwenden, werden Sie aufgefordert, der App Berechtigungen zu gewähren. Klicken Sie auf **Vertrauen**.
    
  
4. Nach einer kurzen Verzögerung, in der das Zugriffstoken abgerufen wird, wird die Seite Default.aspx geöffnet. Vergewissern Sie sich, dass die SAP-Daten angezeigt werden.
    
  

## Optionales Hinzufügen eines Zugriffs auf SharePoint zur ASP.NET-Anwendung
<a name="SharePoint"> </a>

Natürlich muss Ihr SharePoint-Add-In nicht nur SAP-Daten auf einer von SharePoint gestarteten Webseite darstellen. Sie kann auch SharePoint-Daten erstellen, lesen, aktualisieren und löschen. Ihr zugrunde liegender Code kann dafür entweder das SharePoint-Clientobjektmodell (CSOM) oder die REST APIs von SharePoint verwenden. Das CSOM wird als ein Assemblypaar bereitgestellt, das die Office-Entwicklertools für Visual Studio automatisch in das ASP.NET-Projekt eingefügt und auf **Lokale Kopie** in Visual Studio festgelegt haben, damit sie im ASP.NET-Anwendungspaket enthalten sind. Weitere Informationen zum Verwenden von CSOM finden Sie unter [Ausführen grundlegender Vorgänge unter Verwendung von SharePoint 2013-Clientbibliothekscode](complete-basic-operations-using-sharepoint-2013-client-library-code.md). Informationen zum Verwenden der REST-APIs finden Sie unter  [Erläuterungen zur REST-Schnittstelle von SharePoint 2013 und zu ihrer Verwendung](http://msdn.microsoft.com/de-de/magazine/dn198245.aspx). 
  
    
    
Unabhängig davon, ob Sie CSOM oder die REST-APIs für den Zugriff auf SharePoint verwenden, muss Ihre ASP.NET-Anwendung ein Zugriffstoken für SharePoint abrufen, ebenso wie für das SAP-Gateway für Microsoft. Weitere Informationen finden Sie unter  [Verstehen der Authentifizierung und Autorisierung beim SAP-Gateway für Microsoft und SharePoint](#AuthOverview) weiter oben. Das Verfahren unten stellt einige grundlegende Anweisungen zur entsprechenden Vorgehensweise bereit, aber wir empfehlen, dass Sie zunächst die folgenden Artikel lesen:
  
    
    

-  [Erste Schritte beim Erstellen von von einem Anbieter gehosteten SharePoint-Add-Ins](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Autorisierung und Authentifizierung für Add-Ins in SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Drei Autorisierungssysteme für SharePoint-Add-Ins](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Erstellen von SharePoint-Add-Ins, die die Autorisierung mit niedriger Vertrauensebene verwenden](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
-  [OAuth-Ablauf mit Kontexttoken für Add-Ins in SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  

1. Öffnen Sie die Datei Default.aspx.cs, und kommentieren Sie die **Page_PreInit**-Methode aus. Kommentieren Sie außerdem den Code aus, den die Office-Entwicklertools für Visual Studio zur **Page_Load**-Methode hinzugefügt haben.
    
  
2. Wenn Ihr SharePoint-Add-In auf SharePoint-Daten zugreifen wird, müssen Sie das SharePoint-Kontexttoken auskommentieren, das an die Default.aspx-Seite übertragen wurde, als die App in SharePoint gestartet wurde. Damit wird sichergestellt, dass das SharePoint-Kontexttoken nicht verloren geht, wenn der Browser nach der Azure AD-Authentifizierung umgeleitet wird. (Sie haben mehrere Optionen für das Zwischenspeichern dieses Kontexts. Weitere Informationen finden Sie unter  [OAuth-Token](cf2bdd88-4b04-47f6-a876-322f734a6af2.md#Tokens).) Die Office-Entwicklertools für Visual Studio fügen eine SharePointContext.cs-Datei zum ASP.NET-Projekt hinzu, die den größten Teil der Arbeit übernimmt. Zum Verwenden des Sitzungscaches fügen Sie einfach den folgenden Code in den Block " `if (!IsPostBack)`"  *vor*  den Code ein, der den Aufruf an SAP-Gateway für Microsoft durchführt:
    
  ```
  
if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
  ```

3. Die SharePointContext.cs-Datei führt Aufrufe an eine andere Datei durch, die die Office-Entwicklertools für Visual Studio zum Projekt hinzugefügt haben: TokenHelper.cs. Diese Datei stellt den größten Teil des Codes bereit, der erforderlich ist, um Zugriffstoken für SharePoint abzurufen und zu verwenden. Sie stellt jedoch keinen Code für das Erneuern eines abgelaufenen Zugriffstoken oder Aktualisierungstoken bereit. Sie enthält außerdem keinen Code zum Zwischenspeichern von Token. Für ein SharePoint-Add-In in Produktionsqualität benötigen solchen Code. Die Zwischenspeicherlogik im vorherigen Schritt ist ein Beispiel. Ihr Code sollte außerdem das Zugriffstoken zwischenspeichern und es wiederverwenden, bis es abgelaufen ist. Wenn das Zugriffstoken abgelaufen ist, sollte Ihr Code das Aktualisierungstoken verwenden, um ein neues Zugriffstoken abzurufen. Es wird empfohlen, dass Sie  [OAuth-Token](cf2bdd88-4b04-47f6-a876-322f734a6af2.md#Tokens) lesen.
    
  
4. Fügen Sie die Datenaufrufe an SharePoint entweder über CSOM oder REST hinzu. Im folgenden Beispiel wurde CSOM-Code geändert, den die Office-Entwicklertools für Visual Studio zur **Page_Load**-Methode hinzufügen. In diesem Beispiel wurde der Code an eine separate Methode verschoben und beginnt mit dem Abrufen des zwischengespeicherten Kontexttokens.
    
  ```
  
private void GetSharePointTitle()
{
    var spContext = HttpContext.Current.Session["SharePointContext"] as SharePointContext;
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        clientContext.Load(clientContext.Web, web => web.Title);
        clientContext.ExecuteQuery();
        SharePointTitle.Text = "SharePoint web site title is: " + clientContext.Web.Title;
    }
}
  ```

5. Fügen Sie Benutzerflächenelemente hinzu, um die SharePoint-Daten wiederzugeben. Im Folgenden ist das HTML-Steuerelement gezeigt, das in der vorhergehenden Methode referenziert wird:
    
  ```
  
<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
  ```


> **HINWEIS**
> Während Sie das SharePoint-Add-In debuggen, wird sie von den Office-Entwicklertools für Visual Studio jedes Mal erneut bei Azure ACS registriert, wenn Sie F5 in Visual Studio drücken. Wenn Sie das SharePoint-Add-In bereitstellen, müssen Sie ihr eine langfristige Registrierung geben. Weitere Informationen finden Sie im Abschnitt  [Ändern der App und Staging an Azure und Office 365](#Stage). 
  
    
    


## Ändern der App und Staging an Azure und Office 365
<a name="Stage"> </a>

Wenn Sie das Debugging des SharePoint-Add-In mit F5 in Visual Studio abgeschlossen haben, müssen Sie die ASP.NET-Anwendung an eine tatsächliche Azure-Website bereitstellen.
  
    
    

### Erstellen der Azure-Website


1. Öffnen Sie im Microsoft Azure-Portal auf der linken Navigationsleiste **WEBSITES**.
    
  
2. Klicken Sie im unteren Bereich der Seite auf **NEU**, und wählen Sie im Dialogfeld **NEU** die Option **WEBSITE** |**SCHNELLERFASSUNG** aus.
    
  
3. Geben Sie einen Domänennamen ein, und klicken Sie auf **WEBSITE ERSTELLEN**. Kopieren Sie die URL der neuen Website. Diese hat die Form  `my_domain.azurewebsites.net`.
    
  

### Ändern des Codes und Markups in der Anwendung


1. Entfernen Sie in Visual Studio die Zeile  `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;` aus der Datei Default.aspx.cs.
    
  
2. Öffnen Sie die web.config-Datei des ASP.NET-Projekts, und ändern Sie den Domänenteil des Werts für den **AppRedirectUrl**-Schlüssel im Abschnitt **appSettings** in die Domäne der Azure-Website. Ändern Sie beispielsweise `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` in `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`.
    
  
3. Klicken Sie mit der rechten Maustaste auf die Datei AppManifest.xml im SharePoint-Add-In-Projekt, und wählen Sie **Code anzeigen** aus.
    
  
4. Ersetzen Sie im Wert **StartPage** die Zeichenfolge `~remoteAppUrl` durch die vollständige Domäne der Azure-Website, einschließlich Protokoll, z. B. `https://my_domain.azurewebsites.net`. Der gesamte **StartPage**-Werte sollte jetzt wie folgt aussehen:  `https://my_domain.azurewebsites.net/Pages/Default.aspx`. (Normalerweise ist der **StartPage**-Wert identisch mit dem Wert des **AppRedirectUrl**-Schlüssels in der web.config-Datei.)
    
  

### Ändern der AAD-Registrierung und Registrieren der App bei ACS


1. Melden Sie sich im  [Azure-Verwaltungsportal](https://manage.windowsazure.com) mit Ihrem Azure-Administratorkonto an.
    
  
2. Wählen Sie auf der linken Seite **Active Directory** aus.
    
  
3. Klicken Sie auf Ihr Verzeichnis.
    
  
4. Wählen Sie **ANWENDUNGEN** (in der obersten Navigationsleiste) aus.
    
  
5. Öffnen Sie die von Ihnen erstellte Anwendung. Im fortlaufenden Beispiel ist das **ContosoAutomobileCollection**.
    
  
6. Ändern Sie für jeden der folgenden Werte den Teil "localhost: *port*  " des Werts in die Domäne Ihrer neuen Azure-Website:
    
  - **ANMELDE-URL**
    
  
  - **APP-ID- URI**
    
  
  - **ANTWORT-URL**
    
  

    Wenn beispielsweise der **APP-ID-URI** **https://localhost:44304/ContosoAutomobileCollection** ist, ändern Sie ihn zu **https://<my_domain>.azurewebsites.net/ContosoAutomobileCollection**.
    
  
7. Klicken Sie unten auf dem Bildschirm auf **SPEICHERN**.
    
  
8. Registrieren Sie die App bei Azure ACS. Dies muss auch dann durchgeführt werden, wenn die App nicht auf SharePoint zugreift, und dabei werden keine Token von ACS verwendet, da derselbe Prozess die App auch beim App-Verwaltungsdienst des Office 365-Abonnements registriert, was eine Voraussetzung ist. (Er wird als „Add-In-Verwaltungsdienst" bezeichnet, da SharePoint-Add-Ins ursprünglich „Apps für SharePoint" hießen). Sie führen die Registrierung auf der Seite AppRegNew.aspx einer beliebigen SharePoint-Website im Office 365-Abonnement durch. Ausführliche Informationen finden Sie unter  [Registrieren von SharePoint-Add-Ins 2013](register-sharepoint-add-ins-2013.md). Als Teil dieses Prozesses erhalten Sie eine neue Client-ID und einen neuen geheimen Clientschlüssel. Geben Sie diese Werte in die web.config für die Schlüssel **ClientId** (nicht **ida:ClientID**) und **ClientSecret** ein.
    
    > **VORSICHT**
      > Wenn Sie aus irgendeinem Grund nach dieser Änderung F5 drücken, überschreiben die Office-Entwicklertools für Visual Studio einen oder beide dieser Werte. Aus diesem Grund sollten Sie die mit AppRegNew.aspx erhaltenen Werte aufzeichnen und immer sicherstellen, dass die Werte in der web.config korrekt sind, kurz bevor Sie die ASP.NET-Anwendung veröffentlichen. 

### Veröffentlichen der ASP.NET-Anwendung an Azure und Installieren der App an SharePoint


1. Es gibt verschiedene Möglichkeiten, eine ASP.NET-Anwendung an eine Azure-Website zu veröffentlichen. Weitere Informationen finden Sie unter  [Bereitstellen einer Azure-Website](http://azure.microsoft.com/de-de/documentation/articles/web-sites-deploy/).
    
  
2. Klicken Sie in Visual Studio mit der rechten Maustaste auf das SharePoint-App-Projekt, und wählen Sie **Paket** aus. Klicken Sie auf der sich öffnenden Seite **App veröffentlichen** auf **App verpacken**. Der Datei-Explorer öffnet sich mit dem Ordner mit dem App-Paket.
    
  
3. Melden Sie sich bei Office 365 als globaler Administrator an, und navigieren Sie zur App-Katalog-Websitesammlung der Organisation. (Falls keine vorhanden ist, erstellen Sie eine. Informationen finden Sie unter  [Verwenden des App-Katalogs zur Bereitstellung von benutzerdefinierten Geschäfts-Apps für Ihre SharePoint Online-Umgebung](http://office.microsoft.com/de-de/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).)
    
  
4. Laden Sie das App-Paket in den App-Katalog hoch.
    
  
5. Navigieren Sie zur Seite **Websiteinhalt** einer beliebigen Website im Abonnement, und klicken Sie auf **App hinzufügen**.
    
  
6. Blättern Sie auf der Seite **Ihre Apps** zum Abschnitt **Apps, die Sie hinzufügen können**, und klicken Sie auf das Symbol für Ihre App.
    
  
7. Nachdem die App installiert wurde, klicken Sie auf ihr Symbol auf der Seite **Websiteinhalt**, um die App zu starten.
    
  
Weitere Informationen zum Installieren von SharePoint-Add-Ins finden Sie unter  [Bereitstellen und Installieren von SharePoint-Add-Ins: Methoden und Optionen](deploying-and-installing-sharepoint-add-ins-methods-and-options.md).
## Bereitstellen der App an die Produktion
<a name="Stage"> </a>

Wenn Sie das gesamte Testen abgeschlossen haben, können Sie die App an die Produktion bereitstellen. Dafür sind möglicherweise einige Änderungen erforderlich.
  
    
    

1. Wenn die Produktionsdomäne der ASP.NET-Anwendung eine andere als die Stagingdomäne ist, müssen Sie den Wert **AppRedirectUrl** in der web.config und den Wert **StartPage** in der AppManifest.xml-Datei ändern und das SharePoint-Add-In erneut verpacken. Informationen dazu finden Sie im Verfahren **Ändern des Codes und Markups in der Anwendung** weiter oben.
    
  
2. Durch die Domänenänderung müssen Sie außerdem die App-Registrierung bei AAD ändern. Informationen dazu finden Sie im Verfahren **Ändern der AAD-Registrierung und Registrieren der App bei** weiter oben.
    
  
3. Durch die Domänenänderung müssen Sie zudem die App erneut bei ACS (und dem App-Verwaltungsdienst des Abonnements) registrieren, wie im selben Verfahren beschrieben wird. (Es gibt keine Möglichkeit, die Registrierung einer App bei ACS  *zu bearbeiten*  .) Es ist allerdings nicht erforderlich, eine neue Client-ID oder einen geheimen Clientschlüssel auf der Seite AppRegNew.aspx zu generiere. Sie können die ursprünglichen Werte aus den Schlüsseln **ClientId** (nicht **ida:ClientID**) und **ClientSecret** der web.config in das AppRegNew-Formular kopieren. *Wenn Sie neue Werte generieren, achten Sie darauf, dass Sie die neuen Werte in die Schlüssel in der web.config kopieren.* 
    
  

