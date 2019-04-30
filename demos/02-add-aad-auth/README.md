# <a name="completed-module-add-azure-ad-authentication"></a>完了したモジュール: Azure AD 認証を追加する

このディレクトリにあるプロジェクトのバージョンは、「 [Azure AD 認証を追加](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=3)する」のチュートリアルを完了することを反映しています。 このバージョンのプロジェクトを使用する場合は、「[予定表データを取得](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=4)する」からチュートリアルの残りの部分を完了する必要があります。

> **注:**「[アプリをポータルに登録](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=2)する」で示されているように、Azure portal にアプリケーションを登録済みであることを前提としています。 このバージョンのサンプルは、次のように構成する必要があります。
>
> 1. ファイルの`./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example`名前を`OAuthSettings.cs`に変更します。
> 1. `OAuthSettings.cs`ファイルを編集し、次のように変更します。
>     1. を`YOUR_APP_ID_HERE` Azure ポータルから取得した**アプリケーション Id**に置き換えます。
>     1. を`YOUR_REDIRECT_URI_HERE` Azure ポータルから取得したリダイレクト`msal` URI に置き換えます。
> 1. **graphtutorial**のプロジェクトで、 `Info.plist`ファイルを開きます。
>     1. [**詳細設定**] タブで、[ **URL の種類**] セクションを探します。 **URL スキーム**エントリを、から`YOUR_REDIRECT_URI_HERE`始まるアプリ登録からリダイレクト URI に変更し`msal`ます。