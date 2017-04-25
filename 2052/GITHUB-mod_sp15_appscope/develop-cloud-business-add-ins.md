---
title: 开发云企业外接程序
ms.prod: SHAREPOINT
ms.assetid: 8cd707ff-e3b9-4cc7-a733-46e399d690c8
---


# 开发云企业外接程序
云企业外接程序基于 Visual Studio LightSwitch 技术。开发云企业外接程序的某些任务是云企业外接程序模板特定的，但大部分任务与 LightSwitch HTML 客户端外接程序相同。
## 云企业外接程序开发人员任务


- 云企业外接程序在 Visual Studio 中使用云企业外接程序模板创建。请参阅 [创建云企业外接程序](create-a-cloud-business-add-in.md)。


- 通过使用 SharePoint 中的文档库功能，您可以创建或上载与列表或实体中单个项目相关的文档。请参阅 [将文档库与实体相关联](associate-a-document-library-with-an-entity.md)。


- 除了将文档添加到 SharePoint 文档库时可用的 Office 模板，您还可以提供您自己的模板。请参阅 [在云企业外接程序中为文档库提供模板](provide-a-template-for-a-document-library-in-a-cloud-business-add-in.md)。


- SharePoint for Office 365 中的社交和协作功能允许用户跟踪列表中的活动并添加注释。您可以通过启用多个属性，为云企业外接程序轻松创建新闻源。请参阅 [为云企业外接程序启用新闻源](enable-a-newsfeed-for-a-cloud-business-add-in.md)。



## HTML 客户端开发任务


- 设计 HTML 屏幕时，您主要使用设计器和工具窗口，但您也可以通过特定方式，使用代码修改这些屏幕。通过使用 LightSwitch JavaScript API，您可以执行多项任务。请参阅 [如何：使用代码修改 HTML 屏幕](http://msdn.microsoft.com/zh-cn/library/jj733572.aspx#fbid=Rxm5cTOZ_Ny)。


- 开发 HTML 客户端外接程序时，您可以创建多种类型的屏幕。请参阅 [为 HTML 客户端选择屏幕类型](http://msdn.microsoft.com/zh-cn/library/jj713590.aspx)。


- 在移动设备上创建用于显示或收集信息的屏幕。请参阅 [如何：创建 HTML 客户端屏幕](http://msdn.microsoft.com/zh-cn/library/jj713589.aspx)。


- 如果您为外接程序创建了对话框或弹出框，用户可以在为该外接程序运行客户端的手机、平板电脑或其他移动设备上显示或指定信息。请参阅 [如何：创建对话框或弹出框](http://msdn.microsoft.com/zh-cn/library/jj713587.aspx)。


- 通过使用屏幕设计器，您可以修改 HTML 客户端中的屏幕的外观。请参阅 [如何：使用屏幕设计器设计 HTML 屏幕](http://msdn.microsoft.com/zh-cn/library/jj733575.aspx#fbid=Rxm5cTOZ_Ny)。


- 开发 HTML 移动客户端外接程序时，您可以在用户点击该客户端的屏幕上的按钮时添加代码。请参阅 [如何：添加按钮](http://msdn.microsoft.com/zh-cn/library/jj733573.aspx)。


- 您可以通过向屏幕添加局部属性字段，收集输入值或显示计算值。请参阅 [如何：向 HTML 屏幕添加局部属性](http://msdn.microsoft.com/zh-cn/library/jj733571.aspx#fbid=Rxm5cTOZ_Ny)。


- 您可以将自定义 HTML 控件添加到屏幕。通过使用自定义控件，您可以使用超过内置 HTML 控件的功能的方式，显示或收集信息。请参阅 [如何：向 LightSwitch 应用程序的 HTML 屏幕添加自定义控件](http://msdn.microsoft.com/zh-cn/library/jj733569.aspx#fbid=Rxm5cTOZ_Ny)。


- 通过筛选 HTML 客户端中出现的数据，您可以帮助用户标识与任务最相关的数据。请参阅 [如何：在 LightSwitch 应用程序的 HTML 客户端中筛选数据](http://msdn.microsoft.com/zh-cn/library/jj733574.aspx#fbid=Rxm5cTOZ_Ny)。


- 设计 HTML 客户端外接程序时，您指定用户如何从另一个屏幕打开某个屏幕。他们可以通过点击另一个屏幕中的列表项或命令栏上的按钮来打开屏幕。请参阅 [如何：在控制 HTML 屏幕之间的导航](http://msdn.microsoft.com/zh-cn/library/jj733570.aspx)。


- 除了标准导航以外，您还可以提供导航菜单，允许用户直接跳转到另一个屏幕。请参阅 [如何：创建导航菜单](http://msdn.microsoft.com/zh-cn/library/dn546744.aspx)。


- 开发 HTML 移动客户端外接程序时，您可以编写在用户启动特定事件时运行的 JavaScript 代码。请参阅 [如何：在移动客户端中处理屏幕事件](http://msdn.microsoft.com/zh-cn/library/jj863131.aspx)。


- 您可以创建访问和更新 SharePoint 工作流的移动外接程序，这有助于确保业务流程按特定顺序执行。例如，您可以使用工作流将文档发送审批或处理工资单。请参阅 [演练：访问 SharePoint 工作流](http://msdn.microsoft.com/zh-cn/library/dn282437.aspx)。


- 通过使用 LightSwitch，您可以创建一个 HTML 客户端，移动用户可在其中使用现代触屏设备（如手机和平板电脑）查看、添加和更新远程位置的数据。在本演练中，您将为一家虚拟的搬家公司 Contoso Moving 创建客户端，以便其客服员工可以更轻松地估计每项工作需要多少人、卡车和箱子。请参阅 [演练：为移动用户创建 LightSwitch 客户端](http://msdn.microsoft.com/zh-cn/library/jj674624.aspx)。



## 其他资源
<a name="bk_addresources"> </a>


-  [创建云企业外接程序](create-cloud-business-add-ins.md)


-  [LightSwitch 外接程序的 HTML 客户端屏幕](http://msdn.microsoft.com/zh-cn/library/jj674623.aspx)



