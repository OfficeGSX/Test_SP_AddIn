---
title: Tres sistemas de autorización para complementos de SharePoint
ms.prod: SHAREPOINT
ms.assetid: 623fdab7-856e-4a89-9f5d-748a2ba1ef2e
---


# Tres sistemas de autorización para complementos de SharePoint
Conozca los tres sistemas que las Complementos de SharePoint pueden usar para obtener autorización para los recursos de SharePoint.
En SharePoint, una Complemento de SharePoint es una entidad de seguridad de identidades, igual que un usuario, y se debe autenticar y autorizar para usar los recursos de SharePoint. Un complemento puede usar tres sistemas de autorización, que no se excluyen entre sí.
  
    
    


## Los tres sistemas de autorización y cuándo usarlos
<a name="UnderstandThreeSystems"> </a>


  
    
    

- **Confianza baja**: una Complemento de SharePoint hospedada por el proveedor se puede registrar con Servicio de control de acceso (ACS) de Microsoft Azure, que emite un token de acceso para el complemento que le permite acceder a los recursos de la granja o del inquilino de SharePoint donde está instalado el complemento. Azure ACS es el emisor del token de confianza de un "flujo" de OAuth 2.0 Framework que incluye SharePoint y los componentes remotos del complemento. Los complementos que usan este sistema se pueden vender en la Tienda Office. El sistema de confianza baja está pensado principalmente para los complementos cuyos componentes remotos están hospedados en la nube.
    
    Para más información sobre la creación de un Complemento de SharePoint que use el sistema de confianza baja, vea el nodo del SDK  [Crear complementos de SharePoint que usen autorización de baja confianza](creating-sharepoint-add-ins-that-use-low-trust-authorization.md).
    
    > **NOTA**
      > El cliente que instala el complemento debe tener una cuenta de Office 365. Esto es necesario para que el complemento acceda a Azure ACS. Sin embargo, el cliente no necesita usar la cuenta para nada más y el complemento se puede instalar en una granja de SharePoint local tras realizar algunas tareas simples de configuración. 
- **Elevada confianza**: un complemento hospedado por el proveedor puede establecer confianza con SharePoint con certificados digitales. El sistema de elevada confianza está pensado principalmente para los complementos cuyos componentes remotos están hospedados localmente. El complemento se puede instalar en una granja de SharePoint que no esté conectada a Internet, pero no se puede instalar en SharePoint Online ni vender en la Tienda Office.
    
    Para más información sobre la creación de un Complemento de SharePoint que use el sistema de elevada confianza, vea el nodo del SDK  [Creación de complementos para SharePoint que usan el sistema de autorización de elevada confianza](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).
    
  
- **Biblioteca entre dominios**: cuando la lógica empresarial del complemento está en JavaScript, puede usar la biblioteca entre dominios de SharePoint en lugar de, o como complemento de, los sistemas de elevada confianza o de confianza baja. La biblioteca también está pensada para escenarios donde el complemento tiene componentes hospedados en la nube, pero donde el firewall de la empresa del cliente dificulta el uso del sistema de confianza baja. El explorador del usuario bloquea los scripts de otros dominios, pero la biblioteca encapsula un sistema seguro para evitar esta restricción. Los complementos que usan la biblioteca se pueden vender en la Tienda Office e instalar en SharePoint Online o en SharePoint local.
    
    Para más información sobre la creación de un Complemento de SharePoint que use la biblioteca entre dominios, vea el nodo del SDK  [Crear aplicaciones para SharePoint que usen la biblioteca entre dominios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md), la publicación de blog  [Resolver problemas entre dominios en los complementos de SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx).
    
  

## Información sobre OAuth 2.0 Framework y la implementación que SharePoint hace de él
<a name="UnderstandThreeSystems"> </a>

Dos de los tres sistemas de autorización usan OAuth 2.0 Framework. OAuth 2.0 es un **entorno abierto de autorización**. OAuth permite la autorización segura desde aplicaciones de escritorio, dispositivo y web de una manera estándar. OAuth permite a los usuarios autorizar a una aplicación para que actúe en su nombre sin compartir su nombre de usuario y contraseña. Por ejemplo, **permite a un usuario compartir** sus recursos o datos privados (lista de contactos, documentos, fotos, vídeos, etc.) de un sitio con otro sitio, **without requiring the user to provide his or her credentials** (normalmente el nombre de usuario y la contraseña) al otro sitio.
  
    
    
OAuth permite a los usuarios proporcionar tokens de acceso a los datos hospedados en un proveedor de servicios determinado (como SharePoint). Cada token otorga acceso a un proveedor de recursos concreto (como SharePoint) para recursos específicos (por ejemplo, los documentos de una biblioteca de documentos de SharePoint) y durante un lapso determinado (por ejemplo, 12 horas). Esto permite que un usuario conceda a una web o una aplicación de escritorio de terceros el acceso a información almacenada con otro proveedor de recursos o servicios (como SharePoint) sin compartir su nombre de usuario y contraseña ni  *todos*  los datos que tiene en su proveedor.
  
    
    
SharePoint usa el entorno **OAuth 2.0** **que usa "flujos" de transmisión de tokens** para:
  
    
    

- Autorizar solicitudes de una Complemento de SharePoint para acceder a los recursos de SharePoint.
    
  
- Autenticar complementos en la Tienda Office, en un catálogo de complementos o en un inquilino de desarrollador.
    
  
Para más información sobre OAuth y la terminología de OAuth, vea  [ OAuth.net](http://oauth.net/) y el [ Protocolo de autorización web (oauth)](http://datatracker.ietf.org/doc/active/). En resumen, hay un servidor de recursos que hospeda un recurso protegido, un propietario del recurso, una aplicación cliente que quiere acceder al recurso y un servidor de autorizaciones que emite tokens de acceso al recurso cuando el propietario del recurso se lo indica. En la documentación oficial de OAuth se suele a asumir un escenario en el que hay un único propietario de un recurso que concede acceso al recurso desde la aplicación cliente cada vez que esta se ejecuta. También se da por hecho que la persona que usa la aplicación cliente es el propietario del recurso. La implementación de SharePoint tiene en cuenta que un recurso de SharePoint, tal como una lista, se comparte entre varios usuarios. Además, en la implementación de SharePoint se concede acceso a la Complemento de SharePoint cuando se instala, no cada vez que se ejecuta, y otros usuarios la pueden ejecutar, siempre que no sea la persona que la instaló y le concedió el acceso. (En el caso de los complementos que no se instalan en SharePoint, pero que acceden a los recursos de SharePoint (por lo que son "Complementos de SharePoint" solo en un sentido amplio), el usuario que ejecuta el complemento no tiene que conceder permisos cada vez que se ejecuta el complemento).
  
    
    
Por lo tanto, en la implementación de SharePoint, los roles de OAuth los representan los siguientes componentes:
  
    
    

- El componente remoto de una Complemento de SharePoint hace el papel de aplicación cliente.
    
  
- Los usuarios SharePoint hacen el papel de propietario del recurso.
    
  
- SharePoint hace el papel de servidor del recurso.
    
  
- Azure ACS hace el papel de servidor de autorización cuando se usa el  [sistema de autorización de confianza baja](creating-sharepoint-add-ins-that-use-low-trust-authorization.md). Cuando se usa el  [sistema de elevada confianza](creating-sharepoint-add-ins-that-use-high-trust-authorization.md), el propio complemento (junto con un certificado digital) se convierte en el servidor de autorización.
    
  

### Los tokens de acceso no son tokens de inicio de sesión
<a name="FileName_uniquekeyword3"> </a>

Tal y como se describe anteriormente, para acceder a los recursos, un complemento tiene que solicitar un token de acceso a un servidor de autorización OAuth que se haya designado previamente como emisor de tokens de seguridad (STS) de confianza por el propietario del recurso. Por el contrario, los STS de WS-Federation y los STS de inicio de sesión pasivo del lenguaje de marcado de aserción de seguridad (SAML) están destinados principalmente a emitir tokens de inicio de sesión. En SharePoint, no se usa un STS de OAuth para emitir tokens de inicio de sesión; es decir, no se usan como proveedores de identidad. Por eso, no verá un STS de OAuth en la página de inicio de sesión del usuario, en la sección **Proveedor de autenticación** de la Administración central ni en el selector de personas de SharePoint.
  
    
    
Los administradores de SharePoint pueden usar comandos de Windows PowerShell para habilitar o deshabilitar un STS de OAuth, de forma similar a como habilitan o deshabilitan proveedores de inicio de sesión de confianza en SharePoint 2010. 
  
    
    

