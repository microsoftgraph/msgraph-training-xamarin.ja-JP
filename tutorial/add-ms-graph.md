<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、microsoft [Graph クライアントライブラリ](https://github.com/microsoftgraph/msgraph-sdk-dotnet)を使用して microsoft graph を呼び出すことにします。

## <a name="get-calendar-events-from-outlook"></a>Outlook から予定表のイベントを取得する

最初に、 **Graphtutorial .xaml**ファイルを**graphtutorial**プロジェクトに更新します。 ファイルを開き、その内容を次のように置き換えます。

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

を`CalendarPage.xaml.cs`開き、次`using`のステートメントをファイルの先頭に追加します。

```cs
using Newtonsoft.Json;
using Microsoft.Graph;
using System.ComponentModel;
using System.Collections.ObjectModel;
```

その後、次のコードを追加して、ユーザーのイベントを取得し、JSON 応答を表示します。

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

のコードに`OnAppearing`ついて検討します。

- 呼び出し先の URL は`/v1.0/me/events`になります。
- 関数`Select`は、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。
- 関数`OrderBy`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

これで、アプリを実行し、サインインして、メニューの [**予定表**] ナビゲーション項目をクリックできるようになります。 ユーザーの予定表にあるイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果を表示する

これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。 最初に、[バインド値コンバータ](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters)を作成して、Microsoft Graph によって返される[dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0)値をユーザーが想定する日付と時刻の形式に変換します。 **Graphtutorial**プロジェクトの [**モデル**] フォルダーを右クリックし、[**追加**]、[**クラス.**..] の順に選択します。クラス`GraphDateTimeTimeZoneConverter`の名前を指定して、[**追加**] を選択します。 ファイルの内容全体を次のように置き換えます。

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

次に、の`CalendarPage.xaml`内容全体を次のように置き換えます。

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

これは、 `Editor`をに`ListView`置き換えます。 各`DataTemplate`アイテムのレンダリングに使用される`GraphDateTimeTimeZoneConverter`は、を`Start`使用`End`して、イベントのおよびプロパティを文字列に変換します。 これで`CalendarPage.xaml.cs` 、次の行を`OnAppearing`関数から開き、削除します。

```cs
// Temporary
JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
```

その場所に、次のコードを追加します。

```cs
// Add the events to the list view
CalendarList.ItemsSource = events.CurrentPage.ToList();
```

アプリを実行し、サインインして、**予定表**のナビゲーションアイテムをクリックします。 **開始**および**終了**の値が書式設定されたイベントの一覧が表示されます。

![イベントの表のスクリーンショット](./images/calendar-page.png)
