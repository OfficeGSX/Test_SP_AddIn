---
title: Использование API JavaScript для SharePoint для работы с данными SharePoint
ms.prod: SHAREPOINT
ms.assetid: 5c8767c2-317f-4bdb-8f4f-885d06da7feb
---


# Использование API JavaScript для SharePoint для работы с данными SharePoint
В данной статье рассказывается, как использовать объектную модель JavaScript для SharePoint, чтобы работать с данными SharePoint из JavaScript на страницах на сайте надстройки.
Это десятая часть из серии статей о том, как разрабатывать Надстройки SharePoint, размещаемые в SharePoint. Сначала вам необходимо ознакомиться со статьей  [Надстройки SharePoint](sharepoint-add-ins.md) и с указанными ниже предыдущими статьями серии.





-  [Знакомство с созданием надстроек SharePoint с размещением в SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Развертывание и установка надстроек для SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Добавление настраиваемых столбцов в надстройку с размещением в SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Добавление настраиваемого типа контента в надстройку для SharePoint с размещением в SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)


-  [Добавление веб-части на страницу в надстройку для SharePoint с размещением в SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Добавление рабочего процесса в надстройку для SharePoint с размещением в SharePoint](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Добавление настраиваемой страницы и стиля для надстройки с размещением в SharePoint](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Добавление настраиваемой функции отрисовки в клиенте в надстройку SharePoint, размещаемую в SharePoint](add-custom-client-side-rendering-to-a-sharepoint-hosted-sharepoint-add-in.md)


-  [Создание настраиваемой кнопки ленты на хост-сайте надстройки SharePoint](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md)



> **Примечание**
> Если вы изучали предыдущие статьи этой серии о надстройках, размещаемых в SharePoint, то у вас уже есть решение для Visual Studio, которое можно использовать для работы с данной статьей. Кроме того, вы можете скачать репозиторий  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) и открыть файл BeforeJSOM.sln.




Несмотря на то что в надстройках SharePoint, размещаемых в SharePoint, не должно быть серверного кода, вы можете использовать бизнес-логику и взаимодействие времени выполнения с компонентами SharePoint в таких надстройках, применяя JavaScript и клиентскую объектную модель JavaScript для SharePoint. Мы будем называть ее JSOM. Обратите внимание на букву M в конце. Не следует путать это название с JSO **N** (нотация объектов JavaScript). В данной статье вы будете использовать объектную модель JavaScript для поиска и удаления старых элементов из списка **New Employees in Seattle** (Новые сотрудники в Сиэтле).
## Создание JavaScript и кнопки для его вызова


1. Убедитесь, что указанный этап, который описан в первом руководстве этой серии, выполнен. 

    Откройте файл **/Pages/Default.aspx** из корневой папки проекта. Помимо прочего, этот созданный файл загружает один или оба сценария, размещенные в SharePoint: sp.runtime.js и sp.js. Разметку для загрузки этих файлов можно найти в элементе управления **Content** в верхней части файла с идентификатором **PlaceHolderAdditionalPageHead**. Разметка зависит от используемой версии **Инструменты разработчика Microsoft Office для Visual Studio**. Согласно руководствам этой серии необходимо загрузить оба файла с обычными HTML-тегами **<script>**, а не с тегами **<SharePoint:ScriptLink>**. Убедитесь, что указанные ниже строки присутствуют в элементе управления **PlaceHolderAdditionalPageHead** *над*  строкой `<meta name="WebPartPageExpansion" content="full" />`.



 ```

<script type="text/javascript" src="/_layouts/15/sp.runtime.js"></script>
<script type="text/javascript" src="/_layouts/15/sp.js"></script> 

 ```


    Проверьте файл на наличие другой разметки, загружающей один или оба файла сценария и удалите ее. Сохраните и закройте файл.


2. На узле **Сценарии** в **обозревателе решений** уже может быть файл Add-in.js. Если его нет, но есть файл App.js, щелкните файл App.js правой кнопкой мыши и переименуйте на Add-in.js. Если нет ни файла Add-in.js, ни файла App.js, создайте необходимый файл, выполнив указанные ниже действия.

1. Щелкните правой кнопкой мыши узел **Сценарии** и последовательно выберите пункты **Добавить**, **Создать элемент** и **Интернет**.


2. Выберите **файл JavaScript** и назовите егоAdd-in.js.


3. Откройте файл Add-in.js и удалите его содержимое (если оно есть).


4. Добавьте указанные ниже строки в файл. Обратите внимание на указанные ниже особенности этого кода.

  - Если вы непреднамеренно сделаете что-нибудь не так в JavaScript, то благодаря строке  `'use strict';` среда выполнения JavaScript в браузере создаст исключение.


  - В переменной  `clientContext` хранится объект **SP.ClientContext**, который ссылается на веб-сайт SharePoint. Весь код JSOM начинается с создания или получения ссылки на объект этого типа.


  - В переменной  `employeeList` хранится ссылка на экземпляр списка **New Employees in Seattle** (Новые сотрудники в Сиэтле).


  - В переменной  `completedItems` хранятся элементы из списка, которые будет удалять сценарий: элементы, у которых поле **OrientationStage** (Этап вводного обучения) имеет значение **Completed** (Завершен).



 ```

'use strict';

var clientContext = SP.ClientContext.get_current(); 
var employeeList = clientContext.get_web().get_lists().getByTitle('New Employees In Seattle'); 
var completedItems; 
 ```

5. Чтобы свести к минимуму обмен сообщениями между браузером клиента и сервером SharePoint, JSOM использует систему пакетной обработки. Только одна функция ( **SP.ClientContext.executeQueryAsync**) реально отправляет сообщения на сервер (и принимает ответы). Вызовы к API JSOM, поступающие между вызовами **executeQueryAsync**, объединяются в пакеты, которые затем отправляются на сервер при следующем вызове **executeQueryAsync**. В общем случае невозможно вызвать метод объекта JSOM, если только объект не был доставлен на клиент во время предыдущего вызова **executeQueryAsync**. Предполагается, что ваш сценарий будет вызывать метод **SP.ListItem.deleteObject** каждого завершенного элемента в списке, поэтому ему необходимо выполнить два вызова **executeQueryAsync**: один  для получения коллекции завершенных элементов списка, а второй  для объединения вызовов **deleteObject** в пакет и отправки их на сервер для выполнения.

    Таким образом, начнем с создания метода для получения элементов списка с сервера. Добавьте указанный ниже код в файл.



 ```

function purgeCompletedItems() {

   var camlQuery = new SP.CamlQuery(); 
   camlQuery.set_viewXml( 
         '<View><Query><Where><Eq>' + 
           '<FieldRef Name=\\'OrientationStage\\'/><Value Type=\\'Choice\\'>Completed</Value>' + 
         '</Eq></Where></Query></View>'); 
     completedItems = employeeList.getItems(camlQuery); 
}
 ```

6. Когда эти строки будут отправлены на сервер и выполнены там, они создадут коллекцию элементов списка, но сценарий должен доставить эту коллекцию на клиент. Для этого необходимо вызвать функцию **SP.ClientContext.load**, поэтому добавьте указанную ниже строку в конец метода.

 ```

clientContext.load(completedItems);
 ```

7. Добавьте вызов **executeQueryAsync**. У этого метода два параметра и оба они представляют собой функции обратного вызова. Первый метод запускается, если сервер успешно выполняет все команды в пакете. Второй  если по какой-либо причине на серверу не удается выполнить необходимые операции. Вы создадите эти две функции на одном из следующих этапов. Добавьте указанную ниже строку в конец метода.

 ```
  clientContext.executeQueryAsync(deleteCompletedItems, onGetCompletedItemsFail);
 ```

8. Теперь добавьте указанную ниже строку в конец метода. Возвращая значение **false** в кнопку ASP.NET, которая будет вызывать эту функцию, мы отменим поведение кнопок ASP.NET, используемое по умолчанию, то есть перезагрузку страницы. Перезагрузка страницы приведет перезагрузке файла Add-in.js. Это, в свою очередь, повлечет за собой инициализацию объекта `clientContext`. Если такая перезагрузка будет выполнена в период между моментом, когда **executeQueryAsync** отправляет свой запрос, и моментом, когда SharePoint отправляет обратно отклик, то больше не будет исходного объекта `clientContext` для обработки отклика. Функция остановит свою работу, при этом не будет выполнено никакого обратного вызова: ни об успешном выполнении, ни о сбое. (Конкретное поведение может отличаться в зависимости от используемого браузера.)

 ```
  return false;
 ```

9. Добавьте указанную ниже функцию ( `deleteCompletedItems`) в файл. Эта функция запускается при успешном выполнении функции  `purgeCompletedItems`. Обратите внимание на указанные ниже особенности этого кода.

  - Метод **SP.ListItem.get_id** возвращает идентификатор элемента списка. Каждый элемент в массиве представляет собой объект **SP.ListItem**.


  - Метод **SP.List.getItemById** возвращает объект **SP.ListItem** с указанным идентификатором.


  - Метод **SP.ListItem.deleteObject** помечает элемент списка, который необходимо удалить на сервере при вызове **executeQueryAsync**.


  - Прежде чем удалять элементы списка, необходимо скопировать их из коллекции, отправленной с сервера в массив. Если сценарий вызывал метод **deleteObject** непосредственно для каждого элемента в цикле **while**, JavaScript создаст сообщение об ошибке, в котором будет сказано, что длина коллекции изменяется во время перечисления. Это сообщение об ошибке не совсем верное, так как на самом деле элемент не будет удален, пока вызовы **deleteObject** не будут объединены в пакет и отправлены на сервер, но JSOM разработана так, чтобы имитировать создание исключений, который должны произойти на сервере (где код не должен изменять размер коллекции во время ее перечисления). Массивы имеют фиксированный размер, поэтому вызов **deleteObject** для элемента в массиве приведет к удалению элемента из списка, но при этом размер массива не будет изменен.



 ```
  function deleteCompletedItems() {

    var itemArray = new Array();
    var listItemEnumerator = completedItems.getEnumerator();

    while (listItemEnumerator.moveNext()) {
        var item = listItemEnumerator.get_current();
        itemArray.push(item);
    }

    var i;
    for (i = 0; i < itemArray.length; i++) {
        employeeList.getItemById(itemArray[i].get_id()).deleteObject();
    }

    clientContext.executeQueryAsync(onDeleteCompletedItemsSuccess, onDeleteCompletedItemsFail);
}
 ```

10. Добавьте указанную ниже функцию ( `onDeleteCompletedItemsSuccess`) в файл. Эта функция запускается при успешном удалении завершенных элементов (или если в списке нет завершенных элементов). Во второй строке ( `location.reload(true);`) выполняется повторная загрузка страницы с сервера. Это необходимо, так как веб-часть представления списка на странице будет по-прежнему отображать завершенные элементы, пока страница не будет обновлена. (Файл Add-in.js тоже будет повторно загружен, но это не приводит к проблеме, так как в результате этой операции не будет прерываться работа текущей функции JavaScript.)

 ```

function onDeleteCompletedItemsSuccess() {
    alert('Completed orientations have been deleted.');
    location.reload(true);
}
 ```

11. Добавьте две указанные ниже функции обратного вызова в случае сбоя в файл.

 ```

// Failure callbacks

function onGetCompletedItemsFail(sender, args) {
    alert('Unable to get completed items. Error:' + args.get_message() + '\\n' + args.get_stackTrace());
}

function onDeleteCompletedItemsFail(sender, args) {
    alert('Unable to delete completed items. Error:' + args.get_message() + '\\n' + args.get_stackTrace());
}
 ```

12. Откройте файл default.aspx и найдите элемент **asp:Content** с идентификатором **PlaceHolderMain**.


13. Добавьте указанную ниже разметку между элементом **WebPartPages:WebPartZone** и первым из двух элементов **asp:Hyperlink**. Обратите внимание, что обработчик **OnClientClick** имеет значение `return purgeCompletedItems()`, а не просто  `purgeCompletedItems()`. Возвращаемое из функции значение  `false` сообщает ASP.NET, что не нужно перезагружать страницу.

 ```HTML

<p><asp:Button runat="server" OnClientClick="return purgeCompletedItems()"
  ID="purgecompleteditemsbutton" Text="Purge Completed Items" /></p>
 ```

14. Перестройте проект в Visual Studio.


15. Чтобы при тестировании надстройки свести к минимуму ручную работу по заданию для параметра **Orientation Stage** (Этап вводного обучения) элементов списка значенияCompleted (Завершен), откройте файл elements.xml для экземпляра списка **NewEmployeesInSeattle** (но не файл elements.xml для шаблона списка **NewEmployeeOrientation**) и добавьте разметку  `<Field Name="OrientationStage">Completed</Field>` в качестве последнего дочернего элемента в один или несколько элементов **Row**.

    Ниже показано, как должен выглядеть элемент **Rows**.



 ```

<Rows>
  <Row>
    <Field Name="Title">Tom Higginbotham</Field>
    <Field Name="Division">Manufacturing</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Satomi Hayakawa</Field>
    <Field Name="OrientationStage">Completed</Field>
  </Row>
  <Row>
    <Field Name="Title">Cassi Hicks</Field>
  </Row>
  <Row>
    <Field Name="Title">Lertchai Treetawatchaiwong</Field>
  </Row>
</Rows>
 ```


## Запуск и тестирование надстройки






1. Включите всплывающие окна в браузере, который Visual Studio использует при отладке.


2. Нажмите клавишу F5, чтобы развернуть и запустить надстройку. Visual Studio выполнит временную установку надстройки на тестовом сайте SharePoint и сразу же запустит ее.


3. Откроется начальная страница надстройки. В списке будет один или несколько элементов, у которых параметр **Orientation Stage** (Этап вводного обучения) имеет значение **Completed** (Завершен).

   **Список перед очисткой завершенных элементов**



![Список новых сотрудников в Сиэтле, где в столбце "Этап адаптации" два элемента имеют значение "Завершено". Под списком расположена кнопка "Удалить завершенные элементы".](images/e5e4eef8-a218-4797-aabc-c52adbd2d96d.PNG)





4. После полной загрузки начальной страницы надстройки нажмите кнопку **Purge Completed Items** (Удалить завершенные элементы). При успешном выполнении операции (т. е. если не отобразится никакого сообщения об ошибке) будут удалены все элементы со статусом **Complete** (Завершен) и откроется всплывающее окно сообщения с текстом **Completed orientations have been deleted** (Сведения о завершенном вводном обучении удалены).


5. Закройте всплывающее окно. После этого страница будет перезагружена, а в веб-части представления списка больше не будет элементов с состоянием **Completed** (Завершен).

   **Список после очистки завершенных элементов**



![Список новых сотрудников в Сиэтле, в котором на два элемента меньше, чем ранее, при этом ни один из них не отмечен как завершенный в столбце "Этап адаптации".](images/a0330fad-1473-4fde-9df2-8be0b37df1a1.PNG)





6. Чтобы завершить сеанс отладки, закройте окно браузера или остановите отладку в Visual Studio. При каждом нажатии клавиши F5 Visual Studio будет отзывать предыдущую версию надстройки и устанавливать ее последнюю версию.


7. Вы будете работать с этой надстройкой и решением Visual Studio и при изучении других статей, поэтому при перерывах в работе рекомендуется отзывать надстройку. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Отозвать**.



## 
<a name="Nextsteps"> </a>

В следующей статье этой серии вы добавите JavaScript на страницу на сайте надстройки, который работает с данными SharePoint на хост-сайте:  [Работа с данными хост-сайта из JavaScript на сайте надстройки](work-with-host-web-data-from-javascript-in-the-add-in-web.md).




