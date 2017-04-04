---
title: Criar os suplementos do SharePoint no Visual Studio
ms.prod: SHAREPOINT
ms.assetid: f1b9c858-907c-4558-b671-3b488ece40a0
---



# Criar os suplementos do SharePoint no Visual Studio
Aprenda a desenvolver Suplementos do SharePoint usando modelos de projetos e itens de projeto no Visual Studio.
Você pode desenvolver Suplementos do SharePoint usando novos modelos de projetos e itens de projeto no **vsnv**.
  
    
    


## Modelos de projeto
<a name="SP15Projecttemplates_templates"> </a>

Quando você usa um modelo de projeto no Visual Studio, ele cria uma solução que contém os itens de projeto e os arquivos que requer o tipo de projeto. Os seguintes modelos de projeto aparecem na caixa de diálogo **Novo projeto** se expandir o nó **Office/SharePoint** e, em seguida, escolher o nó de **suplementos**. Para obter informações sobre os modelos de projeto sob o nó de **Soluções do SharePoint**, consulte  [projeto do SharePoint e modelos de Item de projeto](http://go.microsoft.com/fwlink/?LinkId=255306).
  
    
    

### Suplemento do Office

Cria uma página da Web é hospedada dentro de um aplicativo do Office, como Excel ou Outlook. Um Suplemento do Office fornece conteúdo adicional e a funcionalidade de um documento ou item do Outlook. Para obter mais informações, consulte  [Visão geral da plataforma Suplementos do Office](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### Suplemento do SharePoint

Cria um Suplemento do SharePoint com base nas informações especificadas em um assistente. Essas informações incluem os seguintes dados.
  
    
    

- O nome do add-in.
    
  
- O local ou remoto site do SharePoint a ser usado para depuração seu suplemento.
    
  
- O tipo de suplemento que você deseja criar: hospedado em provedor ou hospedado no SharePoint.
    
  
Para obter mais informações, consulte  [Suplementos do SharePoint](sharepoint-add-ins.md).
  
    
    

### Suplemento de nuvem comercial

Usando o modelo de **suplemento Business Cloud** no Visual Studio, você pode criar um hospedado no SharePoint suplemento na qual os usuários móveis podem exibir, adicionar e atualizar dados de locais remotos usando dispositivos modernos orientado por toque, como telefones e tablets. Para obter mais informações, consulte [Criar suplementos de negócios de nuvem](create-cloud-business-add-ins.md).
  
    
    

## Modelos de item de projeto
<a name="SP15Projecttemplates_items"> </a>

Depois de criar uma solução do SharePoint, você pode adicionar itens de projeto a ela usando os seguintes modelos aparecem na caixa de diálogo **Adicionar Novo Item** sob o nó **Office/SharePoint**.
  
    
    

### Suplemento do Office

Adiciona um Suplemento do Office à sua Suplemento do SharePoint. Você pode adicionar um suplemento de painel tarefa, um suplemento conteúdo ou um suplemento de email. Para obter mais informações, consulte  [Visão geral da plataforma Suplementos do Office](http://msdn.microsoft.com/library/e64de870-ce22-4331-92e7-76d35279bf91%28Office.15%29.aspx).
  
    
    

### Web Part do cliente (Host Web)

Adiciona uma web part de cliente à sua Suplemento do SharePoint. Adicionando uma web part do cliente, você poderá exibir complementos nas páginas de um site de host. Este modelo contém um único arquivo Elements. XML, cujas propriedades definem os seguintes elementos da web part do cliente.
  
    
    


|**Nome da Propriedade**|**Description**|
|:-----|:-----|
|ClientWebPart <br/> |Especifica o nome, o título, a descrição e as dimensões da web part de cliente. <br/> |
|Conteúdo <br/> |Define o local da página que renderiza dentro da web part do cliente. Este elemento tem duas propriedades:  `Type` e `Src`.  `Type` Especifica o tipo de web part que você está criando, como HTML. `Src` define o local da página que será renderizado dentro da web part do cliente. O modelo faz referência a propriedades na seqüência de consulta usando o padrão _ _PropertyName__, como  `Src="~addinWebUrl/Pages/ClientWebPart1.aspx?Property1=_property1_"` <br/> Para obter mais informações, consulte  [Criar partes do suplemento para instalar com o SharePoint Add-in](create-add-in-parts-to-install-with-your-sharepoint-add-in.md). <br/> |
   

### Content Type

Adiciona um tipo de conteúdo à sua Suplemento do SharePoint, semelhante a tipos de conteúdo que foram usados nas versões anteriores do SharePoint. Um tipo de conteúdo é um conjunto de metadados, fluxos de trabalho e o comportamento para uma categoria de itens em uma lista do SharePoint ou biblioteca. Por exemplo, um item é um tipo de conteúdo de lista. Outros tipos de conteúdo de lista incluem comunicados, contatos e tarefas, e eles herdam o tipo de conteúdo do item. O tipo de conteúdo do contato contém colunas como **nome**, **Sobrenome** e **Cargo**.
  
    
    
Quando você adiciona um tipo de conteúdo à sua Suplemento do SharePoint, você pode especificar o tipo de conteúdo base do qual o novo tipo de conteúdo herda. Por exemplo, ele pode herdar de um comunicado, um contato, um documento ou um tipo de conteúdo do item. Você, em seguida, use o designer de **Tipo de conteúdo** para configurar as colunas para o tipo de conteúdo e as outras propriedades, como seu nome e sua descrição. Os valores que você escolheu são adicionados aos elementos `ContentType` e `FieldRef` no arquivo Elements XML. Para obter mais informações, consulte [Building Block: SharePoint 2010 Content Types](http://msdn.microsoft.com/library/277dfc42-d9a8-4fae-9ae1-0d202b14674f%28Office.15%29.aspx).
  
    
    

### Elemento vazio

Adiciona um item de projeto para um elemento vazio ao seu Suplemento do SharePoint. Esse item de projeto contém um único arquivo Elements. XML, onde você define as propriedades do elemento. Normalmente, você usar um elemento vazio para definir um item para o qual Visual Studio não oferece um modelo.
  
    
    

### List

Adiciona dois itens ao seu Suplemento do SharePoint de projeto: uma definição de lista e uma instância da lista. Quando você adiciona uma lista para seu suplemento, você deve especificar o que a lista de nomes e se é necessário criar uma lista em branco ou uma lista com base no tipo de lista existente. Você também pode especificar se a lista pode ser personalizada. Em seguida, você deve usar o **Designer da lista** para configurar as colunas e modos de exibição para a lista e outras propriedades, como nome e uma descrição da lista. Para obter mais informações sobre propriedades de lista, consulte [Elemento ListTemplate (modelo de lista)](http://msdn.microsoft.com/library/e565ead9-adcb-4a90-97e3-04850719420a%28Office.15%29.aspx) e [Elemento ListInstance (instância de lista)](http://msdn.microsoft.com/library/cfefe8e5-2656-4d71-bb4e-5f991a800598%28Office.15%29.aspx).
  
    
    

### Ação personalizada de Item de menu

Adiciona um item de projeto que se estende a interface do usuário do seu site do host, adicionando uma ação a um menu de lista. A ação de menu personalizado contém um arquivo Elements XML, que você usa para definir as propriedades da ação. Para obter mais informações, consulte  [Criar ações personalizadas para implantar o SharePoint Add-ins](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Module

Adiciona um item de projeto de módulo à sua Suplemento do SharePoint. Módulos são basicamente contêineres que você pode usar para incluir outros arquivos ao implantar sua Suplemento do SharePoint. Para adicionar um arquivo, copie-o no projeto sob o módulo no **Solution Explorer**. Uma referência para o arquivo será automaticamente acrescentada ao arquivo Elements. XML para o módulo, e a referência Especifica o caminho e a URL do novo arquivo. Você pode excluir o arquivo txt que tiver incluído com o módulo porque ele incluiu apenas por exemplo fins.
  
    
    

### Receptor de eventos remotos

Adiciona um item de projeto para um receptor de evento remoto para sua Suplemento do SharePoint e um projeto de aplicativo web à sua solução, se esse projeto não estiver presente. O aplicativo web contém um serviço da web associado com o receptor de evento remoto no seu Suplemento do SharePoint. O serviço web contém um arquivo de código do Visual Basic ou Visual c# cujo código é executado quando um evento de item da web, um item de lista ou uma lista ocorre no Suplemento do SharePoint. Se um aplicativo web estiver presente, ela está associada a Suplemento do SharePoint e o serviço web é adicionado a esse aplicativo. Para obter mais informações, consulte  [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md).
  
    
    

### Ribbon custom action

Adiciona um item de projeto que se estende a interface do usuário do seu site do host, adicionando uma ação a uma faixa de opções. A ação personalizada da faixa de opções contém um arquivo Elements XML, que define as propriedades da ação. Para obter mais informações, consulte  [Criar ações personalizadas para implantar o SharePoint Add-ins](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
  
    
    

### Configuração de pesquisa

Adiciona um item de projeto que permite que você importe as definições de configuração de pesquisa personalizadas que tenham sido exportadas de um site do SharePoint.
  
    
    

### coluna de site

Adiciona um item de projeto para uma coluna de site à sua Suplemento do SharePoint. A coluna de site contém um arquivo Elements XML que define as propriedades de  `Field` de coluna do site, incluindo os seguintes dados.
  
    
    


|**Nome da Propriedade**|**Descrição**|
|:-----|:-----|
|ID <br/> |Um valor GUID exclusivo para a coluna de site. <br/> |
|Name <br/> |Um nome exclusivo que é usado para fazer referência à coluna de site. <br/> |
|DisplayName <br/> |Um nome amigável que aparece na interface do usuário. <br/> |
|Tipo <br/> |O tipo de dados da coluna de site com base em **SPFieldType**, como Boolean, pesquisa ou texto. <br/> |
|Required <br/> |Se a coluna for necessária, a propriedade é definida como **True**; Caso contrário, a propriedade é definida como **False**. <br/> |
|Grupo <br/> |Especifica o nome do grupo ao qual a coluna de site é atribuída. O valor padrão para essa propriedade é **Colunas de Site personalizado**. <br/> |
   
Para obter mais informações, consulte  [Building Block: Columns and Field Types](http://msdn.microsoft.com/library/58548ade-e439-4931-82a2-fa29bd5afdb7%28Office.15%29.aspx).
  
    
    

### Fluxo de trabalho

Adiciona um item de projeto para um fluxo de trabalho Microsoft Azure à sua Suplemento do SharePoint. Para obter mais informações, consulte  [Workflows in SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx). Quando você adiciona esse tipo de item, você pode especificar um nome para o fluxo de trabalho e se ele é um fluxo de trabalho de site ou da lista. Como os nomes sugerem, um fluxo de trabalho de lista funciona somente com uma lista, e um fluxo de trabalho de site funciona somente com o site do SharePoint. Quando você estiver criando o fluxo de trabalho, você também especifica se deseja associar automaticamente o fluxo de trabalho com listas e bibliotecas e em caso afirmativo, quais. Para cada associação que você adiciona, um arquivo para que ele é adicionado ao projeto do fluxo de trabalho. Um fluxo de trabalho contém os seguintes arquivos.
  
    
    


|**Nome de Arquivo**|**Descrição**|
|:-----|:-----|
|Elements.xml <br/> |Especifica a configuração de fluxo de trabalho e os arquivos que ele contém, como o arquivo workflow.xaml e arquivos de associação e as propriedades de cada arquivo, como sua URL, seu tipo e seu caminho. Para cada arquivo que é adicionado ao projeto do fluxo de trabalho, uma seção correspondente é adicionada ao arquivo Elements. XML para o fluxo de trabalho. Arquivos de associação de fluxos de trabalho de lista exigem uma lista, para que eles tenham uma referência para o token de lista. Em um fluxo de trabalho de site, um GUID é adicionado para o site. <br/> > [!CUIDADO]> Como Visual Studio mantém os itens no arquivo Elements XML, não é recomendável alterá-la, a menos que você está familiarizado com o impacto das alterações.          |
|Workflow.xaml <br/> |Representa o criador do fluxo de trabalho. Nesse arquivo, adicione ações ao fluxo de trabalho e definir suas propriedades e código. <br/> |
|WorkflowStartAssociation <br/> |Inicia manualmente o fluxo de trabalho no SharePoint. Este arquivo é adicionado ao projeto do fluxo de trabalho, se você selecionar a caixa de seleção de **um usuário inicia manualmente o fluxo de trabalho** no Assistente de fluxo de trabalho. <br/> |
|ItemAddedAssociation <br/> |Inicia o fluxo de trabalho automaticamente, caso haja algum quando um usuário cria um item no site ou lista (dependendo do tipo de fluxo de trabalho). Este arquivo é adicionado ao projeto do fluxo de trabalho, se você selecionar a caixa de seleção **o fluxo de trabalho é iniciado automaticamente quando um item é adicionado** no Assistente de fluxo de trabalho. <br/> |
|ItemUpdatedAssociation <br/> |Inicia o fluxo de trabalho automaticamente, caso haja algum quando um usuário altera um item em um site ou da lista (dependendo do tipo de fluxo de trabalho). Este arquivo é adicionado ao projeto do fluxo de trabalho, se você selecionar a caixa de seleção **o fluxo de trabalho é iniciado automaticamente quando um item for alterado** no Assistente de fluxo de trabalho. <br/> |
|WorkflowHistoryList <br/> |Representa o arquivo que é adicionado ao projeto do fluxo de trabalho, se você criar uma lista de histórico do fluxo de trabalho no Assistente de fluxo de trabalho. <br/> |
|WorkflowTaskList <br/> |Representa o arquivo que é adicionado ao projeto do fluxo de trabalho, se você criar uma lista de tarefas do fluxo de trabalho no Assistente de fluxo de trabalho. <br/> |
   

### Atividade de fluxo de trabalho personalizados

Adiciona um item de projeto para uma atividade de fluxo de trabalho personalizada à sua Suplemento do SharePoint. Adicionando uma atividade de fluxo de trabalho personalizada, você pode criar ações de fluxo de trabalho adicionais que podem ser importados como ações personalizadas no SharePoint Designer 2013. A atividade de fluxo de trabalho personalizada contém um arquivo Elements XML, que define as propriedades da ação, e um arquivo. XAML para o designer de fluxo de trabalho. Para obter mais informações, consulte  [Workflows in SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx).
  
    
    

## Recursos adicionais
<a name="SP15Projecttemplates_addlresources"> </a>


-  [Ferramentas e ambientes para o desenvolvimento de suplementos do SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
