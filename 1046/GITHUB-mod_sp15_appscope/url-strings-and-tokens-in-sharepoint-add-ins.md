---
title: As cadeias de caracteres da URL e tokens SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# As cadeias de caracteres da URL e tokens SharePoint Add-ins
Saiba quais símbolos de URL estão disponíveis para uso em Suplementos do SharePoint.
> [!IMPORTANTE]
> Para obter informações gerais sobre URLs construindo em SharePoint 2013 e o uso de tokens dessas URLs, consulte  [URLs e tokens do SharePoint 2013](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx). Este tópico descreve os tokens que estão disponíveis no Suplementos do SharePoint.
  
    
    


## 
<a name="URLtokens"> </a>

SharePoint 2013 suporta os tokens listados nas tabelas a seguir para uso em Suplementos do SharePoint.
  
    
    
Os tokens nas tabelas desta seção podem ser usados em URLs em uma ampla variedade de situações no desenvolvimento de Suplementos do SharePoint, como em ações personalizadas e em links em páginas personalizadas. Em alguns contextos, alguns desses tokens não podem ser usado. Três dos lugares onde apenas uma lista restrita de tokens pode ser usada mais importantes são a página inicial de um suplemento, uma ação personalizada na web host e a propriedade  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) de uma parte do suplemento. Eles são chamados de check-out em colunas separadas, * **, mas esses três não são uma lista exaustiva de casas onde os tokens podem ser usados.*** 
  
    
    
A coluna de **página inicial** Especifica se o token pode ser usado no elemento **StartPage** de manifesto de um suplemento. A coluna **Ação personalizada** Especifica se o token pode ser usado na URL de uma ação personalizada em um web host. A coluna de **suplemento parte** Especifica se o token pode ser usado na propriedade [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) da parte add-in.
  
    
    

**Tokens que podem ser usados no início de uma URL em um Suplemento do SharePoint**


|**Token**|**Resolver para**|**StartPage**|**ação personalizada**|**parte de suplemento**|**Comentários**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl <br/> |A URL da web add-in de um Suplemento do SharePoint. <br/> |Sim <br/> |Sim <br/> |Sim <br/> |Este token deve ser usado somente fora de um suplemento de web. Em suplemento web propriamente dito, use **~site** para a URL da web add-in. <br/> |
|~controlTemplates <br/> |A URL da pasta ControlTemplates virtual para o site atual. <br/> |Não <br/> |Não <br/> |Não <br/> ||
|~hostUrl <br/> |A URL da web host. <br/> |Não <br/> |Não <br/> |Sim <br/> ||
|~hostLogoUrl <br/> |A URL do logotipo da web host. <br/> |Não <br/> |Não <br/> |Não <br/> ||
|~ layouts <br/> |A URL da pasta Layouts virtual para o site atual. <br/> |Não <br/> |Não <br/> |Não <br/> ||
|~remoteAppUrl <br/> |A URL de um aplicativo web remoto em um Suplemento do SharePoint. <br/> |Sim <br/> |Sim, no web host, mas não no suplemento de web. <br/> |Sim <br/> |Se você não estiver usando Microsoft Office Developer Tools for Visual Studio para desenvolver o Add-in do SharePoint, é possível usar **~remoteAppUrl** na URL **StartPage**. No entanto, quando você estiver usando Visual Studio e as ferramentas, você pode usar este token para qualquer suplemento de hospedado em provedor e ele é resolvido quando o Visual Studio empacota o add-in. Nessa utilização, é realmente mais de um token de Visual Studio que um token do SharePoint. Ele pode ser usado fora o manifesto do suplemento, mesmo quando você não estiver usando Microsoft Office Developer Tools for Visual Studio. <br/> |
|~ site <br/> |A URL do site atual. <br/> |Não <br/> |Não <br/> |Sim <br/> ||
|~ sitecollection <br/> |A URL do conjunto de sites pai do site atual. <br/> |Não <br/> |Não <br/> |Sim <br/> ||
   
Exceto onde indicado caso contrário, nenhum dos tokens na próxima tabela pode ser usada na parte do  *caminho*  do valor da propriedade [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) da parte add-in. A coluna de **suplemento parte** refere-se a sua utilização na parte do valor de *cadeia de caracteres de consulta*  .
  
    
    

**Tokens que podem ser usados dentro de uma URL**


|**Token**|**Resolver para**|**StartPage**|**ação personalizada**|**parte de suplemento**|**Comentários**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken} <br/> |O token de contexto do OAuth para o suplemento. <br/> |Não <br/> |Não <br/> |Não <br/> ||
|{AppWebUrl} <br/> |A URL da web add-in em um Suplemento do SharePoint. <br/> |Sim <br/> |Sim <br/> |Sim <br/> |Este token deve ser usado somente fora de um suplemento de web. Em suplemento web propriamente dito, use **{Site}** para a URL da web add-in. <br/> |
|{ClientTag} <br/> |O cliente cache controle número (marca do cliente) do site atual. <br/> |Sim <br/> |Sim <br/> |Sim <br/> ||
|{HostLogoUrl} <br/> |O logotipo da Web do host de um Suplemento do SharePoint. <br/> |Sim <br/> |Sim <br/> |Sim <br/> ||
|{HostTitle} <br/> |O título da web de um Suplemento do SharePoint host. <br/> |Sim <br/> |Sim <br/> |Sim <br/> ||
|{HostUrl} <br/> |A URL da web de um Suplemento do SharePoint host. <br/> |Sim <br/> |Sim <br/> |Sim <br/> ||
|{ItemId} <br/> |A identificação de um item em uma lista ou biblioteca (um inteiro). <br/> |Não <br/> |Sim <br/> |Não <br/> ||
|{ItemUrl} <br/> |A URL do item que está sendo tratado. <br/> |Não <br/> |Sim <br/> |Não <br/> ||
|Idioma <br/> |Idioma/cultura atual da web de um Suplemento do SharePoint host. <br/> |Sim <br/> |Sim <br/> |Sim <br/> ||
|{ListId} <br/> |A identificação da lista atual (uma GUID). <br/> |Não <br/> |Sim <br/> |Não <br/> ||
|{ProductNumber} <br/> |Número de versão do farm do SharePoint da compilação completo. <br/> |Sim <br/> |Sim <br/> |Sim <br/> |Um valor de exemplo é "15.0.4433.1011". <br/> |
|{RecurrenceId} <br/> |O índice de recorrência de um evento recorrente. <br/> |Não <br/> |Sim <br/> |Não <br/> |Este token não é suportado para uso nos menus de contexto de itens de lista. <br/> |
|{RemoteAppUrl} <br/> |A URL de um aplicativo web remoto em um Suplemento do SharePoint. <br/> |Sim <br/> |Sim <br/> |Sim <br/> ||
|Site <br/> |A URL do site atual. <br/> |Não <br/> |Sim <br/> |Sim <br/> ||
|{SiteCollection} <br/> |A URL do site pai do site atual. <br/> |Não <br/> |Sim <br/> |Sim <br/> ||
|{SiteUrl} <br/> |A URL do site atual. <br/> |Não <br/> |Sim <br/> |Não <br/> ||
|Origem <br/> |A URL da solicitação HTTP. <br/> |Não <br/> |Sim <br/> |Não <br/> ||
|{StandardTokens} <br/> |Consulte comentários. <br/> |Sim <br/> |Sim <br/> |Sim <br/> |Ele combina cinco outros tokens. Inicialmente, ele é resolvido para  `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}`. Em seguida, cada uma dessas tokens resolve. Se não houver nenhuma web suplemento, a parte  `&amp;SPAppWebUrl={AppWebUrl}` não está presente. <br/> |
   

## Recursos adicionais
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [Advanced Extranet Support](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)
    
  
-  [Getting References to Sites, Web Applications, and other Key Objects](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)
    
  
-  [Working with List Objects and Collections](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)
    
  
-  [Sample Object Model Tasks](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)
    
  

