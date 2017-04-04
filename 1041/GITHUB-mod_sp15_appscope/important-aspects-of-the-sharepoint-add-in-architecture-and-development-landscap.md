---
title: SharePoint アドインのアーキテクチャおよび開発環境に関する重要な要素
ms.prod: SHAREPOINT
ms.assetid: ae96572b-8f06-4fd3-854f-fc312f7f2d88
---


# SharePoint アドインのアーキテクチャおよび開発環境に関する重要な要素
SharePoint アドインのアーキテクチャと SharePoint アドイン用のモデルの概要について、特にアドインのホスティング オプション、ユーザー インターフェイスのオプション、展開システム、セキュリティ システム、ライフサイクルについて説明します。この記事は、記事「 [SharePoint アドイン](sharepoint-add-ins.md)」の情報を補足します。
## SharePoint アドインのホスティング オプション
<a name="SPAppModelArch_SPCenteredVsCloudCentered"> </a>

SharePoint 2013 のアドイン モデルには、SharePoint アドイン コンポーネントをホストするための次のような方法があります。 
  
    
    

- **プロバイダーホスト型:** 少なくとも 1 つのリモート コンポーネントを含むアドイン。SharePoint コンポーネントも含まれる場合があります。SharePoint 以外のコンポーネントが、ユーザーのハードウェアまたはクラウドのアカウントのロジックによって展開されたり、ユーザーが提供するインストール プログラムと手順書を使用してお客様のハードウェアまたはクラウド アカウントに展開されたりします。
    
  
- **SharePoint ホスト型:** SharePoint コンポーネントとクライアント上で実行されるロジックのみを含むアドイン。
    
  
ホスティング オプションの詳細とそれらの選択方法のガイダンスについては、「 [SharePoint アドインを開発およびホスティングするためのパターンを選択する](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)」を参照してください。
  
    
    

## アドイン Web、ホスト Web、フィーチャー、アドイン内の SharePoint コンポーネント
<a name="SPComponents"> </a>

SharePoint アドインがインストールされる Web サイトは、"ホスト Web" と呼ばれます。ただし、SharePoint アドインの重要なパーツである SharePoint コンポーネントと外部コンポーネントは、どちらもホスト Web には展開されません。外部パーツは外部のサーバーまたはクラウド アカウントに展開されます。SharePoint コンポーネントは自身のドメインの特別な Web サイトに展開されます。これらは "アドイン Web" と呼ばれます。ホスト Web には、アドインの他のコンポーネントにアクセスする手段をユーザーに提供する少数の UI 要素のみが展開されます。ホスト Web 内のこれらの UI コンポーネントは、ホスト Web フィーチャーの一部として展開されます。このフィーチャーはアドイン パッケージに結合されておらず, .wsp ファイル内にはありません。アドイン Web に展開されるコンポーネントは常に .wsp ファイル内のフィーチャーになります。どちらの種類のフィーチャーも **Web** スコープである必要があります。その他のスコープは SharePoint アドインのフィーチャーでは使用できません。
  
    
    
一般的に、SharePoint サーバーで実行されるカスタム コードを含まない SharePoint コンポーネントはすべて SharePoint アドインに含めることができます (そしてアドイン Web に展開できます)。ただし、コンポーネントを展開する方法と場所については、いくつかの例外と細かい注意点があります。これらの注意点およびホスト Web、分離されたアドイン Web、アドインのフィーチャーの詳細については、「 [SharePoint 2013 のホスト Web、アドイン Web、および SharePoint コンポーネント](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)」を参照してください。
  
    
    

## UI からのアドインへのアクセス
<a name="AccessingApp"> </a>

SharePoint アドインが Web サイトにインストールされると、アドインはホスト Web の [ **サイト コンテンツ**] ページに一覧表示されます。ユーザーはこのページからアドインを起動できます。このようにして開くと、アドインは全画面表示モードで実行されます。 
  
    
    
その他にアドイン パーツを介して SharePoint アドインを表示する方法があります。アドイン パーツは、 **ClientWebPart** クラスで表される Web パーツの一種です。このタイプの Web パーツは本質的に IFrame のラッパーで、アドインのページをホストします。最も単純なケースでは、Web パーツの重要なプロパティは、ページを指し示す URL ただ 1 つです。ただし Web パーツにはユーザーがツール パーツで設定できるカスタム プロパティを設定することができます。このようなプロパティは、たとえばユーザーの郵便番号などのコンテキスト情報の設定に利用できます。このようなアドイン パーツをアドインに含めるには、アドイン内でホスト Web フィーチャーを作成し、宣言型の Web パーツ マークアップを追加します。他の Web パーツと同様に、アドイン パーツは SharePoint 2013 UI に表示され、そこからユーザーは Web パーツを追加できます。さらにバリエーションを持たせる場合は、2 つ以上のアドイン パーツをアドインで展開できます。たとえば、天気予報のアドインであれば、現在の天気を表示するアドイン パーツと、週間予報を表示する 2 つ目のアドイン パーツを持たせることができます。2 つのパーツは異なる大きさと機能にすることができます。
  
    
    

> **メモ**
> アドイン Web にアドイン パーツを展開することもできます。これを実装するには、Web パーツのマークアップを、ホスト Web のフィーチャーではなく、アドイン パッケージの .wsp ファイル内のフィーチャーに含めます。 
  
    
    

アドインは可能な範囲で SharePoint の外観に合わせることをお勧めしますが、これは必須ではなく、最善の選択であるとも限りません。ユーザー エクスペリエンスのガイドラインの詳細については、「 [SharePoint アドインの UX 設計](ux-design-for-sharepoint-add-ins.md)」を参照してください。 
  
    
    
たとえば、app.master という名前の特殊なマスター ページがあります。このページはアドインのページからの使用に最適化されています。app.master ページは、SharePoint 2013 に含まれている新しいサイト定義の一部です。 
  
    
    
アドインが SharePoint と調和した外観を維持するために使用できる別のツールとして、SharePoint 2013 に付属するクロム コントロールがあります。このコントロールにより、アドイン ページ (外部にホストされるページを含む) に SharePoint ナビゲーション ヘッダー領域を追加できるようなります。SharePoint アドインの UX デザインの詳細については、「 [SharePoint アドインの UX 設計](ux-design-for-sharepoint-add-ins.md)」を参照してください。クロム コントロールの詳細については、「 [SharePoint アドインのクライアント クロム コントロールを使用する](use-the-client-chrome-control-in-sharepoint-add-ins.md)」を参照してください。
  
    
    

## アドインのパッケージ構成
<a name="SPAppModelArch_Package"> </a>

SharePoint アドイン パッケージは拡張子 ".app" を持つファイルで、 [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/ja-jp/magazine/cc163372.aspx) に準拠しています (ファイル名に追加拡張子として ".zip" を追加することによってエクスプローラーでファイルを開くことができます)。パッケージには、アドインの特定のプロファイルの指定や SharePoint インストール インフラストラクチャへの指示を行うアドイン マニフェストが含まれています。アドイン マニフェストとパッケージの詳細については、「 [SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)」を参照してください。
  
    
    

## SharePoint アドインのアクセス許可、認証、承認
<a name="SPAppModelArch_Running"> </a>

SharePoint 2013 には、アドインの新しいアクセス許可とセキュリティ システムが導入されています。
  
    
    

### アドインのアクセス許可
<a name="AppPermissions"> </a>

SharePoint アドインはユーザーやグループと同様にアクセス許可を持ちます。これによりアドインは、アドインを実行したユーザーのアクセス許可とは異なる一連のアクセス許可を保持できます。 
  
    
    
アドイン マニフェストではアドインの実行に必要なアクセス許可を要求する必要があります。アドインを追加するユーザーはこれらの要求を許可する必要があり、ユーザーは自分がユーザーとして所有しているアクセス許可のみを与えることができます。ユーザーと開発者によるアクセス許可の管理を単純にするため、権限は要求されたすべてのアクセス許可に対して付与されるか、またはまったく付与されません (アドイン プリンシパルはアドイン Web に対して常にフル コントロールの権限を持つため、ホスト Web やその他のアドイン Web 以外の場所の SharePoint リソースに対するアクセス許可の要求のみが必要です)。
  
    
    
アドインの権限の詳細については、「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」を参照してください。
  
    
    

### 選択的委任と承認
<a name="SelectiveAuthorization"> </a>

アドインを起動するユーザーと、リソースにアクセスする権限をアドインに付与するリソース所有者のどちらも資格情報またはパスワードをアドインに提供する必要はありません。SharePoint 2013 では、ユーザーおよびリソース所有者は、アドインが要求する特定のアクセス許可のみを付与できます。これを可能にするのが SharePoint 2013 によるトランザクション プロトコル  [OAuth 2.0](http://tools.ietf.org/html/draft-ietf-oauth-v2-22) の使用です。SharePoint 2013 の OAuth の詳細については、「 [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)」を参照してください。
  
    
    

### クロスドメイン アクセス
<a name="SelectiveAuthorization"> </a>

データ アクセス ロジックに JavaScript を使用するリモート Web アプリケーションを含む SharePoint アドインは、アドインがインストールされるテナンシー内の SharePoint データへの承認されたアクセスに JavaScript クロス ドメイン ライブラリを使用できます。詳細については、「 [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)」を参照してください。
  
    
    

## アドインのライフ サイクル
<a name="SPAppModelArch_Lifecycle"> </a>

SharePoint アドインのライフサイクルには、発行、インストール、アップグレード、アンインストールが含まれます。このトピックの詳細については、「 [SharePoint アドインの発行](publish-sharepoint-add-ins.md)」、「 [SharePoint アドインの展開とインストール: 方法とオプション](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)」、および「 [SharePoint アドインの更新プロセス](sharepoint-add-ins-update-process.md)」を参照してください。テナントの管理者が複数の Web サイトに SharePoint アドインを一括インストールできるメカニズムもあります。詳細については、「 [SharePoint アドインのテナントと展開スコープ](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)」を参照してください。
  
    
    

## SharePoint アドインのデータ ストレージ
<a name="Data"> </a>

SharePoint アドインは、構造化データ、ドキュメント、マルチメディア ファイルなどの任意のデータを作成し、それらにアクセスできます。これらのデータは、SharePoint 内または外部に保存できます。 
  
    
    

### 構造化データのストレージ オプション
<a name="StructuredData"> </a>

SharePoint アドインでは、SharePoint 2013 の内部と外部の両方で、また Microsoft プラットフォーム上と Microsoft 以外のプラットフォーム上で、ほぼすべての種類の構造化データ ストレージを使用できます。以下は、SharePoint アドインの構造化データを格納できる場所の *一部*  です。
  
    
    

- アドイン Web の SharePoint リスト
    
  
- SQL Azure
    
  
- Microsoft Business Connectivity Services (BCS) によって SharePoint に接続された外部データ ソース
    
  
- Microsoft 以外のクラウド サービス
    
  
- 独自のサーバー上のデータベース
    
  

> **ヒント**
> 多くの場合、いつか SharePoint アドインをアップグレードすることになります。SharePoint アドインにアドイン Web 上の SharePoint コンポーネントが含まれている場合、アップグレード プロセスによってアドイン Web の完全なコピーが作成されます。このため、アドイン Web 上に非常に大きい SharePoint リストがあると、アップグレード プロセスに時間がかかり、コンテンツ データベース サーバーでプロセッサの負荷が高くなります。アドイン Web の SharePoint リストには "ビッグ データ" を配置しないようにする必要があります。 
  
    
    


### 非構造化データのストレージ オプション
<a name="UnStructuredData"> </a>

SharePoint アドインによって生成または使用される、ドキュメント、イメージ、ビデオ、オーディオ ファイル、およびその他の種類の非構造化データは、SharePoint の内部または外部に格納できます。ドキュメント ライブラリはドキュメントに適しており、SharePoint 検索を使用して検索できます。サイト アセット ライブラリは、多くの場合、マルチメディア ファイルに適しています。 
  
    
    
その他のオプションとしては、Microsoft Azure アカウントまたは独自のサーバー上の BLOB ストレージがあります。Microsoft 以外のプラットフォームまたはクラウド サービスにファイルを格納できる場合もあります。
  
    
    

### アドインの設定とその他のメタデータ ストレージ オプション
<a name="AppMetadata"> </a>

ユーザー設定、位置情報、その他の設定などの SharePoint アドインのメタデータは、複数の場所に格納できます。非表示の SharePoint リストが適している場合もあります。また、アドイン Web のプロパティ バッグを使用することもできます。プロバイダー ホスト型アドイン用の別の選択肢は、Microsoft Azure テーブル ストレージを使用することです。 
  
    
    

### セキュリティで保護されたデータ アクセス オプション
<a name="DataAccess"> </a>

当然ながら、セキュリティで保護されたデータ アクセスのオプションは、ストレージの選択によって異なります。データ アクセスと検索については、他のいくつかの記事で詳しく説明されています。詳細については、「 [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)」を参照してください。
  
    
    

## アドインの管理
<a name="SPAppModelArch_Managing"> </a>

サイト コレクションの管理者およびテナントの管理者は、アドインを監視してアドインに割り当てられるリソースを変更できます。また、Microsoft のアドイン ストア担当者はアドインにフラグを付けたり無効化したりできます。
  
    
    
アドインの管理の詳細については、TechNet の「 [SharePoint 2013 アドインをインストールおよび管理する](http://msdn.microsoft.com/ja-jp/library/733647a3-a5d3-475b-967d-3bb627c2a0c2)」を参照してください。
  
    
    

### アドインの監視
<a name="SPAppModelArch_Monitoring"> </a>

SharePoint 2013 はアドインの健全性を監視し、Web サイト所有者、テナント管理者、ファーム管理者がこの情報を UI で参照できるようにします。監視システムのほとんどのドキュメントは TechNet に収録されています (例: 「 [SharePoint アドインを監視する](http://technet.microsoft.com/ja-jp/library/3adafdd2-f276-4a9d-8a74-e06b8916bbc2)」)。このセクションでは販売するアドインの監視方法の概要について簡単に説明します。
  
    
    
アプリごとに報告されるデータと、アプリのインスタンスごとに報告されるデータがあります。監視フレームワークが報告する主な項目には次のものがあります。
  
    
    

- アドインのインストール回数 (新しいインスタンスの作成数) などのアドインの使用状況。 
    
  
- アドイン インスタンスごとのサーバー リソース使用量。
    
  
- アドイン インスタンスごとのインストール、アップグレード、実行時のエラー。
    
  
- 緑色、黄色、赤色を使用した、アドイン インスタンスごとの全体的な健全性指標
    
  
アドインに Azure Web サイト コンポーネントが含まれる場合、監視フレームワークはエラー データについて 1 時間ごとに Microsoft Azure をポーリングし、SharePoint 2013 UI に重大なエラーとストレージ クォータ データを表示します。Microsoft Azure SQL データベース のエラーは報告されません。
  
    
    
管理者は、監視フレームワークから提供される情報によってアドイン購入予算の支出が賢く行われているか、より多くのリソースをアドインに展開する必要があるか、正常に動作していないアドインを無効にする必要があるかを判断できます。
  
    
    

## アドインの依存関係の登録
<a name="RegisterDependency"> </a>

SharePoint アドインが依存している SharePoint 機能が利用できず、アドイン Web で利用できるようにすることもできない場合、アドインは正常に動作せずに、顧客から苦情を受けることになります。アドインの依存関係をアドイン マニフェストに登録することによって、必須のサービスとフィーチャーが利用できない場所にアドインがインストールされるのを防ぐことができます。SharePoint アドインのインストール インフラストラクチャによって必須コンポーネントがチェックされ、いずれかのコンポーネントが利用できない場合はアドインのインストールが阻止されます。
  
    
    
Excel Services、Access Services、Visio Services などのサービスに関しては、そのサービスがインストールされていて、ライセンスされているかどうかが確認されます。
  
    
    
タスク リストなどのフィーチャーに関しては、そのフィーチャーが展開されていて、次のどちらかに当てはまるかが確認されます。
  
    
    
-- **Farm**、 **WebApplication**、または **Site** (サイト コレクション) スコープでアクティブ化されている。
  
    
    
または
  
    
    
-- アドインのインストール時に作成されるアドイン Web 上で、 **Web** スコープでアクティブ化可能になっている。
  
    
    

> **メモ**
> これらのフィーチャーは、アドイン Web の作成時に、アドイン インストール インフラストラクチャによってアドイン Web 上で自動的にアクティブ化されます。 
  
    
    

以下のセクションで、必須コンポーネントの登録に必要な詳細について説明します。
  
    
    

### アクセス許可要求を使用して依存関係を暗黙的に登録する
<a name="PermAsPreq"> </a>

アドイン Web の外部にある SharePoint コンポーネントにアクセスする必要がある場合、アドインはアドイン マニフェストの **AppPermissionRequests** セクションで該当リソースへのアクセス許可を要求する必要があります。このアクセス許可要求は、必須コンポーネントを登録する役割も果たします。SharePoint はアドインが要求するアクセス許可から、アドインが特定の SharePoint 機能を必要としていることを推測するからです。多くの場合、SharePoint はアドインに必要な機能をすべて推測できるので、このトピックのこれ以降のセクションは必要ありません。ただし、依存関係を余分に登録しても支障はありません。
  
    
    

### AppPrerequistes を使用して依存関係を明示的に登録する
<a name="Explicit"> </a>

アドインの依存関係がアクセス許可要求から推測できない場合は、アドイン マニフェストの **AppPrerequisite** 要素で各依存関係を登録します。この要素には、 **Type**、 **ID**、および (オプションの) **MinimumVersion** という 3 つの属性があります。
  
    
    
 **Type** に指定できる必須コンポーネントの値は、 `Feature`、 `Capablility`、 `AutoProvisioning` の 3 つです。Feature は、アドイン Web 上またはアドイン Web を含むより広い範囲で展開およびアクティブ化する必要がある SharePoint フィーチャーです。Capability は、アドイン Web で利用できるようにする必要がある関連フィーチャーとサービスのセットです ( `AutoProvisioning` については次のセクションで説明します)。
  
    
    
オプションの **MinimumVersion** には、アドインに必要なフィーチャーまたは機能の最小バージョンを指定します。この属性値の形式は n.n.n.n になります (例: `15.0.0.0`)。
  
    
    
 **ID** には、必要なフィーチャーまたは機能を指定します。 **Type** が `Feature` の場合、 **ID** は角かっこで囲んだハイフン付きのフィーチャーの GUID になります (例: `{151D22D9-95A8-4904-A0A3-22E4DB85D1E0}`)。 **Type** が `Capability` の場合、 **ID** は機能の GUID になります。次に機能を示します。機能の GUID を検索するには、「 [AppPrerequisite 要素 (AppPrerequisiteCollection コントロール (SharePoint アドイン マニフェスト)](http://msdn.microsoft.com/library/791be402-981f-519e-fcde-f24cc3cb4139%28Office.15%29.aspx)」を参照してください。
  
    
    

- Access Services 2010
    
  
- Access Services
    
  
- Managed Metadata Web Service
    
  
- PowerPoint Services
    
  
- Secure Store Services
    
  
- Machine Translation Service
    
  
- User Profile Service
    
  
- Visio Graphics Service
    
  
- Work Management Service
    
  
- Duet
    
  
- Workflow
    
  
- Search
    
  
- EDU
    
  
次に示すのは、ワークフロー機能を登録する生の **AppPrerequisites** マークアップの例です。Visual Studio を使用している場合は、デザイナー ツールでアドイン マニフェストを編集します。
  
    
    


```

<AppPrerequisites>
  <AppPrerequisite Type="Capability" ID="{CDD8F991-B459-4512-8048-03D5A03FF27E}" MinimumVersion="15.0.0.0" />
</ AppPrerequisites>```


## このセクションの内容
<a name="RegisterDependency"> </a>


-  [SharePoint アドインを開発およびホスティングするためのパターンを選択する](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)
    
  
-  [SharePoint 2013 のホスト Web、アドイン Web、および SharePoint コンポーネント](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)
    
  

## その他の技術情報
<a name="SPAppModelArch_AdditionalResources"> </a>


-  [SharePoint アドイン](sharepoint-add-ins.md)
    
  
-  [SharePoint アドインと SharePoint ソリューションの比較](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)
    
  
-  [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)
    
  
-  [SharePoint のアドインのコンテキスト トークン OAuth フロー](context-token-oauth-flow-for-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの UX 設計](ux-design-for-sharepoint-add-ins.md)
    
  
-  [IFrame ](http://www.w3schools.com/tags/tag_iframe.asp)
    
  
-  [SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md)
    
  
-  [SharePoint アドインの展開とインストール: 方法とオプション](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)
    
  
-  [SharePoint アドインの更新プロセス](sharepoint-add-ins-update-process.md) 。
    
  
-  [SharePoint アドインのテナントと展開スコープ](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)
    
  

