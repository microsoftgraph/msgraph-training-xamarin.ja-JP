<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、 [.net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。

1. **ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開し、[**モデル**] フォルダーを右クリックします。 [ **Add > Class...**.] を選択します。クラス`OAuthSettings`の名前を指定して、[**追加**] を選択します。

1. **OAuthSettings.cs**ファイルを開き、その内容を次のように置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. を`YOUR_APP_ID_HERE`アプリ登録のアプリケーション ID に置き換えます。

    > [!IMPORTANT]
    > Git などのソース管理を使用している場合は、この時点で、ソース管理`OAuthSettings.cs`からファイルを除外して、アプリ ID が誤ってリークしないようにすることをお勧めします。

## <a name="implement-sign-in"></a>サインインの実装

1. **Graphtutorial**プロジェクトで`using` **App.xaml.cs**ファイルを開き、次のステートメントをファイルの先頭に追加します。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. **アプリケーション**の名前の競合を解決するには、 **App**クラス宣言行を変更します。

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. 次のプロパティを`App`クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. 次に、 `App`クラスの`PublicClientApplication`コンストラクターで新しいを作成します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. を使用`SignIn`して`PublicClientApplication`アクセストークンを取得するように関数を更新します。 行の`await GetUserInfo();`上に次のコードを追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    このコードでは、最初にアクセストークンを暗黙的に取得しようとします。 ユーザーの情報がアプリのキャッシュに既に存在している場合 (たとえば、ユーザーが以前にサインアウトせずにそのアプリを閉じた場合)、これは成功し、ユーザーに確認を求める理由はありません。 キャッシュ内にユーザーの情報がない場合、この関数は`AcquireTokenSilent().ExecuteAsync()`をスロー `MsalUiRequiredException`します。 この場合、コードは interactive 関数を呼び出してトークンを`AcquireTokenInteractive`取得します。

1. `SignOut`関数を更新して、キャッシュからユーザーの情報を削除します。 次のコードを`SignOut`関数の先頭に追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Android プロジェクトを更新してサインインを有効にする

Xamarin Android プロジェクトで使用されている場合、Microsoft 認証ライブラリには[Android 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)のいくつかの要件があります。

1. **Graphtutorial. Android**プロジェクトで、 **Properties**フォルダーを展開し、 **androidmanifest**を開きます。 Visual Studio for Mac を使用している場合は、[ **Androidmanifest** ] を制御し、[ファイルを**開く**]、[**ソースコードエディター**] の順に選択します。 内容全体を次のように置き換えます。

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. **MainActivity.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. 関数を`OnActivityResult`上書きして、msal ライブラリに制御を渡します。 次のものを`MainActivity`クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. `OnCreate`関数で、行の`LoadApplication(new App());`後に次の行を追加します。

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>IOS プロジェクトを更新してサインインを有効にする

> [!IMPORTANT]
> MSAL では、資格を使用する必要があるため、Apple developer アカウントで Visual Studio を構成して、プロビジョニングを有効にする必要があります。 IPhone シミュレータでこのチュートリアルを実行している場合は、「Graphtutorial」の「**カスタム**の利用資格」フィールドに「**資格**を追加する必要があります **。 Ios**プロジェクトの設定、**ビルド >ios バンドル署名**。 詳細については、「[デバイスプロビジョニング (Xamarin](/xamarin/ios/get-started/installation/device-provisioning))」を参照してください。

Xamarin iOS プロジェクトで使用されている場合、Microsoft 認証ライブラリには[iOS 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)のいくつかの要件があります。

1. ソリューションエクスプローラーで、 **Graphtutorial. iOS**プロジェクトを展開し、**資格**ファイルを開きます。

1. **キーチェーン**の資格を見つけ、[**キーチェーンを有効にする**] を選択します。

1. **キーチェーングループ**で、という形式`com.companyname.GraphTutorial`のエントリを追加します。

    ![キーチェーン資格の構成のスクリーンショット](./images/enable-keychain-access.png)

1. 「 **Graphtutorial** 」プロジェクトのコードを更新して、認証中にリダイレクトを処理するようにします。 **AppDelegate.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. 行の`LoadApplication(new App());`直前に、 `FinishedLaunching`次の行を追加します。

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. MSAL `OpenUrl`ライブラリに URL を渡す関数を上書きします。 次のものを`AppDelegate`クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>トークンの格納

Microsoft 認証ライブラリを Xamarin プロジェクトで使用する場合、既定では、ネイティブのセキュリティ保護されたストレージを利用してトークンをキャッシュします。 詳細については、「 [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。

## <a name="test-sign-in"></a>サインインのテスト

この時点で、アプリケーションを実行して [**サインイン**] ボタンをタップすると、サインインするように求められます。 サインインに成功すると、デバッガーの出力にアクセストークンが出力されたことがわかります。

![Visual Studio の [出力] ウィンドウのスクリーンショット](./images/debugger-access-token.png)

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. **アプリ**クラスに新しい関数を追加して、 `GraphServiceClient`を初期化します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. App.xaml.cs の`SignIn`関数を**App.xaml.cs** 、ではなく、この関数`GetUserInfo`を呼び出すように更新します。 次のものを`SignIn`関数から削除します。

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. 次のものを`SignIn`関数の末尾に追加します。

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. 関数を`GetUserInfo`更新して、Microsoft Graph からユーザーの詳細を取得します。 既存の `GetUserInfo` 関数を、以下の関数で置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. 変更内容を保存し、アプリケーションを実行します。 サインイン後、ユーザーの表示名と電子メールアドレスで UI が更新されます。
