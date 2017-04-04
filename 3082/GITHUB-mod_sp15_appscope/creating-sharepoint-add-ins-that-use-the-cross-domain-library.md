---
title: Crear aplicaciones para SharePoint que usen la biblioteca entre dominios
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# Crear aplicaciones para SharePoint que usen la biblioteca entre dominios
Infórmese sobre la biblioteca SharePoint entre dominios de JavaScript.
En algunos escenarios, un Complemento de SharePoint no podrá usar el sistema de autorización de confianza baja ni el de autorización de elevada confianza; o es posible que no sea buena idea usarlos como el único medio para que el complemento obtenga autorización para los recursos de SharePoint. Ejemplos:
  
    
    


- Los componentes remotos del Complemento de SharePoint no son locales, pero un firewall corporativo bloquea las comunicaciones de servidor a servidor entre SharePoint y ACS, lo cual impide usar el sistema de confianza baja.
    
  
- El Complemento de SharePoint se ha diseñado como aplicación web de página única que emplea JavaScript del lado cliente para las operaciones de datos con SharePoint.
    
  
- El Complemento de SharePoint emplea, principalmente, llamadas de servidor a servidor para obtener acceso a los datos de SharePoint (y recibe la autorización de los sistemas de confianza baja o de elevada confianza), pero necesita complementarse con algunas llamadas de JavaScript. Por ejemplo, una página con gran cantidad de gráficos puede usar JavaScript para hacer pequeñas actualizaciones en los datos mostrados sin tener que volver a cargar toda la página.
    
  

Sin embargo,  [por razones de seguridad](http://msdn.microsoft.com/es-es/library%28d=robot%29/cc709423(d=robot,l=es-es,v=vs.85).aspx), los exploradores no permiten que el JavaScript hospedado en un dominio tenga acceso a los recursos de otro dominio, por lo que se necesita una técnica especial para permitir que el JavaScript remoto obtenga acceso a los recursos de SharePoint. La biblioteca JavaScript entre dominios de SharePoint facilita el uso de la técnica por parte de su aplicación web remota.
  
    
    


> **NOTA**
> La biblioteca entre dominios también se usa para obtener acceso a los datos en la dirección contraria; es decir, para permitir que el JavaScript de una página de SharePoint tenga acceso a los datos de un dominio remoto. Vea  [Acceso remoto a los datos de una página de SharePoint](#ReverseDirection) para más información.
  
    
    


## La arquitectura de la biblioteca entre dominios

La biblioteca entre dominios de SharePoint está contenida en el archivo SP.RequestExecutor.js, que se encuentra en la carpeta virtual /_layouts/15/ de todos los sitios web de SharePoint. Los scripts de este archivo encapsulan una técnica segura y conocida para solucionar la restricción del explorador impuesta en el scripting entre dominios: un iFrame puede comunicarse con su página primaria a través de la función  `window.postMessage()` aunque la página del iFrame esté en otro dominio. Las solicitudes de datos y las respuestas se transmiten a través del límite del dominio haciendo llamadas a `postMessage()`.
  
    
    

> **PRECAUCIóN**
> La función  `postMessage()` solo sirve en exploradores compatibles con HTML 5, por lo que los Complementos de SharePoint que usen la biblioteca entre dominios no funcionarán en los exploradores antiguos.
  
    
    

En SharePoint, la biblioteca entre dominios se carga en una página de la aplicación web remota donde crea un iFrame oculto que hospeda una página de proxy especial del dominio SharePoint. La página de proxy ya existe en todos los sitios web de SharePoint. La biblioteca se usa para crear un objeto de Notación de objetos de JavaScript (JSON) que contiene toda la información necesaria para hacer una llamada CRUD a las API de REST de SharePoint. El objeto de JSON se pasa a la página de proxy usando  `postMessage()`. En la página de proxy, donde la biblioteca también está cargada, el objeto de JSON se analiza y reconstruye como llamada de REST a SharePoint. Como la página de proxy está en el dominio de SharePoint, el explorador permite la llamada.
  
    
    
Los componentes remotos del Complemento de SharePoint seguirán teniendo que recibir autorización para obtener acceso a los recursos de SharePoint. Esto se puede hacer de dos formas:
  
    
    

- Establecer el tipo de la entidad de seguridad del complemento en **RemoteWebApplication** (el valor predeterminado para las aplicaciones hospedadas por el proveedor) en el manifiesto de complemento. Cuando se registra el complemento con ACS, el registro incluye el dominio de la aplicación web remota. SharePoint confía en los dominios registrados con ACS, aunque en este escenario no usa ninguno de los flujos de transmisión de tokens que forman parte del sistema de confianza baja del lado servidor. Para más información sobre el registro de complementos, vea [Registrar complementos de SharePoint 2013](register-sharepoint-add-ins-2013.md). 
    
  
- En un complemento hospedado en SharePoint, puede dejar el tipo de entidad de seguridad del complemento establecido en el valor predeterminado, que es **Internal**. Luego establezca el atributo **AllowedRemoteHostUrl** del elemento **Internal** en la dirección URL de la aplicación web remota, como en el ejemplo siguiente.
    
```
<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>
```


> **NOTA**
> Si usa la segunda opción (una entidad de seguridad de complemento **Internal**), solo puede usar JavaScript y la biblioteca entre dominios para obtener acceso a SharePoint. El modelo de objetos de cliente de SharePoint está bloqueado para los Complementos de SharePoint de tipo **Internal**, de modo que no puede tener un sistema de autorización doble que use a la vez la biblioteca entre dominios y los sistemas de confianza baja o de elevada confianza. 
  
    
    

Para obtener más información sobre cómo usar la biblioteca, vea  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Acceso remoto a los datos de una página de SharePoint
<a name="ReverseDirection"> </a>

La biblioteca entre dominios de SharePoint también se puede usar en la dirección contraria; es decir, JavaScript en una página de SharePoint puede usar la biblioteca para obtener datos de los componentes remotos del complemento. Para ello, hay que invertir la arquitectura entre dominios creando una página de proxy en la aplicación web remota. Se llama a la biblioteca desde una página de SharePoint donde crea un iFrame para hospedar la página de proxy. Si quiere más detalles sobre cómo usar la biblioteca de esta forma, vea  [Crear una página de proxy personalizada para la biblioteca entre dominios en SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

## En esta sección
<a name="ReverseDirection"> </a>


-  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [Trabajar con la biblioteca entre dominios en diferentes zonas de seguridad de Internet Explorer en los complementos para SharePoint](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  

## Recursos adicionales
<a name="ReverseDirection"> </a>


-  [Resolver problemas entre dominios en los complementos para SharePoint](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)
    
  

