---
title: Öğretici-çok adımlı görev iş akışı-Azure Container Registry görevler
description: Bu öğreticide, bir git deposuna kaynak kodu kaydederken, bulutta kapsayıcı görüntülerini derlemek, çalıştırmak ve göndermek üzere bir Azure Container Registry görevinin otomatik olarak tetiklenmesi hakkında bilgi edineceksiniz.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: dd7dd6f78087a40de93a5ab47936806076fc562a
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931440"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Öğretici: kaynak kodu kaydederken bulutta çok adımlı bir kapsayıcı iş akışını çalıştırın

[Hızlı bir göreve](container-registry-tutorial-quick-task.md)ek olarak ACR görevleri, kaynak kodu bir git deposuna kaydederken otomatik olarak tetikleyemeyen çok adımlı, çok Kapsayıcılı iş akışlarını destekler. 

Bu öğreticide, kaynak kodu kaydederken bir veya daha fazla kapsayıcı görüntüsünü oluşturan, çalıştıran ve bir kayıt defterine ileten çok adımlı görevleri tanımlamak için örnek YAML dosyalarını nasıl kullanacağınızı öğreneceksiniz. Kod işlemede yalnızca tek bir görüntü derlemesini otomatikleştiren bir görev oluşturmak için bkz. [öğretici: kaynak kodu kaydederken buluttaki kapsayıcı görüntüsü derlemelerini otomatikleştirme](container-registry-tutorial-build-task.md). ACR görevlerine genel bakış için bkz. [ACR görevleri ile işletim sistemi ve çerçeve düzeltme eki uygulamayı otomatikleştirme](container-registry-tasks-overview.md),

Bu öğreticide:

> [!div class="checklist"]
> * YAML dosyası kullanarak çok adımlı bir görev tanımlama
> * Görev oluşturma
> * İsteğe bağlı olarak, başka bir kayıt defterine erişim sağlamak için göreve kimlik bilgileri ekleyin
> * Görevi test etme
> * Görev durumunu görüntüleme
> * Kod işlemesi ile görevi tetikleme

Bu öğreticide, [önceki öğreticide](container-registry-tutorial-quick-task.md) yer alan adımları zaten tamamladığınız varsayılır. Henüz yapmadıysanız, devam etmeden önce önceki öğreticinin [Önkoşullar](container-registry-tutorial-quick-task.md#prerequisites) bölümündeki adımları tamamlayın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLı 'yı yerel olarak kullanmak istiyorsanız, [az Login][az-login]Ile Azure CLI sürüm **2.0.62** veya sonraki bir sürümünün yüklü ve oturum açmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Çok adımlı bir görev oluşturma

ACR görevlerinin kayıt durumunu okumasını ve bir depoda Web kancaları oluşturmasını sağlamak için gerekli adımları tamamladığınıza göre, bir kapsayıcı görüntüsünü oluşturma, çalıştırma ve göndermeyi tetikleyen çok adımlı bir görev oluşturun.

### <a name="yaml-file"></a>YAML dosyası

Bir [YAML dosyasında](container-registry-tasks-reference-yaml.md)çok adımlı bir görev için adımları tanımlarsınız. Bu öğreticinin ilk örnek çoklu adım görevi, Klonladığınız GitHub deposunun kökündeki `taskmulti.yaml`dosya içinde tanımlanmıştır:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Bu çok adımlı görev şunları yapar:

1. Çalışma dizininde Dockerfile 'dan bir görüntü oluşturmak için bir `build` adımı çalıştırır. Görüntüde `Run.Registry`, görevin çalıştırıldığı kayıt defteri ve benzersiz bir ACR görevi çalıştırma KIMLIĞIYLE etiketlendi. 
1. Görüntüyü geçici bir kapsayıcıda çalıştırmak için bir `cmd` adımı çalıştırır. Bu örnek, arka planda uzun süre çalışan bir kapsayıcı başlatır ve kapsayıcı KIMLIĞINI döndürür, sonra kapsayıcıyı sonlandırır. Gerçek dünyada bir senaryoda, doğru çalıştığından emin olmak için çalışan kapsayıcıyı test etme adımlarını dahil edebilirsiniz.
1. `push` adımında, çalışma kayıt defterine oluşturulan görüntüyü iter.

### <a name="task-command"></a>Görev komutu

İlk olarak, bu kabuk ortam değişkenlerini ortamınıza uygun değerlerle doldurun. Bu adımın yapılması kesinlikle zorunlu değildir ancak bu öğreticideki çok satırlı Azure CLI komutlarını yürütmeyi biraz daha kolaylaştırır. Bu ortam değişkenlerini doldurmazsanız, her değeri örnek komutlarda göründüğü her yerde el ile değiştirmelisiniz.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Şimdi aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek görevi oluşturun:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Bu görev, `--context`tarafından belirtilen depodaki *ana* dala herhangi bir zaman kodun teslim olduğunu belirtir, ACR görevleri bu daldaki koddan çok adımlı görevi çalıştıracaktır. Depo kökünden `--file` tarafından belirtilen YAML dosyası adımları tanımlar. 

Başarılı bir [az ACR görev oluştur][az-acr-task-create] komutunun çıktısı aşağıdakine benzer:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Çok adımlı iş akışını test etme

Çok adımlı görevi test etmek için [az ACR Task Run][az-acr-task-run] komutunu yürüterek el ile tetikleyin:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Varsayılan olarak, `az acr task run` komutunu yürüttüğünüzde komut, günlük çıktısını konsolunuza akışla aktarır. Çıktı, görev adımlarının her birini çalıştırmanın ilerlemesini gösterir. Aşağıdaki çıktı, önemli adımları göstermek için yoğunlaştırılmış.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>İşleme ile derleme tetikleme

Görevi el ile çalıştırarak test ettikten sonra, bir kaynak kodu değişikliği ile otomatik olarak tetikleyin.

İlk olarak, [deponun][sample-repo]yerel kopyasını içeren dizinde olduğunuzdan emin olun:

```azurecli-interactive
cd acr-build-helloworld-node
```

Ardından, yeni bir dosya oluşturmak, işlemek ve GitHub üzerindeki depo çatalınıza göndermek için aşağıdaki komutları yürütün:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

`git push` komutunu yürüttüğünüzde GitHub kimlik bilgilerinizi sağlamanız istenebilir. GitHub kullanıcı adınızı sağlayın ve parola için daha önce oluşturduğunuz kişisel erişim belirtecini (PAT) girin.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Deponuza bir kayıt gönderdikten sonra ACR görevleri tarafından oluşturulan Web kancası ateşlenir ve Azure Container Registry görevi kapanır. Derlemenin ilerleme durumunu doğrulamak ve izlemek için o anda devam eden görevin günlüklerini görüntüleyin:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Çıktı aşağıdakine benzer ve o anda yürütülen (veya son yürütülen) görevi gösterir:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Derlemeleri listeleme

Kayıt defteriniz için ACR görevlerinin tamamladığı görev çalıştırmalarının listesini görmek için [az ACR Task List-çalıştırmaları][az-acr-task-list-runs] komutunu çalıştırın:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Komut çıktısı aşağıdakine benzer şekilde görünmelidir. ACR Görevlerinin yürüttüğü çalıştırmalar gösterilir ve en son görev için TRIGGER sütununda "Git İşleme" ifadesi görünür:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Birden çok kayıt defteri çoklu adım görevi oluşturma

ACR görevleri varsayılan olarak, görevin çalıştığı kayıt defterinden görüntü gönderme veya çekme için izinlere sahiptir. Çalışma kayıt defterine ek olarak bir veya daha fazla kayıt defteri hedefleyen çok adımlı bir görev çalıştırmak isteyebilirsiniz. Örneğin, bir kayıt defterinde görüntü oluşturmanız ve bir üretim sisteminin eriştiği ikinci bir kayıt defterinde farklı etiketlere sahip görüntüleri depolamanız gerekebilir. Bu örnek, böyle bir görevin nasıl oluşturulacağını ve başka bir kayıt defteri için kimlik bilgileri nasıl sağlayakullanacağınızı gösterir.

Zaten ikinci bir kayıt defteriniz yoksa, bu örnek için bir tane oluşturun. Bir kayıt defterine ihtiyacınız varsa, [önceki öğreticiye](container-registry-tutorial-quick-task.md) veya [Hızlı Başlangıç: Azure CLI kullanarak kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md) bölümüne bakın.

Görevi oluşturmak için, *mycontainerregistrydate.azurecr.io* biçimindeki kayıt defteri oturum açma sunucusunun adı gerekir (tümü küçük harf). Bu örnekte, derleme tarihine göre etiketlendirilmiş resimleri depolamak için ikinci kayıt defteri kullanılır.

### <a name="yaml-file"></a>YAML dosyası

Bu öğreticinin ikinci örnek çoklu adım görevi, Klonladığınız GitHub deposunun kökündeki `taskmulti-multiregistry.yaml`dosya içinde tanımlanmıştır:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Bu çok adımlı görev şunları yapar:

1. Çalışma dizininde Dockerfile 'dan görüntü oluşturmak için iki `build` adımı çalıştırır:
    * İlki `Run.Registry`, görevin çalıştırıldığı kayıt defteri ve ACR görevlerinin çalışma KIMLIĞIYLE etiketlendi. 
    * İkincisi, görevi oluştururken ayarladığınız `regDate`değeri tarafından tanımlanan kayıt defterini hedefler (veya `az acr task create`'e geçirilmiş bir dış `values.yaml` dosyası aracılığıyla sağlama). Bu görüntü, çalışma tarihi ile etiketlendi.
1. Oluşturulan kapsayıcılardan birini çalıştırmak için bir `cmd` adımı çalıştırır. Bu örnek, arka planda uzun süre çalışan bir kapsayıcı başlatır ve kapsayıcı KIMLIĞINI döndürür, sonra kapsayıcıyı sonlandırır. Gerçek dünyada bir senaryoda, çalışan bir kapsayıcıyı doğru çalıştığından emin olmak için test edebilirsiniz.
1. `push` adımında, oluşturulan görüntüleri, birincisini çalıştırılan kayıt defterine, ikincisi ise `regDate`tarafından tanımlanan kayıt defterine iter.

### <a name="task-command"></a>Görev komutu

Daha önce tanımlanan kabuk ortam değişkenlerini kullanarak, aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek görevi oluşturun. Kayıt defterinizin adını *mycontainerregistrydate*için yerine koyun.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Görev kimlik bilgisi ekle

`regDate`değeri tarafından tanımlanan kayıt defterine görüntü göndermek için [az ACR Task Credential Add][az-acr-task-credential-add] komutunu kullanarak bu kayıt defteri için oturum açma kimlik bilgilerini göreve ekleyin.

Bu örnekte, *Acrpush* rolü kapsamındaki kayıt defterine erişimi olan bir [hizmet sorumlusu](container-registry-auth-service-principal.md) oluşturmanız önerilir. Hizmet sorumlusu oluşturmak için, bu [Azure CLI betiğine](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)bakın.

Aşağıdaki `az acr task credential add` komutunda hizmet sorumlusu uygulama KIMLIĞINI ve parolasını geçirin:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLı, eklediğiniz kayıt defteri oturum açma sunucusunun adını döndürür.

### <a name="test-the-multi-step-workflow"></a>Çok adımlı iş akışını test etme

Yukarıdaki örnekte olduğu gibi, çok adımlı görevi test etmek için [az ACR Task Run][az-acr-task-run] komutunu yürüterek el ile tetikleyin. Görevi git deposuna yapılan bir işlemeye tetiklemek için, bir [oluşturma ile derleme tetikleme](#trigger-a-build-with-a-commit)bölümüne bakın.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Varsayılan olarak, `az acr task run` komutunu yürüttüğünüzde komut, günlük çıktısını konsolunuza akışla aktarır. Daha önce olduğu gibi, çıkış, görev adımlarının her birini çalıştırmanın ilerlemesini gösterir. Çıkış, önemli adımları göstermek için yoğunlaştırılmış.

Çıktı:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir git deposuna kaynak kodu kaydederken otomatik olarak tetiklenen çok adımlı, çok Kapsayıcılı görevler oluşturmayı öğrendiniz. Paralel ve bağımlı adım yürütmesi dahil olmak üzere çok adımlı görevlerin gelişmiş özellikleri için [ACR görevlerine YAML başvurusu](container-registry-tasks-reference-yaml.md)' na bakın. Bir kapsayıcı görüntüsünün temel görüntüsü güncelleştirildiğinde derlemeleri tetikleyen görevler oluşturmak için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Temel görüntü güncelleştirmesi ile derlemeleri otomatikleştirme](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
