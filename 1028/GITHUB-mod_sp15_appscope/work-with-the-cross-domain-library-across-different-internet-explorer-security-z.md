---
title: 跨不同的 Internet Explorer 安全性區域中 SharePoint 增益集使用的跨網域文件庫
ms.prod: SHAREPOINT
ms.assetid: 3d24f916-60b2-4ea9-b182-82e33cad06e8
---


# 跨不同的 Internet Explorer 安全性區域中 SharePoint 增益集使用的跨網域文件庫
了解如何在 Windows Internet Explorer 的其他安全性區域] 中的 [主機的網頁伺服器和增益集] 頁面時使用SharePoint 2013的跨網域文件庫。
如果您要用於您的增益集的SharePoint 2013跨網域文件庫，您應該注意的安全性區域在 Internet Explorer 中的運作方式。您的增益集可能會遇到一些通訊問題如果 SharePoint 網站和增益集是在不同的區域。本文說明當您在不同的 Internet Explorer 安全性區域中使用的跨網域文件庫會發生什麼事。
  
    
    


## 使用 SharePoint 跨網域文件庫的 Internet Explorer 中的跨區域案例
<a name="bk_crosszonescenarios"> </a>

基於安全性考量，Internet Explorer 會防止共用 cookie 因為每個完整性層級都有其專屬 cookie 儲存區位於不同的完整性層級 (也稱為安全性區域) 的頁面。完整性的層級] 頁面上由其最頂端] 頁面上，並在該頁面內任何圖文框會共用相同的完整性層級。如需詳細資訊，請參閱 [小心共用跨區域案例中的 Cookie](http://blogs.msdn.com/b/ieinternals/archive/2011/03/10/internet-explorer-beware-cookie-sharing-in-cross-zone-scenarios.aspx)。
  
    
    
SharePoint 跨網域文件庫使用隱藏的 **IFrame**及主控的 SharePoint 用戶端 proxy] 頁面上啟用使用JavaScript的用戶端通訊。當您在您的頁面中參照的 sp.requestexecutor.js 檔案使用的跨網域文件庫。如需詳細資訊，請參閱 [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)。
  
    
    
遠端增益集] 頁面上與 SharePoint 網站有不同的安全性區域] 中，無法傳送驗證 cookie。如果不有任何授權 cookie，且 **IFrame**嘗試載入 [proxy] 頁面上，將會重新導向至 SharePoint 登入頁面。SharePoint 登入頁面不能包含在 **IFrame**基於安全性考量。在這些案例中，文件庫無法載入 proxy] 頁面上，並與 SharePoint 通訊即無法再。
  
    
    
下圖顯示 [proxy] 頁面無法載入跨區域案例。主要頁面處於同一安全性區域為 `http://remoteserver/remotepage.html`圖文框。無法載入 [proxy] 頁面。
  
    
    

**圖 1。跨集區案例無法載入 [proxy] 頁面上的位置**

  
    
    

  
    
    
![Cross-zone scenario, proxy page cannot be loaded](images/Crosszone_loaderror.png)
  
    
    
以下是一些範例中的跨網域文件庫可能無法載入 [proxy] 頁面上：
  
    
    

- 客戶使用SharePoint Online，而您遠端增益集] 頁面上裝載的內部網路伺服器上。此案例中很容易載入問題因為 SharePoint Online URL 通常是不在近端內部網路區域中的 [proxy] 頁面上。這是很見的案例期間的增益集的初始開發因為您可能會使用 IIS Express 或另一部本機伺服器來裝載您不完整的網際網路網域的頁面。
    
  
- 客戶使用表單型驗證與 SharePoint 內部部署與您的遠端網頁裝載在雲端服務 (例如Microsoft Azure)。
    
  

## 處理SharePoint Add-ins中的跨區域案例
<a name="bk_handlingcrosszone"> </a>

有幾個同時 (強烈建議使用) 的增益集開發及增益集執行階段期間解決此問題的方式。
  
    
    

### 最佳作法： 使用 apphost 模式

若要處理跨區域案例，我們建議您在 SharePoint 中有apphost] 頁面。Apphost 頁面是包含 **IFrame**中的 [遠端] 頁面上的 SharePoint 頁面。內部 **IFrame** apphost] 頁面中的每個項目存在於為增益集 web 相同的安全性區域。[遠端] 頁面上的跨網域文件庫可接收授權 cookie 和成功載入 [proxy] 頁面。
  
    
    
下圖顯示使用 apphost 頁面模式所處理的跨區域案例。
  
    
    

**圖 2。跨集區處理使用 apphost 頁模式的案例**

  
    
    

  
    
    
![Cross-zone scenario handling by using the apphost](images/Crosszone_loadsuccess.png)
  
    
    
所需的 apphost] 頁面上的程式碼都很簡單。[Apphost] 頁面上的主要部分是 **SPAppIFrame**元素。您必須使用 CSS 來隱藏 **IFrame**使其不會干擾增益集。
  
    
    
下列標記為簡單 apphost] 頁面上的範例。標記會執行下列工作：
  
    
    

- 宣告時使用 SharePoint 元件所需的指示詞。
    
  
- 宣告樣式以設計讓 **IFrame**看不見。
    
  
- 宣告 **SPAppIFrame**並將目標設為增益集起始頁面。
    
  



```HTML

<%@ Page
    Inherits="Microsoft.SharePoint.WebPartPages.WebPartPage, Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c" 
    language="C#" %>
<%@ Register 
    Tagprefix="SharePoint" 
    Namespace="Microsoft.SharePoint.WebControls" 
    Assembly="Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c" %>
<%@ Register 
    Tagprefix="Utilities" 
    Namespace="Microsoft.SharePoint.Utilities" 
    Assembly="Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c" %>
<%@ Register 
    Tagprefix="WebPartPages" 
    Namespace="Microsoft.SharePoint.WebPartPages" 
    Assembly="Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, PublicKeyToken=71e9bce111e9429c" %>

<html>
<head>
    <title>Your add-in page title</title>
    <style type="text/css">
        html, body
        {
            overflow:hidden;
        }
        
        body
        {
            margin:0px;
            padding:0px;
        }
         
        iframe 
        {
            border:0px;
            height:100%;
            width:100%;
        }
    </style>
</head>

<body>
    <SharePoint:SPAppIFrame 
        runat="server" 
        src="~remoteAppUrl/StartPage.html?{StandardTokens}" 
        frameborder="0">
    </SharePoint:SPAppIFrame>
</body>
</html>
```

如果您想讓使用者深層連結成增益集的部分，apphost 頁面以及 **IFrame**的內容共同作業以使其成為可能。一個替代項是在遠端增益集中使用 **IFrame**後續訊息通訊及每頁的個別 Url。若要讓每頁的個別 Url，您可以建立增益集 web 中的個別頁面或一頁上使用查詢字串參數。
  
    
    

### 替代方法： 將網站新增至 Internet Explorer 中相同的安全性區域

如果增益集未旨在遵循 apphost 模式，您仍可以讓運作下列網域新增到相同的安全性區域：
  
    
    

- SharePoint 網站 (例如 `https://contoso.sharepoint.com`) 的網域。
    
  
- 雲端託管增益集 ( `http://remoteserver`) 的網域。
    
  
- Microsoft 主控登入頁面與服務 ( `*.microsoftonline.com`) 的網域。
    
  
系統管理員可以使用 Active Directory 原則推送到組織中的所有電腦的變更。
  
    
    

## 使用 apphost 模式的安全性含意
<a name="bk_securityimplications"> </a>

請務必指向的 apphost 圖樣有效處於遠端的頁面為增益集 web 相同的安全性區域。請確定您了解新增至安全性區域的站台的含意。如需詳細資訊，請參閱 [如何使用 Internet Explorer 中的安全性區域](http://support.microsoft.com/kb/174360)。
  
    
    

## 其他瀏覽器中運作： Chrome、 Firefox 和 Safari
<a name="bk_otherbrowsers"> </a>

其他瀏覽器，例如 Google Chrome、 Mozilla Firefox，以及 Apple Safari 不實作安全性區域的概念。如果在瀏覽器不隔離分開儲存在 cookie，它可能就不會遇到本文所述的問題。我們建議您遵循新增寫使用 apphost 模式可確保您的增益集所提及之瀏覽器中運作和 Internet Explorer 中，不論哪些安全性區域是在 SharePoint 中的 apphost 圖樣。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


-  [保護資料存取及用戶端物件模型的 SharePoint 增益集](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [存取 SharePoint 2013 資料增益集使用跨網域文件庫](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  
-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [若要考慮的三種方式設計選項的 SharePoint 增益集](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [主機 web、 增益集 web 及 SharePoint 2013 中的 SharePoint 元件](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  
-  [資料儲存在 SharePoint 增益集](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data)
    
  
-  [SharePoint 2013 中建立自訂的 proxy] 頁面上的跨網域文件庫](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [用戶端的跨網域安全性](http://msdn.microsoft.com/en-us/library/cc709423%28v=vs.85%29.aspx)
    
  

