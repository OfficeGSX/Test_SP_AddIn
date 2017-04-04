---
title: Средства и среды для разработки надстроек для SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# Средства и среды для разработки надстроек для SharePoint
Узнайте о вариантах создания среды разработки для надстроек SharePoint.
Существуют два основных шаблона среды разработки для надстроек SharePoint:
  
    
    


- **Веб-сайт SharePoint для тестирования и отладки включен в веб-сайт SharePoint Online подписки на Office 365.** Обычно Visual Studio устанавливается на локальный компьютер, но можно использовать и облачную версию Visual Studio.
    
  
- **Веб-сайт SharePoint для тестирования и отладки находится в локальной односерверной ферме SharePoint.** Visual Studio устанавливается на тот же компьютер.
    
  

Обратите внимание вот на что:
  
    
    


- Практически любую созданную надстройку можно развернуть в SharePoint Online или на локальных фермах SharePoint, независимо от типа используемой среды. Как правило, надстройки, в SharePoint Online невозможно развернуть те надстройки, которые там невозможно создать. К таким надстройкам могут относиться те, для которых требуются  [разрешения на полный доступ](add-in-permissions-in-sharepoint-2013.md), или те, которые используют  [систему авторизации с высоким уровнем доверия](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).
    
  
- Можно разработать Надстройки SharePoint с размещением в SharePoint и у поставщика, независимо от типа используемой среды.
    
  
- У вас могут быть как локальные тестовые сайты, так и тестовые сайты SharePoint Online.
    
  
- В целом оба варианта одинаково легко настроить.
    
  
 **Руководство по созданию среды SharePoint Online** с помощью подписки SharePoint Online, которую можно использовать для разработки, см. в статье [Создание сайта разработчика с использованием актуальной подписки на Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md). **Инструкции по созданию локальной среды** см. в статье [Настройка локальной среды разработки надстроек SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
> [!Примечание]
> Эта статья посвящена только средам разработки надстроек SharePoint. Если вы хотите разрабатывать решения ферм, см. статью  [Настройка общей среды разработки для SharePoint 2013](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx). Если вы планируете разработать оба варианта, начните с последней статьи, а затем ознакомьтесь с дополнительными действиями по разработке надстроек SharePoint, описанными в статье  [Настройка локальной среды разработки надстроек SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md). 
  
    
    


## Сведения о других средствах


-  [Создание простой надстройки для SharePoint с размещением в SharePoint с помощью средств разработки Napa для Office 365](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)
    
  
-  [Создание надстроек SharePoint в Visual Studio](create-sharepoint-add-ins-in-visual-studio.md)
    
  

## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Надстройки SharePoint](sharepoint-add-ins.md)
    
  

