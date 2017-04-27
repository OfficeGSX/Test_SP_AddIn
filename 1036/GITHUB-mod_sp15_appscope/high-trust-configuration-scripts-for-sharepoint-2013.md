---
title: Scripts de configuration à haut niveau de fiabilité pour SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# Scripts de configuration à haut niveau de fiabilité pour SharePoint 2013
Obtenez des scripts personnalisables Windows PowerShell qui configurent une batterie de serveurs Microsoft SharePoint 2013 pour utiliser un Complément SharePoint à haut niveau de fiabilité.
## Utilisation des scripts
<a name="Usage"> </a>

Les scripts ci-dessous désignent des certificats numériques X.509 en tant qu'émetteurs approuvés de jetons d'accès dans une batterie de serveurs Microsoft SharePoint 2013 intermédiaire ou de production. (Pour un script plus approprié à un environnement de développement de Compléments SharePoint, voir  [Créer des compléments à haut niveau de fiabilité pour SharePoint 2013](create-high-trust-sharepoint-add-ins.md).) Aucun ensemble de scripts ne peut fonctionner pour toutes les batteries de serveurs SharePoint, car il existe trop de méthodes différentes pour acquérir et stocker les certificats. Pour cette raison, veuillez noter les points suivants :




- Les scripts sont destinés à une exécution dans SharePoint Management Shell sur un serveur SharePoint de la batterie de serveurs.


- Ces scripts doivent être considérés comme des brouillons qui devront peut-être être personnalisés.


- Ils interviennent dans le cadre du processus global de la publication d'un Complément SharePoint à haut niveau de fiabilité. Ils doivent être utilisés uniquement par une personne connaissant les rubriques  [Création de compléments pour SharePoint qui utilisent l'autorisation à haut niveau de fiabilité](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) et [Empaquetage et publication de compléments SharePoint à haut niveau de fiabilité](package-and-publish-high-trust-sharepoint-add-ins.md), ainsi que les conditions préalables qui y sont énumérées.


- Ils doivent également être révisés et personnalisés de façon appropriée par une personne connaissant les stratégies de certification du client du complément.


- Les deux principaux scripts ci-dessous, **HighTrustConfig-ForSharedCertificate.ps1** et **HighTrustConfig-ForSingleApp.ps1**, supposent qu'un administrateur a acquis un certificat pour le composant de complément web distant des compléments et que cet administrateur a stocké temporairement une version .cer de ces certificats (qui ne contient pas la clé privée) dans un dossier sur lequel les comptes d'utilisateur des pools d'applications IIS suivants sur les serveurs SharePoint ont un accès en lecture :

  - **SecurityTokenServiceApplicationPool**


  - Pool de compléments servant le site web IIS hébergeant l'application web SharePoint parente pour votre Site web SharePoint de test. Pour le site web IIS **SharePoint - 80**, le pool est nommé **OServerPortalAppPool**.



    Pour déterminer le compte d'utilisateur employé par un pool d'applications, ouvrez le Gestionnaire IIS sur un serveur SharePoint et double-cliquez sur **Pools d'applications** dans le volet **Connexions**. La colonne **Identité** de la liste **Pools d'applications** affiche les utilisateurs des pools d'applications.


- Les instructions des deux principaux scripts supposent également que les certificats ont été installés pour IIS sur les serveurs qui hébergent les applications web à distance. Pour obtenir des instructions, voir  [Configurer le serveur Web distant avec le certificat](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote).


Consultez les remarques d'utilisation spécifique de chaque script dans les sections ci-dessous.




## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

Le certificat du composant d'application web à distance du Complément SharePoint à haut niveau de fiabilité est obtenu auprès d'une autorité de certification commerciale ou d'une autorité de certification de domaine. Dans les deux cas, le certificat est le maillon le plus faible dans une chaîne de certificats, car chaque élément approuve l'élément supérieur, qui provient d'une autorité de certification racine (commerciale ou de domaine). SharePoint 2013 exige que  *tous*  les certificats de la chaîne soient ajoutés à la liste des autorités de certification racine approuvées de SharePoint. Le script ci-dessous peut être utilisé pour ajouter chaque certificat de la chaîne, à la liste *sauf le plus faible*  . Le certificat le plus faible de la chaîne, celui qui est directement lié à l'application web à distance, est ajouté aux autorités racine dans l'un des principaux scripts décrits dans les sections suivantes.



Les paramètres du script sont les suivants :





|**Paramètre**|**Valeur**|
|:-----|:-----|
|-CertPath  <br/> |Chemin d'accès complet et nom de fichier du certificat (fichier .cer).  <br/> |
|-CertName  <br/> |Nom du certificat. Pour connaître le nom, affichez les propriétés du certificat.  <br/> |
 
Par exemple, si le certificat de l'application web est émis par un serveur d'autorité de certification intermédiaire (également appelé « entreprise ») et que son certificat, à son tour, est émis par un serveur d'autorité de certification racine (également appelé « autonome »), le script doit être exécuté une fois pour chaque certificat des deux serveurs d'autorité de certification. L'exemple suivant illustre cette situation :





```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"
```

Si tous les certificats des Compléments SharePoint à haut niveau de fiabilité de l'utilisateur proviennent de la même chaîne de certificats, comme c'est généralement le cas, ce script n'est utilisé qu'une seule fois.



Voici le code pour le script. Il vous suffit de le copier dans un éditeur de texte ou dans l'éditeur PowerShell (IPowerShell ISE) et de l'enregistrer sous le nom AddSPRootAuthority.ps1.




> **REMARQUE**
> Le fichier doit être enregistré au format ANSI, et non UTF-8. PowerShell peut générer de fausses erreurs de syntaxe lors de l'exécution d'un fichier dans un format autre que ANSI. Par défaut, le Bloc-notes et l'éditeur PowerShell procéderont à l'enregistrement au format ANSI. Si vous utilisez un autre éditeur pour enregistrer le fichier, veillez à l'enregistrer au format ANSI. 






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

Les principales tâches de ce script consistent à inscrire le certificat partagé des composants d'applications web à distance dans plusieurs Compléments SharePoint à haut niveau de fiabilité auprès de SharePoint, à la fois comme autorité racine et comme émetteur de jeton d'accès approuvé. Ce script n'est pas utilisé si le client a recours à des certificats distincts pour chaque Complément SharePoint à haut niveau de fiabilité. Pour ce scénario, voir  [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript) ci-dessous. Si tous les compléments utilisent le même certificat, ce script est exécuté une seule fois. Si certains ensembles de compléments utilisent un certificat différent des autres, ce script est exécuté une fois pour chaque ensemble.



Le tableau suivant décrit les paramètres et les commutateurs du script. La personnalisation du script peut nécessiter des modifications par rapport à ce tableau.





|**Paramètre**|**Valeur**|
|:-----|:-----|
|-CertPath (obligatoire)  <br/> |Chemin d'accès complet au fichier *.cer partagé.  <br/> |
|-CertName (obligatoire)  <br/> |Nom du certificat partagé. Pour connaître le nom, ouvrez IIS sur le serveur web distant qui héberge l'application web à distance. Mettez en surbrillance le nœud  _Nom du serveur_ et double-cliquez sur **Certificats**. Le certificat sera indiqué par son nom.  <br/> |
|-TokenIssuerFriendlyName (facultatif)  <br/> |Nom convivial unique pour l'émetteur de jeton, jusqu'à 50 caractères. Cela peut être utile si vous déboguez les problèmes dus à des émetteurs de jetons. Le nom doit être unique. L'utilisation d'un GUID garantirait cela, mais comme un GUID utiliserait jusqu'à 32 des 50 caractères, envisagez de convertir un GUID en chaîne Base 64, laquelle peut être réduite à 22 caractères. Si ce paramètre n'est pas indiqué, le script utilise le nom « Compléments à haut niveau de fiabilité  _<version Base 64 du GUID de l'émetteur>_ ». <br/> |
 
Voici un exemple d'exécution de ce script.



 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`




> **IMPORTANTE**
> L'inscription du certificat en tant qu'émetteur de jeton n'est pas immédiatement effective. Cela peut prendre 24 heures pour que tous les serveurs SharePoint reconnaissent le nouvel émetteur de jeton. L'exécution d'une commande IISreset sur tous les serveurs SharePoint permet la reconnaissance immédiate de l'émetteur par tous ces serveurs, si ceci est réalisable sans déranger les utilisateurs SharePoint. 




Ouvrez un nouveau fichier dans un éditeur de texte ou l'éditeur PowerShell, puis copiez ce qui suit dans un fichier texte et enregistrez-le sous HighTrustConfig-ForSharedCertificate.ps1. Utilisez le format ANSI, et non UTF-8.





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
$specificIssuerId | select * | Out-File -FilePath "SecureTokenIssuerID.txt"```


> **CONSEIL**
> Si le script renvoie une erreur après l'exécution correcte de la ligne  `New-SPTrustedRootAuthority`, il ne peut pas être réexécuté tant que cet objet n'a pas été supprimé. Utilisez l'applet de commande suivante, où la valeur du paramètre  `-Identity` est identique à celle du paramètre `-CertName` dans le script.<br/>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`<br/> Si l'émetteur de jeton doit être supprimé pour une raison quelconque, utilisez l'applet de commande suivante : <br/>  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`<br/> Si le paramètre  `-TokenIssuerFriendlyName` a été utilisé, indiquez la même valeur pour `-Identity`. Si le paramètre  `-TokenIssuerFriendlyName` n'a pas été utilisé, un GUID aléatoire est employé pour le nom de l'émetteur. Pour obtenir la valeur requise pour `-Identity`, exécutez l'applet de commande suivante. Copiez ensuite le fichier TokenIssuers.txt créé et trouvez l'émetteur portant le nom « Compléments à haut niveau de fiabilité  _<version Base 64 du GUID de l'émetteur>_ ». Utilisez ce nom complet pour `-Identity`. <br/>  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`





## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

Les principales tâches de ce script consistent à inscrire le certificat de l'application web à distance dans un Complément SharePoint à haut niveau de fiabilité auprès de SharePoint, à la fois comme autorité racine et comme émetteur de jeton d'accès approuvé. Ce script n'est pas utilisé si le client se sert d'un certificat unique pour plusieurs Compléments SharePoint. Pour ce scénario, voir  [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript) ci-dessus. Ce script est exécuté pour chaque Complément SharePoint à haut niveau de fiabilité.



Le tableau suivant décrit les paramètres et les commutateurs du script. La personnalisation du script peut nécessiter des modifications par rapport à ce tableau.





|**Paramètre**|**Valeur**|
|:-----|:-----|
|-CertPath (obligatoire)  <br/> |Chemin d'accès complet au fichier *.cer partagé.  <br/> |
|-CertName (obligatoire)  <br/> |Nom du certificat partagé. Pour connaître le nom, ouvrez IIS sur le serveur web distant qui héberge l'application web à distance. Mettez en surbrillance le nœud  _Nom du serveur_ et double-cliquez sur **Certificats**. Le certificat sera indiqué par son nom.  <br/> |
|-SPAppClientID (obligatoire)  <br/> |ID client (GUID) utilisé lors de l'inscription du Complément SharePoint sur appregnew.aspx. Il est utilisé comme ID d'émetteur de jeton. Le script garantit qu'il est composé de lettres minuscules, puisque ce critère est requis pour les ID d'émetteur.  <br/> |
|-TokenIssuerFriendlyName (facultatif)  <br/> |Nom convivial unique pour l'émetteur de jeton, jusqu'à 50 caractères. Cela peut être utile si vous déboguez les problèmes dus à des émetteurs de jetons. Le nom doit être unique. Vous pouvez utiliser le nom du Complément SharePoint. Si ce paramètre n'est pas indiqué, le script utilise la valeur de  `-SPAppClientID` comme nom. <br/> |
 
Voici un exemple d'appel du script :



 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`




> **IMPORTANTE**
> L'inscription du certificat en tant qu'émetteur de jeton n'est pas immédiatement effective. Cela peut prendre 24 heures pour que tous les serveurs SharePoint reconnaissent le nouvel émetteur de jeton. L'exécution d'une commande IISreset sur tous les serveurs SharePoint permet la reconnaissance immédiate de l'émetteur par tous ces serveurs, si ceci est réalisable sans déranger les utilisateurs SharePoint. 




Ouvrez un nouveau fichier dans un éditeur de texte ou l'éditeur PowerShell, puis copiez ce qui suit dans un fichier texte et enregistrez-le sous HighTrustConfig-ForSingleApp.ps1. Utilisez le format et non ANSI, UTF-8.





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


> **CONSEIL**
> Les conseils donnés à la fin de la section précédente s'appliquent ici aussi. Toutefois, pour le paramètre  `-Identity` de l'applet de commande `Remove-SPTrustedSecurityTokenIssuer`, utilisez l'ID client si le paramètre  `-TokenIssuerFriendlyName` n'a pas été utilisé avec HighTrustConfig-ForSingleApp.ps1.





## Modifications nécessaires pour les abonnements de site
<a name="SiteSubscriptions"> </a>

Un abonnement de site, parfois appelé location, est un sous-ensemble de collections de site sur une batterie de serveurs SharePoint. Les abonnements de site sont généralement créés sur des batteries de serveurs SharePoint hébergées. Si le client du Complément SharePoint à haut niveau de fiabilité veut installer le complément sur une batterie de serveurs configurée pour les abonnements de site, l'un des deux scripts HighTrustConfig-*.ps1 en cours d'utilisation doit être modifié. Chaque abonnement à un site a son propre ID de domaine, mais la ligne  `$realm = Get-SPAuthenticationRealm` des scripts renvoie toujours le domaine de la batterie de serveurs. Les jetons d'accès émis par un émetteur de jetons sont considérés comme valides par SharePoint uniquement pour le domaine spécifié après le caractère « @ » dans l'ID complet de l'émetteur. Pour obtenir le bon domaine pour le site web sur lequel le complément sera installé, le script doit utiliser le paramètre `-ServiceContext` dans l'appel vers `Get-SPAuthenticationRealm`. L'objet de contexte de service est créé, à son tour, à partir de la collection de site parent du site web cible. Dans l'exemple ci-dessous,  `$WebURL` est une variable de chaîne avec l'URL complète d'un site web SharePoint, comme « http://marketing.contoso.com/sites/northteam/july ». Ce code permet l'exécution du complément à haut niveau de fiabilité, non seulement sur le site web spécifié, mais également sur tous les sites web d'un même abonnement de site.



```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc```

Pour terminer la modification du script, ajoutez un paramètre  `$WebURL` supplémentaire et nécessaire à la liste des paramètres au début du fichier, comme ceci :





```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)```

N'oubliez pas d'ajouter une virgule après le paramètre qui précède le nouveau. Développez l'exemple Usage sur la ligne supérieure pour prendre en compte le nouveau paramètre avec :  `-WebURL <full URL where SP add-in will be installed>`.



Pour approuver le Complément SharePoint sur chaque abonnement de site, obtenez une référence à la batterie de serveurs avec l'applet de commande  `Get-SPFarm`, puis itérez sa propriété **SiteSubscriptions**. Transmettez chaque objet **SPSiteSubscription** à l'applet de commande `Get-SPServiceContext` en tant que valeur du paramètre `-SiteSubscription`. Voici un exemple.





```

$Farm = Get-SPFarm
foreach ($ss in $Farm.SiteSubscriptions)
{
    $sc = Get-SPServiceContext -SiteSubscription $ss
    $realm = Get-SPAuthenticationRealm -ServiceContext $sc

    # All of the lines from the draft script below the call to 
    # Get-SPAuthenticationRealm are inserted here inside the loop.
}
# end of script```


