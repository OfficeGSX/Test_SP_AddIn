---
title: Ferramentas e ambientes para o desenvolvimento de suplementos do SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# Ferramentas e ambientes para o desenvolvimento de suplementos do SharePoint
Saiba as opções para a criação de um ambiente de desenvolvimento para Suplementos do SharePoint.
Existem dois padrões básicos para ambientes de desenvolvimento para Suplementos do SharePoint:
  
    
    


- **O teste e depuração de site do SharePoint está em um site da Web em uma assinatura do Office 365SharePoint Online.** Normalmente, Visual Studio é instalado em um computador local, mas um baseado em nuvem Visual Studio também é uma opção.
    
  
- **o teste e a depuração de site do SharePoint está em um farm do SharePoint no local, um servidor.** Visual Studio está instalado no mesmo computador.
    
  

Considere o seguinte:
  
    
    


- Praticamente qualquer suplemento que criar pode ser implantado para qualquer um dos SharePoint Online ou para farms do SharePoint, independentemente de qual tipo de ambiente utilizado no local. Como regra geral, suplementos que não podem ser implantados para SharePoint Online também não podem ser desenvolvidos com ele. Exemplos: suplementos que requerem  [permissões de controle total](add-in-permissions-in-sharepoint-2013.md) e suplementos que usam o [sistema de autorização de alta confiança](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).
    
  
- Você pode desenvolver hospedado no SharePoint tanto hospedado em provedor Suplementos do SharePoint, independentemente de qual tipo de ambiente utilizado.
    
  
- Você pode ter SharePoint Online testar sites e no local.
    
  
- Tudo considerado, as duas opções são igualmente fácil de configurar.
    
  
 **Para criar o ambiente SharePoint Online** usando uma assinatura de SharePoint Online que você pode usar para o desenvolvimento, consulte [Criar um site do desenvolvedor em uma assinatura existente do Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md). **Para criar o ambiente no local**, consulte [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
> **OBSERVAçãO**
> Este tópico trata apenas ambientes para o desenvolvimento de Suplementos do SharePoint. Se você planeja desenvolver soluções de farm, consulte  [Configurar um ambiente de desenvolvimento geral para o SharePoint 2013](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx). Se você pretende fazer ambos os tipos de desenvolvimento, inicie com o último artigo e, em seguida, consulte  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md) para obter etapas adicionais que necessárias para desenvolver Suplementos do SharePoint.
  
    
    


## Outras informações de ferramentas


-  [Criar um básico hospedado no SharePoint suplemento usando ferramentas de desenvolvimento do Napa Office 365](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)
    
  
-  [Criar os suplementos do SharePoint no Visual Studio](create-sharepoint-add-ins-in-visual-studio.md)
    
  

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Suplementos do SharePoint](sharepoint-add-ins.md)
    
  

