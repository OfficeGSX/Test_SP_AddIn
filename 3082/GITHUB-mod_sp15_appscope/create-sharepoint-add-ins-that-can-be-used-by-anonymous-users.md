---
title: Crear complementos de SharePoint que los usuarios anónimos puedan usar
ms.prod: SHAREPOINT
ms.assetid: e858fce6-7a8f-4b1e-b053-64dc75345801
---


# Crear complementos de SharePoint que los usuarios anónimos puedan usar
Aprenda a crear Complementos de SharePoint que puedan usar los usuarios anónimos en sitios públicos de Microsoft SharePoint 2013.
> [!IMPORTANTE]
> Cuando mencionamos SharePoint 2013 *local*  en este artículo, suponemos que se ha instalado Service Pack 1 para SharePoint 2013.
  
    
    


## Requisitos previos para crear Complementos de SharePoint a las que se pueda acceder de forma anónima

El acceso anónimo es posible en las Complementos de SharePoint hospedadas por SharePoint y por el proveedor. Según el tipo que cree, lea uno de los siguientes conjuntos de requisitos previos:
  
    
    

-  [](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#SP15SPhostedapps_bk_prereqs)
    
  
-  [](get-started-creating-provider-hosted-sharepoint-add-ins.md#SP15createselfhostapp_bk_prereq)
    
  
También necesitará una colección de sitios en la instalación de prueba de SharePoint 2013 que esté configurada para el acceso anónimo. Si tiene un Sitio para desarrolladores de Office 365, ya hay una colección de sitios públicos asociada con él que usa una definición de sitios web públicos especial. (Para más información sobre el uso de los sitios web públicos en Microsoft SharePoint Online, vea  [Ayuda de sitio web público para Office 365](http://office.microsoft.com/es-es/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1)). Esa definición de sitio no está disponible para instalaciones locales de SharePoint 2013, de modo que si su instalación de prueba es local, necesitará:
  
    
    

- Una aplicación web de SharePoint que esté configurada para permitir el acceso anónimo, pero esta configuración debe hacerse después de instalar la Complemento de SharePoint que está probando.
    
  
- Una colección de sitios dentro de la aplicación web de SharePoint que esté configurada para el acceso anónimo, pero esta configuración debe hacerse después de instalar la Complemento de SharePoint que está probando.
    
  
- Si el complemento está hospedado en SharePoint y tiene acceso a una lista de SharePoint, una lista de la colección de sitios que esté configurada para el acceso anónimo.
    
  
Las instrucciones de estas tareas se encuentran en la sección  [Configurar una aplicación web de SharePoint y una colección y lista de sitios para el acceso anónimo](#Configuring).
  
    
    

## Limitaciones en el uso de complementos de SharePoint por parte de usuarios anónimos

Al diseñar su Complemento de SharePoint, debe tener en cuenta lo siguiente:
  
    
    

- Si el Complemento de SharePoint se inicia desde SharePoint, tiene que incluir una acción personalizada o un elemento de complemento; si no, se debe iniciar desde un vínculo personalizado de una página. Esto se debe a que los usuarios anónimos no pueden iniciar el complemento desde su icono. Para iniciar los complementos desde el icono, hay que usar una  [página de aplicación](http://msdn.microsoft.com/library/685c8e01-b163-4b5e-888c-421d9ecbb25e%28Office.15%29.aspx) especial que no es accesible para los usuarios anónimos. Otra opción es hacer que el complemento se pueda iniciar desde fuera de SharePoint: en este caso, este debe usar la directiva de autorizaciónsolo el complemento.
    
  
- Un usuario anónimo solo puede iniciar Complementos de SharePoint que otros usuarios hayan instalado, ya que los usuarios anónimos no pueden abrir la página **Agregar un complemento**.
    
  
- Cuando un usuario que ha iniciado sesión visita un sitio web de una aplicación web de SharePoint local configurada para el acceso anónimo, la identidad del usuario cambia a **Cuenta de sistema**. Esta identidad no puede instalar Complementos de SharePoint. Como los usuarios anónimos tampoco pueden instalar complementos, esto significa que nadie puede instalar Complementos de SharePoint cuando la aplicación web está configurada para el acceso anónimo. De igual forma, las Complementos de SharePoint se deben instalar en sitios web de la aplicación web de SharePoint antes de configurar la aplicación web para el acceso anónimo. Si luego hay que instalar otros complementos, la aplicación web se debe cambiar, temporalmente, para deshabilitar el acceso anónimo para que un usuario que haya iniciado sesión pueda instalar los complementos.
    
  
- Para habilitar las API de REST de búsqueda de SharePoint en un sitio web de SharePoint local, hay que configurar algunos XML de consulta. Para obtener más información, vea  [Habilitar consultas de REST de búsqueda anónima](http://msdn.microsoft.com/library/office/jj163876.aspx#bk_AnonymousREST).
    
  
- Los indizadores de búsqueda no rastrean los datos del sitio web de complemento, por lo que los datos personalizados deben implementarse de forma remota o en la web de host. Esto se aplica a todos los Complementos de SharePoint, pero lo destacamos aquí porque los complementos diseñados para el acceso anónimo a menudo requieren funciones de búsqueda.
    
  

## Creación de complementos hospedados por el proveedor a los que se pueda acceder de forma anónima
<a name="Cloud-hosted"> </a>

Para hacer que un complemento hospedado por el proveedor esté accesible para los usuarios anónimos, solo hay que configurarlo para que use la directiva de autorización de solo complemento. Cuando se usa esta directiva, SharePoint ni siquiera incluye un contexto de usuario, así que no importa que el usuario sea anónimo. Solo es necesario que el administrador del inquilino que instala el complemento le conceda a la entidad de seguridad del complemento los permisos necesarios a la web de host, así como los necesarios para la colección en el sitio principal o arrendamiento principal. Solicite permisos a la web de host en el manifiesto del complemento como lo haría con cualquier otro complemento.
  
    
    

> [!NOTA]
> Si el sitio de SharePoint es accesible a los usuarios anónimos, normalmente permite el acceso HTTP en lugar de HTTPS. Puede haber problemas de seguridad cuando se usa la directiva de solo complemento para los complementos en este escenario. Si quiere consultar información detallada y un método para corregirlos, vea  [Lo que todo desarrollador debe saber sobre los complementos de SharePoint, CSOM y los sitios de publicación anónimos](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx). 
  
    
    

Diseñar un complemento para que use la directiva de solo complemento requiere dos cosas:
  
    
    

- Agregar  `AllowAppOnlyPolicy="true"` al elemento **AppPermissionRequests** en el manifiesto del complemento.
    
  
- El código debe pedir un token de acceso de solo complemento desde el servidor de autorización de OAuth. Si trabaja con código administrado, hay API creadas especialmente para este fin en los archivos de código que genera Microsoft Office Developer Tools para Visual Studio: SharePointContext.cs (o .vb) y TokenHelper.cs (o .vb).
    
  
Si quiere detalles sobre la directiva de solo complemento (con fragmentos de código), los permisos del complemento y el manifiesto del complemento, vea estos temas:
  
    
    

-  [Tipos de directivas de autorización de complementos en SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)
    
  
-  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Explorar la estructura del manifiesto de aplicación y el paquete de un complemento de SharePoint](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

## Creación de complementos hospedados por SharePoint a los que se pueda acceder de forma anónima
<a name="SP-hosted"> </a>

Crear un complemento hospedado por SharePoint que puedan ejecutar usuarios anónimos no requiere técnicas especiales. Se crea de igual forma que se crea cualquier complemento hospedado en SharePoint. Para más información, vea  [Empezar a crear complementos hospedados en SharePoint para SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md) y [Completar operaciones básicas con código de biblioteca de JavaScript en SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md).
  
    
    
Una cosa muy importante para que un complemento hospedado en SharePoint pueda ser accesible de forma anónima es que un administrador de inquilinos configure la colección de sitios de SharePoint Online para permitir el acceso anónimo. No hay forma de que los desarrolladores de complementos hospedados en SharePoint hagan esta configuración desde el complemento, desde un receptor de eventos de complemento remoto ni con la infraestructura de instalación de complementos de SharePoint. Puede incluir una lista o una biblioteca personalizadas en el complemento, pero Lenguaje de marcado de aplicaciones de colaboración (CAML) no ofrece ningún modo de preconfigurarlo para el acceso anónimo, de modo que habría que configurarlo después de instalar el complemento.
  
    
    
Si va a comercializar el complemento a través de la Tienda Office y una parte significativa de sus clientes potenciales cree que el complemento sería más útil si fuese accesible para usuarios anónimos, convendría mencionar este requisito de configuración en la descripción del complemento. También podría incluir una versión del procedimiento **Configuración de una colección de sitios de SharePoint Online para el acceso anónimo** que aparece en la parte inferior de la página de soporte técnico del complemento.
  
    
    
Si el complemento usa el modelo de objetos JavaScript (JSOM) de SharePoint, hay un requisito de configuración muy importante. Solo una pequeña parte de las API del JSOM es accesible a usuarios anónimos de manera predeterminada. Las demás requieren que el usuario tenga el permiso **Utilizar interfaces remotas**, y los usuarios anónimos no tienen este permiso. Este requisito se debe desactivar en la colección del sitio principal o en la aplicación web principal de SharePoint del sitio web donde está instalado el complemento, como se describe en [Configurar una aplicación web de SharePoint y una colección y lista de sitios para el acceso anónimo](#Configuring).
  
    
    

> [!NOTA]
> Desactivar el requisito de que los usuarios tengan el permiso **Utilizar interfaces remotas** tiene implicaciones para la privacidad de la información. Para obtener más información, vea [Lo que todo desarrollador debe saber sobre los complementos de SharePoint, CSOM y los sitios de publicación anónimos](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx). 
  
    
    


## Limitaciones de los complementos hospedados en SharePoint para los usuarios anónimos
<a name="SP-hosted"> </a>

Vamos a ser sinceros: para los usuarios anónimos, hay algunas limitaciones importantes en los complementos hospedados en SharePoint que se deben tener en cuenta. Son las siguientes:
  
    
    

- No se puede hacer que una lista o una biblioteca de SharePoint Online sea accesible para los usuarios anónimos. De igual modo, las API del JSOM que conllevan interacción con listas o bibliotecas no funcionan en SharePoint Online para los usuarios anónimos.
    
  
- Un complemento hospedado en SharePoint e instalado en un sitio web de SharePoint  *local*  puede usar todas las API del JSOM. Sin embargo, la aplicación web, la colección de sitios y las listas o bibliotecas deben configurarse *manualmente*  para el acceso anónimo, y el requisito del permiso **Utilizar interfaces remotas** se debe deshabilitar. Si el complemento instala listas o bibliotecas personalizadas en la web de complemento, los usuarios deben configurarlas manualmente para el acceso anónimo después de instalar el complemento.
    
  
- Como los indizadores de búsqueda no rastrean los datos del sitio web de complemento, no hay forma de instalar listas o bibliotecas personalizadas en la web de host y dado que los complementos hospedados en SharePoint no pueden tener componentes ni receptores de eventos remotos, los datos personalizados en un complemento hospedado en SharePoint no se pueden buscar.
    
  

## Configurar una aplicación web de SharePoint y una colección y lista de sitios para el acceso anónimo
<a name="Configuring"> </a>

Si su instalación de prueba de SharePoint es local, tiene que llevar a cabo las dos primeras tareas descritas abajo para probar si su Complemento de SharePoint la pueden utilizar los usuarios anónimos. Los clientes que instalen su Complemento de SharePoint también deben hacer estas tareas para la colección de sitios principal y la aplicación web de los sitios web donde esté instalada su Complemento de SharePoint.
  
    
    

> [!IMPORTANTE]
> Si es posible, debe instalar el Complemento de SharePoint en un sitio web  *antes*  de llevar a cabo las dos primeras tareas. Los complementos no se pueden instalar en ningún sitio web de una aplicación web de SharePoint local cuando la aplicación web se ha configurado para el acceso anónimo. Si la aplicación web ya se ha configurado para el acceso anónimo, tiene que revertir este valor temporalmente para instalar el complemento.
  
    
    


### Para configurar la aplicación web principal para el acceso anónimo


1. En **Administración Central**, elija **Administración de aplicaciones** y luego **Administrar aplicaciones web**.
    
  
2. (Opcional) Si no quiere permitir el acceso anónimo en alguna de las aplicaciones web de SharePoint, cree una nueva aplicación web. (Aunque el formulario de creación de aplicaciones web le da la opción de permitir el acceso anónimo,  *no use esta opción*  . El acceso anónimo no se debe habilitar hasta *después*  de haber instalado la Complemento de SharePoint). Para más información, vea [Create a web application in SharePoint 2013](http://msdn.microsoft.com/library/121c8d83-a508-4437-978b-303096aa59df.aspx).
    
  
3. En la lista de aplicaciones web, seleccione la que va a ser accesible para los usuarios anónimos y luego seleccione **Proveedores de autenticación** en la cinta de opciones.
    
  
4. En el globo que se abre, elija la zona para la que se habilitará el acceso anónimo. Suele ser **Internet** o **Predeterminada**. Se abrirá el formulario **Editar autenticación**.
    
  
5. Active la casilla **Habilitar el acceso anónimo** si no lo está. Esto define un valor predeterminado que se puede desactivar en ciertas colecciones de sitios, pero si no habilita esta opción para la aplicación web, no la podrá habilitar para ninguna colección de sitios.
    
  
6. (Opcional) Desactive la casilla **Requerir permiso para Utilizar interfaces remotas**. Esto permite que el código y el script que se ejecutan en el contexto de un usuario anónimo puedan hacer llamadas al modelo de objetos de cliente de SharePoint en todas las colecciones de sitios. No puede volver a habilitar el requisito en ninguna colección de sitios. Si deja la casilla activada, significa que, de forma predeterminada, los usuarios anónimos no pueden obtener acceso a los modelos de objetos de cliente, pero puede deshabilitar el requisito (y darles acceso) en determinadas colecciones de sitios.
    
    > [!NOTA]
      > Al desarrollar Complementos de SharePoint para usuarios anónimos, este valor solo es importante para los complementos hospedados en SharePoint. Los Complementos de SharePoint hospedados por el proveedor que están diseñados para usuarios anónimos usan una técnica que hace que los permisos del usuario sean irrelevantes. Para más información, vea la sección  [Creación de complementos hospedados por el proveedor a los que se pueda acceder de forma anónima](#Cloud-hosted) anterior.
7. Elija **Guardar** para cerrar el formulario.
    
  
8. Con la aplicación web aún resaltada, seleccione **Directiva de anonimato** en la cinta de opciones.
    
  
9. En el formulario **Restricciones de acceso anónimo**, elija la zona y asegúrese de que el botón de radio **Ninguno** está habilitado. Si la Complemento de SharePoint que está probando solo necesita derechos para los datos de SharePoint, habilite **Denegar escritura**.
    
    > [!NOTA]
      > Este es otro valor que, en el contexto del desarrollo de Complementos de SharePoint para usuarios anónimos, solo sirve para los complementos hospedados en SharePoint. 
10. Si ha creado una nueva aplicación web en el paso 2, debe crear una colección de sitios en ella.
    
  

### Para configurar una colección de sitios local para el acceso anónimo


1. En la página principal de una colección de sitios de la aplicación web, seleccione el icono de engranaje y luego **Configuración del sitio**.
    
  
2. En la sección **Usuarios y permisos** de la página **Configuración del sitio**, seleccione **Permisos de sitio**.
    
  
3. Elija **Acceso anónimo** en la cinta de opciones.
    
  
4. En el formulario **Acceso anónimo**, elija **Sitio web completo** o **Listas y bibliotecas** en función del nivel de acceso que necesite el complemento que está probando. Independientemente de la opción que elija, los usuarios anónimos no pueden obtener acceso a una lista o biblioteca con un complemento hospedado en SharePoint a menos que la lista de biblioteca esté individualmente configurada para el acceso anónimo. A continuación se describe cómo hacerlo.
    
  
5. Si el complemento que está probando necesita obtener acceso al modelo de objetos de cliente de SharePoint, asegúrese de que la casilla **Requerir permiso para usar interfaces remotas** *no*  esté activada. Si está atenuada en gris, quiere decir que el requisito estaba desactivado en el nivel de la aplicación web, lo que tiene el mismo efecto que desactivar la casilla. (Como hemos indicado en la tarea anterior, este valor, en el contexto del desarrollo de Complementos de SharePoint para usuarios anónimos, solo sirve para las aplicaciones hospedadas en SharePoint).
    
  

> [!IMPORTANTE]
> La tarea siguiente solo se puede llevar a cabo en un sitio web público en SharePoint Online. (Para más información sobre el uso de los sitios web públicos en Microsoft SharePoint Online, vea  [Ayuda de sitio web público para Office 365](http://office.microsoft.com/en-gb/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1)). 
  
    
    


### Para configurar una colección de sitios de SharePoint Online para el acceso anónimo


1. Inicie sesión en Office 365 como administrador de inquilinos.
    
  
2. En la página principal de la colección de sitios, elija **Hacer público el sitio web**, cerca de la esquina superior derecha de la página. Esta acción también desactiva el requisito **Permiso para utilizar interfaces remotas**.
    
  
Si está desarrollando un complemento hospedado en SharePoint que obtiene acceso a una lista de SharePoint, también debe llevar a cabo la tarea siguiente en su colección de sitios. Los clientes que usen su complemento tienen que hacer lo mismo en todos los sitios web donde esté instalado el complemento. Un complemento no puede configurar una lista para el acceso anónimo ni mediante programación ni de forma descriptiva. Los complementos pueden instalar listas personalizadas, pero no existe ninguna forma de preconfigurar estas listas para el acceso anónimo. La configuración se debe hacer manualmente después de instalar el complemento. Si está desarrollando un Complemento de SharePoint hospedado por el proveedor para usuarios anónimos, este procedimiento no será necesario.
  
    
    

> [!NOTA]
> Este procedimiento no se puede llevar a cabo en una colección de sitios de SharePoint Online, de modo que los complementos hospedados por SharePoint, que están instalados en SharePoint Online y que se han diseñado para el uso de usuarios anónimos no pueden obtener acceso a las listas ni a las bibliotecas. 
  
    
    


### Para configurar una lista o biblioteca para el acceso anónimo


1. Vaya a una lista o biblioteca a la que la Complemento de SharePoint que esté probando obtenga acceso y abra la **Configuración de lista** o la **Configuración de biblioteca**. En el caso de un sitio de SharePoint Online, deben iniciar sesión como administrador de inquilinos.
    
  
2. Elija **Permisos para esta lista/biblioteca**.
    
  
3. En la página que se abre, elija **Dejar de heredar permisos** en la pestaña **Permisos** y luego elija **Aceptar** en el mensaje de confirmación.
    
  
4. Elija **Acceso anónimo** en la pestaña **Permisos**.
    
  
5. En el formulario **Acceso anónimo** que se abre, seleccione todos los permisos que necesiten los usuarios de la Complemento de SharePoint. Puede conceder permisos para ver, editar, agregar y eliminar elementos. No puede conceder control total, por lo que los usuarios anónimos no pueden cambiar el esquema de la lista ni cambiar su configuración.
    
  

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Desarrollar complementos para SharePoint](develop-sharepoint-add-ins.md)
    
  

