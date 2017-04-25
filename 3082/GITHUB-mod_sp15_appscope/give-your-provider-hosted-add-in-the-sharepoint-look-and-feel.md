---
title: Dar al complemento hospedado por el proveedor la apariencia de SharePoint
ms.prod: SHAREPOINT
ms.assetid: 53bf456d-a832-4b43-8ccd-42e4dab81dfc
---


# Dar al complemento hospedado por el proveedor la apariencia de SharePoint
Aprenda a dar a sus Complementos de SharePoint hospedados por el proveedor la apariencia de SharePoint.
Este es el segundo de una serie de artículos sobre los conceptos básicos de desarrollo de Complementos de SharePoint hospedados por el proveedor. Primero debe familiarizarse con los  [Complementos de SharePoint](sharepoint-add-ins.md) y con los anteriores artículos de esta serie:





-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)



En el artículo anterior de esta serie aprendimos a configurar un entorno de desarrollo y a usar Visual Studio para crear un primer complemento de nivel "Hola a todos" que expone los datos de SharePoint en la aplicación web remota del complemento. En este artículo, vamos a empezar con una solución de Complemento de SharePoint que ya está creada. Incluye una aplicación de ASP.NET Web Forms y una base de datos de SQL Azure. Hemos creado esto porque esta serie de artículos está diseñada para centrarse únicamente en los Complementos de SharePoint. Agregaremos más funciones e integración de SharePoint al complemento con cada artículo de esta serie.





## Introducción al complemento de base

No vamos a enseñarle programación ASP.NET o T-SQL en esta serie, pero necesita saber un poco sobre los componentes remotos del complemento para poder empezar a integrarlo en SharePoint. El escenario del complemento prevé una cadena de tiendas minoristas y cada tienda tiene un sitio web de equipo en la suscripción a SharePoint Online de la empresa principal. Cuando una tienda instala el complemento en su sitio de grupo, el complemento les permite integrar sus datos de SharePoint y experimentar con los datos en la base de datos de la empresa principal. Cada instancia del complemento tiene su propio arrendamiento en la base de datos corporativa, y los usuarios solo pueden interactuar con datos corporativos, que están asociados a sus tiendas.




### Comenzar por crear sitios de equipo para dos tiendas de la cadena


1. Abra la página principal de su SharePoint Online y haga clic en el vínculo **Contenido del sitio** en Inicio rápido. En la página **Contenido del sitio**, desplácese hacia abajo hasta ver el vínculo **Nuevo subsitio** y haga clic en él.


2. En la página **Nuevo sitio de SharePoint**, complete el formulario de un sitio de equipo con los valores de la siguiente captura de pantalla.

!\[Formulario para crear un nuevo subsitio de SharePoint con "Fabrikam Hong Kong Store" en el cuadro de texto de título y "hongkong" en el de dirección URL.](images/7639578e-3d3f-40cf-8f53-355bb19c539b.png)





3. Deje las demás opciones en sus valores predeterminados y presione **Crear**.



### Explorar el complemento


1. Vaya a  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) y haga clic en el botón **Descargar ZIP** para descargar el repositorio en el escritorio. Descomprima el archivo ZIP.


2. Ejecute Visual Studio *como administrador*  y, a continuación, abra BeforeSharePointUI.sln. Existen tres proyectos diferentes en la solución:

  - **ChainStore**: el proyecto de Complemento de SharePoint.


  - **ChainStoreWeb**: la aplicación web remota.


  - **ChainCorporateDB**: la base de datos de SQL Azure.


3. Seleccione el proyecto **ChainStore** y, en la ventana **Propiedades**, establezca la propiedad **Dirección URL del sitio** en la dirección URL completa del sitio de equipo de Hong Kong:https://{SU_DOMINIO_SHAREPOINT}/hongkong/. Asegúrese de incluir el carácter "/" al final. Presione el botón **Guardar**.  *En algún momento de este proceso, se le pedirá que inicie sesión en su suscripción a SharePoint Online.* 


4. Haga clic con el botón derecho en el nodo de solución en la parte superior del **Explorador de soluciones** y seleccione **Establecer proyectos de inicio**.


5. Asegúrese de que los tres proyectos estén establecidos en **Iniciar** en la columna **Acción**.


6. Use la tecla F5 para implementar y ejecutar el complemento. Visual Studio hospeda la aplicación web remota en IIS Express y hospeda la base de datos SQL en SQL Express. También hace una instalación temporal del complemento en su sitio de SharePoint de prueba y ejecuta inmediatamente el complemento. Deberá conceder permisos al complemento antes de que se abra la página de inicio.


7. La página de inicio del complemento es similar a la siguiente captura de pantalla. En la parte superior aparece el nombre del sitio web de SharePoint donde está instalado el complemento. Se trata de un efecto secundario de algún código de ejemplo que Office Developer Tools para Visual Studio genera. Esto lo cambiaremos en un paso posterior. La página tiene áreas donde se pueden ver los datos de las tablas de bases de datos SQL corporativas **Inventario**, **Pedidos** y **Empleados**. Estas tablas están vacías inicialmente.

!\[Página de inicio del complemento Chain Store con áreas y botones etiquetados para ver el inventario, los pedidos y los empleados de la tienda.](images/9c836899-8f21-4e0f-9260-69b8171a303c.PNG)





8. Abra el vínculo **Formulario de pedido** situado en la parte inferior de la página para abrir un formulario de pedido. Escriba algunos valores en el formulario y presione **Realizar pedido**. La captura de pantalla siguiente muestra un ejemplo. Parecerá que no hay cambios, pero el código subyacente del botón pasa los valores a un procedimiento almacenado con parámetros en la base de datos SQL. El uso de procedimientos almacenados con parámetros protege la base de datos contra ataques de inyección SQL.

!\[Formulario de pedido con cuadros de texto para el proveedor, el producto y la cantidad, y con un botón con la etiqueta Place Order.](images/5ce3e68b-1bcf-4a24-9153-b9164af77a6b.PNG)





9. Use el botón Atrás del explorador para volver a la página de inicio y, a continuación, presione el botón **Mostrar pedidos**. Se actualizará la página y el pedido aparecerá en una página similar a la siguiente captura de pantalla.

    La tabla tiene un campo **Inquilino** con la dirección URL de su sitio web de SharePoint de prueba como valor. Esto no hace referencia a la suscripción a SharePoint Online que a veces se denomina arrendamiento. En su lugar, cada instancia de un complemento es un inquilino distinto en la base de datos corporativa. Como no puede instalarse en una web de host de SharePoint específica más de una instancia de un complemento, la dirección URL de la web de host puede usarse como discriminador de inquilinos en la base de datos. (Para ver un repaso sobre la distinción entre web de host y web de complemento, consulte [Complementos de SharePoint](sharepoint-add-ins.md)). Todos los procedimientos almacenados en el complemento incluyen el valor del discriminador al escribir o leer desde la base de datos. Esto asegura que cuando un usuario presiona el botón **Mostrar pedidos** (o **Mostrar empleados** o **Mostrar inventario**), solo los datos que están asociados con la tienda del usuario se recuperan de la base de datos. Este diseño también garantiza que los usuarios puedan solo realizar pedidos y agregar empleados para su propia tienda.

    La aplicación web remota obtiene la dirección URL de la web de host a partir de un parámetro de cadena de consulta que SharePoint agrega a la dirección URL de la página de inicio cuando se inicia el complemento. Puesto que se usa SSL, esta cadena de consulta se cifra mientras viaja por Internet a la aplicación web remota. 

!\[Sección de la página de inicio de pedidos con un pedido que aparece en una tabla HTML. Tiene columnas para ID, Tenant, Supplier, Product y Quantity.](images/6a82c6eb-4a46-4a97-94f9-c1f4a7605d1f.PNG)





10. Para terminar la sesión de depuración, cierre la ventana del explorador o detenga la depuración en Visual Studio. Cada vez que presione F5, Visual Studio retirará la versión anterior del complemento e instalará la más reciente.


11. De forma predeterminada, el complemento permanece instalado en la web de host de SharePoint entre sesiones de depuración en Visual Studio. Para ver cómo los usuarios finales la ejecutarían después de instalarla, abra el sitio web de SharePoint de Hong Kong de Fabrikam en el explorador y vaya a la página **Contenido del sitio**. Verá el icono del complemento tal como se muestra en la siguiente captura de pantalla:

!\[Icono de inicio del complemento Chain Store en la página de contenido del sitio con el icono y el nombre del complemento.](images/f16e6360-c511-431f-b2c5-9e5541e64d4b.PNG)



    > **NOTA**
      > Si hace clic en el icono, no se abrirá la página de inicio porque Visual Studio cierra la sesión de IIS Express cuando detiene la depuración. 

## Configurar Visual Studio para recompilar la base de datos corporativa con cada sesión de depuración
<a name="Rebuild"> </a>

 De forma predeterminada, Visual Studio *no*  recompilará la base de datos de SQL Express. Por lo tanto, los pedidos y demás elementos que agrega a la base de datos en una sesión de depuración están todavía en la base de datos en sesiones posteriores. Es más fácil empezar con una base de datos vacía cada vez que se presiona F5, de modo que seguiremos estos pasos:




1. Haga clic con el botón derecho en el proyecto **ChainCorporateDB** y seleccione **Propiedades**.


2. Abra la ficha **Depurar** y habilite la opción **Volver a crear siempre la base de datos**.



## Dar a la aplicación web remota la apariencia de SharePoint
<a name="Rebuild"> </a>

En algunos escenarios, necesita que sus páginas remotas tengan su propia marca, pero en la mayoría de los casos deben imitar la interfaz de usuario de SharePoint para que los usuarios sientan que aún están dentro de SharePoint.




### Agregar cromo de SharePoint y la barra superior a la página de inicio


1. En el **Explorador de soluciones**, vaya a **ChainStoreWeb | Páginas** y abra el archivo CorporateDataViewer.aspx. Esta es la página de inicio del complemento.


2. En la sección **head**, verá un script que carga un par de bibliotecas JavaScript. Agregue el siguiente script adicional debajo. Este script carga el archivo SP.UI.Controls.js que se encuentra en cada sitio web de SharePoint en la carpeta /_layouts/15/. Entre otras cosas, este archivo carga la biblioteca CSS de SharePoint.

 ```

<script type="text/javascript">
    var hostweburl;

    // Load the SharePoint resources.
    $(document).ready(function () {

        // Get the URI decoded add-in web URL.
        hostweburl =
            decodeURIComponent(
                getQueryStringParameter("SPHostUrl")
        );

        // The SharePoint js files URL are in the form:
        // web_url/_layouts/15/resource.js
        var scriptbase = hostweburl + "/_layouts/15/";

        // Load the js file and continue to the 
        // success handler.
        $.getScript(scriptbase + "SP.UI.Controls.js")
    });

    // Function to retrieve a query string value.
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
 ```

3. En la parte superior de la sección del cuerpo de la página, agregue el marcado siguiente. Se insertará la barra superior de SharePoint, denominada control de cromo, en la página. Los detalles de este marcado serán más claros cuando probemos el complemento revisado más adelante en este artículo. (La cadena "app" aparece en algunos de los nombres de propiedad porque los complementos se solían llamar "apps").

 ```

<!-- Chrome control placeholder. Options are declared inline.  -->
<div 
    id="chrome_ctrl_container"
    data-ms-control="SP.UI.Controls.Navigation"
    data-ms-options=
        '{
            "appHelpPageUrl" : "Help.aspx",
            "appIconUrl" : "/Images/AppIcon.png",
            "appTitle" : "Chain Store",
            "settingsLinks" : [
                {
                    "linkUrl" : "Account.aspx",
                    "displayName" : "Account settings"
                },
                {
                    "linkUrl" : "Contact.aspx",
                    "displayName" : "Contact us"
                }
            ]
         }'>
</div>
 ```

4. Los encabezados **H1** y el hipervínculo que hay en el cuerpo de la página usarán automáticamente estilos definidos en la biblioteca CSS de SharePoint. Por lo tanto, no es necesario cambiarlos. Para ilustrar cómo puede usar los estilos de SharePoint, establezca los encabezados de columna en los tres controles **GridView** para el estilo "todas mayúsculas" de SharePoint agregando el atributo **HeaderStyle-CssClass** a cada uno de los controles y estableciendo su valor en " `ms-uppercase`". A continuación se muestra un ejemplo. Realice el mismo cambio en los tres controles **GridView**.

 ```XML

<asp:GridView ID="ordersGridView" runat="server" CellPadding="5" GridLines="None"
HeaderStyle-CssClass="ms-uppercase" />
 ```

5. El control de cromo usa el icono del complemento, por lo que tenemos una segunda copia del archivo de icono en el servidor web remoto. En el **Explorador de soluciones**, haga clic en el archivo AppIcon.png del proyecto **ChainStore** y elija **Copiar**. 


6. Haga clic con el botón derecho en la carpeta **Imágenes** del proyecto **ChainStoreWeb** y elija **Pegar**.


7. Abra el archivo CorporateDataViewer.aspx.cs.


8. La clase  `CorporateDataViewer` declara un miembro privado de tipo **SharePointContext**. Esta clase se define en el archivo SharePointContext.cs que Office Developer Tools para Visual Studio generó cuando se creó el proyecto. Se puede considerar como algo parecido a la clase **HttpContextBase** de ASP.NET, pero con información contextual de SharePoint, como la dirección URL de la web de host, agregada allí.

    En el método **Page_Load**, hay una instrucción **using** que escribe el nombre de la web de host de SharePoint en la página de inicio remota. Este es el código de ejemplo, elimine toda la instrucción **using** (pero deje la línea que inicializa la variable `spContext`). El método ahora debería ser similar al siguiente.



 ```cs

protected void Page_Load(object sender, EventArgs e)
{
    spContext = SharePointContextProvider.Current.GetSharePointContext(Context);
}
 ```

9. Hay otros cuatro archivos ASP.NET que necesitan la interfaz de usuario de SharePoint: 

  - Account.aspx


  - Contact.aspx


  - Help.aspx


  - OrderForm.aspx



    > **NOTA**
      > El último archivo aspx en el proyecto, EmployeeAdder.aspx, nunca se representa en verdad, de modo que no cambie su interfaz de usuario. Obtendrá más información en un artículo posterior de esta serie. 

    Pero no queremos el control de cromo en estas páginas. Queremos el acceso a la biblioteca CSS de SharePoint. Para cada uno de estos cuatro, agregue el marcado siguiente al elemento **head**.



 ```XML

<link type="text/css" rel="stylesheet"
href="<%= spContext.SPHostUrl.ToString() + "_layouts/15/defaultcss.ashx" %>" />
 ```

10. Este paso y el siguiente ya se realizaron para la página Formulario de pedido y la página Cuenta, por lo que solo se aplican a las páginas Contacto y Ayuda. Para obtener el objeto  `spContext` en cada una de las páginas, abra los archivos de código subyacente *. aspx.cs de las tres páginas aspx. En cada uno de ellos, agregue el siguiente miembro a la clase **Page**.

 ```cs

protected SharePointContext spContext;
 ```

11. Reemplace el método **Page_Load** por la siguiente versión. Se obtiene el objeto de la memoria caché de la sesión. Se almacenó en la memoria caché cuando se creó inicialmente con el método **Page_Load** de la página de inicio del complemento.

 ```cs
  protected void Page_Load(object sender, EventArgs e)
{
    spContext = Session["SPContext"] as SharePointContext;
}
 ```

12. Abra la página OrderForm.aspx. En el elemento superior **Label**, reemplace el elemento **<b>** en la frase **Realizar pedido** por etiquetas span que hagan referencia a la clase CSS `ms-accentText`. Todo el control **Label** debería ser similar al siguiente cuando termine.

 ```XML

<asp:Label ID="lblOrderPrompt" runat="server"
         Text="Enter a supplier, product, and quantity; and then press <span class='ms-accentText'>Place Order</span>.">
</asp:Label>
 ```


## Ejecutar el complemento y probar la nueva interfaz de usuario de SharePoint
<a name="Rebuild"> </a>






1. Use la tecla F5 para implementar y ejecutar el complemento. Visual Studio hospeda la aplicación web remota en IIS Express y hospeda la base de datos SQL en un SQL Express. También hace una instalación temporal del complemento en su sitio de SharePoint de prueba y ejecuta inmediatamente el complemento. Deberá conceder permisos al complemento antes de que se abra la página de inicio.


2. Ahora, cuando se abre la página de inicio del complemento, se parece a una página de SharePoint. Haga clic en el vínculo **Formulario de pedido**. Ahora se parece también a un formulario de SharePoint.

!\[Formulario de pedido una vez más. La fuente ahora es Segoe y la frase "Place Order" está resaltada en azul.](images/a35fd1af-7194-4c99-b298-bac97025b981.PNG)





3. Cree un pedido y presione **Realizar pedido**.


4.  Use el botón Atrás del explorador para volver a la página de inicio del complemento y, a continuación, presione el botón **Mostrar pedidos**. La página ahora debe parecerse a la siguiente. Tenga en cuenta que los encabezados de columna son ahora todas mayúsculas. 

!\[Página de inicio con el control de cromo en la parte superior. Todo el texto y los controles tienen estilos de SharePoint.](images/e56e4b80-1f1c-4a83-9ac0-3005ceea7c3a.PNG)





5. Presione el icono **?** en el extremo del control de cromo. Se abre una página de ayuda simple. Haga clic en el botón Atrás del explorador.


6. Presione el icono de engranaje en el control de cromo. Se abrirá un menú con vínculos para una página Cuenta y Contacto. Abra la página Cuenta y use el botón Atrás del explorador para volver a la página de inicio. Haga lo mismo con la página Contacto.


7. Haga clic en el botón **Volver al sitio** del control de cromo. Se abre la página principal de la web de host, el sitio de grupo de tienda de Hong Kong.


8. Haga clic en el icono de engranaje en la barra superior y, a continuación, elija **Cambiar el aspecto**.


9. Siga las instrucciones para cambiar el sitio a uno de los "aspectos" alternativos. 


10. Vaya a la página **Contenido del sitio** y ejecute la aplicación Chain Store desde el icono correspondiente. Las páginas personalizadas adoptaron el aspecto elegido. Las capturas de pantalla siguientes muestran cómo aparecen en el aspecto compuesto **Naturaleza**.

!\[Página de inicio del complemento y formulario de pedido con los colores verdosos de la apariencia Naturaleza compuesta.](images/25ba5677-f530-432a-9980-64b5eb33119c.png)





11. Cambie nuevamente el aspecto del sitio al predeterminado, que se denomina **Office**.


12. Para terminar la sesión de depuración, cierre la ventana del explorador o detenga la depuración en Visual Studio. Cada vez que presione F5, Visual Studio retirará la versión anterior del complemento e instalará la más reciente.


13. Trabajará con este complemento y con la solución de Visual Studio en otros artículos, y se considera recomendable retirar el complemento una última vez cuando acabe de trabajar en él durante un tiempo. En el proyecto, haga clic con el botón derecho en el **Explorador de soluciones** y elija **Retirar**.



## 
<a name="Nextsteps"> </a>

 El complemento ahora es similar a SharePoint, pero aún es tan solo una aplicación web que realmente no se integra con SharePoint más allá de que se ejecuta desde un icono de SharePoint. En el siguiente artículo vamos a agregar un comando personalizado que se inicia desde un botón personalizado de la cinta de opciones: [Incluir un botón personalizado en el complemento hospedado por el proveedor](include-a-custom-button-in-the-provider-hosted-add-in.md).




