---
title: Administrador de incidentes Tutorial de complemento empresarial de nube
ms.prod: SHAREPOINT
ms.assetid: 8dec09e6-e197-4f68-bcc2-ca41105c5d61
---


# Administrador de incidentes: Tutorial de complemento empresarial de nube
Al usar la plantilla **Complemento empresarial de nube** en Visual Studio, puede crear un complemento hospedado en SharePoint en el que los usuarios de móviles puedan ver, agregar y actualizar datos de ubicaciones remotas mediante dispositivos modernos y táctiles, como teléfonos y tabletas. En este tutorial, creará un complemento para que un Departamento de bomberos ficticio de Contoso se encargue de la administración de incidentes in situ. El tutorial cubre los conceptos fundamentales para crear un complemento empresarial de nube y también conceptos más avanzados como la integración de bibliotecas de documentos y listas de SharePoint.Puede descargar la aplicación de ejemplo de Administración de incidentes y los archivos asociados de la Galería de ejemplos de MSDN:  [Administrador de incidentes: Tutorial de complemento empresarial de nube](http://code.msdn.microsoft.com/Incident-Manager-A-Cloud-c32d9b04).
## Requisitos previos

Este tutorial requiere Visual Studio 2013 Update 1 y Microsoft Developer Tools para Visual Studio 2013 - Actualización de marzo de 2014



Para hospedar el complemento, necesitará un sitio para desarrolladores de SharePoint en Office 365, que puede obtenerlo desde  [Regístrese en un sitio para desarrolladores de Office 365](http://go.microsoft.com/fwlink/?LinkId=263490).




> **NOTA**
> También puede hospedar un complemento empresarial de nube en un sitio de SharePoint de 2013. 





## Creación de la aplicación de administración de incidentes
<a name="add-in"> </a>

El departamento de bomberos de Contoso responde a los incendios, ayuda médica de emergencia, accidentes de vehículo de motor y otras situaciones de emergencia. Cada incidente de emergencia requiere muchos trámites administrativos para documentar el incidente. Actualmente escriben la información en el lugar mediante formularios en papel y, a continuación, escriben la información en su sistema informático cuando vuelvan al cuartel de bomberos. Poco tiempo atrás, adoptaron SharePoint para Office 365 para administración de registros, y han adquirido tabletas para permitir al comandante de incidentes escriben los datos en el lugar.



Va a crear el complemento móvil que se utilizará para escribir la información básica acerca del incidente. Dado que muchos incidentes implican también ayuda médica, el complemento también deberá ser capaz de escribir los datos de varios pacientes por incidente. En esta sección generará la aplicación básica, y conocerá los aspectos básicos del complemento empresarial de nube a lo largo del proceso.




### Para crear un proyecto


1. En la barra de menú, elija **Archivo**, **Nuevo** y **Proyecto**.

    Se abre el cuadro de diálogo **Nuevo proyecto**.


2. En la lista de plantillas, expanda el nodo **Visual Basic** o **Visual C#**, expanda el nodo **Office/SharePoint**, elija el nodo **Complementos**, y luego elija la plantilla **Complemento empresarial de nube**, como se muestra en la Figura 1.

   **Figura 1. Plantilla Complemento empresarial de nube**



!\[Plantilla de aplicación empresarial de nube](images/CBA_IM1.PNG)





3. En el cuadro **Nombre**, escriba IncidentManager y luego elija el botón **Aceptar**.

    Se abre el asistente para **Nuevo complemento empresarial de nube**.


4. En el asistente **Nuevo complemento empresarial de nube**, escriba la dirección URL de su sitio para desarrolladores de Office 365 y, a continuación, elija el botón **Finalizar**.

    La dirección URL debe tener la forma https:// _MiSitio_.sharepoint.com/sites/Developer/.

    Se agrega una solución de **IncidentManager** al Explorador de soluciones con cuatro proyectos: un proyecto de **IncidentManager** de nivel superior, un proyecto de **IncidentManager.HTMLClient**, un proyecto de **IncidentManager.Server** y un proyecto de **IncidentManager.SharePoint**.







    En el siguiente paso, va a agregar una entidad para representar un grupo de incidentes. La entidad crea una tabla de base de datos de SQL Server que es inherente al complemento. Almacenará información sobre cada incidente.



### Para agregar la entidad de Incidente


1. En la pantalla **Inicio**, elija el hipervínculo **Crear nueva tabla**.

    Se abre el diseñador de entidades.


2. En la ventana **Propiedades** del cuadro de texto para la propiedad **Nombre**, escriba Incidente.


3. En el diseñador de entidades, elija el vínculo **<Agregar propiedad>** y escribaIncidentNumber.


4. Pulse la tecla Entrar, aceptando el **Tipo** predeterminado, **String** y dejando activada la casilla **Requerido**.


5. En la ventana **Propiedades**, active la casilla **Incluir en índice único**.

    Cada incidente debe tener su propio número de incidente único.


6. Elija el vínculo **<Agregar propiedad>**, escriba IncidentDate y pulse la tecla **Tabularador**.


7. En la columna **Tipo**, elija **Fecha** y, a continuación, pulse la teclaTabulador dos veces.


8. En la columna **Nombre**, escriba DispatchTime y, en la columna **Tipo**, elija el tipo de datos **DateTime**.


9. En la siguiente fila, escriba IncidentType y elija **Integer** como tipo de datos.


10. En la siguiente fila, escriba IncidentAddress y elija **String** como tipo de datos.


11. En la siguiente fila, escriba ReportingPartyName y elija **Cadena** como tipo de datos. A continuación, desactive la casilla **Requerido**.

    A veces los incidentes se informan de forma anónima, por lo que no requieren un valor en este campo.


12. En la siguiente fila, escriba ReportingPhone y elija el tipo de datos **PhoneNumber**.

    **Número de teléfono** es untipo empresarial personalizado que aplica formato y proporciona validación de números de teléfono de forma automática.


13. En la ventana **Propiedades**, elija el hipervínculo **Formatos de número de teléfono**.

    Se abre el cuadro de diálogo **Formatos de número de teléfono**.


14. En el cuadro de diálogo **Formatos de número de teléfono**, elija el formato adecuado para su configuración regional y luego elija el botón **Mover arriba** para moverlo a la parte superior de la lista.


15. En el cuadro de texto **Validación de número de teléfono de prueba**, escriba un número de teléfono y compruebe que tenga el formato adecuado y, a continuación, elija el botón **Guardar**.

    El tipo de empresa de **Número de teléfono** controla el formato y la validación por usted en cualquier pantalla en la que aparece.


16. En la siguiente fila, escriba IncidentCommander y elija el tipo de datos **Person**.

    El tipo de empresa **Persona** proporciona una conexión al servicio de perfil de usuario de SharePoint, que extrae información del usuario de Active Directory.

    La Figura 2 muestra la entidad terminada.


   **Figura 2. La entidad Incidentes**



!\[Entidad Incidente](images/CBA_IM_2.PNG)








    En el siguiente paso, agregará una lista de opciones que permite al usuario elegir entre un conjunto fijo de valores. En este caso, el departamento de bomberos tiene un conjunto de tipos de incidentes comunes.



### Para agregar una lista de opciones


1. En el diseñador de entidades, elija el campo **IncidentType** y, a continuación, en la ventana **Propiedades** elija el hipervínculo **Lista de opciones**.

    Se abre el cuadro de diálogo **Lista de opciones**.


2. En el cuadro de diálogo **Lista de opciones**, elija el vínculo **Agregar valor** y escriba1.


3. En la columna **Nombre para mostrar**, escriba **Bomberos - comercial**.


4. Escriba los siguientes valores y nombres para mostrar y, a continuación, elija el botón **Aceptar**.


|**Valor**|**Nombre para mostrar**|
|:-----|:-----|
|2  <br/> |Bomberos - residencial  <br/> |
|3  <br/> |Bomberos - Respuesta de un solo motor  <br/> |
|4  <br/> | Ayuda - soporte de vida avanzado <br/> |
|5  <br/> | Ayuda - soporte vital básico <br/> |
|6  <br/> |Colisión de vehículos de motor  <br/> |
|7  <br/> |Llamada de servicio  <br/> |
|||
 

    En tiempo de ejecución, se presentará una lista de los nombres para mostrar al usuario. Cuando elija una opción, se almacenará en la base de datos el valor **Integer** correspondiente.







    En el siguiente paso, va a agregar una entidad de Paciente. Muchos de los incidentes del departamento de bomberos implican ayuda médica y un solo incidente a menudo implica a varios pacientes.



### Para agregar la entidad de Paciente


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **Orígenes de datos** y elija **Agregar tabla**.


2. En la ventana **Propiedades** del cuadro de texto para la propiedad **Nombre**, escriba Paciente.


3. En el diseñador de entidades, agregue los siguientes campos tal como se muestra en la Figura 3:

   **Figura 3. La entidad de Paciente terminada**



!\[Entidad Paciente](images/CBA_IM_2a.PNG)








    En el siguiente paso, definirá una relación entre las entidades de Incidente y Paciente. Un incidente puede tener varios parientes relacionados.



### Para definir una relación


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **Incidents.lsml** y elija **Abrir**.


2. En la barra de herramientas, elija el botón **Relación**.

    Aparece el cuadro de diálogo **Agregar nueva relación**.


3. En el cuadro de diálogo **Agregar nueva relación** de la columna **Para** correspondiente a la fila **Nombre**, elija **Paciente**.


4. En la fila **Multiplicidad** de la columna **De**, elija **Cero o uno**.


5. En la columna **Para**, elija **Muchos**, y luego elija el botón **Aceptar**.

    La Figura 4 se muestra la relación.


   **Figura 4. Relación de Incidentes y Pacientes**



!\[Relación entre Incidente y Paciente](images/CBA_IM_3.PNG)


    Esto crea un relación de cero o uno a muchos entre incidentes y pacientes. No es necesario que un registro de incidentes sea un paciente, pero cada paciente debe tener un registro de incidentes relacionado.







    En el paso siguiente, agregará pantallas para ver, agregar y editar registros de Pacientes e Incidentes.



### Para agregar pantallas


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **Incidents.lsml** y elija **Abrir**.


2. En el diseñador de pantallas de la barra **Perspectiva**, elija la pestaña **Cliente HTML** y, a continuación, elija en la barra de herramientas el botón **Pantalla**.

    Se abre el cuadro de diálogo **Agregar nueva pantalla**.


3. En el cuadro de diálogo **Agregar nueva pantalla** de la lista **Seleccione una plantilla de pantalla**, elija **Conjunto común de pantallas**.


4. En el cuadro de texto **Nombre de conjunto de pantallas**, escriba Incidentes.


5. En la lista **Datos de pantalla**, elija **Incidentes**.


6. Active las casillas **Detalles del incidente** y los **Pacientes del incidente** y, a continuación, elija el botón **Aceptar**.

    La Figura 5 muestra el cuadro de diálogo Agregar nueva pantalla.


   **Figura 5. Cuadro de diálogo Agregar nueva pantalla**



!\[Pantalla Incidentes](images/CBA_IM_4.PNG)


    Las pantallas Examinar, Ver detalles y Agregar/Editar se agregan al proyecto de HTMLClient. La pantalla Examinar se establece automáticamente como la pantalla de inicio de la aplicación, y se proporcionan automáticamente los comandos necesarios para abrir las pantallas Vista y Agregar/Editar.


7. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar la aplicación. Si aparece el cuadro de diálogo **Conectar con SharePoint**, escriba su nombre de usuario y contraseña.


8. Si se le solicita, elija el botón **Confiar**. El complemento se abrirá con una pantalla vacía. Observe que el título de la pantalla es **IncidentsSet**.


9. Haga clic en el botón **Agregar**.

    Se abre el cuadro de diálogo **Incidentes**. Observe que mientras escribe los datos del incidente, no existe provisión para agregar pacientes. Solucionará ese y otros problemas de diseño al personalizar las pantallas.







    En el siguiente paso, personalizará la pantalla Examinar.


10. Cierre el cuadro de diálogo **incidente** y, a continuación, cierre la ventana del explorador para volver al modo de diseño.



### Para personalizar la pantalla de exploración


1. En la pantalla **Explorador de soluciones**, abra el menú contextual del nodo **BrowseIncidentsSet.lsml** y elija **Abrir**.


2. En la ventana **Propiedades**, elija la propiedad **Nombre para mostrar** y escribaIncidentes.


3. En el diseñador de pantallas, elija el nodo **Diseño de filas | filas** y, a continuación, expanda la lista **Agregar** y elija **Dirección de incidentes**.


4. Elija el nodo **Hora de distribución** y, a continuación, en la barra de herramientas, elija el botón **Eliminar**.

    La Figura 6 muestra el diseño de pantalla terminado.


   **Figura 6. Diseño de pantalla de exploración**



!\[Diseño de la pantalla Examinar](images/CBA_IM4a.PNG)


    En tiempo de ejecución, la pantalla mostrará un mosaico para cada incidente, y cada mosaico mostrará el número de incidente, la fecha y la dirección.







    En el siguiente paso, personalizará la pantalla Agregar/Editar.



### Para personalizar la pantalla Agregar/Editar


1. En la pantalla **Explorador de soluciones**, abra el menú contextual del nodo **AddEditIncidents.lsml** y elija **Abrir**.


2. Elija los nodos **Dirección de incidentes**, **Nombre del informante**, y **Teléfono del informante** y arrástrelos a la sección **Diseño de filas | izquierda** debajo del **Tipo de incidente**.


3. Elija el nodo **Tiempo de envío** y, en la ventana **Propiedades**, desactive la casilla **Selector de fecha habilitado**.

    La fecha no es necesaria ya que siempre será igual a la fecha del incidente.


4. En el panel izquierdo del diseñador de pantallas, elija el vínculo **Agregar pacientes**, como se muestra en la figura 7.

   **Figura 7. Vínculo Agregar pacientes**



!\[Vínculo Agrega pacientes](images/CBA_IM_5.PNG)





5. En el panel izquierdo, elija el nodo **Pacientes** y arrástrelo debajo del nodo **Comandante de incidentes** en el panel central.

    La Figura 8 muestra el diseño de pantalla terminado.


   **Figura 8. El diseño de Agregar/Editar pantalla**



!\[Diseño de la pantalla Agregar/Editar](images/CBA_IM_5a.PNG)








    En el siguiente paso, creará una pantalla para agregar registros de pacientes.



### Para agregar una pantalla Pacientes


1. En la pantalla **Explorador de soluciones**, abra el menú contextual del nodo **AddEditIncidents.lsml** y elija **Abrir**.


2. En el diseñador de pantallas, en el nodo **Diseño de filas | Detalles (pestaña)**, abra el menú contextual del nodo **Barra de comandos** y elija **Botón Agregar**.


3. En el cuadro de diálogo **Botón Agregar**, expanda la lista **showTab** y, en el grupo **Pacientes**, elija **addAndEditNew**y, a continuación, elija el botón **Aceptar**.

    Se abre el cuadro de diálogo **Agregar nueva pantalla**.


4. En el cuadro de diálogo **Agregar nueva pantalla**, acepte los valores predeterminados y elija el botón **Aceptar** botón.

    Se abre la pantalla **AddEditPatient** en el diseñador de pantallas.


5. En la ventana Propiedades, elija la propiedad **Nombre para mostrar** y escribaAgregar paciente.


6. En el diseñador de pantallas, elija el nodo **Incidente** y elimínelo.

    El campo **Incidentes** no es necesario porque el paciente ya está asociado a un incidente.

    La Figura 9 muestra el diseño de pantalla terminado.


   **Figura 9. El diseño de pantalla Agregar/Editar**



!\[Diseño de la pantalla Agregar/Editar](images/CBA_IM_6.PNG)








    En el siguiente paso, ejecutará el complemento y agregará datos.



### Para probar el complemento


1. En la barra de menús, elija **Depurar**, **Iniciar depuración**.


2. En el complemento en ejecución, elija el botón **Agregar**.

    Se abre el elemento emergente **Incidentes**, como se muestra en la Figura 10.


   **Figura 10. Elemento emergente Incidentes**



!\[Elemento emergente Agregar incidente](images/CBA_IM_7.PNG)





3. En el cuadro de texto **Número de incidente**, escriba 2014-1.


4. En los campos **Fecha de incidente** y **Hora de distribución**, utilice los controles **Selector de fecha** y **Selector de hora** para seleccionar una fecha y hora.


5. En la lista **Tipo de incidente**, elija **Ayuda - soporte vital básico**.


6. En el cuadro de texto **Dirección de incidente**, escriba una dirección de su ciudad.

     No incluya ciudad, estado ni código postal. Se supone que el departamento de bomberos únicamente funciona en una sola ciudad.


7. En el control **Selector de usuarios** del **Comandante de incidentes**, escriba el nombre de un usuario en el sitio de SharePoint.

    Observe que, a medida que escribe, aparece una lista de todos los nombres coincidentes.


8. Elija el botón **Agregar paciente**.

    Se abre el elemento emergente **Agregar paciente**, como se muestra en la Figura 11.


   **Figura 11. Elemento emergente Agregar paciente**



!\[Elemento emergente Agregar paciente](images/CBA_IM_8.PNG)





9. Escriba la información de un paciente y, a continuación, elija el botón **Guardar**.

    El nombre del paciente que agregó aparece en el elemento emergente **Incidentes**. Si lo desea, puede agregar más pacientes.


10. En el elemento emergente **Incidentes**, haga clic en el botón **Guardar**.

    Si siguió las instrucciones, verá un mensaje de error de validación. El campo **Teléfono de informe** es un campo obligatorio. Los complementos empresariales de nube tienen validación integrada para los campos obligatorios.


11. Escriba un número de teléfono y, a continuación, elija el botón **Guardar** nuevamente.

    Se muestra un mosaico en la pantalla de inicio **Incidentes** con el número, la fecha y dirección de incidente.


12. Elija el mosaico para abrir la pantalla Vista para el incidente.

    Observe que el campo **Teléfono de informe** se muestra como hipervínculo, proporcionando acceso directo a la aplicación de teléfono predeterminada.


13. Elija el botón **Editar** para abrir la pantalla **AddEditIncidents** y, a continuación, elija el botón **Descartar** para volver a la pantalla **Vista**.


14. Elija la pestaña **Pacientes** para mostrar una lista de los pacientes y, a continuación, elija un mosaico de pacientes.

    Observe que no se abre una pantalla de visualización para el paciente. Esto se debe a que no ha creado ninguna aún.


15. Elija el botón **Cerrar** del explorador para volver al modo de diseño.







    En el siguiente paso, agregará una pantalla para ver a los pacientes.



### Para agregar una pantalla de visualización


1. En la pantalla **Explorador de soluciones**, abra el menú contextual del nodo **Patients.lsml** y elija **Abrir**.


2. En el diseñador de entidades de la barra **Perspectiva**, elija **HTMLClient** y, a continuación, en la barra de herramientas, elija el botón **Pantalla**. 

    Se abre el cuadro de diálogo **Agregar nueva pantalla**.


3. En el cuadro de diálogo **Agregar nueva pantalla** de la lista **Seleccione una plantilla de pantalla**, elija **Pantalla Ver detalles**.


4. En el cuadro de texto **Nombre de pantalla**, escriba **ViewPatient**, elija **Paciente** en la lista **Datos de pantalla** y, a continuación, elija el botón **Aceptar**.

    Se abre la pantalla **ViewPatient** en el diseñador de pantallas.


5. En el diseñador de pantallas, en el nodo **Diseño de filas | Detalles (pestaña)**, abra el menú contextual del nodo **Barra de comandos** y elija **Botón Agregar**.


6. En el cuadro de diálogo **Botón Agregar**, expanda la lista **showTab** y, en el grupo **Pacientes**, elija **Editar**y, a continuación, elija el botón **Aceptar**.


7. Ejecute la aplicación y compruebe que ahora pueda ver y editar los registros de los pacientes.







    Ha creado un complemento de administración de incidentes completamente funcional, pero hay mucho más que puede hacer con complementos empresariales de nube. En la siguiente sección, aprenderá a utilizar los recursos en el sitio de SharePoint desde el complemento de administración de incidentes.



## Integración de recursos de SharePoint
<a name="integrate"> </a>

El departamento de bomberos de Contoso ha empezado a utilizar el complemento Administrador de incidentes y, como es típico en proyectos de desarrollo de software, ahora solicitan una nueva característica. Además de información de incidentes y pacientes, también necesitarán administrar recursos tales como aparatos de incendio y personal. Ya tienen listas de aparatos y personal en su sitio de SharePoint, por lo que se utilizarán estas listas como otro origen de datos para el complemento.



El primer paso es agregar un par de plantillas de lista previamente completas a su sitio para desarrolladores.




### Para agregar plantillas de lista


1. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar el complemento.


2. En el complemento en ejecución, en la barra de cromo, elija el vínculo **Volver al sitio** para desplazarse al sitio para desarrolladores de SharePoint, como se muestra en la Figura 12.

   **Figura 12. El vínculo Volver al sitio**



!\[Navegar al sitio para desarrolladores de SharePoint](images/CBA_IM_8a.PNG)





3. En la página **Desarrollador**, elija el vínculo **Contenidos del sitio**.


4. En la página **Contenidos del sitio**, elija el vínculo **Configuración**, como se muestra en la Figura 13.

   **Figura 13. El vínculo Configuración**



!\[Vínculo Configuración del sitio](images/CBA_IM_8b.PNG)





5. En la página **Configuración del sitio** de la lista **Galerías del diseñador web**, elija el vínculo **Plantillas de lista**, como se muestra en la Figura 14.

   **Figura 14. El vínculo Plantillas de lista**



!\[Lista Plantillas de lista](images/CBA_IM_8c.PNG)





6. En la página **Galería de plantillas de lista**, elija la pestaña **ARCHIVOS** y, a continuación, en la cinta de opciones, elija el botón **Cargar documento**.


7. En el cuadro de diálogo **Agregar una plantilla**, elija el botón **Examinar** y, a continuación, vaya a la carpeta **Recursos** para el ejemplo de la administración de incidentes descargada.


8. Elija el archivo **ContosoApparatus.stp** y el botón **Abrir**. A continuación, elija el botón **Aceptar**.


9. En el cuadro de diálogo **Galería de plantillas de listas**, elija el botón **Guardar**.


10. Repita el proceso y cargue el archivo **ContosoPersonnel.stp**.

   **Figura 15. Los archivos cargados**



!\[Plantillas de Contoso](images/CBA_IM_8d.PNG)





11. Elija el vínculo **Contenidos del sitio** y, en la página **Contenidos del sitio**, elija el mosaico **agregar un complemento**.


12. En la página **Contenidos del sitio > Sus complementos**, elija el mosaico **Aparatos de Contoso**.

    > **NOTA**
      > Es posible que necesite ir a la segunda página de complementos para encontrar el mosaico **Aparatos de Contoso**. 
13. En el cuadro de diálogo **Agregar lista personalizada**, elija el cuadro de texto **Nombre** y escribaAparatos de Contoso y, a continuación, elija el botón **Crear**.


14. Repita el proceso, agregue la lista **Personal de Contoso** y escribaPersonal de Contoso como nombre.


15. En la página **Contenidos del sitio**, compruebe que las listas **Aparatos de Contoso** y **Personal de Contoso** aparezcan ahora.







    En el siguiente paso, agregará su sitio de SharePoint como origen de datos.



### Para agregar un origen de datos de SharePoint


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **Orígenes de datos** y elija **Agregar origen de datos**.

    Se abre el **Asistente para adjuntar orígenes de datos**.


2. En el **Asistente para adjuntar orígenes de datos**, elija el icono **SharePoint** como se muestra en la Figura 16 y, a continuación, elija el botón **Siguiente**.

   **Figura 16. Origen de datos de SharePoint**



!\[Origen de datos de SharePoint](images/CBA_IM_9.PNG)





3. En la página **Escribir información de conexión**, compruebe que la dirección URL de su sitio para desarrolladores de SharePoint sea correcta y, a continuación, elija el botón **Siguiente**.


4. En la página **Elija sus elementos de SharePoint**, active las casillas para las listas **ContosoApparatus** y **ContosoPersonnel** como se muestra en la Figura 17, y elija el botón **Finalizar**.

   **Figura 17. Las listas seleccionadas**



!\[Listas de SharePoint](images/CBA_IM_9a.PNG)


    Las entidades se agregan a un nuevo nodo **Datos de desarrollador** dentro del nodo **Orígenes de datos** en **Explorador de soluciones**, y la entidad **ContosoApparatus** se abre en el diseñador de entidades.


    > **NOTA**
      > Posiblemente haya observado que también se agregó una entidad **UserInformationLists**. SharePoint usa esta lista para administrar los campos **CreatedBy** y **ModifiedBy** para listas.
5. En la ventana **Propiedades**, elija la propiedad **Nombre para mostrar** y cámbiela aAparatos.







    En el siguiente paso, definirá una consulta para limitar los datos que la entidad **ApparatusSet** devuelve. En este caso, solo necesita ver los aparatos que se encuentran en servicio.



### Para definir la consulta AvailableApparatus


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **ApparatusSet.lsml** y elija **Agregar consulta**.

    Se abre el diseñador de consultas.


2. En la ventana **Propiedades**, elija la propiedad **Nombre** y escribaAvailableApparatus.


3. En el diseñador de consultas, elija el vínculo **Agregar filtro** y, a continuación, en la segunda lista desplegable elija **Disponible**.


4. En el último cuadro de texto, cambie **Falso** a **Verdadero**.

    La Figura 18 muestra la consulta.


   **Figura 18. La consulta AvailableApparatus**



!\[Consulta AvailableApparatus](images/CBA_IM_10.PNG)








    En el siguiente paso, definirá otra consulta para la entidad **ContosoPersonnel**. En este caso, solo necesita ver el personal asignado al turno "A"



### Para definir la consulta AvailableApparatus


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **ContosoPersonnels.lsml** y elija **Agregar consulta**.

    Se abre el diseñador de consultas.


2. En la ventana **Propiedades**, elija la propiedad **Nombre** y escribaAvailablePersonnel.


3. En el diseñador de consultas, elija el vínculo **Agregar filtro** y, a continuación, en la segunda lista desplegable elija **Turno**.


4. En el último cuadro de texto, escriba **A**.

    La Figura 19 muestra la consulta.


   **Figura 19. La consulta AvailablePersonnel**



!\[Cuadro de diálogo Relaciones](images/CBA_IM_10a.PNG)








    En el siguiente paso, agregará listas de aparatos disponibles y personal a la pantalla **ViewIncidents**.



### Para agregar consultas a la pantalla


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **ViewIncidents.lsml** y elija **Abrir**.


2. En el diseñador de pantallas, abra el menú contextual del nodo **Pestaña** y elija **Agregar pestaña**.


3. En la ventana **Propiedades**, cambie la propiedad **Nombre** aRecursos.


4. En el diseñador de pantallas, en la barra de herramientas, elija **Agregar elemento de datos**.

    Se abre el cuadro de diálogo **Agregar elemento de datos**.


5. En el cuadro de diálogo **Agregar elemento de datos**, elija el botón de opción **Consulta**.


6. En la lista, elija **DeveloperData.AvailableApparatus** como se muestra en la Figura 20 y, a continuación, elija el botón **Aceptar**.

   **Figura 20. La consulta AvailableApparatus**



!\[Agregar una consulta](images/CBA_IM_11.PNG)


    El grupo **AvailableApparatus** se agrega a la parte izquierda del diseñador de pantallas.


7. Repita el proceso para agregar la consulta **AvailablePersonnel** a la pantalla.


8. En el nodo **Diseño de filas | Recursos**, abra la lista **Agregar** y elija **Aparatos disponibles**.


9. Elija el nodo **Lista | Aparatos disponibles**, abra la lista **Lista** y elija **Lista de mosaicos**.


10. Elimine todos los elementos en el **Diseño de filas | Aparatos de Contoso** excepto **Número de aparatos** y **Tipo de aparatos**.


11. En el nodo **Diseño de filas | Recursos**, abra la lista **Agregar** y elija **Personal disponible**.


12. Elija el nodo **Lista | Personal disponible**, abra la lista **Lista** y elija **Tabla**.


13. Elimine todos los elementos en el nodo **Fila de tabla | Personal de Contoso** excepto **Nombre**, **Rango** y **Asignación**.

    La Figura 21 muestra el diseño de la pantalla.


   **Figura 21. El diseño de la pantalla**



!\[Diseño de la pestaña Recursos](images/CBA_IM_11a.PNG)





14. Ejecute el complemento y observe los cambios. Elija un incidente y luego elija la pestaña **Recursos** para ver una lista de recursos disponibles.







    Los cambios ya están terminados. En la sección siguiente, aprenderá cómo agregar y asociar una biblioteca de documentos de SharePoint con el complemento.



## Asociación de una biblioteca de documentos
<a name="associate"> </a>

En el lugar de un incidente, el Comandante de incidentes debe documentar muchas cosas, algunas con los formularios existentes y algunas ad-hoc. Los documentos creados en el lugar deben ser accesibles más adelante, y deben estar organizados por incidente para facilitar su recuperación. Para el complemento de administración de incidentes, puede utilizar la característica de la biblioteca de documentos personalizada en SharePoint para asociar documentos con cada incidente.




### Para agregar una biblioteca de documentos en el sitio de SharePoint


1. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar el complemento.


2. En el complemento en ejecución, en la barra de cromo, elija el vínculo **Volver al sitio**, como se muestra en la Figura 22, para desplazarse al sitio para desarrolladores de SharePoint.

   **Figura 22. El vínculo Volver al sitio**



!\[Navegar al sitio para desarrolladores de SharePoint](images/CBA_IM_8a.PNG)





3. En la página **Desarrollador**, elija el vínculo **Contenidos del sitio**.


4. En la página **Contenidos del sitio**, elija el mosaico **agregar un complemento**.


5. En la página **Contenidos del sitio > Sus complementos**, elija el mosaico **Biblioteca de documentos**.


6. En el cuadro de diálogo **Adición de biblioteca de documentos**, en el cuadro de texto **Nombre** escribaDocumentos de incidentes y, a continuación, elija el botón **Crear**.


7. En la página **Contenidos del sitio**, elija el mosaico **Documentos de incidentes** para abrir la biblioteca y, a continuación, elija la pestaña **BIBLIOTECA**.


8. En la **Cinta**, elija el botón **Crear columna**.


9. En el cuadro de diálogo **Crear columna**, en el cuadro de texto **Nombre de columna** escribaIncidentNumber y, a continuación, elija el botón **Aceptar**.

    La Figura 23 muestra la columna agregada recientemente.


   **Figura 23. La biblioteca de documentos correspondiente a los Documentos de incidentes**



!\[Biblioteca de documentos con la columna IncidentNumber](images/CBA_IM_12.PNG)


    Para asociar la biblioteca de documentos con el complemento, la biblioteca de documentos debe contener una columna personalizada que se asigna a un campo único en la entidad. En este caso, la columna **IncidentNumber** se asigna al campo **IncidentNumber** de la entidad **Incidentes**.







    En el siguiente paso, agregará la biblioteca de documentos a su complemento.



### Para agregar una biblioteca de documentos a un proyecto


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **Datos de desarrollador** y elija **Actualizar origen de datos**.


2. En la página **Elija sus elementos de SharePoint** del panel izquierdo, elija el elemento de lista **Bibliotecas de documentos**y, en el panel derecho, active la casilla **IncidentDocuments** como se muestra en la Figura 24, y luego elija el botón **Finalizar**.

   **Figura 24. La entidad IncidentDocuments**



!\[Seleccionar la biblioteca de documentos](images/CBA_IM_13.PNG)


    Se agrega un nodo **IncidentDocuments.lsml** al Explorador de soluciones.







    En el paso siguiente, creará una relación entre la entidad de Incidentes y la biblioteca de documentos.



### Para crear una relación entre los orígenes de datos


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **IncidentDocuments.lsml** y elija **Abrir**.


2. En el diseñador de entidades de la barra **Perspectiva**, elija **Servidor**y, a continuación, en la barra de herramientas, elija **Relación**.


3. En el cuadro de diálogo **Agregar nueva relación**, en la lista desplegable **Para**, elija **Incidente** como se muestra en la Figura 25.

   **Figura 25. El cuadro de diálogo Agregar nueva relación**



!\[Relación entre entidades](images/CBA_IM_11b.PNG)





4. En la lista desplegable de claves **Externas**, elija el campo **IncidentNumber (String)** de la entidad **IncidentDocuments**.


5. En la lista desplegable de claves **Principales**, elija el campo **IncidentNumber (String)** de la entidad **Incidentes** y, a continuación, elija el botón **Aceptar**.

    La Figura 26 muestra las claves Externas y Principales.


   **Figura 26. Claves externas y principales**



!\[Campos asociados](images/CBA_IM_11c.PNG)








    En el siguiente paso, agregará la biblioteca de documentos a la pantalla **ViewIncidents**.



### Para agregar una biblioteca de documentos a una pantalla


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **ViewIncidents.lsml** y elija **Abrir**.


2. En el diseñador de pantallas, abra el menú contextual del nodo **Pestaña** y elija **Agregar pestaña**.


3. En la ventana **Propiedades**, cambie la propiedad **Nombre** aDocumentos.


4. En el diseñador de pantallas, en el panel izquierdo, elija el vínculo **Agregar IncidentDocuments**.


5. En el panel central, en el nodo **Diseño de filas | Documentos**, abra la lista **Agregar** y elija **Documentos de incidente**.


6. En el nodo **Diseño de filas | Documentos**, abra el menú contextual del nodo **Barra de comandos** y elija **Botón Agregar**.


7. En el cuadro de diálogo **Botón Agregar**, expanda la lista **showTab** y, en el grupo **IncidentDocuments**, elija **createOrUploadDocument**, como se muestra en la Figura 27, y, a continuación, elija el botón **Aceptar**.

   **Figura 27. El cuadro de diálogo Botón Agregar**



!\[Botón para crear o subir documentos](images/CBA_IM_14.PNG)





8. En la ventana **Propiedades**, elija la propiedad **Nombre para mostrar** y escribaAgregar documento.


9. Expanda la lista de propiedades **Icono** y elija **Datos adjuntos**.


10. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar el complemento.


11. Elija un incidente, elija la pestaña **Documentos** y, a continuación, elija el botón **Agregar documento**.

    Se abre el cuadro de diálogo **Crear un nuevo archivo** de SharePoint, como se muestra en la Figura 28.


   **Figura 28. El cuadro de diálogo Crear un nuevo archivo**



!\[Cuadro de diálogo Crear un archivo nuevo de SharePoint](images/CBA_IM_15.PNG)





12. Elija el vínculo **CARGAR EL ARCHIVO EXISTENTE** y elija cualquier archivo para cargar y, a continuación, elija el botón **Abrir**.

    El archivo se agrega a la pestaña **Documentos**.

    > **SUGERENCIA**
      > Si elige un documento de Office, puede verlo en el complemento. 
13. Elija el botón Agregar documento y, en el cuadro de diálogo **Crear un nuevo archivo**, elija **Documento de Word**.

    Se abre un nuevo documento de Word en **Word en línea**.


14. En la barra de título, elija el nombre de campo **Documento** como se muestra en la Figura 29 y escribaInforme de incidente.

   **Figura 29. El nombre de campo Documento**



!\[Nombre de archivo](images/CBA_IM_16.PNG)


    Se utilizará como nombre de archivo para el documento.


    > **NOTA**
      > Si no escribe un nombre de archivo, se guardará con el nombre predeterminado **Document.docx**. Una vez guardado, solo puede cambiarse el nombre del archivo accediendo a él en la biblioteca de documentos de SharePoint. 
15. Elija el botón Atrás de su explorador para volver al complemento. El documento **Informe de incidente** debe aparecer en la pestaña **documentos**.


16. Elija el botón **Cerrar** del explorador para volver al modo de diseño.







    En la siguiente sección, personalizará el complemento y agregará código JavaScript.



## Personalización del complemento
<a name="custom"> </a>

El Departamento de bomberos de Contoso está muy satisfecho con las adiciones al complemento Administración de incidentes, pero hay algunos elementos más "para retocar y terminar" en su lista. Desean mostrar el logotipo propio en las pantallas en lugar del icono predeterminado. No les gusta el control **FlipSwitch** para el campo **Asegurados** de la pantalla **AddEditPatient** y desean reemplazarla con una casilla. Por último, dado que no todos los incidentes tienen pacientes, no quieren que la pestaña **Pacientes** de la pantalla **ViewIncidents** aparezca si no hay ningún paciente.




### Para mostrar un logotipo personalizado


1. En el **Explorador de soluciones**, en el proyecto **IncidentManager.HtmlClient**, expanda los nodos **Contenido** e **Imágenes**.


2. Elija los archivos **user-logo.png** y **user-splash-screen.png** como se muestra en la Figura 30 y elimínelos.

   **Figura 30. Los archivos para eliminar**



!\[Archivos de logotipo y pantalla de presentación](images/CBA_IM_17.PNG)





3. Abra el menú contextual del nodo **Imágenes**, elija **Agregar**, **Elemento existente**.


4. En el cuadro de diálogo **Agregar elemento existente**, elija el botón **Examinar** y, a continuación, vaya a la carpeta **Recursos** para el ejemplo descargado de Administración de incidentes.


5. Elija los archivos **user-logo.png** y **user-splash-screen.png** y, a continuación, elija el botón **Agregar**.

    Las imágenes nuevas se mostrarán en la barra de título del complemento y en la pantalla de bienvenida que aparece cuando se carga el complemento.







    En el siguiente paso, reemplazará el control **FlipSwitch** con una casilla.



### Para reemplazar el control FlipSwitch


1. En la pantalla **Explorador de soluciones**, abra el menú contextual del nodo de pantalla **AddEditPatient.lsml** y elija **Abrir**.


2. En el diseñador de pantallas, expanda la lista del nodo **Asegurados** y elija **Control personalizado**.


3. En la ventana **Propiedades**, elija el vínculo **Editar código de representación**.


4. En el Editor de código, agregue el siguiente código al método **Insured_render**

 ```

// Create the checkbox and add it to the DOM.
    var checkbox = $("<input type='checkbox'/>")
            .css({
                height: 20,
                width: 20,
                margin: "10px"
            })
            .appendTo($(element));

    // Determine if the change was initiated by the user.
    var changingValue = false;

    checkbox.change(function () {
        changingValue = true;
        contentItem.value = checkbox[0].checked;
        changingValue = false;
    });
    contentItem.dataBind("value", function (newValue) {
        if (!changingValue) {
            checkbox[0].checked = newValue;
        }
    });
 ```


    Este código crea un control **Casilla** cuando se representa la pantalla. También necesitará agregar código para establecer un valor inicial.


5. En la pantalla **Explorador de soluciones**, abra el menú contextual del nodo de entidad **Patients.lsml** y elija **Abrir**.


6. En el diseñador de entidades, en la barra **Perspectiva**, elija la pestaña **HTMLClient**.


7. En la barra de herramientas, expanda la lista **Escribir código** y elija **creado**.


8. En el Editor de código, agregue el siguiente código al método **created**:

 ```

entity.Insured = new Boolean();
    entity.Insured = 'true';
 ```


    Este código establece el valor inicial del control como activado (verdadero) cuando se crea la pantalla.







    En el siguiente paso, agregará código para ocultar la pestaña **Pacientes** si no existen pacientes.



### Para ocultar una pestañade forma condicional


1. En el **Explorador de soluciones**, abra el menú contextual del nodo de pantalla **ViewIncidents.lsml** y elija **Abrir**.


2. En la barra de herramientas, abra la lista **Escribir código** y elija **creado**.


3. En el Editor de código, agregue el siguiente código al método **ViewIncidents_created**:

 ```

screen.getPatients().then(function (results) {
        var queryCount = results.count;
        if (queryCount == 0 ) {
            screen.findContentItem("Patients").isVisible = false;
        }
    });
 ```


    Este código ejecuta la consulta **GetPatients** para obtener un recuento de **Patients**. Si el resultado es cero, el método **findContentItem** establece la propiedad **isVisible** de la pestaña **Pacientes** como falsa, y luego la oculta.


4. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar el complemento. Observe que el nuevo logotipo aparece en las pantallas. Abra el primer incidente que creó anteriormente y edite un paciente. Compruebe que el campo Asegurado ahora sea una casilla. Agregue un nuevo incidente sin pacientes y compruebe que la pestaña **Pacientes** está oculta en la pantalla de vista.







    En la sección final, publicará el complemento terminado en SharePoint.



## Publicación en SharePoint
<a name="pub"> </a>

Hasta ahora solo ha ejecutado el complemento en modo de depuración, que utiliza a SharePoint para proporcionar autenticación y redirige a la instancia del equipo local de IIS Express. A continuación, publicará el complemento como complemento de SharePoint autohospedado, que aprovisionará automáticamente espacio en Office 365 para el complemento y una base de datos intrínseca. Específicamente, el complemento se hospedará en Microsoft Azure y la base de datos en SQL Azure. Después de publicar el complemento, otros usuarios pueden ejecutarlo desde SharePoint en sus equipos y dispositivos móviles.




### Para publicar el complemento


1. En la barra de herramientas de Visual Studio, abra la lista **Depurar** y elija **Versión**.


2. En el **Explorador de soluciones**, abra el menú contextual del nodo **IncidentManager** como se muestra en la Figura 31 y, a continuación, elija **Publicar**.

   **Figura 31. El nodo IncidentManager**



!\[Nodo IncidentManager](images/CBA_IM_18.PNG)


    Aparece el **Asistente para publicación de aplicaciones de LightSwitch**.


3. En la página de opciones de **SharePoint**, elija el botón de opción **Autohospedado** como se muestra en la Figura 32, y luego elija el botón **Publicar**.

   **Figura 32. La opción Autohospedado**



!\[Asistente para publicación](images/CBA_IM_19.PNG)


    Al publicar el complemento, aparece el **Explorador de archivos** y se muestra el directorio **Publicar** para el proyecto.


4. En el explorador, vaya al sitio para desarrolladores de SharePoint


5. En la lista **Complementos en fase de prueba**, elija el vínculo de puntos suspensivos (...) junto a **IncidentManager**y, a continuación, elija el vínculo **Quitar**, como se muestra en la Figura 33.

   **Figura 33. El vínculo Quitar**



!\[Aplicaciones en la lista Prueba](images/CBA_IM_20.PNG)





6. Elija el vínculo **nuevo complemento para implementar**.


7. En el cuadro de diálogo **Implementar complemento**, elija el vínculo **cargar** como se muestra en la Figura 34.

   **Figura 34. El vínculo cargar**



!\[Cuadro de diálogo Implementar aplicación](images/CBA_IM_20a.PNG)


    Se abre el cuadro de diálogo **Cargar complemento**.


8. En el cuadro de diálogo **Cargar complemento**, elija el botón **Examinar** y desplácese hasta la carpeta **Publicar** de su complemento y elija el archivo **IncidentManager.SharePoint.app**, elija el botón **Abrir** y, a continuación, elija el botón **Aceptar**.

    Una vez que se haya cargado el archivo, se abrirá el cuadro de diálogo **Implementar complemento**.


9. En el cuadro de diálogo **Implementar complemento**, elija el botón **Implementar**.


10. En el cuadro de diálogo **Cargar complemento**, elija el botón **Confiar**.

    El complemento se instalará en su sitio de SharePoint. El proceso puede durar unos minutos.

    La Figura 35 muestra la lista Complementos en fase de prueba, a medida que se instala el complemento.


   **Figura 35. La lista Complementos en fase de prueba**



!\[La aplicación se está instalando](images/CBA_IM_21.PNG)





11. Elija el vínculo **Contenidos del sitio**.

    La Figura 37 muestra la lista Contenidos del sitio a medida que se instala el complemento.


   **Figura 37. Se está instalando el complemento**



!\[La aplicación se está instalando](images/CBA_IM_22.PNG)





12. Una vez terminada la instalación del complemento, elija el mosaico **Administrador de incidentes** para ejecutar el complemento.

    La Figura 38 muestra la lista Contenidos del sitio después de instalarse el complemento.


   **Figura 38. El complemento está instalado**



!\[La aplicación está lista para ejecutarse](images/CBA_IM_23.PNG)


    El complemento se abre en el explorador y tiene exactamente el mismo aspecto que cuando lo ejecutó en modo de depuración. Observe que los datos escritos durante el desarrollo han desaparecido. Al implementar el complemento, se implementa la base de datos pero no los datos.


13. Escriba algunos datos y compruebe que todo funciona como se esperaba.

    La Figura 39 muestra el complemento como aparece en un explorador de escritorio.


   **Figura 39. Vista Mosaico**



!\[Diseño en explorador de escritorio](images/CBA_IM_24.PNG)


    También puede comprobar el complemento en un dispositivo móvil. Inicie sesión en el sitio de SharePoint desde el explorador web en el dispositivo y ejecute el complemento.

    La Figura 40 muestra el complemento como aparece en un explorador móvil.


   **Figura 40. Vista Lista**



!\[Diseño en dispositivo móvil](images/CBA_IM_25.PNG)





¡Enhorabuena! Ha terminado el tutorial y conoce los principios básicos de la creación de un complemento empresarial de nube. Ahora anímese y cree un complemento propio, los recursos adicionales siguientes le ayudarán a avanzar más allá.
## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Introducción al desarrollo de complementos empresariales de nube](get-started-developing-cloud-business-add-ins.md)


-  [Desarrollo de complementos empresariales de nube](develop-cloud-business-add-ins.md)


-  [Publicación de complementos empresariales de nube](publish-cloud-business-add-ins.md)



