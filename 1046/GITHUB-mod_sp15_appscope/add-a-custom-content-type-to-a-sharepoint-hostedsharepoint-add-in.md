---
title: Adicionar um tipo de conteúdo personalizado a um suplemento do SharePoint-hostedSharePoint
ms.prod: SHAREPOINT
ms.assetid: b52e5622-bf87-4bb1-a99a-ac1389de6651
---


# Adicionar um tipo de conteúdo personalizado a um suplemento do SharePoint-hostedSharePoint
Saiba como incluir tipos de conteúdo personalizados em um Suplementos do SharePoint.
Este é o quarto em uma série de artigos sobre noções básicas do desenvolvimento hospedado no SharePoint Suplementos do SharePoint. Você primeiro deve estar familiarizado com  [Suplementos do SharePoint](sharepoint-add-ins.md) e os outros artigos desta série:
  
    
    


-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Implantar e instalar um hospedado no SharePoint SharePoint Add-in](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  

> **OBSERVAçãO**
> Se você tiver trabalhado através desta série sobre hospedado no SharePoint suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) e abra o arquivo BeforeContentType.sln.
  
    
    

Neste artigo, você adicionar um tipo de conteúdo personalizado para a orientação de funcionário Suplemento do SharePoint.
## Criar o tipo de conteúdo personalizado


  
    
    

1. No **Solution Explorer**, clique com botão direito do projeto e escolha **Adicionar** > **Nova pasta**. O nome da pasta de Tipos de conteúdo.
    
  
2. Com o botão direito na nova pasta e escolha **Adicionar** > **Novo Item**. A caixa de diálogo **Adicionar Novo Item** é aberto para o nó **Office/SharePoint**..
    
  
3. Escolha o **Tipo de conteúdo** e dê a ela o nomeNewEmployeee escolha **Adicionar**. Quando solicitado pelo Assistente para selecionar o tipo de conteúdo base, escolha o **Item** e, em seguida, escolha **Concluir**.
    
  
4. Se o designer de tipo de conteúdo não abrir automaticamente, escolha o tipo de conteúdo **NewEmployee** no **Solution Explorer** para abri-lo.
    
  
5. Abra a guia **Tipo de conteúdo** no designer e preencha as caixas de texto da seguinte maneira:
    
  - **Nome do tipo de conteúdo**: NewEmployee
    
  
  - **Descrição**: representa um novo funcionário.
    
  
  - **Nome do grupo**: orientação do funcionário
    
  
6. Verifique se que  *Nenhum*  da seleção são marcadas na guia. A caixa de seleção **herda as colunas do tipo de conteúdo pai** pode ser selecionada por padrão. *Não se esqueça de desmarcar proprietário.*  Na guia agora deve parecer com o seguinte:
    
   **Guia de tipo de conteúdo**

  

     ![O Designer de Tipo de Conteúdo mostrando "NewEmployee" como o nome do tipo, "Representa um novo funcionário" como a descrição e "Orientação do Funcionário" como o grupo.](images/8a9768f4-315d-45c0-88d7-687dbf84495c.PNG)
  

    
    
  
7. Abra a guia de **colunas** no designer.
    
  
8. Em gird, escolha **clique aqui para adicionar uma coluna** para abrir uma lista de colunas suspensa e adicione a coluna de **divisão**. Ela é listada na lista suspensa por seu nome de exibição: **divisão**. Faça o mesmo para a coluna de **Orientação estágio**. (Se não estiver listados, você pode não ter iniciado com a solução correta Visual Studio. Inicie com BeforeContentType.sln.) Ao terminar a grade deve parecer com o seguinte:
    
   **Guia de colunas**

  

     ![A guia Colunas do Designer de Tipo de Conteúdo com "Funcionário", "Divisão" e "Estágio de Orientação" listados na grade.](images/835e78b3-a073-45b2-b4ee-3f9be9d88495.PNG)
  

    
    
  
9. Salve o arquivo e feche o designer.
    
  
10. A próxima etapa exige que você trabalhe diretamente no XML bruto para o tipo de conteúdo, portanto no **Solution Explorer**, escolha filho do tipo de conteúdo **NewEmployee** arquivo Elements. XML.
    
  
11. Já existem **FieldRef** elementos no arquivo para as duas colunas que você adicionou. Adicione elementos de **FieldRef** para duas colunas internas SharePoint como peers dos dois que já estão lá. A seguir está a marcação para os elementos. *Você deve usar esses mesmos GUIDs para o atributo ID como esses são os tipos de campo interno com IDs fixas.*  Adicione esses elementos *acima*  os dois **FieldRef** das colunas de site personalizadas.
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Employee" />
  ```


    Observe que podemos concedeu esses campos um nome de exibição personalizado: **funcionário**.
    
  
12. Salve e feche o arquivo.
    
  
13. Expanda o nó de **lista** no **Solution Explorer** e escolha **NewEmployeeOrientation** para abrir o designer de tipo de lista.
    
  
14. Abra a guia **colunas** no designer e escolha o botão de **Tipos de conteúdo**.
    
  
15. Na caixa de diálogo **Configurações de tipo de conteúdo**, adicione o tipo de conteúdo **NewEmployee**.
    
  
16. Escolha o tipo de conteúdo **NewEmployee** na lista de tipos e escolha o botão **Definir como padrão**.
    
  
17. Escolha o tipo de conteúdo do **Item**, clique com o botão de seta pequena que aparece à esquerda do nome do tipo de conteúdo e escolha **Excluir**.
    
  
18. Repita a etapa anterior para o tipo de conteúdo de **pasta**, portanto, **NewEmployee** é o tipo de conteúdo somente listado. A caixa de diálogo agora deve parecer com o seguinte:
    
   **Caixa de diálogo Configurações de tipo conteúda**

  

     ![A caixa de diálogo Configurações de Tipo de Conteúdo com somente um tipo de conteúdo, nomeado NewEmployee, listado.](images/b90699f4-40de-4f50-ad47-3e8773d0eb92.PNG)
  

    
    
  
19. Escolha **OK** para fechar a caixa de diálogo e, em seguida, salve e feche o arquivo.
    
  
20. Abra o arquivo schema.xml.
    
  
21. Encontre o elemento **Fields**. Deve haver três elementos **Field**: **Title**, divisãoe OrientationStage. (Esses elementos podem estar em uma única linha neste arquivo gerado. Em caso afirmativo, separe-os com quebras de linha.)
    
  
22. Deixe o arquivo aberto no **Solution Explorer**, expanda a pasta de **Colunas de Site** e o nó dedivisão e abra o arquivo Elements. XML paradivisão. O elemento de **Field** paradivisão em Schema. XML exatamente deve duplicar o elemento **Field** no Elements adivisão . Se não houver uma correspondência exata, copie o elemento **Field** do arquivo Elements. XML da coluna do site e colá-lo no lugar do elemento incompatíveis **Field** no arquivo Schema. XML. Feche o arquivo element.xml.
    
  
23. Abra o arquivo Elements XML para OrientationStage. Aqui, também, deve haver uma correspondência exata dos elementos **Field** os dois arquivos paraOrientationStage, incluindo todos os elementos filho, como os elementos **CHOICES** e **MAPPINGS**. Se não houver, copie o **Field** no arquivo Elements XML e colá-lo no lugar do elemento incompatíveis **Field** no arquivo Schema. XML. Feche o arquivo element.xml.
    
  
24. Ainda no arquivo Schema. XML, no elemento **View** cujo valor de **BaseViewID** é "1", encontre filho elemento **ViewFields** e adicione os seguintes elementos **FieldRef** dois como filhos dele. Já pode estar lá, mas não tem um atributo **ID**. Em caso afirmativo, adicione o atributo ID.
    
  ```
  
<FieldRef Name="Division" ID="{GUID from the Field element}" />
<FieldRef Name="OrientationStage" ID="{GUID from the Field element}" />

  ```

25. Substitua os dois valores de atributo do espaço reservado **ID** os GUIDs dos elementos **Field** correspondentes no elemento **ContentType** para **NewEmployee** que é anterior no arquivo Schema. XML. Não se esqueça que de enquadramento chaves "{}".
    
    O **ViewFields** para o "1" **View** deve se parecer com isso. (Seus GUIDs podem ser diferentes).
    


  ```
  
<ViewFields>
   <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Employee" />
   <FieldRef Name="Division" ID="{509d2d67-9a96-4596-9b3b-58449cdcc6ff}" />
   <FieldRef Name="OrientationStage" ID="{38a3b54c-acf3-4ddf-b748-55c7c28d4cc2}" />        
</ViewFields>
  ```

26. Ainda no arquivo Schema. XML, localize o elemento **View** cujo valor de **BaseViewID** é "0". Encontre o elemento **ViewFields** com nela.
    
  
27. Copie todo da seção **ViewFields** do modo de exibição "1" sobre a seção **ViewFields** do modo de exibição "0". Os dois modos de exibição agora devem ter idênticos **ViewFields** seções.
    
  
28. Salve e feche o arquivo schema.xml.
    
  
29. Na **lista** pasta, expanda o nó **NewEmployeeOrientation** e sua instância de lista filho **NewEmployeesInSeattle**. Você deve ser capaz de ver claramente e distinguir o Elements. XML para o modelo do Elements. XML para a instância. Abra o para a instância.
    
  
30. Adicione dois elementos **Field** para o primeiro elemento **Row**, para que o elemento **Row** é semelhante ao seguinte.
    
  ```
  
<Row>
  <Field Name="Title">Tom Higginbotham</Field>
  <Field Name="Division">Manufacturing</Field>
  <Field Name="OrientationStage">Tour of building</Field>
</Row>
   
  ```

31. Salve e feche o arquivo.
    
  

## Executar e testar o suplemento


  
    
    

1. Use a tecla F5 para implantar e executar seu suplemento Visual Studio faz uma instalação temporária do add-in no seu site do SharePoint de teste e o executa imediatamente o add-in.
    
  
2. Quando a página padrão do suplemento for exibida, escolha o link **Novos Funcionários em Campinas** para abrir a instância da lista personalizada.
    
  
3. Abre a página de lista e as colunas de divisão eOrientationStage estão contidas nela. Não é necessário para um usuário para adicioná-los manualmente, porque eles fazem parte da lista Tipo de conteúdo. O item superior tem os dados que você adicionou.
    
   **Novos funcionários na lista de Seattle**

  

     ![A lista "Novos Funcionários em Seattle" com as colunas Divisão e Estágio de Orientação já presentes.](images/b654af45-663e-425c-b7c7-b8b5524cb316.PNG)
  

    
    
  
4. Tente adicionar novos itens à lista e edição de itens existentes.
    
  
5. Para encerrar a sessão de depuração, feche a janela do navegador ou interrompa a depuração no Visual Studio. Sempre que você pressiona F5, o Visual Studio retira a versão anterior do suplemento e instala a última mais recente.
    
  
6. Você vai trabalhar com esse suplemento e com a solução do Visual Studio em outros artigos. Além disso, é uma prática recomendada retirar o suplemento uma última vez, quando deixar de trabalhar com ele por algum tempo. Clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Retirar**.
    
  

## 
<a name="Nextsteps"> </a>

O próximo artigo desta série, você adicionará uma Web Part de exibição de lista à página padrão do Suplemento do SharePoint:  [Adicionar uma Web Part a uma página em um SharePoint hospedado SharePoint Add-in](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md).
  
    
    

