---
title: Azure Container Registry resim kaynaklarını otomatik olarak kaldır
description: Bir Azure Container Registry 'den yaş ve etiket filtresine göre birden çok etiketi ve bildirimi silmek için bir temizleme komutu kullanın ve isteğe bağlı olarak temizleme işlemlerini zamanlayın.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/14/2019
ms.author: danlep
ms.openlocfilehash: 6841bf18f57f514455f7680126c3cc58e7ebd7b1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148923"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Azure Container Registry 'den görüntüleri otomatik olarak Temizleme

Bir Azure Container Registry 'yi bir geliştirme iş akışının parçası olarak kullandığınızda, kayıt defteri kısa bir süre sonra gerek duyulmayan görüntülerle veya diğer yapıtlarla hızlıca doldurabilir. Belirli bir süreden eski olan veya belirtilen bir ad filtresiyle eşleşen tüm etiketleri silmek isteyebilirsiniz. Birden çok yapıyı hızlıca silmek için bu makalede, isteğe bağlı veya [Zamanlanmış](container-registry-tasks-scheduled.md) bir ACR görevi olarak çalıştırabileceğiniz `acr purge` komutu tanıtılmaktadır. 

`acr purge` komutu şu anda, GitHub 'daki [ACR-CLI](https://github.com/Azure/acr-cli) deposunda bulunan kaynak kodundan oluşturulan ortak bir kapsayıcı görüntüsünde (`mcr.microsoft.com/acr/acr-cli:0.1`) dağıtılır.

Bu makalede ACR görev örneklerini çalıştırmak için Azure Cloud Shell veya yerel bir Azure CLı yüklemesi kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.69 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install]. 

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

> [!WARNING]
> `acr purge` komutunu dikkatli bir şekilde kullanın--silinen görüntü verileri KURTARıLAMAZ. Bildirim özetine (görüntü adından farklı olarak) görüntüleri çeken sistemleriniz varsa etiketlenmemiş görüntüleri temizleyemezsiniz. Etiketlenmemiş görüntüleri silmek, bu sistemlerin Kayıt defterinizden görüntüleri çekmesini engeller. Bildirime göre çekmek yerine, [Önerilen en iyi yöntem](container-registry-image-tag-version.md)olan *benzersiz etiketleme* düzenini benimsede düşünün.

Azure CLı komutlarını kullanarak tek resim etiketlerini veya bildirimleri silmek istiyorsanız, bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Temizle komutunu kullanma

`acr purge` Container komutu, bir ad filtresiyle eşleşen ve belirtilen süreden eski olan bir depodaki resimleri etikete göre siler. Varsayılan olarak, temel alınan [Bildirimler](container-registry-concepts.md#manifest) ve katman verileri değil yalnızca etiket başvuruları silinir. Komutun Ayrıca bildirimleri silme seçeneği vardır. 

> [!NOTE]
> `acr purge`, `write-enabled` özniteliğinin `false`olarak ayarlandığı bir resim etiketini veya depoyu silmez. Bilgi için bkz. [Azure Container Registry 'de kapsayıcı görüntüsünü kilitleme](container-registry-image-lock.md).

`acr purge`, bir [ACR görevinde](container-registry-tasks-overview.md)kapsayıcı komutu olarak çalışacak şekilde tasarlanmıştır, böylece görevin çalıştığı kayıt defteriyle otomatik olarak kimlik doğrulaması yapılır. 

`acr purge`çalıştırdığınızda, en azından aşağıdakileri belirtin:

* `--registry`-komutu çalıştırdığınız Azure Container kayıt defteri. 
* `--filter`-depodaki etiketleri filtrelemek için bir depo ve *normal bir ifade* . Örnekler: `--filter "hello-world:.*"` `hello-world` deposundaki tüm etiketlerle eşleşir ve `1`ile başlayan etiketlerle eşleşir `--filter "hello-world:^1.*"`. Birden çok depo temizlemek için birden çok `--filter` parametresi geçirin.
* `--ago`, resimlerin silindiği süreyi belirtmek için bir go stili [Duration dizesi](https://golang.org/pkg/time/) . Süre, her biri birim sonekine sahip bir veya daha fazla ondalık sayı dizisinden oluşur. Geçerli zaman birimleri günler için "d", saat için "h", dakika için "m" içerir. Örneğin, `--ago 2d3h6m` en son 2 gün, 3 saat ve 6 dakika önce değiştirilen tüm filtrelenmiş resimleri seçer ve `--ago 1.5h` en son 1,5 saat önce değiştirilmiş resimleri seçer.

`acr purge`, isteğe bağlı çeşitli parametreleri destekler. Aşağıdaki ikisi bu makaledeki örneklerde kullanılmıştır:

* `--untagged`-ilişkili etiketleri (*etiketlenmemiş bildirimler*) olmayan bildirimlerin silineceğini belirtir.
* `--dry-run`, hiçbir veri silinmediğini belirtir, ancak çıktı komutun bu bayrak olmadan çalıştırıldığı ile aynı olur. Bu parametre, korumak istediğiniz verileri yanlışlıkla silmediğinden emin olmak için bir temizleme komutunun test edilmesi için yararlıdır.

Ek parametreler için `acr purge --help`çalıştırın. 

`acr purge`, akan [çalıştırma](container-registry-tasks-reference-yaml.md#run-variables) ve [görev çalıştırma günlükleri](container-registry-tasks-overview.md#view-task-logs) de dahil olmak üzere ACR görevleri komutlarının diğer özelliklerini destekler ve daha sonra alımı için de kaydedilir.

### <a name="run-in-an-on-demand-task"></a>İsteğe bağlı bir görevde Çalıştır

Aşağıdaki örnek, `acr purge` komutunu talep üzerine çalıştırmak için [az ACR Run][az-acr-run] komutunu kullanır. Bu örnek, 1 günden daha önce değiştirilmiş olan *myregistry* içindeki `hello-world` deposundaki tüm resim etiketlerini ve bildirimlerini siler. Kapsayıcı komutu bir ortam değişkeni kullanılarak geçirilir. Görev, kaynak bağlamı olmadan çalışır.

Bu ve aşağıdaki örneklerde, `acr purge` komutunun çalıştırıldığı kayıt defteri, görevi çalıştıran kayıt defterini gösteren `$Registry` diğer adı kullanılarak belirtilir.

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} --filter 'hello-world:.*' --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Zamanlanmış bir görevde Çalıştır

Aşağıdaki örnek, günlük [Zamanlanmış ACR görevi](container-registry-tasks-scheduled.md)oluşturmak için [az ACR Task Create][az-acr-task-create] komutunu kullanır. Görev, `hello-world` deposunda 7 günden daha önce olan etiketleri temizler. Kapsayıcı komutu bir ortam değişkeni kullanılarak geçirilir. Görev, kaynak bağlamı olmadan çalışır.

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} --filter 'hello-world:.*' --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Süreölçer tetikleyicisinin yapılandırıldığını görmek için [az ACR Task Show][az-acr-task-show] komutunu çalıştırın.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Büyük sayıda etiketi ve bildirimi Temizleme

Çok sayıda etiket ve bildirim temizleme birkaç dakika veya daha uzun sürebilir. Binlerce etiket ve bildirimi temizlemek için, komutun isteğe bağlı bir görev için varsayılan zaman aşımı süresi olan 600 saniye veya zamanlanan bir görevde 3600 saniye daha uzun süre çalışması gerekebilir. Zaman aşımı süresi aşılırsa, etiketlerin ve bildirimlerin yalnızca bir alt kümesi silinir. Büyük ölçekli bir temizleme işleminin tamamlandığından emin olmak için, değeri artırmak için `--timeout` parametresini geçirin. 

Örneğin, aşağıdaki isteğe bağlı görev, 3600 saniyelik bir zaman aşımı süresi (1 saat) ayarlar:

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} --filter 'hello-world:.*' --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Örnek: bir kayıt defterinde birden çok depo için zamanlanmış temizleme

Bu örnekte, bir kayıt defterinde düzenli olarak birden çok depo temizlemek için `acr purge` kullanımı gösterilmektedir. Örneğin, görüntüleri `samples/devimage1` ve depoları `samples/devimage2` veren bir geliştirme işlem hattına sahip olabilirsiniz. Geliştirme görüntülerini düzenli aralıklarla dağıtımlarınız için bir üretim deposuna aktarırsınız, böylece artık geliştirme görüntülerine gerek kalmaz. Haftalık olarak, `samples/devimage1` ve `samples/devimage2` depoları, gelecek haftaki iş için hazırlık aşamasında temizleyemezsiniz.

### <a name="preview-the-purge"></a>Temizlemeyi önizleyin

Verileri silmeden önce, `--dry-run` parametresini kullanarak isteğe bağlı bir temizleme görevi çalıştırmayı öneririz. Bu seçenek, herhangi bir veriyi kaldırmadan komutun temizlendirilecektir etiketleri ve bildirimleri görmenizi sağlar. 

Aşağıdaki örnekte, her bir depodaki filtre tüm etiketleri seçer. `--ago 0d` parametresi, filtrelerle eşleşen depolardaki tüm yaştaki görüntülerle eşleşir. Seçim ölçütlerini senaryonuz için gereken şekilde değiştirin. `--untagged` parametresi, etiketlerin yanı sıra bildirimlerin silineceğini gösterir. Kapsayıcı komutu, bir ortam değişkeni kullanılarak [az ACR Run][az-acr-run] komutuna geçirilir.

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd  "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Seçim parametreleriyle eşleşen etiketleri ve bildirimleri görmek için komut çıkışını gözden geçirin. Komut `--dry-run`ile çalıştırıldığı için hiçbir veri silinmez.

Örnek çıktı:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Temizlemeyi zamanla

Kuru çalıştırmayı doğruladıktan sonra, temizleme işlemini otomatikleştirmek için zamanlanmış bir görev oluşturun. Aşağıdaki örnek, önceki temizleme komutunu çalıştırmak için 1:00 UTC 'de Pazar günü bir haftalık görevi zamanlar:

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Süreölçer tetikleyicisinin yapılandırıldığını görmek için [az ACR Task Show][az-acr-task-show] komutunu çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry [resim verilerini silmeye](container-registry-delete.md) yönelik diğer seçenekler hakkında bilgi edinin.

Görüntü depolama hakkında daha fazla bilgi için bkz. [Azure Container Registry Container Image Storage](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

