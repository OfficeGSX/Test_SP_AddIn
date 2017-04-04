---
title: 內容權杖 OAuth 流量的 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 526c8c4a-5cbb-4efc-87d9-23ac73655cf4
---


# 內容權杖 OAuth 流量的 SharePoint 增益集
了解低信任層級、 提供者主控增益集SharePoint的 OAuth 驗證和授權流程。
## 要取得的 OAuth 與SharePoint提供者主控SharePoint Add-ins概觀
<a name="OAuth_Actors"> </a>

在SharePoint、 **OAuth 驗證和授權流程提供者主控低信任、 增益集包括一系列的增益集、 SharePoint、 授權伺服器，與瀏覽器之間的互動** 在執行階段。在此案例中的授權伺服器為Microsoft Azure Access Control Service (ACS)。
  
    
    
提供者主控增益集，您有遠端 web 應用程式或分開SharePoint，且不SharePoint伺服器陣列或SharePoint Online租用的一部分的服務。它可以裝載在雲端或內部部署伺服器上。針對本文中的遠端元件被名為 Contoso.com。
  
    
    

> **注意事項**
> 遠端元件也可裝載回應SharePoint等項目清單或清單項目發生的事件的事件接收器。Contoso.com 可能會想要回應的遠端事件的範例是清單事件，例如新增或移除清單項目 ；或網頁事件，例如新增或刪除網站。如需如何建立的遠端事件接收器的詳細資訊，請參閱 [在 SharePoint 增益集建立的遠端事件接收器](create-a-remote-event-receiver-in-sharepoint-add-ins.md)。
  
    
    

Contoso.com 使用SharePoint用戶端物件模型 (CSOM) 或SharePoint REST Api 來呼叫SharePoint。Contoso.com 應用程式使用 OAuth 權杖傳遞流程SharePoint驗證。 **SharePoint和 Contoso.com 不信任彼此 ； 但同時信任 ACS** 會接受 ACS 所發出的權杖。有三個語彙基元的相關: SharePoint已建立內容權杖 ACS SharePoint轉送給 Constoso.com。Contoso.com 驗證讓其信任，由 ACS 發行內容權杖。Contoso.com 然後擷取內容權杖重新整理權杖和使用它來直接從 ACS 取得存取 token。它包含SharePoint其要求存取權杖。SharePoint驗證的存取權杖發出時 ACS，讓它從 Contoso.com 回應要求。
  
    
    
在遠端元件， **提供權杖處理程式碼** 。(但如果您的遠端元件裝載於.NET、 Microsoft Office Developer Tools for Visual Studio提供範例程式碼可讓您執行大部分的工作)。如需權杖處理程式碼的詳細資訊，請參閱 [處理安全性權杖的提供者主控低信任層級 SharePoint 增益集](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)。
  
    
    

## 完成使用流程的先決條件
<a name="Prerequisites"> </a>

有一些必須先完成特定SharePoint Add-in可以使用內容權杖流程的預備步驟。
  
    
    

- 如果SharePoint Add-in是要安裝至內部部署SharePoint的伺服器陣列，有不套用至SharePoint Online只安裝的安裝需求：
    
  - **伺服器陣列必須設定** 為支援新增寫 (這是實際需求任何SharePoint Add-ins安裝至伺服器陣列，即使這些未使用的快顯 Token 的流程。)如需詳細資訊，請參閱 ＜ [Configure environment for SharePoint 增益集](http://technet.microsoft.com/en-us/library/fp161236%28v=office.15%29.aspx)。
    
  
  - **客戶** 安裝增益集 **必須具備Office 365帳戶** 。這是必要存取 ACS。客戶沒有任何其他用途使用其帳戶。
    
  
  - 您必須設定伺服器陣列共用的信任關係的Office 365具有與 ACS。輕鬆地作法是使用Windows PowerShell指令碼。如需詳細資訊，請參閱 [若要授權提供者主控增益集內部部署 SharePoint 網站上使用 Office 365 SharePoint 網站](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)。
    
  
- 不論是否增益集安裝SharePoint Online或 **SharePoint Add-in必須登錄與 ACS** 內部SharePoint伺服器陣列。如需關於如何完成的詳細資訊，請參閱 [註冊 sharepoint2013 增益集](register-sharepoint-add-ins-2013.md)。除此之外，增益集提供 ACS 其用戶端識別碼和用戶端註冊的一部分的密碼。
    
  

## 請參閱內容權杖流程中的步驟
<a name="OAuth_ProcessFlowSteps"> </a>

SharePoint提供者主控增益集的 OAuth 驗證和授權流程如下圖所示。
  
    
    

**OAuth 權杖內容流程**

  
    
    

  
    
    
![OAuth authorization process flow](images/833fcdcc-1755-438b-9ada-dce9646564c0.gif)
  
    
    
這些是對應圖中的頁碼的步驟：
  
    
    

  
    
    

1. 使用者啟動SharePoint Add-in從 SharePoint。增益集的設計會決定如何這麼做：
    
  - 如果增益集設計目的是表面 (位於 Contoso.com)增益集組件(這是主要的包裝函式 **IFRAME**) 中的遠端 web 應用程式，然後啟動增益集只是表示瀏覽至包含增益集組件SharePoint ] 頁面上。(如果使用者未已登入， SharePoint提示使用者登入。)SharePoint處理] 頁面上，並偵測從 Contoso.com 應用程式] 頁面上的元件。(如需增益集組件的詳細資訊，請參閱 [建立增益集組件安裝與您 SharePoint 的增益集](create-add-in-parts-to-install-with-your-sharepoint-add-in.md))。
    
  
  - 如果增益集設計目的是要作為瀏覽器中的完整頁面，然後使用者啟動它在其增益集並排顯示在SharePoint網站 **Site Contents** ] 頁面上按一下。(此變化為增益集包括啟動遠端元件的自訂功能表或功能區項目時)。
    
  
2. 不論如何增益集可啟動、 SharePoint必須取得它可讓要求 ACS 建立包含目前的使用者、 遠端應用程式 URL 及其他資訊包括SharePoint內容的資訊內容權杖傳送至 Contoso.com 應用程式內容權杖。內容權杖也包含已加密的重新整理權杖。
    
  
3. ACS 簽署內容權杖，搭配使用的 Contoso.com 增益集密碼，並傳回至SharePoint演算法。僅限 ACS 開頭且 Contoso.com 增益集知道密碼。
    
  
4. 如果 Contoso.com 應用程式增益集組件中的呈現， SharePoint轉譯頁面該增益集的主機部分並將內容權杖新增至要取得其內容會呼叫 **IFRAME**增益集組件中的 URL。如果 Contoso.com 應用程式為完整頁面、 SharePoint將瀏覽器重新導向至 Constoso.com 並包含內容權杖重新導向回應的一部分。
    
  
5. 內容權杖隨附於瀏覽器要求傳送至 Contoso.com 伺服器。
    
  
6. Contoso.com 伺服器取得內容權杖，並驗證用途因為它知道用戶端秘密簽章。這可確保 Contoso.com 權杖由 ACS 開頭且不SharePoint假冒詐欺發行。Contoso.com 的重新整理 token 擷取內容權杖並傳送，以及其他資訊包括其用戶端識別碼和用戶端密碼，以允許存取SharePoint，其存取權杖要求的 ACS
    
  
7. ACS 驗證重新整理權杖使其就可以確信它發出 token，且再傳回存取權杖給 Contoso.com。(選用) Contoso.com 可以快取此存取權杖，讓它不會具有要求 ACS 存取權杖其存取SharePoint每次。根據預設，存取權杖是不錯的幾個小時一次。(當本文撰寫時、 SharePoint ACS 發出存取權杖的預設到期時間是 12 小時，但會無法變更)。每個存取權杖是專屬於指定授權的原始要求中且授與服務的權限僅限 (在此例中是SharePoint) 該要求中所指定的使用者帳戶。重新整理權杖較長存在 (六個月本文撰寫時) 和也可在快取。如此，相同的重新整理權杖可以贖回針對新的存取權杖從 ACS 到本身的重新整理權杖到期為止。(如需權杖快取的詳細資訊，請參閱 [處理安全性權杖的提供者主控低信任層級 SharePoint 增益集](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md))。重新整理權杖到期時, 為 Contoso.com 可以取得一個新的取得新的內容權杖。如需這如何完成的詳細資訊，請參閱 [取得新的內容權杖](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken)。
    
  
8. Contoso.com 使SharePoint REST API 呼叫或 CSOM 要求 spnv 用以存取權杖。其執行 HTTP **Authorization**標頭中傳遞 OAuth 存取權杖。(建立標頭的範例程式碼中提供Office Developer Tools for Visual Studio若您的遠端元件主控.NET 平台上。
    
  
9. SharePoint驗證存取 token，讓它可以確信由 ACS 發出的權杖。然後將資料傳送至 Contoso.com 要求該 Contoso.com 或執行建立、 讀取、 更新、，或刪除 Contoso.com 要求 (CRUD) 作業。
    
  
10. Contoso.com 應用程式] 頁面上呈現在瀏覽器 (或 **IFRAME**增益集組件中)。
    
  

## 其他資源
<a name="Filename_AdditionalResources"> </a>


-  [授權與驗證的 SharePoint 增益集](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [增益集 (英文) SharePoint 2013 權限](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [重要方面之 SharePoint 增益集架構設計和開發入門](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

