---
title: SharePoint アドインの承認と認証
ms.prod: SHAREPOINT
ms.assetid: bde5647a-fff1-4b51-b67b-2139de79ce4a
---


# SharePoint アドインの承認と認証
SharePoint の認証と承認の機能について概要を説明します。この機能は、SharePoint リソースにアクセスしようとする SharePoint アドインからの要求を承認するために使用されます。
**アドイン ID についてのビデオをご覧ください。**








![ビデオ](images/mod_icon_video.png)












## SharePoint のアドイン認証
<a name="AuthN"> </a>

 *ユーザー*  が SharePoint にサインインすると、そのユーザーのセキュリティ トークンが検証されます。そのトークンは ID プロバイダーによって発行されたものです。SharePoint はいくつかの種類のユーザー認証をサポートしています。詳細については、「 [SharePoint 2013 での認証、承認、およびセキュリティ](http://msdn.microsoft.com/library/8734790c-eb75-4d78-9604-7cc23b33b693%28Office.15%29.aspx)」を参照してください。



SharePoint アドインも、認証と承認を必要とするセキュリティ プリンシパルです。アドインの認証と承認は、いくつかの方法で実行できます。詳細については、「 [SharePoint アドインの 3 つの承認システム](three-authorization-systems-for-sharepoint-add-ins.md)」を参照してください。 




## 承認ポリシー: ユーザー用ポリシー、ユーザー + アドイン ポリシー、アドイン用ポリシー
<a name="AuthZ"> </a>

承認プロセスは、認証済みのサブジェクト (アドイン、ユーザー、またはその両方) が特定の操作を実行するアクセス許可、または特定のリソース (リストや SharePoint ドキュメント フォルダーなど) にアクセスするアクセス許可を持っているかどうかを検査します。



SharePoint は 3 種類の承認ポリシーを使用します。ユーザー用ポリシーでは、SharePoint への呼び出しに認証されたユーザー ID が含まれていることのみが必要です。アドイン用ポリシーでは、呼び出しに認証されたアドイン ID だけが含まれていることのみが必要です。ユーザー + アドイン ポリシーでは、この両方の種類の認証された ID が含まれていることが必要です。ユーザーが SharePoint リソースに、アドインからではなく SharePoint UI からアクセスした場合、SharePoint はユーザー用ポリシーを使用します。しかし、SharePoint アドインからの呼び出しについては、SharePoint は常にアドイン用ポリシーまたはユーザー + アドイン ポリシーのいずれかを使用します。SharePoint アドインは、SharePoint への要求に含まれるアクセス トークンの種類により、どのポリシーが使用されているかを判別します。ユーザー + アドインの要求が行われると、SharePoint はアドインがアクセスしているリソースへのアクセス許可をアドインとユーザーの両方が持っていることを必要とします。アドイン用の要求の場合、SharePoint はアドインがリソースへのアクセス許可を持っていることを必要としますが、ユーザーがアクセス許可を持っているかどうかは考慮に入れません。(SharePoint アドインがアドイン用の要求を行えるのは、事前にその要求をするアクセス許可を付与された場合のみです。その許可は通常、インストール時に行われます。)



承認ポリシーとそのしくみについては、「 [SharePoint 2013 のアドイン承認ポリシーの種類](add-in-authorization-policy-types-in-sharepoint-2013.md)」を参照してください。




## アドイン アクセス許可とアドイン アクセス許可要求のスコープ
<a name="Permissions"> </a>

SharePoint アドインの開発者は、アドイン マニフェスト ファイルを通じて、アドイン Web 外部の SharePoint リソースに対してアドインが必要とするアクセス許可を指定する必要があります。(アドインは、アドイン Web 全体に対してフル コントロール権限を自動的に獲得します。) アドインが SharePoint 内から起動するように設計されている場合は、アドインのインストール インフラストラクチャにより、必要なアクセス許可を付与または拒否するように求めるメッセージがアドインをインストールするユーザーに表示されます。いったんアクセス許可が付与されると、Web サイトのユーザーはアクセス許可を再付与しないでもアドインを利用できるようになります。しかし、アドインが SharePoint の外部で起動するように設計されている (つまり、SharePoint 上にインストールされていない) 場合は、SharePoint によりアドインの実行のたびに、必要なアクセス許可を付与するように求めるメッセージがアドインを実行しているユーザーに表示されます。SharePoint 上にインストールされていなくても SharePoint にアクセスできるアドインの例としては、モバイル デバイス上のアドインや、Office アドインがあります。



Web サイトに SharePoint アドインをインストールできるのは、SharePoint Web サイトの所有者だけです (ただし、アドインのインストール権限を持つカスタムの役割が作成されている場合を除きます)。ユーザーがアドインに付与できるのは自分が持っているアクセス許可だけであるため、ユーザーは、自分が持っていないアクセス許可を必要とするアドインをインストールできません。同様に、ユーザーが持っていないアクセス許可を必要とする、外部で起動するアドインをそのユーザーが実行することもできません。ただし、SharePoint アドインを SharePoint にインストールする際に、アドイン用呼び出しを行うアクセス許可を求めることができます。そのアクセス許可を付与されたアドインは、アドインを実行しているユーザーがアクセス許可を持っていない操作を SharePoint に対して実行できます。



また、SharePoint Online テナントの管理者や SharePoint ファームの管理者がアドインのアクセス許可の取り消しや付与を行うこともできます。



SharePoint アドインは、アドインが正常に機能するために必要なアクセス許可をアドイン マニフェスト ファイルで指定します。アクセス許可の要求には、アドインが必要とする権限と、その権限が必要とするスコープの両方を指定します。スコープは、アクセス許可の要求が適用される SharePoint 階層内の位置を示します。SharePoint は、テナンシー、サイト コレクション、Web サイト、およびリストという 4 つのコンテンツ スコープをサポートしています。また、検索クエリの実行、分類データへのアクセス、ソーシャル機能、Microsoft Business Connectivity Services (BCS) 機能、および Project Server 2013 機能を実行するための特別なスコープもあります。詳細については、「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」を参照してください。




## OAuth の使用が必要になるケース
<a name="FileName_uniquekeyword4"> </a>

SharePoint アドインの認証と承認では OAuth 2.0 が重要な役割を果たすという説明を既にお読みになったかもしれません。それは確かですが、すべての SharePoint アドインの承認プロセスでそれが必要になるとは限りません。リモート Web アプリケーションで実行すると同時に、サーバー側のコードを使用して SharePoint への逆方向の通信も行う SharePoint アドインを構築する場合は、OAuth を使用する必要があります。リモート Web アプリケーションがオンプレミスでない場合は、Azure ACS がアクセス トークンの発行者になる [低信頼承認システム](creating-sharepoint-add-ins-that-use-low-trust-authorization.md)を使用できます。リモート Web アプリケーションがオンプレミスの場合は、通常、アドイン自体とデジタル証明書がアクセス トークンの発行者になる [高信頼システム](creating-sharepoint-add-ins-that-use-high-trust-authorization.md)を使用できます。



アドイン Web 自体のページにある JavaScript から、または [クロス ドメイン ライブラリ](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)を使用してリモート Web ページから呼び出しを行う場合は、OAuth を使用しません。クロス ドメイン ライブラリの詳細については、「 [クロス ドメイン ライブラリを使用する SharePoint アドインの作成](creating-sharepoint-add-ins-that-use-the-cross-domain-library.md)」を参照してください。




## その他の技術情報
<a name="Filename_AdditionalResources"> </a>


-  [SharePoint アドインの 3 つの承認システム](three-authorization-systems-for-sharepoint-add-ins.md)


-  [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)


-  [SharePoint アドイン](sharepoint-add-ins.md)



