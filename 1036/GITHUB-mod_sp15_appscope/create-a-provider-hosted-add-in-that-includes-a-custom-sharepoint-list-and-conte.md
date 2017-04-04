---
title: Créer un complément hébergé par un fournisseur comportant un type personnalisé de liste et de contenu SharePoint
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# Créer un complément hébergé par un fournisseur comportant un type personnalisé de liste et de contenu SharePoint
Créez un Complément SharePoint qui combine une application web hébergée sur le cloud avec des instances de liste, des types de contenu personnalisés et des modèles de liste personnalisés hébergés sur SharePoint à l'aide de Outils de développement Office pour Visual Studio 2012. Découvrez comment interagir avec des sites web de complément SharePoint 2013 à l'aide du service web REST/OData et comment implémenter OAuth dans un Complément SharePoint.
La plupart des composants SharePoint standard, tels que les types de contenu personnalisés, les définitions de liste personnalisées et les flux de travail, peuvent être inclus dans un Complément SharePoint hébergé sur le cloud. L'exemple simple présenté dans cet article contient les éléments suivants :
  
    
    


- Un site web de complément avec
    
  - quelques colonnes de site personnalisées
    
  
  - un type de contenu personnalisé qui utilise les colonnes personnalisées
    
  
  - un modèle de liste personnalisé qui utilise le type de contenu personnalisé
    
  
  - une instance de liste basée sur la définition de liste personnalisée
    
  
- Une application web ASP.NET qui lit les données à partir de l'instance de liste
    
  

## Conditions requises pour créer ce complément SharePoint
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/fr-fr/download/details.aspx?id=30682) ou version ultérieure.
    
  
-  [Outils de développement Office](https://msdn.microsoft.com/fr-fr/office/aa905340.aspx)
    
  
- Installation de SharePoint 2013 pour les tests et le débogage
    
  - Cette installation peut se trouver sur l'ordinateur de développement ou vous pouvez procéder au développement avec une installation SharePoint 2013 distante. Si vous utilisez une installation distante, vous devez installer le composant redistribuable du modèle objet client sur l'installation cible. Il est disponible sous la forme d'un package redistribuable dans le Centre de téléchargement Microsoft. Recherchez « SharePoint Server 2013 Client Components SDK » ou « SharePoint Online Client Components SDK ».
    
  
  - Le site web SharePoint de test doit être créé à partir de la définition de site **Site du développeur** (que vous pouvez créer dans l'Administration centrale).
    
  
  - Votre application web distante communique avec le site web de complément à l'aide de JavaScript et de la  [bibliothèque inter-domaines](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md) ou [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md). Si OAuth est utilisé, comme c'est le cas dans l'exemple de cet article, l'installation de SharePoint 2013 doit être configurée pour utiliser OAuth.
    
  

> [!REMARQUE]
> Pour obtenir des instructions sur la manière de configurer un environnement de développement adapté à vos besoins, voir  [Commencer à créer des applications pour Office et SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 
  
    
    


### Concepts fondamentaux à connaître pour créer un complément

Avant de créer votre premier complément, vous devez comprendre ce que sont les Compléments SharePoint et connaître les différences entre des Compléments SharePoint hébergés sur le cloud et les mêmes compléments hébergés sur SharePoint. Les articles présentés dans le tableau 1 vous aideront à acquérir ces connaissances.
  
    
    

**Tableau 1. Concepts fondamentaux pour créer un complément**


|**Titre de l'article**|**Description**|
|:-----|:-----|
| [Compléments](sharepoint-add-ins.md) <br/> |Découvrez le nouveau modèle de complément de SharePoint 2013 qui permet de créer des compléments, c'est-à-dire de petites solutions simples d'utilisation pour les utilisateurs finaux.  <br/> |
| [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |Découvrez des aspects de l'architecture des Compléments SharePoint et du modèle de complément SharePoint, notamment les options d'hébergement des compléments, les options d'interface utilisateur (IU), le système de déploiement, le système de sécurité et le cycle de vie.  <br/> |
| [Choisir les modèles de développement et l'hébergement d'un complément pour SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Découvrez les différentes méthodes d'hébergement pour les Compléments SharePoint.  <br/> |
   

## Développement du complément SharePoint
<a name="Develop"> </a>

Dans les procédures de cette section, vous créez un Complément SharePoint qui comprend un site web de complément avec des composants SharePoint et une application web distante sur l'ordinateur de développement.
  
    
    

### Pour configurer la solution Visual Studio 2012 et ses éléments


1. Dans Visual Studio 2012, créez un projet **Complément pour SharePoint 2013** à partir du nœud **Office SharePoint | Compléments** (sous **C#** ou **Visual Basic**) dans l'arborescence des modèles de l'Assistant **Nouveau projet**. Sélectionnez l'option d'hébergement **Hébergé par un fournisseur**. Dans l'exemple de cet article, C# est utilisé comme langage, et LocalTheater (ThéâtreLocal) est le nom du projet.
    
  
2. Cliquez sur **Terminer** dans l'Assistant.
    
  
3. Ouvrez le fichier AppManifest.xml dans le concepteur de manifeste. L'élément **Title** a le nom du projet comme valeur par défaut. Remplacez-le par un nom plus convivial, car il s'agit du nom du complément qui sera visible par les utilisateurs dans l'interface utilisateur.
    
  
4. Spécifiez un **Name** pour le complément. Il s'agit du nom interne qui doit contenir uniquement des caractères ASCII sans espace (LocalTheater, par exemple).
    
  
5. Ouvrez le fichier Web.config dans le projet d'application web, puis ajoutez  `<customErrors mode="Off"/>` à l'élément **system.web**.
    
  
6. Vérifiez que le projet d'application web comporte les références aux assemblys suivants. (Si votre édition de Visual Studio 2012 n'a pas ajouté les références automatiquement, faites-le maintenant.)
    
  - **Microsoft.IdentityModel.dll** Cet assembly est installé dans le Global Assembly Cache avec Windows Identity Foundation (WIF). Comme il s'agit d'un assembly .NET Framework 3.5, il n'apparaît pas par défaut dans le nœud **Framework** de la boîte de dialogue **Ajouter une référence**. Vous pouvez ajouter une référence à cet assembly en accédant directement au répertoire  `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` sur l'ordinateur de développement.
    
  
  - **Microsoft.IdentityModel.Extensions.dll** Vous pouvez ajouter une référence à cet assembly en accédant directement au dossier `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` sur l'ordinateur de développement.
    
  
  - **System.IdentityModel.dll** Cet assembly fait partie de .NET Framework 4. Il apparaît dans le nœud **Assemblys | Framework** de la boîte de dialogue **Ajouter une référence**.
    
  
7. Si votre application web distante accède aux informations du site web hôte et du site web de complément, vous devez ajouter un élément **AppPermissionRequests**, avec un ou plusieurs éléments **AppPermissionRequest** enfants, au fichier AppManifest.xml. (L'application web de l'exemple de cet article accède uniquement au site web de complément. Les principaux du complément disposent automatiquement de toutes les autorisations nécessaires sur le site web de complément. Pour cette raison, le fichier AppManifest.xml de l'exemple ne contient pas d'élément **AppPermissionRequests**.) Pour plus d'informations sur les demandes d'autorisation du complément et sur la façon de les ajouter, voir  [Autorisations des compléments dans SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
    
  

### Pour ajouter les composants SharePoint


1. Vous ajoutez des composants SharePoint à un complément comme vous le feriez pour une solution de batterie de serveurs standard. Il n'est toutefois pas possible d'inclure tous les types de composants SharePoint dans un Complément SharePoint. Les composants jouent leur rôle d'une façon différente dans les Compléments SharePoint. Pour plus d'informations sur les types de composants SharePoint qui peuvent être inclus dans un Complément SharePoint et sur la façon de les inclure dans un projet, voir  [Types de composants SharePoint pouvant se trouver dans un complément SharePoint](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).
    
    Pour les besoins de cet exemple, suivez les procédures ci-après. Elles indiquent comment utiliser Visual Studio 2012 pour ajouter des colonnes, des types de contenu et des modèles de liste personnalisés, ainsi que des instances de liste à un Complément SharePoint.
    
### Pour créer les types de colonnes personnalisés


1. Dans l' **Explorateur de solutions**, ajoutez un élément **Colonne de site** SharePoint portant le nomActor (Acteur) au projet de Complément SharePoint.
    
  
2. Dans le fichier elements.xml de la nouvelle colonne de site, modifiez l'élément **Field** pour qu'il possède les attributs et valeurs de l'exemple suivant. Vous ne devez toutefois pas changer le GUID de l'attribut **ID**; conservez la valeur Visual Studio 2012 générée pour celui-ci. N'oubliez pas d'utiliser des accolades « {} ».
    
  ```
  
<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
  ```

3. Ajoutez une autre **Colonne de site** appeléeCastingStatus (ÉtatCasting) au projet.
    
  
4. Dans le fichier elements.xml de la nouvelle colonne de site, modifiez l'élément **Field** pour qu'il possède les attributs et valeurs de l'exemple suivant. Vous ne devez toutefois pas changer le GUID de l'attribut **ID**; conservez la valeur Visual Studio 2012 générée pour celui-ci.
    
  ```
  
<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
  ```

5. Comme il s'agit d'un champ de choix, vous devez spécifier les choix possibles, l'ordre dans lequel ils doivent apparaître dans la liste déroulante lorsqu'un utilisateur effectue une sélection et le choix par défaut. Ajoutez le balisage enfant suivant à l'élément **Field**.
    
  ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```


### Pour créer le type de contenu personnalisé


1. Dans l' **Explorateur de solutions**, ajoutez un élément **Type de contenu** SharePoint au projet de Complément SharePoint en lui donnant le nomRôle. Lorsque l'Assistant vous demande de sélectionner le type de contenu de base, choisissez **Élément**, puis cliquez sur **Terminer**.
    
  
2. Si le concepteur de type de contenu ne s'ouvre pas automatiquement, sélectionnez le type de contenu **ActingRole** (Rôle) dans l' **Explorateur de solutions** pour l'ouvrir.
    
  
3. Cliquez sur l'onglet **Type de contenu** du concepteur, puis remplissez les zones de texte de la manière suivante :
    
  - **Nom du type de contenu**:ActingRole (Rôle)
    
  
  - **Description**:Represents a role in a play or movie. (Représente un rôle dans une pièce de théâtre ou un film.)
    
  
  - **Nom du groupe**:Theater and Movies Théâtre et films
    
  
4. Vérifiez qu' *aucune*  des cases à cocher de l'onglet n'est activée. La case à cocher **Hériter des colonnes du type de contenu parent** peut être activée par défaut. *Veillez à la désactiver.* 
    
  
5. Cliquez sur l'onglet **Colonnes** du concepteur.
    
  
6. À l'aide de la grille, ajoutez les deux colonnes de site au type de contenu. Elles sont répertoriées dans la liste déroulante par nom d'affichage : **Actor/Actress** et **CastingStatus**. (Si elles ne sont pas répertoriées, cela signifie que vous n'avez peut-être pas enregistré le projet depuis l'ajout des colonnes de site personnalisées. Cliquez sur **Enregistrer tout**.)
    
  
7. Enregistrez le fichier, puis fermez le concepteur.
    
  
8. À l'étape suivante, vous devez utiliser directement le code XML brut pour le type de contenu. Dans l' **Explorateur de solutions**, sélectionnez donc l'enfant du fichier elements.xml du type de contenu **ActingRole**.
    
  
9. Des éléments **FieldRef** existent déjà dans le fichier pour les deux colonnes que vous avez ajoutées. Ajoutez des éléments **FieldRef** pour deux colonnes SharePoint 2013 intégrées comme paires des deux qui figurent déjà dans le fichier. Le code suivant correspond au balisage pour les éléments. *Vous devez utiliser ces mêmes GUID pour l'attribut d'ID, car ce sont des types de champs intégrés avec des ID fixes.*  Ajoutez-les *au-dessus*  des deux éléments **FieldRef** pour les colonnes de site personnalisées.
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  ```


    Notez que le nom d'affichage personnalisé suivant a été donné à ces champs : **Character** (Personnage).
    
  

### Pour créer le modèle de liste personnalisé et une instance de liste


1. Ajoutez un élément **Liste** SharePoint au projet de Complément SharePoint en lui donnant le nomCharactersInShow (PersonnagesSpectacle). Dans la page **Choisir les paramètres de liste** de l' **Assistant Personnalisation de SharePoint**, conservez le nom d'affichage **CharactersInShow** comme valeur par défaut, cliquez sur la case d'option **Créer une liste personnalisable basée sur**, puis sélectionnez **Par défaut (vide)** dans la liste déroulante. Enfin, cliquez sur **Terminer**.
    
  
2. Une fois l'exécution de l'Assistant terminée, un modèle de liste **CharactersInShow** est créé avec une instance de liste enfant appelée **CharactersInShowInstance** (InstancePersonnagesSpectacle). Un concepteur de liste l'a peut-être ouvert par défaut. Elle sera utilisée ultérieurement.
    
  
3. Ouvrez l'enfant du fichier elements.xml du modèle de liste **CharactersInShow** (et *non*  l'enfant du fichier elements.xml de **CharactersInShowInstance**).
    
  
4. Ajoutez des espaces à l'attribut **DisplayName** pour le rendre plus convivial :"Characters In Show" (« Personnages du spectacle »).
    
  
5. Définissez l'attribut **Description** sur"The characters in a play or movie." (Les personnages d'une pièce de théâtre ou d'un film)
    
  
6. Conservez les valeurs par défaut de tous les autres attributs, enregistrez le fichier, puis fermez-le.
    
  
7. Si le concepteur de liste n'est pas ouvert, sélectionnez le nœud **CharactersInShow** dans l' **Explorateur de solutions**.
    
  
8. Cliquez sur l'onglet **Colonnes** du concepteur, puis cliquez sur le bouton **Types de contenu**.
    
  
9. Dans la boîte de dialogue **Paramètres de type de contenu**, ajoutez le type de contenu **ActingRole**.
    
  
10. Sélectionnez le type de contenu **ActingRole** dans la liste des types, puis cliquez sur le bouton **Définir par défaut**.
    
    Sélectionnez le type de contenu **Élément**, cliquez avec le bouton droit sur la petite pointe de flèche qui apparaît à gauche du nom du type de contenu, puis sélectionnez **Supprimer**.
    
  
11. Répétez la procédure ci-dessus pour le type de contenu **Dossier** pour que **ActingRole** soit le seul type de contenu répertorié. Cliquez sur **OK** pour fermer la boîte de dialogue.
    
  
12. Trois colonnes se trouvent désormais dans la liste des colonnes. Sélectionnez le type de contenu **Titre**, cliquez avec le bouton droit sur la petite pointe de flèche qui apparaît à gauche du nom du type de contenu, puis sélectionnez **Supprimer**. Seules les deux colonnes suivantes doivent être à présent répertoriées : **Actor/Actress** et **Casting Status**.
    
  
13. Cliquez sur l'onglet **Liste** du concepteur. Cet onglet est utilisé pour définir certaines valeurs pour l' *instance*  de liste et non le *modèle*  de liste.
    
  
14. Remplacez les valeurs de cet onglet par celles-ci :
    
  - **Titre**:Characters in Hamlet (Personnages dans Hamlet)
    
  
  - **URL de la liste**:Lists/CharactersInHamlet (Listes/PersonnagesDansHamlet)
    
  
  - **Description**:The characters in Hamlet and casting information. (Personnages dans Hamlet et informations sur le casting)
    
  

    Conservez l'état par défaut des cases à cocher, enregistrez le fichier, puis fermez le concepteur.
    
  
15. L'instance de liste peut porter son ancien nom dans l' **Explorateur de solutions**. Si c'est le cas, ouvrez le menu contextuel de **CharactersInShowInstance**, sélectionnez **Renommer**, puis remplacez le nom par CharactersInHamlet.
    
  
16. Ouvrez le fichier schema.xml.
    
  
17. Le fichier peut contenir deux éléments **ContentType**: un avec une valeur d'attribut **Name** deActingRole et un autre appelé **ListFieldsContentType**. Seul l'élément appelé ActingRole doit être conservé (supprimez les autres éléments **ContentType**).
    
    > [!REMARQUE]
      > Des sauts de ligne ne séparent peut-être pas les éléments **ContentType**. Il semble dans ce cas qu'il n'y en ait qu'un. Faites défiler le fichier vers la droite pour en rechercher d'autres. 
18. L'élément **Fields** doit posséder deux éléments **Field** (qui se trouvent sur une seule ligne s'il n'existe pas de saut de ligne). Un élément doit dupliquer exactement l'élément **Field** dans le fichier elements.xml de la colonne de site **Actor**. L'autre élément doit dupliquer exactement l'élément **Field** du fichier elements.xml de la colonne de site **CastingStatus**. S'il n'y a pas de correspondance exacte, y compris tous les éléments enfants (comme les éléments **CHOICES** et **MAPPINGS**), copiez l'élément **Field** dans le fichier elements.xml de la colonne de site et collez-le à la place de l'élément **Field** qui ne correspond pas dans le fichier schema.xml.
    
  
19. Dans le fichier schema.xml, dans l'élément **View** dont la valeur BaseViewID est définie sur 0, remplacez l'élément **ViewFields** existant par le balisage ci-après. (Utilisez précisément ce GUID pour le **FieldRef** appelé `LinkTitle`.)
    
  ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
  ```

20. Remplacez les deux valeurs d'attribut d'ID manquantes par les GUID dans les fichiers elements.xml des colonnes de site respectives. N'oubliez pas d'utiliser des accolades « {} ».
    
  
21. Dans le fichier schema.xml, dans l'élément **View** dont la valeur BaseViewID est définie sur 1, remplacez l'élément **ViewFields** existant par le code ci-dessous. (Utilisez précisément ce GUID pour le **FieldRef** appelé `LinkTitle`.)
    
  ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
  ```

22. Copiez les deux éléments **FieldRef** pour `Actor` et `CastingStatus` que vous avez ajoutés à l'affichage précédent de cet élément **ViewFields** comme frères de `LinkTitle` **FieldRef**.
    
  
23. Enregistrez le fichier schema.xml, puis fermez-le.
    
  
24. Ouvrez le fichier elements.xml qui est un enfant de l'instance de liste **CharactersInHamlet**.
    
  
25. Remplissez la liste avec quelques données initiales en ajoutant le balisage ci-après comme enfant de l'élément **ListInstance**.
    
  ```
  
<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
  ```

2. Dans l' **Explorateur de solutions**, sélectionnez **Fonctionnalité1** pour ouvrir le concepteur de fonctionnalité. Dans le concepteur, définissez le **Titre** surTheater and Movie Data Components (Composants de données Théâtre et Cinéma) et la **Description** surSite columns, content types, and list instances for data about theater and movies. (Colonnes de site, types de contenu et instances de liste pour les données sur le théâtre et les films). Enregistrez le fichier, puis fermez le concepteur.
    
  
3. Dans l' **Explorateur de solutions**, si **Fonctionnalité1** n'a pas été renommé, ouvrez son menu contextuel, sélectionnez **Renommer**, puis attribuez le nom TheaterAndMovieDataComponents.
    
  

### Pour écrire le code du projet d'application web distante


- Développez l'application web comme vous le feriez pour n'importe quelle autre application web destinée à votre pile de plateformes préférée. Pour une pile Microsoft, vous pouvez utiliser le service web REST/OData ou un des modèles objet client de SharePoint 2013. Pour une pile autre que Microsoft, vous pouvez avoir recours aux points de terminaison REST/OData de SharePoint 2013 pour effectuer des opérations CRUD (create/read/update/delete ; créer/lire/mettre à jour/supprimer) sur les données du site web de complément.
    
    > [!REMARQUE]
      > Lorsque vous ajoutez une référence à un assembly dans votre projet d'application web dans Visual Studio, définissez la propriété **Copie locale** de l'assembly sur **True**, sauf si vous savez que l'assembly est déjà installé sur le serveur web ou que vous pouvez vous assurer qu'il sera installé avant le déploiement de votre complément. .NET Framework est installé sur les rôles web Microsoft Azure et les Sites web Azure. Toutefois, les assemblies clients SharePoint 2013 et les différentes extensions de code managé et instances Microsoft Foundation ne sont pas installés. Les Outils de développement Office pour Visual Studio 2012 ajoutent automatiquement les références à certains assemblies fréquemment utilisés dans les Compléments SharePoint et définissent la propriété **Copie locale**. 

    Pour l'exemple de cet article, vous devez développer une application web ASP.NET. Suivez la procédure ci-après.
    
1. Ouvrez le fichier Default.aspx et remplacez l'élément body du fichier par le balisage ci-après. Il ajoute un bouton **Get the Cast** (Obtenir le casting) qui, lorsque vous cliquez dessus, lit la liste **Characters in Hamlet** (Personnages d'Hamlet) qui figure sur le site web de complément, puis présente les données dans un contrôle [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) qui s'affiche que lorsque vous appuyez sur le bouton.
    
  ```HTML
  
<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
  ```

2. Ouvrez le fichier Default.aspx.cs, puis ajoutez-y les instructions **using** ci-dessous.
    
  ```cs
  
using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
  ```


    La dernière de ces instructions fait référence à l'espace de noms déclaré dans le fichier TokenHelper.cs.
    
  
3. Ajoutez les champs ci-dessous à la classe **Default**.
    
  ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
  ```

4. Remplacez la méthode **Page_Load** par le code suivant qui utilise la classe **TokenHelper** pour obtenir des jetons du serveur de jetons sécurisés compatibles OAuth. Le jeton d'accès est ensuite stocké dans la propriété [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) du bouton pour une récupération ultérieure par le gestionnaire d'événements Click du bouton.
    
  ```cs
  
protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;
        
        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
  ```

5. Ajoutez le gestionnaire d'événements ci-dessous à la classe **Default**. Le gestionnaire commence à récupérer le jeton d'accès qui a été stocké dans la propriété  [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) du bouton.
    
  ```cs
  
protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
  ```

6. Comme le gestionnaire doit obtenir de nouveau l'URL du site web de complément au moment des publications (postback), ajoutez le code ci-après.
    
  ```cs
  
if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
  ```

7. Ajoutez la ligne ci-dessous qui utilise les points de terminaison REST/OData SharePoint 2013 pour obtenir les données de liste. Dans cet exemple, le code lit la liste **Personnages d'Hamlet** qui est déployée sur le site web de complément. Les API de ce service permettent, à l'aide d'une seule ligne de code, de sélectionner facilement une liste et de spécifier trois champs à renvoyer de la liste. Dans l'URL d'OData, notez que vous devez utiliser les noms internes des champs (colonnes) plutôt que les noms d'affichage. Le code utilise donc `Title`,  `Actor` et `CastingStatus` à la place de `Character`,  `Actor/Actress` et `Casting Status.`. Pour plus d'informations sur le service web REST/OData, voir  [Utiliser les opérations de requête OData dans les demandes REST SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md).
    
  ```cs
  
// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
  ```

8. Ajoutez le code ci-dessous qui utilise les classes  [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) et [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) de l'espace de noms [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) pour construire les objets de requête et réponse HTTP.
    
  ```cs
  
// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
  ```

9. Ajoutez le code ci-dessous pour analyser le code XML de réponse au format ATOM. Il utilise les classes de l'espace de noms  [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) pour analyser les données renvoyées et construire un [List<T>](http://msdn2.microsoft.com/FR-FR/library/6sh2ey19) des éléments à partir de la liste SharePoint. (Vous pouvez également utiliser les classes de l'espace de noms [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) .) Dans le code XML renvoyé par SharePoint, notez que les éléments enfants de l'élément **entry** contiennent des métadonnées sur l'élément de liste. Les données de ligne actuelles d'un élément de liste SharePoint sont imbriquées deux couches plus bas dans l'élément **properties**. Pour cette raison, la méthode d'extension  [Elements<T>](http://msdn2.microsoft.com/FR-FR/library/bb348465) est utilisée deux fois pour filtrer les niveaux supérieurs.
    
  ```cs
  
// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
  ```

10. Ajoutez la requête LINQ ci-dessous pour construire une collection  [IEnumerable<T>](http://msdn2.microsoft.com/FR-FR/library/9eekhta0) d'un type anonyme possédant les propriétés dont vous avez uniquement besoin. Bien que le code doive faire référence au champ de titre de l'élément par son nom interne `Title`, le nom de la propriété du type anonyme, à laquelle la propriété est affectée, peut être nommé  `Character`. Ainsi, lorsque la collection est liée à un contrôle de grille, le nom plus approprié **Character** (Caractère) apparaît dans la page.
    
  ```cs
  
var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

  ```

11. Terminez le gestionnaire avec le code ci-dessous pour lier les données à un contrôle  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) de la page. Les en-têtes de colonne de la grille ont par défaut les noms des propriétés du type anonyme : `Character`,  `Actor` et `CastingStatus`. Le contrôle  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) possède des propriétés qui permettent de contrôler le nom et la mise en forme des en-têtes de colonne. Vous pouvez donc avoir **Actor/Actress** (Acteur/actrice) et **Casting Status** (État du casting) pour correspondre aux en-têtes de colonne dans SharePoint. Pour des raisons de simplicité, ces techniques ne sont pas décrites dans cet article. (Vous pouvez également utiliser un contrôle [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) .)
    
  ```cs
  
GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

  ```

12. Enregistrez tous les fichiers.
    
  

### Pour tester et déboguer le complément SharePoint


1. Pour tester le Complément SharePoint et l'application web distante, appuyez sur la touche F5 dans Visual Studio 2012. L'application web est déployée sur IIS Express sur localhost. Le Complément SharePoint est installé sur le site web SharePoint cible. (Dans l'exemple, le complément distant  *ne tente pas*  d'interagir avec le site web *hôte*  , et le principal du complément dispose automatiquement des autorisations sur le site web de *complément*  , de sorte que vous *ne soyez pas*  invité à accorder des autorisations.) La page **Contenu du site** du site web SharePoint cible s'ouvre ; le nouveau complément y est répertorié.
    
  
2. Sélectionnez le Complément SharePoint. L'application web distante s'ouvre alors à la page spécifiée dans l'élément **StartPage** du fichier AppManifest.xml. Utilisez l'application web pour vérifier qu'elle fonctionne correctement. Dans l'exemple de cette rubrique, sélectionnez le bouton. Vous créez ainsi une grille qui est renseignée par la liste **Characters in Hamlet** (Personnages d'Hamlet) du site web de complément.
    
  

## Publication du complément SharePoint
<a name="Publish"> </a>

Pour publier le Complément SharePoint, téléchargez le package du complément vers un catalogue de compléments d'entreprise ou sur Office Store. Pour plus d'informations, voir  [Publication sur le Office Store ou sur le catalogue de compléments d'une organisation](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog) et [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md).
  
    
    

## Résolution des problèmes
<a name="Troubleshooting"> </a>

Si le complément ne fonctionne pas, vous devez vérifier si une erreur dans la balise CAML bloque le déploiement des composants SharePoint. Pour vérifier le déploiement, suivez une procédure similaire à celle qui suit, utilisée pour l'exemple de cet article.
  
    
    

### Pour tester la mise en service du site web de complément


1. Ouvrez la page **Paramètres du site** du site web hôte. Dans la section **Administration de la collection de sites**, cliquez sur le lien **Hiérarchie de site**.
    
  
2. Dans la page **Hiérarchie de site**, le complément est répertorié par son URL. Ne le lancez pas. Copiez plutôt l'URL et utilisez-la dans les étapes restantes.
    
  
3. Accédez à  _URL_of_app_web_/_layouts/15/ManageFeatures.aspx, puis, sur la page **Fonctionnalités du site** qui s'ouvre, vérifiez que la fonctionnalité **Composants de données Théâtre et Cinéma** se trouve dans la liste alphabétique des fonctionnalités de votre Complément SharePoint et que son état est **Actif**.
    
  
4. Naviguez vers  _URL_sous_site_web_application_/_layouts/15/mngfield.aspx, puis, dans la page **Colonnes de site** qui s'ouvre, vérifiez que le groupe **Theater and Movies** se trouve dans la liste des colonnes de site et qu'il contient les nouvelles colonnes de site personnalisées **Actor/Actress** et **Casting Status**.
    
  
5. Naviguez vers  _URL_sous_site_web_application_/_layouts/15/mngctype.aspx, puis, dans la page **Types de contenu de site** qui s'ouvre, vérifiez que le groupe **Theater and Movies** se trouve dans la liste des types de contenu et qu'il contient votre nouveau type de contenu **ActingRole**.
    
  
6. Cliquez sur le lien vers le type de contenu **ActingRole**. Dans la page **Types de contenu de site** qui s'ouvre, vérifiez que le type de contenu comporte les deux nouveaux types de colonnes de site **Actor/Actress** et **Casting Status**. Vérifiez également que le champ de titre de l'élément porte le nom d'affichage personnalisé suivant : **Character**.
    
  
7. Naviguez vers  _URL_sous_site_web_application_/_layouts/15/mcontent.aspx, puis, dans la page **Bibliothèques et listes du site** qui s'ouvre, vérifiez qu'il existe un lien **Personnaliser « Characters in Hamlet »**.
    
  
8. Cliquez sur le lien **Personnaliser « Characters in Hamlet »**, puis, dans la page des paramètres de la liste, vérifiez que le seul type de contenu de la liste est votre type de contenu **ActingRole** personnalisé. Vérifiez également que les deux nouvelles colonnes de sites **Actor/Actress** et **Casting Status** sont répertoriées dans la section **Colonnes**. (La colonne Titre peut apparaître sous le nom interne **Titre** au lieu du nom d'affichage **Character** que vous lui avez attribué.)
    
    > [!REMARQUE]
      > S'il n'existe aucune section **Types de contenu** dans la page, vous devez activer la gestion des types de contenu. Cliquez sur le lien **Paramètres avancés**, puis dans la page **Paramètres avancés**, activez la gestion des types de contenu et cliquez sur **OK**. Vous êtes renvoyé vers la page précédente où vous voyez désormais une section **Types de contenu** comportant une liste.
9. Dans la partie supérieure de la page se trouve l' **Adresse web** de la liste. Copiez-la et collez-la dans la barre d'adresses de votre navigateur, puis accédez à la liste. Vérifiez que la liste comporte les exemples d'éléments que vous avez créés. (La colonne Titre peut apparaître sous le nom interne **Titre** au lieu du nom d'affichage **Character** que vous lui avez attribué.)
    
  

## Étapes suivantes
<a name="NextSteps"> </a>

Cet article a montré comment créer un Complément SharePoint hybride simple avec une application web distante. Pour les étapes suivantes, envisagez les tâches suivantes :
  
    
    

- Ajout d'une fonctionnalité CRUD complète au complément avec les points de terminaison REST/OData ou un des modèles objet client. Pour plus d'informations, voir  [Utiliser les opérations de requête OData dans les demandes REST SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md) et [Effectuer des opérations de base avec du code de bibliothèque client SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md).
    
  
- Localisation du Complément SharePoint dans d'autres langues. Pour plus d'informations, voir  [Localiser les compléments pour SharePoint](localize-sharepoint-add-ins.md).
    
  
- Création d'un complément pour Windows Phone qui duplique les fonctionnalités de l'application web distante. Pour plus d'informations, voir  [Créer des compléments mobiles accédant à SharePoint 2013](http://msdn.microsoft.com/fr-fr/library/office/jj163228.aspx).
    
  

## Ressources supplémentaires
<a name="SP15createcloud_bk_addlresources"> </a>


-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

