---
title: Création de compléments pour SharePoint et d'éléments de projet à partir de modèles
ms.prod: SHAREPOINT
ms.assetid: f1b9c858-907c-4558-b671-3b488ece40a0
---



# Création de compléments pour SharePoint et d'éléments de projet à partir de modèles
Découvrez comment développer des Compléments SharePoint à l'aide de modèles pour les projets et éléments de projet de Visual Studio.
Vous pouvez développer des Compléments SharePoint à l'aide des nouveaux modèles pour les projets et éléments de projet de **vsnv**. 
  
    
    


## Modèles de projet
<a name="SP15Projecttemplates_templates"> </a>

Lorsque vous utilisez un modèle de projet dans Visual Studio, il crée une solution qui contient les éléments de projet et les fichiers dont le type de projet a besoin. Les modèles de projet ci-après figurent dans la boîte de dialogue **Nouveau projet** si vous développez le nœud **Office/SharePoint** et que vous choisissez ensuite le nœud **Compléments**. Pour plus d'informations sur les modèles de projet sous le nœud **Solutions SharePoint**, voir  [Modèles de projet et d'élément de projet SharePoint](http://go.microsoft.com/fwlink/?LinkId=255306). 
  
    
    

### Complément Office

Ce modèle crée une page web qui est hébergée au sein d'une application Office, telle qu'Excel ou Outlook. Un Complément Office offre du contenu et des fonctionnalités supplémentaires dans un document ou élément Outlook. Pour plus d'informations, voir  [Vue d'ensemble de la plateforme des compléments pour Office](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### SharePoint Add-in

Ce modèle crée un Complément SharePoint selon les informations spécifiées dans un Assistant. Ces informations comprennent les données ci-après.
  
    
    

- Nom du complément.
    
  
- Site SharePoint local ou distant à utiliser pour le débogage de votre complément.
    
  
- Type de complément à créer : complément hébergé par un fournisseur ou sur SharePoint. 
    
  
Pour plus d'informations, voir  [Compléments](sharepoint-add-ins.md).
  
    
    

### Complément de gestion des informations professionnelles dans le Cloud

En utilisant le modèle de **complément de gestion des informations professionnelles dans le Cloud** dans Visual Studio, vous pouvez créer un complément hébergé sur SharePoint permettant aux utilisateurs mobiles d'afficher, d'ajouter et de mettre à jour des données à partir d'emplacements distants à l'aide d'appareils tactiles modernes comme les téléphones et les tablettes. Pour plus d'informations, voir [Créer des compléments de gestion des informations professionnelles dans le Cloud](create-cloud-business-add-ins.md).
  
    
    

## Modèles d'élément de projet
<a name="SP15Projecttemplates_items"> </a>

Après avoir créé une solution SharePoint, vous pouvez y ajouter les éléments de projet à l'aide des modèles ci-après qui figurent dans la boîte de dialogue **Ajouter un nouvel élément** sous le nœud **Office/SharePoint**.
  
    
    

### Complément Office

Ajoute un Complément Office à votre Complément SharePoint. Vous pouvez ajouter un complément du volet Office, un complément de contenu ou un complément de messagerie. Pour plus d'informations, voir  [Vue d'ensemble de la plateforme des compléments pour Office](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### Composant WebPart client (site web hôte)

Ajoute un composant WebPart client à votre Complément SharePoint. En ajoutant un composant WebPart client, vous pouvez afficher les compléments dans les pages d'un site hôte. Ce modèle contient un seul fichier Elements.xml, dont les propriétés définissent les éléments ci-après du composant WebPart client.
  
    
    


|**Nom de la propriété**|**Description**|
|:-----|:-----|
|Composant WebPart client  <br/> |Spécifie le nom, le titre, la description et les dimensions du composant WebPart client.  <br/> |
|Contenu  <br/> |Définit l'emplacement de la page qui s'affiche dans le composant WebPart client. Cet élément possède deux propriétés :  `Type` et `Src`.  `Type` spécifie le type de composant WebPart que vous créez, comme HTML. `Src` définit l'emplacement de la page qui est affichée dans le composant WebPart client. Le modèle fait référence aux propriétés de la chaîne de requête en utilisant le modèle _ _NomPropriété__, tel que  `Src="~addinWebUrl/Pages/ClientWebPart1.aspx?Property1=_property1_"` <br/> Pour plus d'informations, voir  [Créer des composants de complément à installer avec votre complément pour SharePoint](create-add-in-parts-to-install-with-your-sharepoint-add-in.md).  <br/> |
   

### Type de contenu

Ajoute un type de contenu à votre Complément SharePoint qui est semblable aux types de contenu qui étaient utilisés dans les versions antérieures de SharePoint. Un type de contenu est un ensemble de métadonnées, de flux de travail et de comportement pour une catégorie d'éléments d'une liste ou d'une bibliothèque SharePoint. Par exemple, un élément correspond à un type de contenu de liste. D'autres types de contenu de liste comprennent les annonces, les contacts et les tâches, et ils héritent du type de contenu d'élément. Le type de contenu de contact contient des colonnes comme **Prénom**, **Nom** et **Fonction**.
  
    
    
Quand vous ajoutez un type de contenu à votre Complément SharePoint, vous spécifiez le type de contenu de base duquel hérite le nouveau type de contenu. Par exemple, il peut hériter d'une annonce, d'un contact, d'un document ou d'un type de contenu d'élément. Vous utilisez ensuite le concepteur **Type de contenu** pour configurer les colonnes du type de contenu et ses autres propriétés, comme son nom et sa description. Les valeurs que vous choisissez sont ajoutées aux éléments `ContentType` et `FieldRef` du fichier Elements.xml. Pour plus d'informations, voir [Bloc de construction : types de contenu](http://msdn.microsoft.com/library/277dfc42-d9a8-4fae-9ae1-0d202b14674f%28Office.15%29.aspx).
  
    
    

### Élément vide

Ajoute un élément de projet pour un élément vide à votre Complément SharePoint. Cet élément de projet ne contient qu'un seul fichier, Elements.xml, dans lequel vous définissez les propriétés de l'élément. En règle générale, vous utilisez un élément vide afin de définir un élément pour lequel Visual Studio ne fournit pas de modèle.
  
    
    

### Liste

Ajoute deux éléments de projet à votre Complément SharePoint : une définition de liste et une instance de la liste. Lorsque vous ajoutez une liste à votre complément, vous spécifiez quel nom lui attribuer et si elle doit être vide ou basée sur un type de liste existant. Vous pouvez également spécifier si la liste peut être personnalisée. Vous utilisez ensuite le **Concepteur de liste** pour configurer les colonnes et les affichages de la liste, ainsi que d'autres propriétés comme le nom et la description de la liste. Pour plus d'informations sur les propriétés de liste, voir [ListTemplate, élément (Modèle de liste)](http://msdn.microsoft.com/library/e565ead9-adcb-4a90-97e3-04850719420a%28Office.15%29.aspx) et [ListInstance, élément (Instance de liste)](http://msdn.microsoft.com/library/cfefe8e5-2656-4d71-bb4e-5f991a800598%28Office.15%29.aspx).
  
    
    

### Action personnalisée associée à un élément de menu

Ajoute un élément de projet qui étend l'interface utilisateur de son site hôte en ajoutant une action à un menu de listes. L'action personnalisée du menu contient un fichier Elements.xml, qui permet de définir les propriétés de l'action. Pour plus d'informations, voir  [Créer des actions personnalisées à déployer avec les compléments pour SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Module

Ajoute un élément de projet de module à votre Complément SharePoint. Les modules sont des conteneurs que vous pouvez utiliser pour inclure d'autres fichiers lorsque vous déployez votre Complément SharePoint. Pour ajouter un fichier, vous le copiez dans le projet, sous le module, dans l' **Explorateur de solutions**. Une référence au fichier est automatiquement ajoutée au fichier Elements.xml du module. Elle spécifie le chemin d'accès et l'URL du nouveau fichier. Vous pouvez supprimer le fichier Sample.txt qui est inclus avec le module, car il est proposé uniquement à des fins d'exemple.
  
    
    

### Récepteur d'événements distants

Ajoute un élément de projet pour un récepteur d'événements distants à votre Complément SharePoint et un projet d'application web à votre solution, si ce type de projet n'existe pas déjà. L'application web contient un service web associé au récepteur d'événements distants de votre Complément SharePoint. Le service web contient un fichier de code Visual Basic ou Visual C# dont le code s'exécute quand une liste, un élément de liste ou un événement d'élément web se produit dans le Complément SharePoint. Si une application web existe, elle est associée au Complément SharePoint et le service web est ajouté à cette application. Pour plus d'informations, voir  [Gestion des événements dans les compléments pour SharePoint](handle-events-in-sharepoint-add-ins.md).
  
    
    

### Action personnalisée du ruban

Ajoute un élément de projet qui étend l'interface utilisateur de son site hôte en ajoutant une action à un ruban. L'action personnalisée du ruban contient un fichier Elements.xml, qui définit les propriétés de l'action. Pour plus d'informations, voir  [Créer des actions personnalisées à déployer avec les compléments pour SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Configuration de la recherche

Ajoute un élément de projet qui vous permet d'importer des paramètres de configuration de recherche personnalisés exportés à partir d'un site SharePoint.
  
    
    

### Colonne de site

Ajoute un élément de projet pour une colonne de site à votre Complément SharePoint. La colonne de site contient un fichier Elements.xml qui définit les propriétés  `Field` de la colonne de site, notamment les données ci-après.
  
    
    


|**Nom de la propriété**|**Description**|
|:-----|:-----|
|ID  <br/> |Valeur de GUID unique pour la colonne de site.  <br/> |
|Name  <br/> |Nom unique utilisé pour faire référence à la colonne de site.  <br/> |
|DisplayName  <br/> |Nom convivial qui apparaît dans l'interface utilisateur.  <br/> |
|Type  <br/> |Type de données de la colonne de site, basée sur **SPFieldType** (booléen, de recherche ou texte, par exemple). <br/> |
|Required  <br/> |Si la colonne est obligatoire, la propriété est définie sur **True**; dans le cas contraire, elle est définie sur **False**.  <br/> |
|Group  <br/> |Cette propriété spécifie le nom du groupe auquel la colonne de site est affectée. La valeur par défaut de cette propriété est **Colonnes de site personnalisées**.  <br/> |
   
Pour plus d'informations, voir  [Bloc de construction : Colonnes et types de champs](http://msdn.microsoft.com/library/58548ade-e439-4931-82a2-fa29bd5afdb7%28Office.15%29.aspx).
  
    
    

### Flux de travail

Ajoute un élément de projet pour un flux de travail Microsoft Azure à votre Complément SharePoint. Pour plus d'informations, voir  [Flux de travail dans SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx). Lorsque vous ajoutez ce type d'élément, vous spécifiez un nom pour le flux de travail et s'il s'agit d'un flux de travail de liste ou de site. Comme les noms l'indiquent, un flux de travail de liste fonctionne uniquement avec une liste, et un flux de travail de site, uniquement avec un site SharePoint. Lors de la création du flux de travail, vous spécifiez également s'il doit être automatiquement associé à des listes et des bibliothèques, et lesquelles. Pour chaque association ajoutée, un fichier est ajouté au projet de flux de travail. Un flux de travail contient les fichiers suivants.
  
    
    


|**Nom du fichier**|**Description**|
|:-----|:-----|
|Elements.xml  <br/> |Ce fichier spécifie la configuration du flux de travail et des fichiers qu'il contient (comme le fichier workflow.xaml et les fichiers d'association) et les propriétés de chaque fichier (comme son URL, son type et son chemin d'accès). Pour chaque fichier ajouté au projet de flux de travail, une section correspondante est ajoutée au fichier Elements.xml du flux de travail. Comme les fichiers d'association des flux de travail de liste nécessitent une liste, ils ont une référence au jeton de liste. Dans un flux de travail de site, un GUID est ajouté pour le site.  <br/> > **ATTENTION**> Comme Visual Studio conserve les éléments dans le fichier Elements.xml, il n'est pas conseillé de le modifier, à moins que vous ne connaissiez l'impact de ces modifications.           |
|Workflow.xaml  <br/> |Ce fichier représente le concepteur du flux de travail. Dans ce fichier, vous ajoutez des actions au flux de travail et définissez leur code et leurs propriétés.  <br/> |
|WorkflowStartAssociation  <br/> |Ce fichier démarre manuellement le flux de travail sur SharePoint. Il est ajouté au projet de flux de travail si vous activez la case à cocher **Un utilisateur démarre manuellement le flux de travail** dans l'Assistant de flux de travail. <br/> |
|ItemAddedAssociation  <br/> |Ce fichier démarre automatiquement le flux de travail s'il en existe un lorsqu'un utilisateur crée un élément dans le site ou la liste (selon le type de flux de travail). Ce fichier est ajouté au projet de flux de travail si la case à cocher **Le flux de travail démarre automatiquement lorsqu'un élément est créé** est activée dans l'Assistant de flux de travail. <br/> |
|ItemUpdatedAssociation  <br/> |Ce fichier démarre automatiquement le flux de travail s'il en existe un lorsqu'un utilisateur modifie un élément dans le site ou la liste (selon le type de flux de travail). Ce fichier est ajouté au projet de flux de travail si la case à cocher **Le flux de travail démarre automatiquement lorsqu'un élément est modifié** est activée dans l'Assistant de flux de travail. <br/> |
|WorkflowHistoryList  <br/> |Ce fichier représente le fichier qui est ajouté au projet du flux de travail si vous créez un historique pour le flux de travail dans l'Assistant de flux de travail.  <br/> |
|WorkflowTaskList  <br/> |Ce fichier représente le fichier qui est ajouté au projet du flux de travail si vous créez une liste des tâches pour le flux de travail dans l'Assistant de flux de travail.  <br/> |
   

### Activité personnalisée de flux de travail

Ajoute un élément de projet pour une activité personnalisée de flux de travail à votre Complément SharePoint. En ajoutant une activité personnalisée de flux de travail, vous pouvez créer des actions de flux de travail supplémentaires en vue de les importer en tant qu'actions personnalisées dans SharePoint Designer 2013. L'activité personnalisée de flux de travail contient un fichier Elements.xml, qui définit les propriétés de l'action, et un fichier .xaml pour le Concepteur de flux de travail. Pour plus d'informations, voir  [Flux de travail dans SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx).
  
    
    

## Ressources supplémentaires
<a name="SP15Projecttemplates_addlresources"> </a>


-  [Outils et environnement de développement de compléments pour SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
