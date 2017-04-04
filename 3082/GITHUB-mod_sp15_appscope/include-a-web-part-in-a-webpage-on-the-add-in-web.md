---
title: Incluir un elemento web en una página web de aplicación
ms.prod: OFFICE365
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# Incluir un elemento web en una página web de aplicación
Aprenda a incluir un elemento web en una página en un Complemento de SharePoint.
Puede incluir un elemento web listo para usar en una página en la web de complemento de un Complemento de SharePoint, pero es importante que lo haga de una forma que no origine problemas si alguna vez necesita actualizar el complemento.
  
    
    

Un ejemplo de código que ilustra las instrucciones de este tema se encuentra en:  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## Requisitos previos

Para conocer los requisitos previos, vea  [Empezar a crear complementos hospedados en SharePoint para SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
  
    
    

## Agregar un elemento web a una página


  
    
    

1. Cree un proyecto de un Complemento de SharePoint hospedado en SharePoint en Visual Studio. Para obtener información detallada, vea  [Empezar a crear complementos hospedados en SharePoint para SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
    
  
2. Abra el archivo aspx a la que desea agregar un elemento web. En este tema se usa como ejemplo Default.aspx. 
    
  
3. Agregue **WebPartZone** al elemento **<asp:Content>** donde quiere que esté el elemento web con marcado como el siguiente. Por lo general, se lo agrega al **<asp:Content>** cuyo **ContentPlaceHolderId** es `PlaceHolderMain`. Lo siguiente es un ejemplo.
    
 ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

 ```


    > **PRECAUCIóN**
      > Es posible agregar un elemento web, por ejemplo **<WebPartPages:XsltListViewWebPart>**, como un elemento secundario de la **WebPartZone**. Sin embargo, este no es un procedimiento recomendado en un Complemento de SharePoint. Si alguna vez es necesario actualizar el complemento, un elemento web insertado en el archivo aspx puede generar un error de actualización en algunos escenarios con el mensaje "Ya se ha agregado un elemento web con este Id. a esta página". Se recomienda agregar elementos web al manifiesto de elementos para la página, tal como se describe más adelante en este procedimiento. 
4. Abra el archivo de manifiesto del elemento para la página. Por lo general, se denomina elements.xml y se encuentra en la misma carpeta del proyecto que el archivo aspx.
    
  
5. En el elemento **File** para la página, agregue un elemento **AllUsersWebPart** secundario y establezca su **WebPartZoneID** con el valor de la zona de elementos web que creó en la página, tal como lo muestra este ejemplo:
    
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

6. Agregue un elemento **CDATA** como un elemento secundario del **AllUsersWebPart**, luego agregue un elemento **webParts** como un elemento secundario de **CDATA**, tal como se muestra en este ejemplo. 
    
 ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
 ```

7. Agregue el marcado de **webPart** como un elemento secundario del elemento **webParts**. A continuación, se muestra un ejemplo en el que se agrega un **XsltListViewWebPart**. Se asume que hay una lista personalizada denominada "Lista de prueba" como parte del mismo proyecto de complemento. Para obtener más información sobre cómo agregar una lista personalizada a una web de complemento, vea  [Crear un complemento hospedado por el proveedor que incluya un tipo de contenido y una lista de SharePoint personalizados](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md). 
    
    > **NOTA**
      >  Tenga en cuenta que el elemento web no tiene una propiedad ID. Es un procedimiento recomendado incluir un identificador explícito para el elemento web solo en los dos casos en los que realmente es necesario:>  El elemento web se agrega a una página wiki de SharePoint.>  El elemento web es uno de dos o más elementos web que se conectarán.

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

8. Presione F5 para depurar el complemento. Debería ver el elemento web en la página.
    
  

