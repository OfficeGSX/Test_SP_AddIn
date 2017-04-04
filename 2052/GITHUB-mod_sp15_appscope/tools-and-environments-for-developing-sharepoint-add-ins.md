---
title: SharePoint 外接程序开发工具和环境
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# SharePoint 外接程序开发工具和环境
了解创建 SharePoint 外接程序的开发环境的选项。
以下是 SharePoint 外接程序开发环境的两个基本模式：
  
    
    


- **测试和调试 SharePoint 网站在 Office 365 订阅的 SharePoint Online 网站中进行。** 通常，Visual Studio 安装在本地计算机上，但也可以安装在基于云的 Visual Studio 中。
    
  
- **测试和调试 SharePoint 网站在本地的单服务器 SharePoint 场上进行。** 通常，Visual Studio 安装在同一台计算机上。
    
  

请考虑以下事项：
  
    
    


- 几乎您创建的所有外接程序都可以部署到 SharePoint Online 或部署到本地 SharePoint 场，不论您使用的是哪种类型的环境。根据通用规则，不能部署到 SharePoint Online 的外接程序也不能用其进行开发。例如：需要具备 [完全控制权限](add-in-permissions-in-sharepoint-2013.md)的外接程序和使用 [高信任授权系统](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)的外接程序。
    
  
- 您可以开发 SharePoint 托管和提供程序托管的 SharePoint 外接程序，不论您使用的是哪种类型的环境。
    
  
- 您可以有本地和 SharePoint Online 两种测试网站。
    
  
- 考虑各种因素，两个选项设置起来都同样简单。
    
  
 **若要使用可用于开发的 SharePoint Online 订阅创建 SharePoint Online 环境** ，请参阅 [在现有 Office 365 订阅中创建开发人员网站](create-a-developer-site-on-an-existing-office-365-subscription.md)。 **若要创建本地环境** ，请参阅 [设置 SharePoint 加载项的本地开发环境](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)。
> [!注释]
> 本主题只涉及开发 SharePoint 外接程序的环境。如果您计划开发 服务器场解决方案，请参阅 [设置 SharePoint 2013 的常规开发环境](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx)。如果您计划进行两种类型的开发，请查看后续文章，然后参阅 [设置 SharePoint 加载项的本地开发环境](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)，以了解开发 SharePoint 外接程序所需执行的其他步骤。 
  
    
    


## 其他工具信息


-  [使用 Napa Office 365 开发工具创建 SharePoint 托管的基本的外接程序](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)
    
  
-  [在 Visual Studio 中创建 SharePoint 外接程序](create-sharepoint-add-ins-in-visual-studio.md)
    
  

## 其他资源
<a name="bk_addresources"> </a>


-  [SharePoint 外接程序](sharepoint-add-ins.md)
    
  

