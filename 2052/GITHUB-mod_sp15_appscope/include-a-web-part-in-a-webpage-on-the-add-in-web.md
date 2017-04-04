---
title: 在外接程序 Web 上的网页中包含 Web 部件
ms.prod: OFFICE365
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# 在外接程序 Web 上的网页中包含 Web 部件
了解如何在 SharePoint 外接程序中包含 Web 部件。
您可以在 SharePoint 外接程序的外接程序网页中包含全新的 Web 部件，但重要的一点是，如果您想更新外接程序，执行此操作不能导致问题。
  
    
    

说明本主题指南的代码示例位于： [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## 先决条件

有关先决条件，请参阅 [开始创建 SharePoint 承载的 SharePoint 外接程序](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)。
  
    
    

## 将 Web 部件添加到页面


  
    
    

1. 在 Visual Studio 中创建 SharePoint 托管的 SharePoint 外接程序项目。有关详细信息，请参阅 [开始创建 SharePoint 承载的 SharePoint 外接程序](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)。
    
  
2. 打开要向其添加 Web 部件的 aspx 文件。本主题以 Default.aspx 为例。 
    
  
3. 将 **WebPartZone** 添加到 **<asp:Content>** 元素，您希望该元素的 Web 部件具有如下所示的标记。通常，您希望将其添加到 **<asp:Content>**，其中 **ContentPlaceHolderId** 为 `PlaceHolderMain`。下面是一个示例：
    
 ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

 ```


    > **警告**
      > 可以添加 Web 部件元素，如 **<WebPartPages:XsltListViewWebPart>** 作为 **WebPartZone** 的子元素。但是，这在 SharePoint 外接程序中通常不是好的做法。如果外接程序需要更新，在 aspx 文件中插入的 Web 部件元素在某些情况下可能导致更新失败，并显示消息"具有此 ID 的 Web 部件已添加到此页面"。对于本程序后面所述的页面，我们建议您将 Web 部件添加到元素清单。
4. 打开页面的元素清单文件，通常称为 elements.xml，并位于与 aspx 文件相同的项目文件夹中。
    
  
5. 在页面的 **File** 元素中，添加一个子 **AllUsersWebPart** 元素，并将其 **WebPartZoneID** 设置为您在页面上创建的 Web 部件区域的值，如此示例中所示。
    
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

6. 添加 **CDATA** 元素作为 **AllUsersWebPart** 的子元素，然后添加 **webParts** 元素作为 **CDATA** 的子元素，如此示例中所示。
    
 ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
 ```

7. 添加 **webPart** 标记作为 **webParts** 元素的子元素。下面是一个添加 **XsltListViewWebPart** 的示例。该示例假定名为"测试列表"的自定义列表是同一个外接程序项目的一部分。有关如何将自定义类表添加到外接程序 Web 中的信息，请参阅 [创建包括自定义 SharePoint 列表和内容类型的提供商托管的外接程序](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md)。 
    
    > **注释**
      >  请注意，Web 部件没有 ID 属性。最佳做法是仅在确实需要的两种情况下包含 Web 部件的显式 ID：>  Web 部件将添加到 SharePoint Wiki 页面。>  Web 部件是将连接的两个或多个 Web 部件之一。

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

8. 按 F5 调试外接程序。您应该在页面上看到 Web 部件。
    
  

