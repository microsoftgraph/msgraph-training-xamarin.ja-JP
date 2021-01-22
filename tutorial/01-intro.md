<!-- markdownlint-disable MD002 MD041 -->

このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Xamarin アプリを構築する方法について説明します。

> [!TIP]
> 完成したチュートリアルをダウンロードするだけの場合は [、GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin)リポジトリをダウンロードまたは複製できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、開発者モード [Visual Studio](https://visualstudio.microsoft.com/vs/) Windows 10 を実行しているコンピューターにインストールされている必要 [があります](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。 ダウンロード オプションがない場合Visual Studioダウンロード オプションについては、前のリンクを参照してください。

また、Xamarin をインストールの一部としてインストールVisual Studioがあります。 Xamarin [のインストールと構成の手順については、「Xamarin](/xamarin/cross-platform/get-started/installation) のインストール」を参照してください。

必要に応じて、Mac 用アプリがインストールされた Mac Visual Studioアクセスできる必要があります。 アクセス権を持たない場合でも、このチュートリアルを完了できますが、iOS 固有のセクションを完了できません。

また、メールボックスを持つ個人用の Microsoft アカウントが Outlook.com Microsoft の仕事用アカウントまたは学校アカウントである必要があります。 Microsoft アカウントをお持ちない場合は、無料アカウントを取得するためのオプションが 2 つ提供されています。

- 新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- Office [365 開発者プログラムにサインアップして、365](https://developer.microsoft.com/office/dev-program) サブスクリプションを無料Office取得できます。

> [!NOTE]
> このチュートリアルは、Visual Studio 2019 バージョン 16.8.3 および Visual Studio for Mac バージョン 8.5.1 で記述されました。 どちらのコンピューターにも、Android SDK プラットフォーム 28 がインストールされています。 このガイドの手順は他のバージョンでも動作する可能性がありますが、テストは行ってはいではありません。

## <a name="feedback"></a>フィードバック

このチュートリアルに関するフィードバックは [、GitHub リポジトリで提供してください](https://github.com/microsoftgraph/msgraph-training-xamarin)。
