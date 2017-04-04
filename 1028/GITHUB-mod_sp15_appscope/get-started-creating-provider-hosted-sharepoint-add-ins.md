---
title: 開始建立提供者主控 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 3038dd73-41ee-436f-8c78-ef8e6869bf7b
---


# 開始建立提供者主控 SharePoint 增益集
設定開發環境，並建立您第一個提供者主控SharePoint Add-in。
提供者主控增益集是下列其中一個SharePoint Add-ins兩種主要類型。如需SharePoint Add-ins與兩種不同類型的概觀，請參閱 [SharePoint Add-ins](sharepoint-add-ins.md)。以下是提供者主控增益集的摘要：
  
    
    


- 它們包含 web 應用程式或服務或外部主控從 SharePoint 伺服器陣列或SharePoint Online訂閱的資料庫。他們也可能會包含 SharePoint 元件。您可以架設在任何網頁主控的堆疊，包括燈外部元件 (Linux、 Apache、 MySQL、 及 PHP) 堆疊。
    
  
- 在新增] 中的自訂商務邏輯具有執行外部元件或JavaScript中自訂 SharePoint 頁面上。
    
  

 [![步驟 1 設定開發環境](images/6d3bbe0a-399e-4747-9e1a-01d42954ce32.png)
  
    
    
](get-started-creating-provider-hosted-sharepoint-add-ins.md#Setup) [![步驟 2 建立應用程式專案](images/d69871f6-c503-463b-bf96-4b6d7306c313.png)
  
    
    
](get-started-creating-provider-hosted-sharepoint-add-ins.md#Create) [![步驟 3 為應用程式進行編碼](images/e5f8a9a2-e5fb-42d1-b19a-300178c626fb.png)
  
    
    
](get-started-creating-provider-hosted-sharepoint-add-ins.md#Code)
  
    
    


## 設定開發環境
<a name="Setup"> </a>

有許多種設定SharePoint Add-ins開發環境。本節說明最簡單的方法。替代項目，請參閱 ＜ [其他資源](#bk_addresources)。
  
    
    

### 要取得之工具


- 如果您已經沒有 **Visual Studio** 2013年或更新版本已安裝，再安裝到在 [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx)使用指示。我們建議使用 [從 Microsoft 下載中心的最新版本](https://www.visualstudio.com/downloads/download-visual-studio-vs)。
    
  
- Visual Studio 包含 **Microsoft Office Developer Tools for Visual Studio** 。有時之間的Visual Studio更新發行版本的工具。若要確認您具備最新版的工具，執行 [的 Visual Studio 2013 的 Office 開發人員工具的安裝程式](http://aka.ms/OfficeDevToolsForVS2013)或 [安裝程式對 Office 開發人員工具的 Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015)。
    
  

### 註冊 Office 365 開發人員網站
<a name="o365_signup"> </a>


> [!注意事項]
> 您可能已有 Office 365 開發人員網站 的存取權：> **您是 MSDN 訂閱者嗎？** Visual Studio Ultimate 和 Visual Studio Premium 的 MSDN 訂閱者可以獲得 Office 365 Developer 訂閱的權益。 [立即兌換權益。](https://msdn.microsoft.com/subscriptions/manage/default.aspx)> **您有下列其中一個 Office 365 訂閱計劃嗎？**> **如果Office 365訂閱的管理員，可以建立開發人員網站** 使用 [Office 365 系統管理中心](https://portal.microsoftonline.com/admin/default.aspx)。如需詳細資訊，請參閱 [建立現有的 Office 365 訂閱開發人員網站](create-a-developer-site-on-an-existing-office-365-subscription.md)。
  
    
    

有三種方法可以取得Office 365計劃。
  
    
    

- 延展免費、 一年以上透過 Office 365 開發人員程式的 Office 365 開發人員帳戶登入。 [取得更多資訊](http://dev.office.com/devprogram)或官方前往 [報名表單](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170)。您將取得電子郵件之後您註冊連結的開發人員程式開發人員帳戶註冊。使用下列指示。
    
  
- 利用一個使用者授權，開始使用 [免費 30 天試用](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK)。
    
  
- 購買  [Office 365 開發人員訂閱](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK)。
    
  

> [!秘訣]
> 開啟另一個視窗或] 索引標籤中的下列連結，以保留方便使用下列指示。
  
    
    


**圖 1。Office 365 開發人員網站網域名稱**

  
    
    

  
    
    
![Office 365 帳戶註冊表單的第 2 頁](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. 第一頁 (不會顯示)，註冊表單是自我闡明;提供要求的資訊，然後選擇 [ **下一步**。
    
  
2. 在第二頁上 (圖 1 中所示)，指定訂閱管理員的使用者識別碼。
    
  
3. 建立的子網域 **。 onmicrosoft.com**; 例如，contoso.onmicrosoft.com。
    
    之後，註冊，您可以使用所產生的認證 (在 @ _yourdomain_格式 _UserID_。 onmicrosoft.com) Office 365入口網站讓您管理您的帳戶登入。您SharePoint Online開發人員網站在您的新網域設定: **http:// _yourdomain_。 sharepoint.com** 。
    
  
4. 選擇 [ **下一步**及填寫表單的最後一個頁面。如果您選擇提供要取得的確認程式碼的電話號碼，您可以提供行動裝置或園地線條電話號碼，但VoIP (Voice over Internet Protocol) 數字。
    
  

    
> [!注意事項]
> 如果您正在登入至另一個 Microsoft 帳戶嘗試註冊的開發人員帳戶時，您可能會看到此訊息： 「 抱歉，您輸入的使用者識別碼沒有運作。它看起來不正確。請確定您輸入您的組織指派給您的使用者識別碼。使用者識別碼通常看起來像是 *someone@example.com*  或 *someone@example.onmicrosoft.com*  。 」> 如果您看到該訊息、 登出您所使用的 Microsoft 帳戶並再試一次。如果您仍會收到郵件，清除 [在瀏覽器快取或切換至 **[InPrivate 瀏覽**並再填寫表單。
  
    
    

註冊程序之後，請在瀏覽器開啟Office 365安裝] 頁面。選擇 [以開啟 [系統管理中心] 頁面的 [系統] 圖示。
  
    
    

**圖 2。Office 365 管理中心頁面**

  
    
    

  
    
    
![顯示 Office 365 系統管理中心的螢幕擷取畫面。](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. 您開發人員網站等候完成設定。佈建完成之後，重新整理 admin center 頁面在瀏覽器中。
    
  
2. 然後選擇 [ **建置增益集**] 連結中左上角的 [頁面] 以開啟您開發人員網站。您應該會看到看起來像圖 3 中的網站。 **在測試增益集**清單] 頁面上的確認網站所使用的 SharePoint 開發人員網站範本進行。如果您看到正常的小組網站而等候數分鐘並再重新啟動您的網站。
    
  
3. 記下的網站 ； URL它用Visual Studio中建立SharePoint Add-ins專案時。
    
  

**圖 3. 具有 [測試階段的增益集] 清單的開發人員網站首頁**

  
    
    

  
    
    
![顯示開發人員網站首頁的螢幕擷取畫面。](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## 建立增益集專案
<a name="Create"> </a>


1. 啟動Visual Studio使用 [ **以系統管理員身分執行**] 選項。
    
  
2. 在Visual Studio、 選擇 [ **檔案**> **新增**> **專案**。
    
  
3. 在 [ **新增專案**] 對話方塊中，展開 [ **Visual C#**節點、 展開 [ **Office/SharePoint** ] 節點，然後選擇 [ **增益集**> **SharePoint 增益集**。
    
  
4. 專案SampleAddIn、 命名並再選擇 **[確定]**。
    
  
5. 第一個 **指定的 SharePoint 增益集設定**] 對話方塊中，執行下列動作：
    
  - 提供您想要使用增益集的偵錯SharePoint網站的完整 URL。這是開發人員網站的 URL。使用 HTTPS，而非 HTTP URL 中。在某些指向此程序期間或短時間內完成後，會提示您登入至這個網站。提示時間而異。使用系統管理員認證。 (在 *。 onmicrosoft.com 網域) 您剛才已註冊您的開發人員網站;例如 MyName@contoso.onmicrosoft.com。
    
  
  - 在 [ **您要裝載您 SharePoint 的增益集方式**，請選擇 **提供者主控**。
    
  
  - 選擇 [下一步]。
    
  
6. 在 **指定的目標 SharePoint 版**] 索引標籤上選擇 [ **SharePoint Online**、，，然後選擇 [ **下一步**。
    
  
7. 下 **您要建立的 web 應用程式專案類型?**，選擇 [ **ASP.NET Web 表單應用程式**。選擇 [ **下一步**]。
    
  
8. 下 **您希望增益集來驗證?**，選擇 [ **使用 Windows Azure Access Control Service**。
    
  
9. 在精靈中，選擇 [ **完成**]。
    
    有多少組態完畢時解決方案會隨即開啟。Visual Studio解決方案- SharePoint Add-in和ASP.NET web 應用程式的另一個中建立兩個專案。
    
  

## 程式碼增益集
<a name="Code"> </a>


1. 開啟 AppManifest.xml 檔案。在 [ **權限**] 索引標籤上指定的 **網站集合**範圍及 「 **讀取**」 權限等級。
    
  
2. 刪除 web 應用程式的 /pages/default.aspx 檔案 **<body>**標籤內的任何標記，然後新增下列 HTML 和ASP.NET控制項 (英文) **<body>**。這則範例會使用 [UpdatePanel](https://msdn.microsoft.com/library/System.Web.UI.UpdatePanel.aspx) 控制來啟用部分頁面轉譯。
    
  ```HTML
  
<form id="form1" runat="server">
  <div>
    <asp:ScriptManager ID="ScriptManager1" runat="server"
            EnablePartialRendering="true" />
    <asp:UpdatePanel ID="PopulateData" runat="server" UpdateMode="Conditional">
      <ContentTemplate>      
        <table border="1" cellpadding="10">
         <tr><th><asp:LinkButton ID="CSOM" runat="server" Text="Populate Data" 
                               OnClick="CSOM_Click" /></th></tr>
         <tr><td>

        <h2>SharePoint Site</h2>
        <asp:Label runat="server" ID="WebTitleLabel"/>

        <h2>Current User:</h2>
        <asp:Label runat="server" ID="CurrentUserLabel" />

        <h2>Site Users</h2>
        <asp:ListView ID="UserList" runat="server">     
            <ItemTemplate >
              <asp:Label ID="UserItem" runat="server" 
                                Text="<%# Container.DataItem.ToString()  %>">
              </asp:Label><br />
           </ItemTemplate>
        </asp:ListView>

        <h2>Site Lists</h2>
               <asp:ListView ID="ListList" runat="server">
                   <ItemTemplate >
                     <asp:Label ID="ListItem" runat="server" 
                                Text="<%# Container.DataItem.ToString()  %>">
                    </asp:Label><br />
                  </ItemTemplate>
              </asp:ListView>
            </td>              
          </tr>
         </table>
       </ContentTemplate>
     </asp:UpdatePanel>
  </div>
</form>
  ```

3. 將下列宣告新增至 web 應用程式的 Default.aspx.cs 檔案。
    
  ```cs
  
using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
  ```

4. Default.aspx.cs 檔案並將 web 應用程式中加入下列變數內 [Page](https://msdn.microsoft.com/library/System.Web.UI.Page.aspx) 類別。
    
  ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
string siteName;
string currentUser;
List<string> listOfUsers = new List<string>();
List<string> listOfLists = new List<string>();
  ```

5. 新增內 [Page](https://msdn.microsoft.com/library/System.Web.UI.Page.aspx) 類別的 `RetrieveWithCSOM`方法。這個方法會用來擷取網站資訊並顯示在頁面上的 SharePoint CSOM。
    
  ```cs
  
// This method retrieves information about the host web by using the CSOM.
private void RetrieveWithCSOM(string accessToken)
{

    if (IsPostBack)
    {
        sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);
    }            

    ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);

    // Load the properties for the web object.
    Web web = clientContext.Web;
    clientContext.Load(web);
    clientContext.ExecuteQuery();

    // Get the site name.
    siteName = web.Title;

    // Get the current user.
    clientContext.Load(web.CurrentUser);
    clientContext.ExecuteQuery();
    currentUser = clientContext.Web.CurrentUser.LoginName;

    // Load the lists from the Web object.
    ListCollection lists = web.Lists;
    clientContext.Load<ListCollection>(lists);
    clientContext.ExecuteQuery();

    // Load the current users from the Web object.
    UserCollection users = web.SiteUsers;
    clientContext.Load<UserCollection>(users);
    clientContext.ExecuteQuery();

    foreach (User siteUser in users)
    {
        listOfUsers.Add(siteUser.LoginName);
    }


    foreach (List list in lists)
    {
        listOfLists.Add(list.Title);
    }
}
  ```

6. 新增內 [Page](https://msdn.microsoft.com/library/System.Web.UI.Page.aspx) 類別的 `CSOM_Click`方法。此方法會觸發事件發生在使用者按一下 [ **填入資料**] 連結。
    
  ```cs
  
protected void CSOM_Click(object sender, EventArgs e)
{
    string commandAccessToken = ((LinkButton)sender).CommandArgument;
    RetrieveWithCSOM(commandAccessToken);
    WebTitleLabel.Text = siteName;
    CurrentUserLabel.Text = currentUser;
    UserList.DataSource = listOfUsers;
    UserList.DataBind();
    ListList.DataSource = listOfLists;
    ListList.DataBind();    
 }
  ```

7. 與此取代現有的 `Page_Load`方法。 `Page_Load`方法來擷取自 `Request`物件的內容並從Microsoft Azure Access Control Service (ACS)取得存取 token TokenHelper.cs 檔案中使用方法。
    
  ```cs
  
// The Page_load method fetches the context token and the access token.
// The access token is used by all of the data retrieval methods.
protected void Page_Load(object sender, EventArgs e)
{
     string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        contextToken =
            TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);
        accessToken =
                    TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                    .AccessToken;

         // For simplicity, this sample assigns the access token to the button's CommandArgument property. 
         // In a production add-in, this would not be secure. The access token should be cached on the server-side.
        CSOM.CommandArgument = accessToken;
    }
    else if (!IsPostBack)
    {
        Response.Write("Could not find a context token.");
        return;
    }
}
  ```

8. 當您完成 Default.aspx.cs 檔案應該看起來如下。
    
  ```cs
  
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;

namespace SampleAddInWeb
{
    public partial class Default : System.Web.UI.Page
    {
        SharePointContextToken contextToken;
        string accessToken;
        Uri sharepointUrl;
        string siteName;
        string currentUser;
        List<string> listOfUsers = new List<string>();
        List<string> listOfLists = new List<string>();

        protected void Page_PreInit(object sender, EventArgs e)
        {
            Uri redirectUrl;
            switch (SharePointContextProvider.CheckRedirectionStatus(Context, out redirectUrl))
            {
                case RedirectionStatus.Ok:
                    return;
                case RedirectionStatus.ShouldRedirect:
                    Response.Redirect(redirectUrl.AbsoluteUri, endResponse: true);
                    break;
                case RedirectionStatus.CanNotRedirect:
                    Response.Write("An error occurred while processing your request.");
                    Response.End();
                    break;
            }
        }

        protected void CSOM_Click(object sender, EventArgs e)
        {
            string commandAccessToken = ((LinkButton)sender).CommandArgument;
            RetrieveWithCSOM(commandAccessToken);
            WebTitleLabel.Text = siteName;
            CurrentUserLabel.Text = currentUser;
            UserList.DataSource = listOfUsers;
            UserList.DataBind();
            ListList.DataSource = listOfLists;
            ListList.DataBind();
        }

        // This method retrieves information about the host web by using the CSOM.
        private void RetrieveWithCSOM(string accessToken)
        {

            if (IsPostBack)
            {
                sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);
            }


            ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);


            // Load the properties for the web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();

            // Get the site name.
            siteName = web.Title;

            // Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

            // Load the lists from the Web object.
            ListCollection lists = web.Lists;
            clientContext.Load<ListCollection>(lists);
            clientContext.ExecuteQuery();

            // Load the current users from the Web object.
            UserCollection users = web.SiteUsers;
            clientContext.Load<UserCollection>(users);
            clientContext.ExecuteQuery();

            foreach (User siteUser in users)
            {
                listOfUsers.Add(siteUser.LoginName);
            }

            foreach (List list in lists)
            {
                listOfLists.Add(list.Title);
            }
        }

        protected void Page_Load(object sender, EventArgs e)
        {
            string contextTokenString = 
                 TokenHelper.GetContextTokenFromRequest(Request);

            if (contextTokenString != null)
            {
                contextToken =
                    TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

                sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);
                accessToken =
                    TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                               .AccessToken;
                CSOM.CommandArgument = accessToken;
            }
            else if (!IsPostBack)
            {
                Response.Write("Could not find a context token.");
                return;
            }
        }
    }
}
  ```

9. 部署及執行增益集使用 F5 鍵。如果您看到要求您信任之自我簽署的 Localhost 憑證 **安全性警訊**視窗，選擇 [ **是**]。
    
    選擇 [ **信任它**在同意] 頁面上授與權限增益集Visual Studio會安裝 IIS express 的 web 應用程式然後安裝增益集測試 SharePoint 網站並啟動它。您會看見具有下列螢幕擷取畫面所示的資料表的頁面。選擇要查看SharePoint網站的摘要資訊 **填入資料**。
    

   **啟動] 頁面上的基本裝載提供者增益集範例**

  

     ![Basic self-hosted app launch page](images/SP15_basicself-hostedapp.gif)
  

  

  

## 後續步驟
<a name="SP15createprovider_nextsteps"> </a>

請參閱 [將 SharePoint 外觀和操作提供增益集](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)以了解如何將增益集整合 SharePoint 的 UI 配置。
  
    
    

## 其他資源
<a name="bk_addresources"> </a>


- 如需設定開發環境，例如"所有內部"環境的其他方法請參閱SharePoint Add-ins表格中目錄的 [ [工具](tools-and-environments-for-developing-sharepoint-add-ins.md)] 區段。
    
  

