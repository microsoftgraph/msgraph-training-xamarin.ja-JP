<!-- markdownlint-disable MD002 MD041 -->

この演習では、アプリケーションに Microsoft Graphを組み込む必要があります。 このアプリケーションでは、Microsoft クライアント ライブラリ for [.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) Graphを使用して Microsoft クライアント ライブラリを呼び出Graph。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. **GraphTutorial** **プロジェクトで CalendarPage.xaml** を開き、その内容を次に置き換えます。

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

1. **CalendarPage.xaml.cs** を開き、ファイルの上部に `using` 次のステートメントを追加します。

    ```csharp
    using Microsoft.Graph;

    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. 次の関数をクラスに追加して、ユーザーのタイム ゾーンで現在の週 `CalendarPage` の開始を取得します。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml.cs" id="GetStartOfWeekSnippet":::

1. 次の関数をクラスに追加して、ユーザーのイベントを取得し `CalendarPage` 、JSON 応答を表示します。

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get start and end of week in user's time zone
        var startOfWeek = GetUtcStartOfWeekInTimeZone(DateTime.Today, App.UserTimeZone);
        var endOfWeek = startOfWeek.AddDays(7);

        var queryOptions = new List<QueryOption>
        {
            new QueryOption("startDateTime", startOfWeek.ToString("o")),
            new QueryOption("endDateTime", endOfWeek.ToString("o"))
        };

        var timeZoneString =
            Xamarin.Forms.Device.RuntimePlatform == Xamarin.Forms.Device.UWP ?
                App.UserTimeZone.StandardName : App.UserTimeZone.DisplayName;

        // Get the events
        var events = await App.GraphClient.Me.CalendarView.Request(queryOptions)
            .Header("Prefer", $"outlook.timezone=\"{timeZoneString}\"")
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("start/DateTime")
            .Top(50)
            .GetAsync();

        // Temporary
        JSONResponse.Text = App.GraphClient.HttpProvider.Serializer.SerializeObject(events.CurrentPage);
    }
    ```

    コードの実行を `OnAppearing` 検討します。

    - 呼び出される URL は `/v1.0/me/calendarview` です。
        - パラメーター `startDateTime` と `endDateTime` パラメーターは、予定表ビューの開始と終了を定義します。
        - ヘッダーを使用すると、ユーザーのタイム ゾーンでイベントと `Prefer: outlook.timezone` `start` `end` イベントが返されます。
        - この `Select` 関数は、各イベントで返されるフィールドを、アプリが実際に使用するフィールドに制限します。
        - この `OrderBy` 関数は、結果を開始日時で並べ替える。
        - この `Top` 関数は、最大で 50 のイベントを要求します。

1. アプリを実行し、サインインし、メニューの **[予定表** ] ナビゲーション アイテムをクリックします。 ユーザーの予定表にイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

これで、JSON ダンプを何かに置き換え、結果をユーザーフレンドリーに表示できます。

まず、バインド値[](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)コンバーターを作成して、Microsoft Graph によって返される[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)値をユーザーが期待する日付と時刻の形式に変換します。

1. **GraphTutorial** **プロジェクトの [モデル**] フォルダーを右クリックし、[追加] **、[Class...] の順に選択します**。 クラスに名前を付 `GraphDateTimeTimeZoneConverter` け、[追加] を **選択します**。

1. ファイルの内容全体を次に置き換えてください。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. **CalendarPage.xaml の内容全体を次に置き** 換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    これにより、 に置 `Editor` き換わる `ListView` . 各 `DataTemplate` アイテムのレンダリングに使用されるプロパティは、イベント `GraphDateTimeTimeZoneConverter` のプロパティを文字列 `Start` `End` に変換するために使用されます。

1. **CalendarPage.xaml.cs を** 開き、関数から次の行を削除 `OnAppearing` します。

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. その場所に、次のコードを追加します。

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. アプリを実行し、サインインし、[予定表] ナビゲーション アイテム **を** クリックします。 Start 値と End 値が書式設定 **されたイベントの****一覧が** 表示されます。

    ![イベント表のスクリーンショット](./images/calendar-page.png)
