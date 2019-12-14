<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8a212-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Xamarin アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8a212-101">This tutorial teaches you how to build a Xamarin app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="8a212-102">完成したチュートリアルをダウンロードするだけで済む場合は、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-xamarin)をダウンロードするか、クローンを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8a212-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8a212-103">前提条件</span><span class="sxs-lookup"><span data-stu-id="8a212-103">Prerequisites</span></span>

<span data-ttu-id="8a212-104">このチュートリアルを開始する前に、[開発モードがオンになっ](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)ている Windows 10 を実行しているコンピューターに[Visual Studio](https://visualstudio.microsoft.com/vs/)をインストールしておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a212-104">Before you start this tutorial, you should have [Visual Studio](https://visualstudio.microsoft.com/vs/) installed on a computer running Windows 10 with [Developer mode turned on](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).</span></span> <span data-ttu-id="8a212-105">Visual Studio を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="8a212-105">If you do not have Visual Studio, visit the previous link for download options.</span></span>

<span data-ttu-id="8a212-106">また、Xamarin は、Visual Studio のインストールの一部としてインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a212-106">You should also have Xamarin installed as part of your Visual Studio installation.</span></span> <span data-ttu-id="8a212-107">Xamarin のインストールと構成の手順については、「 [xamarin のインストール](/xamarin/cross-platform/get-started/installation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a212-107">See [Installing Xamarin](/xamarin/cross-platform/get-started/installation) for instructions on installing and configuring Xamarin.</span></span>

<span data-ttu-id="8a212-108">必要に応じて、Visual Studio for Mac がインストールされている Mac にもアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8a212-108">Optionally you should also have access to a Mac with Visual Studio for Mac installed.</span></span> <span data-ttu-id="8a212-109">アクセスできない場合でも、このチュートリアルを完了することはできますが、iOS 固有のセクションを完成させることはできません。</span><span class="sxs-lookup"><span data-stu-id="8a212-109">If you do not have access, you can still complete this tutorial, but will be unable to complete the iOS-specific sections.</span></span>

> [!NOTE]
> <span data-ttu-id="8a212-110">このチュートリアルは、Visual Studio 2019 バージョン16.3.10 および Visual Studio for Mac バージョン7.7.4 を使用して作成されています。</span><span class="sxs-lookup"><span data-stu-id="8a212-110">This tutorial was written with Visual Studio 2019 version 16.3.10 and Visual Studio for Mac version 7.7.4.</span></span> <span data-ttu-id="8a212-111">両方のコンピューターに Android SDK プラットフォーム28がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="8a212-111">Both machines have the Android SDK Platform 28 installed.</span></span> <span data-ttu-id="8a212-112">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。</span><span class="sxs-lookup"><span data-stu-id="8a212-112">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="8a212-113">フィードバック</span><span class="sxs-lookup"><span data-stu-id="8a212-113">Feedback</span></span>

<span data-ttu-id="8a212-114">このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-xamarin)に記入してください。</span><span class="sxs-lookup"><span data-stu-id="8a212-114">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-xamarin).</span></span>
