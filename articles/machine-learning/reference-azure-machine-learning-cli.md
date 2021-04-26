---
title: "& Azure Machine Learning CLı 'yi kullanma"
description: Çalışma alanınız, veri depoları, veri kümeleri, işlem hatları, modeller ve dağıtımlarınız gibi kaynakları oluşturmak & yönetmek için ML için Azure CLı uzantısı 'nı nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f30cd528a329708a7478b1a4a343f7be3b9eac04
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877795"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>& Azure Machine Learning için CLı uzantısını kullanın


Azure Machine Learning CLı, Azure platformu için platformlar arası komut satırı arabirimi olan [Azure CLI](/cli/azure/)'nin bir uzantısıdır. Bu uzantı Azure Machine Learning ile çalışmaya yönelik komutlar sağlar. Machine Learning etkinliklerinizi otomatikleştirmenize olanak tanır. Aşağıdaki listede CLı uzantısıyla yapabileceğiniz bazı örnek eylemler verilmiştir:

+ Makine öğrenimi modelleri oluşturmak için denemeleri çalıştırma

+ Müşteri kullanımı için makine öğrenimi modellerini Kaydet

+ Makine öğrenimi modellerinizin yaşam döngüsünü paketleyin, dağıtın ve izleyin

CLı Azure Machine Learning SDK 'sının yerini almaz. Otomasyonuna uygun olan yüksek parametreli görevleri işlemek için optimize edilmiş, tamamlayıcı bir araçtır.

## <a name="prerequisites"></a>Önkoşullar

* CLı 'yi kullanmak için bir Azure aboneliğinizin olması gerekir. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* **Yerel ortamınızdan** bu belgedeki CLI komutlarını kullanmak IÇIN [Azure CLI](/cli/azure/install-azure-cli)gerekir.

    [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)KULLANıYORSANıZ, CLI tarayıcı aracılığıyla erişilir ve bulutta bulunur.

## <a name="full-reference-docs"></a>Tam başvuru belgeleri

[Azure CLI 'nın Azure-CLI-ml uzantısı için tüm başvuru belgelerini](/cli/azure/ml/)bulun.

## <a name="connect-the-cli-to-your-azure-subscription"></a>CLı 'yı Azure aboneliğinize bağlama

> [!IMPORTANT]
> Azure Cloud Shell kullanıyorsanız, bu bölümü atlayabilirsiniz. Cloud Shell, Azure aboneliğinizde oturum açmak için kullandığınız hesabı kullanarak sizin için otomatik olarak kimlik doğrular.

CLı 'dan Azure aboneliğinizde kimlik doğrulayabilmeniz için çeşitli yollar vardır. En temel, bir tarayıcı kullanarak etkileşimli olarak kimlik doğrulaması yapmak için kullanılır. Etkileşimli olarak kimlik doğrulaması yapmak için bir komut satırı veya terminali açın ve aşağıdaki komutu kullanın:

```azurecli-interactive
az login
```

CLI varsayılan tarayıcınızı açabiliyorsa, tarayıcıyı açar ve oturum açma sayfasını yükler. Aksi takdirde, bir tarayıcı açmanız ve komut satırındaki yönergeleri izlemeniz gerekir. Yönergeler, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) bir yetkilendirme koduna göz atmaya ve girmeye yönelik bilgiler içerir.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Diğer kimlik doğrulama yöntemleri için bkz. [Azure CLI Ile oturum açma](/cli/azure/authenticate-azure-cli).

## <a name="install-the-extension"></a>Uzantıyı yükleme

Uzantı, ile başlayan bir komutu ilk kez kullanmaya çalıştığınızda otomatik olarak yüklenir `az ml` .

## <a name="update-the-extension"></a>Uzantıyı güncelleştirme

Machine Learning CLı uzantısını güncelleştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>Uzantıyı kaldırma

CLı uzantısını kaldırmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Kaynak yönetimi

Aşağıdaki komutlar, Azure Machine Learning tarafından kullanılan kaynakları yönetmek için CLı 'nın nasıl kullanılacağını göstermektedir.

+ Henüz bir tane yoksa, bir kaynak grubu oluşturun:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning çalışma alanı oluşturun:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Daha fazla bilgi için bkz. [az ml çalışma alanı oluştur](/cli/azure/ml/workspace#az_ml_workspace_create).

+ CLı bağlamsal tanımayı etkinleştirmek için bir klasöre çalışma alanı yapılandırması ekleyin.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Bu komut `.azureml` , örnek runconfig ve Conda ortam dosyalarını içeren bir alt dizin oluşturur. Ayrıca `config.json` , Azure Machine Learning çalışma alanınız ile iletişim kurmak için kullanılan bir dosya içerir.

    Daha fazla bilgi için bkz. [az ml Folder Attach](/cli/azure/ml/folder#az_ml_folder_attach).

+ Bir Azure Blob kapsayıcısını veri deposu olarak ekleyin.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Daha fazla bilgi için bkz. [az ml DataStore Attach-blob](/cli/azure/ml/datastore#az_ml_datastore_attach-blob).

+ Dosyaları bir veri deposuna yükleyin.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Daha fazla bilgi için bkz. [az ml DataStore upload](/cli/azure/ml/datastore#az_ml_datastore_upload).

+ Bir AKS kümesini Işlem hedefi olarak ekleyin.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Daha fazla bilgi için bkz. [az ml computetarget Attach aks](/cli/azure/ml/computetarget/attach#az_ml_computetarget_attach-aks)

### <a name="compute-clusters"></a>İşlem kümeleri

+ Yeni bir yönetilen işlem kümesi oluşturun.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Yönetilen kimliğe sahip yeni bir yönetilen işlem kümesi oluşturma

  + Kullanıcı tarafından atanan yönetilen kimlik

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Sistem tarafından atanan yönetilen kimlik

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Var olan bir kümeye yönetilen kimlik ekleme:

    + Kullanıcı tarafından atanan yönetilen kimlik
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Sistem tarafından atanan yönetilen kimlik

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Daha fazla bilgi için bkz. [az ml computetarget Create amlcompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>İşlem örneği
İşlem örneklerini yönetin.  Aşağıdaki örneklerde, işlem örneğinin adı **CPU** olur

+ Yeni bir computeinstance oluşturun.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Daha fazla bilgi için bkz. [az ml computetarget Create computeinstance](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance).

+ Bir computeinstance durdur.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Daha fazla bilgi için bkz. [az ml computetarget computeinstance stop](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

+ Bir computeinstance başlatın.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Daha fazla bilgi için bkz. [az ml computetarget computeinstance start](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

+ Bir computeinstance yeniden başlatın.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Daha fazla bilgi için bkz. [az ml computetarget computeinstance restart](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

+ Bir computeinstance silin.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Daha fazla bilgi için bkz. [az ml computetarget Delete computeinstance](/cli/azure/ml/computetarget#az_ml_computetarget_delete).


## <a name="run-experiments"></a><a id="experiments"></a>Denemeleri Çalıştır

* Denemenizin bir çalıştırmasını başlatın. Bu komutu kullanırken,-c parametresine karşı runconfig dosyasının adını ( \* dosya sisteminize bakıyorsanız. runconfig öğesinden önceki metin) belirtin.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`Komut `.azureml` , iki örnek runconfig dosyası içeren bir alt dizin oluşturur. 
    >
    > Programlı olarak çalıştırılan bir yapılandırma nesnesi oluşturan bir Python betiğinizin olması halinde, [runconfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) komutunu bir runconfig dosyası olarak kaydetmek için kullanabilirsiniz.
    >
    > Tam runconfig şeması bu [json dosyasında](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)bulunabilir. Şema her nesnenin anahtarı aracılığıyla kendi kendine belgedir `description` . Ayrıca, olası değerler için numaralandırmalar ve sonda bir şablon kod parçacığı vardır.

    Daha fazla bilgi için bkz. [az ml Run gönderme-betiği](/cli/azure/ml/run#az_ml_run_submit_script).

* Denemeleri listesini görüntüleyin:

    ```azurecli-interactive
    az ml experiment list
    ```

    Daha fazla bilgi için bkz. [az ml deneme listesi](/cli/azure/ml/experiment#az_ml_experiment_list).

### <a name="hyperdrive-run"></a>Hiper sürücü çalıştırma

Parametre ayarlama çalıştırmalarını gerçekleştirmek için HyperDrive ile Azure CLı kullanabilirsiniz. İlk olarak, aşağıdaki biçimde bir hiper sürücü yapılandırma dosyası oluşturun. Hyperparameter ayarlama parametreleriyle ilgili ayrıntılar için bkz. [modelinize yönelik hiper parametreleri ayarlama](how-to-tune-hyperparameters.md) makalesi.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Bu dosyayı çalıştırma yapılandırma dosyalarının yanına ekleyin. Ardından, kullanarak bir HyperDrive çalıştırması gönderebilirsiniz:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Hiperdrive yapılandırmasında runconfig ve *Parameter Space* içindeki *arguments* bölümüne göz önünde bırakın. Eğitim betiğine geçirilecek komut satırı bağımsız değişkenlerini içerirler. Runconfig içindeki değer her yineleme için aynı kalır, hiper sürücü yapılandırma aralığı üzerinden yinelenir. Her iki dosyada de aynı bağımsız değişkeni belirtmeyin.

## <a name="dataset-management"></a>Veri kümesi Yönetimi

Aşağıdaki komutlarda Azure Machine Learning içindeki veri kümeleriyle nasıl çalışılacağı gösterilmektedir:

+ Veri kümesini kaydetme:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Veri kümesini tanımlamak için kullanılan JSON dosyasının biçimi hakkında bilgi için, kullanın `az ml dataset register --show-template` .

    Daha fazla bilgi için bkz. [az ml DataSet Register](/cli/azure/ml/dataset#az_ml_dataset_register).

+ Çalışma alanındaki tüm veri kümelerini listeleyin:

    ```azurecli-interactive
    az ml dataset list
    ```

    Daha fazla bilgi için bkz. [az ml DataSet List](/cli/azure/ml/dataset#az_ml_dataset_list).

+ Bir veri kümesinin ayrıntılarını alın:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Daha fazla bilgi için bkz. [az ml DataSet Show](/cli/azure/ml/dataset#az_ml_dataset_show).

+ Veri kümesinin kaydını sil:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Daha fazla bilgi için bkz. [az ml DataSet Unregister](/cli/azure/ml/dataset#az_ml_dataset_archive).

## <a name="environment-management"></a>Ortam yönetimi

Aşağıdaki komutlarda, çalışma alanınız için Azure Machine Learning [ortamlarının](how-to-configure-environment.md) nasıl oluşturulacağı, kaydedileceği ve listenin nasıl yapılacağı gösterilmektedir:

+ Bir ortam için bir yapı iskelesi dosyaları oluşturun:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Daha fazla bilgi için bkz. [az ml Environment iskele](/cli/azure/ml/environment#az_ml_environment_scaffold).

+ Bir ortamı kaydedin:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Daha fazla bilgi için bkz. [az ml Environment Register](/cli/azure/ml/environment#az_ml_environment_register).

+ Kayıtlı ortamları listeleyin:

    ```azurecli-interactive
    az ml environment list
    ```

    Daha fazla bilgi için bkz. [az ml Environment List](/cli/azure/ml/environment#az_ml_environment_list).

+ Kayıtlı bir ortamı indirin:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Daha fazla bilgi için bkz. [az ml Environment Download](/cli/azure/ml/environment#az_ml_environment_download).

### <a name="environment-configuration-schema"></a>Ortam yapılandırma şeması

`az ml environment scaffold`Komutunu kullandıysanız, `azureml_environment.json` CLI ile özel ortam yapılandırması oluşturmak için değiştirilebilen ve kullanılabilecek bir şablon dosyası oluşturur. En üst düzey nesne, [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) Python SDK 'sında sınıfla eşlenir. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

Aşağıdaki tabloda JSON dosyasındaki her üst düzey alan, türü ve bir açıklama ayrıntıları verilmiştir. Bir nesne türü Python SDK 'dan bir sınıfa bağlanmışsa, Python sınıfında her bir JSON alanı ve genel değişken adı arasında gevşek bir 1:1 eşleşmesi vardır. Bazı durumlarda alan, sınıf değişkeni yerine bir Oluşturucu bağımsız değişkenine de eşlenir. Örneğin, `environmentVariables` alanı `environment_variables` sınıfındaki değişkenle eşlenir [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) .

| JSON alanı | Tür | Description |
|---|---|---|
| `name` | `string` | Ortamın adı. **Microsoft** veya **AzureML** ile ad başlatma. |
| `version` | `string` | Ortamın sürümü. |
| `environmentVariables` | `{string: string}` | Ortam değişkeni adlarının ve değerlerinin karma eşlemesi. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)hat hedef işlem kaynağında kullanılacak Python ortamını ve yorumlayıcısını tanımlar. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Ortamın belirtimlerine göre oluşturulan Docker görüntüsünü özelleştirmek için ayarları tanımlar. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | Bölüm Spark ayarlarını yapılandırır. Yalnızca Framework, PySpark olarak ayarlandığında kullanılır. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Databricks kitaplığı bağımlılıklarını yapılandırır. |
| `inferencingStackVersion` | `string` | Görüntüye eklenen ınırm yığın sürümünü belirtir. İkinci dereceden bir yığın eklemekten kaçınmak için, bu alanı bırakın `null` . Geçerli değer: "en son". |

## <a name="ml-pipeline-management"></a>ML işlem hattı yönetimi

Aşağıdaki komutlarda makine öğrenimi ardışık düzenleri ile nasıl çalışılacağı gösterilmektedir:

+ Machine Learning işlem hattı oluşturma:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Daha fazla bilgi için bkz. [az ml işlem hattı oluşturma](/cli/azure/ml/pipeline#az_ml_pipeline_create).

    İşlem hattı YAML dosyası hakkında daha fazla bilgi için bkz. [YAML 'de makine öğrenimi işlem hatlarını tanımlama](reference-pipeline-yaml.md).

+ İşlem hattı çalıştırma:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Daha fazla bilgi için bkz. [az ml Run gönderme-işlem hattı](/cli/azure/ml/run#az_ml_run_submit_pipeline).

    İşlem hattı YAML dosyası hakkında daha fazla bilgi için bkz. [YAML 'de makine öğrenimi işlem hatlarını tanımlama](reference-pipeline-yaml.md).

+ İşlem hattı zamanlama:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Daha fazla bilgi için bkz. [az ml işlem hattı oluşturma-zamanlama](/cli/azure/ml/pipeline#az_ml_pipeline_create-schedule).

    İşlem hattı zamanlaması YAML dosyası hakkında daha fazla bilgi için bkz. [YAML 'de makine öğrenimi işlem hatlarını tanımlama](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Model kaydı, profil oluşturma, dağıtım

Aşağıdaki komutlar, eğitilen bir modelin nasıl kaydedileceği ve sonra bir üretim hizmeti olarak nasıl dağıtılacağı gösterilmektedir:

+ Bir modeli Azure Machine Learning Kaydet:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Daha fazla bilgi için bkz. [az ml model Register](/cli/azure/ml/model#az_ml_model_register).

+ **Isteğe bağlı** Dağıtım için en iyi CPU ve bellek değerlerini almak üzere modelinizin profilini yapın.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Daha fazla bilgi için bkz. [az ml model profili](/cli/azure/ml/model#az_ml_model_profile).

+ Modelinizi AKS 'e dağıtın
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Çıkarım yapılandırma dosyası şeması hakkında daha fazla bilgi için bkz. [çıkarım yapılandırma şeması](#inferenceconfig).
    
    Dağıtım yapılandırma dosya şeması hakkında daha fazla bilgi için bkz. [dağıtım yapılandırma şeması](#deploymentconfig).

    Daha fazla bilgi için bkz. [az ml model dağıtımı](/cli/azure/ml/model#az_ml_model_deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Çıkarım yapılandırma şeması

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Dağıtım yapılandırma şeması

### <a name="local-deployment-configuration-schema"></a>Yerel dağıtım yapılandırma şeması

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure Container Instance dağıtım yapılandırma şeması 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes hizmet dağıtımı yapılandırma şeması

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [MACHINE LEARNING CLI uzantısı Için komut başvurusu](/cli/azure/ml).

* [Azure Pipelines kullanarak makine öğrenimi modellerini eğitme ve dağıtma](/azure/devops/pipelines/targets/azure-machine-learning)
