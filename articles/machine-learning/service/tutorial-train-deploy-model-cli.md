---
title: CLı 'dan modelleri eğitme ve dağıtma
titleSuffix: Azure Machine Learning
description: Komut satırından bir modeli eğitmek, kaydettirmek ve dağıtmak için Azure CLı için Machine Learning uzantısını nasıl kullanacağınızı öğrenin.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 1854599956755716955a6e691c3266ac54ddafd9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581559"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Öğretici: CLı 'dan model eğitme ve dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğreticide, bir modeli eğitmek, kaydettirmek ve dağıtmak için Azure CLı için Machine Learning uzantısını kullanırsınız.

Bu öğreticideki Python eğitim betikleri [scikit-](https://scikit-learn.org/) temel bir modeli eğitme hakkında bilgi edinin. Bu öğreticinin odağı betiklerin veya modeldeki değildir, ancak Azure Machine Learning çalışmak için CLı kullanma işlemidir.

Aşağıdaki eylemleri nasıl gerçekleştireceğinizi öğrenin:

> [!div class="checklist"]
> * Machine Learning uzantısını yükler
> * Bir Azure Machine Learning çalışma alanı oluşturma
> * Modeli eğitmek için kullanılan işlem kaynağını oluşturma
> * Eğitim çalıştırması başlatma
> * Bir modeli kaydetme ve indirme
> * Modeli bir Web hizmeti olarak dağıtma
> * Web hizmetini kullanarak veri puanı

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* **Yerel ortamınızdan**bu belgedeki CLI komutlarını kullanmak IÇIN [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)gerekir.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)KULLANıYORSANıZ, CLI tarayıcı aracılığıyla erişilir ve bulutta bulunur.

## <a name="download-the-example-project"></a>Örnek projeyi indirin

Bu öğretici için [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) projesini indirin. `model-training` ve `model-deployment` dizinlerindeki dosyalar bu öğreticideki adımlar tarafından kullanılır.

Dosyaların yerel bir kopyasını almak için, [bir. zip arşivi indirin](https://github.com/microsoft/MLOps/archive/master.zip)ya da depoyu kopyalamak Için aşağıdaki git komutunu kullanın:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Eğitim dosyaları

`model-training` Dizin, bir modeli eğitmek için kullanılan aşağıdaki dosyaları içerir:

* `.azureml\sklearn.runconfig`: bir __Çalıştırma yapılandırma__ dosyası. Bu dosya, modeli eğitmek için gereken çalışma zamanı ortamını tanımlar.
* `train-sklearn.py`: eğitim betiği. Bu dosya, modeli tratlar.
* `mylib.py`: `train-sklearn.py`tarafından kullanılan bir yardımcı modülüdür.
* `training-env.yml`: eğitim betiğini çalıştırmak için gereken yazılım bağımlılıklarını tanımlar.

Eğitim betiği, bir modeli eğitme hakkında bilgi edinmek için scikit-ile sunulan diabetes veri kümesini kullanır.

### <a name="deployment-files"></a>Dağıtım dosyaları

`model-deployment` Dizin, eğitilen modeli bir Web hizmeti olarak dağıtmak için kullanılan aşağıdaki dosyaları içerir:

* `aciDeploymentConfig.yml`: bir __dağıtım yapılandırma__ dosyası. Bu dosya, model için gereken barındırma ortamını tanımlar.
* `inferenceConfig.yml`: bir çıkarım configuration__ dosyası. Bu dosya, hizmet tarafından modelle verileri Puanlama için kullanılan yazılım ortamını tanımlar.
* `score.py`: gelen verileri kabul eden bir Python betiği, modeli kullanarak bir yanıt verir ve ardından bir yanıt döndürür.
* `scoring-env.yml`: modeli ve `score.py` betiğini çalıştırmak için gereken Conda bağımlılıkları.

## <a name="connect-to-your-azure-subscription"></a>Azure aboneliğinize bağlanma

CLı 'dan Azure aboneliğinizde kimlik doğrulayabilmeniz için çeşitli yollar vardır. En temel, bir tarayıcı kullanarak etkileşimli olarak kimlik doğrulaması yapmak için kullanılır. Etkileşimli olarak kimlik doğrulaması yapmak için bir komut satırı veya terminali açın ve aşağıdaki komutu kullanın:

```azurecli-interactive
az login
```

CLI varsayılan tarayıcınızı açabiliyorsa, tarayıcıyı açar ve oturum açma sayfasını yükler. Aksi takdirde, bir tarayıcı açmanız ve komut satırındaki yönergeleri izlemeniz gerekir. Yönergeler [https://aka.ms/devicelogin](https://aka.ms/devicelogin) göz atmayı ve bir yetkilendirme kodu girmeyi içerir.

## <a name="install-the-machine-learning-extension"></a>Machine Learning uzantısını yükler

Machine Learning uzantısını yüklemek için şu komutu kullanın:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Uzantının zaten yüklü olduğunu belirten bir ileti alırsanız, en son sürüme güncelleştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure platformunda temel bir kaynak kapsayıcısıdır. Azure Machine Learning ile çalışırken, kaynak grubu Azure Machine Learning çalışma alanınızı içerecektir. Ayrıca, çalışma alanı tarafından kullanılan diğer Azure hizmetlerini de içerecektir. Örneğin, modelinize bulut tabanlı bir işlem kaynağı kullanarak eğitebilirsiniz, kaynak grubunda bu kaynak oluşturulur.

__Yeni bir kaynak grubu oluşturmak__için aşağıdaki komutu kullanın. `<resource-group-name>`, bu kaynak grubu için kullanılacak adla değiştirin. `<location>`, bu kaynak grubu için kullanılacak Azure bölgesiyle değiştirin:

> [!TIP]
> Azure Machine Learning kullanılabilir olan bir bölge seçmelisiniz. Bilgi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Bu komuttan gelen yanıt aşağıdaki JSON ile benzerdir:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Kaynak gruplarıyla çalışma hakkında daha fazla bilgi için bkz. [az Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Yeni bir çalışma alanı oluşturmak için aşağıdaki komutu kullanın. `<workspace-name>`, bu çalışma alanı için kullanmak istediğiniz adla değiştirin. `<resource-group-name>`, kaynak grubunun adıyla değiştirin:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Yerel projeyi çalışma alanına bağla

Bir Terminal veya komut isteminden aşağıdaki komutları kullanarak dizinleri `mlops` dizinine değiştirin ve ardından çalışma alanınıza bağlanın:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Bu komut, çalışma alanınıza bağlanmak için gereken bilgileri içeren bir `.azureml/config.json` dosyası oluşturur. Bu öğreticide kullanılan `az ml` komutlarının geri kalanı bu dosyayı kullanacaktır. bu nedenle, çalışma alanını ve kaynak grubunu tüm komutlara eklemeniz gerekmez.

## <a name="create-the-compute-target-for-training"></a>Eğitim için işlem hedefi oluşturma

Bu örnek, modeli eğitmek için bir Azure Machine Learning Not defteri VM kullanır. Yeni bir not defteri VM 'si oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Bu komut, en fazla dört düğüm ile `cpu`adlı yeni bir işlem hedefi oluşturur. Seçilen VM boyutu, GPU kaynağına sahip bir VM sağlar. VM boyutu hakkında daha fazla bilgi için bkz. [VM türleri ve boyutları].

> [!IMPORTANT]
> İşlem hedefinin adı (Bu durumda`cpu`) önemlidir; bir sonraki bölümde kullanılan `.azureml/sklearn.runconfig` dosyası tarafından başvurulur.

## <a name="submit-the-training-run"></a>Eğitim çalıştırmasını gönder

`cpu` işlem hedefi üzerinde bir eğitim çalıştırmak için, dizinleri `model-training` diziniyle değiştirin ve aşağıdaki komutu kullanın:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Bu komut, deneme için bir ad belirtir (`myexperiment`). Deneme, çalışma alanında bu çalıştırma hakkındaki bilgileri depolar.

`-c sklearn` parametresi `.azureml/sklearn.runconfig` dosyasını belirtir. Daha önce belirtildiği gibi, bu dosya eğitim çalışması tarafından kullanılan ortamı yapılandırmak için kullanılan bilgileri içerir. Bu dosyayı inceliyorsanız, daha önce oluşturduğunuz `cpu` işlem hedefine başvurmuş olduğunu görürsünüz. Ayrıca, eğitim (`"nodeCount": "4"`) sırasında kullanılacak düğüm sayısını ve eğitim betiğini çalıştırmak için gereken Python paketlerini listeleyen bir `"condaDependenciees"` bölümü içerir.

Yapılandırma dosyalarını Çalıştır hakkında daha fazla bilgi için bkz. [model eğitimi için işlem hedeflerini ayarlama ve kullanma](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)veya bir runconfig 'in tam şemasını görmek Için bu [JSON dosyasına](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) başvuru.

`-t` parametresi bir JSON dosyasında bu çalıştırmaya yönelik bir başvuru depolar ve sonraki adımlarda modeli kaydetmek ve indirmek için kullanılır.

Eğitim işlemleri sırasında, uzaktan işlem kaynağındaki eğitim oturumundan bilgi akışı. Bilgilerin bir kısmı aşağıdaki metne benzer:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Bu metin, eğitim betiğinden (`train-sklearn.py`) günlüğe kaydedilir ve bu modelin performans ölçümlerinin ikisini görüntüler. Bu durumda, modelin en yüksek alfa değeri olmasını istiyoruz. Performans ölçümleri, eğitiminde olduğunuz modele özgüdür. Diğer modeller farklı performans ölçümlerine sahip olacaktır.

`train-sklearn.py`görüyorsanız, Ayrıca, eğitilen modelleri dosya olarak depoladığında Alfa değerini de kullandığını fark edeceksiniz. Bu durumda, çeşitli modeller de vardır. En yüksek Alpha ile olan en iyi bir değer olmalıdır. Yukarıdaki çıktıya ve kod, 0,95 Alpha içeren modelin `./outputs/ridge_0.95.pkl` olarak kaydedildiğini göz lıyor

Model, eğitilen işlem hedefinde `./outputs` dizinine kaydedildi. Bu durumda, Azure bulutundaki Azure Machine Learning Not defteri sanal makinesi. Eğitim süreci, eğitim Azure Machine Learning çalışma alanınıza gerçekleştiği işlem hedefinden `./outputs` dizininin içeriğini otomatik olarak yükler. Bu, denemenin bir parçası olarak depolanır (Bu örnekteki`myexperiment`).

## <a name="register-the-model"></a>Modeli Kaydet

Modeli denemenizin içindeki saklı sürümden doğrudan kaydetmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Bu komut, eğitim tarafından oluşturulan `outputs/ridge_0.95.pkl` dosyasını `mymodel`adlı yeni bir model kaydı olarak kaydeder. `--assets-path` denemelerle bir yola başvurur. Bu durumda, deneme ve çalıştırma bilgileri, eğitim komutu tarafından oluşturulan `runoutput.json` dosyasından yüklenir. `-t registeredmodel.json`, bu komut tarafından oluşturulan yeni kayıtlı modele başvuran bir JSON dosyası oluşturur ve kayıtlı modellerle çalışan diğer CLı komutları tarafından kullanılır.

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Model sürümü oluşturma

Model için döndürülen sürüm numarasını aklınızda edin. Bu ada sahip yeni bir modeli her kaydettiğinizde sürüm artırılır. Örneğin, modeli indirebilir ve aşağıdaki komutları kullanarak yerel bir dosyadan kaydedebilirsiniz:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

İlk komut kayıtlı modeli geçerli dizine indirir. Dosya adı, modeli kaydettiğinizde başvurulan dosya olan `ridge_0.95.pkl`. İkinci komut, önceki kayıtla (`mymodel`) aynı ada sahip yerel modeli (`-p "ridge_0.95.pkl"`) kaydeder. Bu kez, döndürülen JSON verisi sürümü 2 olarak listeler.

## <a name="deploy-the-model"></a>Modeli dağıtma

Bir modeli dağıtmak için dizinleri `model-deployment` dizinine değiştirin ve ardından aşağıdaki komutu kullanın:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

"Docker istemcisi oluşturulamadı" iletisini alabilirsiniz. Bu iletiyi yoksayabilirsiniz. CLı, yerel bir Docker kapsayıcısına Web hizmeti dağıtabilir ve Docker için denetim yapabilir. Bu durumda, yerel bir dağıtım kullanmıyoruz.

Bu komut, daha önce kaydettiğiniz modelin 1. sürümünü kullanarak `myservice`adlı yeni bir hizmet dağıtır.

`inferenceConfig.yml` dosyası, giriş betiği (`score.py`) ve yazılım bağımlılıkları gibi çıkarım gerçekleştirme hakkında bilgi sağlar. Bu dosyanın yapısı hakkında daha fazla bilgi için, bkz. [çıkarım yapılandırma şeması](reference-azure-machine-learning-cli.md#inference-configuration-schema). Giriş betikleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md#prepare-to-deploy).

`aciDeploymentConfig.yml`, hizmeti barındırmak için kullanılan dağıtım ortamını açıklar. Dağıtım yapılandırması, dağıtım için kullandığınız işlem türüne özeldir. Bu durumda, bir Azure Kapsayıcı örneği kullanılır. Daha fazla bilgi için bkz. [dağıtım yapılandırma şeması](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Dağıtım işleminin tamamlanması birkaç dakika sürer.

> [!TIP]
> Bu örnekte, Azure Container Instances kullanılır. ACI 'ya dağıtımlar, gerekli ACI kaynağını otomatik olarak oluşturur. Bunun yerine Azure Kubernetes hizmetine dağıtım yapıyorsanız, zaman içinde bir AKS kümesi oluşturmanız ve bunu `az ml model deploy` komutunun bir parçası olarak belirtmeniz gerekir. AKS 'e dağıtmaya bir örnek için bkz. [Azure Kubernetes hizmet kümesine model dağıtma](how-to-deploy-azure-kubernetes-service.md).

Birkaç dakika sonra, aşağıdaki JSON ile benzer bilgiler döndürülür:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Puanlama URI 'SI

Dağıtımdan döndürülen `scoringUri`, Web hizmeti olarak dağıtılan bir modelin REST uç noktasıdır. Bu URI 'yi aşağıdaki komutu kullanarak da edinebilirsiniz:

```azurecli-interactive
az ml service show -n myservice
```

Bu komut, `scoringUri`dahil olmak üzere aynı JSON belgesini döndürür.

REST uç noktası, hizmete veri göndermek için kullanılabilir. Hizmete veri gönderen bir istemci uygulaması oluşturma hakkında bilgi için bkz. [Web hizmeti olarak dağıtılan Azure Machine Learning modelini](how-to-consume-web-service.md) kullanma

### <a name="send-data-to-the-service"></a>Verileri hizmete gönder

Uç noktasını çağırmak için bir istemci uygulaması oluşturabilirsiniz, ancak Machine Learning CLı, test istemcisi olarak davranabilen bir yardımcı program sağlar. Hizmete test verileri göndermek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

Komuttan gelen yanıt `[4684.920839774082]`benzerdir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

> [!IMPORTANT]
> Oluşturduğunuz kaynaklar, diğer Azure Machine Learning öğreticileri ve nasıl yapılır makalelerinde önkoşul olarak kullanılabilir.

### <a name="delete-deployed-service"></a>Dağıtılan hizmeti Sil

Azure Machine Learning çalışma alanını kullanmaya devam etmek, ancak maliyetleri azaltmak için dağıtılan hizmetten kurtulabilmesi istiyorsanız aşağıdaki komutu kullanın:

```azurecli-interactive
az ml service delete -n myservice
```

Bu komut, silinen hizmetin adını içeren bir JSON belgesi döndürür. Hizmet silinmeden önce bu işlem birkaç dakika sürebilir.

### <a name="delete-the-training-compute"></a>Eğitim işlem programını silme

Azure Machine Learning çalışma alanını kullanmaya devam etmek, ancak eğitim için oluşturulan `cpu` işlem hedefinin kurtulığını almak istiyorsanız aşağıdaki komutu kullanın:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Bu komut, silinen işlem hedefinin KIMLIĞINI içeren bir JSON belgesi döndürür. İşlem hedefi silinmeden önce bu işlem birkaç dakika sürebilir.

### <a name="delete-everything"></a>Her şeyi sil

Oluşturduğunuz kaynakları kullanmayı planlamıyorsanız, ek ücret ödemeniz için bunları silin.

Kaynak grubunu ve bu belgede oluşturulan tüm Azure kaynaklarını silmek için aşağıdaki komutu kullanın. `<resource-group-name>`, daha önce oluşturduğunuz kaynak grubunun adıyla değiştirin:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Sonraki adımlar

Bu Azure Machine Learning öğreticide, aşağıdaki görevler için Machine Learning CLı 'yi kullandınız:

> [!div class="checklist"]
> * Machine Learning uzantısını yükler
> * Bir Azure Machine Learning çalışma alanı oluşturma
> * Modeli eğitmek için kullanılan işlem kaynağını oluşturma
> * Eğitim çalıştırması başlatma
> * Bir modeli kaydetme ve indirme
> * Modeli bir Web hizmeti olarak dağıtma
> * Web hizmetini kullanarak veri puanı

CLı kullanma hakkında daha fazla bilgi için bkz. [Azure Machine Learning IÇIN CLI uzantısını kullanma](reference-azure-machine-learning-cli.md).
