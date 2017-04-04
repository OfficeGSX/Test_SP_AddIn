---
title: Добавление веб-части на страницу сайта надстройки
ms.prod: SHAREPOINT
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# Добавление веб-части на страницу сайта надстройки
Узнайте, как добавить веб-часть на страницу в надстройке SharePoint.
Можно добавить уже готовую веб-часть на страницу веб-надстройки надстройки SharePoint. Важно сделать это так, чтобы не возникли проблемы, если потребуется обновить надстройку.
  
    
    

Пример кода, иллюстрирующий рекомендации этой статьи, находится на веб-странице  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## Необходимые условия

Подробнее о необходимых условиях см. в разделе  [Знакомство с созданием надстроек SharePoint с размещением в SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
  
    
    

## Добавление веб-части на страницу


  
    
    

1. Создание проекта надстройки SharePoint, размещенного в SharePoint, в Visual Studio. Подробнее см. в разделе  [Знакомство с созданием надстроек SharePoint с размещением в SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md).
    
  
2. Откройте ASPX-файл, к которому следует добавить веб-часть. В этой статье в качестве примера используется Default.aspx. 
    
  
3. Добавьте **WebPartZone** в элемент **<asp:Content>**, где будет размещена веб-часть со следующей разметкой. Обычно добавляют в **<asp:Content>** (в этом случае для **ContentPlaceHolderId** используется `PlaceHolderMain`). Ниже приведен пример.
    
  ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

  ```


    > **Внимание!**
      > Можно добавить элемент веб-части, например **<WebPartPages:XsltListViewWebPart>**, как дочерний для элемента **WebPartZone**. Но мы не рекомендуем это делать в надстройке SharePoint. Если требуется обновить надстройку, элемент веб-части, вставленный в ASPX-файл, может вызвать сбой в некоторых сценариях обновления. В этом случае отобразится сообщение: "Веб-часть с таким идентификатором уже добавлена на эту страницу". Рекомендуем добавить веб-части в манифест элементов страницы, как описано ниже. 
4. Откройте файл манифеста элементов для страницы. Обычно это файл с именем elements.xml, размещенный в той же папке проекта, что и ASPX-файл.
    
  
5. В элементе **File** для страницы добавьте дочерний элемент **AllUsersWebPart** и задайте его атрибуту **WebPartZoneID** значение зоны веб-частей, созданной на странице. Ниже приведен пример.
    
  ```
  
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <Module Name="Pages">
    <File Path="Pages\\Default.aspx" Url="Pages/Default.aspx" ReplaceContent="TRUE" >
      <AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">

      </AllUsersWebPart>
    </File>
  </Module>
</Elements>

  ```

6. Добавьте элемент **CDATA** в качестве дочернего для элемента **AllUsersWebPart**. Затем добавьте элемент **webParts** в качестве дочернего для элемента **CDATA**. Ниже приведен пример. 
    
  ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
  ```

7. Добавьте разметку **webPart** как дочерний объект элемента **webParts**. Ниже приведен пример добавления **XsltListViewWebPart**. Предполагается, что настраиваемый список с именем Test List  это часть того же проекта надстройки. Подробнее о добавлении настраиваемого списка в веб-надстройку см. в разделе  [Создание надстроек с размещением у поставщика, которые включают в себя пользовательский список SharePoint и тип контента](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md). 
    
    > **Примечание**
      >  Обратите внимание, что веб-часть не имеет свойства идентификатора. Рекомендуем включать явный идентификатор веб-части только в двух случаях, когда это действительно необходимо:>  Эта веб-часть добавляется на вики-страницу SharePoint.>  Эта веб-часть  одна из двух или более веб-частей, которые будут подключены.

  ```
  
<webParts>
  <webPart xmlns="http://schemas.microsoft.com/WebPart/v3">
    <metaData>
      <type name="Microsoft.SharePoint.WebPartPages.XsltListViewWebPart, 
                   Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, 
                   PublicKeyToken=71e9bce111e9429c" />
    </metaData>
    <data>
      <properties>
        <property name="ListUrl">Lists/{TestList}</property>
        <property name="IsIncluded">True</property>
        <property name="NoDefaultStyle">True</property>
        <property name="Title">{Test List}</property>
        <property name="PageType">PAGE_NORMALVIEW</property>
        <property name="Default">False</property>
        <property name="ViewContentTypeId">0x</property>
      </properties>
    </data>
  </webPart>
</webParts>
  ```

8. Нажмите клавишу F5 для отладки надстройки. Веб-часть должна отобразиться на странице.
    
  

