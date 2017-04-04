---
title: 建立更新事件處理常式中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# 建立更新事件處理常式中 SharePoint 增益集
建立並使用SharePoint Add-in更新事件處理常式。
## 建立增益集更新事件處理常式的必要條件
<a name="Prerequisites"> </a>

先徹底熟悉同時 [處理增益集事件](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)及 [更新 SharePoint 增益集](update-sharepoint-add-ins.md)和必要條件和列在他們的核心概念。
  
    
    

## 建立 UpgradedEventEndpoint 接收器
<a name="UpgradedEventEndpoint"> </a>


> [!注意事項]
> **版本編號系統:**一致性，如本主題假設的增益集版本號碼是 1.0.0.0、 2.0.0.0、 3.0.0.0，依此類推。不過的邏輯和指引適用於編號系統的是中。
  
    
    

自訂更新邏輯，您可以建立的 SharePoint 遠端事件接收器該增益集的控點 updated 的事件。您應該先傳統中使用此技術。使用它只更新無法完成任何其他方法的步驟。此外， [處理增益集事件](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)中找到的指引適用於增益集更新事件做為增益集已安裝及增益集解除安裝事件更加。這包括：
  
    
    

- 您的處理常式已完成並返回 [取消] 或 [繼續] 狀態 SharePoint 30 秒。如果沒有，SharePoint 會一次呼叫的處理常式，最多三個更多時間。
    
  
- 如果您的處理常式會傳回取消狀態，SharePoint 會重試最多三個更多時間。
    
  
- 您通常需要回復和 「 已完成 」 的邏輯納入您處理常式。
    
  
計算的欄位新增至遠端資料庫時的 **UpgradedEventEndpoint**處理常式很有用的其中一個案例。假設加縣/市] 欄，且其值計算從現有的 [郵遞區號] 欄位。您的程式碼中的 **UpgradedEventEndpoint**處理常式無法逐一查看現有的資料庫中的項目並填入郵遞區號欄位及對應到城市 (英文) 的郵遞區號有外部資料來源的值為基礎的新城市欄位的值。使用資料庫平台的最佳作法本身的資料庫結構描述變更最佳可以完成外 **UpgradedEventEndpoint**處理常式。
  
    
    
如需如何建立事件處理常式的增益集的詳細資訊，請參閱 [處理事件中 SharePoint 增益集](handle-events-in-sharepoint-add-ins.md)和 [建立增益集事件接收器中 SharePoint 增益集](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)。下列程序假設您已在Visual Studio您SharePoint Add-in專案新增增益集事件接收器項目。
  
    
    

### 增益集處理事件第一次更新


1. 開啟 AppEventReceiver.svc.cs 檔案並新增條件化結構以測試更新的事件處理常式叫用事件是否 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) 方法。更新程式碼會內此結構。如果您的程式碼需要存取 SharePoint，您可以使用 SharePoint managed 程式碼用戶端物件模型 (CSOM) 或代表性狀態傳輸 (REST) 介面。設定格式化的條件結構方法中的位置方法中的其他程式碼的結構方式而定。通常是對等的類似的設定格式化的條件結構的測試已安裝的增益集及增益集 uninstalling 事件。它可能會測試特定的屬性或子屬性會傳遞至 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) **null**值或其他無效值 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 物件的設定格式化的條件結構內。它也可能 **try**區塊中。以下是結構的範例。 _properties_物件是 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 物件。
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

  ```

2. 若要使用 CSOM 的處理常式中，新增 (內的設定格式化的條件區塊) **using**區塊的呼叫 **TokenHelper.CreateAppEventClientContext**方法來取得 [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) 物件。指定 **true**存取增益集的 web 第二個參數。指定 **false**來存取主應用程式 web。如果您需要存取兩者，您將需要兩個不同的用戶端快顯物件。
    
  
3. 如果您的處理常式需要存取非 SharePoint 元件，放入任何用戶端快顯封鎖外部該程式碼。您的程式碼應該同樣結構的下列：
    
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

4. 若要使用的 REST 介面，您的程式碼會使用其他方法來取得存取 token，然後對 SharePoint 進行要求中包含 **TokenHelper**類別中。如需詳細資訊，請參閱 [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)。您的程式碼應該的下列同樣的結構。
    
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

5. 若要存取 SharePoint、 REST 程式碼也必須知道主機 web 或增益集網頁或兩者的 URL。這些 Url 皆會傳遞給 [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) 方法 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 物件的兩個子屬性。下列程式碼示範如何取得它們。
    
  ```cs
  
Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
  ```

當您更新用於秒 (或第三個、 等等) 增益集的時間時，您可能需要確定某些更新邏輯不會執行多次相同的增益集執行個體上。下列程序顯示您如何。
  
    
    

### 若要處理之增益集更新的事件在後續的更新


1. 開啟 AppEventReceiver.svc.cs 檔案，並找出在第一次更新中實作 update 動作的程式碼 (從 1.0.0.0。 為 2.0.0.0)。讓我們先呼叫這個方法先前的更新程式碼。(這段程式碼是通常位於之後會取得用戶端內容或存取權杖的授權程式碼)。既然您要更新一次 (從至 3.0.0.0 2.0.0.0)、 通常會在上一個更新程式碼此處不想要在相同增益集的執行個體，重新執行的動作但不要想要新增的執行個體上執行永遠不更新為 2.0.0.0 (在此情況下，現在要更新之執行個體從 1.0.0.0。 至 3.0.0.0)。
    
  
2. 測試增益集執行個體的先前版本並執行只有結構中的程式碼不具有之前執行此增益集執行個體上的設定格式化的條件結構內包裝的舊的更新程式碼。舊版的執行個體都會儲存在 [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 物件的 [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) 子屬性。
    
  
3. 新增您新更新的邏輯 (適用於從更新 2.0.0.0 至 3.0.0.0) 低於此結構。以下是範例。
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

  ```

4. 每個後續的更新，請重複這些步驟。從更新 3.0.0.0 至 4.0.0.0、 程式碼應有下列結構。
    
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


> [!重要]
> 如果您將元件新增至 [增益集之 **UpgradedEventEndpoint**處理常式中時，請務必將相同的程式碼新增至 **InstalledEventEndpoint**處理常式因為您要包含在增益集，以及全新安裝的元件。此外，您應該新增 [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) (或修改其) 增益集元件中移除。大致上，已新增或變更的 **InstalledEventEndpoint**的任何項目必須回復或刪除 **UninstallingEventEndpoint**。有一個例外是不刪除增益集已從第二階段資源回收筒之後仍然有用的資料。(網站以外的增益集 web 所建立的增益集應該會視為 data。)
  
    
    


## 將回復邏輯新增至的處理常式
<a name="AddRollbackLogic"> </a>

如果發生錯誤的更新增益集時， SharePoint 2013基礎結構更新程序停止並回復增益集執行個體及其所有元件為舊版本的增益集執行個體。但基礎結構可以沒有辦法知道您 **UpgradedEventEndpoint**處理常式邏輯的不，讓它永遠無法復原它。未處理的例外狀況 **UpgradedEventEndpoint**處理常式會執行時幾乎都表示整個增益集更新程序應復原，但它不會因為不知道基礎結構如何復原某些 **UpgradedEventEndpoint**程式碼可能已完成的事項。此原因而必須 **UpgradedEventEndpoint**程式碼：
  
    
    

1. 捕捉所有例外狀況。
    
  
2. 若要復原內容它具有已完成該點的自訂回復程式碼的分支。
    
  
3. 應復原整個增益集更新SharePoint 2013基礎結構的訊號。
    
  
4. 在錯誤訊息傳遞至基礎結構。
    
  
不每個項目 **UpgradedEventEndpoint**您沒有明確地回復內的處理常式的需求。增益集 web 將要回復由基礎結構，因此不需要復原增益集 web 變更您的程式碼。但 **UpgradedEventEndpoint**處理常式通常沒有反向它所做的變更主機網路或外部SharePoint Add-in的其他元件。
  
    
    
上的第二個 (或第三和等等) 更新您的例外狀況處理邏輯具有要採取的增益集執行個體正在更新是不一定是立即舊版的帳戶。如果不是，可能會有兩個或多個需要回復更新程式碼區塊。此原因而您需要為基礎的舊的版本數目的條件式邏輯。以下是版本 4.0.0.0 更新的回復邏輯的範例。
  
    
    



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

錯誤處理應進行的最後一個工作已指派給 [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) **ProcessEvent**方法所傳回物件SharePoint 2013更新基礎結構的錯誤訊息和 [取消] 狀態。以下是範例。在這段程式碼、  _result_是稍早在 **ProcessEvent**方法中所宣告的 **SPRemoteEventResult**物件。
  
    
    



```cs

catch (Exception e)
{     
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}
```


> [!重要]
> 將 **SPRemoteEventServiceStatus.CancelWithError** (或 **SPRemoteEventServiceStatus.CancelNoError**) 指派給 [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) 屬性很重要。此屬性是什麼訊號回復更新基礎結構。但 SharePoint 會重試您處理常式三次再回復更新。
  
    
    


### 使用的處理常式委派策略

說明 [處理增益集事件](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents)處理常式委派策略可供 **UpdatedEventHandler**太。 是您回復和 「 已完成"邏輯於和遠端元件上執行不僅版本設定邏輯太。限制的策略套用以下太: 您通常不能使用它來更新一個以上的遠端系統。
  
    
    

## 後續步驟
<a name="Next"> </a>

返回 [在增益集更新的主要步驟](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)，或直接移至其中的下列文章，以了解如何更新您SharePoint Add-in的下一個主要元件。
  
    
    

-  [更新 SharePoint 2013 中的增益集 web 元件](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [更新 SharePoint 2013 中的主機 web 元件](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [更新遠端元件的 SharePoint 增益集](update-remote-components-in-sharepoint-add-ins.md)
    
  

## 其他資源
<a name="bk_addresources"> </a>


-  [更新 SharePoint 增益集](update-sharepoint-add-ins.md)
    
  
-  [UpgradedEventEndpoint 元素 (PropertiesDefinition complexType) (SharePoint 增益集資訊清單)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)
    
  
-  [處理事件中 SharePoint 增益集](handle-events-in-sharepoint-add-ins.md)
    
  
-  [建立增益集事件接收器中 SharePoint 增益集](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  

