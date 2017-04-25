---
title: Créer des actions personnalisées à déployer avec les compléments pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: bbd11f94-1798-453e-bbb0-e5eb0df8dc75
---


# Créer des actions personnalisées à déployer avec les compléments pour SharePoint
Découvrez comment créer une action personnalisée dans SharePoint qui se déploie sur le web hôte lorsque vous déployez un Complément SharePoint.
Lorsque vous créez un Complément SharePoint, les actions personnalisées vous permettent d'interagir avec les listes et le ruban dans le site web hôte. Une action personnalisée se déploie sur le web hôte lorsque les utilisateurs finaux installent votre complément. Les actions personnalisées peuvent ouvrir une page web distante et font passer les informations par le biais de la chaîne de requête. Deux types d'actions personnalisées sont disponibles pour les compléments : les actions personnalisées de ruban et les actions personnalisées d'élément de menu.
  
    
    


## Conditions préalables à l'utilisation des exemples de cet article
<a name="SP15Createcustomactionsapps_Prereq"> </a>

Vous avez besoin d'un environnement de développement comme indiqué dans  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md).
  
    
    

### Concepts de base permettant de comprendre ce qu'est une action personnalisée

Le tableau suivant répertorie des articles permettant de comprendre les concepts et les étapes liées à un scénario d'action personnalisée.
  
    
    

**Tableau 1. Concepts de base liés aux actions personnalisées**


|**Article**|**Description**|
|:-----|:-----|
| [Compléments](sharepoint-add-ins.md) <br/> |Découvrez le nouveau modèle de complément SharePoint qui vous permet de créer des compléments, qui sont des solutions faciles à utiliser et de taille réduite destinées aux utilisateurs finaux.  <br/> |
| [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md) <br/> |Découvrez les options d'expérience utilisateur disponibles lorsque vous créez des Compléments SharePoint.  <br/> |
| [Héberger des sites web, des sites web de complément et des composants SharePoint dans SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |Découvrez la différence entre les sites web hôtes et les sites web de complément. Découvrez les composants SharePoint pouvant être inclus dans un Complément SharePoint, les composants déployés sur le site web hôte, les composants déployés sur le site web de complément et la façon dont le site web de complément est déployé dans un domaine isolé.  <br/> |
   

## Exemple de code : création d'une action personnalisée dans les bibliothèques de documents du site web hôte
<a name="SP15Createcustomactionsapps_Codeexample"> </a>

Pour créer une action personnalisée dans les bibliothèques de documents du web hôte, procédez comme suit :
  
    
    

1. Créez le Complément SharePoint et les projets web distants.
    
  
2. Ajoutez une fonction d'action personnalisée dans le projet de Complément SharePoint.
    
  
3. Ajoutez une page web de complément au projet web.
    
  

### Pour créer le Complément SharePoint et les projets web distants


1. Ouvrez Visual Studio en tant qu'administrateur. (Pour ce faire, cliquez avec le bouton droit de la souris sur l'icône Visual Studio dans le menu **Démarrer** et sélectionnez **Exécuter en tant qu'administrateur**).
    
  
2. Créez le Complément SharePoint hébergé par le fournisseur, comme expliqué dans  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md) et nommez-leCustomActionsApp. 
    
  

### Pour ajouter une page web de complément pour les actions personnalisées


1. Après que la solution Visual Studio a été créée, cliquez avec le bouton droit de la souris dans le projet d'application web (et non le projet de Complément SharePoint) et ajoutez un nouveau formulaire web en choisissant **Ajouter** > **Nouvel élément** > **Web** > **Formulaire web**. Nommez le formulaire CustomActionTarget.aspx.
    
  
2. Dans le fichier CustomActionTarget.aspx, remplacez l'ensemble de l'élément **html** et de ses enfants par le code HTML suivant. Conservez tout le balisage figurant au-dessus de l'élément **html** sans le modifier. Le code HTML contient du JavaScript qui effectue les tâches suivantes :
    
  - Fourniture d'un espace réservé pour les paramètres de la chaîne de requête.
    
  
  - Extraction des paramètres de la chaîne de requête.
    
  
  - Renvoi des paramètres dans l'espace réservé.
    
  

    > **IMPORTANTE**
      > Les jetons ItemURL et ItemID sont uniquement transmis lorsqu'un élément est sélectionné. Dans un Complément SharePoint de qualité production, votre code doit gérer les situations dans lesquelles aucun élément n'est sélectionné. Dans cet exemple, le code avertit l'utilisateur qu'aucun élément n'a été sélectionné. 

 ```HTML
  
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>Custom action target</title>
</head>
<body>
    <h2>Query string parameters passed by the custom action:</h2>

    <!-- Placeholder for query string parameters -->
    <ul id="qsparams"/>

    <!-- Main JavaScript function, renders
         the query string parameters -->
    <script lang="javascript">
        var params = document.URL.split("?")[1].split("&amp;");
        var paramsHTML = "";
      
        // Extracts the parameters from the query string.
        // Parameters are URLencoded, decode for rendering
        // in page.
        for (var i = 0; i < params.length; i = i + 1) {
            params[i] = decodeURIComponent(params[i]);
            paramsHTML += "<li>" + params[i] + "</li>";
        }

         // Alert the user when no item has been selected.
         // (The SPListItemId is the 5th parameter.)
         if (params[5] === undefined) {
            paramsHTML += "<div> <h3> No item has been selected from the list.  Please select an item. </h3> </div> ";
         }

        // Render parameters in the placeholder.
        document.getElementById("qsparams").innerHTML =
            paramsHTML;
    </script>
</body>
</html>
 ```


### Pour ajouter une action personnalisée associée à un élément de menu au projet de Complément SharePoint


1. Cliquez avec le bouton droit de la souris dans le projet de Complément SharePoint et choisissez **Ajouter** > **Nouvel élément** > **Office/SharePoint** > **Action personnalisée d'élément de menu**. 
    
  
2. Conservez le nom par défaut, puis cliquez sur **Ajouter**.
    
  
3. L'assistant **Créer une action personnalisée pour un élément de menu** vous pose une série de questions. Fournissez les réponses en vous aidant du tableau suivant :
    
   **Tableau 2. Propriétés d'action personnalisée d'élément de menu**


|**Question liée aux propriétés**|**Réponse**|
|:-----|:-----|
|Où voulez-vous exposer l'action personnalisée ?  <br/> |Choisissez **Web hôte**.  <br/> |
|Quelle est l'étendue de l'action personnalisée ?  <br/> |Choisissez **Modèle de liste**.  <br/> |
|À quel élément particulier s'applique l'étendue de l'action personnalisée ?  <br/> |Choisissez **Bibliothèque de documents**.  <br/> |
|Quel est le texte affiché sur l'élément de menu ?  <br/> |Tapez **Mon action personnalisée**.  <br/> |
|Quelle est la cible de la navigation de l'action personnalisée ?  <br/> |Choisissez la page **CustomActionAppWeb\\CustomActionTarget.aspx**.  <br/> |
   
4. Cliquez sur **Terminer**.
    
    Visual Studio génère le balisage suivant dans le fichier elements.xml de la fonction d'action personnalisée d'élément de menu :
    


 ```XML
  
<?xml version="1.0" encoding="utf-8"?>
<Elements 
    xmlns="http://schemas.microsoft.com/sharepoint/">
    <!-- RegistrationId attribute is the list type id,
        in this case, a document library (id=101). -->
  <CustomAction 
      Id="65695319-4784-478e-8dcd-4e541cb1d682.CustomAction"
      RegistrationType="List"
      RegistrationId="101"
      Location="EditControlBlock"
      Sequence="10001"
      Title="Invoke custom action">
    <!-- 
    Update the Url below to the page you want the custom action to use.
    Start the URL with the token ~remoteAppUrl if the page is in the
    associated web project, use ~appWebUrl if page is in the add-in project.
    -->
    <UrlAction Url=
"~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={ItemId}&amp;amp;SPListId={ListId}" />
  </CustomAction>
</Elements>

 ```

5. Ajoutez les paramètres de requête suivants à la fin de l'attribut **Url** de l'élément **UrlAction**:
    
     `&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}&amp;amp;SPItemURL={ItemUrl}`
    
    L'élément **UrlAction** doit avoir l'aspect suivant :
    
     ` <UrlAction Url= "~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={ItemId}&amp;amp;SPListId={ListId}&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}&amp;amp;SPItemURL={ItemUrl}" />`
    
  

> **REMARQUE**
> Dans cet exemple, la page web distante s'ouvre dans une fenêtre lorsqu'un utilisateur sélectionne l'action personnalisée dans le menu. Les actions de menus personnalisés peuvent également ouvrir la page web distante dans une boîte de dialogue à l'aide de l'attribut **HostWebDialog**. Pour plus d'informations, voir  [SharePoint-Add-in-Localization](https://github.com/OfficeDev/SharePoint-Add-in-Localization). 
  
    
    


### Pour ajouter une action personnalisée de ruban dans le projet de Complément SharePoint


1. Cliquez avec le bouton droit de la souris sur le projet de Complément SharePoint, puis choisissez **Ajouter** > **Nouvel élément** > **Office/SharePoint** > **Action personnalisée de Ruban**. 
    
  
2. Conservez le nom par défaut, puis cliquez sur **Ajouter**.
    
  
3. L'assistant **Créer une action personnalisée pour le Ruban** vous pose une série de questions. Fournissez les réponses en vous aidant du tableau suivant :
    
   **Tableau 3. Propriétés d'action personnalisée de ruban**


|**Question liée aux propriétés**|**Réponse**|
|:-----|:-----|
|Où voulez-vous exposer l'action personnalisée ?  <br/> |Choisissez **Web hôte**.  <br/> |
|Quelle est l'étendue de l'action personnalisée ?  <br/> |Choisissez **Modèle de liste**.  <br/> |
|À quel élément particulier s'applique l'étendue de l'action personnalisée ?  <br/> |Choisissez **Bibliothèque de documents**.  <br/> |
|Où se trouve le contrôle ?  <br/> |Choisissez **Ribbon.Documents.Manage**.  <br/> |
|Quel est le texte affiché sur l'élément de menu ?  <br/> |Entrez **Mon bouton de ruban personnalisé**.  <br/> |
|Quelle est la cible de la navigation de l'action personnalisée ?  <br/> |Choisissez la page **CustomActionAppWeb\\CustomActionTarget.aspx**.  <br/> |
   
4. Visual Studio génère le balisage suivant dans le fichier elements.xml file de la fonction d'action personnalisée de ruban :
    
 ```XML
  
<?xml version="1.0" encoding="utf-8"?>
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <CustomAction Id="85691508-c076-4f43-93d4-96b4d5253a09.RibbonCustomAction1"
                RegistrationType="List"
                RegistrationId="101"
                Location="CommandUI.Ribbon"
                Sequence="10001"
                Title="Invoke &amp;apos;RibbonCustomAction1&amp;apos; action">
    <CommandUIExtension>
      <!-- 
      Update the UI definitions below with the controls and the command actions
      that you want to enable for the custom action.
      -->
      <CommandUIDefinitions>
        <CommandUIDefinition Location="Ribbon.Documents.Manage.Controls._children">
          <Button Id="Ribbon.Documents.Manage.RibbonCustomAction1Button"
                  Alt="My Custom Ribbon Button"
                  Sequence="100"
                  Command="Invoke_RibbonCustomAction1ButtonRequest"
                  LabelText="My Custom Ribbon Button"
                  TemplateAlias="o1"
                  Image32by32="_layouts/15/images/placeholder32x32.png"
                  Image16by16="_layouts/15/images/placeholder16x16.png" />
        </CommandUIDefinition>
      </CommandUIDefinitions>
      <CommandUIHandlers>
        <CommandUIHandler Command="Invoke_RibbonCustomAction1ButtonRequest"
                          CommandAction="~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={SelectedItemId}&amp;amp;SPListId={SelectedListId}"/>
      </CommandUIHandlers>
    </CommandUIExtension >
  </CustomAction>
</Elements> 

 ```

5. Ajoutez les paramètres de requête suivants à la fin de l'attribut **CommandAction** de l'élément **CommandUIHandler**:
    
     `&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}`
    
    L'élément **CommandUIHandler** doit présenter l'aspect suivant :
    
     ` <CommandUIHandler Command="Invoke_RibbonCustomAction1ButtonRequest" CommandAction="~remoteAppUrl/CustomActionTarget.aspx?{StandardTokens}&amp;amp;SPListItemId={SelectedItemId}&amp;amp;SPListId={SelectedListId}&amp;amp;SPSource={Source}&amp;amp;SPListURLDir={ListUrlDir}" />`
    
    > **REMARQUE**
      > Les actions personnalisées de ruban utilisent **SelectedListId** et **SelectedItemId**. **ListId** et **ItemId** fonctionnent uniquement avec les actions personnalisées d'élément de menu.

### Définir la page de démarrage du complément sur la page d'accueil du site web hôte


1. L'exemple de Complément SharePoint suivant n'a pas de site web de complément et son application web distante existe uniquement pour héberger le formulaire. Par conséquent, la page de démarrage du complément doit être définie sur la page d'accueil du site web hôte. 
    
    Pour commencer, sélectionnez le projet de Complément SharePoint (et non le projet d'application web) dans l' **Explorateur de solutions** et copiez la valeur de la propriété **Site URL**, avec le protocole (par exemple **https://contoso.sharepoint.com**) dans le presse-papiers. 
    
  
2. Ouvrez le manifeste du complément, puis collez l'URL dans la zone **Page de démarrage**.
    
  
3. Vous pouvez également supprimer la page Default.aspx du projet d'application web, car elle n'est pas utilisée dans le Complément SharePoint.
    
  

### Pour créer et exécuter la solution


1. Appuyez sur la touche F5.
    
    > **REMARQUE**
      > Lorsque vous appuyez sur F5, Visual Studio génère la solution, déploie le complément et ouvre la page des autorisations pour le complément. 
2. Cliquez sur le bouton **Trust It** (Faire confiance). La page par défaut de votre site de développeur s'ouvre.
    
  
3. Accédez à une bibliothèque de documents sur le site web hôte.
    
   **Démarrage d'une action de menu personnalisé**

  

!\[La bibliothèque de documents avec une légende pour un document, le menu ouvert par le bouton de légende et le menu avancé s'ouvrent.](images/477cecf5-03ff-46ff-9c25-a5f9a86d43f4.png)
  

  

  
4. Choisissez le bouton de légende ( **...**) de n'importe quel document. La légende s'ouvre.
    
  
5. Choisissez le bouton de légende ( **...**) dans la légende. 
    
  
6. Choisissez **Advanced** (Avancé).
    
  
7. Choisissez **Mon action de menu personnalisé** dans le menu contextuel. La page web qui s'ouvre doit avoir l'aspect ci-dessous :
    
   **Page web distante comportant des paramètres de l'action personnalisée.**

  

!\[Page web avec des paramètres à partir d'une action personnalisée](images/CustomActions_target.png)
  

  

  
8. Cliquez sur le bouton **Précédent** de votre navigateur pour revenir à la bibliothèque.
    
   **Lancement d'une action de ruban personnalisé**

  

!\[Bibliothèque de documents avec un document sélectionné, l'onglet Fichier ouvert sur le ruban et le bouton personnalisé sur le ruban.](images/b315cb68-ff6a-4770-a1dc-738696ab71d2.png)
  

  

  
9. Sélectionnez un document.
    
  
10. Ouvrez l'onglet **Fichier** sur le ruban.
    
  
11. Choisissez **Mon bouton Ruban personnalisé**. La même page web distante apparaît.
    
  

**Tableau 4. Dépannage de la solution**


|**Problème**|**Solution**|
|:-----|:-----|
|Visual Studio n'ouvre pas le navigateur une fois que vous avez appuyé sur la touche F5.  <br/> |Définissez le projet de Complément SharePoint en tant que projet de démarrage.  <br/> |
|Les jetons dans l'URL ne sont pas résolus une fois que vous avez appuyé sur la touche F5 dans Visual Studio.  <br/> |Accédez à la page **Contenu du site** dans le site web hôte et cliquez sur l'icône de votre complément. <br/> |
   

## Étapes suivantes
<a name="SP15Createcustomactionsapps_Nextsteps"> </a>

Cet article a décrit la manière de créer une action personnalisée dans un Complément SharePoint. Dans une prochaine étape, vous pourrez découvrir d'autres composants d'expérience utilisateur disponibles dans les Compléments SharePoint. Pour plus d'informations, voir :
  
    
    

-  [Exemple de code : ouverture d'une page web de complément distante à l'aide d'une action personnalisée ECB](http://code.msdn.microsoft.com/SharePoint-2013-Open-e0ca1826)
    
  
-  [SharePoint-Add-in-Localization](https://github.com/OfficeDev/SharePoint-Add-in-Localization)
    
  
-  [Exemple de code : utilisation des actions personnalisées et de la bibliothèque à domaines multiples pour commander des livres](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)
    
  
-  [Utilisation d'une feuille de style de site web SharePoint dans les compléments pour SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)
    
  
-  [Utiliser le contrôle de chrome client dans les compléments pour SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [Créer des composants de complément à installer avec votre complément pour SharePoint](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## Ressources supplémentaires
<a name="SP15Createcustomactionsapps_AddResources"> </a>


-  [Configurer un environnement de développement local pour les compléments pour SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Conception de l'expérience utilisateur pour les compléments dans SharePoint](ux-design-for-sharepoint-add-ins.md)
    
  
-  [Conseils pour la conception de l'expérience utilisateur des compléments pour SharePoint](sharepoint-add-ins-ux-design-guidelines.md)
    
  
-  [Créer des composants d'expérience utilisateur dans SharePoint 2013](create-ux-components-in-sharepoint-2013.md)
    
  
-  [Penser de trois manières différentes les options de conception des compléments pour SharePoint](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [Aspects importants du contexte de développement et de l'architecture des compléments pour SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

