---
title: SharePoint アドインの設計オプションを考慮するときの 3 つの方法
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# SharePoint アドインの設計オプションを考慮するときの 3 つの方法
SharePoint アドインで利用できる設計およびアーキテクチャの選択肢の概要を示します。最初に記事「 [SharePoint アドイン](sharepoint-add-ins.md)」を読んで理解してください。
## 概要と最初の決定
<a name="Overview"> </a>

この記事では、3 とおりの方法で SharePoint アドインのアーキテクチャの選択肢について考えます。最初に、設計の選択肢の最も重要なカテゴリについて説明します。2 つ目に、アプリケーションの階層という観点でアドインのアーキテクチャを確認します。3 つ目に、設計上の決定をする際に考慮する必要がある一連の要素を示します。
  
    
    
ただし、最初に決定する必要があるのは、SharePoint 拡張を、SharePoint アドイン、従来の SharePoint ファーム ソリューション、またはセキュリティで保護されたソリューションのいずれにするかです。主に SharePoint の管理およびセキュリティのカスタマイズと関連する SharePoint オブジェクト モデルの一部には、クライアントからはアクセスできません。SharePoint サーバー上で実行されるカスタム コードからしかそれらにはアクセスできず、SharePoint アドインではカスタム サーバー側コードは許可されません (豊富なクライアント オブジェクト モデルと REST/OData サービスを使用することで、SharePoint アドインでほとんどのエンドユーザー向け SharePoint 拡張を行うことができます)。従来のソリューションとアドインのいずれにするかを決定する方法の詳細については、「 [SharePoint アドインと SharePoint ソリューションの比較](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx)」を参照してください。また、「 [SharePoint 2013 での適切な API セットの選択](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx)」もこの決定に役立ちます。
  
    
    

## SharePoint アドインの設計における重要な要素
<a name="MajorCategoriesOfChoices"> </a>

SharePoint アドインを設計するときに必要な選択には 3 つの主要なカテゴリがあります。例によって、アプリケーションの設計にはトレードオフが伴います。あるカテゴリでの選択によって、別のカテゴリでの選択肢が制限される場合があります。考えられる選択肢の組み合わせすべてが実現可能なわけではありません。
  
    
    

- **ホスティング:** 通常、SharePoint アドインは、その展開およびホストの方法に基づいて、2 つの主要なタイプに分けることができます。
    
  - **プロバイダー ホスト型**アドインでは、開発者が提供するサーバーまたはクラウド アカウント上の SharePoint の外部で、基本的なデータ ストレージとビジネス ロジックが開発者によって展開およびホストされています。アドインを購入する異なる顧客のアカウント間で分離する責任は開発者が負います。このようなアドインには、SharePoint コンポーネントを含めることもできます。これらは、顧客の SharePoint ファームでホストされています。このタイプのアドインでは、設計の選択肢の他のカテゴリにおいて最も高い柔軟性が確保されます。また、外部のデータ、ロジック、および Web ユーザー インターフェイス (UI: User Interface) に Microsoft 以外のプラットフォームを使用することもできます (プロバイダー ホスト型アドインのカテゴリ内では、リモート コンポーネントが SharePoint ファームと同じ会社のファイアウォール内にあるアドインと、リモート コンポーネントがそのファイアウォールの外にあるアドインを区別する必要もあります。これら 2 つのシナリオでは承認システムが異なります。そのため、SharePoint データへのアクセスに使用するプログラミング言語も異なります)。
    
  
  - **SharePoint ホスト型**アドインは、リスト、コンテンツ タイプ、ワークフロー、Web パーツなど、すべて SharePoint 2013 コンポーネントで構成されます。外部コンポーネントは含まれません。SharePoint アドインに含めることができる SharePoint コンポーネントの種類の詳細については、「 [SharePoint 2013 のホスト Web、アドイン Web、および SharePoint コンポーネント](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)」を参照してください。
    
  

    SharePoint アドインのホスティング オプションの詳細については、「 [SharePoint アドインを開発およびホスティングするためのパターンを選択する](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md)」を参照してください。
    
  
- **接続:** SharePoint 2013 では、データに対する 3 種類の安全な作成/読み取り/更新/削除 (CRUD) アクセスがサポートされます。
    
  - アドインに含まれる外部 Web アプリケーションは、OAuth プロトコルを使用して SharePoint データにアクセスします。詳細については、「 [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)」を参照してください。
    
  
  - JavaScript では、安全なクロスドメイン スクリプティングを可能にする特別な JavaScript ライブラリを使用して、SharePoint アドイン Web 上のデータおよび同じテナンシー内の他の Web サイト上のデータにアクセスできます。詳細については、「 [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)」を参照してください。
    
  
  - SharePoint アドインで、Business Connectivity Services (BCS) または Web サービス プロキシを介して外部データにアクセスすることもできます。詳細については、「 [SharePoint 2013 の Business Connectivity Services](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx)」および「 [SharePoint 2013 で Web プロキシを使用してリモート サービスのクエリを実行する](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)」を参照してください。
    
  

    SharePoint アドインにおけるデータの格納とアクセスの詳細については、「 [SharePoint アドインのデータ ストレージ](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data)」、「 [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)」、および「 [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md)」を参照してください。
    
  
- **UI:** SharePoint で SharePoint アドインを表示する方法は 3 とおりあります。少なくとも、すべてのアドインが完全な Web ページで表示されます。オプションで、アドイン パーツを使用して、またメニュー アイテムやリボンのボタンを使用してアドインを表示することもできます。詳細については、「 [SharePoint アドインの UX 設計](ux-design-for-sharepoint-add-ins.md)」を参照してください。
    
    > **メモ**
      > SharePoint アドインは、顧客によって、テナンシー内の複数のサイト コレクションにインストールされたり、Web サイト単位でインストールされたりします。前者はテナントをスコープとするアドインと呼ばれます。テナントをスコープとする選択肢を顧客に提供する場合は、カスタムのリボンのボタンまたはアドイン パーツを含めることはできません。詳細については、「 [SharePoint アドインのテナントと展開スコープ](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)」を参照してください。 

## アーキテクチャの階層
<a name="Tiers"> </a>

アドインのアーキテクチャの選択肢に関する別の考え方では、アドインに UI、ビジネス ロジック、データ アクセスの 3 つの論理階層があると考えます。各レイヤーには、複数の実装の選択肢があります。ここでも、あるレイヤーでの選択によって、別のレイヤーでの選択肢が制限されます。以下の表では、アドインのリモート コンポーネントと SharePoint コンポーネントについて、選択肢の一部とその用途を説明しています。
  
    
    

**プロバイダー ホスト型アドインに含まれるリモート コンポーネント: 階層ごとの選択肢**


|**階層**|**選択肢**|**以下に適合**|
|:-----|:-----|:-----|
|UI  <br/> |ASP.NET 形式の ASP.NET ページまたは Microsoft Azure Web ロールでホストされている MVC アプリケーション。  <br/> |ASP.NET 開発スタッフのスキルの活用。  <br/> |
||JavaScript を使用した HTML 5 ページ。  <br/> |リッチなユーザー インターフェイス。  <br/> |
||Microsoft 以外のクラウド サービスでホストされている PHP またはその他の種類の Web ページ。  <br/> |Microsoft 以外のアプリケーションの SharePoint への統合。  <br/> |
||Windows Phone アプリでの Silverlight。  <br/> |SharePoint データへのモバイル アクセスと、位置情報データおよびプッシュ通知との統合。  <br/> |
|ビジネス ロジック  <br/> |クライアント側 JavaScript。  <br/> |UI ロジックと簡単なビジネス ロジック。  <br/> JavaScript クライアント オブジェクト モデルを通じた SharePoint データへのアクセス。  <br/> |
||Microsoft Azure ワーカー ロール。  <br/> |プロセッサ集約的な機能。  <br/> .NET Framework クライアント オブジェクト モデルを通じた SharePoint データへのアクセス。  <br/> |
||リモート Web サービス。  <br/> |プロセッサ集約的な機能。  <br/> .NET Framework クライアント オブジェクト モデルを通じた SharePoint データへのアクセス。  <br/> |
|データ  <br/> |バリアント型 (SQL Azure) の値を使用します。  <br/> |フル機能のリレーショナル データ。  <br/> |
||Microsoft Azure テーブル ストレージ。  <br/> |アプリケーションの設定およびその他のメタデータ。  <br/> |
||Microsoft Azure BLOB ストレージ。  <br/> |大きなファイルのストレージ。  <br/> |
||Microsoft 以外のクラウド サービス。  <br/> |Microsoft 以外のプラットフォームを基盤とする既存のデータ ソースの活用。  <br/> |
||開発者の独自のサーバー上のデータベース。  <br/> |プロバイダーによるホストと開発者によるテナンシーの分割の制御。  <br/> |
   

**SharePoint コンポーネント: 階層ごとの選択肢**


|**階層**|**選択肢**|**以下に適合**|
|:-----|:-----|:-----|
|UI  <br/> |アドイン Web ページ上の SharePoint 2013 のリストとライブラリのカスタム ビュー。  <br/> |SharePoint の表示および動作との統合の最大化。  <br/> |
||アドイン Web ページ上の Web パーツ (または <object> タグ内) でホストされている Silverlight アプリケーション。  <br/> |既存の Silverlight 開発経験の活用。  <br/> リッチなユーザー インターフェイス。  <br/> |
|ビジネス ロジック  <br/> |SharePoint ワークフロー。  <br/> |ビジネス プロセスの実装。  <br/> |
||SharePoint クロスドメイン ライブラリで補完されたクライアント側 JavaScript。  <br/> |アドイン Web の SharePoint データへのアクセス。  <br/> テナンシー内の他の Web サイトのデータへのアクセス。  <br/> |
||リモート イベント ハンドラー。  <br/> |外部でホストされるロジックを使用した SharePoint リストおよびライブラリの CRUD イベントの処理。  <br/> |
|データ  <br/> |Collaborative Application Markup Language (CAML)、LINQ、またはいずれかの SharePoint クライアント オブジェクト モデルを使用したクエリによって照会される SharePoint 2013のリストおよびライブラリ。  <br/> |既存の SharePoint および .NET Framework 開発経験の活用。  <br/> |
||SharePoint REST/OData Web サービスを通じてクエリされる SharePoint 2013 のリストおよびライブラリ。  <br/> |Microsoft 以外のプラットフォームからの SharePoint データへのアクセス。  <br/> 既存の OData クエリの経験の活用。  <br/> |
||BCS モデル。  <br/> |SharePoint で外部データを SharePoint リストとして表示。  <br/> |
   

## 設計上の決定をする際に考慮すべき要素
<a name="DecisionFactors"> </a>

SharePoint アドイン モデルでは、単純なデシジョン ツリーで対処できないほど多くの設計上の可能性が提供されます。以下に、SharePoint アドインのアーキテクチャを構築する際に考慮すべき最も重要な要素の一部を示します。
  
    
    

- 当然ながら、最も重要なのは、顧客が使用できるようにする機能 (ユース ケース) です。たとえば、ビデオ ファイルを別のビデオ フォーマットに変換するなどの、プロセッサ集約的な機能をアドインに含める場合、そのことが、処理が開発者のサーバーのいずれかまたは Microsoft Azure ワーカー ロールで実行される、プロバイダー ホスト型アドインを作成する根拠となります。
    
  
- SharePoint アドインの 1 つの種類であるプロバイダー ホスト型アドインでは、開発者 (または顧客) が SharePoint 以外のコンポーネントをホストし、テナントの分割を実施する必要があるため、これを実行するハードウェアおよび IT スタッフを用意するかどうか (または、対象の顧客がこれを実行するかどうか) を検討しなければなりません。
    
  
- どのような顧客を対象とするかも非常に重要な考慮事項です。アドインが社内で使用される (つまり、外部のユーザーがいない)、または単一の顧客によって使用される場合、プロバイダー ホスト型アドインの実装と管理は、外部の顧客がいる、または複数の顧客がアドインを使用する場合に比べ大幅に容易になります。アドインを一般に販売する予定である場合は、SharePoint Online アカウントを持つ企業または独自の SharePoint ファームを持つ企業、もしくはその両方のいずれを販売対象とするのかも検討する必要があります。
    
  
- 自分自身または開発スタッフの現在のスキルについても考慮する必要があります。たとえば、自分が経験豊富な ASP.NET 開発者である場合、そのことが、リモート Web アプリケーションを作成し、ASP.NET ページ上に SharePoint リスト データを表示するという方法を選択するポイントとなります。対照的に、自分が経験豊富な SharePoint 開発者である場合、そのことが、カスタムの SharePoint リストおよびサイト ページを使用し、JavaScript で処理を実行するという方法を選択するポイントとなります。
    
  

## その他の技術情報
<a name="AdditionalResources"> </a>


-  [SharePoint アドインの設計](design-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのアーキテクチャおよび開発環境に関する重要な要素](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  

