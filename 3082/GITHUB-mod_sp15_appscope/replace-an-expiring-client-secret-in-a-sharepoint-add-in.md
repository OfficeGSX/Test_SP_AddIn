---
title: Reemplazar un secreto de cliente a punto de expirar en un complemento para SharePoint
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# Reemplazar un secreto de cliente a punto de expirar en un complemento para SharePoint
Obtenga más información sobre cómo agregar un nuevo secreto de cliente para un elemento Complemento de SharePoint registrado con AppRegNew.aspx.
Los secretos de cliente de Complementos de SharePoint registrados con la página de AppRegNew.aspx expiran en un año. En este artículo se explica cómo agregar un nuevo secreto de cliente para un complemento, así como el proceso para agregar un nuevo secreto de cliente que sea válido durante tres años.
  
    
    


> **NOTA**
> Este artículo hace referencia a las Complementos de SharePoint que se distribuyen a través del catálogo de una organización y se registran en la página AppRegNew.aspx. Si el complemento registra en el Panel de vendedores, vea  [Crear o actualizar identificadores y claves secretas de cliente en el Panel de vendedores](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update). 
  
    
    


## Requisitos previos para actualizar un secreto de cliente

Antes de empezar, compruebe lo siguiente:
  
    
    

- Que el  [Ayudante para el inicio de sesión de Microsoft Online Services](http://www.microsoft.com/es-es/download/details.aspx?id=39267) esté instalado en el equipo de desarrollo.
    
  
- Que el módulo de PowerShell de Microsoft Online Services ( [32 bits](http://go.microsoft.com/fwlink/p/?linkid=236298);  [64 bits](http://go.microsoft.com/fwlink/p/?linkid=236297)) esté instalado en el equipo de desarrollo.
    
  
- Que es administrador de inquilinos del inquilino de Office 365 (o administrador de la granja de servidores) donde se registró el complemento con la página AppRegNew.aspx.
    
  

## Buscar las fechas de expiración del Complementos de SharePoint instalado en el arrendamiento de Office 365


  
    
    

1. Abra Windows PowerShell y ejecute el siguiente cmdlet:
    
  ```
  
Connect-MsolService

  ```

2. En el símbolo de inicio de sesión, especifique las credenciales del administrador de inquilinos (o de la granja de servidores) del inquilino o la granja de Office 365 donde se registró el complemento con AppRegNew.aspx.
    
  
3. Cree un informe donde figure cada complemento y la fecha en que expira su secreto con las siguientes líneas. Cosas que debe saber sobre este código:
    
  - Primero filtra las propias aplicaciones de Microsoft y los complementos que aún se están desarrollando (y un tipo de complemento en desuso que se llamaba "complemento autohospedado"). .
    
  
  - De lo que queda, filtra los complementos que no son de SharePoint y los que usan claves asimétricas, como flujos de trabajo.
    
  

  ```
  
$applist = Get-MsolServicePrincipal -all  |Where-Object -FilterScript { ($_.DisplayName -notlike "*Microsoft*") -and ($_.DisplayName -notlike "autohost*") -and  ($_.ServicePrincipalNames -notlike "*localhost*") }

foreach ($appentry in $applist)
{
    $principalId = $appentry.AppPrincipalId
    $principalName = $appentry.DisplayName
    
    Get-MsolServicePrincipalCredential -AppPrincipalId $principalId -ReturnKeyValues $false | Where-Object { ($_.Type -ne "Other") -and ($_.Type -ne "Asymmetric") }
    
     $date = get-date
     Write-Host "$principalName;$principalId;$appentry.KeyId;$appentry.type;$date;$appentry.Usage"

}  > c:\\temp\\appsec.txt
  ```

4. Abra el archivo C:\\temp\\appsec.txt para ver el informe. Deje abierta la ventana de Windows PowerShell para el siguiente procedimiento, si alguno de los secretos está a punto de expirar.
    
  

## Generar un nuevo secreto


  
    
    

1. Cree una variable de identificador de cliente con la siguiente línea usando el identificador de cliente de la Complemento de SharePoint como parámetro.
    
  ```
  
$clientId = 'client id of the add-in'

  ```

2. Cree un nuevo secreto de cliente con las siguientes líneas:
    
  ```
  
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Sign -Value $newClientSecret
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Verify -Value $newClientSecret
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Password -Usage Verify -Value $newClientSecret
$newClientSecret
  ```

3. El nuevo secreto de cliente aparecerá en la consola de Windows PowerShell. Cópielo en un archivo de texto. Úselo en el siguiente procedimiento.
    
  

> **SUGERENCIA**
> De manera predeterminada, el secreto del complemento dura un año. Puede modificarlo para que dure más o menos tiempo (un máximo de tres años), mediante el parámetro **-EndDate** en las tres llamadas del cmdlet **New-MsolServicePrincipalCredential**. El valor del parámetro debe ser un objeto  [DateTime](http://msdn2.microsoft.com/ES-ES/library/03ybds8y) establecido en un máximo de tres años a partir de **DateTime.Now**. 
  
    
    


## Actualización de la aplicación web remota de Visual Studio para usar el nuevo secreto


> **IMPORTANTE**
> Si el complemento se creó originalmente con una versión preliminar de Microsoft Office Developer Tools para Visual Studio, puede contener una versión desactualizada del archivo TokenHelper.cs (o .vb). Si el archivo no contiene la cadena "secondaryClientSecret", está desactualizado y se debe reemplazar para poder actualizar la aplicación web con un nuevo secreto. Para obtener una copia de una versión de lanzamiento del archivo, necesita Visual Studio 2012 o una versión posterior. Cree un nuevo proyecto de Complemento de SharePoint en Visual Studio y copie el archivo TokenHelper en el proyecto de aplicación web de su Complemento de SharePoint. 
  
    
    


1. Abra el proyecto de Complemento de SharePoint en Visual Studio y el archivo web.config del proyecto de aplicación web. En la sección **appSettings** están las claves para el identificador y el secreto de cliente. Aquí tiene un ejemplo:
    
  ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

  ```

2. Cambie el nombre de la clave **ClientSecret** por "SecondaryClientSecret", como se muestra en el siguiente ejemplo:
    
  ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
  ```

3. Agregue una clave **ClientSecret** nueva y asígnele el nuevo secreto de cliente. Su marcado ahora debería tener este aspecto:
    
  ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
  ```

4. Si cambió a un nuevo archivo TokenHelper, vuelva a crear el proyecto.
    
  
5. Vuelva a publicar la aplicación web.
    
  

## Crear un secreto de cliente válido durante tres años

En el caso de los secretos de cliente expirados, primero debe eliminar todos los secretos expirados de un **clientId** concreto. A continuación, cree uno nuevo con el PowerShell de MSO, espere al menos 24 horas y, luego, pruebe la aplicación con el nuevo **clientId** y la clave **ClientSecret**.
  
    
    

1. Conéctese a MSOnline con el usuario administrador del inquilino y la revisión siguiente mediante SharePoint 2013 Windows PowerShell.
    
  ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

  ```

2. Obtenga **ServicePrincipals** y las claves. Al imprimir **$keys**, se devuelven tres registros. Reemplace cada **KeyId** de *KeyId1*  , *KeyId2*  y *KeyId3*  . También verá el elemento **EndDate** de cada clave. Confirme que la clave expirada figure aquí.
    
    **Nota:** El elemento **clientId** debe coincidir con el **clientId** expirado. Se recomienda eliminar todas las claves de este **clientId**, tanto las expiradas como las no expiradas.
    


  ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

  ```

3. Genere un nuevo **ClientSecret** para este **clientID**. Usa el mismo **clientId** que se ha establecido en el paso anterior. El nuevo **ClientSecret** es válido durante tres años.
    
  ```
  
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$dtStart = [System.DateTime]::Now
$dtEnd = $dtStart.AddYears(3)
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Sign -Value $newClientSecret -StartDate $dtStart  -EndDate $dtEnd
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Symmetric -Usage Verify -Value $newClientSecret   -StartDate $dtStart  -EndDate $dtEnd
New-MsolServicePrincipalCredential -AppPrincipalId $clientId -Type Password -Usage Verify -Value $newClientSecret   -StartDate $dtStart  -EndDate $dtEnd
$newClientSecret

  ```

4. Copie la salida de **$newClientSecret**.
    
  
5. Reemplace el elemento **Web.config** por estos **ClientId** y **ClientSecret**. La configuración de la aplicación de **SecondaryClientSecret** no es necesaria.
    
  
6. Espere al menos 24 horas para propagar **ClientSecret** en SharePoint Office (SPO).
    
  

## Vea también


#### Otros recursos


  
    
    
 [Error de la aplicación del proveedor alojada en SPO (Provider Hosted App fails on SPO)](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
