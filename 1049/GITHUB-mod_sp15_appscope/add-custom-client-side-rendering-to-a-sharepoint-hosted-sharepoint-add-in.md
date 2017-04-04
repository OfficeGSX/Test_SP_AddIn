---
title: Добавление настраиваемой функции отрисовки в клиенте в надстройку SharePoint, размещаемую в SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6e0e530a-7d8b-48ab-8d0c-a878ddbf5be1
---


# Добавление настраиваемой функции отрисовки в клиенте в надстройку SharePoint, размещаемую в SharePoint
В данной статье рассказано, как настраивать, отрисовывать и проверять элементы управления на страницах надстроек SharePoint.
Это восьмая часть из серии статей о том, как разрабатывать Надстройки SharePoint, размещаемые в SharePoint. Сначала вам необходимо ознакомиться со статьей  [Надстройки SharePoint](sharepoint-add-ins.md) и с указанными ниже предыдущими статьями серии.
  
    
    


-  [Знакомство с созданием надстроек SharePoint с размещением в SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  
-  [Развертывание и установка надстроек для SharePoint](deploy-and-install-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Добавление настраиваемых столбцов в надстройку с размещением в SharePoint](add-custom-columns-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Добавление настраиваемого типа контента в надстройку для SharePoint с размещением в SharePoint](add-a-custom-content-type-to-a-sharepoint-hostedsharepoint-add-in.md)
    
  
-  [Добавление веб-части на страницу в надстройку для SharePoint с размещением в SharePoint](add-a-web-part-to-a-page-in-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Добавление рабочего процесса в надстройку для SharePoint с размещением в SharePoint](add-a-workflow-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  
-  [Добавление настраиваемой страницы и стиля для надстройки с размещением в SharePoint](add-a-custom-page-and-style-to-a-sharepoint-hosted-sharepoint-add-in.md)
    
  

> **Примечание**
> Если вы изучали предыдущие статьи этой серии о надстройках, размещаемых в SharePoint, то у вас уже есть решение для Visual Studio, которое можно использовать для работы с данной статьей. Кроме того, вы можете скачать репозиторий  [SharePoint_SP-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_SP-hosted_Add-Ins_Tutorials) и открыть файл BeforeClientRenderedControl.sln.
  
    
    

Вы можете использовать маленький клиентский JavaScript для настройки отрисовки веб-частей, большинства типов полей (столбцов) и ряда других элементов управления, назначив файл JavaScript свойству **JSLink** элемента управления, например **SPField.JSLink**. Кроме того, таким образом вы можете добавить код клиентской части для проверки. В данной статье вы настроите отрисовку поля в списке Employee Orientation (Вводное обучение сотрудников) из надстройки SharePoint, используя отрисовку в клиентской части.
> **Примечание**
> Если в браузере конечного пользователя отключен JavaScript, то SharePoint будет использовать серверные функции отрисовки и проверки. 
  
    
    


> **Примечание**
> Свойство JSLink не поддерживается в списках Survey или Events. Календарь SharePoint представляет собой список Events. 
  
    
    


## Создание и регистрация JavaScript


  
    
    

1. В **обозревателе решений** щелкните правой кнопкой мыши узел **Сценарии** и последовательно выберите пункты **Добавить**, **Создать элемент** и **Интернет**.
    
  
2. Выберите **файл JavaScript** и назовите егоOrientationStageRendering.js.
    
  
3. Настраиваемая отрисовка поля должна выполняться автоматически, поэтому добавьте в JavaScript анонимный метод, который будет автоматически запускаться при загрузке файла с указанным ниже кодом.
    
  ```
  
(function () {

})();
  ```

4. В тексте этого метода (т. е. между символами { и }) добавьте указанный ниже код, чтобы создать объекты JSON (нотации объектов Javascript) для отрисовки контекста переопределения, шаблонов в контексте и шаблонов для полей.
    
  ```
  
var customRenderingOverride = {};
customRenderingOverride.Templates = {};
customRenderingOverride.Templates.Fields = {

}
  ```

5. В тексте объекта шаблона  `Fields` добавьте указанный ниже JSON. Имя свойства `OrientationStage` идентифицирует поле, настроившее отрисовку. Значением свойства является другой объект JSON. Свойство `View` идентифицирует контекст страницы, в котором применяется настраиваемая отрисовка. В данном случае объект сообщает SharePoint, что необходимо использовать настраиваемую отрисовку представлений списков. (Другие параметры будут использоваться для форм изменения, создания и отображения.) Значение свойства ( `renderOrientationStage`)  это имя метода настраиваемой отрисовки, который вы создадите на одном из следующих этапов.
    
  ```
  
"OrientationStage": { "View": renderOrientationStage }
  ```

6. Последнее, что должен сделать анонимный метод,  сообщить диспетчеру шаблонов SharePoint о переопределении отрисовки. Добавьте указанную ниже строку в конец раздела body метода.
    
  ```
  SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
  ```


    Теперь этот метод должен выглядеть указанным ниже образом.
    


  ```
  (function () {
    var customRenderingOverride = {};
    customRenderingOverride.Templates = {};
    customRenderingOverride.Templates.Fields = {
        "OrientationStage": { "View": renderOrientationStage }
    }

    SPClientTemplates.TemplateManager.RegisterTemplateOverrides(customRenderingOverride);
})();
  ```

7. Добавьте указанный ниже метод в файл. Он задает для значения столбца **Orientation Stage** (Этап вводного обучения) красный цвет, когда оно равно Not Started (Не начат), и зеленый, когда значение равно Completed (Завершен). (Объект `ctx`  это объект контекста клиента, объявленный во встроенном сценарии SharePoint.)
    
  ```
  
function renderOrientationStage(ctx) {
    var orientationStageValue = ctx.CurrentItem[ctx.CurrentFieldSchema.Name];
    if (orientationStageValue == "Not Started")  {
        return "<span style='color:red'>" + orientationStageValue + "</span>"
    }
    else if (orientationStageValue == "Completed") {
        return "<span style='color:green'>" + orientationStageValue + "</span>"
    }
    else {
        return orientationStageValue;
    }
}
  ```

8. В **обозревателе решений** разверните **Столбцы сайта**, а затем  **OrientationStage**. После этого откройте файл elements.xml.
    
  
9. Чтобы сообщить SharePoint, что необходимо использовать настраиваемый JavaScript, добавьте новый атрибут **JSLink** в элемент **Field**, а затем в качестве его значения назначьте следующий URL-адрес:  `~site/Scripts/OrientationStageRendering.js`.
    
    > **Примечание**
      > Свойство **JSLink** всегда является файлом, а не методом. Не существует способа сообщить SharePoint, какой метод необходимо запустить. Именно поэтому файл содержит метод, который запускается автоматически.

    Теперь тег start для элемента **Field** имеет указанный ниже вид.
    


  ```
  
<Field
       ID="{some_guid_here}"
       Name="OrientationStage"
       Title="OrientationStage"
       DisplayName="Orientation Stage"
       Description="The current orientation stage of the employee."
       Type="Choice"
       Required="TRUE"
       Group="Employee Orientation" 
       JSLink="~site/Scripts/OrientationStageRendering.js">
<!-- child elements and end tag omitted -->
  ```

10. Откройте страницу Default.aspx и добавьте указанный ниже код в качестве последнего дочернего элемента для элемента **asp:Content**, у которого **ContentPlaceHolderID** имеет значение **PlaceHolderMain**. 
    
  ```XML
  
<p><asp:HyperLink runat="server" NavigateUrl="JavaScript:window.location = _spPageContextInfo.webAbsoluteUrl + '/Lists/NewEmployeesInSeattle/AllItems.aspx';"
    Text="List View Page for New Employees in Seattle" /></p>

  ```


## Запуск и тестирование надстройки


  
    
    

1. Нажмите клавишу F5, чтобы развернуть и запустить надстройку. Visual Studio выполнит временную установку надстройки на тестовом сайте SharePoint и сразу же запустит ее. 
    
  
2. Настроенная вами отрисовка в клиенте влияет на отрисовку поля только на странице представления списка, а не на веб-часть представления списка, которую мы поместили на начальную страницу. Это связано с тем, что по умолчанию для веб-части используется отрисовка на сервере. Существуют способы изменить эту ситуацию, но они слишком сложны для данного простого примера. Поэтому чтобы увидеть, как работает отрисовка в клиенте, щелкните ссылку в нижней части страницы **List View Page for New Employees in Seattle** (Страница представления списка для новых сотрудников в Сиэтле).
    
  
3. Чтобы посмотреть, как работает настраиваемая отрисовка цвета, когда откроется страница представления списка, для некоторых элементов присвойте полю **Orientation Stage** (Этап вводного обучения) значение **Not Started** (Не начат), а для других  значение **Completed** (Завершен).
    
   **Список с настраиваемой отрисовкой на стороне клиента**

  

     ![Список новых сотрудников в Сиэтле, где в столбце "Этап адаптации" значения "Не начато" отмечены красным цветом, а значения "Завершено"  зеленым. Другие значения отмечены черным цветом.](images/dc8e2b7d-1747-4b65-aab4-6fc93c6867d4.PNG)
  

  

  
4. Чтобы завершить сеанс отладки, закройте окно браузера или остановите отладку в Visual Studio. При каждом нажатии клавиши F5 Visual Studio будет отзывать предыдущую версию надстройки и устанавливать ее последнюю версию.
    
  
5. Вы будете работать с этой надстройкой и решением Visual Studio и при изучении других статей, поэтому при перерывах в работе рекомендуется отзывать надстройку. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Отозвать**.
    
  

## 
<a name="Nextsteps"> </a>

В статье  [Создание настраиваемой кнопки ленты на хост-сайте надстройки SharePoint](create-a-custom-ribbon-button-in-the-host-web-of-a-sharepoint-add-in.md) этой серии вы добавите настраиваемый элемент меню и настраиваемую кнопку на ленту в надстройке SharePoint.
  
    
    

