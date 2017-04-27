---
title: Controlar eventos de complemento en el complemento hospedado por el proveedor
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# Controlar eventos de complemento en el complemento hospedado por el proveedor
Aprenda a personalizar la instalación de un Complemento de SharePoint hospedado por el proveedor.
Este es el séptimo de una serie de artículos sobre los conceptos básicos de desarrollo de Complementos de SharePoint hospedados por el proveedor. Primero debe familiarizarse con los  [Complementos de SharePoint](sharepoint-add-ins.md) y con los anteriores artículos de esta serie:





-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Dar al complemento hospedado por el proveedor la apariencia de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [Incluir un botón personalizado en el complemento hospedado por el proveedor](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [Obtener una introducción rápida al modelo de objetos de SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [Agregar operaciones de escritura de SharePoint al complemento hospedado por el proveedor](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [Incluir un elemento de complemento en el complemento hospedado por el proveedor](include-an-add-in-part-in-the-provider-hosted-add-in.md)



> **NOTA**
> Si ha estado trabajando en esta serie sobre complementos hospedados por el proveedor, ya tiene una solución de Visual Studio que puede usar para continuar con este tema. También puede descargar el repositorio en  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) y abrir el archivo BeforeAdd-inEventHandlers.sln.




En este artículo, personalizaremos el tratamiento de un tipo de eventos en SharePoint llamados eventos de complemento. En concreto, crearemos controladores para los eventos de instalación y desinstalación de complemento. También existen eventos de lista y elementos de lista que pueden recibir el control personalizado. Aprenderá sobre ellos en un artículo posterior de esta serie. Todos estos eventos se desencadenan en SharePoint, pero el código personalizado que controla cada evento está en la aplicación web remota. SharePoint se configura para que llame al controlador personalizado registrando la dirección URL del controlador con el evento de SharePoint.
## Dos lugares para implementar mediante programación componentes de SharePoint

Queremos que nuestro complemento Chain Store cree e implemente automáticamente las listas **Empleados locales** y **Envíos esperados**. Un complemento puede implementar los componentes de SharePoint, como una lista personalizada, en cualquier momento. Pero cuando un complemento depende de un componente específico, como una lista personalizada, el componente debe implementarse sin falta  *antes*  de que los usuarios empiecen a trabajar con el complemento. Para estos componentes vitales, la lógica de implementación personalizada puede ir en dos sitios:




- En un controlador para el evento de instalación de complemento.


- En la lógica "de primera vista" que se ejecuta la primera vez que se inicia el complemento en SharePoint.


Decidir qué es lo mejor para un complemento determinado es un tema avanzado. En este artículo, podemos mencionar solo algunos puntos de comparación:




- Un controlador de instalación personalizado debe completarse en 30 segundos. No existe ningún límite con respecto a la duración de la lógica de la primera vista.


- Si algo va mal durante la instalación de un complemento, SharePoint revertirá todo lo que hizo como parte de la instalación. Un controlador de instalación personalizado se ejecuta  *después*  de que SharePoint haya hecho todo lo que debía para instalar el complemento, por lo que un controlador personalizado puede participar de este sistema. Por ejemplo, si la lógica personalizada produce una excepción, puede indicarle a SharePoint que revierta la instalación completa del complemento. Si algo va mal en la lógica personalizada de primera vista, sin embargo, el complemento permanece instalado y probablemente no funcionará correctamente.


- SharePoint no se da por vencido si tiene que revertir la instalación de un complemento. Reintentará la instalación de forma inmediata. Hace hasta 4 intentos (se aplica el límite de 30 segundos en cada intento). Cada vez que reintenta, el controlador de instalación personalizada se ejecuta de nuevo  *desde el principio*  . Si el controlador pudo instalar, digamos, una lista, antes de revertir, intentará instalar la misma lista en el reintento. Para evitar que esto suceda, el código de un controlador de instalación tiene que estar escrito de modo que no realice ninguna acción (como, por ejemplo, implementar un componente) a menos que compruebe primero si ya se realizó dicha acción. Esto hace que la lógica de un controlador de instalación sea más compleja que la lógica de la primera vista, ya que esta última lógica no hará más reintentos (a menos que el código indique específicamente que lo haga). Además, comprobar si un componente ya se implementó normalmente requiere una llamada que tarda mucho en hacerse a través de Internet desde el controlador remoto hacia SharePoint. Luego se necesita una segunda llamada para implementar efectivamente el componente (si resulta que no se implementó ya).


Para el complemento Chain Store, combinaremos estas estrategias. En este artículo, crearemos un controlador de instalación que registrará la web de host como inquilino en la base de datos corporativa y, a continuación, configuraremos una señal que especifica si el complemento ya se ejecutó en la web de host. En un artículo posterior de esta serie, colocaremos la lógica de la primera vista en el método **Page_Load** de la página de inicio de los complementos. Esta lógica implementará las dos listas personalizadas y hará otras cosas también.




## Configurar la solución para la depuración de receptores de eventos
<a name="RERDebug"> </a>

La depuración de receptores de eventos requiere el uso del Bus de servicio de Azure. Siga las instrucciones recogidas en  [Depurar y solucionar problemas de un receptor de eventos remotos en un complemento para SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). Si usa un sitio web de SharePoint Online como sitio de prueba, asegúrese de seguir las instrucciones para un sitio de prueba remoto. En el resto de esta serie supondremos que configuró correctamente la depuración. 




## Crear el controlador de instalación
<a name="RERDebug"> </a>






> **NOTA**
>  La configuración de los Proyectos de inicio en Visual Studio tiende a volver a los valores predeterminados cuando se vuelve a abrir la solución. Siempre siga estos pasos inmediatamente después de volver a abrir la solución de ejemplo en esta serie de artículos:<BR />  Haga clic con el botón derecho en el nodo de solución en la parte superior del **Explorador de soluciones** y seleccione **Establecer proyectos de inicio**. <BR />  Asegúrese de que los tres proyectos estén establecidos en **Iniciar** en la columna **Acción**. 





1. En el **Explorador de soluciones**, seleccione el proyecto **ChainStore** para que sus propiedades aparezcan en el panel **Propiedades** de Visual Studio.


2. Establezca el valor de **Controlar complemento instalado** en **True**. (Puede que siga llamándose **Controlar aplicación instalada**). Esto hace dos cosas:

  - Se crea una carpeta llamada **Servicios** en el proyecto **ChainStoreWeb** (no en el proyecto **ChainStore**) y se le agregan dos archivos: un archivo AppEventReceiver.svc y su archivo de código subyacente AppEventReceiver.svc.cs. (Los nombres de archivo comienzan con la cadena "App" porque los complementos se solían llamar "apps".  *No cambie el nombre de estos archivos.*  El Office Developer Tools para Visual Studio supone que los archivos conservarán estos nombres).


  - La dirección URL del controlador está registrada en el manifiesto del complemento. Esta parte del manifiesto no está visible en el diseñador de manifiestos. Para verla, haga clic con el botón derecho en el archivo AppManifest.xml y seleccione **Ver código**. Hay un nuevo elemento secundario del elemento **Propiedades** que tiene el siguiente aspecto. Este marcado indica a SharePoint que llame al método **ProcessEvent** de este servicio cuando termine de realizar las tareas relacionadas con la instalación del complemento. El controlador personalizado es la última acción que se ejecuta como parte de la instalación. La cadena `~remoteAppUrl` es un marcador de posición que el Office Developer Tools para Visual Studio reemplazará por la dirección URL del host de servicio. Cuando se depura, es una dirección URL de Bus de servicio de Azure. Cuando crea el paquete para la implementación en producción, es la dirección URL de producción.

 ```XML

<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
 ```

3. Abra el archivo AppEventReceiver.svc.cs.


4. Verá que el Office Developer Tools para Visual Studio creó una implementación de ejemplo del método **ProcessEvent**. Todas las implementaciones de este método comienzan por inicializar un objeto **SPRemoteEventResult** y todas terminan por devolver ese objeto a SharePoint. Entre otras cosas, este objeto indica a SharePoint si debería o no revertir el evento porque se produjo un error en la lógica de control personalizado. Las herramientas también pueden incluir un bloque **using** en este método que crea un objeto **ClientContext**. El controlador personalizado en el complemento Chain Store no llamará a SharePoint, así que elimine este bloque. El método ahora debería ser similar al siguiente.

 ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
 ```

5. Cualquiera de los tres eventos de complementos posibles podría llamar al receptor de eventos. Por lo tanto, agregue la siguiente estructura **switch** al método **ProcessEvent** entre las líneas que crean y devuelven el objeto `result`. Los nombres de evento tienen la cadena "App" porque los complementos se solían llamar "apps".

 ```cs

switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.     
     
        break;
}
 ```

6. Nuestra lógica de instalación llamará a un procedimiento SQL almacenado para registrar la tienda de Hong Kong como inquilino en la aplicación web remota. Es muy importante que, si hay errores en este proceso, el controlador indique a SharePoint que revierta la instalación del complemento. Por lo tanto agregue los siguientes bloques **try/catch** en lugar de `TODO2`. Tenga en cuenta lo siguiente sobre este código:

  - Crearemos el objeto  `tenantName` y el método `CreateTenant` en un paso posterior.


  - La propiedad **Status** del objeto **SPRemoteEventResult** puede tener tres valores posibles: **Continue** (predeterminado), **CancelNoError** y **CancelWithError**. Cualquiera de los dos últimos indicará a SharePoint que revierta el evento.



 ```cs

try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
 ```

7. La dirección URL de la web de host, que es el discriminador de inquilinos del ejemplo, forma parte de la información que SharePoint pasa al receptor en el parámetro **SPRemoteEventProperties**. Agregue la siguiente línea al método **ProcessEvent** en línea que está justo debajo de la inicialización del objeto **SPRemoteEventResult**.

 ```cs

string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
 ```

8. Ahora nuestro código tiene que trabajar con una pequeña anomalía de la propiedad **AppEventProperties.HostWebFullUrl**. En casi todos los demás contextos, SharePoint incluye un carácter de cierre "/" al final de la dirección URL de la web de host, por lo que la lógica de nuestro código de ejemplo supone que existe este carácter. Pero SharePoint agrega este carácter al final del valor **HostWebFullUrl** únicamente si la web de host es la web raíz de una colección de sitios. Como nuestro sitio web de Hong Kong es una subweb de la colección de sitios, tenemos que agregar este carácter para garantizar que se use en todo el ejemplo la misma cadena de nombre del inquilino. Agregue el siguiente código debajo de la inicialización del objeto `tenantName`.

 ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
 ```

9. Agregue las siguientes instrucciones **using** al principio del archivo.

 ```

using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
 ```

10. Agregue el siguiente método a la clase  `AppEventReceiver`. No trataremos esto en detalle porque el objetivo de esta serie de artículos es enseñar programación de complementos de SharePoint, no programación de SQL Server/Azure. 

    Este método creará una fila en una tabla de base de datos denominada **Inquilinos**. Además de la columna **Nombre**, la tabla también tiene una columna **Versión** con un valor predeterminado establecido en0000.0000.0000.0000. En un artículo posterior de esta serie, crearemos la lógica de la primera vista que examina este valor para determinar si ya se instaló el complemento o no en la web de host. Si la versión es 0000.0000.0000.0000, el código implementará las listas **Empleados locales** y **Envíos esperados** y, a continuación, incrementará el número de versión.



 ```cs

private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
 ```


## Crear el controlador de desinstalación
<a name="RERDebug"> </a>

Generalmente se recomienda controlar el evento de desinstalación siempre que controla el evento instalado. La idea básica es que el controlador de desinstalación elimine o recicle cosas que el controlador instalado implementó. Existen, sin embargo, muchas excepciones, por lo que debe comprender bien los casos de uso del complemento. Por ejemplo, una lista que se implementa con un complemento y se rellena con el complemento podría tener valor incluso después de desinstalar el propio complemento. En este caso no convendría desinstalar la lista en el controlador de eventos de desinstalación. 



El evento de desinstalación no se ejecuta, como cabría esperar, cuando un usuario elimina el complemento desde la página **Contenido del sitio**. Cuando se elimina, solo se mueve el complemento a la Papelera de reciclaje del sitio web. Un usuario podría restaurarlo, pero la restauración no vuelve a ejecutar el controlador instalado, por lo que todo lo que se implementó con el controlador de eventos instalado todavía debe existir si se restaura el complemento. Los componentes de SharePoint se pueden mover de la Papelera de reciclaje a la Papelera de reciclaje de segunda etapa. El evento de desinstalación solo se produce cuando un complemento se elimina de la segunda etapa. Cuando un usuario hace eso, el complemento ya no se puede restaurar de ningún modo. Por lo tanto, queremos que el arrendamiento de la tienda de Hong Kong se quite de la base de datos corporativa en ese momento.




1. Establezca el valor de **Controlar desinstalación de complemento** en **True**. (Puede que siga llamándose **Controlar desinstalación de aplicación**). Esto registra el controlador en el archivo AppManifest.xml igual que registramos antes el controlador de instalación. Si observa el archivo, verá que tienen exactamente la misma dirección URL. El Office Developer Tools para Visual Studio da por supuesto que se usa el mismo archivo *.svc. Es lo que estamos haciendo en este ejemplo y es lo habitual. 


2. Agregue el siguiente código en lugar de  `TODO3` en el archivo AppEventReceiver.svc.cs. Tenga en cuenta lo siguiente sobre este código:

  - El método  `DeleteTenant` se agrega en el paso siguiente.


  - Revertir la desinstalación del complemento lo dejaría en la Papelera de reciclaje de segunda etapa, desde la que aún se podría restaurar.



 ```cs

try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
 ```

3. Agregue el siguiente método a la clase  `AppEventReceiver`.

 ```cs

private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();            
    }//dispose conn and cmd
}
 ```


> **NOTA**
> En un artículo anterior de esta serie, configuró el proyecto para recompilar la base de datos corporativa cada vez que se presiona F5. Con esto se vacía la tabla **Inquilinos**. 





## Ejecutar el complemento y probar el controlador de instalación
<a name="RERDebug"> </a>


1. Use la tecla F5 para implementar y ejecutar el complemento. Visual Studio hospeda la aplicación web remota en IIS Express y hospeda la base de datos SQL en un SQL Express. También hace una instalación temporal del complemento en su sitio de SharePoint de prueba, ejecuta el controlador de evento de instalación y ejecuta inmediatamente el complemento. Deberá conceder permisos al complemento antes de que se abra la página de inicio. 


2. Cuando se abra la página de inicio del complemento, presione el icono de engranaje en el control de cromo de la parte superior y seleccione **Configuración de la cuenta**.


3. En la página **Cuentas**, presione el botón **Mostrar versión del complemento**. La versión aparece como 0000.0000.0000.0000.

![Página de cuentas con el encabezado "Configuración de cuenta". Un botón denominado "Mostrar versión del complemento" y debajo una línea de texto que indica "Versión registrada: cero cero cero cero punto cero cero cero cero punto cero cero cero cero punto cero cero cero cero".](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)





4. Para terminar la sesión de depuración, cierre la ventana del explorador o detenga la depuración en Visual Studio. Cada vez que presione F5, Visual Studio retirará la versión anterior del complemento e instalará la más reciente.


5. Trabajará con este complemento y con la solución de Visual Studio en otros artículos, y se considera recomendable retirar el complemento una última vez cuando acabe de trabajar en él durante un tiempo. En el proyecto, haga clic con el botón derecho en el **Explorador de soluciones** y elija **Retirar**.



## 
<a name="Nextsteps"> </a>

 En el siguiente artículo de la serie, agregaremos la lógica de la primera vista al complemento que implementará mediante programación la lista **Empleados locales** y el botón de la cinta de opciones personalizado: [Agregar lógica de la primera vista al complemento hospedado por el proveedor](add-first-run-logic-to-the-provider-hosted-add-in.md)




