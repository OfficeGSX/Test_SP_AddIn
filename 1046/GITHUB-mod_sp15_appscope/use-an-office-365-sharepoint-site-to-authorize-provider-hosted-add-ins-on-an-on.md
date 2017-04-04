---
title: Usar um site do SharePoint do Office 365 para autorizar hospedado no provedor suplementos em um site do SharePoint local
ms.prod: SHAREPOINT
ms.assetid: 2f65ba3f-b246-4064-b4fb-ad18399d387a
---



# Usar um site do SharePoint do Office 365 para autorizar hospedado no provedor suplementos em um site do SharePoint local
Use um site do SharePoint de Office 365 para criar um ambiente onde você pode usar ACS para estabelecer confiança entre um suplemento hospedado no provedor e um farm de SharePoint 2013 local, tal como faria se estivesse desenvolvendo suplementos para um site do SharePoint de Office 365.
## Pré-requisitos para usar o ACS com hospedado no provedor de suplementos no ambientes locais
<a name="Prerequisites"> </a>

Certifique-se de que você tenha o seguinte.
  
    
    

- Um ambiente de desenvolvimento de SharePoint 2013 local. Consulte  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
    
  
- Um site do SharePoint de Office 365. Se ainda não tem um e você deseja configurar um ambiente de desenvolvimento rapidamente, você pode  [Configurar um ambiente de desenvolvimento para suplementos do SharePoint no Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md).
    
  
-  [O Visual Studio 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30682) instalados remotamente ou no computador em que você instalou SharePoint 2013.
    
  
-  [Ferramentas de desenvolvedor do Microsoft Office para Visual Studio 2012](https://msdn.microsoft.com/en-us/office/aa905340.aspx) .
    
  
- A edição de 64 bits do  [Assistente Microsoft Online Services entrar.](http://www.microsoft.com/en-us/download/details.aspx?id=41950) instalado no computador onde você instalou SharePoint 2013.
    
  
-  [Módulo do Microsoft Online Services para Windows Powershell (64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) instalado no computador onde você instalou SharePoint 2013.
    
  

## Criar um certificado e torná-lo o certificado de segurança de serviço de token (STS) da sua instalação local do SharePoint 2013
<a name="Certificate"> </a>

Você precisará substituir o certificado de (STS) de serviço de token de segurança padrão da instalação local do SharePoint 2013 com seu próprio certificado. Este artigo fornece um exemplo de como criar e exportar um certificado de teste usando a opção de **Criar auto assinado certificado** no IIS. Você também pode usar um certificado comercial emitido por uma autoridade de certificação.
  
    
    
 [Crie um arquivo de certificado do teste. pfx primeiro, e um correspondente teste arquivo. cer](http://msdn.microsoft.com/en-us/library/windows/hardware/ff552299%28v=vs.85%29.aspx).
  
    
    
 [Você também pode usar o programa de teste MakeCert para gerar um certificado de teste x. 509](http://msdn.microsoft.com/en-us/library/ms537364%28VS.85%29.aspx).
  
    
    

### Para criar um arquivo de certificado do teste. pfx


1. No Gerenciador do IIS, selecione o nó  _ServerName_ na exibição em árvore à esquerda.
    
  
2. Escolha **Certificados de servidor**, conforme mostrado na Figura 1.
    
   **Figura 1. Opção de certificados de servidor no IIS**

  

     ![Server Certificates option in IIS](images/e38f9b7f-59a3-468c-bcde-a48272f1f217.gif)
  

  

  
3. Clique no link de **Criar um certificado autoassinado** no conjunto de links no lado direito, conforme mostrado na Figura 2.
    
   **Figura 2. Criar vínculo de certificado autoassinado**

  

     ![Create Self-Signed Cerificate link](images/3f0aae5a-e58b-4ec8-b67f-0024abfa2dab.gif)
  

  

  
4. Nomeie o certificado SampleCerte escolha **OK**.
    
  
5. O certificado de atalho e, em seguida, escolha **Exportar**, conforme mostrado na Figura 3.
    
   **Figura 3. Exportar um certificado de teste**

  

     ![Exporting a test certificate](images/997021de-c60c-46b0-961f-7e1e63c0f619.gif)
  

  

  
6. Exporte o arquivo para um local que você escolher e dar a ele uma senha. **Neste exemplo, a senha é.** Em um ambiente de produção, use uma senha forte. Consulte [diretrizes para criar senhas fortes](http://msdn.microsoft.com/en-us/library/bb416446.aspx) e [senhas fortes](http://msdn.microsoft.com/en-us/library/ms161962.aspx).
    
  

## Verifique seu certificado o certificado STS para sua instalação local do SharePoint 2013
<a name="STSCertificate"> </a>

Agora que você tem um certificado, você tornar o certificado STS para seu farm do SharePoint local.
  
    
    
Abra o SharePoint Management Shell como administrador e execute esse script Windows PowerShell.
  
    
    



```

$certPrKPath = "c:\\location of your .pfx file"
$certPassword = "password"
$stsCertificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 $certPrKPath, $certPassword, 20
Set-SPSecurityTokenServiceConfig -ImportSigningCertificate $stsCertificate -confirm:$false

```


> [!OBSERVAçãO]
> O documento de  [Configurar um ambiente híbrido unidirecional com o SharePoint Server 2013 e Office 365](http://download.microsoft.com/download/6/4/4/644BA525-96CB-4739-B08F-18949A9BDADC/sps-2013-config-one-way-hybrid-environment.docx) que está disponível para download na [página de recursos do SharePoint 2013 híbrido](http://www.microsoft.com/en-us/download/details.aspx?id=35593) explica detalhadamente como substituir o certificado de STS padrão do seu farm local com um certificado de uma autoridade de certificação conhecido ou um certificado auto-assinado.
  
    
    


## Configurar a instalação local do SharePoint 2013 usar ACS
<a name="ConnectAAD"> </a>

Figura 4 mostra as quatro etapas para habilitar as conexões que necessárias dentro da arquitetura geral de um provedor hospedado suplemento que é executado em um site local. Ela também mostra o fluxo de tokens OAuth quando o suplemento é executado.
  
    
    

**Figura 4. Fazer com que o ACS trabalhar com uma instalação local do SharePoint usando um site do SharePoint do Office 365**

  
    
    

  
    
    
![Make ACS work with an on-premises installation of SharePoint by using an Office 365 site](images/SP15_OnPremACSArchitecture.png)
  
    
    

  
    
    

1. Crie um proxy de ACS em seu farm de SharePoint 2013 local.
    
  
2. Instale o certificado de assinatura do seu servidor local para seu aluguel Office 365.
    
  
3. Adicione os nomes de domínio totalmente qualificado dos sites em seu farm de SharePoint 2013 onde você deseja executar suplementos para a coleção de nome principal de serviço no seu aluguel Office 365.
    
  
4. Crie um proxy de gerenciamento de suplemento em seu farm de SharePoint 2013.
    
  
A função abaixo faz todo o trabalho para configurar seu site de SharePoint 2013 local para usar o ACS. Você também pode usar esta função para fazer algumas tarefas de limpeza, se for necessário remover configurações anteriores. Há várias maneiras para executar a função no PowerShell. Um método é o seguinte:
  
    
    

  
    
    

1. No servidor do SharePoint local, copie o código na função para um arquivo de texto e salve-o com o nome MySharePointFunctions.psm1 para um ou outro das pastas a seguir (não ambos). Você pode precisar criar partes do caminho, se ele incluir pastas que já não existem. Observe que em ambos os casos, a pasta mais baixa no caminho deve ter o mesmo nome do arquivo.
    
    > [!DICA]
      > O arquivo foi salvo como formato ANSI, não UTF-8. PowerShell pode fornecer erros de sintaxe ao carregar um arquivo com um formato não-ANSI. Bloco de notas do Windows padrão será salvá-lo como ANSI. Se você usar qualquer outro editor para salvar o arquivo, certifique-se de que você está salvando-o como ANSI.

  -  `C:\\users\\username\\documents\\windowspowershell\\modules\\MySharePointFunctions`, onde  _username_ é o administrador de farm que estará executando o arquivo.
    
  
  -  `C:\\windows\\system32\\windowspowershell\\V1.0\\modules\\MySharePointFunctions`
    
  
2. Abra o SharePoint Management Shell como administrador e execute o cmdlet a seguir para verificar se o módulo MySharePointFunctions está listado.
    
  ```
  
Get-Module -listavailable
  ```

3. Execute o cmdlet a seguir para importar o módulo.
    
  ```
  Import-Module MySharePointFunctions
  ```

4. Execute o seguinte cmdlet para verificar que a função de SPFarmToAAD conectar está listada como parte do módulo:
    
  ```
  Get-Command -module MySharePointFunctions
  ```

5. Execute o cmdlet a seguir para verificar se a função de SPFarmToAAD conectar é carregada.
    
  ```
  ls function:\\ | where {$_.Name -eq "Connect-SPFarmToAAD"}
  ```

6. Execute a função  `Connect-SPFarmToAAD` . Certifique-se de fornecer os parâmetros necessários e quaisquer parâmetros opcionais que se aplicam ao seu ambiente do desenvolvedor. Consulte a próxima seção para obter detalhes e exemplos.
    
  

  
    
    

### Parâmetros de função SPFarmToAAD conectar
<a name="parameters"> </a>



|**Parâmetro**|**Valor**|
|:-----|:-----|
| `-AADDomain` (obrigatório) <br/> |O *. domínio onmicrosoft.com que você criou quando se inscreveu no seu site de Office 365 ( _yourcustomdomain_. onmicrosoft.com). Quando o script solicita a autenticação, use o nome de usuário e senha que você criou para este domínio:  _username_@ _yourcustomdomain_. onmicrosoft.com. <br/> |
| `-SharePointOnlineUrl` (obrigatório) <br/> |A URL do site do SharePoint Office 365 ( _https://yourcustomdomain_. sharepoint.com). Observe que o domínio pai é  *não*  onmicrosoft.com. <br/> |
| `-SharePointWeb` (às vezes obrigatório) <br/> |A URL completa (incluindo o protocolo) do aplicativo da web do SharePoint no local onde você executará hospedado no provedor de suplementos. Esta função adiciona apenas um aplicativo web do SharePoint do seu farm local ao ACS. Se você não especificar um valor para isso, o script seleciona o primeiro aplicativo web em seu farm. Se você estiver usando um Host nome Site conjunto (HNSC) que podem ser definidos com um caractere curinga (como  _http://*.contoso.com_), você pode usar essa cadeia de caracteres como o valor para esse parâmetro. Se o aplicativo da web tem um mapeamento de acesso alternativo (AAM) para a zona da Internet, você deve usar essa URL AAM para esse parâmetro. Se o aplicativo web do SharePoint não está configurado para HTTPS, você terá de usar HTTP como o protocolo e  *você precisa usar a opção - AllowOverHttp (veja abaixo nesta tabela).*  <br/> Se você quiser executar hospedado no provedor suplementos que usam ACS em mais aplicativos da web em seu farm, você precisará adicioná-los ao conjunto de nome principal de serviço. O script Windows PowerShell que acompanha a função de  `Connect-SPFarmToAAD` abaixo mostra como adicionar todos os aplicativos da web em seu farm para o conjunto de nome principal de serviço. <br/> |
| `-AllowOverHttp` (opcional) <br/> |Use esta opção se você estiver trabalhando com um ambiente de desenvolvedor e não quer usar SSL com seus suplementos. Você precisa usar essa opção se o aplicativo web do SharePoint não está configurado para HTTPS. <br/> |
| `-O365Credentials` (opcional) <br/> |O primeiro caractere é maiúsculo "O", não um zero. Se você achar que se repetidamente executando o script para fins de depuração, essa opção permite que você evite precisarem inserir manualmente seu O365 nome e senha cada vez. Antes de poder usar esse parâmetro, você deve criar o objeto de credenciais que você atribuirá-lo com esses cmdlets: <br/> ```$User = "username@yourcustomdomain.onmicrosoft.com"$PWord = ConvertTo-SecureString -String "the_password" -AsPlainText -Force$Credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $PWord```Use  `$Credential` como o valor do parâmetro `-O365Credentials` . <br/> |
| `-Verbose` (opcional) <br/> |Essa opção gera comentários mais detalhados que podem ajudá-lo se a função não está funcionando e você precisa executá-la novamente para depuração. <br/> |
| `-RemoveExistingACS` (opcional) <br/> |Use esta opção se você estiver substituindo uma conexão existente com Microsoft Azure do Active Directory. Remove um proxy de ACS existente se você já tiver criado um em seu farm. <br/> |
| `-RemoveExistingSTS` (opcional) <br/> |Use esta opção se você estiver substituindo uma conexão existente com Microsoft Azure do Active Directory. Remove um emissor do token de segurança confiável existente que resta ao longo de uma conexão anterior ao ACS. <br/> |
| `-RemoveExistingSPOProxy` (opcional) <br/> |Use esta opção se você estiver substituindo uma conexão existente com Microsoft Azure do Active Directory. Remove um proxy de gerenciamento de suplemento existente se você já tiver criado um em seu farm. <br/> |
| `-RemoveExistingAADCredentials` (opcional) <br/> |Use esta opção se você estiver substituindo o site do SharePoint Office 365. <br/> |
   
A seguir é exemplos:
  
    
    

```

Connect-SPFarmToAAD -AADDomain 'MyO365Domain.onmicrosoft.com' -SharePointOnlineUrl https://MyO365Domain.sharepoint.com

Connect-SPFarmToAAD -AADDomain 'MyO365Domain.onmicrosoft.com' -SharePointOnlineUrl https://MyO365Domain.sharepoint.com -SharePointWeb https://fabrikam.com

Connect-SPFarmToAAD -AADDomain 'MyO365Domain.onmicrosoft.com' -SharePointOnlineUrl https://MyO365Domain.sharepoint.com -SharePointWeb http://northwind.com -AllowOverHttp

Connect-SPFarmToAAD -AADDomain 'MyO365Domain.onmicrosoft.com' -SharePointOnlineUrl https://MyO365Domain.sharepoint.com -SharePointWeb http://northwind.com -AllowOverHttp -RemoveExistingACS -RemoveExistingSTS -RemoveExistingSPOProxy -RemoveExistingAADCredentials

```


### Script de função SPFarmToAAD conectar
<a name="function"> </a>


```

function Connect-SPFarmToAAD {
param(
    [Parameter(Mandatory)][String]   $AADDomain,
    [Parameter(Mandatory)][String]   $SharePointOnlineUrl,
    #Specify this parameter if you don't want to use the default SPWeb returned
    [Parameter()][String]            $SharePointWeb,
    [Parameter()][System.Management.Automation.PSCredential] $O365Credentials,
    #Use these switches if you're replacing an existing connection to AAD.
    [Parameter()][Switch]            $RemoveExistingACS,
    [Parameter()][Switch]            $RemoveExistingSTS,
    [Parameter()][Switch]            $RemoveExistingSPOProxy,
    #Use this switch if you're replacing the Office 365 SharePoint site.
    [Parameter()][Switch]            $RemoveExistingAADCredentials,
    #Use this switch if you don't want to use SSL when you launch your app.
    [Parameter()][Switch]            $AllowOverHttp
)
    #Prompt for credentials right away.
    if (-not $O365Credentials) {
        $O365Credentials = Get-Credential -Message "Admin credentials for $AADDomain"
    }
    Add-PSSnapin Microsoft.SharePoint.PowerShell
    #Import the Microsoft Online Services Sign-In Assistant.
    Import-Module -Name MSOnline
    #Import the Microsoft Online Services Module for Windows Powershell.
    Import-Module MSOnlineExtended -force -verbose 
    #Set values for Constants.
    New-Variable -Option Constant -Name SP_APPPRINCIPALID -Value '00000003-0000-0ff1-ce00-000000000000' | Out-Null
    New-Variable -Option Constant -Name ACS_APPPRINCIPALID -Value '00000001-0000-0000-c000-000000000000' | Out-Null
    New-Variable -Option Constant -Name ACS_APPPROXY_NAME -Value ACS
    New-Variable -Option Constant -Name SPO_MANAGEMENT_APPPROXY_NAME -Value 'SPO Add-in Management Proxy'
    New-Variable -Option Constant -Name ACS_STS_NAME -Value ACS-STS
    New-Variable -Option Constant -Name AAD_METADATAEP_FSTRING -Value 'https://accounts.accesscontrol.windows.net/{0}/metadata/json/1'
    New-Variable -Option Constant -Name SP_METADATAEP_FSTRING -Value '{0}/_layouts/15/metadata/json/1'
    #Get the default SPWeb from the on-premises farm if no $SharePointWeb parameter is specified.
    if ([String]::IsNullOrEmpty($SharePointWeb)) {
        $SharePointWeb = Get-SPSite | Select-Object -First 1 | Get-SPWeb | Select-Object -First 1 | % Url
    }

    #Configure the realm ID for local farm so that it matches the AAD realm.
    $ACSMetadataEndpoint = $AAD_METADATAEP_FSTRING -f $AADDomain
    $ACSMetadata = Invoke-RestMethod -Uri $ACSMetadataEndpoint
    $AADRealmId = $ACSMetadata.realm

    Set-SPAuthenticationRealm -ServiceContext $SharePointWeb -Realm $AADRealmId
    
    $LocalSTS = Get-SPSecurityTokenServiceConfig
    $LocalSTS.NameIdentifier = '{0}@{1}' -f $SP_APPPRINCIPALID,$AADRealmId
    $LocalSTS.Update()

    #Allow connections over HTTP if the switch is specified.
    if ($AllowOverHttp.IsPresent -and $AllowOverHttp -eq $True) {
        $serviceConfig = Get-SPSecurityTokenServiceConfig
        $serviceConfig.AllowOAuthOverHttp = $true
        $serviceConfig.AllowMetadataOverHttp = $true
        $serviceConfig.Update()
    }

    #Step 1: Set up the ACS proxy in the on-premises SharePoint farm. Remove the existing ACS proxy
    #if the switch is specified.
    if ($RemoveExistingACS.IsPresent -and $RemoveExistingACS -eq $True) {
        Get-SPServiceApplicationProxy | ? DisplayName -EQ $ACS_APPPROXY_NAME | Remove-SPServiceApplicationProxy -RemoveData -Confirm:$false
    }
    if (-not (Get-SPServiceApplicationProxy | ? DisplayName -EQ $ACS_APPPROXY_NAME)) {
        $AzureACSProxy = New-SPAzureAccessControlServiceApplicationProxy -Name $ACS_APPPROXY_NAME -MetadataServiceEndpointUri $ACSMetadataEndpoint -DefaultProxyGroup
    }

    #Remove the existing security token service if the switch is specified.
    if ($RemoveExistingSTS.IsPresent) {
        Get-SPTrustedSecurityTokenIssuer | ? Name -EQ $ACS_STS_NAME | Remove-SPTrustedSecurityTokenIssuer -Confirm:$false
    }
    if (-not (Get-SPTrustedSecurityTokenIssuer | ? DisplayName -EQ $ACS_STS_NAME)) {
        $AzureACSSTS = New-SPTrustedSecurityTokenIssuer -Name $ACS_STS_NAME -IsTrustBroker -MetadataEndPoint $ACSMetadataEndpoint
    }

    #Update the ACS Proxy for OAuth authentication.
    $ACSProxy = Get-SPServiceApplicationProxy | ? Name -EQ $ACS_APPPROXY_NAME
    $ACSProxy.DiscoveryConfiguration.SecurityTokenServiceName = $ACS_APPPRINCIPALID
    $ACSProxy.Update()

    #Retrieve the local STS signing key from JSON metadata.
    $SPMetadata = Invoke-RestMethod -Uri ($SP_METADATAEP_FSTRING -f $SharePointWeb)
    $SPSigningKey = $SPMetadata.keys | ? usage -EQ "Signing" | % keyValue
    $CertValue = $SPSigningKey.value
    
    #Connect to Office 365.
    Connect-MsolService -Credential $O365Credentials
    #Remove existing connection to an Office 365 SharePoint site if the switch is specified.
    if ($RemoveExistingAADCredentials.IsPresent -and $RemoveExistingAADCredentials -eq $true) {
        $msolserviceprincipal = Get-MsolServicePrincipal -AppPrincipalId $SP_APPPRINCIPALID
        [Guid[]] $ExistingKeyIds = Get-MsolServicePrincipalCredential -ObjectId $msolserviceprincipal.ObjectId -ReturnKeyValues $false | % {if ($_.Type -ne "Other") {$_.KeyId}}
        Remove-MsolServicePrincipalCredential -AppPrincipalId $SP_APPPRINCIPALID -KeyIds $ExistingKeyIds
    }
    #Step 2: Upload the local STS signing certificate
    New-MsolServicePrincipalCredential -AppPrincipalId $SP_APPPRINCIPALID -Type Asymmetric -Value $CertValue -Usage Verify

    #Step 3: Add the service principal name of the local web application, if necessary.
    $indexHostName = $SharePointWeb.IndexOf('://') + 3
    $HostName = $SharePointWeb.Substring($indexHostName)
    $NewSPN = '{0}/{1}' -f $SP_APPPRINCIPALID, $HostName
    $SPAppPrincipal = Get-MsolServicePrincipal -AppPrincipalId $SP_APPPRINCIPALID
    if ($SPAppPrincipal.ServicePrincipalNames -notcontains $NewSPN) {
        $SPAppPrincipal.ServicePrincipalNames.Add($NewSPN)
        Set-MsolServicePrincipal -AppPrincipalId $SPAppPrincipal.AppPrincipalId -ServicePrincipalNames $SPAppPrincipal.ServicePrincipalNames
    }

    #Remove the existing SharePoint Online proxy if the switch is specified.
    if ($RemoveExistingSPOProxy.IsPresent -and $RemoveExistingSPOProxy -eq $True) {
        Get-SPServiceApplicationProxy | ? DisplayName -EQ $SPO_MANAGEMENT_APPPROXY_NAME | Remove-SPServiceApplicationProxy -RemoveData -Confirm:$false
    }
    #Step 4: Add the SharePoint Online proxy
    if (-not (Get-SPServiceApplicationProxy | ? DisplayName -EQ $SPO_MANAGEMENT_APPPROXY_NAME)) {
        $spoproxy = New-SPOnlineApplicationPrincipalManagementServiceApplicationProxy -Name $SPO_MANAGEMENT_APPPROXY_NAME -OnlineTenantUri $SharePointOnlineUrl -DefaultProxyGroup
    }  
}
```


### Configurar o suplemento e o aplicativo da web do SharePoint para o Office Store
<a name="function"> </a>

Há uma etapa de configuração opcional que os administradores de farm devem tomar em ambientes de produção, se desejarem que os usuários possam instalar o provedor hospedado suplementos que usam ACS da Office Store. (Ela serve nenhuma finalidade do seu ambiente de desenvolvimento do SharePoint, a menos que você planeja instalar suplementos que usam ACS na Store em ambiente.) O seguinte cmdlet torna isso possível. Este código pode ser adicionado à função acima.
  
    
    

```

New-SPMarketplaceWebServiceApplicationProxy -Name "ApplicationIdentityDataWebServiceProxy" -ServiceEndpointUri "https://oauth.sellerdashboard.microsoft.com/ApplicationIdentityDataWebService.svc" -DefaultProxyGroup

```

Também é uma boa prática em aplicativos de web de SharePoint de produção para ativar o recurso de **suplementos que exigem internet opostas pontos de extremidade acessíveis** após as etapas de configuração acima foram concluídas. (Consulte as instruções abaixo.) Este recurso não realmente fazer nada. Ela serve simplesmente como um sinalizador que informa o Office Store que hospedado no provedor suplementos que usam ACS podem ser instalados em sites no aplicativo web do SharePoint.
  
    
    
Esse sistema pode ter implicações para o manifesto do suplemento de seu Suplemento do SharePoint. Se você planeja vender seu suplemento por meio da loja, é recomendável adicionar as seguintes **AppPrerequiste** à seção **AppPrerequisites** do manifesto suplemento:
  
    
    



```

<AppPrerequisite Type="Feature" ID="{7877bbf6-30f5-4f58-99d9-a0cc787c1300}" />
```

O efeito do pré-requisito é que quando os usuários estiver pesquisando o armazenamento de um farm do SharePoint local, seu suplemento estará acinzentada e desinstalado quando o pai aplicativo web do SharePoint não tem o recurso de **suplementos que exigem internet opostas pontos de extremidade acessíveis** ativado. Isso garante que você não receberá reclamações de clientes que instalar o suplemento para um site do SharePoint local e constatar que ele não funciona.
  
    
    
Há duas maneiras de ativar o recurso. A primeira é executar o seguinte cmdlet do PowerShell (que pode ser adicionado ao final da função acima) em qualquer servidor do SharePoint:
  
    
    



```
Enable-SPFeature -identity "7877bbf6-30f5-4f58-99d9-a0cc787c1300" -Url http://domain_of_the_SharePoint_web_application
```

A outra maneira de habilitar o recurso é executar as etapas a seguir na Administração Central:
  
    
    

1. Na **Administração Central do SharePoint**, navegue até **gerenciamento de aplicativo | Gerenciar aplicativos web**.
    
  
2. Na página **Gerenciar aplicativos da Web**, selecione o aplicativo web que você deseja alterar.
    
  
3. Na faixa de opções, clique em **Gerenciar recursos**.
    
  
4. Na lista de recursos, ao lado de **suplementos que exigem internet opostas pontos de extremidade acessíveis**, clique em **Ativar**.
    
  
5. Clique em **OK**.
    
  

  
    
    

### Configurar os aplicativos web do SharePoint adicionais no farm
<a name="function"> </a>

Se você tiver aplicativos web adicionais em seu farm do SharePoint e você deseja executar hospedado no provedor suplementos que usam confiança de ACS neles, você pode usar este script de Windows PowerShell (no SharePoint Management Shell ) para adicioná-los ao conjunto de nome principal de serviço.
  
    
    

```
$SPAppPrincipal = Get-MsolServicePrincipal -AppPrincipalId 00000003-0000-0ff1-ce00-000000000000
$id = "00000003-0000-0ff1-ce00-000000000000/"

Get-SPWebApplication | ForEach-Object {
    $hostName = $_.Url.substring($_.Url.indexof("//") + 2)
    $hostName = $hostName.Remove($hostName.Length - 1, 1)

    $NewSPN = $id + $hostName

    Write-Host "Adding SPN for" $NewSPN

    if ($SPAppPrincipal.ServicePrincipalNames -notcontains $NewSPN) {
       $SPAppPrincipal.ServicePrincipalNames.Add($NewSPN)
       Set-MsolServicePrincipal -AppPrincipalId $SPAppPrincipal.AppPrincipalId -ServicePrincipalNames $SPAppPrincipal.ServicePrincipalNames
    }
}

```


## Páginas seguintes
<a name="CreateApp"> </a>

Siga as etapas em  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md) para criar um simples "hello world" hospedado no provedor suplemento que usa ACS como o emissor do token.
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
