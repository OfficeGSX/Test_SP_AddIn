

# Sugerencias y preguntas frecuentes: OAuth y aplicaciones remotas para SharePoint
Este tema ya está obsoleto. Consulte los siguientes vínculos para encontrar dónde se encuentra el contenido. 





||
|:-----|
|**En este artículo** <BR/> [Cómo recuperar información de una aplicación](#AppInfo) <BR/> [Archivo AppManifest.xml](#Manifest) <BR/> [Archivo Web.config](#Webconfig) <BR/> [Direcciones URL y Capa de sockets seguros (SSL)](#URL) <BR/> [Página de redireccionamiento de la aplicación ](#Redirect) <BR/> [URI de redireccionamiento](#RedirectURI) <BR/> [Tokens de OAuth](#Tokens) <BR/> [Permisos y ámbitos de solicitudes de permisos](#Perm) <BR/> [Tipos de políticas de autorización OAuth](#Policy) <BR/> [Depuración](#Debugging) <BR/> [Aplicaciones de confianza alta (aplicaciones de servidor a servidor) localmente](#S2S) <BR/> [Problemas adicionales relacionados con OAuth](#Misc) <BR/> [Características sociales](#Social) <BR/> [Recursos adicionales](#AR)|
 

## Cómo recuperar información de una aplicación
<a name="AppInfo"> </a>

Este contenido se ha movido a  [Recuperación de información sobre el registro del complemento y la entidad de seguridad del complemento ](register-sharepoint-add-ins-2013.md#Retrieve).




## Archivo AppManifest.xml
<a name="Manifest"> </a>

Este contenido se ha movido a:




-  [Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)


-  [Referencia de esquema para los manifiestos de aplicaciones de SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)



### ¿Qué es la URL en el elemento <StartPage>?

Este contenido se ha movido a: 








>  [Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)






>  [Elemento StartPage](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)



### ¿Cuáles son los elementos y atributos del archivo AppManifest.xml?

Este contenido se ha movido a  [Referencia de esquema para los manifiestos de aplicaciones de SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)




## Archivo Web.config
<a name="Webconfig"> </a>

Este contenido se ha movido a  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md), particularmente la sección  [Insertar los valores de registro en los archivos web.config y AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).




### ¿Cuáles son las configuraciones del archivo Web.config?

Este contenido se ha movido a  [Insertar los valores de registro en los archivos web.config y AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).




## Direcciones URL y Capa de sockets seguros (SSL)
<a name="URL"> </a>

Este contenido se ha movido a: 




-  [Varios errores SSL y relacionados con el dominio en aplicaciones de elevada confianza](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [Varios errores SSL y relacionados con el dominio en aplicaciones de ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### Mi aplicación web tiene problemas para comunicarse de vuelta con SharePoint. ¿Qué debería comprobar?

Este contenido se ha movido a: 




-  [Varios errores SSL y relacionados con el dominio en aplicaciones de elevada confianza](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [Varios errores SSL y relacionados con el dominio en aplicaciones de ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### ¿Qué URL debería integrar como parte del código en mi aplicación para que apunte a mi servidor en la nube?

Este contenido se ha movido a: 




-  [Varios errores SSL y relacionados con el dominio en aplicaciones de elevada confianza](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [Varios errores SSL y relacionados con el dominio en aplicaciones de ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### ¿Debería registrar el alias CNAME o la verdadera URL subyacente que hospeda la aplicación?

Este contenido se ha movido a: 




-  [Varios errores SSL y relacionados con el dominio en aplicaciones de elevada confianza](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)


-  [Varios errores SSL y relacionados con el dominio en aplicaciones de ACS](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)



### Recibo el mensaje "Se cerró la conexión subyacente: no se pude establecer una relación de confianza para el canal seguro SSL/TLS." ¿Qué debo hacer?

Este contenido se ha movido a  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed).




## Página de redireccionamiento de la aplicación
<a name="Redirect"> </a>

Este contenido se ha movido a  [Obtener un nuevo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).




### ¿Para qué debo usar la página de redireccionamiento de la aplicación?

Este contenido se ha movido a  [Obtener un nuevo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).




### ¿Cómo uso una página de redireccionamiento de la aplicación para obtener el token de contexto?

Este contenido se ha movido a  [Obtener un nuevo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).




### ¿Cómo uso la página appredirect en la URL?

Este contenido se ha movido a  [Obtener un nuevo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).




## URI de redireccionamiento
<a name="RedirectURI"> </a>

Este contenido se ha movido a  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md).




### ¿Necesito un URI de redireccionamiento?

Este contenido se ha movido a  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md).




## Tokens de OAuth
<a name="Tokens"> </a>

Este contenido se ha movido a:




-  [Administrar tokens de seguridad en complementos de confianza baja hospedados por el proveedor para SharePoint](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)


-  [Crear y usar tokens de acceso en complementos de SharePoint de elevada confianza hospedados por el proveedor](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)



### ¿Qué es un token de contexto?

Este contenido se ha movido a  [Descripción de la estructura y control de los tokens de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).




### ¿Qué es un token de acceso?

Este contenido se ha movido a  [Descripción de la administración de los tokens de acceso](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens).




### ¿Qué es un token de actualización?

Este contenido se ha movido a  [Descripción del control y almacenamiento en caché de tokens de actualización](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).




### ¿Debería estar la cadena de tokens de contexto almacenada en una cookie para que se pueda usar para solicitudes de otras páginas directamente desde la aplicación hospedada por el proveedor?

Este contenido se ha movido a  [Almacenar el token de contexto o partes de él](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).




### ¿De qué está compuesto el valor de la clave de caché? ¿De qué forma es exclusivo?

Este contenido se ha movido a  [Descripción de la clave de caché](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey).




### ¿Cómo recupero el token de contexto?

Este contenido se ha movido a  [Descripción de la estructura y control de los tokens de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).




### ¿Qué información contiene el token de contexto?

Este contenido se ha movido a  [Ejemplo de un token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken).




### ¿Qué información contiene un token de acceso?

Este contenido se ha movido a:




-  [Ejemplos de tokens de acceso en el sistema de autorización de ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)


-  [Ejemplo de un token de acceso en el sistema de autorización de elevada confianza](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)



### ¿Cómo calculo la hora y fecha exactas a partir del valor de nbf y exp?

Este contenido se ha movido a  [Trabajar con valores de tiempo de JWT](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes).




### Quiero proteger el svc para mis aplicaciones de los usuarios que no son de SharePoint. Compruebo la legitimidad del usuario en el punto de entrada de la aplicación (mediante la creación de ClientContext), pero cualquiera puede llamar a mi servicio de WCF. ¿Debo crear ClientContext desde un token de contexto en cada llamada del método svc?

Este contenido se ha movido a  [Usar el token de contexto para limitar el acceso solo a usuarios de SharePoint](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess).




### ¿Es correcto mantener AppContext (obtenida de una solicitud POST de SharePoint) como un campo de entrada oculto en la página?

Este contenido se ha movido a  [Almacenar el token de contexto o partes de él](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).




### ¿Por cuánto tiempo es válido un token de actualización?

Este contenido se ha movido a  [Descripción del control y almacenamiento en caché de tokens de actualización](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).




### Almaceno el token de acceso y la dirección URL de host en cookies para que se puedan usar en otras solicitudes de páginas. Pero el usuario se tomó un descanso y el token de acceso expiró. ¿Qué debo hacer?

Este contenido se ha movido a:




-  [Descripción de la administración de los tokens de acceso](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)


-  [Descripción del control y almacenamiento en caché de tokens de actualización](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)



### ¿En qué escenario debería descartar un token de actualización antiguo que no expiró, que todavía es válido y usar uno nuevo?

Este contenido se ha movido a  [Descripción del control y almacenamiento en caché de tokens de actualización](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).




## Permisos y ámbitos de solicitudes de permisos
<a name="Perm"> </a>

Este contenido se encuentra en  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).




### ¿Cuáles son los ámbitos de solicitud de permisos y los derechos disponibles para lista, biblioteca, contenido y otras características?

Este contenido se encuentra ahora en  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).




## Tipos de políticas de autorización OAuth
<a name="Policy"> </a>

Este contenido se encuentra en  [Tipos de directivas de autorización de complementos en SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).




### ¿Cuál es la diferencia entre la política de solo aplicación y la política de usuario + aplicación?

Este contenido se encuentra en  [Tipos de directivas de autorización de complementos en SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).




### ¿Hay alguna forma de otorgar o denegar el derecho a iniciar una aplicación?

Este contenido se encuentra en  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).




## Depuración
<a name="Debugging"> </a>

Este contenido se ha movido a: 




-  [Solucionar problemas de aplicaciones de elevada confianza para SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)


-  [Solucionar problemas de complementos de confianza baja para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)



### Cómo usar Fiddler

Este contenido se ha movido a:




-  [Solucionar problemas de aplicaciones de elevada confianza para SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)


-  [Solucionar problemas de complementos de confianza baja para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)



## Aplicaciones de confianza alta (aplicaciones de servidor a servidor) localmente
<a name="S2S"> </a>

Este contenido se ha movido a  [Solucionar problemas de aplicaciones de elevada confianza para SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)




### Recibo el mensaje de error no autorizado 401 cuando ejecuto una aplicación de confianza alta. ¿Qué debo hacer?

Este contenido se ha movido a  [Solucionar problemas de aplicaciones de elevada confianza para SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md).




### ¿Cómo obtengo un token de contexto para una aplicación de confianza alta?

Este contenido se ha movido a  [Crear y usar tokens de acceso en complementos de SharePoint de elevada confianza hospedados por el proveedor](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md).




## Problemas adicionales relacionados con OAuth
<a name="Misc"> </a>

Este contenido se ha movido a:




-  [Solucionar problemas de aplicaciones de elevada confianza para SharePoint](troubleshooting-high-trust-sharepoint-add-ins.md)


-  [Solucionar problemas de complementos de confianza baja para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)



### Cuando intento leer un archivo con el método HTTP DAV, recibo un mensaje de error. ¿Qué debo hacer?

Este contenido se ha movido a  [Solucionar problemas de complementos de confianza baja para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md).




### ¿Hay alguna forma de reenviar una autorización OAuth a otros componentes en diferentes dominios, o de configurar OAuth para múltiples URI?

Sí. Consulte  [Reenviar el token de acceso a los sistemas backend](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend).




### ¿El valor de la entidad de seguridad de SharePoint 2013 es constante?

Sí. Consulte  [Ejemplos de tokens de acceso en el sistema de autorización de ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (especialmente la tabla 1).




### ¿El identificador de la aplicación y el secreto de la aplicación son constantes en todos los inquilinos para una aplicación determinada?

Sí, si la aplicación se registra en el Panel de vendedores; pero si se registra por separado con cada granja o arrendamiento de SharePoint, puede tener secretos e identificadores distintos para cada uno. En ese caso, parece como varias aplicaciones en Servicio de control de acceso (ACS) de Microsoft Azure. Consulte  [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md).




### ¿Los dominios son exclusivos?

Sí. Consulte  [Ejemplos de tokens de acceso en el sistema de autorización de ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (especialmente la tabla 1) y [Usar el token de contexto para obtener un token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken).




### ¿Cómo desactivo la solicitud HTTPS para OAuth durante el desarrollo?

Este contenido se ha movido a  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement).




## Características sociales
<a name="Social"> </a>

Este contenido se ha movido a:




1.  [Características sociales y de colaboración en SharePoint 2013](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)


2.  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)



### ¿Cómo recupero la identidad y las propiedades de un usuario?

Vea  [Obtener la identidad y las propiedades del usuario en SharePoint 2013](get-user-identity-and-properties-in-sharepoint-2013.md).




### ¿Cuál es el uso para las diferentes características sociales y ámbitos de solicitud de permiso?

Este contenido se encuentra en  [Comience a desarrollar con características sociales en SharePoint 2013](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms).




### ¿Cómo obtengo propiedades de perfiles de usuario de personas que me siguen?

Este contenido se ha movido a  [Recuperar propiedades de perfil de usuario usando el modelo de objetos de cliente .NET en SharePoint 2013](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp).


    