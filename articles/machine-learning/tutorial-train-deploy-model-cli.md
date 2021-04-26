---
title: CLı 'dan modelleri eğitme ve dağıtma
titleSuffix: Azure Machine Learning
description: Komut satırından bir modeli eğitmek, kaydettirmek ve dağıtmak için Azure CLı için Machine Learning uzantısını nasıl kullanacağınızı öğrenin.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: e518ea83608472753d3463a980aae169b291d4de
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221305"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Öğretici: CLı 'dan model eğitme ve dağıtma


Bu öğreticide, bir modeli eğitmek, kaydettirmek ve dağıtmak için Azure CLı için Machine Learning uzantısını kullanırsınız.

Bu öğreticideki Python eğitim betikleri [scikit-](https://scikit-learn.org/) basit bir modeli eğitme hakkında bilgi edinin. Bu öğreticinin odağı betiklerin veya modeldeki değildir, ancak Azure Machine Learning çalışmak için CLı kullanma işlemidir.

Aşağıdaki eylemleri nasıl gerçekleştireceğinizi öğrenin:

> [!div class="checklist"]
> * Machine Learning uzantısını yükler
> * Azure Machine Learning çalışma alanı oluşturma
> * Modeli eğitmek için kullanılan işlem kaynağını oluşturma
> * Modeli eğitmek için kullanılan veri kümesini tanımlama ve kaydetme
> * Eğitim çalıştırması başlatma
> * Bir modeli kaydetme ve indirme
> * Modeli bir Web hizmeti olarak dağıtma
> * Web hizmetini kullanarak veri puanı

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* **Yerel ortamınızdan** bu belgedeki CLI komutlarını kullanmak IÇIN [Azure CLI](/cli/azure/install-azure-cli)gerekir.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)KULLANıYORSANıZ, CLI tarayıcı aracılığıyla erişilir ve bulutta bulunur.

## <a name="download-the-example-project"></a>Örnek projeyi indirin

Bu öğretici için [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) projeyi indirin. `examples/cli-train-deploy`Dizindeki dosyalar bu öğreticideki adımlar tarafından kullanılır.

Dosyaların yerel bir kopyasını almak için, [bir. zip arşivi indirin](https://github.com/microsoft/MLOps/archive/master.zip)ya da depoyu kopyalamak Için aşağıdaki git komutunu kullanın:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Eğitim dosyaları

`examples/cli-train-deploy`Projedeki Dizin, bir modeli eğitmek için kullanılan aşağıdaki dosyaları içerir:

* `.azureml\mnist.runconfig`: Bir __çalışma yapılandırma__ dosyası. Bu dosya, modeli eğitmek için gereken çalışma zamanı ortamını tanımlar. Bu örnekte, modeli eğitim ortamına eğitmek için kullanılan verileri de takar.
* `scripts\train.py`: Eğitim betiği. Bu dosya, modeli tratlar.
* `scripts\utils.py`: Eğitim betiği tarafından kullanılan bir yardımcı dosyası.
* `.azureml\conda_dependencies.yml`: Eğitim betiğini çalıştırmak için gereken yazılım bağımlılıklarını tanımlar.
* `dataset.json`: Veri kümesi tanımı. Azure Machine Learning çalışma alanına MNIST veri kümesini kaydetmek için kullanılır.

### <a name="deployment-files"></a>Dağıtım dosyaları

Depo, eğitilen modeli bir Web hizmeti olarak dağıtmak için kullanılan aşağıdaki dosyaları içerir:

* `aciDeploymentConfig.yml`: Bir __dağıtım yapılandırma__ dosyası. Bu dosya, model için gereken barındırma ortamını tanımlar.
* `inferenceConfig.json`: Bir __çıkarım yapılandırma__ dosyası. Bu dosya, hizmet tarafından modelle verileri Puanlama için kullanılan yazılım ortamını tanımlar.
* `score.py`: Gelen verileri kabul eden bir Python betiği, modeli kullanarak bunu alır ve bir yanıt döndürür.
* `scoring-env.yml`: Modeli ve betiği çalıştırmak için gereken Conda bağımlılıkları `score.py` .
* `testdata.json`: Dağıtılan Web hizmetini test etmek için kullanılabilecek bir veri dosyası.

## <a name="connect-to-your-azure-subscription"></a>Azure aboneliğinize bağlanma

CLı 'dan Azure aboneliğinizde kimlik doğrulayabilmeniz için çeşitli yollar vardır. En basit, bir tarayıcı kullanarak etkileşimli olarak kimlik doğrulaması yapmak için kullanılır. Etkileşimli olarak kimlik doğrulaması yapmak için bir komut satırı veya terminali açın ve aşağıdaki komutu kullanın:

```azurecli-interactive
az login
```

CLI varsayılan tarayıcınızı açabiliyorsa, tarayıcıyı açar ve oturum açma sayfasını yükler. Aksi takdirde, bir tarayıcı açmanız ve komut satırındaki yönergeleri izlemeniz gerekir. Yönergeler, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) bir yetkilendirme koduna göz atmaya ve girmeye yönelik bilgiler içerir.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure platformunda bir kaynak kapsayıcısıdır. Azure Machine Learning ile çalışırken, kaynak grubu Azure Machine Learning çalışma alanınızı içerecektir. Ayrıca, çalışma alanı tarafından kullanılan diğer Azure hizmetlerini de içerecektir. Örneğin, modelinize bulut tabanlı bir işlem kaynağı kullanarak eğitebilirsiniz, kaynak grubunda bu kaynak oluşturulur.

__Yeni bir kaynak grubu oluşturmak__ için aşağıdaki komutu kullanın. `<resource-group-name>`Bu kaynak grubu için kullanılacak adla değiştirin. `<location>`Bu kaynak grubu için kullanılacak Azure bölgesiyle değiştirin:

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

Kaynak gruplarıyla çalışma hakkında daha fazla bilgi için bkz. [az Group](/cli/azure/group).

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Yeni bir çalışma alanı oluşturmak için aşağıdaki komutu kullanın. `<workspace-name>`Bu çalışma alanı için kullanmak istediğiniz adla değiştirin. `<resource-group-name>`Kaynak grubunun adıyla değiştirin:

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

Bir Terminal veya komut isteminden aşağıdaki komutları kullanarak dizinleri `cli-train-deploy` dizine değiştirin ve ardından çalışma alanınıza bağlanın:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Bu komut `.azureml/config.json` , çalışma alanınıza bağlanmak için gereken bilgileri içeren bir dosya oluşturur. `az ml`Bu öğreticide kullanılan komutların geri kalanı bu dosyayı kullanacaktır. bu nedenle, çalışma alanını ve kaynak grubunu tüm komutlara eklemeniz gerekmez.

## <a name="create-the-compute-target-for-training"></a>Eğitim için işlem hedefi oluşturma

Bu örnek, modeli eğitmek için bir Azure Machine Learning Işlem kümesi kullanır. Yeni bir işlem kümesi oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Bu komut `cpu-cluster` , en fazla dört düğüm ile adlı yeni bir işlem hedefi oluşturur. Seçilen VM boyutu, GPU kaynağına sahip bir VM sağlar. VM boyutu hakkında daha fazla bilgi için bkz. [VM türleri ve boyutları].

> [!IMPORTANT]
> İşlem hedefinin adı ( `cpu-cluster` Bu örnekte) önemlidir; `.azureml/mnist.runconfig` sonraki bölümde kullanılan dosya tarafından başvurulur.

## <a name="define-the-dataset"></a>Veri kümesini tanımlama

Bir modeli eğitebilmeniz için eğitim verilerini bir veri kümesi kullanarak sağlayabilirsiniz. CLı 'dan bir veri kümesi oluşturmak için bir veri kümesi tanım dosyası sağlamanız gerekir. Depoda `dataset.json` belirtilen dosya, veri kümesini kullanarak yeni bir veri kümesi oluşturur. Oluşturduğu veri kümesi adı `mnist-dataset` .

Veri kümesini dosyayı kullanarak kaydetmek için `dataset.json` aşağıdaki komutu kullanın:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Bir `id` sonraki bölümde kullanıldığı gibi girdinin değerini kopyalayın.

Bir veri kümesine yönelik daha kapsamlı bir şablon görmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Veri kümesine başvur

Veri kümesini eğitim ortamında kullanılabilir hale getirmek için, bu dosyaya runconfig dosyasından başvurmanız gerekir. `.azureml/mnist.runconfig`Dosya aşağıdaki YAML girdilerini içerir:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Girişin değerini, `id` veri kümesini kaydettiğinizde döndürülen değerle eşleşecek şekilde değiştirin. Bu değer, eğitim sırasında verileri işlem hedefine yüklemek için kullanılır.

Bu YAML, eğitim sırasında aşağıdaki eylemlere neden olur:

* Veri kümesini (veri kümesinin KIMLIĞINE göre) eğitim ortamında takar ve ortam değişkeninde bağlama noktasının yolunu depolar `mnist` .
* Eğitim ortamının içindeki verinin konumunu (bağlama noktası) bağımsız değişkenini kullanarak betiğe geçirir `--data-folder` .

Runconfig dosyası aynı zamanda eğitim çalıştırması tarafından kullanılan ortamı yapılandırmak için kullanılan bilgileri de içerir. Bu dosyayı inceliyorsanız, `cpu-compute` daha önce oluşturduğunuz işlem hedefine başvurmuş olduğunu görürsünüz. Ayrıca, eğitim () sırasında kullanılacak düğüm sayısını `"nodeCount": "4"` ve `"condaDependencies"` eğitim betiğini çalıştırmak Için gereken Python paketlerini listeleyen bir bölümü içerir.

> [!TIP]
> El ile bir runconfig dosyası oluşturulması mümkün olsa da, bu örnekteki `generate-runconfig.py` Dosya depoya dahil edilen dosya kullanılarak oluşturulmuştur. Bu dosya kayıtlı veri kümesine bir başvuru alır, bir Run config program aracılığıyla oluşturur ve sonra dosyayı devam ettirir.

Yapılandırma dosyalarını Çalıştır hakkında daha fazla bilgi için bkz. [model eğitimi için işlem hedeflerini kullanma](how-to-set-up-training-targets.md#whats-a-run-configuration). Tüm JSON başvurusu için bkz. [runconfigschema.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Eğitim çalıştırmasını gönder

İşlem hedefi üzerinde bir eğitim çalıştırmak için `cpu-cluster` aşağıdaki komutu kullanın:

```azurecli-interactive
az ml run submit-script -c mnist -e tutorial-cli --source-directory scripts -t runoutput.json
```

Bu komut, deneme () için bir ad belirtir `tutorial-cli` . Deneme, çalışma alanında bu çalıştırma hakkındaki bilgileri depolar.

`-c mnist`Parametresi, dosyasını belirtir `.azureml/mnist.runconfig` .

`-t`Parametresi BIR json dosyasında bu çalıştırmaya yönelik bir başvuru depolar ve sonraki adımlarda modeli kaydetmek ve indirmek için kullanılır.

Eğitim işlemleri sırasında, uzaktan işlem kaynağındaki eğitim oturumundan bilgi akışı. Bilgilerin bir kısmı aşağıdaki metne benzer:

```output
Predict the test set
Accuracy is 0.9185
```

Bu metin eğitim betiğiyle günlüğe kaydedilir ve modelin doğruluğunu görüntüler. Diğer modeller farklı performans ölçümlerine sahip olacaktır.

Eğitim betiğini inceleyebileceğiniz zaman da eğitilen modeli depoladığında Alfa değerini de kullandığını fark edeceksiniz `outputs/sklearn_mnist_model.pkl` .

Model, `./outputs` eğitilen işlem hedefinde dizine kaydedildi. Bu durumda, Azure bulutu 'nda Azure Machine Learning Işlem örneği. Eğitim süreci, `./outputs` eğitim Azure Machine Learning çalışma alanınıza gerçekleştiği işlem hedefinden dizinin içeriğini otomatik olarak yükler. Bu, denemenin bir parçası olarak depolanır ( `tutorial-cli` Bu örnekte).

## <a name="register-the-model"></a>Modeli Kaydet

Modeli denemenizin içindeki saklı sürümden doğrudan kaydetmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Bu komut `outputs/sklearn_mnist_model.pkl` , eğitim tarafından oluşturulan dosyayı, adlı yeni bir model kaydı olarak kaydeder `mymodel` . `--assets-path`Bir deneyde bir yola başvurur. Bu durumda, deneme ve çalıştırma bilgileri, `runoutput.json` eğitim komutu tarafından oluşturulan dosyadan yüklenir. , `-t registeredmodel.json` Bu komut tarafından oluşturulan yeni kayıtlı modele başvuran BIR JSON dosyası oluşturur ve kayıtlı modellerle çalışan DIĞER CLI komutları tarafından kullanılır.

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "tutorial-cli",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "tutorial-cli_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Model sürümü oluşturma

Model için döndürülen sürüm numarasını aklınızda edin. Bu ada sahip yeni bir modeli her kaydettiğinizde sürüm artırılır. Örneğin, modeli indirebilir ve aşağıdaki komutları kullanarak yerel bir dosyadan kaydedebilirsiniz:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

İlk komut kayıtlı modeli geçerli dizine indirir. Dosya adı `sklearn_mnist_model.pkl` , modeli kaydettiğinizde başvurulan dosya olur. İkinci komut, `-p "sklearn_mnist_model.pkl"` önceki kayıtla () aynı ada sahip yerel modeli () kaydeder `mymodel` . Bu kez, döndürülen JSON verisi sürümü 2 olarak listeler.

## <a name="deploy-the-model"></a>Modeli dağıtma

Bir modeli dağıtmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> "LocalWebservice varlığı denetlenemedi" veya "Docker Client oluşturulamadı" hatasıyla ilgili bir uyarı alabilirsiniz. Yerel bir Web hizmeti dağıtmadıkça bunu güvenle yoksayabilirsiniz.

Bu komut `myservice` , daha önce kaydettiğiniz modelin 1. sürümünü kullanarak adlı yeni bir hizmet dağıtır.

`inferenceConfig.yml`Dosya, çıkarım için modeli kullanma hakkında bilgi sağlar. Örneğin, giriş betiğine ( `score.py` ) ve yazılım bağımlılıklarına başvurur.

Bu dosyanın yapısı hakkında daha fazla bilgi için, bkz. [çıkarım yapılandırma şeması](reference-azure-machine-learning-cli.md#inference-configuration-schema). Giriş betikleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

`aciDeploymentConfig.yml`Hizmetini barındırmak için kullanılan dağıtım ortamını açıklar. Dağıtım yapılandırması, dağıtım için kullandığınız işlem türüne özeldir. Bu durumda, bir Azure Kapsayıcı örneği kullanılır. Daha fazla bilgi için bkz. [dağıtım yapılandırma şeması](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Dağıtım işleminin tamamlanması birkaç dakika sürer.

> [!TIP]
> Bu örnekte, Azure Container Instances kullanılır. ACI 'ya dağıtımlar, gerekli ACI kaynağını otomatik olarak oluşturur. Bunun yerine Azure Kubernetes hizmetine dağıtım yapıyorsanız, zaman içinde bir AKS kümesi oluşturmanız ve bunu komutun bir parçası olarak belirtmeniz gerekir `az ml model deploy` . AKS 'e dağıtmaya bir örnek için bkz. [Azure Kubernetes hizmet kümesine model dağıtma](how-to-deploy-azure-kubernetes-service.md).

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

`scoringUri`Dağıtımdan döndürülen, Web hizmeti olarak dağıtılan bir MODELIN REST uç noktasıdır. Bu URI 'yi aşağıdaki komutu kullanarak da edinebilirsiniz:

```azurecli-interactive
az ml service show -n myservice
```

Bu komut, dahil olmak üzere aynı JSON belgesini döndürür `scoringUri` .

REST uç noktası, hizmete veri göndermek için kullanılabilir. Hizmete veri gönderen bir istemci uygulaması oluşturma hakkında bilgi için bkz. [Web hizmeti olarak dağıtılan Azure Machine Learning modelini](how-to-consume-web-service.md) kullanma

### <a name="send-data-to-the-service"></a>Verileri hizmete gönder

Uç noktasını çağırmak için bir istemci uygulaması oluşturabilirsiniz, ancak Machine Learning CLı, test istemcisi olarak davranabilen bir yardımcı program sağlar. Dosyadaki verileri hizmete göndermek için aşağıdaki komutu kullanın `testdata.json` :

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> PowerShell kullanıyorsanız, bunun yerine aşağıdaki komutu kullanın:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Komuttan gelen yanıt şuna benzerdir `[ 3 ]` .

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

Azure Machine Learning çalışma alanını kullanmaya devam ederseniz, ancak `cpu-cluster` eğitim için oluşturulan işlem hedefinin kurtulığını yapmak istiyorsanız aşağıdaki komutu kullanın:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Bu komut, silinen işlem hedefinin KIMLIĞINI içeren bir JSON belgesi döndürür. İşlem hedefi silinmeden önce bu işlem birkaç dakika sürebilir.

### <a name="delete-everything"></a>Her şeyi sil

Oluşturduğunuz kaynakları kullanmayı planlamıyorsanız, ek ücret ödemeniz için bunları silin.

Kaynak grubunu ve bu belgede oluşturulan tüm Azure kaynaklarını silmek için aşağıdaki komutu kullanın. `<resource-group-name>`Daha önce oluşturduğunuz kaynak grubunun adıyla değiştirin:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Sonraki adımlar

Bu Azure Machine Learning öğreticide, aşağıdaki görevler için Machine Learning CLı 'yi kullandınız:

> [!div class="checklist"]
> * Machine Learning uzantısını yükler
> * Azure Machine Learning çalışma alanı oluşturma
> * Modeli eğitmek için kullanılan işlem kaynağını oluşturma
> * Modeli eğitmek için kullanılan veri kümesini tanımlama ve kaydetme
> * Eğitim çalıştırması başlatma
> * Bir modeli kaydetme ve indirme
> * Modeli bir Web hizmeti olarak dağıtma
> * Web hizmetini kullanarak veri puanı

CLı kullanma hakkında daha fazla bilgi için bkz. [Azure Machine Learning IÇIN CLI uzantısını kullanma](reference-azure-machine-learning-cli.md).