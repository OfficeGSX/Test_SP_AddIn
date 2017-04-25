---
title: SharePoint 2013 でホスト Web コンポーネントを更新する
ms.prod: SHAREPOINT
ms.assetid: 9c383a69-c2f6-4702-bd64-e77c9bd026b7
---


# SharePoint 2013 でホスト Web コンポーネントを更新する
SharePoint アドインのホスト Web でアドイン パーツとカスタム アクションを更新します。
## ホスト Web コンポーネントを更新するための前提条件
<a name="Prerequisites"> </a>

「 [SharePoint アドインを更新する](update-sharepoint-add-ins.md)」およびそこに示されている前提条件と中心概念を把握しておく必要があります。




## ホスト Web コンポーネントの更新
<a name="UpdateHostWeb"> </a>

アドインは、カスタム アクションとアドイン パーツという 2 種類のコンポーネントを、SharePoint アドインの説明のマークアップを付けてホスト Web にインストールできます。これらのコンポーネントの更新はアドイン Web よりホスト Web の方が簡単です。更新のセマンティクスは必要ありません。カスタム アクションとアドイン パーツを追加/変更するだけです。SharePoint アドインが更新されると、SharePoint は常に新しい要素マニフェスト ファイルを適用し、変更された要素マニフェスト ファイルに最新バージョンを再適用します。再適用しても問題ありません。たとえば、カスタム アクションのリボン ボタンがリボンに複数回追加されることはありません。



アドイン パーツを更新すると、Web パーツ ギャラリーでは古いバージョンが新しいバージョンで置き換えられます。アドイン パーツを更新する場合は、 **ClientWebPart** オブジェクトの **Name** プロパティを必ず変更してください。このようにすることで、アドインが更新されると、アドイン パーツの古いバージョン (アドインの一部ではなくなっています) が追加先のすべてのページから削除されます。ユーザーは新しいバージョンをページに再追加する必要があります。



 **Name** プロパティを未変更のままにすると、古いバージョンが追加先のページに残るため、アドイン パーツの新しいバージョンが利用できることにユーザーが気付かない可能性が高くなります。また、アドイン パーツが他のページに追加されるとき、追加されるのは新しいバージョンであるため、SharePoint アドインの同じバージョンの異なるページに異なるアドイン パーツが配置されることになります。



 [InstalledEventEndpoint](http://msdn.microsoft.com/library/af9f83d8-8325-3ede-d7b0-bb82c0445eb9%28Office.15%29.aspx) 要素でアドイン マニフェストに登録するリモート イベント レシーバーを使用してプログラムで他の種類のホスト Web コンポーネントを展開できます。もともと **InstalledEventEndpoint** レシーバーで展開されたコンポーネントを更新するには、 [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) レシーバーを使用します。 [UpgradedEventEndpoint](http://msdn.microsoft.com/library/09a93d44-d295-47bb-f91c-d243178b0f53%28Office.15%29.aspx) レシーバーについては、「 [SharePoint アドインで更新イベント用のハンドラーを作成する](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)」で説明されています。




## 次の手順
<a name="Next"> </a>

「 [アドイン更新の主要な手順](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)」に移動するか、SharePoint アドインの次の主要コンポーネントを更新する方法を説明する次の記事の 1 つに直接移動します。




-  [SharePoint 2013 でアドイン Web コンポーネントを更新する](update-add-in-web-components-in-sharepoint-2013.md)


-  [SharePoint アドインで更新イベント用のハンドラーを作成する](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)


-  [SharePoint アドインのリモート コンポーネントを更新する](update-remote-components-in-sharepoint-add-ins.md)



## その他のリソース
<a name="bk_addresources"> </a>


-  [SharePoint アドインを更新する](update-sharepoint-add-ins.md)


-  [SharePoint 2013 のホスト Web、アドイン Web、および SharePoint コンポーネント](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md)



