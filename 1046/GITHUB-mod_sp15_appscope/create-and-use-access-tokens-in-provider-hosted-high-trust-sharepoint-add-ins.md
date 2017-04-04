---
title: Criar e usar os tokens de acesso no hospedado em provedor alta confiança SharePoint suplementos
ms.prod: SHAREPOINT
ms.assetid: cb3264d4-41a6-498f-a408-75b077566051
---


# Criar e usar os tokens de acesso no hospedado em provedor alta confiança SharePoint suplementos
Saiba mais sobre a função de tokens de acesso no SharePoint de alta confiança Add-ins e o funcionamento do seu código com eles.
> **IMPORTANTE**
> **Neste artigo é totalmente sobre o uso de tokens de acesso no sistema de autorização de alta confiança, não no sistema do ACS.** Para obter informações sobre o usuário de tokens de segurança no sistema ACS, consulte [Lidar com tokens de segurança no hospedado em provedor baixa confiança SharePoint suplementos](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md).
  
    
    

 **Suplementos do SharePoint que usam o  [sistema de autorização de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) para acessar SharePoint se deve passar um token de acesso** (no formato [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/) ) para SharePoint com cada criar, ler, atualizar ou excluir a solicitação (CRUD). SharePoint valida o token e serve a solicitação. Este artigo fornece informações sobre como seu código cria e passa o token de acesso.
No sistema de autorização de alta confiança, **o componente remoto de sua Suplemento do SharePoint cria o token de acesso**. Se o componente remoto estiver usando código gerenciado para o seu código do lado do servidor, a maioria da codificação funciona criando os tokens é feito para você no SharePointContext.cs (ou. vb) e arquivos de TokenHelper.cs (ou. vb) que estão incluídos na Office Developer Tools for Visual Studio. Desde que o cliente para uma alta confiança Suplemento do SharePoint tiver no local SharePoint, eles não queiram provavelmente não usando o ASP.NET, o IIS e o Windows Sever como a pilha de hospedagem para o componente remoto. Você deve considerar usar essa pilha porque os dois arquivos de gerados salvará muita de codificação e testes de trabalho. Se o componente remoto deve usar um idioma não .NET, e o componente remoto e para o farm de SharePoint estão conectados à Internet, você deve considerar usando [o sistema de autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) , em vez de alta confiança. No sistema Microsoft Azure Access Control Service (ACS), todos os de construção do token é feita ACS, portanto, você também é reservados muita de trabalho. O restante deste artigo destina-se principalmente para fornecer orientação aos desenvolvedores criando Suplementos do SharePoint com componentes de não-.NET remotos e usando o sistema de autorização de alta confiança. Ele também pode fornecer algumas informações valiosas para depuração. NET-based Suplementos do SharePoint que usam o sistema de alta confiança.
  
    
    


## Entender o tratamento de tokens de acesso
<a name="AccessTokens"> </a>


> **OBSERVAçãO**
> Tenha em mente ao ler este artigo, especialmente sobre tarefas que seu código deve realizar, que se você estiver usando código gerenciado, o Microsoft Office Developer Tools for Visual Studio adicionar a cada projeto Suplemento do SharePoint dois geraram código arquivos, SharePointContext.cs (ou. vb) e TokenHelper.cs (ou. vb) que faça maioria dessas tarefas para você. Normalmente o código de tratamento de token de seu aplicativo consiste em apenas algumas chamadas às classes nesses arquivos. Os detalhes deste tópico são para ajudar os desenvolvedores que não estejam usando código gerenciado (e para ajudar as pessoas com a solução de problemas com tokens).> Links para bibliotecas de OAuth para muitas plataformas e idiomas estão:>  [OAuth 2.0](http://oauth.net/2/) Role para **bibliotecas de cliente**.> Você pode encontrar mais pesquisando  [github](https://github.com/) para "OAuth 2" e "Token de web JSON" (sem as aspas).
  
    
    

Execute as principais **coisas que seu código precisa** são:
  
    
    

1. **Criar um token de acesso.** As subtarefas para criar este token variam dependendo se o aplicativo web remoto tornaAdicionar-somente na chamadas para SharePoint, chamadas deusuário + suplemento ou ambos. (Para obter informações sobre os dois tipos de chamadas, consulte [Tipos de política de suplemento autorização no SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)).
    
    Se o suplemento torna usuário + chamadas suplemento, em seguida, criando o token de acesso inclui as subtarefas a seguintes:
    
1. Crie um token de ator que identifica o Suplemento do SharePoint e informa SharePoint para delegar a autenticação e autorização para o add-in e codificá-lo em base 64 de codificação. Detalhes sobre as declarações e a estrutura do token ator estão na tabela 2 abaixo. Detalhes sobre a codificação e o token de autenticação estão em [tokens de codificação e assinatura](#EncodingTokens) abaixo.
    
  
2. Assine o token ator com credenciais a partir de um X509 que um SharePoint administrador de farm do certificado tiver configurado SharePoint confie.
    
  
3. Inclua o token ator no token de acesso.
    
  
4. Adicione outras declarações exigidas para o token de acesso. Detalhes sobre as declarações e a estrutura do token estão na tabela 1 abaixo.
    
  
5. Codifica o token de acesso com base 64. Detalhes sobre a codificação e o token de autenticação estão em  [tokens de codificação e assinatura](#EncodingTokens) abaixo.
    
  

    Se o suplemento faz chamadas add-somente na, em seguida, seu código só precisa fazer o primeiro dois dessas subtarefas. O token de ator serve como o token de acesso.
    
    Se o suplemento faz algumas usuário + suplemento chamadas e algumas chamadas add-somente na, ele deve criar um simple ator token das chamadas add-somente na e o token de acesso de maior, aninhado do usuário + chamadas do suplemento. O mesmo token de acesso não pode ser usado para ambos.
    
  
2. **Incluir o token de acesso em cada solicitação HTTP para SharePoint.** O token é adicionado como um cabeçalho de **Authorization** à solicitação. O valor do cabeçalho é a palavra "Portador", seguida por um espaço, seguido do token de acesso de codificado 64 base.
    
  
3. Opcionalmente, **o cache de token de acesso** para reutilização em solicitações subsequentes.
    
  
Essas tarefas devem ser feitas com o código do lado do servidor. Se você estiver usando código gerenciado, exemplos de código para algumas dessas tarefas é na SharePointContext.cs (ou. vb) e TokenHelper.cs (ou. vb) arquivos que gerar o Microsoft Office Developer Tools for Visual Studio.
  
    
    

### Cache de token de acesso
<a name="CacheAccessToken"> </a>

Depois que um token é criado, podem ser reutilizada em chamadas posteriores à SharePoint até expirar. Dependendo da plataforma de hospedagem e arquitetura do componente remoto, existem várias **maneiras para armazenar em cache o token de acesso** no servidor.
  
    
    

- Em estado de sessão
    
  
- No estado do aplicativo
    
  
- No  [cache do AppFabric do Windows Server](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx).
    
  
- Um banco de dados
    
  
- Em um sistema  [memcached](http://www.memcached.org/)
    
  
Se o armazenamento em cache é compartilhado por sessões de usuário diferente, como o cache de aplicativo, em seguida, não deixe de usar uma chave de cache que seja exclusiva para a sessão. Se o cache é compartilhado por vários aplicativos, seu código também deve **relativize a chave de cache** para essa variável também. Também é possível que seu suplemento acessa SharePoint de diferentes farms. Você precisará tokens de acesso distintos para cada um deles, portanto nesse cenário sua chave de cache precisaria relativize para o farm. Todo você pode precisar cache chaves que se baseiam em um ou mais dos ID de usuário, o ID de aplicativo e o domínio de SharePoint ou o realm. Considere o uso de um sistema de chave de cache similar àquela usada pelo Suplementos do SharePoint que usam o sistema de autorização de baixa confiança. Para obter detalhes, consulte [Entender a chave de cache](handle-security-tokens-in-provider-hosted-low-trust-sharepoint-add-ins.md#CacheKey). Basicamente, você faria concatenar uma ou mais dessas três IDs (e, opcionalmente, o resultado em uma cadeia de caracteres mais curto de hash) para servir como uma chave de cache.
  
    
    

### Lidar com tokens de acesso expirados
<a name="CacheAccessToken"> </a>

 **O token de acesso tem um tempo de expiração** que seu código pode ser definido como qualquer valor desejado. Se seu suplemento constrói um novo token de acesso para cada solicitação, somente terá cada token para o live tempo suficiente para ser validado pelo SharePoint, não mais do que alguns segundos, a menos que LAN do cliente é geralmente sobrecarregada. Você pode definir a expiração para anos no futuro, mas ainda no cenário "todos no local" para o qual os suplementos de alta confiança são projetados, há algumas perigo de tokens de acesso que está sendo roubado. Portanto, você deve considerar definindo a expiração não ultrapassa algumas horas. (Se você estiver trabalhando com código gerenciado, o código de amostra de criação de token no arquivo TokenHelper.cs [ou. vb] define a expiração para 12 horas).
  
    
    
Se uma sessão do usuário com sua Suplemento do SharePoint durar mais do tempo de vida do token de acesso em cache, em seguida, a primeira solicitação ao SharePoint após a expiração do token resultará em um erro de **401 Unauthorized**. Seu código tem que lidar com essa resposta. Como alternativa, ele pode testar a hora de expiração de token de acesso antes que ele seja usado. **Seu código deve responder a um token de acesso expirados criando um novo token de acesso** e repetição de solicitação que falhou.
  
    
    

## Compreender a estrutura de tokens de acesso
<a name="Structure"> </a>

A seguir está um **exemplo de um token de acesso gerado por um de alta confiança Suplemento do SharePoint**; Especificamente, este token foi gerada pelo código de amostra no arquivo TokenHelper.cs (ou. vb) que faz parte do modelo de projeto Suplemento do SharePoint criado pelo Office Developer Tools for Visual Studio no Visual Studio 2013 atualização 2. O token foi decodificado e espaços em branco foi adicionado para melhorar a legibilidade. Os tokens de acesso usados no sistema de alta confiança são compatíveis com o [[MS-SPS2SAUTH]: OAuth 2.0 Authentication Protocol: SharePoint Profile](http://msdn.microsoft.com/library/05f9759b-0fa4-45ff-bd4b-0d7d254e7010.aspx), que também é chamado, o servidor-para-servidor ou o protocolo de S2S. Essas informações são fornecidas para ajudar os desenvolvedores usando código gerenciado debug alta confiança Suplementos do SharePoint e para fornecer uma orientação para os desenvolvedores usando outros idiomas sobre como construir os tokens.
  
    
    
 **Esse token de acesso é gerado se o suplemento está fazendo uma chamada para SharePoint usando o  [usuário + diretiva add-in](add-in-authorization-policy-types-in-sharepoint-2013.md).** Se o suplemento está usando a [diretiva add-somente na](add-in-authorization-policy-types-in-sharepoint-2013.md) e ele faz uma add-somente na chamada para SharePoint, em seguida, o token de ator (que é um token de filho dentro do usuário + o token de acesso de suplemento e é descrito abaixo), se torna o token de acesso (e não há nenhum token pai).
  
    
    

> **OBSERVAçãO**
> Observe que os tokens de acesso de alta confiança que seu código cria serão diferentes daquele criado pelo Azure ACS quando o sistema de autorização de baixa confiança está sendo usado:> A declaração **alg** no cabeçalho é "none", porque o token de acesso em um usuário + suplemento chamada a partir de um suplemento de alta confiança não foi assinado.> A URL do add-in no valor **aud** neste exemplo é um servidor local diante, o que é normal para o sistema de alta confiança.> Não há nenhuma declaração **identityprovider**, mas não há um **nii** (emissor de nome de identidade) com o mesmo tipo de valores, como os tokens de acesso de declaração **identityprovider** usados no sistema de autorização de baixa confiança. (Para obter informações sobre esse valor quando o provedor de identidade é baseada em SAML, consulte postagens no blog de Steve Peschka [segurança no SharePoint Add-ins - parte 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx) e [usando o SharePoint suplementos com SAML e FBA Sites no SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx).> Não há nenhuma declaração **actor**, mas não há uma declaração de **actortoken** que contém uma base 64 codificado inner token com um tempo de vida de 12 horas.
  
    
    

O cabeçalho tem duas propriedades. "Digitar" é o tipo de token. O código no aplicativo web remoto deve sempre definir este valor como "JWT". "alg" é o algoritmo usado para assinar o token. Desde que o token externo em um usuário + suplemento chamada a partir de um suplemento de alta confiança não tiver entrado, defina esse valor como "none". Consulte a tabela 1 para obter informações sobre os valores na parte de corpo de token de acesso de alta confiança.
  
    
    



```

{"typ":"JWT", "alg":"none"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"s-1-5-21-2127521184-1604012920-1887927527-2963467",
 "nii":"urn:office:idp:activedirectory",
 "actortoken":"6sMZhbw … [remainder of long base 64 string omitted] … "
}

```

A tabela a seguir fornece algumas orientações para as **Propriedades de que seu código deve incluir no token de acesso e os valores a serem definidas para eles**. Se você estiver usando código gerenciado, SharePointContext.cs (ou. vb) e arquivos TokenHelper.cs (ou. vb) criam os tokens para você. Por exemplo, o código de você faz uma única chamada ao método **SharePointContext.CreateUserClientContextForSPHost**. -Lo, por sua vez, chamadas de métodos na classe **TokenHelper** que construir o token de acesso que é incluído em cada chamada feita para SharePoint pelo objeto de contexto de cliente SharePoint retornado por **SharePointContext.CreateUserClientContextForSPHost**.
  
    
    

**Tabela 1: Declarações de token de acesso emitido por aplicativo**


|**Declaração**|**Descrição**|**Valor correspondente no token de acesso de amostra**|
|:-----|:-----|:-----|
| `aud` <br/> |Short para "público", que significa que a entidade de segurança para o qual o token é destinado. O formato é  _audience principal ID_/ _fully qualified SharePoint domain_@ _SharePoint realm_. <br/> Audiência principal para um Suplemento do SharePoint é sempre 00000003-0000-0ff1-ce00-000000000000. <br/> Como de alta confiança Suplementos do SharePoint são normalmente usados em um cenário de inteiramente no local, o nome de domínio totalmente qualificado do SharePoint com frequência é apenas um nome de servidor. <br/> O  _SharePoint realm_ é o GUID do farm no local SharePoint que o token de acesso é usado para acesso (ou o GUID do aluguel, se o farm tiver sido configurado para aluguéis são). <br/> Encontre o território de SharePoint executando o cmdlet do PowerShell **Get-SPAuthenticationRealm** no servidor do SharePoint. Em seguida, usá-lo diretamente no seu código ou armazená-lo em um arquivo de configuração onde seu código poderá lê-lo, como o aplicativo. Seção de configurações de um arquivo Web. config. Como alternativa, seu código pode detectar dinamicamente o território do SharePoint em tempo de execução, enviando um desafio de autenticação para SharePoint. Consulte o método `GetRealmFromTargetUrl` no arquivo TokenHelper.cs (ou. vb) para obter um exemplo de como isso é feito em código gerenciado. <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `iss` <br/> |Abreviação de "emissor". Ela representa a entidade de segurança que criou o token. O formato é  _Issuer GUID_@ _SharePoint realm GUID_. <br/> No sistema de alta confiança, o suplemento propriamente dito é emissor, portanto a ID do cliente a Suplemento do SharePoint normalmente é usada para o emissor de GUID.  *Todas as letras da identificação de emissor devem estar em minúsculas.*  <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `nbf` <br/> |Abreviação de "não antes". Ele representa a hora em que o token  *inicia*  sendo válida, em segundos, desde a meia-noite, 1º de janeiro de 1970. Seu código deve defini-lo até o momento em que o token é criado. <br/> |1403212820 <br/> |
| `exp` <br/> |Abreviação de "expiração". Ele representa a hora em que o token expira, em segundos, desde a meia-noite, 1º de janeiro de 1970. <br/> |1403256020 <br/> |
| `nameid` <br/> |Um identificador exclusivo para o usuário para o qual o token emitido. O formato varia dependendo do provedor de identidade. Neste exemplo, o provedor é do Active Directory. <br/> |s-1-5-21-2127521184-1604012920-1887927527-2963467 <br/> |
| `nii` <br/> |Abreviação de "emissor do identificador de nome". O nome exclusivo do provedor de identidade, como registrado com autoridade de números atribuídos da Internet (IANA). <br/> Para uma alta confiança Suplemento do SharePoint, ele é normalmente ser um provedor de identidade no local, como o Active Directory como neste exemplo. <br/> |urn: office: idp:activedirectory <br/> |
| `actortoken` <br/> |Um base codificado 64 JWT token que identifica o Suplemento do SharePoint e informa SharePoint confie o suplemento independentemente do que o usuário está executando o add-in. <br/> |Veja abaixo. <br/> |
   
 **A seguir mostra o decodificada **actortoken**.** O objeto de cabeçalho pequeno JavaScript Object Notation (JSON) na parte superior contém metadados sobre o token, incluindo o tipo de token e o algoritmo que é usado para entrar. A propriedade **x5t** do cabeçalho é um digest feito a partir da impressão digital do certificado x. 509 que é oficialmente o emissor do token. Para criar esse valor, seu código faz o seguinte:
  
    
    

1. Obtenha a versão de (não string) da matriz de bytes da impressão digital do certificado. Este é um digest SHA-1 do certificado. (No código gerenciado, isso pode ser feito com o método  [GetCertHash()](http://msdn2.microsoft.com/PT-BR/library/4f9acc3f) . Você precisará equivalente no idioma que você estiver usando algo.)
    
  
2. Codifica a matriz de bytes com codificação Base 64 URL.
    
  
3. Defina o valor da propriedade **x5t** para o digest codificado.
    
  
A tabela 2 descreve as declarações do que seu código deve incluir no corpo do token e os valores para definir para eles.
  
    
    



```

{"typ":"JWT","alg":"RS256","x5t":"7MjK99QvkVdwz6UrKldx8AG7ydM"}
.
{
 "aud":"00000003-0000-0ff1-ce00-000000000000/MarketingServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "iss":"11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "nbf":"1403212820",
 "exp":"1403256020",
 "nameid":"c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2",
 "trustedfordelegation":"true"
}

```


> **OBSERVAçãO**
> Se o suplemento de alta confiança está usando a  [diretiva add-somente na](add-in-authorization-policy-types-in-sharepoint-2013.md) e faz uma chamada de add-somente para SharePoint, o token mostrado aqui é realmente o token de acesso. Não há nenhum token externa. Além disso, não há nenhuma declaração **trustedfordelegation**, já que as permissões do usuário são irrelevantes para uma chamada de add-somente na.
  
    
    


**Tabela 2: Certificado emitido actortoken declarações**


|**Declaração**|**Descrição**|**Valor correspondente no token de acesso de amostra**|
|:-----|:-----|:-----|
| `aud` <br/> |O mesmo que o token de acesso do pai. <br/> |00000003-0000-0ff1-ce00-000000000000/MarketingSharePointServer@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `iss` <br/> |Significado mesmo que o token de acesso do pai, mas o GUID do emissor não é a ID do cliente do aplicativo web. É o GUID do certificado. Embora o código no aplicativo constrói o token de ator, o certificado é considerado o emissor do token ator. <br/> Observe que o GUID do emissor neste exemplo é uma sequência de GUID fácil de lembrar que o administrador de farm usado quando ela registrados o certificado x. 509 como um emissor de token confiável no SharePoint. Isso é comum quando o mesmo certificado é usado como o emissor de token de ator para todos os de alta confiança Suplementos do SharePoint no farm. Um administrador também pode optar por ter certificados distintos para cada Suplemento do SharePoint. Nesse caso, ela usaria GUIDs gerados aleatoriamente diferentes para os certificados.  *Todas as letras na GUID emissora devem ser minúsculos.*  <br/> |11111111-1111-1111-1111-111111111111@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `nbf` <br/> |Significado mesmo que o token de acesso do pai. <br/> |Mesmo valor que o token de acesso do pai. <br/> |
| `exp` <br/> |Significado mesmo que o token de acesso do pai. <br/> |Mesmo valor que o token de acesso do pai. <br/> |
| `nameid` <br/> |Um identificador exclusivo para o Suplemento do SharePoint, porque ele é o "ator" no sistema de alta confiança. O formato é  _client_ID_@ _SharePoint_realm_ <br/> |c3ab8885-458f-4864-8804-1608145e2ac4@52aa6841-b76b-4ed4-a3d7-a259fce1dfa2 <br/> |
| `trustedfordelegation` <br/> |Um valor Boolean que especifica se deve confiar em SharePoint o Suplemento do SharePoint para autenticar e autorizar o usuário. Isso acontece normalmente no sistema altamente confiável. <br/> Não inclua esta declaração em uma chamada add-somente no sistema altamente confiável. <br/> |true <br/> |
   

## Codificar e assinar tokens
<a name="EncodeTokens"> </a>

 **Depois que o seu código tenha adicionado todas as propriedades e valores para o cabeçalho e objetos JSON de corpo, ele deve ser codificá-las, combiná-los em um JWT e assinar proprietário.** A seguir estão as etapas.
  
    
    

1. Codifica o cabeçalho com codificação Base 64 URL.
    
  
2. Codifica a carga com codificação Base 64 URL.
    
  
3. Concatenar corpo codificado após o cabeçalho codificado com um "." caracteres entre eles. Esse é o JWT.
    
  
4. Crie uma assinatura de SHA256 usando o JWT e a chave privada do certificado.
    
  
5. Codifica a assinatura com codificação Base 64 URL.
    
  
6. Acrescente a assinatura até o final do JWT, com um "." caracteres entre elas.
    
  
Para obter um token de ator que é usado com uma chamada add-somente na, o token de ator serve como o token de acesso. Não há nenhum token externa. Para um token de acesso é usado com um usuário + chamada suplemento, as etapas anteriores são usadas para construir um token de ator que é inserido no corpo de um token de acesso como o valor da propriedade **actortoken**. O token de acesso total, em seguida, é codificado e construído com as primeiras três etapas acima, mas não foi assinado, portanto, as etapas restantes não são usadas para o token externo.
  
    
    

## Solucionar problemas de tokens de acesso
<a name="Trouble"> </a>

A  [ferramenta Fiddler](http://www.telerik.com/fiddler) de livre pode ser usado para capturar as solicitações HTTP enviadas pelo componente remoto do suplemento para SharePoint. Não há uma [extensão gratuita para a ferramenta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automaticamente os tokens nas solicitações.
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Criando Add-ins do SharePoint que usam a autorização de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)
    
  
- Steve Peschka  [segurança no SharePoint suplementos - parte 7](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-7.aspx)
    
  
- Steve Peschka  [segurança no SharePoint suplementos - parte 8](http://blogs.technet.com/b/speschka/archive/2013/08/01/security-in-sharepoint-apps-part-8.aspx)
    
  
- Steve Pescka  [usando suplementos do SharePoint com o SAML e FBA Sites no SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/12/07/using-sharepoint-apps-with-saml-and-fba-sites-in-sharepoint-2013.aspx)
    
  
- De Pedro Evan  [alta confiança SharePoint suplementos em plataformas não-Microsoft](http://blogs.msdn.com/b/kaevans/archive/2014/07/14/high-trust-sharepoint-apps-on-non-microsoft-platforms.aspx)
    
  
-  [OAuth 2.0](http://oauth.net/2/)
    
  

