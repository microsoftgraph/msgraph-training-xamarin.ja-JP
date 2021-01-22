<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="38292-101">Visual Studio を開き、**[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="38292-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="38292-102">In the **Create a new project** dialog, choose Mobile App **(Xamarin.Forms),** then select **Next**.</span><span class="sxs-lookup"><span data-stu-id="38292-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 で新しいプロジェクトの作成ダイアログを作成する](images/new-project-dialog.png)

1. <span data-ttu-id="38292-104">[新しい **プロジェクトの構成]** ダイアログで、プロジェクト名とソリューション名を入力し、[作成] `GraphTutorial` を選択 **します**。  </span><span class="sxs-lookup"><span data-stu-id="38292-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="38292-105">このラボの手順で指定されているプロジェクトの名前とVisual Studio名前を入力してください。</span><span class="sxs-lookup"><span data-stu-id="38292-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="38292-106">Visual Studio プロジェクトの名前が、コードでの名前空間の一部になります。</span><span class="sxs-lookup"><span data-stu-id="38292-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="38292-107">この手順のコードは、この手順で指定した Visual Studio プロジェクト名に一致する名前空間に応じて異なります。</span><span class="sxs-lookup"><span data-stu-id="38292-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="38292-108">異なるプロジェクト名を使用する場合には、Visual Studio プロジェクト作成時に入力したプロジェクト名に一致するようにすべての名前空間を調整しないと、コードがコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="38292-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 の [新しいプロジェクトの構成] ダイアログ](images/configure-new-project-dialog.png)

1. <span data-ttu-id="38292-110">[**新しいクロス プラットフォーム アプリ**]ダイアログで、空のテンプレートを選択し、[プラットフォーム] でビルドするプラットフォーム **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="38292-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="38292-111">**[OK] を** 選択してソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="38292-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 の新しいクロス プラットフォーム アプリ ダイアログ](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="38292-113">パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="38292-113">Install packages</span></span>

<span data-ttu-id="38292-114">次に進む前に、後で使用する追加の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="38292-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="38292-115">[Azure 認証とトークン管理を処理AD Microsoft.Identity.Client。](https://www.nuget.org/packages/Microsoft.Identity.Client/)</span><span class="sxs-lookup"><span data-stu-id="38292-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="38292-116">Microsoft Graph を呼び出す[Microsoft.Graph。](https://www.nuget.org/packages/Microsoft.Graph/)</span><span class="sxs-lookup"><span data-stu-id="38292-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="38292-117">[タイム ゾーンクロスプラットフォームを処理する TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)</span><span class="sxs-lookup"><span data-stu-id="38292-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) for handling time zones cross-platform.</span></span>

1. <span data-ttu-id="38292-118">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="38292-118">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="38292-119">パッケージ マネージャー コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="38292-119">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="38292-120">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="38292-120">Design the app</span></span>

<span data-ttu-id="38292-121">まず、クラスを更新して、認証状態とサインインしているユーザーを追跡する変数 `App` を追加します。</span><span class="sxs-lookup"><span data-stu-id="38292-121">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="38292-122">ソリューション **エクスプローラーで GraphTu** **solutionl プロジェクトを展開** し **、App.xaml ファイルを展開** します。</span><span class="sxs-lookup"><span data-stu-id="38292-122">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="38292-123">新しい **App.xaml.cs** を開き、ファイルの一番上に次 `using` のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="38292-123">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="38292-124">クラス宣言 `INotifyPropertyChanged` にインターフェイスを追加します。</span><span class="sxs-lookup"><span data-stu-id="38292-124">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="38292-125">次のプロパティをクラスに追加 `App` します。</span><span class="sxs-lookup"><span data-stu-id="38292-125">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="38292-126">次の関数をクラスに追加 `App` します。</span><span class="sxs-lookup"><span data-stu-id="38292-126">Add the following functions to the `App` class.</span></span> <span data-ttu-id="38292-127">The `SignIn` `SignOut` , , and functions are `GetUserInfo` just placeholders for now.</span><span class="sxs-lookup"><span data-stu-id="38292-127">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

1. <span data-ttu-id="38292-128">この `GetUserPhoto` 関数は、現在のところ既定の写真を返します。</span><span class="sxs-lookup"><span data-stu-id="38292-128">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="38292-129">独自のファイルを指定するか、サンプルで使用されているファイルを [GitHub からダウンロードできます](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)。</span><span class="sxs-lookup"><span data-stu-id="38292-129">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="38292-130">独自のファイルを使用する場合は、ファイルの名前 **を変更して** no-profile-pic.png。</span><span class="sxs-lookup"><span data-stu-id="38292-130">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="38292-131">ファイルを **./GraphTu読み込み/GraphTu読み込みディレクトリにコピー** します。</span><span class="sxs-lookup"><span data-stu-id="38292-131">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="38292-132">ソリューション エクスプローラーでファイルを右クリックし、[ **プロパティ** ] を選択 **します**。</span><span class="sxs-lookup"><span data-stu-id="38292-132">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="38292-133">[プロパティ **] ウィンドウ** で、[ビルド アクション] の **値を [埋め込み** リソース **] に変更します**。</span><span class="sxs-lookup"><span data-stu-id="38292-133">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![PNG ファイルの [プロパティ] ウィンドウのスクリーンショット](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="38292-135">アプリナビゲーション</span><span class="sxs-lookup"><span data-stu-id="38292-135">App navigation</span></span>

<span data-ttu-id="38292-136">このセクションでは、アプリケーションのメイン ページをマスター/詳細ページ [に変更します](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。</span><span class="sxs-lookup"><span data-stu-id="38292-136">In this section, you'll change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="38292-137">これにより、アプリのビューを切り替えるナビゲーション メニューが提供されます。</span><span class="sxs-lookup"><span data-stu-id="38292-137">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="38292-138">**GraphTuxal** **プロジェクトの MainPage.xaml** ファイルを開き、その内容を次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38292-138">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a><span data-ttu-id="38292-139">メニューを実装する</span><span class="sxs-lookup"><span data-stu-id="38292-139">Implement the menu</span></span>

1. <span data-ttu-id="38292-140">**GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいフォルダー]**の順に選択します**。</span><span class="sxs-lookup"><span data-stu-id="38292-140">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="38292-141">フォルダーに `Models` という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="38292-141">Name the folder `Models`.</span></span>

1. <span data-ttu-id="38292-142">Models フォルダーを右 **クリックし**、[追加]、次に [**クラス\*\*\*\*...] の順に選択します**。クラスに名前を付 `NavMenuItem` け、[追加] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="38292-142">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="38292-143">新しいファイル **NavMenuItem.cs** 開き、その内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="38292-143">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="38292-144">**GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `MenuPage` .</span><span class="sxs-lookup"><span data-stu-id="38292-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="38292-145">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="38292-145">Select **Add**.</span></span>

1. <span data-ttu-id="38292-146">**MenuPage.xaml ファイルを開** き、その内容を次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38292-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. <span data-ttu-id="38292-147">ソリューション **エクスプローラーで MenuPage.xaml** **を展開し** 、 **ファイルMenuPage.xaml.csします** 。</span><span class="sxs-lookup"><span data-stu-id="38292-147">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="38292-148">内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="38292-148">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="38292-149">Visual Studioでエラーが **報告MenuPage.xaml.cs。**</span><span class="sxs-lookup"><span data-stu-id="38292-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="38292-150">これらのエラーは、後の手順で解決されます。</span><span class="sxs-lookup"><span data-stu-id="38292-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="38292-151">ウェルカム ページを実装する</span><span class="sxs-lookup"><span data-stu-id="38292-151">Implement the welcome page</span></span>

1. <span data-ttu-id="38292-152">**GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `WelcomePage` .</span><span class="sxs-lookup"><span data-stu-id="38292-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="38292-153">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="38292-153">Select **Add**.</span></span> <span data-ttu-id="38292-154">**WelcomePage.xaml ファイルを開** き、その内容を次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38292-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. <span data-ttu-id="38292-155">ソリューション **エクスプローラーで WelcomePage.xaml** **を展開し** 、 **ファイルWelcomePage.xaml.csします** 。</span><span class="sxs-lookup"><span data-stu-id="38292-155">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="38292-156">次の関数を `WelcomePage` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="38292-156">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a><span data-ttu-id="38292-157">予定表と新しいイベント ページを追加する</span><span class="sxs-lookup"><span data-stu-id="38292-157">Add calendar and new event pages</span></span>

<span data-ttu-id="38292-158">予定表ページと新しいイベント ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="38292-158">Now add a calendar page and a new event page.</span></span> <span data-ttu-id="38292-159">これらは、今のところプレースホルダーにすすむだけです。</span><span class="sxs-lookup"><span data-stu-id="38292-159">These will just be placeholders for now.</span></span>

1. <span data-ttu-id="38292-160">**GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `CalendarPage` .</span><span class="sxs-lookup"><span data-stu-id="38292-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="38292-161">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="38292-161">Select **Add**.</span></span>

1. <span data-ttu-id="38292-162">**GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `NewEventPage` .</span><span class="sxs-lookup"><span data-stu-id="38292-162">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `NewEventPage`.</span></span> <span data-ttu-id="38292-163">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="38292-163">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="38292-164">MainPage のコード 背後の更新</span><span class="sxs-lookup"><span data-stu-id="38292-164">Update MainPage code-behind</span></span>

<span data-ttu-id="38292-165">すべてのページが配置されたので **、MainPage.xaml** のコード を更新します。</span><span class="sxs-lookup"><span data-stu-id="38292-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="38292-166">ソリューション **エクスプローラーで MainPage.xaml** を **展開** し、MainPage.xaml.csファイルを開き、そのコンテンツ全体を次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38292-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="38292-167">すべての変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="38292-167">Save all of your changes.</span></span> <span data-ttu-id="38292-168">実行するプロジェクト (Android、iOS、または UWP) を右クリックし、[起動プロジェクトとして設定] **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="38292-168">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="38292-169">F5 **キーを押** するか、[デバッグ] **を選択>でデバッグ** を開始Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="38292-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![アプリケーションの Android、iOS、UWP のバージョンのスクリーンショット](./images/welcome-page.png)
