<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="d06bd-101">Visual Studio を開き、**[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="d06bd-102">[**新しいプロジェクトの作成**] ダイアログで、[**モバイルアプリ (Xamarin)**] を選択し、[**次へ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 [新しいプロジェクトの作成] ダイアログ](images/new-project-dialog.png)

1. <span data-ttu-id="d06bd-104">[**新しいプロジェクトの構成**] ダイアログで、 `GraphTutorial` **プロジェクト名**と**ソリューション名**を入力し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="d06bd-105">これらのラボ手順で指定した Visual Studio プロジェクトに対して、まったく同じ名前を入力してください。</span><span class="sxs-lookup"><span data-stu-id="d06bd-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="d06bd-106">Visual Studio プロジェクトの名前が、コードでの名前空間の一部になります。</span><span class="sxs-lookup"><span data-stu-id="d06bd-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="d06bd-107">この手順のコードは、この手順で指定した Visual Studio プロジェクト名に一致する名前空間に応じて異なります。</span><span class="sxs-lookup"><span data-stu-id="d06bd-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="d06bd-108">異なるプロジェクト名を使用する場合には、Visual Studio プロジェクト作成時に入力したプロジェクト名に一致するようにすべての名前空間を調整しないと、コードがコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="d06bd-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 [新しいプロジェクトの構成] ダイアログ](images/configure-new-project-dialog.png)

1. <span data-ttu-id="d06bd-110">[**クロスプラットフォームアプリの新規作成**] ダイアログで、**空**のテンプレートを選択し、**プラットフォーム**の下で構築するプラットフォームを選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="d06bd-111">[ **OK]** を選択してソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 の [クロスプラットフォームアプリの新規作成] ダイアログ](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="d06bd-113">パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="d06bd-113">Install packages</span></span>

<span data-ttu-id="d06bd-114">に進む前に、後で使用する追加の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d06bd-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="d06bd-115">Azure AD 認証とトークン管理を処理するための[クライアント](https://www.nuget.org/packages/Microsoft.Identity.Client/)。</span><span class="sxs-lookup"><span data-stu-id="d06bd-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="d06bd-116">Microsoft Graph に電話をかけるための[グラフ](https://www.nuget.org/packages/Microsoft.Graph/)です。</span><span class="sxs-lookup"><span data-stu-id="d06bd-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="d06bd-117">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-117">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="d06bd-118">パッケージ マネージャー コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-118">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.0.1 -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="d06bd-119">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="d06bd-119">Design the app</span></span>

<span data-ttu-id="d06bd-120">最初に、認証`App`の状態とサインインしているユーザーを追跡するための変数を追加するクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-120">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="d06bd-121">**ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開**し、app.xaml ファイルを**展開します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-121">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="d06bd-122">**App.xaml.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-122">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="d06bd-123">クラス宣言`INotifyPropertyChanged`にインターフェイスを追加します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-123">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="d06bd-124">次のプロパティを`App`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-124">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="d06bd-125">次の関数を`App`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-125">Add the following functions to the `App` class.</span></span> <span data-ttu-id="d06bd-126">、、および`GetUserInfo`関数は、現時点では単なるプレースホルダーです。 `SignIn` `SignOut`</span><span class="sxs-lookup"><span data-stu-id="d06bd-126">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

    ```csharp
    public async Task SignIn()
    {
        await GetUserInfo();

        IsSignedIn = true;
    }

    public async Task SignOut()
    {
        UserPhoto = null;
        UserName = string.Empty;
        UserEmail = string.Empty;
        IsSignedIn = false;
    }

    private async Task GetUserInfo()
    {
        UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
        UserName = "Adele Vance";
        UserEmail = "adelev@contoso.com";
    }

    private Stream GetUserPhoto()
    {
        // Return the default photo
        return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
    }
    ```

1. <span data-ttu-id="d06bd-127">関数`GetUserPhoto`は、現在の既定の写真を返します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-127">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="d06bd-128">独自のファイルを指定するか、または[GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)からサンプルで使用されているファイルをダウンロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-128">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="d06bd-129">独自のファイルを使用する場合は、そのファイルの名前を**no-profile-pic**に変更します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-129">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="d06bd-130">ファイルを **/GraphTutorial/GraphTutorial**ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="d06bd-130">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="d06bd-131">**ソリューションエクスプローラー**でファイルを右クリックし、[**プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-131">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="d06bd-132">[**プロパティ**] ウィンドウで、[**ビルドアクション**] の値を [**埋め込ま**れたリソース] に変更します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-132">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![PNG ファイルの [プロパティ] ウィンドウのスクリーンショット](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="d06bd-134">アプリナビゲーション</span><span class="sxs-lookup"><span data-stu-id="d06bd-134">App navigation</span></span>

<span data-ttu-id="d06bd-135">このセクションでは、アプリケーションのメインページを[マスター/詳細ページ](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)に変更します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-135">In this section, you'll change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="d06bd-136">これにより、アプリの表示を切り替えるナビゲーションメニューが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-136">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="d06bd-137">**Graphtutorial**プロジェクトで**MainPage**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-137">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a><span data-ttu-id="d06bd-138">メニューを実装する</span><span class="sxs-lookup"><span data-stu-id="d06bd-138">Implement the menu</span></span>

1. <span data-ttu-id="d06bd-139">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいフォルダー**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-139">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="d06bd-140">フォルダーに `Models` という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-140">Name the folder `Models`.</span></span>

1. <span data-ttu-id="d06bd-141">[**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`NavMenuItem`の名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-141">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="d06bd-142">**NavMenuItem.cs**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="d06bd-143">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`MenuPage`、ページに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-143">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="d06bd-144">[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-144">Select **Add**.</span></span>

1. <span data-ttu-id="d06bd-145">**MenuPage**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-145">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. <span data-ttu-id="d06bd-146">**ソリューションエクスプローラー**で**MenuPage**を展開し、 **MenuPage.xaml.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-146">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="d06bd-147">その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-147">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="d06bd-148">Visual Studio は**MenuPage.xaml.cs**でエラーを報告します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-148">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="d06bd-149">これらのエラーは、後の手順で解決されます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-149">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="d06bd-150">ウェルカムページを実装する</span><span class="sxs-lookup"><span data-stu-id="d06bd-150">Implement the welcome page</span></span>

1. <span data-ttu-id="d06bd-151">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`WelcomePage`、ページに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-151">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="d06bd-152">[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-152">Select **Add**.</span></span> <span data-ttu-id="d06bd-153">**WelcomePage**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-153">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. <span data-ttu-id="d06bd-154">**ソリューションエクスプローラー**で**WelcomePage**を展開し、 **WelcomePage.xaml.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-154">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="d06bd-155">次の関数を `WelcomePage` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-155">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-page"></a><span data-ttu-id="d06bd-156">[予定表の追加] ページ</span><span class="sxs-lookup"><span data-stu-id="d06bd-156">Add calendar page</span></span>

<span data-ttu-id="d06bd-157">ここで、予定表ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-157">Now add a calendar page.</span></span> <span data-ttu-id="d06bd-158">これは、現時点でのプレースホルダーにすぎません。</span><span class="sxs-lookup"><span data-stu-id="d06bd-158">This will just be a placeholder for now.</span></span>

1. <span data-ttu-id="d06bd-159">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`CalendarPage`、ページに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-159">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="d06bd-160">[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-160">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="d06bd-161">MainPage 分離コードを更新する</span><span class="sxs-lookup"><span data-stu-id="d06bd-161">Update MainPage code-behind</span></span>

<span data-ttu-id="d06bd-162">すべてのページが適切に配置されたので、 **MainPage**の分離コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-162">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="d06bd-163">**ソリューションエクスプローラー**で**MainPage**を展開し、 **MainPage.xaml.cs**ファイルを開き、その内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d06bd-163">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="d06bd-164">すべての変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-164">Save all of your changes.</span></span> <span data-ttu-id="d06bd-165">実行するプロジェクト (Android、iOS、または UWP) を右クリックして、[**スタートアッププロジェクトに設定**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-165">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="d06bd-166">**F5**キーを押すか、[デバッグ] > 選択して、Visual Studio で**デバッグを開始**します。</span><span class="sxs-lookup"><span data-stu-id="d06bd-166">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![Android、iOS、および UWP バージョンのアプリケーションのスクリーンショット](./images/welcome-page.png)
