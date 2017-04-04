---
title: SharePoint アドインで期限が切れたクライアント シークレットを置換する方法
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# SharePoint アドインで期限が切れたクライアント シークレットを置換する方法
AppRegNew.aspx で登録されている SharePoint アドイン のために新しいクライアント シークレットを追加する方法を説明します。
AppRegNew.aspx ページを使用して登録されている SharePoint アドイン のクライアント シークレットは、1 年後に期限が切れます。この資料では、アドイン用の新しいシークレットを追加する方法に加え、3 年間有効な新しいクライアント シークレットを作成する方法ついて説明します。
  
    
    


> [!メモ]
> この記事は、組織のカタログを通じて配布され、AppRegNew.aspx ページを使用して登録された SharePoint アドインについて書かれています。アドインが 販売者ダッシュボード上で登録されたものである場合は、「 [販売者ダッシュボードでクライアント ID とクライアント シークレットを作成または更新する](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update)」を参照してください。 
  
    
    


## クライアント シークレットを更新するための前提条件

開始する前に以下の点を確認します。
  
    
    

-  [Microsoft Online Services サインイン アシスタント](http://www.microsoft.com/download/details.aspx?id=39267) が開発コンピューターにインストールされている。
    
  
- Microsoft Online Services PowerShell Module ( [32 ビット](http://go.microsoft.com/fwlink/p/?linkid=236298);  [64 ビット](http://go.microsoft.com/fwlink/p/?linkid=236297)) が開発コンピューターにインストールされている。
    
  
- AppRegNew.aspx ページでアドインが登録されている Office 365 テナントのテナント管理者 (またはファームのファーム管理者) であること。
    
  

## Office 365 テナンシーにインストールされた SharePoint アドインの有効期限の確認


  
    
    

1. Windows PowerShell を開き、以下のコマンドレットを実行します。
    
  ```
  
Connect-MsolService

  ```

2. ログイン プロンプトで、AppRegNew.aspx でアドインが登録されている Office 365 テナンシーまたはファームのテナント管理者 (またはファーム管理者) の資格情報を入力します。
    
  
3. 次の行を使用して、各アドインとそれぞれのシークレットの有効期限を一覧表示するレポートを作成します。このコードについては以下の点に注意してください。
    
  - 最初に、マイクロソフト独自のアプリケーション、開発中のアドイン (および自動ホスト型と呼ばれていた現在使用されなくなったタイプのアドイン) を除外します。
    
  
  - 残ったものの中から、非 SharePoint アドインと、ワークフローなどの非対称暗号鍵を使用するアドインを除外します。
    
  

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

4. C:\\temp\\appsec.txt ファイルを開いてレポートを表示します。いずれかのシークレットが期限切れ間近である場合は、次の手順のために Windows PowerShell ウィンドウを開いたままにします。
    
  

## 新しいシークレットの生成


  
    
    

1. 以下の行で、SharePoint アドインのクライアント ID をパラメーターとして使用し、クライアント ID 変数を作成します。
    
  ```
  
$clientId = 'client id of the add-in'

  ```

2. 以下の行で、新しいクライアント シークレットを生成します。
    
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

3. 新しいクライアント シークレットが Windows PowerShell コンソールに表示されます。それをテキスト ファイルにコピーします。これは次の手順で使用します。
    
  

> [!ヒント]
> 既定で、アドイン シークレットの有効期限は 1 年です。この設定は、 **New-MsolServicePrincipalCredential** コマンドレットの 3 つの呼び出しで **-EndDate** パラメーターを使用することにより、短くまたは長くする (最大 3 年) ことができます。このパラメーターの値は **DateTime.Now** から 3 年以内に設定された [DateTime](http://msdn2.microsoft.com/JA-JP/library/03ybds8y) オブジェクトでなければなりません。
  
    
    


## Visual Studio でリモート Web アプリケーションを更新し、新しいシークレットを使用する


> [!重要]
> アドインがプレリリース バージョンの Microsoft Office Developer Tools for Visual Studio を使用して作成されている場合、期限が切れたバージョンの TokenHelper.cs (または .vb) ファイルが含まれている可能性があります。ファイルに文字列 "secondaryClientSecret" が含まれていない場合、このファイルは期限が切れており、新しいシークレットで Web アプリケーションを更新する前にファイルを置き換える必要があります。ファイルのリリース バージョンのコピーを入手するには、Visual Studio 2012 以降が必要です。Visual Studio で新しい SharePoint アドイン プロジェクトを作成します。TokenHelper ファイルをそのプロジェクトから SharePoint アドインの Web アプリケーション プロジェクトにコピーします。 
  
    
    


1. Visual Studio で SharePoint アドイン プロジェクトを開き、Web アプリケーション プロジェクトの web.config ファイルを開きます。 **appSettings** セクションには、クライアント ID およびクライアント シークレットのキーがあります。以下にその例を示します。
    
  ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

  ```

2. 以下の例に示されているように、 **ClientSecret** キーの名前を "SecondaryClientSecret" に変更します。
    
  ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
  ```

3. 新しい **ClientSecret** キーを追加し、新しいクライアント シークレットを指定します。マークアップは以下のようになるはずです。
    
  ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
  ```

4. 新しい TokenHelper ファイルに変更した場合は、プロジェクトを再構築します。
    
  
5. Web アプリケーションを再発行します。
    
  

## 3 年間有効なクライアント シークレットを作成する

期限が切れたクライアント シークレットの場合、特定の **clientId** のすべての期限切れのシークレットをまず削除する必要があります。それから MSO PowerShell で新しいシークレットを作成し、少なくとも 24 時間待ってから、アプリを新しい **clientID** と **ClientSecret** キーでテストしてください。
  
    
    

1. SharePoint 2013 Windows PowerShell を使用して、以下のマークアップを持つテナント管理者ユーザーを使って MSOnline に接続します。
    
  ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

  ```

2. **ServicePrincipals** とキーを取得します。 **$keys** を印刷すると、3 つのレコードが返されます。 *KeyId1*  、 *KeyId2*  、 *KeyId3*  でそれぞれの **KeyId** を置換します。それぞれのキーの **EndDate** も表示されます。期限の切れたキーが表示されるか確認してください。
    
    **注記:** **clientId** は、期限切れの **clientId** と一致する必要があります。この **clientId** のためのすべてのキー (期限が切れているものと切れていないもの両方) を削除することをお勧めします。
    


  ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

  ```

3. この **clientID** のための新しい **ClientSecret** を作成します。上記の手順で設定されたものと同じ **clientId** を使用します。新しい **ClientSecret** は 3 年間有効です。
    
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

4. **$newClientSecret** の出力をコピーしてください。
    
  
5. **Web.config** をこの **ClientId** と **ClientSecret** で置換してください。 **SecondaryClientSecret** アプリ設定は必要はありません。
    
  
6. **ClientSecret** が SharePoint Office (SPO) に伝達されるのに少なくとも 24 時間待ってください。
    
  

## 関連項目


#### その他の技術情報


  
    
    
 [プロバイダー向けホスト型アプリが SPO で失敗](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
