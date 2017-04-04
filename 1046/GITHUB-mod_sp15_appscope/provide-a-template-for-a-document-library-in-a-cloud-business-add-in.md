---
title: Fornecer um modelo para uma biblioteca de documentos em um suplemento de negócios nuvem
ms.prod: SHAREPOINT
ms.assetid: 78df5ac7-a832-4fd0-89dc-9e440eefd7c8
---


# Fornecer um modelo para uma biblioteca de documentos em um suplemento de negócios nuvem
Além dos modelos do Office que estão disponíveis quando você adiciona um documento a uma biblioteca de documentos do SharePoint, você pode fornecer seus próprios modelos. Por exemplo, você pode ter seu próprio modelo de cotação de vendas que você deseja usar quando novos pedidos são adicionados.
## 

Se você ainda não tiver feito isso, associe uma biblioteca de documentos com seu suplemento de negócios nuvem. Consulte  [Associar uma biblioteca de documentos uma entidade](associate-a-document-library-with-an-entity.md).
  
    
    

### Para adicionar um modelo


1. Vá para o site de desenvolvedor do SharePoint e na página de **desenvolvedor**, escolha **Conteúdo do Site**.
    
  
2. Na página **Conteúdo do Site**, escolha **configurações**, conforme mostrado na Figura 1.
    
   **Figura 1. O link de configurações**

  

     ![Link Configurações do Site](images/CBA_IM_8b.PNG)
  

  

  
3. Na página **Configurações do Site**, na lista **Galerias de Web Designer**, escolha de **tipos de conteúdo do Site**, conforme mostrado na Figura 2.
    
   **Figura 2. O link de tipos de conteúdo de Site**

  

     ![Link de tipos de conteúdo de sites](images/CBA_IM_26.PNG)
  

  

  
4. Na página **Tipos de conteúdo do Site**, escolha **criar**, conforme mostrado na Figura 3.
    
   **Figura 3. O link criar**

  

     ![Criar link](images/CBA_IM_27.PNG)
  

  

  
5. Na página **Novo tipo de conteúdo do Site**, insira um nome e uma descrição para o modelo. Para o **Tipo de conteúdo pai**, escolha os **Tipos de conteúdo de documento** e o **documento**, conforme mostrado na Figura 4.
    
   **Figura 4. Seleções de tipo de conteúdo pai**

  

     ![Seleções de tipo de conteúdo pai](images/CBA_IM_28.PNG)
  

  

  
6. Na seção **grupo**, na lista de **grupo existente**, escolha **Os tipos de conteúdo de documento**, conforme mostrado na Figura 5 e escolha **OK**.
    
   **Figura 5. Configuração de grupo**

  

     ![Configuração de grupo](images/CBA_IM_28a.PNG)
  

  

  
7. Na página **Tipo de conteúdo do Site**, escolha **Configurações avançadas**.
    
  
8. Na página **Configurações avançadas**, insira a URL de um modelo de documento existente ou carregar um novo modelo de documento, conforme mostrado na Figura 6 e escolha **OK**.
    
   **Figura 6. Especificar o modelo de documento**

  

     ![Especificar modelo de documento](images/CBA_IM_29.PNG)
  

  

  
9. Vá para a página de **Conteúdo do Site** e escolha sua biblioteca de documentos e, em seguida, vá para a página de **configurações**.
    
  
10. Na página **configurações**, escolha **Adicionar de tipos de conteúdo de site existentes**.
    
  
11. Na página **Adicionar tipos de conteúdo**, adicione seu modelo, como mostrado na Figura 7 e escolha **OK**.
    
   **Figura 7. Adicionando o modelo**

  

     ![Adicionando o modelo](images/CBA_IM_29a.PNG)
  

  

  
12. Execute o add-in e adicionar um documento. Você deve ver seu modelo na caixa de diálogo **criar um novo arquivo**, conforme mostrado na Figura 8.
    
   **Figura 8. Criar uma nova caixa de diálogo de arquivo com o novo modelo**

  

     ![Caixa de diálogo Criar novo arquivo com o novo modelo](images/CBA_IM_30.PNG)
  

  

  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Desenvolver suplementos de negócios de nuvem](develop-cloud-business-add-ins.md)
    
  
-  [Associar uma biblioteca de documentos uma entidade](associate-a-document-library-with-an-entity.md)
    
  

