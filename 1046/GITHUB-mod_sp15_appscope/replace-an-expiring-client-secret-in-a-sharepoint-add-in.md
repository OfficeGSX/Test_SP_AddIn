---
title: Substituir um segredo cliente expirando em um SharePoint Add-in
ms.prod: SHAREPOINT
ms.assetid: 369d14f0-75c1-4383-8a2d-05b4030c44ea
---


# Substituir um segredo cliente expirando em um SharePoint Add-in
Saiba como adicionar um novo segredo de cliente para um Suplemento do SharePoint que está registrada com AppRegNew.aspx.
Senhas dos clientes para Suplementos do SharePoint que são registradas usando a página de AppRegNew.aspx expiram depois de um ano. Este artigo explica como adicionar um novo segredo para o suplemento, bem como criar um novo segredo de cliente que é válido por três anos.
  
    
    


> [!OBSERVAçãO]
> Este artigo é sobre Suplementos do SharePoint distribuído por meio de um catálogo de organização e registrados com a página AppRegNew.aspx. Se o suplemento estiver registrado no Painel do Vendedor, consulte  [Criar ou atualizar IDs de cliente e segredos no painel do vendedor](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx#bk_update).
  
    
    


## Pré-requisitos para atualizar um segredo de cliente

Antes de começar, verifique o seguinte:
  
    
    

-  [Assistente Microsoft Online Services entrar](http://www.microsoft.com/download/details.aspx?id=39267) está instalado no computador de desenvolvimento.
    
  
- Módulo do PowerShell ( [32 bits](http://go.microsoft.com/fwlink/p/?linkid=236298); do Microsoft Online Services  [64 bits](http://go.microsoft.com/fwlink/p/?linkid=236297)) está instalado no computador de desenvolvimento.
    
  
- Você é um administrador de locatário para o locatário Office 365 (ou um administrador de farm no farm) onde o suplemento foi registrado com a página AppRegNew.aspx.
    
  

## Descubra as datas de vencimento da Suplementos do SharePoint instalado aluguel Office 365


  
    
    

1. Abra Windows PowerShell e execute o seguinte cmdlet:
    
  ```
  
Connect-MsolService

  ```

2. No prompt de logon, insira o administrador de locatários (ou administrador de farm) as credenciais para o Office 365 aluguel ou farm onde o suplemento foi registrado com AppRegNew.aspx.
    
  
3. Gere um relatório que lista cada suplemento e a data em que seu segredo expira com as seguintes linhas. Observe o seguinte sobre este código:
    
  - Primeiro, ele filtra os da Microsoft aplicativos, suplementos ainda em desenvolvimento (e um tipo de substituídos do suplemento que foi chamada autohosted).
    
  
  - Do restante, ele filtra os suplementos que usam assimétricas chaves, como fluxos de trabalho e os suplementos não são do SharePoint.
    
  

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

4. Abra o arquivo C:\\temp\\appsec.txt para ver o relatório. Deixe a janela de Windows PowerShell aberto para o próximo procedimento, se qualquer um dos segredos está próximo vencimento.
    
  

## Gerar um novo segredo


  
    
    

1. Crie uma variável de ID do cliente com a seguinte linha, usando a ID do cliente do Suplemento do SharePoint como o parâmetro.
    
  ```
  
$clientId = 'client id of the add-in'

  ```

2. Gere um novo segredo de cliente com as seguintes linhas:
    
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

3. O novo segredo de cliente aparecerão no console do Windows PowerShell. Copiá-lo para um arquivo de texto. Você pode usá-lo no próximo procedimento.
    
  

> [!DICA]
> Por padrão, o suplemento segredo dura um ano. Você pode definir isso um endereço mais curto ou mais (até no máximo de 3 anos) usando o parâmetro **-EndDate** nas três chamadas do cmdlet **New-MsolServicePrincipalCredential**. O valor do parâmetro deve ser um objeto de [DateTime](http://msdn2.microsoft.com/PT-BR/library/03ybds8y) definido como não tenha mais de três anos de **DateTime.Now**.
  
    
    


## Atualizar o aplicativo web remoto no Visual Studio para usar o novo segredo


> [!IMPORTANTE]
> Se seu suplemento foi originalmente criado com uma versão de pré-lançamento do Microsoft Office Developer Tools for Visual Studio, ele pode conter uma versão desatualizada do arquivo TokenHelper.cs (ou. vb). Se o arquivo não contiver a cadeia de caracteres "secondaryClientSecret", ele está desatualizado e deve ser substituído antes de atualizar o aplicativo da web com um novo segredo. Para obter uma cópia de uma versão do arquivo, você precisa Visual Studio 2012 ou posterior. Crie um novo projeto de Suplemento do SharePoint no Visual Studio. Copie o arquivo de TokenHelper dela para o projeto de aplicativo web do seu Suplemento do SharePoint.
  
    
    


1. Abra o projeto Suplemento do SharePoint no Visual Studio e abra o arquivo Web. config para o projeto de aplicativo web. Na seção **appSettings**, há teclas para o ID do cliente e o segredo de cliente. A seguir é um exemplo:
    
  ```XML
  
<appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>

  ```

2. Altere o nome da chave **ClientSecret** para "SecondaryClientSecret", conforme mostrado no exemplo a seguir:
    
  ```XML
  
<add key="SecondaryClientSecret" value="your old secret here" />
  ```

3. Adicionar uma nova chave de **ClientSecret** e dar a ele seu novo segredo de cliente. Sua marcação agora deve parecer com o seguinte:
    
  ```XML
  <appSettings>
  <add key="ClientId" value="your client id here" />
  <add key="ClientSecret" value="your new secret here" />
  <add key="SecondaryClientSecret" value="your old secret here" />
     ... other settings may be here ...
</appSettings>
  ```

4. Se você alterou para um novo arquivo de TokenHelper, recrie o projeto.
    
  
5. Republicar o aplicativo da web.
    
  

## Criar um segredo de cliente que é válido para três anos

Para senhas dos clientes expirado, primeiro você deve excluir todas as senhas expiradas para um determinado **clientId**. Depois que você crie um novo com o PowerShell MSO, aguarde pelo menos 24 horas e testar o aplicativo com a nova chave **clientId** e **ClientSecret**.
  
    
    

1. Conectar-se a MSOnline usando o usuário de administração de locatário com o abaixo marcação usando o SharePoint 2013 Windows PowerShell.
    
  ```
  
import-module MSOnline
$msolcred = get-credential
connect-msolservice -credential $msolcred

  ```

2. Obter **ServicePrincipals** e chaves. Impressão **$keys** retorna três registros. Substitua cada **KeyId** no *KeyId1*  , *KeyId2*  e *KeyId3*  . Você também verá a **EndDate** de cada chave. Confirme se sua expirada aparece chave lá.
    
    **Observação:** O **clientId** precisa corresponder sua expirado **clientId**. Recomenda-se para excluir todas as chaves, ambas expirou e não estão vencidos, para este **clientId**.
    


  ```
  
$clientId = "27c5b286-62a6-45c7-beda-abbaea6eecf2"
$keys = Get-MsolServicePrincipalCredential -AppPrincipalId $clientId
Remove-MsolServicePrincipalCredential -KeyIds @("KeyId1"," KeyId2"," KeyId3") -AppPrincipalId $clientId 

  ```

3. Gere um novo **ClientSecret** para este **clientID**. Ele usa a mesma **clientId** conforme definido na etapa acima. O novo **ClientSecret** é válida por 3 anos.
    
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

4. Copie a saída de **$newClientSecret**.
    
  
5. Substitua o **Web. config** este **ClientId** e **ClientSecret**. Você não precisa **SecondaryClientSecret** configurações do aplicativo.
    
  
6. Aguarde pelo menos 24 horas para propagar **ClientSecret** para SharePoint Office (SPO).
    
  

## Ver também


#### Outros recursos


  
    
    
 [Provedor hospedado aplicativo falha em SPO](http://blogs.technet.com/b/sharepointdevelopersupport/archive/2015/03/11/provider-hosted-app-fails-on-spo.aspx)
