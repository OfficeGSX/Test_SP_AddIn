---
title: 建立增益集事件接收器中 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: f40c910f-12a2-4caa-8e91-c7a61ae540db
---


# 建立增益集事件接收器中 SharePoint 增益集
建立處理常式的SharePoint Add-in安裝及解除安裝SharePoint Add-ins中的事件。
## 必要條件
<a name="SP15appevent_prereq"> </a>

本文前提是您必須提供者主控SharePoint Add-ins，了解與您已經開發移最少是一些超過"Hello World"層級。此外，您應該先熟悉 [處理事件中 SharePoint 增益集](handle-events-in-sharepoint-add-ins.md)。
  
    
    

## 取得更多的程式碼範例
<a name="SP15appevent_prereq"> </a>

如果您在本文中延續範例透過解決問題，您必須完成的程式碼範例。以下是一些其他的範例。它們不全部依照本文所述的架構。可以有一個以上的好方法包括工程師增益集事件接收器，並請記住也 Microsoft 的指引可以隨著時間。
  
    
    

-  [OfficeDev/PnP/Samples/Core.AppEvents.HandlerDelegation](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents.HandlerDelegation)的本文中的持續範例關閉相符項目。
    
  
-  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents)示範如何執行上述範例中案例的處理常式委派策略不能使用相同的工作。
    
  
-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)
    
  
-  [建立的裝載提供者增益集的自訂增益集安裝](https://code.msdn.microsoft.com/SharePoint-2013-Create-a-f27752e0)
    
  

## 新增增益集已安裝的事件接收器
<a name="SP15appevent_prereq"> </a>


1. 在Visual Studio，開啟的提供者主控SharePoint Add-in專案。(如果您將增益集事件處理常式新增至 SharePoint 主控增益集時， Office Developer Tools for Visual Studio轉換為提供者主控應用程式。)
    
  
2. 在 **方案總管**] 中選擇 [ SharePoint Add-in] 節點。
    
  
3. 在 [ **屬性**] 視窗中，將 **控點增益集安裝**的值設定為 **True**。
    
   **圖 1。在 [屬性] 視窗中的增益集事件**

  

     ![App events in the properties window](images/SP_VS_Properties_Window_AppEvents.PNG)
  

    Office Developer Tools for Visual Studio會執行下列動作：
    
  - 新增名為 AppEventReceiver.svc 的檔案。包含一些架構的 C# (或 VB.NET) 程式碼。這是服務會處理增益集事件。
    
  
  - 下列項目新增至 [ **屬性**] 區段中的 AppManifest.xml 檔案:  `<InstalledEventEndpoint>~remoteAppUrl/AppEventReceiver.svc</InstalledEventEndpoint>`。此項目會註冊增益集事件接收器至 SharePoint。(請注意， **~ remoteAppUrl**權杖是同一個用於提供者主控SharePoint Add-in的遠端 web 應用程式。Office Developer Tools for Visual Studio假設的 web 應用程式的網域和事件處理常式都一樣。在極罕見的情況下出不時，您需要以手動方式取代權杖 **~ remoteAppUrl**與您服務的實際的網域。)
    
  
  - 如果SharePoint Add-in專案上還沒有 web 專案、 Office Developer Tools for Visual Studio建立 web 應用程式。工具也請確認該增益集資訊清單設定裝載提供者增益集。他們也會新增頁面、 指令碼、 CSS 檔案和其他成品。(如果增益集所需的僅限遠端元件的事件處理 web 服務，可以刪除這些專案。您也應該確定增益集資訊清單中的 **StartPage**元素未指向已刪除的頁面。)
    
  
4. 如果您的測試 SharePoint 伺服器陣列不是正在執行Visual Studio同一部電腦上，設定適用於偵錯使用Microsoft Azure服務匯流排的專案。如需詳細資訊，請參閱 [偵錯及疑難排解中的 SharePoint 增益集的遠端事件接收器](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)。
    
  
5. 如果 AppEventReceiver.svc 檔案中沒有 `ProcessOneWayEvent`方法，它的實作須有剛線條 `throw new NotImplementedException();`，因為此方法不能用於增益集事件處理常式。 *增益集的事件處理常式都傳回會告知 SharePoint 是否完成或回復事件的物件並 `ProcessOneWayEvent`方法不會傳回任何項目。* 
    
  
6. 檔將包含看似下列 `ProcessEvent`方法。(那里可能也說明如何取得用戶端內容的程式碼區塊。刪除或註解。)
    
    請注意下列有關這段程式碼：
    
  -  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) 物件包含從 SharePoint，包括識別事件 [EventType](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.EventType.aspx) 屬性的內容資訊 SOAP 訊息形式傳送給您的處理常式 web 服務。
    
  
  - 您的處理常式傳回 [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) 物件包含其可能的值為 [SPRemoteEventServiceStatus](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventServiceStatus.aspx) [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) 屬性。 **Continue**、 **SPRemoteEventServiceStatus.CancelNoError**、 和 **SPRemoteEventServiceStatus.CancelWithError**。 **Status**屬性的預設值為 **Continue**，這會告訴 SharePoint，以完成該事件。其他兩個值會告知 SharePoint 以：
    
  - 更多時間的三個，執行您的處理常式。
    
  
  - 如果它仍然會取得取消狀態、 取消事件，並回復它已完成之事件的一部分的任何項目。
    
  



  ```cs
  
public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();

    return result;
}
  ```

7. 立即宣告 `result`變數列下方新增下列參數結構來識別所處理的事件。
    
  ```cs
  
switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:
        break;
    case SPRemoteEventType.AppUpgraded:
        break;
    case SPRemoteEventType.AppUninstalling:
        break;
}
  ```


    > [!注意事項]
      > **AppInstalled**、 **AppUpdated**及 **AppInstalling**事件，如果您讓這些處理常式，每個取得自己註冊增益集資訊清單中的 URL。因此您 *可以*  讓不同端點這些;但本文 (和Office Developer Tools for Visual Studio) 會假設其確實相同端點;這就是為什麼需要決定呼叫它的事件程式碼。
8. 所述 [增益集事件處理常式中包含回復邏輯和 「 已完成 」 的邏輯](handle-events-in-sharepoint-add-ins.md#Rollback)，如果發生任何錯誤安裝邏輯中，您幾乎都會想增益集取消安裝，並想要回復它已完成安裝 SharePoint 與您想要回復您處理常式已完成。新增下列程式碼內 **case** AppInstalled 事件是一種方式，來達成這些目標。
    
  ```cs
  
case SPRemoteEventType.AppInstalled:
  try
  {
      // Add-in installed event logic goes here.
  }
  catch (Exception e)
  {
      result.ErrorMessage = e.ErrorMessage;
      result.Status = SPRemoteEventServiceStatus.CancelWithError;

      // Rollback logic goes here.
  }
  break;
  ```


    > [!注意事項]
      > 移動需要超過 30 秒至本身的增益集的安裝程式碼。 您可以將其新增至執行第一次的"第一次執行"邏輯增益集的執行。增益集可顯示訊息的某個項目 like"我們 (英文) 事項備妥可供您。 」或者，增益集可以提示使用者執行初始化程式碼。> 如果"首先執行"邏輯不是合適的增益集、 另一個作法是讓您啟動遠端非同步程序的事件處理常式，然後立即傳回 **Status** [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) 物件設定為 **Continue**。這項策略的缺點是如果遠端程序失敗，具有因而告訴您將回復增益集安裝的 SharePoint。
9.  [新增在事件處理常式架構策略](handle-events-in-sharepoint-add-ins.md#Strategies)所述，處理常式委派策略是慣用，雖然可能無法在每個案例中。在繼續範例中，我們為您示範如何實作的處理常式委派策略時將清單新增至主機網頁。(如需如何建立在未使用的處理常式委派策略類似 AppInstalled 事件處理常式的資訊，請參閱範例 [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents))。
    
    以下是 AppInstalled **case**區塊的新版本。請注意上面 **switch**封鎖移初始化邏輯套用至所有的事件。由於安裝相同清單將會移除 AppUninstalling 處理常式中，那里識別清單。
    


  ```cs
  
SPRemoteEventResult result = new SPRemoteEventResult();
String listTitle = "MyList";

switch (properties.EventType)
{               
    case SPRemoteEventType.AppInstalled:
                    
   try
   {
        string error = TryCreateList(listTitle, properties);
        if (error != String.Empty)
        {
            throw new Exception(error);            
        }
   }
    catch (Exception e)
   {
        // Tell SharePoint to cancel the event.
        result.ErrorMessage = e.Message;
        result.Status = SPRemoteEventServiceStatus.CancelWithError;               
    }
        break;
    case SPRemoteEventType.AppUpgraded:
       break;
    case SPRemoteEventType.AppUninstalling:
       break;
}                      
  ```

10. 下列程式碼與 **private**當成 **AppEventReceiver**類別新增清單建立方法。請注意 `TokenHelper`類別的特殊方法最佳化取得用戶端快顯增益集事件。傳遞給 **false**最後一個參數可確保內容為主機網頁。
    
  ```cs
  
private string TryCreateList(String listTitle, SPRemoteEventProperties properties)
 {    
    string errorMessage = String.Empty;          

    using (ClientContext clientContext =
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
        }
    }
    return errorMessage;
}

  ```

11. 回復邏輯基本上是例外狀況處理邏輯與 SharePoint CSOM (用戶端物件模型) 已 [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) 可讓您委派例外處理至 SharePoint server 的 web 服務。(請參閱也、 [How to: Use Exception Handling Scope](http://msdn.microsoft.com/library/103619ef-1ba3-44e3-93e1-5e0685bc616e%28Office.15%29.aspx))。將下列程式碼新增至上述的程式碼片段的 **if**區塊。
    
  ```cs
  
ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext);

using (scope.StartScope()) 
{ 
    using (scope.StartTry()) 
    { 
    }         
    using (scope.StartCatch()) 
    {                                 
    } 
    using (scope.StartFinally()) 
    { 
    } 
} 
 clientContext.ExecuteQuery();

if (scope.HasException)
{
    errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
        scope.ServerErrorTypeName, scope.ErrorMessage, 
        scope.ServerErrorDetails, scope.ServerErrorValue, 
        scope.ServerStackTrace, scope.ServerErrorCode);
}
  ```

12. 只有一個通話至 SharePoint ( **ExecuteQuery**) 前面的程式碼片段，但然而我們不太運用僅有一個。將要要參照在我們的例外狀況範圍中每個物件具有要先載入給用戶端。因此，新增下列程式碼 *上方*  的 **ExceptionHandlingScope**建構函式。
    
  ```cs
  
ListCollection allLists = clientContext.Web.Lists;
IEnumerable<List> matchingLists =
    clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
clientContext.ExecuteQuery();

var foundList = matchingLists.FirstOrDefault();
 List createdList = null;
  ```

13. 若要建立的主機 web 清單的程式碼就會進入 [StartTry](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartTry.aspx) 區塊中，但是程式碼必須先檢查是否清單已新增 (如下所述 [增益集事件處理常式中包含回復邏輯和 「 已完成 」 的邏輯](handle-events-in-sharepoint-add-ins.md#Rollback))。如果-加上 then-人邏輯使用 [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) 類別可委派給 SharePoint 伺服器。(請參閱也、 [How to: Use Conditional Scope](http://msdn.microsoft.com/library/560112e9-c3ed-4b8f-9cd4-c8bc5d60d63c%28Office.15%29.aspx))。新增下列程式碼內的 **StartTry**區塊。
    
  ```cs
  
ConditionalScope condScope = new ConditionalScope(clientContext,
        () => foundList.ServerObjectIsNull.Value == true, true);
using (condScope.StartScope())
{
    ListCreationInformation listInfo = new ListCreationInformation();
    listInfo.Title = listTitle;
    listInfo.TemplateType = (int)ListTemplateType.GenericList;
    listInfo.Url = listTitle;
    createdList = clientContext.Web.Lists.Add(listInfo);                                
}
  ```

14.  [StartCatch](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartCatch.aspx) 區塊應復原] 清單中，建立但它必須先檢查已建立的清單，因為可能會有已擲回例外狀況 **StartTry**區塊中之前其完成建立清單。將下列程式碼新增至 **StartCatch**區塊。
    
  ```cs
  
ConditionalScope condScope = new ConditionalScope(clientContext,
        () => createdList.ServerObjectIsNull.Value != true, true);
using (condScope.StartScope())
{
    createdList.DeleteObject();
} 
  ```


    > [!秘訣]
      > **疑難排解:**若要測試是否 **StartCatch**區塊輸入時應該是您需要例外狀況 runtime SharePoint 伺服器上的方式。使用 **throw**或除以零將無法運作因為它們造成 *用戶端*  例外日期之前的用戶端執行階段可以即使安裝程式碼將彙整及 (使用 **ExecuteQuery**方法) 傳送至伺服器。而是將下列幾行新增至 **StartTry**區塊。用戶端執行階段接受，但它會使伺服器端的例外狀況，這是您想要。>  `List fakeList = clientContext.Web.Lists.GetByTitle("NoSuchList");`
  
    
    
 `clientContext.Load(fakeList);`

    整個 TryCreateList 方法應該如下所示。(  [StartFinally](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartFinally.aspx) 區塊時需要甚至是無法使用。)
    


  ```cs
  
private string TryCreateList(String listTitle, SPRemoteEventProperties properties)
{    
    string errorMessage = String.Empty;  

    using (ClientContext clientContext = 
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
            ListCollection allLists = clientContext.Web.Lists;
            IEnumerable<List> matchingLists = 
                clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
            clientContext.ExecuteQuery();
            var foundList = matchingLists.FirstOrDefault();
            List createdList = null;

            ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext); 
            using (scope.StartScope()) 
            { 
                using (scope.StartTry()) 
                { 
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                            () => foundList.ServerObjectIsNull.Value == true, true);  
                    using (condScope.StartScope())
                    {
                        ListCreationInformation listInfo = new ListCreationInformation();
                        listInfo.Title = listTitle;
                        listInfo.TemplateType = (int)ListTemplateType.GenericList;
                        listInfo.Url = listTitle;
                        createdList = clientContext.Web.Lists.Add(listInfo);
                    }
                } 
                
                using (scope.StartCatch()) 
                { 
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                            () => createdList.ServerObjectIsNull.Value != true, true);
                    using (condScope.StartScope())
                    {
                        createdList.DeleteObject();
                    }    
                } 

                using (scope.StartFinally()) 
                { 
                } 
            } 
            clientContext.ExecuteQuery();

            if (scope.HasException)
            {
                    errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
                    scope.ServerErrorTypeName, scope.ErrorMessage, 
                    scope.ServerErrorDetails, scope.ServerErrorValue, 
                    scope.ServerStackTrace, scope.ServerErrorCode);
            }
        }
    }
    return errorMessage;
}
  ```


    > [!秘訣]
      > **偵錯:**無論您使用處理常式委派策略，當您逐步執行程式碼與偵錯工具，請記住在您的處理常式會傳回取消狀態任何情況下，SharePoint 將要呼叫您的處理常式一次，最多三個更多時間。所以偵錯工具會循環程式碼最多四倍。

    > [!秘訣]
      > **程式碼架構：**由於您可以使用宣告式標記增益集在 web 上安裝元件外您處理常式，通常不會想要使用任何您處理常式具有可互動的增益集 web 30 秒。但如果您執行動作，請記住您的程式碼的增益集由 web 需要個別 [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) 物件。這表示增益集網頁伺服器和主機網頁的不同元件剛儘 SQL Server 資料庫已與這些項目的不同。讓增益集 web 通話處於 AppInstalled **case**封鎖 **try**封鎖的方法就像延續範例將 TryCreateList 方法。不過，您的處理常式會執行 *不*  需要回復增益集在 web 上採取的動作。如果遇到錯誤，它只需要來取消事件，因為 SharePoint 會刪除整個增益集網頁如果取消事件。

## 建立增益集 uninstalling 事件接收器
<a name="SP15appevent_prereq"> </a>


1. 將專案的 **控點增益集解除安裝**屬性設定為 **True**。工具執行 *不*  建立另一個 web 服務檔案，如果已經有;但他們將 **UninstallingEventEndpoint**元素新增至增益集資訊清單。
    
  
2. AppUninstalling **case**區塊中的程式碼應該移除的增益集不需要之後的增益集已從第二階段資源回收筒，這是什麼會觸發事件的成品。不過，盡可能需要"淘汰"元件而不是完全中予以刪除。這是因為您需要回復 uninstalling 事件是否還原它們。如果發生的問題，增益集仍在第二階段資源回收筒中，且使用者無法將其還原並開始重新使用。純粹重新建立回復邏輯中的已刪除的元件可能足夠讓增益集運作一次，但任何資料或元件中的組態設定將會遺失。
    
    這項策略是較容易 SharePoint 元件，自從 SharePoint 已經從中可以還原事項，且 CSOM Api 存取其資源回收筒。稍後的步驟此程序顯示的方式。其他平台，可能會需要不同的技術。例如如果您想要淘汰您增益集 uninstalling 處理常式中的 SQL Server 表格列、 處理常式中的 T-SQL 預存程序可將 IsDeleted 欄新增至表格和將它設為 **True**列。如果程序遇到錯誤，回復邏輯值會重設為 **False**。此程序完成時不會發生錯誤，然後只會傳回成功旗標之前它可以設定以稍後刪除列的計時器工作。
    
    有時您想要保留的資料，例如清單、 甚至是增益集在刪除後;但本文例如，以下是解除安裝的事件處理常式會建立具有已安裝的事件處理常式清單中刪除。
    


  ```cs
  
case SPRemoteEventType.AppUninstalling:

try
{
    string error = TryRecycleList(listTitle, properties);
    if (error != String.Empty)
    {
        throw new Exception(error);
    }
}
catch (Exception e)
{
    // Tell SharePoint to cancel the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
break;
  ```

3. 新增回收清單的 helper 方法。請注意下列有關這段程式碼：
    
  - 程式碼回收清單中的，而不是永久刪除它。這會使可能還原，包括其資料，如果事件失敗，這就是 **StartCatch**區塊的用途。因此，如果方法成功，該事件完成的增益集從第二階段資源回收筒中，會永久刪除但清單仍處於第一階段資源回收筒。
    
  
  - 程式碼會測試清單存在回收之前建立其因為使用者可能會有已回收其 SharePoint 使用者介面中。機器，回復程式碼會檢查資源回收筒中的清單存在再加以還原，因為使用者可能會有已還原其或移至第二階段資源回收筒。
    
  
  - 有兩個測試以查看它的參照是否 **null**檢查清單存在的設定格式化的條件範圍。但是這兩個它們已測試 nullity 非常相同物件的第二個時間 inner **if**區塊。使用設定格式化的條件的範圍區塊的外圍測試執行的伺服器上，但也需要 inner nullity 測試。這是因為用戶端執行階段透過程式碼行逐列移動建立 **ExecuteQuery**方法將會傳送到伺服器 XML 訊息。 **foundList**和 **recycledList**物件參照已達到時，下列一或另一個這些行會擲回 Null 參考例外狀況除非它們 encased 內 inner nullity 檢查。
    
  

  ```cs
  
private string TryRecycleList(String listTitle, SPRemoteEventProperties properties)
{
    string errorMessage = String.Empty;

    using (ClientContext clientContext = 
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
            ListCollection allLists = clientContext.Web.Lists;
            IEnumerable<List> matchingLists = 
                clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
            RecycleBinItemCollection bin = clientContext.Web.RecycleBin;
            IEnumerable<RecycleBinItem> matchingRecycleBinItems = 
                clientContext.LoadQuery(bin.Where(item => item.Title == listTitle));        
            clientContext.ExecuteQuery();

            List foundList = matchingLists.FirstOrDefault();
            RecycleBinItem recycledList = matchingRecycleBinItems.FirstOrDefault();    

            ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext);
            using (scope.StartScope())
            {
                using (scope.StartTry())
                {
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                        () => foundList.ServerObjectIsNull.Value == false, true);
                    using (condScope.StartScope())
                    {
                        if (foundList != null)
                        {
                            foundList.Recycle();
                        }
                    }
                }
                using (scope.StartCatch())
                {
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                         () => recycledList.ServerObjectIsNull.Value == false, true);
                    using (condScope.StartScope())
                    {
                        if (recycledList != null)
                        {
                            recycledList.Restore(); 
                        }
                    }
                }
                using (scope.StartFinally())
                {
                }
            }
            clientContext.ExecuteQuery();

            if (scope.HasException)
            {
                errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
                    scope.ServerErrorTypeName, scope.ErrorMessage, 
                    scope.ServerErrorDetails, scope.ServerErrorValue, 
                    scope.ServerStackTrace, scope.ServerErrorCode);
            }
        }
    }
    return errorMessage;
}
  ```


### 偵錯及測試增益集 uninstalling 事件接收器


1. 開啟之所有下列頁面中個別視窗或] 索引標籤：
    
  - **網站內容**
    
  
  - **網站設定-資源回收筒**() _layouts/15/AdminRecycleBin.aspx?ql=1
    
  
  - **資源回收筒-第二階段資源回收筒** (_layouts/15/AdminRecycleBin.aspxView=2 &amp; ?ql = 1)
    
  
2. 按 F5 與信任的增益集時出現提示。增益集開始頁面隨即開啟。若僅要測試的解除安裝處理常式，您可以關閉此瀏覽器視窗。  *但如果您正在偵錯處理常式，它保持在開啟狀態。會在關閉將會結束偵錯工作階段。* 
    
  
3. 重新整理 [ **網站內容**] 頁面上，當增益集出現，請加以移除。
    
  
4. 重新整理 [ **網站設定-資源回收筒**] 頁面。增益集將會顯示為最上層項目。選取它旁邊的核取方塊並按一下 [ **刪除選取項目**。
    
  
5. 重新整理 [ **資源回收筒-第二階段資源回收筒**] 頁面。增益集將會顯示為最上層項目。選取其旁邊的核取方塊並按一下 [ **刪除選取項目**。SharePoint 會立即呼叫您增益集 uninstalling 處理常式。
    
  

## 建立增益集更新的事件接收器
<a name="SP15appevent_prereq"> </a>

如需建立增益集更新處理常式的詳細資訊，請參閱 [建立更新事件處理常式中 SharePoint 增益集](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)。
  
    
    

## URL 與裝載實際執行增益集事件接收器的限制
<a name="SP15appevent_prereq"> </a>

增益集事件接收器可以裝載在雲端或未也使用為 SharePoint server 的內部伺服器。實際執行收件者的 URL 不能指定特定的連接埠。這表示您必須使用其中一個連接埠 443 HTTPS，我們建議，或連接埠 80 http。 如果您使用 HTTPS，而接收者服務裝載內部部署，但增益集位於Microsoft SharePoint Online，然後裝載伺服器必須具備公開信任的憑證從憑證授權單位。(只有當增益集是內部部署 SharePoint 伺服器陣列中運作方式的自我簽署的憑證)。
  
    
    

## 其他資源
<a name="SP15appevent_addlresources"> </a>


-  [處理事件中 SharePoint 增益集](handle-events-in-sharepoint-add-ins.md)
    
  

