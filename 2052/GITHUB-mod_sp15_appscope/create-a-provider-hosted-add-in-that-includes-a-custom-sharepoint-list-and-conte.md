---
title: 创建包括自定义 SharePoint 列表和内容类型的提供商托管的外接程序
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# 创建包括自定义 SharePoint 列表和内容类型的提供商托管的外接程序
使用 Visual Studio 2012 Office 开发人员工具创建一个 SharePoint 外接程序，其中将云托管的 Web 应用程序与自定义 Sharepoint 托管的列表模板、列表实例和自定义内容类型组合起来。了解如何使用 REST/OData Web 服务与 SharePoint 2013 外接程序 Web 交互，以及如何在 SharePoint 外接程序中实现 OAuth。
云托管的 SharePoint 外接程序中可以包括最大多数经典的 SharePoint 组件，如自定义内容类型、自定义列表定义和工作流。本文中的简单示例包含以下内容：
  
    
    


- 一个外接程序 Web，它带有：
    
  - 一些自定义网站栏
    
  
  - 一个使用自定义栏的自定义内容类型
    
  
  - 一个使用自定义内容类型的自定义列表模板
    
  
  - 基于自定义列表定义的一个列表实例
    
  
- 一个从该列表实例中读取数据的 ASP.NET Web 应用程序
    
  

## 创建此 SharePoint 外接程序的先决条件
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/zh-cn/download/details.aspx?id=30682) 或更高版本。
    
  
-  [Office 开发人员工具](https://msdn.microsoft.com/zh-cn/office/aa905340.aspx)
    
  
- 一个 SharePoint 2013 安装，用于测试和调试
    
  - 它可以与您的开发计算机是同一台计算机，您也可以使用远程 SharePoint 2013 安装进行开发。如果您使用远程安装，则需要在目标安装上安装可再发行的客户端对象模型。它在 Microsoft 下载中心作为一个可再发行包来提供。搜索"SharePoint Server 2013 客户端组件 SDK"或"SharePoint Online 客户端组件 SDK"。
    
  
  - 必须从"开发人员网站"网站定义（您可以在管理中心创建它）创建测试 SharePoint 网站。
    
  
  - 远程 Web 应用程序通过使用 JavaScript 和 [跨域库](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)或使用  [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md) 与外接程序 Web 通信。如果使用 OAuth（例如本文的后续示例中就使用它），SharePoint 2013 安装必须配置为使用 OAuth。
    
  

> [!注释]
> 有关如何设置符合您的需求的开发环境的指南，请参阅 [开始构建 Office 和 SharePoint 相关应用程序](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)。 
  
    
    


### 创建外接程序需要了解的核心概念

在创建您的第一个外接程序之前，您应该对 SharePoint 外接程序以及 Sharepoint 托管和云托管的 SharePoint 外接程序之间的差异有基本的了解。您可以通过表 1 中的文章了解这些信息。
  
    
    

**表 1. 创建外接程序的核心概念**


|**文章标题**|**说明**|
|:-----|:-----|
| [SharePoint 外接程序](sharepoint-add-ins.md) <br/> |了解 SharePoint 2013 中新的外接程序模型，您可以利用此模型来创建对最终用户来说是小型的易于使用的解决方案的外接程序。  <br/> |
| [SharePoint 外接程序体系结构的重要方面和开发前景](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |了解 SharePoint 外接程序和 SharePoint 外接程序的模型体系结构的各个方面，其中包括外接程序托管选项、用户界面 (UI) 选项、部署系统、安全系统和生命周期。  <br/> |
| [为开发和托管 SharePoint 外接程序选择模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |了解您可以托管 SharePoint 外接程序的各种方式。  <br/> |
   

## 开发 SharePoint 外接程序
<a name="Develop"> </a>

在本节的过程中，您将在开发计算机上使用 SharePoint 组件和一个远程 Web 应用程序创建一个 SharePoint 外接程序，其中包括一个外接程序 Web。
  
    
    

### 设置 Visual Studio 2008 解决方案及其元素


1. 在 Visual Studio 2008 中，从"新建项目"向导的模板树中的"Office SharePoint"|"外接程序"节点（在"C#"或"Visual Basic"下）创建一个"SharePoint 2013 外接程序"项目。选择"提供程序托管"托管选项。在本文的后续示例中，使用 C# 语言和项目名称 LocalTheater。
    
  
2. 在向导中选择"完成"。
    
  
3. 在清单设计器中打开 AppManifest.xml 文件。 **Title** 元素将项目名称作为其默认值。将它替换为更友好的名称，因为这是用户在用户界面中看到的外接程序的名称。
    
  
4. 指定外接程序的 **Name**。这是内部名称，只能包含 ASCII 字符且不能包含空格；例如，LocalTheater。
    
  
5. 在 Web 应用程序项目中打开 Web.config 文件并将元素  `<customErrors mode="Off"/>` 添加到 **system.web** 元素。
    
  
6. 检查以确认在 Web 应用程序项目中有对下列程序集的引用。（如果您的 Visual Studio 2008 版本未自动添加引用，则现在添加它们。）
    
  - **Microsoft.IdentityModel.dll** 此程序集与 Windows Identity Foundation (WIF) 一起安装到全局程序集缓存中。由于这是 .NET Framework 3.5 程序集，默认情况下会在"添加引用"对话框的"Framework"节点中将其筛选掉。您可以通过直接浏览到您的开发计算机的 `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` 目录添加对它的引用。
    
  
  - **Microsoft.IdentityModel.Extensions.dll** 您可以通过直接浏览到您的开发计算机的 `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` 文件夹添加对它的引用。
    
  
  - **System.IdentityModel.dll** 此程序集是 .NET Framework 4 的一部分，它显示在"添加引用"对话框的"程序集"|"框架"节点上。
    
  
7. 如果您的远程 Web 应用程序访问主机 Web 以及外接程序 Web 中的信息，您必须将一个 **AppPermissionRequests** 元素（具有一个或多个 **AppPermissionRequest** 子元素）添加到 AppManifest.xml 文件。（本文的后续示例中的 Web 应用程序仅访问外接程序 Web。外接程序主体自动具有访问外接程序 Web 所需的所有权限，因此示例中的 AppManifest.xml 没有 **AppPermissionRequests** 元素）。有关外接程序权限请求以及如何添加它们的详细信息，请参阅 [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)。
    
  

### 添加 SharePoint 组件


1. 将 SharePoint 组件添加到外接程序的方式与您将其添加到经典服务器场解决方案完全相同。然而，并非每一种 SharePoint 组件都可以包含在 SharePoint 外接程序中。这些组件服务的目的在 SharePoint 外接程序中以其他方式完成。有关在 SharePoint 外接程序中可以包含哪些类型的 SharePoint 组件以及如何将其包含在项目中的详细信息，请参阅  [可位于 SharePoint 外接程序中的 SharePoint 组件的类型](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps)。
    
    为了适应后续示例，请使用下列过程。它们提供的示例说明了如何使用 Visual Studio 2008 将自定义栏、内容类型、列表模板和列表实例添加到 SharePoint 外接程序。
    
### 创建自定义栏类型


1. 在"解决方案资源管理器"中，将 SharePoint"网站栏"项添加到名称为 Actor 的 SharePoint 外接程序项目。
    
  
2. 在新网站栏的 elements.xml 文件中，编辑 **Field** 元素，使其具有以下示例中显示的属性和值，只是您不应从 Visual Studio 2008 为其生成的值更改 **ID** 属性的 GUID。别忘了大括号"{}"。
    
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

3. 将另一个"网站栏"添加到名为 CastingStatus 的项目。
    
  
4. 在新网站栏的 elements.xml 文件中，编辑 **Field** 元素，使其具有以下示例中显示的属性和值，只是您不应从 Visual Studio 2008 为其生成的值更改 **ID** 属性的 GUID。
    
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

5. 因为这是选择字段，您必须指定可能的选项、用户在进行选择时它们应在下拉列表中出现的顺序以及默认选项。将以下子标记添加到 **Field** 元素。
    
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


### 创建自定义内容类型


1. 在"解决方案资源管理器"中，将一个 SharePoint"内容类型"项添加到名为 ActingRole 的 SharePoint 外接程序项目。当向导提示选择基内容类型时，请选择"项"，然后选择"完成"。
    
  
2. 如果内容类型设计器没有自动打开，请在"解决方案资源管理器"中选择"ActingRole"内容类型以打开它。
    
  
3. 在设计器中打开"内容类型"选项卡，并按如下所示填写文本框：
    
  - **内容类型名称**：ActingRole
    
  
  - **说明**：表示在一部戏剧或电影中的角色。
    
  
  - **组名称**：Theater and Movies
    
  
4. 验证选项卡上的复选框 *未*  选中。"继承父内容类型的栏"复选框默认情况下可能选中。 *请务必将其清除。* 
    
  
5. 在设计器中打开"栏"选项卡。
    
  
6. 使用网格将两个网站栏添加到内容类型。它们在下拉列表中按其显示名称列出："Actor/Actress"和"CastingStatus"。（如果它们未列出，可能是您在添加自定义网站栏后未保存该项目。选择"全部保存"。）
    
  
7. 保存文件并关闭设计器。
    
  
8. 下一步需要您直接在内容类型的原始 XML 中工作，因此，在"解决方案资源管理器"中，选择"ActingRole"内容类型的 elements.xml 文件子项。
    
  
9. 文件中已有针对您添加的两个栏的 **FieldRef** 元素。为两个内置的 SharePoint 2013 栏添加 **FieldRef** 元素作为已存在的两个项的对应项。以下是这些元素的标记。 *您必须为 ID 属性使用相同的 GUID，因为这些是具有固定 ID 的内置字段类型。*  为自定义网站栏在这两个 **FieldRef** 元素 *上*  添加它们。
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  ```


    请注意，我们将这些字段指定了自定义显示名称："Character"，即表示戏剧或电影中的角色。
    
  

### 创建自定义列表模板和列表实例


1. 将一个 SharePoint"列表"添加到名为 CharactersInShow 的 SharePoint 外接程序项目。在"SharePoint 自定义向导"的"选择列表设置"页上，保留默认"CharactersInShow"上的列表显示名称，选择"创建自定义列表的依据"选项按钮，并在下拉列表中选择"默认值（空白）"。然后选择"完成"。
    
  
2. 完成向导后，会创建一个"CharactersInShow"列表模板，它带有一个名为"CharactersInShowInstance"的子列表实例。默认情况下，列表设计器可能已打开。在后面的步骤中会使用它。
    
  
3. 打开"CharactersInShow"列表模板的 elements.xml 子项（ *不是*  "CharactersInShowInstance"的 elements.xml 子项）。
    
  
4. 在 **DisplayName** 属性中添加空格使其更为友好："Characters In Show"。
    
  
5. 将 **Description** 属性设置为"The characters in a play or movie."
    
  
6. 保留所有其他属性为默认值，然后保存并关闭该文件。
    
  
7. 如果列表设计器未打开，请在"解决方案资源管理器"中选择"CharactersInShow"节点。
    
  
8. 在设计器中打开"栏"选项卡，然后选择"内容类型"按钮。
    
  
9. 在"内容类型设置"对话框中，添加"ActingRole"内容类型。
    
  
10. 在类型列表中选择"ActingRole"内容类型，然后选择"设置为默认值"按钮。
    
    选择"项"内容类型，右键单击内容类型名称左侧显示的小箭头，然后选择"删除"。
    
  
11. 对"文件夹"内容类型重复上述步骤，使"ActingRole"成为列出的唯一内容类型。选择"确定"以关闭对话框。
    
  
12. 现在栏列表中有三栏。选择"标题"，右键单击内容类型名称左侧显示的小箭头，然后选择"删除"。现在应该仅列出两栏，即"Actor/Actress"和"Casting Status"。
    
  
13. 打开设计器的"列表"选项卡。此选项卡用于为列表 *实例*  （而不是列表 *模板*  ）设置某些值。
    
  
14. 将此选项卡上的值更改为以下内容：
    
  - "标题"：Characters in Hamlet
    
  
  - "列表 URL"：Lists/CharactersInHamlet
    
  
  - "说明"：The characters in Hamlet and casting information.
    
  

    将复选框保留为其默认状态，保存文件并关闭设计器。
    
  
15. 该列表实例在"解决方案资源管理器"中可能有旧名称。如果这样，请打开"CharactersInShowInstance"的快捷菜单，选择"重命名"并将名称更改为 CharactersInHamlet。
    
  
16. 打开 schema.xml 文件。
    
  
17. 文件中可能有两个 **ContentType** 元素，其中一个元素的 **Name** 属性值为ActingRole，另一个为"ListFieldsContentType"。只有名为 ActingRole 的属性属于本项目，所以删除任何其他 **ContentType** 元素。
    
    > [!注释]
      > 各 **ContentType** 元素之间可能没有换行符，在这种情况下，最初可能只显示一个。向右滚动并仔细检查其他元素。
18. **Fields** 元素应该有两个 **Field** 元素（如果它们之间无换行符，则在一行上）。其中一个应与"Actor"网站栏 elements.xml 中的 **Field** 元素完全相同，另一个应与"CastingStatus"网站栏 elements.xml 中的 **Field** 元素完全相同。如果没有精确匹配，包括所有子元素（如 **CHOICES** 和 **MAPPINGS** 元素），请从网站栏 elements.xml 文件复制 **Field** 元素并将其粘贴替换 schema.xml 文件中不匹配的 **Field** 元素。
    
  
19. 仍在 schema.xml 文件中，在 **View** 元素（其 BaseViewID 值为"0"）中，将现有的 **ViewFields** 元素替换为以下标记。（对名为 **FieldRef** 的 `LinkTitle` 准确使用此 GUID。）
    
  ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
  ```

20. 将这两个缺少的 ID 属性值替换为在各自网站栏 elements.xml 文件中的 GUID。别忘了框架大括号"{}"。
    
  
21. 仍在 schema.xml 文件中，在 **View** 元素（其 BaseViewID 值为"1"）中，将现有的 **ViewFields** 元素替换为以下标记。（对名为 **FieldRef** 的 `LinkTitle` 准确使用此 GUID。）
    
  ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
  ```

22. 将您添加到上一视图的  `Actor` 和 `CastingStatus` 的两个 **FieldRef** 元素复制到此 **ViewFields** 元素作为 `LinkTitle` **FieldRef** 的同辈。
    
  
23. 保存并关闭 schema.xml 文件。
    
  
24. 打开作为"CharactersInHamlet"的列表实例的子项的 elements.xml 文件。
    
  
25. 通过添加以下标记作为 **ListInstance** 元素的子项，用一些初始数据填充列表。
    
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

2. 在"解决方案资源管理器"中，选择"Feature1"以打开功能设计器。在设计器中，将"标题"设置为 Theater and Movie Data Components 并将"说明"设置为Site columns, content types, and list instances for data about theater and movies.。保存文件，然后关闭设计器。
    
  
3. 如果"解决方案资源管理器"中的"Feature1"尚未重命名，请打开其快捷菜单，选择"重命名"并将其重命名为 TheaterAndMovieDataComponents。
    
  

### 对远程 Web 应用程序项目进行编码


- 像开发任何其他 Web 应用程序一样，为您首选的平台堆栈开发 Web 应用程序。对于 Microsoft 堆栈，您可以使用 REST/OData Web 服务或 SharePoint 2013 中的客户端对象模型之一。对于非 Microsoft 堆栈，您可以使用 SharePoint 2013 中的 REST/OData 终结点对外接程序 Web 中的数据执行创建/读取/更新/删除 (CRUD) 操作。
    
    > [!注释]
      > 在将对程序集的引用添加到您在 Visual Studio 中的 Web 应用程序项目时，将程序集的"Copy Local"属性设置为 **True**，除非您知道 Web 服务器上已经安装了程序集，或者可以确保在您部署外接程序之前安装程序集。Microsoft Azure Web 角色和 Azure 网站 上安装了 .NET Framework。但是未安装 SharePoint 2013 客户端程序集和各种 Microsoft 托管代码扩展和基础。Visual Studio 2012 Office 开发人员工具自动添加对在 SharePoint 外接程序中常用的一些程序集的引用，并设置"Copy Local"属性。 

    对于后续示例，您开发一个 ASP.NET Web 应用程序。请执行下列步骤。
    
1. 打开 Default.aspx 文件，并使用以下标记替换文件的正文元素。标记会添加一个"获得演员表"按钮，当选择此按钮时，会读取外接程序 Web 中的"哈姆雷特中的角色"列表并在  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) 控件（仅当按下该按钮后，才会出现此控件）中显示其数据。
    
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

2. 打开 Default.aspx.cs 文件，并向其中添加以下 **using** 语句。
    
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


    其中最后一个语句引用在 TokenHelper.cs 文件中声明的命名空间。
    
  
3. 将下列字段添加到 **Default** 类。
    
  ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
  ```

4. 将 **Page_Load** 方法替换为下列代码，这些代码使用 **TokenHelper** 类从符合 OAuth 的安全令牌服务器获取令牌。然后，访问令牌存储在按钮的 [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) 属性中，供该按钮的单击事件处理程序以后检索。
    
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

5. 将以下事件处理程序添加到 **Default** 类。该处理程序首先检索存储在该按钮的 [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) 属性中的访问令牌。
    
  ```cs
  
protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
  ```

6. 该处理程序需要重新获取关于回发的经过修改的外接程序 Web URL，因此添加以下代码。
    
  ```cs
  
if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
  ```

7. 添加以下行（它使用 SharePoint 2013 REST/OData 终结点之一）来获取列表数据。在此示例中，代码读取部署到外接程序 Web 的"哈姆雷特中的角色"列表。通过此服务的 API，只需一行代码即可轻松选择列表并指定从列表返回三个字段。请注意，在 OData URL 中，您必须使用字段（栏）的内部名称而不是显示名称，因此代码使用  `Title`、 `Actor` 和 `CastingStatus`，而不是  `Character`、 `Actor/Actress` 和 `Casting Status.`。有关 REST/OData Web 服务的详细信息，请参阅 [在 SharePoint REST 请求中使用 OData 查询操作](use-odata-query-operations-in-sharepoint-rest-requests.md)。
    
  ```cs
  
// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
  ```

8. 添加以下代码，它们使用  [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) 命名空间的 [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) 和 [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) 类来构建 HTTP 请求和响应对象。
    
  ```cs
  
// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
  ```

9. 添加以下代码以解析 ATOM 格式化的响应 XML。它使用  [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) 命名空间的类解析返回的数据并构建来自 SharePoint 列表中的项的 [List<T>](http://msdn2.microsoft.com/ZH-CN/library/6sh2ey19)。（您也可以使用  [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) 命名空间的类。）请注意，在 SharePoint 返回的 XML 中， **entry** 元素的子元素保留有关列表项的元数据。SharePoint 列表项的实际行数据在 **properties** 元素中向下嵌套两个层。因此，使用两次 [Elements<T>](http://msdn2.microsoft.com/ZH-CN/library/bb348465) 扩展方法来筛选掉更高的级别。
    
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

10. 添加以下 LINQ 查询以构建仅包含您需要的属性（而没有其他属性）的匿名类型的  [IEnumerable<T>](http://msdn2.microsoft.com/ZH-CN/library/9eekhta0) 集合。请注意，尽管代码必须通过项的内部名称 `Title` 引用项的标题字段，仍可以将匿名类型中的属性名称（对其分配值）命名为 `Character`。其结果之一是当集合绑定到一个网格控件时，网页上显示更适当的名称"角色"。
    
  ```cs
  
var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

  ```

11. 处理程序的最后使用以下代码以将数据绑定到网页上的  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) 控件。网格中的列标题默认为匿名类型的属性名称： `Character`、 `Actor` 及 `CastingStatus`。您可以使用  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) 控件的属性来控制列标题的名称和格式，所以您可以让"Actor/Actress"和"Casting Status"匹配 SharePoint 中的列标题。为简单起见，这里不介绍这些技术。（您也可以使用 [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) 控件。）
    
  ```cs
  
GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

  ```

12. 保存所有文件。
    
  

### 测试和调试 SharePoint 外接程序


1. 若要测试 SharePoint 外接程序及其远程 Web 应用程序，请在 Visual Studio 2008 中选择 F5 键。Web 应用程序部署到本地主机上的 IIS Express。SharePoint 外接程序将安装到目标 SharePoint 网站。（在后续示例中，远程外接程序 *不*  尝试与 *主机*  Web 交互且外接程序主体自动拥有对 *外接程序*  Web 的权限，所以 *不*  提示您授予权限。您的目标 SharePoint 网站的"站点内容"页面打开，您会看到其中列出了新外接程序。
    
  
2. 选择 SharePoint 外接程序，远程 Web 应用程序将打开并转到您在 AppManifest.xml 文件中的 **StartPage** 元素中指定的网页。根据需要使用 Web 应用程序验证它能否正常工作。在本主题的后续示例中，只需选择该按钮。这样做会创建一个网格并用外接程序 Web 的"哈姆雷特中的角色"列表填充它。
    
  

## 发布 SharePoint 外接程序
<a name="Publish"> </a>

若要发布您的 SharePoint 外接程序，请将外接程序软件包上载到企业外接程序目录或 Office 外接程序商店。有关详细信息，请参阅 [发布到 Office 商店或组织的外接程序目录](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog)和 [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)。
  
    
    

## 疑难解答
<a name="Troubleshooting"> </a>

如果外接程序无法正常工作，您应该考虑 CAML 标记中是否有错误阻止了 SharePoint 组件的部署。使用类似于以下内容的过程（它基于后续示例）以验证部署。
  
    
    

### 测试外接程序 Web 的设置


1. 打开主机 Web 的"网站设置"。在"网站集管理"部分，选择"网站层次结构"链接。
    
  
2. 在"网站层次结构"页上，您会看到按其 URL 列出的外接程序。不要启动它。相反，请复制该 URL并在其余步骤中使用该 URL。
    
  
3. 导航到  _URL_of_app_web_/_layouts/15/ManageFeatures.aspx，并在打开的"网站功能"页上验证"Theater and Movie Data Components"是否在您的 SharePoint 外接程序中的按字母顺序排列的功能列表上，及其状态是否为"活动"。
    
  
4. 导航到  _URL_of_app_web_/_layouts/15/mngfield.aspx，并在打开的"网站栏"页上验证"Theater and Movies"组是否在网站栏的列表中，以及它是否包含您的新自定义网站栏"Actor/Actress"和"Casting Status"。
    
  
5. 导航到  _URL_of_app_web_/_layouts/15/mngctype.aspx，并在打开的"网站内容类型"页上验证"Theater and Movies"组是否在网站栏的列表中，以及它是否包含您的新"ActingRole"内容类型。
    
  
6. 选择"ActingRole"内容类型的链接。在打开的"站点内容类型"页上，验证内容类型有两个新的网站栏类型"Actor/Actress"和"Casting Status"，并且已对项标题字段分配您的自定义显示名称："角色"。
    
  
7. 导航到  _URL_of_app_web_/_layouts/15/mcontent.aspx，并在打开的"网站库和列表"页上验证是否存在"自定义'Characters in Hamlet'"链接。
    
  
8. 选择"自定义'Characters in Hamlet'"链接，并在列表设置页上验证只有列表的内容类型是您的自定义"ActingRole"内容类型，并且在"栏"部分列出了您的两个新网站栏"Actor/Actress"和"Casting Status"。（标题栏可能显示其内部名称"标题"而不是对其分配的显示名称"角色"。）
    
    > [!注释]
      > 如果该页上没有"内容类型"部分，则必须启用内容类型的管理。单击"高级设置"链接，在"高级设置"页上，启用内容类型的管理并单击"确定"。您将返回到上一页，那里现在有"内容类型"部分的列表。 
9. 网页顶部附近是列表的"Web 地址"。将其复制并粘贴到您的浏览器的地址栏，然后导航到该列表。验证列表中有您创建的示例项目。（"标题"栏可能显示其内部名称"标题"而不是对其分配的显示名称"角色"。）
    
  

## 后续步骤
<a name="NextSteps"> </a>

本文说明了如何使用远程 Web 应用程序创建简单的混合 SharePoint 外接程序。作为后续步骤，请考虑下列内容：
  
    
    

- 将完整的 CRUD 功能添加到具有 REST/OData 终结点或客户端对象模型之一的外接程序。有关详细信息，请参阅 [在 SharePoint REST 请求中使用 OData 查询操作](use-odata-query-operations-in-sharepoint-rest-requests.md)和 [使用 SharePoint 2013 客户端库代码完成基本操作](complete-basic-operations-using-sharepoint-2013-client-library-code.md)。
    
  
- 针对其他文化本地化您的 SharePoint 外接程序。有关详细信息，请参阅 [本地化 SharePoint 外接程序](localize-sharepoint-add-ins.md)。
    
  
- 创建一个复制远程 Web 应用程序的功能的 Windows Phone 套件外接程序。有关详细信息，请参阅 [生成移动 SharePoint 外接程序 2013](http://msdn.microsoft.com/zh-cn/library/office/jj163228.aspx)。
    
  

## 其他资源
<a name="SP15createcloud_bk_addlresources"> </a>


-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [开始创建 SharePoint 承载的 SharePoint 外接程序](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

