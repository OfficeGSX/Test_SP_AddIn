---
title: Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 2148980b-c2b6-4294-b8f7-cfc07f925091
---


# Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins
Saiba mais sobre as opções de acesso de dados estão disponíveis quando você cria Suplementos do SharePoint, incluindo as opções de conectividade para acessar os dados em SharePoint e em sistemas externos, bem como as APIs que estão disponíveis quando você deseja acessar dados do seu suplemento.
Ao avaliar suas opções de acesso de dados para Suplementos do SharePoint, você precisará avaliar o ambiente de suplemento e considerar vários fatores, como a comunicação entre o cliente e servidor e o nível de permissão que é necessário para o add-in executar as tarefas necessárias. Você também precisará avaliar as APIs que estão disponíveis no modelo de Suplementos do SharePoint.
  
    
    


## Visão geral de alto nível dos dados em Suplementos do SharePoint
<a name="SP15_dataaccessoptions_considerations"> </a>

É difícil imaginar um Suplemento do SharePoint (ou qualquer suplemento na verdade) que não precisa consultar, armazenar ou manipular dados. No seu suplemento, freqüentemente será necessário recuperar e manipular dados SharePoint, como itens em listas e bibliotecas de documentos, metadados ou perfis de usuário. Da mesma forma, é possível adotar os cenários em que você precisa para acessar dados externos em seu suplemento. O modelo de Suplementos do SharePoint fornece várias opções de conectividade e um rico conjunto de APIs para acessar os dados e serviços que residem em SharePoint e em sistemas externos.
  
    
    
Como criar seu suplemento e planejar o acesso de dados, você precisa tomar decisões importantes dois:
  
    
    

1. Qual opção de conectividade deve usar?
    
  
2. Quais as APIs deve usar para acessar os dados que necessários?
    
  
As ilustrações a seguintes resumem as diferentes opções fornecidos pelo modelo de Suplementos do SharePoint. Nas seções a seguir, você irá examinar cada opção detalhadamente e saiba quando usá-los.
  
    
    
A Figura 1 ilustra as opções que disponíveis para acessar dados SharePoint em seu suplemento. Quando você está lidando com esses cenários, você precisará decidir se deseja autenticar e comunique-se para SharePoint usando OAuthde (1) ou (2) a biblioteca entre domínios. Em seguida, para o acesso a dados API, você deve decidir entre (3) (4) Transferência de estado representacional (REST)ou o modelo de objeto do cliente (modelos de objeto de cliente JavaScript/.NET).
  
    
    
Tenha em mente que você também pode acessar determinados que dados usando (5)  *receptores de evento remoto*  , no entanto, o cenário principal para receptores de evento remoto é a execução de código remoto.
  
    
    

**Figura 1. Opções de uso de dados do SharePoint em seu suplemento**

  
    
    

  
    
    
![Opções para uso de dados do SharePoint no seu suplemento](images/DataAccess_SharePointData.png)
  
    
    
Figura 2 mostra as opções que você precisa para acessar dados externos em seu suplemento. Quando você estiver trabalhando com esses cenários, você precisará decidir se deseja usar (1) o  *proxy da web*  , (2) *tipos de conteúdo externo*  ou (3) a *biblioteca entre domínios com uma página personalizada de proxy*  para autenticar e se comunicar com sistemas ou serviços externos. Você também pode usar (4) (5)Transferência de estado representacional (REST)ou o modelo de objeto do cliente (modelos de objeto de cliente JavaScript/.NET).
  
    
    

**Figura 2. Opções para usar dados externos em seu suplemento**

  
    
    

  
    
    
![Opções para uso de dados externos no seu suplemento](images/5950bc8a-ed73-4f14-b616-a88c14c4fe56.png)
  
    
    

  
    
    

  
    
    

## Opções de conectividade de dados para Suplementos do SharePoint
<a name="SP15_dataaccessoptions_connectivity"> </a>

Você deve considerar vários aspectos ao trabalhar com dados em seu suplemento. Por exemplo, qual rota é os dados usando? É proveniente de ou indo através do servidor? Ele será por meio do cliente? É OK autenticar como o usuário conectado? O suplemento precisa privilégios elevados? As seções a seguir podem ajudá-lo com essas e outras perguntas que talvez seja necessário.
  
    
    

### conectividade de dados de SharePoint

As seguintes opções de conectividade estão disponíveis ao acessar dados SharePoint (consulte a Figura 1):
  
    
    

- **OAuth:** Um protocolo aberto que permite a autorização de segurança de uma forma simple e standard. OAuth permite aos usuários aprovar um aplicativo para agir em nome sem compartilhar seu nome de usuário e senha. Você pode usar OAuth com código no servidor. É uma boa opção se você precisar executar um processo não interativas, ou se você precisar elevar privilégios para além do usuário conectado. Para obter informações sobre o OAuth, consulte [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md).
    
  
- **Biblioteca entre domínios:** Uma alternativa do lado do cliente na forma de um arquivo de JavaScript ( **SP. RequestExecutor.js** ) hospedado em um site do SharePoint que você pode consultar em seu suplemento remoto. Biblioteca do domínio cruzado permite interagir com mais de um domínio em sua página de suplemento remoto através de um proxy. Isso é uma boa opção se você preferir seu código de suplemento seja executado no cliente, e não no servidor, ou se há barreiras de conectividade, como firewalls, entre SharePoint e sua infraestrutura remota. Para obter mais informações, consulte [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
    
  
- **Receptores de evento remoto:** Você pode usar os receptores de evento remoto para lidar com eventos que ocorrem em um item o suplemento, como uma lista, um item de lista ou uma web. Esses eventos se parecem com aqueles em uma solução tradicional SharePoint, exceto que eles possam trabalhar com os componentes de remota a Suplemento do SharePoint. Observe que algumas propriedades do item estão disponíveis para o receptor de evento remoto. Para obter mais informações, consulte [Criar um receptor de evento remoto no SharePoint Add-ins](create-a-remote-event-receiver-in-sharepoint-add-ins.md). De modo semelhante, você pode usar os receptores de evento do suplemento para personalizar como o add-in estiver instalado, atualizado e desinstalado. Para obter mais informações, consulte  [Criar um receptor de evento add-in SharePoint Add-ins](create-an-add-in-event-receiver-in-sharepoint-add-ins.md).
    
  

### Opções de conectividade de dados SharePoint: qual deles devo usar?

A tabela a seguir lista os requisitos e cenários que podem ocorrer quando você estiver criando suplementos de comuns. Um **x** na coluna indica qual opção você pode usar em cada caso.
  
    
    

**Tabela 1. Opções de conectividade de dados SharePoint**


|**Requisito/cenário**|**OAuth**|**biblioteca entre domínios**|
|:-----|:-----|:-----|
|Posso usar tecnologias de cliente (HTML + JavaScript ). <br/> ||X <br/> |
|Eu quero usar interfaces do REST. <br/> |X <br/> |X <br/> |
|Há um firewall entre o SharePoint e meu suplemento remoto e eu preciso emitir as chamadas através do navegador. <br/> ||X <br/> |
|Meu suplemento precisa acessar recursos como o usuário conectado. <br/> |X <br/> |X <br/> |
|Meu suplemento precisa elevar privilégios para além do usuário conectado atual. <br/> |X <br/> ||
|Meu suplemento precisa ajam em nome de um usuário diferente daquela que está conectado. <br/> |X <br/> ||
|Meu suplemento precisa executar operações apenas enquanto o usuário está conectado. <br/> |X <br/> |X <br/> |
|Meu suplemento precisa realizar operações, mesmo quando o usuário não está conectado. <br/> |X <br/> ||
|Meu suplemento precisa executar código remoto como uma resposta a um evento no SharePoint. <br/> |||
   
Desde que os receptores de evento remoto sejam criados na parte superior de OAuth, uma comparação nesta tabela não é a melhor forma para decidir se você deve usá-los ou não. Use os receptores de evento remoto quando você precisar executar código remoto, além de troca de dados.
  
    
    

### Conectividade de dados externos

As seguintes opções de conectividade estão disponíveis ao acessar dados externos (consulte a Figura 2):
  
    
    

- **Proxy da web:** Como um desenvolvedor, você pode usar o proxy da web exposto no cliente APIs, como o JSOM. Quando você usa o proxy da web, você emitir a solicitação inicial para SharePoint. Por sua vez, o SharePoint solicita os dados para o ponto de extremidade especificado e encaminha a resposta de volta para sua página. Use o proxy da web quando você deseja que a comunicação ocorra no nível do servidor. O proxy da web foi projetado para acessar dados não estruturados que exigem autenticação. Para obter mais informações, consulte [Consulta um serviço remoto usando o proxy da web no SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).
    
  
- **Tipos de conteúdo externo:** Você pode criar os suplementos que acessarem dados externos do SAP, Netflix e o proprietário e outros tipos de dados sem envolvendo o administrador de locatário. Acesso aos aplicativos externos é mantido por meio de Business Connectivity Services (BCS), que fornece uma interface consistente e uniforme que pode ser usada por outros aplicativos SharePoint. ECTs com escopo de aplicativo são uma boa opção quando você estiver usando um modelo BCS e o acesso aos dados requer autenticação. Para obter mais informações, consulte [Adicionar no escopo tipos de conteúdo externo no SharePoint 2013](http://msdn.microsoft.com/library/a34cbbba-dc38-4d3d-b796-d54b5848bdfb%28Office.15%29.aspx).
    
  
- **Página personalizada do proxy para a biblioteca de domínio cruzado:** Você pode usar a biblioteca entre domínios para acessar dados em seu suplemento remoto se você fornecer uma página de proxy personalizado que é hospedada na infraestrutura de suplemento remota. Como o desenvolvedor, você é responsável pela implementação de página personalizados proxy e deve fornecer a lógica personalizada, como o mecanismo de autenticação para o suplemento remoto usam a biblioteca entre domínios com uma página personalizada de proxy, se quiser que a comunicação ocorra no nível do cliente. Para obter mais informações, consulte [Criar uma página personalizada de proxy para a biblioteca entre domínios no SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
    
  

### Opções de conectividade de dados externos: qual deles devo usar?

A tabela a seguir lista os requisitos e cenários que podem ocorrer quando você estiver criando suplementos de comuns. Um **x** na coluna indica qual opção você pode usar em cada caso.
  
    
    

**Tabela 2. Opções de conectividade de dados externos**


|**Requisito/cenário**|**proxy da Web**|**Tipos de conteúdo externos**|**Biblioteca de entre domínios com página proxy personalizado**|
|:-----|:-----|:-----|:-----|
|Posso usar tecnologias de cliente (HTML + JavaScript ). <br/> |{x} <br/> |{x} <br/> |{x} <br/> |
|Não consigo adicionar páginas ou componentes ao remoto suplemento ou serviço. <br/> |X <br/> |X <br/> ||
|Eu quero usar interfaces do REST. <br/> |X <br/> |X <br/> |X <br/> |
|Eu quero usar JavaScript CSOM. <br/> |X <br/> |X <br/> |X <br/> |
|Eu quero usar o CSOM do .NET. <br/> |X <br/> |X <br/> ||
|Não há nenhuma conectividade direta entre a infra-estrutura de SharePoint e meu suplemento. Eu preciso emitir chamadas através do navegador. <br/> ||X <br/> |X <br/> |
|Meu suplemento precisa acessar recursos como o usuário conectado. <br/> |X <br/> |X <br/> |X <br/> |
   

## APIs de acesso a dados disponíveis para Suplementos do SharePoint
<a name="SP15_dataaccessoptions_availableAPIs"> </a>

As seguintes escolhas de API estão disponíveis quando você deseja acessar dados SharePoint do suplemento:
  
    
    

- **Transferência de estado representacional (REST):** Para cenários em que você precisa para acessar SharePoint entidades de tecnologias de cliente que não usam JavaScript e não são criadas na plataforma .NET Framework, SharePoint fornece uma implementação de um serviço web REST que usa o [protocolo do Open Data (OData)](http://www.odata.org/) para executar CRUDQ operações (criar, ler, atualizar, excluir e consulta) nos dados de SharePoint. Além disso, quase todas as API nos modelos de objeto do cliente tem um ponto de extremidade do REST correspondente. Isso permite que o seu código interagir diretamente com SharePoint usando qualquer tecnologia que oferece suporte a recursos padrão do REST. Para usar os recursos do REST que são embutidos no SharePoint, seu código constrói uma solicitação de HTTP por REST para um ponto de extremidade que corresponde ao objeto desejado SharePoint. O serviço REST manipula a solicitação HTTP e serve uma resposta no formato Atom ou JavaScript Object Notation (JSON). Para saber mais sobre o REST no SharePoint, consulte [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md).
    
  
- **Modelo de objeto do .NET framework cliente (OM do cliente .NET):** Quase todas as classes no modelo de objeto do lado do servidor de site e uma lista principal tem uma classe correspondente no modelo de objeto do cliente .NET Framework. Além disso, o modelo de objeto do cliente .NET Framework também expõe um conjunto completo de APIs para estender outros recursos, incluindo alguns SharePoint-avançada de recursos de nível como ECM, taxonomia, perfis de usuário, pesquisa, análise, BCS e outras pessoas. Para saber mais sobre os modelos de objeto do cliente, consulte [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
    
  
- **modelo de objeto do cliente JavaScript (JSOM):** SharePoint fornece um modelo de objeto do JavaScript para uso em script embutida ou arquivos. js separado. Inclui a mesma funcionalidade que o modelo de objeto do cliente .NET Framework. O JSOM é uma forma útil de inclusão de código personalizado SharePoint em um suplemento, especialmente em um suplemento hospedado no SharePoint, onde o código do lado do servidor personalizado não é permitido. Ele também permite aos desenvolvedores da web use suas habilidades JavaScript existentes para criar Suplementos do SharePoint com uma curva de aprendizagem mínima. Para saber mais sobre os modelos de objeto do cliente, consulte [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).
    
  
Pode haver APIs adicionais que você pode usar em sua Suplemento do SharePoint ao acessar dados externos. Ela depende de qual interfaces os serviços externos e sistemas têm a oferecer. Você também deve considerar as interfaces em seu design.
  
    
    

## Recursos adicionais
<a name="SP15_dataaccessoptions_addResources"> </a>


-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [Criar uma página personalizada de proxy para a biblioteca entre domínios no SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [Consulta um serviço remoto usando o proxy da web no SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)
    
  
-  [Criar um receptor de evento remoto no SharePoint Add-ins](create-a-remote-event-receiver-in-sharepoint-add-ins.md)
    
  
-  [Escolha o conjunto de APIs certo no SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)
    
  
-  [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  

