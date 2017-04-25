---
title: Ajout d'opérations d'écriture SharePoint au complément hébergé par un fournisseur
ms.prod: SHAREPOINT
ms.assetid: c3d11afd-098e-4cf9-941e-cca6db28d732
---


# Ajout d'opérations d'écriture SharePoint au complément hébergé par un fournisseur
 Découvrez comment écrire des données dans SharePoint sur un Complément SharePoint hébergé par un fournisseur.
 Cet article est le cinquième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par un fournisseur. Familiarisez-vous tout d'abord avec [Compléments](sharepoint-add-ins.md) et les articles précédents de cette série :
  
    
    


-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [ Procédure pour donner à votre complément hébergé par un fournisseur l'apparence de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [ Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [ Présentation rapide du modèle objet SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  

> **REMARQUE**
>  Si vous avez suivi cette série sur les compléments hébergés par un fournisseur, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) et ouvrir le fichier BeforeSharePointWriteOps.sln.
  
    
    

 Dans cet article, nous revenons au codage et allons ajouter des fonctions qui permettent d'écrire des données dans le Complément SharePoint Chain Store.
## Modification d'une valeur de colonne sur un élément de liste SharePoint

 Notre complément dispose d'un bouton de ruban personnalisé qui ajoute un employé de la liste **Employés locaux** du magasin de Hong Kong à la base de données d'entreprise. Mais l'utilisateur doit se souvenir de modifier manuellement la valeur du champ **Ajouté à la base de données d'entreprise** surOui. Nous allons ajouter du code pour que cette opération soit réalisée automatiquement.
  
    
    

> **REMARQUE**
>  Les paramètres des projets de démarrage dans Visual Studio ont tendance à revenir aux valeurs par défaut à chaque fois que la solution est rouverte. Veillez à toujours suivre les étapes ci-dessous immédiatement après la réouverture de la solution d'exemple de cette série d'articles :>  Cliquez avec le bouton droit sur le nœud de la solution en haut de l' **Explorateur de solutions** et sélectionnez **Définir les projets de démarrage**. >  Assurez-vous que les trois projets sont définis sur **Début** dans la colonne **Action**. 
  
    
    


1.  Dans l' **Explorateur de solutions**, ouvrez le fichier EmployeeAdder.cs.
    
  
2.  Ajoutez la ligne suivante à la méthode **Page_Load**entre l'appel de l'élément `AddLocalEmployeeToCorpDB` et l'appel de **Response.Redirect**. Vous créerez la méthode  `SetLocalEmployeeSyncStatus` à l'étape suivante.
    
 ```cs
  
// Write to SharePoint
SetLocalEmployeeSyncStatus();
 ```

3.  Ajoutez la nouvelle méthode suivante à la classe `EmployeeAdder`. Notez les points suivants concernant ce code.
    
  -  Le nom interne du champ **Ajouté à la base de données d'entreprise** semble anormal. Les noms de champs internes ne peuvent pas contenir d'espaces. Lorsqu'un utilisateur crée un champ avec des espaces dans son nom d'affichage, SharePoint insère la chaîne « _x0020_ » à la place de chaque espace lors de la définition du nom interne. Cela transforme « Ajouté à la base de données d'entreprise » en « Ajouté_x0020_à_x0020_la_x0020_base_x0020_de_x0020_données_x0020_d'entreprise ». Les noms internes ne peuvent pas comporter plus de 32 caractères, c'est pourquoi le nom est tronqué à seulement « Ajouté_x0020_à_x0020_la_x0020_ba ».
    
  
  -  Bien que la colonne **Ajouté à la base de données d'entreprise** soit appelée un champ Oui/Non dans l'interface utilisateur SharePoint, il s'agit en réalité d'une valeur booléenne, c'est pourquoi sa valeur est définie sur **true**, et non sur « Oui ».
    
  
  -  La méthode **Update** de la classe **ListItem** doit être appelée pour valider les modifications apportées à la base de données de contenu SharePoint. D'une manière générale mais pas universelle, lorsque vous modifiez une valeur de propriété d'un objet stocké dans les bases de données SharePoint, vous devez appeler la méthode **Update** de l'objet.
    
  

 ```cs
  
private void SetLocalEmployeeSyncStatus()
{
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        ListItem selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        selectedLocalEmployee["Added_x0020_to_x0020_Corporate_x"] = true;
        selectedLocalEmployee.Update();
        clientContext.ExecuteQuery();
    }
}
 ```


## Demande d'autorisation pour écrire dans la liste du site web hôte

 Étant donné que le complément peut désormais non seulement lire la liste, mais aussi écrire dedans, nous devons passer les autorisations demandées par le complément du statut de lecture à celui d'écriture. Procédez comme suit.
  
    
    

1.  Dans l' **Explorateur de solutions**, ouvrez le fichier AppManifest.xml dans le projet **ChainStore**. 
    
  
2.  Ouvrez l'onglet **Autorisations** et dans le champ **Autorisation**, sélectionnez **Écriture** dans le menu déroulant.
    
  
3. Enregistrez le fichier. 
    
  

## Exécution du complément et test du bouton


  
    
    

1.  Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio héberge l'application web distante dans IIS Express et héberge la base de données SQL dans SQL Express. Il réalise également une installation temporaire du complément sur votre site SharePoint de test et l'exécute immédiatement. Vous êtes invité à accorder des autorisations au complément avant l'ouverture de sa page d'accueil.
    
  
2.  Dans le formulaire d'autorisations, choisissez **Employés locaux** dans la liste et cliquez sur **Approuver**.
    
  
3.  Lorsque la page d'accueil du complément s'ouvre, cliquez sur **Retour au site** sur le contrôle Chrome dans la partie supérieure.
    
  
4.  À partir de la page d'accueil du site web, accédez à **Contenu du site | Employés locaux**. La page de vue de liste s'ouvre.
    
  
5.  Si la liste ne comporte aucun employé dont la colonne **Ajouté à la base de données d'entreprise** est définie sur **Non**, ajoutez un employé à la liste, et  *n'activez pas la case **Ajouté à la base de données d'entreprise**.* 
    
  
6.  Sur le ruban, ouvrez l'onglet **Éléments**. Dans la section **Actions** de l'onglet, vous trouverez le bouton personnalisé **Ajouter à la base de données d'entreprise**.
    
  
7.  Dans la liste, sélectionnez un employé dont la colonne **Ajouté à la base de données d'entreprise** est définie sur **Non**.
    
  
8.  Cliquez sur le bouton **Ajouter à la base de données d'entreprise**.  * **Vous devez d'abord avoir sélectionné un élément.*** 
    
  
9.  Il va vous sembler que la page se recharge, car la méthode **Page_Load** de la page EmployeeAdder vous redirige vers elle. La valeur du champ **Ajouté à la base de données d'entreprise** de l'employé a été modifiée en **Oui**.
    
    > **REMARQUE**
      >  Pour le moment, rien n'empêche un utilisateur de modifier manuellement la valeur **Ajouté à la base de données d'entreprise**, c'est pourquoi il est possible que la liste et la base de données soient incohérentes. Vous obtiendrez une solution à ce problème dans un article ultérieur de cette série. 
10. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.
    
  
11. Cliquez avec le bouton droit sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.
    
  

## Création d'une liste personnalisée sur le site web hôte

 La prochaine amélioration du complément Chain Store consiste à créer des éléments dans une liste, plutôt que de modifier un champ dans un élément existant. En particulier, lorsqu'une nouvelle commande est passée au niveau de l'entreprise, un élément est automatiquement créé dans une liste SharePoint qui indique aux employés locaux de s'attendre à une livraison. La liste s'intitule **Livraisons attendues** et les étapes suivantes vous indiquent comme la créer. Dans un autre article de cette série, vous apprendrez à ajouter par programmation une liste personnalisée à un site web hôte, mais pour le moment vous allez ajouter celle-ci manuellement.
  
    
    

1.  À partir de la page d'accueil du magasin Fabrikam Hong Kong, accédez à **Contenu du site | Ajouter un complément | Liste personnalisée**. 
    
  
2.  Dans la boîte de dialogue **Ajout d'une liste personnalisée**, indiquez Livraisons attendues comme nom et cliquez sur **Créer**. 
    
  
3.  Sur la page **Contenu du site**, ouvrez la liste **Livraisons attendues**.
    
  
4.  Ouvrez l'onglet **Liste** sur le ruban, puis cliquez sur le bouton **Paramètres de liste**.
    
  
5.  Dans la section **Colonnes** de la page **Paramètres de liste**, cliquez sur la colonne **Titre**. 
    
  
6.  Dans le formulaire **Modifier la colonne**, modifiez le **nom de la colonne** Titre enProduit, puis cliquez sur **OK**.
    
  
7.  Sur la page **Paramètres**, cliquez sur **Créer une colonne**.
    
  
8.  Dans un précédent article de cette série, vous avez appris à créer des colonnes personnalisées pour une liste. Pour la liste **Livraisons attendues**, ajoutez quatre colonnes à l'aide des valeurs du tableau suivant. Laissez tous les autres paramètres sur leurs valeurs par défaut.
    

|**Nom de colonne**|**Type**|**Obligatoire ?**|**Valeur par défaut**|
|:-----|:-----|:-----|:-----|
|Fournisseur <br/> |**Une seule ligne de texte** <br/> | non obligatoire <br/> |Aucune  <br/> |
|Quantité <br/> |**Number** <br/> |Obligatoire  <br/> |1 <br/> |
|Arrivé <br/> |**Oui/Non** <br/> | non obligatoire <br/> |Non <br/> |
|Ajouté à l'inventaire <br/> |**Oui/Non** <br/> | non obligatoire <br/> |Non <br/> |
   
9.  Après avoir créé les colonnes, sur la page des paramètres de liste, cliquez sur **Contenu du site** pour ouvrir la page **Contenu du site**. Ouvrez la liste **Livraisons attendues**.
    
  
10.  Cliquez sur **nouvel élément**. Le formulaire de création d'élément doit se présenter exactement comme suit, y compris les deux astérisques indiquant les champs obligatoires :
    
!\[Formulaire de création d'élément de la liste des livraisons prévues. Avec des champs Produit, Fournisseur, Quantité, Arrivé et « Ajouté à l'inventaire ». Des astérisques se trouvent à côté des titres Produit et Quantité, et Quantité comporte la valeur par défaut de un.](images/e552b5c9-8baa-4e53-9295-4d85a79d7734.PNG)
  

  

  
11.  Nous ne voulons pas créer manuellement des éléments sur cette liste, donc cliquez sur **Annuler**.
    
  

## Insertion d'un élément dans une liste SharePoint

 Maintenant, ajoutez une fonction au complément qui permet de créer un élément dans la liste **Livraisons attendues** à chaque fois qu'une commande est passée pour le magasin de Hong Kong au niveau de l'entreprise.
  
    
    

1.  Dans l' **Explorateur de solutions**, ouvrez le fichier OrderForm.aspx.cs.
    
  
2.  Ajoutez une instruction **using** pour l'élément **Microsoft.SharePoint.Client** en haut du fichier.
    
  
3.  Dans la méthode `btnCreateOrder_Click`, ajoutez la ligne suivante juste en dessous de l'appel à l'élément  `CreateOrder`. Vous créerez la méthode CréerLivraisonAttendue à l'étape suivante.
    
 ```cs
  
CreateExpectedShipment(txtBoxSupplier.Text, txtBoxItemName.Text, quantity);
 ```

4.  Ajoutez la méthode suivante à la classe `OrderForm`. Notez les points suivants concernant ce code.
    
  -  Un objet **ListItem** n'est pas créé avec un constructeur pour des raisons de performances. Un objet **ListItem** possède de nombreuses propriétés (avec des valeurs par défaut). Si un constructeur est utilisé, l'ensemble de l'objet doit être inclus dans le message XML que la méthode **ExecuteQuery** envoie au serveur. L'objet **ListItemCreationInformation** est un objet simple qui contient uniquement les valeurs minimales autres que par défaut dont le serveur a besoin pour créer un objet **ListItem**. Il est possible qu'il existe une ligne qui crée un objet **ListItem**, mais souvenez-vous que cette ligne ajoute uniquement des balises XML à un message envoyé au serveur. L'objet **ListItem** est créé à cet endroit sur le serveur.
    
  
  -  Il est inutile de renvoyer l'objet **ListItem** au client, donc il n'existe aucun appel à la méthode **ClientContext.Load**.
    
  
  -  Le code ne doit pas définir explicitement les champs **Arrivé** ou **Ajouté à l'inventaire**, car ils sont définis sur leur valeur par défaut « Non » qui correspond à ce que nous souhaitons.
    
  

 ```
  private void CreateExpectedShipment(string supplier, string product, UInt16 quantity)
{
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List expectedShipmentsList = clientContext.Web.Lists.GetByTitle("Expected Shipments");
        ListItemCreationInformation itemCreateInfo = new ListItemCreationInformation();
        ListItem newItem = expectedShipmentsList.AddItem(itemCreateInfo);
        newItem["Title"] = product;
        newItem["Supplier"] = supplier;
        newItem["Quantity"] = quantity;
        newItem.Update();
        clientContext.ExecuteQuery();
    }
}
 ```


## Vérification des composants supprimés

 Toute personne disposant des privilèges de propriétaire de liste pour une liste SharePoint peut supprimer la liste. Si la liste est déployée sur le site web hôte par un complément, le propriétaire du site web hôte peut la supprimer. Cela peut arriver si le propriétaire décide de travailler sans la fonctionnalité fournie par la liste. (Elle peut être restaurée à partir de la Corbeille SharePoint si le propriétaire change d'avis.)
  
    
    
 La méthode `CreateExpectedShipment` dépend de l'existence de la liste **Livraisons attendues**. Supposons qu'un propriétaire de site web a décidé de supprimer la liste. Plus tard, lorsqu'une commande est passée à l'aide du **bon de commande** du complément, l'élément `CreateExpectedShipment` est appelé et déclenche une exception dont le message indique qu'il n'existe aucune liste **Livraisons attendues** sur le site web SharePoint.
  
    
    
 Vous voudrez peut-être que la méthode vérifie l'existence de l'élément `expectedShipmentsList` avant de prendre une décision. Lorsque vous travaillez avec un CSOM, vous ne pouvez *pas*  réaliser cette vérification avec une structure simple comme celle-ci :
  
    
    
 `if (expectedShipmentsList != null) { ... }`
  
    
    
 Au lieu de cela, vous devez utiliser une classe de CSOM spécifique appelée **ConditionalScope**. Les raisons de ce phénomène sont liées au système de traitement par lots du CSOM, qui a été mentionné dans l'article précédent de cette série. (Voir la section  [ Exécution côté client et traitement par lot](get-a-quick-overview-of-the-sharepoint-object-model.md#CSOMBatching)). **ConditionalScope** et le système de traitement par lots sont des sujets avancés qui sont en dehors du champ d'application de cette série de mise en route, mais nous vous conseillons de consulter la documentation MSDN s'y rapportant après avoir terminé cette série de tutoriels.
  
    
    
 Il existe une autre façon de vérifier l'existence d'une liste : au lieu d'utiliser la méthode **GetByTitle** pour obtenir une référence à la liste, vous pouvez vérifier si une liste avec le nom spécifié existe dans la « liste des listes » du site web avec du code en procédant comme suit.
  
    
    


```cs

var query = from list in clientContext.Web.Lists
             where list.Title == "Expected Shipments" 
             select list; 
IEnumerable<List> matchingLists = clientContext.LoadQuery(query); 
clientContext.ExecuteQuery(); 
if (matchingLists.Count() != 0) 
{ 
    List expectedShipmentsList = matchingLists.Single(); 
    // Do something with the list. 
}
clientContext.ExecuteQuery(); ```

 Le code précédent présente l'avantage d'éviter les complications de la classe **ConditionalScope**. D'autre part, nous utilisons exactement le même code ailleurs dans cette série d'articles. Mais il comporte également un inconvénient : ce code exige un appel supplémentaire de l'élément **ExecuteQuery** exclusivement pour obtenir la valeur que vous souhaitez vérifier dans l'instruction **if**. Si nous utilisons cette technique dans l'élément  `CreateExpectedShipment` pour vérifier l'existence de la liste, cette méthode comportera deux appels de l'élément **ExecuteQuery**, chacun réalisant une requête HTTP à partir du serveur web distant vers SharePoint. Ces demandes représentent le composant le plus chronophage de n'importe quelle méthode du modèle objet client, il est donc généralement recommandé d'en émettre le moins possible.
  
    
    
 Nous laissons l'élément `CreateExpectedShipment` tel qu'il est, mais dans un complément production, vous devez prendre en considération la manière dont votre code va fonctionner si un composant auquel il fait référence est supprimé. La restauration par programme de la liste à partir de la Corbeille est une option, mais cela dérangerait les utilisateurs qui ont décidé intentionnellement de supprimer la liste. Vous devez également prendre en compte que la meilleure solution consiste à ne rien faire pour empêcher l'exception. Une exception provenant de SharePoint alerterait les utilisateurs que la suppression de la liste a endommagé une partie du complément. Cela représente un aspect que la personne qui a supprimé la liste n'a peut-être pas réalisé. Un utilisateur peut alors décider de restaurer la liste à partir de la Corbeille ou de continuer sans la partie de la fonctionnalité du complément qui ne fonctionne plus.
  
    
    

## Demande d'autorisations pour gérer le site web

 Souvenez-vous que lorsqu'un complément demande une autorisation en lecture ou en écriture avec l'étendue de liste, SharePoint invite l'utilisateur à approuver le complément, et la boîte de dialogue contient une liste déroulante dans laquelle l'utilisateur sélectionne la liste à laquelle le complément doit avoir accès. Une seule liste peut être sélectionnée. Toutefois, le complément Chain Store peut désormais écrire dans deux listes différentes. Pour avoir accès à plusieurs listes, le complément doit demander une autorisation à étendue web. Suivez les étapes ci-dessous :
  
    
    

1.  Dans l' **Explorateur de solutions**, ouvrez le fichier AppManifest.xml dans le projet **ChainStore**. 
    
  
2.  Ouvrez l'onglet **Autorisations** et dans le champ **Étendue**, sélectionnez **Web** dans le menu déroulant.
    
  
3.  Dans le champ **Autorisation**, sélectionnez **Écriture** dans la liste déroulante.
    
  
4. Enregistrez le fichier. 
    
  

## Exécution du complément et test de la création d'élément


  
    
    

1.  Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio héberge l'application web distante dans IIS Express et héberge la base de données SQL dans SQL Express. Il réalise également une installation temporaire du complément sur votre site SharePoint de test et l'exécute immédiatement. Vous êtes invité à accorder des autorisations au complément avant l'ouverture de sa page d'accueil.
    
  
2.  Lorsque la page d'accueil du complément s'ouvre, cliquez sur le lien **Bon de commande** au bas de la page.
    
  
3.  Saisissez des valeurs dans le formulaire et cliquez sur **Passer une commande**.
    
  
4.  Utilisez le bouton retour du navigateur pour revenir à la page d'accueil, puis cliquez sur **Retour au site** sur le contrôle Chrome dans la partie supérieure.
    
  
5.  À partir de la page d'accueil du magasin de Hong Kong, accédez à la page **Contenu du site** et ouvrez la liste **Livraisons attendues**. La liste comporte désormais un élément correspondant à la commande. La capture d'écran suivante présente un exemple :
    
!\[Liste des livraisons prévues avec un seul élément. Les champs Produit et Fournisseur comportent des noms. Le champ Quantité contient un nombre. Les deux champs Oui/Non sont définis sur « Non ».](images/e4285084-d31e-4e79-a469-ddebbc7dfb18.PNG)
  

  

  
6. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.
    
  
7. Cliquez avec le bouton droit sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.
    
  

## 
<a name="Nextsteps"> </a>

 Dans le prochain article, vous allez apprendre à faire apparaître le bon de commande distant comme un composant WebPart dans une page SharePoint : [ Insertion d'un composant de complément dans le complément hébergé par un fournisseur](include-an-add-in-part-in-the-provider-hosted-add-in.md)
  
    
    

