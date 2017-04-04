---
title: SharePoint アドインのリモート コンポーネントを更新する
ms.prod: SHAREPOINT
ms.assetid: 53679118-61af-4f25-91f4-a69af38ee6d0
---


# SharePoint アドインのリモート コンポーネントを更新する
SharePoint アドインのリモート Web アプリケーションとデータベースを更新します。
## SharePoint 用アドインのリモート コンポーネント更新の前提条件
<a name="Prerequistes"> </a>

「 [SharePoint アドインを更新する](update-sharepoint-add-ins.md)」およびそのトピックに記載されている前提条件と主要な概念について理解します。
  
    
    

## リモート コンポーネントを更新する
<a name="UpdateRemote"> </a>

プラットフォームやテナンシー システムには大きな違いがあるので、リモート コンポーネントの更新の大部分に関しては、ごく一般的なアドバイスしかできません。以下の 2 つのセクションで、ガイダンスをいくつかをご紹介します。
  
    
    

### プロバイダーでホストされたアドインでリモート コンポーネントを更新する
<a name="UpdateProviderHosted"> </a>

プロバイダーでホストされた SharePoint アドインでは、コンポーネントがホストされているプラットフォームの最善の更新手法を使用してリモート コンポーネントを更新します。プロバイダーでホストされるアドインのリモート コンポーネントが SharePoint アドイン自体のインストールとは別にインストールされるのと同様に、リモート コンポーネントは個別に更新されます。次の点を考慮してください。
  
    
    

- 更新されたリモート コンポーネントは、SharePoint アドインの以前のすべてのバージョンに対して引き続き動作する必要があります。
    
  
- リモート コンポーネント用のテナンシー分離システムを実装した場合、別のテナントがアドインの複数のバージョンを使用していて、特定のテナントではさまざまな SharePoint Web サイトに異なるバージョンがインストールされている可能性もあることに気を付けます。
    
  
Microsoft Azure SQL データベースまたは SQL Server を使用する、プロバイダーでホストされた SharePoint アドインについては、データベースを更新するために複数の方法があります。手始めに「 [Upgrade a Data-tier Application](http://msdn.microsoft.com/library/c117df94-f02b-403f-9383-ec5b3ac3763c.aspx)」を参照してください。
  
    
    

## 次の手順
<a name="Next"> </a>

「 [アドイン更新の主要な手順](update-sharepoint-add-ins.md#MajorAppUpgradeSteps)」に戻るか、次のいずれかの記事に直接移動して、SharePoint アドインの次の主要なコンポーネントを更新する方法を参照してください。
  
    
    

-  [SharePoint 2013 でアドイン Web コンポーネントを更新する](update-add-in-web-components-in-sharepoint-2013.md)
    
  
-  [SharePoint 2013 でホスト Web コンポーネントを更新する](update-host-web-components-in-sharepoint-2013.md)
    
  
-  [SharePoint アドインで更新イベント用のハンドラーを作成する](create-a-handler-for-the-update-event-in-sharepoint-add-ins.md)
    
  

## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint アドインを更新する](update-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの更新プロセス](sharepoint-add-ins-update-process.md)
    
  

