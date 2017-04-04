---
title: Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013
Saiba como realizar operações básicas de criação, leitura, atualização e exclusão (CRUD) com a interface REST do SharePoint 2013.
## Desenvolvendo com o REST e as APIs de cliente do SharePoint
<a name="ClientAPIs"> </a>

Você pode realizar operações básicas de criação, leitura, atualização e exclusão (CRUD) usando a interface REST (Representational State Transfer) fornecida pelo SharePoint 2013. A interface REST expõe todas as entidades e as operações do SharePoint disponíveis nas outras APIs de cliente do SharePoint. Uma das vantagens de usar o REST é que você não precisa adicionar referências a nenhum conjunto de cliente ou biblioteca do SharePoint 2013. Em vez disso, você faz solicitações HTTP para os pontos de extremidade apropriados para recuperar ou atualizar as entidades do SharePoint, como Webs, listas e itens de listas. Confira  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md) para ter acesso a uma introdução completa sobre a interface REST do SharePoint 2013 e sua arquitetura.
  
    
    
 [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md) e [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md) explicam com maiores detalhes como trabalhar com entidades fundamentais do SharePoint. Confira [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations) para ter acesso a um exemplo que mostra como realizar várias dessas operações no contexto de um aplicativo Web ASP.NET escrito em C#.
  
    
    
Para obter mais detalhes sobre os conjuntos de APIs disponíveis na plataforma SharePoint 2013, confira  [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx). Para saber mais sobre como usar as outras APIs de cliente, confira  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md),  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) e [Crie aplicativos do Windows Phone que acessam o SharePoint 2013](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx).
  
    
    

## Operações HTTP nos serviços REST do SharePoint 2013
<a name="HTTPOps"> </a>

Os pontos de extremidade no serviço REST do SharePoint 2013 correspondem aos tipos e membros nos modelos de objeto de cliente do SharePoint. Ao utilizar solicitações HTTP, você poderá usar esses pontos de extremidade REST para realizar operações típicas de CRUD ( **Create**, **Read**, **Update** e **Delete**) nas entidades do SharePoint, como listas e sites.
  
    
    
Em geral, os pontos de extremidade que representam operações de **Read** são mapeados para comandos **GET** HTTP. Os pontos de extremidade que representam as operações de atualização são mapeados para comandos **POST** HTTP, e os pontos de extremidade que representam operações de atualização ou de inserção são mapeados para os comandos **PUT** HTTP.
  
    
    
No SharePoint 2013, use **POST** para criar entidades como listas e sites. O serviço REST do SharePoint 2013 dá suporte ao envio de comandos **POST** que incluem definições de objeto para pontos de extremidade que representam coleções. Por exemplo, você poderia enviar um comando **POST** com uma nova lista de definições de objeto em ATOM para a URL a seguir e criar uma lista do SharePoint:
  
    
    
 `http://<site url>/_api/web/lists`
  
    
    
Para operações **POST**, todas as propriedades que não forem necessárias serão definidas com os valores padrão. Se você tentar definir uma propriedade como somente leitura como parte de uma operação **POST**, o serviço retornará uma exceção.
  
    
    
Use as operações **PUT** e **MERGE** para atualizar objetos do SharePoint existentes. Qualquer ponto de extremidade de serviço que represente uma operação **set** da propriedade do objeto dá suporte a solicitações **PUT** e a solicitações **MERGE**. Para as solicitações **MERGE**, a definição de propriedades é opcional; qualquer propriedade que você não definir explicitamente, conservará a propriedade atual. Para comandos **PUT**, no entanto, qualquer propriedade que você não definir explicitamente será definida de acordo com as propriedades padrão. Além disso, se você não especificar todas as propriedades exigidas nas atualizações de objetos, ao usar os comandos **PUT** HTTP, o serviço REST retornará uma exceção.
  
    
    
Use o comando **DELETE** HTTP na URL específica do ponto de extremidade para excluir o objeto do SharePoint representado por esse ponto de extremidade. No caso de objetos recicláveis, como listas, arquivos e listas de itens, isso resultará em uma operação **Recycle**.
  
    
    

## Lendo dados com a interface REST do SharePoint 2013
<a name="ReadingData"> </a>

Para usar os recursos REST internos ao SharePoint 2013, crie uma solicitação HTTP RESTful, usando o OData padrão, que corresponde à API do modelo de objeto cliente que você deseja usar. Cada entidade do SharePoint é exposta a um ponto de extremidade no site do SharePoint 2013 para o qual você está direcionando, e seus metadados são representados no formato XML ou JSON. Você pode fazer as solicitações HTTP em qualquer linguagem, incluindo, sem limitação, JavaScript e C#.
  
    
    
Para ler informações de um ponto de extremidade REST, você deverá saber qual é a URL do ponto de extremidade e qual é a representação do OData da entidade do SharePoint exposta nesse ponto de extremidade. Por exemplo, para recuperar todas as listas de um site específico do SharePoint, você teria que fazer uma solicitação **GET** para `http://<site url>/_api/web/lists`. Você pode navegar até essa URL no seu navegador e ver qual XML é retornado. Ao fazer a solicitação no código, é possível especificar se a representação das listas do OData deverá ser recebida em XML ou JSON.
  
    
    
O código C# a seguir demonstra como fazer essa solicitação **GET** que retorna uma representação JSON de todas as listas de um site usando JQuery. Ele também pressupõe que você possua um token de acesso OAuth válido armazenado na variável **accessToken**. Você não precisará do token de acesso se fizer a chamada de uma Web de suplementos, como faria em um suplemento hospedado no SharePoint. Observe que você não pode obter um token de acesso do código em execução em um navegador cliente. Você deve obter o token de acesso do código em execução em um servidor.  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md) e [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md) explicam como você pode obter um token de acesso.
  
    
    



```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

Essa solicitação seria um pouco diferente se você estivesse escrevendo seu suplemento no JavaScript usando a biblioteca entre domínios do SharePoint 2013. Nesse caso, você não precisaria fornecer um token de acesso. O código a seguir demonstra como seria essa solicitação se você estivesse usando a biblioteca entre domínios e quisesse receber a representação OData das listas como XML em vez de JSON (como Atom é o formato de resposta padrão, você não precisará incluir um cabeçalho **Accept**). Confira  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md) para saber mais sobre como usar a biblioteca entre domínios.
  
    
    



```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);
```

O código no exemplo a seguir mostra como solicitar uma representação JSON de todas as listas de um site usando C#. Ele pressupõe que você possua um token de acesso OAuth armazenado na variável  `accessToken`.
  
    
    



```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();

```


### Obtendo propriedades não retornadas com o recurso
<a name="NavigationProperties"> </a>

Muitos valores de propriedades são retornados quando você recupera um recurso mas, para algumas propriedades, é preciso enviar uma solicitação **GET** diretamente ao ponto de extremidade da propriedade. Isso é típico de propriedades que representam entidades do SharePoint.
  
    
    
O exemplo a seguir mostra como obter uma propriedade anexando o nome da propriedade ao ponto de extremidade do recurso. O exemplo obtém o valor da propriedade **Author** de um recurso **File**.
  
    
    
http:// _<url do site>_/_api/web/getfilebyserverrelativeurl('/ _<nome da pasta>_/ _<nome do arquivo>_')/author
  
    
    
Para receber os resultados no formato JSON, inclua um cabeçalho **Accept** definido como `"application/json;odata=verbose"`.
  
    
    

## Escrevendo dados usando a interface REST
<a name="WritingData"> </a>

Você pode criar e atualizar entidades do SharePoint criando solicitações HTTP RESTful para os pontos de extremidade apropriados, do mesmo modo que faz ao ler dados. Uma importante diferença, entretanto, é que você deve usar uma solicitação **POST**. Ao atualizar entidades, você também transfere um método de solicitação HTTP **PUT** ou **MERGE** adicionando um desses termos ao cabeçalho da sua solicitação, como o valor da chave **X-HTTP-Method**. O método **MERGE** atualiza apenas as propriedades da entidade que você especificar, enquanto o método **PUT** substitui a entidade existente por uma nova, fornecida no corpo de **POST**. Use o método **DELETE** para excluir a entidade. Ao criar ou atualizar uma entidade, você deverá fornecer uma representação do OData da entidade que deseja criar ou alterar no corpo da sua solicitação HTTP.
  
    
    
Outro ponto importante a ser considerado ao criar, atualizar e excluir entidades do SharePoint é que se você não estiver usando o OAuth para autorizar suas solicitações, essas operações exigirão o valor de resumo de formulário de solicitação como o valor do cabeçalho **X-RequestDigest**. Você pode recuperar esse valor fazendo uma solicitação **POST** com um corpo vazio para `http://<site url>/_api/contextinfo` e extraindo o valor do nó `d:FormDigestValue` no XML retornado pelo ponto de extremidade **contextinfo**. O exemplo a seguir mostra uma solicitação HTTP para o ponto de extremidade **contextinfo** no C#.
  
    
    



```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();

```

Se você estiver usando o fluxo de autorização e autenticação descrito em  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md), não será preciso incluir o resumo da solicitação em suas solicitações.
  
    
    
Se você estiver usando a biblioteca entre domínios do JavaScript, SP.RequestExecutor gerenciará o recebimento e o envio do valor do resumo do formulário para você.
  
    
    
Se você estiver criando um Suplemento do SharePoint hospedado no SharePoint, não será preciso fazer uma solicitação HTTP separada para recuperar o valor do resumo do formulário. Em vez disso, você poderá recuperar o valor no código do JavaScript da página do SharePoint (se a página utilizar a página mestra padrão), como mostrado no exemplo a seguir, que usa JQuery e cria uma lista.
  
    
    



```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});


```

O exemplo a seguir mostra como atualizar a lista criada no exemplo anterior. O exemplo altera o título da lista, usa JQuery e pressupõe que você esteja realizando essa operação em um suplemento hospedado no SharePoint.
  
    
    



```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});

```

O valor da chave **IF-MATCH** nos cabeçalhos de solicitação é o local onde você especifica o valor **etag** de uma lista ou de um item de lista. Esse valor específico se aplica apenas a listas e a itens de listas e ajuda você a evitar problemas de concorrência ao atualizar essas entidades. O exemplo anterior usa um asterisco (*) para esse valor, e você poderá usar esse valor sempre que não tiver motivos para se preocupar com problemas de simultaneidade. Do contrário, você deverá obter o valor **etag** ou uma lista ou um item de lista por meio de uma solicitação **GET** que recupere a entidade. Os cabeçalhos de resposta da resposta HTTP resultante transmitirão a etag com o valor da chave **ETag**. Esse valor também é incluído nos metadados da entidade. O exemplo a seguir mostra a marca de abertura de  `<entry>` para o nó XML que contém a lista de informações. A propriedade **m:etag** contém o valor **etag**.
  
    
    



```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">
```


## Criando um site com REST
<a name="bk_CreateSite"> </a>

Os exemplos a seguir mostram como criar um site no JavaScript.
  
    
    

```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});
```


## Como as solicitações REST diferem por ambiente
<a name="bk_HowRequestsDiffer"> </a>

A criação e o envio de uma solicitação HTTP podem variar de acordo com a linguagem, a biblioteca e o tipo de suplemento e, portanto, você não precisa alterar um ou mais componentes de solicitação ao traduzir uma solicitação de um ambiente para outro. Por exemplo, as solicitações jQuery AJAX usam os parâmetros **data** e **type** para especificar o corpo e tipo de solicitação, mas as solicitações de biblioteca entre domínios usam os parâmetros **body** e **method** para especificar esses valores.
  
    
    
As seções a seguir descrevem outras diferenças comuns entre ambientes.
  
    
    

### A maneira como você recebe e envia o valor do resumo do formulário depende do suplemento
<a name="FormDigest"> </a>

Quando você envia uma solicitação POST, a solicitação deve incluir o valor do resumo do formulário no cabeçalho do **X-RequestDigest**. Entretanto, a maneira como você recebe e envia o valor difere de acordo com o suplemento:
  
    
    

- Nos suplementos hospedados do SharePoint, você pode transferir apenas o seguinte cabeçalho: 
  
    
    
 "X-RequestDigest": $("#__REQUESTDIGEST").val()
    
  
- Em suplementos hospedados na nuvem que usam o OAuth, primeiro recupere o valor do resumo do formulário enviando uma solicitação para o ponto de extremidade **contextinfo** e depois adicionando-o às solicitações, como mostrado em [Escrevendo dados usando a interface REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData).
    
  
- Em suplementos hospedados na nuvem que usam a biblioteca entre domínios do JavaScript, não é preciso especificar o valor do resumo do formulário. Por padrão, o SP.RequestExecutor gerenciará isso automaticamente para você (ele também gerenciará o valor de content-length).
    
  

### Os suplementos que usam o OAuth devem passar os tokens de acesso nas solicitações
<a name="OAuthTokens"> </a>

Os suplementos hospedados na nuvem usam o OAuth ou a biblioteca entre domínios para autorizar o acesso aos dados do SharePoint. Os componentes do suplemento com um código executado em um servidor Web remoto devem usar o OAuth para autorizar o acesso aos dados do SharePoint. Nesse caso, você precisa incluir um cabeçalho **Authorization** para enviar o token de acesso. Confira [Lendo dados com a interface REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData) para obter um exemplo que adiciona uma autorização a um objeto **HTTPWebRequest**.
  
    
    

> [!OBSERVAçãO]
> Os componentes de suplemento hospedados na nuvem escritos em JavaScript devem usar o objeto **SP.RequestExecutor** na biblioteca entre domínios para acessar os dados do SharePoint. As solicitações de biblioteca entre domínios não precisam incluir um token de acesso.
  
    
    

Para saber mais sobre os tokens de acesso do OAuth e como obtê-los, confira  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md) e [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### Os URIs de pontos de extremidade em solicitações entre domínios usam o SP.AppContextSite para alterar o contexto
<a name="AppContextSite"> </a>

As solicitações são enviadas para o ponto de extremidade do recurso que é especificado na propriedade **url** da solicitação. As URIs do ponto de extremidade usam o seguinte formato:
  
    
    
 _<url do site>_/_api/ _<contexto>_/ _<recurso>_ (exemplo, https://contoso.com/_api/web/listas)
  
    
    
As solicitações de bibliotecas entre domínios usam esse formato ao acessar dados no Web de suplementos, que é o contexto padrão para solicitações de bibliotecas entre domínios. Mas para acessar os dados na Web do host ou em outro conjunto de sites, as solicitações precisam inicializar a Web do host ou outro conjunto de sites como o contexto. Para isso, elas usam o ponto de extremidade do **SP.AppContextSite** na URI, conforme mostrado na Tabela 1. As URIs de exemplo na Tabela 1 usam o alias **@target** para enviar a URL de destino na cadeia de caracteres de consulta porque a URL contém um caractere especial (':').
  
    
    

> [!OBSERVAçãO]
> Uma instância de Web de suplementos é necessária para que um suplemento hospedado na nuvem possa acessar dados do SharePoint ao usar a biblioteca entre domínios. 
  
    
    


**Tabela 1. Usando o ponto de extremidade SP.AppContextSite para alterar o contexto da solicitação**


|**Tipo de suplemento**|**Cenário de acesso de dados entre domínios**|**Exemplo de URI de ponto de extremidade**|
|:-----|:-----|:-----|
|Hospedado na nuvem  <br/> |Componente do suplemento JavaScript acessando os dados da Web do host usando a biblioteca entre domínios  <br/> | _<url da web do aplicativo>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<url da web do host>_'  <br/> |
|Hospedado na nuvem  <br/> |Componente de suplemento do JavaScript acessando os dados em um conjunto de sites diferente da Web do host, usando a biblioteca entre domínios (somente suplementos no escopo do locatário)  <br/> | _<url da web do aplicativo>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<url do site de destino>_'  <br/> |
|Hospedado no SharePoint  <br/> |Componente da Web de suplementos acessando os dados em outros conjuntos de sites (somente suplementos no escopo do locatário)  <br/> | _<url da web do aplicativo>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<url do site de destino>_'  <br/> |
   

> [!OBSERVAçãO]
> Os cenários de acesso de dados entre domínios também exigem permissões de suplemento apropriadas. Para saber mais, confira  [Acessar dados da Web do host](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb) e [Acessar dados entre conjuntos de sites](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope). 
  
    
    

Os Suplementos do SharePoint podem obter a URL da Web de suplementos da cadeia de caracteres de consulta da página do suplemento, conforme mostrado no exemplo de código a seguir. O exemplo também mostra como referenciar a biblioteca entre domínios, definida no arquivo SP.RequestExecutor.js na Web do host. O exemplo supõe que seu suplemento será iniciado do SharePoint. Confira  [Fluxo de OAuth do código de autenticação para o SharePoint Add-ins](authorization-code-oauth-flow-for-sharepoint-add-ins.md) para obter orientações sobre como configurar o contexto do SharePoint corretamente quando seu suplemento não for iniciado do SharePoint.
  
    
    



```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions
```


## Propriedades usadas em solicitações REST
<a name="bk_requestElements"> </a>

A Tabela 2 mostra as propriedades que são comumente usadas em solicitações HTTP para o serviço REST do SharePoint.
  
    
    

**Tabela 2. Quando usar as propriedades de solicitação REST em solicitações HTTP**


|**Propriedades**|**Quando é necessário**|**Descrição**|
|:-----|:-----|:-----|
|**url** <br/> |Todas as solicitações  <br/> |A URL do ponto de extremidade de recursos REST. Exemplo:  `http://<site url>/_api/web/lists` <br/> |
|**method** (ou **type**)  <br/> |Todas as solicitações  <br/> |O método de solicitação HTTP: **GET** para operações de leitura e **POST** para operações de escrita. As solicitações **POST** podem realizar operações de atualização ou exclusão especificando um verbo **DELETE**, **MERGE** ou **PUT** no cabeçalho do **X-HTTP-Method**.  <br/> |
|**body** (ou **data**)  <br/> |As solicitações **POST** que enviam dados no corpo da solicitação <br/> |O corpo da solicitação POST. Envia dados (como tipos complexos) que podem ser enviados na URI do ponto de extremidade. Usado com o cabeçalho **content-length**.  <br/> |
|Cabeçalho **Authentication** <br/> |Os suplementos remotos que estão usando o OAuth para autenticar usuários. Não se aplica ao uso do JavaScript ou da biblioteca entre domínios.  <br/> |Envia o token de acesso do OAuth (obtido a partir de um servidor de token seguro do Serviço de Controle de Acesso (ACS) da Microsoft) usado para autenticar o usuário para a solicitação. Exemplo:  `"Authorization": "Bearer " + accessToken`, em que  `accessToken` representa a variável que armazena o token. Os tokens devem ser recuperados usando o código do lado do servidor. <br/> |
|Cabeçalho **X-RequestDigest** <br/> |Solicitações **POST** (exceto solicitações SP.RequestExecutor) <br/> |Os suplementos remotos que usam o OAuth podem obter um valor do resumo do formulário a partir do ponto de extremidade  `http://<site url>/_api/contextinfo`. Os suplementos hospedados no SharePoint podem obter o valor a partir do controle da página **#__REQUESTDIGEST** se ele estiver disponível na página do SharePoint. Confira [Escrevendo dados usando a interface REST](#WritingData).  <br/> |
|Cabeçalho **accept** <br/> |As solicitações que retornam metadados do SharePoint  <br/> |Especifica o formato para dados de resposta do servidor. O formato padrão é  `application/atom+xml`. Exemplo:  `"accept":"application/json;odata=verbose"` <br/> |
|Cabeçalho **content-type** <br/> |As solicitações **POST** que enviam dados no corpo da solicitação <br/> |Especifica o formato dos dados que o cliente está enviando para o servidor. O formato padrão é  `application/atom+xml`. Exemplo:  `"content-type":"application/json;odata=verbose"` <br/> |
|Cabeçalho **content-length** <br/> |As solicitações **POST** que enviam dados no corpo da solicitação (exceto solicitações SP.RequestExecutor) <br/> |Especifica o comprimento do conteúdo. Exemplo:  `"content-length":requestBody.length` <br/> |
|Cabeçalho **IF-MATCH** <br/> |As solicitações **POST** para as operações **DELETE**, **MERGE** ou **PUT**, principalmente para alterar bibliotecas e listas.  <br/> |Oferece uma maneira para verificar se o objeto que está sendo alterado não foi alterado desde a última recuperação. Ou permite especificar para substituir qualquer alteração, como mostrado no exemplo a seguir:  `"IF-MATCH":"*"` <br/> |
|Cabeçalho **X-HTTP-Method** <br/> |Solicitações **POST** para operações **DELETE**, **MERGE** ou **PUT** <br/> |Usado para especificar que a solicitação realizará uma operação de atualização ou exclusão. Exemplo:  `"X-HTTP-Method":"PUT"` <br/> |
|**binaryStringRequestBody** <br/> |As solicitações **POST** do SP.RequestExecutor que enviam dados binários no corpo <br/> |Especifica se o corpo é uma cadeia de caracteres binária. **Boolean**.  <br/> |
|**binaryStringResponseBody** <br/> |As solicitações SP.RequestExecutor que retornam dados binários  <br/> |Especifica se a resposta é um cadeia de caracteres binária. **Boolean**.  <br/> |
   

## Suporte ao trabalho em lotes
<a name="batch"> </a>

O serviço REST do SharePoint Online (e a versão local do SharePoint 2016 e posterior) dá suporte à combinação de várias solicitações em uma única chamada para o serviço usando a opção de consulta  `$batch` do OData. Para obter mais detalhes e links para exemplos de códigos, confira [Fazem solicitações de lote com as APIs REST](make-batch-requests-with-the-rest-apis.md).
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Referência e amostras da API REST](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: Realizar operações básicas de acesso a dados em arquivos e pastas usando REST](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Protocolo Open Data](http://www.odata.org/)
    
  
-  [OData: Formato JavaScript Object Notation (JSON)](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  
-  [Definir permissões personalizadas em uma lista, usando a interface REST](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)
    
  

  
    
    

