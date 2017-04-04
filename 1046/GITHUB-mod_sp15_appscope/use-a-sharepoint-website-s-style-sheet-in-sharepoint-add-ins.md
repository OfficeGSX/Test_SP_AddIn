---
title: Use a folha de estilos de um site SharePoint no SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 25d84ac5-d2b3-40c7-962d-1408aacf14ed
---


# Use a folha de estilos de um site SharePoint no SharePoint Add-ins
Saiba como usar a folha de estilo de um site SharePoint em um Suplemento do SharePoint.
Você pode fazer referência a folha de estilos de um site do SharePoint no seu Suplemento do SharePoint e usá-lo para alterar o estilo de suas páginas da Web usando a folha de estilos em SharePoint. Além disso, se alguém mudar a folha de estilo ou o tema do site do SharePoint, você pode adotar o novo conjunto de estilos em seu suplemento sem modificar a referência de folha de estilo em seu suplemento.
  
    
    


> **IMPORTANTE**
> Se suas páginas da Web usam o controle de cromo ou a página mestra suplemento, os estilos já estão disponíveis para uso, e você não precisa fazer referência a folha de estilo manualmente usando o procedimento neste artigo.
  
    
    


## Pré-requisitos para uso dos exemplos neste artigo
<a name="SP15Usestylesheetcontrol_Prereq"> </a>

Você precisa de um ambiente de desenvolvimento conforme explicado em  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md).
  
    
    

### Principais conceitos saber antes de usar a folha de estilos do SharePoint em um Suplemento do SharePoint

A tabela a seguir lista os artigos úteis que podem ajudá-lo a entender os conceitos envolvidos em um cenário que utiliza a folha de estilos do SharePoint.
  
    
    

**Tabela 1. Principais conceitos antes de usar a folha de estilo**


|**Título do artigo**|**Descrição**|
|:-----|:-----|
| [Suplementos do SharePoint](sharepoint-add-ins.md) <br/> |Saiba mais sobre o novo modelo suplemento em SharePoint que permite que você crie suplementos, que são pequenas e fácil de usar soluções para usuários finais. <br/> |
| [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md) <br/> |Saiba mais sobre as opções de experiência (eu) do usuário e a alternativas que você precisa ao construir Suplementos do SharePoint. <br/> |
| [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |Saiba mais sobre a diferença entre webs de host e o suplemento webs. Descubra quais componentes SharePoint podem ser incluídos em um Suplemento do SharePoint, quais componentes são implantados na Web de host, quais componentes são implantados na Web de suplemento e como o suplemento web é implantada em um domínio isolado. <br/> |
   

## Exemplo de código: Use a folha de estilos de um site SharePoint em um Suplemento do SharePoint
<a name="SP15Usestylesheetcontrol_Example"> </a>

Este exemplo de código mostra como usar a folha de estilos do site do SharePoint. Isso permite que as páginas do seu aplicativo web remoto coincidir com a aparência das páginas em web host do SharePoint.
  
    
    
Para usar a folha de estilo em um Suplemento do SharePoint, siga estas etapas:
  
    
    

1. Criar o provedor hospedado Suplemento do SharePoint..
    
  
2. Força o provisionamento de suplemento web criando uma página em branco.
    
  
3. Adicionar uma página da Web para o projeto da web e a folha de estilo de referência.
    
  
4. Edite o elemento no manifesto do suplemento.
    
  
A Figura 1 mostra uma página da Web do SharePoint que está usando a folha de estilos.
  
    
    

**Figura 1. Página da Web usando a folha de estilo**

  
    
    

  
    
    
![A web page using the stylesheet control](images/StylesheetControl_result.png)
  
    
    

### Para criar o Add-in do SharePoint e projetos web remoto


1. Abra Visual Studio como administrador. (Para fazer isso, clique com botão direito no ícone Visual Studio no menu **Iniciar** e escolha **Executar como administrador** ).
    
  
2. Crie o hospedado em provedor Suplemento do SharePoint conforme explicado em  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md) e nomeie-oStylesheetAdd-in.
    
  

### Para forçar o provisionamento de suplemento web criando uma página em branco


1. Com o botão direito no projeto Suplemento do SharePoint e adicione um novo módulo.
    
  
2. Com o botão direito no novo módulo e adicione um novo item.
    
  
3. Em **Visual c# itens**, **Web**, escolha **Página HTML**. Renomeie a página como **blank.html**.
    
  
4. Exclua o conteúdo da blank.html.
    
  

### Para adicionar uma página da Web que faz referência a folha de estilos no projeto da web


1. Com o botão direito do projeto da web e, em seguida, adicione um novo formulário da Web. Renomeie o formulário da Web para **StyleConsumer.aspx**.
    
  
2. Substitua o conteúdo do arquivo. aspx formulário da Web com o código a seguir. O código executa as seguintes tarefas:
    
  - Carrega a página de blank.html da web add-in em um IFrame invisível.
    
  
  - Carrega o arquivo de defaultcss.ashx da web add-in.
    
  
  - Usa os estilos disponíveis.
    
  

  ```
  
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="StyleConsumer.aspx.cs" Inherits="StylesheetAppWeb.StyleConsumer" %>

<!DOCTYPE html>
<html>
<head>
    <title>Add-in using stylesheet</title>
</head>
<body>

    <!-- The main page title -->
    <h1 class="ms-core-pageTitle">Stylesheet add-in</h1>

    <!-- Some subtitle -->
    <h1 class="ms-accentText">For people</h1>

    <!-- Subtitle comments -->
    <h2 class="ms-accentText">who care about the style in their add-ins</h2>
    <p></p>
    <div>
        <h2 class="ms-webpart-titleText">Get started with style in your add-in... </h2>
        <a class="ms-commandLink" href="#">some command</a>
        <br />
        This sample shows you how to use some of the classes defined in the SharePoint website's style sheet.
    </div>

    <!-- Script to load SharePoint resources
        and load the blank.html page in
        the invisible iframe
        -->
    <script type="text/javascript">
        "use strict";
        var appweburl;

        (function () {
            var ctag;

            // Get the URI decoded add-in web URL.
            appweburl =
                decodeURIComponent(
                    getQueryStringParameter("SPAppWebUrl")
            );
            // Get the ctag from the SPClientTag token.
            ctag =
                decodeURIComponent(
                    getQueryStringParameter("SPClientTag")
            );

            // The resource files are in a URL in the form:
            // web_url/_layouts/15/Resource.ashx
            var scriptbase = appweburl + "/_layouts/15/";

            // Dynamically create the invisible iframe.
            var blankiframe;
            var blankurl;
            var body;
            blankurl = appweburl + "/Pages/blank.html";
            blankiframe = document.createElement("iframe");
            blankiframe.setAttribute("src", blankurl);
            blankiframe.setAttribute("style", "display: none");
            body = document.getElementsByTagName("body");
            body[0].appendChild(blankiframe);

            // Dynamically create the link element.
            var dclink;
            var head;
            dclink = document.createElement("link");
            dclink.setAttribute("rel", "stylesheet");
            dclink.setAttribute("href", scriptbase + "defaultcss.ashx?ctag=" + ctag);
            head = document.getElementsByTagName("head");
            head[0].appendChild(dclink);
        })();

        // Function to retrieve a query string value.
        // For production purposes you may want to use
        //  a library to handle the query string.
        function getQueryStringParameter(paramToRetrieve) {
            var params;
            var strParams;

            params = document.URL.split("?")[1].split("&amp;");
            strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
                var singleParam = params[i].split("=");
                if (singleParam[0] == paramToRetrieve)
                    return singleParam[1];
            }
        }
    </script>
</body>
</html>

  ```


    Em alguns casos, o usuário deve ser autenticado no SharePoint antes de sua página poderá baixar o CSS e imagens para definição de estilo. Marcas de link não autenticar um usuário que já não tiver entrado no automaticamente. Considere a possibilidade de carregamento de um recurso de página da web suplemento na sua página da Web para forçar a autenticação do usuário antes de vinculação para o arquivo CSS. Neste exemplo, a página de blank.html for carregada em um IFrame invisível.
    
  

### Para editar o elemento de página inicial no manifesto do suplemento


1. Clique duas vezes no arquivo **AppManifest.xml** no **Solution Explorer**.
    
  
2. No menu suspenso **página inicial**, selecione a página da Web que usa a folha de estilos.
    
  

### Para criar e executar a solução


1. Certifique-se de que o projeto Suplemento do SharePoint é definido como o projeto de inicialização.
    
  
2. Pressione a tecla F5.
    
    > **OBSERVAçãO**
      > Quando você pressiona F5, Visual Studio aproveita a solução, implanta o suplemento e abre a página de permissões para o suplemento.
3. Escolha o botão **De confiança**.
    
  
4. Clique no ícone do suplemento **StylesheetBasic**.
    
  
5. A Figura 2 mostra a página da Web resultante usando os estilos do SharePoint.
    
   **Figura 2. Folha de estilo usada na página**

  

     ![Stylesheet control used in a web page](images/StylesheetControl_result2.png)
  

  

  
6. Você também pode ir para a web de host e alterar o tema. Recarregar a página de suplemento para usar os novos estilos.
    
  

**Tabela 2. A solução de problemas**


|**Problema**|**Solução**|
|:-----|:-----|
|Visual Studio não abra o navegador depois que você pressionar a tecla F5. <br/> |Defina o projeto de Suplemento do SharePoint como o projeto de inicialização. <br/> |
|Erro de certificado. <br/> |Defina a propriedade **SSL habilitado** do seu projeto web como false. No projeto Suplemento do SharePoint, defina a propriedade do **Projeto da Web** comoNenhume, em seguida, defina a propriedade volta ao nome do seu projeto de web. <br/> |
   

## Próximas etapas
<a name="SP15Usestylesheetcontrol_Nextsteps"> </a>

Este artigo demonstrado como usar a folha de estilo em um Suplemento do SharePoint. Como próxima etapa, você pode aprender sobre outros componentes eu que estão disponíveis para Suplementos do SharePoint. Para saber mais, consulte o seguinte:
  
    
    

-  [Código de exemplo: Use a folha de estilos do SharePoint em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-7a8684e2)
    
  
-  [Use o controle de cromo do cliente no SharePoint Add-ins](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [Criar ações personalizadas para implantar o SharePoint Add-ins](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)
    
  
-  [Criar partes do suplemento para instalar com o SharePoint Add-in](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## Recursos adicionais
<a name="SP15Usestylesheetcontrol_Addresources"> </a>


-  [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md)
    
  
-  [Diretrizes de design UX de suplementos do SharePoint](sharepoint-add-ins-ux-design-guidelines.md)
    
  
-  [Criar componentes UX do SharePoint 2013](create-ux-components-in-sharepoint-2013.md)
    
  
-  [Três maneiras de pensar sobre opções de design para o SharePoint Add-ins](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

