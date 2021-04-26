---
title: Yerel git deposundan dağıtma
description: Azure App Service için yerel git dağıtımını nasıl etkinleştireceğinizi öğrenin. Yerel makinenizden kod dağıtmanın en basit yöntemlerinden biri.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3196233728bb7f6493bbc06234c62d261ac99254
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832419"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service için yerel git dağıtımı

Bu nasıl yapılır Kılavuzu, uygulamanızı yerel bilgisayarınızdaki bir git deposundan [Azure App Service](overview.md) için nasıl dağıtacağınızı gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları takip etmek için:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git 'ı yükler](https://www.git-scm.com/downloads).

- Dağıtmak istediğiniz koda sahip yerel bir git deposuna sahip olabilirsiniz. Örnek bir depoyu indirmek için yerel Terminal pencerenizde aşağıdaki komutu çalıştırın:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

Bkz. [Azure App Service için dağıtım kimlik bilgilerini yapılandırma](deploy-configure-credentials.md). Kullanıcı kapsamı kimlik bilgilerini ya da uygulama kapsamı kimlik bilgilerini kullanabilirsiniz.

## <a name="create-a-git-enabled-app"></a>Git etkin bir uygulama oluşturma

Zaten bir App Service uygulamanız varsa ve yerel git dağıtımını yapılandırmak istiyorsanız, bunun yerine [mevcut bir uygulamayı yapılandırma](#configure-an-existing-app) konusuna bakın.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[`az webapp create`](/cli/azure/webapp#az_webapp_create) `--deployment-local-git` Seçeneğiyle çalıştırın. Örnek:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Çıktı, şunun gibi bir URL içerir: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Uygulamanızı bir sonraki adımda dağıtmak için bu URL 'YI kullanın.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Git deponuzun kökünden [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) komutunu çalıştırın. Örnek:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Bu cmdlet 'i bir git deposu olan bir dizinden çalıştırırsanız, adlı sizin için otomatik olarak App Service uygulamanıza bir git uzak oluşturur `azure` .

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Portalda, önce bir uygulama oluşturmanız ve ardından dağıtımı yapılandırmanız gerekir. Bkz. [var olan bir uygulamayı yapılandırma](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Mevcut bir uygulamayı yapılandırma

Henüz bir uygulama oluşturmadıysanız bunun yerine [Git etkin bir uygulama oluşturma](#create-a-git-enabled-app) bölümüne bakın.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Öğesini çalıştırın [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) . Örnek:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Çıktı, şunun gibi bir URL içerir: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Uygulamanızı bir sonraki adımda dağıtmak için bu URL 'YI kullanın.

> [!TIP]
> Bu URL, kullanıcı kapsamı dağıtım Kullanıcı adını içerir. İsterseniz, bunun yerine [uygulama kapsamı kimlik bilgilerini kullanabilirsiniz](deploy-configure-credentials.md#appscope) . 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

`scmType` [Set-azresource](/powershell/module/az.resources/set-azresource) cmdlet 'ini çalıştırarak uygulamanızın kümesini ayarlayın.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. [Azure Portal](https://portal.azure.com)uygulamanızın yönetim sayfasına gidin.

1. Sol menüden **Dağıtım Merkezi**  >  **ayarları**' nı seçin. Kaynakta **Yerel git** ' i seçin, sonra **Kaydet**' e tıklayın.

    ![Azure portal App Service için yerel git dağıtımının nasıl etkinleştirileceğini gösterir](./media/deploy-local-git/enable-portal.png)

1. Yerel git bölümünde, **Git Clone URI** 'sini daha sonra kopyalayın. Bu URI herhangi bir kimlik bilgisi içermiyor.

-----

## <a name="deploy-the-web-app"></a>Web uygulamasını dağıtma

1. Yerel bir Terminal penceresinde, git deponuzun kök dizinine geçin ve uygulamanızdan aldığınız URL 'YI kullanarak bir git uzak ekleyin. Seçtiğiniz yöntem size bir URL sunmazsa, `https://<app-name>.scm.azurewebsites.net/<app-name>.git` uygulamasındaki uygulama adınızla kullanın `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > [PowerShell 'de New-AzWebApp kullanarak git özellikli bir uygulama oluşturduysanız](#create-a-git-enabled-app), uzak, sizin için zaten oluşturulur.
   
1. İle Azure 'a gönderin `git push azure master` . 
   
1. **Git kimlik bilgileri Yöneticisi** penceresinde, Azure oturum açma kimlik bilgilerinizi değil, [Kullanıcı kapsamınızı veya uygulama kapsamı kimlik bilgilerinizi](#configure-a-deployment-user)girin.

    Git uzak URL 'niz zaten Kullanıcı adı ve parola içeriyorsa sizden uyarılmayacaksınız. 
   
1. Çıktıyı gözden geçirin. For ASP.NET için MSBuild, `npm install` Node.js için ve Python için bkz. çalışma zamanına özgü Otomasyon `pip install` . 
   
1. İçeriğin dağıtıldığını doğrulamak için Azure portal uygulamanıza gidin.

## <a name="troubleshoot-deployment"></a>Dağıtım sorunlarını giderme

Azure 'da bir App Service uygulamasına yayımlamak için git kullandığınızda aşağıdaki genel hata iletilerini görebilirsiniz:

|İleti|Nedeni|Çözüm
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Uygulama çalışır durumda değil.|Uygulamayı Azure portal başlatın. Web uygulaması durdurulduğunda git dağıtımı kullanılamaz.|
|`Couldn't resolve host 'hostname'`|' Azure ' uzak için adres bilgileri yanlış.|`git remote -v`ILIŞKILI URL ile birlikte tüm uzaktan kumandalar listelemek için komutunu kullanın. ' Azure ' uzak için URL 'nin doğru olduğundan emin olun. Gerekirse, doğru URL 'YI kullanarak bu uzak kopyayı kaldırın ve yeniden oluşturun.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Sırasında bir dal belirtmediniz veya ' `git push` `push.default` de değer ayarlamadıysanız `.gitconfig` .|`git push`Ana dalı belirterek yeniden çalıştırın: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|' Azure ' üzerinde uygulama dağıtım dalında eşleşmeyen bir yerel dal gönderdiniz.|Geçerli dalın olduğunu doğrulayın `master` . Varsayılan dalı değiştirmek için uygulama ayarı ' nı kullanın `DEPLOYMENT_BRANCH` .|
|`src refspec [branchname] does not match any.`|' Azure ' uzak üzerinde ana dışında bir dala gönderim çalıştınız.|`git push`Ana dalı belirterek yeniden çalıştırın: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|HTTPS üzerinden büyük bir git deposu göndermeye çalışırsanız bu hata oluşabilir.|Daha büyük olması için yerel makinedeki git yapılandırmasını değiştirin `postBuffer` . Örneğin: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Bir Node.js uygulamasını, ek gerekli modülleri belirten _package.js_ bir dosya ile dağıttınız.|Hatada `npm ERR!` daha fazla bağlam için bu hatadan önce hata iletilerini gözden geçirin. Aşağıda bu hatanın bilinen nedenleri ve ilgili `npm ERR!` iletiler verilmiştir:<br /><br />**Dosyada hatalı biçimlendirilmiş package.js**: `npm ERR! Couldn't read dependencies.`<br /><br />**Yerel modülün Windows için ikili bir dağıtımı yok**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />veya <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ek kaynaklar

- [App Service yapı sunucusu (Project kudu belgeleri)](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service için sürekli dağıtım](deploy-continuous-deployment.md)
- [Örnek: bir Web uygulaması oluşturma ve yerel bir git deposundan kod dağıtma (Azure CLı)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Örnek: bir Web uygulaması oluşturma ve yerel bir git deposundan kod dağıtma (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
