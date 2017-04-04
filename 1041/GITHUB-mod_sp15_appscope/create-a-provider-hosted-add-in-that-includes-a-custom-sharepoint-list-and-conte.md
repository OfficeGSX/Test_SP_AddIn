---
title: SharePoint のカスタムのリストおよびコンテンツ タイプを含む、プロバイダー向けのホスト型アドインを作成する
ms.prod: SHAREPOINT
ms.assetid: d97ab62e-129f-43f4-a825-fb5c3229d7c7
---


# SharePoint のカスタムのリストおよびコンテンツ タイプを含む、プロバイダー向けのホスト型アドインを作成する
クラウド ホスト型 Web アプリケーションと、SharePoint ホスト型のカスタムのリスト テンプレート、リスト インスタンス、およびカスタムのコンテンツ タイプを組み合わせた SharePoint アドインを、Office Developer Tools for Visual Studio 2012 を使用して作成します。REST/OData Web サービスを使用して SharePoint 2013 アドイン Web を操作する方法、および SharePoint アドインに OAuth を実装する方法を説明します。
カスタムのコンテンツ タイプ、カスタムのリスト定義、ワークフローなど、従来の SharePoint コンポーネントのほとんどは、クラウド ホスト型の SharePoint アドインに含めることができます。この記事では、単純な例として次のものを紹介します。
  
    
    


- 以下のものを含むアドイン Web
    
  - カスタムのサイト列
    
  
  - カスタムの列を使用するカスタムのコンテンツ タイプ
    
  
  - カスタムのコンテンツ タイプを使用するカスタムのリスト テンプレート
    
  
  - カスタムのリスト定義に基づいたリスト インスタンス
    
  
- リスト インスタンスからデータを読み取る ASP.NET Web アプリケーション
    
  

## この SharePoint アドインを作成するための前提条件
<a name="Prerequisites"> </a>


-  [Visual Studio 2012](https://www.microsoft.com/ja-jp/download/details.aspx?id=30682) 以降。
    
  
-  [Office Developer Tools](https://msdn.microsoft.com/ja-jp/office/aa905340.aspx)
    
  
- テストとデバッグ用の SharePoint 2013 のインストール
    
  - これは、ご使用の開発用コンピューターと同じコンピューター上で行えます。または、リモートの SharePoint 2013 のインストールを使用して開発することもできます。リモート インストールを使用して作業する場合は、ターゲットのインストール上で再頒布可能なクライアント オブジェクト モデルをインストールする必要があります。これは、Microsoft ダウンロード センターから再頒布可能パッケージとして入手できます。「SharePoint Server 2013 Client Components SDK」または「SharePoint Online Client Components SDK」を検索してください。
    
  
  - テスト用の SharePoint Web サイトは、 **開発者向けサイト**のサイト定義から作成する必要があります ([サーバーの全体管理] から作成できます)。
    
  
  - リモート Web アプリケーションは、JavaScript と  [クロスドメイン ライブラリ](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md)または  [OAuth](authorization-and-authentication-of-sharepoint-add-ins.md) を使用して、アドイン Web と通信します。この記事で継続的に使用する例のように、OAuth を使用する場合は、OAuth を使用するように SharePoint 2013 のインストールを構成する必要があります。
    
  

> **メモ**
> ニーズに合った開発環境をセットアップする方法については、「 [Office 用アプリおよび SharePoint 用アプリの作成を開始する](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)」を参照してください。 
  
    
    


### アドインを作成するために知っておくべき中心概念

最初のアドインを作成する前に、SharePoint アドインがどのようなものであるか、また、SharePoint ホスト型とクラウド ホスト型の SharePoint アドインの違いを理解しておく必要があります。これについては、表 1 に示した記事を参照してください。
  
    
    

**表 1. アドインの作成に関する中心概念**


|**記事のタイトル**|**説明**|
|:-----|:-----|
| [SharePoint アドイン](sharepoint-add-ins.md) <br/> |エンドユーザー向けの小型で使いやすいソリューションであるアドインを作成できる、SharePoint 2013の新しいアドイン モデルについて説明します。  <br/> |
| [SharePoint アドインのアーキテクチャおよび開発環境に関する重要な要素](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md) <br/> |SharePoint アドインおよび SharePoint アドイン用のモデル のアーキテクチャ面について説明しています。アドインのホスティング オプション、ユーザー インターフェイス (UI) オプション、展開システム、セキュリティ システム、およびライフ サイクルを取り上げています。  <br/> |
| [SharePoint アドインを開発およびホスティングするためのパターンを選択する](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md) <br/> |SharePoint アドインをホストするためのさまざまな方法を説明しています。  <br/> |
   

## SharePoint アドインを開発する
<a name="Develop"> </a>

このセクションの手順では、SharePoint コンポーネントとリモート Web アプリケーションを使用するアドイン Web を含む、SharePoint アドインを開発用コンピューター上で作成します。
  
    
    

### Visual Studio 2012 ソリューションとその要素をセットアップするには


1. Visual Studio 2012 で、[ **Office/SharePoint | アドイン**] ノードから [ **SharePoint 2013 用アドイン**] プロジェクトを作成します。このノードは、 **新しいプロジェクト** ウィザードのテンプレート ツリー ( **C#** または **Visual Basic** の下) にあります。[ **プロバイダーホスト**] ホスティング オプションを選択します。この記事の例では、言語として C# を使用し、プロジェクト名は LocalTheater とします。
    
  
2. ウィザードで [ **完了**] をクリックします。
    
  
3. マニフェスト デザイナーで AppManifest.xml ファイルを開きます。 **Title** 要素には、既定値としてプロジェクト名が設定されています。これはユーザーが UI で目にするアドイン名になるので、より分かりやすい名前に変更します。
    
  
4. アドインの **Name** を指定します。これは内部名称であり、使用できるのは ASCII 文字だけです。また、スペースを含めることはできません。たとえば、LocalTheater のように指定します。
    
  
5. Web アプリケーション プロジェクトで Web.config ファイルを開き、 `<customErrors mode="Off"/>` 要素を **system.web** 要素に追加します。
    
  
6. 以下のアセンブリへの参照が Web アプリケーション プロジェクトに含まれているかチェックします (ご使用の Visual Studio 2012 のエディションで参照が自動的に追加されなかった場合は、ここで追加します)。
    
  - **Microsoft.IdentityModel.dll** このアセンブリは、Windows Identity Foundation (WIF) のグローバル アセンブリ キャッシュにインストールされています。これは .NET Framework 3.5 のアセンブリなので、既定では [ **参照の追加**] ダイアログ ボックスの [ **フレームワーク**] ノードからはフィルターで除外されています。ご使用の開発用コンピューターの  `C:\\Program Files\\Reference Assemblies\\Microsoft\\Windows Identity Foundation\\v3.5` ディレクトリを直接参照することで、この参照を追加できます。
    
  
  - **Microsoft.IdentityModel.Extensions.dll** このアセンブリへの参照は、ご使用の開発用コンピューターの `C:\\Program Files\\Reference Assemblies\\Microsoft\\Microsoft Identity Extensions\\1.0` フォルダーを直接参照することで、追加できます。
    
  
  - **System.IdentityModel.dll** このアセンブリは, .NET Framework 4 の一部であり、[ **参照の追加**] ダイアログ ボックスの [ **アセンブリ | フレームワーク**] ノードに表示されます。
    
  
7. ご使用のリモート Web アプリケーションが、アドイン Web だけでなくホスト Web の情報にもアクセスする場合は、 **AppPermissionRequests** 要素 (1 つ以上の子 **AppPermissionRequest** 要素を含む) を AppManifest.xml ファイルに追加する必要があります (この記事の例の Web アプリケーションは、アドイン Web だけにアクセスします。アドイン プリンシパルには、アドイン Web に必要なすべてのアクセス許可が自動的に設定されているので、この例の AppManifest.xml には **AppPermissionRequests** 要素はありません)。アドインのアクセス許可要求およびその追加方法の詳細については、「 [SharePoint 2013 でのアドインのアクセス許可](add-in-permissions-in-sharepoint-2013.md)」を参照してください。
    
  

### SharePoint コンポーネントを追加するには


1. 従来の ファーム ソリューション に追加したときとまったく同じように、SharePoint コンポーネントをアドインに追加します。ただし、すべての種類の SharePoint コンポーネントを SharePoint アドイン に含められるわけではありません。それらのコンポーネントの用途は、SharePoint アドイン では別の方法で実現されます。SharePoint アドイン に含めることができる SharePoint コンポーネントの種類と、プロジェクトに含める方法については、「 [SharePoint アドインに含めることができる SharePoint コンポーネントの種類](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#TypesOfSPComponentsInApps)」をご覧ください。
    
    この例の目的としては、次の手順を使用します。これらは Visual Studio 2012 を使用して、SharePoint アドインのユーザー設定の列、コンテンツ タイプ、リスト テンプレート、およびリスト インスタンスを追加する例を示しています。
    
### ユーザー設定のコンテンツ タイプを作成するには


1. **ソリューション エクスプローラー**で、SharePoint の [ **サイト列**] アイテムを Actor という名前で SharePoint アドイン プロジェクトに追加します。
    
  
2. 新しいサイト列に対する elements.xml ファイルで、次の例に示す属性と値が設定されるように **Field** 要素を編集します。ただし、 **ID** 属性の GUID は、Visual Studio 2012 が生成した値から変更しないでください。また、"{}" で囲むのを忘れないようにします。
    
 ```
  
<Field ID="{generated GUID}"
       Name="Actor" 
       Title="Actor" 
       DisplayName="Actor/Actress" 
       Group="Theater and Movies" 
       Description="The person cast, perhaps tentatively, in the role" 
       Type="Text" 
/>
 ```

3. さらにもう 1 つ [ **サイト列**] を、CastingStatus という名前のプロジェクトに追加します。
    
  
4. 新しいサイト列に対する elements.xml ファイルで、次の例に示す属性と値が設定されるように **Field** 要素を編集します。ただし、 **ID** 属性の GUID は、Visual Studio 2012 が生成した値から変更しないでください。
    
 ```
  
<Field ID="{generated GUID}"
       Name="CastingStatus" 
       Title="CastingStatus"
       DisplayName="Casting Status" 
       Group="Theater and Movies" 
       Description="The current casting status of the role" 
       Type="Choice">
</Field>
 ```

5. これは選択肢フィールドなので、選択可能な項目、ユーザーが選択するときにドロップダウン リストに表示する順序、および既定の選択肢を指定する必要があります。次の子マークアップを **Field** 要素に追加します。
    
 ```
  
<CHOICES>
      <CHOICE>Not Started</CHOICE>
      <CHOICE>Audition Scheduled</CHOICE>
      <CHOICE>Auditioned</CHOICE>
      <CHOICE>Role Offered</CHOICE>
      <CHOICE>Committed to Role</CHOICE>
</CHOICES>
<MAPPINGS>
      <MAPPING Value="1">Not Started</MAPPING>
      <MAPPING Value="2">Audition Scheduled</MAPPING>
      <MAPPING Value="3">Auditioned</MAPPING>
      <MAPPING Value="4">Role Offered</MAPPING>
      <MAPPING Value="5">Committed to Role</MAPPING>
</MAPPINGS>
<Default>Not Started</Default>
 ```


### カスタムのコンテンツ タイプを作成するには


1. **ソリューション エクスプローラー**で、SharePoint の [ **コンテンツ タイプ**] アイテムを ActingRole という名前で SharePoint アドイン プロジェクトに追加します。ウィザードで基本のコンテンツ タイプを選択するように求められたら、[ **アイテム**] を選択して、[ **完了**] をクリックします。
    
  
2. コンテンツ タイプ デザイナーが自動的に開かない場合は、 **ソリューション エクスプローラー**で [ **ActingRole**] コンテンツ タイプを選択して開きます。
    
  
3. デザイナーで [ **コンテンツ タイプ**] タブを開き、テキスト ボックスに次のように入力します。
    
  - [ **コンテンツ タイプ名**]: ActingRole
    
  
  - [ **説明**]: 演劇または映画での役柄を表します。
    
  
  - [ **グループ名**]: Theater and Movies
    
  
4. このタブのチェック ボックスがすべてオフになっていることを確認します。[ **親コンテンツ タイプから列を継承する**] チェック ボックスは既定でオンになっている場合があります。これをオフにするのを忘れないようにしてください。
    
  
5. デザイナーの [ **列**] タブを開きます。
    
  
6. グリッドを使用して、2 つのサイト列をコンテンツ タイプに追加します。これらは、ドロップダウン リストでは [ **Actor/Actress**] および [ **CastingStatus**] の表示名でリストされています (リストされていない場合は、カスタムのサイト列を追加してからプロジェクトを保存していない可能性があります。[ **すべて保存**] をクリックします)。
    
  
7. ファイルを保存し、デザイナーを閉じます。
    
  
8. 次の手順では、未加工の XML でコンテンツ タイプを直接操作する必要があるので、 **ソリューション エクスプローラー**で **ActingRole** コンテンツ タイプの子の elements.xml を選択します。
    
  
9. このファイルには、先ほど追加した 2 つの列に対する **FieldRef** 要素がすでに存在します。すでに存在する 2 つの要素と対になるように、2 つの組み込みの SharePoint 2013 列に対する **FieldRef** 要素を追加します。以下は、これらの要素のマークアップです。これらは、固定された ID を持つ組み込みのフィールドの種類なので、ID 属性に対して同じ GUID を使用する必要があります。これらを、カスタムのサイト列に対する 2 つの **FieldRef** 要素よりも上に追加します。
    
 ```
  
<FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
<FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
 ```


    これらのフィールドにはカスタムの表示名として **Character** を指定してあることに注意してください。これは、演劇または映画の登場人物を意味しています。
    
  

### カスタムのリスト テンプレートとリスト インスタンスを作成するには


1. SharePoint の [ **リスト**] アイテムを CharactersInShow という名前で SharePoint アドイン プロジェクトに追加します。 **SharePoint カスタマイズ ウィザード** の [ **リストの設定を選択**] ページで、リスト表示名は既定の **CharactersInShow** のままにし、[ **次に基づいてカスタマイズ可能なリストを作成**] オプション ボタンをオンにして、ドロップダウン リストで [ **既定 (空白)**] を選択します。次に、[ **完了**] をクリックします。
    
  
2. ウィザードを終了すると、 **CharactersInShow** リスト テンプレートが、 **CharactersInShowInstance** という名前の子リスト インスタンスと共に作成されます。リスト デザイナーが既定で開かれる場合があります。これは後の手順で使用します。
    
  
3. **CharactersInShow** リスト テンプレートの子の elements.xml を開きます ( **CharactersInShowInstance** の子の elements.xml ではありません)。
    
  
4. **DisplayName** 属性に空白を追加して、"Characters In Show" のように、より分かりやすくします。
    
  
5. **Description** 属性を"演劇または映画の登場人物" と変更します。
    
  
6. その他の属性はすべて既定値のままにし、ファイルを保存して閉じます。
    
  
7. リスト デザイナーが開いていない場合は、 **ソリューション エクスプローラー**で [ **CharactersInShow**] ノードを選択します。
    
  
8. デザイナーで [ **列**] タブを開き、[ **コンテンツ タイプ**] ボタンをクリックします。
    
  
9. [ **コンテンツ タイプの設定**] ダイアログで、 **ActingRole** コンテンツ タイプを追加します。
    
  
10. タイプの一覧で **ActingRole** コンテンツ タイプを選択し、[ **既定値として設定**] ボタンをクリックします。
    
    [ **アイテム**] コンテンツ タイプを選択し、コンテンツ タイプ名の左に表示される小さな矢印を右クリックして [ **削除**] をクリックします。
    
  
11. この手順を [ **フォルダー**] コンテンツ タイプについても繰り返し、 **ActingRole** だけがコンテンツ タイプの一覧に表示されるようにします。[ **OK**] をクリックしてダイアログを閉じます。
    
  
12. 列の一覧に 3 つの列が表示されている状態になります。[ **タイトル**] を選択し、コンテンツ タイプ名の左に表示される小さな矢印を右クリックして [ **削除**] をクリックします。一覧には [ **Actor/Actress**] と [ **Casting Status**] の 2 つの列だけが表示されている状態になります。
    
  
13. デザイナーの [ **リスト**] タブを開きます。このタブは、リスト テンプレートではなくリスト インスタンスに特定の値を設定するのに使用されます。
    
  
14. このタブの値を次のように変更します。
    
  - [ **タイトル**]: Characters in Hamlet
    
  
  - [ **リストの URL**]: Lists/CharactersInHamlet
    
  
  - [ **説明**]: ハムレットの登場人物と配役情報
    
  

    チェック ボックスは既定の状態のままにし、ファイルを保存してデザイナーを閉じます。
    
  
15. リスト インスタンスには、 **ソリューション エクスプローラー**での元の名前が設定されている場合があります。その場合は、 **CharactersInShowInstance** のショートカット メニューを開き、[ **名前の変更**] をクリックして、名前を CharactersInHamlet に変更します。
    
  
16. schema.xml ファイルを開きます。
    
  
17. このファイルには 2 つの **ContentType** 要素がある場合があります。1 つは **Name** 属性値がActingRole のもので、もう 1 つは **ListFieldsContentType** という名前のものです。ActingRole という名前の要素だけが該当するので、それ以外の **ContentType** 要素を削除します。
    
    > **メモ**
      > **ContentType** 要素間には改行がない場合があるので、その場合、一見すると 1 つしかにように見えます。右方向にスクロールして、他に要素がないか慎重にチェックします。
18. **Fields** 要素には 2 つの **Field** 要素があるはずです (要素間に改行がない場合は 1 行に表示されています)。1 つは、 **Actor** というサイト列の elements.xml にある **Field** 要素と正確に一致している必要があります。もう 1 つは、 **CastingStatus** というサイト列の elements.xml にある **Field** 要素と正確に一致している必要があります。子要素 ( **CHOICES** 要素や **MAPPINGS** 要素) も含めて正確な一致でない場合は、サイト列の elements.xml ファイルから **Field** 要素をコピーし、schema.xml ファイル内の不一致の **Field** 要素に貼り付けます。
    
  
19. 引き続き schema.xml ファイルで、BaseViewID の値が "0" である **View** 要素を探し、既存の **ViewFields** 要素を次のマークアップで置き換えます ( `LinkTitle` という名前の **FieldRef** に対してこの GUID を正確に使用します)。
    
 ```
  
<ViewFields>
  <FieldRef Name="Title" ID="{fa564e0f-0c70-4ab9-b863-0177e6ddd247}" DisplayName="Character" />
  <FieldRef Name="Actor" ID="{GUID from the site column elements.xml}" />
  <FieldRef Name="CastingStatus" ID="{GUID from the site column elements.xml}" />
</ViewFields>
 ```

20. 未指定の 2 つの ID 属性値を、それぞれのサイト列の elements.xml ファイルでの GUID で置き換えます。また、"{}" で囲むのを忘れないようにします。
    
  
21. 引き続き schema.xml ファイルで、BaseViewID の値が "1" である **View** 要素を探し、既存の **ViewFields** 要素を次のマークアップで置き換えます ( `LinkTitle` という名前の **FieldRef** に対してこの GUID を正確に使用します)。
    
 ```
  
<ViewFields>
  <FieldRef Name="LinkTitle" ID="{82642ec8-ef9b-478f-acf9-31f7d45fbc31}" DisplayName="Character" />
</ViewFields>
 ```

22. 前のビューに追加した  `Actor` と `CastingStatus` に対する 2 つの **FieldRef** 要素を、 `LinkTitle` の **FieldRef** の兄弟として、この **ViewFields** 要素にコピーします。
    
  
23. schema.xml ファイルを保存して閉じます。
    
  
24. リスト インスタンス **CharactersInHamlet** の子の elements.xml ファイルを開きます。
    
  
25. **ListInstance** 要素の子として次のマークアップを追加し、ある程度の初期データをリストに入力します。
    
 ```
  
<Data>
  <Rows>
    <Row>
      <Field Name="Title">Hamlet</Field>
      <Field Name="Actor">Tom Higginbotham</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">Claudius</Field>
      <Field Name="Actor"></Field>
      <Field Name="CastingStatus">Not Started</Field>
    </Row>
    <Row>
      <Field Name="Title">Gertrude</Field>
      <Field Name="Actor">Satomi Hayakawa</Field>
      <Field Name="CastingStatus">Auditioned</Field>
    </Row>
    <Row>
      <Field Name="Title">Ophelia</Field>
      <Field Name="Actor">Cassi Hicks</Field>
      <Field Name="CastingStatus">Committed to Role</Field>
    </Row>
    <Row>
      <Field Name="Title">The ghost</Field>
      <Field Name="Actor">Lertchai Treetawatchaiwong</Field>
      <Field Name="CastingStatus">Role Offered</Field>
    </Row>
  </Rows>
</Data>
 ```

2. **ソリューション エクスプローラー**で、 **Feature1** をクリックし、フィーチャー デザイナーを開きます。デザイナーで、[ **タイトル**] を "Theater and Movie Data Components" と設定し、[ **説明**] を "劇場と映画に関するデータに対するサイト列、コンテンツ タイプ、およびリスト インスタンス" と設定します。ファイルを保存し、デザイナーを閉じます。
    
  
3. **ソリューション エクスプローラー** で **Feature1** の名前が変更されなかった場合、そのショートカット メニューを開き、[ **名前の変更**] をクリックして TheaterAndMovieDataComponents という名前に変更します。
    
  

### リモート Web アプリケーション プロジェクトをコーディングするには


- お好みのプラットフォーム スタック向けの他の Web アプリケーションの場合と同様に、Web アプリケーションを開発します。マイクロソフト スタックの場合、REST/OData Web サービスを使用するか、SharePoint 2013 のいずれかのクライアント オブジェクト モデルを使用できます。マイクロソフト以外のスタックの場合、SharePoint 2013 の REST/OData エンドポイントを使用して、アドイン Web のデータに対して作成/読み取り/更新/削除 (CRUD) 操作を実行できます。
    
    > **メモ**
      > Visual Studio で Web アプリケーション プロジェクトにアセンブリへの参照を追加する場合、アセンブリが既に Web サーバーにインストールされていることがわかっているか、アドインを展開する前に確実にインストールできるのでない限り、アセンブリの [ **ローカルにコピー**] プロパティを [ **True**] に設定します。.NET Framework は Microsoft Azure Web ロールおよび Azure Web サイト にインストールされます。ただし、SharePoint 2013 クライアント アセンブリと各種の Microsoft マネージ コード拡張機能および基盤はインストールされません。Office Developer Tools for Visual Studio 2012 は、SharePoint アドインでよく使われる一部のアセンブリへの参照を自動的に追加し、[ **ローカルにコピー**] プロパティを適切に設定します。 

    この例では、ASP.NET の Web アプリケーションを開発します。次の手順に従います。
    
1. Default.aspx ファイルを開き、ファイルの body 要素を次のマークアップで置き換えます。このマークアップにより、[ **Get the Cast**] ボタンが追加されます。このボタンをクリックすると、アドイン Web にある [ **Characters in Hamlet**] リストが読み取られて、そのデータが、ボタンのクリック後にだけ表示される  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) コントロールに表示されます。
    
 ```HTML
  
<body >
    <form id="form1" runat="server">
    <div>
    <h2>Local Theater</h2>
    </div>
    <asp:Literal ID="Literal1" runat="server"><br /><br /></asp:Literal>

    <asp:Button ID="Button1" runat="server" OnClick="Button1_Click" Text="Get the Cast"/>

    <asp:Literal ID="Literal2" runat="server"><br /><br /></asp:Literal>

    <asp:GridView ID="GridView1" runat="server" Caption="The Cast" ></asp:GridView>
    </form>
</body>
 ```

2. Default.aspx.cs ファイルを開き、次の **using** ステートメントをファイルに追加します。
    
 ```cs
  
using Microsoft.SharePoint.Client;
using Microsoft.IdentityModel.S2S.Tokens;
using System.Net;
using System.IO;
using System.Xml;
using System.Data;
using System.Xml.Linq;
using System.Xml.XPath;
using Microsoft.SharePoint.Samples;
 ```


    これらのステートメントの最後の部分は、TokenHelper.cs ファイルで宣言された名前空間を参照します。
    
  
3. 次のフィールドを **Default** クラスに追加します。
    
 ```cs
  
SharePointContextToken contextToken;
string accessToken;
Uri sharepointUrl;
 ```

4. **Page_Load** メソッドを次のコードで置き換えます。このコードは、 **TokenHelper** クラスを使用して、OAuth 準拠のセキュリティで保護されたトークン サーバーからトークンを取得します。このアクセス トークンは、ボタンのクリック イベント ハンドラーが後で取得できるように、ボタンの [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) プロパティに格納されます。
    
 ```cs
  
protected void Page_Load(object sender, EventArgs e)
{
    TokenHelper.TrustAllCertificates();
    string contextTokenString = TokenHelper.GetContextTokenFromRequest(Request);

    if (contextTokenString != null)
    {
        // Get context token
        contextToken = TokenHelper.ReadAndValidateContextToken(contextTokenString, Request.Url.Authority);

        // Get access token
        sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
        accessToken = TokenHelper.GetAccessToken(contextToken, sharepointUrl.Authority).AccessToken;
        
        // Pass the access token to the button event handler.
        Button1.CommandArgument = accessToken;
    }
}
 ```

5. 次のイベント ハンドラーを **Default** クラスに追加します。このハンドラーは、まず、ボタンの [CommandArgument](https://msdn.microsoft.com/library/System.Web.UI.WebControls.Button.CommandArgument.aspx) プロパティに格納されたアクセス トークンを取得することから開始します。
    
 ```cs
  
protected void Button1_Click(object sender, EventArgs e)
{
    // Retrieve the access token that the Page_Load method stored
    // in the button's command argument.
    string accessToken = ((Button)sender).CommandArgument;
}
 ```

6. このハンドラーは、変更されたアドイン Web の URL をポストバック時に再取得する必要があるので、次のコードを追加します。
    
 ```cs
  
if (IsPostBack)
{
    sharepointUrl = new Uri(Request.QueryString["SPAppWebUrl"]);
}
 ```

7. 次の行を追加します。これは、いずれかの SharePoint 2013 REST/OData エンドポイントを使用してリスト データを取得します。この例では、このコードはアドイン Web に配置された [ **Characters in Hamlet**] リストを読み取ります。このサービスの API によって、1 行のコードで、リストを選択し、リストの 3 つのフィールドを指定して返すのが容易になります。OData URL には表示名ではなくフィールド (列) の内部名を使用する必要があるので、このコードでは、 `Character`、 `Actor/Actress`、および  `Casting Status.` ではなく、 `Title`、 `Actor`、および  `CastingStatus` を使用します。REST/OData Web サービスの詳細については、「 [SharePoint REST 要求で OData クエリ操作を使用する](use-odata-query-operations-in-sharepoint-rest-requests.md)」を参照してください。
    
 ```cs
  
// REST/OData URL section
 string oDataUrl = "/_api/Web/lists/getbytitle('Characters In Hamlet')/items?$select=Title,Actor,CastingStatus";
 ```

8. 次のコードを追加します。このコードでは、 [System.Net](https://msdn.microsoft.com/library/System.Net.aspx) 名前空間の [HttpWebRequest](https://msdn.microsoft.com/library/System.Net.HttpWebRequest.aspx) クラスと [HttpWebResponse](https://msdn.microsoft.com/library/System.Net.HttpWebResponse.aspx) クラスを使用して、HTTP 要求オブジェクトと応答オブジェクトを作成します。
    
 ```cs
  
// HTTP Request and Response construction section
HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(sharepointUrl.ToString() + oDataUrl);
request.Method = "GET";
request.Accept = "application/atom+xml";
request.ContentType = "application/atom+xml;type=entry";
request.Headers.Add("Authorization", "Bearer " + accessToken);
HttpWebResponse response = (HttpWebResponse)request.GetResponse();
 ```

9. ATOM 形式の応答 XML を解析するために、次のコードを追加します。このコードは、 [System.Xml.Linq](https://msdn.microsoft.com/library/System.Xml.Linq.aspx) 名前空間のクラスを使用して、返されたデータを解析し、SharePoint リストのアイテムの [List<T>](http://msdn2.microsoft.com/JA-JP/library/6sh2ey19) を作成します ( [System.Xml](https://msdn.microsoft.com/library/System.Xml.aspx) 名前空間のクラスも使用できます)。SharePoint が返す XML では、 **entry** 要素の子要素にリスト アイテムに関するメタデータが保持されていることに注意してください。SharePoint リスト アイテムの実際の未加工データは、2 階層下の **properties** 要素に入れ子になっています。このため、 [Elements<T>](http://msdn2.microsoft.com/JA-JP/library/bb348465) 拡張メソッドが 2 回使用されて、上位レベルがフィルターで除外されます。
    
 ```cs
  
// Response markup parsing section
XDocument oDataXML = XDocument.Load(response.GetResponseStream(), LoadOptions.None);
XNamespace atom = "http://www.w3.org/2005/Atom";
XNamespace d = "http://schemas.microsoft.com/ado/2007/08/dataservices";
XNamespace m = "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"; 

List<XElement> entries = oDataXML.Descendants(atom + "entry")
                         .Elements(atom + "content")
                         .Elements(m + "properties")
                         .ToList();
 ```

10. 次の LINQ クエリを追加します。このクエリでは、必要なプロパティだけが設定された匿名型の  [IEnumerable<T>](http://msdn2.microsoft.com/JA-JP/library/9eekhta0) コレクションが作成されます。このコードは、内部名である `Title` によってアイテムのタイトル フィールドを参照する必要がありますが、その値が割り当てられる匿名型でのプロパティ名には `Character` という名前を使用できます。その効果の 1 つとして、コレクションがグリッド コントロールにバインドされるときに、より適切な名前である [ **Character**] がページに表示されます。
    
 ```cs
  
var entryFieldValues = from entry in entries
                       select new { Character=entry.Element(d + "Title").Value, 
                                    Actor=entry.Element(d + "Actor").Value, 
                                    CastingStatus=entry.Element(d + "CastingStatus").Value };

 ```

11. ページの  [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) コントロールにデータをバインドする次のコードを使用して、ハンドラーを完成させます。グリッドの列のヘッダーは、既定では、匿名型のプロパティ名である `Character`、 `Actor`、および  `CastingStatus` に設定されます。 [GridView](https://msdn.microsoft.com/library/System.Web.UI.WebControls.GridView.aspx) コントロールには、名前と形式設定列のヘッダーを制御できるプロパティがあるので、[ **Actor/Actress**] と [ **Casting Status**] を SharePoint での列のヘッダーと一致させることができます。簡単にするために、これらの方法はここでは説明しません ( [DataGrid](https://msdn.microsoft.com/library/System.Web.UI.WebControls.DataGrid.aspx) コントロールも使用できます)。
    
 ```cs
  
GridView1.DataSource = entryFieldValues;
GridView1.DataBind();

 ```

12. すべてのファイルを保存します。
    
  

### SharePoint アドインのテストとデバッグを行うには


1. SharePoint アドインとそのリモート Web アプリケーションをテストするには、Visual Studio 2012 で F5 キーを押します。Web アプリケーションは localhost の IIS Express に展開されます。SharePoint アドインはターゲット SharePoint Web サイトにインストールされます (この例では、リモート アドインがホスト Web およびアドインとのやり取りを試みることはなく、アドイン プリンシパルにはアドイン Web に対するアクセス許可が自動的に付与されるので、アクセス許可の付与を求められることはありません)。ターゲット SharePoint Web サイトの [ **サイト コンテンツ**] ページが開き、新しいアドインが一覧に表示されているのが分かります。
    
  
2. SharePoint アドインを選択すると、AppManifest.xml ファイルの **StartPage** 要素に指定したページにリモート Web アプリケーションが開きます。必要に応じてこの Web アプリケーションを使用して、動作を確認します。このトピックの例では、ボタンをクリックするだけにします。それにより、グリッドが作成され、アドイン Web にある [ **Characters in Hamlet**] リストを使用してグリッドに値が入力されます。
    
  

## SharePoint アドインを発行する
<a name="Publish"> </a>

SharePoint アドインを発行するには、企業のアドイン カタログまたは Office アドイン ストアにアドイン パッケージをアップロードします。詳細については、「 [Office ストアまたは組織のアドイン カタログに発行する](deploying-and-installing-sharepoint-add-ins-methods-and-options.md#MarketOrCatalog)」と「 [SharePoint アドインの発行](publish-sharepoint-add-ins.md)」を参照してください。
  
    
    

## トラブルシューティング
<a name="Troubleshooting"> </a>

アドインが動作しない場合は、CAML マークアップの誤りによって SharePoint コンポーネントの展開が妨げられているのかどうかを検討します。この例に基づいた次のような手順を使用して、展開を確認します。
  
    
    

### アドイン Web のプロビジョニングをテストするには


1. ホスト Web の [ **サイトの設定**] ページを開きます。[ **サイト コレクションの管理**] セクションの [ **サイト階層**] リンクをクリックします。
    
  
2. [ **サイト階層**] ページには、アドインが URL ごとに一覧表示されます。アドインを起動しないでください。その代わりに、URL をコピーして、以降の手順ではその URL を使用します。
    
  
3.  _URL_of_app_web_/_layouts/15/ManageFeatures.aspx に移動し、開かれた [ **サイトの機能**] ページで、ご使用の SharePoint アドインの機能のアルファベット順の一覧に [ **Theater and Movie Data Components**] があり、その状態が **アクティブ**であることを確認します。
    
  
4.  _URL_of_app_web_/_layouts/15/mngfield.aspx に移動し、開かれた [ **サイト列**] ページで、サイト列の一覧に [ **Theater and Movies**] グループがあり、そこに新しいユーザー設定のサイト列である [ **Actor/Actress**] と [ **Casting Status**] が含まれていることを確認します。
    
  
5.  _URL_of_app_web_/_layouts/15/mngctype.aspx に移動し、開かれた [ **サイト コンテンツ タイプ**] ページで、コンテンツ タイプの一覧に [ **Theater and Movies**] グループがあり、そこに新しい **ActingRole** コンテンツ タイプが含まれていることを確認します。
    
  
6. **ActingRole** コンテンツ タイプへのリンクをクリックします。開かれた[ **サイト コンテンツ タイプ**] ページで、このコンテンツ タイプに 2 つの新しいサイト列のタイプである [ **Actor/Actress**] と [ **Casting Status**] があること、およびアイテムのタイトル フィールドにユーザー設定の表示名である [ **Character**] が設定されていることを確認します。
    
  
7.  _URL_of_app_web_/_layouts/15/mcontent.aspx に移動し、開かれた [ **サイトのライブラリとリスト**] ページで、[ **"Characters in Hamlet" のカスタマイズ**] リンクがあることを確認します。
    
  
8. この [ **"Characters in Hamlet" のカスタマイズ**] リンクをクリックし、リストの設定ページで、このリストのコンテンツ タイプがカスタムの **ActingRole** コンテンツ タイプだけであること、および 2 つの新しいサイト列である [ **Actor/Actress**] と [ **Casting Status**] が [ **列**] セクションに一覧表示されていることを確認します (([タイトル] 列は、指定した表示名の [ **Character** ] ではなく内部名の [ **Title**] で表示される場合があります)。
    
    > **メモ**
      > ページに [ **コンテンツ タイプ**] セクションがない場合は、コンテンツ タイプの管理を有効にする必要があります。[ **詳細設定**] リンクをクリックし、[ **詳細設定**] ページでコンテンツ タイプの管理を有効にして、[ **OK**] をクリックします。前のページに戻ると、[ **コンテンツ タイプ**] セクションの一覧が表示されます。 
9. ページの最上部付近にはリストの **Web アドレス**が表示されています。これをコピーし、ご使用のブラウザーのアドレス バーに貼り付けて、リストに移動します。このリストに、作成したサンプル アイテムが表示されていることを確認します ([タイトル] 列は、指定した表示名の [ **Character**] ではなく内部名の [ **Title**] で表示される場合があります)。
    
  

## 次の手順
<a name="NextSteps"> </a>

この記事では、リモート Web アプリケーションを使用して単純なハイブリッド SharePoint アドインを作成する方法を説明しました。次の手順として、以下のことを検討します。
  
    
    

- REST/OData エンドポイントまたはいずれかのクライアント オブジェクト モデルを使用して、アドインにフル CRUD 機能を追加します。詳細については、「 [SharePoint REST 要求で OData クエリ操作を使用する](use-odata-query-operations-in-sharepoint-rest-requests.md)」と「 [SharePoint 2013 のクライアント ライブラリ コードを使用して基本的な操作を完了する](complete-basic-operations-using-sharepoint-2013-client-library-code.md)」を参照してください。
    
  
- 他の文化圏向けに SharePoint アドインをローカライズします。詳細については、「 [SharePoint アドインをローカライズする](localize-sharepoint-add-ins.md)」を参照してください。
    
  
- リモート Web アプリケーションの機能を複製する Windows Phone コンパニオン アドインを作成します。詳細については、「 [モバイル SharePoint アドイン 2013 の作成](http://msdn.microsoft.com/ja-jp/library/office/jj163228.aspx)」を参照してください。
    
  

## その他の技術情報
<a name="SP15createcloud_bk_addlresources"> </a>


-  [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md)
    
  
-  [SharePoint ホスト型の SharePoint アドインの作成を始める](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)
    
  

