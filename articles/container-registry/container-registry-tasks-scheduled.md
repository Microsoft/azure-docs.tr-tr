---
title: Azure Container Registry görevleri zamanlayın
description: Bu öğreticide, bir veya daha fazla Zamanlayıcı tetikleyicisi ayarlayarak tanımlı bir zamanlamaya göre Azure Container Registry görevinin nasıl çalıştırılacağını öğrenin
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: ae36b8d67d02f8cae0007b7b06485932db851af5
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148631"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Tanımlı bir zamanlamaya göre bir ACR görevi çalıştırma

Bu öğreticide bir [ACR görevinin](container-registry-tasks-overview.md) bir zamanlamaya göre nasıl çalıştırılacağı gösterilmektedir. Bir veya daha fazla *Zamanlayıcı tetikleyicisi*ayarlayarak bir görev zamanlayın. Zamanlayıcı Tetikleyicileri tek başına veya diğer görev tetikleyicilerle birlikte kullanılabilir.

Bu öğreticide, görevleri planlama ve zamanlama hakkında bilgi edinin:

> [!div class="checklist"]
> * Zamanlayıcı tetikleyicisiyle görev oluşturma
> * Zamanlayıcı tetikleyicilerini yönetme

Bir görevin zamanlanması aşağıdakiler gibi senaryolar için yararlıdır:

* Zamanlanan bakım işlemleri için bir kapsayıcı iş yükü çalıştırın. Örneğin, kayıt defterinizin gereksiz görüntülerini kaldırmak için kapsayıcılı bir uygulama çalıştırın.
* Canlı site izlemenin bir parçası olarak Workday sırasında bir üretim görüntüsünde bir test kümesi çalıştırın.

Bu makaledeki örnekleri çalıştırmak için Azure CLı 'nın Azure Cloud Shell veya yerel bir yüklemesini kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.68 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Görev zamanlama hakkında

* **Cron Ifadesiyle Tetikle** -bir görevin zamanlayıcı tetikleyicisi bir *cron ifadesi*kullanır. İfade, görevin tetiklenmesi için dakika, saat, gün, ay ve haftanın gününü belirten beş alanı olan bir dizedir. Dakikada bir defa kadar olan sıklık desteklenir.

  Örneğin, ifade `"0 12 * * Mon-Fri"` her hafta içinde gece UTC tarihinde bir görevi tetikler. Bu makalenin ilerleyen kısımlarında [ayrıntılara](#cron-expressions) bakın.
* **Birden çok süreölçer tetikleyicisi** -zamanlamalar farklı olduğu sürece bir göreve birden fazla Zamanlayıcı ekleme izni verilir.
    * Görevi oluştururken birden fazla Zamanlayıcı tetikleyicisi belirtin veya daha sonra ekleyin.
    * İsteğe bağlı olarak daha kolay yönetim için Tetikleyicileri adlandırın veya ACR görevleri varsayılan tetikleyici adlarını sağlar.
    * Zamanlayıcı zamanlamaları aynı anda çakışırsa ACR görevleri her Zamanlayıcı için zamanlanmış saatte görevi tetikler.
* **Diğer görev Tetikleyicileri** -Zamanlayıcı tarafından tetiklenen bir görevde, Tetikleyicileri [kaynak kodu işlemeye](container-registry-tutorial-build-task.md) veya [temel görüntü güncelleştirmelerine](container-registry-tutorial-base-image-update.md)göre de etkinleştirebilirsiniz. Diğer ACR görevleri gibi, zamanlanmış bir görevi de [el ile tetikleyebilirsiniz][az-acr-task-run] .

## <a name="create-a-task-with-a-timer-trigger"></a>Zamanlayıcı tetikleyicisiyle görev oluşturma

[Az ACR Task Create][az-acr-task-create] komutuyla bir görev oluşturduğunuzda, isteğe bağlı olarak bir Zamanlayıcı tetikleyicisi ekleyebilirsiniz. `--schedule` parametresini ekleyin ve Zamanlayıcı için bir cron ifadesi geçirin.

Basit bir örnek olarak aşağıdaki komut, `hello-world` görüntüsünü Docker Hub 'dan her gün 21:00 UTC 'den çalıştırmayı tetikler. Görev, kaynak kodu bağlamı olmadan çalışır.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Süreölçer tetikleyicisinin yapılandırıldığını görmek için [az ACR Task Show][az-acr-task-show] komutunu çalıştırın. Varsayılan olarak, temel görüntü güncelleştirme tetikleyicisi de etkinleştirilir.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Doğru ayarlandığından emin olmak için [az ACR görev çalıştırması][az-acr-task-run] ile görevi el ile tetikleyin:

```azurecli
az acr task run --name mytask --registry myregistry
```

Kapsayıcı başarıyla çalışırsa, çıkış aşağıdakine benzer:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Zamanlanan zamandan sonra, zamanlayıcının görevi beklendiği gibi tetiklediğini doğrulamak için [az ACR görev listesi-çalıştırmaları][az-acr-task-list-runs] komutunu çalıştırın:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Süreölçer başarılı olduğunda, çıkış aşağıdakine benzer:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Zamanlayıcı tetikleyicilerini yönetme

ACR görevinin Zamanlayıcı tetikleyicilerini yönetmek için [az ACR Görev Zamanlayıcı][az-acr-task-timer] komutlarını kullanın.

### <a name="add-or-update-a-timer-trigger"></a>Zamanlayıcı tetikleyicisi ekleme veya güncelleştirme

Bir görev oluşturulduktan sonra, isteğe bağlı olarak [az ACR görev süreölçeri Add][az-acr-task-timer-add] komutunu kullanarak bir Zamanlayıcı tetikleyicisi ekleyin. Aşağıdaki örnek, daha önce oluşturulan *MyTask* öğesine *Timer2* bir Zamanlayıcı tetikleyicisi adı ekler. Bu süreölçer, görevi her gün 10:30 UTC 'de tetikler.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

[Az ACR Task Timer Update][az-acr-task-timer-update] komutunu kullanarak var olan bir tetikleyicinin zamanlamasını güncelleştirin veya durumunu değiştirin. Örneğin, *Timer2* adlı tetikleyiciyi, GÖREVI 11:30 UTC 'de tetikleyecek şekilde güncelleştirin:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Zamanlayıcı tetikleyicilerini listeleme

[Az ACR Görev Zamanlayıcı listesi][az-acr-task-timer-list] komutu, bir görev için ayarlanan Zamanlayıcı tetikleyicilerini gösterir:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Örnek çıktı:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Zamanlayıcı tetikleyicisini kaldır

Bir görevden bir Zamanlayıcı tetikleyicisini kaldırmak için [az ACR Task Timer Remove][az-acr-task-timer-remove] komutunu kullanın. Aşağıdaki örnek *MyTask* *Timer2* tetikleyicisini kaldırır:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron ifadeleri

ACR görevleri cron ifadelerini yorumlamak için [Ncrontab](https://github.com/atifaziz/NCrontab) kitaplığını kullanır. ACR görevlerinde desteklenen ifadeler, boşluk ile ayrılmış beş gerekli alana sahiptir:

`{minute} {hour} {day} {month} {day-of-week}`

Cron ifadeleriyle kullanılan saat dilimi Eşgüdümlü Evrensel Saat (UTC). Saatler 24 saat biçimindedir.

> [!NOTE]
> ACR görevleri cron ifadelerinde `{second}` veya `{year}` alanını desteklemez. Başka bir sistemde kullanılan bir cron ifadesini kopyalarsanız, kullanıldıkları takdirde bu alanları kaldırdığınızdan emin olun.

Her alan aşağıdaki değer türlerinden birine sahip olabilir:

|Tür  |Örnek  |Tetiklendiğinde  |
|---------|---------|---------|
|Belirli bir değer |<nobr>`"5 * * * *"`</nobr>|saatte 5 dakikada bir saat geçti|
|Tüm değerler (`*`)|<nobr>`"* 5 * * *"`</nobr>|saatin her dakikası, 5:00 UTC (günde 60 kez)|
|Bir Aralık (`-` işleci)|<nobr>`"0 1-3 * * *"`</nobr>|gün başına 3 kez, 1:00, 2:00 ve 3:00 UTC|
|Bir değerler kümesi (`,` işleci)|<nobr>`"20,30,40 * * * *"`</nobr>|saatte 3 kez, 20 dakika, 30 dakika ve 40 dakika geçmiş|
|Aralık değeri (`/` işleci)|<nobr>`"*/10 * * * *"`</nobr>|Saat başına 6 kez, 10 dakika, 20 dakika, vb. saat geçti

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron örnekleri

|Örnek|Tetiklendiğinde  |
|---------|---------|
|`"*/5 * * * *"`|Beş dakikada bir|
|`"0 * * * *"`|her saatin üstünde bir kez|
|`"0 */2 * * *"`|Her iki saatte bir|
|`"0 9-17 * * *"`|9:00 ile 17:00 UTC arasındaki her saat|
|`"30 9 * * *"`|Her gün saat 9:30 UTC|
|`"30 9 * * 1-5"`|Her gün 9:30 UTC|
|`"30 9 * Jan Mon"`|Ocak ayında her Pazartesi 9:30 UTC|


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Zamanlayıcı tarafından otomatik olarak tetiklenen Azure Container Registry görevlerinin nasıl oluşturulacağını öğrendiniz. 

Bir kayıt defterindeki depoları temizlemek için zamanlanmış bir görev kullanmanın bir örneği için bkz. [Azure Container Registry 'den görüntüleri otomatik olarak temizleme](container-registry-auto-purge.md).

Kaynak kodu işlemeleri veya temel görüntü güncelleştirmeleri tarafından tetiklenen görev örnekleri için [ACR Tasks öğreticisi serisinde](container-registry-tutorial-quick-task.md)diğer makalelere bakın.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
