---
title: Şablon ile hızlı görev Çalıştır
description: Bir Azure Resource Manager şablonu kullanarak bir görüntü oluşturmak için bir ACR görevi çalıştırmasını sıraya al
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781244"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Kaynak Yöneticisi şablonları kullanarak ACR görevlerini çalıştırma

[ACR görevleri](container-registry-tasks-overview.md) kapsayıcı görüntülerini kapsayıcı yaşam döngüsü genelinde yönetmenize ve değiştirmenize yardımcı olmak üzere Azure Container Registry içindeki bir özellik paketidir. 

Bu makalede, az [ACR Build][az-acr-build] komutunu kullanarak el ile oluşturabileceğiniz bir hızlı görev çalıştırmasını sıraya almak için Azure Resource Manager şablon örnekleri gösterilmektedir.

Bir görev çalıştırmasını sıraya almak için Kaynak Yöneticisi şablonu Otomasyon senaryolarında faydalıdır ve işlevselliğini genişletir `az acr build` . Örnek:

* Bir kapsayıcı kayıt defteri oluşturmak ve bir kapsayıcı görüntüsünü oluşturmak ve göndermek için bir görev çalıştırmasını hemen sıraya almak için şablon kullanma
* Azure kaynakları için yönetilen kimlik gibi bir hızlı görev çalıştırmasında kullanabileceğiniz ek kaynaklar oluşturun veya etkinleştirin

## <a name="limitations"></a>Sınırlamalar

* Bir GitHub deposu gibi bir uzak bağlamı, görev çalıştırınızın [kaynak konumu](container-registry-tasks-overview.md#context-locations) olarak belirtmeniz gerekir. Yerel bir kaynak bağlamı kullanamazsınız.
* Yönetilen bir kimlik kullanılarak görev çalıştırmaları için yalnızca *Kullanıcı tarafından atanan* yönetilen kimliğe izin verilir.

## <a name="prerequisites"></a>Önkoşullar

* **GitHub hesabı** -henüz yoksa bir hesap oluşturun https://github.com . 
* **Çatal örnek deposu** -burada gösterilen görev örnekleri için GitHub Kullanıcı arabirimini kullanarak aşağıdaki örnek depoyu GitHub hesabınıza çatalla: https://github.com/Azure-Samples/acr-build-helloworld-node . Bu depo, küçük kapsayıcı görüntüleri oluşturmak için örnek Dockerfiles ve kaynak kodu içerir.

## <a name="example-create-registry-and-queue-task-run"></a>Örnek: kayıt defteri ve kuyruk görevi çalıştırma

Bu örnek bir kapsayıcı kayıt defteri oluşturmak ve bir görüntüyü oluşturup veren bir görev çalıştırmasını sıraya almak için [örnek bir şablon](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) kullanır. 

### <a name="template-parameters"></a>Şablon parametreleri

Bu örnek için aşağıdaki şablon parametreleri için değerler sağlayın:

|Parametre  |Değer  |
|---------|---------|
|registryName     |Oluşturulan kayıt defterinin benzersiz adı         |
|depo     |Derleme görevi için hedef depo        |
|taskRunName     |Resim etiketini belirten görev çalıştırmasının adı |
|sourceLocation     |Derleme görevinin uzak bağlamı, örneğin, https://github.com/Azure-Samples/acr-build-helloworld-node . Depo kökündeki Dockerfile, küçük bir Node.js Web uygulaması için kapsayıcı görüntüsü oluşturur. İsterseniz, depo çatalınızı derleme bağlamı olarak kullanın.         |

### <a name="deploy-the-template"></a>Şablonu dağıtma

[Az Deployment Group Create][az-deployment-group-create] komutuyla şablonu dağıtın. Bu örnek, *HelloWorld-node: TestRun* görüntüsünü, *mycontainerregistry* adlı bir kayıt defterine oluşturur ve gönderir.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Önceki komut, parametreleri komut satırına geçirir. İsterseniz bunları bir [Parametreler dosyasında](../azure-resource-manager/templates/parameter-files.md)geçirin.

### <a name="verify-deployment"></a>Dağıtımı doğrulama

Dağıtım başarıyla tamamlandıktan sonra, [az ACR Repository Show-Tags][az-acr-repository-show-tags]çalıştırarak görüntünün derlendiğini doğrulayın:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Çıkış:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Çalıştırma günlüğünü görüntüle

Görev çalıştıralım hakkındaki ayrıntıları görüntülemek için, çalıştırma günlüğünü görüntüleyin.

İlk olarak, [az ACR görev listesi-çalıştırmaları][az-acr-task-list-runs] Ile çalıştırma kimliğini alın
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

Çıkış şuna benzer olacaktır:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Çalışma KIMLIĞI için görev çalıştırma günlüklerini görüntülemek için [az ACR görev günlüklerini][az-acr-task-logs] çalıştırın, bu durumda *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

Çıktıda, görev çalıştırma günlüğü gösterilmektedir.

Azure portal görev çalıştırma günlüğünü de görüntüleyebilirsiniz. 

1. Kapsayıcı Kayıt defterinize gidin
2. **Hizmetler** altında **Görevler**  >  **çalıştırmalar**' ı seçin.
3. Çalıştırma KIMLIĞINI seçin (Bu durumda *CA1*). 

Portal, görev çalıştırma günlüğünü gösterir.

## <a name="example-task-run-with-managed-identity"></a>Örnek: yönetilen kimlikle görev çalıştırma

Kullanıcı tarafından atanan yönetilen kimlik sağlayan bir görev çalıştırmasını sıraya almak için [örnek bir şablon](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) kullanın. Görev çalıştırıldığında kimlik, başka bir Azure Container Registry 'den görüntü çekmek için kimliğini doğrular. 

Bu senaryo, [Azure tarafından yönetilen bir kimlik kullanılarak ACR görevinde çapraz kayıt defteri kimlik doğrulamasına](container-registry-tasks-cross-registry-authentication.md)benzer. Örneğin, bir kuruluş, birden çok geliştirme ekibinin eriştiği temel görüntülerle merkezi bir kayıt defteri tutabilir.

### <a name="prepare-base-registry"></a>Temel kayıt defterini hazırla

Tanıtım amacıyla, temel kayıt defteri olarak ayrı bir kapsayıcı kayıt defteri oluşturun ve Docker Hub 'ından bir Node.js temel görüntüsünü gönderin.

1. Temel görüntüleri depolamak için örneğin, *mybaseregner* gibi ikinci bir kapsayıcı kayıt defteri oluşturun.
1. `node:9-alpine`Resmi Docker Hub 'ından çekin, temel kayıt defteriniz için etiketleyin ve temel kayıt defterine gönderin:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Yeni Dockerfile oluştur

Temel görüntüyü taban Kayıt defterinizden çeken bir Dockerfile oluşturun. GitHub deposunun yerel çatalınızda aşağıdaki adımları gerçekleştirin, örneğin, `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. GitHub Kullanıcı arabiriminde **yeni dosya oluştur**' u seçin.
1. Dosyanızı *Dockerfile-test* olarak adlandırın ve aşağıdaki içeriği yapıştırın. *Mybaseregbakanlığı* için kayıt defteri adınızı değiştirin.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. **Yeni dosya Kaydet**' i seçin.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Temel kayıt defterine kimlik çekme izinleri verme

Yönetilen kimliğe, *mybaseregbakanlığı* temel kayıt defterinden çekme için izin verin.

Temel kayıt defterinin kaynak KIMLIĞINI almak ve bir değişkende depolamak için [az ACR Show][az-acr-show] komutunu kullanın:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Acrpull rolünün kimliğini temel kayıt defterine atamak için [az role atama Create][az-role-assignment-create] komutunu kullanın. Bu rol yalnızca kayıt defterinden görüntüleri çekmek için izinlere sahiptir.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Şablon parametreleri

Bu örnek için aşağıdaki şablon parametreleri için değerler sağlayın:

|Parametre  |Değer  |
|---------|---------|
|registryName     |Görüntünün oluşturulduğu kayıt defterinin adı  |
|depo     |Derleme görevi için hedef depo        |
|taskRunName     |Resim etiketini belirten görev çalıştırmasının adı |
|Useratandıdentity |Görevde etkin olan kullanıcı tarafından atanan kimliğin kaynak KIMLIĞI|
|Customregistryıdentity | Görevde etkin olan kullanıcı tarafından atanan kimliğin istemci KIMLIĞI, özel kayıt defteri ile kimlik doğrulaması için kullanılır |
|customRegistry |Görevde erişilen özel kayıt defterinin oturum açma sunucusu adı, örneğin, *mybaseregistry.azurecr.io*|
|sourceLocation     |Derleme görevinin uzak bağlamı; Örneğin, *https://github.com/ \<your-GitHub-ID\> /ACR-Build-HelloWorld-Node.* |
|dockerFilePath | Görüntüyü oluşturmak için kullanılan uzak bağlamdaki Dockerfile dosyasının yolu. |

### <a name="deploy-the-template"></a>Şablonu dağıtma

[Az Deployment Group Create][az-deployment-group-create] komutuyla şablonu dağıtın. Bu örnek, *HelloWorld-node: TestRun* görüntüsünü, *mycontainerregistry* adlı bir kayıt defterine oluşturur ve gönderir. Temel görüntü *mybaseregistry.azurecr.io*' dan çekilir.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Önceki komut, parametreleri komut satırına geçirir. İsterseniz bunları bir [Parametreler dosyasında](../azure-resource-manager/templates/parameter-files.md)geçirin.

### <a name="verify-deployment"></a>Dağıtımı doğrulama

Dağıtım başarıyla tamamlandıktan sonra, [az ACR Repository Show-Tags][az-acr-repository-show-tags]çalıştırarak görüntünün derlendiğini doğrulayın:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Çıkış:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Çalıştırma günlüğünü görüntüle

Çalıştırma günlüğünü görüntülemek için [önceki bölümdeki](#view-run-log)adımlara bakın.

## <a name="next-steps"></a>Sonraki adımlar

 * [ACR GitHub](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)deposunda diğer şablon örneklerine bakın.
 * Şablon özellikleri hakkında ayrıntılı bilgi için bkz. [görev çalıştırmaları](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) ve [Görevler](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)için şablon başvurusu.


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
