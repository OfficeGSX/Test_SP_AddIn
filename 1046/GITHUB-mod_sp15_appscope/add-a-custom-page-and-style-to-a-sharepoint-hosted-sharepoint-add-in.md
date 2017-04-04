---
title: Adicionar uma página personalizada e um estilo a um hospedado no SharePoint SharePoint Add-in
ms.prod: SHAREPOINT
ms.assetid: 91a20d79-318f-44ce-9877-3fa07d03aa09
---


# Adicionar uma página personalizada e um estilo a um hospedado no SharePoint SharePoint Add-in
Saiba como incluir uma página personalizada e o arquivo CSS em um Suplementos do SharePoint.
Esse é o sétimo em uma série de artigos sobre noções básicas do desenvolvimento hospedado no SharePoint Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Implantar e instalar um hospedado no SharePoint SharePoint Add-in](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Adicionar um tipo de conteúdo personalizado a um suplemento do SharePoint-hostedSharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Adicionar uma Web Part a uma página em um SharePoint hospedado SharePoint Add-in](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar um fluxo de trabalho para um hospedado no SharePoint SharePoint Add-in](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> **OBSERVAçãO**
> Se você tiver trabalhado através desta série sobre hospedado no SharePoint suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) e abra o arquivo BeforePage.sln.
  
    
    

Neste artigo, você adiciona uma página de ajuda para a orientação de funcionário Suplemento do SharePoint e configurá-lo para usar uma folha de estilos CSS personalizada.
## Adicionar uma página


1. No **Solution Explorer**, clique com botão direito na pasta de **páginas** e escolha **Adicionar** > **Novo Item**. A caixa de diálogo **Adicionar Novo Item** é aberto para o nó **Office/SharePoint**.
    
  
2. Selecione **a página** e atribua a ele o nomeHelp.aspx.
    
  
3. Localize os dois elementos **asp:Content** no arquivo e adicione a seguinte marcação **asp:Content** terceira entre eles.
    
  ```HTML
  
<asp:Content ContentPlaceHolderID="PlaceHolderPageTitleInTitleArea" runat="server">
    Help
</asp:Content> 
  ```

4. Localize o elemento **asp:Content** com a ID de **PlaceholderAdditionalPageHead**e adicione a seguinte marcação a ela.
    
  ```HTML
  
<link rel="Stylesheet" type="text/css" href="../Content/App.css" />
  ```

5. Localize o elemento **asp:Content** com a ID de **PlaceHolderMain**e remover quaisquer elementos filho nela.
    
  
6. Adicione a seguinte como conteúdo para o mesmo elemento **asp:Content**.
    
  ```HTML
  <H3>Having a problem with the add-in?</H3>
<p> Call the help line for Fabrikam Add-ins:</p>
<p>1-555-555-5555</p>
  ```

7. Salve e feche o arquivo.
    
  
8. Abra o arquivo Default.aspx.
    
  
9. Localize o elemento **asp:Content** com a ID de **PlaceHolderMain**e, em seguida, adicione a seguinte marcação ao final dele.
    
  ```HTML
  
<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Pages/Help.aspx';"
    Text="Get help for the Employee Orientation add-in" /></p>

  ```

10. Salve e feche o arquivo.
    
  

## Adicionar uma classe de estilo à folha de estilo


  
    
    

1. No **Solution Explorer**, abra o arquivo app.css na pasta **conteúdo** e, em seguida, adicione a seguinte linha ao arquivo.
    
  ```
  
p {color: green;}
  ```

2. Salve e feche o arquivo.
    
  

## Executar e testar o suplemento


  
    
    

1. Use a tecla F5 para implantar e executar seu suplemento Visual Studio faz uma instalação temporária do add-in no seu site do SharePoint de teste e o executa imediatamente o add-in.
    
  
2. Quando página de padrão do add-in for aberto, clique no link **obter ajuda para a orientação de funcionário suplemento** para abrir a página de **Ajuda**.
    
    Abre sua página personalizada e as duas linhas que você colocar em marcas < p > são verdes.
    

   **Página de ajuda**

  

     ![Uma página do SharePoint com o título "Ajuda". Há uma linha de cabeçalho em preto, seguida por duas linhas de texto em verde.](images/2df51ab0-5b24-4a37-8b6a-6e95dbb1aeaa.PNG)
  

    
    
  
3. Para encerrar a sessão de depuração, feche a janela do navegador ou interromper a depuração em Visual Studio. Cada vez que você pressionar F5, Visual Studio retração da versão anterior do add-in e instalar a versão mais recente.
    
  
4. Você irá trabalhar com essa solução de suplemento e Visual Studio em outros artigos e é uma boa prática para cancelar o suplemento uma última vez quando terminar de trabalhar com ele por algum tempo. Com o botão direito do projeto no **Solution Explorer** e escolha **Retract**.
    
  

## 
<a name="Nextsteps"> </a>

O próximo artigo desta série, você adicionará um processamento personalizado de cliente a uma coluna de lista em um Suplemento do SharePoint:  [Adicionar processamento personalizado de cliente para um hospedado no SharePoint SharePoint Add-in](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md).
  
    
    

