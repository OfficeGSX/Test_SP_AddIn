---
title: Creación de complementos para SharePoint que usan el sistema de autorización de elevada confianza
ms.prod: SHAREPOINT
ms.assetid: 135bf6be-b118-4d2c-8d40-27f5060b06f3
---


# Creación de complementos para SharePoint que usan el sistema de autorización de elevada confianza
Conozca las Complementos de SharePoint de elevada confianza, que usan certificados digitales para establecer la confianza entre los componentes de SharePoint y los componentes remotos que obtienen acceso a SharePoint.
 **Suministrado por:**
  
    
    
Steve Peschka, Microsoft Corporation
  
    
    
Siew Moi Khor, Microsoft Corporation
  
    
    


**Vea un vídeo sobre la creación de complementos de elevada confianza para SharePoint.**

  
    
    

  
    
    
![Vídeos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## Introducción a las Complementos de SharePoint de elevada confianza
<a name="Overview"> </a>

Un complemento de elevada confianza es una Complemento de SharePoint hospedada por el proveedor que se instala en una granja local de SharePoint. No se puede instalar en Microsoft SharePoint Online ni vender en la Tienda Office. Un complemento de elevada confianza usa un certificado en lugar de un token de contexto para establecer la confianza.
  
    
    

> **NOTA**
>  En este tema le explicamos el sistema de autorización de elevada confianza para Complementos de SharePoint. Si quiere información práctica sobre la creación e implementación de complementos de elevada confianza, vea los temas siguientes:>  [Crear complementos de SharePoint de elevada confianza](create-high-trust-sharepoint-add-ins.md)>  [Empaquetar y publicar complementos de gran confianza para SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md)
  
    
    

En SharePoint 2013, el servicio de token de seguridad (STS) ofrece tokens de acceso para la autenticación de servidor a servidor. El STS habilita tokens de acceso temporales para obtener acceso a otros servicios de aplicación, como Exchange 2013, Lync 2013 y Complementos de SharePoint. Un administrador de granja establece la confianza en SharePoint y la otra aplicación o complemento usando cmdlets de Windows PowerShell y un certificado. SharePoint 2013 debe confiar en todos los certificados que se usen con el cmdlet  `New-SPTrustedRootAuthority`. Además, estos certificados se deben registrar en SharePoint como emisores de token con el cmdlet  `New-SPTrustedSecurityTokenIssuer`.
  
    
    

> **NOTA**
> El STS no está diseñado para la autenticación de usuarios, por lo que no se verá en la página de inicio de sesión de usuario, en la sección **Proveedor de autenticación** de Administración central ni en el Selector de personas de SharePoint 2013.
  
    
    


## Dos tipos de emisores de tokens
<a name="TwoKindsOfIssuers"> </a>

La aplicación web remota de una Complemento de SharePoint de elevada confianza se enlaza a un certificado digital. (Si quiere saber cómo se hace, consulte los dos temas vinculados arriba). El certificado lo usa la aplicación web remota para firmar los tokens de acceso que la aplicación incluye en todas sus solicitudes a SharePoint. La aplicación web firma el token con la clave privada del certificado y SharePoint lo valida con la clave pública del certificado. El certificado tiene que estar registrado como un emisor de tokens de confianza antes de que SharePoint pueda confiar en los tokens que emite. Hay dos tipos de emisores de tokens: algunos solo pueden emitir tokens para una determinada Complemento de SharePoint; otros, denominados agentes de confianza, pueden emitir tokens para varias Complementos de SharePoint.
  
    
    
A efectos prácticos, los administradores de la granja de SharePoint determinan qué tipo de emisor de token es creado por los valores de los modificadores y parámetros que usan con el cmdlet  `New-SPTrustedSecurityTokenIssuer`. Para crear un emisor de token que sea un agente de confianza, agregue el modificador  `-IsTrustBroker` a la línea de comandos y use un valor único (que no sea el id. de cliente del complemento) para el parámetro `-Name`. El siguiente es un ejemplo editado.
  
    
    


```

New-SPTrustedSecurityTokenIssuer -IsTrustBroker -RegisteredIssuerName "<full_token_issuer_name> " --other parameters omitted--```

Para crear un emisor de tokens que no sea agente, no se usa el modificador  `-IsTrustBroker`. Hay otra diferencia. El valor del parámetro  `-RegisteredIssuerName` siempre tiene la forma de dos GUID separados por el carácter "@"; _GUID_@ _GUID_. El GUID de la derecha siempre es el Id. del área de autenticación de la granja de SharePoint (o de la suscripción de sitio). El GUID de la izquierda es siempre un Id. específico de un emisor de tokens. Es un GUID aleatorio cuando se está creando un emisor de tokens de  *agente de confianza*  . Pero cuando se crea un emisor de tokens que no es de agente, el GUID del emisor específico debe ser el mismo GUID que se usa como Id. de cliente de la Complemento de SharePoint. Este parámetro no solo proporciona un nombre para el emisor, sino que le indica a SharePoint que Complemento de SharePoint es la única para la que el certificado puede emitir tokens. El siguiente es un ejemplo parcial:
  
    
    


```
$fullIssuerIdentifier = "<client_ID_of_SP_app> " + "@" + "<realm_GUID> "
New-SPTrustedSecurityTokenIssuer -RegisteredIssuerName $fullIssuerIdentifier --other parameters omitted--```

Normalmente, el cmdlet  `New-SPTrustedSecurityTokenIssuer` se usa en un script que hace otras tareas, como configurar SharePoint para complementos de elevada confianza. Para más información sobre estos scripts y ejemplos completos del cmdlet `New-SPTrustedSecurityTokenIssuer`, vea  [Scripts de configuración de elevada confianza para SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).
  
    
    

## Decidir entre usar uno o varios certificados para las Complementos de SharePoint de elevada confianza
<a name="Deciding"> </a>

Los administradores de red y de SharePoint pueden elegir dos estrategias básicas para obtener y administrar certificados para su uso por parte de Complementos de SharePoint de elevada confianza:
  
    
    

- Usar el mismo certificado para varias Complementos de SharePoint.
    
  
- Usar un certificado distinto para cada Complemento de SharePoint.
    
  
En esta sección se describen las ventajas y los inconvenientes de cada estrategia.
  
    
    
La ventaja de usar el mismo certificado para varias Complementos de SharePoint es que un administrador tiene menos certificados en los que confiar y que administrar. El inconveniente de esto es que, cuando se encuentre en una situación en la que desee interrumpir la confianza con una determinada Complemento de SharePoint, la única forma que tiene el administrador de interrumpir la confianza consiste en quitar el certificado como emisor de confianza o como entidad de certificación raíz. Pero al hacerlo, también se interrumpe la confianza con todas las demás Complementos de SharePoint que usen el mismo certificado, lo que hace que todas dejen de funcionar.
  
    
    
Para que la Complementos de SharePoint, aún de confianza, vuelva a funcionar, el administrador tiene que crear un objeto  `New-SPTrustedSecurityTokenIssuer` *usando un certificado nuevo*  e incluir la marca `-IsTrustBroker`. Luego, el certificado nuevo tendría que registrarse en cada servidor que hospedase cualquiera de los complementos de confianza y cada uno de estos complementos debería enlazarse al certificado nuevo.
  
    
    
La ventaja de usar un certificado por aplicación es que es más fácil interrumpir la confianza con una aplicación concreta porque los certificados que usan los complementos que siguen siendo de confianza no se ven afectados cuando el administrador interrumpe la confianza con el certificado del único complemento. El inconveniente de esto es que un administrador tiene que comprar más certificados y SharePoint debe configurarse para que los use todos, lo cual se puede hacer con un script reutilizable, tal como se ve en  [Scripts de configuración de elevada confianza para SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).
  
    
    

## Los certificados son entidades de certificación raíz en SharePoint
<a name="RootAuthorities"> </a>

Como hemos comentando al principio de este artículo, los administradores de granjas de SharePoint tienen que hacer que el certificado (de la aplicación web remota del complemento de elevada confianza) sea una entidad de certificación raíz de confianza en SharePoint, así como un emisor de token de confianza. De hecho, cuando hay una jerarquía de entidades emisoras de certificados detrás del certificado de la aplicación web, todos los certificados de la cadena se deben agregar a la lista de SharePoint de entidades de certificación raíz de confianza.
  
    
    
Todos los certificados de la cadena se agregan a la lista de SharePoint de entidades de certificación raíz de confianza con una llamada del cmdlet  `New-SPTrustedRootAuthority`. Supongamos, por ejemplo, que el certificado de la aplicación web es un certificado de dominio que lo emite una entidad de certificación de dominios de empresa de la LAN, y que su certificado, a su vez, lo emitió una entidad de certificación independiente de primer nivel de la LAN. En este escenario, los certificados de la CA de primer nivel, la CA intermedia y la aplicación web deben agregarse a la lista de SharePoint de entidades de certificación raíz de confianza. Esto se puede hacer con el siguiente código de Windows PowerShell.
  
    
    


```

$rootCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<path_to_top-level_CA's_cer_file>")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $rootCA

$intermediateCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_intermediate_CA's_cer_file")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $intermediateCA

$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_web_application's_cer_file") 
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $certificate 
```

Los certificados intermedios y raíz deben agregarse solo una vez en una granja de SharePoint. Normalmente, se agrega el certificado de la aplicación web en un script independiente que hace otras tareas de configuración, como la llamada a  `New-SPTrustedSecurityTokenIssuer`. Puede ver ejemplos en  [Scripts de configuración de elevada confianza para SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).
  
    
    
Si el certificado de la aplicación web es autofirmado, como podría ser el caso cuando el complemento se está desarrollando y depurando, no existe cadena de certificados y solo hay que agregar a la lista de entidades de certificación raíz el certificado de la aplicación web.
  
    
    
Para más información, vea la publicación del blog  [Error que indica que la raíz de la cadena de certificados no es de confianza con autenticación de notificaciones](http://blogs.technet.com/b/speschka/archive/2010/02/13/root-of-certificate-chain-not-trusted-error-with-claims-authentication.aspx). (Pase por alto la sección sobre la exportación del certificado desde los Servicios de federación de Active Directory (ADFS), dando por supuesto que el certificado se creó para los complementos de elevada confianza usando otros medios; por ejemplo, con un certificado comercial emitido por una entidad de certificación, un certificado autofirmado o un certificado emitido por el dominio).
  
    
    

## La aplicación web necesita saber que se trata de un emisor de tokens
<a name="AppIsTokenIssuer"> </a>

El componente de la aplicación web remota de la Complemento de SharePoint está enlazado a su certificado en IIS. Esto basta para los propósitos tradicionales de los certificados: identificar la aplicación web de forma segura y codificar las solicitudes y respuestas HTTP. Sin embargo, en una Complemento de SharePoint de elevada confianza, el certificado tiene la tarea adicional de ser el "emisor" oficial de tokens de acceso que la aplicación web envía a SharePoint. Para este propósito, la aplicación web tiene que conocer el Id. de emisor del certificado que se usa al registrar el certificado como emisor de tokens con SharePoint. La aplicación web obtiene este Id. de la sección **appSettings** del archivo web.config, donde hay una clave denominada **IssuerId**. Las instrucciones de cómo el desarrollador del complemento establece este valor y cómo se enlaza el certificado a la aplicación web en IIS están en  [Empaquetar y publicar complementos de gran confianza para SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md). Si el cliente usa la estrategia de tener un certificado distinto para cada Complemento de SharePoint de elevada confianza, el valor **ClientId** también se usa como valor **IssuerId**. Este no es el caso cuando varias aplicaciones comparten el mismo certificado, ya que cada Complemento de SharePoint debe tener su propio Id. de cliente único, pero el Id. de emisor es el identificador para un objeto **SPTrustedSecurityTokenIssuer**.
  
    
    
A continuación, verá un ejemplo de una sección **appSettings** para una Complemento de SharePoint de elevada confianza. En este ejemplo, varios complementos comparten un certificado, de modo que el **IssuerId** no es el mismo que el **ClientId**. Observe que no hay ninguna clave **ClientSecret** en una Complemento de SharePoint de elevada confianza.
  
    
    


```XML

<appSettings>
  <add key="ClientId" value="6569a7e8-3670-4669-91ae-ec6819ab461" />
  <add key="ClientSigningCertificatePath" value="C:\\MyCerts\\HighTrustCert.pfx" />
  <add key="ClientSigningCertificatePassword" value="3VeryComplexPa$$word82" />
  <add key="IssuerId" value="e9134021-0180-4b05-9e7e-0a9e5a524965" />
</appSettings>
```


> **NOTA DE SEGURIDAD**
> El ejemplo precedente asume que el certificado se almacena en el sistema de archivos. Esto resulta aceptable para el desarrollo y la depuración. En una Complemento de SharePoint de producción de elevada confianza, el certificado suele guardarse en al almacén de certificados de Windows, y las claves **ClientSigningCertificatePath** y **ClientSigningCertificatePassword** suelen sustituirse por una clave **ClientSigningCertificateSerialNumber**. 
  
    
    


## Responsabilidades del personal de TI en el sistema de elevada confianza
<a name="ITPro"> </a>

Los desarrolladores deberán comprender los requisitos para la seguridad de las aplicaciones tal como se han descrito anteriormente, pero los profesionales de TI serán los encargados de implementar la infraestructura necesaria para todo ello. Los profesionales de TI deben planear los siguientes requisitos operativos:
  
    
    

- Crear o comprar uno o más certificados que se usarán para las Complementos de SharePoint de confianza.
    
  
- Asegúrese de que los certificados se almacenan de manera segura en los servidores de aplicaciones web. Con el sistema operativo Windows, esto significa guardar los certificados en el almacén de certificados de Windows.
    
  
- Administrar la distribución de dichos certificados a los desarrolladores que están compilando las Complementos de SharePoint.
    
  
- Realice un seguimiento de dónde se distribuye cada certificado, tanto de los complementos que lo usan como de los desarrolladores que han recibido una copia. Si tiene que revocar un certificado, el personal de TI deberá preparar con cada desarrollador la transición administrada a un nuevo certificado.
    
  

## Recursos adicionales
<a name="AR"> </a>


-  [Crear complementos de SharePoint de elevada confianza](create-high-trust-sharepoint-add-ins.md)
    
  
-  [Empaquetar y publicar complementos de gran confianza para SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md)
    
  
-  [Sugerencias de solución de problemas para complementos de elevada confianza en SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)
    
  
-  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  

