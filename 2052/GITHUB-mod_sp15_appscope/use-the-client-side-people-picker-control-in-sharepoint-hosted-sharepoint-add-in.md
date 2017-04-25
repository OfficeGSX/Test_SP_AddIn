---
title: 在 SharePoint 托管的 SharePoint 外接程序中使用客户端人员选取器控件
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# 在 SharePoint 托管的 SharePoint 外接程序中使用客户端人员选取器控件
了解如何在 SharePoint承载的 SharePoint 外接程序中使用客户端"人员选取器"控件。
> **重要信息**
> 本主题假定您知道如何创建 SharePoint 承载的 SharePoint 外接程序。要了解如何创建，请从 [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)开始。 





## SharePoint 2013 中的客户端人员选取器控件是什么？
<a name="bk_whatIs"> </a>

利用客户端人员选取器控件，用户可以快速搜索和选择其组织中的人员、组和声明的有效用户帐户。该选取器是提供跨浏览器支持的 HTML 和 JavaScript 控件。将该选取器添加到您的外接程序中很简单：在您的标记中，添加该控件的容器元素和该控件的引用及其依赖关系。然后在您的脚本中，调用 **SPClientPeoplePicker_InitStandaloneControlWrapper** 全局函数来呈现并初始化该选取器。



该选取器由 **SPClientPeoplePicker** 对象表示，它提供其他客户端控件可用于从该选取器获取信息或执行其他操作的方法。您可以使用 **SPClientPeoplePicker** 属性来使用特定于控件的设置配置该选取器，例如允许多个用户或指定缓存选项。该选取器还使用 Web 应用程序配置设置，例如 Active Directory 域服务参数或目标林。将自动（从 **SPWebApplication.PeoplePickerSettings** 属性）选取 Web 应用程序配置设置。



该选取器具有以下组件：




- 用于输入用户、组和声明的名称的输入文本框。


- 显示已解析的用户、组和声明的名称的范围控件。


- 使用匹配的查询结果自动填充下拉框的隐藏的 **div** 元素。


- 自动填充控件。



> **注释**
> 该选取器及其功能在 **clientforms.js**、 **clientpeoplepicker.js** 和 **autofill.js** 脚本文件中定义，这些文件位于服务器上的 %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\TEMPLATE\\LAYOUTS 文件夹中。





## 设置您的开发环境以在 SharePoint 外接程序 2013 中使用客户端人员选取器控件的先决条件
<a name="bk_prereqs"> </a>

本文假定您通过在 Office 365 开发人员网站上使用 Napa来创建 SharePoint 外接程序。如果您使用此开发环境，则已经满足先决条件。




> **注释**
> 转到 [在 Office 365 上设置 SharePoint 加载项的开发环境](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md)来了解如何注册开发人员网站并开始使用 Napa。 




如果您没有在开发人员网站上使用 Napa，则需要：




- 具有至少一个目标用户的 SharePoint 2013


- Visual Studio 2008 或 Visual Studio 2013


- Visual Studio 2013 Office 开发人员工具



> **注释**
> 有关如何设置符合您的需求的开发环境的指南，请参阅 [开始构建 Office 和 SharePoint 相关应用程序](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)。 




以下步骤介绍将该选取器添加到您的外接程序中，然后从其他客户端控件获取其用户信息的概要步骤。要查看相应代码，请参阅 [代码示例：使用客户端人员选取器](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example)。



可以在 SharePoint 承载的 SharePoint 外接程序（而非提供程序承载的外接程序）中使用客户端人员选取器控件。有关显示在提供程序承载的外接程序中如何实施人员选取器控件的示例，请下载  [Office 外接程序模型示例](http://officeams.codeplex.com)。




## 在 SharePoint 托管的外接程序中使用客户端人员选取器控件
<a name="bk_steps"> </a>


### 在页面标记中


1. 添加对客户端人员选取器控件的脚本依赖关系的引用。


2. 添加页面 UI 的 HTML 标记。此示例中的外接程序定义两个 **div** 元素：一个用于要呈现在其中的选取器，一个用于 UI：一个调用脚本以查询选取器和显示用户信息的元素的按钮。



### 在脚本中


1. 创建 JSON 字典来用作存储特定于选取器的属性的架构，例如 **AllowMultipleValues** 和 **MaximumEntitySuggestions**。


2. 调用 **SPClientPeoplePicker_InitStandaloneControlWrapper** 全局函数。


3. 从页面中获取选取器对象。


4. 查询选取器。此示例中的外接程序调用 **GetAllUserInfo** 方法来获取已解析的用户的所有用户信息，并调用 **GetAllUserKeys** 方法来仅获取已解析的用户的密钥。


5. 使用 JavaScript 对象模型获取用户 ID。用户 ID 不包含在选取器返回的信息中，所以该外接程序调用 **SP.Web.ensureUser** 方法并从返回的 **SP.User** 对象中获取 ID。


选取器的呈现、初始化及其他功能由服务器处理，包括针对 SharePoint 身份验证提供程序搜索并解析用户输入。




## 代码示例：在 SharePoint 托管的外接程序中使用客户端人员选取器
<a name="bk_example"> </a>

以下 HTML 和 JavaScript 代码示例将客户端人员选取器控件添加到 SharePoint 托管的外接程序中。



第一个示例显示 Default.aspx 页面中的 **PlaceHolderMain** **asp:Content** 标记的页面标记。此代码引用选取器的脚本依赖关系，为选取器将呈现在其中的 DOM 元素提供唯一 ID，并定义外接程序的 UI。






```HTML

<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
    <SharePoint:ScriptLink name="clienttemplates.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientforms.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientpeoplepicker.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="autofill.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.runtime.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.core.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <div id="peoplePickerDiv"></div>
    <div>
        <br/>
        <input type="button" value="Get User Info" onclick="getUserInfo()"></input>
        <br/>
        <h1>User info:</h1>
        <p id="resolvedUsers"></p>
        <h1>User keys:</h1>
        <p id="userKeys"></p> 
        <h1>User ID:</h1>
        <p id="userId"></p>
    </div>
</asp:Content>
```


> **注释**
> 根据您的环境，您可能不必明确引用所有这些依赖关系。 




下面的示例显示来自 App.js 文件的脚本。此脚本初始化并呈现选取器，查询它以获取用户信息，然后使用 JavaScript 对象模型获取该用户 ID。






```

// Run your custom code when the DOM is ready.
$(document).ready(function () {

    // Specify the unique ID of the DOM element where the
    // picker will render.
    initializePeoplePicker('peoplePickerDiv');
});

// Render and initialize the client-side People Picker.
function initializePeoplePicker(peoplePickerElementId) {

    // Create a schema to store picker properties, and set the properties.
    var schema = {};
    schema['PrincipalAccountType'] = 'User,DL,SecGroup,SPGroup';
    schema['SearchPrincipalSource'] = 15;
    schema['ResolvePrincipalSource'] = 15;
    schema['AllowMultipleValues'] = true;
    schema['MaximumEntitySuggestions'] = 50;
    schema['Width'] = '280px';

    // Render and initialize the picker. 
    // Pass the ID of the DOM element that contains the picker, an array of initial
    // PickerEntity objects to set the picker value, and a schema that defines
    // picker properties.
    this.SPClientPeoplePicker_InitStandaloneControlWrapper(peoplePickerElementId, null, schema);
}

// Query the picker for user information.
function getUserInfo() {

    // Get the people picker object from the page.
    var peoplePicker = this.SPClientPeoplePicker.SPClientPeoplePickerDict.peoplePickerDiv_TopSpan;

    // Get information about all users.
    var users = peoplePicker.GetAllUserInfo();
    var userInfo = '';
    for (var i = 0; i < users.length; i++) {
        var user = users[i];
        for (var userProperty in user) { 
            userInfo += userProperty + ':  ' + user[userProperty] + '<br>';
        }
    }
    $('#resolvedUsers').html(userInfo);

    // Get user keys.
    var keys = peoplePicker.GetAllUserKeys();
    $('#userKeys').html(keys);

    // Get the first user's ID by using the login name.
    getUserId(users[0].Key);
}

// Get the user ID.
function getUserId(loginName) {
    var context = new SP.ClientContext.get_current();
    this.user = context.get_web().ensureUser(loginName);
    context.load(this.user);
    context.executeQueryAsync(
         Function.createDelegate(null, ensureUserSuccess), 
         Function.createDelegate(null, onFail)
    );
}

function ensureUserSuccess() {
    $('#userId').html(this.user.get_id());
}

function onFail(sender, args) {
    alert('Query failed. Error: ' + args.get_message());
}
```


## 其他资源
<a name="bk_addresources"> </a>


-  [在 SharePoint 2013 中创建 UX 组件](create-ux-components-in-sharepoint-2013.md)


-  [人员选取器和声明提供程序概述 (SharePoint 2013)](http://technet.microsoft.com/library/gg602078.aspx)


-  [配置 SharePoint 2013 中的人员选取器](http://technet.microsoft.com/library/gg602075.aspx)



