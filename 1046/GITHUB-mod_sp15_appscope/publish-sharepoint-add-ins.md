---
title: Publicar os suplementos do SharePoint
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# Publicar os suplementos do SharePoint
Encontre artigos e recursos para ajudá-lo a publicar seu Suplementos do SharePoint.
## Introdução ao seus suplementos de publicação
<a name="bk_gettingstarted"> </a>

Concluir a desenvolver seu Suplemento do SharePoint  a etapa final é tornar esse suplemento disponível aos seus usuários. Você pode fazer isso, publicando o add-in em um destes dois locais:
  
    
    

- **O público Office Store.** Publique o suplemento para o Office Store para disponibilizar o suplemento publicamente, para que ele pode ser adquirido por usuários de qualquer implantação do SharePoint.
    
  
- **Um catálogo de suplemento de organização interna.** Publica seus suplementos para um organização interna suplemento catálogo, hospedado em sua implantação do SharePoint, para torná-los disponíveis aos usuários com acesso a essa implantação do SharePoint.
    
  
Para obter informações sobre como compactar seu suplemento para publicação usando Visual Studio 2012, consulte  [Publicar suplementos do SharePoint usando o Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md).
  
    
    

### Publicação para o Office Store

Para publicar um suplemento para o Office Store, você deve primeiro  [registrar como um desenvolvedor do Microsoft](https://sellerdashboard.microsoft.com/Registration).
  
    
    
Quando você carrega um suplemento para o Office Store para publicação, o Microsoft realiza uma série de verificações de verificação para garantir que o suplemento de acordo com os suplemento de conteúdo e comportamento diretrizes. Por exemplo, ele verifica se a marcação de manifesto suplemento é válida e foi concluída e verifica que qualquer pacotes de solução do SharePoint (arquivos. wsp) que estão incluídos no suplemento não contêm os elementos que não são permitidos ou recursos do SharePoint com um escopo que é superando Web. O pacote também é inspecionado para conteúdo questionável. Se o pacote de suplemento passa todos os testes, ele tem quebrado automaticamente em um arquivo e assinado pela Microsoft.
  
    
    
Quando você carrega o suplemento para publicação no Office Store, você pode escolher os termos da licença do qual que você deseja oferecer aos usuários quando eles baixá-lo. Use essa licença suplemento para decidir:
  
    
    

- Se você estiver oferecendo o add-in para avaliação gratuita, ou para compra.
    
  
- Se seu suplemento pode ser adquirido em uma base por usuário ou site.
    
  
SharePoint não impõe termos de licença para o uso do suplemento  ele fornece uma estrutura de licenciamento que permite que você inclua lógica de código em seu suplemento para impor qualquer restrições de licenciamento que você escolher. Por exemplo, você pode incluir lógica de código em seu suplemento que permite aos usuários acessar determinados recursos de suplemento, caso possuam uma licença paga, mas não se eles têm uma licença de avaliação. Para obter mais informações, consulte  [Licenciar seu Office e SharePoint complementos](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx).
  
    
    

### Publicação de um catálogo de suplemento

Se você estiver criando Suplementos do SharePoint para uso da sua própria empresa ou um cliente corporativo específico, em vez do público em geral, você provavelmente deseja publicar o add-in em um catálogo de suplemento interno hospedado no SharePoint. Um catálogo de suplemento privado é um conjunto de sites dedicado em um aplicativo da web SharePoint 2013 (ou um locatário SharePoint Online ) que hospeda as bibliotecas de documentos para Suplementos do SharePoint e Suplementos do Office. Colocar o catálogo em seu próprio conjunto de sites torna mais fácil para o administrador do aplicativo web ou o administrador de locatário limitar as permissões para o catálogo.
  
    
    
Carregando um Suplemento do SharePoint um suplemento do catálogo corporativo é tão fácil quanto o carregamento de qualquer arquivo em uma biblioteca de documentos do SharePoint. Preencha um formulário pop-up, na qual você fornecer a URL do local do pacote de suplemento e outras informações, como o nome do add-in. Ao carregar o suplemento para um catálogo de suplemento, existem verificações semelhantes e suplementos que não passar estão marcados como inválidos ou desabilitados no catálogo.
  
    
    

## Decidir onde publicar seu Suplemento do SharePoint
<a name="bk_decide"> </a>

As ofertas de tabela a seguir uma comparação da publicação para o Office Store ou para um catálogo de suplemento e listas de problemas a serem considerados ao decidir onde publicar seu suplemento. Recomendamos que você decidir onde você planeja publicar seu suplemento antes de projetar e desenvolvê-lo; em alguns casos, como o licenciamento, onde você publica seu suplemento afetará o design e desenvolvimento de seu suplemento.
  
    
    

**Tabela 1. Considerações sobre para onde publicar seu suplemento**


|**Office Store**|**Suplemento do catálogo**|
|:-----|:-----|
|Suplemento está disponível publicamente. <br/> |Suplemento está disponível para usuários com acesso a essa implantação do SharePoint <br/> |
|Framework de licenciamento disponível. <br/> |Framework de licenciamento não está disponível para uso. <br/> |
|Suplemento pacote verificado pela Microsoft para conteúdo e técnico adesão a políticas. <br/> |Verificação de pacote suplementar executada pelo SharePoint quando o suplemento for carregado. <br/> |
|Você deve ser inscreveu com Microsoft painel do vendedor para carregar suplementos. <br/> |Nenhum registro com exigido pela Microsoft. <br/> |
   

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Criar ou atualizar IDs de cliente e segredos no painel do vendedor](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)
    
  
-  [Use o painel do vendedor enviem aplicativos do Office e suplementos do SharePoint e Office 365 para a Office Store](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)
    
  
-  [Políticas de validação de aplicativos e suplementos enviados para a Office Store (versão 2.1)](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)
    
  
-  [Start building Office and SharePoint Add-ins](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea.aspx)
    
  
-  [Licenciar seu Office e SharePoint complementos](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)
    
  
-  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Aluguéis são e escopos de implantação para o SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  
-  [Publicar suplementos do SharePoint usando o Visual Studio](publish-sharepoint-add-ins-by-using-visual-studio.md)
    
  
-  [Repositório de suplementos do Office de publicação](http://social.msdn.microsoft.com/Forums/en-US/officestore)
    
  

