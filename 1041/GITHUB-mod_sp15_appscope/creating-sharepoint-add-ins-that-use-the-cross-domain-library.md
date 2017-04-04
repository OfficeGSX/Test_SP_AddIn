---
title: クロス ドメイン ライブラリを使用する SharePoint アドインの作成
ms.prod: SHAREPOINT
ms.assetid: e63411ef-7137-4f1c-aac5-a3414ec88154
---


# クロス ドメイン ライブラリを使用する SharePoint アドインの作成
SharePoint クロス ドメインJavaScript ライブラリについて説明します。
低信頼承認システムと高信頼承認システムのいずれも SharePoint アドインで使用できないか、アドインが SharePoint リソースへの承認を得る唯一の手段としてふさわしい選択ではないシナリオがいくつかあります。たとえば、次のような場合です。
  
    
    


- SharePoint アドインのリモート コンポーネントがオンプレミスではなく、企業のファイアウォールが SharePoint と ACS のサーバー間通信をブロックしているため、低信頼システムを使用することができない。
    
  
- SharePoint アドインが、SharePoint でのデータ処理にクライアント側の JavaScript に依存する単一ページの Web アプリケーションとして設計されている。
    
  
- SharePoint アドインは、主にサーバー間の呼び出しに依存して、SharePoint のデータにアクセスします (また、低信頼システムまたは高信頼システムによって承認されます) が、いくつかの JavaScript 呼び出しによって補足する必要があります。たとえば、グラフィックの負荷が高いページで表示されるデータに小さな更新を行うには、JavaScript を使用することにより、ページ全体の再読み込みなしで行うことができます。
    
  

ただし、 [セキュリティのために](http://msdn.microsoft.com/ja-jp/library%28d=robot%29/cc709423(d=robot,l=ja-jp,v=vs.85).aspx)、ブラウザーはあるドメインにホストされている JavaScript が別のドメインのリソースにアクセスすることを許可しないため、リモートの JavaScript が SharePoint のリソースにアクセスするには、特別なテクニックが必要です。SharePoint クロス ドメイン JavaScript ライブラリを使用すると、リモート Web アプリケーションでこのテクニックを簡単に使用できるようになります。
  
    
    


> **メモ**
> クロス ドメイン ライブラリは、データを逆方向でアクセスできるようにする場合にも使用されます。つまり、SharePoint ページの JavaScript がリモート ドメインのデータにアクセスできるようにします。詳細については、「 [SharePoint ページからのリモート データへのアクセス](#ReverseDirection)」を参照してください。 
  
    
    


## クロス ドメイン ライブラリのアーキテクチャの理解

SharePoint クロス ドメイン ライブラリは、すべての SharePoint Web サイトの /_layouts/15/ 仮想フォルダーに配置される SP.RequestExecutor.js ファイルに含まれています。このファイルのスクリプトは、ブラウザーのクロス ドメイン スクリプトに関する制限を解消するために、既知の安全なテクニックをカプセル化します。iFrame は、iFrame のページが別のドメイン内にある場合でも、 `window.postMessage()` 関数を使用して親ページと通信できます。このため、データの要求と応答は、 `postMessage()` への呼び出しを使用することで、ドメインの境界を越えて渡されます。
  
    
    

> **注意**
>  `postMessage()` 関数は HTML 5 をサポートするブラウザーでのみ動作するため、クロス ドメイン ライブラリを使用する SharePoint アドインは古いブラウザーでは動作しません。
  
    
    

SharePoint では、クロス ドメイン ライブラリはリモート Web アプリのページに読み込まれ、そのページに SharePoint ドメインから特別なプロキシ ページをホストする非表示の iFrame を作成します。プロキシ ページはすべての SharePoint Web サイトに既に存在します。ライブラリは、SharePoint の REST API への CRUD 呼び出しを行うために必要な情報が含まれる JavaScript Object Notation (JSON) オブジェクトの作成のために使用されます。JSON オブジェクトは、 `postMessage()` を使用してプロキシ ページに渡されます。プロキシ ページではライブラリも読み込まれ、JSON オブジェクトは SharePoint への REST 呼び出しとして解析されて再構築されます。プロキシ ページは SharePoint ドメイン内にあるため、ブラウザーは呼び出しを許可します。
  
    
    
もちろんそれでも、SharePoint アドインのリモート コンポーネントには、SharePoint リソースに対して承認されたアクセス権が必要です。そのためには、次の 2 つの方法があります。
  
    
    

- アドイン マニフェストで、アドインのプリンシパルの種類を **RemoteWebApplication** (プロバイダー向けのホスト型アドインの既定) に設定します。アドインが ACS に登録されるとき、その登録にはリモート Web アプリケーションのドメインが含まれます。SharePoint は、このシナリオで、サーバー側の低信頼システムの一部であるフローを渡すトークンの一部を使用して、ACS に登録されたドメインを信頼します。アドインの登録の詳細については、「 [SharePoint アドイン 2013 を登録する](register-sharepoint-add-ins-2013.md)」を参照してください。 
    
  
- SharePoint ホスト型アドインでは、アドインのプリンシパルの種類を既定の **Internal** に設定したままにできます。その後、 **Internal** 要素の **AllowedRemoteHostUrl** 属性を、次の図に示すようにリモート Web アドインの URL に設定します。
    
  ```
  
<AppPrincipal>
  <Internal AllowedRemoteHostUrl="https://example.com/Home.html" />
</AppPrincipal>
  ```


> **メモ**
> 2 番目のオプション ( **Internal** のアドイン プリンシパル) を使用する場合、JavaScript とクロス ドメイン ライブラリのみを使用して、SharePoint にアクセスできます。SharePoint クライアント オブジェクト モデルは、 **Internal**SharePoint アドインではブロックされるため、クロス ドメイン ライブラリと、低信頼システムまたは高信頼システムのいずれかを使用するデュアル承認システムを持つことはできません。 
  
    
    

ライブラリの使用方法の詳細については、「 [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)」を参照してください。
  
    
    

## SharePoint ページからのリモート データへのアクセス
<a name="ReverseDirection"> </a>

SharePoint クロス ドメイン ライブラリは逆方向でも使用できます。つまり、SharePoint ページの JavaScript がライブラリを使用して、アドインのリモート コンポーネントからのデータを取得できます。そのためには、クロス ドメイン アーキテクチャを逆にし、リモート Web アプリケーションでプロキシ ページを作成します。ライブラリは、プロキシ ページをホストするために iFrame を作成する SharePoint ページから呼び出されます。この方法でライブラリを使用する方法の詳細については、「 [SharePoint 2013 のクロスドメイン ライブラリのカスタム プロキシ ページを作成する](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)」を参照してください。
  
    
    

## このセクションの内容
<a name="ReverseDirection"> </a>


-  [クロスドメイン ライブラリを使用してアドインから SharePoint 2013 のデータにアクセスする](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)
    
  
-  [SharePoint アドインで Internet Explorer の異なるセキュリティ ゾーンを横断してクロスドメイン ライブラリを操作する](work-with-the-cross-domain-library-across-different-internet-explorer-security-z.md)
    
  

## その他の技術情報
<a name="ReverseDirection"> </a>


-  [SharePoint アドインでクロス ドメインの問題を解決する](http://blogs.msdn.com/b/officeapps/archive/2012/11/29/solving-cross-domain-problems-in-apps-for-sharepoint.aspx)
    
  

