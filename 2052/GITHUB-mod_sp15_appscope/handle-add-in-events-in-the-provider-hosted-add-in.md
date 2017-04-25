---
title: 处理提供程序托管的外接程序中的外接程序事件
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# 处理提供程序托管的外接程序中的外接程序事件
了解如何自定义提供程序托管的 SharePoint 外接程序的安装。
这是关于开发 SharePoint 托管的 SharePoint 外接程序的基础知识系列文章中的第七篇文章。您应该首先熟悉  [SharePoint 外接程序](sharepoint-add-ins.md)以及本系列中之前的文章：





-  [开始创建提供程序承载的 SharePoint 加载项](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [使提供程序托管的外接程序具有 SharePoint 的外观](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [在提供程序托管的外接程序中包含自定义按钮](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [获取 SharePoint 对象模型的快速概述](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [将 SharePoint 写入操作添加到提供程序托管的外接程序](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [在提供程序托管的外接程序中包括外接程序部件](include-an-add-in-part-in-the-provider-hosted-add-in.md)



> **注释**
> 如果您阅读过关于提供程序托管的外接程序的系列文章，那么您应该具有 Visual Studio 解决方案，可以继续阅读本主题。您还可以从  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) 下载存储库并打开 BeforeAdd-inEventHandlers.sln 文件。




在本文中，我们将对 SharePoint 中一类称为外接程序事件的事件的处理进行自定义设置。具体地说，我们将为外接程序安装和卸载事件创建处理程序。列表和列表项事件也能进行自定义处理。您将在本系列的后续文章中了解这些处理程序。所有这些事件将在 SharePoint 中触发，但处理每个事件的自定义代码位于您的远程 Web 应用程序中。您可将 SharePoint 配置为通过在 SharePoint 事件中注册处理程序的 URL 来调用自定义处理程序。
## 以编程方式部署 SharePoint 组件的两个位置

我们希望连锁店外接程序自动创建和部署"本地员工"和"预期装运"列表。外接程序可以随时部署 SharePoint 组件，例如自定义列表。但如果外接程序依赖于某个特定组件，例如自定义列表，组件应在用户开始使用外接程序之前进行部署。对于此类重要组件，有两个位置可以运行自定义部署逻辑：




- 在外接程序安装事件的处理程序中。


- 在外接程序在 SharePoint 中首次启动时执行的首次运行逻辑中。


决定对于指定外接程序最合适的部署逻辑是一个高级主题。在本文中，我们仅进行一些比较：




- 自定义安装处理程序必须在 30 秒内完成。对于首次运行逻辑需要的时间没有限制。


- 如果在外接程序安装过程中出现任何错误，SharePoint 将回退它已执行的任何操作。自定义安装处理程序将在 SharePoint 完成安装外接程序的所有操作后运行，因此自定义处理程序可以参与此系统。例如，如果您的自定义逻辑抛出异常，您可以通知 SharePoint 回退整个外接程序安装。但是，如果在自定义首次运行逻辑中出错，外接程序将保留为已安装状态并且很可能无法正常工作。


- 如果 SharePoint 必须回退外接程序安装，它也不会放弃。它将立即再次尝试安装，最多尝试 4 次（每次尝试的时间限制为 30 秒）。每次重试时，自定义安装处理程序将再次从头运行。例如，如果处理程序在回退之前成功安装了某个列表，它在重试时会尝试再次安装该列表。为了防止这种情况，必须编写安装处理程序中的代码，使其不执行任何操作（例如部署组件），除非它首先检查是否已执行了该操作。这使得安装处理程序的逻辑比首次运行逻辑更加复杂，因为首次运行逻辑不会重试（除非您专门将其编码为这样做）。另外，检查组件是否已部署通常需要通过 Internet 执行从远程处理程序到 SharePoint 的调用，这非常耗时。如果发现组件尚未部署，实际部署组件需要进行第二次调用。


对于连锁店外接程序，我们会将这些策略结合起来。在本文中，您将创建一个安装处理程序，它会将主机 Web 在公司数据库中注册为一个租户，然后设置一个信号，指定外接程序是否已在主机 Web 上运行。在本系列的后续文章中，您将首次运行逻辑放在外接程序起始页的 **Page_Load** 方法中。该逻辑将部署两个自定义列表并执行其他一些操作。




## 配置事件接收器调试解决方案
<a name="RERDebug"> </a>

调试事件接收器需要使用 Azure 服务总线。请按照 [在 SharePoint 外接程序中调试远程事件接收器并排除故障](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)中的说明进行操作。因为您使用 SharePoint Online 网站作为测试网站，请务必对远程测试网站执行这些说明。本系列的其余文章假定您已成功配置调试。




## 创建安装处理程序
<a name="RERDebug"> </a>






> **注释**
>  Visual Studio 中的启动项目的设置可能会在重新打开解决方案时恢复为默认值。重新打开本系列文章中的示例解决方案后，请始终立即执行下列步骤：>  右键单击"解决方案资源管理器"顶部的解决方案节点，然后选择"设置启动项目"。>  确保所有三个项目在"操作"列中均设置为"启动"。





1. 在"解决方案资源管理器"中，选择"ChainStore"项目，因此其属性将显示在Visual Studio 的"属性"窗格中。


2. 将"处理外接程序已安装"的值设置为"True"。（可能仍称为"处理应用程序已安装"。）这将执行两项操作：

  - 在"ChainStoreWeb"项目（不是"ChainStore"项目）中创建一个名为"服务"的文件夹，并在其中添加两个文件：AppEventReceiver.svc 文件及 AppEventReceiver.svc.cs 文件的隐藏代码。（文件名称以字符串"App"开头，因为外接程序以前称为"apps"。 *请勿对这些文件重命名。*  Visual Studio Office 开发人员工具 假定文件将继续使用这些名称。）


  - 在外接程序清单中注册处理程序 URL。清单的此部分在清单设计器中不可见。要进行查看，右键单击 AppManifest.xml 文件并选择"查看代码"。"属性"元素有一个新子项看起来如下所示。该标记通知 SharePoint 在完成与安装外接程序相关的所有工作后调用该服务的 **ProcessEvent** 方法。自定义处理程序是在安装中运行的最后一个操作。字符串 `~remoteAppUrl` 是一个占位符，Visual Studio Office 开发人员工具 会将其替换为服务主机 URL。当您进行调试时，为 Azure 服务总线 URL。当您创建用于部署到生产的软件包时，则为生产 URL。

  ```XML

<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
  ```

3. 打开 AppEventReceiver.svc.cs 文件。


4. 您将看到，Visual Studio Office 开发人员工具 已创建 **ProcessEvent** 方法的一个示例实施。此方法的所有实施将首先初始化 **SPRemoteEventResult** 对象，最后都会将该对象返回到 SharePoint。此外，该对象会告知 SharePoint 是否应该回退事件，因为自定义处理逻辑失败了。工具可能还在创建 **ClientContext** 对象的该方法中包括一个 **using** 块。连锁店外接程序中的自定义处理程序不会回调到 SharePoint，因此请删除此块。现在方法看起来应该如下所示：

  ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
  ```

5. 三个可能的外接程序事件均可调用事件接收器，因此请将以下 **switch** 结构添加到 **ProcessEvent** 方法中，位置为创建和返回 `result` 对象的行之间。事件名称中包含字符串"App"，因为外接程序以前被称为"apps"。

  ```cs

switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.     
     
        break;
}
  ```

6. 我们的安装逻辑将调用 SQL 存储过程，以便在远程 Web 应用程序上将香港分店注册为一个租户。如果此过程失败，处理程序必须通知 SharePoint 回退外接程序的安装，这一点很重要，因此请添加以下 **try/catch** 块替换 `TODO2`。对于此代码，请注意以下事项。

  - 您将在后续步骤中创建  `tenantName` 对象和 `CreateTenant` 方法。


  - **SPRemoteEventResult** 对象的 **Status** 属性具有三个可能的值： **Continue**（默认值）、 **CancelNoError** 和 **CancelWithError**。后两个值通知 SharePoint 回退事件。



  ```cs

try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

7. 主机 Web URL 是示例的租户鉴别符，它是 SharePoint 在 **SPRemoteEventProperties** 参数中传递到接收器的信息的一部分。将以下行添加到 **ProcessEvent** 方法，位置为 **SPRemoteEventResult** 对象初始化下方的行。

  ```cs

string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
  ```

8. 现在，我们的代码必须处理 **AppEventProperties.HostWebFullUrl** 属性的一个奇怪现象。在大多数其他上下文中，SharePoint 在主机 Web URL 结尾包含"/"字符，因此我们的示例代码的逻辑假定该字符存在。但是如果且仅当主机 Web 是网站集的根 Web 时，SharePoint 才会将该字符添加到 **HostWebFullUrl** 值的结尾。由于我们的香港网站是网站集中的子网站，我们需要添加该字符，确保在整个示例中使用相同的租户名称字符串。将以下行添加到 `tenantName` 对象的初始化下方。

  ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
  ```

9. 将以下 **using** 语句添加到文件顶部。

  ```

using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
  ```

10. 将以下方法添加到  `AppEventReceiver` 类。我们不对此进行详细讨论，因为本系列文章的目的是教您进行 SharePoint 外接程序编程，而不是 SQL Server/Azure 编程。

    该方法将在名为"租户"的数据库表中创建一行。除了"名称"列以外，表中还有一个"版本"列，其默认值设置为 0000.0000.0000.0000。在本系列的后续文章中，您将创建首次运行逻辑，将根据该值确定外接程序是否已安装到主机 Web 上。如果版本为 0000.0000.0000.0000，您的代码将部署"本地员工"和"预期装运"列表，然后增加版本号。



  ```cs

private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
  ```


## 创建卸载处理程序
<a name="RERDebug"> </a>

在处理已安装事件时处理卸载事件通常是比较好的做法。基本概念是卸载处理程序会删除或回收已安装处理程序部署的内容。但是，存在很多异常，因此您真的需要理解外接程序的用例。例如，使用外接程序部署和填充的列表在外接程序本身卸载后可能仍具有值，在这种情况下，您不希望在卸载事件处理程序中卸载列表。



正如您所期望的，当用户从"网站内容"页面删除外接程序时，不会允许卸载事件。执行此操作只会将外接程序移到网站的回收站。用户可以还原，但还原不会重新运行已安装事件处理程序，因此您希望在外接程序还原后，使用已安装事件处理程序部署的任何内容仍然存在。SharePoint 组件可以从回收站移到第二阶段回收站。仅当外接程序从第二阶段删除时，才会发生卸载事件。当用户执行此操作时，外接程序将不可还原，因此我们希望香港分店的租户此时将从公司数据库中删除。




1. 将"处理外接程序卸载"的值设置为"True"（可能仍称为"处理应用程序卸载"）。这会在 AppManifest.xml 文件中注册处理程序，就像之前注册安装处理程序一样。查看该文件时，您会看到具有完全相同的 URL。Visual Studio Office 开发人员工具 假定您使用相同的 *.svc 文件。我们在本示例中执行此操作，这是标准做法。


2. 在 AppEventReceiver.svc.cs 文件中添加以下代码替换  `TODO3`。对于此代码，请注意以下事项。

  - 在下一步中添加  `DeleteTenant` 方法。


  - 回退外接程序的卸载会将其留在第二阶段回收站中，从该回收站仍可进行还原。



  ```cs

try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

3. 将以下方法添加到  `AppEventReceiver` 类。

  ```cs

private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();            
    }//dispose conn and cmd
}
  ```


> **注释**
> 在本系列之前的文章中，您将项目配置为在每次按 F5 时重建公司数据库。这会清空"租户"表。 





## 运行外接程序并测试安装处理程序
<a name="RERDebug"> </a>


1. 使用 F5 键部署并运行您的外接程序。Visual Studio 在 IIS Express 中托管远程 Web 应用程序，在 SQL Express 中托管 SQL 数据库。它还会在 SharePoint 测试网站上临时安装外接程序，运行安装事件处理程序并立即运行它。在起始页打开之前，将提示您向外接程序授予权限。


2. 外接程序的起始页打开后，按顶部部件版式控件上的齿轮图标，然后选择"帐户设置"。


3. 在"帐户"页面上，按"显示外接程序版本"按钮。版本显示为 0000.0000.0000.0000。

![标题为'帐户设置'的'帐户'页面。一个名为'显示外接程序版本'的按钮，下方为一行文本'已注册版本: zero zero zero zero dot zero zero zero zero dot zero zero zero zero dot zero zero zero zero'。](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)





4. 要结束调试会话，请关闭浏览器窗口或在 Visual Studio 中停止调试。每次按 F5 时，Visual Studio 将撤回外接程序的之前版本并安装最新版本。


5. 您将在其他文章中使用此外接程序和 Visual Studio 解决方案，因此最好是当您使用一段时间后，最后一次撤回外接程序。在"解决方案资源管理器"中右键单击此项目，然后选择"撤回"。



## 
<a name="Nextsteps"> </a>

在本系列的下一篇文章中，您将向外接程序添加首次运行逻辑，以编程方式部署"本地员工"列表和自定义功能区按钮： [将首次运行逻辑添加到提供程序托管的外接程序](add-first-run-logic-to-the-provider-hosted-add-in.md)




