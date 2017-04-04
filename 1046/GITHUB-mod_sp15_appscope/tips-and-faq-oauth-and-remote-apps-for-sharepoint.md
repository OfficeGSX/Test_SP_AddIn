

# Dicas e perguntas freqüentes: OAuth e aplicativos remotos para o SharePoint
Este tópico agora é obsoleto. Consulte os links abaixo para encontrar onde o conteúdo agora está localizado.
 * **Aplica-se a:*** 
  
    
    


||
|:-----|
|**Neste artigo**          [Recuperar informações de aplicativo](#AppInfo)           [Arquivo AppManifest.xml](#Manifest)           [Arquivo Web.config](#Webconfig)           [URLs e Secure Sockets Layer (SSL)](#URL)           [Página de redirecionamento de aplicativo](#Redirect)           [Redirecionar URIs](#RedirectURI)           [Tokens OAuth](#Tokens)           [Escopos de solicitação de permissão e permissões](#Perm)           [Tipos de políticas de autorização do OAuth](#Policy)           [Depuração](#Debugging)           [Aplicativos de alta confiança (servidor-para-servidor de aplicativos) no local](#S2S)           [Problemas adicionais relacionadas a OAuth](#Misc)           [Recursos sociais](#Social)           [recursos adicionais](#AR)|
   

## Recuperar informações de aplicativo
<a name="AppInfo"> </a>

Este conteúdo foi movido para  [Recuperar informações de entidades suplemento e de registro do suplemento](register-sharepoint-add-ins-2013.md#Retrieve).
  
    
    

## Arquivo AppManifest.xml
<a name="Manifest"> </a>

Este conteúdo foi movido para:
  
    
    

-  [Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Referência de esquema para manifestos de aplicativos para SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  

### Qual é a URL no elemento < página inicial >?

Este conteúdo foi movido para:
  
    
    


  
    
    
>  [Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

  
    
    
>  [Elemento da página inicial](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)
    
  

### Quais são os elementos e atributos do arquivo AppManifest.xml?

Este conteúdo foi movido para  [referência de esquema para manifestos de aplicativos para SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
  
    
    

## Arquivo Web.config
<a name="Webconfig"> </a>

This content has been moved to  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md), especially the section  [Insira os valores de registro no Web. config e arquivos de AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

### Quais são as configurações de aplicativo do arquivo Web. config?

Este conteúdo foi movido para  [Insira os valores de registro no Web. config e arquivos de AppManifest.xml](register-sharepoint-add-ins-2013.md#EditConfigFiles).
  
    
    

## URLs e Secure Sockets Layer (SSL)
<a name="URL"> </a>

Este conteúdo foi movido para:
  
    
    

-  [Diversas SSL e erros relacionados a domínios em aplicativos de alta confiança](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Diversas SSL e erros relacionados a domínios no ACS apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Meu aplicativo web possui problemas falando de volta para o SharePoint. O que deve verificar?

Este conteúdo foi movido para:
  
    
    

-  [Diversas SSL e erros relacionados a domínios em aplicativos de alta confiança](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Diversas SSL e erros relacionados a domínios no ACS apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### O que URLs deve eu codificar em meu aplicativo para apontar para o meu servidor de nuvem?

Este conteúdo foi movido para:
  
    
    

-  [Diversas SSL e erros relacionados a domínios em aplicativos de alta confiança](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Diversas SSL e erros relacionados a domínios no ACS apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Posso registrar o alias CNAME ou a URL de fato subjacente que está hospedando o aplicativo?

Este conteúdo foi movido para:
  
    
    

-  [Diversas SSL e erros relacionados a domínios em aplicativos de alta confiança](troubleshooting-high-trust-sharepoint-add-ins.md#DomainRelatedErrors)
    
  
-  [Diversas SSL e erros relacionados a domínios no ACS apps](44ead3b9-75c3-4f68-b908-0af6197f1143.md#DomainRelatedErrors)
    
  

### Recebo o erro "a conexão subjacente foi fechada: não foi possível estabelecer relação de confiança para o canal seguro de SSL/TLS." O que deve fazer?

Este conteúdo foi movido para  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#ErrorConnectonClosed).
  
    
    

## Página de redirecionamento de aplicativo
<a name="Redirect"> </a>

Este conteúdo foi movido para  [Obter um novo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### O que devo usar a página de redirecionamento de aplicativo para?

Este conteúdo foi movido para  [Obter um novo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Como usar uma página de redirecionamento de aplicativo para obter o contexto de token?

Este conteúdo foi movido para  [Obter um novo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

### Como usar a página appredirect na URL?

Este conteúdo foi movido para  [Obter um novo token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#GetNewContextToken).
  
    
    

## Redirecionar URIs
<a name="RedirectURI"> </a>

This content has been moved to  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md).
  
    
    

### É necessário um URI de redirecionamento?

This content has been moved to  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md).
  
    
    

## Tokens OAuth
<a name="Tokens"> </a>

Este conteúdo foi movido para:
  
    
    

-  [Lidar com tokens de segurança no hospedado em provedor baixa confiança SharePoint suplementos](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md)
    
  
-  [Criar e usar os tokens de acesso no hospedado em provedor alta confiança SharePoint suplementos](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md)
    
  

### O que é um token de contexto?

Este conteúdo foi movido para  [Compreender a estrutura e tratamento de tokens de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).
  
    
    

### O que é um token de acesso?

Este conteúdo foi movido para  [Entender o tratamento de tokens de acesso](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens).
  
    
    

### O que é um token de atualização?

Este conteúdo foi movido para  [Entender o tratamento e o cache de tokens de atualização](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

### A cadeia de caracteres de token de contexto deve ser armazenada em um cookie para que ele possa ser usado para outras solicitações de página diretamente do aplicativo hospedado em provedor?

Este conteúdo foi movido para  [Cache de token de contexto ou partes dele](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).
  
    
    

### O que é o valor da chave de cache formado pelo? Como é exclusiva?

Este conteúdo foi movido para  [Entender a chave de cache](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey).
  
    
    

### Como faço para recuperar o token de contexto?

Este conteúdo foi movido para  [Compreender a estrutura e tratamento de tokens de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ContextTokens).
  
    
    

### Quais informações que contém o token de contexto?

Este conteúdo foi movido para  [Ver um exemplo de um token de contexto](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleContextToken).
  
    
    

### Quais informações que contém um token de acesso?

Este conteúdo foi movido para:
  
    
    

-  [Veja exemplos de tokens de acesso no sistema de autorização ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens)
    
  
-  [Ver um exemplo de um token de acesso no sistema de autorização de alta confiança](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md#Structure)
    
  

### Como calcular o tempo exato e data do valor do nbf e exp?

Este conteúdo foi movido para  [Trabalhar com valores de hora JWT](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#JWTtimes).
  
    
    

### Eu desejo proteger o svc para meus aplicativos de usuários que não são do SharePoint. Posso verificar legitimidade do usuário no ponto de entrada do aplicativo (Criando ClientContext), mas meu serviço WCF pode ser chamado por qualquer pessoa. Deve criar ClientContext de um token de contexto de cada chamada de método svc?

Este conteúdo foi movido para  [Use o token de contexto para limitar o acesso aos usuários do SharePoint](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToLimitAccess).
  
    
    

### Há algum problema manter AppContext (obtido com uma solicitação POST do SharePoint) como um campo de entrada oculto na página?

Este conteúdo foi movido para  [Cache de token de contexto ou partes dele](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheContextToken).
  
    
    

### Quanto tempo é um token de atualização válido?

Este conteúdo foi movido para  [Entender o tratamento e o cache de tokens de atualização](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

### Posso armazenar o token de acesso e URL de host em cookies para que possam ser usadas em outras solicitações de página. Mas o usuário executar uma quebra e o token de acesso expirou. O que deve fazer?

Este conteúdo foi movido para:
  
    
    

-  [Entender o tratamento de tokens de acesso](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#AccessTokens)
    
  
-  [Entender o tratamento e o cache de tokens de atualização](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens)
    
  

### Em qual cenário deve eu descartar um token de atualização ainda em vigor antigo que ainda é válido e usar um novo?

Este conteúdo foi movido para  [Entender o tratamento e o cache de tokens de atualização](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#RefreshTokens).
  
    
    

## Escopos de solicitação de permissão e permissões
<a name="Perm"> </a>

This content is in  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Quais são os escopos de solicitação de permissão e os direitos disponíveis para outros recursos, conteúdo de biblioteca e lista?

This content is now in  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Tipos de políticas de autorização do OAuth
<a name="Policy"> </a>

This content is in  [Tipos de política de suplemento autorização no SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### O que é a diferença entre a política somente app e o usuário + política app?

This content is in  [Tipos de política de suplemento autorização no SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

### Há uma maneira de conceder ou negar o direito de início de um aplicativo?

This content is in  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Depuração
<a name="Debugging"> </a>

Este conteúdo foi movido para:
  
    
    

-  [Solucionando problemas de alta confiança SharePoint Add-ins](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Solucionando problemas de suplementos de baixa confiança para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Usando o Fiddler

Este conteúdo foi movido para:
  
    
    

-  [Solucionando problemas de alta confiança SharePoint Add-ins](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Solucionando problemas de suplementos de baixa confiança para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

## Aplicativos de alta confiança (servidor-para-servidor de aplicativos) no local
<a name="S2S"> </a>

This content has been moved to  [Solucionando problemas de alta confiança SharePoint Add-ins](troubleshooting-high-trust-sharepoint-add-ins.md)
  
    
    

### Estou recebendo um erro não autorizado 401 durante a execução de um aplicativo altamente confiável. O que deve fazer?

This content has been moved to  [Solucionando problemas de alta confiança SharePoint Add-ins](troubleshooting-high-trust-sharepoint-add-ins.md).
  
    
    

### Como posso obter um contexto de token para um aplicativo altamente confiável?

This content has been moved to  [Criar e usar os tokens de acesso no hospedado em provedor alta confiança SharePoint suplementos](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md).
  
    
    

## Problemas adicionais relacionadas a OAuth
<a name="Misc"> </a>

Este conteúdo foi movido para:
  
    
    

-  [Solucionando problemas de alta confiança SharePoint Add-ins](troubleshooting-high-trust-sharepoint-add-ins.md)
    
  
-  [Solucionando problemas de suplementos de baixa confiança para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md)
    
  

### Ao tentar ler um arquivo usando o método HTTP DAV, recebo um erro. O que deve fazer?

This content has moved to  [Solucionando problemas de suplementos de baixa confiança para SharePoint 2013](44ead3b9-75c3-4f68-b908-0af6197f1143.md).
  
    
    

### Há uma maneira de encaminhar OAuth autorização para outros componentes em domínios diferentes, ou configurar OAuth para diversos URIs?

Sim. Consulte  [Encaminhar o token de acesso a sistemas de back-end](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ForwardTokenToBackend).
  
    
    

### É a constante de valor principal SharePoint 2013 ?

Sim. Consulte  [Veja exemplos de tokens de acesso no sistema de autorização ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (especialmente na tabela 1).
  
    
    

### É a Id do aplicativo e a constante secreta do aplicativo entre todos os locatários para um determinado aplicativo?

Yes, if the app is registered in the Painel do Vendedor; but if it is separately registered with each SharePoint tenancy or farm, it can have distinct IDs and secrets for each. In that case, it appears to be multiple apps to Microsoft Azure Access Control Service (ACS). See  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md).
  
    
    

### Os territórios são exclusivos?

Sim. Consulte  [Veja exemplos de tokens de acesso no sistema de autorização ACS](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#ExampleAccessTokens) (especialmente na tabela 1) e [Use o token de contexto para obter um token de acesso](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#UseContextTokenToGetAccessToken).
  
    
    

### Como desativar o requisito de HTTPS para OAuth durante o desenvolvimento?

Este conteúdo foi transferido para  [](44ead3b9-75c3-4f68-b908-0af6197f1143.md#TurnOffHTTPRequirement).
  
    
    

## Recursos sociais
<a name="Social"> </a>

Este conteúdo foi transferido para:
  
    
    

1.  [Recursos de colaboração e social no SharePoint 2013](http://msdn.microsoft.com/library/5060f676-9aaa-41fe-88ef-e862ee2e1c52%28Office.15%29.aspx)
    
  
2.  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  

### Como recuperar as propriedades e a identidade do usuário?

See  [Obter a identidade do usuário e propriedades no SharePoint 2013](get-user-identity-and-properties-in-sharepoint-2013.md).
  
    
    

### Qual é o uso para os diferentes recursos sociais e escopos de solicitação de permissão?

Esse conteúdo está em  [Introdução ao desenvolvimento de recursos sociais no SharePoint 2013](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms).
  
    
    

### Como obter o usuário propriedades de perfil de pessoas seguindo mim?

Este conteúdo foi movido para  [Como: recuperar propriedades de perfil de usuário usando o modelo de objeto de cliente .NET no SharePoint 2013](http://msdn.microsoft.com/library/236ebaf8-f92e-4192-9b51-0a9de0210885%28Office.15%29.aspx#SP15UserProfilescodeInApp).
  
    
    