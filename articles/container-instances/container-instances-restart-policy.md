---
title: Azure Container Instances içindeki Kapsayıcılı görevlerle yeniden başlatma ilkeleri kullanın
description: Derleme, test veya görüntü işleme işlerinde olduğu gibi, tamamlama için çalışan görevleri yürütmek üzere Azure Container Instances nasıl kullanacağınızı öğrenin.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 4fe5d9a20249a17030e0ccfa34f6a4f183be0d82
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325674"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Yeniden başlatma ilkeleriyle Kapsayıcılı görevleri çalıştırma

Azure Container Instances kapsayıcıları dağıtmanın kolaylığı ve hızı, bir kapsayıcı örneğinde derleme, test ve görüntü işleme gibi bir kez çalıştırma görevlerinin yürütülmesi için etkileyici bir platform sağlar.

Yapılandırılabilir bir yeniden başlatma ilkesiyle, işlemleriniz tamamlandığında kapsayıcılarınızın durdurulup durdurulduğunu belirtebilirsiniz. Kapsayıcı örnekleri ikinciden faturalandırıldığı için, yalnızca görevi yürüten kapsayıcı çalışırken kullanılan işlem kaynakları için ücretlendirilirsiniz.

Bu makalede sunulan örneklerde Azure CLı kullanılır. [Yerel olarak][azure-cli-install]Azure CLI sürüm 2.0.21 veya üstünü yüklemiş olmanız veya [Azure Cloud Shell](../cloud-shell/overview.md)CLI 'yi kullanmanız gerekir.

## <a name="container-restart-policy"></a>Kapsayıcı yeniden başlatma ilkesi

Azure Container Instances ' de bir [kapsayıcı grubu](container-instances-container-groups.md) oluşturduğunuzda, üç yeniden başlatma ilkesi ayarlarından birini belirtebilirsiniz.

| Yeniden başlatma ilkesi   | Açıklama |
| ---------------- | :---------- |
| `Always` | Kapsayıcı grubundaki kapsayıcılar her zaman yeniden başlatılır. Bu, kapsayıcı oluşturma sırasında yeniden başlatma ilkesi belirtilmediğinde uygulanan **varsayılan** ayardır. |
| `Never` | Kapsayıcı grubundaki kapsayıcılar hiçbir şekilde yeniden başlatılmaz. Kapsayıcılar en fazla bir kez çalışır. |
| `OnFailure` | Kapsayıcı grubundaki kapsayıcılar yalnızca kapsayıcıda yürütülen işlem başarısız olduğunda (sıfır dışında bir çıkış kodu ile sonlandırıldığında) yeniden başlatılır. Kapsayıcılar en az bir kez çalıştırılır. |

## <a name="specify-a-restart-policy"></a>Yeniden başlatma ilkesi belirtin

Yeniden başlatma ilkesi nasıl belirttiğinizde, Azure CLı, Azure PowerShell cmdlet 'leri veya Azure portal gibi kapsayıcı örneklerinizi nasıl oluşturacağınız üzerine bağlıdır. Azure CLI 'da [az Container Create][az-container-create]öğesini `--restart-policy` çağırdığınızda parametresini belirtin.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Tamamlanma için Çalıştır örneği

Yeniden başlatma ilkesini eylemde görmek için Microsoft [aci-WORDCOUNT][aci-wordcount-image] görüntüsünden bir kapsayıcı örneği oluşturun ve `OnFailure` yeniden başlatma ilkesini belirtin. Bu örnek kapsayıcı, varsayılan olarak, Shakespeare 'ın Hamine ait metni analiz ederek en sık kullanılan [](http://shakespeare.mit.edu/hamlet/full.html)10 sözcüğü stdout 'a yazar ve ardından çıkar ' a bir Python betiği çalıştırır.

Aşağıdaki [az Container Create][az-container-create] komutuyla örnek kapsayıcıyı çalıştırın:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances kapsayıcıyı başlatır ve ardından uygulaması (veya komut dosyası, bu durumda) çıktığında bunu bırakır. Azure Container Instances, yeniden başlatma ilkesi `Never` veya `OnFailure`olan bir kapsayıcıyı durdurulduğunda, kapsayıcının durumu **sonlandırıldı**olarak ayarlanır. [Az Container Show][az-container-show] komutuyla bir kapsayıcının durumunu kontrol edebilirsiniz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Örnek çıktı:

```bash
"Terminated"
```

Örnek kapsayıcısının durumu sonlandırıldıktan sonra,kapsayıcı günlüklerini görüntüleyerek onun görev çıktısını görebilirsiniz. Betiğin çıkışını görüntülemek için [az Container logs][az-container-logs] komutunu çalıştırın:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Çıktı:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Bu örnek, komut dosyasının STDOUT 'a gönderdiği çıktıyı gösterir. Ancak Kapsayıcılı görevleriniz, daha sonra almak üzere çıkışını kalıcı depolamaya yazabilir. Örneğin, bir [Azure dosya paylaşımında](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Sonraki adımlar

Birkaç kapsayıcı içeren büyük bir veri kümesini toplu işleme gibi görev tabanlı senaryolar, çalışma zamanında özel [ortam değişkenlerinden](container-instances-environment-variables.md) veya [komut satırlarından](container-instances-start-command.md) faydalanabilir.

Tamamlanmayı çalıştıran kapsayıcılarınızın çıkışını kalıcı hale getirme hakkında daha fazla bilgi için, bkz. [Azure Container Instances bir Azure dosya paylaşma bağlama](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
