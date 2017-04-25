---
title: Publicar aplicaciones para SharePoint
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# Publicar aplicaciones para SharePoint
Encuentre artículos y recursos que le ayudarán a publicar las Complementos de SharePoint.
## Introducción a la publicación de complementos
<a name="bk_gettingstarted"> </a>

Ahora que ha acabado de desarrollar el Complemento de SharePoint, el último paso consiste en ponerlo a disposición de los usuarios. Realice esta operación publicando el complemento en una de las dos ubicaciones siguientes:




- **La Tienda Office pública.** Publique el complemento en la Tienda Office para que esté disponible para el público y los usuarios de cualquier implementación de SharePoint lo puedan comprar.


- **Un catálogo de complementos de la organización interna.** Publique los complementos en un catálogo de complementos interno de la organización, hospedado en la implementación de SharePoint, para ponerlas a disposición de los usuarios que tengan acceso a esa implementación de SharePoint.


Para obtener información acerca de cómo empaquetar el complemento para su publicación usando Visual Studio 2012, vea  [Publicar complementos para SharePoint con Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md).




### Publicación en la Tienda Office

Para publicar un complemento en la Tienda Office, primero debe  [registrarse como desarrollador de Microsoft](https://sellerdashboard.microsoft.com/Registration). 



Cuando cargue el complemento en la Tienda Office para publicarlo, Microsoft llevará a cabo una serie de comprobaciones para asegurarse de que el complemento cumple las instrucciones de contenido y comportamiento de los complementos. Por ejemplo, comprobará si el marcado de manifiesto del complemento es válido y completo y verificará que los paquetes de la solución de SharePoint (archivos .wsp) que están incluidos en el complemento no contengan elementos que no estén permitidos, o características de SharePoint con un ámbito más amplio que la Web. También inspeccionará el paquete para detectar un posible contenido cuestionable. Si el paquete del complemento pasa todas las pruebas, se incluirá en un archivo y Microsoft lo firmará.



Cuando cargue el complemento para publicarlo en la Tienda Office, puede elegir los términos de la licencia que desea ofrecer a los usuarios cuando lo descarguen. Use esta licencia de complemento para decidir:




- Si ofrece el complemento de forma gratuita, en versión de prueba o para comprar.


- Si el complemento se puede comprar por usuario o por sitio.


SharePoint no aplica términos de licencia al uso de los complementos, sino que proporciona un marco de licencia que permite incluir lógica de código en el complemento para aplicar las restricciones de licencia que elija. Por ejemplo, puede incluir lógica de código en el complemento que permite a los usuarios obtener acceso a determinadas características del complemento si tienen una licencia pagada, o no obtenerlo si tienen una licencia de prueba. Para obtener más información, vea  [Licencias de complementos de Office y SharePoint](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx).




### Publicación en un catálogo de complementos

Si crea Complementos de SharePoint para que las use su propia empresa o un cliente corporativo específico, en vez de destinarlas al público en general, probablemente deseará publicar el complemento en un catálogo de complementos internos hospedado en SharePoint. Un catálogo de complementos privados es una colección de sitios exclusivo de una aplicación web de SharePoint 2013 (o un arrendamiento de SharePoint Online) que hospeda bibliotecas de documentos para las Complementos de SharePoint y las Complementos de Office. Si pone el catálogo en su propia colección de sitios facilita al administrador de aplicaciones web o al administrador de arrendamientos limitar los permisos sobre el catálogo.



Cargar una Complemento de SharePoint en un catálogo de complementos corporativos es tan fácil como cargar cualquier archivo en una biblioteca de documentos de SharePoint. Solo tiene que rellenar un formulario emergente en el que se suministra la dirección URL local del paquete del complemento y demás información como, por ejemplo, el nombre del complemento. Cuando se carga un complemento en un catálogo de complementos, se efectúan comprobaciones similares y los complementos que no pasen se marcarán como no válidos o deshabilitados en el catálogo.




## Adopción de la decisión de dónde publicar la Complemento de SharePoint
<a name="bk_decide"> </a>

En la tabla siguiente se establece una comparación entre qué significa publicar en la Tienda Office o en un catálogo de complementos, y se indican los problemas que deberán tenerse en cuenta a la hora de decidir dónde publicar el complemento. Le recomendamos que decida dónde publicará el complemento antes de diseñarlo y publicarlo; en algunos casos, como la generación de licencias, la ubicación del complemento repercutirá en el diseño y desarrollo del mismo.




**Tabla 1. Consideraciones sobre dónde publicar el complemento**


|**Tienda Office**|**Catálogo de complementos**|
|:-----|:-----|
|El complemento estará disponible para el público en general.  <br/> |El complemento estará disponible para los usuarios que tengan acceso a esta implementación de SharePoint  <br/> |
|Marco de generación de licencias disponible.  <br/> |No hay ningún marco de generación de licencias disponible para usarlo.  <br/> |
|Microsoft comprueba que el paquete del complemento cumpla las directivas técnicas y de contenido.  <br/> |SharePoint comprueba el paquete del complemento cuando éste se carga.  <br/> |
|Debe haberse registrado en el Panel de venta de Microsoft para cargar complementos.  <br/> |No necesita registrarse en Microsoft.  <br/> |
 

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Crear o actualizar identificadores y claves secretas de cliente en el Panel de vendedores](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)


-  [Usar el panel de vendedores para enviar complementos de Office y SharePoint y aplicaciones de Office 365 a la Tienda Office](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)


-  [Directivas de validación para aplicaciones enviadas a la Tienda Office (versión 2.0)](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)


-  [Empezar a crear aplicaciones para Office y SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)


-  [Licencias de complementos de Office y SharePoint](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)


-  [Implementación e instalación de aplicaciones para SharePoint: métodos y opciones](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [Arrendamientos y ámbitos de implementación de los complementos para SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)


-  [Publicar complementos para SharePoint con Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md)


-  [Publicación de complementos para la Tienda de Office ](http://social.msdn.microsoft.com/Forums/es-es/officestore)



