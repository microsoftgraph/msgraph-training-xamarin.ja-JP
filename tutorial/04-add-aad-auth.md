<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習のアプリケーションを拡張して、Azure AD での認証をサポートします。 これは、Microsoft Graph を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。 この手順では [、Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) をアプリケーションに統合します。

1. ソリューション **エクスプローラーで****、GraphTu solutionl** プロジェクトを展開し **、[Models]** フォルダーを右クリックします。 [ **クラスの>を選択します**。クラスに名前を付 `OAuthSettings` け、[追加] を **選択します**。

1. 新しい **ファイルOAuthSettings.cs** 開き、その内容を次の内容に置き換えてください。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. アプリ `YOUR_APP_ID_HERE` 登録のアプリケーション ID に置き換える。

    > [!IMPORTANT]
    > git などのソース管理を使っている場合は、アプリ ID が誤って漏洩しないように、ファイルをソース管理から除外する良い時期です `OAuthSettings.cs` 。

## <a name="implement-sign-in"></a>サインインの実装

1. **GraphTu App.xaml.cs****ファイルを** 開き、ファイルの一番上に次の `using` ステートメントを追加します。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. アプリケーションクラス **の宣言** 行を変更して、Application の名前の競合を解決 **します**。

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. 次のプロパティをクラスに追加 `App` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. 次に、クラスの `PublicClientApplication` コンストラクターに新しいオブジェクトを作成 `App` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. アクセス トークン `SignIn` を取得するために `PublicClientApplication` 関数を更新します。 行の上に次のコードを `await GetUserInfo();` 追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    このコードでは、最初にアクセス トークンをサイレント モードで取得します。 ユーザーの情報が既にアプリのキャッシュにある場合 (たとえば、ユーザーがサインアウトせずに以前にアプリを閉じた場合)、これは成功し、ユーザーに確認を求める理由はありません。 キャッシュにユーザーの情報が含されていない場合、関数は `AcquireTokenSilent().ExecuteAsync()` `MsalUiRequiredException` . この場合、コードは対話型関数を呼び出してトークンを取得します `AcquireTokenInteractive` 。

1. キャッシュから `SignOut` ユーザーの情報を削除するには、関数を更新します。 次のコードを関数の先頭に追加 `SignOut` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Android プロジェクトを更新してサインインを有効にする

Xamarin Android プロジェクトで使用する場合、Microsoft 認証ライブラリには Android に固有のいくつかの [要件があります](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)。

1. **GraphTu読み込み.Android** プロジェクトで、[**プロパティ**] フォルダーを展開し、**次AndroidManifest.xml。** If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With,** then **Source Code Editor**. すべての内容を次の内容に置き換えてください。

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. ファイル **MainActivity.cs** を開き、ファイルの一番上に次 `using` のステートメントを追加します。

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. MSAL `OnActivityResult` ライブラリにコントロールを渡す関数をオーバーライドします。 クラスに次を追加 `MainActivity` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. 関数で `OnCreate` 、行の後に次の行を追加 `LoadApplication(new App());` します。

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>iOS プロジェクトを更新してサインインを有効にする

> [!IMPORTANT]
> MSAL では Entitlements.plist ファイルの使用が必要なので、プロビジョニングを有効にVisual Studio Apple 開発者アカウントでこのファイルを構成する必要があります。 このチュートリアルを iPhone シミュレーターで実行している場合は **、GraphTu tutoriall.iOS** プロジェクトの設定の [Build->**iOS Bundle Signing]** の [Custom **Entitlements]** フィールドに **Entitlements.plist** を追加する必要があります。 詳しくは [、Xamarin.iOS のデバイス プロビジョニングに関するページをご覧ください](/xamarin/ios/get-started/installation/device-provisioning)。

Xamarin iOS プロジェクトで使用する場合、Microsoft 認証ライブラリには iOS に固有のいくつかの [要件があります](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)。

1. ソリューション エクスプローラーで **GraphTu solutionl.iOS** プロジェクトを展開し **、Entitlements.plist ファイルを開** きます。

1. キーチェーンの権利 **を** 見つけて **、[KeyChain を有効にする] を選択します**。

1. In **Keychain Groups,** add an entry with the format `com.companyname.GraphTutorial` .

    ![キーチェーンの権利の構成のスクリーンショット](./images/enable-keychain-access.png)

1. 認証中にリダイレクト **を処理するために GraphTu読み込み.iOS** プロジェクトのコードを更新します。 新しい **AppDelegate.cs** を開き、ファイルの上部に `using` 次のステートメントを追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. 行の直前に機能 `FinishedLaunching` する次の行を追加 `LoadApplication(new App());` します。

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. 関数を `OpenUrl` オーバーライドして、MSAL ライブラリに URL を渡します。 クラスに次を追加 `AppDelegate` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>トークンの格納

Xamarin プロジェクトで Microsoft 認証ライブラリを使用する場合、既定では、ネイティブのセキュリティで保護されたストレージを利用してトークンをキャッシュします。 詳細 [については、「トークン キャッシュのシリアル化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。

## <a name="test-sign-in"></a>サインインのテスト

この時点で、アプリケーションを実行して [サインイン] ボタンをタップすると、サインインするように求めるメッセージが表示されます。 サインインが成功すると、アクセス トークンがデバッガーの出力に出力されます。

![ページの [出力] ウィンドウのVisual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. App クラスに新しい関数 **を追加** して初期化します `GraphServiceClient` 。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. 代わりに `SignIn` この関数 **App.xaml.cs** 呼び出すメソッドの関数を更新します `GetUserInfo` 。 関数から次を削除 `SignIn` します。

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. 関数の最後に次を追加 `SignIn` します。

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. Microsoft `GetUserInfo` Graph からユーザーの詳細を取得するために関数を更新します。 既存の `GetUserInfo` 関数を、以下の関数で置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. 変更内容を保存し、アプリケーションを実行します。 サインイン後、UI はユーザーの表示名と電子メール アドレスで更新されます。
