---
title: Créer un complément de gestion des informations professionnelles dans le Cloud
ms.prod: SHAREPOINT
ms.assetid: a42a0e53-5d19-4f0e-b271-23a01acc4946
---


# Créer un complément de gestion des informations professionnelles dans le Cloud
En utilisant le modèle de complément de gestion d'informations professionnelles dans le Cloud dans Visual Studio, vous pouvez créer dans SharePoint 2013 ou SharePoint sur Office 365 des compléments qui sont optimisés pour l'ajout et la gestion des données.
> **REMARQUE**
> Vous pouvez également créer un Complément SharePoint à l'aide du modèle de complément pour SharePoint 2013. 





### Pour créer un complément de gestion des informations professionnelles dans le Cloud


1. Dans la barre de menus, choisissez **Fichier** > **Nouveau** > **Projet**.

    La boîte de dialogue **Nouveau projet** s'ouvre.


2. Dans la liste des modèles, développez le nœud **Visual Basic** ou **Visual C#**, puis le nœud **Office/SharePoint**, choisissez **Compléments**, puis choisissez **Complément de gestion des informations professionnelles dans le Cloud**, comme indiqué à la figure 1.

   **Figure 1. Modèle de complément de gestion des informations professionnelles dans le Cloud**



!\[Modèle de création d'application de gestion des informations professionnelles dans le Cloud](images/CloudBusinessApptemplate.PNG)





3. Dans la zone de texte **Nom**, saisissez le nom de votre projet, puis cliquez sur le bouton **OK**.

    L'Assistant **Nouveau complément de gestion des informations professionnelles dans le Cloud** s'ouvre.


4. Dans l'assistant **Nouveau complément de gestion des informations professionnelles dans le Cloud**, saisissez l'URL de votre serveur SharePoint ou votre site de développeur Office 365 dans la figure 2, puis cliquez sur le bouton **Terminer**.

   **Figure 2. URL SharePoint**



!\[URL SharePoint](images/SiteURL.PNG)


    L'URL doit prendre la forme https://  _Monsite_.sharepoint.com/sites/Developer/.

    Une nouvelle solution est ajoutée à l'Explorateur de solutions avec quatre projets : un projet de niveau supérieur, et quatre autres projets, respectivement **HTMLClient**, **Server** et **SharePoint**.



### Pour modifier le site d'un complément de gestion des informations professionnelles dans le Cloud


1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel correspondant au nœud de niveau supérieur et choisissez **Propriétés**, comme indiqué à la figure 3.

   **Figure 3. Nœud de projet de niveau supérieur**



!\[Nœud de projet de niveau supérieur](images/Top-levelprojectnode.PNG)


    Le concepteur d'applications s'ouvre.


2. Dans le concepteur d'applications, cliquez sur l'onglet **SharePoint**, comme illustré à la figure 4.

   **Figure 4. Onglet SharePoint**



!\[Onglet Propriétés SharePoint](images/SharePointtab.PNG)





3. Dans la liste **URL de site**, choisissez une URL existante, ou saisissez l'URL de site de votre serveur SharePoint ou de votre site de développeur Office 365 .


4. Cliquez sur le bouton **Valider** pour vérifier l'URL.



## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Développement de compléments de gestion d'informations professionnelles dans le Cloud](develop-cloud-business-add-ins.md)


-  [Créer des compléments de gestion des informations professionnelles dans le Cloud](create-cloud-business-add-ins.md)



