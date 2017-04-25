---
title: Publier une complément de gestion des informations professionnelles dans le Cloud sur SharePoint
ms.prod: SHAREPOINT
ms.assetid: b528a23f-72c3-42cb-8265-1c5b9238d404
---


# Publier une complément de gestion des informations professionnelles dans le Cloud sur SharePoint
Vous pouvez publier votre complément de gestion des informations professionnelles dans le Cloud en tant que complément SharePoint hébergé par un fournisseur. Ce type de complément vous offre la possibilité de déployer votre application web et une base de données sur un site SharePoint sur site sur Microsoft Azure, ou sur un site d'hébergement tiers. Une fois votre complément publié, d'autres peuvent l'exécuter depuis Sharepoint à partir de leurs ordinateurs et périphériques mobiles.Vous pouvez publier votre complément directement sur un site WebDeploy ou créer pour votre complément un package pouvant être déployé sur plusieurs serveurs.
## Pour publier un complément
<a name="publish"> </a>


1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel correspondant au nœud de niveau supérieur comme indiqué dans la figure 1, puis choisissez **Publier**.
    
   **Figure 1. Nœud de niveau supérieur**

  

!\[Nœud IncidentManager](images/CBA_IM_18.PNG)
  

  

  
2. Dans l'Assistant Publication d'une application LightSwitch, sur la page **Options SharePoint**, cliquez sur le bouton d'option **Hébergement par le fournisseur**, puis choisissez **Suivant**.
    
  
3. Sur la page **Configuration du serveur d'application**, cliquez sur le bouton d'option **Serveur IIS**, puis choisissez **Suivant**.
    
    > **REMARQUE**
      > Si vous disposez d'un fichier de paramètres de publication (.publishsettings ou .pubxml) créé pour un autre complément, vous pouvez l'utiliser pour fournir le reste des informations dont vous avez besoin pour la publication. Si c'est le cas, choisissez les **Paramètres d'importation** dans l'Assistant.
4. Sur la page **Sortie de publication**, choisissez l'option **Publier directement sur un serveur maintenant**, puis choisissez **Suivant**.
    
  
5. Sur la page **Paramètres de publication**, dans la zone de texte **URL du service**, entrez l'URL correspondant au serveur sur lequel vous souhaitez publier votre complément.
    
    Si vous publiez vers une société d'hébergement, la société fournit cette valeur. Elle peut se présenter sous n'importe lequel des formats suivants :
    
  -  _HostingCompanyURL_ (par exemple, `contoso.com`)
    
  
  -  `https://` _HostingCompanyURL_ (par exemple, `https://contoso.com`)
    
  
  -  `https://` _HostingCompanyURL_ `:8172/msdeploy.axd` (par exemple, `https://contoso.com:8172/msdeploy.axd`)
    
  

    Si vous exécutez une publication sur Internet Information Services (IIS) sur votre ordinateur à des fins de test, entrez  `localhost` ou le nom de votre ordinateur.
    
    Si vous exécutez une publication sur un serveur de votre réseau, entrez l'une de ces URL :
    
  -  `http://` _ServerName_
    
  
  -  `http://` _ServerName_ `/msdeployagentservice`
    
  

    > **REMARQUE**
      > Si, pour votre publication, vous vous servez d'un pare-feu, vous devrez peut-être ouvrir le port 8172. 
6. Dans la zone de texte **Site/application**, saisissez les noms du site web IIS et de votre complément.
    
    Si vous exécutez une publication vers une société d'hébergement, cette dernière vous fournit cette valeur. Il s'agit généralement d'un nom de domaine (par exemple,  `contoso.com`) ou d'un domaine et un nom de complément (par exemple,  `contoso.com/MyApp`).
    
    Si vous exécutez une publication vers IIS sur votre propre ordinateur ou vers un serveur de votre réseau interne à des fins de test, saisissez le site et le nom de complément tels qu'ils apparaissent dans Gestionnaire des services Internet. Par exemple, si vous publiez le complément MyApp vers le site Web IIS par défaut, entrez Site Web par défaut/MyApp.
    
    > **REMARQUE**
      > Si vous exécutez une publication sur un dossier web existant et souhaitez supprimer le contenu existant, cochez la case **Supprimer les fichiers supplémentaires à destination**. 
7. Dans les zones de texte **Nom utilisateur** et **Mot de passe**, entrez les informations d'authentification d'un compte disposant des droits suffisants pour exécuter les tâches de déploiement sur le serveur web de destination, puis choisissez **Suivant**.
    
    Si vous effectuez une publication sur une société d'hébergement, la société fournit ces valeurs.
    
  
8. Sur la page **Paramètres de sécurité**, cliquez sur le bouton d'option **Oui, les utilisateurs doivent se connecter en utilisant HTTPS**, puis sélectionnez **Suivant**.
    
  
9. Sur la page **Connexions de données**, sur l'onglet **Connexions de base de données**, entrez les chaînes de connexion de l'administrateur et de l'utilisateur correspondant au serveur de base de données sur lequel vous souhaitez publier votre base de données de complément.
    
    > **REMARQUE**
      > La base de données ne doit pas être placée sur le serveur sur lequel vous publiez le complément. 
10. Sur l'onglet **Sources de données attachées**, mettez à jour les chaînes de connexion de toutes les connexions supplémentaires en fonction des besoins, puis choisissez **Suivant**.
    
  
11. Sur la page **Fournisseur hébergeur**, dans la zone de texte **Où votre application LightSwitch est-elle hébergée ?**, saisissez l'URL complète de votre complément.
    
    Dans la plupart des cas, cette URL sera équivalente aux valeurs **URL du service** et **Site/Application** que vous avez saisies précédemment (par exemple, `https://contoso.com/MyApplication`).
    
  
12. Entrez les valeurs **ID client** et **Clé secrète client** correspondant à votre complément.
    
     Vous pouvez obtenir ces valeurs sur la page **appregnew** de votre site SharePoint ou à partir du tableau de bord du vendeur. Voir les [instructions d'inscription de compléments pour SharePoint 2013](http://msdn.microsoft.com/fr-fr/library/office/jj687469%28v=office.15%29.aspx).
    
  
13. Choisissez **Publier** pour publier votre complément.
    
    Une fois votre complément publié, l' **Explorateur de fichiers** s'ouvre et s'affiche dans le répertoire **Publier** pour votre projet.
    
  

## Pour insérer votre complément dans un package
<a name="package"> </a>


1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel du nœud d'application supérieur, comme indiqué à la figure 1, puis choisissez **Publier**.
    
   **Figure 1. Nœud de niveau supérieur**

  

!\[Nœud IncidentManager](images/CBA_IM_18.PNG)
  

    
    
  
2. Dans l'Assistant Publication d'application LightSwitch, sur les pages **Options SharePoint**, choisissez un bouton d'option **Hébergement par le fournisseur**, puis choisissez **Suivant**.
    
  
3. Sur la page **Configuration du serveur d'applications**, cliquez sur le bouton d'option **Serveur IIS**, puis choisissez **Suivant**.
    
    > **REMARQUE**
      > Si vous disposez d'un fichier de paramètres de publication (.publishsettings ou .pubxml) créé pour un autre complément, vous pouvez utiliser ce fichier pour fournir le reste des informations dont vous avez besoin pour la publication. Si tel est le cas, cliquez sur le bouton **Paramètres d'importation** de l'Assistant.
4. Sur la page **Sortie de publication**, cliquez sur le bouton d'option **Créer un paquet sur le disque**, puis choisissez **Suivant**.
    
  
5. Sur la page **Paramètres de publication**, dans la zone de texte **Quel sera le nom du site Web ?**, entrez le nom du site web.
    
    Le nom par défaut est le nom du complément.
    
  
6. Dans la zone de texte **Où le package sera-t-il créé ?**, entrez le chemin de l'emplacement où vous souhaitez publier les résultats, puis choisissez **Suivant**.
    
    L'emplacement par défaut est le sous-répertoire Publier du répertoire du projet.
    
  
7. Sur la page **Paramètres de sécurité**, cliquez sur le bouton d'option **Les utilisateurs doivent se connecter en utilisant HTTPS**, puis choisissez **Suivant**.
    
  
8. Sur la page **Configuration de base de données**, cliquez sur le bouton **Générer une nouvelle base de données appelée** et saisissez le nom de votre complément en tant que nom de base de données.
    
  
9. Choisissez l'onglet ** Sources de données attachées** et mettez à jour les chaînes de connexion si nécessaire, puis choisissez **Suivant**.
    
  
10. Sur la page **Fournisseur hébergeur**, dans la zone de texte **Où l'application LightSwitch sera-t-elle hébergée ?**, entrez l'URL complète de votre complément.
    
    Dans la plupart des cas, cette URL aura les mêmes valeurs **URL du service** et **Site/Application** que celles que vous avez saisies précédemment (par exemple `https://contoso.com/MyApplication`).
    
  
11. Entrez les **ID client** et **Clé secrète client** de votre complément.
    
     Vous pouvez obtenir ces valeurs sur la page **appregnew** de votre site SharePoint ou à partir du tableau de bord du vendeur. Voir les [instructions d'inscription de compléments pour SharePoint 2013](http://msdn.microsoft.com/fr-fr/library/office/jj687469%28v=office.15%29.aspx).
    
  
12. Choisissez **Publier** pour publier votre complément.
    
    Une fois votre complément publié, un fichier .zip contenant le package est placé dans le répertoire que vous avez spécifié à l'étape 4. Une fois le package créé, un administrateur de serveur peut utiliser l'outil MSDeploy pour déployer votre complément sur des serveurs qui exécutent IIS et SQL Server.
    
  

## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Enregistrer des compléments dans SharePoint 2013](register-sharepoint-add-ins-2013.md)
    
  
-  [Publier des compléments de gestion d'informations professionnelles en nuage](publish-cloud-business-add-ins.md)
    
  

