---
title: 取代過期的用戶端私人 SharePoint 增益集
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# 取代過期的用戶端私人 SharePoint 增益集
瞭解如何新增新的用戶端密碼的註冊 AppRegNew.aspx SharePoint Add-in 。
註冊使用 AppRegNew.aspx] 頁面的用戶端密碼SharePoint Add-ins的有效期限一年。本文說明如何新增新的密碼的增益集，以及如何建立新的用戶端密碼三年的有效。
  
    
    


> [!注意事項]
> 本文是關於SharePoint Add-ins分散式透過組織目錄及註冊 AppRegNew.aspx 頁面。如果增益集在賣方儀表板註冊，請參閱 [建立或更新用戶端識別碼和賣方儀表板中的機密資料](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update)。
  
    
    


## 重新整理用戶端私人的先決條件

開始之前，請確定下列動作 ︰
  
    
    

-  [Microsoft Online Services 登入小幫手](http://www.microsoft.com/download/details.aspx?id=39267)開發電腦上安裝。
    
  
- Microsoft Online Services PowerShell 模組 ( [32 位元](http://go.microsoft.com/fwlink/p/?linkid=236298); [64 位元](http://go.microsoft.com/fwlink/p/?linkid=236297))開發電腦上已安裝。
    
  
- 您是位置增益集已登錄 AppRegNew.aspx 頁面Office 365租用戶 （或伺服器陣列的伺服器陣列管理員） 的租用戶管理員。
    
  

## 找出安裝在 Office 365 tenancy SharePoint Add-ins的到期日


  
    
    

1. 開啟Windows PowerShell ，然後執行下列 cmdlet:
    
  ```
  
Connect-MsolService

  ```

2. 在登入提示輸入租用戶系統管理員 （或伺服器陣列管理員） Office 365 tenancy 或位置增益集已登錄的 AppRegNew.aspx 伺服器陣列的認證。
    
  
3. 產生報表會列出每一個增益集和日期，其密碼過期的下列行。請注意下列有關此程式碼 ︰
    
  - 第一次以篩選出 Microsoft 自己的應用程式增益集仍在開發 （與現在已遭取代類型的增益集，稱為 autohosted）。
    
  
  - 從其他部分，它會篩選出非 SharePoint 增益集和使用非對稱式按鍵，例如工作流程的增益集。
    
  

  ```
  
$applist = Get-MsolServicePrincipal -all  |Where-Object -FilterScript { ($_.DisplayName -notlike "*Microsoft*") -and ($_.DisplayName -notlike "autohost*") -and  ($_.ServicePrincipalNames -notlike "*localhost*") }

foreach ($appentry in $applist)
{
    $principalId = $appentry.AppPrincipalId
    $principalName = $appentry.DisplayName
    
    Get-MsolServicePrincipalCredential -AppPrincipalId $principalId -ReturnKeyValues $false | Where-Object { ($_.Type -ne "Other") -and ($_.Type -ne "Asymmetric") }
    
     $date = get-date
     Write-Host "$principalName;$principalId;$appentry.KeyId;$appentry.type;$date;$appentry.Usage"

}  > c:\\temp\\appsec.txt
  ```

4. 開啟的檔案來查看報告 C:\\temp\\appsec.txt。保持Windows PowerShell視窗開啟下一個程序，如果有任何的機密資料為快到期。
    
  

## 產生新的密碼


  
    
    

1. 使用下列命令，做為參數使用的SharePoint Add-in用戶端識別碼建立用戶端識別碼變數。
    
  ```
  
$clientId = 'client id of the add-in'

  ```

2. 產生新的用戶端密碼的下列行 ︰
    
  ```
  
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Sign -Value $newClientSecret
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Verify -Value $newClientSecret
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Password -Usage Verify -Value $newClientSecret
$newClientSecret
  ```

3. 新的用戶端機密會出現在Windows PowerShell主控台。將其複製到文字檔案。您可以用它在下一個程序。
    
  

> [!秘訣]
> 根據預設，該增益集機密持續時間一年。您可以設定此為較短或再 （上限 3 年） **New-MsolServicePrincipalCredential**指令程式的三個的電話上使用 **-EndDate**參數。參數的值必須是設定為不能超過 3 年從 **DateTime.Now** [DateTime](http://msdn2.microsoft.com/ZH-TW/library/03ybds8y)物件。
  
    
    


## 更新遠端 web 應用程式在 Visual Studio 中使用新的密碼


> [!重要]
> 如果增益集原先建立以預先發行版本Microsoft Office Developer Tools for Visual Studio，它可能包含過時 TokenHelper.cs （或.vb） 檔案的版本。如果檔案不包含 「 secondaryClientSecret 」 的字串，則過期，必須會之前，您可以使用新的密碼來更新 web 應用程式被取代。若要取得的發行版本檔案的複本，您會需要Visual Studio 2012年或更新版本。建立新的SharePoint Add-in專案中Visual Studio。複製 TokenHelper 檔案，到您SharePoint Add-in的 web 應用程式專案。
  
    
    


1. 開啟SharePoint Add-in專案中Visual Studio，並開啟 web 應用程式專案的 web.config 檔案。在 [ **appSettings** ] 區段中，有的用戶端識別碼和用戶端私人的按鍵。以下是範例 ︰
    
  ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

  ```

2. 「SecondaryClientSecret」 中變更 **ClientSecret**索引鍵的名稱，如下列範例所示 ︰
    
  ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
  ```

3. 新增新的 **ClientSecret**鍵，並提供您新的用戶端密碼。您的標記現在看起來應該如下所示 ︰
    
  ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
  ```

4. 若要變更新 TokenHelper 檔案，重建專案。
    
  
5. 重新發佈 web 應用程式。
    
  

## 建立三年是有效的用戶端密碼

過期的用戶端機密的第一次您必須刪除所有的指定 **clientId** 過期的機密資料。然後您建立一個新的 MSO PowerShell，等待至少 24 小時，然後以新的 **clientId** 和 **ClientSecret**金鑰測試應用程式。
  
    
    

1. 連線至 MSOnline 使用租用戶管理員使用者使用 SharePoint 2013 Windows PowerShell標記下方。
    
  ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

  ```

2. 取得 **ServicePrincipals** 與索引鍵。列印 **$keys**傳回三筆記錄。取代 *KeyId1*  、 *KeyId2*  和 *KeyId3*  中每個 **金鑰識別碼為** 。您也會看到每個索引鍵的 **結束日期** 。確認是否您過期鍵顯示那里。
    
    **附註 ︰** **ClientId** 必須符合您過期的 **clientId** 。建議您刪除所有索引鍵，同時過期，並會過期，為此 **clientId** 。
    


  ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

  ```

3. 產生 **clientID** 此新 **ClientSecret** 。它會在上述步驟中設定為使用相同的 **clientId** 。新的 **ClientSecret** 3 年是有效的。
    
  ```
  
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$dtStart = [System.DateTime]::Now
$dtEnd = $dtStart.AddYears(3)
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Sign -Value $newClientSecret -StartDate $dtStart  -EndDate $dtEnd
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Verify -Value $newClientSecret   -StartDate $dtStart  -EndDate $dtEnd
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Password -Usage Verify -Value $newClientSecret   -StartDate $dtStart  -EndDate $dtEnd
$newClientSecret

  ```

4. 複製之 **$newClientSecret** 的成果。
    
  
5. 使用此 **ClientId** 和 **ClientSecret** 取代 **Web.config** 。您不需要 **SecondaryClientSecret** 應用程式設定。
    
  
6. 請等候至少 24 小時，才會傳播 **ClientSecret** SharePoint Office (SPO)。
    
  

## 請參閱


#### 其他資源


  
    
    
 [在 SPO 的提供者主控應用程式時失敗](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
