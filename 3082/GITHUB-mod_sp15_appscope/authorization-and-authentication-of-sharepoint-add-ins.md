---
title: Autorización y autenticación de complementos de SharePoint
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# Autorización y autenticación de complementos de SharePoint
Vea información general sobre la autenticación y la autorización en SharePoint, usadas para autorizar las solicitudes hechas por un Complemento de SharePoint que desea obtener acceso a los recursos de SharePoint.
**Vea un vídeo sobre la identidad de complemento.**








![Vídeos](images/mod_icon_video.png)












## Autenticación de complementos en SharePoint
<a name="AuthN"> </a>

Cuando un  *usuario*  inicia sesión en SharePoint, se valida el token de seguridad del usuario. El token es emitido por un proveedor de identidad. SharePoint admite varios tipos de autenticación de usuario. Para más información, vea [Autenticación, autorización y seguridad en SharePoint 2013](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx).



Los Complementos de SharePoint son también entidades de seguridad que deben autenticarse y autorizarse. Los complementos se pueden autenticar y autorizar de varias maneras diferentes. Para obtener más información, vea  [Tres sistemas de autorización para complementos de SharePoint](three-authorization-systems-for-sharepoint-add-ins.md). 




## Directivas de autorización: directiva de solo usuario, directiva de usuario+complemento o directiva de solo complemento
<a name="AuthZ"> </a>

El proceso de autorización comprueba que un sujeto autenticado (un complemento, un usuario o ambos) tiene permiso para hacer ciertas operaciones o para obtener acceso a recursos específicos (por ejemplo, una lista o una carpeta de documentos de SharePoint).



SharePoint usa tres tipos de directivas de autorización. La directiva solo de usuario únicamente requiere que la llamada a SharePoint incluya una identidad de usuario autenticada. La directivasolo de complemento requiere únicamente que la llamada incluya una identidad de complemento autenticada. La directiva deusuario+complemento requiere que la llamada incluya ambos tipos de identidades autenticadas. Cuando un usuario accede a los recursos de SharePoint a través de la interfaz de usuario de SharePoint en lugar de a través de un complemento, SharePoint usa la directiva solo de usuario. Sin embargo, para las llamadas desde un Complemento de SharePoint, SharePoint siempre usa la directiva solo de complemento o la directiva de usuario+complemento. El Complemento de SharePoint determina la directiva que se usa según el tipo de token de acceso que incluye en su solicitud a SharePoint. Si se hace una solicitud de usuario+complemento, SharePoint requerirá que el complemento y el usuario tengan permisos para el recurso al que el complemento está accediendo. En el caso de una solicitud solo de complemento, SharePoint requiere que el complemento tenga permisos para el recurso, pero no importa si el usuario los tiene o no. (Un Complemento de SharePoint puede hacer solicitudes solo de complemento únicamente si se le ha dado permiso para hacerlo por adelantado; se suele hacer cuando se instala).



Para más información sobre las políticas de autorización y sobre cómo funcionan, vea  [Tipos de directivas de autorización de complementos en SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).




## Permisos de complementos y ámbitos de solicitudes de permisos de complementos
<a name="Permissions"> </a>

El desarrollador de un Complemento de SharePoint tiene que especificar, a través del archivo de manifiesto del complemento, los permisos que necesita un complemento para obtener acceso a los recursos de SharePoint fuera de la web de complemento. (El complemento automáticamente tiene permiso de control total en toda la web de complemento). Cuando se diseña el complemento para que se inicie desde SharePoint, la infraestructura de instalación del complemento le pide al usuario que lo instala que conceda o deniegue los permisos necesarios. Una vez que se han concedido los permisos, los usuarios del sitio web pueden usar el complemento sin tener que volver a conceder permisos. Sin embargo, cuando el complemento se diseña para que se inicie fuera de SharePoint (es decir, no se instala en SharePoint) SharePoint pedirá al usuario que ejecuta el complemento que le conceda los permisos necesarios todas las veces que se ejecute. Los complementos en dispositivos móviles y Complementos de Office son ejemplos de complementos que pueden acceder a SharePoint sin estar instalados en él.



Solo el propietario de un sitio web puede instalar un Complemento de SharePoint en un sitio web de SharePoint (a menos que se haya creado un rol personalizado que tenga derechos de instalación de complementos). Un usuario solo puede conceder a un complemento los permisos que tiene el propio usuario. Es decir, un usuario no puede instalar un complemento que necesita permisos que el usuario no tiene. De forma similar, un usuario no puede ejecutar un complemento iniciado externamente que necesita permisos que el usuario no tiene. Sin embargo, cuando se instala un Complemento de SharePoint en SharePoint, puede pedir permisos para realizar llamadas solo de aplicación. Un complemento que tenga permisos puede acceder a SharePoint de formas que el usuario que ejecuta el complemento no puede.



Los administradores de inquilinos de SharePoint Online o los administradores de granjas de servidores de SharePoint también pueden revocar o conceder permisos a los complementos.



En el archivo de manifiesto del complemento, un Complemento de SharePoint especifica los permisos que necesita para funcionar correctamente. Las solicitudes de permisos especifican tanto los derechos que necesita un complemento como el ámbito en el que necesitan los derechos. Los ámbitos indican en qué lugar de una jerarquía de SharePoint se aplica una solicitud de permiso. SharePoint admite cuatro ámbitos de contenido distintos: inquilino, colección de sitio, sitio web y lista. También hay ámbitos especiales para realizar consultas de búsqueda, obtener acceso a datos de taxonomía, funciones sociales, funciones de Servicios de conectividad empresarial de Microsoft (BCS) y funciones de Project Server 2013. Para obtener más información, vea  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).




## ¿Cuándo se usa OAuth?
<a name="FileName_uniquekeyword4"> </a>

Quizás haya oído que OAuth 2.0 juega un papel importante en la autenticación y autorización de Complementos de SharePoint. Lo hace, pero no forma parte necesariamente del proceso de autorización de cada Complemento de SharePoint. Si quiere compilar un Complemento de SharePoint que se ejecute en una aplicación web remota y se comunique con SharePoint mediante código del lado servidor, tiene que usar OAuth. Si la aplicación web remota no es local, hay que usar el  [sistema de autorización de baja confianza](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) en el que Azure ACS es el emisor de tokens de acceso. Si es local, se usa el [sistema de elevada confianza](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) en el que el propio complemento y un certificado digital son los emisores de los tokens de acceso.



OAuth no se usa para realizar llamadas desde JavaScript en una página en la propia web de complemento o desde una página web remota mediante  [la biblioteca entre dominios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md). Para obtener más información sobre la biblioteca entre dominios, vea  [Crear aplicaciones para SharePoint que usen la biblioteca entre dominios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md).




## Recursos adicionales
<a name="Filename_AdditionalResources"> </a>


-  [Tres sistemas de autorización para complementos de SharePoint](three-authorization-systems-for-sharepoint-add-ins.md)


-  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)


-  [Complementos de SharePoint](sharepoint-add-ins.md)



