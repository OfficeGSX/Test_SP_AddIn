---
title: Navegue a estrutura de dados do SharePoint representada no serviço REST
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# Navegue a estrutura de dados do SharePoint representada no serviço REST
Saiba como iniciar a partir de um ponto de extremidade do REST para um determinado item do SharePoint e navegue até e acessar os itens relacionados, como sites pai ou a estrutura da biblioteca em que reside a esse item.
## Navegue de uma determinada URL para chegar a outros recursos do SharePoint

Quando você está trabalhando com o serviço REST do SharePoint, você geralmente vai começam com saber a URL de um item específico do SharePoint, mas deseja acessar itens relacionados, como a estrutura de pasta ou biblioteca onde esse item reside. Por exemplo, suponha que você crie um suplemento onde o usuário insere a URL de um documento em uma biblioteca do SharePoint. Seu suplemento deve então romper essa URL para descobrir a URL do site do SharePoint real. Depois que ele tiver feito isso, o suplemento pode então fazer mais solicitações do site em nome do usuário, tais como para criar, atualizar ou excluir itens relacionados ou recursos.
  
    
    
Para fazer isso, seu suplemento precisa consultar SharePoint as seguintes informações:
  
    
    

- As URLs relativas do servidor do site e que contém o recurso de conjunto de sites
    
  
- Um resumo do formulário para habilitá-lo realizar solicitações que alteram o estado do recurso, **POST**, **PUT**, **MERGE**e **DELETE**
    
  
O processo básico:
  
    
    

1. Use o operador de  `/contextinfo` com a URL fornecida para acessar o site e endereços de conjunto de sites e o resumo do formulário. Use o operador de `/contextinfo` no seguinte formato:
    
     `http://server/web/doclib/forms/_api/contextinfo`
    
    Para aumentar a segurança contra tentativas de scripts intersite, os  `/contextinfo` operador aceita somente as solicitações de **POST**.
    
  
2. Use as  [Propriedades do objeto SPContextWebInformation](#bk_props) que o operador `/contextinfo` retorna para acessar recursos adicionais conforme desejado.
    
  
 **Try it**
  
    
    

1. Comece com uma URL para um determinado item do SharePoint. Por exemplo:
    
     `http://site/web/doclib/myDocument.docx`
    
  
2. Remova o nome do recurso específico da extremidade do URL, para que a URL aponta para uma biblioteca de documentos, pasta ou lista. Neste caso:
    
     `http://site/web/doclib/`
    
  
3. Acrescente a URL do ponteiro do serviço REST e o operador de  `/contextinfo` :
    
     `http://site/web/doclib/_api/contextinfo`
    
  
4. Leia o formulário propriedades digest e **webFullUrl** da resposta.
    
  
5. Acrescente o de ponteiro do serviço REST  `_api` a URL da web
    
  
6. Use o resultante URL e o resumo do formulário para fazer solicitações de outros recursos que você precisa.
    
  
Você não precisa passar o resumo do formulário, se você estiver fazendo solicitações **GET** ou fazendo solicitações usando um token de OAuth validado.
  
    
    

## Navegue sites pai e filho
<a name="bk_sites"> </a>

Quando você navega sua estrutura de site usando o modelo de objeto do SharePoint server, você usar as propriedades  [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) e [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) para acessar objetos que representam sites pai e filho.
  
    
    
Os recursos correspondentes do REST  `web/parentweb` e `web/webs` não retornam objetos que representam os sites. Isso ocorre porque o serviço REST está em conformidade com os padrões de OData e site completo representações em retorno tornaria essas solicitações ineficientes. Em vez disso, elas retornam um  [Objeto WebInfo](#bk_webinfo) que contém propriedades escalares do site, mas sem entidade relacionada define como como coleções ou conjuntos de campo.
  
    
    
Para navegar até um pai específico ou site filho, construa a URL adequada do REST nesse site usando a propriedade **Id** ou **Title** como. A partir daí, você pode acessar os conjuntos de entidade relacionada desse site.
  
    
    

## Estrutura de pastas de navegação
<a name="bk_folders"> </a>

O serviço REST do SharePoint não suporta atravessando a hierarquia de pastas de um site por meio da construção de URL. Em vez disso, use o equivalente do restante do método  [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) . Por exemplo:
  
    
    
 *Não é suportada por meio do serviço REST de navegação:* 
  
    
    
 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`
  
    
    
Navegação que é suportada pelo serviço REST:
  
    
    
 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`.
  
    
    

## Propriedades do objeto SPContextWebInformation
<a name="bk_props"> </a>



|**Propriedade SPContextWebInformation**|**Descrição**|
|:-----|:-----|
|**webFullUrl** <br/> |Obtém a URL relativa de servidor do site mais próximo. <br/> |
|**siteFullUrl** <br/> |Obtém a URL relativa de servidor da raiz do que o site está contido no conjunto de sites. <br/> Se o mais próximo da web é a raiz de um conjunto de sites, o valor da propriedade **webFullUrl** é igual à propriedade **siteFullUrl**. <br/> |
|**formDigestValue** <br/> |Obtém o resumo do formulário de solicitação do servidor. <br/> |
|**LibraryVersion** <br/> |Obtém a versão atual da biblioteca REST. <br/> |
|**SupportedSchemaVersions** <br/> |Obtém as versões suportadas do esquema da biblioteca REST/CSOM. <br/> |
   

## Objeto WebInfo
<a name="bk_webinfo"> </a>



|**Propriedade WebInfo**|**Descrição**|
|:-----|:-----|
|**Created** <br/> |Obtém um valor que especifica quando o site foi criado. <br/> |
|**Description** <br/> |Obtém ou define a descrição para o site. <br/> |
|**Id** <br/> |Obtém um valor que especifica o identificador do site. <br/> |
|**Language** <br/> |Obtém um valor que especifica o ID de localidade (LCID) para o idioma usado no site do. <br/> |
|**LastItemModifiedDate** <br/> |Obtém um valor que especifica quando um item foi modificado pela última vez no site. <br/> |
|**Title** <br/> |Obtém ou define o título para o site. <br/> |
|**WebTemplateId** <br/> |Obtém o identificador do modelo de site. <br/> |
   

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Conheça o serviço REST do SharePoint 2013](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [Trabalhar com listas e itens de listas com REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [Trabalhando com pastas e arquivos com REST](working-with-folders-and-files-with-rest.md)
    
  
-  [Determinar o ponto de extremidade do serviço do SharePoint REST URIs](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [Referência e amostras da API REST](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [Sincronizar os itens do SharePoint usando o serviço REST](synchronize-sharepoint-items-using-the-rest-service.md)
    
  
-  [Use valores de ETag através do serviço REST para obter a versão de item de lista do documento](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

