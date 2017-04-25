---
title: Mettre à jour des composants de compléments web dans SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 6da6ac25-53b7-4dd2-8637-a86e7ca1f3ff
---


# Mettre à jour des composants de compléments web dans SharePoint 2013
Découvrez comment mettre à jour les pages, les listes, les types de contenu et d'autres composants web de complément dans un Complément SharePoint.
## Conditions requises pour la mise à jour des composants web de complément
<a name="Prerequisites"> </a>

Avoir consulté  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md) et connaître les composants requis et concepts fondamentaux associés.



Cette rubrique suppose que vous avez développé et testé la dernière version du complément comme décrit dans  [Créer et déboguer la nouvelle version comme s'il s'agissait d'un nouveau complément](update-sharepoint-add-ins.md#DebugFirst).




## Mettre à jour les composants SharePoint dans le site web de complément
<a name="UpdatingAppWeb"> </a>

Tous les composants SharePoint déployés sur le site web de complément sont contenus dans des fonctionnalités d'étendue **Web** dans le package de complément. Par conséquent, mettre à jour ces composants revient à mettre à jour une ou plusieurs de ces fonctionnalités. Ce processus n'a pas changé depuis SharePoint 2010 et est documenté dans [Procédure : ajouter des éléments à un Composant fonctionnel existant](http://msdn.microsoft.com/library/b007f419-e0d6-4e3a-a3ae-b8e448656d02%28Office.15%29.aspx) dans Kit de développement logiciel (SDK) SharePoint 2010. Les autres articles du nœud [Mise à niveau des fonctionnalités](http://msdn.microsoft.com/library/e917f709-6491-4d50-adbe-2ab8f35da990%28Office.15%29.aspx) peuvent également être utiles, mais n'oubliez pas que les compléments ne doivent pas inclure de code personnalisé sur le serveur SharePoint ; ainsi, certains aspects de la mise à niveau de fonctionnalité dans SharePoint 2010 ne sont pas pertinents en ce qui concerne la mise à jour de compléments. Par exemple, vous ne pouvez pas utiliser l'élément [CustomUpgradeAction](http://msdn.microsoft.com/library/16a2182e-80aa-4184-8071-8f717ee5c572%28Office.15%29.aspx) lorsque vous mettez à niveau la fonctionnalité d'un Complément SharePoint




### Opérations pouvant et ne pouvant pas être effectuées de façon déclarative

Avec un complément hébergé par SharePoint, vous ne pouvez utiliser le balisage XML que pour mettre à jour un complément, et il existe certaines limites concernant la modification de façon déclarative d'un complément dans une mise à jour. Dans un complément hébergé par un fournisseur, vous pouvez implémenter un  [gestionnaire UpdatedEventEndpoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md) pour effectuer des opérations qui ne peuvent pas être effectuées de façon déclarative.



Il est facile d'ajouter des composants à un complément. Tous les composants qui sont susceptibles d'être inclus dans un complément peuvent également être ajoutés dans une mise à jour. (Pour plus d'informations sur les composants qui peuvent se trouver dans un complément, voir  [Types de composants SharePoint pouvant se trouver dans un complément SharePoint](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).) Toutefois, lorsque vous souhaitez modifier un composant existant de façon déclarative, tenez compte des points suivants. 




- Nous n'autorisons en aucun cas la modification du type de données d'un champ de type liste ou contenu (colonne) après son déploiement initial. Plus particulièrement, ne modifiez pas le type de données d'un champ dans le cadre d'une mise à jour de complément ( *pas même par programmation*  ). La solution alternative consiste à ajouter un nouveau champ. Si le complément comprend des formulaires de création, de modification ou d'affichage d'éléments personnalisés, veillez à apporter les modifications correspondantes dans ces formulaires. Par exemple, ajoutez l'interface utilisateur du nouveau champ et supprimez l'ancienne. (Dans un complément hébergé par un fournisseur, vous pouvez déplacer des données par programmation de l'ancien champ vers le nouveau, puis supprimer l'ancien.)


- Nous n'autorisons pas la suppression de listes, d'instances de liste, de types de contenu ou de champs dans le balisage de mise à jour.


- Nous n'autorisons pas la suppression de fichiers du site web de complément dans le balisage de mise à jour. En revanche, vous pouvez modifier le contenu de n'importe quel fichier.


- Les éléments **CustomUpgradeAction** et **MapFile** ne peuvent pas être utilisés lors de la mise à jour d'un Complément SharePoint, même s'ils peuvent sembler disponibles dans Visual Studio IntelliSense.



### Mettre à jour le site web de complément pour la première fois

Les procédures décrites dans cette section expliquent comment ajouter ou mettre à jour les types de contenu, listes, fichiers et autres composants SharePoint dans le site web de complément. Par souci de simplicité, nous supposons que tous les composants font partie d'une fonctionnalité unique dans le site web complément, mais les sites web de complément peuvent avoir plusieurs fonctionnalités et vous pouvez en mettre à jour plusieurs dans la même mise à jour.



Les Outils de développement Microsoft Office pour Visual Studio sont orientés vers la création de compléments et le comportement par défaut des outils n'est parfois pas optimal lorsque vous mettez à jour un complément. Pour avoir davantage de contrôle sur le processus, vous devez commencer par désactiver le Concepteur de fonctionnalités à l'aide de la procédure suivante, afin de pouvoir modifier directement le code XML de fonctionnalité brut. 




### Pour modifier le code XML de fonctionnalité


1. Dans l' **Explorateur de solutions**, ouvrez le fichier  _{FeatureName}_.features. Il s'ouvre dans le Concepteur de fonctionnalités.


2. Ouvrez l'onglet **Manifeste** et développez **Options de modification**.


3. Sélectionnez **Remplacez le code XML généré et modifiez le manifeste dans l'éditeur XML**.


4. Sélectionnez **Oui** à l'invite pour désactiver le concepteur.


5. Dans la vue qui s'ouvre, sélectionnez **Modifier le manifeste dans l'éditeur XML**. Le fichier  _{FeatureName}_.Template.xml s'ouvre. 

   **Ouverture de l'éditeur XML de fonctionnalité**



!\[Étapes à suivre pour ouvrir l'éditeur de fonctionnalités XML](images/UpdateAppOpenFeatureXML.png)






> **ATTENTION**
> N'ajoutez pas de commentaires « <!-- --> » au fichier  _{FeatureName}_.features. Les commentaires ne sont pas pris en charge par l'infrastructure de mise à niveau et la mise à niveau échouera si des commentaires sont présents dans le fichier. Ceux-ci sont utilisés dans les exemples de balisage de cet article uniquement pour vous indiquer où le balisage sera placé. 




Procédez comme suit pour mettre à jour la fonctionnalité web de complément.




### Pour mettre à jour la fonctionnalité web de complément pour la première fois


1. Incrémentez l'attribut **Version** de l'élément [Feature](http://msdn.microsoft.com/library/265cd648-1a7e-410f-a1d7-0da8c64b4006%28Office.15%29.aspx), si Outils de développement Office pour Visual Studio ne l'a pas déjà fait lorsque vous avez incrémenté le numéro de version dans le manifeste du complément (les outils ne le font pas dans chaque scénario, donc vous devez vérifier). Vous devrez utiliser le même numéro de version que pour le complément. Nous vous recommandons d'envisager d'augmenter la version de la fonctionnalité lors de la mise à jour d'autres composants du complément, mais pas la fonctionnalité de site web de complément elle-même. La logique de l'élément  [VersionRange](http://msdn.microsoft.com/library/cd715e38-6ec3-43b2-8007-6d0ed8865d91%28Office.15%29.aspx) (décrite dans la section [Mises à jour suivantes du site web de complément](#SubsequentUpgrades)) est plus facile à gérer lorsque la version du complément et la version de la fonctionnalité sont toujours identiques. 


2. Ne supprimez aucun élément de la section  [ElementManifests](http://msdn.microsoft.com/library/d8d4db7e-2bc2-40c6-958b-d5683bdee87a%28Office.15%29.aspx) du fichier. Rien n'est jamais supprimé de cette section.


3. S'ils ne sont pas déjà présents, ajoutez les éléments suivants au fichier : 

  - Un élément enfant  [UpgradeActions](http://msdn.microsoft.com/library/5af24ac1-a290-454d-b32b-bc7f7a4634f0%28Office.15%29.aspx) dans l'élément **Feature**. N'ajoutez  *pas*  l'attribut **ReceiverAssembly** ou **ReceiverClass** à l'élément. Ces attributs ne sont pas utiles lorsque vous mettez à jour un Complément SharePoint. (Ces attributs font référence à un assembly personnalisé, ce qui n'est pas pris en charge dans les Compléments SharePoint. Si vous incluez un assembly personnalisé dans un complément, SharePoint n'installera pas le complément.)


  - Un élément enfant **VersionRange** dans l'élément **UpgradedActions**. N'ajoutez pas l'attribut **BeginVersion** ou **EndVersion** à l'élément. Ces attributs ne sont pas utiles lors de la première mise à jour d'un complément. Leur utilisation est abordée dans la section [Mises à jour suivantes du site web de complément](#SubsequentUpgrades).


  - Un élément enfant  [ApplyElementManifests](http://msdn.microsoft.com/library/c087a0c3-1e27-4034-b4da-e025991454d6%28Office.15%29.aspx) dans l'élément **VersionRange**. 



    À ce stade, le fichier devrait ressembler à l'exemple suivant.

    > **IMPORTANTE**
      > Il est possible que Outils de développement Office pour Visual Studio ait déjà ajouté le balisage ci-dessus et copié certains éléments de la section **ElementManifests** dans la section **ApplyElementManifests** en guise d'illustration. *Supprimez-les.*  Bien que vous puissiez finir par en replacer certains ultérieurement, il est plus facile et plus sûr de commencer avec une section **ApplyElementManifests** vide. Les entrées redondantes pour les composants qui n'ont pas changé peuvent avoir des conséquences néfastes, y compris causer le rallongement éventuel du processus de mise à jour, qu'elles interrompent et font échouer.



 ```XML

<Feature <!-- Some attributes omitted -->
               Version="2.0.0.0">
  <ElementManifests>
    <!-- ElementManifest elements omitted -->
  </ElementManifests>
  <UpgradeActions>
   <VersionRange>
     <ApplyElementManifests>
   
     </ApplyElementManifests>
   </VersionRange>
  </UpgradeActions>
</Feature>
 ```


### Pour ajouter des composants au complément


1. Ajoutez de nouveaux composants à la fonctionnalité, exactement comme vous le feriez si vous créiez un projet de Complément SharePoint.


2. Lorsque vous ajoutez un composant d'un type qui ne figurait pas dans la version antérieure du complément, par exemple lorsque vous ajoutez une liste à un complément qui ne comprenait pas de liste auparavant, Outils de développement Office pour Visual Studio ajoute un fichier elements.xml au projet. Il s'agit du manifeste des éléments du composant. Nous vous recommandons d'ajouter le nouveau numéro de version du complément à ce fichier, par exemple, elements.2.0.0.0.xml. Cela peut être utile en cas de dépannage. Veillez à effectuer la modification dans l' **Explorateur de solutions** pour garantir que les références au fichier, comme dans le fichier csproj et le code XML de fonctionnalité, sont modifiées en conséquence.


3. Pour chaque nouveau manifeste d'élément, ajoutez un élément  [ElementManifest](http://msdn.microsoft.com/library/5a6a2865-5d31-45a2-a402-6da6e0f5567a%28Office.15%29.aspx) comme enfant aux éléments **ElementManifests** et **ApplyElementManifests** du code XML de fonctionnalité (exactement le même élément **ElementManifest** dans les deux emplacements). L'attribut **Location** de l'élément doit pointer vers le chemin d'accès relatif du fichier elements.2.0.0.0.xml. Par exemple, si vous avez ajouté une liste nommée MyCustomList, l'élément **ElementManifest** se présente comme suit.

 ```XML

<ElementManifest Location="MyCustomList\\elements.2.0.0.0.xml" />
 ```

4. Certains types de composants ajoutent des fichiers au projet. Par exemple, un fichier schema.xml est créé lorsque vous ajoutez une liste ; et lorsque vous ajoutez une page, un fichier de page est créé. Pour chacun de ces fichiers, ajoutez un élément  [ElementFile](http://msdn.microsoft.com/library/bd43638e-8f18-4a0d-b122-1c055f97aa71%28Office.15%29.aspx) comme enfant à l'élément **ElementManifests** (ne l'ajoutez pas à l'élément **ApplyElementManifests**). L'attribut **Location** doit pointer vers le chemin d'accès relatif du fichier. Par exemple, si vous avez ajouté une liste, l'élément **ElementFile** pour le fichier schema.xml se présente comme suit.

 ```XML
  <ElementFile Location="MyCustomList\\Schema.xml" />
 ```

5. Lorsque vous ajoutez un autre élément d'un type qui figurait déjà dans la version antérieure du complément, Outils de développement Office pour Visual Studio peut ajouter une référence au nouvel élément à un manifeste d'éléments existant au lieu d'en créer un. Par exemple, la méthode standard d'ajout d'une page à un site web de complément consiste à cliquer avec le bouton droit sur le nœud **Pages** dans l' **Explorateur de solutions**, puis à parcourir **Ajouter | Nouvel élément | Page | Ajouter**. Outils de développement Office pour Visual Studio ajoutera un nouvel élément **File** au module **Pages** dans le fichier manifeste d'éléments existant (généralement appelé elements.xml) plutôt que de créer un manifeste d'élément.

    Ceci n'est pas souhaitable. La meilleure pratique consiste à éviter, autant que possible, de modifier des fichiers manifeste d'élément existants lors de la mise à jour d'un complément (autrement dit, tout manifeste d'élément des versions antérieures du complément). En général, les nouveaux éléments doivent se trouver dans de nouveaux fichiers manifeste d'élément (qui sont eux-mêmes référencés dans l'élément **ApplyElementManifests** du code XML de fonctionnalité). (Quelques exceptions à cette pratique sont décrites plus loin.) Par exemple, pour ajouter une nouvelle page, procédez comme suit :

1. Créez un module nommé Pages.2.0.0.0.


2. Supprimez de celui-ci le fichier sample.txt, qui est automatiquement ajouté par Outils de développement Office pour Visual Studio.


3. Renommez le manifeste des éléments dans le nouveau module en elements.2.0.0.0.xml.


4. Cliquez avec le bouton droit sur le module **Pages.2.0.0.0**, puis parcourez **Ajouter | Nouvel élément | Page | Ajouter**. La nouvelle page est créée et est référencée dans le manifeste des éléments pour **Pages.2.0.0.0** au lieu de **Pages**.


5. Assurez-vous qu'il existe un élément **ElementsFile** pour la nouvelle page dans l'élément **ElementManifests** du code XML de fonctionnalité, et vérifiez qu'il existe un élément **ElementManifest** pour le fichier elements.2.0.0.0.xml dans les sections **ElementManifests** et **ApplyElementManifests**.



    Une autre option, si Outils de développement Office pour Visual Studio a modifié un manifeste des éléments existant, consiste à créer manuellement un fichier elements.2.0.0.0.xml et à déplacer le balisage qui a été ajouté à l'ancien manifeste dans le nouveau. (Vous pouvez placer le nouveau dans le même nœud **Explorateur de solutions** que l'ancien si vous le souhaitez.)


6. Si vous ajoutez un champ à un type de contenu dans la fonctionnalité, ajoutez un élément  [AddContentTypeField](http://msdn.microsoft.com/library/cb04a3ac-f41a-4ffe-aaa1-d4bf3fb6347d%28Office.15%29.aspx) à la section **VersionRange**. Veillez à affecter les valeurs correctes aux attributs **ContentTypeId** et **FieldId**. Vous pouvez également utiliser l'attribut **PushDown** pour indiquer si le nouveau champ doit être ajouté aux types de contenu dérivés. Voici un exemple.

 ```XML
  <VersionRange>
  <AddContentTypeField 
    ContentTypeId="0x0101000728167cd9c94899925ba69c4af6743e"
    FieldId="{CCDD361F-A3FB-40D8-A272-3A3C858F4116}"
    PushDown="TRUE" />
  <!-- Other child elements of VersionRange -->
</VersionRange>
 ```


### Pour modifier les composants existants du composant


1. Si vous avez modifié un fichier qui est référencé dans un fichier manifeste des éléments, comme un fichier Default.aspx, vous n'avez pas du tout besoin de modifier l'élément **ElementFile** pour le fichier. Toutefois, vous devez indiquer à l'infrastructure de mise à jour de remplacer l'ancienne version du fichier par la nouvelle. Pour ce faire, ajoutez un élément **ElementManifest** pour le module à la section **ApplyElementManifests**. Comme un élément de ce type figure déjà dans la section **ElementManifests**, il est parfois possible de simplement le copier (pas de le déplacer) dans **ApplyElementManifests**, mais ceci n'est conseillé que si chaque fichier qui est référencé dans le manifeste a été modifié. En règle générale, vous ne devriez pas remplacer un fichier inchangé par une copie de lui-même. Dans certains cas, cela peut avoir des effets néfastes. Par exemple, si la page a été configurée pour permettre aux utilisateurs de la personnaliser, son remplacement peut causer la suppression des personnalisations. (Si vous avez changé la page, vous devrez en accepter les conséquences, mais vous ne voudrez peut-être pas imposer cet inconvénient à vos clients inutilement.)

    Pour vous assurer que seuls les fichiers modifiés du module sont remplacés, créez un second manifeste d'élément pour le module qui ne référence que les fichiers modifiés et appliquez le second manifeste dans **ApplyElementManifests** en suivant les étapes ci-après.

1. Cliquez avec le bouton droit sur le nœud du module dans l' **Explorateur de solutions** et ajoutez un fichier XML (et non pas une page) nommé elements.2.0.0.0.xml.


2.  Sélectionnez le nouveau fichier dans l' **Explorateur de solutions** pour rendre son volet de propriétés visible et définissez la propriété **Deployment Type** sur **ElementManifest**. Ceci est important pour s'assurer que Outils de développement Office pour Visual Studio traite le fichier correctement.


3. Copiez le contenu du manifeste d'origine vers le nouveau, puis supprimez du nouveau manifeste tous les éléments  [File](http://msdn.microsoft.com/library/c270e4ce-8110-4da7-b0e7-c223604bfce7%28Office.15%29.aspx) correspondant aux fichiers qui n'ont **pas** été modifiés.


4. Ajoutez un élément **ElementManifest** à la section **ApplyElementManifests** qui référence le nouveau fichier manifeste comme dans cet exemple.

 ```XML

<ElementManifest Location="Pages\\elements.2.0.0.0.xml" />
 ```


    > **REMARQUE**
      >  Ne supprimez pas le manifeste d'origine. Le code XML de fonctionnalité utilise à la fois l'ancien et le nouveau.>  Ne copiez aucun élément **ElementFile** de la section **ElementManifests** vers la section **ApplyElementManifests**, même si le fichier référencé dans **ElementFile** a été modifié.
2. Ouvrez chaque fichier manifeste d'élément référencé dans la section **ApplyElementManifests** et assurez-vous que tous les éléments [File](http://msdn.microsoft.com/library/c270e4ce-8110-4da7-b0e7-c223604bfce7%28Office.15%29.aspx) ont un attribut **ReplaceContents** et qu'il est défini sur **TRUE**. Voici un exemple. Outils de développement Office pour Visual Studio l'a peut-être déjà fait, mais vous devez vérifier. Faites-le même pour les manifestes d'élément des versions antérieures du complément. Il s'agit de l'une des rares méthodes conseillées pour modifier un fichier manifeste d'élément existant.

 ```XML
  <Module Name="Pages">
  <File Path="Pages\\Default.aspx" Url="Pages/Default.aspx" ReplaceContent="TRUE" />
</Module>
 ```

3. Des composants WebPart peuvent être incorporés dans les pages comme expliqué dans  [Inclure un composant WebPart dans une page web sur le site web de complément](include-a-web-part-in-a-webpage-on-the-add-in-web.md). Si vous modifiez une page qui a un composant WebPart (ou modifiez les propriétés du composant WebPart), il y a une étape supplémentaire : vous devez ajouter le balisage suivant à la page afin d'empêcher SharePoint d'ajouter une seconde copie du composant WebPart sur la page. Le balisage doit être ajouté à l'élément **asp:Content** avec l'ID `PlaceHolderAdditionalPageHead` (Outils de développement Office pour Visual Studio l'a peut-être déjà ajouté lorsque la page a été créée pour la première fois, mais vous devez vérifier sa présence).

 ```XML

<meta name="WebPartPageExpansion" content="full" />
 ```


    > **REMARQUE**
      >  Si la page a été configurée pour permettre aux utilisateurs de la personnaliser, ce balisage a pour effet secondaire de supprimer ces personnalisations. Les utilisateurs devront les répéter.>  Si le composant WebPart a été ajouté en suivant les instructions indiquées dans [Inclure un composant WebPart dans une page web sur le site web de complément](include-a-web-part-in-a-webpage-on-the-add-in-web.md), le balisage du composant WebPart est dans le manifeste des éléments ; par conséquent, la modification des propriétés du composant WebPart est une exception à la règle générale selon laquelle vous ne devez pas modifier un fichier manifeste d'élément dans le cadre d'une mise à jour de complément. 
4. Comme alternative à la modification d'une page, vous avez également la possibilité d'utiliser la redirection vers une nouvelle page en procédant comme suit. 

1. Créez la page et configurez son balisage de mise à jour comme décrit dans la procédure **Pour ajouter des composants au complément** ci-dessus.


2. Ouvrez l'ancienne page et supprimez l'ensemble du balisage de l'élément **asp:Content** avec l'ID `PlaceHolderAdditionalPageHead`. 


3. Ajoutez le balisage suivant à l'élément **asp:Content**, puis remplacez  _{RelativePathToNewPageFile}_ par les nouveaux chemin d'accès et nom de fichier. Ce script redirigera le navigateur vers la nouvelle page et inclura les paramètres de requête. Il supprimera également l'ancienne page de l'historique du navigateur.

 ```
  <script type="text/javascript">
        var queryString = window.location.search.substring(1);
        window.location.replace("{RelativePathToNewPageFile}" + "?" + queryString);
</script>
 ```

4. Supprimez tout autre élément **asp:Content** sur la page.


5. Si la page que vous remplacez est la page de démarrage du complément, modifiez l'élément **StartPage** dans le manifeste de complément afin qu'il pointe vers la nouvelle page.


5. Si le site web du complément contient un composant **CustomAction** ou **ClientWebPart**, et que vous le modifiez dans le cadre d'une mise à jour, vous devez modifier le manifeste d'élément puisqu'il s'agit de l'emplacement de définition de ces composants. (Ceci constitue une exception à la pratique générale selon laquelle vous ne devez pas modifier un manifeste d'élément à partir d'une version antérieure du complément lors de la mise à jour du complément). Vous devez également copier (et non déplacer) l'élément **ElementManifest** de la section **ElementManifests** vers la section **ApplyElementManifests**.



#### Exemple de code XML de fonctionnalité pour la mise à niveau d'un complément pour la première fois

Voici un exemple de fichier  _{FeatureName}_.Template.xml complet pour la mise à jour d'un complément pour la première fois. Le complément mis à jour dans cet exemple inclut un fichier Default.aspx modifié, référencé dans le fichier  `Pages\\Elements.xml`, et déploie trois nouveaux fichiers jQuery, chacun étant référencé dans le fichier  `Scripts\\Elements.xml`. Notez que tous les éléments **ElementFile** vont dans la section **ElementManifests** et que `<ElementManifest Location="Pages\\Elements.xml" />` a été copié (et non déplacé) de la section **ElementManifests** vers la section **ApplyElementManifests**.



```XML

<Feature xmlns="http://schemas.microsoft.com/sharepoint/" Title="MyApp Feature1"
      Description="SharePoint Add-in Feature" Id="85d309a8-107e-4a7d-b3a2-51341d3b11ff" 
      Scope="Web" Version="2.0.0.0">
  <ElementManifests>
    <ElementFile Location="Pages\\Default.aspx" />
    <ElementManifest Location="Pages\\Elements.xml" />
    <ElementFile Location="Content\\App.css" />
    <ElementManifest Location="Content\\Elements.xml" />
    <ElementFile Location="Images\\AppIcon.png" />
    <ElementManifest Location="Images\\Elements.xml" />
    <ElementFile Location="Scripts\\jquery-3.0.0.intellisense.js" />
    <ElementFile Location="Scripts\\jquery-3.0.0.js" />
    <ElementFile Location="Scripts\\jquery-3.0.0.min.js" />
  </ElementManifests> 
  <UpgradeActions>
      <VersionRange>  
        <ApplyElementManifests>
          <ElementManifest Location="Pages\\Elements.xml" />
          <ElementManifest Location="Scripts\\elements.2.0.0.0.xml" />
        </ApplyElementManifests>
      </VersionRange>
  </UpgradeActions>
</Feature>
```


### Mises à jour suivantes du site web de complément
<a name="SubsequentUpgrades"> </a>

Lorsque vous mettez à jour un Complément SharePoint pour la deuxième fois (ou la troisième, etc.), vous devez tenir compte du fait que certains de vos clients n'ont peut-être pas effectué les mises à jour précédentes. Par conséquent, si un utilisateur répond à l'invite « une mise à jour est disponible » après le déploiement de votre dernière mise à jour sur le catalogue de compléments de l'organisation ou sur l'Office Store, son instance du complément peut être mise à jour via plusieurs versions dans un seul processus de mise à jour. En général, voici exactement ce qui doit se produire : vous voulez que chaque version antérieure du complément soit mise à jour vers la dernière version, mais vous ne voulez pas toujours que chaque action de mise à jour de la fonctionnalité de site web de complément se reproduise pour chaque instance du complément. Certaines actions de mise à jour ne doivent pas se produire plusieurs fois sur une instance de complément donnée. Par exemple, si vous ajoutez un champ à un type de contenu dans une mise à jour, vous ne voulez pas que ce champ soit à nouveau ajouté lors de la prochaine mise à jour. La procédure suivante vous indique comment utiliser l'élément **VersionRange** pour contrôler les actions de mise à jour qui se produisent en fonction de la version de la fonctionnalité mise à jour.




### Pour modifier la fonctionnalité du site web de complément lors des mises à jour ultérieures


1. Ouvrez le fichier  _FeatureName_.Template.xml pour modification comme indiqué dans la procédure **Pour modifier le code XML de fonctionnalité** plus haut dans cet article et incrémentez l'attribut **Version** de l'élément [Feature](http://msdn.microsoft.com/library/265cd648-1a7e-410f-a1d7-0da8c64b4006%28Office.15%29.aspx). Vous devrez utiliser le même numéro de version pour la fonctionnalité que celui que vous avez utilisé pour le complément.

    Pour poursuivre notre exemple, supposons que vous avez précédemment mis à jour le complément de la version 1.0.0.0 vers la version 2.0.0.0 et que vous souhaitez maintenant le mettre à jour vers la version 3.0.0.0. Définissez l'attribut **Version** sur 3.0.0.0.


2. Ajoutez un nouvel élément **VersionRange** sous tous les éléments **VersionRange** existants. N'ajoutez *aucun*  attribut **BeginVersion** ou **EndVersion** à cet élément.


3. Complétez l'élément **VersionRange** comme indiqué dans la procédure **Pour mettre à jour la fonctionnalité web de complément pour la première fois** plus haut dans cet article afin de justifier les modifications que vous avez effectuées dans cette version mise à jour de la fonctionnalité. Chaque fois que cette procédure fait référence à la section **ApplyElementManifests**, faites comme si elle faisait référence à l'élément **ApplyElementManifests** qui est un enfant de l'élément **VersionRange** que vous venez d'ajouter ; autrement dit, le *plus bas*  dans le fichier de code XML de fonctionnalité.


4. Accédez à l'élément **VersionRange** précédent, que vous avez ajouté la dernière fois que vous avez mis à jour le complément (de la version 1.0.0.0 vers 2.0.0.0 dans l'exemple), puis ajoutez un attribut **EndVersion** à cet élément. Vous voulez que les actions de mise à niveau de cet élément **VersionRange** soient appliquées à toutes les versions du complément auxquelles elles n'ont pas encore été appliquées (version 1.0.0.0), mais vous ne voulez pas qu'elles soient appliquées à nouveau aux versions auxquelles elles ont déjà été appliquées (version 2.0.0.0). La valeur **EndVersion** est *exclusive*  ; par conséquent, définissez-la sur la version la plus faible à laquelle vous ne voulez *pas*  que les actions de mise à niveau soient appliquées. Dans l'exemple, vous définissez cette valeur sur 2.0.0.0. Votre fichier doit maintenant ressembler à ce qui suit.

 ```XML

<Feature <!-- Some attributes omitted -->
               Version="3.0.0.0">
  <ElementManifests>
    <!-- ElementManifest elements omitted -->
  </ElementManifests>
  <UpgradeActions>
    <VersionRange EndVersion="2.0.0.0">
      <!--  Child elements for upgrade from 1.0.0.0 to 2.0.0.0 go here. -->
    </VersionRange>
   <VersionRange>
      <!--  Child elements for upgrade from 2.0.0.0 to 3.0.0.0 go here. -->
   </VersionRange>
  </UpgradeActions>
</Feature>
 ```


    Suivez le même modèle à chaque fois que vous mettez à niveau la fonctionnalité. Ajoutez un nouvel élément **VersionRange** pour les dernières actions de mise à jour. Ajoutez ensuite un élément **EndVersion** à l'élément **VersionRange** précédent (nous insistons sur le fait qu'il s'agit du *précédent*  !) et définissez-le sur le numéro de version précédent. Dans l'exemple, pour la mise à jour de la version 3.0.0.0 vers 4.0.0.0, le fichier ressemblerait à ce qui suit.



 ```XML

<Feature <!-- Some attributes omitted -->
               Version="4.0.0.0">
  <ElementManifests>
    <!-- Child elements omitted -->
  </ElementManifests>
  <UpgradeActions>
    <VersionRange EndVersion="2.0.0.0">
       <!-- Child elements for upgrade from 1.0.0.0 to 2.0.0.0 go here. -->
    </VersionRange>
    <VersionRange EndVersion="3.0.0.0">
       <!-- Child elements for upgrade from 2.0.0.0 to 3.0.0.0 go here. -->
    </VersionRange>
    <VersionRange>
       <!-- Child elements for upgrade from 3.0.0.0 to 4.0.0.0 go here. -->
    </VersionRange>
  </UpgradeActions>
</Feature>
 ```


    Notez que l'élément **VersionRange** le plus récent ne possède aucun attribut **BeginVersion** ou **EndVersion**. Cela permet de garantir que les actions de mise à niveau qui accèdent à cet élément **VersionRange** sont appliquées à toutes les versions précédentes de la fonctionnalité, ce qui correspond à votre volonté car toutes les dernières modifications sont référencées dans cet élément **VersionRange**, et aucune d'entre elles ne s'est encore produite pour les instances de la fonctionnalité.

    Notez également que l'attribut **BeginVersion** n'est utilisé dans aucun des attributs **VersionRange**. Ceci est dû au fait que la valeur par défaut de l'attribut **BeginVersion** est 0.0.0.0. Il s'agit de la valeur souhaitée car vous voulez que toutes les actions de mise à niveau soient appliquées à chaque instance du complément antérieure à la version spécifiée dans l'attribut **EndVersion**. 

    > **IMPORTANTE**
      >  L'élément **VersionRange** détermine uniquement les versions de la fonctionnalité auxquelles les mises à niveau sont appliquées. Il ne permet pas de déterminer les versions du complément qui obtiennent une notification indiquant qu'une mise à jour est disponible ; la notification est déclenchée uniquement par le numéro de version du complément. Dans les 24 heures suivant la mise à disposition d'une nouvelle version du complément dans le catalogue de compléments de l'organisation ou dans l'Office Store, chaque instance installée du complément, quelle que soit la version, bénéficie d'une notification indiquant qu'une mise à jour est disponible sur sa vignette sur la page **Contenu du site**. >  L'élément **VersionRange** n'a pas d'incidence sur le nouveau numéro de version de la fonctionnalité récemment mise à niveau ou du complément récemment mis à jour. Ces deux numéros sont toujours remplacés par le dernier numéro de version, quelle que soit la plage de versions de la fonctionnalité avant la mise à niveau. Voilà une autre bonne raison d'éviter d'utiliser un attribut **BeginVersion**. L'attribut **BeginVersion** peut être utilisé pour bloquer l'exécution de certaines actions de mise à niveau sur certaines instances de complément. Cependant, il ne peut pas bloquer le passage à la dernière version d'un complément ou d'une fonctionnalité. Par conséquent, l'utilisation d'un attribut **BeginVersion** peut entraîner une situation dans laquelle deux instances de votre complément peuvent avoir le même numéro de version de complément et le même numéro de version de fonctionnalité de site web de complément, tout en ayant des composants différents dans leurs sites web de complément.

## Vérifier le déploiement des composants de site web de complément
<a name="VerifyDeployAppWebComp"> </a>

Procédez comme suit pour vérifier le déploiement de la fonctionnalité web de complément et de ses composants.




### Pour vérifier la mise en service du site web de complément


1. Ouvrez la page **Paramètres du site** du site web hôte. Dans la section **Administration de la collection de sites**, cliquez sur le lien **Hiérarchie de site**.


2. Dans la page **Hiérarchie de site**, le site web de complément est répertorié par son URL. Ne le lancez pas. Copiez plutôt l'URL et utilisez-la dans les étapes restantes.


3. Accédez à  _URL_of_app_web_/_layouts/15/ManageFeatures.aspx, puis, sur la page **Fonctionnalités du site** qui s'ouvre, vérifiez que la fonctionnalité est un membre de la liste alphabétique des fonctionnalités et que son état est **Actif**. 


4. Si votre fonctionnalité de site web de complément inclut des colonnes de site personnalisées, ouvrez  _URL_of_app_web_/_layouts/15/mngfield.aspx, puis, sur la page **Colonnes de site** qui s'ouvre, vérifiez que vos nouvelles colonnes de site personnalisées sont répertoriées.


5. Si votre fonctionnalité de site web de complément inclut des types de contenu personnalisés, ouvrez  _URL_of_app_web_/_layouts/15/mngctype.aspx, puis, sur la page **Types de contenu de site** qui s'ouvre, vérifiez que vos nouveaux types de contenu sont répertoriés.


6. Pour chaque type de contenu personnalisé et chaque type de contenu auquel vous avez ajouté une colonne, cliquez sur le lien vers le type de contenu. Sur la page **Type de contenu de site** qui s'ouvre, vérifiez que le type de contenu comporte bien les colonnes de site attendues.


7. Si votre fonctionnalité de site web de complément inclut des instances de liste, ouvrez  _URL_of_app_web_ /_layouts/15/mcontent.aspx, puis, sur la page **Bibliothèques et listes du site** qui s'ouvre, vérifiez qu'il existe un lien **Personnaliser « nom_de_la_liste »** pour chaque instance de liste personnalisée.


8. Pour chacune de ces instances de liste personnalisées, sélectionnez le lien **Personnaliser « nom_de_la_liste »**, puis vérifiez sur la page des paramètres de liste que la liste comprend les colonnes et types de contenu attendus.

    > **REMARQUE**
      > S'il n'existe aucune section **Types de contenu** dans la page, vous devez activer la gestion des types de contenu. Cliquez sur le lien **Paramètres avancés**, puis, dans la page Paramètres avancés, activez la gestion des types de contenu et cliquez sur **OK**. Vous êtes renvoyé vers la page précédente où vous voyez désormais une liste de la section **Types de contenu**. 
9. L' **adresse web** de la liste est située en haut de la page. Si vous avez inclus des exemples d'éléments dans la définition de votre instance de liste, copiez l'adresse et collez-la dans la barre d'adresses de votre navigateur, puis accédez à la liste. Vérifiez qu'elle contient les exemples d'éléments que vous avez créés.



## Étapes suivantes
<a name="Next"> </a>

Retournez à la section  [Étapes principales de mise à jour d'un complément](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) ou consultez directement l'un des articles suivants pour découvrir comment mettre à jour le prochain composant majeur de votre Complément SharePoint.




-  [Mettre à jour des composants web hôte dans SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)


-  [Créer un gestionnaire pour l'événement de mise à jour dans des compléments pour SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)


-  [Mettre à jour des composants distants dans les compléments pour SharePoint](update-remote-components-in-sharepoint-add-ins.md)



## Ressources supplémentaires
<a name="bk_addresources"> </a>


-  [Mise à jour des compléments pour SharePoint](update-sharepoint-add-ins.md)


-  [Procédure : ajouter des éléments à un Composant fonctionnel existant](http://msdn.microsoft.com/library/b007f419-e0d6-4e3a-a3ae-b8e448656d02%28Office.15%29.aspx) dans Kit de développement logiciel (SDK) Microsoft SharePoint 2010.


-  [Mise à niveau des fonctionnalités](http://msdn.microsoft.com/library/e917f709-6491-4d50-adbe-2ab8f35da990%28Office.15%29.aspx) dans Kit de développement logiciel (SDK) Microsoft SharePoint 2010.



