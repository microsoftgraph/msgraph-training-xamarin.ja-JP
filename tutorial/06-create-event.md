<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。

1. **NewEventPage.xaml を開** き、その内容を次に置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml":::

1. **NewEventPage.xaml.cs を** 開き、ファイルの上部に `using` 次のステートメントを追加します。

    ```csharp
    using System.ComponentModel;
    using Microsoft.Graph;
    ```

1. **INotifyPropertyChange** インターフェイスを **NewEventPage クラスに追加** します。 既存のクラス宣言を次に置き換える。

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

1. NewEventPage クラスに次の **プロパティを追加** します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. イベントを作成するには、次のコードを追加します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. 変更内容を保存し、アプリケーションを実行します。 サインインし、[**新しいイベント**] メニュー項目を選択し、フォームに入力し、[作成] を選択してユーザーの予定表にイベントを追加します。

    ![新しいイベント ページのスクリーンショット](images/new-event-page.png)
