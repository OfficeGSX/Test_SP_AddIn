---
title: Habilitar um News feed para um suplemento de negócios nuvem
ms.prod: SHAREPOINT
ms.assetid: 3eec1f65-addb-4bfa-940c-dae3ac1e0c8a
---


# Habilitar um News feed para um suplemento de negócios nuvem
Recursos de colaboração e social no SharePoint para o Office 365 permitem aos usuários rastrear atividades em uma lista e adicionar comentários. Você pode criar facilmente um News feed para seu suplemento Business Cloud habilitando algumas propriedades.
## Pré-requisitos

Para hospedar o news feed, você precisará de um site do desenvolvedor do SharePoint no Office 365, que você pode obter do  [Inscreva-se para um Site do desenvolvedor do Office 365](http://go.microsoft.com/fwlink/?LinkId=263490).
  
    
    

## Procedimentos


### Para habilitar um News feed


1. No Solution Explorer, abra a entidade que representa a lista onde deseja adicionar um News feed e, em seguida, na barra de ferramentas **perspectiva**, escolha a guia **servidor**.
    
  
2. Na janela **Propriedades**, marque as caixas de seleção **postar quando criado** e/ou **postar quando atualizado**.
    
     ![Social properties](images/CBAsocial.PNG)
  

    **Postar quando criado** adiciona um thread de News feed para cada novo item de lista. **Postar quando atualizado** adiciona um segmento quando o valor de um item na lista é alterado. POST gatilhos determinam quais campos no item irá disparar uma postagem.
    
  
3. Escolha o link **Escolher postar gatilhos**.
    
    A caixa de diálogo **Choose postar gatilhos** é exibida.
    
  
4. Na caixa de diálogo **Choose postar gatilhos**, marque as caixas de seleção para todos os campos que você deseja disparar uma postagem e escolha o botão **OK**.
    
    Um único segmento será criado para todas as alterações em um item, não importa quantos campos que você escolher.
    
  

### Para acessar um News feed


1. Na barra de menus, escolha **Depurar**, **Iniciar depuração** para executar o aplicativo.
    
  
2. No aplicativo em execução, abra a tela de navegar para a entidade que representa a lista em que você adicionou um News feed. Se você habilitou **postar quando criado**, adicione um novo item. Se você habilitou **postar quando atualizado**, edite os campos que você selecionou na caixa de diálogo **Choose postar gatilhos**.
    
  
3. Na barra de cromo do SharePoint, escolha o link do **news feed**.
    
     ![The SharePoint chrome bar](images/CBAnewsfeed.PNG)
  

    A página **news feed** é aberta em uma nova janela de navegador com entradas para os itens adicionados e/ou atualizados. Você pode escolher no link **como** uma postagem ou você pode escolher o link de **resposta** para adicionar um comentário.
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Desenvolver suplementos de negócios de nuvem](develop-cloud-business-add-ins.md)
    
  
-  [Recursos de colaboração e social no SharePoint 2013](http://msdn.microsoft.com/en-us/library/office/jj163280.aspx)
    
  
-  [Criar um suplemento de negócios nuvem com um News feed social](create-a-cloud-business-add-in-with-a-social-newsfeed.md)
    
  

