---
title: Associer une bibliothèque de documents à une entité
ms.prod: SHAREPOINT
ms.assetid: b00bc2ee-27dc-4ea9-bfc2-c8d46d07ea58
---


# Associer une bibliothèque de documents à une entité
En utilisant la fonctionnalité de bibliothèque dans SharePoint, vous pouvez créer ou télécharger des documents associés à des éléments individuels d'une liste ou d'une entité. Par exemple, vous pouvez utiliser une bibliothèque de documents pour stocker la documentation commerciale et les manuels correspondant à chaque produit d'une liste. Dans un complément de gestion d'informations professionnelles dans le Cloud, vous pouvez associer une bibliothèque de documents et une entité en créant une relation.
## Association d'une bibliothèque de documents

Le processus d'association d'une bibliothèque de documents à une entité implique trois étapes :




1. L'ajout d'une bibliothèque de documents SharePoint à votre projet sous la forme d'une source de données.

    > **IMPORTANTE**
      > Vous devez d'abord créer une bibliothèque de documents sur votre site SharePoint. Elle doit contenir une colonne personnalisée qui mappe un champ unique de l'entité. 
2. La création d'une relation entre une entité et la bibliothèque de documents.


3. L'ajout de la bibliothèque de documents à un écran. Cette procédure diffère selon que vous créez un nouvel écran ou que vous l'ajoutez à un écran existant.



### Pour ajouter une bibliothèque de documents


1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel du nœud **Sources de données**, puis choisissez **Ajouter une source de données**.


2. Dans l' **Assistant Attacher une source de données**, sélectionnez l'icône **SharePoint**, puis cliquez sur le bouton **Suivant**.


3. Sur la page **Entrer les informations de connexion**, dans la zone de texte **Spécifier l'adresse du site SharePoint**, entrez l'URL du site de développement SharePoint et cliquez sur le bouton **Suivant**.


4. Sur la page **Choisir vos éléments SharePoint**, dans le volet de gauche, choisissez l'élément de liste **Bibliothèques de documents** et, dans le volet de droite, cochez la case correspondant à votre bibliothèque de documents, comme indiqué à la figure 1.

   **Figure 1. Sélection de la bibliothèque de documents**



!\[Sélectionner la bibliothèque de documents](images/CBADocLibrary.PNG)


    La figure 2 illustre la bibliothèque de documents sur le site SharePoint.


   **Figure 2. Noter la colonne ProductName personnalisée**



!\[Bibliothèque de documents avec la colonne personnalisée ProductName](images/CBADocLibrary2.PNG)



    > **IMPORTANTE**
      > La bibliothèque de documents doit déjà exister et contenir une colonne personnalisée mappée sur un champ unique de votre entité. 
5. Dans **Spécifiez le nom de la source de données**, entrez un nom, puis cliquez sur le bouton **Terminer**.



### Pour créer une relation


1. Dans l' **Explorateur de solutions**, ouvrez l'entité de bibliothèque de documents, puis, dans la barre **Perspective**, choisissez l'onglet **Serveur**.


2. Dans la barre d'outils, cliquez sur **Relation**.


3. Dans la boîte de dialogue **Ajouter une nouvelle relation**, dans la liste déroulante **À**, choisissez l'entité que vous voulez associer, comme indiqué à la figure 3.

   **Figure 3. Création d'une relation**



!\[Créer une relation](images/CBARelationship.PNG)





4. Dans la liste déroulante de clé **étrangère**, choisissez la colonne personnalisée dans votre bibliothèque de documents.


5. Dans la liste déroulante de clé **principale**, choisissez le champ de votre entité mappé sur la colonne personnalisée dans la bibliothèque de documents, puis cliquez sur le bouton **OK**. Par exemple, pour une colonne personnalisée ProductName, choisissez le champ ProductName, comme indiqué à la figure 4.

   **Figure 4. Clés étrangères et principales associées.**



!\[Définir les propriétés associées](images/CBARelationship2.PNG)



    > **REMARQUE**
      > Le champ doit être du même type de données que le champ de clé **étrangère**. 

### Pour ajouter une bibliothèque de documents à un nouvel ensemble d'écrans


1. Dans l' **Explorateur de solutions**, ouvrez l'entité associée à une bibliothèque de documents, puis, dans la barre **Perspective**, sélectionnez l'onglet **HTMLClient**.


2. Dans la barre d'outils, sélectionnez **Écran**.


3. Dans la boîte de dialogue **Ajouter un nouvel écran**, dans la zone de texte **Nom d'écran défini**, ajoutez un nom pour l'ensemble d'écrans.


4. Dans la liste **Données d'écran**, choisissez votre entité.


5. Dans la liste **Données supplémentaires à inclure**, cochez la case correspondant à votre bibliothèque de documents, puis cliquez sur le bouton **OK**.

    La figure 5 illustre un écran défini pour une entité de produit.


   **Figure 5. Écran Produits défini**



!\[Boîte de dialogue Ajouter un nouvel écran](images/CBAScreenSet.PNG)


    L'écran **Affichage** créé pour l'entité contient un onglet **Documents** avec un bouton **Ajouter un document**. Le bouton permet d'afficher un menu contextuel permettant d'ajouter ou de télécharger des documents.



### Pour ajouter une bibliothèque de documents à un écran existant


1. Dans **Explorateurs de solutions**, ouvrez le menu contextuel de l'écran que vous souhaitez associer à une bibliothèque de documents, et choisissez **Ouvrir**.


2. Dans le concepteur d'écrans, choisissez le nœud **Onglets** comme indiqué à la figure 6, puis choisissez le nœud **Ajouter un onglet**.

   **Figure 6. Nœud Onglets**



!\[Ajouter un nouvel onglet](images/CBAAddTab.PNG)





3. Dans la fenêtre **Propriétés**, sélectionnez la propriété **Non complet** et entrez un nom significatif pour l'onglet que vous venez d'ajouter. Par exemple,Documents.


4. Dans le volet de gauche du concepteur d'écran, choisissez le lien **Ajouter** _DocumentLibraryName_ comme dans la figure 7, où _DocumentLibraryName_ est le nom de votre bibliothèque de documents.

   **Figure 7. Lien Ajouter ProductDocuments**



!\[Ajouter l'entité Documents](images/CBAAddDoc.PNG)





5. Dans le volet central, choisissez le nœud du nouvel onglet, développez la liste **Ajouter**, puis choisissez  _DocumentLibraryName_.


6. Développez le nœud **Barre de commandes** du nouvel onglet, comme indiqué à la figure 8 et choisissez **Barre de commandes**.

   **Figure 8. Nœud Barre de commandes**



!\[Ajouter un bouton](images/CBAAddButton.PNG)





7. Dans la boîte de dialogue **Ajouter un bouton**, acceptez les choix par défaut et cliquez sur le bouton **OK**.

    La figure 9 montre la boîte de dialogue **Ajouter un bouton** avec la méthode par défaut, **createOrUploadDocument**.


   **Figure 9. Boîte de dialogue Ajouter un bouton**



!\[Boîte de dialogue Ajouter un bouton](images/CBAAddDialog.PNG)





8. Dans la fenêtre **Propriétés**, sélectionnez la propriété **Nom complet** et saisissez un nom significatif pour le bouton, par exempleAjouter un document.

    L'écran contient maintenant un onglet **Documents** avec un bouton de barre de commandes. Le bouton affiche un menu contextuel permettant d'ajouter ou de télécharger des documents.



## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Développement de compléments de gestion d'informations professionnelles dans le Cloud](develop-cloud-business-add-ins.md)


-  [Gestionnaire d'incident : didacticiel pour complément d'informations professionnelles en nuage](incident-manager-a-cloud-business-add-in-tutorial.md)



