---
title: 'Öğretici: toplu Puanlama için ML işlem hatları'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning içindeki bir görüntü sınıflandırma modelinde toplu Puanlama çalıştırmak için bir makine öğrenimi işlem hattı oluşturun. Makine öğrenimi ardışık düzenleri, iş akışınızı hız, taşınabilirlik ve yeniden kullanma süreciyle iyileştirin. böylece, altyapı ve otomasyon yerine uzmanlık makinenizin öğrenimine odaklanırsınız.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: ccd29952693ecbc1db5927d5deabae874b6e9933
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796705"
---
# <a name="build--use-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Toplu Puanlama için & oluşturma Azure Machine Learning işlem hattı kullanma

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğreticide, Batch Puanlama işini çalıştırmak için Azure Machine Learning bir işlem hattı kullanırsınız. Örnek, etiketlenmemiş görüntüleri sınıflandırmak için preeğitilen [-v3](https://arxiv.org/abs/1512.00567) evsel sinir Network TensorFlow modelini kullanır. Bir işlem hattı derleyip yayımladıktan sonra, işlem hattını herhangi bir platformda herhangi bir HTTP kitaplığından tetiklemek için kullanabileceğiniz bir REST uç noktası yapılandırırsınız.

Makine öğrenimi ardışık düzenleri, iş akışınızı hız, taşınabilirlik ve yeniden kullanma süreciyle iyileştirin. böylece, altyapı ve otomasyon yerine uzmanlık makinenizin öğrenimine odaklanırsınız. [Makine öğrenimi işlem hatları hakkında daha fazla bilgi edinin](concept-ml-pipelines.md).

Bu öğreticide, aşağıdaki görevleri tamamlayacaksınız:

> [!div class="checklist"]
> * Çalışma alanını yapılandırma ve örnek verileri indirme
> * Verileri getirmek ve çıkarmak için veri nesneleri oluşturma
> * Çalışma alanınıza modeli indirme, hazırlama ve kaydetme
> * İşlem hedeflerini sağlama ve bir Puanlama betiği oluşturma
> * İşlem hattı oluşturma, çalıştırma ve yayımlama
> * İşlem hattı için bir REST uç noktasını etkinleştirme

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

## <a name="prerequisites"></a>Ön koşullar

* Zaten bir Azure Machine Learning çalışma alanınız veya Not defteri sanal makineniz yoksa, [Kurulum öğreticisinin 1. kısmını](tutorial-1st-experiment-sdk-setup.md)doldurun.
* Kurulum öğreticisini tamamladığınızda, *Öğreticiler/Tutorial-Pipeline-Batch-Scoring-Classification. ipynb* Not defterini açmak için aynı not defteri sunucusunu kullanın.

Kurulum öğreticisini kendi [Yerel ortamınızda](how-to-configure-environment.md#local)çalıştırmak istiyorsanız [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)'daki öğreticiye erişebilirsiniz. Gerekli paketleri almak için `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` çalıştırın.

## <a name="configure-workspace-and-create-a-datastore"></a>Çalışma alanını yapılandırma ve veri deposu oluşturma

Mevcut Azure Machine Learning çalışma alanından bir çalışma alanı nesnesi oluşturun.

- [Çalışma alanı](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) , Azure aboneliğinizi ve kaynak bilgilerinizi kabul eden bir sınıftır. Çalışma alanı, modelinizi izlemek ve izlemek için kullanabileceğiniz bir bulut kaynağı da oluşturur. 
- `Workspace.from_config()` `config.json` dosyayı okur ve sonra kimlik doğrulama ayrıntılarını `ws`adlı bir nesneye yükler. `ws` nesnesi bu öğretici boyunca kodda kullanılır.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Örnek görüntüler için bir veri deposu oluşturma

`pipelinedata` hesapta, `sampledata` genel blob kapsayıcısından ımagenet değerlendirmesi ortak veri örneğini alın. Verilerin adı `images_datastore`altında çalışma alanı için kullanılabilir olması için `register_azure_blob_container()` çağırın. Daha sonra, çalışma alanı varsayılan veri deposunu çıkış veri deposu olarak ayarlayın. İşlem hattındaki çıktıyı öğrenmek için çıkış veri deposunu kullanın.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Veri nesneleri oluşturma

Bir işlem hattı oluşturduğunuzda, bir `DataReference` nesnesi, çalışma alanı veri deposundan verileri okur. `PipelineData` nesnesi, işlem hattı adımları arasında ara verileri aktarır.

> [!Important]
> Bu öğreticideki toplu işlem Puanlama örneği yalnızca bir ardışık düzen adımını kullanır. Birden çok adımı olan kullanım durumlarında, tipik akış şu adımları içerir:
>
> 1. Ham verileri getirmek, bazı dönüşümleriniz gerçekleştirmek ve sonra bir `PipelineData` nesnesinin *çıktısını* almak için `DataReference` nesneleri *giriş* olarak kullanın.
>
> 2. *Giriş nesnesi*olarak önceki adımda `PipelineData` *çıktı nesnesini* kullanın. Sonraki adımlar için tekrarlayın.

Bu senaryoda, hem giriş görüntüleri hem de sınıflandırma etiketleri (y-test değerleri) için veri deposu dizinlerine karşılık gelen `DataReference` nesneleri oluşturursunuz. Toplu Puanlama çıkış verileri için bir `PipelineData` nesnesi de oluşturursunuz.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Modeli indir ve Kaydet

Bir işlem hattındaki toplu Puanlama için kullanmak üzere önceden eğitilen TensorFlow modelini indirin. İlk olarak, modeli depoladığınız yerel bir dizin oluşturun. Ardından, modeli indirip ayıklayın.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Ardından, modeli işlem hattı işleminde kolayca alabilmeniz için çalışma alanınıza kaydedin. `register()` static işlevinde `model_name` parametresi, SDK 'nın tamamında modelinizi bulmak için kullandığınız anahtardır.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Uzaktan işlem hedefini oluşturma ve iliştirme

Makine öğrenimi ardışık düzenleri yerel olarak çalıştırılamaz, bu nedenle bunları bulut kaynaklarında veya *uzak işlem hedeflerinde*çalıştırmalısınız. Uzaktan işlem hedefi, denemeleri ve makine öğrenimi iş akışlarını çalıştırdığınız yeniden kullanılabilir bir sanal işlem ortamıdır. 

GPU özellikli [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) hedefi oluşturmak ve ardından çalışma alanınıza eklemek için aşağıdaki kodu çalıştırın. İşlem hedefleri hakkında daha fazla bilgi için [kavramsal makaleye](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target)bakın.


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Puanlama betiği yazma

Puanlama yapmak için `batch_scoring.py`adlı bir toplu işlem Puanlama betiği oluşturun ve sonra geçerli dizine yazın. Betik, giriş görüntülerini alır, sınıflandırma modelini uygular ve daha sonra tahminleri bir sonuç dosyasına çıkarır.

`batch_scoring.py` betiği, bu öğreticide daha sonra oluşturduğunuz ardışık düzen adımından geçirilen aşağıdaki parametreleri alır:

- `--model_name`: kullanılan modelin adı.
- `--label_dir`: `labels.txt` dosyasını tutan dizin.
- `--dataset_path`: giriş görüntülerini içeren dizin.
- `--output_dir`: betik sonrasında `results-label.txt` dosyası için çıkış dizini, verileri veri üzerinde çalıştırır.
- `--batch_size`: modeli çalıştırırken kullanılan toplu iş boyutu.

İşlem hattı altyapısı, parametreleri ardışık düzen adımlarına geçirmek için `ArgumentParser` sınıfını kullanır. Örneğin, aşağıdaki kodda `--model_name` ilk bağımsız değişken `model_name`özellik tanımlayıcısı olarak verilmiştir. `main()` işlevinde, bu özelliğe erişmek için `Model.get_model_path(args.model_name)` kullanılır.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Bu öğreticideki işlem hattının yalnızca bir adımı vardır ve çıktıyı bir dosyaya yazar. Çok adımlı işlem hatları için, sonraki adımlara giriş için çıkış verilerini yazmak üzere bir dizin tanımlamak üzere `ArgumentParser` de kullanabilirsiniz. `ArgumentParser` tasarım düzenini kullanarak birden çok işlem hattı adımı arasında veri geçirmenin bir örneği için, bkz. [Not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>İşlem hattını oluşturma ve çalıştırma

İşlem hattını çalıştırmadan önce, Python ortamını tanımlayan bir nesne oluşturun ve `batch_scoring.py` betiğinizin gerektirdiği bağımlılıkları oluşturur. Gerekli ana bağımlılık, TensorFlow ' dır, ancak arka plan işlemlerinde SDK 'dan `azureml-defaults` de yüklersiniz. Bağımlılıkları kullanarak bir `RunConfiguration` nesnesi oluşturun. Ayrıca, Docker ve Docker-GPU desteği de belirtin.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>İşlem hattını Parametreleştirme

İşlem hattının toplu iş boyutunu denetlemek için özel bir parametre tanımlayın. İşlem hattı yayımlandıktan ve bir REST uç noktası aracılığıyla sunulduktan sonra, yapılandırılmış tüm parametreler de sunulur. Ardışık düzeni bir HTTP isteği aracılığıyla yeniden çalıştırdığınızda JSON yükünde özel parametreler belirtebilirsiniz.

Bu davranışı etkinleştirmek ve bir ad ve varsayılan değer tanımlamak için bir `PipelineParameter` nesnesi oluşturun.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>İşlem hattı adımını oluşturma

İşlem hattı adımı, aşağıdakiler dahil olmak üzere bir işlem hattı çalıştırmak için ihtiyacınız olan her şeyi kapsülleyen bir nesnedir:

* Ortam ve bağımlılık ayarları
* İşlem hattının çalıştırılacağı işlem kaynağı
* Giriş ve çıkış verileri ve tüm özel parametreler
* Adım sırasında çalışacak bir betik veya SDK mantığına başvuru

Birden çok sınıf [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)üst sınıftan devralınır. Bir adım oluşturmak için belirli çerçeveleri veya yığınları kullanmak üzere sınıfları seçebilirsiniz. Bu örnekte, özel bir Python betiği kullanarak adım mantığınızı tanımlamak için [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) sınıfını kullanırsınız. Betiğinizin bir bağımsız değişkeni adım veya adımın çıktısındaki bir giriş ise, bağımsız değişken, sırasıyla `arguments` dizisinde *ve* `input` ya da `output` *parametresinde tanımlanmalıdır.* 

Birden fazla adımın olduğu senaryolarda, `outputs` dizideki bir nesne başvurusu, sonraki bir işlem hattı adımı için *giriş* olarak kullanılabilir hale gelir.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Farklı adım türleri için kullanabileceğiniz tüm sınıfların bir listesi için, bkz. [adımlar paketi](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>İşlem hattını çalıştırma

Şimdi işlem hattını çalıştırın. İlk olarak, çalışma alanı başvurusunu ve oluşturduğunuz işlem hattı adımını kullanarak bir `Pipeline` nesnesi oluşturun. `steps` parametresi bir adım dizisidir. Bu durumda, toplu Puanlama için yalnızca bir adım vardır. Birden çok adım içeren işlem hatları oluşturmak için, adımları Bu dizide sırayla yerleştirin.

Sonra, işlem hattını yürütmeye göndermek için `Experiment.submit()` işlevini kullanın. Ayrıca, `param_batch_size`özel parametresini de belirtirsiniz. `wait_for_completion` işlevi, işlem hattı derleme işlemi sırasında günlükleri çıktı. Geçerli ilerlemeyi görmek için günlükleri kullanabilirsiniz.

> [!IMPORTANT]
> İlk işlem hattı çalıştırması yaklaşık *15 dakika*sürer. Tüm bağımlılıklar indirilmelidir, bir Docker görüntüsü oluşturulur ve Python ortamı sağlanmakta ve oluşturulur. Bu kaynaklar oluşturulması yerine yeniden kullanıldığından işlem hattının yeniden çalıştırılması çok daha az zaman alır. Ancak, işlem hattının toplam çalışma süresi, betiklerinizin ve her bir ardışık düzen adımında çalışan işlemlerin iş yüküne bağlıdır.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Çıktıyı indirin ve gözden geçirin

`batch_scoring.py` betiğinizden oluşturulan çıkış dosyasını indirmek için aşağıdaki kodu çalıştırın. Ardından, Puanlama sonuçlarını araştırın.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped>. dataframe tbody tr TH: yalnızca-of-type {Vertical-ALIGN: Middle;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Kısaltın</th>
      <th>Hızlı</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian bir Rhogeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>Tripod</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>daktilo klavyesi</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>silici korun</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Wınwınör kravat</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>Harvestman</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>Keman</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>Hoparlörü</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>Apron</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>American Lobster</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>REST uç noktasından Yayımla ve Çalıştır

İşlem hattını çalışma alanınıza yayımlamak için aşağıdaki kodu çalıştırın. Azure Machine Learning Studio 'daki çalışma alanınızda çalışma geçmişi ve süreler dahil olmak üzere işlem hattı için meta verileri görebilirsiniz. Ardışık düzeni Studio 'dan el ile de çalıştırabilirsiniz.

İşlem hattının yayımlanması, herhangi bir platformda herhangi bir HTTP kitaplığından işlem hattını çalıştırmak için kullanabileceğiniz bir REST uç noktası sağlar.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

İşlem hattını REST uç noktasından çalıştırmak için, OAuth2 taşıyıcı türünde bir kimlik doğrulama üst bilgisi gerekir. Aşağıdaki örnek etkileşimli kimlik doğrulaması kullanır (çizim amaçları için), ancak otomatik veya gözetimsiz kimlik doğrulaması gerektiren çoğu üretim senaryosunda, [Bu not defterinde açıklandığı](https://aka.ms/pl-restep-auth)gibi hizmet sorumlusu kimlik doğrulamasını kullanın.

Hizmet sorumlusu kimlik doğrulaması, *Azure Active Directory*bir *uygulama kaydı* oluşturulmasını içerir. İlk olarak, bir istemci parolası oluşturun ve ardından hizmet sorumlusu rolünüze Machine Learning çalışma alanınıza *erişim* verirsiniz. Kimlik doğrulama akışınızı yönetmek için [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) sınıfını kullanın. 

Hem [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) hem de `ServicePrincipalAuthentication` `AbstractAuthentication`devralınır. Her iki durumda da [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) işlevini, üstbilgiyi getirmek için aynı şekilde kullanın:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Yayımlanan ardışık düzen nesnesinin `endpoint` özelliğinden REST URL 'sini alın. REST URL 'sini Azure Machine Learning Studio 'daki çalışma alanınızda de bulabilirsiniz. 

Uç noktaya bir HTTP POST isteği oluşturun. İstekte kimlik doğrulama üst bilgisini belirtin. Deneme adı ve toplu iş boyutu parametresine sahip bir JSON yük nesnesi ekleyin. Öğreticide daha önce belirtildiği gibi, adım yapılandırmasında bir `PipelineParameter` nesnesi olarak tanımladığınız için `param_batch_size` `batch_scoring.py` betiğiyle geçirilir.

Çalıştırmayı tetiklemeye yönelik isteği yapın. Çalıştırma KIMLIĞININ değerini almak için yanıt sözlüğünden `Id` anahtarına erişmek üzere kodu ekleyin.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Yeni çalıştırmanın durumunu izlemek için çalıştırma KIMLIĞINI kullanın. Yeni çalıştırmanın tamamlanmasının bir başka 10-15 dakika sürer. 

Yeni çalıştırma öğreticide daha önce çalıştırdığınız işlem hattına benzer şekilde görünür. Tam çıktıyı görüntüleme seçeneğini belirleyebilirsiniz.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure Machine Learning öğreticileri çalıştırmayı planlıyorsanız, bu bölümü tamammayın.

### <a name="stop-the-notebook-vm"></a>Not defteri VM 'sini durdur

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Her şeyi sil

Oluşturduğunuz kaynakları kullanmayı planlamıyorsanız, hiçbir ücret ödemezsiniz:

1. Azure portal, sol taraftaki menüden **kaynak grupları**' nı seçin.
1. Kaynak grupları listesinde, oluşturduğunuz kaynak grubunu seçin.
1. **Kaynak grubunu sil**'i seçin.
1. Kaynak grubu adını girin. Ardından **Sil**' i seçin.

Ayrıca, kaynak grubunu koruyabilir ancak tek bir çalışma alanını silebilirsiniz. Çalışma alanı özelliklerini görüntüleyin ve sonra **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makine öğrenimi ardışık düzenleri öğreticisinde aşağıdaki görevleri yaptınız:

> [!div class="checklist"]
> * Uzak bir GPU işlem kaynağında çalışacak ortam bağımlılıklarıyla bir işlem hattı oluşturuldu.
> * Önceden eğitilen bir TensorFlow modeli kullanarak toplu tahminleri çalıştırmak için bir Puanlama betiği oluşturuldu.
> * Bir işlem hattı yayımlandı ve bir REST uç noktasından çalıştırılmak üzere etkinleştirildi.

Machine Learning SDK 'Yı kullanarak işlem hattı oluşturma hakkında daha fazla örnek için [Not defteri deposuna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)bakın.
