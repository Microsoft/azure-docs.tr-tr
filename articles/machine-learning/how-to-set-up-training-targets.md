---
title: Eğitim çalıştırmasını yapılandırma
titleSuffix: Azure Machine Learning
description: Çeşitli eğitim ortamlarında (işlem hedefleri) makine öğrenimi modelinizi eğitme. Eğitim ortamları arasında kolayca geçiş yapabilirsiniz.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: f38fe7d847754247f8c1510527b3ffe026c20be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518509"
---
# <a name="configure-and-submit-training-runs"></a>Eğitim çalıştırmalarını yapılandırma ve gönderme

Bu makalede, modellerinizi eğitmek için Azure Machine Learning çalıştırmalarını yapılandırmayı ve göndermeyi öğreneceksiniz. Kod parçacıkları, bir eğitim betiğinin yapılandırma ve göndermesinin temel kısımlarını açıklamaktadır.  Ardından, uçtan uca tam çalışma örneklerini bulmak için [örnek not defterlerinden](#notebooks) birini kullanın.

Eğitim sırasında, yerel bilgisayarınızda başlamak ve daha sonra bulut tabanlı bir kümeye genişletmek yaygın bir şekilde yapılır. Azure Machine Learning, komut dosyanızı, eğitim betiğinizi değiştirmek zorunda kalmadan çeşitli işlem hedeflerinde çalıştırabilirsiniz.

Yapmanız gereken tek şey, bir **komut dosyası çalıştırma yapılandırması** içindeki her bir işlem hedefi için ortamı tanımlamaktır.  Daha sonra eğitim denemenizi farklı bir işlem hedefinde çalıştırmak istediğinizde, bu işlem için çalıştırma yapılandırmasını belirtin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin
* [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install) (>= 1.13.0)
* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md),`ws`
* İşlem hedefi, `my_compute_target` .  [İşlem hedefi oluştur](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Betik çalıştırma Yapılandırması nedir?
Bir deneme kapsamında bir eğitim çalışması göndermek için gereken bilgileri yapılandırmak üzere bir [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) kullanılır.

Eğitim denemenizi bir ScriptRunConfig nesnesi ile gönderebilirsiniz.  Bu nesne şunları içerir:

* **source_directory**: eğitim betiğinizi içeren kaynak dizin
* **betik**: çalıştırılacak eğitim betiği
* **compute_target**: üzerinde çalıştırılacak işlem hedefi
* **ortam**: betiği çalıştırırken kullanılacak ortam
* ve bazı ek yapılandırılabilir seçenekler (daha fazla bilgi için [başvuru belgelerine](/python/api/azureml-core/azureml.core.scriptrunconfig) bakın)

## <a name="train-your-model"></a><a id="submit"></a>Modelinizi eğitme

Eğitim çalışması göndermek için kod deseninin her türlü bilgi işlem hedefi vardır:

1. Çalıştırmak için bir deneme oluşturun
1. Betiğin çalışacağı bir ortam oluşturun
1. İşlem hedefini ve ortamı belirten bir ScriptRunConfig oluşturun
1. Çalıştırmayı gönder
1. Çalıştırmanın tamamlanmasını bekleyin

İsterseniz şunları yapabilirsiniz:

* [Hiper parametre ayarlama](how-to-tune-hyperparameters.md)Için bir Hyperdrive çalıştırması gönderebilirsiniz.
* [Vs Code uzantısı](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)aracılığıyla bir deneme gönderir.

## <a name="create-an-experiment"></a>Deneme oluşturma

Çalışma alanınızda bir deneme oluşturun.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Bir işlem hedefi seçin

Eğitim betiğinizin çalışacağı işlem hedefini seçin. ScriptRunConfig içinde herhangi bir işlem hedefi belirtilmemişse veya, `compute_target='local'` Azure ml, betiğinizi yerel olarak yürütecektir. 

Bu makaledeki örnek kod, `my_compute_target` "Önkoşullar" bölümünde zaten bir işlem hedefi oluşturmuş olduğunuzu varsayar.

>[!Note]
>Azure Databricks model eğitimi için işlem hedefi olarak desteklenmez. Veri hazırlama ve dağıtım görevleri için Azure Databricks kullanabilirsiniz. 

## <a name="create-an-environment"></a>Ortam oluşturma
Azure Machine Learning [ortamlar](concept-environments.md) , Machine Learning eğitiminin gerçekleştiği ortamın kapsüllenmesi. Bunlar, eğitim ve Puanlama betikleriniz etrafında Python paketlerini, Docker görüntüsünü, ortam değişkenlerini ve yazılım ayarlarını belirler. Bunlar ayrıca çalışma zamanlarını belirtir (Python, Spark veya Docker).

Kendi ortamınızı tanımlayabilir ya da Azure ML seçkin ortamını kullanabilirsiniz. [Seçkin ortamlar](./how-to-use-environments.md#use-a-curated-environment) , varsayılan olarak çalışma alanınızda kullanılabilir olan önceden tanımlanmış ortamlardır. Bu ortamlar, çalışma hazırlığı maliyetini azaltan önbelleğe alınmış Docker görüntüleri tarafından desteklenir. Kullanılabilir tüm ortamların tam listesi için bkz. [Azure Machine Learning seçkin ortamlar](./resource-curated-environments.md) .

Uzaktan işlem hedefi için, aşağıdaki popüler ortamlarından birini kullanarak şunları yapabilirsiniz:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Ortamlar hakkında daha fazla bilgi ve Ayrıntılar için bkz. [Azure Machine Learning yazılım ortamları oluşturma & kullanma](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Yerel işlem hedefi

İşlem hedefinizin **yerel makineniz** olması durumunda, gerekli tüm paketlerin betiğin çalıştığı Python ortamında kullanılabilir olmasını sağlamaktan siz sorumlusunuz.  `python.user_managed_dependencies`Geçerli Python ortamınızı (veya belirttiğiniz yolda Python) kullanmak için kullanın.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Betik çalıştırma yapılandırmasını oluşturma

Artık bir işlem hedefine ( `my_compute_target` ) ve ortamınıza () sahip olduğunuza göre `myenv` , kendi dizininizde bulunan eğitim betiğinizi () çalıştıran bir betik çalıştırma yapılandırması oluşturun `train.py` `project_folder` :

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Bir ortam belirtmezseniz, sizin için varsayılan bir ortam oluşturulur.

Eğitim betiğe geçirmek istediğiniz komut satırı bağımsız değişkenlerinizin varsa, **`arguments`** ScriptRunConfig oluşturucusunun parametresi aracılığıyla bunları belirtebilirsiniz, örneğin `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Çalıştırma için izin verilen varsayılan en uzun süreyi geçersiz kılmak istiyorsanız, bunu parametresi aracılığıyla yapabilirsiniz **`max_run_duration_seconds`** . Sistem, bu değerden daha uzun sürerse, çalıştırmayı otomatik olarak iptal etmeye çalışacaktır.

### <a name="specify-a-distributed-job-configuration"></a>Dağıtılmış iş yapılandırması belirtme
Dağıtılmış bir eğitim işi çalıştırmak istiyorsanız, parametreye dağıtılmış işe özgü yapılandırmayı sağlayın **`distributed_job_config`** . Desteklenen yapılandırma türleri, [Mpiconation](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [Tensorflowconfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)ve [pytorchconfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)içerir. 

Dağıtılmış Horovod, TensorFlow ve PyTorch işlerini çalıştırmaya yönelik daha fazla bilgi ve örnek için bkz.:

* [TensorFlow modellerini eğitme](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch modellerini eğitme](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Denemeyi gönderme

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Eğitim çalıştırmasını gönderdiğinizde, eğitim betiklerinizi içeren dizinin bir anlık görüntüsü oluşturulur ve işlem hedefine gönderilir. Ayrıca çalışma alanınızdaki denemenin bir parçası olarak da depolanır. Dosyaları değiştirir ve çalışmayı yeniden gönderirseniz yalnızca değiştirilen dosyalar karşıya yüklenir.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Anlık görüntüler hakkında daha fazla bilgi için bkz. [anlık görüntüler](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Özel klasörler** İki klasör, *Çıkış* ve *günlük*, Azure Machine Learning özel bir işleme alır. Eğitim sırasında, kök dizine (ve sırasıyla) göre olan *çıktılar* ve *Günlükler* adlı klasörlere dosya yazdığınızda `./outputs` `./logs` , çalışma işlemi tamamlandıktan sonra dosyalara erişmeniz için dosyalar otomatik olarak çalıştırma geçmişinize yüklenir.
>
> Eğitim sırasında (model dosyaları, kontrol noktaları, veri dosyaları veya çizilmiş görüntüler gibi) yapıt oluşturmak için bunları `./outputs` klasörüne yazın.
>
> Benzer şekilde, eğitim çalıştırınızdan klasöre her türlü günlüğü de yazabilirsiniz `./logs` . Azure Machine Learning [tensorboard tümleştirmesini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) kullanmak Için, tensorboard günlüklerinizi bu klasöre yazdığınızdan emin olun. Çalışma işlemi devam ederken, TensorBoard 'ı başlatabilir ve bu günlükleri akıtırabileceksiniz.  Daha sonra, günlükleri önceki çalıştırabileceksiniz herhangi birinden geri yükleyebilirsiniz.
>
> Örneğin, uzak eğitime çalıştıktan sonra *çıktılar* klasörüne yazılmış bir dosyayı yerel makinenize indirmek için: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git izleme ve Tümleştirme

Kaynak dizinin yerel bir git deposu olduğu bir eğitim çalıştırması başlattığınızda, depo hakkındaki bilgiler çalıştırma geçmişinde depolanır. Daha fazla bilgi için bkz. [Azure Machine Learning Için git tümleştirmesi](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Not defteri örnekleri

Çeşitli eğitim senaryolarında çalıştırmaları yapılandırma örnekleri için bu not defterlerine bakın:
* [Çeşitli işlem hedeflerine yönelik eğitim](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [ML çerçeveleri ile eğitim](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [Öğreticiler/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Sorun giderme

* **Çalıştırma başarısız `jwt.exceptions.DecodeError`**: tam hata iletisi: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    En son azureml-Core sürümüne yükseltmeyi göz önünde bulundurun: `pip install -U azureml-core` .
    
    Bu sorunu yerel çalıştırmalar için çalıştırıyorsanız, ortamınızda çalıştırmalarının çalıştırıldığı PyJWT sürümünün sürümünü denetleyin. PyJWT 'nin desteklenen sürümleri < 2.0.0. Sürüm >= 2.0.0 olduğunda PyJWT öğesini ortamdan kaldırın. PyJWT sürümünü denetleyebilir, doğru sürümü aşağıdaki şekilde kaldırıp yükleyebilirsiniz:
    1. Bir komut kabuğu başlatın, azureml, azureml 'nın yüklü olduğu Conda ortamını etkinleştirin.
    2. `pip freeze` `PyJWT` Bulunursa, listelenen sürümün < 2.0.0 olması gerektiğini belirtin
    3. Listelenen sürüm desteklenen bir sürüm değilse, `pip uninstall PyJWT` komut kabuğu 'nda, onay için y girin.
    4. `pip install 'PyJWT<2.0.0'` kullanarak yükleme
    
    Çalıştırılmasıyla birlikte Kullanıcı tarafından oluşturulan bir ortam gönderiyorsanız, bu ortamda en son azureml-çekirdek sürümünü kullanmayı düşünün. Sürümler >= 1.18.0 of azureml-Core zaten pin PyJWT < 2.0.0. Gönderdiğiniz ortamda bir azureml-Core < 1.18.0 sürümü kullanmanız gerekiyorsa, PIP bağımlılıklarınız içinde PyJWT < 2.0.0 belirttiğinizden emin olun.


 * **Moduleerrors (modül adı yok)**: Azure ML 'de denemeleri gönderirken moduleerrors içinde çalıştırıyorsanız, eğitim betiği bir paketin yüklenmesini bekliyor ancak bu, eklenmez. Paket adı ' nı sağladığınızda, Azure ML paketi eğitim çalıştırınızdan kullanılan ortama yüklenir.

    Denemeleri göndermek için estimators kullanıyorsanız, paketi `pip_packages` `conda_packages` yüklemek istediğiniz kaynağı temel alarak tahmin aracı 'da veya parametresi aracılığıyla bir paket adı belirtebilirsiniz. Ayrıca, tüm bağımlılıklarınızı kullanarak bir de bir de belirtebilirsiniz `conda_dependencies_file` veya parametresini kullanarak bir txt dosyasındaki tüm PIP gereksinimlerinizi listeleyin `pip_requirements_file` . Tahmin aracı tarafından kullanılan varsayılan görüntüyü geçersiz kılmak istediğiniz bir Azure ML ortamı nesneniz varsa, bu ortamı `environment` tahmin aracı oluşturucusunun parametresi aracılığıyla belirtebilirsiniz.
    
    Azure ML tarafından sağlanan Docker görüntüleri ve içerikleri, [AzureML kapsayıcılarında](https://github.com/Azure/AzureML-Containers)görülebilir.
    Çerçeveye özgü bağımlılıklar ilgili Framework belgelerinde listelenmiştir:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [Sköğren](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Belirli bir paketin Azure ML tarafından korunan görüntülere ve ortamlara eklenmek için yeterince yaygın olduğunu düşünüyorsanız, lütfen [AzureML kapsayıcılarında](https://github.com/Azure/AzureML-Containers)GitHub sorununu yükseltin. 
 
* **NameError (ad tanımlı değil), AttributeError (nesne bir özniteliğe sahip değil)**: Bu özel durum eğitim betiklerinden gelmelidir. Tanımlı bir ad veya öznitelik hatası hakkında daha fazla bilgi edinmek için, Azure portal günlük dosyalarına bakabilirsiniz. SDK 'dan, `run.get_details()` hata iletisine bakmak için ' i kullanabilirsiniz. Bu, çalıştırma için oluşturulan tüm günlük dosyalarını da listeler. Lütfen eğitim betiğe göz atın ve çalıştırmayı yeniden göndermeden önce hatayı düzeltemedi. 


* **Çalıştırma veya deneme silme**: denemeleri, [deneme. Arşiv](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) yöntemi kullanılarak veya Azure Machine Learning Studio istemcisindeki deneme sekmesi görünümünden "Arşiv denemesi" düğmesi aracılığıyla arşivlenebilir. Bu eylem, sorgu ve görünümleri listeleme denemesini gizler, ancak silmez.

    Tek tek denemelerin veya çalıştırmaların kalıcı olarak silinmesi şu anda desteklenmiyor. Çalışma alanı varlıklarını silme hakkında daha fazla bilgi için bkz. [Machine Learning hizmeti çalışma alanı verilerinizi dışarı veya silme](how-to-export-delete-data.md).

* **Ölçüm belgesi çok büyük**: Azure Machine Learning bir eğitim çalıştırmasında bir kez günlüğe kaydedilebilir ölçüm nesnelerinin boyutunda iç sınırlara sahiptir. Liste değerli bir ölçümü günlüğe kaydederken "ölçüm belgesi fazla büyük" hatasıyla karşılaşıyorsanız listeyi daha küçük parçalara ayırmayı deneyin, örneğin:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Azure ML dahili olarak aynı ölçüm adına sahip blokları bitişik bir listede birleştirir.

* **İşlem hedefinin başlaması uzun sürüyor**: işlem hedeflerine yönelik Docker görüntüleri Azure Container Registry (ACR) ' den yüklenir. Varsayılan olarak, Azure Machine Learning *temel* hizmet katmanını kullanan bir ACR oluşturur. Çalışma alanınızın ACR 'sini standart veya Premium katmana değiştirmek, görüntüleri oluşturmak ve yüklemek için geçen süreyi azaltabilir. Daha fazla bilgi için bkz. [Azure Container Registry hizmet katmanları](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: bir modeli eğitme](tutorial-train-models-with-aml.md) bir modeli eğmek için yönetilen bir işlem hedefi kullanır.
* Bkz. [Scikit-öğren](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)ve [PYTORCH](how-to-train-pytorch.md)gibi belirli ml çerçeveleri ile modelleri eğitme.
* Daha iyi modeller oluşturmak için [hiper parametreleri verimli](how-to-tune-hyperparameters.md) bir şekilde ayarlamayı öğrenin.
* Eğitilen bir modelden sonra [modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)öğrenin.
* [ScriptRunConfig sınıfı](/python/api/azureml-core/azureml.core.scriptrunconfig) SDK başvurusunu görüntüleyin.
* [Azure sanal ağları ile Azure Machine Learning kullanma](./how-to-network-security-overview.md)
