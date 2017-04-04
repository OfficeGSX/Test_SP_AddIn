---
title: Usar el widget experimental Vista de lista de escritorio en complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: b1e26543-9d03-4759-80bf-e6ce729a1b19
---


# Usar el widget experimental Vista de lista de escritorio en complementos para SharePoint
Aprenda a usar el widget Vista de lista de escritorio en cualquier página web aunque no esté hospedada en SharePoint. Use el widget Vista de lista en los complementos para mostrar datos de las listas que se hospedan en un sitio de SharePoint.
> **NOTA**
> Los Office Web Widgets: Experimental solo se proporcionan con fines de investigación y comentarios. No se deben usar en situaciones de producción. El comportamiento de Office Web Widgets puede cambiar significativamente en futuras versiones. Lea y revise los  [Términos de licencia de Office Web Widgets: Experimental](office-web-widgetsexperimental-license-terms.md). 
  
    
    

Puede usar el widget Vista de lista para mostrar los datos de una lista de SharePoint al igual que se hace con el widget Vista de lista normal, pero aquel se puede usar en los complementos y los sitios web que no están hospedados necesariamente en SharePoint.
**Figura 1. Widget Vista de lista de escritorio que muestra los datos de una lista**

  
    
    

  
    
    
![Control experimental Vista de lista de escritorio en una página](images/DesktopListView_basic.png)
  
    
    

  
    
    

  
    
    

## Introducción

Puede especificar la vista en la lista de SharePoint que quiera usar para mostrar los datos. El widget Vista de lista muestra las columnas y los elementos en el orden especificado por la vista.
  
    
    
El widget Vista de lista usa la biblioteca entre dominios para obtener los datos de lista. Por este motivo, la comunicación se produce a nivel de cliente.
  
    
    

> **PRECAUCIóN**
> El widget Vista de lista de escritorio no habilita todos los escenarios de la vista de lista nativa. 
  
    
    

Los escenarios siguientes no se han habilitado en la versión actual del widget:
  
    
    

- Usar el widget en esquemas de autenticación que no sean compatibles de forma nativa con la biblioteca entre dominios.
    
  
- Usar el widget con orígenes de datos que no sean listas ni bibliotecas de SharePoint.
    
  
- Enlazar datos en el widget.
    
  
- Vistas de usuario táctiles.
    
  
- Edición en línea de usuarios.
    
  
- Mostrar información de presencia.
    
  
- Proporcionar plantillas de representación personalizadas.
    
  
- Escenarios locales. De momento, el widget solo funciona en SharePoint Online.
    
  

## Requisitos previos para usar los ejemplos de este artículo

Para seguir los ejemplos de este artículo, necesita lo siguiente:
  
    
    

- Visual Studio 2013
    
  
- Administrador de paquetes NuGet. Para más información, vea  [Instalar NuGet](http://go.microsoft.com/fwlink/?LinkId=271465).
    
  
- Un entorno de desarrollo de SharePoint 2013 (hay que aislar la aplicación en los escenarios locales). 
    
  
- Paquete Office Web Widgets: Experimental de NuGet. Para más información sobre cómo instalar un paquete de NuGet, vea  [Administrar paquetes de NuGet desde el cuadro de diálogo](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog). También puede visitar la  [página de la galería de NuGet](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/).
    
  

## Usar el widget Vista de lista de escritorio en un Complemento de SharePoint hospedado por el proveedor

En este ejemplo, hay una página sencilla hospedada fuera de SharePoint que declara un widget Vista de lista de escritorio.
  
    
    
Para usar el widget Vista de lista, debe hacer lo siguiente:
  
    
    

- Crear un Complemento de SharePoint y proyectos web.
    
  
- Crear una lista en la web de complemento. Este paso también garantiza que se cree una web de complemento cuando los usuarios implementen el complemento.
    
    > **NOTA**
      > La biblioteca entre dominios requiere la existencia de un web de complemento. El widget Vista de lista se comunica con SharePoint usando la biblioteca entre dominios. 
- Crear una página de complemento que declare una instancia del widget Vista de lista con formato HTML.
    
  

### Para crear un Complemento de SharePoint y proyectos web


1. Abra Visual Studio 2013 como administrador. (Para ello, seleccione el icono de Visual Studio 2013 en el menú **Inicio** y elija **Ejecutar como administrador**).
    
  
2. Cree un nuevo proyecto usando la plantilla de **Complemento de SharePoint 2013**. La plantilla de Complemento de SharePoint 2013 está en **Plantillas**> **Visual C#**, **Office/SharePoint**> **Complementos**.
    
  
3. Indique la dirección URL del sitio web de SharePoint que quiere usar para la depuración.
    
  
4. Seleccione **Hospedado por el proveedor** como la opción de hospedaje del complemento.
    
    > **NOTA**
      > También puede utilizar el widget Vista de lista de escritorio con otras opciones de hospedaje o incluso con complementos para Office o su propio sitio web. 
5. Seleccione **Aplicación de formularios Web Forms ASP.NET** como tipo de proyecto de aplicación web.
    
  
6. Seleccione **Servicio de control de acceso de Windows Azure** como opción de autenticación.
    
  

### Para crear una lista en la web de complemento


1. Elija el proyecto de Complemento de SharePoint en el **Explorador de soluciones** y luego seleccione **Agregar**> **Nuevo elemento…**
    
  
2. Elija **Elementos de Visual C#**> **Office/SharePoint**> **Lista**. Escriba **Anuncios** en el cuadro de texto **Nombre**. Elija **Agregar**.
    
  
3. Elija **Crear una instancia de lista basada en una plantilla de lista existente**. Seleccione la plantilla **Anuncios** y, por último, **Finalizar**.
    
  

### Para agregar una nueva página que use el widget Vista de lista de escritorio


1. Elija la carpeta **Páginas** del proyecto web en **Explorador de soluciones**.
    
  
2. Copie el código siguiente y péguelo en un archivo **ASPX** del proyecto. El código realiza las tareas siguientes:
    
  - Agrega referencias a las bibliotecas y los recursos de Office necesarios.
    
  
  - Proporciona un marcador de posición para el widget Vista de lista.
    
  
  - Inicializa el tiempo de ejecución de los controles.
    
  
  - Ejecuta el método **renderAll** del tiempo de ejecución de los controles de Office.
    
  

  ```
  
<!DOCTYPE html>
<html>
<head>
    <!-- IE9 or superior -->
    <meta http-equiv="x-ua-compatible" content="IE=10">
    <title>Desktop List View HTML Markup</title>

    <!-- Controls Specific CSS File -->
    <link rel="stylesheet" type="text/css" href="/Scripts/Office.Controls.css" />

    <!-- Ajax, jQuery, and utils -->
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js.js">
    </script>
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js">
    </script>
    <script type="text/javascript">

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

    <!-- Cross-Domain Library and Office controls runtime -->
    <script type="text/javascript">
        //Register namespace and variables used through the sample
        Type.registerNamespace("Office.Samples.ListViewBasic");
        //Retrieve context tokens from the querystring
        Office.Samples.ListViewBasic.appWebUrl =
            decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));
        Office.Samples.ListViewBasic.hostWebUrl =
            decodeURIComponent(getQueryStringParameter("SPHostUrl"));
        Office.Samples.ListViewBasic.ctag =
            decodeURIComponent(getQueryStringParameter("SPClientTag"));

        //Pattern to dynamically load JSOM and the cross-domain library
        var scriptbase =
            Office.Samples.ListViewBasic.hostWebUrl + "/_layouts/15/";

        //Get the cross-domain library
        $.getScript(scriptbase + "SP.RequestExecutor.js", 
            //Get the Office controls runtime and 
            //  continue to the createControl function
            function () {
                $.getScript("../Scripts/Office.Controls.js", createControl);
            }
        );
    </script>

    <!-- List View -->
    <script 
        src="../Scripts/Office.Controls.ListView.debug.js" 
        type="text/javascript">
    </script>

    <!-- SharePoint CSS -->
    <script type="text/javascript">
        document.addEventListener("DOMContentLoaded", function () {
            // The resource files are in a URL in the form:
            // web_url/_layouts/15/Resource.ashx
            var scriptbase =
                Office.Samples.ListViewBasic.appWebUrl + "/_layouts/15/";

            // Dynamically create the invisible iframe.
            var blankiframe;
            var blankurl;
            var body;
            blankurl =
                Office.Samples.ListViewBasic.appWebUrl + "/Pages/blank.html";
            blankiframe = document.createElement("iframe");
            blankiframe.setAttribute("src", blankurl);
            blankiframe.setAttribute("style", "display: none");
            body = document.getElementsByTagName("body");
            body[0].appendChild(blankiframe);

            // Dynamically create the link element.
            var dclink;
            var head;
            dclink = document.createElement("link");
            dclink.setAttribute("rel", "stylesheet");
            dclink.setAttribute("href",
                                scriptbase +
                                "defaultcss.ashx?ctag=" +
                                Office.Samples.ListViewBasic.ctag
                                );
            head = document.getElementsByTagName("head");
            head[0].appendChild(dclink);
        }, false);
    </script>
</head>
<body>
    Basic List View sample (HTML markup declaration):
    <div id="ListViewDiv"
         data-office-control="Office.Controls.ListView"
         data-office-options='{"listUrl" : getListUrl()}'>
    </div>

    <script type="text/javascript">
        function createControl() {
            //Initialize Controls Runtime
            Office.Controls.Runtime.initialize({
                sharePointHostUrl: Office.Samples.ListViewBasic.hostWebUrl,
                appWebUrl: Office.Samples.ListViewBasic.appWebUrl
            });

            //Render the widget, this must be executed after the
            //placeholder DOM is loaded
            Office.Controls.Runtime.renderAll();
        }

        function getListUrl() {
            return Office.Samples.ListViewBasic.appWebUrl +
                    "/_api/web/lists/getbytitle('Announcements')";
        }
    </script>
</body>
</html>
  ```


> **NOTA**
> El ejemplo de código anterior especifica explícitamente las direcciones URL de la web de host y la web de complemento para inicializar el tiempo de ejecución de los controles de Office. Sin embargo, si las direcciones URL de la web de complemento y la web de host se especifican en los parámetros de cadena de consulta **SPAppWebUrl** y **SPHostUrl** respectivamente, se puede pasar un objeto vacío para que el código intente obtener los parámetros de forma automática. Los parámetros **SPAppWebUrl** y **SPHostUrl** se incluyen en la cadena de consulta cuando se usa el token **{StandardTokens}**.
  
    
    

En el siguiente ejemplo se muestra cómo pasar un objeto vacío al método de inicialización:
  
    
    



```

// Initialize with an empty object and the code
// will attempt to get the tokens from the
// query string directly.
Office.Controls.Runtime.initialize({});
```


### Para crear y ejecutar la solución


1. Presione la tecla F5.
    
    > **NOTA**
      > Al presionar F5, Visual Studio crea la solución, implementa el complemento y abre la página de permisos del complemento. 
2. Elija el botón **Confiar**.
    
  
3. Elija el icono del complemento en la página **Contenidos del sitio**.
    
  
También puede bajarse este ejemplo desde la galería de código. Vea el ejemplo de código  [Usar el widget experimental Vista de lista de escritorio en un complemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076).
  
    
    

## 

En este artículo le enseñamos a usar el widget Vista de lista de escritorio en su complemento con HTML. También puede explorar los siguientes escenarios y detalles sobre el widget.
  
    
    

### Usar JavaScript para declarar el widget Vista de lista de escritorio

Quizás prefiera usar JavaScript en lugar de HTML para declarar el widget. Si es el caso, puede utilizar el siguiente marcado como marcador de posición para el widget.
  
    
    

```HTML

<div id="ListViewDiv"></div>
```

Use el siguiente código de JavaScript para crear una instancia de la vista de lista.
  
    
    



```
new Office.Controls.ListView(
    document.getElementById("ListViewDiv"), {
        listUrl: Office.Samples.ListViewBasic.appWebUrl + "/_api/web/lists/getbytitle('Announcements')"
    });
```

Si quiere ver un ejemplo de cómo hacer las tareas, vaya a la página **JSSimple.html** en el ejemplo de código [Usar el widget experimental Vista de lista de escritorio en un complemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076).
  
    
    

### Especificar una vista para mostrar los datos

Puede especificar una vista existente en la lista de SharePoint para que el widget Vista de lista muestre los datos usando la especificación de la vista.
  
    
    
Si usa código HTML para declarar el widget, puede usar la siguiente sintaxis para especificar una vista.
  
    
    



```

<div id="ListViewDiv"
        data-office-control="Office.Controls.ListView"
        data-office-options="{listUrl: 'list URL',
                            viewID: 'GUID'
                            }">
</div> 

```

Si quiere declarar el widget con JavaScript, use la siguiente sintaxis para especificar una vista.
  
    
    



```

new Office.Controls.ListView(
    document.getElementById("ListViewDiv"), {
        listUrl: "list URL",
        viewID: "GUID"
    });
```


## Conclusión

Puede usar el widget experimental Vista de lista de escritorio para mostrar datos de listas de SharePoint. El widget muestra datos en el modo de solo lectura. Envíe sus ideas y comentarios al  [sitio Office Developer Platform UserVoice](http://officespdev.uservoice.com/).
  
    
    

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Introducción a Office Web Widgets: Experimental](office-web-widgetsexperimental-overview.md)
    
  
-  [Términos de licencia de Office Web Widgets: Experimental](office-web-widgetsexperimental-license-terms.md)
    
  
-  [Página de la galería NuGet de Office Web Widgets: Experimental](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)
    
  
-  [Ejemplo de código: Usar el widget experimental Vista de lista de escritorio en un complemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)
    
  
-  [Usar el widget experimental Vista de lista de escritorio en complementos para SharePoint](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md)
    
  

