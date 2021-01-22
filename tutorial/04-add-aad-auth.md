<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f1a68-101">この演習では、前の演習のアプリケーションを拡張して、Azure AD での認証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="f1a68-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="f1a68-102">これは、Microsoft Graph を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="f1a68-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="f1a68-103">この手順では [、Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="f1a68-104">ソリューション **エクスプローラーで\*\*\*\*、GraphTu solutionl** プロジェクトを展開し **、[Models]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="f1a68-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="f1a68-105">[ **クラスの>を選択します**。クラスに名前を付 `OAuthSettings` け、[追加] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="f1a68-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="f1a68-106">新しい **ファイルOAuthSettings.cs** 開き、その内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="f1a68-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. <span data-ttu-id="f1a68-107">アプリ `YOUR_APP_ID_HERE` 登録のアプリケーション ID に置き換える。</span><span class="sxs-lookup"><span data-stu-id="f1a68-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="f1a68-108">git などのソース管理を使っている場合は、アプリ ID が誤って漏洩しないように、ファイルをソース管理から除外する良い時期です `OAuthSettings.cs` 。</span><span class="sxs-lookup"><span data-stu-id="f1a68-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="f1a68-109">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="f1a68-109">Implement sign-in</span></span>

1. <span data-ttu-id="f1a68-110">**GraphTu App.xaml.cs\*\*\*\*ファイルを** 開き、ファイルの一番上に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. <span data-ttu-id="f1a68-111">アプリケーションクラス **の宣言** 行を変更して、Application の名前の競合を解決 **します**。</span><span class="sxs-lookup"><span data-stu-id="f1a68-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="f1a68-112">次のプロパティをクラスに追加 `App` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="f1a68-113">次に、クラスの `PublicClientApplication` コンストラクターに新しいオブジェクトを作成 `App` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="f1a68-114">アクセス トークン `SignIn` を取得するために `PublicClientApplication` 関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="f1a68-115">行の上に次のコードを `await GetUserInfo();` 追加します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="f1a68-116">このコードでは、最初にアクセス トークンをサイレント モードで取得します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="f1a68-117">ユーザーの情報が既にアプリのキャッシュにある場合 (たとえば、ユーザーがサインアウトせずに以前にアプリを閉じた場合)、これは成功し、ユーザーに確認を求める理由はありません。</span><span class="sxs-lookup"><span data-stu-id="f1a68-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="f1a68-118">キャッシュにユーザーの情報が含されていない場合、関数は `AcquireTokenSilent().ExecuteAsync()` `MsalUiRequiredException` .</span><span class="sxs-lookup"><span data-stu-id="f1a68-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="f1a68-119">この場合、コードは対話型関数を呼び出してトークンを取得します `AcquireTokenInteractive` 。</span><span class="sxs-lookup"><span data-stu-id="f1a68-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="f1a68-120">キャッシュから `SignOut` ユーザーの情報を削除するには、関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="f1a68-121">次のコードを関数の先頭に追加 `SignOut` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="f1a68-122">Android プロジェクトを更新してサインインを有効にする</span><span class="sxs-lookup"><span data-stu-id="f1a68-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="f1a68-123">Xamarin Android プロジェクトで使用する場合、Microsoft 認証ライブラリには Android に固有のいくつかの [要件があります](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)。</span><span class="sxs-lookup"><span data-stu-id="f1a68-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

1. <span data-ttu-id="f1a68-124">**GraphTu読み込み.Android** プロジェクトで、[**プロパティ**] フォルダーを展開し、**次AndroidManifest.xml。**</span><span class="sxs-lookup"><span data-stu-id="f1a68-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="f1a68-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With,** then **Source Code Editor**.</span><span class="sxs-lookup"><span data-stu-id="f1a68-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="f1a68-126">すべての内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="f1a68-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="f1a68-127">ファイル **MainActivity.cs** を開き、ファイルの一番上に次 `using` のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="f1a68-128">MSAL `OnActivityResult` ライブラリにコントロールを渡す関数をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="f1a68-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="f1a68-129">クラスに次を追加 `MainActivity` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="f1a68-130">関数で `OnCreate` 、行の後に次の行を追加 `LoadApplication(new App());` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="f1a68-131">iOS プロジェクトを更新してサインインを有効にする</span><span class="sxs-lookup"><span data-stu-id="f1a68-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f1a68-132">MSAL では Entitlements.plist ファイルの使用が必要なので、プロビジョニングを有効にVisual Studio Apple 開発者アカウントでこのファイルを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1a68-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="f1a68-133">このチュートリアルを iPhone シミュレーターで実行している場合は **、GraphTu tutoriall.iOS** プロジェクトの設定の [Build->**iOS Bundle Signing]** の [Custom **Entitlements]** フィールドに **Entitlements.plist** を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1a68-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="f1a68-134">詳しくは [、Xamarin.iOS のデバイス プロビジョニングに関するページをご覧ください](/xamarin/ios/get-started/installation/device-provisioning)。</span><span class="sxs-lookup"><span data-stu-id="f1a68-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="f1a68-135">Xamarin iOS プロジェクトで使用する場合、Microsoft 認証ライブラリには iOS に固有のいくつかの [要件があります](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)。</span><span class="sxs-lookup"><span data-stu-id="f1a68-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

1. <span data-ttu-id="f1a68-136">ソリューション エクスプローラーで **GraphTu solutionl.iOS** プロジェクトを展開し **、Entitlements.plist ファイルを開** きます。</span><span class="sxs-lookup"><span data-stu-id="f1a68-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="f1a68-137">キーチェーンの権利 **を** 見つけて **、[KeyChain を有効にする] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="f1a68-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="f1a68-138">In **Keychain Groups,** add an entry with the format `com.companyname.GraphTutorial` .</span><span class="sxs-lookup"><span data-stu-id="f1a68-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![キーチェーンの権利の構成のスクリーンショット](./images/enable-keychain-access.png)

1. <span data-ttu-id="f1a68-140">認証中にリダイレクト **を処理するために GraphTu読み込み.iOS** プロジェクトのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="f1a68-141">新しい **AppDelegate.cs** を開き、ファイルの上部に `using` 次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="f1a68-142">行の直前に機能 `FinishedLaunching` する次の行を追加 `LoadApplication(new App());` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="f1a68-143">関数を `OpenUrl` オーバーライドして、MSAL ライブラリに URL を渡します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="f1a68-144">クラスに次を追加 `AppDelegate` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="f1a68-145">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="f1a68-145">Storing the tokens</span></span>

<span data-ttu-id="f1a68-146">Xamarin プロジェクトで Microsoft 認証ライブラリを使用する場合、既定では、ネイティブのセキュリティで保護されたストレージを利用してトークンをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="f1a68-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="f1a68-147">詳細 [については、「トークン キャッシュのシリアル化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1a68-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="f1a68-148">サインインのテスト</span><span class="sxs-lookup"><span data-stu-id="f1a68-148">Test sign-in</span></span>

<span data-ttu-id="f1a68-149">この時点で、アプリケーションを実行して [サインイン] ボタンをタップすると、サインインするように求めるメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1a68-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="f1a68-150">サインインが成功すると、アクセス トークンがデバッガーの出力に出力されます。</span><span class="sxs-lookup"><span data-stu-id="f1a68-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![ページの [出力] ウィンドウのVisual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="f1a68-152">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="f1a68-152">Get user details</span></span>

1. <span data-ttu-id="f1a68-153">App クラスに新しい関数 **を追加** して初期化します `GraphServiceClient` 。</span><span class="sxs-lookup"><span data-stu-id="f1a68-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="f1a68-154">代わりに `SignIn` この関数 **App.xaml.cs** 呼び出すメソッドの関数を更新します `GetUserInfo` 。</span><span class="sxs-lookup"><span data-stu-id="f1a68-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="f1a68-155">関数から次を削除 `SignIn` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="f1a68-156">関数の最後に次を追加 `SignIn` します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="f1a68-157">Microsoft `GetUserInfo` Graph からユーザーの詳細を取得するために関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="f1a68-158">既存の `GetUserInfo` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1a68-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="f1a68-159">変更内容を保存し、アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1a68-159">Save your changes and run the app.</span></span> <span data-ttu-id="f1a68-160">サインイン後、UI はユーザーの表示名と電子メール アドレスで更新されます。</span><span class="sxs-lookup"><span data-stu-id="f1a68-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
