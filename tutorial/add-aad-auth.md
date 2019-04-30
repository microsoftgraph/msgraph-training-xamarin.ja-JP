<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="dab44-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="dab44-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="dab44-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="dab44-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="dab44-103">この手順では、 [.net 用 Microsoft Authentication Library (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="dab44-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="dab44-104">**ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開し、[**モデル**] フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dab44-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="dab44-105">[ **Add > Class...**.] を選択します。クラス`OAuthSettings`の名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dab44-105">Choose **Add > Class...**. Name the class `OAuthSettings` and choose **Add**.</span></span> <span data-ttu-id="dab44-106">**OAuthSettings.cs**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dab44-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string RedirectUri = "YOUR_REDIRECT_URI_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="dab44-107">git などのソース管理を使用している場合は、この時点で、ソース管理`OAuthSettings.cs`からファイルを除外して、アプリ ID が誤ってリークしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dab44-107">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="dab44-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="dab44-108">Implement sign-in</span></span>

<span data-ttu-id="dab44-109">**graphtutorial**プロジェクトで`using` **App.xaml.cs**ファイルを開き、次のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-109">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="dab44-110">次のプロパティを`App`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-110">Add the following properties to the `App` class.</span></span>

```cs
// UIParent used by Android version of the app
public static UIParent AuthUIParent = null;

// Microsoft Authentication client for native/mobile apps
public static PublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

<span data-ttu-id="dab44-111">次に、 `App`クラスの`PublicClientApplication`コンストラクターで新しいを作成します。</span><span class="sxs-lookup"><span data-stu-id="dab44-111">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

```cs
public App()
{
    InitializeComponent();

    PCA = new PublicClientApplication(OAuthSettings.ApplicationId);

    MainPage = new MainPage();
}
```

<span data-ttu-id="dab44-112">では、 `SignIn`を使用して`PublicClientApplication`アクセストークンを取得するように関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="dab44-112">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="dab44-113">行の`await GetUserInfo();`上に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-113">Add the following code above the `await GetUserInfo();` line.</span></span>

```cs
var scopes = OAuthSettings.Scopes.Split(' ');

// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
try
{
    var accounts = await PCA.GetAccountsAsync();
    var silentAuthResult = await PCA.AcquireTokenSilentAsync(
        scopes, accounts.FirstOrDefault());

    Debug.WriteLine("User already signed in.");
    Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
}
catch (MsalUiRequiredException)
{
    // This exception is thrown when an interactive sign-in is required.
    // Prompt the user to sign-in
    var authResult = await PCA.AcquireTokenAsync(scopes, AuthUIParent);
    Debug.WriteLine($"Access Token: {authResult.AccessToken}");
}
```

<span data-ttu-id="dab44-114">このコードでは、最初にアクセストークンを暗黙的に取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="dab44-114">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="dab44-115">ユーザーの情報がアプリのキャッシュに既に存在している場合 (たとえば、ユーザーが以前にサインアウトせずにそのアプリを閉じた場合)、これは成功し、ユーザーに確認を求める理由はありません。</span><span class="sxs-lookup"><span data-stu-id="dab44-115">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="dab44-116">キャッシュ内にユーザーの情報がない場合、この関数は`AcquireTokenSilentAsync`をスロー `MsalUiRequiredException`します。</span><span class="sxs-lookup"><span data-stu-id="dab44-116">If there is not a user's information in the cache, the `AcquireTokenSilentAsync` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="dab44-117">この場合、コードは interactive 関数を呼び出してトークンを`AcquireTokenAsync`取得します。</span><span class="sxs-lookup"><span data-stu-id="dab44-117">In this case, the code calls the interactive function to get a token, `AcquireTokenAsync`.</span></span>

<span data-ttu-id="dab44-118">次に、 `SignOut`キャッシュからユーザーの情報を削除するように関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="dab44-118">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="dab44-119">次のコードを`SignOut`関数の先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-119">Add the following code to the beginning of the `SignOut` function.</span></span>

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

<span data-ttu-id="dab44-120">最後に、読み込む`MainPage`ときにサインインするようにクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="dab44-120">Finally, update the `MainPage` class to sign-in when it loads.</span></span> <span data-ttu-id="dab44-121">`MainPage` **MainPage.xaml.cs**のクラスに次の関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-121">Add the following function to the `MainPage` class in **MainPage.xaml.cs**.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();
    await (Application.Current as App).SignIn();
}
```

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="dab44-122">Android プロジェクトを更新してサインインを有効にする</span><span class="sxs-lookup"><span data-stu-id="dab44-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="dab44-123">Xamarin android プロジェクトで使用されている場合、Microsoft 認証ライブラリには[Android 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)のいくつかの要件があります。</span><span class="sxs-lookup"><span data-stu-id="dab44-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="dab44-124">最初に、アプリ登録から Android マニフェストにリダイレクト URI を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dab44-124">First, you need to add the redirect URI from your app registration to the Android manifest.</span></span> <span data-ttu-id="dab44-125">これを行うには、新しいアクティビティを**graphtutorial. Android**プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-125">To do this, add a new activity to the **GraphTutorial.Android** project.</span></span> <span data-ttu-id="dab44-126">**graphtutorial**を右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**アクティビティ**] を選択し`MsalActivity`、アクティビティに名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dab44-126">Right-click the **GraphTutorial.Android** and choose **Add**, then **New Item...**. Choose **Activity**, name the activity `MsalActivity`, and choose **Add**.</span></span>

<span data-ttu-id="dab44-127">**MsalActivity.cs**ファイルを開いて`[Activity(Label = "MsalActivity")]`行を削除し、次の属性をクラス宣言の上に追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-127">Open the **MsalActivity.cs** file and delete the `[Activity(Label = "MsalActivity")]` line, then add the following attributes above the class declaration.</span></span>

```cs
// This class only exists to create the necessary activity in the Android
// manifest. Doing it this way allows the value of the RedirectUri constant
// to be inserted at build.
[Activity(Name = "microsoft.identity.client.BrowserTabActivity")]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = Models.OAuthSettings.RedirectUri, DataHost = "auth")]
```

<span data-ttu-id="dab44-128">次に、 **MainActivity.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-128">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="dab44-129">その後、関数`OnActivityResult`を上書きして、msal ライブラリに制御を渡します。</span><span class="sxs-lookup"><span data-stu-id="dab44-129">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="dab44-130">次のものを`MainActivity`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-130">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="dab44-131">iOS プロジェクトを更新してサインインを有効にする</span><span class="sxs-lookup"><span data-stu-id="dab44-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dab44-132">msal では、資格を使用する必要があるため、Apple developer アカウントで Visual Studio を構成して、プロビジョニングを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dab44-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="dab44-133">詳細については、「[デバイスプロビジョニング (Xamarin](/xamarin/ios/get-started/installation/device-provisioning))」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dab44-133">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="dab44-134">Xamarin ios プロジェクトで使用されている場合、Microsoft 認証ライブラリには[iOS 固有](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)のいくつかの要件があります。</span><span class="sxs-lookup"><span data-stu-id="dab44-134">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="dab44-135">最初に、キーチェーンアクセスを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dab44-135">First, you need to enable Keychain access.</span></span> <span data-ttu-id="dab44-136">ソリューションエクスプローラーで、 **graphtutorial. iOS**プロジェクトを展開し、**資格**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="dab44-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="dab44-137">**キーチェーン**の資格を見つけ、[**キーチェーンを有効にする**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dab44-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="dab44-138">**キーチェーングループ**で、という形式`com.YOUR_DOMAIN.GraphTutorial`のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-138">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![キーチェーン資格の構成のスクリーンショット](./images/enable-keychain-access.png)

<span data-ttu-id="dab44-140">次に、アプリ登録手順で構成したリダイレクト URI を、アプリが処理する URL の種類として登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dab44-140">Next, you need to register the redirect URI you configured in the app registration step as a URL type that your app handles.</span></span> <span data-ttu-id="dab44-141">**情報**ファイルを開き、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="dab44-141">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="dab44-142">[**アプリケーション**] タブで、[**バンドル識別子**] の値が、[利用**資格**] の**キーチェーングループ**に設定した値と一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dab44-142">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="dab44-143">そうでない場合は、今すぐ更新してください。</span><span class="sxs-lookup"><span data-stu-id="dab44-143">If it doesn't, update it now.</span></span>
- <span data-ttu-id="dab44-144">[**詳細設定**] タブで、[ **URL の種類**] セクションを探します。</span><span class="sxs-lookup"><span data-stu-id="dab44-144">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="dab44-145">URL の種類を次の値で追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-145">Add a URL type here with the following values:</span></span>
    - <span data-ttu-id="dab44-146">**識別子**:**バンドル識別子**の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="dab44-146">**Identifier**: set to the value of your **Bundle identifier**</span></span>
    - <span data-ttu-id="dab44-147">**URL スキーム**: アプリ登録のリダイレクト URI に設定します。`msal`</span><span class="sxs-lookup"><span data-stu-id="dab44-147">**URL Schemes**: set to the redirect URI from your app registration that begins with `msal`</span></span>
    - <span data-ttu-id="dab44-148">**役割**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="dab44-148">**Role**: `Editor`</span></span>
    - <span data-ttu-id="dab44-149">**アイコン**: 空のままにします</span><span class="sxs-lookup"><span data-stu-id="dab44-149">**Icon**: Leave empty</span></span>

![情報の [URL の種類] セクションのスクリーンショット](./images/add-url-type.png)

<span data-ttu-id="dab44-151">最後に、認証中にリダイレクトを処理するように、 **graphtutorial. iOS**プロジェクトのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="dab44-151">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="dab44-152">**AppDelegate.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-152">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="dab44-153">次の行をステートメント`FinishedLaunching`の`return`直前に関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-153">Add the following line to `FinishedLaunching` function just before the `return` statement.</span></span>

```cs
// Specify the Keychain access group
App.PCA.iOSKeychainSecurityGroup = "com.graphdevx.GraphTutorial";
```

<span data-ttu-id="dab44-154">最後に、 `OpenUrl`関数を上書きして、msal ライブラリに URL を渡します。</span><span class="sxs-lookup"><span data-stu-id="dab44-154">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="dab44-155">次のものを`AppDelegate`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-155">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="dab44-156">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="dab44-156">Storing the tokens</span></span>

<span data-ttu-id="dab44-157">Microsoft 認証ライブラリを Xamarin プロジェクトで使用する場合、既定では、ネイティブのセキュリティ保護されたストレージを利用してトークンをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="dab44-157">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="dab44-158">詳細については、「 [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dab44-158">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="dab44-159">サインインのテスト</span><span class="sxs-lookup"><span data-stu-id="dab44-159">Test sign-in</span></span>

<span data-ttu-id="dab44-160">この時点で、アプリケーションを実行して [**サインイン**] ボタンをタップすると、サインインするように求められます。</span><span class="sxs-lookup"><span data-stu-id="dab44-160">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="dab44-161">サインインに成功すると、デバッガーの出力にアクセストークンが出力されたことがわかります。</span><span class="sxs-lookup"><span data-stu-id="dab44-161">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Visual Studio の [出力] ウィンドウのスクリーンショット](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="dab44-163">ユーザーの詳細を取得する</span><span class="sxs-lookup"><span data-stu-id="dab44-163">Get user details</span></span>

<span data-ttu-id="dab44-164">では、 `SignIn` **App.xaml.cs**の関数を更新し`GraphServiceClient`て、を初期化します。</span><span class="sxs-lookup"><span data-stu-id="dab44-164">Now update the `SignIn` function in **App.xaml.cs** to initialize the `GraphServiceClient`.</span></span> <span data-ttu-id="dab44-165">行の`await GetUserInfo();`前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="dab44-165">Add the following code before the `await GetUserInfo();` line.</span></span>

```cs
// Initialize Graph client
GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
    async (requestMessage) =>
    {
        var accounts = await PCA.GetAccountsAsync();

        var result = await PCA.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());

        requestMessage.Headers.Authorization =
            new AuthenticationHeaderValue("Bearer", result.AccessToken);
    }));
```

<span data-ttu-id="dab44-166">次に、 `GetUserInfo`関数を更新して、Microsoft Graph からユーザーの詳細を取得します。</span><span class="sxs-lookup"><span data-stu-id="dab44-166">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="dab44-167">既存の `GetUserInfo` 関数を、以下の関数で置換します。</span><span class="sxs-lookup"><span data-stu-id="dab44-167">Replace the existing `GetUserInfo` function with the following.</span></span>

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

<span data-ttu-id="dab44-168">変更を保存してすぐにアプリを実行すると、サインイン後にユーザーの表示名と電子メールアドレスで UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="dab44-168">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>

