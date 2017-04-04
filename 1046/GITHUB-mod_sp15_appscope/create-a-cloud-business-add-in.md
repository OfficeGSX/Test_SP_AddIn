---
title: Criar um suplemento de negócios nuvem
ms.prod: SHAREPOINT
ms.assetid: a42a0e53-5d19-4f0e-b271-23a01acc4946
---


# Criar um suplemento de negócios nuvem
Usando o modelo de suplemento de negócios de nuvem no Visual Studio, você pode criar suplementos de SharePoint 2013 ou SharePoint no Office 365 otimizados para adição e o gerenciamento de dados.
> [!OBSERVAçãO]
> Você também pode criar um Suplemento do SharePoint usando o suplemento para o modelo do SharePoint 2013.
  
    
    


### Para criar um suplemento de negócios nuvem


1. Na barra de menus, escolha **arquivo**, **novo** **projeto**.
    
    Abre a caixa de diálogo **Novo projeto**.
    
  
2. Na lista de modelos, expanda o nó do **Visual Basic** ou **Visual c#**, expanda o nó **Office/SharePoint**, escolha o nó de **suplementos** e escolha **o suplemento de nuvem comercial**, conforme mostrado na Figura 1.
    
   **Figura 1. Modelo de suplemento de negócios da nuvem**

  

     ![Template for creating a Cloud Business App](images/CloudBusinessApptemplate.PNG)
  

  

  
3. Na caixa de texto **nome**, digite um nome para o seu projeto e escolha o botão **OK**.
    
    O Assistente de **suplemento novos negócios de nuvem** abre.
    
  
4. No Assistente de **suplemento novos negócios de nuvem**, digite a URL do Site do seu servidor do SharePoint ou o seu site do desenvolvedor do Office 365, conforme mostrado na Figura 2 e escolha o botão **Concluir**.
    
   **Figura 2. URL do SharePoint**

  

     ![SharePoint URL](images/SiteURL.PNG)
  

    A URL deve levar o formulário https://  _MySite_.sharepoint.com/sites/Developer/.
    
    Uma nova solução é adicionada ao Solution Explorer com quatro projetos: um projeto de nível superior, um projeto **HTMLClient**, um projeto do **servidor** e um projeto **do SharePoint**.
    
  

### Para alterar o site para um suplemento de negócios nuvem


1. No **Solution Explorer**, abra o menu de atalho para o nó de nível superior do projeto e escolha **Propriedades**, conforme mostrado na Figura 3.
    
   **Figura 3. No nó do projeto de nível superior**

  

     ![The top level project node](images/Top-levelprojectnode.PNG)
  

    O designer de aplicativos é aberta.
    
  
2. No designer de aplicativo, escolha a guia **SharePoint**, conforme mostrado na Figura 4.
    
   **Figura 4. A guia do SharePoint**

  

     ![The SharePoint properties tab](images/SharePointtab.PNG)
  

  

  
3. Na lista **URL do Site**, escolha uma URL existente ou insira a URL do Site para o seu servidor do SharePoint ou o seu site do desenvolvedor do Office 365.
    
  
4. Escolha o botão **Validar** para verificar a URL.
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Desenvolver suplementos de negócios de nuvem](develop-cloud-business-add-ins.md)
    
  
-  [Criar suplementos de negócios de nuvem](create-cloud-business-add-ins.md)
    
  

