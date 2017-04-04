---
title: Consulta um serviço remoto usando o proxy da web no SharePoint 2013
ms.prod: SHAREPOINT
ms.assetid: 16913e6d-4fc6-4c5e-84a4-6c2688703798
---



# Consulta um serviço remoto usando o proxy da web no SharePoint 2013
Saiba como acessar dados em um domínio remoto de uma página que está hospedada no SharePoint 2013 usando o proxy da web.
Quando você estiver criando Suplementos do SharePoint, geralmente você precisa incorporar dados de várias fontes. Por motivos de segurança, existem mecanismos de bloqueio que impedem a comunicação entre domínios. Quando você usa o proxy da web, as páginas da Web em seu suplemento podem acessar os dados em seu domínio remoto e o domínio do SharePoint.
  
    
    

Como um desenvolvedor, você pode usar o proxy da web exposto no cliente APIs, como o JavaScript e modelos de objeto do cliente .NET. Quando você usa o proxy da web, você emitir a solicitação inicial para o SharePoint. Por sua vez, o SharePoint solicita os dados para o ponto de extremidade especificado e encaminha a resposta de volta para sua página. Use o proxy da web quando você deseja que a comunicação ocorra no nível do servidor. Para obter mais informações, consulte  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md).
**Proxy de Web do SharePoint é ataque intermediário entre o código e a fonte de dados externa**

  
    
    

  
    
    
![Símbolos para "seu código", "Proxy Web do SharePoint" e "Fonte de dados" mostrando que as solicitações de dados passam pelo proxy Web.](images/3fbdcfae-6af9-452b-9a07-48575de7e86a.png)
  
    
    

  
    
    

  
    
    

## Pré-requisitos para uso dos exemplos neste artigo
<a name="SP15Queryremoteservice_Prereq"> </a>

Para acompanhar as etapas deste exemplo, será necessário:
  
    
    

-  [Visual Studio 2015 and the latest Microsoft Office Developer Tools](https://www.visualstudio.com/features/office-tools-vs.aspx)
    
  
- Um ambiente de desenvolvimento SharePoint 2013 (suplemento isolamento necessário para os cenários de local)
    
  

### Principais conceitos saber antes de usar o proxy da web

A tabela a seguir lista alguns artigos úteis que podem ajudá-lo a entender os conceitos envolvidos em um cenário entre domínios Suplementos do SharePoint.
  
    
    

**Tabela 1. Conceitos fundamentais do proxy da web**


|**Título do artigo**|**Descrição**|
|:-----|:-----|
| [Suplementos do SharePoint](sharepoint-add-ins.md) <br/> |Saiba mais sobre o novo modelo suplemento em SharePoint 2013 que permite que você crie suplementos, que são pequenas e fácil de usar soluções para usuários finais. <br/> |
| [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> |Saiba mais sobre dados opções do access no Suplementos do SharePoint. Este artigo fornece orientação sobre as alternativas de alto nível que você tem à sua escolha ao trabalhar com dados em seu suplemento. <br/> |
| [Host webs, suplemento webs e componentes do SharePoint no SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md) <br/> |Saiba mais sobre a diferença entre webs de host e o suplemento webs. Descubra quais componentes SharePoint 2013 podem ser incluídos em um Suplemento do SharePoint, quais componentes são implantados na Web de host, quais componentes são implantados na Web de suplemento e como o suplemento web é implantada em um domínio isolado. <br/> |
| [Segurança de domínio cruzado do lado do cliente](http://msdn.microsoft.com/en-us/library/cc709423%28v=vs.85%29.aspx) <br/> |Explore entre domínios ameaças e casos de uso, os princípios de segurança para solicitações entre origens e avaliar os riscos para desenvolvedores aprimorar o acesso entre domínios de aplicativos web que são executados no navegador. <br/> |
   

## Exemplo de código: acessar dados em um serviço remoto usando o proxy da web
<a name="SP15Queryremoteservice_Codeexample"> </a>

Para ler dados de um serviço remoto, você deve fazer o seguinte:
  
    
    

1. Crie um projeto Suplemento do SharePoint.
    
  
2. Modifica a página **Default. aspx** para usar o proxy da web para o serviço remoto de consulta.
    
  
3. Modifica o manifesto do suplemento para permitir comunicação para o domínio remoto.
    
  
A Figura 1 mostra a janela de navegador com dados do serviço remoto em uma página da Web do SharePoint.
  
    
    

**Figura 1. Página da Web do SharePoint com dados de serviço remoto**

  
    
    

  
    
    
![SharePoint page with data from the remote service](images/WebProxy_result.png)
  
    
    

### Para criar o projeto Add-in do SharePoint


1. Abra 2015 como administrador. (Para fazer isso, clique com botão direito no ícone de 2015 no menu **Iniciar** e escolha **Executar como administrador** ).
    
  
2. Criar um novo projeto usando o modelo de **suplemento do SharePoint**
    
    Figura 2 mostra a localização do modelo **do suplemento do SharePoint** no 2015, em **modelos**, **Visual c#**, **Office/SharePoint**, **suplementos do Office**.
    

   **Figura 2. Add-in SharePoint modelo do Visual Studio**

  

     ![App for SharePoint 2013 Visual Studio template](images/AppForSharePointVSTemplate.PNG)
  

  

  
3. Forneça a URL do site do SharePoint que você deseja usar para depuração.
    
  
4. Selecione **hospedado no SharePoint** como a opção de hospedagem para seu suplemento.
    
  

### Para modificar a página Default. aspx para usar o proxy da web usando o modelo de objeto do JavaScript


1. Clique duas vezes a página **Default. aspx** na pasta **páginas**.
    
  
2. Copie a seguinte marcação e colá-lo na marca **PlaceHolderMain** conteúdo da página. A marcação executa as seguintes tarefas:
    
  - Fornece um espaço reservado para os dados remotos.
    
  
  - Referências os arquivos de JavaScript do SharePoint.
    
  
  - Prepara a solicitação com um objeto **WebRequestInfo**.
    
  
  - Prepara o cabeçalho de solicitação **Accept** para especificar a resposta no formato JavaScript Object Notation (JSON).
    
  
  - Problemas de uma chamada para o ponto de extremidade remoto.
    
  
  - Processa a conclusão bem-sucedida, processamento de dados remotos na página da Web do SharePoint.
    
  
  - Trata os erros, processamento de mensagem de erro na página da Web do SharePoint.
    
  

  ```
  
Categories from the Northwind database exposed as an OData service:
    
<!-- Placeholder for the remote content -->
<span id="categories"></span>

<!-- Add references to the JavaScript libraries. -->
<script 
    type="text/javascript" 
    src="../_layouts/15/SP.Runtime.js">
</script>
<script 
    type="text/javascript" 
    src="../_layouts/15/SP.js">
</script>
<script type="text/javascript">
(function () {
    "use strict";

    // Prepare the request to an OData source
    // using the GET verb.
    var context = SP.ClientContext.get_current();
    var request = new SP.WebRequestInfo();
    request.set_url(
        "http://services.odata.org/Northwind/Northwind.svc/Categories"
        );
    request.set_method("GET");

    // We need the response formatted as JSON.
    request.set_headers({ "Accept": "application/json;odata=verbose" });
    var response = SP.WebProxy.invoke(context, request);

    // Let users know that there is some
    // processing going on.
    document.getElementById("categories").innerHTML =
                "<P>Loading categories...</P>";

    // Set the event handlers and invoke the request.
    context.executeQueryAsync(successHandler, errorHandler);

    // Event handler for the success event.
    // Get the totalResults node in the response.
    // Render the value in the placeholder.
    function successHandler() {

        // Check for status code == 200
        // Some other status codes, such as 302 redirect
        // do not trigger the errorHandler. 
        if (response.get_statusCode() == 200) {
            var categories;
            var output;

            // Load the OData source from the response.
            categories = JSON.parse(response.get_body());

            // Extract the CategoryName and Description
            // from each result in the response.
            // Build the output as a list.
            output = "<UL>";
            for (var i = 0; i < categories.d.results.length; i++) {
                var categoryName;
                var description;
                categoryName = categories.d.results[i].CategoryName;
                description = categories.d.results[i].Description;
                output += "<LI>" + categoryName + ":&amp;nbsp;" +
                    description + "</LI>";
            }
            output += "</UL>";

            document.getElementById("categories").innerHTML = output;
        }
        else {
            var errordesc;

            errordesc = "<P>Status code: " +
                response.get_statusCode() + "<br/>";
            errordesc += response.get_body();
            document.getElementById("categories").innerHTML = errordesc;
        }
    }

    // Event handler for the error event.
    // Render the response body in the placeholder.
    // The body includes the error message.
    function errorHandler() {
        document.getElementById("categories").innerHTML =
            response.get_body();
    }
})();
</script>
  ```


### (Opcional) Para modificar a página Default. aspx para usar o proxy da web usando o ponto de extremidade do REST


1. Clique duas vezes a página **Default. aspx** na pasta **páginas**.
    
  
2. Copie a seguinte marcação e colá-lo na marca **PlaceHolderMain** conteúdo da página. A marcação executa as seguintes tarefas:
    
  - Fornece um espaço reservado para os dados remotos.
    
  
  - Faz referência a biblioteca do jQuery.
    
  
  - Prepara a solicitação para o ponto de extremidade de **SP.WebRequest.Invoke**.
    
  
  - Prepara o corpo da solicitação com um objeto **SP.WebrequestInfo**. O objeto inclui um cabeçalho **Accept** para especificar a resposta no formato JavaScript Object Notation (JSON).
    
  
  - Problemas de uma chamada para o ponto de extremidade remoto.
    
  
  - Processa a conclusão bem-sucedida, processamento de dados remotos na página da Web do SharePoint.
    
  
  - Trata os erros, processamento de mensagem de erro na página da Web do SharePoint.
    
  

  ```
  
Categories from the Northwind database exposed as an OData service:
    
<!-- Placeholder for the remote content -->
<span id="categories"></span>

<script 
    type="text/javascript" 
    src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.min.js">
</script>

<script type="text/javascript">
(function () {
    "use strict";

    // The Northwind categories endpoint.
    var url =
        "http://services.odata.org/Northwind/Northwind.svc/Categories";

    // Let users know that there is some
    // processing going on.
    document.getElementById("categories").innerHTML =
                "<P>Loading categories...</P>";

    // Issue a POST request to the SP.WebProxy.Invoke endpoint.
    // The body has the information to issue a GET request
    // to the Northwind service.
    $.ajax({
        url: "../_api/SP.WebProxy.invoke",
        type: "POST",
        data: JSON.stringify(
            {
                "requestInfo": {
                    "__metadata": { "type": "SP.WebRequestInfo" },
                    "Url": url,
                    "Method": "GET",
                    "Headers": {
                        "results": [{
                            "__metadata": { "type": "SP.KeyValue" },
                            "Key": "Accept",
                            "Value": "application/json;odata=verbose",
                            "ValueType": "Edm.String"
                        }]
                    }
                }
            }),
        headers: {
            "Accept": "application/json;odata=verbose",
            "Content-Type": "application/json;odata=verbose",
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: successHandler,
        error: errorHandler
    });

    // Event handler for the success event.
    // Get the totalResults node in the response.
    // Render the value in the placeholder.
    function successHandler(data) {
        // Check for status code == 200
        // Some other status codes, such as 302 redirect,
        // do not trigger the errorHandler. 
        if (data.d.Invoke.StatusCode == 200) {
            var categories;
            var output;

            // Load the OData source from the response.
            categories = JSON.parse(data.d.Invoke.Body);

            // Extract the CategoryName and Description
            // from each result in the response.
            // Build the output as a list
            output = "<UL>";
            for (var i = 0; i < categories.d.results.length; i++) {
                var categoryName;
                var description;
                categoryName = categories.d.results[i].CategoryName;
                description = categories.d.results[i].Description;
                output += "<LI>" + categoryName + ":&amp;nbsp;" +
                    description + "</LI>";
            }
            output += "</UL>";

            document.getElementById("categories").innerHTML = output;
        }
        else {
            var errordesc;

            errordesc = "<P>Status code: " +
                data.d.Invoke.StatusCode + "<br/>";
            errordesc += response.get_body();
            document.getElementById("categories").innerHTML = errordesc;
        }
    }

    // Event handler for the error event.
    // Render the response body in the placeholder.
    // The 2nd argument includes the error message.
    function errorHandler() {
        document.getElementById("categories").innerHTML =
            arguments[2];
    }
})();
</script>

  ```


### Para editar o arquivo de manifesto suplemento


1. No **Solution Explorer**, abra o menu de atalho para o arquivo **AppManifest.xml** e clique em **Exibir código**.
    
  
2. Copie a seguinte definição **RemoteEndPoints** como um filho do nó **App**.
    
  ```XML
  
<RemoteEndpoints>
    <RemoteEndpoint Url=" http://services.odata.org" />
</RemoteEndpoints>
  ```


    O elemento **RemoteEndpoint** é usado para especificar o domínio remoto. O proxy da web valida que as solicitações emitidas para domínios remotos são declaradas no manifesto do suplemento. Você pode criar até 20 entradas no elemento **RemoteEndpoints**. Somente a parte de autoridade é considerada; `http://domain:port` e `http://domain:port/website` são considerados o mesmo ponto de extremidade. Você pode emitir chamadas para vários pontos de extremidade diferentes dentro do mesmo domínio com apenas uma definição de **RemoteEndpoint**.
    
  

### Para criar e executar a solução


1. Pressione a tecla F5.
    
    > [!OBSERVAçãO]
      > Quando você pressiona F5, Visual Studio aproveita a solução, implanta o suplemento e abre a página de permissões para o suplemento.
2. Escolha o botão **De confiança**.
    
  
3. Clique no ícone de suplemento na página conteúdo do Site.
    
    Figura 3 mostra os dados remotos em página da Web do SharePoint.
    

   **Figura 3. Dados remotos em página da Web do SharePoint**

  

     ![SharePoint page with data from the remote service](images/WebProxy_result.png)
  

  

  

**Tabela 2. A solução de problemas**


|**Problema**|**Solução**|
|:-----|:-----|
|Visual Studio não abra o navegador depois que você pressionar a tecla F5. <br/> |Defina o projeto de Suplemento do SharePoint como o projeto de inicialização. <br/> |
|Não há suporte para a combinação de esquema-port. <br/> |A combinação de esquema-port chamada deve se enquadra aos seguintes critérios: <br/> |**Esquema**|**Porta**|
|:-----|:-----|
|HTTP <br/> |80 <br/> |
|https <br/> |443 <br/> |
|http ou https <br/> |7000-10000 <br/> |
   

> [!IMPORTANTE]
> As portas de saída estão sujeitos a disponibilidade do firewall de host. Em particular, apenas 80 do http e https 443 estão disponíveis no SharePoint Online.
  
    
    

|
|Exceção não tratada **SP é indefinido**. <br/> |Certificar-se de que você pode acessar o SP. RequestExecutor.js o arquivo em uma janela do navegador. <br/> Se você estiver usando seu servidor local como seu ambiente de desenvolvimento, você deve desativar a verificação de loopback do IIS. Execute o seguinte comando em um prompt de comando Windows PowerShell. <br/> ```New-ItemProperty HKLM:\\System\\CurrentControlSet\\Control\\Lsa -Name "DisableLoopbackCheck" -value "1" -PropertyType dword```> [!CUIDADO]> Desabilitando o loopback IIS seleção não é recomendada em um ambiente de produção.          |
|O tamanho da resposta do ponto de extremidade remoto excede o limite configurado. <br/> |Tamanho da resposta de solicitações de proxy da web não deve ser maior do que 200 KB. <br/> |
   

## Próximas etapas
<a name="SP15Queryremoteservice_Next"> </a>

Este artigo demonstrado como ler dados em um serviço remoto de uma página da Web do SharePoint. Como próxima etapa, você pode aprender sobre outros dados que estão disponíveis no Suplementos do SharePoint opções do access. Para saber mais, consulte o seguinte:
  
    
    

-  [Amostra de código: obter dados de um serviço remoto usando o proxy da web](http://code.msdn.microsoft.com/SharePoint-2013-Get-data-705bdcd5)
    
  
-  [Criar uma página personalizada de proxy para a biblioteca entre domínios no SharePoint 2013](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)
    
  
-  [Acessar dados do SharePoint 2013 de suplementos usando a biblioteca de domínio cruzado](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [Como: acessar dados externos com o resto do SharePoint 2013](http://msdn.microsoft.com/library/0663cc8c-a736-434d-9858-6ce12ce7f748%28Office.15%29.aspx)
    
  

## Recursos adicionais
<a name="SP15Queryremoteservice_Addresources"> </a>


-  [Configurar um ambiente de desenvolvimento local para suplementos do SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)
    
  
-  [Trabalhar com dados externos do SharePoint 2013](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Seguro cliente e o acesso a dados modelos de objeto para o SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [Autorização e autenticação do SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md)
    
  
-  [Usar operações de consulta de OData em solicitações REST do SharePoint](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [Três maneiras de pensar sobre opções de design para o SharePoint Add-ins](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Armazenamento de dados no SharePoint Add-ins](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data)
    
  
