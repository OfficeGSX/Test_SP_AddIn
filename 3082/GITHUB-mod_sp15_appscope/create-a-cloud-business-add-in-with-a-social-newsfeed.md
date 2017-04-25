---
title: Procedimiento para crear una aplicación empresarial de nube con un suministro de noticias de redes sociales
ms.prod: SHAREPOINT
ms.assetid: fe15c732-eea0-43cf-92e8-99ff776cb9e2
---


# Procedimiento para crear una aplicación empresarial de nube con un suministro de noticias de redes sociales
Con la plantilla **Complemento empresarial en la nube** en Visual Studio, puede crear complementos para SharePoint en el que los usuarios pueden ver, agregar y actualizar los datos de SharePoint. El tipo de proyecto **Complemento empresarial en la nube** utiliza tecnologías LightSwitch de Visual Studio que simplifican enormemente el proceso de creación de un complemento empresarial hospedado en SharePoint. En este tutorial, creará un complemento para realizar un seguimiento de los candidatos a un puesto, aprovechando la característica de integración social de SharePoint para Office 365 con el fin de proporcionar un suministro de noticias para el complemento.Puede descargar la aplicación de muestra completa desde la galería de códigos de MSDN:  [Creación de un complemento empresarial en la nube con un suministro de noticias sociales](http://code.msdn.microsoft.com/Creating-a-Cloud-Business-8540c0c9)
## Requisitos previos

Este tutorial requiere Visual Studio 2013.



Para hospedar el complemento, necesitará un sitio de SharePoint 2013 o un sitio para desarrolladores de SharePoint en Office 365, que puede obtenerlos desde  [Regístrese en un sitio para desarrolladores de Office 365](http://go.microsoft.com/fwlink/?LinkId=263490).




## Creación de un proyecto de complemento empresarial en la nube
<a name="bk_create"> </a>

 Para crear un complemento empresarial en la nube, empiece con la plantilla del proyecto **Complemento empresarial en la nube** en Visual Studio.




### Para crear el proyecto


1. En la barra de menús, elija **Archivo**, **Nuevo** y **Proyecto**.

    Se abre el cuadro de diálogo **Nuevo proyecto**.


2. En la lista de plantillas, expanda el nodo **Visual Basic** o **Visual C#**, expanda el nodo **Office/SharePoint**, elija el nodo **Complementos**, y luego elija la plantilla **Complemento empresarial en la nube**, como se muestra en la Figura 1.

   **Figura 1. Adición del proyecto**



!\[Plantilla de proyecto de aplicación empresarial de nube](images/CBAtemplate.PNG)





3. En el cuadro de texto **Nombre**, escriba CandidateTracker y luego elija el botón **Aceptar**.

    Se abre el asistente para **Nuevo complemento empresarial en la nube**.


4. En el asistente **Nuevo complemento empresarial en la nube**, escriba la dirección URL de su sitio para desarrolladores de Office 365 y, a continuación, elija el botón **Finalizar**.

    La dirección URL debe tener la forma https:// _MiSitio_.sharepoint.com/sites/Developer/.

    Se agrega una solución **CandidateTracker** al Explorador de soluciones con cuatro proyectos: un proyecto de nivel superior **CandidateTracker**, un proyecto **CandidateTracker.HTMLClient**, un proyecto **CandidateTracker.Server** y un proyecto **CandidateTracker.SharePoint**.



## Adición de datos y pantallas
<a name="bk_add"> </a>

El complemento de seguimiento de candidatos necesita una tabla de base de datos, que usted creará en el proyecto **Servidor**, y pantallas para ver, agregar y actualizar datos, que usted creará en el proyecto **HTMLClient**.




### Para agregar la tabla de base de datos


1. En el **Explorador de soluciones**, abra el menú contextual del nodo **CandidateTracker.Server** y elija **Agregar tabla**.

    Se abre el diseñador de entidades.


2. En la ventana **Propiedades** del cuadro de texto para la propiedad **Nombre**, escriba Candidato.


3. En el diseñador de entidades, elija el vínculo **<Agregar propiedad>** y escribaNombre.


4. Pulse la tecla Entrar, aceptando el **Tipo** predeterminado, **String** y dejando activada la casilla **Requerido**.


5. Elija el vínculo **<Agregar propiedad>**, escriba Teléfono y pulse la tecla **Tabulador**.


6. En la columna **Tipo**, elija **Número de teléfono** y, a continuación, pulse la teclaTabulador dos veces.

    **Número de teléfono** es untipo de negocio personalizado que aplica formato y proporciona validación de números de teléfono de forma automática.


7. En la siguiente fila, escriba Correo electrónico para el **Nombre** y, a continuación, elija **Dirección de correo electrónico** como **Tipo**.

    **Dirección de correo electrónico** es otro tipo de negocio personalizado que proporciona validación de direcciones de correo electrónico.


8. En la siguiente fila, escriba ReferredBy para el **Nombre** y, a continuación, elija **Persona** como **Tipo**.

    El tipo de negocio **Persona** proporciona una conexión al servicio de perfil de usuario de SharePoint, que extrae información del usuario de Active Directory.


9. En la siguiente fila, escriba InterviewDate para el **Nombre** y, a continuación, elija **Desplazamiento de fecha y hora** como **Tipo**.

    El tipo de negocio **Desplazamiento de fecha y hora** almacena la fecha y la hora en formato UTC, permitiendo que los usuarios en distintas zonas horarias puedan ver la hora correcta.

    La Figura 2 muestra la entidad completada.


   **Figura 2. La entidad Candidatos**



!\[Tabla Candidatos](images/CBAentity.PNG)






### Para crear la pantalla de exploración


1. En el diseñador de entidades de la barra **Perspectiva**, elija la pestaña **Cliente HTML** y, a continuación, elija en la barra de herramientas **Pantalla**.

    Se abre el cuadro de diálogo **Agregar nueva pantalla**.


2. En el cuadro de diálogo **Agregar nueva pantalla** de la lista **Seleccione una plantilla de pantalla**, elija **Pantalla Examinar datos**.


3. En la columna **Datos de pantalla**, elija **Candidatos**, y luego elija el botón **Aceptar**.

    Se abre el diseñador de pantallas, con una representación de la entidad de datos en el panel izquierdo y una representación del diseño de pantalla en el panel derecho.


4. En el panel derecho del diseñador de pantallas, seleccione el nodo **Lista | Candidatos**, a continuación, expanda la lista **Lista** y elija **Tabla**, como se muestra en la Figura 3.

   **Figura 3. El control Tabla**



!\[Diseñador de la pantalla BrowseCandidates](images/CBAscreen.PNG)


    Se mostrará la información **Candidato** en un formato tabular en lugar del formato predeterminado de lista.

    Observe que, además de los campos que ha definido para la tabla hay cuatro campos más: Creado por, Creado, Modificado por y Modificado. Estos campos crean un código de seguimiento para mostrar cuándo se agregó o actualizó por última vez un elemento y quién lo hizo. 



### Para crear la pantalla Agregar


1. En el diseñador de pantallas, abra el menú contextual del nodo **Barra de comandos** y elija **Botón agregar**.


2. En el cuadro de diálogo **Botón Agregar**, expanda la lista **showTab** y elija **addAndEditNew** como se muestra en la Figura 4 y, a continuación, elija el botón **Aceptar**.

   **Figura 4. El cuadro de diálogo Botón Agregar**



!\[Cuadro de diálogo Agregar un botón](images/CBAadd.PNG)


    Se abre el cuadro de diálogo **Agregar nueva pantalla**.


3. En el cuadro de diálogo **Agregar nueva pantalla**, acepte los valores predeterminados y elija el botón **Aceptar**.


Se abrirá una nueva ventana de diseñador de pantalla.




### Para crear la pantalla de edición


1. Elija la pestaña de diseñador **BrowseCandidates.lsml** y, a continuación, en el diseñador de pantallas, elija el nodo **Tabla | Candidatos**.


2. En la ventana **Propiedades**, elija el vínculo **Puntear elemento**, como se muestra en la Figura 5.

   **Figura 5. El vínculo Puntear elemento**



!\[Propiedad ItemTap](images/CBAproperty.PNG)


    Se abre el cuadro de diálogo **Editar acción de Puntear elemento**.


3. En el cuadro de diálogo **Editar acción de Puntear elemento**, expanda la lista **showTab** y elija **editSelected** como se muestra en la Figura 6 y, a continuación, elija el botón **Aceptar**.

   **Figura 6. El cuadro de diálogo Editar acción de Puntear elemento**



!\[Cuadro de diálogo Editar acción ItemTap](images/CBAaction.PNG)






### Para probar el complemento


1. En la barra de menús, elija **Depurar**, **Iniciar depuración**.

    La primera vez que ejecute un complemento de SharePoint en el equipo, se le pedirá que instale un certificado de Localhost porque los complementos empresariales en la nube siempre utilizan SSL (Capa de sockets seguros). Si acepta este certificado, no aparecerá una advertencia de seguridad cada vez que ejecute el complemento. Dado que el certificado se aplica solo a Localhost, el sistema no enfrenta ninguna amenaza.


2. En el cuadro de diálogo de **Alerta de seguridad**, elija el botón **Sí**.


3. Si aparece un cuadro de diálogo de **Advertencia de seguridad**, elija el botón **Sí**.

    Aparecerá el explorador web.


4. En la página de **iniciar sesión**, elija el botón **Iniciar sesión**.


5. Escriba su contraseña y, a continuación, elija el botón de **Iniciar sesión**.


6. Si se le solicita, elija el botón **Confiar**.


7. En la pantalla para **examinar candidatos**, elija el botón **agregar candidato**.

    Se abre la pantalla para **Agregar candidato**.


8. En la pantalla para **Agregar candidato**, escriba un nombre, número de teléfono y una dirección de correo electrónico.


9. Elija el campo **Remitido por** y escriba los tres primeros caracteres de su nombre.

    Su nombre completo debe aparecer en la lista. Si el sitio está conectado a Active Directory, verá una lista de todos los usuarios cuyo nombre comienza con los mismos tres caracteres.


10. Opcionalmente, cambie la fecha y hora de la **Fecha de la entrevista** y, a continuación, elija el botón **Guardar** para volver a la pantalla para **Examinar candidatos**.

    Se muestra el candidato que acaba de agregar; si selecciona la fila, se abre la pantalla **Agregar candidatos de edición**. Observe que algunos de los campos heredan comportamientos especiales de sus tipos empresariales personalizados. Elija el campo **Teléfono** para marcar el número, elija el campo **Correo electrónico** para enviar un coreo electrónico, o mantenga el mouse sobre el campo **Creado por** para ver su información de contacto.


11. Cierre la ventana del explorador para detener la ejecución del complemento.



## Adición de un suministro de noticias
<a name="bk_feed"> </a>

Una de las características de SharePoint para Office 365 es la capacidad de agregar suministros de noticias, permitiendo a los usuarios realizar un seguimiento de la actividad en la lista y agregar sus comentarios. Los complementos empresariales en la nube facilitan la creación de un suministro de noticias para su complemento.




### Para habilitar la integración social


1. Elija la pestaña del diseñador **Candidates.lsml** y, a continuación, en la barra **Perspectiva**, elija la pestaña **Servidor**.


2. En la ventana **Propiedades**, active las casillas **Publicar al crear** o **Publicar al actualizar** como se muestra en la Figura 7.

   **Figura 7. Propiedades sociales**



!\[Propiedades sociales](images/CBAsocial.PNG)





3. Elija el vínculo **Elegir desencadenadores de publicación**.

    Aparece el cuadro de diálogo **Elegir desencadenadores de publicación**.


4. En el cuadro de diálogo **Elegir desencadenadores de publicación**, desactive la casilla **Todos los campos**, active la casilla **Fecha de la entrevista** y, a continuación, elija el botón **Aceptar**.



### Para probar el complemento


1. En la barra de menús, elija **Depurar**, **Iniciar depuración**.


2. En la pantalla para **Examinar candidatos**, elija el candidato que ha creado anteriormente.


3. En la pantalla para **Agregar candidatos de edición**, cambie la **Fecha de la entrevista** y, a continuación, elija el botón **Guardar**.


4. Elija el botón para **Agregar candidato** y agregue otro candidato.


5. En la pantalla para **Examinar candidatos**, en la barra de cromo de SharePoint, elija el vínculo **Suministro de noticias**.

    El **Suministro de noticias de seguimiento de candidatos** se abre en una nueva ventana del explorador con dos entradas para los candidatos agregados y actualizados. Puede elegir el vínculo **Me gusta** para una publicación, o el vínculo **Responder** para agregar un comentario.


6. Cierre ambas ventanas del explorador para detener la ejecución del complemento.



## Adición de validación
<a name="bk_validate"> </a>

En muchos complementos empresariales, necesitará restringir el acceso a determinadas funciones, por ejemplo, permitir que solo un administrador cambie la fecha de la entrevista. Los complementos empresariales en la nube le permiten hacer esto al escribir una lógica de validación; en este caso, escribirá código para determinar si el usuario actual es miembro de un grupo de seguridad de Active Directory específico.




### Para agregar la validación


1. Elija la pestaña del diseñador **Candidates.lsml** y, a continuación, en la barra **Perspectiva**, elija la pestaña **Servidor**.


2. En la barra de herramientas, expanda la lista **Escribir código** y elija el método **Candidates_Validate**.

    Se abrirá el Editor de código.


3. En el Editor de código, agregue el siguiente código al método **Candidates_Validate**:

 ```VB.net

If Not Application.User.Department = "Hiring Managers" Then
                results.AddEntityError("Permission denied")
            End If
 ```


 ```cs

if (!(Application.User.Department == "Hiring Managers")) {
results.AddEntityError("Permission denied");
}
 ```


    El método **Validate** se ejecuta cuando un usuario intenta guardar un registro. Si el usuario es un miembro del grupo de seguridad Administradores de contratación, se guarda el registro; de lo contrario, aparece un mensaje de error "Permiso denegado" y se descarta el registro.


En este punto, si ejecuta el complemento y agrega un nuevo candidato, posiblemente verá el mensaje de error a menos que sea miembro de un grupo de seguridad real llamado Administradores de contratación. Reemplace "Administradores de contratación" con el nombre de un grupo de seguridad al que pertenece y vuelva a intentarlo. Ahora debe tener permiso para agregar un candidato.




## Pasos siguientes
<a name="bk_validate"> </a>

Ha creado su primer complemento empresarial en la nube. Si se trata de un complemento que desea compartir con los usuarios, el siguiente paso sería publicar el complemento. Consulte  [Procedimiento para publicar un complemento empresarial en la nube en Office 365](http://msdn.microsoft.com/es-es/library/vstudio/dn454601.aspx) o [Publicación de complementos para SharePoint](http://msdn.microsoft.com/es-es/library/office/apps/jj164070.aspx).



Obviamente, esto es tan solo un ejemplo sencillo; puede hacer muchas cosas más para crear complementos con características completas. Dado que la plantilla Complemento empresarial en la nube está basado en tecnologías LightSwitch, posiblemente desee explorar la documentación de LightSwitch para recopilar ideas. Consulte  [Complementos LightSwitch para SharePoint](http://msdn.microsoft.com/es-es/library/vstudio/jj969620.aspx).




## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Creación de complementos empresariales de nube](create-cloud-business-add-ins.md)



