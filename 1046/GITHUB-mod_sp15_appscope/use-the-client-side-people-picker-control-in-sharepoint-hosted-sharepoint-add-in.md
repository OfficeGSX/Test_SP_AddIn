---
title: Use o controle People Picker de cliente no hospedado no SharePoint SharePoint suplementos
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# Use o controle People Picker de cliente no hospedado no SharePoint SharePoint suplementos
Aprenda a usar o controle de selecionador de pessoas do lado do cliente em SharePoint-hospedado Suplementos do SharePoint.
> **IMPORTANTE**
> Este tópico pressupõe que você saiba como criar um hospedado no SharePoint Suplemento do SharePoint. Para saber como criar um, inicie em  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md).
  
    
    


## O que é o controle People Picker de cliente no SharePoint 2013 ?
<a name="bk_whatIs"> </a>

O controle People Picker de cliente permite que os usuários rapidamente procurar e selecione contas de usuário válidas para pessoas, grupos e declarações em suas organizações. O seletor é um controle HTML e JavaScript que fornece suporte a vários navegadores. Adicionar o seletor para seu suplemento é fácil: na sua marcação, adicione um elemento de contêiner para o controle e referências para o controle e suas dependências. Em seguida, em seu script, chame a função de global de **SPClientPeoplePicker_InitStandaloneControlWrapper** para renderizar e inicializar o seletor.
  
    
    
O seletor é representado pelo objeto **SPClientPeoplePicker**, que fornece os métodos que outros controles do lado do cliente podem usar para obter informações do selecionador de ou para executar outras operações. Você pode usar as propriedades de **SPClientPeoplePicker** para configurar o seletor com configurações de controle específico, permitindo que vários usuários ou especificar opções de cache. O seletor de também usa definições de configuração de aplicativo da web como parâmetros Active Directory Domain Services ou florestas desejadas. Definições de configuração do aplicativo Web são buscadas automaticamente (da propriedade **SPWebApplication.PeoplePickerSettings** ).
  
    
    
O seletor de tem os seguintes componentes:
  
    
    

- Uma caixa de texto de entrada para inserir nomes de usuários, grupos e declarações.
    
  
- Um controle de intervalo que mostra os nomes dos usuários resolvidos, grupos e declarações.
    
  
- Um elemento de ocultas **div** que autofills uma lista suspensa caixa com correspondência de resultados da consulta.
    
  
- Um controle de AutoPreenchimento.
    
  

> **OBSERVAçãO**
> O seletor e sua funcionalidade são definidos nos arquivos de script **clientforms.js**, **clientpeoplepicker.js**e **autofill.js**, que estão localizados na pasta %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\ TEMPLATE\\LAYOUTS no servidor.
  
    
    


## Pré-requisitos para configurar seu ambiente de desenvolvimento para usar o controle People Picker de cliente em uma Suplemento do SharePoint 2013
<a name="bk_prereqs"> </a>

Este artigo pressupõe que você crie o Suplemento do SharePoint usando Napa em um Office 365 Site do desenvolvedor. Se você estiver usando esse ambiente de desenvolvimento, já cumprir os pré-requisitos.
  
    
    

> **OBSERVAçãO**
> Vá para  [Configurar um ambiente de desenvolvimento para suplementos do SharePoint no Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) para descobrir como Inscreva-se para um Site do desenvolvedor e começar a usar o Napa.
  
    
    

Se você não estiver usando Napa em um Site do desenvolvedor, será necessário o seguinte:
  
    
    

- SharePoint 2013 com o usuário de pelo menos um destino
    
  
- Visual Studio 2012 ou Visual Studio 2013
    
  
- Office Developer Tools para Visual Studio 2013
    
  

> **OBSERVAçãO**
> Para obter orientação sobre como configurar um ambiente de desenvolvimento que atenda às suas necessidades, consulte  [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx).
  
    
    

As etapas a seguir descrevem as etapas de alto nível para adicionar o seletor para o add-in e, em seguida, obtendo informações do seu usuário de outro controle do lado do cliente. Consulte  [exemplo de código: usando o seletor de pessoas do lado do cliente](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example) para o código correspondente.
  
    
    
Você pode usar o controle People Picker de cliente no SharePoint-hospedado Suplementos do SharePoint, mas não em hospedado em provedor suplementos. Para obter um exemplo que mostra como implementar um controle seletor de pessoas em um suplemento hospedado em provedor, baixe o  [Amostras do modelo de suplemento de Office](http://officeams.codeplex.com).
  
    
    

## Usar o controle People Picker de cliente em uma SharePoint-hospedado suplemento
<a name="bk_steps"> </a>


### Na sua marcação de página


1. Adicione referências a dependências de script do controle seletor de pessoas do cliente.
    
  
2. Adicione as marcas HTML para a página de interface do usuário. O suplemento neste exemplo define dois elementos **div**: um para o selecionador de renderização em e outro para a interface do usuário: um botão que invoca o script para consultar o seletor e os elementos que exibem informações do usuário.
    
  

### Em seu script


1. Crie um dicionário JSON a ser usado como um esquema que armazena as propriedades do seletor específicas, como **AllowMultipleValues** e **MaximumEntitySuggestions**.
    
  
2. Chame a função de **SPClientPeoplePicker_InitStandaloneControlWrapper** global.
    
  
3. Obtenha o objeto picker da página.
    
  
4. O seletor de consulta. O suplemento neste exemplo chama o método **GetAllUserInfo** para obter todas as informações de usuário para os usuários resolvidos e o método **GetAllUserKeys** apenas Obtenha as chaves para os usuários resolvidos.
    
  
5. Obter a identificação de usuário usando o modelo de objeto JavaScript. A ID de usuário não está incluída nas informações do que são retornadas pela selecionador de, para que o suplemento chama o método **SP.Web.ensureUser** e obtém a identificação do objeto retornado **SP.User**.
    
  
Renderização, inicializando e outras funcionalidades do selecionador de são manipuladas pelo servidor, incluindo a pesquisa e solução de entrada do usuário contra os provedores de autenticação SharePoint.
  
    
    

## Exemplo de código: usando o seletor de pessoas do lado do cliente em uma SharePoint-hospedado suplemento
<a name="bk_example"> </a>

Os seguintes exemplos de código HTML e JavaScript adicionar um controle de selecionador de pessoas do lado do cliente a um SharePoint-hospedado suplemento.
  
    
    
O primeiro exemplo mostra a marcação de página para o **PlaceHolderMain** **asp:Content** marcas na página Default. aspx. Este código dependências de script do seletor de referência, oferece uma ID exclusiva para o elemento DOM onde o seletor será renderizado e define a interface do usuário do suplemento.
  
    
    



```HTML

<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
    <SharePoint:ScriptLink name="clienttemplates.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientforms.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientpeoplepicker.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="autofill.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.runtime.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.core.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <div id="peoplePickerDiv"></div>
    <div>
        <br/>
        <input type="button" value="Get User Info" onclick="getUserInfo()"></input>
        <br/>
        <h1>User info:</h1>
        <p id="resolvedUsers"></p>
        <h1>User keys:</h1>
        <p id="userKeys"></p> 
        <h1>User ID:</h1>
        <p id="userId"></p>
    </div>
</asp:Content>
```


> **OBSERVAçãO**
> Dependendo do seu ambiente, você não pode precisar fazer referência explícita todas essas dependências.
  
    
    

O exemplo a seguir mostra o script do arquivo App.js exe. Esse script inicializa e processa o seletor, ele pede informações do usuário e, em seguida, obtém a identificação de usuário usando o modelo de objeto JavaScript.
  
    
    



```

// Run your custom code when the DOM is ready.
$(document).ready(function () {

    // Specify the unique ID of the DOM element where the
    // picker will render.
    initializePeoplePicker('peoplePickerDiv');
});

// Render and initialize the client-side People Picker.
function initializePeoplePicker(peoplePickerElementId) {

    // Create a schema to store picker properties, and set the properties.
    var schema = {};
    schema['PrincipalAccountType'] = 'User,DL,SecGroup,SPGroup';
    schema['SearchPrincipalSource'] = 15;
    schema['ResolvePrincipalSource'] = 15;
    schema['AllowMultipleValues'] = true;
    schema['MaximumEntitySuggestions'] = 50;
    schema['Width'] = '280px';

    // Render and initialize the picker. 
    // Pass the ID of the DOM element that contains the picker, an array of initial
    // PickerEntity objects to set the picker value, and a schema that defines
    // picker properties.
    this.SPClientPeoplePicker_InitStandaloneControlWrapper(peoplePickerElementId, null, schema);
}

// Query the picker for user information.
function getUserInfo() {

    // Get the people picker object from the page.
    var peoplePicker = this.SPClientPeoplePicker.SPClientPeoplePickerDict.peoplePickerDiv_TopSpan;

    // Get information about all users.
    var users = peoplePicker.GetAllUserInfo();
    var userInfo = '';
    for (var i = 0; i < users.length; i++) {
        var user = users[i];
        for (var userProperty in user) { 
            userInfo += userProperty + ':  ' + user[userProperty] + '<br>';
        }
    }
    $('#resolvedUsers').html(userInfo);

    // Get user keys.
    var keys = peoplePicker.GetAllUserKeys();
    $('#userKeys').html(keys);

    // Get the first user's ID by using the login name.
    getUserId(users[0].Key);
}

// Get the user ID.
function getUserId(loginName) {
    var context = new SP.ClientContext.get_current();
    this.user = context.get_web().ensureUser(loginName);
    context.load(this.user);
    context.executeQueryAsync(
         Function.createDelegate(null, ensureUserSuccess), 
         Function.createDelegate(null, onFail)
    );
}

function ensureUserSuccess() {
    $('#userId').html(this.user.get_id());
}

function onFail(sender, args) {
    alert('Query failed. Error: ' + args.get_message());
}
```


## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Criar componentes UX do SharePoint 2013](create-ux-components-in-sharepoint-2013.md)
    
  
-  [Seletor de pessoas e visão geral de provedores de declarações (SharePoint 2013)](http://technet.microsoft.com/library/gg602078.aspx)
    
  
-  [Configurar o seletor de pessoas no SharePoint 2013](http://technet.microsoft.com/library/gg602075.aspx)
    
  

