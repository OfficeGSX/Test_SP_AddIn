---
title: 替换 SharePoint 外接程序中即将过期的客户端密码
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# 替换 SharePoint 外接程序中即将过期的客户端密码
了解如何对在 AppRegNew.aspx 中注册的 SharePoint 外接程序 添加新的客户端密码。
使用 AppRegNew.aspx 页面注册的 SharePoint 外接程序 客户端密码会在一年后过期。本文介绍了如何添加外接程序的新密码，以及如何创建有效期为三年的新客户端密码。
  
    
    


> [!注释]
> 本文介绍通过组织目录分配且使用 AppRegNew.aspx 页面注册的 SharePoint 外接程序。如果外接程序是通过卖家面板注册的，请参阅 [在卖方仪表板中创建或更新客户端 ID 和密码](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update)。 
  
    
    


## 刷新客户端密码的先决条件

在开始之前，请确保以下内容：
  
    
    

- 开发计算机上已安装  [Microsoft Online Services 登录助手](http://www.microsoft.com/download/details.aspx?id=39267)。
    
  
- 开发计算机上已安装 Microsoft Online Services PowerShell 模块（ [32 位](http://go.microsoft.com/fwlink/p/?linkid=236298)； [64 位](http://go.microsoft.com/fwlink/p/?linkid=236297)）。
    
  
- 您是使用 AppRegNew.aspx 页面注册外接程序的 Office 365 租户的租户管理员（或场的场管理员）。
    
  

## 了解安装到 Office 365 租户的 SharePoint 外接程序的到期日期


  
    
    

1. 打开 Windows PowerShell 并运行以下 cmdlet：
    
  ```
  
Connect-MsolService

  ```

2. 在登录提示符处，输入使用 AppRegNew.aspx 注册外接程序的 Office 365 租赁或场的租户管理员（或场管理员）凭据。
    
  
3. 生成一个报告，其中在下列行中列出每个外接程序及其密钥的到期日期。对于此代码，请注意以下事项：
    
  - 它首先筛选出 Microsoft 自己的应用程序，即仍在开发的外接程序（以及被成为自动托管的已弃用的外接程序类型）。
    
  
  - 然后从剩余的外接程序中筛选出非 SharePoint 外接程序以及使用非对称密钥的外接程序，如工作流。
    
  

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

4. 打开文件 C:\\temp\\appsec.txt 以查看报告。将 Windows PowerShell 窗口保留打开状态，以执行下一过程，如果任何密钥即将到期。
    
  

## 生成新密码


  
    
    

1. 使用以下命令行创建一个客户端 ID，并将 SharePoint 外接程序的客户端 ID 用作参数。
    
  ```
  
$clientId = 'client id of the add-in'

  ```

2. 使用以下命令行生成新的客户端密码：
    
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

3. 新的客户端密码将出现在 Windows PowerShell 控制台上。将其复制到一个文本文件中。在下一个步骤中使用。
    
  

> [!提示]
> 默认情况下，外接程序密钥的有效期为一年。您可以在三次调用 **New-MsolServicePrincipalCredential** cmdlet 时，使用 **-EndDate** 参数将其设置为更短或更长（最长可设置为 3 年）。参数的值必须是设置为从 **DateTime.Now** 不超过 3 年的 [DateTime](http://msdn2.microsoft.com/ZH-CN/library/03ybds8y) 对象。
  
    
    


## 在 Visual Studio 中更新远程 Web 应用程序，以使用新的密码


> [!重要信息]
> 如果您的外接程序最初是使用 Visual Studio Microsoft Office 开发人员工具的预发布版本来创建的，则其中可能包含 TokenHelper.cs（或 .vb）文件的过期版本。如果此文件中未包含"secondaryClientSecret"字符串，则意味着文件已过期，必须在使用新密码更新此 Web 应用程序之前对其进行替换。要获得该文件的发布版本副本，需要 Visual Studio 2012 或更高版本。在 Visual Studio 中创建一个新的 SharePoint 外接程序项目。将其中的 TokenHelper 文件复制到您的 SharePoint 外接程序的 Web 应用程序项目中。 
  
    
    


1. 在 Visual Studio 中打开 SharePoint 外接程序项目，并打开该 Web 应用程序项目的 web.config 文件。在 **appSettings** 部分中，有一些针对客户端 ID 和客户端密码的密钥。下面是一个示例：
    
  ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

  ```

2. 将 **ClientSecret** 密钥的名称改为"SecondaryClientSecret"，示例如下：
    
  ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
  ```

3. 添加新的 **ClientSecret** 密钥，并提供新的客户端密码。现在您的标记应如下所示：
    
  ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
  ```

4. 如果您已经更改为新的 TokenHelper 文件，请重新生成项目。
    
  
5. 重新发布 Web 应用程序。
    
  

## 创建有效期为三年的客户端密码

对于已过期的客户端密码，首先您必须删除给定 **clientId** 的所有过期密码。然后通过 MSO PowerShell 创建一个新密码，并至少等待 24 小时，然后使用新的 **clientId** 和 **ClientSecret** 密钥测试应用。
  
    
    

1. 通过具有以下使用 SharePoint 2013 Windows PowerShell 标记的租户管理员用户连接到 MSOnline。
    
  ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

  ```

2. 获取 **ServicePrincipals** 和密钥。打印 **$keys** 返回三个记录。替换 *KeyId1*  、 *KeyId2*  和 *KeyId3*  中的每个 **KeyId** 。您还将看到每个密钥的 **EndDate** 。确认您的过期密钥是否出现。
    
    **注意：** **clientId** 需要与您的过期 **clientId** 相匹配。建议删除此 **clientId** 的所有密钥（过期和未过期密钥）。
    


  ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

  ```

3. 生成此 **clientID** 的新 **ClientSecret** 。它使用在上述步骤中设置的相同 **clientId** 。新的 **ClientSecret** 的有效期为 3 年。
    
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

4. 复制 **$newClientSecret** 的输出。
    
  
5. 使用此 **ClientId** 和 **ClientSecret** 替换 **Web.config** 。您不需要 **SecondaryClientSecret** 应用设置。
    
  
6. 等待至少 24 小时来将 **ClientSecret** 传播到 SharePoint Office (SPO)。
    
  

## 另请参阅


#### 其他资源


  
    
    
 [SPO 上提供程序托管的应用失败](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
