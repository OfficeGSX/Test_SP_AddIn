---
title: Registrar o SharePoint 2013 de suplementos
ms.prod: SHAREPOINT
ms.assetid: be41a5dc-2af9-4fd9-bf4e-ad6dfa849524
---



# Registrar o SharePoint 2013 de suplementos
Registre sua Suplementos do SharePoint no Azure ACS usando Visual Studio, o Painel do Vendedor ou uma página de AppRegNew.aspx e recuperar informações de registro.
Para os componentes remotos de um provedor hospedado Suplemento do SharePoint interagir com SharePoint usando OAuth, o suplemento deve primeiro registrar com o serviço baseado em nuvem do  [Azure ACS](https://msdn.microsoft.com/en-us/library/azure/gg429788.aspx) e SharePoint App Management Service do aluguel ou farm. (É chamado "App Management Service" porque Suplementos do SharePoint originalmente eram chamados de "aplicativos para SharePoint".)
  
    
    


> **OBSERVAçãO**
> Isso não é necessário para hospedado no SharePoint suplementos.
  
    
    


Para registrar seu suplemento com Azure ACS, você deve especificar as seguintes informações:
  
    
    


- Um GUID para o suplemento chamado uma ID de cliente.
    
  
- Uma senha para o suplemento chamado um segredo do cliente.
    
  
- Um nome de exibição do add-in que é usado na página consentimento onde o usuário é solicitado para confiar o add-in.
    
  
- Uma URL para o domínio onde o suplemento remoto está hospedado.
    
  
- Uma URL de redirecionamento.
    
  
Após registrar o suplemento, ela tem uma identidade de suplemento e é uma  *entidade de segurança*  , conhecido como umsuplemento principal. Quando você instala o add-in, administradores SharePoint podem recuperar informações sobre esse suplemento entidade específica.Quando um usuário primeiro concede um permissões add-in para acessar os recursos de SharePoint (que podem ocorrer no tempo de execução, dependendo do design do aplicativo ou de instalação), o SharePoint obtém informações sobre o suplemento de Azure ACS. SharePoint, em seguida, armazena essas informações no banco de dados serviço de gerenciamento de aplicativo do farm ou Locatário SharePoint. O segredo do cliente é armazenado somente com Azure ACS. SharePoint nunca sabe segredo do suplemento. O serviço de banco de dados de conteúdo e outros componentes, como o serviço de perfil de usuário, podem obter o nome para exibição e o serviço compartilhado de outras informações básicas sobre o suplemento diretamente a partir do gerenciamento de aplicativo. Para obter mais informações, consulte  [Recuperar informações de entidades suplemento e de registro do suplemento](register-sharepoint-add-ins-2013.md#Retrieve) neste artigo.
> **OBSERVAçãO**
> Este artigo pressupõe que você está familiarizado com os conceitos básicos e os princípios em que o OAuth 2.0 Framework. Para obter mais informações, consulte  [OAuth.net](http://oauth.net/) e [Protocolo de autorização de Web (oauth)](http://datatracker.ietf.org/doc/active/).
  
    
    


## Registre sua Suplemento do SharePoint no Azure ACS

Você pode registrar o suplemento em uma destas três formas, dependendo de onde você está no ciclo de vida de desenvolvimento do suplemento, a arquitetura do seu suplemento, e como você planeja comercializá-lo.
  
    
    


|**Método de registro**|**Detalhes**|
|:-----|:-----|
|Use Visual Studio e Microsoft Office Developer Tools for Visual Studio para criar uma identidade de suplemento temporária. <br/> |O Assistente de Office Developer Tools for Visual Studio cria um registro temporário para seu suplemento com ACS e o serviço de gerenciamento de aplicativo do seu site de teste de SharePoint. Quando você executa o suplemento de Visual Studio (F5), essa identidade é usada. As ferramentas também inserir a ID de cliente e o segredo na Web. config e AppManifest.xml arquivos. <br/> Quando você estiver pronto para publicar seu suplemento, você pode usar o Visual Studio publicar Assistente para ir até o Painel do Vendedor registrá-lo. Se você não é marketing sua Suplemento do SharePoint no Office Store, use AppRegNew.aspx registrá-lo. (Etapas exatas estão abaixo). <br/> > **OBSERVAçãO**> Se seu suplemento solicitar permissão para acessar os recursos de SharePoint dinamicamente em tempo de execução, em vez de na instalação do suplemento, você não pode usar Visual Studio para criar o suplemento de identidades.          |
|Registre o suplemento por meio do Painel do Vendedor. <br/> |Se você vai usar seu suplemento em mais de um inquilino de SharePoint ou farm, use o Painel do Vendedor para registrar o suplemento, independentemente de você irá mercado-lo em tempo de Office Store ou torná-lo disponível por meio do catálogo do suplemento. Quando você registra na Painel do Vendedor, você pode criar seu suplemento com uma arquitetura de multilocatário sem exigir que os administradores de Inquilino ou farm registrá-lo separadamente. Além disso, se você pretende publicar seu suplemento no Office Store, você precisará usar o Painel do Vendedor para registrar seu suplemento. Você não precisa usar o repositório para publicar um suplemento que é registrado com o Painel do Vendedor. <br/> Para obter mais informações, consulte  [Criar ou atualizar IDs de cliente e segredos no painel do vendedor](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx). <br/> |
|Use a página AppRegNew.aspx. <br/> |Use o formulário AppRegNew para registrar seu Suplemento do SharePoint, se você pretende usar o suplemento somente em um locatário ou farm. Por exemplo, se você estiver criando suplementos para uma única organização, e você vai distribuí-las por meio do catálogo de suplemento de organização, você pode usar a página de AppRegNew.aspx de qualquer site em um locatário ou farm para registrar o suplemento. <br/> Não será possível publicar um suplemento que é registrado com AppRegNew.aspx para o Office Store. Para suplementos que são publicados para o Office Store, você deve obter uma identidade do Painel do Vendedor. <br/> |
   

### Registrar usando AppRegNew.aspx


1. Navegue até  `http://` *< SharePointWebsite >*  `/_layouts/15/AppRegNew.aspx` no farm ou locatário.
    
   **Formulário de página AppRegNew**

  

     ![O formulário na página de Novo Reg do Aplicativo com caixas de ID do cliente, segredo do cliente, título, domínio de aplicativo, amp, redirecionamento de URL. Os botões com nome "gerar" estão ao lado das duas primeiras. No canto estão os botões Criar e Cancelar.](images/9a38d876-2189-418c-9314-ae493a4cab61.PNG)
  

  

  
2. Insira valores para os campos de formulário follow:
    
  - **ID do suplemento** - também conhecida como ID do cliente, é um GUID que pode ser gerado (quando você escolhe **Gerar** ) ou colados em AppRegNew.aspx. O valor deve ser exclusivo para cada suplemento e *deve estar em minúsculas*  .
    
  
  - **Suplemento segredo** - também conhecido como o segredo do cliente, uma cadeia de caracteres opaca. Ele é gerado na página AppRegNew.aspx usando o botão **Gerar**. A seguir está um exemplo de um suplemento segredo: **xvVpG0AgVIJfch6ldu4dLUlcZyysmGqBRbpFDu6AfJw =**.
    
    > **IMPORTANTE**
      > Suplemento segredos expiram. Se você registrar o suplemento no Painel do Vendedor, você pode definir a expiração para até três anos. No painel, você também pode adicionar novos segredos quando os antigos atingem suas datas de vencimento. O novo segredo será habilitado em todas as instâncias do add-in. Se você registrar o suplemento com AppRegNew.aspx, o segredo expira em um ano. Para obter detalhes, consulte  [Substituir um segredo cliente expirando em um SharePoint Add-in](replace-an-expiring-client-secret-in-a-sharepoint-add-in.md).
  - **Título** - um título amigável; Por exemplo,Contoso foto impressão suplemento. Os usuários são solicitados a conceder ou negar o suplemento as permissões que está solicitando o add-in. Esse título aparece como o nome do add-in no prompt de consentimento.
    
  
  - **Suplemento de domínio** - o nome do host do componente remoto do Suplemento do SharePoint. Se o aplicativo remoto não está usando a porta 443, o suplemento de domínio também deve incluir o número da porta. O domínio de suplemento deve coincidir com as associações de URL que você pode usar para seu aplicativo web. Não inclua protocolo ("https:") ou caracteres "/" nesse valor. Se seu host do aplicativo web está usando um alias CNAME DNS, use o alias. Alguns exemplos:
    
  - www.contoso.com:3333
    
  
  - www.fabrikam.com
    
  
  - **Redirecionar URI:** -o ponto de extremidade em seu aplicativo remoto ou o serviço ao qual o ACS envia um código de autenticação. Rigor, Suplementos do SharePoint não use esse valor. O redirecionamento de URI é necessário para aplicativos da web que são iniciados fora SharePoint e que usam o [fluxo de código de autenticação](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) para obter acesso autorizado aos dados do SharePoint. O URI de redirecionar é ignorado para true Suplementos do SharePoint (que são iniciados a partir SharePoint e usar o [fluxo de Token de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)). O URI de redirecionar geralmente é a mesma página, o método do controlador, o método de serviço web que solicitou o código de autenticação do ACS, mas pode ser um ponto de extremidade diferente. O ponto de extremidade deve ter lógica que obtém o código de autorização de resposta HTTP que é enviada pelo ACS e, em seguida, usa esse código para solicitar um acesso e atualizar token. Para obter mais informações, consulte  [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md). O formulário requer que você digitar um valor válido mesmo para true Suplementos do SharePoint, embora não seja usado.
    
    O valor deve ser uma URL completa do ponto de extremidade incluindo o protocolo,  *que deve ser HTTPS*  . Por exemplo:
    
  - https://www.contoso.com/Default.aspx
    
  
  - https://www.fabrikam.com/RedirectAccept.aspx
    
  
  - https://www.northwindtraders.com/home/index
    
  
  - https://adventureworks.com/vacationdata.svc
    
  
3. Escolha **criar** no formulário. A página será recarregar e mostrar uma confirmação de que os valores digitados. Tornar um registro desses valores em um formulário que é eay para copiar e colar. Você precisará inserir os valores em Web. config e em arquivos de AppManifest.xml ou no assistente **Publicar**Visual Studio.
    
  
Independentemente de como você registrar sua Suplemento do SharePoint, quando estiver pronto para implantar o suplemento para o preparo ou de produção, você precisará  [Insira os valores de registro no Web. config e arquivos de AppManifest.xml](#EditConfigFiles). Se você estiver usando o Visual Studio, o Microsoft Office Developer Tools for Visual Studio fazer essa configuração para você.
  
    
    

## Insira os valores de registro no Web. config e arquivos de AppManifest.xml
<a name="EditConfigFiles"> </a>

Antes de empacotar as Suplemento do SharePoint e antes de implantar seus componentes remotos, insira alguns dos valores de registro a AppManifest.xml e o arquivo Web. config.
  
    
    

> **DICA**
> Se você publicar seu Suplemento do SharePoint usando o Visual Studio Assistente de publicação, Visual Studio solicitará uma ID de cliente e o segredo do cliente durante o processo de publicação e ele colocará as informações nos locais corretos para você.
  
    
    


1. No arquivo Web. config em seu projeto Visual Studio, insira o valor de ID suplemento como o valor de **ClientId** (substituindo o valor temporário que as ferramentas inseridas).
    
    > **IMPORTANTE**
      > Todas as letras no cliente da ID do GUID devem ser minúsculas.

    Este é um exemplo:
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
   .  .  .
</appSettings>
  ```

2. Insira o valor de secreta suplemento como o valor de **ClientSecret** (substituindo o valor temporário que as ferramentas inseridas).
    
    O exemplo a seguir é um exemplo de como os valores são usados no arquivo Web. config de um aplicativo web.
    


  ```XML
  
<appSettings>
  <add key="ClientId" value="a044e184-7de2-4d05-aacf-52118008c44e " />
  <add key="ClientSecret" value="l0z/8TzWN0yQBzMBSEZtYts2Vt3Eo/oE3rfCdPaogKQ= " />
</appSettings>
  ```

3. No arquivo AppManifest.xml em seu projeto Visual Studio, insira o valor de ID suplemento como o valor de **ClientId**, *com letras minúsculas*  .
    
    > **OBSERVAçãO**
      > O manifesto do suplemento não se aplica a aplicativos web que a solicitação de permissão para acessar os recursos SharePoint dinamicamente. Eles não são realmente "Suplementos do SharePoint ". Eles não são instalados no SharePoint e não têm um manifesto do suplemento. Para obter mais informações, consulte  [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md).

    O exemplo a seguir mostra como o valor de **ClientId** é usado no arquivo AppManifest.xml.
    


  ```XML
  
<AppPrincipal>
  <RemoteWebApplication ClientId="a044e184-7de2-4d05-aacf-52118008c44e "/>
</AppPrincipal>
  ```

4. O Office Developer Tools for Visual Studio usar o token  `~remoteAppUrl` no elemento **StartPage**. (Por exemplo, `<StartPage>~remoteAppUrl/Pages/Default.aspx?{StandardTokens}</StartPage>`.) Este token resolve para a URL do componente remoto se você estiver usando o assistente **Publicar** no Visual Studio. Se você não usar o Assistente (ou se você estiver publicando o componente remoto no Azure, mas você fazer), você precisará substituir manualmente o token com o valor de **suplemento do domínio** que você usou ao registrar o suplemento. Ela deve ser *exatamente*  o mesmo valor, incluindo o número da porta, se houver, exceto que você incluir também o protocolo HTTPS. O exemplo a seguir é um exemplo.
    
  ```XML
  
<StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
  ```

5. Considere usar o mesmo valor para o elemento **Title** no arquivo AppManifest.xml usado no campo **título** na AppRegNew.aspx. O valor do elemento **Title** é o nome do add-in que os usuários veem depois que ele é instalado. Talvez seja confuso aos usuários para o add-in ter um nome diferente na caixa de diálogo consentimento que ele tem no SharePoint da interface do usuário.
    
    O exemplo a seguir mostra esses valores no manifesto do suplemento.
    


  ```XML
  <Properties>
  <Title>Contoso photo printing app</Title>
  <StartPage>https://www.contoso.com/Pages/Default.aspx?{StandardTokens}</StartPage>
</Properties>
  ```


## Usar a URL de redirecionamento em um suplemento que solicita permissões dinamicamente
<a name="UseRedirectUrl"> </a>

Se seu aplicativo web é iniciado a partir do SharePoint externo (e, portanto, não é uma verdadeira Suplemento do SharePoint ), ele deve ser desenvolvido para solicitar permissões do SharePoint em tempo de execução. Ele deve ter o código que usa o redirecionamento URI, junto com outras informações, para obter um token de acesso do ACS. Encontre o lugar onde esse URI é definido e use o valor  *exato*  que você usou para o campo **Redirecionar URI** AppRegNew.aspx ou o Painel do Vendedor. Isso pode estar em um arquivo de código ou um arquivo de configuração.
  
    
    

## Recuperar informações de entidades suplemento e de registro do suplemento
<a name="Retrieve"> </a>

É possível recuperar informações de registro do suplemento e o suplemento principais informações para os suplementos que você instalou ou registrado no SharePoint.
  
    
    
Para consultar informações de inscrição para um suplemento que você registrou, vá para  `http://` *< SharePointWebsite >*  `/_layouts/15/AppInv.aspx`.
  
    
    
Para fazer uma pesquisa, você precisará se lembrar a ID de cliente (também conhecido como o suplemento ID) que foi usada para registrar o suplemento. A pesquisa retorna as seguintes informações para uma ID de cliente específico:
  
    
    

- Título
    
  
- Suplemento do domínio
    
  
- Redirecionar URL (Isso é o mesmo que o URI de redirecionamento.)
    
  
A pesquisa não retorna o valor de secreta add-in.
  
    
    
Para ver uma lista de entidades de suplemento registradas, vá para:
  
    
    
 `http://` *<SharePointWebsite>*  `/_layouts/15/AppPrincipals.aspx`
  
    
    

## Recursos adicionais
<a name="AR"> </a>


-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Três sistemas de autorização para o SharePoint Add-ins](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
