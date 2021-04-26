---
title: Sürekli dağıtımı yapılandırma
description: GitHub, BitBucket, Azure Repos veya diğer depolardan Azure App Service için CI/CD 'yi etkinleştirmeyi öğrenin. Gereksinimlerinize uyan derleme işlem hattını seçin.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 633d62fc69c516b482d5749a07052337dc71f567
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789492"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service için sürekli dağıtım

[Azure App Service](overview.md) , en son güncelleştirmeleri çekerek [GitHub](https://help.github.com/articles/create-a-repo), [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)ve [Azure Repos](/azure/devops/repos/git/creatingrepo) depolarından sürekli dağıtım yapılmasını mümkün.

> [!NOTE]
> Eski dağıtım deneyimi olan Azure portal **Geliştirme Merkezi (klasik)** sayfası mart, 2021 ' de kullanım dışı olacaktır. Bu değişiklik uygulamanızdaki mevcut dağıtım ayarlarını etkilemez ve **Dağıtım Merkezi** sayfasında uygulama dağıtımını yönetmeye devam edebilirsiniz.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Dağıtım kaynağını Yapılandır

1. [Azure Portal](https://portal.azure.com), App Service uygulamanızın yönetim sayfasına gidin.

1. Sol menüden **Dağıtım Merkezi**  >  **ayarları**' na tıklayın. 

1. **Kaynak** bölümünde, CI/CD seçeneklerinden birini seçin.

    ![Azure App Service için dağıtım merkezi 'nde dağıtım kaynağını seçme şeklini gösterir](media/app-service-continuous-deployment/choose-source.png)

Adımlar için seçiminize karşılık gelen sekmeyi seçin.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub eylemleri](#how-the-github-actions-build-provider-works) varsayılan derleme sağlayıcıdır. Bunu değiştirmek için **sağlayıcıyı Değiştir**  >  **App Service derleme hizmeti** (kudu) > **Tamam**' a tıklayın.

    > [!NOTE]
    > App Service uygulamanızın yapı sağlayıcısı olarak Azure Pipelines kullanmak için, App Service ' de yapılandırmayın. Bunun yerine, CI/CD 'yi doğrudan Azure Pipelines ' den yapılandırın. **Azure Pipelines** seçeneği yalnızca doğru yönde işaret eder.

1. GitHub 'dan ilk defa dağıtım yapıyorsanız, **Yetkilendir** ' e tıklayın ve yetkilendirme istemlerini izleyin. Farklı bir kullanıcının deposundan dağıtmak istiyorsanız, **hesabı Değiştir**' e tıklayın.

1. Azure hesabınızı GitHub ile yetkilendirdikten sonra CI/CD 'yi yapılandırmak için **kuruluş**, **Depo** ve **dalı** seçin.

1. GitHub eylemleri Seçili yapı sağlayıcısı olduğunda, **çalışma zamanı yığını** ve **Sürüm** açılan listeleri ile istediğiniz iş akışı dosyasını seçebilirsiniz. Azure, derleme ve dağıtım görevlerini işlemek için bu iş akışı dosyasını seçtiğiniz GitHub deponuza kaydeder. Değişikliklerinizi kaydetmeden önce dosyayı görmek için **dosya önizleme**' ye tıklayın.

    > [!NOTE]
    > App Service uygulamanızın [dil yığın ayarını](configure-common.md#configure-language-stack-settings) algılar ve en uygun iş akışı şablonunu seçer. Farklı bir şablon seçerseniz, düzgün şekilde çalıştırılmamış bir uygulama dağıtabilir. Daha fazla bilgi için bkz. [GitHub eylemleri derleme sağlayıcısı 'nın çalışması](#how-the-github-actions-build-provider-works).

1. **Kaydet**’e tıklayın.
   
    Seçili depodaki ve daldaki yeni işlemeler artık App Service uygulamanıza sürekli olarak dağıtılır. İşlemeler ve dağıtımları **Günlükler** sekmesinde izleyebilirsiniz.

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

BitBucket tümleştirmesi, derleme otomasyonu için App Service derleme hizmetlerini (kudu) kullanır.

4. BitBucket 'tan ilk defa dağıtım yapıyorsanız, **Yetkilendir** ' e tıklayın ve yetkilendirme istemlerini izleyin. Farklı bir kullanıcının deposundan dağıtmak istiyorsanız, **hesabı Değiştir**' e tıklayın.

1. Bitbucket için, sürekli olarak dağıtmak istediğiniz Bitbucket **ekibini**, **deposunu** ve **dalını** seçin.

1. **Kaydet**’e tıklayın.
   
    Seçili depodaki ve daldaki yeni işlemeler artık App Service uygulamanıza sürekli olarak dağıtılır. İşlemeler ve dağıtımları **Günlükler** sekmesinde izleyebilirsiniz.
   
# <a name="local-git"></a>[Yerel Git](#tab/local)

[Azure App Service Için yerel git dağıtımına](deploy-local-git.md)bakın.

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Dağıtım kaynağı olarak Azure Repos, Windows uygulamaları için destek.
>

4. App Service yapı hizmeti (kudu) varsayılan yapı sağlayıcıdır.

    > [!NOTE]
    > App Service uygulamanızın yapı sağlayıcısı olarak Azure Pipelines kullanmak için, App Service ' de yapılandırmayın. Bunun yerine, CI/CD 'yi doğrudan Azure Pipelines ' den yapılandırın. **Azure Pipelines** seçeneği yalnızca doğru yönde işaret eder.

1. Sürekli olarak dağıtmak istediğiniz **Azure DevOps organizasyonu**, **Proje**, **Depo** ve **dalını** seçin. 

    DevOps kuruluşunuz listede yoksa, henüz Azure aboneliğinize bağlı değildir. Daha fazla bilgi için bkz. [Azure hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Sürekli dağıtımı devre dışı bırakma

1. [Azure Portal](https://portal.azure.com), App Service uygulamanızın yönetim sayfasına gidin.

1. Sol menüden **Dağıtım Merkezi**  >  **ayarlarının**  >  **bağlantısını kes**' e tıklayın. 

    ![Bulut klasörünüzün Azure portal App Service uygulamanızın bağlantısının nasıl yapılacağını gösterir.](media/app-service-continuous-deployment/disable.png)

1. Varsayılan olarak, GitHub eylemleri iş akışı dosyası deponuzda korunur, ancak uygulamanıza dağıtımı tetiklemeye devam eder. Deponuzdan silmek için **iş akışı dosyasını Sil**' i seçin.

1. **Tamam**'a tıklayın.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>GitHub eylemleri derleme sağlayıcısı nasıl kullanılır

GitHub eylemleri derleme sağlayıcısı, [GitHub 'Dan CI/CD](#configure-deployment-source)için bir SEÇENEKTIR ve CI/CD 'yi ayarlamak için aşağıdakileri yapar:

- App Service oluşturma ve dağıtım görevlerini işlemek için GitHub deponuza bir GitHub eylemleri iş akışı dosyası mevduat.
- Uygulamanıza ilişkin yayımlama profilini GitHub parolası olarak ekler. İş akışı dosyası App Service kimlik doğrulaması için bu parolayı kullanır.
- [İş akışı çalıştırma günlüklerinden](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) bilgileri yakalar ve uygulamanızın **dağıtım merkezindeki** **Günlükler** sekmesinde görüntüler.

GitHub eylemleri derleme sağlayıcısını aşağıdaki yollarla özelleştirebilirsiniz:

- GitHub deponuzda oluşturulduktan sonra iş akışı dosyasını özelleştirin. Daha fazla bilgi için bkz. [GitHub eylemleri Için Iş akışı sözdizimi](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). İş akışının [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) eylemiyle App Service için dağıttığınızdan emin olun.
- Seçilen dal korunuyorsa, yapılandırma kaydedilmeden iş akışı dosyasının önizlemesini devam edebilir, ardından el ile deponuza ekleyebilirsiniz. Bu yöntem, Azure portal günlük tümleştirmesi vermez.
- Bir yayımlama profili yerine, Azure Active Directory bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) kullanarak dağıtın.

#### <a name="authenticate-with-a-service-principal"></a>Hizmet sorumlusu ile kimlik doğrulama

Bu isteğe bağlı yapılandırma, varsayılan kimlik doğrulamanın oluşturulan iş akışı dosyasındaki yayımlama profilleriyle yerini alır.

1. [Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir hizmet sorumlusu oluşturun. Aşağıdaki örnekte, *\<subscription-id>* *\<group-name>* ve *\<app-name>* değerlerini kendi değerlerinizle değiştirin:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Güvenlik için, hizmet sorumlusu için gereken en düşük erişimi verin. Önceki örnekteki kapsam, kaynak grubunun tamamı değil, belirli App Service uygulamasıyla sınırlandırılmıştır.
    
1. Sonraki adımda, en üst düzey dahil olmak üzere tüm JSON çıkışını kaydedin `{}` .

1. [GitHub](https://github.com/)'da deponuza gözatıp **Ayarlar > gizlilikler ' ı seçin > yeni bir gizli dizi ekleyin**.

1. Azure CLı komutundan tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi gibi bir ad verin `AZURE_CREDENTIALS` .

1. **Dağıtım Merkezi** tarafından oluşturulan iş akışı dosyasında, `azure/webapps-deploy` adımları aşağıdaki örnek gibi kodla (bir Node.js iş akışı dosyasından değiştirilir) düzeltin:

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Diğer depolardan dağıtma

Windows uygulamaları için, [Mercurial gibi,](https://gitlab.com/)portalın doğrudan desteklemediği bir bulut git veya deposundan sürekli dağıtımı el ile yapılandırabilirsiniz. **Kaynak** açılan menüsünde Dış git ' i seçerek bunu yapabilirsiniz. Daha fazla bilgi için bkz. [el ile adımları kullanarak sürekli dağıtımı ayarlama](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Diğer kaynaklar

* [Azure Pipelines 'den Azure Uygulama Hizmetleri 'ne dağıtma](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Sürekli dağıtımla ilgili yaygın sorunları araştırın](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell kullanma](/powershell/azure/)
* [Kudu Projesi](https://github.com/projectkudu/kudu/wiki)
