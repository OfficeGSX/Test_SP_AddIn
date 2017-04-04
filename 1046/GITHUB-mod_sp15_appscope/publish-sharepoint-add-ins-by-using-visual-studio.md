---
title: Publicar suplementos do SharePoint usando o Visual Studio
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# Publicar suplementos do SharePoint usando o Visual Studio
Aprenda a publicar suas Suplemento do SharePoint usando Microsoft Visual Studio 2013 ou Visual Studio 2012. Se o suplemento tiver um aplicativo web associado, implantá-lo primeiro. Em seguida, para todos os Suplementos do SharePoint, você empacotar o Suplemento do SharePoint e depois publicá-lo. Você também pode optar por enviar seu suplemento para inclusão na Office Store.
## Pré-requisitos
<a name="Prereq"> </a>


  
    
    

-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284  )
    
    -ou-
    
     [O Visual Studio 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30682) e o Office Developer Tools for Visual Studio. Para baixar as ferramentas, consulte "Ferramentas" na [página de Download](http://go.microsoft.com/fwlink/?LinkId=234393). (O novo Gerenciador de publicar não está disponível no Visual Studio 2012 ou versões anteriores.)
    
  
- Microsoft SharePoint 2013
    
  

## Publicar usando Visual Studio 2013
<a name="VS2013"> </a>

Se seu provedor hospedado Suplemento do SharePoint tiver um aplicativo web, implantar os arquivos-lo primeiro. Você, em seguida, para todos os Suplementos do SharePoint, empacotar o Suplemento do SharePoint e publicá-lo.
  
    
    

> [!IMPORTANTE]
> Para garantir que sua ID de cliente do SharePoint e o segredo cliente valores obtém publicados com seu projeto da web, que permite que o conteúdo da web acessar dados do SharePoint, publicar seu projeto Suplemento do SharePoint na página **publicar seu suplemento**. Você pode acessar esta página abrindo o menu de atalho para o Suplemento do SharePoint, não o menu de atalho para o aplicativo da web, e, em seguida, escolhendo o comando **Publicar**.
  
    
    


### Etapa 1: Implantar o aplicativo da web

Seu Suplemento do SharePoint normalmente tem um aplicativo web host associado que você precisa implantar em um servidor web. Para obter mais informações sobre como usar o Assistente de publicar Web, consulte  [como: implantar um projeto da Web usando a publicação no clique no Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).
  
    
    

### Para abrir a publicar sua página de suplemento


- No **Solution Explorer**, abra o menu de atalho para o projeto Suplemento do SharePoint e escolha **Publicar**.
    
    A página de **publicar seu suplemento** aparece.
    
  

### Selecione ou crie um perfil


- Na lista de **perfil atual**, escolha um perfil para importar ou escolha **< New... >** para criar um perfil.
    
    Um perfil de publicação Especifica o servidor ao qual você está implantando o web app, as credenciais que são necessárias para fazer logon no servidor, os bancos de dados para implantar (se aplicável) e outras opções de implantação. Você pode criar diferentes perfis para atender às suas necessidades de publicação. Por exemplo, você pode criar um perfil para testar e outro perfil para publicação.
    
    Se você escolher **< … Novo >**, o Assistente de **publicação de criar perfil** aparece. Você pode usar esse assistente para importar um perfil de publicação de um provedor de hospedagem de site, como Azure, ou para criar um perfil e adicionar manualmente o nome do seu servidor, as credenciais e outras configurações. Se você criou um novo perfil em vez de importado um perfil existente, você precisará fornecer cliente Id do cliente secretos valores e, conforme descrito em [diretrizes para registrar o SharePoint 2013 de suplementos](http://msdn.microsoft.com/library/jj687469.aspx) e [como: criar IDs de cliente e senhas no painel de vendedor Microsoft](http://msdn.microsoft.com/library/office/jj220036.aspx).
    
    Se você planeja enviar seu Suplemento do SharePoint para o Office Store, certifique-se de usar ID do cliente e valores de secreta do cliente que são criados no painel vendedor. Você pode usar as IDs de cliente e os valores de secreta de cliente que você gerar usando a página de appregnew.aspx durante a fase de desenvolvimento, mas suplementos enviados para a Office Store devem usar IDs de cliente e senhas dos clientes que obteve de painel vendedor. Além disso, você deve criar o perfil de publicação no seu site de Azure e importe-os para Visual Studio, em vez de criar um perfil no assistente **Criar perfil de publicação**. Quando você cria um perfil no Azure, todas as configurações na guia **conexão** são fornecidas em Visual Studio. Para saber mais sobre como importar ou criar um perfil de publicação, consulte [criar um perfil publicar](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile).
    
    > [!DICA]
      > Se você não pode publicar conteúdo da web diretamente, você pode criar uma web implantar pacote que um administrador pode implantar na Web para você. Para criar uma web implantar pacote, criar um novo perfil, escolha a guia **conexão** e em seguida, escolha o **Pacote de implantação da Web** na lista **método de publicação**.

### Para implantar o seu projeto de aplicativo web


1. Na página **publicar seu suplemento**, escolha o botão de **implantar seu projeto da web**.
    
    Caixa de diálogo **Publicar Web** é exibida.
    
  
2. Nas **configurações** de **conexão** e guias, preencha todos os valores ausentes.
    
    Para alterar como os arquivos para seu Suplemento do SharePoint são publicados ou se o suplemento usa um banco de dados externo, escolha a guia **configurações**. Consulte a seção "Configurando o guia de configurações" no  [como: implantar um projeto da Web usando a publicação no clique no Visual Studio](http://msdn.microsoft.com/library/dd465337.aspx).
    
  
3. Para revisar quais itens serão alteradas quando o aplicativo web é implantado, escolha o botão **Iniciar visualização** na guia **Visualizar**.
    
  
4. Escolha o botão **Publicar** para implantar o projeto de aplicativo da web.
    
  

### Etapa 2: Empacotar o suplemento


1. Na página **publicar o add-in**, escolha o botão **pacote do suplemento**.
    
    O assistente **Publicar Office e SharePoint suplementos** aparece.
    
  
2. No **onde seu site está hospedado?** texto, digite a URL do site que hospedará os arquivos de conteúdo de sua Suplemento do SharePoint.
    
    Você deve especificar um endereço que começa com o prefixo "https". Consulte  [por que meus suplementos precisa ser protegida por SSL?](http://msdn.microsoft.com/library/jj591603#bk_q7).
    
    > [!OBSERVAçãO]
      > sites de Azure automaticamente fornecem um ponto de extremidade https. Se você publicar seu suplemento em um site de Office Store ou o Office Store, o endereço deve começar com um prefixo https. No entanto, se você publicar o suplemento para um site local, você pode usar um prefixo http.

    No **o que é ID do cliente do suplemento?** caixa de texto, a ID do cliente que você inseriu no perfil de publicação já deverá aparecer.
    
    Se você usou um valor de espaço reservado para a identificação de cliente até esse ponto, você deve adicionar uma ID de cliente real agora. Essas informações são incorporadas no pacote. App e permite que o conteúdo da web para se comunicar com o SharePoint no site ao vivo.
    
  
3. Escolha o botão **Concluir**.
    
    Visual Studio gera os arquivos que são necessários para publicar seu Suplemento do SharePoint e, em seguida, abre a pasta de saída de publicar. Para obter informações sobre como instalar o suplemento, consulte  [instalar e gerenciar suplementos de SharePoint 2013](http://technet.microsoft.com/library/fp161232.aspx).
    
  

### Etapa 3: Publicar seu Suplemento do SharePoint sobre o Office Store

Execute o procedimento a seguir se desejar enviar seu Suplemento do SharePoint para o Office Store.
  
    
    

1. Na página **publicar seu suplemento**, escolha o botão **visite painel vendedor** e, em seguida, entre sua conta do Microsoft vendedor Dashboard.
    
    Consulte  [Enviar o Office e suplementos do SharePoint e Office 365 web apps para o Office Store](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx).
    
  
2. Escolha **Adicionar um novo aplicativo**, preencha as informações e, em seguida, enviar o suplemento para o Office Store. Para obter detalhes, consulte  [Use o painel do vendedor enviem aplicativos do Office e suplementos do SharePoint e Office 365 para a Office Store](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx).
    
  

## Publicar usando Visual Studio 2012
<a name="VS2012"> </a>

Quando você estiver pronto para empacotar sua Suplemento do SharePoint, abra o Assistente de **suplementos do Office publicar**, que prepara os arquivos em seu Suplemento do SharePoint para publicação.
  
    
    

### Etapa 1: Pacote o Suplemento do SharePoint


1. No **Solution Explorer**, abra o menu de atalho para o projeto Suplemento do SharePoint e escolha **Publicar**.
    
    O Assistente de **suplementos do Office publicar** aparece. O tipo de Suplemento do SharePoint que você esteja compactando determina as páginas que aparecem no assistente. Se seu suplemento será hospedado no SharePoint, somente a página de **Resumo** será exibida. Se seu suplemento será hospedado no provedor, as páginas de **perfil** e **hospedagem** também é exibida.
    
  
2. Se sua Suplemento do SharePoint for hospedado no provedor, especifique um nome de perfil de publicação no **perfil que você deseja publicar?** lista e, em seguida, escolha o botão **Avançar**.
    
    O perfil de publicação salva as informações que você insere na página de **hospedagem**.
    
  
3. No **onde seu site está hospedado?** listar, especifique a URL do aplicativo da web que hospedará sua Suplemento do SharePoint.
    
  
4. Nas caixas em **o que é a identidade do seu suplemento?**, especifique a ID do cliente e o segredo de cliente para seu suplemento e, em seguida, escolha o botão **Avançar**.
    
    Consulte  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md).
    
  
5. Todos os tipos de Suplementos do SharePoint, marque a caixa de seleção **Abrir pasta de saída depois de embalagem bem-sucedida**, se ainda não estiver marcada e, em seguida, escolha o botão **Concluir**.
    
    Visual Studio gera todos os arquivos que você precisa publicar sua Suplemento do SharePoint. Você pode encontrar esses arquivos na pasta  `app.Publish` da pasta de saída do projeto (por exemplo, `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`). Essa pasta conterá um arquivo. App para sua Suplemento do SharePoint e vários arquivos para o aplicativo da web (se sua Suplemento do SharePoint é baseado em nuvem). Todos os Suplementos do SharePoint incluir um arquivo. App, que é o manifesto suplemento para publicar o Suplemento do SharePoint. Provedor hospedado Suplementos do SharePoint também incluir arquivos para publicar o aplicativo da web de host.
    
  

### Etapa 2: Publicar o aplicativo da web

Se seu Suplemento do SharePoint estiver hospedado no provedor, você normalmente terá um aplicativo da web de host associado que você precisa publicar em um servidor web. Visual Studio gera um pacote de implantação e um script para ajudá-lo a realizar esta tarefa.
  
    
    
O pacote de implantação do projeto de aplicativo da web está contido em um arquivo compactado (. zip) na pasta  `app.publish` . Além do arquivo. zip, a pasta de `app.publish` contém os arquivos a seguir:
  
    
    


|**File**|**Descrição**|
|:-----|:-----|
| _ProjectName_. Deploy <br/> |Este arquivo é um arquivo de lote da linha de comando que invoca a implantação da Web para que você possa instalar mais facilmente o pacote em um prompt de comando. <br/> |
| _ProjectName_. SetParameters.xml <br/> |Este arquivo contém parâmetros que são passados para implantar o Web quando você usa o arquivo Deploy para instalar o pacote. As configurações de pacote Visual Studio determinam o valor padrão especificado para cada parâmetro. Você pode alterar esses valores se, por exemplo, você deseja instalar o aplicativo web para vários servidores e usar configurações diferentes para cada servidor. <br/> |
| _ProjectName_. SourceManifest.xml <br/> |Este arquivo contém configurações que Visual Studio passa a implantação da Web e implantar essa Web usa para criar o pacote da web. Implantar Web requer este arquivo somente para criar o pacote. Esse arquivo não é usado quando o pacote é instalado. <br/> |
   
Para obter orientação passo a passo, consulte  [como: instalar o pacote de uma implantação usando o Deploy arquivo criado pelo usando o Visual Studio](http://go.microsoft.com/fwlink/?LinkID=254954)
  
    
    

### Etapa 3: Publicar seu Suplemento do SharePoint
<a name="Publish"> </a>

Para publicar seu Suplemento do SharePoint, carregar o suplemento manifesto arquivo (. app) de seu suplemento para o Office Store, o catálogo de suplementos do Office, SharePoint, um compartilhamento de arquivo ou o catálogo do Exchange. O manifesto suplemento para o suplemento está localizado na pasta  `app.publish` , como `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`. Para obter mais informações sobre como publicar seu Suplemento do SharePoint, consulte  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md).
  
    
    

## Recursos adicionais
<a name="Additional"> </a>


-  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Publicar seu suplemento do Office](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)
    
  

