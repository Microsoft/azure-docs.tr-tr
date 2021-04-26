---
title: Yapıları aktar
description: Azure depolama hesaplarını kullanarak bir aktarım işlem hattı oluşturarak, görüntü veya diğer yapıtların koleksiyonlarını bir kapsayıcı kayıt defterinden başka bir kayıt defterine aktarın
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: c966600b0ca9d65cf533c3c2f0aca211c84917bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780784"
---
# <a name="transfer-artifacts-to-another-registry"></a>Yapıtları başka bir kayıt defterine aktar

Bu makalede, bir Azure Container Registry 'den başka bir kayıt defterine görüntü koleksiyonlarının veya diğer kayıt defteri yapılarının nasıl aktarılacağı gösterilmektedir. Kaynak ve hedef kayıt defterleri aynı veya farklı abonelikler, Active Directory kiracılar, Azure bulutları veya fiziksel olarak bağlantısı kesilen bulutlar içinde olabilir. 

Yapıtları aktarmak için [BLOB depolamayı](../storage/blobs/storage-blobs-introduction.md)kullanarak iki kayıt defterleri arasında yapıtları çoğaltan bir *Aktarım işlem hattı* oluşturursunuz:

* Kaynak kayıt defterindeki yapıtlar, kaynak depolama hesabındaki bir bloba aktarılabilir 
* Blob, kaynak depolama hesabından bir hedef depolama hesabına kopyalanır
* Hedef depolama hesabındaki blob, hedef kayıt defterinde yapıt olarak içeri aktarılır. Yapı blobu hedef depolamada her güncelleştirildiğinde tetiklenecek içeri aktarma ardışık düzenini ayarlayabilirsiniz.

Aktarım, her buluttaki depolama hesaplarına göre, fiziksel olarak bağlantısı kesilen bulutlarda iki Azure Container Registry arasında içerik kopyalamak için idealdir. Bunun yerine, Docker Hub ve diğer bulut satıcıları dahil olmak üzere bağlı bulutlar içindeki kapsayıcı kayıt defterlerinden görüntüleri kopyalamak istiyorsanız, [görüntü içeri aktarma](container-registry-import-images.md) önerilir.

Bu makalede, aktarım ardışık düzeni oluşturmak ve çalıştırmak için Azure Resource Manager şablon dağıtımlarını kullanırsınız. Azure CLı, depolama gizli dizileri gibi ilişkili kaynakları sağlamak için kullanılır. Azure CLı sürüm 2.2.0 veya üzeri önerilir. CLI’yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI’yı yükleme][azure-cli].

Bu özellik **Premium** kapsayıcı kayıt defteri hizmet katmanında kullanılabilir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry katmanları](container-registry-skus.md).

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="prerequisites"></a>Önkoşullar

* **Kapsayıcı kayıt defterleri** -aktarılacak yapıtlara ve hedef kayıt defterine sahip mevcut bir kaynak kayıt defterine ihtiyacınız vardır. ACR aktarımı, fiziksel olarak bağlantısı kesilen bulutlar arasında hareket etmek için tasarlanmıştır. Test için kaynak ve hedef kayıt defterleri aynı veya farklı bir Azure aboneliğinde, Active Directory kiracısında veya bulutta olabilir. 

   Bir kayıt defteri oluşturmanız gerekiyorsa bkz. [hızlı başlangıç: Azure CLI kullanarak özel kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md). 
* **Depolama hesapları** -bir abonelikte ve tercih ettiğiniz konumda kaynak ve hedef depolama hesapları oluşturun. Test amacıyla, kaynak ve hedef kayıt defterlerinden aynı abonelik veya abonelikleri kullanabilirsiniz. Platformlar arası senaryolar için genellikle her bulutta ayrı bir depolama hesabı oluşturursunuz. 

  Gerekirse, [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) veya diğer araçlarla depolama hesapları oluşturun. 

  Her hesapta yapıt aktarımı için bir blob kapsayıcısı oluşturun. Örneğin, *Aktarım* adlı bir kapsayıcı oluşturun. İki veya daha fazla aktarım işlem hattı aynı depolama hesabını paylaşabilir, ancak farklı depolama kapsayıcısı kapsamları kullanmalıdır.
* **Anahtar** kasaları-Anahtar kasaları, kaynak ve hedef depolama hesaplarına erişmek IÇIN kullanılan SAS belirteç gizli dizileri depolamak için gereklidir. Kaynak ve hedef kayıt defterlerinden aynı Azure aboneliğinde veya aboneliklerde kaynak ve hedef anahtar kasaları oluşturun. Tanıtım amacıyla, bu makalede kullanılan şablonlar ve komutlar, kaynak ve hedef anahtar kasalarının sırasıyla kaynak ve hedef kayıt defterleri ile aynı kaynak gruplarında bulunduğunu da varsayar. Ortak kaynak gruplarının bu kullanımı gerekli değildir, ancak bu makalede kullanılan şablonları ve komutları basitleştirir.

   Gerekirse, [Azure CLI](../key-vault/secrets/quick-create-cli.md) veya diğer araçlarla Anahtar kasaları oluşturun.

* **Ortam değişkenleri** -bu makaledeki komutlar gibi, kaynak ve hedef ortamlar için aşağıdaki ortam değişkenlerini ayarlayın. Tüm örnekler bash kabuğu için biçimlendirilir.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Senaryoya genel bakış

Kayıt defterleri arasında görüntü aktarımı için aşağıdaki üç işlem hattı kaynağını oluşturun. All, PUT işlemleri kullanılarak oluşturulur. Bu kaynaklar, *kaynak* ve *hedef* kayıt defterleri ve depolama hesaplarınız üzerinde çalışır. 

Depolama kimlik doğrulaması, anahtar kasalarında gizli dizi olarak yönetilen SAS belirteçlerini kullanır. İşlem hatları, kasalardaki gizli dizileri okumak için Yönetilen kimlikler kullanır.

* **[Exportpipeline](#create-exportpipeline-with-resource-manager)** - *kaynak* kayıt defteri ve depolama hesabı hakkında üst düzey bilgiler içeren uzun süreli kaynak kaynağı. Bu bilgiler, kaynak Depolama Blobu kapsayıcı URI 'sini ve kaynak SAS belirtecini yöneten anahtar kasasını içerir. 
* **[Importpipeline](#create-importpipeline-with-resource-manager)** - *hedef* kayıt defteri ve depolama hesabı hakkında üst düzey bilgiler içeren uzun süreli kaynak kaynağı. Bu bilgiler, hedef Depolama Blobu kapsayıcı URI 'sini ve hedef SAS belirtecini yöneten anahtar kasasını içerir. Varsayılan olarak bir içeri aktarma tetikleyicisi etkindir, bu nedenle bir yapıt blobu hedef depolama kapsayıcısına eklendiğinde işlem hattı otomatik olarak çalışır. 
* **[Ardışık düzen eylemsizlik](#create-pipelinerun-for-export-with-resource-manager)** ya da ımportpipeline kaynağını çağırmak için kullanılan kaynak.  
  * Bir ardışık düzen eylemsizlik kaynağı oluşturarak ve dışarı aktarılacak yapıtları belirterek ExportPipeline 'ı el ile çalıştırırsınız.  
  * İçeri aktarma tetikleyicisi etkinse, ımportpipeline otomatik olarak çalışır. Ardışık düzen eylemsizlik kullanarak el ile de çalıştırılabilir. 
  * Şu anda en fazla **50 Yapıt** , her ardışık düzen eylemsizlik ile aktarılabilir.

### <a name="things-to-know"></a>Bilinmesi gerekenler
* ExportPipeline ve ımportpipeline genellikle kaynak ve hedef bulutlarla ilişkili farklı Active Directory kiracılarda olur. Bu senaryo, dışarı ve içeri aktarma kaynakları için ayrı yönetilen kimlikler ve Anahtar kasaları gerektirir. Sınama amacıyla, bu kaynaklar aynı buluta yerleştirilebilecek ve kimlikleri paylaşıyor.
* Varsayılan olarak, ExportPipeline ve ımportpipeline şablonlarının her biri, Anahtar Kasası gizli dizileri için sistem tarafından atanan bir yönetilen kimlik sağlar. ExportPipeline ve ımportpipeline şablonları, sağladığınız kullanıcı tarafından atanan bir kimliği de destekler. 

## <a name="create-and-store-sas-keys"></a>SAS anahtarları oluşturma ve depolama

Aktarım, kaynak ve hedef ortamlarda depolama hesaplarına erişmek için paylaşılan erişim imzası (SAS) belirteçlerini kullanır. Aşağıdaki bölümlerde açıklandığı gibi belirteçleri oluşturun ve depolayın.  

### <a name="generate-sas-token-for-export"></a>Dışarı aktarma için SAS belirteci oluştur

Yapıt dışarı aktarma için kullanılan kaynak depolama hesabındaki kapsayıcı için bir SAS belirteci oluşturmak üzere [az Storage Container Generate-SAS][az-storage-container-generate-sas] komutunu çalıştırın.

*Önerilen belirteç izinleri*: okuma, yazma, listeleme, ekleme. 

Aşağıdaki örnekte, komut çıktısı, '? ' karakteri ön eki olan EXPORT_SAS ortam değişkenine atanır. `--expiry`Ortamınız için değeri güncelleştirin:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Dışarı aktarma için SAS belirtecini depola

SAS belirtecini, [az keykasasecret set][az-keyvault-secret-set]komutunu kullanarak kaynak Azure anahtar kasasında depolayın:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>İçeri aktarma için SAS belirteci oluştur

Yapıt içeri aktarması için kullanılan hedef depolama hesabındaki kapsayıcı için bir SAS belirteci oluşturmak üzere [az Storage Container Generate-SAS][az-storage-container-generate-sas] komutunu çalıştırın.

*Önerilen belirteç izinleri*: okuma, silme, listeleme

Aşağıdaki örnekte, komut çıktısı, '? ' karakteri ön eki olan IMPORT_SAS ortam değişkenine atanır. `--expiry`Ortamınız için değeri güncelleştirin:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>İçeri aktarma için SAS belirtecini depola

SAS belirtecini, [az keykasasecret set][az-keyvault-secret-set]komutunu kullanarak hedef Azure anahtar kasasında depolayın:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Kaynak Yöneticisi ile ExportPipeline oluşturma

Azure Resource Manager şablonu dağıtımını kullanarak kaynak kapsayıcısı kayıt defteriniz için bir ExportPipeline kaynağı oluşturun.

ExportPipeline Kaynak Yöneticisi [şablon dosyalarını](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) yerel bir klasöre kopyalayın.

Dosyasına aşağıdaki parametre değerlerini girin `azuredeploy.parameters.json` :

|Parametre  |Değer  |
|---------|---------|
|registryName     | Kaynak kapsayıcısı kayıt defterinizin adı      |
|Exportardışık düzen ınename     |  Dışarı aktarma işlem hattı için seçtiğiniz ad       |
|HedefUri     |  Kaynak ortamınızdaki depolama kapsayıcısının URI 'SI (dışa aktarma işlem hattının hedefi).<br/>Örnek: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Kaynak anahtar kasasının adı  |
|sasTokenSecretName  | Kaynak anahtar kasasındaki SAS belirteci parolasının adı <br/>Örnek: acrexportsas

### <a name="export-options"></a>Dışarı aktarma seçenekleri

`options`Dışa aktarma işlem hatları özelliği, isteğe bağlı Boole değerlerini destekler. Aşağıdaki değerler önerilir:

|Parametre  |Değer  |
|---------|---------|
|seçenekler | Overwriteblob 'Lar-mevcut hedef bloblarının üzerine yaz<br/>Devam et-bir yapıt dışarı aktarma başarısız olursa, kaynak kayıt defterindeki kalan yapıtları dışarı aktarmaya devam edin.

### <a name="create-the-resource"></a>Kaynağı oluşturma

Aşağıdaki örneklerde gösterildiği gibi, *Exportpipeline* adlı bir kaynak oluşturmak için [az Deployment Group Create][az-deployment-group-create] ' i çalıştırın. Varsayılan olarak, ilk seçenekle örnek şablon, ExportPipeline kaynağında sistem tarafından atanan bir kimlik sunar. 

İkinci seçenekle, kaynağa Kullanıcı tarafından atanan bir kimlik verebilirsiniz. (Kullanıcı tarafından atanan kimliğin oluşturulması gösterilmez.)

Her iki seçenek de, şablon kimliği dışa aktarma anahtar kasasındaki SAS belirtecine erişecek şekilde yapılandırır. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>1. seçenek: kaynak oluşturma ve sistem tarafından atanan kimliği etkinleştirme

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>2. seçenek: kaynak oluşturma ve Kullanıcı tarafından atanan kimlik sağlama

Bu komutta, Kullanıcı tarafından atanan kimliğin kaynak KIMLIĞINI ek bir parametre olarak belirtin.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Komut çıkışında, işlem hattının kaynak KIMLIĞINI () göz önünde edin `id` . Bu değeri daha sonra kullanmak için [az Deployment Group Show][az-deployment-group-show]' i çalıştırarak bir ortam değişkeninde saklayabilirsiniz. Örnek:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Kaynak Yöneticisi ile ımportpipeline oluşturma 

Azure Resource Manager şablonu dağıtımını kullanarak hedef kapsayıcı kayıt defterinizde bir ımportpipeline kaynağı oluşturun. Varsayılan olarak, işlem hattı, hedef ortamdaki depolama hesabı bir yapıt blobu olduğunda otomatik olarak içeri aktarılacak şekilde etkindir.

Importpipeline Kaynak Yöneticisi [şablon dosyalarını](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) yerel bir klasöre kopyalayın.

Dosyasına aşağıdaki parametre değerlerini girin `azuredeploy.parameters.json` :

Parametre  |Değer  |
|---------|---------|
|registryName     | Hedef kapsayıcı kayıt defterinizin adı      |
|ımportardışık düzen ınename     |  İçeri aktarma işlem hattı için seçtiğiniz ad       |
|sourceUri     |  Hedef ortamınızdaki depolama kapsayıcısının URI 'SI (içeri aktarma işlem hattının kaynağı).<br/>Örnek: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Hedef anahtar kasasının adı |
|sasTokenSecretName     |  Hedef anahtar kasasındaki SAS belirteci parolasının adı<br/>Örnek: ACR ımportsas |

### <a name="import-options"></a>İçeri aktarma seçenekleri

`options`İçeri aktarma işlem hattının özelliği, isteğe bağlı Boole değerlerini destekler. Aşağıdaki değerler önerilir:

|Parametre  |Değer  |
|---------|---------|
|seçenekler | OverwriteTags-varolan hedef etiketlerin üzerine yaz<br/>DeleteSourceBlobOnSuccess-hedef kayıt defterine başarılı bir şekilde alındıktan sonra kaynak Depolama Blobu silme<br/>Devam et-bir yapıt içeri aktarma başarısız olursa, hedef kayıt defterindeki kalan yapıtları içeri aktarmaya devam edin.

### <a name="create-the-resource"></a>Kaynağı oluşturma

Aşağıdaki örneklerde gösterildiği gibi *ımportpipeline* adlı bir kaynak oluşturmak için [az Deployment Group Create][az-deployment-group-create] ' i çalıştırın. Varsayılan olarak, ilk seçenekle örnek şablon, ımportpipeline kaynağında sistem tarafından atanan bir kimlik sunar. 

İkinci seçenekle, kaynağa Kullanıcı tarafından atanan bir kimlik verebilirsiniz. (Kullanıcı tarafından atanan kimliğin oluşturulması gösterilmez.)

Her iki seçenek de, şablon kimliği içeri aktarma anahtar kasasındaki SAS belirtecine erişecek şekilde yapılandırır. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>1. seçenek: kaynak oluşturma ve sistem tarafından atanan kimliği etkinleştirme

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>2. seçenek: kaynak oluşturma ve Kullanıcı tarafından atanan kimlik sağlama

Bu komutta, Kullanıcı tarafından atanan kimliğin kaynak KIMLIĞINI ek bir parametre olarak belirtin.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

İçeri aktarmayı el ile çalıştırmayı planlıyorsanız, işlem hattının kaynak KIMLIĞINI () göz önünde bulabilirsiniz `id` . Bu değeri, daha sonra kullanmak üzere [az Deployment Group Show][az-deployment-group-show] komutunu çalıştırarak bir ortam değişkeninde saklayabilirsiniz. Örnek:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Kaynak Yöneticisi ile dışarı aktarma için ardışık düzen eylemsizlik oluştur 

Azure Resource Manager şablonu dağıtımını kullanarak kaynak kapsayıcısı kayıt defteriniz için bir ardışık düzen eylemsizlik kaynağı oluşturun. Bu kaynak, daha önce oluşturduğunuz ExportPipeline kaynağını çalıştırır ve kapsayıcı kayıt defterinizde belirtilen yapıtları kaynak depolama hesabınıza bir blob olarak dışarı aktarır.

Ardışık düzen eylemsizlik Kaynak Yöneticisi [şablon dosyalarını](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) yerel bir klasöre kopyalayın.

Dosyasına aşağıdaki parametre değerlerini girin `azuredeploy.parameters.json` :

|Parametre  |Değer  |
|---------|---------|
|registryName     | Kaynak kapsayıcısı kayıt defterinizin adı      |
|Ardışık düzen eylemsizlik adı     |  Çalıştırma için seçtiğiniz ad       |
|pipelineResourceId     |  Dışarı aktarma işlem hattının kaynak KIMLIĞI.<br/>Örnek: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Kaynak depolama hesabınıza ( *myblob* gibi) aktarılmış yapıt blobu için seçtiğiniz ad
|Yapıt | Aktarılacak kaynak yapıtların dizisi, Etiketler veya bildirim özetleri<br/>Örnek: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Aynı özelliklere sahip bir ardışık düzen eylemsizlik kaynağını yeniden dağıtıyorsanız, [Forceupdatetag](#redeploy-pipelinerun-resource) özelliğini de kullanmanız gerekir.

Ardışık düzen eylemsizlik kaynağını oluşturmak için [az Deployment Group Create][az-deployment-group-create] ' i çalıştırın. Aşağıdaki örnek, dağıtım *Exportardışık düzen eylemsizlik*' i adlandırır.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Daha sonra kullanmak için, işlem hattı çalıştırmasının kaynak KIMLIĞINI bir ortam değişkeninde depolayın:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Yapıtların dışarı aktarılması birkaç dakika sürebilir. Dağıtım başarıyla tamamlandığında, dışarı aktarılmış blobu kaynak depolama hesabının *Aktarım* kapsayıcısında listeleyerek yapıtı dışarı aktarmayı doğrulayın. Örneğin, [az Storage blob List][az-storage-blob-list] komutunu çalıştırın:

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Blob aktarma (isteğe bağlı) 

Kaynak depolama hesabından hedef depolama hesabına [BLOB verileri aktarmak](../storage/common/storage-use-azcopy-v10.md#transfer-data) Için AzCopy aracını veya diğer yöntemleri kullanın.

Örneğin, aşağıdaki [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) komut myblob 'u kaynak hesabındaki *Aktarım* kapsayıcısından hedef hesaptaki *Aktarım* kapsayıcısına kopyalar. Blob hedef hesapta varsa, üzerine yazılır. Kimlik doğrulaması, SAS belirteçlerini kaynak ve hedef kapsayıcılar için uygun izinlerle kullanır. (Belirteç oluşturma adımları gösterilmez.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Importpipeline kaynağını Tetikle

`sourceTriggerStatus`Importpipeline (varsayılan değer) parametresini etkinleştirdiyseniz, işlem hattı, blob hedef depolama hesabına kopyalandıktan sonra tetiklenir. Yapıtların içeri aktarılması birkaç dakika sürebilir. İçeri aktarma işlemi başarıyla tamamlandığında, hedef kapsayıcı kayıt defterindeki depoları listeleyerek yapıt içeri aktarma işlemini doğrulayın. Örneğin, [az ACR Repository List][az-acr-repository-list]' i çalıştırın:

```azurecli
az acr repository list --name <target-registry-name>
```

`sourceTriggerStatus`İçeri aktarma işlem hattının parametresini etkinleştirmediyseniz, aşağıdaki bölümde gösterildiği gibi ımportpipeline kaynağını el ile çalıştırın. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Kaynak Yöneticisi içe aktarma için ardışık düzen eylemsizlik oluştur (isteğe bağlı) 
 
Ayrıca, hedef kapsayıcı Kayıt defterinize yapıt için bir ımportpipeline tetiklemesi tetiklemek üzere bir ardışık düzen eylemsizlik kaynağını kullanabilirsiniz.

Ardışık düzen eylemsizlik Kaynak Yöneticisi [şablon dosyalarını](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) yerel bir klasöre kopyalayın.

Dosyasına aşağıdaki parametre değerlerini girin `azuredeploy.parameters.json` :

|Parametre  |Değer  |
|---------|---------|
|registryName     | Hedef kapsayıcı kayıt defterinizin adı      |
|Ardışık düzen eylemsizlik adı     |  Çalıştırma için seçtiğiniz ad       |
|pipelineResourceId     |  İçeri aktarma işlem hattının kaynak KIMLIĞI.<br/>Örnek: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|Kaynak     |  Depolama hesabınızdaki, *myblob* gibi, aktarılmış yapılar için mevcut Blobun adı

Aynı özelliklere sahip bir ardışık düzen eylemsizlik kaynağını yeniden dağıtıyorsanız, [Forceupdatetag](#redeploy-pipelinerun-resource) özelliğini de kullanmanız gerekir.

Kaynağı çalıştırmak için [az Deployment Group Create][az-deployment-group-create] ' i çalıştırın.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Daha sonra kullanmak için, işlem hattı çalıştırmasının kaynak KIMLIĞINI bir ortam değişkeninde depolayın:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Dağıtım başarıyla tamamlandığında, hedef kapsayıcı kayıt defterindeki depoları listeleyerek yapıt içeri aktarma işlemini doğrulayın. Örneğin, [az ACR Repository List][az-acr-repository-list]' i çalıştırın:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Ardışık düzen eylemsizlik kaynağını yeniden Dağıt

*Aynı özelliklerle* bir ardışık düzen eylemsizlik kaynağını yeniden dağıtıyorsanız, **forceupdatetag** özelliğinden yararlanabilirsiniz. Bu özellik, yapılandırma değişmemiş olsa bile ardışık düzen eylemsizlik kaynağının yeniden oluşturulması gerektiğini gösterir. Lütfen, ardışık düzen eylemsizlik kaynağını her yeniden dağıtırken forceUpdateTag 'in farklı olduğundan emin olun. Aşağıdaki örnek, dışa aktarma için bir ardışık düzen eylemsizlik oluşturur. ForceUpdateTag ' i ayarlamak için geçerli tarih saat kullanılır, bu nedenle bu özelliğin her zaman benzersiz olmasını sağlar.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>İşlem hattı kaynaklarını silme

Aşağıdaki örnek komutlar [az Resource Delete][az-resource-delete] komutunu kullanarak bu makalede oluşturulan işlem hattı kaynaklarını siler. Kaynak kimlikleri daha önce ortam değişkenlerine depolandı.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Sorun giderme

* **Hata veya hata Şablon dağıtımı**
  * Bir işlem hattı çalıştırması başarısız olursa, `pipelineRunErrorMessage` çalışma kaynağı özelliğine bakın.
  * Ortak şablon dağıtım hataları için bkz. [ARM şablon dağıtımları sorunlarını giderme](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Depolamaya erişme sorunları**<a name="problems-accessing-storage"></a>
  * `403 Forbidden`Depolama alanından bir hata görürseniz, büyük OLASıLıKLA SAS belirtecinizle ilgili bir sorununuz olabilir.
  * SAS belirteci Şu anda geçerli olmayabilir. SAS belirteci oluşturulduktan sonra SAS belirtecinin geçerliliği bitmiş olabilir veya depolama hesabı anahtarları değişmiş olabilir. Depolama hesabı kapsayıcısına erişim için kimlik doğrulaması yapmak üzere SAS belirtecini kullanmayı deneyerek SAS belirtecinin geçerli olduğunu doğrulayın. Örneğin, var olan bir blob uç noktasını, ardından yeni bir Microsoft Edge InPrivate penceresinin Adres çubuğundaki SAS belirtecini yerleştirin veya kullanarak SAS belirtecine bir blob yükleyin `az storage blob upload` .
  * SAS belirteci, Izin verilen yeterli kaynak türüne sahip olmayabilir. SAS belirtecine Izin verilen kaynak türleri altındaki hizmet, kapsayıcı ve nesne izinleri verildiğini doğrulayın ( `srt=sco` SAS belirtecinde).
  * SAS belirteci yeterli izinlere sahip olmayabilir. Dışa aktarma işlem hatları için, gerekli SAS belirteç izinleri okuma, yazma, listeleme ve ekleme. İçeri aktarma işlem hatları için, gerekli SAS belirteç izinleri okuma, silme ve Listetir. (Silme izni yalnızca, içeri aktarma işlem hattının `DeleteSourceBlobOnSuccess` seçeneği etkinse gereklidir.)
  * SAS belirteci yalnızca HTTPS ile çalışacak şekilde yapılandırılmamış olabilir. SAS belirtecinin yalnızca HTTPS ile çalışacak şekilde yapılandırıldığını doğrulayın ( `spr=https` SAS belirtecinde).
* **Depolama bloblarını dışa aktarma veya içeri aktarma sorunları**
  * SAS belirteci geçersiz olabilir veya belirtilen dışarı aktarma veya içeri aktarma çalıştırması için yeterli izinlere sahip olmayabilir. Bkz. [depolama erişimi sorunları](#problems-accessing-storage).
  * Birden çok dışa aktarma çalıştırması sırasında kaynak depolama hesabındaki mevcut depolama Blobun üzerine yazılamaz. Dışarı aktarma çalıştırmasında OverwriteBlob seçeneğinin ayarlandığını ve SAS belirtecinin yeterli izinlere sahip olduğunu doğrulayın.
  * Hedef depolama hesabındaki Depolama Blobu, başarılı bir içeri aktarma çalıştırdıktan sonra silinmeyebilir. DeleteBlobOnSuccess seçeneğinin içeri aktarma çalıştırmasında ayarlandığını ve SAS belirtecinin yeterli izinlere sahip olduğunu doğrulayın.
  * Depolama Blobu oluşturulmadı veya silinmedi. Dışarı aktarma veya içeri aktarma çalıştırmasında belirtilen kapsayıcının bulunduğunu veya el ile içeri aktarma çalıştırması için belirtilen Depolama Blobu olduğunu onaylayın. 
* **AzCopy sorunları**
  * Bkz. [AzCopy sorunlarını giderme](../storage/common/storage-use-azcopy-configure.md).  
* **Yapıt aktarma sorunları**
  * Tüm yapıtlar veya hiçbiri aktarılmaz. Dışarı aktarma çalıştırmasında yapıtların yazımını ve dışarı aktarma ve içeri aktarma çalıştırmalarının blob adını onaylayın. En fazla 50 Yapıt aktaraldığınızı onaylayın.
  * İşlem hattı çalıştırması tamamlanmamış olabilir. Bir dışarı aktarma veya içeri aktarma çalıştırması biraz zaman alabilir. 
  * Diğer işlem hattı sorunları için Azure Container Registry ekibine dışarı aktarma çalıştırmasının veya içeri aktarma çalıştırmasının dağıtım [BAĞıNTı kimliğini](../azure-resource-manager/templates/deployment-history.md) sağlayın.
* **Görüntüyü fiziksel olarak yalıtılmış bir ortamda çekme sorunları**
  * Fiziksel katmanlarla ilgili hatalar görürseniz veya fiziksel olarak yalıtılmış bir ortamda görüntü almaya çalışırken mcr.microsoft.com çözümleme denemeleri varsa, görüntü bildiriminiz büyük olasılıkla dağıtılabilir olmayan katmanlara sahiptir. Fiziksel olarak yalıtılmış bir ortamın doğası nedeniyle, bu görüntüler genellikle çekmeyecektir. Dış kayıt defterlerine yapılan başvuruların görüntü bildirimini denetleyerek bu durumun bu olduğunu doğrulayabilirsiniz. Böyle bir durum söz konusu olduğunda, bir dışarı aktarma işlem hattını dağıtmadan önce, dağıtılabilir olmayan katmanları genel bulutunuzun ACR 'ye göndermeniz gerekir. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [nasıl yaparım? dağıtılabilir katmanları bir kayıt defterine gönderme](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry) .

## <a name="next-steps"></a>Sonraki adımlar

Tek kapsayıcı görüntülerini ortak bir kayıt defterine veya başka bir özel kayıt defterinden Azure Container Registry 'ye aktarmak için, [az ACR Import][az-acr-import] komut başvurusuna bakın.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az_login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az_keyvault_secret_show
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az_storage_container_generate_sas
[az-storage-blob-list]: /cli/azure/storage/blob#az_storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-deployment-group-delete]: /cli/azure/deployment/group#az_deployment_group_delete
[az-deployment-group-show]: /cli/azure/deployment/group#az_deployment_group_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-resource-delete]: /cli/azure/resource#az_resource_delete
