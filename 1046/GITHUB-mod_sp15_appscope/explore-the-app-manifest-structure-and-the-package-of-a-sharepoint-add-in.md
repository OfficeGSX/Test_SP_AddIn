---
title: Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# Explore a estrutura de manifesto de aplicativo e o pacote de um SharePoint Add-in
Saiba mais sobre a estrutura do pacote de suplemento e o arquivo de manifesto para um Suplemento do SharePoint.
## Add-in para a estrutura do pacote do SharePoint
<a name="Package"> </a>

Um pacote de Suplemento do SharePoint é um arquivo que tem a extensão ". app" e que está em conformidade com as  [Convenções de empacotamento aberto (OPC)](http://msdn.microsoft.com/en-us/magazine/cc163372.aspx). O pacote contém os seguintes itens:
  
    
    

- **Manifesto do suplemento:** Este é um arquivo necessário nomeado appmanifest.xml. Ela informa SharePoint 2013 sobre algumas propriedades importantes do add-in, como seu título e as permissões que precisa ser executado. Para obter mais informações sobre o conteúdo desse arquivo, consulte [Suplemento para o arquivo de manifesto do SharePoint](#AppManifest).
    
  
- **Pacotes de solução do SharePoint:** Opcionalmente, o suplemento pode incluir um pacote de solução do SharePoint (arquivo. wsp) que contém os componentes da web add-in. Esses componentes podem incluir páginas, instâncias da lista, exibições, documentos, **Web**-escopo de recursos e outros componentes de SharePoint 2013. (Para obter mais informações sobre quais componentes do SharePoint podem ser incluídos em um Suplemento do SharePoint, consulte  [Tipos de componentes do SharePoint que podem estar em um SharePoint Add-in](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).) O arquivo. wsp também pode conter Suplementos do Office. Os componentes no arquivo. wsp são implantados na Web do suplemento. Para obter um exemplo de um pacote de suplemento que inclui um pacote de solução do SharePoint, consulte  [Criar um provedor hospedado suplemento que inclui uma lista personalizada do SharePoint e o tipo de conteúdo](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md).
    
  
- **Recursos da web com ações personalizadas ou partes do suplemento do host:** Além dos componentes de SharePoint 2013 que são implantados na Web suplemento, um Suplemento do SharePoint também poderá implantar um ou mais ações personalizadas (itens de menu de atalho ou extensões da faixa de opções) na Web de host. Isso é feito com a inclusão de um recurso que não está dentro do arquivo. wsp do pacote e que implanta os componentes que serão encaminhadas web host no pacote do suplemento. Esse recurso "afastado" é chamado de um recurso da web de host. Suplemento partes são implantados na Web de host da mesma maneira. O recurso de web do host consiste em um arquivo Feature XML padrão SharePoint 2013 e um ou mais arquivos Elements associado. Um arquivo Elements XML para uma ação personalizada, por exemplo, contém a marcação de **CustomAction** para a ação personalizada. Ele também pode incluir marcação para partes do suplemento. Esses dois tipos de componentes podem estar em web host recurso. Esses recursos não estão discriminados no manifesto do suplemento de web de host. No entanto, eles são "partes" no sentido OPC e há um relacionamento OPC explícito entre o manifesto do suplemento e cada um desses arquivos. Para obter um exemplo de um pacote de suplemento que inclui um recurso da web de host, consulte [Criar ações personalizadas para implantar o SharePoint Add-ins](create-custom-actions-to-deploy-with-sharepoint-add-ins.md).
    
    > **OBSERVAçãO**
      > Os administradores dos locatários tem a opção de lote-instalar um Suplemento do SharePoint para vários sites. Um suplemento que foi instalado dessa maneira é considerado como tendo **Tenant** escopo. Se o suplemento não tiver sido instalado no lote e, em vez disso instalados separadamente para cada site, ele tem escopo **Web**. Se web host recurso inclui as extensões de faixa de opções ou suplemento partes, não são implantadas às webs host se o add-in estiver instalado no lote, para que apenas itens de menu de atalho estão implantados com escopo de locatário escopo de suplemento do add-ins. não deve ser confundido com o escopo do recurso. Escopo do recurso determina onde os elementos em um recurso estão implantados. As possibilidades são **Farm**, **WebApplication**, **Web**e **Site** (ou seja, o conjunto de sites). Somente a opção **Web** é permitida para recursos no Suplementos do SharePoint (ambos hospedam os recursos da web e recursos dentro de um. wsp em um pacote de suplemento). Suplemento escopo refere-se ao escopo no qual um suplemento está instalado. As possibilidades são **Web**, nesse caso o suplemento foi instalado em um ou mais sites site por site e **Tenant**, nesse caso o suplemento ficou lote instalado subconjunto todos ou alguns dos sites da Web em locação do cliente. Para obter mais informações sobre o escopo de **Tenant** e **Web**, consulte [Aluguéis são e escopos de implantação para o SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md).
- **Arquivos de recurso de localização (. resx):** Essas são para a localização de aspectos de manifesto do suplemento que incluem o suplemento título e aspectos do host web recursos no pacote do suplemento. (Partes individuais do pacote do suplemento que estão dentro de seu próprio pacote, como arquivos. wsp, pacotes Azure Web Sites e manifestos de suplemento, cada uma tem seus próprios processos de localização que são aplicados exatamente como seriam se os itens em questão não eram parte de um Suplemento do SharePoint.) Para obter um exemplo de um pacote de suplemento que inclui os arquivos. resx para um recurso da web de host, consulte [Localizar os suplementos do SharePoint](localize-sharepoint-add-ins.md).
    
  
- **Suplementos do Office Manifestos:** opcionalmente, pode haver uma ou mais Suplementos do Office manifestos que cada empacotar um Suplemento do Office. Esta parte pode ser incluída no pacote de suplemento somente se o suplemento será carregado para um SharePoint 2013 suplemento catálogo corporativo, não no marketplace público. Consulte [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md) para obter mais informações.
    
  

## Suplemento para o arquivo de manifesto do SharePoint
<a name="AppManifest"> </a>

Cada Suplemento do SharePoint inclui um arquivo appmanifest.xml. O appmanifest.xml SharePoint, informa o que deve saber sobre o suplemento e define as propriedades da mais importantes do suplemento. Estes são alguns dos itens que são especificados no manifesto:
  
    
    

- O nome interno, ID de produto e versão do add-in.
    
  
- A URL da página inicial, que é a página aberta quando o suplemento for iniciado. Isso pode ser uma página da web add-in, uma página baseada em nuvem ou uma página em um servidor web do ISV.
    
    > **OBSERVAçãO**
      > Em certas circunstâncias, pode haver restrições sobre qual tipo de arquivo pode ser especificado no elemento **StartPage**. Para obter detalhes, consulte [Página inicial elemento (PropertiesDefinition complexType) (Add-in SharePoint manifesto)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx).> Quando você combina mais de um parâmetro de consulta no valor de **StartPage**, você deve usar o codificado e comercial " `&amp;amp;`" em vez de " `&amp;`" ou ponto e vírgula para acrescentá-los juntos.
- Outras propriedades do add-in. Eles incluem o título e as localidades compatíveis com o suplemento (ambos são obrigatórios), as URLs dos serviços que lidam com a pós-instalação pós-atualização e pré-desinstalar o modelo da web a ser usado ao suplemento web é criado e eventos.
    
  
- Solicitações de permissões o suplemento precisa recursos do SharePoint fora da web do suplemento.
    
  
- Uma identificação, para fins de autenticação e autorização, da entidade add-in. É essa entidade de segurança que tem as permissões. Isso não é necessário para um suplemento hospedado no SharePoint.
    
  
- Uma lista dos pré-requisitos, se houver, que deve estar disponível para o suplemento para que o suplemento seja instalado. Por exemplo, certos recursos podem precisar ser instalados e ativados e determinados serviços talvez precisem ser licenciado e instalado.
    
  

> **OBSERVAçãO**
> O arquivo de manifesto suplemento é o único item necessário no pacote de suplemento, mas nem todos os itens na lista anterior sejam necessários partes do arquivo.
  
    
    

Para obter informações detalhadas sobre o suplemento de manifesto de marcação, consulte o nó  [Referência de esquema de manifestos de aplicativos para o SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx). Este tópico não é um substituto para obter as informações no nó, incluindo informações sobre os elementos e atributos necessários. Além disso, observe que manifestos de suplemento do SharePoint tem um esquema de diferente de manifestos de Suplemento do Office. Você pode encontrar informações sobre o último em  [Schema reference for Office Add-ins manifests (v1.1)](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx).
  
    
    
O exemplo a seguir é um exemplo de um arquivo de appmanifest.xml. Observe que, neste exemplo, a página inicial para o suplemento é uma página ASP.NET que está em um servidor remoto, não uma página no site do SharePoint. A URL da página inclui uma cadeia de caracteres de consulta que passa para o aplicativo web remoto a URL da web host. A parte "{HostUrl}" da sequência de caracteres é um token que é resolvido quando o suplemento for iniciado. O suplemento está solicitando a permissão de gravação para todas as listas na web host. O suplemento principal que deve ser concedido a essa permissão é o aplicativo da web remoto.
  
    
    
Você deve usar o **SupportedLocales** ou o elemento **SupportedLanguages** em seu manifesto do suplemento. **SupportedLanguages** está sendo substituída pela **SupportedLocales**. O elemento **SupportedLanguages** continuará funcionando mesmo depois do lançamento, mas você deve Evite utilizá-lo. Para obter mais informações sobre esses elementos consulte [Elemento SupportedLocales (PropertiesDefinition complexType) (Add-in SharePoint manifesto)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx) e [Elemento SupportedLanguages (PropertiesDefinition complexType) (Add-in SharePoint manifesto)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx).
  
    
    

> **OBSERVAçãO**
> Os valores do atributo **Scope** do elemento **AppPermissionRequest** são estruturados como URIs, mas são cadeias de caracteres literais realmente. Nenhuma parte do valor **Scope** exemplo no exemplo a seguir é um espaço reservado. Para obter mais informações sobre permissões, consulte [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### Tokens de URL no manifesto do suplemento

SharePoint 2013 fornece várias tokens que podem ser usados no elemento **StartPage** e outros lugares em suplementos e componentes de suplementos para representam informações que não são conhecidas até que o suplemento seja executado. A infraestrutura de SharePoint 2013 resolve esses tokens. Alguns são usados no início da URL e outros podem ser usados dentro de uma URL como o valor de um parâmetro de consulta. Esses tokens e várias outras também podem ser usadas em uma variedade de contextos de desenvolvimento SharePoint 2013. Para obter informações detalhadas sobre todos os tokens e onde eles podem ser usados, consulte [As cadeias de caracteres da URL e tokens SharePoint Add-ins](url-strings-and-tokens-in-sharepoint-add-ins.md). Para obter informações gerais sobre outros tokens e URLs em SharePoint 2013, consulte  [URLs e tokens do SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx).
  
    
    

> **OBSERVAçãO**
> Esses tokens não são usados no atributo **Scope** de um elemento **AppPermissionRequest**.
  
    
    


## Recursos adicionais
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Referência de esquema de manifestos de aplicativos para o SharePoint](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [Convenções de empacotamento aberto (OPC)](http://msdn.microsoft.com/en-us/magazine/cc163372.aspx)
    
  
-  [Conexão do data-Tier Application (DAC)](http://msdn.microsoft.com/en-us/library/ee210546)
    
  
-  [Implantar o Web 2.0](http://www.iis.net/download/WebDeploy)
    
  

