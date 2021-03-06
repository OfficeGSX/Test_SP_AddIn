---
title: Обработка событий надстройки в надстройке, размещаемой у поставщика
ms.prod: SHAREPOINT
ms.assetid: d5679867-083f-46c8-a2bd-00a43f042c24
---


# Обработка событий надстройки в надстройке, размещаемой у поставщика
В данной статье рассказывается, как настроить установку Надстройка SharePoint, размещаемой у поставщика.
Это седьмая часть из серии статей о том, как разрабатывать Надстройки SharePoint, размещаемые у поставщика. Сначала вам необходимо ознакомиться со статьей  [Надстройки SharePoint](sharepoint-add-ins.md) и с указанными ниже предыдущими статьями серии.





-  [Знакомство с созданием надстроек SharePoint с размещением у поставщика](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Придание надстройке, размещаемой у поставщика, внешнего вида и удобства использования SharePoint](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [Включение настраиваемой кнопки в надстройку, размещаемую у поставщика](include-a-custom-button-in-the-provider-hosted-add-in.md)


-  [Краткий обзор объектной модели SharePoint](get-a-quick-overview-of-the-sharepoint-object-model.md)


-  [Добавление операций записи SharePoint в надстройку, размещаемую у поставщика](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)


-  [Включение веб-части надстройки в надстройку, размещаемую у поставщика](include-an-add-in-part-in-the-provider-hosted-add-in.md)



> **Примечание**
> Если вы изучали предыдущие статьи этой серии о надстройках, размещаемых у поставщика, то у вас уже есть решение для Visual Studio, которое можно использовать для работы с данной статьей. Кроме того, вы можете скачать репозиторий  [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) и открыть файл BeforeAdd-inEventHandlers.sln.




В данной статье мы настроим в SharePoint обработку событий, называемых событиями надстройки. Точнее мы создадим обработчики событий установки и удаления надстройки. Кроме того, настраиваемые обработчики можно использовать и для событий списка и элементов списка. О таких обработчиках вы узнаете в одной из следующих статей этой серии. Все эти события создаются в SharePoint, но пользовательский код, обрабатывающий каждое событие, находится в вашем удаленном веб-приложении. Вы настроите SharePoint, чтобы он вызывал настраиваемый обработчик, зарегистрировав URL-адрес обработчика для события SharePoint.
## Два места для программного развертывания компонентов SharePoint

Мы хотим, чтобы наша надстройка Chain Store автоматически создавала и развертывала списки **Local Employees** (Местные сотрудники) и **Expected Shipments** (Ожидаемые отгрузки). Надстройка может в любое время развертывать компоненты SharePoint, например настраиваемые списки. Тем не менее когда надстройка зависит от определенного компонента, например настраиваемого списка, этот компонент необходимо развернуть *до того как*  пользователи начнут работать с надстройкой. Для таких критически важных компонентов пользовательский код, выполняющий развертывание, можно разместить в двух местах:




- в обработчике события установки надстройки;


- в коде, выполняемом при первом запуске надстройки в SharePoint.


Описание процесса принятия решения о том, какой способ лучше подходит для конкретной надстройки, выходит за рамки данной статьи. В нашей статье мы приведем только несколько соображений, оказывающих влияние на выбор.




- Настраиваемый обработчик события установки должен выполняться в течение не более 30 секунд. Для кода, выполняемого при первом запуске, таких ограничений нет.


- Если в процессе установки надстройки что-то пойдет не так, SharePoint отменит все действия, которые он выполнил в ходе установки. Настраиваемый обработчик события установки запускается  *после*  того как SharePoint выполнит все действия, необходимые для установки надстройки, поэтому обработчик может принять участие в работе этой системы. Например, если ваш пользовательский код создал исключение, вы можете сообщить SharePoint, что необходимо полностью отменить установку надстройки. Если же что-то пойдет не так в пользовательском коде, выполняемом при первом запуске, надстройка останется установленной и, возможно, будет работать неправильно.


- Если SharePoint придется отменить установку надстройки, он не "успокоится" на этом. Он сразу же попытается установить надстройку еще раз. Он совершает до 4 попыток (на каждую попытку выделяется 30 секунд). При каждой повторной попытке настраиваемый обработчик события установки запускается  *с самого начала*  . Если обработчик установил, например, список, прежде чем были отменены все действия по установке, то он будет пытаться установить этот же список при каждой новой попытке установки надстройки. Чтобы не допустить этого, необходимо написать код обработчика события установки так, чтобы он не выполнял никаких действий (например, развертывание компонента), прежде чем проверит, не было ли выполнено это действие ранее. Из-за этого код обработчика события установки сложнее кода, выполняемого при первом запуске, так как последний не выполняется повторно (если, конечно, вы намеренно не запрограммируете повторное выполнение). Кроме того, для проверки того, не развернут ли какой-либо компонент, необходимо выполнить затратный по времени вызов через Интернет от удаленного обработчика к SharePoint. После этого понадобится выполнить второй вызов для развертывания компонента (если он не был развернут ранее).


В надстройке Chain Store мы объединим эти стратегии. В данной статье вы создадите обработчик события установки, который будет регистрировать хост-сайт в качестве клиента в корпоративной базе данных, а затем задавать сигнал о том, была ли уже запущена надстройка на хост-сайте. В одной из следующих статей серии вы добавите код, выполняемый при первом запуске, в метод **Page_Load** начальной страницы надстройки. Этот код будет развертывать два настраиваемых списка и выполнять ряд других действий.




## Настройка решения для отладки приемника событий
<a name="RERDebug"> </a>

Для отладки приемников событий необходимо использовать шину обслуживания Azure. Следуйте инструкциям в статье  [Устранение неполадок и отладка удаленного приемника событий в надстройке для SharePoint](debug-and-troubleshoot-a-remote-event-receiver-in-a-sharepoint-add-in.md). Так как в качестве тестового сайта вы используете веб-сайт SharePoint Online, не забудьте выполнить эти инструкции на удаленном тестовом сайте. В следующих статьях серии подразумевается, что вы успешно настроили среду отладки. 




## Создание обработчика события установки
<a name="RERDebug"> </a>






> **Примечание**
>  При повторном открытии решения параметры раздела "Начальные проекты" в Visual Studio обычно сбрасываются к значениям, используемым по умолчанию. Сразу же после повторного открытия примера решения в этой серии статей всегда выполняйте указанные ниже действия.<BR />  В верхней части **обозревателя решений** щелкните правой кнопкой мыши узел решения и выберите **Назначить запускаемые проекты**. <BR />  Убедитесь, что в столбце **Действие** для всех трех проектов указано значение **Запускать**. 





1. В **обозревателе решений** выберите проект **ChainStore**, чтобы его свойства отобразились на панели **Свойства** в Visual Studio.


2. Присвойте параметру **Управление установленной надстройкой** значение **True**. (Этот параметр может по-прежнему называться **Управление установленными приложениями**.) Благодаря этому значению будут выполнены два указанных ниже действия.

  - В проекте **ChainStoreWeb** (но не в проекте **ChainStore**) будет создана папка **Services** (Службы), в которую будут добавлены два файла: файл AppEventReceiver.svc и его файл кода программной части AppEventReceiver.svc.cs. (Имена файлов начинаются со строки App, так как ранее надстройки назывались приложениями. *Не переименовывайте эти файлы.*  Для правильной работы Инструменты разработчика Office для Visual Studio необходимо, чтобы файлы имели именно эти имена.)


  - URL-адрес обработчика регистрируется в манифесте надстройки. Эта часть манифеста не отображается в конструкторе манифеста. Чтобы отобразить ее, щелкните правой кнопкой мыши файл AppManifest.xml и выберите пункт **Перейти к коду**. Здесь имеется новый дочерний элемент элемента **Свойства**, который выглядит, как показано ниже. Эта разметка сообщает SharePoint, что когда он закончит всю работу по установке надстройки, необходимо будет вызвать метод **ProcessEvent** этой службы. Настраиваемый обработчик это последний компонент, запускаемый в процессе установки. Строка `~remoteAppUrl` это заполнитель, который Инструменты разработчика Office для Visual Studio заменит URL-адресом узла службы. При отладке в качестве этого URL-адреса используется URL-адрес шины обслуживания Azure. Соответственно, при создании пакета для развертывания в рабочей среде используется URL-адрес рабочей среды.

 ```XML

<InstalledEventEndpoint>~remoteAppUrl/Services/AppEventReceiver.svc</InstalledEventEndpoint>
 ```

3. Откройте файл AppEventReceiver.svc.cs.


4. Вы увидите, что Инструменты разработчика Office для Visual Studio создал пример реализации метода **ProcessEvent**. Все реализации этого метода начинаются с инициализации объекта **SPRemoteEventResult** и заканчиваются возвращением этого объекта в SharePoint. Помимо прочего, этот объект сообщает SharePoint, следует ли откатить событие из-за сбоя кода настраиваемого обработчика. Кроме того, возможно, что пакет средств включил в этот метод блок **using**, который создает объект **ClientContext**. Настраиваемый обработчик в надстройке Chain Store не будет делать обратный вызов в SharePoint, поэтому удалите этот блок. Теперь этот метод должен иметь указанный ниже вид.

 ```cs
  public SPRemoteEventResult ProcessEvent(SPRemoteEventProperties properties)
{
    SPRemoteEventResult result = new SPRemoteEventResult();


    return result;
}
 ```

5. Приемник событий можно вызвать с помощью любого из трех возможных событий надстройки, поэтому добавьте указанную ниже структуру **switch** в метод **ProcessEvent** между строками, в которых выполняется создание и возвращение объекта `result`. В именах событий имеется строка App (Приложение), так как ранее надстройки назывались приложениями.

 ```cs

switch (properties.EventType)
{
    case SPRemoteEventType.AppInstalled:

        // TODO2: Custom installation logic goes here.

        break;
    case SPRemoteEventType.AppUpgraded:
        // This sample does not implement an add-in upgrade handler.
        break;
    case SPRemoteEventType.AppUninstalling:

        // TODO3: Custom uninstallation logic goes here.     
     
        break;
}
 ```

6. Для регистрации магазина в Гонконге в качестве клиента в удаленном веб-приложении наш код, выполняемый во время установки, должен вызывать хранимую процедуру SQL. Очень важно, чтобы при сбое процесса обработчик сообщил SharePoint, что необходимо откатить установку надстройки. Поэтому добавьте указанный ниже блок **try/catch** вместо `TODO2`. Обратите внимание на указанные ниже особенности этого кода.

  - Вы создадите объект  `tenantName` и метод `CreateTenant` на одном из следующих этапов.


  - Свойство **Status** объекта **SPRemoteEventResult** может иметь три одно из трех следующих значений: **Continue** (используется по умолчанию), **CancelNoError** и **CancelWithError**. Два последних значения сообщают SharePoint, что необходимо откатить событие.



 ```cs

try
{
    CreateTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
 ```

7. URL-адрес хост-сайта, представляющий собой дискриминатор клиента в примере, включается в сведения, которые SharePoint передает в приемник в параметре **SPRemoteEventProperties**. Добавьте указанную ниже строку в метод **ProcessEvent** сразу же после блока инициализации объекта **SPRemoteEventResult**.

 ```cs

string tenantName = properties.AppEventProperties.HostWebFullUrl.ToString();
 ```

8. Теперь в нашем коде придется учесть небольшую особенность свойства **AppEventProperties.HostWebFullUrl**. В большинстве других контекстов SharePoint добавляет закрывающий символ косой черты / в конец URL-адреса хост-сайта, поэтому в нашем примере кода предполагается, что этот символ присутствует. Но SharePoint добавляет этот символ в конец значения **HostWebFullUrl** тогда и только тогда, когда хост-сайт является сайтом верхнего уровня семейства веб-сайтов. Так как наш веб-сайт магазина в Гонконге представляет собой подсайт в семействе веб-сайтов, то нам потребуется добавить этот символ, чтобы во всем примере использовалось одно и то же имя клиента. Добавьте указанный ниже код сразу после блока инициализации объекта `tenantName`.

 ```cs
  if (!tenantName.EndsWith("/"))
{
    tenantName += "/";
}
 ```

9. Добавьте указанные ниже операторы **using** в начало файла.

 ```

using System.Data.SqlClient;
using System.Data;
using ChainStoreWeb.Utilities;
 ```

10. Добавьте указанный ниже метод в класс  `AppEventReceiver`. Мы не будем подробно рассматривать его, так как цель этой серии статей научить вас программированию надстроек SharePoint, а не программированию для SQL Server или Azure. 

    Этот метод будет создавать строку в таблице **Tenants** (Клиенты) базы данных. Помимо столбца **Name** (Имя) в таблице имеется столбец **Version** (Версия) со значением0000.0000.0000.0000, используемым по умолчанию. В одной из следующих статей этой серии вы создадите код, выполняемый во время первого запуска, который будет использовать это значение для определения того, была ли установлена надстройка на хост-сайте. Если номер версии равен 0000.0000.0000.0000, то ваш код развернет списки **Local Employees** (Местные сотрудники) и **Expected Shipments** (Ожидаемые отгрузки), а затем увеличит номер версии.



 ```cs

private void CreateTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of installation.

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "AddTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();
    }//dispose conn and cmd
}
 ```


## Создание обработчика события удаления
<a name="RERDebug"> </a>

В случаях, когда обрабатывается событие установки, обычно рекомендуется обрабатывать и событие удаления. Основная причина этого состоит в том, что обработчик события удаления должен перемещать в корзину или удалять объекты, установленные обработчиком события установки. Тем не менее существует много исключений, поэтому вам необходимо хорошо понимать варианты использования вашей надстройки. Например, в списке, который был развернут и заполнен надстройкой, могут остаться значения даже после ее удаления, при этом вам может быть потребоваться, чтобы обработчик события удаления не удалял этот список. 



Если пользователь удаляет надстройку со страницы **Site Contents** (Содержание сайта), то событие удаления не создается (как можно было бы ожидать). В этом случае выполняется только перемещение надстройки в корзину веб-сайта. Пользователь может восстановить ее, но при восстановлении не будет перезапущен обработчик события установки. Поэтому при восстановлении надстройки вам может потребоваться, чтобы по-прежнему существовали все объекты, развернутые обработчиком события установки. Компоненты SharePoint можно переместить из корзины во вторую корзину. Событие удаления будет создано только тогда, когда надстройка будет удалена из второй корзины. Если пользователь сделает это, то надстройка будет безвозвратно удалена, поэтому в этот момент нам необходимо будет удалить тенантность магазина в Гонконге из корпоративной базы данных.




1. Присвойте параметру **Управление удалением надстроек** значение **True**. (Этот параметр может по-прежнему называться **Управление удалением приложений**.) Благодаря этому обработчик будет зарегистрирован в файле AppManifest.xml точно так же, как ранее был зарегистрирован обработчик события установки. Если вы посмотрите содержимое файла, то увидите, что у них абсолютно одинаковые URL-адреса. Инструменты разработчика Office для Visual Studio предполагает, что вы используете один и тот же SVC-файл. Мы делаем это в данном примере, кроме того, это является стандартной рекомендацией. 


2. В файле AppEventReceiver.svc.cs добавьте указанный ниже код вместо  `TODO3`. Обратите внимание на указанные ниже особенности этого кода. 

  - Метод  `DeleteTenant` будет добавлен на следующем этапе.


  - В результате отката удаления надстройки она останется во второй корзине, из которой ее по-прежнему можно будет восстановить.



 ```cs

try
{
    DeleteTenant(tenantName);
 }
catch (Exception e)
{
     // Tell SharePoint to cancel and roll back the event.
    result.ErrorMessage = e.Message;
    result.Status = SPRemoteEventServiceStatus.CancelWithError;
}
 ```

3. Добавьте указанный ниже метод в класс  `AppEventReceiver`.

 ```cs

private void DeleteTenant(string tenantName)
{
    // Do not catch exceptions. Allow them to bubble up and trigger roll back
    // of un-installation (removal from 2nd level Recycle Bin).

    using (SqlConnection conn = SQLAzureUtilities.GetActiveSqlConnection())
    using (SqlCommand cmd = conn.CreateCommand())
    {
        conn.Open();
        cmd.CommandText = "RemoveTenant";
        cmd.CommandType = CommandType.StoredProcedure;
        SqlParameter name = cmd.Parameters.Add("@Name", SqlDbType.NVarChar);
        name.Value = tenantName;
        cmd.ExecuteNonQuery();            
    }//dispose conn and cmd
}
 ```


> **Примечание**
> В предыдущей статье этой серии вы настроили проект так, чтобы при каждом нажатии клавиши F5 выполнялась перестройка корпоративной базы данных. В результате такой операции выполняется очистка таблицы **Tenants** (Клиенты).





## Запуск надстройки и тестирование обработчика события установки
<a name="RERDebug"> </a>


1. Нажмите клавишу F5, чтобы развернуть и запустить вашу надстройку. Visual Studio размещает удаленное веб-приложение в IIS Express, а базу данных SQL в SQL Express. Кроме того, он создает временную установку надстройки на вашем тестовом сайте SharePoint, запускает обработчик события установки и немедленно запускает надстройку. Прежде чем откроется начальная страница надстройки, вам будет предложено предоставить надстройке необходимые разрешения. 


2. Когда откроется начальная страница надстройки, щелкните по значку с изображением шестеренки на расположенном вверху элементе управления хрома и выберите **Параметры учетной записи**.


3. На странице **Учетные записи** нажмите кнопку **Показать версию надстройки**. Будет отображен номер версии 0000.0000.0000.0000.

![Страница "Учетные записи" с заголовком "Параметры учетной записи". Под кнопкой "Показать версию надстройки" находится строка "Зарегистрированная версия: ноль ноль ноль ноль точка ноль ноль ноль ноль точка ноль ноль ноль ноль точка ноль ноль ноль ноль".](images/2a905b7d-89c7-456a-8456-21a9b7e9efc5.PNG)





4. Чтобы завершить сеанс отладки, закройте окно браузера или остановите отладку в Visual Studio. При каждом нажатии клавиши F5 Visual Studio будет отзывать предыдущую версию надстройки и устанавливать ее последнюю версию.


5. Вы будете работать с этой надстройкой и решением Visual Studio и при изучении других статей, поэтому при перерывах в работе рекомендуется отзывать надстройку. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Отозвать**.



## 
<a name="Nextsteps"> </a>

 В следующей статье этой серии вы добавите в надстройку код, выполняемый при первом запуске, для автоматического развертывания списка **Local Employees** (Местные сотрудники) и настраиваемой кнопки ленты: [Добавление кода, выполняемого при первом запуске, в надстройку, размещаемую у поставщика](add-first-run-logic-to-the-provider-hosted-add-in.md).




