

# Советы и часто задаваемые вопросы. OAuth и удаленные приложений для SharePoint
Эта статья устарела. Перемещенный контент можно найти по ссылкам ниже.
 * **Область применения:*** 
  
    
    


||
|:-----|
|**Содержание**          [Извлечение информации о приложении](#AppInfo)           [Файл AppManifest.xml](#Manifest)           [Файл Web.config](#Webconfig)           [URL-адрес и протокол SSL](#URL)           [Страница перенаправления приложения ](#Redirect)           [URI перенаправления](#RedirectURI)           [Маркеры OAuth](#Tokens)           [Разрешения и области запросов разрешений](#Perm)           [Типы политик авторизации OAuth](#Policy)           [Отладка](#Debugging)           [Локальные приложения с высоким уровнем доверия (приложения для работы между серверами)](#S2S)           [Additional OAuth-related issues](#Misc)           [Социальные функции](#Social)           [Дополнительные ресурсы](#AR)|
   

## Извлечение информации о приложении
<a name="AppInfo"> </a>

Этот контент перемещен в статью  [Получение сведений о регистрации надстройки и субъекте надстройки ](register-sharepoint-add-ins-2013.md#Retrieve).
  
    
    

## Файл AppManifest.xml
<a name="Manifest"> </a>

Этот контент перемещен в следующие статьи:
  
    
    

-  [Изучите структуру манифеста надстройки и пакет надстройки для SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Справка по схеме манифестов приложений для SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  

### Что такое URL-адрес в элементе <StartPage>?

Этот контент перемещен в следующие статьи: 
  
    
    


  
    
    
>  [Изучите структуру манифеста надстройки и пакет надстройки для SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

  
    
    
>  [Элемент StartPage](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)
    
  

### Каковы элементы и атрибуты файла AppManifest.xml?

Этот контент перемещен в статью  [Справка по схеме манифестов приложений для SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
  
    
    

## Файл Web.config
<a name="Webconfig"> </a>

Этот контент перемещен в статью  [Регистрация надстроек для SharePoint 2013](register-sharepoint-add-ins-2013.md), в частности раздел  [Ввод данных регистрации в файлы web.config и AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

### Каковы настройки приложения в файле Web.config?

Этот контент перемещен в раздел  [Ввод данных регистрации в файлы web.config и AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

## URL-адрес и протокол SSL
<a name="URL"> </a>

Этот контент перемещен в следующие статьи: 
  
    
    

-  [Прочие ошибки, связанные с SSL и доменами, в приложениях с высоким уровнем доверия](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Прочие ошибки, связанные с SSL и доменами, в приложениях ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### У моего веб-приложения имеются проблемы с обратным обращением к SharePoint. Что мне следует проверить?

Этот контент перемещен в следующие статьи: 
  
    
    

-  [Прочие ошибки, связанные с SSL и доменами, в приложениях с высоким уровнем доверия](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Прочие ошибки, связанные с SSL и доменами, в приложениях службы контроля доступа](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Какие URL-адреса мне следует жестко задать в приложения для указания на облачный сервер?

Этот контент перемещен в следующие статьи: 
  
    
    

-  [Прочие ошибки, связанные с SSL и доменами, в приложениях с высоким уровнем доверия](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Прочие ошибки, связанные с SSL и доменами, в приложениях службы контроля доступа](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Что мне следует регистрировать: псевдоним CNAME или фактический базовый URL-адрес, на котором размещается приложение?

Этот контент перемещен в следующие статьи: 
  
    
    

-  [Прочие ошибки, связанные с SSL и доменами, в приложениях с высоким уровнем доверия](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Прочие ошибки, связанные с SSL и доменами, в приложениях службы контроля доступа](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Отображается сообщение об ошибке "Базовое подключение было закрыто: ошибка установки доверительных отношений для безопасного канала SSL/TLS". Что делать?

Этот контент перемещен в статью  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed).
  
    
    

## Страница перенаправления приложения
<a name="Redirect"> </a>

Этот контент перемещен в статью  [Получение нового маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Для чего мне следует использовать страницу перенаправления приложений?

Этот контент перемещен в статью  [Получение нового маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Как мне использовать страницу перенаправления приложения, чтобы получить контекстный маркер?

Этот контент перемещен в статью  [Получение нового маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Как мне использовать страницу перенаправления приложений в URL-адресе?

Этот контент перемещен в статью  [Получение нового маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

## URI перенаправления
<a name="RedirectURI"> </a>

Этот контент перемещен в статью  [Регистрация надстроек для SharePoint 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Необходимо ли мне перенаправление URI-адреса?

Этот контент перемещен в статью  [Регистрация надстроек для SharePoint 2013](register-sharepoint-add-ins-2013.md).
  
    
    

## Маркеры OAuth
<a name="Tokens"> </a>

Этот контент перемещен в следующие статьи:
  
    
    

-  [Обработка маркеров безопасности в надстройках с низким уровнем доверия для SharePoint, размещаемых у поставщика](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)
    
  
-  [Создание и использование маркеров доступа в надстройках с высоким уровнем доверия для SharePoint, размещаемых у поставщика](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
    
  

### Что такое контекстный маркер?

Этот контент перемещен в статью  [Понятие о структуре и обработке маркеров контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).
  
    
    

### Что такое маркер доступа?

Этот контент перемещен в статью  [Понятие об обработке маркеров доступа](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens).
  
    
    

### Что такое маркер обновления?

Этот контент перемещен в статью  [Понятие об обработке и кэшировании маркеров обновления](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

### Следует ли сохранять строку контекстного маркера в файле cookie для применения к запросам других страниц непосредственно из приложения с размещением у поставщика?

Этот контент перемещен в статью  [Кэширование маркера контекста или его частей](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).
  
    
    

### Из чего состоит значение ключа кэша? Что делает его уникальным?

Этот контент перемещен в статью  [Понятие о ключе кэша](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey).
  
    
    

### Как извлечь контекстный маркер?

Этот контент перемещен в статью  [Понятие о структуре и обработке маркеров контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).
  
    
    

### Какая информация содержится в контекстном маркере?

Этот контент перемещен в статью  [Пример маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken).
  
    
    

### Какие сведения содержит маркер доступа?

Этот контент перемещен в следующие статьи:
  
    
    

-  [Примеры маркеров доступа в системе авторизации службы контроля доступа](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)
    
  
-  [Пример маркера доступа в системе авторизации с высоким уровнем доверия](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)
    
  

### Как мне рассчитать точное время и дату, опираясь на значения nbf (не ранее) и exp (срок истечения)?

Этот контент перемещен в статью  [Работа со значениями времени JWT](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes).
  
    
    

### Я хочу защитить svc моих приложений от пользователей, не относящихся к SharePoint. Я проверяю допустимость пользователя в точке входа в приложение (создав ClientContext), однако моя служба WCF может быть вызвана любым пользователем. Следует ли создавать ClientContext из контекстного маркера для каждого вызова метода svc?

Этот контент перемещен в статью  [Ограничение доступа только для пользователей SharePoint с помощью маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess).
  
    
    

### Возможно ли оставить AppContext (полученный по запросу SharePoint POST) в качестве скрытого поля ввода на странице?

Этот контент перемещен в статью  [Кэширование маркера контекста или его частей](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).
  
    
    

### Каков срок действия маркера обновления?

Этот контент перемещен в статью  [Понятие об обработке и кэшировании маркеров обновления](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

### Я храню маркер доступа и URL-адрес размещения в файлах cookie, чтобы использовать их при запросах других страниц. Однако мой пользователь сделал перерыв на несколько часов, и действие маркера доступа закончилось до его возвращения. Каковы мои действия?

Этот контент перемещен в следующие статьи:
  
    
    

-  [Понятие об обработке маркеров доступа](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)
    
  
-  [Понятие об обработке и кэшировании маркеров обновления](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)
    
  

### В каком сценарии следует отказаться от старого, но действующего маркера обновления и использовать новый?

Этот контент перемещен в статью  [Понятие об обработке и кэшировании маркеров обновления](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

## Разрешения и области запросов разрешений
<a name="Perm"> </a>

Этот контент находится в статье  [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Каковы области запросов разрешений и доступные права на содержимое списков, библиотек и других возможностей?

Теперь этот контент находится в статье  [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Типы политик авторизации OAuth
<a name="Policy"> </a>

Этот контент находится в статье  [Типы политик авторизации для надстроек в SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### В чем отличие между политикой только для приложения и политикой для пользователя и приложения?

Этот контент находится в статье  [Типы политик авторизации для надстроек в SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### Существует ли способ предоставления или отклонения права на запуск приложения?

Этот контент находится в статье  [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Отладка
<a name="Debugging"> </a>

Этот контент перемещен в следующие статьи: 
  
    
    

-  [Устранение неполадок надстроек с высоким уровнем доверия для SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Устранение неполадок надстроек с низким уровнем доверия для SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Использование Fiddler

Этот контент перемещен в следующие статьи:
  
    
    

-  [Устранение неполадок надстроек с высоким уровнем доверия для SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Устранение неполадок надстроек с низким уровнем доверия для SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

## Локальные приложения с высоким уровнем доверия (приложения для работы между серверами)
<a name="S2S"> </a>

Этот контент перемещен в статью  [Устранение неполадок надстроек с высоким уровнем доверия для SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)
  
    
    

### При работе с высоконадежным приложением я получаю сообщение об ошибке авторизации 401. Каковы мои действия?

Этот контент перемещен в статью  [Устранение неполадок надстроек с высоким уровнем доверия для SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md).
  
    
    

### Как получить контекстный маркер для высоконадежного приложения?

Этот контент перемещен в статью  [Создание и использование маркеров доступа в надстройках с высоким уровнем доверия для SharePoint, размещаемых у поставщика](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md).
  
    
    

## Additional OAuth-related issues
<a name="Misc"> </a>

Этот контент перемещен в следующие статьи:
  
    
    

-  [Устранение неполадок надстроек с высоким уровнем доверия для SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Устранение неполадок надстроек с низким уровнем доверия для SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### При попытке чтения файла методом HTTP DAV возникает ошибка. Что делать?

Этот контент перемещен в статью  [Устранение неполадок надстроек с низким уровнем доверия для SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md).
  
    
    

### Есть ли способ передачи авторизации OAuth другим компонентам в различных доменах или настройки OAuth для нескольких URI?

Да. См. раздел  [Передача маркера доступа в серверные системы](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend).
  
    
    

### Является ли значение участника SharePoint 2013 постоянным?

Да. См. раздел  [Примеры маркеров доступа в системе авторизации службы контроля доступа](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (в частности, таблицу 1).
  
    
    

### Являются ли ID-приложения и секрет приложения постоянными для всех клиентов данного приложения?

Да. Если приложение зарегистрировано в Панель мониторинга продаж, но также отдельно зарегистрировано для каждых клиента или фермы SharePoint, у него могут быть отдельные идентификаторы и секреты. В этом случае Служба контроля доступа Microsoft Azure (ACS) воспринимает его как несколько приложений. См. статью  [Регистрация надстроек для SharePoint 2013](register-sharepoint-add-ins-2013.md).
  
    
    

### Являются ли области уникальными?

Да. См. разделы  [Примеры маркеров доступа в системе авторизации службы контроля доступа](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (в частности, таблицу 1) и [Получение маркера доступа с помощью маркера контекста](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken).
  
    
    

### Как отключить требование HTTPS для OAuth во время разработки?

Этот контент перемещен в раздел  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement).
  
    
    

## Социальные функции
<a name="Social"> </a>

Этот контент перемещен в следующие статьи:
  
    
    

1.  [Социальные функции и функции совместной работы в SharePoint 2013](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)
    
  
2.  [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  

### Как можно извлечь удостоверение и свойства пользователя?

См. статью  [Получение удостоверения пользователя и свойств в SharePoint 2013](get-user-identity-and-properties-in-sharepoint-2013.md).
  
    
    

### Как используются различные социальные функции и области запросов разрешений?

Этот контент находится в разделе  [Начало разработки с использованием социальных функций в SharePoint 2013](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms).
  
    
    

### Как узнать свойства профиля моих подписчиков?

Этот контент перемещен в статью  [Как: получение свойств профиля пользователя с помощью клиентской объектной модели .NET в SharePoint 2013](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp).
  
    
    