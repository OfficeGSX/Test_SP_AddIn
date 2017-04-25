---
title: Crear componentes de experiencia de usuario en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: bfdd0a58-2cc5-4805-ac89-4bd2fe6f3b09
---


# Crear componentes de experiencia de usuario en SharePoint 2013
Aprenda cómo crear componentes de experiencia de usuario (UX) para los complementos en SharePoint 2013.
## Cómo crear componentes de experiencia de usuario en Complementos de SharePoint
<a name="SP15CreateUX_Creating"> </a>

El modelo para aplicaciones para SharePoint ofrece muchos componentes y mecanismos de UX que le ayudan a crear una gran experiencia de usuario en Complementos de SharePoint. La experiencia del usuario en el modelo de complemento también es lo suficientemente flexible como para permitir el uso de las técnicas y plataformas que mejor se adapten a las necesidades de los usuarios finales. En la Tabla 1 se muestra una lista de los recursos que le servirán para aprender a crear y usar componentes UX en los complementos.




**Tabla 1. Recursos y pautas para la creación de componentes UX en Complementos de SharePoint**


|**Artículo**|**Descripción**|
|:-----|:-----|
| [Usar una hoja de estilos del sitio web de SharePoint en complementos de SharePoint](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) <br/> |Puede incluir una referencia a la hoja de estilos de un sitio web de SharePoint en el Complemento de SharePoint y usarla para dar estilo a las páginas web con la hoja de estilos de SharePoint 2013. Además, si alguien cambia la hoja de estilos o el tema del sitio web de SharePoint, puede adoptar el nuevo conjunto de estilos en el complemento sin modificar la referencia a la hoja de estilos en el complemento.  <br/> |
| [Usar el control cromo de cliente en complementos de SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md) <br/> |El control cromo en SharePoint 2013 le permite usar el estilo de encabezado de un sitio determinado de SharePoint en el complemento sin necesidad de registrar una biblioteca de servidor ni de usar una tecnología o herramienta específica. Para usar esta función, debe registrar una biblioteca de JavaScript de SharePoint a través de una etiqueta de <script> estándar. Puede proporcionar un marcador de posición con un elemento **div** de HTML y además personalizar el control con las opciones disponibles. El control hereda su apariencia del sitio web de SharePoint especificado. <br/> |
| [Crear elementos del complemento para instalar con el complemento para SharePoint](create-add-in-parts-to-install-with-your-sharepoint-add-in.md) <br/> |Con los elementos de un complemento, puede mostrar la experiencia de usuario del complemento directamente en la web de host. Un elemento del complemento muestra el contenido del complemento con un **IFrame**. Los usuarios finales pueden personalizar la experiencia con las propiedades personalizadas que usted puede proporcionar para el elemento del complemento. La página web del complemento recibe los valores de la propiedad personalizada a través de parámetros en la cadena de consulta.  <br/> |
| [Crear acciones personalizadas para implementarlas con complementos de SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md) <br/> |Cuando está creando un Complemento de SharePoint, las acciones personalizadas le permiten interactuar con las listas y la cinta en la web de host. Una acción personalizada se implementa en la web de host cuando los usuarios finales instalan el complemento. Las acciones personalizadas pueden abrir una página web remota y pasar información a través de la cadena de consulta. Hay dos tipos de acciones personalizadas disponibles para los complementos: de la cinta y de bloque de control de edición.  <br/> |
| [Personalizar una vista de lista en complementos de SharePoint mediante representación del lado cliente](customize-a-list-view-in-sharepoint-add-ins-using-client-side-rendering.md) <br/> |La representación del lado cliente proporciona un mecanismo que puede usar para producir su propia salida de un conjunto de controles hospedados en una página de SharePoint. Este mecanismo le permite usar tecnologías muy conocidas (como HTML y JavaScript) para definir la lógica de representación de las vistas de listas de SharePoint. En la representación del lado cliente, puede especificar sus propios recursos de JavaScript y hospedarlos en las opciones de almacenamiento de datos disponibles para sus complementos, como una biblioteca de documentos.  <br/> |
| [Usar el control Selector de personas del lado cliente en complementos hospedados por SharePoint](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md) <br/> |Aprenda a usar el control Selector de personas del lado cliente en las Complementos de SharePoint. El control Selector de personas del lado cliente permite a los usuarios buscar y seleccionar rápidamente cuentas de usuario válidas para usuarios individuales, grupos y notificaciones en su organización. El Selector es un control HTML y JavaScript que proporciona compatibilidad entre exploradores.  <br/> |
 

## Pasos siguientes: Cómo trabajar con datos en Complementos de SharePoint
<a name="SP15CreateUX_Next"> </a>

¿Terminó de diseñar una gran experiencia de usuario para su complemento? Incorpore datos con los mecanismos disponibles en el modelo para aplicaciones para SharePoint. Para obtener más información, vea  [Trabajar con datos externos en SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).




## Recursos adicionales
<a name="SP15CreateUX_AddRes"> </a>


-  [Complementos de SharePoint](sharepoint-add-ins.md)


-  [Diseño de la experiencia de usuario para aplicaciones en SharePoint 2013](ux-design-for-sharepoint-add-ins.md)


-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)



