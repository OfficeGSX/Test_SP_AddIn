---
title: Trabalhar com dados externos do SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# Trabalhar com dados externos do SharePoint 2013
Encontre recursos e orientações para acessar e manipular dados externos com JavaScript em páginas de Suplementos do SharePoint.
## Use JavaScriptSuplementos do SharePoint
<a name="SP15Workdata_Working"> </a>

No seu Suplementos do SharePoint, freqüentemente será necessário recuperar e manipular dados que são expostos por um aplicativo da web remoto ou o serviço de dentro de uma página de SharePoint ou componente. Como código personalizado não é permitido nos servidores SharePoint, seu suplemento precisa usar JavaScript para essa finalidade. O modelo de Suplementos do SharePoint fornece várias opções para acessar os dados remotos e serviços.
  
    
    

### Use a biblioteca de domínio cruzado JavaScriptSharePoint para acessar dados externos

Você pode usar a biblioteca entre domínios para acessar dados em seu aplicativo web remoto se você fornecer uma página de proxy personalizado que é hospedada na infraestrutura de remoto. Como o desenvolvedor, você é responsável por Implementando a página proxy personalizado e você tem de lidar com lógica personalizada, como o mecanismo de autenticação, se houver algum, para o aplicativo remoto. Use a biblioteca de entre domínios se quiser que a comunicação entre a fonte de dados remotos e a página SharePoint ocorrer no nível do cliente.
  
    
    
Para obter detalhes sobre como usar a biblioteca dessa maneira, consulte  [Criar uma página personalizada de proxy para a biblioteca entre domínios no SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md).
  
    
    

> **OBSERVAçãO**
> Biblioteca do domínio cruzado SharePoint também pode ser usada na outra direção; ou seja, JavaScript em páginas da web remoto pode usá-lo para acessar dados de SharePoint. Para obter mais informações sobre esse uso da biblioteca, consulte  [Criando Add-ins do SharePoint que usam a biblioteca entre domínios](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md).
  
    
    


### Use o proxy da web SharePoint para acessar dados externos

Você pode usar o proxy da web que é exposto no modelo de objeto de cliente JavaScript para acessar dados remotos. (O proxy também está disponível no modelo de objeto do cliente (CSOM) do .NET, mas é possível usar esse modelo de objeto no código que é executado nos servidores SharePoint.) Quando você usa o proxy da web, você emitir a solicitação inicial para SharePoint. Por sua vez, SharePoint solicita os dados para o ponto de extremidade especificado e encaminha a resposta de volta para sua página. Use o proxy da web quando você deseja que a comunicação entre a fonte de dados remotos e a página SharePoint ocorrer no nível do servidor.
  
    
    
Para obter detalhes sobre como usar o proxy, consulte  [Consulta um serviço remoto usando o proxy da web no SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).
  
    
    

## Recursos adicionais
<a name="SP15Workdata_AddRes"> </a>


-  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Desenvolver suplementos do SharePoint](develop-sharepoint-add-ins.md)
    
  

