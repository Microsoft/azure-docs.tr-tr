---
title: Öğretici-çok adımlı ACR görevi
description: Bu öğreticide, bir git deposuna kaynak kodu kaydederken, bulutta kapsayıcı görüntülerini derlemek, çalıştırmak ve göndermek üzere bir Azure Container Registry görevinin otomatik olarak tetiklenmesi hakkında bilgi edineceksiniz.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 6d6ca8c41ce808df4b32bc10a7b537ffae809b3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780686"
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

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Çok adımlı bir görev oluşturma

ACR görevlerinin kayıt durumunu okumasını ve bir depoda Web kancaları oluşturmasını sağlamak için gerekli adımları tamamladığınıza göre, bir kapsayıcı görüntüsünü oluşturma, çalıştırma ve göndermeyi tetikleyen çok adımlı bir görev oluşturun.

### <a name="yaml-file"></a>YAML dosyası

Bir [YAML dosyasında](container-registry-tasks-reference-yaml.md)çok adımlı bir görev için adımları tanımlarsınız. Bu öğreticinin ilk örnek çoklu adım görevi `taskmulti.yaml` , Klonladığınız GitHub deposunun kökündeki dosyada tanımlanmıştır:

```yml
version: v1.1.0
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

1. `build`Çalışma dizininde Dockerfile 'dan görüntü oluşturmak için bir adım çalıştırır. Görüntüde, `Run.Registry` görevin çalıştırıldığı kayıt defteri ve benzersiz BIR ACR görevi çalıştırma kimliğiyle etiketlendi. 
1. `cmd`Görüntüyü geçici bir kapsayıcıda çalıştırmak için bir adım çalıştırır. Bu örnek, arka planda uzun süre çalışan bir kapsayıcı başlatır ve kapsayıcı KIMLIĞINI döndürür, sonra kapsayıcıyı sonlandırır. Gerçek dünyada bir senaryoda, doğru çalıştığından emin olmak için çalışan kapsayıcıyı test etme adımlarını dahil edebilirsiniz.
1. Bir `push` adımda, çalışma kayıt defterine oluşturulan görüntüyü iter.

### <a name="task-command"></a>Görev komutu

İlk olarak, bu kabuk ortam değişkenlerini ortamınıza uygun değerlerle doldurun. Bu adımın yapılması kesinlikle zorunlu değildir ancak bu öğreticideki çok satırlı Azure CLI komutlarını yürütmeyi biraz daha kolaylaştırır. Bu ortam değişkenlerini doldurmazsanız, her değeri örnek komutlarda göründüğü her yerde el ile değiştirmelisiniz.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Şimdi aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek görevi oluşturun:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Bu görev, tarafından belirtilen depodaki *ana* dala herhangi bir zaman kodu taahhüt olduğunu belirtir `--context` , ACR görevleri bu daldaki koddan çok adımlı görevi çalıştırır. Depo kökünden tarafından belirtilen YAML dosyası `--file` adımları tanımlar. 

Başarılı bir [az acr task create][az-acr-task-create] komutundaki çıktı aşağıdakilere benzer:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
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
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git#main",
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
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git#main",
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

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>İşleme ile derleme tetikleme

Görevi el ile çalıştırarak test ettikten sonra, bir kaynak kodu değişikliği ile otomatik olarak tetikleyin.

İlk olarak, [depo][sample-repo] yerel kopyanızı içeren dizinde olduğunuzdan emin olun:

```console
cd acr-build-helloworld-node
```

Ardından, yeni bir dosya oluşturmak, işlemek ve GitHub üzerindeki depo çatalınıza göndermek için aşağıdaki komutları yürütün:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin main
```

`git push` komutunu yürüttüğünüzde GitHub kimlik bilgilerinizi sağlamanız istenebilir. GitHub kullanıcı adınızı sağlayın ve parola için daha önce oluşturduğunuz kişisel erişim belirtecini (PAT) girin.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Deponuza bir kayıt gönderdikten sonra ACR görevleri tarafından oluşturulan Web kancası ateşlenir ve Azure Container Registry görevi kapanır. Derlemenin ilerleme durumunu doğrulamak ve izlemek için o anda devam eden görevin günlüklerini görüntüleyin:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Çıktı aşağıdakine benzer ve o anda yürütülen (veya son yürütülen) görevi gösterir:

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Derlemeleri listeleme

[az acr task list-runs][az-acr-task-list-runs] komutunu çalıştırarak ACR Görevlerinin kayıt defteriniz için tamamladığı çalıştırmaları listeleyebilirsiniz:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Komut çıktısı aşağıdakine benzer şekilde görünmelidir. ACR Görevlerinin yürüttüğü çalıştırmalar gösterilir ve en son görev için TRIGGER sütununda "Git İşleme" ifadesi görünür:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Birden çok kayıt defteri çoklu adım görevi oluşturma

ACR görevleri varsayılan olarak, görevin çalıştığı kayıt defterinden görüntü gönderme veya çekme için izinlere sahiptir. Çalışma kayıt defterine ek olarak bir veya daha fazla kayıt defteri hedefleyen çok adımlı bir görev çalıştırmak isteyebilirsiniz. Örneğin, bir kayıt defterinde görüntü oluşturmanız ve bir üretim sisteminin eriştiği ikinci bir kayıt defterinde farklı etiketlere sahip görüntüleri depolamanız gerekebilir. Bu örnek, böyle bir görevin nasıl oluşturulacağını ve başka bir kayıt defteri için kimlik bilgileri nasıl sağlayakullanacağınızı gösterir.

Zaten ikinci bir kayıt defteriniz yoksa, bu örnek için bir tane oluşturun. Bir kayıt defterine ihtiyacınız varsa, [önceki öğreticiye](container-registry-tutorial-quick-task.md) veya [Hızlı Başlangıç: Azure CLI kullanarak kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md) bölümüne bakın.

Görevi oluşturmak için, *mycontainerregistrydate.azurecr.io* biçimindeki kayıt defteri oturum açma sunucusunun adı gerekir (tümü küçük harf). Bu örnekte, derleme tarihine göre etiketlendirilmiş resimleri depolamak için ikinci kayıt defteri kullanılır.

### <a name="yaml-file"></a>YAML dosyası

Bu öğreticinin ikinci örnek çoklu adım görevi `taskmulti-multiregistry.yaml` , Klonladığınız GitHub deposunun kökündeki dosyada tanımlanmıştır:

```yml
version: v1.1.0
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

1. `build`Çalışma dizininde Dockerfile 'dan görüntü oluşturmak için iki adımı çalıştırır:
    * Birincisi, `Run.Registry` görevin çalıştırıldığı kayıt defteri ve ACR görevlerinin çalıştırma kimliğiyle etiketlendi. 
    * İkincisi, `regDate` görevi oluştururken ayarladığınız (veya bir dış dosya ile bir dış dosya aracılığıyla sağladığınız) değeri tarafından tanımlanan kayıt defterini hedefler `values.yaml` `az acr task create` . Bu görüntü, çalışma tarihi ile etiketlendi.
1. `cmd`Oluşturulan kapsayıcılardan birini çalıştırmak için bir adım çalıştırır. Bu örnek, arka planda uzun süre çalışan bir kapsayıcı başlatır ve kapsayıcı KIMLIĞINI döndürür, sonra kapsayıcıyı sonlandırır. Gerçek dünyada bir senaryoda, çalışan bir kapsayıcıyı doğru çalıştığından emin olmak için test edebilirsiniz.
1. Bir `push` adımda oluşturulan görüntüleri, birincisini çalıştırılan kayıt defterine, ikincisi ise tarafından tanımlanan kayıt defterine iter `regDate` .

### <a name="task-command"></a>Görev komutu

Daha önce tanımlanan kabuk ortam değişkenlerini kullanarak, aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek görevi oluşturun. Kayıt defterinizin adını *mycontainerregistrydate* için yerine koyun.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Görev kimlik bilgisi ekle

' In değeri tarafından tanımlanan kayıt defterine görüntü göndermek için `regDate` , bu kayıt defteri için oturum açma kimlik bilgilerini göreve eklemek üzere [az ACR Task Credential Add][az-acr-task-credential-add] komutunu kullanın.

Bu örnekte, *Acrpush* rolü kapsamındaki kayıt defterine erişimi olan bir [hizmet sorumlusu](container-registry-auth-service-principal.md) oluşturmanızı öneririz; Böylece görüntüleri gönderme izinleri vardır. Hizmet sorumlusu oluşturmak için, bu [Azure CLI betiğine](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)bakın.

Aşağıdaki komutta hizmet sorumlusu uygulama KIMLIĞINI ve parolasını geçirin `az acr task credential add` . *Mycontainerregistrydate* oturum açma sunucusu adını ikinci kayıt defterinizin adıyla güncelleştirdiğinizden emin olun:

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

Çıkış:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
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
    tag: 15-alpine
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
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add    
[az-login]: /cli/azure/reference-index#az_login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
