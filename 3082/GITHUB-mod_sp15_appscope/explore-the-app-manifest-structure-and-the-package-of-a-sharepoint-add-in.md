---
title: Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint
Obtenga información sobre la estructura del paquete de complemento y sobre el archivo de manifiesto de un Complemento de SharePoint.
## Complemento para la estructura de paquetes de SharePoint
<a name="Package"> </a>

Un paquete de Complemento de SharePoint es un archivo con una extensión ".app" que cumple las  [Open Packaging Conventions (OPC, Convenciones de empaquetado abierto)](http://msdn.microsoft.com/es-es/magazine/cc163372.aspx). El paquete contiene los elementos siguientes:
  
    
    

- **Manifiesto de complemento:** se trata de un archivo obligatorio con el nombre appmanifest.xml. Este archivo indica a SharePoint 2013 algunas de las propiedades importantes del complemento, como son el título y los permisos que necesita para ejecutarse. Para obtener más información sobre el contenido de este archivo, consulte [Complemento para el archivo de manifiesto de SharePoint](#AppManifest).
    
  
- **Paquetes de soluciones para SharePoint:** de forma opcional, el complemento puede incluir un paquete de soluciones (archivo .wsp) que contiene los componentes de la web de complemento. Entre estos componentes, es posible encontrar páginas, instancias de lista, vistas, documentos, funciones de ámbito **Web** y otros componentes de SharePoint 2013. (Para obtener más información sobre qué componentes de SharePoint se pueden incorporar en un Complemento de SharePoint, consulte [Tipos de componentes de SharePoint que se pueden encontrar en un complemento para SharePoint](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps). El archivo .wsp también puede contener Complementos de Office. Los componentes del archivo .wsp se implementan en la web de complemento. Para obtener un ejemplo de un paquete de complemento que incluya un paquete de soluciones para SharePoint, consulte  [Crear un complemento hospedado por el proveedor que incluya un tipo de contenido y una lista de SharePoint personalizados](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md).
    
  
- **Características de la web de host con acciones personalizadas o elementos del complemento:** además de los componentes de SharePoint 2013 que se implementan en la web de complemento, un Complemento de SharePoint también puede implementar una o más acciones personalizadas (elementos de menú contextual o extensiones de cinta) en la web de host. Para ello, se debe incluir en el paquete de complemento una característica que no se incorpora en el archivo .wsp del paquete y que implementa los componentes que se van a incluir la web de host. Esta característica "fantasma" se denomina característica de la web de host. Los elementos del complemento se implementan en la web de host de la misma forma. La característica de la web de host está compuesta por un archivo feature.xml de SharePoint 2013 estándar y uno o más archivos elements.xml asociados. Los archivos elements.xml para una acción personalizada, por ejemplo, incorporan el marcado de **CustomAction** para la acción personalizada. Por otra parte, también pueden incorporar el marcado para los elementos del complemento. Únicamente estos dos tipos de componente pueden encontrarse en la característica de la web de host y no aparecen desglosados en el manifiesto del complemento. Sin embargo, son "elementos" en términos de OPC, además de que existe una relación de OPC explícita entre el manifiesto del complemento y cada uno de estos archivos. Para obtener un ejemplo de paquete de complemento que incluya una característica de la web de host, consulte [Crear acciones personalizadas para implementarlas con complementos de SharePoint](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
    
    > **NOTA**
      > Los administradores de inquilinos cuentan con la opción de instalar por lotes un Complemento de SharePoint en varios sitios web. Los complementos que se instalan de esta forma se dice que tienen un ámbito de **Tenant**. Si el complemento no se instaló por lotes y, en su lugar, se instaló en cada sitio web de forma independiente, se dice que tiene ámbito **Web**. Si la característica de la web de host incluye extensiones de cinta o elementos de complemento, no se implementarán en los sitios web de host en caso de que el complemento se hubiera instalado por lotes, de forma que, en los complementos de ámbito de inquilino, solamente se implementan los elementos del menú contextual. El ámbito de complemento no debe confundirse con el ámbito de característica. Este último hace referencia al lugar donde se implementan los elementos en una característica. Las posibilidades son **Farm**, **WebApplication**, **Site** (es decir la colección de sitios) y **Web**. Solamente se permite la opción **Web** para las características de Complementos de SharePoint (tanto las características de la web de host como las características de un archivo .wsp de un paquete de complementos). El ámbito de complemento hace referencia al ámbito en el que se instala un complemento. Las posibilidades son **Web**, en cuyo caso el complemento se instaló en uno o más sitios web de forma individual, y **Tenant**, donde el complemento se instaló por lotes para la totalidad o un subconjunto de los sitios web en el arrendamiento de un cliente. Para obtener más información sobre los ámbitos de **Tenant** y **Web**, consulte  [Arrendamientos y ámbitos de implementación de los complementos para SharePoint](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md). 
- **Archivos de recursos de localización (.resx):** se usan para localizar ciertos aspectos del manifiesto del complemento (como el título del complemento) y de las características de la web de host en el paquete de complemento. (Los elementos individuales del paquete de complemento que se encuentran dentro de su propio paquete, como por ejemplo, los archivos .wsp, los paquetes de Sitios web de Azure y los manifiestos de complemento, cuentan cada uno de ellos con sus propios procedimientos de localización que se aplicarían exactamente como si los elementos en cuestión no formasen parte de un Complemento de SharePoint). Para obtener un ejemplo de un paquete de complemento que incluya archivos .resx para una característica de la web de host, consulte [Localizar complementos para SharePoint](localize-sharepoint-add-ins.md).
    
  
- **Manifiestos de Office Add-ins:** de forma opcional, es posible que haya más de un manifiesto de Complementos de Office para cada uno de los paquetes de Complemento de Office. Este elemento solamente puede incluirse en el paquete de complementos si el complemento se va a cargar a un catálogo de complementos corporativos de SharePoint 2013, y no en el catálogo de soluciones público. Vea [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md) para obtener más información.
    
  

## Complemento para el archivo de manifiesto de SharePoint
<a name="AppManifest"> </a>

Cada Complemento de SharePoint incluye un archivo appmanifest.xml. Este archivo indica a SharePoint lo que debe saber acerca del complemento y define las propiedades más importantes del mismo. A continuación, se describen algunos de los elementos que se especifican en el manifiesto:
  
    
    

- El nombre interno, el identificador del producto y la versión del complemento.
    
  
- La dirección URL de la página de inicio, que es la página que se abre al iniciar el complemento; podría ser una página de la web de complemento, una página en la nube o una página en un servidor web del ISV.
    
    > **NOTA**
      > En algunos casos, puede haber restricciones en cuanto al tipo de archivos que se pueden especificar en el elemento **StartPage**. Para obtener más información, vea  [Elemento StartPage (PropertiesDefinition complexType) (Add in SharePoint manifiesto)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx). > Cuando se combina más de un parámetro de consulta en el valor de la **StartPage**, debe usar la Y comercial codificada " `&amp;amp;`" en lugar de " `&amp;`" o un punto y coma para unirlos. 
- Otras propiedades del complemento. Entre ellas se incluyen el título y las configuraciones regionales que admite el complemento (ambas son necesarias), las direcciones URL de los servicios que controlan los eventos posteriores a la instalación, posteriores a la actualización y previos a la desinstalación, y la plantilla web que se usa para crear la web de complemento.
    
  
- Solicitudes de permisos que necesita el complemento para recursos de SharePoint que están fuera de la web de complemento.
    
  
- Una identificación con fines de autenticación y autorización para la entidad de seguridad del complemento. Los permisos se otorgan a la entidad de seguridad. Esta acción no es necesaria para complementos hospedados en SharePoint.
    
  
- Una lista de los requisitos previos, si existen, que deben estar disponibles para que el complemento se pueda instalar. Por ejemplo, es posible que se deban instalar y activar ciertas características o que algunos servicios se deban licenciar e instalar.
    
  

> **NOTA**
> El archivo de manifiesto del complemento es el único elemento necesario en el paquete de complemento, pero no todos los elementos de la lista anterior son elementos obligatorios del archivo. 
  
    
    

Para obtener información detallada sobre el marcado del manifiesto del complemento, vea el nodo  [Referencia de esquema para manifiestos de SharePoint Add-ins](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx). Este tema no sustituye la información de este nodo como, por ejemplo, la información sobre los atributos y elementos necesarios. Además, los manifiestos de los complementos de SharePoint tienen un esquema diferente de los manifiestos de Complemento de Office. Encontrará información sobre estos últimos en  [Referencia de esquema para manifiestos de aplicaciones para Office (v1.1)](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx).
  
    
    
A continuación se presenta un ejemplo de un archivo appmanifest.xml. Observe que en este ejemplo, la página de inicio del complemento no es una página del sitio de SharePoint, sino que es una página ASP.NET que se encuentra en un servidor remoto. La dirección URL de la página incluye una cadena de consulta que le pasa a la aplicación web remota la dirección URL de la web de host. La porción "{HostUrl}" de la cadena es un token que se resuelve cuando se inicia el complemento. El complemento solicita el permiso de escritura a todas las listas de la web de host. La entidad de seguridad del complemento que debe recibir este permiso es la aplicación web remota.
  
    
    
Debe usar el elemento **SupportedLocales** o el elemento **SupportedLanguages** en el manifiesto del complemento. **SupportedLanguages** está en desuso y se ha sustituido por **SupportedLocales**. El elemento **SupportedLanguages** seguirá funcionando incluso después del lanzamiento, pero debería evitar usarlo. Para obtener más información sobre estos elementos, consulte [Elemento de SupportedLocales (PropertiesDefinition complexType) (Add in SharePoint manifiesto)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx) y [Elemento de SupportedLanguages (PropertiesDefinition complexType) (Add in SharePoint manifiesto)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx).
  
    
    

> **NOTA**
> Los valores del atributo **Scope** del elemento **AppPermissionRequest** se encuentran estructurados como URI, pero son en realidad cadenas literales. En el ejemplo siguiente, ningún elemento del valor **Scope** es un marcador. Para obtener más información sobre permisos, visite [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md). 
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### Tokens de la dirección URL en el manifiesto del complemento

SharePoint 2013 proporciona diversos tokens que se pueden usar en el elemento **StartPage** y en otros lugares de complementos y componentes de complementos para representar información que se desconoce hasta el momento de ejecutar el complemento. La infraestructura de SharePoint 2013 resuelve estos tokens. Algunos se usan al inicio de la dirección URL y otros se pueden usar en el interior de esta, como el valor de un parámetro de consulta. Estos tokens, así como algunos otros, también se pueden usar en diversos contextos de desarrollo de SharePoint 2013. Para obtener información general sobre otros tokens y direcciones URL en [Cadenas de dirección URL y tokens en complementos para SharePoint](url-strings-and-tokens-in-sharepoint-add-ins.md), consulte SharePoint 2013 [Formularios de cadenas de direcciones URL](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx).
  
    
    

> **NOTA**
> Estos tokens no se usan en el atributo **Scope** de un elemento **AppPermissionRequest**. 
  
    
    


## Recursos adicionales
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes del panorama de desarrollo y arquitectura de los complementos para SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Referencia de esquema para manifiestos de SharePoint Add-ins](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [OPC Nuevo estándar para empaquetar sus datos](http://msdn.microsoft.com/es-es/magazine/cc163372.aspx)
    
  
-  [Conexión de aplicación de capa de datos (DAC)](http://msdn.microsoft.com/es-es/library/ee210546.aspx)
    
  
-  [Web Deploy 2.0](http://www.iis.net/downloads/microsoft/web-deploy)
    
  

