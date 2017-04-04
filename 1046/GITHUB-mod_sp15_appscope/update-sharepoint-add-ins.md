---
title: Suplementos de atualização para o SharePoint
ms.prod: SHAREPOINT
ms.assetid: 3edcb33c-fa9e-4e9e-82d6-5519fd981324
---


# Suplementos de atualização para o SharePoint
Saiba como criar e implantar uma atualização para um Suplemento do SharePoint.
Você pode atualizar seu Suplemento do SharePoint usando o suporte atualizando incorporado ao SharePoint 2013. Dentro de 24 horas depois que você carrega uma versão atualizada do add-in suplemento catálogo da organização ou o suplemento é aceita no Office Store, uma notificação que está disponível uma atualização aparece ao lado de suplemento na página de **Conteúdo do Site** de cada site onde ele está instalado. Como você pode ver na Figura 1, um link é fornecido para que os usuários instalem imediatamente a atualização.
  
    
    


**Figura 1. Suplemento para o processo de atualização do SharePoint**

  
    
    

  
    
    
![The UI steps for updating an app](images/UpdatingApp_AppTileUpdateNotice.png)
  
    
    
Um usuário pode instalar a atualização sem primeiro desinstalar a versão anterior. A atualização de infraestrutura testa a instalação da atualização e ele será revertida se houver erros.
    
> [!IMPORTANTE]
> Você não pode alterar o  *tipo de suplemento*  usando o sistema de atualização. Por exemplo, você não pode alterar um suplemento do hospedado no SharePoint para hospedado em provedor com uma atualização. Para fazer uma alteração de tipo, você precisa [migrar de um suplemento antigo para um novo](sharepoint-add-ins-update-process.md#Major). Em particular, desde que  [o programa de visualização para suplementos auto-hospedados foi fechado](http://blogs.office.com/2014/05/16/update-on-autohosted-apps-preview-program/), você deve estar ciente de que você não pode atualizar um suplemento auto-hospedado para um suplemento hospedado pelo provedor. Você precisará converter o suplemento, conforme explicado em  [Converter um auto-hospedados Add-in do SharePoint em um suplemento hospedado em provedor](convert-an-autohosted-sharepoint-add-in-to-a-provider-hosted-add-in.md).
  
    
    


## Pré-requisitos para atualizar um Add-in do SharePoint
<a name="Prerequisites"> </a>


  
    
    

- Uma instalação de SharePoint 2013 de teste que está configurada para o isolamento do suplemento. Consulte  [Configurar um ambiente de desenvolvimento para suplementos do SharePoint no Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) para obter instruções sobre como configurar um Office 365 Developer Site.
    
  
- Ferramentas que são usadas na criação de um Suplemento do SharePoint normalmente são também utilizados em atualizá-lo. Por exemplo, a maioria dos desenvolvedores use Visual Studio e Microsoft Office Developer Tools for Visual Studio para criar Suplementos do SharePoint.
    
  

### Principais conceitos saber para atualizar um Add-in do SharePoint


  
    
    

**Tabela 1. Principais conceitos para atualizar um Add-in do SharePoint**


|**Título do artigo**|**Descrição**|
|:-----|:-----|
| [Escolha os padrões para desenvolver e hospedar o Add-in do SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Saiba mais sobre os diferentes tipos de Suplementos do SharePoint. O processo de atualização varia dependendo do tipo. <br/> |
| [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md) <br/> |Saiba mais sobre o processo para atualizar Suplementos do SharePoint. <br/> |
| [Upgrading Features](http://msdn.microsoft.com/library/e917f709-6491-4d50-adbe-2ab8f35da990%28Office.15%29.aspx) <br/> |Saiba como atualizar os recursos (SharePoint 2010 SDK ). <br/> |
| [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) <br/> |Saiba mais sobre os métodos de publicação, instalação e desinstalação em um Suplemento do SharePoint. <br/> |
| [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md) <br/> |Saiba mais sobre os receptores de evento remoto no SharePoint 2013. <br/> |
   

## Principais etapas atualizando um suplemento
<a name="MajorAppUpgradeSteps"> </a>

A seguir estão as principais etapas que podem ser necessários quando você cria uma atualização para um Suplemento do SharePoint. Cada etapa é discutida detalhadamente nas seções vinculadas ou artigos. Nem todas as etapas são necessárias em cada projeto de atualização. O que você deve fazer depende de quais componentes já estão em seu suplemento e quais componentes você está adicionando. Apenas os itens marcados com ***** sempre são necessários.
  
    
    

- Atualize o manifesto do suplemento.
    
  - ***** Eleve o número de **Version** no elemento [App](http://msdn.microsoft.com/library/d5f30dfe-7500-5f85-0f08-f4f220c0c692%28Office.15%29.aspx) do arquivo appmanifest.xml. (Complementos eram chamados de "apps" quando o esquema foi lançado pela primeira vez.) Faça *não*  alterar o número de **ProductID**.
    
  
  - Altere a seção  [AppPermissionRequests](http://msdn.microsoft.com/library/4e617622-78d3-3d23-677d-9957eb1fb107%28Office.15%29.aspx) do arquivo appmanifest.xml.
    
  
  - Altere a seção  [AppPrerequisites](http://msdn.microsoft.com/library/7622b55f-01a1-2c39-9daa-7cfb1a3c890f%28Office.15%29.aspx) do arquivo appmanifest.xml.
    
  

    Para obter mais informações, consulte  [Atualizar a versão add-in, solicitações de permissão e pré-requisitos](#UpdateManifest).
    
  
- Adicione ou atualize a marcação para o suplemento de web components. Para obter mais informações, consulte  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md).
    
  
- Adicione ou atualize a marcação de componentes da web de host. Para obter mais informações, consulte  [Atualizar os componentes da web de host no SharePoint 2013](update-host-web-components-in-sharepoint-2013.md).
    
  
- Adicionar lógica personalizada a um  [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) e registrá-lo no arquivo appmanifest.xml. Para obter mais informações, consulte [Criar um manipulador para o evento de atualização no SharePoint Add-ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).
    
  
- Atualize os componentes remota:
    
  - Para um provedor hospedado suplemento, atualize os componentes remota usando as técnicas apropriadas para a pilha de plataforma de hospedagem.
    
  

    Para obter mais informações, consulte  [Atualizar componentes remotos no SharePoint Add-ins](update-remote-components-in-sharepoint-add-ins.md).
    
  
- ***** Carregar o suplemento de pacote para o Office Store ou o catálogo de suplemento da organização.
    
  

## Práticas recomendadas para atualizações de suplemento
<a name="BestUpdatePractices"> </a>

As seções a seguir discutem práticas que você deve seguir e pontos importantes a serem consideradas conforme você estiver planejando uma atualização.
  
    
    

### Decidir se realmente precisa ser atualizado

Para um provedor hospedado Suplemento do SharePoint, melhorias para o suplemento não necessariamente exigem atualizando o add-in. Se todas as alterações são para componentes remotos, e essas alterações não têm sejam refletidas em componentes do SharePoint, você pode alterar os componentes remota sem atualizar o suplemento. Não altere as URLs e cadeias de conexão que os componentes do SharePoint usam para acessar os componentes remota, desde que o Suplemento do SharePoint continua a funcionar. Por exemplo, suponha que você adicione um botão a um aplicativo web remoto que lê uma coluna de uma lista do SharePoint que o aplicativo web não leu anteriormente. Se a coluna já existir na lista, você não precisará alterar alguma coisa no SharePoint. Você pode carregar a página da Web revisada e o revisado code-behind ou JavaScript, para o aplicativo web remoto. A nova funcionalidade é imediatamente disponível para os usuários quando eles começarem a Suplemento do SharePoint.
  
    
    

### Lembre-se de que a atualização é opcional para usuários

Quando uma nova versão do seu Suplemento do SharePoint fica disponível no Office Store ou o catálogo de suplemento da organização, uma mensagem é exibida em blocos na página **Conteúdo do Site** informando aos usuários que está disponível uma atualização do suplemento. Leva não mais de 24 horas para esta mensagem apareça. Mas nada na infraestrutura de SharePoint força os usuários a atualizarem. Portanto as alterações feitas para componentes remotos não devem quebrar as versões mais antigas do add-in. Uma regra geral, mas não é bastante universal, é que você deve *Adicionar*  coisas para componentes remotos, mas evitar a exclusão, renomear, mover ou alterando o esquema, a cadeia de caracteres de conexão ou a URL de qualquer componente existente.
  
    
    
Se precisar saber a versão da instância do suplemento que é chamada de um componente remoto, você pode passar essas informações do SharePoint. Por exemplo, você pode adicionar a versão do suplemento como um parâmetro de consulta na URL de  [página inicial](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx) do add-in.
  
    
    

### Criar e depurar a nova versão, como se fosse um novo suplemento
<a name="DebugFirst"> </a>

Convém separar o desenvolvimento e a nova versão do suplemento da depuração da marcação de atualização e a lógica de depuração. Para fazer isso, desinstale a versão anterior do suplemento do seu site do SharePoint de teste de desenvolvimento. Salve uma cópia de backup do arquivo pacote suplementar para a versão anterior. Adicionar e alterar componentes do add-in, conforme necessário e, em seguida, testar e depurá-los contra o site de teste, como se fosse um novo suplemento que você está criando a partir do zero.
  
    
    

### Teste a atualização com cada versão anterior do add-in
<a name="DebugFirst"> </a>

Quando a nova versão do add-in está funcionando corretamente como um "new" add-in, reestruturação o código e marcação para que o projeto seja uma atualização do antigo add-in. Por exemplo, incremente o número de versão do suplemento, conforme indicado na  [Principais etapas atualizando um suplemento](#MajorAppUpgradeSteps). Para obter mais informações sobre transformando o projeto em uma atualização, consulte os tópicos de filho deste tópico.
  
    
    
Quando você estiver pronto para testar sua atualização, a nova versão do site do teste de retração e reimplantar a versão anterior para que você possa testar a lógica de atualização. Se você tiver enviados com várias versões anteriores do add-in, instale cada versão anterior em uma subweb diferente do seu site de teste. Em seguida, carregar a versão mais recente do suplemento suplemento de catálogo do seu site de teste de e atualizar cada instância da verificação suplemento que cada tem o número de add-in da versão mais recente e a versão mais recente de todos os componentes. Se houver um suplemento de web do add-in, verifique se que o suplemento de web components foram implantado usando o procedimento em  [Verificar a implantação do suplemento de web components](update-add-in-web-components-in-sharepoint-2013.md#VerifyDeployAppWebComp).
  
    
    

### Atualizar um suplemento sem aguardar a 24 horas
<a name="ImmediateUpdateNotice"> </a>

Ao desenvolver uma atualização para um suplemento em seu site de teste do SharePoint, é impraticável aguardar 24 horas entre as atualizações. Você (e usuários em um site do SharePoint de produção) podem atualizar um suplemento imediatamente depois que ele é carregado para o Office Store ou catálogo adicionar da organização com estas etapas:
  
    
    

### Para atualizar imediatamente um suplemento


1. Depois que a atualização mais recente é carregada no catálogo add-in, abra a página de **Conteúdo do Site** no site de onde o suplemento está instalado e escolha o botão de **…** no lado a lado do suplemento.
    
  
2. Sobre o texto explicativo é aberta, escolha a guia **sobre**. Sobre a página **sobre** que abre, há um aviso de que uma nova versão está disponível.
    
  
3. Escolha o botão **Obter**. Página **Conteúdo** do Site reabre e há um aviso de que o add-in está sendo atualizado blocos do suplemento.
    
  
A Figura 2 ilustra estas etapas.
  
    
    

**Figura 2. Processo de atualização imediatamente um Add-in do SharePoint**

  
    
    

  
    
    
![Immediate app upgrade process](images/UpdatingApp_ImmediateUpgradeProcess.png)
  
    
    

    
> [!OBSERVAçãO]
> Se você precisar ver o aviso de "atualização disponível" no suplemento lado a lado com mais freqüência que a cada 24 horas, você pode usar o método descrito no  [Processo de atualização para um SharePoint Add-in](sharepoint-add-ins-update-process.md#Minor) para tornar o aviso aparecer imediatamente.
  
    
    


## Atualizar a versão add-in, solicitações de permissão e pré-requisitos
<a name="UpdateManifest"> </a>

Depois de fazer uma cópia de backup da pasta do projeto Visual Studio, abra o projeto de suplemento. Suplemento aberto manifesto e elevar o número de versão, na guia **Geral** do designer manifesto.
  
    
    
Se a versão atualizada do suplemento precisa de mais (ou menos) permissões aos componentes da web host, faça as alterações necessárias para a seção  [AppPermissionRequests](http://msdn.microsoft.com/library/4e617622-78d3-3d23-677d-9957eb1fb107%28Office.15%29.aspx) do add-in. No Visual Studio, use a guia **permissões** do designer manifesto. Quando um add-in for atualizado, o usuário sempre é solicitado para conceder permissões, se as permissões foram alterados ou não desde a versão anterior. Se a nova versão solicitações *menos*  permissões que a versão anterior, as permissões adicionais da versão anterior *não serão revogados*  . A única maneira de restringir o suplemento para as permissões que a versão mais recente precisa, destina-se um usuário abrir a página *{SharePointDomain}*  `/_layouts/15/appinv.aspx` depois que o suplemento foi atualizado e digitar manualmente marcação de permissão que está em conformidade com o esquema de [AppPermissionRequests](http://msdn.microsoft.com/library/4e617622-78d3-3d23-677d-9957eb1fb107%28Office.15%29.aspx) .
  
    
    
Se a versão atualizada do add-in tem pré-requisitos de que as versões anteriores não tinha (ou não tem mais alguns pré-requisitos que tiveram de versões anteriores), faça as alterações necessárias para a seção  [AppPrerequisites](http://msdn.microsoft.com/library/7622b55f-01a1-2c39-9daa-7cfb1a3c890f%28Office.15%29.aspx) do add-in. Em Visual Studio, use a guia de **pré-requisitos** do designer manifesto.
  
    
    

## Próximas etapas
<a name="Next"> </a>

Continue com o próximo marcador na seção  [Principais etapas atualizando um suplemento](#MajorAppUpgradeSteps) ou ir diretamente para um dos seguintes artigos:
  
    
    

-  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Atualizar os componentes da web de host no SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Criar um manipulador para o evento de atualização no SharePoint Add-ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [Atualizar componentes remotos no SharePoint Add-ins](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md)
    
  

