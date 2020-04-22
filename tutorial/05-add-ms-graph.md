<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、microsoft [Graph クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して microsoft graph を呼び出すことにします。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. **Graphtutorial**を開いて、その内容を次のように置き換えます。 **CalendarPage.xaml**

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 Title="Calendar"
                 x:Class="GraphTutorial.CalendarPage">
        <ContentPage.Content>
            <StackLayout>
                <Editor x:Name="JSONResponse"
                        IsSpellCheckEnabled="False"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="FillAndExpand"/>
            </StackLayout>
        </ContentPage.Content>
    </ContentPage>
    ```

1. **CalendarPage.xaml.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. 次の関数を`CalendarPage`クラスに追加して、ユーザーのイベントを取得し、JSON 応答を表示します。

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get the events
        var events = await App.GraphClient.Me.Events.Request()
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        // Temporary
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    のコードに`OnAppearing`ついて検討します。

    - 呼び出される URL は `/v1.0/me/events` です。
    - `Select` 関数は、各イベントに返されるフィールドを、ビューで実際に使用されるフィールドだけに制限します。
    - `OrderBy` 関数は、作成された日時で結果を並べ替えます。最新のアイテムが最初に表示されます。

1. アプリを実行し、サインインして、メニューの [**予定表**] ナビゲーション項目をクリックします。 ユーザーの予定表にあるイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。

最初に、[バインド値コンバータ](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)を作成して、Microsoft Graph によって返される[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)値をユーザーが想定する日付と時刻の形式に変換します。

1. **Graphtutorial**プロジェクトの [**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`GraphDateTimeTimeZoneConverter`の名前を指定して、[**追加**] を選択します。

1. ファイルの内容全体を次のように置き換えます。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. **Calendarpage .xaml**の内容全体を次のように置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    これは、 `Editor`をに`ListView`置き換えます。 各`DataTemplate`アイテムのレンダリングに使用される`GraphDateTimeTimeZoneConverter`は、を`Start`使用`End`して、イベントのおよびプロパティを文字列に変換します。

1. **CalendarPage.xaml.cs**を開き、 `OnAppearing`関数から次の行を削除します。

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. その場所に、次のコードを追加します。

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. アプリを実行し、サインインして、**予定表**のナビゲーションアイテムをクリックします。 **開始**および**終了**の値が書式設定されたイベントの一覧が表示されます。

    ![イベント表のスクリーンショット](./images/calendar-page.png)
