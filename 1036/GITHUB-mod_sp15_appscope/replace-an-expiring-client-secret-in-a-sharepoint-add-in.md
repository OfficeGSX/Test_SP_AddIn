---
title: Remplacement d'une clé secrète client arrivant à expiration dans un complément pour SharePoint
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# Remplacement d'une clé secrète client arrivant à expiration dans un complément pour SharePoint
Découvrez comment ajouter une nouvelle clé secrète client pour un Complément SharePoint, enregistrée avec AppRegNew.aspx.
Les clés secrètes client pour les Compléments SharePoint, enregistrées avec la page AppRegNew.aspx, expirent au bout d'un an. Cet article explique comment ajouter une nouvelle clé secrète pour le complément, et créer une clé valable pour une durée de trois ans.
  
    
    


> **REMARQUE**
> Cet article concerne les Compléments SharePoint distribués via un catalogue de l'organisation et enregistrés avec la page AppRegNew.aspx. Si le complément est enregistré sur Mon tableau de bord vendeur, consultez la section  [Création ou mise à jour d'ID et de clés secrètes client dans le service Mon tableau de bord vendeur](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update). 
  
    
    


## Conditions requises pour mettre à jour une clé secrète client

Tenez compte des informations suivantes avant de commencer :
  
    
    

-  [Assistant de connexion Microsoft Online Services](http://www.microsoft.com/download/details.aspx?id=39267) doit être installé sur l'ordinateur de développement.
    
  
- Le module Microsoft Online Services PowerShell ( [32 bits](http://go.microsoft.com/fwlink/p/?linkid=236298) ; [64 bits](http://go.microsoft.com/fwlink/p/?linkid=236297)) doit être installé sur l'ordinateur de développement.
    
  
- Vous devez être administrateur client du client Office 365 (ou administrateur de batterie de serveurs sur la batterie de serveurs) où le complément a été enregistré avec la page AppRegNew.aspx.
    
  

## Recherche des dates d'expiration des Compléments SharePoint installés sur l'architecture Office 365


  
    
    

1. Ouvrez Windows PowerShell et exécutez la cmdlet suivante :
    
 ```
  
Connect-MsolService

 ```

2. À l'invite de connexion, entrez les identifiants de l'administrateur client (ou de l'administrateur de batterie de serveurs) pour l'architecture Office 365 ou la batterie de serveurs où le complément a été enregistré avec AppRegNew.aspx.
    
  
3. Générez un rapport répertoriant tous les compléments, ainsi que la date d'expiration de leur clé secrète, en saisissant les lignes de code suivantes. Tenez compte des informations suivantes :
    
  - Le code filtre d'abord les applications Microsoft, les compléments en cours de développement (et un type de complément, aujourd'hui obsolète, dit « auto-hébergé »).
    
  
  - Ensuite, il filtre les compléments non-SharePoint et les compléments qui utilisent des clés asymétriques, telles que des flux de travail.
    
  

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

4. Ouvrez le fichier C:\\temp\\appsec.txt pour afficher le rapport. Laissez la fenêtre Windows PowerShell ouverte pour effectuer la procédure suivante, si l'une des clés secrètes arrive à expiration.
    
  

## Génération d'une nouvelle clé secrète


  
    
    

1. Créez une variable de l'ID client à l'aide du code suivant, en utilisant l'ID client du Complément SharePoint comme paramètre.
    
 ```
  
$clientId = 'client id of the add-in'

 ```

2. Générez une nouvelle clé secrète client en saisissant le code suivant :
    
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

3. La nouvelle clé secrète client apparaît sur la console Windows PowerShell. Copiez-la dans un fichier texte. Vous en aurez besoin pour la suite.
    
  

> **CONSEIL**
> Par défaut, la clé secrète du complément est valable pour une durée d'un an. Vous pouvez modifier sa durée de validité (maximum 3 ans) à l'aide du paramètre **-EndDate** dans les trois appels de la cmdlet **New-MsolServicePrincipalCredential**. La valeur du paramètre doit correspondre à un objet  [DateTime](http://msdn2.microsoft.com/FR-FR/library/03ybds8y) dont la valeur ne peut être supérieure à 3 ans à compter de la valeur indiquée dans l'élément **DateTime.Now**. 
  
    
    


## Mise à jour de l'application web distante dans Visual Studio pour utiliser la nouvelle clé secrète


> **IMPORTANTE**
> Si votre complément a été créé à partir d'une version préliminaire des Outils de développement Microsoft Office pour Visual Studio, il peut contenir une version obsolète du fichier TokenHelper.cs (ou .vb). Si le fichier ne contient pas la chaîne « secondaryClientSecret », il est obsolète et doit être remplacé pour que le mot de passe de l'application web puisse être modifié. Pour obtenir une copie de la version finale du fichier, Visual Studio 2012 ou version ultérieure doit être installé. Créez un projet Complément SharePoint dans Visual Studio. Copiez le fichier TokenHelper dans le projet d'application web de votre Complément SharePoint. 
  
    
    


1. Ouvrez le projet du Complément SharePoint dans Visual Studio, puis le fichier web.config du projet d'application web. La section **appSettings** contient les clés pour l'ID client et la clé secrète client. Voici un exemple :
    
 ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

 ```

2. Modifiez le nom de la clé **ClientSecret** et renommez-la «SecondaryClientSecret » comme dans l'exemple suivant :
    
 ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
 ```

3. Ajoutez une nouvelle clé **ClientSecret** et attribuez-lui votre nouvelle clé secrète client. La syntaxe de la clé doit ressembler à ceci :
    
 ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
 ```

4. Si vous avez créé un nouveau fichier TokenHelper, régénérez le projet.
    
  
5. Republiez l'application web.
    
  

## Création d'une clé secrète client pour trois ans

En cas d'expiration de clés secrètes client, vous devez d'abord supprimer toutes les clés secrètes expirées d'un élément **clientId** donné. Ensuite, créez un autre ID client avec MSO PowerShell, puis attendez au moins 24 heures avant de tester l'application avec le nouvel élément **clientId** et la nouvelle clé **ClientSecret**.
  
    
    

1. Connectez-vous à MSOnline en tant qu'administrateur client en utilisant la syntaxe suivante obtenue via SharePoint 2013 pour Windows PowerShell.
    
 ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

 ```

2. Obtenez la chaîne **ServicePrincipals** et les clés correspondantes. L'impression de la chaîne **$keys** renvoie trois enregistrements. Remplacez chaque chaîne **KeyId** dans *KeyId1*  , *KeyId2*  et *KeyId3*  . Vous verrez également la chaîne **EndDate** de chaque clé. Vérifiez que votre clé expirée apparaît ici.
    
    **Remarque :** L'élément **clientId** doit correspondre à l'élément **clientId** expiré. Il est recommandé de supprimer toutes les clés, expirées et non expirées, pour cet élément **clientId**.
    


 ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

 ```

3. Générez une nouvelle clé **ClientSecret** pour cet élément **clientID**. Elle utilise l'élément **clientId** défini à l'étape précédente. La nouvelle clé **ClientSecret** est valable pour une durée de 3 ans.
    
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

4. Copiez la nouvelle clé **$newClientSecret**.
    
  
5. Remplacez l'élément **Web.config** par l'élément **ClientId** et la clé **ClientSecret**. Vous n'avez pas besoin de paramètres d'application pour la chaîne **SecondaryClientSecret**.
    
  
6. Attendez au moins 24 heures avant de propager la clé **ClientSecret** à SharePoint Office (SPO).
    
  

## Voir aussi


#### Autres ressources


  
    
    
 [Provider Hosted App fails on SPO](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
