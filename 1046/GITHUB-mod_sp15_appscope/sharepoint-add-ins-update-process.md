---
title: Processo de atualização de suplementos do SharePoint
ms.prod: SHAREPOINT
ms.assetid: 3dba209d-cb98-4e5d-b4b2-fad31e667ca1
---


# Processo de atualização de suplementos do SharePoint
Saiba mais sobre o processo de atualização de suplementos do SharePoint.
Você precisa atualizar um Add-in do SharePoint, se você adicionar funcionalidade, corrigir um bug ou fazer um título de atualização. Uma atualização para um suplemento é implantada em um pacote de Suplemento do SharePoint da mesma maneira que a primeira versão do add-in é implantada. O processo de atualização do Add-in SharePoint garante que os dados do add-in são preservados se a atualização falhar por qualquer motivo.
  
    
    


> **IMPORTANTE**
> Você não pode alterar o  *tipo de suplemento*  usando o sistema de atualização. Por exemplo, você não pode alterar um suplemento do hospedado no SharePoint para hospedado em provedor com uma atualização. Para fazer uma alteração de tipo, você precisa [migrar de um suplemento antigo para um novo](#Major). Em particular, desde que  [o programa de visualização para suplementos auto-hospedados foi fechado](http://blogs.office.com/2014/05/16/update-on-autohosted-apps-preview-program/), você deve estar ciente de que você não pode atualizar um suplemento auto-hospedado para um suplemento hospedado pelo provedor. Você precisará converter o suplemento, conforme explicado em  [Converter um auto-hospedados Add-in do SharePoint em um suplemento hospedado em provedor](convert-an-autohosted-sharepoint-add-in-to-a-provider-hosted-add-in.md).
  
    
    


## Processo de atualização para um SharePoint Add-in
<a name="Minor"> </a>

Para obter uma atualização, você pode usar a mesma ID de produto no manifesto suplemento que você usou para a versão original. O número da versão no manifesto suplemento deve ser maior que o número de versão do suplemento original ou a atualização mais recente.
  
    
    
Dentro de 24 horas após carregar sua atualização para o catálogo do suplemento de uma organização e dentro de uma semana de carregá-la a Office Store, uma indicação de que está disponível uma atualização aparece ao lado de listagem do add-in na página **Conteúdo do Site** de cada site onde ele está instalado. Os usuários podem clicar em um link para atualizar o suplemento conforme mostrado na Figura 1. Atualizações disponíveis também são expostas no gerenciamento de locatário da interface do usuário.
  
    
    

**Add-in Figura 1 do processo de atualização do SharePoint**

  
    
    

  
    
    
![The UI steps for updating an app](images/UpdatingApp_AppTileUpdateNotice.png)
  
    
    

    
> **DICA**
> Quando você estiver desenvolvendo uma atualização, você não deseja aguardar 24 horas toda vez que você carrega uma nova versão em seu catálogo de suplemento do SharePoint de teste. Para obter informações sobre como atualizar imediatamente um add-in, consulte  [Atualizar um suplemento sem aguardar a 24 horas](update-sharepoint-add-ins.md#ImmediateUpdateNotice) .> Por padrão, o SharePoint verifica cada 24 horas atualizações para suplementos instalados. Um administrador de farm pode definir isso para outro valor usando o seguinte comando SharePoint Management Shell, onde n é o número de horas entre as verificações.>  `Set-SPInternalAppStateUpdateInterval -AppStateSyncHours n`> Se o valor for definido como 0, a seleção é feita sempre que o trabalho de timer internas **Internal suplemento estado atualização** é executado, que por padrão é a cada hora. Administradores de farm podem usar a Administração Central para alterar a frequência do trabalho de timer ou executá-lo imediatamente.
  
    
    

SharePoint 2013 irá fazer o seguinte quando um usuário instala uma atualização para um Suplemento do SharePoint. Esses eventos não necessariamente ocorrem exatamente nesta ordem, e alguns deles podem ocorrer em paralelo. Além disso, se a atualização falhar, há uma reversão concluída.
  
    
    

- SharePoint 2013 solicita ao usuário aprovar as permissões solicitadas pelo suplemento.
    
  
- SharePoint 2013 torna o suplemento indisponíveis para os usuários temporariamente.
    
  
- Se o suplemento contém um pacote de solução do SharePoint (. wsp) e o conteúdo do pacote de solução foi alterado de nenhuma maneira, o SharePoint faz o seguinte:
    
  - Faz o backup da web add-in. (Mas, no SharePoint Online e no local do SharePoint 2016 e posterior, são feitos o backup dos dados reais em listas do SharePoint somente se a atualização está fazendo uma alteração no esquema da lista).
    
  
  - Testa a atualização do backup.
    
  
  - Se o teste for bem-sucedido, atualiza o suplemento web original. Observe que o novo arquivo. wsp no pacote de suplemento é usado para atualizar os recursos e outros elementos no suplemento da web. (As partes de atualização do esquema do recurso foram expandidas no SharePoint 2013.)
    
  
- SharePoint 2013 executa o serviço web de **UpgradedEventEndpoint**, se qualquer um estiver registrado no manifesto do suplemento.
    
    > **OBSERVAçãO**
      > Se o suplemento estiver hospedado em provedor, você fornecer a lógica de atualização para todos os componentes não SharePoint do add-in. Na maioria das vezes, você atualizar esses componentes separadamente da atualização da Suplemento do SharePoint propriamente dito, assim como você instalou esses componentes separadamente da instalação do add-in. Mas, pode haver algumas alterações que devem acontecer somente quando um usuário atualiza o Suplemento do SharePoint. Essa lógica pode passar em um serviço da web **UpgradedEventEndpoint** ou em "primeiro execute após atualização" lógica do add-in em si.
- SharePoint 2013 disponibiliza o suplemento e seus componentes novamente.
    
  

    
> **OBSERVAçãO**
> Se o esquema de qualquer lista no web add-in está sendo alterado, a lista é feita backup juntamente com o restante da web add-in. Isso pode levar algum tempo se houver muitos dados na lista. Se o processo de atualização não é possível concluir em 1 hora, ele pára e a atualização é revertida.
  
    
    


## Migrar de um suplemento antigo para um novo
<a name="Major"> </a>

Em alguns cenários, convém produzir um suplemento totalmente novo para substituir uma antiga, em vez de atualizar original. O novo suplemento pode ter o mesmo nome amigável antigo, mas ele deve receber uma nova ID de produto no manifesto do suplemento e ele será exibido no público Office Store e na página **Adicionar um suplemento** de sites do SharePoint como um item distinto da versão original.
  
    
    

> **OBSERVAçãO**
> Itens no catálogo do suplemento de uma organização são diferenciados pelo  *nome de arquivo*  do pacote suplemento, e não a ID do produto ou o nome do add-in. Se o novo suplemento tem o mesmo nome de arquivo do pacote que o antigo, ele substituirá o antigo no catálogo add-in e o suplemento antigo não terá mais aparecem na página **Adicionar um suplemento**. Se você habilitar o controle de versão no pacote do suplemento quando você o carrega no catálogo, a versão antiga do arquivo (que é o aplicativo antigo) ainda está disponível no histórico do item. Você pode baixar o pacote de suplemento antigo ou revertê-la, mas não é possível ter ambos os antigos e novos suplementos como itens separados no catálogo ou na página **Adicionar um suplemento**, a menos que tenham nomes de arquivo distintos.
  
    
    

Em alguns casos, talvez você precise migrar dados. Por exemplo, o novo suplemento pode usar um Banco de Dados SQL do Microsoft Azure que tem um esquema diferente do que o suplemento antigo. Ou o novo suplemento pode usar um mecanismo de armazenamento de dados diferentes; Por exemplo, um banco de dados externo, em vez de listas do SharePoint. Você deve fornecer o código para a migração de dados.
  
    
    
Se seus dados antigos forem para um local que possa ser acessado por um manipulador de evento remoto, você pode implementar a lógica de migração em um **InstalledEventEndpoint** web service do suplemento novo como alternativa, se o novo suplemento tem acesso aos dados antigos, você pode colocar a lógica de migração em código que executa na primeira vez que inicia um usuário o novo suplemento. Se os dados antigos não podem ser acessados por manipuladores de remota ou o novo suplemento, você pode criar uma atualização do suplemento antigo que adiciona um recurso de exportação de dados e uma interface do usuário para o recurso. Os usuários primeiro atualize o suplemento antigo e usá-lo para exportar os dados para um local onde o novo suplemento pode acessá-lo. Você incluir o recurso e a interface do usuário para importar dados em que o novo suplemento.
  
    
    
Em princípio, você pode reutilizar uma fonte de dados externa, compute componente ou outro componente externo no novo suplemento que foi usado no considere suplemento antiga, no entanto, que quando um Suplemento do SharePoint é desinstalado, a infraestrutura de SharePoint 2013 desinstalará tudo o que ele instalado. Portanto, geralmente é uma boa prática para um Suplemento do SharePoint depender apenas os componentes que ele instalado ou componentes externos que não foram instalados pela infraestrutura de SharePoint 2013.
  
    
    

> **OBSERVAçãO**
> É recomendável que se você implementar um **InstalledEventEndpoint** ou um **UpgradedEventEndpoint** que instala componentes, você também deve implementar um **UninstallingEventEndpoint** que desinstala esses mesmos componentes. Fazer isso está em conformidade com os princípios de design que suplementos devem ser autônomos e desinstalar corretamente. No entanto, os dados que ainda seria útil aos usuários após o suplemento é desinstalado não devem ser excluídos. Sites criados por um suplemento, que não seja a web suplemento, geralmente devem ser considerados dados.
  
    
    

Se a antigos e novos suplementos cada contiverem um suplemento de web, considere a possibilidade de que um nova suplemento da web é criada quando o seu novo suplemento é instalado. Por esse motivo, você não deve usar a marcação XML relacionados à atualização do esquema de recurso SharePoint 2013. Essa marcação não funciona porque você não está atualizando os componentes do SharePoint existentes; você estiver substituindo um suplemento antigo por um novo.
  
    
    

## Recursos adicionais
<a name="SP15appupgrade_addlresources"> </a>


-  [Suplementos de atualização para o SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Atualizar componentes de suplemento web no SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Atualizar os componentes da web de host no SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Criar um manipulador para o evento de atualização no SharePoint Add-ins](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  
-  [Atualizar componentes remotos no SharePoint Add-ins](update-remote-components-in-sharepoint-add-ins.md)
    
  
-  [Publicar os suplementos do SharePoint](publish-sharepoint-add-ins.md)
    
  
-  [Aspectos importantes do Add-in SharePoint arquitetura e desenvolvimento cenário](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [Implantando e instalando os suplementos do SharePoint: métodos e opções](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  

