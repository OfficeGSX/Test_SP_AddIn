---
title: SharePoint アドインの URL 文字列とトークン
ms.prod: SHAREPOINT
ms.assetid: 800ec8cd-a448-46bc-b41e-d4030eeb4048
---


# SharePoint アドインの URL 文字列とトークン
SharePoint アドインで使用できる URL トークンについて
> [!重要]
> SharePoint 2013での URL の作成方法および作成した URL でのトークンの使用に関する一般的な情報については、「 [SharePoint 2013 の URL とトークン](http://msdn.microsoft.com/library/161418d7-8123-4c4e-91a1-97e43c17f0e6%28Office.15%29.aspx)」を参照してください。このトピックでは、SharePoint アドインで使用できるトークンについて説明します。 
  
    
    


## 
<a name="URLtokens"> </a>

SharePoint 2013では、SharePoint アドインで使用できるトークンとして、以下の表に示すトークンがサポートされています。
  
    
    
このセクションの表に示されたトークンは、カスタム アクションやカスタム ページのリンクなど、SharePoint アドイン を開発する際のさまざまな状況の URL で使用できます。場合によっては、使用できないトークンもあります。一部のトークンのみが使用できる 3 つの重要な場所として、アドインの開始ページ、ホスト Web のカスタム アクション、およびアドイン パーツの  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) プロパティがあります。これらについては別の列で説明されていますが、 * **トークンを使用できるのはこれら 3 つの場所だけではありません。*** 
  
    
    
" **StartPage** " 列では、アドイン マニフェストの **StartPage** 要素にトークンを使用できるかどうかを指定します。" **カスタム アクション** " 列では、ホスト Web のカスタム アクションの URL にトークンを使用できるかどうかを指定します。" **アドイン パーツ** " 列では、アドイン パーツの [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) プロパティにトークンを使用できるかどうかを指定します。
  
    
    

**SharePoint アドインで URL の先頭に使用できるトークン**


|**トークン**|**解決される結果**|**StartPage**|**カスタム アクション**|**アドイン パーツ**|**注釈**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|~appWebUrl  <br/> |SharePoint アドインのアドイン Web の URL  <br/> |○  <br/> |○  <br/> |○  <br/> |このトークンは、アドイン Web の外部でのみ使用してください。アドイン Web 自体の内部では、アドイン Web の URL として **~site** を使用します。 <br/> |
|~controlTemplates  <br/> |現在の Web サイトの ControlTemplates 仮想フォルダーの URL  <br/> |×  <br/> |×  <br/> |×  <br/> ||
|~hostUrl  <br/> |ホスト Web の URL。  <br/> |×  <br/> |×  <br/> |○  <br/> ||
|~hostLogoUrl  <br/> |ホスト Web のロゴの URL。  <br/> |×  <br/> |×  <br/> |×  <br/> ||
|~layouts  <br/> |現在の Web サイトの Layouts 仮想フォルダーの URL  <br/> |×  <br/> |×  <br/> |発  <br/> ||
|~remoteAppUrl  <br/> |SharePoint アドイン内のリモート Web アプリケーションの URL  <br/> |○  <br/> |ホスト Web の場合は○、アドイン Web の場合は×。  <br/> |○  <br/> |SharePoint アドインの開発に Microsoft Office Developer Tools for Visual Studio を使用していない場合、 **StartPage** URL で **~remoteAppUrl** を使用することはできません。ただし、Visual Studio とそのツールを使用している場合、プロバイダーホスト型アドインにこのトークンを使用できます。これは、Visual Studio がアドインをパッケージ化するときに解決されます。この使用方法の場合、SharePoint トークンというよりも、Visual Studio トークンに近くなります。これは、Microsoft Office Developer Tools for Visual Studio を使用していない場合であっても、アドイン マニフェスト以外で使用できます。 <br/> |
|~site  <br/> |現在の Web サイトの URL  <br/> |×  <br/> |×  <br/> |○  <br/> ||
|~sitecollection  <br/> |現在の Web サイトの親サイト コレクションの URL  <br/> |×  <br/> |×  <br/> |○  <br/> ||
   
特記がない場合、次の表のトークンは、アドイン パーツの  [Src](https://msdn.microsoft.com/library/Microsoft.SharePoint.WebControls.SPAppIFrame.Src.aspx) プロパティ値の *path*  部分に使用できません。" **アドイン パーツ** " は、値の *クエリ文字列*  部分での使用を示しています。
  
    
    

**URL の途中で使用できるトークン**


|**トークン**|**解決される結果**|**StartPage**|**カスタム アクション**|**アドイン パーツ**|**注釈**|
|:-----|:-----|:-----|:-----|:-----|:-----|
|{AppContextToken}  <br/> |アドインの OAuth コンテキスト トークン。  <br/> |×  <br/> |×  <br/> |×  <br/> ||
|{AppWebUrl}  <br/> |SharePoint アドイン内のアドイン Web の URL  <br/> |○  <br/> |○  <br/> |○  <br/> |このトークンは、アドイン Web の外部でのみ使用してください。アドイン Web 自体の内部では、アドイン Web の URL として **{Site}** を使用します。 <br/> |
|{ClientTag}  <br/> |現在の Web サイトのクライアント キャッシュ コントロール番号 (クライアント タグ)。  <br/> |○  <br/> |○  <br/> |○  <br/> ||
|{HostLogoUrl}  <br/> |SharePoint アドインのホスト Web のロゴ  <br/> |○  <br/> |○  <br/> |○  <br/> ||
|{HostTitle}  <br/> |SharePoint アドインのホスト Web のタイトル  <br/> |○  <br/> |○  <br/> |○  <br/> ||
|{HostUrl}  <br/> |SharePoint アドインのホスト Web の URL  <br/> |○  <br/> |○  <br/> |○  <br/> ||
|{ItemId}  <br/> |リストまたはライブラリ内の項目の ID (整数)。  <br/> |×  <br/> |○  <br/> |×  <br/> ||
|{ItemUrl}  <br/> |作用対象のアイテムの URL  <br/> |×  <br/> |○  <br/> |×  <br/> ||
|{Language}  <br/> |SharePoint アドインのホスト Web に設定されている現在の言語/カルチャ  <br/> |○  <br/> |○  <br/> |○  <br/> ||
|{ListId}  <br/> |現在のリストの ID (GUID)  <br/> |×  <br/> |○  <br/> |×  <br/> ||
|{ProductNumber}  <br/> |SharePoint ファームの完全なビルド バージョン番号。  <br/> |○  <br/> |○  <br/> |○  <br/> |値の例は "15.0.4433.1011" です。  <br/> |
|{RecurrenceId}  <br/> |再帰イベントの再帰インデックス  <br/> |×  <br/> |○  <br/> |×  <br/> |このトークンは、リスト アイテムのコンテキスト メニューでの使用がサポートされていません。  <br/> |
|{RemoteAppUrl}  <br/> |SharePoint アドイン内のリモート Web アプリケーションの URL  <br/> |○  <br/> |○  <br/> |○  <br/> ||
|{Site}  <br/> |現在の Web サイトの URL  <br/> |×  <br/> |○  <br/> |○  <br/> ||
|{SiteCollection}  <br/> |現在の Web サイトの親サイトの URL  <br/> |×  <br/> |○  <br/> |○  <br/> ||
|{SiteUrl}  <br/> |現在の Web サイトの URL  <br/> |×  <br/> |○  <br/> |×  <br/> ||
|{Source}  <br/> |HTTP 要求の URL。  <br/> |×  <br/> |○  <br/> |×  <br/> ||
|{StandardTokens}  <br/> |注釈を参照してください。  <br/> |○  <br/> |○  <br/> |○  <br/> |これは他の 5 個のトークンと組み合わせます。まず、 `SPHostUrl={HostUrl}&amp;SPAppWebUrl={AppWebUrl}&amp;SPLanguage={Language}&amp;SPClientTag={ClientTag}&amp;SPProductNumber={ProductNumber}` に解決されます。次に、これらの各トークンが解決されます。 アドイン Web がない場合、 `&amp;SPAppWebUrl={AppWebUrl}` の部分はありません。 <br/> |
   

## その他の技術情報
<a name="SP15URLstrings_bk_addlresources"> </a>


-  [高度なエクストラネット サポート](http://msdn.microsoft.com/library/21d67796-23c5-4339-8f0e-124208d21ab2%28Office.15%29.aspx)
    
  
-  [サイト、Web アプリケーション、およびその他の主要オブジェクトへの参照を取得する](http://msdn.microsoft.com/library/8623ef1d-e3cc-426c-84a3-6379e0ae284f%28Office.15%29.aspx)
    
  
-  [Working with List Objects and Collections (英語)](http://msdn.microsoft.com/library/d4167b10-6f1e-49f1-8b22-16ce20012a27%28Office.15%29.aspx)
    
  
-  [サンプル オブジェクト モデルのタスク](http://msdn.microsoft.com/library/94d6898d-6a0f-43a7-ad06-1b27ec6916ea%28Office.15%29.aspx)
    
  

