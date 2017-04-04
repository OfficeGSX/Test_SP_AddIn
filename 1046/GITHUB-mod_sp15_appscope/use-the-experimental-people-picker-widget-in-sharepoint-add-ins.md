---
title: Use o widget experimental do seletor de pessoas no SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: edc60550-67d2-4230-8e27-06a328c0d1f1
---


# Use o widget experimental do seletor de pessoas no SharePoint Add-ins
Aprenda a usar o widget seletor de pessoas em qualquer página da web, mesmo se a página não for hospedada na SharePoint. Use o widget People Picker seus suplementos para ajudar a localizar usuários e selecionar pessoas e grupos.
> **CUIDADO**
> Widgets da Web do Office - Experimental são fornecidos apenas para fins de pesquisa e comentários. Não use em cenários de produção. O comportamento do Office Web Widgets pode ser alterado significativamente em futuras versões. Leia e revise o  [Office Web Widgets - termos de licença Experimental](office-web-widgetsexperimental-license-terms.md).
  
    
    

Você pode usar o widget experimental do seletor de pessoas em suplementos para ajudar seus usuários localizar e selecionar pessoas e grupos em um locatário. Os usuários podem começar a digitar na caixa de texto e o widget recupera as pessoas cujo nome ou email coincida com o texto.
**Figura 1. Widget do seletor de pessoas a solução de uma consulta**

  
    
    

  
    
    
![Controle experimental do Seletor de Pessoas em uma página](images/PeoplePicker_basic.png)
  
    
    

  
    
    

  
    
    
Seu suplemento pode acessar as pessoas selecionadas lendo a propriedade **SelectedItem** do widget. A propriedade SelectedItem é uma matriz de objetos que representam pessoas ou grupos. A tabela a seguir mostra as propriedades disponíveis do objeto de usuário.

|**Propriedade**|**Descrição**|
|:-----|:-----|
|**Departamento** <br/> |Representa o departamento do usuário ou grupo. <br/> |
|**displayName** <br/> |Representa o nome de exibição do usuário ou grupo. <br/> |
|**Email** <br/> |Representa o endereço de email do usuário ou grupo. <br/> |
|**isResolved** <br/> |Indica se o widget tem resolvidos com êxito o texto no widget em relação a um usuário ou grupo no inquilino. <br/> |
|**jobTitle** <br/> |Representa o cargo do usuário. <br/> |
|**loginName** <br/> |Representa o nome de logon do usuário ou grupo. <br/> |
|**Mobile** <br/> |Representa o número de telefone celular do usuário ou grupo. <br/> |
|**principalId** <br/> |Representa o ID da entidade do usuário ou grupo. <br/> |
|**principalType** <br/> |Indica se o item é um usuário ou grupo. Ele tem um valor de 1 se for um usuário, 4 se há um grupo. <br/> |
|**sipAddress** <br/> |Representa o endereço sip do usuário ou grupo. <br/> |
|**Texto** <br/> |Representa o título de texto do nome do usuário ou grupo. <br/> |
   
O widget seletor de pessoas tem um cache das entradas usados mais recentemente (MRU). O cache armazena as cinco entradas mais recentes que o widget resolvido.
## Pré-requisitos para uso dos exemplos neste artigo

Para usar os exemplos neste artigo, você precisará dos seguintes itens:
  
    
    

- Visual Studio 2013.
    
  
- NuGet Package Manager. Para obter mais informações, consulte  [Instalando NuGet](http://go.microsoft.com/fwlink/?LinkId=271465).
    
  
- Um ambiente de desenvolvimento SharePoint 2013 (isolamento de aplicativo necessário para os cenários de local).
    
  
- Office Web Widgets - pacote NuGet Experimental. Para obter mais informações sobre como instalar um pacote do NuGet, consulte  [Gerenciando NuGet pacotes usando a caixa de diálogo](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog). Você também pode navegar a  [página de galeria do NuGet](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/).
    
  

## Usar o widget seletor de pessoas em um provedor hospedado Suplemento do SharePoint

Neste exemplo, há uma página simples hospedada fora SharePoint que declara um widget People Picker usando marcação. Para manter a simplicidade, este exemplo não declara quaisquer opções, mas você pode ver um exemplo com opções na seção  [NextSteps](use-the-experimental-people-picker-widget-in-sharepoint-add-ins.md#NextSteps) .
  
    
    
Para usar o widget seletor de pessoas, faça o seguinte:
  
    
    

- Crie projetos da web e Suplemento do SharePoint.
    
  
- Crie um módulo na web add-in. Essa etapa garante que um suplemento de web é criado quando os usuários implantar o suplemento.
    
    > **OBSERVAçãO**
      > Biblioteca do domínio cruzado requer a existência de um suplemento de web. O widget seletor de pessoas se comunica com SharePoint usando a biblioteca de domínio cruzado.
- Crie uma página de suplemento que declara uma instância de widget People Picker usando marcação.
    
  

### Para criar um Suplemento do SharePoint e projetos web


1. Abra o Visual Studio 2013 como administrador. (Para fazer isso, escolha o ícone do Visual Studio 2013 no menu **Iniciar** e escolha **Executar como administrador** ).
    
  
2. Crie um novo projeto usando o modelo Suplemento do SharePoint 2013. O modelo de **Suplemento do SharePoint 2013** está localizado em **modelos** > **Visual c#**, **Office /SharePoint**> **suplementos**.
    
  
3. Forneça a URL do site SharePoint que você deseja usar para depuração.
    
  
4. Selecione **hospedado em provedor**, como a opção de hospedagem para seu suplemento.
    
    > **OBSERVAçãO**
      > Você também pode usar o widget seletor de pessoas com outras opções de hospedagem ou mesmo com suplementos do Office ou seu próprio site.
5. Selecione o **Aplicativo de formulários ASP.NET Web** como o tipo de projeto de aplicativo web.
    
  
6. Selecione o **Serviço de controle de acesso do Windows Azure** como a opção de autenticação.
    
  

### Para criar um módulo na web suplemento


1. Escolha o projeto Suplemento do SharePoint no **Solution Explorer**. Escolha **Adicionar** > **Novo Item …**
    
  
2. Escolher **itens do Visual c#** > **Office /SharePoint**> **módulo**. Forneça um nome para o seu módulo.
    
    > **OBSERVAçãO**
      > Se você estiver criando um suplemento hospedado no SharePoint, você não precisará criar um módulo extra.

### Para adicionar uma nova página que usa o widget seletor de pessoas


1. Escolha a pasta de **páginas** no projeto da web no **Solution Explorer**.
    
  
2. Copie o código a seguir e cole-o em um arquivo **ASPX** no projeto. O código executa as seguintes tarefas:
    
  - Adiciona referências às bibliotecas necessárias do Office e aos recursos.
    
  
  - Inicializa o tempo de execução de controles.
    
  
  - Executa o método **renderAll** do runtime controles do Office.
    
  
  - Declara um espaço reservado para o widget People Picker.
    
  

  ```
  
<!DOCTYPE html>
<html>
<head>
    <!-- IE9 or superior -->
    <meta http-equiv="X-UA-Compatible" content="IE=9" >
    <title>People Picker HTML Markup</title>

    <!-- Widgets Specific CSS File -->
    <link 
        rel="stylesheet" 
        type="text/css" 
        href="../Scripts/Office.Controls.css" 
    />

    <!-- Ajax, jQuery, and utils --> 
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/4.0/1/MicrosoftAjax.js.js">
    </script>
    <script 
        src=" https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js">
    </script>
    <script type="text/javascript">
        // Function to retrieve a query string value.
        // For production purposes you may want to use
        //  a library to handle the query string.
        function getQueryStringParameter(paramToRetrieve) {
            var params =
                document.URL.split("?")[1].split("&amp;");
            var strParams = "";
            for (var i = 0; i < params.length; i = i + 1) {
                var singleParam = params[i].split("=");
                if (singleParam[0] == paramToRetrieve)
                    return singleParam[1];
            }
        }
    </script>

    <!-- Cross-Domain Library and Office controls runtime -->
    <script type="text/javascript">
        //Register namespace and variables used through the sample
        Type.registerNamespace("Office.Samples.PeoplePickerBasic");
        //Retrieve context tokens from the querystring
        Office.Samples.PeoplePickerBasic.appWebUrl =
            decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));
        Office.Samples.PeoplePickerBasic.hostWebUrl =
            decodeURIComponent(getQueryStringParameter("SPHostUrl"));

        //Pattern to dynamically load JSOM and and the cross-domain library
        var scriptbase =
            Office.Samples.PeoplePickerBasic.hostWebUrl + "/_layouts/15/";

        //Get the cross-domain library
        $.getScript(scriptbase + "SP.RequestExecutor.js",
            //Get the Office controls runtime and 
            //  continue to the createControl function
            function () {
                $.getScript("../Scripts/Office.Controls.js", createControl)
            }
        );
    </script>

    <!--People Picker -->
    <script 
        src="../Scripts/Office.Controls.PeoplePicker.js" 
        type="text/javascript">
    </script>
</head>
<body>
Basic People Picker sample (HTML markup declaration):
<div 
        id="PeoplePickerDiv" 
        data-office-control="Office.Controls.PeoplePicker">
</div>

<script type="text/javascript">
    function createControl() {
        //Initialize Controls Runtime
        Office.Controls.Runtime.initialize({
            sharePointHostUrl: Office.Samples.PeoplePickerBasic.hostWebUrl,
            appWebUrl: Office.Samples.PeoplePickerBasic.appWebUrl
        });

        //Render the widget, this must be executed after the
        //placeholder DOM is loaded
        Office.Controls.Runtime.renderAll();
    }
</script>
</body>
</html>

  ```


> **OBSERVAçãO**
> O exemplo de código acima especifica explicitamente os host web e o suplemento web URLs para inicializar o tempo de execução de controles do Office. No entanto, se o suplemento de web e URLs de web de host são especificados nos **SPAppWebUrl** **SPHostUrl** consulta string parâmetros e, respectivamente; Você pode passar um objeto vazio e o código tentará obter os parâmetros automaticamente. Os parâmetros **SPAppWebUrl** e **SPHostUrl** estão incluídos na seqüência de consulta quando você usa o token **{StandardTokens}**.
  
    
    

O exemplo a seguir mostra como passar um objeto vazio para o método initialize:
  
    
    



```

// Initialize with an empty object and the code
// will attempt to get the tokens from the
// query string directly.
Office.Controls.Runtime.initialize({});
```


### Para criar e executar a solução


1. Pressione a tecla F5.
    
    > **OBSERVAçãO**
      > Quando você pressiona F5, Visual Studio cria a solução, implanta o suplemento e abre a página de permissões para o suplemento.
2. Escolha o botão **De confiança**.
    
  
3. Escolha o ícone de suplemento na página **Conteúdo do Site**.
    
  
Você também pode baixar esta amostra da Galeria de código, consulte o exemplo de código  [uso do widget experimental do seletor de pessoas em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85) .
  
    
    

## 
<a name="NextSteps"> </a>

Este artigo mostra como usar o widget seletor de pessoas no seu suplemento usando HTML. Você também pode explorar os seguintes cenários e detalhes sobre o widget.
  
    
    

### Use JavaScript para declarar o widget seletor de pessoas

Dependendo da sua preferência, convém usar o JavaScript em vez de HTML para declarar o widget. Se este for o caso, você pode usar a seguinte marcação como o espaço reservado do widget.
  
    
    

```HTML

<div id="PeoplePickerDiv"></div>
```

Use o seguinte código JavaScript para instanciar o People Picker.
  
    
    



```
new Office.Controls.PeoplePicker(
    document.getElementById("PeoplePickerDiv"), {});
```

Para um exemplo de código que mostra como executar as tarefas, consulte a página **JSSimple.html** na amostra de código de [uso do widget experimental do seletor de pessoas em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85) .
  
    
    

### Especifique as opções para o widget

Você pode especificar opções para o widget usando-se o atributo de **Opções do office de dados** na declaração widget. O HTML a seguir mostra como especificar as opções para o widget PeoplePicker.
  
    
    

```HTML

<div id="PeoplePickerDiv"
        data-office-control="Office.Controls.PeoplePicker"
        data-office-options='{
        "allowMultipleSelections" : true,
        "onChange" : handleChange,
        "placeholder" : "Check the count message, it changes when you add names..."
    }'>
</div>
```

O código a seguir mostra como especificar opções quando você declara o widget PeoplePicker usando JavaScript.
  
    
    



```

new Office.Controls.PeoplePicker(
    document.getElementById("PeoplePickerDiv"), {
        allowMultipleSelections: true,
        placeholder: "Check the count message, it changes when you add names...",
        onChange: function (ctrl) {
            document.getElementById("count").textContent = 
ctrl.selectedItems.length.toString();
        }
    });
```

Você também pode especificar os manipuladores de eventos para os eventos **onChange**, **onAdded** e **onRemoved**. No código acima, observe que o manipulador de eventos para o evento onChange recebe um único parâmetro **ctrl**, que é uma referência ao widget.
  
    
    
Para obter um exemplo de como especificar opções, consulte as páginas de **MarkupOptions.html** e **JSOptions.html** na amostra de código de [uso do widget experimental do seletor de pessoas em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85) .
  
    
    

### Recuperar as pessoas ou grupos selecionados no widget

Para recuperar as pessoas no widget, você deve executar as seguintes tarefas:
  
    
    

- Obter uma referência ao widget.
    
  
- Acesse a propriedade **SelectedItem** do widget.
    
  
Você pode obter uma referência ao widget usando a seguinte sintaxe.
  
    
    



```

var pplPicker = document.getElementById("PeoplePickerDiv")._officeControl;
```

Você também pode salvar uma referência ao instanciar o widget.
  
    
    



```
var pplPicker = new Office.Controls.PeoplePicker(
                        document.getElementById("PeoplePickerDiv"), {});
```

A propriedade **SelectedItem** é uma matriz de objetos que representam pessoas ou grupos. Pessoas ou grupos na matriz SelectedItem podem ser resolvidos ou não resolvidos, que você pode verificar na propriedade **isResolved**. O exemplo a seguir mostra como acessar o elemento *i*  na matriz e use o nome da pessoa ou grupo.
  
    
    



```

var principal = pplPicker.selectedItems[i];
$("#msg").text(principal.text + " is selected in the control.");
```

Para obter um exemplo de como recuperar as pessoas selecionadas ou grupos de widget, consulte a página de **demo.html** do [Office Web Widgets - demonstração Experimental](http://code.msdn.microsoft.com/SharePoint-2013-Office-Web-6d44aa9e) código de exemplo.
  
    
    

### Personalizar o estilo do widget

Como um desenvolvedor, talvez você queira personalizar a aparência do widget &amp; aparência. A imagem a seguir mostra a hierarquia HTML no widget depois que ele tenha sido processado.
  
    
    

**Figura 2. Hierarquia HTML no widget seletor de pessoas**

  
    
    

  
    
    
![Hierarquia HTML no controle Seletor de Pessoas](images/PeoplePicker_HTMLHierarchy.png)
  
    
    
O widget define várias classes prefixados por **peoplepicker do office**, que você pode localizar e personalizar na folha de estilos **Office.Controls.css**.
  
    
    

## Conclusion
<a name="NextSteps"> </a>

Você pode usar o widget experimental do seletor de pessoas para selecionar pessoas e grupos no seu locatário, seu suplemento pode usar as entidades selecionadas por seus usuários. Forneça idéias e comentários no  [site Office Developer plataforma UserVoice](http://officespdev.uservoice.com/).
  
    
    

## Recursos adicionais
<a name="bk_addresources"> </a>


-  [Office Web Widgets - visão geral Experimental](office-web-widgetsexperimental-overview.md)
    
  
-  [Office Web Widgets - termos de licença Experimental](office-web-widgetsexperimental-license-terms.md)
    
  
-  [Office Web Widgets - página de galeria do NuGet Experimental](http://www.nuget.org/packages/Microsoft.Office.WebWidgets.Experimental/)
    
  
-  [Amostra de código: usar o widget experimental do seletor de pessoas em um suplemento](http://code.msdn.microsoft.com/SharePoint-2013-Use-the-57859f85).
    
  
-  [Use o widget experimental do modo de exibição de lista de área de trabalho no SharePoint Add-ins](use-the-experimental-desktop-list-view-widget-in-sharepoint-add-ins.md) .
    
  

