---
title: Create a SharePoint Add-in that contains a document template and a task pane add-in
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 6c27681b-8667-48b2-b5b4-d460b4e1d2db
---


# Create a SharePoint Add-in that contains a document template and a task pane add-in
使用 Visual Studio 开发可在从 SharePoint 外接程序打开的文档中显示的 Office 外接程序。
您可以创建包含文档模板（例如零用金报销单）的 SharePoint 外接程序。该文档可以包含与 SharePoint 数据进行交互的任务窗格外接程序。例如，用户可以通过使用 Business Connectivity Services (BCS) 中的数据来填充发票字段或者通过从 SharePoint 列表中选择费用类别来创建零用金报销单。




此演练向您演示如何创建包含 Excel 工作簿的 SharePoint 外接程序。该 Excel 工作簿包含任务窗格外接程序，该外接程序使用 SharePoint 2013 提供的 REST 界面，将任务窗格外接程序中的 SharePoint 数据填充到下拉列表框中。
## 先决条件
<a name="Prerequisites"> </a>

在开始之前安装以下组件：








- SharePoint 开发环境：

  - 若要在 Office 365 中开发面向 SharePoint 的 SharePoint 外接程序，请参阅 [如何：在 Office 365 上设置 SharePoint 外接程序的开发环境](http://msdn.microsoft.com/zh-cn/library/office/apps/fp161179%28v=office.15%29)。


  - 若要开发面向 SharePoint 的本地安装的 SharePoint 外接程序，请参阅 [如何：设置 SharePoint 外接程序的本地开发环境](http://msdn.microsoft.com/zh-cn/library/office/apps/fp179923%28v=office.15%29)。


-  [Visual Studio 2015 和 Microsoft Office 开发人员工具](https://www.visualstudio.com/features/office-tools-vs)


- Excel 2013 或 Office 365 帐户。



## 在 Visual Studio 中创建 SharePoint 外接程序项目
<a name="Create"> </a>


1. 启动 Visual Studio。


2. 在菜单栏上，依次选择"文件"、"新建"、"项目"。

    将打开"新建项目"对话框。


3. 在模板窗格中要使用的语言节点下，展开"Office/SharePoint"，然后选择"Office 外接程序"。


4. 在项目类型列表中，选择"SharePoint 外接程序"，命名项目 OfficeEnabledAddin，然后选择"确定"按钮。

    将显示"新建 SharePoint 外接程序"对话框。


5. 在"您要使用哪个 SharePoint 网站调试外接程序?"下拉列表中，选择或输入 SharePoint 网站的 URL。


6. 在"希望如何托管 SharePoint 外接程序?"下拉列表中，选择"Sharepoint 托管"，然后选择"下一步"。

    > **注释**
      > 此方案仅适用于"希望如何托管 SharePoint 外接程序?"下拉列表中显示的 SharePoint 托管和提供程序托管选项。 
7. 在下一页上，选择"SharePoint 2013"，然后选择"完成"按钮以关闭对话框。



## 添加任务窗格加载项项
<a name="Add"> </a>

接下来，将 Office 外接程序添加到项目中。可以添加所需的任何类型的外接程序。在此演练中，我们将添加任务窗格外接程序。




1. 在"解决方案资源管理器"中，选择"OfficeEnabledAddin"项目节点。


2. 在"项目"菜单上，选择"添加新项"。


3. 在"添加新项"对话框中，选择"Office/SharePoint"，然后选择"Office 外接程序"。


4. 将任务窗格外接程序命名为 MyTaskPaneAddin，然后选择"添加"按钮。

    将打开"创建 Office 加载项"对话框。


5. 在"创建 Office 外接程序"对话框中，选择"任务窗格"，然后选择"下一步"。在下一页上，清除"Word"和"PowerPoint"复选框，然后选择"下一步"。


6. 在"希望 Office 外接程序出现在新文档还是现有文档中?"页中，选择"创建新文档并插入我的外接程序"，然后选择"完成"。

    Visual Studio 将添加文档库并为该库添加工作簿模板。该工作簿包含任务窗格外接程序。



## 添加文档库
<a name="Library"> </a>

在此过程中，您将添加文档库并使工作簿成为文档库的默认模板。




1. 在"解决方案资源管理器"中，选择"OfficeEnabledAddin"项目节点。


2. 在"项目"菜单上，选择"添加新项"。


3. 在"添加新项"对话框中，选择"Office/SharePoint"，然后选择"列表"，将列表命名为 MyDocumentLibrary，然后选择"添加"按钮。


4. 在"SharePoint 自定义向导"中，选择"创建可自定义列表模板及其列表实例"选项。


5. 此选项下方的下拉列表中，选择"文档库"，然后选择"下一步"按钮。


6. 在"为此文档库选择模板。用户在此库中创建的文档将基于该模板"中，选择"将以下文档用作此库的模板"，然后选择"浏览"按钮。


7. 在"打开"对话框中，打开"OfficeDocuments"文件夹，选择"MyTaskPaneApp.xlsx"文件，依次选择"打开"、"完成"按钮，然后关闭列表设计器。


8. 在"解决方案资源管理器"中，选择"OfficeEnabledAddin"项目节点。


9. 在"视图"菜单上，选择"属性窗口"。


10. 在"解决方案资源管理器"中，选择"AppManifest.xml"文件。


11. 选择"视图"、"设计器"。


12. 在清单设计器中，将"主页"的值设置为 ~appWebUrl/Lists/MyDocumentLibrary。这将转换为 OfficeEnabledAddin/Lists/MyDocumentLibrary 值。

    > **注释**
      > 此 URL 引用了文档库。您必须在引用应用程序 Web 中的项目的 Office 外接程序清单的任何 URL 的开头使用 ~appWebUrl 标记。有关 SharePoint 外接程序项目中 URL 令牌的详细信息，请参阅  [SharePoint 外接程序中的 URL 字符串和标记](url-strings-and-tokens-in-sharepoint-add-ins.md)。 
13. 关闭清单设计器，保存更改。



## 在任务窗格中使用 SharePoint 数据
<a name="Consume"> </a>

在此过程中，将使用 SharePoint 2013 提供的 REST 界面显示网站用户列表。



此示例中仅显示 SharePoint 列表数据，但您可以使用此类数据作为文档审批加载项的一部分。用户选择列表中的名称后，代码将设置文档跟踪列表中审阅者列的值。与该列关联的工作流可向该用户发送审阅通知。或者，您也可以将选中的名称保存到文档设置。然后在用户打开文档时，在任务窗格加载项中显示控件（仅在当前用户和文档设置中存储的用户相同时才可实现）。有关详细信息，请参阅以下主题：




-  [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [保留加载项状态和设置](http://msdn.microsoft.com/library/407df6e8-c237-4d6a-b357-3000fe3de9ff%28Office.15%29.aspx)



1. 在"解决方案资源管理器"中展开"MyTaskPaneAddin"文件夹，展开"主页"文件夹，然后选择"Home.html"文件。

    Home.html 文件将在代码编辑器中打开。


2. 在  `get-data-from-selection` 按钮下方添加以下 HTML。

  ```HTML

<p>Select Reviewer:</p> <select class="select" id="select-reviewer" name="D1"> </select>
  ```

3. 选择"Home.js"文件，以在代码编辑器中打开 Home.js 文件。


4. 将以下声明添加到 Home.js 文件顶部。

  ```
  var appWebURL; var web;
  ```

5. 将  `Initialize` 函数替换为以下代码。

    此代码执行下列任务：

  - 通过在 jQuery 中使用  `getScript` 函数加载 SP.Runtime.js 和 SP.js 文件。加载这些文件后，您的程序便具有对 SharePoint JavaScript 对象模型的访问权。


  - 加载当前网站对象。


  - 调用一个可获取网站所有用户的函数。在下一步骤中将为该函数添加代码。





  ```
   // The initialize function must be run each time a new page is loaded Office.initialize = function (reason) { $(document).ready(function () { app.initialize(); var scriptbase = "/_layouts/15/"; $.getScript(scriptbase + "SP.Runtime.js", function () { $.getScript(scriptbase + "SP.js", function () { getAppWeb(function () { getSPUsers(populateUsersDropDown); }); }); }); function getAppWeb(functionToExecuteOnReady) { var context = SP.ClientContext.get_current(); web = context.get_web(); context.load(web); context.executeQueryAsync(onSuccess, onFailure); function onSuccess() { appWebURL = web.get_url(); functionToExecuteOnReady(); } function onFailure(sender, args) { app.initialize(); app.showNotification("Failed to connect to SharePoint. Error: " + args.get_message()); } } $('#get-data-from-selection').click(getDataFromSelection); }); };
  ```

6. 将以下代码添加到 Home.js 文件底部。

    此代码通过使用 SharePoint 2013 提供的 REST 界面包含网站用户列表。然后，此代码会在下拉列表中填充每个用户的姓名和 ID。



  ```
  function getSPUsers(functionToExecuteOnReady) { var url = appWebURL + "/../_api/web/siteUsers"; jQuery.ajax({ url: url, type: "GET", headers: { "ACCEPT": "application/json;odata=verbose" }, success: onSuccess, error: onFailure }); function onSuccess(data) { var results = data.d.results; functionToExecuteOnReady(results); } function onFailure(jaXHR, textStatus, errorThrown) { var error = textStatus + " " + errorThrown; app.showNotification(error); } } function populateUsersDropDown(results) { for (var i = 0; i < results.length; i++) { var IDTemp = results[i].Id; $('#select-reviewer').append("<option value='" + IDTemp + "'>" + results[i].Title + "</option>"); } }
  ```

7. 在"解决方案资源管理器"中，打开"AppManifest.xml"文件的快捷菜单，然后选择"查看设计器"。


8. 在设计器上，选择"权限"页面。


9. 从"范围"列下的下拉列表中，选择"Web"项。


10. 从"权限"列下的下拉列表中，选择"读取"项。



## 调试任务窗格加载项
<a name="debug"> </a>

您可以调试任务窗格外接程序，方法为启动文档或启动 SharePoint 外接程序并打开文档库中的文档。




### 通过启动文档调试任务窗格加载项






> **注释**
> 由于此过程将打开 Excel，因此仅在系统中安装了 Office 时才正常运行。否则将收到一个错误"计算机上未安装与此项目类型关联的应用程序"。 





1. 在代码编辑器中打开 Home.js 文件，然后在  `getDataFromSelection` 方法旁边设置断点。


2. 在"解决方案资源管理器"中，选择"OfficeEnabledApp"项目节点。


3. 在"视图"菜单上，选择"属性窗口"。


4. 在"属性"窗口中，从"启动操作"下拉列表中选择"Office 桌面客户端"项，此时会显示一个新属性"启动文档"。


5. 从"启动文档"下拉列表中选择"OfficeDocuments\\TaskPaneApp.xlsx"项。


6. 在"调试"菜单中，选择"开始调试"。

    此设置将在应用程序运行时显示任务窗格加载项中的工作簿。工作簿将打开，并出现任务窗格加载项。


7. 在任务窗格加载项中，选择"选择审阅者"下拉列表，以查看 SharePoint 用户列表。


8. 在 Excel 工作簿中，选择任意单元格。


9. 在任务窗格加载项中，选择"从选区获取数据"按钮。

    执行将于您在  `getDataFromSelection` 方法旁设置的断点处停止。



### 通过启动 SharePoint 调试任务窗格加载项






> **注释**
> 此过程将打开 Excel Online。它仅在您拥有 Office 365 帐户时正常运行。请参阅 [如何：在 Office 365 上设置 SharePoint 加载项的开发环境](http://msdn.microsoft.com/zh-cn/library/office/apps/fp161179%28v=office.15%29)。 





1. 在代码编辑器中打开 Home.js 文件，然后在  `getDataFromSelection` 方法旁边设置断点。


2. 在"解决方案资源管理器"中，选择"OfficeEnabledApp"项目节点。


3. 在"视图"菜单上，选择"属性窗口"。


4. 在"属性"窗口中，从"启动操作"下拉列表中选择"Internet Explorer"项。


5. 在"调试"菜单上，选择"开始调试"。

    Visual Studio 将打开 SharePoint 并显示"MyDocumentLibrary"库。


6. 在 SharePoint 中的"文件"选项卡上，选择"新建文档"。


7. 导航到项目中的工作簿 MyTaskPaneApp.xlsx。

    工作簿将打开，并出现任务窗格加载项。


8. 确保在浏览器中启用脚本调试。在 Internet Explorer 中，您可以通过以下方式启用脚本调试：打开"Internet 选项"对话框，选择"高级"选项卡，然后清除"禁用脚本调试(Internet Explorer)"和"禁用脚本调试(其他)"复选框。


9. 在 Visual Studio 中，在"调试"菜单上选择"附加到进程"。


10. 在"附加到进程"对话框中，选择所有可用的"iexplore.exe"进程，然后选择"附加"按钮。


11. 在任务窗格加载项中，选择"选择审阅者"下拉列表，以查看 SharePoint 用户列表。

    使用 REST 调用从 SharePoint 检索列表中的数据。


12. 在 Excel 工作簿中，选择任意单元格。


13. 在任务窗格加载项中，选择"从选区获取数据"按钮。

    执行将于您在  `getDataFromSelection` 方法旁设置的断点处停止。

    > **注释**
      > 如果工作簿不包含任何数据，可以依次选择工作簿中工具栏上的"编辑工作簿"、"在 Excel Online 中编辑"添加数据。 

## 打包和发布加载项
<a name="Package"> </a>

准备好打包要发布的外接程序后，请打开"发布 Office 和 SharePoint 外接程序"向导。




- 在"解决方案资源管理器"中，打开SharePoint 外接程序项目的快捷菜单，然后选择"发布"。

    将出现"发布 Office 和 SharePoint 外接程序"向导。有关详细信息，请参阅 [使用 Visual Studio 发布 SharePoint 外接程序](publish-sharepoint-add-ins-by-using-visual-studio.md)。



## 其他资源
<a name="AdditionalResources"> </a>


-  [Office 2013 的任务窗格和内容加载项](http://msdn.microsoft.com/library/baf73b23-4429-4b7f-bcb9-a99a9618ae38%28Office.15%29.aspx)


-  [Office 外接程序的设计准则](http://msdn.microsoft.com/library/d5b2ab2e-dfc8-47c8-919c-e9c23358d70c%28Office.15%29.aspx)


-  [Office 外接程序开发生命周期](http://msdn.microsoft.com/library/c35b4b2b-7869-4501-9f10-888c8e74c98c%28Office.15%29.aspx)


-  [发布 Office 外接程序](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)


-  [了解 适用于 Office 的 JavaScript API](http://msdn.microsoft.com/library/01180dae-ca45-40c8-b3dd-fd2a85651c0c%28Office.15%29.aspx)


-  [Office 外接程序 XML 清单](http://msdn.microsoft.com/library/4139ff24-afac-472a-af7d-9d069587ac9b%28Office.15%29.aspx)


-  [Office 外接程序 API 和架构参考](http://msdn.microsoft.com/library/afcc2908-1e1b-4297-b554-11e6eb404804%28Office.15%29.aspx)



