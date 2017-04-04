---
title: Lidar com eventos de item de lista em que o suplemento hospedado em provedor
ms.prod: SHAREPOINT
ms.assetid: 4534e0f5-61ef-4145-a63b-a9fa70f51391
---


# Lidar com eventos de item de lista em que o suplemento hospedado em provedor
Aprenda a lidar com eventos de item de lista em um provedor hospedado Suplemento do SharePoint.
Esse é o décimo em uma série de artigos sobre noções básicas do desenvolvimento hospedado em provedor Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Dar seu suplemento a aparência do SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [Incluir um botão personalizado do add-in](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [Obtenha uma visão geral do modelo de objeto do SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [Adicionar operações de gravação do SharePoint para o suplemento](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [Include an add-in part in the provider-hosted add-in](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  
-  [Lidar com eventos de suplemento do add-in](handle-add-in-events-in-the-provider-hosted-add-in.md)
    
  
-  [Adicionar lógica de primeira execução para o suplemento](add-first-run-logic-to-the-provider-hosted-add-in.md)
    
  
-  [Implantar programaticamente um botão personalizado do add-in](programmatically-deploy-a-custom-button-in-the-provider-hosted-add-in.md)
    
  

> **OBSERVAçãO**
> Se você tiver trabalhado através desta série sobre hospedado em provedor suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) e abra o arquivo BeforeRER.sln.
  
    
    

Vimos em um artigo anterior desta série quando um pedido, ela será adicionada à tabela de **pedidos** no banco de dados corporativo e um item para que ele é automaticamente adicionado à lista de **Remessas esperado**. Quando ela chegar ao armazenamento local, um usuário define a coluna **recebido** como **Sim**. Alterar o valor de um campo para um item cria um evento updated do item para o qual você pode adicionar um manipulador personalizado. Neste artigo, você criará um manipulador para este evento de item de lista e depois implantá-la programaticamente na primeira execução lógica do Suplemento do SharePoint. O manipulador irá adicionar o item para a tabela de **estoque** no banco de dados corporativo. Em seguida, ele definirá a coluna **adicionada para inventário** da lista **Remessas esperado** como **Sim**. Você também aprenderá como impedir que este evento updated do item segundo desde a configuração de uma série infinita de eventos item atualizados.
## Implantar programaticamente a lista de remessas esperado


> **OBSERVAçãO**
> As configurações para projetos de inicialização no Visual Studio tendem a reverter para a configuração padrão sempre que a solução for reaberta. Sempre, siga estas etapas imediatamente após reabri-lo a solução de exemplo nesta série de artigos:
  
    
    


1. No **Solution Explorer**, abra o arquivo de Utilities\\SharePointComponentDeployer.cs no projeto **ChainStoreWeb**. Adicione o seguinte método à classe  `SharePointComponentDeployer` . Esse código não introduza qualquer funcionalidade que você já não tenha visto em um artigo anterior desta série, mas observe o seguinte:
    
  - Ele define o atributo **Required** do campo **quantidade** como **TRUE**, portanto, o campo sempre deve ter um valor. Em seguida, define o valor padrão como 1.
    
  
  - Os campos **recebido** e **adicionada para inventário** estão ocultos no formulário Novo Item.
    
  
  - Idealmente, também estaria oculta o campo **foi adicionado ao estoque** no formulário Editar Item, porque ele só deverá ser alterado para **Sim** quando o item atualizado o manipulador de eventos adicionou primeiramente o item à tabela **inventário** corporativa. Por razões técnicas que vamos explicar em uma etapa posterior, um campo deve estar visível no formulário Editar Item, se quisermos programaticamente gravar nele em um manipulador de evento updated do item.
    
  

  ```cs
  
private static void CreateExpectedShipmentsList()
 {
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
        var query = from list in clientContext.Web.Lists
                    where list.Title == "Expected Shipments"
                    select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();
                   
        if (matchingLists.Count() == 0)
        {
                ListCreationInformation listInfo = new ListCreationInformation();
                listInfo.Title = "Expected Shipments";
                listInfo.TemplateType = (int)ListTemplateType.GenericList;
                listInfo.Url = "Lists/ExpectedShipments";
                List expectedShipmentsList = clientContext.Web.Lists.Add(listInfo);

                Field field = expectedShipmentsList.Fields.GetByInternalNameOrTitle("Title");
                field.Title = "Product";
                field.Update();

                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Supplier'" 
                                                            + " Type='Text' />", 
                                                            true,
                                                            AddFieldOptions.DefaultValue);
                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Quantity'" 
                                                            + " Type='Number'" 
                                                            + " Required='TRUE' >" 
                                                            + "<Default>1</Default></Field>",
                                                            true, 
                                                            AddFieldOptions.DefaultValue);
                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Arrived'" 
                                                           + " Type='Boolean'"
                                                           + " ShowInNewForm='FALSE'>"
                                                           + "<Default>FALSE</Default></Field>",
                                                            true, 
                                                            AddFieldOptions.DefaultValue);
                expectedShipmentsList.Fields.AddFieldAsXml("<Field DisplayName='Added to Inventory'" 
                                                            + " Type='Boolean'" 
                                                            + " ShowInNewForm='FALSE'>"
                                                            + "<Default>FALSE</Default></Field>", 
                                                            true, 
                                                            AddFieldOptions.DefaultValue);

                clientContext.ExecuteQuery();
        }
     }
 }
  ```

2. No método  `DeployChainStoreComponentsToHostWeb` , adicione a seguinte linha, logo acima a linha `RemoteTenantVersion = localTenantVersion`.
    
  ```
  
CreateExpectedShipmentsList();
  ```


## Criar o receptor de evento de item de lista


> **OBSERVAçãO**
> Se você tiver trabalhado por meio desta série de artigos, você já tiver configurado o seu ambiente de desenvolvimento para depuração receptores de evento remoto. Se você não tiver feito isso, consulte  [Configurar a solução para o receptor de evento de depuração](handle-add-in-events-in-the-provider-hosted-add-in.md#RERDebug) antes de ir qualquer neste tópico.
  
    
    

O Office Developer Tools for Visual Studio incluem um item de **Receptor de evento remoto** que pode ser adicionado a uma solução de Suplemento do SharePoint. No entanto, no momento em que este artigo foi escrito, esse item de projeto pressupõe que a lista (com o qual o receptor será registrado) está na web suplemento e consequentemente as ferramentas de criam um suplemento de web e alguns artefatos SharePoint nela. Mas o receptor para o suplemento do repositório de cadeia vai ser registrada (em uma etapa posterior) com a lista de **Remessas esperado** na web host, o suplemento precisa um suplemento de web. (Para um lembrete da distinção entre o suplemento webs e webs de host, consulte [Suplementos do SharePoint](sharepoint-add-ins.md).)
  
    
    

> **OBSERVAçãO**
> Lista e receptores de evento de item de lista são chamados de receptores de evento remoto (RER) porque seu código é remoto do SharePoint, na nuvem ou em um servidor local fora do farm do SharePoint. No entanto, os eventos que acionam a eles estão no SharePoint.
  
    
    


1. No **Solution Explorer**, clique com o botão a pasta de **Serviços** do projeto **ChainStoreWeb** e selecione **Adicionar | Serviço WCF**.
    
  
2. Quando solicitado, nomeie o serviço RemoteEventReceiver1e pressione **OK**.
    
  
3. As ferramentas de criam um arquivo de interface, um arquivo SVC e um arquivo code-behind. Não precisamos o arquivo de interface IRemoteEventReceiver1.cs, portanto excluí-la. (As ferramentas podem ter aberto-lo automaticamente. Em caso afirmativo, feche e excluí-la.)
    
    > **OBSERVAçãO**
      > Quando você criou os receptores de evento suplemento para o instalados e desinstalando eventos em um artigo anterior desta série, o Office Developer Tools for Visual Studio adicionado suas URLs para o arquivo de manifesto do aplicativo. Lista e receptores de evento de item de lista não estão registrados no manifesto do aplicativo. Em vez disso, eles são registrados (em um provedor hospedado suplemento) programaticamente. Você vai fazer isso em uma etapa posterior.
4. Abra o arquivo code-behind: RemoteEventReceiver1.svc.cs. Substitua todo o seu conteúdo com o código a seguir. Observe o seguinte sobre este código:
    
  - A interface  `IRemoteEventService` é definida no namespace **Microsoft.SharePoint.Client.EventReceivers**.
    
  
  - Não haverá qualquer "antes" eventos manipulados no suplemento do repositório de cadeia, mas o método **ProcessEvent** é exigido pela interface `IRemoteEventService` .
    
  

  ```cs
  using System;
using System.Collections.Generic;
using Microsoft.SharePoint.Client;
using Microsoft.SharePoint.Client.EventReceivers;
using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;

namespace ChainStoreWeb.Services
{
    public class RemoteEventReceiver1 : IRemoteEventService
    {
        /// <summary>
        /// Handles events that occur before an action occurs, 
        /// such as when a user is adding or deleting a list item.
        /// </summary>
        /// <param name="properties">Holds information about the remote event.</param>
        /// <returns>Holds information returned from the remote event.</returns>
        public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {
            throw new NotImplementedException();
        }

        /// <summary>
        /// Handles events that occur after an action occurs, 
        /// such as after a user adds an item to a list or deletes an item from a list.
        /// </summary>
        /// <param name="properties">Holds information about the remote event.</param>
        public void ProcessOneWayEvent(SPRemoteEventProperties properties)
        {

        }
    }
}
  ```

5. Adicione o seguinte código para o método  `ProcessOneWayEvent` . Observe se o evento **ItemUpdated** é a única pessoa que lidará com esta amostra, portanto, poderíamos ter usado uma estrutura simples **if** em vez de um **switch**. Mas receptores de evento normalmente manipulam vários eventos, por isso, queremos que você veja o padrão que mais comumente serem utilizadas em seus manipuladores de eventos como um suplemento para desenvolvedores do SharePoint.
    
  ```cs
  
switch (properties.EventType)
{
    case SPRemoteEventType.ItemUpdated:

        // TODO12: Handle the item updated event.
                    
        break;
}  
  ```

6. Substitua  `TODO12` o código a seguir. Novamente, aqui, estamos usando uma estrutura **switch** quando uma estrutura simples **if** faria, porque queremos que você veja o padrão comuns de receptores de eventos do SharePoint.
    
  ```cs
  
switch (properties.ItemEventProperties.ListTitle)
{
    case "Expected Shipments":

        // TODO13: Handle the arrival of a shipment.

        break;
}
  ```

7. O código que responde a solicitações a chegada de uma remessa deve fazer duas coisas:
    
  - Adicione o item que foi entregue na loja para o inventário corporativo.
    
  
  - Defina o campo de **adicionada para inventário** na lista de **Remessas esperado** como **Sim**. Mas isso deve acontecer somente se o item foi adicionado com êxito para o inventário.
    
  

    Adicione o seguinte código no lugar de  `TODO13`. Os dois métodos,  `TryUpdateInventory` e `RecordInventoryUpdateLocally` são criados nas etapas posteriores.
    


  ```cs
  
bool updateComplete = TryUpdateInventory(properties);
if (updateComplete)
{
    RecordInventoryUpdateLocally(properties);
}
  ```


    O método  `ProcessOneWayEvent` agora deve parecer com o seguinte:
    


  ```cs
  
public void ProcessOneWayEvent(SPRemoteEventProperties properties)
{
    switch (properties.EventType)
    {
        case SPRemoteEventType.ItemUpdated:

            switch (properties.ItemEventProperties.ListTitle)
            {
                case "Expected Shipments":
                    bool updateComplete = UpdateInventory(properties);
                    if (updateComplete)
                    {
                        RecordInventoryUpdateLocally(properties);
                    }
                    break;
            }
            break;
    }          
}
  ```

8. Adicione o seguinte método à classe  `RemoteEventReceiver1` .
    
  ```cs
  
private bool TryUpdateInventory(SPRemoteEventProperties properties)
{
    bool successFlag = false;

        // TODO14: Test whether the list item is changing because the product has arrived
        // or for some other reason. If the former, add it to the inventory and set the success flag
        // to true.     

    return successFlag;
}
  ```

9. Há cinco colunas na lista de **Remessas esperado**, mas não queremos ao manipulador de reagir à maioria dos tipos de atualizações para um item. Por exemplo, se um usuário corrigir a ortografia do nome do fornecedor, o item atualizado evento é acionado, mas nosso manipulador não deve fazer nada. O manipulador deve atuar apenas quando o campo **recebido** apenas tiver sido definido como **Sim**.
    
    Não há outra condição que precisa ser testado. Suponha que **recebido** é definida como **Sim** e o produto no item é adicionado ao estoque (e **adicionado ao estoque** estiver definida como **Sim** ). Mas posteriormente um usuário por engano altera o campo **recebido** da remessa volta como **não** e então corrige seu erro definindo-a novamente para **recebido**. O erro e a correção gatilho o item atualizado o evento. O manipulador não reage até o erro porque ele atua apenas quando **recebido** é **Sim**, mas ele seria reagir na correção que define **recebido** volta para **Sim**, portanto, o mesmo produto e a quantidade seriam adicionadas para o inventário uma segunda vez. Por esse motivo, o manipulador deve atuar apenas quando o valor **foi adicionado ao estoque** é **não**.
    
    Portanto, o manipulador precisa saber quais são os valores desses campos apenas depois que o usuário atualiza o item. O objeto **SPRemoteEventProperties** tem uma propriedade **ItemEventProperties**. E, em seguida, ele tem uma propriedade **AfterProperties** indexada que contém os valores dos campos no item atualizado. O código a seguir usa essas propriedades para testar se o manipulador deve reagir. Colocar isso no lugar de `TODO14`.
    


  ```cs
  
var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);
var addedToInventory = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Added_x0020_to_x0020_Inventory"]);

if (arrived &amp;&amp; !addedToInventory)
{

    // TODO15: Add the item to inventory

    successFlag = true;
}
  ```

10. Substitua  `TODO15` o código a seguir. Isso é principalmente SQL e ASP.NET de programação, por isso estamos não discutir isso detalhadamente, mas observe:
    
  - Usamos a propriedade **ItemEventProperties.WebUrl** para obter o nome do locatário, o que é o host URL da web.
    
  
  - Usamos o **AfterProperties** novamente para obter os valores do nome do produto e a quantidade.
    
  
  - Nos referimos ao campo nome do produto como "Title", embora o nome de exibição foi alterado para "Product" (no método  `CreateExpectedShipmentsList` ), porque campos sempre são referidos por seus nomes internos.
    
  

  ```cs
  
using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
using (SqlCommand cmd = conn.CreateCommand())
{
    conn.Open();
    cmd.CommandText = "UpdateInventory";
    cmd.CommandType = CommandType.StoredProcedure;
    SqlParameter tenant = cmd.Parameters.Add("@Tenant", SqlDbType.NVarChar);
    tenant.Value = properties.ItemEventProperties.WebUrl + "/";
    SqlParameter product = cmd.Parameters.Add("@ItemName", SqlDbType.NVarChar, 50);
    product.Value = properties.ItemEventProperties.AfterProperties["Title"]; // not "Product"
    SqlParameter quantity = cmd.Parameters.Add("@Quantity", SqlDbType.SmallInt);
    quantity.Value = Convert.ToUInt16(properties.ItemEventProperties.AfterProperties["Quantity"]);
    cmd.ExecuteNonQuery();
}
  ```


    Podemos não ainda terminou com o método  `TryUpdateInventory` , mas neste ponto deve se parecer com o seguinte.
    


  ```cs
  
private bool TryUpdateInventory(SPRemoteEventProperties properties)
{
    bool successFlag = false;

    var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);
    var addedToInventory = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Added_x0020_to_x0020_Inventory"]);

    if (arrived &amp;&amp; !addedToInventory)
    {
        using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
        using (SqlCommand cmd = conn.CreateCommand())
        {
            conn.Open();
            cmd.CommandText = "UpdateInventory";
            cmd.CommandType = CommandType.StoredProcedure;
            SqlParameter tenant = cmd.Parameters.Add("@Tenant", SqlDbType.NVarChar);
            tenant.Value = properties.ItemEventProperties.WebUrl + "/";
            SqlParameter product = cmd.Parameters.Add("@ItemName", SqlDbType.NVarChar, 50);
            product.Value = properties.ItemEventProperties.AfterProperties["Title"]; // not "Product"
            SqlParameter quantity = cmd.Parameters.Add("@Quantity", SqlDbType.SmallInt);
            quantity.Value = Convert.ToUInt16(properties.ItemEventProperties.AfterProperties["Quantity"]);
            cmd.ExecuteNonQuery();
        }            
        successFlag = true;
    }  
    return successFlag;
}
  ```

11. Quando o método  `TryUpdateInventory` retorna **true**, nosso manipulador chamará um método (ainda não gravado) que irá atualizar o mesmo item na lista de **Remessas esperado**, definindo o campo **foi adicionado ao estoque** como **Sim**. Isso acontece em si um evento de atualização de item, o manipulador será chamado novamente. (O fato de que o campo **foi adicionado ao estoque** agora é **Sim**, impedirá que o manipulador adicionando a remessa mesma para fazer o inventário de uma segunda vez, mas o manipulador ainda é chamado).
    
    Mas SharePoint tem um comportamento um pouco diferente quando o item atualizado evento for disparado por uma atualização programática:  *apenas inclui, no **AfterProperties**, os campos que foi alterado na atualização.*  Portanto, o campo **recebido** não está presente, desde que apenas o campo **foi adicionado ao estoque** alterado. A linha:
    
     `var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);`
    
    - lançará um **KeyNotFoundException**.
    
    Não há mais de uma maneira para resolver esse problema. Neste exemplo, vamos para capturar a exceção e usar o bloco de **catch** para garantir que o `successFlag` é definido como **false**. Isso garante que o item não será atualizado uma terceira vez.
    
    Colocar tudo no método que é estabelecido entre a primeira linha,  `bool successFlag = false;`e a última linha,  `return successFlag;` , em um bloco de **try**.
    
  
12. Adicione o seguinte bloco de **catch** logo abaixo do bloco de **try**.
    
  ```cs
  
catch (KeyNotFoundException)
{
    successFlag = false;
}
  ```


    > **OBSERVAçãO**
      > O **KeyNotFoundException** também é a razão por que temos deixar o campo **foi adicionado ao estoque** visíveis no formulário Editar Item. SharePoint não inclui campos que estão ocultos no form Editar Item **AfterProperties**.

    Todo o método agora deve se parecer com o seguinte.
    


  ```cs
  
private bool TryUpdateInventory(SPRemoteEventProperties properties)
{
    bool successFlag = false;
    
    try 
    {
        var arrived = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Arrived"]);
        var addedToInventory = Convert.ToBoolean(properties.ItemEventProperties.AfterProperties["Added_x0020_to_x0020_Inventory"]);

        if (arrived &amp;&amp; !addedToInventory)
        {
            using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
            using (SqlCommand cmd = conn.CreateCommand())
            {
                conn.Open();
                cmd.CommandText = "UpdateInventory";
                cmd.CommandType = CommandType.StoredProcedure;
                SqlParameter tenant = cmd.Parameters.Add("@Tenant", SqlDbType.NVarChar);
                tenant.Value = properties.ItemEventProperties.WebUrl + "/";
                SqlParameter product = cmd.Parameters.Add("@ItemName", SqlDbType.NVarChar, 50);
                product.Value = properties.ItemEventProperties.AfterProperties["Title"]; // not "Product"
                SqlParameter quantity = cmd.Parameters.Add("@Quantity", SqlDbType.SmallInt);
                quantity.Value = Convert.ToUInt16(properties.ItemEventProperties.AfterProperties["Quantity"]);
                cmd.ExecuteNonQuery();
            }            
            successFlag = true;
        }  
    }
    catch (KeyNotFoundException)
    {
        successFlag = false;
    }
    return successFlag;
}
  ```

13. Adicione o seguinte método à classe  `RemoteEventReceiver1` . Agora este padrão de código é familiar dos artigos anteriores nesta série. Mas observe uma diferença. O código obtém o objeto **ClientContext** chamando o método **TokenHelper.CreateRemoteEventReceiverClientContext**, em vez do método **SharePointContext.CreateUserClientContextForSPHost** à medida que usamos em código que é chamado para o SharePoint a partir de páginas, como a página EmployeeAdder. O principal motivo para ter diferentes métodos para obter um objeto **ClientContext** é que o SharePoint passa as informações necessárias para criar esses objetos de forma diferente para os receptores de evento de como passá-lo às páginas. Para receptores de evento, ele passa um objeto **SPRemoteEventProperties**, mas para páginas ele passa de um campo especial, chamado um token de contexto no corpo da solicitação que inicia a página add-in.
    
  ```cs
  
private void RecordInventoryUpdateLocally(SPRemoteEventProperties properties)
{
    using (ClientContext clientContext = TokenHelper.CreateRemoteEventReceiverClientContext(properties))
    {
        List expectedShipmentslist = clientContext.Web.Lists.GetByTitle(properties.ItemEventProperties.ListTitle);
        ListItem arrivedItem = expectedShipmentslist.GetItemById(properties.ItemEventProperties.ListItemId);
        arrivedItem["Added_x0020_to_x0020_Inventory"] = true;
        arrivedItem.Update();
        clientContext.ExecuteQuery();
    }
}
  ```

14. Salve e feche o arquivo de código do receptor.
    
  

## Registrar o receptor

A tarefa final é informar SharePoint que temos um receptor personalizado que queremos SharePoint chamar sempre que um item na lista de **Remessas esperado** é atualizado.
  
    
    

1. Abra o arquivo SharePointContentDeployer.cs e adicione a seguinte linha para o método  `DeployChainStoreComponentsToHostWeb` logo abaixo da linha que cria a lista de **Remessas esperado**. Vamos adicionar esse método na próxima etapa. Observe que, passa para o método do objeto de **HttpRequest** que página de iniciar do add-in transmitida ao método `DeployChainStoreComponentsToHostWeb`.
    
  ```cs
  
RegisterExpectedShipmentsEventHandler(request);
  ```

2. Adicione o seguinte método à classe  `SharePointComponentDeployer` .
    
  ```cs
  private static void RegisterExpectedShipmentsEventHandler(HttpRequest request)
{
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())    
    {
        var query = from list in clientContext.Web.Lists
                    where list.Title == "Expected Shipments"
                    select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();

        List expectedShipmentsList = matchingLists.Single();

        // TODO16: Add the event receiver to the list's collection of event receivers.       

        clientContext.ExecuteQuery();
    }
}
  ```

3. Substitua  `TODO16` com as seguintes linhas. Observe que há uma classe de ***CreationInformation** leves para receptores de evento assim como há para listas e itens de lista.
    
  ```cs
  
EventReceiverDefinitionCreationInformation receiver = new EventReceiverDefinitionCreationInformation();
receiver.ReceiverName = "ExpectedShipmentsItemUpdated";
receiver.EventType = EventReceiverType.ItemUpdated;

 // TODO17: Set the URL of the receiver.

expectedShipmentsList.EventReceivers.Add(receiver);

  ```

4. Agora, você precisa saber a URL do receptor de evento de SharePoint. Em produção, ele vai ser no mesmo domínio que as páginas remotos, com o caminho do /Services/RemoteEventReceiver1.svc. Porque o manipulador está sendo registrado na lógica de primeira execução da página de início do add-in, o domínio é no cabeçalho do host do objeto **HttpRequest** para a solicitação que a página de chamada. Nosso código passou desse objeto da página para o método `DeployChainStoreComponentsToHostWeb` , que por si só passadas-lo para o método `RegisterExpectedShipmentsEventHandler` . Isso pode definimos a URL do receptor com o código a seguir.
    
     `receiver.ReceiverUrl = "https://" + request.Headers["Host"] + "/Services/RemoteEventReceiver1.svc";`
    
    Infelizmente, isso não funcionará quando você estiver depurando o suplemento de Visual Studio. Quando você estiver depurando, o receptor está hospedado no barramento de serviço do Windows Azure, não na URL localhost onde as páginas remotas hospedadas. Precisamos definir URLs distintas para o receptor dependendo se depurar ou não, portanto, substituir  `TODO17` com a seguinte estrutura que usa as diretivas de compilador c#. Observe que, no modo de depuração URL do receptor é leitura de uma configuração de Web. config. *Você irá criar essa configuração em uma etapa posterior.* 
    


  ```cs
  
#if DEBUG
                    receiver.ReceiverUrl = WebConfigurationManager.AppSettings["RERdebuggingServiceBusUrl"].ToString();
#else
                    receiver.ReceiverUrl = "https://" + request.Headers["Host"] + "/Services/RemoteEventReceiver1.svc"; 
#endif

  ```


    O método todo  `RegisterExpectedShipmentsEventHandler` agora deve se parecer com o seguinte.
    


  ```cs
  
private static void RegisterExpectedShipmentsEventHandler(HttpRequest request)
{    
    using (var clientContext = sPContext.CreateUserClientContextForSPHost())
    {
        var query = from list in clientContext.Web.Lists
		            where list.Title == "Expected Shipments"
		            select list;
        IEnumerable<List> matchingLists = clientContext.LoadQuery(query);
        clientContext.ExecuteQuery();

        List expectedShipmentsList = matchingLists.Single();

        EventReceiverDefinitionCreationInformation receiver = new EventReceiverDefinitionCreationInformation();
        receiver.ReceiverName = "ExpectedShipmentsItemUpdated";
        receiver.EventType = EventReceiverType.ItemUpdated;

#if DEBUG
        receiver.ReceiverUrl = WebConfigurationManager.AppSettings["RERdebuggingServiceBusUrl"].ToString();
#else
        receiver.ReceiverUrl = "https://" + request.Headers["Host"] + "/Services/RemoteEventReceiver1.svc"; 
#endif
        expectedShipmentsList.EventReceivers.Add(receiver);
        clientContext.ExecuteQuery();
    }
}
  ```

5. Adicione a seguinte instrução **using** à parte superior do arquivo.
    
  ```cs
  
using System.Web.Configuration;
  ```

6. Para garantir que  `DEBUG` é verdadeiro se e somente se, o add-in está sendo depurado, realizar o subprocedimento a seguir:
    
1. No **Solution Explorer**, clique com botão direito no projeto **ChainStoreWeb** e selecione **Propriedades**.
    
  
2. Abra a guia **Build** das **Propriedades** e selecione **Debug** na lista suspensa **configuração** na parte superior.
    
  
3. Certifique-se de que a caixa de **constante Define DEBUG** está marcada. (Geralmente é por padrão.) A captura de tela a seguir mostra a configuração correta.
    
     ![A subguia Compilar da guia Propriedades no Visual Studio. O menu suspenso Configuração está definido como Depuração. A caixa de seleção "Definir constante DEPURAÇÃO" está marcada.](images/4f81174f-d875-4a9e-bff4-adea0f176f00.PNG)
  

  

  
4. Altere o recebimento de **configuração** para baixo até o **lançamento** e, em seguida, verifique se a caixa **constante Define DEBUG** ** *não* ** está marcada. (Geralmente não é por padrão.) A captura de tela a seguir mostra a configuração correta.
    
     ![A subguia Compilar da guia Propriedades. O menu suspenso Configuração diz Versão de lançameto. A caixa de seleção "Definir constante DEPURAÇÃO" não está marcada.](images/7fd942de-a324-4f70-a750-f5304c993832.PNG)
  

  

  
5. Se você fez alguma alteração, salve e feche a guia **Propriedades**.
    
  
7. Abra o arquivo Web. config e adicione a seguinte marca como um filho do elemento **appSettings**. Recebemos o valor da configuração na próxima seção.
    
  ```XML
  <add key="RERdebuggingServiceBusUrl" value="" />
  ```


## Obtenha a URL do receptor para depuração

O evento add-in e receptores de evento de item de lista são os serviços de serviço WCF (Windows Communication) e sabe seu próprio ponto de extremidade de cada serviço WCF e o armazena em vários locais, incluindo o objeto **System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri**. Quando você estiver depurando, o receptor suplemento está hospedado em um ponto de extremidade de barramento de serviço do Windows Azure é quase o mesmo como o ponto de extremidade para o receptor de item de lista. A diferença é que a URL do ponto de extremidade suplemento termine com "AppEventReceiver.svc", mas a URL de receptores de item de lista termine com "RemoteEventReceiver1.svc". Para obter a URL do ponto de extremidade em que o receptor add-in, faça uma pequena alteração até o final do-lo e usá-lo como o valor da configuração de **RERdebuggingServiceBusUrl** nosso Web. config.
  
    
    

1. Abra o arquivo de AppEventReceiver.svc.cs na pasta de **Serviços** do projeto **ChainStoreWeb**.
    
  
2. Adicione a seguinte como a primeira linha no método **ProcessEvent**.
    
  ```cs
  string debugEndpoint = System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri.ToString(); 
  ```

3. Adicione um ponto de interrupção à linha muito próxima do método.
    
  
4. Pressione F5 para depurar o suplemento. Porque Web. config estiver aberto e Office Developer Tools for Visual Studio altera uma configuração nela toda vez que você pressionar F5, você será solicitado a recarregá-lo. Selecione **Sim**.
    
  
5. Quando o ponto de interrupção é atingido, passe o cursor sobre a variável  `debugEndpoint` . Quando o Visual Studio dica de dados for exibida, clique na seta e selecione o **Visualizador de texto**.
    
     ![Um visualizador de texto do Visual Studio com uma URL do Barramento de Serviço do Azure.](images/494cf01e-3e17-4092-b239-9312ac4ab258.PNG)
  

  

  
6. Copie o valor de cadeia de caracteres do visualizador e colá-lo em algum lugar.
    
  
7. Feche o visualizador e então interromper a depuração no Visual Studio.
    
  
8. Exclua ou comente a linha que você adicionou na segunda etapa deste procedimento e exclua o ponto de interrupção.
    
  
9. Na cadeia de caracteres que você copiou, substitua o "AppEventReceiver.svc" ao final com "RemoteEventReceiver1.svc".
    
  
10. Copie e cole a URL modificada como o valor da chave **RERdebuggingServiceBusUrl** no arquivo Web. config XML.
    
  

> **OBSERVAçãO**
> Copiando manualmente a URL de barramento de serviço e colando (uma versão modificada do)-lo em Web. config não é a única maneira de lidar com a necessidade de uma URL diferente quando estiver depurando um receptor de evento remoto de quando ele está em execução em produção.> Poderíamos programaticamente armazene o valor de **System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri** em algum lugar no SharePoint ou o banco de dados remoto e, em seguida, ter o nosso código de primeira execução lê-lo e atribuí-lo à propriedade `receiver.ReceiverUrl` .> Podemos pôde registrar o receptor de evento de item de lista como parte do manipulador de eventos de instalados add-in. Em seguida, podemos poderia programaticamente ler **System.ServiceModel.OperationContext.Current.Channel.LocalAddress.Uri**, modificá-la e atribuí-lo a  `receiver.ReceiverUrl` sem precisar armazená-lo em qualquer lugar. Essa estratégia exigiria que a lista de **Remessas esperado** também ser criados no manipulador de eventos de instalados add-in, pois ele teria existir antes do manipulador poderia ser registrado com ele. (Além disso, observe que podemos poderia combinar nossos receptor de evento de suplemento e um receptor de evento de item de lista em um único receptor (ou seja, o mesmo. svc e. svc.cs arquivos). Nesse caso, nenhuma modificação da URL é necessária para utilizá-lo como o valor do `receiver.ReceiverUrl`.)
  
    
    


## Execute o add-in e testar o receptor de item de lista


  
    
    

1. Abra a página de **Conteúdo do Site** do site do repositório de Hong Kong *e remover a lista de **Remessas esperado**, se houver algum!* 
    
  
2. Use a tecla F5 para implantar e executar seu suplemento Visual Studio hospeda o aplicativo da web remoto no IIS Express e hospeda o banco de dados SQL em um SQL Express. Ele também faz uma instalação temporária do add-in no seu site do SharePoint de teste e executa imediatamente o add-in. Você será solicitado para conceder permissões para o suplemento antes que ela seja iniciar página é aberta.
    
  
3. Quando a página de início do add-in for aberta, clique no botão de **volta para o Site** no controle de cromo na parte superior.
    
  
4. Na home page de Kong de Hong armazenar, navegue até a página de **Conteúdo do Site** e abrir a lista de **Remessas esperado**.
    
  
5. Criar um item e no formulário novo item. Observe que os campos **recebido** e **adicionada para inventário** não aparecem no formulário.
    
  
6. Depois que o item é criado, reabri-lo para edição. Marque a caixa **recebido** e salvar o item. Isso irá disparar o evento item atualizado. O item será adicionado ao inventário e o valor do **adicionado ao estoque** campo será alterado para **Sim**. (Você pode precisar atualizar a página para ver a alteração **adicionada para inventário**.)
    
  
7. Use o botão Voltar do navegador até correm a página inicial do suplemento do repositório de cadeia e pressione o botão **Mostrar inventário**. O item marcado como **recebido** está agora listado.
    
  
8. Navegue de volta para a lista de **Remessas esperado** e adicione outro item *com exatamente o mesmo nome do produto e o nome do fornecedor*  , mas uma quantidade diferente.
    
  
9. Depois que o item é criado, reabri-lo para edição. Altere o valor **recebido** como **Sim** e salvar o item.
    
  
10. Use o botão Voltar do navegador até correm a página inicial do suplemento do repositório de cadeia e pressione o botão **Mostrar inventário**. Não há ainda apenas a um item para o nome do produto e fornecedor, mas a quantidade agora é o total dos dois itens na lista de **Remessas esperado**.
    
  
11. Para encerrar a sessão de depuração, feche a janela do navegador ou interrompa a depuração no Visual Studio. Sempre que você pressiona F5, o Visual Studio retira a versão anterior do suplemento e instala a última mais recente.
    
  
12. Você vai trabalhar com esse suplemento e com a solução do Visual Studio em outros artigos. Além disso, é uma prática recomendada retirar o suplemento uma última vez, quando deixar de trabalhar com ele por algum tempo. Clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Retirar**.
    
  

## 
<a name="Nextsteps"> </a>

Consulte  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) para saber como publicar o add-in em um site de SharePoint. Ou, vá para o trabalho avançado no desenvolvimento do SharePoint suplemento com esses nós do MSDN:
  
    
    

-  [Suplementos de design para o SharePoint](design-sharepoint-add-ins.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Ferramentas e ambientes para o desenvolvimento de suplementos do SharePoint](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  

