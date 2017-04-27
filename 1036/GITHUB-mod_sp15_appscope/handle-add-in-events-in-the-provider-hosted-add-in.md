---
title: Gestion des événements de complément dans le complément hébergé par un fournisseur
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# Gestion des événements de complément dans le complément hébergé par un fournisseur
 Apprenez à personnaliser l'installation d'un Complément SharePoint hébergé par un fournisseur.
 Cet article est le septième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par un fournisseur. Familiarisez-vous tout d'abord avec [Compléments](sharepoint-add-ins.md) et les articles précédents de cette série :





-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [ Procédure pour donner à votre complément hébergé par un fournisseur l'apparence de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [ Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [ Présentation rapide du modèle objet SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [ Ajout d'opérations d'écriture SharePoint au complément hébergé par un fournisseur](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [ Insertion d'un composant de complément dans le complément hébergé par un fournisseur](include-an-add-in-part-in-the-provider-hosted-add-in.md)



> **REMARQUE**
>  Si vous avez suivi cette série sur les compléments hébergés par un fournisseur, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) et ouvrir le fichier BeforeAdd-inEventHandlers.sln.




 Dans cet article, nous allons personnaliser la gestion d'un type d'événement dans SharePoint, appeléévénements de complément. Plus précisément, nous allons créer des gestionnaires pour les événements d'installation et de désinstallation du complément. Il existe aussi des événements de liste et d'élément de liste pouvant bénéficier d'une gestion personnalisée. Vous en apprendrez plus à ce sujet dans un autre article de cette série. Tous ces événements sont déclenchés dans SharePoint, mais votre code personnalisé qui gère chaque événement se trouve dans votre application web distante. Configurez SharePoint afin qu'il appelle votre gestionnaire personnalisé en inscrivant l'URL du gestionnaire auprès de l'événement SharePoint.
## Deux endroits pour déployer des composants SharePoint par programmation

 Nous souhaitons que notre complément Chain Store crée et déploie automatiquement les listes **Employés locaux** et **Livraisons attendues**. Un complément peut déployer à tout moment des composants SharePoint, tels qu'une liste personnalisée. Toutefois, lorsqu'un complément dépend d'un composant spécifique, comme une liste personnalisée, le composant doit absolument être déployé  *avant*  que les utilisateurs commencent à utiliser le complément. Pour ces composants essentiels, il existe deux endroits où stocker la logique de déploiement personnalisée :




-  dans un gestionnaire pour l'événement d'installation du complément ;


-  dans une logique de « première exécution » qui s'exécute la première fois que le complément est lancé dans SharePoint.


 Le choix de la meilleure solution pour un complément donné implique un niveau plus avancé. Dans cet article, nous allons uniquement mentionner quelques points de comparaison :




-  Un gestionnaire d'installation personnalisé doit terminer son opération en 30 secondes, alors que la logique de première exécution ne dispose pas de limite.


-  Si quelque chose ne se passe pas comme prévu lors de l'installation d'un complément, SharePoint restaure tout ce qu'il a fait dans le cadre de l'installation. Un gestionnaire d'installation personnalisé s'exécute *après*  que SharePoint a fait tout son possible pour installer le complément, afin qu'un gestionnaire personnalisé puisse prendre part à ce système. Par exemple, si votre logique personnalisée génère une exception, vous pouvez indiquer à SharePoint de restaurer l'ensemble de l'installation du complément. Si quelque chose ne se passe pas comme prévu dans la logique de première exécution, le complément reste installé mais ne fonctionne pas correctement.


-  SharePoint n'abandonne pas s'il doit restaurer l'installation d'un complément. Il va immédiatement essayer de le réinstaller. Il peut réaliser jusqu'à 4 tentatives (le délai de 30 secondes s'applique à chaque tentative.) Chaque fois qu'il réessaie, le gestionnaire d'installation personnalisé s'exécute à nouveau, *depuis le début*  . Si le gestionnaire réussit à installer une liste avant la restauration, il essaye d'installer à nouveau la même liste lors de la nouvelle tentative. Pour éviter cela, le code d'un gestionnaire d'installation doit être écrit de façon à ce qu'il n'entreprenne aucune action (telle que le déploiement d'un composant), à moins qu'il vérifie d'abord si cette action a déjà été réalisée ou non. Cela rend la logique d'un gestionnaire d'installation plus complexe que la logique de première exécution, puisque celle-ci n'entreprend pas de nouvelle tentative (à moins que le code indique le contraire spécifiquement). En outre, vérifier si un composant a déjà été déployé nécessite d'émettre un appel chronophage sur Internet à partir du gestionnaire distant vers SharePoint. Un deuxième appel est également nécessaire pour réellement déployer le composant (s'il n'a pas encore été déployé).


 Pour le complément Chain Store, nous allons combiner ces stratégies. Dans cet article, vous allez créer un gestionnaire d'installation qui inscrit le site web hôte comme client dans la base de données d'entreprise, puis qui définit un signal indiquant si le complément a déjà été exécuté sur le site web hôte ou non. Dans un autre article de cette série, vous placerez la logique de première exécution dans la méthode **Page_Load** de la page d'accueil du complément. Cette logique déploie, entre autres, les deux listes personnalisées.




## Configuration de la solution pour le débogage de récepteurs d'événements
<a name="RERDebug"> </a>

 Le débogage de récepteurs d'événements nécessite l'utilisation du bus des services Azure. Suivez les instructions de la rubrique [Déboguer et dépanner un récepteur d'événement distant dans un complément pour SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). Étant donné que vous utilisez un site web SharePoint Online comme site de test, veillez à suivre les instructions pour un site de test distant. Le reste de cette série d'articles considère que vous avez configuré le débogage. 




## Création du gestionnaire d'installation
<a name="RERDebug"> </a>






> **REMARQUE**
>  Les paramètres des projets de démarrage dans Visual Studio ont tendance à revenir aux valeurs par défaut à chaque fois que la solution est rouverte. Veillez à toujours suivre les étapes ci-dessous immédiatement après la réouverture de la solution d'exemple de cette série d'articles :<BR />  Cliquez avec le bouton droit sur le nœud de la solution en haut de l' **Explorateur de solutions** et sélectionnez **Définir les projets de démarrage**. <BR />  Assurez-vous que les trois projets sont définis sur **Début** dans la colonne **Action**. 





1.  Dans l' **Explorateur de solutions**, sélectionnez le projet **ChainStore**, de sorte que ses propriétés apparaissent dans le volet **Propriétés** de Visual Studio.


2.  Définissez la valeur de l'option **Gérer l'installation du complément** sur **True**. (Il est possible que cette option soit encore intitulée **Gérer l'installation de l'application**.) Cette opération entraîne deux choses :

  -  Un dossier portant le nom **Services** est créé dans le projet **ChainStoreWeb** (et pas dans le projet **ChainStore**) et deux fichiers y sont ajoutés : un fichier AppEventReceiver.svc et son fichier code-behind AppEventReceiver.svc.cs. (Les noms de fichiers commencent par la chaîne « App », car auparavant, les compléments étaient appelés des « applications », ou apps ».  *Ne renommez pas ces fichiers.*  Les Outils de développement Office pour Visual Studio considèrent que ces fichiers conservent ces noms.)


  -  L'URL du gestionnaire est enregistrée dans le manifeste du complément. Cette partie du manifeste n'est pas visible dans le concepteur de manifeste. Pour le voir, cliquez avec le bouton droit sur le fichier AppManifest.xml et sélectionnez **Afficher le code**. L'élément **Propriétés** comporte un nouvel enfant qui se présente comme suit. Ce code indique à SharePoint d'appeler la méthode **ProcessEvent** de ce service une fois que le complément est installé. Le gestionnaire personnalisé est le dernier élément qui s'exécute dans le cadre de l'installation. La chaîne `~remoteAppUrl` est un espace réservé que les Outils de développement Office pour Visual Studio remplacent par l'URL d'hôte de service. Lors du débogage, il s'agit d'une URL du bus des services Azure. Lorsque vous créez le package pour le déploiement en production, il s'agit de l'URL de production.

 ```XML

<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
 ```

3.  Ouvrez le fichier AppEventReceiver.svc.cs.


4.  Vous pouvez voir que les Outils de développement Office pour Visual Studio ont créé un exemple d'implémentation de la méthode **ProcessEvent**. Toutes les implémentations de cette méthode commencent par l'initialisation d'un objet **SPRemoteEventResult** et se terminent par le renvoi de cet objet à SharePoint. Entre autres choses, cet objet indique à SharePoint s'il doit ou non restaurer l'événement, car la logique de gestion personnalisée a échoué. Les outils peuvent également inclure un bloc **using** dans cette méthode qui crée un objet **ClientContext**. Le gestionnaire personnalisé du complément Chain Store n'est pas amené à rappeler SharePoint, donc supprimez ce bloc. La méthode doit maintenant se présenter comme suit.

 ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
 ```

5.  Le récepteur d'événements peut être appelé par l'un des trois événements de complément possibles. Ajoutez la structure **switch** suivante à la méthode **ProcessEvent** entre les lignes qui créent et renvoient l'objet `result`. Les noms des événements disposent d'une chaîne « App », car auparavant les compléments étaient appelés des « applications », ou « apps ».

 ```cs

switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.     
     
        break;
}
 ```

6.  Notre logique d'installation appelle une procédure stockée SQL pour enregistrer le magasin de Hong Kong en tant que client dans l'application web distante. En cas d'échec de ce processus, il est très important que les signaux de gestionnaire SharePoint restaurent l'installation du complément. De ce fait, ajoutez les blocs **try/catch** suivants à la place de l'élément `TODO2`. Notez ce qui suit à propos de ce code :

  -  Vous créerez l'objet `tenantName` et la méthode `CreateTenant` à une étape ultérieure.


  -  La propriété **Status** de l'objet **SPRemoteEventResult** peut avoir trois valeurs possibles : **Continue** (par défaut), **CancelNoError** et **CancelWithError**. Les deux dernières indiquent à SharePoint de restaurer l'événement.



 ```cs

try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
 ```

7.  L'URL du site web hôte, qui correspond au discriminant client de l'exemple, fait partie des informations que SharePoint transmet au récepteur dans le paramètre **SPRemoteEventProperties**. Ajoutez la ligne suivante à la méthode **ProcessEvent** sur la ligne juste en dessous de l'initialisation de l'objet **SPRemoteEventResult**.

 ```cs

string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
 ```

8.  À présent, notre code doit faire face à un aspect particulier de la propriété **AppEventProperties.HostWebFullUrl**. Dans la plupart des autres contextes, SharePoint comprend un caractère « / » à la fin de l'URL du site web hôte, c'est pourquoi la logique de notre exemple de code considère que c'est également le cas ici. Cependant, SharePoint ajoute ce caractère à la fin de la valeur **HostWebFullUrl** si et seulement si le site web hôte est le site web racine d'une collection de sites. Étant donné que notre site de Hong Kong est un sous-site web dans la collection de sites, nous devons ajouter ce caractère pour s'assurer que la même chaîne de nom de client est utilisée tout au long de l'exemple. Ajoutez le code suivant en dessous de l'initialisation de l'objet `tenantName`.

 ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
 ```

9.  Ajoutez l'instruction **using** suivante en haut du fichier.

 ```

using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
 ```

10.  Ajoutez la méthode suivante à la classe `AppEventReceiver`. Nous n'aborderons pas ce sujet dans le détail, car l'objectif de cette série d'articles consiste à enseigner la programmation de compléments SharePoint, et non la programmation SQL Server/Azure. 

     Cette méthode crée une ligne dans la table de base de données intitulée **Clients**. En plus de la colonne **Nom**, la table comporte également une colonne **Version** dont la valeur par défaut est définie sur0000.0000.0000.0000. Dans un autre article de cette série, vous créerez une logique de première exécution qui utilise cette valeur pour déterminer si le complément a déjà été installé sur le site web hôte ou non. Si la version a la valeur 0000.0000.0000.0000, votre code déploie les listes **Employés locaux** et **Livraisons attendues**, puis augmente le numéro de version.



 ```cs

private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
 ```


## Création du gestionnaire de désinstallation
<a name="RERDebug"> </a>

 Il est généralement recommandé de gérer l'événement de désinstallation lorsque vous gérez l'événement installé. L'idée de base consiste à ce que le gestionnaire de désinstallation supprime ou recycle les éléments déployés par le gestionnaire installé. Il existe cependant de nombreuses exceptions, c'est pourquoi vous devez comprendre les cas d'utilisation de votre complément. Par exemple, une liste déployée par un complément et remplie avec le complément peut encore avoir de la valeur, même après la désinstallation du complément en question. Dans ce cas, vous ne souhaitez pas désinstaller la liste dans le gestionnaire d'événements de désinstallation.



 L'événement de désinstallation ne fonctionne pas lorsqu'un utilisateur supprime le complément de la page **Contenu du site**. Cette action ne fait que déplacer le complément dans la Corbeille du site. Un utilisateur peut le restaurer, mais la restauration ne permet pas de réexécuter le gestionnaire d'événements installés, donc, si le complément est restauré, vous souhaitez retrouver tous les éléments déployés avec le gestionnaire d'événements installés. Les composants SharePoint peuvent être déplacés de la Corbeille vers la Corbeille second niveau. Un événement de désinstallation se produit uniquement lorsqu'un complément est supprimé du second niveau. Lorsqu'un utilisateur le fait, il est impossible de restaurer le complément, c'est pourquoi nous souhaitons que le client du magasin de Hong Kong soit supprimé de la base de données d'entreprise à ce stade.




1.  Définissez la valeur de l'option **Gérer la désinstallation du complément** sur **True**. (Il est possible que cette option soit encore intitulée **Gérer la désinstallation de l'application**.) Cette opération inscrit le gestionnaire dans le fichier AppManifest.xml, de la même façon que vous avez enregistré le gestionnaire d'installation. Si vous observez le fichier, vous verrez qu'ils ont exactement la même URL. Les Outils de développement Office pour Visual Studio considèrent que vous utilisez le même fichier *.svc. C'est ce que nous allons faire dans cet exemple, mais il s'agit d'une pratique courante. 


2.  Ajoutez le code suivant à la place de l'élément `TODO3` dans le fichier AppEventReceiver.svc.cs. Notez ce qui suit à propos de ce code :

  -  La méthode `DeleteTenant` est ajoutée à l'étape suivante.


  -  L'annulation de la désinstallation du complément laisse ce dernier dans la Corbeille second niveau, à partir de laquelle il peut être restauré.



 ```cs

try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
 ```

3.  Ajoutez la méthode suivante à la classe `AppEventReceiver`.

 ```cs

private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();            
    }//dispose conn and cmd
}
 ```


> **REMARQUE**
>  Dans un article précédent de cette série, vous avez configuré le projet afin qu'il régénère la base de données d'entreprise à chaque fois que vous appuyez sur F5. Cela permet de vider la table **Clients**. 





## Exécution du complément et test du gestionnaire d'installation
<a name="RERDebug"> </a>


1.  Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio héberge l'application web distante dans IIS Express et héberge la base de données SQL dans SQL Express. Il réalise également une installation temporaire du complément sur votre site SharePoint de test, exécute le gestionnaire d'événements d'installation, puis exécute le complément immédiatement. Vous êtes invité à accorder des autorisations au complément avant l'ouverture de sa page d'accueil.


2.  Lorsque la page d'accueil du complément s'ouvre, appuyez sur l'icône d'engrenage du contrôle Chrome dans la partie supérieure, puis sélectionnez **Paramètres du compte**.


3.  Sur la page **Comptes**, cliquez sur le bouton **Afficher la version du complément**. La version est 0000.0000.0000.0000.

![Page Comptes avec l'en-tête « Paramètres du compte ». Un bouton nommé « Afficher la version du complément » sous lequel se trouve une ligne de texte indiquant « Version enregistrée : zéro zéro zéro zéro point zéro zéro zéro zéro point zéro zéro zéro zéro point zéro zéro zéro zéro ».](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)





4. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.


5. Vous allez travailler avec ce complément et la solution Visual Studio dans d'autres articles. Il est donc recommandé de retirer le complément une dernière fois lorsque vous avez terminé de travailler et n'allez pas le réutiliser pendant un moment. Cliquez avec le bouton droit de la souris sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.



## 
<a name="Nextsteps"> </a>

 Dans l'article suivant de cette série, vous allez ajouter une logique de première exécution au complément, ce qui permettra de déployer par programmation la liste **Employés locaux** et le bouton de ruban personnalisé : [ Ajout d'une logique de première exécution au complément hébergé par un fournisseur](add-first-run-logic-to-the-provider-hosted-add-in.md)




