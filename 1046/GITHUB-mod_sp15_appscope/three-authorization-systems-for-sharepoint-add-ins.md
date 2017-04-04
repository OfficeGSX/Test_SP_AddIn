---
title: Três sistemas de autorização para o SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 623fdab7-856e-4a89-9f5d-748a2ba1ef2e
---


# Três sistemas de autorização para o SharePoint Add-ins
Saiba os três sistemas que Suplementos do SharePoint pode usar para obter autorização para os recursos de SharePoint.
SharePoint, um Suplemento do SharePoint é uma identidade principal, assim como um usuário e devem ser autenticado e autorizado a usar os recursos de SharePoint. Existem três sistemas de autorização que pode usar um suplemento. Eles não são mutuamente exclusivos.
  
    
    


## Entender os sistemas de três autorização e quando usá-los
<a name="UnderstandThreeSystems"> </a>


  
    
    

- **Baixa confiança**: um hospedado em provedor Suplemento do SharePoint pode registrar com Microsoft Azure Access Control Service (ACS) que emite um token de acesso para o add-in que permite que o suplemento acessem os recursos no aluguel SharePoint ou de farm no qual o suplemento está instalado. Azure ACS é o emissor de token confiável de uma estrutura de OAuth 2.0 "fluxo" que inclui o SharePoint e os componentes de remota dos suplemento suplementos que usam esse sistema podem ser vendidos no Office Store. O sistema de baixa confiança destina-se principalmente a suplementos cujos componentes remotos são hospedados na nuvem.
    
    Para obter mais informações sobre como criar um Suplemento do SharePoint que usa o sistema de baixa confiança, consulte o nó do SDK  [Criando Add-ins do SharePoint que usam a autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md).
    
    > **OBSERVAçãO**
      > O cliente que instala o suplemento deve ter uma conta de Office 365. Isso é necessário fornecer o acesso suplemento para o Azure ACS. No entanto, o cliente não precisa usar a conta para qualquer outro fim e o suplemento pode ser instalado em um farm no local SharePoint depois de algumas tarefas de configuração simples no farm.
- **Alta confiança**: um suplemento hospedado em provedor pode estabelecer relação de confiança com SharePoint usando certificados digitais. O sistema de alta confiança é principalmente pretendida para suplementos cujos componentes remotos são hospedados no local. O suplemento pode ser instalado em um farm de SharePoint que não está conectado à Internet. O suplemento o não pode ser instalado em SharePoint Online ou vendido na Office Store.
    
    Para obter mais informações sobre como criar um Suplemento do SharePoint que usa o sistema de alta confiança, consulte o nó do SDK  [Criando Add-ins do SharePoint que usam a autorização de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).
    
  
- **Biblioteca de domínio cruzado**: quando lógica de negócios do suplemento estiver em JavaScript, você tem a opção de uso da biblioteca de domínio cruzado SharePoint no lugar de, ou como um suplemento para, os sistemas de baixa confiança e de alta confiança. A biblioteca também foi projetada para cenários em que o suplemento tem componentes de hospedado em nuvem, mas no firewall corporativo do cliente dificulta utilizar o sistema de baixa confiança. O navegador do usuário bloqueia scripts de outros domínios, mas a biblioteca encapsula um sistema seguro para evitar essa restrição. Suplementos que usam a biblioteca podem ser vendidos na Office Store e podem ser instalados em SharePoint Online ou no local SharePoint.
    
    Para obter mais informações sobre como criar um Suplemento do SharePoint que usa a biblioteca entre domínios, consulte o SDK nó  [Criando Add-ins do SharePoint que usam a biblioteca entre domínios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md), a postagem no blog  [Solucionar problemas de domínio cruzado SharePoint Add-ins](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx).
    
  

## Informações básicas sobre o OAuth 2.0 Framework e a implementação de SharePoint dele
<a name="UnderstandThreeSystems"> </a>

Dois dos sistemas três autorização, use o OAuth 2.0 Framework. OAuth 2.0 é uma **estrutura para autorização de abrir**. OAuth permite a autorização de segurança de área de trabalho, dispositivos e aplicativos da web de um modo padrão. OAuth permite que um usuário aprovar um aplicativo para agir em seu nome sem compartilhar seu nome de usuário e senha. Por exemplo, ele **permite que um usuário compartilhar** suas recursos particulares ou dados (contato lista, documentos, fotos, vídeos e assim por diante) em um site com outro site **without requiring the user to provide his or her credentials** (normalmente o nome de usuário e senha) para outro site.
  
    
    
OAuth permite que os usuários forneçam os tokens de acesso aos dados que são hospedados por um provedor de serviço fornecido (por exemplo, SharePoint ). Cada token concede acesso a um provedor de recurso específico (por exemplo, um site SharePoint ), para recursos específicos (por exemplo, os documentos em uma biblioteca de documentos SharePoint ) e por um período definido (por exemplo, 12 horas). Isso permite que um usuário para conceder a um aplicativo da área de trabalho acesso às informações armazenadas com outro provedor de recurso ou serviço (por exemplo, SharePoint ) ou da web de terceiros e de fazê-lo sem compartilhar seu nome de usuário e senha e sem  *todos*  os dados que ele ou ela possui com o provedor de compartilhamento.
  
    
    
SharePoint usa o framework do **OAuth 2.0** **"flui" de passagem de token de usos** para:
  
    
    

- Autorize solicitações por um Suplemento do SharePoint para acessar os recursos de SharePoint.
    
  
- Autentica suplementos no Office Store, um catálogo de suplemento ou um locatário de desenvolvedor.
    
  
Para obter mais informações e plano de fundo sobre OAuth e a terminologia OAuth, consulte  [OAuth.net](http://oauth.net/) e [Protocolo de autorização de Web (oauth)](http://datatracker.ietf.org/doc/active/). Em suma, existe um servidor de recurso que hospeda um recurso protegido, um proprietário de um servidor de autorização que emite o acesso, um aplicativo cliente que procura acesso ao recurso e o recurso de tokens para o recurso quando instruído pelo proprietário do recurso. A documentação oficial do OAuth costuma pressupõem um cenário no qual há um proprietário de recurso único que concede acesso ao recurso do aplicativo cliente sempre que o aplicativo cliente é executado. Ele também pressupõe que a pessoa usando o aplicativo cliente é o proprietário do recurso. A implementação de SharePoint leve em conta o fato de que um recurso de SharePoint, como uma lista, é compartilhado entre vários usuários. Além disso, na implementação SharePoint o Suplemento do SharePoint recebe acesso quando ele é instalado, não a cada vez que ele for executado; e ele pode ser executado por usuários que não seja a pessoa que ele instalado e concedidos a ele acessar. (No caso de suplementos que não são instalados no SharePoint, mas os recursos do access SharePoint (de modo que eles são "Suplementos do SharePoint " somente em um sentido estendido), o usuário que está executando o suplemento precisa conceder permissões a cada vez que o suplemento é executado.)
  
    
    
Então, na implementação SharePoint, as funções de OAuth são executadas pelos seguintes componentes:
  
    
    

- O componente remoto de um Suplemento do SharePoint desempenha a função do aplicativo cliente.
    
  
- usuários de SharePoint reproduzir a função de proprietário do recurso.
    
  
- SharePoint desempenha a função de servidor de recurso.
    
  
- Azure ACS desempenha a função de servidor de autorização, quando  [o sistema de autorização de baixa confiança](creating-sharepoint-add-ins-that-use-low-trust-authorization.md) é usado. Quando o [sistema de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md) é usado, o suplemento propriamente dito (juntamente com um certificado Digitial relatado) torna-se o servidor de autorização.
    
  

### Tokens de acesso não são tokens de entrar
<a name="FileName_uniquekeyword3"> </a>

Conforme descrito acima, para acessar recursos, um suplemento tem que solicitar um token de acesso de um servidor de autorização do OAuth anteriormente foi designado como um emissor de token de segurança confiável (STS) pelo proprietário do recurso. Por outro lado, o STS do WS-Federation e o Security Assertion Markup Language (SAML) entrar STS passivo destinam principalmente para emitir tokens de entrar. Em SharePoint, um STS OAuth não é usado para emitindo tokens de entrar, ou seja, eles não são usados como provedores de identidade. Portanto, você não verá que um STS OAuth listados na página de entrada do usuário, a seção de **Provedor de autenticação** na Administração Central ou o seletor de pessoas no SharePoint.
  
    
    
administradores de SharePoint podem usar os comandos Windows PowerShell para habilitar ou desabilitar um STS OAuth, semelhante a como eles podem habilitar ou desabilitar os provedores de logon confiável no SharePoint 2010.
  
    
    

