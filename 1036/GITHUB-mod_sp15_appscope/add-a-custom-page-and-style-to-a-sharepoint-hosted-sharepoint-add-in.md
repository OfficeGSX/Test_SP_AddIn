---
title: Ajouter une page et un style personnalisés à un complément hébergé par SharePoint pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 91a20d79-318f-44ce-9877-3fa07d03aa09
---


# Ajouter une page et un style personnalisés à un complément hébergé par SharePoint pour SharePoint
Apprenez à inclure une page personnalisée et un fichier CSS dans des Compléments SharePoint.
Cet article est le septième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par SharePoint. Vous devez tout d'abord avoir pris connaissance de  [Compléments](sharepoint-add-ins.md) et des articles précédents de la série :





-  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Déployer et installer un complément hébergé par SharePoint pour SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Ajouter des colonnes personnalisées à un complément hébergé par SharePoint pour SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Ajouter un type de contenu personnalisé à un complément hébergé par SharePoint pour SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Ajouter un composant WebPart à une page dans un complément hébergé par SharePoint pour SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Ajouter un flux de travail à un complément hébergé par SharePoint pour SharePoint](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)



> **REMARQUE**
> Si vous avez suivi cette série sur les compléments hébergés par SharePoint, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) et ouvrir le fichier BeforePage.sln.




Dans cet article, vous ajoutez une page d'aide au Complément SharePoint Employee Orientation (Orientation des employés) et la configurez pour utiliser une feuille de style CSS personnalisée. 
## Ajouter une page


1. Dans l' **Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le dossier **Pages** et choisissez **Ajouter** > **Nouvel élément**. La boîte de dialogue **Ajouter un nouvel élément** s'ouvre dans le nœud **Office/SharePoint**.


2. Choisissez **Page** et attribuez-lui le nomHelp.aspx. 


3. Recherchez les deux éléments **asp:Content** dans le fichier, et ajoutez la troisième balise **asp:Content** suivante entre les deux.

 ```HTML

<asp:Content ContentPlaceHolderID="PlaceHolderPageTitleInTitleArea" runat="server">
    Help
</asp:Content> 
 ```

4. Recherchez l'élément **asp:Content** portant l'ID **PlaceholderAdditionalPageHead**, et ajoutez-y le balisage suivant.

 ```HTML

<link rel="Stylesheet" type="text/css" href="../Content/App.css" />
 ```

5. Recherchez l'élément **asp:Content** portant l'ID **PlaceHolderMain** et supprimez les éléments enfants éventuels qu'il contient.


6. Ajoutez ce qui suit en tant que contenu au même élément **asp:Content**.

 ```HTML
  <H3>Having a problem with the add-in?</H3>
<p> Call the help line for Fabrikam Add-ins:</p>
<p>1-555-555-5555</p>
 ```

7. Enregistrez et fermez le fichier.


8. Ouvrez le fichier Default.aspx.


9. Recherchez l'élément **asp:Content** portant l'ID **PlaceHolderMain** et ajoutez le balisage suivant à la fin de celui-ci.

 ```HTML

<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Pages/Help.aspx';"
    Text="Get help for the Employee Orientation add-in" /></p>

 ```

10. Enregistrez et fermez le fichier.



## Ajouter une classe de style à la feuille de style






1. Dans l' **Explorateur de solutions**, ouvrez le fichier app.css figurant dans le dossier **Contents**, puis ajoutez-y la ligne suivante.

 ```

p {color: green;}
 ```

2. Enregistrez et fermez le fichier.



## Exécuter et tester le complément






1. Appuyez sur F5 pour déployer et exécuter votre complément. Visual Studio procède à une installation temporaire sur votre site SharePoint de test et exécute immédiatement celui-ci. 


2. Lorsque la page par défaut du complément s'ouvre, cliquez sur le lien **Obtenir de l'aide pour le complément Employee Orientation (Orientation des employés)** pour ouvrir la page **Aide**. 

    La page personnalisée s'ouvre et les deux lignes que vous avez placées dans les balises <p> apparaissent en vert.


   **Page d'aide**



!\[Page SharePoint avec le titre « Aide ». Il existe une ligne d'en-tête en noir, suivie de deux lignes de texte en vert.](images/2df51ab0-5b24-4a37-8b6a-6e95dbb1aeaa.PNG)





3. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.


4. Vous allez travailler avec ce complément et la solution Visual Studio dans d'autres articles. Il est donc recommandé de retirer le complément une dernière fois lorsque vous avez terminé de travailler et n'allez pas le réutiliser pendant un moment. Cliquez avec le bouton droit de la souris sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.



## 
<a name="Nextsteps"> </a>

Dans le prochain article de cette série, vous ajouterez un rendu côté client personnalisé à une colonne de liste dans un Complément SharePoint :  [ Ajout de rendu côté client personnalisé à un complément SharePoint hébergé par SharePoint](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md).




