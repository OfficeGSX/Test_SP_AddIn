---
title: Actualizar componentes de web de host en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# Actualizar componentes de web de host en SharePoint 2013
Actualizar elementos de complemento y acciones personalizadas en la web de host de un Complemento de SharePoint.
## Requisitos previos para actualizar los componentes de la web de host
<a name="Prerequisites"> </a>

Familiarícese con  [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md), y con los requisitos previos y los principales conceptos que se describen en el artículo.




## Actualizar componentes de web de host
<a name="UpdateHostWeb"> </a>

Su complemento puede instalar dos tipos de componentes en una web de host con marcado descriptivo en el Complemento de SharePoint: las acciones personalizadas y loselementos de complemento. Actualizar estos componentes es más fácil en la web de host que en la web de complemento. No es necesaria ninguna semántica de actualización. Solo hay que agregar o cambiar las acciones personalizadas y los elementos de complemento. Cuando se actualiza el Complemento de SharePoint, SharePoint siempre aplica todos los archivos de manifiesto de elementos nuevos y vuelve a aplicar todos los archivos de manifiesto de elementos modificados con la versión más reciente. No resulta peligroso volver a aplicar estos archivos; por ejemplo, un botón de cinta para una acción personalizada no se agregará varias veces a la cinta.



Cuando se actualiza un elemento de complemento, SharePoint reemplaza la versión antigua con la versión nueva en la galería de elementos web. Asegúrese de cambiar la propiedad **Name** del objeto **ClientWebPart** cuando actualice un elemento de complemento. Esto garantiza que, cuando se actualice el complemento, SharePoint quitará la versión antigua del elemento de complemento (que ya no forma parte de la aplicación) de todas las páginas a las que se agregó. Los usuarios necesitarán volver a agregar la nueva versión a las páginas.



Si deja la propiedad **Name** sin cambiar, la versión antigua permanecerá en las páginas a las que se agregó, y será poco probable que los usuarios se den cuenta de que hay disponible una nueva versión del elemento del complemento. Asimismo, cuando el elemento del complemento se agrega a otras páginas, se agrega la nueva versión, por lo que la misma versión de un Complemento de SharePoint tendría diferentes elementos del complemento en distintas páginas.



Puede implementar otros tipos de componentes de la web de host mediante programación con un receptor de eventos remotos que debe registrar en el manifiesto del complemento con un elemento  [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx). Debería usar un receptor  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) para actualizar los componentes que se implementaron originalmente con un receptor **InstalledEventEndpoint**. Los receptores  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) se describen en [Crear un controlador para el evento de actualización en complementos de SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).




## Pasos siguientes
<a name="Next"> </a>

Vaya a  [Pasos principales en la actualización de un complemento](update-sharepoint-add-ins.md#MajorAppUpgradeSteps), o visite directamente uno de los siguientes artículos para aprender a actualizar el siguiente componente principal de su Complemento de SharePoint.




-  [Actualizar componentes de web de complemento en SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)


-  [Crear un controlador para el evento de actualización en complementos de SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)


-  [Actualizar componentes remotos en complementos de SharePoint](update-remote-components-in-sharepoint-add-ins.md)



## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md)


-  [Hospedar webs, webs de complementos y componentes de SharePoint en SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)



