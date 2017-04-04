---
title: Incluir uma Web Part em uma página da Web na web suplemento
ms.prod: SHAREPOINT
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# Incluir uma Web Part em uma página da Web na web suplemento
Saiba como incluir uma Web Part em uma página em um Suplemento do SharePoint.
Você pode incluir uma Web Part de-de-imediata em uma página da web do add-in de um Suplemento do SharePoint, mas é importante que você faça isso de uma forma que não causar problemas se você precisa atualizar o suplemento.
  
    
    

Um exemplo de código que ilustra as diretrizes deste tópico é em:  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## Pré-requisitos

Para os pré-requisitos, consulte  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
  
    
    

## Adicionar uma Web Part a uma página


  
    
    

1. Crie um projeto de hospedado no SharePoint Suplemento do SharePoint em Visual Studio. Para obter detalhes, consulte  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
    
  
2. Abra o arquivo aspx ao qual você deseja adicionar uma Web Part. Este tópico usa default. aspx como exemplo.
    
  
3. Adicione um **WebPartZone** ao elemento **<asp:Content>** onde você deseja que a Web Part com marcação como a seguir. Normalmente, você deseja adicioná-lo ao **<asp:Content>** cujo **ContentPlaceHolderId** é `PlaceHolderMain`. O exemplo a seguir é um exemplo.
    
  ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

  ```


    > [!CUIDADO]
      > É possível adicionar um elemento de Web Part, como **<WebPartPages:XsltListViewWebPart>** como um filho do **WebPartZone**. Mas isso é geralmente uma prática ruim em um Suplemento do SharePoint. Se o suplemento nunca precisa ser atualizado, um elemento de Web Part inserido no arquivo aspx pode causar a atualização falhe em alguns cenários com a mensagem "uma web part com essa ID já foi adicionada a esta página." Recomendamos que você adicionar web parts no manifesto elementos da página, conforme será descrito ainda deste procedimento.
4. Abra o arquivo de manifesto do elemento da página. Isso geralmente é chamado elements. XML e está localizado na mesma pasta project arquivo aspx.
    
  
5. No elemento **File** para a página, adicione um elemento de **AllUsersWebPart** filho e defina seu **WebPartZoneID** com o valor da zona Web Part que você criou na página, conforme mostrado neste exemplo.
    
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

6. Adicionar um elemento de **CDATA** como um filho do **AllUsersWebPart**, adicione um elemento **webParts** como um filho do **CDATA**, conforme mostrado neste exemplo.
    
  ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
  ```

7. Adicione a marcação de **webPart** como um filho do elemento **webParts**. O exemplo a seguir é um exemplo que adiciona um **XsltListViewWebPart**. Ele pressupõe que uma lista personalizada chamada "Test List" faz parte do mesmo projeto de suplemento. Para obter informações sobre como adicionar uma lista personalizada para um suplemento de web, consulte  [Criar um provedor hospedado suplemento que inclui uma lista personalizada do SharePoint e o tipo de conteúdo](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md).
    
    > [!OBSERVAçãO]
      > Observe que a Web Part não tem uma propriedade de ID. É uma prática recomendada para incluir uma identificação explícita para a Web Part apenas nos dois casos onde ele é realmente necessário:> A Web Part está sendo adicionada a uma página de wiki SharePoint.> A Web Part é uma das duas ou mais Web Parts que se conectarão.

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

8. Pressione F5 para depurar o suplemento. Você deverá ver a Web Part na página.
    
  

