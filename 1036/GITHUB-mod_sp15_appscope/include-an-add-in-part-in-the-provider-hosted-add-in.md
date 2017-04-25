---
title: Insertion d'un composant de complément dans le complément hébergé par un fournisseur
ms.prod: SHAREPOINT
ms.assetid: f77d528d-8b6d-4e2e-983c-3f3957983e9f
---


# Insertion d'un composant de complément dans le complément hébergé par un fournisseur
 Découvrez comment faire apparaître un formulaire web distant sur une page SharePoint dans un Complément SharePoint hébergé par un fournisseur.
 Cet article est le sixième d'une série sur les concepts de base du développement de Compléments SharePoint hébergés par un fournisseur. Familiarisez-vous tout d'abord avec [Compléments](sharepoint-add-ins.md) et les articles précédents de cette série :





-  [Commencer à créer des compléments hébergés par un fournisseur pour SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [ Procédure pour donner à votre complément hébergé par un fournisseur l'apparence de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [ Insertion d'un bouton personnalisé dans un complément hébergé par un fournisseur](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [ Présentation rapide du modèle objet SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [ Ajout d'opérations d'écriture SharePoint au complément hébergé par un fournisseur](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)



> **REMARQUE**
>  Si vous avez suivi cette série sur les compléments hébergés par un fournisseur, vous disposez d'une solution Visual Studio que vous pouvez continuer à utiliser avec cette rubrique. Vous pouvez également télécharger le référentiel à l'adresse [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) et ouvrir le fichier BeforeAdd-inPart.sln.




 Dans cet article, vous allez ajouter un type spécial de composant WebPart, appelécomposant de complément au Complément SharePoint. Le composant de complément expose le bon de commande du complément sur une page SharePoint.
## Création du composant de complément






> **REMARQUE**
>  Les paramètres des projets de démarrage dans Visual Studio ont tendance à revenir aux valeurs par défaut à chaque fois que la solution est rouverte. Veillez à toujours suivre les étapes ci-dessous immédiatement après la réouverture de la solution d'exemple de cette série d'articles :>  Cliquez avec le bouton droit sur le nœud de la solution en haut de l' **Explorateur de solutions** et sélectionnez **Définir les projets de démarrage**. >  Assurez-vous que les trois projets sont définis sur **Début** dans la colonne **Action**. 





1.  Dans l' **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **ChainStore** et sélectionnez **Ajouter | Nouvel élément**.


2.  Sélectionnez **Composant WebPart client (site web hôte)**, nommez-le Passer une commande, puis appuyez sur **Ajouter**. (« Composant WebPart client » est un autre nom pour « composant de complément ».)


3.  Sur la page suivante de l'Assistant, sélectionnez la deuxième case d'option : **Sélectionner ou entrer l'URL d'une page web existante pour le contenu du composant WebPart client**.


4.  Dans la liste déroulante, sélectionnez l'URL de la page **OrderForm.aspx**, puis cliquez sur **Terminer**.

     Un fichier elements.xml qui définit le composant de complément est ajouté au projet et ouvert.


5.  Dans l'élément **ClientWebPart**, modifiez les attributs suivants pour indiquer ces valeurs :


|**Attribut**|**Valeur**|
|:-----|:-----|
|Titre  <br/> | Passer une commande <br/> |
|Description  <br/> | Formulaire permettant de passer une commande <br/> |
| Hauteur par défaut <br/> |320  <br/> |
 

     Laissez tous les autres attributs définis sur leurs valeurs par défaut et enregistrez le fichier.



## Exécution du complément et test du composant de complément






1.  Utilisez la touche F5 pour déployer et exécuter votre complément. Visual Studio héberge l'application web distante dans IIS Express et héberge la base de données SQL dans SQL Express. Il réalise également une installation temporaire du complément sur votre site SharePoint de test et l'exécute immédiatement. Vous êtes invité à accorder des autorisations au complément avant l'ouverture de sa page d'accueil.


2.  Lorsque la page d'accueil du complément s'ouvre, le complément a été déployé et le composant de complément **Passer une commande** est disponible pour que les utilisateurs puissent l'ajouter à toute zone de composant WebPart sur une page SharePoint sur le site web du magasin de Hong Kong. Suivez les étapes ci-dessous pour l'ajouter à la page d'accueil.

1.  Sélectionnez **Retour au site** sur le contrôle Chrome en haut de la page d'accueil pour ouvrir la page d'accueil du magasin de Hong Kong.


2.  Sur le ruban, ouvrez l'onglet **Page** et cliquez sur le bouton **Modifier**.


3.  Une fois que la page est en mode d'édition, ouvrez l'onglet **Insérer** sur le ruban, puis sélectionnez le bouton **Composant de complément**. (Il est possible qu'il s'intitule encore **Composant d'application**.)


4.  Sur le contrôle d'insertion de composant WebPart qui apparaît, sélectionnez le composant de complément **Passer une commande**. Le contrôle doit se présenter comme suit.

!\[Contrôle d'insertion du composant WebPart SharePoint. La partie appelée « Passer une commande » est mise en surbrillance. Son nom et la description apparaissent dans une case à droite.](images/aae61f89-2e9e-4808-8b0c-2439dad7c701.PNG)





5.  Cliquez dans l'une des zones de composant WebPart du formulaire. Cela permet de définir l'emplacement où le composant de complément sera inséré.


6.  Cliquez sur **Ajouter** sur le contrôle d'insertion de composant WebPart. Le composant de complément **Passer une commande** est ajouté à la zone de composants WebPart.


7.  Sur le ruban, cliquez sur ** Enregistrer**.


3.  Le bon de commande apparaît maintenant sur la page et il a la même apparence que le reste de la page. Il doit se présenter comme suit :

!\[Partie du complément Passer une commande sur la page comprenant les zones de texte Produit, Fournisseur et Quantité. Il y a également un bouton « Passer une commande ».](images/beae2e3c-c1f4-4334-8ab8-0c42252cb2a2.PNG)





4.  Indiquez des valeurs pour **Fournisseur**, **Produit** et **Quantité**, puis sélectionnez **Passer une commande**. Il semble que rien ne se produit, mais une commande est entrée dans la base de données d'entreprise. Facultativement, vous pouvez vider les champs du composant de complément en actualisant la page.


5.  Utilisez le bouton retour du navigateur pour revenir sur la page d'accueil du complément Chain Store, puis cliquez sur **Afficher les commandes**. Votre nouvelle commande est répertoriée.


6. Pour mettre fin à la session de débogage, fermez la fenêtre du navigateur ou arrêtez le débogage dans Visual Studio. Chaque fois que vous appuyez sur F5, Visual Studio retire la version précédente du complément et installe la plus récente.


7. Vous allez travailler avec ce complément et la solution Visual Studio dans d'autres articles. Il est donc recommandé de retirer le complément une dernière fois lorsque vous avez terminé de travailler et n'allez pas le réutiliser pendant un moment. Cliquez avec le bouton droit de la souris sur le projet dans l' **Explorateur de solutions** et choisissez **Retirer**.



## 
<a name="Nextsteps"> </a>

 Le complément dépend de deux listes que vous avez créées manuellement. Il est préférable que vos utilisateurs n'aient pas à faire ça. Dans l'article suivant, vous allez commencer le processus de création automatique de ces listes. La première étape importante consiste à créer des gestionnaires personnalisés pour les événements d'installation et de désinstallation d'un complément : [ Gestion des événements de complément dans le complément hébergé par un fournisseur](handle-add-in-events-in-the-provider-hosted-add-in.md)




