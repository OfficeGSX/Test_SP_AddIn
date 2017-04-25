---
title: Déploiement par programmation d'un bouton personnalisé dans un complément hébergé par un fournisseur
ms.prod: SHAREPOINT
ms.assetid: 4d75d113-8dc8-4026-a297-c47b6d4d7008
---


# Déploiement par programmation d'un bouton personnalisé dans un complément hébergé par un fournisseur
 Découvrez comment inscrire par programmation un bouton de ruban personnalisé auprès d'une liste personnalisée dans le même Complément SharePoint hébergé par un fournisseur.
 Cet article est le neuvième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par un fournisseur. Familiarisez-vous tout d'abord avec [Compléments](sharepoint-add-ins.md) et les articles précédents de cette série :





-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [ Procédure pour donner à votre complément hébergé par un fournisseur l'apparence de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [ Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [ Présentation rapide du modèle objet SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [ Ajout d'opérations d'écriture SharePoint au complément hébergé par un fournisseur](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [ Insertion d'un composant de complément dans le complément hébergé par un fournisseur](include-an-add-in-part-in-the-provider-hosted-add-in.md)


-  [ Gestion des événements de complément dans le complément hébergé par un fournisseur](handle-add-in-events-in-the-provider-hosted-add-in.md)


-  [ Ajout d'une logique de première exécution au complément hébergé par un fournisseur](add-first-run-logic-to-the-provider-hosted-add-in.md)



> **REMARQUE**
>  Si vous avez suivi cette série sur les compléments hébergés par un fournisseur, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) et ouvrir le fichier BeforeProgrammaticButton.sln.




 Dans cet article, vous allez découvrir comment inclure un bouton de ruban personnalisé dans un Complément SharePoint lorsque la liste dont le ruban reçoit le bouton est elle-même déployée par programmation dans le même complément.
## Nouvel ajout du bouton personnalisé au projet


> **REMARQUE**
>  Les paramètres des projets de démarrage dans Visual Studio ont tendance à revenir aux valeurs par défaut à chaque fois que la solution est rouverte. Veillez à toujours suivre les étapes ci-dessous immédiatement après la réouverture de la solution d'exemple de cette série d'articles :>  Cliquez avec le bouton droit sur le nœud de la solution en haut de l' **Explorateur de solutions** et sélectionnez **Définir les projets de démarrage**. >  Assurez-vous que les trois projets sont définis sur **Début** dans la colonne **Action**. 




 Dans l'article précédent, vous avez supprimé le bouton de ruban personnalisé **AjouterEmployéBDEntreprise** du projet. Ajoutez-le à nouveau en suivant les étapes ci-dessous.




1.  Dans l' **Explorateur de solutions**, appuyez sur le bouton **Afficher tous les fichiers** sur la petite barre d'outils en haut de l' **Explorateur de solutions**.

!\[Barre d'outils de l'Explorateur de solutions avec une zone tracée autour du bouton « Afficher tous les fichiers ».](images/f6b035f5-1aa7-452a-8f59-9dd44b062d06.PNG)





2.  Dans le projet **ChainStore**, cliquez avec le bouton droit sur **AjouterEmployéBDEntreprise** et sélectionnez **Inclure dans le projet**.


3.  Cliquez à nouveau sur le bouton **Afficher tous les fichiers**.


4.  Dans le projet **ChainStore**, développez **AjouterEmployéBDEntreprise**, puis ouvrez le fichier elements.xml. 



## Compréhension d'un problème et de sa solution

 Dans le fichier elements.xml, l'attribut **RegistrationId** de l'élément **CustomAction** identifie la liste comportant le ruban auquel le bouton est ajouté : `{$ListId:Lists/Local Employees;}`. Cette méthode fonctionne bien lorsque la liste a déjà été ajoutée manuellement au site web hôte. Cependant, étant donné que nous déployons la liste par programmation dans une logique de première exécution, la liste n'existe pas lorsque SharePoint installe le complément et essaie de déployer le bouton. L'installation de la macro complémentaire génèrerait une exception et échouerait.



 Le déploiement de la liste dans le gestionnaire d'événements d'installation à la place de la logique de première exécution ne permet pas de résoudre le problème, car SharePoint déploie des composants personnalisés définis par une description, tels que le bouton personnalisé (et le composant de complément **Passer une commande**),  *avant*  d'exécuter le gestionnaire personnalisé. Par conséquent, la liste n'existe pas lorsque SharePoint tente de déployer le bouton.



 La création entièrement par programmation d'un bouton personnalisé n'est pas pratique pour des raisons qui sont d'un niveau trop avancé pour en parler ici. Heureusement, ce n'est pas nécessaire. Il existe une façon relativement simple de créer un bouton personnalisé par semi-programmation et de l'affecter à une liste personnalisée. Voici les étapes de base à suivre :




1.  Conservez le bouton défini par une description dans le projet, mais affectez-le au ruban d'un élément qui se trouve toujours sur tous les sites SharePoint, au lieu d'une liste déployée par programmation avec le même complément.


2.  Dans la logique de première exécution, une fois que la liste a été créée par programmation, ajoutez par programmation un bouton non défini au ruban de la liste.


3.  Initialisez les propriétés du nouveau bouton avec les valeurs du bouton d'origine. À ce stade, vous disposez de deux boutons identiques. Le deuxième est affecté au ruban de la liste **Employés locaux**.


4.  Supprimez par programmation le bouton d'origine.



## Inscription par programmation du bouton personnalisé

 La procédure suivante montre comment mettre en œuvre cette stratégie.




1.  Dans le projet **ChainStore**, développez **AjouterEmployéBDEntreprise**, puis ouvrez le fichier elements.xml et définissez la valeur de l'attribut **RegistrationId** de l'élément **CustomAction** sur « 100 ». Il s'agit de l'ID d'un type de liste. Même s'il n'existe aucune instance de liste de ce type sur le site web, la liste *type*  se trouve sur chaque site web SharePoint. L'attribut doit maintenant se présenter comme suit.

 ```XML

RegistrationId="100"
 ```

2.  Dans le fichier SharePointComponentDeployer.cs, ajoutez la ligne suivante à la méthode `DeployChainStoreComponentsToHostWeb`, juste en dessous de la ligne qui appelle  `CreateLocalEmployeesList`. Vous allez créer cette méthode à l'étape suivante.

 ```cs
  ChangeCustomActionRegistration();
 ```

3.  Ajoutez la méthode suivante à la classe `SharePointComponentDeployer`. Notez les points suivants concernant ce code.

  -  Étant donné que l'action personnalisée (en d'autres termes, le bouton personnalisé) a été inscrite auprès du ruban d'une liste *type*  , sa portée s'étend à l'ensemble du site et elle se trouve dans la collection d'actions personnalisées du site. Le code peut ainsi la récupérer à partir de cette collection.


  -  La valeur de l'élément `action.Name` provient de l'attribut **ID** de l'élément **CustomAction** du fichier element.xml dans **AjouterEmployéBDEntreprise**.

    > **IMPORTANTE**
      > **Vous devez modifier la valeur  `action.Name` dans le code ci-dessous afin qu'elle corresponde à la valeur qui apparaît dans votre fichier elements.xml.** Le composant GUID du nom sera différent. Notez qu'il existe un caractère « . » entre le GUID et le reste du nom. Voici un exemple de la ligne.>  `where action.Name == "4a926a42-3577-4e02-9d06-fef78586b1bc.AddEmployeeToCorpDB"`

 ```cs
  private static void ChangeCustomActionRegistration()
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
         var query = from action in clientContext.Web.UserCustomActions
                     where action.Name == "{button_GUID} .AddEmployeeToCorpDB"
                     select action;
          IEnumerable<UserCustomAction> matchingActions = clientContext.LoadQuery(query);	   
	         clientContext.ExecuteQuery();
	
          UserCustomAction webScopedEmployeeAction = matchingActions.Single();

         // TODO8: Get a reference to the (empty) collection of custom actions 
         // that are registered with the custom list.

         // TODO9: Add a blank custom action to the list's collection.

         // TODO10: Copy property values from the descriptively deployed
         // custom action to the new custom action

        // TODO11: Delete the original custom action.     

          clientContext.ExecuteQuery();
    }
}
 ```

4.  Remplacez l'élément `TODO8` par le code suivant.

     Notez que lorsque vous retirez un complément, les composants créés par le complément ne sont pas supprimés. Une fois que votre logique de première exécution s'exécute, une action personnalisée apparaît dans la collection **UserCustomActions** de la liste, et elle ne sera pas retirée la prochaine fois que vous appuierez sur F5. Pour éviter toute confusion, la dernière ligne de ce code ( `listActions.Clear();`) vide la collection. 



 ```cs

var queryForList = from list in clientContext.Web.Lists
                   where list.Title == "Local Employees"
                   select list;
IEnumerable<List> matchingLists = clientContext.LoadQuery(queryForList);
clientContext.ExecuteQuery();

List employeeList = matchingLists.First();
var listActions = employeeList.UserCustomActions;
clientContext.Load(listActions);
listActions.Clear();
 ```

5.  Remplacez l'élément `TODO9` par la ligne suivante, qui ajoute une action personnalisée non définie à la liste **Employés locaux**.

 ```cs

var listScopedEmployeeAction = listActions.Add();
 ```

6.  Remplacez l'élément `TODO10` par le code suivant. Notez ce qui suit à propos de ce code :

  -  Il attribue les valeurs de propriété du bouton d'étendue web (qui a été déployé avec un balisage descriptif) aux propriétés correspondantes du bouton d'étendue de liste afin que les deux boutons soient identiques, sauf en matière d'étendue.


  -  La propriété **Sequence** spécifie l'ordre relatif d'affichage du bouton dans la zone du ruban. Dans ce cas, le bouton se trouve dans la section **Actions** de l'onglet **Éléments** du ruban. Dans le balisage descriptif, cette valeur est définie sur 10 001, une valeur suffisamment élevée pour que le bouton apparaisse après (autrement dit, à droite) des boutons prêts à l'emploi que SharePoint place dans la section **Actions** du ruban.



 ```cs
  listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
listScopedEmployeeAction.Update();
 ```

7.  Remplacez l'élément `TODO11` par la ligne suivante, qui supprime le bouton d'origine défini par une description. Si nous n'avions pas cette ligne, chaque liste du site web utilisant le modèle de liste « 100 » disposerait du bouton personnalisé. Étant donné que la fonctionnalité du bouton est étroitement liée à la liste **Employés Locaux**, il serait inutile que le bouton apparaisse sur n'importe quelle autre liste. En outre, sans cette ligne, le bouton apparaîtrait  *deux fois*  sur la liste **Employés locaux**, car cette liste utilise le modèle « 100 ». 

 ```cs

webScopedEmployeeAction.DeleteObject();
 ```


     L'ensemble de la méthode doit désormais se présenter comme suit (à l'exception de l'espace réservé qui doit être remplacé par un GUID).



 ```cs
  private static void ChangeCustomActionRegistration()
{
    using (var clientContext = SPContext.CreateUserClientContextForSPHost())
    {
         var query = from action in clientContext.Web.UserCustomActions
                     where action.Name == "{button_GUID} .AddEmployeeToCorpDB"
                     select action;
          IEnumerable<UserCustomAction> matchingActions = clientContext.LoadQuery(query);	   
	         clientContext.ExecuteQuery();
	
          UserCustomAction webScopedEmployeeAction = matchingActions.Single();

         var queryForList = from list in clientContext.Web.Lists
                            where list.Title == "Local Employees"
                            select list;
         IEnumerable<List> matchingLists = clientContext.LoadQuery(queryForList);
         clientContext.ExecuteQuery();

        List employeeList = matchingLists.First();
        var listActions = employeeList.UserCustomActions;
        clientContext.Load(listActions);
        listActions.Clear();

        var listScopedEmployeeAction = listActions.Add();

        listScopedEmployeeAction.Title = webScopedEmployeeAction.Title;
        listScopedEmployeeAction.Location = webScopedEmployeeAction.Location;
        listScopedEmployeeAction.Sequence = webScopedEmployeeAction.Sequence;
        listScopedEmployeeAction.CommandUIExtension = webScopedEmployeeAction.CommandUIExtension;
        listScopedEmployeeAction.Update();

        webScopedEmployeeAction.DeleteObject();     

        clientContext.ExecuteQuery();
    }
}
 ```


## Demande du contrôle total du site web hôte

 Étant donné que le complément ajoute et supprime désormais des actions personnalisées à étendue web, nous devons passer les autorisations demandées par le complément du statut de gestion au statut de contrôle total. Procédez comme suit.




1.  Dans l' **Explorateur de solutions**, ouvrez le fichier AppManifest.xml dans le projet **ChainStore**. 


2.  Ouvrez l'onglet **Autorisations**. Laissez la valeur **Portée** définie sur **Web**, mais dans le champ **Autorisation**, sélectionnez **Contrôle total** dans le menu déroulant.


3. Enregistrez le fichier.



## Exécution du complément et test du déploiement du bouton






1.  Ouvrez la page **Contenu du site** du site web de la boutique de Hong Kong *et supprimez la liste **Employés locaux**.* 

    > **REMARQUE**
      >  Le retrait d'un complément dans Visual Studio ne supprime pas les listes créées par le complément. Vous devez donc le supprimer manuellement chaque fois que vous testez le code qui le crée.
2.  Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio héberge l'application web distante dans IIS Express et héberge la base de données SQL dans SQL Express. Il réalise également une installation temporaire du complément sur votre site SharePoint de test et l'exécute immédiatement. Vous êtes invité à accorder des autorisations au complément avant l'ouverture de sa page d'accueil.


3.  Lorsque la page d'accueil du complément s'ouvre, sélectionnez le lien **Retour au site** sur le contrôle Chrome dans la partie supérieure.


4.  Accédez à la page **Contenu du site**. La liste **Employés locaux** est à cet endroit, car votre logique de première exécution l'y a ajoutée.

    > **REMARQUE**
      >  Si la liste n'est pas cet endroit ou que vous détectez d'autres éléments indiquant que le code de première exécution ne s'exécute pas, il se peut que la table **Clients** ne soit pas vidée lorsque vous appuyez sur F5. La cause la plus commune de ce phénomène est que le projet **ChainCorporateDB** n'est plus défini comme projet de démarrage dans Visual Studio. Consultez la remarque en haut de cet article pour résoudre ce problème. Assurez-vous également que vous avez configuré la base de données afin qu'elle soit régénérée comme décrit dans la rubrique relative à la [ Configuration de Visual Studio pour régénérer la base de données d'entreprise à chaque session de débogage](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md#Rebuild). 
5.  Ouvrez la liste et ajoutez un élément.


6.  Dans la vue de liste, sélectionnez l'élément et ouvrez l'onglet **Élément** sur le ruban. Le bouton **Ajouter à la base de données d'entreprise** est sur le ruban.


7.  Cliquez sur le bouton pour ajouter l'employé à la base de données d'entreprise. Le champ **Ajouté à la base de données d'entreprise** est ensuite défini sur **Oui**.


8.  Accédez à la page **Contenu du site** et sélectionnez **Ajouter un complément**.


9.  Ajoutez une nouvelle **liste personnalisée**. Par défaut, elle est de type « générique ». (Il s'agit du type de liste 100.) Une fois que la liste est créée, ouvrez l'onglet **Élément** du ruban. Notez que le bouton **Ajouter à la base de données d'entreprise** ne se trouve *pas*  sur le ruban, car votre code a supprimé le bouton à étendue web.


10. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.


11. Vous allez travailler avec ce complément et la solution Visual Studio dans d'autres articles. Il est donc recommandé de retirer le complément une dernière fois lorsque vous avez terminé de travailler et n'allez pas le réutiliser pendant un moment. Cliquez avec le bouton droit de la souris sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.



## 
<a name="Nextsteps"> </a>

 Les événements de listes et d'éléments de liste peuvent également disposer de gestionnaires personnalisés dans SharePoint. Vous apprendrez à en créer et déployer un dans votre logique de première exécution dans le prochain article : [ Gestion des événements d'élément de liste dans un complément hébergé par un fournisseur](handle-list-item-events-in-the-provider-hosted-add-in.md)




