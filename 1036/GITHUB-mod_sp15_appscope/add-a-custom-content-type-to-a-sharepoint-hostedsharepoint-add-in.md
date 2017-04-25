---
title: Ajouter un type de contenu personnalisé à un complément hébergé par SharePoint pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: b52e5622-bf87-4bb1-a99a-ac1389de6651
---


# Ajouter un type de contenu personnalisé à un complément hébergé par SharePoint pour SharePoint
Découvrez comment inclure des types de contenu personnalisé dans les Compléments SharePoint.
Cet article est le quatrième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par SharePoint. Vous devez préalablement vous être familiarisé avec  [Compléments](sharepoint-add-ins.md) et les autres articles de cette série :





-  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Déployer et installer un complément hébergé par SharePoint pour SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Ajouter des colonnes personnalisées à un complément hébergé par SharePoint pour SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)



> **REMARQUE**
> Si vous avez suivi cette série sur les compléments hébergés par SharePoint, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) et ouvrir le fichier BeforeContentType.sln.




Dans cet article, vous ajoutez un type de contenu personnalisé au Complément SharePoint Employee Orientation (Orientation des employés).
## Créer le type de contenu personnalisé






1. Dans l' **Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet et choisissez **Ajouter** > **Nouveau dossier**. Nommez le dossier Types de contenu.


2. Cliquez avec le bouton droit sur le nouveau dossier, puis choisissez **Ajouter** > **Nouvel élément**. La boîte de dialogue **Ajouter un nouvel élément** s'ouvre sur le nœud **Office/SharePoint**.


3. Choisissez **Type de contenu** et attribuez-lui le nomNewEmployee, puis choisissez **Ajouter**. Lorsque l'Assistant vous demande de sélectionner le type de contenu de base, choisissez **Item**, puis cliquez sur **Terminer**.


4. Si le concepteur de type de contenu ne s'ouvre pas automatiquement, sélectionnez le type de contenu **NewEmployee** (Nouvel employé) dans l' **Explorateur de solutions** pour l'ouvrir.


5. Cliquez sur l'onglet **Type de contenu** du concepteur, puis remplissez les zones de texte de la manière suivante :

  - **Nom du type de contenu**:NewEmployee (Nouvel employé)


  - **Description**:Représente un nouvel employé.


  - **Nom de groupe**:Employee Orientation (Orientation des employés)


6. Vérifiez qu' *aucune*  des cases à cocher de l'onglet n'est activée. La case à cocher **Hériter des colonnes du type de contenu parent** peut être activée par défaut. *Veillez à la désactiver.*  L'onglet doit maintenant avoir l'aspect suivant :

   **Onglet Type de contenu**



!\[Concepteur de type de contenu affichant « NewEmployee » comme nom de type, « Représente un nouvel employé » comme description et « Orientation de l'employé » comme groupe.](images/8a9768f4-315d-45c0-88d7-687dbf84495c.PNG)





7. Cliquez sur l'onglet **Colonnes** du concepteur.


8. Dans la grille, choisissez **Cliquez ici pour ajouter une colonne** pour ouvrir la liste déroulante des colonnes, puis ajoutez la colonne **Division**. Celle-ci est répertoriée dans la liste déroulante par son nom d'affichage : **Division**. Procédez de la même façon pour la colonne **Étape de l'orientation**. (Si elles ne sont pas répertoriées, vous n'avez peut-être pas commencé avec la solution Visual Studio correcte. Démarrez avec BeforeContentType.sln.) Lorsque vous avez terminé, la grille doit présenter l'aspect suivant :

   **Onglet Colonnes**



!\[Onglet Colonnes du concepteur de type de contenu avec les éléments « Employés », « Division » et « Étape d'orientation » répertoriés dans la grille.](images/835e78b3-a073-45b2-b4ee-3f9be9d88495.PNG)





9. Enregistrez le fichier, puis fermez le concepteur.


10. À l'étape suivante, vous devez utiliser directement le code XML brut pour le type de contenu. Dans l' **Explorateur de solutions**, sélectionnez donc l'enfant du fichier elements.xml du type de contenu **NewEmployee**.


11. Des éléments **FieldRef** existent déjà dans le fichier pour les deux colonnes que vous avez ajoutées. Ajoutez des éléments **FieldRef** pour deux colonnes SharePoint intégrées comme paires des deux qui figurent déjà dans le fichier. Le code suivant correspond au balisage pour les éléments. *Vous devez utiliser ces mêmes GUID pour l'attribut d'ID, car ce sont des types de champs intégrés avec des ID fixes.*  Ajoutez-les *au-dessus*  des deux éléments **FieldRef** pour les colonnes de site personnalisées.

 ```

<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Employee" />
 ```


    Notez que nous avons attribué à ces champs un nom d'affichage personnalisé : **Employee** (Employé).


12. Enregistrez et fermez le fichier.


13. Développez le nœud **Listes** dans l' **Explorateur de solutions** et choisissez **NewEmployeeOrientation** (Orientation nouvel employé) pour ouvrir le concepteur de type de liste.


14. Cliquez sur l'onglet **Colonnes** du concepteur, puis cliquez sur le bouton **Types de contenu**.


15. Dans la boîte de dialogue **Paramètres de type de contenu**, ajoutez le type de contenu **NewEmployee**.


16. Sélectionnez le type de contenu **NewEmployee** dans la liste des types, puis cliquez sur le bouton **Définir par défaut**.


17. Sélectionnez le type de contenu **Item**, cliquez avec le bouton droit de la souris sur la petite pointe de flèche qui apparaît à gauche du nom du type de contenu, puis sélectionnez **Supprimer**.


18. Répétez la procédure ci-dessus pour le type de contenu **Folder** pour que **NewEmployee** soit le seul type de contenu répertorié. La boîte de dialogue doit maintenant présenter l'aspect suivant :

   **Boîte de dialogue Paramètres de type de contenu**



!\[Boîte de dialogue Paramètres de type de contenu avec un seul type de contenu nommé NewEmployee répertorié.](images/b90699f4-40de-4f50-ad47-3e8773d0eb92.PNG)





19.  Choisissez **OK** pour fermer la boîte de dialogue, puis enregistrez et fermez le fichier.


20. Ouvrez le fichier schema.xml.


21. Recherchez l'élément **Fields**. Celui-ci doit contenir trois éléments **Field**: **Title**, Division etOrientationStage (Étape d'orientation). (Ces éléments peuvent figurer sur une ligne unique dans ce fichier généré. Le cas échéant, séparez-les par des retours à la ligne.)


22. Laissez le fichier ouvert et dans l' **Explorateur de solutions**, développez le dossier **Colonnes de site** et le nœudDivision, puis ouvrez le fichier elements.xml file pour Division. L'élément **Field** pourDivision dans schema.xml doit reproduire précisément l'élément **Field**Division dans elements.xml. Si la correspondance n'est pas exacte, copiez l'élément **Field** du fichier elements.xml de colonnes de site et collez-le à la place de l'élément **Field** non concordant du fichier schema.xml. Fermez ensuite le fichier element.xml.


23. Ouvrez le fichier elements.xml pour OrientationStage (Étape d'orientation). Ici également, la correspondance doit être exacte entre les éléments **Field** des deux fichiers pourOrientationStage, notamment les éléments enfants tels que **CHOICES** et **MAPPINGS**. Si ce n'est pas le cas, copiez l'élément **Field** dans le fichier elements.xml et collez-le à la place de l'élément **Field** non concordant du fichier schema.xml. Fermez ensuite le fichier elements.xml.


24. Toujours dans le fichier schema.xml, dans l'élément **View** dont la valeur de **BaseViewID** est « 1 », recherchez l'élément **ViewFields** enfant, puis ajoutez-lui les deux éléments **FieldRef** en tant qu'enfants. Il se peut qu'ils soient déjà présents, mais qu'ils ne possèdent pas l'attribut **ID**. Dans ce cas, ajoutez l'attribut ID.

 ```

<FieldRef Name="Division" ID="{GUID from the Field element}" />
<FieldRef Name="OrientationStage" ID="{GUID from the Field element}" />

 ```

25. Remplacez les deux valeurs de l'attribut d'espace réservé **ID** par les GUID des éléments **Field** correspondants de l'élément **ContentType** (Type de contenu) pour **NewEmployee** (Nouvel employé) figurant plus haut dans le fichier schema.xml. N'oubliez pas les accolades « {} ».

    Les éléments **ViewFields** (champs d'affichage) pour l'élément **View** « 1 » doivent avoir l'aspect suivant. (Vos GUID peuvent différer)



 ```

<ViewFields>
   <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
   <FieldRef Name="Division" ID="{509d2d67-9a96-4596-9b3b-58449cdcc6ff}" />
   <FieldRef Name="OrientationStage" ID="{38a3b54c-acf3-4ddf-b748-55c7c28d4cc2}" />    
</ViewFields>
 ```

26. Toujours dans le fichier schema.xml, recherchez l'élément **View** dont la valeur **BaseViewID** est de « 0 ». Recherchez l'élément **ViewFields** qu'il contient.


27. Copiez l'intégralité de la section **ViewFields** de la vue « 1 » à la section **ViewFields** de la vue « 0 ». Les deux vues doivent maintenant contenir des sections **ViewFields** identiques.


28. Enregistrez le fichier schema.xml, puis fermez-le.


29. Dans le dossier **Listes**, développez le nœud **NewEmployeeOrientation** (Orientation de nouvel employé) et son instance liste enfant **NewEmployeesInSeattle** (Nouveaux employés à Seattle). Vous devez maintenant pouvoir distinguer clairement le fichier elements.xml du modèle du fichier elements.xml de l'instance. Ouvrez celui associé à l'instance.


30. Ajoutez deux éléments **Field** au premier élément **Row**, de telle sorte que l'élément **Row** présente l'aspect suivant.

 ```

<Row>
  <Field Name="Title">Tom Higginbotham</Field>
  <Field Name="Division">Manufacturing</Field>
  <Field Name="OrientationStage">Tour of building</Field>
</Row>
 
 ```

31. Enregistrez et fermez le fichier.



## Exécuter et tester le complément






1. Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio effectue une installation temporaire du complément sur votre site SharePoint de test et exécute immédiatement celui-ci. 


2. Lorsque la page par défaut du complément s'ouvre, choisissez le lien **New Employees in Seattle** (Nouveaux employés à Seattle) pour ouvrir l'instance de liste personnalisée.


3. La page de liste s'ouvre et les colonnes Division etOrientationStage (Étape d'orientation) y sont présentes. Il n'est pas nécessaire à un utilisateur de les ajouter manuellement, car elle font partie du type de contenu de la liste. Le premier élément contient les données que vous avez ajoutées.

   **Liste New Employees in Seattle (Nouveaux employés à Seattle)**



!\[Liste « Nouveaux employés de Seattle » avec les colonnes Division et Étape d'orientation déjà présentes.](images/b654af45-663e-425c-b7c7-b8b5524cb316.PNG)





4. Essayez d'ajouter de nouveaux éléments à la liste et de modifier des éléments existants.


5. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.


6. Vous allez travailler avec ce complément et la solution Visual Studio dans d'autres articles. Il est donc recommandé de retirer le complément une dernière fois lorsque vous avez terminé de travailler et n'allez pas le réutiliser pendant un moment. Cliquez avec le bouton droit sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.



## 
<a name="Nextsteps"> </a>

Dans le prochain article de cette série, vous allez ajouter un composant WebPart Affichage Liste à la page par défaut du Complément SharePoint :  [Ajouter un composant WebPart à une page dans un complément hébergé par SharePoint pour SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md).




