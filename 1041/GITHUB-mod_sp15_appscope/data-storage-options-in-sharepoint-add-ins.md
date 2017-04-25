

# SharePoint アドインのデータ ストレージ オプション
SharePoint アドインでデータを格納できるさまざまな方法について説明します。
 * **適用対象:*** 





|||
|:-----|:-----|
|**この記事の内容**          [構造化データのストレージ オプション](#StructuredData)           [非構造化データのストレージ オプション](#UnStructuredData)           [アドインの設定とその他のメタデータ ストレージ オプション](#AppMetadata)           [データ アクセス オプション](#DataAccess)           [その他の技術情報](#AddtionalResources)||
 

## 構造化データのストレージ オプション
<a name="StructuredData"> </a>

SharePoint アドインでは、SharePoint 2013 の内部と外部の両方で、また Microsoft プラットフォーム上と Microsoft 以外のプラットフォーム上で、ほぼすべての種類の構造化データ ストレージを使用できます。以下は、SharePoint アドインの構造化データを格納できる場所の一部です。




- アドイン Web 上の SharePoint リスト


- SQL Azure


- Microsoft Business Connectivity Services (BCS) によって SharePoint に接続された外部データ ソース


- Microsoft 以外のクラウド サービス


- 独自のサーバー上のデータベース



> **ヒント**
> 多くの場合、ある時点でSharePoint アドインをアップグレードします。SharePoint アドインにアドイン Web 上の SharePoint コンポーネントが含まれている場合、アップグレード プロセスによってアドイン Web の完全なコピーが作成されます。このため、アドイン Web 上の非常に大きい SharePoint リストが原因でアップグレード プロセスに時間がかかり、コンテンツ データベース サーバーでプロセッサの負荷が高くなります。アドイン Web の SharePoint リストには "大きなデータ"を配置しないようにする必要があります。 





## 非構造化データのストレージ オプション
<a name="UnStructuredData"> </a>

SharePoint アドインによって生成または使用される、ドキュメント、イメージ、ビデオ、オーディオ ファイル、およびその他の種類の非構造化データは、SharePoint の内部または外部に格納できます。ドキュメント ライブラリはドキュメントに適しており、SharePoint 検索を使用して検索できます。サイト アセット ライブラリは、多くの場合、マルチメディア ファイルに適しています。 



その他のオプションとしては、Microsoft Azure アカウントまたは独自のサーバー上の BLOB ストレージがあります。Microsoft 以外のプラットフォームまたはクラウド サービスにファイルを格納できる場合もあります。




## アドインの設定とその他のメタデータ ストレージ オプション
<a name="AppMetadata"> </a>

ユーザー設定、位置情報、その他の設定などの SharePoint アドインのメタデータは、複数の場所に格納できます。非表示の SharePoint リストが適している場合もあります。また、アドイン Web のプロパティ バッグを使用することもできます。プロバイダーによってホストされるアドインの場合、もう 1 つのオプションは、Microsoft Azure テーブル ストレージを使用することです。 




## データ アクセス オプション
<a name="DataAccess"> </a>

当然ながら、データ アクセスのオプションは、ストレージの選択によって異なります。データ アクセスと検索については、他のいくつかの記事で詳しく説明されています。詳細については、「 [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)」を参照してください。




## その他の技術情報
<a name="AddtionalResources"> </a>


-  [SharePoint アドインのアーキテクチャおよび開発環境に関する重要な要素](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)


-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)


-  [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)





    