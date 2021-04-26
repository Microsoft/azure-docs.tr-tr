---
title: ACR görevinin dış kimlik doğrulaması
description: Azure kaynakları için yönetilen bir kimlik kullanarak bir Azure Anahtar Kasası 'nda depolanan Docker Hub kimlik bilgilerini okumak üzere bir Azure Container Registry görevi (ACR görevi) yapılandırın.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 16404f9244818d91c5333eb5eec5944bfdd9df98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781208"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Azure tarafından yönetilen kimlik kullanarak bir ACR görevinde dış kimlik doğrulama 

Bir [ACR görevinde](container-registry-tasks-overview.md), [Azure kaynakları için yönetilen bir kimliği etkinleştirebilirsiniz](container-registry-tasks-authentication-managed-identity.md). Görev, kimlik bilgilerini sağlamaya veya yönetmeye gerek kalmadan diğer Azure kaynaklarına erişmek için kimliği kullanabilir. 

Bu makalede, bir Azure Anahtar Kasası 'nda depolanan gizli dizileri 'ne erişen bir görevde yönetilen bir kimliği nasıl etkinleştireceğinizi öğreneceksiniz. 

Azure kaynaklarını oluşturmak için bu makale, Azure CLı sürüm 2.0.68 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli].

## <a name="scenario-overview"></a>Senaryoya genel bakış

Örnek görev, bir Azure Anahtar Kasası 'nda depolanan Docker Hub kimlik bilgilerini okur. Kimlik bilgileri, özel bir Docker Hub deposuna yazma (gönderme) izinlerine sahip bir Docker Hub hesabı içindir. Kimlik bilgilerini okumak için, görevi yönetilen bir kimlikle yapılandırın ve ilgili izinleri buna atayın. Kimlik ile ilişkili görev bir görüntü oluşturur ve resmi özel depoya göndermek için Docker Hub 'da oturum açar. 

Bu örnek, Kullanıcı tarafından atanan veya sistem tarafından atanan yönetilen kimlik kullanarak adımları gösterir. Kimlik seçiminiz, kuruluşunuzun ihtiyaçlarına bağlıdır.

Gerçek dünyada bir senaryoda, bir şirket, bir yapı işleminin parçası olarak Docker Hub 'daki özel bir depoya görüntü yayımlayabilir. 

## <a name="prerequisites"></a>Önkoşullar

Görevi çalıştırdığınız bir Azure Container Registry 'ye ihtiyacınız vardır. Bu makalede, bu kayıt defteri *myregistry* olarak adlandırılmıştır. Sonraki adımlarda kendi kayıt defteriniz adıyla değiştirin.

Zaten bir Azure Container kayıt defteriniz yoksa, bkz. [hızlı başlangıç: Azure CLI kullanarak özel kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md). Görüntüleri kayıt defterine henüz göndermeniz gerekmez.

Ayrıca, Docker Hub 'da özel bir depoya ve depoya yazma izinlerine sahip bir Docker Hub hesabına ihtiyacınız vardır. Bu örnekte, bu depo, *hubuser/hubrepo* olarak adlandırılmıştır. 

## <a name="create-a-key-vault-and-store-secrets"></a>Anahtar Kasası oluşturma ve gizli dizileri depolama

İlk olarak, gerekirse, *eastus* konumunda *myresourcegroup* adlı bir kaynak grubunu aşağıdaki [az Group Create][az-group-create] komutuyla oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bir Anahtar Kasası oluşturmak için [az keykasacreate][az-keyvault-create] komutunu kullanın. Benzersiz bir Anahtar Kasası adı belirttiğinizden emin olun. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

[Az keykasasecret set][az-keyvault-secret-set] komutunu kullanarak gerekli Docker Hub kimlik bilgilerini anahtar kasasında depolayın. Bu komutlarda, değerler ortam değişkenlerine geçirilir:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

Gerçek dünyada bir senaryoda, gizli dizi büyük olasılıkla ayrı bir işlemde ayarlanabilir ve korunur.

## <a name="define-task-steps-in-yaml-file"></a>YAML dosyasında görev adımlarını tanımlama

Bu örnek görevin adımları bir [YAML dosyasında](container-registry-tasks-reference-yaml.md)tanımlanmıştır. Yerel çalışma dizininde adlı bir dosya oluşturun `dockerhubtask.yaml` ve aşağıdaki içeriği yapıştırın. Dosyadaki Anahtar Kasası adını anahtar kasanızın adıyla değiştirdiğinizden emin olun.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Görev adımları şunları yapın:

* Docker Hub ile kimlik doğrulamak için gizli kimlik bilgilerini yönetin.
* Gizli dizileri komuta geçirerek Docker Hub ile kimlik doğrulaması yapın `docker login` .
* [Azure-Samples/ACR-Tasks](https://github.com/Azure-Samples/acr-tasks.git) deposunda örnek bir Dockerfile kullanarak bir görüntü oluşturun.
* Görüntüyü özel Docker Hub deposuna gönderin.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Seçenek 1: Kullanıcı tarafından atanan kimlikle görev oluşturma

Bu bölümdeki adımlar bir görev oluşturur ve Kullanıcı tarafından atanan bir kimliği etkinleştirir. Bunun yerine sistem tarafından atanan bir kimliği etkinleştirmek istiyorsanız, bkz. [seçenek 2: sistem tarafından atanan kimlik ile görev oluşturma](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Görev Oluştur

Aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek görev *dockerhubtask* 'ı oluşturun. Görev, kaynak kodu bağlamı olmadan çalışır ve komut `dockerhubtask.yaml` çalışma dizinindeki dosyasına başvurur. `--assign-identity`Parametresi, Kullanıcı tarafından atanan kimliğin kaynak kimliğini geçirir. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Anahtar kasasına kimlik erişimi verme

Anahtar kasasında bir erişim ilkesi ayarlamak için aşağıdaki [az keykasası Set-Policy][az-keyvault-set-policy] komutunu çalıştırın. Aşağıdaki örnek, kimliğin anahtar kasasından gizli dizileri okumasına izin verir. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

[Görevi el ile çalıştırmaya](#manually-run-the-task)devam edin.

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. seçenek: sistem tarafından atanan kimlikle görev oluşturma

Bu bölümdeki adımlar bir görev oluşturur ve sistem tarafından atanan bir kimliği etkinleştirir. Bunun yerine Kullanıcı tarafından atanan bir kimliği etkinleştirmek istiyorsanız, bkz. [1. seçenek: Kullanıcı tarafından atanan kimlik ile görev oluşturma](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Görev Oluştur

Aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek görev *dockerhubtask* 'ı oluşturun. Görev, kaynak kodu bağlamı olmadan çalışır ve komut `dockerhubtask.yaml` çalışma dizinindeki dosyasına başvurur. `--assign-identity`Değer içermeyen parametre, görevde sistem tarafından atanan kimliği etkinleştirmesine izin vermez.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Anahtar kasasına kimlik erişimi verme

Anahtar kasasında bir erişim ilkesi ayarlamak için aşağıdaki [az keykasası Set-Policy][az-keyvault-set-policy] komutunu çalıştırın. Aşağıdaki örnek, kimliğin anahtar kasasından gizli dizileri okumasına izin verir. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Görevi el ile çalıştırın

Yönetilen bir kimliği etkinleştirdiğiniz görevin başarıyla çalıştığını doğrulamak için, görevi [az ACR Task Run][az-acr-task-run] komutuyla el ile tetikleyin. `--set`Parametresi, özel depo adını göreve geçirmek için kullanılır. Bu örnekte, yer tutucu Depo adı *hubuser/hubdepodır*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Görev başarıyla çalıştırıldığında, çıkış Docker Hub 'a başarılı kimlik doğrulaması gösterir ve görüntü başarıyla oluşturulup özel depoya gönderilir:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Görüntünün itildiğini onaylamak için, `cf24` özel Docker Hub deposunda etiketi (Bu örnekte) denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bir [ACR görevinde yönetilen kimliği etkinleştirme](container-registry-tasks-authentication-managed-identity.md)hakkında daha fazla bilgi edinin.
* [ACR görevlerine YAML başvurusunu](container-registry-tasks-reference-yaml.md) gör


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
[az-keyvault-create]: /cli/azure/keyvault?#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
