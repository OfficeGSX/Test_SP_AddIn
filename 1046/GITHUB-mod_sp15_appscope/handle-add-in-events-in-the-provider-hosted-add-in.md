---
title: Lidar com eventos de suplemento do add-in
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# Lidar com eventos de suplemento do add-in
Aprenda a personalizar a instalação de um provedor hospedado Suplemento do SharePoint.
Esse é o sétimo em uma série de artigos sobre noções básicas do desenvolvimento hospedado em provedor Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Dar seu suplemento a aparência do SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)
    
  
-  [Incluir um botão personalizado do add-in](include-a-custom-button-in-the-provider-hosted-add-in.md)
    
  
-  [Obtenha uma visão geral do modelo de objeto do SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)
    
  
-  [Adicionar operações de gravação do SharePoint para o suplemento](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)
    
  
-  [Include an add-in part in the provider-hosted add-in](include-an-add-in-part-in-the-provider-hosted-add-in.md)
    
  

> [!OBSERVAçãO]
> Se você tiver trabalhado através desta série sobre hospedado em provedor suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) e abra o arquivo BeforeAdd-inEventHandlers.sln.
  
    
    

Neste artigo, nós personalizará o tratamento de um tipo de evento no SharePoint, chamado de eventos do suplemento. Especificamente, criaremos manipuladores para os suplemento de instalação e desinstalação eventos. Há também lista e eventos de item de lista que podem obter manipulação personalizada. Você aprenderá sobre estes em um artigo posterior nesta série. Todos esses eventos são disparados no SharePoint, mas seu código personalizado que trata cada evento é em seu aplicativo web remoto. Configurar o SharePoint para chamar o manipulador personalizado registrando-URL do manipulador com o evento do SharePoint.
## Duas casas programaticamente implantar componentes do SharePoint

Queremos nosso suplemento cadeia repositório para criar e implantar os **Funcionários locais** e **Remessas esperado** listas automaticamente. Um suplemento pode implantar os componentes do SharePoint, como uma lista personalizada, qualquer momento. Mas, quando um suplemento depende de um componente específico, como uma lista personalizada, em seguida, o componente realmente deverá ser usuários implantados *antes de*  começar a trabalhar com o suplemento. Para esses componentes vitais, existem duas casas onde a lógica de implantação personalizada pode ir:
  
    
    

- Em um manipulador para o evento de instalação do suplemento.
    
  
- Na lógica "da primeira execução" que é executado na primeira vez que é iniciado o add-in no SharePoint.
    
  
Decidir o que é melhor para um determinado suplemento é um tópico avançado. Neste artigo, devemos pode mencionar apenas alguns pontos de comparação:
  
    
    

- Um manipulador de instalação personalizada deve concluir em 30 segundos. Não há nenhum limite a lógica de primeira execução quanto tempo pode levar.
    
  
- Caso algo saia errado durante uma instalação do suplemento, SharePoint reverterá tudo o que aconteceu como parte da instalação. Um manipulador de instalação personalizada é executado  *depois*  do SharePoint tiver feito tudo seu vai fazer para instalar o suplemento, portanto um manipulador personalizado pode participar neste sistema. Por exemplo, se sua lógica personalizada gera uma exceção, você pode dizer ao SharePoint para reverter a instalação do suplemento inteira. Caso algo saia errado na lógica personalizada de primeira execução, no entanto, o suplemento permanece instalado e possivelmente não funcionarão corretamente.
    
  
- SharePoint não dá se ele tiver que reverter uma instalação do suplemento. Ele tentará novamente a instalação imediatamente. Ele faz tentativas de até 4 (30 a segunda limite de tempo aplica-se em cada tentativa.) Cada vez que ela repete, o manipulador de instalação personalizada será executado novamente,  *desde o início*  . Se o manipulador gerenciado para instalar, digamos, uma lista, antes da reversão, ele tentará instalar o a mesma lista novamente a nova tentativa. Para evitar que isso aconteça, o código em um manipulador de instalação tem de ser escrito para que ele não terão qualquer ação (implantar, como um componente), a menos que se primeiro verifica para ver se essa ação já foi feita. Isso torna a lógica de um manipulador de instalação mais complexo que lógica de primeira execução, pois a lógica de primeira execução não repetir (a menos que você código especificamente para fazê-lo). Além disso, para verificar se um componente já tenha sido implantado normalmente exige uma chamada demorada pela internet usando o manipulador remota no SharePoint. E, em seguida, uma segunda chamada é necessário para implantar, na verdade, o componente (se na verdade não para já tenha sido implantado).
    
  
Para o suplemento do repositório de cadeia, podemos combinará dessas estratégias. Neste artigo, você vai criar um manipulador de instalação que irá registrar web host como um locatário no banco de dados corporativo e, em seguida, definir um sinal que especifica se o suplemento tenha sido executado ainda na web host. Em um artigo posterior nesta série, você colocará lógica de primeira execução no método **Page_Load** da página inicial suplementos. Essa lógica irá implantar as duas listas personalizadas e fazer algumas outras coisas muito.
  
    
    

## Configurar a solução para o receptor de evento de depuração
<a name="RERDebug"> </a>

Depuração de receptores de evento requer o uso de barramento de serviço do Windows Azure. Siga as instruções em  [Depurar e solucionar problemas de um receptor de evento remoto em um SharePoint Add-in](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). Desde que você estiver usando um site SharePoint Online como seu site de teste, ser deve-se executar as instruções para um site remoto de teste. O restante desta série assumirá que você configurou com êxito de depuração.
  
    
    

## Criar o manipulador de instalação
<a name="RERDebug"> </a>


  
    
    

> [!OBSERVAçãO]
> As configurações para projetos de inicialização no Visual Studio tendem a reverter para a configuração padrão sempre que a solução for reaberta. Sempre, siga estas etapas imediatamente após reabri-lo a solução de exemplo nesta série de artigos:> Com o botão direito no nó da solução na parte superior do **Gerenciador de soluções** e selecione **definir projetos de inicialização**.> Verifique se que todos os três projetos estão definidos para **Iniciar** na coluna **ação**.
  
    
    


1. No **Solution Explorer**, selecione o projeto de **ChainStore**, de modo que suas propriedades sejam exibidos no painel **Propriedades** do Visual Studio.
    
  
2. Defina o valor da **alça Add-in instalado** como **True**. (Ele pode ainda ser chamado **Lidar com o aplicativo instalado**.) Isso faz duas coisas:
    
  - Uma pasta denominada **Serviços** é criada no projeto **ChainStoreWeb** (não o projeto **ChainStore** ) e dois arquivos são adicionados a ele: um arquivo de AppEventReceiver.svc e seu arquivo code-behind AppEventReceiver.svc.cs. (Os nomes de arquivo começam com a cadeia de caracteres "App", porque suplementos usada para ser chamado "apps". *Não renomear esses arquivos.*  O Office Developer Tools for Visual Studio pressupõem que os arquivos manterão esses nomes. )
    
  
  - A URL do manipulador é registrada no manifesto do suplemento. Nesta parte do manifesto não está visível no designer de manifesto. Para vê-lo, o arquivo AppManifest.xml do mouse em e selecione **Exibir código**. Não há um novo filho do elemento de **Propriedades** é semelhante ao seguinte. Essa marcação informa ao SharePoint para chamar o método **ProcessEvent** desse serviço quando ele tiver terminado de fazer todo o seu próprio trabalho relacionado ao instalar o suplemento. O manipulador personalizado é a última coisa que é executado como parte da instalação. A cadeia de caracteres `~remoteAppUrl` é um espaço reservado para que o Office Developer Tools for Visual Studio substituirá com a URL do host de serviço. Quando você estiver depurando, é uma URL de barramento de serviço do Windows Azure. Quando você cria o pacote de implantação para produção, ele é a URL de produção.
    
  ```XML
  
<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
  ```

3. Abra o arquivo AppEventReceiver.svc.cs.
    
  
4. Você verá que o Office Developer Tools for Visual Studio tenha criado uma implementação de exemplo do método **ProcessEvent**. Todas as implementações deste método começam por inicializar um objeto **SPRemoteEventResult** e eles todos encerrar, retornando desse objeto no SharePoint. Entre outras coisas, esse objeto informa ao SharePoint ou não ele deve reverter o evento porque a lógica de manipulação personalizada falhou. As ferramentas podem também tiver incluído um bloco **using** neste método que cria um objeto **ClientContext**. O manipulador personalizado no suplemento a cadeia de repositório não vai chamada de retorno no SharePoint, portanto, exclua este bloco. O método agora deve se parecer com o seguinte.
    
  ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
  ```

5. O receptor de evento poderia ser tão chamado por qualquer um dos três possíveis suplemento eventos, adicione a seguinte estrutura de **switch** para o método **ProcessEvent** entre as linhas que criam e retornar o objeto `result` . Os nomes de evento têm a cadeia de caracteres "Aplicativo" neles porque suplementos usada para ser chamado "apps".
    
  ```cs
  
switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.         
         
        break;
}
  ```

6. A lógica de instalação vai chamar um procedimento armazenado SQL para registrar o repositório de Hong Kong como um locatário no aplicativo web remoto. É muito importante que, se esse processo falhar, os sinais de manipulador do SharePoint para reverter a instalação do add-in, para adicionar os seguintes blocos **try/catch** no lugar de `TODO2`. Observe o seguinte sobre este código:
    
  - Você criará o método de objeto e  `CreateTenant` `tenantName` em uma etapa posterior.
    
  
  - A propriedade **Status** do objeto **SPRemoteEventResult** pode ter três valores possíveis: **CancelWithError**, **CancelNoError**e **Continue** (padrão). Uma das duas últimas dizer ao SharePoint para reverter o evento.
    
  

  ```cs
  
try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

7. A URL do host da web, que é discriminador de locatário do exemplo, é parte das informações que passa do SharePoint para o receptor no parâmetro **SPRemoteEventProperties**. Adicione a seguinte linha para o método **ProcessEvent** na linha que está logo abaixo a inicialização do objeto **SPRemoteEventResult**.
    
  ```cs
  
string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
  ```

8. Agora nosso código tem que lidar com um pouco capricho da propriedade **AppEventProperties.HostWebFullUrl**. Na maioria dos outros contextos, SharePoint inclui um fechamento caractere "/" ao final da URL de web do host, portanto a lógica do nosso código de exemplo supõe que esse caractere está presente. Mas SharePoint adiciona esse caractere no final do valor **HostWebFullUrl** se e somente se, web host for a web raiz de um conjunto de sites. Como nosso site de Hong Kong é uma subweb do conjunto de sites, precisamos adicionar esse caractere para garantir que a mesma cadeia de caracteres de nome de Inquilinos é usada em todo o exemplo. Adicione o seguinte código abaixo a inicialização do objeto `tenantName` .
    
  ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
  ```

9. Adicione as seguintes instruções **using** à parte superior do arquivo.
    
  ```
  
using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
  ```

10. Adicione o seguinte método à classe  `AppEventReceiver` . Não abordaremos isso detalhadamente porque a finalidade desta série de artigos é ensinar SharePoint Add-in de programação, não programação do SQL Server/Windows Azure.
    
    Esse método vai criar uma linha em uma tabela de banco de dados chamada **inquilinos**. Além da coluna **nome**, a tabela também tem uma coluna de **versão** com um valor padrão definido como0000.0000.0000.0000. Em um artigo posterior nesta série, você irá criar a lógica de primeira execução examina esse valor para determinar se ou não o suplemento já tenha sido instalado na web host. Se a versão for 0000.0000.0000.0000, seu código implantar os **Funcionários locais** e listas de **Remessas esperado** e, em seguida, elevar o número de versão.
    


  ```cs
  
private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
  ```


## Criar o manipulador de desinstalação
<a name="RERDebug"> </a>

É geralmente uma boa prática para manipular o evento uninstalling sempre que estiver manipulando o evento instalado. A ideia básica é que o manipulador uninstalling exclui ou recicla coisas que o manipulador instalado implantado. Existem, no entanto, muitas exceções, de forma você realmente precisa entender os casos de uso do suplemento. Por exemplo, uma lista que é implantada com um suplemento e preenchida com o suplemento ainda pode ter o valor mesmo depois que o próprio suplemento é desinstalado caso em que você não seria deseja desinstalar a lista no manipulador de eventos uninstalling.
  
    
    
O evento de desinstalação não for executado, conforme o esperado, quando um usuário remove o suplemento da página **Conteúdo** do Site. Só isso move o suplemento para a Lixeira do site. Um usuário pode restaurá-lo, mas restaurando não executar novamente o manipulador de par instalado, portanto você gostaria que tudo o que foi implantado com o manipulador de eventos instalados existam ainda se o suplemento for restaurado. Componentes do SharePoint podem ser movidos da Lixeira de segundo estágio Recycle Bin. É somente quando um suplemento é excluído de segundo estágio que o evento uninstalling acontece; e quando um usuário faz isso, o suplemento é unrestorable mesmo assim, por isso, queremos locação de Hong Kong da loja a ser removido do banco de dados corporativo nesse momento.
  
    
    

1. Defina o valor da **desinstalação do suplemento alça** como **True**. (Ele pode ainda ser chamado **Lidar com a desinstalação do aplicativo**.) O manipulador é registrado no arquivo AppManifest.xml assim como você registrou anteriormente o manipulador de instalação. Se você observar o arquivo, você verá que eles tenham exatamente a mesma URL. O Office Developer Tools for Visual Studio pressupõem que você está usando o mesmo arquivo svc. Estamos fazendo que neste exemplo, e é uma prática padrão.
    
  
2. Adicione o código a seguir no lugar de  `TODO3` no arquivo AppEventReceiver.svc.cs. Observe o seguinte sobre este código:
    
  - O método  `DeleteTenant` é adicionado na próxima etapa.
    
  
  - Revertendo a desinstalação do add-in seria deixá-lo no segundo estágio Lixeira, do qual ele ainda pode ser restaurado.
    
  

  ```cs
  
try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
  ```

3. Adicione o seguinte método à classe  `AppEventReceiver` .
    
  ```cs
  
private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();                
    }//dispose conn and cmd
}
  ```


> [!OBSERVAçãO]
> Em um artigo anterior desta série, você configurou o projeto para reconstruir o banco de dados corporativo sempre que você pressionar F5. Isso esvazia a tabela de **inquilinos**.
  
    
    


## Execute o add-in e testar o manipulador de instalação
<a name="RERDebug"> </a>


1. Use a tecla F5 para implantar e executar seu suplemento Visual Studio hospeda o aplicativo da web remoto no IIS Express e hospeda o banco de dados SQL em um SQL Express. Ele também faz uma instalação temporária do add-in no seu site do SharePoint de teste, executa o manipulador de eventos de instalação e executa imediatamente o add-in. Você será solicitado para conceder permissões para o suplemento antes que ela seja iniciar página é aberta.
    
  
2. Quando página de iniciar do add-in é aberto, pressione o ícone de engrenagem no controle chrome no superior e selecione para **configurações de conta**.
    
  
3. Na página **contas**, pressione o botão de **versão Mostrar Add-in**. A versão mostra como 0000.0000.0000.0000.
    
     ![A página Contas com o título "Configurações de conta". Um botão denominado "Mostrar Versão do Suplemento" e abaixo dele há uma linha de texto "Versão registrada: zero zero zero zero ponto zero zero zero zero ponto zero zero zero zero ponto zero zero zero zero".](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)
  

  

  
4. Para encerrar a sessão de depuração, feche a janela do navegador ou interrompa a depuração no Visual Studio. Sempre que você pressiona F5, o Visual Studio retira a versão anterior do suplemento e instala a última mais recente.
    
  
5. Você vai trabalhar com esse suplemento e com a solução do Visual Studio em outros artigos. Além disso, é uma prática recomendada retirar o suplemento uma última vez, quando deixar de trabalhar com ele por algum tempo. Clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Retirar**.
    
  

## 
<a name="Nextsteps"> </a>

O próximo artigo da série, você irá adicionar lógica de primeira execução para o suplemento que programaticamente implantará a lista de **Funcionários Local** e no botão da faixa de opções personalizada: [Adicionar lógica de primeira execução para o suplemento](add-first-run-logic-to-the-provider-hosted-add-in.md)
  
    
    

