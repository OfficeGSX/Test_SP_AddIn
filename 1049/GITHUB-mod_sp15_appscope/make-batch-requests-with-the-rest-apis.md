---
title: Создание пакетного запроса с помощью интерфейсов REST API
ms.prod: OFFICE365
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# Создание пакетного запроса с помощью интерфейсов REST API
Узнайте, как использовать параметр запроса  `$batch` с интерфейсами REST/OData API.
В этой статье описано, как вы можете пакетно обрабатывать запросы и операции по отношению к интерфейсу REST/OData API для Microsoft SharePoint Online (а также локальной версии SharePoint 2016 и более поздних выпусков) и  [подмножество файлов и папок](http://msdn.microsoft.com/ru-ru/office/office365/api/files-rest-operations) для интерфейсов Office 365 REST API. Благодаря этой технике вы можете повысить производительность своей надстройки, сочетая множество операций в одном запросе к серверу и получая один ответ.
  
    
    


## Краткие сведения о параметре $batch

SharePoint Online (а также локальная версия SharePoint 2016 и последующие выпуски) и интерфейсы Интерфейсы API Office 365 выполняют параметр запроса OData  `$batch`, поэтому подробную информацию о его использовании можно найти в  [официальной документации](http://www.odata.org/documentation/odata-version-3-0/batch-processing). См. также блог Эндрю Коннелла (Andrew Connell) по этой теме, начиная с записи  [Часть 1. Пакетная обработка интерфейсов API в SharePoint REST](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests). Далее приведены лишь основные пункты.
  
    
    

- URL-адрес запроса состоит из URL-адреса корневой службы и параметра  `$batch`, например  `https://fabrikam.sharepoint.com/_api/$batch` или `https://fabrikam.office365.com/api/v1.0/me/$batch`.
    
  
- Текст HTTP-запроса  это MIME-тип  *multipart/mixed*  .
    
  
- Текст запроса делится на части, отделенные друг от друга граничной строкой, которая указывается в заголовке запроса.
    
  
- Каждая часть имеет собственную HTTP-команду и URL-адрес REST, а также собственный внутренний текст, где это необходимо.
    
  
- Часть может быть операцией чтения (или вызовом функции) или набором изменений из одной или нескольких операций (или функций вызова). Набор изменений сам является MIME-типом  *multipart/mixed*  с подразделами, которые содержат операции вставки, обновления или удаления.
    
    > **Важно!**
      > В это время SharePoint и интерфейсы Интерфейсы API Office 365 не поддерживают функциональность "все или ничего" для наборов изменений, которые имеют более одной операции. В случае сбоя дочерних операций, остальные операции по-прежнему выполняются и не откатываются. 

## Примеры кода

Примеры кода, которые используют параметр запроса  `$batch`по отношению к API SharePoint REST/OData: 
  
    
    

- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)
    
  
- **JavaScript:** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)
    
  

## Пример запросов и ответов

Ниже приведен пример необработанного HTTP-запроса, который пакетно обрабатывает две операции GET, которые извлекают названия всех элементов в двух разных списках.
  
    
    
```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Host: fabrikam.sharepoint.com
Content-Length: 527
Expect: 100-continue

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('Composed%20Looks')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('User%20Information%20List')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1--
```

Ниже приведен пример текста необработанного HTTP-запроса, который пакетно обрабатывает операцию DELETE для списка и операцию GET для списка списков SharePoint.
  
    
    


```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Host: fabrikam.sharepoint.com
Content-Length: 647
Expect: 100-continue

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: multipart/mixed; boundary=changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d
Content-Type: application/http
Content-Transfer-Encoding: binary

DELETE https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('OldList') HTTP/1.1
If-Match: "1"

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d--
--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists HTTP/1.1

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e--```


## Ссылки на полезные библиотеки

Существуют библиотеки OData, поддерживающие пакетную обработку OData для различных языков. Ниже представлены два примера. С полным списком можно ознакомиться на странице  [Библиотеки OData](http://www.odata.org/libraries/).
  
    
    

-  [Библиотека OData для .NET](http://msdn.microsoft.com/ru-ru/office/microsoft.data.odata%28v=vs.90%29). Обратите особое внимание на классы **ODataBatch***.
    
  
-  [Библиотека Datajs](http://datajs.codeplex.com/documentation). Обратите особое внимание на  [Пакетные операции](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch).
    
  

