<!-- markdownlint-disable MD002 MD041 -->

このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Xamarin アプリを構築する方法について説明します。

> [!TIP]
> 完了したチュートリアルをダウンロードする場合は、リポジトリをダウンロードまたは複製GitHub[できます](https://github.com/microsoftgraph/msgraph-training-xamarin)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、開発者[](https://visualstudio.microsoft.com/vs/)Visual Studioを有効Windows 10コンピューターにインストール[する必要があります](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。 インストールされていない場合はVisual Studio前のリンクにアクセスしてダウンロード オプションを確認してください。

また、Xamarin をインストールの一部としてインストールVisual Studioがあります。 Xamarin [のインストールと構成の手順については、「Xamarin](/xamarin/cross-platform/get-started/installation) のインストール」を参照してください。

必要に応じて、インストールされている Mac にもアクセスVisual Studio for Macがあります。 アクセス権を持たない場合でも、このチュートリアルを完了できますが、iOS 固有のセクションを完了できません。

また、Outlook.com 上のメールボックスを持つ個人用 Microsoft アカウント、または Microsoft の仕事用または学校用のアカウントを持っている必要があります。 Microsoft アカウントをお持ちでない場合は、無料アカウントを取得するためのオプションが 2 つご利用できます。

- 新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 開発者プログラム[にサインアップしてOffice 365無料](https://developer.microsoft.com/office/dev-program)のサブスクリプションをOffice 365できます。

> [!NOTE]
> このチュートリアルは、Visual Studio 2019 バージョン 16.10.3 および Visual Studio for Mac 8.5.1 で作成されました。 両方のコンピューターに Android SDK プラットフォーム 28 がインストールされています。 このガイドの手順は、他のバージョンでも動作しますが、テストされていない場合があります。

## <a name="feedback"></a>フィードバック

このチュートリアルに関するフィードバックは、GitHub[してください](https://github.com/microsoftgraph/msgraph-training-xamarin)。
