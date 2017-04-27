---
title: Tipos de directivas de autorización de complementos en SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# Tipos de directivas de autorización de complementos en SharePoint 2013
Conozca las distintas directivas de autorización para los complementos de SharePoint: directiva de solo complemento, directiva de usuario y complemento, y directiva de solo usuario. También proporciona instrucciones para usar la directiva de solo complemento.
Antes de leer este artículo, primero debe familiarizarse con los artículos  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) y [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md).





## Obtener información general sobre los tipos de directivas de autorización de complementos
<a name="Overview"> </a>

SharePoint proporciona tres tipos de directivas de autorización:




- **Directiva de solo usuario**: cuando se usa la directiva de solo usuario, SharePoint solo comprueba los permisos del usuario. SharePoint usa su directiva cuando el usuario accede a los recursos directamente sin usar un complemento, como cuando un usuario abre por primera vez la página de inicio de SharePoint 2010 o accede a las API de SharePoint desde PowerShell.




- **Directiva de usuario y complemento**: cuando se usa la directiva de usuario y complemento, SharePoint comprueba los permisos de la entidad de seguridad del complemento y el usuario. La comprobación de autorización solo es correcta si ambos tienen suficientes permisos para realizar la acción en cuestión.

    Por ejemplo, esta directiva se usa cuando un Complemento de SharePoint quiere acceder a los recursos del usuario en SharePoint. (El código de los componentes remotos de Complemento de SharePoint debe estar diseñado para realizar llamadas de usuario y complemento a SharePoint.)




- **Directiva de solo complemento**: cuando se usa la directiva de solo complemento, SharePoint comprueba los permisos de la entidad de seguridad del complemento. Las comprobaciones de autorización solo son correctas si el complemento actual tiene permisos para realizar la acción en cuestión, independientemente de los permisos del usuario actual (si los tuviera) .

    Un complemento de aprobación de gastos es un ejemplo de un complemento que puede estar diseñado para usar esta directiva. El complemento permite a usuarios, que de otro modo no podrían aprobar gastos, aprobar los gastos por debajo de una cantidad determinada. Consulte el siguiente escenario para obtener detalles. 



    > **NOTA**
    > Ciertas API necesitan un contexto de usuario y no pueden ejecutarse con una directiva de solo complemento. Entre ellas, se incluyen muchas API para interactuar con Project Server 2013 y ejecutar consultas de búsqueda. 

### Consultar un escenario de ejemplo de un complemento que usa la directiva de solo complemento
<a name="Scenario"> </a>

Supongamos que un director de ventas de Contoso, Adam, compra un complemento de envío de gastos que usa la directiva de solo complemento. Cuando Adam decide comprar el complemento, se le solicita que permita que el complemento eleve los permisos de usuario, es decir, que permita que el complemento realice llamadas de solo complemento a SharePoint. Adam concede al complemento los permisos solicitados. Luego, compra licencias suficientes del complemento para todos los representantes de ventas de Contoso e instala el complemento en el sitio web de SharePoint central del equipo de ventas.



En poco tiempo, los representantes de ventas envían informes de gastos con el nuevo complemento de envío de gastos. Normalmente, los representantes no pueden aprobar sus propios informes de gastos, pero pueden hacerlo con el complemento porque Adam le concedió la habilidad de hacerlo para envíos de gastos inferiores a 50 $, ya que configuró el complemento para que aprobara automáticamente los informes inferiores a 50 $. El complemento le asigna automáticamente la aprobación de informes de 50 $ o más. Esto se podría implementar otorgando el Complemento de SharePointpermiso de escrituraSharePoint a una lista de gastos aprobados. Pero, entre los usuarios, solo los gestores de recursos humanos tienen permiso de escritura en la lista. El código del complemento está diseñado para agregar el gasto a la lista realizando una llamada de solo complemento a SharePoint, cuando el gasto es inferior a 50 $. Ya que los permisos de usuario no se comprueban, cualquier envío de los usuarios inferior a 50 $ se agrega automáticamente a la lista de gastos aprobados, incluso si el usuario no tiene permisos de escritura en la lista.








### Aprender cómo los complementos obtienen los permisos para usar la directiva de solo complemento
<a name="Approve"> </a>

Para poder realizar llamadas de solo complemento a SharePoint, el complemento debe solicitar el permiso de usar la directiva de solo complemento en el manifiesto del complemento. Para ello, agregue el atributo **AllowAppOnlyPolicy** al elemento **AppPermissionRequests** y configúrelo en **true**, como se muestra en el siguiente marcado:



```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>```


> **NOTA**
> Complementos de SharePoint, se usa para llamar "aplicaciones de SharePoint". Para mantener la compatibilidad con versiones anteriores, el esquema de manifiesto de aplicación no se ha modificado, por lo que aparece la cadena "app" en los nombre de elementos y atributos de mayo. 




Se le solicitará a un usuario que instale el complemento que apruebe esta solicitud. Si el complemento solicita permisos de ámbito de inquilinos, solo un administrador de inquilinos podrá conceder el uso de la directiva de solo complemento, de modo que solo un administrador de inquilinos puede instalar el complemento. Si el complemento no solicita ningún permiso de ámbito superior a la colección de sitios, un administrador de colecciones puede instalar el complemento. Para obtener más información sobre los ámbitos de permisos, consulte  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).




### Aprender cómo los complementos realizan llamadas de solo complemento
<a name="AppOnlyCalls"> </a>

La diferencia entre una llamada de solo complemento a SharePoint y una llamada de usuario y complemento es el tipo de token de acceso que se incluye en la llamada. El siguiente código muestra cómo obtener tokens de acceso de solo complemento, y de usuario y complemento en el código administrado. La codificación detallada se realiza en el archivo TokenHelper.cs (o .vb) que Office Developer Tools para Visual Studio agrega automáticamente al proyecto de de Visual Studio.



```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+add-in access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...

      //Get add-in-only access token.
       string addinOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}```


> **NOTA**
> Los complementos que no realizan llamadas autenticadas de OAuth (por ejemplo, los complementos que solo tienen JavaScript ejecutándose en la web de complemento) no pueden usar la directiva de solo complemento. Pueden solicitar el permiso, pero no podrán beneficiarse de él porque para hacerlo se necesita que se pase un token de OAuth de solo complemento. Solo los complementos con aplicaciones web que se ejecuten fuera de SharePoint pueden crear y pasar tokens de solo complemento. 




En general, se necesita un usuario actual para realizar una llamada. En el caso de la directiva de solo complemento, SharePoint crea un SHAREPOINT\\APP, parecido al usuario SHAREPOINT\\SYSTEM existente. SHAREPOINT\\APP realiza todas las solicitudes de solo complemento. No existe un modo para autenticarse como SHAREPOINT\\APP mediante la autenticación basada en usuarios.




### Obtener instrucciones para usar la directiva de solo complemento
<a name="GuidelinesFor"> </a>

Dado que las llamadas de solo complemento elevan de forma eficaz los privilegios de usuario, debe tener cuidado al crear complementos que soliciten permisos para realizarlas. Las llamadas solo deben usar la directiva de solo complemento si:




- El complemento necesita elevar sus permisos por encima del usuario para una llamada específica, por ejemplo, para aprobar un informe de gastos bajo condiciones evaluadas por el complemento.


- El complemento no actúa en nombre de ningún usuario, por ejemplo, un complemento que realiza tareas de mantenimiento nocturnas en una biblioteca de documentos de SharePoint.



## Recursos adicionales
<a name="AR"> </a>


-  [Autorización y autenticación de complementos de SharePoint](authorization-and-authentication-of-sharepoint-add-ins.md)


-  [Permisos de complemento en SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)


-  [Flujo de tokens de contexto de OAuth para complementos para SharePoint](context-token-oauth-flow-for-sharepoint-add-ins.md)


-  [Complementos de SharePoint](sharepoint-add-ins.md)


-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)



