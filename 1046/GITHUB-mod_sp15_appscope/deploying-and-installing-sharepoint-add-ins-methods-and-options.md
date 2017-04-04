---
title: Implantando e instalando os suplementos do SharePoint métodos e opções
ms.prod: SHAREPOINT
ms.assetid: d15a74a7-3c10-485a-9885-7ef11aaa0d90
---


# Implantando e instalando os suplementos do SharePoint: métodos e opções
Saiba mais sobre os métodos de publicação, instalação e desinstalação em um Suplemento do SharePoint.
## Publicação para o Office Store ou o catálogo do suplemento de uma organização
<a name="MarketOrCatalog"> </a>

Você pode carregar seus Suplemento do SharePoint para o público Office Store ou para privada suplemento catálogo uma organização. Um catálogo de suplemento privado é um conjunto de sites dedicado em um aplicativo da web SharePoint 2013 (ou um locatário SharePoint Online ) que hospeda as bibliotecas de documentos para Suplementos do SharePoint e Suplementos do Office. Colocar o catálogo em seu próprio conjunto de sites torna mais fácil para o administrador do aplicativo web ou o administrador de locatário limitar as permissões para o catálogo.
  
    
    
Se o suplemento for carregado para o Office Store, Microsoft executa algumas verificações de validação contidas nela. Por exemplo, ele verifica se a marcação de manifesto suplemento é válida e foi concluída e verifica se qualquer pacotes de solução do SharePoint (arquivos. wsp) incluídos não incluir elementos não autorizados ou recursos com um escopo mais abrangente que **Web**. O conteúdo do pacote também é inspecionado para conteúdo questionável. Se o suplemento passa todos os testes, o pacote de suplemento é quebrado automaticamente em um arquivo e assinado pela Microsoft.
  
    
    

> **OBSERVAçãO**
> Quando você estiver desenvolvendo o add-in e implantá-lo com Microsoft Office Developer Tools for Visual Studio, o suplemento é instalado diretamente no site do SharePoint de teste de destino. Desde que ele não está passando por meio do Office Store, não ocorrerá a verificação de validação descrito acima.
  
    
    

Carregando um Suplemento do SharePoint catálogo do suplemento de uma organização é tão fácil quanto o carregamento de qualquer arquivo em uma biblioteca de documentos SharePoint Foundation. Preencha um formulário pop-up, na qual você fornecer a URL do local do pacote de suplemento e outras informações, como o nome do add-in. Quando o suplemento for carregado para suplemento catálogo uma organização, semelhante verificações ocorrerão e suplementos que não passar estão marcados como inválidos ou desabilitados no catálogo.
  
    
    
Administrador de locatários ou administradores de aplicativo web SharePoint 2013 podem comprar Suplementos do SharePoint sobre o Office Store. Para abrir o Office Store, o administrador seleciona **Adicionar um suplemento** na página **Conteúdo do Site** e, em seguida, seleciona o **Repositório do SharePoint** na página **seus suplementos**. Isso abre uma página de **Repositório do SharePoint** que os administradores podem usar para descobrir e saiba mais sobre Suplementos do SharePoint que fornecedores estão oferecendo. (Ele também podem fazer isso no office.com.) Suplementos que exigem um pré-requisito que não está instalado no aplicativo web ou a locação do administrador aparecem esmaecidos e não estão disponíveis no repositório de suplemento. Por exemplo, se um suplemento requer serviços Pesquisa e não estiver instalado, o suplemento aparece esmaecido. Os administradores podem classificar, filtrar e navegar pela lista de suplementos, leia sobre os suplementos, consulte add-in revisões e adquirir licenças para um suplemento.
  
    
    
Quando um administrador decide adquirir uma licença, ela deve aceitar os termos e condições de compra e concorda para as permissões que o suplemento deve ter para executar, como read access a listas ou acesso de controle total ao conjunto de sites.
  
    
    
Quando um ou mais licenças para um suplemento serão compradas, as licenças são baixadas para o aplicativo web ou aluguel. O suplemento é não baixado e instalado automaticamente quando a licença é comprada, embora os administradores têm a opção para combinar a instalação com a compra de licenças.
  
    
    
Os usuários instalar suplementos da página **seus suplementos**. Esta página tem uma listagem mesclada das seguintes opções:
  
    
    

- Suplementos do SharePoint do aplicativo da web (ou de locatário) organização suplemento catálogo.
    
  
- Suplementos do SharePoint de Office Store para os quais a organização ou Locatário já possui uma licença de site ou uma licença que foi atribuída ao usuário.
    
  
Todos os suplementos que o usuário pode instalar imediatamente estão listados e, com poucas exceções add-ins somente que o usuário pode instalar imediatamente estão listados. Os usuários podem filtrar os suplementos incluídos na página para incluir somente suplementos no catálogo de suplemento da organização. Quando um add-in estiver instalado, ele aparece na lista de suplementos na página **Conteúdo do Site** do site ao qual ele está instalado.
  
    
    

## Instalando os suplementos do SharePoint
<a name="Installing"> </a>

Proprietários de sites instalar Suplementos do SharePoint da página **seus suplementos**, conforme descrito anteriormente neste tópico. A instalação cria uma instância do add-in. Para obter mais informações sobre como instalar suplementos, consulte  [Adicionar SharePoint suplementos a um site do SharePoint 2013](https://technet.microsoft.com/en-us/library/fp161231.aspx).
  
    
    

> **OBSERVAçãO**
> Em alguns casos, uma perda temporária de uma conexão de rede pode bloquear a instalação. Se a instalação falhar por qualquer motivo, a infraestrutura de instalação repetirá três vezes. Se não tiver êxito, uma indicação da falha aparece na interface do usuário. Os usuários podem tentar novamente a instalação mais tarde.
  
    
    


## Desinstalando os suplementos do SharePoint
<a name="Uninstalling"> </a>

Proprietários de sites podem desinstalar uma instância de um Suplemento do SharePoint por meio da UI do SharePoint. Desinstalação de uma instância de um Suplementos do SharePoint está limpa. Isso significa que tudo instalados pelo suplemento é desinstalado.
  
    
    
No entanto, os componentes que um suplemento usa, mas que são instaladas separadamente da instalação do add-in, não serão removidos. Por exemplo, suponhamos que um suplemento tem uma página da web remoto com botões que criar listas na web host. A desinstalação da remove suplemento blocos do add-in da página **Conteúdo do Site** do qual, por sua vez, torna a página remota efetivamente inacessível ou não-utilizável para usuários finais, mas não removerá as listas que foram criadas com o SharePoint suplemento não manter um registro das quais listas na web host foram criadas com o suplemento e quais foram criados por usuários da UI do SharePoint , portanto, ela não é possível excluir listas que foram criadas com o suplemento. Esse é o comportamento de geralmente desejável porque as listas podem ter dados permanecem útil aos usuários mesmo após a remoção do add-in que criou as listas.
  
    
    
Se o Suplemento do SharePoint inclui um suplemento de web, o suplemento da web é excluída. Isso oferece uma limpeza desinstalar que sistemática desativando recursos e interno da reversão da implantação do suplemento arquivo. wsp.
  
    
    

> **OBSERVAçãO**
> Quando um usuário remove um suplemento, ele é movido para a Lixeira de primeira estágio. Excluí-lo a partir daí move para a Lixeira de segunda estágio. Se ele for excluído da Lixeira de segundo estágio, ela é desinstalada completamente e não pode ser restaurada.
  
    
    

Permissões do suplemento também serão revogadas quando ele é removido (reciclado), de acordo com estas regras:
  
    
    

- Permissões de escopo de Web sempre serão revogadas.
    
  
- Se não houver nenhuma outra instância do add-in no conjunto de sites, permissões de escopo de conjunto de sites também serão revogadas.
    
  
- Se não houver nenhuma outra instância do add-in na inscrição de site (locação), as permissões no escopo do inquilino também serão revogadas.
    
  
O serviço web de  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) , se um está registrado no manifesto do suplemento do add-in, executa no início do processo de desinstalação (o que ocorre quando o suplemento é removido da Lixeira de segundo estágio). É uma prática recomendada para ter um serviço de web de [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) se você tiver um serviço da web [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) e projetar o serviço [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) para reverter a qualquer coisa feita no seu serviço [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) . Para obter mais informações, consulte [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md).
  
    
    

## Recursos adicionais
<a name="SP15deployinstallapps_addlresources"> </a>


-  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md)
    
  

