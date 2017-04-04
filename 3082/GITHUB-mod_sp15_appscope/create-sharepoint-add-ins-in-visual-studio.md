---
title: Crear complementos de SharePoint en Visual Studio
ms.prod: SHAREPOINT
ms.assetid: f1b9c858-907c-4558-b671-3b488ece40a0
---



# Crear complementos de SharePoint en Visual Studio
Aprenda a desarrollar Complementos de SharePoint con las plantillas para proyectos y elementos de proyecto de Visual Studio.
Puede desarrollar Complementos de SharePoint con las nuevas plantillas para proyectos y elementos de proyecto de **vsnv**. 
  
    
    


## Plantillas de proyecto
<a name="SP15Projecttemplates_templates"> </a>

Cuando se usa una plantilla de proyecto de Visual Studio, se crea una solución que contiene los archivos y los elementos que el tipo de proyecto necesita. Las siguientes plantillas de proyecto aparecen en el cuadro de diálogo **Nuevo proyecto** al ampliar el nodo **Office/SharePoint** y elegir el nodo **Complementos**. Si quiere información sobre las plantillas de proyecto del nodo **Soluciones de SharePoint**, vea  [Plantillas de proyecto y de elementos de proyecto para SharePoint](http://go.microsoft.com/fwlink/?LinkId=255306). 
  
    
    

### Complemento de Office

Crea una página web hospedada en una aplicación de Office, como Excel u Outlook. Un Complemento de Office proporciona contenido y funciones suplementarios en un documento o elemento de Outlook. Para más información, vea  [Empezar a crear aplicaciones para Office](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### Complemento de SharePoint

Crea un Complemento de SharePoint en función de la información que especifique en el asistente. La información cuenta con los datos siguientes.
  
    
    

- El nombre del complemento.
    
  
- El sitio local o remoto de SharePoint usado para depurar el complemento.
    
  
- El tipo de complemento que desea crear: hospedado por el proveedor u hospedado en SharePoint. 
    
  
Para más información, vea  [Complementos de SharePoint](sharepoint-add-ins.md).
  
    
    

### Complemento empresarial de nube

Al usar la plantilla **Complemento empresarial de nube** de Visual Studio, puede crear un complemento hospedado en SharePoint en el que los usuarios de móviles puedan ver, agregar y actualizar datos de ubicaciones remotas usando modernos dispositivos táctiles, como teléfonos y tabletas. Para más información, vea [Creación de complementos empresariales de nube](create-cloud-business-add-ins.md).
  
    
    

## Plantillas de elemento de proyecto
<a name="SP15Projecttemplates_items"> </a>

Después de crear una solución de SharePoint, puede agregar elementos de proyecto usando las plantillas siguientes, que aparecen en el cuadro de diálogo **Agregar nuevo elemento** del nodo **Office/SharePoint**.
  
    
    

### Complemento de Office

Agrega un Complemento de Office al Complemento de SharePoint. Puede agregar un complemento de panel de tareas, un complemento de contenido o un complemento de correo. Para más información, vea  [Empezar a crear aplicaciones para Office](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### Elemento web cliente (web de host)

Agrega un elemento web cliente al Complemento de SharePoint. Al agregar un elemento web cliente, puede mostrar complementos en las páginas de un sitio de host. Esta plantilla contiene un único archivo Elements.xml, cuyas propiedades definen los elementos siguientes del elemento web cliente.
  
    
    


|**Nombre de propiedad**|**Descripción**|
|:-----|:-----|
|ClientWebPart  <br/> |Especifica el nombre, el título, la descripción y las dimensiones de la parte web cliente.  <br/> |
|Content  <br/> |Define la ubicación de la página que se representa en elemento web cliente. Este elemento tiene dos propiedades:  `Type` y `Src`.  `Type` especifica qué tipo de elemento web está creando como, por ejemplo, HTML. `Src` define la ubicación de la página que se representará en el interior del elemento web cliente. La plantilla consulta las propiedades en la cadena de consulta usando el patrón_ _nombreDePropiedad__, como por ejemplo  `Src="~addinWebUrl/Pages/ClientWebPart1.aspx?Property1=_property1_"` <br/> Para más información, vea  [Crear elementos del complemento para instalar con el complemento para SharePoint](create-add-in-parts-to-install-with-your-sharepoint-add-in.md).  <br/> |
   

### Tipo de contenido

Agrega un tipo de contenido a la Complemento de SharePoint, similar a los tipos de contenido que se usaron en versiones anteriores de SharePoint. Un tipo de contenido es un conjunto de metadatos, flujo de trabajo y comportamiento de una categoría de elementos en una lista o biblioteca de SharePoint. Por ejemplo, un elemento es un tipo de contenido de lista. También puede haber otros tipos de contenido de lista como anuncios, contactos y tareas, los cuales heredan el tipo de contenido de elemento. El tipo de contenido de contacto contiene columnas como **Nombre**, **Apellidos** y **Título del puesto**.
  
    
    
Cuando se agrega un tipo de contenido a la Complemento de SharePoint, se especifica el tipo de contenido base del que se heredará el tipo de contenido nuevo. Por ejemplo, se puede heredar de un anuncio, un contacto, un documento o un tipo de contenido de elemento. Luego puede usar el diseñador de **Tipo de contenido** para configurar las columnas del tipo de contenido y sus propiedades, como el nombre y la descripción. Los valores que elija se agregarán a los elementos `ContentType` y `FieldRef` del archivo Elements.xml. Para más información, vea [Bloque de creación: Tipos de contenido](http://msdn.microsoft.com/library/277dfc42-d9a8-4fae-9ae1-0d202b14674f%28Office.15%29.aspx).
  
    
    

### Elemento vacío

Agrega a la Complemento de SharePoint un elemento de proyecto correspondiente a un elemento vacío. Este elemento de proyecto contiene un único archivo, Elements.xml, donde se definen las propiedades del elemento. Normalmente se usa un elemento vacío para definir un elemento para el que Visual Studio no proporciona una plantilla.
  
    
    

### Lista

Agrega dos elementos de proyecto al Complemento de SharePoint: una definición de lista y una instancia de la lista. Cuando agregue una lista al complemento, debe especificar el nombre que se dará a la lista y si la lista creada estará en blanco o se basará en un tipo de lista ya existente. También debe especificar si la lista se puede personalizar. Después use el **Diseñador de listas** para configurar las columnas y las vistas de la lista y otras propiedades como el nombre de la lista y la descripción. Para obtener más información sobre las propiedades de las listas, vea [Elemento ListTemplate (Plantilla de listas)](http://msdn.microsoft.com/library/e565ead9-adcb-4a90-97e3-04850719420a%28Office.15%29.aspx) y [Elemento ListInstance (Instancia de lista)](http://msdn.microsoft.com/library/cfefe8e5-2656-4d71-bb4e-5f991a800598%28Office.15%29.aspx).
  
    
    

### Acción personalizada de elemento de menú

Agrega un elemento de proyecto que amplía la interfaz de usuario del sitio host agregando una acción a un menú de lista. La acción personalizada de menú contiene un archivo Elements.xml, que se usa para definir las propiedades de la acción. Para más información, vea  [Crear acciones personalizadas para implementarlas con complementos de SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Módulo

Agrega un elemento de proyecto de módulo a la Complemento de SharePoint. Los módulos son básicamente contenedores que puede usar para contener otros archivos cuando implemente la Complemento de SharePoint. Para agregar un archivo, cópielo en el proyecto del módulo, en **Explorador de soluciones**. En el archivo Elements.xml del módulo, se agregará automáticamente una referencia al archivo en la que se especifica la ruta de acceso y la dirección URL del archivo nuevo. Puede eliminar el archivo Sample.txt incluido en el módulo, ya que se suministra como ejemplo.
  
    
    

### Receptor de eventos remotos

Agrega un elemento de proyecto para un receptor de eventos remotos al Complemento de SharePoint y un proyecto de aplicación web a la solución si todavía no existe este proyecto. La aplicación web contiene un servicio web asociado al receptor de eventos remotos del Complemento de SharePoint. El servicio web contiene un archivo de código de Visual Basic o Visual C# cuyo código se ejecuta cuando una lista, un elemento de lista o un evento de elemento web se producen en el Complemento de SharePoint. Si hay una aplicación web, esta se asocia al Complemento de SharePoint y el servicio web se agrega a la aplicación. Para más información, vea  [Controlar eventos en los complementos de SharePoint](handle-events-in-sharepoint-add-ins.md).
  
    
    

### Acción personalizada de cinta

Agrega un elemento de proyecto que amplía la interfaz de usuario del sitio host agregando una acción a una cinta. La acción personalizada de cinta contiene un archivo Elements.xml, que define las propiedades de la acción. Para más información, vea  [Crear acciones personalizadas para implementarlas con complementos de SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Configuración de búsqueda

Agrega un elemento de proyecto que le permite importar opciones de configuración de búsqueda que se han exportado de un sitio de SharePoint.
  
    
    

### Columna de sitio

Agrega un elemento de proyecto para una columna de sitio a la Complemento de SharePoint. La columna de sitio contiene un archivo Elements.xml que define las propiedades de  `Field` de la columna de sitio, incluidos los datos siguientes.
  
    
    


|**Nombre de propiedad**|**Descripción**|
|:-----|:-----|
|ID  <br/> |Valor GUID único para la columna de sitio.  <br/> |
|Name  <br/> |Nombre único usado para hacer referencia a la columna de sitio.  <br/> |
|DisplayName  <br/> |Nombre descriptivo que aparece en la UI.  <br/> |
|Type  <br/> |Los tipos de datos que se usan en la columna de sitio basados en **SPFieldType**, como booleanos, de búsqueda o de texto.  <br/> |
|Required  <br/> |Si la columna es obligatoria, se establece la propiedad en **True**; de lo contrario, la propiedad se establecerá en **False**.  <br/> |
|Group  <br/> |Especifica el nombre del grupo al que se asigna la columna de sitio. El valor predeterminado de esta propiedad es **Columnas del sitio personalizadas**.  <br/> |
   
Para más información, vea  [Bloque de creación: columnas y tipos de campo](http://msdn.microsoft.com/library/58548ade-e439-4931-82a2-fa29bd5afdb7%28Office.15%29.aspx).
  
    
    

### Flujo de trabajo

Agrega un elemento de proyecto para un flujo de trabajo de Microsoft Azure a la Complemento de SharePoint. Para más información, vea  [Flujos de trabajo de SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx). Cuando se agrega este tipo de elemento, se especifica un nombre para el flujo de trabajo y se indica si es una lista o un flujo de trabajo de lista. Tal como sugiere el nombre, un flujo de trabajo de lista funciona únicamente con una lista, mientras que un flujo de trabajo de sitio solo funciona con un sitio de SharePoint. Cuando cree el flujo de trabajo, especifique también si debe asociarse automáticamente el flujo de trabajo a listas y bibliotecas y, en caso afirmativo, con cuáles. Por toda asociación que agregue, se agregará también un archivo correspondiente en el proyecto de flujo de trabajo. Un flujo de trabajo contiene los archivos siguientes.
  
    
    


|**Nombre de archivo**|**Descripción**|
|:-----|:-----|
|Elements.xml  <br/> |Especifica la configuración del flujo de trabajo y los archivos que contiene, como el archivo workflow.xaml y los archivos de asociación, así como las propiedades de cada archivo, como la URL, el tipo y la ruta de acceso. Por cada archivo que agregue al proyecto de flujo de trabajo, se agregará también la sección correspondiente del archivo Elements.xml de flujo de trabajo. Los archivos de asociación de los flujos de trabajo de la lista necesitan una lista para tener una referencia al token de lista. En un flujo de trabajo de sitio, se agrega un GUID al sitio.  <br/> > **PRECAUCIóN**> Dado que Visual Studio mantiene los elementos en el archivo Elements.xml, no le recomendamos que lo modifique, a menos que esté familiarizado con los cambios que esto conlleva.           |
|Workflow.xaml  <br/> |Representa al diseñador del flujo de trabajo. En este archivo se agregan acciones al flujo de trabajo y se establecen su código y sus propiedades.  <br/> |
|WorkflowStartAssociation  <br/> |Inicia manualmente el flujo de trabajo en SharePoint. Este archivo se agrega al proyecto de flujo de trabajo si activa la casilla **El usuario inicia manualmente el flujo de trabajo** del asistente para el flujo de trabajo. <br/> |
|ItemAddedAssociation  <br/> |Inicia automáticamente el flujo de trabajo si hay uno presente cuando un usuario crea un elemento en el sitio o lista (según el tipo de flujo de trabajo). Este archivo se agrega al proyecto de flujo de trabajo si activa la casilla **El flujo de trabajo se inicia automáticamente cuando se agrega un elemento** en el asistente para flujo de trabajo. <br/> |
|ItemUpdatedAssociation  <br/> |Inicia automáticamente el flujo de trabajo, en caso de que haya uno presente cuando un usuario cambie un elemento del sitio o lista (según el tipo de flujo de trabajo). Este archivo se agrega al proyecto de flujo de trabajo si activa la casilla **El flujo de trabajo se inicia automáticamente cuando cambia un elemento** en el asistente para flujo de trabajo. <br/> |
|WorkflowHistoryList  <br/> |Representa el archivo que se agrega al proyecto de flujo de trabajo si crea un historial para el flujo de trabajo en el asistente para flujo de trabajo.  <br/> |
|WorkflowTaskList  <br/> |Representa el archivo agregado al proyecto de flujo de trabajo si crea una lista de tareas para el flujo de trabajo en el asistente para el flujo de trabajo.  <br/> |
   

### Actividad personalizada de flujo de trabajo

Agrega un elemento de proyecto para una actividad personalizada de flujo de trabajo a la Complemento de SharePoint. Al agregar una actividad personalizada de flujo de trabajo, puede crear acciones de flujo de trabajo adicionales e importarlas como acciones personalizadas en SharePoint Designer 2013. La actividad personalizada de flujo de trabajo contiene un archivo Elements.xml, que define las propiedades de la acción, y un archivo .xaml para el diseñador de flujo de trabajo. Para más información, vea  [Flujos de trabajo de SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx).
  
    
    

## Recursos adicionales
<a name="SP15Projecttemplates_addlresources"> </a>


-  [Herramientas y entornos para desarrollar complementos para SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
