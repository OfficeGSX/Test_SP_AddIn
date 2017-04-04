---
title: Scripts de configuración de elevada confianza para SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# Scripts de configuración de elevada confianza para SharePoint 2013
Obtenga scripts de Windows PowerShell personalizables que configuren una granja de servidores de Microsoft SharePoint 2013 para usar una Complemento de SharePoint de elevada confianza.
## Usar los scripts
<a name="Usage"> </a>

Los scripts siguientes se usan para designar uno o varios certificados digitales X.509 como emisores de confianza de tokens de acceso en una granja de servidores de ensayo o producción de Microsoft SharePoint 2013. (Si quiere un script que sea más apropiado para un entorno de desarrollo de Complementos de SharePoint, vea  [Crear complementos de SharePoint de elevada confianza](create-high-trust-sharepoint-add-ins.md)). Ningún conjunto de scripts puede funcionar en todas las granjas de servidores de SharePoint porque los certificados se pueden comprar y almacenar de muchas formas distintas. Por ello, hay que tener en cuenta los factores siguientes:
  
    
    

- Los scripts están diseñados para ejecutarse en Shell de administración de SharePoint en cualquier servidor de SharePoint dentro de la granja de servidores.
    
  
- Estos scripts deben considerarse borradores que se pueden personalizar.
    
  
- Se usan en el proceso global de publicación de un Complemento de SharePoint de elevada confianza. Solo deben usarlos personas que conozcan los temas  [Creación de complementos para SharePoint que usan el sistema de autorización de elevada confianza](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) y [Empaquetar y publicar complementos de gran confianza para SharePoint](package-and-publish-high-trust-sharepoint-add-ins.md) y los requisitos previos correspondientes.
    
  
- También debe revisarlas y personalizarlas, si es necesario, alguien que conozca las directivas de certificados del cliente del complemento.
    
  
- Los dos scripts principales que aparecen a continuación, **HighTrustConfig-ForSharedCertificate.ps1** y **HighTrustConfig-ForSingleApp.ps1**, suponen que un administrador ha comprado certificados para el componente de la aplicación web remota del complemento o los complementos y ha almacenado temporalmente una versión .cer del certificado (que no contiene la clave privada) en una carpeta donde las cuentas de usuario para los siguientes grupos de aplicaciones IIS de los servidores de SharePoint tienen acceso de lectura:
    
  - **SecurityTokenServiceApplicationPool**
    
  
  - El grupo de complementos que ofrece el sitio web de IIS en el que se hospeda la aplicación web primaria de SharePoint para su sitio web de prueba de SharePoint. En el caso del sitio web de IIS de **SharePoint - 80**, el grupo se llama **OServerPortalAppPool**.
    
  

    Para buscar la cuenta de usuario que un grupo de aplicaciones usa, abra el Administrador de IIS en un servidor de SharePoint y haga doble clic en **Grupos de aplicaciones** en el panel **Conexiones**. La columna **Identidad** de la lista **Grupos de aplicaciones** que se abre muestra a los usuarios los grupos de aplicaciones.
    
  
- Las instrucciones de los dos scripts principales también suponen que los certificados se han instalado en IIS en los servidores que hospedan las aplicaciones web remotas. Para las instrucciones, consulte  [Configurar el servidor web remoto con el certificado](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote).
    
  
Vea las notas de uso específico de cada script en las secciones siguientes.
  
    
    

## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

El certificado del componente de la aplicación web remota de la Complemento de SharePoint de elevada confianza se obtiene de una entidad de certificación (CA) comercial o de una CA de dominio. En ambos casos, el certificado es el vínculo inferior de una cadena de certificados, cada uno de los cuales confía en el que tiene por encima, lo que se origina con una entidad de certificación raíz (comercial o de dominio). SharePoint 2013 necesita que  *todos*  los certificados de la cadena se agreguen a la lista de entidades de certificación raíz de SharePoint. El script siguiente se puede usar para agregar a la lista cada certificado de la cadena *excepto el que está más abajo*  . El certificado inferior de la cadena, el que está directamente enlazado a la aplicación web remota, se agrega a las entidades de certificación raíz en uno de los scripts principales descritos en secciones posteriores.
  
    
    
Los parámetros del script son los siguientes:
  
    
    


|**Parámetro**|**Valor**|
|:-----|:-----|
|-CertPath  <br/> |La ruta de acceso completa y el nombre de archivo del certificado (el archivo .cer).  <br/> |
|-CertName  <br/> |El nombre del certificado. Para consultar el nombre, vea las propiedades del certificado.  <br/> |
   
Por ejemplo, en el escenario común donde el certificado de la aplicación web lo emite un servidor de CA intermedio (también denominado "empresarial") y su certificado, a su vez, lo emite un servidor de CA raíz (también denominado "independiente"), el script se debe ejecutar una vez para cada uno de los certificados de los dos servidores de CA. A continuación ilustramos esto:
  
    
    



```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"

```

Si todos los certificados de las Complementos de SharePoint de elevada confianza del cliente provienen de la misma cadena de certificados, como ocurriría normalmente, este script no se volverá a usar.
  
    
    
El siguientes es el código del script. Solo tiene que pegarlo en un editor de texto o en el editor de PowerShell (IPowerShell ISE) y guardarlo como AddSPRootAuthority.ps1.
  
    
    

> **NOTA**
> El archivo se debe guardar en formato ANSI, no en UTF-8. PowerShell podría generar errores de sintaxis si ejecuta un archivo que no tiene un formato ANSI. El bloc de notas y el editor de PowerShell lo guardarán de forma predeterminada como ANSI. Si usa otro editor para guardar el archivo, asegúrese de guardarlo como ANSI. 
  
    
    




```

param(
    [Parameter(Mandatory)][String] $CertName = $(throw "Usage: AddSPRootAuthority.ps1 -CertPath <full path to .cer file> -CertName <name of certificate>"),
    [Parameter(Mandatory)][String] $CertPath
)
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Make the certificate a trusted root authority in SharePoint
$cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)
New-SPTrustedRootAuthority -Name $CertName -Certificate $cert

```


## HighTrustConfig-ForSharedCertificate.ps1
<a name="MultipleAppScript"> </a>

La tarea principal de este script consiste en registrar el certificado compartido de los componentes de la aplicación web remota en varios Complementos de SharePoint de elevada confianza con SharePoint como entidad de certificación raíz y como emisor de tokens de acceso de confianza. Este script no se usa si el cliente utiliza certificados independientes con cada Complemento de SharePoint de elevada confianza. Para ese escenario, vea  [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript) a continuación. Si todos los complementos usan el mismo certificado, este script se ejecuta solo una vez. Si algunos conjuntos de complementos usan un certificado diferente del de otros conjuntos, el script se ejecuta una vez para cada conjunto.
  
    
    
En la tabla siguiente se describen los parámetros y los modificadores del script. La personalización del script puede introducir cambios en esta tabla.
  
    
    


|**Parámetro**|**Valor**|
|:-----|:-----|
|-CertPath (obligatorio)  <br/> |La ruta de acceso completa al archivo *.cer compartido.  <br/> |
|-CertName (obligatorio)  <br/> |El nombre del certificado compartido. Para buscar el nombre, abra IIS en el servidor web remoto que hospeda la aplicación web remota. Resalte el nodo  _nombre de servidor_ y haga doble clic en **Certificados**. El certificado aparecerá en la lista por su nombre.  <br/> |
|-TokenIssuerFriendlyName (opcional)  <br/> |Un nombre descriptivo único para el emisor de tokens de hasta 50 caracteres. Es útil si quiere depurar problemas de los emisores de tokens. El nombre debe ser único. La inclusión de un GUID garantizaría que lo sea, pero un GUID usa hasta 32 de los 50 caracteres, así que considere la posibilidad de convertir un GUID en una cadena de base 64 que se pueda reducir a 22 caracteres. Si no se usa este parámetro, el script usa el nombre "High-Trust Add-ins  _<versión base64 del GUID del emisor>_".  <br/> |
   
El siguiente es un ejemplo de la ejecución del script.
  
    
    
 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`
  
    
    

> **IMPORTANTE**
> El registro del certificado como emisor de tokens no surte efecto inmediatamente. Pueden pasar 24 horas antes de que todos los servidores de SharePoint reconozcan al nuevo emisor de tokens. Ejecute un iisreset en todos los servidores de SharePoint para que reconozcan inmediatamente al emisor (si puede hacerlo sin interrumpir a los usuarios de SharePoint). 
  
    
    

Inicie un nuevo archivo en un editor de texto o en el editor de PowerShell y copie lo siguiente en un archivo de texto y guárdelo como HighTrustConfig-ForSharedCertificate.ps1. Use el formato ANSI, no UTF-8.
  
    
    



```

param(
    [Parameter(Mandatory)][String] $CertPath = $(throw "Usage: HighTrustConfig-ForSharedCertificate.ps1 -CertPath <full path to .cer file> -CertName <name of certificate> [-TokenIssuerFriendlyName <friendly name>]"),
    [Parameter(Mandatory)][String] $CertName,
    [Parameter()][String] $TokenIssuerFriendlyName
) 
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Ensure friendly name is short enough
if ($TokenIssuerFriendlyName.Length -gt 50)
{
    throw "-TokenIssuerFriendlyName must be unique name of no more than 50 characters."
} 

# Get the certificate
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)

# Make the certificate a trusted root authority in SharePoint
New-SPTrustedRootAuthority -Name $CertName -Certificate $certificate 

# Get the GUID of the authentication realm
$realm = Get-SPAuthenticationRealm

# Generate a unique specific issuer ID
$specificIssuerId = [System.Guid]::NewGuid().ToString()

# Create full issuer ID in the required format
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

# Create issuer name
if ($TokenIssuerFriendlyName.Length -ne 0)
{
    $tokenIssuerName = $TokenIssuerFriendlyName
}
else
{
    $tokenIssuerName = "High-Trust Add-ins " + [System.Convert]::ToBase64String($specificIssuerId.ToByteArray()).TrimEnd('=') 
}

# Register the token issuer
New-SPTrustedSecurityTokenIssuer -Name $tokenIssuerName -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier -IsTrustBroker

# Output the specific issuer ID to a file in the same folder as this script. The file should be given to the developer of the high-trust SharePoint Add-in.
$specificIssuerId | select * | Out-File -FilePath "SecureTokenIssuerID.txt"
```


> **SUGERENCIA**
> Si el script produce un error después de haberse ejecutado correctamente la línea  `New-SPTrustedRootAuthority`, no se puede volver a ejecutar el script hasta que se quite ese objeto. Use el siguiente cmdlet, donde el valor del parámetro  `-Identity` es el mismo que se usó para el parámetro `-CertName` en el script.>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`> Si hay que eliminar el emisor de tokens por algún motivo, use el siguiente cmdlet: >  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`> Si se usó el parámetro  `-TokenIssuerFriendlyName`, use el mismo valor para  `-Identity`. Si no se usó el parámetro  `-TokenIssuerFriendlyName`, en el nombre del emisor aparecerá un GUID al azar. Para conseguir el valor necesario para  `-Identity`, ejecute el siguiente cmdlet. Luego abra el archivo TokenIssuers.txt que se genera y busque el emisor cuyo nombre sea "High-Trust Add-ins  _<versión base64 del GUID del emisor>_". Use el nombre completo para  `-Identity`. >  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`
  
    
    


## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

La tarea principal de este script consiste en registrar el certificado de la aplicación web remota en una Complemento de SharePoint de elevada confianza con SharePoint como entidad de certificación raíz y como emisor de tokens de acceso de confianza. Este script no se usa si el cliente utiliza un solo certificado para varias Complementos de SharePoint. Para ese escenario, vea  [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript) más arriba. Este script se ejecuta para cada Complemento de SharePoint de elevada confianza.
  
    
    
En la tabla siguiente se describen los parámetros y los modificadores del script. La personalización del script puede introducir cambios en esta tabla.
  
    
    


|**Parámetro**|**Valor**|
|:-----|:-----|
|-CertPath (obligatorio)  <br/> |La ruta de acceso completa al archivo *.cer compartido.  <br/> |
|-CertName (obligatorio)  <br/> |El nombre del certificado compartido. Para buscar el nombre, abra IIS en el servidor web remoto que hospeda la aplicación web remota. Resalte el nodo  _nombre de servidor_ y haga doble clic en **Certificados**. El certificado aparecerá en la lista por su nombre.  <br/> |
|-SPAppClientID (obligatorio)  <br/> |El id. de cliente (GUID) que se usó cuando la Complemento de SharePoint se registró en appregnew.aspx. Se usa como id. de emisor del emisor de tokens. El script garantiza que esté en minúscula, que es un requisito para los id. de emisor.  <br/> |
|-TokenIssuerFriendlyName (opcional)  <br/> |Un nombre descriptivo único para el emisor de tokens de hasta 50 caracteres. Es útil si quiere depurar problemas de los emisores de tokens. El nombre debe ser único. Puede usar el nombre de la Complemento de SharePoint. Si no se usa este parámetro, el script usa el valor de  `-SPAppClientID` como nombre. <br/> |
   
El siguiente es un ejemplo de una llamada al script:
  
    
    
 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`
  
    
    

> **IMPORTANTE**
> El registro del certificado como emisor de tokens no surte efecto inmediatamente. Pueden pasar 24 horas antes de que todos los servidores de SharePoint reconozcan al nuevo emisor de tokens. Ejecute un iisreset en todos los servidores de SharePoint para que reconozcan inmediatamente al emisor (si puede hacerlo sin interrumpir a los usuarios de SharePoint). 
  
    
    

Inicie un nuevo archivo en un editor de texto o en el editor de PowerShell y copie lo siguiente en un archivo de texto y guárdelo como HighTrustConfig-ForSingleApp.ps1. Use el formato ANSI, no UTF-8.
  
    
    



```

param(
    [Parameter(Mandatory)][String] $CertPath = $(throw "Usage: HighTrustConfig-ForSingleApp.ps1 -CertPath <full path to .cer file> -CertName <name of certificate> [-SPAppClientID <client ID of SharePoint add-in>] [-TokenIssuerFriendlyName <friendly name>]"),
    [Parameter(Mandatory)][String] $CertName,
    [Parameter(Mandatory)][String] $SPAppClientID,
    [Parameter()][String] $TokenIssuerFriendlyName
) 
# Stop if there's an error
$ErrorActionPreference = "Stop"

# Ensure friendly name is short enough
if ($TokenIssuerFriendlyName.Length -gt 50)
{
    throw "-TokenIssuerFriendlyName must be unique name of no more than 50 characters."
} 

# Get the certificate
$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath)

# Make the certificate a trusted root authority in SharePoint
New-SPTrustedRootAuthority -Name $CertName -Certificate $certificate 

# Get the GUID of the authentication realm
$realm = Get-SPAuthenticationRealm

# Must use the client ID as the specific issuer ID. Must be lower-case!
$specificIssuerId = New-Object System.String($SPAppClientID).ToLower()

# Create full issuer ID in the required format
$fullIssuerIdentifier = $specificIssuerId + '@' + $realm 

# Create issuer name
if ($TokenIssuerFriendlyName.Length -ne 0)
{
    $tokenIssuerName = $TokenIssuerFriendlyName
}
else
{
    $tokenIssuerName = $specificIssuerId 
}

# Register the token issuer
New-SPTrustedSecurityTokenIssuer -Name $tokenIssuerName -Certificate $certificate -RegisteredIssuerName $fullIssuerIdentifier

```


> **SUGERENCIA**
> Las sugerencias que hay al final de la sección anterior también sirven aquí, pero para el parámetro  `-Identity` del cmdlet `Remove-SPTrustedSecurityTokenIssuer`, use el id. de cliente si el parámetro  `-TokenIssuerFriendlyName` no se usó con HighTrustConfig-ForSingleApp.ps1.
  
    
    


## Modificaciones necesarias para las suscripciones a sitios
<a name="SiteSubscriptions"> </a>

Una suscripción de sitio, a veces denominada arrendamiento, es un subconjunto de colecciones de sitios de una granja de servidores de SharePoint. Las suscripciones de sitio se suelen crear en granjas de servidores SharePoint hospedadas. Si el cliente del Complemento de SharePoint de elevada confianza quiere instalar el complemento en una granja que se ha configurado para suscripciones de sitio, habrá que modificar el script HighTrustConfig-*.ps1 que se esté usando. Cada suscripción de sitio tiene su propio identificador de dominio, pero la línea  `$realm = Get-SPAuthenticationRealm` de los scripts siempre devuelve el dominio de la granja de servidores. SharePoint solo considera válidos los tokens de acceso emitidos por un emisor de tokens para el dominio que se especifica después del carácter @ en el identificador completo del emisor. Para obtener el dominio kerberos correcto del sitio web donde se va a instalar el complemento, el script tiene que usar el parámetro `-ServiceContext` en la llamada a `Get-SPAuthenticationRealm`. El objeto del contexto de servicio, a su vez, se crea a partir de la colección de sitios primaria del sitio web de destino. A continuación, verá un ejemplo donde  `$WebURL` es una variable de cadena con la dirección URL completa de un sitio web de SharePoint, como "http://marketing.contoso.com/sites/northteam/july." Este código permite que el complemento de elevada confianza se ejecute no solo en el sitio web especificado, sino en todos los sitios web que hay dentro de la misma suscripción de sitio.
  
    
    

```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc
```

Para completar la modificación del script, agregue un parámetro adicional necesario  `$WebURL` a la lista de parámetros en la parte superior del archivo, como se ve a continuación:
  
    
    



```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)
```

No se olvide de poner una coma después del parámetro que precede al nuevo parámetro. Expanda el ejemplo "Uso" en la línea superior para tener en cuenta el nuevo parámetro con algo como lo siguiente:  `-WebURL <full URL where SP add-in will be installed>`.
  
    
    
Para hacer que la Complemento de SharePoint sea de confianza en todas las suscripciones de sitio, obtenga una referencia a la granja de servidores con el cmdlet  `Get-SPFarm` y luego itere su propiedad **SiteSubscriptions**. Pase cada objeto **SPSiteSubscription** al cmdlet `Get-SPServiceContext` como el valor del parámetro `-SiteSubscription`. A continuación, verá un ejemplo:
  
    
    



```

$Farm = Get-SPFarm
foreach ($ss in $Farm.SiteSubscriptions)
{
    $sc = Get-SPServiceContext -SiteSubscription $ss
    $realm = Get-SPAuthenticationRealm -ServiceContext $sc

    # All of the lines from the draft script below the call to 
    # Get-SPAuthenticationRealm are inserted here inside the loop.
}
# end of script
```


