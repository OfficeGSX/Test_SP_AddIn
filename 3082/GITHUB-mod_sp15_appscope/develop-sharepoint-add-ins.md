---
title: Desarrollar complementos para SharePoint
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# Desarrollar complementos para SharePoint
Encontrar recursos y artículos detallados que le ayudarán a crear capacidades avanzadas en sus Complementos de SharePoint.
> **NOTA**
> Este artículo presupone que está familiarizado con el artículo  [Complementos de SharePoint](sharepoint-add-ins.md) y el material introductorio relacionado.




En **Desarrollo** tenemos los siguientes recursos para ayudar a explicar todo lo que puede hacer en una Complemento de SharePoint:
- Información general detallada


- Artículos sobre procedimientos


- Fragmentos de código


Encontrará artículos sobre: 
- Cómo ejecutar operaciones CRUD en listas


- Cómo crear consultas REST e interactuar con las nuevas API


- Cuándo y cómo configurar OAuth para la seguridad


SharePoint tiene funciones sociales empresariales como fuentes de actividad y perfiles de usuario, junto con funciones de administración de contenido empresarial, funciones de interoperabilidad LOB y funciones de diseño de sitios web que realmente pueden hacer destacar sus complementos. Obtenga más información de todo ello en  [Agregar capacidades de SharePoint 2013](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx).El código es clave, así que analicemos el menú de "Ejemplos" del centro de desarrollo. Es un vínculo directo a nuestros ejemplos de código para complementos. Tan pronto como haya configurado el entorno de desarrollo, debería comprobar algunos de los ejemplos. Aproveche una función de la comunidad que le permite solicitar un código de ejemplo, si no tenemos uno que desearía ver. Recogemos esas peticiones, junto con otros comentarios sobre la documentación y los usamos en nuestras continuas actualizaciones de contenido y ejemplos. Háganos saber si desearía ver algo.
## Empezar a trabajar con recursos de Complementos de SharePoint
<a name="bk_gettingstarted"> </a>

Si acaba de empezar a desarrollar Complementos de SharePoint, primero vea  [Complementos de SharePoint](sharepoint-add-ins.md). Esa página le ofrece artículos clave que le ayudarán a familiarizarse rápidamente con los diferentes tipos de Complementos de SharePoint. Antes de hacer tipos de desarrollo más avanzado con Complementos de SharePoint, debe empezar conociendo los tipos de complementos que desea crear, las tecnologías que desea incluir y las opciones de hospedaje que desea usar.




### Tareas esenciales y recursos para desarrollar Complementos de SharePoint con el modelo de objetos de cliente, el modelo de objetos JavaScript y los extremos REST en SharePoint 2013
<a name="bk_essentials"> </a>

Independientemente del tipo de Complemento de SharePoint que decida crear, su complemento siempre interactuará de alguna forma con un sitio de SharePoint 2013. Los artículos de la tabla 1 describen cómo crear muchos de los tipos más importantes de trabajo con sitios de SharePoint mediante tres interfaces que están disponibles para usar en sus Complementos de SharePoint: el modelo de objetos de cliente, el modelo de objetos JavaScript y los extremos REST.




**Tabla 1. Operaciones básicas con el modelo de objetos de cliente de SharePoint 2013, el modelo de objetos JavaScript y la interfaz de REST**


|**Tema**|**Descripción**|
|:-----|:-----|
| [Realizar operaciones básicas con código de biblioteca de cliente de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |Explica cómo realizar operaciones comunes con C# y el modelo de objetos de cliente.  <br/> |
| [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |Explica cómo realizar operaciones comunes con el modelo de objetos JavaScript.  <br/> |
| [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |Explica cómo realizar operaciones comunes con la interfaz de REST.  <br/> |
 

## Conozca los conceptos fundamentales para el desarrollo con Complementos de SharePoint.
<a name="bk_fundamentals"> </a>

Además de conocer las operaciones básicas, debe comprender los conceptos fundamentales del modelo de desarrollo de complementos de SharePoint 2013. Cada tipo de Complemento de SharePoint contiene un archivo de manifiesto de complemento incorporado en un paquete de complemento que se implementa en un sitio de SharePoint 2013. Y al desarrollar cualquier tipo de complemento debe tener en cuenta una serie de cuestiones relacionadas con la autenticación y autorización, el acceso a los datos y la facilidad de uso. Los artículos de la tabla 2 le familiarizan con estas cuestiones y explican sus implicaciones en cualquier tipo de que desee crear.




**Tabla 2. Conceptos fundamentales para trabajar con Complementos de SharePoint**


|**Tema**|**Descripción**|
|:-----|:-----|
| [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |Le guía a través de conceptos básicos relacionados con la adquisición de los privilegios necesarios para trabajar con recursos de SharePoint 2013.  <br/> |
| [Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |Explica cómo funcionan los manifiestos de complemento y cómo se construyen los paquetes de complemento.  <br/> |
| [Crear componentes de experiencia de usuario en SharePoint 2013](create-ux-components-in-sharepoint-2013.md) <br/> |Explora las maneras en que puede crear una experiencia de usuario enriquecida en Complementos de SharePoint.  <br/> |
| [Trabajar con datos externos en SharePoint 2013](work-with-external-data-in-sharepoint-2013.md) <br/> |Explica las opciones de acceso de datos y las técnicas disponibles en diferentes tipos de Complementos de SharePoint.  <br/> |
| [Licencias de complementos de Office y SharePoint](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |Le guía a través del marco de licencias de complementos para Complementos de Office y SharePoint.  <br/> |
 

## Unir las piezas: Crear complementos avanzados de SharePoint con la integración de capacidades
<a name="bk_integrate"> </a>

Una vez que se haya familiarizado con las capacidades y las funciones de las Complementos de SharePoint, podrá empezar a crear complementos más complejos uniendo todas las piezas de manera que se adapten a sus necesidades. Los artículos de la tabla 3 muestran cómo integrar capacidades y crear Complementos de SharePoint más completas.




**Tabla 3. Conceptos avanzados en Complementos de SharePoint**


|**Tema**|**Descripción**|
|:-----|:-----|
| [Crear un complemento hospedado por el proveedor que incluya un tipo de contenido y una lista de SharePoint personalizados](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |Explica cómo crear Complementos de SharePoint que se hospeden en la nube y que incluyan tipos de contenido y listas de SharePoint personalizados.  <br/> |
 

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Herramientas y entornos para desarrollar complementos para SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)


-  [Diseñar aplicaciones para SharePoint](design-sharepoint-add-ins.md)


-  [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md)


-  [Paquete de ejemplos de complementos de SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)



