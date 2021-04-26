---
title: GitHub eylemine göre kapsayıcı örneği dağıt
description: Azure Container Instances bir kapsayıcı görüntüsü oluşturma, gönderme ve dağıtma adımlarını otomatikleştiren bir GitHub eylemi yapılandırın
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: df8f7a546767f1198cee6fc6ceb6032645c14989
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868997"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Kapsayıcı örneği oluşturmak için bir GitHub eylemi yapılandırma

[GitHub eylemleri](https://docs.github.com/en/actions) , yazılım geliştirme iş akışlarınızı aynı yerde otomatik hale getirmek için GitHub 'daki bir özellik paketidir. böylece, çekme istekleri ve sorunları üzerinde kod depolar ve işbirliği yapın.

Tek bir kapsayıcının Azure Container Instances dağıtımını otomatik hale getirmek için GitHub [Azure Container Instances Için dağıt](https://github.com/azure/aci-deploy) eylemini kullanın. Bu eylem, [az Container Create][az-container-create] komutuyla benzer bir kapsayıcı örneği için özellikler ayarlamanıza olanak sağlar.

Bu makalede, bir GitHub deposunda aşağıdaki eylemleri gerçekleştiren bir iş akışının nasıl ayarlanacağı gösterilmektedir:

* Dockerfile dosyasından görüntü oluşturma
* Görüntüyü Azure Container Registry 'ye gönderme
* Kapsayıcı görüntüsünü bir Azure Container örneğine dağıtma

Bu makalede, iş akışını kurmanın iki yolu gösterilmektedir:

* [GitHub iş akışını yapılandırma](#configure-github-workflow) -Azure Container Instances dağıt eylemini ve diğer eylemleri kullanarak bir GitHub deposunda iş akışı oluşturun.  
* [CLI uzantısı kullan](#use-deploy-to-azure-extension) - `az container app up` Azure CLI 'Da Azure 'da [dağıtma](https://github.com/Azure/deploy-to-azure-cli-extension) uzantısında komutunu kullanın. Bu komut GitHub iş akışını ve dağıtım adımlarını oluşturmayı kolaylaştırır.

> [!IMPORTANT]
> Azure Container Instances için GitHub eylemi şu anda önizlemededir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="prerequisites"></a>Önkoşullar

* **GitHub hesabı** -henüz yoksa bir hesap oluşturun https://github.com .
* **Azure CLI** -Azure CLI adımlarını tamamlayabilmeniz için Azure Cloud Shell veya yerel BIR Azure CLI yüklemesi kullanabilirsiniz. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].
* **Azure Container Registry** -Azure [CLI](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)veya diğer yöntemleri kullanarak temel katmanda bir Azure Container Registry oluşturun. Dağıtım için kullanılan kaynak grubunu, GitHub iş akışı için kullanılan bir yere göz atın.

## <a name="set-up-repo"></a>Depoyu ayarlama

* Bu makaledeki örneklerde, aşağıdaki depoyu çatalı yapmak için GitHub kullanın: https://github.com/Azure-Samples/acr-build-helloworld-node

  Bu depo, küçük bir Web uygulamasının kapsayıcı görüntüsünü oluşturmak için bir Dockerfile ve kaynak dosyaları içerir.

  ![GitHub’daki Çatal düğmesinin (vurgulanmış) ekran görüntüsü](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Deponuz için eylemlerin etkinleştirildiğinden emin olun. Komut deponuza gidin ve **Ayarlar**  >  **Eylemler**' i seçin. **Eylemler izinlerinde**, **Bu depo için yerel ve üçüncü taraf eylemlerin etkinleştir** ' in seçildiğinden emin olun.

## <a name="configure-github-workflow"></a>GitHub iş akışını yapılandırma

### <a name="create-service-principal-for-azure-authentication"></a>Azure kimlik doğrulaması için hizmet sorumlusu oluşturma

GitHub iş akışında Azure CLı kimlik doğrulaması için Azure kimlik bilgilerini sağlamanız gerekir. Aşağıdaki örnek, kapsayıcı kayıt defteriniz için kaynak grubu kapsamındaki katkıda bulunan rolüne sahip bir hizmet sorumlusu oluşturur.

İlk olarak, kaynak grubunuzun kaynak KIMLIĞINI alın. Aşağıdaki [az Group Show][az-group-show] komutunda grubunuzun adını değiştirin:

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Hizmet sorumlusu oluşturmak için [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] kullanın:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Çıkış şuna benzer olacaktır:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Sonraki bir adımda kullanıldığından JSON çıkışını kaydedin. Ayrıca, `clientId` sonraki bölümde hizmet sorumlusunu güncelleştirmeniz gereken öğesini de göz önünde bulabilirsiniz.

### <a name="update-service-principal-for-registry-authentication"></a>Kayıt defteri kimlik doğrulaması için hizmet sorumlusu güncelleştirme

Kapsayıcı Kayıt defterinize gönderme ve çekme erişimi sağlamak için Azure hizmet sorumlusu kimlik bilgilerini güncelleştirin. Bu adım, GitHub iş akışının [kapsayıcı kayıt defterinizde kimlik doğrulamak](../container-registry/container-registry-auth-service-principal.md) ve bir Docker görüntüsü göndermek ve çekmek için hizmet sorumlusunu kullanmasını sağlar. 

Kapsayıcı kayıt defterinizin kaynak KIMLIĞINI alın. Kayıt defterinizin adını şu [az ACR Show][az-acr-show] komutunda değiştirin:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Kayıt defterine gönderme ve çekme erişimi sağlayan AcrPush rolünü atamak için [az role atama Create][az-role-assignment-create] ' i kullanın. Hizmet sorumlunun istemci KIMLIĞINI değiştirin:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Kimlik bilgilerini GitHub deposuna Kaydet

1. GitHub Kullanıcı arabiriminde, gizli deponuza gidin ve **Ayarlar** gizli dizileri ' ni seçin  >  . 

1. Aşağıdaki gizli dizileri eklemek için **Yeni bir parola Ekle** ' yi seçin:

|Gizli dizi  |Değer  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Hizmet sorumlusu oluşturma adımındaki tüm JSON çıkışı |
|`REGISTRY_LOGIN_SERVER`   | Kayıt defterinizin oturum açma sunucusu adı (tümü küçük harf). Örnek: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  `clientId`Hizmet sorumlusu oluşturma işleminden gelen JSON çıktısından       |
|`REGISTRY_PASSWORD`     |  `clientSecret`Hizmet sorumlusu oluşturma işleminden gelen JSON çıktısından |
| `RESOURCE_GROUP` | Hizmet sorumlusu kapsamında kullanılan kaynak grubunun adı |

### <a name="create-workflow-file"></a>İş akışı dosyası oluştur

1. GitHub Kullanıcı arabiriminde **Eylemler**  >  **yeni iş akışı**' nı seçin.
1. **Bir iş akışını kendiniz ayarlayın** öğesini seçin.
1. **Yeni dosyayı Düzenle**' de, örnek kodun üzerine yazmak için aşağıdaki YAML içeriğini yapıştırın. Varsayılan dosya adını kabul edin `main.yml` veya seçtiğiniz bir dosya adı sağlayın.
1. **Yürütmeyi Başlat**' ı seçin, isteğe bağlı olarak işlemeniz için kısa ve genişletilmiş açıklamaları sağlayın ve **Yeni Dosya Yürüt**' ü seçin

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>İş akışını doğrula

İş akışı dosyası kaydedildikten sonra iş akışı tetiklenir. İş akışı ilerlemesini gözden geçirmek için **Eylemler**  >  **iş akışları**' na gidin. 

![İş akışı ilerlemesini görüntüleme](./media/container-instances-github-action/github-action-progress.png)

İş akışınızda her adımın durumunu ve sonuçlarını görüntüleme hakkında bilgi için bkz. [iş akışı çalıştırma geçmişini görüntüleme](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) . İş akışı tamamlanmazsa, bkz. [sorunları tanılamak için günlükleri görüntüleme](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures).

İş akışı başarıyla tamamlandığında, [az Container Show][az-container-show] komutunu çalıştırarak *aci-SampleApp* adlı kapsayıcı örneği hakkında bilgi alın. Kaynak grubunuzun adını değiştirin: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Çıkış şuna benzer olacaktır:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Örnek sağlandıktan sonra, çalışan Web uygulamasını görüntülemek için, tarayıcınızda kapsayıcının FQDN 'sine gidin.

![Web uygulamasını tarayıcıda çalıştırma](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Azure 'a dağıt uzantısını kullanma

Alternatif olarak, iş akışını yapılandırmak için Azure CLı 'da [Azure 'Da dağıtma uzantısı](https://github.com/Azure/deploy-to-azure-cli-extension) ' nı kullanın. `az container app up`Uzantıdaki komut, Azure Container Instances dağıtmak üzere bir iş akışı ayarlamak için sizin giriş parametrelerini alır. 

Azure CLı tarafından oluşturulan iş akışı, [GitHub kullanarak el ile oluşturabileceğiniz](#configure-github-workflow)iş akışına benzer.

### <a name="additional-prerequisite"></a>Ek önkoşul

Bu senaryonun [önkoşullarını](#prerequisites) ve [Depo kurulumuna](#set-up-repo) ek olarak, Azure CLI için  **Azure uzantısına dağıtım** ' yi yüklemeniz gerekir.

Uzantıyı yüklemek için [az Extension Add][az-extension-add] komutunu çalıştırın:

```azurecli
az extension add \
  --name deploy-to-azure
```

Uzantıları bulma, yükleme ve yönetme hakkında daha fazla bilgi için bkz. [Azure CLI ile uzantıları kullanma](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>`az container app up` komutunu çalıştırın

Az [Container App up][az-container-app-up] komutunu çalıştırmak için en az:

* Azure Container Registry 'nizin adı, örneğin, *myregistry*
* GitHub deposunun URL 'SI, örneğin `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Örnek komut:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Komut ilerleme durumu

* İstendiğinde GitHub kimlik bilgilerinizi sağlayın veya GitHub hesabınızda kimlik doğrulaması yapmak için *Depo* ve *Kullanıcı* kapsamlarına sahip bir [GitHub kişisel erişim belirteci](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) (Pat) sağlayın. GitHub kimlik bilgilerini sağlarsanız, komut sizin için bir PAT oluşturur. İş akışını yapılandırmak için ek istemleri izleyin.

* Komut iş akışı için depo gizli dizileri oluşturur:

  * Azure CLı için hizmet sorumlusu kimlik bilgileri
  * Azure Container Registry 'ye erişim için kimlik bilgileri

* Komut iş akışı dosyasını depoya kaydettikten sonra iş akışı tetiklenir. 

Çıkış şuna benzer olacaktır:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

GitHub Kullanıcı arabirimindeki her adımın iş akışı durumunu ve sonuçlarını görüntülemek için bkz. [iş akışı çalıştırma geçmişini görüntüleme](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history).

### <a name="validate-workflow"></a>İş akışını doğrula

İş akışı, bu örnekte *ACR-Build-HelloWorld-node* adlı GitHub deponuzun temel adıyla bir Azure Container örneği dağıtır. İş akışı başarıyla tamamlandığında, [az Container Show][az-container-show] komutunu çalıştırarak *ACR-Build-HelloWorld-node* adlı kapsayıcı örneği hakkında bilgi alın. Kaynak grubunuzun adını değiştirin: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Çıkış şuna benzer olacaktır:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Örnek sağlandıktan sonra, çalışan Web uygulamasını görüntülemek için, tarayıcınızda kapsayıcının FQDN 'sine gidin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcı örneğini [az container delete][az-container-delete] komutu ile durdurun:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Kaynak grubunu ve içindeki tüm kaynakları silmek için [az Group Delete][az-group-delete] komutunu çalıştırın:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Sonraki adımlar

Geliştirme iş akışınızı otomatikleştirmeye yönelik daha fazla eylem için [GitHub Market](https://github.com/marketplace?type=actions) 'e gidin


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az_group_show
[az-group-delete]: /cli/azure/group#az_group_delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-container-create]: /cli/azure/container#az_container_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-container-show]: /cli/azure/container#az_container_show
[az-container-delete]: /cli/azure/container#az_container_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-container-app-up]: /cli/azure/container/app#az_container_app_up
