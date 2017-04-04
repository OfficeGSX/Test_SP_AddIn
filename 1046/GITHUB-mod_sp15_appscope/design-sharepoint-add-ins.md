---
title: Suplementos de design para o SharePoint
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# Suplementos de design para o SharePoint
Obtenha uma visão geral das opções de arquitetura e design que estão disponíveis no Suplementos do SharePoint e saiba como fazer as decisões corretas para facilitar o desenvolvimento de seu suplemento no SharePoint 2013.
Digamos que você tenha uma ideia killer para um suplemento. Nesta seção, nós vai orientá-lo as decisões de design que você precisa fazer e apresentam práticas recomendadas para criar seu suplemento. Por exemplo, o que torna uma interface de usuário boa? Cite o add-in "formas" disponíveis. Quando usar uma em vez de outro? Quais opções eu tenho para acessar os dados?
  
    
    


## Iniciar Projetando Suplementos do SharePoint
<a name="SP15Design_Startdesigning"> </a>

Porque o nuvem suplemento modelo SharePoint 2013 possibilita muitas opções de design, clique Suplementos do SharePoint em várias formas e tamanhos. Esta seção contém orientações úteis para algumas das decisões mais importantes que você precisa tomar conforme você está planejando e Projetando a experiência do usuário e a arquitetura do seu suplemento  incluindo como você irá hospedar o add-in, como seu suplemento com eficiência e segurança acessará os dados e qual será a experiência do usuário.
  
    
    
Para obter uma visão geral das opções de arquitetura e design que estão disponíveis com o SharePoint Add-ins, consulte  [Três maneiras de pensar sobre opções de design para o SharePoint Add-ins](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md). Para obter uma visão geral dos quais Suplementos do SharePoint são, consulte  [Suplementos do SharePoint](sharepoint-add-ins.md).
  
    
    

## Escolha o direito de modelo para o add-in de hospedagem
<a name="SP15Design_Hostingmodel"> </a>

Suplementos do SharePoint suporte a várias opções de hospedagem. Você pode escolher sua própria pilha de web, ter o Microsoft provisionar Microsoft Azure e SQL Azure, ou ter o suplemento hospedado no SharePoint. Tabela 1 contém recursos que podem ajudar você a escolha do modelo de hospedagem correto para seu suplemento.
  
    
    

**Tabela 1. Recursos e orientações para escolher o direito de modelo para Suplementos do SharePoint de hospedagem**


|**Artigo**|**Description**|
|:-----|:-----|
| [Escolha os padrões para desenvolver e hospedar o Add-in do SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Saiba mais sobre as diferentes maneiras que você pode hospedar os componentes de Suplementos do SharePoint. <br/> |
   

## Escolha as tecnologias de acesso de dados corretos para seu suplemento
<a name="SP15Design_Dataaccess"> </a>

Certifique-se de que seu suplemento acessa dados com eficiência e segurança. Diversas tecnologias de acesso de dados estão disponíveis para acessar o SharePoint e trabalhando com dados no seu suplemento a tabela 2 fornece recursos para ajudá-lo a aprender sobre suas opções e escolha uma adequada para seu suplemento.
  
    
    

**Tabela 2. Recursos e orientações para escolher as tecnologias de acesso de dados para usar em Suplementos do SharePoint**


|**Artigo**|**Description**|
|:-----|:-----|
| [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> |Saiba mais sobre as opções de acesso de dados estão disponíveis quando você cria Suplementos do SharePoint, incluindo as opções de conectividade de dados para cenários de dados de entrada e saída e as APIs que estão disponíveis quando você deseja acessar dados do SharePoint a partir de seu suplemento. <br/> |
   

## Design de eu para seu suplemento
<a name="SP15Design_UX"> </a>

Como criar seu suplemento, deve ser a meta de real criar uma experiência que permite aos usuários concluir os cenários que você pretenda para que eles possam realizar. Na tabela 3, descubra os recursos e a orientação de design que você precisa criar suplementos excelentes que seguem as práticas recomendadas para design de experiência do usuário e ter a aparência familiar e o comportamento do SharePoint 2013.
  
    
    

**Tabela 3. Recursos e orientações para projetar uma experiência de usuário excelente para Suplementos do SharePoint**


|**Artigo**|**Descrição**|
|:-----|:-----|
| [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md) <br/> |Saiba mais sobre as opções de experiência do usuário que estão disponíveis quando você cria Suplementos do SharePoint. <br/> |
   

## Design com atualização em mente
<a name="Upgrade"> </a>

Um dia, convém produzir uma atualização do seu suplemento e carregue-o Office Store ou o catálogo do suplemento de uma organização. Essa tarefa, será muito mais fácil se você pensar como você atualizaria o suplemento enquanto estiver criando a primeira versão. Recomendamos que você leia os artigos a seguir no início na fase de design:  [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md) e [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md).
  
    
    

## Próximas etapas: desenvolver e publicar o add-in
<a name="SP15Design_Next"> </a>

Tem um design sólido para o seu suplemento? Prepare-se criar seu suplemento e publicá-lo. Os recursos fornecidos na tabela 4 podem ajudá-lo a começar.
  
    
    

**Tabela 4. Recursos e diretrizes para o desenvolvimento e Suplementos do SharePoint de publicação**


|**Artigo**|**Descrição**|
|:-----|:-----|
| [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md) <br/> |Aborda os conceitos avançados e capacidades do modelo do suplemento. <br/> |
| [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md) <br/> |Descreve o processo e os requisitos para publicação Suplementos do SharePoint. <br/> |
   

## Recursos adicionais
<a name="SP15Design_AddRes"> </a>


-  [Pacote de amostra de suplementos do SharePoint](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
-  [Imagine novamente o desenvolvimento do SharePoint](http://msdn.microsoft.com/pt-br/office/apps/dn133840)
    
  
-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Blog de suplementos](http://blogs.msdn.com/b/spoffapps)
    
  

