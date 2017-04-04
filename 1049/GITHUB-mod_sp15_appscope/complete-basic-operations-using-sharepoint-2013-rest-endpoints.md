---
title: Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST
Узнайте, как создавать, читать, обновлять и удалять (CRUD) операции с помощью интерфейса REST SharePoint 2013.
## Разработка с помощью клиентского интерфейса API и REST для SharePoint
<a name="ClientAPIs"> </a>

Вы можете выполнять базовые операции создания, чтения, обновления и удаления (CRUD) с помощью интерфейса Representational State Transfer (REST), предоставленного SharePoint 2013. REST позволяет использовать все объекты и операции SharePoint, которые доступны в других клиентских интерфейсах API для SharePoint. Одно из преимуществ REST заключается в том, что вам не придется добавлять ссылки на библиотеки или клиентские сборки SharePoint 2013. Вместо этого вы отправляете HTTP-запросы в соответствующие конечные точки для получения или обновления объектов SharePoint, например веб-сайтов, списков и элементов списков. Подробный обзор интерфейса REST SharePoint 2013 и его архитектуры см. в статье  [Знакомство со службой REST для SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md).
  
    
    
Больше о работе с базовыми сущностями можно узнать в статьях SharePoint [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md) и [Работа с папками и файлами в службе REST](working-with-folders-and-files-with-rest.md). С примерами выполнения многих из этих операций в контексте веб-приложения ASP.NET, написанного на C#, можно ознакомиться в репозитории  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations).
  
    
    
Более подробные сведения о наборах API, доступных на платформе SharePoint 2013, см. в статье  [Выбор правильного набора API в SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx). Дополнительные сведения об использовании других клиентских интерфейсов API см. в статьях  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) и [Построение приложений Windows Phone, обращающихся к SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx).
  
    
    

## Операции HTTP в службах REST SharePoint 2013
<a name="HTTPOps"> </a>

Конечные точки в службе REST SharePoint 2013 соответствуют типам и участникам в клиентских объектных моделях SharePoint. С помощью HTTP-запросов можно использовать конечные точки REST для выполнения типичных операций CRUD ( **Create**, **Read**, **Update** и **Delete**) с артефактами SharePoint, такими как списки и сайты.
  
    
    
Обычно конечные точки, представляющие операции **Read**, сопоставляются с HTTP-командами **GET**. Конечные точки, представляющие операции обновления, сопоставляются с HTTP-командами **POST**, а конечные точки, представляющие операции обновления или вставки  с HTTP-командами **PUT**.
  
    
    
В SharePoint 2013 для создания сущностей, например списков и сайтов, используется команда **POST**. Служба REST SharePoint 2013 поддерживает отправку команд **POST**, включающих описания объектов для конечных точек, представляющих коллекции. Например, можно отправить команду **POST**, включающую новое описание объекта списка в ATOM, на следующий URL-адрес, чтобы создать список SharePoint.
  
    
    
 `http://<site url>/_api/web/lists`
  
    
    
Для операций **POST** любые свойства, которые не являются необходимыми, имеют значения по умолчанию. Если попытаться задать свойство, предназначенное только для чтения, в операции **POST**, служба возвращает исключение.
  
    
    
Для обновления существующих объектов SharePoint используйте операции **PUT** и **MERGE**. Любые конечные точки службы, представляющие операцию **set** свойства объекта, поддерживают как запросы **PUT**, так и запросы **MERGE**. Для запросов **MERGE** необязательно задавать свойства. Любые свойства, не заданные явно, сохраняют текущие значения. Однако для команд **PUT** любое свойство, не заданное явно, будет иметь значение по умолчанию. Кроме того, если не указать все обязательные свойства в обновлении объекта при использовании HTTP-команд **PUT**, служба REST вернет исключение.
  
    
    
Используйте HTTP-команду **DELETE** для определенного URL-адреса конечной точки, чтобы удалить объект SharePoint, представленный этой конечной точкой. Для повторно обрабатываемых объектов (например, списков, файлов и элементов списков) это приведет к выполнению операции **Recycle**.
  
    
    

## Чтение данных с помощью интерфейса REST SharePoint 2013
<a name="ReadingData"> </a>

Для использования возможностей REST, которые встроены в SharePoint 2013, вы создаете HTTP-запрос с поддержкой REST, используя стандарт OData, что соответствует клиентской объектной модели API, которую вы хотите использовать. Каждый объект SharePoint предоставляется в конечной точке на странице SharePoint 2013, на которую вы ориентируетесь, а метаданные представлены в формате XML или JSON. Вы можете создавать HTTP-запросы на любом языке, в том числе на JavaScript, C# и многих других.
  
    
    
Чтобы прочитать информацию в конечной точке REST, вы должны знать URL конечной точки и представление OData объекта SharePoint, который предоставляется в этой конечной точке. Например, чтобы получить все списки на определенном сайте SharePoint, нужно создать запрос **GET** для `http://<site url>/_api/web/lists`. Вы можете перейти к этому URL-адресу в браузере и посмотреть на полученный XML. Если вы создаете запрос в коде, вы можете указать, получать ли списки OData в формате XML или JSON.
  
    
    
Следующий код на C# демонстрирует, как можно создать запрос **GET**, который возвращает представление JSON для всех списков сайта с помощью JQuery. Предполагается, что у вас есть допустимый маркер доступа OAuth, хранящийся в переменной **accessToken**. Маркер доступа не нужен, если вы выполняете вызов с сайта надстройки, как это делается в надстройке, размещенной в SharePoint. Обратите внимание, что нельзя получить маркер доступа из кода, который выполняется в браузерном клиенте. Необходимо получить маркер доступа из кода, который выполняется на сервере. В разделах  [Поток маркеров контекста OAuth для надстроек в SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) и [Поток кода аутентификации OAuth для надстроек в SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md) рассказывается, как получить маркер доступа.
  
    
    


```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

Этот запрос будет выглядеть немного по-другому, если вы создаете надстройку в JavaScript, но с использованием междоменной библиотеки SharePoint 2013. В этом случае предоставлять маркер доступа не нужно. Следующий код демонстрирует, как бы выглядел этот запрос, если бы вы использовали междоменную библиотеку и хотели бы получить представление списков OData в формате XML, а не JSON. (Так как формат ответа по умолчанию  это Atom, необязательно добавлять заголовок **Accept**.) Дополнительные сведения об использовании междоменной библиотеки см. в статье  [Обращение к данным SharePoint 2013 из надстроек с помощью междоменной библиотеки](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    


```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);```

В следующем примере показано, как запросить представление JSON всех списков сайта, используя C#. Предполагается, что у вас есть маркер доступа OAuth, который вы храните в переменной  `accessToken`.
  
    
    


```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();
```


### Получение свойств, которые не возвращены с ресурсом
<a name="NavigationProperties"> </a>

Многие значения свойств возвращаются, когда вы получаете ресурс, но для некоторых свойств необходимо отправить запрос **GET** непосредственно в конечную точку свойства. Это типично для свойств, представляющих объекты SharePoint.
  
    
    
В следующем примере показывается, как получить свойство, добавив его имя в конечную точку ресурса. В примере значение свойства **Author** получено от ресурса **File**.
  
    
    
http:// _<URL-адрес сайта>_/_api/web/getfilebyserverrelativeurl('/ _<имя папки>_/ _<имя файла>_')/author
  
    
    
Чтобы получить результаты в формате JSON, включите набор заголовков **Accept** в `"application/json;odata=verbose"`.
  
    
    

## Запись данных с помощью интерфейса REST
<a name="WritingData"> </a>

Вы можете создавать и обновлять объекты SharePoint, создавая HTTP-запросы с поддержкой REST для соответствующих конечных точек, так же, как это происходит при чтении данных. Одно из ключевых отличий заключается в том, что вы используете запрос **POST**. При обновлении объектов вы также передаете HTTP-запрос **PUT** или **MERGE**, добавляя один из этих операторов в заголовки запроса в качестве значения ключа **X-HTTP-Method**. Метод **MERGE** обновляет только те свойства объекта, которые вы укажете, в то время как метод **PUT** заменяет существующий объект на новый, который вы указываете в теле **POST**. Используйте метод **DELETE** для удаления объекта. При создании или обновлении объекта необходимо указать представление OData для объекта, который вы хотите создать или изменить, в теле HTTP-запроса.
  
    
    
Еще одним важным фактором при создании, обновлении и удалении объектов SharePoint является то, что если вы не используете OAuth для авторизации своих запросов, для выполнения этих операций потребуется передавать значение дайджеста формы запроса сервера в качестве значения заголовка **X-RequestDigest**. Чтобы получить это значение, выполните запрос **POST** с пустым телом для `http://<site url>/_api/contextinfo` и извлеките значение сайта `d:FormDigestValue` в XML-коде, возвращаемом конечной точкой **contextinfo**. В следующем примере показан HTTP-запрос к конечной точке **contextinfo**, написанный на C#.
  
    
    


```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

Если вы используете поток аутентификации и авторизации, описанный в статье  [Авторизация и проверка подлинности для надстроек в SharePoint 2013](authorization-and-authentication-of-sharepoint-add-ins.md), нет необходимости включать в свои запросы дайджест запроса.
  
    
    
Если вы используете междоменную библиотеку JavaScript, SP.RequestExecutor обрабатывает получение и отправку значения дайджеста формы.
  
    
    
Если вы создаете Надстройка SharePoint, размещенное в SharePoint, то вам не придется выполнять отдельный HTTP-запрос для получения обработанного значения. Вместо этого вы можете получить значение в коде JavaScript со страницы SharePoint (если страница использует эталонную страницу по умолчанию), как показано в следующем примере, в котором используется JQuery и создается список.
  
    
    


```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});

```

В следующем примере показано, как обновить список, созданный в предыдущем примере. В примере меняется название списка, используется JQuery и предполагается, что вы выполняете эту операцию в надстройке, размещенной в SharePoint.
  
    
    


```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});
```

В значении **IF-MATCH** в заголовках запроса вы указываете значение **etag** списка или элемента списка. Данное значение применяется только для списков и элементов списков, и оно помогает избежать проблем одновременной обработки при обновлении этих объектов. В предыдущем примере для данного значения используется звездочка (*). Вы можете использовать это значение, если у вас нет никаких причин беспокоиться о проблеме одновременной обработки. В противном случае необходимо получить значение **etag** или список или элемент списка, выполнив запрос **GET**, который извлекает объект. Заголовки полученного HTTP-запроса передают etag в качестве значения ключа **ETag**. Это значение также включено в метаданные объекта. В следующем примере показан открывающий тег  `<entry>` для XML сайта, который содержит данные списка. Свойство **m:etag** содержит значение **etag**.
  
    
    


```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">```


## Создание сайта с помощью REST
<a name="bk_CreateSite"> </a>

В следующем примере показано, как создать сайт на JavaScript.
  
    
    
```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});```


## Отличия запросов REST в разных средах
<a name="bk_HowRequestsDiffer"> </a>

Методы составления и отправки HTTP-запросов могут различаться в зависимости от языка, библиотеки и типа надстройки, поэтому при переводе запроса из одной среды в другую часто требуется изменять один или несколько компонентов запросов. Например, запросы jQuery AJAX используют параметры **data** и **type**, чтобы указывать тело и тип запроса, в то время как запросы междоменных библиотек используют параметры **body** и **method**.
  
    
    
В следующих разделах описаны другие распространенные отличия между средами.
  
    
    

### Способ получения и отправки обработанного значения зависит от надстройки
<a name="FormDigest"> </a>

При отправке запроса POST в его заголовке **X-RequestDigest** должно быть указано значение дайджеста формы. Однако способ получения и отправки значения зависит от надстройки:
  
    
    

- В надстройках с размещением в SharePoint достаточно передавать следующий заголовок: 
  
    
    
 "X-RequestDigest": $("#__REQUESTDIGEST").val()
    
  
- Надстройки с размещением в облаке, использующие OAuth, сначала получают значение дайджеста формы, отправляя запрос к конечной точке **contextinfo**, а затем добавляя его в запросы, как показано в разделе  [Запись данных с помощью интерфейса REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData).
    
  
- В надстройках с размещением в облаке, которые используют междоменную библиотеку JavaScript, не требуется указывать значение дайджеста формы. По умолчанию SP.RequestExecutor делает это автоматически. (Метод также обрабатывает значение content-length.)
    
  

### Надстройки, использующие OAuth, должны передавать маркеры доступа в запросах
<a name="OAuthTokens"> </a>

Чтобы разрешать доступ к данным SharePoint, надстройки с размещением в облаке используют OAuth или междоменную библиотеку. Компоненты надстроек с кодом, который выполняется на удаленном веб-сервере, должны использовать OAuth для разрешения доступа к данным SharePoint. В этом случае необходимо включить заголовок **Authorization** для отправки маркера доступа. Пример добавления заголовка авторизации к объекту **HTTPWebRequest** см. в разделе [Чтение данных с помощью интерфейса SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData).
  
    
    

> **Примечание**
> Компоненты надстроек с размещением в облаке, записанные в JavaScript, должны использовать объект **SP.RequestExecutor** в междоменной библиотеке для доступа к данным SharePoint. Запросы к междоменным библиотекам не должны содержать маркер доступа.
  
    
    

Подробнее о маркерах доступа OAuth и их получении см. в статьях  [Поток маркеров контекста OAuth для надстроек в SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) и [Поток кода аутентификации OAuth для надстроек в SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### URI конечных точек в междоменных запросах используют SP.AppContextSite для изменения контекста
<a name="AppContextSite"> </a>

Запросы отправляются к конечной точке ресурсов, указанной в свойстве **url** запроса. URI конечных точек имеют следующий формат:
  
    
    
 _<адрес сайта>_/_api/ _<контекст>_/ _<ресурс>_ (например, https://contoso.com/_api/web/lists)
  
    
    
Запросы к междоменным библиотекам имеют такой формат при доступе к данным на сайте надстройки, стандартном контексте запросов к междоменным библиотекам. Тем не менее, для доступа к данным на сайте надстройки или в другом семействе веб-сайтов запросам необходимо инициализировать хост-сайт или другое семейство веб-сайтов в качестве контекста. Для этого они используют конечную точку **SP.AppContextSite** из URI, как показано в таблице 1. В примерах URI из таблицы 1 используется псевдоним **@target** для отправки целевого URL-адреса в строке запроса, так как URL-адрес содержит специальный символ (":").
  
    
    

> **Примечание**
> Для доступа к данным SharePoint при использовании междоменной библиотеки надстройке с размещением в облаке необходим экземпляр сайта надстройки. 
  
    
    


**Таблица 1. Использование конечной точки SP.AppContextSite для изменения контекста запроса**


|**Тип надстройки**|**Сценарий междоменного доступа к данным**|**Пример URI конечной точки**|
|:-----|:-----|:-----|
|С размещением в облаке  <br/> |Компонент надстройки JavaScript получает доступ к данным сайта узла с помощью междоменной библиотеки  <br/> | _<адрес сайта приложения>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<адрес сайта узла>_'  <br/> |
|С размещением в облаке  <br/> |Компонент надстройки JavaScript получает доступ к данным в семействе веб-сайтов, не включающем хост-сайт, с помощью междоменной библиотеки (только для надстроек с разрешениями на уровне клиента)  <br/> | _<адрес сайта приложения>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<адрес целевого сайта>_'  <br/> |
|С размещением в SharePoint  <br/> |Компонент сайта надстройки получает доступ к данным в другом семействе веб-сайтов (только для надстроек с разрешениями на уровне клиента)  <br/> | _<адрес сайта приложения>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<адрес целевого сайта>_'  <br/> |
   

> **Примечание**
> Сценарии междоменного доступа к данным также требуют соответствующих разрешений для надстроек. Подробнее см. в разделах  [Доступ к данным на хост-сайте](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) и [Доступ к данным на нескольких семействах веб-сайтов](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope). 
  
    
    

Надстройки SharePoint могут получать URL-адреса сайта надстройки и хост-сайта из строки запроса страницы надстройки, как показано в следующем примере кода. Кроме того, в этом примере показывается, как ссылаться на междоменную библиотеку, определенную в файле SP.RequestExecutor.js на хост-сайте. Этот пример предполагает, что ваша надстройка запускается из SharePoint. Указания по правильной настройке контекста SharePoint, если надстройка запускается не из SharePoint, см. в статье  [Поток кода аутентификации OAuth для надстроек в SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
  
    
    


```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions```


## Свойства, используемые в запросах REST
<a name="bk_requestElements"> </a>

В таблице 2 показаны свойства, которые часто используются в HTTP-запросах для службы REST SharePoint.
  
    
    

**Таблица 2. Использование свойств запроса REST в HTTP-запросах**


|**Свойства**|**Когда требуется**|**Описание**|
|:-----|:-----|:-----|
|**url** <br/> |Все запросы  <br/> |URL-адрес конечной точки ресурса REST. Например:  `http://<site url>/_api/web/lists` <br/> |
|**method** (или **type**)  <br/> |Все запросы  <br/> |Метод HTTP-запроса: **GET** для операций чтения и **POST** для операций записи. Запросы **POST** могут выполнять операции обновления или удаления, используя команду **DELETE**, **MERGE** или **PUT** в заголовке **X-HTTP-Method**.  <br/> |
|**body** (или **data**)  <br/> |Запросы **POST**, которые отправляют данные в теле запроса  <br/> |Тело запроса POST. Отправляет данные (например, сложные типы), которые нельзя передать в URI конечной точки. Используется с заголовком **content-length**.  <br/> |
|Заголовок **Authentication** <br/> |Удаленные надстройки, которые используют OAuth для проверки подлинности пользователей. Не применяется при использовании JavaScript или междоменной библиотеки.  <br/> |Отправляет маркер доступа (полученный от сервера токенов безопасности для службы контроля доступа Microsoft Azure), который используется для проверки подлинности пользователя для запроса. Например:  `"Authorization": "Bearer " + accessToken`, где  `accessToken` представляет переменную, которая сохраняет маркер. Маркеры должны загружаться с использованием кода на стороне сервера. <br/> |
|Заголовок **X-RequestDigest** <br/> |Запросы **POST** (кроме запросов SP.RequestExecutor) <br/> |Удаленные надстройки, использующие протокол OAuth, могут получить значение дайджеста формы от конечной точки  `http://<site url>/_api/contextinfo`. Надстройки, размещенные в SharePoint, могут получить это значение от элемента управления страницей **#__REQUESTDIGEST**, если он доступен на странице SharePoint. См. раздел  [Запись данных с помощью интерфейса REST](#WritingData).  <br/> |
|Заголовок **accept** <br/> |Запросы, которые возвращают метаданные SharePoint  <br/> |Указывает формат данных ответа от сервера. Форматом по умолчанию является  `application/atom+xml`, например:  `"accept":"application/json;odata=verbose"` <br/> |
|Заголовок **content-type** <br/> |Запросы **POST**, которые отправляют данные в теле запроса  <br/> |Указывает формат данных, которые клиент отправляет серверу. Форматом по умолчанию является  `application/atom+xml`, например:  `"content-type":"application/json;odata=verbose"` <br/> |
|Заголовок **content-length** <br/> |Запросы **POST**, которые отправляют данные в теле запроса (кроме запросов SP.RequestExecutor)  <br/> |Указывает длину содержимого, например:  `"content-length":requestBody.length` <br/> |
|Заголовок **IF-MATCH** <br/> |**POST** запрашивает выполнение операций **DELETE**, **MERGE** или **PUT**, в первую очередь для изменения списков и библиотек.  <br/> |Предоставляет способ проверки того, что изменяемый объект не менялся с момента его последней загрузки. Или позволяет включить перезапись любых изменений, как показано в следующем примере:  `"IF-MATCH":"*"` <br/> |
|Заголовок **X-HTTP-Method** <br/> |**POST** запрашивает операции **DELETE**, **MERGE** или **PUT** <br/> |Используется для указания того, что запрос выполняет операцию обновления или удаления, например:  `"X-HTTP-Method":"PUT"` <br/> |
|**binaryStringRequestBody** <br/> |Запросы **POST**, которые отправляют двоичные данные в теле запроса  <br/> |Указывает, является ли текст запроса двоичной строкой. **Boolean**.  <br/> |
|**binaryStringResponseBody** <br/> |Запросы SP.RequestExecutor, возвращающие двоичные данные  <br/> |Указывает, является ли ответ двоичной строкой. **Boolean**.  <br/> |
   

## Поддержка пакетного задания
<a name="batch"> </a>

Служба REST SharePoint Online (а также локальной версии SharePoint 2016 и последующих выпусков) поддерживает объединение нескольких запросов в одном вызове службы с помощью параметра запроса OData  `$batch`. Дополнительные сведения и ссылки на примеры кода см. в разделе  [Создание пакетного запроса с помощью интерфейсов REST API](make-batch-requests-with-the-rest-apis.md).
  
    
    

## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Работа со списками и элементами списков в службе REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Работа с папками и файлами в службе REST](working-with-folders-and-files-with-rest.md)
    
  
-  [SharePoint 2013 REST API, конечные точки и примеры](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: выполнение основных операций доступа к данным в файлах и папках с помощью REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Выполнение базовых операций с использованием кода библиотеки JavaScript в SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Разработка надстроек для SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Безопасный доступ к данным и клиентские объектные модели для надстроек SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Работа с внешними данными в SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Протокол Open Data](http://www.odata.org/)
    
  
-  [OData: формат нотации объектов JavaScript (JSON)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  
-  [Установка настраиваемых разрешений для списка с помощью интерфейса REST](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)
    
  

  
    
    

