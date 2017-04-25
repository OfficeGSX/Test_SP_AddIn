---
title: REST を使用してフォルダーとファイルを操作する
ms.prod: MULTIPLEPRODUCTS
ms.assetid: 4c051a49-6393-4a08-868a-4a51408842cf
---


# REST を使用してフォルダーとファイルを操作する
SharePoint 2013 REST インターフェイスを使用してフォルダーやファイルで基本的な作成、読み取り、更新、および削除 (CRUD) の操作を実行する方法について説明します。
> **ヒント**
> SharePoint Online (およびオンプレミスの SharePoint 2016 以降の) REST サービスは、OData  `$batch` クエリ オプションを使用して、複数の要求を結合して 1 つのサービスへの呼び出しにすることをサポートしています。詳細およびコード サンプルへのリンクについては、「 [REST API によりバッチ要求を発行する](make-batch-requests-with-the-rest-apis.md)」を参照してください。 





## REST を使用してフォルダーを操作する
<a name="Folders"> </a>

URL がわかっている場合は、ドキュメント ライブラリ内のフォルダーを取得できます。たとえば、次の例のエンドポイントを使用すると、共有ドキュメント ライブラリのルート フォルダーを取得できます。



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Shared Documents')
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

次の XML は、XML コンテンツ タイプを要求したときに返されるフォルダー プロパティの例を示しています。





```XML

<content type="application/xml">
<m:properties>
<d:ItemCount m:type="Edm.Int32">0</d:ItemCount>
<d:Name>Shared Documents</d:Name>
<d:ServerRelativeUrl>/Shared Documents</d:ServerRelativeUrl>
<d:WelcomePage/>
</m:properties>
</content>```

次の例は、フォルダーを **作成** する方法を示しています。





```

url: http://site url/_api/web/folders
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'ServerRelativeUrl': '/document library relative url/folder name'}
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

次の例は、 **MERGE** メソッドを使用してフォルダーを **更新** する方法を示しています。





```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
body: { '__metadata': { 'type': 'SP.Folder' }, 'Name': 'New name' }
Headers: 
     Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"MERGE",
    accept: "application/json;odata=verbose"
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

次の例は、フォルダーを **削除** する方法を示しています。





```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')
method: POST
Headers: 
     Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    "IF-MATCH": etag or "*"
    "X-HTTP-Method":"DELETE"
```


## REST を使用してファイルを操作する
<a name="Files"> </a>

次の例は、フォルダー内のすべてのファイルを **取得** する方法を示しています。



```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

次の例は、特定のファイルを **取得** する方法を示しています。





```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken```

URL がわかっている場合は、次の例のように、ファイルを **取得** することもできます。





```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken```

次の例は、ファイルを **作成** してフォルダーに追加する方法を示しています。





```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/add(url='a.txt',overwrite=true)
method: POST
body: "Contents of file"
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-length:length of post body```

次の例は、 **PUT** メソッドを使用してファイルを **更新** する方法を示しています。




> **メモ**
> **PUT** はファイルの更新に使用できる唯一のメソッドであり、 **MERGE** メソッドは使用できません。






```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/$value
method: POST
body: "Contents of file."
Headers: 
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    X-HTTP-Method:"PUT"
    content-length:length of post body```

ファイルのメタデータを更新する場合は、リスト アイテムとしてファイルに到達するエンドポイントを作成する必要があります。この操作を行うことができるのは、各フォルダーがリストでもあり、各ファイルはリスト アイテムでもあるからです。 `https://<site url>/_api/web/lists/getbytitle('Documents')/items(<item id>)` のようなエンドポイントを作成します。「 [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)」に、リスト アイテムのメタデータを更新する方法が説明されています。



更新するまでファイルが変更されないようにするため、ファイルをチェックアウトすることもできます。また、更新した後は、他のユーザーが操作できるようにファイルをチェックインして戻す必要がある場合もあります。次の例は、 **ファイルをチェックアウト** する方法を示しています。





```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckOut(),
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value```

次の例は、 **ファイルをチェックイン** する方法を示しています。





```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')/CheckIn(comment='Comment',checkintype=0)
method: POST
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value```

次の例は、ファイルを **削除** する方法を示しています。





```

url: http://site url/_api/web/GetFileByServerRelativeUrl('/Folder Name/file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
     X-RequestDigest: form digest value
    IF-MATCH: etag or "*"
    X-HTTP-Method:"DELETE"
```


## REST を使用して大きなファイルを操作する
<a name="LargeFiles"> </a>

1.5 メガバイト (MB) を超えるバイナリ ファイルをアップロードする必要がある場合は、REST インターフェイスを使用するのが唯一の方法です。SharePoint 2013 JavaScript オブジェクト モデルを使用して 1.5 MB 未満のバイナリ ファイルをアップロードする方法を示すコード例については、「 [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)」を参照してください。REST を使用して作成できるバイナリ ファイルの最大サイズは 2 ギガバイト (GB) です。次の例は、大きなバイナリ ファイルを **作成** する方法を示しています。




> **注意**
> このアプローチは、Internet Explorer 10 と他の最新バージョンのブラウザーでのみ使用できます。 




```

url: http://site url/_api/web/GetFolderByServerRelativeUrl('/Folder Name')/Files/Add(url='file name', overwrite=true)
method: POST
body: contents of binary file
headers:
    Authorization: "Bearer " + accessToken
    X-RequestDigest: form digest value
    content-type: "application/json;odata=verbose"
    content-length:length of post body```

次のコード サンプルは、この REST エンドポイントとクロスドメイン ライブラリを使用してファイルを作成する方法を示しています。





```

function uploadFileBinary() {
XDomainTestHelper.clearLog();
var ro;
if (document.getElementById("TxtViaUrl").value.length > 0) {
ro = new SP.RequestExecutor(document.getElementById("TxtWebUrl").value, document.getElementById("TxtViaUrl").value);
}
else {
ro = new SP.RequestExecutor(document.getElementById("TxtWebUrl").value);
}
var body = "";
for (var i = 0; i < 1000; i++) {
var ch = i % 256;
body = body + String.fromCharCode(ch);
}
var info = {
url: "_api/web/lists/getByTitle('Shared Documents')/RootFolder/Files/Add(url='a.dat', overwrite=true)",
method: "POST",
binaryStringRequestBody: true,
body: body,
success: success,
error: fail,
state: "Update"
};
ro.executeAsync(info);
}
```


## REST を使用してリスト アイテムに添付されたファイルを操作する
<a name="FileAttachments"> </a>

次の例は、リスト アイテムに添付されているすべてのファイルを **取得** する方法を示しています。



```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

次の例は、リスト アイテムに添付されているファイルを **取得** する方法を示しています。





```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: GET
headers:
    Authorization: "Bearer " + accessToken
    accept: "application/json;odata=verbose" or "application/atom+xml"
```

次の例は、リスト アイテムの添付ファイルを **作成** する方法を示しています。





```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles/ add(FileName='file name')
method: POST
headers:
    Authorization: "Bearer " + accessToken
    body: "Contents of file."
    X-RequestDigest: form digest value
    content-length:length of post body```

次の例は、 **PUT** メソッドを使用してリスト アイテムの添付ファイルを **更新** する方法を示しています。




> **メモ**
> **PUT** はファイルの更新に使用できる唯一のメソッドであり、 **MERGE** メソッドは使用できません。






```

url: http://site url/_api/web/lists/getbytitle('list title')/items(item id)/AttachmentFiles('file name')/$value
method: POST
body: "Contents of file."
headers:
    Authorization: "Bearer " + accessToken
    "X-HTTP-Method":"PUT"
    X-RequestDigest: form digest value
    content-length:length of post body```


## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md)


-  [ファイルおよびフォルダー REST API リファレンス](2c3d2545-1cd7-497e-b535-12199d8edfbb.md)


-  [REST API および jQuery を使用してファイルをアップロードする](upload-a-file-by-using-the-rest-api-and-jquery.md)


-  [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)


-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)


-  [SharePoint 2013: REST を使用してファイルおよびフォルダーに対する基本的なデータ操作を実行する](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)


-  [SharePoint 2013 で C# および JavaScript を使用して REST 呼び出しを実行する](http://www.microsoft.com/resources/msdn/ja-jp/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=4e4cc094-ff69-405b-852f-2ac7c41293c5)


-  [SharePoint 2013 で C# および JavaScript を使用して REST 呼び出しを実行するデモ](http://www.microsoft.com/resources/msdn/ja-jp/office/media/video/video.mdl?cid=sdc&amp;from=mscomsdc&amp;videoid=b1e7c9c5-0f62-4a78-bb7b-8e283c86145c)


-  [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)


-  [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)


-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)


-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md)


-  [Open Data Protocol](http://www.odata.org/)


-  [OData: JavaScript Object Notation (JSON) 形式](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)







