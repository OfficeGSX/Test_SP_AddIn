---
title: Obtener la identidad y las propiedades del usuario en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# Obtener la identidad y las propiedades del usuario en SharePoint 2013
Recupere información sobre la identidad del usuario y sobre el usuario en SharePoint 2013.
Existen distintas formas de recuperar la identidad e información del usuario, en función de la información que desee recuperar. En este artículo se muestran algunos métodos para hacerlo.
  
    
    


## Requisitos previos para recuperar la identidad y propiedades del usuario
<a name="Prereq"> </a>


- Prepare el entorno de desarrollo del complemento según se describe en  [Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
    
  
- Instale Visual Studio.
    
  
- Instale la versión más reciente de  [Office Developer Tools para Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) u [Office Developer Tools para Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015).
    
  

> **NOTA**
> En el siguiente enlace encontrará información sobre cómo configurar un entorno de desarrollo que se ajuste a sus necesidades:  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md). 
  
    
    


### Conceptos básicos necesarios para recuperar la identidad y propiedades del usuario

En la tabla siguiente se enumeran algunos artículos que resultan útiles a la hora de abordar los conceptos relativos a la creación de Complementos de SharePoint.
  
    
    


|**Artículo**|**Descripción**|
|:-----|:-----|
| [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) <br/> |Obtenga información sobre los permisos de complemento de SharePoint para trabajar con SharePoint 2013, lo cual incluye los tipos de permisos de complemento, el ámbito de la solicitud del permiso y la administración de permisos. En este artículo también se describen las diferencias entre los derechos de los permisos de complemento y los derechos de usuario.  <br/> |
| [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |Obtenga información sobre la autenticación OAuth y el flujo de autorizaciones para los complementos hospedados en la nube.  <br/> |
| [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |Obtenga información sobre cómo crear una Complemento de SharePoint hospedada en el proveedor con Office Developer Tools para Visual Studio 2012, cómo interactuar con sitios de Microsoft SharePoint 2013 mediante el CSOM de SharePoint y cómo implementar OAuth en una Complemento de SharePoint.  <br/> |
   

## Recuperar la identidad del usuario actual del sitio web
<a name="WebsiteUserID"> </a>

La forma más sencilla de recuperar la identidad del usuario actual de un sitio web es a través del objeto **Web**. Con el archivo TokenHelper.cs en el proyecto, podrá obtener la identidad del usuario actual del sitio web mediante el siguiente fragmento de código.
  
    
    

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


- Si está usando Office 365, el nombre de inicio de sesión que obtiene será similar a  `i:0#.f|membership|adam@contoso.com`.
    
  
- Si está usando Microsoft SharePoint 2013 localmente y el usuario ha iniciado sesión como un usuario normal mediante NTLM, el nombre de inicio de sesión que obtendrá será similar a  `i:0#.w|contoso\\adam`.
    
  
- Si está usando SharePoint 2013 localmente y el usuario ha iniciado sesión con una cuenta de granja, el nombre de usuario que obtendrá es  `SHAREPOINT\\System`.
    
  

## Recuperar la identidad del usuario con el método ResolvePrincipal
<a name="ResolvePrincipal"> </a>

A continuación, se describe otro método para recuperar el nombre de inicio de sesión del usuario. Si dispone de la dirección de correo electrónico del usuario o del nombre para mostrar, puede usar el método  [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) para obtener el nombre de inicio de sesión del usuario.
  
    
    

> **NOTA**
> Las API se encuentran en el espacio de nombres Microsoft.SharePoint.Client.Utilities en el ensamblado Microsoft.SharePoint.Client.dll [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/es-es/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx). 
  
    
    

A continuación se ofrece un ejemplo donde se muestra cómo obtener la información de inicio de sesión del usuario.
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

El valor **Person.LoginName** facilita la información de inicio de sesión.
  
    
    

## Recuperar las propiedades de identidad y perfil del usuario
<a name="Profile"> </a>

Si desea recuperar la identidad y propiedades del usuario, puede usar el token de OAuth y las API de características sociales. En primer lugar, obtenga el token de OAuth y, después, configúrelo según el contexto del cliente. El siguiente código de ejemplo muestra cómo obtener las propiedades del perfil de usuario.
  
    
    

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

A continuación, use la API  [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) para obtener las propiedades del usuario que está usando el complemento.
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

Respecto al código de trabajo:
  
    
    

- El servicio compartido de perfil del usuario se debe configurar y sincronizar en SharePoint 2013 para los usuarios.
    
  
- Es preciso que agregue el siguiente ámbito de permisos para las características sociales en el manifiesto del complemento:
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

Las API están en Microsoft.SharePoint.Client.UserProfiles.dll.
  
    
    
A continuación, se ofrece otro ejemplo de fragmento de código que muestra cómo obtener acceso al almacén de perfiles de usuario.
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## Recursos adicionales
<a name="AdditionalResources"> </a>


-  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Complementos de SharePoint](sharepoint-add-ins.md)
    
  
-  [Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

