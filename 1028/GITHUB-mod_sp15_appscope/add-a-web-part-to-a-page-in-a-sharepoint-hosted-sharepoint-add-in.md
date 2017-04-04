---
title: 將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 6179527d-8452-4cbc-a560-6104dd09e358
---


# 將網頁組件新增至頁面中的 SharePoint 主控 SharePoint 增益集
了解如何在SharePoint Add-ins] 頁面中包含網頁組件。
這是一系列的開發 SharePoint 主控SharePoint Add-ins基本知識文章中的第五個。您首先應該先熟悉 [SharePoint Add-ins](sharepoint-add-ins.md)與此系列中較早的文章：
  
    
    


-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [部署及安裝 SharePoint 主控 SharePoint 增益集](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [將自訂欄新增至 SharePoint hostedSharePoint 增益集](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [將自訂內容類型新增至 SharePoint hostedSharePoint 增益集](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  

> [!注意事項]
> 如果您有已使用透過這一系列有關 SharePoint 主控增益集，則您需要您可以使用以繼續執行本主題的Visual Studio解決方案。您也可以下載 [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials)在存放庫並開啟 BeforeWebPart.sln 檔案。
  
    
    

本文中您可以將網頁組件新增至員工方向SharePoint Add-in的預設頁面。
## 將網頁組件新增至頁面


  
    
    

1. 在 **方案總管**] 中開啟 Default.aspx 檔案。
    
  
2. 我們將會加入新清單檢視網頁組件] 頁面上西雅圖的新員工清單，以該循因此不再需要清單的清單檢視頁面的連結。從其 **ContentPlaceHolderId**是 `PlaceHolderMain` **<asp:Content>**元素中移除 **<asp:HyperLink>**元素。
    
  
3. 在相同的 **<asp:Content>**項目、 內新增下列 **WebPartZone**。
    
  ```XML
  
<WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly"
      ID="HomePage1" Title="loc:full" />

  ```

4. 儲存後關閉檔案。
    
  
5. 在 **方案總管**] 中開啟 elements.xml 檔案頁面的 [ **頁面**] 節點。
    
  
6. 如果自我結束 **File**元素，"/"字元移除和新增結尾標記 `</File>`。
    
  
7. **File**元素中，將子 **AllUsersWebPart**元素新增並設定其 **WebPartZoneID**頁上建立的網頁組件區域的識別碼。檔案的內容現在看起來應類似如下。此標記會告知 SharePoint **AllUsersWebPart**插入名為"HomePage1"的網頁組件區域。
    
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

8. 新增 **CDATA**元素的子 **AllUsersWebPart**中，然後新增 **webParts**元素的子 **CDATA**中，下列標記所示。
    
  ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
  ```

9. 將下列 **webPart**標記新增為 **webParts**元素的子項。此標記新增 **XsltListViewWebPart**並告訴網頁組件以顯示新員工 Seattle清單。 請注意， **ViewContentTypeId**屬性值只是"0x"，不NewEmployee內容類型的實際識別碼。
    
  ```
  
  <webPart xmlns="http://schemas.microsoft.com/WebPart/v3">
    <metaData>
      <type name="Microsoft.SharePoint.WebPartPages.XsltListViewWebPart, 
                   Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, 
                   PublicKeyToken=71e9bce111e9429c" />
    </metaData>
    <data>
      <properties>
        <property name="ListUrl">Lists/NewEmployeesInSeattle</property>
        <property name="IsIncluded">True</property>
        <property name="NoDefaultStyle">True</property>
        <property name="Title">New Employees in Seattle</property>
        <property name="PageType">PAGE_NORMALVIEW</property>
        <property name="Default">False</property>
        <property name="ViewContentTypeId">0x</property>
      </properties>
    </data>
  </webPart>
  ```


## 執行及測試增益集


  
    
    

1. 使用 F5 鍵以部署及執行您的增益集Visual Studio讓您測試 SharePoint 網站上的增益集的暫存安裝並立即執行增益集。
    
  
2. 增益集的預設頁面開啟時，在其上的清單檢視網頁組件會與清單隨即顯示。
    
   **預設值具有清單檢視網頁組件頁面**

  

     ![網頁組件中顯示 [西雅圖新進員工] 清單之增益集的預設頁面。](images/31e8e4b1-e2e6-416b-b360-9979a1f16fc7.PNG)
  

    
    
  
3. 嘗試新增至清單的新項目和編輯現有的項目。
    
  
4. 若要結束偵錯工作階段，關閉瀏覽器視窗或停止在Visual Studio中偵錯。每次您按 F5、 Visual Studio會撤銷舊版增益集和安裝最新的其中一個。
    
  
5. 您將會使用此增益集及Visual Studio解決方案中其他文章與是很好的作法若要撤銷一個增益集時您已完成的最後一次一段使用它。以滑鼠右鍵按一下 [ **方案總管**中的專案，並選擇 **Retract**。
    
  

## 
<a name="Nextsteps"> </a>

在此系列中下一篇文章 ＜ 您將新增工作流程至SharePoint Add-in:  [將工作流程新增至 SharePoint 主控 SharePoint 增益集](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)。
  
    
    

