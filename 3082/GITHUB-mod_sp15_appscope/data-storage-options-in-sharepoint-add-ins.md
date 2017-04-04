

# Opciones de almacenamiento de datos en los complementos para SharePoint
Información sobre las distintas formas en las que una Complemento de SharePoint puede almacenar datos.
 * **Hace referencia a:*** 
  
    
    


|||
|:-----|:-----|
|**En este artículo**          [Opciones de almacenamiento de datos estructurados](#StructuredData)           [Opciones de almacenamiento de datos no estructurados](#UnStructuredData)           [Configuración de complementos y opciones de almacenamiento de metadatos](#AppMetadata) [Opciones de acceso de datos](#DataAccess)           [Recursos adicionales](#AddtionalResources)||
   

## Opciones de almacenamiento de datos estructurados
<a name="StructuredData"> </a>

Una Complemento de SharePoint puede usar casi cualquier tipo de almacenamiento de datos estructurados, tanto dentro como fuera de SharePoint 2013 y en plataformas de Microsoft que no son de Microsoft. Las siguientes son algunas ubicaciones en las que puede almacenar datos estructurados para una Complemento de SharePoint:
  
    
    

- Listas de SharePoint en una web de complemento
    
  
- SQL Azure
    
  
- Orígenes de datos externos conectados a SharePoint con Servicios de conectividad empresarial de Microsoft (BCS)
    
  
- Un servicio de la nube que no es de Microsoft
    
  
- Una base de datos en su propio servidor
    
  

> [!SUGERENCIA]
> Es probable que en algún momento decida actualizar su Complemento de SharePoint. Cuando una Complemento de SharePoint incluye componentes de SharePoint en una web de complemento, el proceso de actualización crea una copia completa de la web de complemento. Por este motivo, las listas de SharePoint muy largas en la web de complemento hacen que el proceso de actualización consuma mucho tiempo y recursos de procesador en el servidor de base de datos de contenido. Debería evitar colocar "grandes cantidades de datos" en listas de SharePoint en la web de complemento. 
  
    
    


## Opciones de almacenamiento de datos no estructurados
<a name="UnStructuredData"> </a>

Los documentos, las imágenes, los videos, los archivos de audio y otros tipos de datos no estructurados que se producen o usan en una Complemento de SharePoint se pueden almacenar fuera o dentro de SharePoint. Las bibliotecas de documentos son una buena opción para los documentos y se pueden buscar a través de búsquedas de SharePoint. Una biblioteca de activos de sitios suele ser una buena opción para los archivos multimedia. 
  
    
    
Otras opciones incluyen el almacenamiento de blob en su cuenta de Microsoft Azure o en sus propios servidores. También puede almacenar archivos en algunas plataformas que no son de Microsoft o en servicios de la nube.
  
    
    

## Configuración de complementos y opciones de almacenamiento de metadatos
<a name="AppMetadata"> </a>

Los metadatos de una Complemento de SharePoint, como preferencias de usuario, información de ubicación y otras configuraciones pueden almacenarse en varios lugares. Una lista de SharePoint oculta a veces es una buena elección. También puede usar la bolsa de propiedades de la web de complemento. Otra opción, para un complemento hospedado en proveedor, es usar Microsoft Azure Almacenamiento en tabla. 
  
    
    

## Opciones de acceso de datos
<a name="DataAccess"> </a>

Las opciones de acceso a datos, por supuesto, dependen de su elección de almacenamiento. La búsqueda y el acceso a los datos se analizan en detalle en muchos otros artículos. Para obtener más información, vea  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).
  
    
    

## Recursos adicionales
<a name="AddtionalResources"> </a>


-  [Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  

  
    
    