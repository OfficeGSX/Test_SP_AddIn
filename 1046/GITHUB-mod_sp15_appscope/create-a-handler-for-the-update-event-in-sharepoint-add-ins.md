---
title: Criar um manipulador para o evento de atualização no SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# Criar um manipulador para o evento de atualização no SharePoint Add-ins
Criar e usar um manipulador para o evento de atualização de um Suplemento do SharePoint.
## Pré-requisitos para a criação de um manipulador para o evento de atualização de suplemento
<a name="Prerequisites"> </a>

Estar completamente familiarizado com tanto  [Manipulando eventos de suplemento](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) e [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md) e os pré-requisitos e os conceitos fundamentais listados neles.
  
    
    

## Criar um receptor de UpgradedEventEndpoint
<a name="UpgradedEventEndpoint"> </a>


> [!OBSERVAçãO]
> **Sistema de numeração de versão:** Para obter consistência, este tópico pressupõe que os números de versão do suplemento são 1.0.0.0, 2.0.0.0, 3.0.0.0 e assim por diante. No entanto, a lógica e orientação aplica não importa qual é o seu sistema de numeração.
  
    
    

Para a lógica de atualização personalizada, você pode criar um receptor de evento remoto SharePoint desse evento updated de alças o add-in. Você deve estar conservadora usando essa técnica. Usá-lo apenas para a atualização etapas que não podem ser feitas de qualquer outra forma. Além disso, as orientações encontradas na  [Manipulando eventos de suplemento](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) se aplica para o evento updated suplemento tanto quanto o add-in instalado e add-in Desinstalando eventos. Isso inclui:
  
    
    

- O manipulador deve concluir e retornar um cancelar ou um status de continuar com o SharePoint em 30 segundos. Se não aparecer, SharePoint chamará o manipulador novamente, até três mais tempo.
    
  
- Se seu manipulador retorna um status de cancelar, o SharePoint repetirá até três vezes mais.
    
  
- Geralmente, você precisa incluir a reversão e lógica "tiver feito" em seu manipulador.
    
  
Um cenário no qual um manipulador de **UpgradedEventEndpoint** é útil é quando um campo calculado é adicionado a um banco de dados remoto. Vamos supor que uma coluna de cidade é adicionada e seu valor é calculado a partir de uma coluna de CEP já existente. Seu código em um manipulador de **UpgradedEventEndpoint** poderia percorrer os itens existentes no banco de dados e preencha o valor do campo Cidade de nova com base no valor do campo CEP e alguns fonte de dados externa que mapeie CEPs em cidades. A alteração do esquema de banco de dados propriamente dito é feita melhor fora o manipulador **UpgradedEventEndpoint** usando as práticas recomendadas da plataforma do banco de dados.
  
    
    
Para obter mais detalhes sobre como criar um manipulador de evento add-in, consulte  [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md) e [Criar um receptor de evento add-in SharePoint Add-ins](create-an-add-in-event-receiver-in-sharepoint-add-ins.md). O procedimento a seguir pressupõe que você adicionou o item do receptor de evento suplemento ao seu projeto de Suplemento do SharePoint em Visual Studio.
  
    
    

### Para lidar com o suplemento atualizado horário do evento o primeiro


1. Abra o arquivo AppEventReceiver.svc.cs e adicione uma estrutura condicional para o método  [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) que testa se o evento que é invocado o manipulador é o evento updated. Atualizando código vai dentro esta estrutura. Se seu código precisar acessar o SharePoint, você pode usar o modelo de objeto de cliente do SharePoint código gerenciado (CSOM) ou a interface de transferência de estado representacional (REST). Onde a estrutura condicional está localizada no método depende de como estruturar o outro código no método. Normalmente, ele é um par de estruturas condicionais semelhantes que testam a para o add-in instalado e o suplemento uninstalling eventos. Ele pode estar dentro de uma estrutura condicional que testa determinadas propriedades ou sub propriedades do objeto [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) que é passado para [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) para valores de **null** ou outros valores inválidos. Ele também pode ser dentro de um bloco de **try**. O exemplo a seguir é um exemplo da estrutura. O objeto de _properties_ é um objeto [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

  ```

2. Para usar o CSOM no manipulador, adicione um bloco de **using** que obtém um objeto [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) chamando o método **TokenHelper.CreateAppEventClientContext** (dentro do bloco condicional). Especifique **true** para o segundo parâmetro acessar a web add-in. Especifique **false** para acessar a web de host. Se você precisar acessar ambos, você precisará de dois objetos de contexto de cliente diferente.
    
  
3. Se seu manipulador precisar acessar componentes não SharePoint, colocar esse código fora qualquer blocos de contexto do cliente. Seu código deve ser estruturado de forma semelhante ao seguinte:
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, true))
    {
        // CSOM code that accesses the add-in web
    }
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, false))
    {
        // CSOM code that accesses the host web
    }
    // Other update code
}

  ```

4. Para usar a interface REST, seu código usa outros métodos da classe **TokenHelper** para obter um token de acesso, que é incluído nas solicitações feitas no SharePoint. Para obter mais informações, consulte [Realizar operações básicas usando os pontos de extremidade REST do SharePoint 2013](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). Seu código deve ser estruturado de forma semelhante ao seguinte.
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
    if (contextTokenString != null)
    {
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                                                                                                                  Request.Url.Authority);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                                   .AccessToken;

       // REST code that accesses SharePoint
    }  
    // Other update code
}

  ```

5. Para acessar o SharePoint, seu código REST também precisa saber a URL do web host ou suplemento web ou ambos. Esses URLs são sub ambas as propriedades do objeto  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) que é passado para o método [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) . O código a seguir mostra como conectá-los.
    
  ```cs
  
Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
  ```

Quando você atualiza um tempo de suplemento para o segundo (ou terceiro e assim por diante), você pode precisar Certifique-se de que alguns lógica de atualização não é executado várias vezes na mesma instância do suplemento. O procedimento a seguir mostra como.
  
    
    

### Manipular o evento de atualizados suplemento atualizações subseqüentes


1. Abra o arquivo AppEventReceiver.svc.cs e localize o código que implementadas ações update na primeira atualização (de 1.0.0.0. para 2.0.0.0). Vamos chamá-lo o código anterior de atualização. (Este código é geralmente localizado após o código de autorização que obtém os tokens de acesso ou o contexto de cliente.) Agora que você está atualizando novamente (em 2.0.0.0 para 3.0.0.0), normalmente haverá ações no código anterior de atualização aqui que você não deseja executar novamente na mesma instância do suplemento, mas você deseja que ele seja executado em uma instância do suplemento que nunca foi atualizada para 2.0.0.0 (nesse caso, a instância agora está sendo atualizada completamente de 1.0.0.0. para 3.0.0.0).
    
  
2. Quebre o código anterior de atualização em uma estrutura condicional que testa da versão anterior da instância do suplemento e executa apenas se o código da estrutura de não foi executado anteriormente nesta instância do suplemento. A versão anterior da instância é armazenada na subpropriedade  [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) do objeto [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  
3. Adicione nova lógica de atualização (para a atualização do 2.0.0.0 3.0.0.0) abaixo esta estrutura. O exemplo a seguir é um exemplo.
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

  ```

4. Para cada atualização subsequente, repita essas etapas. Para a atualização do 3.0.0.0 4.0.0.0, seu código deve ter a seguinte estrutura.
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 is here.
}

Version ver3OOO = new Version("3.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver3OOO)
{
    // Code to update from 2.0.0.0 to 3.0.0.0 (previous update code) is here.
}
// Code to update from 3.0.0.0 to 4.0.0.0 is here.

  ```


> [!IMPORTANTE]
> Se você adicionar um componente para um suplemento em um manipulador de **UpgradedEventEndpoint**, certifique-se de adicionar o mesmo código para um manipulador de **InstalledEventEndpoint** porque você deseja que esse componente incluído no add-in em uma nova instalação. Além disso, você deve adicionar um [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) (ou revisá-lo) para o suplemento Remover o componente. Na maioria das vezes, tudo o que foi adicionado ou alterados pelo **InstalledEventEndpoint** deve ser revertido ou excluído pelo **UninstallingEventEndpoint**. Uma exceção é que os dados permanecerão útil depois que o suplemento for removido da Lixeira de segundo estágio não devem ser excluídos. (Sites, além da web do suplemento, que são criados pelo suplemento devem ser considerados dados.)
  
    
    


## Adicione a lógica de reversão para o manipulador
<a name="AddRollbackLogic"> </a>

Se ocorrer um erro quando um add-in está sendo atualizado, a infraestrutura de SharePoint 2013 interrompe o processo de atualização e reverte a instância de suplemento e todos os seus componentes para a versão anterior da instância do suplemento. Mas a infraestrutura tem há uma maneira de saber o que significa a lógica do manipulador de **UpgradedEventEndpoint**, para que ele não pode desfazer sempre-lo. Uma exceção sem tratamento gerada enquanto está em execução seu manipulador **UpgradedEventEndpoint** certamente indica que o processo de atualização de suplemento inteiro deve ser revertido, mas ele não será porque a infraestrutura não sabe como desfazer algumas coisas que seu código **UpgradedEventEndpoint** pode ter feito. Por esse motivo, o código de **UpgradedEventEndpoint** deve:
  
    
    

1. Captura todas as exceções.
    
  
2. Filial ao código de reversão personalizado para desfazer o que foi feito até esse ponto.
    
  
3. Sinal para a infra-estrutura de SharePoint 2013 que a atualização de suplemento inteira deve ser revertida.
    
  
4. Passe em uma mensagem de erro para a infra-estrutura.
    
  
Nem tudo seu **UpgradedEventEndpoint** não precisa ser revertida explicitamente dentro do manipulador. Suplemento de web vai ser revertida pela infra-estrutura, então seu código não tem desfazer alterações na Web do suplemento. Mas o manipulador **UpgradedEventEndpoint** geralmente têm que reverter as alterações feitas na web de host ou outros componentes que são externos ao Suplemento do SharePoint.
  
    
    
Sobre a atualização de segunda (ou terceira e assim por diante), sua exceção de lógica de manusear deve levar em conta o fato de que a instância do add-in que está sendo atualizada não é necessariamente a versão imediatamente anterior. Se não for, pode haver dois ou mais blocos de código de atualização que precisam ser revertida. Por esse motivo, você precisa lógica condicional que baseia-se no número da versão anterior. A seguir é um exemplo da lógica de reversão para uma atualização para a versão 4.0.0.0.
  
    
    



```cs

catch (Exception e)
{ 
    // Rollback of the 3.0.0.0 to 4.0.0.0 update logic goes here.

    Version ver3OOO = new Version("3.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver3OOO)
    {
        // Rollback of the 2.0.0.0 to 3.0.0.0 update logic goes here.
    }

    Version ver2OOO = new Version("2.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver2OOO)
    {
        // Rollback of the 1.0.0.0 to 2.0.0.0 update logic goes here.
    }
}
```

Últimas coisas seu tratamento de erros deve fazer é atribuir um status de cancelar e uma mensagem de erro ao objeto  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) que o método **ProcessEvent** retorna para a infra-estrutura de atualização de SharePoint 2013. O exemplo a seguir é um exemplo. Nesse código, _result_ é um objeto de **SPRemoteEventResult** que foi declarado anteriormente no método **ProcessEvent**.
  
    
    



```cs

catch (Exception e)
{     
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}
```


> [!IMPORTANTE]
> Atribuindo **SPRemoteEventServiceStatus.CancelWithError** (ou **SPRemoteEventServiceStatus.CancelNoError**) à propriedade  [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) é crucial. Essa propriedade é o que sinaliza a infraestrutura para reverter a atualização. Mas SharePoint repetirá seu manipulador três vezes antes que ele reverte a atualização.
  
    
    


### Use a estratégia de delegação do manipulador

A estratégia de delegação do manipulador descrito em  [Manipulando eventos de suplemento](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) pode ser usada em um **UpdatedEventHandler** muito. Não só é sua reversão e a lógica de "tiver feito" agrupados e executar no componente remoto, mas sua lógica de controle de versão é muito. As limitações da estratégia de aplicam aqui muito: você geralmente não é possível usá-lo para atualizar mais de um sistema remoto.
  
    
    

## Próximas etapas
<a name="Next"> </a>

Retornar às  [Principais etapas atualizando um suplemento](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)ou ir diretamente para um dos artigos a seguir para saber como atualizar o próximo componente principal da sua Suplemento do SharePoint.
  
    
    

-  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Atualizar os componentes da web de host no SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Atualizar componentes remotos no SharePoint Add-ins](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Elemento UpgradedEventEndpoint (PropertiesDefinition complexType) (Add-in SharePoint manifesto)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)
    
  
-  [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Criar um receptor de evento add-in SharePoint Add-ins](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  

