---
title: Visual Studio を使用して SharePoint アドインを公開する
keywords: vs.sharepointtools.project.sharepointprojectpropertytab
f1_keywords:
- vs.sharepointtools.project.sharepointprojectpropertytab
ms.prod: SHAREPOINT
ms.assetid: 8137d0fa-52e2-4771-8639-60af80f693bb
---


# Visual Studio を使用して SharePoint アドインを公開する
Microsoft Visual Studio 2013 または Visual Studio 2012 を使用して、SharePoint アドインを発行する方法について説明します。アドインに関連付けられている Web アプリケーションがある場合、まずそのアプリケーションを配置します。次に、すべての SharePoint アドインについて、SharePoint アドインをパッケージ化してから発行します。またオプションで、Office ストアに登録されるようにアドインを提出することもできます。
## 前提条件
<a name="Prereq"> </a>






-  [Microsoft Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=517284)

    または

     [ Visual Studio 2012](https://www.microsoft.com/ja-jp/download/details.aspx?id=30682) と Office Developer Tools for Visual Studio。これらのツールをダウンロードするには、 [ダウンロード ページ](http://go.microsoft.com/fwlink/?LinkId=234393)の「ツール」を参照してください。(新しいパブリッシュ マネージャーは Visual Studio 2012 以前のバージョンでは使用できません。)


- Microsoft SharePoint 2013



## Visual Studio 2013 を使用した発行
<a name="VS2013"> </a>

プロバイダー ホスト型 SharePoint アドイン に Web アプリケーションがある場合、まずそのアプリケーションのアプリケーションを配置します。次に、すべての SharePoint アドイン について、SharePoint アドイン をパッケージ化してから発行します。




> **重要**
> Web プロジェクトで SharePoint クライアント ID とクライアント シークレット値を発行して、Web コンテンツが SharePoint データにアクセスできるようにするには、[ **アドインの発行**] ページから SharePoint アドイン プロジェクトを発行します。このページにアクセスするには、Web アプリのショートカット メニューではなく、SharePoint アドイン のショートカット メニューを開き、[ **発行**] コマンドを選択します。 





### 手順 1: Web アプリケーションを展開する

SharePoint アドイン には、通常、Web サーバーに配置する必要がある、関連するホスト Web アプリケーションがあります。Web の発行ウィザードの使用方法については、「 [方法: Visual Studio でワンクリック発行を使用して Web アプリケーション プロジェクトを配置する](http://msdn.microsoft.com/library/dd465337.aspx)」を参照してください。




### [アドインの発行] ページを開くには


- **ソリューション エクスプローラー** で、SharePoint アドイン プロジェクトのショートカット メニューを開き、[ **発行**] を選択します。

    [ **アドインの発行**] ページが表示されます。



### プロファイルを選択または作成するには


- [ **現在のプロファイル**] 一覧でインポートするプロファイルを選択するか、[ **<新規作成…>**] を選択してプロファイルを作成します。

    発行プロファイルでは、Web アプリを配置するサーバー、サーバーにログオンするために必要な資格情報、配置するデータベース (該当する場合) などの配置オプションを指定します。必要に応じて、異なる発行プロファイルを作成できます。たとえば、テスト用のプロファイルと発行用のプロファイルを作成することができます。

    **<新規作成 …>** を選択すると、[ **発行プロファイルの作成**] ウィザードが表示されます。このウィザードを使用して、Azure などの Web サイト ホスティング プロバイダーから発行プロファイルをインポートしたり、プロファイルを作成してサーバー名、資格情報などの設定を手動で追加できます。既存のプロファイルをインポートせずに、新しいプロファイルを作成した場合、「 [SharePoint アドイン 2013 の登録に関するガイドライン](http://msdn.microsoft.com/library/jj687469.aspx)」および「 [Microsoft 販売者ダッシュボード でクライアント ID とクライアント シークレットを作成する方法](http://msdn.microsoft.com/library/office/jj220036.aspx)」の説明に従って、クライアント ID とクライアント シークレット値を指定する必要があります。

    SharePoint アドインを Office ストアに提出する予定の場合、[販売者ダッシュボード] で作成されたクライアント ID とクライアント シークレット値を使用してください。開発フェーズ中に appregnew.aspx ページを使用して生成したクライアント ID とクライアント シークレット値を使用できますが、Office ストアに提出するアドインは [販売者ダッシュボード] から取得したクライアント ID とクライアント シークレットを使用する必要があります。また、[ **発行プロファイルの作成**] ウィザードでプロファイルを作成するのではなく、Azure サイトで発行プロファイルを作成してから、Visual Studio にインポートする必要があります。Azure でプロファイルを作成すると、Visual Studio の [ **接続**] タブのすべての設定が指定されます。発行プロファイルのインポートおよび作成方法についての詳細は、「 [発行プロファイルの作成](http://msdn.microsoft.com/library/dd465337.aspx#creating_a_profile)」を参照してください。

    > **ヒント**
    > Web コンテンツを直接発行できない場合、管理者が代理で Web に配置できる Web 配置パッケージを作成できます。Web 配置パッケージを作成するには、新しいプロファイルを作成し、[ **接続**] タブを選択し、[ **発行方法**] リストの [ **Web 配置パッケージ**] を選択します。 

### Web アプリ プロジェクトを配置するには


1. [ **アドインの発行**] ページで、[ **Web プロジェクトの配置**] ボタンを選択します。

    [ **Web の発行**] ダイアログ ボックスが表示されます。


2. [ **接続**] タブと [ **設定**] タブで、必要な値を入力します。

    SharePoint アドインのファイルを発行する方法、またはアドインが外部データベースを使用するかどうかを変更するには、[ **設定**] タブを選択します。「 [方法: Visual Studio でワンクリック発行を使用して Web アプリケーション プロジェクトを配置する](http://msdn.microsoft.com/library/dd465337.aspx)」の「[設定]タブの構成」を参照してください。


3. Web アプリの配置時に変化する項目を確認するには、[ **プレビュー**] タブの [ **プレビュー開始**] ボタンを選択します。


4. [ **発行**] ボタンを選択して、Web アプリ プロジェクトを配置します。



### 手順 2: アドインをパッケージ化する


1. [ **アドインの発行**] ページで、[ **アドインのパッケージ**] ボタンを選択します。

    [ **Office アドインおよび SharePoint アドインを発行する**] ウィザードが表示されます。


2. [ **Web サイトがホストされている場所**] テキスト ボックスに、SharePoint アドインのコンテンツ ファイルをホストする Web サイトの URL を入力します。

    "https" プレフィックスで始まるアドレスを指定する必要があります。「 [アドインを SSL でセキュリティ保護する理由](http://msdn.microsoft.com/library/jj591603#bk_q7)」を参照してください。

    > **メモ**
    > Azure Web サイトは https エンドポイントを自動的に提供します。Office ストア サイトまたは Office ストアにアドインを発行する場合、アドレスは https プレフィックスから始める必要があります。ただし、アドインを社内サイトに発行する場合は、http プレフィックスを使用できます。 

    [ **アドインのクライアント ID**] テキスト ボックスに、発行プロファイルに入力したクライアント ID が表示されます。

    この時点まででクライアント ID にプレースホルダー値を使用していた場合、ここで実際のクライアント ID を追加する必要があります。この情報は .app パッケージに組み込まれ、Web コンテンツが実際のサイトの SharePoint と通信できるようになります。


3. [ **完了**] ボタンを選択します。

    Visual Studio は、SharePoint アドイン の発行に必要なファイルを生成して、発行の出力フォルダーを開きます。アドインのインストール方法についての詳細は、「 [SharePoint アドイン 2013 をインストールおよび管理する](http://technet.microsoft.com/library/fp161232.aspx)」を参照してください。



### 手順 3: Office ストアに SharePoint アドインを発行する

SharePoint アドインを Office ストアに提出する場合、次の手順に従います。




1. [ **アドインの発行**] ページで、[ **販売者ダッシュボード へのアクセス**] ボタンを選択し、Microsoft 販売者ダッシュボード アカウントにサインインします。

    「 [Office および SharePoint 用 App のマーケットプレース ポリシーとガイドライン](http://msdn.microsoft.com/library/ff075782-1303-4517-91cc-b3d730e9b9ae%28Office.15%29.aspx)」をご覧ください。


2. 「 **新しいアプリケーションを追加する**」を選択し、情報を入力し、Office ストア にアドインを送信します。詳細については、「 [販売者ダッシュボードを使用して Office アドインと SharePoint アドインおよび Office 365 アプリを Office ストアに提出する](http://msdn.microsoft.com/library/260ef238-0be4-42d6-ba15-1249a8e2ff12%28Office.15%29.aspx)」をご覧ください。



## Visual Studio 2012 を使用した発行する
<a name="VS2012"> </a>

SharePoint アドインをパッケージ化する準備が整ったら、 **Office アドインの発行**ウィザードを開きます。このウィザードでは、発行する SharePoint アドインのファイルが準備されます。




### 手順 1: SharePoint アドインをパッケージ化する


1. **ソリューション エクスプローラー**で、SharePoint アドイン プロジェクトのショートカット メニューを開き、[ **発行**] を選択します。

    [ **Office アドインの発行**] ウィザードが表示されます。パッケージ化する SharePoint アドインの種類によって、ウィザードに表示されるページが決まります。アドインが SharePoint ホスト型の場合、[ **概要**] ページだけが表示されます。アドインがプロバイダー ホスト型の場合、[ **プロファイル**] ページと [ **ホスト処理**] ページも表示されます。


2. SharePoint アドインがプロバイダー ホスト型の場合、[ **発行するプロファイル**] リストに発行プロファイル名を指定し、[ **次へ**] ボタンを選択します。

    発行プロファイルに、[ **ホスト処理**] ページに入力する情報が保存されます。


3. [ **Web サイトがホストされている場所**] リストで、SharePoint アドインをホストする Web アプリケーションの URL を指定します。


4. [ **ご使用のアドインの ID**] のボックスで、アドインのクライアント ID とクライアント シークレットを指定し、[ **次へ**] を選択します。

    「 [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)」を参照してください。


5. すべての種類の SharePoint アドインに対し、[ **パッケージ化が正常に完了した後で出力フォルダーを開く**] チェック ボックスがオフの場合はオンにし、[ **完了**] ボタンを選択します。

    Visual Studio によって、SharePoint アドインの発行に必要なすべてのファイルが生成されます。これらのファイルは、プロジェクト出力フォルダーの  `app.Publish` フォルダー内にあります (たとえば、 `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish`)。このフォルダーには、SharePoint アドインの .app ファイルと Web アプリケーション用の複数のファイル (SharePoint アドインがクラウドベースの場合) が保存されます。すべての SharePoint アドインには .app ファイルがあります。このファイルは SharePoint アドインの発行に使用されるアドイン マニフェストです。プロバイダー ホスト型の SharePoint アドインには、ホスト Web アプリケーションを発行するためのファイルも含まれます。



### 手順 2: Web アプリケーションを発行する

SharePoint アドインがプロバイダー ホスト型の場合、通常、Web サーバーへの発行が必要な関連する Web アプリケーションがあります。Visual Studio によって生成される配置パッケージとスクリプトは、このタスクの実行に役立ちます。



Web アプリケーション プロジェクトの配置パッケージは、 `app.publish` フォルダーの圧縮 (.zip) ファイルに含まれています。 `app.publish` フォルダーには, .zip ファイルの他に、次のファイルが含まれます。





|**ファイル**|**説明**|
|:-----|:-----|
| _ProjectName_.deploy.cmd  <br/> |このファイルは、Web 配置を呼び出し、コマンド プロンプトでパッケージを簡単にインストールできるようにするコマンドライン バッチ ファイルです。  <br/> |
| _ProjectName_.SetParameters.xml  <br/> |このファイルには、パッケージをインストールする deploy.cmd ファイルを使用するときに Web 配置に渡されるパラメーターが含まれます。 Visual Studio パッケージ設定によって、各パラメーターに指定される既定値が決まります。たとえば、複数のサーバーに Web アプリケーションをインストールし、サーバーごとに異なる設定を使用する場合は、これらの値を変更できます。  <br/> |
| _ProjectName_.SourceManifest.xml  <br/> |このファイルには、Web パッケージを作成するために Visual Studio が Web 配置に渡し、Web 配置が使用する設定が含まれます。Web 配置では、パッケージを作成するためだけにこのファイルが必要です。このファイルは、パッケージのインストール時には使用されません。  <br/> |
 
手順の説明については、「 [方法: Visual Studio で作成された deploy.cmd ファイルを使用して配置パッケージをインストールする](http://go.microsoft.com/fwlink/?LinkID=254954)」を参照してください。




### 手順 3: SharePoint アドインを発行する
<a name="Publish"> </a>

SharePoint アドインを発行するには、アドインのアドイン マニフェスト ファイル (.app) を Office ストア、Office アドイン カタログ、SharePoint、ファイル共有、または Exchange カタログにアップロードします。アドインのアドイン マニフェストは、 `%UserProfile%\\Documents\\Visual Studio 2012\\Projects\\MyApp\\bin\\Debug\\app.publish` などの `app.publish` フォルダーにあります。SharePoint アドインの発行の詳細については、「 [SharePoint アドインの承認と認証](authorization-and-authentication-of-sharepoint-add-ins.md)」を参照してください。




## その他の技術情報
<a name="Additional"> </a>


-  [SharePoint アドインの発行](publish-sharepoint-add-ins.md)


-  [Office 2013 プレビュー用アプリの公開](http://msdn.microsoft.com/library/7f3ae6a0-06e9-438c-8899-bd9f605e6d9e%28Office.15%29.aspx)



