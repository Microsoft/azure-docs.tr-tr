---
title: Görev çalıştırma günlüklerini görüntüleme-görevler
description: ACR görevleri tarafından oluşturulan çalıştırma günlüklerini görüntüleme ve yönetme.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781064"
---
# <a name="view-and-manage-task-run-logs"></a>Görev çalıştırma günlüklerini görüntüleme ve yönetme

[Azure Container Registry görevlerinde](container-registry-tasks-overview.md) çalıştırılan her görev, görev adımlarının başarıyla çalışıp çalışmadığını belirleyebilmek için inceleyebileceğiniz günlük çıktısı oluşturur. 

Bu makalede, görev çalıştırma günlüklerinin nasıl görüntüleneceği ve yönetileceği açıklanmaktadır.

## <a name="view-streamed-logs"></a>Akışlı günlükleri görüntüle

Bir görevi el ile tetiklemeniz durumunda, günlük çıktısı doğrudan konsola akışla kaydedilir. Örneğin, [az ACR Build](/cli/azure/acr#az_acr_build), [az ACR Run](/cli/azure/acr#az_acr_run)veya [az ACR Task Run](/cli/azure/acr/task#az_acr_task_run) komutunu kullanarak bir görevi el ile tetiklersiniz, konsola akan günlük çıkışını görürsünüz. 

Aşağıdaki örnek [az ACR Run](/cli/azure/acr#az_acr_run) komutu aynı kayıt defterinden çekilen bir kapsayıcıyı çalıştıran bir görevi el ile tetikler:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Akışlı günlük:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Depolanan günlükleri görüntüle 

Azure Container Registry tüm görevler için çalıştırma günlüklerini depolar. Depolanan çalışma günlüklerini Azure portal görüntüleyebilirsiniz. Ya da, seçilen günlüğü görüntülemek için [az ACR görev günlükleri](/cli/azure/acr/task#az_acr_task_logs) komutunu kullanın. Varsayılan olarak, Günlükler 30 gün boyunca tutulur.

Bir görev otomatik olarak tetikleniyorsa (örneğin, kaynak kodu güncelleştirmesi için), kayıtlı günlüklere erişmek, çalıştırma günlüklerini görüntülemenin *tek* yoludur. Otomatik görev Tetikleyicileri, kaynak kodu yürütmelerini veya çekme isteklerini, temel görüntü güncelleştirmelerini ve Zamanlayıcı tetikleyicilerini içerir.

Portalda çalıştırma günlüklerini görüntülemek için:

1. Kapsayıcı Kayıt defterinize gidin.
1. **Hizmetler**' de **Görevler**  >  **çalıştırmalar**' ı seçin.
1. Çalıştırma durumunu görüntülemek ve günlükleri çalıştırmak için bir **çalıştırma kimliği** seçin. Günlük, oluşturulduysa akış günlüğü ile aynı bilgileri içerir.

![Görevi çalıştır oturum açma portalını görüntüle](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Azure CLı kullanarak bir günlüğü görüntülemek için [az ACR görev günlüklerini](/cli/azure/acr/task#az_acr_task_logs) çalıştırın ve bır çalıştırma kimliği, bir görev adı ya da bir yapı göreviyle oluşturduğunuz belirli bir görüntüyü belirtin. Bir görev adı belirtilmişse, komut, son oluşturulan çalıştırmanın günlüğünü gösterir.

Aşağıdaki örnek, KIMLIĞI *CF4* olan çalıştırma için günlüğü çıktı olarak verir:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatif günlük depolama

Görev çalıştırma günlüklerini yerel bir dosya sisteminde depolamak veya Azure depolama gibi alternatif bir arşivleme çözümü kullanmak isteyebilirsiniz.

Örneğin, yerel bir *tasklogs* dizini oluşturun ve [az ACR görev günlüklerinin](/cli/azure/acr/task#az_acr_task_logs) çıkışını yerel bir dosyaya yeniden yönlendirin:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Ayrıca, yerel günlük dosyalarını Azure depolama 'ya kaydedebilirsiniz. Örneğin, bir depolama hesabına dosya yüklemek için [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)veya başka yöntemler kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Container Registry görevler](container-registry-tasks-overview.md) hakkında daha fazla bilgi edinin


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
