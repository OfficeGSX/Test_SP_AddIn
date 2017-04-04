---
title: Publique um suplemento de negócios nuvem no SharePoint
ms.prod: SHAREPOINT
ms.assetid: b528a23f-72c3-42cb-8265-1c5b9238d404
---


# Publique um suplemento de negócios nuvem no SharePoint
Você pode publicar seu suplemento de negócios nuvem como um provedor hospedado SharePoint suplemento. Um suplemento hospedado em provedor proporciona a flexibilidade da implantação do seu aplicativo web e o banco de dados para um site do SharePoint no local, para o Microsoft Azure ou para um site de hospedagem de terceiros. Depois de publicar o add-in, outras pessoas podem executá-lo do SharePoint em seus computadores e dispositivos móveis.Você pode publicar seu suplemento diretamente em um site usando WebDeploy ou você pode criar um pacote para seu suplemento que pode ser implantado em vários servidores.
## Para publicar um suplemento
<a name="publish"> </a>


1. No **Solution Explorer**, abra o menu de atalho para o nó de nível superior do aplicativo, conforme mostrado na Figura 1 e clique em **Publicar**.
    
   **Figura 1. O nó de nível superior**

  

     ![Nó IncidentManager](images/CBA_IM_18.PNG)
  

  

  
2. No LightSwitch publicar Assistente do aplicativo, na página de **Opções do SharePoint**, escolha o botão de opção **hospedado em provedor** e escolha **Avançar**.
    
  
3. Na página **Configuração do servidor de aplicativo**, escolha o botão de opção do **Servidor IIS** e escolha **Avançar**.
    
    > **OBSERVAçãO**
      > Se você tiver um publicar configurações publishsettings arquivo (ou .pubxml que foi criado para outro suplemento), você pode usar esse arquivo para fornecer o restante das informações que você precisa para publicação. Em caso afirmativo, escolha o botão de **Definições de importação** no assistente.
4. Na página **Publicar saída**, escolha o botão de opção **publicar diretamente a um servidor agora** e escolha **Avançar**.
    
  
5. Na página **Configurações de publicação**, na caixa de texto **URL do serviço**, insira a URL para o servidor onde você deseja publicar seu suplemento.
    
    Se você estiver publicando em uma empresa de hospedagem, a empresa fornece esse valor. Ele pode estar em qualquer um dos seguintes formatos:
    
  -  _HostingCompanyURL_ (por exemplo, `contoso.com`)
    
  
  -  `https://` _HostingCompanyURL_ (por exemplo, `https://contoso.com`)
    
  
  -  `https://` _HostingCompanyURL_ `:8172/msdeploy.axd` (por exemplo, `https://contoso.com:8172/msdeploy.axd`)
    
  

    Se você estiver publicando para serviços de informações da Internet (IIS) no seu computador de teste, digite  `localhost` ou o nome do seu computador.
    
    Se você estiver publicando em um servidor em sua própria rede, insira uma dessas URLs:
    
  -  `http://` _ServerName_
    
  
  -  `http://` _ServerName_ `/msdeployagentservice`
    
  

    > **OBSERVAçãO**
      > Se você estiver publicando através de um firewall, você pode precisar abrir a porta 8172.
6. Na caixa de texto de **aplicativo do Site**, insira os nomes de site do IIS e o add-in.
    
    Se você estiver publicando em uma empresa de hospedagem, a empresa fornece esse valor. Normalmente, ele é um nome de domínio (por exemplo,  `contoso.com`) ou um nome de domínio e o suplemento (por exemplo,  `contoso.com/MyApp`).
    
    Se você estiver publicando ao IIS no seu computador de teste ou estiver publicando em um servidor na sua rede interna, insira o site e o nome do suplemento que eles aparecem no Gerenciador do IIS. Por exemplo, se você estiver publicando o suplemento MyApp ao site padrão no IIS, insira o Padrão de Site da Web/MyApp.
    
    > **OBSERVAçãO**
      > Se você estiver publicando em uma pasta da web existente e deseja remover qualquer conteúdo existente, marque a caixa de seleção **Remover arquivos adicionais no destino**.
7. Nas caixas de texto **Nome de usuário** e **senha**, insira credenciais para uma conta que tenha autoridade suficiente para executar tarefas de implantação no servidor de web de destino e escolha **Avançar**.
    
    Se você estiver publicando em uma empresa de hospedagem, a empresa fornece esses valores.
    
  
8. Na página **Configurações de segurança**, escolha o botão de opção **Sim, os usuários devem se conectar usando HTTPS** e escolha **Avançar**.
    
  
9. Na página **conexões de dados**, na guia **Conexões do banco de dados**, insira as cadeias de caracteres de conexão do administrador e usuário para o servidor de banco de dados onde você deseja publicar banco de dados do seu suplemento.
    
    > **OBSERVAçãO**
      > O banco de dados não deve estar localizado no servidor onde você estiver publicando o add-in.
10. Na guia **Fontes de dados anexado**, atualize as cadeias de caracteres de conexão para todas as conexões adicionais conforme o necessário e escolha **Avançar**.
    
  
11. Na página do **Provedor de hospedagem**, na caixa de texto **onde o seu aplicativo do LightSwitch está hospedado**, insira a URL completa para o add-in.
    
    Na maioria dos casos, essa URL será que o mesmo que a **URL do serviço** e o **aplicativo do Site** valores que você inseriu anteriormente (por exemplo, `https://contoso.com/MyApplication`).
    
  
12. Insira os valores de **ID de cliente** e **Segredo do cliente** para o add-in.
    
    Você pode obter esses valores da página **appregnew** do seu site do SharePoint ou do painel do vendedor. Consulte as [diretrizes para registrar um Add-ins sharepoint2013](http://msdn.microsoft.com/en-us/library/office/jj687469%28v=office.15%29.aspx).
    
  
13. Escolha **Publicar** para publicar o add-in.
    
    Quando seu suplemento é publicado, o **Gerenciador de arquivos** abre e exibe o diretório de **publicação** para o seu projeto.
    
  

## Empacotar um suplemento
<a name="package"> </a>


1. No **Solution Explorer**, abra o menu de atalho para o nó de nível superior do aplicativo, conforme mostrado na Figura 1 e clique em **Publicar**.
    
   **Figura 1. O nó de nível superior**

  

     ![Nó IncidentManager](images/CBA_IM_18.PNG)
  

    
    
  
2. No LightSwitch publicar Assistente do aplicativo, na página de **Opções do SharePoint**, escolha o botão de opção **hospedado em provedor** e escolha **Avançar**.
    
  
3. Na página **Configuração do servidor de aplicativo**, escolha o botão de opção do **Servidor IIS** e escolha **Avançar**.
    
    > **OBSERVAçãO**
      > Se você tiver um publicar configurações publishsettings arquivo (ou .pubxml que foi criado para outro suplemento), você pode usar esse arquivo para fornecer o restante das informações que você precisa para publicação. Em caso afirmativo, escolha o botão de **Definições de importação** no assistente.
4. Na página **Publicar saída**, escolha o botão de opção de **criar um pacote do disco** e escolha **Avançar**.
    
  
5. Na página **Configurações de publicação**, no **o que deve site ser nomeado?** texto, digite um nome para o site.
    
    O nome padrão é o nome do suplemento.
    
  
6. No **deve onde o pacote ser criado?** caixa de texto, digite o caminho para o local onde você deseja que a saída para serem publicados e escolha **Avançar**.
    
    O local padrão é a subpasta de publicar em seu diretório de projeto.
    
  
7. Na página **Configurações de segurança**, escolha o botão de opção **Sim, os usuários devem se conectar usando HTTPS** e escolha **Avançar**.
    
  
8. Na página **Configuração do banco de dados**, escolha o botão de opção de **gerar um novo banco de dados chamado** e insira o nome do suplemento como o nome do banco de dados.
    
  
9. Escolha a guia **Fontes de dados anexado** e atualize as cadeias de caracteres de conexão para todas as conexões adicionais conforme o necessário e escolha **Avançar**.
    
  
10. Na página do **Provedor de hospedagem**, na caixa de texto **onde o seu aplicativo do LightSwitch está hospedado**, insira a URL completa para o add-in.
    
    Na maioria dos casos, essa URL será que o mesmo que a **URL do serviço** e o **aplicativo do Site** valores que você inseriu anteriormente (por exemplo `https://contoso.com/MyApplication`).
    
  
11. Insira a **ID de cliente** e o **Segredo do cliente** para o add-in.
    
    Você pode obter esses valores da página **appregnew** do seu site do SharePoint ou do painel do vendedor. Consulte as [diretrizes para registrar um Add-ins sharepoint2013](http://msdn.microsoft.com/en-us/library/office/jj687469%28v=office.15%29.aspx).
    
  
12. Escolha **Publicar** para publicar o add-in.
    
    Quando seu suplemento é publicado, um arquivo. zip que contém o pacote é colocado no diretório que você especificou na etapa 4. Depois que esse pacote tiver sido criado, um administrador de servidor pode usar a ferramenta MSDeploy para implantar seu suplemento para servidores que estão executando o IIS e o SQL Server.
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md)
    
  
-  [Publicar os suplementos de negócios de nuvem](publish-cloud-business-add-ins.md)
    
  

