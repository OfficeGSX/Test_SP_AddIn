---
title: Obtenha uma visão geral do modelo de objeto do SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6b8b55f8-9370-43a0-af8d-e07d1028a075
---


# Obtenha uma visão geral do modelo de objeto do SharePoint
Obtenha uma introdução rápida algumas das classes principais no modelo de objeto do SharePoint.
Este é o quarto em uma série de artigos sobre noções básicas do desenvolvimento hospedado em provedor Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Dar seu suplemento a aparência do SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [Incluir um botão personalizado do add-in](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  

> [!OBSERVAçãO]
> Se você tiver trabalhado através desta série sobre hospedado em provedor suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) e abra o arquivo BeforeSharePointWriteOps.sln.
  
    
    

Neste artigo, você terá uma breve quebra da codificação para obter uma rápida visão geral do modelo de objeto do cliente do SharePoint (CSOM). Este modelo é grande e bem documentados no MSDN com tópicos de referência, "como" s e códigos de exemplo. Neste artigo, podemos fornecer apenas a ponta da dica da ponta do iceberg. Mas ainda é uma introdução muito curta tornará boa parte do código, que você verá nesta série muito menos misteriosos.
## Hierarquia de conteúdo

A tabela a seguir mostra a hierarquia de conteúdo no SharePoint e as classes CSOM representá-los. Cada uma dessas entidades tem filhos do tipo logo abaixo dele.
  
    
    

|
|
|**Entidade**|**Aula**|**Comentários**|
|:-----|:-----|:-----|
|SharePoint farm ou SharePoint Online assinatura (também chamado de um inquilino) local <br/> ||Não há apenas limitado acesso programático a este nível na CSOM. Não há nenhuma classe de Farm ou inscrição ou inquilino, por exemplo. (Modelo de objeto do lado do servidor do SharePoint, que não pode ser usado em suplementos, permite acesso programático a essas entidades.) <br/> |
|conjunto de sites <br/> |**Site** <br/> |Uma coleção de sites que são grupos de segurança agrupados juntos para principalmente razões administrativas e para os componentes do SharePoint house, como páginas mestras com marca, ou personalizados, que podem ser aplicados a todos os sites filhos. Todos os sites pertencem a alguns conjunto de sites. <br/> |
|. <br/> |**Web** <br/> |Um conjunto de páginas e componentes do SharePoint. Pode ter subwebsites. <br/> |
|List <br/> |**List** <br/> |Bibliotecas de documentos e outros tipos de bibliotecas de arquivo também estão neste nível. <br/> Uma biblioteca de documentos é um tipo especial de lista na qual cada linha inclui um documento anexado e as outras colunas são dados sobre o documento, como seu autor, quando ele foi editado pela última vez, e quem está com check-out. <br/> |
|Item de Lista <br/> |**ListItem** <br/> |Uma linha em uma lista  ou seja, um item de lista  com determinado valores nos campos da linha. Também tem um tipo. Consulte a próxima linha. <br/> |
|Item de Lista <br/> |**Content Type** <br/> |O tipo de um item de lista. Esses itens são representados pela classe **ContentType**. Cada uma é, basicamente, um conjunto de colunas e metadados. A forma mais simples é o tipo de conteúdo interna **Item**. Todos os outros tipos de conteúdo são derivados do **Item**. SharePoint inclui muitos tipos de conteúdo internos, como o evento e anúncio. <br/> |
|Coluna <br/> |**Field** <br/> |Um objeto **Field** inclui não apenas informações sobre o tipo de dados subjacente, mas também informações sobre como os dados são formatados e renderizados em formulários, como os formulários de criação, exibição e edição de itens de lista específica. <br/> |
   

  
    
    
Programaticamente, você pode criar listas personalizadas, tipos de conteúdo, tipos de coluna e itens de lista.
  
    
    
Além de conteúdo, o CSOM do fornece acesso a usuários, grupos, funções e permissões, taxonomia, pesquisa e mais.
  
    
    

## Tempo de execução do cliente e o processamento em lotes
<a name="CSOMBatching"> </a>

CSOM usa um sistema de lote. Blocos de código gerenciado é convertido em XML e enviadas para o servidor em uma única solicitação HTTP. Para cada comando, é feita uma chamada de modelo de objeto correspondente do servidor e o servidor retorna uma resposta ao cliente em formato JavaScript Object Notation (JSON).
  
    
    
Código do SharePoint em um cliente começa recuperando um objeto de contexto de cliente que representa o contexto da solicitação atual, incluindo a identidade do site do SharePoint (e seu conjunto de sites pai) e por meio de nesse contexto, você pode obter acesso a objetos CSOM. Veja a seguir a estrutura básica que você verá repetidamente. Observe o seguinte sobre este código:
  
    
    

- O objeto  `spContext` é do tipo **SharePointContext** e é definido em um o arquivo SharePointContext.cs (ou. vb) que seja gerado pelo Office Developer Tools for Visual Studio. Esse arquivo pode ser modificado, mas não é sempre que você precisará fazê-lo. Na maioria dos projetos Suplemento do SharePoint, esse arquivo e o arquivo TokenHelper.cs (ou. vb) que também é gerado pelas ferramentas do efetivamente funcionam como extensões dos CSOM em si.
    
  
- O objeto de  `clientContext` é o tipo CSOM **ClientContext**.
    
  
- O método **ExecuteQuery** empacota backup do código de operação CRUD no e a mensagem XML que ele envia ao servidor do SharePoint. Há, ela é convertida em código do modelo de objeto equivalente do lado do servidor e executados.
    
  



```cs

using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    // CRUD operation or query code goes here.

    clientContext.ExecuteQuery();
}
```

Houve um exemplo desse padrão no artigo anterior desta série, no método  `GetLocalEmployeeName` mostrado abaixo. Observe o seguinte sobre esse método:
  
    
    

- As duas primeiras linhas no bloco de **using** aparecem obter referências à lista e o objeto de item de lista. Mas realmente quando essas linhas executados no tempo de execução de cliente do SharePoint, eles simplesmente são convertidos em formato XML. O método **ExecuteQuery** envia que XML para o servidor.
    
  
- O método **Load** adiciona algo extra à mensagem: informa o servidor para enviar o objeto especificado para baixo até o cliente. O método **ExecuteQuery** recebe este objeto (como JSON) e o utiliza para inicializar a variável do lado do cliente `selectedLocalEmployee` . No lado do cliente subsequente de código e faça referência a esse objeto **ListItem** e seus membros. Como você pode ver, a próxima linha referencia o valor do campo de "Title" do item. Esta linha teria lançado uma exceção se o método **Load** não ter sido chamado porque o objeto não existe realmente no lado do cliente até seu carregado.
    
  



```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
```


## 
<a name="Nextsteps"> </a>

No próximo artigo, podemos voltar a codificação e saiba como gravar dados para o SharePoint:  [Adicionar operações de gravação do SharePoint para o suplemento](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
  
    
    

