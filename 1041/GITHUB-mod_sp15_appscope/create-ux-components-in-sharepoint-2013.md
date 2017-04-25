---
title: SharePoint 2013 での UX コンポーネントの作成
ms.prod: SHAREPOINT
ms.assetid: bfdd0a58-2cc5-4805-ac89-4bd2fe6f3b09
---


# SharePoint 2013 での UX コンポーネントの作成
SharePoint 2013 のアドイン向けに UX コンポーネントを作成する方法を説明します。
## SharePoint アドインの UX コンポーネントの作成
<a name="SP15CreateUX_Creating"> </a>

SharePoint アドイン用のモデル は、SharePoint アドインで優れたユーザー エクスペリエンスを作成するために役立つ UX コンポーネントとメカニズムを多数提供しています。アドイン モデルのユーザー エクスペリエンスも十分な柔軟性を備えているため、エンド ユーザーのニーズに最適な方法とプラットフォームを使用できます。表 1 に、アドインでの UX コンポーネントの使用と作成について学習するために役立つ資料を示します。




**表 1. SharePoint アドインでの UX コンポーネントの作成に関する資料およびガイドライン**


|**記事**|**説明**|
|:-----|:-----|
| [SharePoint アドインで SharePoint Web サイトのスタイル シートを使用する](use-a-sharepoint-website-s-style-sheet-in-sharepoint-add-ins.md) <br/> |SharePoint アドインで SharePoint Web サイトのスタイル シートを参照し、それを使用して、SharePoint 2013 のスタイル シートで Web ページのスタイル設定を行うことができます。さらに、SharePoint Web サイトのスタイル シートまたはテーマが変更された場合に、アドイン内のスタイル シート参照を変更することなく、新しいスタイルのセットをアドインに取り込むことができます。  <br/> |
| [SharePoint アドインのクライアント クロム コントロールを使用する](use-the-client-chrome-control-in-sharepoint-add-ins.md) <br/> |SharePoint 2013 でクロム コントロールを使用すると、サーバー ライブラリを登録したり、特定のテクノロジやツールを使用したりすることなく、アドインの特定の SharePoint サイトのヘッダー スタイルを使用できるようになります。この機能を使用するには、標準の <script> タグを介して SharePoint JavaScript ライブラリを登録する必要があります。プレースホルダーを指定するには、HTML の **div** 要素を使用します。また、用意されているオプションを使用して、さらにコントロールをカスタマイズできます。コントロールは、指定された SharePoint Web サイトの外観を継承します。 <br/> |
| [アドイン パーツを作成して SharePoint アドインと共にインストールする](create-add-in-parts-to-install-with-your-sharepoint-add-in.md) <br/> |アドイン パーツを使用すれば、ホスト Web でアドインのユーザー エクスペリエンスを適切に表示することができます。アドイン パーツは、アドインのコンテンツを **IFrame** を使用して表示します。エンド ユーザーは、開発者によってそのアドイン パーツに用意されたカスタム プロパティを使用してエクスペリエンスをカスタマイズできます。アドイン Web ページはカスタム プロパティ値をクエリ文字列内のパラメーターから受け取ります。 <br/> |
| [カスタム アクションを作成して SharePoint アドインで展開する](create-custom-actions-to-deploy-with-sharepoint-add-ins.md) <br/> |SharePoint アドインを作成する場合、カスタム アクションを利用することでホスト Web のリストおよびリボンを操作することが可能になります。カスタム アクションは、エンド ユーザーがアドインをインストールするときにホスト Web に展開されます。カスタム アクションはリモート Web ページを開くことができ、クエリ文字列を使って情報を渡すことも可能です。アドインでは、リボンと、編集コントロール ブロックという 2 種類のカスタム アクションを使用できます。  <br/> |
| [クライアント側レンダリングを使用して SharePoint アドインのリスト ビューをカスタマイズする](customize-a-list-view-in-sharepoint-add-ins-using-client-side-rendering.md) <br/> |クライアント側のレンダリングは、SharePoint ページでホストされる一連のコントロールに対して独自の出力を生成するために使用できるメカニズムです。このメカニズムでは、HTML や JavaScript などのよく知られたテクノロジを使用して、SharePoint リスト ビューのレンダリング ロジックを定義することができます。クライアント側レンダリングでは、独自の JavaScript リソースを指定し、アドインから利用可能なデータ ストレージ オプション (ドキュメント ライブラリなど) でそれらをホストします。  <br/> |
| [SharePoint 用の SharePoint ホスト型アドインでクライアント側ユーザー選択ウィンドウ コントロールを使用する](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md) <br/> |SharePoint アドインでクライアント側ユーザー選択ウィンドウ コントロールを使用する方法について説明します。クライアント側ユーザー選択ウィンドウ コントロールを使用すると、ユーザーは、組織内の人、グループ、およびクレームの有効なユーザー アカウントをすばやく検索し、選択できます。この選択ウィンドウは、クロスブラウザー サポートを提供する HTML および JavaScript コントロールです。  <br/> |
 

## 次のステップ: SharePoint アドインでデータを操作する
<a name="SP15CreateUX_Next"> </a>

アドインに優れた UX をデザインできたら、SharePoint アドイン用のモデル で提供されているメカニズムを使用してデータを組み込みます。詳細については、「 [SharePoint 2013 の外部データの操作](work-with-external-data-in-sharepoint-2013.md)」を参照してください。




## その他の技術情報
<a name="SP15CreateUX_AddRes"> </a>


-  [SharePoint アドイン](sharepoint-add-ins.md)


-  [SharePoint アドインの UX 設計](ux-design-for-sharepoint-add-ins.md)


-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)



