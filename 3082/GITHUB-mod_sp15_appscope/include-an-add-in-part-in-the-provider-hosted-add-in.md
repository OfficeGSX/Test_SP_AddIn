---
title: Incluir un elemento de complemento en el complemento hospedado por el proveedor
ms.prod: SHAREPOINT
ms.assetid: f77d528d-8b6d-4e2e-983c-3f3957983e9f
---


# Incluir un elemento de complemento en el complemento hospedado por el proveedor
Aprenda a exponer un formulario web remoto en una página de SharePoint en un Complemento de SharePoint hospedado por el proveedor.
Este es el sexto de una serie de artículos sobre los conceptos básicos de desarrollo de Complementos de SharePoint hospedados por el proveedor. Primero debe familiarizarse con los  [Complementos de SharePoint](sharepoint-add-ins.md) y con los anteriores artículos de esta serie:





-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Dar al complemento hospedado por el proveedor la apariencia de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [Incluir un botón personalizado en el complemento hospedado por el proveedor](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [Obtener una introducción rápida al modelo de objetos de SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [Agregar operaciones de escritura de SharePoint al complemento hospedado por el proveedor](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)



> **NOTA**
> Si ha estado trabajando en esta serie sobre complementos hospedados por el proveedor, ya tiene una solución de Visual Studio que puede usar para continuar con este tema. También puede descargar el repositorio en  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) y abrir el archivo BeforeAdd-inPart.sln.




En este artículo, se agrega un tipo especial de elemento web, denominado elemento de complemento, al Complemento de SharePoint. El elemento de complemento expone el formulario de pedido del complemento en una página de SharePoint.
## Crear el elemento de complemento






> **NOTA**
>  La configuración de los Proyectos de inicio en Visual Studio tiende a volver a los valores predeterminados cuando se vuelve a abrir la solución. Siempre siga estos pasos inmediatamente después de volver a abrir la solución de ejemplo en esta serie de artículos:>  Haga clic con el botón derecho en el nodo de solución en la parte superior del **Explorador de soluciones** y seleccione **Establecer proyectos de inicio**. >  Asegúrese de que los tres proyectos estén establecidos en **Iniciar** en la columna **Acción**. 





1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **ChainStore** y seleccione **Agregar | Nuevo elemento**.


2. Seleccione **Elemento web cliente (web host)**, asígnele el nombre Realizar pedido y, a continuación, presione **Agregar**. ("Elemento web cliente" es sinónimo de "elemento de complemento").


3. En la siguiente página del asistente, seleccione el segundo botón de opción: **Seleccionar o especificar la dirección URL de una página web existente para el contenido del elemento web cliente**.


4. En la lista desplegable, seleccione la dirección URL de la página **OrderForm.aspx** y presione **Finalizar**.

    Se agrega al proyecto y se abre un archivo elements.xml que define el elemento de complemento.


5. En el elemento **ClientWebPart**, cambie los siguientes atributos a estos valores:


|**Atributo**|**Valor**|
|:-----|:-----|
|Título  <br/> |Realizar pedido  <br/> |
|Descripción  <br/> |Formulario para realizar un pedido  <br/> |
|DefaultHeight  <br/> |320  <br/> |
 

    Deje el resto de los atributos con sus valores predeterminados y guarde el archivo.



## Ejecutar el complemento y probar el elemento de complemento






1. Use la tecla F5 para implementar y ejecutar el complemento. Visual Studio hospeda la aplicación web remota en IIS Express y hospeda la base de datos SQL en un SQL Express. También hace una instalación temporal del complemento en el sitio de SharePoint de prueba y ejecuta inmediatamente el complemento. Deberá conceder permisos al complemento antes de que se abra la página de inicio.


2. Cuando se abre la página de inicio del complemento, este está implementado y el elemento de complemento **Realizar pedido** está disponible para que los usuarios lo agreguen a cualquier área de elementos web en cualquier página de SharePoint en el sitio web de la tienda de Hong Kong. Siga estos pasos para agregarlo a la página principal.

1. Presione **Volver al sitio** en el control de cromo de la parte superior de la página de inicio para abrir la página principal de la tienda de Hong Kong.


2. En la cinta de opciones, abra la ficha **Página** y presione el botón **Editar**.


3. Una vez que la página esté en modo de edición, abra la ficha **Insertar** en la cinta de opciones y luego presione el botón **Elemento de complemento**. (Puede que aún se llame **Elemento de aplicación**).


4. En el control de inserción de elementos web que se abre, seleccione el elemento de complemento **Realizar pedido**. El control tendrá un aspecto similar al siguiente.

!\[Control de inserción de elementos web de SharePoint. El elemento denominado "Realizar pedido" está resaltado. Su nombre y descripción aparece en un cuadro a la derecha.](images/aae61f89-2e9e-4808-8b0c-2439dad7c701.PNG)





5. Haga clic en algún lugar de una de las zonas de elementos web del formulario. Esto se hace para establecer la ubicación donde irá el elemento de complemento. 


6. Haga clic en **Agregar** en el control de inserción de elementos web. El elemento de complemento **Realizar pedido** se agregará a la zona de elementos web.


7. En la cinta de opciones, presione **Guardar**.


3. El formulario de pedido aparece ahora en la página y tiene la apariencia del resto de la página. Ahora debería ser similar al siguiente: 

!\[El elemento del complemento Realizar un pedido en la página con cuadros de texto para Producto, Proveedor y Calidad. También hay un botón "Realizar un pedido".](images/beae2e3c-c1f4-4334-8ab8-0c42252cb2a2.PNG)





4. Escriba los valores para **Proveedor**, **Producto** y **Cantidad**, y presione **Realizar pedido**. Parecerá que no hay cambios, pero se realiza un pedido en la base de datos corporativa. Opcionalmente, puede vaciar los campos del elemento de complemento actualizando la página.


5. Use el botón Atrás del explorador hasta que vuelva a la página de inicio del complemento Chain Store. Presione **Mostrar pedidos**. Aparece el nuevo pedido.


6. Para terminar la sesión de depuración, cierre la ventana del explorador o detenga la depuración en Visual Studio. Cada vez que presione F5, Visual Studio retirará la versión anterior del complemento e instalará la más reciente.


7. Trabajará con este complemento y con la solución de Visual Studio en otros artículos, y se considera recomendable retirar el complemento una última vez cuando acabe de trabajar en él durante un tiempo. En el proyecto, haga clic con el botón derecho en el **Explorador de soluciones** y elija **Retirar**.



## 
<a name="Nextsteps"> </a>

 El complemento depende de dos listas que creó manualmente. No es recomendable que los usuarios tengan que hacer eso. En el siguiente artículo, se comienza con el proceso de creación automática de estas listas. El primer paso importante es crear controladores personalizados para el evento de instalar un complemento: [Controlar eventos de complemento en el complemento hospedado por el proveedor](handle-add-in-events-in-the-provider-hosted-add-in.md)




