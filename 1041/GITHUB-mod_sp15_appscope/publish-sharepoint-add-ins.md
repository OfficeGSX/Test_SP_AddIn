---
title: SharePoint アドインの発行
ms.prod: SHAREPOINT
ms.assetid: f5a92b98-5520-4bba-9131-b56d2a21a321
---


# SharePoint アドインの発行
SharePoint アドインの発行に役立つ記事とリソースを見つけます。
## アドインの発行を開始する
<a name="bk_gettingstarted"> </a>

SharePoint アドインの開発が完了しました。最後の手順は、ユーザーがアドインを取得できるようにすることです。これを行うには、アドインを次の 2 つの場所のいずれかに発行します。




- **パブリック Office ストア。**アプリを Office ストアに発行して、アドインをパブリックに利用できるようにし、SharePoint 展開のユーザーがアドインを取得できるようにします。


- **組織内のアドイン カタログ。**アドインを組織内の SharePoint 展開にホストされているアドイン カタログに発行し、その SharePoint 展開にアクセスするユーザーがアプリを使用できるようにします。


Visual Studio 2012 を使用することによりアドインをパッケージ化して発行する方法の詳細については、「 [Visual Studio を使用して SharePoint アドインを公開する](publish-sharepoint-add-ins-by-using-visual-studio.md)」を参照してください。




### Office ストアに発行する

Office ストア にアドインを公開するには、最初に [マイクロソフトの開発者として登録する](https://sellerdashboard.microsoft.com/Registration)必要があります。 



発行のためにアドインを Office ストアにアップロードしたとき、Microsoft は一連の検証チェックを実行し、アドインがアドインのコンテンツおよび動作に関するガイドラインに準拠していることを確認します。たとえば、アドイン マニフェストのマークアップが有効で完全かどうかのチェックや、アドインに含まれる SharePoint ソリューション パッケージ (.wsp ファイル) に許可されていない要素やスコープが Web の範囲を超える SharePoint 機能が含まれていないかどうかの確認を行います。パッケージに好ましくないコンテンツが含まれているかどうかも調べられます。すべてのテストに合格したアドイン パッケージは、ファイルにラップされ、Microsoft によって署名されます。



発行するアドインを Office ストアにアップロードするとき、アドインをダウンロードするユーザーに提供するライセンスの条件を選択できます。このアドインのライセンスを使用して、次のことを決定します。




- アドインを無料版、試用版、購入版のいずれとして提供するか。


- アドインが、ユーザーごと、あるいはサイトごとのいずれで取得できるか。


SharePoint では、アドインの使用にライセンス条項が適用されませんが、用意されているライセンス フレームワークを使用してアドインにコード ロジックを含めることにより、選択した任意のライセンス制限を適用することができます。たとえば、特定のアドイン機能について、ユーザーの持っているライセンスが有料版ライセンスであればその機能を利用できるようにし、無料版のライセンスであれば利用できないようにするコード ロジックをアドインに含めることができます。詳細については、「 [Office アドインおよび SharePoint アドインのライセンス](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)」を参照してください。




### アドイン カタログへの発行

一般的なユーザーではなく自社のユーザーまたは特定の法人顧客向けに SharePoint アドインを作成する場合、アドインを SharePoint にホストされている内部アドイン カタログに発行できます。プライベート アドイン カタログは、SharePoint アドインおよび Office アドインのドキュメント ライブラリをホストする SharePoint 2013 Web アプリケーション (または SharePoint Online テナント) の専用サイト コレクションです。カタログをその独自のサイト コレクションに配置すると、Web アプリケーションの管理者またはテナントの管理者がカタログに対するアクセス許可を簡単に制限できるようになります。



会社のアドイン カタログへの SharePoint アドインのアップロードは、ファイルを SharePoint ドキュメント ライブラリにアップロードするのと同じように簡単です。アドイン パッケージのローカル URL やアドインの名前などの情報をポップアップ フォームに記入します。アドインをアドイン カタログにアップロードするときには類似のチェックが行われ、不合格のアドインは、カタログで無効のマークが付けられるか、無効にされます。




## SharePoint アドインの発行先を決定する
<a name="bk_decide"> </a>

次の表では、Office ストアへの発行とアドイン カタログへの発行を比較し、アドインの発行先を決定するときの考慮事項を示しています。アドインの設計と開発の前に、アドインの発行先を決定することをお勧めします。アドインの発行先がその設計と開発に影響を及ぼす場合 (ライセンスなど) があります。




**表 1. アドインの発行先を決定するときの考慮事項**


|**Office ストア**|**アドイン カタログ**|
|:-----|:-----|
|アドインはパブリックに利用できます。  <br/> |アドインは SharePoint 展開へのアクセス許可を持つユーザーが利用できます。  <br/> |
|ライセンス フレームワークが使用できます。  <br/> |ライセンス フレームワークが使用できません。  <br/> |
|Microsoft によって、アドイン パッケージの技術およびコンテンツがポリシーに準拠しているかどうかが検証されます。  <br/> |アドインがアップロードされるとき、SharePoint によって、アドイン パッケージが検証されます。  <br/> |
|アドインをアップロードするには、Microsoft Seller Dashboard にサインアップする必要があります。  <br/> |Microsoft に登録する必要はありません。  <br/> |
 

## その他の技術情報
<a name="bk_addresources"> </a>


-  [販売者ダッシュボードでクライアント ID とクライアント シークレットを作成または更新する](http://msdn.microsoft.com/library/f7852781-922f-4499-9dd4-c266907a8c14%28Office.15%29.aspx)


-  [販売者ダッシュボードを使用して Office アドインと SharePoint アドインおよび Office 365 アプリを Office ストアに提出する](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)


-  [Office ストアに提出されたアプリとアドインの検証ポリシー (バージョン 2.0)](http://msdn.microsoft.com/library/cd90836a-523e-42f5-ab02-5123cdf9fefe%28Office.15%29.aspx)


-  [Office 用アプリおよび SharePoint 用アプリの作成を開始する](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)


-  [Office アドインおよび SharePoint アドインのライセンス](http://msdn.microsoft.com/library/3e0e8ff6-66d6-44ff-b0c2-59108ebd9181%28Office.15%29.aspx)


-  [SharePoint アドインの展開とインストール: 方法とオプション](deploying-and-installing-sharepoint-add-ins-methods-and-options.md)


-  [SharePoint アドインのテナントと展開スコープ](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md)


-  [Visual Studio を使用して SharePoint アドインを公開する](publish-sharepoint-add-ins-by-using-visual-studio.md)


-  [Office アドイン ストアを発行する](http://social.msdn.microsoft.com/Forums/ja-jp/officestore)



