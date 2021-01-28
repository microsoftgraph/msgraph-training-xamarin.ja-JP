<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9fffa-101">この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fffa-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="9fffa-102">このアプリケーションでは [、.NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 用 Microsoft Graph クライアント ライブラリを使用して Microsoft Graph を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="9fffa-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="9fffa-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="9fffa-104">**GraphTuxal** プロジェクトで **CalendarPage.xaml** を開き、その内容を次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="9fffa-105">ファイル **CalendarPage.xaml.cs** を開き、ファイルの上部 `using` に次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="9fffa-106">次の関数をクラスに追加して、ユーザーのタイム ゾーンで現在の週の開始 `CalendarPage` を取得します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-106">Add the following function to the `CalendarPage` class to get the start of the current week in the user's time zone.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="9fffa-107">次の関数をクラスに追加して、ユーザーのイベントを取得し `CalendarPage` 、JSON 応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-107">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

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

    <span data-ttu-id="9fffa-108">コードの実行を `OnAppearing` 検討します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-108">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="9fffa-109">呼び出される URL は `/v1.0/me/calendarview` です。</span><span class="sxs-lookup"><span data-stu-id="9fffa-109">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="9fffa-110">パラメーター `startDateTime` は `endDateTime` 、カレンダー ビューの開始と終了を定義します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-110">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
        - <span data-ttu-id="9fffa-111">ヘッダーにより、ユーザーのタイム ゾーンでイベントと `Prefer: outlook.timezone` `start` `end` イベントが返されます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-111">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
        - <span data-ttu-id="9fffa-112">この `Select` 関数は、各イベントで返されるフィールドを、アプリが実際に使用するフィールドに制限します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
        - <span data-ttu-id="9fffa-113">この `OrderBy` 関数は、結果を開始日時で並べ替える。</span><span class="sxs-lookup"><span data-stu-id="9fffa-113">The `OrderBy` function sorts the results by the start date and time.</span></span>
        - <span data-ttu-id="9fffa-114">この `Top` 関数は、最大で 50 のイベントを要求します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-114">The `Top` function requests at most 50 events.</span></span>

1. <span data-ttu-id="9fffa-115">アプリを実行し、サインインして、メニューの **[予定表** ] ナビゲーション 項目をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9fffa-115">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="9fffa-116">ユーザーの予定表にイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-116">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="9fffa-117">結果の表示</span><span class="sxs-lookup"><span data-stu-id="9fffa-117">Display the results</span></span>

<span data-ttu-id="9fffa-118">これで、JSON ダンプを何かに置き換え、結果をユーザー に分け親しみのある方法で表示できます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-118">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="9fffa-119">まず、Microsoft Graph から返 [される](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) 値をユーザーが期待する日付と時刻の形式に変換するバインド値コンバーターを作成します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-119">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="9fffa-120">**GraphTu読** み込みプロジェクト **の Models** フォルダーを右クリックし、[追加] を選択し、[クラス **...] を選択します**。クラスに名前を付 `GraphDateTimeTimeZoneConverter` け、[追加] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="9fffa-120">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="9fffa-121">ファイルの内容全体を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="9fffa-121">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="9fffa-122">**CalendarPage.xaml の内容全体を次の** 内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-122">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    <span data-ttu-id="9fffa-123">This replaces the `Editor` with a `ListView` .</span><span class="sxs-lookup"><span data-stu-id="9fffa-123">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="9fffa-124">各 `DataTemplate` アイテムのレンダリングに使用されるプロパティは、イベントの `GraphDateTimeTimeZoneConverter` プロパティを `Start` `End` 文字列に変換するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-124">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="9fffa-125">この **CalendarPage.xaml.cs** 開き、関数から次の行を削除 `OnAppearing` します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-125">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="9fffa-126">その場所に、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9fffa-126">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="9fffa-127">アプリを実行し、サインインして、[予定表] ナビゲーション **項目を** クリックします。</span><span class="sxs-lookup"><span data-stu-id="9fffa-127">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="9fffa-128">Start 値と End 値が書式設定 **されたイベントの** 一 **覧が** 表示されます。</span><span class="sxs-lookup"><span data-stu-id="9fffa-128">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![イベント表のスクリーンショット](./images/calendar-page.png)
