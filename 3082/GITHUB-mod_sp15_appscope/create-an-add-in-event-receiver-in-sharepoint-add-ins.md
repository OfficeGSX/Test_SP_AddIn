---
title: Crear un receptor de eventos de aplicación en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: f40c910f-12a2-4caa-8e91-c7a61ae540db
---


# Crear un receptor de eventos de aplicación en SharePoint 2013
Cree controladores para los eventos de instalación y desinstalación de Complemento de SharePoint en Complementos de SharePoint.
## Requisitos previos
<a name="SP15appevent_prereq"> </a>

En este artículo, se presupone que tiene conocimientos sobre Complementos de SharePoint hospedadas por el proveedor y que ha desarrollado algunas que van un poco más allá del nivel de "Hello World". Además, debe estar familiarizado con  [Controlar eventos en los complementos de SharePoint](handle-events-in-sharepoint-add-ins.md). 
  
    
    

## Obtener más ejemplos de código
<a name="SP15appevent_prereq"> </a>

Si trabaja con el ejemplo continuo de este artículo, tendrá un ejemplo de código terminado. Los siguientes son algunos otros ejemplos. No todos siguen la arquitectura descrita en este artículo. Puede haber más de una forma buena de diseñar un receptor de eventos de complemento; tenga en cuenta también que las instrucciones que ofrece Microsoft pueden evolucionar con el tiempo. 
  
    
    

-  [OfficeDev/PnP/Samples/Core.AppEvents.HandlerDelegation](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents.HandlerDelegation) está relacionado en gran medida con el ejemplo de este artículo.
    
  
-  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents) muestra cómo hacer la misma tarea que en el ejemplo anterior en escenarios en los que no se puede usar la estrategia de delegación de controlador.
    
  
-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)
    
  
-  [Crear un complemento hospedado por proveedor que personaliza la instalación de complemento](https://code.msdn.microsoft.com/SharePoint-2013-Create-a-f27752e0)
    
  

## Agregar un receptor de eventos de un complemento instalado
<a name="SP15appevent_prereq"> </a>


1. En Visual Studio, abra el proyecto para la Complemento de SharePoint hospedada por proveedor. (Si agrega un controlador de evento de complemento a un complemento hospedado por SharePoint, Office Developer Tools para Visual Studio la convertirá en una aplicación hospedada por proveedor).
    
  
2. En el **Explorador de soluciones**, elija el nodo de la Complemento de SharePoint.
    
  
3. En la ventana **Propiedades**, configure el valor de **Complemento de identificador instalado** en **True**. 
    
   **Figura 1. Eventos del complemento en la ventana Propiedades**

  

!\[Eventos de aplicación en la ventana de propiedades](images/SP_VS_Properties_Window_AppEvents.PNG)
  

    Office Developer Tools para Visual Studio realizará las siguientes acciones:
    
  - Agregar un archivo denominado AppEventReceiver.svc. que contiene el código C# (o VB.NET) de base. Es el servicio que controlará el evento de complemento.
    
  
  - Se agrega la siguiente entrada a la sección **Propiedades** del archivo AppManifest.xml: `<InstalledEventEndpoint>~remoteAppUrl/AppEventReceiver.svc</InstalledEventEndpoint>`. Esta entrada registra el receptor de evento de complemento a SharePoint. (Tenga en cuenta que el token **~remoteAppUrl** es el mismo que se usa para la aplicación web remota en la Complemento de SharePoint hospedada por proveedor. Office Developer Tools para Visual Studio asume que el domino de la aplicación web y el controlador evento es el mismo. En el caso poco habitual de que no lo sea, usted debe reemplazar manualmente el token **~remoteAppUrl** con el dominio real del servicio).
    
  
  - Si el proyecto Complemento de SharePoint no tiene ya un proyecto web, Office Developer Tools para Visual Studio crea uno. Las herramientas también garantizan que el manifiesto del complemento esté configurado para un complemento hospedado por un proveedor. También agregarán páginas, scripts, archivos CSS y otros artefactos. (Si el único componente remoto que el complemento necesita es el servicio web de control de evento, puede eliminarlos del proyecto. También debe asegurarse de que el elemento **StartPage** del manifiesto del complemento no esté apuntando a una página que ha eliminado).
    
  
4. Si la granja de prueba de SharePoint no está en el mismo equipo donde se ejecuta Visual Studio, configure el proyecto para depuración mediante el Bus de servicio de Microsoft Azure. Para obtener más información, consulte  [Depurar y solucionar problemas de un receptor de eventos remotos en un complemento para SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). 
    
  
5. Si existe un método  `ProcessOneWayEvent` en el archivo AppEventReceiver.svc, su implementación debería consistir solo en la línea `throw new NotImplementedException();`, porque no se puede usar este método en un controlador de eventos de complemento.  *Los controladores de eventos de complemento deben devolver un objeto que indica a SharePoint si debe finalizar o revertir el evento y el método  `ProcessOneWayEvent` no devuelve nada.* 
    
  
6. El archivo incluirá un método  `ProcessEvent` que tiene un aspecto similar al siguiente. (También puede existir un bloque de código que ilustra cómo obtener el contexto de un cliente. Elimínelo o coméntelo).
    
    Tenga en cuenta lo siguiente sobre este código:
    
  - El objeto  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) se envía al servicio web del controlador como mensaje SOAP que contiene información contextual de SharePoint, incluida una propiedad [EventType](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.EventType.aspx) que identifica al evento.
    
  
  - El objeto  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) que el controlador devuelve contiene una propiedad [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) cuyos valores posibles son [SPRemoteEventServiceStatus](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventServiceStatus.aspx) . **Continue**, **SPRemoteEventServiceStatus.CancelNoError** y **SPRemoteEventServiceStatus.CancelWithError**. El valor predeterminado de la propiedad **Status** es **Continue**, que indica a SharePoint que finalice el evento. Los otros dos valores indican a SharePoint que haga lo siguiente:
    
  - Ejecute el controlador hasta tres veces más.
    
  
  - Si aún recibe un estado de cancelación, cancele el evento y revierta todo lo que ha hecho como parte del evento. 
    
  



 ```cs
  
public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();

    return result;
}
 ```

7. Inmediatamente debajo de la línea que declara la variable  `result`, agregue la siguiente estructura de conmutador para identificar el evento que se está controlando. 
    
 ```cs
  
switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:
        break;
    case SPRemoteEventType.AppUpgraded:
        break;
    case SPRemoteEventType.AppUninstalling:
        break;
}
 ```


    > **NOTA**
      > Los eventos **AppInstalled**, **AppUpdated** y **AppInstalling**, si tiene controladores para ellos, obtendrán sus propias direcciones URL registradas en el manifiesto del complemento. Por lo tanto,  *es posible*  tener extremos diferentes para ellos; pero este artículo (y Office Developer Tools para Visual Studio) presupone que tienen exactamente el mismo extremo. Por este motivo, el código debe determinar qué evento lo llamó.
8. Tal como se explicó en  [Incluir la lógica de reversión y la lógica de "acciones realizadas" en los controladores de evento de complemento](handle-events-in-sharepoint-add-ins.md#Rollback), si algo sale mal en la lógica de instalación, casi siempre deberá cancelar la instalación del complemento, y necesitará que SharePoint revierta lo hecho para la instalación y que el controlador también reviertan lo hecho. Una forma de lograr estos objetivos es agregar el siguiente código dentro de **case** para el evento AppInstalled.
    
 ```cs
  
case SPRemoteEventType.AppInstalled:
  try
  {
      // Add-in installed event logic goes here.
  }
  catch (Exception e)
  {
      result.ErrorMessage = e.ErrorMessage;
      result.Status = SPRemoteEventServiceStatus.CancelWithError;

      // Rollback logic goes here.
  }
  break;
 ```


    > **NOTA**
      > Mueva el código de instalación que tarda más de 30 segundos en el complemento mismo. Puede agregarlo a la lógica de "primera ejecución" que se ejecuta la primera vez que se inicia el complemento. El complemento puede mostrar un mensaje que dice algo como "Estamos trabajando para usted". De forma opcional, el complemento puede indicar al usuario que ejecute el código de instalación. > Si la lógica de "primera ejecución" no es factible para el complemento, otra opción es hacer que el controlador de eventos inicie un proceso asíncrono remoto y luego que devuelva inmediatamente un objeto  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) con el **Status** establecido en **Continue**. Un punto débil de esta estrategia es que, si el proceso remoto falla, no tiene forma de indicar a SharePoint que revierta la instalación del complemento. 
9. Como se explicó en  [Estrategias de arquitectura del controlador de eventos de complemento](handle-events-in-sharepoint-add-ins.md#Strategies), la estrategia preferida es la delegación de controlador, aunque no es posible en todos los escenarios. En el ejemplo de este artículo, le mostramos cómo implementar la estrategia de delegación de controlador a la hora de agregar una lista a la web de hospedaje. (Para obtener información sobre cómo crear un controlador de evento AppInstalled similar que no use la estrategia de delegación de controlador, consulte el ejemplo  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents)).
    
    La siguiente es la nueva versión del bloque **case** de AppInstalled. Tenga en cuenta la lógica de inicialización que aplica todos los eventos que van más allá del bloque **switch**. Como la misma lista que está instalada se quitará del controlador AppUninstalling, aquí se identifica la lista.
    


 ```cs
  
SPRemoteEventResult result = new SPRemoteEventResult();
String listTitle = "MyList";

switch (properties.EventType)
{               
    case SPRemoteEventType.AppInstalled:
                    
   try
   {
        string error = TryCreateList(listTitle, properties);
        if (error != String.Empty)
        {
            throw new Exception(error);            
        }
   }
    catch (Exception e)
   {
        // Tell SharePoint to cancel the event.
        result.ErrorMessage = e.Message;
        result.Status = SPRemoteEventServiceStatus.CancelWithError;               
    }
        break;
    case SPRemoteEventType.AppUpgraded:
       break;
    case SPRemoteEventType.AppUninstalling:
       break;
}                      
 ```

10. Agregue el método de creación de listas a la clase **AppEventReceiver** como método **private** con el siguiente código. Tenga en cuenta que la clase `TokenHelper` tiene un método especial optimizado para obtener un contexto de cliente para un evento de complemento. Pasar **false** para el último parámetro garantiza que el contexto sea para la web de hospedaje.
    
 ```cs
  
private string TryCreateList(String listTitle, SPRemoteEventProperties properties)
 {    
    string errorMessage = String.Empty;          

    using (ClientContext clientContext =
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
        }
    }
    return errorMessage;
}

 ```

11. La lógica de reversión es básicamente una lógica de control de excepciones y el CSOM (modelo de objetos de cliente) de SharePoint tiene un  [ExceptionHandlingScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.aspx) que permite al servicio web delegar el control de excepciones al servidor de SharePoint. (Consulte también [Procedimiento para usar el ámbito de manipulación de excepciones](http://msdn.microsoft.com/library/103619ef-1ba3-44e3-93e1-5e0685bc616e%28Office.15%29.aspx)). Agregue el siguiente código al bloque **if** en el fragmento de código anterior.
    
 ```cs
  
ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext);

using (scope.StartScope()) 
{ 
    using (scope.StartTry()) 
    { 
    }         
    using (scope.StartCatch()) 
    {                                 
    } 
    using (scope.StartFinally()) 
    { 
    } 
} 
 clientContext.ExecuteQuery();

if (scope.HasException)
{
    errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
        scope.ServerErrorTypeName, scope.ErrorMessage, 
        scope.ServerErrorDetails, scope.ServerErrorValue, 
        scope.ServerStackTrace, scope.ServerErrorCode);
}
 ```

12. Existe solo una llamada a SharePoint ( **ExecuteQuery**) en el fragmento de código anterior, pero lamentablemente no nos alcanza. Todo objeto al que se haga referencia en nuestro ámbito de excepción primero debe cargarse en el cliente. Por lo tanto, agregue el siguiente código  *encima*  del constructor para el **ExceptionHandlingScope**.
    
 ```cs
  
ListCollection allLists = clientContext.Web.Lists;
IEnumerable<List> matchingLists =
    clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
clientContext.ExecuteQuery();

var foundList = matchingLists.FirstOrDefault();
 List createdList = null;
 ```

13. El código para crear una lista de web de hospedaje irá en el bloque  [StartTry](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartTry.aspx) , pero el código primero debe comprobar si ya se ha agregado la lista (tal como se explica en [Incluir la lógica de reversión y la lógica de "acciones realizadas" en los controladores de evento de complemento](handle-events-in-sharepoint-add-ins.md#Rollback)). La lógica If-then-else se puede delegar al servidor de SharePoint con la clase  [ConditionalScope](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ConditionalScope.aspx) . (Consulte también [Procedimiento para usar el ámbito condicional](http://msdn.microsoft.com/library/560112e9-c3ed-4b8f-9cd4-c8bc5d60d63c%28Office.15%29.aspx)). Agregue el siguiente código al bloque **StartTry**.
    
 ```cs
  
ConditionalScope condScope = new ConditionalScope(clientContext,
        () => foundList.ServerObjectIsNull.Value == true, true);
using (condScope.StartScope())
{
    ListCreationInformation listInfo = new ListCreationInformation();
    listInfo.Title = listTitle;
    listInfo.TemplateType = (int)ListTemplateType.GenericList;
    listInfo.Url = listTitle;
    createdList = clientContext.Web.Lists.Add(listInfo);                                
}
 ```

14. El bloque  [StartCatch](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartCatch.aspx) debe deshacer la creación de la lista, pero debe comprobar primero que se haya creado la lista, porque se puede haber producido una excepción en el bloque **StartTry** antes de que el bloque termine de crear la lista. Agregue el siguiente código al bloque **StartCatch**.
    
 ```cs
  
ConditionalScope condScope = new ConditionalScope(clientContext,
        () => createdList.ServerObjectIsNull.Value != true, true);
using (condScope.StartScope())
{
    createdList.DeleteObject();
} 
 ```


    > **SUGERENCIA**
      > **SOLUCIÓN DE PROBLEMAS:** Para probar si el bloque **StartCatch** se especificó en el momento correcto, usted necesita encontrar una manera de producir una excepción en tiempo de ejecución en el servidor de SharePoint. Un **throw** o dividir por cero no funcionará porque producen excepciones *en el cliente*  antes de que el tiempo de ejecución del cliente pueda siquiera agrupar el código y enviarlo al servidor (con el método **ExecuteQuery**). En cambio, agregue las siguientes líneas al bloque **StartTry**. El tiempo de ejecución en el cliente acepta esto, pero genera una excepción en el servidor, que es lo que usted necesita. >  `List fakeList = clientContext.Web.Lists.GetByTitle("NoSuchList");`
  
    
    
 `clientContext.Load(fakeList);`

    Todo el método TryCreateList debe ser similar a lo siguiente. (El bloque  [StartFinally](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ExceptionHandlingScope.StartFinally.aspx) es necesario incluso cuando no se está usando).
    


 ```cs
  
private string TryCreateList(String listTitle, SPRemoteEventProperties properties)
{    
    string errorMessage = String.Empty;  

    using (ClientContext clientContext = 
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
            ListCollection allLists = clientContext.Web.Lists;
            IEnumerable<List> matchingLists = 
                clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
            clientContext.ExecuteQuery();
            var foundList = matchingLists.FirstOrDefault();
            List createdList = null;

            ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext); 
            using (scope.StartScope()) 
            { 
                using (scope.StartTry()) 
                { 
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                            () => foundList.ServerObjectIsNull.Value == true, true);  
                    using (condScope.StartScope())
                    {
                        ListCreationInformation listInfo = new ListCreationInformation();
                        listInfo.Title = listTitle;
                        listInfo.TemplateType = (int)ListTemplateType.GenericList;
                        listInfo.Url = listTitle;
                        createdList = clientContext.Web.Lists.Add(listInfo);
                    }
                } 
                
                using (scope.StartCatch()) 
                { 
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                            () => createdList.ServerObjectIsNull.Value != true, true);
                    using (condScope.StartScope())
                    {
                        createdList.DeleteObject();
                    }    
                } 

                using (scope.StartFinally()) 
                { 
                } 
            } 
            clientContext.ExecuteQuery();

            if (scope.HasException)
            {
                    errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
                    scope.ServerErrorTypeName, scope.ErrorMessage, 
                    scope.ServerErrorDetails, scope.ServerErrorValue, 
                    scope.ServerStackTrace, scope.ServerErrorCode);
            }
        }
    }
    return errorMessage;
}
 ```


    > **SUGERENCIA**
      > **DEPURACIÓN:** Independientemente de que esté usando la estrategia de delegación de controlador, cuando pase por el código con el depurador, recuerde que, en cualquier escenario en que el controlador devuelva un estado de cancelación, SharePoint llamará al controlador nuevamente, hasta tres veces más. Por lo tanto, el depurador reciclará el código hasta cuatro veces.

    > **SUGERENCIA**
      > **ARQUITECTURA DE CÓDIGO:** Como puede instalar componentes en la web de complemento con marcado declarativo fuera del controlador, generalmente no le conviene usar ninguno de los 30 segundos que el controlador tiene disponible para interactuar con la web de complemento. Si lo hace, recuerde que el código requiere un objeto [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) por separado para la web de complemento. Esto significa que la web de complemento y la web de hospedaje son componentes diferentes, del mismo modo en que una base de datos de SQL Server es diferente a cada uno de ellos. Por lo que un método que llama a la web de complemento está en el bloque **try** del bloque AppInstalled **case**, tal como el método TryCreateList en el ejemplo que continúa. Sin embargo, el controlador  *no*  necesita revertir acciones realizadas en la web de complemento. Si encuentra un error, solo necesita cancelar el evento, porque SharePoint eliminará toda la web de complemento si se cancela el evento.

## Crear un receptor de eventos de desinstalación de complemento
<a name="SP15appevent_prereq"> </a>


1. Establezca la propiedad **Desinstalación del complemento de controlador** del proyecto en **True**. Las herramientas  *no*  crean otro archivo de servicio web, si ya existe uno, pero sí agregan un elemento **UninstallingEventEndpoint** al manifiesto del complemento.
    
  
2. El código en el bloque AppUninstalling **case** debe quitar artefactos del complemento que ya no son necesarios una vez quitado el complemento de la papelera de reciclaje de la segunda etapa, que es lo que activa el evento. Sin embargo, siempre que sea posible, debe "retirar" los componentes en lugar de eliminarlos completamente. Esto es porque debe restaurarlos si es necesario revertir el evento de desinstalación. Si eso ocurre, el complemento aún se encuentra en la papelera de reciclaje de la segunda etapa y un usuario podría restaurarlo y comenzar a usarlo nuevamente. Con tan solo volver a crear un componente eliminado en la lógica de reversión podría alcanzar para habilitar el complemento y que vuelva a funcionar. Sin embargo, se perderían los datos y toda configuración del componente.
    
    Esta estrategia es relativamente sencilla para los componentes de SharePoint, puesto que SharePoint tiene una papelera de reciclaje que permite restaurar elementos, y existen API de CSOM para obtener acceso. Los pasos posteriores de este procedimiento muestran cómo hacerlo. Para otras plataformas, es posible que se necesiten técnicas diferentes. Por ejemplo, si desea retirar una fila en una tabla de SQL Server en el controlador de desinstalación de complemento, un procedimiento almacenado en T-SQL del controlador puede agregar una columna IsDeleted a la tabla y establecerla en **True** para la fila. Si el procedimiento encuentra un error, la lógica de reversión restablece el valor a **False**. Si el procedimiento se completa sin errores, justo antes de devolver una marca de éxito, puede establecer un trabajo del temporizador para eliminar la fila más tarde.
    
    A veces es necesario conservar los datos, como las listas, incluso una vez eliminado el complemento; pero como ejemplo para este artículo, el siguiente es un controlador de eventos de desinstalación que elimina la lista que se creó con el controlador de eventos instalado.
    


 ```cs
  
case SPRemoteEventType.AppUninstalling:

try
{
    string error = TryRecycleList(listTitle, properties);
    if (error != String.Empty)
    {
        throw new Exception(error);
    }
}
catch (Exception e)
{
    // Tell SharePoint to cancel the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
break;
 ```

3. Agregue el método de aplicación auxiliar para reciclar la lista. Tenga en cuenta lo siguiente sobre este código:
    
  - El código recicla la lista, en lugar de eliminarla de forma permanente. Esto hace que sea posible restaurarla, con sus datos, si falla el evento, que es lo que hace el bloque **StartCatch**. Por lo tanto, si el método es correcto y se completa el evento, el complemento se elimina de forma permanente de la papelera de reciclaje de la segunda etapa, pero la lista aún se encuentra en la papelera de reciclaje de la primera etapa. 
    
  
  - El código prueba la existencia de la lista antes de reciclarla porque un usuario ya podría haberla reciclado en la interfaz del usuario de SharePoint. De modo similar, el código de reversión comprueba la existencia de la lista en la papelera de reciclaje antes de restaurarla, porque un usuario ya podría haberla restaurado o movido a la papelera de reciclaje de la segunda etapa. 
    
  
  - Existen dos ámbitos condicionales que prueban la existencia de una lista al comprobar si hay una referencia a ella en **null**. Ambos tienen un bloque **if** interno que prueban el mismo objeto para ver su nulidad por segunda vez. El externo prueba, con bloques de ámbito condicionales, la ejecución en el servidor, pero también se necesitan las pruebas de nulidad interna. Esto se debe a que el tiempo de ejecución de cliente se mueve por el código línea por línea para crear el mensaje XML que el método **ExecuteQuery** enviará al servidor. Cuando se alcanzan las referencias a los objetos **foundList** y **recycledList**, una u otra de estas líneas produce una excepción de referencia nula, a menos que estén dentro de las comprobaciones de nulidad interna.
    
  

 ```cs
  
private string TryRecycleList(String listTitle, SPRemoteEventProperties properties)
{
    string errorMessage = String.Empty;

    using (ClientContext clientContext = 
        TokenHelper.CreateAppEventClientContext(properties, useAppWeb: false))
    {
        if (clientContext != null)
        {
            ListCollection allLists = clientContext.Web.Lists;
            IEnumerable<List> matchingLists = 
                clientContext.LoadQuery(allLists.Where(list => list.Title == listTitle));
            RecycleBinItemCollection bin = clientContext.Web.RecycleBin;
            IEnumerable<RecycleBinItem> matchingRecycleBinItems = 
                clientContext.LoadQuery(bin.Where(item => item.Title == listTitle));        
            clientContext.ExecuteQuery();

            List foundList = matchingLists.FirstOrDefault();
            RecycleBinItem recycledList = matchingRecycleBinItems.FirstOrDefault();    

            ExceptionHandlingScope scope = new ExceptionHandlingScope(clientContext);
            using (scope.StartScope())
            {
                using (scope.StartTry())
                {
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                        () => foundList.ServerObjectIsNull.Value == false, true);
                    using (condScope.StartScope())
                    {
                        if (foundList != null)
                        {
                            foundList.Recycle();
                        }
                    }
                }
                using (scope.StartCatch())
                {
                    ConditionalScope condScope = new ConditionalScope(clientContext, 
                         () => recycledList.ServerObjectIsNull.Value == false, true);
                    using (condScope.StartScope())
                    {
                        if (recycledList != null)
                        {
                            recycledList.Restore(); 
                        }
                    }
                }
                using (scope.StartFinally())
                {
                }
            }
            clientContext.ExecuteQuery();

            if (scope.HasException)
            {
                errorMessage = String.Format("{0}: {1}; {2}; {3}; {4}; {5}", 
                    scope.ServerErrorTypeName, scope.ErrorMessage, 
                    scope.ServerErrorDetails, scope.ServerErrorValue, 
                    scope.ServerStackTrace, scope.ServerErrorCode);
            }
        }
    }
    return errorMessage;
}
 ```


### Para depurar y probar un receptor de eventos de desinstalación de un complemento


1. Abra todas las páginas siguientes por separado en ventanas o pestañas:
    
  - **Contenidos del sitio**
    
  
  - **Configuración del sitio - Papelera de reciclaje** (_layouts/15/AdminRecycleBin.aspx?ql=1)
    
  
  - **Papelera de reciclaje - Papelera de reciclaje de la segunda etapa** (_layouts/15/AdminRecycleBin.aspxView=2&amp;?ql=1)
    
  
2. Presione F5 y confíe en el complemento cuando se le solicite. Se abre la página de inicio del complemento. Si solo va a probar el controlador de desinstalación, puede cerrar esta ventana del explorador.  *Pero si va a depurar el controlador, déjela abierta. Al cerrarla, finalizará la sesión de depuración.* 
    
  
3. Actualice la página **Contenidos del sitio** y, cuando aparezca el complemento, quítelo.
    
  
4. Actualice la página **Configuración del sitio: Papelera de reciclaje**. El complemento aparecerá como elemento superior. Active la casilla que aparece al lado y haga clic en **Eliminar selección**.
    
  
5. Actualice la página **Papelera de reciclaje: Papelera de reciclaje de la segunda etapa**. El complemento aparecerá como elemento superior. Active la casilla que aparece al lado y haga clic en **Eliminar selección**. SharePoint llamará inmediatamente al controlador de desinstalación de complemento.
    
  

## Crear un receptor de eventos de actualización de complemento
<a name="SP15appevent_prereq"> </a>

Para conocer los detalles sobre cómo crear un controlador de actualización de complemento, consulte  [Crear un controlador para el evento de actualización en complementos de SharePoint](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).
  
    
    

## Restricciones de hospedaje y de URL en receptores de eventos de complemento de producción
<a name="SP15appevent_prereq"> </a>

El receptor de eventos puede hospedarse en la nube o en un servidor local que no se esté utilizando ya como servidor SharePoint. La URL de un receptor de producción no puede especificar un puerto determinado. Esto implica que o bien debe usarse el puerto 443 para HTTPS, opción que se recomienda, o el puerto 80 para HTTP. Si está utilizando HTTPS y el servicio de receptor está hospedado localmente, pero el complemento está en Microsoft SharePoint Online, entonces el servidor host debe disponer de un certificado de confianza público emitido por una autoridad de certificación. (Un certificado autofirmado funciona solo si el complemento se encuentra en una granja Sharepoint local.)
  
    
    

## Recursos adicionales
<a name="SP15appevent_addlresources"> </a>


-  [Controlar eventos en los complementos de SharePoint](handle-events-in-sharepoint-add-ins.md)
    
  

