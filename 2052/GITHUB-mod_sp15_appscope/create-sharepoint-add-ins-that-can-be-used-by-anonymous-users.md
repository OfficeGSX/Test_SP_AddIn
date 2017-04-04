---
title: 创建可供匿名用户使用的 SharePoint 外接程序
ms.prod: SHAREPOINT
ms.assetid: e858fce6-7a8f-4b1e-b053-64dc75345801
---


# 创建可供匿名用户使用的 SharePoint 外接程序
了解在面向公众的 Microsoft SharePoint 2013 网站上如何创建可供匿名用户使用的 SharePoint 外接程序。
> **重要信息**
> 在本文中，不论在哪里提到 *内部部署*  SharePoint 2013，我们都假定已安装 Service Pack 1 for SharePoint 2013。
  
    
    


## 创建可匿名访问的 SharePoint 外接程序的先决条件

SharePoint 承载和提供程序承载的 SharePoint 外接程序可匿名访问。根据您创建的类型，阅读下列先决条件集之一：
  
    
    

-  [](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#SP15SPhostedapps_bk_prereqs)
    
  
-  [](get-started-creating-provider-hosted-sharepoint-add-ins.md#SP15createselfhostapp_bk_prereq)
    
  
在配置用于匿名访问的测试 SharePoint 2013 安装中，您还需要一个网站集。如果您具有 Office 365 开发人员网站，可能已经有使用特殊公共网站定义的、与其相关的公共网站集。（有关在 Microsoft SharePoint Online 中使用公共网站的详细信息，请参阅  [Office 365 的公共网站帮助](http://office.microsoft.com/zh-cn/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1)。）该网站定义对内部部署 SharePoint 2013 安装不可用。因此，如果您的测试安装位于内部部署中，您将需要以下各项：
  
    
    

- 配置为允许匿名访问的 SharePoint Web 应用程序。但是此配置必须在安装完您当前测试的 SharePoint 外接程序 后执行。
    
  
- 本身配置为允许匿名访问的 Web 应用程序中的网站集。但是此配置必须在安装完您当前测试的 SharePoint 外接程序 后执行。
    
  
- 配置为允许匿名访问的网站集中的列表，如果您的外接程序是 SharePoint 托管的并可访问 SharePoint 列表。
    
  
有关执行这些任务的说明，请参阅 [配置可匿名访问的 SharePoint Web 应用程序和网站集与列表](#Configuring)一节。
  
    
    

## 对匿名用户使用 SharePoint 外接程序的限制

设计 SharePoint 外接程序时请考虑以下事实：
  
    
    

- 如果 SharePoint 外接程序从 SharePoint 启动，它必须包含自定义操作或外接程序部件，或者必须从页面上的自定义链接启动。这是因为匿名用户无法通过其磁贴启动外接程序。外接程序从磁贴启动时使用的机制要求使用匿名用户不可访问的特殊 [应用程序页面](http://msdn.microsoft.com/library/685c8e01-b163-4b5e-888c-421d9ecbb25e%28Office.15%29.aspx)。其他选项是使外接程序可从 SharePoint 外部启动，这种情况下必须使用仅外接程序授权策略。
    
  
- 匿名用户只能启动其他用户安装的 SharePoint 外接程序。这是因为匿名用户无法打开"添加外接程序"页面。
    
  
- 当登录用户导航到配置为可匿名访问的本地 SharePoint Web 应用程序中的网站时，用户的身份将更改为 **系统帐户** 。此身份无法安装 SharePoint 外接程序。由于匿名用户也无法安装外接程序，这意味着当 Web 应用程序配置为匿名访问时，没有人可以安装 SharePoint 外接程序。相应地，SharePoint 外接程序应在 Web 应用程序配置为匿名访问之前安装到 SharePoint Web 应用程序中的网站。如果稍后需要安装其他外接程序，必须临时更改 Web 应用程序以禁用匿名访问，以便登录用户可以安装外接程序。
    
  
- 要在本地 SharePoint 网站上启用 SharePoint Search REST API，您需要配置一些查询 XML。有关详细信息，请参阅 [启用匿名 Search REST 查询](http://msdn.microsoft.com/library/office/jj163876.aspx#bk_AnonymousREST)。
    
  
- 搜索索引器不会对外接程序 Web 上的数据进行爬网，因此自定义数据必须远程部署或在主机 Web 中部署。这适用于所有 SharePoint 外接程序，但我们在这里着重指出是因为用于匿名访问的外接程序通常需要搜索功能。
    
  

## 创建可匿名访问的提供程序托管的外接程序
<a name="Cloud-hosted"> </a>

使提供程序托管的外接程序可供匿名用户访问就是将其配置为使用仅外接程序授权策略。使用此策略时，SharePoint 甚至不会包括用户上下文，因此用户是否匿名并不重要。仅需由安装外接程序的租户管理员向外接程序主体授予对主机 Web 所需的任何权限，以及对父网站集或父租户所需的任何权限。您请求的对外接程序清单中的主机 Web 的权限与对于任何外接程序是相同的。
  
    
    

> **注释**
> 如果 SharePoint 网站可由匿名用户访问，通常允许 HTTP（而非 HTTPS）访问。当仅外接程序策略用于此方案中的外接程序时，存在潜在安全问题。有关消除风险的详细信息和方法，请参阅 [关于 SharePoint 外接程序、CSOM 和匿名发布网站每位开发人员需要知道的内容](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx)。 
  
    
    

将外接程序设计为使用仅外接程序策略需要满足两个条件：
  
    
    

- 您必须将  `AllowAppOnlyPolicy="true"` 添加到外接程序清单中的 **AppPermissionRequests** 元素。
    
  
- 您的代码必须从 OAuth 授权服务器询问仅外接程序访问令牌。如果您使用托管代码，将在 Visual Studio Microsoft Office 开发人员工具 生成的代码文件中提供专为此目的创建的 API：SharePointContext.cs（或 .vb）和 TokenHelper.cs（或 .vb）。
    
  
有关仅外接程序策略（含代码段）、外接程序权限和外接程序清单的详细信息，请参阅这些主题：
  
    
    

-  [SharePoint 2013 中的外接程序授权策略类型](add-in-authorization-policy-types-in-sharepoint-2013.md)
    
  
-  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [了解 SharePoint 外接程序的应用程序清单结构和包](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

## 创建可匿名访问的 SharePoint 托管的外接程序
<a name="SP-hosted"> </a>

创建可由匿名用户运行的 SharePoint 托管的外接程序不需要任何特殊技术。您可使用与创建任何 SharePoint 托管的外接程序相同的方式进行创建。有关详细信息，请参阅 [开始创建 SharePoint 承载的 SharePoint 外接程序](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md) 和 [使用 SharePoint 2013 中的 JavaScript 库代码完成基本操作](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)。
  
    
    
使 SharePoint 托管的外接程序可供匿名访问的真正关键在于，由租户管理员配置的 SharePoint Online 网站集允许匿名访问。SharePoint 托管的外接程序的开发人员无法从外接程序或远程外接程序事件接收器或者在 SharePoint 外接程序安装基础结构中进行此配置。您可以在外接程序中包括自定义列表或库，但 协作应用程序标记语言 (CAML) 不提供任何预先配置匿名访问的方法，因此必须在安装外接程序之后进行配置。
  
    
    
如果您通过 Office 商店推广您的外接程序，且您的大部分潜在客户都认为外接程序运行匿名用户访问时最有用，您应考虑在外接程序说明中添加此配置要求。另外还应考虑在外接程序支持页中添加下面的 **配置 SharePoint Online 网站集以允许匿名访问** 过程版本。
  
    
    
如果您的外接程序使用 SharePoint 的 JavaScript 对象模型 (JSOM)，那么有一项配置要求非常关键。默认情况下，只有一小部分 JSOM API 可供匿名用户访问。所有其他 API 均要求用户具有 **使用远程界面** 权限，匿名用户没有此权限。此要求在安装外接程序的网站的父网站集或父 SharePoint Web 应用程序中必须关闭，如 [配置可匿名访问的 SharePoint Web 应用程序和网站集与列表](#Configuring)中所述。
  
    
    

> **注释**
> 关闭用户必须具有 **使用远程界面** 权限的要求对信息隐私具有一定影响。有关详细信息，请参阅 [关于 SharePoint 外接程序、CSOM 和匿名发布网站每位开发人员需要知道哪些内容](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx)。 
  
    
    


## 对匿名用户使用 SharePoint 托管的外接程序的限制
<a name="SP-hosted"> </a>

我们必须对您坦诚地说，关于匿名用户对 SharePoint 托管的外接程序的使用，有一些您需要注意的重大限制：
  
    
    

- 无法使 SharePoint Online 上的列表或库可供匿名用户访问。相应地，涉及与列表或库交互的 JSOM API 在 SharePoint Online 上对匿名用户不可用。
    
  
- 安装到 *本地*  SharePoint 网站的 SharePoint 托管的外接程序可以使用所有 JSOM API。但是，Web 应用程序、网站集和列表/库必须 *手动*  配置为运行匿名访问，且必须禁用 **使用远程界面** 权限要求。如果外接程序将自定义列表/库安装到外接程序 Web，用户需负责手动配置这些列表/库，使其在外接程序安装后可匿名访问。
    
  
- 由于搜索索引器不会对外接程序 Web 上的数据进行爬网，无法将自定义列表或库安装到主机 Web，SharePoint 托管的外接程序不能具备远程组件或事件接收器，因此无法搜索 SharePoint 托管的外接程序中的自定义数据。
    
  

## 配置可匿名访问的 SharePoint Web 应用程序和网站集与列表
<a name="Configuring"> </a>

如果您的测试 SharePoint 安装位于内部部署中，您必须执行下面所述的前两个步骤，以测试您的 SharePoint 外接程序是否可供匿名用户访问。安装您的 SharePoint 外接程序的客户还必须对安装 SharePoint 外接程序的任何网站的父网站集和 Web 应用程序执行这些步骤。
  
    
    

> **重要信息**
> 如有必要，您应先将 SharePoint 外接程序安装到网站，然后再执行前两个步骤。当 Web 应用程序已配置为允许匿名访问时，外接程序无法安装到本地 SharePoint Web 应用程序中的任何网站。如果 Web 应用程序已配置为允许匿名访问，您必须临时还原该设置以安装外接程序。 
  
    
    


### 将父 Web 应用程序配置为允许匿名访问


1. 在"管理中心"中，选择"应用程序管理"下，然后选择"管理 Web 应用程序"。
    
  
2. （可选）如果您不希望对任何现有的 SharePoint Web 应用程序允许匿名访问，请创建新的 Web 应用程序。（尽管 Web 应用程序创建表单向您提供了允许匿名访问的选项，但 *请勿使用此选项*  ，匿名访问应在安装SharePoint 外接程序后再启用。）有关详细信息，请参阅 [Create a web application in SharePoint 2013](http://msdn.microsoft.com/library/121c8d83-a508-4437-978b-303096aa59df.aspx)。
    
  
3. 在 Web 应用程序列表中，选择可由匿名用户访问的应用程序，然后在功能区上选择"身份验证提供程序"。
    
  
4. 在打开的标注上，选择将启用匿名访问的区域。通常为"Internet"或"默认"。此时将打开"编辑身份验证"表单。
    
  
5. 选中"启用匿名访问"框（如果尚未启用）。这将设置一个对特定网站集可以关闭的默认设置，但是如果您对 Web 应用程序未启用此设置，您将无法对任何网站集启用。
    
  
6. （可选）取消选中"需要'使用远程界面'权限"框。执行此操作可允许代码和脚本在匿名用户上下文中运行，以调用每个网站集上的 SharePoint 客户端对象模型。您无法对任何网站集重新启用此要求。将框保留选中状态意味着默认情况下，匿名用户无法访问客户端对象模型，但您可以对特定网站集禁用此要求（并向其授予访问权限）。
    
    > **注释**
      > 在为匿名用户开发 SharePoint 外接程序的上下文中，此设置仅对 SharePoint 托管的外接程序有意义。专为匿名用户设计的提供程序托管的 SharePoint 外接程序使用的技术使用户权限变得无关紧要。有关详细信息，请参阅上面的 [创建可匿名访问的提供程序托管的外接程序](#Cloud-hosted)一节。 
7. 选择"保存"以关闭表单。
    
  
8. 在 Web 应用程序保持高亮显示的情况下，在功能区中选择"匿名策略"。
    
  
9. 在"匿名访问限制"表单中，选择区域并确保"无"单选按钮已启用。如果您当前测试的 SharePoint 外接程序 仅需要对 SharePoint 数据的只读权限，请改为启用"拒绝写入"。
    
    > **注释**
      > 这是在为匿名用户开发 SharePoint 外接程序的上下文中，仅对 SharePoint 托管的外接程序有意义的另一项设置。 
10. 如果您在第 2 步中创建了新的 Web 应用程序，您必须在其中创建网站集。
    
  

### 配置可匿名访问的内部部署网站集


1. 在 Web 应用程序中某个网站集的主页上，选择齿轮图标，然后选择"网站设置"。
    
  
2. 在"网站设置"页上的"用户和权限"部分，选择"网站权限"。
    
  
3. 在功能区上选择"匿名访问"。
    
  
4. 在"匿名访问"表单上，选择"整个网站"或"列表和库"，具体取决于您当前测试的外接程序所需的访问级别。不论您选择哪个选项，匿名用户都无法访问 SharePoint 托管的外接程序中的列表或库，除非库列表分别配置为允许匿名访问。下面的过程描述了如何执行此操作。
    
  
5. 如果您当前测试的外接程序需要访问 SharePoint 客户端对象模型，请确保"需要'使用远程界面'权限"框 *未*  选中。如果复选框为灰显，说明要求已在 Web 应用程序级别关闭，这与取消选中框的效果一样。（如前面的过程中所述，在为匿名用户开发 SharePoint 外接程序的上下文中，此设置仅对 SharePoint 托管的应用程序有意义。）
    
  

> **重要信息**
> 以下过程只能在 SharePoint Online 中的公共网站上执行。（有关在 Microsoft SharePoint Online 中使用公共网站的详细信息，请参阅  [Office 365 的公共网站帮助](http://office.microsoft.com/zh-cn/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1)。） 
  
    
    


### 配置可匿名访问的 SharePoint Online 网站集


1. 以租户管理员身份登录到 Office 365。
    
  
2. 在网站集的主页上，选择页面右上角附近的"将网站联机"。此操作将关闭 **"使用远程界面"权限** 要求。
    
  
如果您正在开发 SharePoint 托管的外接程序并访问 SharePoint 列表，您还必须在测试网站集中执行以下过程。使用您的外接程序的客户将需要执行此操作，在安装外接程序的任何网站上也需执行此操作。外接程序无法以编程方式或描述性方式将列表配置为允许匿名访问。外接程序可以安装自定义列表，但无法将此类列表配置为允许匿名访问。仍需在安装外接程序后手动执行此配置。如果您当前正在为匿名用户开发提供程序托管的 SharePoint 外接程序，则此过程并非必要条件。
  
    
    

> **注释**
> 此过程无法在 SharePoint Online 网站集中执行，因此安装到 SharePoint Online 且计划供匿名用户访问的 SharePoint 托管的外接程序无法访问列表或库。 
  
    
    


### 配置可匿名访问的列表或库


1. 导航到您当前测试的 SharePoint 外接程序 可访问的列表或库，打开"列表设置"或"库设置"。对于 SharePoint Online 网站，您必须以租户管理员身份登录。
    
  
2. 选择"此列表/库的权限"。
    
  
3. 在打开的页面上，在"权限"选项卡上选择"停止继承权限"，然后在确认提示框中选择"确定"。
    
  
4. 在"权限"选项卡上选择"匿名访问"。
    
  
5. 在打开的"匿名访问"表单上，选择 SharePoint 外接程序 用户需要的所有权限。您可以授予查看、编辑、添加和删除项目的权限。您无法授予完全控制权限，因此匿名用户无法更改列表的架构或更改列表设置。
    
  

## 其他资源
<a name="bk_addresources"> </a>


-  [开发 SharePoint 外接程序](develop-sharepoint-add-ins.md)
    
  

