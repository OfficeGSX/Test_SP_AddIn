---
title: 處理事件中 SharePoint 增益集
keywords: vs.sharepointtools.spe.remoteeventreceiver
f1_keywords:
- vs.sharepointtools.spe.remoteeventreceiver
ms.prod: SHAREPOINT
ms.assetid: c050d056-8548-4496-a053-016779d723d9
---


# 處理事件中 SharePoint 增益集

您的自訂程式碼可以處理事件的提供者主控增益集的三種類別：
  
    
    


- **清單中事件**，例如新增或刪除的網站上的清單。
    
  
- **清單項目事件**，例如編輯清單中的項目。
    
  
- **增益集事件**，例如增益集的安裝。
    
  

SharePoint 主控SharePoint Add-ins不支援事件處理，但是您可以將清單或清單項目事件處理常式的一種藉由設定事件觸發工作流程中開啟工作流程。查看 [在 SharePoint 2013 中的工作流程](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx)。無法由增益集事件觸發工作流程，讓增益集事件無法處理的 SharePoint 裝載增益集。
  
    
    


> **注意事項**
> SharePoint Add-ins中不支援網站事件及網站集合的事件。
  
    
    

有兩種類型的事件：
- ** *Before*  events** 之前SharePoint基礎結構沒有任何觸發它是自己處理 （包括認可變更內容資料庫） 的事件。 在SharePoint **自訂前的事件處理常式一律執行同步** 。與其他用途之用，因此可用來取消事件。例如，如果增益集已刪除清單的函數，清單中刪除事件處理常式可以取消刪除如果不符合特定條件。如果事件的事件順序的一部分，取消它會防止更新事件的任何所有發生。例如，如果在事件處理常式的 **ItemAdding**取消事件，就不會觸發 **ItemAdded**事件通常是稍後而言。
    
  
- ** *後*  事件** 之後SharePoint基礎結構沒有任何觸發它是自己處理此事件。 在SharePoint、 **遠端之後以非同步方式的清單及清單項目事件的事件處理常式，一律執行** 。（應用程式事件會發生例外狀況）。與其他用途之用，他們可以用來登事件。
    
  

## 處理清單和清單項目的事件
<a name="RER"> </a>

處理清單和清單項目的事件，您會建立遠端事件接收器 (RERs) 是執行SharePoint伺服器陣列或SharePoint Online的外部 web 服務。它會處理事件的註冊 RER 服務的 URL。有兩種方式可以將登錄處理常式：
  
    
    

- 使用 CSOM （用戶端物件模型） 或 SharePoint REST API 以程式設計方式登錄中的主機網站事件。這項工作通常是在"首先執行"邏輯增益集或事件處理常式的增益集。（參閱 [處理增益集事件](#HandlingAppEvents)中的增益集事件概觀的文章稍後）。針對以程式設計方式登錄清單事件程式碼範例中，查看 [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)。
    
  
- 在增益集 web 事件通常被登錄中具有一些簡單的 XML 標記的增益集 」 網站的功能。如何建立標記和服務的詳細資料會 [在 SharePoint 增益集建立的遠端事件接收器](create-a-remote-event-receiver-in-sharepoint-add-ins.md)中。它也可以程式設計方式註冊增益集網頁事件。
    
  

> **注意事項**
> RERs 伺服器陣列解決方案; 中具有相同的目的為事件接收器但是事件接收器造成SharePoint在伺服器執行，因此無法用於SharePoint Add-ins的自訂程式碼。
  
    
    

您的增益集可以控制下列清單和文件庫事件。 事件結束中 「 傳送 」 （同步） 事件之前，在 「 ed"結束這些是 （非同步） 事件之後。
  
    
    

|||
|:-----|:-----|
|ListAdding <br/> |ListAdded <br/> |
|ListDeleting <br/> |ListDeleted <br/> |
|FieldAdding <br/> |FieldAdded <br/> |
|FieldDeleting <br/> |FieldDeleted <br/> |
|FieldUpdating <br/> |FieldUpdated <br/> |
   
欄位更新事件所需變更清單上，例如是否可排序，不需變更的欄位中資料欄位 （欄） 的屬性。
  
    
    
您的增益集可以處理下列清單中項目的事件。
  
    
    

|||
|:-----|:-----|
|ItemAdding <br/> |ItemAdded <br/> |
|ItemUpdating <br/> |ItemUpdated <br/> |
|ItemDeleting <br/> |ItemDeleted <br/> |
|ItemCheckingOut <br/> |ItemCheckedOut <br/> |
|ItemCheckingIn <br/> |ItemCheckedIn <br/> |
|ItemUncheckingOut <br/> |ItemUncheckedOut <br/> |
|ItemAttachmentAdding <br/> |ItemAttachmentAdded <br/> |
|ItemAttachmentDeleting <br/> |ItemAtttachmentDeleted <br/> |
|ItemFileMoving <br/> |ItemFileMoved <br/> |
|ItemVersionDeleting* <br/> |ItemVersonDeleted* <br/> |
||ItemFileConverted <br/> |
   

> **注意事項**
> * 這兩個新事件可能會無法使用Visual Studio UI 中。若未、 挑選 ItemDeleting 或 ItemDeleted，然後手動變更名稱。
  
    
    

當您正在中Visual Studio、 工作及SharePoint Add-in專案中加入 RER 時、 Office Developer Tools for Visual Studio執行下列動作：
  
    
    

- Web 服務檔案，例如 RemoteEventReceiver1.svc，會新增至 web 應用程式來處理您指定當您新增到SharePoint Add-in的遠端事件接收器的事件。Web 服務包含要處理的遠端事件程式碼檔案。
    
    建立遠端事件接收器之後，您會新增至 web 應用程式服務以處理事件程式碼檔案的程式碼。根據預設，此程式碼檔案會包含兩種方法即是新增處理程式碼：
    
  - "before"（例如中先前文章中的表格中的左欄） 的事件處理 `ProcessEvent()`並且會傳回物件是否應該取消事件或讓它繼續在報表的 sharepoint。
    
  
  - "after"事件 `ProcessOneWayEvent()`控點。它會以非同步方式執行並不會傳回任何項目至 SharePoint。
    
  

    已註冊的事件發生時，SharePoint 會適當的方法呼叫中您的服務，並提供一些內容資訊的程式碼物件。例如，識別 （從先前文章中的兩個表格） 的事件類型，使您的程式碼可以分支到適用於該事件的邏輯。
    
  
- 遠端事件接收器的專案項目新增至SharePoint Add-in專案。遠端事件接收器的 Elements.xml 檔案參考中的 web 應用程式及遠端事件所指定的 web 服務。下列範例顯示會處理在加入或刪除清單項目的 Elements.xml 檔案。
    
  ```XML
  
<?xml version="1.0" encoding="utf-8"?>
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <Receivers ListTemplateId="104">
      <Receiver>
        <Name>RemoteEventReceiver1ItemAdding</Name>
        <Type>ItemAdding</Type>
        <SequenceNumber>10000</SequenceNumber>
        <Url>~remoteAppUrl/RemoteEventReceiver1.svc</Url>
      </Receiver>
      <Receiver>
        <Name>RemoteEventReceiver1ItemDeleting</Name>
        <Type>ItemDeleting</Type>
        <SequenceNumber>10000</SequenceNumber>
        <Url>~remoteAppUrl/RemoteEventReceiver1.svc</Url>
      </Receiver>
  </Receivers>
</Elements>
  ```

若要變更之事件的遠端事件接收器控點，開啟 **方案總管中**開啟的 [ **屬性**] 視窗的遠端事件接收器，展開 [ **SharePoint 事件**] 節點並接著設定想要以 **True**處理事件。
  
    
    

> **注意事項**
> 如需 RERs，包括一些疑難排解的資訊，請參閱 ＜ [常見問題集的遠端事件接收器](handle-events-in-sharepoint-add-ins.md#RERFAQ)。
  
    
    


## 處理增益集事件
<a name="HandlingAppEvents"> </a>

增益集事件也處理遠端 web 服務，但他們設定不同增益集套件的清單及清單項目 RERs，因此視為元件的個別類別。事件增益集，增益集資訊清單、 不在增益集 web 功能中註冊的遠端 web 服務。增益集即使沒有讓增益集網頁。下列各節所述有三個增益集事件。
  
    
    

### AppInstalled 事件

 **AppInstalled**事件執行之後立即 SharePoint 已完成執行增益集在安裝時，所需的所有項目之前完成安裝後會通知使用者。雖然這是 *之後*  事件時，SharePoint 您處理常式會執行 *同步*  。增益集就不可以使用直到完成您的處理常式與您的處理常式可以取消安裝 （這會導致 SharePoint 回復它已完成安裝的一部分的所有項目）。事實上，即捕捉您處理常式中的任何錯誤和指示回復安裝 SharePoint 的最佳作法。如需詳細資訊，請參閱 [增益集事件處理常式中包含回復邏輯和 「 已完成 」 的邏輯](#Rollback)。
  
    
    

> **注意事項**
> 當增益集安裝以 [租用戶範圍](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)時，即已安裝的增益集目錄網站集合及事件，然後執行 AppInstalled 和僅然後。增益集是出現在多個網站租用中但事件不會為每個這些分開執行。
  
    
    

Click 取消安裝增益集，此事件可以用於其他許多目的包括：
  
    
    

- 安裝至不能以宣告安裝在主機網路功能，例如清單或子網站的主機網站的 SharePoint 元件。
    
  
- 以程式設計方式與主機 web 或增益集 （英文） 網頁登錄清單及清單項目事件處理常式。
    
  
- 設定應用程式執行個體相對起始設定。例如，您的增益集可以有會有所不同到另一個增益集的執行個體的保留設定的增益集 web 屬性包。AppInstalled 處理常式可寫入的屬性包中，值不根據，假設網站輸入 （例如小組網站或部落格網站） 的主機網站。
    
    > **注意事項**
      > 若要查看主機網路是否 AppCatalog 檢查站台是一個好方法來偵測是否增益集已安裝租用戶範圍。請參閱 [租用和部署範圍的 SharePoint 增益集](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)。
- 執行應用程式執行個體相對設定增益集的遠端 web 應用程式中，例如將表格新增至資料庫。
    
  

> **重要**
> 實作 AppInstalled 事件必須完成 30 秒內或 SharePoint 安裝基礎結構會認為具有失敗。基礎結構會重新執行事件， *然後重複您一開始的程式碼*  ，最多三個額外的時間。 之後四個逾時，SharePoint 會回復整個增益集安裝。 [增益集事件處理常式中包含回復邏輯和 「 已完成 」 的邏輯](#Rollback)中所述的下列數據完整的隱含意義。
  
    
    


### AppUninstalling 事件

 **AppUninstalling**事件 *不*  增益集已從主機網頁時執行。移除增益集只會移動增益集至使用者的資源回收筒。兩個步驟所列觸發 AppUninstalling 事件之前。首先，使用者必須從資源回收筒，將其移至第二階段資源回收筒移除增益集。其次， **使用者必須從第二階段資源回收筒移除增益集。此最後一個工作會觸發 AppUninstalling 事件** 。是同步的 AppUninstalling 事件與您可以使用它來取消解除安裝，會在第二階段資源回收筒中保留增益集。
  
    
    
此事件處理常式的主要目的是要刪除或回收事項與 AppInstalled （或 AppUpdated） 已部署處理常式。SharePoint 無法刪除下列事項，或將它們移至 [資源回收筒]，因為不知道有關，至少沒有為增益集的元件。 通常是很好的作法來移除這些事項。但不要刪除增益集已消失後仍然有有效使用年限的事項︰ 如果清單或 AppInstalled 處理常式所建立的網站仍然將要用，不將它刪除 AppUninstalling 處理常式中。
  
    
    

### AppUpgraded 事件

 **AppUpgraded**事件執行之後立即 SharePoint 已完成執行增益集更新為新的版本時所需的所有項目之前已完成更新通知使用者。像 AppInstalled 事件之後的事件，但基本上同步很捕捉錯誤和通知回復更新 SharePoint 的最佳作法。
  
    
    
此事件處理常式可以進行一些範例：
  
    
    

- 新增、 變更或移除主機 web 中的增益集 （英文） 元件。
    
  
- 請勿在增益集 web 不可能與增益集 web 功能中的宣告式更新語法的事項。例如，您無法刪除任何項目和宣告式更新標記一起，但您可以讓以程式設計方式進行 AppUpgraded 處理常式中。
    
  
- 在增益集的 web 應用程式或遠端資料庫中的應用程式執行個體相對元件進行變更。
    
  
 *詳細的指示來建立增益集的事件處理常式處於 [建立增益集事件接收器中 SharePoint 增益集](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)*  。
  
    
    

### 增益集事件處理常式中包含回復邏輯和 「 已完成 」 的邏輯
<a name="Rollback"> </a>

如果 SharePoint 遇到錯誤處理任何的三個增益集事件時，它會取消事件，並回復它與事件有關之所做的任何變更。因為您要實作的事件的部分失敗，如果您想回復，而不是繼續作業並留下事項可能損毀狀態的整個事件與此系統整合有您的增益集的事件處理常式。以下是什麼您處理常式通常會包含執行：
  
    
    

- 告訴 SharePoint 發生錯誤。在增益集事件處理 web 服務會傳回 sharepoint SOAP 訊息會有可接受值 **Continue**、 **CancelWithError**，或 **CancelWithoutError** **Status**屬性。 任一 **Cancel***狀態會告知 SharePoint 回復此事件。
    
  
- 回復什麼處理常式具有尚未完成處理常式遇到錯誤之前。SharePoint 無法通常是執行這項作業您因為不知道您的處理常式的沒有。這不是通用的規則。例如，如果取消的增益集安裝後，SharePoint 就會刪除整個增益集 web 讓它已經完成到增益集的 web 沒有點回復不要 AppInstalled 事件處理常式中。但其通常應該回復它沒有為主機 web 或遠端元件的增益集的事項。
    
  

> **注意事項**
> **AppUninstalling 事件有關的特殊附註：**上述資料點套用至其他兩個增益集事件儘 AppUninstalling 事件。例如，如果您解除安裝的事件處理常式會刪除在遠端資料庫中，資料列，然後遇到錯誤列需要還原。由於您的服務會取消訊息傳送至 SharePoint、 增益集將不會移除從資源回收筒。從該處還原並再次使用，它可能會失敗而該資料庫項目運作。> 不過，AppUninstalling 處理常式會完成 *之前*  將會移除 SharePoint 增益集從資源回收筒。如此，如果本身的 SharePoint 遇到錯誤和取消移除的需求，不是讓您復原它已完成的處理常式。
  
    
    

SharePoint 不會收到結果訊息從您的處理常式 30 秒，它會一次呼叫處理常式。它完全，提供，並會回復事件之後三重試次數 （在所有的四個嘗試）。它會呼叫處理常式，每次您的程式碼重新開始從頭開始。但通常不想要取消復原已經可以完成的事項，例如主機 web 上建立清單您處理常式及您不知道是否完成，或甚至是觸發，處理逾時之前回復邏輯。此原因的處理常式邏輯應該不採取任何動作沒有事先是否已經完成動作，除非是說過無害再次執行。
  
    
    
安裝和更新錯誤可以看到 SharePoint 使用者介面，在下圖所示。
  
    
    

**圖 1。取得安裝錯誤詳細資料。**

  
    
    

  
    
    
![查看 SharePoint 中增益集安裝錯誤的步驟。](images/1edb644e-b3d4-4a9b-b92e-4ae2c07341c2.png)
  
    
    

#### 新增在事件處理常式架構策略
<a name="Strategies"> </a>

表示 「 虛擬程式碼中，您處理常式通常應該結構像下面。如果嘗試] 區段中發生錯誤，應可叫用 Catch 及 Rollback] 區段中。（這可能會發生自動根據的語言和 framework）。
  
    
    

```

Try
    If X not already done,
        Do X.
Catch
    Send cancel message to SharePoint.
    If X not already undone,
        Undo X.

```

不過，在 web 服務中實作您回復和 「 已完成 」 的邏輯會降低處理常式。您安裝及 rollback 邏輯通常變更某些項目更或-無遠端從 web 服務，例如 SharePoint 主機 web 或後端資料庫。如果您安裝及 rollback 的程式碼會分別 Try 和 Catch 區段，然後服務進行遠端元件，個別通話通常數個這類通話中每一節。 最佳作法是通常是在遠端元件本身可以從您嘗試區段中的處理常式中呼叫的程序中實作的安裝和 rollback 邏輯。程序應該傳回成功或失敗的訊息，而且如果它報告失敗時，請在您嘗試] 區段中的程式碼會叫用 Catch 區段 （由該指令程式假設擲回例外狀況）。沒有 Catch] 區段的唯一項重點是通知 SharePoint。我們將會呼叫這個方法的處理常式委派策略。下列的虛擬程式碼說明策略：
  
    
    



```

Try
    Call the "Do X" procedure on remote platform.
    If remote platform reports failure, call Catch.
Catch
    Send cancel message to SharePoint.

```

"不要 X"，會執行程序在遠端系統上，會本身包含回復和 「 已完成 」 邏輯如下所示。
  
    
    



```

Try
    If X not already done,
        Do X.
        Set success flag to true.
Catch
    If X was done before error,
        Undo X.
    Set success flag to false.
Send
    Return success flag to the event handler.

```

例如，如果您處理常式需要 SQL Server 資料庫上採取動作，您就可以實作安裝回復邏輯會使用 [TRY-CATCH](http://msdn.microsoft.com/library/248df62a-7334-4bca-8262-235a28f4b07f%28Office.15%29.aspx)區塊 SQL Server 上安裝預存程序。搭配實作 「 已經完成 」 的邏輯的 [ELSE IF](http://msdn.microsoft.com/library/676c881f-dee1-417a-bc51-55da62398e81%28Office.15%29.aspx)區塊。
  
    
    
SharePoint 增益集模型不會提供一種方式來儲存在 SharePoint 上的自訂伺服器端程式碼和呼叫從 CSOM （用戶端物件模型）。但 CSOM 沒有提供一種方式將彙整 try catch 和 if-加上 then-其他人邏輯並將其傳送到伺服器執行。將清單新增至主機網頁會使用的處理常式委派策略的增益集事件處理常式的詳細的範例，請參閱 [建立增益集事件接收器中 SharePoint 增益集](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)。程式碼範例，請參閱 ＜  [OfficeDev/PnP/Samples/Core.AppEvents.HandlerDelegation](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents.HandlerDelegation)。
  
    
    
您一律不能使用的處理常式委派策略。 例如，當您處理常式呼叫至多個元件，例如資料庫及 SharePoint 主機 web 有機會的其中一個無法成功完成，然後其他失敗。在此案例中，如果您使用的處理常式委派策略而設計不會執行第一個元件的回復邏輯。此原因而如果您同步呼叫元件只有呼叫的最後一個可以使用的處理常式委派策略。 如果他們會以非同步方式呼叫，您無法在這些中使用的策略。在未使用的處理常式委派策略增益集事件處理常式的範例，請參閱 ＜  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents)。
  
    
    

> **秘訣**
> 如果 AppInstalled 事件失敗，SharePoint 會刪除增益集，如果有的話，與 SharePoint 如果 AppUpated 事件失敗，將會還原增益集 web 前更新狀態。此原因而您的處理常式都永遠不需要時採取增益集在 web 的復原動作。如果您的處理常式會主機網頁和增益集在 web 上執行動作，它就應該先處理增益集 web。 如此一來進行安全處理常式委派策略用於主機 web。即使增益集 web 動作成功和失敗的主機 web 動作，但沒有回復邏輯使其送未執行。
  
    
    


## 在 [增益集支援多個安全性區域中的遠端事件接收器
<a name="HandlingAppEvents"> </a>

有一些限制在您設計支援多個安全性區域且具有的遠端事件接收器增益集的方式。如需詳細資訊，請參閱知識庫文章 kb3135876 [您不能新增預設 SharePoint 存放區時使用裝載提供者增益集在 SharePoint 2013 中的非預設區域中的應用程式](https://support.microsoft.com/en-us/kb/3135876)。
  
    
    

## 常見問題集的遠端事件接收器
<a name="RERFAQ"> </a>

下列是您必須使用的遠端事件接收器的常見問題。
  
    
    

### 使用方式的遠端事件接收器與 SharePoint 2010 中的事件接收器不同？
<a name="RER_HowRERDifferentfrom2010"> </a>

在SharePoint 2010、 事件接收器處理事件的發生在 SharePoint 清單、 網站及其他 SharePoint 物件上的 SharePoint 伺服器上執行的程式碼 （完全信任或沙箱中）。在SharePoint 2013仍然存在這種事件接收器。不過， SharePoint 2013也支援在其中都會觸發此事件時執行的程式碼主控的 web 服務的 *遠端*  事件接收器。這表示如果您註冊的遠端事件接收器，您也需要告知 SharePoint 來叫用的 web 服務。在表格 1 左側 （SharePoint 解決方案） 程式碼範例會實作功能使用的事件處理常式。在右邊 (SharePoint Add-ins) 範例會實作相同的功能使用的遠端事件接收器。
  
    
    

**表 1。在 SharePoint 2010 和增益集中的遠端事件接收器比較的事件接收器的程式碼範例**


|**SharePoint 解決方案**|**SharePoint Add-ins**|
|:-----|:-----|
|
```cs

// Trigger an event when an item is added to the SharePoint list.
Public class OnPlantUpdated : SPItemEventReceiver
{
Public override void ItemAdding (SPItemEventProperties properties)
{
Properties.After.Properties.ChangedProperties.Add("Image",CreateLink(properties));
Properties.status =SPEventReceiverStatus.Continue;
}

/// When an item updates, run the following.
Public override void ItemUpdating(SPItemEventProperties properties)
{
Properties.AfterProperties.ChangedProperties.Add("Image",CreateLink9properties));
Properties.Status= SPEventReceiverStatus.Continue;
}

```

|
```cs

/* Trigger an event when an item is added to the SharePoint list*/
Public class OnPlantUpdated : IRemoteEventService
{
public SPRemoteEventResult ProcessEvent (SPRemoteEventProperties properties)
{
SPRemoteEventResult result =new SPRemoteEventResult();
If (properties.EventType == SPRemoteEventType.ItemAdding ||  
properties.EventType == SPRemoteEventType.ItemUpdating)
{

// Add code that runs when an item is added or updated.
}

```

|
   
請參閱 [新增清單項目屬性的遠端事件接收器](http://code.msdn.microsoft.com/SharePoint-2013-Add-list-2c6e71e0)完整程式碼範例。請參閱 [移轉至的遠端事件接收器的 SharePoint 事件接收器](http://channel9.msdn.com/Series/Reimagine-SharePoint-Development/Migrating-a-SharePoint-Event-Receiver-to-a-Remote-Event-Receiver)詳細的程式碼範例示範。
  
    
    

### 
<a name="RER_HowRERDifferentfrom2010"> </a>

資訊，請參閱 [SPRemoteEventType 列舉](https://msdn.microsoft.com/zh-tw/library/microsoft.sharepoint.client.eventreceivers.spremoteeventtype.aspx)
  
    
    

### 執行動作的遠端事件接收器的運作方式
<a name="RER_HowDoRERWork"> </a>

圖 1 顯示如何遠端事件接收器運作：
  
    
    

- 使用者在 SharePoint 上執行的動作 （例如編輯清單項目）。
    
  
- SharePoint 再項登錄的 web 服務。您無法執行某些作業  例如，更新清單項目屬性或更新後端系統。
    
  
- Web 服務也可以會談到 Access Control Service (ACS) 要求執行 SharePoint 呼叫自己簽署的憑證。使用此 token，您可以執行遠端動作從舊版的作業由於 web 服務內的清單項目或後端系統中。
    
  

**圖 2。在 SharePoint 中搭配使用方式的遠端事件接收器**

  
    
    

  
    
    
![How remote event receivers work in SharePoint 2013](images/SP15Con_Remote_Event_Receivers_FAQ_fig1.png)
  
    
    

  
    
    

  
    
    

### 如何偵錯的遠端事件接收器？
<a name="RER_DebugRER"> </a>

請參閱 [偵錯及疑難排解中的 SharePoint 增益集的遠端事件接收器](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)。
  
    
    

### 我是否可以從遠端事件接收器執行用戶端 (JavaScript) 程式碼吗？
<a name="RER_ClientsideCodeFromRER"> </a>

否。
  
    
    

### 是否有任何限制或及其 URL 上的遠端事件接收器主控吗？
<a name="RER_ClientsideCodeFromRER"> </a>

遠端事件接收器可以裝載在雲端或未也使用為 SharePoint server 的內部伺服器。實際執行收件者的 URL 不能指定特定的連接埠。這表示您必須使用其中一個連接埠 443 HTTPS，我們建議，或連接埠 80 http。 如果您使用 HTTPS 與主控接收器 service 內部部署，但的增益集是在Microsoft SharePoint Online，然後裝載伺服器必須具備公開信任的憑證從憑證授權單位。（只有當增益集是內部部署 SharePoint 伺服器陣列中運作方式的自我簽署的憑證）。
  
    
    

### 將 SharePoint 2010 事件處理常式處理 SharePoint 2013 之後使用升級吗？
<a name="RER_Will2020EventHandlerWillWorkOn2013"> </a>

如果SharePoint 2010方案套件包含事件處理常式會升級至SharePoint 2013，視您的自訂方案套件可能運作不含任何修改。這將會太包含事件處理常式。 如果SharePoint 2010解決方案將在SharePoint 2013SharePoint Add-in重塑、 事件處理常式應修正為的遠端事件接收器。（請參閱 [移轉至的遠端事件接收器的 SharePoint 事件接收器](http://channel9.msdn.com/Series/Reimagine-SharePoint-Development/Migrating-a-SharePoint-Event-Receiver-to-a-Remote-Event-Receiver)）。
  
    
    

## 其他資源
<a name="SP15handleevents_addlresources"> </a>


-  [在 SharePoint 增益集建立的遠端事件接收器](create-a-remote-event-receiver-in-sharepoint-add-ins.md)
    
  
-  [建立增益集事件接收器中 SharePoint 增益集](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  
-  [在 SharePoint 2013 中的 Introducing 遠端事件接收器](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=3ef8f7ae-85a7-44c3-967d-d1620e2a019f)
    
  
-  [移轉至的遠端事件接收器的 SharePoint 事件接收器](http://channel9.msdn.com/Series/Reimagine-SharePoint-Development/Migrating-a-SharePoint-Event-Receiver-to-a-Remote-Event-Receiver)
    
  

