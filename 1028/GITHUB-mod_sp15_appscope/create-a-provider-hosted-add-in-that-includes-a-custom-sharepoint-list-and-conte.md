---
title: 建立提供者主控增益集包含的自訂 SharePoint 清單與內容類型
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# 建立提供者主控增益集包含的自訂 SharePoint 清單與內容類型
建立自訂 SharePoint 裝載清單範本、 清單執行個體，與自訂內容類型結合的雲端裝載 web 應用程式，使用Visual Studio 2012 Office 開發人員工具SharePoint Add-in 。瞭解如何互動SharePoint 2013增益集網站使用其餘/OData web 服務，以及如何實作 OAuth SharePoint Add-in中。
大部分傳統 SharePoint 元件，例如自訂內容類型、 自訂清單定義和工作流程，可以包含在雲端裝載SharePoint Add-in。本文中的簡單範例包含下列內容:
  
    
    


- 與增益集網站
    
  - 某些自訂網站欄
    
  
  - 使用自訂資料行的自訂內容類型
    
  
  - 使用自訂內容類型的自訂清單範本
    
  
  - 依據自訂清單定義清單執行個體
    
  
- 讀取清單執行個體中的資料ASP.NET web 應用程式
    
  

## 此 SharePoint 增益集所建立的先決條件
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30682)或更新版本。
    
  
-  [Office 開發人員工具](https://msdn.microsoft.com/en-us/office/aa905340.aspx)
    
  
- 測試和偵錯SharePoint 2013安裝
    
  - 這可能是在同一部電腦開發電腦，或您可以開發遠端SharePoint 2013安裝。如果您使用遠端安裝時，您必須安裝目標安裝的用戶端物件模型可轉散發套件。使用為在 Microsoft 下載中心上可轉散發套件。搜尋 「 SharePoint Server 2013 用戶端元件 SDK 」 或 「 SharePoint Online 用戶端元件 SDK 」。
    
  
  - 測試 SharePoint 網站，必須先建立從的 **開發人員網站**網站定義 (您可以建立在管理中心)。
    
  
  - 遠端 web 應用程式會使用JavaScript和 [跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)或 [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md)通訊與增益集 web。如果使用 OAuth，因為它是在連續的本文範例中，必須使用 OAuth 設定SharePoint 2013安裝。
    
  

> **注意事項**
> 如需如何設定適合您需求的開發環境的指引，請參閱 [啟動 Office 及 SharePoint 增益集](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx)。
  
    
    


### 瞭解增益集所建立的核心概念

建立第一個增益集之前，您應該瞭解哪些SharePoint Add-ins會和 SharePoint 裝載及雲端裝載SharePoint Add-ins之間的差異。表 1] 中的文章應該讓您的瞭解。
  
    
    

**資料表 1。增益集所建立的核心概念**


|**文件標題**|**說明**|
|:-----|:-----|
| [SharePoint Add-ins](sharepoint-add-ins.md) <br/> |瞭解新增益集的模型中SharePoint 2013可讓您建立增益集，也就是小型、 方便使用的使用者的解決方案。 <br/> |
| [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |深入了解層面的架構SharePoint Add-ins以及SharePoint 增益集相關的模型，包括增益集裝載選項、 使用者介面 (UI) 選項、 部署系統、 安全系統和生命週期。 <br/> |
| [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |深入了解您可以主控SharePoint Add-ins的各種方式。 <br/> |
   

## 開發 SharePoint 增益集
<a name="Develop"> </a>

在這一節的程序，您可以建立SharePoint Add-in ，其中包含增益集在 web 上的 SharePoint 元件和遠端 web 應用程式開發電腦上。
  
    
    

### 若要設定Visual Studio 2012方案，以及其項目


1. 在Visual Studio 2012，請從 **Office SharePoint 中建立的增益集 SharePoint 2013專案 |增益集**( **C#** ] 或 [之下 **Visual Basic**) 的 [ **新增專案**精靈範本樹狀目錄中的節點。選擇 [ **提供者主控**裝載選項]。在本文的連續範例中，C# 用來做為語言，然後LocalTheater為專案名稱。
    
  
2. 在精靈中，選擇 [ **完成**]。
    
  
3. 資訊清單的設計工具] 中開啟 AppManifest.xml 檔案。 **Title**元素的專案名稱做為預設值。以取代更好記的項目，因為這是增益集的使用者，請參閱在 ui 上的名稱。
    
  
4. 指定 **Name**增益集。這是內部名稱，必須只包含 ASCII 字元，而且必須包含不加空格;例如， LocalTheater。
    
  
5. 開啟 Web.config 檔案在 web 應用程式專案，並新增項目 `<customErrors mode="Off"/>` **system.web**項目。
    
  
6. 請檢查下列組件的參照是在 web 應用程式專案。(如果您版本Visual Studio 2012並未自動新增參照，然後將其新增現在。)
    
  - **Microsoft.IdentityModel.dll**Windows Identity Foundation (WIF)全域組件快取到已安裝此組件。因為這是.NET Framework 3.5組件，請在預設會篩選出 **新增參照位址**] 對話方塊的 [ **架構**] 節點。您可以瀏覽至 `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5`目錄開發電腦的直接新增它的參考。
    
  
  - **Microsoft.IdentityModel.Extensions.dll**您可以新增的參考直接瀏覽開發電腦的 `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` ] 資料夾。
    
  
  - **System.IdentityModel.dll**這個組件的一部分.NET Framework 4，而且 **組件上會出現 |架構**節點的 [ **新增參照**] 對話方塊。
    
  
7. 如果遠端 web 應用程式存取主機網頁，以及新增在網頁版中的資訊，您必須新增 **AppPermissionRequests**項目，一或多個子 **AppPermissionRequest**項目時，AppManifest.xml 檔案。(在連續範例這篇文章的 web 應用程式存取只增益集網站。Add-in 主體自動都所有增益集網站，因此在範例 AppManifest.xml 並都沒有 **AppPermissionRequests**項目所需的權限。)如需有關增益集權限要求及如何將其新增的詳細資訊，請參閱 [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)。
    
  

### 若要新增 SharePoint 元件


1. 您可以將它們加入傳統的farm solution一樣，您可以新增到 [增益集的 SharePoint 元件。不過，不是每一種 SharePoint 元件可以包含在SharePoint Add-in。這些元件提供服務的目的被完成SharePoint Add-ins的其他方式。如需哪些類型的 SharePoint 元件可以包含在SharePoint Add-in ，以及如何將其包含在專案中的詳細資訊，請參閱 [類型的 SharePoint 元件可在 SharePoint 增益集](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps)。
    
    為了連續的範例，使用下列程序。這會提供使用Visual Studio 2012將自訂資料行、 內容類型、 清單範本及清單執行個體新增到SharePoint Add-in的範例。
    
### 若要建立的自訂欄類型


1. 在 **方案總管]**中，新增 SharePoint **網站欄**項目至SharePoint Add-in專案名稱動作項目。
    
  
2. 在 elements.xml 檔案中的 [新增網站欄，編輯 **Field**項目，讓它具有屬性和下列範例所示的值，但不應該變更為它產生值Visual Studio 2012從 **ID**屬性 GUID。別忘了框架設定括號 「 {」。
    
  ```
  
<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
  ```

3. 名為CastingStatus專案中新增另一個 **網站欄**。
    
  
4. 在 elements.xml 檔案中的 [新增網站欄，編輯 **Field**項目，讓它具有屬性和下列範例所示的值，但不應該變更為它產生值Visual Studio 2012從 **ID**屬性 GUID。
    
  ```
  
<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
  ```

5. 這是選擇欄位，因為您必須指定可能的選項，他們應該顯示在下拉式清單中選擇及預設選項，使用者會進行時的順序。新增下列子標記 **Field**項目。
    
  ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```


### 若要建立自訂內容類型


1. 在 **方案總管]**中，請在名稱ActingRoleSharePoint Add-in專案中新增的 SharePoint **內容類型**項目。當畫面提示您 「 精靈 」 所選取的基本的內容類型，選擇 [ **項目**，，，然後選擇 [ **完成]**。
    
  
2. 如果內容類型設計工具會自動開啟，請選擇 [在 **方案總管]**中，將其開啟的 [ **ActingRole**內容類型]。
    
  
3. 在設計工具中開啟 [ **內容類型**] 索引標籤，然後填入文字] 方塊，如下所示:
    
  - **內容類型名稱**: ActingRole
    
  
  - **描述**:代表播放或影片文件中的角色。
    
  
  - **群組名稱**:劇場和影片
    
  
4. 請確認該 *無*  的] 索引標籤上的方塊已核取。根據預設，可能會選取 **繼承上層內容類型的資料行**核取方塊。 *請務必清除了。* 
    
  
5. 在設計工具中開啟 [ **欄**] 索引標籤。
    
  
6. 若要將兩個網站欄新增至內容類型使用格線。下拉式清單中顯示的名稱來列出: **動作項目/女演員**和 **CastingStatus**。(如果未列出，您可能無法儲存專案後新增自訂的網站欄。選擇 [ **全部儲存**)。
    
  
7. 將檔案儲存並關閉設計工具]。
    
  
8. 下一步需要您直接在內容類型的原始 XML，因此在 **方案總管]**中，選擇 elements.xml 檔案的子 **ActingRole**內容類型。
    
  
9. 在您新增兩個資料行的檔案中已經有 **FieldRef**項目。新增為兩個已有的對等的兩個內建SharePoint 2013資料行的 **FieldRef**項目。以下是元素的標記。 *您必須使用這些相同的 Guid ID 屬性，因為這些是內建的欄位類型，使用固定的識別碼。*  新增這些 *上述*  兩個 **FieldRef**元素自訂網站欄。
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  ```


    請注意，我們有提供這些欄位自訂顯示名稱: **字元**之播放或影片文件中的一個字元。
    
  

### 若要建立自訂清單範本和清單執行個體


1. 以名稱CharactersInShowSharePoint Add-in專案中新增的 SharePoint **清單**項目。在 **SharePoint 自訂精靈**] 的 [ **選擇 [清單設定**] 頁面上保留預設 **CharactersInShow**清單顯示名稱，選擇 [ **建立自訂的清單，根據**選項] 按鈕，然後在下拉式清單中選擇 **預設值 (空白)** 。請選擇 [ **完成**]。
    
  
2. 當您完成精靈時， **CharactersInShow**清單範本建立名為 **CharactersInShowInstance**子清單執行個體。依預設，可能已經開啟清單設計工具。後續步驟中使用。
    
  
3. 開啟 elements.xml 子系 **CharactersInShow**清單範本 ( **CharactersInShowInstance**的 elements.xml 子系)。
    
  
4. 新增空間至，使其更好記的 **DisplayName**屬性: 「 字元中顯示 」。
    
  
5. 將 **Description**屬性設定為「 播放或影片文件中的字元。 」
    
  
6. 將所有其他屬性保留其預設值、 儲存檔案，並將其關閉。
    
  
7. 如果尚未開啟清單設計工具，請選擇 [ **CharactersInShow**節點 **方案總管**] 中。
    
  
8. 在設計工具中，開啟 [ **欄**] 索引標籤，然後選擇 [ **內容類型**] 按鈕。
    
  
9. 在 [ **內容類型設定**] 對話方塊中，新增 **ActingRole**內容類型。
    
  
10. 選擇 [ **ActingRole**內容類型的 [類型] 清單中，選擇 [ **設成預設值**] 按鈕。
    
    選擇的 **項目**內容類型的小型箭號出現在名稱左邊的內容類型，以滑鼠右鍵按一下，然後選擇 [ **刪除**]。
    
  
11. **資料夾**內容類型，重複先前的步驟，因此 **ActingRole**列出僅內容類型。選擇 **[確定]**關閉對話方塊。
    
  
12. 三個資料行會出現在資料行清單。選擇 **標題**，以滑鼠右鍵按一下名稱左邊的內容類型，會出現的小型箭號，然後選擇 **刪除**。只有兩個資料行現在應該會列出， **動作項目/女演員**及 **轉換狀態**。
    
  
13. 開啟設計工具] 的 [ **清單**] 索引標籤。在此索引標籤用來設定清單 *執行個體*  ，不清單 *範本*  的特定值。
    
  
14. 變更下列索引標籤上的值:
    
  - **標題**:村莊中的字元
    
  
  - **清單 URL**:清單/CharactersInHamlet
    
  
  - **描述**:村莊及轉換資訊中的字元。
    
  

    將核取方塊保留其預設狀態、 儲存該檔案，並關閉設計工具。
    
  
15. 在 **方案總管**中的清單執行個體可能舊名稱。如果是這樣，開啟 **CharactersInShowInstance**快顯功能表，選擇 [ **重新命名**，並變更名稱為CharactersInHamlet。
    
  
16. 開啟 schema.xml 檔案。
    
  
17. 在檔案] 的ActingRole的 **Name**屬性值可能會有兩個 **ContentType**項目，另一個名為 **ListFieldsContentType**。只有一個稱為的ActingRole所屬，因此會刪除任何其他 **ContentType**項目。
    
    > **注意事項**
      > 有可能無法分行符號 **ContentType**項目，在此情況下，可能會出現在第一個有只有一個。向右捲動，並仔細檢查其他人。
18. **Fields**項目應該有兩個 **Field**項目 (也就是在同一行如果它們之間沒有分行符號)。其中一個完全應該複製 **Field**中的項目 **動作項目**網站欄 elements.xml 和其他應該完全複製 **CastingStatus**網站欄 elements.xml **Field**項目。如果有不完全符合，包括所有的子元素 (例如 **CHOICES**和 **MAPPINGS**項目)，從網站欄 elements.xml 檔案複製 **Field**項目，並貼取代 schema.xml 檔案中的不對稱的 **Field**項目。
    
  
19. 仍在 schema.xml 檔案，其 BaseViewID 值為 「 0 」， **View**項目中取代現有的 **ViewFields**項目的使用下列標記。(使用完全此 GUID 名為 `LinkTitle` **FieldRef** )。
    
  ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
  ```

20. 取代在個別的網站欄 elements.xml 檔案中的 Guid 兩個遺失識別碼屬性值。別忘了框架設定括號 「 {」。
    
  
21. 仍在 schema.xml 檔案，其 BaseViewID 值為 「 1 」， **View**項目中取代現有的 **ViewFields**項目的使用下列標記。(使用完全此 GUID 名為 `LinkTitle` **FieldRef** )。
    
  ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
  ```

22. 複製 `Actor`和 `CastingStatus`您新增至先前檢視]，此 **ViewFields**元素，為 `LinkTitle` **FieldRef**同層級的兩個 **FieldRef**項目。
    
  
23. 儲存並關閉 schema.xml 檔案。
    
  
24. 開啟子系的清單執行個體 **CharactersInHamlet**elements.xml 檔案。
    
  
25. 藉由新增下列標記 **ListInstance**項目的子項填入初始資料清單。
    
  ```
  
<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
  ```

2. 在 **方案總管**] 中，選擇 [ **Feature1**開啟功能設計工具]。在設計工具中，將 **標題**劇場和影片資料元件， **描述**設定為網站欄、 內容類型及劇場與影片的相關資料的清單執行個體。。儲存檔案，並關閉設計工具]。
    
  
3. 如果尚未重新命名 **Feature1** **方案總管**] 中，開啟捷徑功能表，選擇 **重新命名**，然後將其重新命名為TheaterAndMovieDataComponents。
    
  

### 遠端 web 應用程式專案的程式碼


- 開發 web 應用程式，即您慣用的平台的堆疊的任何其他 web 應用程式。Microsoft 堆疊，您可以使用的其餘部分/OData web 服務或其中一個用戶端物件模型中SharePoint 2013。非 Microsoft 堆疊，您可以使用其餘/OData 端點SharePoint 2013中，執行增益集網頁版中的資料建立/已讀取/更新/刪除 (CRUD) 作業。
    
    > **注意事項**
      > 當您新增組件的參考Visual Studio您 web 應用程式專案時，將 **複製到本機**屬性的組件 **True**，除非您知道網頁伺服器，已安裝的組件，或您可以確保它已安裝增益集部署之前。.NET Framework Microsoft Azure Web 角色和Azure 網站上安裝。但SharePoint 2013用戶端組件和各種 Microsoft managed 程式碼擴充，不會安裝基礎。Visual Studio 2012 Office 開發人員工具自動新增常用的SharePoint Add-ins某些組件的參照，並設定 **複製到本機**的屬性。

    連續的範例中，您可以開發ASP.NET web 應用程式。請執行下列步驟。
    
1. 開啟 Default.aspx 檔案，並以下列標記取代本文元素的檔案。標記會 **取得轉換**] 按鈕來選擇、 會讀取 **村莊字元**清單的增益集網頁版中，並且顯示 [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) 其資料時控制的只之後按下按鈕時，才會出現。
    
  ```HTML
  
<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
  ```

2. 開啟 Default.aspx.cs 檔案，並為其新增下列 **using**陳述式。
    
  ```cs
  
using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
  ```


    這些陳述式的最後一個參照宣告 TokenHelper.cs 檔案中的命名空間。
    
  
3. 將下列欄位加入 **Default**類別。
    
  ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
  ```

4. 下列程式碼使用 **TokenHelper**類別以從-OAuth 相容安全性 token 伺服器取得權杖取代 **Page_Load**方法。稍後擷取] 按鈕，然後按一下 [事件處理常式存取權杖會儲存在 [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) ] 屬性中的按鈕。
    
  ```cs
  
protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;
        
        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
  ```

5. 新增下列事件處理常式 **Default**類別。處理常式開始擷取會儲存在] 按鈕的 [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) 屬性的存取權杖。
    
  ```cs
  
protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
  ```

6. 若要重新取得修改處理常式需求增益集在回傳，網址，因此新增下列程式碼。
    
  ```cs
  
if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
  ```

7. 新增使用其中一個SharePoint 2013其餘/OData 端點取得清單資料的下列行。在此範例中，程式碼會讀取部署增益集網站 **中的村莊字元**清單。這項服務的 Api 讓您更輕鬆，在單一線條的程式碼，以選取清單，並指定三個欄位從 [傳回] 清單中。請注意，OData URL 中您必須使用內部欄位 (欄) 的名稱，而不是顯示名稱] 中的程式碼使用 `Title`，  `Actor`，因此 `CastingStatus`而非 `Character`、  `Actor/Actress`及 `Casting Status.`有關其餘/OData web 服務，請參閱 [在 SharePoint 其餘邀請中使用 OData 查詢作業](use-odata-query-operations-in-sharepoint-rest-requests.md)。
    
  ```cs
  
// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
  ```

8. 新增下列使用 [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) 和 [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) 類別 [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) 命名空間來建構 HTTP 要求與回應物件的程式碼。
    
  ```cs
  
// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
  ```

9. 新增下列功能來剖析 ATOM 格式化回應 XML 程式碼。使用 [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) 命名空間的類別來剖析會傳回的資料，並建構 [List<T>](http://msdn2.microsoft.com/ZH-TW/library/6sh2ey19)從 SharePoint 清單的項目。(您也可以使用 [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) 命名空間的類別)。請注意，在 SharePoint 會傳回 XML 項目的子項目 **entry**保留中繼資料相關清單項目。實際的資料列資料的 SharePoint 清單項目已關閉 **properties**項目中的巢狀的兩個圖層。原因 [Elements<T>](http://msdn2.microsoft.com/ZH-TW/library/bb348465)擴充方法使用兩次，篩選出較高層級的。
    
  ```cs
  
// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
  ```

10. 新增下列 LINQ 查詢來建構擁有只是屬性的匿名類型 [IEnumerable<T>](http://msdn2.microsoft.com/ZH-TW/library/9eekhta0)集合您需要和任何其他人。請注意，雖然程式碼，必須指向依其內部名稱 `Title`的 [項目標題] 欄位的屬性名稱中的值會指派、 可以命名 `Character`匿名類型。一個效果是，集合繫結至格線控制項，更適當的名稱 **字元**會出現在頁面上。
    
  ```cs
  
var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

  ```

11. 完成下列程式碼將資料繫結至 [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) 控制項在頁面上的處理常式。在格線預設的屬性名稱匿名類型的欄標題: `Character` `Actor`，與 `CastingStatus`。 [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) 控制項的屬性會讓您控制 [名稱] 和 [格式設定的欄標題，因此您可能會有 **動作項目/女演員**和 **轉換的狀態**，以符合在 SharePoint 中的欄標題。為求，這些技術不如下所示。(您也可以使用 [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) 控制項)。
    
  ```cs
  
GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

  ```

12. 儲存所有檔案。
    
  

### 若要測試並偵錯 SharePoint 增益集


1. 若要測試SharePoint Add-in和其遠端 web 應用程式，請在 [ Visual Studio 2012中選擇 F5 鍵。在本機的 web 應用程式部署到 IIS Express。SharePoint Add-in將已安裝至目標 SharePoint 網站。(在連續的範例中，遠端增益集並 *不*  會自動與 *主機*  網頁和增益集主體互動嘗試都有 *增益集*  網路的權限，因此您 *不*  會提示您要授與權限。)目標 SharePoint 網站的 [ **網站內容**] 頁面隨即開啟，然後您看到新增益集那里列出。
    
  
2. 選擇 [ SharePoint Add-in，然後遠端 web 應用程式會開啟至您指定 AppManifest.xml 檔案中的 **StartPage**項目中的頁面。使用 web 應用程式如有需要驗證正常運作。在本主題連續範例中，只要選取按鈕。這樣會建立格線，並填入增益集網頁版 **中的村莊字元**清單。
    
  

## 發佈 SharePoint 增益集
<a name="Publish"> </a>

若要發佈SharePoint Add-in您上傳的增益集套件公司的增益集目錄或 Office 增益集存放區。如需詳細資訊，請參閱 [發佈到Office Store或組織的增益集型錄](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog)與 [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)。
  
    
    

## 疑難排解
<a name="Troubleshooting"> </a>

如果無法使用增益集，您應該考慮是否連同 CAML 標記中的錯誤會封鎖部署的 SharePoint 元件。使用類似下列程序，這根據連續範例中，確認部署。
  
    
    

### 若要測試的增益集網站佈建


1. 開啟主機網站的 [ **網站設定**] 頁面。在 [ **網站集合管理**] 區段中，選擇 [ **網站階層**連結]。
    
  
2. 在 [ **網站階層**] 頁面中，您會看到增益集列出其 URL。請勿啟動它。不過，複製 URL，並使用 URL 中其餘的步驟。
    
  
3. 瀏覽至 _URL_of_app_web_/_layouts/15/ManageFeatures.aspx，然後在隨後便會開啟 [ **網站功能**] 頁面中，確認 [ **劇場和影片資料元件**位於您SharePoint Add-in的功能依字母順序排列清單，且其狀態是 **作用中**。
    
  
4. 瀏覽至 _URL_of_app_web_/_layouts/15/mngfield.aspx，然後在隨後便會開啟 [ **網站欄**] 頁面中，確認 **劇場和影片**群組是在清單中的網站欄包含您新增自訂網站和資料行 **動作項目/女演員** **轉換狀態**。
    
  
5. 瀏覽至 _URL_of_app_web_/_layouts/15/mngctype.aspx，然後在隨後便會開啟 [ **網站內容類型**] 頁面中，確認 **劇場和影片**群組是內容類型的清單中，它會包含您的新 **ActingRole**內容類型。
    
  
6. 選擇連結至 **ActingRole**內容類型。在 [開啟 [ **網站內容類型**] 頁面中，確認內容類型有兩種新增網站欄類型， **動作項目/女演員**和 **轉換的狀態**，項目標題欄位具有您自訂的顯示名稱: **字元**。
    
  
7. 瀏覽至 _URL_of_app_web_/_layouts/15/mcontent.aspx，然後在隨後開啟的 [ **網站文件庫和清單**頁面，請確認有 **自訂 」 的字元在村莊 」**連結。
    
  
8. 選擇 [ **自訂] 的字元在村莊 」**連結，並確認在 [清單設定] 頁面上清單僅限內容類型是您自訂的 **ActingRole**內容類型及您兩個新的網站欄， **動作項目/女演員**及 **轉換狀態**會列在 [ **欄**] 區段中。([標題] 欄可能會出現，而非顯示您的 **字元**其內部名稱 **標題**]。)
    
    > **注意事項**
      > 如果在頁面上沒有任何 **內容類型**] 區段，您必須啟用的內容類型的管理。按一下 [ **進階設定**] 連結，在 [ **進階設定**] 頁面上啟用的內容類型的管理並按一下 **[確定]**。您會回到前一頁，現在 **內容類型**] 區段中的清單。
9. 頁面頂端附近是之清單的 **網址**。複製並貼上到您的瀏覽器的網址列，然後瀏覽至清單。請確認清單中有您所建立的範例項目。([標題] 欄可能會出現，而非顯示您的 **字元**其內部名稱 **標題**]。)
    
  

## 後續步驟
<a name="NextSteps"> </a>

本文將示範如何建立簡單的混合式SharePoint Add-in遠端 web 應用程式。接下來的步驟，為考慮下列事項:
  
    
    

- 將完整的 crud 以功能新增至 [增益集與其餘/OData 結束點] 或 [用戶端物件模型中。如需詳細資訊，請參閱 [在 SharePoint 其餘邀請中使用 OData 查詢作業](use-odata-query-operations-in-sharepoint-rest-requests.md)及 [使用 SharePoint 2013 用戶端程式庫程式碼完成基本作業](complete-basic-operations-using-sharepoint-2013-client-library-code.md)。
    
  
- 當地語系化您SharePoint Add-in其他文化特性。如需詳細資訊，請參閱 [找出 SharePoint 增益集](localize-sharepoint-add-ins.md)。
    
  
- 建立的 Windows Phone 小幫手] 增益集重複遠端 web 應用程式的功能。如需詳細資訊，請參閱 [建立行動裝置的 SharePoint 增益集 2013年](http://msdn.microsoft.com/en-us/library/office/jj163228.aspx)。
    
  

## 其他資源
<a name="SP15createcloud_bk_addlresources"> </a>


-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

