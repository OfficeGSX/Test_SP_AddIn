---
title: Autorização e autenticação do SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# Autorização e autenticação do SharePoint Add-ins
Obtenha uma visão geral de autenticação e autorização no SharePoint, que é usado para autorizar solicitações por um Suplemento do SharePoint para acessar os recursos de SharePoint.
**Assista a um vídeo sobre a identidade do suplemento.**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## Autenticação de suplemento em SharePoint
<a name="AuthN"> </a>

Quando um  *usuário*  entra no SharePoint, o token de segurança do usuário é validado. O token emitido por um provedor de identidade. SharePoint oferece suporte a vários tipos de autenticação do usuário. Para obter mais informações, consulte [Autenticação, autorização e segurança no SharePoint 2013](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx).
  
    
    
Suplementos do SharePoint também são entidades de segurança que precisam ser autenticados e autorizados. Suplementos podem ser autenticados e autorizados de várias maneiras diferentes. Para obter mais informações, consulte  [Três sistemas de autorização para o SharePoint Add-ins](three-authorization-systems-for-sharepoint-add-ins.md).
  
    
    

## Políticas de autorização: política de usuário, usuário + suplemento política ou adicionar-somente na diretiva
<a name="AuthZ"> </a>

O processo de autorização certifica-se de que uma entidade autenticada (um suplemento ou um usuário ou ambos) tem permissão para realizar determinadas operações ou para acessar recursos específicos (por exemplo, uma lista ou uma pasta de documentos SharePoint ).
  
    
    
tipos de usuários três SharePoint de diretivas de autorização. O usuário somente apenas requer que a chamada para SharePoint incluir uma identidade de usuário autenticado. AAdicionar-somente na diretiva requer apenas que a chamada incluir apenas uma suplemento identidade autenticada. A política deusuário + suplemento exige que a chamada inclua os dois tipos de identidades autenticados. Quando um usuário acessa os recursos de SharePoint por meio de SharePoint da interface do usuário, em vez de por meio de um suplemento SharePoint usa a política de usuário somente. No entanto, para chamadas de um Suplemento do SharePoint, SharePoint sempre usa o o add-somente na ou o usuário + diretiva add-in. O Suplemento do SharePoint determina qual política é usada pelo tipo de token de acesso que ele inclua em sua solicitação para SharePoint. Se um usuário + suplemento solicitação for feita, SharePoint exigirá que tanto o add-in e o usuário tem permissão para o recurso que está acessando o add-in. No caso de uma solicitação add-somente na, SharePoint requer que o suplemento têm permissão para o recurso, mas não importa se o usuário não ou não. (um Suplemento do SharePoint pode criar solicitações de adicionar-somente na somente se ele tem permissão para fazê-lo com antecedência; normalmente, quando ele é instalado.)
  
    
    
Para obter mais informações sobre políticas de autorização e como eles funcionam, consulte  [Tipos de política de suplemento autorização no SharePoint 2013](add-in-authorization-policy-types-in-sharepoint-2013.md).
  
    
    

## Permissões de suplemento e escopos de solicitação de permissão de suplemento
<a name="Permissions"> </a>

O desenvolvedor de um Suplemento do SharePoint deve especificar, por meio do suplemento do arquivo de manifesto, as permissões um suplemento precisa SharePoint recursos fora da web do suplemento. (O suplemento automaticamente tem a permissão Controle total à Web inteira add-in.) Quando o suplemento foi projetado para ser iniciados em SharePoint, a infraestrutura de instalação do suplemento solicita que o usuário que instala o suplemento para conceder ou negar as permissões necessárias. Depois que tiverem sido concedidas as permissões, os usuários do site podem usar o suplemento sem precisar novamente conceda a ele permissões. No entanto, quando o suplemento foi projetado para ser iniciado fora SharePoint; ou seja, ele não está instalado no SharePoint e SharePoint solicitará ao usuário que está executando o suplemento para conceder as permissões necessárias em cada vez que o suplemento é executado. Suplementos em dispositivos móveis e Suplementos do Office são exemplos de suplementos que podem acessar SharePoint, mas não são instalados nele.
  
    
    
Somente um proprietário de site pode instalar um Suplemento do SharePoint em um site de SharePoint (a menos que foi criada uma função personalizada que possui direitos de instalação do suplemento). Um usuário pode conceder um suplemento somente as permissões que o usuário si ou por conta própria possui. Para que um usuário não pode instalar um suplemento que precisa ter permissões de usuário não tem. Da mesma forma, um usuário não pode ser executado e iniciados externamente suplemento que precisa que o usuário não tem permissões. No entanto, quando um Suplemento do SharePoint é instalado em SharePoint, ele pode pedir permissão para fazer chamadas de adicionar-somente na. Um suplemento com essa permissão pode acessar SharePoint maneiras em que o usuário que está executando o suplemento não tem permissão para fazer.
  
    
    
Suplementos também podem ter permissões revogado ou concedidas por administradores de inquilinos SharePoint Online ou SharePoint administradores de farm.
  
    
    
No suplemento do arquivo de manifesto, um Suplemento do SharePoint Especifica as permissões que ele precisa para funcionar corretamente. As solicitações de permissão especificam os direitos que um suplemento precisa e o escopo no qual eles precisam os direitos. Escopos indicam onde na hierarquia de SharePoint uma solicitação de permissão se aplica. SharePoint suporta quatro escopos diferentes de conteúdo: locação, conjunto de sites, site e lista. Também existem escopos especiais para a execução de consultas de pesquisa, como acessar dados de taxonomia, recursos sociais, recursos Microsoft Business Connectivity Services (BCS) e Project Server 2013 recursos. Para obter mais informações, consulte  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    

## Quando o OAuth é usado?
<a name="FileName_uniquekeyword4"> </a>

Você deve ter já ouvido que OAuth 2.0 desempenha um papel importante na autenticação e autorização de Suplementos do SharePoint. Ele faz, mas ele não é necessariamente uma parte da história autorização para cada Suplemento do SharePoint. Se você planeja criar Suplemento do SharePoint que é executado em um aplicativo web remoto e comunica novamente ao SharePoint usando código do lado do servidor, você precisará usar OAuth. Se o aplicativo web remoto estiver desativado local, você usaria  [o sistema de autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) em que Azure ACS é o emissor de token de acesso. Se estiver no local, em seguida, você normalmente usaria [o sistema de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) em que o próprio suplemento e um certificado digital são os emissores de token de acesso.
  
    
    
Não, você usaria OAuth para fazer uma chamada a partir de JavaScript em uma página da web suplemento em si ou uma página da Web remota usando  [a biblioteca de domínio cruzado](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md). Para obter mais informações sobre a biblioteca entre domínios, consulte  [Criando Add-ins do SharePoint que usam a biblioteca entre domínios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md).
  
    
    

## Recursos adicionais
<a name="Filename_AdditionalResources"> </a>


-  [Três sistemas de autorização para o SharePoint Add-ins](three-authorization-systems-for-sharepoint-add-ins.md)
    
  
-  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  

