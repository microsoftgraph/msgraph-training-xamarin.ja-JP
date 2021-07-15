<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="705ad-101">このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="705ad-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="705ad-102">**NewEventPage.xaml を開** き、その内容を次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="705ad-102">Open **NewEventPage.xaml** and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml":::

1. <span data-ttu-id="705ad-103">**NewEventPage.xaml.cs を** 開き、ファイルの上部に `using` 次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="705ad-103">Open **NewEventPage.xaml.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using Microsoft.Graph;
    ```

1. <span data-ttu-id="705ad-104">**INotifyPropertyChange** インターフェイスを **NewEventPage クラスに追加** します。</span><span class="sxs-lookup"><span data-stu-id="705ad-104">Add the **INotifyPropertyChange** interface to the **NewEventPage** class.</span></span> <span data-ttu-id="705ad-105">既存のクラス宣言を次に置き換える。</span><span class="sxs-lookup"><span data-stu-id="705ad-105">Replace the existing class declaration with the following.</span></span>

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

1. <span data-ttu-id="705ad-106">NewEventPage クラスに次の **プロパティを追加** します。</span><span class="sxs-lookup"><span data-stu-id="705ad-106">Add the following properties to the **NewEventPage** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. <span data-ttu-id="705ad-107">イベントを作成するには、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="705ad-107">Add the following code to create the event.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. <span data-ttu-id="705ad-108">変更内容を保存し、アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="705ad-108">Save your changes and run the app.</span></span> <span data-ttu-id="705ad-109">サインインし、[**新しいイベント**] メニュー項目を選択し、フォームに入力し、[作成] を選択してユーザーの予定表にイベントを追加します。</span><span class="sxs-lookup"><span data-stu-id="705ad-109">Sign in, select the **New event** menu item, fill in the form, and select **Create** to add an event to the user's calendar.</span></span>

    ![新しいイベント ページのスクリーンショット](images/new-event-page.png)
