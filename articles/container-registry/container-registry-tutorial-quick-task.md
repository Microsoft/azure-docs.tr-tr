---
title: Öğretici-hızlı kapsayıcı görüntüsü oluşturma
description: Bu öğreticide, Azure’da Azure Container Registry Görevleri (ACR Görevleri) ile bir Docker kapsayıcı görüntüsü derleme, ardından bu görüntüyü Azure Container Instances’a dağıtma hakkında bilgi edineceksiniz.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 282e6ea56835fba679510a29af936c1fbcb3ead2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775358"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Öğretici: Azure Container Registry Görevleri ile bulutta kapsayıcı görüntüleri derleme ve dağıtma

[ACR Görevleri](container-registry-tasks-overview.md), Azure’da kolaylaştırılmış ve verimli Docker kapsayıcı görüntüsü derlemeleri sağlayan bir Azure Container Registry özellik paketidir. Bu makalede, ACR Görevlerinin *hızlı görev* özelliğini kullanmayı öğreneceksiniz.

"İç döngü" geliştirme döngüsü tekrar eden kod yazma, derleme ve kaynak denetimine göndermeden önce uygulamanızı test etme sürecidir. Hızlı görev, iç döngünüzü buluta genişleterek, derleme başarısını doğrulama ve başarıyla derlenen görüntüleri kapsayıcı kayıt defterinize otomatik olarak gönderme olanağı sağlar. Görüntüleriniz bulutta, kayıt defterinize yakın bir konumda yerel olarak derlenerek daha hızlı dağıtımı mümkün kılar.

Tüm Dockerfile uzmanlığınız ACR Görevlerine doğrudan aktarılabilir. ACR Görevleri ile bulutta derlemek için Dockerfile’ı değiştirmeniz gerekmez, yalnızca çalıştırdığınız komutu değiştirmeniz yeterlidir. 

Dizinin birinci bölümü olan bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Örnek uygulama kaynak kodunu alma
> * Azure’da kapsayıcı görüntüsü derleme
> * Azure Container Instances‘a kapsayıcı dağıtma

Sonraki öğreticilerde, ACR Görevlerini kod işleme ve temel görüntü güncelleştirmesi üzerindeki otomatik kapsayıcı görüntüsü derlemelerine yönelik görevler için kullanmayı öğreneceksiniz. ACR görevleri, birden çok kapsayıcıyı oluşturma, gönderme ve isteğe bağlı olarak test etme adımlarını tanımlamak için bir YAML dosyası kullanarak [çok adımlı görevler](container-registry-tasks-multi-step.md)de çalıştırabilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="github-account"></a>GitHub hesabı

Henüz yoksa https://github.com sayfasında bir hesap oluşturun. Bu öğretici serisinde, ACR Görevlerindeki otomatik görüntü derlemelerini göstermek için bir GitHub deposu kullanılmıştır.

### <a name="fork-sample-repository"></a>Örnek deponun çatalını oluşturma

Sonra, GitHub kullanıcı arabirimini kullanarak GitHub hesabınızda örnek deponun çatalını oluşturun. Bu öğreticide, depodaki kaynaktan bir kapsayıcı görüntüsü derleyecek, sonraki öğreticide ise otomatik bir görev başlatmak üzere deponuzun çatalına bir işleme göndereceksiniz.

Şu deponun çatalını oluşturun: https://github.com/Azure-Samples/acr-build-helloworld-node

![GitHub’daki Çatal düğmesinin (vurgulanmış) ekran görüntüsü][quick-build-01-fork]

### <a name="clone-your-fork"></a>Çatalınızı kopyalama

Deponun çatalını oluşturduktan sonra çatalınızı kopyalayın ve yerel kopyanızı içeren dizine girin.

Depoyu kopyalayın `git` , **\<your-github-username\>** GitHub Kullanıcı adınızla değiştirin:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Kaynak kodunu içeren dizine girin:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash kabuğu

Bu öğretici serisindeki komutlar Bash kabuğu için biçimlendirilmiştir. PowerShell, Komut İstemi veya başka bir kabuk kullanmayı tercih ederseniz, satır devamlılığını ve ortam değişkeni biçimini uygun şekilde ayarlamanız gerekebilir.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>ACR Görevleri ile Azure'da Derleme

Kaynak kodunu makinenize çektikten sonra aşağıdaki adımları izleyerek bir kapsayıcı kayıt defteri oluşturun ve ACR Görevleri ile kapsayıcı görüntüsünü derleyin.

Örnek komutları yürütmeyi kolaylaştırmak için, bu serideki öğreticilerde kabuk ortam değişkenleri kullanılmıştır. `ACR_NAME` değişkenini ayarlamak için aşağıdaki komutu yürütün. **\<registry-name\>** Yeni kapsayıcı kayıt defteriniz için benzersiz bir adla değiştirin. Kayıt defteri adı Azure içinde benzersiz olmalı, yalnızca küçük harf ve 5-50 alfasayısal karakter içermelidir. Bu öğreticide oluşturduğunuz diğer kaynaklar bu adı temel alır; bu nedenle yalnızca bu ilk değişkeni değiştirmeniz gerekir.

```console
ACR_NAME=<registry-name>
```

Kapsayıcı kayıt defteri ortam değişkeni doldurulduğunda, herhangi bir değeri düzenlemeden öğreticideki komutların geri kalanını kopyalayıp yapıştırabilirsiniz. Bir kaynak grubu ve kapsayıcı kayıt defteri oluşturmak için aşağıdaki komutları yürütün:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Bir kayıt defteri oluşturduktan sonra ACR Görevlerini kullanarak örnek koddan bir kapsayıcı görüntüsü derleyebilirsiniz. Bir *hızlı görev* gerçekleştirmek için [az acr build][az-acr-build] komutunu yürütün:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

[az acr build][az-acr-build] komutunun çıktısı aşağıdakine benzer. Kaynak kodun Azure’a yüklenme durumunu ("bağlam") ve ACR görevinin bulutta çalıştırdığı `docker build` işlemini görebilirsiniz. ACR görevleri görüntülerinizi derlemek için `docker build` kullandığından, ACR Görevlerini kullanmaya hemen başlamak üzere Dockerfile üzerinde bir değişiklik yapılması gerekmez.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

Çıktının sonunda ACR Görevleri, görüntünüz için bulunan bağımlılıkları gösterir. Bunun yapılması, ACR Görevlerinin bir temel görüntünün işletim sistemi veya çerçeve yaması ile güncelleştirilmesi gibi durumlarda temel görüntü güncelleştirmelerinde görüntü derlemelerini otomatik hale getirmesini sağlar. Bu öğretici serisinin sonraki bölümlerinde ACR Görevlerinin temel görüntü desteği hakkında bilgi edineceksiniz.

## <a name="deploy-to-azure-container-instances"></a>Azure Container Instances’a dağıtma

Varsayılan olarak ACR Görevleri, başarıyla derlenen görüntüleri otomatik olarak kayıt defterinize gönderir ve kayıt defterinden hemen dağıtmanıza olanak tanır.

Bu bölümde, bir Azure Anahtar Kasası ve hizmet sorumlusu oluşturacak, ardından hizmet sorumlusunun kimlik bilgilerini kullanarak kapsayıcıyı Azure Container Instances (ACI) hizmetine dağıtacaksınız.

### <a name="configure-registry-authentication"></a>Kayıt defteri kimlik doğrulamasını yapılandırma

Tüm üretim senaryoları bir Azure kapsayıcı kayıt defterine erişmek için [hizmet sorumluları][service-principal-auth] kullanmalıdır. Hizmet sorumluları, kapsayıcı görüntüleriniz için rol tabanlı erişim denetimi sağlamanıza olanak tanır. Örneğin, bir hizmet sorumlusunu bir kayıt defterine yalnızca çekme erişimiyle yapılandırabilirsiniz.

#### <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[Azure Key Vault](../key-vault/index.yml) içinde henüz bir kasanız yoksa, aşağıdaki komutları kullanarak Azure CLI ile bir kasa oluşturun.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Hizmet sorumlusu oluşturma ve kimlik bilgilerini depolama

Şimdi bir hizmet sorumlusu oluşturup kimlik bilgilerini anahtar kasanızda depolamanız gerekiyor.

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] komutunu kullanarak hizmet sorumlusunu oluşturun ve [az keyvault secret set][az-keyvault-secret-set] komutunu kullanarak hizmet sorumlusunun **parolasını** kasanızda depolayın:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

`--role`Yukarıdaki komutta bağımsız değişken hizmet sorumlusunu, BT 'nin kayıt defterine yalnızca çekme erişimi veren *acrpull* rolüyle yapılandırır. Hem itme hem de çekme erişimi sağlamak için `--role` bağımsız değişkenini *acrpush* olarak değiştirin.

Ardından, hizmet sorumlusunun *uygulama kimliğini* kasada depolayın. Bu değer, kimlik doğrulaması için Azure Container Registry’ye geçirdiğiniz **kullanıcı adıdır**:

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Bir Azure Anahtar Kasası oluşturdunuz ve içinde iki gizli dizi depoladınız:

* `$ACR_NAME-pull-usr`: Kapsayıcı kayıt defterinin **kullanıcı adı** olarak kullanılacak hizmet sorumlusu kimliği.
* `$ACR_NAME-pull-pwd`: Kapsayıcı kayıt defterinin **parolası** olarak kullanılacak hizmet sorumlusu parolası.

Artık siz veya uygulamalarınız ve hizmetleriniz kayıt defterinden görüntüleri çektiğinde bu gizli dizilere ada göre başvurabilirsiniz.

### <a name="deploy-a-container-with-azure-cli"></a>Azure CLI ile kapsayıcı dağıtma

Hizmet sorumlusu kimlik bilgileri Azure Key Vault gizli dizileri olarak depolandıktan sonra, uygulamalarınız ve hizmetleriniz bu kimlik bilgilerini kullanarak özel kayıt defterinize erişebilir.

Bir kapsayıcı örneği dağıtmak için aşağıdaki [az container create][az-container-create] komutunu yürütün. Komut, kapsayıcı kayıt defterinizde kimlik doğrulaması yapmak için hizmet sorumlusunun Azure Key Vault’ta depolanmış kimlik bilgilerini kullanır.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

`--dns-name-label` değeri Azure içinde benzersiz olmalıdır; bu nedenle, yukarıdaki komut kapsayıcı kayıt defterinizin adını kapsayıcının DNS ad etiketine ekler. Komutun çıktısı, kapsayıcının tam etki alanı adını (FQDN) gösterir, örneğin:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Kapsayıcının FQDN'sini not alın, sonraki bölümde kullanacaksınız.

### <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Kapsayıcının başlangıç işlemini izlemek için [az container attach][az-container-attach] komutunu kullanın:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

`az container attach`Çıktı önce, görüntüyü gösterdiği ve başladığı için kapsayıcının durumunu görüntüler, ardından yerel KONSOLUNUZUN stdout ve stderr ' i kapsayıcının kapsayıcısına bağlar.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

`Server running at http://localhost:80` göründüğünde, çalışan uygulamayı görmek için tarayıcınızda kapsayıcının FQDN’sine gidin. FQDN, önceki bölümde yürüttüğünüz `az container create` komutunun çıktısında görüntülenmiş olmalıdır.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Tarayıcıda çalışan örnek uygulama":::

Konsolunuzu kapsayıcıdan ayırmak için `Control+C` öğesine dokunun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcı örneğini [az container delete][az-container-delete] komutu ile durdurun:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Kapsayıcı kayıt defteri, anahtar kasası ve hizmet sorumlusu dahil olmak üzere bu öğreticide oluşturduğunuz *tüm* kaynakları kaldırmak için aşağıdaki komutları verin. Bu kaynaklar, serinin [sonraki öğreticisinde](container-registry-tutorial-build-task.md) kullanılacaktır ancak doğrudan sonraki öğreticiye geçecekseniz bunları tutmak isteyebilirsiniz.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Sonraki adımlar

Hızlı bir görev ile iç döngünüzü test ettikten sonra, kaynak kodunu bir Git deposuna işlediğinizde kapsayıcı görüntüsü derlemelerini tetikleyecek bir **derleme görevi** yapılandırın:

> [!div class="nextstepaction"]
> [Görevlerle otomatik derlemeler tetikleme](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-login]: /cli/azure/reference-index#az_login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
