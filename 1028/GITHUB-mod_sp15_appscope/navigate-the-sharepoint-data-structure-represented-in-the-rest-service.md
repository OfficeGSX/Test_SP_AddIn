---
title: 瀏覽表示 REST 服務中的 SharePoint 資料結構
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# 瀏覽表示 REST 服務中的 SharePoint 資料結構
了解如何從指定的 SharePoint 項目、 REST 端點啟動並瀏覽至並存取父網站或該項目所在的文件庫結構等相關的項目。
## 瀏覽從指定的 URL 來連絡其他的 SharePoint 資源

當您正在使用 SharePoint REST 服務時，您通常將開始了解特定的 SharePoint 項目的 URL，但要存取相關的項目，例如該項目所在的資料夾或文件庫的結構。例如，假設您建立增益集其中您使用者輸入 SharePoint 文件庫中的文件的 URL。增益集必須再細分算出實際的 SharePoint 網站 URL 的 URL。一旦完成增益集接著可進行更多的要求在網站上的使用者代理，例如建立、 更新或刪除相關的項目或資源。
  
    
    
若要這樣做，增益集需要 SharePoint 查詢的下列資訊：
  
    
    

- 網站與網站集合包含資源伺服器相對 Url
    
  
- 若要讓您執行的資源，例如 **POST**、 **PUT**、 **MERGE**及 **DELETE**狀態變更的要求表單摘要
    
  
基本程序：
  
    
    

1. 若要存取之網站與網站集合位址和表單摘要搭配特定 URL  `/contextinfo`運算子。使用 `/contextinfo`運算子格式如下：
    
     `http://server/web/doclib/forms/_api/contextinfo`
    
    針對跨網站指令碼嘗試、  `/contextinfo`安全性運算子會接受僅 **POST**要求。
    
  
2. 您可以使用 `/contextinfo`運算子會傳回 access 為所需的其他資源 [SPContextWebInformation 物件屬性](#bk_props)。
    
  
 **Try it**
  
    
    

1. 啟動指定的 SharePoint 項目的 url。例如：
    
     `http://site/web/doclib/myDocument.docx`
    
  
2. 使 URL 指向文件庫、 資料夾或清單中移除 URL 的結尾之特定資源的名稱。在此例中：
    
     `http://site/web/doclib/`
    
  
3. Append REST 服務指標及 `/contextinfo`運算子的 url：
    
     `http://site/web/doclib/_api/contextinfo`
    
  
4. 讀取回應表單摘要式及 **webFullUrl**屬性。
    
  
5. REST 服務指標 `_api`附加至網頁 URL
    
  
6. 使用產生 URL 和表單摘要可讓您所需的其他資源的要求。
    
  
您不需要如果您正在進行 **GET**要求或進行要求使用已驗證的 OAuth 權杖傳遞表單摘要。
  
    
    

## 瀏覽父項與子網站
<a name="bk_sites"> </a>

當您瀏覽您使用 SharePoint server 物件模型的網站結構時，您可以使用 [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) 和 [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) 屬性來存取代表父項與子網站的物件。
  
    
    
對應的 REST 資源  `web/parentweb`和 `web/webs` 不會傳回代表網站的物件。這是因為 REST 服務符合 OData 標準，並且傳回完整的站台的表示法會讓這類要求效率。而這些傳回 [WebInfo 物件](#bk_webinfo)所包含的網站純量屬性，但不相關的實體設定例如 like 集合或欄位的集合。
  
    
    
若要瀏覽至特定的上層或子網站、 建構適當 REST URL 使用 **Id**或 **Title**屬性至該網站。如此，您可以存取該網站相關的實體集合。
  
    
    

## 瀏覽資料夾結構
<a name="bk_folders"> </a>

SharePoint REST 服務不支援周遊透過 URL 結構站台之資料夾階層。請改用 REST 相當於 [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) 方法。例如：
  
    
    
 *透過 REST 服務不支援的功能：* 
  
    
    
 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`
  
    
    
REST 服務所支援的功能：
  
    
    
 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`.
  
    
    

## SPContextWebInformation 物件屬性
<a name="bk_props"> </a>



|**SPContextWebInformation 屬性**|**描述**|
|:-----|:-----|
|**webFullUrl** <br/> |取得與最近的網站的伺服器相對 URL。 <br/> |
|**siteFullUrl** <br/> |取得網站集合之網站中所含的根伺服器相對 URL。 <br/> 如果網站集合的根最接近的網頁，然後 **webFullUrl**屬性的值等於 **siteFullUrl**屬性。 <br/> |
|**formDigestValue** <br/> |取得伺服器的要求表單摘要。 <br/> |
|**LibraryVersion** <br/> |取得目前的其餘部分文件庫的版本。 <br/> |
|**SupportedSchemaVersions** <br/> |取得支援的版本中的其餘部分/CSOM 文件庫的結構描述。 <br/> |
   

## WebInfo 物件
<a name="bk_webinfo"> </a>



|**WebInfo 屬性**|**描述**|
|:-----|:-----|
|**Created** <br/> |取得值，指定建立網站的時間。 <br/> |
|**Description** <br/> |取得或設定網站的描述。 <br/> |
|**Id** <br/> |取得一個值，指定網站識別碼。 <br/> |
|**Language** <br/> |取得指定之語言的網站上使用地區設定識別碼 (LCID) 值。 <br/> |
|**LastItemModifiedDate** <br/> |取得值，指定網站中最後修改的項目。 <br/> |
|**Title** <br/> |取得或設定之網站的標題。 <br/> |
|**WebTemplateId** <br/> |取得網站範本的識別碼。 <br/> |
   

## 其他資源
<a name="bk_addresources"> </a>


-  [若要了解 SharePoint 2013 REST 服務取得](get-to-know-the-sharepoint-2013-rest-service.md)
    
  
-  [使用 SharePoint 2013 其餘端點完成基本作業](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)
    
  
-  [使用清單及清單項目與 REST](working-with-lists-and-list-items-with-rest.md)
    
  
-  [使用資料夾與其餘的檔案](working-with-folders-and-files-with-rest.md)
    
  
-  [決定 SharePoint REST 服務端點 Uri](determine-sharepoint-rest-service-endpoint-uris.md)
    
  
-  [在 SharePoint 其餘邀請中使用 OData 查詢作業](use-odata-query-operations-in-sharepoint-rest-requests.md)
    
  
-  [REST API 參考和範例](http://msdn.microsoft.com/library/02128c70-9d27-4388-9374-a11bce68fdb8%28Office.15%29.aspx)
    
  
-  [同步處理 SharePoint 項目的使用 REST 服務](synchronize-sharepoint-items-using-the-rest-service.md)
    
  
-  [使用 ETag 值，透過其他服務，來取得文件清單項目版本設定](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)
    
  

  
    
    

