---
title: Personalizar um modo de exibição de lista de suplementos do SharePoint usando o processamento do lado do cliente
ms.prod: SHAREPOINT
ms.assetid: 8d5cabb2-70d0-46a0-bfe0-9e21f8d67d86
---


# Personalizar um modo de exibição de lista de suplementos do SharePoint usando o processamento do lado do cliente
Saiba como personalizar um modo de exibição de lista em um suplemento hospedado no SharePoint usando a tecnologia de renderização do lado do cliente no SharePoint 2013.
Em SharePoint 2013, a renderização do lado do cliente fornece uma maneira de produzir seu próprios saída para um conjunto de controles que são hospedados em uma página do SharePoint. Permite que você pode usar tecnologias conhecidas, como HTML e JavaScript, para definir a lógica de processamento de exibições de lista do SharePoint. Com o processamento do lado do cliente, você pode especificar seus próprios recursos JavaScript e hospedá-los nas opções de armazenamento de dados que estão disponíveis para os seus suplementos, como em uma biblioteca de documentos. Um suplemento hospedado no SharePoint inclui os componentes do SharePoint. Um suplemento de hospedado no SharePoint tem seus recursos em um subsite isolado da web host, denominado web add-in.
  
    
    


## Pré-requisitos para uso dos exemplos neste artigo
<a name="SP15CSRlistview_Prereq"> </a>

Para executar as etapas neste exemplo, será necessário o seguinte:
  
    
    

-  [2015 do Visual Studio e as ferramentas de desenvolvedor mais recente do Microsoft Office](https://www.visualstudio.com/features/office-tools-vs)
    
  
- Um ambiente de desenvolvimento SharePoint 2013 (suplemento isolamento necessário para os cenários de local)
    
  
Para obter orientação sobre como configurar um ambiente de desenvolvimento que atenda às suas necessidades, consulte  [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx).
  
    
    

### Conceitos principais para ajudá-lo a entender a personalização do modo de exibição de lista com a renderização do lado do cliente

A tabela a seguir lista os artigos úteis que podem ajudá-lo a entender os conceitos que estão envolvidos em um cenário de personalização do modo de exibição de lista.
  
    
    

**Tabela 1. Principais conceitos para personalização de modo de exibição de lista em um suplemento**


|**Título do artigo**|**Descrição**|
|:-----|:-----|
| [Suplementos do SharePoint](sharepoint-add-ins.md) <br/> |Saiba mais sobre o novo modelo suplemento em Microsoft SharePoint 2013 que permite que você crie suplementos, que são pequenas e fácil de usar soluções para usuários finais. <br/> |
| [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md) <br/> |Saiba mais sobre as opções de eu que estão disponíveis quando você estiver criando Suplementos do SharePoint. <br/> |
| [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |Saiba mais sobre a diferença entre webs de host e o suplemento webs. Descubra quais componentes SharePoint 2013 podem ser incluídos em um Suplemento do SharePoint, quais componentes são implantados na Web de host, quais componentes são implantados na Web de suplemento e como o suplemento web é implantada em um domínio isolado. <br/> |
   

## Exemplo de código: personalizar um modo de exibição de lista, usando a renderização do lado do cliente
<a name="SP15CSRlistview_Codeexample"> </a>

Para personalizar um modo de exibição de lista que é implantado na Web suplemento usando renderização do lado do cliente, siga estas etapas:
  
    
    

1. Crie o projeto Suplemento do SharePoint.
    
  
2. Crie uma nova definição de lista com uma exibição personalizada.
    
  
3. Fornece a lógica de processamento personalizado em um arquivo de JavaScript.
    
  
A Figura 1 mostra um modo de exibição renderizado do cliente de uma lista de comunicados.
  
    
    

**Figura 1. Modo de exibição personalizado de uma lista de comunicados**

  
    
    

  
    
    
![Custom view of an announcements list](images/CSRListView_result.png)
  
    
    

### Para criar o projeto Suplemento do SharePoint


1. Abra 2015 do Visual Studio como administrador. (Para fazer isso, clique com botão direito no ícone do **Visual Studio** no menu **Iniciar** e escolha **Executar como administrador** ).
    
  
2. Crie um novo projeto usando o modelo de **suplemento do SharePoint**.
    
    Figura 2 mostra a localização do modelo **do suplemento do SharePoint** no Visual 2015 Studio, em **modelos**, **Visual c#**, **Office/SharePoint**, **suplementos do Office**.
    

   **Figura 2. Suplemento para o modelo do SharePoint 2013 Visual Studio**

  

     ![App for SharePoint 2013 Visual Studio template](images/AppForSharePointVSTemplate.PNG)
  

  

  
3. Forneça a URL do site do SharePoint que você deseja usar para depuração.
    
  
4. Selecione **hospedado no SharePoint** como a opção de hospedagem para seu suplemento.
    
  

### Para criar uma nova definição de lista


1. Com o botão direito no projeto Suplemento do SharePoint e adicione um novo item de **lista**. Crie uma lista personalizável com base em anúncios.
    
  
2. Copie a seguinte marcação e colá-lo no elemento **Views** no arquivo Schema. XML do recurso de sua lista. A marcação executa as seguintes tarefas:
    
  - Declara uma nova exibição chamada Overridable com um BaseViewID = 2.
    
  
  - Fornece um valor para o elemento **JSLink** que aponta para um arquivo de JavaScript que é provisionado com o suplemento.
    
    > **OBSERVAçãO**
      > A propriedade não é suportada em pesquisa ou eventos de JSLink lista. Um calendário do SharePoint é uma lista de eventos.

  ```XML
  
<View BaseViewID="2"
      Name="8d2719f3-c3c3-415b-989d-33840d8e2ddb" 
      DisplayName="Overridable" 
      Type="HTML" 
      WebPartZoneID="Main" 
      SetupPath="pages\\viewpage.aspx" 
      Url="Overridable.aspx"
      DefaultView="TRUE">
  <ViewFields>
    <FieldRef Name="Title" />
  </ViewFields>
  <Query />
  <Toolbar Type="Standard" />
  <XslLink>main.xsl</XslLink>
  <JSLink Default="TRUE">~site/Scripts/CSRListView.js</JSLink>
</View>
  ```


### Para fornecer a lógica de processamento personalizado em um arquivo JavaScript


1. Com o botão direito na pasta **Scripts** e adicione um novo arquivo de JavaScript. Nomeie o arquivoCSRListView.js.
    
  
2. Copie o código a seguir e cole-o no arquivo CSRListView.js. O código executa as seguintes tarefas:
    
  - Fornece manipuladores de eventos para os eventos **PreRender** e **PostRender**.
    
  
  - Fornece modelos para os conjuntos de modelo de cabeçalho, rodapé e Item.
    
  
  - Registra os modelos.
    
  

  ```
  
(function () {
    // Initialize the variable that stores the objects.
    var overrideCtx = {};
    overrideCtx.Templates = {};

    // Assign functions or plain html strings to the templateset objects:
    // header, footer and item.
    overrideCtx.Templates.Header = "<B><#=ctx.ListTitle#></B>" +
        "<hr><ul id='unorderedlist'>";

    // This template is assigned to the CustomItem function.
    overrideCtx.Templates.Item = customItem;
    overrideCtx.Templates.Footer = "</ul>";

    // Set the template to the:
    //  Custom list definition ID
    //  Base view ID
    overrideCtx.BaseViewID = 2;
    overrideCtx.ListTemplateType = 10057;

    // Assign a function to handle the
    // PreRender and PostRender events
    overrideCtx.OnPreRender = preRenderHandler;
    overrideCtx.OnPostRender = postRenderHandler;

    // Register the template overrides.
    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(overrideCtx);
})();

// This function builds the output for the item template.
// It uses the context object to access announcement data.
function customItem(ctx) {

    // Build a listitem entry for every announcement in the list.
    var ret = "<li>" + ctx.CurrentItem.Title + "</li>";
    return ret;
}

// The preRenderHandler attends the OnPreRender event
function preRenderHandler(ctx) {

    // Override the default title with user input.
    ctx.ListTitle = prompt("Type a title", ctx.ListTitle);
}

// The postRenderHandler attends the OnPostRender event
function postRenderHandler(ctx) {

    // You can manipulate the DOM in the postRender event
    var ulObj;
    var i, j;

    ulObj = document.getElementById("unorderedlist");
    
    // Reverse order the list.
    for (i = 1; i < ulObj.children.length; i++) {
        var x = ulObj.children[i];
        for (j = 1; j < ulObj.children.length; j++) {
            var y = ulObj.children[j];
            if(x.innerText<y.innerText){                  
                ulObj.insertBefore(y, x);
            }
        }
    }
}
  ```


### Para criar e executar a solução


1. Pressione a tecla F5.
    
    > **OBSERVAçãO**
      > Quando você pressiona F5, Visual Studio aproveita a solução, implanta o suplemento e abre a página de permissões para o suplemento.
2. Escolha o botão **De confiança**.
    
  
3. Vá para sua lista personalizada, inserindo o endereço  _/Lists/<your_list_instance>_ em relação ao seu diretório add-in no domínio suplemento web (não o domínio da web de host). Adicione um ou dois anúncios. Na faixa de opções, escolha o modo de exibição **Overridable**.
    
  

## Próximas etapas
<a name="SP15CSRlistview_Nextsteps"> </a>

Este artigo demonstrado como personalizar um modo de exibição de lista em um Suplemento do SharePoint usando renderização do lado do cliente. Como próxima etapa, você pode aprender sobre os outros componentes eu que estão disponíveis para Suplementos do SharePoint. Para saber mais, consulte o seguinte:
  
    
    

-  [Código de exemplo: personalizar uma exibição de lista em um suplemento usando o processamento do lado do cliente](http://code.msdn.microsoft.com/SharePoint-2013-Customize-61761017)
    
  
-  [Use a folha de estilos de um site SharePoint no SharePoint Add-ins](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md)
    
  
-  [Use o controle de cromo do cliente no SharePoint Add-ins](use-the-client-chrome-control-in-sharepoint-add-ins.md)
    
  
-  [Criar ações personalizadas para implantar o SharePoint Add-ins](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)
    
  
-  [Criar partes do suplemento para instalar com o SharePoint Add-in](create-add-in-parts-to-install-with-your-sharepoint-add-in.md)
    
  

## Recursos adicionais
<a name="SP15CSRlistview_AddResources"> </a>


-  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Design de eu para o SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md)
    
  
-  [Criar componentes UX do SharePoint 2013](create-ux-components-in-sharepoint-2013.md)
    
  
-  [Três maneiras de pensar sobre opções de design para o SharePoint Add-ins](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

