---
title: Solucionando problemas de alta confiança SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: f464c89e-f318-4051-8589-07cc6b34241f
---


# Solucionando problemas de alta confiança SharePoint Add-ins
Obter ajuda com problemas de desenvolvimento de alta confiança Suplementos do SharePoint.
Este artigo descreve a ferramenta Fiddler e também fornece algumas diretrizes para resolver alguns problemas específicos.
  
    
    


## Use a ferramenta Fiddler

A  [ferramenta Fiddler](http://www.telerik.com/fiddler) de livre pode ser usado para capturar as solicitações HTTP enviadas pelo componente remoto do suplemento para SharePoint. Não há uma [extensão gratuita para a ferramenta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automaticamente os tokens de acesso nas solicitações.
  
    
    
Depois que você instalou o Fiddler no servidor de aplicativos web, adicione a seguinte marcação ao seu arquivo Web. config para fazer com que as solicitações de seu aplicativo web remoto passem por esse proxy. Dessa forma, você pode capturar um rastreamento Fiddler e verifica a resposta completa do SharePoint quando você receber um erro.
  
    
    

> **OBSERVAçãO**
> Certifique-se de que você remova essa marcação se você não estiver executando o Fiddler. Se você não remover a marcação, seu suplemento não conseguirá fazer solicitações HTTP.
  
    
    




```XML

<system.net>
  <defaultProxy>
    <proxy usesystemdefault="False" bypassonlocal="False" proxyaddress="http://127.0.0.1:8888" />
  </defaultProxy>
</system.net>

```

Depois de ter Fiddler instalado, você também pode verificar os cabeçalhos de resposta de SharePoint, que incluirá uma solicitação de GUID. Essa solicitação GUID é uma ID de correlação procure nos logs para encontrar qualquer um log de erros associados a essa solicitação.
  
    
    

## Erro de não autorizado 401
<a name="UnauthorizedException"> </a>

Várias coisas podem causar um erro de **401 Unauthorized** quando o suplemento de alta confiança primeiro acessa SharePoint. Se você estiver usando o modelo de objeto do cliente (CSOM), o erro pode ter a seguinte aparência:
  
    
    

```cs

[WebException: The remote server returned an error: (401) Unauthorized.]
   System.Net.HttpWebRequest.GetResponse() +8515936
   Microsoft.SharePoint.Client.SPWebRequestExecutor.Execute() +178
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQueryToServer(ChunkStringBuilder sb) +1427
   Microsoft.SharePoint.Client.ClientRequest.ExecuteQuery() +270
   Microsoft.SharePoint.Client.ClientRuntimeContext.ExecuteQuery() +146
   Microsoft.SharePoint.Client.ClientContext.ExecuteQuery() +666
   S2STestWeb.Default.Page_Load(Object sender, EventArgs e) in c:\\MyFiles\\HightrustTest\\HightrustTestWeb\\Default.aspx.cs:28
   System.Web.UI.Control.LoadRecursive() +71
   System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +3178
```

Se você estiver usando o arquivo de TokenHelper e a identidade do Windows, o código que dispara a exceção é semelhante ao seguinte:
  
    
    



```cs

ClientContext clientContext =
    TokenHelper.GetS2SClientContextWithWindowsIdentity(sharepointUrl, Request.LogonUserIdentity); 
clientContext.Load(clientContext.Web);
clientContext.ExecuteQuery();
```

Sua primeira etapa ao solucionar o problema é usar o depurador Visual Studio para verificar que o token de acesso e o objeto **ClientContext** são construídas com êxito. Se eles forem, investigue as seguintes possibilidades:
  
    
    
 **Possíveis problemas e resolução:**
  
    
    

- Não há nenhum perfil de usuário criado para o usuário que está acessando o aplicativo web remoto. Crie o perfil de usuário.
    
  
- Seu suplemento não tem permissão para o recurso que você está tentando acessar. Abra o SharePoint Management Shell e execute a que o seguinte cmdlet Windows PowerShell. A variável  `$web` é um site de SharePoint que você está tentando obter acesso a e `$appPrincipal`) é a ID do suplemento. Para obter mais informações, consulte  [Set-SPAppPrincipalPermission](http://technet.microsoft.com/en-us/library/jj219714%28v=office.15%29.aspx).
    
  ```
  
Set-SPAppPrincipalPermission -Site $web -AppPrincipal $appPrincipal -Scope Site -Right FullControl
  ```

- Seu aplicativo web está aceitando solicitações anônimas. Isso significa que não há uma identidade de usuário real no token de acesso. Certifique-se de que o acesso anônimo foi desabilitado para o diretório raiz do seu aplicativo web remoto no IIS. Você também pode verificar isso depuração do seu aplicativo web remoto e verificando o valor de **Request.LogonUserIdentity** no arquivo default.aspx.cs (ou. vb) para garantir que não é um usuário anônimo.
    
  
- O certificado digital não foi adicionado ao repositório de certificados confiáveis. Certifique-se de que você ter seguido os procedimentos em  [Embalar e publicar Add-ins do SharePoint de alta confiança](package-and-publish-high-trust-sharepoint-add-ins.md).
    
  

## Erros de autorização relacionadas ao domínio e diversos SSL
<a name="DomainRelatedErrors"> </a>

Uma incompatibilidade de nomes de domínio em arquivos de configuração e formulários de registro pode impedir que a autorização. Os quatro valores a seguintes deve ser exatamente o mesmo:
  
    
    

- O **suplemento do domínio** que é especificado quando o Suplemento do SharePoint está registrada no AppRegNew.aspx.
    
  
- O domínio sob a qual o computador remoto certificado de segurança do aplicativo da web está registrado.
    
  
- A parte de domínio do valor **StartPage** no arquivo AppManifest.xml.
    
  
- A parte de domínio das URLs de qualquer receptores de evento especificados no AppManifest.xml.
    
  
Em conexão com esse ponto, observe o seguinte:
  
    
    

- Se o componente remoto de sua Suplemento do SharePoint estiver usando qualquer porta que não seja 443, você deve incluir explicitamente a porta como parte do domínio em todos os quatro lugares; Por exemplo,  `MarketingServer:3333`. (Você deve usar o protocolo HTTPS para o qual a porta padrão é 443.)
    
  
- O domínio deve estar codificado no valor de **StartPage** (e nenhum URL do receptor de evento) do arquivo AppManifest.xml, antes do suplemento é empacotado. Se você usar o assistente **Publicar** no Visual Studio empacotar o suplemento, você será solicitado para o domínio e o Office Developer Tools for Visual Studio inserirá no valor **StartPage** para você (no lugar do token de `~remoteWebUrl` que é usado durante a depuração. Porém, se você não estiver usando o Assistente de **Publicar**, você deve substituir manualmente o token com o domínio (e o protocolo); Por exemplo,  `https://MarketingServer` ou `https://MarketingServer:3333`.
    
  

## Mensagem de erro de tempo de execução informando que não há nenhum certificado com esse número de série
<a name="DomainRelatedErrors"> </a>

Se você tiver certeza de que tenha o número de série do certificado correto no Web. config e você pode ver o certificado no **Armazenamento de certificados do Windows**, em seguida, pode haver um caractere extra oculto no número de série em Web. config. Isso acontecerá se o número serial é copy'n' colados a partir do **Console de gerenciamento Microsoft**. Excluir o valor de número de série inteira do Web. config e *manualmente*  -a novamente.
  
    
    

