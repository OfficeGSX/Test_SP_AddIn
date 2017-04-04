---
title: Actualizar componentes remotos en complementos de SharePoint
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# Actualizar componentes remotos en complementos de SharePoint
Actualice las aplicaciones web remotas y las bases de datos en un Complemento de SharePoint.
## Requisitos previos para actualizar los componentes remotos de un complemento de SharePoint
<a name="Prerequistes"> </a>

Debe familiarizarse con  [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md), y con los requisitos previos y los principales conceptos que se describen en el artículo.
  
    
    

## Actualizar componentes remotos
<a name="UpdateRemote"> </a>

En la mayoría de casos, solo se pueden proporcionar unos consejos muy generales sobre la actualización de componentes remotos debido a las enormes diferencias entre plataformas y sistemas de arrendamiento. Las dos secciones siguientes ofrecen cierta orientación.
  
    
    

### Actualizar componentes remotos en un complemento hospedado por el proveedor
<a name="UpdateProviderHosted"> </a>

En el caso de un Complemento de SharePoint hospedado por el proveedor, los componentes remotos se actualizan con los procedimientos recomendados de la plataforma en la que están hospedados los componentes. Al igual que los componentes remotos de un complemento hospedado por el proveedor se instalan de forma independiente a la instalación del propio Complemento de SharePoint, también se actualizan de forma independiente. Algunos puntos que tener en cuenta:
  
    
    

- Los componentes remotos actualizados deberían continuar funcionando con todas las versiones anteriores del Complemento de SharePoint.
    
  
- Si implementó un sistema de aislamiento de arrendamientos para los componentes remotos, recuerde que diferentes inquilinos podrían estar usando varias versiones del complemento, y un determinado inquilino podría incluso tener diferentes versiones instaladas en varios sitios web de SharePoint.
    
  
En el caso de un Complemento de SharePoint hospedado por el proveedor que usa Base de datos SQL de Microsoft Azure o SQL Server, existen varios métodos para actualizar la base de datos. Para empezar, vea  [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx).
  
    
    

## Pasos siguientes
<a name="Next"> </a>

Vuelva a  [Pasos principales en la actualización de un complemento](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) o visite directamente uno de los siguientes artículos para saber cómo actualizar el siguiente componente principal del Complemento de SharePoint.
  
    
    

-  [Actualizar componentes de web de complemento en SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Actualizar componentes de web de host en SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Crear un controlador para el evento de actualización en complementos de SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Proceso de actualización de complementos para SharePoint](sharepoint-add-ins-update-process.md)
    
  

