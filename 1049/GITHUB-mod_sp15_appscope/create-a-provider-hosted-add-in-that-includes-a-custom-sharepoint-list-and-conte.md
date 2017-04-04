---
title: Создание надстроек с размещением у поставщика, которые включают в себя пользовательский список SharePoint и тип контента
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# Создание надстроек с размещением у поставщика, которые включают в себя пользовательский список SharePoint и тип контента
Создайте надстройку SharePoint, сочетающую в себе размещенное в облаке веб-приложение и настраиваемые шаблоны списков, экземпляры списков и пользовательские типы содержимого, размещенные в SharePoint, с помощью Инструменты разработчика Office для Visual Studio 2012. Узнайте, как взаимодействовать с сайтами надстроек SharePoint 2013 посредством веб-службы REST/OData и как применять OAuth в надстройке SharePoint.
Наиболее классические компоненты SharePoint, такие как настраиваемые типы контента, настраиваемые определения списков и рабочие процессы можно включить в надстройку SharePoint с размещением в облаке. Простейший пример в данной статье включает в себя следующее:
  
    
    


- Сайт надстройки с
    
  - рядом настраиваемых столбцов веб-сайтов
    
  
  - типом пользовательского контента, использующим настраиваемые столбцы
    
  
  - шаблоном настраиваемых списков, в котором используется данный тип пользовательского контента
    
  
  - экземпляром списка, основанным на пользовательском определении списка
    
  
- Веб-приложение ASP.NET, считывающее данные из экземпляра списка
    
  

## Компоненты, необходимые для создания этой надстройки для SharePoint
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/ru-ru/download/details.aspx?id=30682) или более новой версии.
    
  
-  [Office Developer Tools](https://msdn.microsoft.com/ru-ru/office/aa905340.aspx)
    
  
- Установка SharePoint 2013для тестирования и отладки
    
  - Может выполняться на том же компьютере, что и разработка. Разработка может также выполняться посредством удаленной установки SharePoint 2013. Если вы работаете с удаленной установкой, необходимо установить распространяемую клиентскую модель объекта для целевой установки. Она доступна в качестве распространяемого пакета в Центре загрузки Майкрософт  выполните поиск по словам "пакет SDK для клиентских компонентов SharePoint Server 2013" или "пакет SDK для клиентских компонентов SharePoint Online". 
    
  
  - Из определения сайта **Developer Site** (Сайт разработчика), которое можно создать в Центре администрирования, необходимо создать тестовый веб-сайт SharePoint.
    
  
  - Для обмена данными с сайтом надстройки ваше удаленное приложение использует либо JavaScript и  [междоменную библиотеку](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md), либо  [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md). При использовании OAuth, как показано в примере ниже в данной статье, необходимо настроить установку SharePoint 2013 для работы с OAuth.
    
  

> **Примечание**
> Руководство о том, как настроить среду разработки, соответствующую вашим потребностям, см. в разделе  [Начало создания приложений для Office и SharePoint](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx). 
  
    
    


### Ключевые понятия, которые необходимо знать при создании надстройки

Перед созданием первой надстройки необходимо иметь базовое представление о том, что такое Надстройки SharePoint, и о различиях между размещением надстроек SharePoint в SharePoint и в облаке. Такие сведения имеются в статьях, перечисленных в таблице 1.
  
    
    

**Таблица 1. Ключевые понятия для создания надстройки**


|**Заголовок статьи**|**Описание**|
|:-----|:-----|
| [Надстройки SharePoint](sharepoint-add-ins.md) <br/> |Изучите новую модель надстроек в SharePoint 2013, позволяющую создавать небольшие и удобные в использовании решения для конечных пользователей.  <br/> |
| [Важные аспекты архитектуры и разработки надстройки SharePoint](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |Изучите аспекты архитектуры надстроек SharePoint и Модель для надстроек SharePoint, в том числе параметры размещения надстроек и пользовательского интерфейса, систему развертывания, систему безопасности и жизненный цикл.  <br/> |
| [Выбор шаблонов для разработки и размещения надстройки SharePoint](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |Узнайте о различных способах, с помощью которых вы можете разместитьНадстройки SharePoint.  <br/> |
   

## Разработка надстройки для SharePoint
<a name="Develop"> </a>

 С помощью процедур в данном разделе на своем компьютере для разработки вы сможете создать надстройку SharePoint, включающую сайт надстройки с компонентами SharePoint и удаленное веб-приложение.
  
    
    

### Настройка решения Visual Studio 2012 и его элементов


1. В Visual Studio 2012 создайте проект **Add-in for SharePoint 2013** в узле **Office SharePoint | Add-ins** (либо на **C#**, либо на **Visual Basic**) в дереве шаблонов мастера **создания проектов**. Выберите вариант **размещения у поставщика**. В примере ниже используется язык C# и имя проекта LocalTheater.
    
  
2. Нажмите в мастере кнопку **Готово**.
    
  
3. Откройте файл AppManifest.xml в конструкторе манифеста. По умолчанию в качестве значения элемента **Title** используется имя проекта. Замените его более понятным именем, так как именно это имя надстройки будет отображаться в интерфейсе для пользователей.
    
  
4. Укажите **Name** для надстройки. Это внутреннее имя, в котором можно использовать только символы ASCII, оно не должно содержать пробелы. Пример:LocalTheater.
    
  
5. Откройте файл Web.config в проекте веб-приложения и добавьте  `<customErrors mode="Off"/>` элемента к элементу **system.web**.
    
  
6. Проверьте, находятся ли ссылки на следующие сборки в проекте веб-приложения. (Если ваша версия Visual Studio 2012 не добавила ссылки автоматически, добавьте их.)
    
  - **Microsoft.IdentityModel.dll** Данная сборка устанавливается в глобальном кэше сборки посредством Windows Identity Foundation (WIF). Так как данная сборка является сборкой.NET Framework 3.5, она отфильтровывается из узла **Framework** в диалоговом окне **Добавить ссылку** по умолчанию. Вы можете добавить к ней ссылку, переместившись непосредственно в папку `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` вашего компьютера, используемого для разработки.
    
  
  - **Microsoft.IdentityModel.Extensions.dll** Вы можете добавить к ней ссылку, переместившись непосредственно в папку `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` вашего компьютера, используемого для разработки.
    
  
  - **System.IdentityModel.dll** Данная сборка является частью .NET Framework 4, и она появляется в узле **Assemblies | Framework** диалогового окна **Добавить ссылку**.
    
  
7. Если ваше удаленное приложение получает доступ к информации на хост-сайте и на сайте надстройки, то необходимо добавить элемент **AppPermissionRequests** (с одним или несколькими дочерними элементами **AppPermissionRequest**) в файл AppManifest.xml. (Веб-приложение в примере ниже получает доступ только к сайту надстройки. Субъекты надстроек автоматически получают все разрешения, необходимые для сайта надстройки, поэтому в примере в файле AppManifest.xml нет элемента **AppPermissionRequests**.) Дополнительные сведения о запросах разрешений для надстроек и о том, как добавлять их, см. в разделе  [Разрешения для надстроек в SharePoint 2013](add-in-permissions-in-sharepoint-2013.md).
    
  

### Добавление компонентов SharePoint


1. Вы можете добавлять компоненты SharePoint в надстройку точно так же, как и в классическое решение фермы. Тем не менее, не каждый тип компонентов SharePoint можно включить в надстройку SharePoint. Цели, для которых служат этих компоненты, Надстройки SharePoint достигают другими способами. Подробные сведения о том, какие типы компонентов SharePoint можно включить в надстройку SharePoint, и о том, как включить их в проект, см. в разделах  [Типы компонентов SharePoint, которые могут находиться в надстройке для SharePoint](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps).
    
    Для примера используйте указанные ниже процедуры. В них имеются примеры использования Visual Studio 2012, с помощью которых можно добавить настраиваемые столбцы, типы содержимого, шаблоны списков и экземпляры списков в надстройку SharePoint.
    
### Для создания настраиваемых типов столбцов


1. В **Solution Explorer** добавьте элемент SharePoint **Столбец сайта** в надстройку SharePoint проект с именемActor.
    
  
2. В файле elements.xml для нового столбца сайта отредактируйте элемент **Field** так, чтобы он имел атрибуты и значения, приведенные в следующем примере, однако не меняйте GUID атрибута **ID**, оставив сгенерированное для него значение Visual Studio 2012. Не забудьте поставить фигурные скобки "{}".
    
  ```
  
<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
  ```

3. Добавьте другой **Столбец сайта**в проект под именем CastingStatus.
    
  
4. В файле elements.xml нового столбца сайта отредактируйте элемент **Field**, так, чтобы он содержал атрибуты и значения, приведенные в следующем примере, однако не меняйте GUID атрибута **ID**, оставив сгенерированное для него значениеVisual Studio 2012.
    
  ```
  
<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
  ```

5. Так как это поле является полем выбора (Choice), необходимо указать возможные варианты, порядок их отображения в выпадающем списке перед пользователем, который должен сделать выбор, и вариант по умолчанию. Добавьте следующую дочернюю маркировку к элементу **Field**
    
  ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
  ```


### Создание пользовательского типа контента


1. В **Solution Explorer** добавьте элемент SharePoint **Content Type** в проект надстройки SharePoint с именемActingRole. При запросе мастера выбрать базовый тип контента выберите **Элемент**, а затем **Finish**
    
  
2. Если конструктор типа контента не открывается автоматически, выберите тип контента **ActingRole** в **Solution Explorer**, чтобы открыть его.
    
  
3. Откройте вкладку **Content Type** в конструкторе и заполните текстовые поля следующим образом:
    
  - **Имя типа контента**: ActingRole
    
  
  - **Описание**: Соответствует роли в пьесе или фильме.
    
  
  - **Имя группы**: Театр и кино
    
  
4. Убедитесь, что ни один из флажков, находящихся во вкладке, не выбран ( *None*  ). Флажок **Наследует столбцы из родительского Типа контента** может быть выбран по умолчанию. *Обязательно очистите его.* 
    
  
5. Откройте вкладку **Столбцы** конструктора.
    
  
6. Для добавления данных двух столбцов сайтов к типу контента используйте сетку. Они располагаются в выпадающем списке в порядке отображаемых имен: **Actor/Actress** и **CastingStatus**. (Если они отсутствуют в списке, вы могли не сохранить проект после добавления настраиваемых столбцов сайтов. Выберите **Сохранить все**.)
    
  
7. Сохраните файл и закройте конструктор.
    
  
8. Следующий шаг требует от вас работы с типом контента непосредственно в исходном XML-файле, поэтому в **Solution Explorer** выберите дочерний объект (потомок) файла elements.xml с типом контента **ActingRole**
    
  
9. Для двух добавленных вами столбцов в файле уже существуют элементы **FieldRef**. Добавьте элементы **FieldRef** для двух встроенных столбцов SharePoint 2013 в качестве аналогов для уже имеющихся двух других. Маркировкой элементов является следующая. *Вы должны использовать те же самые GUID для атрибута ID, так как они являются встроенными типами полей с фиксированными ID.*  Добавьте эти *поверх*  имеющихся двух элементов **FieldRef** для настраиваемых столбцов сайтов.
    
  ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  ```


    Обратите внимание, что мы присвоили данным полям пользовательское отображаемое имя: **Герой** в значении героя пьесы или фильма.
    
  

### Чтобы создать пользовательский шаблон списка и экземпляр списка


1. Добавьте элемент SharePoint **Список** в проект надстройки SharePoint с именемCharactersInShow. На странице **Выбор настроек списка** **мастера настроек SharePoint** оставьте отображаемое имя списка в виде заданного по умолчанию **CharactersInShow**, нажмите кнопку **Создать настраиваемый список на основе**, а затем выберите элемент **По умолчанию (пусто)** в раскрывающемся списке. Далее нажмите кнопку **Готово**.
    
  
2. После завершения работы мастера будет создан шаблон списка **CharactersInShow** с экземпляром дочернего списка под названием **CharactersInShowInstance**. По умолчанию может открыться конструктор списка. Он понадобится в следующем шаге.
    
  
3. Откройте дочерний объект файла elements.xml шаблона списка **CharactersInShow** ( *не*  дочерний объект файла elements.xml, относящегося к **CharactersInShowInstance**).
    
  
4. Добавьте пробелы в атрибут **DisplayName**, чтобы он стал более понятен: Characters In Show (Герои шоу).
    
  
5. Установите атрибут **Description** дляГерои пьесы или фильма
    
  
6. Оставьте другие атрибуты такими, какими они заданы по умолчанию, сохраните и закройте файл.
    
  
7. Если конструктор списка не открыт, выберите узел **CharactersInShow** в **обозревателе решений**.
    
  
8. Откройте вкладку **Столбцы** конструктора и выберите кнопку **Типы контента**.
    
  
9. В диалоговом окне настроек типа контента **Параметры типов содержимого** добавьте тип **ActingRole** контента.
    
  
10. Выберите тип контента **ActingRole** из списка типов контента, а затем нажмите кнопку **Установить по умолчанию**
    
    Выберите тип контента **Элемент**, правой кнопкой мыши щелкните маленькую стрелку, которая появится слева от имени типа контента, затем выберите **Удалить**
    
  
11. Повторите предшествующий шаг для типа контента **Папка**, чтобы **ActingRole** остался единственным типом контента в списке. Чтобы закрыть диалоговое окно, выберите **ОК**.
    
  
12. Теперь в списке столбцов находится три столбца. Выберите **Заголовок**, правой кнопкой мыши щелкните маленькую стрелку, которая появится слева от имени типа контента, а затем выберите **Удалить**. Теперь в списке должно быть только два столбца, **Actor/Actress** (Актер/актриса) и **Casting Status** (Статус кастинга).
    
  
13. Откройте вкладку **Список** конструктора. Данная вкладка используется для настройки определенных значений применительно к *экземпляру*  списка, а не *шаблона*  списка.
    
  
14. Замените значения на вкладке следующими:
    
  - **Заголовок**: Characters in Hamlet (Действующие лица в "Гамлете")
    
  
  - **URL-адрес списка**: Lists/CharactersInHamlet
    
  
  - **Описание**: Действующие лица в "Гамлете" и информация по кастингу.
    
  

     Оставьте флажки в том виде, в каком они заданы по умолчанию, сохраните файл и закройте конструктор.
    
  
15. Экземпляр списка может иметь старое имя в **обозревателе решений**. В этом случае откройте контекстное меню **CharactersInShowInstance**, выберите элемент **Переименовать** и измените название наCharactersInHamlet.
    
  
16. Откройте файл schema.xml.
    
  
17. Файл может содержать два **ContentType** элемента, один со значением атрибута **Name**, относящегося к ActingRole, другой  под именем **ListFieldsContentType**. Подходит только один, под названием ActingRole, поэтому удалите остальные элементы **ContentType**.
    
    > **Примечание**
      > Между элементами **ContentType**может не быть разрывов строк, вследствие чего может показаться, что элемент один. Прокрутите изображение в правую сторону и внимательно проверьте наличие остальных элементов 
18. Элемент **Fields** должен иметь два элемента **Field** (которые расположены на одной строке, если между ними нет разрыва строки). Один должен в точности дублировать элемент **Field** в **Actor** site column elements.xml, а другой полностью дублировать элемент **Field** в **CastingStatus** site column elements.xml. При отсутствии полного совпадения, включая все дочерние элементы (такие как элементы **CHOICES** и **MAPPINGS**), скопируйте элемент **Field** из файла site column elements.xml file и вставьте вместо несовпадающего элемента **Field** в файле schema.xml.
    
  
19. Находясь в файле schema.xml, в элементе **View**, чье значение BaseViewID равно 0, замените существующий элемент **ViewFields** следующей разметкой. (Используйте именно этот GUID для **FieldRef** под именем `LinkTitle`.)
    
  ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
  ```

20. Замените два отсутствующих значения атрибута ID идентификаторами GUID в соответствующих файлах site column elements.xml. Помните о фигурных скобках "{}". 
    
  
21. Находясь в файле schema.xml, в элементе **View**, чье значение BaseViewID равно"1", замените существующий элемент **ViewFields** следующей разметкой. (Используйте именно этот GUID для **FieldRef** под именем `LinkTitle`.)
    
  ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
  ```

22. Скопируйте два элемента **FieldRef** для `Actor` и `CastingStatus`, которые вы добавили в предшествующий вид, в этот элемент **ViewFields** в качестве одноуровневых элементов `LinkTitle` **FieldRef**.
    
  
23. Сохраните и закройте файл schema.xml.
    
  
24. Откройте файл elements.xml, являющийся дочерним объектом экземпляра списка **CharactersInHamlet**
    
  
25. Заполните список какими-либо начальными данными, добавив следующую разметку в качестве дочерних объектов (потомков) элемента **ListInstance**.
    
  ```
  
<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
  ```

2. В **обозревателе решений** выберите **Возможность1** для открытия конструктора возможностей. В конструкторе установите в качестве заголовка **Заголовок**Компоненты данных театра и кино, а в качестве элемента **Описание** Столбцы сайтов, типы контента и экземпляры списков применительно к данным о театре и кино. Сохраните файл и закройте конструктор.
    
  
3. Если **Feature1** в **Solution Explorer** не была переименована, откройте ее контекстное меню, выберите **Переименовать** и переименуйте ее вTheaterAndMovieDataComponents.
    
  

### Для кодирования проекта удаленного приложения


- Вы можете разрабатывать веб-приложение аналогично любому другому веб-приложению для предпочитаемой вами платформы. Что касается стека Майкрософт, вы можете использовать либо веб-службу REST/OData, либо одну из клиентских объектных моделей в SharePoint 2013. При работе с другими стеками вы можете использовать конечные точки REST/OData в SharePoint 2013 для выполнения операций создания, чтения, обновления и удаления данных на сайте надстройки.
    
    > **Примечание**
      > При добавлении ссылки на сборку для проекта веб-приложения в Visual Studio задайте для свойства сборки **Копировать локально** значение **True**, если вам точно не известно, установлена ли сборка на веб-сервере и сможете ли вы обеспечить ее установку перед развертыванием надстройки. Платформа .NET Framework устанавливается в веб-ролях Microsoft Azure и в Веб-сайты Azure. Тем не менее клиентские сборки для SharePoint 2013, а также различные расширения и основы для управляемого кода Майкрософт не устанавливаются. Инструменты разработчика Office для Visual Studio 2012 автоматически добавляет ссылки на некоторые сборки, которые Надстройки SharePoint часто используют, и задает свойство **Копировать локально**. 

    В продолжение текущего примера вы разрабатываете веб-приложение ASP.NET. Выполните следующие действия.
    
1. Откройте файл Default.aspx и замените элемент body в файле указанной ниже разметкой. Эта разметка добавляет кнопку **Get the Cas** (Получить состав актеров), при выборе которой выполняется чтение списка **Characters in Hamlet** (Действующие лица в "Гамлете"), находящегося на сайте надстройки, и отображение данных кнопки в элементе управления [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) , который появляется только после нажатия кнопки.
    
  ```HTML
  
<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
  ```

2. Откройте файл Default.aspx.cs и добавьте в него следующие операторы **using**.
    
  ```cs
  
using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
  ```


    Последний из этих операторов относится к области имен, указанной в файле TokenHelper.cs.
    
  
3. Добавьте следующие поля в класс **Default**.
    
  ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
  ```

4. Замените метод **Page_Load** следующим кодом, который использует класс **TokenHelper** для получения токенов из безопасного сервера токенов, совместимого с OAuth. Тогда токен доступа сохраняется в свойстве [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) кнопки для дальнейшего вызова обработчиком события по щелчку кнопки.
    
  ```cs
  
protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;
        
        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
  ```

5. Добавьте к классу **Default** следующий обработчик событий. Действие обработчика начинается с вызова маркера доступа, который был сохранен в свойстве [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) кнопки.
    
  ```cs
  
protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
  ```

6. Обработчику требуется заново получить измененный URL-адрес сайта надстройки при обратной передаче, поэтому добавьте указанный ниже код.
    
  ```cs
  
if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
  ```

7. Для получения данных списка добавьте следующую строку, в которой используется одна из конечных точек SharePoint 2013 REST/OData. В данном примере код считывает список **Characters in Hamlet** (Действующие лица в "Гамлете"), развернутый на сайте надстройки. Благодаря API для этой службы можно легко, с помощью одной строки кода, выбрать список и указать три поля, которые возвратит список. Обратите внимание, что в URL-адресе OData необходимо использовать внутренние имена полей (столбцов), а не отображаемые имена, поэтому код использует `Title`,  `Actor` и `CastingStatus`, а не  `Character`,  `Actor/Actress` и `Casting Status.` Подробные сведения о веб-службе REST/OData см. в разделе [Использование операций запросов OData в запросах SharePoint REST](use-odata-query-operations-in-sharepoint-rest-requests.md).
    
  ```cs
  
// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
  ```

8. Добавьте следующий код, использующий классы  [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) и [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) поля имени [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) для создания объектов запроса и ответа HTTP.
    
  ```cs
  
// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
  ```

9. Для выполнения синтаксического анализа XML-ответа в формате ATOM добавьте следующий код. Он использует классы области имен  [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) для синтаксического анализа возвращенных данных и создания [List<T>](http://msdn2.microsoft.com/RU-RU/library/6sh2ey19) элементов из списка SharePoint. (Вы также могли бы использовать классы области имен [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) .) Обратите внимание, что в XML-файле, возвращаемом SharePoint, дочерние элементы элемента **entry** содержат метаданные об элементе списка. Фактические данные строк элемента списка SharePoint размещаются двумя уровнями ниже в элементе **properties**. По этой причине для отфильтровывания верхних уровней дважды используется метод расширения  [Elements<T>](http://msdn2.microsoft.com/RU-RU/library/bb348465)
    
  ```cs
  
// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
  ```

10. Для создания коллекции анонимного типа  [IEnumerable<T>](http://msdn2.microsoft.com/RU-RU/library/9eekhta0), имеющей только нужные вам свойства, добавьте следующий запрос LINQ. Обратите внимание, что, хотя код должен ссылаться на поле заголовка элемента по его внутреннему имени  `Title`, имя свойства в анонимном типе, которому присваивается значение, может быть  `Character`. Одно из следствий этого  появление на странице, после привязки коллекции к элементу управления "сетка", более подходящего имени **Герой**.
    
  ```cs
  
var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

  ```

11. Завершите настройку обработчика следующим кодом, привязывающим данные к элементу контроля  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) на странице. Заголовкам столбцов в сетке по умолчанию присваиваются имена свойств анонимного типа: `Character`,  `Actor` и `CastingStatus`. Элемент управления  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) имеет свойства, позволяющие управлять именем и форматировать заголовки колонок, поэтому вы можете сделать так, чтобы имена **Actor/Actress** (Актер/актриса) и **Casting Status** (Статус кастинга) совпадали с заголовками колонок в SharePoint. Чтобы избежать сложностей, эти методы здесь не описываются. (Вы можете также использовать элемент управления [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) .)
    
  ```cs
  
GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

  ```

12. Сохраните все файлы.
    
  

### Тестирование и отладка надстройки для SharePoint


1. Чтобы протестировать надстройку SharePoint и ее удаленное веб-приложение, в Visual Studio 2012 нажмите клавишу F5. Веб-приложение будет развернуто в IIS Express в localhost. Надстройка SharePoint будет установлена на целевом веб-сайте SharePoint. (В нашем примере удаленная надстройка  *не*  пытается взаимодействовать с *хост*  -сайтом, и субъект надстройки автоматически получает разрешения для доступа к сайту *надстройки*  , поэтому для вас *не*  отображается предложение предоставить разрешения.) Откроется страница **Содержимое сайта** целевого веб-сайта SharePoint, на которой отобразится новая надстройка.
    
  
2. Выберите надстройку SharePoint, после чего удаленное веб-приложение откроется на странице, указанной вами в элементе **StartPage** файла AppManifest.xml. Откройте приложение и проверьте его работоспособность. В нашем примере достаточно нажать кнопку. После этого будет создана таблица, заполненная элементами списка **Characters in Hamlet** (Действующие лица в "Гамлете") с сайта надстройки.
    
  

## Публикация надстройки для SharePoint
<a name="Publish"> </a>

Чтобы опубликовать вашу надстройку SharePoint, отправьте пакет надстройки в корпоративный каталог надстроек или в магазин надстроек Office. Дополнительные сведения см. в разделах  [Публикация в Магазин Office или каталоге надстроек организации](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog) и [Публикация надстроек для SharePoint](publish-sharepoint-add-ins.md).
  
    
    

## Устранение неполадок
<a name="Troubleshooting"> </a>

Если надстройка не работает, рекомендуется проверить, не является ли это следствием ошибки разметки CAML, блокирующей развертывание компонентов SharePoint. Чтобы проверить развертывание, используйте процедуру, аналогичную описанной ниже.
  
    
    

### Проверка подготовки сайта надстройки к работе


1. Откройте страницу **Site Settings** (Настройки сайта) хост-сети. В разделе **Site Collection Administration** (Администрирование коллекции сайтов) выберите ссылку **Site hierarchy** (Иерархия сайтов).
    
  
2. Ваша надстройка отображается на странице **иерархии сайтов** в списке, отсортированном по URL-адресам надстроек. Не запускайте ее. Просто скопируйте ее URL-адрес и используйте его в действиях ниже.
    
  
3. Перейдите по адресу  _URL-адрес_сайта_приложения_/_layouts/15/ManageFeatures.aspx и на открывшейся странице **Site Features** (Возможности сайта) проверьте, что **Theater and Movie Data Components** (Компоненты данных театра и кино) находятся в алфавитном списке возможностей вашей надстройки SharePoint в **активном** состоянии.
    
  
4. Перейдите по адресу  _URL-адрес_сайта_приложения_/_layouts/15/mngfield.aspx. На открывшейся странице **столбцов сайта** проверьте наличие группы **Театр и кино** в списке столбцов сайта, а также наличие в нем новых пользовательских столбцов сайта  **Actor/Actress** и **Casting Status**
    
  
5. Перейдите по адресу  _URL-адрес_сайта_приложения_/_layouts/15/mngctype.aspx и на открывшейся странице **Типы контента сайта** проверьте наличие группы **Театр и кино** в списке типов контента, а также наличие там нового типа контента  **ActingRole**.
    
  
6. Выберите ссылку, ведущую к типу контента **ActingRole**. На открывшейся странице **Site Content Type** проверьте, содержит ли тип контента два новых типа столбцов сайта, **Actor/Actress** и **Casting Status**, а также, что элемент в поле заголовка получил пользовательское отображаемое имя: **Character**.
    
  
7. Перейдите по адресу  _URL-адрес_сайта_приложения_/_layouts/15/mcontent.aspx и на открывшейся странице **Списки и библиотеки сайта** проверьте наличие ссылки **Настроить "Characters in Hamlet"** (Настроить "Действующие лица в «Гамлете»").
    
  
8. Выберите ссылку **Настроить "Characters in Hamlet"** и с помощью страницы настроек списка убедитесь, что единственный тип контента для данного списка  это настроенный вами тип контента **ActingRole**, и что ваши два новых столбца сайта, **Actor/Actress** и **Casting Status**, находятся в списке раздела **Столбцы**. (Столбец заголовка может появиться на экране с внутренним именем **Заголовок** вместо присвоенного вами отображаемого имени **Character**.)
    
    > **Примечание**
      > Если на этой странице отсутствует раздел **типов контента**, необходимо включить управление типов контента. Щелкните ссылку **Дополнительные параметры**, на странице **дополнительных параметров** включите управление типов контента и нажмите кнопку **ОК**. Вы будете возвращены на предыдущую страницу, где теперь имеется раздел **типов контента**. 
9. В верхней части страницы находится **Web Address** (Веб-адрес) списка. Скопируйте его и вставьте в адресную строку вашего браузера, а затем перейдите к списку. Убедитесь, что в списке находятся элементы, созданные вами качестве образцов. (Столбец Title может появиться на экране с внутренним именем **Title** вместо присвоенного вами отображаемого имени **Character**.)
    
  

## Дальнейшие действия
<a name="NextSteps"> </a>

В данной статье было показано, как создать несложный гибрид надстройки SharePoint с удаленным веб-приложением. В качестве дальнейших шагов можно рассмотреть следующие:
  
    
    

- Добавление в надстройку всех функций CRUD с помощью конечных точек REST/OData или с помощью одной из клиентских объектных моделей. Подробные сведения см. в разделе  [Использование операций запросов OData в запросах SharePoint REST](use-odata-query-operations-in-sharepoint-rest-requests.md) и [Выполнение базовых операций с использованием кода библиотеки клиента в SharePoint 2013](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
- Надстройка SharePoint: локализация для других стран. Подробные сведения см. в разделе  [Локализация надстроек для SharePoint](localize-sharepoint-add-ins.md).
    
  
- Создание надстройки-компаньона для Windows Phone, которая дублирует функции удаленного веб-приложения. Дополнительные сведения см. в разделе  [Создание мобильных надстроек для SharePoint 2013](https://msdn.microsoft.com/ru-ru/library/office/jj163228.aspx).
    
  

## Дополнительные ресурсы
<a name="SP15createcloud_bk_addlresources"> </a>


-  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [Знакомство с созданием надстроек SharePoint с размещением в SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

