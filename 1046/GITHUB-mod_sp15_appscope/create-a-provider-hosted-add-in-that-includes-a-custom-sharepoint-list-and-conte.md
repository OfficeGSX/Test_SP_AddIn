---
title: Criar um provedor hospedado suplemento que inclui uma lista personalizada do SharePoint e o tipo de conteúdo
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# Criar um provedor hospedado suplemento que inclui uma lista personalizada do SharePoint e o tipo de conteúdo
Crie um Suplemento do SharePoint que combina um aplicativo web hospedado na nuvem com modelos de lista personalizado hospedado no SharePoint, instâncias de lista e tipos de conteúdo personalizados usando o Office Developer Tools para Visual Studio 2012. Saiba como interagir com SharePoint 2013 suplemento webs usando o serviço web REST/OData e como implementar OAuth em um Suplemento do SharePoint.
A maioria dos componentes clássico do SharePoint, como tipos de conteúdo personalizados, definições de lista personalizada e fluxos de trabalho, podem ser incluídos em um hospedados em nuvem Suplemento do SharePoint. O exemplo simples neste artigo contém o seguinte:
  
    
    


- Uma web suplemento com
    
  - algumas colunas de site personalizado
    
  
  - um tipo de conteúdo personalizado que usa as colunas personalizadas
    
  
  - um modelo de lista personalizada que usa o tipo de conteúdo personalizado
    
  
  - uma instância de lista com base na definição de lista personalizada
    
  
- Um aplicativo da web de ASP.NET que lê os dados da instância de lista
    
  

## Pré-requisitos para criar este Add-in do SharePoint
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30682) ou posterior.
    
  
-  [Ferramentas de desenvolvedor do Office](https://msdn.microsoft.com/en-us/office/aa905340.aspx)
    
  
- Uma instalação de SharePoint 2013 para testar e depurar
    
  - Isso pode ser no mesmo computador do seu computador de desenvolvimento, ou você pode desenvolver com uma instalação remota SharePoint 2013. Se você trabalha com uma instalação remota, você precisa instalar o modelo de objeto do cliente redistribuível sobre a instalação de destino. Ele está disponível como um pacote redistribuível no Microsoft Download Center. Pesquise por "SDK de componentes do SharePoint Server 2013 cliente" ou "Componentes de cliente Online do SharePoint SDK".
    
  
  - O site do SharePoint de teste deve ser criado da definição de site do **Site de desenvolvedor** (que você pode criar na Administração Central).
    
  
  - Seu aplicativo web remoto se comunica com a web suplemento usando JavaScript e a  [biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md) ou [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md). Se OAuth é usado, como no exemplo contínuo deste artigo, a instalação SharePoint 2013 deverá ser configurada para usar OAuth.
    
  

> [!OBSERVAçãO]
> Para obter orientações sobre como configurar um ambiente de desenvolvimento que atenda às suas necessidades, consulte  [começar a criar o Office e SharePoint suplementos](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx).
  
    
    


### Conceitos centrais saber para a criação de um suplemento

Antes de criar seu primeiro suplemento, você deve ter um Noções básicas sobre as diferenças entre hospedado no SharePoint e hospedados em nuvem Suplementos do SharePoint e quais Suplementos do SharePoint são. Os artigos na tabela 1 devem fornecer esse entendimento.
  
    
    

**Tabela 1. Conceitos básicos para a criação de um suplemento**


|**Título do artigo**|**Descrição**|
|:-----|:-----|
| [Suplementos do SharePoint](sharepoint-add-ins.md) <br/> |Saiba mais sobre o novo modelo suplemento do SharePoint 2013 que permite que você crie suplementos, que são soluções de pequenas e fácil de usar para usuários finais. <br/> |
| [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |Saiba mais sobre aspectos da arquitetura do Suplementos do SharePoint e a modelo de Suplementos do SharePoint, incluindo o suplemento opções de hospedagem, o opções de interface do usuário, o sistema de implantação, o sistema de segurança e o ciclo de vida. <br/> |
| [Escolha os padrões para desenvolver e hospedar o Add-in do SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Saiba mais sobre as diversas maneiras que você pode hospedar Suplementos do SharePoint. <br/> |
   

## Desenvolver o suplemento do SharePoint
<a name="Develop"> </a>

Os procedimentos desta seção, você cria um Suplemento do SharePoint que inclui uma web suplemento com componentes do SharePoint e um aplicativo web remoto na sua máquina de desenvolvimento.
  
    
    

### Para configurar a solução de Visual Studio 2012 e seus elementos


1. No Visual Studio 2012, criar um projeto de **suplemento do SharePoint 2013** a partir do Office SharePoint**| Suplementos** nó (em **c#** ou **Visual Basic** ) na árvore de modelos do Assistente para **Novo projeto**. Escolha o **provedor hospedado** opção de hospedagem. No exemplo contínuo deste artigo, c# é usado como o idioma eLocalTheater é o nome do projeto.
    
  
2. Escolha **Concluir** no assistente.
    
  
3. Abra o arquivo AppManifest.xml no designer de manifesto. O elemento **Title** tem o nome do projeto como seu valor padrão. Substitua por algo mais amigável porque este é o nome do add-in que os usuários visualizam na interface de usuário.
    
  
4. Especifique um **Name** para o suplemento. Este é um nome interno que deve conter apenas caracteres ASCII e deve conter sem espaços; Por exemplo,LocalTheater.
    
  
5. Abra o arquivo Web. config no projeto de aplicativo da web e adicione o elemento  `<customErrors mode="Off"/>` ao elemento **system.web**.
    
  
6. Verifique se as referências para os seguintes conjuntos são no projeto de aplicativo da web. (Se a edição do Visual Studio 2012 não adicionou automaticamente as referências, em seguida, adicioná-los agora.)
    
  - **Microsoft.IdentityModel.dll** este assembly é instalado no cache global de assembly com Windows Identity Foundation (WIF). Porque este é um conjunto de .NET Framework 3.5, ela será filtrada fora o nó **Framework** da caixa de diálogo **Adicionar referência** por padrão. Você pode adicionar uma referência a ele, navegando diretamente para o diretório `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` do seu computador de desenvolvimento.
    
  
  - **Microsoft.IdentityModel.Extensions.dll** você pode adicionar uma referência a ele, navegando diretamente para a pasta `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` do seu computador de desenvolvimento.
    
  
  - **System.IdentityModel.dll** esse assembly faz parte do .NET Framework 4 e ele aparece no **conjuntos | Estrutura** nó da caixa de diálogo **Adicionar referência**.
    
  
7. Se seu aplicativo web remoto acessa informações na web host, bem como a web suplemento, você deve adicionar um elemento de **AppPermissionRequests**, com um ou mais **AppPermissionRequest** elementos filhos, o arquivo AppManifest.xml. (Aplicativo da web no exemplo contínuo deste artigo acessa somente suplemento web. Add-in objetos têm automaticamente todas as permissões necessárias na Web suplemento, então o AppManifest.xml no exemplo não tem um elemento **AppPermissionRequests**.) Para obter mais informações sobre solicitações de permissão de suplemento e como adicioná-las, consulte [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
    
  

### Para adicionar os componentes do SharePoint


1. Você pode adicionar componentes do SharePoint para um suplemento exatamente como você faria adicioná-las a um clássico farm solution. No entanto, nem todos os tipos de componente do SharePoint podem ser incluídos em um Suplemento do SharePoint. Os fins que servem esses componentes são realizados em outras maneiras em Suplementos do SharePoint. Para obter informações detalhadas sobre que tipos de componentes do SharePoint podem ser incluídos em um Suplemento do SharePoint e como para incluí-los em um projeto, consulte  [Tipos de componentes do SharePoint que podem estar em um SharePoint Add-in](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).
    
    Para fins do exemplo contínuo, use os procedimentos a seguir. Esses fornecerá exemplos do uso de Visual Studio 2012 adicionar colunas personalizadas, tipos de conteúdo, modelos de lista e instâncias de lista para uma Suplemento do SharePoint.
    
### Para criar os tipos de coluna personalizada


1. No **Solution Explorer**, adicione um item de **Coluna de Site** do SharePoint para o projeto de Suplemento do SharePoint com o nome doator.
    
  
2. No arquivo Elements para a nova coluna de site, edite o elemento de **Field** para que ele tenha os atributos e valores mostrados no exemplo a seguir, exceto que você não deve alterar o GUID para o atributo **ID** do valor Visual Studio 2012 gerado para ela. Não se esqueça que o enquadramento chaves "{}".
    
  ```
  
<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
  ```

3. Adicione outra **Coluna de Site** para o projeto chamadoCastingStatus.
    
  
4. No arquivo Elements para a nova coluna de site, edite o elemento de **Field** para que ele tenha os atributos e valores mostrados no exemplo a seguir, exceto que você não deve alterar o GUID para o atributo **ID** do valor Visual Studio 2012 gerado para ela.
    
  ```
  
<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
  ```

5. Porque este é um campo de escolha, você deverá especificar as opções possíveis, a ordem em que devem aparecem na lista suspensa quando um usuário está fazendo uma opção e a opção padrão. Adicione a seguinte marcação filho ao elemento **Field**.
    
  ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```


### Para criar o tipo de conteúdo personalizado


1. No **Solution Explorer**, adicione um item de **Tipo de conteúdo** de documentos para o projeto de Suplemento do SharePoint com o nomeActingRole. Quando solicitado pelo Assistente para selecionar o tipo de conteúdo base, escolha o **Item** e, em seguida, escolha **Concluir**.
    
  
2. Se o designer de tipo de conteúdo não abrir automaticamente, escolha o tipo de conteúdo de **ActingRole** no **Solution Explorer** para abri-lo.
    
  
3. Abra a guia de **Tipo de conteúdo** no designer e preencha as caixas de texto como a seguir:
    
  - **Nome do tipo de conteúdo**: ActingRole
    
  
  - **Descrição**: representa um papel em um filme ou reproduzir.
    
  
  - **Nome do grupo**: teatro e filmes
    
  
4. Verifique se que  *Nenhum*  da verificação caixas na guia são selecionadas. A caixa de seleção para **herda as colunas do tipo de conteúdo pai** pode ser selecionada por padrão. *Certifique-se de limpar o proprietário.* 
    
  
5. Abra a guia de **colunas** no designer.
    
  
6. Use a grade para adicionar as colunas de dois site ao tipo de conteúdo. Elas estão listadas na lista suspensa por seus nomes de exibição: **Ator/Actress** e **CastingStatus**. (Se eles não estiverem listados, você pode não ter salvo o projeto desde a adição das colunas de site personalizado. Escolha **Salvar tudo** ).
    
  
7. Salve o arquivo e feche o designer.
    
  
8. A próxima etapa requer que você trabalhe diretamente no XML bruto para o tipo de conteúdo, portanto no **Solution Explorer**, escolha o filho de arquivo Elements o **ActingRole** do tipo de conteúdo.
    
  
9. Já existem **FieldRef** elementos no arquivo de duas colunas que você adicionou. Adicione elementos de **FieldRef** para duas colunas internas SharePoint 2013 como pares das duas que já estão lá. A seguir está a marcação para os elementos. *Você deve usar esses mesmas GUIDs para o atributo ID porque esses são os tipos de campo interno com IDs fixos.*  Adicione esses elementos de **FieldRef** *acima*  das duas para as colunas de site personalizado.
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  ```


    Observe que apresentamos esses campos um nome de exibição personalizado: **caractere**, no sentido de um caractere em um filme ou reproduzir.
    
  

### Para criar o modelo de lista personalizada e uma instância de lista


1. Adicione um item de **lista** do SharePoint para o projeto de Suplemento do SharePoint com o nomeCharactersInShow. Na página **Escolher configurações de lista** do **Assistente de personalização do SharePoint**, deixe o nome de exibição de lista no padrão **CharactersInShow**, escolha o botão de opção de **criar uma lista personalizável com base em** e escolha **padrão (em branco)** na lista suspensa. Escolha **Concluir**.
    
  
2. Quando você concluir o assistente, um modelo de lista de **CharactersInShow** é criado com uma instância de lista filho chamada **CharactersInShowInstance**. Um designer de lista pode ter aberto por padrão. Ele é usado em uma etapa posterior.
    
  
3. Abra o filho Elements **CharactersInShow** modelo de lista ( *não*  o filho Elements a **CharactersInShowInstance** ).
    
  
4. Adicionar espaços ao atributo **DisplayName** para torná-lo mais amigável:"caracteres em Mostrar".
    
  
5. Defina o atributo **Description** como"Os caracteres em um filme ou reproduzir."
    
  
6. Mantenha todos os outros atributos com seu padrão, salve o arquivo e feche-o.
    
  
7. Se o designer de lista não estiver aberto, escolha o nó **CharactersInShow** no **Solution Explorer**.
    
  
8. Abra a guia **colunas** no designer e, em seguida, escolha o botão de **Tipos de conteúdo**.
    
  
9. Na caixa de diálogo **Configurações de tipo de conteúdo**, adicione o tipo de conteúdo de **ActingRole**.
    
  
10. Escolha o tipo de conteúdo de **ActingRole** na lista de tipos e escolha o botão **Definir como padrão**.
    
    Escolha o tipo de conteúdo do **Item**, clique com botão direito pequena seta que aparece à esquerda do nome do tipo de conteúdo e em seguida, escolha **Excluir**.
    
  
11. Repita a etapa anterior para o tipo de conteúdo de **pasta**, portanto, **ActingRole** é o tipo de conteúdo só listado. Escolha **OK** para fechar a caixa de diálogo.
    
  
12. Três colunas agora estão na lista de colunas. Escolha o **título**, a ponta de seta pequena que aparece à esquerda do nome do tipo de conteúdo de atalho e escolha **Excluir**. Apenas duas colunas agora devem estar listadas, **Ator/Actress** e **Status de projeção**.
    
  
13. Abra a guia de **lista** do designer. Esta guia é usada para definir certos valores para a lista *instância*  , não na lista *modelo*  .
    
  
14. Altere os valores dessa guia para o seguinte:
    
  - **Título**: caracteres em Hamlet
    
  
  - **URL da lista**: Listas/CharactersInHamlet
    
  
  - **Descrição**: os caracteres em Hamlet e informações de projeção.
    
  

    Deixe as caixas de seleção com seu status padrão, salve o arquivo e feche o designer.
    
  
15. A instância de lista pode ter seu antigo nome no **Solution Explorer**. Em caso afirmativo, abrir o menu de atalho para **CharactersInShowInstance**, escolha **Renomear** e altere o nome paraCharactersInHamlet.
    
  
16. Abra o arquivo schema.xml.
    
  
17. Pode haver dois elementos **ContentType** no arquivo, uma com o valor do atributo **Name** daActingRole e outro chamado **ListFieldsContentType**. Portanto, apenas a uma chamada ActingRole pertence, exclua quaisquer outros elementos de **ContentType**.
    
    > [!OBSERVAçãO]
      > Não haja quebras de linha entre os elementos de **ContentType**, nesse caso pode parecer a primeira que não há apenas uma. Role para a direita e marque cuidadosamente para que outras pessoas.
18. O elemento **Fields** deve ter dois elementos de **Field** (que não são em uma única linha se não houver nenhuma quebra de linha entre eles). Um exatamente deve duplicar o elemento **Field** no elements de coluna de site do **ator** e o outro deverá duplicar exatamente o elemento **Field** o elements de coluna de site **CastingStatus**. Se houver não uma correspondência exata, incluindo todos os elementos de filho (como o **CHOICES** e **MAPPINGS** elementos), copiar o elemento **Field** do arquivo de Elements de coluna do site e cole-o no lugar o elemento de incompatíveis **Field** no arquivo Schema.
    
  
19. Ainda no arquivo Schema, no elemento **View** cujo valor de BaseViewID é "0", substitua o elemento **ViewFields** existente a marcação a seguir. (Use exatamente esse GUID **FieldRef** denominada `LinkTitle`).
    
  ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
  ```

20. Substitua os dois valores de atributo ID ausentes os GUIDs nos arquivos respectivos sites coluna Elements. Não se esqueça que o enquadramento chaves "{}".
    
  
21. Ainda no arquivo Schema, no elemento **View** cujo valor de BaseViewID é "1", substitua o elemento **ViewFields** existente a marcação a seguir. (Use exatamente esse GUID **FieldRef** denominada `LinkTitle`).
    
  ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
  ```

22. Copie os dois elementos de **FieldRef** para `Actor` e `CastingStatus` que você adicionou à exibição anterior a esse elemento **ViewFields** como irmãos do `LinkTitle` **FieldRef**.
    
  
23. Salve e feche o arquivo schema.xml.
    
  
24. Abra o arquivo Elements que é um filho da instância de lista **CharactersInHamlet**.
    
  
25. Preencha a lista com alguns dados iniciais, adicionando a seguinte marcação como um filho do elemento **ListInstance**.
    
  ```
  
<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
  ```

2. No **Solution Explorer**, escolha **Feature1** para abrir o designer de recurso. No designer, defina o **título** parateatro e componentes de dados de filme e defina a **Descrição** comoSite colunas, tipos de conteúdo e instâncias de lista para dados sobre filmes e teatro.. Salve o arquivo e feche o designer.
    
  
3. Se o **Feature1** no **Solution Explorer** não foi renomeada, abrir o menu de atalho, escolha **Renomear** e renomeie-a comoTheaterAndMovieDataComponents.
    
  

### Código o projeto de aplicativo web remoto


- Desenvolva o aplicativo da web, como faria com qualquer outro aplicativo web sua pilha de plataforma preferencial. Para uma pilha de Microsoft, você pode usar o serviço web REST/OData ou em um dos modelos de objeto do cliente em SharePoint 2013. Para uma pilha de não sejam da Microsoft, você pode usar os pontos de extremidade do resto/OData no SharePoint 2013 para executar operações de (CRUD) criar/leitura/atualização/excluir dados em web suplemento.
    
    > [!OBSERVAçãO]
      > Quando você adiciona uma referência a um assembly ao seu projeto de aplicativo da web em Visual Studio, defina a propriedade **Cópia Local** do assembly como **True**, a menos que você sabe que o conjunto já está instalado no servidor web, ou você pode garantir que ele está instalado antes de implantar o seu suplemento. O .NET Framework é instalado em funções da Web de Microsoft Azure e Azure Web Sites. Mas os conjuntos de cliente SharePoint 2013 e o Microsoft várias extensões de código gerenciado e fundações não estão instaladas. Office Developer Tools para Visual Studio 2012 automaticamente adiciona referências a alguns conjuntos comumente usados no Suplementos do SharePoint e define a propriedade **Cópia Local**.

    Para o exemplo contínuo, você desenvolver um aplicativo web do ASP.NET. Siga as etapas abaixo.
    
1. Abra o arquivo default. aspx e substitua o elemento de corpo do arquivo com a seguinte marcação. A marcação adiciona um botão **obter a conversão** que, quando escolhido, lê a lista de **caracteres em Hamlet** que está na web suplemento e apresenta seus dados em um [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) controle que é exibido somente depois que o botão é pressionado.
    
  ```HTML
  
<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
  ```

2. Abra o arquivo Default.aspx.cs e adicione as seguintes instruções de **using** -lo.
    
  ```cs
  
using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
  ```


    A última destas instruções se refere ao namespace que é declarado no arquivo TokenHelper.cs.
    
  
3. Adicione os campos a seguir à classe **Default**.
    
  ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
  ```

4. Substitua o método de **Page_Load** com o seguinte código que usa a classe **TokenHelper** para obter tokens do servidor token seguro OAuth compatíveis. O token de acesso é então armazenado na propriedade [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) do botão para recuperação posterior por do botão Clique manipulador de eventos.
    
  ```cs
  
protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;
        
        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
  ```

5. Adicione o seguinte manipulador de eventos à classe **Default**. O manipulador começa recuperando o token de acesso que foi armazenado na propriedade de [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) do botão.
    
  ```cs
  
protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
  ```

6. As necessidades de manipulador para readquirir modificada suplementos do URL da web em postagens, portanto, adicione o seguinte código.
    
  ```cs
  
if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
  ```

7. Adicione a seguinte linha que usa um dos pontos de extremidade REST/OData SharePoint 2013 para obter dados de lista. Neste exemplo, o código lê a lista de **caracteres em Hamlet** que é implantada na Web do suplemento. As APIs para esse serviço facilitam, em uma única linha de código, selecione uma lista e especificar três campos na lista para retornar. Observe que a URL do OData você deve usar os nomes internos dos campos (colunas) em vez de nomes de exibição, para que o código usa `Title`,  `Actor`, e  `CastingStatus` em vez de `Character`,  `Actor/Actress`e  `Casting Status.` para obter mais informações sobre o serviço web REST/OData, consulte [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md).
    
  ```cs
  
// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
  ```

8. Adicione o seguinte código que usa as classes  [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) e [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) do namespace [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) para construir os objetos de solicitação e resposta HTTP.
    
  ```cs
  
// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
  ```

9. Adicione o seguinte código para analisar a resposta formatados ATOM XML. Ele usa as classes do namespace  [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) para analisar os dados que são retornados e construir uma [List<T>](http://msdn2.microsoft.com/PT-BR/library/6sh2ey19) dos itens da lista do SharePoint. (Você também pode usar as classes do namespace [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) .) Observe que, no XML que retorna do SharePoint, os elementos filhos do elemento **entry** mantenha metadados sobre o item da lista. Os dados de linha real de um item de lista do SharePoint são aninhadas duas camadas para baixo no elemento **properties**. Para que o método de extensão [Elements<T>](http://msdn2.microsoft.com/PT-BR/library/bb348465) motivo é usado duas vezes para filtrar os níveis superiores.
    
  ```cs
  
// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
  ```

10. Adicionar a seguinte consulta LINQ para construir uma coleção de  [IEnumerable<T>](http://msdn2.microsoft.com/PT-BR/library/9eekhta0) de um tipo anônimo que tem apenas as propriedades que você precisa e nenhum outro. Observe que, embora o código deve fazer referência para o campo de título do item por seu nome interno `Title`, o nome da propriedade no tipo anônimo, ao qual o valor é atribuída, podem ser nomeados  `Character`. Um efeito disto é que quando o conjunto é vinculado a um controle de grade, o nome mais apropriado **caractere** aparece na página.
    
  ```cs
  
var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

  ```

11. Conclua o manipulador com o seguinte código para vincular os dados a um controle  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) na página. Os cabeçalhos de coluna no padrão para os nomes de propriedade do tipo anônimo grade: `Character`,  `Actor`e  `CastingStatus`. O controle de  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) tem propriedades que permitem que você controle o nome e a formatação de cabeçalhos de coluna, para que você poderia ter **Ator/Actress** e **Status de projeção** para coincidir com os cabeçalhos de coluna no SharePoint. Para simplificar, essas técnicas não são descritas aqui. (Você também pode usar um controle de [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) ).
    
  ```cs
  
GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

  ```

12. Salve todos os arquivos.
    
  

### Para testar e depurar o Add-in do SharePoint


1. Para testar o Suplemento do SharePoint e seu aplicativo web remoto, escolha a tecla F5 no Visual Studio 2012. O aplicativo da web é implantado para IIS Express no host local. O será Suplemento do SharePoint está instalado no site do SharePoint de destino. (No exemplo contínuo, o suplemento remoto faz  *não*  tente interagir com o *host*  na web e de capital suplemento automaticamente tem permissões para web *add-in*  , você *não*  será solicitado a conceder permissões.) A página de **Conteúdo do Site** do seu site do SharePoint de destino é aberta e você verá o novo suplemento listado lá.
    
  
2. Escolha o Suplemento do SharePoint e o aplicativo web remoto abre a página que você especificou no elemento **StartPage** no arquivo AppManifest.xml. Use o aplicativo da web conforme necessário para verificar se ele está funcionando. No exemplo contínuo deste tópico, basta escolha o botão. Isso cria uma grade e preenche-a com a lista de **caracteres em Hamlet** da web suplemento.
    
  

## Publicar o suplemento do SharePoint
<a name="Publish"> </a>

Para publicar seu Suplemento do SharePoint, carregue o pacote do suplemento a um catálogo de suplemento corporativo ou para o repositório de suplemento do Office. Para obter mais informações, consulte  [Publicação para o Office Store ou o catálogo do suplemento de uma organização](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog) e [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md).
  
    
    

## Solução de problemas
<a name="Troubleshooting"> </a>

Se o suplemento não estiver funcionando, você deve considerar se um erro na marcação CAML está bloqueando a implantação dos componentes do SharePoint. Use um procedimento semelhante ao seguinte, que é baseado no exemplo contínuo, para verificar a implantação.
  
    
    

### Para testar o provisionamento da web suplemento


1. Abra a página de **Configurações do Site** da web host. Na seção **Administração do conjunto de sites**, escolha o link de **hierarquia do Site**.
    
  
2. Na página da **Hierarquia do Site**, consulte seu suplemento listado por sua URL. Não iniciá-la. Em vez disso, copie a URL e usar a URL das etapas restantes.
    
  
3. Navegue até  _URL_of_app_web_/_layouts/15/ManageFeatures.aspx e, na página **Recursos do Site** que é aberta, verifique se **teatro e componentes de dados de filme** está na lista alfabética de recursos em seu Suplemento do SharePoint e que seu status está **ativo**.
    
  
4. Navegue até  _URL_of_app_web_/_layouts/15/mngfield.aspx e, na página **Colunas de Site** que é aberta, verifique se um grupo de **teatro e filmes** está na lista de colunas de site e que ele contém suas novas colunas de site personalizado, **Ator/Actress** e **Status de projeção**.
    
  
5. Navegue até  _URL_of_app_web_/_layouts/15/mngctype.aspx e, na página **Tipos de conteúdo de Site** que é aberta, verifique se um grupo de **teatro e filmes** está na lista de tipos de conteúdo e que ele contém seu novo tipo de conteúdo de **ActingRole**.
    
  
6. Escolha o link para o tipo de conteúdo de **ActingRole**. Na página **Tipo de conteúdo de Site** que é aberta, verifique se o tipo de conteúdo tem os duas novo site tipos de coluna, **Ator/Actress** e **Projeção Status**, e que o item título campo recebeu seu nome de exibição personalizado: **caractere**.
    
  
7. Navegue até  _URL_of_app_web_/_layouts/15/mcontent.aspx e, na página **listas e bibliotecas de Site** que é aberta, verifique se há um link de **Personalizar "caracteres em Hamlet"**.
    
  
8. Escolha o link **Personalizar "caracteres em Hamlet"** e verifique se na página de configurações da lista que o tipo de conteúdo somente para a lista é seu tipo de conteúdo personalizado **ActingRole** e que seu duas novas colunas de site, **Ator/Actress** e **Status de projeção** estão listadas na seção **colunas**. (A coluna título pode aparecer com seu nome interno **título**, em vez do nome de exibição **caractere** que você forneceu-lo).
    
    > [!OBSERVAçãO]
      > Se não houver nenhuma seção **Tipos de conteúdo** na página, você deve habilitar o gerenciamento de tipos de conteúdo. Clique no link **Configurações avançadas**, na página **Configurações avançadas**, ativar o gerenciamento de tipos de conteúdo e clique em **OK**. Você voltará para a página anterior e agora há uma lista de **Tipos de conteúdo de** seção.
9. Próximo à parte superior da página é o **Endereço Web** da lista. Copie esta e colá-lo na barra de endereços do seu navegador e, em seguida, navegue até a lista. Verifique se a lista possui os itens de exemplo que você criou. (A coluna título pode aparecer com seu nome interno **título**, em vez do nome de exibição **caractere** que você forneceu-lo).
    
  

## Páginas seguintes
<a name="NextSteps"> </a>

Este artigo demonstrou como criar um simples híbrido Suplemento do SharePoint com um aplicativo web remoto. Próximas etapas, considere o seguinte:
  
    
    

- Adicionando funcionalidades CRUD para o suplemento com os pontos de extremidade do resto/OData ou um dos modelos de objeto do cliente. Para obter mais informações, consulte  [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md) e [Concluir operações básicas usando o código de biblioteca do cliente SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md).
    
  
- Localizando o Suplemento do SharePoint para outra cultura. Para obter mais informações, consulte  [Localizar os suplementos do SharePoint](localize-sharepoint-add-ins.md).
    
  
- Criando um suplemento de companion Windows Phone que reproduz a funcionalidade do aplicativo da web remoto. Para obter mais informações, consulte  [criar móvel SharePoint suplementos 2013](http://msdn.microsoft.com/en-us/library/office/jj163228.aspx).
    
  

## Recursos adicionais
<a name="SP15createcloud_bk_addlresources"> </a>


-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Introdução à criação de Suplementos do SharePoint hospedados no SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

