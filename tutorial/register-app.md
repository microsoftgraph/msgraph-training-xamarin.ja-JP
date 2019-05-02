<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="35202-101">この手順では、Azure Active Directory 管理センターを使用して、新しい Azure AD ネイティブアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="35202-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="35202-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="35202-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="35202-103">左側のナビゲーションで [ **Azure Active Directory** ] を選択し、[**管理**] の下にある [**アプリの登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35202-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="35202-104">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="35202-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="35202-105">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="35202-105">Select **New registration**.</span></span> <span data-ttu-id="35202-106">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="35202-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="35202-107">`Xamarin Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="35202-107">Set **Name** to `Xamarin Graph Tutorial`.</span></span>
    - <span data-ttu-id="35202-108">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="35202-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="35202-109">**[リダイレクト URI]** を空のままにします。</span><span class="sxs-lookup"><span data-stu-id="35202-109">Leave **Redirect URI** empty.</span></span>

    ![[アプリケーションの登録] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="35202-111">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="35202-111">Choose **Register**.</span></span> <span data-ttu-id="35202-112">**Xamarin Graph のチュートリアル**ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="35202-112">On the **Xamarin Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリの登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="35202-114">[**リダイレクト URI を追加する**] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="35202-114">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="35202-115">[**リダイレクト uri** ] ページで、[**パブリッククライアント (モバイル、デスクトップ)] セクションの推奨されるリダイレクト uri**を見つけます。</span><span class="sxs-lookup"><span data-stu-id="35202-115">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="35202-116">で`msal`始まる uri**と**、 **urn: ietf: wg: oauth: 2.0: oob** uri を選択します。</span><span class="sxs-lookup"><span data-stu-id="35202-116">Select the URI that begins with `msal` **and** the **urn:ietf:wg:oauth:2.0:oob** URI.</span></span> <span data-ttu-id="35202-117">で`msal`始まる値をコピーして、[**保存**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35202-117">Copy the value that begins with `msal`, then choose **Save**.</span></span> <span data-ttu-id="35202-118">コピーしたリダイレクト URI を保存するには、次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="35202-118">Save the copied redirect URI, you will need it in the next step.</span></span>

    ![リダイレクト Uri ページのスクリーンショット](./images/aad-redirect-uris.png)