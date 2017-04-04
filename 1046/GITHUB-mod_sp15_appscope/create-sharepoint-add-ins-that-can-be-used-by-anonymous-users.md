---
title: Criar Add-ins do SharePoint que pode ser usado por usuários anônimos
ms.prod: SHAREPOINT
ms.assetid: e858fce6-7a8f-4b1e-b053-64dc75345801
---


# Criar Add-ins do SharePoint que pode ser usado por usuários anônimos
Saiba como criar Suplementos do SharePoint que pode ser usado por usuários anônimos nos sites de Microsoft SharePoint 2013 público.
> [!IMPORTANTE]
> Sempre que  *no local*  SharePoint 2013 mencionada neste artigo, assumimos que Service Pack 1 para SharePoint 2013 foi instalada.
  
    
    


## Pré-requisitos para a criação de acesso anônimo Suplementos do SharePoint

É possível que hospedado no SharePoint e hospedado em provedor Suplementos do SharePoint o acesso anônimo. Dependendo do tipo que você criar, revisar um dos seguintes conjuntos de pré-requisitos:
  
    
    

-  [](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md#SP15SPhostedapps_bk_prereqs)
    
  
-  [](get-started-creating-provider-hosted-sharepoint-add-ins.md#SP15createselfhostapp_bk_prereq)
    
  
Você também precisará de um conjunto de sites em sua instalação do SharePoint 2013 de teste que está configurada para acesso anônimo. Se você tiver um Office 365 Developer Site, já existe uma coleção de site público associada a ela que usa uma definição de site Website público especial. (Para obter mais informações sobre o uso de sites públicos no Microsoft SharePoint Online, consulte o  [Website público ajuda para o Office 365](http://office.microsoft.com/en-gb/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1).) Essa definição de site não está disponível para em instalações de SharePoint 2013 local. Portanto, se sua instalação do teste for local, será necessário:
  
    
    

- Um aplicativo da web do SharePoint que está configurado para permitir o acesso anônimo. Mas esta configuração deve ocorrer depois Suplemento do SharePoint que você está testando tiver sido instalado.
    
  
- Um conjunto de sites no aplicativo web que é configurado para acesso anônimo. Mas esta configuração deve ocorrer depois Suplemento do SharePoint que você está testando tiver sido instalado.
    
  
- Se seu suplemento é hospedado no SharePoint e acessa uma lista do SharePoint, uma lista no conjunto de sites que está configurado para acesso anônimo.
    
  
Instruções para essas tarefas estão na seção  [Configurando um conjunto de site e do aplicativo web do SharePoint e a lista para acesso anônimo](#Configuring).
  
    
    

## Limitações sobre o uso do SharePoint suplementos por usuários anônimos

Considere os seguintes fatos ao projetar sua Suplemento do SharePoint:
  
    
    

- Se o Suplemento do SharePoint é iniciado a partir do SharePoint, ele deve incluir uma ação personalizada ou uma parte do suplemento ou ele deve ser iniciado a partir de um link personalizado em uma página. Isso ocorre porque os usuários anônimos não é possível iniciar o suplemento pelo seu lado a lado. O mecanismo pelo qual suplementos são iniciados a partir de blocos requer o uso de uma  [página aplicativo](http://msdn.microsoft.com/library/685c8e01-b163-4b5e-888c-421d9ecbb25e%28Office.15%29.aspx) especiais que não está acessível a usuários anônimos. Outra opção é tornar o suplemento pode ser iniciado do SharePoint externo, caso em que ele precisa usar oadd-somente na diretiva de autorização.
    
  
- Um usuário anônimo só pode iniciar Suplementos do SharePoint que tiver sido instalado por outras pessoas. Isso ocorre porque os usuários anônimos não podem abrir a página **Adicionar um suplemento**.
    
  
- Quando um usuário conectado navega até um site da Web em um aplicativo web do SharePoint local que tenha sido configurado para acesso anônimo, alterações de identidade do usuário para a **Conta do sistema**. Essa identidade não pode instalar Suplementos do SharePoint. Desde que os usuários anônimos também não é possível instalar suplementos, isso significa que ninguém pode instalar um Suplementos do SharePoint quando o aplicativo web é configurado para acesso anônimo. Apropriadamente, Suplementos do SharePoint deverão ser instalados em sites no aplicativo web do SharePoint antes do aplicativo web é configurado para acesso anônimo. Se outros suplementos precisam ser instalados posteriormente, o aplicativo web deve ser alterado temporariamente para desativar o acesso anônimo para que os suplementos podem ser instalados por um usuário conectado.
    
  
- Para habilitar as APIs de REST de pesquisa do SharePoint em um website do SharePoint local, configurar algumas consulta XML. Para obter detalhes, consulte  [Habilitando consultas de pesquisa REST anônimas](http://msdn.microsoft.com/library/office/jj163876.aspx#bk_AnonymousREST).
    
  
- Dados na web suplemento não são rastreados por indexadores a pesquisa, portanto, dados personalizados devem ser implantados remotamente ou em web host. Isso se aplica a todos os Suplementos do SharePoint, mas observamos ele aqui porque suplementos destinados para acesso anônimo, muitas vezes exigem a funcionalidade de pesquisa.
    
  

## Criando hospedado em provedor suplementos que estão acessíveis anonimamente
<a name="Cloud-hosted"> </a>

Tornar um suplemento hospedado em provedor acessíveis a usuários anônimos é simplesmente uma questão de configurá-lo para usar a Adicionar-somente na diretiva de autorização. Quando essa diretiva é usada, SharePoint não mesmo inclui um contexto de usuário, portanto, não importa se o usuário é anônimo. Só é necessário se a entidade de segurança recebe todas as permissões necessárias para a web host - e qualquer necessárias para o conjunto de sites pai ou locação pai-- pelo administrador do locatário que instala o suplemento. Você solicitar permissões na Web de host no manifesto suplemento como faria qualquer suplemento.
  
    
    

> [!OBSERVAçãO]
> Se o site do SharePoint está acessível aos usuários anônimos, ele geralmente permite acesso HTTP, em vez de HTTPS. Há possíveis problemas de segurança quando a diretiva add-somente na é usada para suplementos neste cenário. Para obter detalhes e um método para atenuá-los, consulte  [o que cada desenvolvedor precisa saber sobre SharePoint suplementos, CSOM e os Sites de publicação anônimo](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx).
  
    
    

Criar um suplemento para usar a diretiva add-somente na exige duas coisas:
  
    
    

- Você deve adicionar  `AllowAppOnlyPolicy="true"` ao elemento **AppPermissionRequests** no manifesto do suplemento.
    
  
- Seu código deve solicitar um token de acesso do add-somente do servidor de autorização de OAuth. Se você estiver trabalhando com código gerenciado, há APIs criados especificamente para essa finalidade nos arquivos de código que são gerados por Microsoft Office Developer Tools for Visual Studio: SharePointContext.cs (ou. vb) e TokenHelper.cs (ou. vb).
    
  
Para obter detalhes sobre a diretiva add-somente na (com trechos de código), o suplemento de permissões e o manifesto do suplemento consulte estes tópicos:
  
    
    

-  [Tipos de política de suplemento autorização no SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md)
    
  
-  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  

## Criando hospedado no SharePoint suplementos que estão acessíveis anonimamente
<a name="SP-hosted"> </a>

Criar um hospedado no SharePoint suplemento que pode ser executado por usuários anônimos não exige qualquer técnicas especiais. Criá-lo da mesma maneira que você criará qualquer suplemento hospedado no SharePoint. Para obter detalhes, consulte  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md) e [Como concluir operações básicas usando código da biblioteca do JavaScript no SharePoint 2013](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md).
  
    
    
O que é realmente crucial para tornar um suplemento hospedado no SharePoint utilizável por acesso anônimo é que o conjunto de sites SharePoint Online está configurado por um administrador de locatário para permitir acesso anônimo. Não é possível que você como um desenvolvedor de um suplemento de hospedado no SharePoint pode fazer essa configuração do suplemento ou um receptor de evento remoto suplemento ou com a infraestrutura de instalação do suplemento do SharePoint. Você pode incluir uma lista personalizada ou biblioteca no suplemento, mas Collaborative Application Markup Language (CAML) não oferece nenhuma maneira para pré-configurá-lo para acesso anônimo, portanto, seria precisa ser configurada depois que o suplemento está instalado.
  
    
    
Se você estiver marketing seu suplemento por meio do Office Store e uma parte significativa dos seus clientes potenciais encontraria o suplemento mais útil se ele está acessível a usuários anônimos, você deve considerar mencionar esse requisito de configuração em sua descrição do suplemento. Considere a inclusão também uma versão do procedimento **para configurar um conjunto de sites do SharePoint Online para acesso anônimo** abaixo no página de suporte da seu suplemento.
  
    
    
Se seu suplemento usa o modelo de objeto JavaScript do SharePoint (JSOM), há um requisito de configuração que é crucial. Apenas uma parte muito pequena das APIs JSOM são acessíveis por padrão, a usuários anônimos. Todos os outros exigem que um usuário tem a permissão **Usar Interfaces remotas** e usuários anônimos não têm essa permissão. Esse requisito deve ser desativado no conjunto de sites pai ou aplicativo de web do SharePoint pai do site onde o add-in estiver instalado, conforme descrito em [Configurando um conjunto de site e do aplicativo web do SharePoint e a lista para acesso anônimo](#Configuring).
  
    
    

> [!OBSERVAçãO]
> Desativar o requisito de que os usuários tenham a permissão **Usar Interfaces remotas** tem implicações de privacidade de informações. Para obter detalhes, consulte [o que cada desenvolvedor precisa saber sobre SharePoint suplementos, CSOM e os Sites de publicação anônimo](http://blogs.msdn.com/b/kaevans/archive/2013/10/24/what-every-developer-needs-to-know-about-sharepoint-apps-csom-and-anonymous-publishing-sites.aspx).
  
    
    


## Limitações de hospedado no SharePoint suplementos para usuários anônimos
<a name="SP-hosted"> </a>

Voltaremos verdade com você: há algumas limitações significativas em hospedado no SharePoint suplementos para usuários anônimos que você precisa estar ciente:
  
    
    

- Não é possível fazer uma lista ou biblioteca em um SharePoint Online acessível aos usuários anônimos. Apropriadamente, JSOM APIs que envolvem a interação com listas ou bibliotecas não funcionam no SharePoint Online para usuários anônimos.
    
  
- Um hospedado no SharePoint suplemento instalado para um site do SharePoint  *no local*  pode tornar a utilizar todas as APIs JSOM. No entanto, o aplicativo web, conjunto de sites e listas/bibliotecas devem ser *manualmente*  configurado para acesso anônimo e o requisito de permissão **Usar Interfaces remotas** deverá ser desabilitado. Se o suplemento instala bibliotecas/listas personalizadas na Web suplemento, os usuários são responsáveis por configurar esses para acesso anônimo a manualmente após a instalação do suplemento.
    
  
- Como os dados na web suplemento não são rastreados por indexadores a pesquisa e porque não é possível instalar bibliotecas ou listas personalizadas na Web de host, e suplementos hospedado no SharePoint não podem ter componentes remotos ou receptores de evento, não são pesquisáveis dados personalizados em um suplemento de hospedado no SharePoint.
    
  

## Configurando um conjunto de site e do aplicativo web do SharePoint e a lista para acesso anônimo
<a name="Configuring"> </a>

Se o teste de instalação do SharePoint local, você deve realizar os dois primeiros procedimentos abaixo para testar se a sua Suplemento do SharePoint pode ser usado por usuários anônimos. Os clientes que instalam o seu Suplemento do SharePoint também devem realizar estes procedimentos para o conjunto de sites pai e o aplicativo web de qualquer site onde sua Suplemento do SharePoint está instalado.
  
    
    

> [!IMPORTANTE]
> Se possível, você deve instalar o Suplemento do SharePoint para um site  *antes de*  que você realizar os dois primeiros procedimentos. Suplementos não podem ser instalados em qualquer site em um aplicativo da web do SharePoint no local quando o aplicativo web foi configurado para acesso anônimo. Se o aplicativo web já tiver sido configurado para acesso anônimo, será necessário reverter essa definição temporariamente para instalar o suplemento.
  
    
    


### Para configurar o aplicativo web pai para o acesso anônimo


1. Na **Administração Central**, escolha **Gerenciamento de aplicativos** e, em seguida, **Gerenciar aplicativos Web**.
    
  
2. (Opcional) Se você não quiser permitir o acesso anônimo para qualquer um dos aplicativos da web existentes do SharePoint, crie um novo aplicativo web. (Embora o formulário de criação do aplicativo web oferece a opção de permitir o acesso anônimo,  *não use essa opção*  . Acesso anônimo não deve ser habilitado até *após*  o Suplemento do SharePoint está instalado.) Para obter detalhes, consulte [Create a web application in SharePoint 2013](http://msdn.microsoft.com/library/121c8d83-a508-4437-978b-303096aa59df.aspx).
    
  
3. Na lista de aplicativos web, selecione aquele que será acessível por usuários anônimos e selecione **Provedores de autenticação** na faixa de opções.
    
  
4. Sobre o texto explicativo é aberta, escolha a zona para o qual o acesso anônimo deve ser habilitado. Geralmente, esse é o **padrão** ou a **Internet**. O formulário **Editar autenticação** abre.
    
  
5. Marque a caixa **Habilitar acesso anônimo**, ele se já não está habilitada. Isso define um padrão que pode ser desativado para conjuntos de sites específico, mas se você não ativar esta para o aplicativo web, você não pode habilitá-lo para quaisquer conjuntos de sites.
    
  
6. (Opcional) Desmarque a caixa de **permissão exigem usar Interfaces remotas**. Isso permite que o código e o script em execução no contexto de um usuário anônimo para fazer chamadas para o modelo de objeto do cliente do SharePoint em cada conjunto de sites. Você não é possível reabilitar o requisito para quaisquer conjuntos de sites. Deixando a caixa verificado significa que, por padrão os usuários anônimos não podem acessar o cliente de modelos de objetos, mas você pode desabilitar o requisito (e concedê-los acessar) para conjuntos de sites específico.
    
    > [!OBSERVAçãO]
      > No contexto de desenvolvimento Suplementos do SharePoint para usuários anônimos, essa configuração somente é significativa para hospedado no SharePoint suplementos hospedado em provedor Suplementos do SharePoint projetadas para usuários anônimos usam uma técnica que faz com que as permissões do usuário irrelevante. Para obter mais informações sobre isso, consulte a seção  [Criando hospedado em provedor suplementos que estão acessíveis anonimamente](#Cloud-hosted) acima.
7. Escolha **Salvar** para fechar o formulário.
    
  
8. Com o aplicativo da web ainda realçado, selecione a **Política de anônimo** na faixa de opções.
    
  
9. No formulário **Restrições de acesso anônimo**, escolha a zona e certifique-se de que o botão de opção **Nenhum** está habilitado. Se o Suplemento do SharePoint que você está testando necessidades apenas direitos de leitura para dados do SharePoint, habilite **Negar gravação**.
    
    > [!OBSERVAçãO]
      > Esta é outra configuração que, no contexto do desenvolvimento Suplementos do SharePoint para usuários anônimos, só é significativa para hospedado no SharePoint suplementos.
10. Se você criou um novo aplicativo web na etapa 2, você precisa criar um conjunto de sites nela.
    
  

### Para configurar um conjunto de sites no local para acesso anônimo


1. Na home page do conjunto de sites no aplicativo da web, selecione o ícone de engrenagem e **Configurações do Site**.
    
  
2. Na seção **usuários e permissões** da página **Configurações do Site**, selecione as **permissões do Site**.
    
  
3. Escolha o **Acesso anônimo** na faixa de opções.
    
  
4. No formulário **Acesso anônimo**, escolha **todo o site** ou **listas e bibliotecas**, dependendo do nível de acesso o suplemento que você está testando necessidades. (Independentemente de qual opção escolher, os usuários anônimos não podem acessar uma lista ou biblioteca com um suplemento hospedado no SharePoint, a menos que a lista da biblioteca do individualmente está configurada para acesso anônimo tudo. Um procedimento a seguir descreve como fazer isso.
    
  
5. Se o suplemento que você está testando precisa acessar o modelo de objeto do cliente do SharePoint, verifique se a caixa **exigem usar Interfaces remotas permissão** *não*  está marcada. Se a caixa de seleção estiver esmaecida, em seguida, o requisito foi desativado no nível do aplicativo web que tem o mesmo efeito que a caixa sendo desmarcado. (Conforme indicado no procedimento anterior, essa configuração, no contexto do desenvolvimento Suplementos do SharePoint para usuários anônimos, só é significativa para aplicativos hospedados pelo SharePoint.)
    
  

> [!IMPORTANTE]
> O procedimento a seguir só pode ser realizado em um site público no SharePoint Online. (Para obter mais informações sobre o uso de sites públicos no Microsoft SharePoint Online, consulte o  [Website público ajuda para o Office 365](http://office.microsoft.com/en-gb/office365-sharepoint-online-enterprise-help/public-website-help-for-office-365-HA102891740.aspx?CTT=1).)
  
    
    


### Para configurar um conjunto de sites SharePoint Online para acesso anônimo


1. Faça login Office 365 como um administrador de locatário.
    
  
2. Na home page do conjunto de sites, escolha **Tornar WEBSITE ONLINE** próximo ao canto superior direito da página. Essa ação também desativa o requisito de **permissão de usar Interfaces remotas**.
    
  
Se você estiver desenvolvendo um suplemento hospedado no SharePoint e ele acessa uma lista do SharePoint, você também deve realizar o procedimento a seguir em seu conjunto de sites de teste. Os clientes que usam o suplemento serão necessário fazer isso também em qualquer site onde o suplemento está instalado. Não é possível que um suplemento pode programaticamente ou descritivos configurar uma lista para acesso anônimo. Suplementos podem instalar listas personalizadas, mas há um meio para pré-configurar essas listas para acesso anônimo. A configuração ainda deve ser feita manualmente após o add-in estiver instalado. Se você estiver desenvolvendo um provedor hospedado Suplemento do SharePoint para usuários anônimos, esse procedimento não é necessário.
  
    
    

> [!OBSERVAçãO]
> Esse procedimento não podem ser executadas em um conjunto de sites SharePoint Online, portanto, hospedado no SharePoint suplementos que são instalados SharePoint Online e foi projetados para usam por usuários anônimos não podem acessar listas ou bibliotecas.
  
    
    


### Para configurar uma lista ou biblioteca para acesso anônimo


1. Navegue até uma lista ou biblioteca que acessa o Suplemento do SharePoint que você está testando e abra as **Definições de lista** ou **Definições da biblioteca**. Para um site do SharePoint Online, você deve estar conectado como um administrador de locatário.
    
  
2. Escolha as **permissões para esta lista/biblioteca**.
    
  
3. Na página que é aberta, escolha **Parar de receber permissões** na guia **permissões** e clique em **OK** no prompt de confirmação.
    
  
4. Escolha o **Acesso anônimo** na guia **permissões**.
    
  
5. No formulário **Acesso anônimo** que é aberta, selecione todas as permissões que os usuários da Suplemento do SharePoint exigem. Você pode conceder permissão para exibir, adicionar, editar e excluir itens. Você não pode conceder controle total, para que os usuários anônimos não podem alterar o esquema da lista ou alterar as definições de lista.
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  

