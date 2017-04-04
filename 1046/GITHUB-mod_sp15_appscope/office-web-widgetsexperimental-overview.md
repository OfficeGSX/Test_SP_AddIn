---
title: Office Web Widgets - visão geral Experimental
ms.prod: SHAREPOINT
ms.assetid: 6ce01956-6bda-45bf-9b4a-cffc0687a913
---



# Office Web Widgets - visão geral Experimental
Saiba mais sobre o Office Web Widgets - Experimental que você pode usar em Suplementos do Office, Suplementos do SharePoint e sites.
> [!CUIDADO]
> Widgets da Web do Office - Experimental são fornecidos apenas para fins de pesquisa e comentários. Não use em cenários de produção. O comportamento do Office Web Widgets pode ser alterado significativamente em futuras versões. Leia e revise o  [Office Web Widgets - termos de licença Experimental](office-web-widgetsexperimental-license-terms.md).
  
    
    

Controles de cliente, como o Office Web Widgets - Experimental, significativamente pode reduzir a quantidade de tempo necessária para criar suplementos e ao mesmo tempo, aumentar a qualidade dos suplementos. Para isso ser verdadeiras, temos para garantir que os widgets atender a determinados critérios:
- Widgets devem ser projetados para ser usado em qualquer página da Web, mesmo se a página não estiver hospedada em SharePoint.
    
  
- Widgets funcionam dentro do tempo de execução de controles do Office. Esse recurso permite conosco para fornecer um conjunto de requisitos e uma sintaxe consistente para usar os widgets comum.
    
  
- Widgets que se comunicam voltar ao SharePoint usam a biblioteca de domínio cruzado. Widgets não têm uma dependência em uma determinada plataforma de servidor ou a tecnologia. Você pode usar os widgets independentemente de sua escolha de tecnologia de servidor.
    
  
- Widgets devem coexistir com outros elementos da página. A inclusão do widget a uma página não deve modificar outros elementos contidos nele.
    
  
- Reproduza BOM com estruturas existentes. Queremos ter certeza de que você ainda pode usar as ferramentas e tecnologias usadas para você.
    
  

**Figura 1. Um suplemento usando Widgets do Office Web - Experimental**

  
    
    

  
    
    
![Office Web Widgets - demonstração experimental](images/OfficeWebWidgetsOverview_demo.png)
  
    
    
Você pode usar os widgets instalando o pacote **Office Web Widgets - Experimental** NuGet da Visual Studio para obter mais informações, consulte [Gerenciando NuGet pacotes usando a caixa de diálogo](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog). Você também pode navegar a  [página de galeria do NuGet](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/).Seus comentários e comentários nos ajudaram a decidir quais widgets para fornecer. Como você pode ver na Figura 1, os widgets People Picker e modo de exibição de lista de área de trabalho (2) (1) estão prontos para que você tente e experimentar. Mantenha o feedback chegando até o site do Office Developer plataforma UserVoicehttp://officespdev.uservoice.com/ []()Você também pode ver os widgets em ação na amostra de código de  [Office Web Widgets - demonstração Experimental](http://code.msdn.microsoft.com/SharePoint-2013-Office-Web-6d44aa9e) .
## Widget do seletor de pessoas

Você pode usar o widget experimental do seletor de pessoas em suplementos para ajudar seus usuários localizar e selecionar pessoas e grupos em um locatário. Os usuários podem começar a digitar na caixa de texto e o widget recupera as pessoas cujo nome ou email coincida com o texto.
  
    
    

**Figura 2. Widget do seletor de pessoas a solução de uma consulta**

  
    
    

  
    
    
![Controle experimental do Seletor de Pessoas em uma página](images/PeoplePicker_basic.png)
  
    
    
Você pode declarar o widget na marcação HTML ou programaticamente usando o JavaScript. Em ambos os casos, você usa um elemento **div** como um espaço reservado para o widget. Você também pode definir propriedades e os manipuladores de eventos para o widget People Picker. A tabela a seguir mostra as propriedades disponíveis e eventos no widget People Picker.
  
    
    


|**Propriedade/evento**|**Tipo**|**Description**|
|:-----|:-----|:-----|
|**objectType** <br/> |Objeto JSON (lista de cadeias de caracteres) <br/> |Tipo de itens que resolverá o widget. Opções: <br/> User <br/> Grupo <br/> Apenas o usuário como padrão. <br/> |
|**allowMultipleSelections** <br/> |Booliano <br/> |Verdadeiro/falso. Se for False, o widget deve permitir a seleção de somente um item no momento.           Padrão = False. <br/> |
|**rootGroupName** <br/> |string <br/> |Se for fornecido, o widget limitará a seleção para itens no grupo.           Se não fornecido, o widget consultará objetos de aluguel inteira. <br/> |
|**selectedItems** <br/> |Matriz JSON <br/> |Lista de itens selecionados. Cada item retornará um objeto representando um usuário ou grupo. <br/> |
|**onAdded** <br/> |Função <br/> |Evento que é acionado quando um novo objeto é adicionado à seleção. A função handler recebeu o objeto adicionado. <br/> |
|**onRemoved** <br/> |Função <br/> |Evento que é acionado quando um novo objeto é removido da seleção. A função handler recebeu o objeto removido. <br/> |
|**onChange** <br/> |Função <br/> |A adição ou remoção de objetos dispara esse evento. Sem parâmetros são passados para a função handler. <br/> |
|**validationErrors** <br/> |matriz <br/> |Matriz de erros de validação possíveis: <br/> Esvaziar <br/> unresolvedItem <br/> tooManyItems <br/> |
|**autoShowValidationMessage** <br/> |Booleano <br/> |True = Mostrar          = False Don ' t show <br/> |
|**hasErrors** <br/> |Booleano <br/> |True = há erros de validação de 1 ou mais          False = não há nenhum erro de validação <br/> |
|**Erros** <br/> |matriz <br/> |Matriz de erros de validação possíveis: <br/> Esvaziar <br/> unresolvedItem <br/> tooManyItems <br/> |
|**displayErrors** <br/> |Booleano <br/> |True = exibir erros          = False Don ' t exibir erros <br/> |
   
As classes CSS para o widget People Picker são definidas na folha de estilos **Office.Controls.css**. Você pode substituir as classes e aplicar o estilo para seu suplemento.
  
    
    
Para obter mais informações, consulte  [Use o widget experimental do seletor de pessoas no SharePoint Add-ins](use-the-experimental-people-picker-widget-in-sharepoint-add-ins.md) e exemplo de código de [uso do widget experimental do seletor de pessoas em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85) .
  
    
    

## Widget da área de trabalho do modo de exibição de lista

Os usuários podem se beneficiar do widget do modo de exibição de lista e exibir os dados em uma lista, assim como o widget regular do modo de exibição de lista, mas você pode usá-lo em seus suplementos que não são necessariamente hospedados em SharePoint.
  
    
    

**Figura 3. Widget da área de trabalho do modo de exibição de lista exibindo os dados em uma lista**

  
    
    

  
    
    
![Controle experimental do Modo de Exibição de Lista da Área de Trabalho em uma página](images/DesktopListView_basic.png)
  
    
    
Você pode especificar uma exibição existente na lista, o widget renderiza os campos na ordem em que eles aparecem no modo de exibição.
  
    
    

    
> [!OBSERVAçãO]
> Neste momento, o widget do modo de exibição de lista de área de trabalho exibe apenas os dados. Ele não oferece recursos de edição.
  
    
    

Você pode fornecer um espaço reservado para o uso de um elemento **div** de widget. Você pode usar programaticamente ou forma declarativa widget.
  
    
    
Você também pode definir propriedades ou manipuladores de eventos para o widget do modo de exibição de lista de área de trabalho. A tabela a seguir mostra as propriedades disponíveis e eventos no widget modo de exibição de lista de área de trabalho.
  
    
    


|**Propriedade/evento**|**Tipo**|**Description**|
|:-----|:-----|:-----|
|**listUrl** <br/> |URL <br/> |URL do modo de exibição de lista para desenhar itens de. Pode ser uma URL relativa nesse caso, ele será considerado para estar localizado na web suplemento em si ou uma URL absoluta. <br/> |
|**viewName** <br/> |string <br/> |Nome do modo de exibição para mostrar. Este é o nome programático do modo de exibição (não seu nome para exibição). <br/> |
|**onItemSelected** <br/> |Função <br/> |Evento que é acionado quando um item é selecionado na lista. <br/> |
|**onItemAdded** <br/> |Função <br/> |Evento que é acionado quando um novo item é adicionado à lista. <br/> |
|**onItemRemoved** <br/> |Função <br/> |Evento que é acionado quando um item é removido da lista. <br/> |
|**selectedItems** <br/> |matriz <br/> |Lista de itens selecionados no formato JSON. <br/> |
   
O widget requer a folha de estilos do site SharePoint. Você pode fazer referência a folha de estilo SharePoint diretamente ou usar o widget chrome. Para obter mais informações sobre a folha de estilos, consulte  [Use a folha de estilos de um site SharePoint no SharePoint Add-ins](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) e [Use o controle de cromo do cliente no SharePoint Add-ins](use-the-client-chrome-control-in-sharepoint-add-ins.md).
  
    
    
Para ver o widget de modo de exibição de lista em ação, consulte o exemplo de código  [uso do widget experimental do modo de exibição de lista de área de trabalho em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076) . Consulte também [Use o widget experimental do modo de exibição de lista de área de trabalho no SharePoint Add-ins](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md).
  
    
    

## Conclusão

Widgets podem ajudar a acelerar o processo de desenvolvimento e reduzir os custos e o tempo de entrega de sua Widgets de Web do Office suplementos - Experimental fornecem widgets que você pode usar em seus suplementos de não-produção. Seus comentários e os comentários são bem-vindos no  [site Office Developer plataforma UserVoice](http://officespdev.uservoice.com/).
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Office Web Widgets - termos de licença Experimental](office-web-widgetsexperimental-license-terms.md)
    
  
-  [Office Web Widgets - página de galeria do NuGet Experimental](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)
    
  
-  [Use o widget experimental do seletor de pessoas no SharePoint Add-ins](use-the-experimental-people-picker-widget-in-sharepoint-add-ins.md)
    
  
-  [Amostra de código: Office Web Widgets - demonstração Experimental](http://code.msdn.microsoft.com/SharePoint-2013-Office-Web-6d44aa9e)
    
  
-  [Use o widget experimental do modo de exibição de lista de área de trabalho no SharePoint Add-ins](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md)
    
  
-  [Amostra de código: usar o widget experimental do seletor de pessoas em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85)
    
  
-  [Amostra de código: usar o widget experimental do modo de exibição de lista de área de trabalho em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-c3edb076)
    
  
