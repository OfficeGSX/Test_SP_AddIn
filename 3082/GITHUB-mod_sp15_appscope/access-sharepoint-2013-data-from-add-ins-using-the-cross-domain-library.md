---
title: Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios
ms.prod: SHAREPOINT
ms.assetid: bc37ff5c-1285-40af-98ae-01286696242d
---



# Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios
Aprenda a acceder a datos en un sitio web de SharePoint desde el complemento mediante la biblioteca entre dominios en SharePoint 2013.
Normalmente, al crear Complementos de SharePoint, tiene que incorporar datos de diversos orígenes. Sin embargo, por  [razones de seguridad](http://msdn.microsoft.com/library/cc709423.aspx), existen mecanismos de bloqueo que impiden comunicarse con más de un dominio a la vez. Estos mecanismos de seguridad se implementan en casi todos los exploradores, lo que dificulta o hace imposible realizar llamadas del cliente entre dominios.




La figura 1 muestra una solicitud bloqueada entre dominios.
**Figura 1. Solicitud bloqueada entre dominios**




Cuando un usuario solicita una página desde el dominio del complemento (1), la comunicación del cliente está enlazada únicamente a ese dominio. El complemento puede emitir llamadas del cliente desde la página solo a otros recursos en el mismo dominio. Sin embargo, los complementos por lo general requieren recursos de otros dominios, como el dominio SharePoint, para atender sus escenarios. En el código de la página, puede intentar emitir una solicitud al dominio de SharePoint (2), que está bloqueado por el explorador. Por lo general, aparece un error **Acceso denegado**. El error no implica que no tenga permisos en los recursos solicitados, pero, lo más probable es que ni siquiera pueda emitir una solicitud en los recursos mencionados.Al usar la biblioteca entre dominios, las páginas web en el complemento pueden acceder a datos en el dominio del complemento y el dominio de SharePoint. La biblioteca entre dominios es una alternativa del lado cliente con forma de un archivo JavaScript (SP.RequestExecutor.js) que se hospeda en el sitio web de SharePoint al que puede hacer referencia en su complemento remoto. Esta biblioteca entre dominios le permite interactuar con más de un dominio en la página del complemento remoto a través de un proxy. Es una buena opción si quiere que el código del complemento se ejecute en el cliente en lugar de en el servidor o si existen barreras de conectividad, como firewalls, entre SharePoint y su infraestructura remota. Puede tener acceso a los datos en el sitio web de host: por ejemplo, puede ver las listas con las que los usuarios finales interactúan independientemente de su complemento. O bien, puede tener acceso a datos en la web del complemento, como listas aprovisionadas específicamente para el complemento. Los complementos también pueden tener acceso a otras colecciones de sitios y sitios web siempre y cuando el complemento tenga permisos del ámbito de inquilino y se haya implementado como una instalación por lotes mediante el catálogo de complementos.
> **NOTA**
> En este tema, **dominio de complemento** se refiere al domino que hospeda las páginas de complementos. Este puede ser el dominio de una aplicación web remota en una aplicación hospedada por el proveedor, pero las páginas de complementos también pueden estar en SharePoint en la web de complemento y hacer llamadas al dominio web de host. En el último escenario, el dominio del complemento es el dominio de la web de complemento.




El principal ejemplo en este artículo muestra cómo crear un complemento que lea datos en la web de complemento y los muestre en una página web. La sección  [Pasos siguientes](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Next) muestra más escenarios que se crean además del ejemplo principal.
## Requisitos previos para usar los ejemplos de este artículo
<a name="SP15Accessdatafromremoteapp_Prereq"> </a>

Para seguir los ejemplos de este artículo, necesita lo siguiente:




-  [Visual Studio 2012](https://www.microsoft.com/es-es/download/details.aspx?id=30682)


-  [Microsoft Office Developer Tools para Visual Studio 2012](https://msdn.microsoft.com/es-es/office/aa905340.aspx)


- Un entorno de desarrollo de SharePoint 2013 (hay que aislar la aplicación en los escenarios locales)



## Leer datos en la web de complemento mediante la biblioteca entre dominios
<a name="SP15Accessdatafromremoteapp_Codeexample"> </a>

En este ejemplo, hay una página sencilla hospedada fuera de SharePoint que usa un extremo de transferencia de estado presencial (REST) para leer datos en un sitio web de SharePoint (la web de complemento). Dado que la biblioteca entre dominios necesita una web de complemento, tiene sentido empezar con este escenario.



Para leer datos de la web de complemento, debe hacer lo siguiente:




1. Crear una Complemento de SharePoint y proyectos web.


2. Crear elementos de lista en la web de complemento. Este paso también garantiza que se cree una web de complemento cuando los usuarios implementen el complemento.


3. Crear una página de complemento que use la biblioteca entre dominios para leer los elementos de lista.


La figura 2 muestra una página web con los datos en la web de complemento.




**Figura 2. Página web que muestra los datos en la web del complemento**








![Pantalla de resultados de ejemplo de lectura de elementos entre dominios](images/CrossDomainReadItemsResult.png)




### Crear una Complemento de SharePoint y proyectos web.


1. Abra Visual Studio 2012 como administrador. (Para hacerlo, haga clic con el botón secundario en el icono de Visual Studio 2012 en el menú **Inicio** y elija **Ejecutar como administrador**).


2. Cree un proyecto nuevo usando la plantilla **Complemento para SharePoint 2013**.

    La plantilla **Complemento para SharePoint 2013** en Visual Studio 2012 está ubicada en **Plantillas** **>** **Visual C#**, **Office SharePoint** **>** **Complementos**.


3. Indique la dirección URL del sitio web de SharePoint que quiere usar para la depuración.


4. Seleccione **Hospedado por el proveedor** como opción de hospedaje del complemento.

    > **NOTA**
      > También puede usar la biblioteca entre dominios en una Complementos hospedados en SharePoint. Sin embargo, en una Complementos hospedados en SharePoint la página de complementos ya está en la web de complemento, en cuyo caso no necesitaría que la biblioteca entre dominios lea los elementos de lista. Para ver una muestra de Complementos hospedados en SharePoint que lea datos en la web de host, vea  [Usar la biblioteca entre dominios en un complemento hospedado por SharePoint (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814) o vea [Acceder a datos de web de host](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) más adelante en este artículo.

### Crear elementos de lista en la web de complemento


1. Haga clic con el botón secundario en el proyecto de Complemento de SharePoint en el **Explorador de soluciones**. Elija **Agregar** **>** **Nuevo elemento**.


2. Elija **Elementos de Visual C#** **>** **Office/SharePoint** **>** **Lista**. Establezca el nombre de la lista en **Anuncios**.


3. Haga doble clic en **Anuncios** **>** **Elements.xml**. Pegue los siguientes nodos XML como elementos secundarios del elemento **ListInstance**.

 ```

<Data>
    <Rows>
        <Row>
            <Field Name="Title">Lorem ipsum 1</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
        <Row>
            <Field Name="Title">Lorem ipsum 2</Field>
            <Field Name="Body">Sed ut perspiciatis, unde omnis iste...</Field>
        </Row>
    </Rows>
</Data>
 ```


### Para agregar una nueva página que use la biblioteca entre dominios


1. Haga doble clic en **Default.aspx** en el proyecto web en el **Explorador de soluciones**.


2. Copie el siguiente código en el archivo Default.aspx. El código realiza las siguientes tareas:

  - Carga la biblioteca jQuery desde la CDN de Microsoft.


  - Proporciona un marcador de posición para el resultado.


  - Extrae la dirección URL de la web de complemento de la cadena de consulta.


  - Carga la biblioteca entre dominios JavaScript usando la función **getScript** de jQuery.

    La función carga los recursos necesarios y luego continúa en la función especificada, garantizando que la biblioteca entre dominios se cargue y esté disponible para que la use el código subsiguiente.


  - Crea una instancia del objeto **RequestExecutor**. De manera predeterminada, RequestExecutor usa la web de complemento como el sitio de contexto.

    > **NOTA**
      > Puede cambiar el sitio de contexto a otros sitios distintos que la web de complemento mediante el extremo de **AppContextSite** (REST) o el objeto (JSOM). Para obtener más información sobre AppContextSite, vea [Acceder a datos de web de host](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) más adelante en este artículo.
  - Emite una llamada REST al extremo de los elementos de lista.


  - Controla la finalización correcta y muestra los elementos de lista en la página web.


  - Controla los errores y muestra los mensajes de error en la página web.



 ```

<html>
    <head>
        <title>Cross-domain sample</title>
    </head>
    <body>
        <!-- This is the placeholder for the announcements -->
        <div id="renderAnnouncements"></div>
        <script 
            type="text/javascript" 
            src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js">
        </script>
        <script type="text/javascript">
          var hostweburl;
          var appweburl;

          // Load the required SharePoint libraries
          $(document).ready(function () {
            //Get the URI decoded URLs.
            hostweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPHostUrl")
            );
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );

            // resources are in URLs in the form:
            // web_url/_layouts/15/resource
            var scriptbase = hostweburl + "/_layouts/15/";

            // Load the js files and continue to the successHandler
            $.getScript(scriptbase + "SP.RequestExecutor.js", execCrossDomainRequest);
          });

          // Function to prepare and issue the request to get
          //  SharePoint data
          function execCrossDomainRequest() {
            // executor: The RequestExecutor object
            // Initialize the RequestExecutor with the add-in web URL.
            var executor = new SP.RequestExecutor(appweburl);

            // Issue the call against the add-in web.
            // To get the title using REST we can hit the endpoint:
            //      appweburl/_api/web/lists/getbytitle('listname')/items
            // The response formats the data in the JSON format.
            // The functions successHandler and errorHandler attend the
            //      sucess and error events respectively.
            executor.executeAsync(
                {
                  url:
                      appweburl +
                      "/_api/web/lists/getbytitle('Announcements')/items",
                  method: "GET",
                  headers: { "Accept": "application/json; odata=verbose" },
                  success: successHandler,
                  error: errorHandler
                }
            );
          }

          // Function to handle the success event.
          // Prints the data to the page.
          function successHandler(data) {
            var jsonObject = JSON.parse(data.body);
            var announcementsHTML = "";

            var results = jsonObject.d.results;
            for (var i = 0; i < results.length; i++) {
              announcementsHTML = announcementsHTML +
                  "<p><h1>" + results[i].Title +
                  "</h1>" + results[i].Body +
                  "</p><hr>";
            }

            document.getElementById("renderAnnouncements").innerHTML =
                announcementsHTML;
          }

          // Function to handle the error event.
          // Prints the error message to the page.
          function errorHandler(data, errorCode, errorMessage) {
            document.getElementById("renderAnnouncements").innerText =
                "Could not complete cross-domain call: " + errorMessage;
          }

          // Function to retrieve a query string value.
          // For production purposes you may want to use
          //  a library to handle the query string.
          function getQueryStringParameter(paramToRetrieve) {
            var params =
                document.URL.split("?")[1].split("&amp;");
            var strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
              var singleParam = params[i].split("=");
              if (singleParam[0] == paramToRetrieve)
                return singleParam[1];
            }
          }
        </script>
    </body>
</html>
 ```


### Para crear y ejecutar la solución


1. Presione la tecla F5.

    > **NOTA**
      > Cuando presiona F5, Visual Studio compila la solución, implementa el complemento y abre la página de permisos para el complemento. 
2. Elija el botón **Confiar**.


3. Elija el icono del complemento en la página **Contenidos del sitio**.


Si prefiere ver ejemplos de código descargables, puede obtener este desde la galería de códigos. **Ejemplo de código: obtener elementos de lista mediante la biblioteca entre dominios**, mediante [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain) o [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain).




**Tabla 2. Solución de problemas de la solución**


|**Si ve…**|**Pruebe…**|
|:-----|:-----|
|Mensaje de error: Lo sentimos, pero hemos tenido algunos problemas para acceder a su sitio.  <br/> También hay un botón para solucionar el error, pero no corrige el problema.  <br/> |Puede que se haya topado con un problema conocido con zonas de seguridad en Internet Explorer. Vea  [Trabajar con la biblioteca entre dominios en diferentes zonas de seguridad de Internet Explorer en los complementos para SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md).  <br/> |
|Mensaje de error: Las funciones requeridas no son compatibles con el explorador. Asegúrese de que está usando IE8, una versión posterior u otro explorador moderno y de que la etiqueta meta "X-UA-Compatible" está definida en "IE=8" o posterior.  <br/> |La biblioteca entre dominios requiere un modo de documento de **IE8** o posterior. En algunos escenarios, el modo de documento está definido en **IE7** de forma predeterminada. Puede usar las herramientas de desarrollo de Internet Explorer para determinar y cambiar el modo de documento de la página. Para más información, consulte [Definir la compatibilidad de documentos](http://msdn.microsoft.com/library/cc288325.aspx).  <br/> |
|Mensaje de error: 'Type' no está definido.  <br/> Además, el complemento usa el modelo de objetos de JavaScript (JSOM).  <br/> |El JSOM usa el método **Type.registerNamespace** en la biblioteca de Microsoft Ajax para registrar el espacio de nombres **SP**. Use el siguiente código para agregar una referencia a la biblioteca de Microsoft Ajax desde la página: <br/>```HTML<script type="text/javascript"  src="//ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js"></script>```|
 

## Pasos siguientes
<a name="SP15Accessdatafromremoteapp_Next"> </a>

En este artículo se muestra cómo consultar un extremo de REST para leer datos de la web de complemento mediante una página de complementos que no esté hospedada en SharePoint. También puede explorar los siguientes escenarios y detalles sobre la biblioteca entre dominios.




### Use el JSOM para leer datos de la web de complemento
<a name="SP15Accessdatafromremoteapp_JSOM"> </a>

Según su preferencia, tal vez quiera usar el JSOM en lugar de REST para consultar datos de la web de complemento. Debe completar tareas adicionales para usar la biblioteca entre dominios con JSOM:




- Haga referencia al JSOM de SharePoint en la página de complementos.


- Inicialice el objeto **ProxyWebRequestExecutorFactory** y establézcalo como la fábrica del objeto de contexto.


- Acceda a los objetos de SharePoint para leer los datos de la lista.


- Cargue los objetos en el contexto y ejecute la consulta.


Para ver un ejemplo de código que muestre cómo llevar a cabo las tareas, vea  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain). Para obtener más información sobre cómo usar el JSOM, vea  [Usar el modelo de objetos de JavaScript (JSOM) en complementos de SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/09/04/using-the-javascript-object-model-jsom-in-apps-for-sharepoint.aspx).




### Acceder a datos de web de host
<a name="SP15Accessdatafromremoteapp_Hostweb"> </a>

El ejemplo en esta página muestra cómo leer datos de la web de complemento. Esto funciona bien como el ejemplo de inicio porque la biblioteca entre dominios usa inicialmente el complemento como sitio de contexto. Sin embargo, hay varios escenarios en los que puede convenirle acceder a datos en la web de host. Hay algunas tareas necesarias para acceder a datos en la web de host:




- Establezca la web de host como el sitio de contexto para la biblioteca entre dominios.


- Proporcione permisos adecuados al complemento.


Puede cambiar el sitio de contexto mediante el extremo de **AppContextSite** (REST) o el objeto (JSOM). El siguiente ejemplo muestra cómo cambiar el sitio de contexto mediante el extremo de REST:





```

executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/title?@target='" +
            hostweburl + "'",
        method: "GET",
        headers: { "Accept": "application/json; odata=verbose" },
        success: successHandler,
        error: errorHandler
    }
);```

El siguiente ejemplo de código muestra cómo cambiar el sitio de contexto mediante JSOM:





```

context = new SP.ClientContext(appweburl);
factory = new SP.ProxyWebRequestExecutorFactory(appweburl);
context.set_webRequestExecutorFactory(factory);
appContextSite = new SP.AppContextSite(context, hostweburl);

this.web = appContextSite.get_web();
context.load(this.web);```

De manera predeterminada, el complemento tiene permisos para la web de complemento, pero no para la web de host. El siguiente ejemplo muestra una sección de manifiesto que declara una solicitud de permiso para leer datos de la web de host:





```XML

<AppPermissionRequests>
    <AppPermissionRequest 
        Scope="http://sharepoint/content/sitecollection/web" 
        Right="Read" />
</AppPermissionRequests>```

Asegúrese de que crea un recurso en la web de complemento (como una lista o página vacía) para forzar el aprovisionamiento de la web de complemento, lo cual es necesario para usar la biblioteca entre dominios.




### Acceder a datos en colecciones de sitios
<a name="SP15Accessdatafromremoteapp_TenantScope"> </a>

Con la biblioteca entre dominios, puede acceder a datos en colecciones de sitios en el mismo inquilino. Hay algunas tareas que necesita completar para acceder a datos en las colecciones de sitios:




- Agregue una solicitud de permiso para acceder a datos en el inquilino.


- En el código, cambie el sitio de contexto a las colecciones de sitios que quiera consultar.


- Agregue el complemento al catálogo de complementos.


- Implemente el complemento como un complemento con ámbito de inquilino en un sitio web. Para ver un ejemplo de cómo implementar un complemento con ámbito de inquilino, consulte la descripción del ejemplo de código  [Usar la biblioteca entre dominios en un complemento con ámbito de inquilino (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e).


El complemento también necesita permiso para acceder a datos del inquilino. El siguiente ejemplo muestra una sección de manifiesto que declara una solicitud de permiso para leer datos del inquilino:





```XML

<AppPermissionRequests>
  <AppPermissionRequest 
    Scope="http://sharepoint/content/tenant" 
    Right="Read" />
</AppPermissionRequests>```

Para cambiar el sitio de contexto en el código, use el extremo de **AppContextSite** (REST) o el objeto (JSOM), del mismo modo que en la sección [Acceder a datos de web de host](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb). Este es un recordatorio del extremo de REST: /_api/SP.AppContextSite(@target)/web/title?@target='weburl'. Aquí tiene un ejemplo de cómo crear una instancia para el objecto en JSOM:  `appContextSite = new SP.AppContextSite(context, weburl);`.



Como desarrollador, solo puede implementar complementos con ámbito de inquilino desde el catálogo de complementos. Puede aprovisionar un catálogo de complementos en los entornos locales o de SharePoint Online. Cargar el complemento al catálogo de complementos es tan sencillo como cargar un archivo a una biblioteca de documentos. Consulte  [Agregar complementos personalizados al sitio de Catálogo de complementos](http://office.microsoft.com/es-es/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx) para obtener instrucciones detalladas.



En el catálogo de complementos puede implementar el complemento en uno o varios sitios web del inquilino. Dado que el complemento tiene permisos para acceder a datos en el inquilino, solo tiene que implementar en un sitio web para acceder a datos en todo el inquilino. Consulte  [Implementar un complemento personalizado](http://office.microsoft.com/es-es/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx) para obtener instrucciones de cómo implementar un complemento en el catálogo de complementos.



Para descargar un ejemplo de código que muestre cómo acceder a datos en colecciones de sitios, vea  [Usar la biblioteca entre dominios en un complemento con ámbito de inquilino (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e).




### Emitir llamadas entre diferentes zonas de seguridad
<a name="SP15Accessdatafromremoteapp_IEZones"> </a>

La biblioteca entre dominios usa una página de proxy que se hospeda en un **IFrame** de la página del complemento para habilitar la comunicación. Cuando la página del complemento y el sitio web de SharePoint están en zonas de seguridad diferentes, no se pueden enviar cookies de autorización. Si no hay ninguna cookie de autorización y el IFrame trata de cargar la página proxy, se le redirigirá a la página de inicio de sesión de SharePoint. Por razones de seguridad, la página de inicio de sesión de SharePoint no puede estar dentro de un IFrame. En estos escenarios, la biblioteca no puede cargar la página proxy y no es posible la comunicación con SharePoint.



Sin embargo, hay una solución para estos escenarios. La solución es el **patrón apphost**, que consiste en ajustar las páginas de complementos en una página hospedada en la web de complemento. Es una buena idea usar el patrón apphost en complementos que usan la biblioteca entre dominios, aun cuando no haya límites de seguridad evidentes. Para obtener más información, vea [Trabajar con la biblioteca entre dominios en diferentes zonas de seguridad de Internet Explorer en los complementos para SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md).




### Acceder a datos de un host remoto adicional en una Complementos hospedados en SharePoint
<a name="SP15Accessdatafromremoteapp_SPhosted"> </a>

De manera predeterminada, Complementos hospedados en SharePoint tiene permitido emitir llamadas entre dominios a la web de host, siempre y cuando tenga permisos adecuados. Sin embargo, una Complementos hospedados en SharePoint también puede especificar un host remoto en el atributo **AllowedRemoteHostUrl** de su **AppPrincipal**. Esto efectivamente permite emitir llamadas entre dominios de la web de complemento y de otro host en otro sitio.



Para descargar un ejemplo de una Complementos hospedados en SharePoint que use la biblioteca entre dominios, vea  [Ejemplo de código: usar la biblioteca entre dominios en un complemento hospedado por SharePoint (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814).




## Recursos adicionales
<a name="SP15Accessdatafromremoteapp_Addresources"> </a>


-  [SharePoint-Add-in-REST-OData-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-CrossDomain)


-  [SharePoint-Add-in-JSOM-CrossDomain](https://github.com/OfficeDev/SharePoint-Add-in-JSOM-CrossDomain)


-  [Ejemplo de código: Obtener el título de la web de host usando la biblioteca entre dominios (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Get-the-0ec36bb6)


-  [Ejemplo de código: Obtener el título de la web de host usando la biblioteca entre dominios (JSOM)](http://code.msdn.microsoft.com/office/SharePoint-2013-Get-the-563f2a3d)


-  [Ejemplo de código: Usar la biblioteca entre dominios en un complemento hospedado por SharePoint (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-00c37814)


-  [Ejemplo de código: Usar la biblioteca entre dominios en un complemento con ámbito de inquilino (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-6b3e4c1e)


-  [Ejemplo de código: Usar el control cromo y la biblioteca entre dominios (REST)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-a759e9f8)


-  [Ejemplo de código: Usar el control cromo y la biblioteca entre dominios (JSOM)](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-97c30a2e)


-  [Ejemplo de código: Usar acciones personalizadas y la biblioteca entre dominios para pedir libros](http://code.msdn.microsoft.com/SharePoint-2013-Open-a-36d1598d)


-  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Trabajar con la biblioteca entre dominios en diferentes zonas de seguridad de Internet Explorer en los complementos para SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)


-  [Crear una página de proxy personalizada para la biblioteca entre dominios en SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)


-  [Consultar un servicio remoto mediante el proxy web en SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)


-  [Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)


