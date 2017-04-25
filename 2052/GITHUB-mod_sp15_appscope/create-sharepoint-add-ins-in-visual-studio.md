---
title: 在 Visual Studio 中创建 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: f1b9c858-907c-4558-b671-3b488ece40a0
---



# 在 Visual Studio 中创建 SharePoint 外接程序
学习使用 Visual Studio 中的项目和项目项的模板开发 SharePoint 外接程序。
您可以使用"vsnv"中的项目和项目项的新模板开发 SharePoint 外接程序。





## 项目模板
<a name="SP15Projecttemplates_templates"> </a>

当使用 Visual Studio 中的项目模板时，会创建一个包含项目类型所要求的项目项和文件的解决方案。如果展开"Office/SharePoint"节点，然后选择"加载项"节点，以下项目模板将显示在"新项目"对话框中。有关"SharePoint 解决方案"节点下的项目模板的信息，请参阅  [SharePoint 项目和项目项模板](http://go.microsoft.com/fwlink/?LinkId=255306)。




### Office 加载项

创建在 Office 应用程序（如 Excel 或 Outlook）内部承载的网页。Office 外接程序在文档或 Outlook 项目中提供了额外的内容和功能。有关详细信息，请参阅  [Office 加载项平台概述](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx)。




### SharePoint 加载项

基于您在向导中指定的信息创建一个 SharePoint 外接程序。此信息包括以下数据。




- 加载项的名称。


- 要用于调试您的加载项的本地或远程 SharePoint 网站。


- 您要创建的加载项的类型：提供商托管或 SharePoint 托管。


有关详细信息，请参阅  [SharePoint 外接程序](sharepoint-add-ins.md)。




### 云业务加载项

通过使用 Visual Studio 中的"云业务加载项"模板，您可以创建一个 SharePoint 承载的加载项，通过该应用程序，移动用户可以使用现代触屏设备（如手机和平板电脑）从远程位置查看、添加和更新数据。有关详细信息，请参阅 [创建云企业外接程序](create-cloud-business-add-ins.md)。




## 项目项模板
<a name="SP15Projecttemplates_items"> </a>

在创建 SharePoint 解决方案后，您可以使用以下模板（显示在"Office/SharePoint"节点下的"添加新项"对话框中）向其中添加项目项。




### Office 加载项

将 Office 外接程序添加到您的 SharePoint 外接程序中。您可以添加任务窗格加载项、内容加载项或邮件加载项。有关详细信息，请参阅  [Office 加载项平台概述](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx)。




### 客户端 Web 部件（主机 Web）

将客户端 Web 部件添加到您的 SharePoint 外接程序中。通过添加客户端 Web 部件，您可以在宿主网站页面上显示加载项。此模板包含一个 Elements.xml 文件，其属性定义客户端 Web 部件的下列元素。





|**属性名称**|**说明**|
|:-----|:-----|
|ClientWebPart  <br/> |指定客户端 Web 部件的名称、标题、描述和尺寸。  <br/> |
|内容  <br/> |定义在客户端 Web 部件内呈现的网页的位置。此元素有两个属性： `Type` 和 `Src`。 `Type` 指定要创建的 Web 部件的类型，如 HTML。 `Src` 定义将在客户端 Web 部件内呈现的网页的位置。该模板通过使用模式 _ _PropertyName__（例如  `Src="~addinWebUrl/Pages/ClientWebPart1.aspx?Property1=_property1_"`）来引用查询字符串上的属性  <br/> 有关详细信息，请参阅 [创建外接程序部件以安装 SharePoint 外接程序](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)。  <br/> |
 

### 内容类型

将一个内容类型添加到您的 SharePoint 外接程序，类似于在 SharePoint 早期版本中使用的内容类型。一个内容类型是 SharePoint 列表或库中一个项目类别的一组元数据、工作流和行为。例如，一个项是一种列表内容类型。其他列表内容类型包括公告、联系人和任务，而且它们从项目内容类型继承。联系人内容类型包含栏，如"名字"、"姓氏"及"职位"。



当您将内容类型添加到您的 SharePoint 外接程序中时，可指定新内容类型从其继承的基内容类型。例如，新内容类型可以继承自公告、联系人、文档或项目内容类型。然后，可使用"内容类型"设计器来配置内容类型的栏及它的其他属性，如名称和描述。您选择的值将添加到 Elements.xml 文件中的  `ContentType` 和 `FieldRef` 元素中。有关详细信息，请参阅 [构建基块：内容类型](http://msdn.microsoft.com/library/277dfc42-d9a8-4fae-9ae1-0d202b14674f%28Office.15%29.aspx)。




### 空元素

将空元素的一个项目项添加到您的 SharePoint 外接程序。此项目项包含单个文件 Elements.xml，您在其中定义元素的属性。您通常使用空元素来定义 Visual Studio 不为其提供模板的项。




### 列表

将两个项目项添加到您的 SharePoint 外接程序：一个列表定义和一个列表实例。将列表添加到您的加载项时，您指定如何命名列表以及是创建一个空白列表还是基于现有列表类型的列表。您还指定是否可自定义列表。然后，您使用"列表设计器"来配置列表的列和视图以及其他属性，如列表的名称和说明。有关列表属性的详细信息，请参阅  [ListTemplate 元素（列表模板）](http://msdn.microsoft.com/library/e565ead9-adcb-4a90-97e3-04850719420a%28Office.15%29.aspx)和  [ListInstance 元素（列表实例）](http://msdn.microsoft.com/library/cfefe8e5-2656-4d71-bb4e-5f991a800598%28Office.15%29.aspx)。




### 菜单项自定义操作

添加项目项，以便通过将操作添加到列表菜单中来扩展其宿主网站的 UI。菜单自定义操作包含一个 Elements.xml 文件，可使用它来定义操作的属性。有关详细信息，请参阅 [创建自定义操作以部署 SharePoint 外接程序](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)。




### 模块

将一个模块项目项添加到您的 SharePoint 外接程序。模块基本上是容器，您可以在部署您的 SharePoint 外接程序时用它来包含其他文件。若要添加一个文件，将其复制到"解决方案资源管理器"中的模块下的项目中。对该文件的引用将自动添加到模块的 Elements.xml 文件中，并且该引用指定新文件的路径和 URL。您可以删除模块附带的 Sample.txt 文件，因为它仅用作示例。




### 远程事件接收器

将远程事件接收器的项目项添加到您的 SharePoint 外接程序中并将 Web 应用程序项目添加到您的解决方案中（如果尚不存在这样的项目）。Web 应用程序包含与您的 SharePoint 外接程序中的远程事件接收器相关联的 Web 服务。该 Web 服务包含 Visual Basic 或 Visual C# 代码文件，当 SharePoint 外接程序中发生列表、列表项或 Web 项事件时会执行其代码。如果存在 Web 应用程序，则它与 SharePoint 外接程序相关联，并将 Web 服务添加到该应用程序中。有关详细信息，请参阅 [处理 SharePoint 外接程序中的事件](handle-events-in-sharepoint-add-ins.md)。




### 功能区自定义操作

添加项目项，以便通过将操作添加到功能区中来扩展其宿主网站的 UI。功能区自定义操作包含一个可定义操作属性的 Elements.xml 文件。有关详细信息，请参阅 [创建自定义操作以部署 SharePoint 外接程序](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)。




### 搜索配置

添加一个项目项，使您可以导入从某个 SharePoint 网站导出的自定义搜索配置设置。




### 网站栏

将网站栏的一个项目项添加到您的 SharePoint 外接程序。该网站栏包含一个 Elements.xml 文件，它定义该网站栏的  `Field` 属性，包括以下数据。





|**属性名称**|**说明**|
|:-----|:-----|
|ID  <br/> |该网站栏的唯一 GUID 值。  <br/> |
|Name  <br/> |用于引用该网站栏的唯一名称。  <br/> |
|DisplayName  <br/> |在 UI 中显示的友好名称。  <br/> |
|Type  <br/> |基于 **SPFieldType** 的网站栏的数据类型，如布尔、查找或文本。 <br/> |
|Required  <br/> |如果栏是必需的，该属性将设置为 **True**；否则，该属性将设置为 **False**。  <br/> |
|Group  <br/> |指定网站栏所分配到的组的名称。此属性的默认值是"自定义网站栏"。  <br/> |
 
有关详细信息，请参阅 [构造块：列和字段类型](http://msdn.microsoft.com/library/58548ade-e439-4931-82a2-fa29bd5afdb7%28Office.15%29.aspx)。




### 工作流

将一个 Microsoft Azure 工作流的项目项添加到您的 SharePoint 外接程序。有关详细信息，请参阅  [SharePoint 2013 中的工作流](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx)。当您添加此类型的项时，指定工作流的名称以及它是列表工作流还是网站工作流。顾名思义，列表工作流仅适用于列表，网站工作流仅适用于 SharePoint 网站。当您创建工作流时，还指定是否自动使该工作流与列表和库相关联，如果关联，还要指定与哪些相关联。对于添加的每个关联，针对它的文件都会添加到工作流项目。工作流包含下列文件。





|**文件名称**|**说明**|
|:-----|:-----|
|Elements.xml  <br/> |指定工作流的配置及其包含的文件，例如 workflow.xaml 文件和关联文件以及每个文件的属性，如其 URL、类型和路径。对于添加到工作流项目的每个文件，会将一个相应的节添加到工作流的 Elements.xml 文件。列表工作流中的关联文件要求一个列表，所以它们有对列表令牌的引用。在网站工作流中，为该网站添加了一个 GUID。  <br/> > **警告**> 由于 Visual Studio 维护 Elements.xml 文件中的项，我们建议不要更改它，除非您熟悉所做更改的影响。           |
|Workflow.xaml  <br/> |表示工作流的设计器。在此文件中，您将操作添加到工作流并设置其代码和属性。  <br/> |
|WorkflowStartAssociation  <br/> |在 SharePoint 上手动启动工作流。如果您在工作流向导中选中"用户手动启动工作流"复选框，此文件将添加到工作流项目。  <br/> |
|ItemAddedAssociation  <br/> |如果用户在网站或列表（根据工作流类型）中创建项时存在一个工作流，将自动启动该工作流。如果您在工作流向导中选中"添加项时自动启动工作流"复选框，此文件将添加到工作流项目。  <br/> |
|ItemUpdatedAssociation  <br/> |如果用户在网站或列表（根据工作流类型）中更改项时存在一个工作流，将自动启动该工作流。如果您在工作流向导中选中"更改项时自动启动工作流"复选框，此文件将添加到工作流项目。  <br/> |
|WorkflowHistoryList  <br/> |表示添加到工作流项目中的文件（如果您在工作流向导中创建工作流的历史记录列表）。  <br/> |
|WorkflowTaskList  <br/> |表示添加到工作流项目中的文件（如果您在工作流向导中创建工作流的任务列表）。  <br/> |
 

### 工作流自定义活动

将工作流自定义活动的项目项添加到您的 SharePoint 外接程序中。通过添加工作流自定义活动，您可以创建以后可作为 SharePoint Designer 2013 中的自定义操作导入的其他工作流操作。工作流自定义活动包含 Elements.xml 文件（用于定义操作的属性）和工作流设计器的 .xaml 文件。有关详细信息，请参阅 [SharePoint 2013 中的工作流](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx)。




## 其他资源
<a name="SP15Projecttemplates_addlresources"> </a>


-  [SharePoint 外接程序开发工具和环境](tools-and-environments-for-developing-sharepoint-add-ins.md)


