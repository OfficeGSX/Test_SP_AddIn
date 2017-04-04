---
title: SharePoint アドインの開発
keywords: vs.sharepointtools.project.sharepointappprojectproperties
f1_keywords:
- vs.sharepointtools.project.sharepointappprojectproperties
ms.prod: SHAREPOINT
ms.assetid: 71ddde4b-fac4-4d8c-aa2e-524f9c2c4c99
---


# SharePoint アドインの開発
SharePoint アドインで高度な機能を構築するために役立つ詳細な記事やリソースを紹介します。
> [!メモ]
> この記事では、「 [SharePoint アドイン](sharepoint-add-ins.md)」という記事と、その記事にリンクが記載されている入門資料にユーザーが通じていることを前提としています。 
  
    
    

 **開発** には、SharePoint アドイン で実行できる各種の作業すべてについて取り上げます。
- 詳しい入門情報
    
  
- ハウツー記事
    
  
- コード スニペット
    
  
以下に関する記事が記されています。 
- リストに対する作成、読み取り、更新、削除 (CRUD) の各操作の実行
    
  
- REST クエリの作成方法と新しい API の操作方法
    
  
- セキュリティのために OAuth を構成する方法と状況
    
  
SharePoint には、エンタープライズ コンテンツ管理機能、LOB 相互運用機能、および Web サイト設計機能に加えて、アクティビティ フィードやユーザー プロファイルなどのエンタープライズ ソーシャル機能が備わっており、これを使用してアドインを容易に差別化できます。詳細については、「 [SharePoint 2013 機能の追加](http://msdn.microsoft.com/library/11ecb65e-6dc5-4cf1-80ca-3c16418697b6%28Office.15%29.aspx)」を参照してください。また、コードが鍵となるため、デベロッパー センターの [サンプル] メニューをご覧ください。これは、アドインのコード サンプルへの直接リンクです。開発環境をセットアップしたら、すぐにいくつかのサンプルをチェックしてください。必要なコード サンプルが提供されていない場合は、コード サンプルをリクエストできるコミュニティ機能をご利用ください。他のドキュメントに関するフィードバックと共にそれらのリクエストを受け取り、今後のコンテンツとサンプルのアップデートに生かします。ご要望がある場合は、ぜひお知らせください。
## SharePoint アドインの開発を開始するためのリソース
<a name="bk_gettingstarted"> </a>

SharePoint アドインの開発をこれから開始しようとしている場合は、最初に「 [SharePoint アドイン](sharepoint-add-ins.md)」に目を通してください。さまざまな種類の SharePoint アドインに早く慣れるための重要な記事が示されています。SharePoint アドインで高度な種類の開発を行う前に、作成するアドインの種類、組み込むテクノロジ、使用するホスティングのオプションについて十分に理解する必要があります。
  
    
    

### クライアント オブジェクト モデル、JavaScript オブジェクト モデル、REST エンドポイントを使用して SharePoint 2013で SharePoint アドインを開発するための基本的なタスクとリソース
<a name="bk_essentials"> </a>

どの種類の SharePoint アドインを作成することに決定したかにかかわらず、アドインは常になんらかの方法で SharePoint 2013 サイトとやり取りします。表 1 に示す記事は、SharePoint アドインで使用できる 3 つのインターフェイス (クライアント オブジェクト モデル、JavaScript オブジェクト モデル、および REST エンドポイント) を使用して SharePoint サイトで多くの重要な種類の作業を行う方法について説明しています。
  
    
    

**表 1. SharePoint 2013 クライアント オブジェクト モデル、JavaScript オブジェクト モデル、および REST インターフェイスを使用した基本的な処理**


|**トピック**|**説明**|
|:-----|:-----|
| [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md) <br/> |C# とクライアント オブジェクト モデルを使用して一般的な処理を行う方法について説明します。  <br/> |
| [SharePoint 2013 の JavaScript ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-javascript-library-code-in-sharepoint-2013.md) <br/> |JavaScript オブジェクト モデルを使用して一般的な処理を行う方法について説明します。  <br/> |
| [SharePoint 2013 REST エンドポイントを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-rest-endpoints.md) <br/> |REST インターフェイスを使用して一般的な処理を行う方法について説明します。  <br/> |
   

## SharePoint アドインでの開発に関する基本概念について
<a name="bk_fundamentals"> </a>

基本的な処理について理解することに加えて、SharePoint 2013のアドイン開発モデルの基本概念を理解する必要があります。どの種類の SharePoint アドインも、アドイン マニフェスト ファイルを含み、アドイン パッケージとしてビルドされます。ビルドしたアドイン パッケージを SharePoint 2013 サイトに展開します。どの種類のアドインでも、展開時には、認証と承認、データ アクセス、およびユーザビリティに関連したさまざまな問題を考慮する必要があります。これらの問題については、表 2 に示す記事を参照してください。作成する各種の に対するこれらの問題の影響について説明しています。
  
    
    

**表 2. SharePoint アドインを開発するための基本概念**


|**トピック**|**説明**|
|:-----|:-----|
| [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md) <br/> |SharePoint 2013のリソースで開発するために必要な権限の取得に関連した中心概念について説明します。  <br/> |
| [SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる](explore-the-app-manifest-structure-and-the-package-of-a-sharepoint-add-in.md) <br/> |アドイン マニフェストの機能とアドイン パッケージの作成方法について説明します。  <br/> |
| [SharePoint 2013 での UX コンポーネントの作成](create-ux-components-in-sharepoint-2013.md) <br/> |SharePoint アドインでリッチなユーザー エクスペリエンスを構築する方法を探ります。  <br/> |
| [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md) <br/> |さまざまな種類の SharePoint アドインで使用可能なデータ アクセス オプションとデータ アクセス方法について説明します。  <br/> |
| [Office アドインおよび SharePoint アドインのライセンス](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx) <br/> |Office アドインと SharePoint アドインのアドイン ライセンス フレームワークについて説明します。  <br/> |
   

## さまざまな機能の統合: 機能を統合して高度な SharePoint アドインを構築する
<a name="bk_integrate"> </a>

SharePoint アドインの機能や特性に慣れている場合は、すべての機能を要件を合うように統合して複雑なアドインを構築できます。表 3 に示す記事は、機能を統合して高度な SharePoint アドインを作成する方法について説明しています。
  
    
    

**表 3. SharePoint アドインの高度な概念**


|**トピック**|**説明**|
|:-----|:-----|
| [SharePoint のカスタムのリストおよびコンテンツ タイプを含む、プロバイダー向けのホスト型アドインを作成する](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md) <br/> |クラウドでホストされ、カスタムの SharePoint リストとコンテンツの種類を使用している SharePoint アドインの作成方法について説明します。  <br/> |
   

## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint アドインを開発するためのツールと環境](tools-and-environments-for-developing-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの設計](design-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの発行](publish-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのサンプル パック](http://code.msdn.microsoft.com/ja-jp/office/Apps-for-SharePoint-sample-64c80184)
    
  

