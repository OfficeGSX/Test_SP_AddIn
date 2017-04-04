---
title: Obter a identidade do usuário e propriedades no SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9d7805e5-5ea8-4309-ba6a-d629281535af
---


# Obter a identidade do usuário e propriedades no SharePoint 2013
Recupere a identidade do usuário e as informações do usuário em SharePoint 2013.
Há maneiras diferentes para recuperar a identidade do usuário e informações, dependendo de quais informações você deseja recuperar. Este artigo mostra algumas das maneiras que pode ser feito.
  
    
    


## Pré-requisitos para recuperar a identidade do usuário e propriedades
<a name="Prereq"> </a>


- Prepare seu ambiente de desenvolvimento do suplemento, conforme descrito em  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
    
  
- Instalar o Visual Studio
    
  
- Instale a versão mais recente do  [Office Developer Tools para Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013) ou [Office Developer Tools para Visual Studio de 2015](http://aka.ms/OfficeDevToolsForVS2015).
    
  

> **OBSERVAçãO**
> Para obter orientação sobre como configurar um ambiente de desenvolvimento que atenda às suas necessidades, consulte  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md).
  
    
    


### Principais conceitos saber para recuperar a identidade do usuário e propriedades

A tabela a seguir lista alguns artigos úteis que podem ajudá-lo a entender os conceitos envolvidos na criação de Suplementos do SharePoint.
  
    
    


|**Artigo**|**Descrição**|
|:-----|:-----|
| [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md) <br/> |Saiba mais sobre o suplemento permissões do SharePoint para trabalhar com SharePoint 2013, incluindo os tipos de suplemento permissões, escopos de solicitação de permissão e gerenciar as permissões. Este artigo também descreve as diferenças em direitos de permissão de suplemento e direitos de usuário. <br/> |
| [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md) <br/> |Saiba mais sobre o fluxo de autenticação e autorização do OAuth para hospedado em nuvem suplementos. <br/> |
| [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md) <br/> |Saiba como criar um básico hospedado em provedor Suplemento do SharePoint com o Office Developer Tools para Visual Studio 2012, como interagir com sites Microsoft SharePoint 2013 usando CSOM do SharePoint e como implementar OAuth em um Suplemento do SharePoint. <br/> |
   

## Recuperando a identidade de usuário do site atual
<a name="WebsiteUserID"> </a>

A maneira mais fácil para recuperar a identidade do usuário atual do site é através do objeto **Web**. Com o arquivo TokenHelper.cs em seu projeto, você pode obter a identidade de usuário atual do site usando o trecho de código a seguir.
  
    
    

```cs

ClientContext clientContext =
                    TokenHelper.GetClientContextWithAccessToken(
                        sharepointUrl.ToString(), accessToken);
 
 
            //Load the properties for the Web object.
            Web web = clientContext.Web;
            clientContext.Load(web);
            clientContext.ExecuteQuery();
 
            //Get the site name.
            siteName = web.Title;
 
            //Get the current user.
            clientContext.Load(web.CurrentUser);
            clientContext.ExecuteQuery();
            currentUser = clientContext.Web.CurrentUser.LoginName;

```


- Se você estiver usando Office 365, o nome de logon que você obtenha é semelhante ao  `i:0#.f|membership|adam@contoso.com`.
    
  
- Se você estiver usando Microsoft SharePoint 2013 no local e o usuário está conectado como um usuário normal usando NTLM, o nome de logon que você obtenha é semelhante ao  `i:0#.w|contoso\\adam`.
    
  
- Se você estiver usando SharePoint 2013 no local e o usuário está conectado usando uma conta do farm, o nome de logon que você obtenha é  `SHAREPOINT\\System`.
    
  

## Recuperando a identidade do usuário usando o método ResolvePrincipal
<a name="ResolvePrincipal"> </a>

A seguir está uma outra maneira de recuperar o nome de logon do usuário. Se você tiver o endereço de email do usuário ou nome de exibição, você pode usar o método  [ResolvePrincipal](https://msdn.microsoft.com/library/Microsoft.SharePoint.Utilities.SPUtility.ResolvePrincipal.aspx) para obter o nome de logon do usuário.
  
    
    

> **OBSERVAçãO**
> As APIs são no namespace Microsoft.SharePoint.Client.Utilities no assembly  [Microsoft.SharePoint.Client.dll](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.client.utilities.utility.resolveprincipal.aspx) .
  
    
    

Veja a seguir exemplos de código para mostrar como obter as informações de logon do usuário.
  
    
    



```cs

ClientResult<Microsoft.SharePoint.Client.Utilities.PrincipalInfo> persons = Microsoft.SharePoint.Client.Utilities.Utility.ResolvePrincipal(clientContext, clientContext.Web, <email>, Microsoft.SharePoint.Client.Utilities.PrincipalType.User, Microsoft.SharePoint.Client.Utilities.PrincipalSource.All, null, true);
                    clientContext.ExecuteQuery();
                    Microsoft.SharePoint.Client.Utilities.PrincipalInfo person = persons.Value;

```

O valor de **Person.LoginName** fornece as informações de logon.
  
    
    

## Recuperar as propriedades de identidade e o perfil de usuário
<a name="Profile"> </a>

Se você deseja recuperar as propriedades e a identidade do usuário, você pode usar o token OAuth e os recursos sociais APIs. Primeiro, obtenha o OAuth token e, em seguida, defina-o como o contexto do cliente. O código de exemplo a seguir mostra como obter propriedades de perfil de usuário.
  
    
    

```cs

ClientContext clientContext = new ClientContext(<sharepointurl>);
clientContext.AuthenticationMode = ClientAuthenticationMode.Anonymous;
clientContext.FormDigestHandlingEnabled = false;
clientContext.ExecutingWebRequest +=
delegate(object oSender, WebRequestEventArgs webRequestEventArgs)
{                      
    webRequestEventArgs.WebRequestExecutor.RequestHeaders["Authorization"] =
        "Bearer " + accessToken;
};

```

Em seguida, use o  [UserProfilesPeopleManager](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.UserProfilesPeopleManager.aspx) API para obter as propriedades do usuário que está usando o suplemento.
  
    
    



```cs

PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties personDetails = peopleManager.GetMyProperties();
clientContext.Load(personDetails, personsD => personsD.AccountName, personsD => personsD.Email,  personsD => personsD.DisplayName);
                clientContext.ExecuteQuery();

```

Para o código funcione:
  
    
    

- Serviço compartilhado do perfil de usuário deve ser configurado e sincronizado em SharePoint 2013 para os usuários.
    
  
- Você deve adicionar o seguinte escopo de permissão recursos sociais no manifesto do suplemento:
    
  ```XML
  
<AppPermissionRequest Right="Read" Scope="http://sharepoint/social/tenant" />

  ```

As APIs são em Microsoft.SharePoint.Client.UserProfiles.dll.
  
    
    
Apresentamos a seguir outro trecho de código de exemplo que mostra como acessar o repositório de perfis de usuário.
  
    
    



```cs

ClientContext clientContext; //Create this like you normally would.               
PeopleManager peopleManager = new PeopleManager(clientContext);
PersonProperties myProperties = peopleManager.GetMyProperties();
clientContext.Load(myProperties);
clientContext.ExecuteQuery();

```


## Recursos adicionais
<a name="AdditionalResources"> </a>


-  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Fluxo do OAuth Token de contexto para o SharePoint Add-ins](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  
-  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  

