---
title: SharePoint アドインを開発するためのツールと環境
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# SharePoint アドインを開発するためのツールと環境
SharePoint アドイン開発環境の作成に関するオプションについて説明します。
SharePoint アドインの開発環境には次の 2 つの基本的なパターンがあります。
  
    
    


- **テストとデバッグ用の SharePoint Web サイトが、SharePoint Onlineサブスクリプション内の Office 365 Web サイト内にある。** 通常、Visual Studio はローカル コンピューターにインストールされますが、クラウド ベースの Visual Studio のオプションもあります。
    
  
- **テストとデバッグ用の SharePoint Web サイトが社内の 1 台のサーバーの SharePoint ファーム上にある。** Visual Studio は同じコンピューターにインストールされます。
    
  

以下の点を考慮してください。
  
    
    


- ご使用の環境の種類に関係なく、作成したほぼすべてのアドインを SharePoint Online か社内 SharePoint ファームに展開できます。一般的な規則として、SharePoint Online に展開できないアドインは、それを使用して開発することもできません (例:  [フル コントロールのアクセス許可](add-in-permissions-in-sharepoint-2013.md)が必要なアドインや [高信頼の認証システム](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)を使用するアドイン)。
    
  
- ご使用の環境の種類に関係なく、SharePoint ホスト型とプロバイダー ホスト型の両方の SharePoint アドインを開発できます。
    
  
- 社内と SharePoint Online の両方にテスト サイトを配置できます。
    
  
- あれこれ考えてみると、2 つのオプションはどちらも同程度に簡単にセットアップできます。
    
  
開発に使用できる SharePoint Online サブスクリプションを使用して **SharePoint Online 環境を作成する場合には** 、「 [既存の Office 365 サブスクリプション内で開発者向けサイトを作成する](create-a-developer-site-on-an-existing-office-365-subscription.md)」をご覧ください。 **社内環境を作成するには** 、「 [SharePoint アドインのオンプレミスの開発環境をセットアップする](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)」を参照してください。
> **メモ**
> このトピックでは、SharePoint アドインを開発するための環境のみ扱います。ファーム ソリューション を開発する計画の場合は、「 [SharePoint 2013 の一般的な開発環境の設定](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx)」を参照してください。両方の開発とも計画している場合は、まず後述の記事を参照してから、SharePoint アドインの開発に必要な追加のステップについて「 [SharePoint アドインのオンプレミスの開発環境をセットアップする](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md)」を参照してください。 
  
    
    


## その他のツールの情報


-  [Napa Office 365 開発ツールを使用して基本的な SharePoint ホスト型アドインを作成する](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)
    
  
-  [Visual Studio で SharePoint アドインを作成する](create-sharepoint-add-ins-in-visual-studio.md)
    
  

## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint アドイン](sharepoint-add-ins.md)
    
  

