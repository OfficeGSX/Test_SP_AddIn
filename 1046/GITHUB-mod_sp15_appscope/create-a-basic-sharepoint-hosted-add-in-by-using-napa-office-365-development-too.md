---
title: Criar um básico hospedado no SharePoint suplemento usando ferramentas de desenvolvimento do Napa Office 365
ms.prod: SHAREPOINT
ms.assetid: 3b47c97b-9e09-47b2-a65f-29b0f44e34bf
---


# Criar um básico hospedado no SharePoint suplemento usando ferramentas de desenvolvimento do Napa Office 365
Saiba como criar um básico hospedado no SharePoint Suplemento do SharePoint usando Ferramentas de Desenvolvimento do Office 365 Napa.
  
    
    
![Run button](images/Apps_NAPA_Run_Button.png)
  
    
    
 [Executar esse exemplo agora!](http://go.microsoft.com/fwlink/?LinkId=313212)
Napa é uma ferramenta que você pode usar para criar hospedado no SharePoint Suplementos do SharePoint. Napa é próprio implementado como uma (hospedado em provedor) Suplemento do SharePoint que pode ser instalado em SharePoint Online sites criados com o modelo de **Site do desenvolvedor**. Sites de desenvolvedor do SharePoint tem uma biblioteca chamada **suplementos no teste** na home page. Instruções para criar um site do desenvolvedor e instalando Napa são mais adiante neste artigo.
  
    
    


> **OBSERVAçãO**
> Nós não suportamos instalando Napa para SharePoint local.
  
    
    


Usando Napa, você pode criar sua Suplementos do SharePoint dentro do seu navegador, em vez de no Visual Studio. A qualquer momento, você pode baixar o seu projeto e abri-lo no Visual Studio para cenários mais avançados.
  
    
    

Seguindo este artigo, você pode aprender como criar um simples hospedado no SharePoint Suplemento do SharePoint usando Napa. O suplemento que você vai criar inclui controles e código para o gerenciamento de listas e itens de lista.
> **OBSERVAçãO**
> Você pode criar apenas hospedado no SharePoint Suplementos do SharePoint com Napa, não hospedado em provedor. Para obter informações sobre as diferenças, consulte  [Suplementos do SharePoint](sharepoint-add-ins.md).> Você não pode usar suplemento atualizando semântica do SharePoint, que é descrita na  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md), em Napa. Isso se você precisar atualizar um suplemento criado no Napa, você precisa primeiro exportá-lo para o Visual Studio. Instruções para fazer isso são mais adiante neste artigo.> Você também pode criar um Suplemento do SharePoint usando Visual Studio. Para obter mais informações, consulte  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
  
    
    


## Opcionalmente, obtenha um Office 365 Developer Site
<a name="Prerequisites"> </a>

Se você ainda não tiver uma assinatura de SharePoint Online que você pode usar para desenvolvimento, use esta seção para obter um. Caso contrário, pule para a  [Install NAPA](#Overview).
  
    
    

> **OBSERVAçãO**
> É possível que você já tenha acesso a um Office 365 Developer Site:> **Você é um assinante do MSDN?** Assinantes do Visual Studio Ultimate e do Visual Studio Premium com MSDN recebem uma Assinatura de Desenvolvedor do Office 365 como benefício. [Resgate seu benefício agora.](https://msdn.microsoft.com/subscriptions/manage/default.aspx)> **Você tem um dos planos de assinatura do Office 365 a seguir?**> **Se sim, um administrador da assinatura do Office 365 pode criar um Site do Desenvolvedor** usando o [centro de administração do Office 365](https://portal.microsoftonline.com/admin/default.aspx). Para saber mais, consulte  [Criar um site do desenvolvedor em uma assinatura existente do Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md).
  
    
    

Duas formas de obter um plano do Office 365.
  
    
    

- Comece com uma  [avaliação gratuita de 30 dias](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK) com uma licença de usuário.
    
  
- Compre uma  [assinatura de desenvolvedor do Office 365](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK).
    
  

> **DICA**
> Cada um desses links será exibido em outra janela ou guia para manter as instruções a seguir acessíveis.
  
    
    


**Figura 1. Nome de domínio do Site de Desenvolvedor do Office 365**

  
    
    

  
    
    
![Página 2 do formulário de Inscrição para conta do Office 365](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. A primeira página (não mostrada) do formulário de inscrição é auto-explicativa. Basta fornecer as informações solicitadas e escolher **Avançar**.
    
  
2. Na segunda página, mostrada na Figura 1, especifique uma ID de usuário para o administrador da assinatura.
    
  
3. Crie um subdomínio do **.onmicrosoft.com**.
    
    Depois de se inscrever, você deve usar as credenciais resultantes (no formato  _IDdeUsuário_@ _seudomínio_.onmicrosoft.com) para entrar no site de portal do Office 365 em que administra a sua conta. Seu Site de Desenvolvedor do SharePoint Online é provisionado no seu novo domínio: **http:// _seudomínio_.sharepoint.com**.
    
  
4. Escolha **Avançar** e preencha a última página do formulário. Se você optar por fornecer um número de telefone para obter um código de confirmação, é possível fornecer um número de celular ou de telefone fixo, mas *não*  um número VoIP (Voice over Internet Protocol).
    
  

    
> **OBSERVAçãO**
> Se você estiver conectado a outra conta da Microsoft quando tentar se inscrever em uma conta de desenvolvedor, é possível que receba esta mensagem: "A ID de usuário que você inseriu não funcionou. Aparentemente, ela não é válida. Verifique se você inseriu a ID de usuário atribuída pela sua organização. Sua ID de usuário, geralmente, é similar a  *nome@exemplo.com*  ou *nome@exemplo.onmicrosoft.com*  ."> Se você receber essa mensagem, faça logoff da conta da Microsoft que estava usando e tente novamente. Se continuar recebendo a mensagem, limpe o cache do navegador ou alterne para a **Navegação InPrivate** e, em seguida, preencha o formulário.
  
    
    

Depois de concluir o processo de inscrição, o navegador abre a página de instalação do Office 365. Escolha o ícone de Administrador para abrir a página do centro de administração.
  
    
    

**Figura 2. Página do centro de administração do Office 365**

  
    
    

  
    
    
![Captura de tela que mostra o centro de administração do Office 365.](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. Aguarde até que o seu Site do Desenvolvedor conclua o provisionamento. Depois que o provisionamento for concluído, atualize a página do centro de administração do no seu navegador.
    
  
2. Em seguida, escolha o link **Criar suplementos** no canto superior esquerdo da página para abrir o Site do Desenvolvedor. Você deve visualizar um site semelhante ao mostrado na Figura 3. Há uma lista de **Suplementos em teste** na página. Isso confirma que o site foi criado com um modelo do Site do Desenvolvedor do SharePoint. Caso veja um site de equipe normal, aguarde alguns minutos e inicie seu site novamente.
    
  
3. Anote a URL do site. Ela é usada quando você cria Suplementos do SharePoint projetos no Visual Studio.
    
  

**Figura 3. Home page do seu Site do Desenvolvedor com a lista Suplementos em teste**

  
    
    

  
    
    
![Captura de tela que mostra a home page do site do Desenvolvedor.](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## Install NAPA
<a name="Overview"> </a>

Se sua assinatura não foi originalmente criada como um Office 365 Developer Site, você tem que criar um Site de desenvolvedor na interface de usuário de administração da assinatura e, em seguida, instalar Napa nela. Instruções para criar o site estão em  [Criar um site do desenvolvedor em uma assinatura existente do Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md).
  
    
    
Para instalar o Napa, abra o seu Site do desenvolvedor e escolha **Conteúdo do Site** > **Adicionar um suplemento** > **Repositório do SharePoint**. No repositório, procure Napa e instalá-lo. (Se você tiver um Office 365 Developer Site, Napa pode ter já foi instalado quando o site foi criado e você verá na página **Conteúdo do Site**.)
  
    
    

## Criar um projeto de Suplemento do SharePoint
<a name="Create"> </a>


1. Abra o suplemento Napa na página Office 365.
    
  
2. Escolha os blocos de **Adicionar um novo projeto** e escolha a **suplemento para o SharePoint** lado a lado.
    
  
3. Nome do projeto de suplemento do teste do SharePointe, em seguida, clique no botão **criar**.
    
    O editor de código é aberto e mostra a página da Web padrão, que já contém código de exemplo que pode ser executado sem que mais nada seja necessário.
    
  

## Adicionar controles a home page
<a name="AddControls1"> </a>

Adicione controles a Suplemento do SharePoint, para a home page padrão para a criação e a exclusão de uma lista do SharePoint genérica e Obtendo o número atual de listas na web do Suplemento do SharePoint. Você adicionará o código para os controles mais tarde.
  
    
    

### Para adicionar controles à home page


1. No lado esquerdo da página sob a pasta de **páginas**, escolha a página **Default. aspx**, se ainda não estiver selecionada.
    
    A página da Web Default. aspx aparece no editor de código.
    
  
2. Na seção  `PlaceHolderMain` , adicione este código em HTML existente
    
  ```HTML
  
<br />
<div>
    <button id="getListCount">Get count of lists in web</button>
</div>
<br />
<div id="starter">
    <input type="text" value="List name here" id="createlistbox"/><button id="createlistbutton">Create List</button>
    <p>
    Lists
    <br />
    <select id="selectlistbox" ></select><button id="deletelistbutton">Delete Selected List</button>
    </p>
</div>
  ```


    O HTML cria esses controles.
    
  - Um botão que obtém o número de listas na web do Suplemento do SharePoint.
    
  
  - Um botão para a criação de uma lista do SharePoint genérica e outro botão para excluir a lista.
    
  
  - Uma lista de listas que estão disponíveis dentro do add-in.
    
  

## Adicionar código para criar e excluir listas
<a name="AddCode1"> </a>

Neste procedimento, você adicionará um código JavaScript para que os usuários podem criar e excluir listas no Suplemento do SharePoint.
  
    
    

### Para adicionar código para criar e excluir listas


1. Escolha a pasta **Scripts** e escolha o link **App.js**.
    
    O arquivo de código JavaScript padrão do modelo de projeto abre para edição. Esse arquivo contém o código que é usado em seu Suplemento do SharePoint. Você pode adicionar outro arquivo. js e adicione o código a ele, em vez de ao arquivo existente. Mas, neste exemplo, adicioná-lo ao arquivo **App.js** fornecida.
    
    Na próxima etapa, você vai definem as funções para os controles que você criou no procedimento anterior.
    

|**Nome da função**|**Descrição**|
|:-----|:-----|
| `getWebProperties()` <br/> |Conectado ao controle **getListCount**  recupera o número de listas na web. <br/> |
| `createlist()` <br/> |Conectado ao controle **createListButton**  cria uma lista do SharePoint genérica. <br/> |
| `deletelist()` <br/> |Conectado ao controle **deletelistbutton**  exclui a lista que o usuário escolhe da lista de listas disponíveis. <br/> |
   

    Você também vai chamar as funções  `welcome()` e `displayLists()` , que este passo a passo descreverá posteriormente.
    
  
2. No arquivo **App.js**, adicione o  `web`,  `lists`e variáveis de  `listItemcollection` nas variáveis de dois padrão e altere o código na função `$(document).ready()` ao exemplo a seguir.
    
    > **OBSERVAçãO**
      > São sublinhadas de erro aparecerá nesse código. Elas vai desaparecem nas etapas posteriores.

  ```
  
'use strict';

var context = SP.ClientContext.get_current();
var user = context.get_web().get_currentUser();
var web = context.get_web();
var lists = web.get_lists();
var listItemCollection;  // This variable is used later when you add list items.

(function () {

// This code runs when the DOM is ready and creates a context object which is 
// needed to use the SharePoint object model.
$(document).ready(function () {
    getUserName();
    $("#getListCount").click(function (event) {
        getWebProperties();
        event.preventDefault();
    });

    $("#createlistbutton").click(function (event) {
        createlist();
        event.preventDefault();
    });

    $("#deletelistbutton").click(function (event) {
        deletelist();
        event.preventDefault();
    });
        displayLists();
    });

  ```


    A próxima etapa, você adicionará funções JavaScript para as definições. Cada função no código for executada pela chamada  `executeQueryAsync()`, que executa o atual solicitação pendente assincronamente no servidor usando o modelo de objeto do cliente (CSOM) para SharePoint. Quando uma função é executado de maneira assíncrona, o script continuará a ser executado sem esperar que o servidor responda. Cada chamada  `executeQueryAsync()` inclui dois manipuladores de eventos. Um manipulador responde se a função for executado com êxito e o outro manipulador responde se a função falhará. Esta tabela descreve as principais funções.
    

|**Nome da função**|**Descrição**|
|:-----|:-----|
| `welcome()` <br/> |Obtém a referência de contexto da web atual e, em seguida, usa para definir as informações do usuário atual para o contexto. <br/> |
| `getWebProperties()` <br/> |Obtém a coleção das listas na web atual e retorna o número de listas. <br/> |
| `displaylists()` <br/> |Obtém a coleção atual das listas nesta Web. Se tiver êxito, essa função adiciona o nome de cada lista na coleção à lista de listas disponíveis. <br/> |
| `createlist()` <br/> |Cria uma lista do SharePoint genérica (lista modelo tipo **genericList**) e lhe atribui o nome que o usuário especifica no controle **createlistbox**. Você pode criar outros tipos de listas. Para obter mais informações sobre os tipos de lista, consulte a  [Enumeração SPListTemplateType](http://go.microsoft.com/fwlink/?LinkId=256687). <br/> |
| `deletelist()` <br/> |Exclui a lista que o usuário escolhe da lista de listas disponíveis. <br/> |
   
3. Adicione o código a seguir após a função de  `onGetUserNameFail()` em **App.js**.
    
  ```
  
function getWebProperties() {
        // Get the number of lists in the current web.
        context.load(lists);
        context.executeQueryAsync(onWebPropsSuccess, onWebPropsFail);
    }

    function onWebPropsSuccess(sender, args) {
        alert('Number of lists in web: ' + lists.get_count());
    }

    function onWebPropsFail(sender, args) {
        alert('Failed to get list. Error: ' + args.get_message());
    }

    function displayLists() {
        // Get the available SharePoint lists, and then set them into 
        // the context.
        lists = web.get_lists();
        context.load(lists);
        context.executeQueryAsync(onGetListsSuccess, onGetListsFail);
    }

    function onGetListsSuccess(sender, args) {
        // Success getting the lists. Set references to the list 
        // elements and the list of available lists.
        var listEnumerator = lists.getEnumerator();
        var selectListBox = document.getElementById("selectlistbox");
        if (selectListBox.hasChildNodes()) {
            while (selectListBox.childNodes.length >= 1) {
                selectListBox.removeChild(selectListBox.firstChild);
            }
        }
        // Traverse the elements of the collection, and load the name of    
        // each list into the dropdown list box.
        while (listEnumerator.moveNext()) {
            var selectOption = document.createElement("option");
            selectOption.value = listEnumerator.get_current().get_title();
            selectOption.innerHTML = listEnumerator.get_current().get_title();
            selectListBox.appendChild(selectOption);
        }
    }

    function onGetListsFail(sender, args) {
        // Lists couldn't be loaded - display error.
        alert('Failed to get list. Error: ' + args.get_message());
    }

function createlist() {
        // Create a generic SharePoint list with the name that the user specifies.
        var listCreationInfo = new SP.ListCreationInformation();
        var listTitle = document.getElementById("createlistbox").value;
        listCreationInfo.set_title(listTitle);
        listCreationInfo.set_templateType(SP.ListTemplateType.genericList);
        lists = web.get_lists();
        var newList = lists.add(listCreationInfo);
        context.load(newList);
        context.executeQueryAsync(onListCreationSuccess, onListCreationFail);
    }

    function onListCreationSuccess() {
        displayLists();
    }

    function onListCreationFail(sender, args) {
        alert('Failed to create the list. ' + args.get_message());
    }

    function deletelist() {
        // Delete the list that the user specifies.
        var selectListBox = document.getElementById("selectlistbox");
        var selectedListTitle = selectListBox.value;
        var selectedList = web.get_lists().getByTitle(selectedListTitle);
        selectedList.deleteObject();
        context.executeQueryAsync(onDeleteListSuccess, onDeleteListFail);
    }

    function onDeleteListSuccess() {
        displayLists();
    }

    function onDeleteListFail(sender, args) {
        alert('Failed to delete the list. ' + args.get_message());
    }
  ```


## Executá-lo!
<a name="Run1"> </a>

A primeira parte da interface do usuário e código é in-loco, portanto, prossiga e execute o add-in para verificar se ele está funcionando.
  
    
    

### Para executar o suplemento


1. Na parte inferior da página, escolha o botão de execução (
  
    
    
![Run button](images/Apps_NAPA_Run_Button.png)
  
    
    
).
    
    O suplemento é empacotado, implantado e instalado no seu Site do desenvolvedor do Office 365.
    
    Após a instalação, o Suplemento do SharePoint inicia. Se o suplemento não for iniciado automaticamente porque, por exemplo, um bloqueador de pop-up estiver habilitado, escolha o link do suplemento para iniciar o add-in.
    
  
2. Escolha o link **clique aqui para iniciar o add-in em uma nova janela**.
    
    A tela para o Suplemento do SharePoint aparece.
    
  
3. Escolha o botão **obter contagem de listas na web**.
    
    Uma caixa de diálogo Especifica que na web para o atual Suplemento do SharePoint contém duas listas. (Web contém a Galeria de Design e listas de galeria de páginas mestras por padrão).
    
  
4. Na caixa **nome da lista aqui**, insira a Lista de testee escolha o botão **Criar lista**.
    
  
5. Abra a lista de **listas** para verificar se a nova lista aparece nela.
    
  
6. Escolha o botão **obter contagem de listas na web** novamente.
    
    Web agora contém três listas, incluindo a lista que você acabou de criar.
    
  
7. Na lista **lista**, escolha a **Lista de teste** e escolha o botão **Excluir lista selecionada**.
    
    **Lista de teste** desaparecerá da lista de listas disponíveis.
    
  
8. Quando terminar, feche a janela do navegador e escolha o botão **Fechar** na janela **do suplemento do lançamento** para voltar para o projeto que você estava editando.
    
  

## Adicione código e controles para adicionar e excluir itens de lista
<a name="AddControls2"> </a>

Agora que os usuários podem criar e excluir listas, você pode executar as seguintes etapas para habilitá-los adicionar e excluir itens de lista.
  
    
    

### Para adicionar código e controles para adicionar e excluir itens de lista


1. Escolha o arquivo default. aspx para editá-lo.
    
  
2. Sob o elemento  `selectlistbox` , adicione este código.
    
  ```XML
  
<p>
    Items
    <br />
    <input type="text" value="item name here" id="createitembox"/><button id="createitembutton">Create Item</button>
    </p>
    <p>
    <select id="selectitembox"></select> <button id="deleteitembutton">Delete Selected Item</button>
    </p>
  ```


    Este código adiciona uma caixa de entrada onde os usuários podem especificar o nome de um item, um botão para adicionar o item à lista e um botão para excluir o item da lista.
    
  
3. Escolha o arquivo **App.js** editá-lo.
    
  
4. Na função  `$(document).ready()` , adicione as definições para funções que são chamados quando o usuário escolhe os botões de **Item de criar** e **Excluir Item selecionado**. Além disso, adicione um manipulador de eventos jQuery para a caixa de listagem **lista** garantir que os itens da lista obtém atualizados quando você seleciona uma nova lista.
    
  ```
  
$("#createitembutton").click(function (event) {
            createitem();
            event.preventDefault();
        });

        $("#deleteitembutton").click(function (event) {
            deleteitem();
            event.preventDefault();
        });
    
        // Update the list items dropdown when a new list
        // is selected in the Lists dropdown.
        $("#selectlistbox").change(function (event) {
            getitems();
            event.preventDefault();
        });
  ```


    > **OBSERVAçãO**
      > Se os itens da lista não estão sendo exibidos quando você executa o suplemento, certifique-se de que a instrução  `displayLists();` vem após o código anterior.

    A próxima etapa, você adicionará funções JavaScript para as novas definições e uma função de suporte ( `getItems()`). Esta tabela descreve o que fazem as principais funções.
    

|**Nome da função**|**Descrição**|
|:-----|:-----|
| `createItem()` <br/> |Adiciona um item à lista que o usuário escolhe e dá esse item o nome que o usuário especifica na caixa **itens**. <br/> |
| `deleteItem()` <br/> |Exclui o item que o usuário escolhe da lista. <br/> |
| `getItems()` <br/> |Recupera a coleção de itens (e seus filhos) na lista que o usuário escolhe. <br/> |
   
5. Adicione este código à parte inferior da **App.js**, após a função  `onDeleteListFail()` .
    
  ```
  
function createitem() {
    // Retrieve the list that the user chose, and add an item to it.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedListTitle = selectListBox.value;
    var selectedList = web.get_lists().getByTitle(selectedListTitle);

    var listItemCreationInfo = new SP.ListItemCreationInformation();
    var newItem = selectedList.addItem(listItemCreationInfo);
    var listItemTitle = document.getElementById("createitembox").value;
    newItem.set_item('Title', listItemTitle);
    newItem.update();
    context.load(newItem);
    context.executeQueryAsync(onItemCreationSuccess, onItemCreationFail);
}

function onItemCreationSuccess() {
    // Refresh the list of items.
    getitems();
}

function onItemCreationFail(sender, args) {
    // The item couldn't be created - display an error message.
    alert('Failed to create the item. ' + args.get_message());
}

function deleteitem() {
    // Delete the item that the user chose.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedListTitle = selectListBox.value;
    var selectedList = web.get_lists().getByTitle(selectedListTitle);
    var selectItemBox = document.getElementById("selectitembox");
    var selectedItemID = selectItemBox.value;
    var selectedItem = selectedList.getItemById(selectedItemID);
    selectedItem.deleteObject();
    selectedList.update();
    context.load(selectedList);
    context.executeQueryAsync(onDeleteItemSuccess, onDeleteItemFail);
}

function onDeleteItemSuccess() {
    // Refresh the list of items.
    getitems();
}

function onDeleteItemFail(sender, args) {
    // The item couldn't be deleted - display an error message.
    alert('Failed to delete the item. ' + args.get_message());
}

function getitems() {
    // Using a CAML query, get the items in the list that the user chose, and 
    // set the context to the collection of list items.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedList = selectListBox.value;
    var selectedListTitle = web.get_lists().getByTitle(selectedList);  
    var camlQuery = new SP.CamlQuery();
    camlQuery.set_viewXml("<View><ViewFields>" +
        "<FieldRef Name='ID' />" +
        "<FieldRef Name='Title' />" +
        "</ViewFields></View>')");
    listItemCollection = selectedListTitle.getItems(camlQuery);
    context.load(listItemCollection, "Include(Title, ID)");
    context.executeQueryAsync(onGetItemsSuccess, onGetItemsFail);
}

function onGetItemsSuccess(sender, args) {
    // The list items were retrieved.
    // Show all child nodes.
    var listItemEnumerator = listItemCollection.getEnumerator();
    var selectItemBox = document.getElementById("selectitembox");
    if (selectItemBox.hasChildNodes()) {
        while (selectItemBox.childNodes.length >= 1) {
     selectItemBox.removeChild(selectItemBox.firstChild);
        }
    }
        while (listItemEnumerator.moveNext()) {
            var selectOption = document.createElement("option");
            selectOption.value = listItemEnumerator.get_current().get_item('ID');
            selectOption.innerHTML = listItemEnumerator.get_current().get_item('Title');
            selectItemBox.appendChild(selectOption);
        }
}

function onGetItemsFail(sender, args) {
    // The list items couldn't be retrieved - display an error message.
    alert('Failed to get items. Error: ' + args.get_message());
}
  ```


## Execute o revisado Suplemento do SharePoint !
<a name="Run2"> </a>

Tudo o que a interface do usuário e o código é in-loco, portanto prossiga e execute o add-in para certificar-se de que ele funciona.
  
    
    

### Para executar o revisado Suplemento do SharePoint


1. Na parte inferior da página, escolha o botão **Executar** novamente.
    
  
2. Na caixa **nome da lista aqui**, insira a Nova lista de testee escolha o botão **Criar lista**.
    
    A nova lista é adicionada à lista de **lista**.
    
  
3. Na lista **lista**, escolha **Nova lista de teste**.
    
  
4. Na caixa **nome do Item aqui**, insira o Item 1e escolha o botão **Criar Item**.
    
    O novo item de lista aparece na lista de **itens**.
    
  
5. Repita a etapa anterior para adicionar o Item 2 e3 do Item.
    
  
6. Na lista de itens, escolha **Item 2** e escolha o botão **Excluir Item selecionado**.
    
    **Item 2** desaparecerá da lista de itens.
    
  
7. Quando terminar, feche a janela do navegador.
    
  

## Exportar o projeto para o Visual Studio
<a name="NextSteps"> </a>

Abra o projeto no Visual Studio, escolhendo o botão **Abrir no Visual Studio**, conforme mostrado na Figura 3. Napa automaticamente instala as ferramentas necessárias e abre o seu projeto no Visual Studio.
  
    
    

**Figura 3. Abrir no botão do Visual Studio**

  
    
    

  
    
    
![Open in Visual Studio button](images/Apps_Napa_OpenInVS.png)
  
    
    

  
    
    

  
    
    

## Recursos adicionais
<a name="Additional"> </a>


-  [Visão geral do desenvolvimento do SharePoint 2013](http://msdn.microsoft.com/library/f86e2695-4d7a-4fc5-bc23-689de96c4b06%28Office.15%29.aspx)
    
  
-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  

