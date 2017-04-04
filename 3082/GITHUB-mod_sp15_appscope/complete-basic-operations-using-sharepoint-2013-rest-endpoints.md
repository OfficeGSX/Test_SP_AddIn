---
title: Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013
Aprenda a realizar operaciones básicas de creación, lectura, actualización y eliminación (CRUD) con la interfaz REST de SharePoint 2013.
## Desarrollar con REST y las API cliente de SharePoint
<a name="ClientAPIs"> </a>

Puede hacer operaciones básicas de creación, lectura, actualización y eliminación (CRUD) usando la interfaz de transferencia de estado de representación (REST) que SharePoint 2013 proporciona. La interfaz REST expone todas las entidades y operaciones de SharePoint que están disponibles en las demás API cliente de SharePoint. Una de las ventajas de usar REST es que no tiene que agregar referencias a ninguna biblioteca ni ningún ensamblado cliente de SharePoint 2013, sino que debe hacer solicitudes HTTP a los extremos apropiados para recuperar o actualizar entidades de SharePoint, como webs, listas o elementos de lista. En  [Introducción al servicio REST para SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md) puede consultar una completa introducción a la interfaz REST de SharePoint 2013 y su arquitectura.
  
    
    
En  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md) y [Trabajar con carpetas y archivos con REST](working-with-folders-and-files-with-rest.md) se explica con gran detalle cómo se trabaja con entidades de SharePoint centrales. Consulte [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations) para ver un ejemplo de cómo ejecutar varias operaciones de este tipo en el contexto de una aplicación web ASP.NET escrita en C#.
  
    
    
Para más información sobre los conjuntos de API disponibles en la plataforma de SharePoint 2013, vea  [Elegir el conjunto de API correcto en SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx). Para más información sobre cómo usar las otras API cliente, consulte  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) y [Creación de aplicaciones de Windows Phone con acceso a SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx).
  
    
    

## Operaciones HTTP en servicios REST de SharePoint 2013
<a name="HTTPOps"> </a>

Los extremos del servicio REST de SharePoint 2013 corresponden a los tipos y miembros de los modelos de objetos de cliente de SharePoint. Al usar solicitudes HTTP, puede usar estos extremos REST para hacer tareas típicas CRUD ( **Create**, **Read**, **Update** y **Delete**) en entidades de SharePoint, como listas y sitios.
  
    
    
Generalmente, los extremos que representan a tareas **Read** se asignan a comandos **GET** HTTP. Los extremos que representan a tareas de actualización se asignan a comandos **POST** HTTP, y los extremos que representan a tareas de actualización o inserción se asignan a comandos **PUT** HTTP.
  
    
    
En SharePoint 2013, use **POST** para crear entidades tales como listas y sitios. El servicio REST de SharePoint 2013 admite el envío de comandos **POST** que incluyen definiciones de objetos a extremos que representan colecciones. Por ejemplo, podría enviar un comando **POST** que incluyera una nueva definición de objetos de lista en ATOM a la siguiente dirección URL para crear una lista de SharePoint:
  
    
    
 `http://<site url>/_api/web/lists`
  
    
    
Para tareas **POST**, toda propiedad innecesaria se establece en sus valores predeterminados. Si trata de establecer una propiedad de solo lectura como parte de una tarea **POST**, el servicio devuelve una excepción.
  
    
    
Use operaciones **PUT** y **MERGE** para actualizar objetos de SharePoint existentes. Los extremos de servicios que representan una operación **set** de propiedades de objeto admiten tanto solicitudes **PUT** como solicitudes **MERGE**. En el caso de las solicitudes **MERGE**, configurar las propiedades es opcional, de manera que aquellas que no defina explícitamente retendrán la propiedad actual. En el caso de los comandos **PUT**, sin embargo, las propiedades que no se configuren explícitamente se establecerán en los valores predeterminados. Además, si no especifica todas las propiedades necesarias en actualizaciones de objeto cuando use comandos **PUT** de HTTP, el servicio REST devolverá una excepción.
  
    
    
Use el comando HTTP **DELETE** con la dirección URL específica del extremo para eliminar el objeto de SharePoint representado por ese extremo. En el caso de los objetos reciclables, como listas, archivos y elementos de lista, esto da como resultado una operación **Recycle**.
  
    
    

## Lectura de datos con la interfaz REST de SharePoint 2013
<a name="ReadingData"> </a>

Para usar las capacidades de REST que están integradas en SharePoint 2013, construya una solicitud RESTful HTTP usando el estándar OData, que corresponde a la API del modelo de objetos de cliente que quiere usar. Cada entidad de SharePoint se expone en un extremo del sitio de SharePoint 2013 de destino y sus metadatos se representan en formato XML o JSON. Puede efectuar las solicitudes HTTP en cualquier lenguaje, incluidos, entre otros, JavaScript y C#.
  
    
    
Para leer información sobre un extremo de REST, debe conocer tanto la dirección URL del extremo como la representación OData de la entidad de SharePoint que se expone en el extremo. Por ejemplo, para recuperar todas las listas de un sitio de SharePoint concreto, debe hacer una solicitud **GET** a `http://<site url>/_api/web/lists`. Puede desplazarse a esta dirección URL en el explorador y ver el XML que devuelve. Si realiza la solicitud en código, puede especificar si quiere recibir la representación OData de las listas en XML o JSON.
  
    
    
El siguiente código C# muestra cómo hacer esta solicitud **GET** que devuelve una representación JSON de todas las listas de un sitio usando JQuery. También parte de la base de que el usuario tiene un token de acceso OAuth válido que se almacena en la variable **accessToken**. No necesita el token de acceso si hace la llamada desde la web del complemento, tal como lo haría en un complemento hospedado en SharePoint. Tenga en cuenta que no puede obtener un token de acceso desde el código que se esté ejecutando en un cliente del explorador. Este token debe obtenerlo desde el código que se esté ejecutando en un servidor. En  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) y [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md) se explica cómo se puede obtener un token de acceso.
  
    
    


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

Esta solicitud sería algo diferente si escribe el complemento en JavaScript pero usa la biblioteca entre dominios de SharePoint 2013. En este caso, no necesita proporcionar un token de acceso. El código siguiente muestra el aspecto de esta solicitud si usa la biblioteca entre dominios y quiere recibir la representación OData de las listas como XML en vez de JSON. (Como Atom es el formato de respuesta predeterminado, no hay que incluir un encabezado.) **Accept**. Consulte  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md), para obtener más información sobre cómo usar la biblioteca entre dominios.
  
    
    


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

El código del ejemplo siguiente muestra cómo solicitar una representación JSON de todas las listas de un sitio usando C#. Se presupone que el usuario tiene un token de acceso OAuth que se almacena en la variable  `accessToken`.
  
    
    


```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();
```


### Obtener propiedades que no se devuelven con el recurso
<a name="NavigationProperties"> </a>

Muchos valores de propiedades se devuelven cuando se recupera un recurso, pero con algunas propiedades, se debe enviar una solicitud **GET** directamente al extremo de la propiedad. Esto suele ser así con las propiedades que representan entidades de SharePoint.
  
    
    
El siguiente ejemplo muestra cómo obtener una propiedad anexando el nombre de la propiedad al extremo del recurso. El ejemplo obtiene el valor de la propiedad de **Author** de un recurso **File**.
  
    
    
http:// _<dirección url del sitio>_/_api/web/getfilebyserverrelativeurl('/ _<nombre de la carpeta>_/ _<nombre del archivo>_')/author
  
    
    
Para obtener los resultados en formato JSON, incluya un encabezado **Accept** establecido en `"application/json;odata=verbose"`.
  
    
    

## Escritura de datos mediante la interfaz de REST
<a name="WritingData"> </a>

Puede crear y actualizar entidades de SharePoint creando solicitudes RESTful HTTP en los extremos apropiados igual que cuando lee datos. No obstante, una de las diferencias clave es que se usa una solicitud **POST**. Cuando actualiza entidades, también pasa un método de solicitud HTTP **PUT** o **MERGE** agregando uno de estos términos a los encabezados de la solicitud como valor de la clave **X-HTTP-Method**. El método **MERGE** actualiza únicamente las propiedades de la entidad que especifica el usuario, mientras que el método **PUT** sustituye la entidad existente por otra nueva que se suministra en el cuerpo de **POST**. Use el método **DELETE** para eliminar la entidad. Si crea o actualiza una entidad, debe proporcionar una representación OData de la entidad que quiere crear o cambiar en el cuerpo de la solicitud HTTP.
  
    
    
Cuando cree, actualice o elimine entidades de SharePoint, es importante que tenga en cuenta que, si no usa OAuth para autorizar sus solicitudes, estas operaciones también necesitan el valor de resumen de formulario de solicitud del servidor como valor del encabezado **X-RequestDigest**. Puede recuperar este valor ejecutando una solicitud **POST** con un cuerpo vacío a `http://<site url>/_api/contextinfo` y extrayendo el valor del nodo `d:FormDigestValue` en el XML que el extremo **contextinfo** devuelve. En el siguiente ejemplo se ve una solicitud HTTP al extremo **contextinfo** de C#.
  
    
    


```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

Si usa el flujo de autorización y autenticación descrito en  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md), no necesita incluir el resumen de solicitud en sus solicitudes.
  
    
    
Si usa la biblioteca entre dominios de JavaScript, SP.RequestExecutor controla la obtención y el envío del valor de síntesis de formulario.
  
    
    
Si va a crear una Complemento de SharePoint hospedada en SharePoint, no tiene que ejecutar una solicitud HTTP diferente para recuperar el valor de síntesis del formulario, sino que puede recuperar el valor del código JavaScript de una página de SharePoint (si la página usa la página maestra predeterminada), tal y como se muestra en el siguiente ejemplo, en el que se usa JQuery y se crea una lista.
  
    
    


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

En el ejemplo siguiente se muestra cómo actualizar la lista que se creó en el ejemplo anterior. El ejemplo cambia el título de la lista, usa JQuery y presupone que esta operación se está ejecutando en un complemento hospedado en SharePoint.
  
    
    


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

El valor de la clave **IF-MATCH** en los encabezados de solicitud es el punto en el que se especifica el valor **etag** de una lista o elemento de lista. Este valor en concreto se aplica únicamente a las listas y elementos de lista, y tiene como objetivo ayudarle a evitar que se produzcan problemas de simultaneidad al actualizar dichas entidades. En el ejemplo anterior se usa un asterisco (*) para este valor y puede usarlo siempre que no tenga que preocuparse por evitar problemas de simultaneidad, de lo contrario, deberá obtener el valor **etag** o una lista o elemento de lista, ejecutando una solicitud **GET** que recupere la entidad. Los encabezados de la respuesta HTTP que obtenga pasarán el etag como el valor de la clave **ETag**. Este valor se incluye también en los metadatos de la entidad. En el ejemplo siguiente se muestra la etiqueta  `<entry>` de apertura del nodo XML que contiene la información de la lista. La propiedad **m:etag** contiene el valor **etag**.
  
    
    


```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">```


## Creación de un sitio con REST
<a name="bk_CreateSite"> </a>

En el siguiente ejemplo se muestra cómo crear un sitio en JavaScript.
  
    
    
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


## Cómo difieren las solicitudes REST por entorno.
<a name="bk_HowRequestsDiffer"> </a>

La creación y el envío de una solicitud HTTP puede variar en función del tipo de lenguaje, de biblioteca y de complemento, por lo que con frecuencia se debe cambiar uno o más componentes de la solicitud cuando se traduce una solicitud de un entorno a otro. Por ejemplo, las solicitudes de jQuery AJAX usan parámetros **data** y **type** para especificar el tipo y el cuerpo de la solicitud, pero las solicitudes de biblioteca entre dominios usan parámetros **body** y **method** para especificar dichos valores.
  
    
    
Las secciones siguientes describen otras diferencias comunes entre entornos.
  
    
    

### La manera de obtener y enviar el valor de síntesis de formulario depende del complemento
<a name="FormDigest"> </a>

Al enviar una solicitud POST, hay que incluir el valor de síntesis de formulario en el encabezado **X-RequestDigest**. Sin embargo, la forma de obtener y enviar el valor difiere según el complemento:
  
    
    

- En los complementos hospedados en SharePoint, basta con pasar el siguiente encabezado: 
  
    
    
 "X-RequestDigest": $("#__REQUESTDIGEST").val()
    
  
- En los complementos hospedados en la nube que usen OAuth, primero recupere el valor de síntesis de formulario enviando una solicitud al extremo de **contextinfo** y luego agréguelo a las solicitudes, tal como se muestra en [Escritura de datos mediante la interfaz de REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData).
    
  
- En los complementos hospedados en la nube que usan la biblioteca entre dominios de JavaScript, no es necesario especificar el valor de síntesis de formulario. De manera predeterminada, SP.RequestExecutor lo hace automáticamente. (También controla el valor content-length).
    
  

### Los complementos que usan OAuth deben pasar tokens de acceso en las solicitudes
<a name="OAuthTokens"> </a>

Los complementos hospedados en la nube usan OAuth o la biblioteca entre dominios para autorizar el acceso a datos de SharePoint. Los componentes de complemento con código que se ejecuta en un servidor web remoto deben usar OAuth para autorizar el acceso a datos de SharePoint. En este caso, se debe incluir un encabezado de **Authorization** para enviar el token de acceso. Vea [Lectura de datos con la interfaz REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData) para obtener un ejemplo que agrega un encabezado de autorización a un objeto. **HTTPWebRequest**.
  
    
    

> **NOTA**
> Los componentes de complementos hospedados en la nube que se escriben en JavaScript deben usar el objeto **SP.RequestExecutor** en la biblioteca entre dominios para acceder a datos de SharePoint. Las solicitudes de biblioteca entre dominios no necesitan incluir un token de acceso.
  
    
    

Para más información sobre los tokens de acceso de OAuth y cómo obtenerlos, vea  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) y [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### Los URI de extremo en solicitudes entre dominios usan SP.AppContextSite para cambiar el contexto
<a name="AppContextSite"> </a>

Las solicitudes se envían al extremo del recurso que se especifica en la propiedad **url** de la solicitud. Los URI de extremo usan el siguiente formato:
  
    
    
 _<url del sitio>_/_api/ _<contexto>_/ _<recurso>_ (ejemplo, https://contoso.com/_api/web/lists)
  
    
    
Las solicitudes de biblioteca entre dominios usan este formato cuando acceden a datos en la web de complemento, que es el contexto predeterminado para las solicitudes de biblioteca entre dominios. Pero para acceder a datos en la web de hospedaje o en otra colección de sitios, las solicitudes deben inicializar la web de hospedaje u otra colección de sitios como contexto. Para ello, usan el extremo **SP.AppContextSite** del URI, tal como se muestra en la Tabla 1. Los URI de ejemplo de la Tabla 1 usan el alias **@target** para enviar la dirección URL de destino de la cadena de consulta porque la dirección URL contiene un carácter especial (':').
  
    
    

> **NOTA**
> Se requiere una instancia web de complemento para que un complemento hospedado en la nube acceda a datos de SharePoint cuando se usa la biblioteca entre dominios. 
  
    
    


**Tabla 1. Uso del extremo SP.AppContextSite para cambiar el contexto de la solicitud**


|**Tipo de complemento**|**Escenario de acceso a datos entre dominios**|**URI de extremo de ejemplo**|
|:-----|:-----|:-----|
|Hospedaje en la nube  <br/> |Acceso de componentes de complemento de JavaScript a datos de web de hospedaje con la biblioteca entre dominios  <br/> | _<dirección url web de la aplicación>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<dirección url web del host>_'  <br/> |
|Hospedaje en la nube  <br/> |Acceso de componentes de complemento de JavaScript a datos en una colección de sitios distinta a la web de hospedaje con la biblioteca entre dominios (solo complementos con ámbito de inquilino)  <br/> | _<dirección url web de la aplicación>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<dirección url del sitio de destino>_'  <br/> |
|Hospedada en SharePoint  <br/> |Acceso de componentes de web de complemento a datos en otra colección de sitios (solo complementos con ámbito de inquilino)  <br/> | _<dirección url web de la aplicación>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<dirección url del sitio de destino>_'  <br/> |
   

> **NOTA**
> Los escenarios de acceso a datos entre dominios también requieren permisos de complemento adecuados. Para obtener más información, vea  [Acceder a datos de web de hospedaje](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) y [Acceder a datos en colecciones de sitios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope). 
  
    
    

Las Complementos de SharePoint pueden obtener la dirección URL de la web de complemento y la dirección URL de la web de hospedaje de la cadena de consulta de la página del complemento, tal como se muestra en el ejemplo de código siguiente. El ejemplo también muestra cómo se hace referencia a la biblioteca entre dominios, lo que se define en el archivo SP.RequestExecutor.js de la web de hospedaje. El ejemplo asume que su complemento se inicia desde SharePoint. Vea  [Flujo de código de autenticación de OAuth para aplicaciones en SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md) para obtener instrucciones sobre la configuración correcta del contexto de SharePoint cuando el complemento no se inicia desde SharePoint.
  
    
    


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


## Propiedades usadas en solicitudes de REST
<a name="bk_requestElements"> </a>

La tabla 2 muestra propiedades que se suelen usar en solicitudes HTTP para el servicio REST de SharePoint.
  
    
    

**Tabla 2. Cuándo usar propiedades de solicitud de REST en solicitudes HTTP**


|**Propiedades**|**Cuándo se requiere**|**Descripción**|
|:-----|:-----|:-----|
|**url** <br/> |Todas las solicitudes  <br/> |La dirección URL del extremo del recurso de REST. Ejemplo:  `http://<site url>/_api/web/lists` <br/> |
|**method** (o **type**)  <br/> |Todas las solicitudes  <br/> |El método de solicitud HTTP: **GET** para operaciones de lectura y **POST** para operaciones de escritura. Las solicitudes **POST** pueden realizar operaciones de actualización o eliminación especificando un verbo **DELETE**, **MERGE** o **PUT** en el encabezado **X-HTTP-Method**.  <br/> |
|**body** (o **data**)  <br/> |Solicitudes **POST** que envían datos en el cuerpo de la solicitud <br/> |El cuerpo de la solicitud POST. Envía datos (como tipos complejos) que no se pueden enviar en el URI de extremo. Se usa con el encabezado **content-length**.  <br/> |
|Encabezado **Autenticación** <br/> |Complementos remotos que usan OAuth para autenticar usuarios. No se aplica cuando se usa JavaScript o la biblioteca entre dominios.  <br/> |Envía el token de acceso de OAuth (obtenido desde un servidor de tokens seguro del servicio de control de acceso (ACS) de Microsoft) que se usa para autenticar al usuario en la solicitud. Ejemplo:  `"Authorization": "Bearer " + accessToken`, donde  `accessToken` representa la variable que almacena el token. Los tokens se deben recuperar usando código del servidor. <br/> |
|Encabezado de **X-RequestDigest** <br/> |Solicitudes **POST** (excepto las solicitudes SP.RequestExecutor) <br/> |Los complementos remotos que usan OAuth deben obtener el valor de síntesis del formulario del extremo de  `http://<site url>/_api/contextinfo`. Los complementos hospedados por SharePoint pueden obtener el valor del control de página **#__REQUESTDIGEST**, siempre que esté disponible en la página de SharePoint. Vea  [Escritura de datos mediante la interfaz de REST](#WritingData).  <br/> |
|Encabezado **accept** <br/> |Solicitudes que devuelven metadatos de SharePoint  <br/> |Especifica el formato de los datos de respuesta recibidos del servidor. El formato predeterminado es  `application/atom+xml`. Ejemplo:  `"accept":"application/json;odata=verbose"` <br/> |
|Encabezado **content-type** <br/> |Solicitudes **POST** que envían datos en el cuerpo de la solicitud <br/> |Especifica el formato de los datos que el cliente está enviando al servidor. El formato predeterminado es  `application/atom+xml`. Ejemplo:  `"content-type":"application/json;odata=verbose"` <br/> |
|Encabezado **content-length** <br/> |Solicitudes **POST** que envían datos en el cuerpo de la solicitud (excepto las solicitudes SP.RequestExecutor) <br/> |Especifica la longitud del contenido. Ejemplo:  `"content-length":requestBody.length` <br/> |
|Encabezado **IF-MATCH** <br/> |Solicitudes **POST** para las operaciones **DELETE**, **MERGE** o **PUT**, principalmente para cambiar listas y bibliotecas.  <br/> |Ofrece una forma de comprobar que el objeto que se está modificando no ha cambiado desde la última vez que se recuperó. También le permite especificar si quiere sobrescribir algún cambio, como se muestra en el siguiente ejemplo:  `"IF-MATCH":"*"` <br/> |
|Encabezado **X-HTTP-Method** <br/> |Solicitudes **POST** para las operaciones **DELETE**, **MERGE** o **PUT** <br/> |Se usa para especificar que la solicitud haga una operación de actualización o eliminación. Ejemplo:  `"X-HTTP-Method":"PUT"` <br/> |
|**binaryStringRequestBody** <br/> |Solicitudes **POST** SP.RequestExecutor que envían datos binarios en el cuerpo <br/> |Especifica si el cuerpo de la solicitud es una cadena binaria. **Boolean**.  <br/> |
|**binaryStringResponseBody** <br/> |Solicitudes SP.RequestExecutor que devuelven datos binarios  <br/> |Especifica si la respuesta es una cadena binaria. **Boolean**.  <br/> |
   

## Soporte de trabajos por lotes
<a name="batch"> </a>

El servicio REST de SharePoint Online (y SharePoint 2016 local o versiones posteriores) admite la combinación de varias solicitudes en una sola llamada al servicio mediante el uso de la opción de consulta  `$batch` de OData. Para obtener información detallada y vínculos a los ejemplos de código, vea [Realizar solicitudes de lote con las API de REST](make-batch-requests-with-the-rest-apis.md).
  
    
    

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Trabajar con listas y elementos de lista con REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabajar con carpetas y archivos con REST](working-with-folders-and-files-with-rest.md)
    
  
-  [API de REST, extremos y ejemplos de SharePoint 2013](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: Realizar operaciones básicas de acceso a datos en archivos y carpetas usando REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Trabajar con datos externos en SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Protocolo Open Data](http://www.odata.org/)
    
  
-  [OData: Formato de notación de objetos JavaScript (JSON)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  
-  [Establecer permisos personalizados en una lista usando la interfaz REST](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)
    
  

  
    
    

