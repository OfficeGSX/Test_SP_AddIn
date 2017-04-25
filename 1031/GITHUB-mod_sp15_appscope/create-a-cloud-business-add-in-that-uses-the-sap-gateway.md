---
title: Erstellen eines Cloud-Geschäfts-Add-Ins, das das SAP-Gateway verwendet
ms.prod: OFFICE365
ms.assetid: b96f887d-d892-4b1d-b832-a3f61228c5cf
---



# Erstellen eines Cloud-Geschäfts-Add-Ins, das das SAP-Gateway verwendet
 Erfahren Sie, wie Sie das vom SharePoint-Anbieter gehostete LightSwitch-Add-In zum Zugreifen auf SAP-Daten mit dem SAP-Gateway für Microsoft erstellen.
In diesem Artikel werden die wichtigsten Schritte des Entwicklungsteams von Microsoft bei der Erstellung eines vom SharePoint-Anbieter gehosteten LightSwitch-Add-Ins erläutert, mit dem auf SAP-Daten mit dem SAP-Gateway für Microsoft zugegriffen werden kann. Dieses Add-In unterstützt CRUD-Vorgänge für SAP-Daten und kann zum Anzeigen von Bildern aus und zum Hochladen von Bildern zu einer SharePoint-Bildbibliothek verwendet werden.




Der Zweck dieses Artikels besteht darin, die wichtigsten Punkte des Add-Ins aufzuzeigen, die Sie beim Erstellen ähnlicher Add-Ins unterstützen. Es wird auch ein Codebeispiel bereitgestellt und mit dem Artikel verknüpft, damit Sie besser nachvollziehen können, wie die funktionierende Lösung erstellt wurde.
 **Beispiel herunterladen:** [ Beispiel: Entwickeln eines Cloud-Business-Add-Ins zum Zugriff auf das SAP-Gateway für Microsoft](https://code.msdn.microsoft.com/Sample-Developing-a-Cloud-25d6d1ea)





## Bevor Sie beginnen

Im Folgenden finden Sie die Voraussetzungen zu den Vorgehensweisen in diesem Artikel:




- **Eine Office 365-Entwicklerwebsite** in einer Office 365-Domäne, die einem Microsoft Azure Active Directory (Azure AD)-Mandanten zugeordnet ist. Weitere Informationen finden Sie unter [Registrieren für ein Office 365 Developer-Abonnement, Einrichten Ihrer Tools und Umgebung und Beginnen mit der Bereitstellung Ihres Add-Ins](https://msdn.microsoft.com/de-de/library/office/fp179924%28v=office.15%29.aspx) oder [Vorgehensweise: Erstellen einer Entwicklerwebsite in Ihrem vorhandenen Office 365-Abonnement](https://msdn.microsoft.com/de-de/library/office/jj692554%28v=office.15%29.aspx).


- **Visual Studio 2013 Update 4** oder höher, das unter [Willkommen bei Visual Studio 2013](http://msdn.microsoft.com/de-de/library/dd831853.aspx) heruntergeladen werden kann.


- **Microsoft Office Developer Tools für Visual Studio.** Die Version, die in Update 4 von Visual Studio 2013 oder höher enthalten ist.


- **SAP-Gateway für Microsoft** wird in Microsoft Azure bereitgestellt und konfiguriert. Einzelheiten finden Sie in der Dokumentation zu [SAP-Gateway für Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).


- **Ein Organisationskonto in Microsoft Azure.** Weitere Informationen finden Sie unter [Erstellen eines Organisationsbenutzerkontos in Azure AD](http://go.microsoft.com/fwlink/?LinkID=512580).

    > **HINWEIS**
    > Melden Sie sich in Ihrem Office 365-Konto (login.microsoftonline.com) an, um das temporäre Kennwort zu ändern, nachdem das Konto erstellt wurde. 
- **Ein SAP OData-Endpunkt** mit Beispieldaten. Weitere Informationen finden Sie in der Dokumentation zu [SAP-Gateway für Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).


- **Grundkenntnisse in Azure AD.** Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD](http://msdn.microsoft.com/de-de/library/azure/dn655157.aspx).


- **Grundkenntnisse der Erstellung von Add-Ins für SharePoint.** Weitere Informationen finden Sie unter [Gewusst wie: Erstellen eines einfachen, von einem Anbieter gehosteten Add-Ins für SharePoint](http://msdn.microsoft.com/de-de/library/office/fp142381%28v=office.15%29.aspx).


- **Grundkenntnisse von OAuth 2.0 in Azure AD.** Weitere Informationen finden Sie unter [OAuth 2.0 in Azure AD](http://msdn.microsoft.com/de-de/library/azure/dn645545.aspx) und den angrenzenden Themen.



## Übersicht über die Lösung

Das fiktive Autounternehmen Contoso Motors verkörpert ein echtes Unternehmensszenario, in dem ein Autohaus Informationen über seinen Fahrzeugbestand für Handelsvertreter und andere Händler freigibt. Mithilfe dieses Add-Ins kann das Verkaufspersonal den aktuellen Fahrzeugbestand prüfen, auf den das Add-In direkt über SAP mit dem SAP-Gateway für Microsoft zugreift. Alle in SAP gespeicherten Daten und ein Bild von jedem Fahrzeug sind in einer Bildbibliothek in SharePoint Online gespeichert. In diesem Add-In werden die Daten aus SAP und der SharePoint-Bildbibliothek zusammengeführt und angezeigt. Innerhalb des Add-Ins können Manager und Administratoren Standard-CRUD-Vorgänge für detaillierte Informationen zu jedem Fahrzeug in SAP durchführen.



Im Beispiel in diesem Artikel werden die Fahrzeugbestandsdaten aus SAP und die zugehörigen Bilder in der SharePoint-Bildbibliothek von dem SellerDashboard-Add-In gehandhabt. Mit diesem Add-In werden die standardmäßigen CRUD-Vorgänge (Create, Read, Update und Delete) für die Detailinformationen zu jedem Fahrzeug in SAP unterstützt.




> **TIPP**
> Eine Videovorführung dieses Add-Ins finden Sie im Video  [Technisches Webinar - SAP-Gateway für Microsoft und das Microsoft Azure-Entwicklungsmodell](http://go.microsoft.com/fwlink/?LinkId=517378) auf BrightTalk. Das Demo der Add-In-Funktionalität beginnt ab der 48. Minute.




 **Codearchitektur**



Die SellerDashboard-Lösung umfasst acht Projekte und ist wie in der folgenden Abbildung dargestellt in zwei Kategorien unterteilt: BoxXDataStudio und SellerDashboardStudio.







![SellerDashboard](images/4092aef3-2fb9-43f1-ad9a-0a326c4648d4.jpg)




### BoxXDataStudio

Dies umfasst alle für die Interaktion mit dem SAP-Gateway für Microsoft benötigten Komponenten.




- **AADAuthLib**

    Mit dieser Komponente wird der [Azure AD-Autorisierungscodefluss](https://msdn.microsoft.com/de-de/library/azure/dn645542.aspx) ausgeführt, für den ein Singleton-Muster verwendet wird.

    Der folgende Codeausschnitt veranschaulicht die Hauptfunktion, die die AADAuthLib-Komponente bereitstellt. Weitere Informationen finden Sie unter AADAuthLib/AuthUtil.cs-Beispielcode-Hilfsklasse.



  ```cs
  // Use the auth code, acquire the refresh token and access token, and store them in the current session
        public bool AcquireTokenFromAuthCode(string authCode, string redirectUrl = "redirectUrl")
        {
            if (!IsAuthorized &amp;&amp; !string.IsNullOrEmpty(authCode))
            {
                AuthenticationResult authResult = _authContext.AcquireTokenByAuthorizationCode(authCode,
                    new Uri(ConfigurationManager.AppSettings["ida:" + redirectUrl]),
                    _clientCredential,
                    ConfigurationManager.AppSettings["ida:ResourceUrl"]);

                HttpContext.Current.Session["RefreshToken"] = authResult.RefreshToken;
                HttpContext.Current.Session["AccessToken-" + ConfigurationManager.AppSettings["ida:ResourceUrl"]] = new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);

                return true;
            }

            return false;
  }

  // Provide the access token, which is stored in the current session, if the access token expired, use the refresh token get a new one.
  public string AccessToken
        {
            get
            {
                if (!IsAuthorized) throw new UnauthorizedAccessException();

                var accessToken = HttpContext.Current.Session["AccessToken-" + ConfigurationManager.AppSettings["ida:ResourceUrl"]] as Tuple<string, DateTimeOffset>;

                if (IsAccessTokenValid(accessToken))
                {
                    return accessToken.Item1;
                }

                accessToken = RenewAccessToken();
                HttpContext.Current.Session["AccessToken-" + ConfigurationManager.AppSettings["ida:ResourceUrl"]] = accessToken;

                return accessToken.Item1;
            }
  }
  ```

- **BoxXDataService**

    Dies ist ein WCF-RIA-Dienst, der die von der in Azure gehosteten serverseitigen SellerDashboard-Komponente verwendete Schnittstelle darstellt und die SAP-Datenquelle vom SAP-Gateway für Microsoft verwendet.

    Der folgende Codeausschnitt veranschaulicht die CRUD-Webmethode, die vom WCF-RIA-Dienst unterstützt wird. Weitere Informationen finden Sie unter BoxXDataService/BoxXDataService.cs.



  ```cs
[Query(IsDefault = true)]
        public IQueryable<InventoryItem> GetAllCarInventory()
        {
            BoxXDataReader dataReader = BoxXDataReader.Instance;
            IInventoryCollection dataCollection = ModelFactory.CreateInventoryCollection(dataReader.SchemaPropertyNames);
            IInventoryQuery query = QueryFactory.CreateQuery();
            dataReader.GetData(dataCollection, query);

            return dataCollection.Items.Cast<InventoryItem>().AsQueryable();
        }

        [Update]
        public void UpdateCarInventoryItem(InventoryItem carInventoryItem)
        {
            BoxXDataUpdater dataUpdater = BoxXDataUpdater.Instance;
            dataUpdater.UpdateInventoryItem(carInventoryItem);
        }

        [Insert]
        public void InsertCarInventoryItem(InventoryItem carInventoryItem)
        {
            BoxXDataCreater dataCreater = BoxXDataCreater.Instance;
            dataCreater.CreateInventoryItem(carInventoryItem);
        }

        [Delete]
        public void DeleteCarInventoryItem(InventoryItem carInventoryItem)
        {
            BoxXDataDeleter.DeleteInventoryItem(carInventoryItem.ID);
 }
  ```

- **CarInventoryBoxXDataOperation**

    Dies ist die tatsächliche CRUD-Implementierung für BoxXDataService. Die folgende Abbildung veranschaulicht die grundlegende Codearchitektur von CarInventoryBoxXDataOperation. Der CRUD-Namespace wird zum Implementieren von CRUD-Vorgängen verwendet, und der Util-Namespace ist der Hilfscode für den CRUD-Namespace.

![CarInventoryBoxDataOperation](images/a9baa59c-4459-4f0d-935d-456f0ca7791e.jpg)





- **CarInventoryModel**

    Dies ist ein Bibliotheksprojekt, das zum Implementieren des Datenmodells und von Hilfsprogrammen verwendet wird. Die Datenmodelldefinition muss mit den Metadaten des vom SAP-Gateway für Microsoft verwendeten Data-Endpunkts übereinstimmen, welches auf dem SAP-Datenschema basiert. Es wird von BoxXDataService und CarInventoryBoxXDataOperation zum Konvertieren des SAP-Datenbankelements in eine Fahrzeugbestandsinstanz verwendet.

    In der folgenden Abbildung werden die Hauptkomponenten dargestellt.






![CarInventoryModel](images/1a814b3b-76a3-4a5c-8eef-550893728499.jpg)












### SellerDashboardStudio

SellerDashboardStudio umfasst die standardmäßigen LightSwitch-Add-In-Komponenten für SharePoint.




- **SellerDashboard**

    SellerDashboard ist das StartUp-Projekt und wird zum Veröffentlichen des SellerDashboard-Add-Ins auf der Azure-Website und des SharePoint-Add-Ins auf der SharePoint-Entwicklerwebsite verwendet.


- **SellerDashboard.HTMLClient**

    Diese Komponente umfasst Bildschirme, ein benutzerdefiniertes Steuerelement und ein Fotoupload-Steuerelement. In der folgenden Abbildung werden die Hauptkomponenten dargestellt.


- ![SellerDashboard.HTMLClient](images/89aa8c23-f8f2-410e-b021-7b0959e11586.jpg)


    SellerDashboard.HTML.Client umfasst die folgenden vier Bildschirme:

  - BrowseInventoryItems ist der Startbildschirm und wird zum Durchsuchen von grundlegenden Informationen für alle Bestandselemente verwendet.


  - ViewInventoryItem ist der Detailbildschirm und wird zum Anzeigen einer detaillierten Ansicht für jedes Bestandselement verwendet.


  - EditInventoryItem ist der Popupbildschirm "Bearbeiten" und wird zum Bearbeiten eines Bestandselement verwendet.


  - AddInventoryItem ist ein Popupbildschirm "Hinzufügen" und wird zum Hinzufügen eines neuen Bestandselements verwendet.



    Die UserCode.js-Datei umfasst den Code für das benutzerdefinierte UX-Steuerelement.

    Die Dateien "photohelper.js" und "sharepointauthhelper.js" im Skriptordner basieren auf dem Leitfaden im Artikel  [Exemplarische Vorgehensweise: Erstellen eines Add-Ins für SharePoint mithilfe von LightSwitch](http://msdn.microsoft.com/de-de/library/jj969621.aspx).


- **SellerDashboard.Server**

    Diese Komponente umfasst die WCF-RIA-Datenquelle, die SharePoint-Datenquelle und die Fotoupload-Web-API. In der folgenden Abbildung werden die Hauptkomponenten dargestellt.

![SellerDashboard.Server](images/43e92a09-180d-4adf-9ae2-8f6212d297c9.jpg)


SharePointContext.cs und TokenHelper.cs sind die Hilfsklassen, die den Clientkontext zum Authentifizieren für SharePoint bereitstellen. Damit kann die Fotoupload-Web-API zum Hochladen von Fahrzeugfotos in die Bildbibliothek verwendet werden. Die Dateien "PhotoListHelper.cs" und "PhotosController.cs" implementieren die Fotoupload-Web-API. Weitere Informationen zur Verwendung der Fotoupload-Web-API mit LightSwitch finden Sie im Artikel  [Vorgehensweise: Erstellen eines Add-Ins für SharePoint mithilfe von LightSwitch](http://msdn.microsoft.com/de-de/library/jj969621.aspx).


- **SellerDashboard.SharePoint**

    Diese Komponente ist nach der LightSwitch-Lösung zum Aktivieren des SharePoint-Projekts enthalten. Mit dieser Komponente können Sie die für die Bildbibliothek verwendete SharePoint-Website konfigurieren und die benötigten Berechtigungen für das Add-In festlegen. SellerDashboard benötigt die Berechtigungsstufe "Vollzugriff" für die SharePoint-Website, die die Bildbibliothek hostet.



## Implementierung






### Übersicht

In der folgenden Abbildung werden die Komponenten dargestellt, die SellerDashboard beeinflussen. Die durchgezogene Linie veranschaulicht den Datenfluss, und die punktierte Linie den OAuth-Fluss. Die grünen Komponenten sind mit den SAP-Datenvorgängen verknüpft, die blauen Komponenten sind mit dem SharePoint-Bildbibliotheksvorgang verknüpft, und die orangefarbenen Komponenten stellen die Vorgänge der gesamten SellerDashboard-App dar.



 **SellerDashboard-Lösung**







![SellerDashboard-Lösung](images/5be3a1d5-4d6e-4d08-aa9f-ca9b093325ae.jpg)












### Datenmashup

LightSwitch unterstützt den Datenmashup durch Hinzufügen einer Beziehung zwischen den zwei Datenquellen im Designer.



 **SAP-Datenquelle**




-  *Datenschema in der SAP-Datenbank* 

    Der folgende Codeausschnitt stellt ein Beispiel eines Datenschemas vom SAP-Gateway für Microsoft dar.



  ```XML

<?xml version="1.0" encoding="UTF-8"?> 
- <edmx:Edmx xmlns:sap="http://www.sap.com/Protocols/SAPData" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" 
xmlns:edmx:"http://schemas.microsoft.com/ado/2007/06/edmx" Version="1.0">
   - <edmx:DataServices m:DataServiceVersion="2.0">
      - <Schema xml:lang="en" xmlns="http://schemas.microsoft.com/ado/2008/09/edm" Namespace="ZCAR_POC_SRV">
         - <EntityType sap:content-version="1" Name="ContosoMotors">
              - <Key>
                     <PropertyRef Name="ID"/>
               </Key>
               <Property Name="ContactPhone" Type="Edm.String"/>
               <Property Name="ContactEmail" Type="Edm.String"/>
               <Property Name="ID" Type="Edm.Int32" Nullable="false"/>
               <Property Name="BuyerEmail" Type="Edm.String" MaxLength="255"/>
               <Property Name="MaxPower" Type="Edm.Int32" Nullable="false"/>
               <Property Name="Engine" Type="Edm.String" Nullable="false" MaxLength="255"/>
               <Property Name="BodyStyle" Type="Edm.String" Nullable="false" MaxLength="255"/>
               <Property Name="Transmission" Type="Edm.String" Nullable="false" MaxLength="255"/>
               <Property Name="Year" Type="Edm.Int32" Nullable="false"/>
               <Property Name="Model" Type="Edm.String" Nullable="false" MaxLength="255"/>
               <Property Name="Brand" Type="Edm.String" Nullable="false" MaxLength="255"/>
               <Property Name="ExtColor" Type="Edm.String" Nullable="false" MaxLength="255"/>
              <Property Name="IntColor" Type="Edm.String" Nullable="false" MaxLength="255"/>
              <Property Name="ContactName" Type="Edm.String" Nullable="false" MaxLength="255"/>
              <Property Name="Price" Type="Edm.String" Nullable="false"/>
              <Property Name="StockNo" Type="Edm.String" Nullable="false"/>
              <Property Name="Arrived_Date" Type="Edm.DateTime" Nullable="false" Precision="0"/>
              <Property Name="Status" Type="Edm.String" Nullable="false" MaxLength="255"/>
      </EntityType>
   - <EntityContainer Name="ZCAR_POC_SRV_Entities" m:IsDefaultEntityContainer="true">
          <EntitySet sap:content-version="1" Name="ContosoMotorsCollection" sap:searchable="true" EntityType="ZCAR_POC_SRV.ContosoMotors"/>
     </EntityContainer>
     <atom:link xmlns:atom="http://www.w3.org/2005/Atom" href="http://contoso.cloudapp.net:8080/perf/sap/opu/odata/sap/ZCAR_POC_SRV/$metadata" rel="self"/>
     <atom:link xmlns:atom="http://www.w3.org/2005/Atom" href="http://contoso.cloudapp.net:8080/perf/sap/opu/odata/sap/ZCAR_POC_SRV/$metadata" rel="latest-version"/>
</Schema>
</edmx:DataServices>
</edms:Edmx>           

  ```


Dies ist unsere Testdatenbank, und der Eigenschaftstyp und Nullable-Wert basieren auf dem Szenario. Die ID ist PropertyRef, und der OData-CRUD-Vorgang basiert auf der ID. Die StockNo-Eigenschaft wird zum Verbinden von Daten mit dem in der SharePoint-Bildbibliothek gespeicherten Fahrzeugbild verwendet .


-  *Für RIA-Dienst definiertes Datenmodell* 

  ```cs

public interface IInventoryItem
    	{
        IEnumerable<InventoryPropertyName> ValidPropertyNames { get; }
        bool IsValid { get; }

        int ID { get; set; }
        DateTime ArrivedDate { get; set; }
        string BodyStyle { get; set; }
        string Brand { get; set; }
        string BuyerEmail { get; set; }
        string ContactEmail { get; set; }
        string ContactName { get; set; }
        string ContactPhone { get; set; }
        string Engine { get; set; }
        string ExtColor { get; set; }
        IEnumerable<Uri> Images { get; }
        string IntColor { get; set; }
        int MaxPower { get; set; }
        string Model { get; set; }
        decimal Price { get; set; }
        bool Removed { get; }
        string Status { get; set; }
        string StockNo { get; set; }
        string Transmission { get; set; }
        int Year { get; set; }

        void AddImageUrl(Uri url);
        bool CopyFrom(IInventoryItem other);
        object GetPropertyValue(InventoryPropertyName property);
        void MarkAsRemoved();
        void SetPropertyValue(InventoryPropertyName property, object value);
       }

    	public interface IInventoryCollection
       {
        IEnumerable<InventoryPropertyName> QueriedPropertyNames { get; }
        IEnumerable<IInventoryItem> Items { get; }
        bool Valid { get; }

        IInventoryItem this[int id] { get; }
        bool Contains(int id);
        void AddItem(IInventoryItem inventoryItem);
        IInventoryCollection Filter(Predicate<IInventoryItem> match);
        bool CopyFrom(IInventoryCollection other);
}

  ```


    Alle nicht im SAP-Datenbankschema enthaltenen Eigenschaften können ignoriert werden. Die **Images** -Eigenschaft beispielsweise wurde hier für Skalierbarkeitsaspekte hinzugefügt. Dieses Datenmodell stellt eine mittlere Ebene zwischen der SAP-Datenbank und der SellerDashboard.Server-Datenquelle dar. Das LightSwitch-Projekt besteht aus zwei Komponenten: Ansicht und Server. Wenn Sie eine externe Datenquelle auf der Serverseite hinzufügen, können Sie mit LightSwitch eine abstrakte Datenebene erstellen, die zu der Datenquelle auf Serverseite hinzugefügt wird.

    Die meisten Eigenschaften weisen den gleichen Typ auf wie die Eigenschaften in dem SAP-Datenbankschema, mit Ausnahme von StockNo, deren Typ von **int** zu **string** geändert wurde. Das liegt daran, dass StockNo zum Definieren der Beziehung zwischen den SAP-Daten und der SharePoint-Bildbibliothek verwendet wird.

    > **TIPP**
      > StockNo muss vom Typ **Zeichenfolge** sein, da der in der SharePoint-Bildbibliothek gespeicherte Wert **Text** ist. Diese zwei Typen müssen übereinstimmen, damit der Datenmashup ausgeführt werden kann.

    Die Implementierung von zwei Schnittstellen findet in "CarInventoryModel/InventoryItem.cs" und "CarInventoryModel/InventoryCollection.cs" statt.


-  *Von der LightSwitch-Serverseite verwendete Datenquelle* 

![InventoryItem](images/b08243f7-3fa3-48b3-bf6c-e3ff49f2e2a2.jpg)


Wenn Sie den WCF-RIA-Dienst (BoxXDataService) auf dem SellerDashboard-Server hinzufügen, ist das in CarInventoryModel definierte Datenmodell miteinbezogen, und Sie erhalten die Tabelle mit relevanten Daten. Sie können den Typ einiger Eigenschaften ändern. Sie können z. B. den **BuyerEmail** -Typ von **Zeichenfolge** zu **E-Mail-Adresse** ändern, wodurch die Überprüfung des E-Mail-Formats auf Clientseite in LightSwitch unterstützt wird.


 **SharePoint-Bildbibliothek**



Die Bildbibliothek auf der SharePoint-Hostwebsite heißt "ContosoMotorsPictureLibrary" und enthält drei neue Spalten mit den Namen "StockNo", "ThumbnailUrl" und "FullImageUrl". Alle Spalten sind als **Text** -Felder konfiguriert.




> **TIPP**
> Bei Spaltennamen wird die Groß-/Kleinschreibung beachtet. 




 Die StockNo-Spalte wird zum Erstellen einer Beziehung zu SAP-Daten verwendet. Die Spalten "ThumbnailUrl" und "FullImageUrl" werden zum einfachen Abrufen der URL für das relevante Foto verwendet.



 *Von der LightSwitch-Serverseite verwendete Datenquelle* 



ContosoMotorsPicture ist ein LightSwitch-Datenquellmodell, das der Bildbibliothek der SharePoint-Hostwebsite auf der LightSwitch-Serverseite zugeordnet ist.







![ContosoMotorsPicture](images/1dfc317f-b4f2-46f7-9f3e-4b81f04ef0dd.jpg)



 **Datenbeziehung**



Fügen Sie eine 1:0-Beziehung oder eine 1:1-Beziehung zwischen InventoryItem und ContosoMotorsPicture hinzu. In der folgenden Abbildung wird dargestellt, wie dies unter Verwendung von LightSwitch in Visual Studio aussieht.







![Datenbeziehung](images/978d8147-cabd-422e-9d02-d8022a114bd6.jpg)












### OAuth mit Azure AD und ACS

Dieser Abschnitt enthält eine Übersicht darüber, wie das Add-In OAuth in Azure AD und Access Control Services (ACS) implementiert.



 **Übersicht**



SellerDashboard ist ein vom SharePoint-Anbieter gehostetes Add-In, das beide Datenquellen verwendet: die SharePoint-Bildbibliothek und SAP-Daten mit dem SAP-Gateway für Microsoft.



Für den Zugriff auf die SharePoint-Bildbibliothek muss SellerDashboard ACS authentifizieren und einen Zugriffstoken von ACS abrufen. Für den Zugriff auf SAP-Daten muss SellerDashboard jedoch einen anderen Zugriffstoken von Azure AD abrufen, da das SAP-Gateway für Microsoft Azure AD für die Authentifizierung und Autorisierung verwendet.







![Rechenzentrum](images/eb83c0d8-0937-4df7-984b-3d8b4717b2b3.jpg)



 **Implementierung**



SellerDashboard ist ein vom SharePoint-Anbieter gehostetes Add-In, die den Fotouploadvorgang unter Verwendung des clientseitigen SharePoint-Objektmodells unterstützt. Der zum Erstellen vom Clientkontext für den SharePoint-Host verwendete SharePoint-Kontext wird zwischengespeichert, und der Clientkontext wird dann von der Upload-Web-API verwendet.



Nachdem der OAuth mit ACS-Prozess abgeschlossen ist, wird die Authentifizierungsanforderung an Azure AD gesendet. Um diesen Prozess abzuschließen, verwendet das Add-In ein Cookie zum Anmelden bei Login.MicrosoftOnline.com und dann zum Abschließen des Azure AD-Autorisierungscodeflusses. Dieser gibt ein Aktualisierungstoken zurück, das vom Add-In für zukünftige Authentifizierungsanforderungen während der Sitzung zwischengespeichert wird.






```cs

protected override void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
 SharePointContext spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
        if (spContext != null)
        {
     // Cache the SharePoint Context, which will be used to create Client Context for SharePoint host
            _spContext = spContext;
        }

		
        // Acquire refresh token and access token, and store them in the current session, which will be used do the OAuth with AAD for operating the SAP data
        AuthUtil.Instance.AcquireTokenFromAuthCode(Request.QueryString["code"]);

        if (!AuthUtil.Instance.IsAuthorized)
        {
             Response.Redirect(AuthUtil.Instance.AuthCodeUrl);
        }
     }

     base.Page_Load(sender, e);
 }
```

Weitere Informationen finden Sie unter "SellerDashBoard.Server/SharePointLaunch.aspx.cs" und "AADAuthLib/AuthUtil.cs" im Codebeispiel.



SellerDashboard.Server enthält die Dateien "SharePointContext.cs" und "TokenHelper.cs", die zum Abrufen von Nur-Add-In-Clientkontext für den SharePoint-Host verwendet werden. Diese zwei Dateien werden von Visual Studio automatisch zu Ihrem SharePoint-Projekt hinzugefügt. (Visual Studio benennt das Projekt < *IhrProjektname*  >Web, z. B. SharePointAppWeb.) Sie können dann die zwei Dateien in Ihren SellerDashboard-Add-In-Quellcode hinzufügen und in Ihr Projekt einbeziehen.




### CRUD-Vorgänge für SAP-Daten vom SAP-Gateway für Microsoft

Im folgenden Codeausschnitt wird die Hauptcodelogik der CRUD-Vorgänge dargestellt. Weitere Informationen finden Sie unter Implementierung von CarInventoryBoxXDataOperation im Codebeispiel.



 **BoxXDataCreater**






```cs

        public void CreateInventoryItem(IInventoryItem inventoryItem)
        {
            if (_model == null)
            {
                _model = BoxXDataCRUDHelpers.GetMetadata(BoxXDataCRUDHelpers.MetadataUrl);
            }

            // Create new entry request generate
            var requestMessage = new ODataClientRequestMessage(new Uri(BoxXDataCRUDHelpers.DataCollectionUrl),
                                                                ODataConstants.MethodPost);
            BoxXDataCRUDHelpers.CreateEntryRequestHeaderSetter(requestMessage, AuthUtil.Instance.AccessToken);
            BoxXDataCRUDHelpers.SetODataRequestContent(requestMessage, _model, inventoryItem);

            // Create new entry request execute
            IODataResponseMessage responseMessage = requestMessage.GetResponse();

        }
BoxXDataReader
 public void GetData(IInventoryCollection dataCollection, IInventoryQuery query)
        {
            GetValueWithoutLocallyFiltering(dataCollection, query);

            if (query.Filters.Any())
            {
                var filteredCollection = dataCollection.Filter(inventoryItem =>
                    query.Filters.Any(filter => filter.Match(inventoryItem)));

                dataCollection.CopyFrom(filteredCollection);
            }
        }
```

 **BoxXDataUpdater**






```cs

public void UpdateInventoryItem(IInventoryItem inventoryItem)
        {
            if (_model == null)
            {
                _model = BoxXDataCRUDHelpers.GetMetadata(BoxXDataCRUDHelpers.MetadataUrl);
            }

            // Update existing entry request generate
            var requestMessage = new ODataClientRequestMessage(new Uri(BoxXDataCRUDHelpers.DataCollectionUrl + '(' + inventoryItem.ID + ')'),
                                                                ODataConstants.MethodPut);
            BoxXDataCRUDHelpers.UpdateEntryRequestHeaderSetter(requestMessage, AuthUtil.Instance.AccessToken);
            BoxXDataCRUDHelpers.SetODataRequestContent(requestMessage, _model, inventoryItem);

            // Update existing entry request execute
            IODataResponseMessage responseMessage = requestMessage.GetResponse();
        }
BoxXDataDeleter
 public static void DeleteInventoryItem(int ID)
        {
            // Delete existing entry request generate
            var requestMessage = new ODataClientRequestMessage(new Uri(BoxXDataCRUDHelpers.DataCollectionUrl + '(' + ID + ')'),
                                                                ODataConstants.MethodDelete);
            BoxXDataCRUDHelpers.DeleteEntryRequestHeaderSetter(requestMessage, AuthUtil.Instance.AccessToken);

            // Delete existing entry request execute
            IODataResponseMessage responseMessage = requestMessage.GetResponse();
        }
```


### Hochladen von Fotos in der SharePoint-Bildbibliothek

Mit dem Fotoupload-Steuerelement und dem Implementieren der Fotoupload-Web-API werden Fotos in der SharePoint-Bildbibliothek hochgeladen. Dabei wird der folgende Code verwendet, der auf dem Leitfaden im Artikel  [Vorgehensweise: Erstellen eines Add-Ins für SharePoint mithilfe von LightSwitch](http://msdn.microsoft.com/de-de/library/jj969621.aspx) basiert.



 **Änderungen für photohelper.js:**



Es wird ein neues Feld zu uploadForm hinzugefügt, der zum Weitergeben von StockNo an die Fotoupload-Web-API verwendet wird.






```

uploadForm = $(
             '<form id="uploadForm" method="POST" enctype="multipart/form-data" action="' + API_URL + '"  data-ajax="false" target="uploadTargetIFrame">' +
             '   <input name="fileInput" id="fileInput" type="file" size="30" data-theme="c" accept="image/*" multiple="true"/>' +
             '   <input type="hidden" name=' + screen.InventoryItem.StockNo + '>' +
             '</form>');

```

Fügen Sie die Logik zum Zwischenspeichern der Foto-URL und die Logik zum Zurücksetzen von Fotos hinzu.






```

function completeUpload(uploadedFiles) {
            var fullImageUrl = uploadedFiles[0];
            var photoNameWithExt = fullImageUrl.substr(fullImageUrl.lastIndexOf('/') + 1);
            var photoNameWithoutExt = photoNameWithExt.substr(0, photoNameWithExt.lastIndexOf("."));
            var photoPathUri = fullImageUrl.substr(0, fullImageUrl.lastIndexOf('/'));
            var photoExt = photoNameWithExt.substr(photoNameWithExt.lastIndexOf(".") + 1, photoNameWithExt.length);
            var thumbnailUrl = photoPathUri + "/_t/" + photoNameWithoutExt + "_" + photoExt + ".jpg";
            setCacheUrl(screen.InventoryItem.StockNo, fullImageUrl + "*#00#" + thumbnailUrl);
            setDetailsCarPicture(fullImageUrl);
            screen.closePopup();
        }
```

 **Änderungen für PhotosController.cs:**



Verwenden Sie den Nur-App-Clientkontext für den SharePoint-Host, der vom zwischengespeicherten SharePoint-Kontext erstellt wurde.



Dies ist notwendig, da ContosoMotorsPictureLibrary nicht zum SellerDashboard-Add-In gehört, sondern eher zur SharePoint-Hostwebsite. Entfernen Sie zudem den für das Löschen von Fotos relevanten Code.






```cs

private ClientContext AppWebContext
    {
        get
        {
            if (appWebContext == null)
            {
                appWebContext = SharePointLaunch.getAppWebContext();
            }
            return appWebContext;
        }
    }

```

 **Änderungen für PhotoListHelper.cs:**



Fügen Sie die Logik zum Aktualisieren von Fotos, mit der alte Fotoelemente, falls vorhanden, gelöscht werden hinzu, und fügen Sie dann ein neues Fotoelement hinzu. Weisen Sie die StockNo-Eigenschaft des neues Fotoelements zu und legen Sie die FullImageUrl- und ThumbnailUrl-Werte für das neue Fotoelement fest.






```cs

            // Delete the old picture item
            foreach (ListItem item in items)
            {
                if (!string.IsNullOrEmpty((string)(item["StockNo"])))
                {
                    string tempStockNo = item["StockNo"].ToString();
                    if (tempStockNo.Equals(stockNo))
                    {
                        item.DeleteObject();
                        siteContext.ExecuteQuery();
                        break;
                    }
                }
            }

            // Update the new picture item
            foreach (ListItem item in items)
            {
                string fullImageName = item["FileLeafRef"].ToString();
                string thumbnailName = fullImageName.Replace('.', '_') + ".jpg";

                if (photoUri.Contains(fullImageName))
                {
                    item["StockNo"] = stockNo;
                    item["FullImageUrl"] = photoUri;
                    item["ThumbnailUrl"] = photoUri.Replace(fullImageName, "_t/" + thumbnailName);
                    item.Update();
                    siteContext.ExecuteQuery();
                    break;
                }
            }



```


### Benutzerfreundlichkeit

Für die Anforderungen dieses Szenarios werden viele benutzerdefinierte Steuerelemente implementiert. Den relevanten Code finden Sie in der Datei "UserCode.js". Mit dem LightSwitch-Designer können Sie im Handumdrehen eine einfache Benutzeroberfläche erstellen. Möglicherweise entspricht der übliche Bildschirmsatz Ihren Testanforderungen. Da der Schwerpunkt dieses Dokuments nicht auf der Benutzerfreundlichkeit liegt, werden hier keine detaillierten Informationen bereitgestellt.




## Bereitstellen der App

Führen Sie zum Bereitstellen des Add-Ins die in diesem Abschnitt beschriebenen Schritte durch. Stellen Sie vor dem Bereitstellen des Add-Ins sicher, dass das SAP-Gateway für Microsoft im Azure-Mandanten bereitgestellt und konfiguriert wurde.




### Erstellen der Azure-Website


1. Melden Sie sich beim  [Microsoft Azure-Portal](http://go.microsoft.com/fwlink/?LinkID=512959) als globaler Administrator an.


2. Klicken Sie im linken Menü auf **Websites**.

    > **HINWEIS**
      > Diese Website werden Sie zum Hosten des vom SharePoint-Anbieter gehosteten Add-Ins für SellerDashboard verwenden. 
3. Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Neu**.


4. Wählen Sie **Berechnen > Website > Schnellerfassung**.


5. Geben Sie im Feld **URL**den ersten Teil der URL ein, die Sie verwenden möchten, und klicken Sie dann auf **Website erstellen**. Kopieren Sie diese URL, da Sie sie bei der Registrierung des Add-Ins im späteren Verlauf dieses Artikels benötigen. Verwenden Sie z. B. Contoso.azurewebsites.net.



### Registrieren der SharePoint-App

Mit einem leeren SharePoint-Add-In werden dem SharePoint-Add-In Leseberechtigungen gewährt, die zum Abrufen von Fahrzeugfotos aus der Bildbibliothek in SharePoint Online benötigt werden.




### So registrieren Sie das SharePoint-Add-In


1. Öffnen Sie die Projektmappe im Projektmappen-Explorer.


2. Geben Sie in den Eigenschaften des SharePoint-Add-Ins die **Website-URL** der SharePoint-Website ein, welche die Bildbibliothek enthält. Melden Sie sich in der angezeigten Anmeldeaufforderung als Websitebesitzer an.


3. Legen Sie die Leseberechtigung im Manifest der Anwendung fest.

![Manifest](images/746bcfe7-7f51-4f5d-a072-12c577e5fa2f.jpg)





4. Legen Sie das Add-In als StartUp-Projekt fest, und führen Sie das Projekt aus. Die ClientID- und ClientSecret-Werte finden Sie in der Datei "ContosoMotorsCarInventoryWeb/Web.config". Der Code sollte folgendermaßen aussehen:

  ```XML

<add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6"/>
<add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk="/>
  ```

5. Ersetzen Sie die Werte von ClientID und ClientSecret unter "Gehostete Add-In-Konfiguration" in der Datei "ContosoMotorsCarInventoryWeb/TokenHelper.cs" mit den oben genannten Werten. Der Code sollte folgendermaßen aussehen:

  ```cs

private static readonly string ClientId = "06af1059-8916-4851-a271-2705e8cf53c6";
private static readonly string ClientSecret = "LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=";

  ```


### Registrieren Ihrer Webanwendung bei Azure AD

In den Schritten in diesem Abschnitt wird beschrieben, wie das Beispiel-Add-In über das Azure-Verwaltungsportal registriert wird.




1. Melden Sie sich beim  [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=512959) mit Ihrem Azure-Dienstadministratorkonto oder Co-Administratorkonto an.


2. Klicken Sie im linken Navigationsbereich auf **Active Directory**.


3. Klicken Sie auf der Active Directory-Seite auf das Verzeichnis, das für das SAP-Gateway für Microsoft konfiguriert wurde.

    > **TIPP**
      >  Wenn Sie nicht sicher sind, welches verwendet wurde, schauen Sie im SAP-Gateway nach Microsoft-Administrator. Hinweis: Es ist das Verzeichnis, das die Benutzer und Gruppen für das SAP-Gateway für Microsoft enthält.
4. Wählen Sie auf der oberen Navigationsleiste **ANWENDUNGEN**.


5. Klicken Sie am unteren Rand der Seite auf **Hinzufügen**.


6. Klicken Sie im sich öffnenden Dialogfeld auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.


7. Geben Sie im Dialogfeld **Anwendung hinzufügen** einen Namen für die Anwendung ein, z. B. "SellerDashboard."


8. Wählen Sie als Anwendungstyp **Webanwendung und/oder Web-API**, und klicken Sie dann auf die NACH-RECHTS-TASTE.


9. Verwenden Sie im Dialogfeld **Eigenschaften hinzufügen** die URL der Website, die Sie erstellt haben (im Abschnitt "Erstellen der Azure-Website), als URL für Anmeldung. Verwenden Sie zum Beispiel *https://Contoso.azurewebsites.net*  .


10. Geben Sie unter **APP-ID-URI** einen eindeutigen URI für die Anwendung ein, z. B. die **URL FÜR ANMELDUNG** und Anwendungsname; Beispiel: *https://Contoso.azurewebsites.net/SellerDashboard*  , wobei Contoso.azurewebsites.net die Website ist, die Sie im früheren Verlauf dieses Artikels erstellt haben.


11. Klicken Sie auf das Häkchen, um die Anwendung zu erstellen. 

    Das Azure-Dashboard für die Anwendung wird geöffnet, und es wird eine Erfolgsmeldung angezeigt.



> **HINWEIS**
> Sie müssen das Add-In mit Azure AD zweimal registrieren: Einmal für Debuggingzwecke und dann noch mal zum Bereitstellen für die Produktion, wie in Schritt 10 beschrieben. > Verwenden Sie zum Registrieren des Add-Ins für Debuggingzwecke **URL FÜR ANMELDUNG** und **APP-ID-URI** mit der Debugging-URL des SellerDashboard.Server-Projekts, damit Sie den Visual Studio-Debugger (F5) ausführen können. Diese URL weist das Format https://localhost. *nnnn*  auf, wobei *nnnn*  eine Portnummer ist. Diese URL finden Sie im Bereich "Eigenschaften" in Visual Studio.> Bearbeiten Sie die Registrierung zur Verwendung der richtigen Produktions-URL, wenn Sie für die Bereitstellung für die Produktion bereit sind. 





### Konfigurieren von Anwendungseinstellungen

Konfigurieren Sie die Einstellungen für die neue Anwendung, die Sie im vorherigen Verfahren erstellt haben. 




1. Klicken Sie im oberen Bereich der Anwendungsseite auf **Konfigurieren**.


2. Legen Sie ggf. erforderliche Parameter fest, und notieren Sie sich Konfigurationseinstellungen, wie in der folgenden Tabelle beschrieben.


|**Parameter**|**Aktion**|
|:-----|:-----|
|Client-ID  <br/> |Kopieren Sie den Wert, der im Feld "Client-ID" angezeigt wird.  <br/> |
|Geheimer Clientschlüssel  <br/> | Generieren Sie einen neuen Anwendungsschlüssel: <br/>  Wählen Sie im Abschnitt Schlüssel die Schlüsseldauer 1 oder 2 Jahre. <br/>  Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Speichern**.  <br/>  Der Schlüsselwert wird nun angezeigt. <br/>  Kopieren und speichern Sie den Schlüsselwert für die zukünftige Verwendung. Er kann nicht mehr abgerufen werden, nachdem Sie die Seite verlassen haben. <br/> |
|App-ID-URI  <br/> |Kopieren Sie den Wert, der im Feld **Add-In-ID-URI** angezeigt wird. <br/> |
 

    Denken Sie daran, diese Informationen bereitzuhalten, da Sie sie in einem späteren Verfahren benötigen.


3. Gehen Sie im Abschnitt **Berechtigungen für andere Anwendungen** folgendermaßen vor:

1. Wählen Sie Ihren SAP-Gateway für Microsoft-Anwendung.


2. Aktivieren Sie in der Spalte **Delegierte Berechtigungen** die Felder für die Berechtigungen für das SAP-Gateway für Microsoft, die das vom SharePoint-Anbieter gehostete Add-In für SellerDashboard benötigt.


4. Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Speichern**.



### Ersetzen Sie die erforderliche Konfiguration für die Bereitstellung


1. Suchen Sie in der Datei "SellerDashboardHTMLClient/UserCode.js" nach dem folgenden Codeplatzhalter:

  ```cs

sharePointUrl: "Replace with your SharePoint host site",
// https://fake_domain.sharepoint.com/sites/Developer
SharePointRootUrl: "Replace with your SharePoint root site"
 // https://fake_domain.sharepoint.com/ 

  ```


1. Ersetzen Sie den sharePointUrl-Wert mit Ihrer SharePoint-Website, für die das Add-In installiert wird und die die Bildbibliothek enthält.


2. Ersetzen Sie den SharePointRootUrl-Wert mit der entsprechenden SharePoint-Stammwebsite.


2. Suchen Sie in der Datei "SellerDashboard.Server/Web.config" in der SellerDashboard-Lösung nach dem folgenden Konfigurationsplatzhalter:

  ```XML

<add key="ClientSecret" value="MwMp1yxOyy8BGhfD5d9VvuqlRbhaqWESxVNLzgpYNHU=" />
<add key="ClientId" value="ed138b32-c89d-4f22-b74d-7d9d5044b260" />
<add key="Ida:ClientId" value="Replace with Azure register add-in client id" />
<add key="Ida:ClientSecret" value="Replace with Azure register add-in client secret" />
<add key="Ida:TenantId" value="Replace with the SAP Gateway for Microsoft tenant domain, for example <yourDomain>.onmicrosoft.com" />
<add key="Ida:RedirectUrl" value="Replace with the Azure host site, for example https://contoso.azurewebsites.net/SharePointLaunch.aspx" />
<add key="Ida:ResourceUrl" value="Replace with the SAP Gateway for Microsoft instance, for example http://contoso.cloudapp.net/" />
<add key="Ida:ODataServiceBaseUrl" value="Replace with the SAP OData endpoint url, for example https://contoso.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/" />
<add key="Ida:ODataServiceMetadataUrl" value="Replace with the SAP OData endpoint metadata url, for example https://contoso.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/$metadata" />
<add key="Ida:DataCollection" value="Replace with OData EntitySet name, for example ContosoMotorsCollection" />
<add key="Ida:ODataFeedOrEntryFormat" value="application/atom+xml" />
<add key="Ida:ODataMetadataFormat" value="application/xml" />
<add key="Ida:ODataContentType" value="application/xml" />
<add key="Ida:ODataVersion" value="2.0" />
<add key="Ida:ODataMaxVersion" value="3.0" />
<add key="Ida:ODataWriterTypeName" value="Replace with the OData EntityType, for example ZCAR_POC_SRV.ContosoMotors" />
<add key="Ida:DefaultID" value="1024" />
<add key="Ida:DefaultStockNo" value="2048" />
<add key="Ida:SPPicLib" value="Replace with you picture library name, for example ContosoMotorsPictureLibrary" />
  ```


1. Ersetzen Sie die ClientId- und ClientSecret-Werte mit den Werten, die Sie im vorherigen Verfahren erhalten haben.


2. Ersetzen Sie die Ida:ClientId und Ida:ClientSecret-Werte mit den Werten aus der Azure AD-App.


3. Ersetzen Sie den Ida:TenantId-Wert mit < *IhreDomäne*  >.onmicrosoft.com.

    Wenn Ihr Organisationskonto beispielsweise jemand@< *IhreDomäne*  >.onmicrosoft.com lautet, müssen Sie < *IhreDomäne*  >.onmicrosoft.com eingeben.


4. Ersetzen Sie den Ida:RedirectUrl-Wert mit der in Azure erstellten Testwebsite, die dieses Add-In hostet.


5. Ersetzen Sie den Ida:ResourceUrl-Wert mit dem SAP-Gateway für Microsoft-Instanz, die in Azure bereitgestellt und konfiguriert wurde.


6. Ersetzen Sie den Ida:ODataServiceBaseUrl-Wert mit der OData-Endpunkt-URL für das SAP-Gateway für Microsoft-Instanz.


7. Ersetzen Sie den Ida:ODataServiceMetadataUrl-Wert mit der OData-Endpunktmetadaten-URL für das SAP-Gateway für Microsoft-Instanz.


8. Ersetzen Sie den Ida:DataCollection-Wert. Diesen Wert finden Sie in den Metadaten. Verwenden Sie zum Beispiel ContosoMotorsCollection.


9. Ersetzen Sie den Ida:ODataWriterTypeName-Wert. Den richtigen Wert finden Sie in den Metadaten. 

    Verwenden Sie zum Beispiel Folgendes:



  ```XML

ZCAR_POC_SRV.ContosoMotors

<EntityContainer Name="ZCAR_POC_SRV_Entities" m:IsDefaultEntityContainer="true">
     <EntitySet sap:content-version="1" Name="ContosoMotorsCollection" sap:searchable="true" EntityType="ZCAR_POC_SRV.ContosoMotors"/>
</EntityContainer>
<atom:link xmlns:atom="http://www.w3.org/2005/Atom" href="http://contoso.cloudapp.net:8080/perf/sap/opu/odata/sap/ZCAR_POC_SRV.ContosoMotors"/>
  ```

10. Ersetzen Sie den Ida:SPPicLib-Wert mit dem Namen der Bildbibliothek, die Sie auf der SharePoint-Hostwebsite erstellt haben.



### Veröffentlichen des Projekts


1. Suchen Sie nach dem SellerDashboard-Projekt in der SellerDashboard-Lösung.


2. Klicken Sie mit der rechten Maustaste auf SellerDashboard, um das Projekt zu veröffentlichen.


3. Wählen Sie **Von Anbieter gehostet**.


4. Wählen Sie **Microsoft Azure**.


5. Melden Sie sich als globaler Administrator an.


6. Wählen Sie die Website, die Sie in Azure erstellt haben.


7. Geben Sie an, dass Sie HTTPS verwenden möchten.


8. Verwenden Sie die standardmäßigen Datenverbindungszeichenfolge.


9. Geben Sie die Website ein, die Sie in Azure als Hostwebsite erstellt haben. Geben Sie die ClientId- und ClientSecret-Werte ein, die Sie im vorherigen Verfahren erhalten haben.


10. Veröffentlichen Sie das Projekt.

     Sie erhalten ein veröffentlichtes Paket.



### Installieren der App


1. Melden Sie sich als Websiteadministrator auf Ihrer SharePoint-Bereitstellungsseite an.


2. Klicken Sie auf der Startseite auf **Neue bereitzustellendes Add-In**.


3. Wählen Sie das Paket, das Sie im Rahmen des Veröffentlichungsprozesses erhalten haben.


4. Stellen Sie es nun bereit.



## Externe Codeliste







|**Webquelle**|**Sammlung**|**Codespeicherort**|**Lizenz**|
|:-----|:-----|:-----|:-----|
| [Lernprogramm zum Umfrage-Add-In: Entwickeln einer SharePoint-Anwendung mithilfe von LightSwitch](http://code.msdn.microsoft.com/Survey-App-Tutorial-a70d0afd) <br/>  [Vorgehensweise: Erstellen eines Add-Ins für SharePoint mithilfe von LightSwitch](http://msdn.microsoft.com/de-de/library/jj969621.aspx) <br/> |**Programm zum Hochladen von Fotos:** <br/> PhotoListHelper.cs  <br/> PhotosController.cs  <br/> Global.asax.cs  <br/> | [Lernprogramm zum Umfrage-Add-In: Entwickeln einer SharePoint-Anwendung mithilfe von LightSwitch (C#)](http://www.getcodesamples.com/src/2571E87E/) <br/> |Apache-Lizenz, Version 2.0  <br/> |
|NULL  <br/> |**ACS-Authentifizierung:** <br/> SharePointContext.cs  <br/> TokenHelper.cs  <br/> |**VS - interne Vorlagen:** <br/> Visual C#/Office/SharePoint/Apps/AppforSharePoint  <br/> ||
 
