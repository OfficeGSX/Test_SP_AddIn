---
title: Atualizar componentes remotos no SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# Atualizar componentes remotos no SharePoint Add-ins
Atualize os aplicativos web remoto e bancos de dados em um Suplemento do SharePoint.
## Pré-requisitos para atualizar os componentes remota de um SharePoint Add-in
<a name="Prerequistes"> </a>

Estar familiarizado com  [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md) e os pré-requisitos e os conceitos fundamentais listados nela.
  
    
    

## Atualizar componentes remotos
<a name="UpdateRemote"> </a>

Na maioria das vezes, apenas muito gerais conselhos podem ser fornecido para atualizar os componentes remota devido às diferenças ampla em plataformas e sistemas de aluguel. As duas seções a seguintes fornecem algumas diretrizes.
  
    
    

### Atualizar componentes remotos em um suplemento hospedado em provedor
<a name="UpdateProviderHosted"> </a>

Para um provedor hospedado Suplemento do SharePoint, você deve atualizar os componentes remota usando as práticas recomendadas de atualização da plataforma no qual os componentes estão hospedados. Assim como os componentes remotos de um suplemento hospedado em provedor são instalados separadamente da instalação do Suplemento do SharePoint em si, eles também são atualizados separadamente. Alguns pontos a considerar:
  
    
    

- Os componentes atualizados do remotos devem continuar a trabalhar com todas as versões anteriores do Suplemento do SharePoint.
    
  
- Se você implementou um sistema de isolamento de locação para os componentes remotos, tenha em mente que diferentes locatários estejam usando várias versões de seu suplemento e um locatário específico mesmo pode ter diferentes versões instaladas em vários sites do SharePoint.
    
  
Para um provedor hospedado Suplemento do SharePoint que usa Banco de Dados SQL do Microsoft Azure ou SQL Server, há vários métodos para atualizar o banco de dados. Para começar, consulte  [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx).
  
    
    

## Próximas etapas
<a name="Next"> </a>

Retornar às  [Principais etapas atualizando um suplemento](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)ou ir diretamente para um dos artigos a seguir para saber como atualizar o próximo componente principal da sua Suplemento do SharePoint.
  
    
    

-  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Atualizar os componentes da web de host no SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Criar um manipulador para o evento de atualização no SharePoint Add-ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md)
    
  

