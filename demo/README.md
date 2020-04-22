# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="9195a-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="9195a-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9195a-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="9195a-102">Prerequisites</span></span>

<span data-ttu-id="9195a-103">このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="9195a-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="9195a-104">開発用コンピューターにインストールされている[Visual Studio](https://visualstudio.microsoft.com/vs/) 。</span><span class="sxs-lookup"><span data-stu-id="9195a-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="9195a-105">(**注:** このチュートリアルは、visual studio 2019 バージョン16.5.2 および visual Studio for Mac バージョン8.5.1 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="9195a-105">(**Note:** This tutorial was written with Visual Studio 2019 version 16.5.2 and Visual Studio for Mac version 8.5.1.</span></span> <span data-ttu-id="9195a-106">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。)</span><span class="sxs-lookup"><span data-stu-id="9195a-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="9195a-107">Xamarin は、Visual Studio のインストールの一部としてインストールされます。</span><span class="sxs-lookup"><span data-stu-id="9195a-107">Xamarin installed as part of your Visual Studio installation.</span></span> <span data-ttu-id="9195a-108">Xamarin のインストールと構成の手順については、「 [xamarin のインストール](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9195a-108">See [Installing Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation) for instructions on installing and configuring Xamarin.</span></span>
- <span data-ttu-id="9195a-109">Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または Microsoft 職場または学校のアカウントのいずれか。</span><span class="sxs-lookup"><span data-stu-id="9195a-109">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="9195a-110">Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="9195a-110">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="9195a-111">[新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。</span><span class="sxs-lookup"><span data-stu-id="9195a-111">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="9195a-112">[Office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="9195a-112">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-an-application-with-the-azure-portal"></a><span data-ttu-id="9195a-113">Azure Portal にアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="9195a-113">Register an application with the Azure Portal</span></span>

1. <span data-ttu-id="9195a-114">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動します。</span><span class="sxs-lookup"><span data-stu-id="9195a-114">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="9195a-115">**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="9195a-115">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="9195a-116">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9195a-116">Select **New registration**.</span></span> <span data-ttu-id="9195a-117">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="9195a-117">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="9195a-118">`Xamarin Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="9195a-118">Set **Name** to `Xamarin Graph Tutorial`.</span></span>
    - <span data-ttu-id="9195a-119">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="9195a-119">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="9195a-120">[**リダイレクト URI (省略可能)**] で、ドロップダウンを [**パブリッククライアント (モバイル & デスクトップ)**] に変更`msauth://com.companyname.GraphTutorial`し、値をに設定します。</span><span class="sxs-lookup"><span data-stu-id="9195a-120">Under **Redirect URI (optional)**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth://com.companyname.GraphTutorial`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](../../tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="9195a-122">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9195a-122">Choose **Register**.</span></span> <span data-ttu-id="9195a-123">**Xamarin Graph のチュートリアル**ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="9195a-123">On the **Xamarin Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](../../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="9195a-125">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="9195a-125">Configure the sample</span></span>

1. <span data-ttu-id="9195a-126">ファイルの`./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example`名前を`OAuthSettings.cs`に変更します。</span><span class="sxs-lookup"><span data-stu-id="9195a-126">Rename the `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` file to `OAuthSettings.cs`.</span></span>
1. <span data-ttu-id="9195a-127">`OAuthSettings.cs`ファイルを編集し、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9195a-127">Edit the `OAuthSettings.cs` file and make the following changes.</span></span>
    1. <span data-ttu-id="9195a-128">を`YOUR_APP_ID_HERE` Azure ポータルから取得した**アプリケーション Id**に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9195a-128">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the Azure portal.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="9195a-129">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="9195a-129">Run the sample</span></span>

<span data-ttu-id="9195a-130">Visual Studio で、 **F5**キーを押すか、デバッグ > 選択して**デバッグを開始**します。</span><span class="sxs-lookup"><span data-stu-id="9195a-130">In Visual Studio, press **F5** or choose **Debug > Start Debugging**.</span></span>
