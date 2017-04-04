---
title: Implementación e instalación de aplicaciones para SharePoint métodos y opciones
ms.prod: SHAREPOINT
ms.assetid: d15a74a7-3c10-485a-9885-7ef11aaa0d90
---


# Implementación e instalación de aplicaciones para SharePoint: métodos y opciones
Aprenda sobre los métodos para publicar, instalar y desinstalar una Complemento de SharePoint.
## Publicación en la Tienda Office o el catálogo de complementos de una organización
<a name="MarketOrCatalog"> </a>

Puede cargar su Complemento de SharePoint en la Tienda Office pública o en un catálogo de complementos privado de una organización. Un catálogo de complementos privado es una colección de sitios dedicada en una SharePoint 2013 aplicación web (o un arrendamiento SharePoint Online) que aloja bibliotecas de documentos para Complementos de SharePoint y Complementos de Office. Colocar el catálogo en su propia colección de sitios facilita al administrador de la aplicación web o de inquilinos limitar los permisos de acceso. 
  
    
    
Si el complemento se carga en la Tienda Office, Microsoft ejecuta algunas comprobaciones de validación. Por ejemplo, comprueba si el marcado del manifiesto del complemento es válido, y verifica que cualquier paquete de soluciones de SharePoint incluido (archivos .wsp) no contenga elementos no permitidos o Características con un ámbito mayor que **Web**. El contenido del paquete también se inspecciona en busca de contenido inapropiado. Si el complemento supera todas las pruebas, el paquete se convierte en un archivo y Microsoft lo firma. 
  
    
    

> **NOTA**
> Cuando desarrolla su complemento y lo implementa con Microsoft Office Developer Tools para Visual Studio, el complemento se instala directamente en el sitio de prueba de SharePoint de destino. Como no pasa a través de la Tienda Office, el proceso de validación anterior no tiene lugar. 
  
    
    

Cargar una Complemento de SharePoint al catálogo de complementos de una organización es tan fácil como cargar cualquier archivo en una biblioteca de documentos de SharePoint Foundation. Complete un formulario emergente en el que debe proporcionar la URL local del paquete del complemento y otra información, como el nombre del complemento. Cuando se carga el complemento al catálogo de complementos de una organización, se hacen comprobaciones similares y los complementos que no superan las pruebas se marcan como no válidas o se desactivan en el catálogo. 
  
    
    
Los administradores de inquilinos y de aplicaciones web SharePoint 2013 pueden comprar la Complementos de SharePoint en la Tienda Office. Para abrir la Tienda Office, el administrador selecciona **Agregar un complemento** en la página **Contenidos del sitio** y luego elige **Tienda SharePoint** en la página **Sus complementos**. Esto abre una página de la **Tienda SharePoint** en la que los administradores pueden explorar y descubrir las Complementos de SharePoint ofrecidas. (También puede hacerse desde office.com.) Los complementos con requisitos previos que no están instalados en la aplicación web o el arrendamiento del administrador aparecen apagados y no están disponibles. Por ejemplo, si un complemento requiere Servicios Buscar y éstos no están instalados, el complemento aparece apagado. Los administradores pueden ordenar, filtrar y explorar la lista de complementos y leer información y reseñas sobre ellos, además de comprar licencias.
  
    
    
Cuando un administrador decide comprar una licencia, debe aceptar los términos y condiciones de compra y los permisos que el complemento debe tener para funcionar, como el acceso de lectura a las listas o el control completo de acceso a la colección de sitios. 
  
    
    
Cuando se compra una o más licencias para un complemento, éstas se descargan en la aplicación o en el arrendamiento web. El complemento no se descarga e instala automáticamente cuando se compra la licencia, aunque los administradores tienen la opción de combinar la instalación con la compra de la licencia.
  
    
    
Los usuarios instalan complementos de la página **Sus complementos**, en la que se encuentra una lista combinada de lo siguiente:
  
    
    

- Complementos de SharePoint del catálogo de complementos de la organización de la aplicación (o del inquilino) web.
    
  
- Las Complementos de SharePoint de la Tienda Office para las que la organización o arrendamiento ya dispone de una licencia de sitio o una licencia asignada al usuario.
    
  
Se muestran todas los complementos que el usuario puede instalar inmediatamente, y con unas pocas excepciones se muestran solo los complementos que el usuario puede instalar inmediatamente. Los usuarios pueden filtrar los complementos de la página para que incluyan solo los complementos del catálogo de complementos de la organización. Cuando se instala un complemento, aparece en la lista de complementos de la página **Contenidos del sitio** del sitio web en el que se instala.
  
    
    

## Cómo instalar complementos para SharePoint
<a name="Installing"> </a>

Los propietarios de sitios web instalan Complementos de SharePoint de la página **Sus complementos**, tal y como se ha descrito en este tema. La instalación crea una instancia del complemento. Para obtener más información sobre la instalación de complementos, consulte  [Agregar complementos para SharePoint a un sitio de SharePoint 2013](https://technet.microsoft.com/es-es/library/fp161231.aspx). 
  
    
    

> **NOTA**
> En ocasiones, una pérdida temporal de la conexión a la red puede bloquear la instalación. Si una instalación no se realiza correctamente por algún motivo, la infraestructura de la instalación reintentará la operación tres veces. Si no lo consigue, aparecerá un aviso de la falla en la IU. Los usuarios pueden reintentar hacer la instalación más adelante. 
  
    
    


## Cómo desinstalar complementos para SharePoint
<a name="Uninstalling"> </a>

Los propietarios de sitios web pueden desinstalar una instancia de una Complemento de SharePoint mediante la interfaz de SharePoint. La desinstalación de una instancia de una Complementos de SharePoint es limpia. Eso significa que se quita todo lo que se instaló con el complemento. 
  
    
    
Sin embargo, no se quitan los componentes que un complemento usa pero que se instalan separadamente. Por ejemplo, suponga que un complemento tiene una página web remota con botones que crean listas en la web de host. Desinstalar el complemento elimina su mosaico de la página **Contenidos del sitio**, lo que a su vez hace que la página remota sea inaccesible o inutilizable para los usuarios finales, pero no elimina las listas que se crearon con el complemento. SharePoint no guarda un registro de qué listas en la web de host fueron creadas con el complemento y cuáles las crearon los usuarios con la interfaz de SharePoint, de modo que no se pueden borrar las listas creadas con el complemento. En general, este es el comportamiento deseable porque las listas podrían contener datos que sigan siendo útiles para los usuarios, aun después de eliminarse el complemento con que se crearon.
  
    
    
Si la Complemento de SharePoint incluye una web de complemento, ésta se elimina. Esto produce una desinstalación más limpia que desactivar sistemáticamente características e invertir la implementación del archivo interno .wsp del complemento.
  
    
    

> **NOTA**
> Cuando un usuario elimina un complemento, este se mueve a la Papelera de reciclaje de primera instancia. Si se borra de aquí, se mueve a la Papelera de reciclaje de segunda instancia. Si se borra de esta segunda Papelera, el complemento queda desinstalado por completo y no es posible restaurarlo. 
  
    
    

Los permisos del complemento también se revocan con la eliminación (reciclaje), de acuerdo con las siguientes reglas:
  
    
    

- Los permisos de ámbito de web siempre se revocan.
    
  
- Si no existe ninguna otra instancia del complemento en la colección de sitios, también se revocarán los permisos del ámbito de la colección.
    
  
- Si no existe ninguna otra instancia del complemento en la suscripción de sitios (arrendamiento), también se revocarán los permisos del ámbito del arrendamiento.
    
  
El servicio web  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx), si se registró en el manifiesto del complemento, se ejecuta al comienzo del proceso de desinstalación (lo cual se produce al eliminar el complemento de la Papelera de reciclaje de segundo nivel). Se recomienda tener un servicio web  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) si tiene un servicio web [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx)y diseñar el servicio  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) para revertir los cambios realizados en el servicio [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx). Para obtener más información, vea  [Controlar eventos en los complementos de SharePoint](handle-events-in-sharepoint-add-ins.md).
  
    
    

## Recursos adicionales
<a name="SP15deployinstallapps_addlresources"> </a>


-  [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Proceso de actualización de complementos para SharePoint](sharepoint-add-ins-update-process.md)
    
  

