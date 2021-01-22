<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。 このアプリケーションでは [、.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 用 Microsoft Graph クライアント ライブラリを使用して Microsoft Graph を呼び出します。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. **GraphTuxal** プロジェクトで **CalendarPage.xaml** を開き、その内容を次の内容に置き換えます。

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

1. ファイル **CalendarPage.xaml.cs** を開き、ファイルの上部 `using` に次のステートメントを追加します。

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

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

        // Get the events
        var events = await App.GraphClient.Me.CalendarView.Request(queryOptions)
            .Header("Prefer", $"outlook.timezone=\"{App.UserTimeZone.DisplayName}\"")
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
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    コードの実行を `OnAppearing` 検討します。

    - 呼び出される URL は `/v1.0/me/calendarview` です。
        - パラメーター `startDateTime` は `endDateTime` 、カレンダー ビューの開始と終了を定義します。
        - ヘッダーにより、ユーザーのタイム ゾーンでイベントと `Prefer: outlook.timezone` `start` `end` イベントが返されます。
        - この `Select` 関数は、各イベントで返されるフィールドを、アプリが実際に使用するフィールドに制限します。
        - この `OrderBy` 関数は、結果を開始日時で並べ替える。
        - この `Top` 関数は、最大で 50 のイベントを要求します。

1. アプリを実行し、サインインして、メニューの **[予定表** ] ナビゲーション 項目をクリックします。 ユーザーの予定表にイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

これで、JSON ダンプを何かに置き換え、結果をユーザー に分け親しみのある方法で表示できます。

まず、Microsoft Graph から返 [される](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) 値をユーザーが期待する日付と時刻の形式に変換するバインド値コンバーターを作成します。

1. **GraphTu読** み込みプロジェクト **の Models** フォルダーを右クリックし、[追加] を選択し、[クラス **...] を選択します**。クラスに名前を付 `GraphDateTimeTimeZoneConverter` け、[追加] を **選択します**。

1. ファイルの内容全体を次の内容に置き換えてください。

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. **CalendarPage.xaml の内容全体を次の** 内容に置き換えます。

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    This replaces the `Editor` with a `ListView` . 各 `DataTemplate` アイテムのレンダリングに使用されるプロパティは、イベントの `GraphDateTimeTimeZoneConverter` プロパティを `Start` `End` 文字列に変換するために使用されます。

1. この **CalendarPage.xaml.cs** 開き、関数から次の行を削除 `OnAppearing` します。

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. その場所に、次のコードを追加します。

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. アプリを実行し、サインインして、[予定表] ナビゲーション **項目を** クリックします。 Start 値と End 値が書式設定 **されたイベントの** 一 **覧が** 表示されます。

    ![イベント表のスクリーンショット](./images/calendar-page.png)
