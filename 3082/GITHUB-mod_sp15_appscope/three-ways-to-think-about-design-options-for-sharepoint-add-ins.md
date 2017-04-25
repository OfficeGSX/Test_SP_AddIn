---
title: Tres formas de concebir las opciones de diseño de complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# Tres formas de concebir las opciones de diseño de complementos para SharePoint
Información general sobre las opciones de diseño y arquitectura disponibles con Complementos de SharePoint. Primero debe estar familiarizado con el artículo  [Complementos de SharePoint](sharepoint-add-ins.md).
## Información general y primera decisión
<a name="Overview"> </a>

En este artículo se examinan las opciones arquitectónicas de las Complementos de SharePoint de tres formas diferentes. En primer lugar, obtendrá más información sobre las categorías de opciones de diseño más importantes. En segundo lugar, verá la arquitectura del complemento en términos de capas de la aplicación. En tercer lugar, verá un conjunto de factores que debe tener en cuenta al crear sus opciones de diseño.



Pero la primera decisión que debe tomar es si su extensión de SharePoint debe ser una Complemento de SharePoint, solución de granja de servidores de SharePoint clásica o solución de espacio aislado. Algunas partes del modelo de objetos de SharePoint, principalmente las relacionadas con la seguridad y la personalización de administración de SharePoint, no son accesibles desde los clientes. Solo el código personalizado que se ejecuta en el servidor de SharePoint pueda obtener acceso a ellos y no se le permite al código de servidor personalizado en una Complemento de SharePoint (un amplio conjunto de modelos de objetos de cliente y un servicio REST/OData permiten a las Complementos de SharePoint crear casi cualquier extensión de SharePoint orientada al usuario final). Para obtener más información sobre la decisión entre complementos y soluciones clásicas, vea  [Complementos para SharePoint comparadas con las soluciones de SharePoint](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx).  [Elegir el conjunto de API correcto en SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx) también es útil para tomar esta decisión.




## Elementos clave en el diseño de complementos para SharePoint
<a name="MajorCategoriesOfChoices"> </a>

Existen tres categorías principales de opciones que es necesario crear al diseñar una Complemento de SharePoint. Como siempre, el diseño de aplicaciones implica ventajas y desventajas; las opciones que cree en una categoría pueden limitar sus opciones en otra. Tenga en cuenta que no todas las combinaciones de opciones posibles son factibles.




- **Hospedaje:** puede ser útil dividir las Complementos de SharePoint en dos grandes tipos basándose en cómo se implementan y hospedan.

  - Los complementos **hospedados en proveedor** tienen su almacenamiento de datos y su lógica empresarial principal que el desarrollador implementó y hospedó fuera de SharePoint en servidores o en una cuenta de la nube que proporciona el desarrollador. Él es responsable de exigir el aislamiento entre las cuentas de los diversos clientes que compren el complemento. Los complementos también pueden tener componentes de SharePoint. Estos se hospedan en la granja de servidores de SharePoint del cliente. Este tipo de complemento es el que proporciona mayor flexibilidad en las otras categorías de opciones de diseño. También permite usar plataformas que no son de Microsoft para los datos externos, la lógica y la interfaz de usuario (IU) web. Dentro de la categoría de complementos hospedados en proveedor, también necesita distinguir entre complementos cuyos componentes remotos están dentro del mismo firewall corporativo, como la granja de servidores de SharePoint, y aquellos cuyos componentes remotos están fuera de dicho firewall. Los sistemas de autorización de estos dos escenarios son diferentes, lo que, a su vez, crea una diferencia en el lenguaje de programación que se usa para obtener acceso a los datos de SharePoint.


  - Los complementos **hospedados en SharePoint** se componen por completo de componentes de SharePoint 2013, como listas, tipos de contenido, flujos de trabajo y elementos web. No existen componentes externos. Para obtener más información sobre los tipos de componentes de SharePoint que se pueden incluir en las Complementos de SharePoint, vea [Hospedar webs, webs de complementos y componentes de SharePoint en SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).



    Para obtener más información sobre las opciones de hospedaje de Complementos de SharePoint, vea  [Elegir patrones para desarrollar y hospedar un complemento para SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).


- **Conectividad:** SharePoint 2013 admite tres tipos de acceso seguro para crear, leer, actualizar o eliminar (CRUD, por sus siglas en inglés) datos.

  - Las aplicaciones web externas en un complemento usan el protocolo de OAuth para obtener acceso a datos de SharePoint. Para obtener más información, vea  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md).


  - JavaScript puede obtener acceso a datos en una web de complemento de SharePoint y en otros sitios web dentro del arrendamiento de la misma con una biblioteca especial de JavaScript que habilita la creación segura de scripts entre dominios. Para más información, vea  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).


  - Una Complemento de SharePoint también puede obtener acceso a datos externos con Servicios de conectividad empresarial (BCS) o un proxy de servicio web. Para obtener más información, vea  [Servicios de conectividad empresarial de SharePoint 2013](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx) y [Consultar un servicio remoto mediante el proxy web en SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).



    Para obtener más información sobre el acceso y el almacenamiento de datos en Complementos de SharePoint, vea  [Almacenamiento de datos en complementos de SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data),  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) y [Trabajar con datos externos en SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).


- **Interfaz de usuario:** hay tres formas de exponer una Complemento de SharePoint en SharePoint; como mínimo, todos los complementos se exponen en una página web completa. Opcionalmente, también se puede exponer un complemento a través de una parte del complemento y un botón de cinta o un elemento de menú. Para más información, vea [Diseño de la experiencia de usuario para aplicaciones en SharePoint 2013](ux-design-for-sharepoint-add-ins.md).

    > **NOTA**
      > Sus clientes pueden instalar las Complementos de SharePoint en varias colecciones de sitios de un arrendamiento o sobre la base de sitios web individuales. Antes se denominaban complementos de ámbito de inquilino. Si desea que sus clientes tengan la opción de ámbito de inquilino, no puede incluir un botón de cinta personalizado o una parte del complemento. Para más información, vea  [Arrendamientos y ámbitos de implementación de los complementos para SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md). 

## Capas arquitectónicas
<a name="Tiers"> </a>

Otra forma de pensar en sus opciones de arquitectura del complemento es pensar que el complemento tiene tres niveles lógicos: la interfaz de usuario, la lógica empresarial y el acceso a datos. Cada capa tiene múltiples opciones de implementación; otra vez, las opciones que elija para una capa limitarán las opciones para las demás. Las siguientes tablas describen algunas opciones, y sus usos, de los componentes de un complemento remoto y los componentes de SharePoint.




**Componentes remotos en complementos hospedados en proveedor: opciones de cada capa**


|**Capa**|**Opciones**|**Bueno para**|
|:-----|:-----|:-----|
|Interfaz de usuario  <br/> |Páginas ASP.NET en una aplicación MVC o formularios ASP.NET hospedados en un rol web Microsoft Azure.  <br/> |Aprovechar las habilidades del personal de desarrollo de ASP.NET.  <br/> |
||Página HTML 5 con JavaScript.  <br/> |Interfaz de usuario enriquecida.  <br/> |
||PHP u otro tipo de página web que se puede hospedar en un servicio de nube que no es de Microsoft.  <br/> |Integración de aplicaciones que no son de Microsoft en SharePoint  <br/> |
||Silverlight en una aplicación de Windows Phone.  <br/> |Acceso móvil a datos de SharePoint e integración con notificaciones de inserción y datos de geolocalización.  <br/> |
|Lógica empresarial  <br/> |JavaScript del lado cliente.  <br/> |Lógica de la interfaz de usuario y lógica empresarial ligera.  <br/> Acceso a datos de SharePoint con el modelo de objetos de cliente JavaScript.  <br/> |
||Un rol de trabajador de Microsoft Azure.  <br/> |Funciones intensivas de procesador.  <br/> Acceso a datos de SharePoint con el modelo de objetos de cliente .NET Framework.  <br/> |
||Un servicio web remoto.  <br/> |Funciones intensivas de procesador.  <br/> Acceso a datos de SharePoint con el modelo de objetos de cliente .NET Framework.  <br/> |
|Datos  <br/> |SQL Azure.  <br/> |Datos relacionales completos.  <br/> |
||Almacenamiento de tablas de Microsoft Azure.  <br/> |Configuración de la aplicación y otros metadatos.  <br/> |
||Almacenamiento de blob de Microsoft Azure.  <br/> |Almacenamiento de archivos grandes.  <br/> |
||Servicio de nube que no es de Microsoft.  <br/> |Aprovechamiento de orígenes de datos existentes que se basan en plataformas que no son de Microsoft.  <br/> |
||Una base de datos en el propio servidor del desarrollador.  <br/> |Control del proveedor y el hospedaje en proveedor del aislamiento de arrendamiento.  <br/> |
 

**Componentes de SharePoint: opciones de cada capa**


|**Capa**|**Opciones**|**Bueno para**|
|:-----|:-----|:-----|
|Interfaz de usuario  <br/> |Vistas personalizadas de bibliotecas y listas de SharePoint 2013 en páginas de web de complemento.  <br/> |Maximización de la integración con la apariencia y el comportamiento de SharePoint.  <br/> |
||Aplicación Silverlight hospedada en un elemento web (o dentro de etiquetas <objeto>) en una página de web de complemento.  <br/> |Aprovechamiento de la experiencia de desarrollo de Silverlight existente.  <br/> Interfaz de usuario enriquecida.  <br/> |
|Lógica empresarial  <br/> |Un flujo de trabajo de SharePoint.  <br/> |Implementación de procesos empresariales.  <br/> |
||JavaScript del lado cliente complementado con la biblioteca entre dominios de SharePoint.  <br/> |Acceso a datos de SharePoint en la web de complemento.  <br/> Acceso a datos en otros sitios web dentro del arrendamiento.  <br/> |
||Un controlador de eventos remoto.  <br/> |Administrar eventos de CRUD en listas y bibliotecas de SharePoint con lógica hospedada externamente.  <br/> |
|Datos  <br/> |Bibliotecas y listas de SharePoint 2013 que se consultan con Lenguaje de marcado de aplicaciones de colaboración (CAML) o LINQ, consultas con uno de los modelos de objetos de cliente de SharePoint.  <br/> |Aprovechamiento de la experiencia de desarrollo de SharePoint y .NET Framework existente.  <br/> |
||Bibliotecas y listas de SharePoint 2013 que se consultan con el servicio web REST/OData de SharePoint.  <br/> |Acceso a datos de SharePoint desde plataformas que no son de Microsoft.  <br/> Aprovechamiento de la experiencia de consulta de OData existente.  <br/> |
||Un modelo BCS.  <br/> |Exposición de datos externos en SharePoint como una lista de SharePoint.  <br/> |
 

## Factores a considerar al tomar decisiones de diseño
<a name="DecisionFactors"> </a>

El modelo de Complemento de SharePoint permite muchas más posibilidades de diseño que no son posibles con un árbol de decisión simple. Estos son algunos de los factores más importantes que se deben tener en cuenta al construir la arquitectura de una Complemento de SharePoint.




- Por supuesto, lo más importante son las funciones que desea que estén disponibles a los clientes, los casos de uso. Por ejemplo, si su complemento incluye funciones intensivas de procesador, como convertir archivos de vídeo a otro formato de vídeo, este sería un argumento para crear un complemento hospedado en proveedor en el que se realiza el procesamiento en uno de sus servidores o en un rol de trabajador de Microsoft Azure.


- Ya que un tipo de Complemento de SharePoint, los complementos hospedados en proveedor, necesitan que usted (o su cliente) hospeden los componentes que no son de SharePoint y hagan cumplir el aislamiento del inquilino. Para ello es preciso que tenga en cuenta si dispone del hardware y del personal de TI para hacerlo (o si lo harán sus clientes de destino).


- Cuáles serán los clientes de destino también es una consideración fundamental. Si usa todos sus complementos internamente (es decir, no tiene clientes externos), o si solo los usa un único cliente, los complementos hospedados en proveedor son significativamente más fáciles de implementar y mantener que cuando tiene clientes externos o múltiples clientes que utilizarán el complemento. Si piensa vender el complemento al público, también debe tener en cuenta si lo comercializará a empresas con cuentas SharePoint Online, a las que tienen sus propias granjas de servidores de SharePoint o a ambas.


- También debe tener en cuenta sus habilidades o las habilidades de su personal de desarrollo. Por ejemplo, si es un desarrollador de ASP.NET con experiencia, eso sería un punto a favor de crear una aplicación web remota y exponer datos de listas de SharePoint en una página de ASP.NET. Por otro lado, si es un desarrollador de SharePoint con experiencia, eso sería un punto a favor de usar una página del sitio y una lista de SharePoint personalizadas con JavaScript para realizar el procesamiento.



## Recursos adicionales
<a name="AdditionalResources"> </a>


-  [Diseñar aplicaciones para SharePoint](design-sharepoint-add-ins.md)


-  [Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)



