---
title: Включение настраиваемой кнопки в надстройку, размещаемую у поставщика
ms.prod: SHAREPOINT
ms.assetid: 58932389-0100-47ee-9d33-1b4321d3f462
---


# Включение настраиваемой кнопки в надстройку, размещаемую у поставщика
В данной статье рассказывается, как включить настраиваемую кнопку ленты в Надстройка SharePoint, размещаемую у поставщика.
Это третья часть из серии статей о том, как разрабатывать Надстройки SharePoint, размещаемые у поставщика. Сначала вам необходимо ознакомиться со статьей  [Надстройки SharePoint](sharepoint-add-ins.md) и с указанными ниже предыдущими статьями серии.





-  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Придание надстройке, размещаемой у поставщика, внешнего вида и удобства использования SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)



> **Примечание**
> Если вы изучали предыдущие статьи этой серии о надстройках, размещаемых в SharePoint, то у вас уже есть решение для Visual Studio, которое можно использовать для работы с данной статьей. Кроме того, вы можете скачать репозиторий  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) и открыть файл BeforeRibbonButton.sln.




Надстройка SharePoint может включать дополнительные действия, представляющие собой условие SharePoint для настраиваемых элементов меню или кнопок ленты. В данной статье рассказывается, как создать настраиваемую кнопку, которая синхронизирует список SharePoint с удаленной базой данных.
## Создание настраиваемого списка на хост-сайте

Предполагается, что настраиваемая кнопка будет размещена на ленте определенного списка, в котором записаны сведения о сотрудниках местного магазина. В одной из следующих статей этой серии вы узнаете, как программным способом добавить настраиваемый список на хост-сайт, а при изучении данной статьи вы добавите такой список вручную. 




1. На начальной странице магазина Fabrikam в Гонконге выберите пункты **Site Contents | add an add-in | Custom List** (Содержание сайта | добавить надстройку | Настраиваемый список).


2. В диалоговом окне **Adding Custom List** (Добавление настраиваемого списка) в укажите имяLocal Employees (Местные сотрудники) и нажмите кнопку **Create** (Создать).


3. На странице **Site Contents** (Содержание сайта) откройте список **Local Employees** (Местные сотрудники).


4. Откройте вкладку **List** (Список) на ленте, а затем нажмите кнопку **List Settings** (Параметры списка).


5. В разделе **Columns** (Столбцы) на странице **List Settings** (Параметры списка) щелкните столбец **Title** (Название).


6. В форме **Edit Column** (Изменение столбца) измените значение в поле **Column name** (Имя столбца) с Title (Название) наName (Имя), а затем нажмите кнопку **OK**.


7. На странице **Settings** (Параметры) щелкните **Create column** (Создать столбец).


8. В форме **Create Column** (Создание столбца) выполните указанные ниже действия.

1. В поле **Column name** (Имя столбца) введитеAdded to Corporate DB (Добавлен в корпоративную базу данных).


2. Выберите тип **Yes/No (check box)** (Да/Нет [флажок]).


3. В поле **Default value** (Значение, используемое по умолчанию) укажите значение **No** (Нет).


4. Нажмите кнопку **OK**. После этого вы вернетесь обратно на страницу **Settings** (Параметры).


9. Щелкните **Site Contents** (Содержание сайта), чтобы открыть страницу **Site Contents** (Содержание сайта). На ней будет плитка для нового списка. Откройте ее.


10. Щелкните **new item** (Создать элемент) и в форме создания элемента введите имя, но *не*  устанавливайте флажок **Added to Corporate DB** (Добавлен в корпоративную базу данных). Затем нажмите кнопку **Save** (Сохранить). Список должен выглядеть примерно следующим образом:

![Список местных сотрудников с одним элементом. Имя Григорий Иванов. В столбце "Добавлен в базу данных предприятия" значение "Нет".](images/a3371859-e42f-49ea-8f17-48d8a248b075.PNG)






## Добавление пользовательской кнопки

В этом разделе вы включите в настройку разметку, которая будет развертывать кнопку на ленте списка. Когда пользователь выделяет сотрудника в списке и нажимает кнопку, имя сотрудника будет добавлено в корпоративную базу данных, а в поле **Added to Corporate DB** (Добавлен в корпоративную базу данных) для этого сотрудника значение No (Нет) будет заменено на Yes (Да).




1.  *Если открыт Visual Studio, вам придется закрыть его*  и повторно открыть решение Chain Store, чтобы Visual Studio обнаружил новый список. (Запустите Visual Studio от имени администратора.)

    > **Примечание**
      >  При повторном открытии решения параметры раздела "Начальные проекты" в Visual Studio обычно сбрасываются к значениям, используемым по умолчанию. Сразу же после повторного открытия примера решения в этой серии статей всегда выполняйте указанные ниже действия.>  В верхней части **обозревателя решений** щелкните правой кнопкой мыши узел решения и выберите **Назначить запускаемые проекты**. >  Убедитесь, что в столбце **Действие** для всех трех проектов указано значение **Запускать**. 
2. В **обозревателе решений** щелкните правой кнопкой мыши проект **ChainStore** и выберите **Добавить | Создать элемент**. 


3. В диалоговом окне **Добавление нового элемента** выберите поле **Дополнительное действие ленты**, укажите в нем имя AddEmployeeToCorpDB, а затем нажмите **Добавить**.


4. В открывшемся диалоговом окне будут три вопроса. Дайте на них указанные ниже ответы.


|**Вопрос**|**Ответ**|
|:-----|:-----|
|**Где требуется предоставить настраиваемое действие?** <br/> |Хост-сайт <br/> |
|**К какой области относится настраиваемое действие?** <br/> |Экземпляр списка <br/> |
|**Каким конкретным элементом ограничена область настраиваемого действия?** <br/> |Local Employees <br/> |
 
5. Нажмите кнопку **Далее**. Отобразятся три указанных ниже вопроса.


|**Вопрос**|**Ответ**|
|:-----|:-----|
|**Где расположен этот элемент управления?** <br/> |Ribbon.ListItem.Actions <br/> |
|**Какой текст должен отображаться на подписи этой кнопки?** <br/> |Add to Corporate DB (Добавить в корпоративную базу данных) <br/> |
|**Куда ведет элемент управления "Кнопка"?** <br/> |ChainStoreWeb\\Pages\\EmployeeAdder.aspx (Это страница, код программной части которой будет добавлять сотрудника в базу данных.) <br/> |
 
6. Нажмите кнопку **Готово**.

    Файл elements.xml, который определяет дополнительное действие, будет добавлен в проект и открыт. Вы можете обращаться с этим файлом как с "черным ящиком". Вам не потребуется вносить в него изменения до знакомства со следующими частями этой серии. А сейчас обратите внимание на указанный ниже момент.

  - Атрибут **Location** элемента **CommandUIDefinition** имеет значение `Ribbon.ListItem.Actions.Controls_children`. Его вторая часть ( `ListItem`) идентифицирует вкладку на ленте, на которую будет помещена кнопка (но это может и не быть точным отображаемым именем вкладки), а третья часть ( `Actions`) это имя раздела ленты, в который будет помещена кнопка.


  - Атрибут **CommandAction** элемента **CommandUIHandler** начинается с заполнителя `~remoteAppUrl`. При развертывании кнопки он будет заменен URL-адресом удаленного веб-приложения.


  - В значение **CommandAction** с помощью значений-заполнителей в фигурных скобках добавлены несколько параметров запроса. Эти заполнители разрешаются во время выполнения. Обратите внимание, что один из них идентификатор элемента списка, выбранного пользователем перед нажатием настраиваемой кнопки на ленте.


7. В проекте **ChainStoreWeb** откройте файл **Pages/EmployeeAdder.aspx**. Обратите внимание, что в нем нет пользовательского интерфейса. Предполагается, что надстройка будет использовать эту страницу в качестве веб-службы. Это возможно, так как класс ASP.NET **System.Web.UI.Page** реализует **System.Web.IHttpHandler** и при запросе к странице автоматически запускается событие ** Page_Load**.


8. Откройте файл с кодом программной части **Pages/EmployeeAdder.aspx.cs**. В нем уже есть метод, который добавляет сотрудника в удаленную базу данных ( `AddLocalEmployeeToCorpDB`). Он использует объект **SharePointContext** для получения URL-адреса хост-сайта, который надстройка использует в качестве своего дискриминатора клиента. Таким образом, первое, что должен сделать метод **Page_Load**, инициализировать этот объект. Объект создается и помещается в кэш в сеансе, когда загружается начальная страница надстройки, поэтому добавьте указанный ниже код в метод **Page_Load**. (Объект **SharePointContext** определен в файле SharePointContext.cs, который Инструменты разработчика Office для Visual Studio создает при создании решения надстройки.)

 ```cs

spContext = Session["SPContext"] as SharePointContext;
 ```

9. Метод  `AddLocalEmployeeToCorpDB` использует имя сотрудника в качестве параметра, поэтому добавьте указанную ниже строку в метод **Page_Load**. Вы создадите метод  `GetLocalEmployeeName` на одном из следующих этапов.

 ```cs
  // Read from SharePoint
string employeeName = GetLocalEmployeeName();
 ```

10. После этой строки добавьте вызов метода  `AddLocalEmployeeToCorpDB`.

 ```cs

// Write to remote database
AddLocalEmployeeToCorpDB(employeeName);
 ```

11. Добавьте в файл оператор **using** для пространства имен `Microsoft.SharePoint.Client`. (При создании проекта **ChainStoreWeb**Инструменты разработчика Office для Visual Studio включил в него сборку Microsoft.SharePoint.Client.)


12. Теперь добавьте указанный ниже метод в класс  `EmployeeAdder`. Клиентская объектная модель (CSOM) .NET для SharePoint подробно задокументирована на MSDN и мы рекомендуем вам изучить ее, когда вы закончите работу с этой серией статей. Для целей данной статьи обратите внимание, что класс **ListItem** представляет элемент в списке SharePoint и что на значение поля в элементе можно сослаться с помощью синтаксиса "индексатора". Кроме того, учтите, что код ссылается на поле, используя имя Title (Название) даже если вы изменили его на Name (Имя). Это происходит из-за того, что в коде необходимо ссылаться на поля, используя их *внутренние*  , а не отображаемые имена. Внутреннее имя поля указывается при создании поля и его не удастся изменить. Вы выполните `TODO1` на одном из следующих этапов.

 ```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    // TODO1: Initialize the localEmployee object by getting
    // the item from SharePoint.
 
    return localEmployee["Title"].ToString();
}
 ```

13. Чтобы наш код смог получить элемент списка из SharePoint, ему потребуется идентификатор этого элемента. Добавьте указанное ниже объявление в класс  `EmployeeAdder` сразу же после объявления для объекта `spContext`.

 ```cs

private int listItemID;
 ```

14. Теперь добавьте указанный ниже метод в класс  `EmployeeAdder`, чтобы получить идентификатор элемента списка из параметра запроса.

 ```cs
  private int GetListItemIDFromQueryParameter()
{
    int result;
    Int32.TryParse(Request.QueryString["SPListItemId"], out result);
    return result;
}
 ```

15. Чтобы инициализировать переменную  `listItemID`, добавьте указанную ниже строку в метод **Page_Load** сразу же за строкой, в которой выполняется инициализация переменной `spContext`.

 ```cs

listItemID = GetListItemIDFromQueryParameter();
 ```

16. В  `GetLocalEmployeeName` замените `TODO1` указанным ниже кодом. На данный момент относитесь к этому коду как к "черному ящику", потому что сейчас нас интересует то, как заставить работать настраиваемую кнопку. Мы более подробно рассмотрим этот код в следующей статье серии, которая будет целиком посвящена клиентской объектной модели SharePoint.

 ```cs
  using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
    localEmployee = localEmployeesList.GetItemById(listItemID);
    clientContext.Load(localEmployee);
    clientContext.ExecuteQuery();
}

 ```


    Теперь весь метод должен выглядеть, как указано ниже.



 ```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}
 ```

17. Страница EmployeeAdder не должна отрисовываться, поэтому добавьте указанный ниже код в метод **Page_Load** в качестве последней строки. Она будет перенаправлять браузер обратно на страницу представления списка для списка **Local Employees** (Местные сотрудники).

 ```cs

// Go back to the Local Employees page
Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);

 ```


    Теперь весь метод **Page_Load** должен выглядеть, как указано ниже.



 ```cs

protected void Page_Load(object sender, EventArgs e)
{
    spContext = Session["SPContext"] as SharePointContext;
    listItemID = GetListItemIDFromQueryParameter();

    // Read from SharePoint
    string employeeName = GetLocalEmployeeName();

    // Write to remote database
    AddLocalEmployeeToCorpDB(employeeName);

    // Go back to the preceding page
    Response.Redirect(spContext.SPHostUrl.ToString() + "Lists/LocalEmployees/AllItems.aspx", true);
}
 ```


## Запрос разрешения на чтение списка хост-сайта

Как вы уже видели, при установке надстройки SharePoint предлагает предоставить ей разрешения на доступ к хост-сайту. Каждый раз, когда вы нажимали клавишу F5, выполнялась переустановка надстройки. До настоящего момента у надстройки были только минимально необходимые разрешения, но методу  `GetLocalEmployeeName` необходимо разрешение на чтение списков хост-сайта. Надстройка использует свой манифест, чтобы сообщить SharePoint, какие разрешения ей необходимы. Выполните указанные ниже действия.




1. В **обозревателе решений** в проекте **ChainStore** откройте файл AppManifest.xml. Файл называется AppManifest и в нем использовано слово App, потому что ранее надстройки назывались приложениями. Откроется конструктор манифестов.


2. Откройте вкладку **Разрешения** и щелкните пустую ячейку под столбцом **Область**. Затем в раскрывающемся списке выберите **Список**.


3. В поле **Разрешение** в раскрывающемся списке выберите **Чтение**.


4. Оставьте поле **Свойства** пустым и сохраните файл. Вкладка **Разрешение** должна иметь примерно следующий вид:

![Вкладка "Разрешения" конструктора манифеста надстройки, на которой отображается область, которую необходимо отобразить, и разрешения, которые необходимо прочитать.](images/8dd2a25f-103a-42af-aa88-c8ec796315db.PNG)






## Запуск надстройки и тестирование кнопки






1. Нажмите клавишу F5, чтобы развернуть и запустить вашу надстройку. Visual Studio размещает удаленное веб-приложение в IIS Express, а базу данных SQL в SQL Express. Кроме того, он создает временную установку надстройки на вашем тестовом сайте SharePoint и сразу же запускает ее. Прежде чем откроется начальная страница надстройки, вам будет предложено предоставить надстройке необходимые разрешения. В этот раз будет предложен раскрывающийся список, в котором вы можете выбрать список, данные из которого необходимо считывать приложению, как показано на снимке экрана ниже. 

![Приглашение разрешения надстройки SharePoint со списком "Местные сотрудники", выбранным в раскрывающемся списке "Чтение элементов в списке"](images/84e8b42c-4800-4947-acbd-21c6f096f4ea.PNG)





2. В списке выберите пункт **Local Employees** (Местные сотрудники), а затем нажмите кнопку **Доверять**.


3. Когда откроется начальная страница, нажмите кнопку **Back to Site** (Вернуться на сайт) на размещенном в верхней части элементе управления хрома.


4. С домашней страницы веб-сайта перейдите на **Site Contents | Local Employees** (Содержимое сайта | Местные сотрудники). Откроется страница представления списка.


5. Добавьте несколько сотрудников в список.  *Не устанавливайте флажок **Added to Corporate DB** (Добавлен в корпоративную базу данных).* 


6. На ленте откройте вкладку **Items** (Элементы). В разделе **Actions** (Действия) вкладки имеется настраиваемая кнопка **Add to Corporate DB** (Добавить в корпоративную базу данных).


7. Выберите элемент в списке. Страница и лента должны иметь примерно следующий вид:

![Список местных сотрудников. Один элемент выделен. Над ним лента и кнопка с текстом "Добавить в базу данных предприятия" в разделе "Действия".](images/797a5ceb-7291-4b62-8075-2bb6a1b8e8a1.PNG)





8. Нажмите кнопку **Add to Corporate DB** (Добавить в корпоративную базу данных). * **Перед этим необходимо выбрать элемент.*** 


9. Вам покажется, что страница перезагружается, так как метод **Page_Load** страницы EmployeeAdder выполняет перенаправление на нее.


10. Дважды нажмите кнопку "Назад" в браузере, чтобы вернуться на начальную страницу надстройки. 


11. Нажмите кнопку **Show Employees** (Показать сотрудников), после чего список сотрудников будет заполнен добавленными вами сотрудниками. Он должен выглядеть примерно следующим образом:

![Список сотрудников организации на начальной странице надстройки, в котором отображается сотрудник, выбранный в предыдущем действии.](images/4a300a4e-f479-4f63-b536-6315c5d9ba4d.PNG)





12. Чтобы завершить сеанс отладки, закройте окно браузера или остановите отладку в Visual Studio. При каждом нажатии клавиши F5 Visual Studio будет отзывать предыдущую версию надстройки и устанавливать ее последнюю версию.


13. Вы будете работать с этой надстройкой и решением Visual Studio и при изучении других статей, поэтому при перерывах в работе рекомендуется отзывать надстройку. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Отозвать**.



## 
<a name="Nextsteps"> </a>

 В следующей статье мы немного отвлечемся от программирования и изучим клиентскую объектную модель SharePoint: [Краткий обзор объектной модели SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md).




