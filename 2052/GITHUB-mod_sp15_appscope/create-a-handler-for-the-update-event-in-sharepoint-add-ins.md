---
title: 在 SharePoint 外接程序中创建更新事件的处理程序
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# 在 SharePoint 外接程序中创建更新事件的处理程序
为 SharePoint 外接程序的更新事件创建并使用处理程序。
## 为外接程序更新事件创建处理程序的先决条件
<a name="Prerequisites"> </a>

全面了解 [处理加载项事件](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)和 [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)以及其中列出的先决条件和核心概念。
  
    
    

## 创建 UpgradedEventEndpoint 接收器
<a name="UpgradedEventEndpoint"> </a>


> **注释**
> **版本编号系统：**为了保持一致，本主题假设外接程序的版本号为 1.0.0.0、2.0.0.0、3.0.0.0 等。但是，无论您的编号系统是什么，逻辑和指南都适用。 
  
    
    

对于自定义更新逻辑，您可以创建处理外接程序更新事件的 SharePoint 远程事件接收器。请慎用此技术。仅在无法使用任何其他方式完成更新步骤时使用。此外，存在于 [处理加载项事件](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)中的指南适用于外接程序更新事件，同样也适用于外接程序安装事件和外接程序卸载事件。这包括：
  
    
    

- 您的处理程序必须在 30 秒内完成并将取消或继续状态返回到 SharePoint。否则，SharePoint 将至多三次重新执行对处理程序的调用。
    
  
- 如果您的处理程序返回取消状态，则 SharePoint 将最多重试三次。 
    
  
- 您通常需要将回滚和"已完成"逻辑包含在您的处理程序中。
    
  
 **UpgradedEventEndpoint** 处理程序有用的一种情况是当将计算字段添加到远程数据库的情况。假设添加了一个"城市"列，并且其值从现有的"邮政编码"列计算得出。 **UpgradedEventEndpoint** 处理程序中的代码可以循环访问数据库中的现有项目，并根据"邮政编码"字段的值和某些将邮政编码映射到城市的外部数据源为新的"城市"字段填写值。数据库架构本身的更改最好通过使用数据库平台的最佳实践在 **UpgradedEventEndpoint** 处理程序外部实现。
  
    
    
有关如何为外接程序事件创建处理程序的详细信息，请参阅 [处理 SharePoint 外接程序中的事件](handle-events-in-sharepoint-add-ins.md)和 [在 SharePoint 外接程序中创建外接程序事件接收器](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)。以下过程假设您已经将外接程序事件接收器项添加到 Visual Studio 的 SharePoint 外接程序项目中。 
  
    
    

### 首次处理外接程序更新事件


1. 打开 AppEventReceiver.svc.cs 文件并向  [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) 方法添加一个条件结构，该方法会测试调用处理程序的事件是否为更新事件。您的更新代码会进入此结构内部。如果您的代码需要访问 SharePoint，则可以使用 SharePoint 托管代码客户端对象模型 (CSOM) 或代表性状态传输 (REST) 接口。条件结构在方法中的位置取决于您在方法中结构化其他代码的方式。它通常是测试安装的外接程序和外接程序卸载事件的类似条件结构的对应项。它可能位于某个条件结构中（测试传递给 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) 的 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 对象的某些属性或子属性是否为 **null** 值或其他无效值）。也可能包含在 **try** 块中。下面是此结构的一个示例。 _properties_ 对象是一个 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 对象。
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

  ```

2. 若要在处理程序中使用 CSOM，添加（在条件块内） **using** 块，该块会通过调用 **TokenHelper.CreateAppEventClientContext** 方法获取 [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) 对象。为第二个参数指定 **true**，以访问外接程序 Web。指定 **false** 以访问主机 Web。如果要同时访问两者，则需要两个不同的客户端上下文对象。
    
  
3. 如果您的处理程序需要访问非 SharePoint 组件，则将该代码放到任何客户端上下文块之外。您的代码的结构应该与以下结构类似：
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, true))
    {
        // CSOM code that accesses the add-in web
    }
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, false))
    {
        // CSOM code that accesses the host web
    }
    // Other update code
}

  ```

4. 若要使用 REST 界面，您的代码会使用 **TokenHelper** 类中的其他方法获取访问令牌，之后，该令牌会包含在向 SharePoint 发出的请求中。有关详细信息，请参阅 [使用 SharePoint 2013 REST 终结点完成基本操作](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)。您的代码的结构应该与以下结构类似。
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
    if (contextTokenString != null)
    {
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                                                                                                                  Request.Url.Authority);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                                   .AccessToken;

       // REST code that accesses SharePoint
    }  
    // Other update code
}

  ```

5. 若要访问 SharePoint，您的 REST 代码还需要知道主机 Web 和/或外接程序 Web 的 URL。这些 URL 都是传递给 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) 方法的 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 对象的子属性。以下代码显示如何获取它们。
    
  ```cs
  
Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
  ```

第二次（或第三次等）更新外接程序时，可能需要确保某些更新逻辑不能在同一外接程序实例中运行多次。以下过程显示了如何操作。
  
    
    

### 处理后续更新中的外接程序更新事件


1. 打开 AppEventReceiver.svc.cs 文件，并找到在首次更新（从 1.0.0.0 到 2.0.0.0）中实施更新操作的代码。将其称为之前的更新代码。（此代码通常位于获取客户端上下文或访问令牌的授权代码之后。）既然要再次更新（从 2.0.0.0 到 3.0.0.0），那么，在此之前的更新代码中通常会存在这样一些操作，您不希望它们在同一外接程序实例中再次运行，但是却希望它们在从未更新到 2.0.0.0 的外接程序实例（在此情况下，实例现在从 1.0.0.0 直接更新到 3.0.0.0）上运行。
    
  
2. 将之前的更新代码打包到一个条件结构中，该结构可测试外接程序实例之前的版本，并仅在结构中的代码之前未在此外接程序实例中运行的情况下执行。之前版本的实例存储在  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 对象的 [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) 子属性中。
    
  
3. 在此结构下方添加新的更新逻辑（针对从 2.0.0.0 到 3.0.0.0 的更新）。示例如下。
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

  ```

4. 对于每次后续更新，重复上述步骤。对于从 3.0.0.0 到 4.0.0.0 的更新，您的代码应该具有以下结构。
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 is here.
}

Version ver3OOO = new Version("3.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver3OOO)
{
    // Code to update from 2.0.0.0 to 3.0.0.0 (previous update code) is here.
}
// Code to update from 3.0.0.0 to 4.0.0.0 is here.

  ```


> **重要信息**
> 如果您在 **UpgradedEventEndpoint** 处理程序中为外接程序添加了组件，确保向 **InstalledEventEndpoint** 处理程序添加相同的代码，这是因为您也需要该组件包含在新安装的外接程序中。此外，应该为外接程序添加（或修改）用于删除组件的 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx)。大多数情况下，由 **InstalledEventEndpoint** 添加或更改的任何内容都应该由 **UninstallingEventEndpoint** 修改或删除。如果数据在将外接程序从第二阶段回收站中删除后仍然有用，则不应删除，这是一个例外。（除外接程序 Web 以外，由外接程序创建的网站都应视为数据。）
  
    
    


## 向处理程序添加回滚逻辑
<a name="AddRollbackLogic"> </a>

如果在更新外接程序时出现错误，SharePoint 2013 基础架构会停止更新过程并将外接程序实例及其所有组件回滚到之前版本的外接程序实例。但是，基础架构无法知道 **UpgradedEventEndpoint** 处理程序逻辑所执行的操作，因此，它有时并不能执行撤销。如果 **UpgradedEventEndpoint** 处理程序执行过程引发无法处理的异常，则几乎可以肯定应该回滚整个外接程序更新过程，但实际情况并非如此，原因在于基础架构不知道如何撤销 **UpgradedEventEndpoint** 代码可能执行的部分操作。因此， **UpgradedEventEndpoint** 代码必须执行以下操作：
  
    
    

1. 捕获所有异常。
    
  
2. 跳转到自定义回滚代码，以撤销该时间之前已经执行的操作。
    
  
3. 向 SharePoint 2013 基础架构发出应回滚整个外接程序更新的信号。
    
  
4. 将错误消息传递给基础架构。
    
  
并非 **UpgradedEventEndpoint** 执行的所有操作都需要在处理程序内明确撤销。外接程序 Web 将由基础架构回滚，所以您的代码无需撤销对外接程序 Web 的更改。但是， **UpgradedEventEndpoint** 处理程序确实需要撤销对 SharePoint 外接程序外部的主机 Web 或其他组件的更改。
  
    
    
在第二次（或第三次等）更新中，您的异常处理逻辑必须考虑这样一个事实：正在更新的外接程序实例并不一定就是上一版本。如果不是，可能会有两个或者更多更新代码块需要撤销。因此，您需要基于之前版本号的条件逻辑。以下是更新到版本 4.0.0.0 的回滚逻辑的一个示例。
  
    
    



```cs

catch (Exception e)
{ 
    // Rollback of the 3.0.0.0 to 4.0.0.0 update logic goes here.

    Version ver3OOO = new Version("3.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver3OOO)
    {
        // Rollback of the 2.0.0.0 to 3.0.0.0 update logic goes here.
    }

    Version ver2OOO = new Version("2.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver2OOO)
    {
        // Rollback of the 1.0.0.0 to 2.0.0.0 update logic goes here.
    }
}
```

错误处理的最后一个操作是为 **ProcessEvent** 方法返回到 SharePoint 2013 更新基础架构的 [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) 对象分配错误消息和取消状态。示例如下。在此代码中， _result_ 是之前已在 **ProcessEvent** 方法中声明的 **SPRemoteEventResult** 对象。
  
    
    



```cs

catch (Exception e)
{     
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}
```


> **重要信息**
> 为  [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) 属性分配 **SPRemoteEventServiceStatus.CancelWithError**（或 **SPRemoteEventServiceStatus.CancelNoError**）至关重要。此属性是让基础架构回滚更新的信号发送方。但在回滚更新之前，SharePoint 将重试三次您的处理程序。 
  
    
    


### 使用处理程序委派策略

在 [处理加载项事件](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)中描述的处理程序委派策略也可在 **UpdatedEventHandler** 中使用。捆绑和运行在远程组件上的不只有您的回滚和"已完成"逻辑，还有您的版本逻辑。策略的限制也适用此处：通常您不能将它用于更新多个远程系统。
  
    
    

## 后续步骤
<a name="Next"> </a>

返回到 [更新外接程序的主要步骤](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接转到以下文章之一，以了解如何更新 SharePoint 外接程序的下一主要组件。
  
    
    

-  [更新 SharePoint 2013 中的外接程序 Web 组件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [更新 SharePoint 2013 中的主机 Web 组件](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [更新 SharePoint 外接程序中的远程组件](update-remote-components-in-sharepoint-add-ins.md)
    
  

## 其他资源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 外接程序](update-sharepoint-add-ins.md)
    
  
-  [UpgradedEventEndpoint 元素 (PropertiesDefinition 复杂类型) (SharePoint 接清单)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)
    
  
-  [处理 SharePoint 外接程序中的事件](handle-events-in-sharepoint-add-ins.md)
    
  
-  [在 SharePoint 外接程序中创建外接程序事件接收器](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  

