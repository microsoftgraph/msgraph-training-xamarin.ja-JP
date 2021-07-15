<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="be0bd-101">Visual Studio を開き、**[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="be0bd-102">[新しい **プロジェクトの作成] ダイアログで** 、[ **モバイル アプリ (Xamarin.Forms)] を** 選択し、[次へ] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 [新しいプロジェクトの作成] ダイアログ ボックス](images/new-project-dialog.png)

1. <span data-ttu-id="be0bd-104">[新しい **プロジェクトの構成]** ダイアログで、[プロジェクト名] と [ソリューションProject] を入力し、[作成 `GraphTutorial` ] を **選択します**。  </span><span class="sxs-lookup"><span data-stu-id="be0bd-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="be0bd-105">これらのラボの手順で指定されているVisual Studio Project同じ名前を入力してください。</span><span class="sxs-lookup"><span data-stu-id="be0bd-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="be0bd-106">Visual Studio プロジェクトの名前が、コードでの名前空間の一部になります。</span><span class="sxs-lookup"><span data-stu-id="be0bd-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="be0bd-107">この手順のコードは、この手順で指定した Visual Studio プロジェクト名に一致する名前空間に応じて異なります。</span><span class="sxs-lookup"><span data-stu-id="be0bd-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="be0bd-108">異なるプロジェクト名を使用する場合には、Visual Studio プロジェクト作成時に入力したプロジェクト名に一致するようにすべての名前空間を調整しないと、コードがコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="be0bd-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 [新しいプロジェクトの構成] ダイアログ](images/configure-new-project-dialog.png)

1. <span data-ttu-id="be0bd-110">[新しい **クロス プラットフォーム アプリ]** ダイアログで、[空のテンプレート] を選択し、[プラットフォーム] でビルドするプラットフォーム **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="be0bd-111">**[OK] を選択** してソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 新しいクロス プラットフォーム アプリ ダイアログ](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="be0bd-113">パッケージのインストール</span><span class="sxs-lookup"><span data-stu-id="be0bd-113">Install packages</span></span>

<span data-ttu-id="be0bd-114">次に進む前に、後で使用NuGet追加のパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="be0bd-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="be0bd-115">[Microsoft.Identity.Client を](https://www.nuget.org/packages/Microsoft.Identity.Client/) 使用して、Azure ADトークン管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="be0bd-116">[Microsoft。Graph](https://www.nuget.org/packages/Microsoft.Graph/)Microsoft ユーザーに電話をGraph。</span><span class="sxs-lookup"><span data-stu-id="be0bd-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="be0bd-117">[タイム ゾーンクロスプラットフォームを処理する TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)</span><span class="sxs-lookup"><span data-stu-id="be0bd-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) for handling time zones cross-platform.</span></span>

1. <span data-ttu-id="be0bd-118">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-118">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="be0bd-119">パッケージ マネージャー コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-119">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 4.0.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="be0bd-120">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="be0bd-120">Design the app</span></span>

<span data-ttu-id="be0bd-121">まず、クラスを更新して、認証状態とサインインしているユーザーを追跡する変数 `App` を追加します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-121">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="be0bd-122">ソリューション **エクスプローラーで\*\*\*\*GraphTutorial プロジェクトを展開** し **、App.xaml ファイルを展開** します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-122">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="be0bd-123">**App.xaml.cs ファイルを開** き、ファイルの上部に次 `using` のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-123">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="be0bd-124">インターフェイスを `INotifyPropertyChanged` クラス宣言に追加します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-124">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="be0bd-125">クラスに次のプロパティを追加 `App` します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-125">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="be0bd-126">クラスに次の関数を追加 `App` します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-126">Add the following functions to the `App` class.</span></span> <span data-ttu-id="be0bd-127">、 `SignIn` `SignOut` 、および `GetUserInfo` 関数は、今のところプレースホルダーにすらなっています。</span><span class="sxs-lookup"><span data-stu-id="be0bd-127">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

1. <span data-ttu-id="be0bd-128">関数 `GetUserPhoto` は、現在の既定の写真を返します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-128">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="be0bd-129">独自のファイルを指定するか、サンプルで使用されているファイルをダウンロードするか、ファイルから[ダウンロードGitHub。](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)</span><span class="sxs-lookup"><span data-stu-id="be0bd-129">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="be0bd-130">独自のファイルを使用する場合は、名前を [no-profile-pic.png] **に変更します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-130">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="be0bd-131">ファイルを **./GraphTutorial/GraphTutorial ディレクトリにコピー** します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-131">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="be0bd-132">ソリューション エクスプローラーでファイルを右クリックし **、[プロパティ** ] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-132">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="be0bd-133">[プロパティ **] ウィンドウで** 、[ビルド アクション] の値 **を [埋め込み** リソース **] に変更します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-133">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![PNG ファイルの [プロパティ] ウィンドウのスクリーンショット](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="be0bd-135">アプリのナビゲーション</span><span class="sxs-lookup"><span data-stu-id="be0bd-135">App navigation</span></span>

<span data-ttu-id="be0bd-136">このセクションでは、アプリケーションのメイン ページを [FlyoutPage に変更します](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage)。</span><span class="sxs-lookup"><span data-stu-id="be0bd-136">In this section, you'll change the application's main page to a [FlyoutPage](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage).</span></span> <span data-ttu-id="be0bd-137">これにより、アプリの表示を切り替えるナビゲーション メニューが提供されます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-137">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="be0bd-138">**GraphTutorial** **プロジェクトで MainPage.xaml** ファイルを開き、その内容を次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-138">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a><span data-ttu-id="be0bd-139">メニューの実装</span><span class="sxs-lookup"><span data-stu-id="be0bd-139">Implement the menu</span></span>

1. <span data-ttu-id="be0bd-140">**GraphTutorial プロジェクトを右クリックし、[** 追加] 、[**新しいフォルダー**]**の順に選択します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-140">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="be0bd-141">フォルダーに `Models` という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-141">Name the folder `Models`.</span></span>

1. <span data-ttu-id="be0bd-142">[モデル] フォルダーを **右クリックし**、[追加] **、[Class...] の順に選択します**。 クラスに名前を付 `NavMenuItem` け、[追加] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-142">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="be0bd-143">**NavMenuItem.cs** ファイルを開き、その内容を次に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="be0bd-143">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="be0bd-144">**GraphTutorial プロジェクトを右クリックし、[** 追加] 、[**新しい** アイテム]**の順に選択します**。[コンテンツ **ページ] を選択** し、ページに名前を付きます `MenuPage` 。</span><span class="sxs-lookup"><span data-stu-id="be0bd-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="be0bd-145">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-145">Select **Add**.</span></span>

1. <span data-ttu-id="be0bd-146">**MenuPage.xaml ファイルを開** き、その内容を次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. <span data-ttu-id="be0bd-147">ソリューション **エクスプローラーで MenuPage.xaml** **を展開し\*\*\*\*、MenuPage.xaml.cs ファイルを開** きます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-147">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="be0bd-148">その内容を次に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="be0bd-148">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="be0bd-149">Visual Studio **MenuPage.xaml.cs でエラーが報告されます**。</span><span class="sxs-lookup"><span data-stu-id="be0bd-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="be0bd-150">これらのエラーは、後の手順で解決されます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="be0bd-151">ウェルカム ページの実装</span><span class="sxs-lookup"><span data-stu-id="be0bd-151">Implement the welcome page</span></span>

1. <span data-ttu-id="be0bd-152">**GraphTutorial プロジェクトを右クリックし、[** 追加] 、[**新しい** アイテム]**の順に選択します**。[コンテンツ **ページ] を選択** し、ページに名前を付きます `WelcomePage` 。</span><span class="sxs-lookup"><span data-stu-id="be0bd-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="be0bd-153">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-153">Select **Add**.</span></span> <span data-ttu-id="be0bd-154">**WelcomePage.xaml ファイルを開** き、その内容を次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. <span data-ttu-id="be0bd-155">ソリューション **エクスプローラーで WelcomePage.xaml** **を展開し\*\*\*\*、WelcomePage.xaml.cs ファイルを開** きます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-155">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="be0bd-156">次の関数を `WelcomePage` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-156">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a><span data-ttu-id="be0bd-157">予定表と新しいイベント ページを追加する</span><span class="sxs-lookup"><span data-stu-id="be0bd-157">Add calendar and new event pages</span></span>

<span data-ttu-id="be0bd-158">予定表ページと新しいイベント ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-158">Now add a calendar page and a new event page.</span></span> <span data-ttu-id="be0bd-159">これらは今のところプレースホルダーです。</span><span class="sxs-lookup"><span data-stu-id="be0bd-159">These will just be placeholders for now.</span></span>

1. <span data-ttu-id="be0bd-160">**GraphTutorial プロジェクトを右クリックし、[** 追加] 、[**新しい** アイテム]**の順に選択します**。[コンテンツ **ページ] を選択** し、ページに名前を付きます `CalendarPage` 。</span><span class="sxs-lookup"><span data-stu-id="be0bd-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="be0bd-161">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-161">Select **Add**.</span></span>

1. <span data-ttu-id="be0bd-162">**GraphTutorial プロジェクトを右クリックし、[** 追加] 、[**新しい** アイテム]**の順に選択します**。[コンテンツ **ページ] を選択** し、ページに名前を付きます `NewEventPage` 。</span><span class="sxs-lookup"><span data-stu-id="be0bd-162">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `NewEventPage`.</span></span> <span data-ttu-id="be0bd-163">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-163">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="be0bd-164">MainPage のコードを更新する</span><span class="sxs-lookup"><span data-stu-id="be0bd-164">Update MainPage code-behind</span></span>

<span data-ttu-id="be0bd-165">すべてのページが配置されたので **、MainPage.xaml** の後ろのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="be0bd-166">ソリューション **エクスプローラーで MainPage.xaml** **を** 展開し **、MainPage.xaml.cs** ファイルを開き、その内容全体を次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="be0bd-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="be0bd-167">すべての変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="be0bd-167">Save all of your changes.</span></span> <span data-ttu-id="be0bd-168">実行するプロジェクト (Android、iOS、または UWP) を右クリックし **、[StartUp** ファイルとして設定] をProject。</span><span class="sxs-lookup"><span data-stu-id="be0bd-168">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="be0bd-169">F5 **キーを押** するか、または [デバッグ] **> [デバッグ** の開始] を選択Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="be0bd-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![Android、iOS、UWP のバージョンのアプリケーションのスクリーンショット](./images/welcome-page.png)
