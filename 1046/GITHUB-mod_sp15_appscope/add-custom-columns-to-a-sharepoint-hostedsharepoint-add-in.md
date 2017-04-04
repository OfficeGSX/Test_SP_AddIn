---
title: Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint
ms.prod: SHAREPOINT
ms.assetid: 0fbc9a8b-d652-44a8-b821-578afd8f33dc
---


# Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint
Saiba como incluir colunas personalizadas em um Suplementos do SharePoint.
Esta é a terceira em uma série de artigos sobre noções básicas do desenvolvimento hospedado no SharePoint Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os artigos anteriores desta série:
  
    
    


-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Implantar e instalar um hospedado no SharePoint SharePoint Add-in](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> [!OBSERVAçãO]
> Se você tiver trabalhado através desta série sobre hospedado no SharePoint suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) e abra o arquivo BeforeColumns.sln.
  
    
    

Neste artigo vamos voltar a codificação adicionando algumas colunas de site para a orientação de funcionário Suplemento do SharePoint.
## Criar tipos de coluna personalizados


  
    
    

1. No **Solution Explorer**, clique com botão direito do projeto e escolha **Adicionar** > **Nova pasta**. O nome da pasta de Colunas de Site.
    
  
2. Clique com botão direito do mouse na nova pasta e escolha **Adicionar** > **Novo Item**. A caixa de diálogo **Adicionar Novo Item** é aberta no nó **Office/SharePoint**.
    
  
3. Escolha a **Coluna de Site**, dê a ela o nome de divisãoe escolha **Adicionar**.
    
  
4. No arquivo Elements. XML para a nova coluna de site, edite o elemento de **Field** para que ele tenha os atributos e valores mostrados no exemplo a seguir, exceto pelo fato de *você deve **alterar o GUID*** para o **ID** o atributo do valor desse Visual Studio gerada para ele, *então, tome cuidado se você estiver usando copiar e colar*  .
    
  ```
  
<Field ID="{generated GUID}"
       Name="Division" 
       Title="Division" 
       DisplayName="Division" 
       Description="The division of the company where the employee works." 
       Group="Employee Orientation" 
       Type="Text" 
       Required ="FALSE">
</Field>
  ```

5. Adicione outra **Coluna de Site** na mesma pasta denominadaOrientationStage.
    
  
6. No arquivo Elements. XML para a nova coluna de site, edite o elemento **Field** para que ele tenha os atributos e valores mostrados no exemplo a seguir, exceto que você não deve alterar o GUID para o atributo **ID** do valor desse Visual Studio gerada para ele.
    
  ```
  
<Field ID="{generated GUID}"
       Name="OrientationStage" 
       Title="OrientationStage"
       DisplayName="Orientation Stage" 
       Group="Employee Orientation" 
       Description="The current orientation stage of the employee." 
       Type="Choice"
       Required ="TRUE">
</Field>
  ```

7. Como este é um campo de escolha, você deve especificar as opções possíveis e a ordem em que elas devem aparecer na lista suspensa quando um usuário está fazendo uma opção. E como ele é um campo obrigatório, você deve especificar um valor padrão. Adicione a seguinte marcação de filhos ao elemento **Field**.
    
  ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Tour of building</CHOICE>
      <CHOICE>HR paperwork</CHOICE>
      <CHOICE>Corporate network access</CHOICE>
      <CHOICE>Completed</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Tour of building</MAPPING>
      <MAPPING Value="3">HR paperwork</MAPPING>
      <MAPPING Value="4">Corp network access</MAPPING>
      <MAPPING Value="5">Completed</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```

8. Salve todos os arquivos.
    
  

## Execute o add-in e as colunas de teste


  
    
    

1. Use a tecla F5 para implantar e executar seu suplemento Visual Studio faz uma instalação temporária do add-in no seu site do SharePoint de teste e o executa imediatamente o add-in.
    
  
2. Quando a página de padrão do add-in for aberta, escolha o link de **Novos funcionários em Seattle** para abrir a instância de lista personalizada.
    
  
3. Abra a página de **configurações** da lista e adicione as duas colunas a ela com estas etapas.
    
1. Clique no botão do texto explicativo, **· · ·**, logo acima da lista e escolha **Create View**.
    
  
2. Abre a página do **Tipo de exibição**, com a estrutura de navegação de trilha **Configurações > Exibir tipo** próxima à parte superior. Clique em **configurações de** trilha.
    
   **Etapas para abrir a página de configurações de lista**

  

     ![A lista Novo Funcionário em Seattle com botão de balão e o item Criar Modo de Exibição realçados como primeira etapa. Em seguida, uma seta para a página Criar Modo de Exibição com a barra de navegação Configurações realçada.](images/6c119cae-adf8-42ff-9890-f3aa1e11719d.png)
  

    
    
  
3. Na página **configurações**, abra o link de **Adicionar colunas de site existente** à esquerda sobre na metade para baixo na página.
    
   **Página de configurações de lista**

  

     ![A página de configurações da instância da lista com o link para Adicionar Colunas a partir das Colunas de Site realçado.](images/a8698b77-b9d2-40f6-89f6-ccc3c6e06073.png)
  

    
    
  
4. Na página **Adicionar colunas de colunas do Site**, escolha a **Orientação do funcionário** na lista suspensa **Selecionar colunas de site de**.
    
   **Adicionar colunas da página de colunas de Site**

  

     ![O controle de seleção de coluna do SharePoint, com a Orientação do Funcionário selecionada no menu suspenso rotulado Selecionar colunas de site.](images/3b33c622-c52a-45fd-8ea1-d7f307539753.png)
  

    
    
  
5. Adicione as colunas de **divisão** e **OrientationStage** à caixa de **colunas a serem adicionadas**.
    
  
6. Escolha **OK** para retornar à página de **configurações** e, em seguida, clique em navegação estrutural **Novos funcionários em Seattle** próxima à parte superior da página.
    
  
4. As novas colunas agora estão na lista. Adicione um novo item à lista. No formulário de edição, o campo de **Orientação estágio** já terá o valor padrãoNão foi iniciado. (Os itens existentes ficará em branco neste campo porque eles foram criados antes do campo foi na lista.)
    
   **A lista com novas colunas**

  

     ![A lista com as novas colunas de Divisão e Estágio de Orientação.](images/d4e17424-c06b-4635-aab8-4912cee5fe35.png)
  

    
    
  
5. Para encerrar a sessão de depuração, feche a janela do navegador ou interromper a depuração em Visual Studio. Cada vez que você pressionar F5, Visual Studio retração da versão anterior do add-in e instalar a versão mais recente.
    
  
6. Você irá trabalhar com essa solução de suplemento e Visual Studio em outros artigos e é uma boa prática para cancelar o suplemento uma última vez quando terminar de trabalhar com ele por algum tempo. Com o botão direito do projeto no **Solution Explorer** e escolha **Retract**.
    
  

## 
<a name="Nextsteps"> </a>

Você realmente não quer que eles tenham adicionar manualmente as colunas personalizadas à lista, o modelo de lista no próximo artigo desta série, você vai criar um tipo de conteúdo personalizado que inclua as colunas personalizadas e é automaticamente associada com os novos funcionários:  [Adicionar um tipo de conteúdo personalizado a um suplemento do SharePoint-hostedSharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md).
  
    
    

