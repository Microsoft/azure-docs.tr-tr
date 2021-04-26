---
title: Öğretici-ACR görevi zamanlama
description: Bu öğreticide, bir veya daha fazla Zamanlayıcı tetikleyicisi ayarlayarak tanımlı bir zamanlamaya göre Azure Container Registry görevinin nasıl çalıştırılacağını öğrenin
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780884"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Öğretici: tanımlı bir zamanlamaya göre bir ACR görevi çalıştırma

Bu öğreticide bir [ACR görevinin](container-registry-tasks-overview.md) bir zamanlamaya göre nasıl çalıştırılacağı gösterilmektedir. Bir veya daha fazla *Zamanlayıcı tetikleyicisi* ayarlayarak bir görev zamanlayın. Zamanlayıcı Tetikleyicileri tek başına veya diğer görev tetikleyicilerle birlikte kullanılabilir.

Bu öğreticide, görevleri planlama ve zamanlama hakkında bilgi edinin:

> [!div class="checklist"]
> * Zamanlayıcı tetikleyicisiyle görev oluşturma
> * Zamanlayıcı tetikleyicilerini yönetme

Bir görevin zamanlanması aşağıdakiler gibi senaryolar için yararlıdır:

* Zamanlanan bakım işlemleri için bir kapsayıcı iş yükü çalıştırın. Örneğin, kayıt defterinizin gereksiz görüntülerini kaldırmak için kapsayıcılı bir uygulama çalıştırın.
* Canlı site izlemenin bir parçası olarak Workday sırasında bir üretim görüntüsünde bir test kümesi çalıştırın.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Görev zamanlama hakkında

* **Cron Ifadesiyle Tetikle** -bir görevin zamanlayıcı tetikleyicisi bir *cron ifadesi* kullanır. İfade, görevin tetiklenmesi için dakika, saat, gün, ay ve haftanın gününü belirten beş alanı olan bir dizedir. Dakikada bir defa kadar olan sıklık desteklenir.

  Örneğin, ifade `"0 12 * * Mon-Fri"` her gün gece UTC tarihinde bir görevi tetikler. Bu makalenin ilerleyen kısımlarında [ayrıntılara](#cron-expressions) bakın.
* **Birden çok süreölçer tetikleyicisi** -zamanlamalar farklı olduğu sürece bir göreve birden fazla Zamanlayıcı ekleme izni verilir.
    * Görevi oluştururken birden fazla Zamanlayıcı tetikleyicisi belirtin veya daha sonra ekleyin.
    * İsteğe bağlı olarak daha kolay yönetim için Tetikleyicileri adlandırın veya ACR görevleri varsayılan tetikleyici adlarını sağlar.
    * Zamanlayıcı zamanlamaları aynı anda çakışırsa ACR görevleri her Zamanlayıcı için zamanlanmış saatte görevi tetikler.
* **Diğer görev Tetikleyicileri** -Zamanlayıcı tarafından tetiklenen bir görevde, Tetikleyicileri [kaynak kodu işlemeye](container-registry-tutorial-build-task.md) veya [temel görüntü güncelleştirmelerine](container-registry-tutorial-base-image-update.md)göre de etkinleştirebilirsiniz. Diğer ACR görevleri gibi, zamanlanmış bir görevi de [el ile çalıştırabilirsiniz][az-acr-task-run] .

## <a name="create-a-task-with-a-timer-trigger"></a>Zamanlayıcı tetikleyicisiyle görev oluşturma

### <a name="task-command"></a>Görev komutu

İlk olarak, aşağıdaki kabuk ortam değişkenini ortamınız için uygun bir değerle doldurun. Bu adımın yapılması kesinlikle zorunlu değildir ancak bu öğreticideki çok satırlı Azure CLI komutlarını yürütmeyi biraz daha kolaylaştırır. Ortam değişkenini doldurmazsanız, her değeri örnek komutlarda göründüğü her yerde el ile değiştirmelisiniz.

[![Ekli başlatma](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell’i başlatma")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

[Az ACR Task Create][az-acr-task-create] komutuyla bir görev oluşturduğunuzda, isteğe bağlı olarak bir Zamanlayıcı tetikleyicisi ekleyebilirsiniz. Parametresini ekleyin `--schedule` ve Zamanlayıcı için bir cron ifadesi geçirin.

Basit bir örnek olarak, aşağıdaki görev `hello-world` her gün Microsoft Container Registry görüntüsünü 21:00 UTC tarihinde çalıştırmayı tetikler. Görev, kaynak kodu bağlamı olmadan çalışır.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Süreölçer tetikleyicisinin yapılandırıldığını görmek için [az ACR Task Show][az-acr-task-show] komutunu çalıştırın. Varsayılan olarak, temel görüntü güncelleştirme tetikleyicisi de etkinleştirilir.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Görevi tetikleme

Doğru ayarlandığından emin olmak için [az ACR görev çalıştırması][az-acr-task-run] ile görevi el ile tetikleyin:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Kapsayıcı başarıyla çalışırsa, çıkış aşağıdakine benzer. Çıkış, önemli adımları göstermek için yoğunlaştırılmış

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Zamanlanan zamandan sonra, zamanlayıcının görevi beklendiği gibi tetiklediğini doğrulamak için [az ACR görev listesi-çalıştırmaları][az-acr-task-list-runs] komutunu çalıştırın:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Süreölçer başarılı olduğunda, çıkış aşağıdakine benzer:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Zamanlayıcı tetikleyicilerini yönetme

ACR görevinin Zamanlayıcı tetikleyicilerini yönetmek için [az ACR Görev Zamanlayıcı][az-acr-task-timer] komutlarını kullanın.

### <a name="add-or-update-a-timer-trigger"></a>Zamanlayıcı tetikleyicisi ekleme veya güncelleştirme

Bir görev oluşturulduktan sonra, isteğe bağlı olarak [az ACR görev süreölçeri Add][az-acr-task-timer-add] komutunu kullanarak bir Zamanlayıcı tetikleyicisi ekleyin. Aşağıdaki örnek, daha önce oluşturulan *timertask* *Timer2* Zamanlayıcı tetikleyici adını ekler. Bu süreölçer, görevi her gün 10:30 UTC 'de tetikler.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

[Az ACR Task Timer Update][az-acr-task-timer-update] komutunu kullanarak var olan bir tetikleyicinin zamanlamasını güncelleştirin veya durumunu değiştirin. Örneğin, *Timer2* adlı tetikleyiciyi, GÖREVI 11:30 UTC 'de tetikleyecek şekilde güncelleştirin:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Zamanlayıcı tetikleyicilerini listeleme

[Az ACR Görev Zamanlayıcı listesi][az-acr-task-timer-list] komutu, bir görev için ayarlanan Zamanlayıcı tetikleyicilerini gösterir:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
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

Bir görevden bir Zamanlayıcı tetikleyicisini kaldırmak için [az ACR Task Timer Remove][az-acr-task-timer-remove] komutunu kullanın. Aşağıdaki örnek, *Timer2* tetikleyicisini *timertask* öğesinden kaldırır:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron ifadeleri

ACR görevleri cron ifadelerini yorumlamak için [Ncrontab](https://github.com/atifaziz/NCrontab) kitaplığını kullanır. ACR görevlerinde desteklenen ifadeler, boşluk ile ayrılmış beş gerekli alana sahiptir:

`{minute} {hour} {day} {month} {day-of-week}`

Cron ifadeleriyle kullanılan saat dilimi Eşgüdümlü Evrensel Saat (UTC). Saatler 24 saat biçimindedir.

> [!NOTE]
> ACR görevleri `{second}` `{year}` cron ifadelerinde veya alanını desteklemez. Başka bir sistemde kullanılan bir cron ifadesini kopyalarsanız, kullanıldıkları takdirde bu alanları kaldırdığınızdan emin olun.

Her alan aşağıdaki değer türlerinden birine sahip olabilir:

|Tür  |Örnek  |Tetiklendiğinde  |
|---------|---------|---------|
|Belirli bir değer |<nobr>`"5 * * * *"`</nobr>|saatte 5 dakikada bir saat geçti|
|Tüm değerler ( `*` )|<nobr>`"* 5 * * *"`</nobr>|saatin her dakikası, 5:00 UTC (günde 60 kez)|
|Bir Aralık ( `-` işleç)|<nobr>`"0 1-3 * * *"`</nobr>|gün başına 3 kez, 1:00, 2:00 ve 3:00 UTC|
|Bir değerler kümesi ( `,` işleç)|<nobr>`"20,30,40 * * * *"`</nobr>|saatte 3 kez, 20 dakika, 30 dakika ve 40 dakika geçmiş|
|Bir Aralık değeri ( `/` işleç)|<nobr>`"*/10 * * * *"`</nobr>|Saat başına 6 kez, 10 dakika, 20 dakika, vb. saat geçti

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

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici serisinde, kapsayıcı kayıt defteri veya kayıt defterleri, kapsayıcı örneği, anahtar kasası ve hizmet sorumlusu dahil oluşturduğunuz tüm kaynakları kaldırmak için aşağıdaki komutları verin:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Zamanlayıcı tarafından otomatik olarak tetiklenen Azure Container Registry görevlerinin nasıl oluşturulacağını öğrendiniz. 

Bir kayıt defterindeki depoları temizlemek için zamanlanmış bir görev kullanmanın bir örneği için bkz. [Azure Container Registry 'den görüntüleri otomatik olarak temizleme](container-registry-auto-purge.md).

Kaynak kodu işlemeleri veya temel görüntü güncelleştirmeleri tarafından tetiklenen görev örnekleri için [ACR Tasks öğreticisi serisinde](container-registry-tutorial-quick-task.md)diğer makalelere bakın.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
