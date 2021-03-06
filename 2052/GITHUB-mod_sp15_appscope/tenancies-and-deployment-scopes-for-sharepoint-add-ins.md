---
title: SharePoint 外接程序的租赁和部署范围
ms.prod: SHAREPOINT
ms.assetid: 1ceb3142-a7a5-453e-920f-5f953a79401a
---


# SharePoint 外接程序的租赁和部署范围
了解租赁的概念以及在租户范围与 Web 范围部署 SharePoint 外接程序之间的区别。
## 租赁和外接程序范围
<a name="AppScope"> </a>

SharePoint 2013 租赁 是 SharePoint 场或 SharePoint Online 中的一组网站集。在 SharePoint Online 中，网站集属于一个客户帐户。在 SharePoint 场中，网站集既可以是 SharePoint Web 应用程序中的部分或所有网站集，也可以是跨服务器场中的多个 Web 应用程序的一组网站集。与 SharePoint Web 应用程序一样，租赁可具有一个 SharePoint 外接程序外接程序目录。



SharePoint 外接程序具有外接程序范围。两个可能的外接程序范围是 Web 范围或租户范围。差异不是外接程序的固有属性，并且开发人员无需确定您的外接程序所在的范围。外接程序的安装方式产生的不利影响是，决策由租户管理员制定。在将外接程序上载到租赁的外接程序目录时，可立即依次将其安装到租赁中的各个网站上。通过此方式安装的外接程序具有 Web 范围。不过，租户管理员还有一种选择。他们可以选择将外接程序批量安装到租赁中的网站子集。通过此方式安装的外接程序具有租户范围。租户管理员可通过管理路径列表、网站模板列表或网站集列表来指定将外接程序安装的网站。批量安装的外接程序只能由租户管理员卸载。租户管理员卸载外接程序时，它将从租赁中的所有网站中卸载。用户不能依次在各个网站上卸载批量安装的外接程序。同样的原则也适用于批量安装外接程序的更新：只有租户管理员可以执行此操作，并且将在安装有此外接程序的租赁中的所有网站中批量更新。



如果批量安装包含外接程序 Web 的外接程序，则仅创建一个外接程序 Web，并且该外接程序安装到的所有主机网站将共享此外接程序 Web。外接程序 Web 位于组织外接程序目录的网站集中。



当租赁中创建了新的网站集时，之前批量安装的外接程序将自动安装在新的网站集上。




> **注释**
> 不要将外接程序范围与功能范围混淆。功能范围确定功能中元素的部署位置。可能的位置包括 **Farm**、 **WebApplication**、 **Site**（即网站集）和 **Web**。仅允许对 SharePoint 外接程序中的功能（主机 Web 功能和外接程序包中的 .wsp 中的功能）使用 **Web**。 <BR /> 此外，不应将外接程序范围与外接程序权限级别混淆。SharePoint 外接程序可在列表、Web、网站集和租赁级别上请求 SharePoint 内容的所有或选定部分的权限。在租户范围内安装外接程序不会向其授予原本没有的权限，也不会取消 SharePoint 安全模型的关键设置。有关外接程序权限的详细信息，请参阅  [SharePoint 2013 中的外接程序权限](add-in-permissions-in-sharepoint-2013.md)。 





## 租户范围的外接程序的限制
<a name="Tenant"> </a>

无法批量安装以下类型的外接程序：




- 包含功能区的自定义操作的外接程序。（允许部署为菜单项的自定义操作。）


- 包含外接程序部件的外接程序。


此外，请再次注意，在 SharePoint Online 的 Office 365 小型企业高级版中无法安装租户范围。




## 如何在租赁中的多个网站中安装、卸载和更新外接程序
<a name="Web"> </a>

无论是从 Office 商店还是从外接程序目录安装外接程序，租户管理员都可以通过以下步骤在租赁中的多个网站中安装、卸载和更新外接程序。




### 将 SharePoint 外接程序安装到多个网站


1. 导航到企业目录网站的"网站内容"页。


2. 选择"添加外接程序"并安装外接程序，就像在任何其他 SharePoint 网站上执行此操作一样。


3. 安装外接程序之后，将鼠标指针悬停在指向"网站内容"页中外接程序的链接的上方。 随即出现"..."链接。


4. 选择链接，标注随即出现。


5. 在菜单上选择"部署"。


6. 使用打开的部署 UI 指定要将外接程序安装到的网站集。您可以按管理路径、网站模板或 URL 进行筛选。筛选器拥有逻辑"OR"关系：外接程序被安装到通过任意一个或多个筛选器的网站集的并集中。


7. 选择"确定"。



### 卸载批量安装的 SharePoint 外接程序


1. 导航到企业目录网站的"网站内容"页。


2. 将鼠标指针悬停在指向"网站内容"页中外接程序的链接的上方。随即出现"..."链接。


3. 选择链接，标注随即出现。


4. 选择标注上的"删除"。



### 更新批量安装的 SharePoint 外接程序


1. 导航到企业目录网站的"网站内容"页。 如果外接程序有可用的更新，则外接程序的旁边会显示消息。将会提供更新外接程序的链接。


2. 单击链接以更新外接程序。


3. 在系统提示您批准外接程序的权限请求时，选择"信任它"。



## 其他资源
<a name="SP15tenancies_addlresources"> </a>


-  [发布 SharePoint 外接程序](publish-sharepoint-add-ins.md)


-  [SharePoint 外接程序体系结构的重要方面和开发前景](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [部署和安装SharePoint 外接程序：方法和选项](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [SharePoint 外接程序更新过程](sharepoint-add-ins-update-process.md)



