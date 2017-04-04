
# 建置 SharePoint 的應用程式
找出哪些SharePoint Add-ins  以及如何建立它們使用如 HTML5、 JavaScript 和 OAuth 的標準技術。深入應用程式開發生命週期。了解如何啟動您的應用程式、 如何設計和開發應用程式，以及希望當您準備好部署SharePoint Add-in。
 * **適用於:*** 
  
    
    

 **本文內容**
  
    
    
 [在SharePoint 2013? SharePoint Add-in是什麼](#SP15_bk_buildSPapps)
  
    
    
 [開始開發SharePoint Add-ins](#SP15GettingStarted_WhatDoYouNeed)
  
    
    
 [設計SharePoint Add-ins](#SP15GettingStarted_WhatCanYouDo)
  
    
    
 [開發SharePoint Add-ins](#SP15GettingStarted_LearnMore)
  
    
    
 [發佈SharePoint Add-ins](#SP15_Gettingstarted_publish)
  
    
    
 [傳送意見反應](#bk_feedback)
  
    
    
 [其他資源](#SP15_Gettingstarted_addlresources)
 **的程式碼：** [SharePoint 範例套件的應用程式](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
  
    
    


## 在SharePoint 2013? SharePoint Add-in是什麼
<a name="SP15_bk_buildSPapps"> </a>

在SharePoint 2013，現在有 sharepoint 建立應用程式的能力。什麼是最佳的解決方案，帶來較淺的耗用量，並使用標準架構的技術，例如 HTML5、 JavaScript 和 OAuth 所述的 app?。應用程式具有較淺的耗用量，因為他們不實際安裝在主機伺服器上，而且這表示它們不多載具有過多的 API 呼叫的 SharePoint 網站。相反地，您有各種不同的 [裝載應用程式的選項](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)可讓您彈性地管理獨立應用程式套件新增至 SharePoint 網站的那些服務。若要瞭解更多的 [SharePoint Add-ins](sharepoint-add-ins.md) ，請參閱。
  
    
    
在這個區段的文件中，我們大綱所有您需要事先知道要建置絕佳的應用程式的概念。文件的組織，帶領您逐步我們建議您的應用程式開發生命週期  開始、 設計、 開發和更新版本中，發行。圖 1] 顯示您的生命週期的外觀。
  
    
    

**[圖 1。SharePoint 開發的應用程式的生命週期**

  
    
    

  
    
    
![Start, design, develop, and publish apps](images/SP15_Buildapps.gif)
  
    
    

  
    
    

  
    
    

## 開始開發SharePoint Add-ins
<a name="SP15GettingStarted_WhatDoYouNeed"> </a>

在 [ **開始** ]，我們告訴您所有基本項目  有關取得設定與 Office 365 的開發人員，有關應用程式模型，在 [一般] 下，以及探索的技術，您必須了解，或是您可能已經知道，以及可以立即套用您SharePoint Add-in的開發。我們已提供連結更深入的資訊、 程式碼範例和使用說明的工作，以幫助您開始建置絕佳的應用程式。如果我們成功提供您需要知道在 **啟動** 時，您應該有您要瀏覽文件的其餘部分的所有資訊。如果答案是，我們希望您得知的意見反應，我們可以讓文件更好 ！這一系列的文件是只是預覽 - 我們必須不斷地更新根據客戶意見反應與問題的主題提供新的發行項。請參閱 [傳送意見反應](#bk_feedback)的方法給我們的聯絡和參與製作的文件更好!
  
    
    
 [直接跳到開始](d07e0a13-1e74-4128-857a-513dedbfef33.md)
  
    
    

## 設計SharePoint Add-ins
<a name="SP15GettingStarted_WhatCanYouDo"> </a>

例如，假設您有殺人的主意，為應用程式。在此區段中，我們將引導您完成您要製作及提供建置您的應用程式的最佳作法的設計決策。例如，有何良好的使用者介面？「 形狀 」 可用的應用程式有哪些？我何時應該使用而不是另一項？存取資料是否有哪些選項？您取得的圖片。
  
    
    
 [直接跳到設計](design-sharepoint-add-ins.md)
  
    
    

## 開發SharePoint Add-ins
<a name="SP15GettingStarted_LearnMore"> </a>

 **發展** 是最基本的文件部份。我們必須深入的概觀、 使用說明文章等和程式碼片段，協助說明您可以在SharePoint Add-in中的所有不同項目。您會發現相關執行 CRUD 作業清單上的文件、 如何建置其他查詢，以及如何及何時進行互動的新的 Api，來設定安全性，以及如何 SharePoint，SharePoint 之豐富程度帶入您應用程式具有企業共享功能，例如活動摘要和使用者設定檔，以及企業內容管理功能、 LOB 互通性功能，以及可能造成您的應用程式的網站設計功能突顯出來的 OAuth。深入了解他們在 [新增 SharePoint 2013 功能](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx)中。
  
    
    
而且，程式碼是索引鍵，所以看一下 「 範例 」 功能表中開發人員中心。它是我們的應用程式的程式碼範例的直接連結。一旦設定好您的開發環境，您應該檢查出幾個我們的範例。善用社群功能，可讓您要求的程式碼範例，如果我們沒有您想要查看一個。我們對這些要求，以及其他文件意見反應，並在我們持續更新內容和範例中使用它們。因此，請讓我們知道是否您想要看到!
  
    
    
 [直接跳到開發](develop-sharepoint-add-ins.md)
  
    
    

## 發佈SharePoint Add-ins
<a name="SP15_Gettingstarted_publish"> </a>

現在您已經建置您的應用程式，並且已經準備共用它。 **發行** 會教您如何讓您的使用者都可以使用您的應用程式。您可以發佈至兩個位置的其中一個應用程式來執行這項操作：
  
    
    

- **公用的Office Store。**將您的應用程式發佈到Office Store讓應用程式公開使用，以便它可以取得任何 SharePoint 部署的使用者。
    
  
- **內部組織應用程式類別目錄。**將您的應用程式發行至內部組織應用程式類別目錄中，位於您的 SharePoint 部署，讓使用者存取該 SharePoint 部署使用。
    
  
如果您決定要送出您Office Store的應用程式，看看 [送出到 Office 市集的 Office 與 SharePoint 增益集和 Office 365 web 應用程式](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx)。本節說明您如何使用賣方儀表板來提交 SharePoint 或 Office 存放區的 Office 應用程式。它也包含我們驗證的指導原則，讓我們用來驗證Office Store中所列出的應用程式。
  
    
    
 [直接跳到發佈](publish-sharepoint-add-ins.md)
  
    
    

## 傳送意見反應
<a name="bk_feedback"> </a>

在 SDK 中的每個發行項有"是以本主題有用嗎?"連結上方，而且沒有可協助您更能表現您有問題的自由格式註解區段。請使用這些來將您的意見。我們監視此每日，並根據您的建議的變更。
  
    
    
您也可以修改每個使用社群的 [新增] 區段底部的每個主題的主題。我們也監視此每日，並根據您的建議的變更。
  
    
    
您是否有意見我們 doing? 的方式傳送意見反應的另一個方法是透過電子郵件。在 [docthis@microsoft.com](mailto:docthis@microsoft.com)與我們連絡。
  
    
    
如果您有支援問題，得到快速的回應的最好方法是公佈到 [SharePoint 論壇 MSDN 上的應用程式](http://social.msdn.microsoft.com/Forums/en-US/appsforsharepoint)。
  
    
    

## 其他資源
<a name="SP15_Gettingstarted_addlresources"> </a>


-  [Reimagine SharePoint 開發](http://msdn.microsoft.com/en-US/office/apps/dn133840)
    
  
-  [開始開發 SharePoint 應用程式](d07e0a13-1e74-4128-857a-513dedbfef33.md)
    
  
-  [設計 SharePoint 增益集](design-sharepoint-add-ins.md)
    
  
-  [開發 SharePoint 的增益集](develop-sharepoint-add-ins.md)
    
  
-  [發佈 SharePoint 增益集](publish-sharepoint-add-ins.md)
    
  
-  [工具及開發 SharePoint 的增益集的環境](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
-  [選擇如何開發和裝載您 SharePoint 的增益集的模式](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)
    
  
-  [在 SharePoint 2013 中的協助工具](http://msdn.microsoft.com/library/c9106d47-c523-49c1-b9f1-cdd7420abd5e%28Office.15%29.aspx)
    
  
