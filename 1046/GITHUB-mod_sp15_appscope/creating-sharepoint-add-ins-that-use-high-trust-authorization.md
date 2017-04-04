---
title: Criando Add-ins do SharePoint que usam a autorização de alta confiança
ms.prod: SHAREPOINT
ms.assetid: 135bf6be-b118-4d2c-8d40-27f5060b06f3
---


# Criando Add-ins do SharePoint que usam a autorização de alta confiança
Saiba mais sobre Suplementos do SharePoint de alta confiança, qual usar certificados digitais para estabelecer confiança entre o SharePoint e os componentes remotos que acessam o SharePoint.
 * **Aplica-se a:*** 
  
    
    

 **Fornecido por:**
  
    
    
Steve Peschka, Microsoft Corporation
  
    
    
 Siew Moi Khor, Microsoft Corporation
 **Neste artigo**
  
    
    
 [Visão geral de alta confiança Suplementos do SharePoint](#Overview)
  
    
    
 [Dois tipos de emissores de token](#TwoKindsOfIssuers)
  
    
    
 [Decidir entre usar um certificado ou muitos de alta confiança Suplementos do SharePoint](#Deciding)
  
    
    
 [Os certificados são autoridades raiz no SharePoint](#RootAuthorities)
  
    
    
 [Aplicativo Web precisa saber o que é um emissor de token](#AppIsTokenIssuer)
  
    
    
 [Responsabilidades da equipe IT no sistema altamente confiável](#ITPro)
  
    
    
 [Recursos adicionais](#AR)
  
    
    


**Assista a um vídeo sobre a criação de alta confiança SharePoint suplementos.**

  
    
    

  
    
    
![Videos](images/mod_icon_video.png)
  
    
    

  
    
    

  
    
    

## Visão geral de alta confiança Suplementos do SharePoint
<a name="Overview"> </a>

Um suplemento de alta confiança é um provedor hospedado Suplemento do SharePoint que é instalado em um farm do SharePoint local. Ele não pode ser instalado para Microsoft SharePoint Online ou comercializado por meio do Office Store. Um suplemento de alta confiança usa um certificado em vez de um token de contexto para estabelecer a confiança.
  
    
    

> **OBSERVAçãO**
> Este tópico ajuda você a entender o sistema de autorização de alta confiança para Suplementos do SharePoint. Para obter informações práticas sobre como criar e implantar os suplementos de alta confiança, consulte os tópicos a seguir:>  [Criar o SharePoint de alta confiança Add-ins](create-high-trust-sharepoint-add-ins.md)>  [Embalar e publicar Add-ins do SharePoint de alta confiança](package-and-publish-high-trust-sharepoint-add-ins.md)
  
    
    

Em SharePoint 2013, o serviço de token de segurança (STS) fornece tokens de acesso para autenticação de servidor-para-servidor. O STS permite que os tokens de acesso temporário acessar outros serviços de aplicativos como Exchange 2013, Lync 2013 e Suplementos do SharePoint. Um administrador de farm estabelece confiança entre o SharePoint e o outro aplicativo ou suplemento usando cmdlets Windows PowerShell e um certificado. Cada certificado usado deve ser confiáveis pelo SharePoint 2013 usando o cmdlet  `New-SPTrustedRootAuthority` . Também cada certificado deve ser registrado com o SharePoint como um emissor de token usando o cmdlet `New-SPTrustedSecurityTokenIssuer` .
  
    
    

> **OBSERVAçãO**
> O STS não é destinado a autenticação do usuário. Portanto, você não verá o STS listado na usuário entrar página, na seção de **Provedor de autenticação** na Administração Central, ou no seletor de pessoas no SharePoint 2013.
  
    
    


## Dois tipos de emissores de token
<a name="TwoKindsOfIssuers"> </a>

O aplicativo web remoto de uma alta confiança Suplemento do SharePoint é acoplado a um certificado digital. (Para obter informações sobre como fazer isso, consulte os dois tópicos vinculadas ao acima). O certificado é usado pelo aplicativo web remoto para assinar os tokens de acesso que ele inclua em todas as suas solicitações no SharePoint. O aplicativo web assina o token com a chave privada do certificado e o SharePoint valida com a chave pública do certificado. O certificado deve ser registrada como um emissor de token confiável antes de SharePoint confiará os tokens que emite. Existem dois tipos de tokens emissores: alguns somente podem emitir tokens para um determinado Suplemento do SharePoint; outras pessoas, chamados agentes de confiança, podem emitir tokens para vários Suplementos do SharePoint.
  
    
    
Como uma questão prática, os administradores de farm do SharePoint determinam qual tipo de token de emissor é criado pelas opções e valores de parâmetro usarem com o cmdlet  `New-SPTrustedSecurityTokenIssuer` . Para criar um emissor de token que é um corretor de confiança, adicione a opção de `-IsTrustBroker` à linha de comando e use um valor exclusivo, que não seja ID do cliente de um suplemento, para o parâmetro `-Name` . O exemplo a seguir é um exemplo de editado.
  
    
    



```

New-SPTrustedSecurityTokenIssuer -IsTrustBroker -RegisteredIssuerName "<full_token_issuer_name> " --other parameters omitted--
```

Para criar um emissor de token de não-broker, a opção  `-IsTrustBroker` não é usada. Não há uma diferença. O valor do parâmetro `-RegisteredIssuerName` é sempre na forma de dois GUIDs separados pelo "@" caractere; _GUID_@ _GUID_. O GUID, no lado direito sempre é a ID do realm de autenticação do farm do SharePoint (ou inscrição de site). O GUID no lado esquerdo é sempre uma ID específica para um emissor de token. É um GUID aleatório quando um emissor de token de  *agente de confiança*  que está sendo criado. Mas, quando um emissor de token de não-broker está sendo criado, o emissor específico GUID deve ser o mesmo GUID que é usado como a ID do cliente a Suplemento do SharePoint. Este parâmetro fornece um nome para o emissor. Ele também informa SharePoint quais Suplemento do SharePoint é a única pessoa para a qual o certificado pode emitir tokens. Este é um exemplo parcial:
  
    
    



```
$fullIssuerIdentifier = "<client_ID_of_SP_app> " + "@" + "<realm_GUID> "
New-SPTrustedSecurityTokenIssuer -RegisteredIssuerName $fullIssuerIdentifier --other parameters omitted--
```

Normalmente, o cmdlet  `New-SPTrustedSecurityTokenIssuer` é usado em um script que realiza outras tarefas para configurar o SharePoint para suplementos de alta confiança. Para obter mais informações sobre esses scripts e exemplos completos do cmdlet `New-SPTrustedSecurityTokenIssuer` , consulte [Scripts de configuração de alta confiança para SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).
  
    
    

## Decidir entre usar um certificado ou muitos de alta confiança Suplementos do SharePoint
<a name="Deciding"> </a>

Administrador do SharePoint e rede ter duas estratégias básicas para escolher quando Obtendo e gerenciando certificados para uso por alta confiança Suplementos do SharePoint:
  
    
    

- Use o mesmo certificado para vários Suplementos do SharePoint.
    
  
- Use um certificado separado para cada Suplemento do SharePoint.
    
  
Esta seção discute as vantagens e desvantagens de cada estratégia.
  
    
    
A vantagem de usar o mesmo certificado para vários Suplementos do SharePoint é que um administrador tem menos de certificados de confiança e gerenciar. A desvantagem nesta abordagem é que, quando você encontrar uma situação em que você deseja interromper a relação de confiança com um determinado Suplemento do SharePoint, a única maneira que o administrador pode quebrar a relação de confiança, removendo o certificado como um emissor de token ou como uma autoridade raiz. Mas também remover o certificado quebras de confiança com todos os outros Suplementos do SharePoint que usam o mesmo certificado, o que faz com que todos eles parem de funcionar.
  
    
    
Para obter o trabalho ainda é confiável Suplementos do SharePoint novamente, o administrador seria necessário criar um objeto  `New-SPTrustedSecurityTokenIssuer` *usando um novo certificado*  e incluir o sinalizador `-IsTrustBroker` . Em seguida, o novo certificado precisa ser registrado com cada servidor que hospeda a qualquer um dos suplementos confiáveis e cada um desses suplementos teria deve ser vinculado o novo certificado.
  
    
    
Os benefícios do uso de um certificado por suplemento é que facilita quebrar a relação de confiança com um determinado suplemento, porque os certificados que são usados pelos suplementos trustworthy ainda não são afetados quando o administrador quebras de confiança com o certificado do um suplemento. A desvantagem dessa estratégia é que um administrador tem mais certificados para adquirir e o SharePoint deve ser configurado para usar a cada um deles, que pode ser feito com um script reutilizável, conforme mostrado na  [Scripts de configuração de alta confiança para SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).
  
    
    

## Os certificados são autoridades raiz no SharePoint
<a name="RootAuthorities"> </a>

Conforme mencionado brevemente no início deste artigo, os administradores de farm do SharePoint tenham de ser feitas do certificado, do aplicativo web remoto no suplemento de alta confiança, uma autoridade raiz confiável no SharePoint, bem como um emissor de token confiável. Na verdade, quando há uma hierarquia de autoridades por trás de certificado do aplicativo da web de emissão de certificado, todos os certificados na cadeia devem ser adicionados à lista do SharePoint de autoridades raiz confiáveis.
  
    
    
Cada certificado na cadeia é adicionado à lista do SharePoint de autoridades raiz confiáveis com uma chamada do cmdlet  `New-SPTrustedRootAuthority` . Por exemplo, suponha que o certificado do aplicativo da web é um certificado de domínio que é emitido por uma autoridade de certificação do domínio corporativo na LAN e suponhamos que se seu certificado, por sua vez, foi emitido por um autônomo, a autoridade de certificação de nível superior na LAN. Neste cenário, os certificados de autoridade de certificação de nível superior, a autoridade de certificação intermediária e o aplicativo web precisam ser adicionado à lista do SharePoint de autoridades raiz confiáveis. O código a seguir Windows PowerShell faria isso.
  
    
    



```

$rootCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<path_to_top-level_CA's_cer_file>")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $rootCA

$intermediateCA = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_intermediate_CA's_cer_file")
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $intermediateCA

$certificate = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("path_to_web_application's_cer_file") 
New-SPTrustedRootAuthority -Name "<name_of_certificate>" -Certificate $certificate 

```

Os certificados raiz e intermediários devem ser adicionados apenas uma vez em um farm do SharePoint. Normalmente, o certificado do aplicativo da web é adicionado em um script separado que faz outra configuração muito, como a chamada para  `New-SPTrustedSecurityTokenIssuer`. Para obter exemplos, consulte  [Scripts de configuração de alta confiança para SharePoint 2013](high-trust-configuration-scripts-for-sharepoint-2013.md).
  
    
    
Se o certificado do aplicativo da web é auto-assinado, conforme pode ser o caso quando o suplemento for sendo desenvolvidos e depurados, há depois, são a cadeia de certificados nenhuma e somente o certificado do aplicativo da web precisa ser adicionado à lista de autoridades raiz.
  
    
    
Para obter mais informações, consulte o blog postar a  [raiz da cadeia de certificados não confiável erro com autenticação de declarações](http://blogs.technet.com/b/speschka/archive/2010/02/13/root-of-certificate-chain-not-trusted-error-with-claims-authentication.aspx). (Rolar passado a seção sobre como exportar o certificado dos serviços de Federação do Active Directory (AD FS), supondo que você criou o seu certificado para suas alta confiança suplementos por outros meios; por exemplo, usando um certificado comercial emitido por uma autoridade de certificação, um certificado autoassinado ou um certificado emitido por domínio).
  
    
    

## Aplicativo Web precisa saber o que é um emissor de token
<a name="AppIsTokenIssuer"> </a>

O componente do aplicativo web remoto do Suplemento do SharePoint está vinculado ao seu certificado no IIS. Isso é suficiente para os fins tradicionais de certificados: que identifica o aplicativo web de forma segura e codificação de solicitações HTTP e respostas. No entanto, em uma alta confiança Suplemento do SharePoint, o certificado tem a tarefa adicional de como sendo o "emissor" oficial dos tokens de acesso que o aplicativo web envia para o SharePoint. Para essa finalidade, o aplicativo web deve saber a identificação do emissor do certificado que é usado ao registrar o certificado como um emissor de token com o SharePoint. O aplicativo web obtém essa ID da seção **appSettings** do arquivo Web. config, onde há uma chave chamada **IssuerId**. As instruções de como o desenvolvedor do suplemento define esse valor e como o certificado está vinculado ao aplicativo web no IIS, estão no  [Embalar e publicar Add-ins do SharePoint de alta confiança](package-and-publish-high-trust-sharepoint-add-ins.md). Observe que se o cliente está usando a estratégia de ter um certificado separado para cada de alta confiança Suplemento do SharePoint, em seguida, o valor **ClientId** também é usado como o valor de **IssuerId**. Isso não é o caso quando vários suplementos compartilham o mesmo certificado, porque cada Suplemento do SharePoint deve ter sua própria identificação exclusiva do cliente, mas a ID de emissor é o identificador de um objeto **SPTrustedSecurityTokenIssuer**.
  
    
    
A seguir está um exemplo de uma seção de **appSettings** para uma alta confiança Suplemento do SharePoint. Neste exemplo, um certificado está sendo compartilhado por vários suplementos, portanto, o **IssuerId** não é igual a **ClientId**. Observe que não há nenhuma chave **ClientSecret** em uma alta confiança Suplemento do SharePoint.
  
    
    



```XML

<appSettings>
  <add key="ClientId" value="6569a7e8-3670-4669-91ae-ec6819ab461" />
  <add key="ClientSigningCertificatePath" value="C:\\MyCerts\\HighTrustCert.pfx" />
  <add key="ClientSigningCertificatePassword" value="3VeryComplexPa$$word82" />
  <add key="IssuerId" value="e9134021-0180-4b05-9e7e-0a9e5a524965" />
</appSettings>

```


> **OBSERVAçãO SOBRE SEGURANçA**
> O exemplo anterior pressupõe que o certificado está armazenado no sistema de arquivos. Isso é aceitável para o desenvolvimento e a depuração. Em um de alta confiança de produção Suplemento do SharePoint, o certificado é geralmente armazenado no repositório de certificados do Windows e as chaves **ClientSigningCertificatePath** e **ClientSigningCertificatePassword** normalmente são substituídas por uma chave de **ClientSigningCertificateSerialNumber**.
  
    
    


## Responsabilidades da equipe IT no sistema altamente confiável
<a name="ITPro"> </a>

Desenvolvedores terão que entender os requisitos de segurança de aplicativo, conforme descrito acima, mas os profissionais de TI implementará a infraestrutura necessária para dar suporte a ele. Profissionais de TI devem planejar os requisitos operacionais a seguintes:
  
    
    

- Criar ou adquirir um ou mais certificados que serão usados para confiável Suplementos do SharePoint.
    
  
- Certifique-se de que os certificados são armazenados com segurança nos servidores de aplicativos web. Quando estiver sendo usado o sistema operacional Windows, isso significa armazenar os certificados no repositório de certificados do Windows.
    
  
- Gerencie a distribuição dos certificados para os desenvolvedores que estão criando Suplementos do SharePoint.
    
  
- Manter o controle onde cada certificado é distribuído para ambos os suplementos usando a ele e os desenvolvedores que receberam uma cópia. Se um certificado tiver a ser revogado, a equipe de TI deve trabalhar com cada desenvolvedor para organizar uma transição gerenciada para um novo certificado.
    
  

## Recursos adicionais
<a name="AR"> </a>


-  [Criar o SharePoint de alta confiança Add-ins](create-high-trust-sharepoint-add-ins.md)
    
  
-  [Embalar e publicar Add-ins do SharePoint de alta confiança](package-and-publish-high-trust-sharepoint-add-ins.md)
    
  
-  [Dicas de solução de alta confiança complementos no SharePoint 2013](http://blogs.technet.com/b/speschka/archive/2012/11/01/more-troubleshooting-tips-for-high-trust-apps-on-sharepoint-2013.aspx)
    
  
-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  

