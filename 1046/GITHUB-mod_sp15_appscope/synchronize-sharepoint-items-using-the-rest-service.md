---
title: Sincronizar os itens do SharePoint usando o serviço REST
ms.prod: SHAREPOINT
ms.assetid: 7f85a4ec-594d-4eee-9c1a-7a5ad232e088
---


# Sincronizar os itens do SharePoint usando o serviço REST
Saiba como sincronizar itens entre o SharePoint e seus suplementos ou serviços usando o recurso de **GetListItemChangesSinceToken**, parte do serviço REST do SharePoint.
## Sincronizando itens do SharePoint usando o recurso de GetListItemChangesSinceToken

Se você deseja sincronizar itens entre o SharePoint e seus suplementos ou serviços, você pode usar o recurso de **GetListItemChangesSinceToken** para fazê-lo. O **GetListItemChangesSinceToken**, parte do serviço REST do SharePoint, corresponde à chamada  [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) web service.
  
    
    
Executar uma solicitação de **POST** que inclui um [SP. Propriedades do objeto ChangeLogItemQuery](#bk_props) objeto no corpo da solicitação.
  
    
    
A solicitação retorna ADO **rowset** XML que inclui as linhas que correspondam a qualquer alteração de item de lista correspondentes a consulta especificada. Para obter mais informações sobre essas propriedades, incluindo as estruturas de dados de propriedade, descrições de elemento CAML e valores de retorno, consulte [Lists.GetListItemChangesSinceToken](https://msdn.microsoft.com/library/WebSvcLists.Lists.GetListItemChangesSinceToken.aspx) .
  
    
    

||
|:-----|
|**Exemplo de solicitação** <br/> |
| `POST http://server/site/_api/web/Lists/GetByTitle('Announcements')/GetListItemChangesSinceToken` <br/> |
|**Corpo de POSTAGEM de exemplo** <br/> |
|
```XML

{ 'd' : {
  'query': { 
    '__metadata': { 'type': 'SP.ChangeLogItemQuery'}, 
    'ViewName': '', 
    'Query': '<Where>
      <Contains>
         <FieldRef Name="Title" />
         <Value Type='Text'>Te</Value>
      </Contains></Where>',
    'QueryOptions': '<QueryOptions>
      <IncludeMandatoryColumns>FALSE</IncludeMandatoryColumns>
      <DateInUtc>False</DateInUtc>
      <IncludePermissions>TRUE</IncludePermissions>
      <IncludeAttachmentUrls>FALSE</IncludeAttachmentUrls>
      <Folder>Shared Documents/Test1</Folder></QueryOptions>', 
    'ChangeToken':'1;3;eee4c6d5-f88a-42c4-8ce1-685122984870;634397182229400000;3710', 
    'Contains':'<Contains>
      <FieldRef Name="Title"/>
      <Value Type="Text">Testing</Value></Contains>' } 
  } 
}

```

|
   

## SP. Propriedades do objeto ChangeLogItemQuery
<a name="bk_props"> </a>


****


|**Propriedade**|**Descrição**|
|:-----|:-----|
|**ListName** <br/> |Uma cadeia de caracteres que contém o título ou o GUID da lista. Ao consultar a tabela UserInfo, a cadeia de caracteres contém UserInfo. Usando os resultados GUID em um melhor desempenho. <br/> |
|**ViewName** <br/> |Uma cadeia de caracteres que contém a GUID para o modo de exibição, que determina o modo de exibição a ser usado para os atributos de exibição padrão representados pelos parâmetros  _query_,  _viewFields_e  _rowLimit_. Se esse argumento não for fornecido, o modo de exibição padrão é assumido. Se ele for fornecido, o valor do parâmetro _query_,  _viewFields_ou  _rowLimit_ substitui a configuração equivalente do modo de exibição. Por exemplo, se o modo de exibição especificado pelo parâmetro _viewFields_ tem um limite de linha de 100 linhas, mas o parâmetro _rowLimit_ contém um valor de 1000, 1.000 linhas são retornadas na resposta. <br/> |
|**Query** <br/> |Um elemento de  [consulta](http://msdn.microsoft.com/en-us/library/ms471093.aspx) contendo a consulta que determina quais registros são retornados e em qual ordem. <br/> |
|**QueryOptions** <br/> |Um fragmento XML no seguinte formato que contém nós separados para as várias propriedades do objeto  [SPQuery](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPQuery.aspx) . <br/> |
|**ChangeToken** <br/> |Uma cadeia de caracteres que contém o token de alteração para a solicitação. Para obter uma descrição do formato que é usado nesta seqüência de caracteres, consulte  [Visão geral do Log de alteração](http://msdn.microsoft.com/en-us/library/bb417456.aspx). Se null é passado, todos os itens na lista serão retornados. <br/> |
|**Contains** <br/> |Um elemento  [contém](http://msdn.microsoft.com/en-us/library/ms196501.aspx) que define a filtragem personalizada para a consulta. <br/> |
   

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Navegue a estrutura de dados do SharePoint representada no serviço REST](navigate-the-sharepoint-data-structure-represented-in-the-rest-service.md)
    
  
-  [Determinar o ponto de extremidade do serviço do SharePoint REST URIs](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [Referência e amostras da API REST](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [Use valores de ETag através do serviço REST para obter a versão de item de lista do documento](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

