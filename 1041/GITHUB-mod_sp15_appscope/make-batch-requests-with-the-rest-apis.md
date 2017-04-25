---
title: REST API によりバッチ要求を発行する
ms.prod: OFFICE365
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# REST API によりバッチ要求を発行する
REST/OData API で  `$batch` クエリ オプションを使用する方法について説明します。
この記事では、Microsoft SharePoint Online の REST/OData API (およびオンプレミスの SharePoint 2016 以降)、および Office 365 REST API の  [ファイルとフォルダーのサブセット](http://msdn.microsoft.com/en-us/office/office365/api/files-rest-operations) に対して、複数のクエリや操作を 1 つにまとめるバッチ処理の方法について説明します。このテクニックを使用すれば、多くの操作を組み合わせてサーバーに対して単一の要求を行い、単一の応答が返されるようにすることにより、アドインのパフォーマンスを改善できます。





## $batch オプションの要約

SharePoint Online (およびオンプレミスの SharePoint 2016 以降) および Office 365 API では、OData の  `$batch` クエリ オプションが実装されています。その使用方法について、詳しくは [公式ドキュメント](http://www.odata.org/documentation/odata-version-3-0/batch-processing) をご覧ください (また、「 [第 1 部 - SharePoint REST API バッチ操作](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests)」から始まる Andrew Connell のブログ投稿もご覧ください)。以下に、そのうちの主要ポイントのみを示します。




- 要求 URL は、ルート サービス URL と  `$batch` オプションとで構成されます (例: `https://fabrikam.sharepoint.com/_api/$batch` または `https://fabrikam.office365.com/api/v1.0/me/$batch`)。


- HTTP 要求本文は、MIME タイプ  *multipart/mixed*  です。


- 要求本文は、要求のヘッダーで指定される境界文字列で区切られた複数のパートに分割されます。


- 本文の各パートには、それぞれ独自の HTTP 動詞と REST URL があり、該当する場合には独自の内部本文があります。


- 1 つのパートは、1 つの読み取り操作 (または関数呼び出し)、または 1 つ以上の書き込み操作 (または関数呼び出し) からなる 1 つのChangeSet です。ChangeSet は、それ自体 1 つの MIME タイプ  *multipart/mixed*  であり、挿入、更新、または削除の操作を含む複数のサブパートを伴います。

    > **重要**
      > 現時点で、SharePoint と Office 365 API において、複数の操作が含まれる ChangeSet のための「all or nothing (全部処理するか、何も処理しないか)」機能はサポートされていません。子操作のいずれかが失敗しても、他の操作は実行を完了し、ロールバックされません。 

## コード サンプル

SharePoint REST/OData API に対して  `$batch` クエリ オプションを使用するコードのサンプル:




- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)


- **JavaScript:** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)



## 要求と応答の例

2 つの異なるリストに含まれるすべてのアイテムのタイトルを取得する 2 つの GET 操作をバッチ処理する未加工 HTTP 要求の例を次に示します。



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

リストの DELETE と SharePoint リストのリストの GET をバッチ処理する未加工 HTTP 要求の本文の例を次に示します。





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


## 役立つライブラリへのリンク

多くの言語に対応した、OData バッチ処理をサポートする OData ライブラリがあります。2 つの例を以下に示します。完全な一覧については、 [OData ライブラリ](http://www.odata.org/libraries/)を参照してください。




-  [.NET OData ライブラリ](http://msdn.microsoft.com/ja-jp/office/microsoft.data.odata%28v=vs.90%29)。特に **ODataBatch*** クラスを参照してください。


-  [Datajs ライブラリ](http://datajs.codeplex.com/documentation)。特に [バッチ操作](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch)を参照してください。



