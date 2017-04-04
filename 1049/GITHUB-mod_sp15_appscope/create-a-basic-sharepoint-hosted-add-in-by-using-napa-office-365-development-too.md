---
title: Создание простой надстройки для SharePoint с размещением в SharePoint с помощью средств разработки Napa для Office 365
ms.prod: SHAREPOINT
ms.assetid: 3b47c97b-9e09-47b2-a65f-29b0f44e34bf
---


# Создание простой надстройки для SharePoint с размещением в SharePoint с помощью средств разработки Napa для Office 365
Узнайте, как создать базовую Надстройка SharePoint с размещением в SharePoint с помощью Средства разработки Napa для Office 365.
  
    
    
![Кнопка "Запустить"](images/Apps_NAPA_Run_Button.png)
  
    
    
 [Запустите этот пример сейчас!](http://go.microsoft.com/fwlink/?LinkId=313212)
Napa  это средство, с помощью которого можно создавать Надстройки SharePoint с размещением в SharePoint. Средство Napa само по себе реализовано в виде Надстройка SharePoint (с размещением у поставщика), которую можно установить на веб-сайтах SharePoint Online, созданных с помощью шаблона **Сайт разработчика**. На домашней странице сайтов разработчиков SharePoint находится библиотека **Тестируемые надстройки**. Инструкции по созданию Сайта разработчика и установке Napa приводятся далее в этой статье.
  
    
    


> **Примечание**
> Мы не поддерживаем установку средства Napa в локальной системе SharePoint. 
  
    
    


С помощью Napa можно создавать собственные Надстройки SharePoint в браузере, а не в Visual Studio. В случае более сложных сценариев можно в любое время скачать свой проект и открыть его в Visual Studio.
  
    
    

В этой статье можно узнать, как создать простую Надстройка SharePoint с размещением в SharePoint, используя Napa. Созданная надстройка будет содержать элементы управления и код для управления списками и элементами списка.
> **Примечание**
> С помощью Napa можно создавать только Надстройки SharePoint с размещением в SharePoint. Создавать таким образом приложения для SharePoint с размещением у поставщика невозможно. Об отличиях можно узнать на странице  [Надстройки SharePoint](sharepoint-add-ins.md). > Использовать в Napa семантику SharePoint для обновления надстроек, описанную в статье  [Обновление веб-компонентов надстройки в SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md), невозможно. Поэтому если требуется обновить надстройку, созданную в Napa, сначала ее нужно экспортировать в Visual Studio. Инструкции о том, как это сделать, приводятся далее в этой статье. > Можно также создать Надстройка SharePoint с помощью Visual Studio. Дополнительные сведения см. в статье  [Знакомство с созданием надстроек SharePoint с размещением в SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md). 
  
    
    


## Вы также можете завести Сайт разработчиков Office 365
<a name="Prerequisites"> </a>

Если у вас еще нет подписки на SharePoint Online, которую можно использовать для разработки, ознакомьтесь со сведениями о ее получении в этом разделе. Если же она у вас есть, перейдите сразу к разделу  [Установка Napa](#Overview).
  
    
    

> **Примечание**
>  Возможно, у вас уже есть доступ к сайту Сайт разработчиков Office 365.> **Вы подписчик MSDN?** Visual Studio Ultimate и Visual Studio Premium с подпиской MSDN предоставляют льготное право на подписку разработчика приложений для Office 365. [Воспользуйтесь этим преимуществом прямо сегодня.](https://msdn.microsoft.com/subscriptions/manage/default.aspx)> **У вас есть один из указанных ниже планов подписки на Office 365?**> **Если есть, администратор подписки на Office 365 может создать Сайт разработчиков** с помощью [Центра администрирования Office 365](https://portal.microsoftonline.com/admin/default.aspx). Дополнительные сведения см. в статье  [Создание сайта разработчика с использованием актуальной подписки на Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md). 
  
    
    

Два способа получения плана Office 365.
  
    
    

- Начните с  [бесплатной 30-дневной пробной версии](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=6881A1CB-F4EB-4db3-9F18-388898DAF510&amp;DL=DEVELOPERPACK) с лицензией для одного пользователя.
    
  
- Приобретите  [подписку разработчика приложений для Office 365](https://portal.microsoftonline.com/Signup/MainSignUp.aspx?OfferId=C69E7747-2566-4897-8CBA-B998ED3BAB88&amp;DL=DEVELOPERPACK).
    
  

> **Совет**
> При выборе каждой из этих ссылок откроется новое окно или вкладка, чтобы указанные ниже инструкции оставались под рукой. 
  
    
    


**Рис. 1. Доменное имя Сайта разработчика Office 365**

  
    
    

  
    
    
![Страница 2 регистрационной формы для учетной записи Office 365](images/ff384c69-56bf-4ceb-81c3-8b874e2407f0.png)
  
    
    

  
    
    

  
    
    

1. Первая страница (не отображается) регистрационной формы не требует объяснений. Просто введите запрашиваемую информацию о себе и нажмите кнопку **Далее**.
    
  
2. На второй странице (рис. 1) укажите ИД администратора подписки.
    
  
3. Создайте поддомен **.onmicrosoft.com**.
    
    После регистрации необходимо использовать полученные учетные данные (в формате  _ИД_пользователя_@ _ваш_домен_.onmicrosoft.com) для входа на сайт портала Office 365, на котором администрируется ваша учетная запись. Ваш Сайт разработчика SharePoint Online подготавливается к работе на новом домене: **http:// _ваш_домен_.sharepoint.com**.
    
  
4. Нажмите кнопку **Далее** и заполните последнюю страницу формы. Если вы хотите указать номер телефона, чтобы получить код подтверждения, можно ввести номер мобильного или стационарного телефона, но *не*  номер VoIP.
    
  

    
> **Примечание**
> Если при попытке зарегистрировать учетную запись разработчика вы входите в другую учетную запись Майкрософт, может отобразиться такое сообщение: "Введенный ИД пользователя неверный. Возможно, он недействителен. Убедитесь, что вы вводите свой ИД пользователя, назначенный вам организацией. Он должен выглядеть так:  *proverka@example.com*  или *proverka@example.onmicrosoft.com*  ".> Если появляется такое сообщение, выйдите из учетной записи Майкрософт, которую использовали, и повторите попытку. Если сообщение продолжает отображаться, очистите кэш браузера или выберите режим **просмотр InPrivate**, затем заполните форму. 
  
    
    

По завершении регистрации в браузере откроется страница установки Office 365. Щелкните значок администратора, чтобы открыть страницу Центра администрирования.
  
    
    

**Рис. 2. Страница Центра администрирования Office 365**

  
    
    

  
    
    
![Снимок экрана с изображением центра администрирования Office 365.](images/SP15_Office365AdminInset_border.png)
  
    
    

  
    
    

1. Необходимо подождать, пока завершится подготовка к работе Сайт разработчиков. После этого обновите страницу Центра администрирования в браузере.
    
  
2. Затем щелкните ссылку **Создание надстроек** в верхнем левом углу страницы, чтобы открыть Сайт разработчиков. Должен открыться сайт, который выглядит так, как показано на рис. 3. На странице размещен список **Тестируемые надстройки**. Это подтверждает, что веб-сайт был создан с помощью шаблона Сайта разработчика SharePoint. Если вместо него вы видите обычный сайт группы, подождите несколько минут и перезапустите сайт.
    
  
3. Обратите внимание на URL-адрес сайта. Он используется при создании проектов Надстройки SharePoint в Visual Studio.
    
  

**Рис. 3. Домашняя страница Сайта разработчика со списком "Тестируемые надстройки"**

  
    
    

  
    
    
![Снимок экрана с изображением домашней страницы сайта разработчика.](images/SP15_DeveloperSiteHome_border.png)
  
    
    

  
    
    

  
    
    

## Установка Napa
<a name="Overview"> </a>

Если ваша подписка на не была изначально создана как Сайт разработчиков Office 365, тогда необходимо создать Сайт разработчика в пользовательском интерфейсе администратора подписки на и установить на нем средства Napa. Инструкции по созданию сайта приводятся в статье  [Создание сайта разработчика с использованием актуальной подписки на Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md).
  
    
    
Чтобы установить средства Napa, откройте Сайт разработчика и последовательно выберите элементы **Контент сайта** > **Добавить надстройку** > **Магазин SharePoint**. В магазине найдите средства Napa, а затем установите их. (Если у вас есть Сайт разработчиков Office 365, возможно, средства Napa уже были установлены при создании сайта. В этом случае они будут отображаться на странице **Контент сайта**.)
  
    
    

## Создание проекта Надстройка SharePoint
<a name="Create"> </a>


1. Откройте надстройку Napa на странице Office 365.
    
  
2. Выберите плитку **Добавить новый проект**, а затем  плитку **Надстройка для SharePoint**.
    
  
3. Назовите проект Тестовая надстройка SharePoint и нажмите кнопку **Создать**.
    
    Откроется редактор кода с веб-страницей по умолчанию, содержащей пример кода, который можно запустить без внесения изменений.
    
  

## Добавление элементов управления на начальную страницу
<a name="AddControls1"> </a>

В Надстройка SharePoint добавьте на начальную страницу по умолчанию элементы управления для создания и удаления универсальных списков SharePoint, а также получения текущего числа списка на сайте Надстройка SharePoint. Код этих элементов будет добавлен позже.
  
    
    

### Добавление элементов управления на начальную страницу


1. В левой части страницы в папке **Страницы** выберите страницу **Default.aspx**, если она еще не выбрана.
    
    Страница Default.aspx отображается в редакторе кода.
    
  
2. В разделе  `PlaceHolderMain` добавьте следующий код под имеющимся HTML-кодом:
    
 ```HTML
  
<br />
<div>
    <button id="getListCount">Get count of lists in web</button>
</div>
<br />
<div id="starter">
    <input type="text" value="List name here" id="createlistbox"/><button id="createlistbutton">Create List</button>
    <p>
    Lists
    <br />
    <select id="selectlistbox" ></select><button id="deletelistbutton">Delete Selected List</button>
    </p>
</div>
 ```


    HTML-код создает следующие элементы управления:
    
  - кнопку, выводящую число списков на сайте Надстройка SharePoint;
    
  
  - кнопки для создания и удаления универсальных списков SharePoint;
    
  
  - список списков, доступных в надстройке.
    
  

## Добавление кода для создания и удаления списков
<a name="AddCode1"> </a>

В этой процедуре добавляется код JavaScript, позволяющий создавать и удалять списки в Надстройка SharePoint.
  
    
    

### Добавление кода для создания и удаления списков


1. Выберите папку **Scripts** и щелкните ссылку **App.js**.
    
    В редакторе будет открыт файл кода JavaScript из шаблона проекта. Он содержит код, который использует Надстройка SharePoint. Вы можете добавить другой JS-файл и добавить код в него, а не в существующий файл, но в этом примере код добавляется в готовый файл **App.js**.
    
    На следующем этапе определяются функции для элементов управления, созданных в предыдущей процедуре.
    

|**Имя функции**|**Описание**|
|:-----|:-----|
| `getWebProperties()` <br/> |Подключена к элементу управления **getListCount**  извлекает число списков на сайте. <br/> |
| `createlist()` <br/> |Подключена к элементу управления **createListButton**  создает универсальный список SharePoint. <br/> |
| `deletelist()` <br/> |Подключена к элементу управления **deletelistbutton**  удаляет список, выбранный пользователем в списке доступных. <br/> |
   

    Функции  `welcome()` и `displayLists()` описываются позже.
    
  
2. В файле **App.js** к двум переменным по умолчанию добавьте переменные `web`,  `lists` и `listItemcollection` и измените код в функции `$(document).ready()` в соответствии со следующим примером.
    
    > **Примечание**
      > В коде появятся подчеркивания, указывающие на ошибки. Позже они пропадут. 

 ```
  
'use strict';

var context = SP.ClientContext.get_current();
var user = context.get_web().get_currentUser();
var web = context.get_web();
var lists = web.get_lists();
var listItemCollection;  // This variable is used later when you add list items.

(function () {

// This code runs when the DOM is ready and creates a context object which is 
// needed to use the SharePoint object model.
$(document).ready(function () {
    getUserName();
    $("#getListCount").click(function (event) {
        getWebProperties();
        event.preventDefault();
    });

    $("#createlistbutton").click(function (event) {
        createlist();
        event.preventDefault();
    });

    $("#deletelistbutton").click(function (event) {
        deletelist();
        event.preventDefault();
    });
        displayLists();
    });

 ```


    На следующем этапе добавляются функции JavaScript для определений. Каждая функция в коде выполняется путем вызова метода  `executeQueryAsync()`, который выполняет текущий ожидающий запрос асинхронным образом на сервере, используя клиентскую объектную модель CSOM для SharePoint. Если функция выполняется асинхронно, ваш сценарий продолжает работу, не дожидаясь ответа сервера. Каждый вызов  `executeQueryAsync()` включает два обработчика событий. Один отвечает, если функция успешно завершилась, а второй  если в ней возник сбой. В этой таблице описаны основные функции.
    

|**Имя функции**|**Описание**|
|:-----|:-----|
| `welcome()` <br/> |Получает ссылку на текущий контекст сайта, а затем использует ее для задания сведений о текущем пользователе в контексте.  <br/> |
| `getWebProperties()` <br/> |Получает коллекцию списков на текущем сайте и возвращает их число.  <br/> |
| `displaylists()` <br/> |Получает текущую коллекцию списков на сайте. В случае успеха функция добавляет их имена в коллекцию в списке доступных списков.  <br/> |
| `createlist()` <br/> |Создает универсальный список SharePoint (типа шаблона списка **genericList**) и дает ему имя, указанное пользователем в элементе управления **createlistbox**. Можно создать списки других типов. Дополнительные сведения о типах списков см. в статье  [Перечисление SPListTemplateType](http://go.microsoft.com/fwlink/?LinkId=256687).  <br/> |
| `deletelist()` <br/> |Удаляет список, выбранный пользователем в списке доступных.  <br/> |
   
3. Добавьте следующий код в конец функции  `onGetUserNameFail()` в файле **App.js**.
    
 ```
  
function getWebProperties() {
        // Get the number of lists in the current web.
        context.load(lists);
        context.executeQueryAsync(onWebPropsSuccess, onWebPropsFail);
    }

    function onWebPropsSuccess(sender, args) {
        alert('Number of lists in web: ' + lists.get_count());
    }

    function onWebPropsFail(sender, args) {
        alert('Failed to get list. Error: ' + args.get_message());
    }

    function displayLists() {
        // Get the available SharePoint lists, and then set them into 
        // the context.
        lists = web.get_lists();
        context.load(lists);
        context.executeQueryAsync(onGetListsSuccess, onGetListsFail);
    }

    function onGetListsSuccess(sender, args) {
        // Success getting the lists. Set references to the list 
        // elements and the list of available lists.
        var listEnumerator = lists.getEnumerator();
        var selectListBox = document.getElementById("selectlistbox");
        if (selectListBox.hasChildNodes()) {
            while (selectListBox.childNodes.length >= 1) {
                selectListBox.removeChild(selectListBox.firstChild);
            }
        }
        // Traverse the elements of the collection, and load the name of    
        // each list into the dropdown list box.
        while (listEnumerator.moveNext()) {
            var selectOption = document.createElement("option");
            selectOption.value = listEnumerator.get_current().get_title();
            selectOption.innerHTML = listEnumerator.get_current().get_title();
            selectListBox.appendChild(selectOption);
        }
    }

    function onGetListsFail(sender, args) {
        // Lists couldn't be loaded - display error.
        alert('Failed to get list. Error: ' + args.get_message());
    }

function createlist() {
        // Create a generic SharePoint list with the name that the user specifies.
        var listCreationInfo = new SP.ListCreationInformation();
        var listTitle = document.getElementById("createlistbox").value;
        listCreationInfo.set_title(listTitle);
        listCreationInfo.set_templateType(SP.ListTemplateType.genericList);
        lists = web.get_lists();
        var newList = lists.add(listCreationInfo);
        context.load(newList);
        context.executeQueryAsync(onListCreationSuccess, onListCreationFail);
    }

    function onListCreationSuccess() {
        displayLists();
    }

    function onListCreationFail(sender, args) {
        alert('Failed to create the list. ' + args.get_message());
    }

    function deletelist() {
        // Delete the list that the user specifies.
        var selectListBox = document.getElementById("selectlistbox");
        var selectedListTitle = selectListBox.value;
        var selectedList = web.get_lists().getByTitle(selectedListTitle);
        selectedList.deleteObject();
        context.executeQueryAsync(onDeleteListSuccess, onDeleteListFail);
    }

    function onDeleteListSuccess() {
        displayLists();
    }

    function onDeleteListFail(sender, args) {
        alert('Failed to delete the list. ' + args.get_message());
    }
 ```


## Запуск
<a name="Run1"> </a>

Первая часть интерфейса и кода готова, поэтому запустите надстройку, чтобы проверить ее работу.
  
    
    

### Порядок запуска надстройки


1. Внизу страницы нажмите кнопку запуска (
  
    
    
![Кнопка "Запустить"](images/Apps_NAPA_Run_Button.png)
  
    
    
).
    
    Надстройка упаковывается, разворачивается и устанавливается на вашем сайте разработчика Office 365.
    
    После установки запустится Надстройка SharePoint. Если надстройка не запустится автоматически, например из-за блокировки всплывающих окон, щелкните ссылку надстройки, чтобы запустить ее.
    
  
2. Нажмите ссылку **Щелкните здесь, чтобы запустить надстройку в новом окне**.
    
    Появится экран Надстройка SharePoint.
    
  
3. Нажмите кнопку **Get count of lists in web** (Подсчитать списки на сайте).
    
    В диалоговом окне будет указано, что сайт текущей Надстройка SharePoint содержит два списка. По умолчанию сайт содержит списки "Библиотека макетов" и "Коллекция главных страниц".
    
  
4. В поле **List name here** (Имя списка) введитеTest List и нажмите кнопку **Create List** (Создать список).
    
  
5. Откройте список **Lists** (Списки), чтобы убедиться, что в нем есть новый список.
    
  
6. Снова нажмите кнопку **Get count of lists in web**.
    
    Теперь на сайте три списка, включая только что созданный.
    
  
7. В списке **Lists** выберите **Test List**, а затем нажмите кнопку **Delete Selected List** (Удалить выбранный список).
    
    **Test List** пропадет из списка доступных списков.
    
  
8. Закончив, закройте окно браузера, а затем нажмите кнопку **Закрыть** в окне **Запуск надстройки**, чтобы вернуться к редактируемому проекту.
    
  

## Добавление кода и элементов управления для добавления и удаления элементов списков
<a name="AddControls2"> </a>

Теперь пользователи могут создавать и удалять списки, поэтому вы можете выполнить следующие действия, чтобы они могли добавлять и удалять элементы списков.
  
    
    

### Добавление кода и элементов управления для добавления и удаления элементов списков


1. Откройте файл Default.aspx для редактирования.
    
  
2. В элементе  `selectlistbox` добавьте следующий код.
    
 ```XML
  
<p>
    Items
    <br />
    <input type="text" value="item name here" id="createitembox"/><button id="createitembutton">Create Item</button>
    </p>
    <p>
    <select id="selectitembox"></select> <button id="deleteitembutton">Delete Selected Item</button>
    </p>
 ```


    Он добавляет поле ввода, где можно ввести имя элемента, кнопку для добавления элемента в список и кнопку для удаления элемента.
    
  
3. Откройте файл **App.js** для редактирования.
    
  
4. В функции  `$(document).ready()` добавьте определения функций, вызываемых при нажатии кнопок **Создать элемент** и **Удалить выбранный элемент**. Кроме того, добавьте обработчик событий jQuery для списка **Списки**, чтобы гарантировать, что элементы списка будут обновляться при выборе нового списка.
    
 ```
  
$("#createitembutton").click(function (event) {
            createitem();
            event.preventDefault();
        });

        $("#deleteitembutton").click(function (event) {
            deleteitem();
            event.preventDefault();
        });
    
        // Update the list items dropdown when a new list
        // is selected in the Lists dropdown.
        $("#selectlistbox").change(function (event) {
            getitems();
            event.preventDefault();
        });
 ```


    > **Примечание**
      > Если при запуске надстройки элементы списка не отображаются, проверьте, указан ли оператор  `displayLists();` после предыдущего кода.

    На следующем этапе добавляются функции JavaScript для новых определений и вспомогательная функция  `getItems()`. В этой таблице описано, что делают основные функции.
    

|**Имя функции**|**Описание**|
|:-----|:-----|
| `createItem()` <br/> |Добавляет элемент в выбранный список и дает ему имя из поля **Items** (Элементы). <br/> |
| `deleteItem()` <br/> |Удаляет элемент, выбранный пользователем в списке.  <br/> |
| `getItems()` <br/> |Извлекает коллекцию элементов (и их дочерних элементов) из выбранного списка.  <br/> |
   
5. Добавьте следующий код в конец файла **App.js** после функции `onDeleteListFail()`.
    
 ```
  
function createitem() {
    // Retrieve the list that the user chose, and add an item to it.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedListTitle = selectListBox.value;
    var selectedList = web.get_lists().getByTitle(selectedListTitle);

    var listItemCreationInfo = new SP.ListItemCreationInformation();
    var newItem = selectedList.addItem(listItemCreationInfo);
    var listItemTitle = document.getElementById("createitembox").value;
    newItem.set_item('Title', listItemTitle);
    newItem.update();
    context.load(newItem);
    context.executeQueryAsync(onItemCreationSuccess, onItemCreationFail);
}

function onItemCreationSuccess() {
    // Refresh the list of items.
    getitems();
}

function onItemCreationFail(sender, args) {
    // The item couldn't be created - display an error message.
    alert('Failed to create the item. ' + args.get_message());
}

function deleteitem() {
    // Delete the item that the user chose.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedListTitle = selectListBox.value;
    var selectedList = web.get_lists().getByTitle(selectedListTitle);
    var selectItemBox = document.getElementById("selectitembox");
    var selectedItemID = selectItemBox.value;
    var selectedItem = selectedList.getItemById(selectedItemID);
    selectedItem.deleteObject();
    selectedList.update();
    context.load(selectedList);
    context.executeQueryAsync(onDeleteItemSuccess, onDeleteItemFail);
}

function onDeleteItemSuccess() {
    // Refresh the list of items.
    getitems();
}

function onDeleteItemFail(sender, args) {
    // The item couldn't be deleted - display an error message.
    alert('Failed to delete the item. ' + args.get_message());
}

function getitems() {
    // Using a CAML query, get the items in the list that the user chose, and 
    // set the context to the collection of list items.
    var selectListBox = document.getElementById("selectlistbox");
    var selectedList = selectListBox.value;
    var selectedListTitle = web.get_lists().getByTitle(selectedList);  
    var camlQuery = new SP.CamlQuery();
    camlQuery.set_viewXml("<View><ViewFields>" +
        "<FieldRef Name='ID' />" +
        "<FieldRef Name='Title' />" +
        "</ViewFields></View>')");
    listItemCollection = selectedListTitle.getItems(camlQuery);
    context.load(listItemCollection, "Include(Title, ID)");
    context.executeQueryAsync(onGetItemsSuccess, onGetItemsFail);
}

function onGetItemsSuccess(sender, args) {
    // The list items were retrieved.
    // Show all child nodes.
    var listItemEnumerator = listItemCollection.getEnumerator();
    var selectItemBox = document.getElementById("selectitembox");
    if (selectItemBox.hasChildNodes()) {
        while (selectItemBox.childNodes.length >= 1) {
     selectItemBox.removeChild(selectItemBox.firstChild);
        }
    }
        while (listItemEnumerator.moveNext()) {
            var selectOption = document.createElement("option");
            selectOption.value = listItemEnumerator.get_current().get_item('ID');
            selectOption.innerHTML = listItemEnumerator.get_current().get_item('Title');
            selectItemBox.appendChild(selectOption);
        }
}

function onGetItemsFail(sender, args) {
    // The list items couldn't be retrieved - display an error message.
    alert('Failed to get items. Error: ' + args.get_message());
}
 ```


## Запустите измененную Надстройка SharePoint
<a name="Run2"> </a>

Весь интерфейс и код готов, поэтому запустите надстройку, чтобы проверить ее работу.
  
    
    

### Как запустить измененную Надстройка SharePoint


1. Внизу страницы снова нажмите кнопку запуска ( **Запустить**).
    
  
2. В поле **List name here** (Имя списка) введитеNew Test List и нажмите кнопку **Create List** (Создать список).
    
    Новый список добавится в список **Lists** (Списки).
    
  
3. В списке **Lists** (Списки) выберите **New Test List** (Создать список тестов).
    
  
4. В поле **Item name here** (Имя элемента) введитеItem 1 и нажмите кнопку **Create Item** (Создать элемент).
    
    Новый элемент списка появится в списке **Items** (Элементы).
    
  
5. Повторите предыдущие действия, чтобы добавить элементы Item 2 иItem 3.
    
  
6. В списке элементов выберите **Item 2**, а затем нажмите кнопку **Delete Selected Item** (Удалить выбранный элемент).
    
    **Item 2** пропадет из списка элементов.
    
  
7. Закончив, закройте окно браузера.
    
  

## Экспортируйте проект в Visual Studio
<a name="NextSteps"> </a>

Откройте свой проект в Visual Studio, нажав кнопку **Открыть в Visual Studio**, как показано на рисунке 3. Napa автоматически устанавливает требуемые средства и открывает проект в Visual Studio.
  
    
    

**Рис. 3. Кнопка открытия в Visual Studio**

  
    
    

  
    
    
![Кнопка "Открыть в Visual Studio"](images/Apps_Napa_OpenInVS.png)
  
    
    

  
    
    

  
    
    

## Дополнительные ресурсы
<a name="Additional"> </a>


-  [Обзор разработки решений с помощью SharePoint 2013](http://msdn.microsoft.com/library/f86e2695-4d7a-4fc5-bc23-689de96c4b06%28Office.15%29.aspx)
    
  
-  [Надстройки SharePoint](sharepoint-add-ins.md)
    
  

