---
title: Использование клиентского элемента управления "Выбор людей" в надстройках для SharePoint с размещением в SharePoint
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# Использование клиентского элемента управления "Выбор людей" в надстройках для SharePoint с размещением в SharePoint
Описание использования клиентского элемента управления "Выбор людей" в приложениях для SharePoint, размещенных в SharePoint.
> **Важно!**
> В этом разделе предполагается, что вы умеете создавать надстройку для SharePoint с размещением в SharePoint. Чтобы узнать, как это сделать, изучите раздел  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md). 





## Описание клиентского элемента управления "Выбор людей" в SharePoint 2013
<a name="bk_whatIs"> </a>

Клиентский элемент управления "Выбор людей" предоставляет пользователям возможность быстрого поиска и выбора допустимых учетных записей пользователей для людей, групп и утверждений в организации. Элемент управления выбора является элементом управления HTML и JavaScript, предоставляющим поддержку в разных браузерах. Элемент управления выбора несложно добавить в надстройку: добавьте в разметку элемент-контейнер для этого элемента управления, ссылки на него и его зависимости. Затем в скрипте вызовите глобальную функцию **SPClientPeoplePicker_InitStandaloneControlWrapper** для отображения и инициализации этого элемента управления выбора.



Элемент управления выбора представлен объектом **SPClientPeoplePicker**, предоставляющим методы, которые другие клиентские элементы управления могут использовать для получения сведений от этого элемента управления выбора или для выполнения других действий. С помощью свойств **SPClientPeoplePicker** можно настроить соответствующие параметры этого элемента управления выбора, такие как разрешение нескольких пользователей или задание параметров кэширования. Данный элемент управления выбора также использует параметры конфигурации веб-приложений, в частности параметры Доменные службы Active Directory, или параметры лесов назначения. Параметры конфигурации веб-приложений собираются автоматически (из свойства **SPWebApplication.PeoplePickerSettings**).



Элемент управления выбора имеет следующие компоненты.




- Текстовое поле для ввода имен пользователей, групп и утверждений.


- Элемент управления "Диапазон", показывающий имена разрешенных пользователей, групп и утверждений.


- Скрытый элемент **div**, который автоматически заполняет раскрывающийся список соответствующими результатами запроса.


- Элемент управления "Автозаполнение".



> **Примечание**
> Элемент управления выбора и его функциональные возможности задаются в файлах скриптов **clientforms.js**, **clientpeoplepicker.js** и **autofill.js**, которые располагаются в папке %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\TEMPLATE\\LAYOUTS на сервере. 





## Необходимые условия для настройки среды разработки для использования клиентского элемента управления "Выбор людей" в приложении для SharePoint 2013
<a name="bk_prereqs"> </a>

В этой статье предполагается, что вы создаете надстройку для SharePoint с помощью Napa на сайте разработчика Office 365. Если вы используете эту среду разработки, то необходимые условия выполняются.




> **Примечание**
> В статье  [Настройка среды для разработки надстроек SharePoint в Office 365](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md) можно найти сведения о том, как зарегистрироваться на сайте разработчика и начать использовать Napa.




Если вы не используете Napa на сайте разработчика, то необходимо следующее:




- SharePoint 2013 хотя бы с одним конечным пользователем;


- Visual Studio 2012 или Visual Studio 2013


- Инструменты разработчика Office для Visual Studio 2013



> **Примечание**
> Руководство по настройке среды разработки, соответствующей вашим потребностям, можно найти в статье  [Начало создания приложений для Office и SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 




В следующих действиях описываются общие действия для добавления элемента управления выбора в надстройку и последующего получения ее сведений о пользователях в другом клиентском элементе управления. Соответствующий код можно найти в разделе  [Пример кода. Использование клиентского элемента управления "Выбор людей"](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example).



Клиентский элемент управления "Выбор людей" можно использовать в приложениях для SharePoint с размещением в SharePoint, но невозможно использовать в надстройках с размещением у поставщика. Пример реализации элемента управления "Выбор людей" в надстройке с размещением у поставщика можно скачать на веб-сайте  [Примеры модели надстроек Office](http://officeams.codeplex.com).




## Использование клиентского элемента управления "Выбор людей" в надстройке с размещением в SharePoint
<a name="bk_steps"> </a>


### В разметке страницы


1. Добавьте ссылки на зависимости скрипта для клиентского элемента управления "Выбор людей".


2. Добавьте теги HTML для пользовательского интерфейса страницы. В надстройке из данного примера задается два элемента **div**: один для отображения элемента управления выбора и второй для пользовательского интерфейса кнопка, вызывающая скрипт для запроса этого элемента управления выбора и элементов, отображающих данные о пользователе.



### В скрипте


1. Создайте словарь JSON для использования в качестве схемы, в которой хранятся свойства этого элемента управления выбора, такие как **AllowMultipleValues** и **MaximumEntitySuggestions**.


2. Вызовите глобальную функцию **SPClientPeoplePicker_InitStandaloneControlWrapper**.


3. Получите объект элемента управления выбора со страницы.


4. Запросите этот элемент управления выбора. В надстройке из данного примера метод **GetAllUserInfo** вызывается для получения всех сведений для разрешенных пользователей, а метод **GetAllUserKeys** для получения ключей для этих разрешенных пользователей.


5. Получите ИД пользователя с помощью объектной модели JavaScript. ИД пользователя не включается в данные, которое возвращается элементом выбора, поэтому надстройка вызывает метод **SP.Web.ensureUser** и получает ИД от возвращенного объекта **SP.User**.


Отображение, инициализация и другая функциональность для элемента управления выбора обрабатывается на сервере, включая поиск и разрешение ввода пользователя при работе с поставщиками проверки подлинности SharePoint.




## Пример кода. Использование клиентского элемента управления "Выбор людей" в надстройке с размещением в SharePoint
<a name="bk_example"> </a>

В следующих примерах HTML и кода JavaScript выполняется добавление клиентского элемента управления "Выбор людей" в надстройку с размещением в SharePoint.



В первом примере показана разметка страницы для тегов **PlaceHolderMain** **asp:Content** на странице Default.aspx. Этот код ссылается на зависимости в скрипте элемента управления выбора, передает уникальный идентификатор в элемент DOM, в котором будет отображаться элемент управления выбора, и задает пользовательский интерфейс надстройки.





```HTML

<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
    <SharePoint:ScriptLink name="clienttemplates.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientforms.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientpeoplepicker.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="autofill.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.runtime.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.core.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <div id="peoplePickerDiv"></div>
    <div>
        <br/>
        <input type="button" value="Get User Info" onclick="getUserInfo()"></input>
        <br/>
        <h1>User info:</h1>
        <p id="resolvedUsers"></p>
        <h1>User keys:</h1>
        <p id="userKeys"></p> 
        <h1>User ID:</h1>
        <p id="userId"></p>
    </div>
</asp:Content>```


> **Примечание**
> В зависимости от используемой среды, возможно, не придется явно ссылаться на все эти зависимости. 




В следующем примере показан скрипт для файла App.js. Этот скрипт инициализирует и отображает элемент управления выбора, а затем запрашивает у него пользовательские сведения и получает ИД пользователя с помощью объектной модели JavaScript.





```

// Run your custom code when the DOM is ready.
$(document).ready(function () {

    // Specify the unique ID of the DOM element where the
    // picker will render.
    initializePeoplePicker('peoplePickerDiv');
});

// Render and initialize the client-side People Picker.
function initializePeoplePicker(peoplePickerElementId) {

    // Create a schema to store picker properties, and set the properties.
    var schema = {};
    schema['PrincipalAccountType'] = 'User,DL,SecGroup,SPGroup';
    schema['SearchPrincipalSource'] = 15;
    schema['ResolvePrincipalSource'] = 15;
    schema['AllowMultipleValues'] = true;
    schema['MaximumEntitySuggestions'] = 50;
    schema['Width'] = '280px';

    // Render and initialize the picker. 
    // Pass the ID of the DOM element that contains the picker, an array of initial
    // PickerEntity objects to set the picker value, and a schema that defines
    // picker properties.
    this.SPClientPeoplePicker_InitStandaloneControlWrapper(peoplePickerElementId, null, schema);
}

// Query the picker for user information.
function getUserInfo() {

    // Get the people picker object from the page.
    var peoplePicker = this.SPClientPeoplePicker.SPClientPeoplePickerDict.peoplePickerDiv_TopSpan;

    // Get information about all users.
    var users = peoplePicker.GetAllUserInfo();
    var userInfo = '';
    for (var i = 0; i < users.length; i++) {
        var user = users[i];
        for (var userProperty in user) { 
            userInfo += userProperty + ':  ' + user[userProperty] + '<br>';
        }
    }
    $('#resolvedUsers').html(userInfo);

    // Get user keys.
    var keys = peoplePicker.GetAllUserKeys();
    $('#userKeys').html(keys);

    // Get the first user's ID by using the login name.
    getUserId(users[0].Key);
}

// Get the user ID.
function getUserId(loginName) {
    var context = new SP.ClientContext.get_current();
    this.user = context.get_web().ensureUser(loginName);
    context.load(this.user);
    context.executeQueryAsync(
         Function.createDelegate(null, ensureUserSuccess), 
         Function.createDelegate(null, onFail)
    );
}

function ensureUserSuccess() {
    $('#userId').html(this.user.get_id());
}

function onFail(sender, args) {
    alert('Query failed. Error: ' + args.get_message());
}```


## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Создание компонентов UX в SharePoint 2013](create-ux-components-in-sharepoint-2013.md)


-  [Обзор средства выбора людей и поставщиков утверждений (SharePoint 2013)](http://technet.microsoft.com/library/gg602078.aspx)


-  [Настройка средства выбора людей в SharePoint 2013](http://technet.microsoft.com/library/gg602075.aspx)



