---
title: Замена секрета клиента с истекающим сроком действия в надстройке SharePoint
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# Замена секрета клиента с истекающим сроком действия в надстройке SharePoint
Узнайте, как добавить новый секрет клиента для надстройки SharePoint, зарегистрированной на странице AppRegNew.aspx.
Срок действия секретов клиента для надстроек SharePoint, зарегистрированных на странице AppRegNew.aspx, завершается через год. В этой статье описано, как добавить новый секрет клиента для надстройки, а также как создать секрет клиента, действительный в течение трех лет.
  
    
    


> [!Примечание]
> В этой статье рассмотрены Надстройки SharePoint, распределенные с помощью каталога организации и зарегистрированные на веб-странице AppRegNew.aspx. Если для регистрации приложения использована Панель мониторинга продаж, см. раздел  [Создание или обновление идентификаторов и секретов клиентов на Панели мониторинга продаж](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update). 
  
    
    


## Необходимые условия для обновления секрета клиента

Убедитесь в следующем перед началом работы:
  
    
    

-  [Помощник по входу в Microsoft Online Services](http://www.microsoft.com/download/details.aspx?id=39267) установлен на компьютере разработчика.
    
  
- Модуль PowerShell для Microsoft Online Services ( [32-разрядный](http://go.microsoft.com/fwlink/p/?linkid=236298);  [64-разрядный](http://go.microsoft.com/fwlink/p/?linkid=236297)) установлен на компьютере для разработки.
    
  
- Вы  администратор фермы или клиента Office 365, для которого зарегистрирована надстройка на странице AppRegNew.aspx.
    
  

## Просмотр дат истечения срока Надстройки SharePoint, установленных на клиенте Office 365


  
    
    

1. Откройте Windows PowerShell и запустите следующий командлет:
    
  ```
  
Connect-MsolService

  ```

2. При появлении запроса введите учетные данные администратора клиента или администратора фермы для области клиента Office 365 или фермы, где надстройка зарегистрирована с использованием AppRegNew.aspx.
    
  
3. Создайте отчет со списком всех надстроек и датами истечения срока действия их секретов с помощью указанных ниже строк. Обратите внимание на особенности этого кода:
    
  - Сначала он применяет фильтр к приложениям Майкрософт, надстройкам в процессе разработки, а также нерекомендуемым надстройкам  автоматически размещенным.
    
  
  - В последнюю очередь он применяет фильтр к надстройкам не для SharePoint и надстройкам, использующим асимметричные ключи, например рабочие процессы.
    
  

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

4. Откройте файл C:\\temp\\appsec.txt, чтобы просмотреть отчет. Не закрывайте окно Windows PowerShell для выполнения следующей процедуры, если скоро истекает срок действия какого-либо секрета.
    
  

## Создание секрета клиента


  
    
    

1. Создайте переменную ИД клиента со следующем строкой, используя ИД клиента, которое имеет Надстройка SharePoint, в качестве параметра.
    
  ```
  
$clientId = 'client id of the add-in'

  ```

2. Создайте новый секрет клиента со следующими строками:
    
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

3. Новый секрет клиента появится в консоли Windows PowerShell. Скопируйте его в текстовый файл, он будет нужен для следующей процедуры.
    
  

> [!Совет]
> По умолчанию секрет надстройки действителен в течение года. Этот срок можно уменьшить или увеличить (максимум до 3 лет) благодаря трем вызовам командлета **New-MsolServicePrincipalCredential** с помощью параметра **-EndDate**. Значение этого параметра должно быть объектом  [DateTime](http://msdn2.microsoft.com/RU-RU/library/03ybds8y) с датой, не превышающей дату **DateTime.Now** более чем на 3 года.
  
    
    


## Обновление удаленного веб-приложения в Visual Studio для использования нового секрета


> [!Важно!]
> Если ваша надстройка изначально создана с помощью предварительного выпуска Инструменты разработчика Microsoft Office для Visual Studio, она может включать устаревшую версию файла TokenHelper.cs (или .vb). Если файл не содержит строку secondaryClientSecret, замените его, чтобы обновить веб-приложение с использованием нового секрета. Чтобы получить копию файла финальной версии, необходимо воспользоваться Visual Studio 2012 или более поздней версии. Создайте проект надстройки SharePoint в Visual Studio. Скопируйте файл TokenHelper из него в проект веб-приложения своей надстройки SharePoint. 
  
    
    


1. Откройте проект для решения Надстройка SharePoint в Visual Studio и откройте файл web.config для проекта веб-приложения. В разделе **appSettings** находятся ключи для ИД клиента и секрет клиента. Ниже приведен пример.
    
  ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

  ```

2. Измените имя ключа **ClientSecret** на "SecondaryClientSecret", как показано в следующем примере.
    
  ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
  ```

3. Добавьте новый ключ **ClientSecret** и включите в него новый секрет клиента. Результат должен выглядеть следующим образом:
    
  ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
  ```

4. Если вы использовали новый файл TokenHelper, перестройте проект.
    
  
5. Опубликуйте веб-приложение повторно.
    
  

## Создание секрета клиента, действительного на протяжении трех лет

Если истек срок действия секретов клиента, сначала удалите все эти секреты с заданным значением **clientId**. После этого создайте новые с помощью PowerShell для Microsoft Office, подождите хотя бы 24 часа, а затем протестируйте приложение с использованием нового значения **clientId** и ключа **ClientSecret**.
  
    
    

1. Подключитесь к Microsoft Office Online, используя права администратора клиента, приведенный ниже код и Windows PowerShell для SharePoint 2013.
    
  ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

  ```

2. Получите **ServicePrincipals** и ключи. При печатании **$keys** возвращаются три записи. Замените каждое значение **KeyId**  *KeyId1*  , *KeyId2*  и *KeyId3*  . Вы увидите значение **EndDate** для каждого ключа. Убедитесь, что просроченный ключ там отображается.
    
    **Примечание.** Значение **clientId** должно соответствовать просроченному **clientId**. Рекомендуем удалить все ключи для этого **clientId**  и просроченные, и действительные.
    


  ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

  ```

3. Создайте новое значение **ClientSecret** для этого **clientID**. Оно использует то же значение **clientId**, которое указано выше. Новое значение **ClientSecret** действительно в течение 3 лет.
    
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

4. Скопируйте выходные данные **$newClientSecret**.
    
  
5. Замените **Web.config** этими значениями **ClientId** и **ClientSecret**. Параметры приложения **SecondaryClientSecret** не нужны.
    
  
6. Подождите хотя бы 24 часа, пока **ClientSecret** распространится в SharePoint Online (SPO).
    
  

## См. также


#### Другие ресурсы


  
    
    
 [Размещенное у поставщика приложение не работает в SPO](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
