---
title: Создание обработчика для события обновления в надстройках для SharePoint
ms.prod: SHAREPOINT
ms.assetid: 0fa088c5-54c6-482c-84ed-51c4f77c4127
---


# Создание обработчика для события обновления в надстройках для SharePoint
Создание и использование обработчика для события обновления Надстройка SharePoint.
## Необходимые условия для создания обработчика событий обновления надстройки
<a name="Prerequisites"> </a>

Внимательно ознакомьтесь со статьями  [Обработка событий надстройки](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) и [Обновление надстроек для SharePoint](update-sharepoint-add-ins.md), а также перечисленными в них необходимыми условиями и базовыми концепциями.
  
    
    

## Создание приемника UpgradedEventEndpoint
<a name="UpgradedEventEndpoint"> </a>


> [!Примечание]
> **Система нумерации версий.** Для согласованности в этом разделе предполагается, что версии надстройки имеют следующую нумерацию: 1.0.0.0, 2.0.0.0, 3.0.0.0 и т. д. Однако логика и инструкции применяются независимо от системы нумерации.
  
    
    

Для настраиваемой логики обновления можно создать приемник удаленных событий SharePoint, который обрабатывает событие обновления надстройки. Этот метод следует применять осторожно. Используйте его только для тех этапов обновления, которые невозможно обработать другим способом. Кроме того, инструкции из раздела  [Обработка событий надстройки](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents) применимы к событию обновления надстройки так же, как и к событиям установки и удаления. В частности:
  
    
    

- после выполнения обработчик должен возвращать в SharePoint состояние отмены или продолжения в течение 30 секунд. Если этого не произойдет, SharePoint будет повторно вызывать обработчик (до трех раз);
    
  
- если обработчик возвращает состояние отмены, SharePoint повторяет попытку до трех раз; 
    
  
- как правило, в обработчик необходимо включать логику отката и логику проверки выполненных действий.
    
  
Один из сценариев, при котором удобно использовать обработчик **UpgradedEventEndpoint**,  когда в удаленную базу данных добавляется вычисляемое поле. Предположим, добавляется столбец "Город" и его значение вычисляется из уже существующего столбца "Почтовый индекс". Ваш код в обработчике **UpgradedEventEndpoint** может итеративно перебирать существующие элементы в базе данных и вставлять значения в новые поля "Город" на основе значений в полях "Почтовый индекс" и внешнего источника данных, который сопоставляет индексы с городами. Изменения самой схемы базы данных лучше вносить за пределами обработчика **UpgradedEventEndpoint**, используя лучшие методики для определенной платформы базы данных. 
  
    
    
Подробнее о создании обработчика для события надстройки можно узнать в статьях  [Обработка событий в надстройках SharePoint](handle-events-in-sharepoint-add-ins.md) и [Создание приемника событий надстройки в надстройках для SharePoint](create-an-add-in-event-receiver-in-sharepoint-add-ins.md). В следующей процедуре предполагается, что вы добавили элемент приемника удаленных событий надстройки в свой проект Надстройка SharePoint в Visual Studio. 
  
    
    

### Обработка события обновления надстройки в первый раз


1. Откройте файл AppEventReceiver.svc.cs и добавьте в метод  [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) условную структуру, проверяющую, является ли событие, вызвавшее обработчик, событием обновления. Ваш код обновления добавляется внутрь этой структуры. Если коду нужно получать доступ к SharePoint, вы можете использовать интерфейс клиентской объектной модели управляемого кода SharePoint (CSOM) или REST. Место размещения условной структуры в методе зависит от того, как в нем структурирован другой код. Обычно оно сходно с аналогичными условными структурами, проверяющими события установки и удаления надстройки. Оно может находиться внутри условной структуры, которая проверяет определенные свойства или подсвойства объекта [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) , который передается в [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) для значений **null** или других значений, обозначающих недопустимые состояния, или внутри блока **try**. Ниже представлен пример структуры. Объект  _properties_  это объект [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
}

  ```

2. Чтобы использовать CSOM в обработчике, добавьте (внутри условного блока) блок **using**, который получает объект  [ClientContext](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.ClientContext.aspx) , вызывая метод **TokenHelper.CreateAppEventClientContext**. Чтобы получать доступ к веб-надстройке, задайте для второго параметра значение **true**. Для доступа к хост-сайту задайте значение **false**. Для одновременного использования обоих вариантов доступа требуется два отдельных объекта контекста клиента. 
    
  
3. Если обработчику нужен доступ к компонентам, расположенным не в SharePoint, поместите этот код за пределами всех блоков контекста клиента. Код должен иметь следующую структуру:
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, true))
    {
        // CSOM code that accesses the add-in web
    }
    using (ClientContext cc = TokenHelper.CreateAppEventClientContext(properties, false))
    {
        // CSOM code that accesses the host web
    }
    // Other update code
}

  ```

4. Для использования интерфейса REST ваш код с помощью других методов в классе **TokenHelper** получает маркер доступа, который потом включается в запросы, отправляемые им SharePoint. Подробнее см. в статье [Выполнение базовых операций с использованием конечных точек SharePoint 2013 REST](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md). Код должен иметь следующую структуру:
    
  ```cs
  
if (properties.EventType == SPRemoteEventType.AppUpgraded)
{
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);
    if (contextTokenString != null)
    {
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, 
                                                                                                                  Request.Url.Authority);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority)
                                   .AccessToken;

       // REST code that accesses SharePoint
    }  
    // Other update code
}

  ```

5. Для доступа к SharePoint вашему коду REST также должен передаваться URL-адрес хост-сайта, сайта надстройки или обоих этих сайтов. Эти URL-адреса являются подсвойствами объекта  [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) , передаваемого методу [ProcessEvent](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.IRemoteEventService.ProcessEvent.aspx) . Ниже показан код, который позволяет их получить.
    
  ```cs
  
Uri hostWebURL = properties.AppEventProperties.HostWebFullUrl;
Uri appWebURL = properties.AppEventProperties.AppWebFullUrl;
  ```

Когда вы обновляете надстройку во второй (или третий и т. д.) раз, может потребоваться сделать так, чтобы логика обновления не выполнялась много раз на одном и том же экземпляре надстройки. Ниже приведена процедура, позволяющая это сделать.
  
    
    

### Обработка события обновления надстройки при последующих обновлениях


1. Откройте файл AppEventReceiver.svc.cs, с помощью которого были реализованы действия по обновлению в первом обновлении (с 1.0.0.0. до 2.0.0.0). Назовем его кодом предыдущего обновления. Этот код обычно расположен после кода авторизации, который получает контекст клиента или маркеры доступа. Теперь при новом обновлении (с 2.0.0.0 до 3.0.0.0) в предыдущем коде, скорее всего, будут содержаться действия, которые не нужно запускать снова на том же экземпляре надстройки, но нужно запустить на экземпляре надстройки, не обновленной до версии 2.0.0.0 (в таком случае экземпляр сейчас обновляется с 1.0.0.0. до 3.0.0.0).
    
  
2. Поместите код предыдущего обновления в условную структуру, которая проверяет предыдущую версию экземпляра надстройки и выполняется, только если код в структуре еще не запускался для этого экземпляра надстройки. Предыдущая версия экземпляра хранится в подсвойстве  [PreviousVersion](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteAppEventProperties.PreviousVersion.aspx) объекта [SPRemoteEventProperties](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventProperties.aspx) .
    
  
3. Добавьте новую логику обновления (для обновления с 2.0.0.0 до 3.0.0.0) под этой структурой. Ниже приведен пример.
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 (previous update code) is here.
}
// Code to update from 2.0.0.0 to 3.0.0.0 is here.

  ```

4. Повторите эти шаги для каждого последующего обновления. Для обновления с 3.0.0.0 до 4.0.0.0 код должен иметь следующую структуру.
    
  ```cs
  
Version ver2OOO = new Version("2.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver2OOO)
{
    // Code to update from 1.0.0.0 to 2.0.0.0 is here.
}

Version ver3OOO = new Version("3.0.0.0");
if (properties.AppEventProperties.PreviousVersion < ver3OOO)
{
    // Code to update from 2.0.0.0 to 3.0.0.0 (previous update code) is here.
}
// Code to update from 3.0.0.0 to 4.0.0.0 is here.

  ```


> [!Важно!]
> Если вы добавляете компонент в надстройку в обработчике **UpgradedEventEndpoint**, обязательно добавьте такой же код в обработчик **InstalledEventEndpoint**, потому что этот компонент также нужно включить в надстройку в новой установке. Кроме того, необходимо добавить обработчик  [UninstallingEventEndpoint](http://msdn.microsoft.com/library/4194e44b-f2af-1db4-aad5-9b7b511b4348%28Office.15%29.aspx) (или изменить его), чтобы надстройка удалила компонент. По большей части, все добавления и изменения, внесенные обработчиком **InstalledEventEndpoint**, отменяются или удаляются с помощью обработчика **UninstallingEventEndpoint**. Единственное исключение состоит в том, что данные, которые будут необходимы после удаления надстройки со второго уровня корзины, не должны быть удалены. Данными также считаются созданные надстройкой веб-сайты, за исключением сайта надстройки. 
  
    
    


## Добавление логики отката в обработчик
<a name="AddRollbackLogic"> </a>

В случае возникновения ошибки при обновлении надстройки инфраструктура SharePoint 2013 останавливает процесс обновления и выполняет откат экземпляра надстройки вместе со всеми его компонентами до предыдущей версии. Но инфраструктура не получает сведений о действиях логики обработчика **UpgradedEventEndpoint**, поэтому не всегда может ее отменить. Необработанное исключение при выполнении обработчика **UpgradedEventEndpoint** почти всегда означает, что необходимо сделать откат всего процесса обновления надстройки. Но этого не происходит, потому что инфраструктура не имеет возможности отменить некоторые действия, выполненные кодом **UpgradedEventEndpoint**. Поэтому код **UpgradedEventEndpoint** должен выполнять следующие задачи:
  
    
    

1. перехватывать все исключения;
    
  
2. переходить к выполнению настроенного кода отката для отмены всех действий, выполненных до текущего момента;
    
  
3. сообщать инфраструктуре SharePoint 2013, что необходимо сделать откат всего обновления надстройки;
    
  
4. передавать инфраструктуре сообщение об ошибке.
    
  
Не все действия, выполняемые обработчиком **UpgradedEventEndpoint**, необходимо явно обращать внутри обработчика. Откат сайта надстройки будет выполнен инфраструктурой, поэтому вашему коду не нужно отменять изменения в нем. Но обработчик **UpgradedEventEndpoint**, как правило, должен обратить изменения, внесенные им в хост-сайт или другие компоненты, которые являются внешними относительно Надстройка SharePoint. 
  
    
    
При втором (или третьем и т. д.) обновлении ваша логика обработки обновлений должна учитывать, что обновляемый экземпляр надстройки не обязательно является непосредственно предыдущей версией. Если это не так, может понадобиться два или больше блоков кода обновления, которые нужно обратить. Для этого требуется условная логика, основанная на номере предыдущей версии. Ниже приведен пример логики отката для обновления до версии 4.0.0.0.
  
    
    



```cs

catch (Exception e)
{ 
    // Rollback of the 3.0.0.0 to 4.0.0.0 update logic goes here.

    Version ver3OOO = new Version("3.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver3OOO)
    {
        // Rollback of the 2.0.0.0 to 3.0.0.0 update logic goes here.
    }

    Version ver2OOO = new Version("2.0.0.0");
    if (properties.AppEventProperties.PreviousVersion < ver2OOO)
    {
        // Rollback of the 1.0.0.0 to 2.0.0.0 update logic goes here.
    }
}
```

На последнем этапе обработки ошибки необходимо назначить сообщение об ошибке и состояние отмены объекту  [SPRemoteEventResult](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.aspx) , который возвращается методом **ProcessEvent** инфраструктуре обновления SharePoint 2013. Ниже приведен пример. В данном коде _result_  это объект **SPRemoteEventResult**, который был объявлен ранее в методе **ProcessEvent**.
  
    
    



```cs

catch (Exception e)
{     
    result.ErrorMessage = "custom message " + e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;

     // Rollback logic from the preceding code snippet  is here. 

}
```


> [!Важно!]
> Очень важно назначить **SPRemoteEventServiceStatus.CancelWithError** (или **SPRemoteEventServiceStatus.CancelNoError**) свойству  [Status](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.EventReceivers.SPRemoteEventResult.Status.aspx) . Это свойство сообщает инфраструктуре, что нужно сделать откат обновления. Тем не менее, SharePoint повторит попытку запуска обработчика еще три раза, прежде чем выполнять откат.
  
    
    


### Использование стратегии делегирования обработчиков

Стратегию делегирования обработчиков, описанную в разделе  [Обработка событий надстройки](handle-events-in-sharepoint-add-ins.md#HandlingAppEvents), можно применять и в **UpdatedEventHandler**. На удаленном компьютере пакетно выполняются не только логика отката и логика проверки выполненных действий, но и логика управления версиями. Ограничения для стратегий применяются и здесь: как правило, эту логику невозможно использовать для обновления нескольких удаленных систем.
  
    
    

## Дальнейшие действия
<a name="Next"> </a>

Вернитесь к разделу  [Основные действия при обновлении надстройки](update-sharepoint-add-ins.md#MajorAppUpgradeSteps) или перейдите непосредственно к одной из следующих статей, чтобы узнать, как обновить следующий важный компонент надстройки SharePoint.
  
    
    

-  [Обновление веб-компонентов надстройки в SharePoint 2013](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [Обновление веб-компонентов узла в SharePoint 2013](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [Обновление удаленных компонентов в надстройках для SharePoint](update-remote-components-in-sharepoint-add-ins.md)
    
  

## Дополнительные ресурсы
<a name="bk_addresources"> </a>


-  [Обновление надстроек для SharePoint](update-sharepoint-add-ins.md)
    
  
-  [Элемент UpgradedEventEndpoint (PropertiesDefinition complexType) (Manifest SharePoint Add-in)](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx)
    
  
-  [Обработка событий в надстройках SharePoint](handle-events-in-sharepoint-add-ins.md)
    
  
-  [Создание приемника событий надстройки в надстройках для SharePoint](create-an-add-in-event-receiver-in-sharepoint-add-ins.md)
    
  

