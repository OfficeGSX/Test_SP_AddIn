---
title: Arrendamientos y ámbitos de implementación de los complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: 1ceb3142-a7a5-453e-920f-5f953a79401a
---


# Arrendamientos y ámbitos de implementación de los complementos para SharePoint
 Información sobre el concepto de arrendamientos y las diferencias entre la implementación de Complementos de SharePoint con ámbito de inquilino y con ámbito de web.
## Ámbito de complemento y arrendamientos
<a name="AppScope"> </a>

Un SharePoint 2013 arrendamiento es un conjunto de colecciones de sitios en una granja de servidores de SharePoint o en SharePoint Online. En SharePoint Online, las colecciones de sitios pertenecen a una sola cuenta de cliente. En una granja de servidores de SharePoint, las colecciones de sitios pueden ser todas las colecciones de sitios de una aplicación web de SharePoint o de un subconjunto de ellas, o pueden ser un conjunto de colecciones de sitios de varias aplicaciones web de la granja. Un arrendamiento puede tener un catálogo de complementos de Complemento de SharePoint al igual que una aplicación web de SharePoint.



Un Complemento de SharePoint tiene un ámbito de complemento . Los dos ámbitos de complemento posibles son:ámbito de web yámbito de inquilino. La diferencia no es una propiedad intrínseca del complemento y usted, el desarrollador, no decide cuál es el ámbito de su complemento. Los administradores de inquilinos toman la decisión en el proceso de instalación del complemento. Después de cargar un complemento en el catálogo de complementos de un arrendamiento, está inmediatamente disponible para instalarse en sitios web del arrendamiento considerando cada uno de forma individual. Los complementos que se instalan así tienen ámbito de web. Sin embargo, los administradores de inquilinos tienen otra opción. Pueden escoger instalar por lotes el complemento en un subconjunto de sitios web del arrendamiento. Los complementos que se instalan así tienen ámbito de inquilino. El administrador de inquilinos puede especificar en qué sitios web se instalará el complemento con una lista de rutas de acceso administradas, una lista de plantillas de sitios o una lista de colecciones de sitios. Solo un administrador de inquilinos puede desinstalar un complemento que se ha instalado por lotes. Cuando el administrador de inquilinos desinstala el complemento, se desinstala de todos los sitios web del arrendamiento. Los usuarios no pueden ir sitio web por sitio web desinstalando un complemento instalado por lotes. El mismo principio se aplica a la actualización de un complemento instalado por lotes: solo un administrador de inquilinos puede hacerlo y la actualización se realiza por lotes en cada sitio web del arrendamiento en los que está instalado.



Si un complemento que incluye una web de complemento se instala por lotes, se crea una sola web de complemento y se comparte por todos los sitios web del host en el que está instalado el complemento. La web de complemento se encuentra en la colección de sitios del catálogo de complementos de la organización.



Cuando se crean nuevas colecciones de sitios en el arrendamiento, los complementos que se instalaron por lotes anteriormente se instalan de forma automática en la nueva colección de sitios.




> **NOTA**
> El ámbito de complemento no debe confundirse con el ámbito de función. El ámbito de función determina dónde se implementarán los elementos de una función. Las posibilidades incluyen **Farm**, **WebApplication**, **Site** (es decir, colección de sitios) y **Web**. Solo se permite **Web** para funciones de Complementos de SharePoint (tanto funciones de web de host como funciones dentro de un .wsp en un paquete de complementos).<BR /> El ámbito de complemento no debe confundirse con los niveles de permiso del complemento. Los Complementos de SharePoint pueden pedir permisos a todas las partes del contenido de SharePoint o solo a las seleccionadas en los niveles de lista, web, colección de sitios y arrendamiento. La instalación de un complemento con ámbito de inquilino no le concede los permisos que, de otra forma, tendría; ni cancela las concesiones fundamentales del modelo de seguridad de SharePoint. Para obtener más información sobre los permisos del complemento, vea  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md). 





## Limitaciones de los complementos con ámbito de inquilino
<a name="Tenant"> </a>

Los siguientes tipos de complementos no se pueden instalar por lotes:




- Complementos que contienen una acción personalizada para la cinta (se permiten acciones personalizadas que se implementan como elementos de menú).


- Complementos que contienen una parte del complemento. 


Además, recuerde que no es posible realizar la instalación con el ámbito de inquilino en la versión de Office 365 Pequeña Empresa Premium de SharePoint Online.




## Cómo instalar, desinstalar y actualizar un complemento en varios sitios web en un arrendamiento
<a name="Web"> </a>

Independientemente de si se instala un complemento desde la Tienda Office o desde un catálogo de complementos, los administradores de inquilinos pueden instalarlo, desinstalarlo y actualizarlo en varios sitios web de un arrendamiento con el siguiente procedimiento.




### Para instalar un complemento de SharePoint en varios sitios web


1. Vaya a la página **Contenido del sitio** del sitio web del catálogo corporativo.


2. Seleccione **Agregar un complemento** e instale el complemento como lo haría en cualquier otro sitio de SharePoint.


3. Después de instalar el complemento, mantenga el mouse sobre el vínculo al complemento en la página **Contenido del sitio**. Aparece un vínculo " **...**".


4. Seleccione el vínculo y aparece una llamada.


5. En el menú, seleccione **Implementación**.


6. Use la interfaz de usuario de implementación que se abre para especificar las colecciones de sitios donde desea que se instale el complemento. Puede filtrar por rutas de acceso administradas, plantillas de sitios o direcciones URL. Los filtros tiene una relación "O" lógico: el complemento se instala en la unión de todas las colecciones de sitios que pasan uno o más de los filtros.


7. Haga clic en **Aceptar**.



### Para desinstalar un complemento de SharePoint instalado por lotes


1. Vaya a la página **Contenido del sitio** del sitio web del catálogo corporativo.


2. Mantenga el mouse sobre el vínculo al complemento en la página **Contenido del sitio**. Aparecerá un vínculo " **...**".


3. Seleccione el vínculo y aparece una llamada.


4. En la llamada, seleccione **Quitar**.



### Para actualizar un complemento de SharePoint instalado por lotes


1. Vaya a la página **Contenido del sitio** del sitio web del catálogo corporativo. Si hay una actualización disponible para un complemento, aparecerá un mensaje junto a este. Habrá un vínculo para actualizar el complemento.


2. Haga clic en el vínculo para actualizar el complemento.


3. Cuando se le solicite aprobar las solicitudes de permiso del complemento, elija **Confiar**.



## Recursos adicionales
<a name="SP15tenancies_addlresources"> </a>


-  [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md)


-  [Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [Implementación e instalación de aplicaciones para SharePoint: métodos y opciones](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [Proceso de actualización de complementos para SharePoint](sharepoint-add-ins-update-process.md)



