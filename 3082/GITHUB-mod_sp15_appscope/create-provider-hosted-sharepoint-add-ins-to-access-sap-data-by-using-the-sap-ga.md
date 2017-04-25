---
title: Crear complementos de SharePoint hospedados por el proveedor para acceder a datos de SAP mediante SAP Gateway for Microsoft
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# Crear complementos de SharePoint hospedados por el proveedor para acceder a datos de SAP mediante SAP Gateway for Microsoft
Aprenda a crear un Complemento de SharePoint que pueda acceder a los datos de SAP.
Puede crear un Complemento de SharePoint que lea y escriba datos de SAP y que, opcionalmente, lea y escriba datos de SharePoint con Puerta de enlace de SAP para Microsoft y la biblioteca de autenticación de Azure AD para .NET. En este artículo se proporcionan los procedimientos para diseñar el Complemento de SharePoint y obtener acceso autorizado a SAP. 





## Antes de empezar

Los siguientes son los requisitos previos de los procedimientos de este artículo:




- **Un Sitio para desarrolladores de Office 365** en un dominio de Office 365 que esté asociado con una suscripción a Microsoft Azure Active Directory. Vea [Configurar un entorno de desarrollo para complementos para SharePoint en Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) o [Crear un sitio para desarrolladores en una suscripción existente de Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md).


- **Visual Studio 2013 Update 2** o posterior, que puede obtener en [Visual Studio 2013](http://msdn.microsoft.com/es-es/library/dd831853.aspx).


- **Microsoft Office Developer Tools para Visual Studio**. La versión que se incluye en Visual Studio 2013 Update 2 o posterior.


- **Puerta de enlace de SAP para Microsoft** implementado y configurado en Microsoft Azure. Vea la documentación de [SAP Gateway for Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).


- **Una cuenta de organización en Microsoft Azure**. Vea  [Agregar manualmente de común acuerdo para la aplicación de la vista previa de las API de Office 365](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount).

    > **NOTA**
      > Inicie sesión en su cuenta de Office 365 (login.microsoftonline.com) para cambiar la contraseña temporal después de crear la cuenta. 
- **Un extremo OData de SAP** que incluya datos de muestra. Vea la documentación de [SAP Gateway for Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).


- **Estar mínimamente familiarizado con Azure AD.** Vea [Introducción a Azure AD](http://msdn.microsoft.com/es-es/library/azure/dn655157.aspx).


- **Estar mínimamente familiarizado con la creación de Complementos de SharePoint.** Vea [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md).


- **Estar mínimamente familiarizado con OAuth 2.0 en Azure AD**. Vea  [OAuth 2.0 en Azure AD](http://msdn.microsoft.com/es-es/library/azure/dn645545.aspx) y sus temas secundarios.


 **Ejemplo de código:** [SharePoint 2013: usar SAP Gateway para Microsoft en un complemento de SharePoint](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)




## Descripción de la autenticación y autorización en Puerta de enlace de SAP para Microsoft y SharePoint
<a name="AuthOverview"> </a>

OAuth 2.0 en Azure AD permite que las aplicaciones tengan acceso a varios recursos hospedados por Microsoft Azure, y Puerta de enlace de SAP para Microsoft es uno de ellos. Con OAuth 2.0, las aplicaciones, además de los usuarios, son entidades de seguridad. Las entidades de seguridad de la aplicación necesitan autenticación y autorización para los recursos protegidos, además de (y, a veces, en lugar de), para los usuarios. El proceso implica un "flujo" de OAuth en el que la aplicación, que puede ser un Complemento de SharePoint, obtiene un token de acceso (y un token de actualización) que aceptan todas las aplicaciones y servicios hospedados por Microsoft Azure que están configurados para usar Azure AD como un servidor de autorización de OAuth 2.0. El proceso es muy parecido al modo en que los componentes remotos de un Complemento de SharePoint hospedado por el proveedor obtiene autorización para SharePoint, tal como se describe en  [Crear complementos de SharePoint que usen autorización de baja confianza](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) y los artículos secundarios. Sin embargo, el sistema de autorización de ACS usa Servicio de control de acceso (ACS) de Microsoft Azure como el emisor de tokens de confianza, en lugar de Azure AD.




> **SUGERENCIA**
> Si su Complemento de SharePoint accede a SharePoint además de a Puerta de enlace de SAP para Microsoft, necesitará usar  *ambos*  sistemas: Azure AD para obtener un token de acceso a Puerta de enlace de SAP para Microsoft y el sistema de autorización ACS para obtener un token de acceso a SharePoint. Los tokens de los dos orígenes no son intercambiables. Para obtener más información, vea [También puede agregar acceso para SharePoint a la aplicación ASP.NET](#SharePoint). 




Para obtener una descripción detallada y un diagrama del flujo de OAuth que usa OAuth 2.0 en Azure AD, vea  [Flujo de concesión de códigos de autorización](http://msdn.microsoft.com/library/azure/dn645542.aspx). (Para ver una descripción similar, y un diagrama, del flujo para acceder a SharePoint, vea  [Pasos del flujo de tokens de contexto](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps)).




## Cree el Complemento de SharePoint
<a name="AuthOverview"> </a>


### Cree la solución de Visual Studio


1. Cree un proyecto de **Complemento de SharePoint** en Visual Studio con los siguientes pasos. (En el siguiente ejemplo de este artículo se da por hecho que usa C#; pero también puede iniciar un proyecto de Complemento de SharePoint en la sección **Visual Basic** de las plantillas de nuevo proyecto).

1. En el asistente **Nuevo complemento de SharePoint**, asigne un nombre al proyecto y haga clic en **Aceptar**. Para el siguiente ejemplo, use SAP2SharePoint.


2. Especifique la dirección URL del dominio de su Sitio para desarrolladores de Office 365 (incluida la barra inclinada final) como el sitio de depuración; por ejemplo, https://<O365_domain>.sharepoint.com/. Especifique **Hospedado por el proveedor** como tipo de complemento. Haga clic en **Siguiente**.


3. Elija un tipo de proyecto. Para nuestro ejemplo, elija **Aplicación de formularios Web Forms ASP.NET**. (También puede crear aplicaciones ASP.NET MVC que accedan a Puerta de enlace de SAP para Microsoft). Haga clic en **Siguiente**.


4. Elija Azure ACS como sistema de autenticación. (Su Complemento de SharePoint usará este sistema si accede a SharePoint. No usa este sistema cuando accede a Puerta de enlace de SAP para Microsoft). Haga clic en **Finalizar**.


2. Una vez creado el proyecto, se le pedirá que inicie sesión en la cuenta de Office 365. Use las credenciales de un administrador de cuenta; por ejemplo, Bob@<O365_domain>.onmicrosoft.com.


3. Hay dos proyectos en la solución de Visual Studio; el proyecto de Complemento de SharePoint correcto y un proyecto de formularios Web Forms ASP.NET. Agregue el paquete **Biblioteca de autenticación de Active Directory** (ADAL) al proyecto de ASP.NET siguiendo estos pasos:

1. Haga clic con el botón secundario en la carpeta **Referencias** en el proyecto de ASP.NET (llamado **SAP2SharePointWeb** en nuestro ejemplo) y seleccione **Administrar paquetes de NuGet**. 


2. En el cuadro de diálogo que se abre, seleccione **En línea** a la izquierda. EscribaMicrosoft.IdentityModel.Clients.ActiveDirectory en el cuadro de búsqueda.


3. Cuando la biblioteca ADAL aparezca en los resultados de búsqueda, haga clic en el botón **Instalar** situado junto a ella y acepte la licencia cuando se le pregunte.


4. Agregue el paquete Json.net al proyecto de ASP.NET siguiendo estos pasos:

1. Escriba Json.net en el cuadro de búsqueda. Si esto genera demasiados resultados, pruebe a buscarNewtonsoft.json.


2. Cuando aparezca Json.net en los resultados de búsqueda, haga clic en el botón **Instalar** situado junto a él.


5. Haga clic en **Cerrar**.



### Registre su aplicación web con Azure AD


1. Inicie sesión en el  [Portal de administración de Azure](https://manage.windowsazure.com) con su cuenta de administrador de Azure.

    > **NOTA**
      > Por motivos de seguridad, desaconsejamos usar una cuenta de administrador durante el desarrollo de complementos. 
2. Elija **Active Directory** a la izquierda.


3. Haga clic en su directorio. 


4. Elija **APLICACIONES** (en la barra de navegación superior).


5. Elija **Agregar** en la barra de herramientas, en la parte inferior de la pantalla.


6. En el cuadro de diálogo que se abre, elija **Agregar una aplicación que mi organización está desarrollando**.


7. En el cuadro de diálogo **AGREGAR APLICACIÓN**, dé un nombre a la aplicación. Para nuestro ejemplo, use ContosoAutomobileCollection. 


8. Elija **Aplicación web y/o API web** como tipo de aplicación y haga clic en el botón de flecha derecha.


9. En la segunda página del cuadro de diálogo, use la dirección URL de depuración de SSL para el proyecto de ASP.NET en la solución de Visual Studio como **URL DE INICIO DE SESIÓN**. Siga estos pasos para encontrar la dirección URL. ** *(Debe registrar el complemento inicialmente con la dirección URL de depuración para poder ejecutar el depurador de Visual Studio (F5). Cuando el complemento esté listo para la fase de ensayo, lo volverá a registrar con la dirección URL de su Sitio web de Azure de ensayo.  [Modificar el complemento y pasarlo a la fase de ensayo a Azure y Office 365.](#Stage)).* **

1. Resalte el proyecto de ASP.NET en el **Explorador de soluciones**. 


2. En la ventana **Propiedades**, copie el valor de la propiedad **Dirección URL de SSL**. Un ejemplo es https://localhost:44300/.


3. Péguela en **URL DE INICIO DE SESIÓN** en el cuadro de diálogo **AGREGAR APLICACIÓN**.


10. En **URI DE ID. DE APLICACIÓN**, asigne a la aplicación un URI único, como el nombre de la aplicación anexado al final de la dirección URL de SSL; por ejemplo, https://localhost:44300/ContosoAutomobileCollection.


11. Haga clic en el botón de la marca de verificación. El panel de Azure para la aplicación se abre con un mensaje de resultado correcto.


12. Elija **CONFIGURAR** en la parte superior de la página.


13. Desplácese a **ID. DE CLIENTE** y haga una copia de él. Lo necesitará para un procedimiento posterior.


14. En la sección **Claves**, cree una clave. No aparecerá inicialmente. Haga clic en **GUARDAR** en la parte inferior de la página y la clave será visible. Haga una copia de ella. La necesitará para un procedimiento posterior.


15. Desplácese a **Permisos para otras aplicaciones** y seleccione su aplicación de servicio de Puerta de enlace de SAP para Microsoft.


16. Abra la lista desplegable **Permisos delegados** y habilite las casillas de los permisos para el servicio Puerta de enlace de SAP para Microsoft que su Complemento de SharePoint necesitará.


17. Haga clic en **GUARDAR** en la parte inferior de la pantalla.



### Configure la aplicación para que se comunique con Azure AD


1. En Visual Studio, abra el archivo web.config en el proyecto de ASP.NET.


2. En la sección  `<appSettings>`, Office Developer Tools para Visual Studio incluye otros elementos para el **ClientID** y **ClientSecret** del Complemento de SharePoint. (Se usan en el sistema de autorización de Azure ACS si la aplicación de ASP.NET accede a SharePoint. Puede pasarlos por alto en nuestro ejemplo, pero no los elimine. Son necesarios en las Complementos de SharePoint hospedadas por el proveedor aunque el complemento no acceda a los datos de SharePoint. Sus valores cambiarán cada vez que presione F5 en Visual Studio). Agregue los siguientes dos elementos a la sección. La aplicación los usa para autenticarse en Azure AD. (Recuerde que las aplicaciones, así como los usuarios, son entidades de seguridad en los sistemas de autenticación y autorización basados en OAuth).

 ```

<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
 ```

3. Inserte el identificador de cliente que guardó en su directorio de Azure AD en el procedimiento anterior, y el valor de la clave **ida:ClientID**. Deje las mayúsculas y minúsculas y la puntuación exactamente como las copió, y tenga cuidado de no incluir espacios al principio o al final del valor. La clave **ida:ClientKey** usa la *clave*  que guardó en el directorio. De nuevo, tenga cuidado de no insertar espacios ni de cambiar el valor de ninguna manera. Ahora, la sección `<appSettings>` tendrá un aspecto similar al siguiente. (El valor de **ClientId** puede ser un GUID o una cadena vacía).

 ```

<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
 ```


    > **NOTA**
      > Azure AD reconoce su aplicación por la dirección URL "localhost" que usó para registrarla. El identificador de cliente y la clave de cliente están asociados a esa identidad. Cuando esté listo para pasar la aplicación a la fase de ensayo a un Sitio web de Azure, volverá a registrarla con una dirección URL nueva. 
4. Aún en la sección **appSettings**, agregue una clave **Authority** y establezca su valor en el dominio de Office 365 ( *some_domain*  .onmicrosoft.com) de su cuenta de organización. En nuestro ejemplo, la cuenta de organización es Bob@<O365_domain>.onmicrosoft.com, por lo que la entidad es `<O365_domain>.onmicrosoft.com`. 

 ```

<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
 ```

5. Aún en la sección **appSettings**, agregue una clave **AppRedirectUrl** y configure su valor en la página a la que se debe redirigir el explorador del usuario después de que el complemento de ASP.NET haya obtenido un código de autorización de Azure AD. Normalmente, esta es la misma página en la que estaba el usuario cuando se realizó la llamada a Azure AD. En nuestro ejemplo, use el valor de dirección URL de SSL anexando "/Pages/Default.aspx", tal y como se muestra a continuación. (Este es otro valor que cambiará para el almacenamiento temporal).

 ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
 ```

6. Aún en la sección **appSettings**, agregue una clave **ResourceUrl** y establezca su valor en el URI del id. de la aplicación de Puerta de enlace de SAP para Microsoft ( *no*  el URI del id. de su aplicación ASP.NET). Solicite este valor al administrador de Puerta de enlace de SAP para Microsoft. Este es un ejemplo.

 ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
 ```


    La sección  `<appSettings>` tendrá un aspecto similar a este:



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

7. Guarde y cierre el archivo web.config.

    > **SUGERENCIA**
      > No deje abierto el archivo web.config cuando ejecute el depurador de Visual Studio (F5). Office Developer Tools para Visual Studio cambia el valor de **ClientId** (no el de **ida:ClientID**) cada vez que presiona F5. Esto requiere que responda a un mensaje para volver a cargar el archivo web.config, si está abierto, para poder ejecutar la depuración. 

### Agregue una clase auxiliar para la autenticación en Azure AD


1. Haga clic con el botón secundario en el proyecto de ASP.NET y use el proceso para agregar un elemento de Visual Studio para agregar un nuevo archivo de clase al proyecto llamado AADAuthHelper.cs.


2. Agregue las siguientes instrucciones **using** al archivo.

 ```

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

 ```

3. Cambie la palabra clave de acceso de **public** a **internal** y agregue la palabra clave **static** a la declaración de la clase.

 ```

internal static class AADAuthHelper
 ```

4. Agregue los siguientes campos a la clase. Estos campos almacenan información que la aplicación ASP.NET usa para obtener tokens de acceso de AAD.

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

5. Agregue la siguiente propiedad a la clase. Esta propiedad inserta la dirección URL en la pantalla de inicio de sesión de Azure AD.

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

6. Agregue las siguientes propiedades a la clase. Almacenan en caché los tokens de acceso y actualización y comprueban su validez.

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

7. Agregue los siguientes métodos a la clase. Se usan para comprobar la validez del código de autorización y para obtener un token de acceso de Azure AD mediante un código de autenticación o un token de actualización.

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

8. Agregue el siguiente método a la clase. Se le llama desde el código subyacente de ASP.NET para obtener un token de acceso válido antes de realizar una llamada para obtener los datos de SAP mediante Puerta de enlace de SAP para Microsoft.

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


> **SUGERENCIA**
> La clase AADAuthHelper realiza un control de errores mínimo. Para obtener un Complemento de SharePoint de producción sólida y de calidad, agregue más control de errores tal y como se describe en este nodo de MSDN:  [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx). 





### Crear clases de modelo de datos


1. Cree una o varias clases para modelar los datos que el complemento obtiene de SAP. En nuestro ejemplo, solo hay una clase de modelo de datos. Haga clic con el botón secundario en el proyecto de ASP.NET y use el proceso para agregar un elemento de Visual Studio para agregar un nuevo archivo de clase al proyecto llamado Automobile.cs.


2. Agregue el siguiente código al cuerpo de la clase:

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


### Agregue código subyacente para obtener datos de SAP mediante Puerta de enlace de SAP para Microsoft


1. Abra el archivo Default.aspx.cs y agregue las instrucciones **using** siguientes.

 ```

using System.Net;
using Newtonsoft.Json.Linq;
 ```

2. Agregue una declaración **const** a la clase Default cuyo valor sea la dirección URL base del extremo OData de SAP al que accederá el complemento. El siguiente es un ejemplo:

 ```

private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
 ```

3. Office Developer Tools para Visual Studio ha agregado un método **Page_PreInit** y un método **Page_Load**. Establezca como comentario el código situado dentro del método **Page_Load** y convierta en comentario todo el método **Page_Init**. Este código no se usa en este ejemplo. (Si su Complemento de SharePoint va a acceder a SharePoint, restablezca este código. Vea  [También puede agregar acceso para SharePoint a la aplicación ASP.NET](#SharePoint)).


4. Agregue la siguiente línea en la parte superior del método **Page_Load**. Esto facilita el proceso de depuración porque su aplicación ASP.NET se comunica con Puerta de enlace de SAP para Microsoft mediante SSL (HTTPS), pero su servidor "localhost:port" no está configurado para que confíe en el certificado de Puerta de enlace de SAP para Microsoft. Sin esta línea de código, obtendría una advertencia de certificado no válido antes de que Default.aspx se abra. Algunos exploradores permiten hacer clic después de este error pero otros no permitirán siquiera abrir Default.aspx.

 ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
 ```


    > **IMPORTANTE**
      > Elimine esta línea cuando esté listo para implementar la aplicación ASP.NET en el entorno de ensayo. Vea  [Modificar el complemento y pasarlo a la fase de ensayo a Azure y Office 365.](#Stage). 
5. Agregue el código siguiente al método **Page_Load**. La cadena que pasa al método  `GetSAPData` es una consulta de OData.

 ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

 ```

6. Agregue el siguiente método a la clase Default. Este método garantiza primero que la memoria caché del token de acceso contiene un token de acceso válido, obtenido de Azure AD. Después, crea una solicitud **GET** HTTP que incluye el token de acceso y lo envía al extremo OData de SAP. El resultado se devuelve como un objeto JSON que se convierte en un objeto **List** de .NET. Se usan tres propiedades de los elementos en una matriz enlazada a **DataListView**.

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


### Crear la interfaz de usuario


1. Abra el archivo Default.aspx y agregue el siguiente marcado al **form** de la página:

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

2. También puede dar a la página el aspecto de una página de SharePoint con el  [control Chrome](use-the-client-chrome-control-in-sharepoint-add-ins.md) de SharePoint y [la hoja de estilos del sitio web de SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md).



### Pruebe el complemento con F5 en Visual Studio


1. Presione F5 en Visual Studio.


2. La primera vez que use F5, puede que se le pida que inicie sesión en el Sitio para desarrolladores que está usando. Use las credenciales del administrador del sitio. En nuestro ejemplo, es Bob@<O365_domain>.onmicrosoft.com.


3. La primera vez que use F5, se le pedirá que conceda permisos al complemento. Haga clic en **Confiar**.


4. Después de una breve demora mientras se obtiene el token de acceso, se abre la página Default.aspx. Compruebe que se muestran los datos de SAP.



## También puede agregar acceso para SharePoint a la aplicación ASP.NET
<a name="SharePoint"> </a>

Por supuesto, su Complemento de SharePoint no tiene que exponer solo datos de SAP en una página web iniciada desde SharePoint. También puede crear, leer, actualizar y eliminar (CRUD) datos de SharePoint. Su código subyacente puede hacerlo usando el modelo de objetos de cliente (CSOM) de SharePoint o las API REST de SharePoint. El CSOM se implementa como un par de ensamblados que Office Developer Tools para Visual Studio incluyó automáticamente en el proyecto de ASP.NET y estableció como **Copia local** en Visual Studio para que se incluyeran en el paquete de la aplicación de ASP.NET. Para obtener más información sobre cómo usar CSOM, comience con [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md). Para obtener más información sobre cómo usar las API REST, comience con  [Descripción y uso de la interfaz REST de SharePoint 2013](http://msdn.microsoft.com/es-es/magazine/dn198245.aspx). 



Independientemente de si usa CSOM o las API REST para acceder a SharePoint, su aplicación de ASP.NET debe obtener un token de acceso para SharePoint, igual que hace con Puerta de enlace de SAP para Microsoft. Vea  [Descripción de la autenticación y autorización en Puerta de enlace de SAP para Microsoft y SharePoint](#AuthOverview), más arriba. El siguiente procedimiento proporciona una guía básica para hacerlo, pero le recomendamos que lea primero los artículos siguientes:




-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)


-  [Tres sistemas de autorización para complementos de SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)


-  [Crear complementos de SharePoint que usen autorización de baja confianza](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)


-  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)



1. Abra el archivo Default.aspx.cs y quite los comentarios del método **Page_PreInit**. Quite también los comentarios del código que Office Developer Tools para Visual Studio agregó al método **Page_Load**.


2. Si el Complemento de SharePoint va a acceder a los datos de SharePoint, almacene en caché el token de contexto de SharePoint que se envía mediante POST a la página Default.aspx cuando se inicia el complemento en SharePoint. Esto es así para garantizar que el token de contexto de SharePoint no se pierde al redirigir el explorador siguiendo la autenticación de Azure AD. (Dispone de varias opciones para almacenar en caché este contexto). Office Developer Tools para Visual Studio agrega un archivo SharePointContext.cs al proyecto de ASP.NET que realiza la mayor parte del trabajo. Para usar la caché de sesión, simplemente agregue el siguiente código en el bloque " `if (!IsPostBack)`"  *antes*  de que el código llame a Puerta de enlace de SAP para Microsoft:

 ```

if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
 ```

3. El archivo SharePointContext.cs hace llamadas a otro archivo que Office Developer Tools para Visual Studio agregó al proyecto: TokenHelper.cs. Este archivo proporciona la mayor parte del código necesario para obtener y usar tokens de acceso para SharePoint. Sin embargo, no proporciona código para renovar un token de acceso o de actualización expirado. Para un Complemento de SharePoint de producción de calidad, debe agregar dicho código. La lógica de almacenamiento en caché del paso anterior es un ejemplo. El código también almacena en caché el token de acceso y lo vuelve a usar hasta que expira. Cuando el token de acceso expira, el código debe usar el token de actualización para obtener un nuevo token de acceso.


4. Agregue llamadas a datos a SharePoint usando CSOM o REST. El ejemplo siguiente es una modificación del código de CSOM que Office Developer Tools para Visual Studio agrega al método **Page_Load**. En este ejemplo, el código se ha movido a un código diferente y comienza recuperando el token de contexto almacenado en caché.

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

5. Agregue elementos de interfaz de usuario para representar los datos de SharePoint. A continuación se muestra el control HTML al que se hace referencia en el método anterior:

 ```

<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
 ```


> **NOTA**
> Mientras depura el Complemento de SharePoint, Office Developer Tools para Visual Studio vuelve a registrarlo con Azure ACS cada vez que presiona F5 en Visual Studio. Cuando pase el Complemento de SharePoint a la fase de ensayo, tiene que proporcionarle un registro a largo plazo. Vea la sección  [Modificar el complemento y pasarlo a la fase de ensayo a Azure y Office 365.](#Stage). 





## Modificar el complemento y pasarlo a la fase de ensayo a Azure y Office 365.
<a name="Stage"> </a>

Cuando termine de depurar el Complemento de SharePoint usando F5 en Visual Studio, implemente la aplicación de ASP.NET en un Sitio web de Azure real.




### Crear el sitio web de Azure


1. En el portal de Microsoft Azure, abra **SITIOS WEB** en la barra de navegación izquierda.


2. Haga clic en **NUEVO** en la parte inferior de la página y, en el cuadro de diálogo **NUEVO**, seleccione **SITIO WEB** |**CREACIÓN RÁPIDA**.


3. Escriba un nombre de dominio y haga clic en **CREAR SITIO WEB**. Haga una copia de la dirección URL del nuevo sitio. Tendrá el formato  `my_domain.azurewebsites.net`.



### Modificar el código y el marcado en la aplicación


1. En Visual Studio, quite la línea  `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;` del archivo Default.aspx.cs.


2. Abra el archivo web.config del proyecto de ASP.NET y cambie la parte correspondiente al dominio en el valor de la clave **AppRedirectUrl**, en la sección **appSettings**, por el dominio del Sitio web de Azure. Por ejemplo, cambie  `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` por `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`.


3. Haga clic con el botón derecho en el archivo AppManifest.xml del proyecto del Complemento de SharePoint y seleccione **Ver código**.


4. En el valor de **StartPage**, reemplace la cadena  `~remoteAppUrl` por el dominio completo del Sitio web de Azure, incluido el protocolo; por ejemplo, `https://my_domain.azurewebsites.net`. El valor de **StartPage** completo ahora debería ser: `https://my_domain.azurewebsites.net/Pages/Default.aspx`. (Normalmente, el valor de **StartPage** es exactamente el mismo que el valor de la clave **AppRedirectUrl** en el archivo web.config).



### Modificar el registro de AAD y registrar el complemento con ACS


1. Inicie sesión en el  [Portal de administración de Azure](https://manage.windowsazure.com) con su cuenta de administrador de Azure.


2. Elija **Active Directory** a la izquierda.


3. Haga clic en su directorio.


4. Elija **APLICACIONES** (en la barra de navegación superior).


5. Abra la aplicación que ha creado. En nuestro ejemplo, es **ContosoAutomobileCollection**.


6. Para cada uno de los siguientes valores, cambie la parte "localhost: *port*  " del valor por el dominio del nuevo Sitio web de Azure:

  - **URL DE INICIO DE SESIÓN**


  - **URI DE ID DE APLICACIÓN**


  - **URL DE RESPUESTA**



    Por ejemplo, si el **URI DE ID DE APLICACIÓN** es **https://localhost:44304/ContosoAutomobileCollection**, cámbielo por **https://<my_domain>.azurewebsites.net/ContosoAutomobileCollection**.


7. Haga clic en **GUARDAR** en la parte inferior de la pantalla.


8. Registre el complemento en Azure ACS. Esto se debe hacer incluso si el complemento no accede a SharePoint y no usa los tokens de ACS, porque el mismo proceso registra también el complemento con el Servicio de administración de complementos de la suscripción de Office 365, que es algo obligatorio. (Se llama "Servicio de administración de complementos" porque los Complementos de SharePoint se denominaban en un principio "aplicaciones para SharePoint"). El registro se realiza en la página AppRegNew.aspx de cualquier sitio web de SharePoint de la suscripción de Office 365. Para obtener detalles, consulte  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md). Como parte de este proceso, obtendrá un nuevo identificador de cliente y un secreto de cliente. Inserte estos valores en web.config para las claves **ClientId** (no **ida:ClientID**) y **ClientSecret**.

    > **PRECAUCIóN**
      > Si por cualquier motivo presiona F5 después de hacer este cambio, Office Developer Tools para Visual Studio sobrescribirá uno o ambos de estos valores. Por eso debería guardar un registro de los valores obtenidos con AppRegNew.aspx y comprobar siempre que los valores en web.config son los correctos antes de publicar la aplicación ASP.NET. 

### Publicar la aplicación ASP.NET en Azure e instalar el complemento en SharePoint


1. Hay varias maneras de publicar una aplicación ASP.NET en un Sitio web de Azure. Para obtener más información, vea  [Implementación de un sitio web de Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-deploy/).


2. En Visual Studio, haga clic con el botón secundario en el proyecto del complemento de SharePoint y seleccione **Paquete**. En la página **Publicar el complemento** que se abre, haga clic en **Empaquetar el complemento**. El explorador de archivos se abre en la carpeta con el paquete de complementos.


3. Inicie sesión en Office 365 como administrador global y vaya a la colección de sitios del catálogo de complementos de la organización. (Si no hay una, créela. Vea  [Usar el Catálogo de complementos para que los complementos empresariales personalizados estén disponibles en su entorno de SharePoint Online](http://office.microsoft.com/es-es/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx)).


4. Cargue el paquete de complementos en el catálogo de complementos.


5. Vaya a la página **Contenidos del sitio** de cualquier sitio web de la suscripción y haga clic en **Agregar un complemento**.


6. En la página **Sus complementos**, vaya a la sección **Complementos que puede agregar** y haga clic en el icono del complemento.


7. Una vez instalado el complemento, haga clic en el icono de la página **Contenidos del sitio** para iniciar el complemento.


Para más información sobre cómo instalar Complementos de SharePoint, vea  [Implementación e instalación de aplicaciones para SharePoint: métodos y opciones](deploying-and-installing-sharepoint-add-ins-methods-and-options.md).
## Implementar el complemento para producción
<a name="Stage"> </a>

Cuando haya terminado todas las pruebas, puede implementar el complemento en producción. Esto puede necesitar algunos cambios.




1. Si el dominio de producción de la aplicación de ASP.NET es diferente del dominio de ensayo, tendrá que cambiar el valor de **AppRedirectUrl** en el archivo web.config y el valor de **StartPage** en el archivo AppManifest.xml, y volver a empaquetar el Complemento de SharePoint. Vea más arriba el procedimiento **Modificar el código y el marcado en la aplicación**.


2. El cambio de dominio también necesita que se edite el registro de complementos de AAD. Vea el procedimiento **Modificar el registro de AAD y registrar el complemento con ACS**, más arriba.


3. El cambio de dominio también necesita volver a registrar el complemento con ACS (y el Servicio de administración de complementos de la suscripción), tal y como se describe en el mismo procedimiento. (No hay ninguna forma de  *editar*  el registro de un complemento con ACS). Sin embargo, no es necesario generar un nuevo identificador de cliente o secreto de cliente en la página AppRegNew.aspx. Puede copiar los valores originales de las claves **ClientId** (no **ida:ClientID**) y **ClientSecret** del archivo web.config en el formulario AppRegNew. *Si genera valores nuevos, asegúrese de copiarlos en las claves en web.config.* 



