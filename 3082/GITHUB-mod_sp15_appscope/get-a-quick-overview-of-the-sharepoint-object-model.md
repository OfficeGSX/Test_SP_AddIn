---
title: Obtener una introducción rápida al modelo de objetos de SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6b8b55f8-9370-43a0-af8d-e07d1028a075
---


# Obtener una introducción rápida al modelo de objetos de SharePoint
Vea una rápida introducción a algunas de las clases más importantes en el modelo de objetos de SharePoint.
Este es el cuarto de una serie de artículos sobre los conceptos básicos de desarrollo de Complementos de SharePoint hospedados por el proveedor. Primero debe familiarizarse con  [Complementos de SharePoint](sharepoint-add-ins.md) y con los anteriores artículos de esta serie:





-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Dar al complemento hospedado por el proveedor la apariencia de SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [Incluir un botón personalizado en el complemento hospedado por el proveedor](include-a-custom-button-in-the-provider-hosted-add-in.md)



> **NOTA**
> Si ha estado trabajando en esta serie sobre complementos hospedados por el proveedor, ya tiene una solución de Visual Studio que puede usar para continuar con este tema. También puede descargar el repositorio en  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) y abrir el archivo BeforeSharePointWriteOps.sln.




En el siguiente artículo, dejaremos brevemente la codificación para ver una introducción rápida al modelo de objetos de cliente (CSOM) de SharePoint. Este modelo es grande y está bien documentado en MSDN con temas de referencia, artículos de procedimientos y ejemplos de código. En este artículo, podemos ofrecer solo la punta de la punta del iceberg. Pero incluso una introducción muy breve hará que gran parte del código que se muestra en esta serie resulte mucho menos misteriosa. 
## Jerarquía de contenido

La tabla siguiente muestra la jerarquía del contenido en SharePoint y las clases CSOM que lo representan. Cada una de estas entidades tiene elementos secundarios del tipo que aparece debajo.




|**Entidad**|**Clase**|**Observaciones**|
|:-----|:-----|:-----|
|Granja de servidores de SharePoint local o suscripción a SharePoint Online (también denominada inquilino)  <br/> ||Hay solamente un acceso mediante programación limitado a este nivel de CSOM. No hay ninguna clase de granja de servidores, suscripción o inquilino, por ejemplo. (El modelo de objetos de servidor de SharePoint, que no se puede usar en los complementos, permite el acceso mediante programación a estas entidades).  <br/> |
|colección de sitios  <br/> |**Site** <br/> |Una colección de sitios web que están agrupados juntos principalmente por razones administrativas y para albergar componentes de SharePoint, como páginas maestras con su marca o grupos de seguridad personalizados, que se pueden aplicar a todos los sitios web secundarios. Todos los sitios web pertenecen a alguna colección de sitios.  <br/> |
|sitio web  <br/> |**Web** <br/> |Un conjunto de páginas y componentes de SharePoint. Pueden tener sitios web dependientes.  <br/> |
|lista  <br/> |**List** <br/> |Las bibliotecas de documentos y otros tipos de bibliotecas de archivos también están en este nivel.  <br/> Una biblioteca de documentos es un tipo especial de lista en la que cada fila incluye un documento adjunto y las demás columnas son datos sobre el documento, como el autor, cuándo se editó por última vez y quién lo desprotegió.  <br/> |
|elemento de lista  <br/> |**ListItem** <br/> |Una fila de una lista, es decir, un elemento de lista, con determinados valores en los campos de la fila. También tiene un tipo. Consulte la siguiente fila.  <br/> |
|elemento de lista  <br/> |**Content Type** <br/> |El tipo de un elemento de lista. Están representados en la clase **ContentType**. Cada uno de ellos es básicamente un conjunto de columnas y metadatos. El más simple es el tipo de contenido **Item** integrado. Todos los demás tipos de contenido se derivan de **Item**. SharePoint incluye muchos tipos de contenido integrados, como Evento y Anuncio.  <br/> |
|columna  <br/> |**Field** <br/> |Un objeto **Field** incluye no solo información sobre el tipo de datos subyacente, sino también información sobre cómo se aplica formato a los datos y se representan en formularios, como los formularios para crear, mostrar y editar elementos de lista específicos. <br/> |
 




Mediante programación, puede crear listas personalizadas, tipos de contenido, tipos de columna y elementos de lista. 



Además del contenido, el CSOM proporciona acceso a los usuarios, grupos, roles y permisos, la taxonomía, la búsqueda y más.




## Tiempo de ejecución del cliente y procesamiento por lotes
<a name="CSOMBatching"> </a>

CSOM usa un sistema de procesamiento por lotes. Los fragmentos de código administrado se convierten en XML y se envían al servidor en una sola solicitud HTTP. Para cada comando, se realiza una llamada de modelo de objetos de servidor correspondiente y, a continuación, el servidor devuelve una respuesta al cliente en el formato de notación de objetos JavaScript (JSON). 



El código de SharePoint en un cliente comienza por recuperar un objeto de contexto de cliente que representa el contexto de la solicitud actual, incluida la identidad del sitio web de SharePoint (y su colección de sitios principal) y mediante este contexto se puede puede obtener acceso a objetos CSOM. Lo siguiente es la estructura básica que verá una y otra vez. Tenga en cuenta lo siguiente sobre este código:




- El objeto  `spContext` es del tipo **SharePointContext** y se define en el archivo SharePointContext.cs (o .vb) que el Office Developer Tools para Visual Studio genera. Este archivo se puede modificar, pero normalmente no es necesario. Para la mayoría de los proyectos de Complemento de SharePoint, este archivo y el archivo TokenHelper.cs (o .vb) que también se genera mediante las herramientas funcionan eficazmente como extensiones del propio CSOM.


- El objeto  `clientContext` es el **ClientContext** del tipo CSOM.


- El método **ExecuteQuery** agrupa el código de operación CRUD en un mensaje XML que envía al servidor de SharePoint. Una vez allí, se traduce al modelo de objetos de servidor equivalente para su posterior ejecución.




```cs

using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    // CRUD operation or query code goes here.

    clientContext.ExecuteQuery();
}```

Había un ejemplo de este patrón en el artículo anterior de esta serie, en el método  `GetLocalEmployeeName` que se muestra a continuación. Tenga en cuenta lo siguiente sobre este método:




- Las dos primeras líneas del bloque **using** parecen obtener referencias a la lista y al objeto de elemento de lista. Pero en realidad, cuando estas líneas se ejecutan en el tiempo de ejecución de cliente de SharePoint, simplemente se traducen al formato XML. El método **ExecuteQuery** envía ese XML al servidor.


-  El método **Load** agrega algo extra al mensaje: indica al servidor que envíe el objeto especificado al cliente. El método **ExecuteQuery** recibe este objeto (como JSON) y lo usa para inicializar la variable `selectedLocalEmployee` del cliente. El código de cliente posterior luego hace referencia a ese objeto **ListItem** y sus miembros. Como puede ver, la siguiente línea hace referencia al valor del campo "Título" del elemento. Esta línea hubiese producido una excepción si no se hubiera llamado al método **Load** porque el objeto no existe realmente en el cliente hasta que se carga.




```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}```


## 
<a name="Nextsteps"> </a>

 En el siguiente artículo, regresamos a la codificación y aprendemos a escribir datos en SharePoint: [Agregar operaciones de escritura de SharePoint al complemento hospedado por el proveedor](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)




