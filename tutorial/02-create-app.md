<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="71652-101">Visual Studio を開き、[**新しいプロジェクトの作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-101">Open Visual Studio, and select **Create a new project**.</span></span> <span data-ttu-id="71652-102">[**新しいプロジェクトの作成**] ダイアログで、[**モバイルアプリ (Xamarin)**] を選択し、[**次へ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

![Visual Studio 2019 [新しいプロジェクトの作成] ダイアログ](images/new-project-dialog.png)

<span data-ttu-id="71652-104">[**新しいプロジェクトの構成**] ダイアログで、 `GraphTutorial` **プロジェクト名**と**ソリューション名**を入力し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71652-105">これらのラボ手順で指定した Visual Studio プロジェクトに対して、まったく同じ名前を入力してください。</span><span class="sxs-lookup"><span data-stu-id="71652-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="71652-106">Visual Studio プロジェクト名は、コード内の名前空間の一部になります。</span><span class="sxs-lookup"><span data-stu-id="71652-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="71652-107">これらの手順内のコードは、この手順で指定した Visual Studio プロジェクト名に一致する名前空間によって決まります。</span><span class="sxs-lookup"><span data-stu-id="71652-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="71652-108">別のプロジェクト名を使用すると、プロジェクトの作成時に入力した Visual Studio プロジェクト名と一致するようにすべての名前空間を調整しない限り、コードはコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="71652-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

![Visual Studio 2019 [新しいプロジェクトの構成] ダイアログ](images/configure-new-project-dialog.png)

<span data-ttu-id="71652-110">[**クロスプラットフォームアプリの新規作成**] ダイアログで、**空**のテンプレートを選択し、**プラットフォーム**の下で構築するプラットフォームを選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="71652-111">[ **OK]** を選択してソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="71652-111">Select **OK** to create the solution.</span></span>

![Visual Studio 2019 の [クロスプラットフォームアプリの新規作成] ダイアログ](images/new-cross-platform-app-dialog.png)

<span data-ttu-id="71652-113">に進む前に、後で使用する追加の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="71652-113">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="71652-114">Azure AD 認証とトークン管理を処理するための[クライアント](https://www.nuget.org/packages/Microsoft.Identity.Client/)。</span><span class="sxs-lookup"><span data-stu-id="71652-114">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="71652-115">Microsoft Graph に電話をかけるための[グラフ](https://www.nuget.org/packages/Microsoft.Graph/)です。</span><span class="sxs-lookup"><span data-stu-id="71652-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="71652-116">[**ツール > NuGet パッケージマネージャー > パッケージマネージャーコンソール**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-116">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="71652-117">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="71652-117">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Install-Package Microsoft.Identity.Client -Version 4.7.1 -Project GraphTutorial
Install-Package Microsoft.Identity.Client -Version 4.7.1 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 4.7.1 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.20.0 -Project GraphTutorial
```

## <a name="design-the-app"></a><span data-ttu-id="71652-118">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="71652-118">Design the app</span></span>

<span data-ttu-id="71652-119">最初に、認証`App`の状態とサインインしているユーザーを追跡するための変数を追加するクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="71652-119">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span> <span data-ttu-id="71652-120">**ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開**し、app.xaml ファイルを**展開します。</span><span class="sxs-lookup"><span data-stu-id="71652-120">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="71652-121">**App.xaml.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-121">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

<span data-ttu-id="71652-122">次に、クラス`INotifyPropertyChanged`宣言にインターフェイスを追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-122">Next, add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

```cs
public partial class App : Application, INotifyPropertyChanged
```

<span data-ttu-id="71652-123">ここで、次のプロパティを`App`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-123">Now add the following properties to the `App` class.</span></span>

```cs
// Is a user signed in?
private bool isSignedIn;
public bool IsSignedIn
{
    get { return isSignedIn; }
    set
    {
        isSignedIn = value;
        OnPropertyChanged("IsSignedIn");
        OnPropertyChanged("IsSignedOut");
    }
}

public bool IsSignedOut { get { return !isSignedIn; } }

// The user's display name
private string userName;
public string UserName
{
    get { return userName; }
    set
    {
        userName = value;
        OnPropertyChanged("UserName");
    }
}

// The user's email address
private string userEmail;
public string UserEmail
{
    get { return userEmail; }
    set
    {
        userEmail = value;
        OnPropertyChanged("UserEmail");
    }
}

// The user's profile photo
private ImageSource userPhoto;
public ImageSource UserPhoto
{
    get { return userPhoto; }
    set
    {
        userPhoto = value;
        OnPropertyChanged("UserPhoto");
    }
}
```

<span data-ttu-id="71652-124">ここで、次の関数を`App`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-124">Now add the following functions to the `App` class.</span></span> <span data-ttu-id="71652-125">、、および`GetUserInfo`関数は、現時点では単なるプレースホルダーです。 `SignIn` `SignOut`</span><span class="sxs-lookup"><span data-stu-id="71652-125">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

```cs
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

<span data-ttu-id="71652-126">関数`GetUserPhoto`は、現在の既定の写真を返します。</span><span class="sxs-lookup"><span data-stu-id="71652-126">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="71652-127">ここでファイルを指定するか、または[GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)からサンプルで使用されているファイルをダウンロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="71652-127">You can either supply your own file here, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="71652-128">ファイルを`./GraphTutorial/GraphTutorial`ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="71652-128">Copy the file to the `./GraphTutorial/GraphTutorial` directory.</span></span> <span data-ttu-id="71652-129">**ソリューションエクスプローラー**で**graphtutorial**プロジェクトを右クリックして、[**追加**]、[**既存の項目.**..] の順に選択します。ファイルを`no-profile-pic.png`選択し、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-129">Right-click the **GraphTutorial** project in **Solution Explorer** and select **Add**, then **Existing Item...**. Select the `no-profile-pic.png` file and select **Add**.</span></span> <span data-ttu-id="71652-130">次に、**ソリューションエクスプローラー**でファイルを右クリックし、[**プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-130">Now right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="71652-131">[**プロパティ**] ウィンドウで、[**ビルドアクション**] の値を [**埋め込ま**れたリソース] に変更します。</span><span class="sxs-lookup"><span data-stu-id="71652-131">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

![PNG ファイルの [プロパティ] ウィンドウのスクリーンショット](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="71652-133">アプリナビゲーション</span><span class="sxs-lookup"><span data-stu-id="71652-133">App navigation</span></span>

<span data-ttu-id="71652-134">次に、アプリケーションのメインページを[マスター/詳細ページ](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)に変更します。</span><span class="sxs-lookup"><span data-stu-id="71652-134">Next, change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="71652-135">これにより、アプリの表示を切り替えるナビゲーションメニューが提供されます。</span><span class="sxs-lookup"><span data-stu-id="71652-135">This will provide a navigation menu to switch between view in the app.</span></span>

<span data-ttu-id="71652-136">**Graphtutorial**プロジェクトで**MainPage**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71652-136">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial"
             x:Class="GraphTutorial.MainPage">

    <MasterDetailPage.Master>
        <local:MenuPage/>
    </MasterDetailPage.Master>

    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:WelcomePage/>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>

</MasterDetailPage>
```

#### <a name="implement-the-menu"></a><span data-ttu-id="71652-137">メニューを実装する</span><span class="sxs-lookup"><span data-stu-id="71652-137">Implement the menu</span></span>

<span data-ttu-id="71652-138">最初に、メニュー項目を表すためのモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="71652-138">Start by creating a model to represent the menu items.</span></span> <span data-ttu-id="71652-139">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいフォルダー**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-139">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="71652-140">フォルダー `Models`に名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="71652-140">Name the folder `Models`.</span></span>

<span data-ttu-id="71652-141">[**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`NavMenuItem`の名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-141">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span> <span data-ttu-id="71652-142">**NavMenuItem.cs**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71652-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public enum MenuItemType
    {
        Welcome,
        Calendar
    }

    public class NavMenuItem
    {
        public MenuItemType Id { get; set; }

        public string Title { get; set; }
    }
}
```

<span data-ttu-id="71652-143">次に、メニューページを追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-143">Now add the menu page.</span></span> <span data-ttu-id="71652-144">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`MenuPage`、ページに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="71652-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="71652-145">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-145">Select **Add**.</span></span> <span data-ttu-id="71652-146">**MenuPage**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71652-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.UseSafeArea="true"
             Title="Menu"
             x:Class="GraphTutorial.MenuPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout VerticalOptions="Start" HorizontalOptions="Center">
            <StackLayout x:Name="UserArea" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Sign in to get started"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Medium"
                       Margin="10,20,10,20" />
                <Button Text="Sign in"
                        Clicked="OnSignIn"
                        HorizontalOptions="Center" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Image Source="{Binding Path=UserPhoto, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       Margin="0,20,0,10" />
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Small" />
                <Label Text="{Binding Path=UserEmail, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Italic" />
                <Button Text="Sign out"
                        Margin="0,20,0,20"
                        Clicked="OnSignOut"
                        HorizontalOptions="Center" />
                <ListView x:Name="ListViewMenu"
                          HasUnevenRows="True"
                          HorizontalOptions="Start">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <ViewCell>
                                <Grid Padding="10">
                                    <Label Text="{Binding Title}" FontSize="20"/>
                                </Grid>
                            </ViewCell>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="71652-147">次に、**ソリューションエクスプローラー**で**MenuPage**を展開し、 **MenuPage.xaml.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="71652-147">Now, expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="71652-148">その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71652-148">Replace its contents with the following.</span></span>

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

using GraphTutorial.Models;

namespace GraphTutorial
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MenuPage : ContentPage
    {
        MainPage RootPage => Application.Current.MainPage as MainPage;
        List<NavMenuItem> menuItems;

        public MenuPage ()
        {
            InitializeComponent ();

            // Add items to the menu
            menuItems = new List<NavMenuItem>
            {
                new NavMenuItem {Id = MenuItemType.Welcome, Title="Home" },
                new NavMenuItem {Id = MenuItemType.Calendar, Title="Calendar" }
            };
            ListViewMenu.ItemsSource = menuItems;

            // Initialize the selected item
            ListViewMenu.SelectedItem = menuItems[0];

            // Handle the ItemSelected event to navigate to the
            // selected page
            ListViewMenu.ItemSelected += async (sender, e) =>
            {
                if (e.SelectedItem == null)
                    return;

                var id = (int)((NavMenuItem)e.SelectedItem).Id;
                await RootPage.NavigateFromMenu(id);
            };
        }

        private async void OnSignOut(object sender, EventArgs e)
        {
            var signout = await DisplayAlert("Sign out?", "Do you want to sign out?", "Yes", "No");
            if (signout)
            {
                await (Application.Current as App).SignOut();
            }
        }

        private async void OnSignIn(object sender, EventArgs e)
        {
            await (Application.Current as App).SignIn();
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="71652-149">Visual Studio は**MenuPage.xaml.cs**でエラーを報告します。</span><span class="sxs-lookup"><span data-stu-id="71652-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="71652-150">これらのエラーは、後の手順で解決されます。</span><span class="sxs-lookup"><span data-stu-id="71652-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="71652-151">ウェルカムページを実装する</span><span class="sxs-lookup"><span data-stu-id="71652-151">Implement the welcome page</span></span>

<span data-ttu-id="71652-152">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`WelcomePage`、ページに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="71652-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="71652-153">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-153">Select **Add**.</span></span> <span data-ttu-id="71652-154">**WelcomePage**ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71652-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Home"
             x:Class="GraphTutorial.WelcomePage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Graph Xamarin Tutorial App"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Large"
                       Margin="10,20,10,20" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Please sign in to get started"
                       HorizontalOptions="Center"
                       FontSize="Medium"
                       Margin="10,0,10,20"/>
                <Button Text="Sign in"
                        HorizontalOptions="Center"
                        Clicked="OnSignIn" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}, StringFormat='Welcome \{0\}!'}"
                       HorizontalOptions="Center"
                       FontSize="Medium"/>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="71652-155">次に、**ソリューションエクスプローラー**で**WelcomePage**を展開し、 **WelcomePage.xaml.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="71652-155">Now, expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="71652-156">次の関数を `WelcomePage` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-156">Add the following function to the `WelcomePage` class.</span></span>

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a><span data-ttu-id="71652-157">[予定表の追加] ページ</span><span class="sxs-lookup"><span data-stu-id="71652-157">Add calendar page</span></span>

<span data-ttu-id="71652-158">ここで、予定表ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="71652-158">Now add a calendar page.</span></span> <span data-ttu-id="71652-159">これは、現時点でのプレースホルダーにすぎません。</span><span class="sxs-lookup"><span data-stu-id="71652-159">This will just be a placeholder for now.</span></span> <span data-ttu-id="71652-160">**Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`CalendarPage`、ページに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="71652-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="71652-161">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-161">Select **Add**.</span></span>

<span data-ttu-id="71652-162">追加したページを現在の状態のままにします。</span><span class="sxs-lookup"><span data-stu-id="71652-162">Leave the added page as-is for now.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="71652-163">MainPage 分離コードを更新する</span><span class="sxs-lookup"><span data-stu-id="71652-163">Update MainPage code-behind</span></span>

<span data-ttu-id="71652-164">すべてのページが適切に配置されたので、 **MainPage**の分離コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="71652-164">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span> <span data-ttu-id="71652-165">**ソリューションエクスプローラー**で**MainPage**を展開し、 **MainPage.xaml.cs**ファイルを開き、その内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71652-165">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;
using GraphTutorial.Models;

namespace GraphTutorial
{
    public partial class MainPage : MasterDetailPage
    {
        Dictionary<int, NavigationPage> MenuPages = new Dictionary<int, NavigationPage>();

        public MainPage()
        {
            InitializeComponent();

            MasterBehavior = MasterBehavior.Popover;

            // Load the welcome page at start
            MenuPages.Add((int)MenuItemType.Welcome, (NavigationPage)Detail);
        }

        // Navigate to the selected page
        public async Task NavigateFromMenu(int id)
        {
            if (!MenuPages.ContainsKey(id))
            {
                switch (id)
                {
                    case (int)MenuItemType.Welcome:
                        MenuPages.Add(id, new NavigationPage(new WelcomePage()));
                        break;
                    case (int)MenuItemType.Calendar:
                        MenuPages.Add(id, new NavigationPage(new CalendarPage()));
                        break;
                }
            }

            var newPage = MenuPages[id];

            if (newPage != null && Detail != newPage)
            {
                Detail = newPage;

                if (Device.RuntimePlatform == Device.Android)
                    await Task.Delay(100);

                IsPresented = false;
            }
        }
    }
}
```

<span data-ttu-id="71652-166">すべての変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="71652-166">Save all of your changes.</span></span> <span data-ttu-id="71652-167">実行するプロジェクト (Android、iOS、または UWP) を右クリックして、[**スタートアッププロジェクトに設定**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="71652-167">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="71652-168">**F5**キーを押すか、[デバッグ] > 選択して、Visual Studio で**デバッグを開始**します。</span><span class="sxs-lookup"><span data-stu-id="71652-168">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

![Android、iOS、および UWP バージョンのアプリケーションのスクリーンショット](./images/welcome-page.png)
