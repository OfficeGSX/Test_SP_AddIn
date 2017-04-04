---
title: 請使用 REST Api 的批次要求
ms.prod: SHAREPOINT
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# 請使用 REST Api 的批次要求
了解如何使用 REST/OData Api 的 `$batch`查詢選項。
本文說明如何批次處理查詢及 REST/OData API 作業的Microsoft SharePoint Online (及內部SharePoint 2016年及更新版本) 與Office 365 REST api (英文) [檔案及資料夾的子集合](http://msdn.microsoft.com/en-us/office/office365/api/files-rest-operations) 。與此技術可以結合成單一要求的伺服器與單一回應後的許多作業以改善效能的增益集。
  
    
    


## 高階主管摘要 $batch 選項。

SharePoint Online(及內部SharePoint 2016年及更新版本) 及Office 365 APIs Preview實作 OData  `$batch`查詢選項，讓您可以依賴 [官方文件](http://www.odata.org/documentation/odata-version-3-0/batch-processing)如需如何使用它的詳細資訊。(另一個作法是開頭 [第 1 部分-SharePoint REST API 批次處理](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests)主體，請參閱 ＜ Andrew Connell 的部落格文章)。以下是主要資料點的提醒：
  
    
    

- 要求 URL 所組成的根服務 URL 和 `$batch`選項 ；例如，  `https://fabrikam.sharepoint.com/_api/$batch`或 `https://fabrikam.office365.com/api/v1.0/me/$batch`。
    
  
- HTTP 要求主體是 *multipart/mixed*  MIME 類型。
    
  
- 本文的要求會分成會與彼此區隔所要求的標頭中所指定的界限字串的組件。
    
  
- 內文結尾每組件] 有其專屬的 HTTP 動詞及 REST URL 和其本身內部本文時適用。
    
  
- 組件可讀取的作業 (或 [函數引動過程) 或一個或多個變更集寫入作業 (或函數叫用)。變更集是本身 MIME 類型 *multipart/mixed*  與零件包含插入、 更新或刪除作業。
    
    > [!重要]
      > 在這個階段， SharePoint和Office 365 APIs Preview不支援 「 所有或 nothing 」 功能具有多個作業內加以變更集。如果任何子作業失敗，其他人仍然完成與未回復。

## Code samples

使用針對SharePoint REST/OData Api  `$batch`查詢選項的程式碼範例：
  
    
    

- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)
    
  
- **JavaScript:** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)
    
  

## 範例要求和回應

以下是範例批次擷取兩個不同的清單中的所有項目的標題的兩個 GET 作業的原始 HTTP 要求。
  
    
    

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

以下是本文的批次清單的刪除和取得的 SharePoint 清單-的-清單的原始 HTTP 要求的範例。
  
    
    



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


## 實用的文件庫的連結

有許多語言支援 OData 批次處理 OData 文件庫。下面是兩個範例。如需更完整清單，請參閱 [OData 文件庫](http://www.odata.org/libraries/)。
  
    
    

-  [.NET OData 文件庫](http://msdn.microsoft.com/en-us/office/microsoft.data.odata%28v=vs.90%29)。請參閱特別 **ODataBatch***類別。
    
  
-  [Datajs 文件庫](http://datajs.codeplex.com/documentation)。請參閱特別 [批次作業](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch)。
    
  

