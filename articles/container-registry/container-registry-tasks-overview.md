---
title: Azure Container Registry görevler-genel bakış
description: Bulutta güvenli, otomatik kapsayıcı görüntüsü oluşturma, yönetim ve düzeltme eki uygulama sağlayan bir Azure Container Registry özellik paketi olan ACR görevlerine giriş.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/05/2019
ms.author: danlep
ms.openlocfilehash: 45fdd68273ed2cd5cfccf37765935ce9f7bfdc13
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931486"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>ACR görevleriyle kapsayıcı görüntüsü derlemelerini ve bakımını otomatikleştirin

Kapsayıcılar, altyapı ve işletimsel gereksinimlerden uygulama ve geliştirici bağımlılıklarını yalıtmak için yeni sanallaştırma düzeyleri sağlar. Ancak, bu uygulama sanallaştırmanın kapsayıcı yaşam döngüsü üzerinde nasıl yönetildiğini ve düzeltme eki ekleneceğini ele alma gereksinimiyle birlikte kalır.

## <a name="what-is-acr-tasks"></a>ACR görevleri nedir?

**ACR görevleri** Azure Container Registry içindeki bir özellik paketidir. Linux, Windows ve ARM dahil olmak üzere [platformlar](#image-platforms) için bulut tabanlı kapsayıcı görüntüsü oluşturma olanağı sağlar ve Docker kapsayıcılarınız Için [işletim sistemi ve çerçeve düzeltme ekini](#automate-os-and-framework-patching) otomatik hale getirebilir. ACR görevleri, isteğe bağlı kapsayıcı görüntüsü Derlemeleriyle yalnızca "iç döngüyle" geliştirme döngüsünü buluta genişletmez, ancak kaynak kodu güncelleştirmeleri tarafından tetiklenen otomatik yapıları, kapsayıcının temel görüntüsüne veya zamanlayıcılara yönelik güncelleştirmeleri de sağlar. Örneğin, temel görüntü güncelleştirme Tetikleyicileri ile, işletim sistemi ve uygulama çerçevesi düzeltme eki uygulama iş akışınızı otomatikleştirerek, sabit kapsayıcıların ilkelerine uyurken güvenli ortamları koruyun.

## <a name="task-scenarios"></a>Görev senaryoları

ACR görevleri kapsayıcı görüntülerini ve diğer yapıtları derlemek ve sürdürmek için çeşitli senaryoları destekler. Ayrıntılar için bu makaledeki aşağıdaki bölümlere bakın.

* **[Hızlı görev](#quick-task)** -tek bir kapsayıcı görüntüsünü oluşturun ve Azure 'da, yerel bir Docker altyapısı yüklemesine gerek kalmadan bir kapsayıcı kayıt defterine gönderin. `docker build`, bulutta `docker push` düşünün.
* **Otomatik tetiklenen görevler** -bir görüntü oluşturmak için bir veya daha fazla *tetikleyici* etkinleştirin:
  * **[Kaynak kodu güncelleştirmesinde Tetikle](#trigger-task-on-source-code-update)** 
  * **[Temel görüntü güncelleştirmesinde Tetikle](#automate-os-and-framework-patching)** 
  * **[Bir zamanlamaya göre Tetikle](#schedule-a-task)** 
* **[Çok adımlı görev](#multi-step-tasks)** -çok adımlı, çok Kapsayıcılı tabanlı iş akışlarıyla ACR görevlerinin tek görüntü derleme ve gönderme özelliğini genişletir. 

Her bir ACR görevinin ilişkili bir [kaynak kodu bağlamı](#context-locations) vardır; bir kapsayıcı görüntüsü veya başka yapıt oluşturmak için kullanılan bir kaynak dosyaları kümesinin konumu. Örnek bağlamlar bir git deposu veya yerel bir dosya sistemi içerir.

Görevler [çalışma değişkenlerinden](container-registry-tasks-reference-yaml.md#run-variables)de yararlanabilir, böylece görev tanımlarını yeniden kullanabilir ve görüntüler ve yapıtlar için etiketleri standartlaşabilirsiniz.

## <a name="quick-task"></a>Hızlı görev

Kaynak denetimine işlemeden önce uygulamanızı kod yazma, oluşturma ve test etme için yinelemeli bir işlem olan iç döngü geliştirme döngüsü, kapsayıcı yaşam döngüsü yönetiminin başlangıcıdır.

İlk kod satırlarınızı işlemeden önce, ACR görevlerinin [hızlı görev](container-registry-tutorial-quick-task.md) özelliği, kapsayıcı görüntüsü derlemelerinizi Azure 'a devrederek bir tümleşik geliştirme deneyimi sağlayabilir. Hızlı görevlerle, kodunuzu işlemeden önce otomatik derleme tanımlarınızı doğrulayabilirsiniz ve olası sorunları yakalayabilin.

Tanıdık `docker build` biçimini kullanarak, Azure CLı 'deki [az ACR Build][az-acr-build] komutu bir [bağlam](#context-locations) alır (oluşturulacak dosya kümesi), ACR görevlerini gönderir ve varsayılan olarak, oluşturulan görüntüyü tamamlandığında kayıt defterine iletir.

Giriş için bkz. Azure Container Registry [bir kapsayıcı görüntüsü oluşturma ve çalıştırma](container-registry-quickstart-task-cli.md) hızlı başlangıcı.  

ACR görevleri kapsayıcı yaşam döngüsü temel olarak tasarlanmıştır. Örneğin, ACR görevlerini CI/CD çözümünüz ile tümleştirin. Bir [hizmet sorumlusu][az-login-service-principal]ile [az oturum açma][az-login] ÇALıŞTıRARAK, CI/CD çözümünüz görüntü yapılarını açmak için [az ACR Build][az-acr-build] komutları verebilir.

İlk ACR görevleri öğreticisinde hızlı görevleri nasıl kullanacağınızı öğrenin, [bulutta kapsayıcı görüntülerini Azure Container Registry görevlerle oluşturun](container-registry-tutorial-quick-task.md).

> [!TIP]
> Dockerfile olmadan doğrudan kaynak kodundan bir görüntü oluşturup göndermek istiyorsanız, Azure Container Registry [az ACR Pack Build][az-acr-pack-build] komutunu (Önizleme) sağlar. Bu araç, [bulut Yerel Buildpacks](https://buildpacks.io/)kullanarak uygulama kaynak kodundan bir görüntü oluşturur ve gönderir.

## <a name="trigger-task-on-source-code-update"></a>Kaynak kodu güncelleştirmesinde tetikleme görevi

Kod yürütüldüğü sırada bir kapsayıcı görüntüsü oluşturma veya çok adımlı bir görev veya GitHub ya da Azure DevOps 'daki bir git deposuna bir çekme isteği yapıldığında veya güncelleştirilirken tetiklenir. Örneğin, bir git deposu ve isteğe bağlı olarak bir dal ve Dockerfile belirterek Azure [CLI komutu ile][az-acr-task-create] bir derleme görevi yapılandırın. Takımınız depodaki kodu güncelleştirdiğinde, ACR görevler tarafından oluşturulan bir Web kancası depoda tanımlanan kapsayıcı görüntüsünün derlemesini tetikler. 

ACR görevleri görevin bağlamı olarak bir git deposu ayarladığınızda aşağıdaki Tetikleyicileri destekler:

| Tetikleyici | Varsayılan olarak etkin |
| ------- | ------------------ |
| İşleme | Yes |
| Çekme isteği | Hayır |

Tetikleyiciyi yapılandırmak için, bir kişisel erişim belirteci (PAT) görevini GitHub veya Azure DevOps deposunda bir Web kancasını ayarlamaya sağlarsınız.

İkinci ACR görevleri öğreticisinde kaynak kodu işlemesinde derlemelerin nasıl tetikleneceğini öğrenin, [Azure Container Registry görevlerle kapsayıcı görüntüsü derlemelerini otomatikleştirin](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>İşletim sistemi ve çerçeve düzeltme ekini otomatikleştirme

ACR görevlerinin, kapsayıcı derleme iş akışınızı gerçek anlamda geliştirme gücü, bir temel görüntüde güncelleştirme algılama özelliğinden geliyor. Güncelleştirilmiş temel görüntü kayıt defterinize gönderildiğinde veya Docker Hub 'daki gibi bir genel depoda bir temel görüntü güncelleştirilirse, ACR görevleri bu uygulamayı temel alan herhangi bir uygulama görüntüsünü otomatik olarak oluşturabilir.

Kapsayıcı görüntüleri, *temel* görüntülere ve *uygulama* görüntülerine büyük ölçüde kategorilere ayrılabilir. Temel görüntüleriniz genellikle uygulamanızın oluşturulduğu işletim sistemini ve uygulama çerçevelerini, diğer özelleştirmelerle birlikte içerir. Bu temel görüntüler genellikle genel yukarı akış görüntülerini temel alır, örneğin: [alp Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]veya [Node. js][base-node]. Uygulama görüntülerinizin birkaçı ortak bir temel görüntü paylaşabilir.

Bir işletim sistemi veya uygulama çerçevesi görüntüsü, örneğin, kritik bir işletim sistemi güvenlik düzeltme ekine sahip olan yukarı akış bakımını tarafından güncelleştirildiği zaman, temel görüntülerinizi de kritik düzeltme içerecek şekilde güncelleştirmeniz gerekir. Ayrıca, bu yukarı akış düzeltmelerini temel görüntıza dahil etmek için her bir uygulama görüntüsünün yeniden oluşturulması gerekir.

ACR görevleri bir kapsayıcı görüntüsü oluşturduğunda temel görüntü bağımlılıklarını dinamik olarak bulduğu için, bir uygulama görüntüsünün temel görüntüsünün ne zaman güncelleştirileceğini algılayabilir. Önceden yapılandırılmış bir [derleme göreviyle](container-registry-tutorial-base-image-update.md#create-a-task), ACR görevleri daha sonra **her uygulama görüntüsünü sizin için otomatik olarak yeniden oluşturur** . Bu otomatik algılama ve yeniden oluşturma ile, ACR görevleri, güncelleştirilmiş temel görüntenize başvuran her bir uygulama görüntüsünü el ile izlemek ve güncelleştirmek için normalde gereken zaman ve çabayı kaydeder.

Dockerfile 'dan görüntü yapıları için, bir ACR görevi, temel görüntü aşağıdaki konumlardan birinde olduğunda bir temel görüntü güncelleştirmesini izler:

* Görevin çalıştığı aynı Azure Container kayıt defteri
* Aynı bölgedeki başka bir Azure Kapsayıcı kayıt defteri 
* Docker Hub 'da ortak depo
* Microsoft Container Registry genel depo

> [!NOTE]
> * Temel görüntü güncelleştirme tetikleyicisi bir ACR görevinde varsayılan olarak etkindir. 
> * Şu anda ACR görevleri yalnızca uygulama (*çalışma zamanı*) görüntüleri için temel görüntü güncelleştirmelerini izler. ACR görevleri, çok aşamalı Dockerfiles 'da kullanılan ara (*buildtime*) görüntüleri için temel görüntü güncelleştirmelerini izlemez. 

Üçüncü ACR görevleri öğreticisinde işletim sistemi ve çerçeve düzeltme eki uygulama hakkında daha fazla bilgi edinin. [Azure Container Registry görevlerle, temel görüntü güncelleştirme üzerinde görüntü derlemelerini otomatikleştirin](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Görev zamanlama

Görevi oluştururken veya güncelleştirdiğinizde bir veya daha fazla *Zamanlayıcı tetikleyicisi* ayarlayarak isteğe bağlı olarak bir görev zamanlayın. Bir görevi zamanlama, tanımlı bir zamanlamaya göre kapsayıcı iş yüklerini çalıştırmak veya düzenli olarak kayıt defterinize gönderilen görüntülerde bakım işlemleri ya da testler çalıştırmak için yararlıdır. Ayrıntılar için bkz. [tanımlı bir zamanlamaya göre ACR görevi çalıştırma](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Çok adımlı görevler

Çok adımlı görevler, bulutta kapsayıcı görüntüleri oluşturmak, test etmek ve düzeltme eki uygulamak için adım tabanlı görev tanımı ve yürütme sağlar. [YAML dosyasında](container-registry-tasks-reference-yaml.md) tanımlanan görev adımları, kapsayıcı görüntüleri veya diğer yapıtlar için tek tek derleme ve gönderme işlemlerini belirtir. Ayrıca, her adımın kapsayıcıyı kendi yürütme ortamı olarak kullanmasıyla bir veya daha fazla kapsayıcının yürütülmesini de tanımlayabilirler.

Örneğin, aşağıdakileri otomatikleştiren bir çok adımlı görev oluşturabilirsiniz:

1. Web uygulaması görüntüsü oluşturma
1. Web uygulaması kapsayıcısını çalıştırma
1. Web uygulaması test görüntüsü oluşturma
1. Çalışan uygulama kapsayıcısına karşı testler gerçekleştiren Web uygulaması test kapsayıcısını çalıştırın
1. Testler başarılı olursa bir Helu grafik arşiv paketi oluşturun
1. Yeni Held grafik Arşivi paketini kullanarak bir `helm upgrade` gerçekleştirme

Çok adımlı görevler, bir görüntünün oluşturma, çalıştırma ve test etme özelliğini, adım adım bağımlılık desteğiyle daha birleştirilebilir adımlara bölmeye olanak tanır. ACR görevlerinde çok adımlı görevlerle, görüntü oluşturma, test etme ve işletim sistemi ve çerçeve düzeltme eki uygulama iş akışları üzerinde daha ayrıntılı denetime sahip olursunuz.

[ACR görevlerinde multi-step Build, test ve Patch görevlerini Çalıştır](container-registry-tasks-multi-step.md)bölümünde çok adımlı görevler hakkında bilgi edinin.

## <a name="context-locations"></a>Bağlam konumları

Aşağıdaki tabloda ACR görevleri için desteklenen bağlam konumlarına yönelik birkaç örnek gösterilmektedir:

| Bağlam konumu | Açıklama | Örnek |
| ---------------- | ----------- | ------- |
| Yerel dosya sistemi | Yerel dosya sisteminde bir dizin içindeki dosyalar. | `/home/user/projects/myapp` |
| GitHub ana dalı | GitHub deposunun ana (veya diğer varsayılan) daldaki dosyalar.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub dalı | GitHub deposunun belirli bir dalı.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub alt klasörü | GitHub deposunda bulunan bir alt klasör içindeki dosyalar. Örnek, bir dal ve alt klasör belirtiminin birleşimini gösterir. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps alt klasörü | Bir Azure deposunda bulunan bir alt klasör içindeki dosyalar. Örnek, dal ve alt klasör belirtiminin birleşimini gösterir. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Uzak tarbol | Uzak Web sunucusu üzerindeki sıkıştırılmış arşivdeki dosyalar. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Görüntü platformları

Varsayılan olarak ACR görevleri, Linux işletim sistemi ve AMD64 mimarisi için görüntüler oluşturur. Diğer mimarilere yönelik Windows görüntülerini veya Linux görüntülerini derlemek için `--platform` etiketini belirtin. İşletim sistemini ve isteğe bağlı olarak desteklenen bir mimariyi OS/Architecture biçiminde belirtin (örneğin, `--platform Linux/arm`). ARM mimarileri için isteğe bağlı olarak OS/Architecture/VARIANT biçiminde bir değişken belirtin (örneğin, `--platform Linux/arm64/v8`):

| İşletim Sistemi | Mimari|
| --- | ------- | 
| Linux | 'tür<br/>uzaklığını<br/>arm64<br/>386 |
| Windows | 'tür |

## <a name="view-task-logs"></a>Görev günlüklerini görüntüle

Her görev çalıştırması, görev adımlarının başarıyla çalışıp çalışmadığını belirleyebilmek için inceleyebileceğiniz günlük çıktısı üretir. Görevi tetiklemek için [az ACR Build](/cli/azure/acr#az-acr-build), [az ACR Run](/cli/azure/acr#az-acr-run)veya [az ACR Task Run](/cli/azure/acr/task#az-acr-task-run) komutunu kullanırsanız, görev çalıştırmasının günlük çıktısı konsola akışla kaydedilir ve ayrıca daha sonra almak üzere saklanır. Bir görev otomatik olarak tetiklendiğinde (örneğin, kaynak kodu kaydı veya temel görüntü güncelleştirmesi), görev günlükleri yalnızca depolanır. Azure portal çalışan bir görevin günlüklerini görüntüleyin veya [az ACR görev günlükleri](/cli/azure/acr/task#az-acr-task-logs) komutunu kullanın.

Varsayılan olarak, bir kayıt defterindeki görev çalıştırmaları için veriler ve Günlükler 30 gün boyunca tutulur ve sonra otomatik olarak temizlenir. Bir görev çalıştırmasının verilerini arşivlemek istiyorsanız, [az ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) komutunu kullanarak arşivlemeyi etkinleştirin. Aşağıdaki örnek, kayıt defteri *myregistry*içinde *CF11* çalıştırması görevi için arşivlemeyi mümkün bir şekilde sunar.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Sonraki adımlar

Bulutta kapsayıcı görüntüsü derlemelerini ve bakımını otomatik hale getirmeye hazır olduğunuzda [ACR görevler öğretici serisini](container-registry-tutorial-quick-task.md)inceleyin.

İsteğe bağlı olarak [Visual Studio Code Için Docker uzantısını](https://code.visualstudio.com/docs/azure/docker) ve Azure Container Registry 'larınız ile birlikte çalışmak Için [Azure hesap](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) uzantısını yükler. Azure Container Registry 'ye görüntü çekme ve gönderme veya ACR görevlerini Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
