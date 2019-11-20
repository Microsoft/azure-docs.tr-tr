---
title: Azure Container Instances bir başlangıç komut satırı kullanın
description: Bir Azure Container Instance dağıtırken kapsayıcı görüntüsünde yapılandırılan giriş noktasını geçersiz kılma
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 40d946db48a65452d2da529098c07d0d0c60d472
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619670"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Varsayılan komut satırı işlemini geçersiz kılmak için bir kapsayıcı örneğindeki komut satırını ayarlayın

Bir kapsayıcı örneği oluşturduğunuzda, isteğe bağlı olarak kapsayıcı görüntüsüne bakmış varsayılan komut satırı yönergesini geçersiz kılmak için bir komut belirtin. Bu davranış, `--entrypoint` komut satırı `docker run`bağımsız değişkenine benzerdir.

Kapsayıcı örnekleri için [ortam değişkenlerini](container-instances-environment-variables.md) ayarlamak gibi, bir başlangıç komut satırı belirtmek, her kapsayıcıyı göreve özgü yapılandırmayla dinamik olarak hazırlamanız gereken toplu işler için yararlıdır.

## <a name="command-line-guidelines"></a>Komut satırı yönergeleri

* Varsayılan olarak, komut satırı kapsayıcıda *Shell olmadan başlayan tek bir işlemi* belirtir. Örneğin, komut satırı bir Python betiği veya yürütülebilir dosya çalıştırabilir. İşlem ek parametreleri veya bağımsız değişkenleri belirtebilir.

* Birden çok komut yürütmek için, kapsayıcı işletim sisteminde desteklenen bir kabuk ortamını ayarlayarak komut satırınızdan başlayın. Örnekler:

  |İşletim sistemi  |Varsayılan kabuk  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Birden çok komutu sırayla çalışacak şekilde birleştirmek için kabuğun kurallarını izleyin.

* Kapsayıcı yapılandırmasına bağlı olarak, komut satırı yürütülebilir dosyası veya bağımsız değişkenlerin tam yolunu ayarlamanız gerekebilir.

* Komut satırının uzun süre çalışan bir görevi mi yoksa bir kez çalıştır görevini mi belirttiğinden bağımsız olarak kapsayıcı örneği için uygun bir [yeniden başlatma ilkesi](container-instances-restart-policy.md) ayarlayın. Örneğin, bir `Never` veya `OnFailure` bir kez çalıştır görevi için bir yeniden başlatma ilkesi önerilir. 

* Bir kapsayıcı görüntüsünde varsayılan giriş noktası kümesi hakkında bilgiye ihtiyacınız varsa [Docker Image İnceleme](https://docs.docker.com/engine/reference/commandline/image_inspect/) komutunu kullanın.

## <a name="command-line-syntax"></a>Komut satırı sözdizimi

Komut satırı sözdizimi, örnekleri oluşturmak için kullanılan Azure API veya aracına bağlı olarak değişir. Bir kabuk ortamı belirtirseniz, kabuğun komut sözdizimi kurallarını da gözlemleyin.

* [az Container Create][az-container-create] komutu: `--command-line` Parametresi ile bir dize geçirin. Örnek: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet 'i: `-Command` Parametresi ile bir dize geçirin. Örnek: `-Command "echo hello"`.

* Azure portal: Kapsayıcı yapılandırmasının **komut geçersiz kılma** özelliğinde, tırnak işaretleri olmadan, virgülle ayrılmış dizelerin bir listesini sağlayın. Örnek: `python, myscript.py, arg1, arg2`). 

* Kaynak Yöneticisi Template veya YAML dosyası ya da Azure SDK 'larından biri: Komut satırı özelliğini dize dizisi olarak belirtin. Örnek: Kaynak Yöneticisi şablonunda JSON `["python", "myscript.py", "arg1", "arg2"]` dizisi. 

  [Dockerfile](https://docs.docker.com/engine/reference/builder/) söz dizimine alışkın değilseniz, bu biçim cmd yönergesinin *Exec* biçimine benzer.

### <a name="examples"></a>Örnekler

|    |  Azure CLI   | Portal | Şablon | 
| ---- | ---- | --- | --- |
| Tek komut | `--command-line "python myscript.py arg1 arg2"` | **Komut geçersiz kılma**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Birden çok komut | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Komut geçersiz kılma**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLı örneği

Örnek olarak, [Microsoft/aci-WORDCOUNT][aci-wordcount] kapsayıcı resminin davranışını değiştirerek, en sık karşılaşılan kelimeleri bulmak için Shakespeare 'in hamine ilişkin metni analiz edin. *Hamlet*'in çözümlenmesi yerine, farklı bir metin kaynağına işaret eden bir komut satırı ayarlayabilirsiniz.

Varsayılan metni analiz eden [Microsoft/aci-WORDCOUNT][aci-wordcount] kapsayıcısının çıktısını görmek için, aşağıdaki [az Container Create][az-container-create] komutuyla çalıştırın. Başlangıç komut satırı belirtilmedi, bu nedenle varsayılan kapsayıcı komutu çalışır. Bu örnek, çizim amacıyla, en az beş karakter uzunluğunda olan ilk 3 sözcüğü bulmak için [ortam değişkenlerini](container-instances-environment-variables.md) ayarlar:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Kapsayıcının durumu sonlandırıldıktan sonra (durumu denetlemek için [az Container Show][az-container-show] kullanın), çıktıyı görmek için [az Container logs][az-container-logs] komutuyla günlüğü görüntüleyin.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Çıktı:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Şimdi farklı bir komut satırı belirterek farklı metni çözümlemek için ikinci bir örnek kapsayıcı ayarlayın. Kapsayıcı tarafından yürütülen Python betiği, *WORDCOUNT.py*, BIR URL 'yi bir bağımsız değişken olarak kabul eder ve varsayılan yerine bu sayfanın içeriğini işler.

Örneğin, *Romeo ve Juliet*içinde en az beş karakter uzunluğunda olan ilk 3 sözcüğü belirleme:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Yeniden, kapsayıcı sonlandırıldıktan sonrakapsayıcının günlüklerini göstererek çıktıyı görüntüleyin:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Çıktı:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Sonraki adımlar

Birden çok Kapsayıcılı büyük bir veri kümesini toplu işleme gibi görev tabanlı senaryolar, çalışma zamanında özel komut satırlarından yararlanabilir. Görev tabanlı kapsayıcılar çalıştırma hakkında daha fazla bilgi için bkz. [yeniden başlatma ilkeleriyle Kapsayıcılı görevleri çalıştırma](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
