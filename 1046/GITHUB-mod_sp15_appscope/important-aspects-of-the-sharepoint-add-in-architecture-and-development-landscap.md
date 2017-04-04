---
title: Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário
ms.prod: SHAREPOINT
ms.assetid: ae96572b-8f06-4fd3-854f-fc312f7f2d88
---


# Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário
Saiba mais sobre os aspectos da arquitetura da Suplementos do SharePoint e o modelo de Suplementos do SharePoint, incluindo as opções de hospedagem suplemento opções da interface do usuário, sistema de implantação, sistema de segurança e ciclo de vida. Este artigo complementa as informações do artigo  [Suplementos do SharePoint](sharepoint-add-ins.md).
## Opções de hospedagem de suplementos do SharePoint
<a name="SPAppModelArch_SPCenteredVsCloudCentered"> </a>

O modelo de suplemento SharePoint 2013 fornece as seguintes maneiras para hospedar os componentes de uma Suplemento do SharePoint:
  
    
    

- **Hospedado em provedor:** Suplementos que incluem pelo menos um componente remoto e também podem incluir os componentes do SharePoint. Os componentes do SharePoint não estão implantados por sua lógica em sua conta de hardware ou nuvem ou implantados em conta hardware ou nuvem do cliente usando programas de instalação e as instruções fornecidas por você.
    
  
- **Hospedado no SharePoint:** Suplementos que incluem somente os componentes do SharePoint e lógica que seja executada no cliente.
    
  
Para obter informações mais detalhadas sobre a hospedagem de opções e alguns orientação sobre como escolher entre elas, consulte  [Escolha os padrões para desenvolver e hospedar o Add-in do SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).
  
    
    

## Suplemento webs, webs de host, recursos e SharePoint componentes em suplementos
<a name="SPComponents"> </a>

O site ao qual um Suplemento do SharePoint está instalado é chamado de host na web. No entanto, as partes significativas do Suplemento do SharePoint, se eles são componentes do SharePoint ou externo, não são implantadas na Web do host. Partes externos são implantados em servidores externos ou contas na nuvem. Componentes do SharePoint são implantadas para um site especial com seu próprio domínio. Isso é chamado de suplemento de web. Somente um conjunto limitado de elementos de interface do usuário que oferecem aos usuários acesso para o suplemento 's outros componentes são implantados na Web do host. Esses componentes de interface do usuário na web host são implantadas como parte de um recurso da web de host  um recurso que está solto no pacote suplemento em vez de dentro de um arquivo. wsp. Os componentes que são implantados na Web suplemento estão sempre em recursos que estão dentro de um arquivo. wsp. Os dois tipos de recursos devem ter **Web** escopo. Nenhum outro escopo é possível para recursos no Suplementos do SharePoint.
  
    
    
Como regra geral, qualquer componente do SharePoint que não inclui um código personalizado que é executado em servidores do SharePoint pode ser incluído em um Suplemento do SharePoint (e ser implantado na Web suplemento). Há, no entanto, algumas exceções e algumas nuances como e onde os componentes são implantados. Para obter mais informações sobre essas nuances e sobre webs de host, isolados suplemento webs e recursos em suplementos, consulte  [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).
  
    
    

## Acessando o suplemento de interface do usuário
<a name="AccessingApp"> </a>

Quando um Suplemento do SharePoint é instalado em um site, o suplemento está listado na página **Conteúdo** do Site da web host. Os usuários podem iniciar o add-in da página. Quando aberto dessa maneira, o suplemento é executado no modo de tela inteira.
  
    
    
Outra maneira que um Suplemento do SharePoint pode ser exibida é por meio de uma parte suplemento, um tipo de Web Part que é representado pela classe **ClientWebPart**. Esse tipo de Web Part é essencialmente um wrapper para um IFrame que hospeda uma página do add-in. No caso mais simples, a propriedade significativa apenas da Web Part é uma URL que aponta para a página. Mas Web Parts podem ter propriedades personalizadas podem ser definidos pelos usuários em uma parte da ferramenta. Tais propriedades podem ser usadas, por exemplo, para definir informações de contexto, como os CEP ou código Postal do usuário. Para incluir uma suplemento parte tão no seu suplemento, você cria um recurso da web de host na marcação de Web Part declarativa add-in e em Adicionar. Como qualquer outra Web Part, ele aparece na interface do usuário SharePoint 2013 do qual os usuários adicionar Web Parts. Você pode ter mais de uma suplemento parte implantada com o add-in, caso você precise variabilidade ainda mais. Por exemplo, um suplemento de clima pode ter uma parte suplemento que mostra clima atual e uma segunda suplemento parte que mostra uma previsão semanal. As duas partes podem ter funcionalidade e tamanhos diferentes.
  
    
    

> [!OBSERVAçãO]
> Você também pode implantar o suplemento partes na Web do suplemento. Para implementar isso, a marcação para a Web Part seria parte de um recurso dentro de um arquivo. wsp no pacote do suplemento, e não nas web host recurso.
  
    
    

Recomendamos que você tente dar seus suplementos uma aparência do SharePoint a extensão possível, embora que não seja obrigatório e nem sempre pode ser a melhor opção. Para obter mais informações sobre as diretrizes da experiência do usuário, consulte  [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md).
  
    
    
Por exemplo, há uma página mestra especial chamada app.master. Esta página é otimizada para uso pelas páginas de suplementos. A página app.master faz parte de uma nova definição de site que está incluída no SharePoint 2013.
  
    
    
Outra ferramenta que você pode usar para ajudar a manter que uma aparência consistente com o SharePoint é o controle de cromo que acompanha SharePoint 2013 seus suplementos. Esse controle permite que você adicione a área de cabeçalho de navegação do SharePoint às suas páginas de suplemento, incluindo as páginas hospedadas externamente. Para obter mais informações sobre o design de eu em Suplementos do SharePoint, consulte  [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md). Para obter mais informações sobre o controle de cromo, consulte  [Use o controle de cromo do cliente no SharePoint Add-ins](use-the-client-chrome-control-in-sharepoint-add-ins.md).
  
    
    

## Estrutura do pacote de suplemento
<a name="SPAppModelArch_Package"> </a>

Um pacote de Suplemento do SharePoint é um arquivo que tem a extensão ". app" e que está em conformidade com as  [Convenções de empacotamento aberto (OPC)](http://msdn.microsoft.com/en-us/magazine/cc163372.aspx). (Você pode abrir o arquivo adicionando ". zip" como uma extensão extra no nome do arquivo e depois abri-lo no Windows Explorer.) Ele contém um manifesto suplemento que especifica determinadas propriedades do suplemento e instruções para a infra-estrutura de instalação do SharePoint. Para obter mais informações sobre o manifesto do add-in e o pacote, consulte  [Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md).
  
    
    

## Permissões, autenticação e autorização para suplementos do SharePoint
<a name="SPAppModelArch_Running"> </a>

SharePoint 2013 introduz um novo sistema de segurança e permissões do suplemento.
  
    
    

### Permissões de suplemento
<a name="AppPermissions"> </a>

Suplementos do SharePoint ter permissões apenas como fazer de usuários e grupos. Isso permite que um suplemento ter um conjunto de permissões que são diferentes das permissões do usuário que está executando o add-in.
  
    
    
Você deverá solicitar, no suplemento do arquivo de manifesto, as permissões que um suplemento precisa ser executado. O usuário que adiciona o suplemento deve conceder essas solicitações, e o usuário só pode conceder permissões que ele ou ela tem como um usuário. A concessão deve ser para todas as permissões solicitadas ou nenhum para simplificar o gerenciamento de permissões para desenvolvedores e usuários. (A entidade de segurança sempre tem direitos de controle total à web suplemento, portanto, ele só precisa solicitar permissões para recursos do SharePoint na web host ou em outros locais fora da web do suplemento).
  
    
    
Para obter mais informações sobre as permissões de suplemento, consulte  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Autorização e delegação seletiva
<a name="SelectiveAuthorization"> </a>

Os usuários que estão lançar um suplemento nem proprietários de recurso, quem estão concedendo permissão um suplemento para acessar um recurso, precisam fornecer o suplemento suas credenciais ou a senha. Em vez disso, SharePoint 2013 permite que os usuários e proprietários de recurso para conceder apenas as permissões específicas que o suplemento solicitações. O que isso torna possível é o uso por SharePoint 2013 do protocolo de transação  [OAuth 2.0](http://tools.ietf.org/html/draft-ietf-oauth-v2-22). Para obter mais informações sobre OAuth no SharePoint 2013, consulte  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md).
  
    
    

### Acesso entre domínios
<a name="SelectiveAuthorization"> </a>

Suplemento do SharePoint que inclui um aplicativo web remoto que usa JavaScript para sua respectiva lógica de acesso de dados pode usar um JavaScript cruze biblioteca do domínio para obter acesso autorizado aos dados do SharePoint dentro de aluguel onde o suplemento está instalado. Para obter mais informações, consulte  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Suplemento do ciclo de vida
<a name="SPAppModelArch_Lifecycle"> </a>

O ciclo de vida de um Suplemento do SharePoint inclui publicação, instalação, atualização e desinstalação. Para obter mais informações sobre esses assuntos, consulte  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md),  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) e [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md). Observe também que há um mecanismo pelo qual locatário administradores podem batch instalar um Suplemento do SharePoint em vários sites. Para obter mais informações, consulte  [Aluguéis são e escopos de implantação para o SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md).
  
    
    

## Armazenamento de dados no SharePoint Add-ins
<a name="Data"> </a>

Suplementos do SharePoint pode criar e acessar qualquer tipo de dados, incluindo dados estruturados, documentos e arquivos de multimídia. Esses dados podem ser armazenados no SharePoint ou em um local externo.
  
    
    

### Opções de armazenamento de dados estruturados
<a name="StructuredData"> </a>

Um Suplemento do SharePoint pode usar praticamente qualquer tipo de armazenamento de dados estruturados, dentro e fora do SharePoint 2013 e nas plataformas de não-Microsoft e Microsoft. Estes são  *alguns*  locais onde você pode armazenar dados estruturados para um Suplemento do SharePoint:
  
    
    

- Listas do SharePoint em um suplemento de web
    
  
- SQL Azure
    
  
- Fontes de dados externas conectados ao SharePoint com Microsoft Business Connectivity Services (BCS)
    
  
- Um serviço em nuvem de não-Microsoft
    
  
- Um banco de dados no seu próprio servidor
    
  

> [!DICA]
> Além disso, você provavelmente atualizará sua Suplemento do SharePoint em algum momento. Quando um Suplemento do SharePoint inclui os componentes do SharePoint em um suplemento de web, o processo de atualização faz uma cópia completa da web add-in. Por esse motivo, muito grandes listas do SharePoint na web suplemento tornar o processador e o processo de atualização demorada intensiva no servidor de banco de dados de conteúdo. Evite colocar "grandes dados" em listas do SharePoint na web add-in.
  
    
    


### Opções de armazenamento de dados não estruturados
<a name="UnStructuredData"> </a>

Documentos, imagens, arquivos de áudio, vídeos e outros tipos de dados não estruturados produzidos ou usados por um Suplemento do SharePoint que podem ser armazenados em ou fora do SharePoint. Bibliotecas de documentos são uma boa opção para documentos e pesquisáveis por meio de pesquisa do SharePoint. Uma biblioteca de ativos do site é geralmente uma boa opção para arquivos de multimídia.
  
    
    
Outras opções incluem o armazenamento de blob em sua conta Microsoft Azure ou em seus próprios servidores. Você também pode armazenar arquivos em algumas plataformas de não-Microsoft ou serviços em nuvem.
  
    
    

### Configurações de suplementos e outras opções de armazenamento de metadados
<a name="AppMetadata"> </a>

Metadados para um Suplemento do SharePoint, como as preferências do usuário, informações de localização e outras configurações podem ser armazenados em vários lugares. Às vezes, uma lista oculta do SharePoint é uma boa opção. Você também pode usar o recipiente de propriedades da web add-in. Outra opção, para um provedor hospedado suplemento, é usar Microsoft Azure armazenamento de tabela.
  
    
    

### Opções de acesso de dados seguro
<a name="DataAccess"> </a>

Suas opções para o acesso de dados seguro, obviamente, dependem de sua escolha de armazenamento. Acesso a dados e pesquisa são abordadas em detalhes em vários outros artigos. Para obter mais informações, consulte  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).
  
    
    

## Gerenciando suplementos
<a name="SPAppModelArch_Managing"> </a>

Os administradores de conjunto de sites e administradores de Inquilino podem monitorar suplementos e altere os recursos alocados para eles. Além disso, a equipe de Microsoft para o repositório de suplemento pode sinalizar suplementos e desabilitá-los.
  
    
    
Para obter mais informações sobre como gerenciar suplementos, consulte  [instalar e gerenciar suplementos SharePoint](http://msdn.microsoft.com/en-us/library/733647a3-a5d3-475b-967d-3bb627c2a0c2) no TechNet.
  
    
    

### Suplementos de monitoramento
<a name="SPAppModelArch_Monitoring"> </a>

SharePoint 2013 fornece o monitoramento de integridade de suplementos e disponibiliza essas informações na interface de usuário para proprietários de sites, os administradores dos locatários e administradores de farm. A maioria dos documentação para o sistema de monitoramento está na TechNet; Por exemplo,  [Monitor SharePoint suplementos](http://technet.microsoft.com/library/3adafdd2-f276-4a9d-8a74-e06b8916bbc2). Esta seção é uma breve introdução para explicar como os suplementos que você vender são monitorados.
  
    
    
Alguns tipos de dados são relatadas por-app e outros tipos são relatadas por --instância do aplicativo. Os itens principais que relata a estrutura de monitoramento são:
  
    
    

- Uso do add-in, como o número de vezes que já tenha sido instalado (Criando uma nova instância).
    
  
- Consumo de recursos de servidor de cada instância do suplemento.
    
  
- Instalação, atualização e os erros de tempo de execução de cada instância do suplemento.
    
  
- Um indicador de integridade geral para cada instância do suplemento do vermelho, amarelo e verde.
    
  
Se o suplemento inclui os componentes de Azure Web Site, a estrutura de monitoramento também sonda Microsoft Azure por hora para seus dados de erro e relatórios de erros críticos e dados de cota de armazenamento em SharePoint 2013 da interface do usuário. erros de Banco de Dados SQL do Microsoft Azure não são relatados.
  
    
    
As informações fornecidas pela estrutura de monitoramento permite aos administradores determinar se seu orçamento de compra de suplemento está sendo comparecer gasto, independentemente de terem implantar mais recursos em suplementos e independentemente de terem que desabilitar um suplemento que não está funcionando corretamente.
  
    
    

## Registrando as dependências de suplemento
<a name="RegisterDependency"> </a>

Se seu Suplemento do SharePoint depende de um recurso do SharePoint que não está disponível e não pode ser tornado disponíveis na web add-in, em seguida, ele não funcionará corretamente e seus clientes serão reclamam. Você pode garantir que o suplemento não está instalado em que os recursos e serviços necessários não estão disponíveis, registrando as dependências do add-in no manifesto do suplemento. A infraestrutura de instalação para Suplementos do SharePoint verificarão esses pré-requisitos e bloquear a instalação de você suplemento se qualquer uma delas não estará disponível.
  
    
    
Para serviços, como serviços do Excel, Access ou do Visio, a infraestrutura verificará se o serviço está instalado e licenciado.
  
    
    
Para recursos, como uma lista de tarefas, a infraestrutura verificará se o recurso é implantado e uma destas opções:
  
    
    
- ativado no escopo **Farm**, **WebApplication**ou **Site** (conjunto de sites)
  
    
    
ou
  
    
    
- activatible, **Web** escopo, na web suplemento que é criado quando o suplemento está instalado.
  
    
    

> [!OBSERVAçãO]
> A infraestrutura de instalação do suplemento ativará automaticamente esses recursos na web suplemento quando ele é criado.
  
    
    

As seções a seguir fornecem os detalhes que você precisa registrar seus pré-requisitos.
  
    
    

### Registrar implicitamente dependências com solicitações de permissão
<a name="PermAsPreq"> </a>

Quando seu suplemento precisa acesso aos componentes do SharePoint fora da web suplemento, ele deve solicitar permissão para esses recursos na seção **AppPermissionRequests** do manifesto do suplemento. Essas solicitações de permissão também servem como pré-requisito registros porque SharePoint deduzirá das permissões que o suplemento solicita que o suplemento precisa determinados recursos do SharePoint esteja disponível. Em várias situações, o SharePoint pode interpretar todos os recursos que seu suplemento precisa e as seções restantes deste tópico não são necessários. No entanto, os registros de dependência redundantes não são prejudiciais.
  
    
    

### Registrar explicitamente dependências com AppPrerequisites
<a name="Explicit"> </a>

Quando o suplemento tem uma dependência que não estará implícito por suas solicitações de permissão, você registrar cada dependência com um elemento **AppPrerequisite** no manifesto do suplemento. Há três atributos nesse elemento; **Type**, **ID**e (opcionalmente) **MinimumVersion**.
  
    
    
Há três valores possíveis de pré-requisito para **Type**;  `Feature`,  `Capablility`e  `AutoProvisioning`. Um pré-requisito de recurso é simplesmente um recurso do SharePoint que deve ser implantado e ativado na web suplemento ou em um escopo mais amplo que inclui web add-in. Um recurso é um conjunto de relacionados recursos e serviços que devem estar disponíveis na web add-in. ( `AutoProvisioning` é discutido na próxima seção).
  
    
    
O opcional **MinimumVersion** Especifica a versão mais baixa do recurso ou do recurso que seu suplemento requer. Os valores de atributo são de forma n.n.n. n; Por exemplo `15.0.0.0`.
  
    
    
O **ID** Especifica qual recurso ou o recurso é necessário. Se **Type** for `Feature`, o **ID** é o GUID de entre colchetes, hifenizado do recurso; Por exemplo `{151D22D9-95A8-4904-A0A3-22E4DB85D1E0}`. Se **Type** for `Capability`, o **ID** é o GUID do recurso. Os recursos estão listados abaixo. Para obter a localizar o GUID de uma capacidade consulte [Elemento de AppPrerequisite (AppPrerequisiteCollection complexType) (SharePoint App manifesto)](http://msdn.microsoft.com/library/791be402-981f-519e-fcde-f24cc3cb4139%28Office.15%29.aspx).
  
    
    

- Serviços do Access 2010
    
  
- Access Services
    
  
- Serviço da Web de Metadados Gerenciados
    
  
- Serviços do PowerPoint
    
  
- Serviço de repositório seguro
    
  
- Machine Translation Service
    
  
- Serviço de Perfil de Usuário
    
  
- Serviço de Gráfico do Visio
    
  
- Serviço de Gerenciamento do Trabalho
    
  
- DUET
    
  
- Fluxo de trabalho
    
  
- Pesquisa
    
  
- EDU
    
  
O exemplo a seguir é um exemplo de uma marcação brutos **AppPrerequisites** que registra o recurso de fluxo de trabalho. Se você estiver usando Visual Studio, você pode editar o manifesto do suplemento em uma ferramenta de designer.
  
    
    



```

<AppPrerequisites>
  <AppPrerequisite Type="Capability" ID="{CDD8F991-B459-4512-8048-03D5A03FF27E}" MinimumVersion="15.0.0.0" />
</ AppPrerequisites>
```


## Nesta seção
<a name="RegisterDependency"> </a>


-  [Escolha os padrões para desenvolver e hospedar o Add-in do SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)
    
  
-  [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

## Recursos adicionais
<a name="SPAppModelArch_AdditionalResources"> </a>


-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  
-  [Suplementos do SharePoint em comparação com soluções do SharePoint](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)
    
  
-  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md)
    
  
-  [IFrame](http://www.w3schools.com/tags/tag_iframe.asp)
    
  
-  [Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md) .
    
  
-  [Aluguéis são e escopos de implantação para o SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  

