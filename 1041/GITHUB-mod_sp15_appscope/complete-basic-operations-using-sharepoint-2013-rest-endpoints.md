---
title: SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する
ms.prod: SHAREPOINT
ms.assetid: e3000415-50a0-426e-b304-b7de18f2f7d9
---


# SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する
SharePoint 2013 REST インターフェイスで基本的な作成、読み取り、更新、および削除 (CRUD) の操作を実行する方法について説明します。
## SharePoint クライアント API および REST を使用して開発する
<a name="ClientAPIs"> </a>

SharePoint 2013 で提供される Representational State Transfer (REST) インターフェイスを使用すると、基本的な作成、読み取り、更新、および削除 (CRUD) の操作を実行できます。REST インターフェイスは、他の SharePoint クライアント API で利用可能なすべての SharePoint エンティティおよび操作を公開します。REST を使用する 1 つのメリットは、SharePoint 2013 ライブラリまたはクライアント アセンブリへの参照を追加する必要がないことです。Web、リスト、リスト アイテムなどの SharePoint エンティティを取得したり更新したりするには、代わりに適当なエンドポイントに HTTP 要求を実行します。SharePoint 2013 REST インターフェイスとそのアーキテクチャの概要については、「 [SharePoint 2013 REST サービスの概要](get-to-know-the-sharepoint-2013-rest-service.md)」を参照してください。
  
    
    
「 [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)」および「 [REST を使用してフォルダーとファイルを操作する](working-with-folders-and-files-with-rest.md)」では、SharePoint コア エンティティとの連携方法について詳しく説明しています。C# で記述された ASP.NET Web アプリケーションのコンテキストでこれらの多くの操作を実行する方法を示しているサンプルについては、「 [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)」を参照してください。
  
    
    
SharePoint 2013 プラットフォームで利用できる API セットの詳細については、「 [SharePoint 2013 での適切な API セットの選択](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)」を参照してください。その他のクライアント API の使用法については、「 [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)」、「 [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)」、および「 [SharePoint 2013 にアクセスする Windows Phone アプリの作成](http://msdn.microsoft.com/library/36681335-f772-4499-8445-f94481bc18e7%28Office.15%29.aspx)」をご覧ください。
  
    
    

## SharePoint 2013 REST サービスにおける HTTP の動作
<a name="HTTPOps"> </a>

SharePoint 2013 REST サービスのエンドポイントは、SharePoint クライアント オブジェクト モデルの型とメンバーに対応しています。HTTP 要求を使用することで、これらの REST エンドポイントを使用して、SharePoint エンティティ (リストやサイトなど) に対して通常の CRUD ( **Create**、 **Read**、 **Update**、および **Delete**) 操作を実行できます。
  
    
    
通常、 **Read** 操作を表すエンドポイントは HTTP の **GET** コマンドに相当し、更新操作を表すエンドポイントは HTTP の **POST** コマンドに相当し、更新または挿入操作を表すエンドポイントは HTTP の **PUT** コマンドに相当します。
  
    
    
SharePoint 2013では、 **POST** を使用してリストやサイトなどのエンティティを作成します。SharePoint 2013 REST サービスでは、オブジェクトの定義を含んだ **POST** コマンドの、コレクションを表すエンドポイントへの送信がサポートされます。たとえば、新しいリスト オブジェクトの定義を ATOM 形式で含んだ **POST** コマンドを次の URL に送信して SharePoint リストを作成することができます。
  
    
    
 `http://<site url>/_api/web/lists`
  
    
    
 **POST** 操作では、必須ではないプロパティはすべて既定値に設定されます。 **POST** 操作の一部として読み取り専用プロパティを設定すると、サービスによって例外が返されます。
  
    
    
既存の SharePoint オブジェクトを更新する場合は、 **PUT** および **MERGE** 操作を使用します。オブジェクト プロパティ **set** 操作を表すサービス エンドポイントは、すべて **PUT** リクエストと **MERGE** リクエストの両方をサポートします。 **MERGE** リクエストの場合、プロパティの設定は省略可能です。プロパティを明示的に設定しなくても、現在のプロパティが保持されます。ただし、 **PUT** コマンドの場合、プロパティを明示的に設定しなかった場合には、既定のプロパティが設定されます。さらに、HTTP **PUT** コマンドを使用したオブジェクトの更新で必要なプロパティをすべて設定しなかった場合には、REST サービスから例外が返されます。
  
    
    
特定のエンドポイント URL に対して HTTP の **DELETE** コマンドを実行すると、そのエンドポイントが表していた SharePoint オブジェクトが削除されます。リスト、ファイル、リスト アイテムなど、再利用可能なオブジェクトの場合は、結果的に **Recycle** 操作になります。
  
    
    

## SharePoint 2013 REST インターフェイスを使用してデータを読み取る
<a name="ReadingData"> </a>

SharePoint 2013 に組み込まれている REST 機能を使用するには、使用するクライアント オブジェクト モデル API に対応する OData 標準を使用して RESTful HTTP 要求を作成します。各 SharePoint エンティティはターゲットとする SharePoint 2013 サイトのエンドポイントで公開され、そのメタデータは XML または JSON 形式で表されます。HTTP 要求は JavaScript や C# に限らず任意の言語で実行できます。
  
    
    
REST エンドポイントから情報を読み取るには、エンドポイントの URL と、エンドポイントで公開されている SharePoint エンティティの OData 表現の両方を知っている必要があります。たとえば、特定の SharePoint サイトのリストをすべて取得するには、 `http://<site url>/_api/web/lists` に **GET** 要求を実行します。ブラウザーでこの URL にアクセスし、返される XML を確認できます。コードで要求を実行する際には、リストの OData 表現を XML と JSON のどちらで受け取るかを指定できます。
  
    
    
次の C# コードは、JQuery を使用してサイトのリストすべての JSON 表現を返す **GET** 要求を実行する方法の例です。 ここでは、 **accessToken** 変数に格納されている有効な OAuth アクセス トークンがあることを前提としています。 アドイン Web 内部からこの呼び出しを実行する場合、SharePoint ホスト型アドイン内で実行する場合に必要になるように、アクセス トークンは必要ではありません。ブラウザー クライアントで実行しているコードからアクセス トークンを取得できないことに注意してください。サーバーで実行しているコードからアクセス トークンを取得する必要があります。「 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)」および「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」で、アクセス トークンの取得方法について説明しています。
  
    
    


```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", 
  "Bearer " + accessToken);
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

JavaScript でアドインを記述するが SharePoint 2013 クロスドメイン ライブラリを使用している場合、この要求は少し違ったものになります。この場合、アクセス トークンを提供する必要がありません。次のコードは、クロスドメイン ライブラリを使用し、リストの OData 表現を JSON ではなく XML で受け取る場合のこの要求の例を示しています。 (Atom が既定の応答形式なので、 **Accept** ヘッダーを含める必要はありません。) クロスドメイン ライブラリの使用の詳細については、「 [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)」を参照してください。
  
    
    


```

var executor = new SP.RequestExecutor(appweburl);
executor.executeAsync(
    {
        url:
            appweburl +
            "/_api/SP.AppContextSite(@target)/web/lists?@target='" +
            hostweburl + "'",
        method: "GET",
        success: successHandler,
        error: errorHandler
    }
);```

次の C# コードは、JQuery を使用してサイトのリストすべての JSON 表現を返す **GET** 要求を実行する方法の例です。 ここでは、 **accessToken** 変数に格納されている有効な OAuth アクセス トークンがあることを前提としています。 アプリ Web 内部からこの呼び出しを実行する場合、SharePoint にホストされるアプリ内で実行する場合に必要になるように、アクセス トークンは必要ではありません。ブラウザー クライアントで実行しているコードからアクセス トークンを取得できないことに注意してください。サーバーで実行しているコードからアクセス トークンを取得する必要があります。「 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)」および「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」で、アクセス トークンの取得方法について説明しています。
  
    
    


```cs

HttpWebRequest endpointRequest = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + "/_api/web/lists");
endpointRequest.Method = "GET";
endpointRequest.Accept = "application/json;odata=verbose";
endpointRequest.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse endpointResponse = (HttpWebResponse)endpointRequest.GetResponse();
```


### リソースと共に返されないプロパティの取得
<a name="NavigationProperties"> </a>

リソースを取得する際には多くのプロパティ値が返されますが、プロパティが SharePoint エンティティを表す場合は、通常、プロパティ エンドポイントに **GET** 要求を直接送信する必要があります。
  
    
    
次の例は、リソース エンドポイントにプロパティ名を追加して、プロパティを取得する方法を示しています。この例では、 **Author** プロパティの値が **File** リソースから取得されます。
  
    
    
http:// _<サイトの url>_/_api/web/getfilebyserverrelativeurl('/ _<フォルダー名>_/ _<ファイル名>_')/author
  
    
    
JSON 形式で結果を取得するには、 `"application/json;odata=verbose"` に設定した **Accept** ヘッダーを含めます。
  
    
    

## REST インターフェイスを使用してデータを書き込む
<a name="WritingData"> </a>

適切なエンドポイントに RESTful HTTP 要求を作成すると、データを読み取るときと同じように SharePoint エンティティの作成および更新を実行できます。1 つの大きな違いは、 **POST** 要求を使用する点です。また、エンティティを更新するときは、 **PUT** または **MERGE** の HTTP 要求メソッドも渡します。これを実行するには、これらのいずれかの語を **X-HTTP-Method** キーの値として要求のヘッダーに追加します。 **MERGE** メソッドでは指定したエンティティのプロパティのみが更新されるのに対し、 **PUT** メソッドでは **POST** の本文に指定した新しいエンティティで既存のエンティティが置き換えられます。エンティティの削除には **DELETE** メソッドを使用します。エンティティを作成したり更新したりするときは、作成または変更するエンティティの OData 表現を HTTP 要求の本文で指定する必要があります。
  
    
    
SharePoint エンティティを作成、更新、削除するときのもう 1 つの重要な考慮事項は、これらの要求の認証に OAuth を使用していない場合、これらの処理にサーバーの要求フォーム ダイジェスト値が **X-RequestDigest** ヘッダーの値として必要であることです。この値を取得するには、 `http://<site url>/_api/contextinfo` に対して本文が空の **POST** 要求を実行し、 **contextinfo** エンドポイントが返す XML の `d:FormDigestValue` ノードの値を抽出します。次の例は、 **contextinfo** エンドポイントに対する HTTP 要求を C# で示しています。
  
    
    


```cs

HttpWebRequest endpointRequest =
  (HttpWebRequest)HttpWebRequest.Create(
  "http://<site url>/_api/contextinfo");
endpointRequest.Method = "POST";
endpointRequest.Accept = "application/json;odata=verbose";
HttpWebResponse endpointResponse =
  (HttpWebResponse)endpointRequest.GetResponse();
```

「 [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)」で説明している認証と承認のフローを使用している場合は、要求に要求のダイジェストを含める必要はありません。
  
    
    
JavaScript クロスドメイン ライブラリを使用している場合は、SP.RequestExecutor がフォーム ダイジェスト値の取得と送信を処理します。
  
    
    
SharePoint でホストされる SharePoint アドインを作成する場合、別途 HTTP 要求を実行してフォーム ダイジェストの値を取得する必要はありません。代わりに、SharePoint のページが既定のマスター ページを使用している場合、次の例に示すようにそのページから JavaScript コードで値を取得することができます。この例では、JQuery を使用してリストを作成します。
  
    
    


```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists",
        type: "POST",
        data:  JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'AllowContentTypes': true,
 'BaseTemplate': 100, 'ContentTypesEnabled': true, 'Description': 'My list description', 'Title': 'Test' }
),
        headers: { 
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val()
        },
        success: doSuccess,
        error: doError
});

```

次の例は、前の例で作成されたリストを更新する方法を示しています。この例ではリストのタイトルを変更し、JQuery を使用しますが、SharePoint ホスト型アドインでこの操作を実行することを前提としています。
  
    
    


```

jQuery.ajax({
        url: "http://<site url>/_api/web/lists/GetByTitle('Test')",
        type: "POST",
        data: JSON.stringify({ '__metadata': { 'type': 'SP.List' }, 'Title': 'New title' }),
        headers: { 
            "X-HTTP-Method":"MERGE",
            "accept": "application/json;odata=verbose",
            "content-type": "application/json;odata=verbose",
            "content-length": <length of post body>,
            "X-RequestDigest": $("#__REQUESTDIGEST").val(),
            "IF-MATCH": "*"
        },
        success: doSuccess,
        error: doError
});
```

要求ヘッダーの **IF-MATCH** キーの値では、リストまたはリスト アイテムの **etag** 値を指定します。この特定の値はリストおよびリスト アイテムのみに適用され、それらのエンティティを更新するときに同時実行の問題を回避できるようにすることを目的としています。前の例ではこの値にアスタリスク (*) を使用しており、この値は同時実行の問題を心配する理由がない場合に使用できます。それ以外の場合は、エンティティを取得する **GET** 要求を実行してリストまたはリスト アイテムの **etag** 値を取得する必要があります。結果の HTTP 応答の応答ヘッダーは、この etag を **ETag** キーの値として渡します。この値はまた、エンティティ メタデータにも含まれます。次の例は、リスト情報を含む XML ノードの開始の `<entry>` タグを示しています。 **m:etag** プロパティに **etag** 値が含まれます。
  
    
    


```XML

<entry xml:base="http://site url/_api/" xmlns=http://www.w3.org/2005/Atom
xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" 
xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"
xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" m:etag=""1"">```


## REST によるサイトの作成
<a name="bk_CreateSite"> </a>

次の例は、JavaScript でサイトを作成する方法を示しています。
  
    
    
```

jQuery.ajax({
    url: "http://<site url>/_api/web/webinfos/add",
    type: "POST",
    data: JSON.stringify(
        {'parameters': {
            '__metadata':  {'type': 'SP.WebInfoCreationInformation' },
            'Url': 'RestSubWeb',
            'Title': 'RestSubWeb',
            'Description': 'REST created web',
            'Language':1033,
            'WebTemplate':'sts',
            'UseUniquePermissions':false}
        }
    ),
    headers: { 
        "accept": "application/json; odata=verbose", 
        "content-type":"application/json;odata=verbose",
        "content-length": <length of post body>,
        "X-RequestDigest": $("#__REQUESTDIGEST").val() 
    },
    success: doSuccess,
    error: doError
});```


## 環境によって異なる REST 要求の方法
<a name="bk_HowRequestsDiffer"> </a>

HTTP 要求の構築と送信は、言語、ライブラリ、アドイン タイプなどによって異なります。このため、1 つの環境から別の環境に要求を変換する場合に 1 つ以上の要求コンポーネントを変更する必要が生じることがよくあります。例えば、jQuery AJAX 要求は **data** パラメーターと **type** パラメーターを使って要求の本文と種類を指定しますが、クロスドメイン ライブラリ要求は **body** パラメーターと **method** パラメーターを使ってこれらの値を指定します。
  
    
    
以下のセクションでは、環境による他の一般的な相違について説明します。
  
    
    

### フォーム ダイジェスト値を取得および送信する方法はアドインに依存します。
<a name="FormDigest"> </a>

POST 要求を送信するときは、 **X-RequestDigest** ヘッダーにフォーム ダイジェスト値が含まれている必要があります。ただし、その値を取得して送信する方法はアドインごとに異なります。
  
    
    

- SharePoint でホストされるアドインの場合は、次のヘッダーを渡すだけです。
  
    
    
"X-RequestDigest": $("#__REQUESTDIGEST").val()
    
  
- OAuth を使用するクラウドでホストされるアドインの場合は、まず要求を **contextinfo** エンドポイントに送信してフォーム ダイジェスト値を取得し、次いで「 [REST インターフェイスを使用してデータを書き込む](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#WritingData)」に示されているように、その値を要求に追加します。
    
  
- JavaScript クロスドメイン ライブラリを使用するクラウドでホストされるアドインでは、フォーム ダイジェスト値を指定する必要はありません。既定で、SP.RequestExecutor が自動でその処理を行います。(content-length 値も処理します。)
    
  

### OAuth を使うアドインは、要求でアクセス トークンを渡す必要があります。
<a name="OAuthTokens"> </a>

クラウドでホストされるアドインは、OAuth またはクロスドメイン ライブラリを使って SharePoint データへのアクセスを承認します。リモート Web サーバー上で実行されるコードを持つアドイン コンポーネントは、OAuth を使って SharePoint データへのアクセスを承認する必要があります。この場合、アクセス トークンを送信するためには、 **Authorization** ヘッダーを含める必要があります。 **HTTPWebRequest** オブジェクトに承認ヘッダーを追加する例は、「 [SharePoint 2013 REST インターフェイスを使用してデータを読み取る](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md#ReadingData)」を参照してください。
  
    
    

> **メモ**
> JavaScript で記述されている、クラウド ホスト アドインのコンポーネントは、クロスドメイン ライブラリ内の **SP.RequestExecutor** オブジェクトを使って SharePoint データにアクセスする必要があります。クロスドメイン ライブラリ要求はアクセス トークンを含める必要はありません。
  
    
    

OAuth アクセス トークンとそれらを取得する方法の詳細は、「 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)」と「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。
  
    
    

### AppContextSite を使ってコンテキストを変更するには、クロスドメイン要求のエンドポイントの Uri を使用してください。
<a name="AppContextSite"> </a>

要求は、要求の **url** プロパティで指定されているリソース エンドポイントに送信されます。エンドポイント URI は次の形式で指定します。
  
    
    
 _<site url>_/_api/ _<context>_/ _<resource>_ (example, https://contoso.com/_api/web/lists)
  
    
    
クロスドメイン ライブラリ要求は、アドイン Web 上のデータにアクセスするときにはこの形式を使います。これは、クロスドメイン ライブラリ要求のデフォルトのコンテキストです。しかし、ホスト Web または他のサイト コレクション上のデータにアクセスするときには、ホスト Web または他のサイト コレクションをコンテキストとして初期化する必要があります。この処理のため、クロスドメイン ライブラリ要求は表 1 に示すように URI 内の **SP.AppContextSite** エンドポイントを使います。表 1 に示されているサンプル URI は、 **@target** エイリアスを使ってクエリ文字列内のターゲット URL を送信します。これは、URL に特殊文字 (':') が含まれているためです。
  
    
    

> **メモ**
> クロスドメイン ライブラリを使うときには、クラウド ホスト アドインが SharePoint データにアクセスできるようにするためにアドイン Web インスタンスが必要となります。 
  
    
    


**表 1. SP.AppContextSite エンドポイントを使って要求のコンテキストを変更する**


|**アドインの種類**|**クロスドメインでのデータ アクセスのシナリオ**|**エンドポイント URI の例**|
|:-----|:-----|:-----|
|クラウド ホスト型  <br/> |クロスドメイン ライブラリを使ってホスト Web データにアクセスする JavaScript アドイン コンポーネント  <br/> | _<app web url>_/_api/SP.AppContextSite(@target)/web/lists?@target=' _<host web url>_'  <br/> |
|クラウド ホスト型  <br/> |クロスドメイン ライブラリを使ってホスト Web 以外のサイト コレクション内のデータにアクセスする JavaScript アドイン コンポーネント (テナント スコープ アドインのみ)  <br/> | _<app web url>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<target site url>_'  <br/> |
|SharePoint ホスト型  <br/> |別のサイト コレクション内のデータにアクセスするアドイン web コンポーネント (テナント スコープ アドインのみ）  <br/> | _<app web url>_/_api/SP.AppContextSite(@target)/web/title?@target=' _<target site url>_'  <br/> |
   

> **メモ**
> クロスドメイン データ アクセスでは、適切なアドインのアクセス許可も必要です。詳しくは、「 [ホスト Web からのデータ アクセス](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_Hostweb)」と「 [サイト コレクション間でのデータ アクセス](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md#SP15Accessdatafromremoteapp_TenantScope)」をご覧ください。 
  
    
    

次のコード例に示すように、SharePoint アドインはアドイン ページのクエリ文字列からアドイン Web URL とホスト Web URL を取得できます。この例には、クロスドメイン ライブラリ (ホスト Web 上の SP.RequestExecutor.js ファイルで定義される) を参照する方法も示されています。この例は、アドインが SharePoint から起動されることを想定しています。アドインが SharePoint から起動されない場合に SharePoint コンテキストを正しく設定するためのガイダンスは、「 [SharePoint アドインの認証コード OAuth フロー](authorization-code-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。
  
    
    


```

var hostweburl;
var appweburl;

// Get the URLs for the add-in web the host web URL from the query string.
$(document).ready(function () {
  //Get the URI decoded URLs.
  hostweburl = decodeURIComponent(getQueryStringParameter("SPHostUrl"));
  appweburl = decodeURIComponent(getQueryStringParameter("SPAppWebUrl"));

  // Load the SP.RequestExecutor.js file.
  $.getScript(hostweburl + "/_layouts/15/SP.RequestExecutor.js", runCrossDomainRequest);
});

// Build and send the HTTP request.
function runCrossDomainRequest() {
  var executor = new SP.RequestExecutor(appweburl); 
  executor.executeAsync({
      url: appweburl + "/_api/SP.AppContextSite(@target)/web/lists?@target='" + hostweburl + "'",
      method: "GET", 
      headers: { "Accept": "application/json; odata=verbose" }, 
      success: successHandler, 
      error: errorHandler 
  });
}

// Get a query string value.
// For production add-ins, you may want to use a library to handle the query string.
function getQueryStringParameter(paramToRetrieve) {
  var params = document.URL.split("?")[1].split("&amp;");
  var strParams = "";
  for (var i = 0; i < params.length; i = i + 1) {
    var singleParam = params[i].split("=");
    if (singleParam[0] == paramToRetrieve) return singleParam[1];
  }
}
… // success and error callback functions```


## REST 要求で使うプロパティ
<a name="bk_requestElements"> </a>

表 2 に、SharePoint REST サービスの HTTP 要求で一般に使われるプロパティを示します。
  
    
    

**表 2. HTTP 要求で REST 要求プロパティを使う場合**


|**プロパティ**|**必要な場合**|**説明**|
|:-----|:-----|:-----|
|**url** <br/> |すべての要求  <br/> |REST リソース エンドポイントの URL。例:  `http://<site url>/_api/web/lists` <br/> |
|**method** (または **type**)  <br/> |すべての要求  <br/> |HTTP 要求メソッド: 読み取り操作用の **GET** および書き込み操作用の **POST**。 **POST** 要求では、 **DELETE** ヘッダーで **MERGE**、 **PUT**、または **X-HTTP-Method** 動詞を指定して、更新または削除操作を行うことができます。 <br/> |
|**body** (または **data**)  <br/> |要求本文でデータを送信する **POST** 要求 <br/> |POST 要求の本文。エンドポイント URI で送信できないデータ (複合型など) を送信します。 **content-length** ヘッダーで使用します。 <br/> |
|**Authentication** ヘッダー <br/> |OAuth を使用してユーザーを認証するリモート アドイン。JavaScript またはクロスドメイン ライブラリを使用する場合は適用されません。  <br/> |(Microsoft Access Control Service (ACS) セキュア トークン サーバーから取得した) OAuth アクセス トークンを送信します。このアクセス トークンは、要求に対してユーザーを認証するのに使用します。例:  `"Authorization": "Bearer " + accessToken`。ここで、 `accessToken` はトークンを格納する変数を表します。トークンはサーバー側のコードを使用して取得する必要があります。 <br/> |
|**X-RequestDigest** ヘッダー <br/> |**POST** 要求 (SP.RequestExecutor 要求を除く) <br/> |OAuth を使用するリモート アドインは、 `http://<site url>/_api/contextinfo` エンドポイントからフォーム ダイジェスト値を取得できます。SharePoint ホスト型アドインは、 **#__REQUESTDIGEST** ページ コントロールから値を取得できます (SharePoint ページで使用できる場合)。「 [REST インターフェイスを使用してデータを書き込む](#WritingData)」を参照してください。  <br/> |
|**accept** ヘッダー <br/> |SharePoint メタデータを返す要求  <br/> |サーバーからの応答データの形式を指定します。既定の形式は  `application/atom+xml` です。例: `"accept":"application/json;odata=verbose"`。  <br/> |
|**content-type** ヘッダー <br/> |要求本文でデータを送信する **POST** 要求 <br/> |クライアントがサーバーに送信するデータの形式を指定します。既定の形式は  `application/atom+xml` です。例: `"content-type":"application/json;odata=verbose"`。  <br/> |
|**content-length** ヘッダー <br/> |要求本文でデータを送信する **POST** 要求 (SP.RequestExecutor 要求を除く) <br/> |コンテンツの長さを指定します。例:  `"content-length":requestBody.length`。  <br/> |
|**IF-MATCH** ヘッダー <br/> |**POST**、 **DELETE**、または **MERGE** 操作に対する **PUT** 要求。主にリストとライブラリの変更に使用します。 <br/> |変更対象のオブジェクトが最後に取得されてから変更されていないことを確認する方法を提供します。または、すべての変更を上書きするように指定することもできます。例:  `"IF-MATCH":"*"`。  <br/> |
|**X-HTTP-Method** ヘッダー <br/> |**POST**、 **DELETE**、または **MERGE** 操作に対する **PUT** 要求 <br/> |要求で更新または削除操作を実行するように指定する場合に使用します。例:  `"X-HTTP-Method":"PUT"`。  <br/> |
|**binaryStringRequestBody** <br/> |本文でバイナリ データを送信する SP.RequestExecutor **POST** 要求 <br/> |要求本文がバイナリ文字列であるかどうかを指定します。 **Boolean**。  <br/> |
|**binaryStringResponseBody** <br/> |バイナリ データを返す SP.RequestExecutor 要求  <br/> |応答がバイナリ文字列であるかどうかを指定します。 **Boolean**。  <br/> |
   

## バッチ ジョブのサポート
<a name="batch"> </a>

SharePoint Online (およびオンプレミスの SharePoint 2016 以降の) REST サービスは、OData  `$batch` クエリ オプションを使用して、複数の要求を組み合わせて 1 つのサービスへの呼び出しにすることをサポートしています。詳細およびコード サンプルについては、「 [REST API によりバッチ要求を発行する](make-batch-requests-with-the-rest-apis.md)」を参照してください。
  
    
    

## その他の技術情報
<a name="bk_addresources"> </a>


-  [REST を使用したリスト アイテムの操作](working-with-lists-and-list-items-with-rest.md)
    
  
-  [REST を使用してフォルダーとファイルを操作する](working-with-folders-and-files-with-rest.md)
    
  
-  [SharePoint 2013 REST API、エンドポイント、およびサンプル](02128c70-9d27-4388-9374-a11bce68fdb8.md)
    
  
-  [SharePoint-Add-in-REST-OData-BasicDataOperations](https://github.com/OfficeDev/SharePoint-Add-in-REST-OData-BasicDataOperations)
    
  
-  [SharePoint 2013: REST を使用してファイルおよびフォルダーに対する基本的なデータ操作を実行する](http://code.msdn.microsoft.com/SharePoint-2013-Perform-ab9c4ae5)
    
  
-  [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)
    
  
-  [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md)
    
  
-  [Open Data Protocol](http://www.odata.org/)
    
  
-  [OData: JavaScript Object Notation (JSON) 形式](http://www.odata.org/documentation/odata-version-2-0/JSON-format/)
    
  
-  [REST インターフェイスを使用してリストにカスタム アクセス許可を設定する](set-custom-permissions-on-a-list-by-using-the-rest-interface.md)
    
  

  
    
    

