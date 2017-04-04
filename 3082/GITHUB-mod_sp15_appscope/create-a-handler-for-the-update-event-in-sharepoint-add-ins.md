---
title: Crear un controlador para el evento de actualización en complementos de SharePoint
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# Crear un controlador para el evento de actualización en complementos de SharePoint
Cree y use un controlador para el evento de actualización de un Complemento de SharePoint.
## Requisitos previos para crear un controlador para el evento de actualización de complementos
<a name="Prerequisites"> </a>

Debe familiarizarse con  [Control de eventos de complemento](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) y [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md), y con los requisitos previos y principales que se describen en ellos.
  
    
    

## Crear un receptor de UpgradedEventEndpoint
<a name="UpgradedEventEndpoint"> </a>


> [!NOTA]
> **Sistema de numeración de versiones:** por cuestiones de coherencia, este tema supone que los números de versión del complemento son 1.0.0.0, 2.0.0.0, 3.0.0.0, etc. No obstante, la lógica y las instrucciones se aplican independientemente del sistema de numeración que se use.
  
    
    

Para una lógica de actualización personalizada, puede crear un receptor de eventos remotos de SharePoint que controle el evento de actualización del complemento. Sea prudente al usar esta técnica y úsela únicamente para pasos de actualización que no se puedan realizar de ningún otro modo. Además, las instrucciones de  [Control de eventos de complemento](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) se aplican tanto para el evento de actualización de complementos como para los eventos de instalación y desinstalación de complementos. Esto incluye:
  
    
    

- El controlador debe finalizar y devolver un estado de cancelación o continuación a SharePoint en 30 segundos. Si no lo hace, SharePoint volverá a llamar al controlador, hasta tres veces más.
    
  
- Si el controlador devuelve un estado de cancelación, SharePoint lo reintentará hasta tres veces más. 
    
  
- Normalmente, es necesario incluir la lógica de reversión y "de acciones realizadas" en el controlador.
    
  
Un escenario en el que resulta útil un controlador **UpgradedEventEndpoint** es cuando se agrega un campo calculado a una base de datos remota. Suponga que se agrega una columna Ciudad y su valor se calcula a partir de una columna existente Código Postal. El código de un controlador **UpgradedEventEndpoint** podría repetirse a través de elementos existentes de la base de datos y rellenar el valor del nuevo campo Ciudad en función del valor del campo Código postal y algún origen de datos externo que asigne códigos postales a ciudades. El cambio al propio esquema de la base de datos se realiza mejor fuera del controlador **UpgradedEventEndpoint** mediante los procedimientos recomendados de la plataforma de base de datos.
  
    
    
Para obtener más detalles sobre cómo crear un controlador para el evento de complementos, consulte  [Controlar eventos en los complementos de SharePoint](handle-events-in-sharepoint-add-ins.md) y [Crear un receptor de eventos de aplicación en SharePoint 2013](create-an-add-in-event-receiver-in-sharepoint-add-ins.md). El siguiente procedimiento da por supuesto que ya ha agregado el elemento receptor de eventos de complementos al proyecto de Complemento de SharePoint de Visual Studio. 
  
    
    

### Controlar el evento actualizado del complemento la primera vez


1. Abra el archivo AppEventReceiver.svc.cs y agregue una estructura condicional al método  [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) que prueba si el evento que invocó al controlador es el evento actualizado. El código de actualización va dentro de esta estructura. Si el código necesita acceder a SharePoint, puede usar el modelo de objetos de cliente de código administrado de SharePoint (CSOM) o la interfaz de transferencia de estado representacional (REST). El lugar en el que se ubique la estructura condicional en el método dependerá de cómo haya estructurado el resto de código en el método. Normalmente, es un par de estructuras condicionales similares que prueban los eventos del complemento instalado y los eventos de desinstalación de complementos. Puede estar dentro de una estructura condicional que prueba determinadas propiedades o subpropiedades del objeto [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) que se pasa a [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) en busca de valores **null** u otros valores no válidos. También puede estar dentro de un bloque **try**. A continuación se muestra un ejemplo de la estructura. El objeto  _properties_ es un objeto [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

  ```

2. Para usar CSOM en el controlador, agregue (dentro del bloque condicional) un bloque **using** que obtiene un objeto [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) mediante llamando al método **TokenHelper.CreateAppEventClientContext**. Especifique **true** para el segundo parámetro para poder acceder a la web del complemento. Especifique **false** para acceder a la web de host. Si necesita acceder a ambas, necesitará dos objetos diferentes de contexto de cliente.
    
  
3. Si el controlador necesita tener acceso a componentes que no son de SharePoint, ponga ese código fuera de cualquier bloque de contexto de cliente. El código debería tener una estructura similar a esta:
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, true))
    {
        // CSOM code that accesses the add-in web
    }
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, false))
    {
        // CSOM code that accesses the host web
    }
    // Other update code
}

  ```

4. Para usar la interfaz REST, el código usa otros métodos en la clase **TokenHelper** para obtener un token de acceso que, a continuación, se incluye en las solicitudes que realiza a SharePoint. Para más información, consulte [Procedimiento para realizar operaciones básicas con extremos REST de SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). El código debería tener una estructura similar a la siguiente.
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
    if (contextTokenString != null)
    {
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                                                                                                                  Request.Url.Authority);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                                   .AccessToken;

       // REST code that accesses SharePoint
    }  
    // Other update code
}

  ```

5. Para acceder a SharePoint, el código REST también necesita conocer la dirección URL de la web de host o de la web de complemento, o de ambas. Estas direcciones URL son subpropiedades del objeto  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) que se pasa al método [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) . El siguiente código muestra cómo obtenerlas.
    
  ```cs
  
Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
  ```

Al actualizar un complemento por segunda (o tercera, etc.) vez, es posible que deba asegurarse de que determinada lógica de actualización no se ejecute varias veces en la misma instancia del complemento. El siguiente procedimiento le muestra cómo.
  
    
    

### Controlar el evento de complemento actualizado en subsiguientes actualizaciones


1. Abra el archivo AppEventReceiver.svc.cs y busque el código que implementó las acciones de actualización en la primera actualización (de la versión 1.0.0.0. a la 2.0.0.0). Llamaremos a este código el código de actualización anterior. (Generalmente, este código está ubicado después del código de autorización que obtiene el contexto de cliente o los tokens de acceso). Ahora que vuelve a actualizar (de la versión 2.0.0.0 a la 3.0.0.0), normalmente habrá acciones del código de actualización anterior que no quiere volver a ejecutar en la misma instancia de complemento, pero que quiere que se ejecute en una instancia de complemento que nunca se actualizó a la versión 2.0.0.0 (en cuyo caso, la instancia se actualizará directamente de la versión 1.0.0.0. a la 3.0.0.0).
    
  
2. Envuelva el código de actualización anterior en una estructura condicional que pruebe la versión anterior de la instancia de complemento y que solo se ejecute si el código de la estructura no se ha ejecutado anteriormente en esta instancia de complemento. La versión anterior de la instancia se almacena en la subpropiedad  [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) del objeto [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  
3. Agregue la nueva lógica de actualización (para la actualización de la versión 2.0.0.0 a la 3.0.0.0) debajo de dicha estructura. A continuación se muestra un ejemplo.
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

  ```

4. Para cada subsiguiente actualización, repita estos pasos. Para la actualización de la versión 3.0.0.0 a la 4.0.0.0, el código debería tener la siguiente estructura.
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 is here.
}

Version ver3OOO = new Version("3.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver3OOO)
{
    // Code to update from 2.0.0.0 to 3.0.0.0 (previous update code) is here.
}
// Code to update from 3.0.0.0 to 4.0.0.0 is here.

  ```


> [!IMPORTANTE]
> Si agrega un componente a un complemento de un controlador **UpgradedEventEndpoint**, asegúrese de agregar el mismo código a un controlador **InstalledEventEndpoint** porque también querrá que se incluya ese componente en el complemento de una instalación totalmente nueva. Además, debe agregar [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) (o revisarlo) para que el complemento quite el componente. En la mayoría de casos, todo aquello que el controlador **InstalledEventEndpoint** agregue o cambie se debería revertir o eliminar con el controlador **UninstallingEventEndpoint**. Una excepción es que los datos que seguirán siendo de utilidad después de quitar el complemento de la papelera de reciclaje de la segunda etapa no se deberían eliminar. (Los sitios web, que no sean las web de complementos, que haya creado el complemento se deben considerar como datos). 
  
    
    


## Agregar lógica de reversión al controlador
<a name="AddRollbackLogic"> </a>

Si se produce un error al actualizar un complemento, la infraestructura de SharePoint 2013 detiene el proceso de actualización y revierte la instancia del complemento y todos sus componentes a la versión anterior de la instancia del complemento. Pero la infraestructura no tiene forma de saber lo que hace la lógica del controlador **UpgradedEventEndpoint**, por lo que no siempre puede deshacerlo. Si se produce una excepción no controlada mientras se ejecuta el controlador **UpgradedEventEndpoint**, indica que, con casi toda seguridad, se debería revertir todo el proceso de actualización del complemento, pero no se podrá hacer porque la infraestructura desconoce cómo deshacer algunas de las cosas que el código de **UpgradedEventEndpoint** podría haber hecho. Por este motivo, el código de **UpgradedEventEndpoint** debe:
  
    
    

1. Capturar todas las excepciones.
    
  
2. Crear una ramificación al código de reversión personalizado para deshacer todo lo que se haya hecho hasta ese punto.
    
  
3. Señalizar a la infraestructura de SharePoint 2013 a la que se debería revertir toda la actualización del complemento.
    
  
4. Pasar un mensaje de error a la infraestructura.
    
  
No todo lo que **UpgradedEventEndpoint** hace se debe revertir explícitamente dentro del controlador. La infraestructura revertirá la web de complemento, por lo que el código no tiene que deshacer los cambios de la web de complemento. Pero el controlador **UpgradedEventEndpoint** generalmente debe revertir los cambios que ha realizado en la web de host o en los otros componentes externos al Complemento de SharePoint.
  
    
    
En la segunda (o tercera, etc.) actualización, la lógica de control de excepciones debe tener en cuenta el hecho de que la instancia del complemento que se está actualizando no es necesariamente la versión inmediatamente anterior. Si no lo es, es posible que haya dos o más bloques de código de actualización que se deban revertir. Por este motivo, se necesita lógica condicional basada en el número de la versión anterior. A continuación se muestra un ejemplo de la lógica de reversión de una actualización a la versión 4.0.0.0.
  
    
    



```cs

catch (Exception e)
{ 
    // Rollback of the 3.0.0.0 to 4.0.0.0 update logic goes here.

    Version ver3OOO = new Version("3.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver3OOO)
    {
        // Rollback of the 2.0.0.0 to 3.0.0.0 update logic goes here.
    }

    Version ver2OOO = new Version("2.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver2OOO)
    {
        // Rollback of the 1.0.0.0 to 2.0.0.0 update logic goes here.
    }
}
```

Las últimas cosas que el control de errores debería hacer es asignar un mensaje de error y un estado de cancelación al objeto  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) que devuelve el método **ProcessEvent** a la infraestructura de actualización de SharePoint 2013. A continuación se muestra un ejemplo. En este código, _result_ es un objeto **SPRemoteEventResult** que se declaró con anterioridad en el método **ProcessEvent**.
  
    
    



```cs

catch (Exception e)
{     
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}
```


> [!IMPORTANTE]
> Es fundamental asignar **SPRemoteEventServiceStatus.CancelWithError** (o **SPRemoteEventServiceStatus.CancelNoError**) a la propiedad  [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) . Esta propiedad es la encargada de indicar a la infraestructura que revierta la actualización. Sin embargo, SharePoint volverá a probar el controlador tres veces antes de revertir la actualización.
  
    
    


### Usar la estrategia de delegación de controlador

La estrategia de delegación de controlador que se describe en  [Control de eventos de complemento](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) se puede usar también en un objeto **UpdatedEventHandler**. Tanto la lógica de reversión y "de acciones realizadas" como la lógica de control de versiones están agrupadas y se ejecutan en el componente remoto. Aquí también se aplican las limitaciones de la estrategia: normalmente no puede usarla para actualizar más de un sistema remoto.
  
    
    

## Pasos siguientes
<a name="Next"> </a>

Vuelva a  [Pasos principales en la actualización de un complemento](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) o visite directamente uno de los siguientes artículos para saber cómo actualizar el siguiente componente principal del Complemento de SharePoint.
  
    
    

-  [Actualizar componentes de web de complemento en SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Actualizar componentes de web de host en SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Actualizar componentes remotos en complementos de SharePoint](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Actualizar complementos para SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Elemento de UpgradedEventEndpoint (PropertiesDefinition complexType) (Add in SharePoint manifiesto)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)
    
  
-  [Controlar eventos en los complementos de SharePoint](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Crear un receptor de eventos de aplicación en SharePoint 2013](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  

