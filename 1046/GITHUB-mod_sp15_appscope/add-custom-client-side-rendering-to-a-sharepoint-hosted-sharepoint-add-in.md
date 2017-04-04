---
title: Adicionar processamento personalizado de cliente para um hospedado no SharePoint SharePoint Add-in
ms.prod: SHAREPOINT
ms.assetid: 6e0e530a-7d8b-48ab-8d0c-a878ddbf5be1
---


# Adicionar processamento personalizado de cliente para um hospedado no SharePoint SharePoint Add-in
Personalize a renderização e validação de controles nas páginas de Suplementos do SharePoint.
Esse é o oitavo em uma série de artigos sobre noções básicas do desenvolvimento hospedado no SharePoint Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Implantar e instalar um hospedado no SharePoint SharePoint Add-in](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Adicionar um tipo de conteúdo personalizado a um suplemento do SharePoint-hostedSharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Adicionar uma Web Part a uma página em um SharePoint hospedado SharePoint Add-in](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar um fluxo de trabalho para um hospedado no SharePoint SharePoint Add-in](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar uma página personalizada e um estilo a um hospedado no SharePoint SharePoint Add-in](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> [!OBSERVAçãO]
> Se você tiver trabalhado através desta série sobre hospedado no SharePoint suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) e abra o arquivo BeforeClientRenderedControl.sln.
  
    
    

Você pode usar um pouco de cliente JavaScript para personalizar a renderização de Web Parts, a maioria dos tipos de campos (colunas) e alguns outros controles, atribuindo um arquivo JavaScript à propriedade **JSLink** do controle, como **SPField.JSLink**. Você também pode adicionar lógica de validação do lado do cliente dessa maneira. Neste artigo, você personalizar a renderização de um campo em uma lista da orientação do funcionário Suplemento do SharePoint usando renderização do lado do cliente.
> [!OBSERVAçãO]
> Se o usuário final tiver JavaScript desabilitada em seu navegador, SharePoint se voltará para validação e renderização do lado do servidor.
  
    
    


> [!OBSERVAçãO]
> A propriedade não é suportada em pesquisa ou eventos de JSLink lista. Um calendário do SharePoint é uma lista de eventos.
  
    
    


## Criar e registrar o JavaScript


  
    
    

1. No **Solution Explorer**, clique com botão direito no nó **Scripts** e escolha **Adicionar** > **Novo Item** > **Web**.
    
  
2. Escolha **Arquivo JavaScript** e nomeie-oOrientationStageRendering.js.
    
  
3. Portanto, seu processamento personalizado do campo deve acontecer automaticamente, adicione um método anônimo para JavaScript que seja executado automaticamente quando o arquivo for carregado com o código a seguir.
    
  ```
  
(function () {

})();
  ```

4. No corpo fo esse método (entre os caracteres {}), adicione o seguinte código para criar objetos JSON (Javascript Object Notation) para o contexto de substituição de renderização, os modelos no contexto e os modelos para os campos.
    
  ```
  
var customRenderingOverride = {};
customRenderingOverride.Templates = {};
customRenderingOverride.Templates.Fields = {

}
  ```

5. No corpo do objeto de modelo  `Fields` , adicione o seguinte JSON. O nome de propriedade `OrientationStage` identifica o campo que tiver personalizado renderização. O valor da propriedade é outro objeto JSON. A propriedade `View` identifica o contexto da página na qual o processamento personalizado será aplicado. Nesse caso, o objeto está informando SharePoint para usar a renderização personalizada em modos de exibição de lista. (Outras opções seriam para edição, novo e exibir formulários.) O valor da propriedade, `renderOrientationStage`, é o nome do método processamento personalizado que você cria em uma etapa posterior.
    
  ```
  
"OrientationStage": { "View": renderOrientationStage }
  ```

6. A última coisa que o método anônimo deve fazer é informar o Gerenciador de modelo do SharePoint sobre a substituição de renderização. Adicione a seguinte linha ao final do corpo do método.
    
  ```
  SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
  ```


    O método agora deve se parecer com o seguinte.
    


  ```
  (function () {
    var customRenderingOverride = {};
    customRenderingOverride.Templates = {};
    customRenderingOverride.Templates.Fields = {
        "OrientationStage": { "View": renderOrientationStage }
    }

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
})();
  ```

7. Adicione o seguinte método ao arquivo. Ele define a cor do valor da coluna **Estágio de orientação** para vermelho quando o valor é "Não foi iniciado" e verde quando o valor é "concluído". (O objeto `ctx` é um objeto de contexto de cliente que é declarado pelo script do SharePoint no predefinidas.)
    
  ```
  
function renderOrientationStage(ctx) {
    var orientationStageValue = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];
    if (orientationStageValue == "Not Started")  {
        return "<span style='color:red'>" + orientationStageValue + "</span>"
    }
    else if (orientationStageValue == "Completed") {
        return "<span style='color:green'>" + orientationStageValue + "</span>"
    }
    else {
        return orientationStageValue;
    }
}
  ```

8. No **Solution Explorer**, expanda **Colunas de Site** e, em seguida, **OrientationStage**; e, em seguida, abra o arquivo Elements XML.
    
  
9. Para informar ao SharePoint para usar seu personalizado JavaScript, adicione um novo atributo, **JSLink**, para o elemento de **Field** e atribua a seguinte URL como seu valor: `~site/Scripts/OrientationStageRendering.js`.
    
    > [!OBSERVAçãO]
      > A propriedade **JSLink** sempre é um arquivo, não um método. Não há um meio para informar ao SharePoint qual método Execute. É por isso que o arquivo contém um método que será executado automaticamente.

    A marca de início para o elemento **Field** agora terá aparência semelhante ao seguinte.
    


  ```
  
<Field
       ID="{some_guid_here}"
       Name="OrientationStage"
       Title="OrientationStage"
       DisplayName="Orientation Stage"
       Description="The current orientation stage of the employee."
       Type="Choice"
       Required="TRUE"
       Group="Employee Orientation" 
       JSLink="~site/Scripts/OrientationStageRendering.js">
<!-- child elements and end tag omitted -->
  ```

10. Abra a página Default. aspx e adicione o código a seguir como último filho do elemento **asp:Content** que tem **ContentPlaceHolderID** definido como **PlaceHolderMain**.
    
  ```XML
  
<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Lists/NewEmployeesInSeattle/AllItems.aspx';"
    Text="List View Page for New Employees in Seattle" /></p>

  ```


## Executar e testar o suplemento


  
    
    

1. Use a tecla F5 para implantar e executar seu suplemento Visual Studio faz uma instalação temporária do add-in no seu site do SharePoint de teste e o executa imediatamente o add-in.
    
  
2. A renderização do lado do cliente que você configurou afeta a renderização do campo apenas na página de modo de exibição de lista, não na Web Part que podemos colocar na home page de exibição de lista. Isso ocorre porque a Web Part padrões para processamento no servidor. Há maneiras para reverter a isso, mas eles são muito avançados para que esse exemplo simple. Assim, para ver a renderização do lado do cliente em ação, clique no link na parte inferior da página da **Página de modo de exibição de lista para novos funcionários em Seattle**.
    
  
3. Quando a página de exibição da lista for aberto, defina o valor do **Estágio de orientação** para alguns itens para **Não foi iniciado** e defina outras pessoas como **concluído** para ver a renderização de cor personalizada.
    
   **Lista com processamento personalizado de cliente**

  

     ![Novos funcionários na lista de Seattle com os valores de Estágio de Orientação "Não iniciado" em vermelho e valores de "Concluído" em verde. Outros valores em preto.](images/dc8e2b7d-1747-4b65-aab4-6fc93c6867d4.PNG)
  

  

  
4. Para encerrar a sessão de depuração, feche a janela do navegador ou interrompa a depuração no Visual Studio. Sempre que você pressiona F5, o Visual Studio retira a versão anterior do suplemento e instala a última mais recente.
    
  
5. Você vai trabalhar com esse suplemento e com a solução do Visual Studio em outros artigos. Além disso, é uma prática recomendada retirar o suplemento uma última vez, quando deixar de trabalhar com ele por algum tempo. Clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Retirar**.
    
  

## 
<a name="Nextsteps"> </a>

O próximo artigo desta série, você adicionará um item de menu personalizado e um botão personalizado à faixa de opções no Suplemento do SharePoint:  [Create a custom ribbon button in the host web of a SharePoint Add-in](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md).
  
    
    

