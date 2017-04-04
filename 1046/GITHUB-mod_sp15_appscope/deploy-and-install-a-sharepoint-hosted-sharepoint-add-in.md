---
title: Implantar e instalar um hospedado no SharePoint SharePoint Add-in
ms.prod: SHAREPOINT
ms.assetid: 3a3875d6-13e3-4411-904f-4f39acf88b95
---


# Implantar e instalar um hospedado no SharePoint SharePoint Add-in
Saiba como Suplementos do SharePoint são implantados e instalado.
Este é o segundo em uma série de artigos sobre noções básicas do desenvolvimento hospedado no SharePoint Suplementos do SharePoint. Você primeiro deve estar familiarizado com o tópico  [Suplementos do SharePoint](sharepoint-add-ins.md) e os tópicos anteriores na série:
  
    
    


-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

> **OBSERVAçãO**
> Se você tiver trabalhado através desta série sobre hospedado no SharePoint suplementos, então você possui uma solução de Visual Studio que você pode usar para continuar com este tópico. Você também pode baixar o repositório em  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) e abra o arquivo BeforeColumns.sln.
  
    
    

Você encontrará muito mais fácil desenvolver hospedado no SharePoint Suplementos do SharePoint se você está familiarizado com como usuários implantar e instalar seus suplementos. Portanto, neste artigo, nós vai fazer uma breve interrupção da codificação para criar e usar um catálogo de suplemento e, em seguida, instalar o suplemento que você tiver trabalhado no.
## Criar um catálogo de suplemento


  
    
    

1. Login à sua assinatura Office 365 como administrador. Escolha o ícone de suplemento iniciador e escolha o add-in de **Administração**.
    
   **Iniciador de suplemento do Office 365**

  

     ![Inicializador de aplicativos do Office 365](images/ec60797c-d329-4922-a811-70c64598f4d5.PNG)
  

    
    
  
2. No **Centro de administração**, expanda o nó de **Administração** no painel de tarefas e, em seguida, escolha **SharePoint**.
    
  
3. No **Centro de administração do SharePoint**, escolha **suplementos** no painel de tarefas.
    
  
4. Na página **suplementos**, escolha o **suplemento do catálogo**. (Se já houver um conjunto de sites do catálogo de suplemento na assinatura, ele será aberto e terminar. Você não pode criar mais de um suplemento de catálogo em uma assinatura.)
    
  
5. Na página **Add-in do Site de catálogo**, escolha **OK** para aceitar a opção padrão e criar um novo site de catálogo do suplemento.
    
  
6. Na caixa de diálogo **Criar Suplemento de conjunto de sites do catálogo**, especifique o endereço de título e o site da web do seu site de catálogo do suplemento. Recomendamos que você inclua "catalog" no título e a URL para torná-la fácil de lembrar e distinta no **Centro de administração do SharePoint**.
    
  
7. Especificar um **fuso horário** e manter-se como **administrador**.
    
  
8. Definir a **Cota de armazenamento** para o menor valor possível (atualmente 110, mas que pode ser alterada), pois os pacotes de suplemento você carregar para este conjunto de sites são muito pequenos.
    
  
9. Definir a **Cota de recursos do servidor** como 0 (zero) e escolha **OK**. (A cota de recursos do servidor está relacionada à limitação de forma inadequada executando soluçăos em área restrita, mas não instalar qualquer soluçăos em área restrita em seu site de catálogo do suplemento).
    
  
Como o conjunto de sites está sendo criado, SharePoint levará de volta para o **Centro de administração do SharePoint**. Após alguns minutos, você verá que o conjunto foi criado.
## Embalar o suplemento e carregá-la ao catálogo


  
    
    

1. Abra a solução Visual Studio e, em seguida, clique com botão direito no nó do projeto no **Solution Explorer**. Escolha **Publicar**.
    
  
2. No painel **Publicar**, escolha o **pacote o add-in**. O suplemento é empacotado e salvo como arquivo *.app na pasta de \\bin\\debug\\web.publish\\1.0.0.0 da solução.
    
  
3. Abra o seu site do suplemento do catálogo em um navegador e escolha **Suplementos do SharePoint** na barra de navegação.
    
  
4. O catálogo de **Suplementos do SharePoint** é uma biblioteca de ativos do SharePoint padrão. Carregar o suplemento de pacote para ele usando qualquer um dos métodos de carregamento de arquivos para bibliotecas do SharePoint.
    
  

## Instalar o suplemento, assim como os usuários finais


1. Navegue até a qualquer site na assinatura do SharePoint Online e abra a página de **Conteúdo do Site**.
    
  
2. Escolha **Adicionar um suplemento** para abrir a página de **seus suplementos**.
    
  
3. Encontre o suplemento de **Orientação do funcionário** na seção **Add-ins, você pode adicionar** e clique em seu lado a lado.
    
  
4. Escolha **Confiar** na caixa de diálogo consentimento. Página **Conteúdo** do Site é aberto automaticamente e o suplemento é exibida com um notação que estiver instalando. Depois que ele instala, os usuários podem escolher a lado a lado para executar o suplemento.
    
  

## Remover o suplemento

Para poder continuar aprimorando o mesmo Suplemento do SharePoint no Visual Studio (consulte  [próximas etapas](#Nextsteps)), o remove o suplemento com estas etapas:
  
    
    

1. Na página **Conteúdo do Site**, mova o cursor sobre o suplemento para que o texto explicativo botão **…** aparece.
    
  
2. Escolha o botão de texto explicativo e escolha **Remover** no texto explicativo.
    
  
3. Navegue de volta para seu site de catálogo de suplemento e escolha **Suplementos do SharePoint** na barra de navegação.
    
  
4. Realce o suplemento e escolha **Gerenciar** na barra de tarefas, logo acima da lista e clique em **Excluir** no menu gerenciar.
    
  

## 

É altamente recomendável que você continua com esta série sobre hospedado no SharePoint suplementos antes de prosseguir para os tópicos mais avançados. Próximo que recebemos de volta para a codificação em  [Adicionar colunas personalizadas para um suplemento do SharePoint-hostedSharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md).
  
    
    

