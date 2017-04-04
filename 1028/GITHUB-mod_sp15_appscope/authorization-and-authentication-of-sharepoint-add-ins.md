---
title: 授權與驗證的 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# 授權與驗證的 SharePoint 增益集
取得SharePoint，來存取SharePoint資源SharePoint Add-in是用來授權要求的驗證與授權概觀。
**觀賞影片關於增益集身分識別。**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## 增益集的SharePoint的驗證
<a name="AuthN"> </a>

當 *使用者*  登入SharePoint時，會驗證使用者的安全性權杖。權杖是由身分識別提供者所發出。SharePoint支援數種使用者驗證。如需詳細資訊，請參閱 [驗證、 授權與 SharePoint 2013 的安全性](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx)。
  
    
    
SharePoint Add-ins也會需要驗證及授權的安全性主體。增益集可以驗證和授權以數種不同的方式。如需詳細資訊，請參閱 [三個授權系統的 SharePoint 增益集](three-authorization-systems-for-sharepoint-add-ins.md)。
  
    
    

## 授權原則： 僅限使用者原則、 使用者 + 增益集原則或新增-僅在原則
<a name="AuthZ"> </a>

授權程序驗證已驗證的主旨 (增益集或使用者或兩者) 具有權限來執行某些作業或存取特定資源 (例如清單或SharePoint文件資料夾)。
  
    
    
SharePoint授權原則的使用者三個類型。僅限使用者原則只需要呼叫SharePoint包括已驗證的使用者身分識別。新增-僅在原則只需要呼叫包含只驗證增益集的識別。使用者 + 增益集原則需要呼叫包含這兩種類型的已驗證的身分識別。當使用者透過增益集，而不是存取透過SharePoint UI SharePoint資源SharePoint使用僅限使用者原則。不過，從SharePoint Add-in通話， SharePoint一定會使用 [新增-僅在或使用者 + 增益集原則。SharePoint Add-in決定哪些原則由它包含SharePoint其要求的存取權杖的類型。如果由使用者 + 增益集要求， SharePoint需要增益集和使用者兩者都擁有存取增益集之資源的權限。但如果是新增-僅在要求， SharePoint需要增益集有權限給資源，但它們是否使用者執行排。( SharePoint Add-in可以請加入-僅在要求只有當已經在指定這麼事先 ； 通常在安裝時的權限)。
  
    
    
如需授權原則和它們的運作方式的詳細資訊，請參閱 [SharePoint 2013 中的增益集授權原則類型](add-in-authorization-policy-types-in-sharepoint-2013.md)。
  
    
    

## 增益集的權限和增益集權限要求範圍
<a name="Permissions"> </a>

必須指定SharePoint Add-in的開發人員，透過增益集資訊清單檔案、 權限增益集必須SharePoint增益集網路外部的資源。(增益集自動具有完全控制權限到整個增益集 web)。當增益集設計目的是要從啟動SharePoint內時，增益集安裝基礎結構提示安裝增益集來授與或拒絕的所需的權限的使用者。網站使用者的之後已授與權限，可以使用增益集不必重新授與權限。不過，當增益集的設計啟動外部SharePoint;也就是上SharePoint、 未安裝然後SharePoint會提示使用者執行增益集所需的權限授與每次執行增益集。增益集在行動裝置與Office 增益集為增益集可以存取SharePoint，但都不會安裝在其上的範例。
  
    
    
僅限網站擁有者可以在SharePoint網站上安裝SharePoint Add-in (除非已建立增益集的安裝權限的自訂角色)。使用者可以授與增益集這些權限的他的使用者或自己具有。讓使用者無法安裝增益集所需的權限的使用者不會有。同樣地，使用者無法執行，並從外部啟動增益集，使用者就不需要的權限。不過， SharePoint上已安裝SharePoint Add-in ，它可以尋求進行新增-僅在通話的權限。增益集具有權限可存取SharePoint執行增益集的使用者沒有權限執行的方式。
  
    
    
增益集可以也已遭撤銷或SharePoint Online租用戶系統管理員或SharePoint伺服器陣列管理員所授與的權限。
  
    
    
在增益集資訊清單檔、 SharePoint Add-in指定正常運作所需的權限。權限要求指定增益集所需的權限和需要權限的範圍。範圍指出其中SharePoint階層中的權限要求適用於。SharePoint支援四種不同的內容範圍： 租用、 網站集合、 網站及清單。也有特殊執行搜尋查詢，存取分類資料、 社交功能、 Microsoft Business Connectivity Services (BCS)功能及Project Server 2013功能的範圍。如需詳細資訊，請參閱 [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)。
  
    
    

## 使用 OAuth 的時
<a name="FileName_uniquekeyword4"> </a>

您可能已經聽 OAuth 2.0 扮演重要角色中的驗證和授權的SharePoint Add-ins。如此，但它不是一定是每個SharePoint Add-in授權本文的一部分。如果您打算建立SharePoint Add-in的遠端 web 應用程式中執行，並且回饋給SharePoint使用伺服器端程式碼，您必須使用 OAuth。關閉內部部署的遠端 web 應用程式時，您可以使用 [低信任層級授權系統](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)Azure ACS是存取 token 簽署者。如果是在內部部署，您可以通常使用 [高信任系統](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)本身的增益集和數位憑證的存取 token 發行者。
  
    
    
您不可以使用 OAuth 撥打從JavaScript增益集網站本身中頁面上或從遠端網頁使用 [的跨網域文件庫](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)。如需詳細的跨網域文件庫的詳細資訊，請參閱 [建立 SharePoint 增益集使用的跨網域文件庫](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)。
  
    
    

## 其他資源
<a name="Filename_AdditionalResources"> </a>


-  [三個授權系統的 SharePoint 增益集](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  

