---
title: Criar hospedado em provedor SharePoint suplementos para acessar dados do SAP usando o Gateway do SAP para o Microsoft
ms.prod: SHAREPOINT
ms.assetid: 6091c7e8-2301-48cb-9400-a882b80f6309
---


# Criar hospedado em provedor SharePoint suplementos para acessar dados do SAP usando o Gateway do SAP para o Microsoft
Saiba como você pode criar um Suplemento do SharePoint que podem acessar dados SAP.
Você pode criar um Suplemento do SharePoint que lê e grava os dados do SAP e, opcionalmente, leituras e gravações SharePoint dados, usando SAP Gateway for Microsoft e biblioteca de autenticação do Windows Azure AD para .NET. Este artigo fornece os procedimentos de como você pode projetar o Suplemento do SharePoint para obter acesso autorizado a SAP.
  
    
    


## Antes de começar

A seguir são pré-requisitos para os procedimentos neste artigo:
  
    
    

- **Um Office 365 Developer Site** em um domínio Office 365 que está associado uma assinatura de Microsoft Azure Active Directory. Consulte [Configurar um ambiente de desenvolvimento para suplementos do SharePoint no Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) ou [Criar um site do desenvolvedor em uma assinatura existente do Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md).
    
  
- **Visual Studio 2013 atualizar 2** ou posterior, que pode ser obtido [Bem-vindo ao Visual Studio 2013](http://msdn.microsoft.com/library/dd831853.aspx).
    
  
- **Microsoft Office Developer Tools for Visual Studio**. A versão que está incluída no 2 de atualização de Visual Studio 2013 ou posterior.
    
  
- **SAP Gateway for Microsoft** é implantada e configurada no Microsoft Azure. Consulte a documentação do [Gateway do SAP para o Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Uma conta organizacional do Microsoft Azure**. Consulte  [Adicionar manualmente o consentimento comum para o seu aplicativo de visualização de APIs do Office 365](http://msdn.microsoft.com/library/95479f73-15d7-426e-abdf-ae2c72b5cd33%28Office.15%29.aspx#bk_CreateOrganizationAccount).
    
    > [!OBSERVAçãO]
      > Login à sua conta do Office 365 (login.microsoftonline.com) para alterar a senha temporária depois que a conta é criada.
- **Ponto de extremidade OData do SAP A** com dados de amostra nela. Consulte a documentação do [Gateway do SAP para o Microsoft](http://go.microsoft.com/fwlink/?LinkId=507635).
    
  
- **Conhecimentos básicos Azure AD.** Consulte o [guia de Introdução do Azure AD](http://msdn.microsoft.com/library/azure/dn655157.aspx).
    
  
- **Conhecimentos básicos criando Suplementos do SharePoint.** Consulte [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md).
    
  
- **Conhecimentos básicos de OAuth 2.0 no Windows Azure AD**. Consulte  [OAuth 2.0 no Windows Azure AD](http://msdn.microsoft.com/library/azure/dn645545.aspx) e tópicos de seu filho.
    
  
 **Código de exemplo:** [SharePoint 2013: usando o Gateway SAP para a Microsoft em um SharePoint suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Using-the-0931abce)
  
    
    

## Entender a autenticação e autorização para SAP Gateway for Microsoft e SharePoint
<a name="AuthOverview"> </a>

OAuth 2.0 no Windows Azure AD permite aos aplicativos acessar vários recursos hospedados pelo Microsoft Azure e SAP Gateway for Microsoft é um deles. Com OAuth 2.0, aplicativos, além de usuários, são entidades de segurança. Entidades de aplicativos requerem autenticação e autorização aos usuários recursos protegidos além (e em alguns casos, em vez de). O processo envolve um OAuth "fluxo" no qual o aplicativo, que pode ser uma Suplemento do SharePoint, obtém um acesso token (e o token de atualização) que é aceita pelo todos os Microsoft Azure-hospedado serviços e aplicativos que estão configurados para usar Azure AD como um servidor de autorização OAuth 2.0. O processo é muito semelhante à maneira como os componentes remotos de um provedor hospedado Suplemento do SharePoint obtém autorização para SharePoint, conforme descrito em  [Criando Add-ins do SharePoint que usam a autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) e seus artigos filho. No entanto, o sistema de autorização ACS usa Microsoft Azure Access Control Service (ACS) como o emissor de token confiável em vez de Azure AD.
  
    
    

> [!DICA]
> Se seu Suplemento do SharePoint acessa SharePoint além acessando SAP Gateway for Microsoft, então ela precisará usar  *ambos os*  sistemas: Azure AD para obter um acesso token SAP Gateway for Microsoft e o sistema de autorização ACS para obter um acesso token para SharePoint. Os tokens de duas fontes não são intercambiáveis. Para obter mais informações, consulte [Opcionalmente, adicione o acesso ao SharePoint para o aplicativo ASP.NET](#SharePoint).
  
    
    

Para obter uma descrição detalhada e diagrama do fluxo de OAuth usado pelos OAuth 2.0 em Azure AD, consulte  [Fluxo de concessão do código de autorização](http://msdn.microsoft.com/library/azure/dn645542.aspx). (Para obter uma descrição semelhante e um diagrama, do fluxo para acessar SharePoint, consulte  [Consulte as etapas no fluxo de Token de contexto](context-token-oauth-flow-for-sharepoint-add-ins.md#OAuth_ProcessFlowSteps).)
  
    
    

## Criar o Suplemento do SharePoint
<a name="AuthOverview"> </a>


### Criar a solução Visual Studio


1. Crie um projeto de **Suplemento do SharePoint** no Visual Studio com as seguintes etapas. (O exemplo contínuo neste artigo pressupõe estiver usando c#; mas você pode iniciar um projeto Suplemento do SharePoint na seção do **Visual Basic** dos modelos de projeto novo também.)
    
1. No Assistente de **novo suplemento do SharePoint**, o nome do projeto e clique em **OK**. No exemplo contínuos, use SAP2SharePoint.
    
  
2. Especifique a URL do domínio do seu Office 365 Developer Site (incluindo uma barra invertida final) como o site de depuração; Por exemplo, https://<O365_domain>.sharepoint.com/. Especifique o **provedor hospedado** como o tipo de suplemento. Clique em **Avançar**.
    
  
3. Escolha um tipo de projeto. No exemplo contínuos, escolha o **Aplicativo de formulários ASP.NET Web**. (Você também pode fazer ASP.NET MVC aplicativos que acessam SAP Gateway for Microsoft.) Clique em **Avançar**.
    
  
4. Escolha Azure ACS como o sistema de autenticação. (O seu Suplemento do SharePoint usará esse sistema se ele acessa SharePoint. Ele não usa esse sistema quando ele acessa SAP Gateway for Microsoft.) Clique em **Concluir**.
    
  
2. Depois que o projeto é criado, você será solicitado a fazer logon na conta de Office 365. Use as credenciais de um administrador de conta; Por exemplo Bob @< O365_domain >. onmicrosoft.com.
    
  
3. Existem dois projetos na solução Visual Studio; projeto adequado Suplemento do SharePoint e um ASP.NET web project de formulários. Adicione o pacote de **Biblioteca de autenticação do Active Directory** (ADAL) ao projeto ASP.NET com estas etapas:
    
1. Com o botão direito na pasta **referências** no projeto do ASP.NET (chamado **SAP2SharePointWeb** no exemplo contínuo) e selecione **Manage NuGet Packages**.
    
  
2. Na caixa de diálogo exibida, selecione **Online** à esquerda. DigiteMicrosoft.IdentityModel.Clients.ActiveDirectory na caixa Pesquisar.
    
  
3. Quando a biblioteca de ADAL aparece nos resultados da pesquisa, clique no botão **instalar** ao lado dela e aceitar a licença quando solicitado.
    
  
4. Adicione o pacote de Json.net ao projeto ASP.NET com estas etapas:
    
1. Digite Json.net na caixa Pesquisar. Se isso produz um número excessivo de ocorrências, tente pesquisar nosNewtonsoft.json.
    
  
2. Quando Json.net aparecer nos resultados da pesquisa, clique no botão **instalar** ao lado dela.
    
  
5. Clique em **Fechar**.
    
  

### Registrar seu aplicativo web com Azure AD


1. Login para o  [portal de gerenciamento do Windows Azure](https://manage.windowsazure.com) com sua conta de administrador do Azure.
    
    > [!OBSERVAçãO]
      > Para fins de segurança, recomendamos contra usando uma conta de administrador ao desenvolver suplementos.
2. Escolha **Do Active Directory** no lado esquerdo.
    
  
3. Clique em seu diretório.
    
  
4. Escolha **aplicativos** (na barra de navegação superior).
    
  
5. Escolha **Adicionar** na barra de ferramentas na parte inferior da tela.
    
  
6. Na caixa de diálogo que é aberta, escolha **Adicionar um aplicativo está desenvolvendo a minha organização**.
    
  
7. Na caixa de diálogo **Adicionar aplicativo**, nomeie o aplicativo. No exemplo contínuos, use ContosoAutomobileCollection.
    
  
8. Escolha a **API de Web de e/ou de aplicativo Web**, como o tipo de aplicativo e, em seguida, clique no botão de seta para a direita.
    
  
9. Na segunda página da caixa de diálogo, use SSL URL de depuração do projeto ASP.NET na solução Visual Studio como a **URL de SIGN-ON**. Você pode encontrar a URL usando as seguintes etapas. ** *(Necessário registrar o suplemento inicialmente com a URL de depuração para que você possa executar o depurador Visual Studio (F5). Quando seu suplemento estiver pronto para preparação, você será registrá-lo novamente com sua URL de Azure Web Site preparo.  [Modificar o suplemento e estágio-lo para o Windows Azure e o Office 365](#Stage).)* **
    
1. Realce o projeto ASP.NET no **Solution Explorer**.
    
  
2. Na janela **Propriedades**, copie o valor da propriedade **URL SSL**. Um exemplo é https://localhost:44300 /.
    
  
3. Cole a **URL de SIGN-ON**, na caixa de diálogo **Adicionar aplicativo**.
    
  
10. Para o **URI de ID de aplicativo**, dar ao aplicativo um URI exclusivo, como o nome do aplicativo adicionado no final da URL SSL; Por exemplo https://localhost:44300/ContosoAutomobileCollection.
    
  
11. Clique no botão de marca de seleção. Abre o painel Azure para o aplicativo com uma mensagem de êxito.
    
  
12. Escolha **CONFIGURE** no início da página.
    
  
13. Role até a **ID do cliente** e fazer uma cópia dela. Você precisará dele para um procedimento posterior.
    
  
14. Na seção de **chaves**, crie uma chave. Ele não será exibido inicialmente. Clique em **Salvar** na parte inferior da página e a chave será visível. Fazer uma cópia dela. Você precisará dele para um procedimento posterior.
    
  
15. Role até **permissões para outros aplicativos** e selecione seu aplicativo de serviço SAP Gateway for Microsoft.
    
  
16. Abra a lista suspensa **Permissões delegadas** e habilite as caixas as permissões para o serviço de SAP Gateway for Microsoft sua Suplemento do SharePoint será necessário.
    
  
17. Clique em **Salvar** na parte inferior da tela.
    
  

### Configure o aplicativo para se comunicar com Azure AD


1. No Visual Studio, abra o arquivo Web. config do projeto do ASP.NET.
    
  
2. Na seção  `<appSettings>` , o Office Developer Tools for Visual Studio adicionou elementos para o **ClientID** e **ClientSecret** do Suplemento do SharePoint. (Eles são usados no sistema de autorização de Azure ACS se o aplicativo ASP.NET acessa SharePoint. Você poderá ignorá-las para o exemplo contínuo, mas não exclui-los. Eles são necessários na hospedado em provedor Suplementos do SharePoint, mesmo se o suplemento não acessando SharePoint dados. Seus valores mudará sempre que você pressionar F5 em Visual Studio.) Adicione os dois elementos a seguintes para a seção. Estas são usadas pelo aplicativo se autentiquem no Azure AD. (Lembre-se de que aplicativos, bem como os usuários, são entidades de segurança nos sistemas de autenticação e autorização OAuth.)
    
  ```
  
<add key="ida:ClientID" value="" />
<add key="ida:ClientKey" value="" />
  ```

3. Insira a ID do cliente que você salvou do seu diretório Azure AD no procedimento anterior, como o valor da chave **ida:ClientID**. Deixe a cobertura e pontuação, exatamente como você copiá-lo e cuidado para não incluir um caractere de espaço no início ou no final do valor. Para **ida:ClientKey** chave uso da *chave*  que você salvou a partir do diretório. Novamente, tenha cuidado para não introduzir quaisquer caracteres de espaço ou altere o valor de nenhuma maneira. A seção `<appSettings>` agora deve ser parecida com o seguinte. (O valor de **ClientId** pode ter um GUID ou uma sequência vazia).
    
  ```
  
<appSettings>
  <add key="ClientId" value="" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
</appSettings>
  ```


    > [!OBSERVAçãO]
      > Seu aplicativo é conhecido como Azure AD pela URL "localhost" você usou para registrá-lo. A ID do cliente e a chave do cliente estão associados essa identidade. Quando você estiver pronto para preparar seu aplicativo para um Azure Web Site, você irá registrá-lo novamente com uma nova URL.
4. Ainda na seção **appSettings**, adicione uma chave **Authority** e defina seu valor como o domínio Office 365 ( *some_domain*  . onmicrosoft.com) da sua conta organizacional. No exemplo contínuo, a conta organizacional é Bob @< O365_domain >. onmicrosoft.com, portanto, a autoridade é `<O365_domain>.onmicrosoft.com`.
    
  ```
  
<add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  ```

5. Ainda na seção **appSettings**, adicione uma chave **AppRedirectUrl** e defina seu valor para a página que o navegador do usuário deve ser redirecionado para depois que o suplemento do ASP.NET obteve um código de autorização de Azure AD. Normalmente, isso é a mesma página que o usuário estava no quando a chamada para Azure AD foi feita. No exemplo contínuo, use o valor de URL de SSL com "/ /Pages/default.aspx." acrescentado a ele, conforme mostrado abaixo. (Isto é outro valor que você irá alterar para preparação).
    
  ```
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  ```

6. Ainda na seção **appSettings**, adicione uma chave **ResourceUrl** e defina seu valor para o URI de ID de aplicativo do SAP Gateway for Microsoft ( *não*  o URI de ID de aplicativo do seu aplicativo ASP.NET). Obter esse valor do administrador SAP Gateway for Microsoft. O exemplo a seguir é um exemplo.
    
  ```
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
  ```


    A seção  `<appSettings>` agora deve ser semelhante esta:
    


  ```
  <appSettings>
  <add key="ClientId" value="06af1059-8916-4851-a271-2705e8cf53c6" />
  <add key="ClientSecret" value="LypZu2yVajlHfPLRn5J2hBrwCk5aBOHxE4PtKCjIQkk=" />
  <add key="ida:ClientID" value="4da99afe-08b5-4bce-bc66-5356482ec2df" />
  <add key="ida:ClientKey" value="URwh/oiPay/b5jJWYHgkVdoE/x7gq3zZdtcl/cG14ss=" />
  <add key="Authority" value="<O365_domain>.onmicrosoft.com" />
  <add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />
  <add key="ResourceUrl" value="http://<SAP_gateway_domain>.cloudapp.net/" />
</appSettings>
  ```

7. Salve e feche o arquivo Web. config.
    
    > [!DICA]
      > Não deixe o arquivo Web. config aberta quando você executa o depurador Visual Studio (F5). O Office Developer Tools for Visual Studio altere o valor de **ClientId** (não o **ida:ClientID**) sempre que você pressionar F5. Isso requer que você responder a uma pergunta ao recarregar o arquivo Web. config, se ele estiver aberto, antes de depuração pode ser executado.

### Adicionar uma classe auxiliar se autentiquem no Azure AD.


1. Com o botão direito do projeto do ASP.NET e usar o item de Visual Studio adicionando o processo para adicionar um novo arquivo de classe ao projeto nomeada AADAuthHelper.cs.
    
  
2. Adicione as seguintes instruções **using** ao arquivo.
    
  ```
  
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using System.Web.UI;

  ```

3. Alterar a palavra-chave de acesso de **public** para **internal** e adicionar a palavra-chave **static** para a declaração de classe.
    
  ```
  
internal static class AADAuthHelper
  ```

4. Adicione os seguintes campos à classe. Esses campos armazenam informações que seu aplicativo ASP.NET usa para obter tokens de acesso do AAD.
    
  ```
  private static readonly string _authority = ConfigurationManager.AppSettings["Authority"];
private static readonly string _appRedirectUrl = ConfigurationManager.AppSettings["AppRedirectUrl"];
private static readonly string _resourceUrl = ConfigurationManager.AppSettings["ResourceUrl"];     
private static readonly string _clientId = ConfigurationManager.AppSettings["ida:ClientID"];        
private static readonly ClientCredential _clientCredential = new ClientCredential(
                           ConfigurationManager.AppSettings["ida:ClientID"],
                           ConfigurationManager.AppSettings["ida:ClientKey"]);

private static readonly AuthenticationContext _authenticationContext = 
            new AuthenticationContext("https://login.windows.net/common/" + 
                                      ConfigurationManager.AppSettings["Authority"]);
  ```

5. Adicione a seguinte propriedade à classe. Essa propriedade contém a URL para a tela de login Azure AD.
    
  ```
  
private static string AuthorizeUrl
{
    get
    {
        return string.Format("https://login.windows.net/{0}/oauth2/authorize?response_type=code&amp;redirect_uri={1}&amp;client_id={2}&amp;state={3}",
            _authority,
            _appRedirectUrl,
            _clientId,
            Guid.NewGuid().ToString());
    }
}

  ```

6. Adicione as seguintes propriedades para a classe. Esses os tokens de acesso e a atualização de cache e verifique sua validade.
    
  ```
  
public static Tuple<string, DateTimeOffset> AccessToken
{
    get {
return HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] 
       as Tuple<string, DateTimeOffset>;
    }

    set { HttpContext.Current.Session["AccessTokenWithExpireTime-" + _resourceUrl] = value; }
}

private static bool IsAccessTokenValid
{
   get 
   { 
       return AccessToken != null &amp;&amp;
       !string.IsNullOrEmpty(AccessToken.Item1) &amp;&amp;
       AccessToken.Item2 > DateTimeOffset.UtcNow;
   }
}

private static string RefreshToken
{
    get { return HttpContext.Current.Session["RefreshToken" + _resourceUrl] as string; }
    set { HttpContext.Current.Session["RefreshToken-" + _resourceUrl] = value; }
}

private static bool IsRefreshTokenValid
{
    get { return !string.IsNullOrEmpty(RefreshToken); }
}

  ```

7. Adicione os seguintes métodos para a classe. Eles são usados para verificar a validade do código de autorização e para obter um token de acesso de Azure AD usando um token de atualização ou um código de autenticação.
    
  ```
  
private static bool IsAuthorizationCodeNotNull(string authCode)
{
    return !string.IsNullOrEmpty(authCode);
}

private static Tuple<Tuple<string,DateTimeOffset>,string> AcquireTokensUsingAuthCode(string authCode)
{
    var authResult = _authenticationContext.AcquireTokenByAuthorizationCode(
                authCode,
                new Uri(_appRedirectUrl),
                _clientCredential,
                _resourceUrl);

    return new Tuple<Tuple<string, DateTimeOffset>, string>(
                new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn), 
                authResult.RefreshToken);
}

private static Tuple<string, DateTimeOffset> RenewAccessTokenUsingRefreshToken()
{
    var authResult = _authenticationContext.AcquireTokenByRefreshToken(
                         RefreshToken,
                         _clientCredential.OwnerId,
                         _clientCredential,
                         _resourceUrl);

    return new Tuple<string, DateTimeOffset>(authResult.AccessToken, authResult.ExpiresOn);
}

  ```

8. Adicione o seguinte método à classe. Ele é chamado do ASP.NET code-behind para obter um token de acesso válido antes de uma chamada for feita para obter dados SAP via SAP Gateway for Microsoft.
    
  ```
  
internal static void EnsureValidAccessToken(Page page)
{
    if (IsAccessTokenValid) 
    {
        return;
    }
    else if (IsRefreshTokenValid) 
    {
        AccessToken = RenewAccessTokenUsingRefreshToken();
        return;
    }
    else if (IsAuthorizationCodeNotNull(page.Request.QueryString["code"]))
    {
        Tuple<Tuple<string, DateTimeOffset>, string> tokens = null;
        try
        {
            tokens = AcquireTokensUsingAuthCode(page.Request.QueryString["code"]);
        }
        catch 
        {
            page.Response.Redirect(AuthorizeUrl);
        }
        AccessToken = tokens.Item1;
        RefreshToken = tokens.Item2;
        return;
    }
    else
    {
        page.Response.Redirect(AuthorizeUrl);
    }
}
  ```


> [!DICA]
> A classe AADAuthHelper tem o tratamento de erros somente mínimos. Para uma robusta, produção qualidade Suplemento do SharePoint, adicione o tratamento de erros mais conforme descrito neste nó do MSDN:  [Error Handling in OAuth 2.0](http://msdn.microsoft.com/library/561bf289-3ff9-4eea-b165-4f5f02bcc520.aspx).
  
    
    


### Criar classes de modelo de dados


1. Crie uma ou mais classes para modelar os dados que seu suplemento obtém do SAP. No exemplo contínuo, há apenas uma classe de modelo de dados. Com o botão direito do projeto do ASP.NET e usar o item de Visual Studio adicionando o processo para adicionar um novo arquivo de classe ao projeto nomeada Automobile.cs.
    
  
2. Adicione o código a seguir para o corpo da classe:
    
  ```
  
public string Price;
public string Brand;
public string Model;
public int Year;
public string Engine;
public int MaxPower;
public string BodyStyle;
public string Transmission;
  ```


### Adicione código por trás para obter os dados do SAP via o SAP Gateway for Microsoft


1. Abra o arquivo Default.aspx.cs e adicione as seguintes instruções **using**.
    
  ```
  
using System.Net;
using Newtonsoft.Json.Linq;
  ```

2. Adicione uma declaração de **const** à classe padrão cujo valor é a URL base do ponto de extremidade OData do SAP que o suplemento acessarão. Este é um exemplo:
    
  ```
  
private const string SAP_ODATA_URL = @"https://<SAP_gateway_domain>.cloudapp.net:8081/perf/sap/opu/odata/sap/ZCAR_POC_SRV/";
  ```

3. O Office Developer Tools for Visual Studio adicionou um método de **Page_PreInit** e um método **Page_Load**. Comente o código dentro do método **Page_Load** e comente o método todo **Page_Init**. Este código não é usado neste exemplo. (Se sua Suplemento do SharePoint vai acessar SharePoint, você restaurar este código. Consulte [Opcionalmente, adicione o acesso ao SharePoint para o aplicativo ASP.NET](#SharePoint).)
    
  
4. Adicione a seguinte linha à parte superior do método **Page_Load**. Isso irá facilitar o processo de depuração porque seu aplicativo ASP.NET está se comunicando com SAP Gateway for Microsoft usando SSL (HTTPS); mas seu servidor de "localhost:port" não está configurado para confiar no certificado da SAP Gateway for Microsoft. Sem esta linha de código, você obterá um aviso de certificado inválido antes de que abrirá o default. aspx. Alguns navegadores permitem que você clique depois desse erro, mas algumas não permitirá que você abra Default. aspx nisso.
    
  ```
  ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;
  ```


    > [!IMPORTANTE]
      > Exclua essa linha quando estiver pronto para implantar o aplicativo ASP.NET para preparação. Consulte  [Modificar o suplemento e estágio-lo para o Windows Azure e o Office 365](#Stage).
5. Adicione o seguinte código para o método **Page_Load**. A cadeia de caracteres que você passa para o método `GetSAPData` é uma consulta OData.
    
  ```
  if (!IsPostBack)
{
    GetSAPData("DataCollection?$top=3");
}

  ```

6. Adicione o seguinte método à classe de padrão. Esse método primeiro assegura que o cache para o token de acesso tem um token de acesso válido dentro dela que tenha sido obtido de Azure AD. Em seguida, ele cria um HTTP **GET** solicitação que inclui o token de acesso e enviá-la ao ponto de extremidade OData do SAP. O resultado é retornado como um objeto JSON que é convertido em um objeto de **List** do .NET. Três propriedades dos itens são usadas em uma matriz que é vinculada ao **DataListView**.
    
  ```
  
private void GetSAPData(string oDataQuery)
{
    AADAuthHelper.EnsureValidAccessToken(this);

    using (WebClient client = new WebClient())
    {
        client.Headers[HttpRequestHeader.Accept] = "application/json";
        client.Headers[HttpRequestHeader.Authorization] = "Bearer " + AADAuthHelper.AccessToken.Item1;
        var jsonString = client.DownloadString(SAP_ODATA_URL + oDataQuery);
        var jsonValue = JObject.Parse(jsonString)["d"]["results"];
        var dataCol = jsonValue.ToObject<List<Automobile>>();

        var dataList = dataCol.Select((item) => {
            return item.Brand + " " + item.Model + " " + item.Price;
            }).ToArray();

        DataListView.DataSource = dataList;
        DataListView.DataBind();
    }
}

  ```


### Criar a interface do usuário


1. Abra o arquivo default. aspx e adicione a seguinte marcação para o **form** da página:
    
  ```
  
<div>
  <h3>Data from SAP via SAP Gateway for Microsoft</h3>

  <asp:ListView runat="server" ID="DataListView">
    <ItemTemplate>
      <tr runat="server">
        <td runat="server">
          <asp:Label ID="DataLabel" runat="server"
            Text="<%# Container.DataItem.ToString()%>" /><br />
        </td>
      </tr>
    </ItemTemplate>
  </asp:ListView>
</div>
  ```

2. Opcionalmente, dê a página da web a "aparência ' n' se sentir" de uma página de SharePoint com SharePoint  [Controle de cromo](use-the-client-chrome-control-in-sharepoint-add-ins.md) e a [folha de estilo do site do SharePoint host](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md).
    
  

### Testar o suplemento com F5 em Visual Studio


1. Pressione F5 no Visual Studio.
    
  
2. Na primeira vez que você use F5, você precisará fazer logon no Site do Desenvolvedor que você está usando. Use as credenciais de administrador do site. No exemplo contínuo, é Bob @< O365_domain >. onmicrosoft.com.
    
  
3. Na primeira vez que você use F5, que você for solicitado para conceder permissões à add-in, clique em **Confiá-lo**.
    
  
4. Após um pequeno atraso enquanto o token de acesso estiver sendo obtido, abre a página Default. aspx. Verifique se os dados do SAP aparecem.
    
  

## Opcionalmente, adicione o acesso ao SharePoint para o aplicativo ASP.NET
<a name="SharePoint"> </a>

Obviamente, seu Suplemento do SharePoint não tem expor SAP somente os dados em uma página da web iniciado a partir de SharePoint. Ele também pode criar, ler, atualizar e excluir dados de SharePoint (CRUD). Seu code-behind pode fazer isso usando o modelo de objeto do cliente SharePoint (CSOM) ou as APIs REST do SharePoint. O CSOM é implantado como um par de assemblies que o Office Developer Tools for Visual Studio automaticamente incluídos no projeto ASP.NET e defina a **Cópia Local** no Visual Studio para que eles estão incluídos no pacote do aplicativo ASP.NET. Para obter informações sobre como usar o CSOM, comece com [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md). Para obter informações sobre como usar APIs REST, comece com  [Compreendendo e usando a Interface REST do SharePoint 2013](http://msdn.microsoft.com/en-us/magazine/dn198245.aspx).
  
    
    
Seja como for, de se você usar o CSOM ou as APIs REST para acessar SharePoint, seu aplicativo ASP.NET deve obter um token de acesso para SharePoint, assim como faz para SAP Gateway for Microsoft. Consulte  [Entender a autenticação e autorização para SAP Gateway for Microsoft e SharePoint](#AuthOverview) acima. O procedimento a seguir fornece algumas diretrizes básicas sobre como fazer isso, mas é recomendável que você primeiro leia os seguintes artigos:
  
    
    

-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Três sistemas de autorização para o SharePoint Add-ins](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Criando Add-ins do SharePoint que usam a autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)
    
  
-  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  

1. Abra o arquivo Default.aspx.cs e não comentar o método **Page_PreInit**. Também não comentar o código que o Office Developer Tools for Visual Studio adicionado ao método **Page_Load**.
    
  
2. Se sua Suplemento do SharePoint será para acessar dados do SharePoint, em seguida, você terá para armazenar em cache o token de contexto do SharePoint que é postado até a página aspx quando o suplemento é iniciado no SharePoint. Isso é para garantir que o token de contexto do SharePoint não é perdido quando o navegador é redirecionado após a autenticação Azure AD. (Você tem várias opções para como armazenar em cache nesse contexto.) O Office Developer Tools for Visual Studio adicionar um arquivo de SharePointContext.cs ao projeto ASP.NET que faz a maior parte do trabalho. Para usar o cache de sessão, simplesmente adicione o seguinte código dentro a " `if (!IsPostBack)`" bloquear  *antes do*  código que chama o check-out para SAP Gateway for Microsoft:
    
  ```
  
if (HttpContext.Current.Session["SharePointContext"] == null)
{
     HttpContext.Current.Session["SharePointContext"]
        = SharePointContextProvider.Current.GetSharePointContext(Context);
}
  ```

3. O arquivo SharePointContext.cs faz chamadas a outro arquivo que o Office Developer Tools for Visual Studio é adicionada ao projeto: TokenHelper.cs. Este arquivo fornece a maioria do código necessário para obter e usar os tokens de acesso para SharePoint. No entanto, ele não oferece nenhum código para renovar um token de acesso expirados ou um token de atualização vencidos. Nem ele contêm qualquer código de cache de token. Para uma qualidade de produção Suplemento do SharePoint, você precisa adicionar esse tipo de código. A lógica de cache na etapa anterior é um exemplo. Seu código também deve armazenar em cache o token de acesso e reutilizá-la até expirar. Quando expira o token de acesso, seu código deve usar o token de atualização para obter um acesso novo token.
    
  
4. Adicione as chamadas de dados para SharePoint usando CSOM ou REST. O exemplo a seguir é uma modificação de código CSOM que Office Developer Tools for Visual Studio adiciona ao método **Page_Load**. Neste exemplo, o código foi movido para um método separado e começa recuperando o token de contexto de cache.
    
  ```
  
private void GetSharePointTitle()
{
    var spContext = HttpContext.Current.Session["SharePointContext"] as SharePointContext;
    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        clientContext.Load(clientContext.Web, web => web.Title);
        clientContext.ExecuteQuery();
        SharePointTitle.Text = "SharePoint web site title is: " + clientContext.Web.Title;
    }
}
  ```

5. Adicione elementos de interface do usuário para processar os dados SharePoint. A seguir mostra o controle HTML que é referenciado no método anterior:
    
  ```
  
<h3>SharePoint title</h3><asp:Label ID="SharePointTitle" runat="server"></asp:Label><br />
  ```


> [!OBSERVAçãO]
> Enquanto você estiver depurando o Suplemento do SharePoint, o Office Developer Tools for Visual Studio registrá-lo novamente com Azure ACS toda vez que pressionar a tecla F5 em Visual Studio. Quando você deve preparar o Suplemento do SharePoint, você precisa dar a ela um registro de longo prazo. Consulte a seção  [Modificar o suplemento e estágio-lo para o Windows Azure e o Office 365](#Stage).
  
    
    


## Modificar o suplemento e estágio-lo para o Windows Azure e o Office 365
<a name="Stage"> </a>

Quando terminar de depuração Suplemento do SharePoint usando F5 no Visual Studio, você precisará implantar o aplicativo ASP.NET para um real Azure Web Site.
  
    
    

### Criar Site do Azure


1. No portal do Microsoft Azure, abra **SITES da WEB** na barra de navegação à esquerda.
    
  
2. Clique em **novo** na parte inferior da página e em **NEW** diálogo Selecione o **SITE da WEB** |**Criar rápida**.
    
  
3. Insira um nome de domínio e clique em **criar SITE da WEB**. Fazer uma cópia da URL do novo site. Ele terá o formulário  `my_domain.azurewebsites.net`.
    
  

### Modificar o código e marcação no aplicativo


1. Em Visual Studio, remova a linha  `ServicePointManager.ServerCertificateValidationCallback = (s, cert, chain, errors) => true;` do arquivo Default.aspx.cs.
    
  
2. Abra o arquivo Web. config do projeto ASP.NET e altere a parte de domínio do valor da chave **AppRedirectUrl** na seção **appSettings** ao domínio do Azure Web Site. Por exemplo, altere `<add key="AppRedirectUrl" value="https://localhost:44322/Pages/Default.aspx" />` para `<add key="AppRedirectUrl" value="https://my_domain.azurewebsites.net/Pages/Default.aspx" />`.
    
  
3. O arquivo AppManifest.xml no projeto Suplemento do SharePoint do mouse em e selecione **Exibir código**.
    
  
4. O valor de **StartPage**, substitua a cadeia de caracteres `~remoteAppUrl` pelo domínio completo do Azure Web Site incluindo o protocolo; Por exemplo `https://my_domain.azurewebsites.net`. O valor de inteiro **StartPage** agora deve ser: `https://my_domain.azurewebsites.net/Pages/Default.aspx`. (Geralmente, o valor de **StartPage** é exatamente o mesmo que o valor da chave **AppRedirectUrl** no arquivo Web. config XML.)
    
  

### Modificar o registro AAD e registrar o suplemento com o ACS


1. Logon no  [portal de gerenciamento do Windows Azure](https://manage.windowsazure.com) com sua conta de administrador do Azure.
    
  
2. Escolha **Do Active Directory** no lado esquerdo.
    
  
3. Clique em seu diretório.
    
  
4. Escolha **aplicativos** (na barra de navegação superior).
    
  
5. Abra o aplicativo que você criou. No exemplo contínuo, é **ContosoAutomobileCollection**.
    
  
6. Para cada um dos seguintes valores, alterar o "localhost: *porta*  " parte do valor ao domínio do seu novo Azure Web Site:
    
  - **URL DE SIGN-ON**
    
  
  - **URI DE ID DE APLICATIVO**
    
  
  - **URL DE RESPOSTA**
    
  

    Por exemplo, se o **URI de ID de aplicativo** for **https://localhost:44304/ContosoAutomobileCollection**, altere-o para **https://<my_domain>.azurewebsites.net/ContosoAutomobileCollection**.
    
  
7. Clique em **Salvar** na parte inferior da tela.
    
  
8. Registre o suplemento com Azure ACS. Isso deve ser feito, mesmo se o suplemento não acessa SharePoint e não usará tokens do ACS, porque o mesmo processo também registra o suplemento com o serviço de gerenciamento de suplemento da assinatura Office 365, que é um requisito. (É chamado "Add-in do serviço de gerenciamento" porque Suplementos do SharePoint originalmente eram chamados de "aplicativos para SharePoint".) Você pode executar o registro na página de qualquer site SharePoint na assinatura do Office 365 AppRegNew.aspx. Para obter detalhes, consulte  [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md). Como parte desse processo, você obterá uma nova ID de cliente e o segredo do cliente. Inserir esses valores em Web. config **ClientId** (não **ida:ClientID**) e teclas de **ClientSecret**.
    
    > [!CUIDADO]
      > Se por alguma razão você pressionar F5 depois de fazer essa alteração, o Office Developer Tools for Visual Studio substituirá uma ou ambas desses valores. Por esse motivo, você deve manter um registro dos valores obtidos com AppRegNew.aspx e sempre verifique se os valores no Web. config estão corretos antes de você publica o aplicativo ASP.NET.

### Publique o aplicativo ASP.NET para o Windows Azure e instalar o suplemento para o SharePoint


1. Há várias maneiras para publicar um aplicativo ASP.NET para um Azure Web Site. Para obter mais informações, consulte  [como implantar um Site do Windows Azure](http://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/).
    
  
2. No Visual Studio, com o botão direito do projeto de suplemento do SharePoint e selecione o **pacote**. Na página **publicar seu suplemento** que é aberta, clique em **pacote o add-in**. Gerenciador de arquivos é aberta para a pasta com o pacote do suplemento.
    
  
3. Login para Office 365 como um administrador global e navegue até o conjunto de sites do catálogo de suplemento de organização. (Se houver uma, criá-lo. Consulte  [Catálogo de usar o suplemento para tornar corporativos personalizados suplementos disponíveis para o seu ambiente do SharePoint Online](http://office.microsoft.com/en-us/sharepoint-help/use-the-app-catalog-to-make-custom-business-apps-available-for-your-sharepoint-online-environment-HA102772362.aspx).)
    
  
4. Carregar o pacote do suplemento para o catálogo do suplemento.
    
  
5. Navegue até a página de **Conteúdo do Site** de qualquer site na assinatura e clique em **Adicionar um suplemento**.
    
  
6. Na página **seus Add-ins**, role até a seção **Add-ins, você pode adicionar** e clique no ícone para seu suplemento.
    
  
7. Após ter instalado o suplemento, clique em seu ícone na página **Conteúdo do Site** para iniciar o add-in.
    
  
Para obter mais informações sobre como instalar o Suplementos do SharePoint, consulte  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md).
## Implantando o suplemento para a produção
<a name="Stage"> </a>

Quando terminar de todos os testes, você podem implantar o suplemento na produção. Isso pode exigir algumas alterações.
  
    
    

1. Se o domínio de produção do aplicativo ASP.NET é diferente do domínio preparo, você terá que alterar o valor de **AppRedirectUrl** no Web. config e o valor de **StartPage** no arquivo AppManifest.xml e remonte o Suplemento do SharePoint. Consulte o procedimento para **modificar o código e marcação no aplicativo** acima.
    
  
2. A alteração no domínio também requer que você edite o registro de suplementos com AAD. Consulte o procedimento para **modificar o registro AAD e registrar o suplemento com o ACS** acima.
    
  
3. A alteração no domínio também requer que você registre novamente o suplemento com ACS (e suplemento de serviço a assinatura de gerenciamento) conforme descrito no mesmo procedimento. (Não há para  *Editar*  registro de um suplemento com o ACS.) No entanto, não é necessário gerar uma nova ID de cliente ou segredo do cliente na página AppRegNew.aspx. Você pode copiar os valores originais do **ClientId** (não **ida:ClientID**) e chaves de **ClientSecret** da Web. config para o formulário AppRegNew. *Se você gerar novos, certifique-se de copiar os novos valores para as chaves no Web. config.* 
    
  

