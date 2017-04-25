---
title: Crear un complemento hospedado por el proveedor que incluya un tipo de contenido y una lista de SharePoint personalizados
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# Crear un complemento hospedado por el proveedor que incluya un tipo de contenido y una lista de SharePoint personalizados
Cree un Complemento de SharePoint que combine una aplicación web hospedada en la nube con plantillas de lista, instancias de lista y tipos de contenido personalizados hospedados en SharePoint mediante Office Developer Tools para Visual Studio 2012. Aprenda a interactuar con webs de complementos de SharePoint 2013 mediante el servicio web REST/OData y a implementar OAuth en un Complemento de SharePoint.
La mayoría de las aplicaciones clásicas de SharePoint como los tipos de contenido personalizados, las definiciones de lista personalizadas y los flujos de trabajo, se pueden incluir en un Complemento de SharePoint hospedado en la nube. El sencillo ejemplo que presentamos en este artículo incluye los elementos siguientes:





- Una web de complemento con

  - algunas columnas de sitio personalizadas


  - un tipo de contenido personalizado que usa las columnas personalizadas


  - una plantilla de lista personalizada que usa el tipo de contenido personalizado


  - una instancia de lista basada en la definición de lista personalizada


- Una aplicación web ASP.NET que lee datos de la instancia de lista



## Requisitos previos para crear este complemento de SharePoint
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/es-es/download/details.aspx?id=30682) o posterior.


-  [Office Developer Tools](https://msdn.microsoft.com/es-es/office/aa905340.aspx)


- Una instalación de SharePoint 2013 para realizar pruebas y depurar

  - Puede ser en el mismo equipo que el equipo de desarrollo, o bien puede desarrollar mediante una instalación de SharePoint 2013 remota. Si trabaja con una instalación remota, deberá instalar el modelo de objetos de cliente redistribuible en la instalación de destino. Está disponible como un paquete redistribuible en el Centro de descarga de Microsoft. Busque "SDK de componentes de cliente de SharePoint Server 2013" o "SDK de componentes de cliente de SharePoint Online".


  - El sitio web de prueba de SharePoint debe crearse desde la definición de sitio del **Sitio para desarrolladores** (que puede crear en la Administración central).


  - La aplicación web remota se comunica con la web de complemento mediante JavaScript y la  [biblioteca transversal de dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md) u [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md). Si se usa OAuth, tal como ocurre en el ejemplo de este artículo, la instalación de SharePoint 2013 deberá configurarse para usar OAuth.



> **NOTA**
> En este enlace encontrará información sobre cómo configurar un entorno de desarrollo que se ajuste a sus necesidades:  [Empezar a crear aplicaciones para Office y SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 





### Conceptos básicos para crear un complemento

Antes de empezar a crear complementos, debe tener un conocimiento básico de qué son los Complementos de SharePoint y qué diferencia hay entre Complementos de SharePoint hospedados en SharePoint y en la nube. Encontrará información en los artículos de la tabla 1.




**Tabla 1. Conceptos básicos para crear un complemento**


|**Título del artículo**|**Descripción**|
|:-----|:-----|
| [Complementos de SharePoint](sharepoint-add-ins.md) <br/> |Obtenga información sobre el nuevo modelo de complementos de SharePoint 2013, que permite crear complementos, que son pequeñas soluciones fáciles de usar para usuarios finales.  <br/> |
| [Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |Obtenga información sobre aspectos de la arquitectura de los Complementos de SharePoint y el modelo para aplicaciones para SharePoint, como las opciones de hospedaje de complementos, las opciones de la interfaz de usuario (IU), el sistema de implementación, el sistema de seguridad y el ciclo de vida.  <br/> |
| [Elegir patrones para desarrollar y hospedar un complemento para SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Obtenga información sobre las diversas formas de hospedaje de los Complementos de SharePoint.  <br/> |
 

## Desarrollo del complemento de SharePoint
<a name="Develop"> </a>

En los procedimientos presentados en esta sección, se crea un Complemento de SharePoint que incluye una web de complemento con componentes de SharePoint y una aplicación web remota en el equipo de desarrollo.




### Para configurar la solución Visual Studio 2012 y sus elementos


1. En Visual Studio 2012, cree un proyecto de **Complemento de SharePoint 2013** desde el nodo **Office SharePoint | Complementos** (bajo **C#** o **Visual Basic**) en el árbol de plantillas del asistente para **Nuevo proyecto**. Elija la opción de hospedaje **Hospedado por el proveedor**. En el ejemplo que presentamos en este artículo, se usa C# como lenguaje y el nombre del proyecto es CineLocal.


2. Elija **Finalizar** en el asistente.


3. Abra el archivo AppManifest.xml en el diseñador de manifiesto. El elemento **Title** tiene el nombre del proyecto como valor predeterminado. Sustitúyalo por otro que sea más atractivo, ya que se trata del nombre del complemento que los usuarios verán en la IU.


4. Especifique un **Name** para el complemento. Se trata de un nombre interno que solo debe contener caracteres ASCII y que no admite espacios; por ejemplo,CineLocal.


5. Abra el archivo Web.config en el proyecto de aplicación web y agregue el elemento  `<customErrors mode="Off"/>` al elemento **system.web**.


6. Compruebe que el proyecto de aplicación web contiene las referencias a los ensamblados siguientes. (Si su edición de Visual Studio 2012 no agregó automáticamente las referencias, agréguelas ahora).

  - **Microsoft.IdentityModel.dll** Este ensamblado está instalado en la memoria caché de ensamblados global con Windows Identity Foundation (WIF). Dado que se trata de un ensamblado de .NET Framework 3.5, de forma predeterminada se saca del filtro del nodo **Marco** del cuadro de diálogo **Agregar referencia**. Puede agregar una referencia a este ensamblado dirigiéndose directamente al directorio  `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` del equipo de desarrollo.


  - **Microsoft.IdentityModel.Extensions.dll** Puede agregar una referencia a este ensamblado examinando directamente la carpeta `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` del equipo de desarrollo.


  - **System.IdentityModel.dll** Este ensamblado forma parte de .NET Framework 4 y aparece en el nodo **Ensamblados | Marco** del cuadro de diálogo **Agregar referencia**.


7. Si la aplicación web remota obtiene acceso a información de la web de host, así como de la web de complemento, debe agregar un elemento **AppPermissionRequests**, con uno o varios elementos **AppPermissionRequest** secundarios, al archivo AppManifest.xml. (La aplicación web del ejemplo siguiente del artículo obtiene acceso únicamente a la web de complemento. Automáticamente las entidades de seguridad del complemento tienen todos los permisos necesarios sobre la web de complemento, por lo que el archivo AppManifest.xml del ejemplo no tiene un elemento **AppPermissionRequests**). Para más información sobre las solicitudes de permiso de complementos y cómo agregarlas, vea  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).



### Para agregar los componentes de SharePoint


1. Puede agregar componentes de SharePoint a un complemento exactamente como los agregaría a una solución de granja de servidores clásica. No obstante, no puede incluir cualquier tipo de componente de SharePoint en un Complemento de SharePoint. Los objetivos de dichos componentes se logran de otras formas en los Complementos de SharePoint. Para obtener información detallada sobre los tipos de componentes de SharePoint que se pueden incluir en un Complemento de SharePoint y cómo incluirlos en un proyecto, vea  [Tipos de componentes de SharePoint que se pueden encontrar en un complemento para SharePoint](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).

    Siguiendo con este ejemplo, ejecute los procedimientos siguientes. Se proporcionarán ejemplos de cómo usar Visual Studio 2012 para agregar columnas personalizadas, tipos de contenido, plantillas de lista e instancias de lista a un Complemento de SharePoint.

### Para crear los tipos de columna personalizados


1. En **Explorador de soluciones**, agregue un elemento **Columna del sitio** de SharePoint al proyecto del Complemento de SharePoint con el nombreActor.


2. En el archivo elements.xml de la nueva columna de sitio, edite el elemento **Field** para que tenga los atributos y los valores que se muestran en el ejemplo siguiente, salvo que no tiene que cambiar el GUID para el atributo **ID** del valor Visual Studio 2012 generado para el mismo. No se olvide de las llaves "{}".

 ```

<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
 ```

3. Agregue otra **Columna de sitio** al proyecto llamadoEstadoCasting.


4. En el archivo elements.xml de la columna nueva de sitio, edite el elemento **Field** para que tenga los atributos y los valores que se muestran en el ejemplo siguiente, salvo que no debe cambiar el GUID del atributo **ID** del valor Visual Studio 2012 generado para él.

 ```

<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
 ```

5. Como este es un campo de elección, debe especificar las posibles opciones, el orden en que deben aparecer en la lista desplegable cuando un usuario efectúe la elección, y la opción predeterminada. Agregue el marcado secundario siguiente al elemento **Field**.

 ```

<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
 ```


### Para crear el tipo de contenido personalizado


1. En **Explorador de soluciones**, agregue un elemento **Tipo de contenido** de SharePoint al proyecto del Complemento de SharePoint con el nombrePapelActuación. Cuando el asistente le solicite que seleccione el tipo de contenido básico, elija **Elemento** y después elija **Finalizar**.


2. Si el diseñador de tipo de contenido no se abre automáticamente, elija el tipo de contenido **PapelActuación** en **Explorador de soluciones** para abrirlo.


3. Abra la pestaña **Tipo de contenido** en el diseñador y rellene los cuadros de texto tal como se indica a continuación:

  - **Nombre de tipo de contenido**: PapelActuación


  - **Descripción**: tiene un papel en una obra o película.


  - **Nombre de grupo**: Cine y películas


4. Verifique que  *ninguna*  de las casillas de la pestaña esté seleccionada. Es posible que, de forma predeterminada, la casilla **Hereda las columnas del tipo de contenido primario** esté seleccionada. *Desactívela.* 


5. Abra la pestaña **Columnas** del diseñador.


6. Use la cuadrícula para agregar las dos columnas de sitio al tipo de contenido. Se muestran en la lista desplegable por su nombre de visualización: **Actor/Actriz** y **EstadoCasting**. (Si no están en la lista, es posible que no haya guardado el proyecto desde que agregó las columnas de sitio personalizadas. Elija **Guardar todo**).


7. Guarde el archivo y cierre el diseñador.


8. En el paso siguiente tiene que trabajar directamente el XML puro para el tipo de contenido. Por lo tanto, en **Explorador de soluciones**, elija el archivo elements.xml secundario del tipo de contenido **PapelActuación**.


9. El archivo ya contiene elementos **FieldRef** para las dos columnas que agregó. Agregue elementos **FieldRef** para dos columnas SharePoint 2013 integradas como pares de las dos que ya están incluidas. A continuación, indicamos el marcador de los elementos. *Debe usar estos mismos GUID para el atributo ID porque son tipos de campo integrados con ID fijos.*  Agréguelos *por encima*  de los dos elementos **FieldRef** para las columnas de sitio personalizadas.

 ```

<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
 ```


    Tenga en cuenta que hemos dado a estos campos un nombre de visualización personalizado: **Personaje**, en el sentido de un personaje de una obra de teatro o una película.



### Para crear una plantilla de lista personalizada y una instancia de lista


1. Agregue un elemento **Lista** de SharePoint al proyecto del Complemento de SharePoint con el nombrePersonajesDelEspectáculo. En la página **Elegir configuración de la lista** del **Asistente para la personalización de SharePoint**, deje el nombre de visualización de la lista en el valor predeterminado **PersonajesDelEspectáculo**, elija el botón de opción **Crear una lista personalizable basada en** y seleccione **Predeterminado (en blanco)** en la lista desplegable. Después, elija **Finalizar**.


2. Cuando complete el asistente, se creará una plantilla de lista **PersonajesDelEspectáculo** con una instancia de lista secundaria llamada **PersonajesEnInstanciaEspectáculo**. Puede que, de forma predeterminada, se haya abierto un diseñador de listas. Se usará en un paso posterior.


3. Abra el archivo elements.xml secundario de la plantilla de lista **PersonajesDelEspectáculo** ( *no*  el archivo elements.xml secundario de **PersonajesEnInstanciaEspectáculo**).


4. Agregue espacios al atributo **DisplayName** para que sea más comprensible:"Personajes del espectáculo".


5. Configure el atributo **Description** en"Los personajes de una obra de teatro o una película".


6. Deje los demás atributos restantes en su valor predeterminado, guarde el archivo y ciérrelo.


7. Si el diseñador de listas no está abierto, elija el nodo **PersonajesEnEspectáculo** en el **Explorador de soluciones**.


8. Abra la pestaña **Columnas** en el diseñador y después elija el botón **Tipos de contenido**.


9. En el cuadro de diálogo **Configuración de tipo de contenido**, agregue el tipo de contenido **PapelActuación**.


10. Elija el tipo de contenido **PapelActuación** en la lista de tipos y seleccione el botón **Establecer como predeterminado**.

    Elija el tipo de contenido **Elemento**, haga clic con el botón secundario en la pequeña flecha que aparece a la izquierda del nombre de tipo de contenido y después elija **Eliminar**.


11. Repita el paso anterior para el tipo de contenido **Carpeta**, para que **PapelActuación** sea el único tipo de contenido de la lista. Seleccione **Aceptar** para cerrar el cuadro de diálogo.


12. Ahora la lista de columnas contiene tres columnas. Seleccione **Título**, haga clic con el botón secundario en la pequeña flecha que aparece a la izquierda del nombre de tipo de contenido y después elija **Eliminar**. Ahora solo deben visualizarse dos columnas, **Actor/Actriz** y **Estado Casting**.


13. Abra la pestaña **Lista** del diseñador. Esta pestaña se usa para configurar determinados valores de la lista *instancia*  , no la lista *plantilla*  .


14. Cambie los valores de esta pestaña por los siguientes:

  - **Título**: Personajes de Hamlet


  - **Dirección URL de lista**: Listas/PersonajesDeHamlet


  - **Descripción**: Los personajes de Hamlet e información de casting.



    Deje las casillas en su estado predeterminado, guarde el archivo y cierre el diseñador.


15. Es posible que en **Explorador de soluciones** esté el antiguo nombre de la instancia de lista. Si es así, abra el menú contextual de **CaracteresDeInstanciaEspectáculo**, elija **Cambiar nombre** y cambie el nombre porPersonajesDeHamlet.


16. Abra el archivo schema.xml.


17. Es probable que haya dos elementos **ContentType** en el archivo, uno que tenga el valor de atributo **Name**PapelActuación y otro denominado **TipoContenidoCamposLista**. Solo pertenece el valor de atributo llamado PapelActuación, por lo que deberá eliminar todos los demás elementos **ContentType**.

    > **NOTA**
      > No puede haber saltos de línea entre los elementos **ContentType**, en cuyo caso al principio puede parecer que solo hay uno. Desplácese a la derecha y compruebe atentamente la existencia de otros. 
18. El elemento **Fields** debería tener dos elementos **Field** (que se encuentran en una única línea si no hay un salto de línea entre ellos). Uno debe duplicar exactamente el elemento **Field** del archivo elements.xml de la columna de sitio **Actor** y el otro debe duplicar exactamente el elemento **Field** del archivo elements.xml de la columna de sitio **EstadoCasting**. Si no coinciden exactamente, incluidos los elementos secundarios (como los elementos **CHOICES** y **MAPPINGS**), copie el elemento **Field** del archivo elements.xml de la columna de sitio y péguelo en lugar del elemento **Field** que no corresponde exactamente en el archivo schema.xml.


19. En el archivo schema.xml, en el elemento **View** cuyo valor BaseViewID es "0", sustituya el elemento **ViewFields** existente con el marcador siguiente. (Use exactamente este GUID para el **FieldRef** llamado `LinkTitle`).

 ```

<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
 ```

20. Sustituya los dos valores de atributo de ID que faltan por los GUID en los archivos elements.xml de la columna de sitio respectivos. No se olvide de las llaves "{}".


21. En el archivo schema.xml, en el elemento **View** cuyo valor BaseViewID es "1", sustituya el elemento **ViewFields** existente por el marcador siguiente. (Use exactamente este GUID para el **FieldRef** llamado `LinkTitle`).

 ```

<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
 ```

22. Copie los dos elementos **FieldRef** para `Actor` y `CastingStatus` que agregó en la vista anterior en este elemento **ViewFields** como elementos del mismo nivel del `LinkTitle` **FieldRef**.


23. Guarde y cierre el archivo schema.xml.


24. Abra el archivo elements.xml que es un elemento secundario de la instancia de lista **PersonajesDeHamlet**.


25. Rellene la lista con algunos datos iniciales agregando el marcador siguiente como elemento secundario del elemento **ListInstance**.

 ```

<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
 ```

2. En **Explorador de soluciones**, elija **Feature1** para abrir el diseñador de características. En el diseñador, configure el **Título** enComponentes de datos de teatros y películas y configure la **Descripción** enColumnas de sitio, tipos de contenido e instancias de lista para datos sobre teatros y películas.. Guarde el archivo y cierre el diseñador.


3. Si no se ha cambiado el nombre de **Feature1** en **Explorador de soluciones**, abra su menú contextual, elija **Cambiar nombre** y cámbielo porComponentesDeDatosDeTeatroYPelículas.



### Para codificar el proyecto de aplicación web remota


- Desarrolle la aplicación web tal como lo haría con cualquier otra aplicación web para su pila de plataforma preferida. En el caso de una pila de Microsoft, puede usar el servicio web REST/OData o uno de los modelos de objeto cliente de SharePoint 2013. En el caso de pilas que no sean de Microsoft, puede usar los extremos REST/OData de SharePoint 2013 para ejecutar operaciones de creación, lectura, actualización y eliminación (CRUD) de datos de la web de complemento.

    > **NOTA**
      > Cuando agregue una referencia de un ensamblado a su proyecto de aplicación web en Visual Studio, configure la propiedad **Copia local** del ensamblado como **True**, a menos que sepa que el ensamblado está ya instalado en el servidor web o que pueda asegurar que está instalado antes de implementar su complemento. .NET Framework está instalado en los roles web de Microsoft Azure y Sitios web de Azure. Sin embargo, los ensamblados cliente de SharePoint 2013 y las diversas fundaciones y extensiones de código administradas por Microsoft no están instaladas. Office Developer Tools para Visual Studio 2012 agrega automáticamente referencias a algunos ensamblados que se usan comúnmente en Complementos de SharePoint y configura la propiedad **Copia local**. 

    Para el ejemplo que hemos propuesto, desarrolle una aplicación web ASP.NET. Ejecute los pasos siguientes.

1. Abra el archivo Default.aspx y sustituya el elemento del cuerpo del archivo por el marcador siguiente. El marcador agrega un botón **Obtener reparto** que, cuando se elige, lee la lista **Personajes de Hamlet** que está en la web de complemento y presenta sus datos en un control [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) que solo aparece después de presionar el botón.

 ```HTML

<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
 ```

2. Abra el archivo Default.aspx.cs y agréguele las instrucciones **using** siguientes.

 ```cs

using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
 ```


    La última de estas instrucciones se refiere al espacio de nombres que se declara en el archivo TokenHelper.cs.


3. Agregue los campos siguientes a la clase **Default**.

 ```cs

SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
 ```

4. Sustituya el método **Page_Load** por el código siguiente que usa la clase **TokenHelper** para obtener tokens del servidor de tokens seguro que cumple OAuth. A continuación, el token de acceso se almacena en la propiedad [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) del botón para que el controlador de eventos de clic de botón lo recupere posteriormente.

 ```cs

protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;
    
        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
 ```

5. Agregue el controlador de eventos siguiente a la clase **Default**. El controlador empezará recuperando el token de acceso almacenado en la propiedad  [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) del botón.

 ```cs

protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
 ```

6. El controlador necesita volver a adquirir la dirección URL de la web de complemento en las devoluciones, por lo que deberá agregar el código siguiente.

 ```cs

if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
 ```

7. Agregue la línea siguiente que usa uno de los extremos REST/OData de SharePoint 2013 para obtener datos de lista. En este ejemplo, el código lee la lista **Personajes de Hamlet** que se implementa en la web del complemento. Las API de este servicio permiten que sea fácil seleccionar una lista en una única línea de código y especificar tres campos de la lista para devolverlos. Recuerde que en la dirección URL de OData debe usar los nombres internos de los campos (columnas) en vez de los nombres de visualización, por lo que el código usará `Title`,  `Actor` y `CastingStatus`, en vez de  `Character`,  `Actor/Actress` y `Casting Status.` Para más información sobre el servicio web REST/OData, vea [Usar operaciones de consulta de OData en solicitudes REST de SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md).

 ```cs

// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
 ```

8. Agregue el código siguiente que usa las clases  [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) y [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) del espacio de nombres [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) para construir la solicitud HTTP y los objetos de respuesta.

 ```cs

// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
 ```

9. Agregue el código siguiente para analizar el XML de respuesta con formato ATOM. Use las clases de nombre de espacio  [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) para analizar los datos que se devuelven y construir una [List<T>](http://msdn2.microsoft.com/ES-ES/library/6sh2ey19) de los elementos de la lista de SharePoint. (También podría usar las clases del espacio de nombres [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) ). Recuerde que en el XML que SharePoint devuelve, los elementos secundarios del elemento **entry** contienen metadatos sobre el elemento de lista. Los datos de fila reales de un elemento de lista de SharePoint se anidan dos capas por debajo, en el elemento **properties**. Por este motivo, el método de extensión  [Elements<T>](http://msdn2.microsoft.com/ES-ES/library/bb348465) se usa dos veces para filtrar los niveles superiores.

 ```cs

// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
 ```

10. Agregue la consulta LINQ siguiente para crear una colección  [IEnumerable<T>](http://msdn2.microsoft.com/ES-ES/library/9eekhta0) de un tipo anónimo que tiene justo las propiedades que necesita y ninguna más. Tenga en cuenta que, aunque el código debe referirse al campo de título del elemento por su nombre interno `Title`, el nombre de la propiedad en el tipo anónimo al que se asigna el valor, se puede llamar  `Character`. A consecuencia de esto, cuando la colección se enlaza a un control de cuadrícula, aparece el nombre **Personaje** más apropiado en la página.

 ```cs

var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

 ```

11. Finalice el controlador con el código siguiente para enlazar los datos a un control  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) de la página. Los encabezados de columna de la cuadrícula toman como valor predeterminado los nombres de propiedad del tipo anónimo: `Character`,  `Actor` y `CastingStatus`. El control  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) tiene propiedades que le permiten controlar los encabezados de columna de nombre y formato, por lo que puede tener **Actor/Actriz** y **Casting Status** para que coincidan con los encabezados de columna de SharePoint. Para mayor simplicidad, no se describen aquí estas técnicas. (Podría también usar un control [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) ).

 ```cs

GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

 ```

12. Guarde todos los archivos.



### Para probar y depurar el complemento de SharePoint


1. Para probar el Complemento de SharePoint y su aplicación web remota, elija la tecla F5 en Visual Studio 2012. La aplicación web se implementa en IIS Express en el localhost. El Complemento de SharePoint se instala en el sitio web de SharePoint de destino. (En el ejemplo de este documento, el complemento remoto  *no*  intenta interactuar con la web de *host*  y la entidad de seguridad del complemento tiene automáticamente permisos sobre la web de *complemento*  , por lo que *no*  se le solicitará que otorgue permisos). La página **Contenido del sitio** del sitio web de SharePoint de destino se abre y verá que el nuevo complemento figura en la lista.


2. Elija el Complemento de SharePoint y la aplicación web remota se abrirá en la página que especificó en el elemento **StartPage** en el archivo AppManifest.xml. Use la aplicación web según sea necesario para comprobar que funciona. En el presente ejemplo, solo tiene que presionar el botón. Al hacerlo, creará una cuadrícula y la rellenará con la lista **Personajes de Hamlet** de la web de complemento.



## Publicación del complemento de SharePoint
<a name="Publish"> </a>

Para publicar el Complemento de SharePoint, cargue el paquete del complemento en un catálogo de complementos corporativos o bien en la tienda de complementos de Office. Para más información, vea  [Publicación en la Tienda Office o el catálogo de complementos de una organización](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog) y [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md).




## Solución de problemas
<a name="Troubleshooting"> </a>

Si el complemento no funciona, es posible que se haya producido un error en el marcado CAML que bloquee la implementación de los componentes de SharePoint. Siga un procedimiento similar al que indicamos a continuación, que está basado en el ejemplo que estamos usando, para comprobar la implementación.




### Para probar el aprovisionamiento de la web de complemento


1. Abra la página **Configuración del sitio** de la web del host. En la sección **Administración de la colección de sitios**, seleccione el vínculo **Jerarquía de sitios**.


2. En la página **Jerarquía de sitios**, verá que el complemento se indica mediante la dirección URL. No lo inicie; copie la dirección URL y úsela en los pasos siguientes.


3. Vaya a  _URL_de_la_web_de_la_apl_/_layouts/15/ManageFeatures.aspx y, en la página **Características del sitio** que se abre, compruebe que **Componentes de datos de teatro y película** se encuentre en la lista alfabética de las características del Complemento de SharePoint y que su estado sea **Activo**.


4. Vaya a  _URL_de_la_web_de_la_apl_/_layouts/15/mngfield.aspx y, en la página **Columnas del sitio** que se abre, compruebe que figure un grupo **Teatro y películas** en la lista de las columnas de sitio y que contiene las columnas de sitio personalizadas nuevas, **Actor/Actriz** y **Estado del casting**.


5. Vaya a  _URL_de_la_web_de_la_apl_/_layouts/15/mngctype.aspx y, en la página **Tipos de contenido de sitio** que se abre, compruebe que figure un grupo **Teatro y películas** en la lista de columnas de sitio y que contiene el tipo de contenido **PapelActuación** nuevo.


6. Seleccione el vínculo con el tipo de contenido **PapelActuación**. En la página **Tipo de contenido del sitio** que se abre, compruebe que el tipo de contenido tenga los dos tipos de columna de sitio nuevos, **Actor/Actriz** y **Estado Casting**, y que el campo de título del elemento ha recibido el nombre de visualización personalizado : **Personaje**.


7. Vaya a  _URL_de_la_web_de_la_apl_/_layouts/15/mcontent.aspx y, en la página **Bibliotecas y listas del sitio** que se abre, compruebe que haya un vínculo **Personalizar los "Personajes de Hamlet"**.


8. Elija el vínculo **Personalizar los "Personajes de Hamlet"** y compruebe en la página de configuración de la lista que el único tipo de contenido de la lista es el tipo de contenido **PapelActuación** personalizado y que las dos columnas de sitio nuevas, **Actor/Actriz** y **Estado Casting** figuran en la sección **Columnas**. (La columna Título puede aparecer con su nombre interno **Título**, en vez del nombre de visualización **Personaje** que le dio).

    > **NOTA**
      > Si en la página no hay ninguna sección **Tipos de contenido**, habilite la administración de tipos de contenido. Haga clic en el vínculo **Configuración avanzada** y, en la página **Configuración avanzada**, habilite la administración de tipos de contenido y haga clic en **Aceptar**. Se le redirige a la página anterior, donde ahora aparece una lista de la sección **Tipos de contenido**. 
9. Cerca de la parte superior de la página se encuentra la **Dirección web** de la lista. Cópiela y péguela en la barra de direcciones del explorador y, después, desplácese a la lista. Compruebe que la lista tenga los elementos de ejemplo que creó. (Es posible que la columna Título aparezca con su nombre interno **Título**, en vez del nombre de visualización **Personaje** que le dio).



## Pasos siguientes
<a name="NextSteps"> </a>

En este artículo se demuestra cómo crear un sencillo Complemento de SharePoint híbrido con una aplicación web remota. En los pasos siguientes, piense en:




- Agregar una funcionalidad CRUD completa al complemento con extremos REST/OData o uno de los modelos de objeto cliente. Para más información, vea  [Usar operaciones de consulta de OData en solicitudes REST de SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md) y [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md).


- Localizar el Complemento de SharePoint para otras culturas. Para más información, vea  [Localizar complementos para SharePoint](localize-sharepoint-add-ins.md).


- Crear un complemento de Windows Phone que duplique las funciones de la aplicación web remota. Para más información, vea  [Creación de complementos móviles para SharePoint 2013](http://msdn.microsoft.com/es-es/library/office/jj163228.aspx).



## Recursos adicionales
<a name="SP15createcloud_bk_addlresources"> </a>


-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Empezar a crear complementos hospedados en SharePoint para SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)



