---
title: Habilitar un suministro de noticias para un complemento empresarial de nube
ms.prod: SHAREPOINT
ms.assetid: 3eec1f65-addb-4bfa-940c-dae3ac1e0c8a
---


# Habilitar un suministro de noticias para un complemento empresarial de nube
Las características sociales y de colaboración de SharePoint para Office 365 permiten a los usuarios realizar un seguimiento de la actividad en una lista y agregar comentarios. Puede crear fácilmente un suministro de noticias para el complemento empresarial de nube al habilitar un par de propiedades.
## Requisitos previos

Para hospedar un suministro de noticias, necesitará un sitio para desarrolladores de SharePoint en Office 365, que puede obtenerlo desde  [Regístrese en un sitio para desarrolladores de Office 365](http://go.microsoft.com/fwlink/?LinkId=263490).




## Procedimientos


### Para habilitar un suministro de noticias


1. En el Explorador de soluciones, abra la entidad que representa la lista en la que desea agregar un suministro de noticias, y, a continuación, en la barra **Perspectiva**, elija la pestaña **Servidor**.


2. En la ventana **Propiedades**, active las casillas **Publicar al crear** o **Publicar al actualizar**.

!\[Propiedades sociales](images/CBAsocial.PNG)


    **Publicar al crear** agrega un subproceso al suministro de noticias para cada elemento de lista nuevo. **Publicar al actualizar** agrega un subproceso cuando cambia el valor para un elemento de la lista. Los desencadenadores de publicación determinan qué campos del elemento desencadenarán una publicación.


3. Elija el vínculo **Elegir desencadenadores de publicación**.

    Aparece el cuadro de diálogo **Elegir desencadenadores de publicación**.


4. En el cuadro de diálogo **Elegir desencadenadores de publicación**, active las casillas para todos los campos en los que desea desencadenar una publicación y, a continuación, elija el botón **Aceptar**.

    Se creará un único subproceso para todos los cambios en un elemento independientemente de la cantidad de campos elegidos.



### Para tener acceso a un suministro de noticias


1. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar la aplicación.


2. En la aplicación en ejecución, abra la pantalla de exploración de la entidad que representa la lista donde agregó un suministro de noticias. Si habilitó **Publicar al crear**, agregue un elemento nuevo. Si habilitó **Publicar al actualizar**, edite los campos seleccionados en el cuadro de diálogo **Elegir desencadenadores de publicación**.


3. En la barra de cromo de SharePoint, elija el vínculo **Suministro de noticias**.

!\[Barra de cromo de SharePoint](images/CBAnewsfeed.PNG)


    La página **Suministro de noticias** se abre en una nueva ventana del explorador con entradas para los elementos agregados o actualizados. Puede elegir el vínculo **Me gusta** para una publicación, o el vínculo **Responder** para agregar un comentario.



## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Desarrollo de complementos empresariales de nube](develop-cloud-business-add-ins.md)


-  [Características sociales y de colaboración en SharePoint 2013](http://msdn.microsoft.com/es-es/library/office/jj163280.aspx)


-  [Procedimiento para crear una aplicación empresarial de nube con un suministro de noticias de redes sociales](create-a-cloud-business-add-in-with-a-social-newsfeed.md)



