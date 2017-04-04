---
title: SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる
ms.prod: SHAREPOINT
ms.assetid: 7cd5850f-cbf3-48d2-bcb7-59b8f4ed0e63
---


# SharePoint アドインのアプリ マニフェスト構造とパッケージを調べる
SharePoint アドインのアドイン パッケージの構造とマニフェスト ファイルについて説明します。
## SharePoint アドイン パッケージの構造
<a name="Package"> </a>

SharePoint アドインパッケージは、".app" 拡張子を持つファイルで、 [Open Packaging Conventions (OPC)](http://msdn.microsoft.com/ja-jp/magazine/cc163372.aspx) でコンパイルするファイルです。このパッケージには、以下のアイテムが含まれます。
  
    
    

- **アドイン マニフェスト:** これは、appmanifest.xml という名前の必須ファイルです。このファイルは、アドインの重要なプロパティ (タイトルや実行するために必要なアクセス許可など) を SharePoint 2013 に通知します。このファイルの内容の詳細については、「 [SharePoint アドインのマニフェスト ファイル](#AppManifest)」を参照してください。
    
  
- **SharePoint ソリューション パッケージ:** オプションで、アドイン Web のコンポーネントを格納する SharePoint ソリューション パッケージ (.wsp ファイル) をアドインに含めることができます。これらのコンポーネントには、ページ、リスト インスタンス、ビュー、ドキュメント、 **Web** 範囲フィーチャーなどの SharePoint 2013 コンポーネントを配置できます (SharePoint アドインに含めることができる SharePoint コンポーネントの詳細については、「 [SharePoint アドインに含めることができる SharePoint コンポーネントの種類](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps)」を参照してください)。.wsp ファイルには、Office アドインを保存することもできます。.wsp ファイルのコンポーネントは、アドイン Web に展開されます。SharePoint ソリューション パッケージを含むアドイン パッケージの例については、「 [SharePoint のカスタムのリストおよびコンテンツ タイプを含む、プロバイダー向けのホスト型アドインを作成する](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md)」を参照してください。
    
  
- **カスタム アクションまたはアドイン パーツ付きのホスト Web フィーチャー:** アドイン Web に展開される SharePoint 2013 コンポーネントに加え、SharePoint アドインは、1 つ以上のカスタム アクション (ショートカット メニュー アイテムやリボン拡張など) をホスト Web に展開することもできます。これは、パッケージの .wsp ファイルには含まれないが、ホスト Web に配置されるコンポーネントを展開するフィーチャーをアドイン パッケージに配置することで実現されます。この "ルーズな" フィーチャーは、ホスト Web フィーチャーと呼ばれます。アドイン パーツは、ホスト Web に同じ方法で展開されます。ホスト Web フィーチャーは、標準的な SharePoint 2013 の feature.xml ファイルと、1 つ以上の関連付けられた elements.xml ファイルで構成されます。たとえば、カスタム アクション用の elements.xml ファイルには、カスタム アクション用の **CustomAction** マークアップが記述されます。アドイン パーツ用のマークアップを記述することもできます。ホスト Web フィーチャーには、この 2 種類のコンポーネントのみを含めることができます。これらのホスト Web フィーチャーは、アドイン マニフェストにはアイテム化されません。ただし、これらは OPC では "パーツ" と見なされるため、アドイン マニフェストとこれらの各ファイルの間には明示的な OPC リレーションシップがあります。ホスト Web フィーチャーがあるアドイン パッケージの例については、「 [カスタム アクションを作成して SharePoint アドインで展開する](create-custom-actions-to-deploy-with-sharepoint-add-ins.md)」をご覧ください。
    
    > **メモ**
      > テナント管理者には、SharePoint アドインを複数の Web サイトにバッチ インストールするというオプションがあります。この方法でインストールされているアドインには、 **Tenant** 範囲が設定されていると見なされます。バッチ インストールではなく各 Web サイトに別々にインストールされているアドインには、 **Web** 範囲が設定されます。ホスト Web フィーチャーに含まれているリボン拡張やアドイン パーツは、アドインをバッチ インストールした場合はホスト Web には展開されないため、ショートカット メニュー アイテムのみが Tenant 範囲のアドインに展開されます。アドイン範囲とフィーチャー範囲を混同しないでください。フィーチャー範囲は、フィーチャー内の要素の展開先を決定します。可能なオプションは **Farm**、 **WebApplication**、 **Site** (サイト コレクション)、および **Web** です。SharePoint アドインのフィーチャー (ホスト Web フィーチャーとアドイン パッケージの .wsp 内のフィーチャーの両方) で許可されるのは **Web** オプションのみです。アドイン範囲は、アドインがインストールされる範囲を指します。可能なオプションは **Web** (1 つ以上の Web サイトに対して、サイトごとにインストールされるアドインの場合) および **Tenant** (顧客のテナント内のすべてまたは一部の Web サイトに対して、バッチ インストールされるアドインの場合) です。 **Tenant** 範囲と **Web** 範囲の詳細については、「 [SharePoint アドインのテナントと展開スコープ](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)」を参照してください。 
- **ローカライズ リソース ファイル (.resx) :** これらは、アドインのタイトルとアドイン パッケージのホスト Web フィーチャーを含むアドイン マニフェストをローカライズするためのファイルです (.wsp ファイル、Azure Web サイト パッケージ、アドイン マニフェストなどの独自のパッケージに含まれるアドイン パッケージの個々のパーツには、それぞれ独自のローカライズ プロセスがあり、それらのプロセスは対象のアイテムが SharePoint アドイン のパーツではない場合とまったく同じように適用されます)。ホスト Web フィーチャー用の .resx ファイルがあるアドイン パッケージの例については、「 [SharePoint アドインをローカライズする](localize-sharepoint-add-ins.md)」を参照してください。
    
  
- **Office アドイン マニフェスト:** オプションで、それぞれが Office アドイン をパッケージ化する 1 つ以上の Office アドイン マニフェストが含まれることがあります。このパーツは、アドインが公式 Marketplace で販売されるのではなく SharePoint 2013 コーポレート アドイン カタログにアップロードされる場合のみ、アドイン パッケージに含めることができます。詳細については、「 [SharePoint アドインの発行](publish-sharepoint-add-ins.md)」を参照してください。
    
  

## SharePoint アドインのマニフェスト ファイル
<a name="AppManifest"> </a>

すべての SharePoint アドインには、appmanifest.xml ファイルがあります。appmanifest.xml は、アドインについて知る必要があるデータを SharePoint に通知し、アドインの最も重要なプロパティを定義します。このマニフェストに指定されるアイテムの一部を次に示します。
  
    
    

- アドインの内部名、製品 ID、およびバージョン。
    
  
- スタート ページの URL。これは、アドインの起動時に開くページです。アドイン Web 内のページ、クラウド ベースのページ、または ISV の Web サーバー上のページを使用できます。
    
    > **メモ**
      > 場合によっては、 **StartPage** 要素で指定できるファイルの種類が制限されることがあります。詳細については、「 [スタート ページ要素 (PropertiesDefinition コントロール (マニフェスト アドインの SharePoint)](http://msdn.microsoft.com/library/3092674c-a6c3-9021-3d7e-e716562a4a4f%28Office.15%29.aspx)」を参照してください。 > 複数のクエリ パラメーターを **StartPage** 値で組み合わせて使用する場合は、" `&amp;`" やセミコロンではなく、コード化されたアンパサンド " `&amp;amp;`" を使用する必要があります。 
- アドインのその他のプロパティ。これには、アドインでサポートされるタイトルとロケール (両方とも必須)、インストール後、アップグレード後、アンインストール前の各イベントを処理するサービスの URL、およびアドイン Web の作成時に使用する Web テンプレートがあります。
    
  
- アドインが必要とする、アドイン Web の外部の SharePoint リソースに対するアクセス許可要求。
    
  
- 認証と承認を目的とするアドイン プリンシパルの ID。アクセス許可を付与されるのはこのプリンシパルです。これは、SharePoint ホスト型アドインでは必要ありません。
    
  
- アドインをインストールするために、アドインから利用できるようにする必要がある前提条件のリスト (該当する場合)。たとえば、特定の機能をインストールしてアクティブ化する必要がある場合や、特定のサービスのライセンスを取得してインストールする必要がある場合があります。
    
  

> **メモ**
> アドイン マニフェスト ファイルはアドイン パッケージの唯一の必須アイテムですが、前述したリストのアイテムは、そのすべてがこのファイルの必須パーツというわけではありません。 
  
    
    

アドイン マニフェスト マークアップの詳細については、「 [SharePoint アドインのマニフェストのスキーマに関するリファレンス](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)」ノードを参照してください。必須の要素と属性に関する情報を含めて、このトピックはそのノードの情報の代わりになるものではありません。また、SharePoint アドイン マニフェストのスキーマは Office アドイン マニフェストのスキーマと異なることに注意してください。後者のスキーマについては、「 [Office アドインのマニフェスト向けのスキーマ リファレンス (v1.1)](http://msdn.microsoft.com/library/7e0cadc3-f613-8eb9-57ef-9032cbb97f92%28Office.15%29.aspx)」を参照してください。
  
    
    
この後、appmanifest.xml ファイルの例を示します。この例では、アドインのスタート ページはリモート サーバー上の ASP.NET ページであり、SharePoint サイト上のページではないことに注意してください。このページの URL には、リモート Web アプリケーションにホスト Web の URL を渡すクエリ文字列が含まれます。文字列の "{HostUrl}" 部分は、アドインの起動時に解決されるトークンです。このアドインは、ホスト Web 内のすべてのリストに対する Write アクセス許可を要求します。このアクセス許可を付与する必要があるアドイン プリンシパルはリモート Web アプリケーションです。
  
    
    
アドイン マニフェストでは、 **SupportedLocales** 要素または **SupportedLanguages** 要素を使用する必要があります。 **SupportedLocales** に移行したため、 **SupportedLanguages** は使用されなくなりました。 **SupportedLanguages** 要素はリリース後も引き続き機能しますが、使用しないようにしてください。これらの要素の詳細については、「 [SupportedLocales 要素 (PropertiesDefinition コントロール (SharePoint アドイン マニフェスト)](http://msdn.microsoft.com/library/49bde91a-8d7a-be17-4c91-82c9c19f0f61%28Office.15%29.aspx)」および「 [SupportedLanguages 要素 (PropertiesDefinition コントロール (マニフェスト アドインの SharePoint)](http://msdn.microsoft.com/library/7a8da886-5731-9abd-2911-5cd268bba4cf%28Office.15%29.aspx)」を参照してください。
  
    
    

> **メモ**
> **AppPermissionRequest** 要素の **Scope** 属性の値は URI のような構成になつていますが、実際にはリテラル文字列です。次の例に含まれる **Scope** 値の例では、どの部分もプレースホルダーではありません。アクセス許可の詳細については、「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」を参照してください。 
  
    
    




```XML

<?xml version="1.0" encoding="utf-8" ?>
<App xmlns="http://schemas.microsoft.com/sharepoint/2012/app/manifest"
     ProductID="{4a07f3bd-803d-45f2-a710-b9e944c3396e}"
     Version="1.0.0.0"
     SharePointMinVersion="15.0.0.0"
     Name="MySampleApp"
>
  <Properties>
    <Title>My Sample App</Title>
    <StartPage>http://MyRemoteWebApplicationServer/default.aspx/?SPHostUrl={HostUrl}</StartPage>
    <SupportedLocales>
      <SupportedLocale CultureName="en-US" />
    </SupportedLocales>        
  </Properties>

  <AppPermissionRequests>
    <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web/list" Right="Write"/>
  </AppPermissionRequests>

  <AppPrincipal>
    <RemoteWebApplication ClientId="1ee82b34-7c1b-471b-b27e-ff272accd564" />
  </AppPrincipal>
</App>

```


### アドイン マニフェスト内の URL トークン

SharePoint 2013 には、アドインの **StartPage** 要素の中やその他の場所、またはアドインのコンポーネントの中で、アドインが実行されるまではわからない情報を表すために使用できるさまざまなトークンが用意されています。これらのトークンは SharePoint 2013 インフラストラクチャによって解決されます。一部は URL の先頭で使用され、一部は URL の中でクエリ パラメーターの値などとして使用できます。これらのトークンと、それ以外の多様なトークンは、SharePoint 2013 のさまざまな開発コンテキストでも使用できます。すべてのトークンとそれらを使用できる場所の詳細については、「 [SharePoint アドインの URL 文字列とトークン](url-strings-and-tokens-in-sharepoint-add-ins.md)」を参照してください。SharePoint 2013 の他のトークンと URL の概要については、「 [SharePoint 2013 の URL とトークン](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)」を参照してください。
  
    
    

> **メモ**
> これらのトークンは、 **AppPermissionRequest** 要素の **Scope** 属性では使用されません。
  
    
    


## その他の技術情報
<a name="SP15Exploreappmanifest_bk_addlresources"> </a>


-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインのアーキテクチャおよび開発環境に関する重要な要素](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)
    
  
-  [SharePoint アドインのマニフェストのスキーマに関するリファレンス](http://msdn.microsoft.com/library/1f8c5d44-3b60-0bfe-9069-1df821220691%28Office.15%29.aspx)
    
  
-  [OPC: データのパッケージ化のための新しい標準](http://msdn.microsoft.com/ja-jp/magazine/cc163372.aspx)
    
  
-  [データ層アプリケーション接続 (DAC)](http://msdn.microsoft.com/ja-jp/library/ee210546.aspx)
    
  
-  [Web Deploy 2.0](http://www.iis.net/downloads/microsoft/web-deploy)
    
  

