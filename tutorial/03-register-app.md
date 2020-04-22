<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8942b-101">この手順では、Azure Active Directory 管理センターを使用して、新しい Azure AD ネイティブアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="8942b-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="8942b-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="8942b-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="8942b-103">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8942b-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="8942b-104">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="8942b-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="8942b-105">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8942b-105">Select **New registration**.</span></span> <span data-ttu-id="8942b-106">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="8942b-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="8942b-107">`Xamarin Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="8942b-107">Set **Name** to `Xamarin Graph Tutorial`.</span></span>
    - <span data-ttu-id="8942b-108">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="8942b-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="8942b-109">[**リダイレクト URI (省略可能)**] で、ドロップダウンを [**パブリッククライアント (モバイル & デスクトップ)**] に変更`msauth://com.companyname.GraphTutorial`し、値をに設定します。</span><span class="sxs-lookup"><span data-stu-id="8942b-109">Under **Redirect URI (optional)**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth://com.companyname.GraphTutorial`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="8942b-111">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8942b-111">Select **Register**.</span></span> <span data-ttu-id="8942b-112">**Xamarin Graph のチュートリアル**ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="8942b-112">On the **Xamarin Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)
