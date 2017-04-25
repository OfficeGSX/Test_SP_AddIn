---
title: SharePoint オブジェクト モデルの概要を簡単に説明する
ms.prod: SHAREPOINT
ms.assetid: 6b8b55f8-9370-43a0-af8d-e07d1028a075
---


# SharePoint オブジェクト モデルの概要を簡単に説明する
SharePoint オブジェクト モデルのいくつかの主なクラスの概要について説明します。
これは、プロバイダー ホスト型 SharePoint アドイン の開発の基本に関する記事のシリーズの 4 番目です。 [SharePoint アドイン](sharepoint-add-ins.md) とこのシリーズの前の記事をよく理解しておいてください。





-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [プロバイダー向けのホスト型アドインに SharePoint の外観を付与する](give-your-provider-hosted-add-in-the-sharepoint-look-and-feel.md)


-  [プロバイダー向けのホスト型アドインにカスタム ボタンを含める](include-a-custom-button-in-the-provider-hosted-add-in.md)



> **メモ**
> プロバイダー ホスト型アドインに関するこのシリーズを続けて学習している場合は、このトピックを続行するために利用できる Visual Studio ソリューションがあります。また、 [SharePoint_Provider-hosted_Add-Ins_Tutorials](https://github.com/OfficeDev/SharePoint_Provider-hosted_Add-ins_Tutorials) でリポジトリをダウンロードして、BeforeSharePointWriteOps.sln ファイルを開くこともできます。




この記事では、コーディングから少し離れて、SharePoint クライアント側オブジェクト モデル (CSOM) の概要を確認します。このモデルは、MSDN 内で大量にきちんと文書化され、リファレンス トピック、「方法」、コード サンプルが提供されています。この記事では、そのほんの一部しか説明できません。ただし、ほんの少しの説明で、このシリーズで使用されるコードの大半がわかりやすくなります。 
## コンテンツの階層

次の表は、SharePoint とそれらを表す CSOM クラスのコンテンツの階層を示します。これらのエンティティは、それぞれそのすぐ下に子の型があります。





|**エンティティ**|**クラス**|**注釈**|
|:-----|:-----|:-----|
|SharePoint オンプレミス ファームまたは SharePoint Online サブスクリプション (テナントとも呼ばれます)  <br/> ||CSOM のこのレベルへのプログラムによるアクセスは非常に制限されています。たとえば、ファーム、サブスクリプション、テナントクラスはありません。(SharePoint のサーバー側のオブジェクト モデルは、アドインで利用できませんが、これらのエンティティへのプログラムによるアクセスを有効にします。)  <br/> |
|サイト コレクション  <br/> |**Site** <br/> |ブランド化されたマスター ページや、カスタム セキュリティ グループなど、すべての子 Web サイトに適用できる、主に管理上の理由、または SharePoint コンポーネントを収容するためにまとめられた Web サイトのコレクション。すべての Web サイトは、何らかのサイト コレクションに属しています。  <br/> |
|Web サイト  <br/> |**Web** <br/> |ページと SharePoint コンポーネントのセット。サブ Web サイトを持つことがあります。  <br/> |
|リスト  <br/> |**List** <br/> |ドキュメント ライブラリとその他の種類のファイルのライブラリも、このレベルです。  <br/> ドキュメント ライブラリは、各行には添付されたドキュメントが含まれ、作成者、最終編集日時、チェックアウトしたユーザーなど、ドキュメントに関するデータをその他の列に持つ、特別な種類のリストです。  <br/> |
|リスト アイテム  <br/> |**ListItem** <br/> |行のフィールドに特定の値を持つ、リストの行、つまりリスト アイテム。型もあります。次の行を を参照してください。  <br/> |
|リスト アイテム  <br/> |**Content Type** <br/> |リスト アイテムの型。これらは **ContentType** クラスによって表されます。それぞれ基本的には列とメタデータのセットです。最もシンプルなものは、組み込みの **Item** コンテンツ タイプです。その他のすべてのコンテンツタイプは、 **Item** から派生します。SharePoint には、Event や Announcement など、多数の組み込みのコンテンツ タイプがあります。 <br/> |
|列  <br/> |**Field** <br/> |**Field** オブジェクトには、元になるデータ型に関する情報だけでなく、特定のリスト アイテムの作成、表示、編集のためのフォームなど、データがフォームで書式設定されてレンダリングされる方法に関する情報も含まれます。 <br/> |
 




カスタムのリスト、コンテンツ タイプ、列タイプ、リスト アイテムをプログラムによって作成できます。 



CSOM によって、コンテンツだけでなく、ユーザー、グループ、ロールとアクセス許可、分類、検索などにアクセスできます。




## クライアント側のランタイムとバッチ処理
<a name="CSOMBatching"> </a>

CSOM はバッチ処理システムを使用します。マネージ コードのブロックが XML に変換され、1 つの HTTP 要求でサーバーに送信されます。コマンドごとに、対応するサーバー オブジェクト モデルの呼び出しが行われ、サーバーは、JavaScript Object Notation (JSON) 形式でクライアントへ応答を返します。 



クライアントの SharePoint コードは、SharePoint の Web サイト (および親サイト コレクション) の ID を含む現在の要求コンテキストを表すクライアント コンテキスト オブジェクトを取得して開始され、このコンテキストを通じて、CSOM オブジェクトへのアクセスを取得できます。以下は、何度も目にすることになる基本的な構造です。このコードについて、以下の点に注意してください。




-  `spContext` オブジェクトは **SharePointContext** 型であり、Office Developer Tools for Visual Studio によって生成される SharePointContext.cs (または .vb) ファイルで定義されます。このファイルは変更できますが、変更しなければならないことはそう多くありません。ほとんどの SharePoint アドイン プロジェクトで、このファイル、およびツールによって生成される TokenHelper.cs (または .vb) ファイルが、CSOM 自体の機能拡張として効率的に機能します。


-  `clientContext` オブジェクトは、CSOM 型 **ClientContext** です。


- **ExecuteQuery** メソッドは、CRUD 操作コードと XML メッセージをまとめて SharePoint サーバーに送信します。ここで、同等のサーバー側のオブジェクト モデルのコードに変換されて実行されます。




```cs

using (var clientContext = spContext.CreateUserClientContextForSPHost())
{
    // CRUD operation or query code goes here.

    clientContext.ExecuteQuery();
}```

このシリーズの前の記事で、このパターンの例が、以下に示す  `GetLocalEmployeeName`メソッドにありました。このメソッドについて、以下の点に注意してください。




- **using** ブロックの最初の 2 行は、リストおよびリスト アイテム オブジェクトへの参照を取得します。ただし実際には、これらの行が SharePoint のクライアント側のランタイムで実行される場合、これらは単純に XML 形式に変換されます。 **ExecuteQuery** メソッドは、その XML をサーバーに送信します。


- **Load** メソッドは、メッセージにほかのものを追加します。指定されたオブジェクトをクライアントに送信するようサーバーに通知します。 **ExecuteQuery** メソッドは、このオブジェクトを (JSON として) 受信し、クライアント側の `selectedLocalEmployee` 変数を初期化するために使用します。後続のクライアント側のコードは、その **ListItem** オブジェクトとメンバーを参照します。ご覧のとおり、次の行はアイテムの [タイトル] フィールドの値を参照します。オブジェクトは読み込まれるまで実際にはクライアント側に存在しないため、この行は、 **Load** メソッドが呼び出されない場合に例外をスローします。




```cs

private string GetLocalEmployeeName()
{
    ListItem localEmployee;

    using (var clientContext = spContext.CreateUserClientContextForSPHost())
    {
        List localEmployeesList = clientContext.Web.Lists.GetByTitle("Local Employees");
        selectedLocalEmployee = localEmployeesList.GetItemById(listItemID);
        clientContext.Load(selectedLocalEmployee);
        clientContext.ExecuteQuery();
    }
    return localEmployee["Title"].ToString();
}```


## 
<a name="Nextsteps"> </a>

 次の記事では、コーディングに戻り、SharePoint にデータを書き込む方法について説明します。 [プロバイダー向けのホスト型アドインに SharePoint の書き込み操作を追加する](add-sharepoint-write-operations-to-the-provider-hosted-add-in.md)




