---
title: Suplemento permissões no SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 5f7a8440-3c09-4cf8-83ec-c236bfa2d6c4
---



# Suplemento permissões no SharePoint 2013
Saiba mais sobre permissões de suplemento em SharePoint, incluindo os tipos de suplemento permissões, escopos de solicitação de permissão e gerenciar as permissões. Este artigo também descreve as diferenças em direitos de permissão de suplemento, direitos de usuário e direitos de app Office Store.
Você primeiro deve estar familiarizado com o tópico  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md) antes de ler este artigo.
  
    
    


**Assista a um vídeo sobre permissões do suplemento.**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## Obter uma visão geral das permissões de suplemento em SharePoint
<a name="Perm_intro"> </a>

um Suplemento do SharePoint solicita as permissões necessárias durante a instalação do usuário que está instalando. O desenvolvedor de um suplemento deve solicitar, por meio do suplemento do arquivo de manifesto, as permissões que o suplemento específico precisa estar poderão ser executadas. (Aplicativos web e do dispositivo que acessam SharePoint, mas não são instalados para sites de SharePoint devem receber permissões em tempo de execução pelo usuário que está executando o add-in. Para obter mais informações, consulte  [Obtenha uma visão geral de suplementos que solicitar permissão de acesso do SharePoint dinamicamente](authorization-code-oauth-flow-for-sharepoint-add-ins.md#Overview).) Os usuários podem conceder somente as permissões que eles têm. O usuário deve conceder todas as permissões que um suplemento solicitações ou não conceder permissão para qualquer. Concede seletiva não é possíveis. (Para suplementos que solicitar permissões dinamicamente, somente um usuário com permissões de gerenciar os recursos de SharePoint que o suplemento tenta acessar pode ser executado o suplemento, mesmo se o suplemento está perguntando apenas para permissões menor, como leitura.)
  
    
    
As permissões que o suplemento recebeu também são armazenadas no banco de dados de conteúdo do aluguel SharePoint farm ou SharePoint Online. Elas não são armazenadas com um serviço de token seguro, como Microsoft Azure Access Control Service (ACS). Quando um usuário primeiro concede permissões de um suplemento, SharePoint obtém informações sobre o suplemento do ACS. SharePoint armazena as informações básicas sobre o suplemento no serviço de gerenciamento de suplementos e o banco de dados de conteúdo, juntamente com as permissões do suplemento. Para obter mais informações sobre o ACS, consulte  [Criando Add-ins do SharePoint que usam a autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md).
  
    
    
Se um objeto ao qual um suplemento foi concedido permissão é excluído, o concede correspondente também é excluídos. Quando um objeto ao qual um suplemento foi concedido permissão é reciclado, SharePoint não modifica a concessão correspondente. Isso é para que se o objeto é restaurado da Lixeira, o grant ainda está intacta.
  
    
    
Quando um add-in for removido, todas as permissões concedidas a esse suplemento no escopo do qual ela foi removida serão revogadas. Isso é para garantir que o suplemento não pode usar suas credenciais para continuar acessando recursos protegidos SharePoint remotamente depois que um usuário remove o suplemento de SharePoint.
  
    
    

## Compreender os tipos de escopos de permissão e permissões de suplemento
<a name="Perm_types"> </a>

Um Suplemento do SharePoint usa solicitações de permissão para especificar as permissões que ele precisa para funcionar corretamente. As solicitações de permissão especificam os direitos que um suplemento precisa e o escopo no qual ele precisa os direitos. Essas permissões são solicitadas como parte de manifesto do suplemento.
  
    
    

> **OBSERVAçãO**
> Os escopos descritos nesta seção se aplicam ao conteúdo de lista e apenas o conteúdo da biblioteca. Para obter informações sobre escopos para outros recursos, consulte a seção de  [Compreender os tipos de escopos de permissão e permissões de suplemento](#Perm_types) neste artigo.
  
    
    

Escopos de solicitação de permissão indicam o local da hierarquia SharePoint onde aplica-se uma solicitação de permissão.
  
    
    

> **OBSERVAçãO**
> Um Suplemento do SharePoint tem sua própria identidade e é uma entidade de segurança, chamada um suplemento principal. Assim como os usuários e grupos, um suplemento de entidade tem determinadas permissões ou direitos. A entidade de segurança tem direitos de controle total na Web do suplemento para que ele só precisa solicitar permissões para os recursos de SharePoint em web host ou outros locais fora da web do suplemento. Para obter mais informações sobre o suplemento de web, consulte  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) e [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).
  
    
    

SharePoint suporta quatro escopos de permissão diferentes dentro do banco de dados de conteúdo e locação, conforme mostrado na tabela 1. Escopos de permissão são nomeados com URIs, incluindo um "http:" prefixo, mas eles não são URLs e eles contêm sem espaços reservados. Os escopos de permissão nesta tabela e neste artigo são cadeias de caracteres literais.
  
    
    

**Tabela 1. Descrições e escopo de solicitação de permissão do suplemento do SharePoint URIs**

|||
|:-----|:-----|
|**URI de escopo** <br/> |**Descrição** <br/> |
|Aluguel <br/> http://SharePoint/Content/tenant <br/> |Aluguel onde o suplemento está instalado. Inclui todos os filhos deste escopo. <br/> |
|conjunto de sites <br/> http://SharePoint/Content/sitecollection <br/> |O conjunto de sites onde o suplemento está instalado. Inclui todos os filhos deste escopo. <br/> |
|. <br/> http://sharepoint/content/sitecollection/web <br/> |O site em que o suplemento está instalado. Inclui todos os filhos deste escopo. <br/> |
|List <br/> http://sharepoint/content/sitecollection/web/list <br/> |Uma única lista no site onde o suplemento está instalado. Quando o usuário que instala o suplemento é solicitado para conceder permissões, a caixa de diálogo permite que o usuário selecionar uma lista à qual o suplemento recebe permissões. Se o suplemento precisa da permissão para mais de uma lista, ele deve solicitar permissão ao escopo da web. Além disso, desde que você, o desenvolvedor, não têm como controlar a lista na qual o usuário escolhe ou para informar ao usuário qual deles escolher, você deve usar o escopo da web se não houver uma lista à qual seu suplemento  *deve*  ter permissão. (Mas não há uma maneira de reduzir a escolha do usuário para determinadas subconjuntos de listas. Consulte o [Escopo de solicitação de permissão com propriedades associadas](#AssociatedProperties) abaixo). <br/> |
   
Se um suplemento é concedido permissão para um dos escopos, a permissão se aplica a todos os filhos do escopo. Por exemplo, se um suplemento é concedido permissão para um site, o suplemento também recebe permissão para cada lista que está contida no site e todos os itens de lista que estão em cada lista.
  
    
    
Porque as solicitações de permissão são feitas sem informações sobre a topologia do conjunto de sites onde o suplemento está instalado, o escopo é expresso como um tipo em vez de como a URL de uma instância específica. Esses tipos de escopo são expressas como URIs. Permissões para recursos que são armazenados no banco de dados de conteúdo de SharePoint estão organizadas sob o seguinte URI:  `http://sharepoint/content`.
  
    
    

## Entender as diferenças entre os direitos de permissão de suplemento e direitos de usuário
<a name="Perm_diff"> </a>

Permissões indicam as atividades que um suplemento é permitido fazer dentro do escopo solicitado. SharePoint oferece suporte a quatro níveis de direitos do banco de dados de conteúdo. Para cada escopo, um suplemento pode ter os seguintes direitos:
  
    
    

- Leitura
    
  
- Gravação
    
  
- Gerenciar
    
  
- FullControl
    
  

> **OBSERVAçãO**
> Para obter mais informações sobre qual leitura, direitos de gravação, gerenciar e controle total incluem, consulte o  [plano de gerenciamento de permissões de suplemento](http://technet.microsoft.com/en-us/library/jj219576%28office.15%29.aspx).
  
    
    


> **OBSERVAçãO**
> Esses direitos correspondem aos níveis de permissão do usuário padrão do SharePoint: leitor, colaborador, Designer e controle total. Para obter mais informações sobre níveis de permissão do usuário, consulte  [permissões de usuário e níveis de permissão](http://technet.microsoft.com/en-us/library/cc288074.aspx).> Os nomes de direitos de suplementos não coincidem SharePoint funções direitos nomes de usuário, para evitar a confusão entre os direitos de funções e direitos de suplemento. Como personalizar as permissões que estão associadas a funções de usuário SharePoint não afeta os níveis de solicitação de permissão do suplemento, os direitos de adicionar nomes não coincidem com as funções de usuário SharePoint correspondentes, exceto controle total, que não podem ser personalizadas por meio da interface de usuário de gerenciamento de permissões.
  
    
    

E mais:
  
    
    

- Para pesquisa, um suplemento pode ter a direito de consulta.
    
  
- Para algumas escopos Microsoft Project Server 2013, há também SubmitStatus direita ou para a direita elevação. Para a maioria dos escopos para Project Server 2013, estão disponíveis somente leitura e gravação. Para obter mais informações, consulte a seção de  [Compreender os tipos de escopos de permissão e permissões de suplemento](#Perm_types) neste artigo.
    
  
- Para taxonomia, direitos somente para leitura e gravação estão disponíveis.
    
  

> **OBSERVAçãO**
> aplicativos Office Store têm algumas restrições em relação a que tipo de direitos de um suplemento pode solicitar. Para obter mais informações, consulte a seção de  [Compreender os tipos de escopos de permissão e permissões de suplemento](#Perm_types) neste artigo.
  
    
    

Diferentemente das funções de usuário SharePoint, nestes níveis de direitos não são personalizáveis. Isso é para garantir que, quando um suplemento recebe uma solicitação de permissão, o suplemento é garantido que um conjunto previsível de recursos e não tem para levar em conta a possibilidade de sendo concedidas menos permissão esperada.
  
    
    
Um usuário não puder conceder permissões de um suplemento que o usuário si ou por conta própria não tem. Se um usuário tentar instalar um suplemento que as solicitações mais permissões que o usuário tem, uma mensagem de erro exibe para o usuário informando que não têm permissões suficientes para conceder o suplemento sua solicitação.
  
    
    
Permissões que não são conhecidas para SharePoint são ignoradas. Isso significa que, se um suplemento solicitar uma permissão que SharePoint não reconhecer, ainda pode ser instalado o suplemento, mas o usuário não será avisado para conceder a permissão, e não é concedida a permissão para o suplemento.
  
    
    

## Saiba mais sobre os escopos disponíveis e as permissões e as restrições de permissões de aplicativos Office Store
<a name="Perm_rightlist"> </a>

Escopos diferentes tem diferentes conjuntos de direitos que estão disponíveis para um suplemento solicitar. Esta seção descreve os conjuntos de direitos que estão disponíveis para cada escopo. Além disso, ele destaca as restrições para Suplementos do SharePoint são vendidos através do Office Store.
  
    
    

### direitos Office Store dos aplicativos

Apenas os direitos de leitura, gravação e gerenciar são permitidos para Office Store aplicativos. Se você tentar enviar um aplicativo para o Office Store que exige direitos de controle total, seu aplicativo seja bloqueado para envio. Como o bloco é no pipeline de envio Office Store, aplicativos que solicitar mais do que gerenciar permissões ainda podem ser implantados por meio do catálogo do suplemento.
  
    
    

### Escopos de solicitação de permissão de conteúdo de lista e conteúdo de biblioteca
<a name="PermissionsForLists"> </a>

Tabela 2 mostra o escopo de solicitação de permissão para conteúdo de lista e biblioteca. Ela também lista os direitos que podem ser especificados para cada URI de escopo.
  
    
    

> **OBSERVAçãO**
> Os URIs usados na tabela 2 são valores literais.
  
    
    


**Tabela 2. Escopo de permissão do suplemento do SharePoint URIs e direitos disponíveis**

|||
|:-----|:-----|
|**URI de escopo** <br/> |**Direitos disponíveis** <br/> |
|http://SharePoint/Content/sitecollection <br/> |Leitura, gravação, gerenciar, controle total <br/> |
|http://sharepoint/content/sitecollection/web <br/> |Leitura, gravação, gerenciar, controle total <br/> |
|http://sharepoint/content/sitecollection/web/list <br/> |Leitura, gravação, gerenciar, controle total <br/> |
|http://SharePoint/Content/tenant <br/> |Leitura, gravação, gerenciar, controle total <br/> |
   
O código a seguir mostra como você pode usar os escopos de permissão e os direitos no arquivo AppManifest.xml. Neste exemplo, está solicitando um suplemento para acesso de gravação ao escopo da lista.
  
    
    



```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleAddIn"
>
  <Properties>
    <Title>My Sample Add-in</Title>
    <StartPage>~remoteAppUrl/Home.aspx?{StandardTokens}</StartPage>
  </Properties>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>
</App>
```

O código a seguir mostra um suplemento que solicita Read access ao escopo da web e acesso de gravação ao escopo da lista.
  
    
    



```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleAddIn"
>
  <Properties>
    <Title>My Sample Add-in</Title>
    <StartPage>~remoteAppUrl/Home.aspx?{StandardTokens}</StartPage>
  </Properties>

  <AppPrincipal>
    <RemoteWebApplication ClientId="6daebfdd-6516-4506-a7a9-168862921986" />
  </AppPrincipal>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>
</App>
```


### Escopos de solicitação de permissão para outros recursos SharePoint
<a name="PermissionsForLists"> </a>

O escopo de solicitação de permissão para outros recursos de SharePoint são listados nas tabelas a seguir.
  
    
    

> **OBSERVAçãO**
> Os URIs usados nas tabelas são valores literais.
  
    
    

A tabela 3 mostra o escopo de solicitação de permissão para Business Connectivity Services (BCS). Ele também lista os direitos que podem ser especificados para esse URI de escopo.
  
    
    

**Tabela 3. Escopo de solicitação de permissão do suplemento do BCS URIs e direitos disponíveis**

|||
|:-----|:-----|
|**URI de escopo** <br/> |**Direitos disponíveis** <br/> |
|http://SharePoint/BCS/Connection <br/> |Leitura <br/> |
   

> **OBSERVAçãO**
> Para obter mais informações sobre o escopo de solicitação de permissão do suplemento de BCS, consulte  [Serviços Corporativos de Conectividade do SharePoint 2013](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx).
  
    
    


  
    
    
A tabela 4 mostra o escopo de solicitação de permissão para pesquisa. Ele também lista os direitos que podem ser especificados para esse URI de escopo.
  
    
    

**Tabela 4. Suplemento de permissão de pesquisa direitos disponíveis e escopo URIs de solicitação**

|||
|:-----|:-----|
|**URI de escopo** <br/> |**Direitos disponíveis** <br/> |
|http://SharePoint/Search <br/> |QueryAsUserIgnoreAppPrincipal <br/> |
   

> **OBSERVAçãO**
> Para obter mais informações sobre o escopo de solicitação de permissão do suplemento de pesquisa, consulte  [Pesquisa no SharePoint 2013](http://msdn.microsoft.com/library/59220f81-0e5e-4945-8056-cf0a116446cb%28Office.15%29.aspx).
  
    
    


  
    
    
Tabela 5 mostra o escopo de solicitação de permissão para Project Server 2013. Ela também lista os direitos que podem ser especificados para cada URI de escopo.
  
    
    

> **OBSERVAçãO**
> Um suplemento que usa os serviços e recursos de Project Server 2013 deve ser testado em um ambiente que tem os recursos necessários do Project Server e os serviços. O assembly do provedor de permissão Project Server 2013 sabe sobre escopos de permissão Project Server 2013 não é instalado por padrão com SharePoint Server. Para obter mais informações, consulte a documentação do desenvolvedor de Project Server 2013.
  
    
    


**Tabela 5. Escopo de solicitação de permissão do suplemento do servidor URIs e direitos disponíveis do Project**

|||
|:-----|:-----|
|**escopo** <br/> |**Direitos disponíveis** <br/> |
|http://SharePoint/ProjectServer <br/> |Gerenciar <br/> |
|http://SharePoint/ProjectServer/Projects <br/> |Leitura, Gravação <br/> |
|http://SharePoint/ProjectServer/Projects/Project <br/> |Leitura, Gravação <br/> |
|http://SharePoint/ProjectServer/enterpriseresources <br/> |Leitura, Gravação <br/> |
|http://SharePoint/ProjectServer/statusing <br/> |SubmitStatus <br/> |
|http://SharePoint/ProjectServer/Reporting <br/> |Leitura <br/> |
|http://SharePoint/ProjectServer/Workflow <br/> |Elevar <br/> |
   

  
    
    
Tabela 6 mostra o escopo de solicitação de permissão dos recursos sociais. Ela também lista os direitos que podem ser especificados para cada URI de escopo.
  
    
    

**Tabela 6. Escopo de solicitação de permissão de suplemento URIs de recursos sociais e direitos disponíveis**

|||
|:-----|:-----|
|**URI de escopo** <br/> |**Direitos disponíveis** <br/> |
|http://SharePoint/social/tenant <br/> |Leitura, gravação, gerenciar, controle total <br/> |
|http://SharePoint/social/Core <br/> |Leitura, gravação, gerenciar, controle total <br/> |
|http://SharePoint/social/microfeed <br/> |Leitura, gravação, gerenciar, controle total <br/> |
   

> **OBSERVAçãO**
> Para obter mais informações sobre o escopo de solicitação de permissão do suplemento de recursos sociais, consulte  [Add-in solicitações de permissão para acessar os recursos sociais](http://msdn.microsoft.com/library/8852ce36-8309-45a7-a141-2e10ac17a123%28Office.15%29.aspx#bkmk_AppPerms).
  
    
    


  
    
    
A tabela 7 mostra o escopo de solicitação de permissão para a taxonomia. Ele também lista os direitos que podem ser especificados para esse URI de escopo.
  
    
    

**A tabela 7. Escopo de solicitação de permissão do suplemento de taxonomia URIs e direitos disponíveis**

|||
|:-----|:-----|
|**URI de escopo** <br/> |**Direitos disponíveis** <br/> |
|http://SharePoint/Taxonomy <br/> |Leitura, Gravação <br/> |
   

> **OBSERVAçãO**
> Para obter mais informações sobre o escopo de solicitação de permissão do suplemento de taxonomia, consulte  [Adicionar recursos do SharePoint 2013](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx).
  
    
    


### Escopo de solicitação de permissão com propriedades associadas
<a name="AssociatedProperties"> </a>

O escopo de solicitação de permissão de lista tem uma propriedade opcional adicional. O escopo da lista pode levar a uma propriedade com o nome **BaseTemplateId** e um valor inteiro correspondente com um modelo de lista base, conforme mostrado no exemplo de marcação abaixo. Sem uma ID de modelo base, o usuário que instala o suplemento tem a opção de conceder permissão para *uma lista*  de todas as listas na web quando estiver ele. Especificar uma ID de modelo base limita a escolha do usuário ao conjunto de listas que coincidem com o que é especificado pela propriedade **BaseTemplateId**.
  
    
    
A propriedade **BaseTemplateId** é um elemento filho, não é um atributo do elemento **AppPermissionRequest**. O código a seguir mostra como usar a propriedade **BaseTemplateId**.
  
    
    



```XML

<AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write">
  <Property Name="BaseTemplateId" Value="101"/>
</AppPermissionRequest>
```


**A tabela 7. Escopo de solicitação de permissão com propriedades associadas**

||||
|:-----|:-----|:-----|
|**URI de escopo** <br/> |**Propriedade** <br/> |**Tipo** <br/> |
|http://sharepoint/content/sitecollection/web/list <br/> |**BaseTemplateId** <br/> |Inteiro <br/> > **OBSERVAçãO**> Para obter mais informações sobre **BaseTemplateId** e o valor de inteiro correspondente para o modelo de lista de base, consulte o atributo **Type** do [Elemento de lista (lista)](http://msdn.microsoft.com/library/b2b26fee-eb45-48ac-99f1-65f725da293f%28Office.15%29.aspx).          |
   

## Gerenciar e solucionar problemas de permissões de suplemento
<a name="Perm_manage"> </a>

Suplementos do SharePoint que são instalados para SharePoint são concedidos com permissões quando estiverem instalados. Suplementos que são instalados em outras plataformas, mas acessar SharePoint, são concedidos com permissões em tempo de execução, pelo usuário que está executando o suplemento ocasionalmente, o primeiro tipo de suplemento poderá perder suas permissões. Suplementos podem ser uma nova concessão suas permissões com as seguintes etapas:
  
    
    

1. Na página **Conteúdo do Site** do site onde o suplemento parece ter perdido permissões, clique no botão de **…** no lado a lado do suplemento. Esse procedimento abrirá um texto explicativo com um link de **PERMISSÕES** ou outro botão **…**.
    
  
2. Clique no link de **PERMISSÕES** se ele estiver listada e ignorar a próxima etapa ou clique no botão **…**.
    
  
3. Clique no link de **permissões**.
    
  
4. Na página que é aberta, clique em link **aqui** na última sentença. Isso regrant o suplemento suas permissões e redirecionar o navegador de volta para a página de **Conteúdo do Site**.
    
  

  
    
    
![Conceder nova permissão para um aplicativo](images/RegrantPermissionsToAnApp.png)
  
    
    
Quando você estiver desenvolvendo um suplemento ou solução de problemas um add-in, pode haver ocasiões em que você deseja alterar ou regrant, as permissões de um suplemento que já tenha sido instalado. Você pode fazer isso com estas etapas:
  
    
    

  
    
    

1. Navegue até  `http://<SharePointWebSite>/_layouts/15/AppInv.aspx`, onde  _<SharePointWebSite>_ é a URL do site em que o suplemento está instalado. Tenha cuidado para não adicionar quaisquer parâmetros de consulta na URL. O formulário que você precisa somente aparecerá nessa página, se a URL for exatamente como mostrado.
    
  
2. Insira ID do suplemento, também chamado de ID do cliente, na caixa **Id do suplemento** e clique em **pesquisa**. As outras caixas no formulário, em seguida, são preenchidas com informações sobre o suplemento.
    
  
3. Preencha a caixa de **XML de solicitação de permissão** com solicitações de permissão, exatamente como deverá ser inserido em um suplemento manifesto. Para obter exemplos, consulte [Escopos de solicitação de permissão de conteúdo de lista e conteúdo de biblioteca](#PermissionsForLists) de acima. Para informações sobre a sintaxe completa, consulte [Elemento AppPermissionRequest](http://msdn.microsoft.com/library/4ad90fb0-33b2-aee5-69c2-5b97ca5334f8%28Office.15%29.aspx).
    
  
4. Clique em **Criar**.
    
  
Permissões de um suplemento para um escopo específico são revogadas quando ele é removido do escopo.
  
    
    

## Saiba por que os suplementos não podem ser ocultados dos usuários
<a name="CannotBeHidden"> </a>

Qualquer usuário com direitos de navegar para um site SharePoint pode iniciar qualquer Suplemento do SharePoint instalado no site. Se o usuário pode fazer nada com o suplemento depende o usuário o outras permissões e qual  [a diretiva de autorização digite](add-in-authorization-policy-types-in-sharepoint-2013.md) está sendo usada pelo suplemento da. Se o usuário tentar fazer algo com o suplemento que o usuário não tem permissão para fazer e a chamada para SharePoint está usando o usuário + add-in da política, a chamada irá falhar.
  
    
    

## Recursos adicionais
<a name="Filename_AdditionalResources"> </a>


-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  
-  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
