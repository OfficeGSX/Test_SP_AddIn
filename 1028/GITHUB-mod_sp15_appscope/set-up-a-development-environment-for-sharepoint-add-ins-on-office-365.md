---
title: 在 Office 365 上設定 SharePoint 增益集的開發環境
ms.prod: MULTIPLEPRODUCTS
ms.assetid: b22ce52a-ae9e-4831-9b68-c9210af6dc54
---


# 在 Office 365 上設定 SharePoint 增益集的開發環境
在 Office 365 開發人員網站上，設定 SharePoint Add-ins 的開發環境。
請閱讀 [工具及開發 SharePoint 的增益集的環境](tools-and-environments-for-developing-sharepoint-add-ins.md)先了解您的選項，再執行本文中的任何程序。如果您不確定想要建立的 SharePoint Add-ins 類型，請參閱 [SharePoint Add-ins](sharepoint-add-ins.md)。
  
    
    


## 在電腦上安裝 Visual Studio 和工具
<a name="devenv_vs"> </a>


- 如果您尚未安裝 **Visual Studio** 2013 或更新版本，請使用 [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx) 中的指示進行安裝。建議您使用 [Microsoft 下載中心內的最新版本](https://www.visualstudio.com/downloads/download-visual-studio-vs)。
    
  
- Visual Studio 包含 **Microsoft Office Developer Tools for Visual Studio** ，但有時候發行版本的工具的更新 Visual Studio。若要確定您有最新版的工具使用執行 [Office Developer Tools for Visual Studio 2013 安裝程式 ](http://aka.ms/OfficeDevToolsForVS2013)，或  [Office Developer Tools for Visual Studio 2015 安裝程式 ](http://aka.ms/OfficeDevToolsForVS2015)。 
    
  

### Visual Studio 中的詳細資訊記錄

如果您想要開啟詳細資訊記錄，請遵循下列步驟：
  
    
    

1. 開啟登錄並瀏覽至 **HKEY_CURRENT_USER\\Software\\Microsoft\\VisualStudio\\ _nn.n_\\SharePointTools** ，其中 _nn.n_ 是Visual Studio 的版本，例如 12.0 或 14.0。
    
  
2. 加入名為 **EnableDiagnostics** 的 DWORD 機碼。
    
  
3. 將機碼的值設為 **1** 。
    
  
在未來版本的 Visual Studio 中，登錄路徑將會變更。
  
    
    

## 註冊 Office 365 開發人員網站
<a name="o365_signup"> </a>


> [!注意事項]
>  您可能已有 Office 365 開發人員網站 的存取權：> **您是 MSDN 訂閱者嗎？** Visual Studio Enterprise 的 MSDN 訂閱者可以獲得 Office 365 Developer 訂閱的權益。 [立即兌換權益。](https://msdn.microsoft.com/subscriptions/manage/default.aspx)> **您有下列其中一個 Office 365 訂閱計劃嗎？**> **如果有，Office 365 訂閱的管理員可以使用  [Office 365 管理中心](https://portal.microsoftonline.com/admin/default.aspx) 建立 開發人員網站** 。如需詳細資訊，請參閱 [建立現有的 Office 365 訂閱開發人員網站](create-a-developer-site-on-an-existing-office-365-subscription.md)。 
  
    
    

有三種方法可以取得 Office 365 計劃。 
  
    
    

- 透過 Office 365 開發人員計劃，註冊免費、為期一年的 Office 365 開發人員帳戶。 [取得更多資訊](http://dev.office.com/devprogram)，或直接前往 [註冊表單](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170)。在您註冊開發人員計劃後，將會收到一封電子郵件，其中包含的連結可讓您註冊開發人員帳戶。請使用下列的指示。
    
  
- 利用一個使用者授權，開始使用 [免費 30 天試用](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK)。
    
  
- 購買  [Office 365 開發人員訂閱](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK)。 
    
  

> [!秘訣]
> 在另一個視窗或索引標籤中開啟這些連結，以便使用下列指示。 
  
    
    


**圖 1。Office 365 開發人員網站網域名稱**

  
    
    

  
    
    
![Office 365 帳戶註冊表單的第 2 頁](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. 註冊表單的第一頁 (未顯示) 一目了然。只要提供所要求的您的個人資訊，然後選擇 [下一步]。
    
  
2. 在第二頁上 (圖 1 中所示)，指定訂閱管理員的使用者識別碼。
    
  
3. 建立 **.onmicrosoft.com** 的子網域。
    
    註冊之後，您必須使用所產生的認證 (格式為  _UserID_@ _yourdomain_.onmicrosoft.com) 登入您在其中管理帳戶的 Office 365 入口網站。SharePoint Online 開發人員網站會佈建於您的新網域中： **http:// _yourdomain_.sharepoint.com** 。
    
  
4. 選擇 [下一步]，並填寫表單的最後一頁。如果您選擇提供電話號碼來取得確認碼，則可以提供行動電話或地面通訊電話號碼，但「不」是 VoIP 號碼。
    
  

    
> [!注意事項]
> 如果您嘗試註冊開發人員帳戶時已登入另一個 Microsoft 帳戶，可能會收到此訊息：「抱歉，您輸入的使用者識別碼無法運作。它看起來無效。請務必輸入組織指派給您的使用者識別碼。您的使用者識別碼通常看起來像是  *someone@example.com*  或 *someone@example.onmicrosoft.com*  。」> 如果您看到此訊息，請登出原本使用的 Microsoft 帳戶然後再試一次。如果仍然收到此訊息，請清除瀏覽器快取，或切換到 [InPrivate 瀏覽]，然後填寫表單。 
  
    
    

完成註冊程序之後，您的瀏覽器會開啟 Office 365 安裝頁面。請選擇 [管理] 圖示，以開啟管理中心頁面。
  
    
    

**圖 2。Office 365 管理中心頁面**

  
    
    

  
    
    
![顯示 Office 365 系統管理中心的螢幕擷取畫面。](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. 您必須等待 開發人員網站 完成準備。準備完成之後，請在瀏覽器中重新整理 管理中心頁面。
    
  
2. 然後選擇頁面左上角的 [建置增益集] 連結來開啟 開發人員網站。您應該會看到與圖 3 中網站類似的網站。頁面上會有 [測試階段的增益集] 清單。這確認已使用 SharePoint 的開發人員網站範本建立網站。如果您是看到一般的小組網站，請稍候幾分鐘，再重新啟動網站。
    
  
3. 記下網站 URL。在 Visual Studio 中建立 SharePoint Add-ins 專案時會使用它。
    
  

**圖 3. 具有 [測試階段的增益集] 清單的開發人員網站首頁**

  
    
    

  
    
    
![顯示開發人員網站首頁的螢幕擷取畫面。](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## 其他資源
<a name="SP15SetupSPO365_bk_addlresources"> </a>


-  [SharePoint Add-ins](sharepoint-add-ins.md)
    
  
-  [開始建立提供者主控 SharePoint 增益集](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [開始建立 SharePoint 主控 SharePoint 增益集](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

  
    
    

