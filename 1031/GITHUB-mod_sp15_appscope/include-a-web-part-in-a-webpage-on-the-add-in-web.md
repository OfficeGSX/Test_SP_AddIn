---
title: Einschließen eines Webparts auf einer Webseite im Add-In-Web
ms.prod: SHAREPOINT
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# Einschließen eines Webparts auf einer Webseite im Add-In-Web
Erfahren Sie, wie Sie ein Webpart auf einer Seite in ein SharePoint-Add-In einschließen.
Sie können ein Out-of-the-Box-Webpart auf einer Seite im Add-In-Web eines SharePoint-Add-Ins einschließen, aber Sie müssen dies so tun, dass dadurch bei einer späteren Aktualisierung des Add-Ins keine Probleme verursacht werden.
  
    
    

Ein Codebeispiel, das die Anweisungen in diesem Thema veranschaulicht, finden Sie unter:  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## Voraussetzungen

Weitere Informationen zu den Voraussetzungen finden Sie unter  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
  
    
    

## Hinzufügen eines Webparts zu einer Seite


  
    
    

1. Erstellen Sie in Visual Studio ein Projekt für ein in SharePoint gehostetes SharePoint-Add-In. Weitere Informationen finden Sie unter  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
    
  
2. Öffnen Sie die aspx-Datei, der Sie ein Webpart hinzufügen möchten. In diesem Thema wird Default.aspx als Beispiel verwendet. 
    
  
3. Fügen Sie dem **<asp:Content>**-Element, das das Webpart mit Markup wie dem folgenden enthalten soll, **WebPartZone** hinzu. Normalerweise fügen Sie es dem **<asp:Content>**-Element hinzu, dessen **ContentPlaceHolderId** `PlaceHolderMain` ist. Im Folgenden finden Sie ein Beispiel.
    
  ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

  ```


    > **VORSICHT**
      > Sie können ein Webpartelement wie **<WebPartPages:XsltListViewWebPart>** als untergeordnetes Element von **WebPartZone** hinzufügen. Im Allgemeinen ist dies jedoch in einem SharePoint-Add-In keine empfehlenswerte Vorgehensweise. Wenn das Add-In aktualisiert werden muss, verursacht ein in die aspx-Datei eingefügtes Webpartelement in einigen Szenarien den Updatefehler „Ein Webpart mit dieser ID wurde dieser Seite bereits hinzugefügt". Wir empfehlen, die Webparts zum Elementmanifest für die Seite hinzuzufügen, wie im weiteren Verlauf dieses Verfahrens beschrieben.
4. Öffnen Sie die Elementmanifestdatei für die Seite. Sie heißt in der Regel „elements.xml" und befindet sich in demselben Projektordner wie die aspx-Datei.
    
  
5. Fügen Sie im **File**-Element für die Seite ein untergeordnetes **AllUsersWebPart**-Element hinzu, und legen Sie seine **WebPartZoneID** auf den Wert der Webpartzone fest, die Sie auf der Seite erstellt haben, wie im folgenden Beispiel gezeigt.
    
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

6. Fügen Sie ein **CDATA**-Element als untergeordnetes Element von **AllUsersWebPart** hinzu, und fügen Sie dann ein **webParts**-Element als untergeordnetes Element von **CDATA** hinzu, wie im folgenden Beispiel gezeigt.
    
  ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
  ```

7. Fügen Sie **webPart**-Markup als untergeordnetes Element des **webParts**-Elements hinzu. Im folgenden Beispiel wird ein **XsltListViewWebPart** hinzugefügt. Es setzt voraus, dass eine benutzerdefinierte Liste "Test List" Teil desselben Add-In-Projekts ist. Weitere Informationen zum Hinzufügen einer benutzerdefinierten Liste zu einem Add-In-Web finden Sie unter [Gewusst wie: Erstellen eines von einem Anbieter gehosteten Add-Ins, das eine benutzerdefinierte SharePoint-Liste und einen benutzerdefinierten Inhaltstyp enthält](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md). 
    
    > **HINWEIS**
      >  Beachten Sie, dass das Webpart keine ID-Eigenschaft hat. Es ist eine bewährte Methode, eine explizite ID für das Webpart nur in den zwei Fällen hinzufügen, in denen es unbedingt erforderlich ist:>  Das Webpart wird zu einer SharePoint-Wiki-Seite hinzugefügt.>  Das Webpart ist eins von zwei oder mehr Webparts, die verbunden werden.

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

8. Drücken Sie F5, um das Add-In zu debuggen. Das Webpart sollte auf der Seite angezeigt werden.
    
  

