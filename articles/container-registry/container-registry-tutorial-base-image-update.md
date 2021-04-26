---
title: Öğretici-temel görüntü güncelleştirmesinde görüntü oluşturmayı Tetikle
description: Bu öğreticide, aynı kayıt defterinde bir temel görüntü güncelleştirildiği zaman buluttaki kapsayıcı görüntüsü yapılarını otomatik olarak tetiklemek üzere bir Azure Container Registry görevinin nasıl yapılandırılacağını öğreneceksiniz.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6d83fb7796cecab686d52d561266bd41daef9e01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780812"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Öğretici: Azure Container Registry 'de temel görüntü güncelleştirildiği zaman kapsayıcı görüntüsü derlemelerini otomatikleştirin 

[ACR görevleri](container-registry-tasks-overview.md) , bir kapsayıcının [temel görüntüsü](container-registry-tasks-base-images.md)güncelleştirilirken (örneğin, işletim sistemi veya uygulama çerçevesini temel görüntülerinizden birinde yamanız gibi) otomatik kapsayıcı görüntüsü derlemelerini destekler. 

Bu öğreticide, bir kapsayıcının temel görüntüsü aynı kayıt defterine gönderildiğinde buluttaki bir derlemeyi tetikleyen ACR görevi oluşturma hakkında bilgi edineceksiniz. Ayrıca, bir temel görüntü [başka bir Azure Container Registry](container-registry-tutorial-private-base-image-update.md)'ye gönderildiğinde görüntü derlemesini tetikleyen bir ACR görevi oluşturma öğreticisini deneyebilirsiniz. 

Bu öğreticide:

> [!div class="checklist"]
> * Temel görüntü oluşturma
> * Temel görüntüyü izlemek için aynı kayıt defterinde bir uygulama görüntüsü oluşturma 
> * Uygulama görüntüsü görevini tetiklemek için temel görüntüyü güncelleştirme
> * Tetiklenen görevi görüntüleme
> * Güncelleştirilmiş uygulama görüntüsünü doğrulama

## <a name="prerequisites"></a>Önkoşullar

### <a name="complete-the-previous-tutorials"></a>Önceki öğreticileri tamamlama

Bu öğreticide, ortamınızı zaten yapılandırdığınız ve serideki ilk iki öğreticideki adımları tamamladığınız varsayılmaktadır:

- Azure kapsayıcı kayıt defteri oluşturma
- Örnek deponun çatalını oluşturma
- Örnek depoyu kopyalama
- GitHub kişisel erişim belirteci oluşturma

Daha önce yapmadıysanız, devam etmeden önce aşağıdaki öğreticilerini doldurun:

[Azure Container Registry Görevleri ile bulutta kapsayıcı görüntüleri derleme](container-registry-tutorial-quick-task.md)

[Azure Container Registry Görevleri ile kapsayıcı görüntüsü derlemelerini otomatik hale getirme](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Ortamı yapılandırma

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Bu makale, Azure CLı 'nin sürüm 2.0.46 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

Bu kabuk ortam değişkenlerini ortamınıza uygun değerlerle doldurun. Bu adımın yapılması kesinlikle zorunlu değildir ancak bu öğreticideki çok satırlı Azure CLI komutlarını yürütmeyi biraz daha kolaylaştırır. Bu ortam değişkenlerini doldurmazsanız, her değeri örnek komutlarda göründüğü her yerde el ile değiştirmelisiniz.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>Temel görüntü güncelleştirme senaryosu

Bu öğretici, bir temel görüntü ve bir uygulama görüntüsünün tek bir kayıt defterinde tutulacağı bir temel görüntü güncelleştirme senaryosunda size kılavuzluk eder. 

[Kod örneği][code-sample] iki Dockerfile: bir uygulama görüntüsü ve bunun temel olarak belirttiği bir görüntü. Aşağıdaki bölümlerde, temel görüntünün yeni bir sürümü aynı kapsayıcı kayıt defterine gönderildiğinde, uygulama görüntüsünün derlemesini otomatik olarak tetikleyen bir ACR görevi oluşturacaksınız.

* [Dockerfile-app][dockerfile-app]: Temel alınan Node.js sürümünün görüntülendiği bir statik web sayfası işleyen küçük bir Node.js web uygulaması. Sürüm dizesinin simülasyonu yapılır ve bu, temel görüntüde tanımlanan `NODE_VERSION` ortam değişkeninin içeriğini görüntüler.

* [Dockerfile-base][dockerfile-base]: `Dockerfile-app` tarafından kendi temeli olarak belirtilen görüntü. Bunun kendisi de [Node][base-node] görüntüsünü temel alır ve `NODE_VERSION` ortam değişkenini içerir.

Aşağıdaki bölümlerde bir görev oluşturacak, temel görüntü Dockerfile içinde `NODE_VERSION` değerini güncelleştirecek ve sonra da ACR Görevlerini kullanarak temel görüntü oluşturacaksınız. ACR görevi yeni temel görüntüyü kayıt defterinize gönderdikten sonra, uygulama görüntüsünün derlemesini otomatik olarak tetikler. İsteğe bağlı olarak, derleme görüntülerinde farklı sürüm dizeleri görmek için uygulama kapsayıcısı görüntüsünü yerel olarak çalıştırırsınız.

Bu öğreticide, ACR göreviniz bir Dockerfile dosyasında belirtilen bir uygulama kapsayıcısı görüntüsünü oluşturur ve gönderir. ACR görevleri, birden çok kapsayıcıyı oluşturma, gönderme ve isteğe bağlı olarak test etme adımlarını tanımlamak için bir YAML dosyası kullanarak [çok adımlı görevler](container-registry-tasks-multi-step.md)de çalıştırabilir.

## <a name="build-the-base-image"></a>Temel görüntü oluşturma

[Az ACR Build][az-acr-build]kullanarak bir ACR görevler *hızlı göreviyle* temel görüntü oluşturarak başlayın. Serinin [ilk öğreticisinde](container-registry-tutorial-quick-task.md) açıklandığı gibi, bu işlem yalnızca görüntüyü oluşturmakla kalmaz, oluşturma başarılı olduysa bunu kapsayıcınızın kayıt defterine de gönderir.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Görev oluşturma

Ardından, [az acr task create][az-acr-task-create] ile bir görev oluşturun:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample1 \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

Bu görev, [önceki öğreticide](container-registry-tutorial-build-task.md)oluşturulan görevle benzerdir. ACR Görevlerine, işlemeler `--context` tarafından belirtilen depoya gönderildiğinde bir görüntü derlemesi tetiklemesini bildirir. Önceki öğreticide görüntüyü oluşturmak için kullanılan Dockerfile, genel bir temel görüntü ( `FROM node:15-alpine` ), bu görevde Dockerfile, [dockerfile-App][dockerfile-app], aynı kayıt defterindeki bir temel görüntüyü belirtir:

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Bu yapılandırma, bu öğreticide daha sonra temel görüntüde bir çerçeve düzeltme ekinin benzetimini yapmayı kolaylaştırır.

## <a name="build-the-application-container"></a>Uygulama kapsayıcısını oluşturma

Görevi el ile tetiklemek ve uygulama görüntüsünü derlemek için [az ACR görev çalıştırmasını][az-acr-task-run] kullanın. Görevin, temel görüntüde uygulama görüntüsünün bağımlılığını izlemesi için bu adım gereklidir.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample1
```

Görev tamamlandıktan sonra, aşağıdaki isteğe bağlı adımı tamamlamak istiyorsanız **Run ID** (örneğin, "da6") değerini not alın.

### <a name="optional-run-application-container-locally"></a>İsteğe bağlı: Uygulama kapsayıcısını yerel olarak çalıştırma

Cloud Shell'de değil de yerel olarak çalışıyorsanız ve Docker'ı yüklediyseniz, temel görüntüsünü oluşturmadan önce web tarayıcısında işlenen uygulamayı görmek için kapsayıcıyı çalıştırın. Cloud Shell kullanıyorsanız bu bölümü atlayın (Cloud Shell `az acr login` veya `docker run` komutunu desteklemez).

İlk olarak, [az ACR oturum açma][az-acr-login]ile kapsayıcı kayıt defterinizde kimlik doğrulaması yapın:

```azurecli
az acr login --name $ACR_NAME
```

Şimdi `docker run` ile kapsayıcıyı yerel olarak çalıştırın. **\<run-id\>** Önceki adımdan alınan çıkışta bulunan çalıştırma kimliğiyle değiştirin (örneğin, "DA6"). Bu örnek, kapsayıcıyı bir `myapp` şekilde adlandırır ve `--rm` bunu durdurduğunuzda kapsayıcıyı kaldırmak için parametresini içerir.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Tarayıcınızda `http://localhost:8080` adresine gidin; aşağıdakine benzer biçimde web sayfasında işlenmiş Node.js sürüm numarasını görüyor olmalısınız. Sonraki adımlardan birinde, sürüm dizesine bir "a" ekleyerek sürümü yükseltirsiniz.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Tarayıcıda örnek uygulamanın ekran görüntüsü":::

Kapsayıcıyı durdurmak ve kaldırmak için aşağıdaki komutu çalıştırın:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Derlemeleri listeleme

Bu adımda [az acr task list-runs][az-acr-task-list-runs] komutunu kullanarak ACR Görevlerinin kayıt defteriniz için tamamladığı çalıştırmaları listeleyin:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Önceki öğreticiyi tamamladıysanız (ve kayıt defterini silmediyseniz), aşağıdakine benzer bir çıkış görüyor olmalısınız. Sonraki bölümde temel görüntüyü güncelleştirdikten sonra çıkışı karşılaştırabilmek için görev sayısını ve en son RUN ID değerini not alın.

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cax       baseexample1    linux       Succeeded  Manual     2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual     2020-11-20T23:07:29Z  00:00:27
```

## <a name="update-the-base-image"></a>Temel görüntüyü güncelleştirme

Burada, temel görüntüde bir çerçeve yamasının simülasyonunu yaparsınız. **Dockerfile-base** öğesini düzenleyin ve `NODE_VERSION` içinde tanımlanan sürüm numarasından sonra bir "a" ekleyin:

```dockerfile
ENV NODE_VERSION 15.2.1a
```

Değiştirilmiş temel görüntüyü derlemek için bir hızlı görev çalıştırın. Çıkıştaki **Run ID** değerini not alın.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

Derleme tamamlandıktan ve ACR görevi yeni temel görüntüyü kayıt defterinize gönderdikten sonra, uygulama görüntüsünün derlemesini tetikler. Daha önce oluşturduğunuz görevin uygulama görüntüsü derlemeyi tetiklemesi birkaç dakika sürebilir çünkü yeni derlenen ve gönderilen temel görüntüyü algılaması gerekir.

## <a name="list-updated-build"></a>Güncelleştirilmiş derlemeyi listeleme

Artık temel görüntüyü güncelleştirdiğinize göre, görev çalıştırmalarınızı bir kez daha listeleyip önceki listeyle bunu karşılaştırın. İlk seferinde çıkış farklı görünmüyorsa, yeni görev çalıştırmasının listede yer aldığını görmek için komutu düzenli aralıklarla çalıştırın.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Çıktı aşağıdakine benzer olacaktır. Son yürütülen derlemenin TRIGGER değeri "Image Update" olmalıdır; bu değer, görevin temel görüntünüzün hızlı görevi ile başlatıldığını gösterir.

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual        2020-11-20T23:07:29Z  00:00:27
```

Güncelleştirilmiş sürüm numarasını görmek için yeni oluşturulan kapsayıcıyı çalıştırmanın aşağıdaki isteğe bağlı adımını gerçekleştirmek istiyorsanız, görüntü güncelleştirmesi tarafından tetiklenen derleme için **çalıştırma kimliği** değerini (önceki çıktıda, "CA11") göz atın.

### <a name="optional-run-newly-built-image"></a>İsteğe bağlı: Yeni oluşturulan görüntüyü çalıştırma

Cloud Shell'de değil de yerel olarak çalışıyorsanız ve Docker'ı yüklediyseniz, derlemesi tamamlandıktan sonra yeni uygulama görüntüsünü çalıştırın. `<run-id>` değerinin yerine önceki adımda aldığınız RUN ID değerini koyun. Cloud Shell kullanıyorsanız bu bölümü atlayın (Cloud Shell `docker run` komutunu desteklemez).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Tarayıcınızda http://localhost:8081 adresine gidin; web sayfasında güncelleştirilmiş Node.js sürüm numarasını ("a" ile) görüyor olmalısınız:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Tarayıcıdaki güncelleştirilmiş örnek uygulamanın ekran görüntüsü":::


**Temel** görüntünüzü yeni sürüm numarasıyla güncelleştirdiğinize ama son oluşturulan **uygulama** görüntüsünde yeni sürümün görüntülendiğine dikkat etmelisiniz. ACR Görevler temel görüntüde yaptığınız değişikliği almış ve uygulama görüntünüzü otomatik olarak yeniden oluşturmuştur.

Kapsayıcıyı durdurmak ve kaldırmak için aşağıdaki komutu çalıştırın:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, temel görüntü güncelleştirildiğinde kapsayıcı görüntü derlemelerini otomatik olarak tetiklemek üzere bir görevi kullanmayı öğrendiniz. Şimdi, tanımlı bir zamanlamaya göre görevleri nasıl tetikleyeceğinizi öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Bir görevi zamanlamaya göre çalıştırma](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-node]: https://hub.docker.com/_/node/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr
