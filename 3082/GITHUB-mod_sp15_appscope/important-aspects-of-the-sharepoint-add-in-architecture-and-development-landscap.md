---
title: Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: ae96572b-8f06-4fd3-854f-fc312f7f2d88
---


# Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint
Obtenga información sobre los distintos aspectos de la arquitectura de Complementos de SharePoint y de modelo para aplicaciones para SharePoint, lo cual incluye las opciones de host de la complemento, las opciones de la interfaz de usuario, el sistema de implementación, el sistema de seguridad y el ciclo de vida. Este artículo complementa la información en el artículo  [Complementos de SharePoint](sharepoint-add-ins.md).
## Opciones de host de los complementos para SharePoint
<a name="SPAppModelArch_SPCenteredVsCloudCentered"> </a>

El modelo de complemento de SharePoint 2013 aporta los siguientes métodos para hospedar los componentes de una Complemento de SharePoint: 
  
    
    

- **Hospedados por el proveedor:** complementos que incluyen al menos un componente remoto y pueden incluir también componentes de SharePoint. Los componentes que no son de SharePoint, bien los implementa su lógica en su hardware o cuenta en la nube, o bien se implementan en el hardware del cliente o en la cuenta en la nube usando los programas y las instrucciones de instalación que haya proporcionado.
    
  
- **Hospedados en SharePoint:** complementos que solo incluyen componentes de SharePoint y una lógica que se ejecuta en el cliente.
    
  
Si quiere información detallada sobre las opciones de hospedaje y sobre cuál elegir, vea  [Elegir patrones para desarrollar y hospedar un complemento para SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).
  
    
    

## Sitios web de complemento, sitios web host, características y componentes de SharePoint en los complementos
<a name="SPComponents"> </a>

El sitio web en el que se instala una Complemento de SharePoint se denomina sitio web host. Sin embargo, los elementos importantes de Complemento de SharePoint, ya sean componentes de SharePoint o componentes externos, no se implementan en el sitio web host. Los elementos externos se implementan en servidores externos o cuentas en la nube. Los componentes de SharePoint se implementan en un sitio web especial con su propio dominio que se denomina sitio web de complemento. Únicamente se implementa un conjunto limitado de elementos de interfaz de usuario que ofrecen acceso a los usuarios a otros componentes del complemento en el sitio web host. Estos componentes de la interfaz de usuario en el sitio web host se implementa como parte de la característica del sitio web host (una característica "fantasma" que se encuentra en el paquete del complemento en lugar de dentro de un archivo .wsp). Los componentes que se implementan en el sitio web de complemento están siempre en características que se encuentran en un archivo .wsp. Ambos tipos de característica deben tener un ámbito **Web**. No se permiten otros ámbitos para las características en Complementos de SharePoint.
  
    
    
Como regla general, cualquier componente de SharePoint que no incluya código personalizado que se ejecute en los servidores de SharePoint puede incluirse en una Complemento de SharePoint (e implementarse en el sitio web de complemento). Existen sin embargo algunas excepciones y algunos matices respecto a cómo y dónde se implementan los componentes. Para obtener más información sobre estos matices y sobre los sitios web host, los sitios web de complemento aislados y las características en los complementos, consulte  [Hospedar webs, webs de complementos y componentes de SharePoint en SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).
  
    
    

## Obtener acceso al complemento desde la interfaz de usuario
<a name="AccessingApp"> </a>

Cuando se instala una aplicación Complemento de SharePoint en un sitio web, el complemento aparece en la página **Contenidos del sitio** del sitio web host. Los usuarios pueden iniciar el complemento desde esa página. Cuando se abre de esta forma, el complemento se ejecuta en modo de pantalla completa.
  
    
    
Otro método para exponer una Complemento de SharePoint es a través de un elemento de complemento, un tipo de elemento web que represente la clase **ClientWebPart**. Este tipo de elemento web es fundamentalmente un contenedor para un IFrame que hospedaría una página del complemento. En el caso más sencillo, la única propiedad importante del elemento web es una dirección URL que señala a la página. Pero los elementos web pueden disponer de propiedades personalizadas las cuales pueden establecer los usuarios en un elemento de herramientas. Estas propiedades se pueden usar, por ejemplo, para establecer información de contexto como el código postal del usuario. Para incluir un elemento de esta índole en el complemento, cree una característica en el complemento y agregue un marcado declarativo del elemento web. Al igual que con cualquier otro elemento web, aparece en la interfaz de usuario de SharePoint 2013 desde la que los usuarios agregan elementos web. Puede implementar más de un elemento de complemento con el complemento si necesitara más variabilidad aún. Por ejemplo, un complemento meteorológico puede constar de un elemento de complemento que muestre el tiempo en estos momentos y un segundo elemento de complemento que muestre la previsión para la semana. Los dos elementos pueden tener tamaños y funcionalidades diferentes.
  
    
    

> **NOTA**
> También puede implementar elementos de complemento en el sitio web de complemento. Para hacerlo, el marcado para el elemento web formaría parte de una característica en un archivo. wsp en el paquete del complemento y no en la característica del sitio web host. 
  
    
    

Se recomienda que intente conferir a los complementos un aspecto parecido al de SharePoint en la medida de lo posible, pese a que no es obligatorio y no siempre es la mejor elección. Para obtener más información sobre las directrices de la experiencia del usuario, consulte  [Diseño de la experiencia de usuario para aplicaciones en SharePoint 2013](ux-design-for-sharepoint-add-ins.md). 
  
    
    
Hay, por ejemplo, una página maestra que se denomina app.master. Esta página está optimizada para que la usen las páginas de los complementos. La página app.master forma parte de una definición de sitio nueva que se incluye en SharePoint 2013. 
  
    
    
Otra herramienta que puede usar para contribuir a que los complementos mantengan una apariencia y forma de uso coherentes con SharePoint es el control cromo que se incluye con SharePoint 2013. Con este control, podrá agregar un área de encabezado de SharePoint que permita la navegación a las páginas del complemento, lo cual incluye páginas hospedadas externamente. Para obtener más información sobre el diseño de la UX en Complementos de SharePoint, consulte  [Diseño de la experiencia de usuario para aplicaciones en SharePoint 2013](ux-design-for-sharepoint-add-ins.md). Para obtener más información sobre el control cromo, consulte  [Usar el control cromo de cliente en complementos de SharePoint](use-the-client-chrome-control-in-sharepoint-add-ins.md).
  
    
    

## Estructura del paquete del complemento
<a name="SPAppModelArch_Package"> </a>

Un paquete de Complemento de SharePoint es un archivo que cuenta con una extensión ".app" y que cumple con las  [Open Packaging Conventions (OPC, Convenciones de empaquetado abierto)](http://msdn.microsoft.com/es-es/magazine/cc163372.aspx). (Puede abrir el archivo si agrega ".zip" como extensión extra al nombre de archivo y, después, lo abre en el Explorador de Windows). Contiene un manifiesto del complemento que especifica ciertas propiedades del complemento e instrucciones para la infraestructura de instalación de SharePoint. Para obtener más información sobre el manifiesto y el paquete del complemento, consulte  [Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md).
  
    
    

## Permisos, autenticación y autorización para complementos de SharePoint
<a name="SPAppModelArch_Running"> </a>

SharePoint 2013 presenta un sistema nuevo de seguridad y permisos para complementos.
  
    
    

### Permisos de complemento
<a name="AppPermissions"> </a>

Complementos de SharePoint disponen de permisos de la misma forma que ocurre con usuarios y grupos. De esta manera, un complemento puede disponer de un conjunto de permisos distintos a los permisos de un usuario que está ejecutando el complemento. 
  
    
    
Es preciso solicitar, en el archivo de manifiesto del complemento, los permisos que necesite el complemento para ejecutarse. El usuario que agregue el complemento debe otorgar estas solicitudes y el usuario solo podrá conferir los permisos de los que disponga como usuario. La concesión se debe realizar para todos los permisos solicitados o para ninguno con el fin de simplificar la administración de permisos para usuarios y desarrolladores. (La entidad de seguridad del complemento dispone siempre de derechos de control pleno para el sitio web de complemento, por tanto solamente necesita solicitar permisos para usar los recursos de SharePoint en el sitio web host u otras ubicaciones que no se encuentren en el sitio web de complemento).
  
    
    
Para obtener más información sobre los permisos del complemento, consulte  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Delegación y autorización selectivas
<a name="SelectiveAuthorization"> </a>

Ni los usuarios que van a iniciar un complemento ni los propietarios de los recursos que otorgan los permisos de complemento para tener acceso a un recurso, deben facilitar sus credenciales o contraseña al complemento. En su lugar, gracias a SharePoint 2013, los usuarios y propietarios de recursos podrán otorgar únicamente los permisos específicos que solicita el complemento. Esto es posible porque SharePoint 2013 usa el protocolo de transacción  [OAuth 2.0](http://tools.ietf.org/html/draft-ietf-oauth-v2-22). Para obtener más información sobre OAuth en SharePoint 2013, consulte  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### Acceso entre dominios
<a name="SelectiveAuthorization"> </a>

Una Complemento de SharePoint que incluya una aplicación web remota que use JavaScript para la lógica de acceso a datos podrá usar una biblioteca entre dominios de JavaScript para obtener acceso autorizado a los datos de SharePoint en el arrendamiento donde se ha instalado el complemento. Para obtener más información, consulte  [Cómo obtener acceso a los datos de SharePoint 2013 desde aplicaciones con la biblioteca entre dominios](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Ciclo de vida de un complemento
<a name="SPAppModelArch_Lifecycle"> </a>

El ciclo de vida de una Complemento de SharePoint incluye la publicación, la instalación, la actualización y la desinstalación. Para obtener más información sobre estos temas, consulte  [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md),  [Implementación e instalación de aplicaciones para SharePoint: métodos y opciones](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) y [Proceso de actualización de complementos para SharePoint](sharepoint-add-ins-update-process.md). Tenga en cuenta que existe un mecanismo a través del cual los administradores de inquilinos puede instalar por lotes una Complemento de SharePoint en varios sitios web. Para obtener más información, consulte  [Arrendamientos y ámbitos de implementación de los complementos para SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md).
  
    
    

## Almacenamiento de datos en complementos de SharePoint
<a name="Data"> </a>

Complementos de SharePoint puede crear y obtener acceso a cualquier tipo de datos, lo cual incluye datos estructurados, documentos y archivos multimedia. Estos datos se pueden almacenar en SharePoint o en una ubicación externa. 
  
    
    

### Opciones de almacenamiento de datos estructurados
<a name="StructuredData"> </a>

Un Complemento de SharePoint puede usar casi cualquier tipo de almacenamiento de datos estructurados, tanto dentro como fuera de SharePoint 2013 y en plataformas de Microsoft y que no sean de Microsoft. A continuación encontrará  *algunas*  ubicaciones en las que puede almacenar datos estructurados para un Complemento de SharePoint:
  
    
    

- Listas de SharePoint en una web de complemento
    
  
- SQL Azure
    
  
- Orígenes de datos externos conectados a SharePoint con Servicios de conectividad empresarial de Microsoft (BCS)
    
  
- Un servicio en la nube que no es de Microsoft
    
  
- Una base de datos en su propio servidor
    
  

> **SUGERENCIA**
> Es probable que actualice su Complemento de SharePoint en algún momento. Cuando un Complemento de SharePoint incluye componentes de SharePoint en un complemento web, el proceso de actualización realiza una copia completa del complemento web. Por este motivo, si se usan listas de SharePoint de gran tamaño en un complemento web, el proceso de actualización tardará mucho tiempo en completarse y consumirá un gran número de recursos en el servidor de bases de datos de contenido. No agregue macrodatos en listas de SharePoint del complemento web. 
  
    
    


### Opciones de almacenamiento de datos sin estructurar
<a name="UnStructuredData"> </a>

Los documentos, imágenes, vídeos, archivos de audio y otros tipos de datos sin estructurar que son producidos o usados por un Complemento de SharePoint se pueden almacenar dentro o fuera de SharePoint. Las bibliotecas de documentos son una opción adecuada para los documentos y admiten búsquedas mediante la función de búsqueda de SharePoint. Una biblioteca de activos suele ser una opción adecuada para archivos multimedia. 
  
    
    
Otras opciones son el almacenamiento de blobs en su cuenta de Microsoft Azure o en sus propios servidores. También puede almacenar archivos en plataformas o servicios en la nube que no sean de Microsoft.
  
    
    

### Configuración del complemento y otras opciones de almacenamiento de metadatos
<a name="AppMetadata"> </a>

Los metadatos para un Complemento de SharePoint (como preferencias de usuario, información de ubicación y otras opciones de configuración) se pueden almacenar en diferentes ubicaciones. Una lista oculta de SharePoint puede ser una buena opción. También puede usar el contenedor de propiedades del complemento web. Otra opción, para un complemento hospedado por un proveedor, es usar el almacenamiento de tablas de Microsoft Azure. 
  
    
    

### Opciones de acceso a datos seguro
<a name="DataAccess"> </a>

Las opciones disponibles para el acceso a datos seguro dependen, obviamente, del almacenamiento que se use. El acceso a los datos y las opciones de búsqueda se explican con detalle en otros artículos. Para obtener más información, consulte  [Acceso a datos seguro y modelos de objetos de cliente para complementos de SharePoint](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).
  
    
    

## Administración de complementos
<a name="SPAppModelArch_Managing"> </a>

Los administradores de colecciones de sitios y los administradores de inquilinos pueden supervisar los complementos y cambiar los recursos que se les han asignado. Además, el personal de Microsoft para la tienda de complementos puede identificar complementos y deshabilitarlos.
  
    
    
Para obtener más información sobre la administración de complementos, consulte  [Instalación y administración de complementos para SharePoint](http://msdn.microsoft.com/es-es/library/733647a3-a5d3-475b-967d-3bb627c2a0c2) en TechNet.
  
    
    

### Supervisión de complementos
<a name="SPAppModelArch_Monitoring"> </a>

SharePoint 2013 ofrece una supervisión del estado de los complementos y pone la información a disposición de propietarios, administradores de inquilinos y administradores de granjas en la interfaz de usuario. La mayoría de la documentación para la supervisión del sistema se encuentra en TechNet; por ejemplo  [Supervisar complementos para SharePoint](http://technet.microsoft.com/library/3adafdd2-f276-4a9d-8a74-e06b8916bbc2). Esta sección constituye únicamente una introducción rápida para explicar la forma en que se supervisan los complementos que tiene a la venta.
  
    
    
Algunos tipos de datos se notifican por cada aplicación, mientras que para otros tipos de datos la notificación se realiza por instancia de aplicación. Los elementos principales que notifica la estructura de notificación son los siguientes:
  
    
    

- Uso del complemento como, por ejemplo, la cantidad de veces que se instaló (creando una instancia nueva). 
    
  
- Consumo de los recursos del servidor de cada instancia de complemento.
    
  
- Errores de instalación, actualización y tiempo de ejecución de cada instancia de complemento.
    
  
- Indicador del estado global para cada instancia de complemento en verde, amarillo y rojo.
    
  
Si el complemento incluye componentes de Sitio web de Azure, la estructura de supervisión también sondea Microsoft Azure cada hora para identificar sus datos de error e informa de los errores críticos y sobre los datos de la cuota de almacenamiento en la interfaz de usuario de SharePoint 2013. Los errores de Base de datos SQL de Microsoft Azure no se notifican.
  
    
    
La información que facilita la estructura de supervisión permite a los administradores determinar si el presupuesto para la compra de complementos se ha invertido de forma inteligente, si han implementado más recursos para los complementos y si han deshabilitado un complemento que no funcionaba correctamente.
  
    
    

## Registrar dependencias de complementos
<a name="RegisterDependency"> </a>

Si su Complemento de SharePoint depende de una funcionalidad de SharePoint que no está disponible y no lo puede estar en la web del complemento, no funcionará correctamente y sus clientes se quejarán. Puede asegurarse de que su complemento no está instalado donde las características y los servicios de requisitos no están disponibles registrando las dependencias del complemento en el manifiesto del complemento. La infraestructura de la instalación de Complementos de SharePoint comprobará estos requisitos previos y bloqueará la instalación del complemento si alguno de ellos no está disponible.
  
    
    
En cuanto a los servicios, como Excel, Access o Visio, la infraestructura verificará que el servicio está instalado y que dispone de una licencia.
  
    
    
En cuanto a las características, como una lista de tareas, la infraestructura verificará que la característica está implementada y:
  
    
    
-- está activada en el ámbito **Farm**, **WebApplication** o **Site** (colección de sitios)
  
    
    
o
  
    
    
-- se puede activar con el ámbito **Web** en la web de complemento creada al instalar el complemento.
  
    
    

> **NOTA**
> La infraestructura de instalación del complemento activará automáticamente dichas características en la web de complemento al crearse. 
  
    
    

En las siguientes secciones se proporciona la información detallada necesaria para registrar sus requisitos previos.
  
    
    

### Registrar dependencias de forma implícita con solicitudes de permiso
<a name="PermAsPreq"> </a>

Cuando su complemento necesita acceder a los componentes de SharePoint fuera de la web de complemento, debe solicitar permiso para obtener dichos recursos en la sección **AppPermissionRequests** del manifiesto del complemento. Estas solicitudes de permiso también sirven como registros de requisitos previos, ya que SharePoint deducirá de los permisos que solicita el complemento que necesita determinadas funcionalidades de SharePoint para estar disponible. En muchas situaciones, SharePoint puede deducir todas las funcionalidades que necesita el complemento, de modo que el resto de las secciones de este tema no son necesarias. Sin embargo, los registros de dependencias redundantes no son perjudiciales.
  
    
    

### Registrar dependencias de forma explícita con AppPrerequisites
<a name="Explicit"> </a>

Cuando su complemento tiene una dependencia no implicada por sus solicitudes de permiso, puede registrar cada dependencia con un elemento **AppPrerequisite** en el manifiesto del complemento. Hay tres atributos en este elemento: **Type**, **ID** y **MinimumVersion** (opcional).
  
    
    
Hay tres valores de requisito previo posibles para **Type**;  `Feature`,  `Capablility` y `AutoProvisioning`. El requisito previo Feature (Característica) es una característica de SharePoint que se debe implementar y activar en la web de complemento o en un ámbito más amplio que incluya la web de complemento. Una funcionalidad es un conjunto de características y servicios relacionados que deben estar disponibles en la web de complemento (en la siguiente sección se describe  `AutoProvisioning`).
  
    
    
El valor opcional **MinimumVersion** especifica la versión más antigua de la característica o funcionalidad que necesita su complemento. Los valores de atributo tienen el formato n.n.n.n; por ejemplo, `15.0.0.0`.
  
    
    
El valor **ID** especifica la característica o funcionalidad necesaria. Si **Type** es `Feature`, el **ID** es el GUID con guiones y entre corchetes de la característica; por ejemplo, `{151D22D9-95A8-4904-A0A3-22E4DB85D1E0}`. Si **Type** es `Capability`, el **ID** es el GUID de la funcionalidad. Las funcionalidades se enumeran a continuación. Para obtener el GUID de una funcionalidad, consulte [Elemento de AppPrerequisite (AppPrerequisiteCollection complexType) (Add in SharePoint manifiesto)](http://msdn.microsoft.com/library/791be402-981f-519e-fcde-f24cc3cb4139%28Office.15%29.aspx).
  
    
    

- Servicios de Access 2010
    
  
- Servicios de Access
    
  
- Servicio web de metadatos administrados
    
  
- Servicios de PowerPoint
    
  
- Servicios de almacenamiento seguro
    
  
- Servicio de traducción automática
    
  
- Servicio de perfiles de usuario
    
  
- Servicio de Visio Graphics
    
  
- Servicio de administración de trabajo
    
  
- Duet
    
  
- Flujo de trabajo
    
  
- Buscar
    
  
- EDU
    
  
A continuación se muestra un ejemplo de marcado **AppPrerequisites** sin formato que registra la funcionalidad Flujo de trabajo. Si usa Visual Studio, puede editar el manifiesto del complemento en una herramienta de diseñadores.
  
    
    



```

<AppPrerequisites>
  <AppPrerequisite Type="Capability" ID="{CDD8F991-B459-4512-8048-03D5A03FF27E}" MinimumVersion="15.0.0.0" />
</ AppPrerequisites>
```


## En esta sección
<a name="RegisterDependency"> </a>


-  [Elegir patrones para desarrollar y hospedar un complemento para SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)
    
  
-  [Hospedar webs, webs de complementos y componentes de SharePoint en SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

## Recursos adicionales
<a name="SPAppModelArch_AdditionalResources"> </a>


-  [Complementos de SharePoint](sharepoint-add-ins.md)
    
  
-  [Complementos para SharePoint comparadas con las soluciones de SharePoint](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)
    
  
-  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Diseño de la experiencia de usuario para aplicaciones en SharePoint 2013](ux-design-for-sharepoint-add-ins.md)
    
  
-  [IFrame](http://www.w3schools.com/tags/tag_iframe.asp)
    
  
-  [Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Implementación e instalación de aplicaciones para SharePoint: métodos y opciones](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Proceso de actualización de complementos para SharePoint](sharepoint-add-ins-update-process.md) .
    
  
-  [Arrendamientos y ámbitos de implementación de los complementos para SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  

