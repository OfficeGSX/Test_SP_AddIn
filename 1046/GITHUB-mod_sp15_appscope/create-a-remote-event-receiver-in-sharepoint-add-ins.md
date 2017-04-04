---
title: Criar um receptor de evento remoto no SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 628c6103-52f9-4d85-9464-4a6862b36640
---


# Criar um receptor de evento remoto no SharePoint Add-ins
Crie um receptor de evento remoto (RER) que manipula a lista e eventos de item de lista em um Suplemento do SharePoint.
## Pré-requisitos
<a name="SP15appevent_prereq"> </a>

É útil se você tiver primeiro uma compreensão do hospedado em provedor Suplementos do SharePoint e para que você tenha desenvolvido poucos que passam pelo menos um baixo além do nível de "Olá mundo". Além disso, você deve estar familiarizado com  [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md).
  
    
    

## Criar um receptor de evento remoto
<a name="MakeRER"> </a>

Este artigo mostra como estender um Suplemento do SharePoint adicionando um receptor de evento remoto (RER) que manipula o evento ItemAdded para uma lista personalizada na web add-in. O RER é registrado com o site de suplemento usando marcação declarativa. RERs são registrados com  *web host*  programaticamente. Para obter um exemplo de código que faz isso, consulte [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers).
  
    
    
Um RER deve ser um serviço web SOAP. O exemplo de contínuo implementa isso como um serviço do Windows Communication Foundation (WCF); mas é possível em princípio para implementar um RER em uma pilha de não-Microsoft.
  
    
    
Para acompanhe este artigo e insira o código sozinho, baixe o exemplo do  [SharePoint-adicionar-em-CSOM-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-CSOM-BasicDataOperations)e, em seguida, abra o exemplo no Visual Studio.
  
    
    

> **OBSERVAçãO**
> Esta amostra usar um arquivo de TokenHelper.cs que seja gerado pelo Office Developer Tools for Visual Studio. A versão atual era quando a amostra foi criada, mas não pode ser a versão mais recente ao ler isso. O exemplo é ainda ótimo para criar seu primeiro RER. Mas, quando você estiver pronto para ir além disso, você deve examinar as amostras listadas na seção próximas etapas abaixo. Eles são mais prováveis de ser mantidos atualizados.
  
    
    


### Para registrar um receptor de evento remoto


1. Abra o projeto Suplemento do SharePoint em Visual Studio.
    
  
2. No **Solution Explorer**, escolha o suplemento do nó do projeto.
    
  
3. Na barra de menus, escolha **Project**, **Adicionar Novo Item**.
    
  
4. No painel de **Modelos instalados**, escolha o **Office / SharePoint** nó.
    
  
5. No painel de **modelos**, escolha o modelo do **Receptor de evento remoto**.
    
  
6. Na caixa **nome**, mantenha o nome padrão (RemoteEventReceiver1) e, em seguida, escolha o botão **Adicionar**.
    
  
7. No **que tipo de receptor de evento você deseja?** lista, escolha **Eventos de Item de lista**.
    
  
8. No **qual item deve ser a origem do evento?** listar, escolha a **Lista personalizada**.
    
    O exemplo contínuo usa uma lista personalizada de genérica. Mas um RER também pode lidar com eventos que ocorrem nas listas padrão do SharePoint, como **anúncios** ou **Contatos**.
    
  
9. Na lista de **lidar com os eventos a seguir**, escolha **um item que está sendo adicionado** e, em seguida, escolha o botão **Concluir**.
    
    Um serviço web é adicionado ao aplicativo web para manipular o evento remoto que você especificou. Um receptor de evento remoto é adicionado para o Suplemento do SharePoint e o evento de item de lista é referenciado no arquivo Elements do receptor que é o próprio contida na web add-in do recurso.
    
  

### Para criar a lista


1. No **Solution Explorer**, selecione o suplemento do nó do projeto.
    
  
2. Na barra de menus, escolha **Project**, **Adicionar Novo Item**.
    
  
3. No painel de **Modelos instalados**, escolha o nó do **Office SharePoint**.
    
  
4. No painel de **modelos**, escolha o modelo de **lista**.
    
  
5. Na caixa **nome**, deixe o nome padrão (Lista1) e escolha o botão **Adicionar**.
    
  
6. Escolha o botão de opção **criar uma instância de lista com base em um modelo de lista existente**, escolha a **Lista personalizada** na lista e, em seguida, escolha o botão **Concluir**.
    
  

### Para adicionar funcionalidades para o receptor de evento remoto


1. Se seu farm do SharePoint de teste não está no mesmo computador que está executando o Visual Studio (ou você estiver usando um inquilino SharePoint Online como seu site do SharePoint de teste), configure o projeto para depuração usando Microsoft Azure barramento de serviço. Para obter mais informações, consulte o  [Depurar e solucionar problemas de um receptor de evento remoto em um SharePoint Add-in](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md).
    
  
2. No arquivo de código para o serviço do receptor de evento remoto (ou seja, RemoteEventReceiver1.svc.cs), substitua o conteúdo com o código a seguir.
    
    Este código executa as seguintes tarefas.
    
  - Obtém o objeto de contexto de um cliente válido.
    
  
  - Se ainda não existir uma lista que possui chamada o **log de eventos**, cria uma para conter os nomes dos eventos remotos que ocorrem.
    
  
  - Adiciona uma entrada à lista para o evento, incluindo um carimbo de data e hora.
    
  

    > **OBSERVAçãO**
      > No momento em que este artigo foi escrito o Office Developer Tools for Visual Studio adicione referências a todos os assemblies necessários quando o destinatário é criado, mas versões mais recentes das ferramentas não podem. Se você receber erros de compilador, basta adicionar as referências ausentes; Por exemplo, você pode precisar adicionar referências a ServiceModel ou System.ComponentModel.DataAnnotations.



  ```cs
  
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Text;
using Microsoft.SharePoint.Client;
using Microsoft.SharePoint.Client.EventReceivers;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.ServiceModel.Channels;


namespace BasicDataOperationsWeb.Services
{
    public class RemoteEventReceiver1 : IRemoteEventService
    {
        public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
        {
            // When a "before" event occurs (such as ItemAdding), call the event 
            // receiver code.
            ListRemoteEventReceiver(properties);
            return new SPRemoteEventResult();
        }

        public void ProcessOneWayEvent(SPRemoteEventProperties properties)
        {
            // When an "after" event occurs (such as ItemAdded), call the event 
            // receiver code.            
        }

        public static void ListRemoteEventReceiver(SPRemoteEventProperties properties)
        {
            string logListTitle = "EventLog";

            // Return if the event is from the EventLog list itself. Otherwise, it may go into
            // an infinite loop.
            if (string.Equals(properties.ItemEventProperties.ListTitle, logListTitle, 
                  StringComparison.OrdinalIgnoreCase))
                return;

            // Get the token from the request header.
            HttpRequestMessageProperty requestProperty = 
                  (HttpRequestMessageProperty)OperationContext
                   .Current.IncomingMessageProperties[HttpRequestMessageProperty.Name];
            string contextTokenString = requestProperty.Headers["X-SP-ContextToken"];

            // If there is a valid token, continue.
            if (contextTokenString != null)
            {
                SharePointContextToken contextToken =
                    TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                         requestProperty.Headers[HttpRequestHeader.Host]);

                Uri sharepointUrl = new Uri(properties.ItemEventProperties.WebUrl);
                string accessToken = TokenHelper.GetAccessToken(contextToken, 
                                                      sharepointUrl.Authority).AccessToken;
                bool exists = false;

                // Retrieve the log list "EventLog" and add the name of the event that occurred
                // to it with a date/time stamp.
                using (ClientContext clientContext = 
                     TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), 
                                                                                                         accessToken))
                {
                    clientContext.Load(clientContext.Web);
                    clientContext.ExecuteQuery();
                    List logList = clientContext.Web.Lists.GetByTitle(logListTitle);

                    try
                    {
                        clientContext.Load(logList);
                        clientContext.ExecuteQuery();
                        exists = true;
                    }

                    catch (Microsoft.SharePoint.Client.ServerUnauthorizedAccessException)
                    {
                        // If the user doesn't have permissions to access the server that's 
                        // running SharePoint, return.
                        return;
                    }

                    catch (Microsoft.SharePoint.Client.ServerException)
                    {
                        // If an error occurs on the server that's running SharePoint, return.
                        exists = false;
                    }

                    // Create a log list called "EventLog" if it doesn't already exist.
                    if (!exists)
                    {
                        ListCreationInformation listInfo = new ListCreationInformation();
                        listInfo.Title = logListTitle;
                        // Create a generic custom list.
                        listInfo.TemplateType = 100;
                        clientContext.Web.Lists.Add(listInfo);
                        clientContext.Web.Context.ExecuteQuery();
                    }

                    // Add the event entry to the EventLog list.
                    string itemTitle = "Event: " + properties.EventType.ToString() + 
                          " occurred on: " + 
                          DateTime.Now.ToString(" yyyy/MM/dd/HH:mm:ss:fffffff");
                    ListCollection lists = clientContext.Web.Lists;
                    List selectedList = lists.GetByTitle(logListTitle);
                    clientContext.Load<ListCollection>(lists);
                    clientContext.Load<List>(selectedList);
                    ListItemCreationInformation listItemCreationInfo = 
                          new ListItemCreationInformation();
                    var listItem = selectedList.AddItem(listItemCreationInfo);
                    listItem["Title"] = itemTitle;
                    listItem.Update();
                    clientContext.ExecuteQuery();
                }
            }
        }
    }
}
  ```

3. Na Home.aspx.cs, altere todas as instâncias de  `SPHostUrl` para `SPAppWebUrl`.
    
    Por exemplo,  `sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);` deve ser alterado para `sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);`.
    
  

## Executar e testar o manipulador de eventos
<a name="RunAndTest"> </a>

Teste seu manipulador com o procedimento a seguir.
  
    
    

1. Pressione a tecla **F5** para executar o projeto.
    
  
2. Confie o suplemento quando for solicitado a fazê-lo.
    
    Sua Suplemento do SharePoint é executado, e um índice de listas disponíveis aparece e inclui **Lista1**.
    
  
3. Escolha a identificação do **Lista1**.
    
    Esse ID é copiado para a caixa de **Recuperar itens de lista**.
    
  
4. Escolha o botão de **Recuperar itens de lista**.
    
    **Lista1** aparece com nenhum item nela.
    
  
5. Na caixa **Adicionar Item**, especifique o Primeiro Iteme escolha o botão **Adicionar Item**.
    
    Um item de lista que nomeado **Primeiro Item** é adicionado à **Lista1**, que faz com que o receptor de evento remoto acionar e adicionar uma entrada à lista de log de eventos.
    
  
6. Escolha o botão **Atualizar lista** para retornar à tabela de listas.
    
    Na tabela, uma nova lista que nomeado **log de eventos** é exibida.
    
  
7. Escolha o valor GUID **ListID** para o **log de eventos** e escolha o botão de **Recuperar itens de lista**.
    
    Um objeto table para o **log de eventos** é exibida com uma entrada para o evento **ItemAdding manipular** que ocorreram quando você adicionou o item ao **Lista1**.
    
  

## Adicionar ou remover os manipuladores de eventos usando o Visual Studio
<a name="Handle"> </a>


1. No **Solution Explorer**, escolha o nó do projeto para o receptor de evento remoto.
    
  
2. No painel **Propriedades**, defina as propriedades para os eventos que você deseja lidar com a **True**.
    
    Por exemplo, se você deseja responder sempre que um usuário adiciona um item de lista, defina o valor da propriedade **ItemAdding lidar com** **True**. Se você não deseja lidar com esse evento, defina o valor dessa propriedade para **False**.
    

   **Figura 1. Eventos remotos do SharePoint no Visual Studio**

  

     ![SharePoint remote events in Visual Studio](images/SP_VS_Properties_Window_RemoteEvents.PNG)
  

  

  
3. Se você adicionou um evento, adicione o código de tratamento de evento para o arquivo de código para o serviço web como você fez com eventos anteriores.
    
    Para lidar com um tipo diferente de evento, adicione outra receptor de evento remoto para o Suplemento do SharePoint. Por exemplo, se um receptor de evento remoto manipular eventos de item de lista, você pode adicionar outro evento de item de lista a ela. Mas você deve adicionar outra receptor de evento remoto se você deseja lidar com eventos de lista.
    
  

## URL e a hospedagem de restrições para receptores de evento remoto de produção
<a name="Handle"> </a>

O receptor de evento remoto pode ser hospedado em nuvem ou em um servidor no local que também não está sendo usado como um servidor do SharePoint. A URL de um receptor de produção, não é possível especificar uma porta específica. Isso significa que você deve usar qualquer porta 443 para HTTPS, que é recomendável, ou a porta 80 para HTTP. Se estiver usando HTTPS e o serviço receptor é hospedado no local, mas o suplemento é em Microsoft SharePoint Online, então o servidor de hospedagem deve ter um certificado confiável publicamente em uma autoridade de certificação. (Um certificado autoassinado funciona somente se o suplemento estiver em um farm do SharePoint no local.)
  
    
    

## Próximas Etapas
<a name="Handle"> </a>

Use os exemplos de código a seguir para aprimorar a compreensão de RERs:
  
    
    

-  [OfficeDev/PnP/Samples/Core.EventReceivers](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.EventReceivers)
    
  
-  [OfficeDev/PnP/Samples/Provisioning.ReR](
https://github.com/OfficeDev/PnP/tree/master/Samples/Provisioning.ReR)
    
  
-  [OfficeDev/PnP/Scenarios/ECM.AutoTagging](https://github.com/OfficeDev/PnP/tree/master/Samples/ECM.AutoTagging)
    
  

## Recursos adicionais
<a name="Additional"> </a>


-  [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Depurar e solucionar problemas de um receptor de evento remoto em um SharePoint Add-in](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md)
    
  
-  [Receptores de eventos remoto perguntas Frequentes](handle-events-in-sharepoint-add-ins.md#RERFAQ)
    
  

