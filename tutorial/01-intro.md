<!-- markdownlint-disable MD002 MD041 -->

このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Xamarin アプリを構築する方法について説明します。

> [!TIP]
> 完成したチュートリアルをダウンロードするだけで済む場合は、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-xamarin)をダウンロードするか、クローンを作成できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、[開発モードがオンになっ](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)ている Windows 10 を実行しているコンピューターに[Visual Studio](https://visualstudio.microsoft.com/vs/)をインストールしておく必要があります。 Visual Studio を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。

また、Xamarin は、Visual Studio のインストールの一部としてインストールする必要があります。 Xamarin のインストールと構成の手順については、「 [xamarin のインストール](/xamarin/cross-platform/get-started/installation)」を参照してください。

必要に応じて、Visual Studio for Mac がインストールされている Mac にもアクセスできます。 アクセスできない場合でも、このチュートリアルを完了することはできますが、iOS 固有のセクションを完成させることはできません。

> [!NOTE]
> このチュートリアルは、Visual Studio 2019 バージョン16.3.10 および Visual Studio for Mac バージョン7.7.4 を使用して作成されています。 両方のコンピューターに Android SDK プラットフォーム28がインストールされています。 このガイドの手順は、他のバージョンでは動作しますが、テストされていません。

## <a name="feedback"></a>フィードバック

このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-xamarin)に記入してください。
