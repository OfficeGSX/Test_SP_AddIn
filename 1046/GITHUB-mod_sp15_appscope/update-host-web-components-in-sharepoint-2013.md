---
title: Atualizar os componentes da web de host no SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# Atualizar os componentes da web de host no SharePoint 2013
Atualize o suplemento partes e ações personalizadas na web host de um Suplemento do SharePoint.
## Pré-requisitos para atualizar os componentes da web de host
<a name="Prerequisites"> </a>

Estar familiarizado com  [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md) e os pré-requisitos e os conceitos fundamentais listados nela.
  
    
    

## Atualizar os componentes da web de host
<a name="UpdateHostWeb"> </a>

Seu suplemento pode instalar dois tipos de componentes em uma web host com marcação descritiva no Suplemento do SharePoint: ações personalizadas epartes do suplemento. Atualizar esses componentes é mais fácil na web que host no suplemento da web. Você não precisa qualquer semântica de atualização. Basta adicionar/alterar as ações personalizadas e partes do suplemento. Quando o Suplemento do SharePoint é atualizado, o SharePoint sempre aplica quaisquer novos arquivos de manifesto do elemento e reaplica quaisquer arquivos de manifesto do elemento alteradas com a versão mais recente. Não há danos é feito de reaplicação; Por exemplo, um botão da faixa de opções para uma ação personalizada será não adicionado várias vezes à faixa de opções.
  
    
    
Quando você atualiza uma parte do suplemento, SharePoint substitui a versão antiga com a nova versão na Galeria de Web Parts. Certifique-se de alterar a propriedade **Name** do objeto **ClientWebPart** quando você atualiza uma parte do suplemento. Isso garante que, quando o suplemento for atualizado, SharePoint removerá a versão antiga da parte suplemento (que não mais é parte do aplicativo) de todas as páginas à qual ele foi adicionado. Os usuários precisarão adicione novamente a nova versão às páginas.
  
    
    
Se você deixar a propriedade **Name** inalterada, a versão antiga permanece em páginas em que ele foi adicionado, que torna improvável que os usuários serão cientes de que uma nova versão da parte suplemento está disponível. Além disso, quando a parte do suplemento é adicionada a outras páginas, é a nova versão é adicionada, portanto a mesma versão de um Suplemento do SharePoint teria partes de suplemento diferentes em páginas diferentes.
  
    
    
Você pode implantar outros tipos de componentes da web de host programaticamente usando um receptor de evento remoto que você registre no manifesto suplemento com um elemento  [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) . Você deve usar um receptor de [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) para atualizar os componentes que foram implantados com um receptor de **InstalledEventEndpoint**. Receptores de [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) são descritos em [Criar um manipulador para o evento de atualização no SharePoint Add-ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md).
  
    
    

## Próximas etapas
<a name="Next"> </a>

Vá para  [Principais etapas atualizando um suplemento](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)ou ir diretamente para um dos artigos a seguir para saber como atualizar o próximo componente principal da sua Suplemento do SharePoint.
  
    
    

-  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Criar um manipulador para o evento de atualização no SharePoint Add-ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [Atualizar componentes remotos no SharePoint Add-ins](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

