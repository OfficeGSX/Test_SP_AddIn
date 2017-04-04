---
title: Conheça o serviço REST do SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 2de035a0-ac75-43bd-9665-5c5a59c4c590
---


# Conheça o serviço REST do SharePoint 2013
Obtenha as Noções básicas de como usar o serviço REST SharePoint 2013 para acessar e atualizar os dados do SharePoint, usando os padrões de protocolo da web REST e OData.
SharePoint 2013 introduz um serviço de transferência de estado representacional (REST) que é comparável ao SharePoint  [modelos de objeto de cliente](http://msdn.microsoft.com/library/88e5e1b9-eab2-4f3b-a3f2-75c96b86f1f4%28Office.15%29.aspx)existentes. Agora, os desenvolvedores podem interagir remotamente com dados do SharePoint usando qualquer tecnologia que ofereça suporte a solicitações da web REST. Isso significa que os desenvolvedores podem ser executadas **Create**, **Read**, **Update**e **Delete** (CRUD) operações de seus suplementos do SharePoint, soluções e aplicativos de cliente, usando tecnologias da web REST e sintaxe do Open Data Protocol (OData) padrão.
  
    
    


## Pré-requisitos

Este tópico pressupõe que você ter conhecimentos básicos REST e como construir solicitações REST.
  
    
    

## Como funciona o serviço REST de SharePoint 2013
<a name="bk_how"> </a>

SharePoint 2013 adiciona a capacidade de interagir remotamente com sites do SharePoint usando o REST. Agora, você pode interagir diretamente com objetos do SharePoint usando qualquer tecnologia que oferece suporte a recursos padrão do REST.
  
    
    
Para acessar os recursos do SharePoint usando REST, construa uma solicitação de HTTP por REST, usando o padrão de Open Data Protocol (OData), que corresponde à API de modelo de objeto do cliente desejado. Por exemplo:
  
    
    
 *Método de modelo de objeto do cliente:* 
  
    
    
List.GetByTitle(listname)
  
    
    
 *Ponto de extremidade do REST:* 
  
    
    
 `http://server/site/_api/lists/getbytitle('listname')`
  
    
    
O serviço web do SharePoint client.svc manipula a solicitação HTTP e serve a resposta apropriada no Atom ou JSON (JavaScript Object Notation) format. O aplicativo cliente deve ser analisado, em seguida, essa resposta. A figura a seguir mostra um modo de exibição de alto nível da arquitetura do REST do SharePoint.
  
    
    

**SharePoint REST service architecture**

  
    
    

  
    
    
![SharePoint REST service architecture](images/SPF15Con_REST_RESTStructure.png)
  
    
    
Devido a funcionalidade e facilidade de uso fornecer modelos de objeto do cliente, eles permanecem a opção de desenvolvimento principal para a comunicação com sites do SharePoint usando o .NET Framework código gerenciado, Silverlight ou JavaScript.
  
    
    

### Use os comandos HTTP com o serviço REST de SharePoint 2013
<a name="bk_usingHTTP"> </a>

Para usar os recursos do REST que são embutidos no SharePoint 2013, você construir uma solicitação HTTP por REST, usando o padrão de OData, que corresponde à API de modelo de objeto do cliente que deseja usar. O serviço da web client.svc manipula a solicitação HTTP e serve a resposta apropriada no formato Atom ou JavaScript Object Notation (JSON). O aplicativo cliente deve ser analisado, em seguida, essa resposta.
  
    
    
Os pontos de extremidade no serviço REST SharePoint 2013 correspondem aos tipos e membros em modelos de objeto do cliente do SharePoint. Usando solicitações HTTP, você pode usar esses pontos de extremidade do REST para executar as operações CRUD típicas nas entidades do SharePoint, como listas e sites.
  
    
    
Em geral:
  
    
    


|**Se você quiser fazer isso para um ponto de extremidade**|**Use essa solicitação HTTP**|**Tenha em mente**|
|:-----|:-----|:-----|
|Ler um recurso <br/> |**GET** <br/> ||
|Criar ou atualizar um recurso <br/> |**POST** <br/> |Use **POST** para criar entidades como listas e sites. O serviço REST SharePoint 2013 suporta envio comandos de **POST** que incluem definições de objeto aos pontos de extremidade que representam conjuntos. <br/> Para operações de **POST**, todas as propriedades que não são necessárias são definidas para seus valores padrão. Se você tentar definir uma propriedade de somente leitura como parte de uma operação de **POST**, o serviço retorna uma exceção. <br/> |
|Atualizar ou inserir um recurso <br/> |**PUT** <br/> |Use operações **PUT** e **MERGE** para atualizar os objetos do SharePoint existentes. <br/> Qualquer ponto de extremidade de serviço que representa uma operação de **set** de propriedade do objeto suporta as solicitações de **PUT** e **MERGE**. <br/> Para solicitações de **MERGE**, a definição das propriedades é opcional. todas as propriedades que você não definir explicitamente mantêm sua propriedade atual. <br/> Para solicitações de **PUT**, se você não especificar todas as propriedades obrigatórias nas atualizações do objeto, o serviço REST retorna uma exceção. Além disso, quaisquer propriedades opcionais que você não definir explicitamente estiver definidas como suas propriedades padrão. <br/> |
|Excluir um recurso <br/> |**DELETE** <br/> |Use o comando de **DELETE** HTTP contra a URL do ponto de extremidade específicos para excluir o objeto representado por esse ponto de extremidade do SharePoint. <br/> No caso de objetos recicláveis, como listas, arquivos e itens de lista, isso resulta em uma operação de **Recycle**. <br/> |
   

### Construir URLs REST para acessar os recursos do SharePoint
<a name="bk_constructURLs"> </a>

Sempre que possível, o URI para esses pontos de extremidade do REST bastante simule a assinatura de API do recurso no modelo de objeto do cliente do SharePoint. Os pontos de entrada principal para o serviço REST representam o conjunto de sites e site do contexto especificado.
  
    
    
Para acessar um conjunto de sites específico, use a seguinte construção:
  
    
    
 `http://server/site/_api/site`
  
    
    
Para acessar um site específico, use a seguinte construção:
  
    
    
 `http://server/site/_api/web`
  
    
    
Em cada caso,  *server*  representa o nome do servidor e *site*  representa o nome da ou o caminho, para o site específico.
  
    
    
Deste ponto inicial, você poderá então construir URIs mais específicas do REST 'percorrendo"o modelo de objeto, usando os nomes das APIs do modelo de objeto do cliente, separado por uma barra (/).
  
    
    
Essa sintaxe não se aplica ao SocialFeedManager ou SocialFollowingManager REST APIs. Consulte  [Social feed REST API reference for SharePoint 2013](http://msdn.microsoft.com/library/f1cb914f-1e91-4e23-bf53-d2ab323eac13%28Office.15%29.aspx) e [Seguintes pessoas e conteúdo Referência API REST para o SharePoint 2013](http://msdn.microsoft.com/library/c05755df-846d-4a39-941d-950d066cc6d4%28Office.15%29.aspx) para obter mais informações.
  
    
    
Consulte  [Determinar o ponto de extremidade do serviço do SharePoint REST URIs](determine-sharepoint-rest-service-endpoint-uris.md) para obter mais diretrizes para determinação do ponto de extremidade do REST do SharePoint URIs da assinatura do modelo de objeto de cliente correspondente APIs.
  
    
    

## Exemplos de ponto de extremidade do REST do SharePoint
<a name="bk_URLexamples"> </a>

A tabela a seguir contém exemplos típicos de URL de ponto de extremidade REST para você começar a trabalhar com dados do SharePoint. Preceda  `http://server/site/_api/` para a URL de fragmentos mostradas na tabela a construir uma URL totalmente qualificada do REST. Onde for necessário para os comandos **POST**, a tabela contém dados de amostra, que você deve passar no corpo da solicitação HTTP para criar o item especificado do SharePoint. Itens em itálico representam as variáveis que você deve substituir com os seus valores.
  
    
    


|**Descrição**|**Ponto de extremidade de URL**|**Método HTTP**|**Conteúdo do corpo**|
|:-----|:-----|:-----|:-----|
|Recupera o título de uma lista <br/> | `web/title` <br/> |Get <br/> |Não aplicável <br/> |
|Recupera todas as listas em um site <br/> | `lists` <br/> |Get <br/> |Não aplicável <br/> |
|Recupera um único ' metadados da lista <br/> | `lists/getbytitle('listname')` <br/> |Get <br/> |Não se aplica <br/> |
|Recupera os itens dentro de uma lista <br/> | `lists/getbytitle('listname')/items` <br/> |Get <br/> |Não aplicável <br/> |
|Recupera uma propriedade específica de um documento. (No caso, o título do documento.) <br/> | `lists/getbytitle('listname')?select=Title` <br/> |Get <br/> |Não aplicável <br/> |
|Cria uma lista <br/> | `lists` <br/> |postar <br/> |
```

{
  '_metadata':{'type':SP.List},
  'AllowContentTypes': true,
  'BaseTemplate': 104 ,
  'ContentTypesEnabled': true,
  'Description': 'My list description ',
  'Title': 'RestTest '
}
```

|
|Adiciona um item a uma lista <br/> | `lists/getbytitle('listname')/items` <br/> |postar <br/> |
```

{
  '_metadata':{'type':SP.listname ListItem},
  'Title': 'MyItem'
}

```

|
   

## Suporte de trabalho em lotes
<a name="batch"> </a>

SharePoint Online (e no local SharePoint 2016 ou posterior) combinando várias solicitações em uma única chamada para o serviço usando a opção de consulta OData  `$batch` oferece suporte a serviço REST. Para obter detalhes e links para exemplos de código, consulte [Fazem solicitações de lote com as APIs REST](make-batch-requests-with-the-rest-apis.md). .
  
    
    

## Recursos adicionais
<a name="bk_learnmore"> </a>

Use os recursos listados abaixo para saber mais sobre como usar o serviço REST do SharePoint.
  
    
    

|||
|:-----|:-----|
| [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |Saiba como realizar básica criar, ler, atualizar e excluir operações CRUD () com a interface REST do SharePoint 2013. <br/> |
| [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md) <br/> |Saiba como realizar básica criar, ler, atualizar e excluir operações CRUD () em listas e itens de lista com a interface REST do SharePoint 2013. <br/> |
| [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md) <br/> |Saiba como realizar básica criar, ler, atualizar e excluir operações CRUD () em pastas e arquivos com a interface REST do SharePoint 2013. <br/> |
| [Navegue a estrutura de dados do SharePoint representada no serviço REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md) <br/> |Saiba como iniciar a partir de um ponto de extremidade do REST para um determinado item do SharePoint e navegue até e acessar os itens relacionados, como sites pai ou a estrutura da biblioteca em que reside a esse item. <br/> |
| [Determinar o ponto de extremidade do serviço do SharePoint REST URIs](determine-sharepoint-rest-service-endpoint-uris.md) <br/> |Saiba as diretrizes gerais para determinação do ponto de extremidade do REST do SharePoint URIs da assinatura do modelo de objeto de cliente correspondente APIs. <br/> |
| [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md) <br/> |Saiba como usar operadores de cadeia de caracteres de consulta uma ampla gama de OData para selecionar, filtrar e solicitar os dados que você solicitar do serviço REST do SharePoint. <br/> |
| [Referência e amostras da API REST](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx) <br/> |Esta página contém links para todos os recursos do REST que estão disponíveis para os desenvolvedores de SharePoint 2013 no MSDN. <br/> |
| [Visão geral da API REST da Pesquisa do SharePoint](http://msdn.microsoft.com/library/8a4f7863-e4c1-4099-9189-a1894db36930%28Office.15%29.aspx) <br/> |Adicione a funcionalidade de pesquisa e aplicativos móveis para os clientes usando o serviço de Pesquisa REST no SharePoint Server 2013 e em qualquer tecnologia compatível com os requisitos da Web para REST. <br/> |
| [Social feed REST API reference for SharePoint 2013](http://msdn.microsoft.com/library/f1cb914f-1e91-4e23-bf53-d2ab323eac13%28Office.15%29.aspx) <br/> |Saiba mais sobre pontos de extremidade REST do SharePoint 2013 para tarefas relacionadas a feeds. <br/> |
| [Seguintes pessoas e conteúdo Referência API REST para o SharePoint 2013](http://msdn.microsoft.com/library/c05755df-846d-4a39-941d-950d066cc6d4%28Office.15%29.aspx) <br/> |Saiba mais sobre pontos de extremidade REST do SharePoint 2013 para o acompanhamento de pessoas e conteúdo. <br/> |
| [Fazem solicitações de lote com as APIs REST](make-batch-requests-with-the-rest-apis.md) <br/> |Aprenda a combinar várias solicitações em uma única chamada para o serviço REST. <br/> |
| [Sincronizar os itens do SharePoint usando o serviço REST](synchronize-sharepoint-items-using-the-rest-service.md) <br/> |Saiba como sincronizar itens entre o SharePoint e seus suplementos ou serviços usando o recurso de **GetListItemChangesSinceToken**, parte do serviço REST do SharePoint. <br/> |
| [Use valores de ETag através do serviço REST para obter a versão de item de lista do documento](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md) <br/> |Saiba como usar HTML ETags com o serviço REST do SharePoint para controle de simultaneidade de listas do SharePoint e itens de lista. <br/> |
   

## Recursos de OData
<a name="SP15startREST_bk_addlresources"> </a>


  
    
    

-  [Introdução ao OData](http://msdn.microsoft.com/pt-br/data/hh237663)
    
  
-  [Protocolo do Open Data by Example](http://msdn.microsoft.com/en-us/library/ff478141.aspx)
    
  
-  [Protocolo Open Data](http://www.odata.org/)
    
  
-  [Convenções URI do protocolo OData](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/)
    
  
-  [Lidando com as operações de serviço](http://www.odata.org/documentation/odata-version-2-0/uri-conventions#AddressingServiceOperations)
    
  
-  [Operações de protocolo OData](http://www.odata.org/documentation/odata-version-2-0/operations/)
    
  
-  [Condições de erro](http://www.odata.org/documentation/odata-version-2-0/operations#ErrorConditions)
    
  

