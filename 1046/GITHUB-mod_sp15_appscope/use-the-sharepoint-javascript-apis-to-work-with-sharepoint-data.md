---
title: Use as APIs do SharePoint JavaScript para trabalhar com dados do SharePoint
ms.prod: SHAREPOINT
ms.assetid: 5c8767c2-317f-4bdb-8f4f-885d06da7feb
---


# Use as APIs do SharePoint JavaScript para trabalhar com dados do SharePoint
Use o modelo de objeto do SharePoint JavaScript para trabalhar com dados do SharePoint de JavaScript em páginas da web add-in.
Esse é o dia 10 em uma série de artigos sobre noções básicas do desenvolvimento hospedado no SharePoint Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Implantar e instalar um hospedado no SharePoint SharePoint Add-in](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Adicionar um tipo de conteúdo personalizado a um suplemento do SharePoint-hostedSharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Adicionar uma Web Part a uma página em um SharePoint hospedado SharePoint Add-in](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar um fluxo de trabalho para um hospedado no SharePoint SharePoint Add-in](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar uma página personalizada e um estilo a um hospedado no SharePoint SharePoint Add-in](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar processamento personalizado de cliente para um hospedado no SharePoint SharePoint Add-in](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Create a custom ribbon button in the host web of a SharePoint Add-in](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md)
    
  

> [!OBSERVAçãO]
> Se você tiver trabalhado através desta série sobre hospedado no SharePoint suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) e abri-lo BeforeJSOM.sln.
  
    
    

Embora hospedado no SharePoint Suplementos do SharePoint não pode ter o código do lado do servidor, ainda pode haver interação de lógica e tempo de execução de negócios com os componentes do SharePoint em um hospedado no SharePoint Suplemento do SharePoint usando JavaScript e a biblioteca de modelo de objeto do cliente de JavaScript do SharePoint. (Que chamaremos de JSOM. Observe o "M" no final. Não confunda isso com JSO **N** [JavaScript Object Notation].) Neste artigo, você usar o modelo de objeto JavaScript para encontrar e remover itens antigos da lista de **Novos funcionários em Seattle**.
## Criar o JavaScript e um botão para chamá-la


1. Verifique se a etapa a seguir do tutorial de primeira nesta série foi concluída:
    
    Abra o arquivo **/Pages/Default.aspx** partindo da raiz do projeto. Entre outras coisas, esse arquivo gerado carrega um destes dois scripts hospedadas no SharePoint ou ambos: sp.runtime.js e sp.js. A marcação para carregar esses arquivos está no controle **Content** próxima à parte superior do arquivo que tem a ID **PlaceHolderAdditionalPageHead**. A marcação varia dependendo da versão do **Microsoft Office Developer Tools for Visual Studio** que você está usando. Esta série de tutoriais exige que os dois arquivos ser carregado e que eles ser carregado com marcas de **<script>** HTML comuns, marcas de **<SharePoint:ScriptLink>** não. Certifique-se de que as seguintes linhas estão no controle **PlaceHolderAdditionalPageHead**,  *logo acima*  a linha `<meta name="WebPartPageExpansion" content="full" />`:
    


  ```
  
<script type="text/javascript" src="/_layouts/15/sp.runtime.js"></script>
<script type="text/javascript" src="/_layouts/15/sp.js"></script> 

  ```


    , Em seguida, procure o arquivo de qualquer outra marcação também carrega um ou outro desses arquivos e remover a marcação redundante. Salve e feche o arquivo.
    
  
2. No nó **Scripts** no **Solution Explorer**, já haja um arquivo Add-in.js. Se não existe, mas não há um App.js, clique com o botão App.js e renomeie-a como Add-in.js. Se não houver um Add-in.js ou o App.js, crie um com estas etapas:
    
1. Com o botão direito no nó **Scripts** e escolha **Adicionar** > **Novo Item** > **Web**.
    
  
2. Escolha **Arquivo JavaScript** e nomeie-oAdd-in.js.
    
  
3. Abra o Add-in.js e exclua o seu conteúdo, se houver alguma.
    
  
4. Adicione as seguintes linhas ao arquivo. Observe o seguinte sobre este código:
    
  - A linha  `'use strict';` garante que o tempo de execução no navegador JavaScript gerará uma exceção se você usar inadvertidamente certas práticas ruim no JavaScript.
    
  
  - A variável  `clientContext` contém um objeto **SP.ClientContext** que referencia o site do SharePoint. Todos os códigos JSOM começa criando ou obter uma referência ao, um objeto desse tipo.
    
  
  - A variável  `employeeList` contém uma referência para a instância de lista de **Novos funcionários em Seattle**.
    
  
  - A variável  `completedItems` retém os itens da lista que o script excluirá: os itens cujo campo **OrientationStage** é definido como **concluído**.
    
  

  ```
  
'use strict';

var clientContext = SP.ClientContext.get_current(); 
var employeeList = clientContext.get_web().get_lists().getByTitle('New Employees In Seattle'); 
var completedItems; 
  ```

5. Para minimizar as mensagens entre o navegador do cliente e servidor do SharePoint, o JSOM usa um sistema de lote. Apenas uma função, **SP.ClientContext.executeQueryAsync**, realmente envia mensagens para o servidor (e recebe respostas). Chamadas para as APIs de JSOM que vêm entre chamadas de **executeQueryAsync** são agrupadas para cima e enviadas ao servidor em um lote que o próximo horário **executeQueryAsync** é chamado. No entanto, não é possível geralmente chamar um método de um objeto JSOM, a menos que o objeto foi colocado ao cliente em uma chamada anterior de **executeQueryAsync**. Seu script será chame o método **SP.ListItem.deleteObject** cada item concluída na lista, para que ele tenha fazer duas chamadas de **executeQueryAsync**; uma para obter uma coleção de itens da lista concluídas e, em seguida, um segundo para as chamadas de **deleteObject** de lote e enviá-las para o servidor para execução.
    
    Portanto, comece criando um método para obter os itens de lista do servidor. Adicione o código a seguir ao arquivo.
    


  ```
  
function purgeCompletedItems() {

   var camlQuery = new SP.CamlQuery(); 
   camlQuery.set_viewXml( 
         '<View><Query><Where><Eq>' + 
           '<FieldRef Name=\\'OrientationStage\\'/><Value Type=\\'Choice\\'>Completed</Value>' + 
         '</Eq></Where></Query></View>'); 
     completedItems = employeeList.getItems(camlQuery); 
}
  ```

6. Quando essas linhas são enviadas ao servidor e executadas lá, eles criam uma coleção de itens de lista, mas o script deve trazer essa coleção para baixo até o cliente. Isso é feito com uma chamada para a função **SP.ClientContext.load**, portanto, adicione a seguinte linha para o método até o final do método.
    
  ```
  
clientContext.load(completedItems);
  ```

7. Adicione uma chamada de **executeQueryAsync**. Esse método tem dois parâmetros, ambos os quais são as funções de retorno de chamada. O primeiro executa se server executa com êxito todos os comandos no lote. O segundo é executado se o servidor falhar por qualquer motivo. Você pode criar essas duas funções nas etapas posteriores. Adicione a seguinte linha no final do método.
    
  ```
  clientContext.executeQueryAsync(deleteCompletedItems, onGetCompletedItemsFail);
  ```

8. Finalmente, adicione a seguinte linha no final do método. Retornando **false** ao botão ASP.NET que chamará a função, podemos cancelar o comportamento padrão de botões ASP.NET, que é para recarregar a página. Um novo carregamento da página causaria um novo carregamento do arquivo Add-in.js. Que, por sua vez, seria reinicializar o objeto `clientContext` . Se isso recarregar concluídas entre o momento em que o **executeQueryAsync** envia sua solicitação e a hora em que o servidor SharePoint envia de volta a resposta, em seguida, o objeto `clientContext` original não está mais na existência para processar a resposta. A função seria travar e não o sucesso nem os retornos de chamada de falha executados. (Comportamento exato pode variar dependendo do navegador).
    
  ```
  return false;
  ```

9. Adicione a seguinte função,  `deleteCompletedItems`, para o arquivo. Esta é a função que será executado se a função  `purgeCompletedItems` for bem-sucedida. Observe o seguinte sobre este código:
    
  - O método **SP.ListItem.get_id** retorna a ID do item da lista. Cada item na matriz é um objeto **SP.ListItem**.
    
  
  - O método **SP.List.getItemById** retorna o objeto de **SP.ListItem** com a ID especificada.
    
  
  - O método **SP.ListItem.deleteObject** marca o item de lista a ser excluído no servidor quando a chamada de **executeQueryAsync** é feita.
    
  
  - Os itens da lista tem que ser copiado da coleção que será enviada para baixo do servidor para uma matriz antes de serem excluídos. Se o script chamado o método **deleteObject** para cada item diretamente no loop **while**, o JavaScript seria acionar um erro reclamando que o comprimento da coleção está sendo alterado enquanto a enumeração está em andamento. A mensagem de erro não é true literalmente, porque o item não é realmente excluído do nada até que as chamadas **deleteObject** são agrupadas e enviadas ao servidor, mas o JSOM foi projetado para imitação que a exceção lança que ocorreria no servidor (onde código não deve alterar o tamanho de um conjunto enquanto a coleção está sendo enumerada). No entanto, matrizes tem um tamanho fixo, então chamar **deleteObject** em um item em uma matriz exclui o item da lista, mas não altera o tamanho da matriz.
    
  

  ```
  function deleteCompletedItems() {

    var itemArray = new Array();
    var listItemEnumerator = completedItems.getEnumerator();

    while (listItemEnumerator.moveNext()) {
        var item = listItemEnumerator.get_current();
        itemArray.push(item);
    }

    var i;
    for (i = 0; i < itemArray.length; i++) {
        employeeList.getItemById(itemArray[i].get_id()).deleteObject();
    }

    clientContext.executeQueryAsync(onDeleteCompletedItemsSuccess, onDeleteCompletedItemsFail);
}
  ```

10. Adicione a seguinte função,  `onDeleteCompletedItemsSuccess`, para o arquivo. Esta é a função que será executado se os itens concluídos com êxito são excluídos (ou não sejam concluídos todos os itens na lista). A segunda linha,  `location.reload(true);`, faz com que a página recarregar a partir do servidor. Isso é uma conveniência porque a Web Part de exibição de lista na página ainda mostra os itens concluídos até que a página for atualizada. (Recarregar o arquivo Add-in.js muito, mas que não causar um problema porque ele não fazê-lo de uma forma que interrompe uma função de JavaScript em andamento.
    
  ```
  
function onDeleteCompletedItemsSuccess() {
    alert('Completed orientations have been deleted.');
    location.reload(true);
}
  ```

11. Adicione as seguintes duas funções de retorno de chamada em caso de falha no arquivo.
    
  ```
  
// Failure callbacks

function onGetCompletedItemsFail(sender, args) {
    alert('Unable to get completed items. Error:' + args.get_message() + '\\n' + args.get_stackTrace());
}

function onDeleteCompletedItemsFail(sender, args) {
    alert('Unable to delete completed items. Error:' + args.get_message() + '\\n' + args.get_stackTrace());
}
  ```

12. Abra o arquivo default. aspx e localize o elemento de **asp:Content** com a ID **PlaceHolderMain**.
    
  
13. Adicione a seguinte marcação entre o elemento **WebPartPages:WebPartZone** e o primeiro dos dois elementos **asp:Hyperlink**. Observe que o valor do manipulador **OnClientClick** é `return purgeCompletedItems()` , em vez de apenas `purgeCompletedItems()`.  `false` que é retornado da função informa ASP.NET não para recarregar a página.
    
  ```HTML
  
<p><asp:Button runat="server" OnClientClick="return purgeCompletedItems()"
  ID="purgecompleteditemsbutton" Text="Purge Completed Items" /></p>
  ```

14. Recrie o projeto no Visual Studio.
    
  
15. Para minimizar a necessidade de definir manualmente o **Estágio de orientação** de itens de lista comoconcluído , ao testar o suplemento, abra o arquivo Elements XML para a instância de lista **NewEmployeesInSeattle** (não o Elements. XML para o modelo de lista **NewEmployeeOrientation** ) e adicione a marcação `<Field Name="OrientationStage">Completed</Field>` como último filho a um ou mais dos elementos **Row**.
    
    O exemplo a seguir é um exemplo de como o elemento **Rows** deve ficar.
    


  ```
  
<Rows>
  <Row>
    <Field Name="Title">Tom Higginbotham</Field>
    <Field Name="Division">Manufacturing</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Satomi Hayakawa</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Cassi Hicks</Field>
  </Row>
  <Row>
    <Field Name="Title">Lertchai Treetawatchaiwong</Field>
  </Row>
</Rows>
  ```


## Executar e testar o suplemento


  
    
    

1. Permitir pop-ups no navegador que Visual Studio usa quando você depurar.
    
  
2. Use a tecla F5 para implantar e executar seu suplemento Visual Studio faz uma instalação temporária do add-in no seu site do SharePoint de teste e o executa imediatamente o add-in.
    
  
3. Abre a home page do add-in e há um ou mais itens na lista com o **Estágio de orientação** em **concluído**.
    
   **Listar antes da limpeza de itens concluídos**

  

     ![A lista "Novos Funcionários em Seattle" com a coluna "Estágio de Orientação" de dois itens definidos como Concluídos. Existe um botão chamado "Limpar Itens Concluídos" abaixo na lista.](images/e5e4eef8-a218-4797-aabc-c52adbd2d96d.PNG)
  

  

  
4. Quando a página inicial do add-in for totalmente carregada, escolha o botão **Limpar itens concluída**. Se a operação tenha êxito (não obtiver qualquer mensagem de falha), em seguida, todos os itens de **Complete** são excluídos e você verá uma caixa de mensagem pop-up dizendo **concluído orientações foram excluídas**.
    
  
5. Feche o popup e a página será recarregado e os itens de **Completed** não estão mais na Web Part de exibição de lista …
    
   **Após a limpeza de concluídas itens de lista**

  

     ![A lista "Novos Funcionários em Seattle" com dois itens a menos do que antes, sem que nenhum tenha o "Estágio de Orientação" definido como Concluído.](images/a0330fad-1473-4fde-9df2-8be0b37df1a1.PNG)
  

  

  
6. Para encerrar a sessão de depuração, feche a janela do navegador ou interrompa a depuração no Visual Studio. Sempre que você pressiona F5, o Visual Studio retira a versão anterior do suplemento e instala a última mais recente.
    
  
7. Você vai trabalhar com esse suplemento e com a solução do Visual Studio em outros artigos. Além disso, é uma prática recomendada retirar o suplemento uma última vez, quando deixar de trabalhar com ele por algum tempo. Clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Retirar**.
    
  

## 
<a name="Nextsteps"> </a>

O próximo artigo desta série, você adicionará JavaScript a uma página da Web suplemento que trabalha com dados do SharePoint na web host:  [Work with host web data from JavaScript in the add-in web](work-with-host-web-data-from-javascript-in-the-add-in-web.md).
  
    
    

