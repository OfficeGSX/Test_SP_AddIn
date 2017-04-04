---
title: Três maneiras de pensar sobre opções de design para o SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# Três maneiras de pensar sobre opções de design para o SharePoint Add-ins
Obtenha uma visão geral das opções de arquitetura e design que estão disponíveis com Suplementos do SharePoint. Você primeiro deve estar familiarizado com o artigo  [Suplementos do SharePoint](sharepoint-add-ins.md).
## Visão geral e a primeira decisão
<a name="Overview"> </a>

Este artigo analisa as opções de arquitetura para Suplementos do SharePoint de três maneiras diferentes. Primeiro, você saiba mais sobre as categorias mais importantes de escolhas de design; segundo; você visualizar a arquitetura de suplemento em termos de níveis de aplicativos; e, terceiro, você verá um conjunto de fatores que você precisa considerar ao fazer suas escolhas de design.
  
    
    
Mas a decisão primeira fazer é se sua extensão do SharePoint deve ser um Suplemento do SharePoint ou um clássico do SharePoint farm solution ou soluçăo em área restrita. Algumas partes do modelo de objeto do SharePoint, principalmente conectados com a personalização de administração do SharePoint e segurança, não são acessíveis de clientes. Somente códigos personalizados em execução no servidor SharePoint possam acessá-los e código do lado do servidor personalizado não é permitido em um Suplemento do SharePoint. (Um rico conjunto de modelos de objeto do cliente e um serviço REST/OData possibilitam para Suplementos do SharePoint fazer praticamente qualquer extensão do SharePoint end - orientado ao usuário.) Para obter mais informações sobre como decidir entre clássicas soluções e suplementos, consulte  [Suplementos do SharePoint em comparação com soluções do SharePoint](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx). Também é útil para tomar essa decisão  [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
  
    
    

## Principais elementos no design do SharePoint Add-ins
<a name="MajorCategoriesOfChoices"> </a>

Existem três categorias principais de opções que precisam ser realizadas quando um Suplemento do SharePoint é projetada. Como sempre, o design do aplicativo envolve compensações; opções que você faz uma categoria podem limitar as opções em outro. Nem todas as combinações possíveis de escolhas é viável.
  
    
    

- **Hospedando:**Suplementos do SharePoint pode ser útil dividido em dois tipos principais com base em como eles são implantados e hospedados.
    
  - **Hospedado em provedor** suplementos têm seu dados primários armazenamento e lógica de negócios implantado e hospedado por você  o desenvolvedor  fora do SharePoint em servidores ou uma conta de nuvem que você fornecer. Você é responsável por impor o isolamento entre as contas dos vários clientes que adquirem o add-in. Tais suplementos podem ter muito componentes do SharePoint. Estas são hospedadas no farm do SharePoint do cliente. Esse tipo de suplemento oferece mais flexibilidade nas outras categorias de escolhas de design. Ele também permite que você use plataformas de não-Microsoft para os dados externos, lógica e interface de usuário (UI) do web. (Dentro da categoria de hospedado em provedor suplementos, você também precisa distinguir entre os suplementos cujos componentes remotos estão dentro do firewall corporativo mesmo que o farm do SharePoint e aqueles cujos componentes remotos estão fora do firewall. Os sistemas de autorização para esses dois cenários são diferentes, que, por sua vez, faz diferença em qual linguagem de programação que você usar para acessar dados do SharePoint..)
    
  
  - **Hospedado no SharePoint** suplementos consistem inteiramente em componentes de SharePoint 2013, listas, tipos de conteúdo, fluxos de trabalho e Web Parts. Não há nenhuma componentes externos. Para obter mais informações sobre os tipos de componentes do SharePoint que podem ser incluídos no Suplementos do SharePoint, consulte [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).
    
  

    Para obter informações mais detalhadas sobre as opções de hospedagem de Suplementos do SharePoint, consulte  [Escolha os padrões para desenvolver e hospedar o Add-in do SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).
    
  
- **Conectividade:**SharePoint 2013 oferece suporte a três tipos de acesso de criar/ler/atualizar/excluir (CRUD) seguro aos dados.
    
  - Aplicativos da web externa em um suplemento usam o protocolo de OAuth para acessar dados do SharePoint. Para obter mais informações, consulte  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md).
    
  
  - JavaScript podem acessar dados em um suplemento de web do SharePoint e dados em outros sites dentro do mesmo aluguel usando uma biblioteca especial JavaScript que permite o script de seguro entre domínios. Para obter mais informações, consulte  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
    
  
  - Um Suplemento do SharePoint também pode acessar dados externos por meio de Business Connectivity Services (BCS) ou de um proxy de serviço web. Para obter mais informações, consulte  [Serviços Corporativos de Conectividade do SharePoint 2013](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx) e [Consulta um serviço remoto usando o proxy da web no SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).
    
  

    Para obter mais informações sobre o armazenamento de dados e o acesso em Suplementos do SharePoint, consulte  [Armazenamento de dados no SharePoint Add-ins](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data),  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)e  [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).
    
  
- **UI:** Há três maneiras para representar um Suplemento do SharePoint no SharePoint: no mínimo, todos os suplementos estão exibidos em uma página da web completo. Opcionalmente, um suplemento pode também ser exibido por meio de uma parte de suplemento e através de um botão da faixa de opções ou de item de menu. Para obter mais informações, consulte [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md).
    
    > **OBSERVAçãO**
      > Suplementos do SharePoint pode ser instalado pelos clientes para vários conjuntos de sites em um locatário ou em uma base de site por site. O primeiro é chamado de escopo de locatário suplementos. Se desejar que os clientes tenham a opção de escopo de locatário, você não pode incluir um botão da faixa de opções personalizada ou uma parte do suplemento. Para obter mais informações, consulte  [Aluguéis são e escopos de implantação para o SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md).

## Camadas de arquiteturais
<a name="Tiers"> </a>

Outra maneira de pensar suas opções de arquitetura de suplemento é pensar o suplemento como tendo três camadas de lógicas: a interface do usuário, a lógica de negócios e o acesso a dados. Cada camada tem várias opções de implementação; novamente, escolhas feitas para o limite de uma camada as opções para outras pessoas. As tabelas a seguir descrevem algumas das opções e seus usos, para os componentes remotos de um suplemento e os componentes do SharePoint.
  
    
    

**Componentes remotos no hospedado em provedor suplementos: opções para cada camada**


|**Camada**|**Opções**|**BOM para**|
|:-----|:-----|:-----|
|INTERFACE DO USUÁRIO <br/> |páginas de ASP.NET em uma ASP.NET formulários ou aplicativo MVC hospedado em uma função de web Microsoft Azure. <br/> |Aproveitando as habilidades de uma equipe de desenvolvimento ASP.NET. <br/> |
||5 de HTML da página com JavaScript. <br/> |Interface de usuário avançada. <br/> |
||PHP ou outro tipo de página da web hospedado em um serviço em nuvem de não-Microsoft. <br/> |Como integrar aplicativos não-Microsoft no SharePoint. <br/> |
||Silverlight em um Windows Phone app. <br/> |Acesso móvel a dados do SharePoint e integração com notificações de push e dados de localização geográfica. <br/> |
|Lógica de negócios <br/> |JavaScript do lado do cliente. <br/> |Lógica de interface do usuário e a lógica de negócios claro. <br/> Acessando dados do SharePoint por meio do modelo de objeto de cliente JavaScript. <br/> |
||Uma função de trabalho Microsoft Azure. <br/> |Funcionalidade de intensivos de processador. <br/> Acessando dados do SharePoint por meio do modelo de objeto de cliente .NET Framework. <br/> |
||Um serviço web remoto. <br/> |Funcionalidade de intensivos de processador. <br/> Acessando dados do SharePoint por meio do modelo de objeto de cliente .NET Framework. <br/> |
|dados <br/> |SQL Azure. <br/> |Dados relacionais completo. <br/> |
||armazenamento de tabela Microsoft Azure. <br/> |Configurações de aplicativo e outros metadados. <br/> |
||armazenamento de blob Microsoft Azure. <br/> |Armazenamento de arquivos grandes. <br/> |
||Um serviço de nuvem de não-Microsoft. <br/> |Aproveitando as fontes de dados existentes que se baseiam em plataformas de não-Microsoft. <br/> |
||Um banco de dados no servidor do desenvolvedor. <br/> |Controle de provedor de hospedagem e desenvolvedor de isolamento de aluguel. <br/> |
   

**Componentes do SharePoint: opções para cada camada**


|**Camada**|**Opções**|**BOM para**|
|:-----|:-----|:-----|
|INTERFACE DO USUÁRIO <br/> |Modos de exibição personalizados de SharePoint 2013 listas e bibliotecas em suplemento páginas da web. <br/> |Maximizando a integração com SharePoint aparência e comportamento. <br/> |
||aplicativo de Silverlight hospedado em um Web Part (ou dentro de marcas < objeto >) em uma página da web add-in. <br/> |Aproveitando a experiência de desenvolvimento do Silverlight existente. <br/> Interface de usuário avançada. <br/> |
|Lógica de negócios <br/> |Um fluxo de trabalho do SharePoint. <br/> |Implementando os processos de negócios. <br/> |
||No lado do cliente JavaScript complementados com a biblioteca do SharePoint entre domínios. <br/> |Acessando dados do SharePoint no suplemento de web. <br/> Acessando dados em outros sites dentro de aluguel. <br/> |
||Um manipulador de evento remoto. <br/> |Manipulando eventos CRUD em listas do SharePoint e bibliotecas usando externamente hospedado lógica. <br/> |
|dados <br/> |SharePoint 2013 listas e bibliotecas que são consultadas por meio de Collaborative Application Markup Language (CAML) ou LINQ, consultas com um dos modelos de objeto do cliente do SharePoint. <br/> |Como aproveitar SharePoint existente e experiência de desenvolvimento .NET Framework. <br/> |
||SharePoint 2013 listas e bibliotecas que são consultadas pelo serviço web REST/OData do SharePoint. <br/> |Acessando dados do SharePoint de plataformas de não-Microsoft. <br/> Aproveitando a experiência de consulta OData existente. <br/> |
||Um modelo de BCS. <br/> |Meio da exibição de dados externos no SharePoint como uma lista do SharePoint. <br/> |
   

## Fatores a serem considerados ao fazer suas decisões de design
<a name="DecisionFactors"> </a>

O modelo de Suplemento do SharePoint permite tantas possibilidades de design que não é possível uma árvore de decisão simples. A seguir são alguns dos fatores mais importantes a serem considerados ao construir a arquitetura de um Suplemento do SharePoint.
  
    
    

- Mais importante, é claro, a funcionalidade que você deseja disponibilizar para os clientes  os casos de uso. Por exemplo, se seu suplemento inclui funções de intensivos de processador, como converter arquivos de vídeo em outro formato de vídeo, que seria argumento para a criação de um provedor hospedado suplemento em que o processamento é feito em um dos seus servidores ou uma função de trabalho Microsoft Azure.
    
  
- Como um tipo de Suplemento do SharePoint, hospedado em provedor suplementos, requer que você (ou seu cliente) para hospedar os componentes não SharePoint e impor o isolamento de locatário, você precisa considerar se você tem o hardware e a equipe de TI para fazer isso (ou se seus clientes direcionados fazem).
    
  
- Quais clientes que você está direcionando também é uma preocupação essencial. Se todos os seus suplementos serão usados internamente (ou seja, não existem nenhum cliente externo) ou usado por um único cliente, hospedado em provedor suplementos são significativamente mais fácil implementar e manter que quando você tiver clientes externos ou vários clientes usarão o add-in. Se você pretende vender o suplemento publicamente, você também deve considerar se vai ser comercializar para empresas que têm contas SharePoint Online ou aqueles com seus próprios farms do SharePoint, ou ambos.
    
  
- Você também deve considerar suas habilidades existentes ou as habilidades de sua equipe de desenvolvimento. Por exemplo, se você for um desenvolvedor experiente ASP.NET, que seria um ponto em favor criando um aplicativo web remoto e exibição de dados de lista do SharePoint em uma página de ASP.NET. Por outro lado, se você for um desenvolvedor experiente do SharePoint, que seria um ponto em relação ao uso de uma SharePoint lista e site página personalizada, com o JavaScript para executar o processamento.
    
  

## Recursos adicionais
<a name="AdditionalResources"> </a>


-  [Suplementos de design para o SharePoint](design-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

