---
title: Agregar un flujo de trabajo a un complemento hospedado en SharePoint para SharePoint
ms.prod: SHAREPOINT
ms.assetid: b33a61d7-5bc9-4720-97a5-8475d9a0d7c5
---


# Agregar un flujo de trabajo a un complemento hospedado en SharePoint para SharePoint
Obtenga información sobre cómo incluir un flujo de trabajo en una Complemento de SharePoint.
Este es el sexto de una serie de artículos sobre los fundamentos del desarrollo de Complementos de SharePoint hospedadas en SharePoint. Primero debe familiarizarse con  [Complementos de SharePoint](sharepoint-add-ins.md) y con los anteriores artículos de esta serie:
  
    
    


-  [Empezar a crear complementos hospedados en SharePoint para SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Implementar e instalar un complemento hospedado en SharePoint para SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Agregar columnas personalizadas a un complemento de SharePoint hospedado en SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Agregar un tipo de contenido personalizado a un complemento de SharePoint hospedado en SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Agregar un elemento web a una página de un complemento para SharePoint hospedado en SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> **NOTA**
> Si ha estado trabajando en esta serie sobre complementos hospedados en SharePoint, tiene una solución Visual Studio que puede usar para continuar con este tema. También puede descargar el repositorio en  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) y abrir el archivo BeforeWorkflow.sln.
  
    
    

En este artículo va a agregar a la Complemento de SharePoint Orientación de empleados un flujo de trabajo que avisa al departamento de Recursos Humanos (RRHH) de que hay un nuevo empleado listo para rellenar el papeleo de RRHH.
## Agregar un flujo de trabajo a un complemento


  
    
    

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y elija **Agregar** > **Nueva carpeta**. Denomine a la carpeta Workflows.
    
  
2. Haga clic con el botón secundario en la nueva carpeta y elija **Agregar** > **Nuevo elemento**. El cuadro de diálogo **Agregar nuevo elemento** se abre en el nodo de **Office/SharePoint**.
    
  
3. Elija **Flujo de trabajo** y dele el nombreAdmisión_RRHH. Cuando se le indique que elija el tipo de flujo de trabajo, seleccione **Flujo de trabajo de lista** y luego **Siguiente**.
    
  
4. En la siguiente página del asistente, habilite la opción **Sí, asociarlo...** y luego establezca los siguientes valores en los controles desplegables:
    
  - **La biblioteca o lista con la que asociar su flujo de trabajo**
    
    Nuevos empleados de Seattle
    
  
  - **La lista de historial...**
    
    <crear nuevo>
    
  
  - **La lista de tareas...**
    
    <crear nueva>
    
  

    Haga clic en **Siguiente**.
    
  
5. En la última página del asistente, habilite  *solo*  la opción para comenzar automáticamente el flujo de trabajo cuando se *cambie*  un elemento.
    
  
6. Elija **Terminar**.
    
    Office Developer Tools para Visual Studio y luego haga lo siguiente:
    
  - Cree un flujo de trabajo Admisión_RRHH en la carpeta **Workflow**, con un archivo Workflow.xaml secundario que está abierto en el diseñador de flujos de trabajo.
    
  
  - Cree una instancia de lista **WorkflowTaskList**en la que se creen y actualicen las tareas que forman parte del flujo de trabajo.
    
  
  - Cree una instancia de lista **WorkflowHistoryList**, que es un registro de los distintos pasos que se van dando en cada ejecución del flujo de trabajo.
    
  
7. Arrastre las dos nuevas instancias de lista a la carpeta **Lists**.
    
  

## Diseño del flujo de trabajo

El flujo de trabajo envía un correo electrónico para notificar a alguien de RRHH que el nuevo empleado ha terminado la fase **Visita del edificio** de la orientación y está listo para ocuparse del papeleo de admisión. Cualquier cambio en un elemento existente de la listaNuevos empleados de Seattle activa el flujo de trabajo, pero este no hace nada si el campoFase de orientación del elemento de lista está establecido enPapeleo RRHH. Si es así, se envía un correo electrónico al personal de RRHH y se agrega a **WorkflowTaskList** una tarea para el empleado.
  
    
    

> **NOTA**
> En varios momentos durante el diseño del flujo de trabajo, aparecerá el símbolo de un diamante azul con un signo de admiración 
  
    
    
![Una forma de diamante azul pequeña con un signo de exclamación blanco dentro de ella.](images/f7b82a70-80fe-4e7e-a0f5-5a78cd12b367.PNG)
  
    
    
en uno o más elementos del diseñador de flujos e trabajo. Este símbolo avisa de errores puntuales (pase el cursor sobre el símbolo para ver un breve mensaje, o consulte más detalles en la **Lista de errores** de Visual Studio). Se trata de efectos secundarios y el motivo es que el flujo de trabajo no está completado. No debería quedar ninguno cuando acabe este procedimiento.
  
    
    


1. Abra el panel **Cuadro de herramientas** en Visual Studio, expanda el nodo ** SP - List** y luego arrastre **LookupSPListItem** hasta la **Secuencia** en el diseñador.
    
  
2. Seleccione **LookupSPListItem**, de modo que sus propiedades aparezcan en el panel **Propiedades** de Visual Studio. Establezca para las siguientes propiedades estos valores:
    
  - **ItemID:** (elemento actual)
    
  
  - **ListID:** (lista actual)
    
  
  - **DisplayName:** LookupCurrentNewEmployee
    
  

    El panel **Propiedades** debería tener ahora este aspecto:
    

   **Panel Propiedades de LookupSPListItem**

  

!\[Panel Propiedades de la actividad de flujo de trabajo del elemento de lista de búsqueda con las propiedades DisplayName, ListID e ItemID establecidas.](images/60f3302e-ca9c-45be-b785-0c9f636181da.PNG)
  

    Haga clic en cualquier punto fuera del panel para guardar los cambios. La superficie del diseñador debería tener ahora este aspecto.
    

   **Secuencia en el diseñador de flujos de trabajo**

  

!\[El diseñador de flujo de trabajo con un cuadro Secuencia y, dentro de él, una actividad llamada Buscar nuevo empleado actual.](images/c8fbf801-e8e4-444a-9d2e-c14e29f537de.PNG)
  

    
    
  
3. Haga clic en el vínculo **Obtener propiedades** dentro de la actividad (recién renombrada)LookupCurrentNewEmployee del diseñador. Esto agrega una actividad **GetDynamicValueProperties** a la secuencia.
    
  
4. Haga clic en el texto **Definir…** de la actividad **GetDynamicValueProperties**. Esto abre el cuadro de diálogo **Propiedades**.
    
  
5. Establezca **Tipo de entidad** en **Elemento de lista de** _nombre_instancia_lista_, donde  _nombre_instancia_lista_ esNuevos empleados de Seattle.
    
  
6. En la columna **Ruta**, haga clic en la celda superior y elija Fase orientación en el menú desplegable.
    
  
7. Haga clic en la celda debajo de ella y elija **Título (Titulo)** en el menú desplegable.
    
  
8. Haga clic en **Rellenar variables**. Esto creará unas variables llamadas OrientationStage y **Title**, y asignará a cada una el valor de los campos correspondientes en el elemento actual de la lista Nuevos empleados de Seattle. El cuadro de diálogo **Propiedades** debería tener ahora este aspecto:
    
   **Cuadro de diálogo Propiedades de la actividad de flujo de trabajo**

  

!\[El cuadro de diálogo Propiedades de la actividad "Obtener valores dinámicos", con el tipo de entidad establecido en elementos de la lista Nuevos empleados y las variables denominadas Title y OrientationStage asignadas a los campos de los mismos nombres.](images/36a841e7-ce1b-444c-9bfe-7cdc56399ec1.PNG)
  

  

  
9. Elija **Aceptar**. Ahora, la superficie del diseñador debería parecerse a lo siguiente:
    
   **Diseñador de flujos de trabajo**

  

!\[El diseñador de flujo de trabajo con dos actividades: Búsqueda de elementos de lista y Obtener valores dinámicos.](images/cd8eb456-d883-491a-b171-38c1b9f64018.PNG)
  

    
    
  
10. Abra el panel **Cuadro de herramientas** en Visual Studio, expanda el nodo **Flujo de control** y arrastre **Si** hasta la parte inferior de la **Secuencia**, debajo de **GetDynamicValueProperties**.
    
  
11. En el cuadro **Condición** de **Si**, escriba OrientationStage=="Papeleo RRHH".
    
  
12. Abra el panel **Cuadro de herramientas** en Visual Studio, expanda el nodo ** SP - Utilities** y arrastre **Correo electrónico** hasta el cuadro **Entonces** de la actividad **Si**.
    
  
13. Seleccione la actividad **Correo electrónico**. En el panel **Propiedades**, establezca los valores de las propiedades Cuerpo, Asunto y Para. En cada caso, elija el botón de llamada, **. . .**, para la propiedad, y use el **Editor de expresiones** que se abre para establecer el valor de la propiedad como en la tabla siguiente. Se trata de expresiones y cadenas de C#, así que use las comillas tal y como se muestra. `Title` es aquí una variable que asignó anteriormente en el campo **Título** del elemento de lista (y que aloja el nombre del empleado).
    
  - **Cuerpo:** `Title + " is waiting in the lobby to fill out benefits and employment forms."`
    
  
  - **Asunto:** `Title + " is ready for HR paperwork"`
    
  
  - **Para:** `new System.Collections.ObjectModel.Collection<string>() {"your_O365_email"}`
    
    Sustituya el marcador de posición  *su_dirección_O365*  por la identidad que usa para iniciar sesión en su cuenta de desarrollador de Office 365, como *alias*  @ *O365domain*  .sharepoint.com. Esta es una cadena de C#, de modo que debe ir entre comillas.
    
  
14. Abra el panel **Cuadro de herramientas** en Visual Studio, expanda el nodo **Runtime** y arrastre **TerminateWorkflow** hasta el cuadro **Si no** de la actividad **Si**.
    
  
15. Elija la actividad **TerminateWorkflow** y, en el panel **Propiedades**, establezca la **Razón** para lo siguiente, * incluyendo las comillas*  :"No en la fase Papeleo RRHH".. El diseñador debería tener ahora este aspecto:
    
   **El Diseñador de flujos de trabajo cuando el flujo de trabajo está completado**

  

!\[El diseñador de flujo de trabajo con actividades para "Búsqueda de elemento de lista", "Obtener valor dinámico" y una estructura "If Then Else". Correo electrónico es la actividad en la parte Then y Finalizar flujo de trabajo es la actividad en la parte Else.](images/c1230e3b-d149-413c-aa66-d258250a4512.PNG)
  

  

  

## Ejecutar y probar el complemento


  
    
    

1. Use la tecla F5 para implementar y ejecutar su complemento. Visual Studio realiza una instalación temporal en su sitio de pruebas de SharePoint y ejecuta inmediatamente el complemento. También se abre la consola **Host de servicio de prueba** del Administrador de flujos de trabajo.
    
  
2. Cuando se abra la página predeterminada del complemento, abra uno de los elementos para su edición y establezca el valor de Fase de orientación enPapeleo RRHH. 
    
    En la consola **Host de servicio de prueba** aparece una indicación de que el flujo de trabajo ha comenzado. Poco después recibirá el aviso de que ha terminado. Lo siguiente es un ejemplo:
    

   **Consola Host de servicio de prueba**

  

!\[La ventana del Host del servicio de pruebas de flujo de trabajo con una línea que indica que se ha iniciado el flujo de trabajo, seguida de una línea que indica que ha finalizado. El GUID de la instancia de flujo de trabajo está al principio de cada línea.](images/2422936d-7ef6-4c90-a03f-30053fbb9743.PNG)
  

    
    
    
    > **NOTA**
      > Si la consola **Host de servicio de prueba** no se abre, es posible que deba habilitar el depurador de flujos de trabajo. Haga clic con el botón secundario en el nombre del proyecto en el **Explorador de soluciones** y elija **Propiedades**. Abra la pestaña **SharePoint** en el panel **Propiedades** y marque la casilla **Habilitar depuración de flujos de trabajo**. 
3. Vaya a la carpeta de entrada (Outlook) de su cuenta de desarrollador de Office 365. Habrá un mensaje con el asunto " *Empleado*  está listo para el papeleo de RRHH",, en el que *Empleado*  es el nombre del empleado cuyo elemento ha editado. El cuerpo del mensaje dice: " *Empleado*  está esperando en el vestíbulo a completar los formularios de condiciones y el contrato de trabajo." Lo siguiente es un ejemplo:
    
   **Correo electrónico enviado por flujo de trabajo**

  

!\[Un mensaje de correo electrónico en Outlook desde el flujo de trabajo con el asunto "Cassie Hicks está lista para la documentación de RR. HH." y el cuerpo "Cassie Hicks está esperando en la sala de espera para rellenar los formularios de empleo y prestaciones".](images/7b1d8f47-9c34-441e-af6a-3af4a8c65533.PNG)
  

    
    
    
    > **SUGERENCIA**
      > Si el flujo de trabajo comienza pero no llega a completarse y no se envía el correo electrónico, pruebe a terminar la sesión de depuración y use F5 unas cuantas veces más antes de concluir que existe algún error en el código. A veces, el problema está en SharePoint Online. > Si todavía tiene problemas, pruebe a agregar un tipo de contenido llamado **ListFieldsContentType**, si es que no lo hay ya, a la sección **ContentTypes** del archivo schema.xml. Lo siguiente es un ejemplo de código de marcado.>  `<ContentType ID="0x0100781dd48170b94fdc9706313c82b3d04c" Name="ListFieldsContentType" Hidden="TRUE">`
  
    
    
 `</ContentType>`> Después, copie toda la sección **FieldRefs** del tipo de contenido **NewEmployee** a este nuevo tipo de contenido.> Guarde el proyecto, retírelo y pruebe F5 otra vez. 
4. Para terminar la sesión de depuración, cierre la ventana del explorador o detenga la depuración en Visual Studio. Cada vez que presione F5, Visual Studio retirará la versión anterior del complemento e instalará la más reciente.
    
  
5. Trabajará con este complemento con la solución Visual Studio en otros artículos, y se considera recomendable retirar el complemento una última vez cuando vaya a dejar de trabajar en él durante un tiempo. Haga clic con el botón secundario en el **Explorador de soluciones** y elija **Retirar**.
    
  

## 
<a name="Nextsteps"> </a>

En el siguiente artículo de la serie, agregará una página y un estilo personalizados a la Complemento de SharePoint:  [Agregar una página y un estilo personalizados a un complemento hospedado en SharePoint para SharePoint](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md).
  
    
    

