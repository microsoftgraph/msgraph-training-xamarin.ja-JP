<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。

1. 新しいイベント ビューの新しいページを追加します。 ソリューション エクスプローラーで **GraphTu solutionl** プロジェクトを右クリックし、[新しいアイテムの> **を選択します**。Choose **Blank Page,** enter `NewEventPage.xaml` in the **Name** field, and select **Add**.

1. **NewEventPage.xaml を開き**、その内容を次の内容に置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml" id="NewEventPageXamlSnippet":::

1. ファイル **NewEventPage.xaml.cs** を開き、ファイルの一番上に次 `using` のステートメントを追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="UsingStatementsSnippet":::

1. **INotifyPropertyChange インターフェイスを** **NewEventPage クラスに追加** します。 既存のクラス宣言を次に置き換える。

    ```csharp
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class NewEventPage : ContentPage, INotifyPropertyChanged
    {
        public NewEventPage()
        {
            InitializeComponent();
            BindingContext = this;
        }
    }
    ```

1. NewEventPage クラスに次 **のプロパティを追加** します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. 次のコードを追加してイベントを作成します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. 変更内容を保存し、アプリケーションを実行します。 サインインし、[新しい **イベント]** メニュー項目を選択してフォームに入力し、[作成] を選択してユーザーの予定表にイベントを追加します。

    ![新しいイベント ページのスクリーンショット](images/new-event-page.png)
