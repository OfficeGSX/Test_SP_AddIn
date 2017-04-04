---
title: 使用 Visual Studio 发布 SharePoint 外接程序
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# 使用 Visual Studio 发布 SharePoint 外接程序
了解如何使用 Microsoft Visual Studio 2013 或 Visual Studio 2012 发布 SharePoint 外接程序。如果外接程序具有关联的 Web 应用程序，则首先部署该外接程序。然后，对于所有 SharePoint 外接程序，您将打包 SharePoint 外接程序并发布它。您也可以选择提交您的外接程序，以便将它包括在 Office 商店中。
## 先决条件
<a name="Prereq"> </a>


  
    
    

-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284)
    
    -或者-
    
     [Visual Studio 2012](https://www.microsoft.com/zh-cn/download/details.aspx?id=30682) 和 Visual Studio Office 开发人员工具。若要下载这些工具，请参阅 [下载页](http://go.microsoft.com/fwlink/?LinkId=234393)的"工具"。（Visual Studio 2012 或更早版本中不提供新的发布管理器。）
    
  
- Microsoft SharePoint 2013
    
  

## 由 Visual Studio 2013 发布
<a name="VS2013"> </a>

如果您的提供程序承载的 SharePoint 外接程序具有 Web 应用程序，则首先为其部署文件。然后针对所有 SharePoint 外接程序，打包 SharePoint 外接程序并发布。
  
    
    

> [!重要信息]
> 为了确保您的 SharePoint 客户端 ID 和客户端密码值与您的 Web 项目一起发布（这允许您的 Web 内容访问 SharePoint 数据），请从"发布外接程序"页发布您的 SharePoint 外接程序项目。 若要访问该页，则首先打开 SharePoint 外接程序快捷菜单（而非 Web 应用的快捷菜单），然后选择"发布"命令。 
  
    
    


### 步骤 1：部署 Web 应用程序

您的 SharePoint 外接程序通常有一个必须部署至 Web 服务器的关联主机 Web 应用程序。有关如何使用"发布 Web"向导的详细信息，请参阅 [如何：在 Visual Studio 中使用"一键式发布"部署 Web 项目](http://msdn.microsoft.com/library/dd465337.aspx)。
  
    
    

### 打开"发布外接程序"页面


- 在"解决方案资源管理器"中，打开 SharePoint 外接程序项目的快捷菜单，然后选择"发布"。
    
    将显示"发布外接程序"页面。
    
  

### 选择或创建一个配置文件


- 在"当前配置文件"列表中，选择要导入的配置文件，或选择"<新建 …>"以创建配置文件。
    
    发布配置文件将指定 Web 应用程序要部署到的服务器、登录该服务器所需的凭据、要部署的数据库（如果适用）和其他部署选项。您可以创建不同的发布配置文件以满足需要。例如，您可以为测试创建一个配置文件，而为发布创建另一个配置文件。
    
    如果您选择"<新建 …>"，将显示"创建发布配置文件"向导。您可以使用该向导从网站宿主提供程序（如 Azure）中导入发布配置文件，或创建配置文件，然后手动添加服务器名称、凭据和其他设置。如果您将创建新的配置文件，而不是导入现有配置文件，您将需要提供客户端 ID 和客户端密码值，如 [注册 SharePoint 2013 外接程序指南](http://msdn.microsoft.com/library/jj687469.aspx)和 [如何：在 Microsoft 卖家面板中创建客户端 ID 和密码](http://msdn.microsoft.com/library/office/jj220036.aspx)中所概述。
    
    如果您计划将您的 SharePoint 外接程序提交到 Office 商店，请务必使用在卖家面板中创建的客户端 ID 和客户端密码值。您可以使用在开发阶段使用 appregnew.aspx 页面生成的客户端 ID 和客户端密码值，但您提交到 Office 应用商店的外接程序必须使用您从卖家面板获取的客户端 ID 和客户端密码。此外，您还应该在 Azure 网站上创建发布配置文件，然后将其导入到 Visual Studio，而不是在"创建发布配置文件"向导中创建配置文件。在 Azure 中创建配置文件时，"连接"选项卡上的所有设置都将在 Visual Studio 中提供给您。有关如何导入或创建发布配置文件的详细信息，请参阅 [创建发布配置文件](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile)。
    
    > [!提示]
      > 如果您无法直接发布 Web 内容，您可以创建 Web 部署包，并让管理员为您部署到 Web。要创建 Web 部署包，则创建新的配置文件，选择"连接"选项卡，然后在"发布方法"列表中选择"Web 部署包"。 

### 部署 Web 应用程序项目


1. 在"发布外接程序"页上，选择"部署 Web 项目"按钮。
    
    显示"发布 Web"对话框。
    
  
2. 在"连接"和"设置"选项卡上，填写任何缺失的值。
    
    要更改 SharePoint 外接程序文件的发布方式，或者更改外接程序是否使用外部数据库，请选择"设置"选项卡。请参阅 [如何：在 Visual Studio 中使用"一键式发布"部署 Web 项目](http://msdn.microsoft.com/library/dd465337.aspx)中的"配置设置选项卡"一节。
    
  
3. 要查看部署 Web 应用程序后，什么项目将发生改变，则选择"预览"选项卡上的"开始预览" 按钮。
    
  
4. 选择"发布"按钮部署 Web 应用程序项目。
    
  

### 步骤 2：打包外接程序


1. 在"发布外接程序"页上，选择"打包外接程序"按钮。
    
    将出现"发布 Office 和 SharePoint 外接程序"向导。
    
  
2. 在"您的网站在什么位置承载？"文本框中，输入将承载 SharePoint 外接程序内容文件的网站的 URL。
    
    您必须指定以"https"前缀开始的地址。请参阅 [为什么我的外接程序必须采用 SSL 保护？](http://msdn.microsoft.com/library/jj591603#bk_q7)。
    
    > [!注释]
      > Azure 网站将自动提供 https 终结点。如果您在 Office 商店网站上发布外接程序或将外接程序发布到 Office 商店，地址必须以 https 前缀开头。但是，如果您将外接程序发布到本地网站，您可以使用 http 前缀。 

    在"外接程序的客户端 ID 是什么？"文本框中，应已显示您在发布配置文件中输入的客户端 ID。
    
    如果至此您一直使用占位符值代替客户端 ID，则现在您必须添加实际的客户端 ID。该信息嵌入在 .app 程序包中，它使您的 Web 内容可以在实时网站上与 SharePoint 通信。
    
  
3. 选择"完成"按钮。
    
    Visual Studio 将生成发布 SharePoint 外接程序所需的文件，然后打开发布输出文件夹。有关如何安装该外接程序的信息，请参阅 [安装和管理 SharePoint 2013 外接程序](http://technet.microsoft.com/library/fp161232.aspx)。
    
  

### 步骤三：在 Office 商店上发布 SharePoint 外接程序

如果想将 SharePoint 外接程序提交到 Office 商店，请执行以下过程。
  
    
    

1. 在"发布外接程序"页，选择"访问卖家面板"按钮，然后登录您的 Microsoft 卖家面板帐户。
    
    请参阅 [将 Office 与 SharePoint 外接程序和 Office 365 Web 应用提交到 Office 应用商店](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx)。
    
  
2. 选择"添加新应用"，填写信息，然后将外接程序提交到 Office 商店。有关详细信息，请参阅 [使用"卖家面板"将 Office 和 SharePoint 外接程序和 Office 365 应用提交到 Office 应用商店](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)。
    
  

## 使用 Visual Studio 2012 发布
<a name="VS2012"> </a>

当您准备好打包 SharePoint 外接程序时，请打开"发布 Office 外接程序"向导，它将准备 SharePoint 外接程序中要发布的文件。
  
    
    

### 步骤 1：打包 SharePoint 外接程序


1. 在"解决方案资源管理器"中，打开 SharePoint 外接程序项目的快捷菜单，然后选择"发布"。
    
    将出现"发布 Office 外接程序"向导。您要打包的 SharePoint 外接程序的类型确定向导中出现的页。如果您的外接程序将是 SharePoint 托管的，则将仅出现"摘要"页。如果您的外接程序将是提供程序托管的，则还将出现"配置文件"页和"宿主"页。
    
  
2. 如果您的 SharePoint 外接程序是提供程序承载的，则请在"希望发布哪个配置文件?"列表中指定发布配置文件名称，然后选择"下一步"按钮。
    
    发布配置文件将保存您在"宿主"页中输入的信息。
    
  
3. 在"您的网站在什么位置承载?"列表中，指定将承载您的 SharePoint 外接程序的 Web 应用程序的 URL。
    
  
4. 在"什么是外接程序的标识?"下的框中，指定您的外接程序的客户端 ID 和客户端密码，然后选择"下一步"按钮。
    
    请参阅  [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)。
    
  
5. 对于所有类型的 SharePoint 外接程序，请选中"成功打包后打开输出文件夹"复选框（如果尚未选中），然后选择"完成"按钮。
    
    Visual Studio 将生成您发布 SharePoint 外接程序所需的全部文件。您可在项目输出文件夹的  `app.Publish` 文件夹（例如， `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`）中找到这些文件。该文件夹将包含一个 SharePoint 外接程序 .app 文件和多个 Web 应用程序文件（如果您的 SharePoint 外接程序是云托管的）。所有 SharePoint 外接程序将包含一个 .app 文件，该文件是用于发布 SharePoint 外接程序的外接程序清单文件。提供程序托管的 SharePoint 外接程序还包含用于发布主机 Web 应用程序的文件。
    
  

### 步骤二：发布 Web 应用程序

如果 SharePoint 外接程序是提供程序承载的，则您通常将有一个必须发布到 Web 服务器上的关联主机 Web 应用程序。Visual Studio 将生成一个部署包和一个脚本帮助您执行此任务。
  
    
    
Web 应用程序项目的部署包包含在  `app.publish` 文件夹的压缩 (.zip) 文件中。除了此 .zip 文件， `app.publish` 文件夹还包含下列文件：
  
    
    


|**文件**|**说明**|
|:-----|:-----|
| _ProjectName_.deploy.cmd  <br/> |此文件是调用 Web Deploy 以便您可轻松在命令提示符处安装包的命令行批处理文件。  <br/> |
| _ProjectName_.SetParameters.xml  <br/> |此文件包含您使用 deploy.cmd 文件安装包时将传递到 Web Deploy 的参数。Visual Studio 程序包设置确定为每个参数指定的默认值。您可更改这些值，例如，您要将 Web 应用程序安装到多台服务器并对每台服务器使用不同设置时。  <br/> |
| _ProjectName_.SourceManifest.xml  <br/> |此文件包含 Visual Studio 传递到 Web 部署的设置和 Web 部署用于创建 Web 包的设置。Web 部署只需此文件就能创建包。安装包时不会使用此文件。  <br/> |
   
有关分步指南，请参阅 [如何：使用 Visual Studio 创建的 deploy.cmd 文件安装部署包](http://go.microsoft.com/fwlink/?LinkID=254954)
  
    
    

### 步骤 3：发布 SharePoint 外接程序
<a name="Publish"> </a>

若要发布 SharePoint 外接程序，请将外接程序的外接程序清单文件 (.app) 上载到 Office 商店、Office 外接程序目录、SharePoint、文件共享或 Exchange 目录。外接程序的外接程序清单位于  `app.publish` 文件夹，如 `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`。有关如何发布 SharePoint 外接程序的详细信息，请参阅 [SharePoint 外接程序的授权和身份验证](authorization-and-authentication-of-sharepoint-add-ins.md)。
  
    
    

## 其他资源
<a name="Additional"> </a>


-  [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)
    
  
-  [发布 Office 外接程序](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)
    
  

