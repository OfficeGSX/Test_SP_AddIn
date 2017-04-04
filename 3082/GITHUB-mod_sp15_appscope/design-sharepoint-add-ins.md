---
title: Diseñar aplicaciones para SharePoint
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# Diseñar aplicaciones para SharePoint
Obtenga información general sobre las opciones de diseño y de arquitectura que están disponibles en Complementos de SharePoint y aprenda a tomar las decisiones adecuadas para facilitar el desarrollo de su complemento en SharePoint 2013.
Digamos que tiene una idea fantástica para un complemento. En esta sección, le guiaremos a través de las decisiones de diseño que necesita tomar y le ofrecemos procedimientos recomendados para crear su complemento. Por ejemplo, ¿qué hace una buena interfaz de usuario? ¿Cuáles son la "formas" de complementos disponibles? ¿Cuándo se debe utilizar una en vez de otra? ¿Qué opciones tiene para obtener acceso a datos? 
  
    
    


## Introducción al diseñoComplementos de SharePoint
<a name="SP15Design_Startdesigning"> </a>

Dado que el modelo de complementos de nube de SharePoint 2013 facilita la existencia de un gran número de opciones de diseño, las Complementos de SharePoint pueden tener varias formas y tamaños. En esta sección proporcionamos información que le ayudará a tomar algunas de las decisiones más importantes relativas a la planeación y diseño de la arquitectura y a la experiencia del usuario del complemento, como la forma de hospedaje del complemento, cómo éste obtendrá acceso a los datos de forma segura y eficaz, y cuál será la experiencia del usuario.
  
    
    
Para obtener información general sobre las opciones de diseño y arquitectura disponibles con los complementos para SharePoint, vea  [Tres formas de concebir las opciones de diseño de complementos para SharePoint](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md). Para obtener información general sobre qué son las Complementos de SharePoint, vea  [Complementos de SharePoint](sharepoint-add-ins.md).
  
    
    

## Elección del modelo de hospedaje adecuado para el complemento
<a name="SP15Design_Hostingmodel"> </a>

Las Complementos de SharePoint admiten una gran variedad de opciones de hospedaje. Puede elegir su propia pila web, hacer que Microsoft aprovisione Microsoft Azure y SQL Azure, o que el complemento se hospede en SharePoint. La tabla 1 contiene recursos que pueden ayudarle a elegir el modelo de hospedaje adecuado para su complemento.
  
    
    

**Tabla 1. Recursos y orientación para elegir el modelo de hospedaje adecuado para Complementos de SharePoint**


|**Artículo**|**Descripción**|
|:-----|:-----|
| [Elegir patrones para desarrollar y hospedar un complemento para SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Obtenga información acerca de las diferentes formas de hospedar los componentes de Complementos de SharePoint.  <br/> |
   

## Elección de las tecnologías de acceso de datos adecuadas para el complemento
<a name="SP15Design_Dataaccess"> </a>

Debe asegurarse de que su complemento obtiene acceso a los datos de forma eficaz y segura. Dispone de varias tecnologías de acceso de datos para obtener acceso a SharePoint y trabajar con los datos del complemento. En la tabla 2 se proporcionan recursos que le ayudarán a informarse sobre las opciones y elegir la opción más adecuada para su complemento. 
  
    
    

**Tabla 2. Recursos y orientación para elegir las tecnologías de acceso a datos que se usarán en las Complementos de SharePoint**


|**Artículo**|**Descripción**|
|:-----|:-----|
| [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> | Infórmese de las opciones de acceso de datos de las que dispone al crear las Complementos de SharePoint, como las opciones de conexión de datos para escenarios de datos entrantes y salientes, o las API disponibles cuando se desea obtener acceso a los datos de SharePoint desde el complemento. <br/> |
   

## Diseño de la UX para el complemento
<a name="SP15Design_UX"> </a>

Cuando diseñe el complemento, su verdadero objetivo debe ser crear una experiencia que permita a los usuarios completar los escenarios que desea que los usuarios lleven a cabo. En la tabla 3, descubra los recursos y la información que necesita para crear buenos complementos que sigan las mejores prácticas de diseño de experiencia del usuario y tengan el aspecto y el comportamiento familiar de SharePoint 2013.
  
    
    

**Tabla 3. Recursos y orientación para diseñar una gran experiencia de usuario para las Complementos de SharePoint**


|**Artículo**|**Descripción**|
|:-----|:-----|
| [Diseño de la experiencia de usuario para aplicaciones en SharePoint 2013](ux-design-for-sharepoint-add-ins.md) <br/> |Infórmese sobre las opciones de experiencia del usuario que tiene a su disposición cuando crea Complementos de SharePoint.  <br/> |
   

## Diseño teniendo en cuenta la actualización
<a name="Upgrade"> </a>

Es posible que algún día quiera actualizar su complemento y cargarlo en la Tienda Office o en el catálogo de complementos de una organización. Esa tarea será mucho más sencilla si piensa en cómo actualizará el complemento cuando esté diseñando la primera versión. Le recomendamos que lea los siguientes artículos al principio de la fase del diseño:  [Proceso de actualización de complementos para SharePoint](sharepoint-add-ins-update-process.md) y [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md). 
  
    
    

## Pasos siguientes: desarrolle y publique el complemento
<a name="SP15Design_Next"> </a>

¿Tiene un diseño sólido para el complemento? Prepárese para crear el complemento y publicarlo. Los recursos proporcionados en la tabla 4 le ayudarán a empezar.
  
    
    

**Tabla 4. Recursos e información para desarrollar y publicarComplementos de SharePoint**


|**Artículo**|**Descripción**|
|:-----|:-----|
| [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md) <br/> |En este artículo se debaten características y conceptos avanzados del modelo de complementos.  <br/> |
| [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md) <br/> |En este artículo se describen el proceso y los requisitos de publicaciónComplementos de SharePoint.  <br/> |
   

## Recursos adicionales
<a name="SP15Design_AddRes"> </a>


-  [Paquete de ejemplos de código de complementos para SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
-  [Vuelva a imaginar el desarrollo de SharePoint](http://msdn.microsoft.com/es-es/office/apps/dn133840)
    
  
-  [Complementos de SharePoint](sharepoint-add-ins.md)
    
  
-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Blog para complementos](http://blogs.msdn.com/b/spoffapps)
    
  

