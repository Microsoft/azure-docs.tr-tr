---
title: Yerel git deposundan dağıtma-Azure App Service
description: Azure App Service için yerel git dağıtımını nasıl etkinleştireceğinizi öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9714052de5cdfbdbf0789c7b4711469ee9202bac
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070652"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service için yerel git dağıtımı

Bu nasıl yapılır Kılavuzu, uygulamanızı yerel bilgisayarınızdaki bir git deposundan [Azure App Service](overview.md) için nasıl dağıtacağınızı gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzundaki adımları takip etmek için:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git’i yükleyin](https://www.git-scm.com/downloads).
  
- Dağıtmak istediğiniz koda sahip yerel bir git deposuna sahip olabilirsiniz. Örnek bir depoyu indirmek için yerel Terminal pencerenizde aşağıdaki komutu çalıştırın:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Kudu derleme sunucusu ile dağıtma

Kudu App Service yapı sunucusuyla uygulamanız için yerel git dağıtımını etkinleştirmenin en kolay yolu Azure Cloud Shell kullanmaktır. 

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Dağıtım URL 'sini al

Mevcut bir uygulama için yerel git dağıtımını etkinleştirmek üzere URL 'yi almak için Cloud Shell çalıştırın [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) . App \<-name > ve grup \<adı > ' nı uygulamanızın adlarıyla ve Azure Kaynak grubuyla değiştirin.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Ya da git özellikli yeni bir uygulama oluşturmak için, Cloud Shell [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) `--deployment-local-git` parametresiyle çalıştırın. App \<-name >, \<grup adı > ve \<plan adı > Yeni git uygulamanızın adı, Azure Kaynak grubu ve Azure App Service planı ile değiştirin.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Her iki komut şöyle bir URL döndürür `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`:. Uygulamanızı bir sonraki adımda dağıtmak için bu URL 'YI kullanın.

Bu hesap düzeyi URL 'YI kullanmak yerine, uygulama düzeyinde kimlik bilgilerini kullanarak yerel git 'i de etkinleştirebilirsiniz. Azure App Service her uygulama için bu kimlik bilgilerini otomatik olarak oluşturur. 

Cloud Shell aşağıdaki komutu çalıştırarak uygulama kimlik bilgilerini alın. App \<-name > ve grup \<adı > ' nı uygulamanızın adı ve Azure Kaynak grubu adıyla değiştirin.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Uygulamanızı bir sonraki adımda dağıtmak için döndüren URL 'YI kullanın.

### <a name="deploy-the-web-app"></a>Web uygulamasını dağıtma

1. Yerel git deponuza yerel bir Terminal penceresi açın ve bir Azure uzak ekleyin. Aşağıdaki komutta URL > ' yi \<, önceki adımda aldığınız dağıtıma özel URL veya uygulamaya özel URL ile değiştirin.
   
   ```bash
   git remote add azure <url>
   ```
   
1. İle `git push azure master`Azure 'a gönderin. 
   
1. **Git kimlik bilgileri Yöneticisi** penceresinde, Azure oturum açma parolanızı değil, [dağıtım Kullanıcı parolanızı](#configure-a-deployment-user)girin.
   
1. Çıktıyı gözden geçirin. Node. js ve `npm install` `pip install` Python için ASP.NET için MSBuild gibi çalışma zamanına özgü Otomasyon görebilirsiniz. 
   
1. İçeriğin dağıtıldığını doğrulamak için Azure portal uygulamanıza gidin.

## <a name="deploy-with-azure-pipelines-builds"></a>Azure Pipelines Derlemeleriyle dağıtma

Hesabınız gerekli izinlere sahipse, uygulamanız için yerel git dağıtımını etkinleştirmek üzere Azure Pipelines (Önizleme) ayarlayabilirsiniz. 

- Azure hesabınızın Azure Active Directory yazma ve hizmet oluşturma izinlerine sahip olması gerekir. 
  
- Azure hesabınızın, Azure aboneliğinizde **sahip** rolü olmalıdır.

- Kullanmak istediğiniz Azure DevOps projesinde bir yönetici olmanız gerekir.

Azure Pipelines (Önizleme) ile uygulamanız için yerel git dağıtımını etkinleştirmek için:

1. [Azure portal](https://portal.azure.com)Azure App Service uygulama sayfanıza gidin ve Sol menüdeki **Dağıtım Merkezi** ' ni seçin.
   
1. **Dağıtım Merkezi** sayfasında **Yerel git**' i seçin ve ardından **devam**' ı seçin. 
   
   ![Yerel git ' i seçin ve ardından devam ' ı seçin.](media/app-service-deploy-local-git/portal-enable.png)
   
1. **Yapı sağlayıcısı** sayfasında **Azure Pipelines (Önizleme)** öğesini seçin ve ardından **devam**' ı seçin. 
   
   ![Azure Pipelines (Önizleme) öğesini seçin ve ardından devam ' ı seçin.](media/app-service-deploy-local-git/pipeline-builds.png)

1. **Yapılandır** sayfasında, yeni bir Azure DevOps organizasyonu yapılandırın veya mevcut bir kuruluş belirtip **devam**' ı seçin.
   
   > [!NOTE]
   > Mevcut Azure DevOps kuruluşunuz listede yoksa Azure aboneliğinize bağlamanız gerekebilir. Daha fazla bilgi için bkz. [CD yayın işlem hattınızı tanımlama](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. App Service planı [fiyatlandırma katmanınıza](https://azure.microsoft.com/pricing/details/app-service/plans/)bağlı olarak, **hazırlama sayfasına dağıt** sayfasına bakabilirsiniz. [Dağıtım yuvalarının](deploy-staging-slots.md)etkinleştirilip etkinleştirilmeyeceğini seçin ve ardından **devam**' ı seçin.
   
1. **Özet** sayfasında, ayarları gözden geçirin ve **son**' u seçin.
   
1. Azure işlem hattı kullanılabilir olduğunda, git deposu URL 'sini bir sonraki adımda kullanmak üzere **Dağıtım Merkezi** sayfasından kopyalayın. 
   
   ![Git deposu URL 'sini Kopyala](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Yerel Terminal pencerenizde yerel git deponuza bir Azure uzak ekleyin. Komutta, URL > ' \<yi önceki adımdan aldığınız git deposunun URL 'si ile değiştirin.
   
   ```bash
   git remote add azure <url>
   ```
   
1. İle `git push azure master`Azure 'a gönderin. 
   
1. **Git kimlik bilgileri Yöneticisi** sayfasında, VisualStudio.com Kullanıcı adınızla oturum açın. Diğer kimlik doğrulama yöntemleri için bkz. [Azure DevOps Services kimlik doğrulamasına genel bakış](/vsts/git/auth-overview?view=vsts).
   
1. Dağıtım tamamlandıktan sonra, konumundaki derleme ilerlemesini `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`ve dağıtım `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`ilerleme durumunu görüntüleyin.
   
1. İçeriğin dağıtıldığını doğrulamak için Azure portal uygulamanıza gidin.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Dağıtım sorunlarını giderme

Azure 'da bir App Service uygulamasına yayımlamak için git kullandığınızda aşağıdaki genel hata iletilerini görebilirsiniz:

|`Message`|Nedeni|Çözüm
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Uygulama çalışır durumda değil.|Uygulamayı Azure portal başlatın. Web uygulaması durdurulduğunda git dağıtımı kullanılamaz.|
|`Couldn't resolve host 'hostname'`|' Azure ' uzak için adres bilgileri yanlış.|İlişkili URL ile birlikte tüm uzaktan kumandalar listelemek için komutunukullanın.`git remote -v` ' Azure ' uzak için URL 'nin doğru olduğundan emin olun. Gerekirse, doğru URL 'YI kullanarak bu uzak kopyayı kaldırın ve yeniden oluşturun.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Sırasında `git push`bir dal belirtmediniz veya ' de `push.default` `.gitconfig`değer ayarlamadıysanız.|Ana `git push` dalı belirterek yeniden çalıştırın:. `git push azure master`|
|`src refspec [branchname] does not match any.`|' Azure ' uzak üzerinde ana öğe dışında bir dala gönderim girişiminde çalıştınız.|Ana `git push` dalı belirterek yeniden çalıştırın:. `git push azure master`|
|`RPC failed; result=22, HTTP code = 5xx.`|HTTPS üzerinden büyük bir git deposu göndermeye çalışırsanız bu hata oluşabilir.|`postBuffer` Daha büyük olması için yerel makinedeki git yapılandırmasını değiştirin. Örneğin: `git config --global http.postBuffer 524288000`|
|`Error - Changes committed to remote repository but your web app not updated.`|Gerekli modülleri belirten bir _Package. JSON_ dosyası Ile bir Node. js uygulaması dağıttınız.|Hatada daha fazla bağlam için bu hatadan önce hatailetilerinigözdengeçirin.`npm ERR!` Aşağıda bu hatanın bilinen nedenleri ve ilgili `npm ERR!` iletiler verilmiştir:<br /><br />**Hatalı biçimlendirilmiş Package. JSON dosyası**:`npm ERR! Couldn't read dependencies.`<br /><br />**Yerel modülün Windows için ikili bir dağıtımı yok**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />veya <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ek kaynaklar

- [Proje kudu belgeleri](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service için sürekli dağıtım](deploy-continuous-deployment.md)
- [Örnek: Bir Web uygulaması oluşturma ve yerel git deposundan kod dağıtma (Azure CLı)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Örnek: Bir Web uygulaması oluşturma ve yerel git deposundan kod dağıtma (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
