---
title: Scripts de configuração de alta confiança para SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: ebc9eb42-cca8-436f-a035-0c4c9e7d8305
---


# Scripts de configuração de alta confiança para SharePoint 2013
Obtenha personalizável Windows PowerShell scripts que configurar um farm Microsoft SharePoint 2013 para usar uma alta confiança Suplemento do SharePoint.
## Como usar os scripts
<a name="Usage"> </a>

Os scripts a seguir são usados para designar um ou mais certificados digitais x. 509 como emissores confiáveis de tokens de acesso em um farm de preparo ou de produção Microsoft SharePoint 2013. (Para um script que é mais apropriado para um ambiente de desenvolvimento Suplementos do SharePoint, consulte  [Criar o SharePoint de alta confiança Add-ins](create-high-trust-sharepoint-add-ins.md).) Nenhum conjunto único de scripts pode trabalhar para cada farm do SharePoint, porque há muitas maneiras diferentes que os certificados podem ser adquiridos e armazenados. Por esse motivo, observe o seguinte:
  
    
    

- Os scripts devem ser executados em SharePoint Management Shell em qualquer servidor do SharePoint no farm.
    
  
- Esses scripts devem ser considerados como rascunhos que talvez precisem ser personalizado.
    
  
- Eles são usados como parte do processo geral de publicação de uma alta confiança Suplemento do SharePoint. Eles devem ser usados apenas por alguém que esteja familiarizado com os tópicos  [Criando Add-ins do SharePoint que usam a autorização de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) e [Embalar e publicar Add-ins do SharePoint de alta confiança](package-and-publish-high-trust-sharepoint-add-ins.md) e os pré-requisitos listados neles.
    
  
- Eles também devem ser revisados e personalizados conforme necessário por alguém que esteja familiarizado com as diretivas de certificado do cliente add-in.
    
  
- Os dois principais scripts abaixo, **HighTrustConfig-ForSharedCertificate.ps1** e **HighTrustConfig-ForSingleApp.ps1**, supõem que um administrador tenha adquirido um certificado para o componente do aplicativo web remoto do add-in ou suplementos e que o administrador tenha armazenado temporariamente uma versão. cer do certificado (que não contém a chave privada) em uma pasta para o qual as contas de usuário para os seguintes pools de aplicativos do IIS nos servidores do SharePoint têm acesso de leitura :
    
  - **SecurityTokenServiceApplicationPool**
    
  
  - O suplemento do pool que serve o site do IIS que hospeda o pai de aplicativo web do SharePoint para o seu site do SharePoint de teste. Para o site do IIS do **SharePoint - 80**, o pool é chamado **OServerPortalAppPool**.
    
  

    Para localizar a conta de usuário que está usando um pool de aplicativos, abra o Gerenciador do IIS em um servidor do SharePoint e clique duas vezes em **Pools de aplicativos** no painel **conexões**. A coluna de **identidade** na lista **Pools de aplicativos** que abre mostra os usuários para os pools de aplicativos.
    
  
- As instruções para os dois scripts principais também pressupõem que o certificado (s) tiver sido instalado no IIS no servidor (es) que hospedam os aplicativos web remoto. Para obter instruções, consulte  [Configurar o servidor de web remoto com o certificado](package-and-publish-high-trust-sharepoint-add-ins.md#ConfigureRemote).
    
  
Consulte as notas de uso específico para cada script nas seções a seguir.
  
    
    

## AddSPRootAuthority.ps1
<a name="RootScript"> </a>

O certificado do componente aplicativo web remoto de alta confiança Suplemento do SharePoint for obtido de uma autoridade de certificação (CA) comercial ou um domínio da autoridade de certificação. Em ambos os casos, o certificado é o link mais baixo em uma cadeia de certificados, cada confiando acima dele, qual se origina com uma raiz da autoridade de certificação (comercial ou domínio). SharePoint 2013 requer que  *todos*  os certificados na cadeia a ser adicionado à lista do SharePoint de autoridades raiz confiáveis. O script a seguir pode ser usado para adicionar cada certificado na cadeia *exceto a menor*  à lista. O certificado mais baixo na cadeia, o certificado que está acoplado diretamente para o aplicativo web remoto, é adicionado às autoridades raiz em um dos principais scripts descritos nas seções posteriores.
  
    
    
Os parâmetros do script são as seguintes:
  
    
    


|**Parâmetro**|**Valor**|
|:-----|:-----|
|-CertPath <br/> |O caminho completo e nome de arquivo do certificado (o arquivo. cer). <br/> |
|-CertName <br/> |O nome do certificado. Para localizar o nome, exiba as propriedades do certificado. <br/> |
   
Por exemplo, no cenário comum onde o certificado do aplicativo da web é emitido por um servidor de autoridade de certificação intermediário (também chamado ' enterprise') e seu certificado, por sua vez, é emitido por um servidor de autoridade de certificação raiz (também chamado de 'standalone'), o script deve ser executado uma vez para cada um dos certificados de dois servidores de CA. Isso mostra o seguinte:
  
    
    



```

./AddSPRootAuthority.ps1 -CertPath "\\\\CertStorage\\RootCA.cer" -CertName "Contoso Root CA"

./AddSPRootAuthority.ps1 -CertPath "C:\\RegionalCerts\\NorthRegion.cer" -CertName "North Region Intermediate CA"

```

Se todos os certificados de de alta confiança do cliente Suplementos do SharePoint provém da mesma cadeia de certificados, como seria normalmente o caso, esse script nunca é usado novamente.
  
    
    
O exemplo a seguir é o código para o script. Simplesmente colá-lo em qualquer editor de texto ou no editor de PowerShell (IPowerShell ISE) e salve-o como AddSPRootAuthority.ps1.
  
    
    

> **OBSERVAçãO**
> O arquivo foi salvo como formato ANSI, não UTF-8. PowerShell pode dar erros de sintaxe quando ele é executado um arquivo com um formato ANSI não. O bloco de notas e o editor de PowerShell serão definido como salvá-lo como ANSI. Se você usar qualquer outro editor para salvar o arquivo, certifique-se de que você está salvando-o como ANSI.
  
    
    




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

As principais tarefas desse script são registrar o certificado compartilhado dos componentes do aplicativo web remoto em vários alta confiança Suplementos do SharePoint com o SharePoint como ambas uma autoridade de raiz e como um emissor de token de acesso confiável. Esse script não é usado se o cliente estiver usando certificados separados com cada de alta confiança Suplemento do SharePoint. Para esse cenário, consulte  [HighTrustConfig-ForSingleApp.ps1](#SingleAppScript) abaixo. Se todos os suplementos usam o mesmo certificado, esse script é executado apenas uma vez. Se alguns conjuntos de suplementos usam um certificado diferente de outros conjuntos, em seguida, esse script é executado uma vez para cada conjunto.
  
    
    
A tabela a seguir descreve os parâmetros e opções para o script. Personalização do script pode exigir alterações nesta tabela.
  
    
    


|**Parâmetro**|**Valor**|
|:-----|:-----|
|-CertPath (obrigatório) <br/> |O caminho completo para o arquivo CER compartilhada. <br/> |
|-CertName (obrigatório) <br/> |O nome do certificado compartilhado. Para localizar o nome, abra o IIS no servidor web remoto que hospeda o aplicativo web remoto. Realce o nó  _server name_ e clique duas vezes em **certificados**. O certificado será listado por seu nome. <br/> |
|-TokenIssuerFriendlyName (opcional) <br/> |Um nome amigável exclusivo para o emissor de token, até 50 caracteres. Isso pode ser útil se você estiver depurando problemas com emissores de token. O nome deve ser exclusivo. Incluindo um GUID seria Certifique-se de que ele é, mas um GUID utiliza até 32 dos 50 caracteres, considere a conversão de um GUID para uma cadeia de caracteres de Base 64 que pode ser reduzida para 22 caracteres. Se esse parâmetro não for usado, o script usa o nome "suplementos de alta confiança  _<base64 version of issuer GUID>_". <br/> |
   
O exemplo a seguir é um exemplo de executar esse script.
  
    
    
 `./HighTrustConfig-ForSharedCertificate.ps1 -CertPath "C:\\Certs\\MyCert.cer" -CertName "My Cert" -TokenIssuerFriendlyName "SharePoint High-Trust Add-ins Token Issuer"`
  
    
    

> **IMPORTANTE**
> O registro do certificado como um emissor de token não tem efeito imediato. Pode levar desde que 24 horas antes de todos os servidores do SharePoint reconhecem o novo emissor de token. Executar um comando iisreset em todos os servidores do SharePoint-los reconhecer imediatamente o emissor, causaria a se você pode fazer isso sem atrapalhar os usuários do SharePoint.
  
    
    

Iniciar um novo arquivo em um editor de texto ou editor do PowerShell e copie o seguinte para um arquivo de texto e salve-o como HighTrustConfig-ForSharedCertificate.ps1. Use o formato ANSI, não UTF-8.
  
    
    



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


> **DICA**
> Se o script gera um erro depois que a linha  `New-SPTrustedRootAuthority` foi executado com êxito, o script não pode ser executado novamente até que esse objeto foi removido. Use o cmdlet a seguir, onde o valor do parâmetro `-Identity` é a mesma que foi usado para o parâmetro `-CertName` no script.>  `Remove-SPTrustedRootAuthority -Identity <certificate name>`> Se o emissor de token precisa ser removido por qualquer motivo, use o seguinte cmdlet:>  `Remove-SPTrustedSecurityTokenIssuer -Identity <issuer name>`> Se o parâmetro  `-TokenIssuerFriendlyName` foi usado, em seguida, use o mesmo valor para `-Identity`. Se o parâmetro  `-TokenIssuerFriendlyName` não foi usado, um GUID aleatório é parte do nome do emissor. Para obter o valor necessário para `-Identity`, execute o seguinte cmdlet. Em seguida, abra o arquivo de TokenIssuers.txt que é produzido e localizar o emissor cujo nome é "suplementos de alta confiança  _<base64 version of issuer GUID>_". Use esse nome inteiro para  `-Identity`.>  `Get-SPTrustedSecurityTokenIssuer | Out-File -FilePath "TokenIssuers.txt"`
  
    
    


## HighTrustConfig-ForSingleApp.ps1
<a name="SingleAppScript"> </a>

As principais tarefas desse script são registrar o certificado do aplicativo web remoto em uma alta confiança Suplemento do SharePoint com o SharePoint como ambas uma autoridade de raiz e como um emissor de token de acesso confiável. Esse script não é usado se o cliente está usando um único certificado para vários Suplementos do SharePoint. Para esse cenário, consulte  [HighTrustConfig-ForSharedCertificate.ps1](#MultipleAppScript) acima. Esse script é executado para cada de alta confiança Suplemento do SharePoint.
  
    
    
A tabela a seguir descreve os parâmetros e opções para o script. Personalização do script pode exigir alterações nesta tabela.
  
    
    


|**Parâmetro**|**Valor**|
|:-----|:-----|
|-CertPath (obrigatório) <br/> |O caminho completo para o arquivo CER compartilhada. <br/> |
|-CertName (obrigatório) <br/> |O nome do certificado compartilhado. Para localizar o nome, abra o IIS no servidor web remoto que hospeda o aplicativo web remoto. Realce o nó  _server name_ e clique duas vezes em **certificados**. O certificado será listado por seu nome. <br/> |
|-SPAppClientID (obrigatório) <br/> |A identificação do cliente (uma GUID) que foi usada quando o Suplemento do SharePoint foi registrado no appregnew.aspx. Isso é usado como a ID do emissor do emissor de token. O script garante que ele está em minúscula, o que é um requisito para IDs do emissor. <br/> |
|-TokenIssuerFriendlyName (opcional) <br/> |Um nome amigável exclusivo para o emissor de token, até 50 caracteres. Isso pode ser útil se você estiver depurando problemas com emissores de token. O nome deve ser exclusivo. Considere usando o nome do Suplemento do SharePoint. Se esse parâmetro não for usado, o script usa o valor de  `-SPAppClientID` como o nome. <br/> |
   
Este é um exemplo de chamar o script:
  
    
    
 `./HighTrustConfig-ForSingleApp.ps1 -CertPath "\\\\MyServer\\Certs\\MyCert.cer" -CertName "My Cert" -SPAppClientID "afe332f4-1f87-4c31-89b8-9c343ad7f24e" -TokenIssuerFriendlyName "Payroll add-in"`
  
    
    

> **IMPORTANTE**
> O registro do certificado como um emissor de token não tem efeito imediato. Pode levar desde que 24 horas antes de todos os servidores do SharePoint reconhecem o novo emissor de token. Executar um comando iisreset em todos os servidores do SharePoint-los reconhecer imediatamente o emissor, causaria a se você pode fazer isso sem atrapalhar os usuários do SharePoint.
  
    
    

Iniciar um novo arquivo em um editor de texto ou editor do PowerShell e copie o seguinte para um arquivo de texto e salve-o como HighTrustConfig-ForSingleApp.ps1. Use o formato ANSI, não UTF-8.
  
    
    



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


> **DICA**
> As dicas no final da seção anterior aplicam também aqui, mas para o parâmetro  `-Identity` do cmdlet `Remove-SPTrustedSecurityTokenIssuer` , usam a identificação do cliente, se o parâmetro `-TokenIssuerFriendlyName` não foi usado com HighTrustConfig-ForSingleApp.ps1.
  
    
    


## Modificações necessárias para assinaturas de site
<a name="SiteSubscriptions"> </a>

Uma assinatura de site, às vezes chamado um locatário, é um subconjunto dos conjuntos de sites em um farm do SharePoint. Inscrições de site são geralmente criadas nos farms do SharePoint hospedados. Se o cliente sobre a alta confiança Suplemento do SharePoint quiser instalar o suplemento em um farm que foi configurado para assinaturas de site, em seguida, aquele que dos dois scripts HighTrustConfig-ps1 está sendo usada precisa ser modificado. Cada inscrição de site tem sua própria ID do realm, mas a linha  `$realm = Get-SPAuthenticationRealm` nos scripts sempre retorna o território do farm. Os tokens de acesso emitidos por um emissor de token são apenas considerados como válido pelo SharePoint para o realm especificado após o "@" caracteres na identificação do emissor completo. Para obter o território correto para o site onde o suplemento vai ser instalado, o script deve usar o parâmetro `-ServiceContext` na chamada a `Get-SPAuthenticationRealm`. O objeto de contexto de serviço, por sua vez, é criado a partir do conjunto de sites pai do site de destino de. O exemplo a seguir está um exemplo, onde o  `$WebURL` é uma variável de cadeia de caracteres com a URL completa de um site do SharePoint, como "http://marketing.contoso.com/sites/northteam/july". Este código permite que o suplemento de alta confiança ser executado, não apenas no site especificado, mas cada site de assinatura do mesmo site.
  
    
    

```

$Web = Get-SPWeb $WebURL
$sc = Get-SPServiceContext -Site $Web.Site
$realm = Get-SPAuthenticationRealm -ServiceContext $sc
```

Para concluir a modificação do script, adicione um parâmetro obrigatório adicionais  `$WebURL` à lista param na parte superior do arquivo, semelhante a esta:
  
    
    



```

param (
    # other parameters omitted 
    [Parameter()][String] $WebURL
)
```

Certifique-se de adicionar uma vírgula depois do parâmetro que precede a uma nova. E expanda o exemplo de "Uso" na linha superior para levar em consideração o novo parâmetro algo parecido com isto:  `-WebURL <full URL where SP add-in will be installed>`.
  
    
    
Para tornar o Suplemento do SharePoint confiáveis para cada inscrição de site, obter uma referência para o farm com o cmdlet  `Get-SPFarm` e, em seguida, percorrer a sua propriedade **SiteSubscriptions**. Passe a cada objeto **SPSiteSubscription** para o cmdlet `Get-SPServiceContext` como o valor do parâmetro `-SiteSubscription` . O exemplo a seguir é um exemplo.
  
    
    



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


