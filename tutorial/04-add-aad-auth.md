<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習からアプリケーションを拡張して、Azure サーバーでの認証をサポートAD。 これは、Microsoft サーバーを呼び出す必要がある OAuth アクセス トークンを取得するために必要Graph。 この手順では [、Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) をアプリケーションに統合します。

1. ソリューション **エクスプローラーで****、GraphTutorial** プロジェクトを展開し、[モデル] フォルダーを **右クリック** します。 [Add **> クラス... を選択します**。クラスに名前を付 `OAuthSettings` け、[追加] を **選択します**。

1. **OAuthSettings.cs** ファイルを開き、その内容を次に置き換えてください。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. アプリ `YOUR_APP_ID_HERE` 登録のアプリケーション ID に置き換える。

    > [!IMPORTANT]
    > git などのソース管理を使用している場合は、誤ってアプリ ID が漏洩しないように、ソース管理からファイルを除外する良 `OAuthSettings.cs` い時期です。

## <a name="implement-sign-in"></a>サインインの実装

1. **GraphTutorial** **プロジェクトで App.xaml.cs** ファイルを開き、ファイルの上部に次の `using` ステートメントを追加します。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. Application の **名前の** 競合を解決するには、App クラス宣言行を **変更します**。

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. クラスに次のプロパティを追加 `App` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. 次に、クラスの `PublicClientApplication` コンストラクターに新しいオブジェクトを作成 `App` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. アクセス トークン `SignIn` を取得するために使用 `PublicClientApplication` する関数を更新します。 行の上に次のコードを追加 `await GetUserInfo();` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    このコードでは、最初にアクセス トークンをサイレント モードで取得します。 ユーザーの情報が既にアプリのキャッシュ内にある場合 (たとえば、ユーザーが以前にサインアウトせずにアプリを閉じた場合)、これは成功し、ユーザーにメッセージを表示する理由はありません。 キャッシュにユーザーの情報が含めされていない場合、 `AcquireTokenSilent().ExecuteAsync()` 関数は `MsalUiRequiredException` . この場合、コードは対話型関数を呼び出してトークンを取得します `AcquireTokenInteractive` 。

1. 関数を `SignOut` 更新して、キャッシュからユーザーの情報を削除します。 関数の先頭に次のコードを追加 `SignOut` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Android プロジェクトを更新してサインインを有効にする

Xamarin Android プロジェクトで使用する場合、Microsoft 認証ライブラリには Android 固有のいくつかの [要件があります](/azure/active-directory/develop/msal-net-xamarin-android-considerations)。

1. **GraphTutorial.Android プロジェクトで、[** プロパティ] フォルダー **を展開** し、[プロパティ] を **開** AndroidManifest.xml。 ユーザー設定を使用している場合は、[Visual Studio for Mac] をクリックAndroidManifest.xmlをクリックし、[ファイルを開く] 、[ソース コード エディター]**の順に選択します**。 コンテンツ全体を次に置き換えてください。

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. **MainActivity.cs を** 開き、ファイルの上部に `using` 次のステートメントを追加します。

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. MSAL `OnActivityResult` ライブラリにコントロールを渡す関数をオーバーライドします。 クラスに次の項目を追加 `MainActivity` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. 関数で `OnCreate` 、行の後に次の行を追加 `LoadApplication(new App());` します。

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>iOS プロジェクトを更新してサインインを有効にする

> [!IMPORTANT]
> MSAL では Entitlements.plist ファイルの使用が必要なので、プロビジョニングを有効にVisual Studio Apple 開発者アカウントを使用して構成する必要があります。 iPhone シミュレーターでこのチュートリアルを実行している場合は **、GraphTutorial.iOS** プロジェクトの設定であるビルド **->iOS** バンドル署名のカスタムエンタイトルメント フィールドに **Entitlements.plist** を追加する必要があります。 詳細については [、「Xamarin.iOS のデバイス プロビジョニング」を参照してください](/xamarin/ios/get-started/installation/device-provisioning)。

Xamarin iOS プロジェクトで使用する場合、Microsoft 認証ライブラリには iOS 固有のいくつかの [要件があります](/azure/active-directory/develop/msal-net-xamarin-ios-considerations)。

1. ソリューション エクスプローラーで **、GraphTutorial.iOS** プロジェクトを展開し **、Entitlements.plist ファイルを開** きます。

1. キーチェーンの **資格を** 見つけて、[キー **チェーンの有効化] を選択します**。

1. [ **キーチェーン グループ]** で、形式のエントリを追加します `com.companyname.GraphTutorial` 。

    ![キーチェーンのエンタイトルメント構成のスクリーンショット](./images/enable-keychain-access.png)

1. **GraphTutorial.iOS プロジェクトの** コードを更新して、認証中にリダイレクトを処理します。 **AppDelegate.cs** ファイルを開き、ファイルの上部に `using` 次のステートメントを追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. 行の直前に次 `FinishedLaunching` の行を関数に追加 `LoadApplication(new App());` します。

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. 関数を `OpenUrl` オーバーライドして、URL を MSAL ライブラリに渡します。 クラスに次の項目を追加 `AppDelegate` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>トークンの格納

Xamarin プロジェクトで Microsoft 認証ライブラリを使用する場合、既定ではネイティブのセキュリティで保護されたストレージを利用してトークンをキャッシュします。 詳細については [、「トークン キャッシュのシリアル化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。

## <a name="test-sign-in"></a>サインインのテスト

この時点で、アプリケーションを実行して [サインイン] ボタンをタップすると、サインインを求めるメッセージが表示されます。 サインインが成功すると、デバッガーの出力にアクセス トークンが印刷されます。

![[出力] ウィンドウのスクリーンショットが表示Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. 新しい関数を App クラス **に追加して** 初期化します `GraphServiceClient` 。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. 代わりにこの関数を呼び出す `SignIn` **App.xaml.cs** の関数を更新します `GetUserInfo` 。 関数から次の項目を削除 `SignIn` します。

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. 関数の末尾に次の項目を追加 `SignIn` します。

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. 関数を `GetUserInfo` 更新して、Microsoft ユーザーからユーザーの詳細を取得Graph。 既存の `GetUserInfo` 関数を、以下の関数で置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. 変更内容を保存し、アプリケーションを実行します。 サインイン後、UI はユーザーの表示名と電子メール アドレスで更新されます。
