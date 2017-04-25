---
title: 部署和安装SharePoint 外接程序：方法和选项
ms.prod: SHAREPOINT
ms.assetid: d15a74a7-3c10-485a-9885-7ef11aaa0d90
---


# 部署和安装SharePoint 外接程序：方法和选项
了解发布、安装和卸载 SharePoint 外接程序的方法。
## 发布到 Office 商店或组织的外接程序目录
<a name="MarketOrCatalog"> </a>

您可以将 SharePoint 外接程序上载到公用 Office 商店或组织的专用外接程序目录。专用外接程序目录是 SharePoint 2013 Web 应用程序（或 SharePoint Online 租赁）中的一个专用网站集，用于托管 SharePoint 外接程序和 Office 外接程序的文档库。将该目录放入其自有网站集更便于 Web 应用程序管理员或租户管理员限制对该目录的权限。



如果将外接程序上载到 Office 商店，Microsoft 会对其运行一些验证检查。例如，它会检查外接程序清单标记是否有效且完整，并验证包含的任何 SharePoint 解决方案包（.wsp 文件）不包括超过 **Web** 范围而不允许的元素或功能。还会检查包的内容是否存在不允许的内容。如果外接程序通过所有测试，则会将外接程序包打包到一个文件并由 Microsoft 签署。




> **注释**
> 如果您使用 Visual Studio Microsoft Office 开发人员工具 开发和部署外接程序，外接程序将直接安装在目标测试 SharePoint 网站上。因为它未通过 Office 商店传递，因此不会进行上述验证检查。 




将 SharePoint 外接程序上载到组织的外接程序目录与将任何文件上载到 SharePoint Foundation 文档库一样容易。您需要填写弹出窗体，在其中提供外接程序包的本地 URL 和其他信息，例如外接程序的名称。将外接程序上载到组织的外接程序目录后，会对其进行类似的检查，未通过检查的外接程序将标记为无效或在该目录中禁用。



租户管理员和 SharePoint 2013 Web 应用程序管理员可以在 Office 商店上购买 SharePoint 外接程序。若要打开 Office 商店，管理员在"站点内容"页上选择"添加外接程序"，然后在"您的外接程序"页中选择"SharePoint 商店"。这将打开一个"SharePoint 商店"页，管理员可以通过它来发现和了解供应商提供的 SharePoint 外接程序。（他们也可以在 office.com 上执行此操作。）如果在管理员的 Web 应用程序或租赁上未安装外接程序需要的先决条件，则该外接程序变暗显示且在外接程序商店中不可用。例如，如果外接程序要求搜索服务而未安装该服务，该外接程序会变暗显示。管理员可以对外接程序列表进行排序、筛选和浏览，了解外接程序、查看外接程序评论，以及购买外接程序的许可证。



管理员在决定购买许可证时，必须接受购买的条款和条件，并同意执行该外接程序必须具备的权限，如对列表的读取访问权限或对网站集的完全控制访问权限。



购买一个外接程序的一个或多个许可证时，许可证将下载到 Web 应用程序或租赁。尽管管理员可以选择一起进行安装和许可证购买，但购买许可证时不会自动下载并安装外接程序。



用户从"您的外接程序"页安装外接程序。此页面包含以下内容的合并列表：




- Web 应用程序（或租户）的组织的外接程序目录中的 SharePoint 外接程序。


- 组织或租户已对其拥有已分配给用户的站点许可证或许可证的 Office 商店中的 SharePoint 外接程序。


用户可立即安装的所有外接程序都会列出（仅有少数例外）。用户可以筛选页面上包含的外接程序以仅包含该组织的外接程序目录中的外接程序。安装外接程序后，它会显示在它安装到的网站的"站点内容"页上的外接程序列表中。




## 安装 SharePoint 外接程序
<a name="Installing"> </a>

网站所有者按本主题中前面所述从"您的外接程序"页安装 SharePoint 外接程序。安装会创建该外接程序的一个实例。有关安装外接程序的详细信息，请参阅 [将 SharePoint 外接程序添加到 SharePoint 2013 网站](https://technet.microsoft.com/zh-cn/library/fp161231.aspx)。




> **注释**
> 有时网络连接暂时丢失会阻止安装。不管任何原因导致安装失败，安装基础结构都会重试三次。如果不成功，用户界面中会指出（显示）安装失败。用户可以稍后重试安装。 





## 卸载 SharePoint 外接程序
<a name="Uninstalling"> </a>

网站所有者可以通过 SharePoint UI 卸载 SharePoint 外接程序的实例。SharePoint 外接程序实例的卸载非常彻底。这意味着外接程序安装的所有内容都将卸载。



但是，外接程序使用但是与外接程序单独安装的组件不会删除。例如，假定外接程序具有一个远程网页，其中包含在主机 Web 上创建列表的按钮。卸载外接程序会将外接程序的磁贴从"网站内容"页面中删除，这反过来会使远程页面对最终用户不可访问或不可使用，但不会删除随外接程序一起创建的列表。SharePoint 不会记录外接程序在主机 Web 上创建了哪些列表，用户在 SharePoint UI 中创建了哪些列表，因此它无法删除在外接程序中创建的列表。通常这是希望的行为，因为即使在删除创建列表的外接程序后，列表中的数据可能仍然对用户有用。



如果 SharePoint 外接程序包含一个外接程序 Web，则该外接程序 Web 将被删除。与系统地停用功能并还原到外接程序的内部 .wsp 文件的部署相比，这种卸载方式更为彻底。




> **注释**
> 当用户删除外接程序时，外接程序将移到第一阶段回收站。将其从中删除则会移到第二阶段回收站。如果从第二阶段回收站中删除，则将彻底卸载，且不能恢复。 




根据这些规则，如果外接程序已删除（回收），则该外接程序的权限也将被吊销：




- Web 范围内的权限将始终被吊销。


- 如果网站集中没有外接程序的任何其他实例，则网站集范围内的权限也将被吊销。


- 如果网站订阅（租赁）中没有外接程序的任何其他实例，则租户范围内的权限也将被吊销。


如果在外接程序的外接程序清单中已注册一个  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) Web 服务，则在卸载过程的开始（该过程发生在将外接程序从第二阶段回收站中删除的时候）会执行该服务。如果您有一个 [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) Web 服务，则最佳做法是有一个 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) Web 服务并将 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) 服务设计为撤消在 [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) 服务中所做的任何操作。有关详细信息，请参阅 [处理 SharePoint 外接程序中的事件](handle-events-in-sharepoint-add-ins.md)。




## 其他资源
<a name="SP15deployinstallapps_addlresources"> </a>


-  [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)


-  [SharePoint 外接程序体系结构的重要方面和开发前景](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [SharePoint 外接程序更新过程](sharepoint-add-ins-update-process.md)



