---
title: Tools und Umgebungen für die Entwicklung von Add-Ins für SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# Tools und Umgebungen für die Entwicklung von Add-Ins für SharePoint
Erfahren Sie mehr über die Optionen zum Erstellen einer Entwicklungsumgebung für SharePoint-Add-Ins.
Es gibt zwei grundlegende Muster für Entwicklungsumgebungen für SharePoint-Add-Ins:
  
    
    


- **Die SharePoint-Website für Test und Debugging ist in einer SharePoint Online-Website in einem Office 365-Abonnement enthalten.** Normalerweise wird Visual Studio auf einem lokalen Computer installiert, aber auch eine cloudbasierte Visual Studio-Installation ist eine Option.
    
  
- **Das Testen und Debuggen einer SharePoint-Website befindet sich auf einer lokalen SharePoint-Farm mit einem Server. Visual Studio ist auf demselben Computer installiert.**.
    
  

Beachten Sie Folgendes:
  
    
    


- Nahezu jedes von Ihnen erstellte Add-In kann entweder an SharePoint Online oder lokale SharePoint-Farmen bereitgestellt werden, unabhängig davon, welche Art von Umgebung Sie verwenden. Allgemein gilt, dass Apps, die nicht an SharePoint Online bereitgestellt werden können, auch nicht damit entwickelt werden können. Beispiele: Add-Ins, die  [Vollzugriffberechtigungen](add-in-permissions-in-sharepoint-2013.md) benötigen, und Add-Ins, die das [besonders vertrauenswürdige Autorisierungssystem](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) verwenden.
    
  
- Sie können sowohl von SharePoint gehostete als auch von einem Anbieter gehostete SharePoint-Add-Ins entwickeln, unabhängig davon, welche Art von Umgebung Sie verwenden.
    
  
- Sie können sowohl lokale als auch SharePoint Online-Testwebsites haben.
    
  
- Insgesamt sind die zwei Optionen gleich einfach einzurichten.
    
  
 **Informationen zum Erstellen der SharePoint Online-Umgebung** mithilfe eines SharePoint Online-Abonnements finden Sie unter [Erstellen einer Entwicklerwebsite in einem vorhandenen Office 365-Abonnement](create-a-developer-site-on-an-existing-office-365-subscription.md). **Informationen zum Erstellen der lokalen Umgebung** finden Sie unter [Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
> [!HINWEIS]
> Dieses Thema befasst sich nur mit Umgebungen für die Entwicklung von SharePoint-Add-Ins. Wenn Sie die Entwicklung von Farmlösungen planen, finden Sie weitere Informationen unter  [Einrichten einer allgemeinen Entwicklungsumgebung für SharePoint 2013](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx). Wenn Sie beide Arten von Entwicklung planen, beginnen Sie mit dem letzteren Artikel, und sehen Sie sich dann  [Einrichten einer lokalen Entwicklungsumgebung für SharePoint-Add-Ins](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md) für weitere Schritte an, die für die Entwicklung von SharePoint-Add-Ins erforderlich sind.
  
    
    


## Weitere Toolinformationen


-  [Erstellen eines einfachen von SharePoint gehosteten Add-Ins mithilfe von Napa Office 365-Entwicklungstools](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)
    
  
-  [Erstellen von SharePoint-Add-Ins in Visual Studio](create-sharepoint-add-ins-in-visual-studio.md)
    
  

## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [SharePoint-Add-Ins](sharepoint-add-ins.md)
    
  

