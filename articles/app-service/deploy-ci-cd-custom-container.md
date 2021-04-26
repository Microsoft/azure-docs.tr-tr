---
title: Özel kapsayıcılara CI/CD
description: Azure App Service bir özel Windows veya Linux kapsayıcısına sürekli dağıtım ayarlayın.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782612"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Azure App Service özel kapsayıcılarla sürekli dağıtım

Bu öğreticide, yönetilen [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) depolarından veya [Docker Hub 'ından](https://hub.docker.com)özel bir kapsayıcı görüntüsü için sürekli dağıtımı yapılandırırsınız.

## <a name="1-go-to-deployment-center"></a>1. dağıtım merkezi 'ne gidin

[Azure Portal](https://portal.azure.com), App Service uygulamanızın yönetim sayfasına gidin.

Sol menüden **Dağıtım Merkezi**  >  **ayarları**' na tıklayın. 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. dağıtım kaynağını seçin

Dağıtım kaynağını **seçin** senaryonuza bağlıdır:
- **Kapsayıcı kayıt defteri** , kapsayıcı kayıt defteriniz ve App SERVICE arasında CI/CD 'yi ayarlar.
- GitHub 'da kapsayıcı görüntünüz için kaynak kodu korumanız durumunda **GitHub eylemleri** seçeneği sizin için yapılır. GitHub deponuzda yeni işlemeler tarafından tetiklenen dağıtım eylemi, `docker build` `docker push` doğrudan kapsayıcı Kayıt defterinize çalışabilir ve ardından App Service uygulamanızı yeni görüntüyü çalıştıracak şekilde güncelleştirebilir. Daha fazla bilgi için bkz. [CI/CD 'Nin GitHub eylemleri Ile nasıl çalıştığı](#how-cicd-works-with-github-actions).
- **Azure Pipelines** ile CI/CD 'yi ayarlamak için, bkz. [Azure Pipelines Azure Web uygulaması kapsayıcısı dağıtma](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> Docker Compose uygulama için **Container Registry**' ı seçin.

GitHub eylemleri ' ni seçerseniz,  **Yetkilendir** ' e tıklayın ve yetkilendirme istemlerini izleyin. Daha önce GitHub ile zaten yetkilendirildiyseniz, **hesabı Değiştir**' i tıklatarak farklı bir kullanıcının deposundan dağıtım yapabilirsiniz.

Azure hesabınızı GitHub ile yetkilendirdikten sonra, dağıtım yapılacak **organizasyonu**, **depoyu** ve **dalı** **seçin** .
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. kayıt defteri ayarlarını yapılandırma
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. kayıt defteri ayarlarını yapılandırma

Çok Kapsayıcılı (Docker Compose) bir uygulama dağıtmak için **kapsayıcı türünde** **Docker Compose** **seçin** .

**Kapsayıcı türü** açılan listesini görmüyorsanız **kaynağa** geri gidin ve **Container Registry**' yi **seçin** .
::: zone-end

**Kayıt defteri kaynağında**, kapsayıcı kayıt defterinizin nerede olduğunu **seçin** . Azure Container Registry veya Docker Hub 'ı yoksa, **özel kayıt defteri**' ni **seçin** .

::: zone pivot="container-linux"
> [!NOTE]
> Çok Kapsayıcılı (Docker Compose) uygulamanız birden fazla özel görüntü kullanıyorsa, özel görüntülerin aynı özel kayıt defterinde olduğundan ve aynı kullanıcı kimlik bilgileriyle erişilebildiğinden emin olun. Çok Kapsayıcılı uygulamanız yalnızca ortak görüntüleri kullanıyorsa, bazı görüntüler Docker Hub 'ında olmasa bile **Docker Hub**' ı **seçin** .
::: zone-end  

Seçiminizden eşleşen sekmeyi seçerek sonraki adımları izleyin.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

**Kayıt defteri** açılan listesi, uygulamanızla aynı abonelikteki kayıt defterlerini görüntüler. İstediğiniz kayıt defterini **seçin** .

> [!NOTE]
> Farklı bir abonelikteki kayıt defterinden dağıtım yapmak için, bunun yerine **kayıt defteri kaynağında** **özel kayıt defteri** ' ni **seçin** .

::: zone pivot="container-windows"
Dağıtılacak **resmi** ve **etiketi** **seçin** . İsterseniz **Başlangıç dosyasında** başlangıç komutunu **yazın** . 
::: zone-end
::: zone pivot="container-linux"
**Kapsayıcı türüne** bağlı olarak sonraki adımı izleyin:
- **Docker Compose** için, özel görüntüleriniz için kayıt defterini **seçin** . [Docker Compose dosyanızı](https://docs.docker.com/compose/compose-file/)karşıya yüklemek için **Dosya Seç** **' e tıklayın** veya Docker Compose dosyanızın içeriğini **config** dosyasına **yapıştırmanız** yeterlidir.
- **Tek kapsayıcı** için, dağıtılacak **resmi** ve **etiketi** **seçin** . İsterseniz **Başlangıç dosyasında** başlangıç komutunu **yazın** . 
::: zone-end

App Service, kapsayıcınızı başlatırken **Başlangıç dosyasındaki** dizeyi [ `docker run` komutun sonuna ( `[COMMAND] [ARG...]` kesim olarak)](https://docs.docker.com/engine/reference/run/) ekler.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
**Depo erişimi**' nde, dağıtılacak görüntünün genel mi yoksa özel mi olduğunu **seçin** .
::: zone-end
::: zone pivot="container-linux"
**Depo erişimi**' nde, dağıtılacak görüntünün genel mi yoksa özel mi olduğunu **seçin** . Bir veya daha fazla özel görüntü içeren Docker Compose bir uygulama için  **özel**' i seçin.
::: zone-end

Özel bir görüntü seçerseniz, Docker hesabının **oturum açma** (Kullanıcı adı) ve **parolasını** **belirtin** .

::: zone pivot="container-windows"
 Görüntüyü ve etiket adını **tam görüntü adı ve etiketi** olarak `:` (örneğin,) ayırarak sağlayın `nginx:latest` . İsterseniz **Başlangıç dosyasında** başlangıç komutunu **yazın** . 
::: zone-end
::: zone pivot="container-linux"
**Kapsayıcı türüne** bağlı olarak sonraki adımı izleyin:
- **Docker Compose** için, özel görüntüleriniz için kayıt defterini **seçin** . [Docker Compose dosyanızı](https://docs.docker.com/compose/compose-file/)karşıya yüklemek için **Dosya Seç** **' e tıklayın** veya Docker Compose dosyanızın içeriğini **config** dosyasına **yapıştırmanız** yeterlidir.
- **Tek kapsayıcı** için, görüntü ve etiket adını **tam görüntü adı ve etiketi** olarak  `:` (örneğin,) ayırarak sağlayın `nginx:latest` . İsterseniz **Başlangıç dosyasında** başlangıç komutunu **yazın** . 
::: zone-end

App Service, kapsayıcınızı başlatırken **Başlangıç dosyasındaki** dizeyi [ `docker run` komutun sonuna ( `[COMMAND] [ARG...]` kesim olarak)](https://docs.docker.com/engine/reference/run/) ekler.

# <a name="private-registry"></a>[Özel kayıt defteri](#tab/private)

**Sunucu URL 'si**' nde, **https://** ile başlayan sunucunun URL 'sini **yazın** .

**Oturum açma** ve **parola** ' da, özel kayıt defteriniz için oturum açma kimlik bilgilerinizi **yazın** .

::: zone pivot="container-windows"
 Görüntüyü ve etiket adını **tam görüntü adı ve etiketi** olarak `:` (örneğin,) ayırarak sağlayın `nginx:latest` . İsterseniz **Başlangıç dosyasında** başlangıç komutunu **yazın** . 
::: zone-end
::: zone pivot="container-linux"
**Kapsayıcı türüne** bağlı olarak sonraki adımı izleyin:
- **Docker Compose** için, özel görüntüleriniz için kayıt defterini **seçin** . [Docker Compose dosyanızı](https://docs.docker.com/compose/compose-file/)karşıya yüklemek için **Dosya Seç** **' e tıklayın** veya Docker Compose dosyanızın içeriğini **config** dosyasına **yapıştırmanız** yeterlidir.
- **Tek kapsayıcı** için, görüntü ve etiket adını **tam görüntü adı ve etiketi** olarak  `:` (örneğin,) ayırarak sağlayın `nginx:latest` . İsterseniz **Başlangıç dosyasında** başlangıç komutunu **yazın** . 
::: zone-end

App Service, kapsayıcınızı başlatırken **Başlangıç dosyasındaki** dizeyi [ `docker run` komutun sonuna ( `[COMMAND] [ARG...]` kesim olarak)](https://docs.docker.com/engine/reference/run/) ekler.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. CI/CD 'yi etkinleştirme
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. CI/CD 'yi etkinleştir
::: zone-end

App Service, Azure Container Registry ve Docker Hub ile CI/CD tümleştirmesini destekler. Etkinleştirmek için **sürekli dağıtımda** **Açık** ' ı **seçin** .

::: zone pivot="container-linux"
> [!NOTE]
> **Kaynakta** **GitHub eylemleri** ' ni seçerseniz, bu SEÇENEĞI, CI/CD GitHub eylemleri tarafından doğrudan işlendiği için almaz. Bunun yerine, iş akışı dosyasını incelemek için **Önizleme dosyası** **' na Tıklabileceğiniz** bir **iş akışı yapılandırma** bölümü görürsünüz. Azure, derleme ve dağıtım görevlerini işlemek için bu dosyayı seçtiğiniz GitHub Kaynak deponuza kaydeder. Daha fazla bilgi için bkz. [CI/CD 'Nin GitHub eylemleri Ile nasıl çalıştığı](#how-cicd-works-with-github-actions).
::: zone-end

Bu seçeneği etkinleştirdiğinizde, App Service Azure Container Registry veya Docker Hub 'ında deponuza bir Web kancası ekler. Seçtiğiniz görüntü her güncelleştirildiğinde deponuzu bu Web kancasına nakleder `docker push` . Web kancası, App Service uygulamanızın `docker pull` güncelleştirilmiş görüntüyü almak için yeniden başlatılmasına ve çalışmasına neden olur.

**Diğer özel kayıt defterleri için**, Web kancasına el ile veya bir CI/CD işlem hattındaki adımla bir adım gönderebilirsiniz. Web kancası **URL 'si ' nde,** Web kancası URL 'sini almak için **Kopyala** düğmesine **tıklayın** .

::: zone pivot="container-linux"
> [!NOTE]
> Çok Kapsayıcılı (Docker Compose) uygulamalar için destek sınırlıdır: 
> - Azure Container Registry için App Service, seçilen kayıt defterinde kapsam olarak kayıt defteriyle bir Web kancası oluşturur. `docker push`Kayıt defterindeki bir depoya (Docker Compose dosyanız tarafından başvurulmayan olanlar dahil), bir uygulamanın yeniden başlatılmasını tetikler. [Web kancasını](../container-registry/container-registry-webhook.md) daha dar bir kapsam olarak değiştirmek isteyebilirsiniz.
> - Docker Hub, kayıt defteri düzeyinde Web kancalarını desteklemez. Web kancalarını Docker Compose dosyanızda belirtilen görüntülere el ile **eklemeniz** gerekir.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. ayarlarınızı kaydedin
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. ayarlarınızı kaydedin
::: zone-end

 **Kaydet**' e tıklayın.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>CI/CD, GitHub eylemleri ile nasıl kullanılır?

**Kaynakta** **GitHub eylemleri** ' ni seçerseniz ( [Dağıtım Kaynağı Seç](#2-choose-deployment-source)' i SEÇIN), App Service CI/CD 'yi aşağıdaki yollarla ayarlar:

- App Service oluşturma ve dağıtım görevlerini işlemek için GitHub deponuza bir GitHub eylemleri iş akışı dosyası mevduat.
- Özel kayıt defteriniz için kimlik bilgilerini GitHub gizli dizileri olarak ekler. Oluşturulan iş akışı dosyası, özel kayıt defteriniz ile oturum açmak için [Azure/Docker-Login](https://github.com/Azure/docker-login) eylemini çalıştırır ve ardından `docker push` bunu dağıtmak için çalışır.
- Uygulamanıza ilişkin yayımlama profilini GitHub parolası olarak ekler. Oluşturulan iş akışı dosyası App Service kimlik doğrulaması yapmak için bu parolayı kullanır, ardından güncelleştirilmiş görüntüyü yapılandırmak için [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) eylemini çalıştırır ve bu, güncelleştirilmiş görüntüde çekme için bir uygulamanın yeniden başlatılmasını tetikler.
- [İş akışı çalıştırma günlüklerinden](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) bilgileri yakalar ve uygulamanızın **dağıtım merkezindeki** **Günlükler** sekmesinde görüntüler.

GitHub eylemleri derleme sağlayıcısını aşağıdaki yollarla özelleştirebilirsiniz:

- GitHub deponuzda oluşturulduktan sonra iş akışı dosyasını özelleştirin. Daha fazla bilgi için bkz. [GitHub eylemleri Için Iş akışı sözdizimi](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Bir uygulamanın yeniden başlatılmasını tetiklemek için iş akışının [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) eylemiyle biteceğinden emin olun.
- Seçilen dal korunuyorsa, yapılandırma kaydedilmeden iş akışı dosyasının önizlemesini devam edebilir, ardından bu dosyayı ve gerekli GitHub gizli dizilerini deponuza el ile ekleyebilirsiniz. Bu yöntem, Azure portal günlük tümleştirmesi vermez.
- Bir yayımlama profili yerine, Azure Active Directory bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) kullanarak dağıtın.

#### <a name="authenticate-with-a-service-principal"></a>Hizmet sorumlusu ile kimlik doğrulama

Bu isteğe bağlı yapılandırma, varsayılan kimlik doğrulamanın oluşturulan iş akışı dosyasındaki yayımlama profilleriyle yerini alır.

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir hizmet sorumlusu **oluşturun** . Aşağıdaki örnekte,, *\<subscription-id>* *\<group-name>* ve *\<app-name>* değerlerini kendi değerlerinizle değiştirin. Sonraki adımda, en üst düzey dahil olmak üzere tüm JSON çıkışını **kaydedin** `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Güvenlik için, hizmet sorumlusu için gereken en düşük erişimi verin. Önceki örnekteki kapsam, kaynak grubunun tamamı değil, belirli App Service uygulamasıyla sınırlandırılmıştır.

[GitHub](https://github.com/)'da deponuza **gidin** , ardından ayarlar > gizlilikler ' ı **seçerek** **Yeni bir parola ekleyin >**. Azure CLı komutundan tüm JSON çıkışını gizli dizi değeri alanına **yapıştırın** .  Gizli dizi gibi bir ad verin `AZURE_CREDENTIALS` .

**Dağıtım Merkezi** tarafından oluşturulan iş akışı dosyasında,  `azure/webapps-deploy` Aşağıdaki örnekte olduğu gibi kodla adımları düzeltin:

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
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>CLı ile otomatikleştirin

Kapsayıcı kayıt defterini ve Docker görüntüsünü yapılandırmak için [az WebApp config Container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) **komutunu çalıştırın** .

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Özel kayıt defteri](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Çok Kapsayıcılı (Docker Compose) bir uygulamayı yapılandırmak için, bir Docker Compose dosyayı yerel olarak **hazırlayın** ve ardından parametresiyle [az WebApp config Container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) **komutunu çalıştırın** `--multicontainer-config-file` . Docker Compose dosyanız özel görüntüler içeriyorsa,  `--docker-registry-server-*` Önceki örnekte gösterildiği gibi parametreleri ekleyin.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Kapsayıcı kayıt defterinden uygulamanıza olan CI/CD 'yi yapılandırmak için  [az WebApp Deployment Container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) `--enable-cd` parametresini parametresiyle çalıştırın. Komut, Web kancası URL 'sini çıkarır, ancak Web kancasını kayıt defterinizde el ile ayrı bir adımda oluşturmanız gerekir. Aşağıdaki örnek, uygulamanızda CI/CD 'yi sağlar ve ardından Azure Container Registry ' de Web kancasını oluşturmak için çıktıda Web kancası URL 'sini kullanır.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Diğer kaynaklar

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Linux’ta App Service’te .NET Core web uygulaması oluşturma](quickstart-dotnetcore.md)
* [Hızlı başlangıç: App Service özel kapsayıcı çalıştırma](quickstart-custom-container.md)
* [Linux’ta App Service hakkında SSS](faq-app-service-linux.md)
* [Özel kapsayıcıları yapılandırma](configure-custom-container.md)
* [Azure 'a dağıtılacak eylemler iş akışları](https://github.com/Azure/actions-workflow-samples)
