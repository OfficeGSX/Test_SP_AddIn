---
title: Publicar complementos para SharePoint con Visual Studio
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# Publicar complementos para SharePoint con Visual Studio
Aprenda a publicar su Complemento de SharePoint con Microsoft Visual Studio 2013 o Visual Studio. Si el complemento tiene una aplicación web asociada, la implementará en primer lugar. Luego, como sucede con todas las Complementos de SharePoint, empaquetará la Complemento de SharePoint y la publicará. Si lo desea, puede elegir enviar el complemento para incluirlo en la Tienda Office.
## Requisitos previos
<a name="Prereq"> </a>


  
    
    

-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284)
    
    o
    
     [Visual Studio 2012](https://www.microsoft.com/es-es/download/details.aspx?id=30682) y Office Developer Tools para Visual Studio. Para descargar las herramientas, consulte "Herramientas" en la [página Descargar](http://go.microsoft.com/fwlink/?LinkId=234393). (El nuevo Administrador de publicación no está disponible en Visual Studio 2012 o versiones anteriores).
    
  
- Microsoft SharePoint 2013
    
  

## Publicar con Visual Studio 2013
<a name="VS2013"> </a>

Si la Complemento de SharePoint hospedada por el proveedor tiene una aplicación web, implemente los archivos para esta en primer lugar. Después, como para todas las Complementos de SharePoint, empaquete la Complemento de SharePoint y publíquela.
  
    
    

> **IMPORTANTE**
> Para asegurarse de que el identificador de cliente y los valores de secreto de cliente de SharePoint se publican con el proyecto web para permitir al contenido web tener acceso a los datos de SharePoint, publique su proyecto de la Complemento de SharePoint desde la página **Publicación de los complementos**. Para tener acceso a esta página, abra el menú contextual de la Complemento de SharePoint (no el menú contextual de la aplicación web) y elija el comando **Publicar**. 
  
    
    


### Paso 1: Implementar la aplicación web

La Complemento de SharePoint suele tener asociada una aplicación web host, que se debe implementar en un servidor web. Para más información sobre el uso que debe hacer del asistente para la publicación web, consulte el artículo sobre  [cómo implementar un proyecto web mediante la publicación con un solo clic en Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).
  
    
    

### Para abrir la página Publicación de los complementos


- En el **Explorador de soluciones**, abra el menú contextual para el proyecto de Complemento de SharePoint y, a continuación, elija **Publicar**.
    
    Aparecerá la página **Publicación de los complementos**.
    
  

### Para seleccionar o crear un perfil


- En la lista **Perfil actual**, seleccione un perfil para importarlo o bien **<Nuevo …>** para crear uno nuevo.
    
    Un perfil de publicación especifica el servidor en el que está implementada la aplicación web, las credenciales que se necesitan para iniciar sesión en el servidor, las bases de datos que se implementarán (si corresponde) y otras opciones de implementación. Puede crear diferentes perfiles de publicación que se adapten a sus necesidades. Por ejemplo, podría crear un perfil para realizar pruebas y otro para publicaciones.
    
    Si elige **<Nuevo…>**, se abrirá el asistente **Crear perfil de publicación**. Puede usar este asistente para importar un perfil de publicación de un proveedor de hospedaje de sitios web, como Azure, o para crear un perfil y después agregar manualmente el nombre del servidor, las credenciales y el resto de los valores. Si ha creado un perfil nuevo en lugar de importar un perfil existente, tendrá que dar el identificador y los valores de secretos de cliente, como se describe en  [Cómo registrar complementos para SharePoint 2013](http://msdn.microsoft.com/library/jj687469.aspx) y [Procedimiento para crear identificadores y claves secretas de cliente en el Panel de vendedores de Microsoft](http://msdn.microsoft.com/library/office/jj220036.aspx).
    
    Si tiene previsto enviar su Complemento de SharePoint a la Tienda Office, asegúrese de usar el identificador de cliente y los valores de secreto de cliente que se hayan creado en el panel de vendedores. Puede usar los identificadores de cliente y los valores de secreto de cliente que se hayan generado con la página appregnew.aspx durante la fase de desarrollo, pero los complementos que envíe a la Tienda Office deben usar identificadores de cliente y secretos de cliente que se hayan obtenido del panel de vendedores. Asimismo, debe crear el perfil de publicación en el sitio de Azure para, después, importarlo a Visual Studio en lugar de crear un perfil en el asistente **Crear perfil de publicación**. Cuando crea un perfil en Azure, todos los valores de la pestaña **Conexión** se proporcionan de forma automática en Visual Studio. Para más información sobre cómo importar o crear un perfil de publicación, vea [Crear un perfil de publicación](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile).
    
    > **SUGERENCIA**
      > Si no puede publicar contenido web directamente, puede crear un paquete de implementación web para que un administrador lo implemente en la web en su lugar. Para crear un paquete de implementación web, cree un perfil nuevo, elija la pestaña **Conexión** y, después, seleccione **Paquete de Web Deploy** en la lista **Método de publicación**. 

### Para implementar su proyecto de aplicación web


1. En la página **Publicación de los complementos**, seleccione el botón para **implementar el proyecto web**.
    
    Se abrirá el cuadro de diálogo **Publicación web**.
    
  
2. En las pestañas **Conexión** y **Configuración**, rellene los valores que falten.
    
    Para cambiar el procedimiento de publicación de los archivos para la Complemento de SharePoint o si el complemento utiliza una base de datos externa, seleccione la pestaña **Configuración**. Consulte la sección "Configurar la pestaña Configuración" en el artículo sobre el  [procedimiento para implementar un proyecto web con la publicación con un solo clic en Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).
    
  
3. Para revisar qué elementos cambiarán cuando la aplicación web se implemente, seleccione el botón **Iniciar vista previa** en la ficha **Vista previa**.
    
  
4. Seleccione el botón **Publicación** para implementar el proyecto de aplicación web.
    
  

### Paso 2: Empaquetar el complemento


1. En la página **Publicación de los complementos**, seleccione el botón para **empaquetar el complemento**.
    
    Aparecerá el asistente **Publicar complementos para Office y SharePoint**.
    
  
2. En el cuadro de texto **¿Dónde está hospedado el sitio web?**, introduzca la dirección URL del sitio web que hospedará los archivos de contenido de la Complemento de SharePoint.
    
    Debe especificar una dirección que comience con el prefijo "https". Consulte  [¿Por qué los complementos deben estar protegidos mediante SSL?](http://msdn.microsoft.com/library/jj591603#bk_q7).
    
    > **NOTA**
      > Los sitios web de Azure proporcionan automáticamente un extremo https. Si publica el complemento en un sitio de la Tienda Office o la Tienda Office, la dirección debe empezar con un prefijo https. Sin embargo, si publica el complemento en un sitio local, podrá usar un prefijo http. 

    En el cuadro de texto **¿Cuál es el identificador de cliente del complemento?**, debería aparecer el identificador de cliente que haya introducido en el perfil de publicación.
    
    Si ha utilizado un valor de marcador de posición para el identificador de cliente hasta este punto, tendrá que agregar un identificador de cliente real ahora. Esta información está incrustada en el paquete .app y permite al contenido web comunicarse con SharePoint en el sitio activo.
    
  
3. Elija el botón **Finalizar**.
    
    Visual Studio genera los archivos necesarios para publicar su Complemento de SharePoint y, después, abre la carpeta de salida de la publicación. Para más información sobre cómo instalar el complemento, vea  [Instalación y administración de complementos para SharePoint 2013](http://technet.microsoft.com/library/fp161232.aspx).
    
  

### Paso 3: Publicar la Complemento de SharePoint en la Tienda Office

Lleve a cabo el siguiente procedimiento si desea enviar la Complemento de SharePoint a la Tienda Office.
  
    
    

1. En la página **Publicación de los complementos**, elija el botón para **visitar el panel de vendedores** y, después, inicie sesión en su cuenta de Panel del vendedor de Microsoft.
    
    Vea  [Directivas e instrucciones del catálogo de soluciones de aplicaciones para Office y SharePoint](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx).
    
  
2. Elija **agregar una nueva aplicación**, rellene la información y, a continuación, envíe el complemento a la Tienda Office. Para más información, vea  [Usar el panel de vendedores para enviar complementos de Office y SharePoint y aplicaciones de Office 365 a la Tienda Office](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx).
    
  

## Publicar con Visual Studio 2012
<a name="VS2012"> </a>

Cuando esté todo listo para empaquetar la Complemento de SharePoint, abra el asistente para **Publicar complementos de Office** que prepara los archivos de la Complemento de SharePoint para su publicación.
  
    
    

### Paso 1: Empaquetar la Complemento de SharePoint


1. En el **Explorador de soluciones**, abra el menú contextual para el proyecto de Complemento de SharePoint y, a continuación, elija **Publicar**.
    
    Aparece el asistente para **Publicar complementos de Office**. El tipo de Complemento de SharePoint que va a empaquetar determina los paquetes que aparecen en el asistente. Si el complemento va estar hospedado en SharePoint, aparecerá únicamente **Resumen**. Si va a estar hospedado mediante el proveedor, aparecerán también las páginas **Perfil** y **Hospedaje**.
    
  
2. Si Complemento de SharePoint está hospedada en el proveedor, especifique un nombre de perfil de publicación en la lista **¿Qué perfil desea publicar?** y, a continuación, presione el botón **Siguiente**.
    
    El perfil de publicación guarda la información que especifique en la página **Hosting**.
    
  
3. En la lista **¿Dónde se hospeda el sitio web?**, especifique la dirección URL de la aplicación web que vaya a hospedar a Complemento de SharePoint.
    
  
4. En los cuadros debajo de **¿Cuál es la identidad del complemento?**, especifique el identificador de cliente y la clave secreta del cliente del complemento y, después, presione el botón **Siguiente**.
    
    Consulte  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md).
    
  
5. Para todos los tipos de Complementos de SharePoint, seleccione la casilla **Abrir carpeta de salida tras empaquetar correctamente**, si no se encuentra seleccionada, y, después, presione el botón **Finalizar**.
    
    Visual Studio genera todos los archivos que necesita para publicar la Complemento de SharePoint. Puede encontrar estos archivos en la carpeta  `app.Publish` de la carpeta de salida del proyecto (por ejemplo, `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`). Esa carpeta contendrá un archivo .app para la Complemento de SharePoint y diversos archivos para la aplicación web (en caso de que la Complemento de SharePoint esté basada en nube). Todas las Complementos de SharePoint incluyen un archivo .app, que es el manifiesto del complemento para publicar la Complemento de SharePoint. Las Complementos de SharePoint que están hospedadas por el proveedor también incluyen archivos para la publicación de la aplicación de la web de host.
    
  

### Paso 2: Publicar la aplicación web

Si la Complemento de SharePoint se hospeda en un proveedor, normalmente tendrá una aplicación web host asociada que tendrá que publicar en un servidor web. Visual Studio genera un paquete de implementación y un script para ayudarle a realizar esta tarea.
  
    
    
El paquete de implementación de un proyecto de aplicación web se encuentra en un archivo comprimido (.zip) en la carpeta  `app.publish`. Además del archivo .zip, la carpeta  `app.publish` incluye los archivos siguientes:
  
    
    


|**Archivo**|**Descripción**|
|:-----|:-----|
| _ProjectName_.deploy.cmd  <br/> |Este archivo es un archivo por lotes de línea de comandos que invoca a Web Deploy de forma que podrá instalar más fácilmente el paquete en un símbolo del sistema.  <br/> |
| _ProjectName_.SetParameters.xml  <br/> |Este archivo contiene parámetros que se pasan a Web Deploy cuando use el archivo deploy.cmd para instalar el paquete. La configuración del paquete de Visual Studio determina el valor predeterminado que se especifica en cada parámetro. Puede cambiar estos valores si, por ejemplo, desea instalar la aplicación web en varios servidores y usar distintas configuraciones para cada servidor.  <br/> |
| _ProjectName_.SourceManifest.xml  <br/> |Este archivo contiene los parámetros de configuración que Visual Studio pasa a Web Deploy y que este último usa para crear el paquete web. Web Deploy necesita este archivo para crear el paquete. Este archivo no se usa cuando el paquete está instalado.  <br/> |
   
Para obtener un procedimiento detallado, consulte el artículo para  [instalar un paquete de implementación con el archivo dploy.cmd que se creó con Visual Studio](http://go.microsoft.com/fwlink/?LinkID=254954)
  
    
    

### Paso 3: Publicar la Complemento de SharePoint
<a name="Publish"> </a>

Para publicar la Complemento de SharePoint, cargue el archivo del manifiesto (.app) del complemento en la Tienda Office, el catálogo de complementos para Office, SharePoint, un recurso compartido de archivos o el catálogo de Exchange. El manifiesto del complemento se encuentra en la carpeta  `app.publish`, como por ejemplo,  `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`. Para más información sobre cómo publicar la Complemento de SharePoint, consulte  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md).
  
    
    

## Recursos adicionales
<a name="Additional"> </a>


-  [Publicar aplicaciones para SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Publicación de la aplicación para Office 2013 Preview](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)
    
  

