---
title: Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# Résolution des problèmes relatifs aux compléments SharePoint à haut niveau de fiabilité
Trouvez des solutions aux problèmes rencontrés lors du développement de Compléments SharePoint à haut niveau de fiabilité.
Cet article décrit l'outil Fiddler et fournit également des conseils pour vous aider à résoudre certains problèmes spécifiques.





## Utilisation de l'outil Fiddler

L' [outil Fiddler](http://www.telerik.com/fiddler) gratuit peut être utilisé pour capturer les demandes HTTP envoyées par le composant distant de votre complément à SharePoint. Il existe une [extension gratuite de l'outil ](https://github.com/andrewconnell/SPOAuthFiddlerExt) qui décode automatiquement les jetons d'accès dans les demandes.



Une fois Fiddler installé sur le serveur de l'application web, ajoutez le balisage suivant à votre fichier web.config pour que les demandes effectuées à partir de votre application web distante passent par ce proxy. Ainsi, vous pouvez capturer une trace Fiddler et voir la réponse complète de SharePoint lorsqu'une erreur se produit.




> **REMARQUE**
> Veillez à supprimer ce balisage si vous n'exécutez pas Fiddler. Si vous ne le faites pas, votre complément ne sera pas en mesure d'effectuer des requêtes HTTP. 






```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>
```

Une fois Fiddler installé, vous pouvez également vérifier les en-têtes de réponse de SharePoint qui comprennent un GUID de requête. Ce GUID de requête est un ID de corrélation que vous pouvez rechercher dans les fichiers journaux pour trouver des erreurs associées à cette requête en particulier.




## Erreur 401 : accès non autorisé
<a name="UnauthorizedException"> </a>

Plusieurs éléments peuvent générer une erreur **401 Unauthorized** lorsqu'un complément à haut niveau de fiabilité accède à SharePoint pour la première fois. Si vous utilisez le modèle objet côté client (CSOM), l'erreur ressemble à ce qui suit :



```cs

[WebException: The remote server returned an error: (401) Unauthorized.]
   System.Net.HttpWebRequest.GetResponse() +8515936
   Microsoft.SharePoint.Client.SPWebRequestExecutor.Execute() +178
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQueryToServer(ChunkStringBuilder sb) +1427
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQuery() +270
   Microsoft.SharePoint.Client.ClientRuntimeContext.ExecuteQuery() +146
   Microsoft.SharePoint.Client.ClientContext.ExecuteQuery() +666
   S2STestWeb.Default.Page_Load(Object sender, EventArgs e) in c:\\MyFiles\\HightrustTest\\HightrustTestWeb\\Default.aspx.cs:28
   System.Web.UI.Control.LoadRecursive() +71
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178```

Si vous utilisez le fichier TokenHelper et l'identité Windows, le code qui déclenche l'exception ressemble à ce qui suit :





```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();```

La première chose à faire pour résoudre ce problème est d'utiliser le débogueur Visual Studio pour vérifier que le jeton d'accès et l'objet **ClientContext** sont construits correctement. Si c'est le cas, étudiez les pistes suivantes :



 **Problèmes potentiels et résolution :**




- L'utilisateur qui accède à l'application web distante ne possède pas de profil d'utilisateur. Créez le profil d'utilisateur.


- Votre complément ne dispose pas d'autorisation sur la ressource à laquelle vous essayez d'accéder. Ouvrez l'SharePoint Management Shell et exécutez la cmdlet Windows PowerShell suivante. La variable  `$web` est le site web SharePoint auquel vous essayez d'accéder et `$appPrincipal` est l'ID du complément. Pour en savoir plus, voir [Set-SPAppPrincipalPermission](http://technet.microsoft.com/fr-fr/library/jj219714%28v=office.15%29.aspx).
    ```
Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl```

- Votre application web accepte les demandes anonymes. Cela signifie que le jeton d'accès ne contient pas d'identité utilisateur réelle. Vérifiez que l'accès anonyme est désactivé pour le répertoire racine de votre application web distante dans IIS. Vous pouvez également vérifier ce point en déboguant votre application web distante et en vérifiant la valeur de **Request.LogonUserIdentity** dans le fichier default.aspx.cs (ou .vb) afin de vous assurer qu'il ne s'agit pas d'un utilisateur anonyme.


- Votre certificat numérique n'a pas été ajouté au magasin de certificats approuvé. Assurez-vous que vous avez suivi les procédures indiquées dans  [Empaquetage et publication de compléments SharePoint à haut niveau de fiabilité](package-and-publish-high-trust-sharepoint-add-ins.md).



## Erreurs d'autorisation diverses liées au domaine et à SSL
<a name="DomainRelatedErrors"> </a>

Si les noms de domaine dans les fichiers de configuration et les formulaires d'inscription ne correspondent pas, l'autorisation peut être bloquée. Les quatre valeurs suivantes doivent être identiques :




- Le **domaine de complément** qui est indiqué lors de l'inscription du Complément SharePoint auprès d'AppRegNew.aspx.


- Le domaine dans lequel le certificat de sécurité de l'application web distante est inscrit.


- La partie de domaine de la valeur **StartPage** dans le fichier AppManifest.xml.


- La partie de domaine des URL de tous les récepteurs d'événements spécifiés dans le fichier AppManifest.xml.


Concernant ce point, veillez à prendre en compte les éléments suivants :




- Si le composant distant de votre Complément SharePoint utilise un port autre que le port 443, vous devez inclure explicitement le port en tant que partie du domaine dans les quatre endroits. Par exemple,  `MarketingServer:3333`. (Vous devez utiliser le protocole HTTPS pour lequel le port par défaut est le port 443.)


- Le domaine doit être codé en dur dans la valeur **StartPage** (et toutes les URL de récepteur d'événements) du fichier AppManifest.xml avant que le complément ne soit inclus dans un package. Si vous utilisez l'Assistant **Publier** dans Visual Studio pour inclure le complément dans un package, le domaine vous sera demandé et les Outils de développement Office pour Visual Studio l'inséreront dans la valeur **StartPage** pour vous (à la place du jeton `~remoteWebUrl` qui est utilisé pendant le débogage). Par contre, si vous n'utilisez pas l'Assistant **Publier**, vous devez remplacer manuellement le jeton par le domaine (et le protocole), par exemple  `https://MarketingServer` ou `https://MarketingServer:3333`.



## Erreur d'exécution indiquant qu'il n'existe aucun certificat portant ce numéro de série
<a name="DomainRelatedErrors"> </a>

Si vous êtes sûr que le numéro de série du certificat indiqué dans le fichier web.config est correct et que vous pouvez voir le certificat dans le **magasin de certificats Windows**, il se peut que le numéro de série contenu dans le fichier web.config comporte un caractère supplémentaire masqué. Cela se produit si le numéro de série est copié-collé à partir de **Microsoft Management Console**. Supprimez toute la valeur du numéro de série dans le fichier web.config et saisissez-la à nouveau *manuellement*  .




