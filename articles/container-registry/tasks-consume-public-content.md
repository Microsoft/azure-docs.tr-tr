---
title: Ortak kayıt defteri içeriğini yönetmek için görev iş akışı
description: Özel bir Azure Container Registry 'de ortak görüntü içeriğini izlemek, yönetmek ve kullanmak için otomatik bir Azure Container Registry görevleri iş akışı oluşturun.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349291"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Azure Container Registry görevlerle ortak içeriği kullanma ve koruma

Bu makalede, ortak içeriği tüketerek ve korumanıza yardımcı olmak için Azure Container Registry bir örnek iş akışı sunulmaktadır:

1. Bağımlı ortak görüntülerin yerel kopyalarını içeri aktarın.
1. Genel görüntüleri güvenlik taraması ve işlevsel test aracılığıyla doğrulayın.
1. İç kullanım için resimleri özel kayıt defterlerine yükseltin.
1. Ortak içeriğe bağımlı uygulamalar için temel görüntü güncelleştirmelerini tetikler.
1. [Azure Container Registry görevleri](container-registry-tasks-overview.md) kullanarak bu iş akışını otomatikleştirin.

İş akışı aşağıdaki görüntüde özetlenmiştir:

![Ortak içerik Iş akışını kullanma](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

Geçitli içeri aktarma iş akışı, kuruluşunuzun dışarıdan yönetilen yapıtlardaki bağımlılıklarını yönetmeye yardımcı olur. Örneğin, [Docker Hub][docker-hub], [GCR][gcr], [Quay][quay], [GitHub Container Registry][ghcr], [Microsoft Container Registry][mcr], hatta diğer [Azure Container kayıt defterleri][acr]dahil ortak kayıt defterlerinden alınan görüntüler. 

Ortak içerikte bağımlılıklar tarafından tanıtılan riskler ve bunları azaltmak için Azure Container Registry kullanma hakkında arka plan için, bkz. [OCI kullanan ortak Içerik blog][oci-consuming-public-content] gönderisi ve [Azure Container Registry ortak içeriği yönetme](buffer-gate-public-content.md).

Bu yönergeyi tamamlamak için Azure Cloud Shell veya Azure CLı 'nın yerel bir yüklemesini kullanabilirsiniz. Azure CLı sürüm 2,10 veya sonraki bir sürümü önerilir. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-cli].

## <a name="scenario-overview"></a>Senaryoya genel bakış

![iş akışı bileşenlerini içeri aktar](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Bu izlenecek yol şu şekilde ayarlanır:

1. Şunları temsil eden üç **kapsayıcı kayıt defterleri**:
   * Temel görüntünün değiştirilmesini desteklemek için sanal bir [Docker Hub][docker-hub] ( `publicregistry` )
   * `contoso`Özel görüntüleri paylaşmak için ekip kayıt defteri ()
   * İçeri aktarılan ortak içerik için Şirket/takım paylaşılan kayıt defteri ( `baseartifacts` )
1. Her kayıt defterinde bir **ACR görevi** . Görevler:  
   1. Sanal ortak görüntü oluşturma `node`
   1. `node`Görüntüyü şirket/ekip paylaşılan kayıt defterine aktarın ve doğrulayın
   1. Görüntü oluşturma ve dağıtma `hello-world`
1. İçin yapılandırma dahil **ACR görev tanımları**:
1. Bir Anahtar Kasası işaretçisi olan **kayıt defteri kimlik bilgileri** koleksiyonu
1. Bir  `acr-task.yaml` Anahtar Kasası işaretçisi olan, içinde bulunan gizli dizileri koleksiyonu
1. Bir içinde kullanılan **yapılandırılmış değerler** koleksiyonu `acr-task.yaml`
1. Tüm gizli dizileri güvenli hale getirmek için bir **Azure Anahtar Kasası**
1. Yapı uygulamasını barındıran bir **Azure Container örneği** `hello-world`

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki adımlar, yönergede oluşturulan ve kullanılan kaynakların değerlerini yapılandırır.

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Ortamınıza özgü değişkenleri yapılandırın. Yanlışlıkla silme işlemini en aza indirmek için, kaynakların kendi kaynak grubundaki dayanıklı içeriğe yerleştirilmesi için en iyi uygulamaları izliyoruz. Bununla birlikte, isterseniz bunları tek bir kaynak grubuna yerleştirebilirsiniz.

Bu makaledeki örnekler bash kabuğu için biçimlendirilir.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git depoları ve belirteçleri

Ortamınızın benzetimini yapmak için aşağıdaki git deposunun her birini yönetebileceğiniz depolara birleştirin. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Ardından, tüm depolarınız için aşağıdaki değişkenleri güncelleştirin.

`:main`Git URL 'lerinin sonuna eklenen, varsayılan depo dalını temsil eder.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Git Web kancalarını klonlamak ve oluşturmak için ACR görevlerine yönelik bir [GitHub erişim belirteci (Pat)][git-token] gereklidir. Özel depoya gerekli izinlere sahip bir belirteç oluşturma adımları için bkz. [GitHub erişim belirteci oluşturma](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker Hub kimlik bilgileri  
Docker Hub 'ından görüntü çekmeden azaltma ve kimlik isteklerinden kaçınmak için bir [Docker Hub belirteci][docker-hub-tokens]oluşturun. Ardından, aşağıdaki ortam değişkenlerini ayarlayın:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Kayıt defterleri oluşturma

Azure CLı komutlarını kullanarak her biri kendi kaynak grubunda üç Premium katman kapsayıcısı kayıt defterleri oluşturun:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Anahtar Kasası oluşturma ve gizli dizileri ayarlama

Anahtar Kasası oluşturma:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Anahtar kasasında Docker Hub Kullanıcı adı ve belirteci ayarlayın:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Anahtar kasasında git PAT ayarlama ve doğrulama:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Azure Container örneği için kaynak grubu oluşturma

Bu kaynak grubu, görüntü dağıtımında daha sonraki bir görevde kullanılır `hello-world` .

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Ortak `node` temel görüntü oluştur

`node`Docker Hub 'da görüntünün benzetimini yapmak için, genel görüntüyü derlemek ve sürdürmek üzere bir [ACR görevi][acr-task] oluşturun. Bu kurulum, görüntü bakım yapanlar tarafından değişikliklere benzetim yapılmasına izin verir `node` .

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Docker azaltmasını önlemek için, göreve [Docker Hub kimlik bilgilerini][docker-hub-tokens] ekleyin. [ACR görevi kimlik bilgileri][acr-task-credentials] komutu Docker Hub da dahil olmak üzere herhangi bir kayıt defterine Docker kimlik bilgilerini geçirmek için kullanılabilir.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Anahtar kasasından değerleri okumak için görev erişimi verin:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Görevler git işlemeleri, temel görüntü güncelleştirmeleri, zamanlayıcılar veya el ile çalıştırmalar tarafından [tetiklenebilir][acr-task-triggers] . 

Görüntüyü oluşturmak için görevi el ile çalıştırın `node` :

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Görüntüyü sanal ortak kayıt defterinde listeleyin:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Görüntü oluşturma `hello-world`

Sanal ortak `node` görüntüye göre bir `hello-world` görüntü oluşturun.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Sanal ortak kayıt defterine çekme erişimi için belirteç oluştur

Kapsamında olan, sanal ortak kayıt defterine bir [erişim belirteci][acr-tokens] oluşturun `pull` . Ardından, anahtar kasasında ayarlayın:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Azure Container Instances tarafından çekme erişimi için belirteç oluştur

Görüntüyü barındıran kayıt defterine, çekme kapsamına göre bir [erişim belirteci][acr-tokens] oluşturun `hello-world` . Ardından, anahtar kasasında ayarlayın:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Görüntü derlemek ve bakımını yapmak için görev oluşturma `hello-world`

Aşağıdaki komut, depoda içindeki tanımından bir görev oluşturur `acr-tasks.yaml` `hello-world` . Görev adımları `hello-world` görüntüyü oluşturur ve sonra Azure Container Instances dağıtır. Azure Container Instances kaynak grubu, önceki bölümde oluşturulmuştur. `az container create`Görevi yalnızca içindeki bir fark ile çağırarak, `image:tag` görev Bu izlenecek yol boyunca aynı örneğe dağıtılır.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Sanal ortak kayıt defteri için göreve kimlik bilgileri ekleyin:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Anahtar kasasından değerleri okumak için görev erişimi verin:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Kaynak grubuna erişim izni vererek Azure Container Instances oluşturma ve yönetme için görev erişimi verin:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Oluşturulan ve yapılandırılan görev ile, görüntüyü derlemek ve dağıtmak için görevi çalıştırın `hello-world` :

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Oluşturulduktan sonra, görüntüyü barındıran kapsayıcının IP adresini alın `hello-world` .

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Tarayıcınızda, çalışan uygulamayı görmek için IP adresine gidin.

## <a name="update-the-base-image-with-a-questionable-change"></a>Temel görüntüyü "şüpheli" bir değişikliğe göre güncelleştir

Bu bölüm, temel görüntüde, ortamda sorun oluşmasına neden olabilecek bir değişikliğe benzetir.

1. Bu `Dockerfile` `base-image-node` depoyu açın.
1. `BACKGROUND_COLOR`Değişikliğin benzetimini yapmak için öğesini olarak değiştirin `Orange` .

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

, Oluşturmaya otomatik olarak başlamak üzere değişiklik ve izleme görevlerini uygulayın.

Yürütmeye başlamak için görevi izleyin:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Son `Succeeded` olarak şu bir tetikleyicisine göre durum görmeniz gerekir `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

İzle komutundan çıkmak için **CTRL + C** yazın ve ardından en son çalıştırma için günlükleri görüntüleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

`node`Görüntü tamamlandığında, `watch` ACR görevlerinin otomatik olarak görüntü oluşturmaya başlaması için `hello-world` :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Son `Succeeded` olarak şu bir tetikleyicisine göre durum görmeniz gerekir `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

İzle komutundan çıkmak için **CTRL + C** yazın ve ardından en son çalıştırma için günlükleri görüntüleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Tamamlandıktan sonra, güncelleştirilmiş görüntüyü barındıran sitenin IP adresini alın `hello-world` :

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Tarayıcınızda, turuncu (şüpheli) bir arka plana sahip olması gereken siteye gidin.

### <a name="checking-in"></a>İade etme

Bu noktada, `hello-world` Git Yürütmelerinde ve temel görüntüde değişiklikler üzerine otomatik olarak oluşturulan bir görüntü oluşturdunuz `node` . Bu örnekte, görev Azure Container Registry bir temel görüntüye göre oluşturulur, ancak desteklenen tüm kayıt defteri kullanılabilir.

Temel görüntü güncelleştirme, görüntü güncelleştirilirken görevi otomatik olarak yeniden çalıştırır `node` . Burada görüldüğü gibi, tüm güncelleştirmeler istenmez.

## <a name="gated-imports-of-public-content"></a>Ortak içeriğin geçitli içeri aktarmaları

Yukarı akış değişikliklerinin kritik iş yüklerini bozmasını engellemek için güvenlik taraması ve işlevsel testler eklenebilir.

Bu bölümde, aşağıdakileri yapmak için bir ACR görevi oluşturacaksınız:

* Test görüntüsü oluşturma
* Test görüntüsüne karşı işlevsel bir test betiği çalıştırın `./test.sh`
* Görüntü başarıyla test olduysa, ortak görüntüyü **baseımages** kayıt defterine aktarın

### <a name="add-automation-testing"></a>Otomasyon testi ekleme

Tüm yukarı akış içeriğini bağlamak için otomatik test uygulanır. Bu örnekte, ' yi `test.sh` denetleyen bir `$BACKGROUND_COLOR` . Test başarısız olursa, `EXIT_CODE` `1` ACR görev adımının başarısız olmasına neden olan, görevi çalıştırmayı sonlandıran bir, döndürülür. Testler, günlüğe kaydetme sonuçları da dahil olmak üzere herhangi bir araç biçiminde genişletilebilir. Ağ Geçidi, komut dosyasındaki bir pass/Fail yanıtı tarafından yönetilir, burada yeniden oluşturulur:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>Görev YAML 

Depodaki, `acr-task.yaml` `import-baseimage-node` aşağıdaki adımları gerçekleştiren öğesini gözden geçirin:

1. Aşağıdaki Dockerfile dosyasını kullanarak test temel görüntüsünü oluşturun:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Tamamlandığında, çalışma kapsayıcısını çalıştırarak görüntüyü doğrulama `./test.sh`
1. Yalnızca başarıyla tamamlanırsa, ile geçitli içeri aktarma adımlarını çalıştırın `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Temel görüntüyü içeri ve test etmek için görev oluşturma

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Sanal ortak kayıt defteri için göreve kimlik bilgileri ekleyin:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Anahtar kasasından değerleri okumak için görev erişimi verin:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

İçeri aktarma görevini çalıştırın:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Görev nedeniyle başarısız olursa `./test.sh: Permission denied` , betiğin yürütme izinlerine sahip olduğundan emin olun ve git deposuna geri kaydedin:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>`hello-world`Geçitli görüntüden derlemek için görüntü güncelleştirme `node`

Depodan kapsamındaki temel yapıt kayıt defterine erişmek için bir [erişim belirteci][acr-tokens] oluşturun `read` `node` . Ardından, anahtar kasasında ayarlanır:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Temel yapıt kayıt defteri için **Hello-World** görevine kimlik bilgileri ekleyin:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

`REGISTRY_FROM_URL`Kayıt defterini kullanacak şekilde değiştirmek için görevi güncelleştirin `BASE_ARTIFACTS`

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Kendi temel görüntü bağımlılığını değiştirmek için **Merhaba-Dünya** görevini çalıştırın:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Temel görüntüyü "geçerli" bir değişiklik ile Güncelleştir

1. Depodaki `Dockerfile` `base-image-node` depoyu açın.
1. Öğesini `BACKGROUND_COLOR` `Green` geçerli bir değişikliğin benzetimini yapmak için olarak değiştirin.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Değişikliği yürütün ve güncelleştirmelerin sırasını izleyin:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Çalışırken **CTRL + C** yazın ve günlükleri izleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Tamamlandıktan sonra, **temel görüntü içeri aktarma** görevini izleyin:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Çalışırken **CTRL + C** yazın ve günlükleri izleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Tamamlandıktan sonra **Merhaba-Dünya** görevini izleyin:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Çalışırken **CTRL + C** yazın ve günlükleri izleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Tamamlandıktan sonra, güncelleştirilmiş görüntüyü barındıran sitenin IP adresini alın `hello-world` :

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Tarayıcınızda, yeşil (geçerli) bir arka plana sahip olması gereken siteye gidin.

### <a name="view-the-gated-workflow"></a>Geçitli iş akışını görüntüleme

Önceki bölümdeki adımları kırmızı bir arka plan rengi ile yeniden gerçekleştirin.

1. Depoyu depoda açın `Dockerfile` `base-image-node`
1. `BACKGROUND_COLOR` `Red` Geçersiz bir değişikliğin benzetimini yapmak için öğesini olarak değiştirin.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Değişikliği yürütün ve güncelleştirmelerin sırasını izleyin:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Çalışırken **CTRL + C** yazın ve günlükleri izleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Tamamlandıktan sonra, **temel görüntü içeri aktarma** görevini izleyin:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Çalışırken **CTRL + C** yazın ve günlükleri izleyin:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Bu noktada, **temel içeri aktarma düğümü** görevinin hata doğrulamasını görmeniz ve bir güncelleştirmeyi yayımlamak için diziyi durdurmanız gerekir `hello-world` . Çıkış şuna benzer olacaktır:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Güncelleştirme yayımlama `hello-world`

Görüntüdeki değişiklikler, `hello-world` son doğrulanan görüntüyü kullanmaya devam eder `node` .

Taban `node` görüntüde geçişli doğrulamaları geçen diğer değişiklikler görüntüye temel görüntü güncelleştirmelerini tetikler `hello-world` .

## <a name="cleaning-up"></a>Temizleme

Artık gerekli değilse, bu makalede kullanılan kaynakları silin.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede. ortamınızdaki güncelleştirilmiş temel görüntüleri tanıtmak üzere otomatik bir başarı iş akışı oluşturmak için ACR görevlerini kullandınız. Azure Container Registry görüntüleri yönetmek için ilgili bilgiler bölümüne bakın.


* [Kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri](container-registry-image-tag-version.md)
* [Azure Container Registry 'de kapsayıcı görüntüsünü kilitleme](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io