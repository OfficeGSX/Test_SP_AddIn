---
title: Depurar e solucionar problemas de um receptor de evento remoto em um SharePoint Add-in
ms.prod: SHAREPOINT
ms.assetid: 21de092e-27f2-4000-bbb7-cb5cbfeaf195
---


# Depurar e solucionar problemas de um receptor de evento remoto em um SharePoint Add-in
Configurar o ambiente de desenvolvimento para depurar eventos remotos no usando o Visual Studio.
 * **Aplica-se a:*** 
  
    
    


## Configurar a depuração para um site de teste do SharePoint remoto


> [!OBSERVAçãO]
> Os procedimentos nesta seção se aplicam somente quando o teste de site do SharePoint está em um computador diferente do Visual Studio ou se estiver usando um SharePoint Online Site do desenvolvedor como seu site de teste. Se o SharePoint e Visual Studio estiverem no mesmo computador, ignore esta seção.
  
    
    

Quando um projeto de Suplemento do SharePoint no Visual Studio inclui um receptor de evento remoto (RER) ou um receptor de evento suplemento, você deve fazer alguma configuração rápida adicional nas propriedades do projeto antes de você pode depurar o suplemento com (F5). Essa configuração, por sua vez, requer que você faça algumas configurações do Azure. Você não precisará repetir a configuração do Windows Azure para cada projeto que tiver um RER ou um suplemento do evento. (Se o suplemento inclui um manipulador de eventos AppInstalled, o suplemento não mesmo executados com F5 ou Ctrl-F5 [run sem depuração], a menos que você realizar a configuração nesta seção.)
  
    
    

### Para configurar o Windows Azure


1. Se você ainda não tiver um, obtenha uma assinatura de Microsoft Azure. Um é incluído como um benefício com uma  [Inscrição no MSDN](http://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits/).
    
  
2. Siga as instruções em  [How To: Create or Modify a Service Bus Service Namespace](http://msdn.microsoft.com/library/fa561f70-007c-45aa-b34d-56317dbbfc87.aspx).
    
  

### Para configurar o projeto Add-in do SharePoint no Visual Studio


1. Você deve ter a versão mais recente do Office Developer Tools para Visual Studio 2013, caso  [execute o instalador do WebPI](http://aka.ms/OfficeDevToolsForVS2013).
    
  
2. Depois de adicionar um RER ou um manipulador de eventos do suplemento a um projeto de Suplemento do SharePoint em Visual Studio, com o botão direito do projeto no **Solution Explorer** e selecione **Propriedades**.
    
  
3. No painel Propriedades, abra o guia do **SharePoint** e role até a parte inferior.
    
  
4. Marque a caixa de seleção para **Habilitar a depuração via barramento de serviço do Microsoft Azure**.
    
  
5. Insira a cadeia de caracteres de conexão completa na caixa de texto fornecida. Você obter a cadeia de caracteres com estas etapas.
    
1. Faça logon no portal do Windows Azure e abra a guia de **Barramento de serviço**.
    
  
2. Abra o namespace que você criou para depuração RER e navegue até as cadeias de caracteres de conexão. Portal do Azure UI muda com freqüência. Consulte a  [Ajuda do portal Azure](https://msdn.microsoft.com/en-us/library/azure/dn578292.aspx) se você não puder encontrar as cadeias de caracteres de conexão.
    
  
3. Copie a cadeia de caracteres de conexão **SAS**. Esta é a cadeia de caracteres que você digitar nas propriedades do projeto Visual Studio.
    
  
No futuro, quando você criar projetos Suplemento do SharePoint em Visual Studio, essa informação é já preenchida, portanto você não tem, abra o portal do Azure sempre.
## Testar a configuração
<a name="CreateRER"> </a>

Use os procedimentos nesta seção para verificar se você pode depurar um RER.
  
    
    

### Para criar um projeto do receptor de evento remoto


1. No Visual Studio, crie um provedor hospedado Suplemento do SharePoint.
    
    Consulte  [Introdução à criação de suplementos do SharePoint hospedados pelo provedor](get-started-creating-provider-hosted-sharepoint-add-ins.md).
    
  
2. No **Solution Explorer**, escolha o suplemento do nó do projeto.
    
  
3. Na barra de menus, escolha **Project**, **Adicionar Novo Item**.
    
  
4. No painel de **modelos**, escolha o modelo de **lista** e, em seguida, escolha o botão **Adicionar**.
    
  
5. Escolha o botão **terminar de** adicionar uma lista personalizada de padrão para o projeto de suplemento.
    
  
6. Adicione outro item ao projeto add-in, no painel de **modelos**, escolha o modelo do **Receptor de evento remoto**.
    
  
7. Na caixa **nome**, deixe o nome padrão (RemoteEventReceiver1) e, em seguida, escolha o botão **Adicionar**.
    
  
8. No **que tipo de receptor de evento você deseja?** lista, escolha **Eventos de Item de lista**.
    
    Deixe a origem do evento como **Lista1**, a lista que você adicionou nas etapas anteriores.
    
  
9. Na lista de **lidar com os eventos a seguir**, escolha **um item que está sendo adicionado** e, em seguida, escolha o botão **Concluir**.
    
    Um serviço web é adicionado ao aplicativo web para manipular o evento remoto que você especificou. Um receptor de evento remoto é adicionado a Suplemento do SharePoint. O receptor referencia o serviço da web e o evento de item de lista no arquivo Elements XML do receptor de evento.
    
  
10. No projeto de suplemento, abra AppManifest.xml.
    
  
11. Alterar a página inicial para a página da lista:  _AddInProjectName_/Lists/List1.
    
    Substitua  _AddInProjectName_ com o nome do seu projeto suplemento, comoSharePointAddIn4/listas/Lista1. Neste exemplo, estamos definindo a página inicial para a página da lista. No entanto, em um típico suplemento, você precisava ponto provavelmente seu próprios UI na página da web de projeto.
    
  

### Para executar e testar depuração do manipulador de eventos


1. Se você ainda não tiver feito isso, conclua o procedimento **para configurar o projeto de suplemento do SharePoint no Visual Studio** neste artigo.
    
  
2. No projeto da web, abra o serviço do receptor de evento remoto (RemoteEventReceiver1.svc) e, em seguida, adicione um ponto de interrupção para qualquer linha de código dentro do método  `ProcessEvent()` .
    
  
3. Escolha a tecla **F5** para executar o projeto.
    
  
4. Escolha o botão **+ novo item** para adicionar um item à lista.
    
  
5. Forneça um título para o item e escolha o botão **Salvar**.
    
    O ponto de interrupção que você adicionou para o receptor de evento remoto é atingido, verificando se você estiver depurando o receptor de evento remoto.
    
  
6. Escolha a tecla **F5** para continuar a executar o projeto e, em seguida, pare a depuração quando terminar.
    
  

## Ativar/desativar a notificação do Visual Studio esse evento depuração precisam ser configurados
<a name="RER_TurnOnOffNotificationsinRER"> </a>

Se você tiver um evento remoto em seu projeto e não tiver configurado a depuração de evento remoto, Visual Studio solicita que você configure o evento remoto de depuração (consulte a Figura 1). Você pode alterar esse comportamento, desmarcando a caixa de seleção **Notifique-me se depuração de evento remoto não estiver configurado** na guia **SharePoint**.
  
    
    

**Figura 1. Notificação de evento remoto depuração**

  
    
    

  
    
    
![Notifications in remote event receivers](images/SP15Con_Remote_Event_Receivers_FAQ_fig3.png)
  
    
    

  
    
    

  
    
    

## Verificar se o seu serviço está hospedado no barramento de serviço
<a name="RER_HowDoIKnowWheteherMyServiceisHostedintheServiceBus"> </a>

Depois de você pressionar F5 e confia o add-in, vá para o namespace de barramento de serviço no seu navegador; Por exemplo, http://mynamespace.servicebus.windows.net e você verá sua listado como um número de ponto de extremidade. Figura 2 mostra como a página aparece quando um namespace é  *não*  listados; ou seja, antes de você pressionar F5.
  
    
    

**Figura 2. Navegando até o namespace de barramento de serviço**

  
    
    

  
    
    
![Browsing to the service bus namespace](images/SP15Con_Remote_Event_Receivers_FAQ_fig4.PNG)
  
    
    

  
    
    

  
    
    

## RER não atingiu o ponto de interrupção
<a name="RER_DoesNotHitTheBreakPoint"> </a>

Dependendo do evento, o evento remoto pode ser síncrono ou assíncrono. Ela pode levar alguns segundos ou mais para atingiu seu ponto de interrupção se ele for assíncrono.
  
    
    

## Erro: "não houve nenhum ponto de extremidade escutando"
<a name="RER_DoesNotHitTheBreakPoint"> </a>

Quando seu manipulador é executado em produção, você obtém o seguinte erro:
  
    
    
"O texto explicativo receptor de evento remoto falhou. Detalhes: não houve nenhum ponto de extremidade está escutando em https:// _{domain}_: _nnnnn_/ /AppEventReceiver.svc _{path}_pode aceitar a mensagem. Isso é geralmente causado por um endereço incorreto ou ação SOAP."onde  _nnnnn_ é uma porta.
  
    
    
SharePoint não requer o nenhuma porta explícita na URL do manipulador na produção. Isso significa que você deve usar qualquer porta 443 para HTTPS, que é recomendável, ou a porta 80 para HTTP.
  
    
    

## Erro: "não foi possível estabelecer o relação de confiança para o canal seguro de SSL/TLS com autoridade"
<a name="RER_DoesNotHitTheBreakPoint"> </a>

Quando seu manipulador é executado em produção, você obtém o seguinte erro:
  
    
    
"O texto explicativo receptor de evento remoto falhou. Detalhes: não foi possível estabelecer relação de confiança para o canal seguro de SSL/TLS com autoridade "
  
    
    
Quando o add-in é Microsoft SharePoint Online, mas o serviço do receptor de evento remoto fica no local e está usando HTTPS, conforme é recomendável, o servidor que está hospedando o receptor não pode usar um certificado autoassinado em produção. O servidor deve ter um certificado publicamente aceito em uma autoridade de certificação. Se o suplemento estiver em um farm do SharePoint no local, os certificados autoassinados são aceitáveis.
  
    
    

## Recursos adicionais
<a name="Additional"> </a>


-  [Lidar com eventos no SharePoint Add-ins](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Eventos remotos de depuração do SharePoint 2013 usando o Visual Studio 2012](http://blogs.msdn.com/b/officeapps/archive/2013/03/21/update-to-debugging-sharepoint-2013-remote-events-using-visual-studio-2012.aspx)
    
  

