---
title: Aluguéis são e escopos de implantação para o SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 1ceb3142-a7a5-453e-920f-5f953a79401a
---


# Aluguéis são e escopos de implantação para o SharePoint Add-ins
Saiba mais sobre o conceito de aluguéis são e as diferenças entre a implantação do Suplementos do SharePoint no escopo de Inquilino e o escopo da web.
## Escopo de suplemento e aluguéis são
<a name="AppScope"> </a>

Um SharePoint 2013 locação é um conjunto de conjuntos de sites em um farm do SharePoint ou em SharePoint Online. Em SharePoint Online, os conjuntos de sites pertencem a uma única conta de cliente. Em um farm do SharePoint, os conjuntos de sites podem ser todas as coleções de sites em um aplicativo web do SharePoint ou um subconjunto de, ou ele pode ser um conjunto de conjuntos de sites de entre vários aplicativos web no farm. Um locatário pode ter um catálogo de suplemento Suplemento do SharePoint exatamente como um aplicativo web do SharePoint pode.
  
    
    
Um Suplemento do SharePoint tem um suplemento do escopo. Os dois possíveis suplemento escopos são o escopo da web oulocatário. A diferença não é uma propriedade intrínseca do add-in e não você, o desenvolvedor, decida qual é o escopo do seu suplemento. A decisão será tomada pelos administradores de Inquilino como efeito lado de como o suplemento é instalado. Depois que um suplemento for carregado no catálogo add-in de um inquilino, é imediatamente disponível para ser instalado em sites dentro de aluguel em cada site por site. Suplementos instalados dessa maneira têm escopo de web. No entanto, os administradores dos locatários têm outra opção. Eles podem optar por instalar o suplemento a um subconjunto de sites dentro de aluguel de lote. Suplementos instalados dessa maneira têm escopo de locatário. O administrador proprietário pode especificar os sites que o suplemento é instalado por meio de uma lista de caminhos gerenciados, uma lista de modelos de site ou uma lista de conjuntos de sites. Um suplemento que tenha sido instalado no lote só pode ser desinstalado por um administrador de locatário. Quando o administrador proprietário desinstala o suplemento, ele é desinstalado de cada site na aluguel. Os usuários não é possível desinstalar um lote instalada suplemento em cada site por site. O mesmo princípio se aplica ao atualizar um suplemento instalado no lote: apenas um administrador de inquilino pode executá-la e ela é atualizada em lote em cada site na aluguel onde ele está instalado.
  
    
    
Se um suplemento que inclui um suplemento de web estiver instalado no lote, apenas um suplemento de web é criada e ele é compartilhado por todos os sites de host em que o suplemento está instalado. Suplemento de web está localizada no conjunto de sites do catálogo de suplemento da organização.
  
    
    
Quando novos conjuntos de sites são criados no aluguel, suplementos lote-instalados anteriormente são instalados automaticamente em um novo conjunto de sites.
  
    
    

> **OBSERVAçãO**
> Escopo de suplemento não deve ser confundido com o escopo do recurso. Escopo do recurso determina onde os elementos em um recurso estão implantados. As possibilidades incluem **Farm**, **WebApplication**, **Web**e **Site** (ou seja, o conjunto de sites). **Web** é permitida para recursos no Suplementos do SharePoint (ambos hospedam os recursos da web e recursos dentro de um. wsp em um pacote de suplemento).> Suplemento de escopo, também, não deve ser confundido com níveis de permissão do suplemento. Suplementos do SharePoint pode solicitar permissões a todos ou partes do conteúdo do SharePoint nos níveis de lista, web, conjunto de sites e locação selecionadas. Instalar um suplemento com escopo de locatário não dê a ela permissões que ele não teriam, nem cancelar a chaves cláusulas do modelo de segurança do SharePoint. Para obter mais informações sobre as permissões de suplemento, consulte  [Suplemento permissões no SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
  
    
    


## Limitações de escopo de locatário suplementos
<a name="Tenant"> </a>

Os seguintes tipos de suplementos não podem ser instalado no lote:
  
    
    

- Suplementos que contêm uma ação personalizada da faixa de opções. (Ações personalizadas que são implantadas como itens de menu são permitidos.)
    
  
- Suplementos que contêm uma parte do suplemento.
    
  
Além disso, observe novamente que não é possível na versão do Office 365 Small Business Premium do SharePoint Online instalar com escopo de locatário.
  
    
    

## Como instalar, desinstalar e atualizar um suplemento em vários sites em um locatário
<a name="Web"> </a>

Independentemente de um suplemento é instalado a partir do Office Store ou um catálogo de suplemento, administradores de Inquilino podem instalá-lo em vários sites em uma locação, desinstalá-lo e atualizá-lo usando os procedimentos a seguir.
  
    
    

### Para instalar um Add-in do SharePoint em vários sites


1. Navegue até a página de **Conteúdo do Site** do site do catálogo corporativo.
    
  
2. Selecione **Adicionar um add-in** e instalar o suplemento como faria em qualquer outro site do SharePoint.
    
  
3. Depois que o add-in estiver instalado, passe o mouse sobre o link para o suplemento na página **Conteúdo** do Site. Um link de " **…** " é exibido.
    
  
4. Selecione o link e um texto explicativo é exibida.
    
  
5. Selecione a **implantação** no menu.
    
  
6. Use a interface do usuário que abre para especificar os conjuntos de sites ao qual você deseja que o suplemento instalado de implantação. Você pode filtrar por caminhos gerenciados, modelos de site ou URLs. Os filtros têm uma relação de "Ou" lógica: add-in estiver instalado a união de todos os conjuntos de sites que passa de um ou mais dos filtros.
    
  
7. Selecione **OK**.
    
  

### Para desinstalar um lote instalada SharePoint Add-in


1. Navegue até a página de **Conteúdo do Site** do site do catálogo corporativo.
    
  
2. Passe o mouse sobre o link para o suplemento na página **Conteúdo** do Site. Um link de " **…** " é exibido.
    
  
3. Selecione o link e um texto explicativo é exibida.
    
  
4. Selecione **Remover** o texto explicativo.
    
  

### Para atualizar um lote instalada SharePoint Add-in


1. Navegue até a página de **Conteúdo do Site** do site do catálogo corporativo. Se houver uma atualização disponível para um suplemento, uma mensagem aparece ao lado do suplemento. Haverá um link para atualizar o suplemento.
    
  
2. Clique no link para atualizar o suplemento.
    
  
3. Quando você for solicitado para aprovar as solicitações de permissão do add-in, escolha **Confia**.
    
  

## Recursos adicionais
<a name="SP15tenancies_addlresources"> </a>


-  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [Processo de atualização de suplementos do SharePoint](sharepoint-add-ins-update-process.md)
    
  

