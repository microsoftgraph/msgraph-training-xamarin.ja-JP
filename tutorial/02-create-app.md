<!-- markdownlint-disable MD002 MD041 -->

1. Visual Studio を開き、**[新しいプロジェクトの作成]** を選択します。

1. [**新しいプロジェクトの作成**] ダイアログで、[**モバイルアプリ (Xamarin)**] を選択し、[**次へ**] を選択します。

    ![Visual Studio 2019 [新しいプロジェクトの作成] ダイアログ](images/new-project-dialog.png)

1. [**新しいプロジェクトの構成**] ダイアログで、 `GraphTutorial` **プロジェクト名**と**ソリューション名**を入力し、[**作成**] を選択します。

    > [!IMPORTANT]
    > これらのラボ手順で指定した Visual Studio プロジェクトに対して、まったく同じ名前を入力してください。 Visual Studio プロジェクトの名前が、コードでの名前空間の一部になります。 この手順のコードは、この手順で指定した Visual Studio プロジェクト名に一致する名前空間に応じて異なります。 異なるプロジェクト名を使用する場合には、Visual Studio プロジェクト作成時に入力したプロジェクト名に一致するようにすべての名前空間を調整しないと、コードがコンパイルされません。

    ![Visual Studio 2019 [新しいプロジェクトの構成] ダイアログ](images/configure-new-project-dialog.png)

1. [**クロスプラットフォームアプリの新規作成**] ダイアログで、**空**のテンプレートを選択し、**プラットフォーム**の下で構築するプラットフォームを選択します。 [ **OK]** を選択してソリューションを作成します。

    ![Visual Studio 2019 の [クロスプラットフォームアプリの新規作成] ダイアログ](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>パッケージをインストールする

に進む前に、後で使用する追加の NuGet パッケージをインストールします。

- Azure AD 認証とトークン管理を処理するための[クライアント](https://www.nuget.org/packages/Microsoft.Identity.Client/)。
- Microsoft Graph に電話をかけるための[グラフ](https://www.nuget.org/packages/Microsoft.Graph/)です。

1. **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]** を選択します。

1. パッケージ マネージャー コンソールで、次のコマンドを入力します。

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.0.1 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>アプリを設計する

最初に、認証`App`の状態とサインインしているユーザーを追跡するための変数を追加するクラスを更新します。

1. **ソリューションエクスプローラー**で、 **graphtutorial**プロジェクトを展開**し、app.xaml ファイルを**展開します。 **App.xaml.cs**ファイルを開き、次`using`のステートメントをファイルの先頭に追加します。

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. クラス宣言`INotifyPropertyChanged`にインターフェイスを追加します。

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. 次のプロパティを`App`クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. 次の関数を`App`クラスに追加します。 、、および`GetUserInfo`関数は、現時点では単なるプレースホルダーです。 `SignIn` `SignOut`

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

1. 関数`GetUserPhoto`は、現在の既定の写真を返します。 独自のファイルを指定するか、または[GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)からサンプルで使用されているファイルをダウンロードすることができます。 独自のファイルを使用する場合は、そのファイルの名前を**no-profile-pic**に変更します。

1. ファイルを **/GraphTutorial/GraphTutorial**ディレクトリにコピーします。

1. **ソリューションエクスプローラー**でファイルを右クリックし、[**プロパティ**] を選択します。 [**プロパティ**] ウィンドウで、[**ビルドアクション**] の値を [**埋め込ま**れたリソース] に変更します。

    ![PNG ファイルの [プロパティ] ウィンドウのスクリーンショット](./images/png-file-properties.png)

### <a name="app-navigation"></a>アプリナビゲーション

このセクションでは、アプリケーションのメインページを[マスター/詳細ページ](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)に変更します。 これにより、アプリの表示を切り替えるナビゲーションメニューが提供されます。

1. **Graphtutorial**プロジェクトで**MainPage**ファイルを開き、その内容を次のように置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a>メニューを実装する

1. **Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいフォルダー**] の順に選択します。 フォルダーに `Models` という名前を指定します。

1. [**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`NavMenuItem`の名前を指定して、[**追加**] を選択します。

1. **NavMenuItem.cs**ファイルを開き、その内容を次のように置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. **Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`MenuPage`、ページに名前を指定します。 [**追加**] を選択します。

1. **MenuPage**ファイルを開き、その内容を次のように置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. **ソリューションエクスプローラー**で**MenuPage**を展開し、 **MenuPage.xaml.cs**ファイルを開きます。 その内容を次のように置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio は**MenuPage.xaml.cs**でエラーを報告します。 これらのエラーは、後の手順で解決されます。

#### <a name="implement-the-welcome-page"></a>ウェルカムページを実装する

1. **Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`WelcomePage`、ページに名前を指定します。 [**追加**] を選択します。 **WelcomePage**ファイルを開き、その内容を次のように置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. **ソリューションエクスプローラー**で**WelcomePage**を展開し、 **WelcomePage.xaml.cs**ファイルを開きます。 次の関数を `WelcomePage` クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-page"></a>[予定表の追加] ページ

ここで、予定表ページを追加します。 これは、現時点でのプレースホルダーにすぎません。

1. **Graphtutorial**プロジェクトを右クリックし、[**追加**]、[**新しいアイテム**] の順に選択します。[**コンテンツ] ページ**を選択し`CalendarPage`、ページに名前を指定します。 [**追加**] を選択します。

#### <a name="update-mainpage-code-behind"></a>MainPage 分離コードを更新する

すべてのページが適切に配置されたので、 **MainPage**の分離コードを更新します。

1. **ソリューションエクスプローラー**で**MainPage**を展開し、 **MainPage.xaml.cs**ファイルを開き、その内容全体を次のように置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. すべての変更を保存します。 実行するプロジェクト (Android、iOS、または UWP) を右クリックして、[**スタートアッププロジェクトに設定**] を選択します。 **F5**キーを押すか、[デバッグ] > 選択して、Visual Studio で**デバッグを開始**します。

    ![Android、iOS、および UWP バージョンのアプリケーションのスクリーンショット](./images/welcome-page.png)
