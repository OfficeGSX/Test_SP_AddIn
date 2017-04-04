---
title: Создание размещаемой у поставщика надстройки SharePoint, которая получает доступ к данным SAP через шлюз SAP для Microsoft
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# Создание размещаемой у поставщика надстройки SharePoint, которая получает доступ к данным SAP через шлюз SAP для Microsoft
Сведения о том, как создать надстройку SharePoint с доступом к данным SAP.
Вы можете создать надстройку SharePoint, которая считывает и записывает данные SAP, а при необходимости  и данные SharePoint, с помощью Шлюз SAP для Майкрософт и библиотеки аутентификации Azure AD для .NET. В этой статье описано, как создать надстройку SharePoint для получения авторизованного доступа к SAP. 
  
    
    


## Подготовка к работе

Ниже представлены необходимые условия для процедур, описанных в этой статье.
  
    
    

- **Сайт разработчиков Office 365**  это домен Office 365, связанный с подпиской Microsoft Azure Active Directory. См. статью [Настройка среды для разработки надстроек SharePoint в Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) или [Создание сайта разработчика с использованием актуальной подписки на Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md).
    
  
- **Visual Studio 2013 с обновлением 2** или более поздней версии, которую можно приобрести на странице [Добро пожаловать в Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx).
    
  
- **Инструменты разработчика Microsoft Office для Visual Studio**. Версия, включенная в обновление 2 для Visual Studio 2013 или более позднюю версию.
    
  
- **Шлюз SAP для Майкрософт**, развернутый и настроенный в Microsoft Azure. Подробнее см. в документации по  [шлюзу SAP для Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Учетная запись организации в Microsoft Azure**. См. статью  [Вручную добавьте в ваше приложение API-интерфейсов Office 365 Preview общего согласия](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount).
    
    > **Примечание**
      > После создания учетной записи Office 365 (login.microsoftonline.com) войдите в нее, чтобы изменить временный пароль. 
- **Конечная точка OData для SAP** с примером данных. См. документацию по [шлюзу SAP для Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Владение Azure AD на базовом уровне.** См. статью [Начало работы с Azure AD](http://msdn.microsoft.com/library/azure/dn655157.aspx).
    
  
- **Умение создавать простые Надстройки SharePoint.** См. статью [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md).
    
  
- **Понимание основных принципов работы OAuth 2.0 в Azure AD**. См. статью  [OAuth 2.0 в Azure AD](http://msdn.microsoft.com/library/azure/dn645545.aspx) и ее подразделы.
    
  
 **Пример кода:** [SharePoint 2013: использование шлюза SAP для Microsoft в надстройке SharePoint](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)
  
    
    

## Понимание аутентификации и авторизации в Шлюз SAP для Майкрософт и SharePoint
<a name="AuthOverview"> </a>

OAuth 2.0 в Azure AD позволяет приложениям получать доступ к различным ресурсам, размещенным в Microsoft Azure, например Шлюз SAP для Майкрософт. В OAuth 2.0 приложения и пользователи  субъекты безопасности. Субъектам приложений, как и пользователям (а иногда и вместо пользователей), требуется аутентификация и авторизация на защищенных ресурсах. В этом процессе участвует поток OAuth, в котором приложение (например, Надстройка SharePoint) получает маркеры доступа и обновления. Первый из двух маркеров принимают все службы и приложения, размещенные в Microsoft Azure и использующие Azure AD в качестве сервера авторизации OAuth 2.0. Этот процесс во многом аналогичен тому, как удаленные компоненты размещенного у поставщика приложения для SharePoint авторизируются в SharePoint (см. статью  [Создание надстроек для SharePoint, которые используют авторизацию с низким уровнем доверия](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) и ее подразделы). Однако система авторизации для контроля доступа использует в качестве доверенного поставщика маркеров Служба контроля доступа Microsoft Azure (ACS), а не Azure AD.
  
    
    

> **Совет**
> Если Надстройка SharePoint получает доступ к SharePoint, а не только к Шлюз SAP для Майкрософт, она должна использовать  *обе*  системы: Azure AD и систему авторизации для контроля доступа (чтобы получить маркеры доступа к Шлюз SAP для Майкрософт и SharePoint соответственно). Маркеры из этих двух систем не являются взаимозаменяемыми. Дополнительные сведения см. в разделе [(Необязательно) Предоставление приложению ASP.NET доступа к SharePoint](#SharePoint). 
  
    
    

Подробное описание и схему потока OAuth, используемого протоколом OAuth 2.0 в Azure AD, см. в статье  [Поток предоставления кода авторизации](http://msdn.microsoft.com/library/azure/dn645542.aspx). Похожее описание и схему потока доступа к SharePoint см. в статье  [Этапы потока маркеров контекста](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps).
  
    
    

## Создание надстройки SharePoint
<a name="AuthOverview"> </a>


### Создание решения Visual Studio


1. Чтобы создать проект **Надстройка SharePoint** в Visual Studio, выполните указанные ниже действия. В примере из этой статьи используется язык C#, но вы также можете создать проект надстройки SharePoint с помощью раздела **Visual Basic** шаблона нового проекта.
    
1. В **мастере создания надстройки SharePoint** присвойте проекту имя и нажмите кнопку **ОК**. В нашем примере используется SAP2SharePoint.
    
  
2. Укажите URL-адрес домена Сайт разработчиков Office 365 (включая косую черту в конце) в качестве сайта отладки. Пример: https://<домен_O365>.sharepoint.com/. Выберите тип надстройки **Размещено у поставщика**. Нажмите кнопку **Далее**.
    
  
3. Выберите тип проекта. В нашем примере используется тип **Приложение веб-форм ASP.NET**. Вы также можете создавать приложения на основе ASP.NET и MVC, которые получают доступ к Шлюз SAP для Майкрософт. Нажмите кнопку **Далее**.
    
  
4. Выберите Azure ACS в качестве системы аутентификации. Надстройка SharePoint будет использовать эту систему при доступе к SharePoint. Она не используется при доступе к Шлюз SAP для Майкрософт. Нажмите кнопку **Готово**.
    
  
2. После создания проекта, вам будет предложено войти в учетную запись Office 365. Используйте учетные данные администраторы учетных записей, например Bob@<домен_O365>.onmicrosoft.com.
    
  
3. Решение Visual Studio содержит два проекта: основной проект надстройки SharePoint и проект веб-форм ASP.NET. Добавьте пакет **Библиотека аутентификации Active Directory** (ADAL) к проекту ASP.NET, выполнив следующие действия.
    
1. Щелкните правой кнопкой мыши папку **Ссылки** проекта ASP.NET (в приведенном примере он имеет название **SAP2SharePointWeb**) и выберите команду **Manage NuGet Packages** (Управление пакетами NuGet).
    
  
2. В левой части открывшегося диалогового окна выберите пункт **В сети**. Введите в поле поиска запрос Microsoft.IdentityModel.Clients.ActiveDirectory.
    
  
3. Когда в результатах поиска появится библиотека ADAL, нажмите кнопку **Установить** рядом с ней и примите условия лицензионного соглашения.
    
  
4. Добавьте пакет Json.net к проекту ASP.NET, выполнив следующие действия.
    
1. Введите в поле поиска запрос Json.net. Если появится слишком много результатов, попробуйте выполнить поиск по запросу Newtonsoft.json.
    
  
2. Когда в результатах поиска появится библиотека Json.net, нажмите кнопку **Установить** рядом с этим пунктом.
    
  
5. Нажмите кнопку **Закрыть**.
    
  

### Регистрация веб-приложения в Azure AD


1. Войдите на  [портал управления Azure](https://manage.windowsazure.com), используя учетную запись администратора Azure.
    
    > **Примечание**
      > В целях безопасности не рекомендуется использовать учетную запись администратора при разработке надстроек. 
2. Выберите **Active Directory** в левой части страницы.
    
  
3. Щелкните свой каталог. 
    
  
4. Выберите **ПРИЛОЖЕНИЯ** (на верхней панели навигации).
    
  
5. На панели инструментов в нижней части экрана нажмите **Добавить**.
    
  
6. В открывшемся диалоговом окне выберите **Добавить приложение, разрабатываемое моей организацией**.
    
  
7. В диалоговом окне **ДОБАВЛЕНИЕ ПРИЛОЖЕНИЯ** укажите имя приложения. В нашем примере используется имяContosoAutomobileCollection. 
    
  
8. Выберите тип приложения **Веб-приложение и/или веб-API** и щелкните стрелку вправо.
    
  
9. На второй странице диалогового окна укажите URL-адрес отладки SSL из проекта ASP.NET решения Visual Studio в поле **URL-АДРЕС ВХОДА**. Вы можете найти URL-адрес следующим образом. ** *(Для начала необходимо зарегистрировать надстройку с URL-адресом отладки, чтобы иметь возможность запускать отладчик Visual Studio (клавиша F5). После этого надстройку можно будет зарегистрировать с промежуточным URL-адресом Веб-сайт Azure.  [Изменение и подготовка надстройки в Azure и Office 365](#Stage).)* **
    
1. Выделите проект ASP.NET в **обозревателе решений**. 
    
  
2. В окне **Свойства** скопируйте значение свойства **URL-адрес SSL**. Пример: https://localhost:44300/.
    
  
3. Вставьте его в поле **URL-АДРЕС ВХОДА** диалогового окна **ДОБАВЛЕНИЕ ПРИЛОЖЕНИЯ**.
    
  
10. В качестве **URI КОДА ПРИЛОЖЕНИЯ** укажите уникальный URI-адрес. например имя приложения, добавленное к URL-адресу SSL:https://localhost:44300/ContosoAutomobileCollection.
    
  
11. Нажмите кнопку с галочкой. Откроется панель управления приложением Azure с сообщением об успешном завершении.
    
  
12. Выберите пункт **НАСТРОЙКА** в верхней части страницы.
    
  
13. Найдите **КОД КЛИЕНТА** и скопируйте его. Он пригодится для дальнейших действий.
    
  
14. Создайте ключ в разделе **Ключи**. Он появится не сразу. Нажмите кнопку **СОХРАНИТЬ** в нижней части страницы, и ключ станет видимым. Скопируйте его  он понадобится позже.
    
  
15. Прокрутите вниз до раздела **Разрешения для других приложений** и выберите свое приложение-службу Шлюз SAP для Майкрософт.
    
  
16. Откройте раскрывающийся список **Делегированные разрешения** и выберите разрешения службы Шлюз SAP для Майкрософт, которые будет использовать ваша Надстройка SharePoint.
    
  
17. Выберите элемент **СОХРАНИТЬ** в нижней части экрана.
    
  

### Настройка приложения для взаимодействия с Azure AD


1. В Visual Studio откройте файл web.config проекта ASP.NET.
    
  
2. В разделе  `<appSettings>` в Инструменты разработчика Office для Visual Studio добавлены элементы для **ClientID** и **ClientSecret**, которые использует Надстройка SharePoint. Они используется в системе авторизации Azure ACS, если приложение ASP.NET получает доступ к SharePoint. В нашем примере на них можно не обращать внимания, но не следует их удалять. Размещаемые у поставщика Надстройки SharePoint используют эти сведения, даже если надстройка не получает доступ к данным SharePoint. Их значения будут изменяться при каждом нажатии клавиши F5 в Visual Studio. Добавьте в раздел два следующих элемента. Приложение использует их для аутентификации в Azure AD. Помните, что в системах аутентификации и авторизации на основе OAuth приложения, как и пользователи, являются субъектами безопасности.
    
  ```
  
<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
  ```

3. Вставьте ранее сохраненный код клиента из каталога Azure AD в качестве значения ключа **ida:ClientID**. В точности сохраните регистр и пунктуацию и убедитесь, что в начале и конце значения нет пробелов. В качестве ключа **ida:ClientKey** используйте *ключ*  из каталога. Как и в предыдущем случае, избегайте лишних пробелов и других изменений значения. Теперь раздел `<appSettings>` должен иметь примерно следующий вид (значением **ClientId** может быть код GUID или пустая строка):
    
  ```
  
<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
  ```


    > **Примечание**
      > Azure AD распознает приложение по URL-адресу localhost, который использовался при регистрации. Код и ключ клиента связаны с этим удостоверением. Когда придет время подготовки приложения в Веб-сайт Azure, его нужно будет заново зарегистрировать с новым URL-адресом. 
4. Не покидая раздел **appSettings**, добавьте ключ **Authority** и укажите в качестве его значения домен Office 365 ( *имя_домена*  .onmicrosoft.com) учетной записи организации. В нашем примере используется учетная запись Bob@<домен_O365>.onmicrosoft.com, поэтому ключ Authority имеет значение `<O365_domain>.onmicrosoft.com`. 
    
  ```
  
<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  ```

5. В разделе **appSettings** добавьте ключ **AppRedirectUrl** и задайте в качестве его значения адрес страницы, на который браузер пользователя должен перенаправляться после того, как надстройка ASP.NET получит код авторизации из Azure AD. Как правило, пользователь просматривает именно эту страницу во время отправки вызова к Azure AD. В нашем примере используется URL-адрес SSL с приставкой "/Pages/Default.aspx", как показано ниже. Это значение также нужно изменить при подготовке.
    
  ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  ```

6. Не покидая раздел **appSettings**, добавьте ключ **ResourceUrl** и задайте в качестве его значения URI кода приложения Шлюз SAP для Майкрософт, а *не*  URI кода приложения ASP.NET. Это значение можно узнать у администратора Шлюз SAP для Майкрософт. Ниже приведен пример.
    
  ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
  ```


    Теперь раздел  `<appSettings>` должен иметь примерно следующий вид:
    


  ```
  <appSettings>
  <add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
  <add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
</appSettings>
  ```

7. Сохраните и закройте файл web.config.
    
    > **Совет**
      > Не оставляйте файл web.config открытым при запуске отладчика Visual Studio (F5). Инструменты разработчика Office для Visual Studio изменяет значение **ClientId** (не **ida:ClientID**) при каждом нажатии клавиши F5. При этом вам нужно подтвердить повторную загрузку файла web.config, если он открыт при запуске отладки. 

### Добавление вспомогательного класса для аутентификации в Azure AD


1. Щелкните проект ASP.NET правой кнопкой мыши и добавьте новый файл класса к проекту под названием AADAuthHelper.cs, следуя процедуре добавления элемента Visual Studio.
    
  
2. Добавьте в файл следующие операторы **using**:
    
  ```
  
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

  ```

3. Измените ключевое слово доступа с **public** на **internal** и добавьте ключевое слово **static** к объявлению класса.
    
  ```
  
internal static class AADAuthHelper
  ```

4. Добавьте к классу следующие поля. В них хранятся сведения, которые приложение ASP.NET использует для получения маркеров доступа из AAD.
    
  ```
  private static readonly string _authority = ConfigurationManager.AppSettings["Authority"];
private static readonly string _appRedirectUrl = ConfigurationManager.AppSettings["AppRedirectUrl"];
private static readonly string _resourceUrl = ConfigurationManager.AppSettings["ResourceUrl"];     
private static readonly string _clientId = ConfigurationManager.AppSettings["ida:ClientID"];        
private static readonly ClientCredential _clientCredential = new ClientCredential(
                           ConfigurationManager.AppSettings["ida:ClientID"],
                           ConfigurationManager.AppSettings["ida:ClientKey"]);

private static readonly AuthenticationContext _authenticationContext = 
            new AuthenticationContext("https://login.windows.net/common/" + 
                                      ConfigurationManager.AppSettings["Authority"]);
  ```

5. Добавьте к классу следующее свойство. В нем хранится URL-адрес экрана входа Azure AD.
    
  ```
  
private static string AuthorizeUrl
{
    get
    {
        return string.Format("https://login.windows.net/{0}/oauth2/authorize?response_type=code&amp;redirect_uri={1}&amp;client_id={2}&amp;state={3}",
            _authority,
            _appRedirectUrl,
            _clientId,
            Guid.NewGuid().ToString());
    }
}

  ```

6. Добавьте в класс следующие свойства. Они кэшируют маркеры доступа и обновления, а также проверяют, действительны ли они.
    
  ```
  
public static Tuple<string, DateTimeOffset> AccessToken
{
    get {
return HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] 
       as Tuple<string, DateTimeOffset>;
    }

    set { HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] = value; }
}

private static bool IsAccessTokenValid
{
   get 
   { 
       return AccessToken != null &amp;&amp;
       !string.IsNullOrEmpty(AccessToken.Item1) &amp;&amp;
       AccessToken.Item2 > DateTimeOffset.UtcNow;
   }
}

private static string RefreshToken
{
    get { return HttpContext.Current.Session["RefreshToken" + _resourceUrl] as string; }
    set { HttpContext.Current.Session["RefreshToken-" + _resourceUrl] = value; }
}

private static bool IsRefreshTokenValid
{
    get { return !string.IsNullOrEmpty(RefreshToken); }
}

  ```

7. Добавьте к классу следующие методы. Они используются для проверки кода аутентификации и получения маркера доступа из Azure AD с помощью кода авторизации или маркера обновления.
    
  ```
  
private static bool IsAuthorizationCodeNotNull(string authCode)
{
    return !string.IsNullOrEmpty(authCode);
}

private static Tuple<Tuple<string,DateTimeOffset>,string> AcquireTokensUsingAuthCode(string authCode)
{
    var authResult = _authenticationContext.AcquireTokenByAuthorizationCode(
                authCode,
                new Uri(_appRedirectUrl),
                _clientCredential,
                _resourceUrl);

    return new Tuple<Tuple<string, DateTimeOffset>, string>(
                new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn), 
                authResult.RefreshToken);
}

private static Tuple<string, DateTimeOffset> RenewAccessTokenUsingRefreshToken()
{
    var authResult = _authenticationContext.AcquireTokenByRefreshToken(
                         RefreshToken,
                         _clientCredential.OwnerId,
                         _clientCredential,
                         _resourceUrl);

    return new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);
}

  ```

8. Добавьте к классу следующий метод. Он вызывается из кода ASP.NET для получения маркера доступа перед отправкой запроса данных SAP через Шлюз SAP для Майкрософт.
    
  ```
  
internal static void EnsureValidAccessToken(Page page)
{
    if (IsAccessTokenValid) 
    {
        return;
    }
    else if (IsRefreshTokenValid) 
    {
        AccessToken = RenewAccessTokenUsingRefreshToken();
        return;
    }
    else if (IsAuthorizationCodeNotNull(page.Request.QueryString["code"]))
    {
        Tuple<Tuple<string, DateTimeOffset>, string> tokens = null;
        try
        {
            tokens = AcquireTokensUsingAuthCode(page.Request.QueryString["code"]);
        }
        catch 
        {
            page.Response.Redirect(AuthorizeUrl);
        }
        AccessToken = tokens.Item1;
        RefreshToken = tokens.Item2;
        return;
    }
    else
    {
        page.Response.Redirect(AuthorizeUrl);
    }
}
  ```


> **Совет**
> Класс AADAuthHelper содержит минимальный набор обработчиков ошибок. Чтобы создать надежную и профессиональную надстройку SharePoint, добавьте обработчики ошибок, описанные в разделе  [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx) библиотеки MSDN.
  
    
    


### Создание классов модели данных


1. Создайте один или несколько классов модели данных, которые надстройка получает от SAP. В нашем примере используется только один класс модели данных. Щелкните проект ASP.NET правой кнопкой мыши и добавьте новый файл класса к проекту под названием Automobile.cs, следуя процедуре добавления элемента Visual Studio.
    
  
2. Добавьте в тело класса следующий код:
    
  ```
  
public string Price;
public string Brand;
public string Model;
public int Year;
public string Engine;
public int MaxPower;
public string BodyStyle;
public string Transmission;
  ```


### Добавление кода для получения данных из SAP через Шлюз SAP для Майкрософт


1. Откройте файл Default.aspx.cs и добавьте следующие операторы **using**.
    
  ```
  
using System.Net;
using Newtonsoft.Json.Linq;
  ```

2. Добавьте объявление **const** к классу Default, указав в качестве значения URL-адрес конечной точки OData SAP, которую будет использовать надстройку. Вот пример такого объявления:
    
  ```
  
private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
  ```

3. В Инструменты разработчика Office для Visual Studio добавлены методы **Page_PreInit** и **Page_Load**. Закомментируйте код метода **Page_Load** и весь метод **Page_Init**. Этот код не используется в данном примере. Если ваша Надстройка SharePoint будет использовать SharePoint, этот код нужно восстановить. См. раздел  [(Необязательно) Предоставление приложению ASP.NET доступа к SharePoint](#SharePoint).
    
  
4. Добавьте следующую строку в начало метода **Page_Load**. Это упростит отладку, так как приложение ASP.NET взаимодействует с Шлюз SAP для Майкрософт с помощью SSL (HTTPS), но сервер localhost:port не доверяет сертификату Шлюз SAP для Майкрософт. Без этой строки кода перед открытием страницы Default.aspx появится предупреждение о недействительном сертификате. Некоторые браузеры позволяют пропустить эту ошибку, но в остальных открыть страницу Default.aspx может быть невозможно.
    
  ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
  ```


    > **Важно!**
      > Удалите эту строку, когда вы будете готовы к развертыванию приложения ASP.NET для его подготовки. См. раздел  [Изменение и подготовка надстройки в Azure и Office 365](#Stage). 
5. Добавьте следующий код в метод **Page_Load**. Строка, передаваемая методу  `GetSAPData`,  это запрос OData.
    
  ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

  ```

6. Добавьте следующий метод к классу Default. Этот метод сначала обеспечивает наличие в кэше действительного маркера доступа, полученного из Azure AD, а затем создает HTTP-запрос **GET**, который включает маркер доступа и отправляет его конечной точке OData SAP. Возвращаемый результат является объектом JSON, преобразованным в объект .NET **List**. В массиве, связанном с **DataListView**, используются три свойства элементов.
    
  ```
  
private void GetSAPData(string oDataQuery)
{
    AADAuthHelper.EnsureValidAccessToken(this);

    using (WebClient client = new WebClient())
    {
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.Authorization] = "Bearer " + AADAuthHelper.AccessToken.Item1;
        var jsonString = client.DownloadString(SAP_ODATA_URL + oDataQuery);
        var jsonValue = JObject.Parse(jsonString)["d"]["results"];
        var dataCol = jsonValue.ToObject<List<Automobile>>();

        var dataList = dataCol.Select((item) => {
            return item.Brand + " " + item.Model + " " + item.Price;
            }).ToArray();

        DataListView.DataSource = dataList;
        DataListView.DataBind();
    }
}

  ```


### Создание пользовательского интерфейса


1. Откройте файл Default.aspx и добавьте следующую разметку к объекту **form**:
    
  ```
  
<div>
  <h3>Data from SAP via SAP Gateway for Microsoft</h3>

  <asp:ListView runat="server" ID="DataListView">
    <ItemTemplate>
      <tr runat="server">
        <td runat="server">
          <asp:Label ID="DataLabel" runat="server"
            Text="<%# Container.DataItem.ToString()%>" /><br />
        </td>
      </tr>
    </ItemTemplate>
  </asp:ListView>
</div>
  ```

2. При желании вы можете придать веб-странице внешний вид страницы SharePoint с помощью SharePoint  [элемента управления хрома](use-the-client-chrome-control-in-sharepoint-add-ins.md) и [таблицы стилей веб-сайта SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md).
    
  

### Тестирование надстройки с помощью клавиши F5 в Visual Studio


1. Нажмите клавишу F5 в Visual Studio.
    
  
2. При первом нажатии клавиши F5 вам может быть предложено войти на ваш Сайт разработчиков. Используйте учетные данные администратора. В нашем примере используется учетная запись Bob@<домен_O365>.onmicrosoft.com.
    
  
3. Кроме того, первом нажатии клавиши F5 вам будет предложено предоставить надстройке разрешения. Нажмите кнопку **Доверять**.
    
  
4. После небольшой задержки откроется страница Default.aspx. Убедитесь, что отображаются данные SAP.
    
  

## (Необязательно) Предоставление приложению ASP.NET доступа к SharePoint
<a name="SharePoint"> </a>

Конечно, Надстройка SharePoint может не только отображать данные SAP на веб-странице, открытой из SharePoint. Она также может создавать, считывать, обновлять и удалять (операция CRUD) данные SharePoint. Для этого код использует клиентскую модель объектов SharePoint (CSOM) или интерфейсы API REST SharePoint. CSOM развертывается как две сборки, которые Инструменты разработчика Office для Visual Studio автоматически включает в проект ASP.NET. В Visual Studio для них установлен параметр **Копировать локально**, чтобы включить их в пакет приложения ASP.NET. Начальные сведения об использовании CSOM см. в статье  [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md). Сведения об использовании интерфейсов REST см. в статье  [Знакомство с REST-интерфейсом SharePoint 2013 и его использование](http://msdn.microsoft.com/ru-ru/magazine/dn198245.aspx). 
  
    
    
Независимо от того, используете ли вы CSOM или API REST для доступа к SharePoint, приложению ASP.NET необходимо получить маркер доступа к SharePoint, как и к Шлюз SAP для Майкрософт. См. раздел  [Понимание аутентификации и авторизации в Шлюз SAP для Майкрософт и SharePoint](#AuthOverview) выше. Далее описаны основные действия, необходимые для этого, но для начала рекомендуем прочитать следующие статьи:
  
    
    

-  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Авторизация и проверка подлинности для надстроек в SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Три системы авторизации для надстроек для SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Создание надстроек для SharePoint, которые используют авторизацию с низким уровнем доверия](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
-  [Поток маркеров контекста OAuth для надстроек в SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  

1. Откройте файл Default.aspx.cs и раскомментируйте метод **Page_PreInit**. Затем раскомментируйте код, добавленный Инструменты разработчика Office для Visual Studio к методу **Page_Load**.
    
  
2. Если ваша Надстройка SharePoint будет обращаться к данным SharePoint, необходимо кэшировать маркер контекста SharePoint, отправленный POST-запросом на страницу Default.aspx при запуске приложения в SharePoint. Это позволит убедиться, что маркер контекста SharePoint не потеряется при перенаправлении браузера после аутентификации в Azure AD. (Кэшировать этот контекст можно несколькими способами.) Инструменты разработчика Office для Visual Studio добавляет файл SharePointContext.cs к проекту ASP.NET, который выполняет основную часть работы. Чтобы использовать кэш сеанса, добавьте следующий код в блок " `if (!IsPostBack)`"  *перед*  кодом, который вызывает Шлюз SAP для Майкрософт:
    
  ```
  
if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
  ```

3. Файл SharePointContext.cs отправляет вызовы другому файлу, который Инструменты разработчика Office для Visual Studio добавляет к проекту: TokenHelper.cs. Этот файл содержит большую часть кода, необходимого для получения и использования маркеров доступа для SharePoint. Тем не менее, в нем отсутствует код для продления срока действия устаревшего маркера доступа или маркера обновления. Кроме того, в нем нет кода для кэширования маркеров. Если вы создаете профессиональную надстройку SharePoint, добавить этот код необходимо. Примером может служить логика кэширования из предыдущего этапа. Ваш код также должен кэшировать маркер доступа и продолжать использовать его до истечения срока действия. По истечении срока действия маркера код должен получить новый маркер доступа с помощью маркера обновления.
    
  
4. Добавьте вызовы данных SharePoint с помощью CSOM или REST. Следующий пример является вариацией кода CSOM, который Инструменты разработчика Office для Visual Studio добавляет к методу **Page_Load**. В этом примере код был перемещен в отдельный метод и начинается с получения кэшированного маркера доступа.
    
  ```
  
private void GetSharePointTitle()
{
    var spContext = HttpContext.Current.Session["SharePointContext"] as SharePointContext;
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        clientContext.Load(clientContext.Web, web => web.Title);
        clientContext.ExecuteQuery();
        SharePointTitle.Text = "SharePoint web site title is: " + clientContext.Web.Title;
    }
}
  ```

5. Добавьте элементы пользовательского интерфейса для отображения данных SharePoint. Ниже показан элемент управления HTML, на который ссылается предыдущий метод:
    
  ```
  
<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
  ```


> **Примечание**
> При отладке надстройки SharePoint Инструменты разработчика Office для Visual Studio заново регистрирует ее в Azure ACS при каждом нажатии клавиши F5 в Visual Studio. Когда вы подготавливаете надстройку SharePoint, ей необходимо назначить долгосрочную регистрацию. См. раздел  [Изменение и подготовка надстройки в Azure и Office 365](#Stage). 
  
    
    


## Изменение и подготовка надстройки в Azure и Office 365
<a name="Stage"> </a>

Закончив отлаживать надстройку SharePoint с помощью клавиши F5 в Visual Studio, необходимо выполнить развертывание приложения ASP.NET в Веб-сайт Azure.
  
    
    

### Создание веб-сайта Azure


1. На портале Microsoft Azure выберите **ВЕБ-САЙТЫ** в левой области навигации.
    
  
2. Нажмите **СОЗДАТЬ** в нижней части страницы, а в диалоговом окне **СОЗДАНИЕ** выберите команды **ВЕБ-САЙТ** |**БЫСТРОЕ СОЗДАНИЕ**.
    
  
3. Введите имя домена и нажмите **СОЗДАТЬ ВЕБ-САЙТ**. Скопируйте URL-адрес нового сайта. Он будет иметь вид  `my_domain.azurewebsites.net`.
    
  

### Изменение кода и разметки приложения


1. В Visual Studio удалите строку  `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;` из файла Default.aspx.cs.
    
  
2. Откройте файл web.config проекта ASP.NET и замените доменную часть значения ключа **AppRedirectUrl** в разделе **appSettings** на домен Веб-сайт Azure. Например, замените `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` на `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`.
    
  
3. Щелкните правой кнопкой мыши файл AppManifest.xml в проекте надстройки SharePoint и выберите команду **Перейти к коду**.
    
  
4. В значении **StartPage** замените строку `~remoteAppUrl` полным доменным именем Веб-сайт Azure, включая протокол, например `https://my_domain.azurewebsites.net`. Теперь полное значение **StartPage** должно выглядеть так: `https://my_domain.azurewebsites.net/Pages/Default.aspx` (как правило, значение **StartPage** должно совпадать со значением ключа **AppRedirectUrl** в файле web.config).
    
  

### Изменение регистрационных данных в AAD и регистрация надстройки в службе контроля доступа


1. Войдите на  [портал управления Azure](https://manage.windowsazure.com), используя учетную запись администратора Azure.
    
  
2. Выберите **Active Directory** в левой части страницы.
    
  
3. Щелкните свой каталог.
    
  
4. Выберите **ПРИЛОЖЕНИЯ** (на верхней панели навигации).
    
  
5. Откройте созданное приложение. В нашем примере используется имя **ContosoAutomobileCollection**.
    
  
6. В каждом из следующих значений замените часть "localhost: *порт*  " на значение домена нового Веб-сайт Azure:
    
  - **URL-АДРЕС ВХОДА**
    
  
  - **КОД URI ПРИЛОЖЕНИЯ**
    
  
  - **URL-АДРЕС ОТВЕТА**
    
  

    Например, если **КОД URI ПРИЛОЖЕНИЯ** имеет значение **https://localhost:44304/ContosoAutomobileCollection**, замените его на **https://<мой_домен>.azurewebsites.net/ContosoAutomobileCollection**.
    
  
7. Выберите элемент **СОХРАНИТЬ** в нижней части экрана.
    
  
8. Зарегистрируйте надстройку в Azure ACS. Это необходимо сделать, даже если надстройка не получает доступ к SharePoint и не будет использовать маркеры из службы контроля доступа, так как при этом надстройка регистрируется еще и в службе управления надстройками для подписки на Office 365, а это обязательно. ("Служба управления надстройками" получила свое название, так как используется для управления надстройками SharePoint, которые ранее назывались "приложениями для SharePoint".) В случае подписки на Office 365 регистрация выполняется на странице AppRegNew.aspx любого веб-сайта SharePoint. Дополнительные сведения см. в статье  [Регистрация надстроек для SharePoint 2013](register-sharepoint-add-ins-2013.md). В ходе регистрации вы получите новые идентификатор клиента и секрет клиента. Вставьте эти значения в файл web.config как ключи **ClientId** (не **ida:ClientID**) и **ClientSecret**.
    
    > **Внимание!**
      > Если по какой-либо причине вы нажмете клавишу F5 после внесения этих изменений, Инструменты разработчика Office для Visual Studio перезапишет одно из этих значений или оба значения. Поэтому следует вести журнал значений, полученных страницей AppRegNew.aspx, и всегда проверять правильность значений в файле web.config непосредственно перед публикацией приложения ASP.NET. 

### Публикация приложения ASP.NET в Azure и установка надстройки в SharePoint


1. Опубликовать приложение ASP.NET в Веб-сайт Azure можно несколькими способами. Дополнительные сведения см. в статье  [Развертывание веб-сайта Azure](http://azure.microsoft.com/ru-ru/documentation/articles/web-sites-deploy/).
    
  
2. В Visual Studio щелкните правой кнопкой мыши надстройку SharePoint и выберите пункт **Упаковать**. На открывшейся странице **Публикация надстройки** нажмите **Упаковать надстройку**. Откроется окно проводника с папкой, содержащей пакет надстройки.
    
  
3. Войдите в Office 365 от имени глобального администратора и перейдите к семейству веб-сайтов каталога надстроек. Если такого семейства нет, создайте его (см. статью  [Использование каталога надстроек для развертывания пользовательских бизнес-надстроек в среде SharePoint Online](http://office.microsoft.com/ru-ru/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)).
    
  
4. Отправьте пакет надстройки в каталог надстроек.
    
  
5. Откройте на любом сайте подписки страницу **Контент сайта** и нажмите **добавить надстройку**.
    
  
6. На странице **Надстройки** найдите раздел **Надстройки, которые вы можете добавить** и щелкните значок вашей надстройки правой кнопкой мыши.
    
  
7. После установки надстройки щелкните его значок на странице **Контент сайта**, чтобы запустить надстройку.
    
  
Дополнительные сведения о том, как установить Надстройки SharePoint, см. в статье  [Развертывание и установка надстроек для SharePoint: методы и параметры](deploying-and-installing-sharepoint-add-ins-methods-and-options.md).
## Развертывание надстройки для работы
<a name="Stage"> </a>

Завершив тестирование, вы можете развернуть надстройку для работы. Для этого могут потребоваться некоторые изменения.
  
    
    

1. Если рабочий домен приложения ASP.NET отличается от промежуточного домена, необходимо изменить значение **AppRedirectUrl** в файле web.config и значение **StartPage** в файле AppManifest.xml, а затем заново упаковать надстройку SharePoint. См. раздел **Изменение кода и разметки приложения** выше.
    
  
2. При изменении домена также требуется изменить регистрационные данные надстроек в AAD. См. раздел **Изменение регистрационных данных в AAD и регистрация надстройки в службе контроля доступа** выше.
    
  
3. Кроме того, при изменении домена требуется повторно зарегистрировать надстройку в службе контроля доступа (и службе управления надстройками подписки). Этот процесс также описан в вышеупомянутом разделе.  *Изменить*  регистрационные данные надстройки в службе контроля доступа невозможно. Однако создавать новые идентификатор клиента и секрет клиента на странице AppRegNew.aspx не требуется. Вы можете скопировать исходные значения из ключей **ClientId** (не **ida:ClientID**) и **ClientSecret** файла web.config в форму AppRegNew. *Создавая новые ключи, не забывайте копировать новые значения в ключи файла web.config.* 
    
  

