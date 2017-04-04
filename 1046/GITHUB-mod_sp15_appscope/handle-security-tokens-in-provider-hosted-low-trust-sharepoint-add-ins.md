---
title: Lidar com tokens de segurança no hospedado em provedor baixa confiança SharePoint suplementos
ms.prod: SHAREPOINT
ms.assetid: b437181d-bc70-4838-b43d-9f1bb744f0cb
---


# Lidar com tokens de segurança no hospedado em provedor baixa confiança SharePoint suplementos
Saiba mais sobre o contexto de acesso e atualize os tokens que são usados para autorização baixa confiança, hospedado em provedor Suplementos do SharePoint e como trabalhar com elas em seu código.
> **IMPORTANTE**
> **Neste artigo é totalmente sobre o uso de tokens de segurança no sistema de autorização de baixa confiança, não no sistema de alta confiança.** Para obter informações sobre o uso de tokens no sistema de alta confiança, consulte [Criar e usar os tokens de acesso no hospedado em provedor alta confiança SharePoint suplementos](create-and-use-access-tokens-in-provider-hosted-high-trust-sharepoint-add-ins.md).
  
    
    

 **Suplementos do SharePoint que usam o  [sistema de autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) para obter acesso a dados SharePoint participar em umfluxo de OAuth que envolve a passagem de tokens de segurança (no formato [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/) ) entre SharePoint, Microsoft Azure Access Control Service (ACS), os componentes de remota a Suplemento do SharePoint e, em alguns casos, o navegador do usuário.** Existem diversos fluxos que depende do design do add-in, mas todos eles envolvem pelo menos dois tipos de tokens a seguir:
- **Token de acesso:** Incluídos em cada criar, ler, atualizar ou excluir a solicitação (CRUD) os componentes remotos do add-in para SharePoint. SharePoint valida o token e serve a solicitação.
    
  
- **Refresh token:** Usada para obter um token de acesso primeiro no [Token de contexto de fluxo](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)e substituir expirar tokens de acesso em ambos o fluxo de Token de contexto e o  [fluxo de autorização de código](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) do sistema de autorização de baixa confiança.
    
  
Dependendo de qual OAuth está usando o suplemento do fluxo, um ou outro das seguintes opções também é parte do processo:
- **Token de contexto:** Usada, no fluxo de Token de contexto, para fornecer o componente remoto com um token de atualização e informações que ele precisa solicitar um token de acesso do Azure ACS.
    
  
- **Authorizaton código:** Não é um token, mas um código de autorização, exclusivo para cada par de usuários e aplicativos. Ele é usado no fluxo de código de autorização para obter um token de acesso a primeira e um token de atualização.
    
  

## Entender o tratamento de tokens de acesso
<a name="AccessTokens"> </a>

No sistema de autorização de baixa confiança, os tokens de acesso são criados por Azure ACS e enviados para o componente remoto de sua Suplemento do SharePoint. (Quando este artigo foi escrito, os tokens de acesso do ACS emitidos para SharePoint tinham um tempo de vida de 12 horas, mas que podem ser alteradas.) As principais **coisas que o código no seu Suplemento do SharePoint precisa fazer** são:
  
    
    

- **Solicitar um token de acesso** de Azure ACS. Dependendo de qual fluem OAuth estiver sendo usado, o suplemento usa um código de autorização ou informações que ele extrai de um token de contexto para tornar a solicitação.
    
  
- **Incluir o token em cada solicitação HTTP para SharePoint.** O token é adicionado como um cabeçalho de **Authorization** à solicitação. O valor do cabeçalho é a palavra "Portador", seguida por um espaço, seguido do token de acesso de codificado 64 base.
    
  
- Opcionalmente (mas recomendado), **cache de token de acesso** para reutilização em solicitações subsequentes.
    
  
- Opcionalmente, encaminhar o token de acesso para fazer encerrar sistemas para que possam acessar diretamente SharePoint.
    
  
Essas tarefas devem ser feitas com o código do lado do servidor. Se você estiver usando código gerenciado, código de exemplo para algumas dessas tarefas está na SharePointContext.cs (ou. vb) e TokenHelper.cs (ou. vb) arquivos que fazem parte do Microsoft Office Developer Tools for Visual Studio. Para obter um exemplo de código PHP que desempenha algumas dessas tarefas, consulte  [Compreendendo e usando a Interface REST do SharePoint 2013](http://msdn.microsoft.com/en-us/magazine/dn198245.aspx).
  
    
    

### Cache de token de acesso
<a name="CacheAccessToken"> </a>

Dependendo da sua Suplemento do SharePoint da arquitetura e a plataforma de hospedagem, há várias **maneiras para armazenar em cache o token de acesso** no servidor.
  
    
    

- Em estado de sessão
    
  
- No estado do aplicativo
    
  
- No  [Cache do Windows Server AppFabric](http://msdn.microsoft.com/library/8aef3f5d-2a77-46d9-b951-0768fedd31a1%28Office.15%29.aspx) ou seu equivalente em um não - Microsoft OS.
    
  
- O  [Serviço de cache do Microsoft Azure](http://msdn.microsoft.com/library/7c679300-07cc-4ba1-b8fa-39421b570d56%28Office.15%29.aspx) ou seu equivalente em um serviço de nuvem de não-Microsoft.
    
  
- Um banco de dados
    
  
- Em um sistema  [memcached](http://www.memcached.org/)
    
  

> **OBSERVAçãO**
> Na maioria dos cenários, você não conseguirá usar termos simples, como "AccessToken" como a chave de cache, porque seu suplemento deve manter os tokens para diferentes usuários e farms do SharePoint/aluguéis são distintos. Se seu suplemento usa o  [fluxo de Token de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), não há especiais **CacheKey** fornecidos pelo SharePoint que pode ser usado para distinguir os tokens de cache. Esta seção explica quais são os problemas e o que fazer quando o seu aplicativo não estiver usando o fluxo de Token de contexto.
  
    
    

O cache de token de acesso no estado de sessão é funciona bem para a maioria dos cenários. Se o aplicativo web remoto está acessando a outros serviços que usam o OAuth (além do SharePoint ) e é armazenamento em cache os vários tokens de acesso no estado de sessão, certifique-se de usar as chaves de cache distintos para os tokens; Por exemplo, "AccessToken", em vez de usar "SharePoint_AccessToken", "Facebook_AccessToken", "SAP_Gateway_AccessToken", etc.. (Se você não estiver usando o estado da sessão ou alguns outros cache que separa automaticamente o cache de cada usuário, você precisaria também relativize as chaves de usuário.)
  
    
    
Se o aplicativo acessa a mais de um farm de SharePoint ou locação online, você pode usar o domínio SharePoint como parte da chave primária de cache do aplicativo ("SharePoint_ *< mydomain >*  . sharepoint.com_AccessToken") ou use o território de farm/aluguel ("SharePoint_ *< realmGUID >*  _AccessToken"), ambos os quais podem ser lido a partir do token de acesso. (Precisa de seu código para reverter a codificação de Base 64 do token para lê-lo. Em código gerenciado, o TokenHelper.cs ou. vb, arquivo tem o código de amostra para essa finalidade que usa as classes dos namespaces **Microsoft.IdentityModel.S2S.Tokens** e **System.IdentityModel.Tokens**.) Há outra opção disponível quando o aplicativo está usando o fluxo de Token de contexto, conforme descrito no próximo parágrafo.
  
    
    
Existem alguns cenários nos quais na qual seu aplicativo será necessário para armazenar em cache o acesso de token em um local que está disponível para o aplicativo nas sessões ou após o término de uma sessão. Por exemplo, o aplicativo pode ser criado para permitir que os usuários agendem ações ocorrer quando o usuário fechou o aplicativo. Se essas ações incluem acessando SharePoint, em seguida, o suplemento será necessário recuperar o token de acesso. Nesse tipo de cenário, **seu aplicativo deve manter os tokens de acesso de usuários diferentes distintos**. Se você estiver usando o fluxo de Token de contexto, uma cadeia de caracteres de chave do cache é fornecida para essa finalidade do token de contexto SharePoint passa para o componente remoto de sua Suplemento do SharePoint quando o suplemento for iniciado. Para obter mais informações sobre essa **chave especial de cache** e como usá-lo, consulte [Entender a chave de cache](#CacheKey). Você também pode usar essa cadeia de caracteres para o cenário descrito no parágrafo anterior. Sistema de agendamento terão alguns meios de armazenamento de dados de configuração que ele precisará, como quando o trabalho agendado executará e o que ele deve fazer. Use este armazenamento pressionando a tecla de cache para o token de acesso.
  
    
    
Se você usar o cache de sessão cruzada também é compartilhado por vários aplicativos, as chaves de cache precisará ser relativizados para aplicativos, bem como os usuários e os territórios do SharePoint. A chave de cache que é fornecida no token de contexto é exclusiva de aplicativos, bem como os usuários e realm do SharePoint.
  
    
    
 **Se seu aplicativo está usando o fluxo de autorização de código**, e não há nenhum token de contexto e, portanto, não especialmente feitas a chave de cache. Nesse cenário, **você precisará criar seu próprio sistema de chaves para dados armazenados em cache** que são relativizados para um ou mais dos seguintes dependendo de qual nome possível corresponde podem ocorrer dado os casos de uso do seu aplicativo: o usuário, o território SharePoint e o aplicativo. Você pode usar as declarações do token de acesso para essa finalidade. Por exemplo, o **nameId** e o **aud** (consulte as tabelas a seguir). Seu código pode simplesmente concatenar as cadeias de caracteres ou usá-los como sementes para criar um hash exclusivo que pode servir como a chave de cache.
  
    
    
Finalmente, se seu aplicativo toma ambos adicionar-somente na e usuário + chamadas de suplemento para SharePoint, em seguida, ele terá dois tokens de acesso diferente. Portanto, você precisará chaves de cache distintos. Quando você tiver decidido em uma chave de cache básico, basta acrescentar "beça da-apenas na" ou "beça da-em + usuário" a ela.
  
    
    

> **CUIDADO**
> **Não é uma prática segura para armazenar o token de acesso em um cookie.** Geralmente é uma boa prática para evitar a passagem de token de acesso através do navegador.
  
    
    


### Encaminhar o token de acesso a sistemas de back-end
<a name="ForwardTokenToBackend"> </a>

um Suplemento do SharePoint pode ter servidores back-end que não são hospedados no mesmo domínio como o aplicativo da web remoto. Quando um servidor de back-end precisa realizar operações CRUD em SharePoint, o add-in desempenho mais rápido se essas operações podem ir diretamente do sistema back-end para SharePoint. Felizmente, o domínio só é importante quando seu aplicativo está recebendo um acesso token do ACS. Depois que tiver sido o token, ele poderá encaminhá-lo para os serviços de back-end e ligarem SharePoint ao usá-lo. Código nesses sistemas precisará lidar com tokens de acesso expirados e enviar uma solicitação para um novo um back ao aplicativo web pai que está na realidade registrado com o ACS. Consulte a  [Lidar com tokens de acesso expirados](#Expired). Essa técnica deve ser usada apenas para servidores back-end do aplicativo, não para serviços web externos. Além disso, se você estiver usando essa técnica, considere o planejamento dos serviços de back-end use Adicionar-somente na chamadas sempre que possível.
  
    
    

### Lidar com tokens de acesso expirados
<a name="Expired"> </a>

Um token de acesso expira após algumas horas (doze horas no momento em que este artigo foi escrito, mas que pode alterar). Se o aplicativo ainda está acessando SharePoint após a expiração de token de acesso, em seguida, a primeira solicitação ao SharePoint após a expiração resultará em um erro de **401 Unauthorized**. Seu código tem que lidar com essa resposta. Como alternativa, o código pode testar a hora de expiração de token de acesso antes que ele seja usado. **Seu código usa o token de atualização para obter outro token de acesso do ACS.** No fluxo de Token de contexto, o token de atualização está incluído no token de contexto que seu suplemento recebe do SharePoint no início da sua primeira sessão com SharePoint. No fluxo de autorização de código, ele é passado para o aplicativo, juntamente com o primeiro token de acesso. Seu código deve cache para que esteja disponível quando necessário. O token de atualização dura poucos meses e pode ser mantido em um cookie ou em armazenamento do servidor. Para obter mais informações, consulte [cache de tokens de atualização e compreender o tratamento](#RefreshToken).
  
    
    

### Veja exemplos de tokens de acesso
<a name="ExampleAccessTokens"> </a>

Esta seção descreve, com exemplos, tokens de acesso e mostra como diferem, dependendo da política de autorização que está sendo usada.
  
    
    

#### Consulte tokens de acesso para o sistema de autorização de baixa confiança

 **Usuário + suplemento política**
  
    
    
A seguir está um decodificada **exemplo de um usuário + o token de acesso de suplemento gerado pelo ACS** a serem usados para chamadas para SharePoint usando o [usuário + suplemento política](add-in-authorization-policy-types-in-sharepoint-2013.md). Espaço em branco foi adicionado para melhorar a legibilidade. O token é compatível com o protocolo  [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1) . O objeto JavaScript Object Notation (JSON) no token é chamado adeclaração set consulte Tabela 1 para obter detalhes sobre as propriedades nessa declaração definido. Observe que todos os valores devem ser minúsculas. (Usuário + tokens de acesso do suplemento são os mesmos no [fluxo de autorização de código](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows)e o  [Token de contexto de fluxo](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) .)
  
    
    



```

{
 "aud": "00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss": "00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf": 1377549246,
 "exp": 1377592446,
 "nameid": "2303000085ff9abc",
 "actor": "964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73",
 "identityprovider": "urn:federation:microsoftonline"
}
```


**Tabela 1: Usuário emitidos ACS + suplemento acessar declarações de token**


|**Declaração**|**Descrição**|**Valor correspondente no token de acesso de amostra**|
|:-----|:-----|:-----|
| `aud` <br/> |Short para "público", que significa que a entidade de segurança para o qual o token é destinado. O formato é  _audience principal ID_/ _SharePoint domain_@ _SharePoint realm_, onde  _audience principal ID_ é uma ID de entidade de segurança permanente para SharePoint. (Consulte a [Entidade de segurança de aplicativo de produto Microsoft constantes](http://msdn.microsoft.com/en-us/library/hh629982%28v=office.12%29.aspx).) <br/> .  _SharePoint realm_ é o GUID do aluguel SharePoint Online ou o farm local SharePoint, que o token de acesso é usado para o access. Este GUID funciona como ID do realm para o emissor de token, neste caso Azure ACS. <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `iss` <br/> |Abreviação de "emissor". Ela representa a entidade de segurança que criou o token. O formato é  _Issuer GUID_@ _SharePoint realm GUID_. <br/> No sistema de autorização de baixa confiança, o emissor é o que GUID de Azure ACS e de seus é **00000001-0000-0000-c000-000000000000**. <br/> |00000001-0000-0000-C000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `nbf` <br/> |Abreviação de "não antes". Ele representa a hora em que o token  *inicia*  sendo válida, em segundos, desde a meia-noite, 1º de janeiro de 1970. Por padrão, ela é definida como o momento em que o token é criado. Consulte [Trabalhar com valores de hora JWT](#JWTtimes) para obter mais informações. <br/> |1377549246 <br/> |
| `exp` <br/> |Abreviação de "expiração". Ele representa a hora em que o token expira. Por padrão, esse é 12 horas após o momento **nbf**. Consulte [Trabalhar com valores de hora JWT](#JWTtimes) para obter mais informações. <br/> |1377592446 <br/> |
| `nameid` <br/> |Um identificador exclusivo para o usuário para o qual o token emitido. O formato varia dependendo do provedor de identidade. Neste exemplo, o provedor é o Microsoft Online, mas se ele tiver sido do Active Directory, a ID será semelhante a "s-1-5-21-2127521184-1604012920-1887927527-415149". <br/> |2303000085ff9abc <br/> |
| `actor` <br/> |A entidade de segurança que procura acesso ao farm SharePoint ou aluguel. Ele tem o formulário  _Client ID of Application_@ _SharePoint realm_. <br/> |964de6ad-6d28-4dc7-8e05-3acd8006e5c9@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `identityprovider` <br/> |O nome exclusivo do provedor de identidade, como registrado com autoridade de números atribuídos da Internet (IANA). <br/> Para um suplemento que SharePoint Online está instalado, o valor é normalmente o mesmo como neste exemplo. <br/> Para um suplemento que é instalado em um farm no local, geralmente seria um provedor de identidade no local, como "urn: office: idp:activedirectory". <br/> |Federação: urn: microsoftonline <br/> |
   
 **Adicionar-somente na diretiva**
  
    
    
A seguir está um decodificada **exemplo de um token de acesso add-somente na gerados pelo ACS** a serem usados para chamadas para SharePoint usando a [diretiva add-somente na](add-in-authorization-policy-types-in-sharepoint-2013.md). Espaço em branco foi adicionado para melhorar a legibilidade. O token é compatível com o protocolo  [JSON Web Token](http://datatracker.ietf.org/doc/draft-ietf-oauth-json-web-token/?include_text=1) . Consulte a tabela 2 para obter detalhes sobre as propriedades nessa declaração no conjunto. (A diretiva add-somente na não está disponível para aplicativos que usam o [fluxo de autorização de código](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), porque não têm um arquivo de manifesto add-in e, portanto, não poderá solicitar permissão para usar chamadas add-somente na.)
  
    
    



```

{
 "aud":"00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":1403304705,
 "exp":1403347905,
 "nameid":"c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73",
 "sub":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "oid":"1d47ac31-498b-4988-8aac-85fc9bd2e1ce",
 "trustedfordelegation":"false",
 "identityprovider":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73"
}
```


**Tabela 2: Declarações de token emitido pelo ACS adicionar-em-acesso somente**


|**Declaração**|**Descrição**|**Valor correspondente no token de acesso de amostra**|
|:-----|:-----|:-----|
| `aud` <br/> |Mesmo que o usuário + token suplemento acima. <br/> |00000003-0000-0ff1-ce00-000000000000/company.sharepoint.com@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `iss` <br/> |Mesmo que o usuário + token suplemento acima. <br/> |00000001-0000-0000-C000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `nbf` <br/> |Mesmo que o usuário + token suplemento acima. <br/> |1403304705 <br/> |
| `exp` <br/> |Mesmo que o usuário + token suplemento acima. <br/> |1403347905 <br/> |
| `nameid` <br/> |Um identificador exclusivo para o suplemento, em vez do usuário, desde que a identidade do usuário não importa com a diretiva add-somente na. O formato é  _client ID_@ _SharePoint realm_. <br/> |c76da14e-07fd-4638-a723-1ff60ce70d63@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
| `sub` <br/> |Abreviação de "assunto". O assunto do token, que é a entidade que está tentando obter acesso a SharePoint; Nesse caso, o computador remoto aplicativo web. O ID do objeto é usado para o valor. Consulte o **oid** declaração abaixo. <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce <br/> |
| `oid` <br/> |Abreviação de "ID do objeto". É a ID de objeto no Windows Azure Active Directory para o aplicativo web remoto. Em um token de acesso add-somente na, a ID da entidade e o objeto são o mesmo valor. <br/> |1d47ac31-498b-4988-8aac-85fc9bd2e1ce <br/> |
| `trustedfordelegation` <br/> |Um valor Boolean que especifica se deve confiar em SharePoint o Suplemento do SharePoint para autenticar e autorizar o usuário. É false em chamadas de adicionar-somente na porque a identidade do usuário não importa. <br/> |false <br/> |
| `identityprovider` <br/> |O nome exclusivo do provedor de identidade. Uma vez que é o suplemento, em vez de um usuário, cuja identidade está sendo fornecida, o ACS é o provedor de identidade. O formato é  _ACS GUID_@ _SharePoint realm_. <br/> |00000001-0000-0000-C000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
   

## Compreender a estrutura e tratamento de tokens de contexto
<a name="ContextTokens"> </a>

Um token de contexto é usado somente no  [fluxo de Token de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows) do sistema de autorização de baixa confiança. **Quando o Suplemento do SharePoint é iniciado em SharePoint, SharePoint solicita que Azure ACS criar um token de contexto que SharePoint e em seguida, passa para o componente remoto do Suplemento do SharePoint.** O token é passado como um parâmetro de formulário oculto chamado **SPAppToken** em uma solicitação de SharePoint para a página inicial do componente remoto. O token é assinado com um conhecido secreta do cliente apenas para o ACS e o Suplemento do SharePoint. O **token de contexto inclui um token de atualização que o suplemento usa**, junto com outras informações a partir do contexto token, **para solicitar um token de acesso** do ACS. (Quando este artigo foi escrito, tokens de contexto do ACS emitidos para SharePoint tinham um tempo de vida de 12 horas, mas que podem ser alteradas.) As **principais tarefas para o código no Suplemento do SharePoint** são as seguintes:
  
    
    

- Use o segredo do cliente do add-in para **Validar o token de contexto**.
    
  
- **Cache de token de contexto** ou extração e cache separadamente, o token de atualização e certos outros itens de dentro dela.
    
  
- Use o token de atualização e outras informações a **solicitação de um token de acesso** do ACS (que também é então armazenado em cache).
    
  
- **A CacheKey do cache** do token de contexto.
    
  

> **IMPORTANTE**
> As duas primeiras tarefas devem ocorrer antes que o usuário navega para outra página ou atualiza a página ou o token é perdido. Por exemplo, em um aplicativo de formulários do ASP.NET web, considere essas tarefas no método **Page_Load** (em um bloco de código condicional que é executada somente quando a solicitação não for uma postagem). Em um aplicativo ASP.NET MVC, considere estas tarefas no método controlador padrão.
  
    
    

Se você estiver usando código gerenciado, código de exemplo para algumas dessas tarefas está na SharePointContext.cs (ou. vb) e TokenHelper.cs (ou. vb) arquivos que estão incluídos na Microsoft Office Developer Tools for Visual Studio. Você precisará fazer chamadas simples para os membros da classe TokenHelper. Por exemplo, seu código pode fazer a primeira tarefa com a única linha de código a seguir:
  
    
    



```cs

SharePointContextToken contextToken =
    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
    Request.Url.Authority);
```

Para obter um exemplo de como fazer algumas dessas tarefas com PHP, consulte o exemplo  [SharePoint 2013: executar operações em biblioteca de documentos do SharePoint do site PHP](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef).
  
    
    

### Cache de token de contexto ou partes dele
<a name="CacheContextToken"> </a>

Você pode **cache de** token de contexto todo, ou apenas o token de atualização e determinados os itens que estão dentro dela que seu código usa para obter tokens de acesso, **no armazenamento do servidor ou cliente**. Para manter a simplicidade, este artigo pressupõe que você armazena em cache o token de contexto, como uma unidade.
  
    
    

> **IMPORTANTE**
> Podemos lembrá-lo mais uma vez porque ele é realmente importante: não use o cache de cliente para o token de  *acesso*  . É seguro para usá-lo apenas para o token de contexto.
  
    
    

Você tem as mesmas  [Opções de cache do lado do servidor](#CacheAccessToken) para conforme listadas acima para o token de acesso. Opções do cliente incluem um cookie e um campo de formulário oculto em uma página HTML. Outra opção é armazenar o token de contexto no cache da sessão, mas armazenar **CacheKey** obtido de dentro no cliente.
  
    
    
Se seu aplicativo acessa SharePoint depois que uma sessão será encerrada, em seguida, nem o cache de sessão nem o cliente cache é uma opção, porque o token de atualização deve estar disponível para o aplicativo caso o token de acesso original expirou quando o trabalho de pós-sessão é executado. Neste cenário, você precisa de um cache (sessão cruzada) durável que é compartilhado por vários usuários e/ou territórios do SharePoint e/ou aplicativos. Portanto, seu código deve usar as chaves de cache que distinguir usuário, realm do SharePoint e/ou aplicativo conforme explicado acima no  [Cache de token de acesso](#CacheAccessToken). Você pode **usar a chave de cache especiais** que está dentro do token de contexto para essa finalidade assim como você usou para o token de acesso. (Consulte [Entender a chave de cache](#CacheKey) de abaixo.)
  
    
    

#### Entender a chave de cache
<a name="CacheKey"> </a>

 **A chave de cache é uma sequência de caracteres opaca que seja exclusiva para a combinação de usuário, emissor de nome de usuário, suplemento e locatário de farm ou SharePoint OnlineSharePoint.** Antes que ele é criptografado, ele tem o seguinte formato, onde _Realm_ é o GUID do farm no local SharePoint ou SharePoint Online aluguel.
  
    
    
 _UserNameId_ + "," + _UserNameIdIssuer_ + "," + _ApplicationId_ + "," + _Realm_
  
    
    
A chave de cache não contém informações de URL do site. Cada locatário SharePoint Online (ou o farm local SharePoint ) tem um território exclusivo, portanto, a chave de cache é exclusiva para cada combinação de nome de usuário, emissor de nome de usuário, aplicativo e farm. O token de contexto de exemplo abaixo, é o valor da chave criptografada de cache:
  
    
    
 `KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=`
  
    
    
Desde que o seu aplicativo pode ser cache vários itens, como o token de acesso e o token de contexto no mesmo cache com a mesma chave de cache, **considere usar a chave de cache como uma haste** e qualquer acrescentando ou anexando ao início de uma cadeia de caracteres específica, como "AccessToken" ou "ContextToken"-lo conforme necessário para formar uma chave de cache completa. **Outra opção é criar uma classe** com propriedades para as várias coisas que você deseja armazenar em cache e, em seguida, armazenar em cache um objeto desse tipo. Uma **terceira opção**, se você estiver **usando um banco de dados** como um cache, é usar uma tabela com uma coluna CacheKey e colunas adicionais para os itens armazenados em cache (AccessToken, ContextToken, etc.).
  
    
    
Seu aplicativo não precisará usar o mesmo cache para tudo, que ele é cache, obviamente. Um padrão comum é para armazenar em cache o token de acesso no cache de sessão, o token de contexto (ou o token de atualização do dentro dele em um banco de dados) e a CacheKey em um cookie.
  
    
    

### Use o token de contexto para obter um token de acesso
<a name="UseContextTokenToGetAccessToken"> </a>

 **Para obter um token de acesso, o seu aplicativo que envia uma solicitação diretamente para o ACS.** A solicitação inclui três partes de informações que serão extraídos do token de contexto (e outras informações):
  
    
    

- O token de atualização
    
  
- A entidade de segurança do aplicativo GUID do SharePoint.
    
  
- O GUID do realm do SharePoint farm ou SharePoint Online aluguel ao qual o suplemento está tentando obter acesso.
    
  
O arquivo TokenHelper.cs (ou. vb) tem o código que cria essa solicitação. Para obter um exemplo de código PHP que faz isso, consulte  [SharePoint 2013: executar operações em biblioteca de documentos do SharePoint do site PHP](http://code.msdn.microsoft.com/office/SharePoint-2013-Perform-8a78b8ef/sourcecode?fileId=117521&amp;pathId=1932320454).
  
    
    
O aplicativo pode obter o território do SharePoint locação ou farm em tempo de execução, como uma alternativa para a análise do token de contexto. Se você estiver usando código gerenciado, há um método  `TokenHelper.GetRealmFromTargetUrl` para obter o realm. Certifique-se de que o valor de cache para que seu código não fazer outra rede chamada para obtê-lo novamente.
  
    
    

### Obter um novo token de contexto
<a name="GetNewContextToken"> </a>

 **Se precisar de um novo token de contexto**, geralmente porque o token de atualização (que estão contidos em tokens de contexto) expirou, **seu código pode fazer uma nova redirecionando o navegador para uma página de especial em cada site SharePoint** - AppRedirect.aspx. Dois parâmetros de consulta precisarem estar anexada à URL da página:
  
    
    

-  `client_id`: a ID de cliente do seu Suplemento do SharePoint.
    
  
-  `redirect_uri`: o URI ao qual você deseja que o navegador redirecionado depois que o novo token de contexto é obtido. SharePoint LANÇARÁ o token de contexto para esse URI. Geralmente, isso é a mesma página, o método de controlador ou o método de serviço web que solicitou o novo token de contexto. O valor deve ser codificado URL.
    
  
O exemplo a seguir mostra a estrutura da URL:
  
    
    



```

https://<SharePointDomain> /_layouts/15/appredirect.aspx?client_id=<app_client_GUID> &amp;redirect_uri=<URL-encoded_redirect_URI>
```

Este é um exemplo de fazendo a solicitação no ASP.NET que usa o arquivo TokenHelper:
  
    
    



```
Response.Redirect(TokenHelper.GetAppContextTokenRequestUrl(sharePointUrl, Server.UrlEncode(Request.Url.ToString())));
```


### Ver um exemplo de um token de contexto
<a name="ExampleContextToken"> </a>

O exemplo a seguir é um exemplo de um token de contexto. O objeto pequeno JavaScript Object Notation (JSON) na parte superior contém metadados sobre o token. Essas propriedades são os mesmos que os tokens de acesso (veja acima). O valor da propriedade **alg** é o nome do algoritmo que é usado para gerar a assinatura que ACS acrescenta ao token. Consulte a tabela 3 para obter detalhes sobre as propriedades na carga do token. Observe que todos os valores devem ser minúsculas. (Espaço em branco foi adicionado para melhorar a legibilidade.)
  
    
    

```
{"typ":"JWT","alg":"HS256"}
.
{
 "aud":"a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73",
 "iss":"00000001-0000-0000-c000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "nbf":"1335822895",
 "exp":"1335866095",
 "appctxsender":"00000003-0000-0ff1-ce00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73",
 "appctx":"{
            \\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\",
            \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\"
           }",
 "refreshtoken":"IAAAAC1Lv5w0OrcFAmJx0xk6aaBdhgsw3VPnPzNEDAWypTHtCYytZ2/dBBUKj+HLK8YB3IUCUfDxYpAque
NHKtgs4rYJJ5AegQpNMOJR1yYK8ngivQx0oetj7aSPuGVb+k6at6G0Kx5LZ5vhxkAq8iUSwu8p4L2cvNMzDF1mDKfMivqxgrIZkr2nbf9as0SJFL6VG5hZnDE4HKq
xJnejSW3umatKM4fsfY1MClVCxrkXb2EQ8H/TmwaJc388YW063GEVUS/3BTSgSIRBKQUmXJuJ6BZY7WTm84LaGrx3mIjnUTM/jnqPoPG55JbCC9sS/MeGNPtzPPCDg
6Vv7dVhQ1Dq5Y3fQ65e9LpJ580jCgzYYvpIFT+Wx5V+17mjY2T8wug04K2ts87Znsr+GfFCorf7NS/lj5HjoxRAQ2tva/8dwguSLwxcUwi/Q9MbpR0NNtlpwVazqi9O
hJ4Df7gVhUDdJ0Dtc6aFCPbl5ZLDDRs42xK2", 
 "isbrowserhostedapp": "true"
}
```

As declarações **aud**, **iss**, **nbf** e **exp** são exatamente os mesmos que um token de acesso conforme descrito acima. As declarações **appctxsender**, **appctx**, **CacheKey**, **SecurityTokenServiceUri**, **refreshtoken** e **isbrowserhostedapp** são descritas na tabela a seguir.
  
    
    

**Tabela 3. Informações e declarações de token de contexto**


|****declaração****|****Descrição****|****Valor correspondente no token de contexto de amostra****|
|:-----|:-----|:-----|
|AUD <br/> ||a044e184-7de2-4d05-aacf-52118008c44e/fabrikam.com@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
|ISS <br/> ||00000001-0000-0000-C000-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
|NBF <br/> ||1335822895 <br/> |
|EXP <br/> ||1335866095 <br/> |
|appctxsender <br/> |Abreviação de "remetente de contexto de aplicativo". Representa o aplicativo que enviou o token de contexto para o Suplemento do SharePoint. <br/> Ele tem o formulário  _GUID of principal_@ _SharePoint realm_, onde  _GUID of principal_ é a constante ID do aplicativo principal; é possível SharePoint, Exchange 2013, Lync 2013 ou fluxo de trabalho. <br/> |00000003-0000-0ff1-CE00-000000000000@040f2415-e6e3-4480-96ce-26ef73275f73 <br/> |
|appctx <br/> |Abreviação de "suplemento contexto". É um objeto JSON que contém o **CacheKey** e **SecurityTokenServiceURI**. <br/> |\\"CacheKey\\":\\"KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw=\\", \\"SecurityTokenServiceUri\\":\\"https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2\\" <br/> |
|CacheKey <br/> |Um valor exclusivo que pode ser usado como a chave em qualquer chave/valor cache estruturado para armazenar e recuperar o token de contexto. Ele também pode ser usado como o valor de uma coluna de chave em uma linha de um banco de dados. <br/> |KQAIUpDUD0sm5Tr83U+jZGYVuPPCPu8BGwoWiAACqNw= <br/> |
|SecurityTokenServiceURI <br/> |O URI do token de serviço de emissão. <br/> |https://accounts.accesscontrol.windows-int-sn1-004.accesscontrol.aadint.windows-int.net/tokens/OAuth/2 <br/> |
|refreshtoken <br/> |O token de atualização para o suplemento. <br/> |IAAAAC1Lv5w0OrcFAmJx0xk6… <br/> |
|isbrowserhostedapp <br/> |Um campo **Boolean** que especifica se a solicitação para o suplemento que contém o token de contexto está acessando a partir de um navegador (true) ou um receptor de evento remoto (false). <br/> |true <br/> |
   

### Use o token de contexto para limitar o acesso aos usuários do SharePoint
<a name="UseContextTokenToLimitAccess"> </a>

Se desejar limitar o acesso para o componente remoto, como um serviço WCF, aos usuários SharePoint, seu código pode simplesmente validar o token de contexto na solicitação HTTP. (Se você estiver usando código gerenciado, você pode simplesmente chamar **TokenHelper.ReadAndValidateContextToken()**). Seu código pode verificar se a declaração de ator do token é iniciado com **00000003-0000-0ff1-ce00-000000000000**, se você quiser certificar-se de que ele é SharePoint (e não, por exemplo, Exchange 2013, Lync 2013 ou fluxo de trabalho). Se você deseja fazer a validação adicional que requer uma chamada de volta para o SharePoint, como limitar o acesso aos usuários com uma determinada função no SharePoint, você pode armazenar em cache o fato de que você fez essa validação para um usuário específico (por meio **CacheKey** do token de contexto) para que você deve fazer isso somente uma vez.
  
    
    

## Entender o tratamento e o cache de tokens de atualização
<a name="RefreshTokens"> </a>

 **Um token de atualização está incluído no contexto token que SharePoint remete para seu aplicativo web quando ele for iniciado.** O token de atualização é um token criptografado que seu Suplemento do SharePoint não pode descriptografar. **Seu código utiliza**, junto com outras informações, **para obter um novo token de acesso quando o token de acesso atual expira**. Ele também é usado para acessar o *primeiro*  token no [fluxo de Token de contexto](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows). (Quando este artigo foi escrito, emitidos ACS tokens de atualização para SharePoint tinham um tempo de vida de seis meses, mas que podem ser alteradas.)
  
    
    
Desde que um token de acesso dura horas (atualmente 12) e um usuário final obtém um novo toda vez que ele iniciará sua Suplemento do SharePoint de SharePoint, você só precisa o token de atualização em uma das seguintes situações:
  
    
    

- Os usuários têm longa duração de sessões com seu suplemento no qual o suplemento faz chamadas para SharePoint muitas horas (atualmente mais de 12) depois que ele é iniciado.
    
  
- Design do suplemento permite aos usuários agendar o suplemento para acessar um dia SharePoint após a sessão termine.
    
  
 **Cenários exigem o add-in para armazenar em cache o token de atualização**, tanto segundo cenário requer um cache no servidor que é durável nas sessões. Suas opções de armazenamento em cache são os mesmos para o [token de acesso](#CacheAccessToken) e, no fluxo de Token de contexto, você pode usar [a chave de cache do token de contexto](#CacheKey). (No fluxo de Token de contexto, você normalmente apenas o cache de token de contexto. Ele contém as informações de token e outros de atualização necessárias para obter um novo token de acesso. No  [fluxo de autorização de código](creating-sharepoint-add-ins-that-use-low-trust-authorization.md#Flows), não há nenhum token de contexto, portanto você armazena em cache o token de atualização propriamente dito.)
  
    
    
Se você estiver em cache o token de atualização em um armazenamento que se mantêm em sessões de um usuário específico com o add-in, certifique-se de substituí-lo com o token de atualização mais recente. Em tanto o hospedado em nuvem e fluxos de código de autorização, obtém o usuário de um novo refresh token cada tempo ele inicia o suplemento.
  
    
    
Se o token de atualização tiver expirado, uma solicitação para o ACS para um novo token de acesso resultará em um erro de **401 Unauthorized**. Seu suplemento deve responder a esse erro, obtendo um novo token de atualização e usá-lo para obter um novo token de acesso. (Desde que o token de atualização é criptografado, seu código não pode verificar sua expiração para utilizá-lo.) No fluxo de Token de contexto, seu suplemento obtém um novo token refresh obtendo [um novo token de contexto](#GetNewContextToken). No fluxo de código de autorização, seu suplemento obtém uma nova atualização token reiniciando-se o fluxo. Especificamente, seu código deve responder para o erro redirecionando o usuário à página OAuthAuthorize.aspx SharePoint conforme explicado no  [Entender o fluxo de OAuth para suplementos que solicitar permissões dinamicamente](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Flow).
  
    
    

## Noções básicas sobre o tratamento códigos de autorização
<a name="Authcodes"> </a>

 **No fluxo de código de autorização, o processo de autorização começa com um código de autorização ACS emite, mediante solicitação do SharePoint e quais SharePoint em seguida, passa para o aplicativo remoto** como um parâmetro de consulta. O código de autorização é exclusivo para cada par de usuários e aplicativos remotos. (Quando este artigo foi escrito, os códigos de autorização ACS emitidos para SharePoint tinham um tempo de vida de 5 minutos, mas que podem ser alteradas.) A lógica em **seu aplicativo deve obter o código de autorização do parâmetro de consulta e usá-lo em uma solicitação para o ACS para um token de acesso**. Se você estiver usando código gerenciado, código de exemplo para criar o token é no arquivo TokenHelper.cs (e. vb). Código de amostra para o parâmetro de consulta de leitura está em [Obter exemplos de código por trás de uma página que acessa SharePoint](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Default). ACS invalida o código de autorização imediatamente após emitir o token de acesso, para que ele pode ser usado apenas uma vez e não faz sentido no cache-lo.
  
    
    

## Trabalhar com valores de hora JWT
<a name="JWTtimes"> </a>

As declarações **nbf** e **exp** estão no formato especificado pela [especificação de JWT](http://self-issued.info/docs/draft-goland-json-web-token-00.mdl). Eles são gravados como o número de segundos desde 1º de janeiro de 1970. Em c#, você pode converter esses valores com o código a seguir, onde  _jWTTimeStamp_ é o valor do token, como 1335822895.
  
    
    

```cs

DateTime exp = new DateTime(1970,1,1).AddSeconds(jWTTimeStamp);

```


## Tratamento de token de solução de problemas
<a name="Troubleshooting"> </a>

A  [ferramenta Fiddler](http://www.telerik.com/fiddler) de livre pode ser usado para capturar as solicitações HTTP enviadas pelo componente remoto do suplemento para SharePoint. Não há uma [extensão gratuita para a ferramenta](https://github.com/andrewconnell/SPOAuthFiddlerExt) que decodifica automaticamente os tokens nas solicitações.
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Criando Add-ins do SharePoint que usam a autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
- Para amostras de código que usam TokenHelper e código gerenciado, consulte  [SharePoint 2013: remoto Hello World usando CSOM](http://code.msdn.microsoft.com/SharePoint-2013-Hello-0fd15fbf) e [pacote de amostra de suplementos do SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
- Para um exemplo de código que usa o REST chama a partir de um suplemento PHP:  [SharePoint 2013: executar operações em biblioteca de documentos do SharePoint do site PHP](https://code.msdn.microsoft.com/SharePoint-2013-Perform-8a78b8ef)
    
  

