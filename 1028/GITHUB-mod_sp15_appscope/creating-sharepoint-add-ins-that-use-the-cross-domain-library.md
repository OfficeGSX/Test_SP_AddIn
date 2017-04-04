---
title: 建立 SharePoint 增益集使用的跨網域文件庫
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# 建立 SharePoint 增益集使用的跨網域文件庫
了解SharePoint跨網域JavaScript文件庫。
以下是在未低信任層級或高信任授權系統可以使用由SharePoint Add-in，某些案例或不是很好的選擇做為增益集以獲得授權SharePoint資源的唯一方法。範例：
  
    
    


- SharePoint Add-in遠端元件內部部署，但公司防火牆封鎖了SharePoint與 ACS，進而防止使用低信任層級系統之間的伺服器對伺服器通訊。
    
  
- SharePoint Add-in的設計為依賴資料作業與 SharePoint 的用戶端JavaScript單頁的 web 應用程式。
    
  
- SharePoint Add-in主要依賴存取SharePoint資料的伺服器對伺服器通話 (和授權的低信任層級或高信任系統)，但它必須一些JavaScript通話的地方自動執行。例如，圖形作業] 頁面上也可以使用JavaScript對次要更新顯示的資料而不需要重新載入整個頁面。
    
  

不過， [安全性](http://msdn.microsoft.com/en-us/library%28d=robot%29/cc709423(d=robot,l=en-us,v=vs.85).aspx)、 瀏覽器不允許JavaScript架設在一個網域讓特殊的技巧，才可允許遠端JavaScript存取SharePoint資源存取另一個網域上的資源。SharePoint跨網域JavaScript文件庫可讓您輕鬆遠端 web 應用程式使用的技術。
  
    
    


> **注意事項**
> 跨網域文件庫也用來以相反方向 ； 允許存取資料也就是允許JavaScriptSharePoint頁面以遠端網域中存取資料。如需詳細資訊，請參閱 [從 SharePoint] 頁面上的存取遠端資料](#ReverseDirection)。
  
    
    


## 了解跨網域文件庫的架構

SP. SharePoint跨網域文件庫包含檔案中RequestExecutor.js 位於/_layouts/15/虛擬資料夾中的每個SharePoint網站。此檔案中的指令碼封裝克服瀏覽器的限制跨網域指令碼的安全之已知技巧: iFrame 可以與彼此通訊其父頁面藉由 `window.postMessage()`函數，即使頁面的 iFrame 位於不同的網域。讓資料要求和回應傳遞的網域界限使用 `postMessage()`來電。
  
    
    

> **注意**
>  `postMessage()`函數只適用於支援 HTML 5 中，因此SharePoint Add-ins所使用的跨網域文件庫將無法運作較舊的瀏覽器上的瀏覽器。
  
    
    

SharePoint，它會建立隱藏的 iFrame 主控從SharePoint網域特殊的 proxy] 頁面上的遠端 web 應用程式] 頁面上載入時的跨網域文件庫。在每個SharePoint網站上已存在 proxy] 頁面。文件庫用來建立JavaScript Object Notation (JSON)物件包含 CRUD 撥打SharePointREST Api 所需的所有資訊。JSON 物件是使用 `postMessage()`傳遞至 proxy] 頁面。在 [proxy] 頁面，其中也會載入文件庫，JSON 物件是剖析而重新建構為SharePoint其餘呼叫。由於SharePoint網域中是 [proxy] 頁面上，瀏覽器可讓來電。
  
    
    
當然， SharePoint Add-in的遠端元件，仍有已獲得授權SharePoint資源的存取權。有兩種方式可以執行這項作業：
  
    
    

- 增益集主體類型設為 **RemoteWebApplication** (提供者主控應用程式的預設值) 增益集資訊清單中。當增益集使用 ACS 登錄時，用以登錄包含的遠端 web 應用程式的網域。SharePoint信任網域註冊的 ACS，即使它不，在此案例中，使用任何傳遞流程屬於一部分的伺服器端低信任層級系統的 token。如需登錄的增益集的詳細資訊，請參閱 [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md)。
    
  
- SharePoint 主控增益集] 中您可以將增益集主體類型維持設為其預設值為 **Internal**。然後將 **AllowedRemoteHostUrl**屬性 **Internal**元素的遠端 web 應用程式，如下列範例所示的 url。
    
  ```
  
<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>
  ```


> **注意事項**
> 如果您使用的第二個選項 ( **Internal**增益集主要)，然後您可以使用只有JavaScript及跨網域文件庫來存取SharePoint。SharePoint用戶端物件模型會封鎖 **Internal**SharePoint Add-ins，所以您不能使用同時跨網域程式庫的雙授權系統和低信任層級或高信任層級的系統。
  
    
    

如需如何使用文件庫的詳細資訊，請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
  
    
    

## 從 SharePoint] 頁面上的存取遠端資料
<a name="ReverseDirection"> </a>

SharePoint跨網域文件庫也可用以相反方向 ；也就是JavaScriptSharePoint頁面上可用於文件庫中之增益集的遠端元件從取得資料。若要這樣做，回復跨網域架構： 您建立 proxy] 頁面中的遠端 web 應用程式。文件庫會呼叫它會建立以架設 proxy] 頁面的 iFrame 從SharePoint頁面。如需如何使用這種方式的文件庫的詳細資訊，請參閱 [SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)。
  
    
    

## 在本章節中
<a name="ReverseDirection"> </a>


-  [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [跨不同的 Internet Explorer 安全性區域中 SharePoint 增益集使用的跨網域文件庫](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  

## 其他資源
<a name="ReverseDirection"> </a>


-  [解決跨網域問題中 SharePoint 增益集](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)
    
  

