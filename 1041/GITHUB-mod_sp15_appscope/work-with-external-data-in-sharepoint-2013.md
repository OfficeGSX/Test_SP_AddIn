---
title: SharePoint 2013 の外部データの操作
ms.prod: SHAREPOINT
ms.assetid: 1534a5f4-1d83-45b4-9714-3a1995677d85
---


# SharePoint 2013 の外部データの操作
SharePoint アドインのページで JavaScript を使用してデータにアクセスし、操作するための情報とガイダンスを示します。
## JavaScript を SharePoint アドインで使用する
<a name="SP15Workdata_Working"> </a>

SharePoint アドインでは、SharePoint ページまたはコンポーネント内から、リモート Web アプリケーションまたはサービスによって公開されるデータを取得して操作しなければならないことがよくあります。SharePoint サーバーでカスタム コードの使用はできないため、アドインではこの目的のために JavaScript を使用する必要があります。SharePoint アドイン用のモデルは、リモート データとサービスにアクセスするための複数のオプションを提供します。
  
    
    

### SharePoint のクロスドメイン JavaScript ライブラリを使用して外部データにアクセスする

リモート インフラストラクチャでホストされるカスタム プロキシ ページを提供すると、クロスドメイン ライブラリを使用してリモート Web アプリケーション上のデータにアクセスできます。開発者は、カスタム プロキシ ページを実装する責任を負い、リモート アプリケーションに対する認証メカニズムなどのカスタム ロジックが存在する場合は、処理を行う必要があります。リモート データ ソースと SharePoint ページの間での通信をクライアント レベルで行う場合は、クロス ドメイン ライブラリを使用します。
  
    
    
この方法でライブラリを使用する方法の詳細については、「 [SharePoint 2013 のクロスドメイン ライブラリのカスタム プロキシ ページを作成する](create-a-custom-proxy-page-for-the-cross-domain-library-in-sharepoint-2013.md)」を参照してください。
  
    
    

> **メモ**
> また、SharePoint クロス ドメイン ライブラリは、逆方向でも使用できます。つまり、リモート Web ページの JavaScript からこのライブラリを使用して SharePoint のデータへのアクセスに使用できます。ライブラリのこの使用方法の詳細については、「 [クロス ドメイン ライブラリを使用する SharePoint アドインの作成](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)」を参照してください。 
  
    
    


### SharePoint Web プロキシを使用して外部データにアクセスする

JavaScript クライアント オブジェクト モデルで公開されている Web プロキシを使用して、リモート データにアクセスできます。(プロキシは .NET クライアント側オブジェクト モデル (CSOM) でも利用できますが、SharePoint サーバーで動作するコードでこのオブジェクト モデルを使用することはできません)。 Web プロキシを使用する場合は、SharePoint に最初の要求を発行します。そうすると、指定されたエンドポイントに対して SharePoint がデータを要求し、応答をページに転送します。リモート データ ソースと SharePoint ページの間の通信をサーバー レベルで行う場合は、Web プロキシを使用します。
  
    
    
プロキシの使用方法の詳細については、「 [SharePoint 2013 で Web プロキシを使用してリモート サービスのクエリを実行する](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md)」を参照してください。
  
    
    

## その他の技術情報
<a name="SP15Workdata_AddRes"> </a>


-  [SharePoint アドインのセキュリティで保護されたデータ アクセスとクライアント オブジェクト モデル](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md)
    
  
-  [SharePoint アドインの開発](develop-sharepoint-add-ins.md)
    
  

