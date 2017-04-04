---
title: Tipos de política de suplemento autorização no SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 124879c7-a746-4c10-96a7-da76ad5327f0
---


# Tipos de política de suplemento autorização no SharePoint 2013
Saiba mais sobre as políticas de autorização diferentes para suplementos no SharePoint: política de aplicativo, usuário + política app e política de usuário. Ela também fornece diretrizes para usar a diretiva somente app.
Antes de ler este artigo, você deve estar familiarizado com os artigos  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) e [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md).
  
    
    


## Obtenha uma visão geral da autorização de suplemento tipos de políticas
<a name="Overview"> </a>

SharePoint oferece três tipos de políticas de autorização:
  
    
    

- **Política de usuário**  quando a política de usuário somente é usada, SharePoint verifica apenas as permissões para o usuário. SharePoint usa essa diretiva quando o usuário está acessando recursos diretamente sem usar um suplemento, como quando um usuário primeiro abre home page de um site SharePoint ou acesso SharePoint APIs do PowerShell.
    
    
    
  
- **Usuário + política app**  quando o usuário + app política é usada, SharePoint verifica as permissões do usuário e a entidade de segurança. Verificações de autorização bem-sucedidas apenas se o usuário atual e o suplemento tem permissões para executar a ação em questão.
    
    Um exemplo de quando esta diretiva é usada é quando um Suplemento do SharePoint deseja obter o acesso aos recursos do usuário SharePoint. O código nos componentes remotos do Suplemento do SharePoint deve ser projetado para fazer chamadas de aplicativo para SharePoint + de usuário.
    
    
    
  
- **Política somente App**  quando a política somente app é usada, SharePoint verifica apenas as permissões da entidade de segurança add-in. Seleção de autorização é bem-sucedida somente se o suplemento atual tiver permissões suficientes para executar a ação em questão, independentemente das permissões do usuário atual (se houver).
    
    Um suplemento de aprovação despesas é um exemplo de quando esta diretiva é usada. O suplemento permite que os usuários que não será capazes de aprovar as despesas para aprovar as despesas abaixo de um determinado valor. Consulte o cenário abaixo para obter detalhes.
    
    
    
    > **OBSERVAçãO**
      > Determinadas APIs exigem um contexto de usuário e não podem ser executadas com uma política de somente app. Isso inclui muitas APIs para interagir com Project Server 2013 e para a execução de consultas de pesquisa.

### Veja um exemplo de cenário de um suplemento que usa a diretiva somente app
<a name="Scenario"> </a>

Vamos informa que um gerente de vendas na Contoso, Adam, adquire um despesas envio suplemento que usa a diretiva somente app. Quando o Adam escolhe comprar o suplemento, Adam é solicitado para permitir que o suplemento elevar as permissões do usuário; ou seja, para permitir que o suplemento fazer chamadas somente app para SharePoint. ADAM concede o suplemento a permissão solicitada. Ele então compras licenças suficientes para todas as pessoas de vendas da Contoso para usar o suplemento, e ele instala-lo no site de central SharePoint da equipe de vendas.
  
    
    
Em breve, os vendedores estiverem enviando relatórios de despesas através do novo despesas envio add-in vendedores geralmente não é possível aprovar seus próprios relatórios de despesas, mas eles podem fazer isso ao usar o suplemento porque o Adam concedeu a capacidade de fazer isso para envios de despesas abaixo de US $50 porque ele definido o suplemento para aprovar automaticamente relatórios abaixo de US $50. O suplemento é automaticamente atribui-lo uma tarefa para aprovar os relatórios de US $50 ou mais. Isso poderia ser implementado, oferecendo Suplemento do SharePoint permissão de gravação para uma lista de SharePoint de despesas aprovadas. Porém, entre usuários, somente os recursos humanos gerentes tenham permissão de gravação na lista. O código no suplemento foi projetado para fazer uma chamada de app somente a SharePoint, se a despesa for menor que $50, para adicionar a despesa à lista. Já que as permissões do usuário não são verificadas, envios de qualquer usuário abaixo de US $50 serão automaticamente adicionados à lista de despesas aprovados, mesmo se o usuário não tem permissão de gravação na lista.
  
    
    

  
    
    

### Saiba como suplementos de obtém permissão para usar a política somente app
<a name="Approve"> </a>

Para poder fazer chamadas somente app para SharePoint, o suplemento deve solicitar a capacidade de usar a diretiva somente app no manifesto do suplemento. Para fazer isso, adicionando o atributo **AllowAppOnlyPolicy** ao elemento **AppPermissionRequests** e defini-la como **true**, conforme mostrado na seguinte marcação:
  
    
    

```XML

<AppPermissionRequests AllowAppOnlyPolicy="true">
    ...
</AppPermissionRequests>
```

Um usuário que está instalando o suplemento será solicitado a aprovar essa solicitação. Se o suplemento solicita permissões de escopo de locatário, em seguida, somente um administrador de inquilino pode conceder o uso da diretiva somente app, portanto, somente um administrador de inquilino pode instalar o suplemento. Se o suplemento não pede todas as permissões no escopo do maior do que o conjunto de sites, um administrador de conjunto de sites pode instalar o suplemento. Para obter mais informações sobre escopos de permissão, consulte  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

### Saiba como suplementos tornam chamadas apenas de aplicativo
<a name="AppOnlyCalls"> </a>

A diferença entre uma chamada somente app para SharePoint e um usuário + chamada app é o tipo de token de acesso que está incluído na chamada. O código a seguir mostra como obter o usuário + app e os tokens de acesso somente app em código gerenciado. A codificação detalhada é feita para você no arquivo TokenHelper.cs (ou. vb) que o Office Developer Tools for Visual Studio adicionar automaticamente ao projeto no Visual Studio.
  
    
    

```cs

string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
if (contextTokenString != null)
{
     //Get context token.
     SharePointContextToken contextToken =
          TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);
     Uri sharepointUrl = new Uri(Request.QueryString["SPHostUrl"]);

     //Get user+app access token.
     string accessToken =
          TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;

      ClientContext clientContext =
           TokenHelper.GetClientContextWithAccessToken(sharepointUrl.ToString(), accessToken);

      //Do something. 
       ...
    
      //Get app-only access token.
       string appOnlyAccessToken = 
            TokenHelper.GetAppOnlyAccessToken(contextToken.TargetPrincipalName, 
                              sharepointUrl.Authority, contextToken.Realm).AccessToken;
         //Do something.
         ...
}
```


> **OBSERVAçãO**
> Suplementos que não faça chamadas OAuth autenticado (por exemplo, suplementos que estão apenas JavaScript em execução no suplemento web) não é possível usar a diretiva somente app. Eles podem solicitar a permissão, mas não será capazes de tirar proveito dela porque essas tarefas exigem passando uma OAuth somente app token. Somente suplementos com aplicativos web em execução fora SharePoint podem criar e passar somente app tokens.
  
    
    

Em geral, um usuário atual é necessário estar presentes para uma chamada a ser feita. No caso de política somente app, SharePoint cria um SHAREPOINT\\APP, semelhante ao usuário SHAREPOINT\\SYSTEM existente. Todas as solicitações somente app são feitas por SHAREPOINT\\APP. Não é possível autenticar como SHAREPOINT\\APP por meio da autenticação baseada em usuário.
  
    
    

### Obtenha diretrizes para usando a diretiva somente app
<a name="GuidelinesFor"> </a>

Desde que apenas app chamadas efetivamente elevem os privilégios de usuário, você deve estar conservadora na criação de suplementos que pedem permissão para torná-los. Chamadas devem usar a diretiva somente app somente se:
  
    
    

- O suplemento precisa elevar suas permissões acima ao usuário uma chamada específico; Por exemplo, para aprovar um relatório de despesas sob condições avaliados pelo suplemento.
    
  
- O suplemento não está agindo em nome de qualquer usuário; Por exemplo, um suplemento que realiza tarefas de manutenção noturna em uma biblioteca de documentos SharePoint.
    
  

## Recursos adicionais
<a name="AR"> </a>


-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  
-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

