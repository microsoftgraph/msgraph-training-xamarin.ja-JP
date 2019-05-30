<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f1b5b-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="f1b5b-102">このアプリケーションでは、microsoft [Graph クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して microsoft graph を呼び出すことにします。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="f1b5b-103">Outlook から予定表のイベントを取得する</span><span class="sxs-lookup"><span data-stu-id="f1b5b-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="f1b5b-104">最初に、 **Graphtutorial .xaml**ファイルを**graphtutorial**プロジェクトに更新します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-104">Start by updating the **CalendarPage.xaml** file in the **GraphTutorial** project.</span></span> <span data-ttu-id="f1b5b-105">ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-105">Open the file and replace its contents with the following.</span></span>

```xml
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

<span data-ttu-id="f1b5b-106">を`CalendarPage.xaml.cs`開き、次`using`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-106">Open `CalendarPage.xaml.cs` and add the following `using` statements at the top of the file.</span></span>

```cs
using Newtonsoft.Json;
using Microsoft.Graph;
using System.ComponentModel;
using System.Collections.ObjectModel;
```

<span data-ttu-id="f1b5b-107">その後、次のコードを追加して、ユーザーのイベントを取得し、JSON 応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-107">Then add the following code to get the user's events and display the JSON response.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Get the events
    var events = await App.GraphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
}
```

<span data-ttu-id="f1b5b-108">のコードに`OnAppearing`ついて検討します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-108">Consider what the code in `OnAppearing` is doing.</span></span>

- <span data-ttu-id="f1b5b-109">呼び出し先の URL は`/v1.0/me/events`になります。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-109">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="f1b5b-110">関数`Select`は、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-110">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="f1b5b-111">関数`OrderBy`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-111">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="f1b5b-112">これで、アプリを実行し、サインインして、メニューの [**予定表**] ナビゲーション項目をクリックできるようになります。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-112">You can now run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="f1b5b-113">ユーザーの予定表にあるイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-113">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="f1b5b-114">結果を表示する</span><span class="sxs-lookup"><span data-stu-id="f1b5b-114">Display the results</span></span>

<span data-ttu-id="f1b5b-115">これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-115">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="f1b5b-116">最初に、[バインド値コンバータ](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)を作成して、Microsoft Graph によって返される[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)値をユーザーが想定する日付と時刻の形式に変換します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-116">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span> <span data-ttu-id="f1b5b-117">**Graphtutorial**プロジェクトの [**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`GraphDateTimeTimeZoneConverter`の名前を指定して、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-117">Right-click the **Models** folder in the **GraphTutorial** project and choose **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and choose **Add**.</span></span> <span data-ttu-id="f1b5b-118">ファイルの内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-118">Replace the entire contents of the file with the following.</span></span>

```cs
using Microsoft.Graph;
using System;
using System.Globalization;
using Xamarin.Forms;

namespace GraphTutorial.Models
{
    class GraphDateTimeTimeZoneConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            if (value is DateTimeTimeZone date)
            {
                // Resolve the time zone
                var timezone = TimeZoneInfo.FindSystemTimeZoneById(date.TimeZone);
                // Parse method assumes local time, which may not be the case
                var parsedDateAsLocal = DateTimeOffset.Parse(date.DateTime);
                // Determine the offset from UTC time for the specific date
                // Making this call adjusts for DST as appropriate
                var tzOffset = timezone.GetUtcOffset(parsedDateAsLocal.DateTime);
                // Create a new DateTimeOffset with the specific offset from UTC
                var correctedDate = new DateTimeOffset(parsedDateAsLocal.DateTime, tzOffset);
                // Return the local date time string
                return $"{correctedDate.LocalDateTime.ToShortDateString()} {correctedDate.LocalDateTime.ToShortTimeString()}";
            }

            return string.Empty;
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
}
```

<span data-ttu-id="f1b5b-119">次に、の`CalendarPage.xaml`内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-119">Next, replace the entire contents of `CalendarPage.xaml` with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial.Models"
             Title="Calendar"
             x:Class="GraphTutorial.CalendarPage">
    <ContentPage.Resources>
        <local:GraphDateTimeTimeZoneConverter x:Key="DateConverter" />
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout>
            <ListView x:Name="CalendarList"
                      HasUnevenRows="true"
                      Margin="10,10,10,10">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Margin="10,10,10,10">
                                <Label Text="{Binding Path=Subject}"
                                       FontAttributes="Bold"
                                       FontSize="Medium" />
                                <Label Text="{Binding Path=Organizer.EmailAddress.Name}"
                                       FontSize="Small" />
                                <StackLayout Orientation="Horizontal">
                                    <Label Text="{Binding Path=Start, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                    <Label Text="to"
                                           FontSize="Micro" />
                                    <Label Text="{Binding Path=End, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                </StackLayout>
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="f1b5b-120">これは、 `Editor`をに`ListView`置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-120">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="f1b5b-121">各`DataTemplate`アイテムのレンダリングに使用される`GraphDateTimeTimeZoneConverter`は、を`Start`使用`End`して、イベントのおよびプロパティを文字列に変換します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-121">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span> <span data-ttu-id="f1b5b-122">これで`CalendarPage.xaml.cs` 、次の行を`OnAppearing`関数から開き、削除します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-122">Now open `CalendarPage.xaml.cs` and remove the following lines from the `OnAppearing` function.</span></span>

```cs
// Temporary
JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
```

<span data-ttu-id="f1b5b-123">その場所に、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-123">In their place, add the following code.</span></span>

```cs
// Add the events to the list view
CalendarList.ItemsSource = events.CurrentPage.ToList();
```

<span data-ttu-id="f1b5b-124">アプリを実行し、サインインして、**予定表**のナビゲーションアイテムをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-124">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="f1b5b-125">**開始**および**終了**の値が書式設定されたイベントの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1b5b-125">You should see the list of events with the **Start** and **End** values formatted.</span></span>

![イベントの表のスクリーンショット](./images/calendar-page.png)
