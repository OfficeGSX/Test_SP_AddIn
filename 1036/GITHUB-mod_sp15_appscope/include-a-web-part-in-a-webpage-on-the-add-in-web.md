---
title: Inclure un composant WebPart dans une page web sur le site web de complément
ms.prod: SHAREPOINT
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# Inclure un composant WebPart dans une page web sur le site web de complément
Découvrez comment inclure un composant WebPart sur une page dans un Complément SharePoint .
Vous pouvez inclure un composant WebPart prêt à l'emploi dans le site web d'un Complément SharePoint, mais vous devez le faire de sorte qu'aucun problème ne survienne si vous devez mettre à jour le complément.
  
    
    

Un exemple de code illustrant les instructions fournies dans cette rubrique est disponible dans :  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## Configuration requise

Pour la configuration requise, reportez-vous à  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
  
    
    

## Ajouter un composant WebPart à une page


  
    
    

1. Créez un projet de Complément SharePoint hébergé sur Sharepoint dans Visual Studio. Pour plus de détails, reportez-vous à  [Commencer à créer des compléments SharePoint hébergés par SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
    
  
2. Ouvrez le fichier aspx auquel vous souhaitez ajouter un composant WebPart. Cette rubrique utilise Default.aspx comme exemple. 
    
  
3. Ajoutez **WebPartZone** à l'élément **<asp:Content>** dans lequel vous voulez insérer le composant WebPart, en utilisant un balisage semblable au suivant. Généralement, vous devez l'ajouter à l'élément **<asp:Content>** dont la valeur **ContentPlaceHolderId** est `PlaceHolderMain`. Voici un exemple.
    
 ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

 ```


    > **ATTENTION**
      > Il est possible d'ajouter un composant WebPart, tel que **<WebPartPages:XsltListViewWebPart>**, en tant qu'enfant de l'élément **WebPartZone**. Toutefois, cette opération est généralement déconseillée dans un Complément SharePoint. Si jamais le complément a besoin d'une mise à jour, l'insertion d'un élément WebPart dans le fichier .aspx peut faire échouer la mise à jour dans certains cas et le message suivant s'affiche : « Un composant WebPart portant cet ID a déjà été ajouté à cette page ». Nous vous recommandons d'ajouter les composants WebPart dans le manifeste des éléments de la page, comme décrit plus loin dans cette procédure. 
4. Ouvrez le fichier manifeste des éléments de la page. Il est généralement appelé elements.xml et situé dans le même dossier de projet que le fichier aspx.
    
  
5. Dans l'élément **File** de la page, ajoutez un élément **AllUsersWebPart** enfant et définissez la propriété **WebPartZoneID** sur la valeur de la zone de composants WebPart que vous avez créée sur la page comme indiqué dans cet exemple.
    
 ```
  
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <Module Name="Pages">
    <File Path="Pages\\Default.aspx" Url="Pages/Default.aspx" ReplaceContent="TRUE" >
      <AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">

      </AllUsersWebPart>
    </File>
  </Module>
</Elements>

 ```

6. Ajoutez un élément **CDATA** en tant qu'enfant de l'élément **AllUsersWebPart**, puis ajoutez un élément **webParts** en tant qu'enfant de l'élément **CDATA** comme indiqué dans cet exemple.
    
 ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
 ```

7. Ajoutez une balise **webPart** en tant qu'enfant de l'élément **webParts**. L'exemple de code suivant permet d'ajouter un élément **XsltListViewWebPart**. Dans cet exemple, nous partons du principe qu'une liste personnalisée appelée « Test List » fait partie du même projet. Pour plus d'informations sur la façon d'ajouter une liste personnalisée à un site web de complément, voir  [Créer un complément hébergé par un fournisseur comportant un type personnalisé de liste et de contenu SharePoint](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md). 
    
    > **REMARQUE**
      >  Notez que le composant WebPart ne possède pas une propriété ID. Il est recommandé d'inclure un ID explicite pour le composant WebPart seulement dans les deux cas où il est vraiment nécessaire :>  Le composant WebPart est ajouté à une page wiki SharePoint.>  Le composant WebPart est l'un des deux composants WebPart minimum qui seront connectés.

 ```
  
<webParts>
  <webPart xmlns="http://schemas.microsoft.com/WebPart/v3">
    <metaData>
      <type name="Microsoft.SharePoint.WebPartPages.XsltListViewWebPart, 
                   Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, 
                   PublicKeyToken=71e9bce111e9429c" />
    </metaData>
    <data>
      <properties>
        <property name="ListUrl">Lists/{TestList}</property>
        <property name="IsIncluded">True</property>
        <property name="NoDefaultStyle">True</property>
        <property name="Title">{Test List}</property>
        <property name="PageType">PAGE_NORMALVIEW</property>
        <property name="Default">False</property>
        <property name="ViewContentTypeId">0x</property>
      </properties>
    </data>
  </webPart>
</webParts>
 ```

8. Appuyez sur F5 pour déboguer le complément. Vous devez voir le composant WebPart sur la page.
    
  

