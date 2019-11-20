---
title: Öğretici-temel görüntü güncelleştirmesinde görüntü oluşturmayı Tetikle-Azure Container Registry
description: Bu öğreticide, bir temel görüntü güncelleştirilirken buluttaki kapsayıcı görüntüsü yapılarını otomatik olarak tetiklemek üzere bir Azure Container Registry görevinin nasıl yapılandırılacağını öğreneceksiniz.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 04a1d2c6adf3f7a09e239a9c14990c2e3c6b0425
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148796"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Öğretici: Azure Container Registry 'de temel görüntü güncelleştirildiği zaman kapsayıcı görüntüsü derlemelerini otomatikleştirin 

ACR Görevleri, kapsayıcının temel görüntüsü güncelleştirildiğinde örneğin temel görüntülerinizden birinde işletim sistemine ve uygulama çerçevesine yama uyguladığınızda otomatik derleme yürütmeyi destekler. Bu öğreticide, ACR Görevlerinde bir kapsayıcı temel görüntüsü kayıt defterinize gönderildiğinde bulutta bir görev tetikleyen derleme görevini oluşturmayı öğreneceksiniz.

Bu öğreticide, serinin son kısmı:

> [!div class="checklist"]
> * Temel görüntü oluşturma
> * Uygulama görüntüsü derleme görevi oluşturma
> * Uygulama görüntüsü görevini tetiklemek için temel görüntüyü güncelleştirme
> * Tetiklenen görevi görüntüleme
> * Güncelleştirilmiş uygulama görüntüsünü doğrulama

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI’yı yerel olarak kullanmak istiyorsanız Azure CLI **2.0.46** veya sonraki bir sürüm yüklü olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek][azure-cli].

## <a name="prerequisites"></a>Önkoşullar

### <a name="complete-the-previous-tutorials"></a>Önceki öğreticileri tamamlama

Bu öğreticide, serinin ilk iki öğreticisindeki adımları zaten tamamladığınız ve şu işlemleri yaptığınız varsayılır:

* Azure kapsayıcı kayıt defteri oluşturma
* Örnek deponun çatalını oluşturma
* Örnek depoyu kopyalama
* GitHub kişisel erişim belirteci oluşturma

Henüz yapmadıysanız, devam etmeden önce ilk iki öğreticiyi tamamlayın:

[Azure Container Registry Görevleri ile bulutta kapsayıcı görüntüleri derleme](container-registry-tutorial-quick-task.md)

[Azure Container Registry Görevleri ile kapsayıcı görüntüsü derlemelerini otomatik hale getirme](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Ortamı yapılandırma

Bu kabuk ortam değişkenlerini ortamınıza uygun değerlerle doldurun. Bu adımın yapılması kesinlikle zorunlu değildir ancak bu öğreticideki çok satırlı Azure CLI komutlarını yürütmeyi biraz daha kolaylaştırır. Bu ortam değişkenlerini doldurmazsanız her değeri, örnek komutlarda her göründükleri durumda el ile değiştirmeniz gerekir.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Temel görüntüler

Çoğu kapsayıcı görüntüsünü tanımlayan Dockerfile'lar, temel alınan ve çoğunlukla *temel görüntü* olarak adlandırılan bir üst görüntü belirtir. Temel görüntüler genellikle, kapsayıcı katmanlarının geri kalanının uygulandığı [alp Linux][base-alpine] veya [Windows nano sunucu][base-windows]gibi işletim sistemini içerir. Ayrıca, [Node. js][base-node] veya [.NET Core][base-dotnet]gibi uygulama çerçeveleri de içerebilir.

### <a name="base-image-updates"></a>Temel görüntü güncelleştirmeleri

Temel görüntü çoğunlukla, görüntüdeki işletim sistemi veya çerçevenin yeni özelliklerini veya geliştirmelerini içermesi için görüntü bakımcısı tarafından güncelleştirilir. Temel görüntüyü güncelleştirmenin bir diğer yaygın nedeni de güvenlik yamalarıdır.

Temel görüntü güncelleştirildiğinde, yeni özellik ve düzeltmelerin eklenmesi için kayıt defterinizde bulunan ve bu temel görüntüye dayanan tüm kapsayıcı görüntülerini yeniden oluşturmanız gerektiği belirtilir. ACR Görevleri, kapsayıcının temel görüntüsü güncelleştirildiğinde sizin için görüntüleri otomatik olarak oluşturma özelliğine sahiptir.

### <a name="tasks-triggered-by-a-base-image-update"></a>Bir temel görüntü güncelleştirmesi tarafından tetiklenen görevler

* Bir Dockerfile dosyasındaki görüntü yapıları için ACR görevi aşağıdaki konumlarda temel görüntülerde bağımlılıkları algılar:

  * Görevin çalıştığı aynı Azure Container kayıt defteri
  * Aynı bölgedeki başka bir Azure Kapsayıcı kayıt defteri 
  * Docker Hub 'da ortak depo 
  * Microsoft Container Registry genel depo

   `FROM` bildiriminde belirtilen temel görüntü bu konumlardan birinde yer alıyorsa ACR görevi, temel aldığı her seferinde görüntünün yeniden oluşturulmasını sağlamak için bir kanca ekler.

* Şu anda ACR görevleri yalnızca uygulama (*çalışma zamanı*) görüntüleri için temel görüntü güncelleştirmelerini izler. Çok aşamalı Dockerfiles 'da kullanılan ara (*buildtime*) görüntüleri için temel görüntü güncelleştirmelerini izlemez.  

* [Az ACR Task Create][az-acr-task-create] komutuyla bir ACR görevi oluşturduğunuzda, varsayılan olarak görev, bir temel görüntü güncelleştirmesi tarafından tetikleyici için *etkinleştirilmiştir* . Diğer bir deyişle, `base-image-trigger-enabled` özelliği true olarak ayarlanır. Bir görevde bu davranışı devre dışı bırakmak istiyorsanız, özelliği false olarak güncelleştirin. Örneğin, şu [az ACR Task Update][az-acr-task-update] komutunu çalıştırın:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Bir ACR görevinin, kendi temel görüntüsünü içeren bir kapsayıcı görüntüsünün bağımlılıklarını belirlemesini ve izlemesini sağlamak için, önce görevi **en az bir kez**tetiklemeniz gerekir. Örneğin, [az ACR Task Run][az-acr-task-run] komutunu kullanarak görevi el ile tetikleyin.

* Temel görüntü güncelleştirmesinde bir görevi tetiklemek için, temel görüntünün `node:9-alpine`gibi *kararlı* bir etiketi olması gerekir. Bu etiketleme, işletim sistemi ve çerçeve düzeltme ekleriyle en son kararlı sürüme güncelleştirilmiş bir temel görüntü için tipik bir noktadır. Temel görüntü yeni bir sürüm etiketiyle güncelleştirilirse bir görevi tetiklemez. Görüntü etiketleme hakkında daha fazla bilgi için [en iyi yöntemler Kılavuzu](container-registry-image-tag-version.md)' na bakın. 

### <a name="base-image-update-scenario"></a>Temel görüntü güncelleştirme senaryosu

Bu öğreticide, bir temel görüntü güncelleştirme senaryosunda size yol gösterilir. [Kod örneği][code-sample] , Iki Dockerfiles içerir: bir uygulama görüntüsü ve temeli olarak belirttiği bir görüntü. Aşağıdaki bölümlerde, temel görüntünün yeni bir sürümü aynı kapsayıcı kayıt defterine gönderildiğinde, uygulama görüntüsünün derlemesini otomatik olarak tetikleyen bir ACR görevi oluşturacaksınız.

[Dockerfile-App][dockerfile-app]: temel aldığı Node. js sürümünü görüntüleyen statik bir Web sayfası işleyen küçük bir Node. js web uygulaması. Sürüm dizesinin simülasyonu yapılır ve bu, temel görüntüde tanımlanan `NODE_VERSION` ortam değişkeninin içeriğini görüntüler.

[Dockerfile-Base][dockerfile-base]: `Dockerfile-app` temel olarak belirttiği görüntü. Kendisi bir [düğüm][base-node] görüntüsünü temel alır ve `NODE_VERSION` ortam değişkenini içerir.

Aşağıdaki bölümlerde bir görev oluşturacak, temel görüntü Dockerfile içinde `NODE_VERSION` değerini güncelleştirecek ve sonra da ACR Görevlerini kullanarak temel görüntü oluşturacaksınız. ACR görevi yeni temel görüntüyü kayıt defterinize gönderdikten sonra, uygulama görüntüsünün derlemesini otomatik olarak tetikler. İsteğe bağlı olarak, derleme görüntülerinde farklı sürüm dizeleri görmek için uygulama kapsayıcısı görüntüsünü yerel olarak çalıştırırsınız.

Bu öğreticide, ACR göreviniz bir Dockerfile dosyasında belirtilen bir uygulama kapsayıcısı görüntüsünü oluşturur ve gönderir. ACR görevleri, birden çok kapsayıcıyı oluşturma, gönderme ve isteğe bağlı olarak test etme adımlarını tanımlamak için bir YAML dosyası kullanarak [çok adımlı görevler](container-registry-tasks-multi-step.md)de çalıştırabilir.

## <a name="build-the-base-image"></a>Temel görüntü oluşturma

Başlangıç olarak ACR Görevleri *hızlı görevi* ile temel görüntüyü oluşturun. Serinin [ilk öğreticisinde](container-registry-tutorial-quick-task.md) açıklandığı gibi, bu işlem yalnızca görüntüyü oluşturmakla kalmaz, oluşturma başarılı olduysa bunu kapsayıcınızın kayıt defterine de gönderir.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Görev oluşturma

Sonra, [az ACR Task Create][az-acr-task-create]komutuyla bir görev oluşturun:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Daha önce `az acr build-task` komutuyla önizleme sırasında görevler oluşturduysanız, bu görevlerin [az ACR Task][az-acr-task] komutu kullanılarak yeniden oluşturulması gerekir.

Bu görev, [önceki öğreticide](container-registry-tutorial-build-task.md) oluşturulan hızlı göreve benzer. ACR Görevlerine, işlemeler `--context` tarafından belirtilen depoya gönderildiğinde bir görüntü derlemesi tetiklemesini bildirir. Önceki öğreticide görüntüyü oluşturmak için kullanılan Dockerfile, genel bir temel görüntü (`FROM node:9-alpine`), bu görevde Dockerfile, [dockerfile-App][dockerfile-app], aynı kayıt defterindeki bir temel görüntüyü belirtir:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Bu yapılandırma, bu öğreticide daha sonra temel görüntüde bir çerçeve düzeltme ekinin benzetimini yapmayı kolaylaştırır.

## <a name="build-the-application-container"></a>Uygulama kapsayıcısını oluşturma

Görevi el ile tetiklemek ve uygulama görüntüsünü derlemek için [az ACR görev çalıştırmasını][az-acr-task-run] kullanın. Bu adım, görevin temel görüntüde uygulama görüntüsünün bağımlılığını takip edilmesini sağlar.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Görev tamamlandıktan sonra, aşağıdaki isteğe bağlı adımı tamamlamak istiyorsanız **Run ID** (örneğin, "da6") değerini not alın.

### <a name="optional-run-application-container-locally"></a>İsteğe bağlı: Uygulama kapsayıcısını yerel olarak çalıştırma

Cloud Shell'de değil de yerel olarak çalışıyorsanız ve Docker'ı yüklediyseniz, temel görüntüsünü oluşturmadan önce web tarayıcısında işlenen uygulamayı görmek için kapsayıcıyı çalıştırın. Cloud Shell kullanıyorsanız bu bölümü atlayın (Cloud Shell `az acr login` veya `docker run` komutunu desteklemez).

İlk olarak, [az ACR oturum açma][az-acr-login]ile kapsayıcı kayıt defterinizde kimlik doğrulaması yapın:

```azurecli
az acr login --name $ACR_NAME
```

Şimdi `docker run` ile kapsayıcıyı yerel olarak çalıştırın. **\<run-id\>** değerini önceki adımdaki çıktıda bulunan Run ID değeriyle (örneğin, "da6") değiştirin. Bu örnek, kapsayıcıyı `myapp` adlandırır ve `--rm` parametresini, durdurduğunuzda kapsayıcıyı kaldırmak için içerir.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Tarayıcınızda `http://localhost:8080` adresine gidin; aşağıdakine benzer biçimde web sayfasında işlenmiş Node.js sürüm numarasını görüyor olmalısınız. Sonraki adımlardan birinde, sürüm dizesine bir "a" ekleyerek sürümü yükseltirsiniz.

![Tarayıcıda işlenen örnek uygulamanın ekran görüntüsü][base-update-01]

Kapsayıcıyı durdurmak ve kaldırmak için aşağıdaki komutu çalıştırın:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Derlemeleri listeleme

Ardından, [az ACR Task List-çalıştırmaları][az-acr-task-list-runs] komutunu kullanarak kayıt defteriniz için ACR görevlerinin tamamladığı görevi listeleyin:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Önceki öğreticiyi tamamladıysanız (ve kayıt defterini silmediyseniz), aşağıdakine benzer bir çıkış görüyor olmalısınız. Sonraki bölümde temel görüntüyü güncelleştirdikten sonra çıkışı karşılaştırabilmek için görev sayısını ve en son RUN ID değerini not alın.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Temel görüntüyü güncelleştirme

Burada, temel görüntüde bir çerçeve yamasının simülasyonunu yaparsınız. **Dockerfile-base** öğesini düzenleyin ve `NODE_VERSION` içinde tanımlanan sürüm numarasından sonra bir "a" ekleyin:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Değiştirilmiş temel görüntüyü derlemek için bir hızlı görev çalıştırın. Çıkıştaki **Run ID** değerini not alın.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Derleme tamamlandıktan ve ACR görevi yeni temel görüntüyü kayıt defterinize gönderdikten sonra, uygulama görüntüsünün derlemesini tetikler. Daha önce oluşturduğunuz görevin uygulama görüntüsü derlemeyi tetiklemesi birkaç dakika sürebilir çünkü yeni derlenen ve gönderilen temel görüntüyü algılaması gerekir.

## <a name="list-updated-build"></a>Güncelleştirilmiş derlemeyi listeleme

Artık temel görüntüyü güncelleştirdiğinize göre, görev çalıştırmalarınızı bir kez daha listeleyip önceki listeyle bunu karşılaştırın. İlk seferinde çıkış farklı görünmüyorsa, yeni görev çalıştırmasının listede yer aldığını görmek için komutu düzenli aralıklarla çalıştırın.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Çıktı aşağıdakine benzer olacaktır. Son yürütülen derlemenin TRIGGER değeri "Image Update" olmalıdır; bu değer, görevin temel görüntünüzün hızlı görevi ile başlatıldığını gösterir.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Yeni oluşturulan kapsayıcıyı çalıştırıp güncelleştirilmiş sürüm numarasını görmek için aşağıdaki isteğe bağlı adımı uygulamak isterseniz, Görüntü Güncelleştirmesi ile tetiklenen derlemenin **RUN ID** değerini (önceki çıktıda "da8") not alın.

### <a name="optional-run-newly-built-image"></a>İsteğe bağlı: Yeni oluşturulan görüntüyü çalıştırma

Cloud Shell'de değil de yerel olarak çalışıyorsanız ve Docker'ı yüklediyseniz, derlemesi tamamlandıktan sonra yeni uygulama görüntüsünü çalıştırın. `<run-id>` değerinin yerine önceki adımda aldığınız RUN ID değerini koyun. Cloud Shell kullanıyorsanız bu bölümü atlayın (Cloud Shell `docker run` komutunu desteklemez).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Tarayıcınızda http://localhost:8081 adresine gidin; web sayfasında güncelleştirilmiş Node.js sürüm numarasını ("a" ile) görüyor olmalısınız:

![Tarayıcıda işlenen örnek uygulamanın ekran görüntüsü][base-update-02]

**Temel** görüntünüzü yeni sürüm numarasıyla güncelleştirdiğinize ama son oluşturulan **uygulama** görüntüsünde yeni sürümün görüntülendiğine dikkat etmelisiniz. ACR Görevler temel görüntüde yaptığınız değişikliği almış ve uygulama görüntünüzü otomatik olarak yeniden oluşturmuştur.

Kapsayıcıyı durdurmak ve kaldırmak için aşağıdaki komutu çalıştırın:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcı kayıt defteri, kapsayıcı örneğin, anahtar kasası ve hizmet sorumlusu dahil olmak üzere bu öğretici serisinde oluşturduğunuz tüm kaynakları kaldırmak için aşağıdaki komutları gönderin:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, temel görüntü güncelleştirildiğinde kapsayıcı görüntü derlemelerini otomatik olarak tetiklemek üzere bir görevi kullanmayı öğrendiniz. Şimdi, tanımlı bir zamanlamaya göre görevleri nasıl tetikleyeceğinizi öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Bir görevi zamanlamaya göre çalıştırma](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
