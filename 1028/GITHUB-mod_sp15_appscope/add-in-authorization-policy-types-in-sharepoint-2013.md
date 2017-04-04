---
title: SharePoint 2013 中的增益集授權原則類型
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# SharePoint 2013 中的增益集授權原則類型
深入了解增益集SharePoint不同授權原則： 新增-僅在原則、 使用者 + 增益集 (英文) 原則和唯讀使用者原則。使用增益集僅在原則也提供指導方針。
之前先閱讀本文，您應該先先熟悉的文章 [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)和 [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)。
  
    
    


## 概略了解增益集授權的原則類型
<a name="Overview"> </a>

SharePoint 提供三種類型的授權原則：
  
    
    

- **僅限使用者原則**  SharePoint時使用的僅限使用者原則，則會檢查的使用者權限。使用者直接而不需使用增益集，當使用者第一次開啟 SharePoint 網站的首頁上或從 PowerShell 存取 SharePoint Api 等存取資源時SharePoint會使用此原則。
    
    
    
  
- **使用者 + 增益集原則**  SharePoint使用使用者 + 增益集原則時，檢查使用者和增益集主體的權限。驗證檢查成功只有當目前的使用者及其增益集擁有權限來執行上述的巨集指令。
    
    例如SharePoint Add-in想要取得使用者的資源的存取權的SharePoint時可以使用此原則。( SharePoint Add-in遠端元件中的程式碼有設計，讓使用者 + 增益集呼叫SharePoint)。
    
    
    
  
- **新增-僅在原則**  SharePoint使用增益集僅在原則時，檢查的增益集主體的權限。驗證檢查成功只有當目前的增益集有足夠的權限來執行有問題，不論目前使用者的權限的動作 (如果有的話)。
    
    費用核准增益集是無法使用此原則設計增益集範例。增益集可讓有用否則能核准費用核准費用下方特定數量的使用者。請參閱以下案例的詳細資訊。
    
    
    
    > [!注意事項]
      > 某些 api (英文) 需要將使用者內容，且無法執行與增益集只內的原則。與執行搜尋查詢的互動Project Server 2013包含許多 api (英文)。

### 請參閱使用增益集僅在原則增益集的範例案例
<a name="Scenario"> </a>

我們指出在 Contoso、 銷售經理 Adam、 購買費用提交增益集使用新增的-僅限原則。Adam Adam 選擇購買增益集，當系統提示允許增益集來提升使用者的權限 ；也就是允許增益集來新增-僅在呼叫SharePoint。Adam 將增益集所要求的權限。他然後購買足夠授權的增益集的所有 Contoso 銷售人員，並他安裝的增益集銷售團隊的SharePoint網站中。
  
    
    
推出、 審查正在送出費用報告使用新費用提交增益集審查通常是無法核准他們自己的費用報表，但是他們可以這麼做時使用的增益集，因為 Adam 授與該能夠執行這項作業的送出費用低於 $50 因為他設定增益集來自動核准報表低於 $50。增益集會自動指派他核准 $50 或以上的報告工作。這可以實作由提供SharePoint Add-in寫入權限的已核准費用SharePoint清單。但是，之間的使用者，只有人力資源經理必須寫入權限清單。在增益集程式碼的設計新增至清單費用進行SharePoint新增-僅在呼叫每當費用遠遠低於 $50。由於未檢查使用者的權限，會自動新增低於 $50 的任何使用者送出至已核准的費用] 清單中，即使使用者沒有寫入權限清單。
  
    
    

  
    
    

### 了解增益集如何取得權限以使用新增的-僅限原則
<a name="Approve"> </a>

若要能夠新增-僅在呼叫SharePoint、 增益集必須要求權限以使用增益集僅在原則。此要求為進行中的增益集資訊清單。您這麼做將 **AllowAppOnlyPolicy**屬性新增至 **AppPermissionRequests**元素，並將它 **true**下列標記所示：
  
    
    

```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>
```


> [!注意事項]
> SharePoint Add-ins用來呼叫"apps for SharePoint"。若要維持回溯相容性、 應用程式資訊清單結構描述已不變更，所以 5 元素與屬性名稱中出現字串"app"。
  
    
    

核准此要求將會提示使用者安裝的增益集。如果增益集要求在承租人範圍設定的權限，然後只租用戶系統管理員可以新增-僅在原則，所以只有承租人管理員授與使用可安裝增益集。如果增益集不要求高於網站集合範圍的任何權限、 網站集合管理員可以安裝的增益集。如需權限範圍的詳細資訊，請參閱 [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)。
  
    
    

### 了解如何增益集進行新增-僅在通話
<a name="AppOnlyCalls"> </a>

新增-僅在呼叫的使用者和SharePoint + 增益集呼叫的差異在於呼叫所含的存取權杖的類型。下列程式碼示範如何取得使用者 + 以 managed 程式碼增益集及增益集僅在存取權杖。詳細的程式碼可以讓您完成Office Developer Tools for Visual Studio自動新增至專案中Visual StudioTokenHelper.cs (或.vb) 檔案中。
  
    
    

```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+add-in access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...
    
      //Get add-in-only access token.
       string addinOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}
```


> [!注意事項]
> 增益集，不會進行 OAuth 驗證呼叫 (例如增益集的增益集 web 中執行的 JavaScript) 無法使用新增-僅在原則。它們可要求的權限，但無法利用它因為這樣需要傳遞給 add-僅在 OAuth 權杖。僅限增益集與執行SharePoint以外的 web 應用程式可以建立和傳遞新增-僅在權杖。
  
    
    

一般而言，目前的使用者才可進行呼叫的存在。但如果是新增-僅在原則SharePoint會建立 SHAREPOINT\\APP 類似現有 SHAREPOINT\\SYSTEM 使用者。所有的增益集僅在要求中所 SHAREPOINT\\APP 進行。沒有為 SHAREPOINT\\APP 驗證透過使用者為主的驗證方法。
  
    
    

### 使用增益集僅在原則取得指導方針
<a name="GuidelinesFor"> </a>

由於新增-僅在呼叫有效提高使用者的權限，您應該先傳統中建立增益集的要求，使其權限。通話應該使用增益集僅在原則時：
  
    
    

- 增益集需要提高權限給特定通話 ； 使用者上方其權限例如，核准費用報表評估的增益集的情況下。
    
  
- 代表任何使用者 ； 不所代表的增益集例如，增益集SharePoint文件庫上執行夜間維護工作。
    
  

## 其他資源
<a name="AR"> </a>


-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [內容權杖 OAuth 流量的 SharePoint 增益集](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  
-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

