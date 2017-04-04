---
title: Criando Add-ins do SharePoint que usam a biblioteca entre domínios
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# Criando Add-ins do SharePoint que usam a biblioteca entre domínios
Saiba mais sobre a biblioteca de domínio cruzado JavaScriptSharePoint.
Existem alguns cenários nos quais não baixa confiança nem os sistemas de autorização de alta confiança podem ser usados por um Suplemento do SharePoint ou não são uma boa opção como o único meio para o suplemento obter autorização para os recursos de SharePoint. Exemplos:
  
    
    


- Os componentes de remota a Suplemento do SharePoint não estão no local, mas um firewall corporativo bloqueia a comunicação de servidor-para-servidor entre SharePoint e ACS, evitando assim o uso do sistema baixa confiança.
    
  
- O Suplemento do SharePoint destina-se como um aplicativo web de página única que depende do cliente JavaScript para operações de dados com o SharePoint.
    
  
- O Suplemento do SharePoint baseia-se principalmente em chamadas de servidor-para-servidor para acessar dados SharePoint (e é autorizado pelos sistemas de confiança baixa ou alta confiança), mas ele precisa ser complementados com algumas chamadas JavaScript. Por exemplo, uma página de elementos gráficos intensa pode usar o JavaScript para fazer pequenas atualizações nos dados exibidos sem precisar recarregar a página inteira.
    
  

No entanto,  [para segurança](http://msdn.microsoft.com/en-us/library%28d=robot%29/cc709423(d=robot,l=en-us,v=vs.85).aspx), navegadores não permita JavaScript que está hospedado em um domínio para acessar recursos em outro domínio, portanto, uma técnica especial é necessária para permitir que o JavaScript remotos acessem recursos SharePoint. Biblioteca do domínio cruzado JavaScriptSharePoint facilita para seu aplicativo web remoto usar a técnica.
  
    
    


> **OBSERVAçãO**
> Biblioteca do domínio cruzado também é usada para permitir o acesso aos dados na direção contrária; ou seja, para permitir JavaScript em uma página de SharePoint para acessar dados em um domínio remoto. Consulte  [Dados de acesso remotos de uma página do SharePoint](#ReverseDirection) para obter mais informações.
  
    
    


## Entender a arquitetura da biblioteca entre domínios

Biblioteca do domínio cruzado SharePoint está contida no arquivo SP. RequestExecutor.js que está localizada layouts/15/virtual pasta de cada site SharePoint. Os scripts neste arquivo encapsulam uma técnica de conhecido segura para superar restrição do navegador no script entre domínios: iFrame pode se comunicar com sua página pai por meio da função  `window.postMessage()` , mesmo se a página no iFrame está em um domínio diferente. Portanto dados solicitações e respostas são passadas sobre o limite de domínio usando chamadas para `postMessage()`.
  
    
    

> **CUIDADO**
> A função de  `postMessage()` funciona somente em navegadores que oferecem suporte a HTML 5, portanto Suplementos do SharePoint que usam a biblioteca entre domínios não funcionará nos navegadores mais antigos.
  
    
    

Para SharePoint, a biblioteca de domínio cruzado é carregada em uma página do aplicativo web remoto onde ele cria um iFrame oculto que hospeda uma página de proxy especiais do domínio SharePoint. A página de proxy já existe em cada site SharePoint. A biblioteca é usada para criar um objeto JavaScript Object Notation (JSON) que contém todas as informações necessárias para fazer uma chamada CRUD as APIs do restante do SharePoint. O objeto JSON é passado para a página de proxy usando  `postMessage()`. Na página de proxy, onde a biblioteca também é carregada, o objeto JSON é analisado e reconstruído como uma chamada REST para SharePoint. Como a página de proxy está no domínio SharePoint, o navegador permite que a chamada.
  
    
    
Obviamente, os componentes de remota a Suplemento do SharePoint ainda terá que têm acesso aos recursos SharePoint autorizado. Há duas maneiras de fazer isso:
  
    
    

- Defina o tipo de entidade suplemento **RemoteWebApplication** (o padrão para aplicativos hospedados pelo provedor) no manifesto do suplemento. Quando o suplemento é registrado com o ACS, o registro inclui o domínio do aplicativo web remoto. SharePoint confia domínios que estão registrados com o ACS, mesmo que ele não está, neste cenário, usando qualquer um do token passando fluxos que são parte do sistema baixa confiança no servidor. Para obter informações detalhadas sobre como registrar suplementos, consulte [Registrar o SharePoint 2013 de suplementos](register-sharepoint-add-ins-2013.md).
    
  
- Em um SharePoint hospedado add-in, você pode deixar o suplemento de tipo de entidade definido como seu padrão, que é **Internal**. Defina o atributo **AllowedRemoteHostUrl** do elemento **Internal** para a URL do aplicativo web remoto, como no exemplo a seguir.
    
```
<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>
```


> **OBSERVAçãO**
> Se você usar a segunda opção (um **Internal** suplemento principal), em seguida, você pode usar apenas JavaScript e a biblioteca entre domínios para acessar SharePoint. O modelo de objeto do cliente SharePoint seja bloqueado em **Internal**Suplementos do SharePoint, portanto você não pode ter um sistema de autorização dual que usa a biblioteca de entre domínios e os sistemas de confiança baixa ou alta confiança.
  
    
    

Para obter detalhes sobre como usar a biblioteca, consulte  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).
  
    
    

## Dados de acesso remotos de uma página do SharePoint
<a name="ReverseDirection"> </a>

Biblioteca do domínio cruzado SharePoint também pode ser usada na direção contrária; ou seja, JavaScript em uma página de SharePoint pode usar a biblioteca para obter dados a componentes remotos do add-in. Para fazer isso, você reverte a arquitetura de domínio cruzado: criar uma página de proxy do aplicativo web remoto. A biblioteca é chamada de uma página de SharePoint onde ele cria um iFrame para hospedar a página de proxy. Para obter detalhes sobre como usar a biblioteca dessa maneira, consulte  [Criar uma página personalizada de proxy para a biblioteca entre domínios no SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

## Nesta seção
<a name="ReverseDirection"> </a>


-  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [Trabalhar com a biblioteca entre domínios entre diferentes zonas de segurança do Internet Explorer no SharePoint Add-ins](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  

## Recursos adicionais
<a name="ReverseDirection"> </a>


-  [Solucionando problemas de domínio cruzado SharePoint Add-ins](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)
    
  

