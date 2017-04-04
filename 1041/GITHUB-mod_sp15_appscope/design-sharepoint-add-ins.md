---
title: SharePoint アドインの設計
ms.prod: SHAREPOINT
ms.assetid: f7ece24a-1684-4a3c-b9ef-814cbf206ca1
---


# SharePoint アドインの設計
SharePoint アドインで使用できる設計およびアーキテクチャのオプションの概要と、SharePoint 2013 へのアドインの展開を容易にするための適切な決定について説明します。
素晴らしいアドインのアイデアがあるとします。このセクションでは、そのアドインを構築するために必要な設計決定について説明し、ベスト プラクティスを提供します。たとえば、どうすれば良いユーザー インターフェイスになりますか? どのようなアドインの「形態」が使用できますか? 2 つの選択肢のうち一方を使用する必要があるのはどんなときですか? データ アクセスには、どのようなオプションがありますか? 
  
    
    


## SharePoint アドインの設計を開始する
<a name="SP15Design_Startdesigning"> </a>

SharePoint 2013 のクラウド アドイン モデルでは、多くの設計オプションが使用できるので、さまざまな形態とサイズの SharePoint アドインが設計できます。このセクションには、アドインのアーキテクチャとユーザー エクスペリエンスを計画および設計するときに決定する必要のあるいくつかの最重要事項 (アドインをホストする方法、アドインが効率的かつ安全にデータにアクセスする方法、実現するユーザー エクスペリエンスなど) に関する有用なガイダンスが含まれています。
  
    
    
SharePoint アドインで使用できる設計およびアーキテクチャのオプションの概要については、「 [SharePoint アドインの設計オプションを考慮するときの 3 つの方法](three-ways-to-think-about-design-options-for-sharepoint-add-ins.md)」を参照してください。SharePoint アドインの概要については、「 [SharePoint アドイン](sharepoint-add-ins.md)」を参照してください。
  
    
    

## アドインに適したホスティング モデルを選択する
<a name="SP15Design_Hostingmodel"> </a>

SharePoint アドインは、複数のホスティング オプションをサポートします。独自の Web スタックを選択することも、Microsoft によって Microsoft Azure および SQL Azure のプロビジョニングが行われるようにすることも、アドインを SharePoint でホストすることもできます。表 1 は、アドインに適したホスティング モデルを選択するときに役立つリソースを示しています。
  
    
    

**表 1. SharePoint アドインに適したホスティング モデルを選択するためのリソースとガイダンス**


|**記事**|**説明**|
|:-----|:-----|
| [SharePoint アドインを開発およびホスティングするためのパターンを選択する](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |SharePoint アドインのコンポーネントをホストするさまざまな方法について説明します。  <br/> |
   

## アドインに適したデータ アクセス テクノロジを選択する
<a name="SP15Design_Dataaccess"> </a>

アドインが効率的かつ安全にデータにアクセスすることを確認する必要があります。さまざまなデータ アクセス テクノロジを使用して、SharePoint へのアクセスとアドインでのデータ処理を実行できます。表 2 は、オプションについての理解とアドインに適したオプションの選択に役立つリソースを示しています。 
  
    
    

**表 2. SharePoint アドインで使用するデータ アクセス テクノロジの選択に関するリソースとガイダンス**


|**記事**|**説明**|
|:-----|:-----|
| [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md) <br/> |SharePoint アドインを構築するときに使用できるデータ アクセス オプションについて説明します。これらのオプションには、入力および出力データ シナリオのデータ接続オプションや、アドインから SharePoint データに接続するときに使用できる API などが含まれます。  <br/> |
   

## アドイン用の UX を設計する
<a name="SP15Design_UX"> </a>

アドインを設計するときの実際の目標として、設計どおりのシナリオをユーザーが完了できるようにするエクスペリエンスを作成する必要があります。表 3 は、ユーザー エクスペリエンス設計のベスト プラクティスに準拠していると同時に、SharePoint 2013 の使い慣れた外観と動作を持つ優れたアドインを構築するときに必要なリソースおよび設計ガイダンスを示しています。
  
    
    

**表 3. SharePoint アドイン向けの優れたユーザー エクスペリエンスを設計するためのリソースとガイダンス**


|**記事**|**説明**|
|:-----|:-----|
| [SharePoint アドインの UX 設計](ux-design-for-sharepoint-add-ins.md) <br/> |SharePoint アドインを構築するときに使用できるユーザー エクスペリエンス オプションについて説明します。  <br/> |
   

## アップグレードを考慮して設計する
<a name="Upgrade"> </a>

更新アドインを作成し、それを Office ストア または組織のアドイン カタログにアップロードする必要が生じる可能性があります。最初のバージョンの設計中にアドインの更新方法について考えておくと、その作業は大幅に簡単になります。設計フェーズの早期に記事「 [SharePoint アドインの更新プロセス](sharepoint-add-ins-update-process.md)」および「 [SharePoint アドインを更新する](update-sharepoint-add-ins.md)」を読んでおくことをお勧めします。 
  
    
    

## 次の手順: アドインを開発して発行する
<a name="SP15Design_Next"> </a>

アドイン用の堅実な設計が完了したら、アドインを構築して発行する準備をします。表 4 に示すリソースは、アドインの構築と発行に役立ちます。
  
    
    

**表 4. SharePoint アドインを開発して発行するためのリソースとガイダンス**


|**記事**|**説明**|
|:-----|:-----|
| [SharePoint アドインの開発](develop-sharepoint-add-ins.md) <br/> |アドイン モデルの高度な概念と機能について説明します。  <br/> |
| [SharePoint アドインの発行](publish-sharepoint-add-ins.md) <br/> |SharePoint アドインの発行のプロセスと要件について説明します。  <br/> |
   

## その他の技術情報
<a name="SP15Design_AddRes"> </a>


-  [SharePoint アドインのサンプル パック](http://code.msdn.microsoft.com/office/Apps-for-SharePoint-sample-64c80184)
    
  
-  [SharePoint 開発の再考](http://msdn.microsoft.com/ja-jp/office/apps/dn133840)
    
  
-  [SharePoint アドイン](sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [アドインに関するブログ](http://blogs.msdn.com/b/spoffapps)
    
  

