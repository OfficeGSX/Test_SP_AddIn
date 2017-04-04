---
title: Présentation rapide du modèle objet SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6b8b55f8-9370-43a0-af8d-e07d1028a075
---


# Présentation rapide du modèle objet SharePoint
 Voici une présentation rapide de certaines classes principales du modèle objet SharePoint.
 Cet article est le quatrième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par un fournisseur. Familiarisez-vous tout d'abord avec [Compléments](sharepoint-add-ins.md) et les articles précédents de cette série :
  
    
    


-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [ Procédure pour donner à votre complément hébergé par un fournisseur l'apparence de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [ Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  

> [!REMARQUE]
>  Si vous avez suivi cette série sur les compléments hébergés par un fournisseur, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) et ouvrir le fichier BeforeSharePointWriteOps.sln.
  
    
    

 Dans cet article, vous allez cesser de coder pendant un court instant pour découvrir rapidement le modèle objet côté client SharePoint (CSOM). Ce modèle est largement documenté sur MSDN avec des rubriques de référence, des procédures et des exemples de code. Dans cet article, nous pouvons fournir uniquement une partie infime de la pointe de l'iceberg, mais nous pensons qu'une présentation, aussi brève soit-elle, vous permettra de beaucoup mieux comprendre le code que vous allez rencontrer dans les articles suivants.
## Hiérarchie de contenu

 Le tableau ci-dessous présente la hiérarchie de contenu dans SharePoint et les classes CSOM qui le représentent. Toutes ces entités ont des enfants, qui sont du premier type inférieur.
  
    
    

|
|
|**Entité**|**Classe**|**Remarques**|
|:-----|:-----|:-----|
| Batterie de serveurs SharePoint locale ou abonnement SharePoint Online (également appelé client) <br/> || Dans un CSOM, l'accès à ce niveau est uniquement limité par programmation. Par exemple, il n'existe aucune classe de batterie, d'abonnement ou de client. (Le modèle objet côté serveur SharePoint, qui ne peut pas être utilisé dans les compléments, permet d'accéder par programmation à ces entités.) <br/> |
|collection de sites  <br/> |**Site** <br/> | Collection de sites web regroupés pour des raisons essentiellement administratives, ainsi que pour héberger des composants SharePoint, tels que des pages maîtres ou des groupes de sécurité personnalisés, qui peuvent être appliqués à l'ensemble des sites enfant. Tous les sites appartiennent à la même collection de sites. <br/> |
|site web  <br/> |**Web** <br/> | Ensemble de pages et composants SharePoint pouvant comporter des sous-sites web. <br/> |
|liste  <br/> |**List** <br/> | Les bibliothèques de documents et d'autres types de bibliothèques de fichiers sont également à ce niveau. <br/>  Une bibliothèque de documents est un type spécial de liste dans laquelle chaque ligne comprend un document joint. Les autres colonnes sont des données sur le document, comme son auteur, la date de sa dernière modification et la personne l'ayant vérifié. <br/> |
| élément de liste <br/> |**ListItem** <br/> | Ligne d'une liste comportant des valeurs particulières dans les champs de la ligne. Elle a aussi un type. Voir la ligne suivante. <br/> |
| élément de liste <br/> |**Content Type** <br/> | Type d'un élément de liste. Ces éléments sont représentés par la classe **ContentType**. Chacun constitue essentiellement un ensemble de colonnes et de métadonnées. Le plus simple est le type de contenu intégré **Item**. Tous les autres types de contenu sont dérivés du type **Item**. SharePoint inclut de nombreux types de contenu intégrés, tels que Événement et Annonce.  <br/> |
| colonne <br/> |**Field** <br/> | Un objet **Field** n'inclut pas seulement des informations sur le type de données sous-jacent, mais également des informations sur la façon dont les données sont mises en forme et affichées sur les formulaires, tels que les formulaires pour la création, l'affichage et la modification d'éléments de liste spécifiques. <br/> |
   

  
    
    
 Vous pouvez créer par programmation des listes, types de contenu, types de colonne et éléments de liste personnalisés.
  
    
    
 En plus du contenu, le CSOM vous permet d'avoir accès aux utilisateurs, groupes, rôles et autorisations, à la taxonomie, la recherche, et bien plus.
  
    
    

## Exécution côté client et traitement par lot
<a name="CSOMBatching"> </a>

 Le CSOM utilise un système de traitement par lot. Des fragments de code géré sont convertis en XML et envoyés au serveur dans une seule requête HTTP. Pour chaque commande, un appel de modèle objet côté serveur correspondant est réalisé, et le serveur renvoie une réponse au client au format JSON (JavaScript Object Notation).
  
    
    
 Le code SharePoint sur un client commence par récupérer un objet de contexte client qui représente le contexte de la demande actuelle, y compris l'identité du site web SharePoint (et sa collection de sites parent). Grâce à ce contexte, vous pouvez accéder aux objets CSOM. Le code suivant représente la structure de base que vous verrez très souvent. Notez ce qui suit à propos de ce code :
  
    
    

-  L'objet `spContext` est de type **SharePointContext** et est défini dans le fichier SharePointContext.cs (ou .vb) qui est généré par les Outils de développement Office pour Visual Studio. Ce fichier peut être modifié, mais il est rare que vous deviez le faire. Pour la plupart des projets de Complément SharePoint, ce fichier et le fichier TokenHelper.cs (ou .vb), qui sont également générés par les outils, fonctionnent en tant qu'extensions du CSOM lui-même.
    
  
-  L'objet `clientContext` est un élément **ClientContext** de type CSOM.
    
  
-  La méthode **ExecuteQuery** regroupe le code d'opération CRUD et un message XML qu'elle envoie au serveur SharePoint. Une fois sur le serveur, ce code est traduit en code de modèle objet côté serveur et exécuté.
    
  



```cs

using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    // CRUD operation or query code goes here.

    clientContext.ExecuteQuery();
}
```

 Vous avez vu un exemple de ce modèle dans l'article précédent de cette série, dans la méthode `GetLocalEmployeeName` présentée ci-dessous. Notez les points suivants concernant cette méthode :
  
    
    

-  Les deux premières lignes du bloc **using** s'affichent pour obtenir des références à la liste et l'objet d'élément de liste. En réalité, lorsque ces lignes s'exécutent dans l'exécution côté client SharePoint, elles sont simplement converties au format XML. La méthode **ExecuteQuery** envoie ce code XML au serveur.
    
  
-  La méthode **Load** ajoute un élément supplémentaire au message : elle indique au serveur d'envoyer l'objet spécifié au client. La méthode **ExecuteQuery** reçoit cet objet (en tant que JSON) et l'utilise pour initialiser la variable côté client `selectedLocalEmployee`. Le code côté client à la suite fait référence à l'objet **ListItem** et ses membres. Comme vous pouvez le constater, la ligne suivante fait référence à la valeur du champ « Titre » de l'élément. Cette ligne aurait généré une exception si la méthode **Load** n'avait pas été appelée, car l'objet n'existe pas vraiment côté client jusqu'à ce qu'il soit chargé.
    
  



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


## 
<a name="Nextsteps"> </a>

 Dans l'article suivant, nous allons revenir au codage et allons apprendre à écrire des données dans SharePoint : [ Ajout d'opérations d'écriture SharePoint au complément hébergé par un fournisseur](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
  
    
    

