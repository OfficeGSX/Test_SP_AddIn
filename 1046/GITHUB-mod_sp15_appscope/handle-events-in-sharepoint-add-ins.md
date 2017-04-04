---
title: Lidar com eventos no SharePoint Add-ins
keywords: vs.sharepointtools.spe.remoteeventreceiver
f1_keywords:
- vs.sharepointtools.spe.remoteeventreceiver
ms.prod: SHAREPOINT
ms.assetid: c050d056-8548-4496-a053-016779d723d9
---


# Lidar com eventos no SharePoint Add-ins

Seu código personalizado pode lidar com três categorias de eventos no hospedado em provedor suplementos:
  
    
    


- **Eventos de lista**, como adicionar ou excluir de uma lista em um site.
    
  
- **Eventos de item da lista**, como a edição de um item em uma lista.
    
  
- **Suplemento de eventos**, como a instalação de um suplemento.
    
  

Hospedado no SharePoint Suplementos do SharePoint não têm suporte para a manipulação de eventos, mas você pode transformar um fluxo de trabalho em um tipo de lista ou de um manipulador de eventos do item de lista, definindo um evento acionar o fluxo de trabalho. Consulte  [Workflows in SharePoint 2013](http://msdn.microsoft.com/library/e0602371-ae22-44be-8a7e-9e47e9f046d6%28Office.15%29.aspx). Fluxos de trabalho não podem ser disparados pelo suplemento eventos, para que o suplemento eventos não podem ser manipuladas com um suplemento hospedado no SharePoint.
  
    
    


> [!OBSERVAçãO]
> Eventos de site e eventos do conjunto de sites não são suportados no Suplementos do SharePoint.
  
    
    

Existem dois tipos de eventos:
- ** *Antes*  de eventos** são acionadas antes que a infra-estrutura de SharePoint realiza qualquer uma dela é o própria manipulação do evento (incluindo confirmar as alterações no banco de dados de conteúdo). Na SharePoint ** personalizadas antes manipuladores de eventos sempre executados de maneira síncrona**. Entre outras finalidades, eles podem ser usados para cancelar o evento. Por exemplo, se um suplemento possui uma função para a exclusão de uma lista, um manipulador para a lista de exclusão de evento pode cancelar a exclusão se certas condições não forem atendidas. Se o evento for parte de uma sequência de eventos, cancelar ele impede qualquer um dos eventos posteriores ocorrência nisso. Por exemplo, se o seu manipulador para o evento **ItemAdding** cancelar o evento, o evento **ItemAdded**, que normalmente vem posteriormente, não é acionado.
    
  
- **Eventos  *após* ** são acionadas depois que a infra-estrutura de SharePoint realiza qualquer uma dele é própria manipulação do evento. Em SharePoint, **remoto depois manipuladores de eventos, para a lista e eventos do item de lista, sempre executado de forma assíncrona**. (Os eventos de aplicativo são uma exceção.) Entre outras finalidades, elas podem ser usadas para registrar eventos.
    
  

## Lidar com eventos de item de lista e lista
<a name="RER"> </a>

Para manipular eventos de item da lista e de lista, você criar receptores de eventos remotos (RERs), que são os serviços da web que executam externamente ao farm SharePoint ou SharePoint Online. A URL do serviço RER é registrada para os eventos que ele lida com. Há duas maneiras de registrar um manipulador:
  
    
    

- Eventos no web host registrados programaticamente com o CSOM (modelo de objeto do cliente) ou a API REST de SharePoint. Essa tarefa geralmente é feita em lógica "da primeira execução" em suplemento ou em um manipulador de eventos add-in. (Consulte  [Manipulando eventos de suplemento](#HandlingAppEvents) posteriormente neste artigo para uma visão geral de suplemento eventos.) Para obter um exemplo de código que registra um evento de lista programaticamente, consulte [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers).
    
  
- Eventos no suplemento de web geralmente são registrados em um recurso da web suplemento com alguns marcação XML simples. Detalhes de como criar a marcação e o serviço estão no  [Criar um receptor de evento remoto no SharePoint Add-ins](create-a-remote-event-receiver-in-sharepoint-add-ins.md). Também é possível registrar eventos de suplemento web programaticamente.
    
  

> [!OBSERVAçãO]
> RERs ter a mesma finalidade que receptores de evento em soluções de farm; mas os receptores de eventos com código personalizado que é executado nos servidores de SharePoint, portanto, eles não podem ser usados em Suplementos do SharePoint.
  
    
    

O add-in pode manipular os seguintes eventos de biblioteca de documentos e listas. Eventos terminando em "ER" são antes de eventos (síncronos) e os terminando em "ed" são depois (assíncronos) eventos.
  
    
    

|||
|:-----|:-----|
|ListAdding <br/> |ListAdded <br/> |
|ListDeleting <br/> |ListDeleted <br/> |
|FieldAdding <br/> |FieldAdded <br/> |
|FieldDeleting <br/> |FieldDeleted <br/> |
|FieldUpdating <br/> |FieldUpdated <br/> |
   
Os eventos de atualização de campo estão prestes a alteração das propriedades de um campo (coluna) em uma lista, como se ele está ordenável, não sobre como alterar os dados no campo.
  
    
    
Suplemento pode lidar com os seguintes eventos de item de lista.
  
    
    

|||
|:-----|:-----|
|ItemAdding <br/> |ItemAdded <br/> |
|ItemUpdating <br/> |ItemUpdated <br/> |
|ItemDeleting <br/> |ItemDeleted <br/> |
|ItemCheckingOut <br/> |ItemCheckedOut <br/> |
|ItemCheckingIn <br/> |ItemCheckedIn <br/> |
|ItemUncheckingOut <br/> |ItemUncheckedOut <br/> |
|ItemAttachmentAdding <br/> |ItemAttachmentAdded <br/> |
|ItemAttachmentDeleting <br/> |ItemAtttachmentDeleted <br/> |
|ItemFileMoving <br/> |ItemFileMoved <br/> |
|ItemVersionDeleting* <br/> |ItemVersonDeleted* <br/> |
||ItemFileConverted <br/> |
   

> [!OBSERVAçãO]
> * Esses dois novos eventos podem não estar disponíveis no Visual Studio da interface do usuário. Caso contrário, escolha ItemDeleting ou ItemDeleted e, em seguida, alterar manualmente os nomes.
  
    
    

Quando você estiver trabalhando em Visual Studio e você adiciona um RER a um projeto de Suplemento do SharePoint, o Office Developer Tools for Visual Studio, faça o seguinte:
  
    
    

- Um arquivo de serviço da web, como RemoteEventReceiver1.svc, é adicionado ao aplicativo web para manipular os eventos que você especificou quando você adicionou o receptor de evento remoto para o Suplemento do SharePoint. O serviço web contém um arquivo de código para manipular os eventos remotos.
    
    Após criar o receptor de evento remoto, você adicionar código para o arquivo de código para o serviço de aplicativo web manipular os eventos. Por padrão, o arquivo de código contém dois métodos aos quais você adicionar seu código de tratamento:
    
  -  `ProcessEvent()` manipula "before" eventos (por exemplo, aqueles nas colunas nas tabelas anteriormente neste artigo esquerdas) e retorna um objeto no SharePoint que emite um relatório sobre ele deverá cancelar o evento ou deixe que ele continue.
    
  
  -  `ProcessOneWayEvent()` manipula "depois" eventos. Ele é executado de forma assíncrona e não retornará nada para SharePoint.
    
  

    Quando ocorre um evento registrado, SharePoint chama o método apropriado no seu serviço e passa um objeto que fornece algumas informações de contexto para seu código. Por exemplo, o tipo de evento (de uma das duas tabelas anteriormente neste artigo) é identificado, para que seu código pode branch à lógica apropriado para o evento.
    
  
- Um item do projeto para o receptor de evento remoto é adicionado ao projeto Suplemento do SharePoint. Arquivo Elements XML para o receptor de eventos remotos referencia serviço da web no aplicativo da web e os eventos remotos que você especificou. O exemplo a seguir mostra um arquivo Elements XML que manipula a adição ou exclusão de um item de lista.
    
  ```XML
  
<?xml version="1.0" encoding="utf-8"?>
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <Receivers ListTemplateId="104">
      <Receiver>
        <Name>RemoteEventReceiver1ItemAdding</Name>
        <Type>ItemAdding</Type>
        <SequenceNumber>10000</SequenceNumber>
        <Url>~remoteAppUrl/RemoteEventReceiver1.svc</Url>
      </Receiver>
      <Receiver>
        <Name>RemoteEventReceiver1ItemDeleting</Name>
        <Type>ItemDeleting</Type>
        <SequenceNumber>10000</SequenceNumber>
        <Url>~remoteAppUrl/RemoteEventReceiver1.svc</Url>
      </Receiver>
  </Receivers>
</Elements>
  ```

Para alterar os eventos que as alças de receptor de evento remoto, abra o **Solution Explorer**, abra a janela **Propriedades** para o receptor de evento remoto, expanda o nó de **Eventos do SharePoint** e, em seguida, defina somente os eventos que você deseja lidar com a **True**.
  
    
    

> [!OBSERVAçãO]
> Para obter informações adicionais sobre RERs, incluindo algumas informações de solução de problemas, consulte  [Receptores de eventos remoto perguntas Frequentes](handle-events-in-sharepoint-add-ins.md#RERFAQ).
  
    
    


## Manipulando eventos de suplemento
<a name="HandlingAppEvents"> </a>

Eventos de suplemento também são manipulados pelos serviços web remoto, mas eles são configurados diferentemente no pacote suplemento da lista e item da lista RERs, portanto, eles são tratados como uma categoria separada de componente. Para um evento de suplemento, o serviço web remoto está registrado no manifesto suplemento, não em um suplemento de web recurso. O suplemento ainda não tem ter um suplemento de web. Há três eventos suplemento conforme descrito nas próximas seções.
  
    
    

### Evento AppInstalled

O evento **AppInstalled** executa imediatamente depois que o SharePoint concluiu a tudo que ele precisa fazer quando o suplemento está instalado, mas antes que o usuário é notificado de que a instalação for concluída. Embora esta seja um evento *depois*  , SharePoint executa seu manipulador *de maneira síncrona*  . O suplemento não está disponível para uso até após seu manipulador foi concluída e seu manipulador pode cancelar a instalação (que fará com que o SharePoint reverter tudo que isso tenha feito como parte da instalação). Na verdade, é uma prática recomendada para capturar erros no seu manipulador e instruir o SharePoint para reverter a instalação. Para obter mais informações, consulte [Incluir lógica de reversão e "tiver feito" lógica em seus manipuladores de eventos de suplemento](#Rollback).
  
    
    

> [!OBSERVAçãO]
> Quando você instala um suplemento de com  [escopo de locatário](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md), é instalado para o conjunto de sites do catálogo add-in e o AppInstalled evento é executado, em seguida e, em seguida, somente. O suplemento é visível em vários sites da web no locatário, mas o evento não é executado separadamente para cada uma dessas.
  
    
    

Além de cancelamento de uma instalação do suplemento, esse evento pode ser usado para muitas outras finalidades incluindo:
  
    
    

- Instale componentes do SharePoint em que não pode ser instalada de forma declarativa com a web host recurso, como listas ou subwebs web host.
    
  
- Registre programaticamente lista e manipuladores de eventos de item de lista com o suplemento web ou da web do host.
    
  
- Defina configurações de inicialização relativa de instância de aplicativo. Por exemplo, o suplemento pode ter um recipiente de propriedades de web do suplemento para configurações de retenção que variam de uma instância do add-in para outra. O manipulador AppInstalled pode gravar valores que variam o recipiente de propriedades, com base nas, digamos, o site digite da web host (por exemplo, sites de equipe ou o site de Blog).
    
    > [!OBSERVAçãO]
      > Para verificar se a web de host é um AppCatalog site é uma boa maneira de detectar se o suplemento foi instalado com escopo de locatário. Consulte  [Aluguéis são e escopos de implantação para o SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md).
- Execute aplicativo da web remoto do add-in, como a adição de uma tabela para um banco de dados configuração relativa de instância de aplicativo.
    
  

> [!IMPORTANTE]
> Conclua a sua implementação do evento AppInstalled dentro de 30 segundos ou a infraestrutura de instalação do SharePoint irá pensar que ele falhar. A infraestrutura será execute novamente o evento,  *e repita seu código desde o início*  , até três vezes adicionais. Depois de quatro tempos limite, SharePoint reverterá a instalação do suplemento inteira. As implicações desses fatos são abordadas em [Incluir lógica de reversão e "tiver feito" lógica em seus manipuladores de eventos de suplemento](#Rollback).
  
    
    


### Evento AppUninstalling

O evento **AppUninstalling** *não*  executar quando o suplemento é removido da web host. Remoção de um suplemento move somente o suplemento para o Lixeira do usuário. Mais duas etapas são necessárias antes do evento AppUninstalling é disparado. Primeiro, um usuário deve remover o add-in da Lixeira, que move para a Lixeira de segundo estágio. Segundo, um usuário **Remova o add-in da Lixeira de segundo estágio. Esta última tarefa dispara o evento AppUninstalling**. O evento AppUninstalling é síncrono e você pode usá-lo para cancelar a desinstalação, qual seria deixar o suplemento na Lixeira de segundo estágio.
  
    
    
A finalidade principal de um manipulador para este evento é excluir ou reciclar coisas que foram implantadas com um AppInstalled (ou um AppUpdated) manipulador. SharePoint não é possível excluir essas coisas ou transfira-os para a Lixeira, porque não saber sobre eles, pelo menos não como componentes do add-in. Geralmente é uma boa prática para remover essas coisas. Mas você não deseja excluir coisas que ainda têm uma vida útil depois que o suplemento desapareceu: se uma lista ou um site criado pelo seu manipulador AppInstalled ainda vai ser usado, não exclua-lo em seu manipulador AppUninstalling.
  
    
    

### Evento AppUpgraded

O evento **AppUpgraded** executa imediatamente após a conclusão SharePoint tudo de que ele precisa fazer quando o suplemento é atualizado para uma nova versão, mas antes que o usuário é notificado de que a atualização é concluída. Como o evento AppInstalled, é um evento depois, mas é essencialmente síncrona e é a melhor prática capturar erros e notificar SharePoint para reverter a atualização.
  
    
    
Alguns exemplos do que um manipulador para este evento pode fazer:
  
    
    

- Adicionar, alterar ou remover componentes do suplemento do web host.
    
  
- Fazer coisas no suplemento da web que não são possíveis com a semântica de atualização declarativos em um suplemento de web recurso. Por exemplo, não é possível excluir qualquer coisa com a marcação declarativa de atualização, mas é possível fazer isso programaticamente em um manipulador de AppUpgraded.
    
  
- Fazer alterações em componentes relativa de instância de aplicativo no suplemento aplicativo web ou banco de dados remoto.
    
  
 *Etapas detalhadas para criar suplemento manipuladores de eventos está em  [Criar um receptor de evento add-in SharePoint Add-ins](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)*  .
  
    
    

### Incluir lógica de reversão e "tiver feito" lógica em seus manipuladores de eventos de suplemento
<a name="Rollback"> </a>

Se SharePoint encontra um erro durante o processamento de qualquer uma das três eventos de suplemento, ele será cancelar o evento e reverte as alterações feitas em conexão com o evento. Os manipuladores de eventos de suplemento precisa integrar com esse sistema porque se a parte do evento que você estiver implementando falhar, você deseja que o evento todo para reverter, em vez de continuar e deixar as coisas em um estado possivelmente corrompido. Aqui está o que seu manipulador geralmente tem a ver:
  
    
    

- Diga-SharePoint que ocorreu um erro. A mensagem SOAP que seu suplemento manipulação de eventos serviço web retorna para o SharePoint terão uma propriedade de **Status** que pode ter os valores de **Continue**, **CancelWithError**ou **CancelWithoutError**. Qualquer um dos status **Cancel*** informa ao SharePoint para reverter o evento.
    
  
- Reverter o que o manipulador já tenha feito antes do manipulador encontra o erro. SharePoint não é geralmente fazer isso para você porque não saber o que fez o manipulador. Isso não é uma regra universal. Por exemplo, se uma instalação do suplemento for cancelada, SharePoint excluirá a suplemento web inteira, portanto, não há nenhum ponto em um manipulador de eventos de AppInstalled Revertendo nada que tem feito na Web suplemento. Mas, ele geralmente deve reverter coisas fizesse na Web do host ou para componentes remotos do add-in.
    
  

> [!OBSERVAçãO]
> **Observação especial sobre o evento AppUninstalling:** Os pontos anteriores se aplicam ao evento AppUninstalling tanto quanto aos outros dois suplemento eventos. Por exemplo, se seu manipulador para o evento uninstalling exclui uma linha em um banco de dados remoto e, em seguida, encontra um erro, a linha deve ser restaurado. Desde que o seu serviço enviará uma mensagem de cancelar ao SharePoint, o suplemento não será removido da Lixeira. Se ele é restaurado a partir daí e usado novamente, pode falhar ao funcionar sem essa entrada de banco de dados.> Entretanto, o seu manipulador AppUninstalling conclui  *antes*  SharePoint remove o suplemento da Lixeira. Assim, se SharePoint próprio encontra um erro e precisa para cancelar a remoção, não é possível para o manipulador desfazer o que aconteceu.
  
    
    

Se SharePoint não receber uma mensagem de resultados do seu manipulador em 30 segundos, ele chamará o manipulador novamente. Ele fornece inteiramente e reverte o evento, após três tentativas (quatro tentam em todas). Cada vez que ele chama o manipulador, seu código inicia novamente desde o início. Mas geralmente você não deseja que o manipulador para refazer coisas que já tenha feito, como criar uma lista na web host e você não sabe se sua lógica de reversão foi concluída ou até mesmo disparada, antes do manipulador se esgotou. Por esse motivo, sua lógica do manipulador deve não executar qualquer ação sem verificar primeiro se a ação já tiver sido estabelecida, a menos que seria inofensiva para fazê-la novamente.
  
    
    
Instalação e a atualização de erros que podem ser vistos na UI SharePoint, conforme mostrado no seguinte gráfico.
  
    
    

**Figura 1. Obtendo detalhes do erro de instalação.**

  
    
    

  
    
    
![Etapas para ver erros de instalação de suplemento no SharePoint.](images/1edb644e-b3d4-4a9b-b92e-4ae2c07341c2.png)
  
    
    

#### Estratégias de arquitetura do manipulador de eventos de suplemento
<a name="Strategies"> </a>

Expresso em pseudocódigo, seu manipulador geralmente deve ser estruturado parecido com o seguinte. Se ocorrer um erro na seção Try, a seção Catch e Rollback deve ser chamada. (Isso pode acontecer automaticamente dependendo do idioma e a estrutura.)
  
    
    

```

Try
    If X not already done,
        Do X.
Catch
    Send cancel message to SharePoint.
    If X not already undone,
        Undo X.

```

No entanto, a implementação de sua reversão e a lógica de "já concluída" no seu serviço da web pode diminuir o manipulador. A instalação e a lógica de reversão geralmente fazer alterações em algo mais ou menos remoto do serviço da web, como web host SharePoint ou um banco de dados de back-end. Se seu código de instalação e rollback é dividido entre as seções Try e Catch, o serviço é fazendo chamadas separadas para os componentes remotos, geralmente várias dessas chamadas em cada seção. A prática recomendada é geralmente implementar a instalação e a lógica de reversão no componente remoto próprio em um procedimento que pode ser chamado de seu manipulador na seção tentar. O procedimento deve retornar uma mensagem de sucesso ou falha e, se ele relatar falha, código na sua seção Try invoca seção Catch (, digamos, emitindo uma exceção). A única coisa que faz de seção Catch é notificar SharePoint. Que isso chamaremos a estratégia de delegação do manipulador . O seguinte código pseudo ilustra a estratégia de:
  
    
    



```

Try
    Call the "Do X" procedure on remote platform.
    If remote platform reports failure, call Catch.
Catch
    Send cancel message to SharePoint.

```

O procedimento "Faça X", que é executado no sistema remoto, próprio conteria a reversão e a lógica de "tiver feito" semelhante ao seguinte.
  
    
    



```

Try
    If X not already done,
        Do X.
        Set success flag to true.
Catch
    If X was done before error,
        Undo X.
    Set success flag to false.
Send
    Return success flag to the event handler.

```

Por exemplo, se seu manipulador precisará tomar ação em um banco de dados do SQL Server, você poderá instalar um procedimento armazenado no SQL Server que usa um bloco  [TRY-CATCH](http://msdn.microsoft.com/library/248df62a-7334-4bca-8262-235a28f4b07f%28Office.15%29.aspx) para implementar a lógica de reversão da instalação. com blocos [IF-ELSE](http://msdn.microsoft.com/library/676c881f-dee1-417a-bc51-55da62398e81%28Office.15%29.aspx) implementar lógica "já concluída".
  
    
    
O modelo de suplemento SharePoint não fornece uma maneira para armazenar o código personalizado do lado do servidor no SharePoint e chamá-la do CSOM (modelo de objeto do cliente). Mas o CSOM possibilitam agrupam try-catch e if-then-else lógica e enviá-la ao servidor para execução. Para um exemplo detalhado de um manipulador de eventos de suplemento que usa a estratégia de delegação manipulador para adicionar uma lista em uma web host, consulte  [Criar um receptor de evento add-in SharePoint Add-ins](create-an-add-in-event-receiver-in-sharepoint-add-ins.md). Para obter um exemplo de código, consulte  [OfficeDev/PnP/Samples/Core.AppEvents.HandlerDelegation](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents.HandlerDelegation).
  
    
    
Sempre é possível usar a estratégia de delegação manipulador. Por exemplo, ao seu manipulador é chamar check-out a mais de um componente, como um banco de dados e web host do SharePoint, há uma chance de que uma foi concluída com sucesso e, em seguida, o outro falharem. Neste cenário, a lógica de reversão para o primeiro componente não é executada se o que foi criada com a estratégia de delegação do manipulador. Por esse motivo, se você estiver chamando aos componentes de maneira síncrona, apenas o último em uma chamada pode usar a estratégia de delegação do manipulador. Se eles são chamados de forma assíncrona, é possível usar essa estratégia em qualquer uma delas. Para obter um exemplo de um manipulador de eventos de suplemento que não usa a estratégia de delegação do manipulador, consulte  [OfficeDev/PnP/Samples/Core.AppEvents](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.AppEvents).
  
    
    

> [!DICA]
> Se o evento AppInstalled falhar, o SharePoint excluirá web add-in, se houver algum; e se o evento AppUpated falhar, o SharePoint restaurará web suplemento ao seu estado de pré-atualização. Por esse motivo, seus manipuladores nunca precisará ações reversão levem na web suplemento. Se manipulador você executa ações na web host e o suplemento web, ele deve lidar com a web suplemento pela primeira vez. Isso torna segura usar a estratégia de delegação do manipulador para a web host. Mesmo se as ações suplemento web bem-sucedidas e as ações do host web falharem, não há nenhuma lógica de reversão que vai não executada.
  
    
    


## Receptores de eventos remoto em suplementos que oferecem suporte a várias zonas de segurança
<a name="HandlingAppEvents"> </a>

Existem algumas restrições sobre como criar um suplemento que dá suporte a várias zonas de segurança e tem um receptor de eventos remotos. Para mais informações, consulte o Knowledge Base kb3135876  [não é possível adicionar um aplicativo do repositório do SharePoint padrão, quando você usa o provedor hospedado em suplementos em zonas de não-padrão no sharepoint2013](https://support.microsoft.com/en-us/kb/3135876)do artigo.
  
    
    

## Receptores de eventos remoto perguntas Frequentes
<a name="RERFAQ"> </a>

A seguir estão as perguntas comuns que você pode ter ao usar receptores de eventos remoto.
  
    
    

### Receptores de eventos remotos são diferentes de receptores de eventos no SharePoint 2010?
<a name="RER_HowRERDifferentfrom2010"> </a>

Em SharePoint 2010, receptores de evento manipular eventos que ocorrem em listas do SharePoint, sites e outros objetos do SharePoint, executando o código no servidor do SharePoint (confiança total ou em uma área restrita). Esse tipo de receptor de evento ainda existe no SharePoint 2013. No entanto, SharePoint 2013 também suporta receptores de evento  *remoto*  no qual o código que é executado quando o evento é disparado é hospedado por um serviço web. Isso significa que se registrar um receptor de evento remoto, você também precisa saber quais serviços web para invocar a SharePoint. Na tabela 1, o exemplo de código à esquerda (soluções do SharePoint) implementa a funcionalidade usando um manipulador de eventos. O exemplo à direita (Suplementos do SharePoint ) implementa a mesma funcionalidade usando um receptor de evento remoto.
  
    
    

**Tabela 1. Exemplos de códigos para receptores de eventos no SharePoint 2010 versus receptores de eventos remoto em suplementos**


|**Soluções do SharePoint**|**Suplementos do SharePoint**|
|:-----|:-----|
|
```cs

// Trigger an event when an item is added to the SharePoint list.
Public class OnPlantUpdated : SPItemEventReceiver
{
Public override void ItemAdding (SPItemEventProperties properties)
{
Properties.After.Properties.ChangedProperties.Add("Image",CreateLink(properties));
Properties.status =SPEventReceiverStatus.Continue;
}

/// When an item updates, run the following.
Public override void ItemUpdating(SPItemEventProperties properties)
{
Properties.AfterProperties.ChangedProperties.Add("Image",CreateLink9properties));
Properties.Status= SPEventReceiverStatus.Continue;
}

```

|
```cs

/* Trigger an event when an item is added to the SharePoint list*/
Public class OnPlantUpdated : IRemoteEventService
{
public SPRemoteEventResult ProcessEvent (SPRemoteEventProperties properties)
{
SPRemoteEventResult result =new SPRemoteEventResult();
If (properties.EventType == SPRemoteEventType.ItemAdding ||  
properties.EventType == SPRemoteEventType.ItemUpdating)
{

// Add code that runs when an item is added or updated.
}

```

|
   
Consulte  [Adicionar propriedades de item da lista com um receptor de eventos remotos](http://code.msdn.microsoft.com/SharePoint-2013-Add-list-2c6e71e0) de exemplo de código completo. Consulte [Migrando um receptor de eventos de SharePoint a um receptor de eventos remotos](http://channel9.msdn.com/Series/Reimagine-SharePoint-Development/Migrating-a-SharePoint-Event-Receiver-to-a-Remote-Event-Receiver) for uma demonstração detalhada do código de exemplo.
  
    
    

### 
<a name="RER_HowRERDifferentfrom2010"> </a>

Para obter informações, consulte  [SPRemoteEventType enumeração](https://msdn.microsoft.com/pt-br/library/microsoft.sharepoint.client.eventreceivers.spremoteeventtype.aspx)
  
    
    

### Como funcionam os receptores de eventos remoto?
<a name="RER_HowDoRERWork"> </a>

A Figura 1 mostra a trabalhar de receptores de evento como remoto:
  
    
    

- O usuário realiza uma ação nos SharePoint (por exemplo, edita um item de lista).
    
  
- Em seguida, SharePoint comunica-se ao serviço web registrado. Você pode executar algumas operações  por exemplo, uma propriedade de item de lista de atualização ou atualizar um sistema back-end.
    
  
- O serviço da web também pode conversar para Access Control Service (ACS) para solicitar o seu próprio token assinado fazer uma chamada para o SharePoint. Usando este token, você pode executar ações de remotas de dentro do serviço da web como resultado da operação anterior no item da lista ou no sistema back-end.
    
  

**Figura 2. Receptores de evento remoto como funcionam no SharePoint**

  
    
    

  
    
    
![How remote event receivers work in SharePoint 2013](images/SP15Con_Remote_Event_Receivers_FAQ_fig1.png)
  
    
    

  
    
    

  
    
    

### Como depurar as receptores de evento remoto?
<a name="RER_DebugRER"> </a>

Consulte  [Depurar e solucionar problemas de um receptor de evento remoto em um SharePoint Add-in](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md).
  
    
    

### Pode executar código do cliente (JavaScript) de receptores de eventos remotos?
<a name="RER_ClientsideCodeFromRER"> </a>

Não.
  
    
    

### Há alguma restrição no onde um receptor de eventos remotos pode ser hospedado ou em sua URL?
<a name="RER_ClientsideCodeFromRER"> </a>

O receptor de evento remoto pode ser hospedado em nuvem ou em um servidor no local que também não está sendo usado como um servidor SharePoint. A URL de um receptor de produção, não é possível especificar uma porta específica. Isso significa que você deve usar qualquer porta 443 para HTTPS, que é recomendável, ou a porta 80 para HTTP. Se estiver usando HTTPS e o serviço receptor está hospedado no local, mas o add-in estiver na Microsoft SharePoint Online e o servidor de hospedagem deve ter um certificado confiável publicamente em uma autoridade de certificação. (Um certificado autoassinado funciona somente se o suplemento estiver em um farm do SharePoint local.)
  
    
    

### Um manipulador de eventos do SharePoint 2010 funcionarão no sharepoint2013 depois posso atualizar?
<a name="RER_Will2020EventHandlerWillWorkOn2013"> </a>

Se um pacote de solução de SharePoint 2010 que contém um manipulador de eventos é atualizado para SharePoint 2013, dependendo de suas personalizações, o pacote de solução pode funcionar sem quaisquer modificações. Isso incluirá o manipulador de eventos muito. Se a solução SharePoint 2010 é remodelada em um Suplemento do SharePoint em SharePoint 2013, o manipulador de eventos deve ser reescrito como um receptor de evento remoto. (Consulte  [Migrando um receptor de evento do SharePoint para um receptor de evento remoto](http://channel9.msdn.com/Series/Reimagine-SharePoint-Development/Migrating-a-SharePoint-Event-Receiver-to-a-Remote-Event-Receiver).)
  
    
    

## Recursos adicionais
<a name="SP15handleevents_addlresources"> </a>


-  [Criar um receptor de evento remoto no SharePoint Add-ins](create-a-remote-event-receiver-in-sharepoint-add-ins.md)
    
  
-  [Criar um receptor de evento add-in SharePoint Add-ins](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  
-  [Receptores de eventos remotos Introducing no SharePoint 2013](http://www.microsoft.com/resources/msdn/en-us/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;VideoID=3ef8f7ae-85a7-44c3-967d-d1620e2a019f)
    
  
-  [Migrando um receptor de eventos SharePoint a um receptor de eventos remotos](http://channel9.msdn.com/Series/Reimagine-SharePoint-Development/Migrating-a-SharePoint-Event-Receiver-to-a-Remote-Event-Receiver)
    
  

