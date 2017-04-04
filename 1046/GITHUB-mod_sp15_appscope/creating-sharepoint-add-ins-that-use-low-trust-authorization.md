---
title: Criando Add-ins do SharePoint que usam a autorização de baixa confiança
ms.prod: SHAREPOINT
ms.assetid: c33966ab-1515-407c-b1ac-8c653eab10dc
---


# Criando Add-ins do SharePoint que usam a autorização de baixa confiança
Saiba mais sobre o sistema de autorização de baixa confiança para Suplementos do SharePoint.
Componentes remotos em um Suplemento do SharePoint (ou aplicativo externo) podem obter autorização para recursos do SharePoint, passando um token de acesso para SharePoint com cada solicitação HTTP. Os componentes remota obtenham o token de acesso de uma conta de Microsoft Azure Access Control Service (ACS) que está associada à locação de Office 365 do cliente. Azure ACS atua como o servidor de autorização em uma transação  [OAuth 2.0](http://oauth.net/) , chamado de umfluxo, com SharePoint como o servidor de recurso e os componentes remotos como o cliente. Para as especificações do protocolo relacionados, consulte  [Web Authorization Protocol (oauth)](http://datatracker.ietf.org/doc/active/#oauth).
  
    
    

Hospedado em provedor Suplementos do SharePoint que usam o sistema de autorização de baixa confiança pode ser vendidos no Office Store e instalado em Microsoft SharePoint Online ou um farm no local SharePoint que tenha sido configurado para usar locação de Office 365 do cliente para estabelecer a confiança com Azure ACS. O cliente deve ter um inquilino Office 365 instalar Suplementos do SharePoint que usam o sistema de baixa confiança. No entanto, não é necessário para o cliente usar aluguel para qualquer outro fim. Para obter instruções sobre a vinculação de um farm no local para uma locação Office 365, consulte  [Usar um site do SharePoint do Office 365 para autorizar hospedado no provedor suplementos em um site do SharePoint local](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md).
## Registro com Azure ACS
<a name="Registration"> </a>

Para usar o sistema de baixa confiança, o Suplemento do SharePoint primeiro devem ser registrados com Azure ACS e com o serviço de gerenciamento de aplicativo do aluguel SharePoint farm ou SharePoint Online. (É chamado "App Management Service" porque Suplementos do SharePoint originalmente eram chamados de "aplicativos para SharePoint".) Para suplementos que são vendidos através do Office Store, registro do ACS é realizado na Painel do Vendedor e registro com o serviço é executado quando o suplemento está instalado. Para suplementos que são distribuídos no catálogo de suplemento de organização, o registro para o ACS e o serviço é realizado na página \\_Layouts\\15\\AppRegNew.aspx de quaisquer SharePoint locação ou farm onde o suplemento deve ser instalado. Externo, não - SharePoint, aplicativos que acessam SharePoint, também precisará ser registrado. Esta categoria inclui aplicativos Suplementos do Office, Windows Store, aplicativos web e aplicativos de dispositivo, como aplicativos do smartphone.
  
    
    

> **OBSERVAçãO**
> O registro requer que o aplicativo tem um domínio da Internet. Qualquer domínio existente pode ser usado para essa finalidade, mas você não pode ser 100% certas que qualquer domínio em que você não possui sempre existirá, portanto, um aplicativo web precisa ser parte de um aplicativo de dispositivo nativo, mesmo que o componente do aplicativo web não reproduzido nenhuma outra função que to ativar o registro. Para uma amostra de código avançado projetado dessa maneira, consulte  [provisionar os sites em lotes com o modelo de suplemento](http://code.msdn.microsoft.com/Provision-sites-in-batches-fcf31bc6).
  
    
    

Para obter mais informações sobre o registro, consulte  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md).
  
    
    

### Suplemento secreta expiração

O suplemento segredo deve ser substituído cada 12 meses. Para obter detalhes, consulte  [Substituir um segredo cliente expirando em um SharePoint Add-in](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md).
  
    
    

## Políticas de autorização
<a name="Policies"> </a>

Um Suplemento do SharePoint podem ser projetados para usar qualquer uma das duas políticas de autorização:
  
    
    

- **Usuário + suplemento política:** Suplementos que usam essa diretiva só poderá executar ações que tanto o add-in e o usuário tem permissão para fazer. Esta é a política padrão que é usada, a menos que o desenvolvedor leva etapas específicas para usar o outro.
    
  
- **Política somente app:** Suplementos que usam essa diretiva podem executar qualquer ação que ele tem permissão para fazer, mesmo se o usuário não tem permissão para a ação. O desenvolvedor deve solicitar que essa diretiva ser usado no manifesto do suplemento do add-in. A solicitação deve ser aprovada pelo usuário que instala o suplemento. Essa política é permitida para apenas hospedado em provedor Suplementos do SharePoint.
    
  
Para obter mais informações sobre políticas de autorização, consulte  [Tipos de política de suplemento autorização no SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Dois fluxos de tempo de execução do OAuth
<a name="Flows"> </a>

Cada vez que um hospedado em nuvem Suplemento do SharePoint ou um aplicativo externo que está acessando SharePoint for executado, um fluxo - uma série de interações entre o suplemento, SharePoint 2013, ACS e em alguns casos, o usuário final - ocorre. O resultado final do fluxo é que SharePoint recebe um token de acesso incluído em cada create, update, delete (CRUD) solicitação que o aplicativo faz SharePoint.
  
    
    
Há dois fluxos de OAuth principais usados pelo SharePoint. Uma é hospedado em nuvem Suplementos do SharePoint. O outro, chamado "mutantes", é para aplicativos em outras plataformas que acessam dados SharePoint.
  
    
    

- **Fluxo de Token de contexto:** O componente remoto do Suplemento do SharePoint usa o modelo de objeto do cliente SharePoint (CSOM) ou pontos de extremidade do REST para fazer chamadas para SharePoint. SharePoint solicita um token de contexto do ACS que ele pode enviar para o servidor remoto. O servidor remoto usa o token de contexto para solicitar um token de acesso do ACS. O servidor remoto, em seguida, usa o token de acesso para responder ao SharePoint. Para obter detalhes sobre esse fluxo de mensagens, consulte [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md).
    
  
- **Fluxo do código de autenticação:** um Suplemento do SharePoint tem as permissões necessárias para os recursos de SharePoint quando ele é instalado. Mas aplicativos que não são instalados no SharePoint devem solicitar permissões "ligado"dinamicamente, ou seja, cada vez que eles são executados. Não há nenhum token de contexto SharePoint este fluxo. Em vez disso, quando o suplemento é executado e tenta acessar SharePoint, SharePoint solicita ao usuário para conceder as permissões para o aplicativo que está solicitando. Quando o usuário concede as permissões, SharePoint obtém um código de autorização do ACS que ele passa para o aplicativo. O aplicativo usa o código para obter um token de acesso do ACS que ele pode usar para se comunicar com SharePoint. Para obter detalhes sobre esse fluxo de mensagens, consulte [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
    
  

## Solução de problemas de baixa confiança Suplementos do SharePoint
<a name="Trouble"> </a>

Este artigo fornece algumas diretrizes gerais de solução de problemas e informações sobre alguns problemas específicos com Suplementos do SharePoint que usam o sistema de autorização de baixa confiança.
  
    
    

### Use a ferramenta Fiddler

A  [ferramenta Fiddler](http://www.telerik.com/fiddler) de livre pode ser usado para capturar as solicitações HTTP enviadas pelo componente remoto do suplemento para SharePoint. Não há uma [extensão gratuita para a ferramenta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automaticamente os tokens de acesso nas solicitações.
  
    
    

### Desativar o requisito de HTTPS para OAuth durante o desenvolvimento
<a name="TurnOffHTTPRequirement"> </a>

OAuth requer SharePoint para executar o HTTPS (não apenas seu serviço, mas SharePoint muito). Isso pode obter no ausente quando você estiver desenvolvendo o add-in. Por exemplo, você pode receber uma mensagem de (proibido) 403 ao tentar fazer uma chamada para o SharePoint, quando estiver depurando o suplemento porque o suporte a SSL não está presente no "localhost" onde o add-in está sendo executado.
  
    
    
Você pode desativar o requisito de HTTPS durante o desenvolvimento usando os cmdlets a seguir Windows PowerShell.
  
    
    



```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $true
$serviceConfig.Update()

```

Para ativar o requisito de HTTPS novamente mais tarde, use os seguintes cmdlets Windows PowerShell.
  
    
    



```

$serviceConfig = Get-SPSecurityTokenServiceConfig
$serviceConfig.AllowOAuthOverHttp = $false
$serviceConfig.Update()

```


### Erros de autorização relacionadas ao domínio e diversos SSL
<a name="DomainRelatedErrors"> </a>

Uma incompatibilidade de nomes de domínio em arquivos de configuração e formulários de registro pode impedir que a autorização. Os quatro valores a seguintes deve ser exatamente o mesmo:
  
    
    

- O **suplemento do domínio** que é especificado quando o Suplemento do SharePoint é registrado em AppRegNew.aspx ou Painel do Vendedor.
    
  
- O domínio sob a qual o computador remoto certificado de segurança do aplicativo da web está registrado.
    
  
- A parte de domínio do valor **StartPage** no arquivo AppManifest.xml.
    
  
- A parte de domínio das URLs de qualquer receptores de evento especificados no AppManifest.xml.
    
  
Em conexão com esse ponto, observe o seguinte:
  
    
    

- Se o componente remoto de sua Suplemento do SharePoint estiver usando qualquer porta que não seja 443, você deve incluir explicitamente a porta como parte do domínio em todos os quatro lugares; Por exemplo,  `contoso.com:3333`. (Você deve usar o protocolo HTTPS para o qual a porta padrão é 443.)
    
  
- Se você criar um alias DNS CNAME para o seu aplicativo web remoto, use o CNAME alias para o valor de domínio em todos os quatro lugares. Por exemplo, se seu aplicativo está hospedado em contoso.cloudapp.net e criar um registro CNAME do contososoftware.com para ele, use contososoftware.com como o domínio.
    
  
- O domínio deve estar codificado no valor de **StartPage** (e nenhum URL do receptor de evento) do arquivo AppManifest.xml, antes do suplemento é empacotado. Se você usar o assistente **Publicar** no Visual Studio empacotar o suplemento, você será solicitado para o domínio e o Office Developer Tools for Visual Studio inserirá no valor **StartPage** para você (no lugar do token de `~remoteWebUrl` que é usado durante a depuração. Mas se você não estiver usando o assistente **Publicar** ou mesmo se não estiver, mas seu aplicativo web remoto é implantado no Azure, você deve manualmente substitua o token de domínio (e protocolo); Por exemplo, `https://contososoftware.com` ou `https://MyCloudVM:3333`.
    
  

### Erro "a conexão subjacente foi fechada: não foi possível estabelecer relação de confiança para o canal seguro de SSL/TLS."
<a name="ErrorConnectionClosed"> </a>

Esse erro é um problema de handshake SSL, não é um problema de OAuth. Certifique-se de que o certificado que você está usando é confiável pelo SharePoint e que o certificado corresponde ao nome de ponto de extremidade.
  
    
    

### Erros ao usar o método DAV HTTP para ler arquivos do SharePoint
<a name="ErrorConnectionClosed"> </a>

DAV HTTP não funciona com OAuth. Se você estiver usando o modelo de objeto do cliente SharePoint (CSOM), use o código a seguir para ler um arquivo.
  
    
    

```cs

File f = clientContext.Web.GetFileByServerRelativeUrl( url);
ClientResult<Stream> r = f.OpenBinaryStream();
clientContext.ExecuteQuery();

```


## Nesta seção
<a name="Trouble"> </a>

 [Usar um site do SharePoint do Office 365 para autorizar hospedado no provedor suplementos em um site do SharePoint local](use-an-office-365-sharepoint-site-to-authorize-provider-hosted-add-ins-on-an-on.md)
  
    
    
 [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md)
  
    
    
 [Substituir um segredo cliente expirando em um SharePoint Add-in](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md)
  
    
    

## Recursos adicionais
<a name="FileName_AdditionalResources"> </a>


-  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md)
    
  

