<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="91b29-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="91b29-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="91b29-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="91b29-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="91b29-103">この手順では、 [.net 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="91b29-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="91b29-104">**ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開し、[**モデル**] フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="91b29-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="91b29-105">[ **Add > Class...**.] を選択します。クラス`OAuthSettings`の名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="91b29-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span> <span data-ttu-id="91b29-106">**OAuthSettings.cs**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91b29-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="91b29-107">を`YOUR_APP_ID_HERE`アプリ登録のアプリケーション ID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91b29-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="91b29-108">Git などのソース管理を使用している場合は、この時点で、ソース管理`OAuthSettings.cs`からファイルを除外して、アプリ ID が誤ってリークしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="91b29-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="91b29-109">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="91b29-109">Implement sign-in</span></span>

<span data-ttu-id="91b29-110">**Graphtutorial**プロジェクトで`using` **App.xaml.cs**ファイルを開き、次のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="91b29-111">次のプロパティを`App`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-111">Add the following properties to the `App` class.</span></span>

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

<span data-ttu-id="91b29-112">次に、 `App`クラスの`PublicClientApplication`コンストラクターで新しいを作成します。</span><span class="sxs-lookup"><span data-stu-id="91b29-112">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

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

<span data-ttu-id="91b29-113">では、 `SignIn`を使用して`PublicClientApplication`アクセストークンを取得するように関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="91b29-113">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="91b29-114">行の`await GetUserInfo();`上に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-114">Add the following code above the `await GetUserInfo();` line.</span></span>

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

<span data-ttu-id="91b29-115">このコードでは、最初にアクセストークンを暗黙的に取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="91b29-115">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="91b29-116">ユーザーの情報がアプリのキャッシュに既に存在している場合 (たとえば、ユーザーが以前にサインアウトせずにそのアプリを閉じた場合)、これは成功し、ユーザーに確認を求める理由はありません。</span><span class="sxs-lookup"><span data-stu-id="91b29-116">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="91b29-117">キャッシュ内にユーザーの情報がない場合、この関数は`AcquireTokenSilent().ExecuteAsync()`をスロー `MsalUiRequiredException`します。</span><span class="sxs-lookup"><span data-stu-id="91b29-117">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="91b29-118">この場合、コードは interactive 関数を呼び出してトークンを`AcquireTokenInteractive`取得します。</span><span class="sxs-lookup"><span data-stu-id="91b29-118">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

<span data-ttu-id="91b29-119">次に、 `SignOut`キャッシュからユーザーの情報を削除するように関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="91b29-119">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="91b29-120">次のコードを`SignOut`関数の先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-120">Add the following code to the beginning of the `SignOut` function.</span></span>

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

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="91b29-121">Android プロジェクトを更新してサインインを有効にする</span><span class="sxs-lookup"><span data-stu-id="91b29-121">Update Android project to enable sign-in</span></span>

<span data-ttu-id="91b29-122">Xamarin Android プロジェクトで使用されている場合、Microsoft 認証ライブラリには[Android 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)のいくつかの要件があります。</span><span class="sxs-lookup"><span data-stu-id="91b29-122">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="91b29-123">最初に、Android マニフェストを更新してリダイレクト URI を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91b29-123">First, you need to update the Android manifest to register the redirect URI.</span></span> <span data-ttu-id="91b29-124">**Graphtutorial. Android**プロジェクトで、 **Properties**フォルダーを展開し、 **androidmanifest**を開きます。</span><span class="sxs-lookup"><span data-stu-id="91b29-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="91b29-125">Visual Studio for Mac を使用している場合は、ファイルの下部にあるタブを使用して、**ソース**ビューに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="91b29-125">If you're using Visual Studio for Mac, switch to the **Source** view using the tabs at the bottom of the file.</span></span> <span data-ttu-id="91b29-126">内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91b29-126">Replace the entire contents with the following.</span></span>

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

<span data-ttu-id="91b29-127">を`YOUR_APP_ID_HERE`アプリ登録のアプリケーション ID で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="91b29-127">Replace `YOUR_APP_ID_HERE` with your application ID from your app registration.</span></span>

<span data-ttu-id="91b29-128">次に、 **MainActivity.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-128">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="91b29-129">その後、関数`OnActivityResult`を上書きして、msal ライブラリに制御を渡します。</span><span class="sxs-lookup"><span data-stu-id="91b29-129">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="91b29-130">次のものを`MainActivity`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-130">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

<span data-ttu-id="91b29-131">最後に、 `OnCreate`関数の行の`LoadApplication(new App());`後に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-131">Finally, in the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="91b29-132">IOS プロジェクトを更新してサインインを有効にする</span><span class="sxs-lookup"><span data-stu-id="91b29-132">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="91b29-133">MSAL では、資格を使用する必要があるため、Apple developer アカウントで Visual Studio を構成して、プロビジョニングを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91b29-133">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="91b29-134">IPhone シミュレータでこのチュートリアルを実行している場合は、「Graphtutorial」の「**カスタム**の利用資格」フィールドに「**資格**を追加する必要があります **。 Ios**プロジェクトの設定、**ビルド >ios バンドル署名**。</span><span class="sxs-lookup"><span data-stu-id="91b29-134">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="91b29-135">詳細については、「[デバイスプロビジョニング (Xamarin](/xamarin/ios/get-started/installation/device-provisioning))」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91b29-135">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="91b29-136">Xamarin iOS プロジェクトで使用されている場合、Microsoft 認証ライブラリには[iOS 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)のいくつかの要件があります。</span><span class="sxs-lookup"><span data-stu-id="91b29-136">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="91b29-137">最初に、キーチェーンアクセスを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91b29-137">First, you need to enable Keychain access.</span></span> <span data-ttu-id="91b29-138">ソリューションエクスプローラーで、 **Graphtutorial. iOS**プロジェクトを展開し、**資格**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="91b29-138">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="91b29-139">**キーチェーン**の資格を見つけ、[**キーチェーンを有効にする**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="91b29-139">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="91b29-140">**キーチェーングループ**で、という形式`com.YOUR_DOMAIN.GraphTutorial`のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-140">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![キーチェーン資格の構成のスクリーンショット](./images/enable-keychain-access.png)

<span data-ttu-id="91b29-142">次に、アプリが処理する URL の種類として、既定の MSAL リダイレクト URI を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91b29-142">Next, you need to register the default MSAL redirect URI as a URL type that your app handles.</span></span> <span data-ttu-id="91b29-143">**情報**ファイルを開き、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="91b29-143">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="91b29-144">[**アプリケーション**] タブで、[**バンドル識別子**] の値が、[利用**資格**] の**キーチェーングループ**に設定した値と一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="91b29-144">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="91b29-145">そうでない場合は、今すぐ更新してください。</span><span class="sxs-lookup"><span data-stu-id="91b29-145">If it doesn't, update it now.</span></span>
- <span data-ttu-id="91b29-146">[**詳細設定**] タブで、[ **URL の種類**] セクションを探します。</span><span class="sxs-lookup"><span data-stu-id="91b29-146">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="91b29-147">URL の種類を次の値で追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-147">Add a URL type here with the following values:</span></span>
  - <span data-ttu-id="91b29-148">**識別子**:**バンドル識別子**の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="91b29-148">**Identifier**: set to the value of your **Bundle identifier**</span></span>
  - <span data-ttu-id="91b29-149">**URL スキーム**: に`msal{YOUR-APP-ID}`設定します。</span><span class="sxs-lookup"><span data-stu-id="91b29-149">**URL Schemes**: set to `msal{YOUR-APP-ID}`.</span></span> <span data-ttu-id="91b29-150">たとえば、アプリ ID が`67ad5eba-0cfc-414d-8f9f-0a6d973a907c`である場合は、をに`msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`設定します。</span><span class="sxs-lookup"><span data-stu-id="91b29-150">For example, if your app ID is `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, you would set this to `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`.</span></span>
  - <span data-ttu-id="91b29-151">**役割**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="91b29-151">**Role**: `Editor`</span></span>
  - <span data-ttu-id="91b29-152">**アイコン**: 空のままにします</span><span class="sxs-lookup"><span data-stu-id="91b29-152">**Icon**: Leave empty</span></span>

![情報の [URL の種類] セクションのスクリーンショット](./images/add-url-type.png)

<span data-ttu-id="91b29-154">最後に、認証中にリダイレクトを処理するように、 **Graphtutorial. iOS**プロジェクトのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="91b29-154">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="91b29-155">**AppDelegate.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-155">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="91b29-156">行の`LoadApplication(new App());`直前に、 `FinishedLaunching`次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-156">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

<span data-ttu-id="91b29-157">最後に、 `OpenUrl`関数を上書きして、msal ライブラリに URL を渡します。</span><span class="sxs-lookup"><span data-stu-id="91b29-157">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="91b29-158">次のものを`AppDelegate`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-158">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="91b29-159">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="91b29-159">Storing the tokens</span></span>

<span data-ttu-id="91b29-160">Microsoft 認証ライブラリを Xamarin プロジェクトで使用する場合、既定では、ネイティブのセキュリティ保護されたストレージを利用してトークンをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="91b29-160">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="91b29-161">詳細については、「 [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91b29-161">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="91b29-162">サインインのテスト</span><span class="sxs-lookup"><span data-stu-id="91b29-162">Test sign-in</span></span>

<span data-ttu-id="91b29-163">この時点で、アプリケーションを実行して [**サインイン**] ボタンをタップすると、サインインするように求められます。</span><span class="sxs-lookup"><span data-stu-id="91b29-163">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="91b29-164">サインインに成功すると、デバッガーの出力にアクセストークンが出力されたことがわかります。</span><span class="sxs-lookup"><span data-stu-id="91b29-164">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Visual Studio の [出力] ウィンドウのスクリーンショット](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="91b29-166">ユーザーの詳細を取得する</span><span class="sxs-lookup"><span data-stu-id="91b29-166">Get user details</span></span>

<span data-ttu-id="91b29-167">では、 `SignIn` **App.xaml.cs**の関数を更新し`GraphServiceClient`て、を初期化します。</span><span class="sxs-lookup"><span data-stu-id="91b29-167">Now update the `SignIn` function in **App.xaml.cs** to initialize the `GraphServiceClient`.</span></span> <span data-ttu-id="91b29-168">行の`await GetUserInfo();`前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="91b29-168">Add the following code before the `await GetUserInfo();` line.</span></span>

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

<span data-ttu-id="91b29-169">次に、 `GetUserInfo`関数を更新して、Microsoft Graph からユーザーの詳細を取得します。</span><span class="sxs-lookup"><span data-stu-id="91b29-169">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="91b29-170">既存の `GetUserInfo` 関数を、以下の関数で置換します。</span><span class="sxs-lookup"><span data-stu-id="91b29-170">Replace the existing `GetUserInfo` function with the following.</span></span>

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

<span data-ttu-id="91b29-171">変更を保存してすぐにアプリを実行すると、サインイン後にユーザーの表示名と電子メールアドレスで UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="91b29-171">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>