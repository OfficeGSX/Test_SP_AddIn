---
title: Desenvolver suplementos do SharePoint
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# Desenvolver suplementos do SharePoint
Encontre detalhados artigos e recursos para ajudá-lo a criar recursos avançados na sua Suplementos do SharePoint.
> **OBSERVAçãO**
> Este artigo pressupõe que você está familiarizado com o artigo  [Suplementos do SharePoint](sharepoint-add-ins.md) e o guia de Introdução material que ela está vinculada.
  
    
    

Sob **Develop**, temos o seguinte para ajudar a explicar todas as coisas diferentes que você pode fazer em um Suplemento do SharePoint:
- Visões gerais profundo
    
  
- Artigos de instruções
    
  
- Trechos de código
    
  
Você encontrará artigos sobre:
- Realizando criar, ler, atualizar e excluir operações CRUD () em listas
    
  
- Como construir consultas REST e interagir com as novas APIs
    
  
- Como e quando configurar OAuth para segurança
    
  
SharePoint tem a recursos sociais corporativos como feeds de atividade e perfis de usuário, juntamente com os recursos de gerenciamento de conteúdo corporativo, recursos de interoperabilidade linha de negócios (LOB) e recursos de design do site que podem realmente fazer seus suplementos destacarem. Saiba mais sobre-los no  [Adicionar recursos do SharePoint 2013](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx).E, código é a chave, portanto, dê uma olhada no menu "Exemplos" no Centro de desenvolvimento. É um link direto para nossos amostras de código para suplementos. Assim que você definiu seu ambiente de desenvolvimento, você deve check-out de alguns dos nossos exemplos. Beneficie-se de um recurso de comunidade que permite que você solicite um exemplo de código, se não temos um que você gostaria de ver. Podemos tomar essas solicitações, junto com os outros comentários doc e usá-los no nossas atualizações contínuas o conteúdo e exemplos. Portanto, informe-nos se você gostaria de ver algo!
## Introdução ao Suplementos do SharePoint recursos
<a name="bk_gettingstarted"> </a>

Se você estiver apenas Introdução ao desenvolvimento Suplementos do SharePoint, primeiro dar uma olhada um  [Suplementos do SharePoint](sharepoint-add-ins.md). Nessa página você aponta para os principais artigos para ajudá-lo rapidamente se familiarizar com os diferentes tipos de Suplementos do SharePoint. Antes de fazer mais avançados tipos de desenvolvimento com o Suplementos do SharePoint, você deve começar com uma boa ideia dos tipos de suplementos que você deseja compilar e as tecnologias que convém incluir as opções de hospedagem que você vai querer usar.
  
    
    

### Tarefas essenciais e recursos para o desenvolvimento de Suplementos do SharePoint usando o modelo de objeto do cliente, o modelo de objeto JavaScript e pontos de extremidade do REST em SharePoint 2013
<a name="bk_essentials"> </a>

Não importa qual tipo de Suplemento do SharePoint você decidir criar, seu suplemento irá sempre interagir de alguma maneira com um site SharePoint 2013. Os artigos na tabela 1 descrevem como fazer muitos dos mais importantes tipos de trabalho com sites do SharePoint usando três interfaces que estão disponíveis para uso em sua Suplementos do SharePoint: modelo de objeto do cliente, o modelo de objeto do JavaScript e pontos de extremidade do REST.
  
    
    

**Tabela 1. Operações básicas com o modelo de objeto do cliente SharePoint 2013, o modelo de objeto JavaScript e interface REST**


|**Tópico**|**Descrição**|
|:-----|:-----|
| [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |Explica como fazer operações comuns usando c# e o modelo de objeto do cliente. <br/> |
| [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |Explica como fazer operações comuns usando o modelo de objeto do JavaScript. <br/> |
| [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |Explica como fazer operações comuns usando a interface REST. <br/> |
   

## Aprenda os conceitos fundamentais para o desenvolvimento com Suplementos do SharePoint
<a name="bk_fundamentals"> </a>

Além da compreensão as operações básicas, você deve compreender os conceitos fundamentais do modelo de desenvolvimento do suplemento do SharePoint 2013. Cada tipo de Suplemento do SharePoint contém um arquivo de manifesto suplemento e é incorporado a um pacote de suplemento que você implantar em um site SharePoint 2013. E, ao desenvolver qualquer tipo de suplemento que você deve considerar uma gama de problemas relacionados à autenticação e autorização, usabilidade e acesso a dados. Os artigos na tabela 2 apresentar esses problemas e explicam suas implicações para qualquer tipo de que você deseja criar.
  
    
    

**Tabela 2. Conceitos fundamentais para trabalhar com Suplementos do SharePoint**


|**Tópico**|**Descrição**|
|:-----|:-----|
| [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |Orientações principais conceitos relacionados ao adquirir os privilégios necessários para trabalhar com os recursos de SharePoint 2013. <br/> |
| [Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |Explica como suplemento manifestos de pacotes de trabalho e como suplemento são criados. <br/> |
| [Criar componentes UX do SharePoint 2013](create-ux-components-in-sharepoint-2013.md) <br/> |Explora das formas nas quais você pode criar uma experiência de usuário rica em Suplementos do SharePoint. <br/> |
| [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md) <br/> |Explica as opções de acesso de dados e técnicas que estão disponíveis em diferentes tipos de Suplementos do SharePoint. <br/> |
| [Licenciar seu Office e SharePoint complementos](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |Orienta a estrutura de licença do suplemento do Suplementos do Office e do SharePoint. <br/> |
   

## Juntar as peças: construção avançadas suplementos do SharePoint com a integração de recursos
<a name="bk_integrate"> </a>

Quando você está familiarizado com as capacidades e recursos do Suplementos do SharePoint, você pode começar a construir suplementos mais complexos, colocando todas as partes juntas, de modo a atender às suas necessidades. Os artigos na tabela 3 demonstram como integrar os recursos e criar mais totalmente em destaque Suplementos do SharePoint.
  
    
    

**Tabela 3. Conceitos avançados no Suplementos do SharePoint**


|**Tópico**|**Descrição**|
|:-----|:-----|
| [Criar um provedor hospedado suplemento que inclui uma lista personalizada do SharePoint e o tipo de conteúdo](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |Explica como criar Suplementos do SharePoint que são hospedados na nuvem e que incluem a tipos de conteúdo e listas personalizadas do SharePoint. <br/> |
   

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Ferramentas e ambientes para o desenvolvimento de suplementos do SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
-  [Suplementos de design para o SharePoint](design-sharepoint-add-ins.md)
    
  
-  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Pacote de amostra de suplementos do SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  

