---
title: REST サービスで表される SharePoint データ構造内を移動する
ms.prod: SHAREPOINT
ms.assetid: fa4154ea-de8c-4f62-8a1b-8c70072eddce
---


# REST サービスで表される SharePoint データ構造内を移動する
特定の SharePoint アイテムに対する REST エンドポイントから、親サイトやそのアイテムが存在するライブラリ構造などの関連するアイテムに移動およびアクセスする方法について説明します。 
## 特定の URL から他の SharePoint リソースに移動する

SharePoint REST サービスを使用する際には、多くの場合、特定の SharePoint アイテムの URL を理解することから始め、そのアイテムが存在するフォルダーやライブラリ構造などの関連アイテムにアクセスします。たとえば、SharePoint ライブラリ内のドキュメントの URL をユーザーが入力するアドインを作成する場合を考えましょう。このアドインは、ユーザーが入力した URL を分析して実際の SharePoint サイトの URL を特定します。それが完了すると、関連するアイテムやリソースの作成、更新、または削除などの他の要求をアドインがそのサイトから自動的に行えるようになります。 



これを行うために、アドインは SharePoint の次の情報に対してクエリを実行する必要があります。




- リソースを含むサイトおよびサイト コレクションのサーバーの相対 URL


- **POST**、 **PUT**、 **MERGE**、および **DELETE** など、リソースの状態を変更する要求を実行できるようにするためのフォーム ダイジェスト


基本的なプロセス:




1. 特定の URL で  `/contextinfo` 演算子を使用して、サイトおよびサイト コレクション アドレス、およびフォーム ダイジェストにアクセスします。 `/contextinfo` 演算子は次の形式で使用します。

     `http://server/web/doclib/forms/_api/contextinfo`

    クロスサイト スクリプティング攻撃に対するセキュリティ防御を強化するために、 `/contextinfo` 演算子は **POST** 要求のみを受け付けます。


2.  `/contextinfo` から返される [SPContextWebInformation オブジェクト プロパティ](#bk_props)を使用して、必要に応じて追加のリソースにアクセスします。 


 **試用する**




1. 次のような特定の SharePoint アイテムに対する URL が最初に指定されます。

     `http://site/web/doclib/myDocument.docx`


2. URL の末尾から特定のリソースの名前を削除して、URL がドキュメント ライブラリ、フォルダー、またはリストを指すようにします。この場合は、次のようになります。

     `http://site/web/doclib/`


3. REST サービスのポインターと  `/contextinfo` 演算子を URL に追加します。

     `http://site/web/doclib/_api/contextinfo`


4. フォーム ダイジェストと **webFullUrl** プロパティを応答から読み取ります。


5. REST サービスのポインター  `_api` を Web の URL に追加します。


6. 作成された URL とフォーム ダイジェストを使用して、必要な他のリソースに対する要求を行います。


 **GET** 要求を行う場合や、検証済みの OAuth トークンを使用して要求を行う場合、フォーム ダイジェストを渡す必要はありません。




## 親サイトと子サイト間を移動する
<a name="bk_sites"> </a>

SharePoint サーバー オブジェクト モデルを使用してサイト構造を移動する場合は、 [SPWeb.ParentWeb](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.ParentWeb.aspx) プロパティと [SPWeb.Webs](https://msdn.microsoft.com/library/Microsoft.SharePoint.SPWeb.Webs.aspx) プロパティを使用して、親サイトおよび子サイトを表すオブジェクトにアクセスします。



対応する REST リソース ( `web/parentweb` および `web/webs`) は、サイトを表すオブジェクトを返しません。これは、REST サービスが OData 標準に準拠しているためで、完全なサイト表現を返すと、これらの要求が非効率に行われます。代わりに、これらのリソースは、サイトのスカラー プロパティを含む  [WebInfo オブジェクト ](#bk_webinfo)を返しますが、そのオブジェクトには、リスト コレクションやフィールド コレクションなどの関連するエンティティ セットは含まれません。



特定の親サイトまたは子サイトへ移動するには、 **Id** または **Title** プロパティを使用して、それぞれのサイトへの REST URL を適切に作成します。そこから、そのサイトに関連するエンティティ セットにアクセスすることができます。




## フォルダー階層内を移動する
<a name="bk_folders"> </a>

SharePoint REST サービスでは、URL 構築によるサイトのフォルダー階層間の移動はサポートされていません。代わりに、REST では  [Web.GetFolderByServerRelativeUrl](https://msdn.microsoft.com/library/Microsoft.SharePoint.Client.Web.GetFolderByServerRelativeUrl.aspx) メソッドに相当する機能を使用できます。次に例を示します。



 *REST サービスでサポートされない移動:* 



 `/_vti_bin/client.svc/web/lists/SharedDocuments/folder1/stuff/things/Recycle`



REST サービスでサポートされる移動: 



 `/_vti_bin/client.svc/web/GetFolderByServerRelativeUrl('SharedDocuments/folder1/stuff/things')/Recycle`.




## SPContextWebInformation オブジェクト プロパティ
<a name="bk_props"> </a>



|**SPContextWebInformation プロパティ**|**説明**|
|:-----|:-----|
|**webFullUrl** <br/> |一番近いサイトのサーバー相対 URL を取得します。  <br/> |
|**siteFullUrl** <br/> |サイトが含まれているサイト コレクションのルートのサーバー相対 URL を取得します。  <br/> 一番近い Web サイトがサイト コレクションのルートの場合、 **webFullUrl** プロパティの値は **siteFullUrl** プロパティの値と等しくなります。 <br/> |
|**formDigestValue** <br/> |サーバーの要求フォーム ダイジェストを取得します。  <br/> |
|**LibraryVersion** <br/> |REST ライブラリの現在のバージョンを取得します。  <br/> |
|**SupportedSchemaVersions** <br/> |サポートされる REST/CSOM ライブラリのスキーマのバージョンを取得します。  <br/> |
 

## WebInfo オブジェクト
<a name="bk_webinfo"> </a>



|**WebInfo プロパティ**|**説明**|
|:-----|:-----|
|**Created** <br/> |サイトが作成された日時を示す値を取得します。  <br/> |
|**Description** <br/> |サイトの説明を取得または設定します。  <br/> |
|**Id** <br/> |サイト識別子を示す値を取得します。  <br/> |
|**Language** <br/> |サイトで使用される言語のロケール ID (LCID) を示す値を取得します。  <br/> |
|**LastItemModifiedDate** <br/> |サイトのアイテムが最後に更新された日付を示す値を取得します。  <br/> |
|**Title** <br/> |サイトのタイトルを取得または設定します。  <br/> |
|**WebTemplateId** <br/> |サイト テンプレートの識別子を取得します。  <br/> |
 

## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 REST サービスの概要](get-to-know-the-sharepoint-2013-rest-service.md)


-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)


-  [REST を使用してフォルダーとファイルを操作する](working-with-folders-and-files-with-rest.md)


-  [SharePoint REST サービス エンドポイント URI を決定する](determine-sharepoint-rest-service-endpoint-uris.md)


-  [SharePoint REST 要求で OData クエリ操作を使用する](use-odata-query-operations-in-sharepoint-rest-requests.md)


-  [SharePoint 2013 REST API、エンドポイント、およびサンプル](02128c70-9d27-4388-9374-a11bce68fdb8.md)


-  [REST サービスを使用して SharePoint アイテムを同期させる](synchronize-sharepoint-items-using-the-rest-service.md)


-  [REST サービスで ETag を使用してドキュメント リスト アイテムのバージョンを取得する](5f7e0579-46b7-44ab-b3b4-cdbc622dcd98.md)







