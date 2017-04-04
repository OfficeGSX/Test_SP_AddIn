---
title: Usar operações de consulta de OData em solicitações REST do SharePoint
ms.prod: SHAREPOINT
ms.assetid: d4b5c277-ed50-420c-8a9b-860342284b72
---



# Usar operações de consulta de OData em solicitações REST do SharePoint
Saiba como usar os operadores de seqüência de consulta uma ampla variedade de OData para selecionar, filtrar e ordenar os dados de que solicitação do serviço SharePoint REST.
 **Antes de começar**
  
    
    


-  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Navegue a estrutura de dados do SharePoint representada no serviço REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [Determinar o ponto de extremidade do serviço do SharePoint REST URIs](determine-sharepoint-rest-service-endpoint-uris.md)
    
  

O serviço REST SharePoint suporta um operadores de cadeia de caracteres de consulta ampla variedade de OData que permitem que você selecione, filtrar e ordenar os dados que você solicitar.
  
    
    


> **DICA**
> O serviço REST do SharePoint Online (e a versão local do SharePoint 2016 e posterior) dá suporte à combinação de várias solicitações em uma única chamada para o serviço usando a opção de consulta  `$batch` do OData. Para obter mais detalhes e links para exemplos de códigos, confira [Fazem solicitações de lote com as APIs REST](make-batch-requests-with-the-rest-apis.md).
  
    
    


## Selecionar campos para retornar

Use a opção de consulta  [$select](http://www.odata.org/documentation/odata-version-2-0/uri-conventions#SelectSystemQueryOption) para especificar quais campos para retornar para uma determinada lista, item de lista ou outro objeto do SharePoint são representados por um conjunto de entidade. Você pode usar `$select=*` para retornar todos os campos disponíveis.
  
    
    

> **OBSERVAçãO**
> Em geral, se você não especificar a opção de consulta  `$select` , o serviço REST retorna todos os campos disponíveis por padrão. No entanto, em alguns casos, alguns objetos SharePoint incluem propriedades que são recursos com muita intensidade para recuperar; para otimizar o desempenho do serviço restante, essas propriedades não são incluídas na consulta padrão e devem ser explicitamente solicitadas.> Por exemplo, a propriedade **SPWeb.EffectiveBasePermissions** não é retornada por padrão e deve ser solicitada explicitamente usando a opção de consulta `$select` .
  
    
    

Além disso, você pode especificar que a solicitação retorna campos projetados de outras listas e os valores das pesquisas. Para fazer isso, especifique o nome de campo em  `$select` tanto o `$expand` opções de consulta. Por exemplo:
  
    
    
 `http://server/site/_api/web/lists('guid')/items?$select=Title,Products/Name&amp;$expand=Products/Name`
  
    
    
Não há suporte para seleção de itens relacionados e expansão em massa.
  
    
    

## Selecionar itens para retornar

Use a opção de consulta  [$filter](http://www.odata.org/documentation/odata-version-2-0/uri-conventions#FilterSystemQueryOption) para selecionar quais itens para retornar. [Operadores de consulta do OData compatíveis compatíveis o serviço REST do SharePoint](#bk_supported) lista as funções que você pode usar com o serviço do SharePoint REST e opções de comparação de consulta de filtro.
  
    
    

## Consulta campos de pesquisa de valor único

Campos de pesquisa de valor único são representados por dois campos separados no serviço do SharePoint REST: um campo que representa o valor real do campo e outra que representa o nome do campo. Você pode executar consultas com relação ao valor do campo de pesquisa como faria com qualquer outro campo desse tipo de dados. Por exemplo, se o valor do campo de pesquisa não for uma cadeia de caracteres, você pode usar as opções de comparação de cadeia de caracteres em sua consulta.
  
    
    

## Consulta para usuários

No serviço REST SharePoint, os usuários são representados pelo nome do usuário amigável (exibição) e não seu combinação alias ou domínio \\ alias. Portanto, você deve construir consultas de usuário em nomes amigáveis dos usuários.
  
    
    

> **OBSERVAçãO**
> Não há suporte para consultas de usuário baseada em associação.> Não há suporte para o uso do operador **Current** fazer consultas usando a ID do usuário atual.
  
    
    


## Consulta para usuários e campos de pesquisa de múltiplos valores

Como campos de pesquisa de múltiplos valores são retornados como uma cadeia de caracteres de vários valores, não há nenhuma maneira de consulta para eles (por exemplo, o equivalente de um elemento de **Includes** ou elemento de **NotIncludes** não é suportado).
  
    
    

## Classificar itens devolvido

Use a opção de consulta  [$orderby](http://www.odata.org/documentation/odata-version-2-0/uri-conventions#OrderBySystemQueryOption) para especificar como classificar os itens em seu conjunto de retorno da consulta. Para classificar por vários campos, especifique uma lista separada por vírgulas de campos. Você também pode especificar se deseja classificar os itens em ordem crescente ou decrescente anexando a palavra-chave **asc** ou **desc** à sua consulta.
  
    
    

## Página por meio de itens devolvidos

Use as opções de consulta  [$top](http://www.odata.org/documentation/odata-version-2-0/uri-conventions#TopSystemQueryOption) e [$skiptoken](http://msdn.microsoft.com/library/dd942121.aspx) para selecionar um subconjunto dos itens que caso contrário, seria retornado pela sua consulta.
  
    
    

> **OBSERVAçãO**
> A opção de consulta $skip não funciona com consultas para itens de lista do SharePoint.
  
    
    

A opção  `$top` permite que você selecione os primeiros itens de *n*  do retorno definido para retorno. Por exemplo, o seguinte URI solicita que apenas os dez primeiros itens no conjunto de retorno potencial realmente ser retornado:
  
    
    
 `http://server/site/_api/web/lists('<guid>')/items$top=10`
  
    
    
A opção $skiptoken permite ignorar itens até que o item especificado é alcançado e devolver o restante.
  
    
    
 `$skiptoken=Paged=TRUE&amp;p_ID=5`
  
    
    

> **OBSERVAçãO**
> Ao usar essas opções de consulta, leve em consideração que paginação no OData é ordinal. Por exemplo, suponha que você está implementando um botão próxima página para exibir itens de lista do SharePoint. Você usar o serviço REST para habilitar o botão retornar itens 1 a 20 quando clicado, e itens 21 até 40 e assim por diante. No entanto, suponha que outro usuário exclui itens 4 e 18 entre cliques do botão próximo. Nesse caso, o posicionamento ordinal dos itens restantes é redefinido e realmente exibindo itens 21 a 40 ignora mais de dois itens.
  
    
    


## Operadores de consulta do OData compatíveis compatíveis o serviço REST do SharePoint
<a name="bk_supported"> </a>



|**Com suporte**|**Sem suporte**|
|:-----|:-----|
|**Comparações numéricas** <br/> Lt <br/> Entidade legal <br/> Gt <br/> GE <br/> EQ <br/> Ne <br/> |Operadores aritméticos            (Adicionar, Sub, Mul, Div, Mod) <br/> Funções matemáticas básicas            (round, plantas, teto) <br/> |
|**Comparações de cadeia de caracteres** <br/> startsWith <br/> substringof <br/> EQ <br/> Ne <br/> |endsWith <br/> Substituir <br/> subcadeia de caracteres <br/> ToLower <br/> ToUpper <br/> Cortar <br/> concat <br/> |
|**Funções de data e hora** <br/> Day() <br/> Month() <br/> Year() <br/> Hour() <br/> Minute() <br/> Second() <br/> |Operador de DateTimeRangesOverlap <br/> Consultando como para se uma data hora cai dentro de um padrão de tempo de data recorrente <br/> |
   
A figura a seguir mostra as opções de consulta OData com suporte.
  
    
    

**Opções de consulta OData compatíveis**

  
    
    

  
    
    
![SharePoint REST service query option syntax](images/SPF15Con_REST_queryOptionSyntax.png)
  
    
    

  
    
    

  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Navegue a estrutura de dados do SharePoint representada no serviço REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [Determinar o ponto de extremidade do serviço do SharePoint REST URIs](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Referência e amostras da API REST](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  

  
    
    
