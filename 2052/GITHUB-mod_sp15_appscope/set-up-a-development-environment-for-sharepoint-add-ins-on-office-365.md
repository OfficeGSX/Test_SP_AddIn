---
title: 在 Office 365 上设置 SharePoint 加载项的开发环境
ms.prod: MULTIPLEPRODUCTS
ms.assetid: b22ce52a-ae9e-4831-9b68-c9210af6dc54
---


# 在 Office 365 上设置 SharePoint 加载项的开发环境
在 Office 365 开发人员网站上设置 SharePoint 外接程序开发环境。
在执行本文中的任何步骤之前，请阅读  [SharePoint 外接程序开发工具和环境](tools-and-environments-for-developing-sharepoint-add-ins.md)来了解各个选项。如果您不确定要创建哪种 SharePoint 外接程序，请参阅  [SharePoint 外接程序](sharepoint-add-ins.md)。





## 在您的计算机上安装 Visual Studio 和工具
<a name="devenv_vs"> </a>


- 如果您还没有安装 **Visual Studio** 2013 或更高版本，请按照 [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx) 中的说明进行安装。建议使用 [从 Microsoft 下载中心获取的最新版本](https://www.visualstudio.com/downloads/download-visual-studio-vs)。


- Visual Studio 提供了 **Visual Studio Microsoft Office 开发人员工具** ，但有时这些工具的版本发布的各个更新之间 Visual Studio。若要确保您拥有最新版本的工具使用运行 [Visual Studio 2013 的 Office 开发人员工具的安装程序 ](http://aka.ms/OfficeDevToolsForVS2013)，或  [Visual Studio 2015 的 Office 开发人员工具的安装程序 ](http://aka.ms/OfficeDevToolsForVS2015)。



### Visual Studio 中的详细日志记录

如果想要启用详细日志记录，请执行以下步骤：




1. 打开注册表，导航到 **HKEY_CURRENT_USER\\Software\\Microsoft\\VisualStudio\\ _nn.n_\\SharePointTools** ，其中 _nn.n_ 是 Visual Studio 版本，如 12.0 或 14.0。


2. 添加名为 EnableDiagnostics 的 DWORD 项。


3. 为此项提供值 1。


在 Visual Studio 的未来版本中，注册表路径将会更改。




## 注册 Office 365 开发人员网站
<a name="o365_signup"> </a>


> **注释**
>  您可能已经拥有 Office 365 开发人员网站的访问权限：> **已经是 MSDN 订阅者？** Visual Studio Enterprise with MSDN 订阅者可以享受 Office 365 开发人员订阅权益。 [立即索取您的权益](https://msdn.microsoft.com/subscriptions/manage/default.aspx)。 > **您是否具有下列 Office 365 订阅计划之一？**> **如果有的话，Office 365 订阅的管理员可以创建一个 开发人员网站** ，方法是使用 [Office 365 管理中心](https://portal.microsoftonline.com/admin/default.aspx)。有关详细信息，请参阅 [在现有 Office 365 订阅中创建开发人员网站](create-a-developer-site-on-an-existing-office-365-subscription.md)。 




获取 Office 365 计划的三种方法。




- 可通过 Office 365 开发人员计划免费注册一年的 Office 365 开发人员帐户。 [获取更多信息](http://dev.office.com/devprogram)，或直接转到 [注册表](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170)。您通过用于注册开发人员帐户的链接注册了开发人员计划后，将收到一封电子邮件。请使用以下说明。


- 开始 [免费 30 天试用](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK)，获取一个用户许可证。


- 购买  [Office 365 开发人员订阅](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK)。



> **提示**
> 为了方便您使用以下说明，请在另一个窗口或选项卡中打开这些链接。 





**图 1. Office 365 开发人员网站域名**








![Office 365 帐户注册表单第 2 页](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)












1. 注册表单的第一页（未显示）很容易理解。只需提供一些有关您的必要信息，然后选择"下一步"。


2. 在第二页（如图 1 所示）上指定订阅管理员的用户 ID。


3. 创建 **.onmicrosoft.com** 的子域。

    注册后，您必须使用生成的凭据（格式： _UserID_@ _您的域_.onmicrosoft.com）登录到您的 Office 365 门户网站以便管理帐户。您的 SharePoint Online 开发人员网站将在新域中进行设置： **http:// _您的域_.sharepoint.com** 。


4. 选择"下一步"并填写表单的最后一页。如果您选择提供电话号码来获取确认代码，可以提供一个移动或固定电话号码，但 *不能是*  VoIP（IP 电话）号码。




> **注释**
> 当您尝试登录开发人员帐户时，如果您已经登录其他 Microsoft 帐户，则有可能收到这样的消息："对不起，您输入的用户 ID 无法使用。它似乎是无效的。请务必输入您的组织分配的用户 ID。通常，您的用户 ID 类似于  *someone@example.com*  或 *someone@example.onmicrosoft.com*  。"> 如果您看到这种消息，请注销正在使用的 Microsoft 帐户并重试。如果仍然收到这种消息，请清除浏览器缓存或切换到"InPrivate 浏览"，然后填写表单。 




完成注册过程后，您的浏览器会打开 Office 365 安装页。选择管理员图标以打开管理中心页。




**图 2. Office 365 管理中心页**








![显示 Office 365 管理中心的屏幕截图。](images/SP15_Office365AdminInset_border.png)








1. 您必须等待开发人员网站完成设置。设置完成后，在浏览器中刷新 管理中心页。


2. 然后，选择页面左上角的"构建加载项"链接，以打开您的开发人员网站。您会看到如图 3 所示的网站。页面上有一个"测试中的加载项"列表。这证实该网站是使用 SharePoint 开发人员网站模板构建的。如果您看到的是一个常规的工作组网站，请稍等几分钟，然后重新启动网站。


3. 记下网站的 URL，在 Visual Studio 中创建 SharePoint 外接程序项目时将会用到。



**图 3. 您的开发人员网站主页，其中包含"测试中的加载项"列表**








![显示开发人员网站主页的屏幕截图。](images/SP15_DeveloperSiteHome_border.png)












## 其他资源
<a name="SP15SetupSPO365_bk_addlresources"> </a>


-  [SharePoint 外接程序](sharepoint-add-ins.md)


-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [开始创建 SharePoint 承载的 SharePoint 外接程序](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)







