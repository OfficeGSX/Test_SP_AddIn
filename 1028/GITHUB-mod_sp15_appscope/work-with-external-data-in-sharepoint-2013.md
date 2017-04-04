---
title: 使用 SharePoint 2013 中的外部資料
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# 使用 SharePoint 2013 中的外部資料
用於存取並處理外部資料與JavaScriptSharePoint Add-ins在頁面上尋找資源和指引。
## 使用SharePoint Add-insJavaScript
<a name="SP15Workdata_Working"> </a>

在您SharePoint Add-ins中，會經常必須來擷取並操作遠端 web 應用程式或服務內SharePoint頁面或元件所公開的資料。因為SharePoint伺服器上不允許自訂程式碼、 增益集必須使用JavaScript達到此目的。SharePoint 增益集相關的模型提供用於存取遠端資料和服務的多個選項。
  
    
    

### 使用SharePoint跨網域JavaScript文件庫來存取外部資料

如果您提供架設在遠端基礎結構的自訂 proxy 頁面，您可以使用遠端 web 應用程式中的跨網域文件庫來存取資料。為開發人員，負責實作自訂的 proxy] 頁面上，且必須如何處理自訂邏輯驗證機制，例如若有的話，遠端應用程式。如果您想遠端資料來源與用戶端層級進行SharePoint頁面之間的通訊，使用的跨網域文件庫。
  
    
    
如需如何使用這種方式的文件庫的詳細資訊，請參閱 [SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)。
  
    
    

> [!注意事項]
> SharePoint跨網域文件庫也可用於其他方向 ；也就是JavaScript遠端網頁上可以使用它來存取資料從SharePoint。如需有關此使用的文件庫，請參閱 [建立 SharePoint 增益集使用的跨網域文件庫](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)。
  
    
    


### 使用SharePoint web proxy 來存取外部資料

您可以使用JavaScript用戶端物件模型存取遠端資料中公開的 web proxy。(Proxy 也是.NET 用戶端物件模型 (CSOM) 中可用但您無法使用該物件模型在SharePoint伺服器執行的程式碼中)。當您使用的 web proxy 時，您會發出至SharePoint初始要求。接著， SharePoint要求至指定之端點的資料及轉寄您] 頁面上的回應。當您想遠端資料來源及伺服器層級進行SharePoint頁面之間的通訊時使用的 web proxy。
  
    
    
如需如何使用之 proxy 的詳細資訊，請參閱 [查詢使用 SharePoint 2013 中的 web proxy 遠端服務](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)。
  
    
    

## 其他資源
<a name="SP15Workdata_AddRes"> </a>


-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  

