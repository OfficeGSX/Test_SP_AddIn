---
title: Realizar solicitudes de lote con las API de REST
ms.prod: OFFICE365
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# Realizar solicitudes de lote con las API de REST
Aprenda a usar la opción de consulta  `$batch` con las API de REST/OData.
En este artículo se describe cómo se pueden procesar en lote consultas y operaciones contra la API de REST/OData de Microsoft SharePoint Online (y SharePoint 2016 local o versiones posteriores) y el  [Subconjunto de archivos y carpetas](http://msdn.microsoft.com/es-es/office/office365/api/files-rest-operations) de las API de REST de Office 365. Con esta técnica, puede mejorar el rendimiento del complemento al combinar muchas operaciones en una sola solicitud al servidor y una sola respuesta.
  
    
    


## Resumen ejecutivo de la opción $batch

SharePoint Online (y SharePoint 2016 local o versiones posteriores) y las API de Office 365 implementan la opción de consulta  `$batch` de OData de modo que puede confiar en [la documentación oficial](http://www.odata.org/documentation/odata-version-3-0/batch-processing) con respecto a los detalles sobre modo de uso. (Otra opción es ver las publicaciones del blog de Andrew Connell sobre el tema comenzando desde la [parte 1 sobre el procesamiento por lotes de la API de REST de SharePoint](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests)). A continuación, se presenta solo un recordatorio de los puntos más importantes:
  
    
    

- La dirección URL de solicitud consta de la dirección URL de servicio de raíz y la opción  `$batch`. Por ejemplo:  `https://fabrikam.sharepoint.com/_api/$batch` o `https://fabrikam.office365.com/api/v1.0/me/$batch`.
    
  
- El cuerpo de la solicitud HTTP es de tipo MIME  *de varias partes/mixta*  .
    
  
- El cuerpo de la solicitud está dividido en partes separadas unas de otras por una cadena de límite que se especifica en el encabezado de la solicitud.
    
  
- Cada parte del cuerpo tiene su propio verbo HTTP y su propia dirección URL REST (cuando corresponde, también tiene su propio cuerpo interno).
    
  
- Una parte puede ser una operación de lectura (o invocación a función), o un ChangeSet de una o más operaciones de escritura (o invocaciones a funciones). Un ChangeSet es en sí mismo un tipo MIME  *de varias partes/mixta*  con subpartes que contienen operaciones para insertar, actualizar o eliminar.
    
    > [!IMPORTANTE]
      > En este momento, SharePoint y las API de Office 365 no admiten la funcionalidad "todo o nada" para ChangeSets que tienen más de una operación dentro. Si alguna de las operaciones secundarias falla, las otras aún se completan y no se revierten. 

## Ejemplos de código

Ejemplos de código que usa la opción de consulta  `$batch` en las API de REST/OData de SharePoint:
  
    
    

- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)
    
  
- **JavaScript:** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)
    
  

## Solicitudes de ejemplo y respuestas

A continuación, se presenta un ejemplo de una solicitud HTTP sin procesar que procesa en lote dos operaciones GET que recuperan los títulos de todos los elementos de dos listas diferentes.
  
    
    

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

A continuación, se presenta un ejemplo del cuerpo de una solicitud HTTP sin procesar que procesa en lote una DELETE de una lista y una GET de la lista de listas de SharePoint.
  
    
    



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

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e--
```


## Vínculos a bibliotecas útiles

Existen bibliotecas de OData que admiten el procesamiento en lote de OData para muchos idiomas. A continuación, se presentan dos ejemplos. Para ver una lista más completa, consulte  [Bibliotecas de OData](http://www.odata.org/libraries/).
  
    
    

-  [Biblioteca de OData de .NET](http://msdn.microsoft.com/es-es/office/microsoft.data.odata%28v=vs.90%29). Consulte especialmente las clases **ODataBatch***.
    
  
-  [Biblioteca de Datajs](http://datajs.codeplex.com/documentation). Consulte especialmente  [Operaciones en lote](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch).
    
  

