---
title: Usar el control Selector de personas del lado cliente en complementos hospedados por SharePoint
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# Usar el control Selector de personas del lado cliente en complementos hospedados por SharePoint
Aprenda a usar el control Selector de personas del lado cliente en las Complementos de SharePoint hospedadas por SharePoint.
> **IMPORTANTE**
> En este tema se asume que sabe cómo crear una Complemento de SharePoint hospedada en SharePoint. Para aprender a crear una, comience en  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md). 
  
    
    


## ¿Qué es el control Selector de personas del lado cliente de SharePoint 2013?
<a name="bk_whatIs"> </a>

El control Selector de personas del lado cliente permite a los usuarios buscar y seleccionar rápidamente cuentas de usuario válidas para usuarios individuales, grupos y notificaciones en su organización. El selector es un control HTML y JavaScript que proporciona compatibilidad entre exploradores. Agregar el selector al complemento es sencillo: en el marcado, agregue un elemento contenedor para el control y las referencias para ese control y sus dependencias. Luego, en el script, llame a la función global **SPClientPeoplePicker_InitStandaloneControlWrapper** para que represente e inicialice el selector.
  
    
    
El selector está representado por el objeto **SPClientPeoplePicker**, que proporciona métodos que otros controles del lado cliente pueden usar para obtener información del selector o para llevar a cabo otras tareas. Puede usar las propiedades **SPClientPeoplePicker** para configurar el selector con opciones específicas de controles, como permitir múltiples usuarios o especificar opciones de almacenamiento en caché. El selector también usa opciones de configuración de aplicaciones web, como parámetros Servicios de dominio de Active Directory o bosques dirigidos. Las opciones de configuración de aplicaciones web se seleccionan de forma automática (desde la propiedad **SPWebApplication.PeoplePickerSettings**).
  
    
    
El selector tiene los componentes siguientes:
  
    
    

- Un cuadro de entrada de texto donde escribir los nombres de los usuarios, los grupos o las notificaciones.
    
  
- Un control span que muestra los nombres de los usuarios, grupos o notificaciones que se resolvieron.
    
  
- Un elemento **div** oculto que autorellena un cuadro desplegable con resultados de la consulta.
    
  
- Un control de autorelleno.
    
  

> **NOTA**
> El selector y sus funciones vienen definidos por los archivos de script **clientforms.js**, **clientpeoplepicker.js** y **autofill.js**, que se encuentran en la carpeta %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\TEMPLATE\\LAYOUTS en el servidor. 
  
    
    


## Requisitos previos para configurar el entorno de desarrollo para usar el control Selector de personas del lado cliente en una Complemento de SharePoint 2013
<a name="bk_prereqs"> </a>

En este artículo se presupone que crea las Complemento de SharePoint con Napa en un sitio para desarrolladores de Office 365. Si está usando este entorno de desarrollo, significa que cumple los requisitos previos.
  
    
    

> **NOTA**
> Vaya a  [Configurar un entorno de desarrollo para complementos para SharePoint en Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) para descubrir cómo registrarse para obtener un sitio para desarrolladores y empezar a usar Napa.
  
    
    

Si no usa Napa en un sitio para desarrolladores, necesitará:
  
    
    

- SharePoint 2013 con, al menos, un usuario de destino
    
  
- Visual Studio 2012 o Visual Studio 2013
    
  
- Office Developer Tools para Visual Studio 2013
    
  

> **NOTA**
> En este enlace encontrará información para configurar un entorno de desarrollo que se ajuste a sus necesidades:  [Empezar a crear aplicaciones para Office y SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 
  
    
    

En los siguientes pasos se describen las instrucciones de nivel superior para agregar un selector al complemento y luego obtener la información de usuario desde otro control del lado cliente. Consulte  [Ejemplo de código: Cómo usar el Selector de personas del lado cliente](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example) para el código correspondiente.
  
    
    
Puede usar el Selector de personas del lado cliente en las Complementos de SharePoint que hospeda SharePoint, pero no en los complementos que hospeda el proveedor. Para ver un ejemplo de cómo implementar un control de selector de personas en un complemento hospedado por el proveedor, descargue los  [Ejemplos del modelo de complementos de Office](http://officeams.codeplex.com).
  
    
    

## Usar el control Selector de personas del lado cliente en un complemento hospedado por SharePoint
<a name="bk_steps"> </a>


### En el marcado de la página


1. Agregue referencias a las dependencias de script del control Selector de personas del lado cliente.
    
  
2. Agregue las etiquetas HTML para la interfaz de usuario de la página. El complemento de este ejemplo define dos elementos **div**: uno para que el selector se represente y otro para la interfaz de usuario, un botón que invoca el script para que consulte al selector y a los elementos que muestran información del usuario.
    
  

### En el script


1. Cree un diccionario JSON para usarlo como esquema que almacena propiedades específicas del selector, como **AllowMultipleValues** o **MaximumEntitySuggestions**.
    
  
2. Llame a la función global **SPClientPeoplePicker_InitStandaloneControlWrapper**.
    
  
3. Obtenga el objeto selector de la página.
    
  
4. Consulte al Selector. En este ejemplo, el complemento llama al método **GetAllUserInfo** para obtener toda la información de usuario perteneciente a los usuarios resueltos y al método **GetAllUserKeys** solo para obtener las claves de los usuarios resueltos.
    
  
5. Obtenga el identificador de usuario con el modelo de objetos de JavaScript. El identificador de usuario no está incluido en la información que devuelve el selector, así que el complemento llama al método **SP.Web.ensureUser** y obtiene el identificador del objeto **SP.User** devuelto.
    
  
El servidor controla las funciones del Selector, como la representación o la inicialización, así como la búsqueda y la resolución de entradas de usuario con los proveedores de autenticación de SharePoint.
  
    
    

## Ejemplo de código: cómo usar el Selector de personas del lado cliente en una aplicación hospedada de SharePoint
<a name="bk_example"> </a>

Los siguientes ejemplos de código HTML y JavaScript agregan un control Selector de personas del lado cliente a un complemento hospedado por SharePoint.
  
    
    
En el primer ejemplo se muestra el marcado de página de las etiquetas **PlaceHolderMain** **asp:Content** de la página Default.aspx. Este código hace referencia a las dependencias de script del selector, otorga un identificador único al elemento DOM donde se representará el selector y define la interfaz de usuario del complemento.
  
    
    



```HTML

<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
    <SharePoint:ScriptLink name="clienttemplates.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientforms.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientpeoplepicker.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="autofill.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.runtime.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.core.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <div id="peoplePickerDiv"></div>
    <div>
        <br/>
        <input type="button" value="Get User Info" onclick="getUserInfo()"></input>
        <br/>
        <h1>User info:</h1>
        <p id="resolvedUsers"></p>
        <h1>User keys:</h1>
        <p id="userKeys"></p> 
        <h1>User ID:</h1>
        <p id="userId"></p>
    </div>
</asp:Content>
```


> **NOTA**
> Dependiendo del entorno en cuestión, quizá no sea necesario especificar las referencias de todas estas dependencias. 
  
    
    

En el siguiente ejemplo se muestra el script del archivo App.js. Este script inicializa y representa el Selector, lo consulta para obtener información de usuario y, a continuación, obtiene el identificador de usuario mediante el modelo de objetos JavaScript.
  
    
    



```

// Run your custom code when the DOM is ready.
$(document).ready(function () {

    // Specify the unique ID of the DOM element where the
    // picker will render.
    initializePeoplePicker('peoplePickerDiv');
});

// Render and initialize the client-side People Picker.
function initializePeoplePicker(peoplePickerElementId) {

    // Create a schema to store picker properties, and set the properties.
    var schema = {};
    schema['PrincipalAccountType'] = 'User,DL,SecGroup,SPGroup';
    schema['SearchPrincipalSource'] = 15;
    schema['ResolvePrincipalSource'] = 15;
    schema['AllowMultipleValues'] = true;
    schema['MaximumEntitySuggestions'] = 50;
    schema['Width'] = '280px';

    // Render and initialize the picker. 
    // Pass the ID of the DOM element that contains the picker, an array of initial
    // PickerEntity objects to set the picker value, and a schema that defines
    // picker properties.
    this.SPClientPeoplePicker_InitStandaloneControlWrapper(peoplePickerElementId, null, schema);
}

// Query the picker for user information.
function getUserInfo() {

    // Get the people picker object from the page.
    var peoplePicker = this.SPClientPeoplePicker.SPClientPeoplePickerDict.peoplePickerDiv_TopSpan;

    // Get information about all users.
    var users = peoplePicker.GetAllUserInfo();
    var userInfo = '';
    for (var i = 0; i < users.length; i++) {
        var user = users[i];
        for (var userProperty in user) { 
            userInfo += userProperty + ':  ' + user[userProperty] + '<br>';
        }
    }
    $('#resolvedUsers').html(userInfo);

    // Get user keys.
    var keys = peoplePicker.GetAllUserKeys();
    $('#userKeys').html(keys);

    // Get the first user's ID by using the login name.
    getUserId(users[0].Key);
}

// Get the user ID.
function getUserId(loginName) {
    var context = new SP.ClientContext.get_current();
    this.user = context.get_web().ensureUser(loginName);
    context.load(this.user);
    context.executeQueryAsync(
         Function.createDelegate(null, ensureUserSuccess), 
         Function.createDelegate(null, onFail)
    );
}

function ensureUserSuccess() {
    $('#userId').html(this.user.get_id());
}

function onFail(sender, args) {
    alert('Query failed. Error: ' + args.get_message());
}
```


## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Crear componentes de experiencia de usuario en SharePoint 2013](create-ux-components-in-sharepoint-2013.md)
    
  
-  [Introducción al Selector de personas y los proveedores de notificaciones (SharePoint 2013)](http://technet.microsoft.com/library/gg602078.aspx)
    
  
-  [Configuración del Selector de personas en SharePoint 2013](http://technet.microsoft.com/library/gg602075.aspx)
    
  

