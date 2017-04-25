---
title: Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur
ms.prod: SHAREPOINT
ms.assetid: 58932389-0100-47ee-9d33-1b4321d3f462
---


# Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur
 Découvrez comment inclure un bouton de ruban personnalisé à un Complément SharePoint hébergé par un fournisseur.
 Cet article est le troisième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par un fournisseur. Familiarisez-vous tout d'abord avec [Compléments](sharepoint-add-ins.md) et les articles précédents de cette série :





-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [ Procédure pour donner à votre complément hébergé par un fournisseur l'apparence de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)



> **REMARQUE**
>  Si vous avez suivi cette série sur les compléments hébergés par un fournisseur, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) et ouvrir le fichier BeforeRibbonButton.sln.




 Un Complément SharePoint peut inclure desactions personnalisées, qui est le terme de SharePoint pour désigner des boutons de ruban ou des éléments de menu personnalisés. Dans cet article, vous allez apprendre à créer un bouton personnalisé qui synchronise une liste SharePoint avec une base de données distante.
## Création d'une liste personnalisée sur le site web hôte

 Le bouton personnalisé apparaîtra sur le ruban d'une liste spécifique qui enregistre les employés du magasin local. Dans un autre article de cette série, vous apprendrez comment ajouter par programmation une liste personnalisée à un site web hôte, mais pour l'instant vous allez en ajouter une manuellement.




1.  À partir de la page d'accueil du magasin Fabrikam Hong Kong, accédez à **Contenu du site | Ajouter un complément | Liste personnalisée**. 


2.  Dans la boîte de dialogue **Ajout d'une liste personnalisée**, indiquez Employés locaux comme nom et appuyez sur **Créer**. 


3.  Sur la page **Contenu du site**, ouvrez la liste **Employés locaux**.


4.  Ouvrez l'onglet **Liste** sur le ruban, puis cliquez sur le bouton **Paramètres de liste**.


5.  Dans la section **Colonnes** de la page **Paramètres de liste**, cliquez sur la colonne **Titre**. 


6.  Dans le formulaire **Modifier la colonne**, modifiez le **nom de la colonne** (Titre) enNom, puis cliquez sur **OK**.


7.  Sur la page **Paramètres**, cliquez sur **Créer une colonne**.


8.  Dans le formulaire **Créer une colonne**, procédez comme suit :

1.  SaisissezAjouté à la base de données d'entreprise pour le **nom de la colonne**.


2.  Définissez le type sur **Oui/Non (case à cocher)**.


3.  Définissez la **valeur par défaut** sur **Non**.


4.  Cliquez sur **OK**. Vous êtes redirigé vers la page **Paramètres**.


9.  Cliquez sur **Contenu du site** pour ouvrir la page **Contenu du site**. La mosaïque de la nouvelle liste se trouve ici. Ouvrez-la.


10.  Cliquez sur **nouvel élément** et sur le formulaire de création d'élément, saisissez un nom, mais n'activez *pas*  la case **Ajouté à la base de données d'entreprise**. Ensuite, cliquez sur **Enregistrer**. La liste doit se présenter comme suit :

!\[Liste des employés locaux comportant un seul élément : Christian Cartier. La valeur de la colonne « Ajouté à la base de données de l'entreprise » est « Non ».](images/a3371859-e42f-49ea-8f17-48d8a248b075.PNG)






## Ajout du bouton personnalisé

 Dans cette section, vous allez ajouter au complément du balisage qui déploie un bouton sur le ruban de la liste. Quand un utilisateur met en surbrillance un employé sur la liste et clique sur le bouton, le nom de l'employé est ajouté à la base de données d'entreprise et le champ **Ajouté à la base de données d'entreprise** correspondant à l'employé est défini sur Oui.




1.  *Si Visual Studio est ouvert, vous devez le fermer*  et rouvrir la solution Chain Store afin que Visual Studio puisse découvrir votre nouvelle liste. (Exécutez Visual Studio en tant qu'administrateur.)

    > **REMARQUE**
      >  Les paramètres des projets de démarrage dans Visual Studio ont tendance à revenir aux valeurs par défaut à chaque fois que la solution est rouverte. Veillez à toujours suivre les étapes ci-dessous immédiatement après la réouverture de la solution d'exemple de cette série d'articles :>  Cliquez avec le bouton droit sur le nœud de la solution en haut de l' **Explorateur de solutions** et sélectionnez **Définir les projets de démarrage**. >  Assurez-vous que les trois projets sont définis sur **Début** dans la colonne **Action**. 
2.  Cliquez avec le bouton droit sur le projet **ChainStore** dans l' **Explorateur de solutions** et choisissez **Ajouter | Nouvel élément**. 


3.  Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Action personnalisée de ruban**, donnez-lui le nom AjouterEmployéBDEntreprise, puis cliquez sur **Ajouter**.


4.  La boîte de dialogue qui apparaît pose trois questions. Indiquez les réponses suivantes :


|**Question**|**Réponse**|
|:-----|:-----|
|**Où souhaitez-vous exposer l'action personnalisée ?** <br/> |Site web hôte <br/> |
|**Sur quoi l'action personnalisée doit-elle porter ?** <br/> |Instance de liste <br/> |
|**Sur quel élément particulier l'action personnalisée doit-elle porter ?** <br/> |Employés locaux <br/> |
 
5.  Cliquez sur **Suivant**. Vous obtenez trois questions supplémentaires :


|**Question**|**Réponse**|
|:-----|:-----|
|**Où le contrôle se trouve-t-il ?** <br/> |Ribbon.ListItem.Actions <br/> |
|**Quel est le texte d'étiquette du contrôle de bouton ?** <br/> |Ajouter à la base de données d'entreprise <br/> |
|**Vers quoi le contrôle de bouton navigue-t-il ?** <br/> |ChainStoreWeb\\Pages\\EmployeeAdder.aspx (Il s'agit d'une page dont le code-behind ajoute l'employé à la base de données.) <br/> |
 
6. Cliquez sur **Terminer**.

     Un fichier elements.xml qui définit l'action personnalisée est ajouté au projet et ouvert. Vous pouvez en grande partie traiter ce fichier comme une boîte noire et vous n'aurez pas besoin de le modifier jusqu'à un autre article de cette série. Pour l'instant, notez uniquement ce qui suit :

  -  L'attribut **Location** de l'élément **CommandUIDefinition** a la valeur `Ribbon.ListItem.Actions.Controls_children`. La deuxième partie de cette valeur,  `ListItem`, identifie l'onglet du ruban où sera placé le bouton (il est toutefois possible que cela ne corresponde pas au nom exact du nom de l'onglet) et la troisième partie,  `Actions`, est le nom de la section du ruban où sera placé le bouton.


  -  L'attribut **CommandAction** de l'élément **CommandUIHandler** commence par l'espace réservé `~remoteAppUrl`. Cet espace est remplacé par l'URL de l'application web distante lorsque le bouton est déployé.


  -  Plusieurs paramètres de requête ont été ajoutés à la valeur **CommandAction** avec des valeurs d'espace réservé entre des accolades « {} ». Ces espaces réservés sont résolus lors de l'exécution. Notez que l'un d'eux est l'ID de l'élément de liste sélectionné par l'utilisateur avant de cliquer sur le bouton personnalisé du ruban.


7.  Dans le projet **ChainStoreWeb**, ouvrez le fichier **Pages/EmployeeAdder.aspx**. Notez qu'il n'a pas d'interface utilisateur. Le complément va utiliser cette page comme un type de service web. Cela est possible grâce à la classe ASP.NET **System.Web.UI.Page** qui implémente **System.Web.IHttpHandler** et à l'événement ** Page_Load** qui s'exécute automatiquement lorsque la page est demandée.


8.  Ouvrez le fichier code-behind **Pages/EmployeeAdder.aspx.cs**. La méthode qui ajoute l'employé à la base de données distante,  `AddLocalEmployeeToCorpDB`, est déjà présente. Elle utilise l'objet **SharePointContext** pour obtenir l'URL du site web hôte, que le complément utilise comme discriminant client. Par conséquent, la première chose que la méthode **Page_Load** doit faire consiste à initialiser cet objet. Celui-ci est créé et mis en cache dans la session lors du chargement de la page d'accueil du complément, donc ajoutez le code suivant à la méthode **Page_Load**. (L'objet **SharePointContext** est défini dans le fichier SharePointContext.cs que les Outils de développement Office pour Visual Studio génèrent lors de la création de la solution de complément.)

 ```cs

spContext = Session["SPContext"] as SharePointContext;
 ```

9.  La méthode `AddLocalEmployeeToCorpDB` prend le nom de l'employé en tant que paramètre. Ajoutez la ligne suivante à la méthode **Page_Load**. Vous créerez la méthode  `GetLocalEmployeeName` dans une étape ultérieure.

 ```cs
  // Read from SharePoint
string employeeName = GetLocalEmployeeName();
 ```

10.  Sous cette ligne, ajoutez l'appel à la méthode `AddLocalEmployeeToCorpDB`.

 ```cs

// Write to remote database
AddLocalEmployeeToCorpDB(employeeName);
 ```

11.  Ajoutez une instruction **using** au fichier pour l'espace de noms `Microsoft.SharePoint.Client`. (Les Outils de développement Office pour Visual Studio ont inclus l'assembly Microsoft.SharePoint.Client dans le projet **ChainStoreWeb** lors de sa création.)


12.  À présent, ajoutez la méthode suivante à la classe `EmployeeAdder`. Le modèle objet côté client SharePoint .NET (CSOM) est décrit en détail sur une autre page de MSDN et nous vous encourageons à la consulter lorsque vous aurez terminé de lire cette série. Pour cet article, notez que la classe **ListItem** représente un élément dans une liste SharePoint et que la valeur d'un champ dans l'élément peut être référencée avec la syntaxe « indexer ». Notez également que le code fait référence au champ en tant que « Titre », même si vous avez modifié le nom du champ par « Nom ». En effet, les champs sont toujours référencés dans le code par leur nom *interne*  , pas par leur nom d'affichage. Le nom interne d'un champ est défini lorsque le champ est créé et ne peut pas être modifié. Vous terminerez de définir l'élément `TODO1` à une étape ultérieure.

 ```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    // TODO1: Initialize the localEmployee object by getting
    // the item from SharePoint.
 
    return localEmployee["Title"].ToString();
}
 ```

13.  Notre code a besoin de l'ID de l'élément de liste avant de pouvoir le récupérer auprès de SharePoint. Ajoutez la déclaration suivante à la classe `EmployeeAdder` juste en dessous de la déclaration de l'objet `spContext`.

 ```cs

private int listItemID;
 ```

14.  À présent, ajoutez la méthode suivante à la classe `EmployeeAdder` pour obtenir l'ID de l'élément de liste à partir du paramètre de requête.

 ```cs
  private int GetListItemIDFromQueryParameter()
{
    int result;
    Int32.TryParse(Request.QueryString["SPListItemId"], out result);
    return result;
}
 ```

15.  Pour initialiser la variable `listItemID`, ajoutez la ligne suivante à la méthode **Page_Load** juste en dessous de la ligne qui initialise la variable `spContext`.

 ```cs

listItemID = GetListItemIDFromQueryParameter();
 ```

16.  Dans `GetLocalEmployeeName`, remplacez l'élément  `TODO1` par le code suivant. Considérez simplement ce code comme une boîte noire pendant que nous essayons de faire fonctionner le bouton personnalisé. Nous en apprendrons davantage sur ce code dans l'article suivant de cette série, qui porte sur le modèle objet client SharePoint.

 ```cs
  using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
    localEmployee = localEmployeesList.GetItemById(listItemID);
    clientContext.Load(localEmployee);
    clientContext.ExecuteQuery();
}

 ```


     L'ensemble de la méthode doit désormais se présenter de la façon suivante.



 ```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
 ```

17.  La page EmployeeAdder ne doit pas réellement faire l'objet d'un rendu, ajoutez donc les éléments suivants en tant que dernière ligne de la méthode **Page_Load**. Cela permet de rediriger le navigateur vers la page de vue de liste pour la liste **Employés locaux**.

 ```cs

// Go back to the Local Employees page
Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);

 ```


     L'ensemble de la méthode **Page_Load** doit désormais se présenter de la façon suivante.



 ```cs

protected void Page_Load(object sender, EventArgs e)
{
    spContext = Session["SPContext"] as SharePointContext;
    listItemID = GetListItemIDFromQueryParameter();

    // Read from SharePoint
    string employeeName = GetLocalEmployeeName();

    // Write to remote database
    AddLocalEmployeeToCorpDB(employeeName);

    // Go back to the preceding page
    Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);
}
 ```


## Demande d'autorisation pour lire la liste du site web hôte

 Comme vous l'avez vu, SharePoint vous invite à accorder les autorisations de complément au site web hôte lorsqu'il est installé. Vous avez réinstallé le complément chaque fois que vous avez appuyé sur F5. Jusqu'à présent, le complément a eu uniquement besoin des autorisations minimales, mais la méthode `GetLocalEmployeeName` nécessite l'autorisation de lire les listes du site web hôte. Le complément utilise son manifeste de complément pour indiquer à SharePoint les autorisations dont il a besoin. Procédez comme suit.




1.  Dans l' **Explorateur de solutions**, ouvrez le fichier AppManifest.xml dans le projet **ChainStore**. (Le fichier s'appelle AppManifest, car à l'origine les compléments étaient des « applications », ou « apps ».) Le concepteur de manifeste s'ouvre.


2.  Ouvrez l'onglet **Autorisations** et cliquez sur la cellule vide sous la colonne **Portée**, puis sélectionnez **Liste** dans le menu déroulant.


3.  Dans le champ **Autorisation**, sélectionnez **Lecture** dans la liste déroulante.


4.  Laissez le champ **Propriétés** vide et enregistrez le fichier. L'onglet **Autorisation** doit se présenter comme suit :

!\[Onglet Autorisations du concepteur du manifeste de complément affichant la portée de la liste et l'autorisation de lecture.](images/8dd2a25f-103a-42af-aa88-c8ec796315db.PNG)






## Exécution du complément et test du bouton






1.  Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio héberge l'application web distante dans IIS Express et héberge la base de données SQL dans SQL Express. Il réalise également une installation temporaire du complément sur votre site SharePoint de test et l'exécute immédiatement. Vous êtes invité à accorder des autorisations au complément avant l'ouverture de sa page d'accueil. Cette fois, l'invite comporte un menu déroulant dans lequel vous sélectionnez la liste que l'application a besoin de lire, comme indiqué dans la capture d'écran suivante.

!\[Invite d'autorisation de complément SharePoint avec la liste nommée « Employés locaux » sélectionnée dans une liste déroulante intitulée « L'autoriser à lire les éléments de la liste »](images/84e8b42c-4800-4947-acbd-21c6f096f4ea.PNG)





2.  Sélectionnez **Employés locaux** dans la liste et cliquez sur **Approuver**.


3.  Lorsque la page d'accueil du complément s'ouvre, cliquez sur **Retour au site** sur le contrôle Chrome dans la partie supérieure.


4.  À partir de la page d'accueil du site web, accédez à **Contenu du site | Employés locaux**. La page de vue de liste s'ouvre.


5.  Ajoutez quelques employés à la liste. *N'activez pas la case **Ajouté à la base de données d'entreprise**.* 


6.  Sur le ruban, ouvrez l'onglet **Éléments**. Dans la section **Actions** de l'onglet, vous trouverez le bouton personnalisé **Ajouter à la base de données d'entreprise**.


7.  Sélectionnez un élément dans la liste. La page et le ruban doivent se présenter comme suit :

!\[Liste des employés locaux. Un élément est mis en surbrillance. Au-dessus figurent le ruban et un bouton nommé « Ajouter à la base de données de l'entreprise » dans la section Actions.](images/797a5ceb-7291-4b62-8075-2bb6a1b8e8a1.PNG)





8.  Cliquez sur le bouton **Ajouter à la base de données d'entreprise**.  * **Vous devez d'abord avoir sélectionné un élément.*** 


9.  Il va vous sembler que la page se recharge, car la méthode **Page_Load** de la page EmployeeAdder vous redirige vers elle.


10.  Utilisez le bouton retour du navigateur à deux reprises pour revenir à la page d'accueil du complément.


11.  Cliquez sur **Afficher les employés** et la liste des employés comporte tous les employés que vous avez ajoutés. Elle doit se présenter comme suit :

!\[Liste des employés de l'entreprise sur la page de démarrage du complément affichant le même employé sélectionné à l'étape précédente.](images/4a300a4e-f479-4f63-b536-6315c5d9ba4d.PNG)





12. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.


13. Vous allez travailler avec ce complément et la solution Visual Studio dans d'autres articles. Il est donc recommandé de retirer le complément une dernière fois lorsque vous avez terminé de travailler et n'allez pas le réutiliser pendant un moment. Cliquez avec le bouton droit de la souris sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.



## 
<a name="Nextsteps"> </a>

 Dans l'article suivant, nous allons cesser de coder pendant un bref instant pour présenter un aperçu du modèle objet côté client SharePoint : [ Présentation rapide du modèle objet SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md).




