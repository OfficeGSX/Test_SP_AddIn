---
title: 在 [增益集在 web 上網頁中包含網頁組件
ms.prod: OFFICE365
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# 在 [增益集在 web 上網頁中包含網頁組件
了解如何在SharePoint Add-in中包含網頁組件頁面上。
您可以包含的方塊 (英文) 網頁組件中的SharePoint Add-in、 增益集 web 頁面中，但是很重要您執行此動作將不會導致問題如果您曾經需要更新增益集的方式。
  
    
    

說明本主題的指導方針的程式碼範例會在:  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## 必要條件

必要條件，請參閱 [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)。
  
    
    

## 將網頁組件新增至頁面


  
    
    

1. 建立 SharePoint 主控SharePoint Add-in專案中Visual Studio。如需詳細資訊，請參閱 [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)。
    
  
2. 開啟您要新增網頁組件的 aspx 檔案。本主題會使用 Default.aspx 做為範例。
    
  
3. 將 **WebPartZone**新增至您想要使用類似下列的標記 [網頁組件的 **<asp:Content>**元素。一般而言，您想要將其新增至其 **ContentPlaceHolderId**是 `PlaceHolderMain` **<asp:Content>** 。以下是範例。
    
  ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

  ```


    > [!注意]
      > 有可能新增網頁組件元素，例如 **<WebPartPages:XsltListViewWebPart>** **WebPartZone**的子。但是這通常是在SharePoint Add-in故障的作法。如果增益集曾經需要更新、 插入 aspx 檔案中的網頁組件元素可能會造成在某些情況下"具有此識別碼的網頁組件已新增到此頁面上。 」 訊息失敗的更新建議您稍後說明此程序的網頁組件新增至頁面元素資訊清單。
4. 開啟 [] 頁面上的元素資訊清單檔案。這通常會呼叫 elements.xml 和位於 aspx 檔案相同的專案資料夾。
    
  
5. **File**元素中頁面上，將子 **AllUsersWebPart**元素新增並設定其 **WebPartZoneID**如本範例會顯示在頁面上建立的網頁組件區域的值。
    
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

6. 新增 **CDATA**元素的子 **AllUsersWebPart**，然後新增 **webParts**元素 **CDATA**、 的子在此範例所示。
    
  ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
  ```

7. 新增 **webPart**標記為 **webParts**元素的子項。下列是新增 **XsltListViewWebPart**的範例。它假設名為"Test List"之自訂清單相同的增益集專案的一部分。如需如何將自訂清單新增至增益集的網站的資訊，請參閱 [建立提供者主控增益集包含的自訂 SharePoint 清單與內容類型](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md)。
    
    > [!注意事項]
      > 請注意 [網頁組件沒有 ID 屬性。 就只能在兩個案例中是真正必要網頁組件包含未明確 ID 的最佳作法：> 網頁組件新增至SharePoint wiki 頁面。> 網頁組件是一種將連線的兩個或多個網頁組件。

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

8. 按 F5 偵錯增益集。您應該會看到 [網頁組件] 頁面上。
    
  

