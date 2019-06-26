<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、 [.net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。

**ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開し、[**モデル**] フォルダーを右クリックします。 [ **Add > Class...**.] を選択します。クラス`OAuthSettings`の名前を指定して、[**追加**] を選択します。 **OAuthSettings.cs**ファイルを開き、その内容を次のように置き換えます。

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

を`YOUR_APP_ID_HERE`アプリ登録のアプリケーション ID に置き換えます。

> [!IMPORTANT]
> Git などのソース管理を使用している場合は、この時点で、ソース管理`OAuthSettings.cs`からファイルを除外して、アプリ ID が誤ってリークしないようにすることをお勧めします。

## <a name="implement-sign-in"></a>サインインの実装

**Graphtutorial**プロジェクトで`using` **App.xaml.cs**ファイルを開き、次のステートメントをファイルの先頭に追加します。

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

次のプロパティを`App`クラスに追加します。

```cs
// UIParent used by Android version of the app
public static object AuthUIParent = null;

// Keychain security group used by iOS version of the app
public static string iOSKeychainSecurityGroup = null;

// Microsoft Authentication client for native/mobile apps
public static IPublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

次に、 `App`クラスの`PublicClientApplication`コンストラクターで新しいを作成します。

```cs
public App()
{
    InitializeComponent();

    var builder = PublicClientApplicationBuilder
        .Create(OAuthSettings.ApplicationId);

    if (!string.IsNullOrEmpty(iOSKeychainSecurityGroup))
    {
        builder = builder.WithIosKeychainSecurityGroup(iOSKeychainSecurityGroup);
    }

    PCA = builder.Build();

    MainPage = new MainPage();
}
```

では、 `SignIn`を使用して`PublicClientApplication`アクセストークンを取得するように関数を更新します。 行の`await GetUserInfo();`上に次のコードを追加します。

```cs
var scopes = OAuthSettings.Scopes.Split(' ');

// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
string accessToken = string.Empty;
try
{
    var accounts = await PCA.GetAccountsAsync();
    if (accounts.Count() > 0)
    {
        var silentAuthResult = await PCA
            .AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();

        Debug.WriteLine("User already signed in.");
        Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
        accessToken = silentAuthResult.AccessToken;
    }
}
catch (MsalUiRequiredException)
{
    // This exception is thrown when an interactive sign-in is required.
    Debug.WriteLine("Silent token request failed, user needs to sign-in");
}

if (string.IsNullOrEmpty(accessToken))
{
    // Prompt the user to sign-in
    var interactiveRequest = PCA.AcquireTokenInteractive(scopes);

    if (AuthUIParent != null)
    {
        interactiveRequest = interactiveRequest
            .WithParentActivityOrWindow(AuthUIParent);
    }

    var authResult = await interactiveRequest.ExecuteAsync();
    Debug.WriteLine($"Access Token: {authResult.AccessToken}");
}
```

このコードでは、最初にアクセストークンを暗黙的に取得しようとします。 ユーザーの情報がアプリのキャッシュに既に存在している場合 (たとえば、ユーザーが以前にサインアウトせずにそのアプリを閉じた場合)、これは成功し、ユーザーに確認を求める理由はありません。 キャッシュ内にユーザーの情報がない場合、この関数は`AcquireTokenSilent().ExecuteAsync()`をスロー `MsalUiRequiredException`します。 この場合、コードは interactive 関数を呼び出してトークンを`AcquireTokenInteractive`取得します。

次に、 `SignOut`キャッシュからユーザーの情報を削除するように関数を更新します。 次のコードを`SignOut`関数の先頭に追加します。

```cs
// Get all cached accounts for the app
// (Should only be one)
var accounts = await PCA.GetAccountsAsync();
while (accounts.Any())
{
    // Remove the account info from the cache
    await PCA.RemoveAsync(accounts.First());
    accounts = await PCA.GetAccountsAsync();
}
```

### <a name="update-android-project-to-enable-sign-in"></a>Android プロジェクトを更新してサインインを有効にする

Xamarin Android プロジェクトで使用されている場合、Microsoft 認証ライブラリには[Android 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)のいくつかの要件があります。

最初に、Android マニフェストを更新してリダイレクト URI を登録する必要があります。 **Graphtutorial. Android**プロジェクトで、 **Properties**フォルダーを展開し、 **androidmanifest**を開きます。 Visual Studio for Mac を使用している場合は、ファイルの下部にあるタブを使用して、**ソース**ビューに切り替えます。 内容全体を次のように置き換えます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.GraphTutorial">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="28" />
    <application android:label="GraphTutorial.Android">
        <activity android:name="microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msalYOUR_APP_ID_HERE" android:host="auth" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

を`YOUR_APP_ID_HERE`アプリ登録のアプリケーション ID で置き換えます。

次に、 **MainActivity.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

その後、関数`OnActivityResult`を上書きして、msal ライブラリに制御を渡します。 次のものを`MainActivity`クラスに追加します。

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

最後に、 `OnCreate`関数の行の`LoadApplication(new App());`後に次の行を追加します。

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a>IOS プロジェクトを更新してサインインを有効にする

> [!IMPORTANT]
> MSAL では、資格を使用する必要があるため、Apple developer アカウントで Visual Studio を構成して、プロビジョニングを有効にする必要があります。 IPhone シミュレータでこのチュートリアルを実行している場合は、「Graphtutorial」の「**カスタム**の利用資格」フィールドに「**資格**を追加する必要があります **。 Ios**プロジェクトの設定、**ビルド >ios バンドル署名**。 詳細については、「[デバイスプロビジョニング (Xamarin](/xamarin/ios/get-started/installation/device-provisioning))」を参照してください。

Xamarin iOS プロジェクトで使用されている場合、Microsoft 認証ライブラリには[iOS 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)のいくつかの要件があります。

最初に、キーチェーンアクセスを有効にする必要があります。 ソリューションエクスプローラーで、 **Graphtutorial. iOS**プロジェクトを展開し、**資格**ファイルを開きます。 **キーチェーン**の資格を見つけ、[**キーチェーンを有効にする**] を選択します。 **キーチェーングループ**で、という形式`com.YOUR_DOMAIN.GraphTutorial`のエントリを追加します。

![キーチェーン資格の構成のスクリーンショット](./images/enable-keychain-access.png)

次に、アプリが処理する URL の種類として、既定の MSAL リダイレクト URI を登録する必要があります。 **情報**ファイルを開き、次のように変更します。

- [**アプリケーション**] タブで、[**バンドル識別子**] の値が、[利用**資格**] の**キーチェーングループ**に設定した値と一致していることを確認します。 そうでない場合は、今すぐ更新してください。
- [**詳細設定**] タブで、[ **URL の種類**] セクションを探します。 URL の種類を次の値で追加します。
  - **識別子**:**バンドル識別子**の値を設定します。
  - **URL スキーム**: に`msal{YOUR-APP-ID}`設定します。 たとえば、アプリ ID が`67ad5eba-0cfc-414d-8f9f-0a6d973a907c`である場合は、をに`msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`設定します。
  - **役割**:`Editor`
  - **アイコン**: 空のままにします

![情報の [URL の種類] セクションのスクリーンショット](./images/add-url-type.png)

最後に、認証中にリダイレクトを処理するように、 **Graphtutorial. iOS**プロジェクトのコードを更新します。 **AppDelegate.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。

```cs
using Microsoft.Identity.Client;
```

行の`LoadApplication(new App());`直前に、 `FinishedLaunching`次の行を追加します。

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

最後に、 `OpenUrl`関数を上書きして、msal ライブラリに URL を渡します。 次のものを`AppDelegate`クラスに追加します。

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a>トークンの格納

Microsoft 認証ライブラリを Xamarin プロジェクトで使用する場合、既定では、ネイティブのセキュリティ保護されたストレージを利用してトークンをキャッシュします。 詳細については、「 [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。

## <a name="test-sign-in"></a>サインインのテスト

この時点で、アプリケーションを実行して [**サインイン**] ボタンをタップすると、サインインするように求められます。 サインインに成功すると、デバッガーの出力にアクセストークンが出力されたことがわかります。

![Visual Studio の [出力] ウィンドウのスクリーンショット](./images/debugger-access-token.png)

## <a name="get-user-details"></a>ユーザーの詳細を取得する

では、 `SignIn` **App.xaml.cs**の関数を更新し`GraphServiceClient`て、を初期化します。 行の`await GetUserInfo();`前に次のコードを追加します。

```cs
// Initialize Graph client
GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
    async (requestMessage) =>
    {
        var accounts = await PCA.GetAccountsAsync();

        var result = await PCA.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();

        requestMessage.Headers.Authorization =
            new AuthenticationHeaderValue("Bearer", result.AccessToken);
    }));
```

次に、 `GetUserInfo`関数を更新して、Microsoft Graph からユーザーの詳細を取得します。 既存の `GetUserInfo` 関数を、以下の関数で置換します。

```cs
private async Task GetUserInfo()
{
    // Get the logged on user's profile (/me)
    var user = await GraphClient.Me.Request().GetAsync();

    UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
    UserName = user.DisplayName;
    UserEmail = string.IsNullOrEmpty(user.Mail) ? user.UserPrincipalName : user.Mail;
}
```

変更を保存してすぐにアプリを実行すると、サインイン後にユーザーの表示名と電子メールアドレスで UI が更新されます。
