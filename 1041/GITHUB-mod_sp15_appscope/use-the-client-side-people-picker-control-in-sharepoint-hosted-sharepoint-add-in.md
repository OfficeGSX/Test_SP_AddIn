---
title: SharePoint 用の SharePoint ホスト型アドインでクライアント側ユーザー選択ウィンドウ コントロールを使用する
ms.prod: SHAREPOINT
ms.assetid: 383f265f-ed44-4d09-b2f6-366f13d52347
---


# SharePoint 用の SharePoint ホスト型アドインでクライアント側ユーザー選択ウィンドウ コントロールを使用する
SharePoint ホスト型 SharePoint アドインでクライアント側の [ユーザー選択] コントロールを使用する方法について説明します。
> **重要**
> このトピックは、SharePoint ホスト型 SharePoint アドイン の作成方法の知識があることを前提としています。作成方法の学習は、 [プロバイダー ホスト型 SharePoint アドインの作成を始める](get-started-creating-provider-hosted-sharepoint-add-ins.md) から開始してください。





## SharePoint 2013のクライアント側ユーザー選択ウィンドウ コントロールとは
<a name="bk_whatIs"> </a>

クライアント側ユーザー選択ウィンドウ コントロールを使用すると、ユーザーは、組織内の人、グループ、およびクレームの有効なユーザー アカウントをすばやく検索し、選択できます。この選択ウィンドウは、クロスブラウザー サポートを提供する HTML および JavaScript のコントロールです。アドインに選択ウィンドウを追加するのは簡単です。マークアップに、コントロール、コントロール参照、およびその依存関係の参照を含めるコンテナー要素を追加します。次にスクリプトで、 **SPClientPeoplePicker_InitStandaloneControlWrapper** グローバル関数を呼び出し、選択ウィンドウの表示および初期化を行います。



選択ウィンドウは **SPClientPeoplePicker** オブジェクトで表されます。このオブジェクトを使用することで、他のクライアント側コントロールは、選択ウィンドウから情報を取得したり、他の操作を実行したりできます。 **SPClientPeoplePicker** プロパティを使用して、複数ユーザーの許可、キャッシュ オプションの指定など、コントロールに特有の設定で選択ウィンドウを構成できます。選択ウィンドウは、Active Directory ドメイン サービスのパラメーター、対象のフォレストなど、Web アプリケーション構成設定も使用します。Web アプリケーション構成設定は、( **SPWebApplication.PeoplePickerSettings** プロパティから) 自動的に取得されます。



選択ウィンドウには、次のコンポーネントがあります。




- ユーザー、グループ、およびクレームの名前を入力する入力テキスト ボックス。


- 解決済みユーザー、グループ、およびクレームの名前を示すスパン コントロール。


- 一致するクエリ結果でドロップダウン ボックスを自動入力する非表示の **div** 要素。


- オートフィル コントロール。



> **メモ**
> 選択ウィンドウおよびその機能は、 **clientforms.js**、 **clientpeoplepicker.js**、および **autofill.js** スクリプト ファイルで定義されます。これらのファイルは、サーバー上の %ProgramFiles%\\Common Files\\Microsoft Shared\\web server extensions\\15\\TEMPLATE\\LAYOUTS フォルダーにあります。





## SharePoint アドイン 2013 でクライアント側ユーザー選択ウィンドウ コントロールを使用するための開発環境をセットアップする際の前提条件
<a name="bk_prereqs"> </a>

この記事では、Office 365 開発者向けサイトで Napaを使用して SharePoint アドインを作成するものとしています。この開発環境を使用している場合は、すでに前提条件を満たしています。




> **メモ**
> 「 [Office 365 で SharePoint アドインの開発環境をセットアップする](set-up-a-development-environment-for-sharepoint-add-ins-on-office-365.md)」にアクセスして、開発者向けサイトにサインアップする方法、および Napaの使用を開始する方法を確認します。 




開発者向けサイトの Napaを使用していない場合、次のツールが必要です。




- 少なくとも 1 名の対象ユーザーを持つ SharePoint 2013


- Visual Studio 2012 または Visual Studio 2013


- Office Developer Tools for Visual Studio 2013



> **メモ**
> ニーズに合った開発環境をセットアップする方法については、「 [Office 用アプリおよび SharePoint 用アプリの作成を開始する](http://msdn.microsoft.com/library/187f8c8c-1b15-471c-80b5-69a40e67deea%28Office.15%29.aspx)」を参照してください。 




以下の手順では、アドインに選択ウィンドウを追加し、別のクライアント側コントロールからユーザー情報を取得する手順の概要を説明します。対応するコードについては、「 [コード例: クライアント側ユーザー選択ウィンドウを使用する](use-the-client-side-people-picker-control-in-sharepoint-hosted-sharepoint-add-in.md#bk_example)」を参照してください。



SharePoint ホスト型 SharePoint アドインでクライアント側の [ユーザー選択] コントロールを使用できますが、プロバイダー ホスト型アドインでは使用できません。プロバイダー ホスト型アドインにユーザー選択コントロールを実装する方法のサンプルについては、「 [Office アドイン モデル サンプル](http://officeams.codeplex.com)」をダウンロードしてください。




## SharePoint ホスト型アドインでクライアント側ユーザー選択ウィンドウ コントロールを使用する
<a name="bk_steps"> </a>


### ページ マークアップに、次の追加を行います。


1. クライアント側ユーザー選択ウィンドウ コントロールのスクリプト依存関係への参照を追加します。


2. UI ページの HTML タグを追加します。この例のアドインでは、2 つの **div** 要素を定義します。1 つは選択ウィンドウを表示するためで、もう 1 つは選択ウィンドウへのクエリを実行するスクリプトと、ユーザー情報を表示する要素の起動ボタン用です。



### スクリプトで次の操作を行います。


1. **AllowMultipleValues**、 **MaximumEntitySuggestions** など、選択ウィンドウに特有のプロパティを保存するスキーマとして使用する JSON 辞書を作成します。


2. **SPClientPeoplePicker_InitStandaloneControlWrapper** グローバル関数を呼び出します。


3. ページから選択ウィンドウ オブジェクトを取得します。


4. 選択ウィンドウへのクエリを実行します。この例のアドインでは、 **GetAllUserInfo** メソッドを呼び出して、解決済みユーザーのユーザー情報をすべて取得し、 **GetAllUserKeys** メソッドを呼び出して、解決済みユーザーのキーを取得します。


5. JavaScript オブジェクト モデルを使用してユーザー ID を取得します。選択ウィンドウによって返される情報にはユーザー ID が含まれていないため、アドインは **SP.Web.ensureUser** メソッドを呼び出し、返された **SP.User** オブジェクトから ID を取得します。


選択ウィンドウの表示、初期化、その他の機能は、SharePoint 認証プロバイダーに対するユーザー入力の検索、解決を含め、サーバーで処理されます。




## コード例: SharePoint ホスト型アドインのクライアント側ユーザー選択ウィンドウを使用する
<a name="bk_example"> </a>

次の HTML および JavaScript のコード例では、SharePoint ホスト型アドインにクライアント側ユーザー選択ウィンドウ コントロールを追加します。



最初の例では、Default.aspx ページの **PlaceHolderMain** **asp:Content** タグのページ マークアップを示しています。このコードは、選択ウィンドウのスクリプト依存関係を参照し、選択ウィンドウが表示される DOM 要素に一意の ID を与え、アドインの UI を定義します。





```HTML

<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
    <SharePoint:ScriptLink name="clienttemplates.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientforms.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="clientpeoplepicker.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="autofill.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.runtime.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <SharePoint:ScriptLink name="sp.core.js" runat="server" LoadAfterUI="true" Localizable="false" />
    <div id="peoplePickerDiv"></div>
    <div>
        <br/>
        <input type="button" value="Get User Info" onclick="getUserInfo()"></input>
        <br/>
        <h1>User info:</h1>
        <p id="resolvedUsers"></p>
        <h1>User keys:</h1>
        <p id="userKeys"></p> 
        <h1>User ID:</h1>
        <p id="userId"></p>
    </div>
</asp:Content>```


> **メモ**
> ご使用の環境によっては、これらの依存関係すべてを明示的に参照する必要がない場合もあります。 




次の例は、App.js ファイルのスクリプトを示しています。このスクリプトでは、選択ウィンドウの初期化および表示を行った後、ユーザー情報のクエリを実行し、JavaScript オブジェクト モデルを使用してユーザー ID を取得します。





```

// Run your custom code when the DOM is ready.
$(document).ready(function () {

    // Specify the unique ID of the DOM element where the
    // picker will render.
    initializePeoplePicker('peoplePickerDiv');
});

// Render and initialize the client-side People Picker.
function initializePeoplePicker(peoplePickerElementId) {

    // Create a schema to store picker properties, and set the properties.
    var schema = {};
    schema['PrincipalAccountType'] = 'User,DL,SecGroup,SPGroup';
    schema['SearchPrincipalSource'] = 15;
    schema['ResolvePrincipalSource'] = 15;
    schema['AllowMultipleValues'] = true;
    schema['MaximumEntitySuggestions'] = 50;
    schema['Width'] = '280px';

    // Render and initialize the picker. 
    // Pass the ID of the DOM element that contains the picker, an array of initial
    // PickerEntity objects to set the picker value, and a schema that defines
    // picker properties.
    this.SPClientPeoplePicker_InitStandaloneControlWrapper(peoplePickerElementId, null, schema);
}

// Query the picker for user information.
function getUserInfo() {

    // Get the people picker object from the page.
    var peoplePicker = this.SPClientPeoplePicker.SPClientPeoplePickerDict.peoplePickerDiv_TopSpan;

    // Get information about all users.
    var users = peoplePicker.GetAllUserInfo();
    var userInfo = '';
    for (var i = 0; i < users.length; i++) {
        var user = users[i];
        for (var userProperty in user) { 
            userInfo += userProperty + ':  ' + user[userProperty] + '<br>';
        }
    }
    $('#resolvedUsers').html(userInfo);

    // Get user keys.
    var keys = peoplePicker.GetAllUserKeys();
    $('#userKeys').html(keys);

    // Get the first user's ID by using the login name.
    getUserId(users[0].Key);
}

// Get the user ID.
function getUserId(loginName) {
    var context = new SP.ClientContext.get_current();
    this.user = context.get_web().ensureUser(loginName);
    context.load(this.user);
    context.executeQueryAsync(
         Function.createDelegate(null, ensureUserSuccess), 
         Function.createDelegate(null, onFail)
    );
}

function ensureUserSuccess() {
    $('#userId').html(this.user.get_id());
}

function onFail(sender, args) {
    alert('Query failed. Error: ' + args.get_message());
}```


## その他の技術情報
<a name="bk_addresources"> </a>


-  [SharePoint 2013 での UX コンポーネントの作成](create-ux-components-in-sharepoint-2013.md)


-  [ユーザー選択ウィンドウとクレーム プロバイダーの概要 (SharePoint 2013)](http://technet.microsoft.com/ja-jp/library/gg602078.aspx)


-  [ユーザー選択ウィンドウを構成する (SharePoint 2013)](http://technet.microsoft.com/library/gg602075.aspx)



