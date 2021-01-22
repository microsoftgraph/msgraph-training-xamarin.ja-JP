<!-- markdownlint-disable MD002 MD041 -->

1. Visual Studio を開き、**[新しいプロジェクトの作成]** を選択します。

1. In the **Create a new project** dialog, choose Mobile App **(Xamarin.Forms),** then select **Next**.

    ![Visual Studio 2019 で新しいプロジェクトの作成ダイアログを作成する](images/new-project-dialog.png)

1. [新しい **プロジェクトの構成]** ダイアログで、プロジェクト名とソリューション名を入力し、[作成] `GraphTutorial` を選択 **します**。  

    > [!IMPORTANT]
    > このラボの手順で指定されているプロジェクトの名前とVisual Studio名前を入力してください。 Visual Studio プロジェクトの名前が、コードでの名前空間の一部になります。 この手順のコードは、この手順で指定した Visual Studio プロジェクト名に一致する名前空間に応じて異なります。 異なるプロジェクト名を使用する場合には、Visual Studio プロジェクト作成時に入力したプロジェクト名に一致するようにすべての名前空間を調整しないと、コードがコンパイルされません。

    ![Visual Studio 2019 の [新しいプロジェクトの構成] ダイアログ](images/configure-new-project-dialog.png)

1. [**新しいクロス プラットフォーム アプリ**]ダイアログで、空のテンプレートを選択し、[プラットフォーム] でビルドするプラットフォーム **を選択します**。 **[OK] を** 選択してソリューションを作成します。

    ![Visual Studio 2019 の新しいクロス プラットフォーム アプリ ダイアログ](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>パッケージをインストールする

次に進む前に、後で使用する追加の NuGet パッケージをインストールします。

- [Azure 認証とトークン管理を処理AD Microsoft.Identity.Client。](https://www.nuget.org/packages/Microsoft.Identity.Client/)
- Microsoft Graph を呼び出す[Microsoft.Graph。](https://www.nuget.org/packages/Microsoft.Graph/)
- [タイム ゾーンクロスプラットフォームを処理する TimeZoneConverter。](https://www.nuget.org/packages/TimeZoneConverter/)

1. **[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]** を選択します。

1. パッケージ マネージャー コンソールで、次のコマンドを入力します。

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>アプリを設計する

まず、クラスを更新して、認証状態とサインインしているユーザーを追跡する変数 `App` を追加します。

1. ソリューション **エクスプローラーで GraphTu** **solutionl プロジェクトを展開** し **、App.xaml ファイルを展開** します。 新しい **App.xaml.cs** を開き、ファイルの一番上に次 `using` のステートメントを追加します。

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. クラス宣言 `INotifyPropertyChanged` にインターフェイスを追加します。

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. 次のプロパティをクラスに追加 `App` します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. 次の関数をクラスに追加 `App` します。 The `SignIn` `SignOut` , , and functions are `GetUserInfo` just placeholders for now.

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

1. この `GetUserPhoto` 関数は、現在のところ既定の写真を返します。 独自のファイルを指定するか、サンプルで使用されているファイルを [GitHub からダウンロードできます](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png)。 独自のファイルを使用する場合は、ファイルの名前 **を変更して** no-profile-pic.png。

1. ファイルを **./GraphTu読み込み/GraphTu読み込みディレクトリにコピー** します。

1. ソリューション エクスプローラーでファイルを右クリックし、[ **プロパティ** ] を選択 **します**。 [プロパティ **] ウィンドウ** で、[ビルド アクション] の **値を [埋め込み** リソース **] に変更します**。

    ![PNG ファイルの [プロパティ] ウィンドウのスクリーンショット](./images/png-file-properties.png)

### <a name="app-navigation"></a>アプリナビゲーション

このセクションでは、アプリケーションのメイン ページをマスター/詳細ページ [に変更します](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page)。 これにより、アプリのビューを切り替えるナビゲーション メニューが提供されます。

1. **GraphTuxal** **プロジェクトの MainPage.xaml** ファイルを開き、その内容を次の内容に置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a>メニューを実装する

1. **GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいフォルダー]**の順に選択します**。 フォルダーに `Models` という名前を指定します。

1. Models フォルダーを右 **クリックし**、[追加]、次に [**クラス****...] の順に選択します**。クラスに名前を付 `NavMenuItem` け、[追加] を **選択します**。

1. 新しいファイル **NavMenuItem.cs** 開き、その内容を次の内容に置き換えてください。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. **GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `MenuPage` . **[追加]** を選択します。

1. **MenuPage.xaml ファイルを開** き、その内容を次の内容に置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. ソリューション **エクスプローラーで MenuPage.xaml** **を展開し** 、 **ファイルMenuPage.xaml.csします** 。 内容を次の内容に置き換えてください。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studioでエラーが **報告MenuPage.xaml.cs。** これらのエラーは、後の手順で解決されます。

#### <a name="implement-the-welcome-page"></a>ウェルカム ページを実装する

1. **GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `WelcomePage` . **[追加]** を選択します。 **WelcomePage.xaml ファイルを開** き、その内容を次の内容に置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. ソリューション **エクスプローラーで WelcomePage.xaml** **を展開し** 、 **ファイルWelcomePage.xaml.csします** 。 次の関数を `WelcomePage` クラスに追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a>予定表と新しいイベント ページを追加する

予定表ページと新しいイベント ページを追加します。 これらは、今のところプレースホルダーにすすむだけです。

1. **GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `CalendarPage` . **[追加]** を選択します。

1. **GraphTu読み込みプロジェクトを右クリックし、[追加**]、次に **[新** しいアイテム **...] の順に選択します**。Choose **Content Page** and name the page `NewEventPage` . **[追加]** を選択します。

#### <a name="update-mainpage-code-behind"></a>MainPage のコード 背後の更新

すべてのページが配置されたので **、MainPage.xaml** のコード を更新します。

1. ソリューション **エクスプローラーで MainPage.xaml** を **展開** し、MainPage.xaml.csファイルを開き、そのコンテンツ全体を次の内容に置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. すべての変更を保存します。 実行するプロジェクト (Android、iOS、または UWP) を右クリックし、[起動プロジェクトとして設定] **を選択します**。 F5 **キーを押** するか、[デバッグ] **を選択>でデバッグ** を開始Visual Studio。

    ![アプリケーションの Android、iOS、UWP のバージョンのスクリーンショット](./images/welcome-page.png)
