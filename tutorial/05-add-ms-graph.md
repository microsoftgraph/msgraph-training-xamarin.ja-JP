<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e3a67-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="e3a67-102">このアプリケーションでは、microsoft [Graph クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して microsoft graph を呼び出すことにします。</span><span class="sxs-lookup"><span data-stu-id="e3a67-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e3a67-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="e3a67-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e3a67-104">**Graphtutorial**を開いて、その内容を次のように置き換えます。 **CalendarPage.xaml**</span><span class="sxs-lookup"><span data-stu-id="e3a67-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="e3a67-105">**CalendarPage.xaml.cs**を開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="e3a67-106">次の関数を`CalendarPage`クラスに追加して、ユーザーのイベントを取得し、JSON 応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-106">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

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

    <span data-ttu-id="e3a67-107">のコードに`OnAppearing`ついて検討します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-107">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="e3a67-108">呼び出される URL は `/v1.0/me/events` です。</span><span class="sxs-lookup"><span data-stu-id="e3a67-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="e3a67-109">`Select` 関数は、各イベントに返されるフィールドを、ビューで実際に使用されるフィールドだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-109">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="e3a67-110">`OrderBy` 関数は、作成された日時で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-110">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="e3a67-111">アプリを実行し、サインインして、メニューの [**予定表**] ナビゲーション項目をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3a67-111">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="e3a67-112">ユーザーの予定表にあるイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-112">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e3a67-113">結果の表示</span><span class="sxs-lookup"><span data-stu-id="e3a67-113">Display the results</span></span>

<span data-ttu-id="e3a67-114">これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="e3a67-115">最初に、[バインド値コンバータ](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)を作成して、Microsoft Graph によって返される[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)値をユーザーが想定する日付と時刻の形式に変換します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-115">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="e3a67-116">**Graphtutorial**プロジェクトの [**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`GraphDateTimeTimeZoneConverter`の名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-116">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="e3a67-117">ファイルの内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-117">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="e3a67-118">**Calendarpage .xaml**の内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-118">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    <span data-ttu-id="e3a67-119">これは、 `Editor`をに`ListView`置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-119">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="e3a67-120">各`DataTemplate`アイテムのレンダリングに使用される`GraphDateTimeTimeZoneConverter`は、を`Start`使用`End`して、イベントのおよびプロパティを文字列に変換します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-120">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="e3a67-121">**CalendarPage.xaml.cs**を開き、 `OnAppearing`関数から次の行を削除します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-121">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="e3a67-122">その場所に、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e3a67-122">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="e3a67-123">アプリを実行し、サインインして、**予定表**のナビゲーションアイテムをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3a67-123">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="e3a67-124">**開始**および**終了**の値が書式設定されたイベントの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3a67-124">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![イベント表のスクリーンショット](./images/calendar-page.png)
