---
title: Получение удостоверения пользователя и свойств в SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# Получение удостоверения пользователя и свойств в SharePoint 2013
Получение удостоверения пользователя и сведений о пользователе в SharePoint 2013.
Существуют разные способы получения удостоверения и сведений о пользователе, в зависимости от сведений, которые требуется получить. В этой статье рассматриваются некоторые способы, с помощью которых это можно сделать.
  
    
    


## Предварительные требования для получения удостоверения и свойств пользователя
<a name="Prereq"> </a>


- Подготовьте среду разработки надстроек, как описано в статье  [Настройка локальной среды разработки надстроек SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
    
  
- Установите Visual Studio.
    
  
- Установите последнюю версию  [Инструментов разработчика Office для Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) или [Инструментов разработчика Office для Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015).
    
  

> [!Примечание]
> Руководство по настройке среды разработки, соответствующей вашим потребностям, см. в статье  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md). 
  
    
    


### Основные понятия, которые необходимо знать для получения удостоверения и свойств пользователя

В таблице ниже перечислены некоторые полезные статьи, которые помогут разобраться в понятиях, используемых при создании надстроек SharePoint.
  
    
    


|**Статья**|**Описание**|
|:-----|:-----|
| [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) <br/> |Сведения о разрешениях надстроек SharePoint для работы с SharePoint 2013, включая типы разрешений надстроек, уровни запроса разрешений и управление разрешениями. В этой статье также рассматриваются различия между правами разрешений надстройки и правами пользователя.  <br/> |
| [Поток маркеров контекста OAuth для надстроек в SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |Узнайте о проверке подлинности OAuth и потоке авторизации для размещенных в облаке надстроек.  <br/> |
| [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |Узнайте, как создавать базовое размещенное у поставщика Надстройка SharePoint с помощью Инструменты разработчика Office для Visual Studio 2012, как взаимодействовать с сайтами Microsoft SharePoint 2013 с помощью CSOM (клиентской объектной модели) SharePoint, и как реализовать OAuth в Надстройка SharePoint.  <br/> |
   

## Получение удостоверения текущего пользователя веб-сайта
<a name="WebsiteUserID"> </a>

Простейший способ получения удостоверения текущего пользователя веб-сайта состоит в использовании объекта **Web**. С помощью файла TokenHelper.cs в проекте можно получить удостоверение текущего пользователя веб-сайта, используя следующий фрагмент кода.
  
    
    

```cs

ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);
 
 
            //Load the properties for the Web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();
 
            //Get the site name.
            siteName = web.Title;
 
            //Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

```


- При использовании Office 365 полученное имя для входа будет аналогично следующему:  `i:0#.f|membership|adam@contoso.com`.
    
  
- Если вы используете Microsoft SharePoint 2013 локально, и пользователь вошел как обычный пользователь с помощью NTLM, то полученное имя для входа будет аналогично следующему:  `i:0#.w|contoso\\adam`.
    
  
- Если вы используете SharePoint 2013 локально, и пользователь вошел с помощью учетной записи фермы, то полученное имя для входа будет аналогично следующему:  `SHAREPOINT\\System`.
    
  

## Получение удостоверения пользователя с помощью метода ResolvePrincipal
<a name="ResolvePrincipal"> </a>

Далее приводится другой способ получения имени для входа пользователя. Если у вас есть адрес электронной почты или отображаемое имя пользователя, то для получения его имени для входа можно использовать метод  [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) .
  
    
    

> [!Примечание]
> API находятся в пространстве имен Microsoft.SharePoint.Client.Utilities в сборке  [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/ru-ru/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx). 
  
    
    

Далее приводится пример кода, показывающий, как получить сведения для входа пользователя.
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

Значение **Person.LoginName** предоставляет сведения для входа.
  
    
    

## Получение удостоверения и свойств пользователя
<a name="Profile"> </a>

Если требуется получить удостоверение и свойства пользователя, можно использовать маркер OAuth и API социальных компонентов. Сначала получите маркер OAuth и установите его в контекст клиента. В следующем примере кода показывается, как получить свойства профиля пользователя.
  
    
    

```cs

ClientContext clientContext = new ClientContext(<sharepointurl>);
clientContext.AuthenticationMode = ClientAuthenticationMode.Anonymous;
clientContext.FormDigestHandlingEnabled = false;
clientContext.ExecutingWebRequest +=
delegate(object oSender, WebRequestEventArgs webRequestEventArgs)
{                      
    webRequestEventArgs.WebRequestExecutor.RequestHeaders["Authorization"] =
        "Bearer " + accessToken;
};

```

Затем с помощью API  [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) получите свойства пользователя, который использует эту надстройку.
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

Код для работы
  
    
    

- Необходимо настроить и синхронизировать общую службу профилей пользователей в SharePoint 2013 для пользователей.
    
  
- Необходимо добавить в манифест надстройки следующий уровень разрешений для социальных компонентов:
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

API находятся в библиотеке Microsoft.SharePoint.Client.UserProfiles.dll.
  
    
    
Далее приводится еще один пример фрагмента кода, показывающий, как можно получить доступ к хранилищу профилей пользователей.
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## Дополнительные ресурсы
<a name="AdditionalResources"> </a>


-  [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Поток маркеров контекста OAuth для надстроек в SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Надстройки SharePoint](sharepoint-add-ins.md)
    
  
-  [Настройка локальной среды разработки надстроек SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

