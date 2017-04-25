---
title: Publier des compléments pour SharePoint à l'aide de Visual Studio
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# Publier des compléments pour SharePoint à l'aide de Visual Studio
Découvrez comment publier votre Complément SharePoint à l'aide de Microsoft Visual Studio 2013 ou de Visual Studio 2012. Si le complément est associé à une application web, déployez-la d'abord. Ensuite, comme pour tous les Compléments SharePoint, créez un package pour le Complément SharePoint, puis publiez-le. Vous pouvez également choisir d'envoyer votre complément en vue de son ajout dans l'Office Store.
## Conditions requises
<a name="Prereq"> </a>






-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284)

    -ou-

     [Visual Studio 2012](https://www.microsoft.com/fr-fr/download/details.aspx?id=30682) et les Outils de développement Office pour Visual Studio. Pour télécharger les outils, reportez-vous à la section « Outils » de la [page de téléchargement](http://go.microsoft.com/fwlink/?LinkId=234393). (Le nouveau Gestionnaire de publication n'est pas disponible dans Visual Studio 2012 ou versions antérieures.)


- Microsoft SharePoint 2013



## Publier une application à l'aide de Visual Studio 2013
<a name="VS2013"> </a>

Si votre Complément SharePoint hébergé par le fournisseur comporte une application Web, déployez d'abord les fichiers associés à cette dernière. Ensuite, comme pour tous les Compléments SharePoint, créez un package pour le Complément SharePoint et publiez-le.




> **IMPORTANTE**
> Pour vous assurer que votre ID client SharePoint et votre clé secrète client sont publiés avec votre projet web, afin de permettre à votre contenu web d'accéder aux données SharePoint, publiez votre projet de Complément SharePoint à partir de la page **Publier votre complément**. Vous pouvez accéder à cette page en ouvrant le menu contextuel du Complément SharePoint, et non celui de l'application web, puis en sélectionnant la commande **Publier**. 





### Étape 1 : Déploiement de l'application web

Votre Complément SharePoint est généralement associé à une application web hôte que vous devez déployer sur un serveur web. Pour plus d'informations sur l'utilisation de l'assistant Publier le site web, voir l'article  [Procédure : Déployer un projet d'application Web à l'aide de la publication en un clic dans Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).




### Pour ouvrir la page Publier votre complément


- Dans l' **Explorateur de solutions**, ouvrez le menu contextuel du projet de Complément SharePoint, puis sélectionnez **Publier**.

    La page **Publier votre complément** s'ouvre.



### Pour sélectionner ou créer un profil


- Dans la liste **Profil actuel**, sélectionnez un profil à importer ou sélectionnez **<Nouveau…>** pour créer un profil.

    Un profil de publication spécifie le serveur sur lequel vous déployez l'application web, les informations d'identification requises pour la connexion au serveur, les bases de données à déployer (le cas échéant), ainsi que d'autres options de déploiement. Vous pouvez créer différents profils de publication pour répondre à vos besoins. Par exemple, vous pouvez créer un profil à des fins de test et un autre pour la publication.

    Si vous sélectionnez **<Nouveau…>**, l'Assistant **Créer un profil de publication** apparaît. Celui-ci permet d'importer un profil de publication à partir d'un fournisseur hébergeur de sites web, tel que Azure, ou de créer un profil, puis d'ajouter manuellement le nom du serveur, les informations d'identification et les autres paramètres. Si vous avez créé un profil au lieu d'importer un profil existant, vous devrez fournir l'ID client et la clé secrète client, conformément à ce qui est indiqué dans les pages [inscrire des compléments SharePoint 2013](http://msdn.microsoft.com/library/jj687469.aspx) et [Création ou mise à jour d'ID et de clés secrètes client dans le service Mon tableau de bord vendeur](http://msdn.microsoft.com/library/office/jj220036.aspx).

    Si vous prévoyez de soumettre votre Complément SharePoint à l'Office Store, veillez à utiliser l'ID et la clé secrète clients créés dans Mon tableau de bord vendeur. Vous pouvez utiliser des ID et des clés secrètes clients générés à l'aide de la page appregnew.aspx lors de la phase de déploiement, mais les compléments que vous soumettez à l'Office Store doivent utiliser les ID et les clés secrètes clients obtenus à partir de Mon tableau de bord vendeur. Vous devez également créer le profil de publication sur votre site Azure, puis l'importer dans Visual Studio au lieu de créer un profil dans l'Assistant **Créer un profil de publication**. Lorsque vous créez un profil dans Azure, tous les paramètres de l'onglet **Connexion** sont déjà définis dans Visual Studio. Pour plus d'informations sur l'importation ou la création d'un profil de publication, voir [Création d'un profil de publication](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile).

    > **CONSEIL**
      > Si vous ne pouvez pas publier de contenu Web directement, vous pouvez créer un package Web Deploy pouvant être déployé pour vous par un administrateur. Pour créer un package Web Deploy, créez un profil, sélectionnez l'onglet **Connexion**, puis cliquez sur **Package Web Deploy** dans la liste **Méthode de publication**. 

### Pour déployer votre projet d'application web


1. Sur la page **Publier votre complément**, cliquez sur le bouton **Déployer votre projet web**.

    La boîte de dialogue **Publier le site web** s'affiche.


2. Dans les onglets **Connexion** et **Paramètres**, indiquez les valeurs manquantes.

    Pour modifier la méthode utilisée pour publier les fichiers de votre Complément SharePoint ou si le complément utilise une base de données externe, sélectionnez l'onglet **Paramètres**. Consultez la section concernant la configuration de l'onglet Paramètres sur la page relative au  [déploiement d'un projet web à l'aide de la publication en un clic dans Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).


3. Pour vérifier les éléments qui seront modifiés lors du déploiement de l'application Web, sélectionnez **Démarrer l'aperçu** sous l'onglet **Aperçu**.


4. Sélectionnez **Publier** pour déployer le projet d'application Web.



### Étape 2 : Création du package du complément


1. Sur la page **Publier votre complément**, cliquez sur le bouton **Créer le package de complément**.

    L'Assistant **Publication de compléments Office et SharePoint** s'affiche.


2. Dans la zone de texte **Où votre site web est-il hébergé ?**, entrez l'URL du site Web qui hébergera les fichiers de contenu de votre Complément SharePoint.

    Vous devez spécifier une adresse commençant par le préfixe « https ». Pour plus d'informations, voir  [Pourquoi mes compléments doivent-ils être sécurisés par une protection SSL ?](http://msdn.microsoft.com/library/jj591603#bk_q7).

    > **REMARQUE**
      > Les sites web Azure fournissent automatiquement un point de terminaison HTTPS. Si vous publiez votre complément sur un site de l'Office Store ou sur l'Office Store, l'adresse doit commencer par un préfixe HTTPS. Cependant, si vous publiez le complément sur un site local, vous pouvez utiliser un préfixe HTTP. 

    L'ID client que vous avez entré dans le profil de publication devrait déjà apparaître dans la zone de texte **Quel est l'ID client du complément ?**.

    Si vous avez utilisé une valeur d'espace réservé pour l'ID client jusqu'à présent, vous devez maintenant ajouter un ID client réel. Cette information est incorporée dans le package de l'application et permet à votre contenu Web de communiquer avec SharePoint sur le site actif.


3. Cliquez sur le bouton **Terminer**.

    Visual Studio génère les fichiers nécessaires pour publier votre Complément SharePoint, puis ouvre le dossier de sortie de publication. Pour plus d'informations sur l'installation du complément, voir  [Installer et gérer des compléments SharePoint 2013](http://technet.microsoft.com/library/fp161232.aspx).



### Étape 3 : Publication de votre Complément SharePoint sur l'Office Store

Pour envoyer votre Complément SharePoint à l'Office Store, suivez la procédure décrite ci-après.




1. Sur la page **Publier votre complément**, sélectionnez **Visiter Mon tableau de bord vendeur**, puis connectez-vous à votre compte Mon tableau de bord vendeur Microsoft.

    Voir  [Soumission des compléments SharePoint et Office, ainsi que des applications web Office 365 dans l'Office Store](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx).


2. Choisissez **Ajouter une nouvelle application**, renseignez les informations requises, puis soumettez le complément à l'Office Store. Pour plus d'informations, voir  [Soumission des compléments SharePoint et Office, ainsi que des applications Office 365 à l'Office Store dans le tableau de bord vendeur](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx).



## Publier une application à l'aide de Visual Studio 2012
<a name="VS2012"> </a>

Lorsque vous êtes prêt à créer un package de votre Complément SharePoint, ouvrez l'Assistant **Publier des compléments Office**, qui prépare les fichiers de votre Complément SharePoint en vue de sa publication.




### Étape 1 : Création du package du Complément SharePoint


1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel du projet de Complément SharePoint, puis sélectionnez **Publier**.

    L'Assistant **Publier des compléments Office** s'ouvre. Le type de Complément SharePoint pour lequel vous créez un package détermine les pages qui s'affichent dans l'Assistant. Si votre complément est hébergé par SharePoint, seule la page **Résumé** s'affiche. Si votre complément est hébergé par un fournisseur, les pages **Profil** et **Hébergement** s'ouvrent également.


2. Si votre Complément SharePoint est hébergé par un fournisseur, indiquez un nom de profil de publication dans la liste **Quel profil souhaitez-vous publier ?**, puis sélectionnez le bouton **Suivant**.

    Le profil de publication enregistre les informations que vous avez entrées sur la page **Hébergement**.


3. Dans la liste **Où est hebergé votre site Web ?**, indiquez l'URL de l'application web qui hébergera votre Complément SharePoint.


4. Dans les zones sous **Quelle est l'identité de votre complément ?**, indiquez l'ID client et la clé secrète client de votre complément, puis sélectionnez le bouton **Suivant**.

    Pour plus d'informations, voir  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md).


5. Pour tous les types de Compléments SharePoint, sélectionnez la case **Ouvrir le dossier de sortie après l'empaquetage** si cela n'est pas déjà fait, puis sélectionnez le bouton **Terminer**.

    Visual Studio génère tous les fichiers dont vous avez besoin pour publier votre Complément SharePoint. Vous pouvez trouver ces fichiers dans le dossier  `app.Publish` du dossier de sortie de votre projet (par exemple, `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`). Ce dossier contient un fichier .app pour votre Complément SharePoint et plusieurs fichiers pour l'application web (si votre Complément SharePoint est basé sur le cloud). Tous les Compléments SharePoint comprennent un fichier .app, qui est le manifeste du complément destiné à publier le Complément SharePoint. Les Compléments SharePoint hébergés par un fournisseur comprennent également des fichiers pour la publication de l'application du site web hôte.



### Étape 2 : Publication de l'application web

Si votre Complément SharePoint est hébergé par un fournisseur, il est généralement associé à une application web hôte que vous devez publier sur un serveur web. Visual Studio génère un package de déploiement et un script pour vous aider à effectuer cette tâche.



Le package de déploiement du projet d'application web se trouve dans un fichier compressé (.zip) dans le dossier  `app.publish`. Outre le fichier .zip, le dossier  `app.publish` contient les fichiers suivants :





|**Fichier**|**Description**|
|:-----|:-----|
| _ProjectName_.deploy.cmd  <br/> |Il s'agit d'un fichier de lignes de commande qui appelle Web Deploy pour vous permettre d'installer le package plus facilement à une invite de commande.  <br/> |
| _ProjectName_.SetParameters.xml  <br/> |Ce fichier contient les paramètres transférés à Web Deploy quand vous utilisez le fichier deploy.cmd afin d'installer le package. Les paramètres du package Visual Studio déterminent la valeur par défaut spécifiée pour chaque paramètre. Vous pouvez modifier ces valeurs si, par exemple, vous souhaitez installer l'application web sur plusieurs serveurs avec des paramètres différents pour chacun d'entre eux.  <br/> |
| _ProjectName_.SourceManifest.xml  <br/> |Ce fichier contient les paramètres que Visual Studio transfère à Web Deploy et que ce dernier utilise pour créer le package Web. Web Deploy n'a besoin de ce fichier que pour créer le package. Ce fichier n'est pas utilisé quand le package est installé.  <br/> |
 
Pour obtenir des instructions étape par étape, voir la page relative à la  [procédure permettant d'installer un package de déploiement à l'aide du fichier deploy.cmd créé par Visual Studio](http://go.microsoft.com/fwlink/?LinkID=254954).




### Étape 3 : Publication de votre Complément SharePoint
<a name="Publish"> </a>

Pour publier votre Complément SharePoint, téléchargez le fichier manifeste (.app) de votre complément sur l'Office Store, le catalogue de compléments Office, SharePoint, un partage de fichiers ou le catalogue Exchange. Le manifeste de votre complément est situé dans le dossier  `app.publish`, par exemple,  `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`. Pour plus d'informations sur la publication de votre Complément SharePoint, voir  [Autorisation et authentification des compléments dans SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md).




## Ressources supplémentaires
<a name="Additional"> </a>


-  [Publier des compléments pour SharePoint](publish-sharepoint-add-ins.md)


-  [Publier votre complément Office](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)



