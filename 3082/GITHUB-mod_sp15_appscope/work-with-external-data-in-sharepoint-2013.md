---
title: Trabajar con datos externos en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# Trabajar con datos externos en SharePoint 2013
Encuentre recursos e información para obtener acceso a datos externos y manipularlos con JavaScript en páginas de Complementos de SharePoint.
## Usar JavaScript en Complementos de SharePoint
<a name="SP15Workdata_Working"> </a>

Con frecuencia, en los Complementos de SharePoint, tendrá que recuperar y manipular los datos que expone un servicio o una aplicación web remota desde dentro de un componente o de una página de SharePoint. Dado que en los servidores de SharePoint no se permite el código personalizado, el complemento debe usar JavaScript a tal fin. El modelo para aplicaciones para SharePoint proporciona varias opciones para acceder a los servicios y los datos remotos.




### Usar la biblioteca JavaScript entre dominios de SharePoint para obtener acceso a datos externos

Puede usar la biblioteca entre dominios para obtener acceso a datos en su aplicación web remota si proporciona una página proxy personalizada hospedada en la infraestructura remota. Como desarrollador, es responsable de la implementación de la página personalizada del proxy y tiene que usar lógica personalizada, como el mecanismo de autenticación (si existe) de la aplicación remota. Use la biblioteca entre dominios si quiere que la comunicación entre el origen de datos remotos y la página de SharePoint se produzca a nivel de cliente.



Para más información sobre cómo usar la biblioteca de esta manera, vea  [Crear una página de proxy personalizada para la biblioteca entre dominios en SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).




> **NOTA**
> La biblioteca entre dominios de SharePoint también se puede usar en otra dirección; es decir, JavaScript en páginas remotas puede usarla para acceder a los datos de SharePoint. Para más información sobre este uso de la biblioteca, consulte  [Crear aplicaciones para SharePoint que usen la biblioteca entre dominios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md). 





### Usar el proxy web de SharePoint para obtener acceso a datos externos

Puede usar el proxy web expuesto en el modelo de objetos de cliente de JavaScript para obtener acceso a datos remotos. (El proxy también está disponible en el modelo de objetos de cliente [CSOM] .de NET, pero este modelo de objetos no se puede usar en un código que se ejecute en los servidores de SharePoint). Cuando usa el proxy web, emite la solicitud inicial a SharePoint. A su vez, SharePoint solicita los datos al extremo especificado y reenvía la respuesta a su página. Use el proxy web cuando quiera que la comunicación entre el origen de datos remotos y la página de SharePoint ocurra a nivel de servidor.



Para más información sobre cómo usar el servidor proxy, vea  [Consultar un servicio remoto mediante el proxy web en SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).




## Recursos adicionales
<a name="SP15Workdata_AddRes"> </a>


-  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)



