---
title: Büyük miktarlarda veri üzerinde toplu çıkarımı Çalıştır
titleSuffix: Azure Machine Learning
description: Azure Machine Learning toplu çıkarımı kullanarak büyük miktarlardaki verileri zaman uyumsuz olarak nasıl alabileceğinizi öğrenin. Toplu çıkarım, büyük veri kullanım durumları için, kullanıma hazır olan paralel işleme yetenekleri sağlar ve yüksek aktarım hızı, yangın ve unutma çıkarımı için en iyi duruma getirir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 4e7ddf7fc7b18d57b8251d4fb8293ed2f6e83d17
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929566"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Azure Machine Learning kullanarak büyük miktarlarda veri üzerinde toplu çıkarımı çalıştırın
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu nasıl yapılır, Azure Machine Learning kullanarak, büyük miktarlarda verileri zaman uyumsuz ve paralel olarak nasıl alabileceğinizi öğrenirsiniz. Burada açıklanan toplu işlem çıkarım özelliği genel önizlemeye sunuldu. Bu, anahtarların ve işlem verilerinin işlenmesi için yüksek performanslı ve yüksek maliyetli bir yoldur. Bu, kutudan zaman uyumsuz yetenekler sağlar.

Batch çıkarımı sayesinde, çevrimdışı ınmalların terabaytlarca üretim verilerinde büyük miktarda makine kümelerine göre daha fazla verimlilik ve optimize edilmiş maliyetle ölçeklendirilmesi basittir.

Bu nasıl yapılır bölümünde aşağıdaki görevleri öğrenirsiniz:

> * Uzaktan işlem kaynağı oluşturun.
> * Özel bir çıkarım betiği yazın.
> * Bir [makine öğrenme işlem hattı](concept-ml-pipelines.md) [oluşturun ve daha](https://publicdataset.azurewebsites.net/dataDetail/mnist/) önce eğitimli bir görüntü sınıflandırma modelini, veri kümesine göre kaydedin. 
> * Azure Blob depolama hesabınızda bulunan örnek görüntülerde toplu çıkarımı çalıştırmak için modeli kullanın. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* Bir Azure Machine Learning çalışma alanınız veya Not defteri sanal makineniz yoksa, kılavuzlu bir hızlı başlangıç için [Kurulum öğreticisini](tutorial-1st-experiment-sdk-setup.md) doldurun. 

* Kendi ortamınızı ve bağımlılıklarınızı yönetmek için kendi ortamınızı yapılandırma hakkında [nasıl yapılır Kılavuzu](how-to-configure-environment.md) ' na bakın. Gerekli bağımlılıkları indirmek için ortamınızda `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` çalıştırın.

## <a name="set-up-machine-learning-resources"></a>Makine öğrenimi kaynaklarını ayarlama

Aşağıdaki eylemler bir toplu çıkarım ardışık düzeni çalıştırmak için ihtiyacınız olan kaynakları ayarlar:

- Çıkarımını görüntülere sahip bir blob kapsayıcısını işaret eden bir veri deposu oluşturun.
- Toplu çıkarım ardışık düzen adımı için veri başvurularını giriş ve çıkış olarak ayarlayın.
- Yığın çıkarımı adımını çalıştırmak için bir işlem kümesi ayarlayın.

### <a name="create-a-datastore-with-sample-images"></a>Örnek görüntülerle bir veri deposu oluşturma

Ortak blob kapsayıcısından `sampledata`, `pipelinedata`adlı bir hesapta yer alan özel bir değerlendirme kümesini alın. Bu kapsayıcıya işaret eden `mnist_datastore`adlı bir veri deposu oluşturun. `register_azure_blob_container`için aşağıdaki çağrıda `overwrite` bayrağını `True` olarak ayarlamak, daha önce bu adla oluşturulmuş herhangi bir veri deposundaki üzerine yazar. 

`datastore_name`, `container_name`ve `account_name`için kendi değerlerinizi sağlayarak bu adımı blob kapsayıcınızı işaret etmek üzere değiştirebilirsiniz.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Ardından, çıkış veri deposu olarak çalışma alanı varsayılan veri deposunu belirtin. Çıkarımı çıkışı için kullanacaksınız.

Çalışma alanınızı oluşturduğunuzda, [Azure dosyaları](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) ve [BLOB depolama](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) varsayılan olarak çalışma alanına eklenir. Azure dosyaları bir çalışma alanı için varsayılan veri depo, ancak blob depolamayı da bir veri deposu olarak kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Storage seçenekleri](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Veri girişlerini ve çıkışları yapılandırma

Artık aşağıdakiler dahil olmak üzere veri girişlerini ve çıkışları yapılandırmanız gerekir:

- Girdi görüntülerini içeren dizin.
- Önceden eğitilen modelin depolandığı dizin.
- Etiketleri içeren dizin.
- Çıkış dizini.

`Dataset`, Azure Machine Learning verileri keşfetmek, dönüştürmek ve yönetmek için bir sınıftır. Bu sınıfta iki tür vardır: `TabularDataset` ve `FileDataset`. Bu örnekte, Batch çıkarım ardışık düzen adımının girdileri olarak `FileDataset` kullanacaksınız. 

> [!NOTE] 
> Batch çıkarımı 'nda `FileDataset` desteği şimdilik Azure Blob depolama ile kısıtlıdır. 

Ayrıca, özel çıkarım betiğinizdeki diğer veri kümelerine de başvurabilirsiniz. Örneğin, `Dataset.register` ve `Dataset.get_by_name`kullanarak resimleri etiketlemek için betiğinizdeki etiketlere erişmek üzere kullanabilirsiniz.

Azure Machine Learning veri kümeleri hakkında daha fazla bilgi için bkz. [veri kümeleri oluşturma ve erişim (Önizleme)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

`PipelineData` nesneler, işlem hattı adımları arasında ara verileri aktarmak için kullanılır. Bu örnekte, çıkarım çıktıları için kullanacaksınız.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>İşlem hedefi ayarlama

Azure Machine Learning, *işlem* (veya *işlem hedefi*), makine öğrenimi ardışık düzeninde hesaplama adımlarını gerçekleştiren makinelere veya kümelere başvurur. CPU tabanlı bir [Amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) hedefi oluşturmak için aşağıdaki kodu çalıştırın.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>Modeli hazırlama

[Önceden eğitilen görüntü sınıflandırma modelini indirin](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)ve ardından `models` dizinine ayıklayın.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Ardından, uzak işlem kaynağınız tarafından kullanılabilmesi için modeli çalışma alanınıza kaydedin.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Çıkarım betiğinizi yazma

>[!Warning]
>Aşağıdaki kod, [örnek Not defterinin](https://aka.ms/batch-inference-notebooks) kullandığı bir örnektir. Senaryonuz için kendi komut dosyanızı oluşturmanız gerekir.

Betik iki işlev *içermelidir* :
- `init()`: Bu işlevi, daha sonraki çıkarım için pahalı veya genel hazırlık için kullanın. Örneğin, modeli genel bir nesneye yüklemek için kullanın.
-  `run(mini_batch)`: işlev her bir `mini_batch` örneği için çalışacaktır.
    -  `mini_batch`: Batch çıkarımı Run metodunu çağırır ve bir liste ya da Pandas DataFrame 'i yönteme bağımsız değişken olarak geçiracaktır. Giriş bir TabularDataset ise, min_batch içindeki her giriş bir dosya DosyaYolu olur.
    -  `response`: Run () yöntemi bir Pandas DataFrame veya Array döndürmelidir. Append_row output_action için, döndürülen bu öğeler ortak çıkış dosyasına eklenir. Summary_only için öğelerin içeriği yok sayılır. Tüm çıkış eylemleri için döndürülen her çıkış öğesi, giriş öğesinin girdi mini Batch 'de başarılı bir çıkardığını gösterir. Kullanıcı, girişi çıkarımı ile eşlemek için çıkarım sonucuna yeterli miktarda veri eklendiğinden emin olmalıdır. Çıkarım çıkışı çıkış dosyasında yazılır ve bu sırada olması garanti edilmez, kullanıcının çıktıda bir anahtar kullanması gerekir.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

## <a name="build-and-run-the-batch-inference-pipeline"></a>Toplu çıkarım ardışık düzeni oluşturma ve çalıştırma

Artık işlem hattını oluşturmak için ihtiyacınız olan her şeye sahipsiniz.

### <a name="prepare-the-run-environment"></a>Çalıştırma ortamını hazırlama

İlk olarak, betiğinizin bağımlılıklarını belirtin. Bu nesneyi daha sonra işlem hattı adımını oluştururken kullanırsınız.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_CPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Toplu çıkarım ardışık düzen adımınızda parametreleri belirtin

`ParallelRunConfig`, Azure Machine Learning işlem hattının içindeki yeni tanıtılan toplu iş çıkarımı `ParallelRunStep` örneğinin ana yapılandırmadır. Komut dosyanızı kaydırmak ve aşağıdakiler dahil olmak üzere gerekli parametreleri yapılandırmak için kullanın:
- `entry_script`: bir Kullanıcı betiği, birden çok düğümde paralel olarak çalıştırılacak yerel dosya yolu olarak. `source_directly` varsa, göreli bir yol kullanın. Aksi takdirde, makinede erişilebilen herhangi bir yolu kullanın.
- `mini_batch_size`: tek bir `run()` çağrısına geçirilen mini toplu iş boyutu. (İsteğe bağlı; varsayılan değer `1`.)
    - `FileDataset`için, en az `1`değeri olan dosya sayısıdır. Birden çok dosyayı tek bir mini toplu işte birleştirebilirsiniz.
    - `TabularDataset`, verilerin boyutudur. Örnek değerler `1024`, `1024KB`, `10MB`ve `1GB`. Önerilen değer `1MB`. `TabularDataset` 'den mini toplu işin hiçbir yere dosya sınırları hiçbir şekilde kesileceğini unutmayın. Örneğin, çeşitli boyutlarda. csv dosyalarınız varsa en küçük dosya 100 KB 'tır ve en büyük değer 10 MB 'tır. `mini_batch_size = 1MB`ayarlarsanız, boyutu 1 MB 'tan küçük olan dosyalar bir mini toplu işlem olarak kabul edilir. Boyutu 1 MB 'tan büyük olan dosyalar birden çok mini toplu iş içine bölünür.
- `error_threshold`: işleme sırasında yoksayılması gereken `FileDataset` için `TabularDataset` ve dosya hatalarıyla ilgili kayıt hatalarının sayısı. Tüm girdinin hata sayısı bu değerin üzerine gittiğinde, iş durdurulur. Hata eşiği, `run()` yöntemine gönderilen tek bir mini toplu iş için değil, tüm giriş içindir. Aralık `[-1, int.max]`. `-1` bölümü, işleme sırasındaki tüm hataların yok saydığını gösterir.
- `output_action`: aşağıdaki değerlerden biri çıktının nasıl düzenleneceğini gösterir:
    - `summary_only`: Kullanıcı betiği çıktıyı depolayacaktır. `ParallelRunStep` yalnızca hata eşiği hesaplaması için çıktıyı kullanacaktır.
    - `append_row`: tüm giriş dosyaları Için, Line ile ayrılmış tüm çıktıları eklemek için çıkış klasöründe yalnızca bir dosya oluşturulur. Dosya adı parallel_run_step. txt olacaktır.
- `source_directory`: işlem hedefinde yürütülecek tüm dosyaları içeren klasörlere yollar (isteğe bağlı).
- `compute_target`: yalnızca `AmlCompute` desteklenir.
- `node_count`: Kullanıcı betiğini çalıştırmak için kullanılacak işlem düğümlerinin sayısı.
- `process_count_per_node`: düğüm başına işlem sayısı.
- `environment`: Python ortam tanımı. Bunu mevcut bir Python ortamını kullanacak şekilde yapılandırabilir veya deneme için geçici bir ortam ayarlayabilirsiniz. Tanım ayrıca gerekli uygulama bağımlılıklarını ayarlamaktan de sorumludur (isteğe bağlı).
- `logging_level`: günlük ayrıntı düzeyi. Artan ayrıntı değerleri: `WARNING`, `INFO`ve `DEBUG`. Varsayılan değer `INFO` (isteğe bağlı).
- `run_invocation_timeout`: saniye cinsinden `run()` yöntemi çağırma zaman aşımı. Varsayılan değer `60`.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>İşlem hattı adımını oluşturma

Komut dosyası, ortam yapılandırması ve parametreleri kullanarak işlem hattı adımını oluşturun. Komut dosyası için yürütme hedefi olarak çalışma alanınıza zaten iliştirtiğiniz işlem hedefini belirtin. Aşağıdaki parametreleri alan toplu çıkarım ardışık düzen adımını oluşturmak için `ParallelRunStep` kullanın:
- `name`: adım adı, şu adlandırma kısıtlamalarına sahip: benzersiz, 3-32 karakter ve Regex ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: Azure Machine Learning modeli kayıt defterinde sıfır veya daha fazla model adı zaten kaydedilmiş.
- `parallel_run_config`: daha önce tanımlanan bir `ParallelRunConfig` nesnesi.
- `inputs`: bir veya daha fazla tek tür Azure Machine Learning veri kümesi.
- `output`: çıkış dizinine karşılık gelen bir `PipelineData` nesnesi.
- `arguments`: Kullanıcı betiğine geçirilen bağımsız değişkenlerin listesi (isteğe bağlı).
- `allow_reuse`: aynı ayarlarla/girişlerle çalıştırıldığında, adımın önceki sonuçları yeniden kullanıp kullanmayacağını belirtir. Bu parametre `False`, işlem hattı yürütmesi sırasında bu adım için her zaman yeni bir çalıştırma oluşturulacaktır. (İsteğe bağlı; varsayılan değer `True`.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

### <a name="run-the-pipeline"></a>İşlem hattını çalıştırma

Şimdi işlem hattını çalıştırın. İlk olarak, çalışma alanı başvurusunu ve oluşturduğunuz işlem hattı adımını kullanarak bir `Pipeline` nesnesi oluşturun. `steps` parametresi bir adım dizisidir. Bu durumda, toplu Puanlama için yalnızca bir adım vardır. Birden çok adım içeren işlem hatları oluşturmak için, adımları Bu dizide sırayla yerleştirin.

Sonra, işlem hattını yürütmeye göndermek için `Experiment.submit()` işlevini kullanın.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Toplu çıkarım işini izleme

Bir toplu çıkarım işinin tamamlanması uzun zaman alabilir. Bu örnek, bir Jupyıter pencere öğesi kullanarak ilerlemeyi izler. Ayrıca şunları kullanarak işin ilerlemesini yönetebilirsiniz:

* Azure Machine Learning Studio. 
* [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) nesnesinden konsol çıktısı.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Sonraki adımlar

Bu işlemin sona erdirmek için, [toplu çıkarım Not defterini](https://aka.ms/batch-inference-notebooks)deneyin. 

İşlem hatları için hata ayıklama ve sorun giderme kılavuzu için bkz. [nasıl yapılır Kılavuzu](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

